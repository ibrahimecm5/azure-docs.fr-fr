---
title: Configurer Azure RBAC pour le service FHIR-API de santé Azure
description: Cet article explique comment configurer Azure RBAC pour FHIR.
author: SteveWohl
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 11/17/2021
ms.author: zxue
ms.openlocfilehash: b43127a483e9935fe2212f85ab730d344815db07
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132814626"
---
# <a name="configure-azure-rbac-for-healthcare-apis"></a>Configurer Azure RBAC pour les API de santé

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Dans cet article, vous allez apprendre à utiliser le [contrôle d’accès en fonction du rôle (RBAC) Azure](../role-based-access-control/index.yml) pour attribuer l’accès au plan de données des API de santé. Un RBAC Azure est la méthode recommandée pour l’attribution de l’accès au plan de données lorsque les utilisateurs du plan de données sont gérés dans le locataire Azure Active Directory associé à votre abonnement Azure.

Vous pouvez effectuer des attributions de rôles à l’aide de l’Portail Azure. Notez que le service FHIR et le service DICOM ont défini des rôles d’application différents. Ajoutez ou supprimez un ou plusieurs rôles pour gérer les contrôles d’accès utilisateur.

## <a name="assign-roles-for-the-fhir-service"></a>Affecter des rôles pour le service FHIR

Pour accorder à des utilisateurs, des principaux de service ou des groupes l’accès au plan de données FHIR, sélectionnez le service FHIR dans la Portail Azure. Sélectionnez **contrôle d’accès (IAM)**, puis sélectionnez l’onglet **attributions de rôles** . Sélectionnez **+ Ajouter**, puis ajouter une **attribution de rôle**.
 
Si l’option attribution de rôle est grisée, demandez à votre administrateur d’abonnement Azure de vous accorder les autorisations pour l’abonnement ou le groupe de ressources, par exemple, « administrateur de l’accès utilisateur ». Pour plus d’informations sur les rôles intégrés Azure, consultez [rôles intégrés Azure](../role-based-access-control/built-in-roles.md).

[![Attribution de rôle de contrôle d’accès. ](fhir/media/rbac/role-assignment.png) ](fhir/media/rbac/role-assignment.png#lightbox)

Dans la sélection de rôle, recherchez l’un des rôles intégrés pour le plan de données FHIR, par exemple, « FHIR Data Contributor ». Vous pouvez choisir d’autres rôles ci-dessous.

* **Lecteur de données FHIR**: peut lire (et Rechercher) des données FHIR.
* **FHIR Data Writer**: peut lire, écrire et supprimer de manière réversible des données FHIR.
* **Exportateur de données FHIR**: peut lire et exporter des données (opérateur $Export).
* **FHIR Data Contributor**: peut effectuer toutes les opérations du plan de données.
* **Convertisseur de données FHIR**: peut utiliser le convertisseur pour effectuer la conversion de données

Dans la section **Sélectionner** , tapez le nom de l’inscription de l’application cliente. Si le nom est trouvé, le nom de l’application est indiqué. Sélectionnez le nom de l’application, puis sélectionnez **Enregistrer**. 

Si l’application cliente est introuvable, vérifiez l’inscription de votre application pour vous assurer que le nom est correct. Assurez-vous que l’application cliente est créée dans le même locataire que le service FHIR dans les API de santé Azure (par le biais du service FHIR) est déployé dans.


[![Sélectionnez attribution de rôle. ](fhir/media/rbac/select-role-assignment.png) ](fhir/media/rbac/select-role-assignment.png#lightbox)

Vous pouvez vérifier l’attribution de rôle en sélectionnant l’onglet **attributions de rôles** dans l’option de menu **contrôle d’accès (IAM)** .
 
## <a name="assign-roles-for-the-dicom-service"></a>Attribuer des rôles pour le service DICOM

Pour accorder à des utilisateurs, des principaux de service ou des groupes l’accès au plan de données DICOM, sélectionnez le **panneau contrôle d’accès (IAM)** . Sélectionnez l’onglet **attributions de rôle** , puis sélectionnez **+ Ajouter**.

[![contrôle d’accès DICOM. ](dicom/media/dicom-access-control.png) ](dicom/media/dicom-access-control.png#lightbox)

Dans la sélection de **rôle** , recherchez l’un des rôles intégrés pour le plan de données DICOM :

[![Ajoutez l’attribution de rôle RBAC. ](dicom/media/rbac-add-role-assignment.png) ](dicom/media/rbac-add-role-assignment.png#lightbox)

Vous pouvez choisir entre les rôles suivants :

* Propriétaire des données DICOM : accès complet aux données DICOM.
* Lecteur de données DICOM : Lisez et recherchez les données DICOM.

Si ces rôles ne suffisent pas à vos besoins, vous pouvez utiliser PowerShell pour créer des rôles personnalisés.  Pour plus d’informations sur la création de rôles personnalisés, consultez [créer un rôle personnalisé à l’aide de Azure PowerShell](../role-based-access-control/custom-roles-powershell.md).

Dans la zone **Sélectionner** , recherchez un utilisateur, un principal de service ou un groupe auquel vous souhaitez affecter le rôle.

> [!NOTE]
> Si vous ne pouvez pas accéder au service FHIR ou DICOM dans votre application ou d’autres outils, vous devrez peut-être attendre quelques minutes supplémentaires pour que l’attribution de rôle se termine dans le système.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à attribuer des rôles Azure pour le service FHIR et le service DICOM. Pour savoir comment accéder aux API de santé à l’aide de la publication, consultez

- [Accès à l’aide du poste](use-postman.md)
- [Accès à l’aide du client REST](using-rest-client.md)
- [Accès à l’aide de la boucle](using-curl.md)
