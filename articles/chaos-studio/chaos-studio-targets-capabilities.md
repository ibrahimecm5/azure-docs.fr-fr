---
title: Cibles et fonctionnalités dans Azure Chaos Studio
description: Découvrez comment contrôler l’intégration des ressources dans Azure Chaos Studio à l’aide des cibles et des fonctionnalités.
author: johnkemnetz
ms.author: johnkem
ms.service: chaos-studio
ms.topic: conceptual
ms.date: 11/01/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: cccfcfa60ed0d527fe3bd0dac9db0a6206a95c2f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097043"
---
# <a name="targets-and-capabilities-in-azure-chaos-studio"></a>Cibles et fonctionnalités dans Azure Chaos Studio

Avant de pouvoir injecter une erreur dans une ressource Azure, les cibles et les fonctionnalités correspondantes doivent d’abord être activées pour la ressource. Les cibles et les fonctionnalités contrôlent quelles ressources sont activées pour l’injection d’erreurs, et quelles erreurs peuvent s’exécuter sur ces ressources. À l’aide des cibles et des fonctionnalités, [ainsi que d’autres mesures de sécurité](chaos-studio-permissions-security.md), vous pouvez éviter l’injection accidentelle ou malveillante d’erreurs avec Chaos Studio. Par exemple, avec les cibles et les fonctionnalités, vous pouvez autoriser l’exécution de l’erreur de sollicitation du processeur sur vos machines virtuelles de production tout en empêchant l’exécution de l’erreur d’arrêt de processus sur celles-ci.

## <a name="targets"></a>Cibles

Une **cible** de chaos permet à Chaos Studio d’interagir avec une ressource pour un type de cible particulier. Un **type de cible** représente la méthode d’injection des erreurs sur une ressource. Les types de ressources qui prennent uniquement en charge les erreurs de service direct ont un seul type de cible, par exemple le type `Microsoft-CosmosDB` pour Azure Cosmos DB. Les types de ressources qui prennent en charge les erreurs de service direct et celles basées sur l’agent comprennent deux types de cibles : l’un pour les erreurs de service direct (par exemple, `Microsoft-VirtualMachine`) et l’autre pour les erreurs basées sur l’agent (toujours`Microsoft-Agent`).

Une cible est une ressource d’extension créée en tant qu’enfant de la ressource et qui est intégrée à Chaos Studio (par exemple, une machine virtuelle ou un groupe de sécurité réseau). Une cible définit le type de cible qui est activé sur la ressource. Par exemple, si l’intégration d’une instance de Cosmos DB avec cet ID de ressource :

```
/subscriptions/fd9ccc83-faf6-4121-9aff-2a2d685ca2a2/resourceGroups/chaosstudiodemo/providers/Microsoft.DocumentDB/databaseAccounts/myDB
```

La ressource Cosmos DB aura une ressource enfant se présentant sous la forme suivante :

```
/subscriptions/fd9ccc83-faf6-4121-9aff-2a2d685ca2a2/resourceGroups/chaosstudiodemo/providers/Microsoft.DocumentDB/databaseAccounts/myDB/providers/Microsoft.Chaos/targets/Microsoft-CosmosDB
```

Seules les ressources dotées d’une cible créée à partir d’elles-mêmes peuvent être ciblées pour l’injection d’erreurs avec Chaos Studio.

## <a name="capabilities"></a>Fonctionnalités

Une **fonctionnalité** permet à Chaos Studio d’exécuter une erreur particulière sur une ressource, telles que l’arrêt d’une machine virtuelle. Les fonctionnalités sont uniques pour chaque type de cible et représentent l’erreur qu’elles activent, par exemple, `CPUPressure-1.0`. [Visitez la bibliothèque d’erreurs Chaos Studio](chaos-studio-fault-library.md) pour comprendre toutes les erreurs disponibles ainsi que les noms de leurs fonctionnalités et types de cibles correspondants.

