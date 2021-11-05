---
title: Workflow CI/CD avec GitOps – Kubernetes avec Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: conceptual
description: Cet article fournit une vue d’ensemble conceptuelle d’un workflow CI/CD avec GitOps
keywords: GitOps, Kubernetes, K8s, Azure, Helm, Arc, AKS, Azure Kubernetes Service, conteneurs, intégration continue, livraison continue, Azure DevOps
ms.openlocfilehash: 837c0124739fa6659fc4c7652fcb9ca7be4fbf85
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131039655"
---
# <a name="cicd-workflow-using-gitops---azure-arc-enabled-kubernetes"></a>Workflow CI/CD avec GitOps – Kubernetes avec Azure Arc

Les déploiements Kubernetes modernes hébergent plusieurs applications, clusters et environnements. GitOps vous permet de gérer ces configurations complexes plus facilement, en effectuant le suivi de l’état souhaité des environnements Kubernetes avec Git. En utilisant les outils Git courants pour suivre l’état des clusters, vous pouvez augmenter la responsabilité, faciliter l’investigation des défaillances et activer l’automatisation pour gérer les environnements.

Cette vue d’ensemble conceptuelle explique GitOps en tant que réalité dans le cycle de vie entier des modifications d’application à l’aide d’Azure Arc, d’Azure Repos et d’Azure Pipelines. [Accédez à un exemple](#example-workflow) de changement d’application unique dans des environnements Kubernetes contrôlés par GitOps.

## <a name="architecture"></a>Architecture

Considérez une application déployée dans un ou plusieurs environnements Kubernetes.

![Architecture CI/CD GitOps](./media/gitops-arch.png)

### <a name="application-repo"></a>Référentiel d’application
Le référentiel d’application contient le code d’application sur lequel les développeurs travaillent au cours de leur boucle interne. Les modèles de déploiement d’application se trouvent dans ce référentiel sous forme générique, comme Helm ou Kustomize. Les valeurs spécifiques à l’environnement ne sont pas stockées. Les modifications apportées à ce référentiel appellent un pipeline de demande de tirage (pull request) ou d’intégration continue qui démarre le processus de déploiement.
### <a name="container-registry"></a>Container Registry
Le registre de conteneurs contient toutes les images internes et tierces utilisées dans les environnements Kubernetes. Dotez les images d’application tierces de balises lisibles et de la validation Git utilisée pour créer l’image. Mettez en cache les images tierces à des fins de sécurité, de vitesse et de résilience. Définissez un plan à des fins de test et d’intégration en temps opportun des mises à jour de sécurité. Pour plus d’informations, consultez le guide relatif à [la consommation ACR et au maintien du contenu public](../../container-registry/tasks-consume-public-content.md) pour un exemple.
### <a name="pr-pipeline"></a>Pipeline de demande de tirage (pull request)
Les demandes de tirage (pull requests) à destination du référentiel d’application sont contrôlées lors de l’exécution réussie du pipeline de demande de tirage. Ce pipeline exécute des contrôles de qualité de base, linting et tests unitaires par exemple, sur le code d’application. Le pipeline teste l’application et effectue le linting des Dockerfiles et des modèles Helm utilisés à des fins de déploiement dans un environnement Kubernetes. Les images Docker doivent être générées et testées, mais pas envoyées (push). Optez pour une durée de pipeline relativement courte pour permettre une itération rapide.
### <a name="ci-pipeline"></a>Pipeline d’intégration continue
Le pipeline d’intégration continue d’application exécute toutes les étapes du pipeline de demande de tirage (pull request) et développe les contrôles de test et de déploiement. Le pipeline peut être exécuté pour chaque validation ou à une cadence régulière avec un groupe de validations. À ce niveau, effectuez des tests d’application trop longs pour un pipeline de demande de tirage. Envoyez (push) les images Docker au registre de conteneurs après la génération pour préparer le déploiement. Le modèle remplacé peut faire l’objet d’un linting avec un ensemble de valeurs de test. À ce stade, les images utilisées au moment de l’exécution du service doivent faire l’objet d’un linting, être créées et testées. Dans la build d’intégration continue, les artefacts sont publiés pour l’étape de déploiement continu à utiliser pour préparer le déploiement.
### <a name="flux"></a>Flux
Le service Flux s’exécute dans chaque cluster et assure le maintien de l’état souhaité. Le service interroge fréquemment le référentiel GitOps pour rechercher les modifications apportées à son cluster et les applique.
### <a name="cd-pipeline"></a>Pipeline de déploiement continu
Le pipeline de déploiement continu est automatiquement déclenché par les builds d’intégration continue qui ont abouti. Il utilise les modèles précédemment publiés, remplace les valeurs d’environnement et ouvre une demande de tirage (pull request) à destination du référentiel GitOps pour demander une modification de l’état souhaité d’un ou plusieurs clusters Kubernetes. Les administrateurs de cluster examinent la demande de tirage de modification de l’état et approuvent la fusion vers le référentiel GitOps. Le pipeline attend ensuite que la demande de tirage soit terminée, ce qui permet à Flux de récupérer la modification de l’état.
### <a name="gitops-repo"></a>Référentiel GitOps
Le référentiel GitOps représente l’état souhaité actuel de tous les environnements entre les clusters. Toute modification apportée à ce référentiel est récupérée par le service Flux dans chaque cluster et déployée. Les demandes de tirage sont créées avec les modifications apportées à l’état souhaité, révisées et fusionnées. Ces demandes de tirage contiennent les modifications apportées aux modèles de déploiement et aux manifestes de rendu Kubernetes qui en résultent. Les manifestes de bas niveau permettent une inspection plus minutieuse des modifications généralement invisibles au niveau du modèle.
### <a name="kubernetes-clusters"></a>Clusters Kubernetes
Au moins un cluster Kubernetes avec Azure Arc dessert les différents environnements que l’application requiert. Par exemple, un cluster unique peut desservir un environnement de développement et d’assurance qualité par le biais de différents espaces de noms. Un deuxième cluster permet une séparation plus aisée des environnements et un contrôle plus précis.
## <a name="example-workflow"></a>Exemple de flux de travail
En tant que développeur d’applications, Alice :
* Écrit le code de l’application.
* Détermine comment exécuter l’application dans un conteneur Docker.
* Définit les modèles qui exécutent le conteneur et les services dépendants dans un cluster Kubernetes.

Alors qu’Alice sait que l’application doit pouvoir s’exécuter dans plusieurs environnements, elle ne connaît pas les paramètres spécifiques de chaque environnement.

Supposons qu’Alice souhaite apporter une modification d’application qui modifie l’image Docker utilisée dans le modèle de déploiement d’application.

1. Alice modifie le modèle de déploiement, l’envoie (push) à une branche distante sur le référentiel d’application et ouvre une demande de tirage (pull request) à des fins de vérification.
2. Alice demande à son équipe de vérifier la modification.
    * Le pipeline de demande de tirage (pull request) exécute la validation.
    * Après une exécution de pipeline réussie, l’équipe se déconnecte et la modification est fusionnée.
3. Le pipeline d’intégration continue valide la modification d’Alice et son exécution est menée à bien.
    * La modification peut être déployée en toute sécurité sur le cluster, et les artefacts sont enregistrés dans l’exécution du pipeline d’intégration continue.
4. La modification d’Alice fusionne et déclenche le pipeline de déploiement continu.
    * Le pipeline de déploiement continu récupère les artefacts stockés par l’exécution du pipeline d’intégration continue d’Alice.
    * Le pipeline de déploiement continu remplace les modèles par des valeurs spécifiques de l’environnement, et étend les modifications apportées à l’état du cluster existant dans le dépôt GitOps.
    * Le pipeline de déploiement continu crée une demande de tirage (pull request) à destination du référentiel GitOps avec les modifications souhaitées en termes d’état du cluster.
5. L’équipe d’Alice vérifie et approuve sa demande de tirage (pull request).
    * La modification est fusionnée dans la branche cible correspondant à l’environnement.
6. En quelques minutes, Flux remarque qu’une modification a été apportée au référentiel GitOps et tire (pull) la modification d’Alice.
    * L’image Docker ayant changé, le pod d’application requiert une mise à jour.
    * Flux applique la modification au cluster.
7. Alice teste le point de terminaison de l’application pour vérifier le bon déroulement du déploiement.
   > [!NOTE]
   > Pour plus d’environnements ciblés pour le déploiement, le pipeline de déploiement continu itère en créant une demande de tirage (pull request) pour l’environnement suivant et répète les étapes 4 à 7. Le processus requiert une approbation supplémentaire pour les déploiements ou environnements plus risqués, comme une modification liée à la sécurité ou un environnement de production.
8.  Lorsque tous les environnements ont reçu des déploiements réussis, le pipeline prend fin.

## <a name="next-steps"></a>Étapes suivantes
Découvrez comment créer des connexions entre votre cluster et un dépôt Git en guise de [ressource de configuration intégrant Kubernetes avec Azure Arc](./conceptual-configurations.md)
