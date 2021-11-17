---
title: Langage Bicep pour le déploiement de ressources Azure
description: Décrit le langage Bicep pour le déploiement d’infrastructure sur Azure. Il offre une expérience de création améliorée par rapport à l’utilisation de JSON pour développer des modèles.
ms.topic: conceptual
ms.date: 11/12/2021
ms.openlocfilehash: 028ec297aa30bf6bfd397fee83b8b4525a66bdbc
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484293"
---
# <a name="what-is-bicep"></a>Qu’est-ce que Bicep ?

Bicep est un langage spécifique à un domaine (DSL) qui utilise la syntaxe déclarative pour déployer des ressources Azure. Il fournit une syntaxe concise, une cohérence des types fiable et une prise en charge de la réutilisation du code. Nous pensons que Bicep offre la meilleure expérience de création pour vos solutions d’infrastructure en tant que code dans Azure.

Vous pouvez utiliser Bicep au lieu de JSON pour développer vos modèles Azure Resource Manager (modèles ARM). La syntaxe JSON pour la création d’un modèle ARM peut être détaillée et nécessiter une expression complexe. La syntaxe Bicep réduit cette complexité et améliore l’expérience de développement. Bicep est une abstraction transparente sur un modèle ARM JSON et ne perd aucune des fonctionnalités de modèle JSON. Pendant le déploiement, l’interface de ligne de commande Bicep convertit un fichier Bicep en JSON de modèle ARM.

Bicep n’est pas conçu comme un langage de programmation général pour l’écriture d’applications. Un fichier Bicep déclare des ressources Azure et des propriétés de ressources, sans écrire de séquence de commandes de programmation pour créer des ressources.

Les types de ressources, les versions d’API et les propriétés qui sont valides dans un modèle ARM sont valides dans un fichier Bicep.

