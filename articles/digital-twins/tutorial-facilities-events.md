---
title: Gebeurtenissen uit de Azure Digital Twins-ruimte vastleggen | Microsoft Docs
description: Informatie over hoe u meldingen ontvangt uit uw ruimten door Azure Digital Twins te integreren met logische apps met behulp van de stappen in deze zelfstudie.
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: dkshir
ms.openlocfilehash: 91dd16938efbd1e24419352f66e3238646a77e8a
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323206"
---
# <a name="tutorial-receive-notifications-from-your-azure-digital-twins-spaces-using-logic-apps"></a>Zelfstudie: Meldingen ontvangen uit uw Azure Digital Twins-ruimten met behulp van logische apps

Nadat u uw Azure Digital Twins-exemplaar hebt geïmplementeerd, uw opslagruimten hebt ingericht en aangepaste functies hebt geïmplementeerd voor het bewaken van specifieke voorwaarden, kunt u uw gebouwbeheerder via e-mail waarschuwen wanneer de bewaakte voorwaarden optreden. 

In [de eerste zelfstudie](tutorial-facilities-setup.md) hebt u de ruimtelijke grafiek van een denkbeeldig gebouw geconfigureerd, met hierin een ruimte met sensoren voor beweging, koolstofdioxide en temperatuur. In [de tweede zelfstudie](tutorial-facilities-udf.md) hebt u uw grafiek ingericht en riep een aangepaste functie de door de gebruiker gedefinieerde functie aan om deze sensorwaarden te bewaken en meldingen te activeren wanneer de ruimte leeg is en de temperatuur en koolstofdioxide zich in een vertrouwd bereik bevinden. In deze zelfstudie leert hoe u deze meldingen kunt integreren met Azure Logic Apps om e-mails te verzenden wanneer een dergelijke ruimte beschikbaar is. Een gebouwbeheerder kan deze informatie gebruiken om werknemers te helpen de meest productieve vergaderruimte te boeken. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Gebeurtenissen integreren met Event Grid
> * Gebeurtenissen melden met logische Apps
    
## <a name="prerequisites"></a>Vereisten

