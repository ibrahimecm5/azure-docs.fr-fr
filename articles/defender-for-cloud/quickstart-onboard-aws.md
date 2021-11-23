---
title: Connecter votre compte AWS à Microsoft Defender pour le cloud
description: Défendre vos ressources AWS avec Microsoft Defender pour le cloud
author: memildin
ms.author: memildin
ms.date: 11/09/2021
ms.topic: quickstart
ms.service: defender-for-cloud
manager: rkarlin
zone_pivot_groups: connect-aws-accounts
ms.openlocfilehash: 37352dcf8ae68a107fd10dc76a7ccce5fbab95d0
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132526367"
---
#  <a name="connect-your-aws-accounts-to-microsoft-defender-for-cloud"></a>Connecter vos comptes AWS à Microsoft Defender pour le cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Les charges de travail cloud couvrant généralement plusieurs plates-formes cloud, les services de sécurité cloud se doivent d’en faire de même.

Microsoft Defender pour le cloud protège les charges de travail dans Azure, Amazon Web Services (AWS) et Google Cloud Platform (GCP).

Pour protéger vos ressources basées sur AWS, vous pouvez connecter un compte avec l’un des deux mécanismes suivants :

- **Expérience des connecteurs cloud classiques** : dans le cadre de l’offre multicloud initiale, nous avons introduit ces connecteurs cloud pour connecter vos comptes AWS et GCP. Si vous avez déjà configuré un connecteur AWS en utilisant l’expérience des connecteurs cloud classiques, nous vous recommandons de reconnecter le compte en utilisant le mécanisme plus récent. Une fois que vous avez ajouté votre compte dans la page des paramètres d’environnement, supprimez l’ancien connecteur pour ne pas voir les recommandations en double.

- **Page des paramètres d’environnement (en préversion)** (recommandé) : cette page en préversion fournit une expérience d’intégration simplifiée et nettement améliorée (y compris le provisionnement automatique). Ce mécanisme étend également les fonctionnalités de sécurité renforcée de Defender pour le cloud à vos ressources AWS.

    - Les **fonctionnalités CSPM de Defender pour le cloud** s’étendent à vos ressources AWS. Ce plan sans agent évalue vos ressources AWS conformément aux recommandations de sécurité spécifiques à AWS. Celles-ci sont incluses dans votre niveau de sécurité. Les ressources sont également évaluées par rapport à leur conformité aux standards intégrés spécifiques à AWS (AWS CIS, AWS PCI DSS et AWS Foundational Security Best Practices). La [page d’inventaire des ressources](asset-inventory.md) de Defender pour le cloud est une fonctionnalité multicloud qui vous permet de gérer vos ressources AWS avec vos ressources Azure.
    - **Microsoft Defender pour Kubernetes** étend sa détection des menaces contre les conteneurs et ses défenses avancées à vos **clusters Amazon EKS Linux**.
    - **Microsoft Defender pour les serveurs** ajoute la détection des menaces et les défenses avancées à vos instances EC2 Linux et Windows. Ce plan comprend la licence intégrée de Microsoft Defender pour point de terminaison, des bases de référence de sécurité et des évaluations de niveau système d’exploitation, l’analyse de l’évaluation des vulnérabilités, des contrôles d’application adaptatifs (AAC), le monitoring de l’intégrité des fichiers (FIM), etc.

Cette capture d’écran montre des comptes AWS dans le [tableau de bord de vue d’ensemble](overview-page.md) de Defender pour le cloud.

:::image type="content" source="./media/quickstart-onboard-aws/aws-account-in-overview.png" alt-text="Quatre projets AWS listés dans le tableau de bord de vue d’ensemble de Defender pour le cloud" lightbox="./media/quickstart-onboard-aws/aws-account-in-overview.png":::


::: zone pivot="env-settings"

## <a name="availability"></a>Disponibilité

|Aspect|Détails|
|----|:----|
|État de sortie :|Préversion.<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]|
|Prix :|Le plan CSPM est gratuit.<br>Le plan Defender pour Kubernetes est gratuit pendant la préversion. Il est ensuite facturé au même prix que le plan Defender pour Kubernetes pour les ressources Azure.<br>Pour chaque machine AWS connectée à Azure avec des [serveurs avec Azure Arc](../azure-arc/servers/overview.md), le plan Defender pour les serveurs est facturé au même prix que le plan Defender pour les serveurs pour les machines Azure. Si une machine EC2 AWS n’a pas d’agent Azure Arc déployé, elle ne vous est pas facturée.|
|Rôles et autorisations obligatoires :|**Propriétaire** sur l’abonnement Azure approprié<br>Un **Contributeur** peut également connecter un compte AWS si un propriétaire fournit les détails du principal de service (obligatoire pour le plan Defender pour les serveurs)|
|Clouds :|:::image type="icon" source="./media/icons/yes-icon.png"::: Clouds commerciaux<br>:::image type="icon" source="./media/icons/no-icon.png"::: National (Azure Government, Azure China 21Vianet)|
|||

