---
title: Utiliser Azure Migrate avec des points de terminaison privés
description: Utilisez la prise en charge des liaisons privées par Azure Migrate pour découvrir, évaluer et migrer à l’aide d’Azure Private Link.
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 05/10/2020
ms.openlocfilehash: c581e89a8252ea8e084a48dc827d75aba90bc670
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500485"
---
# <a name="use-azure-migrate-with-private-endpoints"></a>Utiliser Azure Migrate avec des points de terminaison privés

Cet article décrit l’utilisation d’Azure Migrate pour découvrir, évaluer et migrer des serveurs sur un réseau privé à l’aide d’[Azure Private Link](../private-link/private-endpoint-overview.md).

Vous pouvez utiliser les outils [Azure Migrate : découverte et évaluation](./migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) et [Azure Migrate : migration de serveur](./migrate-services-overview.md#azure-migrate-server-migration-tool) pour vous connecter de manière privée et sécurisée à Azure Migrate via un Peering privé ExpressRoute ou une connexion VPN site à site (S2S) à l’aide de Private Link.

Nous recommandons la méthode de connectivité de point de terminaison privé lorsqu’il existe une exigence organisationnelle pour accéder à Azure Migrate et à d’autres ressources Azure sans traverser les réseaux publics. En utilisant Private Link, vous pouvez utiliser vos circuits de Peering privé ExpressRoute existants pour répondre à vos besoins en matière de bande passante ou de latence.

## <a name="support-requirements"></a>Conditions de prise en charge requises

Passez en revue les autorisations requises suivantes, ainsi que les scénarios et les outils pris en charge.

### <a name="supported-geographies"></a>Zones géographiques prises en charge

La fonctionnalité est désormais disponible en préversion dans les zones géographiques du [cloud public](./migrate-support-matrix.md#supported-geographies-public-cloud) et du [cloud du secteur public](./migrate-support-matrix.md#supported-geographies-azure-government) prises en charge.

### <a name="required-permissions"></a>Autorisations requises

Vous devez disposer des autorisations Contributeur + Administrateur de l’accès utilisateur ou Propriétaire sur l’abonnement.

### <a name="supported-scenarios-and-tools"></a>Scénarios et outils pris en charge

**Déploiement** | **Détails** | **outils**
--- | --- | ---
**Découverte et évaluation** | Effectuez une détection et une évaluation à grande échelle, sans agent, de vos serveurs fonctionnant sur n’importe quelle plateforme. Il peut s’agir, par exemple, de plateformes d’hyperviseur telles que [VMware vSphere](./tutorial-discover-vmware.md) ou [Microsoft Hyper-V](./tutorial-discover-hyper-v.md), de clouds publics tels qu’[AWS](./tutorial-discover-aws.md) ou [GCP](./tutorial-discover-gcp.md), ou même de [serveurs nus](./tutorial-discover-physical.md). | Azure Migrate: découverte et évaluation <br/>
**Inventaire logiciel** | Découvrez des applications, des rôles et des fonctionnalités qui s’exécutent sur des machines virtuelles VMware. | Azure Migrate: découverte et évaluation
**Visualisation des dépendances** | Utilisez la fonctionnalité d’analyse de dépendance pour identifier et comprendre les dépendances entre les serveurs. <br/> La [visualisation de dépendance sans agent](./how-to-create-group-machine-dependencies-agentless.md) est prise en charge en mode natif avec le support de liaison privée Azure Migrate. <br/>La [visualisation des dépendances basée sur un agent](./how-to-create-group-machine-dependencies.md) nécessite une connectivité Internet. Découvrez comment utiliser des [points de terminaison privés pour la visualisation des dépendances basée sur un agent](../azure-monitor/logs/private-link-security.md). | Azure Migrate: découverte et évaluation |
**Migration** | Effectuez [des migrations Hyper-v sans agent](./tutorial-migrate-hyper-v.md) ou utilisez l’approche basée sur un agent pour migrer vos [machines virtuelles VMware](./tutorial-migrate-vmware-agent.md), [machines virtuelles Hyper-V](./tutorial-migrate-physical-virtual-machines.md), [serveurs physiques](./tutorial-migrate-physical-virtual-machines.md), [machines virtuelles exécutées sur AWS](./tutorial-migrate-aws-virtual-machines.md), [machines virtuelles exécutées sur GCP](./tutorial-migrate-gcp-virtual-machines.md)ou machines virtuelles s’exécutant sur un autre fournisseur de virtualisation. | Azure Migrate : Server Migration

>[!Note]
> Les [migrations VMware sans agent](./tutorial-migrate-vmware.md) requièrent un accès ou une connectivité Internet via le Peering Microsoft ExpressRoute. Découvrez comment utiliser des [points de terminaison privés pour effectuer des réplications via un Peering privé ExpressRoute ou une connexion S2S VPN](./replicate-using-expressroute.md).

#### <a name="other-integrated-tools"></a>Autres outils intégrés

D’autres outils de migration sont susceptibles de ne pas pouvoir charger de données d’utilisation dans le projet Azure Migrate si l’accès au réseau public est désactivé. Le projet Azure Migrate doit être configuré de façon à autoriser le trafic de tous les réseaux à recevoir des données provenant d’autres offres d’ [éditeurs de logiciels indépendants (ISV)](./migrate-services-overview.md#isv-integration) Microsoft ou externes.

Pour activer l’accès au réseau public pour le projet Azure Migrate,connectez-vous au portail Azure, accédez à la page **Propriétés d’Azure Migrate** sur le portail Azure et sélectionnez **Non** > **Enregistrer**.

![Capture d’écran montrant la procédure de modification d’un mode d’accès au réseau.](./media/how-to-use-azure-migrate-with-private-endpoints/migration-project-properties.png)

### <a name="other-considerations"></a>Autres considérations

**Considérations** | **Détails**
--- | ---
**Tarification** | Pour plus d’informations sur la tarification, consultez [Tarification Objets blob de pages Azure](https://azure.microsoft.com/pricing/details/storage/page-blobs/) et [Tarification Private Link](https://azure.microsoft.com/pricing/details/private-link/).
**Conditions requises pour le réseau virtuel** | Le point de terminaison de la passerelle ExpressRoute/VPN doit résider dans le réseau virtuel sélectionné ou dans un réseau virtuel connecté à celui-ci. Vous pourriez avoir besoin d’environ 15 adresses IP dans le réseau virtuel.

## <a name="create-a-project-with-private-endpoint-connectivity"></a>Créer un projet avec une connectivité de point de terminaison privé

Pour configurer un nouveau projet Azure Migrate, consultez [Créer et gérer des projets](./create-manage-projects.md#create-a-project-for-the-first-time).

> [!Note]
> Vous ne pouvez pas modifier la méthode de connectivité en connectivité de point de terminaison privé pour les projets Azure Migrate existants.

Dans la section de configuration **Avancé**, indiquez les détails suivants pour créer un point de terminaison privé pour votre projet Azure Migrate.
1. Dans **Méthode de connectivité**, choisissez **Point de terminaison privé**.
1. Dans **Désactiver l’accès au point de terminaison public**, conservez le paramètre par défaut **Non**. Certains outils de migration sont susceptibles de ne pas pouvoir charger de données d’utilisation dans le projet Azure Migrate si l’accès au réseau public est désactivé. En savoir plus sur les [autres outils intégrés](#other-integrated-tools).
1. Dans **Abonnement au réseau virtuel**, sélectionnez l’abonnement pour le réseau virtuel de point de terminaison privé.
1. Dans **Réseau virtuel**, sélectionnez le réseau virtuel pour le point de terminaison privé. L’appliance Azure Migrate et les autres composants logiciels qui doivent se connecter au projet Azure Migrate doivent se trouver sur ce réseau ou sur un réseau virtuel connecté.
1. Dans **Sous-réseau**, sélectionnez le sous-réseau pour le point de terminaison privé.

   ![Capture d’écran montrant la section Avancé sur la page Créer un projet.](./media/how-to-use-azure-migrate-with-private-endpoints/create-project.png)

1. Sélectionnez **Créer** pour créer un projet de migration et y attacher un point de terminaison privé. Attendez quelques minutes, le temps nécessaire au déploiement du projet Azure Migrate. Ne fermez pas cette page tant que la création du projet est en cours.

## <a name="discover-and-assess-servers-for-migration-by-using-private-link"></a>Découvrir et évaluer les serveurs à des fins de migration à l’aide de Private Link

Cette section décrit comment configurer l’appliance Azure Migrate. Vous l’utiliserez ensuite pour découvrir et évaluer les serveurs à des fins de migration.

### <a name="set-up-the-azure-migrate-appliance"></a>Configurer l’appliance Azure Migrate

1. Dans **Détecter des machines** > **Vos machines sont-elles virtualisées ?** , sélectionnez le type de serveur.
1. Dans **Générer une clé de projet Azure Migrate**, fournissez un nom pour l’appliance Azure Migrate.
1. Sélectionnez **Générer une clé** pour créer les ressources Azure requises.

    > [!Important]
    > Ne fermez pas la page **Découvrir des machines** pendant la création de ressources.
    - À cette étape, Azure Migrate crée un coffre de clés, un compte de stockage, un coffre Recovery Services (uniquement pour les migrations VMware sans agent) et quelques ressources internes. Azure Migrate attache un point de terminaison privé à chaque ressource. Les points de terminaison privés sont créés dans le réseau virtuel sélectionné lors de la création du projet.
    - Une fois les points de terminaison privés créés, les enregistrements de ressource CNAME DNS pour les ressources Azure Migrate sont mis à jour vers un alias dans un sous-domaine avec le préfixe *privatelink*. Par défaut, Azure Migrate crée également une zone DNS privée correspondant au sous-domaine *privatelink* pour chaque type de ressource et insère des enregistrements A DNS pour les points de terminaison privés associés. Cette action permet à l’appliance Azure Migrate et à d’autres composants logiciels résidant sur le réseau source d’atteindre les points de terminaison des ressources Azure Migrate sur des adresses IP privées.
    - Azure Migrate active également une [identité managée](../active-directory/managed-identities-azure-resources/overview.md) pour le projet Migrate et accorde des autorisations à l’identité managée pour accéder en toute sécurité au compte de stockage.

1. Une fois la clé correctement générée, copiez les détails de la clé pour configurer et inscrire l’appliance.

#### <a name="download-the-appliance-installer-file"></a>Télécharger le fichier du programme d’installation de l’appliance

L’outil Azure Migrate : découverte et évaluation utilise une appliance Azure Migrate légère. L’appliance effectue la détection du serveur, puis envoie la configuration et les métadonnées de performances du serveur à Azure Migrate.

> [!Note]
> Si vous avez déployé une appliance à l’aide d’un modèle (OVA pour les serveurs d’un environnement VMware et VHD pour un environnement Hyper-V), vous pouvez utiliser la même appliance et l’enregistrer dans un projet Azure Migrate avec une connectivité de point de terminaison privée.

Pour configurer l’appliance :
  1. Téléchargez le fichier ZIP qui contient le script du programme d’installation à partir du portail.
  1. Copiez le fichier zip sur le serveur qui hébergera l’appliance.
  1. Après avoir téléchargé le fichier ZIP, vérifiez la sécurité du fichier.
  1. Exécutez le script du programme d’installation pour déployer l’appliance.

#### <a name="verify-security"></a>Vérifier la sécurité

Vérifiez que le fichier compressé est sécurisé avant de le déployer.

1. Sur le serveur où vous avez téléchargé le fichier, ouvrez une fenêtre de commande d’administrateur.
2. Exécutez la commande suivante pour générer le code de hachage du fichier compressé :
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemple d’utilisation : ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
3.  Vérifiez la dernière version de l’appliance et la valeur de hachage :

    **Télécharger** | **Valeur de hachage**
    --- | ---
    [Version la plus récente](https://go.microsoft.com/fwlink/?linkid=2160648) | 3C00F9EB54CC6C55E127EDE47DFA28CCCF752697377EB1C9F3435E75DA5AA029

> [!NOTE]
> Le même script peut être utilisé pour configurer une appliance avec une connectivité de point de terminaison privé pour l’un des scénarios choisis, tels que VMware, Hyper-V, physique ou autre pour déployer une appliance avec la configuration souhaitée.

Assurez-vous que le serveur répond aux [exigences matérielles](./migrate-appliance.md) du scénario choisi, tel que VMware, Hyper-V, physique ou autre, et qu’il peut se connecter aux [URL requises](./migrate-appliance.md#public-cloud-urls-for-private-link-connectivity).

#### <a name="run-the-azure-migrate-installer-script"></a>Exécuter le script du programme d’installation Azure Migrate

1. Extrayez le fichier compressé dans un dossier sur le serveur qui hébergera l’appliance.  Veillez à ne pas exécuter le script sur un serveur disposant d’une appliance Azure Migrate.

2. Lancez PowerShell sur le serveur ci-dessus avec un privilège administratif (élevé).

3. Remplacez le répertoire PowerShell par le dossier dans lequel le contenu a été extrait du fichier compressé téléchargé.

4. Exécutez le script `AzureMigrateInstaller.ps1` à l’aide de la commande suivante :

   `PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1`

5. Sélectionnez parmi les options de scénario, de cloud et de connectivité pour déployer une appliance avec la configuration souhaitée. Par exemple, la sélection présentée ci-dessous configure une appliance pour la détection et l’évaluation de **serveurs exécutés sur votre environnement VMware** dans un projet Azure Migrate doté d’une **connectivité de point de terminaison privé** sur le **cloud public Azure**.

   :::image type="content" source="./media/how-to-use-azure-migrate-with-private-endpoints/script-vmware-private-inline.png" alt-text="Capture d’écran montrant comment configurer l’appliance avec la configuration souhaitée pour le point de terminaison privé." lightbox="./media/how-to-use-azure-migrate-with-private-endpoints/script-vmware-private-expanded.png":::

Une fois que le script a été exécuté avec succès, le gestionnaire de configuration de l’appliance est lancé automatiquement.

> [!NOTE]
> Si vous rencontrez des problèmes, vous pouvez accéder aux journaux de script dans C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>horodatage</em>.log pour les résoudre.

### <a name="configure-the-appliance-and-start-continuous-discovery"></a>Configurer l’appliance et démarrer la détection en continu

Ouvrez un navigateur sur n’importe quelle machine pouvant se connecter au serveur de l’appliance. Ouvrez l’URL du gestionnaire de configuration de l’appliance : `https://appliance name or IP address: 44368`. Vous pouvez également ouvrir le gestionnaire de configuration à partir du bureau du serveur de l’appliance en sélectionnant le raccourci du gestionnaire de configuration.

#### <a name="set-up-prerequisites"></a>Configurer les prérequis

1. Lisez les informations du tiers et acceptez les **termes du contrat de licence**.

1. Dans le gestionnaire de configuration, sous **Configurer les prérequis**, procédez comme suit :
   - **Connectivité** : l’appliance vérifie l’accès aux URL requises. Si le serveur utilise un proxy :
     - Cliquez sur **Configurer le proxy** pour spécifier l’adresse du proxy `http://ProxyIPAddress` ou `http://ProxyFQDN`, ainsi que le port d’écoute.
     - Spécifiez les informations d’identification si le proxy nécessite une authentification. Seuls les proxys HTTP sont pris en charge.
     - Vous pouvez ajouter une liste d’URL ou d’adresses IP qui doivent contourner le serveur proxy.
        ![Ajout à la liste de contournement du proxy](./media/how-to-use-azure-migrate-with-private-endpoints/bypass-proxy-list.png)
     - Cliquez sur **Enregistrer** pour inscrire la configuration si vous avez mis à jour les détails du serveur proxy ou ajouté des URL ou adresses IP pour contourner le proxy.

        > [!Note]
        > Si vous obtenez une erreur avec le lien aka.ms/* lors de la vérification de la connectivité et que vous ne souhaitez pas que l’appliance accède à cette URL sur Internet, désactivez le service de mise à jour automatique sur l’appliance. Suivez les étapes de la section [Désactiver la mise à jour automatique](./migrate-appliance.md#turn-off-auto-update). Une fois que vous aurez désactivé la mise à jour automatique, la vérification de la connectivité de l’URL aka.ms/* est ignorée.

   - **Synchronisation de l’heure** : L’heure de l’appliance doit être synchronisée avec l’heure Internet pour que la découverte fonctionne correctement.
   - **Installer les mises à jour** : L’appliance vérifie que les dernières mises à jour sont installées. Une fois la vérification terminée, cliquez sur **Voir les services d’appliance** pour voir l’état et les versions des services qui s’exécutent sur le serveur de l’appliance.
        > [!Note]
        > Si vous avez désactivé la mise à jour automatique sur l’appliance, vous pouvez mettre à jour les services de l’appliance manuellement pour obtenir les dernières versions des services. Suivez les étapes de la section [Mise à jour manuelle d’une ancienne version](./migrate-appliance.md#manually-update-an-older-version).
   - **Installer VDDK** : _(uniquement nécessaire pour l’appliance VMware)_ L’appliance vérifie que le VDDK (Virtual Disk Development Kit) de VMware vSphere est installé. S’il n’est pas installé, téléchargez VDDK 6.7 à partir de VMware. Extrayez le contenu zippé téléchargé à l’emplacement spécifié sur l’appliance, comme indiqué dans les instructions d’installation.

#### <a name="register-the-appliance-and-start-continuous-discovery"></a>Inscrire l’appliance et démarrer la détection en continu

Une fois la vérification des prérequis terminée, procédez comme suit pour inscrire l’appliance et démarrer la détection en continu pour les scénarios correspondants :
- [Machines virtuelles VMware](./tutorial-discover-vmware.md#register-the-appliance-with-azure-migrate)
- [Machines virtuelles Hyper-V](./tutorial-discover-hyper-v.md#register-the-appliance-with-azure-migrate)
- [Serveurs physiques](./tutorial-discover-physical.md#register-the-appliance-with-azure-migrate)
- [Machines virtuelles AWS](./tutorial-discover-aws.md#register-the-appliance-with-azure-migrate)
- [Machines virtuelles GCP](./tutorial-discover-gcp.md#register-the-appliance-with-azure-migrate)

>[!Note]
> Si vous rencontrez des problèmes de résolution DNS lors de l’inscription de l’appliance ou au moment du démarrage de la détection, assurez-vous que les ressources Azure Migrate créées à l’étape **Générer la clé** dans le portail sont accessibles à partir du serveur local qui héberge l’appliance Azure Migrate. En savoir plus sur la procédure de vérification de la [connectivité réseau](./troubleshoot-network-connectivity.md).

### <a name="assess-your-servers-for-migration-to-azure"></a>Évaluer vos serveurs en vue de leur migration vers Azure
Une fois la détection terminée, évaluez vos serveurs, tels que les [machines virtuelles VMware](./tutorial-assess-vmware-azure-vm.md), les [machines virtuelles Hyper-V](./tutorial-assess-hyper-v.md), les [serveurs physiques](./tutorial-assess-vmware-azure-vm.md), les [machines virtuelles AWS](./tutorial-assess-aws.md) et les [machines virtuelles GCP](./tutorial-assess-gcp.md), en vue de leur migration vers des machines virtuelles Azure ou vers Azure VMware Solution à l’aide de l’outil Azure Migrate : découverte et évaluation.

Vous pouvez également [évaluer vos machines locales](./tutorial-discover-import.md#prepare-the-csv) avec l’outil Azure Migrate : découverte et évaluation en utilisant un fichier CSV importé.

## <a name="migrate-servers-to-azure-by-using-private-link"></a>Migrer des serveurs vers Azure à l’aide de Private Link

Les sections suivantes décrivent les étapes nécessaires à l’utilisation d’Azure Migrate avec des [points de terminaison privés](../private-link/private-endpoint-overview.md) pour les migrations à l’aide d’un Peering privé ExpressRoute ou de connexions VPN.

Cet article présente une démonstration de faisabilité du déploiement de réplications basées sur un agent afin de migrer vos [machines virtuelles VMware](./tutorial-migrate-vmware-agent.md), [machines virtuelles Hyper-V](./tutorial-migrate-physical-virtual-machines.md), [serveurs physiques](./tutorial-migrate-physical-virtual-machines.md), [machines virtuelles exécutées sur AWS](./tutorial-migrate-aws-virtual-machines.md), [machines virtuelles exécutées sur GCP](./tutorial-migrate-gcp-virtual-machines.md) ou vos machines virtuelles exécutées sur un autre fournisseur de virtualisation à l’aide de points de terminaison privés Azure. Vous pouvez utiliser une approche similaire pour effectuer des [migrations Hyper-V sans agent](./tutorial-migrate-hyper-v.md) en utilisant Private Link.

>[!Note]
>Les [migrations VMware sans agent](./tutorial-assess-physical.md) requièrent un accès ou une connectivité Internet via le Peering Microsoft ExpressRoute.

### <a name="set-up-a-replication-appliance-for-migration"></a>Configurer une appliance de réplication pour la migration

Le diagramme suivant illustre le flux de travail de réplication basé sur un agent avec des points de terminaison privés en utilisant l’outil Azure Migrate : migration de serveur.

![Diagramme illustrant l’architecture de réplication.](./media/how-to-use-azure-migrate-with-private-endpoints/replication-architecture.png)

L’outil ses sert d’une appliance de réplication pour répliquer vos serveurs sur Azure. En savoir plus sur la [procédure de préparation et configuration d’une machine pour l’appliance de réplication](./tutorial-migrate-physical-virtual-machines.md#prepare-a-machine-for-the-replication-appliance).

Une fois l’appliance de réplication configurée, suivez ces étapes pour créer les ressources nécessaires à la migration.

1. Dans **Découvrir des machines** > **Vos machines sont-elles virtualisées ?** , sélectionnez **Non virtualisé/Autre**.
1. Dans **Région cible**, sélectionnez et confirmez la région Azure vers laquelle vous souhaitez migrer les machines.
1. Sélectionnez **Créer des ressources** pour créer les ressources Azure nécessaires. Ne fermez pas la page pendant la création des ressources.
    - Cette étape crée un coffre Recovery Services en arrière-plan et active une identité managée pour le coffre. Un coffre Recovery Services est une entité qui contient les informations de réplication des serveurs et qui permet de déclencher des opérations de réplication.
    - Si le projet Azure Migrate dispose d’une connectivité de point de terminaison privée, un point de terminaison privé est créé pour le coffre Recovery Services. Cette étape ajoute cinq noms de domaine complets (FQDN) au point de terminaison privé, un pour chaque microservice lié au coffre Recovery Services.
    - Les cinq noms de domaine sont mis en forme dans ce modèle : <br/> _{Vault-ID}-asr-pod01-{type}-.{target-geo-code}_ .privatelink.siterecovery.windowsazure.com
    - Par défaut, Azure Migrate crée automatiquement une zone DNS privée et ajoute des enregistrements A DNS pour les microservices du coffre Recovery Services. Le DNS privé est ensuite lié au réseau virtuel du point de terminaison privé.

1. Avant d’inscrire l’appliance de réplication, assurez-vous que les noms de domaine complets des liaisons privées du coffre sont accessibles à partir de la machine qui héberge l’appliance de réplication. Une configuration DNS supplémentaire peut être nécessaire pour que l’appliance de réplication locale puisse résoudre les noms de domaine complets des liaisons privées en adresses IP privées. En savoir plus sur la [procédure de vérification de la connectivité réseau](./troubleshoot-network-connectivity.md#verify-dns-resolution).

1. Une fois la connectivité vérifiée, téléchargez le fichier de configuration et de clé de l’appliance, exécutez le processus d’installation et inscrivez l’appliance auprès d’Azure Migrate. En savoir plus sur la [procédure de configuration de l’appliance de réplication](./tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance). Une fois l’appliance de réplication configurée, suivez ces instructions pour [installer le service de mobilité](./tutorial-migrate-physical-virtual-machines.md#install-the-mobility-service) sur les machines que vous souhaitez migrer.

### <a name="replicate-servers-to-azure-by-using-private-link"></a>Répliquer des serveurs vers Azure à l’aide de Private Link

Suivez [ces étapes](./tutorial-migrate-physical-virtual-machines.md#replicate-machines) pour sélectionner les serveurs à répliquer.

Dans **Répliquer** > **Paramètres de la cible** > **Compte de stockage de cache/réplication**, utilisez la liste déroulante pour sélectionner un compte de stockage à répliquer sur une liaison privée.

Si votre projet Azure Migrate dispose d’une connectivité de point de terminaison privé, vous devez [accorder des autorisations à l’identité managée du coffre Recovery Services](#grant-access-permissions-to-the-recovery-services-vault) pour accéder au compte de stockage requis par Azure Migrate.

Pour activer les réplications sur une liaison privée, [créez un point de terminaison privé pour le compte de stockage](#create-a-private-endpoint-for-the-storage-account-optional).

#### <a name="grant-access-permissions-to-the-recovery-services-vault"></a>Accorder des autorisations d’accès au coffre Recovery Services

Vous devez accorder les autorisations au coffre Recovery Services pour permettre l’accès authentifié au compte de stockage de cache/réplication.

Pour identifier le coffre Recovery Services créé par Azure Migrate et accorder les autorisations requises, procédez comme suit.

**Identifier le coffre Recovery Services et l’ID de l’objet d’identité managée**

Vous trouverez les détails du coffre Recovery Services sur la page **Propriétés** de l’outil Azure Migrate : migration de serveur.

1. Accédez au hub **Azure Migrate** et, sur la vignette **Azure Migrate : migration de serveur**, sélectionnez **Vue d’ensemble**.

    ![Capture d’écran montrant la page Vue d’ensemble du hub Azure Migrate.](./media/how-to-use-azure-migrate-with-private-endpoints/hub-overview.png)

1. Dans le volet gauche, sélectionnez **Propriétés**. Notez le nom du coffre Recovery Services et l’ID de l’identité managée. Le coffre a **Point de terminaison privé** comme **type de connectivité** et **Autre** comme **type de réplication**. Vous aurez besoin de ces informations pour fournir l’accès au coffre.

    ![Capture d’écran montrant la page Propriétés de l’outil Azure Migrate : migration de serveur.](./media/how-to-use-azure-migrate-with-private-endpoints/vault-info.png)

**Autorisations d’accès au compte de stockage**

 Vous devez accorder à l’identité managée du coffre les autorisations de rôle suivantes sur le compte de stockage requis pour la réplication. Dans ce cas, vous devez créer le compte de stockage à l’avance.

>[!Note]
> Lorsque vous migrez des machines virtuelles Hyper-V vers Azure en utilisant Private Link, vous devez accorder l’accès au compte de stockage de réplication et au compte de stockage de cache.

Les autorisations de rôle pour Azure Resource Manager varient en fonction du type de compte de stockage.

|**Type de compte de stockage** | **Autorisations de rôle**|
|--- | ---|
|Type standard | [Contributeur](../role-based-access-control/built-in-roles.md#contributor)<br>[Contributeur aux données Blob du stockage](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|
|Type Premium | [Contributeur](../role-based-access-control/built-in-roles.md#contributor)<br>[Propriétaire des données Blob du stockage](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)

1. Accédez au compte de stockage de réplication/cache sélectionné pour la réplication. Dans le volet gauche, sélectionnez **Contrôle d’accès (IAM)** .

1. Sélectionnez **+ Ajouter**, puis **Ajouter une attribution de rôle**.

   ![Capture d’écran montrant Ajouter une attribution de rôle.](./media/how-to-use-azure-migrate-with-private-endpoints/storage-role-assignment.png)

1. Sur la page **Ajouter une attribution de rôle**, dans la zone **Rôle**, sélectionnez le rôle approprié dans la liste des autorisations mentionnée précédemment. Entrez le nom du coffre noté précédemment, puis sélectionnez **Enregistrer**.

    ![Capture d’écran représentant la page Ajouter une attribution de rôle.](./media/how-to-use-azure-migrate-with-private-endpoints/storage-role-assignment-select-role.png)

1. En plus de ces autorisations, vous devez également autoriser l’accès aux services approuvés Microsoft. Si votre accès réseau est limité aux réseaux sélectionnés, dans l’onglet **Mise en réseau** puis dans la section **Exceptions**, sélectionnez **Autoriser les services Microsoft approuvés à accéder à ce compte de stockage**.

   ![Capture d’écran montrant l’option Autoriser les services Microsoft approuvés à accéder à ce compte de stockage.](./media/how-to-use-azure-migrate-with-private-endpoints/exceptions.png)

### <a name="create-a-private-endpoint-for-the-storage-account-optional"></a>Créer un point de terminaison privé pour le compte de stockage (facultatif)

Pour répliquer à l’aide d’ExpressRoute avec le Peering privé, [créez un point de terminaison privé](../private-link/tutorial-private-endpoint-storage-portal.md#create-storage-account-with-a-private-endpoint) pour les comptes de stockage de cache/réplication (sous-ressource cible : _blob_).

>[!Note]
> Seul un compte de stockage v2 universel offre la possibilité de créer des points de terminaison privés. Pour plus d’informations sur la tarification, consultez [Tarification Objets blob de pages Azure](https://azure.microsoft.com/pricing/details/storage/page-blobs/) et [Tarification Azure Private Link](https://azure.microsoft.com/pricing/details/private-link/).

Créez le point de terminaison privé pour le compte de stockage dans le même réseau virtuel que le point de terminaison privé du projet Azure Migrate ou dans un autre réseau virtuel connecté à ce réseau.

Sélectionnez **Oui** et intégrez à une zone DNS privée. La zone DNS privée permet d’acheminer les connexions entre le réseau virtuel et le compte de stockage via une liaison privée. L’option **Oui** permet d’associer automatiquement la zone DNS au réseau virtuel. Elle ajoute également les enregistrements DNS pour la résolution des nouvelles adresses IP et des nouveaux noms de domaine complets créés. Pour plus d’informations, consultez [Zones DNS privées](../dns/private-dns-overview.md).

Si l’utilisateur qui a créé le point de terminaison privé est également le propriétaire du compte de stockage, la création du point de terminaison privé sera automatiquement approuvée. Dans le cas contraire, le propriétaire doit approuver l’utilisation du point de terminaison privé. Pour approuver ou rejeter une demande de connexion de point de terminaison privé, sur la page du compte de stockage, sous **Mise en réseau**, accédez à **Connexions des points de terminaison privés**.

Vérifiez l’état de la connexion de point de terminaison privé avant de continuer.

![Capture d’écran montrant l’état d’approbation du point de terminaison privé.](./media/how-to-use-azure-migrate-with-private-endpoints/private-endpoint-connection-state.png)

Une fois le point de terminaison privé créé, utilisez la liste déroulante dans **Répliquer** > **Paramètres de la cible** > **Compte de stockage de cache** pour sélectionner le compte de stockage à répliquer sur une liaison privée.

Assurez-vous que l’appliance de réplication locale dispose d’une connectivité réseau au compte de stockage sur son point de terminaison privé. En savoir plus sur la procédure de vérification de la [connectivité réseau](./troubleshoot-network-connectivity.md).

>[!Note]
> Pour les migrations de machines virtuelles Hyper-V vers Azure, si le compte de stockage de réplication est de type _Premium_, vous devez sélectionner un autre compte de stockage de type _Standard_ pour le compte de stockage de cache. Dans ce cas, vous devez créer des points de terminaison privés pour le compte de stockage de réplication et le compte de stockage de cache.

Ensuite, suivez les instructions pour [évaluer et démarrer la réplication](./tutorial-migrate-physical-virtual-machines.md#replicate-machines) et [effectuer des migrations](./tutorial-migrate-physical-virtual-machines.md#run-a-test-migration).

## <a name="next-steps"></a>Étapes suivantes

- Terminez le [processus de migration](./tutorial-migrate-physical-virtual-machines.md#complete-the-migration).
- Passez en revue les [meilleures pratiques post-migration](./tutorial-migrate-physical-virtual-machines.md#post-migration-best-practices).