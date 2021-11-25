---
title: Gérer Microsoft Azure Maps Creator
description: Cet article explique comment gérer Microsoft Azure Maps Creator.
author: stevemunk
ms.author: v-munksteve
ms.date: 11/11/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 124b75aa0a80f84299fd0e113f2d4d7f1a427213
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132400203"
---
# <a name="manage-azure-maps-creator"></a>Gérer le Créateur Azure Maps

Vous pouvez utiliser Azure Maps Creator pour créer des données cartographiques d’intérieur privées. L’API Azure Maps et le module Cartes d’intérieur vous permettent de développer des applications web de carte d’intérieur interactives et dynamiques. Pour plus d’informations sur la tarification, consultez la section *Creator* dans [Tarification d’Azure Maps](https://aka.ms/CreatorPricing).

Cet article vous guide tout au long des étapes de création et de suppression d’une ressource de Créateur dans un compte Azure Maps.

## <a name="create-creator-resource"></a>Créer une ressource Creator

1. Connectez-vous au [portail Azure](https://portal.azure.com)

2. Accédez au menu du portail Azure. Sélectionnez **Toutes les ressources**, puis votre compte Azure Maps.

      :::image type="content" border="true" source="./media/how-to-manage-creator/select-all-resources.png" alt-text="Sélectionner un compte Azure Maps":::

3. Dans le volet de navigation, sélectionnez **Vue d’ensemble de Creator**, puis sélectionnez **Créer**.

    :::image type="content" border="true" source="./media/how-to-manage-creator/creator-blade-settings.png" alt-text="Page Créer un Créateur Azure Maps":::

4. Entrez le nom, l’emplacement et le mappage des unités de stockage d’approvisionnement pour votre ressource Creator, puis sélectionnez **Vérifier + créer**.

   :::image type="content" source="./media/how-to-manage-creator/creator-creation-dialog.png" alt-text="Page Entrer les informations du compte du Créateur":::

5. Vérifiez vos paramètres, puis sélectionnez **Créer**.

    :::image type="content" source="./media/how-to-manage-creator/creator-create-dialog.png" alt-text="Page Confirmer les paramètres du compte du créateur":::

    Une fois le déploiement terminé, une page contenant un message de réussite ou d’échec s’affiche.

    :::image type="content" source="./media/how-to-manage-creator/creator-resource-created.png" alt-text="Page État du déploiement de la ressource":::

6. Sélectionnez **Accéder à la ressource**. La page d’affichage de la ressource de Créateur montre l’état de votre ressource de Créateur et la région démographique choisie.
      :::image type="content" source="./media/how-to-manage-creator/creator-resource-view.png" alt-text="Page État du créateur":::

   >[!NOTE]
   >Pour revenir au compte Azure Maps, sélectionnez **Compte Azure Maps** dans le volet de navigation.

## <a name="delete-creator-resource"></a>Supprimer une ressource Creator

Pour supprimer une ressource Creator :

1. Dans votre compte Azure Maps, sélectionnez **Vue d’ensemble** sous **Creator**.

2. Sélectionnez **Supprimer**.

    >[!WARNING]
    >Lorsque vous supprimez la ressource Creator de votre compte Azure Maps, vous supprimez également les conversions, jeux de données, tilesets et statesets de caractéristiques créés à l’aide des services Creator. Une fois qu’une ressource Creator est supprimée, il est impossible de la rétablir.

     :::image type="content" source="./media/how-to-manage-creator/creator-delete.png" alt-text="Page Créateur avec le bouton Supprimer":::

3. Vous êtes invité à confirmer la suppression en tapant le nom de votre ressource Creator. Une fois la ressource supprimée, une page de confirmation semblable à la suivante s’affiche :

     :::image type="content" source="./media/how-to-manage-creator/creator-confirm-delete.png" alt-text="Page Créateur avec confirmation de la suppression":::

## <a name="authentication"></a>Authentification

Le Créateur hérite des paramètres de Contrôle d’accès (IAM) d’Azure Maps. Tous les appels d’API pour l’accès aux données doivent être envoyés avec des règles d’authentification et d’autorisation.

Les données d’utilisation du Créateur sont incorporées dans vos graphiques d’utilisation et journal d’activité Azure Maps.  Pour plus d’informations, consultez [Gérer l’authentification dans Azure Maps](./how-to-manage-authentication.md).

>[!Important]
>Nous vous recommandons d’utiliser :
>
> * Azure Active Directory (Azure AD) dans toutes les solutions créées avec un compte Azure Maps utilisant des services Creator. Pour plus d’informations, dans Azure AD, consultez [Authentification Azure AD](azure-maps-authentication.md#azure-ad-authentication).
>
>* Paramètres de contrôle d’accès en fonction du rôle (RBAC). À l’aide de ces paramètres, les créateurs de cartes peuvent prendre le rôle de Contributeur aux données Azure Maps, et les utilisateurs de données cartographiques Creator, celui de Lecteur de données Azure Maps. Pour plus d’informations, consultez [Autorisation avec contrôle d’accès en fonction du rôle](azure-maps-authentication.md#authorization-with-role-based-access-control).

## <a name="access-to-creator-services"></a>Accès aux services du Créateur

Les services Creator et les services qui utilisent des données hébergées dans Creator (par exemple, le service Render) sont accessibles à une URL géographique. L’URL géographique est déterminée par l’emplacement sélectionné lors de la création. Par exemple, si Creator est créé dans une région aux États-Unis, tous les appels au service Conversion doivent être envoyés à `us.atlas.microsoft.com/conversions`. Pour afficher les mappages de la région à l’emplacement géographique, [consultez Limite géographique du service Creator](creator-geographic-scope.md).

De même, toutes les données importées dans le Créateur doivent être chargées dans le même emplacement géographique que la ressource de Créateur. Par exemple, si Creator est approvisionné au États-Unis, toutes les données brutes doivent être chargées via `us.atlas.microsoft.com/mapData/upload`.

## <a name="next-steps"></a>Étapes suivantes

Présentation des services Creator pour les cartes d’intérieur :

> [!div class="nextstepaction"]
> [Chargement de données](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [Conversion de données](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [Dataset](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [Tileset](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [Jeu d’état de caractéristique](creator-indoor-maps.md#feature-statesets)

Découvrez comment utiliser les services Creator pour afficher des cartes d’intérieur dans votre application :

> [!div class="nextstepaction"]
> [Tutoriel sur le Créateur Azure Maps](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Application de style dynamique de carte d’intérieur](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [Utiliser le module Cartes d’intérieur](how-to-use-indoor-module.md)
