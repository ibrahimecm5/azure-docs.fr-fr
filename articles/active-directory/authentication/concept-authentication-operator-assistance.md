---
title: Assistance opérateur dans Azure Active Directory
description: Découvrir la dépréciation de la fonctionnalité d’assistance opérateur dans Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/21/2021
ms.author: justinha
author: rckyplln
manager: daveba
ms.reviewer: ripull
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c00fb3c68955a888bbc383fe59aef8f7ed75835
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270905"
---
# <a name="how-to-enable-and-disable-operator-assistance"></a>Guide pratique pour activer et désactiver l’assistance opérateur

L’assistance opérateur est une fonctionnalité Azure AD qui permet à un opérateur de transférer manuellement les appels téléphoniques à la place d’un transfert automatique. Lorsque ce paramètre est activé, le numéro de téléphone du bureau est composé et, en réponse, le système demande à l’opérateur de transférer l’appel à un numéro de poste donné.

L’assistance opérateur peut être activée pour un locataire entier ou pour un utilisateur individuel. Si le paramètre a la valeur **Activé**, l’intégralité du locataire est activée pour l’assistance opérateur. Si vous choisissez **Appel téléphonique** comme méthode par défaut et que vous avez un numéro de poste spécifié avec votre numéro de bureau (délimité par **x**), un opérateur peut transférer manuellement l’appel téléphonique.

Par exemple, imaginons qu’un client aux États-Unis a le numéro de bureau 425-555-1234x5678. Lorsque l’assistance opérateur est activée, le système compose le 425-555-1234. Une fois que le client (également appelé opérateur) répond à l’appel, il est invité à transférer l’appel au numéro de poste 5678. Une fois le transfert et la réponse effectués, le système envoie l’invite MFA normale et attend l’approbation.

Si le paramètre est **Désactivé**, le système compose automatiquement les numéros de poste qui font partie du numéro de téléphone. Votre administrateur peut toujours spécifier des utilisateurs individuels qui doivent être activés pour l’assistance opérateur, en préfixant le numéro de poste avec « @ ». Par exemple, 425-555-1234x@5678 indique que l’assistance opérateur doit être utilisée, même si le paramètre est **Désactivé**.

Vous pouvez vérifier l’état de cette fonctionnalité dans votre propre locataire en accédant au [portail Azure AD](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade), puis dans le volet gauche, en cliquant sur **Sécurité** > **MFA** > **Paramètres de l’appel téléphonique**. Vérifiez **Opérateur requis pour transférer les extensions** pour voir si le paramètre est **Activé** ou **Désactivé**. 

![Capture d’écran des paramètres de l’assistance opérateur](./media/concept-authentication-operator-assistance/settings.png)

Vous pouvez améliorer la fiabilité, la sécurité et créer une expérience MFA fluide en utilisant les conseils suivants :

- Vous [enregistrez un numéro de téléphone direct](https://aka.ms/mfasetup) (sans numéro de poste) ou une [autre méthode](concept-authentication-methods.md) à utiliser pour l’authentification multifacteur ou la réinitialisation de mot de passe en libre-service si elle est activée. 
- Vos administrateurs enregistrent un numéro de téléphone direct (sans numéro de poste) pour le compte de l’utilisateur à utiliser pour l’[authentification multifacteur](howto-mfa-userdevicesettings.md#add-authentication-methods-for-a-user) ou la [réinitialisation de mot de passe en libre-service](tutorial-enable-sspr.md) si elle est activée. 
- Le système téléphonique prend en charge la fonctionnalité de standard automatique. 
 
