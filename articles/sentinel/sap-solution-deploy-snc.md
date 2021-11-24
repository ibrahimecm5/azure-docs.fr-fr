---
title: Déployer le connecteur de données SAP Microsoft Sentinel avec des Communications réseau sécurisées (CRS) | Microsoft Docs
description: Découvrez comment déployer le connecteur de données Microsoft Sentinel pour les environnements SAP avec une connexion sécurisée via SNC, pour les journaux basés sur l’interface NetWeaver/ABAP.
author: batamig
ms.author: bagol
ms.service: microsoft-sentinel
ms.topic: how-to
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.subservice: microsoft-sentinel
ms.openlocfilehash: cc3c58743fc1cfca90ff8a2f20e5450895accc75
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132519873"
---
# <a name="deploy-the-microsoft-sentinel-sap-data-connector-with-snc"></a>Déployer le connecteur de données SAP Microsoft Sentinel avec SNC

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Cet article décrit comment déployer le connecteur de données SAP Microsoft Sentinel lorsque vous disposez d’une connexion sécurisée à SAP par le biais des Communications réseau sécurisées (SNC) pour les journaux basés sur l’interface NetWeaver/ABAP.

> [!NOTE]
> Le processus par défaut, et le plus recommandé, pour déployer le connecteur de données SAP Microsoft Sentinel consiste à [utiliser une machine virtuelle Azure](sap-deploy-solution.md). Cet article s’adresse aux utilisateurs avancés.

