---
title: Aangepaste opslaan in cache in Azure API Management
description: Meer informatie over het items in de cache per sleutel in Azure API Management
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 772bc8dd-5cda-41c4-95bf-b9f6f052bc85
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 838850d38c9df51fabcf620831371bed401e9492
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29376028"
---
# <a name="custom-caching-in-azure-api-management"></a>Aangepaste opslaan in cache in Azure API Management
Azure API Management-service biedt ingebouwde ondersteuning voor [HTTP-antwoord in cache opslaan](api-management-howto-cache.md) met behulp van de bron-URL als de sleutel. De sleutel kan worden gewijzigd door aanvraagheaders met behulp van de `vary-by` eigenschappen. Dit is handig voor het opslaan van de hele HTTP-antwoorden (aka verklaringen), maar soms is het nuttig om alleen cache een deel van een weergave. De nieuwe [cache zoekwaarde](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) en [cache-store-value](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) -beleid biedt de mogelijkheid op te slaan en willekeurige soorten gegevens vanuit beleidsdefinities ophalen. Deze mogelijkheid wordt ook waarde toegevoegd aan de eerder geïntroduceerd [aanvragen verzenden](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) beleid omdat u kunt nu antwoorden van externe services cache.

## <a name="architecture"></a>Architectuur
API Management-service gebruikt een gedeelde tenant-gegevens in de cache zodat als schaal tot meerdere eenheden u nog steeds toegang krijgen tot dezelfde gegevens uit de cache. Als u werkt met een implementatie met meerdere regio zijn er echter onafhankelijke caches in elke regio. Het is belangrijk om te behandelen niet de cache als een gegevensarchief, waar de enige bron van een stukje informatie is. Als u hebt en later besluit om te profiteren van de implementatie van meerdere landen/regio, klikt u vervolgens klanten met gebruikers die geen toegang meer tot die gegevens in de cache.

## <a name="fragment-caching"></a>Fragment opslaan in cache
Er zijn bepaalde gevallen waarbij antwoorden worden geretourneerd bevatten een gedeelte van de gegevens die kostbaar om te bepalen en voor een redelijk tijdsbestek nog actueel blijft. Een voorbeeld kunt u een service die is gebouwd door een luchtvaartmaatschappij die voorziet in informatie over Vluchtreserveringen, vlucht status, enzovoort. Als de gebruiker lid is van het programma van de punten airlines, zou ze ook informatie met betrekking tot de huidige status en kilometers verzameld. Deze gebruiker-gerelateerde gegevens worden opgeslagen in een ander systeem, maar het wenselijk is opgenomen in antwoorden over de status van de vlucht en -reserveringen geretourneerd. U kunt dit doen via een proces genaamd fragment opslaan in cache. De primaire weergave kan worden opgehaald vanaf de oorspronkelijke server met behulp van een soort token om aan te geven waar de gebruiker-gerelateerde informatie moet worden ingevoegd. 

Houd rekening met het volgende JSON-antwoord van een back-end API.

```json
{
  "airline" : "Air Canada",
  "flightno" : "871",
  "status" : "ontime",
  "gate" : "B40",
  "terminal" : "2A",
  "userprofile" : "$userprofile$"
}  
```

En secundaire bron ingesteld op `/userprofile/{userid}` die vergelijkbaar is,

```json
{ "username" : "Bob Smith", "Status" : "Gold" }
```

Moeten identificeren die de eindgebruiker is om te bepalen van de gewenste gebruikersgegevens voor het toevoegen van API Management. Dit mechanisme is afhankelijk zijn van toepassing. Als u bijvoorbeeld gebruik ik de `Subject` claimen van een `JWT` token. 

```xml
<set-variable
  name="enduserid"
  value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />
```

API Management slaat de `enduserid` waarde in een variabele context voor later gebruik. De volgende stap is om te bepalen of een eerdere aanvraag al is opgehaald van de gebruikersgegevens en in de cache opgeslagen. Hiervoor API Management maakt gebruik van de `cache-lookup-value` beleid.

```xml
<cache-lookup-value
key="@("userprofile-" + context.Variables["enduserid"])"
variable-name="userprofile" />
```

Als er is geen vermelding in de cache die overeenkomt met de waarde van de sleutel en klik op Nee `userprofile` context variabele is gemaakt. API Management controleert het succes van de zoekactie met behulp van de `choose` beleid overdrachten beheren.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("userprofile"))">
        <!-- If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
    </when>
