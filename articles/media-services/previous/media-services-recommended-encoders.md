---
title: Meer informatie over coderingsprogramma's die zijn aanbevolen door Azure Media Services | Microsoft Docs
description: Meer informatie over coderingsprogramma's die zijn aanbevolen door mediaservices
services: media-services
keywords: codering; coderingsprogramma's; media
author: dbgeorge
manager: johndeu
ms.author: johndeu
ms.date: 09/13/2018
ms.topic: article
ms.service: media-services
ms.openlocfilehash: c90d6a5784fe9d80df4fab304b6122d3fa24d0b5
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45605161"
---
# <a name="recommended-on-premises-encoders"></a>Aanbevolen on-premises coderingsprogramma 's
Wanneer een live streamen met Azure Media Services, kunt u opgeven hoe u wilt dat het kanaal voor het ontvangen van de invoerstroom. Als u gebruiken van een on-premises coderingsprogramma met een live codering wilt, moet uw encoder een hoge kwaliteit single-bitrate stream pushen als uitvoer. Als u kiest voor het gebruik van een on-premises coderingsprogramma met een pass through-kanaal, moet uw encoder een multi-bitrate stream pushen als uitvoer met alle kenmerken van de gewenste uitvoer. Zie voor meer informatie, [Live streamen met on-premises coderingsprogramma's](media-services-live-streaming-with-onprem-encoders.md).

Azure Media Services raadt u aan met behulp van een van de volgende live coderingsprogramma's die RTMP als uitvoer zijn:
- Adobe Flash Media Live Encoder 3.2
- Haivision Makito X HEVC
- Haivision KB
- Telestream Wirecast 8.1 +
- Telestream Wirecast S
- Teradek segment 756
- TriCaster 8000
- Tricaster Mini HD-4
- IB-Studio
- VMIX
- xStream
- Overschakelen naar Studio (iOS)

Azure Media Services raadt u aan met een van de volgende live coderingsprogramma's die multi-bitrate fragmented-MP4 (Smooth Streaming) als uitvoer:
- Media Excel Hero Live en Hero 4K (UHD/HEVC)
- Ateme TITAN Live
- Cisco digitale Media Encoder 2200
- Elemental Live
- Envivio 4Caster C4 ALG III
- Imagine Communications Selenio MCP3

> [!NOTE]
> Een live coderingsprogramma kan een single-bitrate stream naar een pass through-kanaal verzenden, maar deze configuratie wordt niet aanbevolen omdat deze niet is toegestaan voor adaptive bitrate streaming aan de client.

## <a name="how-to-become-an-on-prem-encoder-partner"></a>Hoe u een on-premises coderingsprogramma partner
Als een Azure Media Services on-premises coderingsprogramma partner verhogen Media Services uw product door uw encoder aanbeveelt voor zakelijke klanten. Een on-premises coderingsprogramma als partner wilt worden, moet u controleren of de compatibiliteit van uw on-premises coderingsprogramma met Media Services. Voer de volgende verificaties om dit te doen:

Verificatie van kanaal doorgeven
1. Maken of Ga naar uw Azure Media Services-account
2. Maak en start een **pass-through** kanaal
3. Configureer uw encoder als u wilt een multi-bitrate live stream pushen.
4. Een gepubliceerde live gebeurtenis maken
5. Het live coderingsprogramma voor ongeveer 10 minuten uitvoeren
6. Stop de live-gebeurtenis
7. Maken, een Streaming-eindpunt starten, gebruikt u een speler zoals [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) bekijken van de gearchiveerde asset om ervoor te zorgen dat afspelen heeft geen haperingen zichtbaar voor alle kwaliteitsniveaus (of u kunt ook bekijken en valideren via de voorbeeld-URL tijdens de live sessie vóór stap 6)
8. Noteer de Asset-ID, gepubliceerde streaming-URL voor het livearchief en de instellingen en de versie die wordt gebruikt vanuit uw live coderingsprogramma
9. De kanaalstatus opnieuw instellen na het maken van elk voorbeeld
10. Herhaal stap 3 t/m 9 voor alle configuraties ondersteund door uw encoder (met en zonder ad-signalering/bijschriften/verschillende snelheden codering)

Live encoding channel-verificatie
1. Maken of Ga naar uw Azure Media Services-account
2. Maak en start een **van realtime codering** kanaal
3. Configureer uw encoder als u wilt een single-bitrate live stream pushen.
4. Een gepubliceerde live gebeurtenis maken
5. Het live coderingsprogramma voor ongeveer 10 minuten uitvoeren
6. Stop de live-gebeurtenis
7. Maken, een Streaming-eindpunt starten, gebruikt u een speler zoals [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) bekijken van de gearchiveerde asset om ervoor te zorgen dat afspelen heeft geen haperingen zichtbaar voor alle kwaliteitsniveaus (of u kunt ook bekijken en valideren via de voorbeeld-URL tijdens de live sessie vóór stap 6)
8. Noteer de Asset-ID, gepubliceerde streaming-URL voor het livearchief en de instellingen en de versie die wordt gebruikt vanuit uw live coderingsprogramma
9. De kanaalstatus opnieuw instellen na het maken van elk voorbeeld
10. Herhaal stap 3 t/m 9 voor alle configuraties ondersteund door uw encoder (met en zonder ad-signalering/bijschriften/verschillende snelheden codering)

Duurzaamheid verificatie
1. Maken of Ga naar uw Azure Media Services-account
2. Maak en start een **pass-through** kanaal
3. Configureer uw encoder als u wilt een multi-bitrate live stream pushen.
4. Een gepubliceerde live gebeurtenis maken
5. Uitvoeren van uw live coderingsprogramma 1 week voor een of meer
6. Gebruik een speler zoals [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) om te bekijken van de live streaming van tijd naar een keer (of gearchiveerde asset) om ervoor te zorgen dat afspelen heeft geen zichtbare haperingen
7. Stop de live-gebeurtenis
8. Noteer de Asset-ID, gepubliceerde streaming-URL voor het livearchief en de instellingen en de versie die wordt gebruikt vanuit uw live coderingsprogramma

Ten slotte uw geregistreerde verzendinstellingen en archive-parameters voor Media Services live door e-mailen amsstreaming@microsoft.com. Bij ontvangst voert mediaservices verificatietests uitvoeren op de voorbeelden van het live coderingsprogramma. U kunt contact opnemen met de Media Services met vragen met betrekking tot dit proces.