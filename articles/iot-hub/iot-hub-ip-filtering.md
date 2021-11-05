---
title: Filtres IP d’Azure Hub IoT | Microsoft Docs
description: Utilisation de filtres IP pour autoriser les connexions à partir d’adresses IP spécifiques pour votre hub Azure IoT.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/22/2021
ms.author: jlian
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b984b7ee718a051456f8f51e5c9a49c247b884b4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131040551"
---
# <a name="use-ip-filters"></a>Utiliser des filtres IP

La sécurité est un aspect important de toute solution IoT basée sur Azure IoT Hub. Parfois, dans le cadre de votre configuration de sécurité, vous devez spécifier explicitement les adresses IP à partir desquelles les appareils peuvent se connecter. La fonctionnalité *Filtre IP* vous permet de configurer des règles de rejet ou d’acceptation du trafic provenant de certaines adresses IPv4.

## <a name="when-to-use"></a>Quand l’utiliser

Utilisez le filtre IP pour recevoir le trafic uniquement à partir d’une plage spécifiée d’adresses IP et refuser tout le reste. Par exemple, vous utilisez votre hub IoT avec [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services) pour créer des connexions privées entre un hub IoT et votre infrastructure locale.

## <a name="default-setting"></a>Paramètre par défaut

Pour accéder à la page Paramètres de filtre IP, sélectionnez **Réseau**, **Accès public**, puis choisissez **Plages d’adresses IP sélectionnées** :

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-default.png" alt-text="Capture d’écran montrant comment définir les paramètres de filtre IP par défaut.":::

Par défaut, la grille **Filtre IP** dans le portail pour un IoT Hub est vide. Ce paramètre par défaut signifie que votre hub bloque les connexions provenant de toutes les adresses IP. Ce paramètre par défaut est équivalent à une règle qui bloque la plage d’adresses IP `0.0.0.0/0`.

## <a name="add-or-edit-an-ip-filter-rule"></a>Ajouter ou modifier une règle de filtre IP

Pour ajouter une règle de filtre IP, sélectionnez **+ Ajouter une règle de filtre IP**. Pour ajouter rapidement l’adresse IP de votre ordinateur, cliquez sur **Ajouter l’adresse IP de votre client**. 

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-add-rule.png" alt-text="Capture d’écran montrant comment ajouter une règle de filtre IP IoT Hub.":::

Après avoir sélectionné **Ajouter une règle de filtre IP**, renseignez les champs. Ces champs sont préremplis si vous avez choisi d’ajouter l’adresse IP de votre client.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-after-selecting-add.png" alt-text="Capture d’écran montrant ce qu’il faut faire après l’ajout d’une règle de filtre IP.":::

* Donnez un **nom** à la règle de filtre IP. Ce nom doit être une chaîne alphanumérique unique qui ne respecte pas la casse et qui ne dépasse pas 128 caractères. Seuls les caractères alphanumériques ASCII 7 bits et `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` sont acceptés.

* Fournissez une adresse IPv4 unique ou un bloc d’adresses IP en notation CIDR. Par exemple, dans la notation CIDR, 192.168.100.0/22 représente les 1024 adresses IPv4 allant de 192.168.100.0 à 192.168.103.255.

Après avoir rempli les champs, sélectionnez **Enregistrer** pour enregistrer la règle. Une alerte s’affiche vous informant que la mise à jour est en cours.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png" alt-text="Capture d’écran montrant une notification sur l’enregistrement d’une règle de filtre IP.":::

L’option **Ajouter** est désactivée lorsque vous atteignez le nombre maximal de dix règles de filtre IP.

Pour modifier une règle existante, sélectionnez les données que vous souhaitez modifier, effectuez la modification, puis sélectionnez **Enregistrer** pour enregistrer votre modification.

## <a name="delete-an-ip-filter-rule"></a>Suppression d’une règle de filtre IP

Pour supprimer une règle de filtre IP, sélectionnez l’icône de la corbeille sur cette ligne, puis sélectionnez **Enregistrer**. La règle est supprimée et la modification est enregistrée.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-delete-rule.png" alt-text="Capture d’écran montrant comment supprimer une règle de filtre IP IoT Hub.":::

## <a name="apply-ip-filter-rules-to-the-built-in-event-hub-compatible-endpoint"></a>Appliquer des règles de filtre IP au point de terminaison intégré compatible avec Event Hub

Pour appliquer les règles de filtre IP au point de terminaison intégré compatible avec Event Hub, cochez la case à côté de l’option **Appliquer des filtres IP au point de terminaison intégré ?** , puis sélectionnez **Enregistrer**.

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-built-in-endpoint.png" alt-text="Capture d’écran montrant le bouton bascule pour le point de terminaison intégré.":::

> [!NOTE]
> Cette option n’est pas disponible pour les hubs IoT (F1) gratuits. Pour appliquer des règles de filtre IP au point de terminaison intégré, utilisez un hub IoT payant.

En activant cette option, vos règles de filtre IP sont répliquées sur le point de terminaison intégré, de sorte que seules les plages d’adresses IP approuvées peuvent y accéder.

Si vous désactivez cette option, le point de terminaison intégré est accessible à toutes les adresses IP. Ce comportement peut être utile si vous souhaitez lire à partir du point de terminaison avec des services dont les adresses IP changent, comme Azure Stream Analytics. 

## <a name="how-filter-rules-are-applied"></a>Application des règles de filtre

