---
title: Tableau de prise en charge de sauvegarde SAP HANA
description: Dans cet article,découvrez les scénarios pris en charge et les limitations lorsque vous utilisez Sauvegarde Azure pour sauvegarder des bases de données SAP HANA sur des machines virtuelles Azure.
ms.topic: conceptual
ms.date: 11/10/2021
ms.custom: references_regions
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 310fc7ba4753656015c15001b23e5a953aa76adf
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488446"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Matrice de prise en charge pour la sauvegarde des bases de données SAP HANA sur des machines virtuelles Azure

Le service Sauvegarde Azure prend en charge la sauvegarde des bases de données SAP HANA sur Azure. Cet article résume les scénarios pris en charge et les limitations qui se présentent lorsque vous utilisez Sauvegarde Azure pour sauvegarder des bases de données SAP HANA sur des machines virtuelles Azure.

> [!NOTE]
> La fréquence de sauvegarde de fichier journal peut désormais être définie sur un minimum de 15 minutes. Les sauvegardes de fichiers journaux ne commencent à s’effectuer qu’en cas de réussite d’une sauvegarde complète de la base de données.

## <a name="scenario-support"></a>Prise en charge du scénario

| **Scénario**               | **Configurations prises en charge**                                | **Configurations non prises en charge**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Topologie**               | SAP HANA s’exécutant sur des machines virtuelles Azure Linux uniquement                    | Grandes instances HANA (HLI)                                   |
| **Régions**                   | **Disponibilité générale :**<br> **Amériques**  – USA Centre, USA Est 2, USA Est, USA Centre Nord, USA Centre Sud, USA Ouest 2, USA Ouest 3, USA Centre-Ouest, USA Ouest, Canada Centre, Canada Est, Brésil Sud <br> **Asie-Pacifique** – Australie Centre, Australie Centre 2, Australie Est, Australie Sud-Est, Japon Est, Japon Ouest, Corée Centre, Corée Sud, Asie Est, Asie Sud-Est, Inde Centre, Inde Sud, Inde Ouest, Chine Est, Chine Nord, Chine Est 2, Chine Nord 2 <br> **Europe** – Europe Ouest, Europe Nord, France Centre, Royaume-Uni Sud, Royaume-Uni Ouest, Allemagne Nord, Allemagne Centre-Ouest, Suisse Nord, Suisse Ouest, Suisse Centre Nord, Norvège Est, Norvège Ouest <br> **Moyen-Orient et Afrique** – Afrique du Sud Nord, Afrique du Sud Ouest, Émirats arabes unis Nord, Émirats arabes unis Centre  <BR>  **Régions Azure Government** | France Sud, Allemagne Centre, Allemagne Nord-Est, US Gov IOWA |
| **Versions du système d’exploitation**            | SLES 12 avec SP2, SP3, SP4 et SP5 ; SLES 15 avec SP0, SP1, SP2 et SP3 <br><br>  RHEL 7.4, 7.6, 7.7, 7.9, 8.1, 8.2 et 8.4                |                                             |
| **Versions HANA**          | SDC sur HANA 1.x, MDC sur HANA 2.x SPS04, SPS05 Rév. <= 55 (validé également pour les scénarios prenant en charge le chiffrement)      |                                                            |
| **Chiffrement** | SSLEnforce, chiffrement des données HANA |            |
| **Déploiements HANA**       | SAP HANA sur une machine virtuelle Azure unique - Scale-up uniquement. <br><br> Pour les déploiements à haute disponibilité, les deux nœuds présents sur les deux ordinateurs sont traités individuellement avec des chaînes de données distinctes.               | Montée en charge <br><br> Dans les déploiements à haute disponibilité, la sauvegarde ne bascule pas automatiquement vers le nœud secondaire. La configuration de la sauvegarde doit être effectuée séparément pour chaque nœud.                                           |
| **Instances HANA**         | Une seule SAP HANA instance sur une seule machine virtuelle Azure - montée en puissance uniquement | Plusieurs instances de SAP HANA sur une seule machine virtuelle. Vous ne pouvez protéger qu’une seule de ces instances à la fois.                  |
| **Types de bases de données HANA**    | Conteneur de base de données unique (SDC) sur 1.x, conteneur de bases de données multiples (MDC) sur 2.x | MDC dans HANA 1. x                                              |
| **Taille de la base de données HANA**     | Bases de données HANA de taille <= 8 To (il ne s’agit pas de la taille de la mémoire du système HANA)               |                                                              |
| **Types de sauvegarde**           | Sauvegardes complètes, différentielles, incrémentielles et de fichier journal                          |  Instantanés                                       |
| **Types de restaurations**          | Reportez-vous à la note SAP HANA [1642148](https://launchpad.support.sap.com/#/notes/1642148) pour connaître les types de restauration pris en charge |                                                              |
| **Limites de sauvegarde**          | Jusqu’à 8 To de taille de sauvegarde complète par instance de SAP HANA (limite conditionnelle)         |                                                              |
| **Nombre de sauvegardes complètes par jour**     |   Une sauvegarde planifiée.  <br><br>   Trois sauvegardes à la demande. <br><br> Nous vous recommandons de ne pas déclencher plus de trois sauvegardes par jour. Toutefois, pour permettre à l’utilisateur de réaliser de nouvelles tentatives en cas d’échec, la limite matérielle pour les sauvegardes à la demande est définie à neuf tentatives.  |
| **Configurations spéciales** |                                                              | SAP HANA + Dynamic Tiering <br>  Clonage via LaMa        |

------

>[!NOTE]
>Sauvegarde Azure ne s’ajuste pas automatiquement au changement d’heure lorsque vous sauvegardez une base de données SAP HANA qui s’exécute dans une machine virtuelle Azure.
>
>Modifiez la stratégie manuellement en fonction des besoins.

> [!NOTE]
> Vous pouvez maintenant [superviser les travaux de sauvegarde et de restauration](./sap-hana-db-manage.md#monitor-manual-backup-jobs-in-the-portal) (sur la même machine) déclenchés à partir de clients natifs HANA (SAP HANA Studio/Cockpit/DBA Cockpit) sur le portail Azure.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [sauvegarder des bases de données SAP HANA qui s’exécutent sur des machines virtuelles Azure](./backup-azure-sap-hana-database.md)
* Découvrez comment [restaurer des bases de données SAP HANA qui s’exécutent sur des machines virtuelles Azure](./sap-hana-db-restore.md)
* Découvrez comment [gérer des bases de données SAP HANA sauvegardées à l’aide de Sauvegarde Azure](sap-hana-db-manage.md)
* Découvrez comment [résoudre les problèmes courants lors de la sauvegarde de bases de données SAP HANA](./backup-azure-sap-hana-database-troubleshoot.md)
