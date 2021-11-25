---
title: Bibliothèque d’erreurs et d’actions Chaos Studio
description: Découvrez les actions disponibles que vous pouvez utiliser avec Chaos Studio, dont leurs prérequis et paramètres.
services: chaos-studio
author: johnkemnetz
ms.topic: article
ms.date: 11/10/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: ignite-fall-2021
ms.openlocfilehash: 60e9dd18d8c9357f2696ec79be08925cdd7e97da
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132371008"
---
# <a name="chaos-studio-fault-and-action-library"></a>Bibliothèque d’erreurs et d’actions Chaos Studio

Voici les erreurs utilisables aujourd’hui. Pour comprendre les types de ressources pris en charge, visitez la page [Fournisseurs d’erreurs](./chaos-studio-fault-providers.md).

## <a name="time-delay"></a>Temporisation

| Propriété | Value |
|-|-|
| Fournisseur d’erreur | N/A |
| Types de systèmes d’exploitation pris en charge | N/A |
| Description | Ajoute une temporisation avant, entre ou après d’autres actions. Utile pour attendre que l’impact d’une erreur apparaisse dans un service, ou qu’une activité en dehors de l’expérience se termine (par exemple, attendre qu’une réparation automatique se produise avant d’injecter une autre erreur). |
| Prérequis | N/A |
| URN | urn:provider:Azure-chaosStudio:Microsoft.Azure.Chaos.Delay.Timed |
| duration | Durée de la temporisation au format ISO 8601 (exemple : PT10M) |

### <a name="sample-json"></a>Exemple de code JSON

```json
{
  "name": "branchOne",
  "actions": [ 
    {
      "type": "delay",
      "name": "urn:provider:Azure-chaosStudio:Microsoft.Azure.Chaos.Delay.Timed",
      "duration": "PT10M"
    }
  ] 
}
```

## <a name="cpu-pressure"></a>Sollicitation du processeur

| Propriété | Value |
|-|-|
| Nom de fonctionnalité | CPUPressure-1.0 |
| Type cible | Microsoft-Agent |
| Types de systèmes d’exploitation pris en charge | Windows, Linux |
| Description | Ajoutez la sollicitation du processeur jusqu’à la valeur spécifiée sur la machine virtuelle dans laquelle cette erreur est injectée pendant la durée de l’action d’erreur. La sollicitation artificielle du processeur est supprimée à la fin de la durée ou si l’expérience est annulée. |
| Prérequis | **Linux :** l’exécution de l’erreur sur une machine virtuelle Linux nécessite l’installation de l’utilitaire **stress-ng**. Vous pouvez l’installer à l’aide du gestionnaire de package pour votre distribution Linux. </br> Commande APT pour installer stress-ng : *sudo apt-get update && sudo apt-get -y install unzip && sudo apt-get -y install stress-ng* </br> Commande YUM pour installer stress-ng : *sudo dnf -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm && sudo yum -y install stress-ng*  |
| | **Windows :** aucun. |
| URN | urn:csci:microsoft:agent:cpuPressure/1.0 |
| Paramètres (clé, valeur)  |
| pressureLevel | Entier compris entre 1 et 99 indiquant la sollicitation du processeur (%) appliquée à la machine virtuelle. |
| virtualMachineScaleSetInstances | Tableau d’ID d’instance lors de l’application de cette erreur à un groupe de machines virtuelles identiques. Nécessaire pour les groupes de machines virtuelles identiques. |

