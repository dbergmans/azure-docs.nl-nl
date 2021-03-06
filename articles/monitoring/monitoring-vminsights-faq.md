---
title: Veelgestelde vragen over Azure Monitor voor virtuele machines (Preview) | Microsoft Docs
description: Azure Monitor voor virtuele machines (Preview) is een oplossing in Azure en combineert status en prestaties bewaken van het besturingssysteem van de virtuele machine van Azure, evenals automatisch detecteren van onderdelen van de toepassing en afhankelijkheden met andere resources en de communicatie wordt toegewezen tussen beide. In dit artikel vindt u antwoorden op veelgestelde vragen.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/25/2018
ms.author: magoedte
ms.openlocfilehash: ff870f948acaae14ba772e14d48b27683f0bf07e
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50091688"
---
# <a name="azure-monitor-for-vms-preview-frequently-asked-questions"></a>Veelgestelde vragen over Azure Monitor voor virtuele machines (Preview)
Dit Microsoft-FAQ is een lijst met veelgestelde vragen over Azure Monitor voor virtuele machines. Als u aanvullende vragen over de oplossing hebt, gaat u naar de [discussieforum](https://feedback.azure.com/forums/34192--general-feedback) en plaats uw vraag. Wanneer u een vraag is vaak wordt gevraagd, toevoegen we deze aan dit artikel zodat snel en eenvoudig kunnen worden gevonden.

## <a name="can-i-onboard-to-an-existing-workspace"></a>Kan ik toevoegen aan een bestaande werkruimte?
Als uw virtuele machines al met een Log Analytics-werkruimte verbonden zijn, u kunt blijven gebruiken die werkruimte wanneer het voorbereiden op Azure-Monitor voor virtuele machines, mits deze is in een van de ondersteunde regio's die worden vermeld [hier](monitoring-vminsights-onboard.md#prerequisites).

Wanneer onboarding, we configureert prestatiemeteritems voor de werkruimte die zorgt ervoor dat alle virtuele machines waarvoor gegevens zijn gerapporteerd aan de werkruimte om te beginnen met het verzamelen van deze informatie voor het weergeven en analyseren in Azure Monitor voor virtuele machines.  Als gevolg hiervan, ziet u de prestatiegegevens van alle virtuele machines die zijn verbonden met de geselecteerde werkruimte.  De status en kaart-functies zijn alleen ingeschakeld voor de virtuele machines die u hebt opgegeven om te onboarden.

Voor meer informatie over welke prestaties prestatiemeteritems zijn ingeschakeld, raadpleegt u onze [onboarding](monitoring-vminsights-onboard.md) artikel.

## <a name="can-i-onboard-to-a-new-workspace"></a>Kan ik toevoegen aan een nieuwe werkruimte? 
Als uw VM's momenteel niet met een bestaande Log Analytics-werkruimte verbonden zijn, moet u een nieuwe werkruimte maken voor het opslaan van uw gegevens.  Het maken van een nieuwe standaardwerkruimte gebeurt automatisch als u één Azure-VM voor Azure Monitor voor VM's via Azure portal configureren.

Als u ervoor kiest om de methode op basis van een script te gebruiken, als volgt worden behandeld in de [onboarding](monitoring-vminsights-onboard.md) artikel. 

## <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>Wat moet ik doen als mijn virtuele machine is al aan een bestaande werkruimte rapporteren?
Als u al gegevens van uw virtuele machines verzamelen, u mogelijk al hebt geconfigureerd het rapport gegevens naar een bestaande Log Analytics-werkruimte.  Als deze werkruimte zich in een van onze ondersteunde regio's, kunt u Azure Monitor inschakelen voor virtuele machines aan die vooraf bestaande werkruimte.  Als de werkruimte die u al gebruikt zich niet in een van onze ondersteunde regio's, kunt u zich niet voor Onboarding van Azure Monitor voor virtuele machines op dit moment.  We werken actief ter ondersteuning van extra regio's.

>[!NOTE]
>We configureert prestatiemeteritems voor de werkruimte die van invloed is op alle VM's die aan de werkruimte rapporteren, ongeacht of u hebt gekozen voor de onboarding ze naar Azure Monitor voor virtuele machines of niet. Raadpleeg voor meer informatie over hoe de prestatiemeteritems zijn geconfigureerd voor de werkruimte, onze [documentatie](../log-analytics/log-analytics-data-sources-performance-counters.md). Raadpleeg voor informatie over de items die zijn geconfigureerd voor Azure Monitor voor virtuele machines, onze [inleidende documentatie](monitoring-vminsights-onboard.md#performance-counters-enabled).  

## <a name="why-did-my-vm-fail-to-onboard"></a>Waarom is mijn virtuele machine voor onboarding mislukt?
Wanneer onboarding van een Azure-VM vanuit de Azure-portal, de volgende stappen plaats:

* Een standaard Log Analytics-werkruimte is gemaakt, als die optie hebt geselecteerd.
* De prestatiemeteritems zijn geconfigureerd voor de geselecteerde werkruimte. Als deze stap mislukt, ziet u dat sommige van de van prestatiegrafieken en tabellen gegevens worden niet voor de virtuele machine weergegeven onboarding. U kunt dit oplossen door het uitvoeren van het PowerShell-script beschreven [hier](monitoring-vminsights-onboard.md#enable-with-powershell).
* De Log Analytics-agent is geïnstalleerd op virtuele Azure-machines met behulp van een VM-extensie, als dat nodig is vastgesteld.  
* De Azure-Monitor voor agent voor afhankelijkheden voor virtuele machines-kaart is geïnstalleerd op virtuele Azure-machines met de extensie, als dat nodig is vastgesteld.  
* Azure Monitor-onderdelen ondersteuning van de Health-functie zijn geconfigureerd, indien nodig, en de virtuele machine is geconfigureerd voor de gezondheid van rapportgegevens.

We controleren tijdens het onboarding-proces voor de status van elk van de bovenstaande om terug te keren van de meldingsstatus van een aan u in de portal.  Configuratie van de werkruimte en de installatie van de agent duurt doorgaans 5 tot 10 minuten.  Weergeven en de status van gegevens in de portal voor duren een aanvullende 5 tot 10 minuten.  

Als u onboarding hebt gestart en er berichten die aangeven dat de virtuele machine moet worden uitgevoerd, wacht u tot 30 minuten voor de virtuele machine om het proces te voltooien. 

## <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>Ik zie niet sommige of alle gegevens in de prestatiegrafieken voor mijn virtuele machine
Als er geen prestatiegegevens in de tabel schijf of in enkele van de van prestatiegrafieken en vervolgens uw prestatiemeteritems kunnen niet worden geconfigureerd in de werkruimte. Als u wilt oplossen, voert u de volgende [PowerShell-script](monitoring-vminsights-onboard.md#enable-with-powershell).

## <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Hoe verschilt Azure Monitor voor de functie voor toewijzing van virtuele machines van Serviceoverzicht?
De Azure-Monitor voor de functie voor toewijzing van virtuele machines is gebaseerd op Service Map, maar heeft de volgende verschillen:

* De kaartweergave zijn toegankelijk vanaf de VM-beheerblade en van Azure Monitor voor virtuele machines in Azure Monitor.
* De verbindingen in de kaart zijn nu klikbaar en een weergave van de metrische gegevens van de verbinding in het deelvenster aan clientzijde voor de geselecteerde verbinding.
* Er is een nieuwe API die wordt gebruikt voor de toewijzingen voor betere ondersteuning van meer complexe kaarten maken.
* Bewaakte VM's zijn nu opgenomen in het knooppunt voor de client en het ringdiagram toont het deel van de bewaakte vs niet-bewaakte virtuele machines in de groep.  Het kan ook worden gebruikt voor het filteren van de lijst met computers wanneer de groep is uitgevouwen.
* Bewaakte virtuele machines zijn nu opgenomen in de knooppunten poort groep en het ringdiagram toont het deel van de bewaakte vs niet-gecontroleerde computers in de groep.  Het kan ook worden gebruikt voor het filteren van de lijst met computers wanneer de groep is uitgevouwen.
* De stijl van de kaart is meer consistent zijn met App-kaart van Application insights bijgewerkt.
* De deelvensters aan clientzijde zijn bijgewerkt, maar nog niet de volledige set met integratie van die werden ondersteund in de Serviceoverzicht - updatebeheer, bijhouden, beveiliging en -servicedesk. 
* De optie voor het kiezen van groepen en computers toe te wijzen, is bijgewerkt en biedt nu ondersteuning voor abonnementen, resourcegroepen, schaalsets van virtuele Azure-machine en cloudservices.
* U kunt nieuwe Serviceoverzicht machine-groepen maken in de Azure-Monitor voor de functie voor virtuele machines toewijzen.  

## <a name="why-do-my-performance-charts-show-dotted-lines"></a>Waarom kunnen mijn prestatiegrafieken stippellijnen weergeven?

Dit kan gebeuren een aantal oorzaken hebben.  In gevallen waarbij er een onderbreking in het verzamelen van gegevens wordt de stippellijn met regels weer.  Als u de samplingfrequentie gegevens voor de prestatiemeteritems ingeschakeld hebt gewijzigd (de standaardinstelling is het verzamelen van gegevens elke 60 seconden), ziet u de stippellijn in de grafiek als u een smal tijdsbereik voor de grafiek en de samplingfrequentie is kleiner dan de Bucketgrootte in de grafiek gebruikt (bijvoorbeeld de samplingfrequentie is elke 10 minuten en elke bucket op de grafiek is 5 minuten).  Het kiezen van een breder scala van de tijd om weer te geven, zou de regels van de grafiek wordt weergegeven als ononderbroken lijnen in plaats van punten in dit geval veroorzaken.

## <a name="are-groups-supported-with-azure-monitor-for-vms"></a>Worden groepen ondersteund met Azure Monitor voor virtuele machines?
Ja, zodra u de agent voor afhankelijkheden we gegevens verzamelen van de virtuele machines om weer te geven van de groepen op basis van abonnement, resourcegroep, virtuele machine schaalsets, en cloudservices.  Als u hebt gebruikt Serviceoverzicht en machine-groepen hebt gemaakt, worden deze ook weergegeven.  Computergroepen wordt ook weergegeven in het filter groepen als u deze hebt gemaakt voor de werkruimte die u bekijkt. 

## <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Hoe zien de details voor wat het 95e percentiel vraagt om een lijndiagram in het aggregaat prestaties?
Standaard wordt de lijst om weer te geven u de virtuele machines waarvoor de hoogste waarde voor het 95e percentiel voor de geselecteerde metrische gegevens, met uitzondering van de grafiek beschikbaar geheugen, waarin de machines met de laagste waarde van 5% van de gevallen gesorteerd.  Te klikken op de grafiek wordt geopend de **Top N lijst** weergeven met de juiste metriek geselecteerd.

## <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>Hoe wordt de functie voor toewijzing dubbele IP-adressen in verschillende vnets en subnetten verwerkt?
Als u IP-adresbereiken dupliceert met virtuele machines of virtuele machine van Azure schaalsets in subnetten en vnets, kan dit leiden tot Azure-Monitor voor virtuele machines toewijzen om onjuiste gegevens weer te geven. Dit is een bekend probleem en we bekijken de opties om deze ervaring te verbeteren.

## <a name="does-map-feature-support-ipv6"></a>Ondersteuning voor IPv6-functies worden toegewezen?
Map-functie ondersteunt momenteel alleen IPv4 en we bekijken de ondersteuning voor IPv6. We ondersteunen ook IPv4 die wordt toegepast in IPv6.

## <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>Wanneer ik een toewijzing voor een resourcegroep of andere grote groep laadt is de kaart het moeilijk om weer te geven
Terwijl we verbeteringen in kaart aangebracht hebben voor het afhandelen van grote en complexe configuraties, realiseren we ons dat een kaart kan hebben een groot aantal knooppunten, verbindingen en knooppunt werkt als een cluster.  We zijn om verder te verbeteren van ondersteuning voor meer schaalbaarheid.   

## <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>Waarom ziet de grafiek netwerk op het tabblad prestaties er anders dan de grafiek netwerk op de pagina overzicht van Azure-VM?

De overzichtspagina voor een Azure-VM wordt weergegeven voor diagrammen op basis van de meting van de host van de activiteit in de Gast-VM.  Voor op de Azure VM-overzicht van de grafiek netwerk alleen wordt weergegeven netwerkverkeer dat wordt in rekening gebracht.  Dit omvat geen inter-vnet-verkeer.  De gegevens en grafieken weergegeven voor Azure Monitor voor virtuele machines is gebaseerd op gegevens van de Gast-VM en de netwerk-grafiek wordt weergegeven voor alle TCP/IP-verkeer dat binnenkomend en uitgaand naar die virtuele machine, met inbegrip van inter-vnet.

## <a name="next-steps"></a>Volgende stappen
Beoordeling [ingebouwde Azure-Monitor voor virtuele machines](monitoring-vminsights-onboard.md) voor informatie over vereisten en methoden voor bewaking van uw virtuele machines wilt inschakelen.
