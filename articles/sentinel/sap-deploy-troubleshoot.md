---
title: Résolution des problèmes de déploiement de la solution SAP Microsoft Sentinel | Microsoft Docs
description: Découvrez comment résoudre les problèmes spécifiques qui peuvent se produire dans le déploiement de votre solution SAP Microsoft Sentinel.
author: batamig
ms.author: bagold
ms.service: microsoft-sentinel
ms.topic: troubleshooting
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.subservice: microsoft-sentinel
ms.openlocfilehash: 7eee4c9fdd3de93017c15556646fa9e6ece8b22c
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520006"
---
# <a name="troubleshooting-your-microsoft-sentinel-sap-solution-deployment"></a>Résolution des problèmes de déploiement de votre solution SAP Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

## <a name="useful-docker-commands"></a>Commandes Docker utiles

Lors de la résolution des problèmes de votre connecteur de données SAP, les commandes suivantes peuvent être utiles :

|Function  |Commande  |
|---------|---------|
|**Arrêter le conteneur Docker**     |  `docker stop sapcon-[SID]`       |
|**Démarrer le conteneur Docker**     |`docker start sapcon-[SID]`         |
|**Afficher les journaux système de Docker**     |  `docker logs -f sapcon-[SID]`       |
|**Entrer le conteneur Docker**     |   `docker exec -it sapcon-[SID] bash`      |
|     |         |

