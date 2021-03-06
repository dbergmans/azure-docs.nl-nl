---
title: Aanbiedingen in Azure Stack delegeren | Microsoft Docs
description: Informatie over het plaatsen van andere personen die verantwoordelijk is voor het maken van aanbiedingen en ondertekening van gebruikers voor u.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2018
ms.author: sethm
ms.reviewer: alfredop
ms.openlocfilehash: 77819c5592fe8b61ed4e3fcb5f874fc0bf5ca602
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077981"
---
# <a name="delegate-offers-in-azure-stack"></a>Aanbiedingen in Azure Stack delegeren

*Is van toepassing op: geïntegreerde Azure Stack-systemen en Azure Stack Development Kit*

Als de Azure Stack-operators wilt u meestal om andere personen die verantwoordelijk is voor gebruikers aanmelden en het maken van abonnementen. Bijvoorbeeld, als u een serviceprovider bent, kunt u wederverkopers aan klanten aanmelden en deze beheren namens. Of, als u deel uitmaakt van een centrale IT-groep in een onderneming bent, kunt u om te delegeren aanmelding door een gebruiker tot andere IT-personeel.

Delegering wordt het gemakkelijker te bereiken en te beheren van meer gebruikers die u door uzelf doen kunt, zoals wordt weergegeven in de volgende afbeelding. 

![Niveaus van overdracht](media/azure-stack-delegated-provider/image1.png)

Met delegering de gedelegeerde provider beheert een aanbieding (gedelegeerde aanbieding) en eindgebruikers merken verkrijgen abonnementen voor deze aanbieding zonder tussenkomst van de systeembeheerder. 

## <a name="understand-delegation-roles-and-steps"></a>Functies van de delegatie en stappen begrijpen

### <a name="delegation-roles"></a>De delegatie-rollen

De volgende rollen zijn onderdeel van de delegatie van:

* De *Azure Stack-operators* beheert de Azure Stack-infrastructuur en maakt u een sjabloon van de aanbieding. De operator voor netwerkapparaten delegeert anderen voor aanbiedingen voor hun tenant.

* De gedelegeerde Azure Stack-operators zijn gebruikers met *eigenaar* of *Inzender* rechten in de abonnementen met de naam *gedelegeerde providers*. Ze kunnen deel uitmaken van andere organisaties, zoals andere tenants Azure Active Directory (Azure AD).

* *Gebruikers* zich registreren voor de aanbiedingen en ze gebruiken voor het beheren van hun workloads, het maken van virtuele machines, opslag van gegevens, enzovoort.

### <a name="delegation-steps"></a>Stappen voor overdracht

Er zijn twee eenvoudige stappen voor het instellen van overdracht:

1. *Maken van een gedelegeerde-Providerabonnement* door u te abonneren op een aanbieding met alleen de service van de abonnementen van een gebruiker. Gebruikers die zijn geabonneerd op deze aanbieding kunnen vervolgens de gedelegeerde aanbiedingen uitbreiden naar andere gebruikers door deze te ondertekenen voor aanbiedingen.

2. *Aanbieding delegeren aan de gedelegeerde provider*. Deze aanbieding kunnen de gedelegeerde provider om abonnementen te maken of uitbreiden van de aanbieding voor hun gebruikers. De gedelegeerde provider kan nu nemen van de aanbieding en aanbieden aan andere gebruikers.

De volgende afbeelding ziet u de stappen voor het instellen van overdracht.

![De gedelegeerde provider maken en ze te registreren van gebruikers](media/azure-stack-delegated-provider/image2.png)

**Vereisten voor gedelegeerde providers**

Om te fungeren als een gedelegeerde provider, moet een gebruiker een relatie tot stand brengen met de belangrijkste provider door een abonnement te maken. Dit abonnement geeft de gedelegeerde provider als bestaande uit het recht om weer te geven van de gedelegeerde aanbiedingen namens de belangrijkste provider.

Nadat u deze relatie tot stand is gebracht, kunt de Azure Stack-operator een aanbieding delegeren aan de gedelegeerde-provider. De gedelegeerde provider kan duren voordat de aanbieding, wijzig de naam (maar niet de inhoud te wijzigen), en deze te bieden aan klanten.

