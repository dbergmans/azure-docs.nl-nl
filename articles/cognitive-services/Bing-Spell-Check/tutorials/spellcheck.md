---
title: 'Zelfstudie: resultaten voor spellingcontrole ophalen met behulp van de Spellingcontrole-API voor Bing'
titlesuffix: Azure Cognitive Services
description: Laat zien hoe u Bing Spellingcontrole gebruikt.
services: cognitive-services
author: v-jaswel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: tutorial
ms.date: 09/28/2017
ms.author: v-jaswel
ms.openlocfilehash: d7afcd18e00e820ad63b8b12bbc352faaca24c84
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48803593"
---
# <a name="tutorial-build-a-web-page-spell-check-client"></a>Zelfstudie: een webpagina met Spellingcontrole bouwen

In deze zelfstudie bouwen we een webpagina waarmee gebruikers query's kunnen uitvoeren op de Spellingcontrole-API voor Bing.

In deze zelfstudie ontdekt u hoe u:

> [!div class="checklist"]
> - Een eenvoudige query verstuurt naar de Spellingcontrole-API voor Bing
> - Queryresultaten weergeeft

## <a name="prerequisites"></a>Vereisten

Als u deze zelfstudie wilt volgen, hebt u een abonnementssleutel nodig voor de Spellingcontrole-API voor Bing. Als u geen sleutel hebt, kunt u zich [registreren voor een gratis proefversie](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api).

## <a name="create-a-new-web-page"></a>Een nieuwe webpagina maken

Open een teksteditor. Maak een nieuw bestand met bijvoorbeeld de naam spellcheck.html.

## <a name="add-html-header"></a>HTML-header toevoegen

Voeg de gegevens van de HTML-header toe en begin het scriptgedeelte als volgt.

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8"> 
    <title>Bing Spell Check</title>

<style type="text/css">
    html, body, div, p, h1, h2 {font-family: Verdana, "Lucida Sans", sans-serif;}

    html, body, div, p  {font-weight: normal;}
    h1, h2 {font-weight: bold;}
    sup {font-weight: normal;}

    html, body, div, p  {font-size: 12px;}
    h1 {font-size: 20px;}
    h2 {font-size: 16px;}
    h1, h2 {clear: left;}

    img#logo {float: right;
</style>

<script type="text/javascript">
```

## <a name="getsubscriptionkey-function"></a>getSubscriptionKey-functie

De functie getSubscriptionKey retourneert de sleutel voor de Spellingcontrole-API voor Bing. De sleutel wordt opgehaald uit de lokale opslag (in de vorm van een cookie) of de gebruiker wordt gevraagd om de sleutel, indien nodig.

Begin met de functie getSubscriptionKey en declareer de cookienaam als volgt.

```html
getSubscriptionKey = function() {

    var COOKIE = "bing-spell-check-api-key";   // name used to store API key in key/value storage
```

De helperfunctie findCookie retourneert de waarde van de opgegeven cookie. Als de cookie niet wordt gevonden, wordt er een lege tekenreeks geretourneerd.

```html
    function findCookie(name) {
        var cookies = document.cookie.split(";");
        for (var i = 0; i < cookies.length; i++) {
            var keyvalue = cookies[i].split("=");
            if (keyvalue[0].trim() === name) {
                return keyvalue[1];
            }
        }
        return "";
        }
```

De helperfunctie getSubscriptionKeyCookie vraagt de gebruiker om de waarde van de sleutel voor de Spellingcontrole-API voor Bing en retourneert de waarde van de sleutel.

```html
    function getSubscriptionKeyCookie() {
        var key = findCookie(COOKIE);
        while (key.length !== 32) {
            key = prompt("Enter Bing Spell Check API subscription key:", "").trim();
            var expiry = new Date();
            expiry.setFullYear(expiry.getFullYear() + 2);
            document.cookie = COOKIE + "=" + key.trim() + "; expires=" + expiry.toUTCString();
        }
        return key;
    }
```

De helperfunctie getSubscriptionKeyLocalStorage probeert eerst om de sleutel voor de Spellingcontrole-API voor Bing op te halen door de betreffende cookie op te zoeken. Als de cookie niet wordt gevonden, wordt de gebruiker gevraagd om de sleutelwaarde. Vervolgens wordt de waarde van de sleutel geretourneerd.

```html
    function getSubscriptionKeyLocalStorage() {
        var key = localStorage.getItem(COOKIE) || "";
        while (key.length !== 32)
            key = prompt("Enter Bing Spell Check API subscription key:", "").trim();
        localStorage.setItem(COOKIE, key)
        return key;
    }
```

De helperfunctie getSubscriptionKey accepteert één parameter: **invalidate**. Als **invalidate** is ingesteld op **true**, verwijdert getSubscriptionKey de cookie die de sleutel voor de Spellingcontrole-API voor Bing bevat. Als **invalidate** is ingesteld op **false**, retourneert getSubscriptionKey de waarde van de sleutel voor de Spellingcontrole-API voor Bing.

```html
    function getSubscriptionKey(invalidate) {
        if (invalidate) {
            try {
                localStorage.removeItem(COOKIE);
            } catch (e) {
                document.cookie = COOKIE + "=";
            }
        } else {
            try {
                return getSubscriptionKeyLocalStorage();
            } catch (e) {
                return getSubscriptionKeyCookie();
            }
        }
    }
```

Retourneer de helperfunctie getSubscriptionKey als het resultaat van de buitenste getSubscriptionKey-functie. Sluit de definitie van de buitenste getSubscriptionKey-functie.

```html
    return getSubscriptionKey;

}();
```

## <a name="helper-functions"></a>Helperfuncties

De helperfunctie pre retourneert de opgegeven tekst, vooraf opgemaakt met de HTML-tag [pre](https://www.w3schools.com/tags/tag_pre.asp).

```html
function pre(text) {
    return "<pre>" + text.replace(/&/g, "&amp;").replace(/</g, "&lt;") + "</pre>"
}
```

De functie renderSearchResults toont de opgegeven resultaten van de Spellingcontrole-API voor Bing, met behulp van JSON Pretty Print.

```html
function renderSearchResults(results) {
    document.getElementById("results").innerHTML = pre(JSON.stringify(results, null, 2));
}
```

De functie renderErrorMessage geeft het opgegeven foutbericht en de opgegeven foutcode weer.

```html
function renderErrorMessage(message, code) {
    if (code)
        document.getElementById("results").innerHTML = "<pre>Status " + code + ": " + message + "</pre>";
    else
        document.getElementById("results").innerHTML = "<pre>" + message + "</pre>";
}
```

## <a name="bingspellcheck-function"></a>bingSpellCheck-functie

De functie bingSpellCheck wordt telkens aangeroepen wanneer de gebruiker tekst invoert in het HTML-formulierveld.
De functie accepteert twee parameters: de inhoud van het HTML-formulierveld en de sleutel voor de Spellingcontrole-API voor Bing.

```html
function bingSpellCheck(query, key) {
```

Geef het eindpunt voor de Spellingcontrole-API voor Bing op en declareer een XMLHttpRequest-object, dat nodig is voor het verzenden van aanvragen naar het eindpunt.

```html
    var endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/";

    var request = new XMLHttpRequest();

    try {
        request.open("GET", endpoint + "?mode=proof&mkt=en-US&text=" + encodeURIComponent(query));
    }
    catch (e) {
        renderErrorMessage("Bad request");
        return false;
    }
```

Stel de header **Ocp-Apim-Subscription-Key** in op de waarde van de sleutel voor de Spellingcontrole-API voor Bing.

```html
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
```

Verwerk het antwoord van het eindpunt. Als de status 200 (OK) is, worden de resultaten weergegeven, anders de foutinformatie.

```html
    request.addEventListener("load", function() {
        if (this.status === 200) {
            renderSearchResults(JSON.parse(this.responseText));
        }
        else {
            if (this.status === 401) getSubscriptionKey(true);
            renderErrorMessage(this.statusText, this.status);
        }
    });
```

Zorg ook voor afhandeling van mogelijke foutgebeurtenissen van het XMLHttpRequest-object.

```html
    request.addEventListener("error", function() {
        renderErrorMessage("Network error");
    });

    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });
