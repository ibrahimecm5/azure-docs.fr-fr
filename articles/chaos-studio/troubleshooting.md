---
title: Résoudre les problèmes courants liés à Azure Chaos Studio
description: Apprenez à résoudre les problèmes courants liés à l’utilisation d’Azure Chaos Studio
author: c-ashton
ms.service: chaos-studio
ms.author: cashton
ms.topic: troubleshooting
ms.date: 11/10/2021
ms.custom: template-troubleshooting, ignite-fall-2021
ms.openlocfilehash: 9d56666f42d837f59ec22031646d46e22f0b298d
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132372489"
---
# <a name="troubleshoot-issues-with-azure-chaos-studio"></a>Résoudre les problèmes liés à Azure Chaos Studio

Lorsque vous utilisez Chaos Studio, vous pouvez parfois rencontrer des problèmes. Cet article décrit en détail les problèmes courants, et indique la façon de les résoudre.

## <a name="general-troubleshooting-tips"></a>Conseils généraux de dépannage

Les sources suivantes sont utiles lors de la résolution des problèmes liés à Chaos Studio :
1. **Le journal d’activité** : le [Journal d’activité Azure](../azure-monitor/essentials/activity-log.md) contient un enregistrement de toutes les opérations de création, de mise à jour et de suppression dans un abonnement, y compris les opérations Chaos Studio comme l’activation d’une cible et/ou des fonctionnalités, l’installation de l’agent et la création ou l’exécution d’une expérience. Les échecs dans le journal d’activité indiquent qu’une action de l’utilisateur essentielle à l’utilisation de Chaos Studio n’a peut-être pas pu se terminer. La plupart des erreurs de service-direct injectent également des erreurs en exécutant une opération Azure Resource Manager. Par conséquent, le journal d’activité aura également l’enregistrement des erreurs injectées pendant une expérimentation pour certaines erreurs de service-direct.
2. **Détails de l’expérience** : les détails de l’exécution de l’expérience affichent l’état et les erreurs d’une expérimentation individuelle. L’ouverture d’une erreur spécifique dans les détails de l’expérience présente les ressources qui ont échoué et les messages d’erreur relatifs à un échec. [En savoir plus sur la façon d’accéder aux détails de l’expérience](chaos-studio-run-experiment.md#view-experiment-history-and-details).
3. **Journaux de l’agent**: si vous utilisez une défaillance basée sur un agent, vous devrez peut-être utiliser le protocole RDP ou SSH dans la machine virtuelle pour comprendre la raison pour laquelle l’agent n’a pas réussi à exécuter une erreur. Les instructions d’accès aux journaux de l’agent dépendent du système d’exploitation :
    * **Agent de Windows Chaos** : les journaux de l’agent se trouvent dans le journal des événements Windows dans la catégorie Application avec la source AzureChaosAgent. L’agent ajoute une activité d’erreur et un contrôle d’intégrité normal (possibilité de s’authentifier auprès des événements du service de l’agent Chaos Studio) et de communiquer avec ce journal.
    * **Agent Chaos de Linux** : l’agent Linux utilise systemd pour gérer le processus de l’agent en tant que service Linux. Pour afficher le journal systemd de l’agent (événements consignés par le service d’agent), exécutez la commande `journalctl -u azure-chaos-agent`.
4. **État de l’extension de machine virtuelle** : si vous utilisez une erreur basée sur un agent, vous devrez peut-être également vérifier que l’extension de machine virtuelle est installée et intègre. Dans le Portail Azure, accédez à votre machine virtuelle et accédez à **Extensions** ou **Extensions + applications**. Cliquez sur l’extension ChaosAgent et recherchez les champs suivants :
    * **État** doit indiquer « Approvisionnement réussi ». Tout autre état indique que l’installation de l’agent a échoué. Vérifiez que la [Configuration système requise](chaos-studio-limitations.md#limitations) est respectée et essayez de réinstaller l’agent.
    * **État du gestionnaire** doit indiquer « Prêt ». Tout autre «tat indique que l’agent est installé mais qu’il ne peut pas se connecter au service Chaos Studio. Vérifiez que toutes les [exigences réseau](chaos-studio-limitations.md#limitations) sont satisfaites et que l’identité gérée affectée à l’utilisateur a été ajoutée à la machine virtuelle et essayez de redémarrer.

## <a name="issues-onboarding-a-resource"></a>Problèmes d’intégration d’une ressource

### <a name="resources-do-not-show-up-in-the-targets-list-in-the-azure-portal"></a>Les ressources ne sont pas affichées dans la liste des cibles dans le Portail Azure
Si vous ne voyez pas les ressources que vous souhaitez activer dans la liste des cibles de Chaos Studio, cela peut être dû à l’un des problèmes suivants :
* Les ressources ne sont pas dans [une région prise en charge pour Chaos Studio](https://azure.microsoft.com/global-infrastructure/services/?products=chaos-studio).
* Les ressources ne sont pas d'[un type de ressource pris en charge dans Chaos Studio](chaos-studio-fault-providers.md).
* Les ressources se trouvent dans un abonnement ou un groupe de ressources qui sont filtrées dans les filtres de la liste cible. Modifiez les filtres d’abonnement et de groupe de ressources pour afficher vos ressources.

### <a name="target-andor-capability-enablement-fails-or-doesnt-show-correctly-in-the-target-list"></a>L'activation des cibles et/ou des fonctionnalités échoue ou n'apparaît pas correctement dans la liste des cibles
Si vous voyez une erreur lors de l’activation des cibles et/ou des fonctionnalités, procédez comme suit :
1. Vérifiez que vous disposez des autorisations appropriées pour les ressources que vous intégrez. L’activation d’une cible et/ou de fonctionnalités requiert l’autorisation Microsoft.Chaos/\* au niveau de la portée de la ressource. Les rôles intégrés tels que Contributeur disposent d’une autorisation de lecture et d’écriture de caractères génériques, qui comprend l’autorisation sur toutes les opérations Microsoft.Chaos.
2. Attendez quelques minutes que la liste cible et la liste de fonctionnalités soient mises à jour. Le Portail Azure utilise Azure Resource Graph pour collecter des informations sur l’intégration des capacités et des cibles, et peut prendre jusqu’à cinq minutes pour la propagation de la mise à jour.
3. Si la ressource affiche toujours « Non activé », procédez comme suit :
    1. Réessayez d’activer la ressource.
    2. Si l’activation des ressources échoue toujours, visitez le journal d’activité et recherchez l’opération de création de cible ayant échoué pour afficher des informations détaillées sur l’erreur.
4. Si la ressource affiche « Activé » mais que les fonctionnalités d’intégration ont échoué, procédez comme suit :
    1. Cliquez sur le bouton **Gérer les actions** de la ressource dans la liste de cibles. Vérifiez les fonctionnalités qui n’ont pas été vérifiées, puis cliquez sur **Enregistrer**.
    2. Si l’activation des fonctionnalités échoue toujours, visitez le journal d’activité et recherchez l’opération de création de cible ayant échoué pour afficher des informations détaillées sur l’erreur.

## <a name="prerequisite-issues"></a>Problèmes liés aux conditions préalables

Certains problèmes sont dus à des conditions préalables manquantes. 

### <a name="agent-based-faults-fail-on-a-virtual-machine"></a>Échec des erreurs basées sur un agent sur une machine virtuelle
Les erreurs basées sur un agent peuvent échouer pour diverses raisons liées à des conditions préalables manquantes :
* Sur les machines virtuelles Linux, les erreurs [Sollicitation du processeur](chaos-studio-fault-library.md#cpu-pressure), [Sollicitation de la mémoire physique](chaos-studio-fault-library.md#physical-memory-pressure), [Sollicitation des disques E/S](chaos-studio-fault-library.md#disk-io-pressure-linux) et [Contrainte Stress-ng arbitraire](chaos-studio-fault-library.md#arbitrary-stress-ng-stress) nécessitent l’installation de l’[utilitaire stress-ng](https://wiki.ubuntu.com/Kernel/Reference/stress-ng) sur votre machine virtuelle. Pour plus d’informations sur l’installation de stress-ng, consultez les sections conditions préalables pour les erreurs.
* Sur les machines virtuelles Linux ou Windows, l’identité gérée affectée par l’utilisateur fournie lors de l’activation de la cible basée sur l’agent doit également être ajoutée à la machine virtuelle.
* Sur les machines virtuelles Linux ou Windows, l’identité gérée affectée par le système pour l’expérience doit se voir attribuer le rôle de lecteur sur la machine virtuelle (les rôles apparemment élevés, tels que le contributeur de machine virtuelle, n’incluent pas l'opération \*/Read nécessaire au service de l’agent Chaos Studio pour lire la ressource de proxy cible microsoft-agent sur la machine virtuelle).

### <a name="aks-chaos-mesh-faults-fail"></a>Échec des erreurs AKS Chaos Mesh
Les erreurs AKS Chaos Mesh peuvent échouer pour diverses raisons liées à des conditions préalables manquantes :
* Vous devez d’abord installer Chaos Mesh sur le cluster AKS avant d’utiliser les erreurs AKS Chaos Mesh. Vous trouverez des instructions dans le [didacticiel sur les erreurs Chaos Mesh sur AKS](chaos-studio-tutorial-aks-portal.md#set-up-chaos-mesh-on-your-aks-cluster).
* Chaos Mesh doit être de version 2.0.4 ou supérieure. Vous pouvez récupérer la version de Chaos Mesh en vous connectant à votre cluster AKS et en exécutant `helm version chaos-mesh`.
* Chaos Mesh doit être installé avec l’espace de noms `chaos-testing`. Les autres noms d’espaces de noms pour Chaos Mesh ne sont pas pris en charge.
* Le rôle d’administrateur de cluster Azure Kubernetes Service doit être affecté à l’identité gérée affectée par le système pour l’expérimentation chaos.

## <a name="issues-creating-or-designing-an-experiment"></a>Problèmes lors de la création ou de la conception d’une expérience

### <a name="when-adding-a-fault-my-resource-does-not-show-in-the-target-resources-list"></a>Quand vous ajoutez une erreur, ma ressource ne s’affiche pas dans la liste des ressources cibles
Lorsque vous ajoutez une erreur, si vous ne voyez pas la ressource que vous souhaitez cibler avec une erreur dans la liste des ressources cibles, cela peut être dû à l’un des problèmes suivants :
* Le filtre **Abonnement** est défini pour exclure l’abonnement dans lequel votre cible est déployée. Cliquez sur le filtre d’abonnement et modifiez les abonnements sélectionnés.
* La ressource n’a pas encore été intégrée. Accédez à la vue **Cibles** et activez la cible. Une fois cette opération terminée, vous devez fermer le volet Ajouter une erreur et le rouvrir pour afficher une liste cible mise à jour.
* La ressource n’a pas encore été activée pour le type de cible de cette erreur. Consultez la [bibliothèque d’erreurs](chaos-studio-fault-library.md) pour voir quel type de cible est utilisé pour l’erreur, puis accédez à la vue **Cibles** et activez ce type de cible (soit l’agent pour les erreurs microsoft-agent, soit le service direct pour tous les autres types de cibles). Une fois cette opération terminée, vous devez fermer le volet Ajouter une erreur et le rouvrir pour afficher une liste cible mise à jour.
* La ressource n’a pas encore la capacité de cette erreur activée. Consultez la [bibliothèque d’erreurs](chaos-studio-fault-library.md) pour connaître le nom de la fonctionnalité d’erreur, accédez à la vue **Cibles**, puis cliquez sur **Gérer les actions** sur la ressource cible. Cochez la case correspondant à la capacité qui correspond à l’erreur que vous essayez d’exécuter, puis cliquez sur **Enregistrer**. Une fois cette opération terminée, vous devez fermer le volet Ajouter une erreur et le rouvrir pour afficher une liste cible mise à jour.
* La ressource vient d’être intégrée récemment et n’a pas encore été affichée dans Azure Resource Graph. La liste des ressources cibles est interrogée à partir d’Azure Resource Graph. Après avoir activé une nouvelle cible, la mise à jour peut prendre jusqu’à cinq minutes pour se propager à Azure Resource Graph. Patientez quelques minutes, puis rouvrez le volet Ajouter une erreur.

### <a name="when-creating-an-experiment-i-get-the-error-the-microsoftagent-provider-requires-a-managed-identity"></a>Lors de la création d’une expérience, j’obtiens l’erreur `The microsoft:agent provider requires a managed identity`

Cette erreur se produit lorsque l’agent n’a pas été déployé sur votre machine virtuelle. Pour obtenir des instructions d’installation, consultez [Créer et exécuter une expérience qui utilise des erreurs basées sur des agents](chaos-studio-tutorial-agent-based-portal.md).

### <a name="when-creating-an-experiment-i-get-the-error-the-content-media-type-null-is-not-supported-only-applicationjson-is-supported"></a>Lors de la création d’une expérience, j’obtiens l’erreur `The content media type '<null>' is not supported. Only 'application/json' is supported.`

Cette erreur peut se produire si vous créez votre expérience à l’aide d’un modèle ARM ou de l’API REST de Chaos Studio. L’erreur indique qu’il existe un JSON mal formé dans votre définition d’expérimentation. Vérifiez si vous avez des erreurs de syntaxe, telles que des accolades ou des crochets incompatibles ({} et \[\]), au moyen d’un linter JSON tel que Visual Studio Code.

## <a name="issues-running-an-experiment"></a>Problèmes liés à l’exécution d’une expérimentation

### <a name="the-execution-status-of-my-experiment-after-starting-is-failed"></a>L’état d’exécution de mon expérience après le démarrage est « Échec »

Dans la liste **Expériences** du Portail Azure, cliquez sur le nom de l’expérience pour voir la **Vue d’ensemble de l’expérience**. Dans la section **Historique**, cliquez sur **Détails** en regard de l’exécution de l’expérimentation en échec pour afficher des informations détaillées sur l’erreur.

![Historique des expériences](images/run-experiment-history.png)

### <a name="my-agent-based-fault-failed-with-error-verify-that-the-target-is-correctly-onboarded-and-proper-read-permissions-are-provided-to-the-experiment-msi"></a>Échec de l’erreur basée sur l’agent avec l’erreur : vérifiez que la cible est correctement intégrée et que les autorisations de lecture appropriées sont fournies à l’expérience msi.

Cela peut se produire si vous avez intégré l’agent à l’aide du Portail Azure, qui présente un problème connu : l’activation d’une cible basée sur un agent n’affecte pas l’identité gérée affectée par l’utilisateur à la machine virtuelle ou au groupe de machines virtuelles identiques.

Pour résoudre ce point, accédez à la machine virtuelle ou au groupe de machines virtuelles identiques dans le Portail Azure, accédez à **Identité**, ouvrez l’onglet **Utilisateur affecté**, puis **Ajoutez** votre identité affectée par l’utilisateur à la machine virtuelle. Une fois l’opération terminée, vous devrez peut-être redémarrer la machine virtuelle pour que l’agent se connecte.
