---
title: 'Démarrage rapide : Intégration dans Microsoft Sentinel'
description: Dans ce guide de démarrage rapide, apprenez à intégrer Microsoft Sentinel en commençant par l’activer, puis en connectant des sources de données.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: quickstart
ms.date: 11/09/2021
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: 46ac4308035491dc2d6b75e34bc0c58a58f93535
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520804"
---
# <a name="quickstart-on-board-microsoft-sentinel"></a>Démarrage rapide : Intégrer Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Dans ce guide de démarrage rapide, apprenez à intégrer Microsoft Sentinel. Pour intégrer Microsoft Sentinel, vous devez d’abord l’activer, puis vous connecter à vos sources de données.

Microsoft Sentinel est fourni avec plusieurs connecteurs pour les solutions Microsoft, disponibles, prêts à l’emploi et offrant une intégration en temps réel, notamment les solutions Microsoft 365 Defender (anciennement Protection Microsoft contre les menaces), les sources Microsoft 365 (dont Office 365), Azure AD, Microsoft Defender pour Identity (anciennement Azure ATP), Microsoft Defender for Cloud Apps, alertes de sécurité depuis Microsoft Defender pour le cloud, entre autres. En outre, il existe des connecteurs intégrés pour la connexion aux écosystèmes de sécurité élargis pour les solutions non Microsoft. Vous pouvez également utiliser le format CEF (Common Event Format), Syslog ou l’API REST pour connecter vos sources de données à Microsoft Sentinel.

Après avoir connecté vos sources de données, opérez votre choix dans une galerie de classeurs créés par des experts, qui mettent en avant des insights sur la base de vos données. Vous pouvez aisément adapter ces classeurs à vos besoins.

>[!IMPORTANT]
> Pour plus d’informations sur les frais liés à l’utilisation de Microsoft Sentinel, consultez les [Tarifs Microsoft Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/) et [Coûts et facturation Microsoft Sentinel](azure-sentinel-billing.md).

## <a name="global-prerequisites"></a>Conditions préalables globales

- Activez l’abonnement Azure, et si vous n’en avez pas, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

- Espace de travail Log Analytics. Découvrez comment [créer un espace de travail Log Analytics](../azure-monitor/logs/quick-create-workspace.md). Pour plus d’informations sur les espaces de travail Log Analytics, consultez [Conception de votre déploiement de journaux Azure Monitor](../azure-monitor/logs/design-logs-deployment.md).

