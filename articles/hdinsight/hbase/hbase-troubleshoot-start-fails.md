---
title: Échec du démarrage d’Apache HBase Master dans Azure HDInsight
description: Échec du démarrage d’Apache HBase Master (HMaster) dans Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/07/2021
ms.openlocfilehash: 7870aa48a75de6a5443298d909f0fe7d59311a78
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061622"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>Échec du démarrage d’Apache HBase Master (HMaster) dans Azure HDInsight

Cet article décrit les éventuelles solutions à appliquer pour résoudre les problèmes rencontrés lors d’interactions avec des clusters Azure HDInsight.

## <a name="scenario-master-startup-cannot-progress-in-holding-pattern-until-region-comes-online"></a>Scénario : Le démarrage du Master ne peut pas progresser, car il est en d’attente tant que la région n’est pas en ligne.

### <a name="issue"></a>Problème

HMaster ne parvient pas à démarrer en raison de l’avertissement suivant :
```output
hbase:namespace,,<timestamp_region_create>.<encoded_region_name>.is NOT online; state={<encoded_region_name> state=OPEN, ts=<some_timestamp>, server=<server_name>}; ServerCrashProcedures=true. Master startup cannot progress, in holding-pattern until region onlined. 
```

Par exemple, les valeurs des paramètres peuvent varier dans le message réel :
```output
hbase:namespace,,1546588612000.0000010bc582e331e3080d5913a97000. is NOT online; state={0000010bc582e331e3080d5913a97000 state=OPEN, ts=1633935993000, server=<wn fqdn>,16000,1622012792000}; ServerCrashProcedures=false. Master startup cannot progress, in holding-pattern until region onlined.
```

### <a name="cause"></a>Cause

HMaster recherche le répertoire WAL sur les serveurs de la région avant de remettre en ligne les régions **OUVERTES**. Dans ce cas, si cet annuaire n’était pas présent, il n’a pas démarré

### <a name="resolution"></a>Résolution

1. Créez cet annuaire factice à l’aide de la commande suivante : `sudo -u hbase hdfs dfs -mkdir /hbase-wals/WALs/<wn fqdn>,16000,1622012792000`.

2. Redémarrez le service HMaster à partir de l’interface utilisateur Ambari.

## <a name="scenario-atomic-renaming-failure"></a>Scénario : défaillance renommage atomique

### <a name="issue"></a>Problème

Fichiers inattendus identifiés au cours du processus de démarrage.

### <a name="cause"></a>Cause

Pendant le processus de démarrage, HMaster effectue de nombreuses étapes d’initialisation, notamment le déplacement des données du dossier temporaire (. tmp) vers le dossier de données. HMaster examine également le dossier de journaux d’activité WAL (write-ahead log) pour voir s’il existe des serveurs régionaux ne répondant pas.

HMaster effectue une commande de liste de base sur les dossiers WAL. S’il rencontre un fichier inattendu dans l’un de ces dossiers, il envoie une exception et ne démarre pas.

### <a name="resolution"></a>Résolution

Vérifiez la pile des appels et essayez de déterminer quel dossier pourrait être responsable du problème (par exemple le dossier de journaux WAL ou le dossier .tmp). Ensuite, dans Cloud Explorer ou à l’aide de commandes HDFS, tentez de localiser le fichier posant problème. En règle générale, il s’agit d’un fichier `*-renamePending.json`. (Le fichier `*-renamePending.json` est un fichier journal utilisé pour implémenter l’opération de renommage atomique au niveau du pilote WASB. En raison des bogues de cette implémentation, ces fichiers peuvent rester après le blocage du processus, etc.) Forcez la suppression de ce fichier soit dans Cloud Explorer, soit à l’aide de commandes HDFS.

Dans certains cas, cet emplacement contient également un fichier temporaire nommé `$$$.$$$`. Vous devez utiliser la commande HDFS `ls` pour voir ce fichier ; vous ne pouvez pas le voir dans Cloud Explorer. Pour supprimer ce fichier, utilisez la commande HDFS `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$`.

Après avoir exécuté ces commandes, HMaster devrait démarrer immédiatement.

---

## <a name="scenario-no-server-address-listed"></a>Scénario : aucune adresse de serveur répertoriée

