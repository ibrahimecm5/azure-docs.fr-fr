---
title: Authentifier les playbooks auprès de Microsoft Sentinel | Microsoft Docs
description: Découvrez comment permettre à vos playbooks d’accéder à Microsoft Sentinel et leur fournir l’autorisation de prendre des mesures correctives.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6cb45659ab0e31ed4d1259fc19c10b6f6bdfa015
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522172"
---
# <a name="authenticate-playbooks-to-microsoft-sentinel"></a>Authentifier des playbooks auprès de Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

De la façon dont Logic Apps fonctionne, il doit se connecter séparément et s’authentifier indépendamment auprès de chaque ressource de chaque type avec lequel il interagit, dont Microsoft Sentinel lui-même. Logic Apps utilise des [connecteurs spécialisés](/connectors/connector-reference/) à cet effet, chaque type de ressource ayant son propre connecteur. Ce document décrit les types de connexion et d’authentification dans le [connecteur Logic Apps Microsoft Sentinel](/connectors/azuresentinel/), que les playbooks peuvent utiliser pour interagir avec Microsoft Sentinel afin d’accéder aux informations contenues dans les tables de votre espace de travail.

Ce document, de même que notre guide d’[utilisation des déclencheurs et des actions dans les playbooks](playbook-triggers-actions.md), vient compléter notre autre documentation sur les playbooks, [Tutoriel : Utiliser des playbooks avec des règles d’automatisation dans Microsoft Sentinel](tutorial-respond-threats-playbook.md).

Pour obtenir une présentation des playbooks, consultez [Automatiser la réponse aux menaces à l’aide de playbooks dans Microsoft Sentinel](automate-responses-with-playbooks.md).

Pour connaître la spécification complète du connecteur Microsoft Sentinel, consultez la [documentation du connecteur Logic Apps](/connectors/azuresentinel/).

## <a name="authentication"></a>Authentification

Le connecteur Microsoft Sentinel dans Logic Apps, ainsi que les déclencheurs et actions de ses composants, peuvent fonctionner pour le compte d’une identité disposant des autorisations nécessaires (lecture et/ou écriture) dans l’espace de travail approprié. Le connecteur prend en charge plusieurs types d’identités :