</choose>
```

Als de `userprofile` context variabele bestaat niet en gaat API Management hebt om een HTTP-aanvraag te halen.

```xml
<send-request
  mode="new"
  response-variable-name="userprofileresponse"
  timeout="10"
  ignore-error="true">

  <!-- Build a URL that points to the profile for the current end-user -->
  <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),
      (string)context.Variables["enduserid"]).AbsoluteUri)
  </set-url>
  <set-method>GET</set-method>
</send-request>
```

API Management maakt gebruik van de `enduserid` de URL van de gebruiker de resource-profiel maken. Als een API Management heeft het antwoord, haalt de platte tekst uit het antwoord en slaat deze weer in een context-variabele.

```xml
<set-variable
    name="userprofile"
    value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />
```

Om te voorkomen API Management kunnen deze HTTP-aanvraag opnieuw wanneer de gebruiker een andere aanvraag indient, kunt u opgeven voor het opslaan van het gebruikersprofiel in de cache.

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />
```

API Management slaat de waarde in de cache met exact dezelfde sleutel waartoe API Management oorspronkelijk te halen met. De duur die API Management kiest voor het opslaan van de waarde moet worden gebaseerd op hoe vaak de wijzigingen en hoe fouttolerante gebruikers tot verouderde gegevens worden. 

Houd er rekening mee dat bij het ophalen van de cache nog steeds een out-of-process netwerkaanvraag is en mogelijk kan nog steeds tientallen tijd in milliseconden aan de aanvraag toevoegen is. De voordelen worden geleverd bij het bepalen van dat de gebruikersprofielgegevens duurt langer dan die als gevolg van hoeven om query's of statistische gegevens uit meerdere back-ends van databases.

De laatste stap in het proces is het geretourneerde antwoord bijwerken met informatie over de gebruikersprofielen.

```xml
<!-- Update response body with user profile-->
<find-and-replace
    from='"$userprofile$"'
    to="@((string)context.Variables["userprofile"])" />
```

U kunt ervoor kiezen de aanhalingstekens opnemen als onderdeel van het token, zodat zelfs wanneer het vervangen niet wordt uitgevoerd, het antwoord nog een geldige JSON is.  

Als u al deze stappen samen combineren, is het eindresultaat een beleid dat op een lijkt.

```xml
<policies>
    <inbound>
        <!-- How you determine user identity is application dependent -->
        <set-variable
          name="enduserid"
          value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

        <!--Look for userprofile for this user in the cache -->
        <cache-lookup-value
          key="@("userprofile-" + context.Variables["enduserid"])"
          variable-name="userprofile" />

        <!-- If API Management doesn’t find it in the cache, make a request for it and store it -->
        <choose>
            <when condition="@(!context.Variables.ContainsKey("userprofile"))">
                <!-- Make HTTP request to get user profile -->
                <send-request
                  mode="new"
                  response-variable-name="userprofileresponse"
                  timeout="10"
                  ignore-error="true">

                   <!-- Build a URL that points to the profile for the current end-user -->
                    <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),(string)context.Variables["enduserid"]).AbsoluteUri)</set-url>
                    <set-method>GET</set-method>
                </send-request>

                <!-- Store response body in context variable -->
                <set-variable
                  name="userprofile"
                  value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

                <!-- Store result in cache -->
                <cache-store-value
                  key="@("userprofile-" + context.Variables["enduserid"])"
                  value="@((string)context.Variables["userprofile"])"
                  duration="100000" />
            </when>
        </choose>
        <base />
    </inbound>
    <outbound>
        <!-- Update response body with user profile-->
        <find-and-replace
              from='"$userprofile$"'
              to="@((string)context.Variables["userprofile"])" />
        <base />
    </outbound>
</policies>
```

Deze cache benadering wordt voornamelijk gebruikt in websites waar HTML bestaat op de server zodat deze kan worden gerenderd als één pagina. Dit kan ook nuttig in API's wanneer clients HTTP-caching aan clientzijde kunnen niet of is het wenselijk zijn geen te plaatsen dat de verantwoordelijkheid van de client zijn.

Dit dezelfde soort fragment opslaan in cache kan ook worden uitgevoerd op de back-end-webservers met behulp van een in Redis cache van de server, echter met de API Management-service voor het uitvoeren van deze taak is nuttig wanneer de in de cache fragmenten afkomstig zijn uit verschillende back-ends dan de antwoorden in de primaire.

## <a name="transparent-versioning"></a>Transparante versiebeheer
Het is gebruikelijk om voor meerdere versies van de andere implementatie van een API op elk gewenst moment worden ondersteund. Bijvoorbeeld, ter ondersteuning van verschillende omgevingen (ontwikkelen, testen, productie, enz.) of voor de ondersteuning van oudere versies van de API om tijd voor de API-consumenten te migreren naar nieuwere versies te geven. 

