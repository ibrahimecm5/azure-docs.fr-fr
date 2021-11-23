---
title: Déplacer votre application de fonction entre régions dans Azure Functions
description: Découvrez comment déplacer des ressources Azure Functions d’une région vers une autre en créant une copie de vos ressources Azure Function existantes dans la région cible.
ms.topic: how-to
ms.service: azure-functions
author: nzthiago
ms.date: 05/11/2021
ms.custom: subject-moving-resources
ms.openlocfilehash: c106ec2e5e4592937974f040e1ae14aabc45f354
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132138965"
---
# <a name="move-your-function-app-between-regions-in-azure-functions"></a>Déplacer votre application de fonction entre régions dans Azure Functions

Cet article explique comment déplacer des ressources Azure Functions vers une autre région Azure. Vous pouvez déplacer vos ressources vers une autre région pour l’une des raisons suivantes :
 + Tirer parti d’une nouvelle région Azure
 + Déployer des fonctionnalités ou services disponibles uniquement dans des régions spécifiques
 + Respecter les exigences de gouvernance et de stratégie internes
 + Répondre à des exigences de planification de la capacité

Les ressources Azure Functions sont spécifiques d’une région et ne peuvent pas être déplacées d’une région à l’autre. Vous devez créer une copie de vos ressources d’application de fonction existantes dans la région cible, puis redéployer le code de vos fonctions sur la nouvelle application.

Si un temps d’arrêt minimal est requis, envisagez d’exécuter votre application de fonction dans les deux régions pour implémenter une architecture de récupération d’urgence :
+ [Géo-reprise d’activité après sinistre dans Azure Functions](functions-geo-disaster-recovery.md)
+ [Récupération d’urgence et géodistribution dans Azure Durable Functions](durable/durable-functions-disaster-recovery-geo-distribution.md)

## <a name="prerequisites"></a>Prérequis

+ S’assurer que la région cible prend en charge Azure Functions et tout service associé dont vous souhaitez déplacer les ressources
+ Avoir accès au code source d’origine pour les fonctions que vous migrez

## <a name="prepare"></a>Préparation

Identifiez toutes les ressources d’application de fonction utilisées sur la région source, qui peuvent inclure les éléments suivants :

+ Conteneur de fonctions
+ [Plan d’hébergement](functions-scale.md#overview-of-plans)
+ [Emplacements de déploiement](functions-deployment-slots.md)
+ [Domaines personnalisés achetés dans Azure](../app-service/manage-custom-dns-buy-domain.md)
+ [Certificats et paramètres TLS/SSL](../app-service/configure-ssl-certificate.md)
+ [Options de mise en réseau configurées](functions-networking-options.md)
+ [Identités managées](../app-service/overview-managed-identity.md)
+ [Paramètres d’application configurés](functions-how-to-use-azure-function-app-settings.md) : les utilisateurs disposant d’un accès suffisant peuvent copier tous les paramètres de l’application source à l’aide de la fonctionnalité Modification avancée dans le portail
+ [Mise à l’échelle des configurations](functions-scale.md#scale)

Vos fonctions peuvent se connecter à d’autres ressources à l’aide de déclencheurs ou de liaisons. Pour plus d’informations sur la façon de déplacer ces ressources d’une région à l’autre, consultez la documentation des services respectifs.

Vous devez également être en mesure d’[exporter un modèle à partir de ressources existantes](../azure-resource-manager/templates/export-template-portal.md).

## <a name="move"></a>Déplacer

Déployez l’application de fonction dans la région cible et examinez les ressources configurées. 

### <a name="redeploy-function-app"></a>Redéployer l’application de fonction

Si vous avez accès aux ressources de déploiement et d’automatisation qui ont créé l’application de fonction dans la région source, réexécutez les mêmes étapes de déploiement dans la région cible pour créer et redéployer votre application. 

Si vous avez uniquement accès au code source, mais pas aux ressources de déploiement et d’automatisation, vous pouvez déployer et configurer l’application de fonction sur la région cible à l’aide de l’une des [technologies de déploiement](functions-deployment-technologies.md) disponibles ou de l’une des [méthodes de déploiement continu](functions-continuous-deployment.md).

### <a name="review-configured-resources"></a>Examiner les ressources configurées

Examinez et configurez les ressources identifiées à l’étape [Préparation](#prepare) ci-dessus dans la région cible si elles n’ont pas été configurées pendant le déploiement.

### <a name="move-considerations"></a>Considérations relatives au déplacement
+ Si vos ressources de déploiement et l’automatisation ne créent pas d’application de fonction, [créez une application du même type dans un nouveau plan d’hébergement](functions-scale.md#overview-of-plans) dans la région cible
+ Les noms d’application de fonction étant globalement uniques dans Azure, l’application dans la région cible ne peut pas porter le même nom que celle dans la région source
+ Les références et les paramètres d’application qui connectent votre application de fonction aux dépendances doivent être examinés et, si nécessaire, mis à jour. Par exemple, lorsque vous déplacez une base de données que vos fonctions appellent, vous devez également mettre à jour les paramètres ou la configuration de l’application pour vous connecter à la base de données dans la région cible. Certains paramètres de l’application, tels que la clé d’instrumentation Application Insights ou le compte de stockage Azure utilisé par l’application de fonction peuvent être déjà configurés sur la région cible et n’ont pas besoin d’être mis à jour
+ N’oubliez pas de vérifier votre configuration et de tester vos fonctions dans la région cible
+ Si vous avez configuré un domaine personnalisé, [remappez le nom de domaine](../app-service/manage-custom-dns-migrate-domain.md#remap-the-active-dns-name)
+ Pour les fonctions qui s’exécutent sur des plans dédiés, examinez également le [Plan de migration App service](../app-service/manage-move-across-regions.md) au cas où il serait partagé avec des applications web

## <a name="clean-up-source-resources"></a>Nettoyer les ressources sources

Une fois le déplacement terminé, supprimez l’application de fonction et le plan d’hébergement de la région source. Vous payez pour des applications de fonction dans des plans Premium ou Dedicated, même quand l’application elle-même n’est pas en cours d’exécution.

## <a name="next-steps"></a>Étapes suivantes

+ Consultez le [Centre des architectures Azure](/azure/architecture/browse/?expanded=azure&products=azure-functions) pour obtenir des exemples d’Azure Functions s’exécutant dans plusieurs régions dans le cadre d’architectures de solution plus avancées