- [Identité managée (préversion)](#authenticate-with-managed-identity)
- [Utilisateur Azure AD](#authenticate-as-an-azure-ad-user)
- [Principal de service (application Azure AD)](#authenticate-as-a-service-principal-azure-ad-application)

    ![Options d’authentification](media/authenticate-playbooks-to-sentinel/auth-methods.png)

### <a name="permissions-required"></a>Autorisations requises

| Rôles / Composants du connecteur | Déclencheurs | Actions « Get » | Mettre à jour l’incident,<br>ajouter un commentaire |
| ------------- | :-----------: | :------------: | :-----------: |
| **[Lecteur Microsoft Sentinel](../role-based-access-control/built-in-roles.md#microsoft-sentinel-reader)** | &#10003; | &#10003; | &#10007; |
| **Microsoft Sentinel[Répondeur](../role-based-access-control/built-in-roles.md#microsoft-sentinel-responder)/[Contributeur](../role-based-access-control/built-in-roles.md#microsoft-sentinel-contributor)** | &#10003; | &#10003; | &#10003; |
| 

[En savoir plus sur les autorisations dans Microsoft Sentinel.](./roles.md)

### <a name="authenticate-with-managed-identity"></a>Authentifier avec l’identité gérée

Cette méthode d’authentification vous permet de fournir des autorisations directement au playbook (ressource de workflow d’application logique), afin que les actions du connecteur Microsoft Sentinel prises par le playbook fonctionnent au nom du playbook, comme s’il s’agissait d’un objet indépendant avec ses propres autorisations sur Microsoft Sentinel. L’utilisation de cette méthode réduit le nombre d’identités que vous devez gérer. 

> [!NOTE]
> Pour permettre à une identité managée d’accéder à d’autres ressources (telle que votre espace de travail Microsoft Sentinel), votre utilisateur connecté doit avoir un rôle autorisé à écrire des attributions de rôle, telles que Propriétaire ou Administrateur de l’accès utilisateur de l’espace de travail Microsoft Sentinel.

Pour s’authentifier avec une identité managée :

1. [Activez l’identité managée](../logic-apps/create-managed-service-identity.md#enable-system-assigned-identity-in-azure-portal) sur la ressource de workflow Logic Apps. Pour récapituler :

    - Dans le menu de l’application logique, sous **Paramètres**, sélectionnez **Identité**. Sélectionnez **Attribuée par le système > Activée > Enregistrer**. Quand Azure vous invite à confirmer l’opération, sélectionnez **Oui**.

    - Votre application logique peut désormais utiliser l’identité affectée par le système qui est inscrite auprès d’Azure AD et représentée par un ID d’objet.

1. [Accordez à cette identité l’accès](../logic-apps/create-managed-service-identity.md#give-identity-access-to-resources) à l’espace de travail Microsoft Sentinel :
    1. Dans le menu Microsoft Sentinel, sélectionnez **Paramètres**.
    1. Sélectionnez l’onglet **Paramètres de l’espace de travail**. Dans le menu de l’espace de travail, sélectionnez **Contrôle d’accès (IAM)** .
    1. Dans la barre de boutons en haut, sélectionnez **Ajouter**, puis choisissez **Ajouter une attribution de rôle**. Si l’option **Ajouter une attribution de rôle** est désactivée, vous n’avez pas les autorisations pour attribuer les rôles.
    1. Dans le nouveau volet qui s’affiche, attribuez le rôle approprié :

        | Role | Situation |
        | --- | --- |
        | [**Répondeur Microsoft Sentinel**](../role-based-access-control/built-in-roles.md#microsoft-sentinel-responder) | Le playbook inclut les étapes de mise à jour des incidents ou des watchlists |
        | [**Lecteur Microsoft Sentinel**](../role-based-access-control/built-in-roles.md#microsoft-sentinel-reader) | Le playbook reçoit uniquement des incidents |
        |

        En savoir plus sur les [rôles disponibles dans Microsoft Sentinel](./roles.md).
    1. Sous **Attribuer l’accès à**, choisissez **Application logique**.
    1. Choisissez l’abonnement auquel appartient le playbook, puis sélectionnez le nom du playbook.
    1. Sélectionnez **Enregistrer**.

1. Activez la méthode d’authentification d’identité managée dans le connecteur Logic Apps Microsoft Sentinel :

    1. Dans le Concepteur Logic Apps, ajoutez une étape de connecteur Logic Apps Microsoft Sentinel. Si le connecteur est déjà activé pour une connexion existante, cliquez sur le lien **Modifier la connexion**.

        ![Modifier la connexion](media/authenticate-playbooks-to-sentinel/change-connection.png)

    1. Dans la liste de connexions qui en résulte, sélectionnez **Ajouter nouveau** en bas. 

    1. Créez une connexion en sélectionnant **Se connecter avec une identité managée (préversion)** .

        ![Option d’identité managée](media/authenticate-playbooks-to-sentinel/auth-methods-msi-choice.png)

    1. Entrez un nom pour cette connexion, sélectionnez **Identité managée affectée par le système**, puis **Créer**.

        ![Se connecter avec une identité managée](media/authenticate-playbooks-to-sentinel/auth-methods-msi.png)

### <a name="authenticate-as-an-azure-ad-user"></a>S’authentifier en tant qu’utilisateur Azure AD

Pour établir une connexion, sélectionnez **Se connecter**. Vous serez invité à fournir des informations sur votre compte. Une fois que vous avez effectué cette opération, suivez les instructions restantes à l’écran pour créer une connexion.

### <a name="authenticate-as-a-service-principal-azure-ad-application"></a>S’authentifier en tant que principal de service (application Azure AD)

Les principaux de service peuvent être créés en inscrivant une application Azure AD. Il est **préférable** d’utiliser une application inscrite comme identité du connecteur, au lieu d’utiliser un compte d’utilisateur, car vous serez mieux en mesure de contrôler les autorisations, de gérer les informations d’identification et d’activer certaines limitations sur l’utilisation du connecteur.

Pour utiliser votre propre application avec le connecteur Microsoft Sentinel, procédez comme suit :

1. Inscrivez l’application auprès d’Azure AD et créez un principal de service. [Découvrez comment](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal).

1. Obtenez les informations d’identification (pour une authentification ultérieure).

    Dans le panneau de l’application inscrite, récupérez les informations d’identification de l’application pour la connexion :

    - **ID client** : sous **Vue d’ensemble**
    - **Secret client** : sous **Certificats et secrets**.

1. Accordez des autorisations à l’espace de travail Microsoft Sentinel.

    Dans cette étape, l’application obtient l’autorisation d’utiliser l’espace de travail Microsoft Sentinel.

    1. Dans l’espace de travail Microsoft Sentinel, accédez à **Paramètres** -> **Paramètres d’espace de travail** -> **Contrôle d’accès (IAM)**

    1. Sélectionnez **Ajouter une attribution de rôle**.

    1. Sélectionnez le rôle que vous souhaitez affecter à l’application. Par exemple, pour permettre à l’application d’effectuer des actions qui apportent des modifications à l’espace de travail Sentinel, comme la mise à jour d’un incident, sélectionnez le rôle **Contributeur Microsoft Sentinel**. Pour les actions qui lisent uniquement les données, le rôle **Lecteur Microsoft Sentinel** est suffisant. [En savoir plus sur les rôles disponibles dans Microsoft Sentinel](./roles.md).

    1. Recherchez l’application requise et enregistrez-la. Par défaut, les applications Azure AD ne figurent pas dans les options disponibles. Pour trouver votre application, recherchez-la par son nom et sélectionnez-la.

1. Authenticate

    Dans cette étape, nous utilisons les informations d’identification de l’application pour une authentification auprès du connecteur Sentinel dans Logic Apps.

    - Sélectionnez **Se connecter avec le principal de service**.

        ![Option du principal de service](media/authenticate-playbooks-to-sentinel/auth-methods-spn-choice.png)

    - Renseignez les paramètres obligatoires (qui se trouvent dans le panneau de l’application inscrite)
        - **Locataire** : sous **Vue d’ensemble**
        - **ID client** : sous **Vue d’ensemble**
        - **Secret client** : sous **Certificats et secrets**
        
        ![Se connecter avec le principal de service](media/authenticate-playbooks-to-sentinel/auth-methods-spn.png)

### <a name="manage-your-api-connections"></a>Gérer vos connexions d’API

Chaque fois qu’une authentification est créée pour la première fois, une nouvelle ressource Azure de type Connexion d’API est créée. La même connexion d’API peut être utilisée dans tous les déclencheurs et actions Microsoft Sentinel dans le même groupe de ressources.

Toutes les connexions d’API se trouvent dans le panneau **Connexions d’API** (recherchez *Connexions d’API* dans le portail Azure).

Vous pouvez également les trouver en accédant au panneau **Ressources** et en filtrant l’affichage par type *Connexion d’API*. Vous pouvez ainsi sélectionner plusieurs connexions pour les opérations en bloc.

Pour modifier l’autorisation d’une connexion existante, entrez la ressource de connexion, puis sélectionnez **Modifier la connexion d’API**.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris les différentes méthodes d’authentification d’un playbook basé sur Logic Apps auprès de Microsoft Sentinel.

- Apprenez-en davantage sur l’[utilisation des déclencheurs et des actions dans les playbooks](playbook-triggers-actions.md).
