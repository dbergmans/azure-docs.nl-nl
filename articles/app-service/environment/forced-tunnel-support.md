---
title: Azure App Service Environment configureren voor het gebruik van geforceerde tunnels
description: Instellen dat App Service Environment werkt wanneer uitgaand verkeer gebruikmaakt van geforceerde tunnels
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 384cf393-5c63-4ffb-9eb2-bfd990bc7af1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 05/29/2018
ms.author: ccompy
ms.custom: mvc
ms.openlocfilehash: ba93aab14c8eaccf9e3ed9ae9db0d169f41dddea
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2018
ms.locfileid: "44024042"
---
# <a name="configure-your-app-service-environment-with-forced-tunneling"></a>De Azure App Service-omgeving configureren met geforceerde tunnels

De ASE (App Service Environment) is een implementatie van Azure App Service in een virtueel Azure-netwerk van de klant. Veel klanten configureren hun virtuele Azure-netwerken als uitbreidingen van hun on-premises netwerken met VPN’s of Azure ExpressRoute-verbindingen. Geforceerde tunneling vindt plaats wanneer u internetverkeer omleidt naar uw VPN of een virtueel apparaat. Dit wordt vaak gedaan als onderdeel van beveiligingsvereisten, om het uitgaande verkeer te inspecteren en te controleren. 

De ASE heeft een aantal externe afhankelijkheden. Deze worden beschreven in het document [App Service Environment network architecture][network] (Netwerkarchitectuur van App Service Environment). Normaal gesproken moet al het verkeer met een uitgaande ASE-afhankelijkheid het VIP passeren dat is ingericht met de ASE. Als u de routering voor het verkeer naar of van de ASE wijzigt zonder onderstaande informatie te volgen, zal uw ASE niet meer werken.

In een virtueel Azure-netwerk vindt routering plaats op basis van LPM (Longest Prefix Match). Als er meer dan één route met dezelfde overeenkomende LPM is, wordt een route geselecteerd op basis van de oorsprong. Dit gebeurt in de volgende volgorde:

* UDR (door de gebruiker opgegeven route)
* BGP-route (wanneer u ExpressRoute gebruikt)
* Systeemroute

Lees [User-defined routes and IP forwarding][routes] (Door de gebruiker opgegeven routes en Doorsturen via IP) voor meer informatie over routering in een virtueel netwerk. 

Als u uw uitgaande ASE-verkeer wilt omleiden zodat het niet meer direct naar het internet gaat, hebt u de volgende opties:

* Instellen dat uw ASE directe toegang heeft tot internet
* Uw ASE-subnet configureren om BGP-routes te negeren
* Uw ASE-subnet configureren voor het gebruik van service-eindpunten naar Azure SQL en Azure Storage
* Uw eigen IP-adressen toevoegen aan de Azure SQL-firewall van ASE

## <a name="enable-your-app-service-environment-to-have-direct-internet-access"></a>Zorgen dat de App Service-omgeving rechtstreeks toegang heeft tot internet

Als u wilt instellen dat uw ASE rechtstreeks verbinding heeft met het internet, zelfs als uw virtuele Azure-netwerk is geconfigureerd met ExpressRoute, kunt u het volgende doen:

* Configureer ExpressRoute om 0.0.0.0/0 te adverteren. Standaard wordt al het uitgaande verkeer on-premises omgeleid.
* Maak een UDR met het adresvoorvoegsel 0.0.0.0/0 en Internet als het volgend hoptype. Pas deze route toe op het ASE-subnet.

Als u deze twee wijzigingen aanbrengt, wordt internetverkeer dat afkomstig is van het subnet van het ASE-subnet niet gedwongen om de ExpressRoute-verbinding te gebruiken.

Als het netwerk al verkeersrouting on-premises uitvoert, moet u het subnet maken om uw ASE te hosten en de UDR ervoor te configureren voordat u de ASE implementeert.  

