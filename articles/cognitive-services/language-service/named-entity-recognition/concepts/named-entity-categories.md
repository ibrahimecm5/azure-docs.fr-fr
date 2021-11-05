---
title: Catégories d’entités reconnues par Reconnaissance d’entité nommée dans Azure Cognitive Service for Language
titleSuffix: Azure Cognitive Services
description: Découvrez les entités que la fonctionnalité NER peut reconnaître à partir de texte non structuré.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: article
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-ner, ignite-fall-2021
ms.openlocfilehash: 17c911dadbe0ce741e6e33fd51e0133ff1a67345
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097965"
---
# <a name="supported-named-entity-recognition-ner-entity-categories"></a>Catégories d’entités Reconnaissance d’entité nommée (NER) prises en charge

Utilisez cet article pour rechercher les catégories d’entité qui peuvent être retournées par la [Reconnaissance d’entité nommée](../how-to-call.md) (NER). NER exécute un modèle prédictif pour identifier et classer les entités nommées à partir d’un document d’entrée.

## <a name="category-person"></a>Catégorie : Personne

Cette catégorie contient l’entité suivante :

:::row:::
    :::column span="":::
        **Entité**

        Personne

    :::column-end:::
    :::column span="2":::
        **Détails**

        Noms des personnes.
      
    :::column-end:::
    :::column span="2":::
      **Langues de document prises en charge**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, <br> `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt`-`pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

## <a name="category-persontype"></a>Catégorie : PersonType

Cette catégorie contient l’entité suivante :


:::row:::
    :::column span="":::
        **Entité**

        PersonType

    :::column-end:::
    :::column span="2":::
        **Détails**

        Type de tâche ou rôle d’une personne
      
    :::column-end:::
    :::column span="2":::
      **Langues de document prises en charge**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

## <a name="category-location"></a>Catégorie : Emplacement

Cette catégorie contient l’entité suivante :

:::row:::
    :::column span="":::
        **Entité**

        Emplacement

    :::column-end:::
    :::column span="2":::
        **Détails**

        Points de repère, structures et caractéristiques géographiques et entités géopolitiques naturels et créés par l’homme.
      
    :::column-end:::
    :::column span="2":::
      **Langues de document prises en charge**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt-pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Sous-catégories

L’entité de cette catégorie peut contenir les sous-catégories suivantes.

:::row:::
    :::column span="":::
        **Sous-catégorie d’entité**

        Entité géopolitique (GPE)

    :::column-end:::
    :::column span="2":::
        **Détails**

        Villes, pays/régions, États.
      
    :::column-end:::
    :::column span="2":::
      **Langues de document prises en charge**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Structurel

    :::column-end:::
    :::column span="2":::

        Structures faites par l’homme. 
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Géographique

    :::column-end:::
    :::column span="2":::

        Caractéristiques géographiques et naturelles telles que les rivières, les océans et les déserts.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

## <a name="category-organization"></a>Catégorie : Organisation

Cette catégorie contient l’entité suivante :

:::row:::
    :::column span="":::
        **Entité**

        Organisation

    :::column-end:::
    :::column span="2":::
        **Détails**

        Sociétés, partis politiques, groupes de musique, clubs de sport, organismes gouvernementaux et organisations publiques. Les nationalités et les religions ne sont pas incluses dans ce type d’entité.
      
    :::column-end:::
    :::column span="2":::
      **Langues de document prises en charge**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt-pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Sous-catégories

L’entité de cette catégorie peut contenir les sous-catégories suivantes.

:::row:::
    :::column span="":::
        **Sous-catégorie d’entité**

        Médecine

    :::column-end:::
    :::column span="2":::
        **Détails**

        Sociétés et groupes médicaux.
      
    :::column-end:::
    :::column span="2":::
      **Langues de document prises en charge**

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Bourse

    :::column-end:::
    :::column span="2":::

        Groupes de bourse. 
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Sports

    :::column-end:::
    :::column span="2":::

        Les organisations liées au sport.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

## <a name="category-event"></a>Catégorie : Événement

Cette catégorie contient l’entité suivante :

:::row:::
    :::column span="":::
        **Entité**

        Événement

    :::column-end:::
    :::column span="2":::
        **Détails**

        Événements historiques, sociaux et naturels.
      
    :::column-end:::
    :::column span="2":::
      **Langues de document prises en charge**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt` et `pt-br`  
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Sous-catégories

L’entité de cette catégorie peut contenir les sous-catégories suivantes.

:::row:::
    :::column span="":::
        **Sous-catégorie d’entité**

        Culturel

    :::column-end:::
    :::column span="2":::
        **Détails**

        Événements culturels et jours fériés.
      
    :::column-end:::
    :::column span="2":::
      **Langues de document prises en charge**

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Natural

    :::column-end:::
    :::column span="2":::

        Événements naturels.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Sports

    :::column-end:::
    :::column span="2":::

        Événements sportifs.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

## <a name="category-product"></a>Catégorie : Produit

Cette catégorie contient l’entité suivante :

:::row:::
    :::column span="":::
        **Entité**

        Produit

    :::column-end:::
    :::column span="2":::
        **Détails**

        Objets physiques de différentes catégories.
      
    :::column-end:::
    :::column span="2":::
      **Langues de document prises en charge**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::


#### <a name="subcategories"></a>Sous-catégories

