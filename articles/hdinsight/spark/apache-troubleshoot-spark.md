---
title: Résoudre les problèmes liés à Apache Spark dans Azure HDInsight
description: Obtenez les réponses aux questions courantes sur l’utilisation d’Apache Spark et d’Azure HDInsight.
ms.service: hdinsight
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/22/2019
ms.custom: seodec18
ms.openlocfilehash: 6841a6965646afbf4192c502c0a9eba260531f5e
ms.sourcegitcommit: 4f185f97599da236cbed0b5daef27ec95a2bb85f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2021
ms.locfileid: "112369071"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>Résolution de problèmes Apache Spark à l’aide d’Azure HDInsight

Découvrez les principaux problèmes rencontrés lors de l’utilisation de charges utiles Apache Spark dans [Apache Ambari](https://ambari.apache.org/) et leur résolution.

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>Comment configurer une application Apache Spark sur des clusters via Apache Ambari ?

Les valeurs de configuration Spark peuvent être paramétrées afin d’éviter l’exception `OutofMemoryError` de l’application Apache Spark. Les étapes suivantes montrent des valeurs de configuration Spark par défaut dans Azure HDInsight :

1. Connectez-vous à Ambari sur `https://CLUSTERNAME.azurehdidnsight.net` avec les informations d’identification du cluster. L’écran initial affiche un tableau de bord de présentation. Il existe de légères différences de présentation entre HDInsight 3.6 et 4.0.

1. Accédez à **Spark2** > **Configs**.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config2.png" alt-text="Sélectionnez l’onglet Configurations" border="true":::

1. Dans la liste des configurations, sélectionnez et développez **Custom-spark2-defaults**.

1. Recherchez le paramètre de valeur que vous avez besoin d’ajuster, par exemple **spark.executor.memory**. Dans le cas présent, la valeur de **9728m** est trop élevée.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config4.png" alt-text="Sélectionnez custom-spark-defaults" border="true":::

1. Définissez la valeur sur le paramètre recommandé. La valeur **2048m** est recommandée pour ce paramètre.

1. Enregistrez la valeur, puis la configuration. Sélectionnez **Enregistrer**.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config6a.png" alt-text="Remplacez la valeur par 2048m" border="true":::

    Notez les modifications apportées à la configuration, puis sélectionnez **Enregistrer**.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config6c.png" alt-text="Saisissez une note concernant les modifications apportées" border="true":::

    Si des configurations requièrent votre attention, un message s’affiche. Notez les éléments, puis sélectionnez **Proceed Anyway** (Continuer).

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config6b.png" alt-text="Sélectionnez Proceed Anyway (Continuer)" border="true":::

1. À chaque fois que vous enregistrez une configuration, vous êtes invité à redémarrer le service. Sélectionnez **Redémarrer**.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config7a.png" alt-text="Sélectionnez Redémarrer" border="true":::

    Confirmez le redémarrage.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config7b.png" alt-text="Sélectionnez Confirm Restart All (Confirmer le redémarrage)" border="true":::

    Vous pouvez examiner les processus en cours d’exécution.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config7c.png" alt-text="Examinez les processus en cours d’exécution" border="true":::

1. Vous pouvez ajouter des configurations. Dans la liste des configurations, sélectionnez **Custom-spark2-defaults**, puis **Ajouter une propriété**.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config8.png" alt-text="Sélectionnez Ajouter une propriété" border="true":::

1. Définissez une nouvelle propriété. Vous pouvez définir une propriété unique via une boîte de dialogue de paramètres spécifiques tels que le type de données. Vous pouvez également définir plusieurs propriétés en utilisant une définition par ligne.

    Dans cet exemple, la propriété **spark.driver.memory** est définie avec une valeur de **4g**.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config9.png" alt-text="Définissez une nouvelle propriété" border="true":::

1. Enregistrez la configuration et redémarrez le service en suivant la procédure décrite aux étapes 6 et 7.

Ces modifications s’appliquent à l’ensemble du cluster, mais elles peuvent être remplacées au moment de l’envoi du travail Spark.

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Comment configurer une application Apache Spark sur des clusters avec un notebook Jupyter ?

Dans la première cellule du notebook Jupyter, spécifiez, après la directive **%%configure**, les configurations Spark dans un format JSON valide. Modifiez les valeurs si nécessaire :

:::image type="content" source="./media/apache-troubleshoot-spark/add-configuration-cell.png" alt-text="Ajoutez une configuration" border="true":::

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>Comment configurer une application Apache Spark sur des clusters via Apache Livy ?

Soumettez la demande de l’application Spark à Livy à l’aide d’un client REST comme cURL. Utilisez une commande similaire à la suivante. Modifiez les valeurs si nécessaire :

```apache
curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
```

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>Comment configurer une application Apache Spark sur des clusters via spark-submit ?

Lancez spark-shell à l’aide d’une commande semblable à la suivante. Modifiez la valeur des configurations selon les besoins :

```apache
spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
```

### <a name="additional-reading"></a>Documentation supplémentaire

[Apache Spark job submission on HDInsight clusters](/archive/blogs/azuredatalake/spark-job-submission-on-hdinsight-101) (Envoi de travaux Spark sur des clusters HDInsight)

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* [Vue d’ensemble de la gestion de la mémoire dans Spark](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [Débogage d’une application Spark sur des clusters HDInsight](/archive/blogs/azuredatalake/spark-debugging-101).

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous à [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client. Connexion de la communauté Azure aux ressources appropriées : réponses, support technique et experts.

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une requête de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour plus d’informations, consultez [Création d’une demande de support Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus avec votre abonnement Microsoft Azure. En outre, le support technique est fourni avec l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).
