---
title: Mise à niveau automatique des extensions (préversion) pour serveurs avec Azure Arc
description: Découvrez comment activer la Mise à niveau automatique de des extension (préversion) pour vos serveurs avec Azure Arc.
ms.topic: conceptual
ms.date: 11/06/2021
ms.openlocfilehash: aea00dbe8609d0a8481d9ca742c12e6d0215f189
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132033761"
---
# <a name="automatic-extension-upgrade-preview-for-azure-arc-enabled-servers"></a>Mise à niveau automatique des extensions (préversion) pour serveurs avec Azure Arc

La Mise à niveau automatique des extension (préversion) est disponible pour des serveurs avec Azure Arc qui on pris en charge des extensions de machine virtuelle installées. Lorsque la Mise à niveau automatique des extensions (préversion) est activée sur une machine, l’extension est automatiquement mise à niveau chaque fois que l’éditeur de l’extension publie une nouvelle version pour cette extension.

 La mise à niveau automatique des extensions présente les fonctionnalités suivantes :

- Vous pouvez activer et désactiver les mises à niveau automatiques à tout moment.
- Chaque extension prise en charge est inscrite individuellement, et vous pouvez choisir les extensions à mettre à niveau automatiquement.
- Prise en charge dans toutes les régions du cloud public.

> [!NOTE]
> Dans cette version, seule l’interface de ligne de commande Azure est prise en charge pour configurer la Mise à niveau automatique des extensions.

## <a name="how-does-automatic-extension-upgrade-work"></a>Comment fonctionne la mise à niveau automatique des extensions ?

Le processus de mise à niveau d’extension remplace la version existante de l’extension de machine virtuelle Azure prise en charge par les serveurs avec Azure Arc, par une nouvelle version de la même extension lors de la publication de celle-ci par l’éditeur d’extension.

Une mise à jour d’extension qui a échoué fait automatiquement l’objet d’une nouvelle tentative. Une nouvelle tentative est effectuée automatiquement à quelques jours d’intervalle, sans intervention de l’utilisateur.

### <a name="availability-first-updates"></a>Mises à jour selon la première disponibilité

Le modèle de première disponibilité pour les mises à jour orchestrées de la plateforme garantit que les configurations de disponibilité dans Azure sont respectées sur plusieurs niveaux de disponibilité.

Pour un groupe de serveurs avec Arc en cours de mise à jour, la plateforme Azure orchestre les mises à jour en suivant le modèle décrit dans la [Mise à niveau de l’extension Automation](../../virtual-machines/automatic-extension-upgrade.md#availability-first-updates). Toutefois, il existe des différences notables entre les serveurs avec Arc et les machines virtuelles Azure :

**Entre les régions :**

- Les régions appairées géographiquement ne sont pas applicables.

**Dans une région :**

- Les zones de disponibilité ne sont pas applicables.
- Les machines sont regroupées par lot au mieux pour éviter les mises à jour simultanées de toutes les machines inscrites auprès de serveurs avec Arc dans un abonnement.  

## <a name="supported-extensions"></a>Extensions prises en charge

La Mise à niveau automatique des extensions (préversion) prend en charge les extensions suivantes (d’autres étant ajoutées régulièrement) :

- Agent Azure Monitor – Linux et Windows
- Agent de sécurité Azure – Linux et Windows
- Dependency Agent – Linux et Windows
- Extension Key Vault – Linux uniquement
- Agent de Log Analytics (agent OMS) – Linux uniquement

## <a name="enabling-automatic-extension-upgrade-preview"></a>Activation de la Mise à niveau automatique des extensions (préversion)

Pour activer la Mise à niveau automatique des extensions (préversion) pour une extension, vous devez vous assurer que la propriété `enable-auto-upgrade` est définie sur `true` et ajoutée à chaque définition d’extension individuellement.

Utilisez la cmdlet [az connectedmachine extension](/cli/azure/connectedmachine/extension) avec les paramètres `--name`, `--machine-name`, `--enable-auto-upgrade` et `--resource-group`.

```azurecli
az connectedmachine extension update \
    --resource-group resourceGroupName \
    --machine-name machineName \
    --name DependencyAgentLinux \
    --enable-auto-upgrade true
```

Pour vérifier l’état de la Mise à niveau automatique des extensions (préversion) pour toutes les extensions sur un serveur avec Arc, exécutez la commande suivante :

```azurecli
az connectedmachine extension list --resource-group resourceGroupName --machine-name machineName --query "[].{Name:name, AutoUpgrade:properties.enableAutoUpgrade}" --output table
```

## <a name="extension-upgrades-with-multiple-extensions"></a>Mises à niveau d’extension avec plusieurs extensions

Une machine gérée par des serveurs avec Arc peut avoir plusieurs extensions pour lesquelles la mise à niveau automatique est activée. La même machine peut également avoir d’autres extensions pour lesquelles la mise à niveau automatique n’est pas activée.

Si plusieurs mises à niveau d’extension sont disponibles pour une machine, il est possible de les regrouper par lot, mais chaque mise à niveau d’extension est appliquée individuellement sur une machine. Un échec sur une extension n’a aucun impact sur les autres extensions à mettre à niveau. Par exemple, si deux extensions sont planifiées pour une mise à niveau et que la mise à niveau de la première extension échoue, la deuxième extension sera quand même mise à niveau.

## <a name="disable-automatic-extension-upgrade"></a>Désactiver la Mise à niveau automatique des extensions

Pour désactiver la Mise à niveau automatique des extensions (préversion) pour une extension, vous devez vous assurer que la propriété `enable-auto-upgrade` est définie sur `false` et ajoutée à chaque définition d’extension individuellement.

### <a name="using-the-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure (CLI)

Utilisez la cmdlet [az connectedmachine extension](/cli/azure/connectedmachine/extension) avec les paramètres `--name`, `--machine-name`, `--enable-auto-upgrade` et `--resource-group`.

```azurecli
az connectedmachine extension update \
    --resource-group resourceGroupName \
    --machine-name machineName \
    --name DependencyAgentLinux \
    --enable-auto-upgrade false
```

## <a name="next-steps"></a>Étapes suivantes

- Vous pouvez déployer, gérer et supprimer des extensions de machine virtuelle à l’aide d’[Azure CLI](manage-vm-extensions-cli.md), de [PowerShell](manage-vm-extensions-powershell.md) ou de [modèles Azure Resource Manager](manage-vm-extensions-template.md).

- Des informations de dépannage sont accessibles dans le [guide Résoudre les problèmes liés aux extensions de machine virtuelle](troubleshoot-vm-extensions.md).
