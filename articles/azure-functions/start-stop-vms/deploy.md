---
title: Déployer Start/Stop VMs v2 (préversion)
description: Cet article explique comment déployer la fonctionnalité Start/Stop VMs v2 (préversion) pour vos machines virtuelles Azure dans votre abonnement Azure.
services: azure-functions
ms.subservice: start-stop-vms
ms.date: 06/25/2021
ms.topic: conceptual
ms.openlocfilehash: 878bead652cf9962febc7622ef98ade0abc6cce5
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399690"
---
# <a name="deploy-startstop-vms-v2-preview"></a>Déployer Start/Stop VMs v2 (préversion)

Effectuez les étapes de cette rubrique dans l’ordre pour installer la fonctionnalité Start/Stop VMs v2 (préversion). Une fois le processus d’installation terminé, configurez les planifications pour les adapter à vos besoins.

## <a name="permissions-considerations"></a>Considérations relatives aux autorisations
Gardez à l’esprit les points suivants avant et pendant le déploiement :
+   La solution permet à ceux qui possèdent des autorisations de contrôle d’accès en fonction du rôle (RBAC) appropriées sur le déploiement de Start/Stop v2 d’ajouter, de supprimer et de gérer des planifications pour les machines virtuelles dans le cadre de l’étendue de Start/Stop v2. Ce comportement est normal. Dans la pratique, cela signifie qu’un utilisateur qui ne dispose pas de l’autorisation RBAC directe sur une machine virtuelle peut toujours créer des opérations de démarrage, d’arrêt et d’arrêt automatique sur cette machine virtuelle lorsqu’il dispose de l’autorisation RBAC pour modifier la solution Start/Stop v2 qui le gère.
+ Tous les utilisateurs ayant accès à la solution Start/Stop v2 peuvent découvrir les coûts, les économies, l’historique des opérations et les autres données stockées dans l’instance Application Insights utilisée par l’application Start/Stop v2.
+ Lorsque vous gérez une solution Start/Stop v2, vous devez considérer les autorisations des utilisateurs par la solution Start/Stop v2, en particulier lorsqu’il n’y a pas d’autorisation de modifier directement les machines virtuelles cibles.
## <a name="deploy-feature"></a>Déployer la fonctionnalité

