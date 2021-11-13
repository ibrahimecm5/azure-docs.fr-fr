---
title: Configurer l’arrêt automatique des machines virtuelles dans Azure Lab Services
description: Cet article explique comment configurer l’arrêt automatique des machines virtuelles dans le compte lab.
ms.topic: how-to
ms.date: 08/17/2020
ms.openlocfilehash: 40b4f3971d2fd9fc84337bb38d49d04212b3f5ba
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130228522"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab-account"></a>Configurer l’arrêt automatique des machines virtuelles pour un compte lab

Vous pouvez activer plusieurs fonctionnalités de contrôle des coûts d’arrêt automatique afin d’éviter de manière proactive les coûts supplémentaires lorsque les machines virtuelles ne sont pas utilisées activement. La combinaison des trois fonctionnalités suivantes d’arrêt et de déconnexion automatiques permet d’intercepter la plupart des cas où les utilisateurs laissent accidentellement leurs machines virtuelles en marche :
 
- Déconnectez automatiquement les utilisateurs des machines virtuelles que le système d’exploitation juge inactives.
- Arrêt automatique des machines virtuelles quand les utilisateurs se déconnectent.
- Arrêter automatiquement les machines virtuelles démarrées, mais auxquelles les utilisateurs ne se connectent pas.

Pour plus d’informations sur les fonctionnalités d’arrêt automatique, consultez la section [Optimiser le contrôle des coûts à l’aide des paramètres d’arrêt automatique](cost-management-guide.md#automatic-shutdown-settings-for-cost-control).

> [!IMPORTANT]
> Les labs Linux prennent uniquement en charge l’arrêt automatique quand les utilisateurs se déconnectent et quand les machines virtuelles sont démarrées sans que les utilisateurs s’y connectent.  La prise en charge varie aussi en fonction des [distributions et versions spécifiques de Linux](../virtual-machines/extensions/diagnostics-linux.md#supported-linux-distributions).  Les paramètres d’arrêt ne sont pas pris en charge par l’image [Data Science Virtual Machine-Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804). 

## <a name="enable-automatic-shutdown"></a>Activer l’arrêt automatique

1. Dans le [portail Azure](https://portal.azure.com/), accédez à la page **Compte lab**.
1. Sélectionnez **Paramètres des labs** dans le menu de gauche.
1. Sélectionnez le ou les paramètres d’arrêt automatique appropriés pour votre scénario.  

    > [!div class="mx-imgBorder"]
    > ![Paramètre d’arrêt automatique au niveau du compte Lab](./media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)
    
    Ces paramètres s’appliquent à tous les labs créés dans le compte lab. Un créateur de laboratoire (enseignant) peut remplacer ce paramètre au niveau du laboratoire. La modification apportée à ce paramètre au niveau du compte Lab n’affecte que les laboratoires créés après la modification.

    Pour désactiver les paramètres, décochez-en les cases sur cette page.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la façon dont un propriétaire de lab peut configurer ou remplacer ce paramètre au niveau du lab, consultez [Configurer l’arrêt automatique des machines virtuelles pour un lab](how-to-enable-shutdown-disconnect.md).
