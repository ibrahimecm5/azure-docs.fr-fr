---
title: Connecter vos machines non Azure à Microsoft Defender pour le cloud
description: Découvrez comment connecter vos machines non Azure à Microsoft Defender pour le cloud
author: memildin
ms.author: memildin
ms.date: 07/12/2021
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
zone_pivot_groups: non-azure-machines
ms.openlocfilehash: 390a628640389b2d2b89afbf36b4aab41f1eb115
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131422308"
---
# <a name="connect-your-non-azure-machines-to-microsoft-defender-for-cloud"></a>Connecter vos machines non Azure à Microsoft Defender pour le cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Defender pour le cloud peut superviser la posture de sécurité de vos ordinateurs non Azure, mais vous devez commencer par les connecter à Azure.

Vous pouvez connecter vos ordinateurs autres qu’Azure de l’une des manières suivantes :

- Utilisation de serveurs avec Azure Arc (**recommandé**)
- À partir des pages Defender pour le cloud du portail Azure (**Prise en main** et **Inventaire**)

Les deux méthodes sont décrites dans cette page.

> [!TIP]
> Si vous connectez des machines à partir d’autres fournisseurs de cloud, consultez [Connecter vos comptes AWS](quickstart-onboard-aws.md) ou [Connecter vos comptes GCP](quickstart-onboard-gcp.md).

::: zone pivot="azure-arc"

## <a name="add-non-azure-machines-with-azure-arc"></a>Ajouter des machines autres qu’Azure avec Azure Arc

Le meilleur moyen d’ajouter vos ordinateurs non Azure à Microsoft Defender pour le cloud est d’utiliser des [serveurs avec Azure Arc](../azure-arc/servers/overview.md).

Une machine qui comprend des serveurs avec Azure Arc devient une ressource Azure. Quand vous avez installé l’agent Log Analytics sur celle-ci, elle apparaît dans Defender pour le cloud avec des recommandations comme vos autres ressources Azure.

