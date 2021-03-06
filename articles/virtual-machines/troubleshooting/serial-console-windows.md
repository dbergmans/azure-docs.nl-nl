---
title: Seriële Console van virtuele Azure-Machine | Microsoft Docs
description: Bidirectionele seriële Console voor virtuele machines van Azure.
services: virtual-machines-windows
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/22/2018
ms.author: harijay
ms.openlocfilehash: facd9be037894932e516e8294e36b6b0e55374c8
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50024409"
---
# <a name="virtual-machine-serial-console"></a>Seriële Console van virtuele Machine


De seriële Console van de virtuele Machine op Azure biedt toegang tot een op tekst gebaseerde console voor Windows-machines. Deze seriële verbinding is met de seriële poort COM1 van de virtuele machine, toegang tot de virtuele machine die onafhankelijk is van het netwerk of de staat van besturingssysteem van een virtuele machine. Toegang tot de seriële console voor een virtuele machine op dit moment kunnen alleen worden uitgevoerd via Azure portal en alleen voor gebruikers die VM-Inzender hebben of hoger toegang tot de virtuele machine is toegestaan. 

Voor de seriële console-documentatie voor virtuele Linux-machines [Klik hier](serial-console-linux.md).

> [!NOTE] 
> Seriële Console voor virtuele machines is algemeen beschikbaar in de globale Azure-regio's. Op dit moment is seriële Console nog niet beschikbaar in Azure Government or Azure China-clouds.

 

## <a name="prerequisites"></a>Vereisten 

