---
title: Configurer une appliance Azure Migrate pour les serveurs physiques
description: Découvrez comment configurer une appliance Azure Migrate pour la détection et l’évaluation de serveurs physiques.
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/02/2021
ms.openlocfilehash: 171b03e53266949f1ceae79dd0bbeae7ab146eda
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500447"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>Configurer une appliance pour les serveurs physiques

Cet article explique comment configurer l’appliance Azure Migrate si vous évaluez des serveurs physiques avec l’outil Azure Migrate : Découverte et évaluation.

L’appliance Azure Migrate est une appliance légère utilisée par Azure Migrate : découverte et évaluation pour effectuer les opérations suivantes :

- Découvrir les serveurs locaux.
- Envoyer des métadonnées et des données de performances concernant les serveurs découverts à Azure Migrate : découverte et évaluation.

[En savoir plus](migrate-appliance.md) sur les appliances d’Azure Migrate.


## <a name="appliance-deployment-steps"></a>Étapes de déploiement d’appliance

Pour configurer l’appliance, vous devez :

1. Fournissez un nom d’appliance et générez une clé de projet sur le portail.
2. Télécharger un fichier compressé avec le script du programme d’installation Azure Migrate à partir du portail Azure.
3. Extraire le contenu du fichier compressé. Lancer la console PowerShell avec des privilèges administratifs.
4. Exécuter le script PowerShell pour lancer le gestionnaire de configuration de l’appliance.
5. Configurez l’appliance pour la première fois, puis inscrivez-la auprès du projet en utilisant la clé de projet.

### <a name="generate-the-project-key"></a>Générer la clé de projet

1. Dans **Objectifs de migration** > **Serveurs** > **Azure Migrate : découverte et évaluation**, sélectionnez **Découvrir**.
2. Dans **Découvrir des serveurs** > **Vos serveurs sont-ils virtualisés ?** , sélectionnez **Physiques ou autres (AWS, GCP, Xen, etc.)** .
3. Dans **1 : Générer une clé de projet**, attribuez un nom à l’appliance Azure Migrate que vous allez configurer pour la découverte des serveurs virtuels ou physiques. Le nom doit être alphanumérique et comporter 14 caractères au maximum.
1. Cliquez sur **Générer une clé** pour lancer la création des ressources Azure nécessaires. Ne fermez pas la page Découvrir des serveurs pendant la création de ressources.
1. Une fois les ressources Azure créées, une **clé de projet** est générée.
1. Copiez la clé car vous en aurez besoin pour terminer l'inscription de l'appliance lors de sa configuration.

   ![Sélections pour la génération d’une clé](./media/tutorial-assess-physical/generate-key-physical-1.png)

### <a name="download-the-installer-script"></a>Télécharger le script du programme d’installation

Dans **2 : Télécharger l’appliance Azure Migrate**, cliquez sur **Télécharger**.

### <a name="verify-security"></a>Vérifier la sécurité

Vérifiez que le fichier compressé est sécurisé avant de le déployer.

