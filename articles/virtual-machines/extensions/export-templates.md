---
title: Exportation de groupes de ressources Azure contenant des extensions de machine virtuelle
description: Exportez des modèles Resource Manager qui incluent des extensions de machine virtuelle.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 12/05/2016
ms.openlocfilehash: b4b21e475856b724fe06e40fc83665443428b30e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132281491"
---
# <a name="exporting-resource-groups-that-contain-vm-extensions"></a>Exportation de groupes de ressources contenant des extensions de machine virtuelle

Des groupes de ressources Azure peuvent être exportés dans un nouveau modèle Resource Manager qui peut alors être redéployé. Le processus d’exportation interprète les ressources existantes et crée un modèle Resource Manager qui, lorsqu’il est déployé, génère un groupe de ressources similaires. Lorsque vous utilisez l’option d’exportation de groupe de ressources pour un groupe de ressources contenant des extensions de machine virtuelle, plusieurs éléments doivent être pris en considération comme la compatibilité et les paramètres protégés d’extension.

Ce document décrit en détails le fonctionnement du processus d’exportation de groupe de ressources par rapport aux extensions de machine virtuelle, notamment une liste des extensions prises en charge, et des informations sur la gestion des données sécurisées.

## <a name="supported-virtual-machine-extensions"></a>Extensions de machine virtuelle prises en charge

De nombreuses extensions de machine virtuelle sont disponibles. Toutes les extensions ne peuvent pas être exportées dans un modèle Resource Manager à l’aide de la fonctionnalité « Script Automation ». Si une extension de machine virtuelle n’est pas prise en charge, elle doit être placée manuellement dans le modèle exporté.

Les extensions suivantes peuvent être exportées à l’aide de la fonctionnalité de script Automation.

> Sauvegarde Acronis, Sauvegarde Acronis Linux, Infos Bg, Agent BMC CTM Linux, Agent BMC CTM Windows, Client Chef, Script personnalisé, Extension de script personnalisé, Script personnalisé pour Linux, Agent Datadog Linux, Agent Datadog Windows, Extension Docker, Extension DSC, Dynatrace Linux, Dynatrace Windows, Application de sécurité HPE Defender pour le cloud, Logiciel anti-programme malveillant IaaS, Diagnostics IaaS, Client Chef Linux, Diagnostic Linux, Application de correctifs au système d’exploitation pour Linux, Agent Puppet, Aperçu Apm 24 x 7 de site, Serveur Linux 24 x 7 de site, Windows Server 24 x 7 de site, Trend Micro DSA, Trend Micro DSA Linux, Accès aux machines virtuelles pour Linux, Instantané de machine virtuelle, Instantané de machine virtuelle Linux

## <a name="export-the-resource-group"></a>Exporter le groupe de ressources

Pour exporter un groupe de ressources dans un modèle réutilisable, procédez comme suit :

1. Connectez-vous au portail Azure.
2. Dans le menu Hub, cliquez sur Groupes de ressources.
3. Sélectionnez le groupe de ressources cible dans la liste.
4. Dans le panneau Groupe de ressources , cliquez sur Script Automation.

![Exportation de modèle](./media/export-templates/template-export.png)

Le script Automation d’Azure Resource Manager génère un modèle Resource Manager, un fichier de paramètres et plusieurs exemples de scripts de déploiement tels que PowerShell et Interface de ligne de commande Azure. À ce stade, le modèle exporté peut être téléchargé à l’aide du bouton de téléchargement, ajouté en tant que nouveau modèle à la bibliothèque de modèles ou redéployé à l’aide du bouton de déploiement.

## <a name="configure-protected-settings"></a>Configurer les paramètres protégés

Plusieurs extensions de machine virtuelle Azure incluent une configuration de paramètres protégés, qui chiffre les données sensibles telles que les informations d’identification et les chaînes de configuration. Les paramètres protégés ne sont pas exportés avec le script Automation. Si nécessaire, les paramètres protégés doivent être réinsérés dans le modèle exporté.

