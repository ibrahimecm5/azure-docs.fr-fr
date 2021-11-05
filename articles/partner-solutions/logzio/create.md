---
title: Créer une ressource Logz.io - solutions de partenaires Azure
description: Article de démarrage rapide qui décrit comment créer une ressource Logz.io dans Azure.
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 10/25/2021
ms.author: tomfitz
ms.custom: references_regions
ms.openlocfilehash: d97339251888b522fd31d30b3ce77d1bb4241f3a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131070068"
---
# <a name="quickstart-create-a-logzio-resource-in-azure-portal"></a>Démarrage rapide : créer une ressource Logz.io dans le Portail Azure

Cet article explique comment activer l’intégration Azure du software as a service (SaaS) Logz.io. Vous utilisez Logz.io pour analyser l'intégrité et le niveau de performance de votre environnement Azure.

## <a name="prerequisites"></a>Configuration requise

- **Propriétaire de l’abonnement** : pour configurer Logz.io, vous devez attribuer le [Rôle de propriétaire](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) dans l’abonnement Azure. Avant de commencer cette intégration, [vérifiez votre accès](../../role-based-access-control/check-access.md).
- **Fournisseur de ressources du registre** : si `Microsoft.Insights` n’est pas déjà inscrit pour votre abonnement, inscrivez-le. Pour plus d’informations, consultez [Inscrire un fournisseur de ressources](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider).

## <a name="find-offer"></a>Rechercher une offre

Utilisez le Portail Azure pour rechercher Logz.io dans la place de marché Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Si vous avez visité la **Place de marché** dans une session récente, sélectionnez l’icône parmi les options disponibles. Dans le cas contraire, recherchez _Place de marché_.

    :::image type="content" source="./media/create/marketplace.png" alt-text="Place de marché Azure.":::

1. Dans la Place de marché Azure, recherchez _Logz.io_.
1. Sélectionnez **Logz.io - observabilité basée sur ELK et Prometheus**.
1. Sélectionnez le bouton **Configurer + s’inscrire**. La fenêtre **Créer un nouveau compte Logz.io** s’ouvre.

    :::image type="content" source="./media/create/logzio-app.png" alt-text="Application d’observation Logz.io.":::

## <a name="create-new-logzio-account"></a>Créer un nouveau compte Logz.io

Dans l’onglet **Informations de base** de l’écran **Créer un compte Logz.io**, entrez les valeurs suivantes :

