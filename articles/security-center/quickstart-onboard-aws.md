---
title: Connecter votre compte AWS à Microsoft Defender pour le cloud
description: Supervision de vos ressources AWS à partir de Microsoft Defender pour le cloud
author: memildin
ms.author: memildin
ms.date: 01/24/2021
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
ms.custom: ignite-fall-2021
ms.openlocfilehash: fb37074fae4d984009f33b45c805cb4dcc886551
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131047778"
---
#  <a name="connect-your-aws-accounts-to-microsoft-defender-for-cloud"></a>Connecter vos comptes AWS à Microsoft Defender pour le cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Les charges de travail cloud couvrant généralement plusieurs plates-formes cloud, les services de sécurité cloud se doivent d’en faire de même.

Microsoft Defender pour le cloud protège les charges de travail dans Azure, Amazon Web Services (AWS) et Google Cloud Platform (GCP).

Le fait d’intégrer votre compte AWS à Defender pour le cloud a pour effet d’intégrer AWS Security Hub à Microsoft Defender pour le cloud. Defender pour le cloud offre ainsi une visibilité et une protection dans ces deux environnements cloud dans le but de fournir :

- Le provisionnement automatique des agents (Defender pour le cloud utilise [Azure Arc](../azure-arc/servers/overview.md) pour déployer l’agent Log Analytics sur vos instances AWS)
- Gestion des stratégies
- Gestion des vulnérabilités
- Détection de point de terminaison et réponse incorporée (EDR)
- Détection des erreurs de configuration de sécurité
- Une vue unique présentant les recommandations de Defender pour le cloud et les découvertes d’AWS Security Hub
- L’incorporation de vos ressources AWS dans les calculs de score sécurisés de Defender pour le cloud
- Évaluations de conformité réglementaire de vos ressources AWS

Dans la capture d’écran ci-dessous, vous pouvez voir les comptes AWS dans le tableau de bord de vue d’ensemble de Defender pour le cloud.

:::image type="content" source="./media/quickstart-onboard-aws/aws-account-in-overview.png" alt-text="Trois projets GCP listés dans le tableau de bord de vue d’ensemble de Defender pour le cloud" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::

## <a name="availability"></a>Disponibilité

|Aspect|Détails|
|----|:----|
|État de sortie :|Disponibilité générale|
|Prix :|Nécessite [Microsoft Defender pour les serveurs](defender-for-servers-introduction.md)|
|Rôles et autorisations obligatoires :|**Propriétaire** sur l’abonnement Azure approprié<br>Le **contributeur** peut également connecter un compte AWS si un propriétaire fournit les détails du principal de service|
|Clouds :|:::image type="icon" source="./media/icons/yes-icon.png"::: Clouds commerciaux<br>:::image type="icon" source="./media/icons/no-icon.png"::: National/Souverain (Azure Government, Azure China 21Vianet)|
|||



## <a name="connect-your-aws-account"></a>Connecter votre compte AWS

Suivez les étapes ci-dessous pour créer votre connecteur cloud AWS. 

### <a name="step-1-set-up-aws-security-hub"></a>Étape 1. Configurer AWS Security Hub :

1. Pour afficher les recommandations de sécurité pour plusieurs régions, répétez les étapes suivantes pour chaque région concernée.

    > [!IMPORTANT]
    > Si vous utilisez un compte principal AWS, répétez les trois étapes suivantes pour configurer le compte principal et tous les comptes de membres connectés dans toutes les régions concernées

    1. Activez [Configuration d’AWS](https://docs.aws.amazon.com/config/latest/developerguide/gs-console.html).
    1. Activer [AWS Security Hub](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-settingup.html).
    1. Vérifier que les données sont transmises à Security Hub.

        Lorsque vous activez Security Hub pour la première fois, plusieurs heures peuvent être nécessaires pour que les données soient disponibles.

### <a name="step-2-set-up-authentication-for-defender-for-cloud-in-aws"></a>Étape 2. Configurer l’authentification de Defender pour le cloud dans AWS

Il existe deux façons d’autoriser Defender pour le cloud à s’authentifier auprès de AWS :

- **Créer un rôle IAM pour Defender pour le cloud** : il s’agit de la méthode la plus sécurisée et donc de celle qui est recommandée
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
1. Révisez le résumé, puis cliquez sur **Créer un utilisateur**.


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

1. Dans le menu Defender pour le cloud, sélectionnez **Connecteurs multicloud**.
1. Sélectionnez **Ajouter un compte AWS**.
    :::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Ajouter un compte AWS dans la page Connecteurs multicloud de Defender pour le cloud":::
1. Configurez les options dans l’onglet **Authentification AWS** :
    1. Entrez un **nom d’affichage** pour le connecteur.
    1. Confirmez que l’abonnement est correct. Il s’agit de l’abonnement qui inclura les recommandations relatives au connecteur et à AWS Security Hub.
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
    :::image type="content" source="./media/quickstart-onboard-aws/aws-resources-in-recommendations.png" alt-text="Ressources AWS et recommandations dans la page Recommandations de Defender pour le cloud":::



## <a name="monitoring-your-aws-resources"></a>Surveillance de vos ressources AWS

Comme vous pouvez le voir ci-dessus, la page Recommandations de sécurité de Microsoft Defender pour le cloud affiche vos ressources AWS avec vos ressources Azure et GCP pour une véritable vue multicloud.

Si vous souhaitez afficher toutes les recommandations actives pour vos ressources par type de ressource, utilisez la page d’inventaire des ressources de Defender pour le cloud et filtrez sur le type de ressource AWS qui vous intéresse :

:::image type="content" source="./media/quickstart-onboard-aws/aws-resource-types-in-inventory.png" alt-text="Filtre de type de ressource de la page d’inventaire des ressources avec les options AWS"::: 


## <a name="faq---aws-in-defender-for-cloud"></a>FAQ - AWS dans Defender pour le cloud

### <a name="what-operating-systems-for-my-ec2-instances-are-supported"></a>Quels sont les systèmes d’exploitation pris en charge pour mes instances EC2 ?

Système d’exploitation pris en charge pour l’intégration automatique à Azure Arc pour les machines AWS

- Ubuntu 16.04 : l’agent SSM est préinstallé par défaut
- Ubuntu 18.04 : l’agent SSM est préinstallé par défaut
- Windows Server : l’agent SSM est préinstallé par défaut
- CentOS Linux 7 : SSM doit être installé manuellement ou intégré séparément
- SUSE Linux Enterprise Server (SLES) 15 (x64) : SSM doit être installé manuellement ou intégré séparément
- Red Hat Enterprise Linux (RHEL) 7 (x64) : SSM doit être installé manuellement ou intégré séparément


## <a name="next-steps"></a>Étapes suivantes

La connexion de votre compte AWS fait partie de l’expérience multicloud qui est disponible dans Microsoft Defender pour le cloud. Pour accéder à des informations connexes, consultez la page suivante :

- [Connecter vos comptes GCP à Microsoft Defender pour le cloud](quickstart-onboard-gcp.md)
