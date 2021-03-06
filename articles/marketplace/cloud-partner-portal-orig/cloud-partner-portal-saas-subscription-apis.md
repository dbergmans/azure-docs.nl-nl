---
title: SaaS-verkopen via Azure - API's | Microsoft Docs
description: Wordt uitgelegd hoe u een SaaS-aanbiedingen via marketplace API's maken.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: c9ed3f3511def085f5e0658bbcbd7978e3a7ce20
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079307"
---
<a name="saas-sell-through-azure---apis"></a>SaaS-verkopen via Azure - API 's
==============================

In dit artikel wordt uitgelegd hoe u een SaaS-aanbieding maken met API's. De API's die nodig zijn voor het toestaan van abonnementen voor uw SaaS-aanbieding hebt u verkopen via Azure geselecteerd. Als u maken van een reguliere SaaS-aanbieding die geen commerce ingeschakeld wilt, raadpleegt u [SaaS-toepassing technische Publicatiehandleiding Guide]./cloud-partner-portal-saas-offers-tech-publishing-guide.md).

In dit artikel is onderverdeeld in twee secties:

-   Service-naar-Serviceverificatie tussen een SaaS-Service en de Azure Marketplace
-   API-methoden en eindpunten

De volgende API's zijn bedoeld om u uw SaaS-service te integreren met Azure:

-   Oplossen
-   Aanmelden
-   Converteren
-   Afmelden

Het volgende diagram toont de stroom van het abonnement van een nieuwe klant en wanneer deze API's worden gebruikt:

![SaaS biedt een API-stroom](media/saas-offer-publish-with-subscription-apis/saas-offer-publish-api-flow.png)

<a name="service-to-service-authentication-between-saas-service-and-azure-marketplace"></a>Service-verificatie tussen SaaS-service en Azure marketplace-service
----------------------------------------------------------------------------

Azure legt geen beperkingen op de verificatie die de SaaS-service beschikbaar aan de eindgebruikers stelt. Echter, als het gaat om de SaaS-service communiceert met de Azure Marketplace-API's, de verificatie wordt uitgevoerd in de context van een Azure Active Directory (Azure AD)-toepassing.

De volgende sectie wordt beschreven hoe u een Azure AD-toepassing maken.

### <a name="register-an-azure-ad-application"></a>Een Azure AD-toepassing registreren

Elke toepassing die de mogelijkheden van Azure Active Directory wil gebruiken, moet eerst in een Azure Active Directory-tenant worden geregistreerd. Dit registratieproces bestaat uit Azure AD-details over uw toepassing, zoals de URL waar deze zich, de URL om te antwoorden verzenden nadat een gebruiker is geverifieerd, geeft de URI die u de app, enzovoort identificeert.

Voor het registreren van een nieuwe toepassing met behulp van de Azure portal, moet u de volgende stappen uitvoeren:

