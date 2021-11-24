---
title: Filtres de connexion IP de Microsoft Azure IoT DPS
description: Utilisation de filtres IP pour bloquer les connexions à partir d’adresses IP spécifiques pour votre instance IoT DPS Azure.
author: anastasia-ms
ms.author: v-stharr
ms.service: iot-dps
services: iot-dps
ms.topic: how-to
ms.date: 11/12/2021
ms.openlocfilehash: 9354b1e3bfc57951cff919b0c14e3bc950939d8f
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132485279"
---
# <a name="use-azure-iot-dps-ip-connection-filters"></a>Utiliser les filtres de connexion IP d’instance IoT DPS Azure

La sécurité est un aspect important de toute solution IoT. Parfois, dans le cadre de votre configuration de sécurité, vous devez spécifier explicitement les adresses IP à partir desquelles les appareils peuvent se connecter. La fonctionnalité *Filtre IP* d’un service Azure IoT Hub Device Provisioning Service (DPS) vous permet de configurer des règles de rejet ou d’acceptation du trafic provenant de certaines adresses IPv4.

## <a name="when-to-use"></a>Quand l’utiliser

Il existe deux cas d’utilisation spécifiques dans lesquels il est utile de bloquer les connexions à un point de terminaison d’instance DPS à partir de certaines adresses IP :

* Votre instance DPS doit recevoir le trafic uniquement d’une plage d’adresses IP spécifiée et refuser tout le reste. Par exemple, vous utilisez votre instance DPS avec [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services) pour créer des connexions privées entre une instance DPS et vos appareils.

* Vous devez refuser le trafic provenant d’adresses IP qui ont été identifiées comme suspectes par l’administrateur de l’instance DPS.

>[!Note]
>Si le filtrage IP est activé, vous ne pourrez plus utiliser le portail Azure pour effectuer des opérations de service (par exemple, la gestion des inscriptions). Pour effectuer des opérations de service à l’aide du portail, vous devez désactiver temporairement le filtrage IP, terminer votre travail, puis réactiver la fonctionnalité de filtrage IP. Si vous souhaitez utiliser vos propres clients et éviter la désactivation du filtre IP, vous pouvez choisir d’ajouter l’adresse IP de votre ordinateur à `ipFilterRules` et de gérer les inscriptions dans le DPS par le biais de l’interface CLI.

## <a name="how-filter-rules-are-applied"></a>Application des règles de filtre

Les règles de filtre IP sont appliquées au niveau de l’instance DPS. Par conséquent, les règles de filtre IP s’appliquent à toutes les connexions issues des appareils et des applications principales utilisant n’importe quel protocole pris en charge.

Toute tentative de connexion à partir d’une adresse IP qui correspond à une règle IP de rejet dans votre instance DPS reçoit un code d’état 401 non autorisé et une description. Le message de réponse ne mentionne pas la règle IP.

> [!IMPORTANT]
> Le rejet d’adresses IP peut empêcher d’autres services Azure d’interagir avec l’instance DPS.

## <a name="default-setting"></a>Paramètre par défaut

Par défaut, le filtrage IP est désactivé et **Accès réseau public** est défini sur *Tous les réseaux*. Ce paramètre par défaut signifie que votre DPS accepte les connexions à partir de n’importe quelle adresse IP ou qu’il est conforme à une règle qui accepte la plage d’adresses IP 0.0.0.0/0.

:::image type="content" source="./media/iot-dps-ip-filtering/ip-filter-default.png" alt-text="Paramètres de filtre IP par défaut de l’instance IoT DPS.":::

## <a name="add-an-ip-filter-rule"></a>Ajouter une règle de filtre IP

Pour ajouter une règle de filtre IP :

