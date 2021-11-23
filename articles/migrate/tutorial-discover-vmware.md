---
title: Découvrir les serveurs fonctionnant dans un environnement VMware avec l’outil de découverte et d’évaluation d’Azure Migrate
description: Apprenez à découvrir les applications, dépendances et serveurs locaux dans un environnement VMware en utilisant l’outil de découverte et d’évaluation d’Azure Migrate.
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 11/12/2021
ms.custom: mvc
ms.openlocfilehash: 7eb6aea743176bb4cdaf313d392320378bc016d1
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397494"
---
# <a name="tutorial-discover-servers-running-in-a-vmware-environment-with-azure-migrate"></a>Tutoriel : Découvrir les serveurs fonctionnant dans un environnement VMware avec Azure Migrate

Dans le cadre de votre migration vers Azure, vous devez découvrir vos charges de travail locales et réaliser votre inventaire.

Ce tutoriel vous montre comment découvrir les serveurs qui fonctionnent dans votre environnement VMware en utilisant l’outil Azure Migrate : découverte et évaluation, qui est une appliance Azure Migrate légère. Vous déployez l’appliance en tant que serveur s’exécutant dans votre instance vCenter Server afin de découvrir en permanence les serveurs et leurs métadonnées de performances, les applications qui s’exécutent sur les serveurs, les dépendances de serveur, les applications web ASP.NET et les bases de données et instances SQL Server.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Configurer un compte Azure
> * Préparer l’environnement VMware pour la découverte
> * Crée un projet.
> * Configurer l’appliance Azure Migrate
> * Démarrer la découverte en continu

> [!NOTE]
> Les tutoriels vous présentent le parcours le plus rapide pour tester un scénario. Ils utilisent les options par défaut quand cela est possible.  

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Avant de commencer ce tutoriel, vérifiez les prérequis suivants :

