---
title: Guide pratique d’utilisation de Defender pour le cloud pour les registres de conteneurs
description: Découvrez comment analyser les images Linux de vos registres hébergés par Linux avec Microsoft Defender pour les registres de conteneurs
author: memildin
ms.author: memildin
ms.date: 10/21/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9925fcb9f6a4d4284c7c6784a45e896ea825abc0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131056088"
---
# <a name="use-microsoft-defender-for-container-registries-to-scan-your-images-for-vulnerabilities"></a>Analyse des vulnérabilités dans les images avec Microsoft Defender pour les registres de conteneurs

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Cette page explique comment utiliser le scanner de vulnérabilité intégré pour analyser les images conteneur stockées dans un conteneur Azure Container Registry basé sur Azure Resource Manager.

Lorsque **Microsoft Defender pour les registres de conteneurs** est activé, chaque image envoyée au registre est immédiatement analysée, de même que chaque image extraite au cours des 30 derniers jours. 

Lorsque l’analyseur signale des vulnérabilités à Defender pour le cloud, ce dernier présente les résultats et les informations connexes sous forme de recommandations. En outre, les résultats incluent des informations connexes, telles que les étapes de correction, les CVE pertinents, les scores CVSS et bien plus encore. Les vulnérabilités identifiées peuvent être affichées pour un ou plusieurs abonnements ou pour un registre en particulier.

> [!TIP]
> Vous pouvez également analyser des images conteneur pour détecter des vulnérabilités au fur et à mesure que les images sont générées dans votre workflow GitHub CI/CD. En savoir plus en consultant [Identifier les images conteneur vulnérables dans vos workflows CI/CD](defender-for-container-registries-cicd.md).


## <a name="identify-vulnerabilities-in-images-in-azure-container-registries"></a>Identification des vulnérabilités dans les images des registres de conteneurs Azure Container Registry 

Pour activer l’analyse de vulnérabilité des images stockées dans un registre de conteneurs Azure Container Registry basé sur Azure Resource Manager :

1. Activez **Microsoft Defender pour les registres de conteneurs** dans votre abonnement. Defender pour le cloud est maintenant prêt à analyser les images dans les registres.

    >[!NOTE]
    > Cette fonctionnalité est facturée par image.

1. Les analyses d’images sont déclenchées à chaque envoi ou importation, et si l’image a été extraite au cours des 30 derniers jours. 

    Une fois l’analyse terminée (généralement après environ deux minutes, mais parfois au bout de 15 minutes), les résultats sont disponibles sous forme de recommandations Defender pour le cloud.

