---
title: Créer une expérience qui utilise une erreur basée sur un agent avec Azure Chaos Studio
description: Créer une expérience qui utilise une erreur basée sur un agent et configurer l’agent de chaos
author: johnkemnetz
ms.topic: how-to
ms.date: 11/01/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 2dc71c72ebbc31af30e25834ece0e15dddcba5dc
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096895"
---
# <a name="create-a-chaos-experiment-that-uses-an-agent-based-fault-to-add-cpu-pressure-to-a-linux-vm"></a>Créer une expérience de chaos qui utilise une erreur basée sur un agent pour ajouter la sollicitation du processeur à une machine virtuelle Linux

Vous pouvez utiliser une expérience de chaos pour vérifier que votre application résiste aux défaillances en les provoquant dans un environnement contrôlé. Dans ce guide, vous allez provoquer un événement d’utilisation élevée du processeur sur une machine virtuelle Linux à l’aide d’une expérience de chaos et d’Azure Chaos Studio. L’exécution de cette expérience peut vous aider à vous défendre contre l’épuisement des ressources.

Ces mêmes étapes peuvent être utilisées pour configurer et exécuter une expérience pour toute erreur basée sur un agent. Une erreur **basée sur un agent** requiert la configuration et l’installation de l’agent de chaos, contrairement à une erreur de service direct qui s’exécute directement sur une ressource Azure sans nécessiter d’instrumentation.


## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
- Une machine virtuelle Linux. Si vous ne disposez pas d’une machine virtuelle, vous pouvez [suivre ces étapes pour en créer une](../virtual-machines/linux/quick-create-portal.md).
- Une configuration réseau qui vous permet d’[établir une connexion SSH à votre machine virtuelle](../virtual-machines/ssh-keys-portal.md)
- Identité managée affectée par l’utilisateur. Si vous n’avez pas d’identité managée affectée par l’utilisateur, vous pouvez [suivre ces étapes pour en créer une](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md)


## <a name="enable-chaos-studio-on-your-virtual-machine"></a>Activer Chaos Studio sur votre machine virtuelle

Chaos Studio ne peut pas injecter d’erreurs dans une machine virtuelle, sauf si cette dernière a d’abord été intégrée à Chaos Studio. Vous intégrez une machine virtuelle à Chaos Studio en créant une [cible et des fonctionnalités](chaos-studio-targets-capabilities.md) sur la ressource, puis en installant l’agent de chaos. Les machines virtuelles ont deux types de cibles : l’un qui active les erreurs de service direct (où aucun agent n’est requis) et l’autre qui active les erreurs basées sur un agent (ce qui requiert l’installation d’un agent). L’agent de chaos est une application installée sur votre machine virtuelle en tant qu’[extension de machine virtuelle](../virtual-machines/extensions/overview.md) qui vous permet d’injecter des erreurs dans le système d’exploitation invité.

### <a name="install-stress-ng"></a>Installer stress-ng

L’agent Chaos Studio pour Linux nécessite stress-ng, une application open source qui peut provoquer divers événements de contrainte sur une machine virtuelle. Vous pouvez installer stress-ng en vous [connectant à votre machine virtuelle Linux](../virtual-machines/ssh-keys-portal.md) et en exécutant la commande d’installation appropriée pour votre gestionnaire de package, par exemple :

```bash
sudo apt-get update && sudo apt-get -y install unzip && sudo apt-get -y install stress-ng
```

ou

```bash
sudo dnf -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm && sudo yum -y install stress-ng
```

### <a name="enable-chaos-target-capabilities-and-agent"></a>Activer la cible, les fonctionnalités et l’agent de chaos