> [!IMPORTANT]
> De routes die zijn gedefinieerd in een UDR, moeten specifiek genoeg zijn om voorrang te krijgen boven alle routes die worden geadverteerd met de ExpressRoute-configuratie. In het voorgaande voorbeeld wordt het brede adresbereik 0.0.0.0/0 gebruikt. Dit bereik kan mogelijk per ongeluk worden overschreven door routeadvertenties die gebruikmaken van specifiekere adresbereiken.
>
> App Service-omgevingen worden niet ondersteund met ExpressRoute-configuraties die advertentieoverschrijdende routes gebruiken van het openbare-peeringpad naar het privé-peeringpad. ExpressRoute-configuraties waarvoor openbare peering is geconfigureerd, ontvangen routeadvertenties van Microsoft. De advertenties bevatten een groot aantal Microsoft Azure-adresbereiken. Als de adresbereiken advertentieoverschrijdend zijn op het privé-peeringpad, worden alle uitgaande netwerkpakketten vanuit het ASE-subnet omgeleid naar een on-premises netwerkinfrastructuur van de klant. Deze netwerkstroom wordt momenteel niet standaard ondersteund met ASE's. Een mogelijke oplossing voor dit probleem is om advertentieoverschrijdende routes van het openbare-peeringpad naar het privé-peeringpad te stoppen. Een andere oplossing is om ervoor te zorgen dat de App Service-omgeving werkt in een geforceerde-tunnelconfiguratie.

![Directe internettoegang][1]

## <a name="configure-your-ase-subnet-to-ignore-bgp-routes"></a>Uw ASE-subnet configureren om BGP-routes te negeren ## 

U kunt uw ASE-subnet configureren om alle BGP-routes te negeren.  Wanneer dit zo is geconfigureerd heeft de ASE zonder problemen toegang tot de bijbehorende afhankelijkheden.  U moet echter UDR's maken waarmee uw apps toegang krijgen tot on-premises resources.

Uw ASE-subnet configureren om BGP-routes te negeren:

* Maak een UDR en wijs deze toe aan uw ASE-subnet, als u dit nog niet hebt gedaan.
* Open in de Azure-portal de gebruikersinterface voor de routetabel die aan uw ASE-subnet is toegewezen.  Selecteer Configuratie.  Stel de doorgifte van de BGP-route in op Uitgeschakeld.  Klik op Opslaan. Documentatie over hoe u dit kunt uitschakelen, vindt u in het document [Een routetabel maken][routetable].

Nadat u dat hebt gedaan, zijn uw apps niet langer on-premises bereikbaar. Als u dit wilt oplossen, bewerkt u de UDR die is toegewezen aan uw ASE-subnet en voegt u routes toe voor uw on-premises adresbereiken. Het type van de volgende hop moet worden ingesteld op Virtueel-netwerkgateway. 


## <a name="configure-your-ase-with-service-endpoints"></a>Uw ASE configureren met service-eindpunten ##

 > [!NOTE]
   > Service-eindpunten met SQL werken niet met ASE in de regio's van de Amerikaanse overheid.  De volgende informatie is alleen geldig in de openbare Azure-regio's.  

Voer de volgende stappen uit als u al het uitgaande verkeer vanuit uw ASE, behalve het verkeer naar Azure SQL en Azure Storage, wilt omleiden:

1. Maak een routetabel en wijs deze toe aan uw ASE-subnet. Zoek in [App Service Environment management addresses][management] (Beheeradressen van App Service Environment) de adressen bij uw regio. Maak routes voor deze adressen met Internet als de volgende hop. Dit is nodig omdat het inkomend managementverkeer van ASE moet beantwoorden vanaf hetzelfde adres als waarnaar het is verzonden.   

2. Service-eindpunten met Azure SQL en Azure Storage instellen met uw ASE-subnet.  Nadat deze stap is voltooid, kunt u uw VNet configureren met geforceerde tunneling.

