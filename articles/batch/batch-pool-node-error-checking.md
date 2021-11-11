---
title: Rechercher les erreurs des pools et des nœuds
description: Cet article décrit les opérations d'arrière-plan qui peuvent se produire, ainsi que les erreurs à rechercher et comment les éviter lors de la création de pools et de nœuds.
author: mscurrell
ms.author: markscu
ms.date: 03/15/2021
ms.topic: how-to
ms.openlocfilehash: 295877bd894d9a91ba310d133c670a40db5f10ec
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131454938"
---
# <a name="check-for-pool-and-node-errors"></a>Rechercher les erreurs des pools et des nœuds

Lorsque vous créez et gérez des pools Azure Batch, certaines opérations se produisent immédiatement. La détection des échecs de ces opérations est généralement simple, car ils sont retournés immédiatement par l’API, l’interface de ligne de commande ou l’interface utilisateur. Toutefois, certaines opérations sont asynchrones et s’exécutent en arrière-plan, ce qui prend plusieurs minutes.

Vérifiez que vous avez paramétré vos applications pour implémenter le contrôle complet des erreurs, en particulier pour les opérations asynchrones. Cela peut vous aider à identifier et diagnostiquer rapidement les problèmes.

Cet article explique comment détecter et éviter les échecs d’opérations en arrière-plan susceptibles de se produire pour des pools et nœuds de pool.

## <a name="pool-errors"></a>Erreurs de pool

### <a name="resize-timeout-or-failure"></a>Dépassement du délai d’attente ou échec du redimensionnement

Lors de la création d’un pool ou du redimensionnement d’un pool existant, vous spécifiez le nombre de nœuds cible. L’opération de création ou de redimensionnement se termine immédiatement, mais l’allocation réelle des nouveaux nœuds ou la suppression de nœuds existants peut prendre plusieurs minutes. Vous pouvez spécifier le délai d’attente du redimensionnement dans l’API de [création](/rest/api/batchservice/pool/add) ou de [redimensionnement](/rest/api/batchservice/pool/resize). Si Batch ne peut pas obtenir le nombre cible de nœuds pendant le délai d'attente du redimensionnement, le pool passe à l’état stable et signale des erreurs de redimensionnement.

