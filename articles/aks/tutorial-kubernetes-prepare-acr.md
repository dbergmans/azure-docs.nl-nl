---
title: 'Zelfstudie: Kubernetes in Azure: een containerregister maken'
description: In deze zelfstudie over AKS (Azure Kubernetes Service) gaat u een exemplaar van Azure Container Registry maken en een containerinstallatiekopie van een voorbeeldtoepassing uploaden.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 08/14/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 4f240d346457717c66a6ed189cfd8610c7a764da
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/15/2018
ms.locfileid: "41917565"
---
# <a name="tutorial-deploy-and-use-azure-container-registry"></a>Zelfstudie: Azure Container Registry implementeren en gebruiken

Azure Container Registry (ACR) is een op Azure gebaseerd, particulier register voor Docker-containerinstallatiekopieën. Een particulier containerregister stelt u in staat om op een veilige manier toepassingen en aangepaste code te bouwen en implementeren. In deze zelfstudie, deel twee van zeven, implementeert u een ACR-exemplaar en pusht u vervolgens een containerinstallatiekopie naar het exemplaar. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Een ACR-exemplaar (Azure Container Registry) maken
> * Een containerinstallatiekopie voor ACR taggen
> * De installatiekopie uploaden naar ACR
> * Installatiekopieën weergeven in het register

In volgende zelfstudies wordt dit ACR-exemplaar geïntegreerd met een Kubernetes-cluster in AKS en wordt er met behulp van de installatiekopie een toepassing geïmplementeerd.

## <a name="before-you-begin"></a>Voordat u begint

In de [vorige zelfstudie][aks-tutorial-prepare-app] hebt u een containerinstallatiekopie voor een eenvoudige Azure Voting-toepassing gemaakt. Als u niet de installatiekopie voor de Azure Voting-toepassing hebt gemaakt, ga dan terug naar [Zelfstudie 1: Containerinstallatiekopieën maken][aks-tutorial-prepare-app].

Voor deze zelfstudie moet u Azure CLI versie 2.0.44 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][azure-cli-install].

## <a name="create-an-azure-container-registry"></a>Een Azure Container Registry maken

Om een Azure-containerregister te maken, hebt u eerst een resourcegroep nodig. Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

Een resourcegroep maken met de opdracht [az group create][az-group-create]. In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt in de regio *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Maak een Azure Container Registry-exemplaar met de opdracht [az acr create][az-acr-create] en geef de naam van uw register op. De registernaam moet uniek zijn binnen Azure en mag 5 tot 50 alfanumerieke tekens bevatten. In de rest van deze zelfstudie wordt `<acrName>` gebruikt als een tijdelijke aanduiding voor de naam van het containerregister. De SKU *Basic* is een toegangspunt voor ontwikkelingsdoeleinden dat is geoptimaliseerd voor kosten, met een balans tussen opslag en doorvoer.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

## <a name="log-in-to-the-container-registry"></a>Aanmelden bij het containerregister

Als u het ACR-exemplaar wilt gebruiken, moet u zich eerst aanmelden. Gebruik de opdracht [az acr login][az-acr-login] en voer de unieke naam in die u in de vorige stap aan het containerregister hebt gegeven.

```azurecli
az acr login --name <acrName>
```

De opdracht retourneert het bericht *Aanmelden geslaagd* wanneer deze is uitgevoerd.

## <a name="tag-a-container-image"></a>Een containerinstallatiekopie taggen

Als u een lijst met de huidige installatiekopieën wilt weergeven, gebruikt u de opdracht [docker images][docker-images]:

```
$ docker images

REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front             latest              4675398c9172        13 minutes ago      694MB
redis                        latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        9 months ago        694MB
```

Als u de containerinstallatiekopie *azure-vote-front* wilt gebruiken met ACR, moet de installatiekopie worden getagd met het adres van de aanmeldingsserver van uw register. Deze tag wordt gebruikt voor routering bij het pushen van containerinstallatiekopieën naar een installatiekopieregister.

U kunt het adres van de aanmeldingsserver opvragen door met de opdracht [az acr list][az-acr-list] als volgt een query uit te voeren op de *loginServer*:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Tag nu de lokale installatiekopie *azure-vote-front* met het adres *acrloginServer* van het containerregister. U kunt de versie van de installatiekopie aangeven door *:v1* toe te voegen aan het eind van de naam van de installatiekopie:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Controleer of de tags zijn toegepast door [docker images][docker-images] opnieuw uit te voeren. Een installatiekopie wordt getagd met het adres van het ACR-exemplaar en een versienummer.

```
$ docker images

REPOSITORY                                           TAG           IMAGE ID            CREATED             SIZE
azure-vote-front                                     latest        eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry.azurecr.io/azure-vote-front      v1            eaf2b9c57e5e        8 minutes ago       716 MB
redis                                                latest        a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask                           flask         788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-registry"></a>Installatiekopieën naar het register pushen

U kunt de installatiekopie *azure-vote-front* nu pushen naar het ACR-exemplaar. Gebruik [docker push][docker-push] en geef als volgt uw eigen *acrLoginServer*-adres op voor de naam van de installatiekopie:

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

Het kan enkele minuten duren voordat de installatiekopie naar ACR is gepusht.

## <a name="list-images-in-registry"></a>Installatiekopieën vermelden in het register

Gebruik de opdracht [az acr repository list][az-acr-repository-list] om een lijst weer te geven met installatiekopieën die naar het ACR-exemplaar zijn gepusht. Geef als volgt uw eigen `<acrName>` op:

```azurecli
az acr repository list --name <acrName> --output table
```

In het volgende voorbeeld van uitvoer ziet u dat de installatiekopie *azure-vote-front* beschikbaar is in het register:

```
Result
----------------
azure-vote-front
```

Gebruik de opdracht [az acr repository show-tags][az-acr-repository-show-tags] als volgt om de tags voor een specifieke installatiekopie te zien:

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

In het volgende voorbeeld van uitvoer ziet u de tag *v1* zoals toegevoegd aan de installatiekopie in een eerdere stap:

```
Result
--------
v1
```

U beschikt nu over een containerinstallatiekopie die is opgeslagen in een particulier Azure Container Registry-exemplaar. Deze installatiekopie wordt in de volgende zelfstudie vanuit ACR geïmplementeerd naar een Kubernetes-cluster.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een Azure Container Registry gemaakt en een installatiekopie gepusht voor gebruik in een AKS-cluster. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een ACR-exemplaar (Azure Container Registry) maken
> * Een containerinstallatiekopie voor ACR taggen
> * De installatiekopie uploaden naar ACR
> * Installatiekopieën weergeven in het register

Ga naar de volgende zelfstudie voor informatie over het implementeren van een Kubernetes-cluster in Azure.

> [!div class="nextstepaction"]
> [Kubernetes-cluster implementeren][aks-tutorial-deploy-cluster]

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#create
[az-acr-list]: /cli/azure/acr#list
[az-acr-login]: https://docs.microsoft.com/cli/azure/acr#az-acr-login
[az-acr-list]: https://docs.microsoft.com/cli/azure/acr#az-acr-list
[az-acr-repository-list]: /cli/azure/acr/repository#list
[az-acr-repository-show-tags]: /cli/azure/acr/repository#show-tags
[az-group-create]: /cli/azure/group#az-group-create
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-tutorial-deploy-cluster]: ./tutorial-kubernetes-deploy-cluster.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
