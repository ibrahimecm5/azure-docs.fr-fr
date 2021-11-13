---
title: Stratégie de prise en charge des clusters Azure Red Hat OpenShift 4
description: Comprendre les exigences de la stratégie de prise en charge pour Red Hat OpenShift 4
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 03/05/2021
ms.openlocfilehash: ae8311da88ec2598417dd248e12fb044bc9dbf38
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130223212"
---
# <a name="azure-red-hat-openshift-support-policy"></a>Stratégie de prise en charge d’Azure Red Hat OpenShift

Certaines configurations pour les clusters Azure Red Hat OpenShift 4 peuvent affecter la prise en charge de votre cluster. Azure Red Hat OpenShift 4 permet aux administrateurs de clusters d’apporter des modifications aux composants de cluster internes, mais les modifications ne sont pas toutes prises en charge. La stratégie de prise en charge ci-dessous partage les modifications qui enfreignent la stratégie et invalident la prise en charge de Microsoft et Red Hat.

> [!NOTE]
> Les fonctionnalités marquées Technology Preview dans OpenShift Container Platform ne sont pas prises en charge dans Azure Red Hat OpenShift.

## <a name="cluster-configuration-requirements"></a>Exigences de configuration de cluster

* Tous les opérateurs de cluster OpenShift doivent rester dans un état managé. La liste des opérateurs de cluster peut être retournée en exécutant `oc get clusteroperators`.
* Le cluster doit comporter au minimum trois nœuds Worker et trois nœuds Manager. Faites en sorte de ne pas avoir de teintes qui empêchent la planification des composants OpenShift. Ne réduisez pas les nœuds Worker du cluster à la valeur zéro, ou tentez un arrêt normal du cluster.
* Ne supprimez pas ou ne modifiez pas les services Prometheus et AlertManager du cluster.
* Ne supprimez pas les règles AlertManager du service.
* Vous ne devez pas supprimer ni modifier les groupes de sécurité réseau.
* Ne supprimez pas ou ne modifiez pas la journalisation du service Azure Red Hat OpenShift (pods MDSD).
* Ne supprimez pas ou ne modifiez pas le secret d’extraction de cluster « arosvc.azurecr.io ».
* Toutes les machines virtuelles du cluster doivent disposer d’un accès Internet sortant direct, du moins aux points de terminaison Azure Resource Manager (ARM), et de la journalisation des services (Geneva).  Aucune forme de proxy HTTPS n’est prise en charge.
* Ne remplacez pas les objets MachineConfig du cluster (par exemple, la configuration kubelet) de quelque manière que ce soit.
* Ne définissez pas d’options unsupportedConfigOverrides. La définition de ces options empêche les mises à niveau de versions mineures.
* Le service Azure Red Hat OpenShift accède à votre cluster par le biais du service de liaison privée.  Ne supprimez pas et ne modifiez pas l’accès au service.
* Les nœuds de calcul non RHCOS ne sont pas pris en charge. Par exemple, vous ne pouvez pas utiliser un nœud de calcul RHEL.
* Ne placez pas de stratégies au sein de votre groupe d’administration ou d’abonnement qui empêchent les SRE d’effectuer une maintenance normale sur le cluster ARO, par exemple en exigeant des étiquettes sur le groupe de ressources de cluster géré par ARO RP.

## <a name="supported-virtual-machine-sizes"></a>Tailles de machine virtuelle prises en charge

Azure Red Hat OpenShift 4 prend en charge les instances de nœuds de travail sur les tailles de machine virtuelle suivantes :

### <a name="general-purpose"></a>Usage général

|Série|Taille|Processeurs virtuels|Mémoire : Gio|
|-|-|-|-|
|Dasv4|Standard_D4as_v4|4|16|
|Dasv4|Standard_D8as_v4|8|32|
|Dasv4|Standard_D16as_v4|16|64|
|Dasv4|Standard_D32as_v4|32|128|
|Dsv3|Standard_D4s_v3|4|16|
|Dsv3|Standard_D8s_v3|8|32|
|Dsv3|Standard_D16s_v3|16|64|
|Dsv3|Standard_D32s_v3|32|128|

### <a name="memory-optimized"></a>Mémoire optimisée

|Série|Taille|Processeurs virtuels|Mémoire : Gio|
|-|-|-|-|
|Esv3|Standard_E4s_v3|4|32|
|Esv3|Standard_E8s_v3|8|64|
|Esv3|Standard_E16s_v3|16|128|
|Esv3|Standard_E32s_v3|32|256|

### <a name="compute-optimized"></a>Optimisé pour le calcul

|Série|Taille|Processeurs virtuels|Mémoire : Gio|
|-|-|-|-|
|Fsv2|Standard_F4s_v2|4|8|
|Fsv2|Standard_F8s_v2|8|16|
|Fsv2|Standard_F16s_v2|16|32|
|Fsv2|Standard_F32s_v2|32|64|

### <a name="master-nodes"></a>Nœuds master

|Série|Taille|Processeurs virtuels|Mémoire : Gio|
|-|-|-|-|
|Dsv3|Standard_D8s_v3|8|32|
|Dsv3|Standard_D16s_v3|16|64|
|Dsv3|Standard_D32s_v3|32|128|

### <a name="day-2-worker-node"></a>Nœud Worker du jour 2
La configuration de machinesets permet de prendre en charge les types d’instances suivants dans une opération de jour 2. Pour plus d’informations sur la création d’un machineset, consultez [Création d’un machineset dans Azure](https://docs.openshift.com/container-platform/4.8/machine_management/creating_machinesets/creating-machineset-azure.html).


|Série|Taille|Processeurs virtuels|Mémoire : Gio|
|-|-|-|-|
|L4s|Standard_L4s|4|32|
|L8s|Standard_L8s|8|64|
|L16s|Standard_L16s|16|128|
|L32s|Standard_L32s|32|256|
|L8s_v2|Standard_L8s_v2|8|64|
|L16s_v2|Standard_L16s_v2|16|128|
|L32s_v2|Standard_L32s_v2|32|256|
|L48s_v2|Standard_L48s_v2|32|384|
|L64s_v2|Standard_L48s_v2|64|512|
