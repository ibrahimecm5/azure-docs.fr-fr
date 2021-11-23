---
title: Configurer des paramètres personnalisés
description: Configurez des paramètres qui s’appliquent à l’ensemble de l’environnement Azure App Service. Découvrez comment faire cela avec des modèles Azure Resource Manager.
author: madsd
ms.assetid: 1d1d85f3-6cc6-4d57-ae1a-5b37c642d812
ms.topic: tutorial
ms.date: 11/03/2021
ms.author: madsd
ms.custom: mvc, seodec18
ms.openlocfilehash: c90357a77d8ea95675fb17ecbde5c8dfb94b49ea
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524984"
---
# <a name="custom-configuration-settings-for-app-service-environments"></a>Paramètres de configuration personnalisés pour les environnements App Service

## <a name="overview"></a>Vue d’ensemble
Les environnements App Service (ASE) étant isolés pour chaque client, certains paramètres de configuration peuvent être appliqués exclusivement à des environnements App Service. Cet article décrit les différentes personnalisations pour les environnements App Service disponibles.

Si vous n’avez pas d’environnement App Service, consultez [Guide pratique pour créer un environnement ASEv3](./creation.md).

Vous pouvez stocker les personnalisations de l’environnement App Service (App Service Environment) à l’aide d’un tableau dans le nouvel attribut **clusterSettings** . Cet attribut se trouve dans le dictionnaire des « Propriétés » de l’entité Azure Resource Manager *hostingEnvironments* .

L’extrait de code abrégé de modèle Resource Manager suivant indique l’attribut **clusterSettings** :

```json
"resources": [
{
    "apiVersion": "2021-03-01",
    "type": "Microsoft.Web/hostingEnvironments",
    "name": ...,
    "location": ...,
    "properties": {
        "clusterSettings": [
            {
                "name": "nameOfCustomSetting",
                "value": "valueOfCustomSetting"
            }
        ],
        "internalLoadBalancingMode": ...,
        etc...
    }
}
```

