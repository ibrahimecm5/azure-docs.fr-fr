---
title: Résolution des problèmes de synchronisation cloud Azure AD Connect
description: Cet article explique comment résoudre les problèmes pouvant survenir avec l’agent de provisionnement cloud.
author: billmath
ms.author: billmath
manager: daveba
ms.date: 10/13/2021
ms.topic: how-to
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 4fa397505d7bb98235a97e5818409baee9c9c9e4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131451860"
---
# <a name="cloud-sync-troubleshooting"></a>Résolution des problèmes de synchronisation cloud

La synchronisation cloud a une incidence sur bien des éléments et compte de nombreuses dépendances différentes. Cette large étendue peut entraîner divers problèmes. Cet article vous permet de résoudre ces problèmes. Il présente les principales causes de ces problèmes, explique comment rassembler des informations supplémentaires et vous donne différentes techniques permettant de détecter les problèmes.


## <a name="common-troubleshooting-areas"></a>Zones courantes de résolution de problème

|Nom|Description|
|-----|-----|
|[Problèmes rencontrés avec l’agent](#agent-problems)|Vérifiez que l’agent a été correctement installé et qu’il communique avec Azure Active Directory (Azure AD).|
|[Problèmes de synchronisation des objets](#object-synchronization-problems)|Utilisez les journaux de provisionnement pour résoudre les problèmes de synchronisation des objets.|
|[Problèmes de provisionnement en quarantaine](#provisioning-quarantined-problems)|Comprendre les problèmes de mise en quarantaine du provisionnement et la façon de les corriger.|
|[Réécriture du mot de passe](#password-writeback)|Découvrez les problèmes courants de réécriture du mot de passe et comment les corriger.|


## <a name="agent-problems"></a>Problèmes rencontrés avec l’agent
Voici quelques-unes des premières choses à vérifier au niveau de l’agent :

-  L’agent est-il installé ?
-  L’agent s’exécute-t-il localement ?
-  L’agent se trouve-t-il dans le portail ?
-  L’agent est-il marqué comme sain ?

Vous pouvez vérifier ces points dans le portail Azure et sur le serveur local qui exécute l’agent.

### <a name="azure-portal-agent-verification"></a>Vérification de l’agent dans le portail Azure

Pour vérifier que l’agent est visible par Azure et qu’il est sain, effectuez les étapes suivantes :

1. Connectez-vous au portail Azure.
1. À gauche, sélectionnez **Azure Active Directory** > **Azure AD Connect** Au centre, sélectionnez **Gérer la synchronisation**.
1. Sur l’écran **Synchronisation cloud Azure AD Connect**, sélectionnez **Passer en revue tous les agents**.

   ![Passer en revue tous les agents](media/how-to-install/install-7.png)</br>
 
1. L’écran **Agents de provisionnement locaux** affiche les agents que vous avez installés. Vérifiez que l’agent en question est présent et qu’il est marqué comme étant *Sain*.

   ![Écran Agents de provisionnement locaux](media/how-to-install/install-8.png)</br>

### <a name="verify-the-port"></a>Vérifier le port

Vérifiez que le service Azure est à l’écoute sur le port 443 et que votre agent peut communiquer avec lui. 

Ce test vérifie que vos agents peuvent communiquer avec Azure via le port 443. Ouvrez un navigateur et accédez à l’URL précédents à partir du serveur sur lequel l’agent est installé.

![Vérification de l’accessibilité du port](media/how-to-install/verify-2.png)

### <a name="on-the-local-server"></a>Sur le serveur local

Pour vérifier que l’agent est en cours d’exécution, procédez comme suit.

1. Sur le serveur où l’agent est installé, ouvrez **Services** en y accédant ou en accédant à **Démarrer** > **Exécuter** > **Services.msc**.
1. Sous **Services**, assurez-vous que le **Programme de mise à jour de l’agent Microsoft Azure AD Connect** et l’**agent de provisionnement Microsoft Azure AD Connect** sont présents, et que leur état est *En cours d’exécution*.

   ![Écran Services](media/how-to-troubleshoot/troubleshoot-1.png)

### <a name="common-agent-installation-problems"></a>Problèmes courants concernant l’installation de l’agent

Les sections suivantes décrivent certains problèmes courants liés à l’installation de l’agent et fournissent des solutions à ces problèmes.

#### <a name="agent-failed-to-start"></a>Échec du démarrage de l’agent

Vous pouvez recevoir un message d’erreur indiquant ceci :

**Le service « Agent de provisionnement Microsoft Azure AD Connect » n’a pas pu démarrer. Veillez à avoir les droits nécessaires pour démarrer les services système.** 

Ce problème est généralement dû à une stratégie de groupe qui a empêché l’application d’autorisations sur le compte d’ouverture de session du service NT local créé par le programme d’installation (NT SERVICE\AADConnectProvisioningAgent). Ces autorisations sont nécessaires pour démarrer le service.

Pour résoudre ce problème, effectuez les étapes suivantes.

1. Connectez-vous au serveur avec un compte Administrateur.
1. Ouvrez **Services** en y accédant ou en accédant à **Démarrer** > **Exécuter** > **Services.msc**.
1. Sous **Services**, double-cliquez sur **Agent de provisionnement Microsoft Azure AD Connect**.
1. Sous l’onglet **Ouvrir une session**, remplacez **ce compte** par celui d’un administrateur de domaine. Ensuite, redémarrez le service. 

   ![Onglet Ouvrir une session](media/how-to-troubleshoot/troubleshoot-3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>Le délai de l’agent a expiré ou le certificat n’est pas valide

Les messages d’erreur suivants peuvent s’afficher si vous essayez d’inscrire l’agent.

![Message d’erreur d’expiration du délai](media/how-to-troubleshoot/troubleshoot-4.png)

Ce problème est généralement dû au fait que l’agent ne peut pas se connecter au service d’identité hybride, et nécessite donc la configuration d’un proxy HTTP. Pour résoudre ce problème, configurez un proxy sortant. 

L’agent de provisionnement prend en charge l’utilisation du proxy sortant. Vous pouvez le configurer en modifiant le fichier de configuration *C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config* de l'agent. Ajoutez les lignes suivantes à la fin du fichier, juste avant la balise `</configuration>` de fermeture.
Remplacez les variables `[proxy-server]` et `[proxy-port]` par le nom de votre serveur proxy et les valeurs de port.

```xml
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

#### <a name="agent-registration-fails-with-security-error"></a>L’inscription de l’agent échoue avec une erreur de sécurité

Vous pouvez recevoir un message d’erreur lorsque vous installez l’agent de provisionnement cloud.

Ce problème est généralement dû à l’incapacité de l’agent à exécuter les scripts d’inscription PowerShell en raison des stratégies d’exécution PowerShell locales.

Pour résoudre ce problème, modifiez les stratégies d’exécution PowerShell sur le serveur. Les stratégies de la machine et de l’utilisateur doivent être définies sur *Undefined* ou *RemoteSigned*. Si elles sont définies sur *Unrestricted*, vous verrez ce message d’erreur. Pour plus d’informations, consultez [Stratégies d’exécution PowerShell](/powershell/module/microsoft.powershell.core/about/about_execution_policies). 

### <a name="log-files"></a>Fichiers journaux

Par défaut, l’agent émet peu de messages d’erreur et d’informations sur la trace de la pile. Vous pouvez trouver ces journaux de suivi dans le dossier **C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace**.

Pour obtenir des détails supplémentaires en vue de résoudre les problèmes liés à l’agent, effectuez les étapes suivantes.

1.  Installez le module PowerShell AADCloudSyncTools comme décrit [ici](reference-powershell.md#install-the-aadcloudsynctools-powershell-module).
2. Utilisez l’applet de commande PowerShell `Export-AADCloudSyncToolsLogs` pour capturer les informations.  Vous pouvez utiliser les commutateurs suivants pour affiner votre collecte de données.
      - SkipVerboseTrace pour exporter uniquement les journaux actuels sans capturer les journaux détaillés (par défaut = false)
      - TracingDurationMins pour spécifier une durée de capture différente (par défaut = 3 minutes)
      - OutputPath pour spécifier un autre chemin de sortie (par défaut = les documents de l’utilisateur)


## <a name="object-synchronization-problems"></a>Problèmes de synchronisation des objets

La section suivante contient des informations sur la résolution des problèmes de synchronisation d’objet.

### <a name="provisioning-logs"></a>Journaux d’approvisionnement

Dans le portail Azure, les journaux de provisionnement peuvent être utilisés pour faciliter le suivi et résoudre les problèmes de synchronisation des objets. Pour voir les journaux, sélectionnez **Journaux**.

![Bouton Journaux d’activité](media/how-to-troubleshoot/log-1.png)

Les journaux de provisionnement fournissent une multitude d’informations sur l’état des objets synchronisés entre votre environnement Active Directory local et Azure.

![Écran Journaux de provisionnement](media/how-to-troubleshoot/log-2.png)

Vous pouvez utiliser les listes déroulantes en haut de la page pour filtrer la vue et ainsi mettre en évidence certains problèmes, comme des dates erronées. Double-cliquez sur un événement pour afficher des informations supplémentaires.

![Informations de la liste déroulante des journaux de provisionnement](media/how-to-troubleshoot/log-3.png)

Ces informations détaillent les étapes, et le moment auquel le problème de synchronisation se produit. De cette façon, vous pouvez identifier l’emplacement exact du problème.


## <a name="provisioning-quarantined-problems"></a>Problèmes de provisionnement en quarantaine

La synchronisation cloud supervise l’intégrité de la configuration et place les objets qui ne sont pas sains en état de quarantaine. Si la plupart ou la totalité des appels effectués sur le système cible échouent systématiquement à cause d’une erreur (par exemple, informations d’identification non valides), le travail de synchronisation est mis en quarantaine.

![État de la quarantaine](media/how-to-troubleshoot/quarantine-1.png)

En sélectionnant l’état, vous pouvez voir des informations supplémentaires sur la mise en quarantaine. Vous pouvez également obtenir le code et le message d’erreur.

![Capture d’écran qui affiche des informations supplémentaires sur la quarantaine.](media/how-to-troubleshoot/quarantine-2.png)

Cliquez avec le bouton droit sur le statut pour afficher des options supplémentaires :

- Afficher les journaux de provisionnement
- Afficher l’agent
- Désactiver la mise en quarantaine

![Capture d’écran des options de menu contextuel.](media/how-to-troubleshoot/quarantine-4.png)

### <a name="resolve-a-quarantine"></a>Résoudre une mise en quarantaine

Il existe deux façons différentes de résoudre une quarantaine. Il s'agit des éléments suivants :

- Désactiver la quarantaine : désactive la limite et exécute une synchronisation différentielle
- Redémarrer le travail de provisionnement : désactive la limite et exécute une synchronisation initiale

#### <a name="clear-quarantine"></a>Désactivation de la mise en quarantaine

Pour désactiver et exécuter une synchronisation différentielle sur le travail de provisionnement une fois celui-ci vérifié, il suffit de cliquer avec le bouton droit et de sélectionner **désactiver la mise en quarantaine**.

Une notification vous informe que la mise en quarantaine est en cours de désactivation.

![Capture d’écran qui montre l’avis d’effacement de la mise en quarantaine.](media/how-to-troubleshoot/quarantine-5.png)

Ensuite, l’état de l’agent devient sain.

![Informations sur l’état de mise en quarantaine](media/how-to-troubleshoot/quarantine-6.png)

#### <a name="restart-the-provisioning-job"></a>Redémarrage du travail de provisionnement

Utilisez le portail Azure pour redémarrer le travail de provisionnement. Dans la page de configuration de l’agent, sélectionnez **Redémarrer le provisionnement**.

  ![Redémarrer le provisionnement](media/how-to-troubleshoot/quarantine-3.png)

- Utilisez Microsoft Graph pour [redémarrer le travail de provisionnement](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true). Vous bénéficiez d’un contrôle total sur ce que vous redémarrez. Vous pouvez choisir d’effacer les éléments suivants :

  - Les escrows, afin de redémarrer le compteur des escrows dont l’augmentation amène à la mise en quarantaine
  - La mise en quarantaine, afin de retirer l’application de la quarantaine
  - Les filigranes 
  
  Utilisez la requête suivante :
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="repairing-the-the-cloud-sync-service-account"></a>Réparation du compte de service Cloud Sync

Si vous devez réparer le compte de service Cloud Sync, vous pouvez utiliser `Repair-AADCloudSyncToolsAccount`.

   1. Utilisez les étapes d’installation décrites [ici](reference-powershell.md#install-the-aadcloudsynctools-powershell-module) pour commencer, puis passez aux étapes restantes.

   2. À partir d’une session PowerShell avec des privilèges d’administrateur, tapez ou copiez et collez les éléments suivants :

      ```powershell
      Connect-AADCloudSyncTools
      ```

   3. Entrez vos informations d’identification d’administrateur général Azure AD.

   4. Tapez ou collez le code suivant :

      ```powershell
      Repair-AADCloudSyncToolsAccount
      ```

   5. Une fois cette opération terminée, le compte devrait indiquer que le compte a été correctement réparé.

## <a name="password-writeback"></a>Réécriture du mot de passe
Lors de l’activation et de l’utilisation de la réécriture du mot de passe avec une synchronisation cloud, il est important de garder les informations suivantes à l’esprit.

- Si vous devez mettre à jour les [autorisations gMSA](how-to-gmsa-cmdlets.md#using-set-aadcloudsyncpermissions), la réplication de celles-ci sur tous les objets de l’annuaire peut durer jusqu’à une heure voire davantage. Si vous n’attribuez pas ces autorisations, la réécriture peut sembler être configurée correctement, mais les utilisateurs peuvent rencontrer des erreurs quand ils mettent à jour leurs mots de passe locaux à partir du cloud. Les autorisations doivent être appliquées à « cet objet et tous ceux descendants » pour que l’option **Ne pas faire expirer le mot de passe** apparaisse. 
- Si les mots de passe de certains comptes d’utilisateur ne sont pas réécrits dans l’annuaire local, vérifiez que l’héritage n’est pas désactivé pour le compte dans l’environnement local AD DS. Les autorisations d’écriture pour les mots de passe doivent être appliquées aux objets descendants pour que la fonctionnalité fonctionne correctement. 
- Les stratégies de mot de passe dans l’environnement AD DS local peuvent empêcher le traitement correct des réinitialisations de mot de passe. Si vous testez cette fonctionnalité et souhaitez réinitialiser les mots de passe des utilisateurs plusieurs fois par jour, la stratégie de groupe pour Durée de vie minimale du mot de passe doit être définie sur 0. Ce paramètre se trouve sous **Configuration ordinateur > Stratégies > Paramètres Windows > Paramètres de sécurité > Stratégies de compte** dans **gpmc.msc**. 
     - Si vous mettez à jour la stratégie de groupe, attendez la réplication de la stratégie mise à jour ou utilisez la commande gpupdate /force. 
     - Pour que les mots de passe soient changés immédiatement, l’âge minimum du mot de passe doit être défini sur 0. Toutefois, si les utilisateurs adhèrent aux stratégies locales et que le paramètre Durée de vie minimale du mot de passe est défini sur une valeur supérieure à zéro, la réécriture du mot de passe ne fonctionne pas une fois les stratégies locales évaluées. 





## <a name="next-steps"></a>Étapes suivantes 

- [Limitations connues](how-to-prerequisites.md#known-limitations)
- [Codes d’erreur](reference-error-codes.md)