1. Sur le serveur où vous avez téléchargé le fichier, ouvrez une fenêtre de commande d’administrateur.
2. Exécutez la commande suivante pour générer le code de hachage du fichier compressé :
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemple d’utilisation : ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
3.  Vérifiez la dernière version de l’appliance et la valeur de hachage :

    **Télécharger** | **Valeur de hachage**
    --- | ---
    [Version la plus récente](https://go.microsoft.com/fwlink/?linkid=2140334) | 3C00F9EB54CC6C55E127EDE47DFA28CCCF752697377EB1C9F3435E75DA5AA029

> [!NOTE]
> Le même script peut être utilisé pour configurer une appliance Physique pour un cloud public Azure ou un cloud Azure Government.

### <a name="run-the-azure-migrate-installer-script"></a>Exécuter le script du programme d’installation Azure Migrate

1. Extrayez le fichier compressé dans un dossier sur le serveur qui hébergera l’appliance.  Veillez à ne pas exécuter le script sur un serveur disposant d’une appliance Azure Migrate.
2. Lancez PowerShell sur le serveur ci-dessus avec un privilège administratif (élevé).
3. Remplacez le répertoire PowerShell par le dossier dans lequel le contenu a été extrait du fichier compressé téléchargé.
4. Exécutez le script nommé **AzureMigrateInstaller.ps1** via la commande suivante :

   `PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1`

5. Sélectionnez parmi les options de scénario, de cloud et de connectivité pour déployer une appliance avec la configuration souhaitée. Par exemple, la sélection présentée ci-dessous configure une appliance pour découvrir et évaluer des **serveurs physiques** _(ou des serveurs qui s’exécutent sur d’autres clouds comme AWS, GCP, Xen, etc.)_ dans un projet Azure Migrate avec une **connectivité par défaut _(point de terminaison public)_** sur le **cloud public Azure**.

    :::image type="content" source="./media/tutorial-discover-physical/script-physical-default-1.png" alt-text="Capture d’écran montrant comment configurer l’appliance avec la configuration souhaitée.":::

6. Le script du programme d’installation effectue les opérations suivantes :

    - Installe les agents et une application web.
    - Installe des rôles Windows, notamment le service d’activation Windows, IIS et PowerShell ISE.
    - Télécharge et installe un module réinscriptible IIS.
    - Met à jour une clé de Registre (HKLM) avec les détails de paramètres persistants pour Azure Migrate.
    - Crée les fichiers suivants sous le chemin :
        - **Fichiers de configuration** : %Programdata%\Microsoft Azure\Config
        - **Fichiers journaux** : %Programdata%\Microsoft Azure\Logs

Une fois que le script a été exécuté avec succès, le gestionnaire de configuration de l’appliance est lancé automatiquement.

> [!NOTE]
> Si vous rencontrez des problèmes, vous pouvez accéder aux journaux de script dans C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>horodatage</em>.log pour les résoudre.

### <a name="verify-appliance-access-to-azure"></a>Vérifier l’accès de l’appliance à Azure

Vérifiez que l’appliance peut se connecter aux URL Azure pour les clouds [publics](migrate-appliance.md#public-cloud-urls) et du [secteur public](migrate-appliance.md#government-cloud-urls).

### <a name="configure-the-appliance"></a>Configurer l’appliance

Configurez l’appliance pour la première fois.

1. Ouvrez un navigateur sur une machine qui peut se connecter à l’appliance, puis ouvrez l’URL de l’application web de l’appliance : **https://*nom ou adresse IP de l’appliance* : 44368**.

   Vous pouvez aussi ouvrir l’application à partir du bureau en cliquant sur le raccourci de l’application.
2. Acceptez les **termes du contrat de licence** et lisez les informations relatives aux tiers.
1. Dans l’application web > **Configurer les prérequis**, procédez comme suit :
    - **Connectivité** : L’application vérifie que le serveur a accès à Internet. Si le serveur utilise un proxy :
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
4. Si le compte d’utilisateur Azure utilisé pour la connexion dispose des [autorisations](./tutorial-discover-physical.md) adéquates sur les ressources Azure créées au moment de la génération de la clé, l’inscription de l’appliance est lancée.
1. Une fois l'appliance inscrite, vous pouvez consulter les détails de l'inscription en cliquant sur **Afficher les détails**.


## <a name="start-continuous-discovery"></a>Démarrer la découverte en continu

À présent, connectez-vous à partir de l’appliance aux serveurs physiques à découvrir, puis démarrez la découverte.

1. À l’**Étape 1 : Fournir des informations d’identification pour la découverte des serveurs physiques ou virtuels Windows et Linux**, cliquez sur **Ajouter des informations d’identification**.
1. Pour le serveur Windows, sélectionnez le type de source **Windows Server**, spécifiez un nom convivial pour les informations d’identification, puis ajoutez le nom d’utilisateur et le mot de passe. Cliquez sur **Save**(Enregistrer).
1. Si vous utilisez l’authentification par mot de passe pour le serveur Linux, sélectionnez le type de source **Serveur Linux (basé sur un mot de passe)** , spécifiez un nom convivial pour les informations d’identification, puis ajoutez le nom d’utilisateur et le mot de passe. Cliquez sur **Save**(Enregistrer).
1. Si vous utilisez l’authentification par clé SSH pour le serveur Linux, vous pouvez sélectionner le type de source **Serveur Linux (par clé SSH)** , spécifier un nom convivial pour les informations d’identification, ajouter le nom d’utilisateur, puis rechercher et sélectionner le fichier de clé privée SSH. Cliquez sur **Save**(Enregistrer).

    - Azure Migrate prend en charge la clé privée SSH générée par la commande ssh-keygen avec les algorithmes RSA, DSA, ECDSA et ed25519.
    - Azure Migrate ne prend pas en charge la clé SSH basée sur une phrase secrète. Utilisez une clé SSH sans phrase secrète.
    - Actuellement, Azure Migrate ne prend pas en charge le fichier de clé privée SSH généré par PuTTy.
    - Azure Migrate prend en charge le format OpenSSH du fichier de clé privée SSH, comme indiqué ci-dessous :
    
    ![Format de clé privée SSH pris en charge](./media/tutorial-discover-physical/key-format.png)
1. Si vous souhaitez ajouter plusieurs informations d’identification à la fois, cliquez sur **Ajouter** pour enregistrer et ajouter d’autres informations d'identification. Plusieurs informations d’identification sont prises en charge pour la découverte de serveurs physiques.
1. Dans **Étape 2 : Fournir les détails du serveur physique ou virtuel**, cliquez sur **Ajouter une source de découverte** pour spécifier l’**adresse IP/nom de domaine complet** du serveur ainsi que le nom convivial des informations d’identification pour se connecter au serveur.
1. Vous pouvez soit **ajouter un seul élément** à la fois, soit **ajouter plusieurs éléments** simultanément. Il existe également une option permettant de fournir les détails du serveur via **Importer au format CSV**.

    ![Sélections pour l’ajout de la source de découverte](./media/tutorial-assess-physical/add-discovery-source-physical.png)

    - Si vous choisissez **Ajouter un seul élément**, vous pouvez choisir le type de système d’exploitation, attribuer un nom convivial aux informations d’identification, ajouter l’**adresse IP/nom de domaine complet** du serveur, puis cliquer sur **Enregistrer**.
    - Si vous choisissez **Ajouter plusieurs éléments**, vous pouvez ajouter plusieurs enregistrements à la fois en spécifiant l’**adresse IP/nom de domaine complet** du serveur avec le nom convivial des informations d’identification dans la zone de texte. Vérifiez les enregistrements ajoutés, puis cliquez sur **Enregistrer**.
    - Si vous choisissez **Importer au format CSV** _(sélectionné par défaut)_ , vous pouvez télécharger un fichier de modèle CSV, compléter le fichier avec l’**adresse IP/nom de domaine complet** du serveur et le nom convivial des informations d’identification. Importez ensuite le fichier dans l’appliance, **vérifiez** les enregistrements dans le fichier, puis cliquez sur **Enregistrer**.

1. Dès que vous cliquez sur Enregistrer, l’appliance tente de valider la connexion aux serveurs ajoutés et affiche l’**état de validation** de chaque serveur dans le tableau.
    - Si la validation échoue pour un serveur, examinez l’erreur en cliquant sur **Échec de la validation** dans la colonne État du tableau. Corrigez le problème et recommencez la validation.
    - Pour supprimer un serveur, cliquez sur **Supprimer**.
1. Vous pouvez **revalider** la connectivité aux serveurs à tout moment avant de lancer la découverte.
1. Cliquez sur **Démarrer la découverte** pour lancer la découverte des serveurs validés. Une fois la découverte lancée, vous pouvez vérifier l’état de la découverte de chaque serveur dans le tableau.


Ceci démarre la découverte. Il faut environ deux minutes par serveur pour que les métadonnées du serveur détecté s’affichent sur le portail Azure.

## <a name="verify-servers-in-the-portal"></a>Vérifier les serveurs dans le portail

Une fois la découverte terminée, vous pouvez vérifier que les serveurs apparaissent dans le portail.

1. Ouvrez le tableau de bord Azure Migrate.
2. Dans la page **Azure Migrate - Windows, Linux et serveurs SQL** > **Azure Migrate : Discovery and assessment**, cliquez sur l’icône qui affiche le nombre de **Serveurs découverts**.


## <a name="next-steps"></a>Étapes suivantes

Essayez l’[évaluation des serveurs physiques](tutorial-assess-physical.md) avec Azure Migrate : Découverte et évaluation.
