---
title: Catégories d’entités reconnues par des données personnelles (détection) dans Azure Cognitive Service for Language
titleSuffix: Azure Cognitive Services
description: Découvrez les entités que la fonctionnalité des données personnelles peut reconnaître à partir de textes non structurés.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: article
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-pii, ignite-fall-2021
ms.openlocfilehash: 49db4778dcdb2f4cbe3bff2ac07fc16ece2883f8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096487"
---
# <a name="supported-personally-identifiable-information-pii-entity-categories"></a>Catégories d’entités de données personnelles prises en charge

Utilisez cet article pour rechercher les catégories d’entité qui peuvent être renvoyées par la [fonctionnalité de détection des données personnelles](../how-to-call.md). Cette fonctionnalité exécute un modèle prédictif pour identifier, classer et supprimer les informations sensibles d’un document d’entrée.

La fonctionnalité des données personnelles permet de détecter les informations personnelles (`PII`) et médicales (`PHI`).

## <a name="entity-categories"></a>Catégories d’entité

> [!NOTE]
> Pour détecter des informations médicales protégées, utilisez le paramètre `domain=phi` et la version du modèle `2020-04-01` (ou une version ultérieure).
>
> Par exemple : `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/entities/recognition/pii?domain=phi&model-version=2021-01-15`
 
Les catégories d’entité suivantes sont renvoyées lorsque vous envoyez des requêtes d’API à la fonctionnalité des données personnelles.

## <a name="category-person"></a>Catégorie : Personne

Cette catégorie contient l’entité suivante :

