---
title: Azure Stream Analytics op IoT Edge (preview)
description: Edge-taken maken in Azure Stream Analytics en deze implementeren naar apparaten gestart Azure IoT Edge.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/16/2017
ms.openlocfilehash: 73b594aaabd814108dfce813b53a4ea865336e63
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2018
ms.locfileid: "49985060"
---
# <a name="azure-stream-analytics-on-iot-edge-preview"></a>Azure Stream Analytics op IoT Edge (preview)

> [!IMPORTANT]
> Deze functionaliteit is beschikbaar als preview en wordt niet aanbevolen voor gebruik in productie.
 
Azure Stream Analytics (ASA) op IoT Edge kunnen ontwikkelaars bijna-realtime analytische intelligence dichter bij IoT-apparaten implementeren, zodat ze de volledige waarde van het apparaat worden gegenereerd gegevens kunnen ontgrendelen. Azure Stream Analytics is ontworpen voor lage latentie, tolerantie en efficiënt gebruik van bandbreedte en naleving. Ondernemingen kunnen nu implementeren logische besturingselement dicht bij de industriële bewerkingen en een aanvulling vormen op Big Data-analyses klaar in de cloud.  

Azure Stream Analytics op IoT Edge wordt uitgevoerd binnen de [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) framework. Nadat de taak is gemaakt in ASA, kunt u deze kunt implementeren en beheren van de ASA-taken met behulp van IoT-Hub. Deze functie is beschikbaar als preview-versie. Als u vragen of feedback hebt, kunt u [deze enquête](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) contact opnemen met het productteam. 

## <a name="scenarios"></a>Scenario's
![Diagram op hoog niveau](media/stream-analytics-edge/ASAedge_highlevel.png)

* **Lage latentie opdracht en controle**: bijvoorbeeld veiligheid productiesystemen moet reageren op operationele gegevens met zeer lage latentie. Met ASA op IoT Edge, kunt u analyseren sensor gegevens in bijna realtime en opdrachten geven bij het detecteren van afwijkingen op een virtuele machine stoppen of alarmen te activeren.
*   **Verbinding met de cloud beperkt**: missie kritieke systemen, zoals externe analysestructuur apparatuur, verbonden vaartuigen of offshore analyseren wilt analyseren en reageren op gegevens, zelfs wanneer cloudconnectiviteit onderbroken wordt. Met ASA, uw streaming logica wordt uitgevoerd onafhankelijk van de verbinding met het netwerk en u kunt kiezen wat u verzendt naar de cloud voor verdere verwerking of opslag.
* **Beperkte bandbreedte**: de hoeveelheid gegevens die worden geproduceerd door jet-engines of verbonden auto's kunnen zo groot is dat gegevens moet worden gefilterd of vooraf verwerken voordat deze wordt verzonden naar de cloud. Met ASA, kunt u filteren en samenvoegen van de gegevens die moet worden verzonden naar de cloud.
* **Naleving**: naleving van regelgeving mogelijk enkele gegevens aan lokaal worden geanonimiseerd of samengevoegd voordat het wordt verzonden naar de cloud.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Edge-taken in Azure Stream Analytics
### <a name="what-is-an-edge-job"></a>Wat is een 'edge'-taak?

Edge ASA-taken uitvoeren als-modules binnen [Azure IoT Edge-runtime](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works). Deze zijn samengesteld uit twee delen:
1.  Een cloud-onderdeel dat verantwoordelijk is voor de taakdefinitie: gebruikers definiëren invoer, uitvoer en andere instellingen (niet-geordende gebeurtenissen, enzovoort) in de cloud.
2.  De ASA op IoT Edge-module die lokaal wordt uitgevoerd. Het bevat de engine ASA complexe verwerking van de gebeurtenis en de taakdefinitie ontvangt van de cloud. 