Pour plus d’informations, consultez la [documentation sur l’interface de ligne de commande de Docker](https://docs.docker.com/engine/reference/commandline/docker/).

## <a name="review-system-logs"></a>Examiner les journaux système

Nous vous recommandons vivement d’examiner les journaux système après l’installation ou la [réinitialisation du connecteur de données](#reset-the-sap-data-connector).

Exécutez :

```bash
docker logs -f sapcon-[SID]
```

## <a name="enable-debug-mode-printing"></a>Activer l’impression en mode débogage

**Pour activer l’impression en mode débogage** :

1. Copiez le fichier suivant dans votre répertoire **sapcon/[SID]** , puis renommez-le `loggingconfig.yaml` : https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/template/loggingconfig_DEV.yaml

1. [Réinitialisez le connecteur de données SAP](#reset-the-sap-data-connector).

Par exemple, pour le SID `A4H` :

```bash
wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/template/loggingconfig_DEV.yaml
              cp loggingconfig.yaml ~/sapcon/A4H
              docker restart sapcon-A4H
```

**Pour désactiver l’impression en mode débogage, exécutez**  :

```bash
mv loggingconfig.yaml loggingconfig.old
ls
docker restart sapcon-[SID]
```

## <a name="view-all-docker-execution-logs"></a>Afficher tous les journaux d’exécution de Docker

Pour afficher tous les journaux d’exécution de Docker pour votre déploiement de connecteurs de données SAP Microsoft Sentinel, exécutez l’une des commandes suivantes :

```bash
docker exec -it sapcon-[SID] bash && cd /sapcon-app/sapcon/logs
```

ou

```bash
docker exec –it sapcon-[SID] cat /sapcon-app/sapcon/logs/[FILE_LOGNAME]
```

Une sortie similaire à celle ci-après devrait s’afficher :

```bash
Logs directory:
root@644c46cd82a9:/sapcon-app# ls sapcon/logs/ -l
total 508
-rwxr-xr-x 1 root root      0 Mar 12 09:22 ' __init__.py'
-rw-r--r-- 1 root root    282 Mar 12 16:01  ABAPAppLog.log
-rw-r--r-- 1 root root   1056 Mar 12 16:01  ABAPAuditLog.log
-rw-r--r-- 1 root root    465 Mar 12 16:01  ABAPCRLog.log
-rw-r--r-- 1 root root    515 Mar 12 16:01  ABAPChangeDocsLog.log
-rw-r--r-- 1 root root    282 Mar 12 16:01  ABAPJobLog.log
-rw-r--r-- 1 root root    480 Mar 12 16:01  ABAPSpoolLog.log
-rw-r--r-- 1 root root    525 Mar 12 16:01  ABAPSpoolOutputLog.log
-rw-r--r-- 1 root root      0 Mar 12 15:51  ABAPTableDataLog.log
-rw-r--r-- 1 root root    495 Mar 12 16:01  ABAPWorkflowLog.log
-rw-r--r-- 1 root root 465311 Mar 14 06:54  API.log # view this log to see submits of data into Microsoft Sentinel
-rw-r--r-- 1 root root      0 Mar 12 15:51  LogsDeltaManager.log
-rw-r--r-- 1 root root      0 Mar 12 15:51  PersistenceManager.log
-rw-r--r-- 1 root root   4830 Mar 12 16:01  RFC.log
-rw-r--r-- 1 root root   5595 Mar 12 16:03  SystemAdmin.log
```

Pour copier vos journaux sur le système d’exploitation hôte, exécutez :

```bash
docker cp sapcon-[SID]:/sapcon-app/sapcon/logs /directory
```

Par exemple :

```bash
docker cp sapcon-A4H:/sapcon-app/sapcon/logs /tmp/sapcon-logs-extract
```

## <a name="review-and-update-the-sap-data-connector-configuration"></a>Examiner et mettre à jour la configuration du connecteur de données SAP

Si vous souhaitez vérifier le fichier de configuration du connecteur de données SAP et effectuer des mises à jour manuelles, procédez comme suit :

1. Sur votre machine virtuelle, dans le répertoire de base de l’utilisateur, ouvrez le fichier **~/sapcon/[SID]/systemconfig.ini**.
1. Mettez à jour la configuration si nécessaire, puis redémarrez le conteneur :

    ```bash
    docker restart sapcon-[SID]
    ```

## <a name="reset-the-sap-data-connector"></a>Réinitialiser le connecteur de données SAP

Les étapes suivantes réinitialisent le connecteur et ré-ingèrent les journaux SAP des dernières 24 heures.

1.  Arrêtez le connecteur. Exécutez :

    ```bash
    docker stop sapcon-[SID]
    ```

1.  Supprimez le fichier **metadata.db** du répertoire **sapcon/[SID]** . Exécutez :

    ```bash
    cd ~/sapcon/<SID>
    ls
    mv metadata.db metadata.old
    ```

    > [!NOTE]
    > Le fichier **metadata.db** contient le dernier horodatage de chacun des journaux et fonctionne pour empêcher la duplication.

1. Redémarrez le connecteur. Exécutez :

    ```bash
    docker start sapcon-[SID]
    ```

Quand vous avez terminé, veillez à [consulter les journaux système](#review-system-logs).



## <a name="common-issues"></a>Problèmes courants

Une fois le connecteur de données et le contenu de sécurité SAP déployés, il se peut que vous rencontriez les erreurs ou problèmes suivants :

### <a name="corrupt-or-missing-sap-sdk-file"></a>Fichier du SDK SAP endommagé ou manquant

Cette erreur peut se produire quand le connecteur ne parvient pas à démarrer avec PyRfc ou que des messages d’erreur liés au fichier zip s’affichent.

1. Réinstallez le SDK SAP.
1. Veillez à disposer de la bonne version Linux 64 bits. À ce jour, le nom de fichier de la version est : **nwrfc750P_8-70002752.zip**.

Si vous avez installé le connecteur de données manuellement, assurez-vous que vous avez copié le fichier du SDK dans le conteneur Docker.

Exécutez :

```bash
Docker cp SDK by running docker cp nwrfc750P_8-70002752.zip /sapcon-app/inst/
```

### <a name="abap-runtime-errors-appear-on-a-large-system"></a>Des erreurs d’exécution ABAP s’affichent sur un grand système

Si des erreurs d’exécution ABAP s’affichent sur de grands systèmes, essayez de définir une taille de bloc plus petite :

1. Modifiez le fichier **sapcon/SID/systemconfig.ini** et définissez `timechunk = 5`.
2. [Réinitialisez le connecteur de données SAP](#reset-the-sap-data-connector).

> [!NOTE]
> La taille **timechunk** est définie en minutes.

### <a name="empty-or-no-audit-log-retrieved-with-no-special-error-messages"></a>Journal d’audit vide ou aucun journal d’audit récupéré, sans message d’erreur spécial

1. Vérifiez que la journalisation d’audit est activée dans SAP.
1. Vérifiez les transactions **SM19** ou **RSAU_CONFIG** .
1. Activez les événements en fonction des besoins.
1. Vérifiez si les messages arrivent et existent dans les transactions SAP **SM20** ou **RSAU_READ_LOG**, sans qu’aucune erreur spéciale n’apparaisse dans le journal du connecteur.


### <a name="incorrect-microsoft-sentinel-workspace-id-or-key"></a>Clé ou ID d’espace de travail Microsoft Sentinel incorrect

Si vous vous rendez compte que vous avez entré une clé ou un ID d’espace de travail incorrect dans votre [script de déploiement](sap-deploy-solution.md#create-a-key-vault-for-your-sap-credentials), mettez à jour les informations d’identification stockées dans Azure Key Vault.

Après avoir vérifié vos informations d’identification dans Azure Key Vault, redémarrez le conteneur :

```bash
docker restart sapcon-[SID]
```

### <a name="incorrect-sap-abap-user-credentials-in-a-fixed-configuration"></a>Informations d’identification de l’utilisateur SAP ABAP incorrectes dans une configuration fixe

Le stockage direct du mot de passe dans le fichier de configuration **systemconfig.ini** est un exemple de configuration fixe.

Si vos informations d’identification y sont incorrectes, vérifiez-les.

Utilisez le chiffrement Base64 pour chiffrer l’utilisateur et le mot de passe. Vous pouvez utiliser les outils de chiffrement en ligne pour chiffrer vos informations d’identification, tels que https://www.base64encode.org/.

### <a name="incorrect-sap-abap-user-credentials-in-key-vault"></a>Informations d’identification d’utilisateur SAP ABAP incorrectes dans le coffre de clés

Vérifiez vos informations d’identification et corrigez-les au besoin en appliquant les valeurs correctes pour **ABAPUSER** et **ABAPPASS** dans Azure Key Vault.

Redémarrez ensuite le conteneur :

```bash
docker restart sapcon-[SID]
```


### <a name="missing-abap-sap-user-permissions"></a>Autorisations ABAP (utilisateur SAP) manquantes

Si vous recevez un message d’erreur semblable à **Autorisation RFC manquante dans le back-end**, vos rôle et autorisations SAP n’ont pas été appliqués correctement.

1. Assurez-vous que le rôle **MSFTSEN/SENTINEL_CONNECTOR** a été importé dans le cadre d’un transport de [demande de modification](sap-solution-detailed-requirements.md#required-sap-log-change-requests) et appliqué à l’utilisateur du connecteur.

1. Exécutez le processus de génération de rôle et de comparaison des utilisateurs au moyen de la transaction SAP PFCG.

### <a name="missing-data-in-your-workbooks-or-alerts"></a>Données manquantes dans vos workbooks ou alertes

Si certaines données font défaut dans vos workbooks ou alertes Microsoft Sentinel, assurez-vous que la stratégie **Auditlog** est correctement activée côté SAP, sans erreur dans le fichier journal. 

Utilisez la transaction **RSAU_CONFIG_LOG** pour cette étape.


### <a name="missing-sap-change-request"></a>Demande de modification SAP manquante

Si vous constatez des erreurs selon lesquelles une [demande de modification SAP](sap-solution-detailed-requirements.md#required-sap-log-change-requests) requise fait défaut, vérifiez que vous avez importé la demande de modification SAP appropriée pour votre système.

Pour plus d’informations, consultez [Configurer votre système SAP](sap-deploy-solution.md#configure-your-sap-system).

### <a name="network-connectivity-issues"></a>Problèmes de connectivité réseau

Si vous rencontrez des problèmes de connectivité réseau avec l’environnement SAP ou Microsoft Sentinel, vérifiez votre connectivité réseau pour vous assurer que les données circulent comme prévu.

Voici quelques problèmes courants :

- Les pare-feu entre le conteneur de la station d’accueil et les hôtes SAP peuvent bloquer le trafic. L’hôte de l’environnement SAP reçoit des communications via les ports TCP suivants, qui doivent être ouverts : **32xx**, **5xx13** et **33xx**, où **xx** est le numéro de l’instance SAP.

- La communication sortante entre votre hôte SAP et Microsoft Container Registry ou Azure nécessite une configuration du proxy. Cela a généralement un impact sur l’installation et vous oblige à configurer les variables d’environnement `HTTP_PROXY` et `HTTPS_PROXY`. Vous pouvez également ingérer des variables d’environnement dans le conteneur d’ancrage lorsque vous créez le conteneur, en ajoutant l'indicateur `-e` à la commande docker `create` / `run`.

### <a name="other-unexpected-issues"></a>Autres problèmes inattendus

Si vous rencontrez des problèmes inattendus non listés dans cet article, essayez les étapes suivantes :

- [Réinitialisez le connecteur et rechargez vos journaux](#reset-the-sap-data-connector).
- [Mettez à niveau le connecteur](sap-deploy-solution.md#update-your-sap-data-connector) vers la dernière version.

> [!TIP]
> Nous vous recommandons également de réinitialiser votre connecteur et de vérifier que vous disposez des dernières mises à niveau après toute modification de configuration majeure.

### <a name="retrieving-an-audit-log-fails-with-warnings"></a>La récupération d’un journal d’audit échoue avec des avertissements

Si vous tentez de récupérer un journal d’audit, sans que la [demande de modification requise](sap-solution-detailed-requirements.md#required-sap-log-change-requests) soit déployée ou sur une version antérieure ou non corrigée, et que le processus échoue avec des avertissements, vérifiez que le journal d’audit SAP peut être récupéré avec l’une des méthodes suivantes :

- Utilisation d’un mode de compatibilité appelé *XAL* sur des versions antérieures
- Utilisation d’une version non récemment corrigée
- Sans la demande de modification requise installée

Même si votre système doit automatiquement basculer en mode de compatibilité si cela est nécessaire, vous pouvez être amené à le faire basculer manuellement. Pour basculer en mode de compatibilité manuellement :

1. Dans le répertoire **sapcon/SID**, modifiez le fichier **systemconfig.ini**.

1. Définissez : `auditlogforcexal = True`

1. Redémarrez le conteneur Docker :

    ```bash
    docker restart sapcon-[SID]
    ```

### <a name="sapcontrol-or-java-subsystems-unable-to-connect"></a>Les sous-systèmes SAPCONTROL ou JAVA ne parviennent pas à se connecter

Vérifiez que l’utilisateur du système d’exploitation est valide et qu’il peut exécuter la commande suivante sur le système SAP cible :

```bash
sapcontrol -nr <SID> -function GetSystemInstanceList
```

### <a name="sapcontrol-or-java-subsystem-fails-with-timezone-related-error-message"></a>Le sous-système SAPCONTROL ou JAVA échoue avec un message d’erreur relatif aux fuseaux horaires

Si votre sous-système SAPCONTROL ou JAVA échoue avec un message d’erreur relatif aux fuseaux horaires, par exemple **Vérifiez la configuration et l’accès réseau au serveur SAP - « Etc/NZST »** , vérifiez que vous utilisez des codes de fuseau horaire standard.

Par exemple, utilisez `javatz = GMT+12` ou `abaptz = GMT-3**`.


### <a name="unable-to-import-the-change-request-transports-to-sap"></a>Impossible d’importer les transports de demande de modification vers SAP

Si vous n’êtes pas en mesure d’importer les [demandes de modification de journal SAP nécessaires](sap-solution-detailed-requirements.md#required-sap-log-change-requests) et que vous obtenez une erreur concernant une version de composant non valide, ajoutez `ignore invalid component version` quand vous importez la demande de modification.

### <a name="audit-log-data-not-ingested-past-initial-load"></a>Données du journal d’audit non ingérées au-delà de la charge initiale

Si les données du journal d’audit SAP, visibles dans les transactions **RSAU_READ_LOAD** ou **SM200**, ne sont pas ingérées dans Microsoft Sentinel après la charge initiale, il se peut que le système SAP et le système d’exploitation hôte SAP soient incorrectement configurés.

- Les charges initiales sont ingérées après une nouvelle installation du connecteur de données SAP ou après la suppression du fichier **metadata.db**.
- La définition du fuseau horaire de votre système SAP sur **CET** dans la transaction **STZAC**, alors que le fuseau horaire du système d’exploitation hôte SAP est défini sur **UTC** est un exemple de configuration incorrecte.

Pour rechercher les configurations incorrectes, exécutez le rapport **RSDBTIME** dans la transaction **SE38**. Si vous détectez une différence entre le système SAP et le système d’exploitation hôte SAP :

1. Arrêtez le conteneur Docker. Exécuter

    ```bash
    docker stop sapcon-[SID]
    ```

1.  Supprimez le fichier **metadata.db** du répertoire **sapcon/[SID]** . Exécutez :

    ```bash
    rm ~/sapcon/[SID]/metadata.db
    ```

1. Mettez à jour le système SAP et le système d’exploitation hôte SAP afin d’utiliser les mêmes paramètres (par exemple, le même fuseau horaire). Pour plus d’informations, consultez la [page wiki de la communauté SAP](https://wiki.scn.sap.com/wiki/display/Basis/Time+zone+settings%2C+SAP+vs.+OS+level).

1. Redémarrez le conteneur. Exécutez :

    ```bash
    docker start sapcon-[SID]
    ```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez les pages suivantes :

- [Déployer une surveillance continue des menaces SAP (préversion publique)](sap-deploy-solution.md)
- [Informations de référence sur les journaux de la solution SAP Microsoft Sentinel (préversion publique)](sap-solution-log-reference.md)
- [Déployer le connecteur de données SAP Microsoft Sentinel avec SNC](sap-solution-deploy-snc.md)
- [Options de configuration pour experts, déploiement local et sources de journaux SAPControl](sap-solution-deploy-alternate.md)
- [Solution SAP Microsoft Sentinel : Informations de référence relatives au contenu de sécurité (préversion publique)](sap-solution-security-content.md)
- [Exigences de SAP détaillées pour la solution SAP Microsoft  Sentinel (préversion publique)](sap-solution-detailed-requirements.md)