Een aanpak voor het verwerken van dit hoeft client ontwikkelaars wijzigen van de URL's van `/v1/customers` naar `/v2/customers` opslaan in de consument profielgegevens welke versie van de API ze momenteel wilt gebruiken en roept u de juiste back-end-URL. Om te bepalen van de juiste back-end-URL voor het aanroepen van een bepaalde client, is het nodig om sommige configuratiegegevens van een query. API Management kunt de prestaties van de handeling deze zoekactie minimaliseren door deze configuratiegegevens cache.

De eerste stap is om te bepalen van de id die wordt gebruikt voor het configureren van de gewenste versie. In dit voorbeeld hebt ik ervoor gekozen om te koppelen van de versie op de productcode van het abonnement. 

```xml
<set-variable name="clientid" value="@(context.Subscription.Key)" />
```

API Management biedt vervolgens een zoeken in het cachegeheugen om te zien of deze al het gewenste clientversie opgehaald.

```xml
<cache-lookup-value
key="@("clientversion-" + context.Variables["clientid"])"
variable-name="clientversion" />
```

API Management controleert om te zien als het niet in de cache vinden is.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("clientversion"))">
```

Als de beheer-API niet wordt gevonden, wordt het door de API Management opgehaald.

```xml
<send-request
    mode="new"
    response-variable-name="clientconfiguresponse"
    timeout="10"
    ignore-error="true">
            <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
            <set-method>GET</set-method>
</send-request>
```

De hoofdtekst van antwoord extraheren uit het antwoord.

```xml
<set-variable
      name="clientversion"
      value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
```

Sla het terug in het cachegeheugen voor toekomstig gebruik.

```xml
<cache-store-value
      key="@("clientversion-" + context.Variables["clientid"])"
      value="@((string)context.Variables["clientversion"])"
      duration="100000" />
```

En tot slot werkt u de URL van de back-end om te selecteren van de versie van de gewenste door de client-service.

```xml
<set-backend-service
      base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
```

Het volledige beleid is als volgt:

```xml
<inbound>
    <base />
    <set-variable name="clientid" value="@(context.Subscription.Key)" />
    <cache-lookup-value key="@("clientversion-" + context.Variables["clientid"])" variable-name="clientversion" />

    <!-- If API Management doesn’t find it in the cache, make a request for it and store it -->
    <choose>
        <when condition="@(!context.Variables.ContainsKey("clientversion"))">
            <send-request mode="new" response-variable-name="clientconfiguresponse" timeout="10" ignore-error="true">
                <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
                <set-method>GET</set-method>
            </send-request>
            <!-- Store response body in context variable -->
            <set-variable name="clientversion" value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
            <!-- Store result in cache -->
            <cache-store-value key="@("clientversion-" + context.Variables["clientid"])" value="@((string)context.Variables["clientversion"])" duration="100000" />
        </when>
    </choose>
    <set-backend-service base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
</inbound>
```

Om de API-consumenten transparant bepalen welke endversie van de back-om wordt geopend door clients zonder bijwerken en implementeren van clients is een elegante oplossing die zijn gericht op veel API versioning problemen.

## <a name="tenant-isolation"></a>Isolatie van tenants
Sommige bedrijven Maak in grotere implementaties met meerdere tenants afzonderlijke groepen van tenants voor verschillende implementaties van back-end-hardware. Hiermee wordt het aantal klanten die worden beïnvloed door de hardware op de back-end geminimaliseerd. Ook kunt nieuwe softwareversies voor een in fasen uit worden vernieuwd. Deze back-end-architectuur moet in het ideale geval transparant voor gebruikers van de API. Dit kan worden bereikt op een vergelijkbare manier als in transparante versiebeheer omdat deze is gebaseerd op dezelfde techniek van het manipuleren van de back-end-URL met behulp van de status van de configuratie per API-sleutel.  

In plaats van een aanbevolen versie van de API voor elke abonnementssleutel wordt geretourneerd, zou u een id die is gekoppeld van een tenant aan de Hardwaregroep toegewezen retourneren. Deze id kan worden gebruikt om de juiste back-end-URL samen te stellen.

## <a name="summary"></a>Samenvatting
De vrijheid biedt de Azure API management-cache gebruiken voor het opslaan van elk soort gegevens kan efficiënte toegang tot de configuratiegegevens die kunnen invloed hebben op de manier die een binnenkomende aanvraag is verwerkt. Het kan ook worden gebruikt voor het opslaan van gegevens fragmenten die reacties, geretourneerd vanuit een back-end API kunnen verbeteren.
