---
title: Mettre à niveau vers la préversion du service Mobilité et des composants de l’appliance
description: Cet article décrit les mises à jour automatiques pour la préversion de l’agent de mobilité, ainsi que la procédure impliquée dans les mises à jour manuelles.
ms.service: site-recovery
ms.topic: article
ms.date: 09/01/2021
ms.openlocfilehash: a683aacc762ce169045ee17891b49624ef1ee991
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131437641"
---
# <a name="upgrade-mobility-service-and-appliance-components-preview"></a>Mettre à niveau vers la préversion du service Mobilité et des composants de l’appliance

Dans cette version préliminaire, vous n’avez pas besoin de conserver les informations d’identification racine/administrateur de l’ordinateur source pour effectuer des mises à niveau. Les informations d’identification sont requises uniquement pour l’installation initiale de l’agent. Vous pourrez ensuite supprimer les informations d’identification.


## <a name="update-mobility-agent-automatically"></a>Mettre à jour automatiquement l’agent de mobilité

Par défaut, les mises à jour automatiques sont activées sur un coffre. Les mises à jour automatiques sont déclenchées à 12:00 (midi) heure locale chaque jour, si une nouvelle version est disponible.

> [!NOTE]
> Si vous utilisez des préversions privées, les mises à jour automatiques sont bloquées pour les ordinateurs protégés. Veillez à configurer Site Recovery sur votre ordinateur, à l’aide d’une nouvelle appliance de préversion.

Pour bénéficier des fonctionnalités, améliorations et correctifs les plus récents, nous vous recommandons de choisir l’option **Autoriser Site Recovery à gérer** dans **Mobility agent upgrade settings (Preview)** (Paramètres de mise à niveau vers la préversion de l’agent de mobilité). Les mises à jour automatiques ne nécessitent pas de redémarrage ou n’affectent pas la réplication continue de vos machines virtuelles. Les mises à jour automatiques garantissent également que tous les dispositifs de réplication dans le coffre sont automatiquement mis à jour.

![Mises à jour automatiques de l’agent de mobilité](./media/upgrade-mobility-service-preview/automatic-updates-on.png)

Pour désactiver les mises à jour automatiques, activez le bouton **Autoriser Site Recovery à gérer**.

![Mises à jour automatiques désactivées pour l’agent de mobilité](./media/upgrade-mobility-service-preview/automatic-updates-off.png)


## <a name="update-mobility-agent-manually"></a>Mettre à jour manuellement l’agent de mobilité

Si vous avez désactivé les mises à jour automatiques pour votre agent de mobilité, vous pouvez effectuer une mise à jour manuelle de l’agent en suivant ces procédures :

### <a name="upgrade-mobility-agent-on-multiple-protected-items"></a>Mettre à niveau l’agent de mobilité sur plusieurs éléments protégés

Pour mettre à jour manuellement l’agent de mobilité sur plusieurs éléments protégés, procédez comme suit :

1. Accédez à **Coffre Recovery Services** > **Éléments répliqués**, cliquez sur *Une nouvelle mise à jour de l'agent de mobilité Azure Site Recovery est disponible*. Cliquez pour l'installer.

   ![Mise à jour manuelle de l’agent de mobilité sur plusieurs éléments protégés](./media/upgrade-mobility-service-preview/agent-update.png)