Condition requise | Détails
--- | ---
**vCenter Server/Hôte ESXi** | Vous avez besoin d’un serveur exécutant vCenter Server version 6.7, 6.5, 6.0 ou 5.5.<br /><br /> Les serveurs doivent être hébergés sur un hôte ESXi exécutant la version 5.5 ou une version ultérieure.<br /><br /> Dans vCenter Server, autorisez les connexions entrantes sur le port TCP 443 pour que l’appliance puisse collecter des métadonnées de configuration et de performances.<br /><br /> L’appliance se connecte à vCenter Server sur le port 443 par défaut. Si le serveur exécutant vCenter Server écoute un autre port, vous pouvez modifier le port quand vous fournissez à vCenter Server des détails dans le gestionnaire de configuration de l’appliance.<br /><br /> Sur les hôtes ESXi, assurez-vous que l’accès entrant est autorisé sur le port TCP 443 pour la découverte des applications installées et pour l’analyse des dépendances sans agent sur les serveurs.
**Appliance Azure Migrate** | vCenter Server doit pouvoir allouer les ressources suivantes à un serveur qui héberge l’appliance Azure Migrate :<br /><br /> - 32 Go de RAM, 8 processeurs virtuels et approximativement 80 Go de stockage sur disque.<br /><br /> - Un commutateur virtuel externe et un accès à Internet sur le serveur de l’appliance, directement ou via un proxy.
**Serveurs** | Toutes les versions de système d’exploitation Windows et Linux sont prises en charge pour la détection des métadonnées de configuration et de performances. <br /><br /> Pour la découverte des applications sur les serveurs, toutes les versions de système d’exploitation Windows et Linux sont prises en charge. Vérifiez les [versions de système d’exploitation prises en charge pour l’analyse de dépendance sans agent](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless).<br /><br /> Pour la découverte des applications installées et pour l’analyse des dépendances sans agent, VMware Tools (version 10.2.1 ou ultérieure) doit être installé et opérationnel sur les serveurs. PowerShell version 2.0 ou ultérieure doit être installé sur les serveurs Windows.<br /><br /> Pour découvrir les bases de données et instances SQL, consultez [les versions et les éditions des systèmes d’exploitation SQL Server et Windows prises en charge](migrate-support-matrix-vmware.md#sql-server-instance-and-database-discovery-requirements) et les mécanismes d’authentification Windows.<br /><br /> Pour découvrir les applications web ASP.NET exécutant le serveur web IIS, consultez les [versions du système d’exploitation Windows et d’IIS prises en charge](migrate-support-matrix-vmware.md#aspnet-web-apps-discovery-requirements).

## <a name="prepare-an-azure-user-account"></a>Préparer un compte de stockage Azure

Pour créer un projet et inscrire l’appliance Azure Migrate, vous devez avoir un compte Azure disposant des autorisations suivantes :

- Des autorisations de niveau Contributeur ou Propriétaire dans l’abonnement Azure
- Des autorisations permettant d’inscrire des applications Azure Active Directory (Azure AD)
- Des autorisations de niveau Propriétaire ou Contributeur et Administrateur de l’accès utilisateur dans l’abonnement Azure pour créer une instance d’Azure Key Vault, qui est utilisée pendant la migration du serveur sans agent

Si vous avez créé un compte Azure gratuit, vous êtes, par défaut, le propriétaire de l’abonnement Azure. Si vous n’êtes pas le propriétaire de l’abonnement, demandez au propriétaire de vous attribuer les autorisations.

Pour définir les autorisations de niveau Contributeur ou Propriétaire dans l’abonnement Azure :

1. Dans le portail Azure, recherchez « abonnements ». Sous **Services** dans les résultats de la recherche, sélectionnez **Abonnements**.

    :::image type="content" source="./media/tutorial-discover-vmware/search-subscription.png" alt-text="Capture d’écran montrant comment rechercher un abonnement Azure dans la zone de recherche.":::

1. Dans **Abonnements**, sélectionnez l’abonnement dans lequel vous souhaitez créer un projet.
1. Dans le menu de gauche, sélectionnez **Contrôle d’accès (IAM)** .
1. Sous l’onglet **Vérifier l’accès**, sous **Vérifier l’accès**, recherchez le compte d’utilisateur que vous souhaitez utiliser.
1. Dans le volet **Ajouter une attribution de rôle**, sélectionnez **Ajouter**.

    :::image type="content" source="./media/tutorial-discover-vmware/azure-account-access.png" alt-text="Capture d’écran montrant comment rechercher un compte d’utilisateur pour vérifier l’accès et ajouter une attribution de rôle.":::
    
1. Dans **Ajouter une attribution de rôle**, sélectionnez le rôle Contributeur ou Propriétaire, puis sélectionnez le compte. Sélectionnez **Enregistrer**.

    :::image type="content" source="./media/tutorial-discover-vmware/assign-role.png" alt-text="Capture d’écran montrant la page Ajouter une attribution de rôle permettant d’attribuer un rôle au compte.":::

Pour accorder au compte les autorisations nécessaires afin d’inscrire des applications Azure AD :

1. Dans le portail, accédez à **Azure Active Directory** > **Utilisateurs** > **Paramètres utilisateur**.
1. Dans **Paramètres utilisateur**, vérifiez que les utilisateurs Azure AD peuvent inscrire des applications (défini sur **Oui** par défaut).

    :::image type="content" source="./media/tutorial-discover-vmware/register-apps.png" alt-text="Capture d’écran montrant comment vérifier les paramètres utilisateur pour inscrire des applications.":::

9. Si **Inscriptions d’applications** est défini sur **Non**, demandez au locataire ou à l’administrateur général d’affecter les autorisations nécessaires. L’administrateur général ou le locataire peuvent également attribuer à un compte le rôle Développeur d’applications pour permettre l’inscription d’applications Azure AD par les utilisateurs. [Plus d’informations](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)

## <a name="prepare-vmware"></a>Préparer VMware

Sur vCenter Server, vérifiez que votre compte dispose des autorisations nécessaires pour créer une machine virtuelle avec un fichier d’installation de machine virtuelle OVA (Open Virtualization Appliance) VMware. Vous devez avoir ces autorisations quand vous déployez l’appliance Azure Migrate en tant que machine virtuelle VMware en utilisant un fichier OVA.

Azure Migrate doit avoir un compte vCenter Server en lecture seule pour découvrir et évaluer les serveurs fonctionnant dans votre environnement VMware. Si vous souhaitez également exécuter la découverte des applications installées et l’analyse des dépendances sans agent, le compte doit disposer des autorisations appropriées dans VMware pour les opérations de système invité VM.

### <a name="create-an-account-to-access-vcenter-server"></a>Créer un compte pour accéder à vCenter Server

Dans le client web VMware vSphere, configurez un compte en lecture seule à utiliser pour vCenter Server :

1. À partir d’un compte disposant de privilèges d’administrateur, dans le client web vSphere, dans le menu **Accueil**, sélectionnez **Administration**.
1. Sous **Authentification unique**, sélectionnez **Utilisateurs et groupes**.
1. Dans **Utilisateurs**, sélectionnez **Nouvel utilisateur**.
1. Entrez les détails du compte et sélectionnez **OK**.
1. Dans le menu sous **Administration**, sous **Contrôle d’accès**, sélectionnez **Autorisations globales**.
1. Sélectionnez le compte d’utilisateur, puis sélectionnez **Lecture seule** pour attribuer le rôle au compte. Sélectionnez **OK**.
1. Pour pouvoir démarrer la découverte des applications installées et l’analyse des dépendances sans agent, dans le menu sous **Contrôle d’accès**, sélectionnez **rôles**. Dans le volet **Rôles**, sous **Rôles**, sélectionnez **Lecture seule**. Sous **Privilèges**, sélectionnez **Opérations invité**. Pour propager les privilèges à tous les objets dans l’instance vCenter Server, cochez la case **Propager aux enfants**.

    :::image type="content" source="./media/tutorial-discover-vmware/guest-operations.png" alt-text="Capture d’écran montrant le client web vSphere et comment créer un compte et sélectionner des rôles d’utilisateur et des privilèges.":::

> [!NOTE]
> Vous pouvez définir l’étendue du compte vCenter Server pour limiter la découverte à des centres de données vCenter Server, des clusters, des hôtes, des dossiers de clusters ou d’hôtes ou des serveurs individuels spécifiques. Découvrez comment [définir l’étendue du compte d’utilisateur vCenter Server](set-discovery-scope.md).

> [!NOTE]
> Les ressources vCenter connectées via Linked-Mode au serveur vCenter spécifié pour la découverte ne seront pas découvertes par Azure Migrate. Une appliance Azure Migrate doit être déployée pour chaque environnement vCenter que vous souhaitez découvrir.

### <a name="create-an-account-to-access-servers"></a>Créer un compte pour accéder aux serveurs

Votre compte d’utilisateur sur vos serveurs doit disposer des autorisations nécessaires pour lancer la découverte des applications installées, l’analyse des dépendances sans agent et la découverte des applications web, et les bases de données et instances SQL Server. Vous pouvez fournir les informations du compte d’utilisateur dans le gestionnaire de configuration de l’appliance. L’appliance n’installe pas d’agents sur les serveurs.

* Pour la découverte des serveurs Windows et des applications web, créez un compte (local ou de domaine) doté d’autorisations d’administrateur sur les serveurs. Pour découvrir les bases de données et instances SQL, le compte Windows ou SQL Server doit être membre du rôle serveur sysadmin. Découvrez comment [attribuer le rôle requis au compte d’utilisateur](/sql/relational-databases/security/authentication-access/server-level-roles).
* Pour les serveurs Linux, fournissez les détails du compte d’utilisateur racine ou créez un compte disposant des autorisations CAP_DAC_READ_SEARCH et CAP_SYS_PTRACE sur les fichiers /bin/netstat et /bin/ls.

> [!NOTE]
> Vous pouvez ajouter diverses informations d’identification de serveur dans le gestionnaire de configuration de l’appliance Azure Migrate pour lancer une découverte des applications installées, une analyse des dépendances sans agent et une découverte des applications web et des bases de données et instances SQL Server. Vous pouvez ajouter diverses informations d’authentification de domaine, Windows (hors domaine), Linux (hors domaine) ou SQL Server. Découvrez comment [ajouter des informations d’identification de serveur](add-server-credentials.md).

## <a name="set-up-a-project"></a>Configuration d’un projet

Pour configurer un nouveau projet :

1. Dans le portail Azure, sélectionnez **Tous les services**, puis recherchez **Azure Migrate**.
1. Sous **Services**, sélectionnez **Azure Migrate**.
1. Dans **Vue d’ensemble**, sélectionnez l’une des options suivantes en fonction de vos objectifs de migration : **Serveurs, bases de données et applications web**, **SQL Server (uniquement)** ou **Explorez d’autres scénarios**.
1. Sélectionnez **Créer un projet**.
1. Dans **Créer un projet**, sélectionnez votre abonnement Azure et votre groupe de ressources. Créez un groupe de ressources si vous n’en avez pas.
1. Dans **Détails du projet**, spécifiez le nom du projet et la zone géographique où vous souhaitez créer le projet. Passez en revue les [zones géographiques prises en charge pour les clouds publics](migrate-support-matrix.md#supported-geographies-public-cloud) et les [zones géographiques prises en charge pour le secteur public](migrate-support-matrix.md#supported-geographies-azure-government).

    :::image type="content" source="./media/tutorial-discover-vmware/new-project.png" alt-text="Capture d’écran montrant comment ajouter les détails d’un nouveau projet Azure Migrate.":::

1. Sélectionnez **Create** (Créer).
1. Attendez quelques minutes, le temps nécessaire au déploiement du projet. L’outil **Azure Migrate : découverte et évaluation** est ajouté par défaut au nouveau projet.

> [!NOTE]
> Si vous avez déjà créé un projet, vous pouvez l’utiliser pour inscrire des appliances supplémentaires afin de découvrir et d’évaluer davantage de serveurs. Découvrez comment [gérer les projets](create-manage-projects.md#find-a-project).

## <a name="set-up-the-appliance"></a>Configurer l’appliance

L’outil Azure Migrate : découverte et évaluation utilise une appliance Azure Migrate légère. L’appliance exécute la découverte du serveur, puis envoie la configuration et les métadonnées de performances du serveur à Azure Migrate. Configurez l’appliance en déployant un modèle OVA pouvant être téléchargé à partir du projet.

> [!NOTE]
> Si vous ne pouvez pas configurer l’appliance en utilisant le modèle OVA, vous pouvez la configurer en exécutant un script PowerShell sur un serveur existant exécutant Windows Server 2016. Découvrez comment [utiliser PowerShell pour configurer une appliance Azure Migrate](deploy-appliance-script.md#set-up-the-appliance-for-vmware). <br/>
> L’option de déploiement d’une appliance à l’aide d’un modèle OVA n’est pas prise en charge dans le cloud Azure Government. [En savoir plus](./deploy-appliance-script-government.md) sur le déploiement d’une appliance pour le cloud Azure Government.

### <a name="deploy-by-using-an-ova-template"></a>Déployer à l’aide d’un modèle OVA

Pour configurer l’appliance en utilisant un modèle OVA, vous devez effectuer les étapes décrites plus en détail dans cette section :

1. Fournissez un nom d’appliance et générez une clé de projet sur le portail.
1. Téléchargez un fichier de modèle OVA, puis importez-le dans vCenter Server. Vérifiez que l’OVA est sécurisé.
1. Créez l’appliance à partir du fichier OVA. Vérifiez que l’appliance peut se connecter à Azure Migrate.
1. Configurez l’appliance pour la première fois. 
1. Inscrivez l’appliance auprès du projet avec la clé de projet.

#### <a name="generate-the-project-key"></a>Générer la clé de projet

1. Dans **Objectifs de migration**, sélectionnez **Serveurs, bases de données et applications web** > **Azure Migrate : découverte et évaluation** > **Découvrir**.
1. Dans **Découvrir des serveurs**, sélectionnez **Vos serveurs sont-ils virtualisés ?** > **Oui, avec l’hyperviseur vSphere VMware**.
1. Dans **1 : Générer une clé de projet**, attribuez un nom à l’appliance Azure Migrate que vous allez configurer pour la découverte des serveurs dans votre environnement VMware. Le nom doit être alphanumérique et comporter 14 caractères au maximum.
1. Pour commencer à créer les ressources Azure requises, sélectionnez **Générer la clé**. Ne fermez pas le volet **Découvrir** pendant la création des ressources.
1. Une fois les ressources Azure créées, une *clé de projet* est générée.
1. Copiez la clé. Vous utiliserez la clé pour inscrire l’appliance lors de sa configuration.

#### <a name="download-the-ova-template"></a>Télécharger le modèle OVA

Dans **2 : Télécharger l’appliance Azure Migrate**, sélectionnez le fichier .OVA, puis **Télécharger**.

##### <a name="verify-security"></a>Vérifier la sécurité

Avant de déployer le fichier OVA, vérifiez qu’il est sécurisé :

1. Sur le serveur sur lequel vous avez téléchargé le fichier, ouvrez une fenêtre d’invite de commandes en utilisant l’option **Exécuter en tant qu’administrateur**.
1. Exécutez la commande suivante pour générer le code de hachage du fichier OVA :
  
    ```bash
    C:\>CertUtil -HashFile <file_location> <hashing_agorithm>
    ```
   
    Exemple : `C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256`

1. Vérifiez les toutes dernières versions de l’appliance et les valeurs de hachage :

    - Pour le cloud public Azure :
    
        **Algorithme** | **Télécharger** | **SHA256**
        --- | --- | ---
        VMware (11,9 Go) | [Version la plus récente](https://go.microsoft.com/fwlink/?linkid=2140333) | 08e4fa3aa827a88b68d3b56306926773f75c3a8a47ba4296871cfcb6c9830ea8

    - Pour Azure Government :
    
        **Algorithme** | **Télécharger** | **SHA256**
        --- | --- | ---
        VMware (85,8 Mo) | [Version la plus récente](https://go.microsoft.com/fwlink/?linkid=2140337) | 3C00F9EB54CC6C55E127EDE47DFA28CCCF752697377EB1C9F3435E75DA5AA029

#### <a name="create-the-appliance-server"></a>Créer le serveur de l’appliance

Importez le fichier téléchargé, puis créez un serveur dans l’environnement VMware :

1. Dans la console du client vSphere, sélectionnez **File** (Fichier) > **Deploy OVF Template** (Déployer le modèle OVF).
1. Dans l’Assistant de déploiement du modèle OVF, sélectionnez **Source**, puis entrez l’emplacement du fichier OVA.
1. Dans **Nom**, entrez un nom pour le serveur. Dans **Emplacement**, sélectionnez l’objet d’inventaire dans lequel le serveur doit être hébergé.
1. Dans **Hôte/Cluster**, sélectionnez l’hôte ou le cluster sur lequel s’exécute le serveur.
1. Dans **Stockage**, sélectionnez la destination du stockage du serveur.
1. Dans **Format de disque**, sélectionnez le type de disque et la taille.
1. Dans **Mappage réseau**, sélectionnez le réseau auquel le serveur se connectera. Le réseau nécessite une connexion à Internet pour envoyer des métadonnées à Azure Migrate.
1. Validez les paramètres, puis sélectionnez **Finish (Terminer)** .

#### <a name="verify-appliance-access-to-azure"></a>Vérifier l’accès de l’appliance à Azure

Vérifiez que le serveur de l’appliance peut se connecter aux URL Azure pour les [clouds publics](migrate-appliance.md#public-cloud-urls) et ceux du [secteur public](migrate-appliance.md#government-cloud-urls).

### <a name="configure-the-appliance"></a>Configurer l’appliance

Pour configurer l’appliance pour la première fois :

> [!NOTE]
> Si vous configurez l’appliance avec un [script PowerShell](deploy-appliance-script.md) au lieu d’un modèle OVA téléchargé, vous pouvez ignorer les deux premières étapes.

1. Dans le client vSphere, cliquez avec le bouton droit sur le serveur, puis sélectionnez **Ouvrir la console**.
1. Sélectionnez ou entrez la langue, le fuseau horaire et le mot de passe pour l’appliance.
1. Ouvrez un navigateur sur n’importe quel ordinateur pouvant se connecter à l’appliance. Ensuite, ouvrez l’URL du gestionnaire de configuration de l’appliance : `https://appliance name or IP address: 44368`.

     Vous pouvez également ouvrir le gestionnaire de configuration à partir du bureau du serveur de l’appliance en sélectionnant le raccourci du gestionnaire de configuration.
1. Acceptez les termes du contrat de licence et lisez les informations relatives aux tiers.
1. Dans le gestionnaire de configuration, sélectionnez **Configurer les prérequis**, puis procédez comme suit :
    1. **Connectivité** : l’appliance vérifie que le serveur a accès à Internet. Si le serveur utilise un proxy :
        1. Sélectionnez **Configurer le proxy** pour spécifier l’adresse du proxy (sous la forme `http://ProxyIPAddress` ou `http://ProxyFQDN`, où *FQDN* fait référence à un *nom de domaine complet*) et le port d’écoute.
        1.  Entrez les informations d’identification si le proxy nécessite une authentification.
        1. Si vous avez ajouté les détails du proxy ou désactivé le proxy ou l’authentification, sélectionnez **Enregistrer** pour relancer la vérification de la connectivité.

            Seuls les proxys HTTP sont pris en charge.
    1. **Synchronisation de l’heure** : Vérifiez que l’heure de l’appliance est synchronisée avec l’heure Internet pour que la découverte fonctionne correctement.
    1. **Installer les mises à jour** : L’appliance vérifie que les dernières mises à jour sont installées. Une fois la vérification terminée, vous pouvez cliquer sur **Voir les services d’appliance** pour voir l’état et les versions des services s’exécutant sur le serveur de l’appliance.
    1. **Installer VDDK** : L’appliance vérifie que VDDK (VMware vSphere Virtual Disk Development Kit) est installé. Si le VDDK n’est pas installé, téléchargez VDDK 6.7 à partir de VMware. Extrayez le contenu du fichier zip téléchargé à l’emplacement spécifié sur l’appliance, comme indiqué dans les *instructions d’installation*.

        L’outil de migration de serveur Azure Migrate utilise VDDK pour répliquer les serveurs durant la migration vers Azure. 
1. Vous pouvez *réexécuter les prérequis* à tout moment pendant la configuration de l’appliance pour vérifier si celle-ci remplit les remplit tous :

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-prerequisites.png" alt-text="Capture d’écran montrant la configuration des prérequis dans le gestionnaire de configuration de l’appliance.":::

#### <a name="register-the-appliance-with-azure-migrate"></a>Inscrire l’appliance auprès d’Azure Migrate

1. Collez la clé de projet copiée à partir du portail. Si vous n’avez pas la clé, accédez à **Azure Migrate : découverte et évaluation** > **Découvrir** > **Gérer les appliances existantes**. Sélectionnez le nom d’appliance que vous avez fourni lors de la génération de la clé de projet, puis copiez la clé affichée.
1. Vous aurez besoin d’un code d’appareil pour vous authentifier auprès d’Azure. Sélectionnez **Connexion**. Cliquez sur **Continuer avec la connexion Azure**, sélectionnez **Copier le code et se connecter** pour copier le code de l’appareil et ouvrir une invite de connexion Azure dans un nouvel onglet de navigateur. Vérifiez que vous avez désactivé le bloqueur de fenêtres publicitaires dans le navigateur pour voir l’invite.

    :::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="Capture d’écran montrant où copier le code d’appareil et se connecter.":::

1. Dans un nouvel onglet de votre navigateur, collez le code de l’appareil, puis connectez-vous avec votre nom d’utilisateur et votre mot de passe Azure. La connexion avec un code PIN n’est pas prise en charge.

    Si vous avez fermé accidentellement l’onglet Connexion avant de vous être connecté, actualisez l’onglet du gestionnaire de configuration de l’appliance pour afficher le code de l’appareil et le bouton **Copier le code et se connecter**.
1. Une fois que vous êtes connecté, revenez à l’onglet de navigateur qui affiche le gestionnaire de configuration de l’appliance. Si le compte d’utilisateur Azure que vous avez utilisé pour vous connecter dispose des autorisations nécessaires pour les ressources Azure qui ont été créées lors de la génération de la clé, l’inscription de l’appliance démarre.
1. Une fois l’inscription de l’appliance terminée, consultez les détails de l’inscription en sélectionnant **Afficher les détails**.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-registration.png" alt-text="Capture d’écran de l’inscription avec le volet Azure Migrate montrant que l’appliance a été inscrite avec succès.":::

## <a name="start-continuous-discovery"></a>Démarrer la découverte en continu

Effectuez les étapes de configuration dans le gestionnaire de configuration d’appliance pour préparer et démarrer la découverte.

### <a name="provide-vcenter-server-details"></a>Fournir les détails de vCenter Server

L’appliance doit se connecter à vCenter Server pour découvrir les données de configuration et de performances des serveurs :

1. Dans **Étape 1 : Fournir les informations d’identification de vCenter Server**, sélectionnez **Ajouter des informations d’identification** afin d’entrer un nom pour les informations d’identification. Ajoutez le nom d’utilisateur et le mot de passe du compte vCenter Server que l’appliance utilisera pour découvrir les serveurs qui s’exécutent sur vCenter Server.
    - Vous devriez avoir configuré un compte avec les autorisations requises, comme décrit plus tôt dans cet article.
    - Si vous souhaitez étendre la découverte à des objets VMware spécifiques (centres de données vCenter Server, clusters, dossier de clusters, hôtes, dossier d’hôtes ou serveurs individuels), passez en revue les instructions pour [définir l’étendue de découverte](set-discovery-scope.md) afin de restreindre le compte utilisé par Azure Migrate.
1. Dans **Étape 2 : Fournir les détails de vCenter Server**, sélectionnez **Ajouter une source de découverte** pour sélectionner le nom des informations d’identification dans la liste déroulante. Sélectionnez l’adresse IP ou le nom de domaine complet (FQDN) du vCenter Server. Vous pouvez laisser le port par défaut (443) ou spécifier un port personnalisé sur lequel votre serveur vCenter Server est à l’écoute. Sélectionnez **Enregistrer**.
1. L’appliance tente de valider la connexion au serveur exécutant vCenter Server avec les informations d’identification. Elle affiche l’état de validation de l’adresse IP ou du nom de domaine complet vCenter Server dans la table des informations d’identification.
1. Vous pouvez *revalider* la connectivité à vCenter Server à tout moment avant de lancer la découverte.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="Capture d’écran montrant la gestion des informations d’identification et des sources de découverte pour vCenter Server dans le gestionnaire de configuration d’appliance.":::

### <a name="provide-server-credentials"></a>Fournir les informations d’identification du serveur

Dans **Étape 3 : Fournir des informations d’identification de serveur pour faire l’inventaire logiciel, l’analyse des dépendances sans agent, la découverte des instances et bases de données SQL Server, et la découverte d’applications web ASP.NET dans votre environnement VMware.** , vous pouvez fournir plusieurs informations d’identification de serveur. Si vous ne souhaitez pas utiliser ces fonctionnalités d’appliance, vous pouvez ignorer cette étape et procéder à la découverte de vCenter Server. Vous pouvez changer cette option à tout moment.

:::image type="content" source="./media/tutorial-discover-vmware/appliance-server-credentials-mapping.png" alt-text="Capture d’écran montrant comment fournir des informations d’identification pour l’inventaire logiciel, l’analyse des dépendances et la découverte de serveurs SQL.":::

Si vous souhaitez utiliser ces fonctionnalités, fournissez des informations d’identification de serveur en procédant comme suit. L’appliance tente de mapper automatiquement les informations d’identification aux serveurs pour exécuter les fonctionnalités de découverte.

Pour ajouter des informations d’identification de serveur :

1. Sélectionnez **Ajouter des informations d’identification**.
1. Dans le menu déroulant, sélectionnez **Type d’informations d’identification**.
    
    Vous pouvez fournir des informations d’authentification de domaine/Windows (hors domaine)/Linux (hors domaine)/ et SQL Server. Découvrez comment [fournir des informations d’identification](add-server-credentials.md) et comment nous les traitons.
1. Pour chaque type d’informations d’identification, entrez :
    * Un nom convivial.
    * Un nom d'utilisateur : username.
    * Un mot de passe.
    Sélectionnez **Enregistrer**.

    Si vous choisissez d’utiliser les informations d’identification du domaine, vous devez également entrer le nom de domaine complet du domaine. Celui-ci est requis pour valider l’authenticité des informations d’identification auprès de l’instance Active Directory de ce domaine.
1. Examinez les [autorisations requises](add-server-credentials.md#required-permissions) sur le compte pour la découverte des applications installées, l’analyse des dépendances sans agent ou la découverte des applications web et des bases de données et instances SQL Server.
1. Pour ajouter plusieurs informations d’identification à la fois, sélectionnez **Ajouter** afin d’enregistrer les informations d’identification et d’en ajouter d’autres.
    Quand vous sélectionnez **Enregistrer** ou **Ajouter**, l’appliance valide les informations d’identification de domaine auprès de l’instance Active Directory du domaine pour l’authentification. La validation est effectuée après chaque ajout afin d’éviter les verrouillages de compte, car l’appliance procède à une itération pour mapper les informations d’identification aux serveurs respectifs.

Pour vérifier la validation des informations d’identification de domaine :

Dans le gestionnaire de configuration, dans le tableau des informations d’identification, consultez l’**état de validation** des informations d’identification de domaine. Seules les informations d’identification de domaine seront validées.

Si la validation échoue, vous pouvez sélectionner un état d’**échec** pour voir l’erreur de validation. Corrigez le problème, puis sélectionnez **Revalider les informations d’identification** pour retenter la validation des informations d’identification.

:::image type="content" source="./media/tutorial-discover-vmware/add-server-credentials-multiple.png" alt-text="Capture d’écran illustrant la fourniture et la validation de plusieurs informations d’identification.":::

### <a name="start-discovery"></a>Démarrer la découverte

Pour démarrer la découverte de vCenter Server, sélectionnez **Démarrer la découverte**. Une fois la découverte lancée, vous pouvez vérifier son état par rapport à l’adresse IP ou au le nom de domaine complet de vCenter Server dans la table des sources.

## <a name="how-discovery-works"></a>Fonctionnement de la découverte

* Il faut environ 15 minutes par hôte pour que l’inventaire de serveurs découverts apparaisse sur le portail Azure.
* Si vous avez fourni des informations d’identification de serveur, l’inventaire logiciel (découverte des applications installées) est automatiquement lancé une fois la découverte des serveurs exécutant vCenter Server terminée. L’inventaire logiciel est effectué une fois toutes les 12 heures.
* L’[inventaire logiciel](how-to-discover-applications.md) identifie les instances SQL qui s’exécutent sur les serveurs. Avec ces informations collectées, l’appliance tente de se connecter aux diverses instances SQL grâce aux informations d’authentification Windows ou SQL Server fournies sur l’appliance. Ensuite, elle recueille des données sur les bases de données SQL Server et leurs propriétés. La découverte SQL Server est effectuée une fois toutes les 24 heures.
* L’appliance ne peut se connecter qu’aux instances SQL auxquelles elle est reliée par une ligne de mire réseau, alors que l’inventaire logiciel en lui-même n’a pas nécessairement besoin d’une ligne de mire réseau.
* La découverte des applications installées peut prendre plus de 15 minutes. Sa durée dépend du nombre de serveurs découverts. Pour 500 serveurs, environ une heure s’écoule avant que l’inventaire découvert apparaisse dans le projet Azure Migrate dans le portail.
* L’[inventaire logiciel](how-to-discover-applications.md) identifie le rôle de serveur web existant sur les serveurs découverts. Si le rôle de serveur web est activé pour un serveur, Azure Migrate procède à la découverte d’applications web sur le serveur. Les données de configuration des applications web sont mises à jour une fois par jour.
* Au cours de l’inventaire logiciel, les informations d’identification des serveurs ajoutées sont comparées aux serveurs et validées pour l’analyse des dépendances sans agent. Une fois la découverte des serveurs terminée, vous pouvez activer l’analyse des dépendances sans agent sur les serveurs. Seuls les serveurs pour lesquels la validation réussit peuvent être sélectionnés pour activer l’[analyse des dépendances sans agent](how-to-create-group-machine-dependencies-agentless.md).
* Les données des applications web ASP.NET et des bases de données et instances SQL Server commencent à apparaître dans le portail dans les 24 heures qui suivent le démarrage de la découverte.
* Par défaut, Azure Migrate utilise le moyen le plus sûr de se connecter aux instances SQL, c’est-à-dire qu’Azure Migrate chiffre la communication entre l’appliance Azure Migrate et les instances SQL sources en définissant la propriété TrustServerCertificate sur `true`. En outre, la couche transport utilise SSL pour chiffrer le canal et contourner la chaîne de certificat pour valider l’approbation. Par conséquent, le serveur d’appliance doit être configuré pour approuver l’autorité racine du certificat. Toutefois, vous pouvez modifier les paramètres de connexion en sélectionnant **Modifier les propriétés de connexion de SQL Server** sur l’appliance. [Plus d’informations](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine) pour comprendre ce que vous devez choisir.

    :::image type="content" source="./media/tutorial-discover-vmware/sql-connection-properties.png" alt-text="Capture d’écran montrant comment modifier les propriétés de connexion de SQL Server.":::

Pour démarrer la découverte de vCenter Server, sélectionnez **Démarrer la découverte**. Une fois la découverte lancée, vous pouvez vérifier son état par rapport à l’adresse IP ou au le nom de domaine complet de vCenter Server dans la table des sources.

### <a name="view-discovered-data"></a>Afficher les données découvertes

1. Retournez à Azure Migrate dans le portail Azure.
1. Sélectionnez **Actualiser** pour afficher les données découvertes.

   :::image type="content" source="./media/tutorial-discover-vmware/discovery-assessment-tile.png" alt-text="Capture d’écran montrant comment actualiser les données dans la vignette de découverte et d’évaluation.":::

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [évaluer les serveurs à migrer vers des machines virtuelles Azure](./tutorial-assess-vmware-azure-vm.md).
- Découvrez comment [évaluer les serveurs exécutant SQL Server à migrer vers Azure SQL](./tutorial-assess-sql.md).
- Découvrez comment [évaluer les applications web à migrer vers Azure App Service](./tutorial-assess-webapps.md).
- Passez en revue les [données que l’appliance Azure Migrate collecte](migrate-appliance.md#collected-data---vmware) pendant la découverte.