ASA maakt gebruik van IoT-Hub aan het edge-taken implementeren naar apparaten. Meer informatie over [IoT Edge-implementatie, ziet u hier](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

![Edge-taak](media/stream-analytics-edge/ASAedge_job.png)


### <a name="installation-instructions"></a>Installatie-instructies
De stappen op hoog niveau worden in de volgende tabel beschreven. Meer informatie krijgen in de volgende secties.
|      |Stap   | Plaats     | Opmerkingen   |
| ---   | ---   | ---       |  ---      |
| 1   | **Maak een opslagcontainer**   | Azure Portal       | Storage-containers worden gebruikt voor het opslaan van de taakdefinitie van de waar ze kunnen worden geopend door uw IoT-apparaten. <br>  U kunt een bestaande storage-container opnieuw gebruiken.     |
| 2   | **Een ASA edge-taak maken**   | Azure Portal      |  Maak een nieuw project, selecteer **Edge** als **hostomgeving**. <br> Deze taken zijn gemaakt of worden beheerd vanuit de cloud en uitvoeren op uw eigen IoT Edge-apparaten.     |
| 3   | **Instellen van uw IoT Edge-omgeving op uw apparaat/apparaten**   | Appara(a)t(en)      | Instructies voor het [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) of [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).          |
| 4   | **ASA implementeren op uw IoT Edge-apparaten**   | Azure Portal      |  De definitie van de ASA-taak wordt geëxporteerd naar de opslagcontainer eerder hebt gemaakt.       |
U kunt volgen [deze stapsgewijze zelfstudie](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics) aan uw eerste taak ASA op IoT Edge kunt implementeren. De volgende video kunt u inzicht in het proces voor het uitvoeren van een Stream Analytics-taak op een IoT edge-apparaat:  


> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T157/player]

#### <a name="create-a-storage-container"></a>Maak een opslagcontainer
Een storage-container is vereist om te exporteren van de query ASA gecompileerd en de taakconfiguratie van de. Het wordt gebruikt voor het configureren van de ASA-Docker-installatiekopie met uw specifieke query. 
1. Ga als volgt [deze instructies](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) een storage-account maken vanuit Azure portal. U kunt alle standaardopties aan dit account gebruiken met ASA houden.
2. Maak in de zojuist gemaakte opslagaccount, een blob storage-container:
    1. Klik op **Blobs**, klikt u vervolgens **+ Container**. 
    2. Voer een naam en de container als blijft **persoonlijke**.

#### <a name="create-an-asa-edge-job"></a>Een Edge ASA-taak maken
> [!Note]
> Deze zelfstudie richt zich op de ASA-taak maken met behulp van Azure portal. U kunt ook [Visual Studio-invoegtoepassing gebruiken om een ASA Edge-taak maken](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)

1. Maken van de Azure-portal een nieuwe 'Stream Analytics-taak'. [Directe koppeling naar het maken van een nieuwe ASA-taak hier](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob).

2. Selecteer in het scherm voor het maken van **Edge** als **hostomgeving** (Zie de volgende afbeelding) ![taak maken](media/stream-analytics-edge/ASAEdge_create.png)
3. Taakdefinitie
    1. **Invoer Stream(s) definiëren**. Definieer een of meer invoerstromen voor uw taak.
    2. Definieer referentiegegevens (optioneel).
    3. **Uitvoer Stream(s) definiëren**. Definieer een of meerdere uitvoer stromen voor uw taak. 
    4. **Query definiëren**. Definieer de ASA-query in de cloud met behulp van de inline-editor. De compiler wordt automatisch gecontroleerd of de syntaxis voor ASA edge is ingeschakeld. U kunt ook uw query testen door het uploaden van voorbeeldgegevens. 
