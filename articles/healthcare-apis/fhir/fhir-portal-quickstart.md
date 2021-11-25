---
title: Déployer un service FHIR dans les API de santé Azure
description: Cet article apprend aux utilisateurs comment déployer un service FHIR dans le Portail Azure.
author: stevewohl
ms.service: healthcare-apis
ms.topic: quickstart
ms.date: 09/10/2021
ms.author: ginle
ms.custom: mode-other
ms.openlocfilehash: 659fb82e35cd25622158d05a88d85a16e736c770
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133069710"
---
# <a name="deploy-a-fhir-service-within-azure-healthcare-apis---using-portal"></a>Déployer un service FHIR dans les API de santé Azure-à l’aide du portail

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Dans cet article, vous allez apprendre à déployer le service FHIR dans les API de santé Azure (par le biais du service FHIR) à l’aide de la Portail Azure.

## <a name="prerequisite"></a>Prérequis

Avant de commencer, vous devez déjà avoir déployé les API de santé Azure. Pour plus d’informations sur le déploiement des API de santé Azure, voir [déployer un espace de travail dans le portail Azure](../healthcare-apis-quickstart.md).

## <a name="create-a-new-fhir-service"></a>Créer un nouveau service FHIR

Dans l’espace de travail, sélectionnez **déployer les services FHIR**.

[![Déployer le service ](media/fhir-service/deploy-fhir-services.png) FHIR ](media/fhir-service/deploy-fhir-services.png#lightbox)

Sélectionnez **+ Ajouter un service FHIR**.

[![Ajouter un service ](media/fhir-service/add-fhir-service.png) FHIR ](media/fhir-service/add-fhir-service.png#lightbox)

Entrez un **nom de compte** pour votre service FHIR. Sélectionnez la **version FHIR** (**STU3** ou **R4**), puis sélectionnez **examiner + créer**.

[![Créer un service ](media/fhir-service/create-fhir-service.png) FHIR ](media/fhir-service/create-fhir-service.png#lightbox)

Avant de sélectionner **créer**, passez en revue les propriétés des paramètres de **base** et des **paramètres supplémentaires** de votre service FHIR. Si vous devez revenir en arrière et apporter des modifications, sélectionnez **précédent**. Confirmez que le message de réussite de la **validation** s’affiche. 

[![Valider le service ](media/fhir-service/validation-fhir-service.png) FHIR ](media/fhir-service/validation-fhir-service.png#lightbox)

## <a name="additional-settings-optional"></a>Paramètres supplémentaires (facultatif)

Vous pouvez également sélectionner l’onglet **paramètres supplémentaires** pour afficher les paramètres d’authentification. La configuration par défaut de l’API Azure pour FHIR consiste à **utiliser Azure RBAC pour attribuer des rôles de plan de données**. lorsqu’il est configuré dans ce mode, l' « autorité » pour le service FHIR est défini sur le locataire Azure Active Directory de l’abonnement.

[![Service ](media/fhir-service/additional-settings-tab.png) FHIR de paramètres supplémentaires ](media/fhir-service/additional-settings-tab.png#lightbox)

Notez que la zone d’entrée des **ID d’objet autorisés** est grisée. Cela est dû au fait que nous utilisons Azure RBAC pour configurer les attributions de rôles dans ce cas.

Si vous souhaitez configurer le service FHIR pour utiliser un locataire Azure Active Directory externe ou secondaire, vous pouvez modifier l’Autorité et entrer des ID d’objet pour un utilisateur et des groupes qui doivent être autorisés à accéder au serveur.

## <a name="fetch-fhir-api-capability-statement"></a>Récupérer une déclaration de capacité relative à l’API FHIR

Pour vérifier que le nouveau compte d’API FHIR est approvisionné, récupérez une instruction de fonctionnalité en accédant à `https://<WORKSPACE NAME>-<ACCOUNT-NAME>.fhir.azurehealthcareapis.com/metadata` .

## <a name="next-steps"></a>Étapes suivantes

>[!div class="nextstepaction"]
>[Accéder au service FHIR à l’aide du poster](../use-postman.md)