- Pour activer Microsoft Sentinel, vous avez besoin d’autorisations de contributeur sur l’abonnement dans lequel réside l’espace de travail Microsoft Sentinel. 
- Pour utiliser Microsoft Sentinel, vous avez besoin d’autorisations de contributeur ou de lecteur sur le groupe de ressources auquel appartient l’espace de travail.
- Des autorisations supplémentaires peuvent être nécessaires pour la connexion à des sources de données spécifiques.
- Microsoft Sentinel est un service payant. Pour plus d’informations, consultez [À propos de Microsoft Sentinel](https://go.microsoft.com/fwlink/?linkid=2104058).

Pour plus d’informations, consultez [Activités de prédéploiement et prérequis pour déployer Microsoft Sentinel](prerequisites.md).

### <a name="geographical-availability-and-data-residency"></a>Disponibilité géographique et résidence des données

- Microsoft Sentinel peut s’exécuter sur des espaces de travail dans la plupart des [régions couvertes par la mise à disposition générale de Log Analytics](https://azure.microsoft.com/global-infrastructure/services/?products=monitor), à l’exception des régions Chine et Allemagne (souveraines). Parfois, l’intégration du service Microsoft Sentinel par les nouvelles régions Log Analytics peut prendre un certain temps. 

- Les données générées par Microsoft Sentinel, telles que les incidents, les signets et les règles d’analytique, peuvent contenir des données client provenant des espaces de travail Log Analytics du client. Les données générées par Microsoft Sentinel sont enregistrées dans la zone géographique indiquée dans le tableau suivant, en fonction de la zone géographique dans laquelle se trouve l’espace de travail :

    | Zone géographique de l’espace de travail | Zone géographique des données générées par Microsoft Sentinel |
    | --- | --- |
    | États-Unis<br>Inde | États-Unis |
    | Europe<br>France | Europe |
    | Australie | Australie |
    | Royaume-Uni | Royaume-Uni |
    | Canada | Canada |
    | Japon | Japon |
    | Asie-Pacifique | Asie-Pacifique * |
    | Brésil | Brésil * |
    | Norvège | Norvège |
    | Afrique | Afrique |
    | Corée du Sud | Corée du Sud |
    | Allemagne | Allemagne |
    | Émirats arabes unis | Émirats arabes unis |
    | Suisse | Suisse |
    |

    \* La résidence des données dans une seule région est actuellement fournie uniquement dans la région Asie Sud-Est (Singapour) de la zone géographique Asie-Pacifique et dans la région Brésil Sud (État de Sao Paulo) de la zone géographique Brésil. Pour toutes les autres régions, les données client peuvent être stockées n’importe où dans la zone géographique de l’espace de travail où est intégré Sentinel.

    > [!IMPORTANT]
    > - En activant certaines règles qui utilisent le moteur Machine Learning (ML), **vous autorisez Microsoft à copier les données ingérées pertinentes en dehors de la zone géographique de votre espace de travail Microsoft Sentinel** en fonction de ce dont le moteur Machine Learning a besoin pour traiter ces règles.

## <a name="enable-microsoft-sentinel"></a>Activer Microsoft Sentinel <a name="enable"></a>

1. Connectez-vous au portail Azure. Assurez-vous que l’abonnement dans lequel est créé Microsoft Sentinel est bien sélectionné.

1. Recherchez et sélectionnez **Microsoft Sentinel**.

   ![Recherche de services](./media/quickstart-onboard/search-product.png)

1. Sélectionnez **Ajouter**.

1. Sélectionnez l’espace de travail que vous souhaitez utiliser ou créez-en un. Vous pouvez exécuter Microsoft Sentinel sur plusieurs espaces de travail, mais les données sont isolées sur un seul espace de travail.

   ![Choisir un espace de travail](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Les espaces de travail par défaut créés par Microsoft Defender pour le cloud n’apparaissent pas dans la liste. Vous ne pouvez pas installer Microsoft Sentinel sur ces derniers.
   >

   >[!IMPORTANT]
   >
   > - Une fois déployé dans un espace de travail, Microsoft Sentinel **ne prend pas en charge** le déplacement de cet espace de travail vers d’autres groupes de ressources ou abonnements. 
   >
   >   Si vous avez déjà déplacé l’espace de travail, désactivez toutes les règles actives dans **Analytics**, puis réactivez-les après cinq minutes. Cela doit fonctionner la plupart du temps, mais ce n’est, pour rappel, pas pris en charge et est sous votre entière responsabilité.

1. Sélectionnez **Ajouter Microsoft Sentinel**.

## <a name="connect-data-sources"></a>Connecter des sources de données

Microsoft Sentinel ingère les données des services et des applications en se connectant à ceux-ci et en se transférant les événements et les journaux. Pour les machines physiques et virtuelles, vous pouvez installer l’agent Log Analytics qui collecte les journaux et les transfère à Microsoft Sentinel. Pour les pare-feu et les proxys, Microsoft Sentinel installe l’agent Log Analytics sur un serveur Syslog Linux, à partir duquel l’agent collecte les fichiers journaux et les transfère à Microsoft Sentinel. 
 
1. Dans le menu principal, sélectionnez **Connecteurs de données**. La galerie des connecteurs de données s’ouvre.

1. La galerie est une liste de toutes les sources de données que vous pouvez connecter. Sélectionnez une source de données, puis le bouton **Ouvrir la page du connecteur**.

1. La page du connecteur affiche des instructions sur la configuration du connecteur, ainsi que des instructions supplémentaires qui peuvent être nécessaires.

    Par exemple, si vous sélectionnez la source de données **Azure Active Directory**, qui vous permet d’envoyer en streaming des journaux d’Azure AD vers Microsoft Sentinel, vous pouvez sélectionner le type de journal que vous voulez obtenir : journaux de connexion et/ou journaux d’audit. <br> Suivez les instructions d’installation ou [consultez le guide de connexion concerné](data-connectors-reference.md) pour plus d’informations. Pour plus d’informations sur les connecteurs de données, consultez [Connecteurs de données Microsoft Sentinel](connect-data-sources.md).

1. L’onglet **Étapes suivantes** de la page du connecteur affiche les modèles de règle d’analytique, exemples de requêtes et classeurs intégrés appropriés qui accompagnent le connecteur de données. Vous pouvez les utiliser tels quels ou les modifier ; dans les deux cas, vous pouvez obtenir immédiatement des insights intéressants sur vos données.

Une fois que vos données sources sont connectées, elles commencent à être envoyées en streaming dans Microsoft Sentinel et peuvent être utilisées. Vous pouvez afficher les journaux dans les [classeurs intégrés](get-visibility.md) et commencer à créer des requêtes dans Log Analytics pour [examiner les données](investigate-cases.md).

Pour plus d’informations, consultez [Bonnes pratiques de collecte de données](best-practices-data.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez les pages suivantes :

- **Options de déploiement de substitution** :

    - [Déployer Microsoft Sentinel via une API](/rest/api/securityinsights/)
    - [Déployer Microsoft Sentinel via PowerShell](https://www.powershellgallery.com/packages/Az.SecurityInsights/0.1.0)
    - [Déployer Microsoft Sentinel via un modèle ARM](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-all-in-one-accelerator/ba-p/1807933)

- **Démarrage** :
    - [Bien démarrer avec Microsoft Sentinel](get-visibility.md)
    - [Créer des règles d’analytique personnalisées pour détecter des menaces](detect-threats-custom.md)
    - [Connectez votre solution externe en utilisant le format CEF](connect-common-event-format.md)
