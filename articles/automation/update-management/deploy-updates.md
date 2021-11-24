---
title: Guide pratique pour créer des déploiements de mises à jour pour Azure Automation Update Management
description: Cet article explique comment planifier des déploiements de mises à jour et vérifier leur état.
services: automation
ms.subservice: update-management
ms.date: 11/05/2021
ms.topic: conceptual
ms.openlocfilehash: 6830c06de1fc687f8c408d438086f6c08550d9f0
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551403"
---
# <a name="how-to-deploy-updates-and-review-results"></a>Guide pratique pour déployer des mises à jour et voir les résultats

Cet article explique comment planifier un déploiement de mises à jour et voir le processus après le déploiement. Vous pouvez configurer un déploiement de mise à jour à partir d’une machine virtuelle Azure sélectionnée, du serveur avec Azure Arc sélectionné ou du compte Automation pour l’ensemble des ordinateurs et serveurs configurés.

Dans chaque scénario, le déploiement que vous créez cible l’ordinateur ou le serveur sélectionné, ou, dans le cas de la création d’un déploiement à partir de votre compte Automation, vous pouvez cibler un ou plusieurs ordinateurs. Lorsque vous planifiez un déploiement de mises à jour à partir d’une machine virtuelle Azure ou d’un serveur avec Azure Arc, les étapes sont les mêmes que pour le déploiement à partir de votre compte Automation, avec les exceptions suivantes :

* Le système d’exploitation est automatiquement présélectionné en fonction du système d’exploitation de l’ordinateur.
* L’ordinateur cible à mettre à jour se définit automatiquement.
* Lors de la configuration de la planification, vous pouvez spécifier **Mettre à jour maintenant**, Se produit une fois ou Utilise une planification récurrente.

> [!IMPORTANT]
> En créant un déploiement de mise à jour, vous acceptez les termes du contrat de licence logiciel (CLUF) fournis par la société qui offre des mises à jour pour son système d’exploitation.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com)

## <a name="schedule-an-update-deployment"></a>Planifier un déploiement de mises à jour

La planification d’un déploiement de mises à jour entraîne la création d’une ressource de [planification](../shared-resources/schedules.md) liée au runbook **Patch-MicrosoftOMSComputers**, qui gère le déploiement des mises à jour sur les ordinateurs cibles. Vous devez planifier un déploiement qui suit votre fenêtre de maintenance et de planification de la mise en production pour installer les mises à jour. Vous pouvez choisir les types de mises à jour à inclure dans le déploiement. Par exemple, vous pouvez inclure des mises à jour critiques ou des correctifs de sécurité et exclure des correctifs cumulatifs.

>[!NOTE]
>Si vous supprimez la ressource de planification dans le Portail Azure ou si vous utilisez PowerShell après la création du déploiement, l’opération de suppression arrête le déploiement de mises à jour planifié. De plus, une erreur est générée quand vous essayez de reconfigurer la ressource de planification à partir du Portail. Vous ne pouvez supprimer la ressource de planification qu’en supprimant la planification de déploiement correspondante.  

Pour planifier un nouveau déploiement de mises à jour, procédez comme suit. En fonction de la ressource sélectionnée (autrement dit, compte Automation, serveur avec Azure Arc ou machine virtuelle Azure), les étapes ci-dessous s’appliquent à toutes avec des différences mineures lors de la configuration de la planification de déploiement.

1. Dans le portail, pour planifier un déploiement pour :

   * une ou plusieurs machines, accédez à **Comptes Automation** et sélectionnez votre compte Automation avec Update Management activé dans la liste.
   * une machine virtuelle Azure, accédez à **Machines virtuelles** et sélectionnez votre machine virtuelle dans la liste.
   * Pour un serveur avec Azure Arc, accédez à **Serveurs - Azure Arc** et sélectionnez votre serveur dans la liste.

2. En fonction de la ressource que vous avez sélectionnée, accédez à Update Management :

   * Si vous avez sélectionné votre compte Automation, accédez à **Update Management** sous **Gestion des mises à jour**, puis sélectionnez **Planifier le déploiement de la mise à jour**.
   * Si vous avez sélectionné une machine virtuelle Azure, accédez à **Mises à jour de l’hôte et de l’invité**, puis sélectionnez **Accéder à Update Management**.
   * Si vous avez sélectionné un serveur avec Azure Arc, accédez à **Update Management**, puis sélectionnez **Planifier le déploiement de mises à jour**.

3. Sous **Nouveau déploiement de mises à jour**, dans le champ **Nom**, entrez un nom unique pour votre déploiement.

