---
title: Validation des alertes dans Microsoft Defender pour le cloud | Microsoft Docs
description: Découvrir comment vérifier que vos alertes de sécurité sont correctement configurées dans Microsoft Defender pour le cloud
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/17/2021
ms.author: memildin
ms.openlocfilehash: b5acd38ec3885fab3d1de9d70a80fa76cd9c60b0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131453285"
---
# <a name="alert-validation-in-microsoft-defender-for-cloud"></a>Validation des alertes dans Microsoft Defender pour le cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Ce document est conçu pour vous apprendre à vérifier si votre système est correctement configuré pour les alertes dans Microsoft Defender pour le cloud.

## <a name="what-are-security-alerts"></a>Que sont les alertes de sécurité ?
Les alertes sont les notifications générées par Defender pour le cloud lorsqu’il détecte des menaces pesant sur des ressources. Il hiérarchise et répertorie les alertes ainsi que les informations vous permettant d’analyser rapidement le problème. Defender pour le cloud fournit également des suggestions sur la manière dont vous pouvez corriger les problèmes liés à une attaque.
Pour plus d’informations, consultez [Alertes de sécurité dans Defender pour le cloud](alerts-overview.md) et [Gestion et résolution des alertes de sécurité](managing-and-responding-alerts.md)


## <a name="generate-sample-security-alerts"></a>Générer des exemples d’alertes de sécurité

Si vous utilisez la nouvelle expérience d’alertes en préversion comme décrit dans [Gérer et résoudre les alertes de sécurité dans Microsoft Defender pour le cloud](managing-and-responding-alerts.md), vous pouvez créer des exemples d’alertes en quelques clics dans la page des alertes de sécurité du portail Azure.

Utilisez des exemples d’alertes pour :

- évaluer la valeur et les fonctionnalités de vos plans Microsoft Defender
- valider les configurations que vous avez effectuées pour vos alertes de sécurité (telles que les intégrations SIEM, l’automatisation des flux de travail et les notifications par e-mail).

Pour créer des exemples d’alertes :

1. En tant qu’utilisateur avec le rôle **Collaborateur de l’abonnement**, dans la barre d’outils de la page Alertes, sélectionnez **Créer des exemples d’alertes**.
1. Sélectionnez l’abonnement.
1. Sélectionnez le ou les plans Microsoft Defender appropriés pour lesquels vous souhaitez afficher les alertes. 
1. Sélectionnez **Créer des exemples d’alertes**.

    :::image type="content" source="media/alert-validation/create-sample-alerts-procedures.png" alt-text="Étapes de création d’exemples d’alertes dans Microsoft Defender pour le cloud.":::
    
    Une notification s’affiche pour vous informer que les exemples d’alertes sont en cours de création :

    :::image type="content" source="media/alert-validation/notification-sample-alerts-creation.png" alt-text="Notification indiquant que les exemples d’alertes sont générés.":::

    Après quelques minutes, les alertes s’affichent dans la page des alertes de sécurité. Elles apparaîtront également dans tous les autres emplacements que vous avez configurés pour recevoir vos alertes de sécurité Microsoft Defender pour le cloud (SIEM connectés, notifications par e-mail, etc.).

    :::image type="content" source="media/alert-validation/sample-alerts.png" alt-text="Exemples d’alertes dans la liste des alertes de sécurité.":::

    > [!TIP]
    > Les alertes concernent ont trait à des ressources simulées.

## <a name="simulate-alerts-on-your-azure-vms-windows"></a>Simuler des alertes sur vos machines virtuelles Azure (Windows) <a name="validate-windows"></a>

Après avoir installé l’agent Log Analytics sur votre machine, suivez les étapes ci-dessous depuis l’ordinateur sur lequel vous voulez configurer l’alerte de la ressource attaquée :

1. Copiez un exécutable (par exemple, **calc.exe**) sur le bureau de votre ordinateur ou dans le répertoire de votre choix et renommez-le **ASC_AlertTest_662jfi039N.exe**.
1. Ouvrez l’invite de commandes et exécutez ce fichier avec un argument (un faux nom d’argument), tel que : ```ASC_AlertTest_662jfi039N.exe -foo```
1. Patientez 5 à 10 minutes et ouvrez les alertes Defender pour le cloud. Une alerte doit s’afficher.

> [!NOTE]
> En examinant cette alerte de test, veillez à ce que la valeur du champ **Arguments Auditing Enabled** (Audit pour arguments activé) soit **true**. S’il est **false**, vous devez activer la ligne de commande d’audit pour arguments. Pour ce faire, utilisez la commande suivante :
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="simulate-alerts-on-your-azure-vms-linux"></a>Simuler des alertes sur vos machines virtuelles Azure (Linux) <a name="validate-linux"></a>

Après avoir installé l’agent Log Analytics sur votre machine, suivez les étapes ci-dessous depuis l’ordinateur sur lequel vous voulez configurer l’alerte de la ressource attaquée :

1. Copiez un fichier exécutable dans un emplacement approprié et renommez-le `./asc_alerttest_662jfi039n`. Par exemple :

    `cp /bin/echo ./asc_alerttest_662jfi039n`

1. Ouvrez l’invite de commandes et exécutez ce fichier :

    `./asc_alerttest_662jfi039n testing eicar pipe`

1. Patientez 5 à 10 minutes, puis ouvrez les alertes Defender pour le cloud. Une alerte doit s’afficher.

## <a name="simulate-alerts-on-kubernetes"></a>Simuler des alertes sur Kubernetes <a name="validate-kubernetes"></a>

Si vous avez intégré Azure Kubernetes Service à Defender pour le cloud, vous pouvez tester le fonctionnement de vos alertes avec la commande kubectl suivante :

`kubectl get pods --namespace=asc-alerttest-662jfi039n`

Pour en savoir plus sur la protection de vos nœuds et clusters Kubernetes, consultez [Présentation de Microsoft Defender pour Kubernetes](defender-for-kubernetes-introduction.md)

## <a name="next-steps"></a>Étapes suivantes
Cet article vous a présenté le processus de validation des alertes. Maintenant que vous êtes familiarisé avec la validation, vous pouvez consulter les articles suivants :

* [Validation de la détection des menaces Azure Key Vault dans Microsoft Defender pour le cloud](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [Gestion et résolution des alertes de sécurité dans Microsoft Defender pour le cloud](managing-and-responding-alerts.md) : apprenez à gérer les alertes et à résoudre les alertes de sécurité dans Defender pour le cloud.
* [Présentation des alertes de sécurité dans Microsoft Defender pour le cloud](./alerts-overview.md) : en savoir plus sur les différents types d’alertes de sécurité.
