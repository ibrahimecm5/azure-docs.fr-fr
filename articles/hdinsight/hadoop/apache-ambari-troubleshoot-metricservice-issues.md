---
title: Problèmes de collecteur de métriques Apache Ambari dans Azure HDInsight
description: Résolution des problèmes de collecteur de métriques Apache Ambari dans Azure HDInsight
ms.service: hdinsight
ms.author: linjzhu
author: jacky-hdi
ms.topic: troubleshooting
ms.date: 11/03/2021
ms.openlocfilehash: 6fbfc4f47cb14966a7d03d9ef378bb03f519183c
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132064040"
---
# <a name="apache-ambari-metrics-collector-issues-in-azure-hdinsight"></a>Problèmes de collecteur de métriques Apache Ambari dans Azure HDInsight

Cet article décrit les éventuelles solutions à appliquer pour résoudre les problèmes rencontrés lors d’interactions avec des clusters Azure HDInsight.

## <a name="scenario-outofmemoryerror-or-unresponsive-apache-ambari-metrics-collector"></a>Scénario : OutOfMemoryError ou collecteur de métriques Apache Ambari ne répondant pas

### <a name="issue"></a>Problème

* Vous pourriez recevoir une alerte **« Processus du collecteur de métriques »** critique dans l’interface utilisateur d’Ambari et voir s’afficher un message similaire.
    `Connection failed: timed out to <headnode fqdn>:6188`
* Il se peut que le collecteur de métriques Ambari soit fréquemment redémarré dans le headnote
* Il se peut que certaines métriques Apache Ambari ne s’affichent pas. Par exemple, **NAMENODE** affiche l’état **Démarré** au lieu de l’état **Actif/Veille** 


### <a name="cause"></a>Cause

Les scénarios pouvant être à l’origine de ces problèmes sont les suivants :

#### <a name="an-out-of-memory-exception-happens-frequently"></a>Une exception de mémoire insuffisante se produit fréquemment

Consultez le journal du collecteur de métriques Apache Ambari `/var/log/ambari-metrics-collector/ambari-metrics-collector.log*`.

```
19:59:45,457 ERROR [325874797@qtp-2095573052-22] log:87 - handle failed
java.lang.OutOfMemoryError: Java heap space

19:59:45,457 FATAL [MdsLoggerSenderThread] YarnUncaughtExceptionHandler:51 - Thread Thread[MdsLoggerSenderThread,5,main] threw an Error.  Shutting down now...
java.lang.OutOfMemoryError: Java heap space
```

#### <a name="busy-garbage-collection"></a>Nettoyage de la mémoire (GC) occupé

1. Le collecteur de métriques Apache Ambari n’écoute pas sur **6188** dans le journal hbase-ams `/var/log/ambari-metrics-collector/hbase-ams-master-hn*.log`

   ```
   2021-04-13 05:57:37,546 INFO  [timeline] timeline.HadoopTimelineMetricsSink: No live collector to send metrics to. Metrics to be sent will be discarded. This message will be skipped for the next 20 times.
   ```
   
2. Obtient le PID du collecteur de métriques Apache Ambari et vérifie les performances de GC

   ```
   ps -fu ams | grep 'org.apache.ambari.metrics.AMSApplicationServer'
   ```
       
3. Vérifiez l’état du nettoyage de la mémoire à l’aide de `jstat -gcutil <pid> 1000 100`. Si vous voyez que la valeur **FGCT** augmente beaucoup en quelques secondes, cela indique que le collecteur de métriques Apache Ambari est complètement occupé par le tâche de GC et qu’il ne peut pas traiter d’autres demandes.

### <a name="resolution"></a>Résolution

Pour éviter ces problèmes, envisagez d’utiliser l’une des options suivantes :

1. Augmentez la mémoire de segment du collecteur de métriques Apache Ambari à partir de **Ambari** > **Collecteur de métriques Ambari** > **Configuration** > **Taille de segment du collecteur de métriques**.

   :::image type="content" source="./media/apache-ambari-troubleshoot-ams-issues/editing-ams-configuration-ambari.png" alt-text="Capture d’écran de la modification des propriétés de configuration du service métrique Ambari." border="true":::

2. Procédez comme suit pour nettoyer les données du service des métriques Ambari (AMS).

   > [!NOTE]
   > Le nettoyage des données de l’AMS supprime toutes les données de l’AMS historiques disponibles. Si vous avez besoin de l’historique, il se peut que ce ne soit pas la meilleure option.

   1.  Se connecter au portail Ambari  
    1.  Définir AMS pour la maintenance  
    2.  Arrêter AMS depuis Ambari  
    3.  Identifiez les valeurs suivantes dans l’écran **Configurations de l’AMS**  
                1.  `hbase.rootdir` (la valeur par défaut est `file:///mnt/data/ambari-metrics-collector/hbase`)  
                2.  `hbase.tmp.dir` (la valeur par défaut est `/var/lib/ambari-metrics-collector/hbase-tmp`)  
   2. Utilisez le protocole SSH dans le headnote où se trouve le collecteur de métriques Apache Ambari. En tant que superutilisateur :
    1. Supprimez les données d’AMS ZooKeeper en procédant à une **sauvegarde** ainsi qu’à la suppression du contenu de `'hbase.tmp.dir'/zookeeper`
    2. Supprimez tous les fichiers spouleur Phoenix depuis le dossier `<hbase.tmp.dir>/phoenix-spool` 
    3. ***(Il est judicieux d’ignorer cette étape initialement, puis de tenter de redémarrer l’AMS pour voir si le problème est résolu. Si l’exécution de l’AMS continue d’échouer, essayez cette étape)***  
            Les données AMS seront stockées dans le dossier `hbase.rootdir` mentionné ci-dessus. Utilisez les commandes du système d’exploitation habituelles pour sauvegarder et supprimer les fichiers. Exemple :     
            `tar czf /mnt/backupof-ambari-metrics-collector-hbase-$(date +%Y%m%d-%H%M%S).tar.gz /mnt/data/ambari-metrics-collector/hbase`  
   3.  Redémarrez l’AMS à l’aide d’Ambari.


## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]
