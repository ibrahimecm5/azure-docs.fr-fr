---
title: Découvrir des serveurs sur Hyper-V avec l’outil de découverte et d’évaluation d’Azure Migrate
description: Apprenez à découvrir les serveurs locaux sur Hyper-V avec l’outil de découverte et d’évaluation d’Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 11/12/2021
ms.custom: mvc
ms.openlocfilehash: 1c9e0c5a6a0672ec36bfef1d2f8d909b0ddea984
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397677"
---
# <a name="tutorial-discover-servers-running-on-hyper-v-with-azure-migrate-discovery-and-assessment"></a>Tutoriel : Découvrir les serveurs fonctionnant sur Hyper-V avec l’outil de découverte et d’évaluation d’Azure Migrate

Dans le cadre de votre migration vers Azure, vous devez découvrir vos charges de travail locales et réaliser votre inventaire.

Ce tutoriel vous montre comment découvrir des serveurs locaux sur des hôtes Hyper-V avec l’outil Azure Migrate : découverte et évaluation, en utilisant une appliance Azure Migrate légère. Vous allez déployer l’appliance en tant que serveur sur un hôte Hyper-V afin de découvrir de façon continue les métadonnées relatives aux machines et aux performances.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Configurer un compte Azure
> * Préparer l’environnement Hyper-V pour la découverte
> * Crée un projet.
> * Configurer l’appliance Azure Migrate
> * Démarrer la découverte en continu

> [!NOTE]
> Les tutoriels enseignent le moyen le plus rapide de tester un scénario et d’utiliser les options par défaut.  

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Avant de commencer ce tutoriel, vérifiez les prérequis.

**Prérequis** | **Détails**
--- | ---
**Hôte Hyper-V** | Les hôtes Hyper-V sur lesquels les serveurs sont situés peuvent être autonomes ou appartenir à un cluster.<br/><br/> L’hôte doit exécuter Windows Server 2019, Windows Server 2016 ou Windows Server 2012 R2.<br/><br/> Vérifiez que les connexions entrantes sont autorisées sur le port 5985 WinRM (HTTP), afin de permettre à l’appliance de se connecter en vue de tirer (pull) les données de performances et les métadonnées de serveurs avec une session CIM (Common Information Model).
**Étapes de déploiement d’appliance** | L’hôte Hyper-V doit allouer des ressources à un serveur pour l’appliance :<br/><br/> - 16 Go de RAM, 8 processeurs virtuels et environ 80 Go de stockage sur disque.<br/><br/> - Un commutateur virtuel externe et un accès à Internet sur l’appliance, directement ou via un proxy.
**Serveurs** | Les serveurs peuvent exécuter le système d’exploitation Windows ou Linux.

## <a name="prepare-an-azure-user-account"></a>Préparer un compte de stockage Azure

Pour créer un projet et inscrire l’appliance Azure Migrate, vous avez besoin d’un compte avec :
- Des autorisations de niveau Contributeur ou Propriétaire sur un abonnement Azure
- Des autorisations permettant d’inscrire des applications Azure Active Directory (AAD).

Si vous venez de créer un compte Azure gratuit, vous êtes le propriétaire de votre abonnement. Si vous n’êtes pas le propriétaire de l’abonnement, demandez à celui-ci de vous attribuer les autorisations de la façon suivante :

1. Dans le portail Azure, recherchez « abonnements », puis sous **Services**, sélectionnez **Abonnements**.

    ![Zone de recherche pour rechercher l’abonnement Azure](./media/tutorial-discover-hyper-v/search-subscription.png)

2. Dans la page **Abonnements**, sélectionnez l’abonnement dans lequel vous souhaitez créer un projet.
3. Dans l’abonnement, sélectionnez **Contrôle d’accès (IAM)**  > **Vérifier l’accès**.
4. Dans **Vérifier l’accès**, recherchez le compte d’utilisateur correspondant.
5. Dans **Ajouter une attribution de rôle**, cliquez sur **Ajouter**.

    ![Rechercher un compte d’utilisateur pour vérifier l’accès et attribuer un rôle](./media/tutorial-discover-hyper-v/azure-account-access.png)

