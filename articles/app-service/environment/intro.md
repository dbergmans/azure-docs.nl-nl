---
title: Inleiding tot Azure App Service Environments
description: Kort overzicht van Azure App Service Environments
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 3c7eaefa-1850-4643-8540-428e8982b7cb
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/19/2018
ms.author: ccompy
ms.custom: mvc
ms.openlocfilehash: 34c041bf96ecc8ce7f4440d7afaa64de775baef7
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45576933"
---
# <a name="introduction-to-the-app-service-environments"></a>Inleiding tot Azure App Service Environments #
 
## <a name="overview"></a>Overzicht ##

Azure App Service Environment is een functie van Azure App Service die een volledig geïsoleerde en toegewezen omgeving biedt waarin App Service-apps veilig en op grote schaal kunnen worden uitgevoerd. In deze omgeving kunt u het volgende hosten:

* Web-apps van Windows
* Linux-web-apps 
* Docker-containers
* Mobiele apps
* Functions

AS-omgevingen (App Service-omgevingen) zijn geschikt voor werkbelastingen van toepassingen waarvoor het volgende is vereist:

* Een zeer hoge schaal.
* Isolatie en beveiligde netwerktoegang.
* Hoog geheugengebruik.

Klanten kunnen meerdere AS-omgevingen maken, binnen één Azure-regio of in meerdere Azure-regio's. Door deze flexibiliteit zijn AS-omgevingen ideaal voor het horizontaal schalen van staatloze toepassingslagen ter ondersteuning van hoge RPS-werkbelastingen.

AS-omgevingen zijn geïsoleerd om de toepassingen van slechts één klant uit te voeren en worden altijd geïmplementeerd in een virtueel netwerk. Klanten hebben meer controle over binnenkomend en uitgaand netwerkverkeer voor de toepassing. Er kunnen via VPN’s zeer snelle, beveiligde verbindingen tot stand worden gebracht tussen toepassingen en on-premises bedrijfsresources.

