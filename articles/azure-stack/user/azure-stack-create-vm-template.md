---
title: In deze zelfstudie maakt u een Azure Stack-VM met behulp van een sjabloon | Microsoft Docs
description: Beschrijft hoe u de ASDK gebruiken voor het maken van een virtuele machine met behulp van een sjabloon predfined en een aangepaste sjabloon voor GitHub.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/12/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 0b3ba5c3a091cf673d8b3dbc413d36cb5fb75de5
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/12/2018
ms.locfileid: "44713405"
---
# <a name="tutorial-create-a-vm-using-a-community-template"></a>Zelfstudie: een virtuele machine met behulp van een communitysjabloon maken
Als een Azure Stack-operators of de gebruiker, kunt u een virtuele machine met [aangepaste snelstartsjablonen van GitHub](https://github.com/Azure/AzureStack-QuickStart-Templates) in plaats van het implementeren van een handmatig vanuit de Azure Stack marketplace.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Meer informatie over Azure Stack-quickstart-sjablonen 
> * Een virtuele machine met een aangepaste GitHub-sjabloon maken
> * Start minikube en installeren van een toepassing

## <a name="learn-about-azure-stack-quickstart-templates"></a>Meer informatie over Azure Stack-quickstart-sjablonen
Azure Stack-quickstart-sjablonen worden opgeslagen in de [openbare opslagplaats met Snelstartsjablonen AzureStack](https://github.com/Azure/AzureStack-QuickStart-Templates) op GitHub. Deze opslagplaats bevat sjablonen van Azure Resource Manager-implementatie die zijn getest met de Microsoft Azure Stack Development Kit (ASDK). U kunt ze maken het gemakkelijker voor u om te evalueren van Azure Stack en gebruik de ASDK-omgeving. 

Na verloop van tijd hebben veel GitHub-gebruikers bijgedragen tot de opslagplaats, wat resulteert in een enorme verzameling van meer dan 400 implementatiesjablonen. Deze opslagplaats is een goed startpunt om een beter begrip van hoe u verschillende soorten omgevingen op Azure Stack implementeren kunt. 

>[!IMPORTANT]
> Sommige van deze sjablonen zijn gemaakt door leden van de community en niet door Microsoft. Elke sjabloon is in licentie gegeven onder een licentieovereenkomst van de eigenaar, niet van Microsoft. Microsoft is niet verantwoordelijk voor deze sjablonen en voert geen controle veiligheid, compatibiliteit of prestaties. Communitysjablonen worden niet ondersteund Microsoft ondersteuningsprogramma of-service, en worden beschikbaar gesteld "AS IS" zonder garantie van welke aard dan ook.

Als u wilt om bij te dragen van uw Azure Resource Manager-sjablonen naar GitHub, moet u uw bijdrage aan de [opslagplaats voor azure-quickstart-templates](https://github.com/Azure/AzureStack-QuickStart-Templates).

Zie voor meer informatie over de GitHub-opslagplaats en het bijdragen aan de [Leesmij-bestand](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/README.md). 


## <a name="create-a-vm-using-a-custom-github-template"></a>Een virtuele machine met een aangepaste GitHub-sjabloon maken
In deze zelfstudie voorbeeld de [101-vm-linux-minikube](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-linux-minikube) Azure Stack-quickstart-sjabloon wordt gebruikt voor het implementeren van een Ubuntu 16.04 virtuele machine op Azure Stack Minikube voor het beheren van een Kubernetes-cluster uitgevoerd.

Minikube is een hulpprogramma waarmee u eenvoudig Kubernetes lokaal uitvoeren. Minikube wordt uitgevoerd een Kubernetes-cluster van één knooppunt in een virtuele machine, zodat u kunt uitproberen Kubernetes of ontwikkelen met het dagelijkse. Een eenvoudige, één knooppunt Kubernetes-cluster die worden uitgevoerd op een Linux-VM ondersteunt. Minikube is de snelste en eenvoudigste manier om een volledig werkend Kubernetes-cluster uitgevoerd. Hiermee kunnen ontwikkelaars ontwikkelen en testen van hun op basis van een Kubernetes-toepassingsimplementaties op hun lokale machines. Qua, de VM Minikube hoofd- en knooppunt onderdelen van de Agent wordt lokaal uitgevoerd:

- Knooppunt van de master-onderdelen, zoals API-Server, Scheduler, en [etcd Server](https://coreos.com/etcd/) worden uitgevoerd in een enkel Linux-proces LocalKube genoemd.
- Knooppunt onderdelen van de agent worden uitgevoerd in dockercontainers, precies zoals ze zou worden uitgevoerd op een normale Agent-knooppunt. Vanuit het oogpunt van een implementatie van toepassing is het geen verschil wanneer de toepassing wordt geïmplementeerd op een Minikube of reguliere Kubernetes-cluster.

Deze sjabloon wordt de volgende onderdelen geïnstalleerd:

- Ubuntu 16.04 LTS-VM
- [Docker CE](https://download.docker.com/linux/ubuntu) 
- [Kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.8.0/bin/linux/amd64/kubectl)
- [Minikube](https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64)
- xFCE4
- xRDP

> [!IMPORTANT]
> De Ubuntu-VM-installatiekopie (Ubuntu Server 16.04 LTS in dit voorbeeld) moet al zijn toegevoegd aan de Azure Stack marketplace voordat u begint met deze stappen.

1.  Klik op **+ een resource maken** > **aangepaste** > **sjabloonimplementatie**.

    ![](media/azure-stack-create-vm-template/1.PNG) 

2. Klik op **template bewerken**.

    ![](media/azure-stack-create-vm-template/2.PNG) 

3.  Klik op **Quickstart-sjabloon**.

    ![](media/azure-stack-create-vm-template/3.PNG)

4. Selecteer **101-vm-linux-minikube** van de beschikbare sjablonen met behulp van de **selecteert u een sjabloon** vervolgkeuzelijst lijst en klik vervolgens op **OK**.  

    ![](media/azure-stack-create-vm-template/4.PNG)

5. Als u wijzigingen aanbrengen in de JSON die u kunt doen, als dat niet wilt, of als u klaar bent, klikt u op van de sjabloon **opslaan** te sluiten van het dialoogvenster van de sjabloon bewerken.

    ![](media/azure-stack-create-vm-template/5.PNG) 

6.  Klik op **Parameters**, vult u of de beschikbare velden Wijzig indien nodig, en klik op **OK**. Kies het abonnement gebruiken, maken of kies een bestaande resourcenaam van de groep en klik vervolgens op **maken** aan de sjabloonimplementatie van een initiëren.

    ![](media/azure-stack-create-vm-template/6.PNG)

7. Kies het abonnement gebruiken, maken of kies een bestaande resourcenaam van de groep en klik vervolgens op **maken** aan de sjabloonimplementatie van een initiëren.

    ![](media/azure-stack-create-vm-template/7.PNG)

8. De implementatie van resourcegroep duurt enkele minuten om de aangepaste VM op basis van een sjabloon te maken. U kunt de status van de installatie via meldingen en van de eigenschappen van de resourcegroep kunt bewaken. 

    ![](media/azure-stack-create-vm-template/8.PNG)

    >[!NOTE]
    > De virtuele machine zal worden uitgevoerd wanneer de implementatie is voltooid. 

## <a name="start-minikube-and-install-an-application"></a>Start minikube en installeren van een toepassing
Nu dat de Linux-VM is gemaakt, kunt u zich kunt aanmelden bij minikube start en een toepassing te installeren. 

1. Nadat de implementatie is voltooid, klikt u op **Connect** om het openbare IP-adres dat wordt gebruikt om verbinding maken met de Linux-VM weer te geven. 

    ![](media/azure-stack-create-vm-template/9.PNG)

2. Voer vanuit een opdrachtprompt met verhoogde bevoegdheid **mstsc.exe** verbinding met extern bureaublad openen en verbinding maken met de Linux-VM openbare IP-adres is gedetecteerd in de vorige stap. Wanneer u hierom wordt gevraagd om aan te melden bij xRDP, gebruikt u de referenties die u hebt opgegeven bij het maken van de virtuele machine.

    ![](media/azure-stack-create-vm-template/10.PNG)

3. Open de Terminal Emulator en voert u de volgende opdrachten uit om te beginnen minikube:

    ```shell
    sudo minikube start --vm-driver=none
    sudo minikube addons enable dashboard
    sudo minikube dashboard --url
    ```

    ![](media/azure-stack-create-vm-template/11.PNG)

4. Open de webbrowser en Ga naar het adres van de Kubernetes-dashboard. Gefeliciteerd, u hebt nu een volledig werken Kubernetes-installatie minikube!

    ![](media/azure-stack-create-vm-template/12.PNG)

5. Als u wilt een voorbeeldtoepassing implementeren, gaat u naar de pagina officiële documentatie van kubernetes, de sectie 'Minikube Cluster maken' overslaan als u al hebt gemaakt een hierboven. Gewoon gaat u naar de sectie 'Uw Node.js-toepassing maken' op https://kubernetes.io/docs/tutorials/stateless-application/hello-minikube/.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Meer informatie over Azure Stack-quickstart-sjablonen 
> * Een virtuele machine met een aangepaste GitHub-sjabloon maken
> * Start minikube en installeren van een toepassing