1.  Meld u aan bij [Azure-portal](https://portal.azure.com/).
2.  Als u via uw account toegang tot meer dan één tenant hebt, klikt u op uw account in de rechterbovenhoek en stelt u uw portalsessie in op de gewenste Azure Active Directory-tenant.
3.  Klik in het navigatiedeelvenster links op de **Azure Active Directory** service, klikt u op **App-registraties**, en klikt u op **nieuwe toepassing registreren**.

    ![SaaS AD App-registraties](media/saas-offer-publish-with-subscription-apis/saas-offer-app-registration.png)

4.  Voer op de pagina voor het maken, uw toepassing\'s registratie-informatie:
    -   **Naam**: een zinvolle toepassingsnaam invoeren
    -   **Toepassingstype**: 
        - Selecteer **Systeemeigen** voor [clienttoepassingen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) die lokaal op een apparaat zijn geïnstalleerd. Deze instelling wordt gebruikt voor openbare [systeemeigen clients](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client) met OAuth.
        - Selecteer **Web-app / API** voor [clienttoepassingen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) en [resource/API-Apps](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server) die op een beveiligde server worden geïnstalleerd. Deze instelling wordt gebruikt voor OAuth vertrouwelijke [web-clients](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) en openbare [gebruiker agent-gebaseerde clients](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client).
        Dezelfde toepassing kan zowel een client als resource/API beschikbaar maken.
    -   **Aanmeldings-URL**: voor Web-app/API-toepassingen, bieden de basis-URL van uw app. Bijvoorbeeld, **http://localhost:31544** mogelijk de URL voor een WebApp die wordt uitgevoerd op uw lokale computer. Gebruikers zouden deze URL vervolgens gebruiken voor het aanmelden bij een web-clienttoepassing.
    -   **Omleidings-URI**: voor systeemeigen toepassingen geeft u de URI die door Azure AD gebruikt om tokenantwoorden te retourneren. Voer een specifieke waarde aan uw toepassing, bijvoorbeeld **http://MyFirstAADApp**.

        ![SaaS AD App-registraties](media/saas-offer-publish-with-subscription-apis/saas-offer-app-registration-2.png) voor specifieke voorbeelden voor webtoepassingen of systeemeigen toepassingen, bekijk de Snelstartgids begeleide instellingen die beschikbaar in de sectie aan de slag van zijn de [Azure AD-handleiding voor ontwikkelaars](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide#get-started).

5.  Klik op **Create** als u klaar bent. Azure AD wijst een unieke toepassings-ID toe voor uw toepassing, en u\'re genomen om uw toepassing\'pagina voor de belangrijkste registratie s. Afhankelijk van of uw toepassing een webtoepassing of systeemeigen toepassing is, worden er verschillende opties geboden om extra mogelijkheden aan uw toepassing toe te voegen.

    **Opmerking:** standaard de zojuist geregistreerde toepassing is geconfigureerd dat alleen gebruikers van dezelfde tenant aanmelden bij uw toepassing.

<a name="api-methods-and-endpoints"></a>API-methoden en eindpunten
-------------------------

De volgende secties beschrijven de API-methoden en eindpunten beschikbaar voor het inschakelen van abonnementen voor een SaaS-aanbod.

### <a name="get-a-token-based-on-the-azure-ad-app"></a>Een op basis van de Azure AD-app-token verkrijgen

HTTP-methode

`GET`

*Aanvraag-URL*

**https://login.microsoftonline.com/*{tenant-id}*/oauth2/token**

*URI-parameter*

|  **Parameternaam**  | **Vereist**  | **Beschrijving**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| tenant-id             | True          | Tenant-ID van de geregistreerde AAD-toepassing   |
|  |  |  |


*Aanvraagheader*

|  **Header-naam**  | **Vereist** |  **Beschrijving**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  Inhoudstype     | True         | Type van de inhoud die is gekoppeld aan de aanvraag. De standaardwaarde is `application/x-www-form-urlencoded`.  |
|  |  |  |


*Aanvraagtekst*

| **Eigenschapsnaam**   | **Vereist** |  **Beschrijving**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  Grant_type         | True         | Toekenningstype. De standaardwaarde is `client_credentials`.                    |
|  Client_id          | True         |  Client/app-id die is gekoppeld aan de Azure AD-app.                  |
|  client_secret      | True         |  Het wachtwoord dat is gekoppeld aan de Azure AD-app.                               |
|  Resource           | True         |  De doelresource waarvoor het token is aangevraagd. De standaardwaarde is `62d94f6c-d599-489b-a797-3e10e42fbe22`. |
|  |  |  |


*Antwoord*

|  **Naam**  | **Type**       |  **Beschrijving**    |
| ---------- | -------------  | ------------------- |
| 200 OK    | TokenResponse  | De aanvraag is voltooid   |
|  |  |  |

*TokenResponse*

Het antwoordtoken voorbeeld:

``` json
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "b3cca048-ed2e-406c-aff2-40cf19fe7bf5",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }               
```

### <a name="marketplace-api-endpoint-and-api-version"></a>Marketplace API-eindpunt en de API-versie

Het eindpunt voor de API van Azure Marketplace is `https://marketplaceapi.microsoft.com`.

De huidige API-versie is `api-version=2017-04-15`.


### <a name="resolve-subscription"></a>Abonnement oplossen

Actie na de op oplossen eindpunt kan gebruikers een token omzetten in een permanente Resource-ID.

*Aanvraag*

**POST**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=2017-04-15**

|  **Parameternaam** |     **Beschrijving**                                      |
|  ------------------ |     ---------------------------------------------------- |
|  API-versie        |  De versie van de bewerking te gebruiken voor deze aanvraag.   |
|  |  |


*Headers*

| **Header-sleutel**     | **Vereist** | **Beschrijving**                                                                                                                                                                                                                  |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | Nee           | Een unieke tekenreeks-waarde voor het bijhouden van de aanvraag van de client, bij voorkeur een GUID. Als deze waarde niet is opgegeven, wordt een gegenereerd en vindt u in de antwoordheaders.  |
| x-ms-correlationid | Nee           | Een unieke tekenreeks-waarde voor de bewerking op de client. Dit komt overeen met alle gebeurtenissen van clientbewerking met gebeurtenissen op de server. Als deze waarde niet is opgegeven, wordt een gegenereerd en vindt u in de antwoordheaders. |
| inhoudstype       | Ja          | `application/json`                                        |
| Autorisatie      | Ja          | De JSON web token (JWT) bearer-token.                    |
| x-ms-marketplace-token| Ja| De token queryparameter in de URL in wanneer de gebruiker wordt omgeleid naar de website SaaS ISV's van Azure. **Opmerking:** URL van de token waarde vanuit de browser worden ontsleuteld voordat u deze gebruikt.|
|  |  |  |
  

*De hoofdtekst van antwoord*

 ``` json       
    { 
        “id”: “”, 
        “subscriptionName”: “”,
        “offerId”:””, 
         “planId”:””
    }     
```

| **Parameternaam** | **Gegevenstype** | **Beschrijving**                       |
|--------------------|---------------|---------------------------------------|
| id                 | Reeks        | ID van de SaaS-abonnement.          |
| subscriptionName| Reeks| De naam van de SaaS-abonnement instellen door de gebruiker in Azure terwijl u zich abonneert op de SaaS-service.|
| OfferId            | Reeks        | Aanbiedings-ID die de gebruiker een abonnement op. |
| planId             | Reeks        | Plan-ID die de gebruiker een abonnement op.  |
|  |  |  |


*Responscodes*

| **HTTP-statuscode** | **Foutcode**     | **Beschrijving**                                                                         |
|----------------------|--------------------| --------------------------------------------------------------------------------------- |
| 200                  | `OK`                 | Token is opgelost.                                                            |
| 400                  | `BadRequest`         | Een vereiste headers ontbreken of een ongeldige api-versie die is opgegeven. Kan niet aan het token niet ophalen omdat een van beide het token ongeldig of verlopen is. |
| 403                  | `Forbidden`          | De aanroeper is niet geautoriseerd deze bewerking uit te voeren.                                 |
| 429                  | `RequestThrottleId`  | Service is bezet verwerken van aanvragen, voer later opnieuw uit.                                |
| 503                  | `ServiceUnavailable` | Service is omlaag tijdelijk, probeer het later opnieuw.                                        |
|  |  |  |


*Antwoordheaders*

| **Header-sleutel**     | **Vereist** | **Beschrijving**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Aanvraag-ID ontvangen van de client.                                                                   |
| x-ms-correlationid | Ja          | Correlatie-ID is als doorgegeven door de client, anders wordt deze waarde de correlatie-id op.                   |
| x-ms-activityid    | Ja          | Een unieke tekenreeks-waarde voor het bijhouden van de aanvraag van de service. Dit wordt gebruikt voor alle aansluitingen. |
| Opnieuw proberen na        | Nee           | Deze waarde wordt alleen ingesteld voor een fout 429.                                                                   |
|  |  |  |


### <a name="subscribe"></a>Aanmelden

Het eindpunt abonneren kan gebruikers een abonnement om een SaaS-service voor een bepaald abonnement te starten en inschakelen van facturering in het commerce-systeem.

**PUT**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}* ?api-version=2017-04-15**

| **Parameternaam**  | **Beschrijving**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | De unieke Id van saas-abonnement dat is verkregen na het oplossen van het token via API oplossen.                              |
| API-versie         | De versie van de bewerking te gebruiken voor deze aanvraag. |
|  |  |

*Headers*

|  **Header-sleutel**        | **Vereist** |  **Beschrijving**                                                  |
| ------------------     | ------------ | --------------------------------------------------------------------------------------- |
| x-ms-requestid         |   Nee         | Een unieke tekenreeks-waarde voor het bijhouden van de aanvraag van de client, bij voorkeur een GUID. Als dit niet is opgegeven, wordt een gegenereerd en vindt u in de antwoordheaders. |
| x-ms-correlationid     |   Nee         | Een unieke tekenreeks-waarde voor de bewerking op de client. Deze waarde is voor het correleren van alle gebeurtenissen van clientbewerking met gebeurtenissen op de server. Als dit niet is opgegeven, wordt een gegenereerd en vindt u in de antwoordheaders. |
| If-Match/If-None-Match |   Nee         |   Validatie van sterke ETag-waarde.                                                          |
| inhoudstype           |   Ja        |    `application/json`                                                                   |
|  Autorisatie         |   Ja        |    De JSON web token (JWT) bearer-token.                                               |
| x-ms-marketplace-sessie-modus| Nee | De vlag om in te schakelen testmodus terwijl u zich abonneert op een SaaS-aanbod. Als is ingesteld, het abonnement wordt niet in rekening gebracht. Dit is handig voor ISV-scenario's te testen. Stel deze in op **'is een'**|
|  |  |  |

*Hoofdtekst*

``` json
  { 
      “planId”:””
   }      
```

| **De naam van element** | **Gegevenstype** | **Beschrijving**                      |
|------------------|---------------|--------------------------------------|
| planId           | (Vereist) Tekenreeks        | Plan-Id van de gebruiker van de SaaS-service is geabonneerd op.  |
|  |  |  |

*Responscodes*

| **HTTP-statuscode** | **Foutcode**     | **Beschrijving**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Activering van SaaS-abonnement ontvangen voor een bepaald abonnement.                   |
| 400                  | `BadRequest`         | Een vereiste headers ontbreken of de hoofdtekst van de JSON is ongeldig. |
| 403                  | `Forbidden`          | De aanroeper is niet geautoriseerd deze bewerking uit te voeren.                   |
| 404                  | `NotFound`           | Het abonnement niet vinden met de opgegeven ID                                  |
| 409                  | `Conflict`           | Een andere bewerking wordt uitgevoerd op het abonnement.                     |
| 429                  | `RequestThrottleId`  | Service is bezet verwerken van aanvragen, voer later opnieuw uit.                  |
| 503                  | `ServiceUnavailable` | Service is omlaag tijdelijk, probeer het later opnieuw.                          |
|  |  |  |

Volgen voor een 202-antwoord op de status van de aanvraagbewerking bij de bewerking-location-header. De verificatie is hetzelfde als andere Marketplace API's.

*Antwoordheaders*

| **Header-sleutel**     | **Vereist** | **Beschrijving**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Aanvraag-ID ontvangen van de client.                                                                   |
| x-ms-correlationid | Ja          | Correlatie-ID is als doorgegeven door de client, anders wordt deze waarde de correlatie-id op.                   |
| x-ms-activityid    | Ja          | Een unieke tekenreeks-waarde voor het bijhouden van de aanvraag van de service. Deze waarde wordt gebruikt voor alle aansluitingen. |
| Opnieuw proberen na        | Ja          | Interval met welke client u kunt de status controleren.                                                       |
| Bewerking-locatie | Ja          | Koppelen aan een resource om op te halen van de status van de bewerking.                                                        |
|  |  |  |

### <a name="change-plan-endpoint"></a>Eindpunt van de planning wijzigen

Het eindpunt van de wijziging kan de gebruiker van een plan op dat moment geabonneerde converteren naar een nieuw abonnement.

**PATCH**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}* ?api-version=2017-04-15**