In deze zelfstudie wordt ervan uitgegaan dat u de Azure Digital Twins-installatie hebt [geconfigureerd](tutorial-facilities-setup.md) en [ingericht](tutorial-facilities-udf.md). Zorg er, vóórdat u doorgaat, voor dat u beschikt over:
- Een [Azure-account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Een actief exemplaar van Digital Twins.
- De gedownloade en uitgepakte [Digital Twins C#-voorbeelden](https://github.com/Azure-Samples/digital-twins-samples-csharp) op een werkcomputer.
- [.NET Core SDK-versie 2.1.403 of hoger](https://www.microsoft.com/net/download) op een ontwikkelcomputer om het voorbeeld uit te voeren. `dotnet --version` hebt uitgevoerd om te controleren of de juiste versie is geïnstalleerd. 
- Een Office 365-account voor het verzenden van melding via e-mail.

## <a name="integrate-events-with-event-grid"></a>Gebeurtenissen integreren met Event Grid 
In deze sectie stelt u een [Event Grid](../event-grid/overview.md) in voor het verzamelen van gebeurtenissen uit uw Digital Twins-instantie en leidt u deze om naar een [gebeurtenis-handler](../event-grid/event-handlers.md) zoals Azure Logic Apps.

### <a name="create-event-grid-topic"></a>Event Grid-onderwerp maken
[Event Grid-onderwerpen](../event-grid/concepts.md#topics) bieden een interface voor het routeren van de gebeurtenissen die worden gegenereerd door de functie die door de gebruiker is gedefinieerd. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Klik in het navigatiepaneel aan de linkerkant op **Resource maken**. 

1. Zoek en selecteer **Event Grid-onderwerp**. Klik op **Create**.

1. Voer een **Naam** in voor uw Event Grid-onderwerp en kies het **Abonnement**. Selecteer de **resourcegroep** die u hebt gebruikt of gemaakt voor uw Digital Twins-exemplaar, en de **locatie**. Klik op **Create**. 

    ![Event Grid-onderwerp maken](./media/tutorial-facilities-events/create-event-grid-topic.png)

1. Navigeer naar het Event Grid-onderwerp in de resourcegroep, klik op **Overzicht**, en kopieer de waarde voor **Eindpunt onderwerp** naar een tijdelijk bestand. U hebt deze URL nodig in de volgende sectie. 

1. Klik op **Toegangssleutels** en kopieer **Sleutel 1** en **Sleutel 2** naar een tijdelijk bestand. U hebt deze waarden nodig om het eindpunt in de volgende sectie te maken.

    ![Event Grid-sleutels](./media/tutorial-facilities-events/event-grid-keys.png)

### <a name="create-an-endpoint-for-the-event-grid-topic"></a>Een eindpunt voor het Event Grid-onderwerp maken

1. Zorg dat u in het opdrachtvenster de map **__occupancy-quickstart\src_** van het Digital Twins-voorbeeld hebt geopend.

1. Open het bestand **_actions\createEndpoints.yaml_** in de Visual Studio Code-editor. Zorg ervoor dat het de volgende inhoud bevat:

    ```yaml
    - type: EventGrid
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: Primary_connection_string_for_your_Event_Grid
      secondaryConnectionString: Secondary_connection_string_for_your_Event_Grid
      path: Event_Grid_Topic_Path
    ```

1. Vervang de tijdelijke aanduiding `Primary_connection_string_for_your_Event_Grid` door de waarde van **Sleutel 1**. 

1. Vervang de tijdelijke aanduiding `Secondary_connection_string_for_your_Event_Grid` door de waarde van **Sleutel 2**.

1. Vervang de tijdelijke aanduiding `Event_Grid_Topic_Path` door het pad van het Event Grid-onderwerp. Haal dit pad op door de *https://* en de navolgende resourcepaden uit de URL van het **Eindpunt onderwerp** te verwijderen. Het moet er uitzien zoals deze indeling: **yourEventGridName.yourLocation.eventgrid.azure.net**. 

    > [!IMPORTANT]
    > Voer alle waarden in zonder aanhalingstekens. Zorg dat de dubbele punten in het *YAML*-bestand worden gevolgd door minstens één spatie. U kunt de inhoud van het *YAML*-bestand ook valideren met behulp van een YAML-onlinevalidatie, zoals [dit hulpprogramma](https://onlineyamltools.com/validate-yaml).

1. Sla het bestand op en sluit het. Voer de volgende opdracht uit in het opdrachtvenster en meld u aan wanneer u hierom wordt gevraagd. 

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   Met deze opdracht maakt u het eindpunt voor de Event Grid. 

   ![Eindpunten voor Event Grid](./media/tutorial-facilities-events/dotnet-create-endpoints.png)


## <a name="notify-events-with-logic-app"></a>Gebeurtenissen melden met logische Apps
Met de [Azure Logic Apps](../logic-apps/logic-apps-overview.md)-service kunt u geautomatiseerde taken maken voor gebeurtenissen die zijn ontvangen van andere services. In deze sectie stelt u Azure Logic Apps in om e-mailmeldingen te maken voor gebeurtenissen die zijn doorgestuurd vanuit uw ruimtelijke sensoren, met behulp van een [Event Grid-onderwerp](../event-grid/overview.md).

1. Klik in de [Azure-portal](https://portal.azure.com) in het navigatiedeelvenster aan de linkerkant op **Een resource maken**.

1. Zoek en selecteer een nieuwe resource voor de **logische app**. Klik op **Create**.

1. Voer een **Naam** in voor uw logische app en selecteer vervolgens uw **Abonnement**, uw **Resourcegroep** en **Locatie**. Klik op **Create**.

    ![Een logische app maken](./media/tutorial-facilities-events/create-logic-app.png)

1. Open uw logische app wanneer deze is geïmplementeerd en open vervolgens het deelvenster **Ontwerper van logische app**. 

1. Selecteer de trigger **Wanneer een Event Grid-gebeurtenis optreedt**. **Meld u aan** bij uw tenant met uw Azure-account wanneer u hierom wordt gevraagd. Bevestig dat u **toegang wilt toestaan** tot uw Event Grid wanneer uw hierom wordt gevraagd. Klik op **Doorgaan**.

1. In het venster **Wanneer een resourcegebeurtenis optreedt (Preview)**, 
    1. Selecteer het **abonnement** dat u eerder hebt gebruikt voor het maken van de Event Grid,

    1. Selecteer **Microsoft.EventGrid.Topics** als het **Resourcetype**,

    1. Selecteer uw Event Grid-resource in de vervolgkeuzelijst voor de **Resourcenaam**.

    ![Een logische app maken](./media/tutorial-facilities-events/logic-app-resource-event.png)

1. Klik op de knop **Nieuwe stap**.

1. In het venster **Kies een actie**,
    1. Zoekt u de woordgroep *JSON parseren* en selecteert u de actie **JSON parseren**.

    1. Klik in het veld **Inhoud** en selecteer **Hoofdtekst** in de lijst **Dynamische inhoud**.

    1. Klik op **Voorbeeldnettolading om een schema te genereren**. Plak de volgende JSON-nettolading en klik vervolgens op **Gereed**.

        ```JSON
        {
        "id": "32162f00-a8f1-4d37-aee2-9312aabba0fd",
        "subject": "UdfCustom",
        "data": {
          "TopologyObjectId": "20efd3a8-34cb-4d96-a502-e02bffdabb14",
          "ResourceType": "Space",
          "Payload": "\"Air quality is poor.\"",
          "CorrelationId": "32162f00-a8f1-4d37-aee2-9312aabba0fd"
        },
        "eventType": "UdfCustom",
        "eventTime": "0001-01-01T00:00:00Z",
        "dataVersion": "1.0",
        "metadataVersion": "1",
        "topic": "/subscriptions/a382ee71-b48e-4382-b6be-eec7540cf271/resourceGroups/HOL/providers/Microsoft.EventGrid/topics/DigitalTwinEventGrid"
        }
        ```
    
    Deze nettolading heeft fictieve waarden. De logische app maakt gebruik van dit voorbeeld van nettolading voor het genereren van een **Schema**.
    
    ![Logische app: JSON parseren voor Event Grid](./media/tutorial-facilities-events/logic-app-parse-json.png)

1. Klik op de knop **Nieuwe stap**.

1. In het venster **Kies een actie**,
    1. Zoek en selecteer **Beheer van voorwaarden** in de lijst met **Acties**. 

    1. Klik in het eerste tekstvak **Een waarde kiezen** en selecteer **Type gebeurtenis** in de lijst **Dynamische inhoud** voor het venster **JSON parseren**.

    1. Klik in het tweede tekstvak **Een waarde kiezen** en typ *UdfCustom*.

    ![Logische app: JSON parseren voor Event Grid](./media/tutorial-facilities-events/logic-app-condition.png)

1. In het venster **Indien waar**,
    1. Klik op **Een actie toevoegen** en selecteert u *Office 365 Outlook*.

    1. Selecteer in de lijst **Een e-mail verzenden** de optie **Een e-mail verzenden**. Klik op **aanmelden** en gebruik de referenties van uw e-mailaccount. Klik op **Toegang toestaan** wanneer hierom wordt gevraagd.

    1. In het vak **Aan** voert u uw e-mail-ID in om meldingen te ontvangen. Voer bij **Onderwerp** de tekst *Digital Twins-melding voor slechte luchtkwaliteit in de ruimte* in, en selecteer vervolgens **TopologyObjectId** in de lijst **Dynamische inhoud** voor **JSON parseren**.

    1. Voer in de **Hoofdtekst** van hetzelfde venster soortgelijke tekst als deze in: *Slechte luchtkwaliteit gedetecteerd in een ruimte en temperatuur moet worden aangepast*. U kunt dit gerust uitbreiden met behulp van elementen uit de lijst **Dynamische inhoud** zoals hieronder wordt weergegeven.

    ![Logische app verzendt een e-mail](./media/tutorial-facilities-events/logic-app-send-email.png)

1. Klik op de knop **Opslaan** boven aan het deelvenster **Ontwerper van logische app**.

1. Zorg ervoor dat u sensorgegevens simuleert door te navigeren naar de map **_apparaatconnectiviteit_** van het Digital Twin-voorbeeld in een opdrachtvenster en dat u `dotnet run` uitvoert.

Binnen een paar minuten moet u e-mailmeldingen gaan ontvangen van deze logische app. 

   ![Logische app verzendt een e-mail](./media/tutorial-facilities-events/logic-app-notification.png)

Als u wilt stoppen met het ontvangen van deze e-mailberichten, gaat u naar uw **logische app** in de portal en selecteert het deelvenster **Overzicht**. Klik op **Uitschakelen**.


## <a name="clean-up-resources"></a>Resources opschonen

Als u Azure Digital Twins niet verder wilt verkennen, kunt u de resources die in deze zelfstudie zijn gemaakt, gerust verwijderen:

1. Klik in het linkermenu in de [Azure-portal](http://portal.azure.com) op **Alle resources**, selecteer de Digital Twins-resourcegroep en kies **Verwijderen**.
2. Als u wilt, kunt u ook de voorbeeldtoepassingen op de werkcomputer verwijderen. 


## <a name="next-steps"></a>Volgende stappen

U kunt doorgaan met de volgende zelfstudie voor meer informatie over hoe u uw sensorgegevens kunt visualiseren, trends kunt analyseren en afwijkingen kunt vinden. 
> [!div class="nextstepaction"]
> [Zelfstudie: Gebeurtenissen uit Azure Digital Twins-ruimten visualiseren en analyseren met Time Series Insights](tutorial-facilities-analyze.md)

U kunt ook meer leren over grafieken voor ruimtelijke intelligentie en objectmodellen in Azure Digital Twins. 
> [!div class="nextstepaction"]
> [Begrip van objectmodellen en grafieken voor ruimtelijke intelligentie van Digital Twins](concepts-objectmodel-spatialgraph.md)