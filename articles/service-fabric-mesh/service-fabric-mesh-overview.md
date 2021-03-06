---
title: Overzicht van Azure Service Fabric Mesh | Microsoft Docs
description: Meer informatie over Azure Service Fabric Mesh. Met Azure Service Fabric Mesh kunt u uw toepassing implementeren en schalen zonder u zorgen te maken over de infrastructuurbehoeften van uw toepassing.
services: service-fabric-mesh
keywords: ''
author: rwike77
ms.author: ryanwi
ms.date: 10/1/2018
ms.topic: overview
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 361e742b3d9b7a5d2d12aafd15233077c967b825
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2018
ms.locfileid: "48888190"
---
# <a name="what-is-service-fabric-mesh"></a>Wat is Service Fabric?

Deze video geeft een kort overzicht van Service Fabric Mesh.
> [!VIDEO https://www.youtube.com/embed/7qWeVGzAid0]

Azure Service Fabric Mesh is een volledig beheerde service waarmee ontwikkelaars microservices-toepassingen kunnen implementeren zonder virtuele machines, opslag of netwerken hoeven te beheren. U kunt toepassingen die worden gehost in Service Fabric Mesh uitvoeren en schalen zonder u zorgen te maken over de infrastructuur die dit mogelijk maakt.  Service Fabric Mesh bestaat uit clusters met duizenden machines.  Alle clusterbewerkingen zijn verborgen voor de ontwikkelaar. Upload eenvoudigweg uw code en geef de bronnen, vereisten voor beschikbaarheid en resourcelimieten op.  Service Fabric Mesh wijst automatisch de infrastructuur toe en handelt problemen met de infrastructuur af, waarbij ervoor wordt gezorgd dat uw toepassingen maximaal beschikbaar zijn. U hoeft zich alleen bezig te houden met de status en de reactietijd van uw toepassing, niet met de infrastructuur.  

[!INCLUDE [preview note](./includes/include-preview-note.md)]

Dit artikel bevat een overzicht van de belangrijkste voordelen van Service Fabric Mesh.

## <a name="great-developer-experience"></a>Geweldige ontwikkelervaring

Service Fabric Mesh ondersteunt alle programmeertalen en frameworks die kunnen worden uitgevoerd in een container. De ondersteuning voor de hulpprogramma’s Visual Studio 2017 en Visual Studio Code biedt krachtige bewerkings- en foutopsporingsmogelijkheden voor .NET- en .NET Core-toepassingen. 

Met Service Fabric Mesh kunt u het volgende:

- Bestaande toepassingen via 'lift and shift' verplaatsen naar containers om uw huidige toepassingen te moderniseren en op schaal uit te voeren.
- Nieuwe microservices-toepassingen op schaal bouwen en implementeren in Azure.  Integreren met andere Azure-services of bestaande toepassingen die in containers worden uitgevoerd. Elke microservice maakt deel uit van een veilige, geïsoleerde netwerktoepassing met beleid voor resourcebeheer dat is gedefinieerd voor CPU-kernen, geheugen, schijfruimte en meer.
- Integreren en uitbreiden van bestaande toepassingen zonder wijzigingen aan te brengen in deze toepassingen. Uw eigen virtuele netwerk gebruiken om bestaande toepassingen met de nieuwe toepassing te verbinden.  
- Uw bestaande Cloud Services-toepassingen moderniseren door te migreren naar Service Fabric Mesh.  

## <a name="simple-operational-lifecycle"></a>Eenvoudige operationele levenscyclus

Beheer gemakkelijk actieve toepassingen, waaronder upgrades van toepassingen en versiebeheer, bewaking van toepassingen en foutopsporing in productieomgevingen. Deze toepassingen kunnen bestaan uit een enkele microservice of meerdere microservices geïsoleerd binnen hun eigen netwerk. Toepassingen worden efficiënt uitgevoerd met snelle implementatie, plaatsing en failovertijden.

Met Service Fabric Mesh kunt u het volgende:

- Implementeren en beheren van toepassingen zonder expliciete inrichting en beheer van de infrastructuur.  Door Service Fabric Mesh wordt de onderliggende infrastructuur voor u ingericht, bijgewerkt en van patches voorzien.
- Continue integratie instellen met behulp van de geïntegreerde hulpprogramma's om toepassingen gemakkelijk te verpakken en te implementeren.
- Maak gebruik van alle functies van Azure Resource Manager-resources (bijvoorbeeld audittrail en [op rollen gebaseerd toegangsbeheer](/azure/role-based-access-control/overview)) omdat alle resources, zoals toepassingen, services, geheimen enzovoort, die u implementeert in de Service Fabric Mesh-service in Azure Azure Resource Manager-resources zijn.
- Implementeren en beheren van resources met behulp van [Azure Portal](https://portal.azure.com), Resource Manager-sjablonen of Azure CLI/PowerShell-bibliotheken.
- Instellen van operationele bewaking en waarschuwingen met behulp van [Application Insights](/azure/application-insights/) (of een hulpprogramma naar keuze) om de operationele en diagnostische traceringen van het platform vast te leggen.
- Toegang krijgen tot diagnostische gegevens van de toepassing die afkomstig zijn uit het toepassingmodel via [Application Insights](/azure/application-insights/) of een hulpprogramma naar keuze.

## <a name="mission-critical-platform-capabilities"></a>Bedrijfskritieke platformmogelijkheden

Service Fabric Mesh maakt een verzameling clusters die [Azure-beschikbaarheidszones](/azure/availability-zones/az-overview) en/of geo-politieke regionale grenzen omspannen. Toepassingen worden beschreven met een set intenties zoals schaal, hardwarevereisten, vereisten voor duurzaamheid en veiligheidsbeleid.  Wanneer de toepassing wordt geïmplementeerd, zoekt Service Fabric Mesh naar de optimale plaats om deze uit te voeren.

Met Service Fabric Mesh kunt u het volgende:

- Profiteren van hoge beschikbaarheid, omhoog/omlaag schalen, indeling, berichtroutering, betrouwbare uitwisseling van berichten, upgrades zonder downtime, beheer van beveiliging/geheimen, herstel na noodgevallen, statusbeheer, configuratiebeheer en gedistribueerde transacties.
- Kiezen tussen meerdere toepassingsmodellen bij het maken van toepassingen.
- Platformmogelijkheden die toegankelijk zijn via REST-eindpunten door de gebruikte taalspecifieke bindingen die zijn gegenereerd met Swagger te gebruiken.
- Toepassingen implementeren via [Beschikbaarheidszones](/azure/availability-zones/az-overview) en meerdere regio's voor geografische betrouwbaarheid.
- Gebruikmaken van alle beveiligings- en nalevingsfuncties die Azure biedt.

## <a name="next-steps"></a>Volgende stappen

Het kost slechts enkele stappen om een voorbeeldproject met Visual Studio te implementeren. Zie [Een ASP.NET Core-website maken](service-fabric-mesh-quickstart-dotnet-core.md) voor meer informatie. 


<!-- Links -->

[service-fabric-overview]: ../service-fabric/service-fabric-overview.md