| Propriété | Description |
| ---- | ---- |
| **Abonnement** | Dans la liste déroulante, sélectionnez un abonnement Azure pour lequel vous disposez d’un accès propriétaire. |
| **Groupe de ressources** | Indiquez si vous souhaitez créer un groupe de ressources ou utiliser un groupe existant. Un [groupe de ressources](../../azure-resource-manager/management/overview.md#resource-groups) est un conteneur réunissant les ressources associées d’une solution Azure. |
| **Nom du compte Logz** | Fournissez le nom du compte Logz.io que vous souhaitez créer. |
| **Lieu** | Sélectionnez **USA Ouest 2** ou **Europe Ouest**. Logz.io prend uniquement en charge ces régions Azure. |
| **Plan tarifaire** | Sélectionnez dans la liste des plans Logz.io disponibles. |
| **Conditions de facturation** | **Mensuelle** est la valeur par défaut. |
| **Price** | Spécifié en fonction du plan Logz.io sélectionné. |

:::image type="content" source="./media/create/basics.png" alt-text="Créer un compte Logz.io.":::

Après avoir entré les valeurs, sélectionnez le bouton **Suivant : journaux et mesures**.

## <a name="configure-logs"></a>Configurer les journaux

Dans l’onglet **Journaux et mesures**, spécifiez quelles ressources Azure enverront des journaux et des mesures vers Logz.io.

Il existe deux types de journaux qui peuvent être envoyés à partir d’Azure vers Logz.io :

- **Journaux de niveau abonnement** : fournit des informations sur les opérations effectuées sur chaque ressource Azure dans l’abonnement à partir de l’extérieur (plan de gestion) et les mises à jour sur des événements **Service Health**. Utilisez le **Journal d’activité** pour déterminer quoi, qui et quand de toute opération d’écriture (PUT, POST, DELETE) effectuée sur les ressources dans votre abonnement. Il n’y qu’un seul **Journal d’activité** par abonnement Azure.
- **Journaux des ressources Azure** : fournissent des informations sur les opérations qui ont été effectuées sur une ressource Azure (plan des données). Par exemple, l’obtention d’un secret à partir d’un coffre de clés ou l’exécution d’une requête sur une base de données. Le contenu des journaux de ressources varie en fonction du service Azure et du type de ressource.

Les journaux de niveau d’abonnement peuvent être envoyés à Logz.io en cochant la case **Envoyer les journaux de niveau d’abonnement**. Si cette case n’est pas cochée, aucun des journaux de niveau d’abonnement n’est envoyé à Logz.io.

Les journaux de ressources Azure peuvent être envoyés à Logz.io en cochant la case **Envoyer les journaux de ressources Azure pour toutes les ressources définies**. Les types de journaux de ressources Azure sont répertoriés dans [Catégories de journaux de ressources Azure Monitor](../../azure-monitor/essentials/resource-logs-categories.md). Pour filtrer l’ensemble spécifique des ressources Azure qui envoient des journaux à Logz.io, vous pouvez utiliser des étiquettes de ressources Azure.

Les règles d’étiquette pour l’envoi de journaux sont les suivantes :

- Par défaut, les journaux sont collectés pour toutes les ressources.
- Les ressources Azure ayant des étiquettes _Inclure_ envoient des journaux à Logz.io.
- Les ressources Azure ayant des étiquettes _Exclure_ n’envoient **pas** de journaux à Logz.io.
- En cas de conflit entre les règles d’inclusion et d’exclusion, l’exclusion a la priorité.

:::image type="content" source="./media/create/logs.png" alt-text="Configurer les journaux et les mesures.":::

Après avoir configuré les journaux et les mesures, sélectionnez le bouton **Suivant : étiquettes**.

## <a name="add-custom-tags"></a>Ajouter des étiquettes personnalisées

Vous pouvez spécifier des étiquettes personnalisées pour la nouvelle ressource Logz.io en ajoutant des paires clé-valeur.

Chaque paire clé-valeur inclut un **Nom** et une **Valeur** :

| Propriété | Description |
| ---- | ---- |
| **Nom** | Nom de l’étiquette correspondant à la ressource Azure Logz.io. |
| **Valeur** | Valeur de l’étiquette correspondant à la ressource Azure Logz.io. |

:::image type="content" source="./media/create/tags.png" alt-text="Ajouter des étiquettes personnalisées.":::

Après avoir ajouté des étiquettes, sélectionnez le bouton **Suivant : authentification unique**.

## <a name="configure-single-sign-on"></a>Configurer l’authentification unique

L’authentification unique (SSO) est une fonctionnalité facultative :

- Pour refuser l’authentification unique, ignorez cette étape.
- Pour accepter l’authentification unique, consultez [Configurer l’authentification unique Logz.io](setup-sso.md).

Une fois AAD configuré, dans l’onglet **Authentification unique**, sélectionnez votre application d’authentification unique Logz.io.

:::image type="content" source="./media/create/sso.png" alt-text="Configurer l’authentification unique.":::

Sélectionnez le bouton **Suivant : évaluer + créer** afin d’accéder à l’étape finale de création des ressources.

## <a name="create-resource"></a>Créer une ressource

Dans la page **Évaluer + créer**, toutes les validations sont exécutées. Vous pouvez consultez toutes les sélections effectuées dans les onglets **Informations de base**, **Journaux et mesures**, **Étiquettes** et **Authentification unique**. Vous pouvez également consulter les conditions générales de Logz.io et de la Place de marché Azure.

Consultez toutes les informations pour vérifier qu’elles sont correctes. Pour commencer le déploiement, sélectionnez le bouton **Créer**.

:::image type="content" source="./media/create/create-resource.png" alt-text="Évaluer et créer un compte.":::

Après un déploiement réussi, vous pouvez afficher la ressource Logz.io déployée en sélectionnant le bouton **Allez à la ressource**.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [gérer](manage.md) l’intégration de votre Logz.io.
- Pour résoudre les problèmes liés à l’intégration, consultez [Résolution des problèmes](troubleshoot.md).
