---
title: Procédure d’activation d’attribution d’applications en libre-service
titleSuffix: Azure AD
description: Activer l’accès aux applications en libre-service pour permettre aux utilisateurs de rechercher leurs propres applications à partir de leur portail Mes applications.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 09/23/2021
ms.author: davidmu
ms.collection: M365-identity-device-management
ms.reviewer: phsignor
ms.openlocfilehash: 2a076fa39266a0295d2b1fda6c60f9e6a528f143
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054635"
---
# <a name="enable-self-service-application-assignment-in-azure-active-directory"></a>Activer l’attribution d’applications en libre-service dans Azure Active Directory

Dans cet article, vous allez apprendre à activer l’accès aux applications en libre-service à l’aide du centre d’administration Azure Active Directory.

Pour permettre à vos utilisateurs de découvrir eux-mêmes des applications depuis leur page [Mes applications](my-apps-deployment-plan.md), vous devez activer l’**accès aux applications en libre-service** pour les applications. Cette fonctionnalité est disponible pour les applications qui ont été ajoutées à partir d’Azure AD Gallery, du [Proxy d’application Azure AD](../app-proxy/application-proxy.md) ou par l’intermédiaire du [consentement utilisateur ou administrateur](../develop/application-consent-experience.md).

À l’aide de cette fonctionnalité, vous pouvez :

- Autorisez les utilisateurs à découvrir eux-mêmes des applications à partir du portail Mes applications sans déranger le groupe informatique.

- Ajouter ces utilisateurs à un groupe préconfiguré pour vous permettre de voir qui a demandé l’accès, de retirer l’accès et de gérer les rôles qui leur ont été attribués.

- Éventuellement, autoriser un approbateur d’entreprise à approuver les demandes d’accès aux applications à la place du groupe informatique.

- Éventuellement, configurer jusqu'à 10 personnes qui peuvent autoriser l’accès à cette application.

- Éventuellement, autoriser un approbateur d’entreprise à définir des mots de passe dont ces utilisateurs peuvent se servir pour se connecter à l’application, directement depuis le portail Mes applications sous l’approbateur d’entreprise

- Éventuellement, attribuer automatiquement directement un rôle d’application à des utilisateurs affectés en libre-service.

## <a name="prerequisites"></a>Prérequis

Pour activer l’accès aux applications en libre-service, vous devez effectuer les opérations suivantes :

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un des rôles suivants : Administrateur général, Administrateur d’application cloud, Administrateur d’application ou propriétaire du principal de service.
- Une licence Azure Active Directory Premium (P1 ou P2) est requise pour que les utilisateurs puissent demander à rejoindre une application libre-service et pour permettre aux propriétaires d’approuver ou de refuser les demandes. Sans licence Azure Active Directory Premium, les utilisateurs ne peuvent pas ajouter d’applications libre-service.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Activer l’accès aux applications en libre-service pour permettre aux utilisateurs de rechercher leurs propres applications

L’accès aux applications en libre-service est un excellent moyen pour permettre aux utilisateurs de découvrir eux-mêmes des applications et éventuellement de permettre au groupe d’entreprise d’approuver l’accès à ces applications. Pour les applications nécessitant une authentification unique par mot de passe, vous pouvez également autoriser le groupe d’entreprise à gérer les informations d’identification affectées à ces utilisateurs depuis leur propre portail Mes applications.

Pour activer l’accès en libre-service à une application, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur général.

1. Sélectionnez **Azure Active Directory**. Dans le menu de navigation gauche, sélectionnez **Applications d’entreprise**.

1. Sélectionnez l’application dans la liste. Si vous ne voyez pas l’application, commencez à taper son nom dans la zone de recherche. Vous pouvez également utiliser des contrôles de filtre pour sélectionner le type, l’état ou la visibilité de l’application, puis sélectionner **Appliquer**.

1. Dans le menu de navigation gauche, sélectionnez **Libre-service**.

1. Pour activer l’accès en libre-service à cette application, définissez l’option **Autoriser les utilisateurs à demander l’accès à cette application ?** sur **Oui**.

1. En regard de **À quel groupe les utilisateurs attribués doivent-ils être ajoutés ?** , cliquez sur **Sélectionner un groupe**. Choisissez un groupe, puis sélectionnez **Sélectionner**. Quand la demande d’un utilisateur est approuvée, il est ajouté à ce groupe. Lorsque vous affichez l’appartenance à ce groupe, vous pouvez voir qui a obtenu l’accès à l’application par le biais de l’accès en libre-service.
  
    > [!NOTE]
    > Ce paramètre ne prend pas en charge les groupes synchronisés localement.

1. **Facultatif :** pour exiger une approbation d’entreprise avant d’accorder l’accès aux utilisateurs, définissez l’option **Demander une approbation avant d’accorder l’accès à cette application ?** sur **Oui**.

1. **Facultatif :** concernant les applications utilisant uniquement l’authentification unique par mot de passe, pour autoriser les approbateurs d’entreprise à spécifier les mots de passe envoyés à cette application pour les utilisateurs approuvés, définissez l’option **Autoriser les approbateurs à définir les mots de passe de l’utilisateur pour cette application ?** sur **Oui**.

1. **Facultatif :** Pour spécifier les approbateurs d’entreprise autorisés à approuver l’accès à cette application, choisissez **Sélectionner des approbateurs**, sélectionnez jusqu’à 10 approbateurs d’entreprise, puis choisissez **Sélectionner**.

    >[!NOTE]
    >Les groupes ne sont pas pris en charge. Vous pouvez sélectionner jusqu’à 10 approbateurs d’entreprise individuels. Si vous spécifiez plusieurs approbateurs, chaque approbateur peut approuver une demande d’accès.

1. **Facultatif :** **Pour les applications qui exposent des rôles**, pour attribuer un rôle à des utilisateurs approuvés en libre-service, choisissez **Sélectionner un rôle**, sélectionnez le rôle auquel ces utilisateurs doivent être affectés, puis choisissez **Sélectionner**.

1. Sélectionnez le bouton **Enregistrer** en haut du volet pour terminer.

Lorsque vous avez terminé la configuration d’applications en libre-service, les utilisateurs peuvent accéder au portail Mes applications et sélectionner le bouton **Ajouter des applications en libre-service** pour trouver les applications pour lesquelles vous avez activé l’accès en libre-service. Les approbateurs d’entreprise reçoivent également une notification dans leur portail Mes applications. Vous pouvez leur envoyer une notification par e-mail les avertissant qu’un utilisateur a demandé l’accès à une application nécessitant leur approbation.

## <a name="next-steps"></a>Étapes suivantes

[Configuration d’Azure Active Directory pour la gestion de groupe en libre-service](../enterprise-users/groups-self-service-management.md)