4. Sélectionnez le système d’exploitation à cibler pour le déploiement de mises à jour.

    > [!NOTE]
    > Cette option n’est pas disponible si vous avez sélectionné une machine virtuelle Azure ou un serveur avec Azure Arc. Le système d’exploitation est automatiquement identifié.

5. Dans la section **Groupes à mettre à jour**, définissez une requête qui combine un abonnement, des groupes de ressources, des emplacements et des balises pour créer un groupe dynamique de machines virtuelles Azure à inclure dans votre déploiement. Pour en savoir plus, consultez [Utiliser des groupes dynamiques avec Update Management](configure-groups.md).

    > [!NOTE]
    > Cette option n’est pas disponible si vous avez sélectionné une machine virtuelle Azure ou un serveur avec Azure Arc. La machine est automatiquement ciblée pour le déploiement planifié.

   > [!IMPORTANT]
   > Lors de la création d’un groupe dynamique de machines virtuelles Azure, Update Management prend en charge un maximum de 500 requêtes combinant des abonnements ou des groupes de ressources dans l’étendue du groupe.

6. Dans la section **Machines à mettre à jour**, sélectionnez une recherche enregistrée, un groupe importé, ou choisissez **Machines** dans la liste déroulante et sélectionnez des machines spécifiques. Avec cette option, vous pouvez voir la préparation de l’agent Log Analytics pour chaque machine. Pour en savoir plus sur les différentes méthodes de création de groupes d’ordinateurs dans les journaux Azure Monitor, consultez [Groupes d’ordinateurs dans les journaux Azure Monitor](../../azure-monitor/logs/computer-groups.md). Vous pouvez inclure jusqu’à 1000 machines dans un déploiement planifié de mises à jour.

    > [!NOTE]
    > Cette option n’est pas disponible si vous avez sélectionné une machine virtuelle Azure ou un serveur avec Azure Arc. La machine est automatiquement ciblée pour le déploiement planifié.