6. Dans **Ajouter une attribution de rôle**, sélectionnez le rôle Contributeur ou Propriétaire, puis sélectionnez le compte (azmigrateuser dans notre exemple). Ensuite, cliquez sur **Enregistrer**.

    ![Ouvre la page Ajouter une attribution de rôle pour attribuer un rôle au compte.](./media/tutorial-discover-hyper-v/assign-role.png)

1. Pour inscrire l’appliance, votre compte Azure doit disposer d’**autorisations pour inscrire des applications AAD**.
1. Dans le portail Azure, accédez à **Azure Active Directory** > **Utilisateurs** > **Paramètres utilisateur**.
1. Dans **Paramètres utilisateur**, vérifiez que les utilisateurs Azure AD peuvent inscrire des applications (défini sur **Oui** par défaut).

    ![Vérifier dans les paramètres utilisateur que les utilisateurs peuvent inscrire des applications Active Directory](./media/tutorial-discover-hyper-v/register-apps.png)

9. Si les paramètres « Inscriptions d’applications » ont la valeur « Non », demandez au locataire ou à l’administrateur général d’affecter l’autorisation nécessaire. L’administrateur général/le locataire peuvent également attribuer le rôle **Développeur d’applications** à un compte pour permettre l’inscription d’une application AAD. [Plus d’informations](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)

## <a name="prepare-hyper-v-hosts"></a>Préparer les hôtes Hyper-V

Vous pouvez préparer les hôtes Hyper-V manuellement ou à l’aide d’un script. Les étapes de préparation sont résumées dans le tableau. Le script les prépare automatiquement.