```

Verzend de aanvraag. Sluit de functie bingSpellCheck, de tag **script** en de tag **head**.

```html
    request.send();
    return false;
}
// --></script>

</head>
```

## <a name="html-body"></a>HTML-hoofdtekst

Wanneer de webpagina wordt geladen, zorg er dan voor dat u de sleutel voor de Spellingcontrole-API voor Bing hebt, en vraag de gebruiker erom indien nodig.

```html
<body onload="document.forms.bing.query.focus(); getSubscriptionKey();">
```

Geef het Bing-logo weer.

```html
<img id="logo" align=base src="data:image/png;base64,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">
```

Maak een HTML-formulier met een tekstveld. Verwerk de gebeurtenis **onsubmit** en roep de functie bingSpellCheck aan. Geef hierbij de inhoud van het tekstveld en de sleutel voor de Spellingcontrole-API voor Bing door.

```html
<form name="bing" onsubmit="return bingSpellCheck(this.query.value, getSubscriptionKey())">
    <h2>Spell Check</h2>
    <input type="text" name="query" size="80" placeholder="Spell Check" autocomplete=off>
</form>
```

Voeg de HTML-tag **div** toe, die we gebruiken om de resultaten weer te geven. In de JavaScript die we eerder hebben gedefinieerd, wordt er verwezen naar deze **div**-tag.

```html
<h2>Results</h2>
<div id="results">
<p>None yet.</p>

</div>

</body>
</html>
```

Sla het bestand op.

## <a name="display-results"></a>Resultaten weergeven

Open de webpagina in uw browser. Voer bij de prompt de abonnementssleutel voor de Spellingcontrole-API voor Bing in. Voer een query (bijvoorbeeld 'hollo, wlrd!') in het tekstvak **Spellingcontrole** in en druk op **Enter**. De webpagina geeft vervolgens de queryresultaten weer.

```json
{
  "_type": "SpellCheck",
  "flaggedTokens": [
    {
      "offset": 0,
      "token": "Hollo",
      "type": "UnknownToken",
      "suggestions": [
        {
          "suggestion": "Hello",
          "score": 0.856629936217145
        },
        {
          "suggestion": "Hollow",
          "score": 0.816717853225633
        }
      ]
    },
    {
      "offset": 7,
      "token": "wlrd",
      "type": "UnknownToken",
      "suggestions": [
        {
          "suggestion": "world",
          "score": 0.856629936217145
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Vereisten voor gebruik en weergave](../UseAndDisplayRequirements.md)
