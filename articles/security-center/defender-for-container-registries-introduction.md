---
title: 'Microsoft Defender pour les registres de conteneurs : avantages et fonctionnalités'
description: Découvrez les avantages et les fonctionnalités de Microsoft Defender pour les registres de conteneurs.
author: memildin
ms.author: memildin
ms.date: 10/04/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 627ac6bacbd9d6f1c3649229bd29bbcd27d3e93b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131463826"
---
# <a name="introduction-to-microsoft-defender-for-container-registries"></a>Présentation de Microsoft Defender pour les registres de conteneurs

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Azure Container Registry (ACR) est un service de registre Docker privé et géré qui stocke et gère vos images conteneurs pour les déploiements Azure dans un registre central. Il est basé sur le registre Docker open source 2.0.

Pour protéger les registres basés sur Azure Resource Manager dans votre abonnement, activez **Microsoft Defender pour les registres de conteneurs** au niveau de l’abonnement. Defender pour le cloud analyse ensuite toutes les images poussées vers le registre, importées dans le registre, ou tirées au cours des 30 derniers jours. Vous serez facturé pour chaque image analysée, une fois par image.

## <a name="availability"></a>Disponibilité

|Aspect|Détails|
|----|:----|
|État de sortie :|Disponibilité générale (GA)|
|Prix :|**Microsoft Defender pour les registres de conteneurs** est facturé comme indiqué dans la [page des tarifs](https://azure.microsoft.com/pricing/details/security-center/)|
|Registres et images pris en charge :|Images Linux dans des registres ACR accessibles depuis l’Internet public grâce à l’accès shell<br>[Registres ACR protégés par Azure Private Link](../container-registry/container-registry-private-link.md)|
|Registres et images pris en charge :|Images Windows<br>Registres « privés » (sauf si l’accès est accordé aux [Services approuvés](../container-registry/allow-access-trusted-services.md#trusted-services))<br>Images super-minimalistes, telles que les images de [base Docker](https://hub.docker.com/_/scratch/) ou « Distroless » qui contiennent uniquement une application et ses dépendances de runtime sans gestionnaire de packages, interpréteur de commandes ni système d’exploitation<br>Images avec [Spécification du format d’image Open Container Initiative (OCI)](https://github.com/opencontainers/image-spec/blob/master/spec.md)|
|Rôles et autorisations obligatoires :|**Lecteur de sécurité** et [autorisations et rôles Azure Container Registry](../container-registry/container-registry-roles.md)|
|Clouds :|:::image type="icon" source="./media/icons/yes-icon.png" border="false"::: Clouds commerciaux<br>:::image type="icon" source="./media/icons/yes-icon.png" border="false"::: Nationaux/Souverains (Azure Government, Azure China 21Vianet)|
|||

## <a name="what-are-the-benefits-of-microsoft-defender-for-container-registries"></a>Quels sont les avantages de Microsoft Defender pour les registres de conteneurs ?

Defender pour le cloud identifie les registres ACR basés sur Azure Resource Manager dans votre abonnement, et fournit de manière fluide l’évaluation et la gestion des vulnérabilités natives d’Azure pour les images de votre registre.

**Microsoft Defender pour les registres de conteneurs** comprend un scanneur de vulnérabilité pour analyser les images dans vos registres Azure Container Registry basés sur Azure Resource Manager et fournir une visibilité plus poussée des vulnérabilités de vos images. L'analyseur intégré est proposé par Qualys, un leader dans le secteur des solutions d’identification des vulnérabilités.

Quand des problèmes sont détectés (par Qualys ou Defender pour le cloud), vous êtes averti dans le tableau de bord de la protection de charge de travail. Pour chaque vulnérabilité, Defender pour le cloud fournit des recommandations actionnables, accompagnées d’une classification de la gravité et de conseils sur la façon de corriger le problème. Pour plus d’informations sur les recommandations de Defender pour le cloud pour les conteneurs, consultez la [liste de référence des recommandations](recommendations-reference.md#recs-container).

Defender pour le cloud filtre et classe les résultats du scanneur. Quand une image est saine, Defender pour le cloud la marque comme telle. Defender pour le cloud génère des recommandations de sécurité uniquement pour les images qui ont des problèmes à résoudre. Defender pour le cloud fournit des détails sur chaque vulnérabilité signalée et une classification de la gravité. En outre, il fournit des conseils sur la façon de corriger les vulnérabilités spécifiques détectées sur chaque image.

En vous avertissant seulement en cas de problème, Defender pour le cloud réduit la possibilité d’alertes informatives indésirables.


> [!TIP]
> Pour en savoir plus sur les fonctionnalités de sécurité de conteneur de Defender pour le cloud, consultez :
>
> - [Microsoft Defender pour le cloud et sécurité des conteneurs](container-security.md)
> - [Présentation de Microsoft Defender pour Kubernetes](defender-for-kubernetes-introduction.md)

## <a name="when-are-images-scanned"></a>Quand les images sont-elles analysées ?

Il existe trois déclencheurs pour une analyse d’image :

- **En cas de poussage** : chaque fois qu’une image est poussée vers votre registre, Defender pour les registres de conteneurs analyse automatiquement cette image. Pour déclencher l’analyse d’une image, poussez-la vers votre référentiel.

- **Tirée récemment** : comme des nouvelles vulnérabilités sont découvertes chaque jour, **Microsoft Defender pour les registres de conteneurs** analyse également toutes les semaines les images tirées au cours des 30 derniers jours. Aucuns frais supplémentaires ne sont facturés pour ces nouvelles analyses. Comme indiqué ci-dessus, vous êtes facturé une fois par image.

- **Lors de l’importation** : Azure Container Registry inclut des outils d’importation pour importer des images dans votre registre à partir de Dockr Hub, d’un registre de conteneurs Microsoft ou d’un autre registre de conteneurs Azure. **Microsoft Defender pour les registres de conteneurs** analyse toutes les images prises en charge que vous importez. Pour plus d’informations, consultez [Importation d’images conteneur dans un registre de conteneurs](../container-registry/container-registry-import-images.md).
 
L’analyse se termine généralement dans un délai de 2 minutes, mais elle peut prendre jusqu’à 15 minutes. Les résultats sont présentés sous forme de recommandations de ce type :

[![Exemple de recommandation Microsoft Defender pour le cloud sur les vulnérabilités découvertes dans une image hébergée par Azure Container Registry (ACR).](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)


## <a name="how-does-defender-for-cloud-work-with-azure-container-registry"></a>Fonctionnement de Defender pour le cloud avec Azure Container Registry

Le diagramme de haut niveau ci-dessous représente les composants et les avantages de la protection de vos registres avec Defender pour le cloud.

![Vue d’ensemble de haut niveau de Microsoft Defender pour le cloud et Azure Container Registry (ACR).](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="faq---azure-container-registry-image-scanning"></a>FAQ - Analyse d’images Azure Container Registry

### <a name="how-does-defender-for-cloud-scan-an-image"></a>Comment Defender pour le cloud analyse-t-il une image ?
Defender pour le cloud tire l’image du registre et l’exécute dans un bac à sable isolé avec le scanneur Qualys. L’analyseur extrait une liste des vulnérabilités connues.

Defender pour le cloud filtre et classe les résultats du scanneur. Quand une image est saine, Defender pour le cloud la marque comme telle. Defender pour le cloud génère des recommandations de sécurité uniquement pour les images qui ont des problèmes à résoudre. En vous avertissant seulement en cas de problème, Defender pour le cloud réduit la possibilité d’alertes informatives indésirables.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Puis-je obtenir les résultats de l’analyse via l’API REST ?
Oui. Les résultats se trouvent sous l’[API REST Sub-Assessments](/rest/api/securitycenter/subassessments/list/). De plus, vous pouvez utiliser Azure Resource Graph (ARG), l’API de type Kusto pour toutes vos ressources : une requête peut extraire une analyse spécifique.

### <a name="what-registry-types-are-scanned-what-types-are-billed"></a>Quels sont les types de registres analysés ? Quels types sont facturés ?
Pour voir la liste des types de registres de conteneurs pris en charge par Microsoft Defender pour les registres de conteneurs, consultez [Disponibilité](#availability).

Si vous connectez des registres non pris en charge à votre abonnement Azure, Defender pour le cloud ne les analyse pas et ne vous les facture pas.

### <a name="can-i-customize-the-findings-from-the-vulnerability-scanner"></a>Puis-je personnaliser les résultats de l’analyseur de vulnérabilités ?
Oui. Si votre organisation préfère ignorer un résultat, plutôt que de le corriger, vous pouvez éventuellement désactiver cette fonction. Les résultats désactivés n’ont pas d’impact sur votre Niveau de sécurité ni ne génèrent de bruit indésirable.

[Découvrir la création de règles pour désactiver les résultats à partir de l’outil d’évaluation des vulnérabilités intégré](defender-for-container-registries-usage.md#disable-specific-findings-preview).

### <a name="why-is-defender-for-cloud-alerting-me-to-vulnerabilities-about-an-image-that-isnt-in-my-registry"></a>Pourquoi Defender pour le cloud m’avertit de vulnérabilités sur une image qui ne se trouve pas dans mon registre ?
Defender pour le cloud fournit des évaluations de vulnérabilité pour chaque image poussée ou tirée dans un registre. Certaines images peuvent réutiliser des étiquettes à partir d’une image déjà analysée. Par exemple, vous pouvez réassigner l’étiquette « latest » chaque fois que vous ajoutez une image à une synthèse. Dans ce cas, l’« ancienne » image existe toujours dans le registre et peut encore être extraite par sa synthèse. Si l’image présente des résultats de sécurité et qu’elle est extraite, elle expose des vulnérabilités de sécurité.


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Analyser vos images à la recherche de vulnérabilités](defender-for-container-registries-usage.md)
