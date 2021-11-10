---
title: Activer l’extension de machine virtuelle à partir du portail Azure
description: Cet article explique comment déployer des extensions de machine virtuelle sur des serveurs avec Azure Arc qui s’exécutent dans des environnements cloud hybrides à partir du portail Azure.
ms.date: 10/15/2021
ms.topic: conceptual
ms.openlocfilehash: b3bb6e6b6dcb70dc229dc01a02d04cf195c89700
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131423904"
---
# <a name="enable-azure-vm-extensions-from-the-azure-portal"></a>Activer les extensions de machine virtuelle Azure à partir du portail Azure

Cet article explique comment déployer, mettre à jour et désinstaller des extensions de machine virtuelle Azure prises en charge par les serveurs avec Azure Arc, sur une machine hybride Linux ou Windows à l’aide du portail Azure.

> [!NOTE]
> L’extension de machine virtuelle Key Vault ne prend pas en charge le déploiement à partir du portail Azure, uniquement à l’aide de l’interface Azure CLI, d’Azure PowerShell ou d’un modèle Azure Resource Manager.

> [!NOTE]
> Les serveurs avec Azure Arc ne prennent pas en charge le déploiement ni la gestion des extensions de machine virtuelle sur les machines virtuelles Azure. Pour les machines virtuelles Azure, consultez l’article [Vue d’ensemble de l’extension de machine virtuelle](../../virtual-machines/extensions/overview.md) suivant.

## <a name="enable-extensions"></a>Activer les extensions

Les extensions de machine virtuelle peuvent être appliquées à votre machine managée par serveur avec Azure Arc via le portail Azure.

