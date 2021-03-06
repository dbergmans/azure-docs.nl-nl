---
title: Profiel live Azure-web-apps met Application Insights | Microsoft Docs
description: Profiel live web-apps op Azure met Application Insights Profiler.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 9aae08aa5906f341a890ac15e30d2863109d83a2
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/26/2018
ms.locfileid: "50140005"
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>Profiel live Azure-web-apps met Application Insights

Profiler is op dit moment werkt voor ASP.NET en ASP.NET Core web-apps die worden uitgevoerd op Web-Apps. De basis-servicelaag of hoger is vereist voor het gebruik van Profiler.

## <a id="installation"></a> Profiler voor uw Web-Apps inschakelen
Als u wilt Profiler inschakelen voor een web-app, volg de onderstaande instructies. Als u een ander type Azure-service uitvoert, vindt hier u instructies voor het inschakelen van Profiler op andere ondersteunde platforms:
* [Cloud Services](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric-toepassingen](app-insights-profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtuele machines](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)


Application Insights Profiler wordt geïnstalleerd met de extensie Application Insights-site. U moet de site-extensie installeren en configureren voor het ophalen van profielen voor uw Azure-Web-Apps. Nadat u een Web-App hebt geïmplementeerd, zelfs als u hebt de App Insights-SDK opgenomen in de broncode, volgt u de stappen hieronder om de profiler inschakelen.

1. Ga naar de **App Services** deelvenster in de Azure-portal.
1. Navigeer naar **instellingen > controle** deelvenster.

   ![App Insights inschakelen in App Services-portal](./media/app-insights-profiler/AppInsights-AppServices.png)

1. Ofwel de instructies in het deelvenster te maken van een nieuwe resource of Selecteer een bestaande App Insights-resource voor het bewaken van uw web-app. Accepteer alle standaardopties. **Gegevens op codeniveau** is standaard ingeschakeld en kunt u Profiler.

   ![App Insights-site-extensie toevoegen][Enablement UI]

1. Profiler is nu geïnstalleerd met de App Insights-site-extensie en is ingeschakeld met behulp van een App-instelling van de App-Services.

    ![App-instelling voor Profiler][profiler-app-setting]

## <a name="disable-profiler"></a>Profiler uitschakelen

Profiler voor een afzonderlijke web-app-exemplaar, onder opnieuw te starten of stoppen **-webtaken**, gaat u naar de Web-Apps-resource. Als u wilt verwijderen van Profiler, gaat u naar **extensies**.

![Profiler uitschakelen voor een webtaak][disable-profiler-webjob]

Het is raadzaam dat u hebt ingeschakeld op al uw WebApps voor het detecteren van eventuele prestatieproblemen zo vroeg mogelijk Profiler.

Als u wijzigingen aan uw webtoepassing implementeren met web Deploy, zorg er dan voor dat u de map App_Data uitsluiten van wordt verwijderd tijdens de implementatie. De Profiler-extensie bestanden verwijderd anders de volgende keer dat u de web-App in Azure implementeren.



## <a name="next-steps"></a>Volgende stappen

* [Werken met Application Insights in Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[appinsights-in-appservices]:./media/app-insights-profiler/AppInsights-AppServices.png
[Enablement UI]: ./media/app-insights-profiler/Enablement_UI.png
[profiler-app-setting]:./media/app-insights-profiler/profiler-app-setting.png
[performance-blade]: ./media/app-insights-profiler/performance-blade.png
[performance-blade-examples]: ./media/app-insights-profiler/performance-blade-examples.png
[performance-blade-v2-examples]:./media/app-insights-profiler/performance-blade-v2-examples.png
[trace-explorer]: ./media/app-insights-profiler/trace-explorer.png
[trace-explorer-toolbar]: ./media/app-insights-profiler/trace-explorer-toolbar.png
[trace-explorer-hint-tip]: ./media/app-insights-profiler/trace-explorer-hint-tip.png
[trace-explorer-hot-path]: ./media/app-insights-profiler/trace-explorer-hot-path.png
[enable-profiler-banner]: ./media/app-insights-profiler/enable-profiler-banner.png
[disable-profiler-webjob]: ./media/app-insights-profiler/disable-profiler-webjob.png
[linked app services]: ./media/app-insights-profiler/linked-app-services.png

