---
title: Concepts d’accès en libre-service d’Azure Purview
description: Découvrez les accès en libre-service et la découverte de données dans Azure Purview et explorez comment les utilisateurs peuvent en tirer parti.
author: bjspeaks
ms.author: blessonj
ms.service: purview
ms.subservice: purview-data-policies
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: b4b90a7b2bab6801754121d56fc5b0fd7eca8c61
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097114"
---
# <a name="azure-purview-self-service-data-discovery-and-access-preview"></a>Découverte des données et accès en libre service dans Azure Purview (préversion)

Cet article vous aide à comprendre la gestion de l’accès en libre-service d’Azure Purview.

> [!IMPORTANT]
> La découverte des données et l’accès en libre service dans Azure Purview sont actuellement en préversion. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

## <a name="overview"></a>Vue d’ensemble

La gestion de l’accès en libre-service d’Azure Purview permet aux consommateurs de données de demander l’accès aux données lors de la navigation ou de la recherche de données. Actuellement, nous prenons en charge l’accès aux données en libre-service aux comptes de stockage, aux conteneurs, aux dossiers et aux fichiers.

Un **administrateur de collection** doit mapper un flux de travail libre-service à une collection. Une collection est un regroupement logique de sources de données inscrites dans Azure Purview. **Seules les sources de données qui sont inscrites** pour la gestion de la stratégie autorisent les consommateurs de données à demander l’accès aux données.

## <a name="terminology"></a>Terminologie

* Le **consommateur de données** est la personne qui utilise les données. Par exemple, un analyste de données qui accède aux données de marketing pour la segmentation des clients. Les termes consommateur de données et demandeur des données seront utilisés de façon interchangeable dans ce document.

* Une **collection** est un regroupement logique de sources de données inscrites dans Azure Purview.

* Le **flux de travail en libre-service** est le processus qui est appelé lorsqu’un consommateur de données demande l’accès aux données.

* L’**approbateur** est un groupe de sécurité ou des utilisateurs AAD qui peuvent approuver les demandes d’accès en libre-service.

## <a name="how-to-use-azure-purview-self-service-access-management"></a>Utilisation de la gestion des accès en libre-service d’Azure Purview

Azure Purview permet aux organisations de cataloguer les métadonnées relatives à toutes les ressources de données inscrites. Il permet aux consommateurs de données de rechercher ou d’accéder à la ressource de données requise.  

Avec la gestion de l’accès en libre-service, les consommateurs de données peuvent non seulement trouver des ressources de données, mais également demander l’accès aux ressources de données qui se trouvent dans les sources de données inscrites pour l’intégration de la stratégie. Lorsque le consommateur de données demande l’accès à une ressource de données, le flux de travail d’accès en libre-service associé est déclenché.

Un modèle de workflow en libre-service par défaut est disponible avec le compte Azure Purview créé.
Le modèle par défaut peut être modifié pour ajouter d’autres approbateurs et/ou définir l’adresse e-mail de l’approbateur.

Le rôle de conservateur de données pourra personnaliser le modèle de flux de travail en libre-service par défaut ou utiliser le workflow par défaut sans personnalisation. Les approbateurs de la demande d’accès sont affectés par le conservateur des données. Pour ce faire, le conservateur des données peut utiliser un groupe de distribution de courrier électronique ou l’adresse e-mail d’un approbateur.

Chaque fois qu’un consommateur de données demande l’accès à un jeu de données, la notification est envoyée à l’approbateur du flux de travail. L’approbateur sera en mesure de voir la demande et de l’approuver, soit à partir du portail Purview, soit depuis de la notification par e-mail. Lorsque la demande est approuvée, une stratégie est générée automatiquement et appliquée à la source de données respective.

Le demandeur ou le consommateur de données est informé que la demande a été approuvée.

Si la demande d’accès aux données d’un consommateur de données est refusée, aucune stratégie n’est générée et le consommateur de données est informé que la demande d’accès a été refusée.

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez voir un aperçu de ces fonctionnalités dans votre environnement, suivez le lien ci-dessous.

-  [Inscrivez-vous à la préversion du libre-service Azure Purview](https://aka.ms/opt-in-data-use-policy)
