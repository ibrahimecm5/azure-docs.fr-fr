---
title: Meilleures pratiques opérateur - Gestion des images de conteneur dans Azure Kubernetes Services (AKS)
description: Découvrez les meilleures pratiques qui s’appliquent pour l’opérateur de cluster concernant la gestion et la sécurisation des images de conteneur dans Azure Kubernetes Service (ACS)
services: container-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: e6c767d1fdf765739f361b91df21e5bc104bc171
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306526"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Meilleures pratiques relatives à la gestion et à la sécurisation des images de conteneur dans Azure Kubernetes Services (AKS)

La sécurité des conteneurs et des images de conteneur est une priorité majeure lorsque vous développez et exécutez des applications dans Azure Kubernetes Service (AKS). Les conteneurs avec des images de base obsolètes ou des runtimes d’application non corrigés constituent un risque de sécurité et un possible vecteur d’attaque. 

Réduisez les risques en intégrant et en exécutant des outils d’analyse et de correction dans vos conteneurs au moment de la génération et du runtime. Plus vous avez intercepté rapidement la vulnérabilité ou l’image de base obsolète, plus votre cluster est sécurisé. 

Dans cet article, *« conteneurs »* signifie les deux :
* Les images de conteneur stockées dans un registre de conteneurs.
* Les conteneurs en cours d'exécution.

Cet article est dédié à la sécurisation de votre conteneur dans AKS. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Rechercher et corriger les vulnérabilités des images.
> * Déclencher et redéployer automatiquement les images de conteneur lors de la mise à jour d’une image de base.

Vous pouvez également consulter les bonnes pratiques relatives à la [sécurité des clusters][best-practices-cluster-security] et à la [sécurité du pod][best-practices-pod-security].

Vous pouvez également utiliser [la sécurité du conteneur dans Defender pour le cloud][security-center-containers] pour vous aider à analyser les vulnérabilités de vos conteneurs. L’[intégration d’Azure Container Registry][security-center-acr] avec Defender pour le cloud contribue à la protection de vos images et de votre registre contre les vulnérabilités.

## <a name="secure-the-images-and-run-time"></a>Sécuriser les images et le runtime

> **Conseils sur les bonnes pratiques** 
>
> Analysez vos images conteneur pour détecter les vulnérabilités. Déployez uniquement les images validées. Mettez régulièrement à jour les images de base et le runtime de l’application. Redéployez les charges de travail dans le cluster AKS.

Lorsque vous adoptez des charges de travail basées sur des conteneurs, vous voudrez vérifier la sécurité des images et du runtime utilisé pour créer vos propres applications. Comment éviter d’introduire des failles de sécurité dans vos déploiements ? 
* Intégrez à votre flux de travail de développement un processus pour analyser les images de conteneur à l’aide d’outils tels que [Twistlock][twistlock] ou [Aqua][aqua].
* Autorisez uniquement le déploiement des images vérifiées.

![Analyser et corriger les images de conteneur, les valider et les déployer](media/operator-best-practices-container-security/scan-container-images-simplified.png)

Par exemple, vous pouvez utiliser un pipeline d’intégration continue et de déploiement continu (CI/CD) pour automatiser les analyses, la vérification et les déploiements d’image. Azure Container Registry inclut ces fonctions d’analyse de vulnérabilités.

## <a name="automatically-build-new-images-on-base-image-update"></a>Générer automatiquement les nouvelles images sur la mise à jour de l’image de base

> **Conseils sur les bonnes pratiques** 
>
> Comme vous utilisez des images de base pour les images de l’application, utilisez l’automatisation pour générer de nouvelles images lorsque l’image de base est mise à jour. Comme les images de base incluent généralement des correctifs de sécurité, mettez à jour les images de conteneur d’application en aval.

Chaque fois qu’une image de base est mise à jour, vous devez également mettre à jour les images de conteneur en aval. Intégrez ce processus de génération aux pipelines de validation et de déploiement comme [Azure Pipelines][azure-pipelines] ou Jenkins. Ces pipelines permettent de s’assurer que vos applications continuent à s’exécuter sur les images de base mises à jour. Une fois que vos images de conteneur d’application sont validées, les déploiements AKS peuvent ensuite être mis à jour pour exécuter les images les plus récentes et les plus sécurisées.

Azure Container Registry Tasks peut également mettre à jour automatiquement les images de conteneur lorsque l’image de base est mise à jour. Avec cette fonctionnalité, vous créez des images de base et vous les maintenez à jour grâce à des correctifs de bogues et de sécurité.

Pour plus d’informations sur la mise à jour des images de base, consultez [Automatiser la génération des images en fonction de la mise à jour d’une image de base avec Azure Container Registry Tasks][acr-base-image-update].

## <a name="next-steps"></a>Étapes suivantes

Cet article était dédié à la sécurisation de vos conteneurs. Pour implémenter quelques-unes de ces pratiques, consultez les articles suivants :

* [Automatiser la génération des images en fonction de la mise à jour d’une image de base avec Azure Container Registry Tasks][acr-base-image-update]

<!-- EXTERNAL LINKS -->
[azure-pipelines]: /azure/devops/pipelines/
[twistlock]: https://www.twistlock.com/
[aqua]: https://www.aquasec.com/

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[acr-base-image-update]: ../container-registry/container-registry-tutorial-base-image-update.md
[security-center-containers]: ../security-center/container-security.md
[security-center-acr]: ../security-center/defender-for-container-registries-introduction.md
