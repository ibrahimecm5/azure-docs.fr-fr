---
title: Déployer une surveillance continue des menaces SAP | Microsoft Docs
description: Découvrez comment déployer la solution Microsoft Sentinel pour environnements SAP.
author: batamig
ms.author: bagol
ms.service: microsoft-sentinel
ms.topic: how-to
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.subservice: microsoft-sentinel
ms.openlocfilehash: e8d58104e2a622482280ea0a546dbacc95cdc832
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520101"
---
#  <a name="deploy-sap-continuous-threat-monitoring-preview"></a>Déployer une surveillance continue des menaces SAP (préversion)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Cet article vous guide pas à pas dans le processus de déploiement de la solution Microsoft Sentinel de surveillance continue des menaces pour SAP.

> [!IMPORTANT]
> La solution SAP Microsoft Sentinel est actuellement en PRÉVERSION. Les [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluent des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore disponibles dans la version en disponibilité générale.
>

## <a name="overview"></a>Vue d’ensemble

Les [solutions Microsoft Sentinel](sentinel-solutions.md) incluent du contenu de sécurité groupé, tel que des détections de menaces, des classeurs et des watchlists. Grâce à ces solutions, vous pouvez intégrer le contenu de sécurité Microsoft Sentinel pour un connecteur de données spécifique à l’aide d’un processus unique.

En utilisant le connecteur de données SAP Microsoft Sentinel, vous pouvez surveiller des systèmes SAP afin de détecter des menaces sophistiquées au sein des couches métier et application.

Le connecteur de données SAP diffuse 14 journaux d’application à partir de l’ensemble du paysage du système SAP. Le connecteur de données collecte les journaux à partir de ABAP (Advanced Business Application programming) par le biais d’appels RFC NetWeaver et de données de stockage de fichiers via l’interface de contrôle OSSAP. Le connecteur de données SAP ajoute à Microsoft Sentinel la capacité de surveiller l’infrastructure SAP sous-jacente.

Pour ingérer des journaux SAP dans Microsoft Sentinel, le connecteur de données SAP Microsoft Sentinel doit être installé sur votre environnement SAP. Pour le déploiement, nous vous recommandons d’utiliser un conteneur Docker sur une machine virtuelle Azure, comme décrit dans ce didacticiel.

Une fois le connecteur de données SAP déployé, déployez le contenu de sécurité de la solution SAP pour avoir un aperçu de l’environnement SAP de votre organisation et améliorer les fonctionnalités d’opération de sécurité associées.

Dans cet article, vous allez apprendre à :

> [!div class="checklist"]
> * Préparer votre système SAP pour le déploiement du connecteur de données SAP.
> * Utiliser un conteneur Docker et une machine virtuelle Azure pour déployer le connecteur de données SAP.
> * Déployer le contenu de sécurité de la solution SAP dans Microsoft Sentinel.

> [!NOTE]
> Des étapes supplémentaires sont nécessaires pour déployer votre connecteur de données SAP via une connexion SNC (Secure Network Communications) sécurisée. Pour plus d’informations, consultez [Déployer le connecteur de données SAP Microsoft Sentinel avec SNC](sap-solution-deploy-snc.md).
>
## <a name="prerequisites"></a>Prérequis

Pour déployer le connecteur de données SAP Microsoft Sentinel et le contenu de sécurité comme décrit dans ce tutoriel, vous devez disposer des éléments suivants :

| Domaine | Description |
| --- | --- |
|**Conditions préalables pour Azure** | **Accéder à Microsoft Sentinel**. Prenez note de l’ID et de la clé de votre espace de travail Microsoft Sentinel à utiliser dans ce tutoriel lors du [déploiement de votre connecteur de données SAP](#deploy-your-sap-data-connector). <br><br>Pour afficher ces détails à partir de Microsoft Sentinel, accédez à **Paramètres** > **Paramètres d’espace de travail** > **Gestion des agents**. <br><br>**Possibilité de créer des ressources Azure**. Pour plus d’informations, consultez la [documentation sur Azure Resource Manager](../azure-resource-manager/management/manage-resources-portal.md). <br><br>**Accédez à votre coffre de clés Azure**. Ce tutoriel décrit les étapes recommandées pour utiliser votre solution Azure Key Vault afin de stocker vos informations d’identification. Pour plus d’informations, consultez la [documentation sur Azure Key Vault](../key-vault/index.yml). |
|**Prérequis système** | **Logiciels**. Le script de déploiement du connecteur SAP Data installe automatiquement les logiciels requis. Pour plus d’informations, consultez [Logiciel installés automatiquement](#automatically-installed-software). <br><br> **Connectivité du système**. Assurez-vous que la machine virtuelle hôte du connecteur de données SAP a accès aux ressources suivantes : <br>- Microsoft Sentinel <br>- Votre coffre Azure Key Vault <br>- L’hôte de l’environnement SAP, via les ports TCP suivants : *32xx*, *5xx13* et *33xx*, où *xx* est le numéro de l’instance SAP. <br><br>Assurez-vous que vous disposez également d’un compte d’utilisateur SAP pour accéder à la page de téléchargement des logiciels SAP.<br><br>**Architecture du système**. La solution SAP est déployée sur une machine virtuelle en tant que conteneur Docker, et chaque client SAP requiert sa propre instance de conteneur. Pour obtenir des recommandations relatives au dimensionnement, consultez [Dimensionnement recommandé des machines virtuelles](sap-solution-detailed-requirements.md#recommended-virtual-machine-sizing). <br>Votre machine virtuelle et l’espace de travail Microsoft Sentinel peuvent se trouver dans des abonnements Azure différents, voire dans des locataires Azure AD différents.|
|**Configuration requise pour SAP** | **Versions de SAP prises en charge**. Nous vous recommandons d’utiliser [SAP_BASIS versions 750 SP13](https://support.sap.com/en/my-support/software-downloads/support-package-stacks/product-versions.html#:~:text=SAP%20NetWeaver%20%20%20%20SAP%20Product%20Version,%20%20SAPKB710%3Cxx%3E%20%207%20more%20rows) ou versions ultérieures. <br><br>Certaines étapes de ce tutoriel fournissent des instructions distinctes si vous travaillez sur la version antérieure [SAP_BASIS 740](https://support.sap.com/en/my-support/software-downloads/support-package-stacks/product-versions.html#:~:text=SAP%20NetWeaver%20%20%20%20SAP%20Product%20Version,%20%20SAPKB710%3Cxx%3E%20%207%20more%20rows).<br><br> **Détails du système SAP**. Prenez note des détails suivants du système SAP à utiliser dans ce tutoriel :<br>- Adresse IP du système SAP<br>- Numéro du système SAP, par exemple `00`<br>- ID du système SAP, provenant du système SAP NetWeaver (par exemple, `NPL`) <br>- ID client SAP, tel que `001`<br><br>**Accès à l’instance SAP NetWeaver**. L’accès à vos instances SAP doit utiliser l’une des options suivantes : <br>- [Utilisateur/mot de passe SAP ABAP](#configure-your-sap-system). <br>- Un utilisateur avec un certificat x509, utilisant SAP CRYPTOLIB PSE. Cette option peut nécessiter des étapes manuelles expertes.<br><br>**Support de votre équipe SAP**.  Vous aurez besoin du support de votre équipe SAP pour vous assurer que votre système SAP est [correctement configuré](#configure-your-sap-system) pour le déploiement de la solution. |
| | |

### <a name="automatically-installed-software"></a>Logiciels installés automatiquement

Le [script de déploiement du connecteur de données SAP](#deploy-your-sap-data-connector) installe les logiciels suivants sur votre machine virtuelle à l’aide de privilèges *sudo* (racine) :

- [Unzip](https://www.microsoft.com/en-us/p/unzip/9mt44rnlpxxt?activetab=pivot:overviewtab)
- [NetCat](https://sectools.org/tool/netcat/)
- [Python 3.6 ou version ultérieure](https://www.python.org/downloads/)
- [Python 3-pip](https://pypi.org/project/pip/)
- [Docker](https://www.docker.com/)

## <a name="configure-your-sap-system"></a>Configurer votre système SAP

Cette procédure décrit comment vérifier que votre système SAP présente la configuration requise et est configuré pour le déploiement du connecteur de données SAP Microsoft Sentinel.

> [!IMPORTANT]
> Suivez cette procédure avec votre équipe SAP pour vous assurer que les configurations sont correctes.
>

**Pour configurer votre système SAP pour le connecteur de données SAP** :

1. Assurez-vous que les notes SAP suivantes sont déployées dans votre système, conformément à votre version :

    | Versions SAP&nbsp;BASIS&nbsp; | Note requise |
    | --- | --- |
    | – 750 SP01 à SP12<br>– 751 SP01 à SP06<br>– 752 SP01 à SP03 | 2641084 : accès en lecture standardisé pour les données du journal d’audit de sécurité |
    | – 700 à 702<br>– 710 à 711, 730, 731, 740 et 750 | 2173545 : DM : CHANGEDOCUMENT_READ_ALL |
    | – 700 à 702<br>– 710 à 711, 730, 731 et 740<br>– 750 à 752 | 2502336 : DM (Document de modification) : RSSCD100, lecture uniquement à partir de l’archive, pas de la base de données |
    | | |

   Les versions ultérieures ne nécessitent pas de notes supplémentaires. Pour plus d’informations, consultez le site [SAP ONE Support Launchpad](https://support.sap.com/en/index.html). Connectez-vous avec un compte d’utilisateur SAP.

1. Téléchargez et installez l’une des demandes de modification de SAP suivantes à partir du [dépôt GitHub Microsoft Sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/SAP/CR) :

    - **SAP versions 750 ou ultérieures** : installez la demande de changement SAP *144 (NPLK900144)*
    - **SAP versions 740** : installez la demande de changement SAP *146 (NPLK900146)*

    Lorsque vous effectuez cette étape, assurez-vous d’utiliser le mode binaire pour transférer les fichiers vers le système SAP et d’utiliser le code de transaction SAP **STMS_IMPORT**.

    > [!NOTE]
    > Dans la zone **Options d’importation** de SAP, l’option **Ignorer la version du composant non valide** peut s’afficher. S’il est affiché, sélectionnez cette option avant de continuer.
    >

1. Créez un rôle SAP nommé **/MSFTSEN/SENTINEL_CONNECTOR** en important la demande de changement *14 (NPLK900163)* . Utilisez le code de transaction SAP **STMS_IMPORT**.

    Vérifiez que le rôle est créé avec les autorisations requises, par exemple :

    :::image type="content" source="media/sap/required-sap-role-authorizations.png" alt-text="Autorisations de rôle SAP requises pour le connecteur de données SAP Microsoft Sentinel.":::

    Pour plus d’informations, consultez les [autorisations pour utilisateur d’ABAP](sap-solution-detailed-requirements.md#required-abap-authorizations).

1. Créez un utilisateur de RFC/NetWeaver autre que par boîte de dialogue pour le connecteur de données SAP, et attachez le rôle nouvellement créé */MSFTSEN/SENTINEL_CONNECTOR*.

    - Après avoir attaché le rôle, vérifiez que les autorisations de rôle sont distribuées à l’utilisateur.
    - Ce processus requiert l’utilisation d’un nom d’utilisateur et d’un mot de passe pour l’utilisateur d’ABAP. Une fois le nouvel utilisateur créé et doté des autorisations requises, veillez à modifier le mot de passe de l’utilisateur d’ABAP.

1. Téléchargez et placez le kit de développement logiciel (SDK) RFC de SAP NetWeaver 7.50 pour Linux sur x86_64 version 64 bits sur votre machine virtuelle, car il est requis pendant le processus d’installation.

    Par exemple, recherchez le Kit de développement logiciel (SDK) sur le [site de téléchargement de logiciels SAP](https://launchpad.support.sap.com/#/softwarecenter/template/products/_APP=00200682500000001943&_EVENT=DISPHIER&HEADER=Y&FUNCTIONBAR=N&EVENT=TREE&NE=NAVIGATE&ENR=01200314690100002214&V=MAINT) > **SAP NW RFC SDK** > **SAP NW RFC SDK 7.50** > **nwrfc750X_X-xxxxxxx.zip**. Veillez à télécharger l’option **LINUX ON X86_64 65BIT**. Copiez le fichier, par exemple à l’aide du protocole SCP, sur votre machine virtuelle.

    Vous aurez besoin d’un compte d’utilisateur SAP pour accéder à la page de téléchargement de logiciels SAP.

1. (Facultatif) Le fichier SAP *Auditlog* est utilisé dans l’ensemble du système et prend en charge plusieurs clients SAP. Toutefois, chaque instance de la solution SAP Microsoft Sentinel ne prend en charge qu’un seul client SAP.

    Par conséquent, si vous avez un système SAP à plusieurs clients, nous vous recommandons d’activer le fichier *Auditlog* uniquement pour le client sur lequel vous déployez la solution SAP afin d’éviter la duplication de données.


## <a name="deploy-a-linux-vm-for-your-sap-data-connector"></a>Déployer une machine virtuelle Linux pour votre connecteur de données SAP

Cette procédure décrit comment utiliser Azure CLI pour déployer une machine virtuelle Ubuntu Server 18.04 LTS et lui attribuer une [identité gérée par le système](../active-directory/managed-identities-azure-resources/index.yml).

> [!TIP]
> Vous pouvez également déployer le connecteur de données sur RHEL version 7,7 ou ultérieure, ou sur SUSE version 15 ou ultérieure. Notez que le système d’exploitation et les correctifs doivent être complètement mis à jour.
>

Pour déployer et préparer votre machine virtuelle Ubuntu, procédez comme suit :

1. Utilisez la commande suivante à titre d’exemple. Veillez à insérer les valeurs de votre groupe de ressources et de votre nom de machine virtuelle.

    ```azurecli
    az vm create  --resource-group [resource group name]   --name [VM Name] --image UbuntuLTS  --admin-username azureuser --data-disk-sizes-gb 10 – --size Standard_DS2 --generate-ssh-keys  --assign-identity
    ```

1. Sur votre nouvelle machine virtuelle, installez :

    - [Venv](https://docs.python.org/3.8/library/venv.html) avec Python version 3.8 ou ultérieure.
    - [Azure CLI](/cli/azure/) version 2.8.0 ou ultérieure.

> [!IMPORTANT]
> Veillez à respecter les meilleures pratiques de sécurité de votre organisation, comme vous le feriez pour n’importe quelle autre machine virtuelle.
>

Pour plus d’informations, consultez [Démarrage rapide : Créer une machine virtuelle Linux avec Azure CLI](../virtual-machines/linux/quick-create-cli.md).

## <a name="create-a-key-vault-for-your-sap-credentials"></a>Créer un coffre de clés pour vos informations d’identification SAP

Dans ce didacticiel, vous utilisez un [Azure Key Vault](../key-vault/index.yml) nouvellement créé ou dédié pour stocker les informations d’identification de votre connecteur de données SAP.

Pour créer ou dédier un Azure Key Vault, effectuez les actions suivantes :

1. Créez un Azure Key Vault ou choisissez-en un à dédier à votre déploiement de connecteurs de données SAP.

    Par exemple, pour créer un coffre de clés, exécutez les commandes suivantes. Veillez à utiliser le nom de votre groupe de ressources de coffre de clés et entrez votre nom de coffre de clés.

    ```azurecli
    kvgp=<KVResourceGroup>

    kvname=<keyvaultname>

    #Create a key vault
    az keyvault create \
      --name $kvname \
      --resource-group $kvgp
    ```

1. Attribuez une stratégie d’accès, dont des autorisations GET, LIST et SET, à l’identité managée de la machine virtuelle en utilisant l’une des méthodes suivantes :

    - **Le portail Azure** :

        1. Dans votre Azure Key Vault, sélectionnez **Accéder aux stratégies** > **Ajouter une stratégie d’accès - Autorisations du secret : Get, List et Set** > **Sélectionner le principal**.  
        1. Entrez le [nom de votre machine virtuelle](#deploy-a-linux-vm-for-your-sap-data-connector), puis sélectionnez **Ajouter** > **Enregistrer**.

        Pour plus d’informations, consultez la [documentation relative à Key Vault](../key-vault/general/assign-access-policy-portal.md).

    - **Interface de ligne de commande Azure** :

        1. Exécutez la commande suivante pour récupérer [l’ID du principal de la machine virtuelle](#deploy-a-linux-vm-for-your-sap-data-connector). Veillez à entrer le nom de votre groupe de ressources Azure.  

            ```azurecli
            VMPrincipalID=$(az vm show -g [resource group] -n [Virtual Machine] --query identity.principalId -o tsv)
            ```  

            Votre ID principal est affiché. Vous allez l’utiliser à l’étape suivante.

        1. Exécutez la commande suivante pour affecter les autorisations d’accès à la machine virtuelle au coffre de clés. Veillez à entrer le nom de votre groupe de ressources et la valeur d’ID du principal qui a été retournée à l’étape précédente.

            ```azurecli
            az keyvault set-policy -n [key vault] -g [resource group] --object-id $VMPrincipalID --secret-permissions get list set
            ```

## <a name="deploy-your-sap-data-connector"></a>Déployer votre connecteur de données SAP

Le script de déploiement du connecteur de données SAP Microsoft Sentinel installe les [logiciels requis](#automatically-installed-software), puis le connecteur sur votre [machine virtuelle nouvellement créée](#deploy-a-linux-vm-for-your-sap-data-connector). Il stocke les informations d’identification dans votre [coffre de clés dédié](#create-a-key-vault-for-your-sap-credentials).

Le script de déploiement du connecteur de données SAP est stocké dans le [référentiel GitHub Microsoft Sentinel > DataConnectors > SAP](https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/sapcon-sentinel-kickstart.sh).

Pour exécuter le script de déploiement du connecteur de données SAP, vous avez besoin des éléments suivants :

- Les détails de votre espace de travail Microsoft Azure Sentinel, tels qu’indiqués dans la section [Prérequis](#prerequisites) ;
- Les détails du système SAP indiqués dans la section [Prérequis](#prerequisites) ;
- accès à un utilisateur de machine virtuelle disposant de privilèges sudo ;
- utilisateur SAP que vous avez créé dans la section [Configurer votre système SAP](#configure-your-sap-system), avec le rôle **/MSFTSEN/SENTINEL_CONNECTOR** appliqué ;
- aide de votre équipe SAP.

Pour exécuter le script de déploiement de la solution SAP, effectuez les étapes suivantes :

1. Exécutez la commande suivante pour déployer la solution SAP sur votre machine virtuelle :

    ```azurecli
    wget -O sapcon-sentinel-kickstart.sh https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/sapcon-sentinel-kickstart.sh && bash ./sapcon-sentinel-kickstart.sh
    ```

1. Suivez les instructions à l’écran pour entrer les détails de votre SAP et de votre coffre de clés, et accomplissez le déploiement. Une fois le déploiement terminé, un message de confirmation s’affiche :

    ```azurecli
    The process has been successfully completed, thank you!
    ```

    Microsoft Sentinel commence à récupérer les journaux SAP pour l’intervalle de temps configuré, jusqu’à 24 heures avant l’heure d’initialisation.

1. Nous vous recommandons de consulter les journaux système pour vous assurer que le connecteur de données transmet des données. Exécutez :

    ```bash
    docker logs -f sapcon-[SID]
    ```

## <a name="deploy-sap-security-content"></a>Déployer le contenu de sécurité SAP

Déployez le [contenu de sécurité SAP](sap-solution-security-content.md) à partir des zones **Solutions** et **Watchlists** de Microsoft Sentinel.

La solution **Microsoft Sentinel - Surveillance continue des menaces pour SAP** permet d’afficher le connecteur de données SAP dans la zone **Connecteurs de données** Microsoft Sentinel. La solution déploie également le classeur **SAP - Applications et produits système** et les règles d’analyse SAP associées.

Ajoutez manuellement les watchlists liées à SAP à votre espace de travail Microsoft Sentinel.

Pour déployer du contenu de sécurité de solution SAP, procédez comme suit :

1. Dans Microsoft Sentinel, dans le volet gauche, sélectionnez **Solutions (préversion)** .

    La page **Solutions** affiche une liste de solutions filtrée pouvant faire l’objet d’une recherche.

1. Sélectionnez **Microsoft Sentinel – Surveillance continue des menaces pour SAP (préversion)** pour ouvrir la page de la solution SAP.

    :::image type="content" source="media/sap/sap-solution.png" alt-text="Capture d’écran du volet solution « Microsoft Sentinel-Continuous Threat monitoring pour SAP (préversion) ».":::

1. Sélectionnez **Créer** pour lancer l’Assistant Déploiement de la solution, puis entrez les détails de l’abonnement Azure, du groupe de ressources et de l’espace de travail Log Analytics dans lequel vous souhaitez déployer la solution.

1. Sélectionnez **Suivant** pour parcourir les onglets **Connecteurs de données**, **Analytique** et **Classeurs**, où vous pouvez découvrir les composants qui seront déployés avec cette solution.

    Le nom par défaut du classeur est **SAP – Applications et produits système – Préversion**. Modifiez-le sous l’onglet Classeurs si nécessaire.

    Pour plus d’informations, consultez [Solution SAP Microsoft Sentinel : Informations de référence sur le contenu de sécurité (préversion publique)](sap-solution-security-content.md).

1. Dans le volet **Vérifier + créer**, attendez que le message **Validation réussie** s’affiche, puis sélectionnez **Créer** pour déployer la solution.

    > [!TIP]
    > Vous pouvez également sélectionner **Télécharger un modèle** afin d’obtenir un lien pour déployer la solution en tant que code.

1. Une fois le déploiement terminé, un message de confirmation s’affiche en haut à droite.

    Pour afficher le contenu nouvellement déployé, accédez à :

    - **Gestion des menaces** > **Classeurs** > **Mes classeurs**, pour trouver les [classeurs SAP intégrés](sap-solution-security-content.md#built-in-workbooks).
    - **Configuration** > **Analytique** pour rechercher une série de [règles d’analyse liées à SAP](sap-solution-security-content.md#built-in-analytics-rules).

1. Ajoutez des watchlists liées à SAP à utiliser dans votre recherche, vos règles de détection, votre chasse des menaces et vos playbooks de réponse. Ces watchlists fournissent la configuration de la solution de surveillance continue des menaces SAP Microsoft Sentinel. Effectuez les actions suivantes :

    a. Téléchargez les watchlists SAP à partir du dépôt GitHub Microsoft Sentinel à l’adresse https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/SAP/Analytics/Watchlists.  
    b. Dans la zone **Watchlists** de Microsoft Sentinel, ajoutez les watchlists à votre espace de travail Microsoft Sentinel. Utilisez les fichiers CSV téléchargés en tant que sources, puis personnalisez-les en fonction des besoins de votre environnement.  

    [ ![Watchlists liées à SAP ajoutées à Microsoft Sentinel.](media/sap/sap-watchlists.png) ](media/sap/sap-watchlists.png#lightbox)

    Pour plus d’informations, consultez [Utiliser les Watchlists Microsoft Sentinel](watchlists.md) et [Watchlists SAP disponibles](sap-solution-security-content.md#available-watchlists).

1. Dans Microsoft Sentinel, accédez au connecteur de données **Surveillance continue des menaces Microsoft Sentinel pour SAP** afin de vérifier la connexion :

    [ ![Capture d’écran de la page Microsoft Sentinel-Continuous Threat monitoring pour un connecteur de données SAP. ](media/sap/sap-data-connector.png) ](media/sap/sap-data-connector.png#lightbox)

    Les journaux SAP ABAP s’affichent dans la page **Journaux** de Microsoft Sentinel sous **Journaux personnalisés** :

    [ ![capture d’écran des journaux SAP ABAP dans la zone « journaux personnalisés » de Microsoft Sentinel.](media/sap/sap-logs-in-sentinel.png) ](media/sap/sap-logs-in-sentinel.png#lightbox)

    Pour plus d’informations, consultez [Informations de référence sur les journaux de la solution SAP Microsoft Sentinel (préversion publique)](sap-solution-log-reference.md).


## <a name="update-your-sap-data-connector"></a>Mettre à jour votre connecteur de données SAP

Si vous avez un conteneur Docker s’exécutant avec une version antérieure du connecteur de données SAP, exécutez le script de mise à jour du connecteur de données SAP pour obtenir les dernières fonctionnalités disponibles.

Assurez-vous que vous disposez des versions les plus récentes des scripts de déploiement appropriés du dépôt Github Microsoft Sentinel. 

Exécutez :

```azurecli
wget -O sapcon-instance-update.sh https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/sapcon-instance-update.sh && bash ./sapcon-instance-update.sh
```

Le conteneur Docker du connecteur de données SAP sur votre ordinateur est mis à jour. 

Veillez à rechercher d’autres mises à jour disponibles, telles que :

- Demandes de modification SAP pertinentes dans le [dépôt GitHub Microsoft Sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/SAP/CR).
- Contenu de sécurité SAP Microsoft Sentinel dans la solution **Supervision continue des menaces pour SAP Microsoft Sentinel**.
- Listes de surveillance pertinentes dans le [dépôt GitHub Microsoft Sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/SAP/Analytics/Watchlists).

## <a name="collect-sap-hana-audit-logs"></a>Collecter les journaux d’audit SAP HANA

Si les journaux d’audit de la base de données SAP HANA sont configurés avec Syslog, vous devrez également configurer votre agent Log Analytics pour collecter les fichiers Syslog.

1. Assurez-vous que le journal d’audit SAP HANA est configuré pour utiliser Syslog, comme décrit dans la *note SAP 0002624117*, accessible depuis le [site du support SAP](https://launchpad.support.sap.com/#/notes/0002624117). Pour plus d'informations, consultez les pages suivantes :

    - [Piste d’audit SAP HANA – Meilleures pratiques](https://archive.sap.com/documents/docs/DOC-51098)
    - [Recommandations pour l’audit](https://help.sap.com/viewer/742945a940f240f4a2a0e39f93d3e2d4/2.0.05/en-US/5c34ecd355e44aa9af3b3e6de4bbf5c1.html)

1. Vérifiez les fichiers Syslog de votre système d’exploitation à la recherche de tout événement pertinent de la base de données HANA.

1. Installez et configurez un agent Log Analytics sur votre ordinateur :

    a. Connectez-vous au système d’exploitation de votre base de données HANA en tant qu’utilisateur disposant des privilèges sudo.  
    b. Dans le portail Azure, accédez à votre espace de travail Log Analytics. Sur le volet gauche, sous **Paramètres**, sélectionnez **Gestion des agents** > **Serveurs Linux**.  
    c. Copiez le code affiché dans l’encadré sous **Télécharger et intégrer l’agent pour Linux** dans votre terminal, puis exécutez le script.

    L’agent Log Analytics est installé sur votre ordinateur et connecté à votre espace de travail. Pour plus d’informations, consultez [Installer l’agent Log Analytics sur des ordinateurs Linux](../azure-monitor/agents/agent-linux.md) et [Agent OMS pour Linux](https://github.com/microsoft/OMS-Agent-for-Linux) dans le référentiel GitHub de Microsoft.

1. Actualisez l’onglet **Gestion des agents > Serveurs Linux** pour confirmer que vous avez **1 ordinateur(s) Linux connecté(s)** .

1. Sur le volet gauche, sous **Paramètres**, sélectionnez **Configuration des agents** puis l’onglet **Syslog**.

1. Sélectionnez **Ajouter une installation** pour ajouter les installations dont vous souhaitez collecter les journaux. 

    > [!TIP]
    > Comme les installations dans lesquelles les événements de la base de données HANA sont enregistrés peuvent changer entre les différentes distributions, nous vous recommandons d’ajouter toutes les installations, de les vérifier par rapport à vos journaux Syslog, puis de supprimer celles qui ne sont pas pertinentes.
    >

1. Dans Microsoft Sentinel, vérifiez que les événements de la base de données HANA apparaissent désormais dans les journaux ingérés.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les solutions SAP Microsoft Sentinel :

- [Déployer le connecteur de données SAP Microsoft Sentinel avec SNC](sap-solution-deploy-snc.md)
- [Options de configuration pour experts, déploiement local et sources de journaux SAPControl](sap-solution-deploy-alternate.md)
- [Exigences de SAP détaillées pour la solution SAP Microsoft Sentinel](sap-solution-detailed-requirements.md)
- [Informations de référence sur les journaux de la solution SAP Microsoft Sentinel](sap-solution-log-reference.md)
- [Solution SAP Microsoft Sentinel : contenu de sécurité intégré](sap-solution-security-content.md)
- [Résolution des problèmes de déploiement de votre solution SAP Microsoft Sentinel](sap-deploy-troubleshoot.md)

Pour plus d’informations, consultez [Solutions Microsoft Sentinel](sentinel-solutions.md).
