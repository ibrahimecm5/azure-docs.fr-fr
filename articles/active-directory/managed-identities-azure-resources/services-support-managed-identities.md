---
title: Services Azure qui prennent en charge les identités managées - Azure AD
description: Liste des services qui prennent en charge les identités managées pour ressources Azure et l’authentification Azure AD
services: active-directory
author: barclayn
ms.author: barclayn
ms.date: 10/26/2021
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: references_regions
ms.openlocfilehash: 43b4fcdbd867a2aee95c6bf23d57e77e8fbbe08f
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132026897"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Services Azure qui prennent en charge les identités managées pour ressources Azure

Les identités managées pour ressources Azure fournissent automatiquement aux services Azure une identité managée dans Azure Active Directory. Avec une identité gérée, vous pouvez vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. L’intégration des identités managées pour ressources Azure et de l’authentification Azure AD sur Azure est en cours. Vérifiez régulièrement cette page si des mises à jour sont disponibles.

> [!NOTE]
> Identités managées pour les ressources Azure est le nouveau nom du service anciennement nommé Managed Service Identity (MSI).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Services Azure qui prennent en charge les identités gérées pour les ressources Azure

Les services Azure prenant en charge les identités managées pour les ressources Azure sont les suivants :

### <a name="azure-api-management"></a>Gestion des API Azure

Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Attribuée par le système | ![Disponible][check] | ![Disponible][check] | Non disponible | ![Disponible][check] |
| Attribuée par l'utilisateur | ![Disponible][check] | ![Disponible][check] | Non disponible | ![Disponible][check] |

Reportez-vous à la liste suivante pour configurer l'identité managée du service de gestion des API Azure (dans les régions où il est disponible) :

- [Modèle Azure Resource Manager](../../api-management/api-management-howto-use-managed-service-identity.md)

### <a name="azure-app-configuration"></a>Azure App Configuration

| Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Attribuée par le système | ![Disponible][check] | ![Disponible][check] | Non disponible | ![Disponible][check] |
| Attribuée par l'utilisateur | ![Disponible][check] | ![Disponible][check]  | Non disponible  | ![Disponible][check] |

Reportez-vous à la liste suivante pour configurer l’identité managée d’Azure App Configuration (dans les régions où il est disponible) :

- [Azure CLI](../../azure-app-configuration/overview-managed-identity.md)

### <a name="azure-app-service"></a>Azure App Service

| Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Attribuée par le système | ![Disponible][check] | ![Disponible][check] | ![Disponible][check] | ![Disponible][check] |
| Attribuée par l'utilisateur | ![Disponible][check] | ![Disponible][check]  | ![Disponible][check]  | ![Disponible][check] |

Reportez-vous à la liste suivante pour configurer l'identité managée d'Azure App Service (dans les régions où il est disponible) :

