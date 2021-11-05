---
title: 'Tutoriel : Vérifications de conformité réglementaire - Microsoft Defender pour le cloud'
description: 'Tutoriel : Découvrez comment améliorer votre conformité réglementaire à l’aide de Microsoft Defender pour le cloud.'
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: tutorial
ms.date: 08/09/2021
ms.author: memildin
ms.openlocfilehash: 6051f0625408345b0e8665bb3ddcccf97e940367
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131452848"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Tutoriel : Améliorer votre conformité aux normes

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Le **tableau de bord de conformité réglementaire** de Microsoft Defender pour le cloud simplifie le processus visant à respecter les exigences de conformité réglementaire. 

Defender pour le cloud évalue continuellement votre environnement cloud hybride afin d’analyser les facteurs de risque en fonction des contrôles et des bonnes pratiques du point de vue des normes appliqués à vos abonnements. Le tableau de bord reflète l’état de votre conformité à ces normes. 

Quand vous activez Defender pour le cloud dans un abonnement Azure, le [Benchmark de sécurité Azure](/security/benchmark/azure/introduction) lui est automatiquement attribué. Ce benchmark, largement respecté et centré sur le cloud, est basé sur les contrôles du [CIS (Center for Internet Security)](https://www.cisecurity.org/benchmark/azure/) et du [NIST (National Institute of Standards and Technology)](https://www.nist.gov/).

Le tableau de bord de conformité réglementaire montre l’état de toutes les évaluations au sein de votre environnement pour les normes et réglementations de votre choix. Si vous suivez les recommandations et réduisez les facteurs de risque de votre environnement, votre niveau de conformité s’améliore.

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Évaluer votre conformité aux réglementations à l’aide du tableau de bord de conformité réglementaire
> * Améliorer votre niveau de conformité en suivant les recommandations
> * Télécharger les rapports PDF/CSV et les rapports de certification de votre état de conformité
> * Configurer des alertes en cas de modification de votre état de conformité
> * Exporter vos données de conformité en tant que flux continu et en tant qu’instantanés hebdomadaires

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour parcourir les fonctionnalités couvertes dans ce tutoriel :

- [Activez les fonctionnalités de sécurité renforcée](defender-for-cloud-introduction.md). Vous pouvez les activer gratuitement pendant 30 jours.
- Vous devez être connecté avec un compte qui dispose d’un accès de lecteur aux données de conformité de stratégie (**Lecteur Sécurité** est insuffisant). Le rôle **Lecteur général** pour l’abonnement fonctionnera. Au minimum, les rôles **Contributeur de stratégie de ressource** et **Administration de sécurité** doivent vous être affectés.

## <a name="assess-your-regulatory-compliance"></a>Évaluer votre conformité aux réglementations

Le tableau de bord de conformité réglementaire montre vos normes de conformité sélectionnées et toutes leurs exigences, celles prises en charge étant comparées aux évaluations de sécurité applicables. L’état de ces évaluations reflète votre conformité à la norme.

Utilisez le tableau de bord de conformité réglementaire pour vous aider à prendre conscience d’un manque de conformité aux normes et aux réglementations de votre choix. Cette vue ciblée vous permet également de superviser votre conformité au fil du temps dans les environnements cloud et hybrides dynamiques.

1. Dans le menu de Defender pour le cloud, sélectionnez **Conformité réglementaire**.

    Un tableau de bord apparaît en haut de l’écran avec une vue d’ensemble de votre état de conformité et l’ensemble des réglementations de conformité prises en charge. Vous verrez votre score de conformité global et le nombre d’évaluations ayant réussi ou échoué pour chaque norme.

    :::image type="content" source="./media/regulatory-compliance-dashboard/compliance-dashboard.png" alt-text="Tableau de bord de conformité réglementaire." lightbox="./media/regulatory-compliance-dashboard/compliance-dashboard.png":::

1. Sélectionnez un onglet correspondant à une norme de conformité qui vous intéresse (1). Vous verrez sur quels abonnements la norme est appliquée (2) et la liste de tous les contrôles relatifs à cette norme (3). Pour les contrôles applicables, vous pouvez voir les détails des évaluations ayant réussi ou échoué associées à ce contrôle (4) et le nombre de ressources affectées (5). Certains contrôles sont grisés. Ces contrôles ne sont associés à aucune évaluation Defender pour le cloud. Vérifiez les conditions qui leur sont associées et évaluez-les dans votre environnement. Certaines d’entre elles peuvent être liés au processus et ne pas être d’ordre technique.

    :::image type="content" source="./media/regulatory-compliance-dashboard/compliance-drilldown.png" alt-text="Exploration des détails de la conformité à une norme spécifique.":::

## <a name="improve-your-compliance-posture"></a>Améliorer votre niveau de conformité

À l’aide des informations figurant dans le tableau de bord de conformité réglementaire, améliorez votre niveau de conformité en résolvant les recommandations directement dans le tableau de bord.

1.  Sélectionnez l’une des évaluations ayant échoué dans le tableau de bord pour voir les détails de cette recommandation. Chaque recommandation comprend un ensemble d’étapes de correction pour résoudre le problème.

1.  Sélectionnez une ressource particulière pour voir plus de détails et résoudre la recommandation associée à cette ressource. <br>Par exemple, dans la **norme Azure CIS 1.1.0**, sélectionnez la recommandation **Le chiffrement de disque doit être appliqué sur les machines virtuelles**.

    :::image type="content" source="./media/regulatory-compliance-dashboard/sample-recommendation.png" alt-text="La sélection d’une recommandation concernant une norme vous amène directement à la page des détails de la recommandation.":::

1. Dans cet exemple, quand vous sélectionnez **Entreprendre une action** dans la page des détails de la recommandation, vous arrivez dans les pages relatives aux machines virtuelles Azure du portail Azure, où vous pouvez activer le chiffrement sous l’onglet **Sécurité** :

    :::image type="content" source="./media/regulatory-compliance-dashboard/encrypting-vm-disks.png" alt-text="Le bouton Entreprendre une action de la page des détails de la recommandation vous amène aux options de correction.":::

    Pour plus d’informations sur la manière d’appliquer des recommandations, consultez [Implémentation des recommandations de sécurité dans Microsoft Defender pour le cloud](review-security-recommendations.md).

1.  Quand vous aurez pris des mesures pour appliquer les recommandations, vous constaterez une amélioration de votre score de conformité dans le rapport du tableau de bord de conformité.

    > [!NOTE]
    > Les évaluations s’exécutent toutes les 12 heures environ. L’impact sur vos données de conformité n’est donc visible qu’après l’exécution suivante de l’évaluation correspondante.

## <a name="generate-compliance-status-reports-and-certificates"></a>Générer des rapports d’état de conformité et des certificats

- Pour générer un rapport PDF comportant un résumé de votre état de compatibilité actuel pour une norme particulière, sélectionnez **Télécharger un rapport**.

    Le rapport fournit un résumé général de votre état de conformité pour la norme sélectionnée en fonction des données d’évaluation Defender pour le cloud. Le rapport est organisé en fonction des contrôles de cette norme particulière. Le rapport peut être partagé avec les parties prenantes concernées et servir de preuve aux auditeurs internes et externes.

    :::image type="content" source="./media/regulatory-compliance-dashboard/download-report.png" alt-text="Utilisation de la barre d’outils du tableau de bord de conformité réglementaire de Defender pour le cloud pour télécharger les rapports de conformité.":::

- Pour télécharger des **rapports de certification** Azure et Dynamics pour les normes appliquées à vos abonnements, utilisez l’option **Rapports d’audit**. 

    :::image type="content" source="media/release-notes/audit-reports-regulatory-compliance-dashboard.png" alt-text="Utilisation de la barre d’outils du tableau de bord de conformité réglementaire de Defender pour le cloud pour télécharger les rapports de certification Azure et Dynamics.":::

    Sélectionnez l’onglet pour les types de rapports appropriés (PCI, SOC, ISO et autres) et utiliser des filtres pour rechercher les rapports spécifiques dont vous avez besoin :

    :::image type="content" source="media/release-notes/audit-reports-list-regulatory-compliance-dashboard-ga.png" alt-text="Filtrage de la liste des rapports d’audit Azure disponibles en utilisant des onglets et des filtres.":::

    Par exemple, à partir de l’onglet PCI, vous pouvez télécharger un fichier ZIP contenant un certificat signé numériquement, qui démontre la conformité de Microsoft Azure, Dynamics 365 et d’autres services en ligne au framework ISO22301, ainsi que la documentation nécessaire pour interpréter et présenter le certificat. 

    > [!NOTE]
    > Lorsque vous téléchargez l’un de ces rapports de certification, vous verrez apparaître l’avis de confidentialité suivant :
    > 
    > _En téléchargeant ce fichier, vous autorisez Microsoft à enregistrer l’utilisateur actuel et les abonnements sélectionnés au moment du téléchargement. Ces données sont utilisées pour vous informer en cas de modifications ou de mises à jour du rapport d’audit téléchargé. Ces données sont utilisées par Microsoft et les cabinets d’audit qui produisent la certification._

## <a name="configure-frequent-exports-of-your-compliance-status-data"></a>Configurer des exportations fréquentes de vos données d’état de conformité

Si vous souhaitez suivre votre état de conformité avec d’autres outils de monitoring dans votre environnement, Defender pour le cloud comprend un mécanisme d’exportation pour faciliter cette tâche. Configurez l’**exportation continue** pour envoyer des données spécifiques à un hub d’événements Azure ou à un espace de travail Log Analytics. Apprenez-en davantage dans [Exportation continue des données Defender pour le cloud](continuous-export.md).

Utilisez les données d’exportation continue envoyées à un hub d’événements Azure ou un espace de travail Log Analytics :

- Exportez toutes les données de conformité réglementaire dans un **flux continu** :

    :::image type="content" source="media/regulatory-compliance-dashboard/export-compliance-data-stream.png" alt-text="Exporter en continu un flux de données de conformité réglementaire." lightbox="media/regulatory-compliance-dashboard/export-compliance-data-stream.png":::

- Exportez des **instantanés hebdomadaires** de vos données de conformité réglementaire :

    :::image type="content" source="media/regulatory-compliance-dashboard/export-compliance-data-snapshot.png" alt-text="Exporter en continu un instantané hebdomadaire des données de conformité réglementaire." lightbox="media/regulatory-compliance-dashboard/export-compliance-data-snapshot.png":::

> [!TIP]
> Vous pouvez également exporter manuellement des rapports à partir d’un point unique dans le temps directement à partir du tableau de bord de conformité réglementaire. Générez ces **rapports PDF / CSV** ou **rapports de certification Azure et Dynamics** avec les options de barre d’outils **Télécharger le rapport**  ou **Rapports d’audit** Options de la barre d’outils. Consultez [Évaluer votre conformité aux réglementations](#assess-your-regulatory-compliance) 


## <a name="run-workflow-automations-when-there-are-changes-to-your-compliance"></a>Exécuter des automatisations de workflow en cas de modification de votre conformité

La fonctionnalité d’automatisation de workflow de Defender pour le cloud peut déclencher des applications logiques chaque fois que l’une de vos évaluations de conformité réglementaire change d’état.

Par exemple, vous pouvez faire en sorte que Defender pour le cloud envoie un e-mail à un utilisateur donné en cas d’échec d’une évaluation de conformité. Vous devez d’abord créer l’application logique (à l’aide d’[Azure Logic Apps](../logic-apps/logic-apps-overview.md)), puis configurer le déclencheur dans une nouvelle automatisation de workflow, comme expliqué dans [Automatiser les réponses aux déclencheurs Defender pour le cloud](workflow-automation.md).

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Utilisation des modifications apportées aux évaluations de conformité réglementaire pour déclencher une automatisation de workflow." lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::




## <a name="faq---regulatory-compliance-dashboard"></a>Questions fréquentes (FAQ) – Tableau de bord de conformité réglementaire

- [Quelles sont les normes prises en charge dans le tableau de bord de conformité ?](#what-standards-are-supported-in-the-compliance-dashboard)
- [Pourquoi certains contrôles sont-ils grisés ?](#why-do-some-controls-appear-grayed-out)
- [Comment supprimer une norme intégrée, telle que PCI-DSS, ISO 27001 ou SOC2 TSP du tableau de bord ?](#how-can-i-remove-a-built-in-standard-like-pci-dss-iso-27001-or-soc2-tsp-from-the-dashboard)
- [J’ai apporté la modification suggérée par la recommandation, mais elle n’apparaît pas encore dans le tableau de bord](#i-made-the-suggested-changed-based-on-the-recommendation-yet-it-isnt-being-reflected-in-the-dashboard)
- [De quelles autorisations ai-je besoin pour accéder au tableau de bord de conformité ?](#what-permissions-do-i-need-to-access-the-compliance-dashboard)
- [Le tableau de bord de conformité réglementaire ne se charge pas pour moi](#the-regulatory-compliance-dashboard-isnt-loading-for-me)
- [Comment afficher un rapport sur les contrôles ayant réussi ou échoué par norme dans mon tableau de bord ?](#how-can-i-view-a-report-of-passing-and-failing-controls-per-standard-in-my-dashboard)
- [Comment télécharger un rapport avec des données de conformité dans un format autre que PDF ?](#how-can-i-download-a-report-with-compliance-data-in-a-format-other-than-pdf)
- [Comment créer des exceptions pour certaines des stratégies présentes dans le tableau de bord de conformité réglementaire ?](#how-can-i-create-exceptions-for-some-of-the-policies-in-the-regulatory-compliance-dashboard)
- [De quels plans ou licences Microsoft Defender pour le cloud ai-je besoin pour utiliser le tableau de bord de conformité réglementaire ?](#what-microsoft-defender-plans-or-licenses-do-i-need-to-use-the-regulatory-compliance-dashboard)
- [Comment déterminer le benchmark ou la norme à utiliser ?](#how-do-i-know-which-benchmark-or-standard-to-use)

### <a name="what-standards-are-supported-in-the-compliance-dashboard"></a>Quelles sont les normes prises en charge dans le tableau de bord de conformité ?
Par défaut, le tableau de bord conformité réglementaire vous montre le benchmark de sécurité Azure. Le benchmark de sécurité Azure rassemble les directives de sécurité propres à Azure et créées par Microsoft et les bonnes pratiques de conformité s’inscrivant dans les cadres de conformité courants. Apprenez-en davantage dans [Introduction aux benchmarks de sécurité Azure](../security/benchmarks/introduction.md).

Pour suivre votre conformité à d’autres normes, vous devez les ajouter explicitement à votre tableau de bord.
 
Vous pouvez ajouter d’autres normes comme : Azure CIS 1.3.0, NIST SP 800-53, NIST SP 800-171, SWIFT CSP CSCF-v2020, UK Official et UK NHS, HIPAA, Canada Federal PBMM, ISO 27001, SOC2-TSP et PCI-DSS 3.2.1.  

D’autres normes seront ajoutées au tableau de bord et incluses dans les informations fournies dans [Personnaliser l’ensemble de normes du tableau de bord de conformité réglementaire](update-regulatory-compliance-packages.md).

### <a name="why-do-some-controls-appear-grayed-out"></a>Pourquoi certains contrôles sont-ils grisés ?
Pour chaque norme de conformité présente dans le tableau de bord, il existe une liste de contrôles. Pour les contrôles applicables, vous pouvez voir les détails des évaluations ayant réussi ou échoué.

Certains contrôles sont grisés. Ces contrôles ne sont associés à aucune évaluation Defender pour le cloud. Certains peuvent être liés à une procédure ou à un processus, ce qui explique qu’ils ne peuvent pas être vérifiés par Defender pour le cloud. Certaines stratégies ou évaluations automatisées n’ont pas encore été implémentées, mais le seront dans l’avenir. Certains contrôles peuvent relever de la responsabilité de la plateforme, comme expliqué dans [Responsabilité partagée dans le cloud](../security/fundamentals/shared-responsibility.md). 

### <a name="how-can-i-remove-a-built-in-standard-like-pci-dss-iso-27001-or-soc2-tsp-from-the-dashboard"></a>Comment supprimer une norme intégrée, telle que PCI-DSS, ISO 27001 ou SOC2 TSP, du tableau de bord ? 
Pour personnaliser le tableau de bord de conformité réglementaire et vous concentrer uniquement sur les normes qui vous sont applicables, vous pouvez supprimer les normes réglementaires affichées qui ne concernent pas votre organisation. Pour supprimer une norme, suivez les instructions dans [Supprimer une norme de votre tableau de bord](update-regulatory-compliance-packages.md#remove-a-standard-from-your-dashboard).

### <a name="i-made-the-suggested-changed-based-on-the-recommendation-yet-it-isnt-being-reflected-in-the-dashboard"></a>J’ai apporté la modification suggérée par la recommandation, mais elle n’apparaît pas encore dans le tableau de bord
Après avoir pris les mesures pour appliquer les recommandations, patientez 12 heures avant de voir les modifications apparaître dans vos données de conformité. Les évaluations sont exécutées toutes les 12 heures environ. L’effet sur vos données de conformité n’est donc visible qu’après leur exécution.
 
### <a name="what-permissions-do-i-need-to-access-the-compliance-dashboard"></a>De quelles autorisations ai-je besoin pour accéder au tableau de bord de conformité ?
Pour afficher les données de conformité, vous devez disposer d’au moins un accès **Lecteur** sur les données de conformité de stratégie. L’accès Lecteur Sécurité seul ne suffit donc pas. Si vous êtes lecteur global dans l’abonnement, cela suffit.

L’ensemble minimal de rôles pour accéder au tableau de bord et gérer les normes est **Contributeur de stratégie de ressource** et **Administrateur de sécurité**.


### <a name="the-regulatory-compliance-dashboard-isnt-loading-for-me"></a>Le tableau de bord de conformité réglementaire ne se charge pas pour moi
Pour utiliser le tableau de bord de conformité réglementaire, Microsoft Defender pour le cloud doit avoir Defender activé au niveau de l’abonnement. Si le tableau de bord ne se charge pas correctement, essayez les étapes suivantes :

1. Effacez le cache de votre navigateur.
1. Essayez un autre navigateur.
1. Essayez d’ouvrir le tableau de bord à partir d’un autre emplacement réseau.


### <a name="how-can-i-view-a-report-of-passing-and-failing-controls-per-standard-in-my-dashboard"></a>Comment afficher un rapport sur les contrôles ayant réussi ou échoué par norme dans mon tableau de bord ?
Dans le tableau de bord principal, vous pouvez voir un rapport sur les contrôles ayant réussi ou échoué pour (1) les quatre premières normes ayant la conformité la plus faible dans le tableau de bord. Pour afficher l’état de tous les contrôles ayant réussi ou échoué, sélectionnez (2) **Afficher les *x*** (x représentant le nombre de normes que vous suivez). Un plan de contexte affiche l’état de conformité pour chacune des normes que vous suivez.

:::image type="content" source="media/regulatory-compliance-dashboard/summaries-of-compliance-standards.png" alt-text="Section Résumé du tableau de bord de conformité réglementaire.":::


### <a name="how-can-i-download-a-report-with-compliance-data-in-a-format-other-than-pdf"></a>Comment télécharger un rapport avec des données de conformité dans un format autre que PDF ?
Quand vous sélectionnez **Télécharger le rapport**, sélectionnez la norme et le format (PDF ou CSV). Le rapport obtenu reflète l’ensemble actuel d’abonnements que vous avez sélectionnés dans le filtre du portail.

- Le rapport PDF affiche un résumé de l’état de la norme sélectionnée
- Le rapport CSV fournit des résultats détaillés par ressource, par rapport aux stratégies associées à chaque contrôle

Pour l’heure, il n’est pas possible de télécharger un rapport pour une stratégie personnalisée (seules les normes réglementaires fournies sont prises en charge).


### <a name="how-can-i-create-exceptions-for-some-of-the-policies-in-the-regulatory-compliance-dashboard"></a>Comment créer des exceptions pour certaines des stratégies présentes dans le tableau de bord de conformité réglementaire ?
Pour les stratégies intégrées à Defender pour le cloud et incluses dans le degré de sécurisation, vous pouvez créer des exemptions pour une ou plusieurs ressources directement dans le portail, comme expliqué dans [Exempter une ressource des recommandations et du degré de sécurisation](exempt-resource.md).

Pour les autres stratégies, vous pouvez créer une exemption directement dans la stratégie elle-même, en suivant les instructions fournies dans [Structure d’exemption Azure Policy](../governance/policy/concepts/exemption-structure.md).


### <a name="what-microsoft-defender-plans-or-licenses-do-i-need-to-use-the-regulatory-compliance-dashboard"></a>De quels plans ou licences Microsoft Defender pour le cloud ai-je besoin pour utiliser le tableau de bord de conformité réglementaire ?
Si vous avez activé *n’importe lequel* des plans Microsoft Defender sur *n’importe laquelle* de vos ressources Azure, vous pouvez accéder au tableau de bord de conformité réglementaire de Defender pour le cloud et à toutes ses données.


### <a name="how-do-i-know-which-benchmark-or-standard-to-use"></a>Comment déterminer le benchmark ou la norme à utiliser ?
Le [benchmark de sécurité Azure](/security/benchmark/azure/introduction) (ASB) est l’ensemble canonique des recommandations de sécurité et des bonnes pratiques définies par Microsoft, alignées sur les frameworks de contrôle de conformité courants, notamment [CIS Microsoft Azure Foundations Benchmark](https://www.cisecurity.org/benchmark/azure/) et [NIST SP 800-53](https://csrc.nist.gov/publications/detail/sp/800-53/rev-5/final). ASB est un benchmark complet conçu pour recommander les fonctionnalités de sécurité les plus récentes d’un grand nombre de services Azure. Nous recommandons ASB aux clients qui veulent maximiser leur posture de sécurité et aligner leur état de conformité sur les normes du secteur.

Le [benchmark CIS](https://www.cisecurity.org/benchmark/azure/) est créé par une entité indépendante, le CIS (Center for Internet Security), et contient des recommandations sur une partie des principaux services Azure. Nous travaillons en collaboration avec le CIS pour garantir que leurs recommandations comprennent les dernières améliorations d’Azure, mais il arrive qu’elles prennent du retard et deviennent obsolètes. Néanmoins, certains clients aiment utiliser cette évaluation tierce objective du CIS comme première et principale base de référence de sécurité.

Depuis que nous avons publié le benchmark de sécurité Azure, de nombreux clients ont choisi de l’utiliser en remplacement des benchmarks CIS.


## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à utiliser le tableau de bord de conformité réglementaire de Defender pour le cloud pour effectuer les tâches suivantes :

> [!div class="checklist"]
> * Afficher et superviser votre niveau de conformité par rapport à des normes et à des réglementations qui sont importantes pour vous.
> * Améliorer votre état de conformité en suivant les recommandations appropriées et en augmentant le score de conformité.

Le tableau de bord de conformité réglementaire peut grandement simplifier le processus de conformité et réduire considérablement le temps nécessaire à la collecte des preuves de conformité pour votre environnement Azure, hybride et multicloud.

Pour en savoir plus, consultez les pages connexes suivantes :

- [Personnaliser l’ensemble de normes dans votre tableau de bord de conformité réglementaire](update-regulatory-compliance-packages.md) : découvrez comment sélectionner les normes affichées dans votre tableau de bord de conformité réglementaire. 
- [Gestion des recommandations de sécurité dans Defender pour le cloud](review-security-recommendations.md) – Découvrez comment utiliser les recommandations proposées par Defender pour le cloud pour protéger vos ressources Azure.
