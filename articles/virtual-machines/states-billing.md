---
title: États et statut de facturation des machines virtuelles Azure
description: Vue d’ensemble des différents états dans lesquels une machine virtuelle peut entrer et des moments où un utilisateur est facturé.
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/8/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.openlocfilehash: 38c43b168e4524529139dcb5c0807d8563e484eb
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132025814"
---
# <a name="states-and-billing-status-of-azure-virtual-machines"></a>États et statut de facturation des machines virtuelles Azure

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

Les machines virtuelles Microsoft Azure passent par différents états qui peuvent être classés en états *d’approvisionnement* et *d’alimentation*. Cet article vise à décrire ces états et en particulier à mettre en exergue la date de facturation des clients pour l’utilisation des instances. 

## <a name="get-states-using-instance-view"></a>Obtenir des états à l’aide de la vue d’instance

L’API de vue d’instance fournit des informations sur l’état d’exécution d’une machine virtuelle. Pour plus d’informations, consultez la documentation relative à l’API [Virtual Machines - Instance View](/rest/api/compute/virtualmachines/instanceview) (Machines virtuelles : vue d’instance).

Azure Resource Explorer présente une interface utilisateur simple permettant d’afficher l’état d’exécution des machines virtuelles : [Resource Explorer](https://resources.azure.com/).

L’état d’approvisionnement de la machine virtuelle est disponible (sous une forme légèrement différente) dans les propriétés de la machine virtuelle `provisioningState` et l’InstanceView. Dans l’InstanceView de la machine virtuelle, un élément figure dans le tableau `status` sous la forme de `ProvisioningState/<state>[/<errorCode>]`.

Pour récupérer l’état d’alimentation de toutes les machines virtuelles de votre abonnement, utilisez l’[API Machines virtuelles - Répertorier tout](/rest/api/compute/virtualmachines/listall) avec le paramètre **statusOnly** défini sur *true*.

> [!NOTE]
> [Machines virtuelles - Lister toutes les API](/rest/api/compute/virtualmachines/listall) avec le paramètre **statusOnly** défini sur true permet de récupérer l’état d’alimentation de toutes les machines virtuelles de votre abonnement. Toutefois, dans de rares cas, l’état d’alimentation peut ne pas être disponible en raison de problèmes intermittents dans le processus de récupération. Dans de telles situations, nous vous recommandons d’effectuer une nouvelle tentative avec la même API, ou en utilisant [Azure Resource Health](../service-health/resource-health-overview.md) pour vérifier l’état d’alimentation de vos machines virtuelles.
 
## <a name="power-states-and-billing"></a>États d’alimentation et facturation

L’état d’alimentation représente le dernier état connu de la machine virtuelle.

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/vm-power-states.png" alt-text="Image montrant un diagramme des états d’alimentation par lesquels une machine virtuelle peut passer.":::

Le tableau suivant contient une description de l’état de chaque instance et indique si elle est facturée pour utilisation ou non.

| État d’alimentation | Description | Facturation |  
|---|---|---|
| Démarrage en cours| La machine virtuelle est en cours de mise sous tension. | Facturation | 
| Exécution en cours | La machine virtuelle est entièrement opérationnelle. Il s’agit de l’état de fonctionnement standard. | Facturation | 
| En cours d’arrêt | Il s’agit d’un état transitoire entre l’exécution et l’arrêt. | Facturation| 
|Arrêté | La Machine virtuelle est allouée sur un ordinateur hôte, mais pas en cours d’exécution. Egalement appelé état PoweredOff ou *Arrêté (Alloué)* . Cela peut être le résultat de l’appel de l’opération de l’API hors tension ou de l’appel de l’arrêt depuis le système d’exploitation invité. L’état Arrêté peut également être observé brièvement lors de la création de la machine virtuelle ou lors du démarrage d’une machine virtuelle à partir de l’état Désalloué.  | Facturation | 
| Libération | Il s’agit de l’état transitoire entre l’exécution et la désallocation. | Pas de facturation* | 
| Libéré | La machine virtuelle a libéré le bail sur le matériel sous-jacent et est entièrement mise hors tension. Cet état est également appelé *Arrêté (désalloué)* . | Pas de facturation* | 


**Exemple de PowerState en JSON**

```json
        {
          "code": "PowerState/running",
          "level": "Info",
          "displayStatus": "VM running"
        }
```

&#42; Certaines ressources Azure, par exemple les [Disques](https://azure.microsoft.com/pricing/details/managed-disks) et le [Réseau](https://azure.microsoft.com/pricing/details/bandwidth/), continueront à impliquer des frais.


## <a name="provisioning-states"></a>États d’approvisionnement

L’état d’approvisionnement est l’état d’une opération de type plan de contrôle initiée par l’utilisateur sur la machine virtuelle. Ces états sont distincts de l’état d’alimentation d’une machine virtuelle.

| État de provisionnement | Description |
|---|---|
| Creating | La machine virtuelle est en cours de création. |
| Mise à jour | La machine virtuelle est en cours de mise à jour vers le dernier modèle. Certains changements apportés à une machine virtuelle et ne concernant pas un modèle, comme le démarrage et le redémarrage, relèvent de l’état de mise à jour. |
| Échec | La dernière opération sur la ressource de machine virtuelle a échoué. | 
| Opération réussie | La dernière opération sur la ressource de machine virtuelle a réussi. | 
| Suppression | La machine virtuelle est en cours de suppression. | 
| Migration | Visible lors de la migration d’Azure Service Manager vers Azure Resource Manager. | 

## <a name="os-provisioning-states"></a>États de provisionnement d’un système d’exploitation
Les états d’approvisionnement d’un système d’exploitation s’appliquent uniquement aux machines virtuelles créées avec une image de système d’exploitation [généralisée](./linux/imaging.md#generalized-images). Les images et les disques [spécialisés](./linux/imaging.md#specialized-images) attachés en tant que disques de système d’exploitation n’affichent pas ces états. L’état d’approvisionnement du système d’exploitation n’est pas indiqué séparément. Il s’agit d’un sous-état de l’état d’approvisionnement dans l’instanceView de la machine virtuelle. Par exemple : `ProvisioningState/creating/osProvisioningComplete`.

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/os-provisioning-states.png" alt-text="Image montrant les états de provisionnement d’un système d’exploitation par lesquels une machine virtuelle peut passer.":::

| États de provisionnement d’un système d’exploitation | Description | 
|---|---|
| OSProvisioningInProgress | La machine virtuelle est en cours d’exécution et l’initialisation (configuration) du système d’exploitation invité est en cours. |
| OSProvisioningComplete | Il s’agit d’un état à courte durée de vie. La machine virtuelle passe rapidement de cet état à l’état **Réussite**. Si les extensions sont toujours en cours d’installation, vous continuez à voir cet état jusqu’à ce que l’opération soit terminée. |
| Opération réussie | Les actions lancées par l’utilisateur se sont terminées. | 
| Échec | Représente une opération ayant échoué. Pour obtenir plus d’informations et connaître les solutions possibles, reportez-vous au code d’erreur. | 

## <a name="troubleshooting-vm-states"></a>Résolution des problèmes d’état de machine virtuelle

Pour résoudre des problèmes spécifiques d’état de machine virtuelle, consultez [Résoudre les problèmes de déploiement de machine virtuelle Windows](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/troubleshoot-deployment-new-vm-windows) et [Résoudre les problèmes de déploiement de machine virtuelle Linux](https://docs.microsoft.comtroubleshoot/azure/virtual-machines/troubleshoot-deployment-new-vm-linux).

Pour plus d’informations sur la résolution des problèmes, consultez la [documentation relative à la résolution des problèmes de machines virtuelles Azure](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/welcome-virtual-machines).


## <a name="next-steps"></a>Étapes suivantes
- Consultez la [Documentation Cost Management and Billing](../cost-management-billing/index.yml).
- Utilisez la [Calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/) pour planifier vos déploiements.
- Découvrez-en plus sur la supervision de votre machine virtuelle en consultant [Supervision des machines virtuelles dans Azure](../azure-monitor/vm/monitor-vm-azure.md).