L’attribut **clusterSettings** peut être inclus dans un modèle Resource Manager pour mettre à jour l’environnement App Service.

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>Utilisation de l’Explorateur de ressources Azure pour mettre à jour un environnement App Service
Vous pouvez également mettre à jour l’environnement App Service à l’aide de [l’Explorateur de ressources Azure](https://resources.azure.com).  

1. Dans Explorateur de ressources, accédez au nœud de l’environnement App Service (**subscriptions** >  **{votre abonnement}**  > **resourceGroups** >  **{votre groupe de ressources}**  > **providers** > **Microsoft.Web** > **hostingEnvironments**). Cliquez ensuite sur l’environnement App Service spécifique que vous souhaitez mettre à jour.
2. Dans le volet de droite, cliquez sur **Lecture/écriture** dans la barre d’outils du haut pour autoriser la modification interactive dans l’Explorateur de ressources.  
3. Cliquez sur le bouton bleu **Modifier** pour permettre la modification du modèle Resource Manager.
4. Faites défiler jusqu'au bas du panneau de droite. L’attribut **clusterSettings** , dont vous pouvez entrer ou mettre à jour la valeur, se trouve tout en bas.
5. Entrez (ou copiez et collez) le tableau de valeurs de configuration souhaité dans l’attribut **clusterSettings** .  
6. Cliquez sur le bouton vert **PUT** situé en haut du panneau de droite pour valider la modification apportée à l’environnement App Service.

Quelle que soit la manière dont vous soumettez les modifications, environ 30 minutes (multipliées par le nombre de serveurs frontaux présents dans l’environnement App Service) pour qu’elles prennent effet.
Par exemple, si un environnement App Service a quatre serveurs frontaux, la mise à jour de la configuration prendra environ deux heures. Tant que la modification de la configuration n’est pas déployée, aucune autre opération de mise à l’échelle ou de modification de configuration n’est possible dans l’environnement App Service.

## <a name="enable-internal-encryption"></a>Activer le chiffrement interne

App Service Environment fonctionne comme un système de boîte noire dans laquelle vous ne pouvez pas voir les composants internes ou la communication au sein du système. Pour activer un débit plus élevé, le chiffrement n’est pas activé par défaut entre les composants internes. Le système est sécurisé, car le trafic est inaccessible à des fins de supervision. Si toutefois vous avez une exigence de conformité qui requiert un chiffrement complet du chemin de données de bout en bout, il existe un moyen d’activer le chiffrement du chemin de données complet avec un clusterSetting.

```json
"clusterSettings": [
    {
        "name": "InternalEncryption",
        "value": "true"
    }
],
```
L’affectation de la valeur true à InternalEncryption chiffre le trafic réseau interne de votre ASE entre les front-ends et les Workers, chiffre le fichier d’échange et chiffre les disques des Workers. Une fois le clusterSetting InternalEncryption activé, il peut y avoir un impact sur les performances de votre système. Quand vous apportez la modification pour activer InternalEncryption, votre ASE sera dans un état instable jusqu’à ce que la modification soit entièrement propagée. La propagation complète de la modification peut prendre quelques heures, en fonction du nombre d’instances présentes dans votre ASE. Nous vous recommandons vivement de ne pas activer InternalEncryption sur un ASE pendant qu’il est en cours d’utilisation. Si vous avez besoin d’activer InternalEncryption sur un ASE activement utilisé, nous vous recommandons vivement de diriger le trafic vers un environnement de sauvegarde jusqu’à ce que l’opération soit terminée. 


## <a name="disable-tls-10-and-tls-11"></a>Désactiver TLS 1.0 et TLS 1.1

Si vous souhaitez gérer les paramètres TLS application par application, vous pouvez utiliser les instructions fournies dans la documentation [Appliquer des versions TLS](../configure-ssl-bindings.md#enforce-tls-versions). 

Si vous souhaitez désactiver tout le trafic TLS 1.0 et TLS 1.1 entrant pour toutes les applications dans un environnement ASE, vous pouvez définir l’entrée **clusterSettings** suivante :

```json
"clusterSettings": [
    {
        "name": "DisableTls1.0",
        "value": "1"
    }
],
```

Le nom du paramètre indique 1.0, mais quand il est configuré, il désactive TLS 1.0 et TLS 1.1.

## <a name="change-tls-cipher-suite-order"></a>Modifier l’ordre des suites de chiffrement TLS
L’ASE prend en charge la modification de la suite de chiffrement par défaut. L’ensemble de chiffrements par défaut est le même que celui utilisé dans le service multilocataire. La modification des suites de chiffrement affecte l’intégralité d’un déploiement App Service, ce qui est possible uniquement dans l’ASE monolocataire. Deux suites de chiffrement sont requises pour un ASE : TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 et TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256. Si vous souhaitez faire fonctionner votre ASE avec l’ensemble de suites de chiffrement le plus puissant et le plus minimal, utilisez uniquement les deux chiffrements requis. Pour configurer votre ASE de manière à ce qu’il utilise uniquement les chiffrements dont il a besoin, modifiez **clusterSettings** comme indiqué ci-dessous. 

```json
"clusterSettings": [
    {
        "name": "FrontEndSSLCipherSuiteOrder",
        "value": "TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384,TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256"
    }
],
```

> [!WARNING]
> Si des valeurs incorrectes sont définies pour la suite de chiffrement et incompréhensibles pour SChannel, l’ensemble de la communication TLS avec votre serveur peut cesser de fonctionner. Dans ce cas, vous devrez supprimer l’entrée *FrontEndSSLCipherSuiteOrder* des **clusterSettings** et envoyer le modèle Resource Manager mis à jour pour rétablir les paramètres de suite de chiffrement par défaut.  Utilisez cette fonctionnalité avec précaution.

## <a name="get-started"></a>Bien démarrer
Le site de modèles Azure Quickstart Resource Manager comprend un modèle dont la définition de base permet de [créer un environnement App Service](https://azure.microsoft.com/resources/templates/web-app-asp-app-on-asev3-create/).
