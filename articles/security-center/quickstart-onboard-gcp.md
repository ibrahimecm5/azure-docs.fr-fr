---
title: Connecter votre compte GCP à Microsoft Defender pour le cloud
description: Supervision de vos ressources GCP à partir de Microsoft Defender pour le cloud
author: memildin
ms.author: memildin
ms.date: 02/08/2021
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1b7d5e5a418fd776197cdbc6108fc4871668292c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131064807"
---
#  <a name="connect-your-gcp-accounts-to-microsoft-defender-for-cloud"></a>Connecter vos comptes GCP à Microsoft Defender pour le cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Les charges de travail cloud couvrant généralement plusieurs plates-formes cloud, les services de sécurité cloud se doivent d’en faire de même.

Microsoft Defender pour le cloud protège les charges de travail dans Azure, Amazon Web Services (AWS) et Google Cloud Platform (GCP).

L’intégration de vos comptes GCP dans Defender pour le cloud intègre GCP Security Command Center et Microsoft Defender pour le cloud. Defender pour le cloud offre ainsi une visibilité et une protection dans ces deux environnements cloud afin de fournir :

- Détection des erreurs de configuration de sécurité
- Une seule vue montrant les recommandations de Defender pour le cloud et les résultats de GCP Security Command Center
- L’incorporation de vos ressources GCP dans les calculs de degré de sécurisation de Defender pour le cloud
- L’intégration des recommandations de GCP Security Command Center basées sur la norme CIS dans le tableau de bord de conformité réglementaire de Defender pour le cloud

Dans la capture d’écran ci-dessous, vous pouvez voir des projets GCP dans le tableau de bord de vue d’ensemble de Defender pour le cloud.

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-account-in-overview.png" alt-text="3 projets GCP listés dans le tableau de bord de vue d’ensemble de Defender pour le cloud" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::


## <a name="availability"></a>Disponibilité

|Aspect|Détails|
|----|:----|
|État de sortie :|Disponibilité générale|
|Prix :|Nécessite [Microsoft Defender pour les serveurs](defender-for-servers-introduction.md)|
|Rôles et autorisations obligatoires :|**Propriétaire** ou **Contributeur** sur l’abonnement Azure approprié|
|Clouds :|:::image type="icon" source="./media/icons/yes-icon.png"::: Clouds commerciaux<br>:::image type="icon" source="./media/icons/no-icon.png"::: National/Souverain (Azure Government, Azure China 21Vianet)|
|||

## <a name="connect-your-gcp-account"></a>Connecter votre compte GCP

Créez un connecteur pour chaque organisation que vous voulez superviser à partir de Defender pour le cloud.

