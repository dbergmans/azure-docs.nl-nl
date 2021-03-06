---
title: 'OpenVPN op Azure VPN-Gateway configureren: PowerShell | Microsoft Docs'
description: Stappen voor het configureren van OpenVPN voor Azure VPN-Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/26/2018
ms.author: cherylmc
ms.openlocfilehash: 958f4f46ec6ba407df7c739b7c62aa1489458485
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2018
ms.locfileid: "47408273"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway-preview"></a>OpenVPN configureren voor Azure point-to-site VPN-Gateway (Preview)

Dit artikel helpt u bij het instellen van OpenVPN op Azure VPN-Gateway. Het artikel wordt ervan uitgegaan dat u al een werkomgeving voor punt-naar-site. Als u dit niet doet, gebruikt u de instructies in stap 1 te maken van een punt-naar-site-VPN.

> [!IMPORTANT]
> Deze openbare preview-versie wordt aangeboden zonder serviceovereenkomst en moet niet worden gebruikt voor productieworkloads. Bepaalde functies worden mogelijk niet ondersteund, zijn mogelijk beperkt of zijn mogelijk niet beschikbaar in alle Azure-locaties. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register"></a>Deze functie registreren

Klik op de **TryIt** in deze stappen voor het registreren van deze functie eenvoudig met Azure Cloud Shell.

>[!NOTE]
>Als u deze functie niet registreert, wordt het niet mogelijk om deze te gebruiken.
>

Nadat u hebt geklikt **TryIt** om te openen in de Azure Cloud Shell, kopieer en plak de volgende opdrachten:

```azurepowershell-interactive
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```
 
```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```

Zodra de functie wordt weergegeven als ingeschreven, Registreer het abonnement op de Microsoft.Network-naamruimte.

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="vnet"></a>1. Een punt-naar-site-VPN maken

Als u nog een werkende punt-naar-site-omgeving hebt, volgt u de instructies voor het maken van een. Zie [maken van een punt-naar-site-VPN](vpn-gateway-howto-point-to-site-resource-manager-portal.md) maken en configureren van een punt-naar-site VPN-gateway met systeemeigen Azure certificaatverificatie.

## <a name="cmdlets"></a>2. PowerShell-cmdlets installeren

Installeer de meest recente versie van de PowerShell-cmdlets van Resource Manager. Zie [How to install and configure Azure PowerShell](/powershell/azure/overview) (Azure PowerShell installeren en configureren) voor meer informatie over het installeren van de PowerShell-cmdlets. Dit is belangrijk omdat eerdere versies van de cmdlets niet de huidige waarden bevatten die u nodig hebt voor deze oefening.

## <a name="enable"></a>3. OpenVPN inschakelen op de gateway

Schakel OpenVPN op uw gateway. Zorg ervoor dat de gateway is al geconfigureerd voor punt-naar-site (IKEv2 of SSTP) voordat u de volgende opdrachten uitvoert:

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Volgende stappen

Zie configureren van clients voor OpenVPN [OpenVPN configureren clients](vpn-gateway-howto-openvpn-clients.md).