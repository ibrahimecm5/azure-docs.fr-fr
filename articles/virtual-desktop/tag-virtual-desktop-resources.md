---
title: Étiqueter les ressources Azure Virtual Desktop - Azure
description: Le balisage, et comment vous pouvez l’utiliser pour gérer les coûts des services Azure dans Azure Virtual Desktop.
author: heidilohr
ms.topic: conceptual
ms.date: 11/12/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 1c77e7b2ec767cd843cbf34b7f3a44d074d20ab5
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132404343"
---
# <a name="tag-azure-virtual-desktop-resources-to-manage-costs"></a>Baliser les ressources Azure Virtual Desktop pour gérer les coûts

Le balisage est un outil disponible dans les services Azure qui vous permet d’organiser les ressources au sein de leur abonnement Azure. L’organisation des ressources facilite le suivi des coûts sur plusieurs services. Les balises vous aident également à comprendre le coût de chaque regroupement de ressources Azure par période de facturation. Si vous souhaitez en savoir plus sur le balisage en général, consultez [Utiliser des balises pour organiser vos ressources Azure et votre hiérarchie de gestion](../azure-resource-manager/management/tag-resources.md). Vous pouvez également regarder une [vidéo rapide](https://www.youtube.com/watch?v=dUft4FZ40O8) sur d’autres façons d’utiliser les balises Azure.

## <a name="how-tagging-works"></a>Fonctionnement du balisage

Vous pouvez baliser les services Azure que vous gérez dans le Portail Azure ou via PowerShell. Les balises s’affichent sous forme de paires clé-valeur de texte. Lorsque vous utilisez des ressources Azure avec balises, la paire clé-valeur associée est attachée à l’utilisation des ressources.

Une fois que votre déploiement signale des informations d’utilisation avec balises à [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md), vous pouvez utiliser votre structure de balises pour filtrer les données de coût. Pour savoir comment filtrer par balises dans Azure Cost Management, voir [Démarrage rapide : explorer et analyser les coûts à l’aide de l’analyse des coûts](../cost-management-billing/costs/quick-acm-cost-analysis.md).

### <a name="add-edit-or-delete-tags"></a>Ajouter, modifier ou supprimer des balises

Lorsque vous appliquez une nouvelle balise à une ressource, celle-ci n’est pas visible dans Azure Cost Management jusqu’à ce que l’activité des rapports de ressources Azure qui lui est associée soit affichée. Si vous appliquez une balise existante à vos ressources, cette modification n’est pas non plus visible dans Azure Cost Management tant que les ressources Azure ne sont pas liées à l’activité.

Si vous modifiez un nom de balise, les ressources associées associent désormais les coûts à sa nouvelle paire clé-valeur. Vous pouvez toujours filtrer les données avec l'ancienne balise, mais toutes les nouvelles données provenant d'après le changement seront signalées avec la nouvelle balise.

Si vous supprimez une balise, Azure Virtual Desktop ne signale plus les données associées à la balise supprimée à Azure Cost Management. Vous pouvez toujours filtrer avec les balises supprimées pour les données signalées avant que vous ne supprimiez la balise.

>[!IMPORTANT]
>Les ressources Azure balisées qui n’ont pas été actives depuis que vous leur avez appliqué des balises nouvelles ou mises à jour ne signalent aucune activité associée aux balises modifiées pour Azure Cost Management. Vous ne pouvez pas filtrer les balises spécifiques tant que l’activité associée n’est pas signalée à Azure Cost Management par le service.

### <a name="view-all-existing-tags"></a>Afficher toutes les balises existantes

Vous pouvez afficher toutes les balises existantes pour vos services Azure en accédant au Portail Azure, puis en ouvrant [l’onglet **Balises**.](https://ms.portal.azure.com/#blade/HubsExtension/TagsBlade) L’onglet Balises affiche toutes les balises dans les objets auxquels vous avez accès. Vous pouvez également trier les balises en fonction de leurs clés ou valeurs chaque fois que vous devez mettre à jour rapidement un grand nombre de balises en même temps.

### <a name="what-tags-can-and-cant-do"></a>Ce que les balises peuvent et ne peuvent pas faire

Les balises signalent uniquement les données d’utilisation et de coût des ressources Azure auxquelles elles sont directement affectées. Si vous avez étiqueté une ressource sans étiqueter les autres ressources qu’elle contient, Azure Virtual Desktop ne signale que les activités liées à la ressource étiquetée de niveau supérieur. Vous devez également étiqueter chaque ressource sous cette ressource de niveau supérieur si vous souhaitez que vos données de facturation soient exactes.

Pour en savoir plus sur le fonctionnement des balises dans Azure Cost Management, consultez la rubrique [Utilisation des balises dans les données de coût et d’utilisation](../cost-management-billing/costs/understand-cost-mgt-data.md#how-tags-are-used-in-cost-and-usage-data).

Pour obtenir la liste des limitations connues des balises Azure, consultez [Utiliser des balises pour organiser vos ressources Azure et votre hiérarchie de gestion](../azure-resource-manager/management/tag-resources.md#limitations).

## <a name="using-tags-in-azure-virtual-desktop"></a>Utilisation de balises dans Azure Virtual Desktop

Maintenant que vous comprenez les principes de base des balises Azure, voyons comment vous pouvez les utiliser dans Azure Virtual Desktop.

Vous pouvez utiliser des balises Azure pour organiser les coûts liés à la création, la gestion et le déploiement d’expériences virtualisées pour vos clients et utilisateurs. Le balisage peut également vous aider à suivre les ressources que vous achetez directement via Azure Virtual Desktop et d’autres services Azure connectés aux déploiements Azure Virtual Desktop.

## <a name="suggested-tags-for-azure-virtual-desktop"></a>Balises suggérées pour Azure Virtual Desktop

Étant donné qu’Azure Virtual Desktop peut fonctionner avec d’autres services Azure pour prendre en charge ses déploiements, il n’existe pas de système universel pour le balisage des ressources de déploiement. Ce qui est le plus important, c’est que vous développiez une stratégie qui fonctionne pour vous et votre organisation. Toutefois, nous avons des suggestions qui peuvent vous aider, en particulier si vous ne connaissez pas encore Azure. 

Les suggestions suivantes s’appliquent à tous les déploiements Azure Virtual Desktop :

- Familiarisez-vous avec vos services Azure achetés pour comprendre l’étendue de ce que vous souhaitez étiqueter. À mesure que vous apprenez à utiliser le Portail Azure, conservez une liste des groupes de services et des objets dans lesquels vous pouvez appliquer des balises. Certaines ressources dont vous devez effectuer le suivi incluent les groupes de ressources, les machines virtuelles, les disques et les cartes d’interface réseau (NIC). Pour obtenir une liste plus complète des composants de service de génération de coûts que vous pouvez étiqueter, consultez [Comprendre le coût total de déploiement d’Azure Virtual Desktop](./remote-app-streaming/total-costs.md).

- Créez une agrégation de rapports de coûts pour organiser vos balises. Vous pouvez soit [suivre un modèle de balisage commun](/azure/cloud-adoption-framework/ready/azure-best-practices/resource-tagging.md), soit créer un nouveau modèle qui répond aux besoins de votre organisation.

- Assurez la cohérence de vos balises partout où vous les appliquez. Même la plus petite faute de frappe peut avoir un impact sur les rapports de données. Assurez-vous que vous ajoutez la paire clé-valeur exacte que vous souhaitez consulter ultérieurement.

- Conservez un enregistrement des balises que vous mettez à jour ou modifiez. Cet enregistrement vous permet de combiner les données historiques de chaque balise en fonction des besoins. Lorsque vous modifiez ou mettez à jour une balise, vous devez également appliquer ces modifications à toutes les ressources qui incluent la balise modifiée.

## <a name="suggested-tags-for-azure-virtual-desktop-host-pools"></a>Balises suggérées pour les pools d’hôtes Azure Virtual Desktop

Chaque machine virtuelle d’un pool d’hôtes Azure Virtual Desktop crée une construction de production de coûts. Étant donné que les pools d’hôtes constituent la base d’un déploiement Azure Virtual Desktop, leurs machines virtuelles sont généralement la principale source de coûts pour les déploiements Azure Virtual Desktop. Si vous souhaitez configurer un système de balisage, nous vous recommandons de commencer par étiqueter tous les pools d’ordinateurs hôtes de votre déploiement pour effectuer le suivi des coûts de calcul des machines virtuelles. Le balisage de vos pools d’hôtes peut vous aider à utiliser le filtrage dans Azure Cost Management pour identifier ces coûts de machine virtuelle.

Comme avec les [suggestions générales](#suggested-tags-for-azure-virtual-desktop), il n’existe pas de système universel pour le balisage des pools d’hôtes. Toutefois, nous avons quelques suggestions pour vous aider à organiser vos balises de pool d’hôtes :

- Le balisage des pools d’hôtes lors de leur création est facultatif, mais le balisage au cours du processus de création vous permet d’afficher plus facilement toutes les utilisations avec balises dans Azure Cost Management plus tard. Les balises de votre pool d’hôtes suivent tous les composants générant des coûts des hôtes de la session au sein de votre pool d’hôtes. Apprenez-en davantage sur les coûts spécifiques aux hôtes de la session dans la rubrique [Comprendre le coût total de déploiement Azure Virtual Desktop](./remote-app-streaming/total-costs.md).

- Si vous utilisez le Portail Azure pour créer un pool d’ordinateurs hôtes, le flux de travail de création vous donne la possibilité d’ajouter des balises existantes. Ces balises sont transmises à toutes les ressources que vous créez pendant le processus de création du pool d’hôtes. Les balises sont également appliquées aux hôtes de la session que vous ajoutez à un pool d’hôtes existant dans le Portail Azure. Toutefois, vous devez entrer les balises manuellement chaque fois que vous ajoutez un nouvel hôte de la session.

- Il est peu probable que vous obteniez un rapport de coût complet de tous les services Azure qui prennent en charge l’utilisation de vos pools d’hôtes, car les options de configuration sont à la fois limitées et uniques à chaque client. C’est à vous de décider à quel niveau vous souhaitez effectuer le suivi des coûts dans tous les services Azure associés à votre déploiement Azure Virtual Desktop. Plus vous effectuez de suivi de ces coûts en les marquant, plus votre rapport de coût mensuel Azure Virtual Desktop sera précis.

- Si vous créez votre système de balisage autour de vos pools d’hôtes, veillez à utiliser des paires clé-valeur qui sont logiques à ajouter ultérieurement à d’autres services Azure.

## <a name="suggested-tags-for-other-azure-virtual-desktop-resources"></a>Balises suggérées pour d’autres ressources Azure Virtual Desktop

La plupart des clients Azure Virtual Desktop déploient d’autres services Azure pour prendre en charge leurs déploiements. Si vous souhaitez inclure le coût de ces services supplémentaires dans votre rapport sur les coûts, vous devez prendre en compte les suggestions suivantes :

- Si vous avez déjà acheté un service ou des ressources Azure que vous souhaitez intégrer dans vos déploiements Azure Virtual Desktop, vous disposez de deux options :
   
   - Séparez les services Azure achetés entre différents abonnements Azure.
   - Combinez tous les services Azure achetés dans le même abonnement avec vos balises Azure Virtual Desktop.

   En séparant vos services, vous aurez une idée plus précise des coûts de chaque service, mais vous risquez de payer plus cher au final. Vous devrez peut-être acheter du stockage supplémentaire pour ces services pour vous assurer que votre Azure Virtual Desktop dispose de son propre stockage désigné. 

   Combiner vos services achetés est moins coûteux, mais peut gonfler votre rapport de coûts car les données d'utilisation des ressources partagées ne seront pas aussi précises. Pour s’assurer de l’absence de précision, vous pouvez ajouter plusieurs balises à vos ressources pour voir les coûts partagés grâce à des filtres qui effectuent le suivi de différents facteurs.

- Si vous avez commencé à créer votre système de balisage avec un service Azure différent, assurez-vous que les paires clé-valeur que vous créez peuvent être appliquées ultérieurement à votre déploiement Azure Virtual Desktop ou à d’autres services.

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez en savoir plus sur les coûts courants liés à Azure Virtual Desktop, consultez [Comprendre le coût total du déploiement d’Azure Virtual Desktop](./remote-app-streaming/total-costs.md).

Si vous souhaitez en savoir plus sur les balises Azure, consultez les ressources suivantes :

- [Utiliser des étiquettes pour organiser vos ressources Azure et votre hiérarchie de gestion](../azure-resource-manager/management/tag-resources.md)

- [Vidéo expliquant la valeur de l’utilisation des balises Azure](https://www.youtube.com/watch?v=dUft4FZ40O8)

- [Utilisation des étiquettes dans les données de coût et d’utilisation](../cost-management-billing/costs/understand-cost-mgt-data.md#how-tags-are-used-in-cost-and-usage-data)

- [Développer une stratégie de nommage et de catégorisation des ressources Azure](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)

- [Définir votre stratégie d’étiquetage](/azure/cloud-adoption-framework/ready/azure-best-practices/resource-tagging)

- [Guides de décision concernant le nommage et l’étiquetage des ressources](/azure/cloud-adoption-framework/decision-guides/resource-tagging)

Si vous souhaitez en savoir plus sur Azure Cost Management, consultez les articles suivants :

- [Présentation d’Azure Cost Management + Facturation](../cost-management-billing/cost-management-billing-overview.md)

- [Démarrage rapide : Explorer et analyser les coûts avec une analyse des coûts](../cost-management-billing/costs/quick-acm-cost-analysis.md)
