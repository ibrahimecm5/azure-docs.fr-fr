---
title: Vue d’ensemble du schéma YAML CLI (v2)
titleSuffix: Azure Machine Learning
description: Vue d’ensemble et index des schémas YAML CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 541cb9049be64977858a10cd486605f9572b3f30
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131561344"
---
# <a name="cli-v2-yaml-schemas"></a>Schémas YAML CLI (v2)

La CLI (v2) Azure Machine Learning, une extension d’Azure CLI, utilise souvent et requiert parfois des fichiers YAML avec des schémas spécifiques. Cet article répertorie les documents de référence et le schéma source pour les fichiers YAML. Des exemples sont inclus inline dans des articles individuels.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="workspace"></a>Espace de travail

| Référence | URI |
| - | - |
| [Espace de travail](reference-yaml-workspace.md) | https://azuremlschemas.azureedge.net/latest/workspace.schema.json |

## <a name="environment"></a>Environnement

| Référence | URI |
| - | - |
| [Environment](reference-yaml-environment.md) | https://azuremlschemas.azureedge.net/latest/environment.schema.json |

## <a name="dataset"></a>Dataset

| Référence | URI |
| - | - |
| [Dataset](reference-yaml-dataset.md) | https://azuremlschemas.azureedge.net/latest/dataset.schema.json |

## <a name="model"></a>Modéliser

| Référence | URI |
| - | - |
| [Modèle](reference-yaml-model.md) | https://azuremlschemas.azureedge.net/latest/model.schema.json |

## <a name="compute"></a>Calcul

| Référence | URI |
| - | - |
| [Cluster de calcul (AmlCompute)](reference-yaml-compute-aml.md) | https://azuremlschemas.azureedge.net/latest/amlCompute.schema.json |
| [Instance de calcul](reference-yaml-compute-instance.md) | https://azuremlschemas.azureedge.net/latest/computeInstance.schema.json |
| [Machine virtuelle jointe](reference-yaml-compute-vm.md) | https://azuremlschemas.azureedge.net/latest/vmCompute.schema.json |

## <a name="job"></a>Travail

| Référence | URI |
| - | - |
| [Commande](reference-yaml-job-command.md) | https://azuremlschemas.azureedge.net/latest/commandJob.schema.json |
| [Sweep](reference-yaml-job-sweep.md) | https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json |
| [Pipeline](reference-yaml-job-pipeline.md) | https://azuremlschemas.azureedge.net/latest/pipelineJob.schema.json |

## <a name="datastore"></a>Magasin de données

| Référence | URI |
| - | - |
| [Blob Azure](reference-yaml-datastore-blob.md) | https://azuremlschemas.azureedge.net/latest/azureBlob.schema.json |
| [Azure Files](reference-yaml-datastore-files.md) | https://azuremlschemas.azureedge.net/latest/azureFile.schema.json |
| [Azure Data Lake Gen1](reference-yaml-datastore-data-lake-gen1.md) | https://azuremlschemas.azureedge.net/latest/azureDataLakeGen1.schema.json |
| [Azure Data Lake Gen2](reference-yaml-datastore-data-lake-gen2.md) | https://azuremlschemas.azureedge.net/latest/azureDataLakeGen2.schema.json |

## <a name="endpoint"></a>Point de terminaison

| Référence | URI |
| - | - |
| [Managé en ligne (en temps réel)](reference-yaml-endpoint-managed-online.md) | https://azuremlschemas.azureedge.net/latest/managedOnlineEndpoint.schema.json |
| [Batch](reference-yaml-endpoint-batch.md) | https://azuremlschemas.azureedge.net/latest/batchEndpoint.schema.json |

## <a name="deployment"></a>Déploiement

| Référence | URI |
| - | - |
| [Managé en ligne (en temps réel)](reference-yaml-deployment-managed-online.md) | https://azuremlschemas.azureedge.net/latest/managedOnlineDeployment.schema.json |
| [Batch](reference-yaml-deployment-batch.md) | https://azuremlschemas.azureedge.net/latest/batchDeployment.schema.json |

## <a name="component"></a>Composant

| Référence | URI |
| - | - |
| [Commande](reference-yaml-component-command.md) | https://azuremlschemas.azureedge.net/latest/commandComponent.schema.json |

## <a name="next-steps"></a>Étapes suivantes

- [Installer et utiliser l’interface CLI (v2)](how-to-configure-cli.md)
