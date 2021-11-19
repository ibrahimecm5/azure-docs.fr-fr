---
title: Utiliser l’historique des versions de l’extension Azure Desired State Configuration
description: Cet article présente des informations sur l’historique des versions de l’extension DSC (Desired State Configuration) dans Azure.
ms.date: 02/17/2021
keywords: dsc, powershell, azure, extension
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.openlocfilehash: 3aaadbb6f972838984f4ec01347537f21e244f14
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491466"
---
# <a name="work-with-azure-desired-state-configuration-extension-version-history"></a>Utiliser l’historique des versions de l’extension Azure Desired State Configuration

L’[extension](../virtual-machines/extensions/dsc-overview.md) de machine virtuelle Azure DSC (Desired State Configuration) est mise à jour en fonction des besoins pour prendre en charge les améliorations et les nouvelles fonctionnalités fournies par Azure, par Windows Server et par Windows Management Framework qui inclut Windows PowerShell.

> [!NOTE]
> Avant d’activer l’extension DSC, nous aimerions que vous sachiez qu’une version plus récente de DSC est désormais disponible en version préliminaire, gérée par une fonctionnalité d’Azure Policy nommée [configuration invité](../governance/policy/concepts/guest-configuration.md). Le service de configuration invité combine les fonctionnalités d’Extension DSC, d’Azure Automation State Configuration, ainsi que les fonctionnalités les plus couramment demandées par les commentaires des clients. La configuration invité comprend également une prise en charge d’ordinateur hybride via des [serveurs avec Arc](../azure-arc/servers/overview.md).

Cet article fournit des informations sur chaque version de l’extension de machine virtuelle Azure DSC et sur les environnements qu’elle prend en charge. Il contient aussi des commentaires et des remarques sur les nouvelles fonctionnalités ou sur les changements.

## <a name="latest-version"></a>Version la plus récente

### <a name="version-283"></a>Version 2.83

- **Date de publication :**
  - Février 2021
- **Prise en charge du système d’exploitation :**
  - Windows Server 2019
  - Windows Server 2016
  - Windows Server 2012 R2
  - Windows Server 2012
  - Windows Server 2008 R2 SP1
  - Windows Client 7/8.1/10
  - Nano Server
- **Prise en charge de WMF :**
  - WMF 5.1
  - WMF 5.0 RTM
  - WMF 4.0 Update
  - WMF 4.0
- **Environment :**
  - Azure
  - Azure Chine Vianet 21
  - Azure Government
- **Remarque :** Cette version comprend un correctif pour les fichiers binaires non signés avec l’extension de machine virtuelle Windows.

### <a name="version-280"></a>Version 2.80

- **Date de publication :**
  - 26 septembre, Sep-2019 (Azure) | 6 juillet 2020 (Azure China 21Vianet) | 20 juillet 2020 (Azure Government)
- **Prise en charge du système d’exploitation :**
  - Windows Server 2019
  - Windows Server 2016
  - Windows Server 2012 R2
  - Windows Server 2012
  - Windows Server 2008 R2 SP1
  - Windows Client 7/8.1/10
  - Nano Server
- **Prise en charge de WMF :**
  - WMF 5.1
  - WMF 5.0 RTM
  - WMF 4.0 Update
  - WMF 4.0
- **Environment :**
  - Azure
  - Azure Chine Vianet 21
  - Azure Government
- **Remarques :** Aucune nouvelle fonctionnalité n’est incluse dans cette version.

### <a name="version-276"></a>Version 2.76

- **Date de publication :**
  - 9 mai 2018 (Azure) | 21 juin 2018 (Azure Chine Vianet 21, Azure Government)
- **Prise en charge du système d’exploitation :**
  - Windows Server 2016
  - Windows Server 2012 R2
  - Windows Server 2012
  - Windows Server 2008 R2 SP1
  - Windows Client 7/8.1/10
  - Nano Server
- **Prise en charge de WMF :**
  - WMF 5.1
  - WMF 5.0 RTM
  - WMF 4.0 Update
  - WMF 4.0
- **Environment :**
  - Azure
  - Azure Chine Vianet 21
  - Azure Government
