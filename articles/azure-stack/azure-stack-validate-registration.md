---
title: Valideren van de registratie van Azure voor Azure Stack | Microsoft Docs
description: Gebruik de Azure Stack-gereedheid van de vereisten voor het valideren van de registratie van Azure.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/08/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 51753a5324bbbcbf4e951628a42dd3bf425354af
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2018
ms.locfileid: "49957579"
---
# <a name="validate-azure-registration"></a>Valideren van de registratie van Azure 
Gebruik het hulpprogramma Azure Stack gereedheid van de Registercontrole (AzsReadinessChecker) om te valideren dat uw Azure-abonnement klaar is voor gebruik met Azure Stack. Registratie valideert voordat u begint met een Azure Stack-implementatie. De gereedheid van vereisten wordt gevalideerd:
- Het Azure-abonnement met u is een ondersteund type. Abonnementen moet een Cloud Service Provider (CSP) of een Enterprise Agreement (EA). 
- Het account waarmee u uw abonnement registreren bij Azure kan zich aanmelden bij Azure en is de eigenaar van een abonnement. 

Zie voor meer informatie over Azure Stack-registratie, [Azure Stack registreren met Azure](azure-stack-registration.md). 

## <a name="get-the-readiness-checker-tool"></a>De gereedheid van de Registercontrole ophalen
Download de nieuwste versie van het hulpprogramma Azure Stack gereedheid van de Registercontrole (AzsReadinessChecker) is beschikbaar op [PSGallery](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Vereisten
De volgende vereisten moeten worden voldaan.

**De computer waarop het hulpprogramma wordt uitgevoerd:**
 - Windows 10 of Windows Server 2016, met een internetverbinding.
 - PowerShell 5.1 of hoger. Voer de volgende PowerShell-opdracht om te controleren of uw versie, en bekijk vervolgens de *belangrijke* versie en *kleine* versies:  

    >`$PSVersionTable.PSVersion` 
 - Configureer [PowerShell voor Azure Stack](azure-stack-powershell-install.md). 
 - Download de nieuwste versie van [Microsoft Azure Stack-gereedheid Checker](https://aka.ms/AzsReadinessChecker) hulpprogramma.  

**Azure Active Directory-omgeving:**
 - Identificeer de gebruikersnaam en wachtwoord voor een account dat eigenaar van het Azure-abonnement dat u met Azure Stack gebruiken wilt.  
 - Identificeer de abonnements-ID voor de Azure-abonnement die u wilt gebruiken. 
 - Identificeren van de Azure-omgeving u gebruikt: *AzureCloud*, *AzureGermanCloud*, of *AzureChinaCloud*.

## <a name="validate-azure-registration"></a>Valideren van de registratie van Azure
1. Op een computer die voldoet aan de vereisten, open een beheeropdrachtprompt PowerShell en voer de volgende opdracht voor het installeren van de AzsReadinessChecker.
    > `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

2. Voer de volgende om in te stellen van de PowerShell-prompt *$registrationCredential* als het account dat is eigenaar van het abonnement.   Vervang *subscriptionowner@contoso.onmicrosoft.com* met uw account en de tenant. 
    > `$registrationCredential = Get-Credential subscriptionowner@contoso.onmicrosoft.com -Message "Enter Credentials for Subscription Owner"`

3. Voer de volgende om in te stellen van de PowerShell-prompt *$subscriptionID* als de Azure-abonnement u wilt gebruiken. Vervang *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx* door uw eigen abonnement-ID.  
     > `$subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"` 

4. Voer de volgende voor het starten van de validatie van uw abonnement van de PowerShell-prompt 
   - Geef de waarde voor Azure-omgeving als *AzureCloud*, *AzureGermanCloud*, of *AzureChinaCloud*.  
   - Geef uw Azure Active Directory-beheerder en de naam van uw Azure Active Directory-Tenant. 

   > `Invoke-AzsRegistrationValidation -RegistrationAccount $registrationCredential -AzureEnvironment AzureCloud -RegistrationSubscriptionID $subscriptionID`

5. Nadat het hulpprogramma wordt uitgevoerd, Controleer de uitvoer. Controleer of de status OK voor aanmelding en de vereisten voor apparaatregistratie. Een succesvolle validatie wordt weergegeven als volgt uit:  
````PowerShell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: OK

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
````


## <a name="report-and-log-file"></a>Rapport en een logboekbestand
Elke validatie wordt uitgevoerd, resultaten van deze vereistencontrole **AzsReadinessChecker.log** en **AzsReadinessCheckerReport.json**. De locatie van deze bestanden wordt weergegeven met de validatieresultaten in PowerShell. 

Deze bestanden kunnen u helpen de validatiestatus delen voordat u Azure Stack implementeren of validatieproblemen onderzoeken. Beide bestanden behouden de resultaten van elke volgende validatiecontrole. Het rapport bevat de bevestiging van uw implementatie-team van de configuratie van de identiteit. Het logboekbestand kan helpen bij uw implementatie of ondersteuning team validatieproblemen onderzoeken. 

Standaard beide bestanden worden geschreven naar *C:\Users\<gebruikersnaam > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
 - Gebruik de **- OutputPath** ***&lt;pad&gt;*** parameter aan het einde van het uitvoeren vanaf de opdrachtregel om op te geven van een andere locatie.   
 - Gebruik de **- CleanReport** parameter aan het einde van de opdracht uitvoeren om te wissen van gegevens uit *AzsReadinessCheckerReport.json*.  over eerdere uitvoeringen van het hulpprogramma. Voor meer informatie, [Azure Stack-validatierapport](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Validatiefouten
Als een validatiecontrole mislukt, wordt informatie over de fout weergegeven in het PowerShell-venster. Het hulpprogramma wordt ook informatie in de AzsReadinessChecker.log geregistreerd.

De volgende voorbeelden geven richtlijnen op algemene validatiefouten.

### <a name="user-must-be-an-owner-of-the-subscription"></a>Gebruiker moet een eigenaar van het abonnement   
````PowerShell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail 
Error Details for registration account admin@contoso.onmicrosoft.com:
The user admin@contoso.onmicrosoft.com is role(s) Reader for subscription 3f961d1c-d1fb-40c3-99ba-44524b56df2d. User must be an owner of the subscription to be used for registration.
Additional help URL https://aka.ms/AzsRemediateRegistration

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
````
**Oorzaak** -het account is niet een beheerder van het Azure-abonnement.   

**Resolutie** -een account dat een beheerder van de Azure-abonnement dat wordt gefactureerd voor gebruik van de Azure Stack-implementatie gebruiken.


### <a name="expired-or-temporary-password"></a>Verlopen of tijdelijk wachtwoord 
````PowerShell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail 
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription 3f961d1c-d1fb-40c3-99ba-44524b56df2d using account admin@contoso.onmicrosoft.com failed with AADSTS50055: Force Change P
assword.
Trace ID: 48fe06f5-a5b4-4961-ad45-a86964689900
Correlation ID: 3dd1c9b2-72fb-46a0-819d-058f7562cb1f
Timestamp: 2018-10-22 11:16:56Z: The remote server returned an error: (401) Unauthorized.

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
````
**Oorzaak** -het account kan niet aanmelden omdat het wachtwoord is verlopen of tijdelijk is.     

**Resolutie** - In PowerShell, uitvoeren en volg de aanwijzingen voor het wachtwoord opnieuw instellen. 
  > `Login-AzureRMAccount` 

U kunt ook, meld u aan bij https://portal.azure.com als het account en de gebruiker gedwongen om het wachtwoord te wijzigen.


### <a name="unknown-user-type"></a>Onbekende gebruikerstype  
````PowerShell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail 
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription 3f961d1c-d1fb-40c3-99ba-44524b56df2d using account admin@contoso.onmicrosoft.com failed with unknown_user_type: Unknown Us
er Type

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
````
**Oorzaak** -het account zich niet aanmelden bij de opgegeven Azure Active Directory-omgeving. In dit voorbeeld *AzureChinaCloud* is opgegeven als de *AzureEnvironment*.  

**Resolutie** -bevestigen dat het account geldig voor de opgegeven Azure-omgeving is. Voer in PowerShell het volgende om te controleren of dat het account is geldig voor een specifieke omgeving.     
  > `Login-AzureRmAccount -EnvironmentName AzureChinaCloud`


## <a name="next-steps"></a>Volgende stappen
[Valideren van de Azure-identiteit](azure-stack-validate-identity.md)
[weergeven van het rapport gereedheid](azure-stack-validation-report.md)
[aandachtspunten voor algemene Azure Stack-integratie](azure-stack-datacenter-integration.md)