L’entité de cette catégorie peut contenir les sous-catégories suivantes.

:::row:::
    :::column span="":::
        **Sous-catégorie d’entité**

        Calcul des produits
    :::column-end:::
    :::column span="2":::
        **Détails**

        Calcul des produits.
      
    :::column-end:::
    :::column span="2":::
      **Langues de document prises en charge**

      `en`   
      
   :::column-end:::
:::row-end:::

## <a name="category-skill"></a>Catégorie : Compétence

Cette catégorie contient l’entité suivante :

:::row:::
    :::column span="":::
        **Entité**

        Compétence

    :::column-end:::
    :::column span="2":::
        **Détails**

        Une capacité, une compétence ou une expertise.
      
    :::column-end:::
    :::column span="2":::
      **Langues de document prises en charge**

      `en` , `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br` 
      
   :::column-end:::
:::row-end:::

## <a name="category-address"></a>Catégorie : Adresse

Cette catégorie contient l’entité suivante :

:::row:::
    :::column span="":::
        **Entité**

        Adresse

    :::column-end:::
    :::column span="2":::
        **Détails**

        Adresse postale complète.
      
    :::column-end:::
    :::column span="2":::
      **Langues de document prises en charge**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

## <a name="category-phonenumber"></a>Catégorie : PhoneNumber

Cette catégorie contient l’entité suivante :

:::row:::
    :::column span="":::
        **Entité**

        PhoneNumber

    :::column-end:::
    :::column span="2":::
        **Détails**

        Numéros de téléphone (Numéros de téléphone américains et européens uniquement).
      
    :::column-end:::
    :::column span="2":::
      **Langues de document prises en charge**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt` `pt-br`
      
   :::column-end:::
:::row-end:::

## <a name="category-email"></a>Catégorie : E-mail

Cette catégorie contient l’entité suivante :

:::row:::
    :::column span="":::
        **Entité**

        E-mail

    :::column-end:::
    :::column span="2":::
        **Détails**

        Adresses e-mail.
      
    :::column-end:::
    :::column span="2":::
      **Langues de document prises en charge**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

## <a name="category-url"></a>Catégorie : URL

Cette catégorie contient l’entité suivante :

:::row:::
    :::column span="":::
        **Entité**

        URL

    :::column-end:::
    :::column span="2":::
        **Détails**

        URL vers des sites web. 
      
    :::column-end:::
    :::column span="2":::
      **Langues de document prises en charge**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

## <a name="category-ip"></a>Catégorie : IP

Cette catégorie contient l’entité suivante :

:::row:::
    :::column span="":::
        **Entité**

        IP

    :::column-end:::
    :::column span="2":::
        **Détails**

        Adresses IP du réseau. 
      
    :::column-end:::
    :::column span="2":::
      **Langues de document prises en charge**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

## <a name="category-datetime"></a>Catégorie : DateTime

Cette catégorie contient les entités suivantes :

:::row:::
    :::column span="":::
        **Entité**

        DateTime

    :::column-end:::
    :::column span="2":::
        **Détails**

        Dates et heures du jour. 
      
    :::column-end:::
    :::column span="2":::
      **Langues de document prises en charge**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

Les entités de cette catégorie peuvent contenir les sous-catégories suivantes

#### <a name="subcategories"></a>Sous-catégories

L’entité de cette catégorie peut contenir les sous-catégories suivantes.

:::row:::
    :::column span="":::
        **Sous-catégorie d’entité**

        Date

    :::column-end:::
    :::column span="2":::
        **Détails**

        Dates du calendrier.
      
    :::column-end:::
    :::column span="2":::
      **Langues de document prises en charge**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Temps

    :::column-end:::
    :::column span="2":::

        Heures de la journée.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        DateRange

    :::column-end:::
    :::column span="2":::

        Plages de dates.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        TimeRange

    :::column-end:::
    :::column span="2":::

        Intervalles de temps.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Duration

    :::column-end:::
    :::column span="2":::

        Durées.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Définissez

    :::column-end:::
    :::column span="2":::

        Définie, répétée tant de fois.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

## <a name="category-quantity"></a>Catégorie : Quantité

Cette catégorie contient les entités suivantes :

:::row:::
    :::column span="":::
        **Entité**

        Quantité

    :::column-end:::
    :::column span="2":::
        **Détails**

        Nombres et quantités numériques.
      
    :::column-end:::
    :::column span="2":::
      **Langues de document prises en charge**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Sous-catégories

L’entité de cette catégorie peut contenir les sous-catégories suivantes.

:::row:::
    :::column span="":::
        **Sous-catégorie d’entité**

        Nombre

    :::column-end:::
    :::column span="2":::
        **Détails**

        Nombres.
      
    :::column-end:::
    :::column span="2":::
      **Langues de document prises en charge**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Pourcentage

    :::column-end:::
    :::column span="2":::

        Pourcentages
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Nombres ordinaux

    :::column-end:::
    :::column span="2":::

        Nombres ordinaux.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Age

    :::column-end:::
    :::column span="2":::

        Ages.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Devise

    :::column-end:::
    :::column span="2":::

        Devises
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Dimensions

    :::column-end:::
    :::column span="2":::

        Dimensions et mesures.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Température

    :::column-end:::
    :::column span="2":::

        Températures.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::


## <a name="next-steps"></a>Étapes suivantes

* [Présentation de NER](../overview.md)
