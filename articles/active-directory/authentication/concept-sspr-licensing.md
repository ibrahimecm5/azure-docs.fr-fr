---
title: Licence de réinitialisation de mot de passe libre-service - Azure Active Directory
description: Découvrir les différents besoins de licence pour la réinitialisation de mot de passe en libre-service Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb6f125310e50bbbc48071a06fcaf1aad0650c46
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114458220"
---
# <a name="licensing-requirements-for-azure-active-directory-self-service-password-reset"></a>Licences requises pour la réinitialisation de mot de passe en libre-service Azure Active Directory

Si vous souhaitez réduire le nombre d’appels au support technique et la perte de productivité quand un utilisateur ne parvient pas à se connecter à son appareil ou à une application, vous pouvez activer la technologie SSPR (réinitialisation de mot de passe en libre-service) pour les comptes d’utilisateurs Azure AD (Azure Active Directory). Les fonctionnalités SSPR incluent le changement, la réinitialisation, le déverrouillage et la réécriture de mot de passe dans un annuaire local. Les fonctionnalités SSPR de base sont disponibles dans Microsoft 365 Business Standard ou version ultérieure, et toutes les références (SKU) Azure AD Premium sont gratuites.

Cet article détaille les différentes façons d’obtenir et d’utiliser la licence de réinitialisation de mot de passe en libre-service. Pour des détails spécifiques sur les prix et la facturation, consultez la [page de tarification d’Azure AD](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing).

Bien que certains utilisateurs sans licence puissent techniquement accéder à SSPR, une licence est nécessaire pour tout utilisateur qui souhaite bénéficier du service.

> [!NOTE] 
> Certains services de locataire ne peuvent pas limiter les avantages à des utilisateurs spécifiques. Des efforts doivent être entrepris pour limiter les avantages des services aux utilisateurs disposant d’une licence. Cela permet d’éviter une éventuelle interruption de service à votre organisation, une fois que les fonctionnalités de ciblage sont disponibles.

## <a name="compare-editions-and-features"></a>Comparer les éditions et les fonctionnalités

Le tableau suivant présente les différents scénarios SSPR liés au changement, à la réinitialisation ou à la réécriture locale de mot de passe ainsi que les références SKU qui fournissent la fonctionnalité correspondante.

| Fonctionnalité | Azure AD Gratuit | Microsoft 365 Business Standard | Microsoft 365 Business Premium | Azure AD Premium P1 ou P2 |
| --- |:---:|:---:|:---:|:---:|
| **Changement de mot de passe utilisateur cloud uniquement**<br />Quand un utilisateur d’Azure AD connaît son mot de passe et souhaite le remplacer par un nouveau. | ● | ● | ● | ● |
| **Réinitialisation de mot de passe utilisateur cloud uniquement**<br />Quand un utilisateur d’Azure AD a oublié son mot de passe et doit le réinitialiser. | | ● | ● | ● |
| **Changement ou réinitialisation de mot de passe utilisateur hybride avec réécriture locale**<br />Quand un utilisateur d’Azure AD synchronisé à partir d’un annuaire local via Azure AD Connect souhaite changer ou réinitialiser son mot de passe mais également réécrire le nouveau mot de passe localement. | | | ● | ● |

> [!WARNING]
> Les plans de licences autonomes Microsoft 365 De base et Standard ne prennent pas en charge la technologie SSPR avec l’écriture différée locale. La fonctionnalité d’écriture différée locale requiert Azure AD Premium P1, Premium P2 ou Microsoft 365 Business Premium. 

Pour plus d’informations sur les licences, notamment les coûts, consultez les pages suivantes :


* [Guide de gestion des licences Microsoft 365 pour la conformité et la sécurité](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-tenantlevel-services-licensing-guidance/microsoft-365-security-compliance-licensing-guidance)
* [Tarification d’Azure Active Directory](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)
* [Fonctionnalités Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Entreprise](https://www.microsoft.com/microsoft-365/enterprise)
* [Microsoft 365 Business](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="next-steps"></a>Étapes suivantes

Pour bien démarrer avec SSPR, suivez le tutoriel suivant :

> [!div class="nextstepaction"]
> [Tutoriel : Activer SSPR (réinitialisation de mot de passe en libre-service)](tutorial-enable-sspr.md)