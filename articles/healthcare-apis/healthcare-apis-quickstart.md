---
title: Déployer l’espace de travail dans le Portail Azure-API de santé Azure
description: Ce document enseigne aux utilisateurs comment déployer un espace de travail dans le Portail Azure.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 07/12/2021
ms.author: ginle
ms.custom: mode-portal
ms.openlocfilehash: 89be6d8bab9ef544ffa62a85f0b87afb15fab013
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133061567"
---
# <a name="deploy-healthcare-apis-preview-workspace-using-azure-portal"></a>Déployer un espace de travail API de santé (préversion) à l’aide de Portail Azure

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Dans cet article, vous allez apprendre à créer un espace de travail en déployant des API de santé Azure via le Portail Azure. L’espace de travail est un conteneur logique centralisé pour tous vos services API de santé tels que FHIR services, DICOM® services et IoT Connectors. Elle vous permet d’organiser et de gérer certains paramètres de configuration partagés entre tous les jeux de données et services sous-jacents, le cas échéant.


## <a name="prerequisite"></a>Prérequis

Avant de pouvoir créer un espace de travail dans le Portail Azure, vous devez disposer d’un abonnement à un compte. Si vous n’avez pas d’abonnement Azure, consultez [créer votre compte Azure gratuit dès aujourd’hui](https://azure.microsoft.com/free/search/?OCID=AID2100131_SEM_c4b0772dc7df1f075552174a854fd4bc:G:s&ef_id=c4b0772dc7df1f075552174a854fd4bc:G:s&msclkid=c4b0772dc7df1f075552174a854fd4bc).

## <a name="create-new-azure-service"></a>Créer un service Azure

Dans le portail Azure, sélectionnez **Créer une ressource**.

[![Créer une ressource ](media/create-resource.png) ](media/create-resource.png#lightbox)

## <a name="search-for-azure-healthcare-apis"></a>Rechercher des API de santé Azure

Dans SearchBox, entrez **Azure Healthcare API**.

[![Rechercher des API ](media/search-for-healthcare-apis.png) de santé ](media/search-for-healthcare-apis.png#lightbox)

## <a name="create-azure-healthcare-api-account"></a>Créer un compte Azure Healthcare API

Sélectionnez **créer** pour créer un nouveau compte Azure Healthcare API.

   [![Créer un aperçu ](media/create-workspace-preview.png) de l’espace de travail ](media/create-workspace-preview.png#lightbox)

## <a name="enter-subscription-and-instance-details"></a>Entrer les détails de l’abonnement et de l’instance

1. Sélectionnez un **abonnement** et un **groupe de ressources** dans les listes déroulantes ou sélectionnez créer un **nouveau**.

   [![Créer un espace de travail nouveau ](media/create-healthcare-api-workspace-new.png) ](media/create-healthcare-api-workspace-new.png#lightbox)

2. Entrez un **nom** pour l’espace de travail, puis sélectionnez une **région**. Le nom doit comprendre entre 3 et 24 caractères alphanumériques, tous en minuscules. N’utilisez pas de trait d’Union « - », car il s’agit d’un caractère non valide pour le nom. Pour plus d’informations sur les régions et les zones de disponibilité, consultez [régions et zones de disponibilité dans Azure](../availability-zones/az-overview.md).

3. (**Facultatif**) Sélectionnez **suivant : balises >**. Entrez un **nom** et une **valeur**, puis sélectionnez **suivant : passer en revue + créer**. 

   [![Balises ](media/tags-new.png) ](media/tags-new.png#lightbox)

   Les balises sont des paires nom/valeur utilisées pour la catégorisation des ressources. Pour plus d’informations sur les balises, consultez [utiliser des balises pour organiser vos ressources Azure et votre hiérarchie de gestion](.././azure-resource-manager/management/tag-resources.md).

4. Sélectionnez **Create** (Créer).

[![Termes ](media/workspace-terms.png) de l’espace de travail ](media/workspace-terms.png)


   **Facultatif**: vous pouvez sélectionner **Télécharger un modèle pour l’automatisation** de l’espace de travail que vous venez de créer.


## <a name="next-steps"></a>Étapes suivantes

Maintenant que l’espace de travail est créé, vous pouvez :

* Déployer FHIR services
* Déployer des services DICOM
* déployez une IoT Connector et transmettrez des données à votre service FHIR.
* Transformez vos données dans différents formats et utilisez l’utilisation secondaire par le biais de nos API de conversion et de désidentification.


[![Déployer différents services ](media/healthcare-apis-deploy-services.png) ](media/healthcare-apis-deploy-services.png)

>[!div class="nextstepaction"]
>[Vue d’ensemble de l’espace de travail](workspace-overview.md)
