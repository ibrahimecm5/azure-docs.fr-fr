---
title: Nouveautés relatives à Data Science Virtual Machine
titleSuffix: Azure Data Science Virtual Machine
description: Notes de publication pour Azure Data Science Virtual Machine
author: michalmar
ms.service: data-science-vm
ms.author: mimarusa
ms.date: 10/11/2021
ms.topic: reference
ms.openlocfilehash: 97b95b37367d1cede3326307602a19ffdc5df8d5
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517137"
---
# <a name="azure-data-science-virtual-machine-release-notes"></a>Notes de publication Azure Data Science Virtual Machine

Dans cet article, découvrez les versions d’Azure Data Science Virtual Machine. Pour obtenir la liste complète des outils inclus, ainsi que les numéros de version, consultez [cette page](./tools-included.md).

Consultez la [liste des problèmes connus](reference-known-issues.md) pour en savoir plus sur les bogues connus et les solutions de contournement.

## <a name="november-4-2021"></a>4 novembre 2021

Nouvelle image pour [Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview).

Version : 21.11.04

Principaux changements :
* Mise à jour de .NET Framework vers la version 3.1.414
* Mise à jour d’Azcopy vers la version 10.13.0
* Mise à jour d’Azure CLI vers la version 2.30.0
* Mise à jour de CUDA vers la version 11.5
* Mise à jour de Docker vers la version 20.10.10
* Mise à jour d’Intellijidea vers la version 2021.2.3
* Mise à jour des pilotes NVIDIA vers la version 495.29.05
* Mise à jour de NVIDIA SMI vers la version 495.29.05
* Mise à jour de NodeJS vers la version 16.13.0
* Mise à jour de Pycharm vers la version 2021.2.3
* Mise à jour de VS Code vers la version 1.61.2
* Conda
  * *azureml_py36_automl*
    * Mise à jour d’azureml-core vers la version 1.35.0
  * *py38_default*
    * Mise à jour de Jupyter Lab/jupyterlab vers la version 3.2.1
    * Mise à jour de Jupyter Notebook/notebook vers la version 6.4.5
    * Mise à jour de Jupyter Server/jupyter_server vers la version 1.11.2
    * Mise à jour du plug-in PyTorch Profiler TensorBoard/torch-tb-profiler vers la version 0.3.1
    * Mise à jour d’azure-core vers la version 1.19.1
    * Mise à jour de matplotlib vers la version 3.4.3
    * Mise à jour de mkl vers la version 2021.4.0
    * Mise à jour d’onnx vers la version 1.10.2
    * Mise à jour d’opencv-python vers la version 4.5.4.58
    * Mise à jour de pandas vers la version 1.3.4
    * Mise à jour de PyTorch vers la version 1.10.0
    * Mise à jour de scikit-learn vers la version 1.0.1
    * Mise à jour de tensorflow-gpu vers la version 2.6.2


## <a name="october-7-2021"></a>7 octobre 2021

Nouvelle image pour [Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview).

Version : 07-10-21

Principaux changements :
 - Mise à jour de PyTorch vers la version 1.9.1
 - Mise à jour de Docker vers la version 20.10.9
 - Mise à jour d’Intellijidea vers la version 2021.2.2
 - Mise à jour de NodeJS vers la version 14.18.0
 - Mise à jour de Pycharm vers la version 2021.2.2
 - Mise à jour de VS Code vers la version 1.60.2
 - Correction de l’environnement AutoML (azureml_py36_automl)
 - Correction de la stabilité d’Explorateur Stockage Azure
 - Amélioration de la stabilité et correction de bogues mineurs 


## <a name="august-11-2021"></a>11 août 2021

Nouvelle image pour [Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019?tab=Overview).

Version 21.08.11

Principaux changements :

- Mise à jour de la sécurité de Windows
- Mise à jour de NVIDIA CuDNN vers 8.1.0
- Mise à jour de Jupyter Lab-to 3.0.16
- Ajout de MLFLow pour le suivi des expériences
- Amélioration de la stabilité et correction de bogues mineurs 



## <a name="july-12-2021"></a>12 juillet 2021

Nouvelle image pour [Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview).

Principaux changements :

- Mise à jour vers PyTorch 1.9.0
- Mise à jour d’Azure CLI vers la version 2.26.1
- Mise à jour de l'extension Azure CLI Azure Machine Learning à la version 1.29.0
- Mise à jour de VS Code version 1.58.1
- Amélioration de la stabilité et correction de bogues mineurs 


## <a name="june-22-2021"></a>22 juin 2021

Nouvelle image pour [Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019?tab=Overview).

Version : 21.06.22

