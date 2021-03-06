---
title: Azure Cloud Shell-gebruiksgegevens voor LUIS
titleSuffix: Azure Cognitive Services
description: Informatie over het verkrijgen van informatie over het gebruik in Azure Cloud Shell voor LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/06/2017
ms.author: diberry
ms.openlocfilehash: 42bd8ef95ac1b55e76fe1cf0702b79de91832741
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2018
ms.locfileid: "48870176"
---
# <a name="usage-data-for-luis-service-from-azure-cloud-shell"></a>Gegevens over het gebruik van LUIS-service van Azure Cloud Shell
De Azure-portal kunt u PowerShell-cmdlets gebruiken om te werken met LUIS-resources. 

Deze cmdlets kunt u [maken](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/new-azurermcognitiveservicesaccount?view=azurermps-6.0.0) een LUIS-abonnement, informatie over het abonnement, met inbegrip van [gebruik](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/get-azurermcognitiveservicesaccountusage?view=azurermps-6.0.0), en [verwijderen](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/remove-azurermcognitiveservicesaccount?view=azurermps-6.0.0) het abonnement. 

## <a name="cloud-shell-storage-account-and-authentication"></a>Cloud shell-storage-account en -verificatie
Als u wilt gebruiken van PowerShell in Azure portal [cloud shell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell), moet u beschikken over een Azure storage-account. Als u geen een [opslagaccount](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage), wordt u gevraagd een te maken. Het storage-account kunt u voor het opslaan van de PowerShell-scripts in de cloudshell.  

U moet ook worden geverifieerd bij Azure in de cloudshell toegang tot bronnen. 

Nadat u een storage-account hebt en worden geverifieerd, kunt u PowerShell-cmdlets kunt uitvoeren.

## <a name="open-cloud-shell"></a>Cloud Shell openen
Wanneer u de Azure portal cloudshell gebruikt, bent u altijd op de meest recente versie van PowerShell. 

Gebruik de **Cloud Shell openen** knop Cloud Shell openen of open een browser met [ https://shell.azure.com ](https://shell.azure.com). 

<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><image src="https://shell.azure.com/images/launchcloudshell.png" /></a>

## <a name="luis-endpoint-usage-information"></a>LUIS informatie over het gebruik van het eindpunt

De PowerShell-cmdlet 6.x `Get-AzureRmCognitiveServicesAccountUsage`, biedt informatie over het gebruik van Microsoft Cognitive Services, met inbegrip van LUIS. [Get-azurermcognitiveservicesaccountusage toe](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/get-azurermcognitiveservicesaccountusage?view=azurermps-6.0.0) moet u de resourcegroep en de resourcenaam van de service. 

De syntaxis van de opdracht is:

```
Get-AzureRmCognitiveServicesAccountUsage -ResourceGroupName my-resource-group -Name my-luis-service-name
```

In het volgende voorbeeld wordt de naam van de resourcegroep is `luis-westus-rg` en de naam van het LUIS-service-abonnement is `luis-westus-1`. Deze beide namen worden gekozen wanneer de LUIS-service is gemaakt. 

De cmdlet retourneert informatie over het gebruik van 16 van 10.000 eindpunt treffers in een periode van 30 dagen met de periode eindigt op 7 juni gebruikt:

```
CurrentValue  : 16
Name          : LUIS.Calls
Limit         : 10000
Status        : Included
Unit          : Count
QuotaPeriod   : 30.00:00:00
NextResetTime : 2018-06-07T18:28:52Z
```

De opdracht Opslaan als een PowerShell-bestand, *.ps1, in de Azure storage-account dat is gekoppeld aan de cloudshell en op elk gewenst moment worden uitgevoerd. 

![Script uitvoeren vanuit opslag](./media/luis-how-to-manage-from-powershell/run-script-from-storage.png)

Wanneer het script is opgeslagen op de schijf van de cloud, kunt u het PowerShell-script uitvoeren van de Azure cloudshell van telefoon-app. 

![Script uitvoeren vanuit de opslag in telefoon-app](./media/luis-how-to-manage-from-powershell/phone-app.png)