- [Azure portal](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [Azure CLI](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Modèle Azure Resource Manager](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-arc-enabled-kubernetes"></a>Kubernetes avec Azure Arc

| Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Attribuée par le système | PRÉVERSION | Non disponible | Non disponible | Non disponible |
| Attribuée par l'utilisateur | Non disponible | Non disponible | Non disponible | Non disponible |

Kubernetes avec Azure Arc [prend actuellement en charge l’identité affectée par le système](../../azure-arc/kubernetes/quickstart-connect-cluster.md). Le certificat d’identité de service managée est utilisé par tous les agents Kubernetes avec Azure Arc à des fins de communication avec Azure.

### <a name="azure-arc-enabled-servers"></a>Serveurs avec Azure Arc

| Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Attribuée par le système | ![Disponible][check] | ![Disponible][check] | Non disponible | Non disponible |
| Attribuée par l'utilisateur | Non disponible | Non disponible | Non disponible | Non disponible |

Tous les serveurs compatibles avec Azure Arc ont une identité attribuée par le système. Vous ne pouvez pas désactiver ou modifier l'identité attribuée par le système sur un serveur compatible avec Azure Arc. Reportez-vous aux ressources suivantes pour en savoir plus sur l’utilisation des identités managées sur des serveurs avec Azure Arc :

- [S’authentifier auprès de ressources Azure au moyen de serveurs avec Azure Arc](../../azure-arc/servers/managed-identity-authentication.md)
- [Utilisation d’une identité managée sur les serveurs avec Azure Arc](../../azure-arc/servers/security-overview.md#using-a-managed-identity-with-azure-arc-enabled-servers)

### <a name="azure-automanage"></a>Azure Automanage

| Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Attribuée par le système | PRÉVERSION | Non disponible | Non disponible | Non disponible |
| Attribuée par l'utilisateur | Non disponible | Non disponible | Non disponible | Non disponible |

Reportez-vous au document suivant pour reconfigurer une identité managée si vous avez déplacé votre abonnement dans un nouveau locataire :

* [Réparer un compte Automanage endommagé](../../automanage/repair-automanage-account.md)

### <a name="azure-automation"></a>Azure Automation

| Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Attribuée par le système | ![Disponible][Check]| ![Disponible][Check] | Non disponible | ![Disponible][Check] |
| Attribuée par l'utilisateur | ![Disponible][Check] | ![Disponible][Check] | Non disponible | ![Disponible][Check] |

Reportez-vous aux documents suivants pour utiliser une identité managée avec [Azure Automation](../../automation/automation-intro.md) :

* [Vue d’ensemble de l’authentification des comptes Automation - Identités managées](../../automation/automation-security-overview.md#managed-identities)
* [Activer et utiliser l’identité managée pour Automation](../../automation/enable-managed-identity-for-automation.md)

### <a name="azure-blueprints"></a>Azure Blueprints

|Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Attribuée par le système | ![Disponible][check] | ![Disponible][check] | Non disponible | Non disponible |
| Attribuée par l'utilisateur | ![Disponible][check] | ![Disponible][check] | Non disponible | Non disponible |

Reportez-vous à la liste suivante pour utiliser une identité managée avec [Azure Blueprints](../../governance/blueprints/overview.md) :

- [Portail Azure : attribution de blueprint](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [API REST : attribution de blueprint](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)


### <a name="azure-cognitive-search"></a>Recherche cognitive Azure

Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Attribuée par le système | ![Disponible][check] | ![Disponible][check] | Non disponible | ![Disponible][check] |
| Attribuée par l'utilisateur | Non disponible | Non disponible | Non disponible | Non disponible |

### <a name="azure-cognitive-services"></a>Azure Cognitive Services

Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Attribuée par le système | ![Disponible][check] | ![Disponible][check] | Non disponible | ![Disponible][check] |
| Attribuée par l'utilisateur | Non disponible | Non disponible | Non disponible | Non disponible |

### <a name="azure-container-instances"></a>Azure Container Instances

Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Attribuée par le système | Linux : PRÉVERSION<br>Windows : Non disponible | Non disponible | Non disponible | Non disponible |
| Attribuée par l'utilisateur | Linux : PRÉVERSION<br>Windows : Non disponible | Non disponible | Non disponible | Non disponible |

Reportez-vous à la liste suivante pour configurer l'identité managée du service Azure Container Instances (dans les régions où il est disponible) :

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Modèle Azure Resource Manager](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)


### <a name="azure-container-registry-tasks"></a>Tâches Azure Container Registry

Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Attribuée par le système | ![Disponible][check] | PRÉVERSION | Non disponible | PRÉVERSION |
| Attribuée par l'utilisateur | PRÉVERSION | PRÉVERSION | Non disponible | PRÉVERSION |

Reportez-vous à la liste suivante pour configurer une identité managée pour Azure Container Registry Tasks (dans les régions où il est disponible) :

- [Azure CLI](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

### <a name="azure-data-explorer"></a>Explorateur de données Azure

Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Attribuée par le système | ![Disponible][check] | ![Disponible][check] | Non disponible | ![Disponible][check] |
| Attribuée par l'utilisateur | Non disponible | Non disponible | Non disponible | Non disponible |

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Attribuée par le système | ![Disponible][check] | ![Disponible][check] | Non disponible | ![Disponible][check] |
| Attribuée par l'utilisateur | Non disponible | Non disponible | Non disponible | Non disponible |

Reportez-vous à la liste suivante pour configurer l'identité managée du service Azure Data Factory V2 (dans les régions où il est disponible) :

- [Azure portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)

### <a name="azure-digital-twins"></a>Azure Digital Twins

Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Attribuée par le système | ![Disponible][check] | Non disponible | Non disponible | Non disponible |
| Attribuée par l'utilisateur | Non disponible | Non disponible | Non disponible | Non disponible |

Reportez-vous à la liste suivante pour configurer l’identité managée du service Azure Digital Twins (dans les régions où il est disponible) :

- [Azure portal](../../digital-twins/how-to-route-with-managed-identity.md)

### <a name="azure-event-grid"></a>Azure Event Grid

Type d'identité managée |Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Attribuée par le système | PRÉVERSION | PRÉVERSION | Non disponible | PRÉVERSION |
| Attribuée par l'utilisateur | PRÉVERSION | PRÉVERSION | Non disponible | PRÉVERSION |

### <a name="azure-firewall-policy"></a>Stratégie de Pare-feu Azure

Type d'identité managée |Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Attribuée par le système | Non disponible | Non disponible | Non disponible | Non disponible |
| Attribuée par l'utilisateur | PRÉVERSION | Non disponible  | Non disponible  | Non disponible |

### <a name="azure-functions"></a>Azure Functions

Type d'identité managée |Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Attribuée par le système | ![Disponible][check] | ![Disponible][check] | ![Disponible][check] | ![Disponible][check] |
| Attribuée par l'utilisateur | ![Disponible][check] | ![Disponible][check]  | ![Disponible][check]  | ![Disponible][check]  |

Reportez-vous à la liste suivante pour configurer l'identité managée du service Azure Functions (dans les régions où il est disponible) :

- [Azure portal](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [Azure CLI](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Modèle Azure Resource Manager](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-iot-hub"></a>Azure IoT Hub

Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Attribuée par le système | ![Disponible][check] | ![Disponible][check] | Non disponible | ![Disponible][check] |
| Attribuée par l'utilisateur | ![Disponible][check] | Non disponible | Non disponible | Non disponible |

Reportez-vous à la liste suivante pour configurer l’identité managée du service Azure IoT Hub (dans les régions où il est disponible) :

- Pour plus d’informations, consultez [Prise en charge d’Azure IoT Hub pour les identités managées](../../iot-hub/iot-hub-managed-identity.md).

### <a name="azure-importexport"></a>Azure Import/Export

Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Attribuée par le système | Disponible dans la région où le service d’importation/exportation Azure est disponible | PRÉVERSION | Disponible | Disponible |
| Attribuée par l'utilisateur | Non disponible | Non disponible | Non disponible | Non disponible |

### <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

| Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Attribuée par le système | ![Disponible][check] | ![Disponible][check] | Non disponible | Non disponible |
| Attribuée par l'utilisateur | PRÉVERSION | ![Disponible][check] | Non disponible | Non disponible |


Pour plus d’informations, voir [Utiliser les identités managées dans Azure Kubernetes Service](../../aks/use-managed-identity.md).

### <a name="azure-log-analytics-cluster"></a>Cluster Azure Log Analytics

Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Attribuée par le système | ![Disponible][check] | ![Disponible][check] | Non disponible | ![Disponible][check] |
| Attribuée par l'utilisateur | ![Disponible][check] | ![Disponible][check] | Non disponible | ![Disponible][check] |

Pour plus d’informations, consultez la page sur le [fonctionnement des identités dans Azure Monitor](../../azure-monitor/logs/customer-managed-keys.md)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Attribuée par le système | ![Disponible][check] | ![Disponible][check] | Non disponible | ![Disponible][check] |
| Attribuée par l'utilisateur | ![Disponible][check] | ![Disponible][check] | Non disponible | ![Disponible][check] |


Reportez-vous à la liste suivante pour configurer l'identité managée du service Azure Logic Apps (dans les régions où il est disponible) :

- [Azure portal](../../logic-apps/create-managed-service-identity.md#enable-system-assigned-identity-in-azure-portal)
- [Modèle Azure Resource Manager](../../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)

### <a name="azure-machine-learning"></a>Azure Machine Learning

Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Attribuée par le système | PRÉVERSION | Non disponible | Non disponible | Non disponible |
| Attribuée par l'utilisateur | PRÉVERSION | Non disponible | Non disponible | Non disponible |

Pour plus d’informations, consultez [Utiliser les identités managées avec Azure Machine Learning](../../machine-learning/how-to-use-managed-identities.md).

### <a name="azure-media-services"></a>Azure Media Services

| Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Attribuée par le système | ![Disponible][check] | ![Disponible][check] | Non disponible | ![Disponible][check] |
| Attribuée par l'utilisateur | Non disponible  | Non disponible  | Non disponible  | Non disponible  |

Reportez-vous à la liste suivante pour configurer l’identité managée d’Azure Media Services (dans les régions où il est disponible) :

- [Azure CLI](../../media-services/latest/security-access-storage-managed-identity-cli-tutorial.md)

### <a name="azure-policy"></a>Azure Policy

|Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Attribuée par le système | ![Disponible][check] | ![Disponible][check] | ![Disponible][check] | ![Disponible][check] |
| Attribuée par l'utilisateur | Non disponible | Non disponible | Non disponible | Non disponible |

Reportez-vous à la liste suivante pour configurer l’identité managée du service Azure Policy (dans les régions où il est disponible) :

- [Azure portal](../../governance/policy/tutorials/create-and-manage.md#assign-a-policy)
- [PowerShell](../../governance/policy/how-to/remediate-resources.md#create-managed-identity-with-powershell)
- [Azure CLI](/cli/azure/policy/assignment#az_policy_assignment_create)
- [Modèles Microsoft Azure Resource Manager](/azure/templates/microsoft.authorization/policyassignments)
- [REST](/rest/api/policy/policyassignments/create)


### <a name="azure-service-fabric"></a>Azure Service Fabric

La fonctionnalité [Identité managée pour les applications Service Fabric](../../service-fabric/concepts-managed-identity.md) est disponible dans toutes les régions.

Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Attribuée par le système | ![Disponible][check] | Non disponible | Non disponible | Non disponible |
| Attribuée par l'utilisateur | ![Disponible][check] | Non disponible | Non disponible |Non disponible |

Reportez-vous à la liste suivante pour configurer l’identité managée pour les applications Azure Service Fabric dans toutes les régions :

- [Modèle Azure Resource Manager](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)

### <a name="azure-spring-cloud"></a>Azure Spring Cloud

| Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Attribuée par le système | ![Disponible][check] | Non disponible | Non disponible | ![Disponible][check] |
| Attribuée par l'utilisateur | Non disponible | Non disponible | Non disponible | Non disponible |


Pour plus d’informations, consultez [Activer une identité managée affectée par le système pour des applications dans Azure Spring Cloud](../../spring-cloud/how-to-enable-system-assigned-managed-identity.md).

### <a name="azure-stack-edge"></a>Azure Stack Edge

Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Attribuée par le système | Disponible dans la région où le service Azure Stack Edge est disponible | Non disponible | Non disponible | Non disponible |
| Attribuée par l'utilisateur | Non disponible | Non disponible | Non disponible | Non disponible |

### <a name="azure-virtual-machine-scale-sets"></a>Groupes de machines virtuelles identiques Azure

|Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Attribuée par le système | ![Disponible][check] | ![Disponible][check] | ![Disponible][check] | ![Disponible][check] |
| Attribuée par l'utilisateur | ![Disponible][check] | ![Disponible][check] | ![Disponible][check] | ![Disponible][check] |

Reportez-vous à la liste suivante pour configurer l'identité managée des groupes Azure Virtual Machine Scale Sets (dans les régions où ils sont disponibles) :

- [Azure portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Modèles Microsoft Azure Resource Manager](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)



### <a name="azure-virtual-machines"></a>Machines virtuelles Azure

| Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Attribuée par le système | ![Disponible][check] | ![Disponible][check] | ![Disponible][check] | ![Disponible][check] |
| Attribuée par l'utilisateur | ![Disponible][check] | ![Disponible][check] | ![Disponible][check] | ![Disponible][check] |

Reportez-vous à la liste suivante pour configurer l'identité managée des machines virtuelles Azure (dans les régions où elles sont disponibles) :

- [Azure portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Modèles Microsoft Azure Resource Manager](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)
- [Kits Azure SDK](qs-configure-sdk-windows-vm.md)


### <a name="azure-vm-image-builder"></a>Azure VM Image Builder

| Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Attribuée par le système | Non disponible | Non disponible | Non disponible | Non disponible |
| Attribuée par l'utilisateur | [Disponible dans les régions prises en charge](../../virtual-machines/image-builder-overview.md#regions) | Non disponible | Non disponible | Non disponible |

Pour savoir comment configurer l’identité managée pour Azure VM Image Builder (dans les régions où le service est disponible), consultez la [présentation d’Image Builder](../../virtual-machines/image-builder-overview.md#permissions).
### <a name="azure-signalr-service"></a>Service Azure SignalR

Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Attribuée par le système | PRÉVERSION | PRÉVERSION | Non disponible | PRÉVERSION |
| Attribuée par l'utilisateur | PRÉVERSION | PRÉVERSION | Non disponible | PRÉVERSION |

Reportez-vous à la liste suivante pour configurer l’identité managée d’Azure SignalR Service (dans les régions où il est disponible) :

- [Modèle Azure Resource Manager](../../azure-signalr/howto-use-managed-identity.md)

### <a name="azure-resource-mover"></a>Azure Resource Mover

Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Attribuée par le système | Disponible dans les régions où le service Azure Resource Mover est disponible | Non disponible | Non disponible | Non disponible |
| Attribuée par l'utilisateur | Non disponible | Non disponible | Non disponible | Non disponible |

Reportez-vous au document suivant pour utiliser Azure Resource Mover :

- [Azure Resource Mover](../../resource-mover/overview.md)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Services Azure qui prennent en charge l’authentification Azure AD

Les services suivants prennent en charge l’authentification Azure AD et ont été testés avec des services client qui utilisent des identités managées pour ressources Azure.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Reportez-vous à la liste suivante pour configurer l’accès à Azure Resource Manager :

- [Attribuer l’accès via le Portail Azure](howto-assign-access-portal.md)
- [Attribuer l’accès via PowerShell](howto-assign-access-powershell.md)
- [Attribuer l’accès via Azure CLI](howto-assign-access-CLI.md)
- [Attribuer l’accès via le modèle Azure Resource Manager](../../role-based-access-control/role-assignments-template.md)

| Cloud | ID de ressource | Statut |
|--------|------------|:-:|
| Azure Global | `https://management.azure.com/`| ![Disponible][check] |
| Azure Government | `https://management.usgovcloudapi.net/` | ![Disponible][check] |
| Azure Germany | `https://management.microsoftazure.de/` | ![Disponible][check] |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | ![Disponible][check] |

### <a name="azure-key-vault"></a>Azure Key Vault

| Cloud | ID de ressource | Statut |
|--------|------------|:-:|
| Azure Global | `https://vault.azure.net`| ![Disponible][check] |
| Azure Government | `https://vault.usgovcloudapi.net` | ![Disponible][check] |
| Azure Germany |  `https://vault.microsoftazure.de` | ![Disponible][check] |
| Azure China 21Vianet | `https://vault.azure.cn` | ![Disponible][check] |

### <a name="azure-data-lake"></a>Azure Data Lake

| Cloud | ID de ressource | Statut |
|--------|------------|:-:|
| Azure Global | `https://datalake.azure.net/` | ![Disponible][check] |
| Azure Government |  | Non disponible |
| Azure Germany |   | Non disponible |
| Azure China 21Vianet |  | Non disponible |

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

| Cloud | ID de ressource | Statut |
|--------|------------|:-:|
| Azure Global | `https://<account>.documents.azure.com/`<br/><br/>`https://cosmos.azure.com` | ![Disponible][check] |
| Azure Government | `https://<account>.documents.azure.us/`<br/><br/>`https://cosmos.azure.us` | ![Disponible][check] |
| Azure Germany | `https://<account>.documents.microsoftazure.de/`<br/><br/>`https://cosmos.microsoftazure.de` | ![Disponible][check] |
| Azure China 21Vianet | `https://<account>.documents.azure.cn/`<br/><br/>`https://cosmos.azure.cn` | ![Disponible][check] |

### <a name="azure-sql"></a>Azure SQL

| Cloud | ID de ressource | Statut |
|--------|------------|:-:|
| Azure Global | `https://database.windows.net/` | ![Disponible][check] |
| Azure Government | `https://database.usgovcloudapi.net/` | ![Disponible][check] |
| Azure Germany | `https://database.cloudapi.de/` | ![Disponible][check] |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | ![Disponible][check] |

### <a name="azure-data-explorer"></a>Explorateur de données Azure

| Cloud | ID de ressource | Statut |
|--------|------------|:-:|
| Azure Global | `https://<account>.<region>.kusto.windows.net` | ![Disponible][check] |
| Azure Government | `https://<account>.<region>.kusto.usgovcloudapi.net` | ![Disponible][check] |
| Azure Germany | `https://<account>.<region>.kusto.cloudapi.de` | ![Disponible][check] |
| Azure China 21Vianet | `https://<account>.<region>.kusto.chinacloudapi.cn` | ![Disponible][check] |

### <a name="azure-event-hubs"></a>Hubs d'événements Azure

| Cloud | ID de ressource | Statut |
|--------|------------|:-:|
| Azure Global | `https://eventhubs.azure.net` | ![Disponible][check] |
| Azure Government | `https://eventhubs.azure.net` | ![Disponible][check] |
| Azure Germany | `https://eventhubs.azure.net` | ![Disponible][check] |
| Azure China 21Vianet | `https://eventhubs.azure.net` | ![Disponible][check] |

### <a name="azure-service-bus"></a>Azure Service Bus

| Cloud | ID de ressource | Statut |
|--------|------------|:-:|
| Azure Global | `https://servicebus.azure.net`  | ![Disponible][check] |
| Azure Government | `https://servicebus.azure.net`  | ![Disponible][check] |
| Azure Germany |  `https://servicebus.azure.net`  | ![Disponible][check] |
| Azure China 21Vianet | `https://servicebus.azure.net`  | ![Disponible][check] |


### <a name="azure-storage-blobs-and-queues"></a>Objets blob et files d’attente Stockage Azure

| Cloud | ID de ressource | Statut |
|--------|------------|:-:|
| Azure Global | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | ![Disponible][check] |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | ![Disponible][check] |
| Azure Germany | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | ![Disponible][check] |
| Azure China 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | ![Disponible][check] |

### <a name="azure-analysis-services"></a>Azure Analysis Services

| Cloud | ID de ressource | Statut |
|--------|------------|:-:|
| Azure Global | `https://*.asazure.windows.net` | ![Disponible][check] |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | ![Disponible][check] |
| Azure Germany | `https://*.asazure.cloudapi.de` | ![Disponible][check] |
| Azure China 21Vianet | `https://*.asazure.chinacloudapi.cn` | ![Disponible][check] |

### <a name="azure-communication-services"></a>Azure Communication Services

Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Attribuée par le système | ![Disponible][check] | Non disponible | Non disponible | Non disponible |
| Attribuée par l'utilisateur | ![Disponible][check] | Non disponible | Non disponible | Non disponible |

> [!NOTE]
> Vous pouvez utiliser une identité managée pour authentifier un [travail Azure Stream Analytics dans Power BI](../../stream-analytics/powerbi-output-managed-identity.md).


[check]: media/services-support-managed-identities/check.png "Disponible"
