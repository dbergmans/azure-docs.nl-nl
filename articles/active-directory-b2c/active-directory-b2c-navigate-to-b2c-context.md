---
title: Overschakelen naar een B2C-tenant in Azure Active Directory B2C | Microsoft Docs
description: Klik hier voor meer informatie over het overschakelen naar de context van uw Active Directory B2C-tenant.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 4/13/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 9b8ff03ff90a0962a6a890cf7cc99e7134559b7f
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442953"
---
# <a name="switching-to-your-azure-ad-b2c-tenant"></a>Overschakelen naar uw Azure AD B2C-tenant

U moet zich in de context van uw Azure AD B2C-tenant bevinden om Azure AD B2C te configureren.

## <a name="log-into-azure-ad-b2c-tenant"></a>Aanmelden bij de Azure AD B2C-tenant

U moet bij Azure Portal zijn aangemeld als een globale beheerder van de Azure AD B2C-tenant om naar uw Azure AD B2C-tenant te gaan.

1. Meld u aan bij de [Azure Portal](http://portal.azure.com).
1. Schakel tenants over door te klikken op uw e-mailadres of afbeelding in de rechterbovenhoek.
1. In de `Directory`-lijst die wordt weergegeven selecteert u de Azure AD B2C-tenant die u wilt beheren.

Azure Portal wordt vernieuwd.  U bent nu aangemeld bij Azure Portal in de context van uw Azure AD B2C-tenant.

## <a name="navigate-to-the-b2c-features-pane"></a>Ga naar het deelvenster B2C-functies

1. Klik op **Bladeren** in de navigatiebalk aan de linkerkant.
1. Klik op **> Alle services** en zoek vervolgens naar `Azure AD B2C` in het linkernavigatievenster.  (Als u het aan het Startboard links wilt vastmaken, klikt u op de ster aan de linkerkant van Azure AD B2C)
1. Klik op **Azure AD B2C** voor toegang tot het deelvenster B2C-functies.
   
    ![Schermafbeelding van bladeren naar het deelvenster B2C-functies](./media/active-directory-b2c-get-started/b2c-browse.png)

> [!IMPORTANT]
> U moet een algemene beheerder van de B2C-tenant zijn om het deelvenster B2C-functies te kunnen openen. Een globale beheerder van andere tenant of een gebruiker van een tenant heeft hiertoe geen toegang.  U kunt overschakelen naar uw B2C-tenant met behulp van de tenantwisselaar in de rechterbovenhoek van Azure Portal.
