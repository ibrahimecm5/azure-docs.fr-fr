---
title: Vue d’ensemble des modèles de base de données Azure Synapse
description: En savoir plus sur les modèles de base de données
author: gsaurer
ms.author: gesaur
ms.service: synapse-analytics
ms.subservice: database-editor
ms.topic: overview
ms.date: 11/02/2021
ms.custom: template-overview, ignite-fall-2021
ms.openlocfilehash: f34ce2b785aa1515db39cb0266963d8da5c85558
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131437425"
---
# <a name="what-is-azure-synapse-database-templates"></a>Présentation des modèles de base de données Azure Synapse

Les données prennent de nombreuses formes lorsqu’elles passent des systèmes sources aux entrepôts de données et aux mini-Data Warehouses dans le but de résoudre les problèmes métier. Les modèles de base de données peuvent faciliter la transformation des données en insights. Les modèles de base de données sont un ensemble de définitions de données techniques et d’entreprise préconçues pour répondre aux besoins d’un secteur en particulier. Ils jouent le rôle de blueprints fournissant des éléments communs dérivés des bonnes pratiques, des réglementations gouvernementales et des exigences complexes en matière de données et d’analytique d’une organisation appartenant à un secteur spécifique. 

Ces blueprints d’informations peuvent être utilisés par les organisations pour planifier et concevoir des solutions de données pour la gouvernance des données, la création de rapports, l’analyse décisionnelle et l’analytique avancée. Les modèles de données fournissent des architectures d’informations d’entreprise intégrées qui peuvent vous aider à implémenter, de manière opportune et prévisible, une architecture de données éprouvée. 

Par exemple, si vous créez une solution de recommandations pour vos clients de la vente au détail, vous aurez besoin d’un blueprint de base pour comprendre ce que le client a acheté et la transaction qui a conduit à l’achat. Vous pouvez également avoir besoin d’informations sur le Store où l’achat a été effectué. Vous devez également savoir si le client fait partie d’un programme de fidélité. Pour ce cas d’utilisation, nous avons besoin des schémas suivants : 

 - Produit 
 - Transaction 
 - TransactionLineItem 
 - Customer 
 - CustomerLoyalty 
 - Magasin 

Vous pouvez configurer ce cas d’usage en sélectionnant les six tables du modèle de base de données de vente au détail. 

[[retail-database-template-example.png|alt=Retail database template example]]

Un modèle de base de données standard répond aux exigences de base d’un secteur spécifique et se compose des éléments suivants : 

 - Un ensemble de [modèles de secteur d’activité](concepts-database-templates.md#business-area-templates).
 - Un ou plusieurs [modèles d’entreprise](concepts-database-templates.md#enterprise-templates).  

## <a name="available-database-templates"></a>Modèles de base de données disponibles 

Il existe actuellement six modèles de base de données dans Azure Synapse Studio que les clients peuvent utiliser pour créer leur base de données Lake. 

 - **Banque** : pour les entreprises qui analysent des données bancaires.
 - **Biens de consommation** : pour les fabricants ou les producteurs de biens achetés et utilisés par les consommateurs.
 - **Gestion des fonds** : pour les entreprises qui gèrent des fonds d’investissement pour les investisseurs.
 - **Assurance vie et rentes** : pour les sociétés qui vendent des assurances vie, des rentes ou les deux.
 - **Assurance propriété et accidents** : pour les entreprises qui vendent des assurances concernant les risques liés à la propriété et les diverses formes de couverture responsabilité.
 - **Vente au détail** : pour les entreprises qui vendent des biens de consommation ou des services aux clients par le biais de plusieurs canaux.

Étant donné que la gestion des émissions carbone est un sujet important dans tous les secteurs, nous avons inclus ces composants dans tous les modèles de base de données disponibles. Ces composants permettent aux entreprises d’effectuer plus facilement le suivi de leurs émissions de gaz à effet serre directes et indirectes afin de créer des rapports.

## <a name="next-steps"></a>Étapes suivantes
Continuez à explorer les fonctionnalités du concepteur de base de données en suivant les liens ci-dessous.
- [Concept de modèles de base de données](concepts-database-templates.md)
- [Démarrage rapide](quick-start-create-lake-database.md)
