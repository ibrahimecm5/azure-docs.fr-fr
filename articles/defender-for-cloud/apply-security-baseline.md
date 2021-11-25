---
title: Renforcer votre système d’exploitation Windows et Linux avec la base de référence de sécurité Azure et Microsoft Defender pour le cloud
description: Découvrez comment Microsoft Defender pour le cloud utilise la configuration Invité pour comparer le renforcement de votre système d’exploitation avec les conseils du Benchmark de sécurité Azure.
author: memildin
manager: rkarlin
ms.service: defender-for-cloud
ms.topic: how-to
ms.date: 11/09/2021
ms.author: memildin
ms.openlocfilehash: 3ac0b2745ac7abe91bc9f1a122e98842ce0e4f0e
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132526409"
---
# <a name="apply-azure-security-baselines-to-machines"></a>Appliquer des bases de référence de sécurité Azure sur des ordinateurs

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Pour réduire la surface d’attaque d’un ordinateur et éviter les risques connus, il est important de configurer le système d’exploitation de manière aussi sécurisée que possible.

Le Benchmark de sécurité Azure contient des conseils pour renforcer le système d’exploitation, ce qui a permis de générer des documents de base sur la sécurité pour [Windows](../governance/policy/samples/guest-configuration-baseline-windows.md) et [Linux](../governance/policy/samples/guest-configuration-baseline-linux.md).

Les recommandations de sécurité décrites dans cet article permettent d’évaluer les ordinateurs de votre environnement et d’effectuer les opérations suivantes :

- Identifier les lacunes dans les configurations de sécurité
- Découvrir la procédure de correction de ces lacunes

## <a name="availability"></a>Disponibilité
|Aspect|Détails|
|----|:----|
|État de sortie :|Préversion.<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]|
|Prix :|Gratuit|
|Configuration requise :|Les ordinateurs doivent (1) être membres d’un groupe de travail, (2) avoir l’extension Configuration Invité, (3) avoir une identité managée affectée par le système et (4) exécuter un système d’exploitation pris en charge :<br>• Windows Server 2012, 2012r2, 2016 ou 2019<br>• Ubuntu 14.04, 16.04, 17.04, 18.04 ou 20.04<br>• Debian 7, 8, 9 ou 10<br>• CentOS 7 ou 8<br>• Red Hat Enterprise Linux (RHEL) 7 ou 8<br>• Oracle Linux 7 ou 8<br>• SUSE Linux Enterprise Server 12|
|Rôles et autorisations obligatoires :|Pour installer l’extension Configuration Invité et sa configuration requise, l’autorisation d’accès en **écriture** est obligatoire sur les ordinateurs concernés.<br>Pour **afficher** les recommandations et explorer les données de base du système d’exploitation, l’autorisation d’accès en **lecture** est obligatoire au niveau de l’abonnement.|
|Clouds :|:::image type="icon" source="./media/icons/yes-icon.png"::: Clouds commerciaux<br>:::image type="icon" source="./media/icons/no-icon.png"::: National (Azure Government, Azure China 21Vianet)|
|||

## <a name="what-are-the-hardening-recommendations"></a>Quelles sont les recommandations relatives au renforcement de la sécurité ?

Microsoft Defender pour le cloud comprend deux recommandations qui vérifient si la configuration des ordinateurs Windows et Linux dans votre environnement est conforme aux configurations de la ligne de base de sécurité Azure :

- Pour les ordinateurs **Windows**, [Vulnérabilités dans la configuration de la sécurité sur vos machines Windows doivent être corrigées (avec Configuration Invité)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/1f655fb7-63ca-4980-91a3-56dbc2b715c6) compare la configuration avec la [ligne de base de la sécurité Windows](../governance/policy/samples/guest-configuration-baseline-windows.md).
- Pour les ordinateurs **Linux**, [Les vulnérabilités dans la configuration de la sécurité sur vos machines Linux doivent être corrigées (avec Configuration Invité)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/8c3d9ad0-3639-4686-9cd2-2b2ab2609bda) compare la configuration avec la [ligne de base de la sécurité Linux](../governance/policy/samples/guest-configuration-baseline-linux.md).

Ces recommandations utilisent la fonctionnalité de configuration d’invité d’Azure Policy pour comparer la configuration du système d’exploitation d’un ordinateur à la ligne de base définie dans le [Benchmark de sécurité Azure](/security/benchmark/azure/overview).

## <a name="compare-machines-in-your-subscriptions-with-the-os-security-baselines"></a>Comparer les ordinateurs de vos abonnements avec les lignes de base de la sécurité du système d’exploitation

Pour comparer des ordinateurs avec les lignes de base de la sécurité du système d’exploitation :
 
