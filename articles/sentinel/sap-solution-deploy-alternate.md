---
title: Options de configuration pour experts du connecteur de données SAP Microsoft Sentinel, déploiement local et sources de journaux SAPControl | Microsoft Docs
description: Découvrez comment déployer le connecteur de données de Microsoft  Sentinel pour les environnements SAP à l’aide d’options de configuration pour experts et d’un ordinateur local. Apprenez-en également davantage sur les sources de journaux SAPControl.
author: batamig
ms.author: bagol
ms.service: microsoft-sentinel
ms.topic: how-to
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.subservice: microsoft-sentinel
ms.openlocfilehash: 56176315a6d4d56c419f15a4472aa4f6b739a227
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520024"
---
# <a name="expert-configuration-options-on-premises-deployment-and-sapcontrol-log-sources"></a>Options de configuration pour experts, déploiement local et sources de journaux SAPControl

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Cet article décrit comment déployer le connecteur de données SAP Microsoft Sentinel dans un processus expert ou personnalisé, comme l’utilisation d’un ordinateur local et d’un coffre de clés Azure pour stocker vos informations d’identification.

> [!NOTE]
> Le processus par défaut, et le plus recommandé, pour déployer le connecteur de données SAP Microsoft Sentinel consiste à [utiliser une machine virtuelle Azure](sap-deploy-solution.md). Cet article s’adresse aux utilisateurs avancés.

