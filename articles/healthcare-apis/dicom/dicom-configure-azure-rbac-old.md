---
title: Configurer Azure RBAC pour le service DICOM-API Azure Healthcare
description: Cet article explique comment configurer Azure RBAC pour le service DICOM
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 07/13/2020
ms.author: aersoy
ms.openlocfilehash: 7b414fcd5c42ccfec48b3e17d8bfe1bad27ec952
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132814625"
---
# <a name="configure-azure-rbac-for-the-dicom-service"></a>Configurer Azure RBAC pour le service DICOM

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale. 

Dans cet article, vous allez apprendre à utiliser le [contrôle d’accès en fonction du rôle (RBAC) Azure](../../role-based-access-control/index.yml) pour attribuer l’accès au service DICOM. 

## <a name="assign-roles"></a>Attribuer des rôles

Pour accorder à des utilisateurs, des principaux de service ou des groupes l’accès au plan de données DICOM, sélectionnez le **panneau contrôle d’accès (IAM)** . Sélectionnez l’onglet **attributions de rôle** , puis sélectionnez **+ Ajouter**.

[![contrôle d’accès DICOM. ](media/dicom-access-control.png) ](media/dicom-access-control.png#lightbox)


Dans la sélection de **rôle** , recherchez l’un des rôles intégrés pour le plan de données DICOM :

[![Ajoutez l’attribution de rôle RBAC. ](media/rbac-add-role-assignment.png) ](media/rbac-add-role-assignment.png#lightbox)

Vous pouvez choisir entre les rôles suivants :

* Propriétaire des données DICOM : accès complet aux données DICOM.
* Lecteur de données DICOM : Lisez et recherchez les données DICOM.

Si ces rôles ne suffisent pas à vos besoins, vous pouvez utiliser PowerShell pour créer des rôles personnalisés.  Pour plus d’informations sur la création de rôles personnalisés, consultez [créer un rôle personnalisé à l’aide de Azure PowerShell](../../role-based-access-control/tutorial-custom-role-powershell.md).

Dans la zone **Sélectionner** , recherchez un utilisateur, un principal de service ou un groupe auquel vous souhaitez affecter le rôle.

## <a name="caching-behavior"></a>Comportement de mise en cache

Le service DICOM met en cache les décisions pendant cinq minutes maximum. Si vous accordez à un utilisateur l’accès au service DICOM en l’ajoutant à la liste des ID d’objet autorisés, ou que vous le supprimez de la liste, vous devez attendre jusqu’à cinq minutes pour que les modifications apportées aux autorisations soient propagées.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à attribuer des rôles Azure pour le plan de données du service DICOM. 
 
>[!div class="nextstepaction"]
>[Vue d’ensemble du service DICOM](dicom-services-overview.md)