| **Parameternaam**  | **Beschrijving**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID van de SaaS-abonnement.                              |
| API-versie         | De versie van de bewerking te gebruiken voor deze aanvraag. |
|  |  |

*Headers*

| **Header-sleutel**          | **Vereist** | **Beschrijving**                                                                                                                                                                                                                  |
|-------------------------|--------------|---------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid          | Nee           | Een unieke tekenreeks-waarde voor het bijhouden van de aanvraag van de client. Het beste een GUID. Als dit niet is opgegeven, wordt een gegenereerd en vindt u in de antwoordheaders.   |
| x-ms-correlationid      | Nee           | Een unieke tekenreeks-waarde voor de bewerking op de client. Deze waarde is voor het correleren van alle gebeurtenissen van clientbewerking met gebeurtenissen op de server. Als dit niet is opgegeven, wordt een gegenereerd en vindt u in de antwoordheaders. |
| If-None-Match /If-None-Match | Nee           | Validatie van sterke ETag-waarde.                              |
| inhoudstype            | Ja          | `application/json`                                        |
| Autorisatie           | Ja          | De JSON web token (JWT) bearer-token.                    |
|  |  |  |


*Hoofdtekst*

``` json
                { 
                    “planId”:””
                } 
```


|  **De naam van element** |  **Gegevenstype**  | **Beschrijving**                              |
|  ---------------- | -------------   | --------------------------------------       |
|  planId           |  (Vereist) Tekenreeks         | Plan-Id van de gebruiker van de SaaS-service is geabonneerd op.          |
|  |  |  |

