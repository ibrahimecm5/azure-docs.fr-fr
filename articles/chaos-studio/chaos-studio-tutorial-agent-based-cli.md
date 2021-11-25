---
title: Créer une expérience qui utilise une erreur basée sur un agent avec Azure Chaos Studio à l’aide d’Azure CLI
description: Créer une expérience qui utilise une erreur basée sur un agent et configurer l’agent de chaos à l’aide d’Azure CLI
author: johnkemnetz
ms.topic: how-to
ms.date: 11/10/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: e2f605e06cd7d57acbe9fe421f4c1b883cc4d193
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373248"
---
# <a name="create-a-chaos-experiment-that-uses-an-agent-based-fault-on-a-virtual-machine-or-virtual-machine-scale-set-with-the-azure-cli"></a>Créer une expérience de chaos qui utilise une erreur basée sur un agent sur une machine virtuelle ou un groupe de machines virtuelles identiques à l’aide d’Azure CLI

Vous pouvez utiliser une expérience de chaos pour vérifier que votre application résiste aux défaillances en les provoquant dans un environnement contrôlé. Dans ce guide, vous allez provoquer un événement d’utilisation élevée du processeur sur une machine virtuelle Linux à l’aide d’une expérience de chaos et d’Azure Chaos Studio. L’exécution de cette expérience peut vous aider à vous défendre contre l’épuisement des ressources.

Ces mêmes étapes peuvent être utilisées pour configurer et exécuter une expérience pour toute erreur basée sur un agent. Une erreur **basée sur un agent** requiert la configuration et l’installation de l’agent de chaos, contrairement à une erreur de service direct qui s’exécute directement sur une ressource Azure sans nécessiter d’instrumentation.


## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
- une machine virtuelle. Si vous ne disposez pas d’une machine virtuelle, vous pouvez [suivre ces étapes pour en créer une](../virtual-machines/linux/quick-create-portal.md).
- Une configuration réseau qui vous permet d’[établir une connexion SSH à votre machine virtuelle](../virtual-machines/ssh-keys-portal.md)
- Identité managée affectée par l’utilisateur. Si vous n’avez pas d’identité managée affectée par l’utilisateur, vous pouvez [suivre ces étapes pour en créer une](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md)

## <a name="launch-azure-cloud-shell"></a>Lancement d’Azure Cloud Shell

Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte. 

Pour ouvrir Cloud Shell, sélectionnez simplement **Essayer** en haut à droite d’un bloc de code. Vous pouvez également ouvrir Cloud Shell dans un onglet distinct du navigateur en accédant à [https://shell.azure.com/bash](https://shell.azure.com/bash). Sélectionnez **Copier** pour copier les blocs de code, collez-les dans Cloud Shell et sélectionnez **Entrée** pour les exécuter.

Si vous préférez installer et utiliser l’interface de ligne de commande localement, vous aurez besoin d’Azure CLI version 2.0.30 ou ultérieure pour ce tutoriel. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI]( /cli/azure/install-azure-cli).

## <a name="assign-managed-identity-to-the-virtual-machine"></a>Attribuer une identité managée à la machine virtuelle

Avant de configurer Chaos Studio sur la machine virtuelle, vous devez attribuer une identité managée affectée par l’utilisateur à chaque machine virtuelle ou groupe de machines virtuelles identiques sur lesquels vous prévoyez d’installer l’agent à l’aide de la commande `az vm identity assign` ou `az vmss identity assign`. Remplacez `$VM_RESOURCE_ID`/`$VMSS_RESOURCE_ID` par l’ID de ressource de la machine virtuelle que vous ajoutez en tant que cible de chaos et `$MANAGED_IDENTITY_RESOURCE_ID` par l’ID de ressource de l’identité managée affectée par l’utilisateur.

**Machine virtuelle**

```azurecli-interactive
az vm identity assign --ids $VM_RESOURCE_ID --identities $MANAGED_IDENTITY_RESOURCE_ID
```

**Groupe de machines virtuelles identiques**

```azurecli-interactive
az vmss identity assign --ids $VMSS_RESOURCE_ID --identities $MANAGED_IDENTITY_RESOURCE_ID
```

## <a name="enable-chaos-studio-on-your-virtual-machine"></a>Activer Chaos Studio sur votre machine virtuelle

Chaos Studio ne peut pas injecter d’erreurs dans une machine virtuelle, sauf si cette dernière a d’abord été intégrée à Chaos Studio. Vous intégrez une machine virtuelle à Chaos Studio en créant une [cible et des fonctionnalités](chaos-studio-targets-capabilities.md) sur la ressource, puis en installant l’agent de chaos. Les machines virtuelles ont deux types de cibles : l’un qui active les erreurs de service direct (où aucun agent n’est requis) et l’autre qui active les erreurs basées sur un agent (ce qui requiert l’installation d’un agent). L’agent de chaos est une application installée sur votre machine virtuelle en tant qu’[extension de machine virtuelle](../virtual-machines/extensions/overview.md) qui vous permet d’injecter des erreurs dans le système d’exploitation invité.

