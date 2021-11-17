---
title: Guide pratique pour retirer l’accès d’un utilisateur à une application
description: Découvrir comment retirer l’accès d’un utilisateur à une application dans Azure Active Directory
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/02/2020
ms.author: davidmu
ms.reviewer: phsignor
ms.openlocfilehash: b0b3e1074df0a1067736900dbce7d7521414c1bc
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132549752"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Guide pratique pour retirer l’accès d’un utilisateur à une application

Cet article vous explique comment supprimer l’accès d’un utilisateur à une application.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Je souhaite supprimer l’attribution d’une application à un utilisateur ou groupe spécifique

Pour supprimer l’attribution d’une application à un utilisateur ou groupe, suivez les étapes répertoriées dans l’article [Supprimer l’attribution d’une application à un utilisateur ou groupe à partir d’une application d’entreprise dans Azure Active Directory](./assign-user-or-group-access-portal.md).

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Je souhaite désactiver tous les accès à une application pour chaque utilisateur

Pour désactiver toutes les connexions des utilisateurs à une application, suivez les étapes répertoriées dans l’article [Désactiver les connexions des utilisateurs à une application d’entreprise dans Azure Active Directory](./disable-user-sign-in-portal.md).

## <a name="i-want-to-delete-an-application-entirely"></a>Je veux complètement supprimer une passerelle d’application

La [Série de guides de démarrage rapide sur la gestion des applications](delete-application-portal.md) contient des instructions sur la suppression d’une application de votre locataire Azure Active Directory.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Je veux désactiver toutes les futures opérations de consentement de l’utilisateur pour n’importe quelle application

La désactivation du consentement de l’utilisateur pour votre annuaire entier empêche les utilisateurs finaux de donner leur consentement pour n’importe quelle application. Les administrateurs peuvent toujours donner leur consentement au nom de l’utilisateur. Pour plus d’informations sur le consentement de l’application et sur les conditions pour donner ou refuser ce consentement, lisez la rubrique [Comprendre le consentement de l’utilisateur et de l’administrateur](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent). Voir aussi [Autorisations et consentement](../develop/v2-permissions-and-consent.md).

Pour **désactiver toutes les futures opérations de consentement de l’utilisateur dans l’ensemble de votre annuaire**, procédez comme suit :

1. Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général**.

2. Ouvrir le panneau **Extension Azure Active Directory**

3. Cliquez sur **Applications d’entreprise** dans le menu de navigation.

4. Cliquez sur **Paramètres utilisateur**.

5. Définissez l’option **Les utilisateurs peuvent autoriser les applications à accéder à leurs données** sur **Non**, puis cliquez sur le bouton Enregistrer.

## <a name="next-steps"></a>Étapes suivantes

[Gestion de l’accès aux applications](what-is-access-management.md)