La propriété [ResizeError](/rest/api/batchservice/pool/get#resizeerror) de l’évaluation la plus récente répertorie toutes les erreurs survenues.

Les causes courantes des erreurs de redimensionnement sont notamment les suivantes :

- Le délai d’expiration du redimensionnement est trop court
  - Dans la plupart des cas, le délai d’attente par défaut de 15 minutes est suffisamment long pour que les nœuds de pool puissent être affectés ou supprimés.
  - Si vous allouez un grand nombre de nœuds, nous vous recommandons de régler le délai d’expiration du redimensionnement à 30 minutes. Par exemple, lorsque vous redimensionnez plus de 1 000 nœuds à partir d’une image Place de marché Microsoft Azure, ou plus de 300 nœuds à partir d’une image de machine virtuelle personnalisée.
- Quota de cœurs insuffisant
  - Un compte Batch est limité dans le nombre de cœurs qu’il peut allouer dans tous les pools. Batch arrête l’allocation de nœuds une fois que le quota a été atteint. Vous [ pouvez augmenter](./batch-quota-limit.md) le quota de cœurs pour que Batch puisse allouer plus de nœuds.
- Nombre insuffisant d’adresses IP du sous-réseau quand un [pool est dans un réseau virtuel](./batch-virtual-network.md)
  - Un sous-réseau de réseau virtuel doit avoir suffisamment d’adresses IP non affectées à allouer à chaque nœud du pool demandés. Sinon, les nœuds ne peuvent pas être créés.
- Ressources insuffisantes quand un [pool est dans un réseau virtuel](./batch-virtual-network.md)
  - Vous pouvez créer des ressources comme des équilibreurs de charge, des adresses IP publiques et des groupes de sécurité réseau dans le même abonnement que le compte Batch. Vérifiez que les quotas de l’abonnement sont suffisants pour ces ressources.
- Vastes pools avec images de machines virtuelles personnalisées
  - L’allocation de vastes pools utilisant des images de machine virtuelle personnalisées peut prendre plus de temps et des dépassements du délai d’expiration peuvent se produire.  Pour obtenir des recommandations sur les limites et la configuration, consultez [Créer un pool avec Azure Compute Gallery](batch-sig-images.md).

### <a name="automatic-scaling-failures"></a>Échecs de mise à l’échelle automatique

Vous pouvez configurer Azure Batch de façon à mettre automatiquement à l’échelle le nombre de nœuds dans un pool. Vous définissez les paramètres de la [formule de mise à l’échelle automatique d’un pool](./batch-automatic-scaling.md). Le service Batch utilise ensuite la formule pour évaluer régulièrement le nombre de nœuds du pool et définir un nouveau nombre de cibles.

Les types de problèmes suivants peuvent se produire lors de l’utilisation d’une mise à l’échelle automatique :

- L’évaluation de la mise à l’échelle automatique échoue.
- L’opération de redimensionnement qui en résulte échoue et dépasse le délai d’expiration.
- Un problème portant sur la formule de mise à l’échelle automatique produit des valeurs cibles de nœud incorrectes. Le redimensionnement fonctionne ou arrive à expiration.

Pour obtenir des informations sur la dernière évaluation de mise à l’échelle automatique, utilisez la propriété [autoScaleRun](/rest/api/batchservice/pool/get#autoscalerun). Cette propriété rapporte le temps d’évaluation, les valeurs et les résultats, ainsi que les éventuelles erreurs de performance.

Un [événement de fin de redimensionnement de pool](./batch-pool-resize-complete-event.md) capture les informations sur toutes les évaluations.

### <a name="pool-deletion-failures"></a>Échecs de suppression de pool

Lorsque vous supprimez un pool qui contient des nœuds, le premier Batch supprime les nœuds. L’exécution de cette commande peut prendre plusieurs minutes. Ensuite, Batch supprime l’objet pool proprement dit.

Batch définit l’[état du pool](/rest/api/batchservice/pool/get#poolstate) sur **En cours de suppression** pendant le processus de suppression. L’application appelante peut détecter si la suppression du pool prend trop de temps en utilisant les propriétés **state** et **stateTransitionTime**.

Si la suppression du pool prend plus de temps que prévu, Batch réessayera régulièrement jusqu’à ce que le pool soit supprimé. Dans certains cas, le retard est dû à une interruption du service Azure ou à d’autres problèmes temporaires. D’autres facteurs pouvant empêcher la suppression d’un pool peuvent vous obliger à prendre des mesures pour corriger le problème. Ces facteurs sont les suivants :

- Des verrous de ressources ont été placés sur des ressources créées par Batch, ou sur des ressources réseau utilisées par Batch.
- Les ressources que vous avez créées ont une dépendance vis-à-vis d’une ressource créée par Batch. Par exemple, si vous [créez un pool dans un réseau virtuel](batch-virtual-network.md), Batch crée un groupe de sécurité réseau (NSG), une IP publique et un équilibreur de charge. Si vous utilisez ces ressources en dehors du pool, le pool ne peut pas être supprimé tant que cette dépendance n’est pas supprimée.
- Le fournisseur de ressources Microsoft.Batch a été désinscrit de l’abonnement qui contient votre pool.
- « Microsoft Azure Batch » n’a plus le [rôle Contributeur ou Propriétaire](batch-account-create-portal.md#allow-azure-batch-to-access-the-subscription-one-time-operation) pour l’abonnement qui contient votre pool (pour les comptes Batch en mode abonnement utilisateur).

## <a name="node-errors"></a>Erreurs de nœud

Même lorsque Batch alloue des nœuds d’un pool, différents problèmes peuvent les endommager ou les rendre incapables d’exécuter des tâches. Ces nœuds entraînent toujours des frais. Il est donc important de détecter les problèmes afin d’éviter de payer pour des nœuds qui ne peuvent pas être utilisés. En plus des erreurs de nœud courantes, il est utile de connaître l’[état actuel de la tâche](/rest/api/batchservice/job/get#jobstate) en vue de la résolution des problèmes.

### <a name="start-task-failures"></a>Échecs de la tâche de démarrage

Vous souhaiterez peut-être indiquer une [tâche de démarrage](/rest/api/batchservice/pool/add#starttask) facultative pour un pool. Comme avec n’importe quelle tâche, vous pouvez utiliser une ligne de commande et des fichiers de ressources à télécharger à partir du stockage. La tâche de démarrage est exécutée pour chaque nœud après son lancement. La propriété **waitForSuccess** indique si Batch attend que la tâche de démarrage se termine avec succès avant de planifier des tâches vers un nœud.

Que se passe-t-il si vous avez configuré le nœud de sorte qu’il attende l’achèvement réussi de la tâche de démarrage mais que la tâche de démarrage échoue ? Dans ce cas, le nœud ne sera pas utilisable, mais il entraînera toujours des frais.

Vous pouvez détecter les échecs des tâches de démarrage via les propriétés [result](/rest/api/batchservice/computenode/get#taskexecutionresult) et [failureInfo](/rest/api/batchservice/computenode/get#taskfailureinformation) de la propriété du nœud [startTaskInfo](/rest/api/batchservice/computenode/get#starttaskinformation) du plus haut niveau.

Une tâche de démarrage qui a échoué fait passer [l’état](/rest/api/batchservice/computenode/get#computenodestate) du nœud à **starttaskfailed** si **waitForSuccess** a été défini sur **true**.

Comme toute tâche, une tâche de démarrage peut échouer pour diverses raisons. Pour résoudre les problèmes, examinez les fichiers journaux stdout, stderr et tous les fichiers journaux spécifiques aux tâches.

Les tâches de démarrage doivent être réentrantes, car il est possible que la tâche de démarrage soit exécutée plusieurs fois sur le même nœud ; la tâche de démarrage est exécutée lors de la réinitialisation ou du redémarrage d’un nœud. Dans de rares cas, une tâche de démarrage est exécutée après un événement provoquant un redémarrage du nœud, où l’un des systèmes d’exploitation ou des disques éphémères a été réinitialisé alors que l’autre ne l’a pas été. Étant donné que les tâches de démarrage de Batch (comme toutes les tâches Batch) sont exécutées à partir du disque éphémère, ce n’est normalement pas un problème. Cependant, dans certains cas, où la tâche de démarrage installe une application sur le disque du système d'exploitation et conserve les autres données sur le disque éphémère, des problèmes liés à l’absence de synchronisation peuvent survenir. Protégez votre application en conséquence si vous utilisez les deux disques.

### <a name="application-package-download-failure"></a>Échec du téléchargement des packages d’application

Vous pouvez spécifier un ou plusieurs packages d’application pour un pool. Batch télécharge les fichiers de package spécifiés vers chaque nœud et décompresse les fichiers après le démarrage du nœud, mais avant que les tâches ne soient planifiées. Il est courant d’utiliser une ligne de commande de tâche de démarrage conjointement avec des packages d’application. Par exemple, pour copier des fichiers vers un emplacement différent ou exécuter le programme d’installation.

La propriété [errors](/rest/api/batchservice/computenode/get#computenodeerror) du nœud rapporte un échec du téléchargement et de décompression d’un package d’application ; l’état du nœud est défini sur **inutilisable**.

### <a name="container-download-failure"></a>Échec du téléchargement du conteneur

Vous pouvez spécifier une ou plusieurs références de conteneur sur un pool. Batch télécharge les conteneurs spécifiés pour chaque nœud. La propriété [erreurs](/rest/api/batchservice/computenode/get#computenodeerror) du nœud signale l’échec de téléchargement d’un conteneur et définit l’état du nœud sur **inutilisable**.

### <a name="node-os-updates"></a>Mises à jour de système d’exploitation de nœud

Pour des pools Windows, `enableAutomaticUpdates` est défini sur `true` par défaut. S’il est recommandé d’autoriser les mises à jour automatiques, celles-ci peuvent interrompre la progression des tâches, en particulier de longue durée. Vous pouvez définir cette valeur sur `false` pour vous assurer qu’une mise à jour du système d’exploitation ne se produise pas de manière inattendue.

### <a name="node-in-unusable-state"></a>Nœud dans un état inutilisable

Azure Batch peut définir [l’état du nœud](/rest/api/batchservice/computenode/get#computenodestate) sur **inutilisable** pour de nombreuses raisons. Quand l’état du nœud est défini sur **inutilisable**, il n’est pas possible de planifier des tâches pour le nœud, mais il continue d’entraîner la facturation de frais.

Lorsque des nœuds sont en état **inutilisable** sans [erreurs](/rest/api/batchservice/computenode/get#computenodeerror), cela signifie que Batch n’est pas en mesure de communiquer avec la machine virtuelle. Dans ce cas, Batch tente toujours de récupérer la machine virtuelle. Batch ne tente pas automatiquement de récupérer les machines virtuelles qui n’ont pas réussi à installer les packages d’applications ou les conteneurs, même si leur état est **inutilisable**.

Si Batch peut déterminer la cause de l’erreur, Batch la signale à l’aide de la propriété [errors](/rest/api/batchservice/computenode/get#computenodeerror).

Voici des exemples supplémentaires de causes aboutissant à des nœuds **inutilisables** :

- Une image de machine virtuelle personnalisée n’est pas valide. Par exemple, il s’agit d’une image qui n’est pas correctement préparée.
- Une machine est déplacée en raison d’une défaillance de l’infrastructure ou d’une mise à niveau de bas niveau. Batch récupère le nœud.
- Une image de machine virtuelle a été déployée sur un matériel qui ne la prend pas en charge. Par exemple, si vous essayez d’exécuter une image HPC CentOS sur une machine virtuelle [Standard_D1_v2](../virtual-machines/dv2-dsv2-series.md).
- Les machines virtuelles sont dans un [réseau virtuel Azure](batch-virtual-network.md), et le trafic a été bloqué pour les ports principaux.
- Les machines virtuelles se trouvent dans un réseau virtuel, mais le trafic sortant vers le stockage Azure est bloqué.
- Les machines virtuelles se trouvent dans un réseau virtuel avec une configuration DNS du client et le serveur DNS ne peut pas résoudre le stockage Azure.

### <a name="node-agent-log-files"></a>Fichiers de journaux de l’agent du nœud

Si vous devez contacter le support technique concernant un problème de nœud de pool, le processus de l’agent Batch qui s’exécute sur chaque nœud du pool peut vous fournir des fichiers journaux très utiles. Les fichiers journaux pour un nœud peuvent être chargés via le portail Azure, Batch Explorer ou une [API](/rest/api/batchservice/computenode/uploadbatchservicelogs). Il est utile de charger et d’enregistrer les fichiers journaux. Ensuite, vous pouvez supprimer le nœud ou le pool pour maîtriser le coût des nœuds en cours d’exécution.

### <a name="node-disk-full"></a>Disque de nœud plein

Batch utilise le lecteur temporaire d’une machine virtuelle de nœud de pool pour des fichiers de travail, des fichiers de tâche et des fichiers partagés tels que les suivants :

- Fichiers de packages d’applications
- Fichiers de ressources de tâche
- Fichiers spécifiques à l’application téléchargés vers l’un des dossiers Batch
- Fichiers stdout et stderr pour chaque exécution d’application de tâche
- Fichiers de sortie spécifiques à l’application

Certains de ces fichiers sont écrits une seule fois lors de la création de nœuds de pool, tels que les packages d’applications de pools ou les fichiers de ressources de tâche de démarrage de pool. Même s’ils sont écrits une seule fois lors de la création du nœud, ces fichiers peuvent remplir le lecteur temporaire s’ils sont trop volumineux.

D’autres fichiers sont écrits pour chaque tâche exécutée sur un nœud, par exemple stdout et stderr. Si un grand nombre de tâches s’exécutent sur le même nœud et/ou que les fichiers de tâches sont trop volumineux, ils peuvent remplir le lecteur temporaire.

En outre, une fois le nœud démarré, une petite quantité d’espace est nécessaire sur le disque du système d’exploitation pour créer des utilisateurs.

La taille du lecteur temporaire dépend de la taille de la machine virtuelle. L’un des aspects à prendre en compte lors de la sélection d’une taille de machine virtuelle est de veiller à ce que le lecteur temporaire dispose d’un espace suffisant pour la charge de travail planifiée.

- Dans le portail Azure, lors de l’ajout d’un pool, la liste complète des tailles de machines virtuelles peut être affichée et il existe une colonne « Taille du disque de ressources ».
- Les articles décrivant toutes les tailles de machines virtuelles, par exemple [Tailles de machines virtuelles optimisées pour le calcul](../virtual-machines/sizes-compute.md), ont des tables avec une colonne « Stockage temporaire ».

Concernant les fichiers écrits par chaque tâche, une durée de rétention déterminant la durée pendant laquelle ils seront conservés avant d’être automatiquement nettoyés peut respectivement être spécifiée. La durée de rétention peut être raccourcie pour réduire les exigences de stockage.

Si le disque temporaire ou le disque du système d’exploitation manque (ou est sur le point de manquer) d’espace, le nœud passe à l’état [Inutilisable](/rest/api/batchservice/computenode/get#computenodestate), et une erreur de nœud est signalée, indiquant que le disque est plein.

Si vous n’êtes pas certain de ce qui occupe de l’espace sur le nœud, essayez d’y accéder à distance et d’examiner manuellement l’utilisation de l’espace. Vous pouvez également recourir à [l’API Batch Lister les fichiers](/rest/api/batchservice/file/listfromcomputenode) pour examiner les fichiers des dossiers gérés par Batch (par exemple, les sorties de tâches). Notez que cette API répertorie uniquement les fichiers figurant dans les répertoires gérés par Batch. Si vos tâches ont créé des fichiers ailleurs, vous ne les verrez pas.

Vérifiez que toutes les données dont vous avez besoin ont été récupérées à partir du nœud ou chargées dans un magasin durable, puis supprimez les données nécessaires pour libérer de l’espace.

Vous pouvez supprimer d’anciens travaux ou tâches accomplis dont les données se trouvent toujours sur les nœuds. Examinez la collection [RecentTasks](/rest/api/batchservice/computenode/get#taskinformation) sur le nœud, ou les [fichiers sur le nœud](/rest/api/batchservice/file/listfromcomputenode). La suppression d’un travail entraîne la suppression de toutes les tâches associées à celui-ci. La suppression des tâches du travail a pour effet de supprimer des données des répertoires de tâches sur le nœud, et donc de libérer de l’espace. Une fois que vous avez libéré suffisamment d’espace, redémarrez le nœud ; il devrait repasser de l’état « Inutilisable » à l’état « Inactif ».

Pour récupérer un nœud inutilisable dans des pools [VirtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration), vous pouvez supprimer un nœud du pool à l’aide de l’[API Suppression de nœuds](/rest/api/batchservice/pool/removenodes), puis agrandir le pool pour remplacer le mauvais nœud par un nouveau. Pour les pools [CloudServiceConfiguration](/rest/api/batchservice/pool/add#cloudserviceconfiguration), vous pouvez réimager le nœud via l’[API Batch Recréation d’image](/rest/api/batchservice/computenode/reimage). Cela a pour effet de nettoyer le disque entier. La recréation d’image n’est à l’heure actuelle pas prise en charge pour les pools [VirtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez la [vérification des erreurs de travail et de tâche](batch-job-task-error-checking.md).
- Découvrez les [meilleures pratiques](best-practices.md) d’utilisation d’Azure Batch.
