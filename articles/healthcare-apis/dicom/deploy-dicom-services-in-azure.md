---
title: Déployer le service DICOM à l’aide des API Portail Azure-Azure Healthcare
description: Cet article décrit comment déployer le service DICOM dans le Portail Azure.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 08/04/2021
ms.author: aersoy
ms.custom: mode-portal
ms.openlocfilehash: fefaf7fc2daced95ffde90834ad38131ecfc6b94
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133041685"
---
# <a name="deploy-dicom-service-using-the-azure-portal"></a>Déployer le service DICOM à l’aide de l’Portail Azure

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Dans ce guide de démarrage rapide, vous allez apprendre à déployer le service DICOM à l’aide de l’Portail Azure.

Une fois le déploiement terminé, vous pouvez utiliser la Portail Azure pour accéder au service DICOM nouvellement créé afin d’afficher les détails, notamment votre URL de service. L’URL du service pour accéder à votre service DICOM sera : ```https://<workspacename-dicomservicename>.dicom.azurehealthcareapis.com``` . Veillez à spécifier la version dans le cadre de l’URL lors de l’exécution des requêtes. Pour plus d’informations, consultez la documentation sur le contrôle [de version des API pour le service DICOM](api-versioning-dicom-service.md).

## <a name="prerequisite"></a>Prérequis

Pour déployer le service DICOM, vous devez disposer d’un espace de travail créé dans le Portail Azure. Pour plus d’informations sur la création d’un espace de travail, voir **déployer un espace de travail dans le portail Azure**.

## <a name="deploying-dicom-service"></a>Déploiement du service DICOM

1. Sur la page **groupe de ressources** de la portail Azure, sélectionnez le nom de votre espace de **travail API de santé**.

   [![sélectionnez Groupe de ressources de l’espace de travail. ](media/select-workspace-resource-group.png) ](media/select-workspace-resource-group.png#lightbox)

2. Sélectionnez **déployer le service DICOM**.

   [![déployez le service DICOM. ](media/workspace-deploy-dicom-services.png) ](media/workspace-deploy-dicom-services.png#lightbox)


3. Sélectionnez **Ajouter un service DICOM**.

   [![Ajoutez le service DICOM. ](media/add-dicom-service.png) ](media/add-dicom-service.png#lightbox)


4. Entrez un nom pour le service DICOM, puis sélectionnez **examiner + créer**. 

    [![nom du service DICOM. ](media/enter-dicom-service-name.png) ](media/enter-dicom-service-name.png#lightbox)


   (**Facultatif**) Sélectionnez **suivant : balises >**.

    Les balises sont des paires nom/valeur utilisées pour la catégorisation des ressources. Pour plus d’informations sur les balises, consultez [utiliser des balises pour organiser vos ressources Azure et votre hiérarchie de gestion](../../azure-resource-manager/management/tag-resources.md).

5. Lorsque vous remarquez la coche de validation verte, sélectionnez **créer** pour déployer le service DICOM.

6. Une fois le processus de déploiement terminé, sélectionnez **accéder à la ressource**.  

   [![accédez à la ressource DICOM. ](media/go-to-resource.png) ](media/go-to-resource.png#lightbox)



   Le résultat du service DICOM nouvellement déployé est illustré ci-dessous.

   [le ![ déploiement de DICOM est terminé. ](media/results-deployed-dicom-service.png) ](media/results-deployed-dicom-service.png#lightbox)



## <a name="next-steps"></a>Étapes suivantes

>[!div class="nextstepaction"]
>[Vue d’ensemble du service DICOM](dicom-services-overview.md)
