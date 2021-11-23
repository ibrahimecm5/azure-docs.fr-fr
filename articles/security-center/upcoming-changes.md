---
title: Modifications importantes concernant Microsoft Defender pour le cloud
description: Changements à venir concernant Microsoft Defender pour le cloud dont vous devez peut-être tenir compte et pour lesquels une planification peut être nécessaire
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 11/10/2021
ms.author: memildin
ms.openlocfilehash: fca622c8c49a16d9926a5fc1e8e0f0064ee758d7
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132157442"
---
# <a name="important-upcoming-changes-to-microsoft-defender-for-cloud"></a>Modifications importantes à venir concernant Microsoft Defender pour le cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
> Les informations de cette page concernent des produits ou fonctionnalités en préversion, qui peuvent être modifiés de façon substantielle avant d’être commercialisés, le cas échéant. Microsoft n’offre aucun engagement ni garantie, formel ou implicite, concernant les informations contenues dans ce document.

Dans cette page, vous allez découvrir les changements qui sont planifiés pour Defender pour le cloud. Elle décrit les changements planifiés du produit qui peuvent avoir un impact sur votre degré de sécurisation ou vos workflows.

Si vous recherchez les notes de publication les plus récentes, vous les trouverez dans [Nouveautés de Microsoft Defender pour le cloud](release-notes.md).


## <a name="planned-changes"></a>Changements planifiés