De plus, les serveurs avec Azure Arc offrent des fonctionnalités améliorées, comme l’option permettant d’activer des stratégies de configuration des invités sur la machine, de simplifier le déploiement avec d’autres services Azure et plus encore. Pour une présentation des avantages, consultez [Scénarios pris en charge](../azure-arc/servers/overview.md#supported-cloud-operations).

> [!NOTE]
> Les outils de déploiement automatique de Defender pour le cloud pour le déploiement de l’agent Log Analytics ne prennent pas en charge les machines qui exécutent Azure Arc. Une fois que vous avez connecté vos machines à l’aide d’Azure Arc, utilisez la recommandation de Defender pour le cloud pour déployer l’agent et tirer parti de l’ensemble des protections proposées par Defender pour le cloud :
>
> - [L’agent Log Analytics doit être installé sur vos machines Azure Arc Linux](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/720a3e77-0b9a-4fa9-98b6-ddf0fd7e32c1)
> - [L’agent Log Analytics doit être installé sur vos machines Azure Arc Windows](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/27ac71b1-75c5-41c2-adc2-858f5db45b08)

Découvrez-en plus sur les [serveurs avec Azure Arc](../azure-arc/servers/overview.md).

**Pour déployer Azure Arc :**

- Pour une seule machine, suivez les instructions fournies dans [Démarrage rapide : Connecter des machines hybrides à l’aide de serveurs avec Azure Arc](../azure-arc/servers/learn/quick-enable-hybrid-vm.md).
- Pour connecter à grande échelle plusieurs machines à des serveurs avec Azure Arc, consultez [Connecter des machines hybrides à Azure à grande échelle](../azure-arc/servers/onboard-service-principal.md)

> [!TIP]
> Si vous intégrez des machines exécutées sur Amazon Web Services (AWS), le connecteur Defender pour le cloud AWS gère de façon transparente le déploiement d’Azure Arc. Pour en savoir plus, consultez [Connecter vos comptes AWS à Microsoft Defender pour le cloud](quickstart-onboard-aws.md).

::: zone-end

::: zone pivot="azure-portal"

## <a name="add-non-azure-machines-from-the-azure-portal"></a>Ajouter des machines non Azure à partir du portail Azure

1. Dans le menu de Defender pour le cloud, ouvrez la page **Prise en main**.
1. Sélectionnez l’onglet **Prise en main**.
1. Sous **Ajouter des serveurs non-Azure**, sélectionnez **Configurer** .

    :::image type="content" source="./media/quickstart-onboard-machines/onboarding-get-started-tab.png" alt-text="Onglet Prise en main de la page Mise en route." lightbox="./media/quickstart-onboard-machines/onboarding-get-started-tab.png":::

    > [!TIP]
    > Vous pouvez également ajouter des ordinateurs à partir de la page **Inventaire**, en cliquant sur le bouton **Ajouter des serveurs non-Azure**.
    > 
    > :::image type="content" source="./media/quickstart-onboard-machines/onboard-inventory.png" alt-text="Ajouter des machines autres qu’Azure à partir de la page d’inventaire des ressources.":::

    Une liste de vos espaces de travail Log Analytics apparaît. Elle comprend, le cas échéant, l’espace de travail par défaut qui a été créé automatiquement par Defender pour le cloud lors de l’activation du provisionnement automatique. Sélectionnez cet espace de travail ou un autre espace de travail à utiliser.

    Vous pouvez ajouter des ordinateurs à un espace de travail existant ou créer un espace de travail.

1. Vous pouvez également créer un espace de travail en sélectionnant **Créer un espace de travail**.

1. Dans la liste des espaces de travail, sélectionnez **Ajouter des serveurs** pour l’espace de travail approprié.
    La page **Gestion des agents** apparaît.

    À partir de là, choisissez la procédure appropriée ci-dessous en fonction du type de machine que vous intégrez :

    - [Intégrer vos machines virtuelles Azure Stack Hub](#onboard-your-azure-stack-hub-vms)
    - [Intégrer vos machines Linux](#onboard-your-linux-machines)
    - [Intégrer vos machines Windows](#onboard-your-windows-machines)

### <a name="onboard-your-azure-stack-hub-vms"></a>Intégrer vos machines virtuelles Azure Stack Hub

Vous avez besoin des informations de la page **Gestion des agents** pour ajouter des machines virtuelles Azure Stack Hub et pour configurer l’extension de machine virtuelle **Azure Monitor, Update and Configuration Management** sur les machines virtuelles s’exécutant sur votre instance Azure Stack Hub.

1. Dans la page **Gestion des agents**, copiez **ID de l’espace de travail** et **Clé primaire** dans le Bloc-notes.
1. Connectez-vous à votre portail **Azure Stack Hub** et ouvrez la page **Machines virtuelles**.
1. Sélectionnez la machine virtuelle que vous souhaitez protéger avec Defender pour le cloud.
    >[!TIP]
    > Pour plus d’informations sur la façon de créer une machine virtuelle sur Azure Stack Hub, consultez [ce guide de démarrage rapide pour les machines virtuelles Windows](/azure-stack/user/azure-stack-quick-windows-portal) ou [ce guide de démarrage rapide pour les machines virtuelles Linux](/azure-stack/user/azure-stack-quick-linux-portal).
1. Sélectionnez **Extensions**. La liste des extensions de machine virtuelle installées sur cette machine virtuelle s’affiche.
1. Sélectionnez l’onglet **Ajouter**. Le menu **Nouvelle ressource** affiche la liste des extensions de machine virtuelle disponibles.
1. Sélectionnez l’extension **Azure Monitor, Update and Configuration Management**, puis sélectionnez **Créer**. La page de configuration **Installer l’extension** s’ouvre.
    >[!NOTE]
    > Si vous ne voyez pas l’extension **Azure Monitor, Update and Configuration Management** dans votre place de marché, contactez votre opérateur Azure Stack Hub pour qu’il la rendre disponible.
1. Dans la page de configuration **Installer l’extension**, collez l’**ID de l’espace de travail** et la **Clé de l’espace de travail (clé primaire)** que vous avez copiés dans le Bloc-notes au cours de l’étape précédente.
1. Sélectionnez **OK** pour achever la configuration. L’état de l’extension indique **Approvisionnement réussi**. Il peut s’écouler une heure avant que la machine virtuelle n’apparaisse dans Defender pour le cloud.

### <a name="onboard-your-linux-machines"></a>Intégrer vos machines Linux

Pour ajouter des machines Linux, vous avez besoin de la commande WGET de la page **Gestion des agents**.

1. Depuis la page **Gestion des agents**, copiez la commande **WGET** dans le Bloc-notes. Enregistrez ce fichier dans un emplacement accessible à partir de votre ordinateur Linux.
1. Sur votre ordinateur Linux, ouvrez le fichier avec la commande WGET. Sélectionnez tout le contenu, copiez-le, puis collez-le dans une console de terminal.
1. Une fois l’installation terminée, vous pouvez vérifier que *omsagent* est installé en exécutant la commande *pgrep*. La commande retourne l’ID de processus (PID) *omsagent*.
    Les journaux de l’agent se trouvent à l’emplacement suivant : */var/opt/microsoft/omsagent/\<workspace id>/log/* . 30 minutes peuvent s’écouler avant que la nouvelle machine Linux n’apparaisse dans Defender pour le cloud.

### <a name="onboard-your-windows-machines"></a>Intégrer vos machines Windows

Vous avez besoin des informations de la page **Gestion des agents** pour ajouter des machines Windows et télécharger le fichier d’agent approprié (32/64 bits).
1. Cliquez sur le lien **Télécharger l’Agent Windows** correspondant au type de processeur de votre ordinateur pour télécharger le fichier d’installation.
1. Dans la page **Gestion des agents**, copiez **ID de l’espace de travail** et **Clé primaire** dans le Bloc-notes.
1. Copiez le fichier d’installation téléchargé sur l’ordinateur cible, puis exécutez-le.
1. Suivez les instructions de l’Assistant d’installation (**Suivant**, **J’accepte**, **Suivant**, **Suivant**).
    1. Dans la page **Azure Log Analytics**, collez les valeurs **ID de l’espace de travail** et **Clé de l’espace de travail (clé primaire)** que vous avez copiées dans le Bloc-notes.
    1. Si l’ordinateur doit rendre compte à un espace de travail Log Analytics dans le cloud Azure Government, sélectionnez **Azure US Government** dans la liste déroulante **Cloud Azure**.
    1. Si l’ordinateur a besoin de communiquer avec le service Log Analytics par le biais d’un serveur proxy, sélectionnez **Avancé**, puis indiquez l’URL et le numéro de port du serveur proxy.
    1. Une fois que vous avez entré tous les paramètres de configuration, sélectionnez **Suivant**.
    1. Sur la page **Prêt à installer**, passez en revue les paramètres à appliquer, puis sélectionnez **Installer**.
    1. Sur la page **Configuration effectuée**, sélectionnez **Terminer**.

Lorsque vous avez terminé, **Microsoft Monitoring Agent** apparaît dans le **Panneau de configuration**. Vous pouvez vérifier votre configuration et vous assurer que l’agent est connecté.

Pour plus d’informations sur l’installation et la configuration de l’agent, consultez la page [Connecter des machines Windows](../azure-monitor/agents/agent-windows.md#install-agent-using-setup-wizard).

::: zone-end

## <a name="verifying"></a>Vérification

Félicitations ! Vous pouvez maintenant afficher vos machines Azure et non-Azure au même endroit. Ouvrez la [page d’inventaire des ressources](asset-inventory.md) et filtrez les types de ressources appropriés. Ces icônes distinguent les différents types :

  ![Icône ASC pour machine autre qu’Azure](./media/quickstart-onboard-machines/security-center-monitoring-icon1.png) Machine non-Azure

  ![Icône ASC pour machine Azure](./media/quickstart-onboard-machines/security-center-monitoring-icon2.png) Azure VM

  ![Icône ASC pour serveur Azure Arc](./media/quickstart-onboard-machines/arc-enabled-machine-icon.png) Serveur avec Azure Arc

## <a name="next-steps"></a>Étapes suivantes

Cette page vous a montré comment ajouter vos machines non Azure à Microsoft Defender pour le cloud. Pour surveiller leur état, utilisez les outils d’inventaire, comme expliqué à la page suivante :

- [Explorer et gérer vos ressources avec l’inventaire des ressources](asset-inventory.md)