> [!IMPORTANT]
> La solution SAP Microsoft Sentinel est actuellement en PRÉVERSION. Les [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluent des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore disponibles dans la version en disponibilité générale.

## <a name="prerequisites"></a>Prérequis

Les conditions préalables de base pour le déploiement de votre connecteur de données SAP Microsoft Sentinel sont les mêmes, quelle que soit votre méthode de déploiement.

Avant de commencer, assurez-vous que votre système est conforme aux conditions préalables décrites dans la [procédure de déploiement du connecteur de données SAP](sap-deploy-solution.md#prerequisites) principale .

Voici d’autres conditions préalables à l’utilisation de SNC :

- **Une connexion sécurisée à SAP avec SNC**. Définissez les paramètres SNC spécifiques à la connexion dans les constantes de référentiel pour le système AS ABAP système auquel vous vous connectez. Pour plus d’informations, consultez la [page wiki de la communauté SAP](https://wiki.scn.sap.com/wiki/display/Security/Securing+Connections+to+AS+ABAP+with+SNC) correspondante.

- **L’utilitaire SAPCAR**, téléchargé à partir de la Place de marché du Service SAP. Pour plus d’informations, consultez le [Guide d’installation SAP](https://help.sap.com/viewer/d1d04c0d65964a9b91589ae7afc1bd45/5.0.4/en-US/467291d0dc104d19bba073a0380dc6b4.html)

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

## <a name="deploy-the-sap-data-connector"></a>Déployer le connecteur de données SAP

Cette procédure décrit comment déployer le connecteur de données SAP sur une machine virtuelle lors de la connexion via SNC.

Nous vous recommandons d’effectuer cette procédure après avoir préparé un [coffre de clés](#create-your-azure-key-vault) avec vos [informations d’identification SAP](#add-azure-key-vault-secrets).

**Pour déployer le connecteur de données SAP** :

1. Sur la machine virtuelle de votre connecteur de données, téléchargez la version la plus récente du Kit de développement logiciel (SDK) SAP NW RFC à partir du [site SAP Launchpad](https://support.sap.com) > **SAP NW RFC SDK** > **SAP NW RFC SDK 7.50** > **nwrfc750X_X-xxxxxxx.zip**.

    > [!NOTE]
    > Vous aurez besoin de vos informations de connexion d’utilisateur SAP pour accéder au Kit de développement logiciel (SDK), et vous devrez télécharger le Kit de développement logiciel (SDK) qui correspond à votre système d’exploitation.
    >
    > Veillez à sélectionner l’option **LINUX ON X86_64**.

1. Créez un nouveau dossier avec un nom explicite, puis copiez le fichier zip du Kit de développement logiciel (SDK) dans votre nouveau dossier.

1. Clonez le référentiel GitHub de la solution Microsoft Sentinel sur la machine virtuelle de votre connecteur de données, puis copiez le fichier **systemconfig.ini** de la solution SAP Microsoft Sentinel dans votre nouveau dossier.

    Par exemple :

    ```bash
    mkdir /home/$(pwd)/sapcon/<sap-sid>/
    cd /home/$(pwd)/sapcon/<sap-sid>/
    wget  https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/template/systemconfig.ini
    cp <**nwrfc750X_X-xxxxxxx.zip**> /home/$(pwd)/sapcon/<sap-sid>/
    ```

1. Modifiez le fichier **systemconfig.ini** le cas échéant en utilisant les commentaires incorporés comme guide.

    Vous devrez modifier toutes les configurations, à l’exception des secrets du coffre de clés. Pour plus d’informations, consultez [Configurer manuellement le connecteur de données SAP](sap-solution-deploy-alternate.md#manually-configure-the-sap-data-connector).

1. Définissez les journaux que vous souhaitez ingérer dans Microsoft Sentinel à l’aide des instructions du fichier **systemconfig.ini**.

    Par exemple, consultez [Définir les journaux SAP qui sont envoyés à Microsoft Sentinel](sap-solution-deploy-alternate.md#define-the-sap-logs-that-are-sent-to-microsoft-sentinel).

    > [!NOTE]
    > Les journaux pertinents pour les communications SNC sont uniquement les journaux qui sont récupérés par le biais de l’interface NetWeaver/ABAP. Les journaux de contrôle et HANA SAP sont hors de portée pour SNC.
    >

1. Définissez les configurations suivantes à l’aide des instructions du fichier **systemconfig.ini** :

    - S’il faut inclure ou non les adresses e-mail des utilisateurs dans les journaux d’audit
    - S’il faut réessayer ou non les appels d’API qui ont échoué
    - S’il faut inclure ou non les journaux d’audit cexal
    - S’il faut attendre ou non un intervalle de temps entre les extractions de données, en particulier pour les extractions volumineuses

    Pour plus d’informations, consultez [Configurations du connecteur de journaux SAL](sap-solution-deploy-alternate.md#sal-logs-connector-settings).

1. Enregistrez votre fichier **systemconfig.ini** mis à jour dans le répertoire **sapcon** de votre machine virtuelle.

1. Téléchargez et exécutez l’image Docker prédéfinie avec le connecteur de données SAP installé. Exécutez :

    ```bash
    docker pull docker pull mcr.microsoft.com/azure-sentinel/solutions/sapcon:latest-preview
    docker create -v $(pwd):/sapcon-app/sapcon/config/system -v /home/azureuser /sap/sec:/sapcon-app/sec --env SCUDIR=/sapcon-app/sec --name sapcon-snc mcr.microsoft.com/azure-sentinel/solutions/sapcon:latest-preview
    ```

## <a name="post-deployment-sap-system-procedures"></a>Procédures système SAP postérieures au déploiement

Après avoir déployé votre connecteur de données SAP, effectuez les procédures système SAP suivantes :

1. Téléchargez la bibliothèque cryptographique SAP à partir de la [Place de marché du Service SAP](https://launchpad.support.sap.com/#/) > **Téléchargements de logiciels** > **Parcourir notre catalogue de téléchargements** > **Logiciel cryptographique SAP**.

    Pour plus d’informations, consultez le [Guide d’installation SAP](https://help.sap.com/viewer/d1d04c0d65964a9b91589ae7afc1bd45/5.0.4/en-US/86921b29cac044d68d30e7b125846860.html).

1. Utilisez l’utilitaire SAPCAR pour extraire les fichiers de la bibliothèque, puis déployez-les sur la machine virtuelle de votre connecteur de données SAP, dans le répertoire `<sec>`.

1. Vérifiez que vous disposez des autorisations nécessaires pour exécuter les fichiers de la bibliothèque.

1. Définissez une variable d’environnement appelée **SECUDIR**, avec une valeur du chemin d’accès complet vers le `<sec>` répertoire.

1. Créer un environnement de sécurité personnel (PSE). L’outil en ligne de commande **sapgenspe** est disponible dans le répertoire `<sec>` de la machine virtuelle de votre connecteur de données SAP.

    Par exemple :

    ```bash
    ./sapgenpse get_pse -p my_pse.pse -noreq -x my_pin "CN=sapcon.com, O=my_company, C=IL"
    ```

    Pour plus d'informations, consultez [Créer un Environnement de sécurité personnel](https://help.sap.com/viewer/4773a9ae1296411a9d5c24873a8d418c/8.0/en-US/285bb1fda3fa472c8d9205bae17a6f95.html)dans la documentation SAP .

1. Créez des informations d’identification pour votre PSE. Par exemple :

    ```bash
    ./sapgenpse seclogin -p my_pse.pse -x my_pin -O MXDispatcher_Service_User
    ```

    Pour plus d’informations, consultez [Créer des informations d’identification](https://help.sap.com/viewer/4773a9ae1296411a9d5c24873a8d418c/8.0/en-US/d8b50371667740e797e6c9f0e9b7141f.html) dans la documentation SAP.

1. Echanger les certificats de Clé publique entre le Centre d’identité et le  PSE SNC de l’instance AS ABAP.

    Par exemple, pour exporter le certificat de Clé publique du centre d’identité, exécutez :

    ```bash
    ./sapgenpse export_own_cert -o my_cert.crt -p my_pse.pse -x abcpin
    ```

    Importez le certificat sur le PSE SNC de l’instance AS ABAP, exportez-le à partir du PSE, puis importez-le dans le Centre d’identité.

    Par exemple, pour importer le certificat vers le Centre d’identité, exécutez :

    ```bash
    ./sapgenpse maintain_pk -a full_path/my_secure_dir/my_exported_cert.crt -p my_pse.pse -x my_pin
    ```

    Pour plus d'informations, consultez [Echanger les certificats de Clé](https://help.sap.com/viewer/4773a9ae1296411a9d5c24873a8d418c/8.0/en-US/7bbf90b29c694e6080e968559170fbcd.html) dans la documentation SAP.

## <a name="edit-the-sap-data-connector-configuration"></a>Modifier la configuration du connecteur de données SAP

1. Sur la machine virtuelle de votre connecteur de données SAP, accédez au fichier **systemconfig.ini** et définissez les paramètres suivants avec les valeurs appropriées :

    ```ini
    [Secrets Source]
    secrets = AZURE_KEY_VAULT
    ```

1. Dans votre [coffre de clés Azure](#create-your-azure-key-vault), générez les secrets suivants :

    - `<Interprefix>-ABAPSNCPARTNERNAME`, où la valeur est le `<Relevant DN details>`
    - `<Interprefix>-ABAPSNCLIB`, où la valeur est le `<lib_Path>`
    - `<Interprefix>-ABAPX509CERT`, où la valeur est le `<Certificate_Code>)`

    Par exemple :

    ```ini
    S4H-ABAPSNCPARTNERNAME  =  'p:CN=help.sap.com, O=SAP_SE, C=IL' (Relevant DN)
    S4H-ABAPSNCLIB = 'home/user/sec-dir' (Relevant directory)
    S4H-ABAPX509CERT = 'MIIDJjCCAtCgAwIBAgIBNzA ... NgalgcTJf3iUjZ1e5Iv5PLKO' (Relevant certificate code)
    ```

    > [!NOTE]
    > Par défaut, la valeur `<Interprefix>` est votre SID, tel que `A4H-<ABAPSNCPARTNERNAME>`.
    >

Si vous entrez des secrets directement dans le fichier de configuration, définissez les paramètres comme suit :

```ini
[Secrets Source]
secrets = DOCKER_FIXED
[ABAP Central Instance]
snc_partnername =  <Relevant_DN_Deatils>
snc_lib =  <lib_Path>
x509cert = <Certificate_Code>
For example:
snc_partnername =  p:CN=help.sap.com, O=SAP_SE, C=IL (Relevant DN)
snc_lib = /sapcon-app/sec/libsapcrypto.so (Relevant directory)
x509cert = MIIDJjCCAtCgAwIBAgIBNzA ... NgalgcTJf3iUjZ1e5Iv5PLKO (Relevant certificate code)
```

### <a name="attach-the-snc-parameters-to-your-user"></a>Associer les paramètres SNC à votre utilisateur

1. Sur la machine virtuelle de votre connecteur de données SAP, appelez la transaction `SM30` puis sélectionnez pour conserver la table `USRACLEXT`.

1. Ajouter une nouvelle entrée. Dans le champ **Utilisateur**, entrez l’utilisateur de communication utilisé pour la connexion au système ABAP.

1. Lorsque vous y êtes invité, entrez le nom SNC. Le nom SNC est le nom unique fourni lorsque vous avez créé le Gestionnaire d’identité PSE. Par exemple : `CN=IDM, OU=SAP, C=DE`

    Veillez à ajouter un `p` avant le nom SNC. Par exemple : `p:CN=IDM, OU=SAP, C=DE`.

1. Sélectionnez **Enregistrer**.

SNC est activée sur la machine virtuelle de votre connecteur de données.

## <a name="activate-the-sap-data-connector"></a>Activer le connecteur de données SAP

Cette procédure décrit comment activer le connecteur de données SAP à l’aide de la connexion SNC sécurisée que vous avez créée à l’aide des procédures décrites plus haut dans cet article.

1. Activer l’image docker :

    ```bash
    docker start sapcon-<SID>
    ```

1. Vérifier la connexion. Exécutez :

    ```bash
    docker logs sapcon-<SID>
    ```

1. Si la connexion échoue, utilisez les journaux pour comprendre le problème.

    Si nécessaire, désactivez l’image docker :

    ```bash
    docker stop sapcon-<SID>
    ```

Par exemple, des problèmes peuvent se produire en raison d’une configuration incorrecte dans le fichier **systemconfig.ini** ou dans votre coffre de clés Azure, ou certaines des étapes de création d’une connexion sécurisée via SNC n’ont pas été exécutées correctement.

Essayez d’effectuer les étapes ci-dessus pour configurer une connexion sécurisée via SNC. Pour plus d’informations, consultez également [Résolution des problèmes liés à au déploiement de votre solution SAP Microsoft Sentinel](sap-deploy-troubleshoot.md).

## <a name="next-steps"></a>Étapes suivantes

Une fois votre Connecteur de données SAP activé, continuez en déployant la solution **Microsoft Sentinel - Surveillance continue des menaces pour SAP**. Pour plus d’informations, consultez [Déployer le contenu de sécurité SAP](sap-deploy-solution.md#deploy-sap-security-content).

Le déploiement de la solution permet au connecteur de données SAP de s’afficher dans Microsoft Sentinel et déploie le classeur et les règles d’analyse SAP. Lorsque vous avez terminé, ajoutez manuellement et personnalisez vos listes de surveillance SAP.

Pour plus d'informations, consultez les pages suivantes :

- [Exigences de SAP détaillées pour la solution SAP Microsoft Sentinel](sap-solution-detailed-requirements.md)
- [Informations de référence sur les journaux de la solution SAP Microsoft Sentinel](sap-solution-log-reference.md)
- [Solution SAP Microsoft Sentinel : informations de référence relatives au contenu de sécurité](sap-solution-security-content.md)