> [!IMPORTANT]
> La solution SAP Microsoft Sentinel est actuellement en PRÉVERSION. Les [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluent des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore disponibles dans la version en disponibilité générale.
>

## <a name="prerequisites"></a>Prérequis

Les conditions préalables de base pour le déploiement de votre connecteur de données SAP Microsoft Sentinel sont les mêmes, quelle que soit votre méthode de déploiement.

Avant de commencer, assurez-vous que votre système est conforme aux conditions préalables décrites dans la [procédure de déploiement du connecteur de données SAP](sap-deploy-solution.md#prerequisites) principale .

Pour plus d’informations, consultez [Exigences de SAP détaillées pour la solution SAP Microsoft Sentinel (préversion publique)](sap-solution-detailed-requirements.md).

## <a name="create-your-azure-key-vault"></a>Créer votre coffre de clés Azure

Créez un coffre de clés Azure que vous pouvez dédier à votre connecteur de données SAP Microsoft Sentinel.

Exécutez la commande suivante pour créer votre coffre de clés Azure et accorder l’accès à un principal de service Azure :

``` azurecli
kvgp=<KVResourceGroup>

kvname=<keyvaultname>

spname=<sp-name>

kvname=<keyvaultname>
# Optional when Azure MI not enabled - Create sp user for AZ cli connection, save details for env.list file
az ad sp create-for-rbac –name $spname 

SpID=$(az ad sp list –display-name $spname –query “[].appId” --output tsv

#Create key vault
az keyvault create \
  --name $kvname \
  --resource-group $kvgp
  
# Add access to SP
az keyvault set-policy --name $kvname --resource-group $kvgp --object-id $spID --secret-permissions get list set
```

Pour plus d’informations, consultez [Démarrage rapide : Créer un coffre de clés à l’aide d’Azure CLI](../key-vault/general/quick-create-cli.md).

## <a name="add-azure-key-vault-secrets"></a>Ajouter des secrets Azure Key Vault

Pour ajouter des secrets Azure Key Vault, exécutez le script suivant avec votre propre ID système et les informations d’identification que vous souhaitez ajouter :

```azurecli
#Add Abap username
az keyvault secret set \
  --name <SID>-ABAPUSER \
  --value "<abapuser>" \
  --description SECRET_ABAP_USER --vault-name $kvname

#Add Abap Username password
az keyvault secret set \
  --name <SID>-ABAPPASS \
  --value "<abapuserpass>" \
  --description SECRET_ABAP_PASSWORD --vault-name $kvname

#Add java Username
az keyvault secret set \
  --name <SID>-JAVAOSUSER \
  --value "<javauser>" \
  --description SECRET_JAVAOS_USER --vault-name $kvname

#Add java Username password
az keyvault secret set \
  --name <SID>-JAVAOSPASS \
  --value "<javauserpass>" \
  --description SECRET_JAVAOS_PASSWORD --vault-name $kvname

#Add abapos username
az keyvault secret set \
  --name <SID>-ABAPOSUSER \
  --value "<abaposuser>" \
  --description SECRET_ABAPOS_USER --vault-name $kvname

#Add abapos username password
az keyvault secret set \
  --name <SID>-ABAPOSPASS \
  --value "<abaposuserpass>" \
  --description SECRET_ABAPOS_PASSWORD --vault-name $kvname

#Add Azure Log ws ID
az keyvault secret set \
  --name <SID>-LOG_WS_ID \
  --value "<logwsod>" \
  --description SECRET_AZURE_LOG_WS_ID --vault-name $kvname

#Add Azure Log ws public key
az keyvault secret set \
  --name <SID>-LOG_WS_PUBLICKEY \
  --value "<loswspubkey>" \
  --description SECRET_AZURE_LOG_WS_PUBLIC_KEY --vault-name $kvname
```

Pour plus d’informations, consultez la documentation CLI [az keyvault secret](/cli/azure/keyvault/secret).

## <a name="perform-an-expert--custom-installation"></a>Effectuer une installation experte/personnalisée

Cette procédure décrit comment déployer le connecteur de données SAP à l’aide d’une installation experte ou personnalisée, par exemple lors d’une installation locale.

Nous vous recommandons d’effectuer cette procédure après avoir préparé un coffre de clés avec vos informations d’identification SAP.

**Pour déployer le connecteur de données SAP** :

1. Sur votre ordinateur local, téléchargez la version la plus récente du Kit de développement logiciel (SDK) SAP NW RFC à partir du [site SAP Launchpad](https://support.sap.com) > **SAP NW RFC SDK** > **SAP NW RFC SDK 7.50** > **nwrfc750X_X-xxxxxxx.zip**.

    > [!NOTE]
    > Vous aurez besoin de vos informations de connexion d’utilisateur SAP pour accéder au Kit de développement logiciel (SDK), et vous devrez télécharger le Kit de développement logiciel (SDK) qui correspond à votre système d’exploitation.
    >
    > Veillez à sélectionner l’option **LINUX ON X86_64**.

1. Sur votre ordinateur local, créez un nouveau dossier avec un nom explicite, puis copiez le fichier zip du Kit de développement logiciel (SDK) dans votre nouveau dossier.

1. Clonez le référentiel GitHub de la solution Microsoft Sentinel sur votre ordinateur local et copiez le fichier **systemconfig.ini** de la solution SAP Microsoft Sentinel dans votre nouveau dossier.

    Par exemple :

    ```bash
    mkdir /home/$(pwd)/sapcon/<sap-sid>/
    cd /home/$(pwd)/sapcon/<sap-sid>/
    wget  https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/template/systemconfig.ini 
    cp <**nwrfc750X_X-xxxxxxx.zip**> /home/$(pwd)/sapcon/<sap-sid>/
    ```

1. Modifiez le fichier **systemconfig.ini** le cas échéant en utilisant les commentaires incorporés comme guide. Pour plus d’informations, consultez [Configurer manuellement le connecteur de données SAP](#manually-configure-the-sap-data-connector).

    Pour tester votre configuration, vous pouvez ajouter l’utilisateur et le mot de passe directement au fichier config **systemconfig.ini**. Bien que nous vous recommandons d’utiliser [Azure Key Vault](#add-azure-key-vault-secrets) pour stocker vos informations d’identification, vous pouvez également utiliser un fichier **env.list** ou des [secrets Docker](#manually-configure-the-sap-data-connector), ou vous pouvez ajouter vos informations d’identification directement au fichier **systemconfig.ini**.

1. Définissez les journaux que vous souhaitez ingérer dans Microsoft Sentinel à l’aide des instructions du fichier **systemconfig.ini**. Par exemple, consultez [Définir les journaux SAP qui sont envoyés à Microsoft Sentinel](#define-the-sap-logs-that-are-sent-to-microsoft-sentinel).

1. Définissez les configurations suivantes à l’aide des instructions du fichier **systemconfig.ini** :

    - S’il faut inclure ou non les adresses e-mail des utilisateurs dans les journaux d’audit
    - S’il faut réessayer ou non les appels d’API qui ont échoué
    - S’il faut inclure ou non les journaux d’audit cexal
    - S’il faut attendre ou non un intervalle de temps entre les extractions de données, en particulier pour les extractions volumineuses

    Pour plus d’informations, consultez [Configurations du connecteur de journaux SAL](#sal-logs-connector-settings).

1. Enregistrez votre fichier **systemconfig.ini** mis à jour dans le répertoire **sapcon** sur votre ordinateur.

1. Si vous avez choisi d’utiliser un fichier **env.list** pour vos informations d’identification, créez un fichier **env.list** temporaire avec les informations d’identification requises. Une fois que votre conteneur Docker fonctionne correctement, veillez à supprimer ce fichier.

    > [!NOTE]
    > Dans le script suivant, chaque conteneur Docker se connecte à un système ABAP spécifique. Modifiez votre script selon les besoins de votre environnement.
    >

    Exécutez :

    ```bash
    ##############################################################
    # Include the following section if you're using user authentication
    ##############################################################
    # env.list template for Credentials
    SAPADMUSER=<SET_SAPCONTROL_USER>
    SAPADMPASSWORD=<SET_SAPCONTROL_PASS>
    ABAPUSER=SET_ABAP_USER>
    ABAPPASS=<SET_ABAP_PASS>
    JAVAUSER=<SET_JAVA_OS_USER>
    JAVAPASS=<SET_JAVA_OS_USER>
    ##############################################################
    # Include the following section if you are using Azure Keyvault
    ##############################################################
    # env.list template for AZ Cli when MI is not enabled
    AZURE_TENANT_ID=<your tenant id>
    AZURE_CLIENT_ID=<your client/app id>
    AZURE_CLIENT_SECRET=<your password/secret for the service principal>
    ##############################################################
    ```

1. Téléchargez et exécutez l’image Docker prédéfinie avec le connecteur de données SAP installé.  Exécutez :

    ```bash
    docker pull docker pull mcr.microsoft.com/azure-sentinel/solutions/sapcon:latest-preview
    docker run --env-file=<env.list_location> -d --restart unless-stopped -v /home/$(pwd)/sapcon/<sap-sid>/:/sapcon-app/sapcon/config/system --name sapcon-<sid> sapcon
    rm -f <env.list_location>
    ```

1. Vérifiez que le conteneur Docker fonctionne correctement. Exécutez :

    ```bash
    docker logs –f sapcon-[SID]
    ```

1. Poursuivez en déployant la solution **Microsoft Sentinel – Surveillance continue des menaces pour SAP**.

    Le déploiement de la solution permet au connecteur de données SAP de s’afficher dans Microsoft Sentinel et déploie le classeur et les règles d’analyse SAP. Lorsque vous avez terminé, ajoutez manuellement et personnalisez vos listes de surveillance SAP.

    Pour plus d’informations, consultez [Déployer le contenu de sécurité SAP](sap-deploy-solution.md#deploy-sap-security-content).

## <a name="manually-configure-the-sap-data-connector"></a>Configurer manuellement le connecteur de données SAP

Le connecteur de données de la solution SAP Microsoft Sentinel est configuré dans le fichier **systemconfig.ini**, que vous avez cloné sur votre ordinateur de connecteur de données SAP dans le cadre de la [procédure de déploiement](#perform-an-expert--custom-installation).

Le code suivant montre un exemple de fichier **systemconfig.ini** :

```python
[Secrets Source]
secrets = '<DOCKER_RUNTIME/AZURE_KEY_VAULT/DOCKER_SECRETS/DOCKER_FIXED>'
keyvault = '<SET_YOUR_AZURE_KEYVAULT>'
intprefix = '<SET_YOUR_PREFIX>'

[ABAP Central Instance]
##############################################################
# Define the following values according to your server configuration.
ashost = <SET_YOUR_APPLICATION_SERVER_HOST>
mshost = <SET_YOUR_MESSAGE_SERVER_HOST> - #In case different then App
##############################################################
group = <SET_YOUR_LOGON_GROUP>
msserv = <SET_YOUR_MS_SERVICE> - #Required only if the message server service is not defined as sapms<SYSID> in /etc/services
sysnr = <SET_YOUR_SYS_NUMBER>
user = <SET_YOUR_USER>
##############################################################
# Enter your password OR your X509 SNC parameters
passwd = <SET_YOUR_PASSWORD>
snc_partnername = <SET_YOUR_SNC_PARTNER_NAME>
snc_lib = <SET_YOUR_SNC_LIBRARY_PATH>
x509cert = <SET_YOUR_X509_CERTIFICATE>
##############################################################
sysid = <SET_YOUR_SYSTEM_ID>
client = <SET_YOUR_CLIENT>

[Azure Credentials]
loganalyticswsid = <SET_YOUR_LOG_ANALYTICS_WORKSPACE_ID>
publickey = <SET_YOUR_PUBLIC_KEY>

[File Extraction ABAP]
osuser = <SET_YOUR_SAPADM_LIKE_USER>
##############################################################
# Enter your password OR your X509 SNC parameters
ospasswd = <SET_YOUR_SAPADM_PASS>
x509pkicert = <SET_YOUR_X509_PKI_CERTIFICATE>
##############################################################
appserver = <SET_YOUR_SAPCTRL_SERVER IP OR FQDN>
instance = <SET_YOUR_SAP_INSTANCE NUMBER, example 10>
abapseverity = <SET_ABAP_SEVERITY 0 = All logs ; 1 = Warning ; 2 = Error>
abaptz = <SET_ABAP_TZ --Use ONLY GMT FORMAT-- example - For OS Timezone = NZST use abaptz = GMT+12>

[File Extraction JAVA]
javaosuser = <SET_YOUR_JAVAADM_LIKE_USER>
##############################################################
# Enter your password OR your X509 SNC parameters
javaospasswd = <SET_YOUR_JAVAADM_PASS>
javax509pkicert = <SET_YOUR_X509_PKI_CERTIFICATE>
##############################################################
javaappserver = <SET_YOUR_JAVA_SAPCTRL_SERVER IP ADDRESS OR FQDN>
javainstance = <SET_YOUR_JAVA_SAP_INSTANCE for example 10>
javaseverity = <SET_JAVA_SEVERITY  0 = All logs ; 1 = Warning ; 2 = Error>
javatz = <SET_JAVA_TZ --Use ONLY GMT FORMAT-- example - For OS Timezone = NZST use javatz = GMT+12>
```

### <a name="define-the-sap-logs-that-are-sent-to-microsoft-sentinel"></a>Définir les journaux SAP qui sont envoyés à Microsoft Sentinel

Ajoutez le code suivant au fichier **systemconfig.ini** de la solution SAP Microsoft Sentinel pour définir les journaux qui sont envoyés à Microsoft Sentinel.

Pour plus d’informations, consultez [Informations de référence sur les journaux de la solution SAP Microsoft Sentinel (préversion publique)](sap-solution-log-reference.md).

```python
##############################################################
# Enter True OR False for each log to send those logs to Microsoft Sentinel
[Logs Activation Status]
ABAPAuditLog = True
ABAPJobLog = True
ABAPSpoolLog = True
ABAPSpoolOutputLog = True
ABAPChangeDocsLog = True
ABAPAppLog = True
ABAPWorkflowLog = True
ABAPCRLog = True
ABAPTableDataLog = False
# ABAP SAP Control Logs - Retrieved by using SAP Conntrol interface and OS Login
ABAPFilesLogs = False
SysLog = False
ICM = False
WP = False
GW = False
# Java SAP Control Logs - Retrieved by using SAP Conntrol interface and OS Login
JAVAFilesLogs = False
##############################################################
```

### <a name="sal-logs-connector-settings"></a>Paramètres du connecteur de journaux SAL

Ajoutez le code suivant au fichier **systemconfig.ini** du connecteur de données SAP Microsoft Sentinel pour définir d’autres paramètres pour les journaux SAP ingérés dans Microsoft Sentinel.

Pour plus d’informations, consultez [Effectuer une installation experte/personnalisée du connecteur de données SAP](#perform-an-expert--custom-installation).

```python
##############################################################
[Connector Configuration]
extractuseremail = True
apiretry = True
auditlogforcexal = False
auditlogforcelegacyfiles = False
timechunk = 60
##############################################################
```

Cette section vous permet de configurer les paramètres suivants :

|Nom du paramètre  |Description  |
|---------|---------|
|**extractuseremail**     |  Détermine si les adresses e-mail des utilisateurs sont incluses dans les journaux d’audit.       |
|**apiretry**     |   Détermine si les appels d’API font l’objet d’une nouvelle tentative comme mécanisme de basculement.      |
|**auditlogforcexal**     |  Détermine si le système force l’utilisation de journaux d’audit pour les systèmes non SAL, tels que SAP BASIS version 7.4.       |
|**auditlogforcelegacyfiles**     |  Détermine si le système force l’utilisation de journaux d’audit avec les capacités système héritées, par exemple celles de SAP BASIS version 7.4 avec des niveaux de correctifs inférieurs.|
|**timechunk**     |   Détermine si le système attend un nombre spécifique de minutes comme intervalle entre les extractions de données. Utilisez ce paramètre si une grande quantité de données est attendue. <br><br>Par exemple, pendant le chargement initial des données au cours des premières 24 heures, vous souhaiterez peut-être que l’extraction des données soit seulement exécutée toutes les 30 minutes afin de laisser suffisamment de temps à chaque extraction de données. Dans ce cas, définissez cette valeur sur **30**.  |
|     |         |

### <a name="configuring-an-abap-sap-control-instance"></a>Configuration d’une instance de contrôle SAP ABAP

Pour ingérer tous les journaux ABAP dans Microsoft Sentinel, y compris les journaux basés sur le service web de contrôle NW RFC et SAP, configurez les informations de contrôle SAP ABAP suivantes :

|Paramètre  |Description  |
|---------|---------|
|**javaappserver**     |Entrez votre hôte de serveur ABAP de contrôle SAP. <br>Par exemple : `contoso-erp.appserver.com`         |
|**javainstance**     |Entrez votre numéro d’instance ABAP de contrôle SAP. <br>Par exemple : `00`         |
|**abaptz**     |Entrez le fuseau horaire configuré sur votre serveur ABAP de contrôle SAP, au format GMT. <br>Par exemple : `GMT+3`         |
|**abapseverity**     |Entrez le niveau de gravité le plus bas, inclus, pour lequel vous souhaitez ingérer les journaux ABAP dans Microsoft Sentinel.  Ces valeurs comprennent : <br><br>- **0** = Tous les journaux <br>- **1** = Avertissement <br>- **2** = Erreur     |

### <a name="configuring-a-java-sap-control-instance"></a>Configuration d’une instance de contrôle SAP Java

Pour ingérer les journaux du service Web de contrôle SAP dans Microsoft Sentinel, configurez les informations sur l’instance de contrôle SAP JAVA suivantes :

|Paramètre  |Description  |
|---------|---------|
|**javaappserver**     |Entrez votre hôte de serveur Java de contrôle SAP. <br>Par exemple : `contoso-java.server.com`         |
|**javainstance**     |Entrez votre numéro d’instance ABAP de contrôle SAP. <br>Par exemple : `10`         |
|**javatz**     |Entrez le fuseau horaire configuré sur votre serveur Java de contrôle SAP, au format GMT. <br>Par exemple : `GMT+3`         |
|**javaseverity**     |Entrez le niveau de gravité le plus bas, inclus, pour lequel vous souhaitez ingérer les journaux de service web dans Microsoft Sentinel.  Ces valeurs comprennent : <br><br>- **0** = Tous les journaux <br>- **1** = Avertissement <br>- **2** = Erreur     |

## <a name="next-steps"></a>Étapes suivantes

Après avoir installé votre connecteur de données SAP, vous pouvez ajouter le contenu de sécurité lié à SAP.

Pour plus d’informations, consultez [Déployer la solution SAP](sap-deploy-solution.md#deploy-sap-security-content).

Pour plus d'informations, consultez les pages suivantes :

- [Déployer le connecteur de données SAP Microsoft Sentinel avec SNC](sap-solution-deploy-snc.md)
- [Exigences de SAP détaillées pour la solution SAP Microsoft Sentinel](sap-solution-detailed-requirements.md)
- [Informations de référence sur les journaux de la solution SAP Microsoft Sentinel](sap-solution-log-reference.md)
- [Solution SAP Microsoft Sentinel : informations de référence relatives au contenu de sécurité](sap-solution-security-content.md)
- [Résolution des problèmes de déploiement de votre solution SAP Microsoft Sentinel](sap-deploy-troubleshoot.md)
