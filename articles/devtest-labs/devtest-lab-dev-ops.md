---
title: Intégrer Azure DevTest Labs et DevOps
description: Découvrez comment utiliser des labos au sein d’un pipeline d’intégration continue (CI) et de livraison continue (CD) dans un environnement d’entreprise.
ms.topic: conceptual
ms.date: 06/26/2020
ms.openlocfilehash: 59c259a27fa7e171a6e6aa6bd7bc2ffa25fa89fd
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286333"
---
# <a name="integrate-azure-devtest-labs-and-azure-devops"></a>Intégrer Azure DevTest Labs et Azure DevOps
DevOps est une méthodologie de développement de logiciel qui intègre le développement de logiciels (Dev) avec des opérations (Ops) pour un système. Ce système peut fournir de nouvelles fonctionnalités, des mises à jour et des correctifs adaptés aux objectifs de l’entreprise. Cette méthodologie comprend tout, de la conception de nouvelles fonctionnalités basées sur les objectifs, les modèles d’utilisation et les commentaires des clients ; à la résolution, la récupération et le renforcement du système en cas de problèmes. Un composant facilement identifié de cette méthodologie est le pipeline d’intégration continue (CI) et de livraison continue (CD). Un pipeline CI/CD prend les informations, le code et les ressources d’une validation à l’aide d’une série d'étapes pour produire le système. Les étapes incluent la génération, le test et le déploiement. Cet article se concentre sur les différentes façons d’utiliser efficacement les labs au sein d’un pipeline dans un environnement d’entreprise. 

## <a name="benefits-of-using-labs-in-devops-workflow"></a>Avantages de l’utilisation des labs dans un flux de travail DevOps 

### <a name="focused-access"></a>Accès concentré 
L’utilisation d’un lab en tant que composant permet à un écosystème spécifique d’être associé à un groupe limité de personnes. En règle générale, un groupe ou une équipe qui travaille dans une zone commune ou avec une fonctionnalité spécifique dispose d’un labo assigné.   

### <a name="infrastructure-replication-in-the-cloud"></a>Réplication d’infrastructure dans le cloud 
Un développeur peut configurer rapidement un écosystème de développement qui inclut une boîte de développement avec outils et code source. Le développeur peut également créer un environnement quasi identique à la configuration de production. Cet environnement permet d’accélérer le développement de la boucle interne. 

### <a name="pre-production"></a>Préproduction 
En disposant d’un lab dans le pipeline CI/CD, il est plus simple d’avoir plusieurs environnements ou machines de préproduction différents qui s’exécutent en même temps pour des tests asynchrones. Des infrastructures de support différentes, telles que des agents de build, peuvent être déployées et gérées au sein d’un lab. 

## <a name="devops-with-devtest-labs"></a>DevOps avec DevTest Labs 

### <a name="development--operation"></a>Développement / opération 
Un lab doit se concentrer sur une équipe qui travaille dans une zone de fonctionnalité. Cet intérêt commun permet de partager des ressources spécifiques à une zone, comme des outils, des scripts, ou des modèles Resource Manager. Cette combinaison permet des modifications plus rapides tout en limitant les effets négatifs à un groupe plus petit. Les ressources partagées permettent au développeur de créer des machines virtuelles de développement avec le code, les outils et la configuration dont il a besoin. Les ressources peuvent être créées de manière dynamique ou dans un système qui crée des images de base avec des personnalisations. Le développeur peut non seulement créer des machines virtuelles, mais aussi des environnements DevTest Labs en fonction des modèles nécessaires afin de créer les ressources Azure appropriées dans le labo. Toute modification ou tâche destructive peut être effectuée sur l’environnement lab sans affecter qui que ce soit d’autre. Prenons pour exemple le scénario où le produit est un système autonome installé sur la machine du client. Dans ce scénario, DevTest Labs a amélioré la création de machine virtuelle, qui inclut l’installation des logiciels à l’aide des artefacts et la création préalable des configurations de client pour un test du code de la boucle interne plus rapide. 
  
## <a name="cicd-pipeline"></a>Pipeline CI/CD 
Le pipeline CI/CD est l’un des composants critiques de DevOps. Le pipeline déplace le code de la demande de tirage (pull) d’un développeur, l’intègre au code existant, puis le déploie dans l’écosystème de production. Toutes les ressources n’ont pas besoin d’être dans un lab. Par exemple, un hôte Jenkins pourrait être configuré en dehors du laboratoire comme une ressource plus persistante. Voici quelques exemples spécifiques de l’intégration de labs dans le pipeline. 

### <a name="build"></a>Build 
Le pipeline de build se concentre sur la création d’un package de composants testés ensemble pour être remis au pipeline de mise en production. Les labos peuvent faire partie du pipeline de build en tant qu’emplacements pour les agents de build et d’autres ressources de support. La possibilité de générer dynamiquement l’infrastructure offre un meilleur contrôle. Avec la possibilité d’utiliser plusieurs environnements dans un labo, chaque build peut être exécuté de manière asynchrone. L'ID de build fait partie des informations d'environnement qui identifient de manière unique les ressources du build spécifique.   

Pour les agents de build, la capacité d’un lab à limiter l’accès améliore la sécurité et réduit les risques de corruption accidentelle.  

### <a name="test"></a>Test 
DevTest Labs permet à un pipeline CI/CD d’automatiser la création de ressources Azure, telles que des machines virtuelles, et des environnements, pouvant être utilisés pour effectuer des tests automatisés et manuels. Les machines virtuelles sont créées à l’aide des artefacts ou des formules qui utilisent les informations du processus de génération pour créer les différentes configurations personnalisées nécessaires pour le test.   

### <a name="release"></a>Libérer 
DevTest Labs est couramment utilisé pour la vérification dans la section de mise en production avant le déploiement du code. Le processus est similaire au test dans la section Build. Les ressources de production ne doivent pas être déployées dans DevTest Labs. 

### <a name="customization"></a>Personnalisation 
Dans Azure DevOps, il existe des tâches qui permettent la manipulation des machines virtuelles et des environnements dans des labs spécifiques. Azure DevOps Services est un moyen de gérer le pipeline CI/CD. Vous pouvez intégrer le labo à tout système capable d’appeler des API REST, d’exécuter des scripts PowerShell ou d’utiliser Azure CLI. 

Certains gestionnaires de pipeline CI/CD disposent de plugin open source existants qui peuvent gérer des ressources dans Azure et DevTest Labs. Vous devrez peut-être utiliser des scripts personnalisés pour répondre aux besoins spécifiques du pipeline.  En gardant ceci à l’esprit, lors de l’exécution d’une tâche, un principal de service est utilisé avec le rôle approprié pour accéder au labo. Le principal de service a généralement besoin du rôle de contributeur pour accéder au labo. Pour en savoir plus, consultez [Intégrer Azure DevTest Labs à votre pipeline de livraison et d’intégration continue Azure DevOps](devtest-lab-integrate-ci-cd.md). 
 