Om uw ASE in een virtueel netwerk te maken dat al is geconfigureerd voor het routeren van al het verkeer on-premises, moet u uw ASE met een resource manager-sjabloon maken.  Het is niet mogelijk in een bestaand subnet met het portal een ASE te maken.  Wanneer u uw ASE implementeert in een VNet dat is al geconfigureerd om uitgaand verkeer on-premises te routeren, moet u uw ASE maken met een resource manager-sjabloon, hiermee bent u in staat om een bestaand subnet op te geven. Lees voor meer informatie over het implementeren van een ASE met een sjabloon [Een App Service Environment maken met een sjabloon][template].

Met service-eindpunten kunt u de toegang tot multitenant-services beperken tot een reeks virtuele Azure-netwerken en subnetten. In de documentatie [Virtual Network Service Endpoints][serviceendpoints] (Virtuele netwerkservice-eindpunten) vindt u meer informatie over service-eindpunten. 

Wanneer u service-eindpunten voor een bron inschakelt, worden er routes gemaakt die een hogere prioriteit hebben dan alle andere routes. Als u service-eindpunten gebruikt met een ASE met geforceerde tunnels, maakt het managementverkeer van Azure SQL en Azure Storage geen gebruik van geforceerde tunnels. Het andere verkeer met ASE-afhankelijkheid maakt wel gebruik van geforceerde tunnels en kan niet verloren gaan, anders werkt de ASE niet correct.

Als Service-eindpunten in een subnet met een Azure SQL-exemplaar is ingeschakeld, moet Service-eindpunten zijn ingeschakeld op alle Azure SQL-exemplaren waarmee vanuit dat subnet een verbinding wordt gemaakt. Als u vanuit hetzelfde subnet toegang wilt hebben tot meerdere Azure SQL-exemplaren, is het niet mogelijk om Service-eindpunten wel op het ene Azure SQL-exemplaar in te schakelen en niet op een ander.  Azure Storage gedraagt zich niet op dezelfde manier als Azure SQL.  Wanneer u Service-eindpunten met Azure Storage inschakelt, kunt u de toegang tot die resource vanuit uw subnet vergrendelen en toegang behouden tot andere Azure Storage-accounts, zelfs als Service-eindpunten op die accounts niet is ingeschakeld.  

Als u geforceerd tunneling configureert met een netwerkfilterapparaat, houd er dan rekening mee dat de ASE naast Azure SQL en Azure Storage nog een aantal andere afhankelijkheden heeft. U moet verkeer naar deze afhankelijkheden toestaan, anders werkt de ASE niet goed.

![Geforceerde tunnels met Service-eindpunten][2]

## <a name="add-your-own-ips-to-the-ase-azure-sql-firewall"></a>Uw eigen IP-adressen toevoegen aan de Azure SQL-firewall van ASE ##

Voer de volgende stappen uit als u al het uitgaande verkeer vanuit uw ASE, behalve het verkeer naar Azure SQL en Azure Storage, wilt omleiden:

1. Maak een routetabel en wijs deze toe aan uw ASE-subnet. Zoek in [App Service Environment management addresses][management] (Beheeradressen van App Service Environment) de adressen bij uw regio. Maak routes voor deze adressen met Internet als de volgende hop. Dit is nodig omdat het inkomend managementverkeer van ASE moet beantwoorden vanaf hetzelfde adres als waarnaar het is verzonden. 

2. Service-eindpunten met Azure Storage instellen met uw ASE-subnet

3. Haal de adressen op die worden gebruikt voor al het uitgaande verkeer van uw App Service Environment naar het internet. Als u het verkeer on-premises omleidt, zijn deze adressen uw NAT's of gateway-IP-adressen. Als u het uitgaande verkeer van de App Service-omgeving wilt routeren via een NVA, is het uitgaande adres het openbare IP-adres van de NVA.