**Étape** | **Script** | **Manuel**
--- | --- | ---
Vérifier la configuration requise pour l’hôte | Vérifie que l’hôte exécute une version prise en charge d’Hyper-V et le rôle Hyper-V.<br/><br/>Active le service WinRM et ouvre les ports 5985 (HTTP) et 5986 (HTTPs) sur l’ordinateur hôte (nécessaire pour la collecte de métadonnées). | L’hôte doit exécuter Windows Server 2019, Windows Server 2016 ou Windows Server 2012 R2.<br/><br/> Vérifiez que les connexions entrantes sont autorisées sur le port 5985 WinRM (HTTP), afin de permettre à l’appliance de se connecter en vue de tirer (pull) les données de performances et les métadonnées de serveurs avec une session CIM (Common Information Model).<br/><br/> Le script n’est pas pris en charge sur les hôtes avec des paramètres régionaux non anglais.  
Vérifier la version de PowerShell | Vérifie que vous exécutez le script sur une version de PowerShell prise en charge. | Vérifiez que vous exécutez PowerShell version 4.0 ou ultérieure sur l’hôte Hyper-V.
Créer un compte | Vérifie que vous disposez des autorisations appropriées sur l’hôte Hyper-V.<br/><br/> Permet de créer un compte d’utilisateur local avec les autorisations appropriées. | Option 1 : Préparez un compte disposant d’un accès Administrateur sur l’hôte Hyper-V.<br/><br/> Option n°2 : Préparez un compte d’administrateur local ou un compte d’administrateur de domaine, puis ajoutez le compte à ces groupes : Utilisateurs de gestion à distance, Administrateurs Hyper-V et Utilisateurs de l’Analyseur de performances.
Activer la communication à distance de PowerShell | Active la communication à distance de PowerShell sur l’hôte, ce qui permet à l’appliance Azure Migrate d’exécuter des commandes PowerShell sur l’hôte, par le biais d’une connexion WinRM. | Pour la configurer, sur chaque hôte, ouvrez une console PowerShell en tant qu’administrateur, puis exécutez cette commande : ``` powershell Enable-PSRemoting -force ```
Configurer Hyper-V Integration Services | Vérifie que les services d’intégration Hyper-V sont activés sur tous les serveurs gérés par l’hôte. | [Activez Hyper-V Integration Services](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) sur chaque serveur.<br/><br/> Si vous utilisez Windows Server 2003, [suivez ces instructions](prepare-windows-server-2003-migration.md).
Déléguer les informations d’identification si les disques de serveur se trouvent sur des partages SMB distants | Délègue les informations d’identification. | Exécutez cette commande pour permettre à CredSSP de déléguer les informations d’identification sur des hôtes exécutant des serveurs avec des disques sur des partages SMB : ```powershell Enable-WSManCredSSP -Role Server -Force ```<br/><br/> Vous pouvez exécuter cette commande à distance sur tous les hôtes Hyper-V.<br/><br/> Si vous ajoutez de nouveaux nœuds hôtes sur un cluster, ils sont automatiquement ajoutés pour la découverte, mais vous devez activer manuellement CredSSP.<br/><br/> Quand vous configurez l’appliance, vous terminez la configuration de CredSSP en [l’activant sur l’appliance](#delegate-credentials-for-smb-vhds). 

### <a name="run-the-script"></a>Exécuter le script

1. Téléchargez le script à partir du [Centre de téléchargement Microsoft](https://aka.ms/migrate/script/hyperv). Le script est signé par chiffrement par Microsoft.
2. Validez l’intégrité du script avec le fichier de hachage SHA256. La valeur de code de hachage est indiquée ci-dessous. Pour générer le hachage pour le script, exécutez la commande suivante :

    ```powershell
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    Exemple d’utilisation :

    ```powershell
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1 SHA256
    ```
3. Après avoir validé l’intégrité du script, exécutez ce dernier sur chaque hôte Hyper-V à l’aide de la commande PowerShell suivante avec des autorisations élevées :

    ```powershell
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```
La valeur de hachage est :

**Code de hachage** |  **Valeur**
--- | ---
SHA256 | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2

## <a name="set-up-a-project"></a>Configuration d’un projet

Configurez un nouveau projet.

1. Dans le portail Azure, sélectionnez **Tous les services**, puis recherchez **Azure Migrate**.
2. Sous **Services**, sélectionnez **Azure Migrate**.
3. Dans **Vue d’ensemble**, sélectionnez **Créer un projet**.
5. Dans **Créer un projet**, sélectionnez votre abonnement Azure et votre groupe de ressources. Créez un groupe de ressources si vous n’en avez pas.
6. Dans **Détails du projet**, spécifiez le nom du projet ainsi que la zone géographique où vous souhaitez le créer. Passez en revue les zones géographiques prises en charge pour les clouds [publics](migrate-support-matrix.md#supported-geographies-public-cloud) et du [secteur public](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Zones pour le nom et la région du projet](./media/tutorial-discover-hyper-v/new-project.png)

7. Sélectionnez **Create** (Créer).
8. Attendez quelques minutes, le temps nécessaire au déploiement du projet. L’outil **Azure Migrate : découverte et évaluation** est ajouté par défaut au nouveau projet.

![Page montrant l’outil Azure Migrate : découverte et évaluation ajouté par défaut](./media/tutorial-discover-hyper-v/added-tool.png)

> [!NOTE]
> Si vous avez déjà créé un projet, vous pouvez utiliser le même projet pour inscrire des appliances supplémentaires afin de découvrir et d’évaluer un plus grand nombre de serveurs.[En savoir plus](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>Configurer l’appliance

Azure Migrate utilise une appliance Azure Migrate légère. L’appliance effectue la détection du serveur, puis envoie la configuration et les métadonnées de performances du serveur à Azure Migrate. L’appliance peut être configurée en déployant un fichier VHD pouvant être téléchargé à partir du projet.

> [!NOTE]
> Si, pour une raison quelconque, vous ne pouvez pas configurer l’appliance à l’aide du modèle, vous pouvez la configurer à l’aide d’un script PowerShell sur un serveur Windows Server 2016 existant. [Plus d’informations](deploy-appliance-script.md#set-up-the-appliance-for-hyper-v)<br/>
> L’option de déploiement d’une appliance à l’aide d’un modèle VHD n’est pas prise en charge dans le cloud Azure Government. [En savoir plus](./deploy-appliance-script-government.md) sur le déploiement d’une appliance pour le cloud Azure Government.

Ce tutoriel configure l’appliance sur un serveur s’exécutant dans l’environnement Hyper-V, de la façon suivante :

1. Fournissez un nom d’appliance et générez une clé de projet sur le portail.
1. Téléchargez un disque dur virtuel Hyper-V compressé à partir du portail Azure.
1. Créez l’appliance et vérifiez qu’elle peut se connecter à Azure Migrate : découverte et évaluation.
1. Configurez l’appliance pour la première fois, puis inscrivez-la auprès du projet en utilisant la clé de projet.

### <a name="1-generate-the-project-key"></a>1. Générer la clé de projet

1. Dans **Objectifs de migration** > **Serveurs** > **Azure Migrate : découverte et évaluation**, sélectionnez **Découvrir**.
2. Dans **Découvrir les serveurs** > **Vos serveurs sont-ils virtualisés ?** , sélectionnez **Oui, avec Hyper-V**.
3. Dans **1 : Générer une clé de projet**, attribuez un nom à l’appliance Azure Migrate que vous allez configurer pour la découverte des serveurs. Le nom doit être alphanumérique et comporter 14 caractères au maximum.
1. Cliquez sur **Générer une clé** pour lancer la création des ressources Azure nécessaires. Ne fermez pas la page Découvrir les serveurs pendant la création des ressources.
1. Une fois les ressources Azure créées, une **clé de projet** est générée.
1. Copiez la clé car vous en aurez besoin pour terminer l'inscription de l'appliance lors de sa configuration.

### <a name="2-download-the-vhd"></a>2. Télécharger le disque dur virtuel

Dans **2 : Télécharger l'appliance Azure Migrate**, sélectionnez le fichier .VHD et cliquez sur **Télécharger**.

### <a name="verify-security"></a>Vérifier la sécurité

Vérifiez que le fichier compressé est sécurisé avant de le déployer.

1. Sur l’ordinateur où vous avez téléchargé le fichier, ouvrez une fenêtre de commande d’administrateur.

2. Exécutez la commande PowerShell suivante pour générer le code de hachage du fichier ZIP
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - Exemple d’utilisation : ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v3.20.09.25.zip -Algorithm SHA256```

3.  Vérifiez les toutes dernières versions de l’appliance et les valeurs de hachage :

    - Pour le cloud public Azure :

        **Scénario** | **Télécharger** | **SHA256**
        --- | --- | ---
        Hyper-V (8,91 Go) | [Version la plus récente](https://go.microsoft.com/fwlink/?linkid=2140422) |  91A435962F0DC9C7032DE0288D578171A76CC07563AC543C5E1EB25CD31BB231

    - Pour Azure Government :

        **Scénario** _ | _ *Télécharger** | **SHA256**
        --- | --- | ---
        Hyper-V (85,8 Mo) | [Version la plus récente](https://go.microsoft.com/fwlink/?linkid=2140424) |  3C00F9EB54CC6C55E127EDE47DFA28CCCF752697377EB1C9F3435E75DA5AA029

### <a name="3-create-an-appliance"></a>3. Créer une appliance

Importez le fichier téléchargé, puis créez une appliance.

1. Extrayez le fichier de disque dur virtuel compressé dans un dossier sur l’hôte Hyper-V qui hébergera l’appliance. Trois dossiers sont extraits.
2. Ouvrez le Gestionnaire Hyper-V. Dans **Actions**, cliquez sur **Importer la machine virtuelle**.
2. Dans l’Assistant Importation de machine virtuelle > **Avant de commencer**, cliquez sur **Suivant**.
3. Dans **Localiser le dossier**, spécifiez le dossier contenant le disque dur virtuel extrait. Cliquez ensuite sur **Suivant**.
1. Dans **Sélectionner une machine virtuelle**, cliquez sur **Suivant**.
2. Dans **Choisir le type d’importation**, cliquez sur **Copier la machine virtuelle (créer un identifiant unique)** . Cliquez ensuite sur **Suivant**.
3. Dans **Choisir la destination**, laissez la valeur par défaut. Cliquez sur **Suivant**.
4. Dans **Dossiers de stockage**, laissez la valeur par défaut. Cliquez sur **Suivant**.
5. Dans **Choisir un réseau**, spécifiez le commutateur virtuel qui sera utilisé par l’appliance. Le commutateur nécessite une connexion à Internet pour envoyer des données à Azure.
6. Dans **Récapitulatif**, passez en revue les paramètres. Puis, cliquez sur **Terminer**.
7. Dans Gestionnaire Hyper-V > **Machines virtuelles**, démarrez l’appliance.

### <a name="verify-appliance-access-to-azure"></a>Vérifier l’accès de l’appliance à Azure

Vérifiez que l’appliance peut se connecter aux URL Azure pour les clouds [publics](migrate-appliance.md#public-cloud-urls) et du [secteur public](migrate-appliance.md#government-cloud-urls).

### <a name="4-configure-the-appliance"></a>4. Configurer l’appliance

Configurez l’appliance pour la première fois.

> [!NOTE]
> Si vous configurez l’appliance à l’aide d’un [script PowerShell](deploy-appliance-script.md) au lieu du disque dur virtuel téléchargé, ne tenez pas compte des deux premières étapes de cette procédure.

1. Dans Gestionnaire Hyper-V> **Machines virtuelles**, cliquez avec le bouton droit sur l’appliance > **Se connecter**.
2. Spécifiez la langue, le fuseau horaire et le mot de passe pour l’appliance.
3. Ouvrez un navigateur sur une machine qui peut se connecter à l’appliance, puis ouvrez l’URL de l’application web de l’appliance : **https://*nom ou adresse IP de l’appliance* : 44368**.

   Vous pouvez aussi ouvrir l’application à partir du Bureau de l’appliance en cliquant sur le raccourci de l’application.
1. Acceptez les **termes du contrat de licence** et lisez les informations relatives aux tiers.
1. Dans l’application web > **Configurer les prérequis**, procédez comme suit :
    - **Connectivité** : L’application vérifie que l’appliance a accès à Internet. Si l’appliance utilise un proxy :
      - Cliquez sur **Configurer le proxy**, puis spécifiez l’adresse du proxy (sous la forme http://ProxyIPAddress ou http://ProxyFQDN) et le port d’écoute.
      - Spécifiez les informations d’identification si le proxy nécessite une authentification.
      - Seuls les proxys HTTP sont pris en charge.
      - Si vous avez ajouté des détails de proxy ou désactivé le proxy et/ou l’authentification, cliquez sur **Enregistrer** pour relancer la vérification de la connectivité.
    - **Synchronisation de l’heure** : L’heure est vérifiée. L’heure de l’appliance doit être synchronisée avec l’heure Internet pour que la découverte de serveur fonctionne correctement.
    - **Installer les mises à jour** : Azure Migrate: Discovery and assessment vérifie que les dernières mises à jour sont installées sur l’appliance. Une fois la vérification terminée, vous pouvez cliquer sur **Afficher les services de l’appliance** pour voir l’état et les versions des composants s’exécutant sur l’appliance.

### <a name="register-the-appliance-with-azure-migrate"></a>Inscrire l’appliance auprès d’Azure Migrate

1. Collez la **clé de projet** copiée à partir du portail. Si vous n’avez pas la clé, accédez à **Azure Migrate : Discovery and Assessment > Découvrir > Gérer les appliances existantes**, sélectionnez le nom d’appliance que vous avez indiqué au moment de générer la clé, puis copiez la clé correspondante.
1. Vous aurez besoin d’un code d’appareil pour vous authentifier auprès d’Azure. Le fait de cliquer sur **Connexion** ouvre une boîte de dialogue modale comprenant le code de l’appareil, comme celle affichée ci-dessous.

    ![Boîte de dialogue modale indiquant le code de l’appareil](./media/tutorial-discover-vmware/device-code.png)

1. Cliquez sur **Copier le code et se connecter** pour copier le code de l’appareil et ouvrir une invite de connexion Azure dans un nouvel onglet de navigateur. S’il n’apparaît pas, vérifiez que vous avez désactivé le bloqueur de fenêtres publicitaires dans le navigateur.
1. Sous le nouvel onglet, collez le code de l’appareil, puis connectez-vous avec votre nom d’utilisateur et votre mot de passe Azure.
   
   La connexion avec un code PIN n’est pas prise en charge.
3. Si vous avez fermé accidentellement l’onglet Connexion avant de vous être connecté, vous devrez actualiser l’onglet Appliance Configuration Manager pour réactiver le bouton de connexion.
1. Une fois connecté, revenez à l’onglet précédent, c’est-à-dire, l’onglet Appliance Configuration Manager.
4. Si le compte d’utilisateur Azure utilisé pour la connexion dispose des autorisations adéquates sur les ressources Azure créées au moment de la génération de la clé, l’inscription de l’appliance est lancée.
1. Une fois l'appliance inscrite, vous pouvez consulter les détails de l'inscription en cliquant sur **Afficher les détails**.

### <a name="delegate-credentials-for-smb-vhds"></a>Déléguer des informations d’identification pour les disques durs virtuels sur SMB

Si vous utilisez des disques durs virtuels sur des SMB, vous devez activer la délégation des informations d’identification de l’appliance aux hôtes Hyper-V. Pour effectuer cette opération à partir de l’appliance :

1. Sur l’appliance, exécutez cette commande. HyperVHost1/HyperVHost2 sont des exemples de noms d’hôte.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
    ```

2. Vous pouvez également effectuer cette opération dans l’éditeur d’objets de stratégie de groupe sur l’appliance :
    - Dans **Stratégie de l’ordinateur local** > **Configuration de l’ordinateur**, cliquez sur **Modèles d’administration** > **Système** > **Délégation d’informations d’identification**.
    - Double-cliquez sur **Autoriser la délégation de nouvelles informations d’identification**, puis sélectionnez **Activé**.
    - Dans **Options**, cliquez sur **Afficher**, puis ajoutez chaque hôte Hyper-V que vous voulez découvrir à la liste, avec **wsman/** comme préfixe.
    - Dans **Délégation d’informations d’identification**, double-cliquez sur **Autoriser la délégation de nouvelles informations d’identification avec l’authentification de serveur NTLM uniquement**. À nouveau, ajoutez chaque hôte Hyper-V que vous voulez découvrir à la liste, avec **wsman/** comme préfixe.

## <a name="start-continuous-discovery"></a>Démarrer la découverte en continu

Connectez-vous de l’appliance à des hôtes ou des clusters Hyper-V, et démarrez la découverte des serveurs.

1. À l’**Étape 1 : Fournir les informations d’identification de l’hôte Hyper-V**, cliquez sur **Ajouter les informations d’identification** afin de spécifier un nom convivial pour les informations d’identification, puis ajoutez un **Nom d’utilisateur** et un **Mot de passe** pour l’hôte/le cluster Hyper-V que l’appliance utilisera afin de découvrir les serveurs. Cliquez sur **Enregistrer**.
1. Si vous souhaitez ajouter plusieurs informations d’identification à la fois, cliquez sur **Ajouter** pour enregistrer et ajouter d’autres informations d'identification. Plusieurs informations d’identification sont prises en charge pour la découverte de serveurs dans un environnement Hyper-V.
1. À l’**Étape 2 : Fournir les détails de l'hôte/du cluster Hyper-V**, cliquez sur **Ajouter une source de détection** afin de spécifier l'**adresse IP/nom de domaine complet** de l'hôte/du cluster Hyper-V ainsi que le nom convivial des informations d'identification à utiliser pour la connexion à l'hôte/au cluster.
1. Vous pouvez soit **Ajouter un seul élément**  à la fois, soit **Ajouter plusieurs éléments**  en une seule fois. Une option permet également de connaître les détails de l'hôte/du cluster Hyper-V via **Importer CSV**.

    - Si vous choisissez **Ajouter un seul élément**, vous devez spécifier un nom convivial pour les informations d'identification ainsi que l'**adresse IP/nom de domaine complet** de l'hôte/du cluster Hyper-V, puis cliquer sur **Enregistrer**.
    - Si vous choisissez **Ajouter plusieurs éléments** _(sélectionné par défaut)_ , vous pouvez ajouter plusieurs enregistrements à la fois en spécifiant l’**adresse IP/nom de domaine complet** de l’hôte/du cluster Hyper-V avec le nom convivial des informations d’identification dans la zone de texte. **Vérifiez** les enregistrements ajoutés, puis cliquez sur **Enregistrer**.
    - Si vous choisissez **Importer CSV**, vous pouvez télécharger un fichier de modèle CSV, puis renseigner le fichier avec l'**adresse IP/nom de domaine complet** de l'hôte/du cluster Hyper-V ainsi qu'un nom convivial pour les informations d'identification. Importez ensuite le fichier dans l'appliance, **vérifiez** les enregistrements dans le fichier, puis cliquez sur **Enregistrer**.

1. Dès que vous cliquerez sur Enregistrer, l'appliance essaiera de valider la connexion aux hôtes/clusters Hyper-V ajoutés et affichera l'**État de validation** dans le tableau pour chaque hôte/cluster.
    - Pour les hôtes/clusters validés avec succès, vous pouvez afficher plus de détails en cliquant sur leur adresse IP/nom de domaine complet.
    - Si la validation d'un hôte échoue, examinez l'erreur en cliquant sur **Échec de validation** dans la colonne État du tableau. Réglez le problème et recommencez la validation.
    - Pour supprimer des hôtes ou des clusters, cliquez sur **Supprimer**.
    - Vous ne pouvez pas supprimer un hôte spécifique d’un cluster. Vous pouvez supprimer seulement la totalité du cluster.
    - Vous pouvez ajouter un cluster, même s’il existe des problèmes avec des hôtes spécifiques dans le cluster.
1. Vous pouvez **revalider** la connectivité aux hôtes/clusters à tout moment avant de lancer la découverte.
1. Cliquez sur **Démarrer la découverte** pour lancer la découverte des serveurs à partir des hôtes/clusters validés. Une fois la détection lancée, vous pouvez vérifier son état pour chaque hôte/cluster du tableau.

Ceci démarre la découverte. Il faut environ 2 minutes par hôte pour que les métadonnées des serveurs détectés apparaissent sur le portail Azure.

## <a name="verify-servers-in-the-portal"></a>Vérifier les serveurs dans le portail

Une fois la découverte terminée, vous pouvez vérifier que les serveurs apparaissent dans le portail.

1. Ouvrez le tableau de bord Azure Migrate.
2. Dans la page **Azure Migrate - Serveurs** > **Azure Migrate : découverte et évaluation**, cliquez sur l’icône qui affiche le nombre de **Serveurs découverts**.

## <a name="next-steps"></a>Étapes suivantes

- [Évaluer les serveurs d’un environnement Hyper-V](tutorial-assess-hyper-v.md) en vue de les migrer vers des machines virtuelles Azure
- [Passer en revue les données](migrate-appliance.md#collected-data---hyper-v) que l’appliance collecte pendant la découverte