### <a name="sample-json"></a>Exemple de code JSON
```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:cpuPressure/1.0",
      "parameters": [
        {
          "key": "pressureLevel",
          "value": "95"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

### <a name="notes"></a>Notes
Problèmes connus sur Linux :
1. Il se peut que l’effet de contrainte ne se termine pas correctement en cas d’arrêt inopiné d’AzureChaosAgent.
2. L’erreur de processeur Linux n’est testée que sur Ubuntu 16.04-LTS et Ubuntu 18.04-LTS.

## <a name="physical-memory-pressure"></a>Sollicitation de la mémoire physique

| Propriété | Value |
|-|-|
| Nom de fonctionnalité | PhysicalMemoryPressure-1.0 |
| Type cible | Microsoft-Agent |
| Types de systèmes d’exploitation pris en charge | Windows, Linux |
| Description | Ajoutez la sollicitation de la mémoire physique jusqu’à la valeur spécifiée sur la machine virtuelle dans laquelle cette erreur est injectée pendant la durée de l’action d’erreur. La sollicitation artificielle de la mémoire physique est supprimée à la fin de la durée ou si l’expérience est annulée. |
| Prérequis | **Linux :** l’exécution de l’erreur sur une machine virtuelle Linux nécessite l’installation de l’utilitaire **stress-ng**. Vous pouvez l’installer à l’aide du gestionnaire de package pour votre distribution Linux. </br> Commande APT pour installer stress-ng : *sudo apt-get update && sudo apt-get -y install unzip && sudo apt-get -y install stress-ng* </br> Commande YUM pour installer stress-ng : *sudo dnf -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm && sudo yum -y install stress-ng*  |
| | **Windows :** aucun. |
| URN | urn:csci:microsoft:agent:physicalMemoryPressure/1.0 |
| Paramètres (clé, valeur) |  |
| pressureLevel | Entier compris entre 1 et 99 indiquant la sollicitation de la mémoire physique (%) appliquée à la machine virtuelle. |
| virtualMachineScaleSetInstances | Tableau d’ID d’instance lors de l’application de cette erreur à un groupe de machines virtuelles identiques. Nécessaire pour les groupes de machines virtuelles identiques. |

### <a name="sample-json"></a>Exemple de code JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:physicalMemoryPressure/1.0",
      "parameters": [
        {
          "key": "pressureLevel",
          "value": "95"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="virtual-memory-pressure"></a>Sollicitation de la mémoire virtuelle

| Propriété | Value |
|-|-|
| Nom de fonctionnalité | VirtualMemoryPressure-1.0 |
| Type cible | Microsoft-Agent |
| Types de systèmes d’exploitation pris en charge | Windows |
| Description | Ajoutez la sollicitation de la mémoire virtuelle jusqu’à la valeur spécifiée sur la machine virtuelle dans laquelle cette erreur est injectée pendant la durée de l’action d’erreur. La sollicitation artificielle de la mémoire virtuelle est supprimée à la fin de la durée ou si l’expérience est annulée. |
| Prérequis | Aucun. |
| URN | urn:csci:microsoft:agent:virtualMemoryPressure/1.0 |
| Paramètres (clé, valeur) |  |
| pressureLevel | Entier compris entre 1 et 99 indiquant la sollicitation de la mémoire physique (%) appliquée à la machine virtuelle. |
| virtualMachineScaleSetInstances | Tableau d’ID d’instance lors de l’application de cette erreur à un groupe de machines virtuelles identiques. Nécessaire pour les groupes de machines virtuelles identiques. |

### <a name="sample-json"></a>Exemple de code JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:virtualMemoryPressure/1.0",
      "parameters": [
        {
          "key": "pressureLevel",
          "value": "95"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="disk-io-pressure-windows"></a>Sollicitation des E/S disque (Windows)

| Propriété | Value |
|-|-|
| Nom de fonctionnalité | DiskIOPressure-1.0 |
| Type cible | Microsoft-Agent |
| Types de systèmes d’exploitation pris en charge | Windows |
| Description | Se sert de l’[utilitaire diskspd](https://github.com/Microsoft/diskspd/wiki) pour ajouter la sollicitation du disque au stockage principal de la machine virtuelle dans laquelle elle est injectée pendant la durée de l’action d’erreur. Cette erreur est déclinée en cinq modes d’exécution différents. La sollicitation artificielle du disque est supprimée à la fin de la durée ou si l’expérience est annulée. |
| Prérequis | Aucun. |
| URN | urn:csci:microsoft:agent:diskIOPressure/1.0 |
| Paramètres (clé, valeur) |  |
| pressureMode | Mode prédéfini de sollicitation du disque à ajouter au stockage principal de la machine virtuelle. Doit être l’un des PressureModes dans le tableau ci-dessous. |
| virtualMachineScaleSetInstances | Tableau d’ID d’instance lors de l’application de cette erreur à un groupe de machines virtuelles identiques. Nécessaire pour les groupes de machines virtuelles identiques. |

### <a name="pressure-modes"></a>Modes de pression

| PressureMode | Description |
| -- | -- |
| PremiumStorageP10IOPS | numberOfThreads = 1<br/>randomBlockSizeInKB = 64<br/>randomSeed = 10<br/>numberOfIOperThread = 25<br/>sizeOfBlocksInKB = 8<br/>sizeOfWriteBufferInKB = 64<br/>fileSizeInGB = 2<br/>percentOfWriteActions = 50 |
| PremiumStorageP10Throttling |<br/>numberOfThreads = 2<br/>randomBlockSizeInKB = 64<br/>randomSeed = 10<br/>numberOfIOperThread = 25<br/>sizeOfBlocksInKB = 64<br/>sizeOfWriteBufferInKB = 64<br/>fileSizeInGB = 1<br/>percentOfWriteActions = 50 |
| PremiumStorageP50IOPS | numberOfThreads = 32<br/>randomBlockSizeInKB = 64<br/>randomSeed = 10<br/>numberOfIOperThread = 32<br/>sizeOfBlocksInKB = 8<br/>sizeOfWriteBufferInKB = 64<br/>fileSizeInGB = 1<br/>percentOfWriteActions = 50 |
| PremiumStorageP50Throttling | numberOfThreads = 2<br/>randomBlockSizeInKB = 1024<br/>randomSeed = 10<br/>numberOfIOperThread = 2<br/>sizeOfBlocksInKB = 1024<br/>sizeOfWriteBufferInKB = 1024<br/>fileSizeInGB = 20<br/>percentOfWriteActions = 50|
| Default | numberOfThreads = 2<br/>randomBlockSizeInKB = 64<br/>randomSeed = 10<br/>numberOfIOperThread = 2<br/>sizeOfBlocksInKB = 64<br/>sizeOfWriteBufferInKB = 64<br/>fileSizeInGB = 1<br/>percentOfWriteActions = 50 |

### <a name="sample-json"></a>Exemple de code JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:diskIOPressure/1.0",
      "parameters": [
        {
          "key": "pressureMode",
          "value": "PremiumStorageP10IOPS"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="disk-io-pressure-linux"></a>Sollicitation des E/S disque (Linux)

| Propriété | Value |
|-|-|
| Nom de fonctionnalité | LinuxDiskIOPressure-1.0 |
| Type cible | Microsoft-Agent |
| Types de systèmes d’exploitation pris en charge | Linux |
| Description | Utilise stress-ng pour appliquer la sollicitation au disque. Un ou plusieurs processus Worker sont générés, qui effectuent les processus d’E/S avec des fichiers temporaires. Pour plus d’informations sur l’application de la sollicitation, consultez https://wiki.ubuntu.com/Kernel/Reference/stress-ng. |
| Prérequis | L’exécution de l’erreur sur une machine virtuelle Linux nécessite l’installation de l’utilitaire **stress-ng**. Vous pouvez l’installer à l’aide du gestionnaire de package pour votre distribution Linux. </br> Commande APT pour installer stress-ng : *sudo apt-get update && sudo apt-get -y install unzip && sudo apt-get -y install stress-ng* </br> Commande YUM pour installer stress-ng : *sudo dnf -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm && sudo yum -y install stress-ng* |
| URN | urn:csci:microsoft:agent:linuxDiskIOPressure/1.0 |
| Paramètres (clé, valeur) |  |
| workerCount | Nombre de processus Worker à exécuter. La définition de cette valeur sur 0 génère autant de processus Worker qu’il y a de processeurs. |
| fileSizePerWorker | Taille du fichier temporaire sur lequel un Worker effectue des opérations d’E/S. Entier plus une unité exprimé en octets (b), kilo-octets (k), mégaoctets (m) ou gigaoctets (g) (par exemple, 4m pour 4 mégaoctets, 256g pour 256 gigaoctets) |
| blockSize | Taille de bloc à utiliser pour les opérations d’E/S disque, limitée à 4 mégaoctets. Entier plus une unité exprimé en octets (b), kilo-octets (k) ou mégaoctets (m) (par exemple, 512k pour 512 kilo-octets) |
| virtualMachineScaleSetInstances | Tableau d’ID d’instance lors de l’application de cette erreur à un groupe de machines virtuelles identiques. Nécessaire pour les groupes de machines virtuelles identiques. |

### <a name="sample-json"></a>Exemple de code JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:linuxDiskIOPressure/1.0",
      "parameters": [
        {
          "key": "workerCount",
          "value": "0"
        },
        {
          "key": "fileSizePerWorker",
          "value": "512m"
        },
        {
          "key": "blockSize",
          "value": "256k"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="arbitrary-stress-ng-stress"></a>Contrainte Stress-ng arbitraire

| Propriété | Value |
|-|-|
| Nom de fonctionnalité | StressNg-1.0 |
| Type cible | Microsoft-Agent |
| Types de systèmes d’exploitation pris en charge | Linux |
| Description | Exécutez une commande stress-ng quelconque en passant des arguments directement à stress-ng. Utile quand l’une des erreurs prédéfinies pour stress-ng ne répond pas à vos besoins. |
| Prérequis | L’exécution de l’erreur sur une machine virtuelle Linux nécessite l’installation de l’utilitaire **stress-ng**. Vous pouvez l’installer à l’aide du gestionnaire de package pour votre distribution Linux. </br> Commande APT pour installer stress-ng : *sudo apt-get update && sudo apt-get -y install unzip && sudo apt-get -y install stress-ng* </br> Commande YUM pour installer stress-ng : *sudo dnf -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm && sudo yum -y install stress-ng* |
| URN | urn:csci:microsoft:agent:stressNg/1.0 |
| Paramètres (clé, valeur) |  |
| stressNgArguments | Un ou plusieurs arguments à passer au processus stress-ng. Pour plus d’informations sur les arguments stress-ng possibles, consultez https://wiki.ubuntu.com/Kernel/Reference/stress-ng. |

### <a name="sample-json"></a>Exemple de code JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:stressNg/1.0",
      "parameters": [
        {
          "key": "stressNgArguments",
          "value": "--random 64"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="stop-windows-service"></a>Arrêter le service Windows

| Propriété | Value |
|-|-|
| Nom de fonctionnalité | StopService-1.0 |
| Type cible | Microsoft-Agent |
| Types de systèmes d’exploitation pris en charge | Windows |
| Description | Utilise les API du Contrôleur de services Windows pour arrêter un Service Windows pendant la durée de l’erreur, en le redémarrant à la fin de la durée ou en cas d’annulation de l’expérience. |
| Prérequis | Aucun. |
| URN | urn:csci:microsoft:agent:stopService/1.0 |
| Paramètres (clé, valeur) |  |
| serviceName | Nom du service Windows que vous souhaitez arrêter. Vous pouvez exécuter `sc.exe query` dans une invite de commandes pour explorer les noms de service. Les noms conviviaux des services Windows ne sont pas pris en charge. |
| virtualMachineScaleSetInstances | Tableau d’ID d’instance lors de l’application de cette erreur à un groupe de machines virtuelles identiques. Nécessaire pour les groupes de machines virtuelles identiques. |

### <a name="sample-json"></a>Exemple de code JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:stopService/1.0",
      "parameters": [
        {
          "key": "serviceName",
          "value": "nvagent"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="time-change"></a>Changement d’heure

| Propriété | Value |
|-|-|
| Nom de fonctionnalité | TimeChange-1.0 |
| Type cible | Microsoft-Agent |
| Types de systèmes d’exploitation pris en charge | Windows |
| Description | Modifie l’heure système de la machine virtuelle dans laquelle elle est injectée, et la réinitialise à la fin de la durée ou en cas d’annulation de l’expérience. |
| Prérequis | Aucun. |
| URN | urn:csci:microsoft:agent:timeChange/1.0 |
| Paramètres (clé, valeur) |  |
| dateTime | Chaîne DateTime au [format ISO8601](https://www.cryptosys.net/pki/manpki/pki_iso8601datetime.html). Si les valeurs AAAA-MM-JJ sont manquantes, elles sont définies par défaut sur le jour actuel lors de l’exécution de l’expérience. Si les valeurs Thh:mm:ss sont manquantes, la valeur par défaut est 12:00:00. Si une année à 2 chiffres est fournie (AA), elle est convertie en année à 4 chiffres (AAAA) en fonction du siècle actuel. Si la valeur \<Z\> est manquante, elle est définie par défaut sur le décalage du fuseau horaire local. \<Z\> doit toujours inclure un symbole de signe (négatif ou positif). |
| virtualMachineScaleSetInstances | Tableau d’ID d’instance lors de l’application de cette erreur à un groupe de machines virtuelles identiques. Nécessaire pour les groupes de machines virtuelles identiques. |

### <a name="sample-json"></a>Exemple de code JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:timeChange/1.0",
      "parameters": [
        {
          "key": "dateTime",
          "value": "2038-01-01T03:14:07"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="kill-process"></a>Arrêter le processus

| Propriété | Value |
|-|-|
| Nom de fonctionnalité | KillProcess-1.0 |
| Type cible | Microsoft-Agent |
| Types de systèmes d’exploitation pris en charge | Windows, Linux |
| Description | Arrête toutes les instances en cours d’exécution d’un processus correspondant au nom de processus envoyé dans les paramètres d’erreur. Dans la durée définie pour l’action d’erreur, un processus est arrêté de façon répétée en fonction de la valeur de l’intervalle d’arrêt spécifié. Cette erreur est une erreur destructive dans laquelle l’administrateur système doit récupérer manuellement le processus si une réparation spontanée est configurée pour celui-ci. |
| Prérequis | Aucun. |
| URN | urn:csci:microsoft:agent:killProcess/1.0 |
| Paramètres (clé, valeur) |  |
| processName | Nom d’un processus en cours d’exécution sur une machine virtuelle (sans l’extension .exe) |
| killIntervalInMilliseconds | Durée d’attente de l’erreur entre des tentatives d’arrêt successives, exprimée en millisecondes. |
| virtualMachineScaleSetInstances | Tableau d’ID d’instance lors de l’application de cette erreur à un groupe de machines virtuelles identiques. Nécessaire pour les groupes de machines virtuelles identiques. |

### <a name="sample-json"></a>Exemple de code JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:killProcess/1.0",
      "parameters": [
        {
          "key": "processName",
          "value": "myapp"
        },
        {
          "key": "killIntervalInMilliseconds",
          "value": "1000"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="dns-failure"></a>Échec DNS

| Propriété | Value |
|-|-|
| Nom de fonctionnalité | DnsFailure-1.0 |
| Type cible | Microsoft-Agent |
| Types de systèmes d’exploitation pris en charge | Windows |
| Description | Remplace la réponse d’une demande de recherche DNS par un code d’erreur spécifié. |
| Prérequis | Aucun. |
| URN | urn:csci:microsoft:agent:dnsFailure/1.0 |
| Paramètres (clé, valeur) |  |
| hôtes | Tableau JSON délimité de noms d’hôtes pour lesquels la demande de recherche DNS doit échouer. |
| dnsFailureReturnCode | Code d’erreur DNS à retourner au client pour l’échec de recherche (FormErr, ServFail, NXDomain, NotImp, Refused, XDomain, YXRRSet, NXRRSet, NotAuth, NotZone). Pour plus d’informations sur les codes de retour DNS, visitez [le site web IANA](https://www.iana.org/assignments/dns-parameters/dns-parameters.xml#dns-parameters-6). |
| virtualMachineScaleSetInstances | Tableau d’ID d’instance lors de l’application de cette erreur à un groupe de machines virtuelles identiques. Nécessaire pour les groupes de machines virtuelles identiques. |

### <a name="sample-json"></a>Exemple de code JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:dnsFailure/1.0",
      "parameters": [
        {
          "key": "hosts",
          "value": "[ \"www.bing.com\", \"msdn.microsoft.com\" ]"
        },
        {
          "key": "dnsFailureReturnCode",
          "value": "ServFail"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

### <a name="limitations"></a>Limitations

* L’erreur Échec DNS nécessite Windows 2019 RS5 ou version plus récente.
* Le cache DNS sera ignoré pendant la durée de l’erreur pour les noms d’hôte définis dans l’erreur.

## <a name="network-latency"></a>Latence du réseau

| Propriété | Value |
|-|-|
| Nom de fonctionnalité | NetworkLatency-1.0 |
| Type cible | Microsoft-Agent |
| Types de systèmes d’exploitation pris en charge | Windows |
| Description | Augmente la latence du réseau pour une plage de ports et un bloc réseau spécifiés. |
| Prérequis | L’agent doit être exécuté en tant qu’administrateur. Si l’agent est installé en tant qu’extension de machine virtuelle, il est exécuté en tant qu’administrateur par défaut. |
| URN | urn:csci:microsoft:agent:networkLatency/1.0 |
| Paramètres (clé, valeur) |  |
| latencyInMilliseconds | Latence à appliquer, exprimée en millisecondes. |
| destinationFilters | Tableau JSON délimité de filtres de paquets définissant les paquets sortants à cibler pour l’injection d’erreur. Maximum 3. |
| address | Adresse IP indiquant le début de la plage d’adresses IP. |
| subnetMask | Masque de sous-réseau pour la plage d’adresses IP. |
| portLow | (Facultatif) Numéro de port du début de la plage de ports. |
| portHigh | (Facultatif) Numéro de port de la fin de la plage de ports. |
| virtualMachineScaleSetInstances | Tableau d’ID d’instance lors de l’application de cette erreur à un groupe de machines virtuelles identiques. Nécessaire pour les groupes de machines virtuelles identiques. |

### <a name="sample-json"></a>Exemple de code JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:networkLatency/1.0",
      "parameters": [
        {
          "key": "destinationFilters",
          "value": "[ { \"address\": \"23.45.229.97\", \"subnetMask\": \"255.255.255.224\", \"portLow\": \"5000\", \"portHigh\": \"5200\" } ]"
        },
        {
          "key": "latencyInMilliseconds",
          "value": "100",
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="network-disconnect"></a>Déconnexion du réseau

| Propriété | Value |
|-|-|
| Nom de fonctionnalité | NetworkDisconnect-1.0 |
| Type cible | Microsoft-Agent |
| Types de systèmes d’exploitation pris en charge | Windows |
| Description | Bloque le trafic réseau sortant pour la plage de ports et le bloc réseau spécifiés. |
| Prérequis | L’agent doit être exécuté en tant qu’administrateur. Si l’agent est installé en tant qu’extension de machine virtuelle, il est exécuté en tant qu’administrateur par défaut. |
| URN | urn:csci:microsoft:agent:networkDisconnect/1.0 |
| Paramètres (clé, valeur) |  |
| destinationFilters | Tableau JSON délimité de filtres de paquets définissant les paquets sortants à cibler pour l’injection d’erreur. Maximum 3. |
| address | Adresse IP indiquant le début de la plage d’adresses IP. |
| subnetMask | Masque de sous-réseau pour la plage d’adresses IP. |
| portLow | (Facultatif) Numéro de port du début de la plage de ports. |
| portHigh | (Facultatif) Numéro de port de la fin de la plage de ports. |
| virtualMachineScaleSetInstances | Tableau d’ID d’instance lors de l’application de cette erreur à un groupe de machines virtuelles identiques. Nécessaire pour les groupes de machines virtuelles identiques. |

### <a name="sample-json"></a>Exemple de code JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:networkDisconnect/1.0",
      "parameters": [
        {
          "key": "destinationFilters",
          "value": "[ { \"address\": \"23.45.229.97\", \"subnetMask\": \"255.255.255.224\", \"portLow\": \"5000\", \"portHigh\": \"5200\" } ]"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

> [!WARNING]
> L’erreur de déconnexion du réseau n’affecte que les nouvelles connexions. Les connexions **actives** existantes persistent. Vous pouvez redémarrer le service ou le processus pour forcer l’arrêt des connexions.

## <a name="network-disconnect-with-firewall-rule"></a>Déconnexion du réseau avec une règle de pare-feu

| Propriété | Value |
|-|-|
| Nom de fonctionnalité | NetworkDisconnectViaFirewall-1.0 |
| Type cible | Microsoft-Agent |
| Types de systèmes d’exploitation pris en charge | Windows |
| Description | Applique une règle de pare-feu Windows afin de bloquer le trafic sortant pour une plage de ports et un bloc réseau spécifiés. |
| Prérequis | L’agent doit être exécuté en tant qu’administrateur. Si l’agent est installé en tant qu’extension de machine virtuelle, il est exécuté en tant qu’administrateur par défaut. |
| URN | urn:csci:microsoft:agent:networkDisconnectViaFirewall/1.0 |
| Paramètres (clé, valeur) |  |
| destinationFilters | Tableau JSON délimité de filtres de paquets définissant les paquets sortants à cibler pour l’injection d’erreur. Maximum 3. |
| address | Adresse IP indiquant le début de la plage d’adresses IP. |
| subnetMask | Masque de sous-réseau pour la plage d’adresses IP. |
| portLow | (Facultatif) Numéro de port du début de la plage de ports. |
| portHigh | (Facultatif) Numéro de port de la fin de la plage de ports. |
| virtualMachineScaleSetInstances | Tableau d’ID d’instance lors de l’application de cette erreur à un groupe de machines virtuelles identiques. Nécessaire pour les groupes de machines virtuelles identiques. |

### <a name="sample-json"></a>Exemple de code JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:networkDisconnectViaFirewall/1.0",
      "parameters": [
        {
          "key": "destinationFilters",
          "value": "[ { \"Address\": \"23.45.229.97\", \"SubnetMask\": \"255.255.255.224\", \"PortLow\": \"5000\", \"PortHigh\": \"5200\" } ]"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="arm-virtual-machine-shutdown"></a>Arrêt de la machine virtuelle ARM
| Propriété | Value |
|-|-|
| Nom de fonctionnalité | Shutdown-1.0 |
| Type cible | Microsoft-VirtualMachine |
| Types de systèmes d’exploitation pris en charge | Windows, Linux |
| Description | Arrête une machine virtuelle pendant la durée de l’erreur, et redémarre éventuellement la machine virtuelle à la fin de la durée de l’erreur ou en cas d’annulation de l’expérience. Seules les ressources Azure Resource Manager sont prises en charge. |
| Prérequis | Aucun. |
| URN | urn:csci:microsoft:virtualMachine:shutdown/1.0 |
| Paramètres (clé, valeur) |  |
| abruptShutdown | (Facultatif) Booléen indiquant si la machine virtuelle doit être arrêtée de manière normale ou brutale (destructive). |

### <a name="sample-json"></a>Exemple de code JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:virtualMachine:shutdown/1.0",
      "parameters": [
        {
          "key": "abruptShutdown",
          "value": "false"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="arm-virtual-machine-scale-set-instance-shutdown"></a>Arrêt d’instance de groupe de machines virtuelles identiques ARM

| Propriété | Value |
|-|-|
| Nom de fonctionnalité | Shutdown-1.0 |
| Type cible | Microsoft-VirtualMachineScaleSet |
| Types de systèmes d’exploitation pris en charge | Windows, Linux |
| Description | Arrête ou supprime une instance de groupe de machines virtuelles identiques pendant la durée de l’erreur, et redémarre éventuellement la machine virtuelle à la fin de la durée de l’erreur ou si l’expérience est annulée. |
| Prérequis | Aucun. |
| URN | urn:csci:microsoft:virtualMachineScaleSet:shutdown/1.0 |
| Paramètres (clé, valeur) |  |
| abruptShutdown | (Facultatif) Valeur booléenne indiquant si l’instance de groupe de machines virtuelles identiques doit être arrêtée de manière normale ou brutale (destructive). |
| instances | Chaîne qui est un tableau délimité d’ID d’instances de groupe de machines virtuelles identiques auxquelles l’erreur sera appliquée. |

### <a name="sample-json"></a>Exemple de code JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:virtualMachineScaleSet:shutdown/1.0",
      "parameters": [
        {
          "key": "abruptShutdown",
          "value": "true"
        },
        {
          "key": "instances",
          "value": "[\"1\",\"3\"]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="cosmos-db-failover"></a>Basculement de Cosmos DB

| Propriété | Value |
|-|-|
| Nom de fonctionnalité | Failover-1.0 |
| Type cible | Microsoft-CosmosDB |
| Description | Provoque le basculement d’un compte Cosmos DB avec une seule région d’écriture vers une région de lecture spécifiée afin de simuler une [panne de région d’écriture](../cosmos-db/high-availability.md#multi-region-accounts-with-a-single-write-region-write-region-outage). |
| Prérequis | Aucun. |
| URN | urn:csci:microsoft:cosmosDB:failover/1.0 |
| Paramètres (clé, valeur) |  |
| readRegion | Région de lecture à promouvoir en région d’écriture pendant le basculement, par exemple, « USA Est 2 ». |

### <a name="sample-json"></a>Exemple de code JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:cosmosDB:failover/1.0",
      "parameters": [
        {
          "key": "readRegion",
          "value": "West US 2"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-network-faults"></a>Erreurs réseau AKS Chaos Mesh

| Propriété | Value |
|-|-|
| Nom de fonctionnalité | NetworkChaos-2.1 |
| Type cible | Microsoft-AzureKubernetesServiceChaosMesh |
| Description | Provoque l’exécution d’une erreur réseau disponible via [Chaos Mesh](https://chaos-mesh.org/docs/simulate-network-chaos-on-kubernetes/) sur votre cluster AKS. Utile pour recréer des incidents AKS suite à des pannes de réseau, des retards, des duplications, des pertes et des altérations. |
| Prérequis | [Chaos Mesh](chaos-studio-tutorial-aks-portal.md) doit être déployé sur le cluster AKS. |
| URN | urn:csci:microsoft:azureKubernetesServiceChaosMesh:networkChaos/2.1 |
| Paramètres (clé, valeur) |  |
| jsonSpec | Spécification Chaos Mesh au format JSON et, si créée via un modèle ARM, l’API REST ou l’interface Azure CLI, dans une séquence d’échappement JSON de [type NetworkChaos](https://chaos-mesh.org/docs/simulate-network-chaos-on-kubernetes/#create-experiments-using-the-yaml-files). Vous pouvez utiliser un [convertisseur YAML-JSON comme celui-ci](https://www.convertjson.com/yaml-to-json.htm) pour convertir le fichier YAML Chaos Mesh en fichier JSON, le réduire et utiliser un [outil d’échappement de chaîne JSON comme celui-ci](https://www.freeformatter.com/json-escape.html) pour échapper la spécification JSON. Incluez uniquement le YAML sous la propriété « jsonSpec » (n’incluez pas de métadonnées, de genre, etc.). |

### <a name="sample-json"></a>Exemple de code JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:networkChaos/2.1",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"action\":\"delay\",\"mode\":\"one\",\"selector\":{\"namespaces\":[\"default\"],\"labelSelectors\":{\"app\":\"web-show\"}},\"delay\":{\"latency\":\"10ms\",\"correlation\":\"100\",\"jitter\":\"0ms\"}}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-pod-faults"></a>Erreurs de pod AKS Chaos Mesh

| Propriété | Value |
|-|-|
| Nom de fonctionnalité | PodChaos-2.1 |
| Type cible | Microsoft-AzureKubernetesServiceChaosMesh |
| Description | Provoque l’exécution d’une erreur de pod disponible via [Chaos Mesh](https://chaos-mesh.org/docs/simulate-pod-chaos-on-kubernetes/) sur votre cluster AKS. Utile pour recréer des incidents AKS qui résultent de défaillances de pod ou de problèmes de conteneur. |
| Prérequis | [Chaos Mesh](chaos-studio-tutorial-aks-portal.md) doit être déployé sur le cluster AKS. |
| URN | urn:csci:microsoft:azureKubernetesServiceChaosMesh:podChaos/2.1 |
| Paramètres (clé, valeur) |  |
| jsonSpec | Spécification Chaos Mesh au format JSON et, si créée via un modèle ARM, l’API REST ou l’interface Azure CLI, dans une séquence d’échappement JSON de [type PodChaos](https://chaos-mesh.org/docs/simulate-pod-chaos-on-kubernetes/#create-experiments-using-yaml-configuration-files). Vous pouvez utiliser un [convertisseur YAML-JSON comme celui-ci](https://www.convertjson.com/yaml-to-json.htm) pour convertir le fichier YAML Chaos Mesh en fichier JSON et le réduire, et utiliser un [outil d’échappement de chaîne JSON comme celui-ci](https://www.freeformatter.com/json-escape.html) pour échapper la spécification JSON. Incluez uniquement le YAML sous la propriété « jsonSpec » (n’incluez pas de métadonnées, de genre, etc.). |

### <a name="sample-json"></a>Exemple de code JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:podChaos/2.1",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"action\":\"pod-failure\",\"mode\":\"one\",\"duration\":\"30s\",\"selector\":{\"labelSelectors\":{\"app.kubernetes.io\/component\":\"tikv\"}}}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-stress-faults"></a>Erreurs de contrainte AKS Chaos Mesh

| Propriété | Value |
|-|-|
| Nom de fonctionnalité | StressChaos-2.1 |
| Type cible | Microsoft-AzureKubernetesServiceChaosMesh |
| Description | Provoque l’exécution d’une erreur de contrainte disponible via [Chaos Mesh](https://chaos-mesh.org/docs/simulate-heavy-stress-on-kubernetes/) sur votre cluster AKS. Utile pour recréer des incidents AKS résultant de contraintes sur une collection de pods, par exemple, en raison d’une utilisation élevée du processeur ou de la mémoire. |
| Prérequis | [Chaos Mesh](chaos-studio-tutorial-aks-portal.md) doit être déployé sur le cluster AKS. |
| URN | urn:csci:microsoft:azureKubernetesServiceChaosMesh:stressChaos/2.1 |
| Paramètres (clé, valeur) |  |
| jsonSpec | Spécification Chaos Mesh au format JSON et, si créée via un modèle ARM, l’API REST ou l’interface Azure CLI, dans une séquence d’échappement JSON de [type StressChaos](https://chaos-mesh.org/docs/simulate-heavy-stress-on-kubernetes/#create-experiments-using-the-yaml-file). Vous pouvez utiliser un [convertisseur YAML-JSON comme celui-ci](https://www.convertjson.com/yaml-to-json.htm) pour convertir le fichier YAML Chaos Mesh en fichier JSON et le réduire, et utiliser un [outil d’échappement de chaîne JSON comme celui-ci](https://www.freeformatter.com/json-escape.html) pour échapper la spécification JSON. Incluez uniquement le YAML sous la propriété « jsonSpec » (n’incluez pas de métadonnées, de genre, etc.). |

### <a name="sample-json"></a>Exemple de code JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:stressChaos/2.1",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"mode\":\"one\",\"selector\":{\"labelSelectors\":{\"app\":\"app1\"}},\"stressors\":{\"memory\":{\"workers\":4,\"size\":\"256MB\"}}}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-io-faults"></a>Erreurs d’E/S AKS Chaos Mesh

| Propriété | Value |
|-|-|
| Nom de fonctionnalité | IOChaos-2.1 |
| Type cible | Microsoft-AzureKubernetesServiceChaosMesh |
| Description | Provoque l’exécution d’une erreur d’E/S disponible via [Chaos Mesh](https://chaos-mesh.org/docs/simulate-io-chaos-on-kubernetes/) sur votre cluster AKS. Utile pour recréer des incidents AKS résultant de retards d’E/S et des défaillances de lecture/écriture lors de l’utilisation d’appels de système d’E/S tels que `open`, `read` et `write`. |
| Prérequis | [Chaos Mesh](chaos-studio-tutorial-aks-portal.md) doit être déployé sur le cluster AKS. |
| URN | urn:csci:microsoft:azureKubernetesServiceChaosMesh:IOChaos/2.1 |
| Paramètres (clé, valeur) |  |
| jsonSpec | Spécification Chaos Mesh au format JSON et, si créée via un modèle ARM, l’API REST ou l’interface Azure CLI, dans une séquence d’échappement JSON de [type IOChaos](https://chaos-mesh.org/docs/simulate-io-chaos-on-kubernetes/#create-experiments-using-the-yaml-files). Vous pouvez utiliser un [convertisseur YAML-JSON comme celui-ci](https://www.convertjson.com/yaml-to-json.htm) pour convertir le fichier YAML Chaos Mesh en fichier JSON et le réduire, et utiliser un [outil d’échappement de chaîne JSON comme celui-ci](https://www.freeformatter.com/json-escape.html) pour échapper la spécification JSON. Incluez uniquement le YAML sous la propriété « jsonSpec » (n’incluez pas de métadonnées, de genre, etc.). |

### <a name="sample-json"></a>Exemple de code JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:IOChaos/2.1",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"action\":\"latency\",\"mode\":\"one\",\"selector\":{\"labelSelectors\":{\"app\":\"etcd\"}},\"volumePath\":\"\/var\/run\/etcd\",\"path\":\"\/var\/run\/etcd\/**\/*\",\"delay\":\"100ms\",\"percent\":50,\"duration\":\"400s\"}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-time-faults"></a>Erreurs d’heure AKS Chaos Mesh

| Property | Value |
|-|-|
| Nom de fonctionnalité | TimeChaos-2.1 |
| Type cible | Microsoft-AzureKubernetesServiceChaosMesh |
| Description | Entraîne une modification de l’horloge système sur votre cluster AKS à l’aide de [Chaos Mesh](https://chaos-mesh.org/docs/simulate-time-chaos-on-kubernetes/). Utile pour recréer des incidents AKS résultant d’un échec de synchronisation de systèmes distribués, d’une logique d’année bissextile ou de seconde intercalaire, et bien plus encore. |
| Prérequis | [Chaos Mesh](chaos-studio-tutorial-aks-portal.md) doit être déployé sur le cluster AKS. |
| URN | urn:csci:microsoft:azureKubernetesServiceChaosMesh:timeChaos/2.1 |
| Paramètres (clé, valeur) |  |
| jsonSpec | Spécification Chaos Mesh au format JSON et, si créée via un modèle ARM, l’API REST ou l’interface Azure CLI, dans une séquence d’échappement JSON de [type TimeChaos](https://chaos-mesh.org/docs/simulate-time-chaos-on-kubernetes/#create-experiments-using-the-yaml-file). Vous pouvez utiliser un [convertisseur YAML-JSON comme celui-ci](https://www.convertjson.com/yaml-to-json.htm) pour convertir le fichier YAML Chaos Mesh en fichier JSON et le réduire, et utiliser un [outil d’échappement de chaîne JSON comme celui-ci](https://www.freeformatter.com/json-escape.html) pour échapper la spécification JSON. Incluez uniquement le YAML sous la propriété « jsonSpec » (n’incluez pas de métadonnées, de genre, etc.). |

### <a name="sample-json"></a>Exemple de code JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:timeChaos/2.1",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"mode\":\"one\",\"selector\":{\"labelSelectors\":{\"app\":\"app1\"}},\"timeOffset\":\"-10m100ns\"}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-kernel-faults"></a>Erreurs du noyau AKS Chaos Mesh

| Propriété | Value |
|-|-|
| Nom de fonctionnalité | KernelChaos-2.1 |
| Type cible | Microsoft-AzureKubernetesServiceChaosMesh |
| Description | Provoque l’exécution d’une erreur de noyau disponible via [Chaos Mesh](https://chaos-mesh.org/docs/simulate-kernel-chaos-on-kubernetes/) sur votre cluster AKS. Utile pour recréer des incidents AKS résultant d’erreurs au niveau du noyau Linux, telles qu’un échec de montage ou la non-allocation de mémoire. |
| Prérequis | [Chaos Mesh](chaos-studio-tutorial-aks-portal.md) doit être déployé sur le cluster AKS. |
| URN | urn:csci:microsoft:azureKubernetesServiceChaosMesh:kernelChaos/2.1 |
| Paramètres (clé, valeur) |  |
| jsonSpec | Spécification Chaos Mesh au format JSON et, si créée via un modèle ARM, l’API REST ou l’interface Azure CLI, dans une séquence d’échappement JSON de [type KernelChaos](https://chaos-mesh.org/docs/simulate-kernel-chaos-on-kubernetes/#configuration-file). Vous pouvez utiliser un [convertisseur YAML-JSON comme celui-ci](https://www.convertjson.com/yaml-to-json.htm) pour convertir le fichier YAML Chaos Mesh en fichier JSON et le réduire, et utiliser un [outil d’échappement de chaîne JSON comme celui-ci](https://www.freeformatter.com/json-escape.html) pour échapper la spécification JSON. Incluez uniquement le YAML sous la propriété « jsonSpec » (n’incluez pas de métadonnées, de genre, etc.). |

### <a name="sample-json"></a>Exemple de code JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:kernelChaos/2.1",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"mode\":\"one\",\"selector\":{\"namespaces\":[\"chaos-mount\"]},\"failKernRequest\":{\"callchain\":[{\"funcname\":\"__x64_sys_mount\"}],\"failtype\":0}}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-http-faults"></a>Erreurs HTTP AKS Chaos Mesh

| Propriété | Value |
|-|-|
| Nom de fonctionnalité | HTTPChaos-2.1 |
| Type cible | Microsoft-AzureKubernetesServiceChaosMesh |
| Description | Provoque l’exécution d’une erreur HTTP disponible via [Chaos Mesh](https://chaos-mesh.org/docs/simulate-http-chaos-on-kubernetes/) sur votre cluster AKS. Utile pour recréer des incidents dus à des échecs de traitement de requête et de réponse HTTP, comme des réponses retardées ou incorrectes. |
| Prérequis | [Chaos Mesh](chaos-studio-tutorial-aks-portal.md) doit être déployé sur le cluster AKS. |
| URN | urn:csci:microsoft:azureKubernetesServiceChaosMesh:httpChaos/2.1 |
| Paramètres (clé, valeur) |  |
| jsonSpec | Spécification Chaos Mesh au format JSON et, si créée via un modèle ARM, l’API REST ou l’interface Azure CLI, dans une séquence d’échappement JSON de [type HTTPChaos](https://chaos-mesh.org/docs/simulate-http-chaos-on-kubernetes/#create-experiments). Vous pouvez utiliser un [convertisseur YAML-JSON comme celui-ci](https://www.convertjson.com/yaml-to-json.htm) pour convertir le fichier YAML Chaos Mesh en fichier JSON et le réduire, et utiliser un [outil d’échappement de chaîne JSON comme celui-ci](https://www.freeformatter.com/json-escape.html) pour échapper la spécification JSON. Incluez uniquement le YAML sous la propriété « jsonSpec » (n’incluez pas de métadonnées, de genre, etc.). |

### <a name="sample-json"></a>Exemple de code JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:httpChaos/2.1",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"mode\":\"all\",\"selector\":{\"labelSelectors\":{\"app\":\"nginx\"}},\"target\":\"Request\",\"port\":80,\"method\":\"GET\",\"path\":\"\/api\",\"abort\":true,\"duration\":\"5m\",\"scheduler\":{\"cron\":\"@every 10m\"}}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-dns-faults"></a>Erreurs DNS AKS Chaos Mesh

| Propriété | Value |
|-|-|
| Nom de fonctionnalité | DNSChaos-2.1 |
| Type cible | Microsoft-AzureKubernetesServiceChaosMesh |
| Description | Provoque l’exécution d’une erreur DNS disponible via [Chaos Mesh](https://chaos-mesh.org/docs/simulate-dns-chaos-on-kubernetes/) sur votre cluster AKS. Utile pour recréer des incidents dus à des défaillances DNS. |
| Prérequis | [Chaos Mesh](chaos-studio-tutorial-aks-portal.md) doit être déployé sur le cluster AKS et le [service DNS doit être installé](https://chaos-mesh.org/docs/simulate-dns-chaos-on-kubernetes/#deploy-chaos-dns-service). |
| URN | urn:csci:microsoft:azureKubernetesServiceChaosMesh:dnsChaos/2.1 |
| Paramètres (clé, valeur) |  |
| jsonSpec | Spécification Chaos Mesh au format JSON et, si créée via un modèle ARM, l’API REST ou l’interface Azure CLI, dans une séquence d’échappement JSON de [type DNSChaos](https://chaos-mesh.org/docs/simulate-dns-chaos-on-kubernetes/#create-experiments-using-the-yaml-file). Vous pouvez utiliser un [convertisseur YAML-JSON comme celui-ci](https://www.convertjson.com/yaml-to-json.htm) pour convertir le fichier YAML Chaos Mesh en fichier JSON et le réduire, et utiliser un [outil d’échappement de chaîne JSON comme celui-ci](https://www.freeformatter.com/json-escape.html) pour échapper la spécification JSON. Incluez uniquement le YAML sous la propriété « jsonSpec » (n’incluez pas de métadonnées, de genre, etc.). |

### <a name="sample-json"></a>Exemple de code JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:dnsChaos/2.1",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"action\":\"random\",\"mode\":\"all\",\"patterns\":[\"google.com\",\"chaos-mesh.*\",\"github.?om\"],\"selector\":{\"namespaces\":[\"busybox\"]}}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="network-security-group-set-rules"></a>Groupe de sécurité réseau (règles définies)

| Propriété | Value |
|-|-|
| Nom de fonctionnalité | SecurityRule-1.0 |
| Type cible | Microsoft-NetworkSecurityGroup |
| Description | Permet la manipulation ou la création d’une règle dans un groupe de sécurité réseau Azure existant ou un ensemble de groupes de sécurité réseau Azure (en supposant que la définition de la règle est applicable aux groupes de sécurité croisés). Utile pour simuler une panne de dépendance/non-dépendance en aval ou inter-régions, simuler un événement supposé déclencher une logique pour forcer un basculement de service, simuler un événement supposé déclencher une action d’un service de surveillance ou de gestion d’état, ou en tant qu’alternative pour bloquer ou autoriser le trafic réseau là où Chaos Agent ne peut pas être déployé |
| Prérequis | Aucun. |
| URN | urn:csci:microsoft:networkSecurityGroup:securityRule/1.0 |
| Paramètres (clé, valeur) |  |
| name | Nom unique pour la règle de sécurité qui sera créée. L’erreur échoue si une autre règle existe déjà sur le groupe de sécurité réseau portant le même nom. Doit commencer par une lettre ou un chiffre, se terminer par une lettre, un chiffre ou un trait de soulignement, et ne peut contenir que des lettres, des chiffres, des traits de soulignement, des points ou des tirets. |
| protocol | Protocole pour la règle de sécurité. Doit être Any, TCP, UDP ou ICMP. |
| sourceAddresses | Chaîne représentant un tableau délimité json d’adresses IP au format CIDR. Peut également être un nom d’étiquette de service pour une règle de trafic entrant, par exemple « AppService ». Peut également utiliser un astérisque (*) pour établir une correspondance avec toutes les adresses IP sources. |
| destinationAddresses | Chaîne représentant un tableau délimité json d’adresses IP au format CIDR. Peut également être un nom d’étiquette de service pour une règle de trafic sortant, par exemple « AppService ». Peut également utiliser un astérisque (*) pour établir une correspondance avec toutes les adresses IP de destination. |
| action | Type d’accès au groupe de sécurité. Doit être Autoriser ou Refuser |
| destinationPortRanges | Chaîne représentant un tableau délimité json de ports uniques et/ou de plages de ports, par exemple, 80 ou 1024-65535. |
| sourcePortRanges | Chaîne représentant un tableau délimité json de ports uniques et/ou de plages de ports, par exemple, 80 ou 1024-65535. |
| priority | Entrez une valeur comprise entre 100 et 4096 qui est unique pour toutes les règles de sécurité au sein du groupe de sécurité réseau. L’erreur échoue si une autre règle existe déjà sur le groupe de sécurité avec la même priorité. |
| direction | Direction du trafic impactée par la règle de sécurité. Il doit s’agir d’un trafic entrant ou sortant. |

### <a name="sample-json"></a>Exemple de code JSON

```json
{ 
  "name": "branchOne", 
  "actions": [ 
    { 
      "type": "continuous", 
      "name": "urn:csci:microsoft:networkSecurityGroup:securityRule/1.0", 
      "parameters": [ 
          { 
              "key": "name", 
              "value": "Block_SingleHost_to_Networks" 

          }, 
          { 
              "key": "protocol", 
              "value": "Any" 
          }, 
          { 
              "key": "sourceAddresses", 
              "value": "[\"10.1.1.128/32\"]"
          }, 
          { 
              "key": "destinationAddresses", 
              "value": "[\"10.20.0.0/16\",\"10.30.0.0/16\"]"
          }, 
          { 
              "key": "access", 
              "value": "Deny" 
          }, 
          { 
              "key": "destinationPortRanges", 
              "value": "[\"80-8080\"]"
          }, 
          { 
              "key": "sourcePortRanges", 
              "value": "[\"*\"]"
          }, 
          { 
              "key": "priority", 
              "value": "100" 
          }, 
          { 
              "key": "direction", 
              "value": "Outbound" 
          } 
      ], 
      "duration": "PT10M", 
      "selectorid": "myResources" 
    } 
  ] 
} 
```

### <a name="limitations"></a>Limitations

* L’erreur ne peut être appliquée qu’à un groupe de sécurité réseau existant.
* Quand une règle de groupe de sécurité réseau destinée à refuser le trafic est appliquée, les connexions existantes ne sont pas arrêtées tant qu’elles n’ont pas été **inactives** pendant 4 minutes. Une solution de contournement consiste à ajouter une autre branche dans la même étape, qui utilise une erreur qui provoquerait l’arrêt des connexions existantes lors de l’application de l’erreur de groupe de sécurité réseau. Par exemple, l’arrêt du processus, l’arrêt temporaire du service ou le redémarrage de la machine virtuelle entraînent la réinitialisation des connexions.
* Les règles sont appliquées au début de l’action. Toute modification externe de la règle pendant la durée de l’action entraîne l’échec de l’expérience.
* La création ou la modification des règles de groupe de sécurité d’application ne sont pas prises en charge.
* Les valeurs de priorité doivent être uniques sur chaque groupe de sécurité réseau ciblé. Toute tentative de création d’une nouvelle règle ayant la même valeur de priorité peut entraîner l’échec de l’expérience.

## <a name="azure-cache-for-redis-reboot"></a>Redémarrage d’Azure Cache pour Redis

| Propriété | Value |
|-|-|
| Nom de fonctionnalité | Reboot-1.0 |
| Type cible | Microsoft-AzureClusteredCacheForRedis |
| Description | Entraîne une opération de redémarrage forcé sur la cible afin de simuler une courte panne. |
| Prérequis | Aucun. |
| URN | urn:csci:microsoft:azureClusteredCacheForRedis:reboot/1.0 |
| Type d’erreur | Discret |
| Paramètres (clé, valeur) |  |
| rebootType | Types de nœuds où l’action de redémarrage doit être exécutée, qui peuvent être spécifiés en tant que PrimaryNode, SecondaryNode ou AllNodes.  |
| shardId | ID de la partition à redémarrer.  |

### <a name="sample-json"></a>Exemple de code JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "discrete",
      "name": "urn:csci:microsoft:azureClusteredCacheForRedis:reboot/1.0",
      "parameters": [
        {
          "key": "RebootType",
          "value": "AllNodes"
        },
        {
          "key": "ShardId",
          "value": "0"
        }
      ],
      "selectorid": "myResources"
    }
  ]
}
```

### <a name="limitations"></a>Limitations

* L’erreur de redémarrage entraîne un redémarrage forcé afin de mieux simuler une panne, ce qui signifie qu’il existe un risque de perte de données.
* L’erreur de redémarrage est un type d’erreur **discrète**. Contrairement aux erreurs continues, il s’agit d’une action ponctuelle qui n’a donc aucune durée.