### <a name="install-stress-ng-linux-only"></a>Installer stress-ng (Linux uniquement)

L’agent Chaos Studio pour Linux nécessite stress-ng, une application open source qui peut provoquer divers événements de contrainte sur une machine virtuelle. Vous pouvez installer stress-ng en vous [connectant à votre machine virtuelle Linux](../virtual-machines/ssh-keys-portal.md) et en exécutant la commande d’installation appropriée pour votre gestionnaire de package, par exemple :

```bash
sudo apt-get update && sudo apt-get -y install unzip && sudo apt-get -y install stress-ng
```

Ou :

```bash
sudo dnf -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm && sudo yum -y install stress-ng
```

### <a name="enable-chaos-target-and-capabilities"></a>Activer la cible et les capacités de Chaos

Ensuite, configurez une cible Microsoft-Agent sur chaque machine virtuelle ou groupe de machines virtuelles identiques qui spécifie l’identité managée affectée par l’utilisateur que l’agent utilisera pour se connecter à Chaos Studio. Dans cet exemple, nous utilisons une identité managée pour toutes les machines virtuelles. Une cible doit être créée via l’API REST. Dans cet exemple, nous utilisons la commande CLI `az rest` pour exécuter les appels d’API REST.

1. Modifiez le code JSON suivant en remplaçant `$USER_IDENTITY_CLIENT_ID` par le clientID de votre identité managée, que vous trouverez sur le portail Azure, dans la vue d’ensemble de l’identité managée affectée par l’utilisateur que vous avez créée, et `$USER_IDENTITY_TENANT_ID` par votre ID de locataire Azure, que vous pouvez trouver dans le portail Azure sous **Azure Active Directory**, sous **Informations sur le locataire**. Enregistrez le code JSON en tant que fichier dans le même emplacement que celui où vous exécutez l’interface de ligne de commande Azure (dans Cloud Shell, vous pouvez glisser-déplacer le fichier JSON pour le charger).

    ```json
    {
      "properties": {
        "identities": [
          {
            "clientId": "$USER_IDENTITY_CLIENT_ID",
            "tenantId": "$USER_IDENTITY_TENANT_ID",
            "type": "AzureManagedIdentity"
          }
        ]
      }
    }
    ```

2. Créez la cible en remplaçant `$RESOURCE_ID` par l’ID de ressource de la machine virtuelle ou du groupe de machines virtuelles identiques cible. Remplacez `target.json` par le nom du fichier JSON que vous avez créé à l’étape précédente.

    ```azurecli-interactive
    az rest --method put --uri https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/Microsoft-Agent?api-version=2021-09-15-preview --body @target.json --query properties.agentProfileId -o tsv
    ```

3. Prenez note du GUID du **agentProfileId** renvoyé par cette commande pour l’utiliser dans une étape ultérieure.

