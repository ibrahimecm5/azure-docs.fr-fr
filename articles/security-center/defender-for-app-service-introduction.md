---
title: Microsoft Defender pour App Service - avantages et fonctionnalités
description: Découvrez les fonctionnalités de Microsoft Defender pour App Service et comment activer ce service dans votre abonnement
author: memildin
ms.author: memildin
ms.date: 01/25/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.custom: ignite-fall-2021
ms.openlocfilehash: ecd51b53b2aacd11bd5a02af8f7b432f9a08e133
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131075975"
---
# <a name="protect-your-web-apps-and-apis"></a>Protéger vos applications web et vos API

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

## <a name="prerequisites"></a>Prérequis

Defender pour le cloud étant intégré en mode natif avec App Service, aucun déploiement ou intégration ne sont nécessaires. L’intégration est transparente.

Pour protéger votre plan Azure App Service avec Microsoft Defender pour App Service, vous aurez besoin des éléments suivants :

- Un plan App Service pris en charge associé aux ordinateurs dédiés. Les plans pris en charge sont répertoriés dans la section [Disponibilité](#availability).

- Les protections améliorées de Defender pour le cloud activées sur votre abonnement, comme décrit dans [Démarrage rapide : Activer les fonctionnalités de sécurité améliorées](enable-enhanced-security.md).

    > [!TIP]
    > Vous pouvez éventuellement activer des plans individuels Microsoft Defender, tels que Microsoft Defender pour App Service.

## <a name="availability"></a>Disponibilité

| Aspect                       | Détails                                                                                                                                                                                        |
|------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| État de sortie :               | Disponibilité générale                                                                                                                                                                      |
| Prix :                     | Microsoft Defender pour App Service est facturé comme indiqué sur la [page de tarification](https://azure.microsoft.com/pricing/details/security-center/).<br>La facturation se fait en fonction du nombre total d’instances de calcul dans tous les plans       |
| Plans App Service pris en charge : | [Tous les plans App service](https://azure.microsoft.com/pricing/details/app-service/plans/) sont pris en charge, à l’exception d’[Azure Functions sur le plan de consommation](../azure-functions/functions-scale.md). |
| Clouds :                      | :::image type="icon" source="./media/icons/yes-icon.png"::: Clouds commerciaux<br>:::image type="icon" source="./media/icons/no-icon.png"::: Nationaux/Souverains (Azure Government, Azure China 21Vianet)                                                     |
|                              |                                                                                                                                                                                                |

## <a name="what-are-the-benefits-of-microsoft-defender-for-app-service"></a>Quels sont les avantages de Microsoft Defender pour App Service ?

Azure App Service est une plateforme complètement managée qui permet de créer et d’héberger vos applications web et vos API. Étant donné que la plateforme est complètement managée, vous n’avez pas à vous soucier de l’infrastructure. Elle fournit des informations sur la gestion, la surveillance et l’exploitation afin de répondre à des exigences en matière de performances, de sécurité et de conformité de classe entreprise. Pour en savoir plus, consultez [Azure App Service](https://azure.microsoft.com/services/app-service/).

**Microsoft Defender pour App Service** utilise l’échelle du cloud pour identifier les attaques ciblant les applications exécutées sur App Service. Les attaquants sondent les applications web pour trouver et exploiter les faiblesses. Avant d’être routées vers des environnements spécifiques, les demandes adressées aux applications s’exécutant dans Azure passent par plusieurs passerelles, où elles sont inspectées et journalisées. Ces données sont ensuite utilisées pour identifier les codes malveillants exploitant une faille de sécurité ainsi que les attaquants, et pour découvrir de nouveaux modèles qui seront utilisés ultérieurement.

Quand vous activez Microsoft Defender pour App Service, vous bénéficiez immédiatement des services suivants proposés par ce plan Defender :

- **Sécurisation** : Defender pour App Service évalue les ressources couvertes par votre plan App Service et génère des suggestions de sécurité en fonction de ses résultats. Utilisez les instructions détaillées de ces recommandations pour durcir la sécurité de vos ressources App Service.

- **Détection** : Defender pour App Service détecte une multitude de menaces pour vos ressources App Service en supervisant les éléments suivants :
    - L’instance de machine virtuelle dans laquelle App Service s’exécute et son interface de gestion
    - Les requêtes et les réponses échangées avec vos applications App Service
    - Les bacs à sable et les machines virtuelles sous-jacents
    - Journaux internes App Service : disponibles grâce à la visibilité dont bénéficie Azure en tant que fournisseur de cloud

En tant que solution native Cloud, Defender pour App Service peut identifier les méthodologies d’attaque qui s’appliquent à plusieurs cibles. Par exemple, il serait difficile, à partir d’un seul hôte, d’identifier une attaque distribuée en se basant sur un petit sous-ensemble d’adresses IP et en analysant des points de terminaison similaires sur plusieurs hôtes.

Ensemble, les données des journaux et l’infrastructure peuvent expliquer ce qui s’est passé, depuis la circulation d’une nouvelle attaque à la compromission des machines clients. Par conséquent, même si Microsoft Defender pour App Service est déployé après qu’une application web a été attaquée, il est capable de détecter les attaques en cours.


## <a name="what-threats-can-defender-for-app-service-detect"></a>Quelles sont les menaces qui peuvent être détectées par Defender pour App Service ?

### <a name="threats-by-mitre-attck-tactics"></a>Menaces émanant des tactiques MITRE ATT&CK

Defender pour le cloud recherche de nombreuses menaces sur vos ressources App Service. Les alertes couvrent la quasi totalité des tactiques MITRE ATT&CK, de la pré-attaque jusqu’à l’attaque « commande et contrôle ».

- **Menaces de pré-attaque** : Defender pour le cloud peut détecter plusieurs types d’analyseurs de vulnérabilité que les attaquants utilisent fréquemment pour sonder les failles des applications.

- **Menaces d’accès initial** - [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684) génère ces alertes, notamment lorsqu’une adresse IP malveillante connue se connecte à votre interface FTP Azure App Service.

- **Menaces d’exécution** : Defender pour le cloud peut détecter les tentatives d’exécution de commandes à privilèges élevés et de commandes Linux sur App Service Windows, mais également les comportements d’attaque sans fichier, les outils d’exploration de données monétaires numériques et de nombreuses autres activités d’exécution de code suspect et malveillant.

### <a name="dangling-dns-detection"></a>Détection des entrées DNS non résolues

Defender pour App Service identifie également les entrées DNS restantes dans votre bureau d’enregistrement DNS lors de la mise hors service d’un site web App Service. Ces entrées DNS sont appelées « entrées non résolues ». Lorsque vous supprimez un site web sans supprimer son domaine personnalisé de votre bureau d’enregistrement DNS, l’entrée DNS pointe vers une ressource inexistante, ce qui rend votre sous-domaine vulnérable aux prises de contrôle. Defender pour le cloud ne recherche pas les entrées DNS non résolues *existantes* dans votre bureau d’enregistrement DNS. Il vous avertit quand un site web App Service a été désactivé, mais que son domaine personnalisé (entrée DNS) n’a pas été supprimé.

La prise de contrôle des sous-domaines constitue une menace grave et courante pour les organisations. Lorsqu’un acteur de menace détecte une entrée DNS non résolue, il crée son propre site à l’adresse de destination. Le trafic destiné au domaine de l’organisation est ensuite dirigé vers le site de l’acteur de menace, qui peut utiliser ce trafic pour un large éventail d’activités malveillantes.

La protection des entrées DNS non résolues est disponible pour les domaines gérés par Azure DNS et pour ceux gérés par un bureau d’enregistrement de domaines externes. En outre, elle s’applique aussi bien à App Service sur Windows qu’à App Service sur Linux.

:::image type="content" source="media/defender-for-app-service-introduction/dangling-dns-alert.png" alt-text="Exemple d’alerte concernant une entrée DNS non résolue détectée. Activez Microsoft Defender pour App Service pour recevoir ce message et d’autres alertes pour votre environnement." lightbox="media/defender-for-app-service-introduction/dangling-dns-alert.png":::

Pour plus d’informations sur la prise de sous-domaine et les entrées DNS non résolues, consultez [Empêcher les entrées DNS non résolues et la prise de contrôle des sous-domaines](../security/fundamentals/subdomain-takeover.md).

Pour obtenir la liste complète des alertes App Service, consultez la [table de référence des alertes](alerts-reference.md#alerts-azureappserv).

> [!NOTE]
> Defender peut ne pas déclencher d’alertes concernant des entrées DNS non résolues si votre domaine personnalisé ne pointe pas directement vers une ressource App Service, ou si Defender n’a pas supervisé le trafic vers votre site web depuis l’activation de la protection des entrées DNS non résolues (car il n’y a pas de journaux pour faciliter l’identification du domaine personnalisé).

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert Microsoft Defender pour App Service. 

> [!div class="nextstepaction"]
> [Activer les protections renforcées](enable-enhanced-security.md)

Pour des informations connexes, consultez les articles suivants : 

- Pour exporter vos alertes vers Microsoft Sentinel, un système SIEM tiers ou tout autre outil externe, suivez les instructions fournies dans [Diffuser des alertes vers un système SIEM, SOAR ou une solution de gestion des services informatiques](export-to-siem.md).
- Pour obtenir la liste des alertes Microsoft Defender pour App Service, consultez la [table de référence des alertes](alerts-reference.md#alerts-azureappserv).
- Pour plus d’informations sur les plans App Service, consultez [Plans App Service](https://azure.microsoft.com/pricing/details/app-service/plans/).
