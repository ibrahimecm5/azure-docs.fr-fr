---
title: Télécharger une liste d'utilisateurs dans le portail Azure Active Directory | Microsoft Docs
description: Téléchargez des enregistrements d’utilisateur en bloc dans le centre d’administration Azure dans Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: KarenH444
ms.date: 10/26/2021
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cfeef7e89d94b2f00d1ca1795bb9b4874858411
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132402083"
---
# <a name="download-a-list-of-users-in-azure-active-directory-portal"></a>Télécharger une liste d’utilisateurs dans le portail Azure Active Directory

Azure Active Directory (Azure AD) prend en charge les opérations de téléchargement de listes d’utilisateurs en bloc.

## <a name="required-permissions"></a>Autorisations requises

Les utilisateurs administrateurs et non administrateurs peuvent télécharger des listes d’utilisateurs.

## <a name="to-download-a-list-of-users"></a>Pour télécharger une liste d’utilisateurs

1. [Connectez-vous à votre organisation Azure AD](https://aad.portal.azure.com).
2. Accédez à **Azure Active Directory** > **Utilisateurs**.
3. Dans Azure AD, sélectionnez **Utilisateurs** > **Télécharger les utilisateurs**. Par défaut, tous les profils utilisateurs sont exportés.
4. Dans la page **Télécharger les utilisateurs**, sélectionnez **Démarrer** pour recevoir un fichier CSV répertoriant les propriétés de profil utilisateur. Si des erreurs se produisent, vous pouvez télécharger et consulter le fichier de résultats dans la page **Résultats de l’opération en bloc**. Le fichier contient la raison de chaque erreur.

   ![Sélectionnez l’emplacement où vous souhaitez télécharger la liste des utilisateurs](./media/users-bulk-download/bulk-download.png)
   
>[!NOTE]
>Le fichier de téléchargement contiendra la liste filtrée des utilisateurs en fonction de l’étendue des filtres appliqués.

   Les attributs utilisateur suivants sont inclus :

   - `userPrincipalName`
   - `displayName`
   - `surname`
   - `mail`
   - `givenName`
   - `objectId`
   - `userType`
   - `jobTitle`
   - `department`
   - `accountEnabled`
   - `usageLocation`
   - `streetAddress`
   - `state`
   - `country`
   - `physicalDeliveryOfficeName`
   - `city`
   - `postalCode`
   - `telephoneNumber`
   - `mobile`
   - `authenticationAlternativePhoneNumber`
   - `authenticationEmail`
   - `alternateEmailAddress`
   - `ageGroup`
   - `consentProvidedForMinor`
   - `legalAgeGroupClassification`

## <a name="check-status"></a>Vérification du statut

Pour connaître l'état de vos demandes d'opérations en bloc en attente, consultez la page **Résultats des opérations en bloc**.

[![Vérifier l'état sur la page Résultats des opérations en bloc.](./media/users-bulk-download/bulk-center.png)](./media/users-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Limites du service de téléchargement en bloc

Chaque opération en bloc d’exportation de liste d’utilisateurs peut prendre jusqu’à une heure. Ce rythme permet d’exporter et de télécharger une liste de jusqu’à 500 000 utilisateurs.

## <a name="next-steps"></a>Étapes suivantes

- [Ajouter des utilisateurs en bloc](users-bulk-add.md)
- [Supprimer des utilisateurs en bloc](users-bulk-delete.md)
- [Restaurer des utilisateurs en bloc](users-bulk-restore.md)