4. Instellen van de storage-container-informatie in de **IoT Edge-instellingen** menu.
5. Optionele instellingen
    1. **Gebeurtenisvolgorde**. U kunt de volgorde out-beleid configureren in de portal. Documentatie is beschikbaar [hier](https://msdn.microsoft.com/library/azure/mt674682.aspx?f=255&MSPPError=-2147217396).
    2. **Landinstelling**. Stel de indeling van de deur.



> [!Note]
> Wanneer een implementatie wordt gemaakt, wordt de taakdefinitie in ASA geëxporteerd naar een opslagcontainer. Deze taakdefinitie blijven hetzelfde tijdens de duur van een implementatie. Als gevolg hiervan, als u wilt een taak die wordt uitgevoerd op de rand van het bij te werken, moet u de ASA-taak bewerken en maak vervolgens een nieuwe implementatie van IoT-Hub.


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>Instellen van uw IoT Edge-omgeving op uw apparaat/apparaten
Edge-taken kunnen worden geïmplementeerd op apparaten met Azure IoT Edge.
Hiervoor moet u als volgt te werk:
- Een Iot-Hub maken.
- Docker en IoT Edge-runtime installeren op uw edge-apparaten.
- Instellen van uw apparaten als **IoT Edge-apparaten** in IoT Hub.

Deze stappen worden beschreven in de IoT Edge-documentatie voor [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) of [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>Implementatie ASA op uw IoT Edge-apparaten
##### <a name="add-asa-to-your-deployment"></a>ASA toevoegen aan uw implementatie
- In de portal voor Azure IoT Hub te openen, naar **IoT Edge** en klik op het apparaat dat u voor deze implementatie wilt targeten.
- Selecteer **modules instellen**en selecteer vervolgens **+ toevoegen** en kies **Azure Stream Analytics-Module**.
- Selecteer het abonnement en de ASA-Edge-taak die u hebt gemaakt. Klik op Opslaan.
![ASA-module in uw implementatie toevoegen](media/stream-analytics-edge/set_module.png)


> [!Note]
> Tijdens deze stap maakt de ASA een map met de naam 'EdgeJobs' in de storage-container (als deze niet al bestaat). Voor elke implementatie, wordt een nieuwe submap in de map 'EdgeJobs' gemaakt.
> Als u wilt uw taak voor edge-apparaten implementeren, maakt de ASA een shared access signature (SAS) voor het definitiebestand van de taak. De SAS-sleutel wordt veilig naar de IoT Edge-apparaten met behulp van dubbele apparaat verzonden. De vervaldatum van deze sleutel is drie jaar van de dag van het is gemaakt.


Zie voor meer informatie over IoT Edge-implementaties, [deze pagina](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).


##### <a name="configure-routes"></a>Routes configureren
IoT Edge biedt een manier om declaratief routeren van berichten tussen modules en tussen modules en IoT-Hub. De volledige syntaxis wordt beschreven [hier](https://docs.microsoft.com/azure/iot-edge/module-composition).
Namen van de invoer en uitvoer die zijn gemaakt in de ASA-taak kunnen worden gebruikt als eindpunten voor de routering.  

###### <a name="example"></a>Voorbeeld
```
{
"routes": {                                              
    "sensorToAsa":   "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/ASA/inputs/temperature\")",
    "alertsToCloud": "FROM /messages/modules/ASA/* INTO $upstream", 
    "alertsToReset": "FROM /messages/modules/ASA/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")" 
}
}   

```
Dit voorbeeld toont de routes voor het scenario dat wordt beschreven in de volgende afbeelding. Deze bevat een edge-taak met de naam '**ASA**', met een invoer met de naam '**temperatuur**'en de uitvoer met de naam'**waarschuwing**'.
![Voorbeeld van Routering](media/stream-analytics-edge/RoutingExample.png)

In dit voorbeeld definieert de volgende routes:
- Elk bericht dat vanuit de **tempSensor** wordt verzonden naar de module met de naam **ASA** naar de invoer met de naam **temperatuur**,
- Alle uitvoer van **ASA** module worden verzonden naar de IoT-Hub die is gekoppeld aan dit apparaat (upstream$),
- Alle uitvoer van **ASA** module worden verzonden naar de **besturingselement** eindpunt van de **tempSensor**.


## <a name="technical-information"></a>Technische informatie
### <a name="current-limitations-for-edge-jobs-compared-to-cloud-jobs"></a>Huidige beperkingen voor edge-taken in vergelijking met cloudtaken
Het doel is dat pariteit tussen edge-taken en taken in de cloud. De meeste SQL-query-taalfuncties worden al ondersteund.
Echter de volgende functies zijn nog niet ondersteund voor edge-taken:
* Gebruiker gedefinieerde functies (UDF's) en de gebruiker gedefinieerde aggregaties (UDA).
* Azure ML-functies.
* Met behulp van meer dan 14 combinaties in één stap.
* AVRO-indeling voor invoer/uitvoer. Op dit moment worden alleen CSV en JSON ondersteund.
* De volgende SQL-operators:
    * AnomalyDetection
    * Georuimtelijke operators:
        * CreatePoint
        * CreatePolygon
        * CreateLineString
        * ST_DISTANCE
        * ST_WITHIN
        * ST_OVERLAPS
        * ST_INTERSECTS
    * PARTITION BY
    * GetMetadataPropertyValue


### <a name="runtime-and-hardware-requirements"></a>Runtime-en hardwarevereisten
Als u wilt uitvoeren ASA op IoT Edge, moet u de apparaten die kunnen worden uitgevoerd [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). 

ASA en Azure IoT Edge gebruiken **Docker** containers om een draagbare oplossing die wordt uitgevoerd op meerdere host-besturingssystemen (Windows, Linux) te bieden.

ASA on IoT Edge is beschikbaar als Windows en Linux-installatiekopieën, die worden uitgevoerd op zowel x86 64- of Azure Resource Manager-architecturen. 


### <a name="input-and-output"></a>Invoer en uitvoer
#### <a name="input-and-output-streams"></a>Invoer en uitvoer Streams
Edge ASA-taken kunnen krijgen in- en uitvoer van andere modules die worden uitgevoerd op IoT Edge-apparaten. Voor verbinding van en naar specifieke modules, kunt u de configuratie van de routering instellen tijdens de implementatie. Meer informatie wordt beschreven op [documentatie voor de samenstelling van de IoT Edge module](https://docs.microsoft.com/azure/iot-edge/module-composition).

Voor invoer en uitvoer, worden CSV en JSON-indelingen ondersteund.

Voor elke invoer en uitvoerstroom die u in de ASA-taak maken, wordt een bijbehorende eindpunt wordt gemaakt op uw geïmplementeerde module. Deze eindpunten kunnen worden gebruikt in de routes van uw implementatie.

Momenteel, de enige ondersteunde Stroominvoer en uitvoertypen van stream Edge Hub zijn. Verwijzen naar invoer ondersteunt verwijzing bestandstype. Andere uitvoer kunnen worden bereikt met behulp van een taak in de cloud downstream. Een Stream Analytics-taak die wordt gehost in Microsoft Edge wordt bijvoorbeeld uitvoer verzonden naar Edge Hub die u de uitvoer vervolgens naar IoT Hub verzenden kunt. U kunt een tweede in de cloud gehoste Azure Stream Analytics-taak met de invoer van IoT-Hub en de uitvoer naar Power BI of een andere uitvoertype.



##### <a name="reference-data"></a>Referentiegegevens
Referentiegegevens (ook wel bekend als een opzoektabel) is een eindige gegevensset die statisch of traag wijzigen in aard. Het wordt gebruikt om uit te voeren een zoekopdracht of correleren met de stroom van uw gegevens. Om het gebruik van referentiegegevens in uw Azure Stream Analytics-taak, gebruikt u in het algemeen een [verwijzing gegevens Join](https://msdn.microsoft.com/library/azure/dn949258.aspx) in uw query. Zie voor meer informatie de [ASA-documentatie over referentiegegevens](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-use-reference-data).

Voor het gebruik van referentiegegevens voor ASA op Iot Edge, de volgende stappen uit: 
1. Maak een nieuwe invoer voor de taak
2. Kies **verwijzen naar gegevens** als de **brontype**.
3. Stel het bestandspad. Het bestandspad moet een **absolute** bestandspad op het apparaat ![verwijzen naar het maken van gegevens](media/stream-analytics-edge/ReferenceData.png)
4. Schakel **gedeelde stations** in uw Docker-configuratie en zorg ervoor dat het station is ingeschakeld voordat u begint met uw implementatie.

Zie voor meer informatie, [Docker-documentatie voor Windows hier](https://docs.docker.com/docker-for-windows/#shared-drives).

> [!Note]
> Op dit moment wordt alleen lokale referentiegegevens ondersteund.




## <a name="license-and-third-party-notices"></a>Licentie en kennisgevingen van derden
* [Azure Stream Analytics op IoT Edge preview licentie](https://go.microsoft.com/fwlink/?linkid=862827). 
* [Kennisgeving van derden voor Azure Stream Analytics op IoT Edge preview](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="get-help"></a>Help opvragen
Voor verdere ondersteuning kunt u proberen de [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over Azure Iot Edge](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)
* [ASA on IoT Edge-zelfstudie](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Feedback verzenden naar het team met behulp van deze enquête](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
* [Stream Analytics Edge-taken met behulp van Visual Studio-hulpprogramma's ontwikkelen](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
