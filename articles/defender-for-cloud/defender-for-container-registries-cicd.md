---
title: Analyseur de vulnérabilité de Defender pour le cloud pour les images conteneurs dans des flux de travail CI/CD
description: Découvrez comment analyser les images conteneurs dans des flux de travail CI/CD avec Microsoft Defender pour les registres de conteneurs
author: memildin
ms.author: memildin
ms.date: 11/09/2021
ms.topic: how-to
ms.service: defender-for-cloud
manager: rkarlin
ms.openlocfilehash: b7b30e6702b4c2c5f899f6ea1fb584dc9cbd0927
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132526398"
---
# <a name="identify-vulnerable-container-images-in-your-cicd-workflows"></a>Identifier les images conteneur vulnérables dans vos workflows CI/CD

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Cette page explique comment analyser vos images conteneur basées sur Azure Container Registry à l’aide de l’analyseur de vulnérabilité intégré lorsqu’elles sont créées dans le cadre de vos workflows GitHub.

Pour configurer l’analyseur, vous devez activer **Microsoft Defender pour les registres de conteneurs** et l’intégration de CI/CD. Lorsque vos workflows CI/CD envoient des images à vos registres, vous pouvez afficher les résultats d’analyse des registres et une synthèse des résultats d’analyse CI/CD.