*Responscodes*

| **HTTP-statuscode** | **Foutcode**     | **Beschrijving**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Activering van SaaS-abonnement ontvangen voor een bepaald abonnement.                   |
| 400                  | `BadRequest`         | Een vereiste headers ontbreken of de hoofdtekst van de JSON is ongeldig. |
| 403                  | `Forbidden`          | De aanroeper is niet geautoriseerd deze bewerking uit te voeren.                   |
| 404                  | `NotFound`           | Het abonnement niet vinden met de opgegeven ID                                  |
| 409                  | `Conflict`           | Een andere bewerking wordt uitgevoerd op het abonnement.                     |
| 429                  | `RequestThrottleId`  | Service is bezet verwerken van aanvragen, voer later opnieuw uit.                  |
| 503                  | `ServiceUnavailable` | Service is omlaag tijdelijk, probeer het later opnieuw.                          |
|  |  |  |

*Antwoordheaders*

| **Header-sleutel**     | **Vereist** | **Beschrijving**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Aanvraag-ID ontvangen van de client.                                                                   |
| x-ms-correlationid | Ja          | Correlatie-ID is als doorgegeven door de client, anders wordt deze waarde de correlatie-id op.                   |
| x-ms-activityid    | Ja          | Een unieke tekenreeks-waarde voor het bijhouden van de aanvraag van de service. Deze waarde wordt gebruikt voor alle aansluitingen. |
| Opnieuw proberen na        | Ja          | Interval met welke client u kunt de status controleren.                                                       |
| Bewerking-locatie | Ja          | Koppelen aan een resource om op te halen van de status van de bewerking.                                                        |
|  |  |  |

