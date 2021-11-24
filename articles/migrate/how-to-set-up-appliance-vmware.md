---
title: Configurer une appliance Azure Migrate pour l’évaluation des serveurs dans un environnement VMware
description: Découvrez comment configurer une appliance Azure Migrate pour évaluer et migrer des serveurs dans un environnement VMware.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/12/2021
ms.openlocfilehash: c95c92c1f592cc5f042975e832df66e585d99740
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132402482"
---
# <a name="set-up-an-appliance-for-servers-in-a-vmware-environment"></a>Configurer une appliance pour les serveurs dans un environnement VMware

Cet article explique comment configurer l’appliance Azure Migrate pour l’évaluation à l’aide de l’outil [Azure Migrate : découverte et évaluation](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool).

L’[appliance Azure Migrate](migrate-appliance.md) est une appliance légère que l’outil de découverte et d’évaluation d’Azure Migrate utilise pour découvrir les serveurs locaux s’exécutant dans un environnement vCenter Server et envoyer des métadonnées de configuration et de performances du serveur à Azure.

## <a name="set-up-the-appliance"></a>Configurer l’appliance

Vous pouvez déployer l’appliance Azure Migration à l’aide de quelques méthodes :

- Créer un serveur sur une machine virtuelle vCenter Server à l’aide d’un modèle OVA téléchargé. Il s’agit de la méthode décrite dans cet article.
- Configurer l’appliance sur un serveur existant à l’aide d’un script d’installation PowerShell. Vous devez [exécuter un script PowerShell](deploy-appliance-script.md) si vous ne pouvez pas utiliser un modèle OVA ou si vous êtes dans Azure Government.

Après avoir créé l’appliance, vérifiez qu’elle peut se connecter à Azure Migrate : Découverte et évaluation, inscrivez-la auprès du projet et configurez-la pour lancer une découverte.

### <a name="deploy-by-using-an-ova-template"></a>Déployer à l’aide d’un modèle OVA

Pour configurer l’appliance en utilisant un modèle OVA, vous devez effectuer les étapes décrites plus en détail dans cette section :

1. Fournissez un nom d’appliance et générez une clé de projet sur le portail.
1. Téléchargez un fichier de modèle OVA, puis importez-le dans vCenter Server. Vérifiez que l’OVA est sécurisé.
1. Créez l’appliance à partir du fichier OVA. Vérifiez que l’appliance peut se connecter à Azure Migrate.
1. Configurez l’appliance pour la première fois. 
1. Inscrivez l’appliance auprès du projet avec la clé de projet.

#### <a name="generate-the-project-key"></a>Générer la clé de projet

1. Dans **Objectifs de migration**, sélectionnez **Serveurs** > **Azure Migrate : découverte et évaluation** > **Découvrir**.
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