* ASE wordt geleverd met eigen prijscategorieën. Zie hoe de [geïsoleerd aanbieding](https://channel9.msdn.com/Shows/Azure-Friday/Security-and-Horsepower-with-App-Service-The-New-Isolated-Offering?term=app%20service%20environment) hyperschaalbaarheid en beveiliging van stations bevordert.
* [App Service-omgevingen v2](https://channel9.msdn.com/Blogs/Azure/Azure-Application-Service-Environments-v2-Private-PaaS-Environments-in-the-Cloud?term=app%20service%20environment) bieden een ombouw om uw apps te beschermen in een subnet van uw netwerk en bieden uw eigen persoonlijke implementatie van Azure App Service.
* Er kunnen meerdere AS-omgevingen worden gebruikt om horizontaal te schalen. Zie [how to set up a geo-distributed app footprint](app-service-app-service-environment-geo-distributed-scale.md) (een footprint voor geografisch verspreide apps instellen) voor meer informatie.
* AS-omgevingen kunnen worden gebruikt voor het configureren van beveiligingsarchitectuur, zoals u kunt zien in AzureCon Deep Dive. Raadpleeg het artikel over[het implementeren van een gelaagde beveiligingsarchitectuur](app-service-app-service-environment-layered-security.md) met App Service-omgevingen om te zien hoe de beveiligingsarchitectuur in AzureCon Deep Dive is geconfigureerd.
* De toegang tot apps die worden uitgevoerd in AS-omgevingen, kan worden vergrendeld met upstream-apparaten, zoals WAF’s (Web Application Firewall). Zie [Web Application Firewall (WAF)][AppGW] voor meer informatie.

## <a name="dedicated-environment"></a>Toegewezen omgeving ##

Een ASE is exclusief toegewezen aan één abonnement en kan 100 App Service Plan-exemplaren hosten. Het bereik loopt van 100 exemplaren in één App Service-plan tot 100 App Service-plannen met één exemplaar, en alles hier tussenin.

Een AS-omgeving bestaat uit front-ends en werkrollen. Front-ends zijn verantwoordelijk voor HTTP/HTTPS-beëindiging en automatische taakverdeling van app-aanvragen in een AS-omgeving. Front-ends worden automatisch toegevoegd wanneer de App Service-plannen in de AS-omgeving worden uitgeschaald.

Werkrollen zijn rollen die klanten-apps hosten. Werkrollen zijn beschikbaar in drie vaste grootten:

* Eén vCPU/3.5 GB RAM
* Twee vCPU/7 GB RAM
* Vier vCPU/14 GB RAM

Klanten hoeven de front-ends en werkrollen niet te beheren. Alle infrastructuur wordt automatisch toegevoegd wanneer klanten hun App Service-plannen uitschalen. Wanneer App Service-plannen worden gemaakt of geschaald in een AS-omgeving, wordt de vereiste infrastructuur toegevoegd of verwijderd, zoals toepasselijk.

AS-omgevingen hebben een vast maandtarief waarmee de infrastructuur wordt betaald. Dit tarief is onafhankelijk van de grootte van de AS-omgeving. Daarnaast zijn er kosten per vCPU voor het App Service-plan. Alle apps die worden gehost in een AS-omgeving, vallen onder de Geïsoleerde prijs-SKU. Ga naar de pagina met [prijzen voor App Service][Pricing] en bekijk de beschikbare opties voor AS-omgevingen voor informatie over prijzen voor een AS-omgeving.

## <a name="virtual-network-support"></a>Ondersteuning voor virtuele netwerken ##

De ASE-functie is een implementatie van Azure App Service rechtstreeks in het virtuele netwerk van Azure Resource Manager van een klant. Zie de [Veelgestelde vragen over virtuele Azure-netwerken](https://azure.microsoft.com/documentation/articles/virtual-networks-faq/) voor meer informatie over virtuele Azure-netwerken. Een AS-omgeving bevindt zich altijd in een virtueel netwerk en, preciezer nog, binnen een subnet van een virtueel netwerk. U kunt de beveiligingsfuncties van virtuele netwerken gebruiken om binnenkomende en uitgaande netwerkcommunicatie voor apps te beheren.

Een AS-omgeving kan internetgericht zijn met een openbaar IP-adres of intern met alleen een Azure ILB-adres (Internal Load Balancer).

Met [netwerkbeveiligingsgroepen][NSGs] wordt de binnenkomende netwerkcommunicatie beperkt tot het subnet waarin een AS-omgeving zich bevindt. U kunt NSG’s (netwerkbeveiligingsgroepen) gebruiken om apps achter upstream-apparaten, en services zoals WAF’s en SaaS-netwerkproviders uit te voeren.

Apps hebben ook vaak toegang nodig tot bedrijfsresources zoals interne databases en webservices. Als u de AS-omgeving implementeert in een virtueel netwerk met een VPN-verbinding naar het on-premises netwerk, krijgen de apps in de AS-omgeving toegang tot on-premises resources. Dit gebeurt altijd, ongeacht of het VPN van het type [site-naar-site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site) of [Azure ExpressRoute](http://azure.microsoft.com/services/expressroute/) is.

Zie [App Service Environment network considerations][ASENetwork] (Overwegingen voor App Service Environment-netwerken) voor meer informatie over hoe AS-omgevingen werken met virtuele netwerken en on-premises netwerken.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Application-Service-Environments-v2-Private-PaaS-Environments-in-the-Cloud/player]

## <a name="app-service-environment-v1"></a>App Service-omgeving v1 ##

App Service Environment heeft twee versies: ASEv1 en ASEv2. De voorgaande informatie is gebaseerd op ASEv2. In deze sectie leest u wat de verschillen zijn tussen ASEv1 en ASEv2. 

In ASEv1 moet u alle resources handmatig beheren. Dit geldt ook voor de front-ends, werkrollen en IP-adressen die worden gebruikt voor op IP gebaseerd SSL. Voordat u uw App Service-plan kunt uitschalen, moet u eerst de groep met werkrollen uitschalen waarin u de service wilt hosten.

ASEv1 maakt gebruik van een ander prijsmodel dan ASEv2. In ASEv1 betaalt u voor elke toegewezen vCPU. Dit is inclusief vCPU’s die worden gebruikt voor front-ends of werkrollen die geen werkbelastingen hosten. In ASEv1 is de maximale schaalgrootte voor een AS-omgeving standaard 55 hosts (in totaal). Dit is inclusief werkrollen en front-ends. Een voordeel van ASEv1 is dat deze versie kan worden geïmplementeerd in een klassiek virtueel netwerk en een virtueel netwerk van Resource Manager. Zie [Inleiding tot App Service Environment v1][ASEv1Intro] voor meer informatie over ASEv1.

<!--Links-->
[App Service Environments v2]: https://channel9.msdn.com/Blogs/Azure/Azure-Application-Service-Environments-v2-Private-PaaS-Environments-in-the-Cloud?term=app%20service%20environment
[Isolated offering]: https://channel9.msdn.com/Shows/Azure-Friday/Security-and-Horsepower-with-App-Service-The-New-Isolated-Offering?term=app%20service%20environment
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/waf-overview.md
