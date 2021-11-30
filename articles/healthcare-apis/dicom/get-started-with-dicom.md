---
title: Prise en main du service DICOM-API Azure Healthcare
description: Ce document décrit comment prendre en main le service DICOM dans les API de santé Azure.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 11/24/2021
ms.author: zxue
ms.openlocfilehash: bc3c691d7a990e47b6afe624854d9085169fe0b2
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133273158"
---
# <a name="get-started-with-the-dicom-service"></a>Prise en main du service DICOM

Cet article décrit les étapes de base pour prendre en main le service DICOM dans les [API de santé Azure](../healthcare-apis-overview.md). 

Comme condition préalable, vous aurez besoin d’un abonnement Azure et bénéficier des autorisations appropriées pour créer des groupes de ressources Azure et déployer des ressources Azure. Vous pouvez suivre toutes les étapes ou ignorer certains si vous disposez d’un environnement existant. En outre, vous pouvez combiner toutes les étapes et les compléter dans des scripts de l’API REST, de Azure CLI et de PowerShell.

[![Prise en main avec DICOM](media/get-started-with-dicom.png)](media/get-started-with-dicom.png#lightbox)

## <a name="create-a-workspace-in-your-azure-subscription"></a>Créer un espace de travail dans votre abonnement Azure

Vous pouvez créer un espace de travail à partir de la [portail Azure](../healthcare-apis-quickstart.md) ou à l’aide de PowerShell, Azure CLI et l’API REST. Vous trouverez des scripts à partir des [exemples d’API de santé](https://github.com/microsoft/healthcare-apis-samples/tree/main/src/scripts).

> [!NOTE]
> Il existe des limites quant au nombre d’espaces de travail et au nombre d’instances de service DICOM que vous pouvez créer dans chaque abonnement Azure.

## <a name="create-a-dicom-service-in-the-workspace"></a>Créer un service DICOM dans l’espace de travail

Vous pouvez créer une instance de service DICOM à partir de la [portail Azure](deploy-dicom-services-in-azure.md) ou à l’aide de PowerShell, Azure CLI et l’API REST. Vous trouverez des scripts à partir des [exemples d’API de santé](https://github.com/microsoft/healthcare-apis-samples/tree/main/src/scripts).

Si vous le souhaitez, vous pouvez créer un [service FHIR](../fhir/fhir-portal-quickstart.md) et un [connecteur IOT](../iot/deploy-iot-connector-in-azure.md) dans l’espace de travail.

## <a name="access-the-dicom-service"></a>Accéder au service DICOM

le service DICOM est sécurisé par Azure Active Directory (Azure AD) qui ne peuvent pas être désactivés. pour accéder à l’API de service, vous devez créer une application cliente également appelée principal du service dans Azure AD et lui accorder les autorisations appropriées.

### <a name="register-a-client-application"></a>Inscrire une application cliente

Vous pouvez créer ou inscrire une application cliente à partir de la [portail Azure](../register-application.md)ou à l’aide de scripts PowerShell et Azure CLI. Cette application cliente peut être utilisée pour une ou plusieurs instances de service DICOM. Il peut également être utilisé pour d’autres services dans les API de santé Azure.

Si l’application cliente est créée avec un certificat ou une clé secrète client, veillez à renouveler le certificat ou la clé secrète client avant l’expiration et à remplacer les informations d’identification du client dans vos applications.

Vous pouvez supprimer une application cliente. Avant cela, assurez-vous qu’il n’est pas utilisé dans les environnements de production, de développement, de test ou d’assurance qualité (AQ).

### <a name="grant-access-permissions"></a>Accorder les autorisations d’accès

Vous pouvez accorder des autorisations d’accès ou attribuer des rôles à partir de la [portail Azure](../configure-azure-rbac.md), ou à l’aide de scripts PowerShell et Azure CLI.

### <a name="perform-create-read-update-and-delete-crud-transactions"></a>Effectuer des transactions de création, de lecture, de mise à jour et de suppression (CRUD)

Vous pouvez effectuer des transactions de création, de lecture (recherche), de mise à jour et de suppression (CRUD) sur le service DICOM dans vos applications ou à l’aide d’outils tels que poster, client REST, boucle et Python. Étant donné que le service DICOM est sécurisé par défaut, vous devez obtenir un jeton d’accès et l’inclure dans votre demande de transaction.

#### <a name="get-an-access-token"></a>Obtention d’un jeton d’accès

vous pouvez obtenir un jeton d’accès Azure AD à l’aide de PowerShell, Azure CLI, de l’interface CLI REST ou du kit de développement logiciel (SDK) .net.  Pour plus d’informations, consultez [obtenir un jeton d’accès](../get-access-token.md).

#### <a name="access-using-existing-tools"></a>Accès à l’aide des outils existants

- [Postman](../use-postman.md)
- [Client REST](../using-rest-client.md)
- [.NET C #](dicomweb-standard-apis-c-sharp.md)
- [cURL](dicomweb-standard-apis-curl.md)
- [Python](dicomweb-standard-apis-python.md)

### <a name="dicomweb-standard-apis-and-change-feed"></a>API standard DICOMweb et flux de modification

Vous trouverez plus d’informations sur les API standard DICOMweb et les flux de modification dans la documentation du [service DICOM](dicom-services-overview.md) .

#### <a name="dicomcast"></a>Dicomcast

Vous pouvez utiliser [le projet FHIR](https://github.com/microsoft/dicom-server/tree/main/converter/dicom-cast) Open source pour travailler avec des données de. À l’avenir, cette fonctionnalité sera disponible dans le service géré.

## <a name="next-steps"></a>Étapes suivantes

Cet article a décrit les étapes de base pour commencer à utiliser le service DICOM. Pour plus d’informations sur le déploiement du service DICOM dans l’espace de travail, consultez.

>[!div class="nextstepaction"]
>[Déployer le service DICOM à l’aide de l’Portail Azure](deploy-dicom-services-in-azure.md)