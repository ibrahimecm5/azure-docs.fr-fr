---
title: GitOps et offre Dev/Test Azure
description: Utiliser GitOps en association avec l’offre Dev/Test Azure
author: jamestramel
ms.author: jametra
ms.prod: visual-studio-windows
ms.topic: how-to
ms.date: 10/20/2021
ms.custom: devtestoffer
ms.openlocfilehash: df3fd830c04f90315126bd68a07a22a95cbfbc53
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097626"
---
# <a name="using-gitops-with-azure-devtest-offer-to-optimize-and-secure-applications"></a>Utilisation de GitOps avec l’offre Dev/Test Azure pour optimiser et sécuriser les applications

GitOps est une infrastructure opérationnelle. Elle prend les meilleures pratiques DevOps utilisées pour développer des applications et les applique à l’automatisation de l’infrastructure.  

Lorsque les équipes exécutent GitOps, elles utilisent des fichiers de configuration stockés en tant que code (infrastructure en tant que code). Ces fichiers génèrent le même environnement chaque fois qu’elle est déployée. Imaginez que le code source de l’application génère les mêmes fichiers binaires d’application chaque fois que vous la générez.  

## <a name="gitops-methodology"></a>Méthodologie GitOps  

Ce processus, ou méthodologie, utilise des référentiels Git. Ces référentiels sont une source fiable pour un état et une configuration que vous définissez pour votre application. Ils contiennent des descriptions déclaratives de l’infrastructure dont vous avez besoin en production. Un processus automatisé permet à cet environnement de correspondre à l’état décrit dans le référentiel.  

Pour déployer une nouvelle application ou en mettre une à jour, il vous suffit de mettre à jour le référentiel ; le processus automatisé gère tout le reste.  

## <a name="benefits-of-gitops"></a>Avantages de GitOps  

- Permet la collaboration sur les modifications apportées à l’infrastructure  
- Contrôle d’accès amélioré  
- Réduction du délai de mise sur le marché  
- Réduction des risques  
- Coût réduit  
- Moins sujette aux erreurs  

## <a name="use-gitops-with-devtest"></a>Utiliser GitOps avec Dev/Test  

En tant que processus et infrastructure, GitOps doit être appliquée à vos instances hors production. Elle peut être vérifiée ou utilisée dans vos environnements Dev/Test. Vous pouvez utiliser les principes GitOps pour améliorer vos processus DevOps. Utilisez vos avantages et environnements Dev/Test avec des principes GitOps pour optimiser vos activités et maintenir la sécurité et la fiabilité de vos applications.  

GitOps combine l’automatisation et les infrastructures de collaboration couramment utilisées comme Git. Elles peuvent être combinées pour offrir une livraison rapide de l’infrastructure Cloud tout en respectant les normes de sécurité de l’entreprise.  

En savoir plus sur GitOps et Azure :  

- [Vidéo Azure Friday : Kubernetes avec Azure Arc et GitOps](https://azure.microsoft.com/resources/videos/azure-friday-azure-arc-enabled-kubernetes-with-gitops/)  
- [Blog Azure Friday : Kubernetes avec Azure Arc et GitOps](https://techcommunity.microsoft.com/t5/azure-arc/azure-arc-enabled-kubernetes-with-gitops/ba-p/1654171?ocid=AID754288&wt.mc_id=azfr-c9-scottha&wt.mc_id=CFID0570)  
- [GitOps pour l’automatisation du cycle de vie de l’infrastructure Azure](https://github.com/travisnielsen/azure-gitops)