1. Ouvrez le [portail Azure](https://portal.azure.com).
2. Recherchez **Chaos Studio (version préliminaire)** dans la barre de recherche.
3. Cliquez sur **Targets** (Cibles) et accédez à votre machine virtuelle.
![Vue des cibles dans le Portail Azure](images/tutorial-agent-based-targets.png)
4. Cochez la case affichée à côté de votre machine virtuelle et cliquez sur **Enable targets** (Activer les cibles), puis sur **Enable agent-based targets** (Activer les cibles basées sur un agent) dans le menu déroulant.
![Activation des cibles dans le Portail Azure](images/tutorial-agent-based-targets-enable.png)
5. Sélectionnez l’identité managée (**Managed Identity**) que vous allez utiliser pour authentifier l’agent de chaos et éventuellement permettre à Application Insights de voir les événements d’expérience et les journaux de l’agent.
![Sélection d’une identité managée](images/tutorial-agent-based-targets-enable-options.png)
6. Cliquez sur **Review + Enable** (Vérifier + activer), puis sur **Enable** (Activer).
![Vérification de l’activation de la cible basée sur un agent](images/tutorial-agent-based-targets-enable-review.png)
7. Après quelques minutes, une notification s’affiche, indiquant que la ou les ressources sélectionnées ont été activées correctement. Le portail Azure ajoute l’identité affectée à l’utilisateur à la machine virtuelle, active la cible et les fonctionnalités de l’agent, et installe l’agent de chaos en tant qu’extension de machine virtuelle.
![Notification indiquant que la cible a été activée correctement](images/tutorial-agent-based-targets-enable-confirm.png)

Vous avez correctement intégré votre machine virtuelle Linux à Chaos Studio. Dans la vue **Cibles**, vous pouvez également gérer les fonctionnalités activées sur cette ressource. En cliquant sur le lien **Gérer les actions** à côté d’une ressource, vous affichez les fonctionnalités activées pour cette ressource.

## <a name="create-an-experiment"></a>Créer une expérience
Maintenant que votre machine virtuelle est intégrée, vous pouvez créer votre expérience. Une expérience de chaos définit les actions que vous souhaitez effectuer sur les ressources cibles, organisées en étapes, qui s’exécutent de manière séquentielle, et les branches qui s’exécutent en parallèle.

1. Cliquez sur l’onglet **Expériences** dans Chaos Studio. Dans cette vue, vous pouvez afficher et gérer toutes vos expériences de chaos. Cliquez sur **Ajouter une expérience**
![Vue des expériences dans le Portail Azure](images/tutorial-agent-based-add.png)
2. Renseignez les champs **Abonnement**, **Groupe de ressources** et **Emplacement**, là où vous souhaitez déployer l’expérience de chaos. Donnez un **nom** à votre expérience. Cliquez sur **Suivant : concepteur d’expérience >** 
![Ajout des détails de l’expérience de base](images/tutorial-agent-based-add-basics.png)
3. Vous êtes maintenant dans le concepteur d’expérience Chaos Studio. Le concepteur d’expérience vous permet de créer votre expérience en ajoutant des étapes, des branches et des erreurs. Donnez un nom convivial à votre **étape** et votre **branche**, puis cliquez sur **Ajouter une erreur**.
![Concepteur d’expériences](images/tutorial-agent-based-add-designer.png)
4. Sélectionnez **CPU Pressure** (Sollicitation du processeur) dans la liste déroulante, puis spécifiez une valeur **Duration** pour la durée d’application de la sollicitation, en minutes, et une valeur **pressureLevel** pour la quantité de sollicitation du processeur à appliquer. Laissez **virtualMachineScaleSetInstances** vide. Cliquez sur **Suivant : ressources cibles >** 
![Propriétés d’erreur](images/tutorial-agent-based-add-fault.png)
5. Sélectionnez votre machine virtuelle, puis cliquez sur **Suivant**
![Ajouter une cible](images/tutorial-agent-based-add-targets.png)
6. Vérifiez que votre expérience semble correcte, puis cliquez sur **Vérifier + créer**, puis sur **Créer.** 
![Vérifier et créer une expérience](images/tutorial-agent-based-add-review.png)

## <a name="give-experiment-permission-to-your-virtual-machine"></a>Accorder une autorisation d’expérience à votre machine virtuelle
Lorsque vous créez une expérience de chaos, Chaos Studio crée une identité managée affectée par le système qui exécute des erreurs sur vos ressources cibles. Cette identité doit avoir les [autorisations appropriées](chaos-studio-fault-providers.md) sur la ressource cible pour que l’expérience s’exécute correctement.

1. Accédez à votre machine virtuelle, puis cliquez sur **Contrôle d’accès (IAM).** .
![Page de vue d’ensemble de la machine virtuelle](images/tutorial-agent-based-access-resource.png)
2. Cliquez sur **Ajouter**, puis sur **Ajouter une attribution de rôle**.
![Présentation du contrôle d’accès](images/tutorial-agent-based-access-iam.png)
3. Recherchez **Lecteur**, puis sélectionnez le rôle. Cliquez sur **Suivant**
![Attribution du rôle Contributeur de machines virtuelles](images/tutorial-agent-based-access-role.png)
4. Cliquez sur **Sélectionner des membres** et recherchez le nom de votre expérience. Sélectionnez votre expérience, puis cliquez sur **Sélectionner**. S’il existe plusieurs expériences dans le même locataire avec le même nom, le nom de votre expérience sera tronqué avec des caractères aléatoires ajoutés.
![Ajout de l’expérience au rôle](images/tutorial-agent-based-access-experiment.png)
5. Cliquez sur **Vérifier + attribuer**, puis sur **Vérifier + attribuer**.

## <a name="run-your-experiment"></a>Exécuter votre expérience
Vous pouvez désormais exécuter votre service. Pour voir l’impact, nous vous recommandons d’ouvrir [un graphique des métriques Azure Monitor](../azure-monitor/essentials/tutorial-metrics-explorer.md) avec la sollicitation du processeur de votre machine virtuelle affichée sous un onglet de navigateur distinct.

1. Dans **Expériences**, cliquez sur votre expérience, sur **Démarrer**, puis sur **OK**.
![Démarrage de l’expérience](images/tutorial-agent-based-start.png)
2. Lorsque l'**État** passe à **En cours d’exécution**, cliquez sur **Détails** pour la dernière exécution sous **Historique** pour afficher les détails de l’expérience en cours d’exécution.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez exécuté une expérience basée sur un agent, vous êtes prêt à effectuer les opérations suivantes :
- [Créer une expérience qui utilise des erreurs de service direct](chaos-studio-tutorial-service-direct.md)
- [Choisir votre expérience](chaos-studio-run-experiment.md)
