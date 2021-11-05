---
title: Identités managées avec Azure Video Analyzer
description: Cette rubrique explique comment utiliser des identités managées avec Azure Video Analyzer.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 10/21/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5a0352586279a7d3efe7c40c4e59bda9c9fad355
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131035747"
---
# <a name="managed-identity"></a>Identité managée

La gestion des secrets et des informations d’identification pour sécuriser la communication entre les différents services constitue un défi courant pour les développeurs. Sur Azure, les identités managées évitent aux développeurs d’avoir à gérer les informations d’identification en fournissant une identité pour la ressource Azure dans Azure Active Directory (Azure AD) et en l’utilisant pour obtenir des jetons Azure AD.

Quand vous créez un compte Azure Video Analyzer, vous devez y associer un compte de stockage Azure. Si vous utilisez Video Analyzer pour enregistrer la vidéo en direct à partir d’une caméra, ces données sont stockées en tant que blobs dans un conteneur dans le compte de stockage. Vous avez la possibilité d’associer un hub IoT à votre compte Video Analyzer. Cette association est nécessaire si vous utilisez le module de périphérie Video Analyzer comme [passerelle transparente](./cloud/use-remote-device-adapter.md). Vous devez utiliser une identité managée pour accorder au compte Video Analyzer l’accès approprié au compte de stockage et au hub IoT (si celui-ci est nécessaire pour votre solution) comme suit.

## <a name="user-assigned-managed-identity-for-video-analyzer"></a>Identité managée affectée par l’utilisateur pour Video Analyzer

* Créer une [identité managée affectée par l’utilisateur (UAMI, User-Assigned Managed Identity)](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)

> [!NOTE]
> Vous allez avoir besoin d’un abonnement Azure (dans lequel vous avez accès aux rôles [Contributeur](../../role-based-access-control/built-in-roles.md#contributor) et [Administrateur de l’accès utilisateur](../../role-based-access-control/built-in-roles.md#user-access-administrator)) au groupe de ressources sous lequel vous allez créer des ressources (identité managée affectée par l’utilisateur, compte de stockage, compte Video Analyzer). Si vous ne disposez pas des autorisations appropriées, demandez à l’administrateur de compte qu’il vous les octroie. Le compte de stockage associé doit se trouver dans la même région que le compte Video Analyzer. Nous vous recommandons d’utiliser un compte de stockage [Standard v2 à usage général](../../storage/common/storage-account-overview.md#types-of-storage-accounts).
Vous devez également avoir accès au rôle contributeur pour le hub IoT si vous choisissez d’en attacher un à votre compte Video Analyzer.

### <a name="enable-video-analyzer-account-to-access-storage-account"></a>Autoriser le compte Video Analyzer à accéder au compte de stockage

* Créez un compte de stockage Azure.

* Ajoutez à l’identité managée les rôles [Contributeur des données Blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) et [Lecteur](../../role-based-access-control/built-in-roles.md#reader) pour le compte de stockage ci-dessus.

* Créez le compte de Video Analyzer, en fournissant l’identité managée affectée par l’utilisateur et le compte de stockage en tant que valeurs aux propriétés appropriées.

Video Analyzer peut alors accéder au compte de stockage en votre nom en utilisant l’identité managée.

### <a name="enable-video-analyzer-account-to-access-iot-hub"></a>Autoriser le compte Video Analyzer à accéder au hub IoT

Dans la section précédente, vous avez créé une UAMI qui permet à Video Analyzer d’accéder au compte de stockage. Au cours de cette étape, l’accès au hub IoT sera accordé au compte Video Analyzer par le biais de l’UAMI, puis le hub IoT sera lié à votre compte Video Analyzer. Pour plus d’informations sur le fonctionnement de l’identité managée avec IoT Hub, consultez [Identité managée IoT Hub](../../iot-hub/iot-hub-managed-identity.md)

* Accédez au portail Azure, puis au panneau de gestion de Video Analyzer. Sélectionnez ensuite **Hub IoT**  sous **Paramètres** dans le volet de gauche.
* Puis, sélectionnez **Attacher** dans le volet « Attacher un hub IoT ». Dans le volet de configuration **Attacher un hub IoT**, renseignez les champs requis :
    * Abonnement : sélectionnez le nom de l’abonnement Azure où le hub IoT est créé.
    * Hub IoT : sélectionnez un hub IoT existant qui doit être attaché au compte Video Analyzer.
    * Identité managée : sélectionnez l’identité managée affectée par l’utilisateur (créée à la section précédente) à utiliser pour accéder au hub IoT.
* Cliquez sur **Enregistrer** pour lier le hub IoT à votre compte Video Analyzer.

Pour voir un exemple d’utilisation du portail Azure pour accomplir l’opération ci-dessus, consultez [cet](create-video-analyzer-account.md) article.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur ce que les identités managées peuvent faire pour vous et vos applications Azure, consultez [Identités managées Azure AD](../../active-directory/managed-identities-azure-resources/overview.md).