4. Créez les capacités sur la cible en créant un fichier capability.json comme indiqué ci-dessous. Enregistrez le fichier JSON dans le même emplacement que celui où vous exécutez l’interface de ligne de commande Azure. Remplacez `$RESOURCE_ID` par l’ID de ressource de la machine virtuelle ou du groupe de machines virtuelles identiques cible et `$CAPABILITY` par le [nom de la capacité d’erreur que vous activez](chaos-studio-fault-library.md).
    
    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/Microsoft-Agent/capabilities/$CAPABILITY?api-version=2021-09-15-preview" --body "{\"properties\":{}}"
    ```

    Par exemple, si vous activez la capacité de sollicitation de l’UC :

    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myVM/providers/Microsoft.Chaos/targets/Microsoft-Agent/capabilities/CPUPressure-1.0?api-version=2021-09-15-preview" --body "{\"properties\":{}}"
    ```

### <a name="install-the-chaos-studio-virtual-machine-extension"></a>Installer l’extension de machine virtuelle Chaos Studio

L’agent Chaos est une application qui s’exécute sur vos instances de machine virtuelle ou de groupe de machines virtuelles identiques pour exécuter des erreurs basées sur un agent. Lors de l’installation, vous configurez l’agent avec l’identité managée que l’agent doit utiliser pour s’authentifier auprès de Chaos Studio, l’ID de profil de la cible Microsoft-Agent que vous avez créée et, en option, une clé d’instrumentation Application Insights qui permet à l’agent d’envoyer des événements de diagnostic à Azure Application Insights.

1. Avant de commencer, assurez-vous de disposer des informations suivantes :
    * **agentProfileId** : propriété renvoyée lors de la création de la cible. Si vous ne disposez pas de cette propriété, vous pouvez exécuter `az rest --method get --uri https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/Microsoft-Agent?api-version=2021-09-15-preview` et copier la propriété `agentProfileId`.
    * **ClientId** : ID client de l’identité managée affectée par l’utilisateur utilisée dans la cible. Si vous ne disposez pas de cette propriété, vous pouvez exécuter `az rest --method get --uri https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/Microsoft-Agent?api-version=2021-09-15-preview` et copier la propriété `clientId`.
    * (Facultatif) **AppInsightsKey** : clé d’instrumentation de votre composant Application Insights, que vous pouvez trouver dans la page Application Insights du portail sous **Éléments principaux**.

2. Installez l’extension de machine virtuelle Chaos Studio. Remplacez `$VM_RESOURCE_ID` par l’ID de ressource de votre machine virtuelle ou remplacez `$SUBSCRIPTION_ID`, `$RESOURCE_GROUP` et `$VMSS_NAME` par les propriétés de votre groupe de machines virtuelles identiques. Remplacez `$AGENT_PROFILE_ID` par l’agentProfileId, `$USER_IDENTITY_CLIENT_ID` par le clientID de votre identité managée et `$APP_INSIGHTS_KEY` par votre clé d’instrumentation Application Insights. Si vous n’utilisez pas Application Insights, supprimez cette paire clé-valeur.

    #### <a name="install-the-agent-on-a-virtual-machine"></a>Installer l’agent sur une machine virtuelle

    **Windows**

    ```azurecli-interactive
    az vm extension set --ids $VM_RESOURCE_ID --name ChaosWindowsAgent --publisher Microsoft.Azure.Chaos --version 1.0 --settings '{"profile": "$AGENT_PROFILE_ID", "auth.msi.clientid":"$USER_IDENTITY_CLIENT_ID", "appinsightskey":"$APP_INSIGHTS_KEY"}'
    ```

    **Linux**

    ```azurecli-interactive
    az vm extension set --ids $VM_RESOURCE_ID --name ChaosLinuxAgent --publisher Microsoft.Azure.Chaos --version 1.0 --settings '{"profile": "$AGENT_PROFILE_ID", "auth.msi.clientid":"$USER_IDENTITY_CLIENT_ID", "appinsightskey":"$APP_INSIGHTS_KEY"}'
    ```

    #### <a name="install-the-agent-on-a-virtual-machine-scale-set"></a>Installer l’agent sur une machine virtuelle

    **Windows**

    ```azurecli-interactive
    az vmss extension set --subscription $SUBSCRIPTION_ID --resource-group $RESOURCE_GROUP --vmss-name $VMSS_NAME --name ChaosWindowsAgent --publisher Microsoft.Azure.Chaos --version 1.0 --settings '{"profile": "$AGENT_PROFILE_ID", "auth.msi.clientid":"$USER_IDENTITY_CLIENT_ID", "appinsightskey":"$APP_INSIGHTS_KEY"}'
    ```

    **Linux**

    ```azurecli-interactive
    az vmss extension set --subscription $SUBSCRIPTION_ID --resource-group $RESOURCE_GROUP --vmss-name $VMSS_NAME --name ChaosLinuxAgent --publisher Microsoft.Azure.Chaos --version 1.0 --settings '{"profile": "$AGENT_PROFILE_ID", "auth.msi.clientid":"$USER_IDENTITY_CLIENT_ID", "appinsightskey":"$APP_INSIGHTS_KEY"}'
    ```
3. Si vous configurez un groupe de machines virtuelles identiques, vérifiez que les instances ont été mises à niveau vers le modèle le plus récent. 

## <a name="create-an-experiment"></a>Créer une expérience

Maintenant que votre machine virtuelle est intégrée, vous pouvez créer votre expérience. Une expérience de chaos définit les actions que vous souhaitez effectuer sur les ressources cibles, organisées en étapes, qui s’exécutent de manière séquentielle, et les branches qui s’exécutent en parallèle.

1. Formulez votre JSON d’expérience à partir de l’exemple de JSON ci-dessous. Modifiez le JSON pour qu’il corresponde à l’expérience que vous souhaitez exécuter à l’aide de l’[API de création d’expérience](/rest/api/chaosstudio/experiments/create-or-update) et de la [bibliothèque d’erreurs](chaos-studio-fault-library.md).

    ```json
    {
      "identity": {
        "type": "SystemAssigned"
      },
      "location": "centralus",
      "properties": {
        "selectors": [
          {
            "id": "Selector1",
            "targets": [
              {
                "id": "/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myWindowsVM/providers/Microsoft.Chaos/targets/microsoft-agent",
                "type": "ChaosTarget"
              },
              {
                "id": "/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myLinuxVM/providers/Microsoft.Chaos/targets/micosoft-agent",
                "type": "ChaosTarget"
              }
            ],
            "type": "List"
          }
        ],
        "steps": [
          {
            "branches": [
              {
                "actions": [
                  {
                    "duration": "PT10M",
                    "name": "urn:csci:microsoft:agent:cpuPressure/1.0",
                    "parameters": [
                      {
                        "key": "pressureLevel",
                        "value": "95"
                      }
                    ],
                    "selectorId": "Selector1",
                    "type": "continuous"
                  }
                ],
                "name": "Branch 1"
              }
            ],
            "name": "Step 1"
          }
        ]
      }
    }
    ```

    En cas d’exécution sur un groupe de machines virtuelles identiques, modifiez les paramètres d’erreur pour inclure les numéros d’instance à cibler :

    ```json
    "parameters": [
      {
        "key": "pressureLevel",
        "value": "95"
      },
      {
        "key": "virtualMachineScaleSetInstances",
        "value": "[0,1,2]"
      }
    ]
    ```

    Vous pouvez identifier les numéros d’instance des groupes identiques dans le portail Azure en accédant à votre groupe de machines virtuelles identiques et en cliquant sur **Instances**. Le nom de l’instance se termine par le numéro de l’instance.
    
2. Créez l’expérience à l’aide d’Azure CLI, en remplaçant `$SUBSCRIPTION_ID`, `$RESOURCE_GROUP` et `$EXPERIMENT_NAME` par les propriétés de votre expérience. Assurez-vous que vous avez enregistré et chargé votre JSON d’expérience, puis mettez à jour `experiment.json` avec votre nom de fichier JSON.

    ```azurecli-interactive
    az rest --method put --uri https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Chaos/experiments/$EXPERIMENT_NAME?api-version=2021-09-15-preview --body @experiment.json
    ```

    Chaque expérience crée une identité managée affectée par le système correspondante. Notez le `principalId` pour cette identité dans la réponse en vue de l’étape suivante.

## <a name="give-experiment-permission-to-your-virtual-machine"></a>Accorder une autorisation d’expérience à votre machine virtuelle
Lorsque vous créez une expérience de chaos, Chaos Studio crée une identité managée affectée par le système qui exécute des erreurs sur vos ressources cibles. Cette identité doit avoir les [autorisations appropriées](chaos-studio-fault-providers.md) sur la ressource cible pour que l’expérience s’exécute correctement. Le rôle Lecteur est requis pour les erreurs basées sur un agent. Les autres rôles qui n’ont pas d’autorisation */Read, tels que Contributeur à la machine virtuelle, n’accordent pas l’autorisation appropriée pour les erreurs basées sur un agent.

Donnez à l’expérience l’accès à votre machine virtuelle ou à votre groupe de machines virtuelles identiques à l’aide de la commande ci-dessous, en remplaçant `$EXPERIMENT_PRINCIPAL_ID` par le principalId de l’étape précédente et `$RESOURCE_ID` par l’ID de ressource de la machine virtuelle ou du groupe de machines virtuelles identiques cible (ID de ressource de la machine virtuelle, et non l’ID de ressource de l’agent Chaos utilisé dans la définition de l’expérience). Exécutez cette commande pour chaque machine virtuelle ou groupe de machines virtuelles identiques ciblé dans votre expérience.

```azurecli-interactive
az role assignment create --role "Reader" --assignee-principal-type "ServicePrincipal" --assignee-object-id $EXPERIMENT_PRINCIPAL_ID --scope $RESOURCE_ID 
```


## <a name="run-your-experiment"></a>Exécuter votre expérience
Vous pouvez désormais exécuter votre service. Pour voir l’impact, nous vous recommandons d’ouvrir [un graphique des métriques Azure Monitor](../azure-monitor/essentials/tutorial-metrics.md) avec la sollicitation du processeur de votre machine virtuelle affichée sous un onglet de navigateur distinct.

1. Démarrez l’expérience à l’aide d’Azure CLI, en remplaçant `$SUBSCRIPTION_ID`, `$RESOURCE_GROUP` et `$EXPERIMENT_NAME` par les propriétés de votre expérience.

    ```azurecli-interactive
    az rest --method post --uri https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Chaos/experiments/$EXPERIMENT_NAME/start?api-version=2021-09-15-preview
    ```

2. La réponse comprend une URL d’état que vous pouvez utiliser pour interroger l’état de l’expérience pendant l’exécution de celle-ci.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez exécuté une expérience basée sur un agent, vous êtes prêt à effectuer les opérations suivantes :
- [Créer une expérience qui utilise des erreurs de service direct](chaos-studio-tutorial-service-direct-portal.md)
- [Choisir votre expérience](chaos-studio-run-experiment.md)