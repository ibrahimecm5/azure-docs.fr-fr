---
title: Utiliser un kit de développement logiciel (SDK) pour configurer des identités sur une machine virtuelle - Azure AD
description: Instructions détaillées pour configurer et utiliser des identités managées pour ressources Azure sur une machine virtuelle Azure en utilisant un SDK Azure.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/03/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 555ef4456f0aa77aaeb54981e214666a0f87a19a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131440697"
---
# <a name="configure-a-vm-with-managed-identities-for-azure-resources-using-an-azure-sdk"></a>Configurer une machine virtuelle avec des identités managées pour ressources Azure en utilisant un SDK Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Les identités managées pour ressources Azure fournissent des services Azure avec une identité managée automatiquement dans Azure Active Directory (AD). Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. 

Dans cet article, vous allez découvrir comment activer et supprimer des identités managées pour ressources Azure sur une machine virtuelle Azure en utilisant un SDK Azure.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-managed-identities-for-azure-resources-support"></a>SDK Azure prenant en charge les identités managées pour ressources Azure 

Azure prend en charge plusieurs plates-formes de programmation via une série de [Kits de développement logiciel (SDK) Azure](https://azure.microsoft.com/downloads). Plusieurs d’entre eux ont été mis à jour pour prendre en charge les identités managées pour ressources Azure, et fournissent des exemples appropriés pour en illustrer l’utilisation. Cette liste a été mise à jour, suite à l’ajout d’une prise en charge supplémentaire :

| Kit SDK | Exemple |
| --- | ------ | 
| .NET   | [Gérer une ressource à partir d’une machine virtuelle activée avec des identités managées pour ressources Azure](https://github.com/Azure-Samples/aad-dotnet-manage-resources-from-vm-with-msi) |
| Java   | [Gérer un stockage à partir d’une machine virtuelle activée avec des identités managées pour ressources Azure](https://github.com/Azure-Samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group)|
| Node.js| [Créer une machine virtuelle avec une identité managée affectée par le système activée](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [Créer une machine virtuelle avec une identité managée affectée par le système activée](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Créer une machine virtuelle Azure avec une identité affectée par le système activée](https://github.com/Azure-Samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment utiliser également le portail Azure, PowerShell, CLI et des modèles de ressources, voir les articles à ce propos répertoriés dans la section **Configurer une identité pour une machine virtuelle Azure**.