7. Utilisez la section **Classifications des mises à jour** pour spécifier des [classifications des mises à jour](view-update-assessments.md#work-with-update-classifications) pour les produits. Pour chaque produit, désélectionnez toutes les classifications de mises à jour prises en charge, à l’exception de celles que vous souhaitez inclure dans votre déploiement de mises à jour.

   :::image type="content" source="./media/deploy-updates/update-classifications-example.png" alt-text="Exemple montrant la sélection de classifications de mises à jour spécifiques.":::

    Si votre déploiement est censé appliquer uniquement un ensemble de mises à jour, il est nécessaire de désélectionner toutes les classifications de mise à jour présélectionnées lors de la configuration de l’option **Inclure/exclure des mises à jour**, comme décrit à l’étape suivante. Cela garantit que seules les mises à jour que vous avez spécifiées comme devant être *incluses* dans ce déploiement sont installées sur les ordinateurs cibles.

   >[!NOTE]
   > Le déploiement de mises à jour par classification ne fonctionne pas sur les versions RTM de CentOS. Pour déployer correctement les mises à jour pour CentOS, sélectionnez toutes les classifications pour garantir que les mises à jour sont appliquées. Il n’existe actuellement aucune méthode prise en charge permettant d’activer la disponibilité des données de classification natives sur CentOS. Pour plus d’informations sur les [classifications de mises à jour](overview.md#update-classifications), consultez les rubriques suivantes.

   >[!NOTE]
   > Le déploiement de mises à jour par classification des mises à jour peut ne pas fonctionner correctement pour les distributions Linux prises en charge par Update Management. Cela est dû à un problème identifié avec le schéma d’affectation de noms du fichier OVAL, qui empêche Update Management de faire correspondre correctement les classifications basées sur des règles de filtrage. En raison de la logique différente utilisée dans les évaluations des mises à jour de sécurité, les résultats peuvent différer des mises à jour de sécurité appliquées pendant le déploiement. Si vous avez défini la classification comme **Critique** et **Sécurité**, le déploiement de la mise à jour fonctionnera comme prévu. Seule la *classification des mises à jour* pendant une évaluation est affectée.
   >
   > Update Management pour les machines Windows Server n’est pas affecté. La classification des mises à jour et les déploiements sont inchangés.

8. Utilisez la région **Inclure/exclure des mises à jour** pour ajouter ou exclure des mises à jour sélectionnées du déploiement. Dans la page **Inclure/Exclure**, vous entrez les numéros d’identification des articles de la base de connaissances à inclure ou à exclure pour les mises à jour Windows. Pour les distributions Linux prises en charge, spécifiez le nom du package.

   :::image type="content" source="./media/deploy-updates/include-specific-updates-example.png" alt-text="Exemple montrant comment inclure des mises à jour spécifiques.":::

   > [!IMPORTANT]
   > N’oubliez pas que les exclusions sont prioritaires par rapport aux inclusions. Par exemple, si vous définissez une règle d’exclusion `*`, Update Management exclut tous les correctifs et packages de l’installation. Les correctifs exclus sont toujours affichés comme étant manquants sur les machines. Sur les machines Linux, si vous incluez un package qui a un package dépendant ayant été exclu, Update Management n’installe pas le package principal.

   > [!NOTE]
   > Vous ne pouvez pas spécifier des mises à jour qui ont été remplacées pour être incluses dans le déploiement des mises à jour.

   Voici quelques exemples de scénarios pour vous aider à comprendre comment utiliser l’inclusion/exclusion et la classification des mises à jour simultanément dans les déploiements de mises à jour :

   * Si vous souhaitez uniquement installer une liste spécifique de mises à jour, vous ne devez pas sélectionner de **classifications de mises à jour** et fournir une liste des mises à jour à appliquer à l’aide de l’option **Inclure**.

   * Si vous souhaitez installer uniquement les mises à jour de sécurité et critiques, ainsi qu’une ou plusieurs mises à jour facultatives, vous devez sélectionner **Sécurité** et **Critique** dans **Classifications de mises à jour**. Ensuite, pour l’option **Inclure**, spécifiez les KBID des mises à jour facultatives.

   * Si vous souhaitez installer uniquement les mises à jour de sécurité et critiques, mais ignorer une ou plusieurs mises à jour pour Python afin d’éviter d’arrêter votre application héritée, vous devez sélectionner **Sécurité** et **Critique** sous **Classifications de mises à jour**. Ensuite, pour l’option **Exclure**, ajoutez les packages Python à ignorer.

9. Sélectionnez **Paramètres de planification**. L’heure de début par défaut est dans 30 minutes. Vous pouvez définir l’heure de début à tout moment à partir de 10 minutes à l’avenir.

    > [!NOTE]
    > Cette option est différente si vous avez sélectionné un serveur avec Azure Arc. Vous pouvez sélectionner **Mettre à jour maintenant** ou une heure de début 20 minutes dans le futur.

10. Utilisez la **périodicité** pour spécifier si le déploiement se produit une seule fois ou utilise une planification récurrente, puis sélectionnez **OK**.

11. Dans la section **Préscripts + postscripts**, sélectionnez les scripts à exécuter avant et après votre déploiement. Pour plus d’informations, consultez [Gérer des pré-scripts et des post-scripts](pre-post-scripts.md).

12. Utilisez le champ **Fenêtre de maintenance (minutes)** afin de spécifier la durée autorisée pour l’installation des mises à jour. Tenez compte des détails suivants au moment de spécifier une fenêtre de maintenance :

    * Les fenêtres de maintenance contrôlent le nombre de mises à jour installées.
    * Update Management n’arrête pas l’installation de nouvelles mises à jour si la fin d’une fenêtre de maintenance est proche.
    * Update Management ne met pas fin aux mises à jour en cours si la fenêtre de maintenance est dépassée. Aucune mise à jour restante ne fait l’objet d’une tentative d’installation. Si cela se produit régulièrement, vous devez réévaluer la durée de votre fenêtre de maintenance.
    * Le dépassement de la fenêtre de maintenance sur Windows est souvent le signe que l’installation d’une mise à jour de Service Pack prend beaucoup de temps.

    > [!NOTE]
    > Pour éviter que les mises à jour soient appliquées en dehors d’une fenêtre de maintenance sur Ubuntu, reconfigurez le package `Unattended-Upgrade` pour désactiver les mises à jour automatiques. Pour plus d’informations sur la façon de configurer le package, consultez la [rubrique sur les mises à jour automatiques du Guide du serveur Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

13. Utilisez le champ **Options de redémarrage** pour spécifier la manière de gérer les redémarrages au cours du déploiement. Les options suivantes sont disponibles : 
    * Redémarrer si nécessaire (option par défaut)
    * Toujours redémarrer
    * Ne jamais redémarrer
    * Redémarrer uniquement ; cette option n’installe pas les mises à jour

    > [!NOTE]
    > Les clés de Registre listées sous [Clés de Registre utilisées pour gérer le redémarrage](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) peuvent provoquer un événement de redémarrage même si **Options de redémarrage** est défini sur **Ne jamais redémarrer**.

14. Quand vous avez terminé de configurer la planification du déploiement, sélectionnez **Créer**.

    ![Volet Paramètres de planification des mises à jour](./media/deploy-updates/manageupdates-schedule-win.png)

    > [!NOTE]
    > Lorsque vous avez terminé de configurer la planification de déploiement pour un serveur avec Azure Arc sélectionné, sélectionnez **Vérifier + créer**.

15. Vous revenez au tableau de bord d’état. Sélectionnez **Planifications de déploiement** pour afficher la planification de déploiement que vous avez créée. Un maximum de 500 planifications sont répertoriées. Si vous avez plus de 500 planifications et que vous souhaitez consulter la liste complète, consultez la méthode d’API REST [Configurations de la mise à jour logicielle – Liste](/rest/api/automation/softwareupdateconfigurations/list). Spécifiez l’API version 2019-06-01 ou ultérieure.

## <a name="schedule-an-update-deployment-programmatically"></a>Planifier un déploiement de mises à jour par programmation

Pour savoir comment créer un déploiement de mises à jour avec l’API REST, consultez [Configurations des mises à jour logicielles - Créer](/rest/api/automation/softwareupdateconfigurations/create).

Vous pouvez utiliser un exemple de runbook pour créer un déploiement de mises à jour hebdomadaire. Pour en savoir plus sur ce runbook, consultez [Créer un déploiement de mises à jour hebdomadaires pour une ou plusieurs machines virtuelles dans un groupe de ressources](https://github.com/azureautomation/create-a-weekly-update-deployment-for-one-or-more-vms-in-a-resource-group).

## <a name="check-deployment-status"></a>Vérifier l’état du déploiement

Une fois que le déploiement planifié a démarré, vous pouvez voir son état sous l’onglet **Historique** sous **Gestion des mises à jour**. Si le déploiement est en cours d’exécution, son état est **En cours**. Quand le déploiement s’est déroulé correctement, son état passe à **Réussi**. Si une ou plusieurs mises à jour ont échoué pendant le déploiement, l’état passe à **Échec**.

## <a name="view-results-of-a-completed-update-deployment"></a>Afficher les résultats d’un déploiement de mises à jour terminé

Quand le déploiement est terminé, vous pouvez le sélectionner pour voir les résultats correspondants.

[ ![Tableau de bord des états de déploiement des mises à jour pour un déploiement spécifique](./media/deploy-updates/manageupdates-view-results.png)](./media/deploy-updates/manageupdates-view-results-expanded.png#lightbox)

Sous **Résultats des mises à jour**, un récapitulatif indique le nombre total de mises à jour et de résultats du déploiement sur les machines virtuelles cibles. Le tableau de droite montre une répartition détaillée des mises à jour et les résultats de l’installation de chacune d’elles.

Les valeurs disponibles sont :

* **Aucune tentative effectuée** : la mise à jour n’a pas été installée car le temps disponible était insuffisant, d’après la durée de la fenêtre de maintenance définie.
* **Non sélectionné** : la mise à jour n’a pas été sélectionnée pour le déploiement.
* **Réussi** : la mise à jour a réussi.
* **Échec** : la mise à jour a échoué.

Pour voir toutes les entrées de journal d’activité créées par le déploiement, sélectionnez **Tous les journaux d’activité**.

Cliquez sur **Sortie** pour voir le flux des tâches du runbook chargé de gérer le déploiement des mises à jour sur les machines virtuelles cibles.

Pour afficher les informations détaillées sur les erreurs du déploiement, sélectionnez **Erreurs**.

## <a name="deploy-updates-across-azure-tenants"></a>Déployer des mises à jour dans tous les locataires Azure

Si des machines devant être mises à jour se trouvent dans un autre rapport de tenant Azure pour Update Management, vous devez utiliser une solution de contournement pour planifier l’opération. Vous pouvez utiliser la cmdlet [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) avec le paramètre `ForUpdateConfiguration` pour créer une planification. Vous pouvez utiliser la cmdlet [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration) et transférer les ordinateurs de l’autre locataire vers le paramètre `NonAzureComputer`. L’exemple suivant vous montre comment procéder.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule `
    -ResourceGroupName mygroup `
    -AutomationAccountName myaccount `
    -Name myupdateconfig `
    -Description test-OneTime `
    -OneTime `
    -StartTime $startTime `
    -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  `
    -ResourceGroupName $rg `
    -AutomationAccountName <automationAccountName> `
    -Schedule $sched `
    -Windows `
    -NonAzureComputer $nonAzurecomputers `
    -Duration (New-TimeSpan -Hours 2) `
    -IncludedUpdateClassification Security,UpdateRollup `
    -ExcludedKbNumber KB01,KB02 `
    -IncludedKbNumber KB100
```

## <a name="next-steps"></a>Étapes suivantes

* Pour savoir comment créer des alertes pour vous informer des résultats du déploiement des mises à jour, consultez [Créer des alertes pour Update Management](configure-alerts.md).
* Pour résoudre les erreurs générales d’Update Management, consultez [Résoudre les problèmes liés à Update Management](../troubleshoot/update-management.md).
