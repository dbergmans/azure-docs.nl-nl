---
title: Levenscyclus van het knowledge base - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker leert beste in een iteratief cyclus van wijzigingen in het gegevensmodel, utterance voorbeelden, publiceren en verzamelen van gegevens van eindpunt query's.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: ec5e9f92114e9bae1aaa840a1d02f5a42b2fd7bf
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2018
ms.locfileid: "48857165"
---
# <a name="knowledge-base-lifecycle"></a>Knowledge base-levenscyclus
QnA Maker leert beste in een iteratief cyclus van wijzigingen in het gegevensmodel, utterance voorbeelden, publiceren en verzamelen van gegevens van eindpunt query's. 

![Ontwerpcyclus](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Het maken van een kennisdatabase QnA Maker
QnA Maker knowledge base (KB)-eindpunt biedt een best-none-match-antwoord op een gebruikersquery op basis van de inhoud van de KB. Het maken van een kennisdatabase is een eenmalige actie voor het instellen van een opslagplaats voor inhoud van de vragen, antwoorden en gekoppelde metagegevens. Een kennisdatabase kunnen worden gemaakt met het verkennen van de bestaande inhoud zoals veelgestelde vragen over pagina's, producthandleidingen of gestructureerde Q-A-paren. Meer informatie over het [maken van een kennisdatabase](../How-To/create-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Testen en bijwerken van de knowledge base
De knowledge base is klaar voor testen wanneer het wordt gevuld met inhoud, redactioneel of via automatische extractie. Testen kan worden gedaan via de **Test** deelvenster door te voeren van de algemene query's van gebruikers en controleren of de antwoorden geretourneerd zijn zoals verwacht en met voldoende betrouwbaarheidsscore. U kunt alternatieve vragen om u te weinig vertrouwen scores rectificatie toevoegen. U kunt ook nieuwe antwoorden toevoegen wanneer een query wordt "geen overeenkomst gevonden in de KB" Standaardantwoord. Deze lus van test-update gaat door totdat u tevreden met de resultaten bent. Meer informatie over het [uw knowledge base test](../How-To/test-knowledge-base.md).

Voor grote kB's, kan de tests worden geautomatiseerd via generateAnswer API's. 

## <a name="publish-the-knowledge-base"></a>De kennisdatabase publiceren
Zodra u klaar bent met testen van de knowledge base, kunt u deze publiceert. De nieuwste versie van de geteste knowledge base voor een specifieke Azure Search-index vertegenwoordigt pushes publiceren de **gepubliceerd** knowledge base. Het maakt ook een eindpunt dat kan worden aangeroepen in uw toepassing of bot chatten.

Op deze manier alle wijzigingen aan de testversie van de knowledge base niet van invloed op de gepubliceerde versie die mogelijk live in een productietoepassing.

Elk van deze knowledge bases kan worden gericht voor het testen van afzonderlijk. Met de API's, kunt u de testversie van de knowledge base met richten `isTest=true` vlag in de aanroep van generateAnswer.

Meer informatie over het [kennisbank publiceren](../How-To/publish-knowledge-base.md).

## <a name="monitor-usage"></a>Gebruik bewaken
Als u zich de chatlogs van uw service, moet u Application Insights inschakelen wanneer u [uw QnA Maker-service maken](../How-To/set-up-qnamaker-service-azure.md).

U kunt verschillende analyse van uw gebruik van de service ophalen. Meer informatie over hoe u application insights gebruiken om op te halen [analytics voor uw service QnA Maker](../How-To/get-analytics-knowledge-base.md).

Op basis van wat u van uw analyses leert, Controleer juiste [updates voor uw knowledge base](../How-To/edit-knowledge-base.md).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Betrouwbaarheidsscore](./confidence-score.md)

## <a name="see-also"></a>Zie ook 

[Knowledge base](./knowledge-base.md)
[QnA Maker-overzicht](../Overview/overview.md)