### <a name="delete-subscription"></a>Abonnement verwijderen

De Delete-bewerking op het eindpunt abonneren kan een gebruiker verwijderen van een abonnement met een opgegeven ID.

*Aanvraag*

**DELETE**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Parameternaam**  | **Beschrijving**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID van de SaaS-abonnement.                              |
| API-versie         | De versie van de bewerking te gebruiken voor deze aanvraag. |
|  |  |

*Headers*

| **Header-sleutel**     | **Vereist** | **Beschrijving**                                                                                                                                                                                                                  |
|--------------------|--------------| ----------------------------------------------------------|
| x-ms-requestid     | Nee           | Een unieke tekenreeks-waarde voor het bijhouden van de aanvraag van de client. Het beste een GUID. Als deze waarde niet is opgegeven, wordt een gegenereerd en vindt u in de antwoordheaders.                                                           |
| x-ms-correlationid | Nee           | Een unieke tekenreeks-waarde voor de bewerking op de client. Deze waarde is voor het correleren van alle gebeurtenissen van clientbewerking met gebeurtenissen op de server. Als dit niet is opgegeven, wordt een gegenereerd en vindt u in de antwoordheaders. |
| Autorisatie      | Ja          | De JSON web token (JWT) bearer-token.                    |
|  |  |  |
 

*Responscodes*

| **HTTP-statuscode** | **Foutcode**     | **Beschrijving**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Activering van SaaS-abonnement ontvangen voor een bepaald abonnement.                   |
| 400                  | `BadRequest`         | Een vereiste headers ontbreken of de hoofdtekst van de JSON is ongeldig. |
| 403                  | `Forbidden`          | De aanroeper is niet geautoriseerd deze bewerking uit te voeren.                   |
| 404                  | `NotFound`           | Het abonnement niet vinden met de opgegeven ID                                  |
| 429                  | `RequestThrottleId`  | -Service is bezet het verwerken van aanvragen, probeer het later opnieuw.                  |
| 503                  | `ServiceUnavailable` | Service is tijdelijk niet beschikbaar. Probeer het later opnieuw.                          |
|  |  |  |