## <a name="prerequisites"></a>Prérequis

- Pour connecter un compte AWS à votre abonnement Azure, vous devez évidemment avoir un accès à un compte AWS.

- **Pour activer le plan Defender pour Kubernetes**, vous avez besoin des ressources suivantes :
    - Au moins un cluster Amazon EKS avec l’autorisation d’accéder au serveur d’API K8s EKS. Si vous devez créer un cluster EKS, suivez les instructions de [Bien démarrer avec Amazon EKS – eksctl](https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html).
    - Une capacité de ressources suffisante pour créer une file d’attente SQS, un flux de livraison Kinesis Firehose et un compartiment S3 dans la région du cluster.

- **Pour activer le plan Defender pour les serveurs**, vous avez besoin des ressources suivantes :
    - Microsoft Defender pour les serveurs activé (consultez [Démarrage rapide : Activer les fonctionnalités de sécurité renforcée](enable-enhanced-security.md).
    - Un compte AWS actif avec des instances EC2 gérées par AWS Systems Manager (SSM) et utilisant l’agent SSM. Certaines Amazon Machine Images (AMI) ont l’agent SSM préinstallé, ces AMI sont listées dans [AMIs avec SSM Agent préinstallé](https://docs.aws.amazon.com/systems-manager/latest/userguide/ssm-agent-technical-details.html#ami-preinstalled-agent). Si vos instances EC2 ne disposent pas de l’agent SSM, suivez les instructions correspondantes d’Amazon :
        - [Installer SSM Agent pour un environnement hybride (Windows)](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-managed-win.html)
        - [Installer SSM Agent pour un environnement hybride (Linux)](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-managed-linux.html)


## <a name="connect-your-aws-account"></a>Connecter votre compte AWS

Suivez les étapes ci-dessous pour créer votre connecteur cloud AWS. 

1. Dans le menu de Defender pour le cloud, ouvrez **Paramètres de l’environnement**.
1. Sélectionnez **Ajouter un environnement** > **Amazon Web Services**.

    :::image type="content" source="media/quickstart-onboard-aws/add-aws-account-environment-settings.png" alt-text="Connexion d’un compte AWS à un abonnement Azure.":::

1. Entrez les détails du compte AWS, y compris l’emplacement où stocker la ressource de connecteur, puis sélectionnez **Suivant : Sélectionner des plans**.

    :::image type="content" source="media/quickstart-onboard-aws/add-aws-account-details.png" alt-text="Étape 1 de l’Assistant Ajout d’un compte AWS : Entrer les détails du compte.":::

1. L’onglet Sélectionner des plans vous permet de choisir les fonctionnalités Defender pour le cloud à activer pour ce compte AWS. 

    > [!NOTE]
    > Chaque fonctionnalité a ses propres exigences en matière d’autorisations et peut entraîner des frais.

    :::image type="content" source="media/quickstart-onboard-aws/add-aws-account-plans-selection.png" alt-text="L’onglet Sélectionner des plans vous permet de choisir les fonctionnalités Defender pour le cloud à activer pour ce compte AWS.":::

    > [!IMPORTANT]
    > Pour présenter l’état actuel de vos recommandations, le plan CSPM interroge les API de ressources AWS plusieurs fois par jour. Ces appels d’API en lecture seule ne sont pas facturés, mais ils *sont* inscrits dans CloudTrail si vous avez activé un traçage pour les événements de lecture. Comme expliqué dans [la documentation AWS](https://aws.amazon.com/cloudtrail/pricing/), aucun frais supplémentaire n’est facturé pour la conservation d’un seul traçage. Si vous exportez les données à en dehors d’AWS (par exemple vers une solution SIEM externe), cette augmentation du volume des appels peut également augmenter les coûts d’ingestion. Dans ce cas, nous vous recommandons de filtrer les appels en lecture seule de l’utilisateur ou du rôle ARN de Defender pour le cloud : arn:aws:iam::[IDCompte]:role/CspmMonitorAws (c’est le nom de rôle par défaut : vérifiez le nom du rôle configuré sur votre compte).

    - Afin d’étendre la couverture de Defender pour les serveurs à votre machine AWS EC2, définissez le plan **Serveurs** sur **Activé** et modifiez la configuration selon vos besoins. 

    - Afin d’étendre la couverture de Defender pour Kubernetes à vos clusters AWS EKS Linux, définissez le plan **Conteneurs** sur **Activé** et modifiez la configuration selon vos besoins.

1. Effectuez la configuration :
    1. Sélectionnez **Suivant : Configurer l’accès**.
    1. Téléchargez le modèle CloudFormation.
    1. Utilisez le modèle CloudFormation téléchargé pour créer la pile dans AWS, comme indiqué à l’écran.
    1. Sélectionnez **Suivant : Vérifier et générer**.
    1. Sélectionnez **Create** (Créer).

Defender pour le cloud commence immédiatement l’analyse de vos ressources AWS et affiche des recommandations de sécurité au bout de quelques heures.

::: zone-end


::: zone pivot="classic-connector"

## <a name="availability"></a>Disponibilité

|Aspect|Détails|
|----|:----|
|État de sortie :|Disponibilité générale|
|Prix :|Nécessite [Microsoft Defender pour les serveurs](defender-for-servers-introduction.md)|
|Rôles et autorisations obligatoires :|**Propriétaire** sur l’abonnement Azure approprié<br>Le **contributeur** peut également connecter un compte AWS si un propriétaire fournit les détails du principal de service|
|Clouds :|:::image type="icon" source="./media/icons/yes-icon.png"::: Clouds commerciaux<br>:::image type="icon" source="./media/icons/no-icon.png"::: National (Azure Government, Azure China 21Vianet)|
|||


## <a name="connect-your-aws-account"></a>Connecter votre compte AWS

Suivez les étapes ci-dessous pour créer votre connecteur cloud AWS. 

### <a name="step-1-set-up-aws-security-hub"></a>Étape 1. Configurer AWS Security Hub :

1. Pour afficher les recommandations de sécurité pour plusieurs régions, répétez les étapes suivantes pour chaque région concernée.

    > [!IMPORTANT]
    > Si vous utilisez un compte d’administration AWS, répétez les trois étapes suivantes pour configurer le compte d’administration et tous les comptes membres connectés dans toutes les régions concernées

    1. Activez [Configuration d’AWS](https://docs.aws.amazon.com/config/latest/developerguide/gs-console.html).
    1. Activer [AWS Security Hub](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-settingup.html).
    1. Vérifiez que les données sont transmises à Security Hub. Lorsque vous activez Security Hub pour la première fois, plusieurs heures peuvent être nécessaires pour que les données soient disponibles.

### <a name="step-2-set-up-authentication-for-defender-for-cloud-in-aws"></a>Étape 2. Configurer l’authentification de Defender pour le cloud dans AWS

Il existe deux façons d’autoriser Defender pour le cloud à s’authentifier auprès de AWS :

- **Créer un rôle IAM pour Defender pour le cloud** (recommandé) : méthode la plus sécurisée
- **Utilisateur AWS pour Defender pour le cloud** : option moins sécurisée si IAM n’est pas activé

#### <a name="create-an-iam-role-for-defender-for-cloud"></a>Créer un rôle IAM pour Defender pour le cloud
1. Depuis votre console Amazon Web Services, sous **Security, Identity & Compliance** (Sécurité, identité et conformité), sélectionnez **IAM**.
    :::image type="content" source="./media/quickstart-onboard-aws/aws-identity-and-compliance.png" alt-text="Services AWS.":::

1. Sélectionnez **Rôles** et **Créer un rôle**.
1. Sélectionnez **Another AWS account** (Autre compte AWS).
1. Entrez les informations suivantes :

    - **ID de compte** : entrez l’ID de compte Microsoft (**158177204117**) comme indiqué dans la page du connecteur AWS dans Defender pour le cloud.
    - **Exiger un ID externe** : doit être sélectionné
    - **ID externe** : entrez l’ID d’abonnement comme indiqué dans la page du connecteur AWS dans Defender pour le cloud 

1. Sélectionnez **Suivant**.
1. Dans la section **Attacher des stratégies d’autorisation**, sélectionnez les [stratégies managées AWS](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_job-functions.html) suivantes :

    - SecurityAudit (`arn:aws:iam::aws:policy/SecurityAudit`)
    - AmazonSSMAutomationRole (`arn:aws:iam::aws:policy/service-role/AmazonSSMAutomationRole`)
    - AWSSecurityHubReadOnlyAccess (`arn:aws:iam::aws:policy/AWSSecurityHubReadOnlyAccess`)

1. Ajoutez des étiquettes si vous le souhaitez. L’ajout d’étiquettes à l’utilisateur n’affecte pas la connexion.
1. Sélectionnez **Suivant**.

1. Dans la liste Rôles, choisissez le rôle que vous avez créé

1. Enregistrez le Nom de ressource Amazon (ARN) pour plus tard. 

#### <a name="create-an-aws-user-for-defender-for-cloud"></a>Créer un utilisateur AWS pour Defender pour le Cloud 
1. Ouvrez l’onglet **Utilisateurs**, puis sélectionnez **Ajouter un utilisateur**.
1. À l’étape **Détails**, entrez un nom d’utilisateur pour Defender pour le cloud, et veillez à sélectionner **Accès programmatique**  pour le type d’accès AWS. 
1. Sélectionnez **Next: Permissions** (Suivant : Autorisations).
1. Sélectionnez **Attacher directement des stratégies existantes** et appliquez les stratégies suivantes :
    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess
    
1. Sélectionnez **Suivant : Balises**. Ajoutez des étiquettes si vous le souhaitez. L’ajout d’étiquettes à l’utilisateur n’affecte pas la connexion.
1. Sélectionnez **Révision**.
1. Enregistrez l’**ID de clé d’accès** généré automatiquement et le fichier CSV de la **clé d’accès secrète** pour plus tard.
1. Passez en revue le récapitulatif et sélectionnez **Créer un utilisateur**.


### <a name="step-3-configure-the-ssm-agent"></a>Étape 3. Configurer l’agent SSM

AWS Systems Manager est requis pour l’automatisation des tâches entre vos ressources AWS. Si vos instances EC2 ne disposent pas de l’agent SSM, suivez les instructions correspondantes d’Amazon :

- [Installation et configuration de l’agent SSM sur les instances Windows](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-win.html)
- [Installation et configuration de l’agent SSM sur les instances Amazon EC2 Linux](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-agent.html)


### <a name="step-4-complete-azure-arc-prerequisites"></a>Étape 4. Satisfaire les prérequis Azure Arc
1. Vérifiez que les [fournisseurs de ressources Azure](../azure-arc/servers/agent-overview.md#register-azure-resource-providers) appropriés sont enregistrés :
    - Microsoft.HybridCompute
    - Microsoft.GuestConfiguration

1. Créez un principal de service pour une intégration à grande échelle. En tant que **propriétaire** de l’abonnement que vous souhaitez utiliser pour l’intégration, créez un principal de service pour l’intégration d’Azure Arc, comme décrit dans [Créer un principal de service pour une intégration à grande échelle](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale).


### <a name="step-5-connect-aws-to-defender-for-cloud"></a>Étape 5. Connecter AWS à Defender pour le cloud

1. Dans le menu de Defender pour le cloud, ouvrez **Paramètres d’environnement** et sélectionnez l’option pour revenir à l’expérience des connecteurs classiques.

    :::image type="content" source="media/quickstart-onboard-gcp/classic-connectors-experience.png" alt-text="Retour à l’expérience des connecteurs cloud classiques dans Defender pour le cloud.":::

1. Sélectionnez **Ajouter un compte AWS**.
    :::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Ajouter un compte AWS dans la page Connecteurs multicloud de Defender pour le cloud":::
1. Configurez les options dans l’onglet **Authentification AWS** :
    1. Entrez un **nom d’affichage** pour le connecteur.
    1. Confirmez que l’abonnement est correct. Il s’agit de l’abonnement qui comprend le connecteur et les recommandations d’AWS Security Hub.
    1. Selon l’option d’authentification que vous avez choisie à l’[Étape 2. Configurer l’authentification pour Defender pour le cloud dans AWS](#step-2-set-up-authentication-for-defender-for-cloud-in-aws) :
        - Sélectionnez **Prendre le rôle** et collez l’ARN obtenu à partir de [Créer un rôle IAM pour Defender pour le cloud](#create-an-iam-role-for-defender-for-cloud).

            :::image type="content" source="./media/quickstart-onboard-aws/paste-arn-in-portal.png" alt-text="Collage du fichier ARN dans le champ approprié de l’Assistant de connexion AWS sur le portail Azure.":::

            OR

        - Sélectionnez **Informations d’identification** puis collez la **clé d’accès** et la **clé secrète** du fichier .csv que vous avez enregistré dans [Créer un utilisateur AWS pour Defender pour le cloud](#create-an-aws-user-for-defender-for-cloud).
1. Sélectionnez **Suivant**.
1. Configurez les options dans l’onglet **Configuration d’Azure Arc** :

    Defender pour le cloud découvre les instances EC2 dans le compte AWS connecté et utilise SSM pour les intégrer à Azure Arc. 

    > [!TIP]
    > Pour obtenir la liste des systèmes d’exploitation pris en charge, consultez [Quels sont les systèmes d’exploitation pris en charge pour mes instances EC2 ?](#what-operating-systems-for-my-ec2-instances-are-supported) dans le forum aux questions.

    1. Sélectionnez le **groupe de ressources** et la **région Azure** à laquelle les services AWS EC2 découverts seront intégrés dans l’abonnement sélectionné.
    1. Entrez l’**ID du principal du service** et la **clé secrète du client du principal du service** pour Azure Arc, comme décrit dans [Créer un principal de service pour l’intégration à l’échelle](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)
    1. Si la machine se connecte à Internet via un serveur proxy, spécifiez l’adresse IP du serveur proxy ou le nom et le numéro de port que la machine utilise pour communiquer avec le serveur proxy. Saisissez la valeur au format ```http://<proxyURL>:<proxyport>```
    1. Sélectionnez **Revoir + créer**.

        Afficher le résumé des informations

        Les sections Étiquettes répertorient toutes les étiquettes Azure qui seront automatiquement créées pour chaque EC2 intégré, avec leurs propres détails correspondants pour les reconnaître facilement dans Azure. 

        En savoir plus sur les étiquettes Azure dans [Utiliser des étiquettes pour organiser vos ressources Azure et votre hiérarchie de gestion](../azure-resource-manager/management/tag-resources.md).

### <a name="step-6-confirmation"></a>Étape 6. Confirmation

Lorsque le connecteur est correctement créé et qu’AWS Security Hub a été configuré correctement :

- Defender pour le cloud analyse l’environnement à la recherche des instances AWS EC2 puis il les intègre à Azure Arc, ce qui permet d’installer l’agent Log Analytics et de fournir des recommandations en matière de sécurité et de protection contre les menaces. 
- Le service Defender pour le cloud recherche les nouvelles instances AWS EC2 toutes les 6 heures et les intègre en fonction de la configuration.
- Le standard AWS CIS s’affichera dans le tableau de bord de conformité réglementaire de Defender pour le cloud.
- Si la stratégie Security Hub est activée, des recommandations s’afficheront dans le portail Defender pour le cloud et dans le tableau de bord de conformité aux réglementations 5 à 10 minutes après l’intégration.


::: zone-end

:::image type="content" source="./media/quickstart-onboard-aws/aws-resources-in-recommendations.png" alt-text="Ressources AWS et recommandations dans la page Recommandations de Defender pour le cloud" lightbox="./media/quickstart-onboard-aws/aws-resources-in-recommendations.png":::


## <a name="monitoring-your-aws-resources"></a>Surveillance de vos ressources AWS

Comme vous pouvez le voir dans la capture d’écran précédente, la page des recommandations de sécurité de Defender pour le cloud affiche vos ressources AWS. Vous pouvez utiliser le filtre d’environnements pour profiter des fonctionnalités multicloud de Defender pour le cloud : consultez les recommandations pour l’ensemble des ressources Azure, AWS et GCP.

Si vous souhaitez afficher toutes les recommandations actives pour vos ressources par type de ressource, utilisez la page d’inventaire des ressources de Defender pour le cloud et filtrez sur le type de ressource AWS qui vous intéresse :

:::image type="content" source="./media/quickstart-onboard-aws/aws-resource-types-in-inventory.png" alt-text="Filtre de type de ressource de la page d’inventaire des ressources avec les options AWS"::: 


## <a name="faq---aws-in-defender-for-cloud"></a>FAQ - AWS dans Defender pour le cloud

### <a name="what-operating-systems-for-my-ec2-instances-are-supported"></a>Quels sont les systèmes d’exploitation pris en charge pour mes instances EC2 ?

Système d’exploitation pris en charge pour l’intégration automatique à Azure Arc pour les machines AWS

- Ubuntu 16.04 : l’agent SSM est préinstallé par défaut
- Ubuntu 18.04 : l’agent SSM est préinstallé par défaut
- Windows Server : l’agent SSM est préinstallé par défaut
- CentOS Linux 7 : SSM doit être installé manuellement ou intégré séparément
- SUSE Linux Enterprise Server (SLES) 15 (x64) : SSM doit être installé manuellement ou intégré séparément
- Red Hat Enterprise Linux (RHEL) 7 (x64) : SSM doit être installé manuellement ou intégré séparément


## <a name="next-steps"></a>Étapes suivantes

La connexion de votre compte AWS fait partie de l’expérience multicloud qui est disponible dans Microsoft Defender pour le cloud. Pour accéder à des informations connexes, consultez la page suivante :

- [Connecter vos comptes GCP à Microsoft Defender pour le cloud](quickstart-onboard-gcp.md)