1. Vérifiez la dernière valeur du code de hachage en comparant le résultat de la commande ci-dessus à la valeur documentée [ici](/azure/migrate/tutorial-discover-vmware#verify-security).

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

<a name="4-configure-the-appliance"></a>

### <a name="configure-the-appliance"></a>Configurer l’appliance

Pour configurer l’appliance pour la première fois :

> [!NOTE]
> Si vous configurez l’appliance avec un [script PowerShell](deploy-appliance-script.md) au lieu d’un modèle OVA téléchargé, vous pouvez ignorer les deux premières étapes.

1. Dans le client vSphere, cliquez avec le bouton droit sur le serveur, puis sélectionnez **Ouvrir la console**.
1. Sélectionnez ou entrez la langue, le fuseau horaire et le mot de passe pour l’appliance.
1. Ouvrez un navigateur sur n’importe quel serveur pouvant se connecter au serveur de l’appliance. Ensuite, ouvrez l’URL du gestionnaire de configuration de l’appliance : `https://appliance name or IP address: 44368`.

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

1. Collez la clé de projet copiée à partir du portail. Si vous n’avez pas la clé, accédez à **Découverte et évaluation** > **Découvrir** > **Gérer les appliances existantes**. Sélectionnez le nom d’appliance que vous avez fourni lors de la génération de la clé de projet, puis copiez la clé affichée.
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
1. Passez en revue les [autorisations requises](add-server-credentials.md#required-permissions) sur le compte pour l’étape 3 : Fournir des informations d’identification de serveur pour faire l’inventaire logiciel, l’analyse des dépendances sans agent, la découverte des instances et bases de données SQL Server, et la découverte d’applications web ASP.NET.
1. Pour ajouter plusieurs informations d’identification à la fois, sélectionnez **Ajouter** afin d’enregistrer les informations d’identification et d’en ajouter d’autres.
    Quand vous sélectionnez **Enregistrer** ou **Ajouter**, l’appliance valide les informations d’identification de domaine auprès de l’instance Active Directory du domaine pour l’authentification. La validation est effectuée après chaque ajout afin d’éviter les verrouillages de compte, car l’appliance procède à une itération pour mapper les informations d’identification aux serveurs respectifs.

Pour vérifier la validation des informations d’identification de domaine :

Dans le gestionnaire de configuration, dans le tableau des informations d’identification, consultez l’**état de validation** des informations d’identification de domaine. Seules les informations d’identification de domaine seront validées.

Si la validation échoue, vous pouvez sélectionner un état d’**échec** pour voir l’erreur de validation. Corrigez le problème, puis sélectionnez **Revalider les informations d’identification** pour retenter la validation des informations d’identification.

:::image type="content" source="./media/tutorial-discover-vmware/add-server-credentials-multiple.png" alt-text="Capture d’écran illustrant la fourniture et la validation de plusieurs informations d’identification.":::

### <a name="start-discovery"></a>Démarrer la découverte

Pour démarrer la découverte de vCenter Server, dans **Étape 3 : Fournir des informations d’identification de serveur pour faire l’inventaire logiciel, l’analyse des dépendances sans agent, la découverte des instances et bases de données SQL Server, et la découverte d’applications web ASP.NET dans votre environnement VMware.** , sélectionnez **Démarrer la découverte**. Une fois la découverte lancée, vous pouvez vérifier son état par rapport à l’adresse IP ou au le nom de domaine complet de vCenter Server dans la table des sources.

## <a name="how-discovery-works"></a>Fonctionnement de la découverte

* Il faut environ 15 minutes par hôte pour que l’inventaire de serveurs découverts apparaisse sur le portail Azure.
* Si vous avez fourni des informations d’identification de serveur, l’inventaire logiciel (découverte des applications installées) est automatiquement lancé une fois la découverte des serveurs exécutant vCenter Server terminée. L’inventaire logiciel est effectué une fois toutes les 12 heures.
* L’[inventaire logiciel](how-to-discover-applications.md) identifie les instances SQL qui s’exécutent sur les serveurs. Avec ces informations collectées, l’appliance tente de se connecter aux diverses instances SQL grâce aux informations d’authentification Windows ou SQL Server fournies sur l’appliance. Ensuite, elle recueille des données sur les bases de données SQL Server et leurs propriétés. La découverte SQL Server est effectuée une fois toutes les 24 heures.
* L’[inventaire logiciel](how-to-discover-applications.md) identifie le rôle de serveur web sur les serveurs. Avec ces informations collectées, l’appliance tente de se connecter au serveur web IIS via les informations d’authentification Windows fournies sur l’appliance. Ensuite, elle collecte des données sur les applications web. La découverte d’applications web est effectuée une fois toutes les 24 heures.
* La découverte des applications installées peut prendre plus de 15 minutes. Sa durée dépend du nombre de serveurs découverts. Pour 500 serveurs, environ une heure s’écoule avant que l’inventaire découvert apparaisse dans le projet Azure Migrate dans le portail.
* Au cours de l’inventaire logiciel, les informations d’identification des serveurs ajoutées sont comparées aux serveurs et validées pour l’analyse des dépendances sans agent. Une fois la découverte des serveurs terminée, vous pouvez activer l’analyse des dépendances sans agent sur les serveurs. Seuls les serveurs pour lesquels la validation réussit peuvent être sélectionnés pour activer l’analyse des dépendances sans agent.
* Les données des bases de données et des instances SQL et les données des applications web commencent à apparaître dans le portail dans les 24 heures qui suivent le démarrage de la découverte.

## <a name="next-steps"></a>Étapes suivantes

Passez en revue les tutoriels pour l’[évaluation VMware](./tutorial-assess-vmware-azure-vm.md) et la [migration sans agent](tutorial-migrate-vmware.md).
