---
title: Obtenir un jeton d’accès à l’aide de Azure CLI-API de santé Azure pour le service DICOM
description: Cet article explique comment obtenir un jeton d’accès pour le service DICOM à l’aide de l’Azure CLI.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 07/10/2021
ms.author: aersoy
ms.openlocfilehash: 1fe6fe950d11f30079d9b0d9ea3b91b5edf0711d
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132814608"
---
# <a name="get-access-token-for-the-dicom-service-using-azure-cli"></a>Obtenir un jeton d’accès pour le service DICOM à l’aide de Azure CLI

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Dans cet article, vous allez apprendre à obtenir un jeton d’accès pour le service DICOM à l’aide de l’Azure CLI. Lorsque vous [déployez le service DICOM](deploy-dicom-services-in-azure.md), vous configurez un ensemble d’utilisateurs ou de principaux de service ayant accès au service. Si votre ID d’objet utilisateur figure dans la liste des ID d’objet autorisés, vous pouvez accéder le service à l’aide d’un jeton obtenu à l’aide d’Azure CLI.

## <a name="prerequisites"></a>Prérequis

Utilisez l’environnement bash dans Azure Cloud Shell.


[![Lancez Azure Cloud Shell. ](media/launch-cloud-shell.png) ](media/launch-cloud-shell.png#lightbox)

Si vous préférez, [installez](/cli/azure/install-azure-cli) l’interface Azure CLI pour exécuter les commandes de référence de l’interface de ligne de commande.

* Si vous utilisez une installation locale, connectez-vous à Azure CLI à l’aide de la commande [az login](/cli/azure/reference-index#az_login). Pour finir le processus d’authentification, suivez les étapes affichées dans votre terminal. Pour connaître les autres options de connexion, consultez [Se connecter avec Azure CLI](/cli/azure/authenticate-azure-cli).
* Lorsque vous y êtes invité, installez les extensions Azure CLI lors de la première utilisation. Pour plus d’informations sur les extensions, consultez [Utiliser des extensions avec Azure CLI](/cli/azure/azure-cli-extensions-overview).
* Exécutez [az version](/cli/azure/reference-index#az_version) pour rechercher la version et les bibliothèques dépendantes installées. Pour effectuer une mise à niveau vers la dernière version, exécutez [az upgrade](/cli/azure/reference-index#az_upgrade).

## <a name="obtain-a-token"></a>Obtenir un jeton

Le service DICOM utilise un `resource` ou un `Audience` URI égal à l’URI du serveur DICOM  `https://dicom.healthcareapis.azure.com` . Vous pouvez obtenir un jeton et le stocker dans une variable (nommée `$token`) avec la commande suivante :


```Azure CLICopy
Try It
$token=$(az account get-access-token --resource=https://dicom.healthcareapis.azure.com --query accessToken --output tsv)
```

## <a name="use-with-the-dicom-service"></a>Utilisation avec le service DICOM

```Azure CLICopy
Try It
curl -X GET --header "Authorization: Bearer $token"  https://<workspacename-dicomservicename>.dicom.azurehealthcareapis.com/v<version of REST API>/changefeed
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à obtenir un jeton d’accès pour le service DICOM à l’aide de l’Azure CLI. 

>[!div class="nextstepaction"]
>[Vue d’ensemble du service DICOM](dicom-services-overview.md)
