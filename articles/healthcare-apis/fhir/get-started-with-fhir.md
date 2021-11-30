---
title: Prise en main du service FHIR-API Azure Healthcare
description: Ce document décrit comment prendre en main le service FHIR dans les API de santé Azure.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 11/24/2021
ms.author: zxue
ms.openlocfilehash: f679f82531c84b5c05de4bddc2551c9c51c6d09b
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133273171"
---
# <a name="get-started-with-the-fhir-service"></a>Prise en main du service FHIR

Cet article décrit les étapes de base pour la prise en main du service FHIR dans les [API de santé Azure](../healthcare-apis-overview.md).

Comme condition préalable, vous aurez besoin d’un abonnement Azure et bénéficier des autorisations appropriées pour créer des groupes de ressources Azure et déployer des ressources Azure. Vous pouvez suivre toutes les étapes ou ignorer certains si vous disposez d’un environnement existant. En outre, vous pouvez combiner toutes les étapes et les compléter dans des scripts de l’API REST, de Azure CLI et de PowerShell.

[![Prise en main du diagramme de service FHIR.](media/get-started-with-fhir.png)](media/get-started-with-fhir.png#lightbox)

## <a name="create-a-workspace-in-your-azure-subscription"></a>Créer un espace de travail dans votre abonnement Azure

Vous pouvez créer un espace de travail à partir de la [portail Azure](../healthcare-apis-quickstart.md)ou à l’aide de PowerShell, Azure CLI et l’API REST. Vous trouverez des scripts à partir des [exemples d’API de santé](https://github.com/microsoft/healthcare-apis-samples/tree/main/src/scripts).

> [!NOTE]
> Il existe des limites quant au nombre d’espaces de travail et au nombre d’instances de service FHIR que vous pouvez créer dans chaque abonnement Azure.

## <a name="create-a-fhir-service-in-the-workspace"></a>Créer un service FHIR dans l’espace de travail

Vous pouvez créer une instance de service FHIR à partir de la [portail Azure](../fhir/fhir-portal-quickstart.md)ou à l’aide de PowerShell, Azure CLI et l’API REST. Vous trouverez des scripts à partir des [exemples d’API de santé](https://github.com/microsoft/healthcare-apis-samples/tree/main/src/scripts).

Si vous le souhaitez, vous pouvez créer un [service DICOM](../dicom/deploy-dicom-services-in-azure.md) et un [connecteur IOT](../iot/deploy-iot-connector-in-azure.md) dans l’espace de travail.

## <a name="access-the-fhir-service"></a>Accéder au service FHIR

le service FHIR est sécurisé par Azure Active Directory (Azure AD) qui ne peuvent pas être désactivés. pour accéder à l’API de service, vous devez créer une application cliente également appelée principal du service dans Azure AD et lui accorder les autorisations appropriées.

### <a name="register-a-client-application"></a>Inscrire une application cliente

Vous pouvez créer ou inscrire une application cliente à partir de la [portail Azure](../register-application.md)ou à l’aide de scripts PowerShell et Azure CLI. Cette application cliente peut être utilisée pour une ou plusieurs instances de service FHIR. Il peut également être utilisé pour d’autres services dans les API de santé Azure.

Si l’application cliente est créée avec un certificat ou une clé secrète client, veillez à renouveler le certificat ou la clé secrète client avant l’expiration et à remplacer les informations d’identification du client dans vos applications.

Vous pouvez supprimer une application cliente. Avant de supprimer une application cliente, assurez-vous qu’elle n’est pas utilisée dans les environnements de production, de développement, de test ou d’assurance qualité (AQ).

### <a name="grant-access-permissions"></a>Accorder les autorisations d’accès

Vous pouvez accorder des autorisations d’accès ou attribuer des rôles à partir de la [portail Azure](../configure-azure-rbac.md), ou à l’aide de scripts PowerShell et Azure CLI.

### <a name="perform-create-read-update-and-delete-crud-transactions"></a>Effectuer des transactions de création, de lecture, de mise à jour et de suppression (CRUD)

Vous pouvez effectuer des transactions de création, de lecture (recherche), de mise à jour et de suppression (CRUD) sur le service FHIR dans vos applications ou à l’aide d’outils tels que poster, client REST et faire une boucle. Étant donné que le service FHIR est sécurisé par défaut, vous devez obtenir un jeton d’accès et l’inclure dans votre demande de transaction.

#### <a name="get-an-access-token"></a>Obtention d’un jeton d’accès

vous pouvez obtenir un jeton d’accès Azure AD à l’aide de PowerShell, Azure CLI, icc REST ou le kit de développement logiciel (SDK) .net.  Pour plus d’informations, consultez [obtenir un jeton d’accès](../get-access-token.md).

#### <a name="access-using-existing-tools"></a>Accès à l’aide des outils existants

- [Postman](../use-postman.md)
- [Client Rest](../using-rest-client.md)
- [cURL](../using-curl.md)

#### <a name="load-data"></a>Charger les données

Vous pouvez charger des données directement à l’aide de la méthode d’envoi ou de placement sur le service FHIR. Pour charger des données en masse, vous pouvez utiliser l’un des outils open source listés ci-dessous.
 
- [Chargeur FHIR](https://github.com/microsoft/healthcare-apis-samples/tree/main/src/FHIRDL) Il s’agit d’une application console .NET qui charge les données stockées dans le stockage Azure dans le service FHIR. Il s’agit d’une application à thread unique, mais vous pouvez exécuter plusieurs copies localement ou dans un conteneur d’ancrage. 
- [Chargeur en masse FHIR](https://github.com/microsoft/fhir-loader) Cet outil est une application de fonction Azure (microservice) et s’exécute dans des threads parallèles.
- [Importation en bloc](https://github.com/microsoft/fhir-server/blob/main/docs/BulkImport.md) Cet outil fonctionne uniquement avec le serveur FHIR Open source. Toutefois, il sera disponible pour les API Azure Healthcare à l’avenir.

### <a name="cms-search-profile-validation-and-reindex"></a>CMS, recherche, validation de profil et réindexation

Vous trouverez plus d’informations sur l’interopérabilité et l’accès aux patients, la recherche, la validation des profils et la réindexation dans la documentation du [service FHIR](overview.md) .

### <a name="export-data"></a>Exporter des données

si vous le souhaitez, vous pouvez exporter des données ($export) vers [stockage Azure](../data-transformation/export-data.md) et les utiliser dans vos projets d’analyse ou d’apprentissage automatique. Vous pouvez exporter les données « en l’or » ou en [-ID](../data-transformation/de-identified-export.md) dans le `ndjson` format. 

Vous pouvez également exporter des données vers [Synapse](../data-transformation/move-to-synapse.md) à l’aide du projet open source. À l’avenir, cette fonctionnalité sera intégrée au service géré.

### <a name="converting-data"></a>Conversion des données

Si vous le souhaitez, vous pouvez convertir [HL7 V2](../data-transformation/convert-data.md) et d’autres données de format en FHIR.

### <a name="using-fhir-data-in-power-bi-dashboard"></a>utilisation des données FHIR dans le tableau de bord Power BI

si vous le souhaitez, vous pouvez créer Power BI des rapports de tableau de bord avec des données FHIR.

- [Connecteur Power Query pour FHIR](https://docs.microsoft.com/power-query/connectors/fhir/fhir)
- [Connecteur IoT et Power BI Microsoft](../iot/iot-connector-power-bi.md)

## <a name="next-steps"></a>Étapes suivantes

Cet article a décrit les étapes de base pour commencer à utiliser le service FHIR. Pour plus d’informations sur le déploiement du service FHIR dans l’espace de travail, consultez.

>[!div class="nextstepaction"]
>[Déployer un service FHIR dans les API de santé Azure](fhir-portal-quickstart.md)
