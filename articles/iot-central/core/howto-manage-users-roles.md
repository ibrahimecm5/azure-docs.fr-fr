---
title: Gérer les utilisateurs et rôles dans votre application Azure IoT Central | Microsoft Docs
description: Comment gérer les rôles et utilisateurs de votre application Azure IoT Central en qualité d’administrateur
author: dominicbetts
ms.author: dobett
ms.date: 08/20/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 4398ab5ed46276c397e812cb4ffbcd1e6e1296e6
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997568"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>Gérer les utilisateurs et rôles dans votre application Azure IoT Central

Cet article explique comment vous pouvez ajouter, modifier et supprimer des utilisateurs dans votre application Azure IoT Central. L’article explique également comment gérer des rôles dans votre application.

Pour accéder à la section **Administration** et l’utiliser, vous devez avoir le rôle **Administrateur d’application** pour une application Azure IoT Central ou un rôle personnalisé incluant des autorisations d’administration. Si vous créez une application Azure IoT Central, le rôle **Administrateur d’application** vous est automatiquement attribué pour cette application.

## <a name="add-users"></a>Ajouter des utilisateurs

Chaque utilisateur doit disposer d’un compte d’utilisateur pour pouvoir se connecter à une application et y accéder. IoT Central prend actuellement en charge les comptes Microsoft et les comptes Azure Active Directory, mais pas les groupes Azure Active Directory.