## <a name="delegation-walkthrough"></a>Overzicht van delegeren

De volgende secties bevatten een praktische scenario voor het instellen van een gedelegeerde provider en verifiëren van gebruikers kunnen zich aanmelden voor de gedelegeerde aanbieding een aanbieding wordt gedelegeerd.

### <a name="set-up-roles"></a>Rollen instellen

Als u wilt gebruiken in dit scenario, moet u twee Azure AD-accounts naast uw Azure Stack-operator-account. Als u deze twee accounts hebt, moet u om ze te maken. De accounts kunnen deel uitmaken van een Azure AD-gebruiker en worden aangeduid als de gedelegeerd provider en de gebruiker.

| **Rol** | **Organisatie-rechten** |
| --- | --- |
| Gedelegeerde provider |Gebruiker |
| Gebruiker |Gebruiker |

### <a name="identify-the-delegated-provider"></a>De gedelegeerde provider identificeren

1. Meld u aan de administrator-portal als Azure Stack-operators.

1. Maken van een aanbieding waarmee een gebruiker om te worden van een gedelegeerde provider:

   a.  [Maak een plan](azure-stack-create-plan.md).
       Dit plan moet alleen de service abonnementen bevatten. In dit artikel wordt een abonnement met de naam **PlanForDelegation** als voorbeeld.

   b.  [Maak een aanbieding](azure-stack-create-offer.md) op basis van dit plan. In dit artikel wordt gebruikgemaakt van een aanbieding met de naam **OfferToDP** als voorbeeld.

   c.  De gedelegeerde provider toevoegen als een abonnee op deze aanbieding door te selecteren **abonnementen** > **toevoegen** > **nieuwe Tenantabonnement**.

   ![De gedelegeerde provider toevoegen als een abonnee](media/azure-stack-delegated-provider/image3.png)

   > [!NOTE]
   > Als met alle Azure Stack-aanbiedingen, hebt u de optie van het maken van de aanbieding voor openbare en zodat gebruikers zich aanmelden voor, of het privé houden en laat het beheren van de aanmelding bij Azure Stack-operators. Gedelegeerde providers zijn meestal een kleine groep. U wilt om te bepalen wie is toegelaten, zodat het privé houden van deze aanbieding zinvol in de meeste gevallen.

### <a name="azure-stack-operator-creates-the-delegated-offer"></a>Azure Stack-operators maakt de gedelegeerde aanbieding

De volgende stap is het maken van het plan en aanbieding die u wilt overdragen, en dat uw gebruikers wordt gebruikt. Er is een goed idee om te definiëren van deze aanbieding, precies zoals u wilt dat gebruikers zien omdat de gedelegeerde provider de abonnementen en quota's die het bevat niet wijzigen.

1. Als Azure Stack-operators, [maken van een plan](azure-stack-create-plan.md) en [een aanbieding](azure-stack-create-offer.md) op basis van het abonnement. In dit artikel wordt gebruikgemaakt van een aanbieding met de naam **DelegatedOffer** als voorbeeld.

   > [!NOTE]
   > Deze aanbieding hoeft te zijn openbaar, maar u kunt u deze openbaar maken als u wilt. In de meeste gevallen wilt u echter alleen gedelegeerde providers toegang hebben tot de aanbieding. Nadat u een persoonlijke aanbieding delegeren zoals beschreven in de volgende stappen, heeft de provider van gedelegeerde toegang toe.

1. De aanbieding delegeren. Ga naar **DelegatedOffer**. Onder **instellingen**, selecteer **gedelegeerde Providers** > **toevoegen**.

1. Selecteer het abonnement voor de gedelegeerde provider uit de vervolgkeuzelijst en selecteer vervolgens **gemachtigde**.

   ![Een gedelegeerde provider toevoegen](media/azure-stack-delegated-provider/image4.png)

### <a name="delegated-provider-customizes-the-offer"></a>Gedelegeerde provider past de aanbieding

Meld u aan bij de gebruikersportal aanmeldt als gedelegeerde-provider en maak vervolgens een nieuwe aanbieding met behulp van de gedelegeerde aanbieding als sjabloon.