:::row:::
    :::column span="":::
        **Entité**

        Personne

    :::column-end:::
    :::column span="2":::
        **Détails**

        Noms des personnes. Également retourné avec `domain=phi`.

        Pour accéder à cette catégorie d’entité, ajoutez `Person` au `pii-categories` paramètre. `Person`  est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    
    :::column span="":::
      **Langues de document prises en charge**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
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

        Type de tâche ou rôle d’une personne.

        Pour accéder à cette catégorie d’entité, ajoutez `PersonType` au `pii-categories` paramètre. `PersonType`  est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::

    :::column span="":::
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

        Numéros de téléphone (Numéros de téléphone américains et européens uniquement). Également retourné avec `domain=phi`.

        Pour accéder à cette catégorie d’entité, ajoutez `PhoneNumber` au `pii-categories` paramètre. `PhoneNumber`  est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::

    :::column span="":::
      **Langues de document prises en charge**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt` `pt-br`
      
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

        Sociétés, partis politiques, groupes de musique, clubs de sport, organismes gouvernementaux et organisations publiques. Les nationalités et les religions ne sont pas incluses dans ce type d’entité. Également retourné avec `domain=phi`.

        Pour accéder à cette catégorie d’entité, ajoutez `Organization` au `pii-categories` paramètre. `Organization`  est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::

    :::column span="":::
      **Langues de document prises en charge**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
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

        Pour accéder à cette catégorie d’entité, ajoutez `OrganizationMedical` au `pii-categories` paramètre. `OrganizationMedical` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::

    :::column span="":::
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

        Pour accéder à cette catégorie d’entité, ajoutez `OrganizationStockExchange` au `pii-categories` paramètre. `OrganizationStockExchange`  est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::

    :::column span="":::

      `en`   
      
   :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Sports

    :::column-end:::
    :::column span="2":::

        Les organisations liées au sport.

        Pour accéder à cette catégorie d’entité, ajoutez `OrganizationSports` au `pii-categories` paramètre. `OrganizationSports`  est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::

    :::column span="":::

      `en`   
      
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

        Adresse postale complète. Également retourné avec `domain=phi`.

        Pour accéder à cette catégorie d’entité, ajoutez `Address` au `pii-categories` paramètre. `Address`  est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::

    :::column span="":::
      **Langues de document prises en charge**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
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

        Adresses e-mail. Également retourné avec `domain=phi`.
      
        Pour accéder à cette catégorie d’entité, ajoutez `Email` au `pii-categories` paramètre. `Email` est retourné dans la réponse de l’API si elle est détectée.

    :::column-end:::
    :::column span="":::
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

        URL vers des sites web. Également retourné avec `domain=phi`.

        Pour accéder à cette catégorie d’entité, ajoutez `URL` au `pii-categories` paramètre. `URL`  est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::

    :::column span="":::
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

        Adresses IP du réseau. Également retourné avec `domain=phi`.

        Pour accéder à cette catégorie d’entité, ajoutez `IP` au `pii-categories` paramètre. `IP`  est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::

    :::column span="":::
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

        Pour accéder à cette catégorie d’entité, ajoutez `DateTime` au `pii-categories` paramètre. `DateTime`  est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
:::column span="":::
      **Langues de document prises en charge**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Sous-catégories

L’entité de cette catégorie peut contenir les sous-catégories suivantes.

:::row:::
    :::column span="":::
        **Sous-catégorie d’entité**

        Date

    :::column-end:::
    :::column span="2":::
        **Détails**

        Dates du calendrier. Également retourné avec `domain=phi`.

        Pour accéder à cette catégorie d’entité, ajoutez `Date` au `pii-categories` paramètre. `Date`  est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="2":::
      **Langues de document prises en charge**
      
      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
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

        Pour accéder à cette catégorie d’entité, ajoutez `Quantity` au `pii-categories` paramètre. `Quantity`  est retourné dans la réponse de l’API si elle est détectée.
      
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

        Age

    :::column-end:::
    :::column span="2":::
        **Détails**

        Ages. 

        Pour accéder à cette catégorie d’entité, ajoutez `Age` au `pii-categories` paramètre. `Age` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="2":::
        **Langues de document prises en charge**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="azure-information"></a>Informations Azure

Ces catégories d’entités englobent les informations Azure identifiables, notamment les informations d’authentification et les chaînes de connexion. Non retourné avec le paramètre `domain=phi`.

:::row:::
    :::column span="":::
        **Entité**

        Clé d’autorisation Azure DocumentDB 

    :::column-end:::
    :::column span="2":::
        **Détails**

        Clé d’autorisation d’un serveur Azure CosmosDB.   

        Pour accéder à cette catégorie d’entité, ajoutez `AzureDocumentDBAuthKey` au `pii-categories` paramètre. `AzureDocumentDBAuthKey` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::
      **Langues de document prises en charge**

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Chaîne de connexion à la base de données Azure IAAS et chaîne de connexion Azure SQL
        

    :::column-end:::
    :::column span="2":::

        Chaîne de connexion pour une base de données IaaS (infrastructure as a service) Azure et chaîne de connexion SQL.

        Pour accéder à cette catégorie d’entité, ajoutez `AzureIAASDatabaseConnectionAndSQLString` au `pii-categories` paramètre. `AzureIAASDatabaseConnectionAndSQLString` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Chaîne de connexion Azure IoT  

    :::column-end:::
    :::column span="2":::

        Chaîne de connexion pour Azure IoT. 
      
        Pour accéder à cette catégorie d’entité, ajoutez `AzureIoTConnectionString` au `pii-categories` paramètre. `AzureIoTConnectionString` est retourné dans la réponse de l’API si elle est détectée.

    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Mot de passe de paramètre de publication Azure  

    :::column-end:::
    :::column span="2":::

        Mot de passe pour les paramètres de publication Azure.

        Pour accéder à cette catégorie d’entité, ajoutez `AzurePublishSettingPassword` au `pii-categories` paramètre. `AzurePublishSettingPassword` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Chaîne de connexion Azure Cache pour Redis 

    :::column-end:::
    :::column span="2":::

        Chaîne de connexion pour un cache Redis

        Pour accéder à cette catégorie d’entité, ajoutez `AzureRedisCacheString` au `pii-categories` paramètre. `AzureRedisCacheString` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure SAAS 

    :::column-end:::
    :::column span="2":::

        Chaîne de connexion pour SaaS (software as a service) Azure.

        Pour accéder à cette catégorie d’entité, ajoutez `AzureSAS` au `pii-categories` paramètre. `AzureSAS` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Chaîne de connexion Azure Service Bus

    :::column-end:::
    :::column span="2":::

        Chaîne de connexion pour Azure Service Bus.

        Pour accéder à cette catégorie d’entité, ajoutez `AzureServiceBusString` au `pii-categories` paramètre. `AzureServiceBusString` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Clé du compte de Stockage Azure 

    :::column-end:::
    :::column span="2":::

        Clé d’un compte de stockage Azure. 

        Pour accéder à cette catégorie d’entité, ajoutez `AzureStorageAccountKey` au `pii-categories` paramètre. `AzureStorageAccountKey` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Clé du compte de Stockage Azure (générique)

    :::column-end:::
    :::column span="2":::

        Clé générique d’un compte de stockage Azure.

        Pour accéder à cette catégorie d’entité, ajoutez `AzureStorageAccountGeneric` au `pii-categories` paramètre. `AzureStorageAccountGeneric` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Chaîne de connexion SQL Server 

    :::column-end:::
    :::column span="2":::

        Chaîne de connexion pour un ordinateur exécutant SQL Server.

        Pour accéder à cette catégorie d’entité, ajoutez `SQLServerConnectionString` au `pii-categories` paramètre. `SQLServerConnectionString` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::

### <a name="identification"></a>Identification

[!INCLUDE [supported identification entities](../includes/identification-entities.md)]


## <a name="next-steps"></a>Étapes suivantes

* [Présentation de NER](../overview.md)