Lors de la connexion de vos comptes GCP à des abonnements Azure spécifiques, tenez compte de la [hiérarchie des ressources Google Cloud](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#resource-hierarchy-detail) et des instructions suivantes :

- Vous pouvez connecter vos comptes GCP à Defender pour le cloud au niveau de l’*organisation*
- Vous pouvez connecter plusieurs organisations à un seul abonnement Azure
- Vous pouvez connecter plusieurs organisations à plusieurs abonnements Azure
- Quand vous connectez une organisation, tous les *projets* au sein de cette organisation sont ajoutés à Defender pour le cloud

Suivez les étapes ci-dessous pour créer votre connecteur cloud GCP. 

### <a name="step-1-set-up-gcp-security-command-center-with-security-health-analytics"></a>Étape 1. Configurer la sécurité de GCP et Command Center avec Security Health Analytics

Pour tous les projets GCP de votre organisation, vous devez également :

1. Configurer **GCP Security Command Center** à l’aide de [ces instructions de la documentation de GCP](https://cloud.google.com/security-command-center/docs/quickstart-scc-setup).
1. Activer **Security Health Analytics** à l’aide de [ces instructions de la documentation de GCP](https://cloud.google.com/security-command-center/docs/how-to-use-security-health-analytics).
1. Vérifier que les données sont transmises à Security Command Center.

Les instructions de connexion de votre environnement GCP pour la configuration de sécurité suivent les recommandations de Google pour l’utilisation des recommandations de configuration de la sécurité. L’intégration s’appuie sur Google Security Command Center et consomme des ressources supplémentaires susceptibles d’avoir un impact sur votre facturation.

Lorsque vous activez pour la première fois Security Health Center, plusieurs heures peuvent être nécessaires pour que les données soient disponibles.


### <a name="step-2-enable-gcp-security-command-center-api"></a>Étape 2. Activer l’API GCP Security Command Center

1. À partir de la **bibliothèque d’API de Google Cloud Console**, sélectionnez chaque projet dans l’organisation que vous voulez connecter à Microsoft Defender pour le cloud.
1. Dans la bibliothèque d’API, recherchez et sélectionnez **API Security Command Center**.
1. Sur la page de l’API, sélectionnez **ACTIVER**.

En savoir plus sur [l’API Security Command Center](https://cloud.google.com/security-command-center/docs/reference/rest/).


### <a name="step-3-create-a-dedicated-service-account-for-the-security-configuration-integration"></a>Étape 3. Créer un compte de service dédié pour l’intégration de la configuration de la sécurité

1. Dans **GCP Console**, sélectionnez un projet à partir de l’organisation dans laquelle vous créez le compte de service demandé. 

    > [!NOTE]
    > Lorsque ce compte de service est ajouté au niveau de l’organisation, il est utilisé pour accéder aux données collectées par Security Command Center à partir de tous les autres projets activés dans l’organisation. 

1. Dans le **menu de navigation**, sous les options **IAM & admin**, sélectionnez **Comptes de service**.
1. Sélectionnez **CRÉER LE COMPTE DE SERVICE**.
1. Entrez un nom de compte, puis sélectionnez **Créer**.
1. Spécifiez le **Rôle** **Visionneuse d’administration de Defender pour le cloud** et sélectionnez **Continuer**.
1. La section **Accorder aux utilisateurs l’accès à ce compte de service** est facultative. Sélectionnez **Terminé**.
1. Copiez la **valeur E-mail** du compte de service créé, puis enregistrez-la pour une utilisation ultérieure.
1. Dans le **menu de navigation**, sous les options **IAM & admin**, sélectionnez **IAM**
    1. Basculez vers le niveau de l’organisation.
    1. Sélectionnez **AJOUTER**.
    1. Dans le champ **Nouveaux membres**, collez la **valeur E-mail** que vous avez copiée précédemment.
    1. Spécifiez le rôle **Visionneuse d’administration de Defender pour le cloud**, puis sélectionnez **Enregistrer**.
        :::image type="content" source="./media/quickstart-onboard-gcp/iam-settings-gcp-permissions-admin-viewer.png" alt-text="Définition les autorisations GCP pertinentes":::


### <a name="step-4-create-a-private-key-for-the-dedicated-service-account"></a>Étape 4. Créer une clé privée pour le compte de service dédié
1. Passez au niveau du projet.
1. Dans le **menu de navigation**, sous les options **IAM & admin**, sélectionnez **Comptes de service**.
1. Ouvrez le compte de service dédié et sélectionnez Modifier.
1. Dans la section **Clés**, sélectionnez **Ajouter une clé**, puis **Créer une nouvelle clé**.
1. Sur l’écran Créer une clé privée, sélectionnez **JSON**, puis sélectionnez **CRÉER**.
1. Enregistrez ce fichier JSON pour une utilisation ultérieure.


### <a name="step-5-connect-gcp-to-defender-for-cloud"></a>Étape 5. Connecter GCP à Defender pour le cloud
1. Dans le menu de Defender pour le cloud, sélectionnez **Connecteurs cloud**.
1. Sélectionnez Ajouter un compte GCP.
1. Dans la page d’intégration, effectuez les opérations suivantes, puis sélectionnez **Suivant**.
    1. Validez l’abonnement choisi.
    1. Dans le champ **Nom d'affichage**, entrez un nom pour le connecteur.
    1. Dans le champ **ID d’organisation**, entrez l’ID de votre organisation. Si vous ne le connaissez pas, consultez [Création et gestion des organisations](https://cloud.google.com/resource-manager/docs/creating-managing-organization).
    1. Dans la zone de fichier **Clé privée**, accédez au fichier JSON que vous avez téléchargé à l’étape [l’Étape 4. Créer une clé privée pour le compte de service dédié](#step-4-create-a-private-key-for-the-dedicated-service-account).


### <a name="step-6-confirmation"></a>Étape 6. Confirmation

Lorsque le connecteur est correctement créé et que le GCP Security Command Center a été configuré correctement :

- La norme CIS GCP s’affiche dans le tableau de bord de conformité réglementaire de Defender pour le cloud.
- Des recommandations de sécurité pour vos ressources GCP apparaissent dans le portail Defender pour le cloud et le tableau de bord de conformité réglementaire 5-10 minutes après l’intégration :   :::image type="content" source="./media/quickstart-onboard-gcp/gcp-resources-in-recommendations.png" alt-text="Ressources et recommandations GCP dans la page de recommandations de Defender pour le cloud":::


## <a name="monitoring-your-gcp-resources"></a>Surveillance de vos ressources GCP

Comme vous pouvez le voir ci-dessus, la page de recommandations de sécurité de Microsoft Defender pour le cloud affiche vos ressources GCP avec vos ressources Azure et AWS pour une véritable vue multicloud.

Afin de voir toutes les recommandations actives pour vos ressources par type de ressource, utilisez la page d’inventaire des ressources de Defender pour le cloud et filtrez sur le type de ressource GCP qui vous intéresse :

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-resource-types-in-inventory.png" alt-text="Filtre de type de ressource de la page d’inventaire des ressources avec les options GCP"::: 


## <a name="faq---connecting-gcp-accounts-to-microsoft-defender-for-cloud"></a>FAQ - Connexion de vos comptes GCP à Microsoft Defender pour le cloud

### <a name="can-i-connect-multiple-gcp-organizations-to-defender-for-cloud"></a>Puis-je connecter plusieurs organisations GCP à Defender pour le cloud ?
Oui. Le connecteur GCP de Defender pour le cloud connecte vos ressources Google Cloud au niveau de l’*organisation*. 

Créez un connecteur pour chaque organisation GCP que vous voulez superviser à partir de Defender pour le cloud. Quand vous connectez une organisation, tous les projets au sein de cette organisation sont ajoutés à Defender pour le cloud.

Découvrez la hiérarchie des ressources Google Cloud dans la [documentation en ligne de Google](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy).


### <a name="is-there-an-api-for-connecting-my-gcp-resources-to-defender-for-cloud"></a>Existe-t-il une API pour connecter mes ressources GCP à Defender pour le cloud ?
Oui. Pour créer, modifier ou supprimer des connecteurs cloud Defender pour le cloud avec une API REST, consultez les détails de [l’API Connectors](/rest/api/securitycenter/connectors).

## <a name="next-steps"></a>Étapes suivantes

La connexion de votre compte GCP fait partie de l’expérience multicloud disponible dans Microsoft Defender pour le cloud. Pour accéder à des informations connexes, consultez la page suivante :

- [Connecter vos comptes AWS à Microsoft Defender pour le cloud](quickstart-onboard-aws.md)
- [Hiérarchie des ressources Google Cloud](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy)--En savoir plus sur la hiérarchie des ressources Google Cloud dans la documentation en ligne de Google
