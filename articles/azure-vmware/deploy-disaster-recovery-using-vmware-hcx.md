---
title: Déployer la reprise d’activité avec VMware HCX
description: Découvrez comment déployer la récupération d’urgence de vos machines virtuelles avec la récupération d’urgence de VMware HCX. Découvrez également comment utiliser Azure VMware Solution comme site de récupération ou cible.
ms.topic: how-to
ms.date: 06/10/2021
ms.openlocfilehash: f08f1c1ccff0387c5750a355f83de02e1dcd8e09
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113430985"
---
# <a name="deploy-disaster-recovery-using-vmware-hcx"></a>Déployer la reprise d’activité avec VMware HCX

Dans cet article, vous déployez le processus de récupération d’urgence de vos machines virtuelles avec la solution VMware HCX et en utilisant un cloud privé Azure VMware Solution en tant que site de récupération ou cible.

>[!IMPORTANT]
>Bien qu’elle fasse partie de HCX, la récupération d’urgence de VMware HCX n’est pas recommandée pour les déploiements à grande échelle. L’orchestration de la récupération d’urgence est 100 % manuelle, et la solution Azure VMware ne dispose actuellement pas de runbooks ou de fonctionnalités pour prendre en charge le basculement de récupération d’urgence HCX manuel. Pour la récupération d’urgence de niveau entreprise, reportez-vous aux solutions VMware Site Recovery Manager (SRM) ou VMware Business Continuation and Disaster Recovery (BCDR).

La solution VMware HCX exécute diverses opérations qui permettent de contrôler avec souplesse et précision les stratégies de réplication. Les opérations disponibles sont les suivantes :

- **Inverser** : après un incident. Une inversion permet de faire d’un site B le site source, et d’un site A l’emplacement de résidence de la machine virtuelle protégée.

- **Mettre en pause** : met en pause la stratégie de réplication actuelle associée à la machine virtuelle sélectionnée.

- **Reprendre** : reprend la stratégie de réplication actuelle associée à la machine virtuelle sélectionnée.

- **Supprimer** : supprime la stratégie de réplication actuelle associée à la machine virtuelle sélectionnée.

- **Synchroniser maintenant** : de la machine virtuelle source de synchronisation hors limites vers la machine virtuelle protégée.

Ce guide présente les scénarios de réplication suivants :

- Protéger une machine virtuelle ou un groupe de machines virtuelles.

- Effectuer un test de récupération d’une machine virtuelle ou d’un groupe de machines virtuelles.

- Récupérer une machine virtuelle ou un groupe de machines virtuelles.

- Inverser la protection d’une machine virtuelle ou d’un groupe de machines virtuelles.

## <a name="protect-vms"></a>Protéger les machines virtuelles

1. Connectez-vous au **Client vSphere** sur le site source, puis accédez au **plug-in HCX**.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/hcx-vsphere.png" alt-text="Capture d’écran montrant l’option HCX dans le Client Web vSphere." border="true":::

1. Entrez dans la zone **Récupération d’urgence** et sélectionnez **Protéger les machines virtuelles**.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machine.png" alt-text="Capture d’écran montrant le tableau de bord de récupération d’urgence dans le Client Web vSphere." border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-virtual-machine.png":::

1. Sélectionnez la source et les sites distants. Dans ce cas, le site distant doit être le cloud privé Azure VMware Solution.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machines.png" alt-text="Capture d’écran montrant la fenêtre HCX : fenêtre Machines virtuelles protégées." border="true":::

