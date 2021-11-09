---
title: Mise à l’échelle d’applications HPC - Machines virtuelles Microsoft Azure | Microsoft Docs
description: Découvrez comment mettre à l’échelle des applications HPC sur des machines virtuelles Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 04/16/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 408f55592ee7a192172c85e7a417db0b277f0f46
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131054511"
---
# <a name="scaling-hpc-applications"></a>Mise à l’échelle d’applications HPC

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

Pour bénéficier de performances de scale-out et de montée en puissance optimales pour les applications HPC sur Azure, vous devez régler les performances et effectuer différentes tentatives d’optimisation en fonction d’une charge de travail spécifique. Cette section et les pages spécifiques à la série de machines virtuelles offrent des conseils généraux pour la mise à l’échelle de vos applications.

## <a name="application-setup"></a>Configuration des applications
Le [référentiel azurehpc](https://github.com/Azure/azurehpc) contient de nombreux exemples de :
- Configuration et exécution optimale des [applications](https://github.com/Azure/azurehpc/tree/master/apps).
- Configuration des [systèmes de fichiers et des clusters](https://github.com/Azure/azurehpc/tree/master/examples).
- [Didacticiels](https://github.com/Azure/azurehpc/tree/master/tutorials) sur la prise en main facile de certains workflows d’application courants.

## <a name="optimally-scaling-mpi"></a>Mise à l’échelle optimale de MPI 

Les suggestions suivantes s’appliquent pour une mise à l’échelle optimale de l’efficacité, des performances et de la cohérence :

- Pour les tâches de mise à l’échelle plus petites (connexions < 256 k), utilisez l’option :
   ```bash
   UCX_TLS=rc,sm
   ```

- Pour les tâches de mise à l’échelle plus grandes (connexions > 256 k), utilisez l’option :
   ```bash
   UCX_TLS=dc,sm
   ```

- Dans le paragraphe ci-dessus, pour calculer le nombre de connexions pour votre travail MPI, utilisez :
   ```bash
   Max Connections = (processes per node) x (number of nodes per job) x (number of nodes per job) 
   ```

## <a name="adaptive-routing"></a>Routage adaptatif
Le routage adaptatif (AR) permet aux machines virtuelles Azure exécutant EDR et HDR InfiniBand de détecter automatiquement et d’éviter la congestion du réseau en sélectionnant de façon dynamique des chemins d’accès réseau plus optimaux. Par conséquent, le routage adaptatif permet une latence et une bande passante améliorées sur le réseau InfiniBand, qui à son tour améliore les performances et la mise à l’échelle. Pour plus d’informations, reportez-vous à l’[article TechCommunity](https://techcommunity.microsoft.com/t5/azure-compute/adaptive-routing-on-azure-hpc/ba-p/1205217).

## <a name="process-pinning"></a>Épinglage de processus

- Le code confidentiel effectue un traitement sur les cœurs à l’aide d’une approche d’épinglage (soit l’inverse d’une approche d’auto-équilibrage). 
- Il est préférable d’opter pour une liaison via Numa/Core/HwThread que pour la liaison par défaut.
- Pour les applications parallèles hybrides (OpenMP+MPI), utilisez 4 threads et 1 rang MPI par CCX sur les tailles de machine virtuelle HB et HBv2.
- Pour les applications entièrement MPI, essayez d’utiliser 1 à 4 rangs MPI par CCX, pour des performances optimales sur les tailles de machine virtuelle HB et HBv2.
- Certaines applications extrêmement sensibles à la bande passante peuvent tirer parti de l’utilisation d’un nombre réduit de cœurs par CCX. Pour ces applications, l’utilisation de 3 ou 2 cœurs par CCI peut permettre de réduire les contentions au niveau de la bande passante de la mémoire et générer dans le monde réel des performances plus élevées ou une évolutivité plus importante. En particulier, MPI Allreduce peut tirer parti de cette approche.
- Pour des exécutions à plus grande échelle, nous vous recommandons d’utiliser des transports UD, ou des transports hybrides RC+UD. De nombreuses bibliothèques/bibliothèques de runtime MPI effectuent cette opération en interne (comme UCX ou MVAPICH2). Vérifier vos configurations de transport pour les exécutions à grande échelle.

## <a name="compiling-applications"></a>Compilation d’applications
<br>
<details>
<summary>Cliquez pour développer</summary>

Bien qu’elle ne soit pas nécessaire, la compilation des applications avec les indicateurs d’optimisation appropriés fournit les meilleures performances de montée en puissance sur des machines virtuelles HB et HC.

### <a name="amd-optimizing-cc-compiler"></a>Compilateur AOCC (AMD Optimizing C/C++ Compiler)

Le compilateur AOCC (AMD Optimizing C/C++ Compiler) est un système de compilation offrant des optimisations avancées de haut niveau, ainsi que le multithreading et une prise en charge des processeurs incluant l’optimisation globale, la vectorisation, diverses analyses interprocédurales, des transformations de boucle et la génération de code. Les fichiers binaires du compilateur AOCC conviennent pour les systèmes Linux disposant de la bibliothèque C de GNU (glibc) versions 2.17 et plus. La suite du compilateur se compose d’un compilateur C /C++ (clang), d’un compilateur Fortran (FLANG) et d’un serveur frontal Fortran pour Clang (Dragon Egg).

### <a name="clang"></a>Clang

Clang est un compilateur C, C++ et Objective-C qui gère le prétraitement, l’analyse, l’optimisation, la génération de code, les assemblys et la liaison. Il prend en charge l’indicateur `-march=znver1` pour activer la meilleure génération de code possible et le réglage pour l’architecture AMD x86 basée sur Z.

### <a name="flang"></a>FLANG

Le compilateur FLANG est un ajout récent à la suite AOCC (au mois d’avril 2018). Il est actuellement en préversion, afin que les développeurs puissent le télécharger et le tester. Basée sur Fortran 2008, l’architecture AMD étend la version GitHub de FLANG (https://github.com/flang-compiler/flang). Le compilateur FLANG prend en charge toutes les options de compilateur Clang, ainsi qu’un nombre supplémentaire d’options de compilateur FLANG spécifiques.

### <a name="dragonegg"></a>DragonEgg

DragonEgg est un plug-in de GCC qui remplace les générateurs de code et optimiseurs GCC par ceux du projet LLVM. DragonEgg, fourni avec AOCC, fonctionne avec gcc-4.8.x, et a été testé avec des cibles x86-32/x86-64. Il a été testé avec succès sur différentes plateformes Linux.

GFortran est le serveur frontal réel pour les programmes Fortran responsables du prétraitement, de l’analyse et de l’analyse sémantique générant la représentation intermédiaire GCC SIMPLE. DragonEgg est un plug-in GNU, qui se connecte au flux de compilation GFortran. Il implémente l’API de plug-in GNU. Avec l’architecture du plug-in, DragonEgg devient le pilote du compilateur, qui dirige les différentes phases de compilation.  Après avoir suivi les instructions de téléchargement et d’installation, vous pouvez appeler DragonEgg à l’aide des éléments suivants : 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>Compilateur PGI
Il a été confirmé que PGI Community Edition 17 fonctionne avec AMD EPYC. Une version compilée par PGI de STREAM permet de proposer la bande passante de mémoire complète de la plateforme. Community Edition 18.10 (plus récent, car datant de novembre 2018) devrait également fonctionner avec ce logiciel. Voici un exemple de CLI offrant une compilation optimale avec Intel Compiler :

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Intel Compiler
Il a été confirmé qu’Intel Compiler 18 fonctionne avec AMD EPYC. Voici un exemple de CLI offrant une compilation optimale avec Intel Compiler.

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>Compilateur GCC 
Pour HPC, AMD recommande le compilateur GCC 7.3 ou une version ultérieure. Les versions antérieures, telles que la version 4.8.5 incluse avec RHEL/CentOS 7.4, ne sont pas recommandées. GCC 7.3 et les versions ultérieures offrent des performances nettement supérieures lors de tests avec HPL, HPCG et DGEMM.

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```
</details>

## <a name="next-steps"></a>Étapes suivantes

- Testez vos connaissances avec un [module d’apprentissage sur l’optimisation des applications HPC dans Azure](/learn/modules/optimize-tightly-coupled-hpc-apps/).
- Consultez la [Présentation de la série HBv3](hbv3-series-overview.md) et la [Présentation de la série HC](hc-series-overview.md).
- Consultez les dernières annonces, des exemples de charge de travail HPC et les résultats des performances sur les [blogs de la communauté Azure Compute Tech](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- En savoir plus sur [HPC](/azure/architecture/topics/high-performance-computing/) sur Azure.