### <a name="step-1---remove-template-parameter"></a>Étape 1 : Supprimer un paramètre de modèle

Lorsque le groupe de ressources est exporté, un paramètre de modèle unique est créé afin de fournir une valeur pour les paramètres protégés exportés. Ce paramètre peut être supprimé. Pour supprimer le paramètre, examinez la liste des paramètres et supprimez le paramètre semblable à cet exemple JSON.

```json
"extensions_extensionname_protectedSettings": {
    "defaultValue": null,
    "type": "SecureObject"
}
```

### <a name="step-2---get-protected-settings-properties"></a>Étape 2 : Obtenir les propriétés des paramètres protégés

Étant donné que paramètre protégé comporte un ensemble de propriétés obligatoires, une liste de ces propriétés doit être collectée. Chaque paramètre de la configuration des paramètres protégés se trouve dans le [Schéma Azure Resource Manager sur GitHub](https://raw.githubusercontent.com/Azure/azure-resource-manager-schemas/master/schemas/2015-08-01/Microsoft.Compute.json). Ce schéma inclut uniquement les jeux de paramètres pour les extensions répertoriées dans la section de présentation de ce document. 

Dans le référentiel de schémas, recherchez l’extension souhaitée ; pour cet exemple `IaaSDiagnostics`. Une fois l’objet `protectedSettings` des extensions trouvé, prenez note de chaque paramètre. Dans l’exemple de l’extension `IaasDiagnostic`, les paramètres obligatoires sont `storageAccountName`, `storageAccountKey` et `storageAccountEndPoint`.

```json
"protectedSettings": {
    "type": "object",
    "properties": {
        "storageAccountName": {
            "type": "string"
        },
        "storageAccountKey": {
            "type": "string"
        },
        "storageAccountEndPoint": {
            "type": "string"
        }
    },
    "required": [
        "storageAccountName",
        "storageAccountKey",
        "storageAccountEndPoint"
    ]
}
```

### <a name="step-3---re-create-the-protected-configuration"></a>Étape 3 : Créer de nouveau la configuration protégée

Dans le modèle exporté, recherchez `protectedSettings` et remplacez l’objet de paramètre protégé exporté par un objet incluant les paramètres d’extension obligatoires et une valeur pour chacun d’entre eux.

Dans l’exemple de l’extension `IaasDiagnostic`, la nouvelle configuration de paramètres protégés peut être semblable à l’exemple suivant :

```json
"protectedSettings": {
    "storageAccountName": "[parameters('storageAccountName')]",
    "storageAccountKey": "[parameters('storageAccountKey')]",
    "storageAccountEndPoint": "https://core.windows.net"
}
```

La ressource d’extension finale est semblable à l’exemple JSON suivant :

```json
{
    "name": "Microsoft.Insights.VMDiagnosticsSettings",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "[variables('apiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "tags": {
        "displayName": "AzureDiagnostics"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Diagnostics",
        "type": "IaaSDiagnostics",
        "typeHandlerVersion": "1.5",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
            "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
        },
        "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]",
            "storageAccountEndPoint": "https://core.windows.net"
        }
    }
}
```

Si vous utilisez des paramètres de modèle pour fournir des valeurs de propriété, ceux-ci doivent être créés. Lorsque vous créez des paramètres de modèle pour des valeurs de paramètres protégés, veillez à utiliser le type de paramètre `SecureString` afin que les valeurs sensibles soient sécurisées. Pour en savoir plus sur l’utilisation des paramètres, consultez [Création de modèles Azure Resource Manager](../../azure-resource-manager/templates/syntax.md).

Dans l’exemple de l’extension `IaasDiagnostic`, les paramètres suivants doivent être créés dans la section des paramètres du modèle Resource Manager.

```json
"storageAccountName": {
    "defaultValue": null,
    "type": "SecureString"
},
"storageAccountKey": {
    "defaultValue": null,
    "type": "SecureString"
}
```

À ce stade, le modèle peut être déployé à l’aide de n’importe quelle méthode de déploiement de modèle.