| Modifications planifiées       | Date estimée de la modification |
|----------------------|---------------------------|
| [Dépréciation d’une alerte de préversion : ARM.MCAS_ActivityFromAnonymousIPAddresses](#deprecating-a-preview-alert-armmcas_activityfromanonymousipaddresses)             | Novembre 2021|
| [L’implémentation héritée de la norme ISO 27001 est remplacée par la nouvelle norme ISO 27001:2013](#legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013)| Novembre 2021|
| [L’affichage de l’inventaire des machines locales utilise un autre modèle pour le nom de ressource](#inventory-display-of-on-premises-machines-will-use-different-template-for-resource-name)    | Novembre 2021    |
| [Changements multiples apportés aux recommandations sur l’identité](#multiple-changes-to-identity-recommendations)                                                                                          | Décembre 2021    |
| [Modifications apportées à une alerte de sécurité provenant de Microsoft Defender pour le stockage](#changes-to-a-security-alert-from-microsoft-defender-for-storage)  | Novembre 2021    |
| [Améliorations apportées à la recommandation pour classer les données sensibles dans les bases de données SQL](#enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases)   | PREMIER TRIMESTRE 2022    |
| [Modifications apportées aux suggestions pour la gestion des solutions de protection des points de terminaison](#changes-to-recommendations-for-managing-endpoint-protection-solutions)             | Février 2022| 
|||

### <a name="deprecating-a-preview-alert-armmcas_activityfromanonymousipaddresses"></a>Dépréciation d’une alerte de préversion : ARM.MCAS_ActivityFromAnonymousIPAddresses

**Date estimée du changement :** novembre 2021

Nous allons déprécier l’alerte de préversion suivante :

|Nom de l’alerte| Description|
|----------------------|---------------------------|
|**PRÉVERSION – Activité à partir d’une adresse IP à risque**<br>(ARM.MCAS_ActivityFromAnonymousIPAddresses)|Une activité utilisateur depuis une adresse IP qui a été identifiée comme adresse IP de proxy anonyme a été détectée.<br>Ces proxys sont utilisés par des individus souhaitant masquer l’adresse IP de leur appareil et peuvent être utilisés dans un but malveillant. Cette détection utilise un algorithme Machine Learning qui réduit les faux positifs, tels que les adresses IP mal étiquetées qui sont largement utilisées par d’autres utilisateurs de l’organisation.<br>Requiert une licence active de Microsoft Cloud App Security.|
|||

Nous avons mis au point de nouvelles alertes qui fournissent ces informations et qui les complètent. De plus, les alertes plus récentes (ARM_OperationFromSuspiciousIP, ARM_OperationFromSuspiciousProxyIP) ne demandent pas de licence pour Microsoft Defender pour les applications cloud (anciennement Microsoft Cloud App Security).

### <a name="legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013"></a>L’implémentation héritée de la norme ISO 27001 est remplacée par la nouvelle norme ISO 27001:2013

**Date estimée du changement :** novembre 2021

L’implémentation héritée de la norme ISO 27001 sera supprimée du tableau de bord de conformité réglementaire de Defender pour le cloud. Si vous effectuez le suivi de la conformité ISO 27001 avec Defender pour le cloud, intégrez la nouvelle norme ISO 27001:2013 pour tous les groupes d’administration ou abonnements appropriés, car la norme ISO 27001 actuelle sera bientôt supprimée du tableau de bord.

:::image type="content" source="media/upcoming-changes/removing-iso-27001-legacy-implementation.png" alt-text="Tableau de bord de conformité réglementaire Defender pour le cloud présentant le message relatif à la suppression de l’implémentation héritée de la norme ISO 27001." lightbox="media/upcoming-changes/removing-iso-27001-legacy-implementation.png":::


### <a name="inventory-display-of-on-premises-machines-will-use-different-template-for-resource-name"></a>L’affichage de l’inventaire des machines locales utilise un autre modèle pour le nom de ressource

**Date estimée du changement :** novembre 2021

Pour améliorer la présentation des ressources dans l’[inventaire des ressources](asset-inventory.md), nous allons supprimer l’élément « source-computer-IP » du modèle de nommage des machines locales.

- **Format actuel :** ``machine-name_source-computer-id_VMUUID``
- **À partir de la mise à jour :** ``machine-name_VMUUID``


### <a name="multiple-changes-to-identity-recommendations"></a>Changements multiples apportés aux recommandations sur l’identité

**Date estimée du changement :** décembre 2021

Defender pour le cloud inclut plusieurs recommandations permettant d’améliorer la gestion des utilisateurs et des comptes. En décembre, nous allons effectuer les changements décrits ci-dessous.

- **Intervalle d’actualisation amélioré** - Pour le moment, les recommandations sur l’identité ont un intervalle d’actualisation de 24 heures. Cette mise à jour va réduire l’intervalle à 12 heures.

- **Fonctionnalité d’exemption de compte** : Defender pour le cloud dispose de nombreuses fonctionnalités qui permettent de personnaliser l’expérience utilisateur et de vérifier que le niveau de sécurité reflète les priorités de sécurité de votre organisation. L’option d’exemption des recommandations de sécurité est l’une de ces fonctionnalités. Pour obtenir une vue d’ensemble complète et des instructions, consultez [Exemption de ressources et de recommandations dans votre niveau de sécurité](exempt-resource.md). Avec cette mise à jour, vous pouvez exempter d’évaluation des comptes spécifiques en fonction des huit recommandations listées dans le tableau suivant.

    En règle générale, vous exemptez les comptes « de secours » d’urgence des recommandations relatives à l’authentification MFA, car ces comptes sont souvent délibérément exclus des obligations d’authentification MFA d’une organisation. Vous pouvez également disposer de comptes externes auxquels vous souhaitez autoriser l’accès, mais pour lesquels l’authentification MFA n’est pas activée.

    > [!TIP]
    > Quand vous exemptez un compte, il n’est pas affiché comme étant non sain. Il n’indique pas non plus qu’un abonnement n’est pas sain.

    |Recommandation| Clé d’évaluation|
    |-|-|
    |[L’authentification multifacteur (MFA) doit être activée sur les comptes disposant d’autorisations de propriétaire sur votre abonnement](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/94290b00-4d0c-d7b4-7cea-064a9554e681)|94290b00-4d0c-d7b4-7cea-064a9554e681|
    |[L’authentification multifacteur (MFA) doit être activée sur les comptes disposant d’autorisations de lecture sur votre abonnement](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/151e82c5-5341-a74b-1eb0-bc38d2c84bb5)|151e82c5-5341-a74b-1eb0-bc38d2c84bb5|
    |[L’authentification multifacteur (MFA) doit être activée sur les comptes disposant d’autorisations d’écriture sur votre abonnement](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/57e98606-6b1e-6193-0e3d-fe621387c16b)|57e98606-6b1e-6193-0e3d-fe621387c16b|
    |[Les comptes externes disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/c3b6ae71-f1f0-31b4-e6c1-d5951285d03d)|c3b6ae71-f1f0-31b4-e6c1-d5951285d03d|
    |[Les comptes externes disposant d’autorisations de lecture doivent être supprimés de votre abonnement](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/a8c6a4ad-d51e-88fe-2979-d3ee3c864f8b)|a8c6a4ad-d51e-88fe-2979-d3ee3c864f8b|
    |[Les comptes externes disposant d’autorisations d’écriture doivent être supprimés de votre abonnement](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/04e7147b-0deb-9796-2e5c-0336343ceb3d)|04e7147b-0deb-9796-2e5c-0336343ceb3d|
    |[Les comptes déconseillés disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/e52064aa-6853-e252-a11e-dffc675689c2)|e52064aa-6853-e252-a11e-dffc675689c2|
    |[Les comptes déconseillés doivent être supprimés de votre abonnement](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/00c6d40b-e990-6acf-d4f3-471e747a27c4)|00c6d40b-e990-6acf-d4f3-471e747a27c4|
    |||
 
- **Renommage des recommandations** - À partir de cette mise à jour, nous renommons deux recommandations. Nous révisons également leurs descriptions. Les clés d’évaluation restent inchangées. 


    |Propriété  |Valeur actuelle  | À partir de la mise à jour|
    |---------|---------|---------|
    |Clé d’évaluation     | e52064aa-6853-e252-a11e-dffc675689c2        | Inchangé|
    |Name     |[Les comptes déconseillés disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/e52064aa-6853-e252-a11e-dffc675689c2)         |Les comptes bloqués dans Active Directory et qui disposent d’autorisations de propriétaire doivent être supprimés définitivement dans les abonnements        |
    |Description     |Les comptes d’utilisateur qui ne peuvent plus se connecter doivent être supprimés de vos abonnements.<br>Ces comptes peuvent être des cibles pour les attaquants qui cherchent des moyens d’accéder à vos données sans être remarqués.|Les comptes d’utilisateur dont la connexion à Active Directory a été bloquée doivent être supprimés de vos abonnements. Ces comptes peuvent être des cibles pour les attaquants qui cherchent des moyens d’accéder à vos données sans être remarqués.<br>Découvrez plus en détail la sécurisation du périmètre d’identité dans [Bonnes pratiques pour la sécurité du contrôle d’accès et la gestion des identités Azure](../security/fundamentals/identity-management-best-practices.md).|
    |Stratégie associée     |[Les comptes déconseillés disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2febb62a0c-3560-49e1-89ed-27e074e9f8ad)         |Les comptes bloqués dans Active Directory et qui disposent d’autorisations de propriétaire doivent être supprimés définitivement dans les abonnements |
    |||

    |Propriété  |Valeur actuelle  | À partir de la mise à jour|
    |---------|---------|---------|
    |Clé d’évaluation     | 00c6d40b-e990-6acf-d4f3-471e747a27c4        | Inchangé|
    |Name     |[Les comptes déconseillés doivent être supprimés de votre abonnement](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/00c6d40b-e990-6acf-d4f3-471e747a27c4)|Les comptes bloqués dans Active Directory et qui disposent d’autorisations en lecture et en écriture doivent être supprimés définitivement dans les abonnements|
    |Description     |Les comptes d’utilisateur qui ne peuvent plus se connecter doivent être supprimés de vos abonnements.<br>Ces comptes peuvent être des cibles pour les attaquants qui cherchent des moyens d’accéder à vos données sans être remarqués.|Les comptes d’utilisateur dont la connexion à Active Directory a été bloquée doivent être supprimés de vos abonnements. Ces comptes peuvent être des cibles pour les attaquants qui cherchent des moyens d’accéder à vos données sans être remarqués.<br>Découvrez plus en détail la sécurisation du périmètre d’identité dans [Bonnes pratiques pour la sécurité du contrôle d’accès et la gestion des identités Azure](../security/fundamentals/identity-management-best-practices.md).|
    |Stratégie associée     |[Les comptes déconseillés doivent être supprimés de votre abonnement](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6b1cbf55-e8b6-442f-ba4c-7246b6381474)|Les comptes bloqués dans Active Directory et qui disposent d’autorisations en lecture et en écriture doivent être supprimés définitivement dans les abonnements|
    |||
 

### <a name="changes-to-a-security-alert-from-microsoft-defender-for-storage"></a>Modifications apportées à une alerte de sécurité provenant de Microsoft Defender pour le stockage

**Date estimée du changement :** novembre 2021

L’une des alertes en préversion fournies par Microsoft Defender pour le stockage se divise en deux nouvelles recommandations pour offrir une plus grande clarté sur les événements suspects découverts. Cette alerte concerne uniquement Stockage Blob Azure.

**Le type d’alerte change également.**

- Avant la modification, l’alerte était la suivante :<br>
    « Préversion – Analyse anonyme des conteneurs de stockage public »<br>(Storage.Blob_ContainerAnonymousScan)

- À partir de ce changement, deux recommandations vont coexister :

    - « Conteneurs de stockage ouverts détectés par un script ou un outil d’analyse externe »<br>(Storage.Blob_OpenContainersScanning.FailedAttempt)
    - « Détection réussie des conteneurs de stockage ouverts par un script ou un outil d’analyse externe »<br>(Storage.Blob_OpenContainersScanning.SuccessfulDiscovery)

D’autres informations sur ces alertes seront publiés quand la modification sera diffusée.


### <a name="enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases"></a>Améliorations apportées à la recommandation pour classer les données sensibles dans les bases de données SQL

**Date estimée de la modification :** T1 2022

La recommandation **Les données sensibles de vos bases de données SQL doivent être classifiées** du contrôle de sécurité **Appliquer la classification des données** va être remplacée par une nouvelle version mieux alignée sur la stratégie de classification des données de Microsoft. L’ID de la recommandation sera donc également modifié (actuellement, il s’agit de b0df6f56-862d-4730-8597-38c0fd4ebd59).


### <a name="changes-to-recommendations-for-managing-endpoint-protection-solutions"></a>Modifications apportées aux suggestions pour la gestion des solutions de protection des points de terminaison

**Date estimée du changement :** février 2022

En août 2021, nous avons ajouté deux suggestions **en préversion** pour déployer et conserver les solutions de protection des points de terminaison sur vos machines. Pour plus d’informations, consultez [la note de publication](release-notes.md#two-new-recommendations-for-managing-endpoint-protection-solutions-in-preview).

Lorsque les suggestions sont publiées en disponibilité générale, elles remplacent les suggestions existantes suivantes :

- **La protection des points de terminaison doit être installée sur vos machines** remplacera :
    - [Installer la solution de protection des points de terminaison sur des machines virtuelles (clé : 83f577bd-a1b6-b7e1-0891-12ca19d1e6df)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/83f577bd-a1b6-b7e1-0891-12ca19d1e6df)
    - [Installer la solution de protection des points de terminaison sur vos machines (clé : 383cf3bc-fdf9-4a02-120a-3e7e36c6bfee)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/383cf3bc-fdf9-4a02-120a-3e7e36c6bfee)

- **Les problèmes d’intégrité de la protection des points de terminaison doivent être résolus sur vos ordinateurs** remplacera la recommandation existante qui porte le même nom. Les deux suggestions ont des clés d’évaluation différentes :
    - Clé d’évaluation pour la recommandation de la **préversion** : 37a3689a-818e-4a0e-82ac-b1392b9bb000
    - Clé d’évaluation pour la recommandation **GA** : 3bcd234d-c9c7-c2a2-89e0-c01f419c1a8a

En savoir plus :
- [Solutions de protection des points de terminaison prises en charge par Defender pour le cloud](supported-machines-endpoint-solutions-clouds.md#endpoint-supported)
- [Comment ces suggestions évaluent l’état de vos solutions déployées](endpoint-protection-recommendations-technical.md)



## <a name="next-steps"></a>Étapes suivantes

Pour toutes les modifications récentes apportées à Defender pour le cloud, consultez [Nouveautés de Microsoft Defender pour le cloud](release-notes.md).
