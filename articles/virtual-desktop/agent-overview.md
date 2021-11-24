---
title: Bien démarrer avec l’agent Azure Virtual Desktop
description: Vue d’ensemble de l’agent Azure Virtual Desktop et des processus de mise à jour.
author: Sefriend
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: sefriend
manager: clarkn
ms.openlocfilehash: ae07694e72f910945fbb213a448bfd910fef9786
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132483863"
---
# <a name="get-started-with-the-azure-virtual-desktop-agent"></a>Bien démarrer avec l’agent Azure Virtual Desktop

Dans l’infrastructure de service Azure Virtual Desktop, il existe trois composants principaux : le client Bureau à distance, le service et les machines virtuelles. Ces machines virtuelles se trouvent dans l’abonnement client où sont installés l’agent Azure Virtual Desktop et le chargeur de démarrage de l’agent. L’agent agit en tant que communicateur intermédiaire entre le service et les machines virtuelles, ce qui permet la connectivité. Par conséquent, si vous rencontrez des problèmes lors de l’installation, de la mise à jour ou de la configuration de l’agent, vos machines virtuelles ne seront pas en mesure de se connecter au service. Le chargeur de démarrage de l’agent est l’exécutable qui charge l’agent. 

Cet article donne un bref aperçu des processus d’installation et de mise à jour de l’agent.

>[!NOTE]
>Cette documentation ne concerne pas l’agent FSLogix ou l’agent client Bureau à distance.


## <a name="initial-installation-process"></a>Processus d'installation initiale

L’agent Azure Virtual Desktop est installé initialement de l’une des deux manières suivantes. Si vous approvisionnez des machines virtuelles dans le portail Azure et la Place de marché Azure, l’agent et le chargeur de démarrage de l’agent sont automatiquement installés. Si vous approvisionnez des machines virtuelles à l’aide de PowerShell, vous devez télécharger manuellement les fichiers .msi de l’agent et du chargeur de démarrage de l’agent lors de la [création d’un pool d’hôtes Azure Virtual Desktop avec PowerShell](create-host-pools-powershell.md#register-the-virtual-machines-to-the-azure-virtual-desktop-host-pool). Une fois l’agent installé, cela installe la pile côte à côte de l’agent Azure Virtual Desktop et l’agent de surveillance Geneva. Le composant de pile côte à côte est requis pour que les utilisateurs établissent en toute sécurité des connexions de serveur à client inverses. L’agent de surveillance Geneva analyse l’intégrité de l’agent. Ces trois composants sont tous essentiels pour que la connectivité des utilisateurs de bout en bout fonctionne correctement.

>[!IMPORTANT]
>Pour installer correctement l’agent Azure Virtual Desktop, la pile côte à côte et l’agent de surveillance Geneva, vous devez débloquer toutes les URL répertoriées dans la [liste des URL requises](safe-url-list.md#virtual-machines). Le déblocage de ces URL est nécessaire pour utiliser le service Azure Virtual Desktop.

## <a name="agent-update-process"></a>Processus de mise à jour de l’agent

Le service Azure Virtual Desktop met à jour l’agent chaque fois qu’une mise à jour devient disponible. Les mises à jour de l’agent peuvent inclure de nouvelles fonctionnalités ou des correctifs de problèmes précédents. Vous devez toujours disposer de la dernière version stable de l’agent pour que vos machines virtuelles ne connaissent aucune perte de connectivité ou de sécurité. Une fois la version initiale de l’agent Azure Virtual Desktop installée, l’agent interroge régulièrement le service Azure Virtual Desktop pour déterminer s’il existe une version plus récente de l’agent, de la pile ou du composant d’analyse. Si une version plus récente de l’un des composants a déjà été déployée, le composant mis à jour est installé automatiquement par le système de distribution de version d’évaluation.

Les nouvelles versions de l’agent sont déployées à intervalles réguliers dans des périodes de cinq jours sur tous les abonnements Azure. Ces périodes de mise à jour sont appelées « distributions de versions d’évaluation ». Il faut 24 heures pour que toutes les machines virtuelles d’une même région de répartiteur reçoivent la mise à jour de l’agent avec un vol. En raison de cela, en cas de distribution de version d’évaluation, il se peut que les machines virtuelles de votre pool d’hôtes reçoivent la mise à jour de l’agent à des moments différents. En outre, si les machines virtuelles se trouvent dans des régions différentes, elles peuvent être actualisées lors de jours différents au cours de la période de cinq jours. Le vol met à jour tous les agents de machine virtuelle dans tous les abonnements à la fin de la période de déploiement. Le système de déploiement en mode Flighting d’Azure Virtual Desktop améliore la fiabilité du service en garantissant la stabilité et la qualité de la mise à jour de l’agent.


Autres éléments importants à prendre en compte :

- La mise à jour de l’agent n’est pas connectée aux mises à jour de version de l’infrastructure Azure Virtual Desktop. La mise à jour de l’infrastructure Azure Virtual Desktop ne signifie pas que l’agent a été mis à jour en même temps.
- Étant donné que les machines virtuelles de votre pool d’hôtes peuvent recevoir des mises à jour de l’agent à des moments différents, vous devez être en mesure de déterminer la différence entre les problèmes de distribution de version d’évaluation et les mises à jour d’agent qui ont échoué. Si vous accédez aux journaux des événements de votre machine virtuelle dans **Observateur d’événements** > **Journaux Windows** > **Application** et que vous voyez un événement intitulé « ID 3277 », cela signifie que la mise à jour de l’agent n’a pas fonctionné. Si vous ne voyez pas cet événement, cela signifie que la machine virtuelle se trouve dans un autre mode Flighting et sera mise à jour ultérieurement.
- Lorsque l’agent de surveillance Geneva est mis à jour vers la dernière version, l’ancienne tâche GenevaTask est localisée et désactivée avant la création d’une tâche pour le nouvel agent de surveillance. La version antérieure de l’agent de surveillance n’est pas supprimée si la version la plus récente de l’agent de surveillance rencontre un problème nécessitant de revenir à la version antérieure pour effectuer une correction. Si la dernière version rencontre un problème, l’ancien agent de surveillance est réactivé pour continuer à fournir des données de surveillance. Toutes les versions du moniteur antérieures à la dernière que vous avez installée avant la mise à jour seront supprimées de votre machine virtuelle.
- Votre machine virtuelle conserve trois versions de l’agent et de la pile côte à côte à la fois. Cela permet une récupération rapide en cas de problème avec la mise à jour. La version la plus ancienne de l’agent ou de la pile est supprimée de la machine virtuelle à chaque mise à jour de l’agent ou de la pile. Si vous supprimez ces composants prématurément et que l’agent ou la pile rencontre un échec, l’agent ou la pile ne pourra pas revenir à une version antérieure, ce qui placera votre machine virtuelle dans un état d’indisponibilité.

La mise à jour de l’agent dure normalement 2-3 minutes sur une nouvelle machine virtuelle et ne doit pas entraîner de perte de connexion ou d’arrêt de votre machine virtuelle. Ce processus de mise à jour s’applique tant à la version classique qu’à la dernière version d’Azure Virtual Desktop avec Azure Resource Manager.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous comprenez mieux l’agent Azure Virtual Desktop, voici quelques ressources qui peuvent vous aider :

- Si vous rencontrez des problèmes d’agent ou de connectivité, consultez le [Guide de dépannage des problèmes liés à l’agent Azure Virtual Desktop](troubleshoot-agent.md).