1. [Affichez et corrigez les résultats (cf. explications ci-dessous)](#view-and-remediate-findings).

## <a name="identify-vulnerabilities-in-images-in-other-container-registries"></a>Identification des vulnérabilités dans les images d’autres registres de conteneurs 

1. Utilisez les outils ACR pour importer des images dans votre registre à partir de Docker Hub ou de Microsoft Container Registry.  Une fois l’importation terminée, les images importées sont analysées par la solution d’évaluation des vulnérabilités intégrée.

    Pour plus d’informations, consultez [Importation d’images conteneur dans un registre de conteneurs](../container-registry/container-registry-import-images.md).

    Une fois l’analyse terminée (généralement après environ deux minutes, mais parfois au bout de 15 minutes), les résultats sont disponibles sous forme de recommandations Defender pour le cloud.

1. [Affichez et corrigez les résultats (cf. explications ci-dessous)](#view-and-remediate-findings).


## <a name="view-and-remediate-findings"></a>Affichage et correction des résultats

1. Pour voir les résultats, accédez à la page **Recommandations**. Si des problèmes sont trouvés, la recommandation **Les vulnérabilités des images Azure Container Registry doivent être corrigées** apparaît.

    ![Recommandation de correction des problèmes](media/monitor-container-security/acr-finding.png)

1. Sélectionnez la recommandation. 

    La page de détails de la recommandation s’ouvre et présente des informations supplémentaires. Vous y trouverez notamment la liste des registres contenant des images vulnérables (« Ressources affectées ») et les étapes de correction. 

1. Sélectionnez un registre pour voir les référentiels vulnérables qu’il contient.

    ![Sélectionner un registre](media/monitor-container-security/acr-finding-select-registry.png)

    La page de détails du registre s’ouvre et présente la liste des référentiels affectés.

1. Sélectionnez un référentiel pour afficher les référentiels qui contiennent des images vulnérables.

    ![Sélectionnez un dépôt.](media/monitor-container-security/acr-finding-select-repository.png)

    La page de détails du référentiel s’ouvre. Elle liste les images vulnérables, accompagnées d’une évaluation de la gravité des vulnérabilités détectées.

1. Sélectionnez une image pour voir les vulnérabilités.

    ![Sélectionner une image](media/monitor-container-security/acr-finding-select-image.png)

    La liste des résultats pour l’image sélectionnée s’affiche.

    ![Liste des résultats](media/monitor-container-security/acr-findings.png)

1. Pour en savoir plus sur un résultat, sélectionnez-le. 

    Le volet des résultats détaillés s’ouvre.

    [![Volet des résultats détaillés](media/monitor-container-security/acr-finding-details-pane.png)](media/monitor-container-security/acr-finding-details-pane.png#lightbox)

    Ce volet inclut une description détaillée du problème ainsi que des liens vers des ressources externes pour vous aider à atténuer les menaces.

1. Effectuez les étapes de la section Correction de ce volet.

1. Une fois que vous avez suivi les étapes requises pour corriger le problème de sécurité, remplacez l’image dans votre registre :

    1. Envoyez l’image mise à jour. Cette opération push déclenche une analyse. 
    
    1. Accédez à la page des recommandations pour voir si la recommandation « Les vulnérabilités dans les images Azure Container Registry doivent être corrigées » y figure. 
    
        Si vous voyez encore cette recommandation et que l’image corrigée figure toujours dans la liste des images vulnérables, recommencez les étapes de correction.

    1. Quand vous êtes sûr que l’image mise à jour a été envoyée et analysée, et qu’elle n’apparaît plus dans la recommandation, supprimez l’« ancienne » image vulnérable de votre registre.


## <a name="disable-specific-findings-preview"></a>Désactivation de certains résultats (préversion)

> [!NOTE]
> [!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]

Si votre organisation préfère ignorer un résultat, plutôt que de le corriger, vous avez la possibilité de désactiver cette fonction. Les résultats désactivés n’ont pas d’impact sur votre Niveau de sécurité ni ne génèrent de bruit indésirable.

Les résultats correspondant aux critères définis dans vos règles de désactivation n’apparaissent pas dans la liste des résultats. Voici quelques exemples de scénarios courants :

- Désactiver les résultats de gravité inférieure à moyenne
- Désactiver les résultats qui ne peuvent pas être corrigés
- Désactiver les résultats dont le score CVSS inférieur à 6.5
- Désactiver les résultats dont la catégorie ou la vérification de sécurité comporte certains mots (par exemple, « RedHat », « Correctif de sécurité CentOS pour sudo »)

> [!IMPORTANT]
> Pour créer une règle, vous devez disposer de l’autorisation de modifier une stratégie dans Azure Policy.
>
> Pour plus d’informations, consultez [Autorisations Azure RBAC dans Azure Policy](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy).

Les critères suivants peuvent être utilisés : 

- ID de résultat 
- Category
- Vérification de sécurité 
- Scores CVSS v3
- Gravité 
- Possibilité de correction 

Pour créer une règle :

1. Sur à la page de détail de la recommandation **Les vulnérabilités des images Azure Container Registry doivent être corrigées**, sélectionnez **Désactivez la règle**.
1. Sélectionnez l’étendue nécessaire.
1. Définissez vos critères.
1. Sélectionnez **Appliquer la règle**.

    :::image type="content" source="./media/defender-for-container-registries-usage/new-disable-rule-for-registry-finding.png" alt-text="Création d’une règle de désactivation des résultats de VA sur le registre":::

1. Pour afficher, remplacer ou supprimer une règle : 
    1. Sélectionnez **Désactiver une règle**.
    1. Dans la liste des étendues, les abonnements présentant des règles actives apparaissent avec la mention **Règle appliquée**.
        :::image type="content" source="./media/remediate-vulnerability-findings-vm/modify-rule.png" alt-text="Modifier ou supprimer une règle existante.":::
    1. Pour afficher ou supprimer la règle, sélectionnez le menu de points de suspension (« … »).


## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur [les plans de protection avancée de Microsoft Defender](defender-for-cloud-introduction.md).