### <a name="issue"></a>Problème

Vous pouvez voir un message indiquant que la table `hbase: meta` n’est pas en ligne. L’exécution de `hbck` peut indiquer `hbase: meta table replicaId 0 is not found on any region.` Dans les journaux d’activité HMaster, vous pouvez voir le message: `No server address listed in hbase: meta for region hbase: backup <region name>`.  

### <a name="cause"></a>Cause

HMaster n’a pas pu être initialisé après le redémarrage de HBase.

### <a name="resolution"></a>Résolution

1. Entrez la commande suivante dans l’interpréteur de commandes HBase (modifiez les valeurs le cas échéant) :

    ```hbase
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>'
    ```

1. Supprimez l’entrée `hbase: namespace`. Cette entrée peut être à l’origine de l’erreur signalée quand la table `hbase: namespace` est analysée.

1. Redémarrez le service HMaster actif à partir de l’interface utilisateur Ambari pour rétablir le fonctionnement de HBase.

1. Dans l’interpréteur de commandes HBase, exécutez la commande suivante pour afficher toutes les tables hors connexion :

    ```hbase
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>Scénario : java.io.IOException : Timedout

### <a name="issue"></a>Problème

HMaster arrive à expiration avec une exception irrécupérable similaire à `java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned`.

### <a name="cause"></a>Cause

Vous pourriez rencontrer ce problème si plusieurs tables et régions n’ont pas été vidées lors du redémarrage de vos services HMaster. Le délai d’attente est un défaut connu de HMaster. Les tâches générales de démarrage du cluster peuvent prendre beaucoup de temps. HMaster s’arrête si la table namespace n’a pas encore été affectée. Les tâches de démarrage fastidieuses se produisent quand une grande quantité de données n’a pas été vidée et qu’un délai d’attente de cinq minutes est insuffisant.

### <a name="resolution"></a>Résolution

1. Dans l’interface utilisateur d’Apache Ambari, accédez à **HBase** > **Configs**. Dans le fichier `hbase-site.xml` personnalisé, ajoutez le paramètre suivant :

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. Redémarrez les services requis (HMaster et éventuellement d’autres services HBase).

---

## <a name="scenario-frequent-region-server-restarts"></a>Scénario : fréquent redémarrages de serveurs régionaux

### <a name="issue"></a>Problème

Les nœuds redémarrent périodiquement. Dans les journaux des serveurs régionaux, vous pouvez voir des entrées similaires à celles-ci :

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>Cause

Longue pause de nettoyage de la mémoire de machine virtuelle Java de `regionserver`. La pause entraîne l’absence de réponse de `regionserver` et l’impossibilité d’envoyer une pulsation à HMaster dans les 40 secondes de délai d’expiration de la session zk. HMaster estime que `regionserver` est inactif et l’abandonne, puis redémarre.

### <a name="resolution"></a>Résolution

Modifiez le délai d’expiration de la session Zookeeper ; non seulement le paramètre `hbase-site``zookeeper.session.timeout`, mais également le paramètre Zookeeper `zoo.cfg``maxSessionTimeout` doit être modifié.

1. Dans l’interface utilisateur Ambari, accédez à **HBase -> Configs -> Paramètres**, dans la section Délais d’expiration, modifiez la valeur du délai d’expiration de la session Zookeeper.

1. Dans l’interface utilisateur Ambari, accédez à **Zookeeper -> Configs -> Custom (Personnalisée)** `zoo.cfg`, puis ajoutez ou modifiez le paramètre suivant. Vérifiez que la valeur est identique à HBase `zookeeper.session.timeout`.

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. Redémarrez les services requis.

---

## <a name="scenario-log-splitting-failure"></a>Scénario : défaillance fractionnement de journaux

### <a name="issue"></a>Problème

HMaster n’apparaît pas sur un cluster HBase.

### <a name="cause"></a>Cause

Paramètres HDFS et HBase mal configurés pour un compte de stockage secondaire.

### <a name="resolution"></a>Résolution

`set hbase.rootdir: wasb://@.blob.core.windows.net/hbase` et redémarrez les services sur Ambari.

---

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [notes](../includes/hdinsight-troubleshooting-next-steps.md)]
