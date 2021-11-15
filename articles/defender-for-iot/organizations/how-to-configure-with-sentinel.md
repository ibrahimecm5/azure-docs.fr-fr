---
title: Configurer Azure Sentinel avec Defender pour IoT pour les organisations
description: Explique comment configurer Azure Sentinel pour recevoir des données de votre solution Defender pour IoT.
ms.topic: how-to
ms.date: 11/08/2021
ms.openlocfilehash: 3b1decfa09120732dce372f7dd5ed1bb50c66bef
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132026403"
---
# <a name="connect-your-data-from-defender-for-iot-for-organizations-to-azure-sentinel-public-preview"></a>Connecter vos données de Defender pour IoT pour les organisations à Azure Sentinel (préversion)

Utilisez le connecteur Defender pour IoT pour diffuser tous vos événements Defender pour IoT dans Azure Sentinel.

Cette intégration permet aux organisations de détecter rapidement les attaques multiphases qui, souvent, franchissent les limites de l’informatique et de la formation organisationnelle. En outre, l’intégration de Defender pour IoT aux capacités d’orchestration, d’automatisation et de réponse aux problèmes de sécurité (SOAR) d’Azure Sentinel permet une réponse et une prévention automatisées grâce à des playbooks intégrés optimisés pour la formation organisationnelle.

## <a name="prerequisites"></a>Prérequis

- Autorisations de **lecture** et d’**écriture** sur l’espace de travail sur lequel Azure Sentinel est déployé

- **Defender pour IoT** doit être **activé** sur le ou les hubs IoT correspondants

- Vous devez disposer des autorisations de **Contributeur** sur **l’Abonnement** à connecter

## <a name="connect-to-defender-for-iot"></a>Se connecter à Defender pour IoT

1. Dans Azure Sentinel, sélectionnez **Connecteurs de données**, puis **Defender pour IoT** (qui peut encore être appelé Azure Security Center pour IoT) dans la galerie.

1. Au bas du volet droit, sélectionnez **Ouvrir la page des connecteurs**.

1. Sélectionnez **Se connecter** en regard de chaque abonnement dont vous souhaitez diffuser les alertes et les alertes d’appareil dans Azure Sentinel.

    > [!NOTE]
    > Vous recevrez un message d’erreur si Defender pour IoT n’est pas activé sur au moins un hub IoT au sein de cet abonnement. Activez Defender pour IoT dans le hub IoT pour supprimer l’erreur.

1. Vous pouvez décider si vous souhaitez que les alertes de Defender pour IoT génèrent automatiquement des incidents dans Azure Sentinel. Sous **Créer des incidents**, sélectionnez **Activer** pour permettre à la règle d’analytique par défaut de créer automatiquement des incidents à partir des alertes générées. Cette règle est modifiable sous **Analytique** > **Règles actives**.

> [!NOTE]
> L’actualisation de la liste **Abonnement** peut prendre 10 secondes ou plus après la modification de la connexion.

## <a name="log-analytics-alert-view"></a>Affichage des alertes Log Analytics

Pour utiliser le schéma pertinent dans Log Analytics afin d’afficher les alertes Defender pour IoT :

1. Ouvrez **Journaux** > **SecurityInsights** > **SecurityAlert** ou recherchez **SecurityAlert**.

1. Filtrez les résultats pour afficher uniquement les alertes générées par Defender pour IoT à l’aide du filtre kql suivant :

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
```

### <a name="service-notes"></a>Notes de service

Après la connexion d’un **Abonnement**, les données du hub sont disponibles dans Azure Sentinel environ 15 minutes plus tard.

## <a name="next-steps"></a>Étapes suivantes

Ce document vous a montré comment connecter Defender pour IoT à Azure Sentinel. Pour en savoir plus sur la détection des menaces et l’accès aux données de sécurité, consultez les articles suivants :

- Découvrez comment utiliser Azure Sentinel pour le [Démarrage rapide : prise en main d’Azure Sentinel](../../sentinel/get-visibility.md).