Le déploiement est initié à partir de l’organisation GitHub Start/Stop VMs v2 [ici](https://github.com/microsoft/startstopv2-deployments/blob/main/README.md). Bien que cette fonctionnalité soit conçue pour gérer toutes les machines virtuelles de votre abonnement dans tous les groupes de ressources à partir d’un seul déploiement dans l’abonnement, vous pouvez en installer une autre instance en fonction du modèle opérationnel ou des besoins de votre entreprise. Elle peut également être configurée pour gérer de manière centralisée les machines virtuelles de plusieurs abonnements.

Pour simplifier la gestion et la suppression, nous vous recommandons de déployer Start/Stop VMs v2 (préversion) sur un groupe de ressources dédié.

> [!NOTE]
> Actuellement, cette préversion ne permet pas de spécifier un compte de stockage ou une ressource Application Insights existants.


> [!NOTE]
> Le format d’affectation de noms pour l’application de fonction et le compte de stockage a changé. Pour garantir l’unicité globale, une chaîne aléatoire et unique est maintenant ajoutée aux noms de ces ressources.

1. Ouvrez votre navigateur et accédez à l’[organisation GitHub](https://github.com/microsoft/startstopv2-deployments/blob/main/README.md) Start/Stop VMs v2.
1. Sélectionnez l’option de déploiement en fonction de l’environnement cloud Azure dans lequel vos machines virtuelles Azure sont créées. Cela ouvrira la page de déploiement Azure Resource Manager personnalisé dans le portail Azure.
1. Si vous y êtes invité, connectez-vous au [portail Azure](https://portal.azure.com).
1. Saisissez les valeurs suivantes :

    |Nom |Value |
    |-----|------|
    |Région |Sélectionnez une région près de chez vous pour les nouvelles ressources.|
    |Nom du groupe de ressources |Spécifiez le nom du groupe de ressources qui contiendra les ressources individuelles pour Start/Stop VMs. |
    |Région du groupe de ressources |Spécifiez la région pour le groupe de ressources. Par exemple, **USA Centre**.|
    |Nom de l’application de fonction Azure |Tapez un nom valide dans un chemin d’URL. Le système vérifie que le nom que vous tapez est unique dans Azure Functions. |
    |Nom Application Insights |Spécifiez le nom de votre instance Application Insights qui contiendra l’analyse pour Start/Stop VMs. |
    |Région Application Insights |Spécifiez la région de l’instance Application Insights.|
    |Nom du compte de stockage |Spécifiez le nom du compte de stockage Azure qui servira à stocker la télémétrie d’exécution de Start/Stop VMs. |
    |Adresse de messagerie |Spécifiez une ou plusieurs adresses e-mail pour recevoir des notifications d’état, séparées par une virgule (,).|

    :::image type="content" source="media/deploy/deployment-template-details.png" alt-text="Configuration du déploiement du modèle Start/Stop VMs":::

1. Sélectionnez **Vérifier + créer** au bas de la page.
1. Sélectionnez **Créer** pour démarrer le déploiement.
1. Sélectionnez l’icône représentant une cloche (notifications) en haut de l’écran pour afficher l’état du déploiement. **Déploiement en cours** doit s’afficher. Attendez la fin du déploiement.
1. Sélectionnez **Accédez au groupe de ressources** à partir du volet de notification. Vous devriez voir un écran semblable à :

    :::image type="content" source="media/deploy/deployment-results-resource-list.png" alt-text="Liste des ressources de déploiement du modèle Start/Stop VMs":::

> [!NOTE]
> Nous collectons des données de télémétrie des opérations et des pulsations pour vous aider au mieux si vous contactez l’équipe de support pour tout dépannage. Nous collectons également l’historique des événements des machines virtuelles pour vérifier que le service a agi sur une machine virtuelle et déterminer combien de temps une machine virtuelle a été répétée pour connaître l’efficacité du service.

## <a name="enable-multiple-subscriptions"></a>Activer plusieurs abonnements

Une fois le déploiement de Start/Stop VMs terminé, procédez comme suit pour permettre à Start/Stop VMs v2 (préversion) d’effectuer des actions sur plusieurs abonnements.

1. Copiez la valeur du nom de l’application de fonction Azure que vous avez spécifiée pendant le déploiement.

1. Dans le portail, accédez à votre abonnement secondaire. Sélectionnez l’abonnement, puis sélectionnez **Contrôle d’accès (IAM)** .

1. Sélectionnez **Ajouter**, puis sélectionnez **Ajouter une attribution de rôle**.

1. Sélectionnez le rôle **Contributeur** dans la liste déroulante **Rôle**.

1. Entrez le nom de l’application de fonction Azure dans le champ **Sélectionner**. Sélectionnez le nom de la fonction dans les résultats.

1. Sélectionnez **Enregistrer** pour valider vos modifications.

## <a name="configure-schedules-overview"></a>Vue d’ensemble de la configuration des planifications

Pour gérer la méthode d’automatisation permettant de contrôler le démarrage et l’arrêt de vos machines virtuelles, vous devez configurer une ou plusieurs des applications logiques incluses en fonction de vos besoins.

- Planifié : Les actions de démarrage et d’arrêt sont basées sur une planification que vous spécifiez sur les machines virtuelles Azure Resource Manager et classiques. **ststv2_vms_Scheduled_start** et **ststv2_vms_Scheduled_stop** configurent le démarrage et l’arrêt planifiés.

- Séquencé : Les actions de démarrage et d’arrêt sont basées sur une planification ciblant les machines virtuelles avec des balises de séquençage prédéfinies. Seules deux balises nommées sont prises en charge : **sequencestart** et **sequencestop**. **ststv2_vms_Sequenced_start** et **ststv2_vms_Sequenced_stop** configurent le démarrage et l’arrêt séquencés.

    > [!NOTE]
    > Ce scénario ne prend en charge que les machines virtuelles Azure Resource Manager.

- Autostop : Cette fonctionnalité est utilisée uniquement pour effectuer une action d’arrêt sur les machines virtuelles Azure Resource Manager et classiques en fonction de leur utilisation de l’UC. Il peut également s’agir d’une *action* planifiée, qui crée des alertes sur les machines virtuelles et, en fonction de la condition, l’alerte est déclenchée pour effectuer l’action d’arrêt. **ststv2_vms_AutoStop** configure la fonctionnalité d’arrêt automatique.

Si vous avez besoin de planifications supplémentaires, vous pouvez dupliquer l’une des applications logiques fournies en utilisant l’option **Cloner** dans le portail Azure.

:::image type="content" source="media/deploy/logic-apps-clone-option.png" alt-text="Sélectionner l’option Cloner pour dupliquer une application logique":::

## <a name="scheduled-start-and-stop-scenario"></a>Scénario de démarrage et d’arrêt planifiés

Procédez comme suit pour configurer l’action de démarrage et d’arrêt planifiés pour les machines virtuelles Azure Resource Manager et classiques. Par exemple, vous pouvez configurer la planification de **ststv2_vms_Scheduled_start** pour les démarrer le matin lorsque vous êtes au bureau et arrêter toutes les machines virtuelles d’un abonnement lorsque vous quittez le travail le soir en fonction de la planification de **ststv2_vms_Scheduled_stop**.

La configuration de l’application logique pour qu’elle démarre uniquement les machines virtuelles est prise en charge.

Pour chaque scénario, vous pouvez cibler l’action sur un ou plusieurs abonnements, un ou plusieurs groupes de ressources, et spécifier une ou plusieurs machines virtuelles dans une liste d’inclusions ou d’exclusions. Vous ne pouvez pas les spécifier ensemble dans la même application logique.

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à **Applications logiques**.

1. Dans la liste des applications logiques, sélectionnez **ststv2_vms_Scheduled_start** pour configurer le démarrage planifié. Pour configurer l’arrêt planifié, sélectionnez **ststv2_vms_Scheduled_stop**.

1. Dans le volet gauche, sélectionnez **Concepteur d’applications logiques**.

1. Une fois que le concepteur d’applications logiques s’affiche, dans le volet du concepteur, sélectionnez **Périodicité** pour configurer la planification de l’application logique. Pour en savoir plus sur les options de périodicité spécifiques, consultez [Planifier une tâche périodique](../../connectors/connectors-native-recurrence.md#add-the-recurrence-trigger).

    :::image type="content" source="media/deploy/schedule-recurrence-property.png" alt-text="Configurer la fréquence de périodicité de l’application logique":::

1. Dans le volet du concepteur, sélectionnez **Try de fonction** pour configurer les paramètres cibles. Si vous souhaitez gérer les machines virtuelles de tous les groupes de ressources de l’abonnement, modifiez le corps de la demande comme indiqué dans l’exemple suivant.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/"
        ]
     }
    }
    ```

    Spécifiez plusieurs abonnements dans le tableau `subscriptions` avec chaque valeur séparée par une virgule, comme dans l’exemple suivant.

    ```json
    "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/"
        ]
    ```

    Si vous souhaitez gérer les machines virtuelles de groupes de ressources spécifiques, modifiez le corps de la demande comme indiqué dans l’exemple suivant. Chaque chemin de ressource spécifié doit être séparé par une virgule. Vous pouvez spécifier un ou plusieurs groupes de ressources le cas échéant.

    Cet exemple illustre également l’exclusion d’une machine virtuelle. Vous pouvez exclure la machine virtuelle en spécifiant le chemin de ressource de la machine virtuelle ou un caractère générique.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ResourceGroups": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/"
        ],
        "ExcludedVMLists": [
         "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/providers/Microsoft.Compute/virtualMachines/vm1"
        ]
      }
    }
    ```

    Ici, l’action sera effectuée sur toutes les machines virtuelles, à l’exception de celles dont le nom commence par Az et Bz dans les deux abonnements.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ExcludedVMLists": [“Az*”,“Bz*”],
       "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/"
    
        ]
      }
    }
    ```

    Si vous souhaitez gérer un ensemble spécifique de machines virtuelles dans l’abonnement, modifiez le corps de la demande comme indiqué dans l’exemple suivant. Chaque chemin de ressource spécifié doit être séparé par une virgule. Vous pouvez spécifier une machine virtuelle si nécessaire.

    ```json
    {
      "Action": "start",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "VMLists": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1",
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg3/providers/Microsoft.Compute/virtualMachines/vm2",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/providers/Microsoft.ClassicCompute/virtualMachines/vm30"
          
        ]
      }
    }
    ```

1. Dans le volet Vue d’ensemble de l’application logique, sélectionnez **Activer**.  

## <a name="sequenced-start-and-stop-scenario"></a>Scénario de démarrage et d’arrêt séquencés

Dans un environnement comprenant deux composants ou plus sur plusieurs machines virtuelles Azure Resource Manager dans une architecture d’application distribuée, il est important de prendre en charge l’ordre de démarrage et d’arrêt des composants. Assurez-vous d’avoir appliqué les étiquettes **sequencestart** et **sequencestop** aux machines virtuelles cibles, comme décrit dans la [page Vue d’ensemble](../../azure-functions/start-stop-vms/overview.md#overview) avant de configurer ce scénario.

1. Dans la liste des applications logiques, sélectionnez **ststv2_vms_Sequenced_start** pour configurer le démarrage séquencé. Pour configurer l’arrêt séquencé, sélectionnez **ststv2_vms_Sequenced_stop**.

1. Dans le volet gauche, sélectionnez **Concepteur d’applications logiques**.

1. Une fois que le concepteur d’applications logiques s’affiche, dans le volet du concepteur, sélectionnez **Périodicité** pour configurer la planification de l’application logique. Pour en savoir plus sur les options de périodicité spécifiques, consultez [Planifier une tâche périodique](../../connectors/connectors-native-recurrence.md#add-the-recurrence-trigger).

    :::image type="content" source="media/deploy/schedule-recurrence-property.png" alt-text="Configurer la fréquence de périodicité de l’application logique":::

1. Dans le volet du concepteur, sélectionnez **Try de fonction** pour configurer les paramètres cibles. Si vous souhaitez gérer les machines virtuelles de tous les groupes de ressources de l’abonnement, modifiez le corps de la demande comme indiqué dans l’exemple suivant.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/"
        ]
     },
       "Sequenced": true
    }
    ```

    Spécifiez plusieurs abonnements dans le tableau `subscriptions` avec chaque valeur séparée par une virgule, comme dans l’exemple suivant.

    ```json
    "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/"
        ]
    ```

    Si vous souhaitez gérer les machines virtuelles de groupes de ressources spécifiques, modifiez le corps de la demande comme indiqué dans l’exemple suivant. Chaque chemin de ressource spécifié doit être séparé par une virgule. Vous pouvez spécifier un groupe de ressources si nécessaire.

    Cet exemple démontre également l’exclusion d’une machine virtuelle par son chemin de ressource, par rapport à l’exemple de démarrage et d’arrêt planifiés, qui utilisait des caractères génériques.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ResourceGroups": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/"
        ],
        "ExcludedVMLists": [
         "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/providers/Microsoft.Compute/virtualMachines/vm1"
        ]
      },
       "Sequenced": true
    }
    ```

    Si vous souhaitez gérer un ensemble spécifique de machines virtuelles dans un abonnement, modifiez le corps de la demande comme indiqué dans l’exemple suivant. Chaque chemin de ressource spécifié doit être séparé par une virgule. Vous pouvez spécifier une machine virtuelle si nécessaire.

    ```json
    {
      "Action": "start",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "VMLists": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1",
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg2/providers/Microsoft.ClassicCompute/virtualMachines/vm2",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/providers/Microsoft.ClassicCompute/virtualMachines/vm30"
        ]
      },
       "Sequenced": true
    }
    ```

## <a name="auto-stop-scenario"></a>Scénario d’arrêt automatique

Start/Stop VMs v2 (préversion) peut aider à gérer le coût d’exécution des machines virtuelles Azure Resource Manager et classiques dans votre abonnement en évaluant les ordinateurs qui ne sont pas utilisés pendant les périodes creuses, par exemple après les heures de travail, et les arrêtant automatiquement si l’utilisation du processeur est inférieure à un pourcentage spécifié.

Les propriétés d’alerte de métrique suivantes dans le corps de la demande prennent en charge la personnalisation :

- AutoStop_MetricName
- AutoStop_Condition
- AutoStop_Threshold
- AutoStop_Description
- AutoStop_Frequency
- AutoStop_Severity
- AutoStop_Threshold
- AutoStop_TimeAggregationOperator
- AutoStop_TimeWindow

Pour en savoir plus sur le fonctionnement des alertes de métrique d’Azure Monitor et sur la façon de les configurer, consultez [Alertes de métrique dans Azure Monitor](../../azure-monitor/alerts/alerts-metric-overview.md).

1. Dans la liste des applications logiques, sélectionnez **ststv2_vms_AutoStop** pour configurer l’arrêt automatique.

1. Dans le volet gauche, sélectionnez **Concepteur d’applications logiques**.

1. Une fois que le concepteur d’applications logiques s’affiche, dans le volet du concepteur, sélectionnez **Périodicité** pour configurer la planification de l’application logique. Pour en savoir plus sur les options de périodicité spécifiques, consultez [Planifier une tâche périodique](../../connectors/connectors-native-recurrence.md#add-the-recurrence-trigger).

    :::image type="content" source="media/deploy/schedule-recurrence-property.png" alt-text="Configurer la fréquence de périodicité de l’application logique":::

1. Dans le volet du concepteur, sélectionnez **Try de fonction** pour configurer les paramètres cibles. Si vous souhaitez gérer les machines virtuelles de tous les groupes de ressources de l’abonnement, modifiez le corps de la demande comme indiqué dans l’exemple suivant.

    ```json
    {
      "Action": "stop",
      "EnableClassic": false,    
      "AutoStop_MetricName": "Percentage CPU",
      "AutoStop_Condition": "LessThan",
      "AutoStop_Description": "Alert to stop the VM if the CPU % exceed the threshold",
      "AutoStop_Frequency": "00:05:00",
      "AutoStop_Severity": "2",
      "AutoStop_Threshold": "5",
      "AutoStop_TimeAggregationOperator": "Average",
      "AutoStop_TimeWindow": "06:00:00",
      "RequestScopes":{        
        "Subscriptions":[
            "/subscriptions/12345678-1111-2222-3333-1234567891234/",
            "/subscriptions/12345678-2222-4444-5555-1234567891234/"
        ],
        "ExcludedVMLists":[]
      }        
    }
    ```

    Si vous souhaitez gérer les machines virtuelles de groupes de ressources spécifiques, modifiez le corps de la demande comme indiqué dans l’exemple suivant. Chaque chemin de ressource spécifié doit être séparé par une virgule. Vous pouvez spécifier un groupe de ressources si nécessaire.

    ```json
    {
      "Action": "stop",
      "AutoStop_Condition": "LessThan",
      "AutoStop_Description": "Alert to stop the VM if the CPU % exceed the threshold",
      "AutoStop_Frequency": "00:05:00",
      "AutoStop_MetricName": "Percentage CPU",
      "AutoStop_Severity": "2",
      "AutoStop_Threshold": "5",
      "AutoStop_TimeAggregationOperator": "Average",
      "AutoStop_TimeWindow": "06:00:00",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "ResourceGroups": [
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/",
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroupsvmrg2/",
          "/subscriptions/12345678-2222-4444-5555-1234567891234/resourceGroups/VMHostingRG/"
          ]
      }
    }
    ```

    Si vous souhaitez gérer un ensemble spécifique de machines virtuelles dans l’abonnement, modifiez le corps de la demande comme indiqué dans l’exemple suivant. Chaque chemin de ressource spécifié doit être séparé par une virgule. Vous pouvez spécifier une machine virtuelle si nécessaire.

    ```json
    {
      "Action": "stop",
      "AutoStop_Condition": "LessThan",
      "AutoStop_Description": "Alert to stop the VM if the CPU % exceed the threshold",
      "AutoStop_Frequency": "00:05:00",
      "AutoStop_MetricName": "Percentage CPU",
      "AutoStop_Severity": "2",
      "AutoStop_Threshold": "5",
      "AutoStop_TimeAggregationOperator": "Average",
      "AutoStop_TimeWindow": "06:00:00",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "VMLists": [
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/rg3/providers/Microsoft.ClassicCompute/virtualMachines/Clasyvm11",
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/providers/Microsoft.Compute/virtualMachines/vm1"
        ]
      }
    }
    ```

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment surveiller l’état de vos machines virtuelles Azure gérées par la fonctionnalité Start/Stop VMs v2 (préversion) et effectuer d’autres tâches de gestion, consultez l’article [Gérer Start/Stop VMs](manage.md).
