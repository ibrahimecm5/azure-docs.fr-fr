---
title: 'Démarrage rapide : Créer une configuration any-to-any à l’aide d’un modèle ARM'
titleSuffix: Azure Virtual WAN
description: Ce guide de démarrage rapide vous montre comment créer une configuration any-to-any à l’aide d’un modèle Azure Resource Manager (modèle ARM).
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: quickstart
ms.date: 02/02/2021
ms.author: cherylmc
ms.custom: subject-armqs
ms.openlocfilehash: 79b78af6e0ff1d23ea47b150dfd6c185e3d9df18
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132323117"
---
# <a name="quickstart-create-an-any-to-any-configuration-using-an-arm-template"></a>Démarrage rapide : Créer une configuration any-to-any à l’aide d’un modèle ARM

Ce guide de démarrage rapide explique comment utiliser un modèle Azure Resource Manager (modèle ARM) pour créer un scénario any-to-any dans lequel un spoke quelconque peut atteindre n’importe quel autre spoke.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Si votre environnement remplit les prérequis et que vous êtes déjà familiarisé avec l’utilisation des modèles ARM, sélectionnez le bouton **Déployer sur Azure**. Le modèle s’ouvre dans le portail Azure.

[![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2fquickstarts%2fmicrosoft.network%2fvirtual-wan-with-all-gateways%2fazuredeploy.json)

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
* Des données de certificat de clé publique sont nécessaires pour cette configuration. Des exemples de données sont fournis dans l’article. Cependant, ces données sont fournies uniquement pour répondre aux besoins du modèle, qui vise à créer une passerelle P2S. Une fois que le modèle a abouti et que les ressources ont été déployées, vous devez mettre à jour ce champ avec les données de votre propre certificat pour rendre la configuration opérationnelle. Consultez [Certificats VPN utilisateur](certificates-point-to-site.md#cer).

## <a name="review-the-template"></a><a name="review"></a>Vérifier le modèle

Le modèle utilisé dans ce démarrage rapide est tiré des [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/virtual-wan-with-all-gateways). Le modèle utilisé pour cet article est trop long pour être affiché ici. Pour voir le modèle, consultez [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.network/virtual-wan-with-all-gateways/azuredeploy.json).

Dans ce guide de démarrage rapide, vous allez créer un déploiement multihub Azure Virtual WAN, avec toutes les passerelles et les connexions de réseau virtuel. La liste des paramètres d’entrée a été volontairement réduite au minimum. Le schéma d’adressage IP peut être modifié en rectifiant les variables à l’intérieur du modèle. Le scénario est expliqué plus en détail dans l’article [Scénario Any-to-any](scenario-any-to-any.md).

:::image type="content" source="./media/routing-scenarios/any-any/figure-1.png" alt-text="Architecture de déploiement":::

Ce modèle crée un environnement Azure Virtual WAN entièrement fonctionnel avec les ressources suivantes :

* Deux hubs distincts dans des régions différentes
* Quatre réseaux virtuels (VNet) Azure
* Deux connexions de réseau virtuel pour chaque hub VWAN
* Une passerelle VPN de point à site (P2S) dans chaque hub
* Une passerelle VPN de site à site (P2S) dans chaque hub
* Une passerelle ExpressRoute dans chaque hub

Plusieurs ressources Azure sont définies dans le modèle :

* [**Microsoft.Network/virtualwans**](/azure/templates/microsoft.network/virtualwans)
* [**Microsoft.Network/virtualhubs**](/azure/templates/microsoft.network/virtualhubs)
* [**Microsoft.Network/virtualnetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft.Network/hubvirtualnetworkconnections**](/azure/templates/microsoft.network/virtualhubs/hubvirtualnetworkconnections)
* [**Microsoft.Network/p2svpngateways**](/azure/templates/microsoft.network/p2svpngateways)
* [**Microsoft.Network/vpngateways**](/azure/templates/microsoft.network/vpngateways) 
* [**Microsoft.Network/expressroutegateways**](/azure/templates/microsoft.network/expressroutegateways)
* [**Microsoft.Network/vpnserverconfigurations**](/azure/templates/microsoft.network/vpnserverconfigurations)

>[!NOTE]
> Ce modèle ARM ne crée pas les ressources côté client nécessaires pour la connectivité hybride. Après avoir déployé le modèle, il vous reste à créer et configurer les clients VPN P2S, les branches VPN (sites locaux) et à connecter les circuits ExpressRoute.
>

Pour obtenir d’autres modèles, consultez [Modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a><a name="deploy"></a>Déployer le modèle

Pour déployer ce modèle convenablement, vous devez utiliser le bouton **Déployer sur Azure** dans le portail Azure, plutôt que d’autres méthodes, pour les raisons suivantes :

* Pour créer la configuration P2S, vous devez charger les données du certificat racine. Le champ de données n’accepte pas les données de certificat quand PowerShell ou l’interface CLI est utilisé.
* Ce modèle ne fonctionne pas correctement avec Cloud Shell en raison du chargement des données du certificat.
* Par ailleurs, vous pouvez facilement modifier le modèle et les paramètres sur le portail pour prendre en charge des plages d’adresses IP et d’autres valeurs.

1. Cliquez sur **Déployer dans Azure**.

   [![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2fquickstarts%2fmicrosoft.network%2fvirtual-wan-with-all-gateways%2fazuredeploy.json)
1. Pour afficher le modèle, cliquez sur **Modifier le modèle**. Dans cette page, vous pouvez ajuster certaines valeurs comme l’espace d’adressage ou le nom de certaines ressources. Cliquez sur **Enregistrer** pour enregistrer vos modifications ou sur **Abandonner**.
1. Dans la page du modèle, entrez les valeurs. Pour ce modèle, les données du certificat public P2S sont nécessaires. Si vous envisagez cet article comme exercice, vous pouvez utiliser les données suivantes de ce fichier .cer comme données d’exemple pour les deux hubs. Une fois que le modèle s’exécute et que le déploiement est terminé, pour utiliser la configuration P2S, vous devez remplacer ces informations par les [données de certificat](certificates-point-to-site.md#cer) de la clé publique pour votre propre déploiement.

   ```certificate-data
   MIIC5zCCAc+gAwIBAgIQGxd3Av1q6LJDZ71e3TzqcTANBgkqhkiG9w0BAQsFADAW
   MRQwEgYDVQQDDAtQMlNSb290Q2VydDAeFw0yMDExMDkyMjMxNTVaFw0yMTExMDky
   MjUxNTVaMBYxFDASBgNVBAMMC1AyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEF
   AAOCAQ8AMIIBCgKCAQEA33fFra/E0YmGuXLKmYcdvjsYpKwQmw8DjjDkbwhE9jcc
   Dp50e7F1P6Rxo1T6Hm3dIhEji+0QkP4Ie0XPpw0eW77+RWUiG9XJxGqtJ3Q4tyRy
   vBfsHORcqMlpV3VZOXIxrk+L/1sSm2xAc2QGuOqKaDNNoKmjrSGNVAeQHigxbTQg
   zCcyeuhFxHxAaxpW0bslK2hEZ9PhuAe22c2SHht6fOIDeXkadzqTFeV8wEZdltLr
   6Per0krxf7N2hFo5Cfz0KgWlvgdKLL7dUc9cjHo6b6BL2pNbLh8YofwHQOQbwt6H
   miAkEnx1EJ5N8AWuruUTByR2jcWyCnEAUSH41+nk4QIDAQABozEwLzAOBgNVHQ8B
   Af8EBAMCAgQwHQYDVR0OBBYEFJMgnJSYHH5AJ+9XB11usKRwjbjNMA0GCSqGSIb3
   DQEBCwUAA4IBAQBOy8Z5FBd/nvgDcjvAwNCw9h5RHzgtgQqDP0qUjEqeQv3ALeC+
   k/F2Tz0OWiPEzX5N+MMrf/jiYsL2exXuaPWCF5U9fu8bvs89GabHma8MGU3Qua2x
   Imvt0whWExQMjoyU8SNUi2S13fnRie9ZlSwNh8B/OIUUEtVhQsd4OfuZZFVH4xGp
   ibJMSMe5JBbZJC2tCdSdTLYfYJqrLkVuTjynXOjmz2JXfwnDNqEMdIMMjXzlNavR
   J8SNtAoptMOK5vAvlySg4LYtFyXkl0W0vLKIbbHf+2UszuSCijTUa3o/Y1FoYSfi
   eJH431YTnVLuwdd6fXkXFBrXDhjNsU866+hE
   ```

1. Quand vous avez fini d’entrer les valeurs, sélectionnez **Vérifier + créer**.
1. Dans la page **Vérifier + créer**, une fois la validation réussie, sélectionnez **Créer**.
1. Le déploiement prend environ 75 minutes. Vous pouvez suivre sa progression dans la page **Vue d’ensemble** du modèle.  Si vous fermez le portail, le déploiement se poursuit.

   :::image type="content" source="./media/quickstart-any-to-any-template/template.png" alt-text="Exemple d’un déploiement ayant abouti":::

## <a name="validate-the-deployment"></a><a name="validate"></a>Valider le déploiement

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez **Groupes de ressources** dans le volet gauche.
1. Sélectionnez le groupe de ressources que vous avez créé dans la section précédente. Dans la page **Vue d’ensemble**, vous voyez quelque chose qui ressemble à cet exemple : :::image type="content" source="./media/quickstart-any-to-any-template/resources.png" alt-text="Exemple de ressources" lightbox="./media/quickstart-any-to-any-template/resources.png":::

1. Cliquez sur le WAN virtuel pour afficher les hubs. Dans la page du WAN virtuel, cliquez sur chaque hub pour afficher les connexions et d’autres informations sur le hub en question.
   :::image type="content" source="./media/quickstart-any-to-any-template/hub.png" alt-text="Exemple de hubs" lightbox="./media/quickstart-any-to-any-template/hub.png":::

## <a name="complete-the-hybrid-configuration"></a><a name="complete"></a>Terminer la configuration hybride

Le modèle ne configure pas tous les paramètres nécessaires à un réseau hybride. Vous devez compléter les configurations et les paramètres suivants en fonction de vos besoins.

* [Configurer les branches VPN – sites locaux](virtual-wan-site-to-site-portal.md#site)
* [Effectuer la configuration VPN P2S](virtual-wan-point-to-site-portal.md)
* [Connecter les circuits ExpressRoute](virtual-wan-expressroute-portal.md)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’avez plus besoin des ressources que vous avez créées, supprimez-les. Certaines des ressources Virtual WAN doivent être supprimées dans un certain ordre en raison des dépendances. La suppression peut prendre environ 30 minutes.

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Effectuer la configuration VPN P2S](virtual-wan-point-to-site-portal.md)

> [!div class="nextstepaction"]
> [Configurer les branches VPN – sites locaux](virtual-wan-site-to-site-portal.md#site)

> [!div class="nextstepaction"]
> [Connecter les circuits ExpressRoute](virtual-wan-expressroute-portal.md)