4. _U stelt als volgt de uitgaande adressen in een bestaande App Service Environment in:_ ga naar resources.azure.com en ga naar Subscription/\<subscription id>/resourceGroups/\<ase resource group>/providers/Microsoft.Web/hostingEnvironments/\<ase name>. Vervolgens ziet u de JSON die de App Service-omgeving beschrijft. Controleer of bovenaan **lezen/schrijven** staat. Selecteer **Bewerken**. Schuif naar beneden. Wijzig de waarde **userWhitelistedIpRanges** van **null** in een waarde die lijkt op het volgende. Gebruik de adressen die u wilt instellen als het bereik met uitgaande adressen. 

        "userWhitelistedIpRanges": ["11.22.33.44/32", "55.66.77.0/24"] 

   Selecteer bovenaan **PUT**. Met deze optie wordt een schaalbewerking in de App Service-omgeving geactiveerd en de firewall aangepast.

_U maakt als volgt uw ASE met de uitgaande adressen_: volg de aanwijzingen in [Create an App Service Environment with a template][template] (Een App Service Environment maken met een sjabloon) en haal de geschikte sjabloon op.  Bewerk de sectie met bronnen in het bestand azuredeploy.json, maar niet in het blok 'properties' en neem voor **userWhitelistedIpRanges** een regel op die uw waarden bevat.

    "resources": [
      {
        "apiVersion": "2015-08-01",
        "type": "Microsoft.Web/hostingEnvironments",
        "name": "[parameters('aseName')]",
        "kind": "ASEV2",
        "location": "[parameters('aseLocation')]",
        "properties": {
          "name": "[parameters('aseName')]",
          "location": "[parameters('aseLocation')]",
          "ipSslAddressCount": 0,
          "internalLoadBalancingMode": "[parameters('internalLoadBalancingMode')]",
          "dnsSuffix" : "[parameters('dnsSuffix')]",
          "virtualNetwork": {
            "Id": "[parameters('existingVnetResourceId')]",
            "Subnet": "[parameters('subnetName')]"
          },
        "userWhitelistedIpRanges":  ["11.22.33.44/32", "55.66.77.0/30"]
        }
      }
    ]

Door deze wijzigingen wordt verkeer vanuit de ASE rechtstreeks naar Azure Storage gestuurd en is toegang tot de Azure SQL toegestaan vanaf andere adressen dan het VIP van de ASE.

   ![Geforceerde tunnel met goedgekeurde SQL][3]

## <a name="preventing-issues"></a>Problemen voorkomen ##

Als de communicatie tussen de ASE en de bijbehorende afhankelijkheden wordt verbroken, wordt de ASE onbetrouwbaar.  Als deze te lang onbetrouwbaar blijft, wordt de toegang tot de ASE tijdelijk ingetrokken. Volg de instructies in de ASE-portal als u de toegang tot de ASE wilt herstellen.

Niet alleen een onderbroken communicatie kan een nadelige invloed hebben op een ASE, maar ook de introductie van te veel latentie. Te veel latentie vindt plaats als uw ASE zich te ver van uw on-premises netwerk bevindt.  Het is bijvoorbeeld te ver als er een oceaan of continent tussen de ASE en uw on-premises netwerk ligt. Latentie kan ook optreden als gevolg van congestie van het intranet of beperkingen van de uitgaande bandbreedte.


<!--IMAGES-->
[1]: ./media/forced-tunnel-support/asedependencies.png
[2]: ./media/forced-tunnel-support/forcedtunnelserviceendpoint.png
[3]: ./media/forced-tunnel-support/forcedtunnelexceptstorage.png

<!--Links-->
[management]: ./management-addresses.md
[network]: ./network-info.md
[routes]: ../../virtual-network/virtual-networks-udr-overview.md
[template]: ./create-from-template.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[routetable]: ../../virtual-network/manage-route-table.md#create-a-route-table