2. Choisissez les machines sources à mettre à jour, puis cliquez sur **OK**.

   >[!NOTE]
   >Si la configuration requise pour mettre à niveau le service mobilité n’est pas respectée, la machine virtuelle ne peut pas être sélectionnée. En savoir plus sur [la façon de résoudre ce problème](#resolve-blocking-issues-for-agent-upgrade).


4. Après l’initialisation de la mise à niveau, un travail de Site Recovery est créé dans le coffre pour chaque opération de mise à niveau et peut être suivi en accédant à **Supervision** > **Travaux Site Recovery**.

### <a name="update-mobility-agent-for-a-single-protected-machine"></a>Mettre à jour l’agent de mobilité pour un seul ordinateur protégé

Pour mettre à jour l’agent de mobilité d’un élément protégé, procédez comme suit :
1. Accédez à **Coffre Recovery Services** > **Éléments répliqués**, puis sélectionnez une machine virtuelle.
2. Dans le panneau **Vue d’ensemble** de la machine virtuelle, en regard de **Version de l’agent**, consultez la version actuelle de l’agent de mobilité. Si une nouvelle mise à jour est disponible, l’état est mis à jour sur **Nouvelle mise à jour disponible**.

   ![Mise à jour manuelle de l’agent de mobilité sur un seul élément protégé](./media/upgrade-mobility-service-preview/agent-version.png)

3. Cliquez sur **Nouvelle mise à jour disponible**. La dernière version disponible s’affiche. Cliquez sur **Mettre à jour avec cette version** pour lancer le travail de mise à jour.

   ![détails de la mise à jour de l’agent de mobilité](./media/upgrade-mobility-service-preview/agent-update-details.png)

   > [!NOTE]
   > Si la mise à niveau est bloquée, vérifiez et résolvez les erreurs comme indiqué [ici](#resolve-blocking-issues-for-agent-upgrade).

## <a name="mobility-agent-on-latest-version"></a>Agent de mobilité actualisé

Une fois l’agent de mobilité mis à jour manuellement ou automatiquement vers sa dernière version, l’état affiché est **À jour**.

### <a name="resolve-blocking-issues-for-agent-upgrade"></a>Résoudre les problèmes empêchant la mise à niveau de l’agent

Si les conditions requises pour mettre à niveau l’agent de mobilité ne sont pas respectées, la machine virtuelle ne peut pas être mise à jour. Voici quelques exemples de problèmes.

Il est tout à fait possible que d’autres surviennent.

- Un redémarrage obligatoire est en attente sur l’ordinateur protégé.

- La version de l’appliance de réplication n’est pas compatible.

- Un problème de communication est survenu entre les composants de l’appliance de réplication, le serveur proxy ou le serveur de processus et les services Azure.

- L’agent de mobilité de l’ordinateur protégé n’est pas en mesure de communiquer avec l’appliance de réplication.

Si vous rencontrez l’un des problèmes ci-dessus, l’état affiché est **Impossible de mettre à jour avec la dernière version**. Cliquez sur cet état pour afficher les causes du blocage de la mise à jour et les actions recommandées pour résoudre le problème.

>[!NOTE]
>Après avoir résolu les causes du blocage, patientez 30 minutes avant de réessayer les opérations. Il vous faudra patienter un peu avant de pouvoir consulter les dernières informations mises à jour dans les services Site Recovery.

### <a name="mobility-agent-upgrade-job-failure"></a>Échec de la tâche de mise à niveau de l’agent de mobilité

En cas d’échec de l’opération de mise à niveau manuelle ou automatique de l’agent de mobilité, le travail est mis à jour pour afficher les causes de l’échec. Corrigez les erreurs et relancez l’opération.

Pour afficher les erreurs ayant causé l’échec, vous pouvez accéder aux travaux de Site Recovery, puis cliquer sur un travail spécifique pour connaître les moyens de résoudre les erreurs. Vous pouvez également suivre les étapes ci-dessous :

1. Accédez à la section Éléments répliqués, puis sélectionnez une machine virtuelle.

2. Dans le panneau **Vue d’ensemble**, en regard de **Version de l’agent**, vous pouvez voir quelle est la version de l’agent de mobilité.

3. À côté de la version actuelle, l’état est **Échec de la mise à jour**. Cliquez sur l’état pour réitérer l’opération de mise à jour.

4.  Un lien vers le travail de mise à niveau précédent est disponible. Cliquez dessus pour accéder au travail.

5. Résolvez les erreurs ayant empêché le bon déroulement du travail.

Déclenchez l’opération de mise à jour après avoir résolu les erreurs.

## <a name="upgrade-appliance"></a>Mettre à niveau l’appliance

L’appliance est mise à jour automatiquement par défaut. Les mises à jour automatiques sont déclenchées à 12:00 (midi) heure locale chaque jour, si une nouvelle version est disponible pour l’un des composants.

Pour vérifier l’état de mise à jour de l’un des composants, accédez au serveur de l’appliance et ouvrez **Gestionnaire de configuration d’appliance Microsoft Azure**. Accédez à **Composants de l’appliance** et développez l’affichage pour consulter la liste indiquant tous les composants et leurs versions.

Si une mise à jour est requise, cela est indiqué dans la colonne **État**. Dans ce cas, cliquez sur le message d’état pour mettre à niveau le composant.

  ![composants d’appliance de réplication](./media/upgrade-mobility-service-preview/appliance-components.png)

### <a name="turn-off-auto-update"></a>Désactiver la mise à jour automatique

1. Sur le serveur exécutant l’appliance, ouvrez l’Éditeur du Registre.
2. Accédez à **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**.
3. Pour désactiver la mise à jour automatique, créez une clé de Registre **AutoUpdate** avec la valeur DWORD 0.

    ![Définir la clé de Registre](./media/upgrade-mobility-service-preview/registry-key.png)


### <a name="turn-on-auto-update"></a>Activer la mise à jour automatique

Vous pouvez activer la mise à jour automatique en supprimant la clé de Registre AutoUpdate de HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance.

Pour supprimer la clé de Registre :

1. Sur le serveur exécutant l’appliance, ouvrez l’Éditeur du Registre.
2. Accédez à **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**.
3. Supprimez la clé de Registre **AutoUpdate** précédemment créée pour désactiver la mise à jour automatique.

### <a name="resolve-issues-with-component-upgrade"></a>Résoudre les problèmes de mise à niveau des composants

Si les conditions requises pour la mise à niveau des composants ne sont pas respectées, cette dernière ne peut avoir lieu. Voici quelques exemples de problèmes. Il est tout à fait possible que d’autres surviennent.

- La version de l’un des composants de l’appliance de réplication n’est pas compatible avec les autres.

- L’appliance de réplication ne parvient pas à communiquer avec les services Azure.

Si vous rencontrez l’un des problèmes ci-dessus, l’état affiché est **Impossible de mettre à jour avec la dernière version**. Cliquez sur cet état pour afficher les causes du blocage de la mise à jour et les actions recommandées pour résoudre le problème. Après avoir résolu les causes du blocage, effectuez manuellement la mise à jour.
