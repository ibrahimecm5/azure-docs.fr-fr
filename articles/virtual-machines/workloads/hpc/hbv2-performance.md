---
title: Performances de la taille de machine virtuelle de la série HBv2
description: En savoir plus sur les résultats des tests de performances pour les machines virtuelles de la série HBv2 dans Azure.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/28/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 81ac751ca209c7741e3bf55082f7b041ff13022d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132318158"
---
# <a name="hbv2-series-virtual-machine-sizes"></a>Tailles des machines virtuelles de la série HBv2

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

Plusieurs tests de performance ont été exécutés sur des machines virtuelles de [HBv2](../../hbv2-series.md) taille. Voici certains des résultats obtenus.


| Charge de travail                                        | HBv2                                                              |
|-------------------------------------------------|-------------------------------------------------------------------|
| STREAM Triad                                    | 350 Go/s (21-23 Go/s par CCX)                                     |
| Linpack hautes performances (HPL)                  | 4 téraflops (Rpeak, FP64), 8 téraflops (Rmax, FP32)               |
| Latence et bande passante RDMA                        | 1,2 microsecondes, 190 Go/s                                        |
| FIO sur des disques SSD NVMe locaux                           | 2,7 Go/s lectures, 1,1 Go/s écrits ; lectures d’e/s par seconde de 102 000, 115 écritures IOPS |
| IOR sur 8 * SSD Premium Azure (P40 Managed Disks, RAID0) **  | 1,3 Go/s lectures, 2,5 Go/écriture ; lectures d’e/s par seconde 101k, écritures d’e/s 105k |


## <a name="mpi-latency"></a>Latence MPI

Le test de latence MPI de la suite de microbenchmark OSU est exécuté. Les exemples de scripts se trouvent sur [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh).

```bash
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency
```

:::image type="content" source="./media/latency-hbv2.png" alt-text="Latence MPI sur Azure HB.":::

## <a name="mpi-bandwidth"></a>Bande passante MPI

Le test de bande passante MPI de la suite de microbenchmark OSU est exécuté. Les exemples de scripts se trouvent sur [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh).

```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```

:::image type="content" source="./media/bandwidth-hbv2.png" alt-text="Bande passante MPI sur Azure HB.":::

## <a name="mellanox-perftest"></a>Mellanox Perftest

Le [package Mellanox Peretpt](https://community.mellanox.com/s/article/perftest-package) comporte de nombreux tests InfiniBand tels que la latence (ib_send_lat) et la bande passante (ib_send_bw). Vous trouverez ci-dessous un exemple de commande. 


```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```


## <a name="next-steps"></a>Étapes suivantes

- Consultez les dernières annonces, des exemples de charge de travail HPC et les résultats des performances sur les [blogs de la communauté Azure Compute Tech](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Pour une vision plus globale de l’architecture d’exécution des charges de travail HPC, consultez [Calcul haute performance (HPC) sur Azure](/azure/architecture/topics/high-performance-computing/).