Les conclusions des analyses CI/CD sont un enrichissement pour les résultats d’analyse de registres existants effectués par Qualys. L’analyse CI/CD de Defender pour le cloud est optimisée par [Aqua Trivy](https://github.com/aquasecurity/trivy).

Vous obtiendrez des informations de traçabilité, telles que le workflow GitHub et l’URL d’exécution GitHub, pour permettre l’identification des workflows qui aboutissent à des images vulnérables.

> [!TIP]
> Les vulnérabilités identifiées dans une analyse de votre registre peuvent être différentes des résultats de vos analyses CI/CD. Une des raisons de ces différences vient du fait que l’analyse de registre est [continue](defender-for-container-registries-introduction.md#when-are-images-scanned), alors que l’analyse CI/CD se produit juste avant que le workflow n’envoie l’image dans le registre.

## <a name="availability"></a>Disponibilité

|Aspect|Détails|
|----|:----|
|État de sortie :| **Cette intégration CI/CD est en préversion.**<br>Nous vous recommandons de l’expérimenter uniquement sur les workflows hors production.<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]|
|Prix :|**Microsoft Defender pour les registres de conteneurs** est facturé comme indiqué dans la [page des tarifs](https://azure.microsoft.com/pricing/details/security-center/)|
|Clouds :|:::image type="icon" source="./media/icons/yes-icon.png"::: Clouds commerciaux<br>:::image type="icon" source="./media/icons/no-icon.png"::: National (Azure Government, Azure China 21Vianet)|
|||

## <a name="prerequisites"></a>Prérequis

Pour analyser vos images lorsqu’elles sont envoyées par des workflows CI/CD dans vos registres, **Microsoft Defender pour les registres de conteneurs** doit être activé sur l’abonnement.

## <a name="set-up-vulnerability-scanning-of-your-cicd-workflows"></a>Configuration de l’analyse de vulnérabilité de vos workflows CI/CD

Pour activer les analyses de vulnérabilité des images dans vos workflows GitHub :

[Étape 1. Activer l’intégration de CI/CD dans Defender pour le cloud](#step-1-enable-the-cicd-integration-in-defender-for-cloud)

[Étape 2. Ajouter les lignes nécessaires à votre workflow GitHub](#step-2-add-the-necessary-lines-to-your-github-workflow-and-perform-a-scan)

### <a name="step-1-enable-the-cicd-integration-in-defender-for-cloud"></a>Étape 1. Activer l’intégration de CI/CD dans Defender pour le cloud

1. Dans le menu de Defender pour le cloud, ouvrez **Paramètres de l’environnement**.
1. Sélectionnez l’abonnement approprié.
1. Dans la barre latérale de la page des paramètres de cet abonnement, sélectionnez **Intégrations**.
1. Dans le volet qui s’affiche, sélectionnez un compte Application Insights pour envoyer les résultats de l’analyse CI/CD à partir de votre workflow.
1. Copiez le jeton d’authentification et la chaîne de connexion dans votre workflow GitHub.

    :::image type="content" source="./media/defender-for-container-registries-cicd/enable-cicd-integration.png" alt-text="Activer l’intégration CI/CD pour les analyses de vulnérabilité des images conteneur dans vos workflows GitHub" lightbox="./media/defender-for-container-registries-cicd/enable-cicd-integration.png":::

    > [!IMPORTANT]
    > Le jeton d’authentification et la chaîne de connexion sont utilisés pour mettre en corrélation la télémétrie de sécurité ingérée avec les ressources de l’abonnement. Si vous utilisez des valeurs non valides pour ces paramètres, la suppression des données de télémétrie s’ensuivra.

### <a name="step-2-add-the-necessary-lines-to-your-github-workflow-and-perform-a-scan"></a>Étape 2. Ajouter les lignes nécessaires à votre workflow GitHub et procéder à une analyse

1. À partir de votre workflow GitHub, activez l’analyse CI/CD comme suit :

    > [!TIP]
    > Nous vous recommandons de créer deux secrets dans votre dépôt pour référencer dans votre fichier YAML, comme indiqué ci-dessous. Les secrets peuvent être nommés en fonction de vos propres conventions de nommage. Dans cet exemple, les secrets sont référencés en tant que **AZ_APPINSIGHTS_CONNECTION_STRING** et **AZ_SUBSCRIPTION_TOKEN**.

    > [!IMPORTANT]
    >  L’envoi (push) au registre doit se produire avant la publication des résultats.

    ```yml
    - run: |
      echo "github.sha=$GITHUB_SHA"
      docker build -t githubdemo1.azurecr.io/k8sdemo:${{ github.sha }}
    
    - uses: Azure/container-scan@v0 
      name: Scan image for vulnerabilities
      id: container-scan
      continue-on-error: true
      with:
        image-name: githubdemo1.azurecr.io/k8sdemo:${{ github.sha }} 
    
    - name: Push Docker image - githubdemo1.azurecr.io/k8sdemo:${{ github.sha }}
      run: |
      docker push githubdemo1.azurecr.io/k8sdemo:${{ github.sha }}
    
    - name: Post logs to appinsights
      uses: Azure/publish-security-assessments@v0
      with: 
        scan-results-path: ${{ steps.container-scan.outputs.scan-report-path }}
        connection-string: ${{ secrets.AZ_APPINSIGHTS_CONNECTION_STRING }}
        subscription-token: ${{ secrets.AZ_SUBSCRIPTION_TOKEN }} 
    ```

1. Exécutez le workflow qui enverra l’image vers le registre de conteneurs sélectionné. Une fois l’image envoyée au registre, une analyse de celui-ci est effectuée et vous pouvez afficher les résultats de l’analyse CI/CD avec ceux de l’analyse du registre dans Microsoft Defender pour le cloud.

1. [Affichez les résultats de l’analyse CI/CD](#view-cicd-scan-results).

## <a name="view-cicd-scan-results"></a>Afficher les résultats de l’analyse CI/CD

1. Pour voir les résultats, accédez à la page **Recommandations**. Si des problèmes sont trouvés, la recommandation **Les vulnérabilités des images Azure Container Registry doivent être corrigées** s’affiche.

    ![Recommandation de correction des problèmes](media/monitor-container-security/acr-finding.png)

1. Sélectionnez la recommandation.

    La page de détails de la recommandation s’ouvre et présente des informations supplémentaires. Vous y trouverez notamment la liste des registres contenant des images vulnérables (« Ressources affectées ») et les étapes de correction.

1. Ouvrez la liste des **ressources affectées** et sélectionnez un registre non sain pour afficher dans celui-ci les dépôts qui contiennent des images vulnérables.

    :::image type="content" source="media/defender-for-container-registries-cicd/select-registry.png" alt-text="Sélectionnez un registre non sain.":::

    La page de détails du registre s’ouvre et présente la liste des référentiels affectés.

1. Sélectionnez un référentiel pour afficher les référentiels qui contiennent des images vulnérables.

    :::image type="content" source="media/defender-for-container-registries-cicd/select-repository.png" alt-text="Sélectionnez un dépôt non sain.":::

    La page de détails du référentiel s’ouvre. Elle liste les images vulnérables, accompagnées d’une évaluation de la gravité des vulnérabilités détectées.

1. Sélectionnez une image pour voir les vulnérabilités.

    :::image type="content" source="media/defender-for-container-registries-cicd/select-image.png" alt-text="Sélectionnez une image non saine.":::

    La liste des résultats pour l’image sélectionnée s’affiche.

    :::image type="content" source="media/defender-for-container-registries-cicd/cicd-scan-results.png" alt-text="Résultats de l’analyse de l’image.":::

1. Pour en apprendre davantage sur le workflow GitHub qui envoie ces images vulnérables, sélectionnez la bulle d’informations :

    :::image type="content" source="media/defender-for-container-registries-cicd/cicd-findings.png" alt-text="Résultats CI/CD sur des branches et des validations GitHub spécifiques.":::

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [les plans de protection avancée de Microsoft Defender for Cloud](defender-for-cloud-introduction.md).