Une fonctionnalité est une ressource d’extension créée en tant qu’enfant d’une cible. Par exemple, si vous activez l’erreur d’arrêt sur une machine virtuelle avec un ID cible de service direct :

```
/subscriptions/fd9ccc83-faf6-4121-9aff-2a2d685ca2a2/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myVM/providers/Microsoft.Chaos/targets/microsoft-virtualMachine
```

La ressource cible aura une ressource enfant se présentant sous la forme suivante :

```
/subscriptions/fd9ccc83-faf6-4121-9aff-2a2d685ca2a2/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myVM/providers/Microsoft.Chaos/targets/microsoft-virtualMachine/capabilities/shutdown-1.0
```

Une expérience peut uniquement injecter des erreurs sur les cibles intégrées avec les fonctionnalités correspondantes activées. 

## <a name="listing-capability-names-and-parameters"></a>Liste des noms de fonctionnalités et des paramètres
À titre de référence, une liste de noms de fonctionnalités, d’URN d’erreur et de paramètres est disponible [dans notre bibliothèque d’erreurs](chaos-studio-fault-library.md), mais vous pouvez utiliser la réponse HTTP pour créer une fonctionnalité, ou effectuer une opération GET sur une fonctionnalité existante pour obtenir ces informations à la demande. Par exemple, en effectuant une opération GET sur une fonctionnalité d’arrêt de machine virtuelle :

```bash
az rest --method get --url "https://management.azure.com/subscriptions/fd9ccc83-faf6-4121-9aff-2a2d685ca2a2/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myVM/providers/Microsoft.Chaos/targets/microsoft-virtualMachine/capabilities/shutdown-1.0?api-version=2021-08-11-preview"
```

Le code JSON suivant est retourné :

```JSON
{
  "id": "/subscriptions/fd9ccc83-faf6-4121-9aff-2a2d685ca2a2/myRG/providers/Microsoft.Compute/virtualMachines/myVM/providers/Microsoft.Chaos/targets/microsoft-virtualMachine/capabilities/shutdown-1.0",
  "name": "shutdown-1.0",
  "properties": {
    "description": null,
    "name": "shutdown-1.0",
    "parametersSchema": "https://schema-tc.eastus.chaos-prod.azure.com/targetTypes/Microsoft-VirtualMachine/capabilityTypes/Shutdown-1.0/parametersSchema.json",
    "publisher": "Microsoft",
    "targetType": "VirtualMachine",
    "type": "shutdown",
    "urn": "urn:csci:microsoft:virtualMachine:shutdown/1.0",
    "version": "1.0"
  },
  "resourceGroup": "myRG",
  "systemData": {
    "createdAt": "2021-09-15T23:00:00.826575+00:00",
    "lastModifiedAt": "2021-09-15T23:00:00.826575+00:00"
  },
  "type": "Microsoft.Chaos/targets/capabilities"
}
```

La propriété `properties.urn` est utilisée pour définir l’erreur que vous souhaitez exécuter dans une expérience de chaos. Pour comprendre le schéma des paramètres de cette erreur, vous pouvez obtenir par GET le schéma référencé par `properties.parametersSchema`.

```bash
az rest --method get --url "https://schema-tc.eastus.chaos-prod.azure.com/targetTypes/Microsoft-VirtualMachine/capabilityTypes/Shutdown-1.0/parametersSchema.json"
```

retourne le code JSON suivant :
```JSON
{
  "$schema": "https://json-schema.org/draft-07/schema",
  "properties": {
    "abruptShutdown": {
      "type": "boolean"
    },
    "restartWhenComplete": {
      "type": "boolean"
    }
  },
  "type": "object"
}
```

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous comprenez ce que sont les cibles et les fonctionnalités, vous êtes prêt à :
- [En savoir plus sur les erreurs et les actions](chaos-studio-faults-actions.md)
- [Créer et exécuter votre première expérience](chaos-studio-tutorial-service-direct.md)