1. À partir des pages du portail de Defender pour le cloud, ouvrez la page **Recommandations**. 
1. Sélectionnez la recommandation appropriée :
    - Pour les ordinateurs **Windows**, [Les vulnérabilités dans la configuration de la sécurité sur vos machines Windows doivent être corrigées (avec Configuration Invité)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/1f655fb7-63ca-4980-91a3-56dbc2b715c6)
    - Pour les ordinateurs **Linux**, [Les vulnérabilités dans la configuration de la sécurité sur vos ordinateurs Linux doivent être corrigées (avec Configuration Invité)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/8c3d9ad0-3639-4686-9cd2-2b2ab2609bda)

    :::image type="content" source="media/apply-security-baseline/recommendations-baseline.png" alt-text="Les deux recommandations pour comparer la configuration du système d’exploitation des machines avec la ligne de base de sécurité Azure appropriée." lightbox="media/apply-security-baseline/recommendations-baseline.png":::

1. Sur la page des détails des recommandations, vous pouvez voir ce qui suit :
    1. Ressources concernées.
    1. Vérifications de sécurité spécifiques ayant échoué.

    :::image type="content" source="media/apply-security-baseline/recommendation-details-page-vulnerabilities-windows.png" alt-text="Page des détails des recommandations pour la recommandation Windows sur les vulnérabilités dans la configuration de base des ordinateurs Windows." lightbox="media/apply-security-baseline/recommendation-details-page-vulnerabilities-windows.png":::

1. Pour en savoir plus sur un résultat, sélectionnez-le.

    :::image type="content" source="media/apply-security-baseline/finding-details.png" alt-text="En savoir plus sur une recherche spécifique de la comparaison de configuration Invité d’une configuration de système d’exploitation avec la ligne de base de sécurité définie." lightbox="media/apply-security-baseline/finding-details.png":::

1. Autres possibilités d’investigation :

    - Pour afficher la liste des ordinateurs qui ont été évalués, ouvrez **Ressources affectées**.
    - Pour afficher la liste des résultats d’un ordinateur, sélectionnez un ordinateur sous l’onglet **Ressources non saines**. Une page s’ouvre et ne répertorie que les résultats de cette machine.


## <a name="faq---hardening-an-os-according-to-the-security-baseline"></a>FAQ - Sécurisation renforcée d’un système d’exploitation en fonction de la ligne de base de sécurité

- [Comment déployer la configuration requise pour les recommandations de configuration de la sécurité ?](#how-do-i-deploy-the-prerequisites-for-the-security-configuration-recommendations)
- [Pourquoi un ordinateur est-il indiqué comme non applicable ?](#why-is-a-machine-shown-as-not-applicable)

### <a name="how-do-i-deploy-the-prerequisites-for-the-security-configuration-recommendations"></a>Comment déployer la configuration requise pour les recommandations de configuration de la sécurité ?

Pour déployer l’extension Configuration Invité avec les composants requis suivants :

- Pour les ordinateurs sélectionnés, suivez les recommandations de sécurité **L’extension Configuration Invité doit être installée sur vos ordinateurs** du contrôle de sécurité **Implémenter les bonnes pratiques de sécurité**.

- À grande échelle, attribuez l’initiative de stratégie **Déployer les prérequis pour activer les stratégies Configuration Invité sur les machines virtuelles**.


### <a name="why-is-a-machine-shown-as-not-applicable"></a>Pourquoi un ordinateur est-il indiqué comme non applicable ?

La liste des ressources sous l’onglet **Non applicable** contient une colonne **Motif**. Voici quelques-unes des raisons les plus courantes :

| Motif                                                            | Détails                                                                                                                                                                        |
|-------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Aucune donnée d’analyse disponible sur l’ordinateur**                         | Il n’existe aucun résultat de conformité pour cet ordinateur dans Azure Resource Graph. Tous les résultats de conformité sont écrits dans Azure resource Graph par l’extension de Configuration Invité. Vous pouvez vérifier les données Azure Resource Graph en utilisant les exemples de requête figurant dans [Configuration Invité Azure Policy - Exemples de requête ARG](../governance/policy/samples/resource-graph-samples.md?tabs=azure-cli#azure-policy-guest-configuration).|
| **L’extension Configuration Invité n’est pas installée sur l’ordinateur** | L’ordinateur ne dispose pas de l’extension Configuration Invité, ce qui est une condition préalable à l’évaluation de la conformité avec la ligne de base de sécurité Azure.                               |
| **L’identité managée par le système n’est pas configurée sur l’ordinateur**      | Une identité managée affectée par le système doit être déployée sur l’ordinateur.                                                                                                           |
| **La recommandation est désactivée dans la stratégie**                      | La définition de stratégie qui évalue la ligne de base du système d’exploitation est désactivée sur l’étendue qui comprend l’ordinateur concerné.                                                               |
|                                                                   |                                                                                                                                                                                |

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à utiliser les recommandations de configuration invité de Defender pour le cloud afin de comparer le renforcement de votre système d’exploitation avec la base de référence de sécurité Azure.

Pour en savoir plus sur ces paramètres de configuration, consultez :

- [Base de référence de sécurité Windows](../governance/policy/samples/guest-configuration-baseline-windows.md)
- [Base de référence de sécurité Linux](../governance/policy/samples/guest-configuration-baseline-linux.md)
- [Benchmark de sécurité Azure](/security/benchmark/azure/overview)