1. Si nécessaire, sélectionnez les options de **réplication par défaut** :

   - **Activer la compression :** option recommandée pour les scénarios à faible débit.

   - **Enable l’arrêt inactif :** suspend l’activité de la machine virtuelle pour s’assurer de la cohérence de la copie synchronisée avec le site distant.

   - **Stockage de destination :** Magasin de données distant pour les machines virtuelles protégées et, dans un cloud privé Azure VMware Solution, il doit s’agir du magasin de données vSAN.

   - **Conteneur de calcul :** cluster vSphere ou pool de ressources distants.

   - **Dossier de destination :** dossier de destination distant. Ce paramètre est facultatif et, à défaut de dossier sélectionné, les machines virtuelles sont placées directement dans le cluster sélectionné.

   - **RPO :** intervalle de synchronisation entre la machine virtuelle source et la machine virtuelle protégée. Il peut être compris entre 5 minutes et 24 mois.

   - **Intervalle des captures instantanées :** intervalle entre les captures instantanées.

   - **Nombre de captures instantanées :** nombre total de captures instantanées au cours de l’intervalle configuré.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machine-options.png" alt-text="Capture d’écran montrant les options de réplication Protect Virtual Machines." border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-virtual-machine-options.png":::

1. Sélectionnez une ou plusieurs machines virtuelles dans la liste, puis configurez les options de réplication nécessaires.

   Par défaut, les machines virtuelles héritent de la stratégie de paramètres globaux configurée dans les options de réplication par défaut. Pour chaque interface réseau de la machine virtuelle sélectionnée, configurez le **Groupe de ports réseau** distant, puis **Terminer** pour démarrer le processus de protection.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/network-interface-options.png" alt-text="Capture d’écran montrant les options d’interface réseau de Protect Virtual Machines." border="true" lightbox="./media/disaster-recovery-virtual-machines/network-interface-options.png":::

1. Surveillez le processus pour chacune des machines virtuelles sélectionnées dans la même zone de récupération d’urgence.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-monitor-progress.png" alt-text="Capture d’écran montrant la progression de la protection de Protect Virtual Machines." border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-monitor-progress.png":::

1. Une fois la machine virtuelle protégée, vous pouvez afficher les différents instantanés sous l’onglet **Instantanés**.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/list-of-snapshots.png" alt-text="Capture d’écran montrant une liste de clichés instantanés Protect Virtual Machines." border="true" lightbox="./media/disaster-recovery-virtual-machines/list-of-snapshots.png":::

   Le triangle jaune signifie que les instantanés et les machines virtuelles n’ont pas été testés dans le cadre d’une opération de test de récupération.

   Il existe des différences clés entre une machine virtuelle hors tension et une machine virtuelle sous tension. L’image montre le processus de synchronisation d’une machine virtuelle sous tension. Celui-ci entame la synchronisation jusqu’à la prise du premier instantané, qui est une copie complète de la machine virtuelle, puis prend les captures suivantes dans l’intervalle configuré. Il synchronise une copie de machine virtuelle hors tension, puis la machine virtuelle apparaît inactive et l’opération de protection s’affiche comme terminée.  Lorsque la machine virtuelle est sous tension, elle démarre le processus de synchronisation sur le site distant.

## <a name="complete-a-test-recover-of-vms"></a>Effectuer un test de récupération des machines virtuelles

1. Connectez-vous au **Client vSphere** sur le site distant, qui est le cloud privé Azure VMware Solution. 
1. Dans le **plug-in HCX**, dans la zone Récupération d’urgence, sélectionnez l’ellipse verticale sur une machine virtuelle pour afficher le menu Opérations, puis sélectionnez **Tester la récupération de la machine virtuelle**.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/test-recover-virtual-machine.png" alt-text="Capture d’écran montrant l’option de menu Test Recovery VM." border="true":::

1. Sélectionnez les options du test et de l’instantané que vous souhaitez utiliser pour tester les différents états de la machine virtuelle.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/choose-snapshot.png" alt-text="Capture d’écran montrant l’instance d’instantané de réplica à tester." border="true":::

1. Après avoir sélectionné **Test**, l’opération de récupération commence.

1. Lorsque vous avez terminé, vous pouvez vérifier la nouvelle machine virtuelle dans le cloud privé vCenter Azure VMware Solution.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/verify-test-recovery.png" alt-text="Capture d’écran montrant le résumé de l’opération de vérification de la récupération." border="true" lightbox="./media/disaster-recovery-virtual-machines/verify-test-recovery.png":::