1. Selecteer **+ een resource maken** > **Tenant aanbiedingen + plannen** > **bieden**.

    ![Een nieuwe aanbieding maken](media/azure-stack-delegated-provider/image5.png)

1. Een naam toewijzen aan de aanbieding. In dit artikel wordt gebruikgemaakt van **ResellerOffer** als voorbeeld. Selecteer de gedelegeerde aanbieding waarop u wilt baseren, en selecteer vervolgens **maken**.

   ![Een naam toewijzen](media/azure-stack-delegated-provider/image6.png)

   >[!IMPORTANT]
   >Het is belangrijk om te begrijpen dat gedelegeerde providers aanbiedingen die worden overgedragen aan ze alleen kunnen kiezen. Ze aanbrengen geen wijzigingen in aanbiedingen. Alleen een Azure Stack-operator kunt wijzigen deze aanbiedingen, bijvoorbeeld wijzigen van hun abonnementen en quota's. Een gedelegeerde provider samenstellen niet van een aanbieding van basisplannen en aanvullende plannen. 

3. De gedelegeerde provider kunt maken deze aanbiedingen via hun eigen portal openbare URL. Als u de aanbieding voor openbare, schakelt u **Bladeren**, en vervolgens **biedt**. Selecteer de aanbieding en selecteer vervolgens **status wijzigen**.

4. De openbare gedelegeerde aanbiedingen zijn nu zichtbaar zijn alleen door de gedelegeerde-portal. Om te zoeken en wijzig deze URL:

    a.  Selecteer **Bladeren** > **alle services**, en klik vervolgens onder de **algemene** categorie, selecteer **abonnementen**. De gedelegeerde-Providerabonnement selecteren. Bijvoorbeeld, **DPSubscription** > **eigenschappen**.

    b.  Kopiëren van de portal URL naar een afzonderlijke locatie, zoals Kladblok.

    ![De gedelegeerde-Providerabonnement selecteren](media/azure-stack-delegated-provider/dpportaluri.png)  

   U klaar bent met het maken van een gedelegeerde aanbieding als een gedelegeerde-provider. Meld u af als gedelegeerde-provider op en sluit het browservenster dat u gebruikt.

### <a name="sign-up-for-the-offer"></a>Aanmelden voor de aanbieding

1. In een nieuw browservenster geopend, gaat u naar de gedelegeerde portal-URL die u in de vorige stap hebt opgeslagen. Meld u aan de portal aan als een gebruiker.

   >[!NOTE]
   >De gedelegeerde aanbiedingen zijn niet zichtbaar, tenzij u de gedelegeerde-portal.

1. Selecteer in het dashboard, **Neem een abonnement**. U ziet dat alleen de gedelegeerde aanbiedingen die zijn gemaakt door de gedelegeerde provider worden weergegeven voor de gebruiker.

   ![Weergeven en selecteren van aanbiedingen](media/azure-stack-delegated-provider/image8.png)

Het proces van het overdragen van een aanbieding is voltooid. Nu kan een gebruiker zich registreren voor deze aanbieding met het ophalen van een abonnement voor deze.

## <a name="move-subscriptions-between-delegated-providers"></a>Abonnementen verplaatsen tussen gedelegeerde providers

Indien nodig, kan een abonnement worden verplaatst tussen de nieuwe of bestaande gedelegeerde providerabonnementen die deel uitmaken van dezelfde Directory-tenant. Dit is met behulp van de PowerShell-cmdlet [verplaatsen AzsSubscription](https://docs.microsoft.com/powershell/module/azs.subscriptions.admin).

Dit is handig wanneer:
- U onboarding een nieuw teamlid die gaat ondernemen op de rol van gedelegeerde provider en u wilt toewijzen aan dit team lid gebruiker-abonnementen die eerder zijn gemaakt in het abonnement op standaard-Provider.
- U hebt meerdere abonnementen voor gedelegeerde providers in dezelfde Directory-tenant (Azure Active Directory) en moet gebruikersabonnementen verplaatsen tussen beide. Dit kan bijvoorbeeld het geval waarbij een teamlid worden verplaatst tussen teams en hun abonnement moet worden toegewezen aan het nieuwe team.


## <a name="next-steps"></a>Volgende stappen

[Een virtuele machine inrichten](azure-stack-provision-vm.md)