1. À partir de votre navigateur, accédez au [portail Azure](https://portal.azure.com).

2. Sur le portail, accédez à **Serveurs – Azure Arc**, puis sélectionnez votre machine hybride dans la liste.

3. Choisissez **Extensions**, puis sélectionnez **Ajouter**. Choisissez l’extension souhaitée dans la liste des extensions disponibles, puis suivez les instructions de l’Assistant. Dans cet exemple, nous allons déployer l’extension de machine virtuelle Log Analytics.

    ![Sélectionner l’extension de machine virtuelle pour la machine sélectionnée](./media/manage-vm-extensions/add-vm-extensions.png)

    L’exemple suivant illustre l’installation de l’extension de machine virtuelle Log Analytics à partir du portail Azure :

    ![Installer l’extension de machine virtuelle Log Analytics](./media/manage-vm-extensions/mma-extension-config.png)

    Pour mener à bien l’installation, il vous est demandé de fournir l’ID et la clé primaire de l’espace de travail. Si vous ne savez pas comment vous procurer ces informations, consultez [Obtenir l’ID et la clé d’espace de travail](../../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

4. Après avoir confirmé les informations requises fournies, sélectionnez **Vérifier + créer**. Dans le résumé du déploiement qui s’affiche, examinez l’état du déploiement.

>[!NOTE]
>Même si plusieurs extensions peuvent être regroupées et traitées ensemble, elles sont installées en série. Une fois l’installation de la première extension terminée, l’installation de la suivante est entreprise.

## <a name="list-extensions-installed"></a>Lister les extensions installées

Vous pouvez obtenir la liste des extensions de machine virtuelle sur votre serveur avec Azure Arc à partir du portail Azure. Pour les voir, procédez comme suit.

1. À partir de votre navigateur, accédez au [portail Azure](https://portal.azure.com).

2. Sur le portail, accédez à **Serveurs – Azure Arc**, puis sélectionnez votre machine hybride dans la liste.

3. Choisissez **Extensions** pour obtenir la liste des extensions installées.

    :::image type="content" source="media/manage-vm-extensions/list-vm-extensions.png" alt-text="Répertorier l’extension de machine virtuelle déployée sur la machine sélectionnée" border="true":::

## <a name="upgrade-extensions"></a>Mettre à niveau les extensions

Quand une nouvelle version d’une extension prise en charge est publiée, vous pouvez mettre à niveau l’extension vers cette dernière version. Les serveurs avec Azure Arc présentent une bannière dans le portail Azure quand vous accédez à des serveurs avec Azure Arc. Elle vous informe que des mises à jour sont disponibles pour une ou plusieurs extensions installées sur une machine. Quand vous affichez la liste des extensions installées pour un serveur avec Azure Arc sélectionné, vous remarquez une colonne intitulée **Mise à jour disponible**. Si une version plus récente d’une extension est publiée, la colonne **Mise à jour disponible** pour cette extension indique **Oui**.

>[!NOTE]
>Le portail Azure utilise actuellement le terme **Mise à jour** pour cette expérience. Toutefois, ce mot ne décrit pas précisément cette l’opération. Les extensions sont mises à niveau en installant une version plus récente de l’extension actuellement installée sur la machine ou le serveur.

La mise à niveau d’une extension vers la version la plus récente n’affecte pas la configuration de cette extension. Vous n’êtes pas obligé de redéfinir les informations de configuration des extensions que vous mettez à niveau.

:::image type="content" source="media/manage-vm-extensions-portal/vm-extensions-update-status.png" alt-text="Lister l’état de mise à jour des extensions de machine virtuelle" border="true":::

Vous pouvez mettre à niveau une extension ou sélectionner plusieurs extensions éligibles pour une mise à niveau à partir du portail Azure en procédant comme suit.

> [!NOTE]
> Actuellement, vous pouvez uniquement mettre à niveau les extensions à partir du portail Azure. La réalisation de cette opération dans l’interface Azure CLI ou à l’aide d’un modèle Azure Resource Manager n’est pas prise en charge pour l’instant.

1. À partir de votre navigateur, accédez au [portail Azure](https://portal.azure.com).

2. Sur le portail, accédez à **Serveurs – Azure Arc**, puis sélectionnez votre machine hybride dans la liste.

3. Choisissez **Extensions** et passez en revue l’état des extensions sous la colonne **Mise à jour disponible**.

Vous pouvez mettre à niveau une extension de trois manières :

* En sélectionnant une extension dans la liste des extensions installées et, sous les propriétés de l’extension, en sélectionnant l’option **Mettre à jour**.

    :::image type="content" source="media/manage-vm-extensions-portal/vm-extensions-update-from-extension.png" alt-text="Mettez à niveau l’extension à partir de l’extension sélectionnée." border="true":::

* En sélectionnant l’extension dans la liste des extensions installées et en sélectionnant l’option **Mettre à jour** en haut de la page.

* En sélectionnant une ou plusieurs extensions éligibles pour une mise à niveau dans la liste des extensions installées, puis en sélectionnant l’option **Mettre à jour**.

    :::image type="content" source="media/manage-vm-extensions-portal/vm-extensions-update-selected.png" alt-text="Mettre à jour l’extension sélectionnée" border="true":::

## <a name="remove-extensions"></a>Supprimer des extensions

Vous pouvez supprimer une ou plusieurs extensions d’un serveur avec Azure Arc à partir du portail Azure. Ensuite, effectuez les opérations suivantes pour supprimer une extension.

1. À partir de votre navigateur, accédez au [portail Azure](https://portal.azure.com).

2. Sur le portail, accédez à **Serveurs – Azure Arc**, puis sélectionnez votre machine hybride dans la liste.

3. Choisissez **Extensions**, puis sélectionnez une extension dans la liste des extensions installées.

4. Sélectionnez **Désinstaller**, puis, lorsque vous êtes invité à vérifier, sélectionnez **Oui** pour continuer.

## <a name="next-steps"></a>Étapes suivantes

- Vous pouvez déployer, gérer et supprimer des extensions de machine virtuelle à l’aide d’[Azure CLI](manage-vm-extensions-cli.md), de [PowerShell](manage-vm-extensions-powershell.md) ou de [modèles Azure Resource Manager](manage-vm-extensions-template.md).

- Des informations de dépannage sont accessibles dans le [guide Résoudre les problèmes liés aux extensions de machine virtuelle](troubleshoot-vm-extensions.md).