Principaux changements :

- Mise à jour vers PyTorch 1.9.0
- Correction d’un bogue empêchant la disponibilité de git


## <a name="june-1-2021"></a>1er juin 2021

Nouvelle image pour [Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview).

Version : 21.06.01

Les principaux changements sont les suivants :

- Docker est activé par défaut
- JupyterHub utilise JupyterLab par défaut
- Mise à jour des versions de Python pour corriger [CVE-2020-15523](https://nvd.nist.gov/vuln/detail/CVE-2020-15523)
- Mise à jour d’IntelliJ IDEA à la version 2021.1 pour corriger [CVE-2021-25758](https://nvd.nist.gov/vuln/detail/CVE-2021-25758)
- Mise à jour de PyCharm Community à la version 2021.1
- Mise à jour de TensorFlow à la version 2.5.0

<br/>
Suppression de plusieurs icônes du bureau

## <a name="may-22-2021"></a>22 mai 2021

Nouvelle image pour [Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019?tab=Overview).

Version : 21.05.22

Les mises à jour de version sélectionnées sont les suivantes :
- CUDA 11.1
- Python 3.8
- PyTorch 1.8.1
- TensorFlow 2.5.0
- Spark 3.1.1
- Java 11
- R 4.1.0
- Julia 1.0.5
- NodeJS 16.2.0
- Visual Studio Code 1.56.2, y compris l’extension Azure ML
- PyCharm Community édition 2021.1.1
- Jupyter Lab 2.2.6
- RStudio 1.4.1106
- Visual Studio Community édition 2019 (version 16.9.6)
- Azure CLI 2.23.0
- Explorateur Stockage 1.19.1
- AzCopy 10.10.0
- Power BI Desktop 2.93.641.0 64 bits (mai 2021)
- Azure Data Studio 1.28.0
- Navigateur Microsoft Edge

<br/>
Suppression de Firefox, d’Apache Drill et de Microsoft Integration Runtime.

<br/>
Mode sombre, modification des icônes sur le bureau, modification du papier peint en arrière-plan.

## <a name="may-12-2021"></a>12 mai 2021

Nouvelle image pour [Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview).

Les mises à jour de version sélectionnées sont les suivantes :
- CUDA 11.3, cuDNN 8, NCCL2
- Python 3.8
- R 4.0.5
- Spark 3.1, y compris mmlspark et les connecteurs vers Stockage Blob, Data Lake et CosmosDB
- Java 11 (OpenJDK)
- Jupyter Lab 3.0.14
- PyTorch 1.8.1, y compris torchaudio torchtext torchvision, torch-tb-profiler
- TensorFlow 2.4.1, y compris TensorBoard
- dask 2021.01.0
- VS.Code 1.56
- Azure Data Studio 1.22.1
- Azure CLI 2.23.0
- Explorateur Stockage Azure 1.19.1
- azcopy 10.10
- Navigateur Microsoft Edge (bêta)

<br/>
Ajout de Docker. Pour économiser des ressources, le service Docker n’est pas démarré par défaut. Pour démarrer le service Docker, exécutez les commandes de ligne de commande suivantes :

```
sudo systemctl start docker
```

> [!NOTE]
> Si votre machine est équipée de GPU, vous pouvez les utiliser à l’intérieur des conteneurs en ajoutant un paramètre `--gpus` à votre commande Docker.
>
> Par exemple, l’exécution de 
>
> `sudo docker run --gpus all -it --rm -v local_dir:container_dir nvcr.io/nvidia/pytorch:18.04-py3`
>
> exécutera un conteneur Ubuntu 18.04 avec PyTorch préinstallé et tous les GPU activés. Elle rendra également un dossier local *local_dir* disponible dans le conteneur sous *container_dir*.
>


## <a name="february-24-2020"></a>24 février 2020

Images Data Science Virtual Machine désormais disponibles pour les images [Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview) et [Windows 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019?tab=Overview).

## <a name="october-8-2019"></a>8 octobre 2019

### <a name="updates-to-software-on-the-windows-dsvm"></a>Mises à jour des logiciels sur la machine virtuelle DSVM Windows

- Explorateur Stockage Azure 1.10.1
- Power BI Desktop 2.73.55xx
- Firefox 69.0.2
- PyCharm 19.2.3
- RStudio 1.2.50xx

### <a name="default-browser-for-windows-updated"></a>Mise à jour du navigateur par défaut pour Windows

Précédemment, le navigateur par défaut était défini sur Internet Explorer. Les utilisateurs sont désormais invités à choisir un navigateur par défaut lorsqu’ils se connectent pour la première fois.