Pour suivre l’état du travail Bicep, consultez le [référentiel de projet Bicep](https://github.com/Azure/bicep).

Pour en savoir plus sur Bicep, consultez la vidéo suivante.

> [!VIDEO https://www.youtube.com/embed/sc1kJfcRQgY]

## <a name="get-started"></a>Bien démarrer

Pour commencer à utiliser Bicep :

1. **Installez les outils**. Consultez [Configurer des environnements de développement et de déploiement Bicep](./install.md). Vous pouvez également utiliser le [référentiel VS Code Devcontainer/Codespaces](https://github.com/Azure/vscode-remote-try-bicep) pour obtenir un environnement de création préconfiguré.
2. **Effectuez le [démarrage rapide](./quickstart-create-bicep-use-visual-studio-code.md) et les [modules Microsoft Learn sur Bicep](./learn-bicep.md)** .

Pour décompiler un modèle ARM existant au format Bicep, consultez [Décompiler des modèles ARM au format Bicep](./decompile.md). Le [Terrain de jeu Bicep](https://bicepdemo.z22.web.core.windows.net/) vous permet d’afficher côte à côte un fichier Bicep et son équivalent JSON.

Pour découvrir les ressources disponibles dans votre fichier Bicep, consultez [Informations de référence sur les ressources Bicep](/azure/templates/).

Vous trouverez d’autres exemples Bicep dans le [dépôt GitHub Bicep](https://github.com/Azure/bicep/tree/main/docs/examples).

## <a name="benefits-of-bicep-versus-other-tools"></a>Avantages de Bicep par rapport à d’autres outils

Bicep offre les avantages suivants par rapport aux autres options :

- **Support pour tous les types de ressources et versions d’API** : Bicep prend immédiatement en charge toutes les versions de préversion et de disponibilité générale (GA) pour les services Azure. Dès qu’un fournisseur de ressources introduit de nouveaux types de ressources et de versions d’API, vous pouvez les utiliser dans votre fichier Bicep. Vous ne devez pas attendre la mise à jour des outils avant d’utiliser les nouveaux services.
- **Syntaxe simple** : par rapport au modèle JSON équivalent, les fichiers Bicep sont plus concis et plus faciles à lire. Bicep ne nécessite aucune connaissance préalable des langages de programmation. La syntaxe Bicep est déclarative et spécifie les ressources et les propriétés de ressource que vous souhaitez déployer.
- **Expérience de création** : lorsque vous utilisez VS code pour créer vos fichiers Bicep, vous bénéficiez d’une expérience de création de première classe. L’éditeur fournit une validation enrichie de type sécurisé, IntelliSense et de la syntaxe.
- **Modularité** : vous pouvez diviser votre code Bicep en parties gérables à l’aide de [modules](./modules.md). Le module déploie un ensemble de ressources associées. Les modules vous permettent de réutiliser le code et de simplifier le développement. Ajoutez le module à un fichier Bicep chaque fois que vous devez déployer ces ressources.
- **Intégration aux services Azure** : Bicep est intégré aux services Azure, tels qu’Azure Policy, les specs de modèle et les blueprints.
- **Aucun fichier d’état ou état à gérer** : tous les états sont stockés dans Azure. Les utilisateurs peuvent collaborer et être assurés que leurs mises à jour sont traitées comme prévu. Utilisez l’[opération de simulation](./deploy-what-if.md) pour afficher des modifications avant de déployer votre modèle.
- **Aucuns frais et open source** : Bicep est complètement gratuit. Vous ne devez pas payer les fonctionnalités Premium. Elles sont également prises en charge par le support Microsoft.

## <a name="bicep-improvements"></a>Améliorations apportées à Bicep

Bicep offre une syntaxe plus simple et plus concise que JSON. Vous n’utilisez pas d’expressions entre crochets `[...]`. Au lieu de cela, vous appelez directement des fonctions, et récupérez des valeurs à partir de paramètres et de variables. Vous attribuez à chaque ressource déployée un nom symbolique qui facilite le référencement de cette ressource dans votre modèle.

Par exemple, le code JSON suivant retourne une valeur de sortie à partir d’une propriété de ressource.

```json
"outputs": {
  "hostname": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
    },
}
```

L’expression de sortie équivalente dans Bicep est plus facile à écrire. L’exemple suivant retourne la même propriété en utilisant le nom symbolique **publicIP** pour une ressource définie dans le modèle :

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

Pour une comparaison complète de la syntaxe, consultez [Comparaison de JSON et Bicep pour les modèles](compare-template-syntax.md).

Bicep gère automatiquement les dépendances entre ressources. Vous pouvez éviter de définir `dependsOn` quand le nom symbolique d’une ressource est utilisé dans une autre déclaration de ressource.

La structure du fichier Bicep est plus flexible celle du modèle JSON. Vous pouvez déclarer des paramètres, des variables et des sorties n’importe où dans le fichier. Dans JSON, vous devez déclarer l’ensemble des paramètres, variables et sorties dans les sections correspondantes du modèle.

## <a name="faq"></a>Questions fréquentes (FAQ)

**Pourquoi créer un nouveau langage au lieu d’utiliser un langage existant ?**

Vous pouvez considérer Bicep comme une révision du langage de modèle ARM existant plutôt qu’un nouveau langage. La syntaxe a changé, mais la fonctionnalité de base et le runtime restent les mêmes.

Avant de développer Bicep, nous avons envisagé d’utiliser un langage de programmation existant. Nous avons décidé que notre public cible trouverait plus facile d’apprendre Bicep que de commencer avec un autre langage.

**Pourquoi ne pas concentrer votre énergie sur Terraform ou d’autres infrastructures tierces en tant qu’offres de code ?**

Chaque utilisateur a ses préférences en matière de langages et d’outils. Nous voulons nous assurer que tous ces outils offrent une magnifique expérience sur Azure. Bicep participe de cet effort.

Si vous êtes heureux d’utiliser Terraform, il n’y a aucune raison de changer. Microsoft veille à ce que Terraform sur Azure soit le meilleur possible.

Pour les clients qui ont choisi des modèles ARM, nous pensons que Bicep améliore l’expérience de création. Bicep facilite également la transition pour les clients qui n’ont pas adopté d’infrastructure en tant que code.

**Est-il adapté à une utilisation en production ?**

Oui. Depuis la version 0.3, Bicep est inclus dans des plans de support Microsoft. Bicep permet d’effectuer les mêmes tâches que les modèles ARM. Aucune modification avec rupture n’est actuellement planifiée, mais nous devrons peut-être créer des modifications avec rupture à l’avenir.

**Bicep est-il uniquement destiné à Azure ?**

Actuellement, nous ne prévoyons pas d’étendre Bicep au-delà d’Azure. Nous souhaitons prendre entièrement en charge Azure et optimiser l’expérience de déploiement.

La poursuite de cet objectif nécessite d’utiliser des API qui se trouvent en dehors d’Azure. Nous prévoyons de fournir des points d’extensibilité pour ces scénarios.

**Qu’advient-il de mes modèles ARM existants ?**

Ils continueront de fonctionner comme ils l’ont toujours fait. Vous n’avez pas besoin d’apporter de modifications. Nous continuerons à prendre en charge le langage JSON du modèle ARM sous-jacent. Les fichiers Bicep se compilent en un JSON qui est envoyé à Azure pour le déploiement.

Lorsque vous êtes prêt, vous pouvez [décompiler les fichiers JSON en langage Bicep](./decompile.md).

**Puis-je utiliser Bicep pour effectuer le déploiement sur Azure Stack Hub ?**

Oui, vous pouvez utiliser Bicep pour vos déploiements Azure Stack Hub, mais notez que Bicep peut afficher des types qui ne sont pas encore disponibles dans Azure Stack Hub. Vous pouvez afficher un ensemble d’exemples dans le [référentiel GitHub Modèles de démarrage rapide Azure Stack Hub](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/Bicep). 

## <a name="next-steps"></a>Étapes suivantes

Commencez par le [démarrage rapide](./quickstart-create-bicep-use-visual-studio-code.md).