Les règles de filtre IP sont appliquées au niveau du service IoT Hub. Par conséquent, les règles de filtre IP s’appliquent à toutes les connexions issues des appareils et des applications principales utilisant n’importe quel protocole pris en charge. Vous pouvez également choisir si le [point de terminaison intégré compatible avec Event Hub](iot-hub-devguide-messages-read-builtin.md) (et non par le biais de la chaîne de connexion IoT Hub) est soumis à ces règles. 

Toute tentative de connexion à partir d’une adresse IP qui n’est pas explicitement autorisée reçoit un code d’état 401 non autorisé et une description. Le message de réponse ne mentionne pas la règle IP. Le rejet d’adresses IP est de nature à empêcher d’autres services Azure (comme Azure Stream Analytics, Azure Virtual Machines ou l’Explorateur d’appareils dans le portail Azure) d’interagir avec IoT Hub.

> [!NOTE]
> Si vous devez utiliser Azure Stream Analytics (ASA) pour lire des messages à partir d’un hub IoT avec le filtre IP activé, **désactivez** l’option **Appliquer des filtres IP au point de terminaison intégré**, puis utilisez le nom compatible avec Event Hub et le point de terminaison de votre hub IoT pour ajouter manuellement une [entrée de flux Event Hubs](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs) dans l’ASA.

### <a name="ordering"></a>Classement

Les règles de filtre IP sont des règles *d’autorisation* appliquées sans ordre. Seules les adresses IP que vous ajoutez sont autorisées à se connecter à IoT Hub. 

Par exemple, si vous souhaitez accepter des adresses dans la plage `192.168.100.0/22` et rejeter tout le reste, il vous suffit d’ajouter une règle dans la grille avec la plage d’adresses `192.168.100.0/22`.

### <a name="azure-portal"></a>Portail Azure 

Les règles de filtre IP sont également appliquées lors de l’utilisation d’IoT Hub via le portail Azure. Cela est dû au fait que les appels d’API au service IoT Hub sont effectués directement à l’aide de votre navigateur avec vos informations d’identification, ce qui est cohérent avec les autres services Azure. Pour accéder à IoT Hub avec le portail Azure quand le filtre IP est activé, ajoutez l’adresse IP de votre ordinateur à la liste d’autorisation. 

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Récupérer et mettre à jour des filtres IP à l'aide d'Azure CLI

Les filtres IP de votre hub IoT peuvent être récupérés et mis à jour via [Azure CLI](/cli/azure/).

Pour récupérer les filtres IP actuels de votre hub IoT, exécutez :

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Cette opération renvoie un objet JSON dans lequel vos filtres IP existants sont répertoriés sous la clé `properties.networkRuleSets` :

```json
{
...
    "properties": {
        "networkRuleSets": {
            "defaultAction": "Deny",
            "applyToBuiltInEventHubEndpoint": true,
            "ipRules": [{
                    "filterName": "TrustedFactories",
                    "action": "Allow",
                    "ipMask": "1.2.3.4/5"
                },
                {
                    "filterName": "TrustedDevices",
                    "action": "Allow",
                    "ipMask": "1.1.1.1/1"
                }
            ]
        }
    }
}
```

Pour ajouter un nouveau filtre IP à votre hub IoT, exécutez :

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.networkRuleSets.ipRules "{\"action\":\"Allow\",\"filterName\":\"TrustedIP\",\"ipMask\":\"192.168.0.1\"}"
```

Pour supprimer un filtre IP existant de votre hub IoT, exécutez :

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.networkRuleSets.ipRules <ipFilterIndexToRemove>
```

Ici, `<ipFilterIndexToRemove>` doit correspondre à l’ordre des filtres IP dans les règles `properties.networkRuleSets.ipRules` de votre hub IoT.

## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>Récupérer et mettre à jour des filtres IP à l'aide d'Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Les filtres IP de votre hub IoT peuvent être récupérés et définis via [Azure PowerShell](/powershell/azure/).

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.networkRuleSets.ipRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='TrustedIP'; 'action'='Allow'; 'ipMask'='192.168.0.1'}

# Add your new IP filter rule
$iothubResource.Properties.networkRuleSets.ipRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.networkRuleSets.ipRules = @($iothubResource.Properties.networkRuleSets.ipRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

## <a name="update-ip-filter-rules-using-rest"></a>Mettre à jour les règles de filtrage IP à l'aide de REST


Vous pouvez également récupérer et modifier les filtres IP de votre hub IoT via le point de terminaison REST du fournisseur de ressources Azure. Reportez-vous à `properties.networkRuleSets` dans la [méthode createorupdate](/rest/api/iothub/iothubresource/createorupdate).

## <a name="ip-filter-classic-retirement"></a>Mise hors service du filtre IP (classique)

Le filtre IP classique a été mis hors service. Pour en savoir plus, consultez [Filtre IP classique IoT Hub et comment procéder à une mise à niveau](iot-hub-ip-filter-classic.md).

## <a name="next-steps"></a>Étapes suivantes

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Métriques d’IoT Hub](./monitor-iot-hub.md)
* [Prise en charge par IoT Hub des réseaux virtuels avec Private Link et Managed Identity](virtual-network-support.md)
* [Gestion de l’accès réseau public pour votre hub IoT](iot-hub-public-network-access.md)
* [Superviser avec IoT Hub](monitor-iot-hub.md)