- **Remarques :** Cette version utilise DSC telle qu’elle est incluse dans Windows Server 2016 ; pour les autres systèmes d’exploitation Windows, elle installe [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (l’installation de WMF nécessite un redémarrage). Pour Nano Server, le rôle DSC est installé sur la machine virtuelle.
- **Nouvelles fonctionnalités :**
  - Amélioration des métadonnées d’extension pour le sous-état et autres correctifs de bogues mineurs.

## <a name="supported-versions"></a>Versions prises en charge

> [!WARNING]
> Les versions 2.4 à 2.13 utilisent WMF 5.0 en préversion publique, dont les certificats de signature ont expiré en août 2016.
> Pour plus d’informations sur ce problème, consultez cet [article de blog](https://devblogs.microsoft.com/powershell/azure-dsc-extension-versions-2-4-up-to-2-13-will-retire-in-august/).

### <a name="version-275"></a>Version 2.75

- **Date de publication :** 5 mars 2018
- **Prise en charge du système d’exploitation :** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10, Nano Server
- **Prise en charge de WMF :** WMF 5.1, WMF 5.0 RTM, WMF 4.0 Update, WMF 4.0
- **Environment :** Azure
- **Remarques :** Cette version utilise DSC telle qu’elle est incluse dans Windows Server 2016 ; pour les autres systèmes d’exploitation Windows, elle installe [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (l’installation de WMF nécessite un redémarrage). Pour Nano Server, le rôle DSC est installé sur la machine virtuelle.
- **Nouvelles fonctionnalités :**
  - Depuis l’application du protocole TLS 1.2 par GitHub, vous ne pouvez pas intégrer une machine virtuelle au service State Configuration d’Azure Automation en utilisant des modèles Resource Manager personnalisés disponibles sur la Place de Marché Azure, ni utiliser l’extension DSC pour récupérer des configurations hébergées sur GitHub. Une erreur similaire à celle présentée ci-après est retournée quand vous déployez l’extension :

    ```json
    {
        "code": "DeploymentFailed",
        "message": "At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.",
        "details": [{
            "code": "Conflict",
            "message": "{
                \"status\": \"Failed\",
                \"error\": {
                    \"code\": \"ResourceDeploymentFailure\",
                    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",
                    \"details\": [ {
                        \"code\": \"VMExtensionProvisioningError\",
                        \"message\": \"VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'.
                        Error message: \\\"The DSC Extension failed to execute: Error downloading
                        https://github.com/Azure/azure-quickstart-templates/raw/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip
                        after 29 attempts: The request was aborted: Could not create SSL/TLS secure channel..\\nMore information about the failure can
                        be found in the logs located under 'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.74.0.0' on the VM.\\\".\"
                    } ]
                }
            }"
        }]
    }
    ```

  - Dans la nouvelle version de l’extension, TLS 1.2 est maintenant imposé. Quand vous déployez l’extension, si vous aviez déjà spécifié `AutoUpgradeMinorVersion = true` dans le modèle Resource Manager, l’extension est automatiquement mise à niveau vers la version 2.75. Pour les mises à jour manuelles, spécifiez `TypeHandlerVersion = 2.75` dans votre modèle Resource Manager.

### <a name="version-270---272"></a>Version 2.70 - 2.72

- **Date de publication :** 13 novembre 2017
- **Prise en charge du système d’exploitation :** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10, Nano Server
- **Prise en charge de WMF :** WMF 5.1, WMF 5.0 RTM, WMF 4.0 Update, WMF 4.0
- **Environment :** Azure
- **Remarques :** Cette version utilise DSC telle qu’elle est incluse dans Windows Server 2016 ; pour les autres systèmes d’exploitation Windows, elle installe [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (l’installation de WMF nécessite un redémarrage). Pour Nano Server, le rôle DSC est installé sur la machine virtuelle.
- **Nouvelles fonctionnalités :**
  - Correctifs de bogues et améliorations simplifiant l’utilisation du service State Configuration d’Azure Automation dans le portail et avec un modèle Resource Manager. Pour plus d’informations, consultez [Script de configuration par défaut](../virtual-machines/extensions/dsc-overview.md) dans la documentation de l’extension DSC.

### <a name="version-226"></a>Version 2.26

- **Date de publication :** 9 juin 2017
- **Prise en charge du système d’exploitation :** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10, Nano Server
- **Prise en charge de WMF :** WMF 5.1, WMF 5.0 RTM, WMF 4.0 Update, WMF 4.0
- **Environment :** Azure
- **Remarques :** Cette version utilise DSC telle qu’elle est incluse dans Windows Server 2016 ; pour les autres systèmes d’exploitation Windows, elle installe [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (l’installation de WMF nécessite un redémarrage). Pour Nano Server, le rôle DSC est installé sur la machine virtuelle.
- **Nouvelles fonctionnalités :**
  - Améliorations de la télémétrie.

### <a name="version-225"></a>Version 2.25

- **Date de publication :** 2 juin 2017
- **Prise en charge du système d’exploitation :** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10, Nano Server
- **Prise en charge de WMF :** WMF 5.1, WMF 5.0 RTM, WMF 4.0 Update, WMF 4.0
- **Environment :** Azure
- **Remarques :** Cette version utilise DSC telle qu’elle est incluse dans Windows Server 2016 ; pour les autres systèmes d’exploitation Windows, elle installe [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (l’installation de WMF nécessite un redémarrage). Pour Nano Server, le rôle DSC est installé sur la machine virtuelle.
- **Nouvelles fonctionnalités :**
  - Plusieurs correctifs de bogues et d’autres améliorations mineures ont été ajoutés.

### <a name="version-224"></a>Version 2.24

- **Date de publication :** 13 avril 2017
- **Prise en charge du système d’exploitation :** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **Prise en charge de WMF :** WMF 5.1, WMF 5.0 RTM, WMF 4.0 Update, WMF 4.0
- **Environment :** Azure
- **Remarques :** Cette version utilise DSC telle qu’elle est incluse dans Windows Server 2016 ; pour les autres systèmes d’exploitation Windows, elle installe [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (l’installation de WMF nécessite un redémarrage). Pour Nano Server, le rôle DSC est installé sur la machine virtuelle.
- **Nouvelles fonctionnalités :**
  - Expose l’UUID de la machine virtuelle et l’ID de l’agent DSC sous forme de métadonnées de l’extension. D’autres améliorations mineures ont été ajoutées.

### <a name="version-223"></a>Version 2.23

- **Date de publication :** 15 mars 2017
- **Prise en charge du système d’exploitation :** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **Prise en charge de WMF :** WMF 5.1, WMF 5.0 RTM, WMF 4.0 Update, WMF 4.0
- **Environment :** Azure
- **Remarques :** Cette version utilise DSC telle qu’elle est incluse dans Windows Server 2016 ; pour les autres systèmes d’exploitation Windows, elle installe [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (l’installation de WMF nécessite un redémarrage). Pour Nano Server, le rôle DSC est installé sur la machine virtuelle.
- **Nouvelles fonctionnalités :**
  - Des correctifs de bogues et d’autres améliorations ont été ajoutés.

### <a name="version-222"></a>Version 2.22

- **Date de publication :** 8 février 2017
- **Prise en charge du système d’exploitation :** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **Prise en charge de WMF :** WMF 5.1, WMF 5.0 RTM, WMF 4.0 Update, WMF 4.0
- **Environment :** Azure
- **Remarques :** Cette version utilise DSC telle qu’elle est incluse dans Windows Server 2016 ; pour les autres systèmes d’exploitation Windows, elle installe [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (l’installation de WMF nécessite un redémarrage). Pour Nano Server, le rôle DSC est installé sur la machine virtuelle.
- **Nouvelles fonctionnalités :**
  - L’extension DSC prend désormais en charge WMF 5.1.
  - D’autres améliorations mineures ont été ajoutées.

### <a name="version-221"></a>Version 2.21

- **Date de publication :** 2 décembre 2016
- **Prise en charge du système d’exploitation :** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **Prise en charge de WMF :** WMF 5.1 Preview, WMF 5.0 RTM, WMF 4.0 Update, WMF 4.0
- **Environment :** Azure
- **Remarques :** Cette version utilise DSC telle qu’elle est incluse dans Windows Server 2016 ; pour les autres systèmes d’exploitation Windows, elle installe [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (l’installation de WMF nécessite un redémarrage). Pour Nano Server, le rôle DSC est installé sur la machine virtuelle.
- **Nouvelles fonctionnalités :**
  - L’extension DSC est désormais disponible sur Nano Server. Cette version contient principalement des modifications du code pour l’exécution de l’extension sur Nano Server.
  - D’autres améliorations mineures ont été ajoutées.

### <a name="version-220"></a>Version 2.20

- **Date de publication :** 2 août 2016
- **Prise en charge du système d’exploitation :** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Prise en charge de WMF :** WMF 5.1 Preview, WMF 5.0 RTM, WMF 4.0 Update, WMF 4.0
- **Environment :** Azure
- **Remarques :** Cette version utilise DSC telle qu’elle est incluse dans Windows Server 2016 Technical Preview ; pour les autres systèmes d’exploitation Windows, elle installe [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (l’installation de WMF nécessite un redémarrage).
- **Nouvelles fonctionnalités :**
  - Prise en charge de WMF 5.1 Preview. Lors de la première publication, cette version était une mise à niveau facultative, et vous deviez spécifier Wmfversion = '5.1PP' dans les modèles Resource Manager pour installer WMF 5.1 Preview.
    Wmfversion = 'latest' installe toujours [WMF 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/).
    Pour plus d’informations sur WMF 5.1 Preview, consultez [ce blog](https://devblogs.microsoft.com/powershell/announcing-windows-management-framework-wmf-5-1-preview/).
  - D’autres correctifs et améliorations mineures ont été ajoutés.

### <a name="version--219"></a>Version 2.19

- **Date de publication :** 3 juin 2016
- **Prise en charge du système d’exploitation :** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Prise en charge de WMF :** WMF 5.0 RTM, WMF 4.0 Update, WMF 4.0
- **Environment :** Azure, Azure Chine Vianet 21, Azure Government
- **Remarques :** Cette version utilise DSC telle qu’elle est incluse dans Windows Server 2016 Technical Preview ; pour les autres systèmes d’exploitation Windows, elle installe [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (l’installation de WMF nécessite un redémarrage).
- **Nouvelles fonctionnalités :**
  - L’extension DSC est désormais disponible dans Azure China 21Vianet. Cette version contient des correctifs pour l’exécution de l’extension sur Azure China 21Vianet.

### <a name="version-218"></a>Version 2.18

- **Date de publication :** 3 juin 2016
- **Prise en charge du système d’exploitation :** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Prise en charge de WMF :** WMF 5.0 RTM, WMF 4.0 Update, WMF 4.0
- **Environment :** Azure
- **Remarques :** Cette version utilise DSC telle qu’elle est incluse dans Windows Server 2016 Technical Preview ; pour les autres systèmes d’exploitation Windows, elle installe [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (l’installation de WMF nécessite un redémarrage).
- **Nouvelles fonctionnalités :**
  - La télémétrie devient non bloquante quand une erreur se produit pendant le téléchargement du correctif de la télémétrie (problème connu d’Azure DNS) ou lors de l’installation.
  - Correctif du problème intermittent où l’extension arrêtait de traiter la configuration après un redémarrage. Ceci avait pour effet que l’extension DSC restait dans l’état « en transition ».
  - D’autres correctifs et améliorations mineures ont été ajoutés.

### <a name="version-217"></a>Version 2.17

- **Date de publication :** 26 avril 2016
- **Prise en charge du système d’exploitation :** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Prise en charge de WMF :** WMF 5.0 RTM, WMF 4.0 Update, WMF 4.0
- **Environment :** Azure
- **Remarques :** Cette version utilise DSC telle qu’elle est incluse dans Windows Server 2016 Technical Preview ; pour les autres systèmes d’exploitation Windows, elle installe [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (l’installation de WMF nécessite un redémarrage).
- **Nouvelles fonctionnalités :**
  - Prise en charge de WMF 4.0 Update. Pour plus d’informations sur WMF 4.0 Update, consultez [ce blog](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-4-0-update-now-available-for-windows-server-2012-windows-server-2008-r2-sp1-and-windows-7-sp1/).
  - Logique de nouvelle tentative sur les erreurs qui se produisent pendant l’installation de l’extension DSC ou lors de l’application d’une installation de l’extension postérieure à la configuration DSC. Avec cette modification, l’extension réessaye l’installation en cas d’échec d’une installation précédente ou reprend une configuration DSC qui a précédemment échoué, trois fois au maximum jusqu’à atteindre un état d’achèvement (réussite/erreur), ou si une nouvelle demande arrive. Si l’extension échoue en raison d’une entrée utilisateur ou de paramètres utilisateur non valides, elle n’effectue pas de nouvelle tentative. Dans ce cas, l’extension doit être rappelée avec une nouvelle demande et des paramètres utilisateur corrects. 

  > [!NOTE]
   > L’extension DSC dépend de l’agent de machine virtuelle Azure pour les nouvelles tentatives. L’agent de machine virtuelle Azure appelle l’extension avec la dernière demande en échec jusqu’à atteindre un état de réussite ou d’erreur.

### <a name="version-216"></a>Version 2.16

- **Date de publication :** 21 avril 2016
- **Prise en charge du système d’exploitation :** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Prise en charge de WMF :** WMF 5.0 RTM, WMF 4.0
- **Environment :** Azure
- **Remarques :** Cette version utilise DSC telle qu’elle est incluse dans Windows Server 2016 Technical Preview ; pour les autres systèmes d’exploitation Windows, elle installe [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (l’installation de WMF nécessite un redémarrage).
- **Nouvelles fonctionnalités :**
  - Amélioration de la gestion des erreurs et autres correctifs de bogues mineurs.
  - Nouvelle propriété dans les paramètres de l’extension DSC. `ForcePullAndApply` a été ajouté dans AdvancedOptions pour permettre à l’extension DSC de traiter des configurations DSC quand le mode d’actualisation est par tirage (pull), par opposition au mode par envoi (push) par défaut. Pour plus d’informations sur les paramètres d’extension DSC, reportez-vous à [ce blog](https://devblogs.microsoft.com/powershell/arm-dsc-extension-settings/).

### <a name="version-215"></a>Version 2.15

- **Date de publication :** 14 mars 2016
- **Prise en charge du système d’exploitation :** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Prise en charge de WMF :** WMF 5.0 RTM, WMF 4.0
- **Environment :** Azure
- **Remarques :** Cette version utilise DSC telle qu’elle est incluse dans Windows Server 2016 Technical Preview ; pour les autres systèmes d’exploitation Windows, elle installe [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (l’installation de WMF nécessite un redémarrage).
- **Nouvelles fonctionnalités :**
  - Dans l’extension version 2.14, des modifications ont été apportées pour l’installation de WMF RTM. Lors de la mise à niveau depuis l’extension version 2.13.2.0 vers 2.14.0.0, certaines cmdlets DSC peuvent échouer, ou votre configuration peut échouer avec une erreur : « Aucune instance trouvée avec les valeurs de propriété données ». Pour plus d’informations, consultez les [notes de publication de DSC](/powershell/scripting/wmf/known-issues/known-issues-dsc). Les solutions de contournement pour ces problèmes ont été ajoutées dans la version 2.15. 
  - Si vous avez déjà installé la version 2.14 et que vous rencontrez l’un des deux problèmes mentionnés ci-dessus, vous devez effectuer ces étapes manuellement. Dans une session PowerShell avec élévation de privilèges, exécutez les commandes suivantes :
    - `Remove-Item -Path $env:SystemRoot\system32\Configuration\DSCEngineCache.mof`
    - `mofcomp $env:windir\system32\wbem\DscCoreConfProv.mof`

### <a name="version-214"></a>Version 2.14

- **Date de publication :** 25 février 2016
- **Prise en charge du système d’exploitation :** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Prise en charge de WMF :** WMF 5.0 RTM, WMF 4.0
- **Environment :** Azure
- **Remarques :** Cette version utilise DSC telle qu’elle est incluse dans Windows Server 2016 Technical Preview ; pour les autres systèmes d’exploitation Windows, elle installe [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (l’installation de WMF nécessite un redémarrage).
- **Nouvelles fonctionnalités :**
  - Utilise WMF RTM.
  - Active la collecte de données pour améliorer la qualité de l’extension DSC. Pour plus d’informations, consultez cet [article de blog](https://devblogs.microsoft.com/powershell/azure-dsc-extension-data-collection-2/).
  - Fournit un format de paramètres mis à jour pour l’extension dans un modèle Resource Manager. Pour plus d’informations, consultez cet [article de blog](https://devblogs.microsoft.com/powershell/arm-dsc-extension-settings/).
  - Correctifs de bogues et autres améliorations.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus informations sur DSC PowerShell, consultez le [Centre de documentation PowerShell](/powershell/scripting/dsc/overview/overview).
- Examinez le [modèle Resource Manager pour l’extension DSC](../virtual-machines/extensions/dsc-template.md).
- Pour d’autres fonctionnalités et ressources que vous pouvez gérer avec DSC PowerShell, parcourez le référentiel [PowerShell Gallery](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).
- Pour plus d’informations sur le passage de paramètres sensibles dans des configurations, consultez [Gérer les informations d’identification de façon sécurisée avec le gestionnaire d’extensions DSC](../virtual-machines/extensions/dsc-credentials.md).