* U moet de resource Manager-implementatiemodel gebruiken. Klassieke implementaties worden niet ondersteund. 
* Virtuele machine moet zijn [diagnostische gegevens over opstarten](boot-diagnostics.md) ingeschakeld 

    ![](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

* Het account met behulp van de seriële console moet hebben [rol van Inzender](../../role-based-access-control/built-in-roles.md) voor virtuele machine en de [diagnostische gegevens over opstarten](boot-diagnostics.md) storage-account. 
* De virtuele machine waarvoor u toegang de seriële console tot moet ook een account op basis van wachtwoorden hebben. U kunt maken met de [wachtwoord opnieuw instellen](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) functionaliteit van de toegang tot VM-extensie - Zie de onderstaande schermafbeelding.

    ![](../media/virtual-machines-serial-console/virtual-machine-serial-console-reset-password.png)

## <a name="get-started-with-serial-console"></a>Aan de slag met de seriële Console
Seriële console voor virtuele machines is alleen toegankelijk via [Azure-portal](https://portal.azure.com). Hieronder volgen de stappen voor toegang tot de seriële console voor virtuele machines via de portal.

  1. De Azure-portal openen
  2. Selecteer de virtuele machines in het menu links.
  3. Klik op de virtuele machine in de lijst. De overzichtspagina voor de virtuele machine wordt geopend.
  4. Schuif omlaag naar de ondersteuning en probleemoplossing sectie en klik op de optie 'Seriële console'. Een nieuw deelvenster met de seriële console opent en start de verbinding.

## <a name="enable-serial-console-in-custom-or-older-images"></a>Seriële Console inschakelen in aangepaste of oudere installatiekopieën
Nieuwere Windows Server-installatiekopieën in Azure heeft [speciale beheerconsole](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC) standaard ingeschakeld. SAC wordt ondersteund in serverversies van Windows, maar is niet beschikbaar is op clientversies (bijvoorbeeld Windows 10, Windows 8 of Windows 7). Als u wilt inschakelen voor Windows-machines die zijn gemaakt vóór februari 2018 de seriële console, gebruikt u de volgende stappen uit: 

1. Verbinding maken met uw Windows-machine via Extern bureaublad
2. Voer de volgende opdrachten vanaf een opdrachtprompt met beheerdersrechten. 
* `bcdedit /ems {current} on`
* `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
3. Opnieuw opstarten van het systeem voor de console SAC zijn ingeschakeld

![](/media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Indien nodig, kan de SAC ook worden ingeschakeld offline:

1. De windows-schijf die u wilt dat SAC geconfigureerd voor als een gegevensschijf aan bestaande virtuele machine koppelen. 
2. Voer de volgende opdrachten vanaf een opdrachtprompt met beheerdersrechten. 
* `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
* `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

### <a name="how-do-i-know-if-sac-is-enabled"></a>Hoe weet ik of SAC is ingeschakeld?

Als [SAC](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) niet is ingeschakeld de seriële console wordt niet weergegeven voor de SAC-prompt. In sommige gevallen kan de VM-status gegevens worden weergegeven en in andere gevallen wordt het leeg zijn. Als u van een installatiekopie van Windows Server die zijn gemaakt vóór februari 2018 gebruikmaakt, wordt SAC waarschijnlijk niet worden ingeschakeld.

## <a name="enable-the-windows-boot-menu-in-serial-console"></a>Het opstartmenu Windows in de seriële Console inschakelen 

Als u nodig hebt om in te schakelen van Windows-opstartlaadprogramma wordt u gevraagd om weer te geven in de seriële console, kunt u de volgende aanvullende opties toevoegen aan uw opstartconfiguratiegegevens. Zie [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set) voor meer informatie

1. Verbinding maken met uw Windows-machine via Extern bureaublad
2. Voer de volgende opdrachten vanaf een opdrachtprompt met beheerdersrechten. 
* `bcdedit /set {bootmgr} displaybootmenu yes`
* `bcdedit /set {bootmgr} timeout 10`
* `bcdedit /set {bootmgr} bootems yes`
3. Opnieuw opstarten van het systeem voor het opstartmenu worden ingeschakeld

> [!NOTE] 
> De time-out die u hebt ingesteld voor het opstartmenu manager worden weergegeven is van invloed op de opstarttijd van uw besturingssysteem in de toekomst. Hoewel het mogelijk aanvaardbaar is voor sommige om toe te voegen een 10 tweede time-out om ervoor te zorgen dat de boot manager zichtbaar zijn via de seriële console, kunnen anderen een kortere of langere time-out wilt. De time-outwaarde op een waarde die u vertrouwd met bent instellen.

## <a name="use-serial-console-for-nmi-calls-in-windows-vms"></a>Seriële Console voor NMI aanroepen in de Windows-VM's gebruiken
Een niet-maskeren interrupt (NMI) is ontworpen voor het maken van een signaal dat software op een virtuele machine worden niet genegeerd. In het verleden zijn NMIs gebruikt om te controleren op hardwareproblemen op systemen die specifieke reactietijden vereist.  NMI vandaag, programmeurs en systeembeheerders vaak gebruikt als een mechanisme voor foutopsporing of problemen met systemen die zijn vastgelopen.

De seriële Console kan worden gebruikt voor het verzenden van een NMI met een Azure-machine via het toetsenbordpictogram in de opdrachtbalk die hieronder wordt weergegeven. Zodra de NMI wordt geleverd, worden de virtuele-machineconfiguratie bepalen hoe het systeem reageert. Windows kan worden geconfigureerd voor crashes en een geheugendump maken wanneer er een NMI worden ontvangen.

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Zie voor meer informatie over het configureren van Windows voor het maken van een crashdump wanneer deze een NMI ontvangt: [het genereren van een volledige crashdumpbestand of een kernel crashdumpbestand met behulp van een NMI op een Windows-systeem](https://support.microsoft.com/en-us/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file)

## <a name="open-cmd-or-powershell-in-serial-console"></a>Open CMD of Powershell in de seriële Console

1. Verbinding maken met de seriële Console. Als u is met de seriële Console verbonden, ziet u **SAC >** als de volgende schermafbeelding ziet:

    ![Verbinding maken met SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)

3.  Type `cmd` om een kanaal waaraan een CMD-exemplaar te maken. 
4.  Type `ch -si 1` om over te schakelen naar het kanaal dat de CMD-exemplaar wordt uitgevoerd. 
5.  Druk op Enter en geef uw referenties voor clusteraanmelding die beheerdersrechten hebben.
6.  Nadat u geldige referenties invoert, wordt het exemplaar CMD geopend.
7.  Een PowerShell-sessie starten, typt `PowerShell` in de CMD-exemplaar en druk op Enter. 

    ![Open PowerShell-exemplaar](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)


## <a name="disable-serial-console"></a>Seriële Console uitschakelen
Standaard hebben alle abonnementen seriële console-toegang ingeschakeld voor alle virtuele machines. U kunt de seriële console van het abonnement of de VM-niveau uitschakelen.

> [!NOTE]       
> Als u wilt in- of uitschakelen voor een abonnement op de seriële console, moet u hebt schrijfmachtigingen voor het abonnement. Dit omvat, maar is niet beperkt tot de beheerder of eigenaar van rollen. Aangepaste rollen kunnen ook schrijfmachtigingen hebben.

### <a name="subscription-level-disable"></a>Abonnementsniveau uitschakelen
Seriële Console kan worden uitgeschakeld voor een volledige abonnement door via de [uitschakelen Console REST API-aanroep](https://aka.ms/disableserialconsoleapi). U kunt het 'Try It' functionaliteit die beschikbaar is op de pagina van de API-documentatie uitschakelen en inschakelen van de seriële Console voor een abonnement. Voer uw `subscriptionId`, 'standaard' in de `default` veld en klik op uitvoeren. Azure CLI-opdrachten zijn nog niet beschikbaar en wordt bezorgd moeten worden op een later tijdstip. [Probeer de REST-API-aanroep hier](https://aka.ms/disableserialconsoleapi).

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

U kunt ook de reeks opdrachten hieronder gebruiken in Cloud Shell (bash-opdrachten die worden weergegeven) als wilt uitschakelen, inschakelen en weergeven van de uitgeschakelde status van de seriële console voor een abonnement. 

* De uitgeschakelde status van de seriële console voor een abonnement ophalen:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s | jq .properties
    ```
* Om uit te schakelen voor een abonnement op de seriële console:
    ```azurecli-interactive 
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/disableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```
* Om in te schakelen voor een abonnement op de seriële console:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/enableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```

### <a name="vm-level-disable"></a>VM-niveau uitschakelen
Seriële console kan worden uitgeschakeld voor specifieke virtuele machines door het uitschakelen van deze virtuele machine opstarten diagnostische instelling. Gewoon opstarten diagnostische gegevens van de Azure portal en de seriële console zal worden uitgeschakeld voor de virtuele machine uitschakelen.

## <a name="serial-console-security"></a>Seriële Console-beveiliging 

### <a name="access-security"></a>Beveiliging voor toegang 
Toegang tot de seriële console is beperkt tot gebruikers die hebben [VM inzenders](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) of hoger dan toegang tot de virtuele machine. Als uw AAD-tenant meervoudige verificatie vereist en toegang tot de seriële console ook de MFA moet omdat de toegang [Azure-portal](https://portal.azure.com).

### <a name="channel-security"></a>Beveiliging van het kanaal
Alle gegevens die worden verzonden heen en weer worden versleuteld op de kabel.

### <a name="audit-logs"></a>Controlelogboeken
Alle toegang tot de seriële console momenteel is aangemeld de [diagnostische gegevens over opstarten](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) logboeken van de virtuele machine. Toegang tot deze logboeken zijn eigendom van en beheerd door de beheerder van de virtuele machine van Azure.  

>[!CAUTION] 
Terwijl er geen wachtwoorden voor toegang voor de console bent aangemeld als opdrachten worden uitgevoerd binnen de console bevat of uitvoer van wachtwoorden, geheimen, gebruikersnamen of enige andere vorm van persoonlijk identificeerbare informatie (PII), worden die geschreven naar de diagnostische gegevens over opstarten van virtuele machine Logboeken, samen met alle andere zichtbare tekst, als onderdeel van de implementatie van de seriële console schuiven back-functionaliteit. Deze logboeken zijn circulaire en alleen personen met leesmachtigingen voor het opslagaccount voor diagnostische gegevens hebben de toegang tot, maar we raden aan na de aanbevolen procedure van het gebruik van de extern bureaublad voor alles wat die hebben mogelijk betrekking op geheimen en/of PII. 

### <a name="concurrent-usage"></a>Gelijktijdig gebruik
Als een gebruiker is verbonden met de seriële console en een andere gebruiker is toegang tot deze virtuele machine met dezelfde aanvraagt, wordt de eerste gebruiker verbroken en wordt de tweede gebruiker op een manier die lijkt op de eerste gebruiker permanent van en het verlaten van de fysieke-console en een nieuwe verbinding de gebruiker zich omlaag.

>[!CAUTION] 
Dit betekent dat de gebruiker die wordt verbroken wordt niet afgemeld. De mogelijkheid om af te dwingen een afmelden bij het verbreken van de verbinding (via SIGHUP of een vergelijkbaar mechanisme) is nog steeds in de roadmap. Voor Windows is het een automatische time-out ingeschakeld in SAC, maar u terminal time-outinstelling voor Linux configureren kunt. 

## <a name="common-scenarios-for-accessing-serial-console"></a>Algemene scenario's voor toegang tot de seriële console 
Scenario          | Acties in de seriële console                
:------------------|:-----------------------------------------
Onjuiste firewall-regels | Toegang tot de seriële console en de oplossing Windows firewall-regels. 
Bestandssysteem beschadigd/selectievakje | Toegang tot de seriële console van maken en herstellen van bestandssysteem. 
Problemen met RDP-configuratie | Toegang tot de seriële console en instellingen wijzigen. Ga naar de [RDP documentatie](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access) aan de slag.
Systeem voor het vergrendelen van netwerk| Toegang tot de seriële console via de portal voor het beheren van systeem. Sommige netwerkopdrachten worden vermeld in de [seriële Console CMD- en PowerShell-documentatie](serial-console-cmd-ps-commands.md). 
Interactie met de bootloader | BCD toegang via de seriële console. Ga naar [Opstartmenu inschakelen om weer te geven in de seriële console](#enabling-boot-menu-to-show-in-the-serial-console) aan de slag. 

## <a name="accessibility"></a>Toegankelijkheid
Toegankelijkheid is een belangrijke focus voor de seriële console van Azure. Wat dat betreft, hebben we ervoor gezorgd dat dat de seriële console is toegankelijk voor mensen met het visuele element en gehoorproblemen, evenals de mensen die misschien niet mogelijk een muis te gebruiken.

### <a name="keyboard-navigation"></a>Toetsenbordnavigatie
Gebruik de `tab` sleutel op het toetsenbord om te navigeren in de interface van de seriële console binnen de Azure-portal. Uw locatie wordt gemarkeerd op het scherm worden weergegeven. Als u wilt de focus van de seriële console-blade laten, drukt u op `Ctrl + F6` op het toetsenbord.

### <a name="use-serial-console-with-a-screen-reader"></a>Seriële console gebruiken met een schermlezer
Seriële console wordt geleverd met ingebouwde ondersteuning voor schermlezers. Navigeren om met een schermlezer ingeschakeld, kunnen de alt-tekst voor de geselecteerde knop om te worden door de schermlezer voorgelezen.

## <a name="errors"></a>Fouten
De meeste fouten zijn tijdelijke aard en het adres van de verbinding opnieuw wordt geprobeerd deze. De onderstaande tabel geeft een lijst met fouten en oplossingen

Fout                            |   Oplossing 
:---------------------------------|:--------------------------------------------|
Kan niet worden opgehaald van de instellingen voor diagnostische gegevens over opstarten voor '<VMNAME>'. Voor het gebruik van de seriële console of dat de diagnostische gegevens over die opstarten is ingeschakeld voor deze virtuele machine. | Zorg ervoor dat de virtuele machine heeft [diagnostische gegevens over opstarten](boot-diagnostics.md) ingeschakeld. 
De virtuele machine is gestopt toewijzing ongedaan gemaakt. Start de virtuele machine en probeer de seriële console-verbinding. | Virtuele machine moet zich in een status gestart voor toegang tot de seriële console
U hebt niet de vereiste machtigingen voor het gebruik van de seriële console van deze virtuele machine. Zorg ervoor dat u ten minste beschikken over machtigingen van de rol Inzender voor virtuele machine.| Toegang tot de seriële console moet bepaalde machtiging voor toegang tot. Zie [vereisten](#prerequisites) voor meer informatie
Kan niet bepalen van de resourcegroep voor het opslagaccount van de diagnostische gegevens over opstarten '<STORAGEACCOUNTNAME>'. Controleer of u diagnostische gegevens over opstarten is ingeschakeld voor deze virtuele machine en u toegang hebt tot dit opslagaccount wordt gebruikt. | Toegang tot de seriële console moet bepaalde machtiging voor toegang tot. Zie [vereisten](#prerequisites) voor meer informatie
Een antwoord 'Verboden' is opgetreden bij het openen van deze virtuele machine opstarten diagnostische storage-account. | Zorg ervoor dat diagnostische gegevens over de opstarten beschikt niet over een account-firewall. Een toegankelijke opstarten diagnostische storage-account is nodig voor de seriële console van functie.
Web socket is gesloten of kan niet worden geopend. | U moet mogelijk aan lijst met geaccepteerde `*.console.azure.com`. Een meer gedetailleerde maar langer aanpak is het whitelist de [Microsoft Azure Datacenter IP-bereiken](https://www.microsoft.com/en-us/download/details.aspx?id=41653), waardoor vrij regelmatig worden gewijzigd.
Alleen gegevens over de servicestatus wordt weergegeven bij het verbinden met een Windows-VM| Dit wordt weergegeven als de beheerconsole van speciale niet is ingeschakeld voor uw Windows-installatiekopie. Zie [toegang seriële Console voor Windows](#access-serial-console-for-windows) voor instructies over het handmatig inschakelen SAC op uw Windows-VM. Meer informatie kunnen u vinden op [Windows Health signalen](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md).

## <a name="known-issues"></a>Bekende problemen 
We zijn ons bewust van enkele problemen met de seriële console. Hier volgt een lijst van deze problemen beschreven en stappen voor risicobeperking.

Probleem                             |   Oplossing 
:---------------------------------|:--------------------------------------------|
Nadat de banner van de verbinding wordt niet weergegeven voor een logboek in de prompt te maken met invoeren | Deze pagina wordt weergegeven: [Hitting invoeren, gebeurt er niets](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Dit kan gebeuren als u een aangepaste VM, beperkte toestel of de configuratie van een opstartinstallatiekopie die Windows causers als u wilt geen correct verbinding maken met de seriële poort. Dit wordt ook als u een Windows 10 client VM, gebeuren als Windows Server-VM's alleen worden geconfigureerd met EMS is ingeschakeld.
Kan niet naar het type op SAC vragen als kernelfoutopsporing is ingeschakeld | RDP-verbinding VM en voer `bcdedit /debug {current} off` vanaf een opdrachtprompt met verhoogde bevoegdheid. Als u niet de RDP-verbinding kunt u in plaats daarvan de besturingssysteemschijf koppelen aan een andere Azure-virtuele machine en wijzigen terwijl gekoppeld als een gegevens-schijf met `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`, klikt u vervolgens wisselen de schijf weer.
Als de oorspronkelijke inhoud had een herhalende teken in PowerShell te plakken in SAC resulteert in een derde teken | Een tijdelijke oplossing is verwijderd na de PSReadLine-module op basis van de huidige sessie. Voer `Remove-Module PSReadLine` te verwijderen van de module PSReadLine uit de huidige sessie - wordt dit niet verwijderen of de module verwijderen.
Sommige invoer toetsenbord vreemd SAC uitvoer produceren (bijvoorbeeld `[A`, `[3~`) | [VT100](https://aka.ms/vtsequences) escapereeksen worden niet ondersteund door de SAC-prompt.
Het plakken van zeer lange tekenreeksen werkt niet | Seriële console beperkt de lengte van tekenreeksen in de terminal naar 2048 tekens geplakt. Zo wordt voorkomen dat de bandbreedte van de seriële poort wordt belast.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen 

**Q. Hoe kan ik feedback verzenden?**

A. Feedback geven als het probleem door te gaan naar https://aka.ms/serialconsolefeedback. U kunt ook minder (aanbevolen) feedback verzenden via azserialhelp@microsoft.com of in de categorie van de virtuele machine van de http://feedback.azure.com

**Q. Biedt ondersteuning voor de seriële console kopiëren/plakken?**

A. Ja doet. Gebruik Ctrl + Shift + C en Ctrl + Shift + V om te kopiëren en plakken in de terminal.

**Q. Wie kunt inschakelen of uitschakelen van de seriële console voor mijn abonnement?**

A. Als u wilt in- of uitschakelen van de seriële console op het niveau van een brede, door het abonnement, moet u hebt schrijfmachtigingen voor het abonnement. Rollen die gemachtigd te schrijven bevatten, maar niet beperkt zijn tot de beheerder of eigenaar van rollen. Aangepaste rollen kunnen ook schrijfmachtigingen hebben.

**Q. Wie toegang heeft tot de seriële console voor mijn VM?**

A. Hebt u toegang van Inzender-niveau of hoger met een virtuele machine voor toegang tot de seriële console van de virtuele machine. 

**Q. Mijn seriële console niet wordt weergegeven van alles zijn, wat moet ik doen?**

A. Uw installatiekopie is waarschijnlijk niet goed is geconfigureerd voor toegang tot de seriële console. Zie [seriële Console inschakelen in aangepaste of oudere installatiekopieën](#enable-serial-console-in-custom-or-older-images) voor meer informatie over het configureren van de afbeelding om in te schakelen van de seriële console.

**Q. Seriële console beschikbaar is voor Virtual Machine Scale Sets?**

A. Toegang tot de seriële console voor schaalsetinstanties virtuele machine wordt niet ondersteund op dit moment.

## <a name="next-steps"></a>Volgende stappen
* Voor een uitgebreide handleiding voor het CMD- en PowerShell-opdrachten kunt u in de Windows-SAC, klikt u op [hier](serial-console-cmd-ps-commands.md).
* Seriële Console van het is ook beschikbaar voor [Linux](serial-console-linux.md) VM's.
* Meer informatie over [diagnostische gegevens over opstarten](boot-diagnostics.md).