1. Une fois les tests effectués sur la machine virtuelle ou toute application s’exécutant sur celle-ci, effectuez un nettoyage pour supprimer l’instance de test.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/cleanup-test-instance.png" alt-text="Capture d’écran montrant l’instance de test de nettoyage." border="true" lightbox="./media/disaster-recovery-virtual-machines/cleanup-test-instance.png":::

## <a name="recover-vms"></a>Récupérer les machines virtuelles

1. Connectez-vous au **Client vSphere** sur le site distant, qui est le cloud privé Azure VMware Solution, puis accédez au **plug-in HCX**.

   Pour le scénario de récupération, un groupe de machines virtuelles est utilisé pour cet exemple.

1. Sélectionnez la machine virtuelle à récupérer dans la liste, ouvrez le menu **ACTIONS**, puis sélectionnez **Récupérer des machines virtuelles**.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/recover-virtual-machines.png" alt-text="Capture d’écran montrant l’option de menu Récupérer des machines virtuelles." border="true":::

1. Configurez les options de récupération pour chaque instance, puis sélectionnez **Récupérer** pour démarrer l’opération de récupération.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/recover-virtual-machines-confirm.png" alt-text="Capture d’écran montrant la confirmation de la récupération des machines virtuelles sur le site cible." border="true":::

1. Une fois l’opération de récupération terminée, les nouvelles machines virtuelles apparaissent dans l’inventaire du vCenter Server distant.

## <a name="complete-a-reverse-replication-on-vms"></a>Effectuer une réplication inverse sur des machines virtuelles

1. Connectez-vous au **Client vSphere** sur votre cloud privé Azure VMware Solution, puis accédez au **plug-in HCX**.
   
   >[!NOTE]
   > Vérifiez que les machines virtuelles d’origine sur le site source sont hors tension avant de lancer la réplication inversée. L’opération échoue si les machines virtuelles ne sont pas hors tension.

1. Dans la liste, sélectionnez les machines virtuelles à répliquer sur le site source, ouvrez le menu **ACTIONS**, puis sélectionnez **Inverser**. 
1. Sélectionnez **Inverser** pour lancer la réplication.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/reverse-operation-virtual-machines.png" alt-text="Capture d’écran montrant l’option de menu inverse." border="true":::

1. Analysez la section Détails de chaque machine virtuelle.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/review-reverse-operation.png" alt-text="Capture d’écran montrant les résultats d’une action inverse." border="true" lightbox="./media/disaster-recovery-virtual-machines/review-reverse-operation.png":::

## <a name="disaster-recovery-plan-automation"></a>Automatisation de plan de récupération d’urgence

Actuellement, la solution VMware HCX ne dispose d'aucun mécanisme intégré pour créer et automatiser un plan de récupération d'urgence. Toutefois, VMware HCX fournit un ensemble d’API REST, dont certaines pour l’opération Récupération d’urgence. La spécification de l’API est accessible dans VMware HCX Manager via l’URL.

Ces API couvrent les opérations de récupération d’urgence suivantes.

- Protéger

- Récupérer

- Tester la récupération

- Récupération planifiée

- Inverser

- Requête

- Tester le nettoyage

- Suspendre

- Reprendre

- Supprimer la protection

- Reconfigurer

Vous trouverez ci-dessous un exemple de charge utile d’opération de récupération dans un fichier JSON.

```json
[

    {

        "replicationId": "string",

        "needPowerOn": true,

        "instanceId": "string",

        "source": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "destination": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "placement": [

            {

                "containerType": "string",

                "containerId": "string"

            }

        ],

        "resourceId": "string",

        "forcePowerOff": true,

        "isTest": true,

        "forcePowerOffAfterTimeout": true,

        "isPlanned": true

    }

]
```

Ces API vous permettent de créer un mécanisme personnalisé afin d’automatiser la création et l’exécution d’un plan de récupération d’urgence.
