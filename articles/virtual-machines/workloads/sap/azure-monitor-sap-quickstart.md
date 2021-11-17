---
title: Déploiement d’Azure Monitor pour SAP Solutions avec le Portail Azure
description: Découvrez comment utiliser une méthode par navigateur pour déployer Azure Monitor pour SAP Solutions.
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.date: 07/08/2021
ms.openlocfilehash: 82dc8aaa14aeae63549a872d887f40d16eebec96
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315328"
---
# <a name="deploy-azure-monitor-for-sap-solutions-by-using-the-azure-portal"></a>Déploiement d’Azure Monitor pour SAP Solutions avec le Portail Azure

Dans cet article, nous allons voir comment déployer Azure Monitor pour SAP Solutions sur le [Portail Azure](https://azure.microsoft.com/features/azure-portal). À l’aide de l’interface basée sur le navigateur du portail, nous allons déployer Azure Monitor pour SAP Solutions et configurer des fournisseurs.

## <a name="sign-in-to-the-portal"></a>Se connecter au portail

Connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="create-a-monitoring-resource"></a>Création d’une ressource de monitoring

1. Sous **Place de marché**, sélectionnez **Azure Monitor pour SAP Solutions**.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-1.png" alt-text="Capture d’écran montrant la sélection de l’offre Azure Monitor pour SAP Solutions sur la Place de marché Azure." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-1.png":::

2. Dans l’onglet **De base**, indiquez les valeurs requises. Le cas échéant, vous pouvez utiliser un espace de travail Log Analytics existant.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-2.png" alt-text="Capture d’écran montrant les options de configuration de l’onglet De base." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-2.png":::

   Lorsque vous sélectionnez un réseau virtuel, assurez-vous que les systèmes dont vous souhaitez effectuer le monitoring y sont accessibles. 

   > [!IMPORTANT]
   > Si vous sélectionnez **Partager** pour **Partager des données avec le Support Microsoft**, nos équipes de support pourront vous aider à résoudre les problèmes.

## <a name="configure-providers"></a>Configurer des fournisseurs

### <a name="sap-netweaver-provider"></a>Fournisseur SAP NetWeaver

Le service de démarrage SAP propose différents services, notamment le monitoring du système SAP. Nous utilisons SAPControl, une interface de services web SOAP qui expose ces fonctionnalités. Elle fait la distinction entre les méthodes de services web [protégées et non protégées](https://wiki.scn.sap.com/wiki/display/SI/Protected+web+methods+of+sapstartsrv). 

Pour extraire des métriques spécifiques, vous devez ôter la protection de certaines méthodes pour la version actuelle. Suivez ces étapes pour chaque système SAP :

1. Ouvrez une connexion GUI SAP au serveur SAP.
2. Connectez-vous avec un compte d’administration.
3. Exécutez la transaction RZ10.
4. Sélectionnez le profil approprié (*DEFAULT.PFL*).
5. Sélectionnez **Maintenance étendue** > **Modifier**. 
6. Sélectionnez le paramètre de profil « service/protectedwebmethods », modifiez-le pour qu’il ait la valeur suivante, puis cliquez sur Copier :  

   ```service/protectedwebmethods instruction
      SDEFAULT -GetQueueStatistic -ABAPGetWPTable -EnqGetStatistic -GetProcessList

7. Go back and select **Profile** > **Save**.
8. After saving the changes for this parameter, please restart the SAPStartSRV service on each of the instances in the SAP system. (Restarting the services will not restart the SAP system; it will only restart the SAPStartSRV service (in Windows) or daemon process (in Unix/Linux))
   8a. On Windows systems, this can be done in a single window using the SAP Microsoft Management Console (MMC) / SAP Management Console(MC).  Right-click on each instance and choose All Tasks -> Restart Service.
![MMC](https://user-images.githubusercontent.com/75772258/126453939-daf1cf6b-a940-41f6-98b5-3abb69883520.png)

   8b. On Linux systems, use the below command where NN is the SAP instance number to restart the host which is logged into.
   
   ```RestartService
   sapcontrol -nr <NN> -function RestartService
   
9. Once the SAP service is restarted, please check to ensure the updated web method protection exclusion rules have been applied for each instance by running the following command: 

**Logged as \<sidadm\>** 
   `sapcontrol -nr <NN> -function ParameterValue service/protectedwebmethods`

**Logged as different user** 
   `sapcontrol -nr <NN> -function ParameterValue service/protectedwebmethods -user "<adminUser>" "<adminPassword>"`

   The output should look like :-
   ![SS](https://user-images.githubusercontent.com/75772258/126454265-d73858c3-c32d-4afe-980c-8aba96a0b2a4.png)

10. To conclude and validate, a test query can be done against web methods to validate ( replace the hostname , instance number and method name ) leverage the below powershell script 

```Powershell command to test unprotect method 
$SAPHostName = "<hostname>"
$InstanceNumber = "<instancenumber>"
$Function = "ABAPGetWPTable"
[System.Net.ServicePointManager]::ServerCertificateValidationCallback = {$true}
$sapcntrluri = "https://&quot; + $SAPHostName + &quot;:5&quot; + $InstanceNumber + &quot;14/?wsdl"
$sapcntrl = New-WebServiceProxy -uri $sapcntrluri -namespace WebServiceProxy -class sapcntrl
$FunctionObject = New-Object ($sapcntrl.GetType().NameSpace + ".$Function")
$sapcntrl.$Function($FunctionObject)
```
11. **Répétez les étapes 3 à 10 pour chaque profil d’instance**.

>[!Important] 
>Il est essentiel que le service sapstartsrv soit redémarré sur chacune des instances du système SAP pour ôter la protection des méthodes web SAPControl.  Ces API SOAP en lecture seule sont requises pour que le fournisseur NetWeaver extraie les données de métriques du système SAP. Si la protection de ces méthodes n’est pas ôtée, le classeur métrique NetWeaver risque de présenter des visualisations vides ou manquantes.
   
>[!Tip]
> Utilisez une liste de contrôle d’accès (ACL, Access Control List) pour filtrer l’accès à un port de serveur. Pour plus d’informations, consultez [cette note SAP](https://launchpad.support.sap.com/#/notes/1495075).

Pour installer le fournisseur NetWeaver sur le Portail Azure, procédez comme suit :

1. Vous devez avoir suivi la procédure requise précédente et redémarré le serveur.
1. Sur le Portail Azure, sélectionnez **Ajouter un fournisseur** sous **Azure Monitor pour SAP Solutions**. Ensuite, suivez les étapes ci-dessous :

   1. Pour **Type**, sélectionnez **SAP NetWeaver**.

   1. Pour **Nom d’hôte**, entrez le nom d’hôte du système SAP.

   1. Pour **Sous-domaine**, entrez un sous-domaine, le cas échéant.

   1. Pour **Numéro de l’instance**, entrez le numéro d’instance correspondant au nom d’hôte entré. 

   1. Pour **SID**, entrez l’ID système.
   
   ![Capture d’écran montrant les options de configuration possibles pour ajouter un fournisseur SAP NetWeaver.](https://user-images.githubusercontent.com/75772258/114583569-5c777d80-9c9f-11eb-99a2-8c60987700c2.png)

1.    Lorsque vous avez terminé, sélectionnez **Ajouter un fournisseur**. Continuez à ajouter des fournisseurs si besoin, ou sélectionnez **Vérifier + créer** pour terminer le déploiement.

>[!Important]
>Si les serveurs d’applications SAP (c’est-à-dire les machines virtuelles) font partie d’un domaine réseau (par exemple géré par Azure Active Directory), il est essentiel que le sous-domaine correspondant soit fourni dans la zone de texte Sous-domaine.  La machine virtuelle du collecteur Azure Monitor pour SAP Solutions qui existe dans le réseau virtuel n’est pas jointe au domaine. Par conséquent, elle ne peut pas résoudre le nom d’hôte des instances situées dans le système SAP, à moins que le nom d’hôte ne soit un nom de domaine complet.  Si vous ne le fournissez pas, le classeur NetWeaver risque de présenter des visualisations manquantes ou incomplètes.
 
>Si, par exemple, le nom d’hôte du système SAP possède le nom de domaine complet « myhost.mycompany.global.corp », entrez le nom d’hôte « myhost » et spécifiez le sous-domaine « mycompany.global.corp ».  Lorsque le fournisseur NetWeaver appelle l’API GetSystemInstanceList sur le système SAP, SAP renvoie le nom d’hôte de toutes les instances du système.  La machine virtuelle du collecteur utilise cette liste pour effectuer des appels d’API supplémentaires visant à récupérer les métriques propres aux fonctionnalités de chaque instance (par exemple ABAP, J2EE, MESSAGESERVER, ENQUE et ENQREP). Elle se sert ensuite du sous-domaine « mycompany.global.corp », s’il est spécifié, pour créer le nom de domaine complet de chacune des instances du système SAP.  
 
>Veuillez NE PAS spécifier d’adresse IP pour le champ Nom d’hôte si le système SAP fait partie d’un domaine réseau.

   
### <a name="sap-hana-provider"></a>Fournisseur SAP HANA 

1. Sélectionnez l’onglet **Fournisseurs** pour ajouter les fournisseurs que vous souhaitez configurer. Vous pouvez ajouter plusieurs fournisseurs successivement, ou les ajouter après avoir déployé la ressource de monitoring. 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-3.png" alt-text="Capture d’écran montrant l’onglet permettant d’ajouter des fournisseurs." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-3.png":::

1. Sélectionnez **Ajouter un fournisseur**, puis suivez les étapes ci-dessous :

   1. Pour **Type**, sélectionnez **SAP HANA**. 

      > [!IMPORTANT]
      > Vérifiez qu’un fournisseur SAP HANA est configuré pour le nœud SAP HANA `master`.

   1. Pour **Adresse IP**, entrez l’adresse IP privée du serveur HANA.

   1. Pour **Locataire de la base de données**, entrez le nom du locataire à utiliser. Vous pouvez choisir n’importe lequel. Toutefois, nous vous recommandons d’utiliser **SYSTEMDB**, car il offre un plus large éventail de zones de monitoring. 

   1. Pour **Port SQL**, entrez le numéro de port associé à votre base de données HANA. Il doit être au format *[3]*  +  *[numéro_instance]*  +  *[13]* , par exemple **30013**. 

   1. Pour **Nom d’utilisateur de la base de données**, entrez le nom d’utilisateur à utiliser. Les rôles de *monitoring* et de *lecture de catalogue* doivent être attribués à l’utilisateur de la base de données.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-4.png" alt-text="Capture d’écran montrant les options de configuration possibles pour ajouter un fournisseur SAP HANA." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-4.png":::

1. Lorsque vous avez terminé, sélectionnez **Ajouter un fournisseur**. Continuez à ajouter des fournisseurs si besoin, ou sélectionnez **Vérifier + créer** pour terminer le déploiement.

   
### <a name="microsoft-sql-server-provider"></a>Fournisseur Microsoft SQL Server

1. Avant d’ajouter le fournisseur Microsoft SQL Server, exécutez le script suivant dans SQL Server Management Studio pour créer un utilisateur doté des autorisations nécessaires à la configuration du fournisseur.

   ```sql
   USE [<Database to monitor>]
   DROP USER [AMS]
   GO
   USE [master]
   DROP USER [AMS]
   DROP LOGIN [AMS]
   GO
   CREATE LOGIN [AMS] WITH PASSWORD=N'<password>', DEFAULT_DATABASE=[<Database to monitor>], DEFAULT_LANGUAGE=[us_english], CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF
   CREATE USER AMS FOR LOGIN AMS
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GRANT CONNECT TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW ANY DEFINITION TO AMS
   GRANT EXEC ON xp_readerrorlog TO AMS
   GO
   USE [<Database to monitor>]
   CREATE USER [AMS] FOR LOGIN [AMS]
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GO
   ```

1. Sélectionnez **Ajouter un fournisseur**, puis suivez les étapes ci-dessous :

   1. Pour **Type**, sélectionnez **Microsoft SQL Server**. 

   1. Renseignez les champs restants à l’aide des informations associées à votre instance SQL Server. 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-6.png" alt-text="Capture d’écran montrant les options de configuration possibles pour ajouter un fournisseur Microsoft SQL Server." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-6.png":::

1. Lorsque vous avez terminé, sélectionnez **Ajouter un fournisseur**. Continuez à ajouter des fournisseurs si besoin, ou sélectionnez **Vérifier + créer** pour terminer le déploiement.

### <a name="high-availability-cluster-pacemaker-provider"></a>Fournisseur de cluster à haute disponibilité (Pacemaker)

Avant d’ajouter des fournisseurs pour les clusters haute disponibilité (Pacemaker), installez l’agent correspondant à votre environnement.

Pour les clusters **SUSE**, ha_cluster_provider doit être installé sur chaque nœud. Découvrez comment installer [l’exportateur de clusters haute disponibilité](https://github.com/ClusterLabs/ha_cluster_exporter#installation). Versions de SUSE prises en charge : SLES pour SAP 12 SP3 et versions ultérieures.  
   
Pour les clusters **RHEL**, Performance Co-Pilot (PCP) et le sous-package pcp-pmda-hacluster doivent être installés sur chaque nœud. Découvrez comment installer [l’agent PCP HACLUSTER] (https://access.redhat.com/articles/6139852). Versions de RHEL prises en charge : 8.2, 8.4 et versions ultérieures.
 
Une fois que vous avez effectué l’installation des composants requis, créez un fournisseur pour chaque nœud de cluster.

1. Sélectionnez **Ajouter un fournisseur**, puis suivez les étapes ci-dessous :

1. Pour **Type**, sélectionnez **Cluster haute disponibilité (Pacemaker)** . 
   
1. Configurez les fournisseurs pour chaque nœud de cluster en entrant l’URL du point de terminaison dans **Point de terminaison de l’exportateur de clusters haute disponibilité**. Pour les clusters **SUSE**, entrez **http://\<IP  address\>:9664/metrics**. Pour les clusters **RHEL**, entrez **http://\<IP address\>:44322/metrics?names=ha_cluster**.
 
1. Entrez l’ID système, le nom d’hôte et le nom de cluster dans les zones correspondantes.
   
   > [!IMPORTANT]
   > Le nom d’hôte fait référence au nom d’hôte réel dans la machine virtuelle. Utilisez la commande « hostname -s » pour les clusters SUSE comme les clusters RHEL.  

1. Lorsque vous avez terminé, sélectionnez **Ajouter un fournisseur**. Continuez à ajouter des fournisseurs si besoin, ou sélectionnez **Vérifier + créer** pour terminer le déploiement.

### <a name="os-linux-provider"></a>Fournisseur de système d’exploitation (Linux) 

1. Sélectionnez **Ajouter un fournisseur**, puis suivez les étapes ci-dessous :

   1. Pour **Type**, sélectionnez **Système d’exploitation (Linux)** . 

      >[!IMPORTANT]
      > Pour configurer un fournisseur de système d’exploitation (Linux), assurez-vous que la [dernière version de node_exporter](https://prometheus.io/download/#node_exporter) est installée sur chacun des hôtes (système nu ou machine virtuelle) à analyser. [Plus d’informations](https://github.com/prometheus/node_exporter)

   1. Pour **Nom**, entrez un nom. Ce sera l’identificateur de l’instance système nu.

   1. Pour **Point de terminaison node_exporter**, entrez **http://IP:9100/metrics** .

      >[!IMPORTANT]
      >Utilisez l’adresse IP privée de l’hôte Linux. L’hôte et la ressource Azure Monitor pour SAP Solutions doivent se trouver dans le même réseau virtuel. 
      >
      >Le port du pare-feu « 9100 » doit être ouvert sur l’hôte Linux. Si vous utilisez `firewall-cmd`, exécutez les commandes suivantes : 
      >
      >`firewall-cmd --permanent --add-port=9100/tcp`
      >
      >`firewall-cmd --reload`
      >
      >Si vous utilisez `ufw`, exécutez les commandes suivantes :
      >
      >`ufw allow 9100/tcp`
      >
      >`ufw reload`
      >
      > Si l’hôte Linux est une machine virtuelle Azure, assurez-vous que tous les groupes de sécurité réseau applicables autorisent le trafic entrant sur le port 9100 à partir de la source `VirtualNetwork`.
 
1. Lorsque vous avez terminé, sélectionnez  **Ajouter un fournisseur**. Continuez à ajouter des fournisseurs si besoin, ou sélectionnez  **Vérifier + créer**  pour terminer le déploiement. 


## <a name="next-steps"></a>Étapes suivantes

Découvrez plus en détail Azure Monitor pour SAP Solutions.

> [!div class="nextstepaction"]
> [Superviser SAP sur Azure](monitor-sap-on-azure.md)