1. Accédez au [portail Azure](https://portal.azure.com).

2. Dans le menu de gauche ou dans la page du portail, sélectionnez **Toutes les ressources**.

3. Sélectionnez votre service Device Provisioning.

4. Dans le menu **Paramètres** à gauche, sélectionnez *Mise en réseau*.

5. Sous **Accès réseau public**, sélectionnez *Plages d’adresses IP sélectionnées*.

6. Sélectionnez **+ Ajouter une règle de filtre IP**.

    :::image type="content" source="./media/iot-dps-ip-filtering/ip-filter-add-rule.png" alt-text="Ajouter une règle de filtre IP à une instance IoT DPS.":::

7. Remplissez les champs suivants :

    | Champ | Description|
    |-------|------------|
    | **Nom** |Chaîne alphanumérique unique qui ne respecte pas la casse et qui ne dépasse pas 128 caractères. Seuls les caractères alphanumériques ASCII 7 bits et `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` sont acceptés.|
    | **Plage d’adresses** |Adresse IPv4 unique ou bloc d’adresses IP en notation CIDR. Par exemple, dans la notation CIDR, 192.168.100.0/22 représente les 1024 adresses IPv4 allant de 192.168.100.0 à 192.168.103.255.|
    | **Action** |Sélectionnez **Autoriser** ou **Bloquer**.|

    :::image type="content" source="./media/iot-dps-ip-filtering/ip-filter-after-selecting-add.png" alt-text="Après avoir sélectionné Ajouter une règle de filtre IP.":::

8. Sélectionnez **Enregistrer**. Une alerte s’affiche vous informant que la mise à jour est en cours.

    :::image type="content" source="./media/iot-dps-ip-filtering/ip-filter-save-new-rule.png" alt-text="Notification sur l’enregistrement d’une règle de filtre IP.":::

    >[!Note]
    > L’option **+ Ajouter une règle de filtre IP** est désactivée lorsque vous atteignez le nombre maximal de 100 règles de filtre IP.

## <a name="edit-an-ip-filter-rule"></a>Modifier une règle de filtre IP

Pour modifier une règle existante :

1. Sélectionnez les données de la règle de filtre IP que vous souhaitez modifier.

    :::image type="content" source="./media/iot-dps-ip-filtering/ip-filter-rule-edit.png" alt-text="Modifier une règle de filtre IP.":::

2. Apportez la modification.

3. Sélectionnez **Enregistrer**.

## <a name="delete-an-ip-filter-rule"></a>Suppression d’une règle de filtre IP

Pour supprimer une règle de filtre IP :

1. Sélectionnez l’icône Supprimer sur la ligne de la règle d’adresse IP que vous souhaitez supprimer.

    :::image type="content" source="./media/iot-dps-ip-filtering/ip-filter-delete-rule.png" alt-text="Supprimer une règle de filtre IP de l’instance IoT DPS.":::

2. Sélectionnez **Enregistrer**.

## <a name="ip-filter-rule-evaluation"></a>Évaluation de règle de filtre IP

Les règles de filtre IP sont appliquées dans l’ordre. La première règle qui correspond à l’adresse IP détermine l’action accepter ou refuser.

Par exemple, si vous souhaitez accepter les adresses dans la plage 192.168.100.0/22 et rejeter tout le reste, la première règle de la grille doit accepter la plage d’adresses 192.168.100.0/22. La règle suivante doit rejeter toutes les adresses à l’aide de la plage 0.0.0.0/0.

Pour modifier l’ordre de vos règles de filtre IP :

1. Sélectionnez la règle que vous souhaitez déplacer.

2. Faites glisser la règle et déposez-la à l’emplacement souhaité.

3. Sélectionnez **Enregistrer**.

## <a name="update-ip-filter-rules-using-azure-resource-manager-templates"></a>Mettre à jour les règles de filtre IP à l’aide de modèles Azure Resource Manager

Vous pouvez mettre à jour votre filtre IP DPS de deux manières :

1. Appelez la méthode de l’API REST de fournisseur de ressources IoT Hub. Pour savoir comment mettre à jour vos règles de filtre IP à l’aide de REST, consultez `IpFilterRule` dans la [section Définitions](/api/iothub/iot-hub-resource/update#definitions) de la [méthode Fournisseur de ressources Iot Hub – Mise à jour](/api/iothub/iot-hub-resource/update).

2. Utilisez les modèles Azure Resource Manager. Pour obtenir de l’aide sur l’utilisation des modèles Resource Manager, consultez [Modèles Azure Resource Manager](../azure-resource-manager/templates/overview.md). Les exemples qui suivent vous montrent comment créer, modifier et supprimer des règles de filtre IP DPS à l’aide de modèles Azure Resource Manager.

    >[!NOTE]
    >Azure CLI et Azure PowerShell ne prennent actuellement pas en charge les mises à jour des règles de filtre IP DPS.

### <a name="add-an-ip-filter-rule"></a>Ajouter une règle de filtre IP

L’exemple de modèle suivant crée une règle de filtre IP appelée « AllowAll » qui accepte tout le trafic.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#", 
    "contentVersion": "1.0.0.0", 
    "parameters": {
        "iotDpsName": {
            "type": "string",
            "defaultValue": "[resourceGroup().name]",
            "minLength": 3,
            "metadata": {
                "description": "Specifies the name of the IoT DPS service."
            }
        }, 
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for Iot DPS resource."
            }
        }        
    }, 
    "variables": {
        "iotDpsApiVersion": "2020-01-01"
    }, 
    "resources": [
        {
            "type": "Microsoft.Devices/provisioningServices",
            "apiVersion": "[variables('iotDpsApiVersion')]",
            "name": "[parameters('iotDpsName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "capacity": 1
            },
            "properties": {
                "IpFilterRules": [
                    {
                        "FilterName": "AllowAll",
                        "Action": "Accept",
                        "ipMask": "0.0.0.0/0"
                    }
                ]
            }            
        }
    ]
}
```

Modifiez les attributs de la règle de filtre IP du modèle selon vos besoins.

| Attribut                | Description |
| ------------------------ | ----------- |
| **FilterName**           | Donnez un nom à la règle de filtre IP. Ce nom doit être une chaîne alphanumérique unique qui ne prend pas en compte la casse et qui ne dépasse pas 128 caractères. Seuls les caractères alphanumériques ASCII 7 bits et `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` sont acceptés. |
| **Action**               | Les valeurs autorisées sont **Accepter** ou  **Refuser** pour l’action de la règle de filtre IP. |
| **ipMask**               | Fournissez une adresse IPv4 unique ou un bloc d’adresses IP en notation CIDR. Par exemple, dans la notation CIDR, 192.168.100.0/22 représente les 1024 adresses IPv4 allant de 192.168.100.0 à 192.168.103.255. |


### <a name="update-an-ip-filter-rule"></a>Modifier une règle de filtre IP

L’exemple de modèle suivant met à jour la règle de filtre IP nommée « AllowAll », mentionnée précédemment, pour refuser tout le trafic.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
                "IpFilterRules": [ 
                    { 
                        "FilterName": "AllowAll", 
                        "Action": "Reject", 
                        "ipMask": "0.0.0.0/0" 
                    } 
                ] 
            }             
        } 
    ] 
}
```

### <a name="delete-an-ip-filter-rule"></a>Suppression d’une règle de filtre IP

L’exemple de modèle suivant supprime toutes les règles de filtre IP pour l’instance DPS.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
            }             
        } 
    ] 
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour aller plus loin dans la gestion d’une instance DPS, consultez :

* [Compréhension des adresses IP d’une instance IoT DPS](iot-dps-understand-ip-address.md)
* [Configurer une instance DPS avec Azure CLI](quick-setup-auto-provision-cli.md)
* [Contrôler l’accès à une instance DPS](how-to-control-access.md)