Volgen voor een 202-antwoord op de status van de aanvraagbewerking bij de bewerking-location-header. De verificatie is hetzelfde als andere Marketplace API's.

*Antwoordheaders*

| **Header-sleutel**     | **Vereist** | **Beschrijving**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Aanvraag-ID ontvangen van de client.                                                                   |
| x-ms-correlationid | Ja          | Correlatie-ID is als doorgegeven door de client, anders wordt dit de correlatie-id op.                   |
| x-ms-activityid    | Ja          | Een unieke tekenreeks-waarde voor het bijhouden van de aanvraag van de service. Dit wordt gebruikt voor alle aansluitingen. |
| Opnieuw proberen na        | Ja          | Interval met welke client u kunt de status controleren.                                                       |
| Bewerking-locatie | Ja          | Koppelen aan een resource om op te halen van de status van de bewerking.                                                        |
|   |  |  |

### <a name="get-operation-status"></a>Bewerkingsstatus ophalen

Dit eindpunt kan een gebruiker voor het bijhouden van de status van een geactiveerde asynchrone bewerking (aanmelden/afmelden/wijzigen-plan).

*Aanvraag*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/operations/*{bewerkings-id}*?api-version=2017-04-15**

| **Parameternaam**  | **Beschrijving**                                       |
|---------------------|-------------------------------------------------------|
| operationId         | Unieke ID voor de bewerking die wordt geactiveerd.                |
| API-versie         | De versie van de bewerking te gebruiken voor deze aanvraag. |
|  |  |


*Headers*

| **Header-sleutel**     | **Vereist** | **Beschrijving**                                                                                                                                                                                                                  |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Nee           | Een unieke tekenreeks-waarde voor het bijhouden van de aanvraag van de client. Het beste een GUID. Als deze waarde niet is opgegeven, wordt een gegenereerd en vindt u in de antwoordheaders.   |
| x-ms-correlationid | Nee           | Een unieke tekenreeks-waarde voor de bewerking op de client. Deze waarde is voor het correleren van alle gebeurtenissen van clientbewerking met gebeurtenissen op de server. Als deze waarde niet is opgegeven, wordt een gegenereerd en vindt u in de antwoordheaders.  |
| Autorisatie      | Ja          | De JSON web token (JWT) bearer-token.                    |
|  |  |  | 
  

*De hoofdtekst van antwoord*

``` json
  { 
      “id”: “”, 
      “status”:””, 
       “resourceLocation”:””, 
      “created”:””, 
      “lastModified”:”” 
  } 
```

| **Parameternaam** | **Gegevenstype** | **Beschrijving**                                                                                                                                               |
|--------------------|---------------|-------------------------------------------------------------------------------------------|
| id                 | Reeks        | ID van de bewerking.                                                                      |
| status             | Enum          | Status van de bewerking, een van de volgende: `In Progress`, `Succeeded`, of `Failed`.          |
| resourceLocation   | Reeks        | Koppelen aan het abonnement dat is gemaakt of gewijzigd. Hiermee wordt de client om op te halen van de geüpdate toestand post-bewerking. Deze waarde is niet ingesteld voor `Unsubscribe` bewerkingen. |
| gemaakt            | DateTime      | Bewerking maken van de tijd in UTC.                                                           |
| lastModified       | DateTime      | Laatste update voor de bewerking die wordt in UTC.                                                      |
|  |  |  |

*Responscodes*

| **HTTP-statuscode** | **Foutcode**     | **Beschrijving**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | De get-aanvraag is opgelost en de hoofdtekst van het antwoord bevat.    |
| 400                  | `BadRequest`         | Een vereiste headers ontbreken of er is een ongeldige api-versie opgegeven. |
| 403                  | `Forbidden`          | De aanroeper is niet geautoriseerd deze bewerking uit te voeren.                      |
| 404                  | `NotFound`           | Het abonnement niet vinden met de opgegeven ID                                     |
| 429                  | `RequestThrottleId`  | Service is bezet verwerken van aanvragen, voer later opnieuw uit.                     |
| 503                  | `ServiceUnavailable` | Service is omlaag tijdelijk, probeer het later opnieuw.                             |
|  |  |  |

*Antwoordheaders*

| **Header-sleutel**     | **Vereist** | **Beschrijving**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Aanvraag-ID ontvangen van de client.                                                                   |
| x-ms-correlationid | Ja          | Correlatie-ID is als doorgegeven door de client, anders wordt dit de correlatie-id op.                   |
| x-ms-activityid    | Ja          | Een unieke tekenreeks-waarde voor het bijhouden van de aanvraag van de service. Dit wordt gebruikt voor alle aansluitingen. |
| Opnieuw proberen na        | Ja          | Interval met welke client u kunt de status controleren.                                                       |
|  |  |  |

### <a name="get-subscription"></a>Abonnement ophalen

De Get-actie op abonneren eindpunt kan een gebruiker een abonnement met een bepaalde resource-id ophalen.

*Aanvraag*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Parameternaam**  | **Beschrijving**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID van de SaaS-abonnement.                              |
| API-versie         | De versie van de bewerking te gebruiken voor deze aanvraag. |
|  |  |

*Headers*

| **Header-sleutel**     | **Vereist** | **Beschrijving**                                                                                           |
|--------------------|--------------|-----------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Nee           | Een unieke tekenreeks-waarde voor het bijhouden van de aanvraag van de client, bij voorkeur een GUID. Als deze waarde niet is opgegeven, wordt een gegenereerd en vindt u in de antwoordheaders.                                                           |
| x-ms-correlationid | Nee           | Een unieke tekenreeks-waarde voor de bewerking op de client. Deze waarde is voor het correleren van alle gebeurtenissen van clientbewerking met gebeurtenissen op de server. Als deze waarde niet is opgegeven, wordt een gegenereerd en vindt u in de antwoordheaders. |
| Autorisatie      | Ja          | De JSON web token (JWT) bearer-token.                                                                    |
|  |  |  |

*De hoofdtekst van antwoord*

``` json
  { 
      “id”: “”, 
      “saasSubscriptionName”:””, 
      “offerId”:””, 
       “planId”:””, 
      “saasSubscriptionStatus”:””, 
      “created”:””, 
      “lastModified”: “” 
  }
```
| **Parameternaam**     | **Gegevenstype** | **Beschrijving**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | Reeks        | Abonnement-resource-ID van SaaS in Azure.    |
| offerId                | Reeks        | Aanbiedings-ID die de gebruiker een abonnement op.         |
| planId                 | Reeks        | Plan-ID die de gebruiker een abonnement op.          |
| saasSubscriptionName   | Reeks        | De naam van de SaaS-abonnement.                |
| saasSubscriptionStatus | Enum          | Status van de bewerking.  Een van de volgende:  <br/> - `Subscribed`: Abonnement is actief.  <br/> - `Pending`: Gebruiker maken van de resource, maar deze niet is geactiveerd door de ISV.   <br/> - `Unsubscribed`: De gebruiker zich heeft afgemeld.   <br/> - `Suspended`: De gebruiker heeft het abonnement onderbroken.   <br/> - `Deactivated`: Azure-abonnement is onderbroken.  |
| gemaakt                | DateTime      | Abonnement maken van de waarde van het tijdstempel in UTC. |
| lastModified           | DateTime      | Abonnement gewijzigd tijdstempelwaarde in UTC. |
|  |  |  |

*Responscodes*

| **HTTP-statuscode** | **Foutcode**     | **Beschrijving**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | De get-aanvraag is opgelost en de hoofdtekst van het antwoord bevat.    |
| 400                  | `BadRequest`         | Een vereiste headers ontbreken of er is een ongeldige api-versie opgegeven. |
| 403                  | `Forbidden`          | De aanroeper is niet geautoriseerd deze bewerking uit te voeren.                      |
| 404                  | `NotFound`           | Het abonnement niet vinden met de opgegeven ID                                     |
| 429                  | `RequestThrottleId`  | Service is bezet verwerken van aanvragen, voer later opnieuw uit.                     |
| 503                  | `ServiceUnavailable` | Service is omlaag tijdelijk, probeer het later opnieuw.                             |
|  |  |  |

*Antwoordheaders*

| **Header-sleutel**     | **Vereist** | **Beschrijving**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Aanvraag-ID ontvangen van de client.                                                                   |
| x-ms-correlationid | Ja          | Correlatie-ID is als doorgegeven door de client, anders wordt dit de correlatie-id op.                   |
| x-ms-activityid    | Ja          | Een unieke tekenreeks-waarde voor het bijhouden van de aanvraag van de service. Dit wordt gebruikt voor alle aansluitingen. |
| Opnieuw proberen na        | Nee           | Interval met welke client u kunt de status controleren.                                                       |
| eTag               | Ja          | Koppelen aan een resource om op te halen van de status van de bewerking.                                                        |
|  |  |  |


### <a name="get-subscriptions"></a>Abonnementen ophalen

De Get-actie op abonnementen eindpunt kan een gebruiker om op te halen van alle abonnementen voor alle aanbiedingen in de ISV.

*Aanvraag*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2017-04-15**

| **Parameternaam**  | **Beschrijving**                                       |
|---------------------|-------------------------------------------------------|
| API-versie         | De versie van de bewerking te gebruiken voor deze aanvraag. |
|  |  |

*Headers*

| **Header-sleutel**     | **Vereist** | **Beschrijving**                                           |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | Nee           | Een unieke tekenreeks-waarde voor het bijhouden van de aanvraag van de client. Het beste een GUID. Als deze waarde niet is opgegeven, wordt een gegenereerd en vindt u in de antwoordheaders.             |
| x-ms-correlationid | Nee           | Een unieke tekenreeks-waarde voor de bewerking op de client. Deze waarde is voor het correleren van alle gebeurtenissen van clientbewerking met gebeurtenissen op de server. Als deze waarde niet is opgegeven, wordt een gegenereerd en vindt u in de antwoordheaders. |
| Autorisatie      | Ja          | De JSON web token (JWT) bearer-token.                    |
|  |  |  |


*De hoofdtekst van antwoord*

``` json
  { 
      “id”: “”, 
      “saasSubscriptionName”:””, 
      “offerId”:””, 
       “planId”:””, 
      “saasSubscriptionStatus”:””, 
      “created”:””, 
      “lastModified”: “”
  }
```

| **Parameternaam**     | **Gegevenstype** | **Beschrijving**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | Reeks        | Abonnement-resource-ID van SaaS in Azure.    |
| offerId                | Reeks        | Aanbiedings-ID die de gebruiker een abonnement op.         |
| planId                 | Reeks        | Plan-ID die de gebruiker een abonnement op.          |
| saasSubscriptionName   | Reeks        | De naam van de SaaS-abonnement.                |
| saasSubscriptionStatus | Enum          | Status van de bewerking.  Een van de volgende:  <br/> - `Subscribed`: Abonnement is actief.  <br/> - `Pending`: Gebruiker maken van de resource, maar deze niet is geactiveerd door de ISV.   <br/> - `Unsubscribed`: De gebruiker zich heeft afgemeld.   <br/> - `Suspended`: De gebruiker heeft het abonnement onderbroken.   <br/> - `Deactivated`: Azure-abonnement is onderbroken.  |
| gemaakt                | DateTime      | Abonnement maken van de waarde van het tijdstempel in UTC. |
| lastModified           | DateTime      | Abonnement gewijzigd tijdstempelwaarde in UTC. |
|  |  |  |

*Responscodes*

| **HTTP-statuscode** | **Foutcode**     | **Beschrijving**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | De get-aanvraag is opgelost en de hoofdtekst van het antwoord bevat.    |
| 400                  | `BadRequest`         | Een vereiste headers ontbreken of er is een ongeldige api-versie opgegeven. |
| 403                  | `Forbidden`          | De aanroeper is niet geautoriseerd deze bewerking uit te voeren.                      |
| 404                  | `NotFound`           | Het abonnement niet vinden met de opgegeven ID                                     |
| 429                  | `RequestThrottleId`  | -Service is bezet het verwerken van aanvragen, probeer het later opnieuw.                     |
| 503                  | `ServiceUnavailable` | Service is tijdelijk niet beschikbaar. Probeer het later opnieuw.                             |
|  |  |  |

*Antwoordheaders*

| **Header-sleutel**     | **Vereist** | **Beschrijving**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Aanvraag-ID ontvangen van de client.                                                                   |
| x-ms-correlationid | Ja          | Correlatie-ID is als doorgegeven door de client, anders wordt dit de correlatie-id op.                   |
| x-ms-activityid    | Ja          | Een unieke tekenreeks-waarde voor het bijhouden van de aanvraag van de service. Dit wordt gebruikt voor alle aansluitingen. |
| Opnieuw proberen na        | Nee           | Interval met welke client u kunt de status controleren.                                                       |
|  |  |  |