Pour en savoir plus, consultez [Aide sur le compte Microsoft](https://support.microsoft.com/products/microsoft-account?category=manage-account) et [Démarrage rapide : Ajouter ou supprimer des utilisateurs à l’aide d’Azure Active Directory](../../active-directory/fundamentals/add-users-azure-active-directory.md).

1. Pour ajouter un utilisateur à une application IoT Central, accédez à la page **Utilisateurs** dans la section **Administration**.

    :::image type="content" source="media/howto-manage-users-roles/manage-users-pnp.png" alt-text="Capture d’écran de la gestion des utilisateurs.":::

1. Pour ajouter un utilisateur, dans la page **Utilisateurs**, choisissez **+ Attribuer un utilisateur**.

1. Si votre application utilise des [organisations](howto-create-organizations.md), choisissez une organisation à attribuer à l’utilisateur dans le menu déroulant **Organisation**.

1. Choisissez un rôle pour l’utilisateur dans la liste déroulante **Rôle**. En savoir plus sur les rôles dans la section [Gérer les rôles](#manage-roles) de cet article.

    :::image type="content" source="media/howto-manage-users-roles/add-user-pnp.png" alt-text="Capture d’écran d’ajout d’un utilisateur et de sélection d’un rôle.":::

    Les rôles disponibles dépendent de l’organisation à laquelle l’utilisateur est associé. Vous pouvez attribuer des rôles **Application** à des utilisateurs associés à l’organisation racine, et des rôles **Organisation** à des utilisateurs associés à toute autre organisation figurant dans la hiérarchie.

    > [!NOTE]
    > Un utilisateur qui se trouve dans un rôle personnalisé qui lui accorde l’autorisation d’ajouter d’autres utilisateurs peut uniquement ajouter des utilisateurs à un rôle avec des autorisations identiques ou moins nombreuses que son propre rôle.
  
    > [!NOTE]
    > Si un utilisateur est supprimé d’Azure Active Directory puis rajouté, il ne peut pas se connecter à l’application IoT Central. Pour réactiver l’accès, l’administrateur de l’application doit également supprimer et rajouter l’utilisateur dans l’application.

### <a name="edit-the-roles-and-organizations-that-are-assigned-to-users"></a>Modifier les rôles et les organisations attribués aux utilisateurs

Il n’est pas possible de modifier des rôles et organisations après leur attribution. Pour modifier le rôle ou l’organisation attribués à un utilisateur, supprimez celui-ci, puis rajoutez-le avec un rôle ou une organisation différents.

> [!NOTE]
> Les rôles attribués sont spécifiques à l’application IoT Central et ne peuvent pas être gérés à partir du portail Azure.

## <a name="delete-users"></a>Suppression d’utilisateurs

Pour supprimer des utilisateurs, sélectionnez une ou plusieurs cases à cocher sur la page **Utilisateurs**. Puis sélectionnez **Supprimer**.

## <a name="manage-roles"></a>Gérer les rôles

Les rôles vous permettent de contrôler qui, au sein de votre organisation, peut effectuer différentes tâches dans IoT Central. Vous pouvez attribuer trois rôles intégrés aux utilisateurs de votre application. Vous pouvez également [créer des rôles personnalisés](#create-a-custom-role) si vous avez besoin d’un contrôle plus fin.

:::image type="content" source="media/howto-manage-users-roles/manage-roles-pnp.png" alt-text="Capture d’écran de la sélection de gestion des rôle.":::

### <a name="app-administrator"></a>Administrateur d’application

Les utilisateurs auxquels est attribué le rôle **Administrateur d’administrateur** peuvent gérer et contrôler toutes les parties de l’application, dont la facturation.

L’utilisateur qui crée une application se voit automatiquement attribué le rôle **Administrateur d’application**. Le rôle **Administrateur d’application** doit toujours être attribué à au moins un utilisateur.

### <a name="app-builder"></a>Générateur d’application

Les utilisateurs auxquels est attribué le rôle **Créateur** peuvent gérer toutes les parties de l’application, mais ne peuvent pas changer les onglets Administration ou Exportation continue des données.

### <a name="app-operator"></a>Opérateur d’application

Les utilisateurs auxquels est attribué le rôle **Opérateur d’application** peuvent surveiller l’intégrité et l’état des appareils. Ils ne sont pas autorisés à apporter des modifications aux modèles d’appareils ni à administrer l’application. Les opérateurs peuvent ajouter et supprimer des appareils, gérer des ensembles d’appareils et exécuter des analytiques et des travaux.

### <a name="org-administrator"></a>Administrateur de l’organisation

IoT Central ajoute ce rôle automatiquement quand vous ajoutez une organisation à votre application. Ce rôle empêche les administrateurs d’organisation d’accéder à certaines fonctionnalités d’application, telles que la facturation, la personnalisation, les couleurs, les jetons d’API et les informations du groupe d’inscription.

Les utilisateurs auxquels est attribué le rôle **Administrateur d’organisation** peuvent inviter des utilisateurs à accéder à l’application, créer des sous-organisations dans la hiérarchie de leur organisation, et gérer les appareils au sein de leur organisation.

### <a name="org-operator"></a>Opérateur d’organisation

IoT Central ajoute ce rôle automatiquement quand vous ajoutez une organisation à votre application. Ce rôle empêche les opérateurs d’organisation d’accéder à certaines fonctionnalités à l’échelle de l’application.

Les utilisateurs auxquels est attribué le rôle **Opérateur d’organisation** peuvent effectuer des tâches telles que l’ajout d’appareils, l’exécution de commandes, l’affichage de données d’appareil, la création de tableaux de bord et la création de groupes d’appareils.

### <a name="org-viewer"></a>Observateur de l’organisation

IoT Central ajoute ce rôle automatiquement quand vous ajoutez une organisation à votre application.

Les utilisateurs auxquels est attribué le rôle **Observateur d’organisation** peuvent afficher des éléments tels que des appareils et leurs données, des tableaux de bord d’organisation, des groupes d’appareils et des modèles d’appareil.

## <a name="create-a-custom-role"></a>Créer un rôle personnalisé

Si votre solution nécessite des contrôles d’accès plus précis, vous pouvez créer des rôles avec des ensembles d’autorisations personnalisés. Pour créer un rôle personnalisé, accédez à la page **Rôle** dans la section **Administration** de votre application, puis choisissez l’une des options suivantes :

- Sélectionnez **+ Nouveau**, ajoutez un nom et une description pour votre rôle, puis sélectionnez **Application** ou **Organisation** en tant que type de rôle. Cette option vous permet de créer une définition de rôle à partir de rien.
- Accédez à un rôle existant et sélectionnez **Copier**. Cette option vous permet de commencer avec une définition de rôle existante que vous pouvez personnaliser.

:::image type="content" source="media/howto-manage-users-roles/create-custom-role-pnp.png" alt-text="Capture d’écran de la création d’un rôle personnalisé.":::

> [!WARNING]
> Après avoir créé un rôle, vous ne pouvez pas modifier son type.

Lorsque vous invitez un utilisateur à accéder à votre application, si vous associez l’utilisateur à :

- l’organisation racine, seuls les rôles **Application** sont disponibles.
- toute autre organisation, seuls les rôles **Organisation** sont disponibles.

Vous pouvez ajouter des utilisateurs à votre rôle personnalisé de la même façon que vous ajoutez des utilisateurs à un rôle intégré.

### <a name="custom-role-options"></a>Options de rôle personnalisé

Lorsque vous définissez un rôle personnalisé, vous choisissez le jeu d’autorisations qu’un utilisateur reçoit s’il est membre du rôle. Certaines autorisations dépendent les unes des autres. Par exemple, si vous ajoutez l’autorisation **Mettre à jour les tableaux de bord personnels** à un rôle, l’autorisation **Afficher les tableaux de bord personnels** est automatiquement ajoutée. Les tableaux suivants résument les autorisations disponibles et leurs dépendances, que vous pouvez utiliser lors de la création de rôles personnalisés.

#### <a name="managing-devices"></a>Gestion des appareils

**Autorisations du modèle d’appareil**

| Nom | Les dépendances |
| ---- | -------- |
| Affichage | None     |
| Gérer | Affichage <br/> Autres dépendances : Afficher les instances d’appareil  |
| Contrôle total | Afficher, Gérer <br/> Autres dépendances : Afficher les instances d’appareils |

**Autorisations des instances d’appareils**

| Nom | Les dépendances |
| ---- | -------- |
| Affichage | None <br/> Autres dépendances : Afficher les modèles d’appareils et les groupes d’appareils |
| Update | Affichage <br/> Autres dépendances : Afficher les modèles d’appareils et les groupes d’appareils  |
| Créer | Affichage <br/> Autres dépendances : Afficher les modèles d’appareils et les groupes d’appareils  |
| DELETE | Affichage <br/> Autres dépendances : Afficher les modèles d’appareils et les groupes d’appareils  |
| Exécuter des commandes | Mettre à jour, Afficher <br/> Autres dépendances : Afficher les modèles d’appareils et les groupes d’appareils  |
| Voir les données brutes | Affichage <br/> Autres dépendances : Afficher les modèles d’appareils et les groupes d’appareils  |
| Contrôle total | Afficher, Mettre à jour, Créer, Supprimer, Exécuter des commandes, Afficher les données brutes <br/> Autres dépendances : Afficher les modèles d’appareils et les groupes d’appareils  |

**Autorisations des groupes d’appareils**

| Nom | Les dépendances |
| ---- | -------- |
| Affichage | None <br/> Autres dépendances : Afficher les modèles d’appareils et les instances d’appareils |
| Update | Affichage <br/> Autres dépendances : Afficher les modèles d’appareils et les instances d’appareils   |
| Créer | Afficher, Mettre à jour <br/> Autres dépendances : Afficher les modèles d’appareils et les instances d’appareils   |
| DELETE | Affichage <br/> Autres dépendances : Afficher les modèles d’appareils et les instances d’appareils  |
| Contrôle total | Afficher, Mettre à jour, Créer, Supprimer <br/> Autres dépendances : Afficher les modèles d’appareils et les instances d’appareils |

**Autorisations de gestion de la connectivité des appareils**

| Nom | Les dépendances |
| ---- | -------- |
| Lire l’instance | None <br/> Autres dépendances : Afficher les modèles d’appareils, les groupes d’appareils, les instances d’appareils |
| Gérer l’instance | Lire l’instance <br /> Autres dépendances : Afficher les modèles d’appareils, les groupes d’appareils, les instances d’appareils |
| Lire au niveau global | None   |
| Gérer au niveau global | Lire au niveau global |
| Contrôle total | Lire l’instance, Gérer l’instance, Lire au niveau global, Gérer au niveau global <br/> Autres dépendances : Afficher les modèles d’appareils, les groupes d’appareils, les instances d’appareils |

**Autorisations de travaux**

| Nom | Les dépendances |
| ---- | -------- |
| Affichage | None <br/> Autres dépendances : Afficher les modèles d’appareils, les instances d’appareils et les groupes d’appareils |
| Update | Affichage <br/> Autres dépendances : Afficher les modèles d’appareils, les instances d’appareils et les groupes d’appareils |
| Créer | Afficher, Mettre à jour <br/> Autres dépendances : Afficher les modèles d’appareils, les instances d’appareils et les groupes d’appareils |
| DELETE | Affichage <br/> Autres dépendances : Afficher les modèles d’appareils, les instances d’appareils et les groupes d’appareils |
| Execute | Affichage <br/> Autres dépendances : Afficher les modèles d’appareils, les instances d’appareils et les groupes d’appareils ; Mettre à jour les instances d’appareils ; Exécuter des commandes sur des instances d’appareils |
| Contrôle total | Afficher, Mettre à jour, Créer, Supprimer, Exécuter <br/> Autres dépendances : Afficher les modèles d’appareils, les instances d’appareils et les groupes d’appareils ; Mettre à jour les instances d’appareils ; Exécuter des commandes sur des instances d’appareils |

**Autorisations de règles**

| Nom | Les dépendances |
| ---- | -------- |
| Affichage | None <br/> Autres dépendances : Afficher les modèles d’appareils |
| Update | Affichage <br/> Autres dépendances : Afficher les modèles d’appareils |
| Créer | Afficher, Mettre à jour <br/> Autres dépendances : Afficher les modèles d’appareils |
| DELETE | Affichage <br/> Autres dépendances : Afficher les modèles d’appareils |
| Contrôle total | Afficher, Mettre à jour, Créer, Supprimer <br/> Autres dépendances : Afficher les modèles d’appareils |

#### <a name="managing-the-app"></a>Gestion de l’application

**Autorisations des paramètres d’application**

| Nom | Les dépendances |
| ---- | -------- |
| Affichage | None     |
| Update | Affichage   |
| Copier | Affichage <br/> Autres dépendances : Afficher les modèles d’appareils, les instances d’appareils, les groupes d’appareils, les tableaux de bord, l’exportation de données, la personnalisation, les liens d’aide, les rôles personnalisés, les règles |
| DELETE | Affichage   |
| Contrôle total | Afficher, Mettre à jour, Copier, Supprimer <br/> Autres dépendances : Afficher les modèles d’appareils, les groupes d’appareils, les tableaux de bord d’application, l’exportation de données, la personnalisation, les liens d’aide, les rôles personnalisés, les règles |

**Autorisations d’exportation du modèle d’application**

| Nom | Les dépendances |
| ---- | -------- |
| Affichage | None     |
| Exporter | Affichage <br/> Autres dépendances : Afficher les modèles d’appareils, les instances d’appareils, les groupes d’appareils, les tableaux de bord, l’exportation de données, la personnalisation, les liens d’aide, les rôles personnalisés, les règles |
| Contrôle total | Afficher, Exporter <br/> Autres dépendances : Afficher les modèles d’appareils, les groupes d’appareils, les tableaux de bord d’application, l’exportation de données, la personnalisation, les liens d’aide, les rôles personnalisés, les règles |

**Autorisations de chargement de fichier d’appareil**

| Nom | Les dépendances |
| ---- | -------- |
| Affichage | None     |
| Gérer | Affichage   |
| Contrôle total | Afficher, Gérer |

**Autorisations de facturation**

| Nom | Les dépendances |
| ---- | -------- |
| Gérer | None     |
| Contrôle total | Gérer |

#### <a name="managing-users-and-roles"></a>Gestion des utilisateurs et des rôles

**Autorisations de rôles personnalisés**

| Nom | Les dépendances |
| ---- | -------- |
| Affichage | None |
| Update | Affichage |
| Créer | Afficher, Mettre à jour |
| DELETE | Affichage |
| Contrôle total | Afficher, Mettre à jour, Créer, Supprimer |

**Autorisations de gestion des utilisateurs**

| Nom | Les dépendances |
| ---- | -------- |
| Affichage | None <br/> Autres dépendances : Afficher les rôles personnalisés |
| Ajouter | Affichage <br/> Autres dépendances : Afficher les rôles personnalisés |
| DELETE | Affichage <br/> Autres dépendances : Afficher les rôles personnalisés |
| Contrôle total | Afficher, Ajouter, Supprimer <br/> Autres dépendances : Afficher les rôles personnalisés |

**Autorisations de gestion de l’organisation**

| Nom | Les dépendances |
| ---- | -------- |
| Affichage | None |
| Update | Affichage |
| Créer | Afficher, Mettre à jour |
| DELETE | Affichage |
| Contrôle total | Afficher, Mettre à jour, Créer, Supprimer |

> [!NOTE]
> Un utilisateur qui se trouve dans un rôle personnalisé qui lui accorde l’autorisation d’ajouter d’autres utilisateurs peut uniquement ajouter des utilisateurs à un rôle avec des autorisations identiques ou moins nombreuses que son propre rôle.

#### <a name="customizing-the-app"></a>Personnalisation de l’application

**Autorisations des tableaux de bord d’applications**

| Nom | Les dépendances |
| ---- | -------- |
| Affichage | None     |
| Update | Affichage   |
| Créer | Afficher, Mettre à jour |
| DELETE | Affichage   |
| Contrôle total | Afficher, Mettre à jour, Créer, Supprimer |

**Autorisations des tableaux de bord personnels**

| Nom | Les dépendances |
| ---- | -------- |
| Affichage | None     |
| Update | Affichage   |
| Créer | Afficher, Mettre à jour   |
| DELETE | Affichage   |
| Contrôle total | Afficher, Mettre à jour, Créer, Supprimer |

**Autorisations Marque, Icône favorite et Couleurs**

| Nom | Les dépendances |
| ---- | -------- |
| Affichage | None     |
| Update | Affichage   |
| Contrôle total | Afficher, Mettre à jour |

**Autorisations de liens d’aide**

| Nom | Les dépendances |
| ---- | -------- |
| Affichage | None     |
| Update | Affichage   |
| Contrôle total | Afficher, Mettre à jour |

#### <a name="extending-the-app"></a>Extension de l’application

**Autorisations d’exportation de données**

| Nom | Les dépendances |
| ---- | -------- |
| Affichage | None     |
| Update | Affichage   |
| Créer | Afficher, Mettre à jour  |
| DELETE | Affichage   |
| Contrôle total | Afficher, Mettre à jour, Créer, Supprimer |

**Autorisations de jeton d’API**

| Nom | Les dépendances |
| ---- | -------- |
| Affichage | None  <br/> Autres dépendances : Afficher les rôles personnalisés |
| Créer | Affichage <br/> Autres dépendances : Afficher les rôles personnalisés |
| DELETE | Affichage <br/> Autres dépendances : Afficher les rôles personnalisés |
| Contrôle total | Afficher, Créer, Supprimer <br/> Autres dépendances : Afficher les rôles personnalisés |

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à gérer les utilisateurs et les rôles dans votre application IoT Central, l'étape suivante suggérée est d'apprendre à [Personnaliser l'interface utilisateur de l'application](howto-customize-ui.md).
