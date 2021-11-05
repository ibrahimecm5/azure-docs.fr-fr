---
title: Configurer le stockage SQL pour des workflows d’application logique Standard
description: Configurez une base de données SQL pour stocker les artefacts, les états et l’historique des exécutions de workflows d’application logique (Standard) dans Azure Logic Apps monolocataire.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2c88bc4adc49dfb5ddad3ccfd8f20267fc5a420d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131098139"
---
# <a name="set-up-sql-database-storage-for-standard-logic-apps-in-single-tenant-azure-logic-apps-preview"></a>Configurer le stockage de base de données SQL pour des applications logiques Standard dans Azure Logic Apps monolocataire (préversion)

> [!IMPORTANT]
> Cette fonctionnalité est en préversion et est soumise aux [conditions d’utilisation supplémentaires](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) des préversions de Microsoft Azure.

Quand vous choisissez le type de ressource **Application logique (Standard)** pour créer des workflows qui s’exécutent dans Azure Logic Apps monolocataire, App Service Environment v3 ou en dehors d’Azure, vous devez également créer un compte Stockage Azure pour enregistrer les artefacts, états et données d’exécution liés aux workflows. Toutefois, si vous souhaitez davantage de flexibilité et de contrôle sur l’environnement d’exécution, le débit, la mise à l’échelle, les performances et la gestion de vos workflows d’application logique, vous pouvez utiliser le fournisseur de stockage SQL à la place du Stockage Azure pour les transactions de stockage liées aux workflows.

Cet article donne un aperçu des raisons pour lesquelles vous pouvez utiliser le stockage SQL comme fournisseur de stockage principal pour Azure Logic Apps à la place du Stockage Azure. Il montre également comment configurer SQL à des fins de stockage durant la création d’une application logique dans le portail Azure ou durant son déploiement à partir de Visual Studio Code.

Si vous débutez avec les applications logiques, consultez la documentation suivante :

- [Qu’est-ce qu’Azure Logic Apps](logic-apps-overview.md)
- [Architecture monolocataire ou multilocataire et environnement de service d’intégration pour Azure Logic Apps](single-tenant-overview-compare.md)

<a name="why-sql"></a>

## <a name="why-use-sql"></a>Pourquoi utiliser SQL

SQL est une alternative de stockage pour Azure Logic Apps monolocataire qui offre les avantages suivants :

| Avantage | Description |
|---------|-------------|
| **Portabilité** | SQL a de nombreux facteurs de forme : machines virtuelles, PaaS (platform as a service), conteneurs, etc. Vous pouvez exécuter des bases de données SQL dans quasiment tous les emplacements propices à l’exécution de workflows d’application logique. |
| **Contrôle** | SQL permet de contrôler avec précision le débit, les performances et la mise à l’échelle des bases de données pendant des périodes particulières ou pour des charges de travail spécifiques. La tarification de SQL étant basée sur l’utilisation du processeur et le débit, son coût est plus prévisible que celui du Stockage Azure qui varie selon chaque opération. |
| **Utiliser des ressources existantes** | Si vous avez de l’expérience avec les outils Microsoft, vous pouvez utiliser leurs composants pour des intégrations SQL modernes. Vous pouvez réutiliser ces composants dans le cadre de déploiements locaux traditionnels et d’implémentations cloud modernes avec Azure Hybrid Benefit. SQL fournit également des outils éprouvés et bien pris en charge, comme SQL Server Management Studio (SSMS), des interfaces de ligne de commande et des kits SDK. |
| **Conformité** | SQL propose davantage d’options de sauvegarde, de restauration, de basculement et d’intégration des redondances que le Stockage Azure. Vous pouvez appliquer les mêmes mécanismes de niveau entreprise que les autres applications d’entreprise au stockage de votre application logique. |
|||

<a name="when-use-sql"></a>

## <a name="when-to-use-sql"></a>Quand utiliser SQL

Le tableau suivant décrit les raisons pour lesquelles vous pouvez opter pour SQL :

| Scénario | Fournisseur de stockage recommandé |
|----------|----------------------------|
| Vous souhaitez exécuter des workflows d’application logique dans Azure avec davantage de contrôle sur le débit et les performances du stockage. | Utilisez SQL comme fournisseur de stockage, car le Stockage Azure ne fournit pas d’outils pour ajuster le débit et les performances. |
| Vous souhaitez exécuter des workflows d’application logique localement, ce que vous pouvez faire en utilisant [Logic Apps avec Azure Arc](azure-arc-enabled-logic-apps-overview.md). | Utilisez SQL comme fournisseur de stockage afin de pouvoir choisir où héberger votre base de données SQL, par exemple localement dans une machine virtuelle, dans un conteneur ou dans plusieurs clouds. Envisagez d’exécuter vos workflows d’application logique près des systèmes que vous souhaitez intégrer ou de réduire votre dépendance au cloud.   |
| Vous souhaitez des coûts de stockage prévisibles. | Utilisez SQL comme fournisseur de stockage si vous souhaitez davantage de contrôle sur les coûts de mise à l’échelle. Les coûts associés à SQL sont basés sur chaque opération de calcul et d’entrée/sortie par seconde (IOPS). Les coûts du Stockage Azure sont basés sur le nombre d’opérations, ce qui peut convenir mieux aux petites charges de travail avec mise à l’échelle à zéro. |
| Vous souhaitez utiliser SQL plutôt que le Stockage Azure. | SQL est un écosystème bien connu et fiable que vous pouvez utiliser pour appliquer la même gouvernance et la même gestion aux opérations d’arrière-plan de vos applications logiques. |
| Vous souhaitez réutiliser des environnements SQL existants. | Utilisez SQL en tant que fournisseur de stockage si vous possédez déjà des licences SQL que vous souhaitez réutiliser ou moderniser sur le cloud. Vous pouvez également utiliser Azure Hybrid Benefit pour les intégrations d’application logique. |
| Tout le reste | Utilisez le Stockage Azure comme fournisseur de stockage par défaut. |
|||

## <a name="prerequisites"></a>Prérequis

- Un compte et un abonnement Azure actifs. Si vous n’en avez pas,  [inscrivez-vous pour bénéficier d’un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Un environnement SQL à utiliser avec votre application logique. Toutefois, avant de configurer votre environnement, effectuez les étapes suivantes :

  1. Créez une instance SQL Server.

     Les types pris en charge sont  [SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads), [Azure SQL Database](https://azure.microsoft.com/products/azure-sql/database/),  [Azure SQL Managed Instance](https://azure.microsoft.com/products/azure-sql/managed-instance/), etc.

     - Si votre serveur SQL est hébergé sur Azure avec l’un des types pris en charge, veillez à configurer les autorisations suivantes :

       1. Dans le [portail Azure](https://portal.azure.com), accédez à votre ressource SQL Server.

       1. Dans le menu de navigation des ressources, sous **Sécurité**, sélectionnez **Pare-feux et réseaux virtuels**.

       1. Dans le volet qui s’ouvre, sous **Autoriser les services et les ressources Azure à accéder à ce serveur**, sélectionnez **Oui**.

       1. Enregistrez vos modifications.

     - Si votre serveur SQL n’est pas hébergé sur Azure, vérifiez que les pare-feux ou paramètres réseau de votre serveur autorisent les services et les ressources Azure à accéder à votre serveur et à votre base de données.

     - Si vous utilisez SQL Express pour le développement local, connectez-vous à l’instance nommée par défaut  `localhost\SQLExpress`.

  1. Créez ou utilisez une base de données existante.

     Vous devez disposer d’une base de données utilisable pour pouvoir configurer le fournisseur de stockage SQL.

  1. Vous pouvez à présent effectuer les [étapes de configuration d’un environnement SQL](#set-up-sql-environment) dans cet article.

- Pour le développement local, [Visual Studio Code](https://code.visualstudio.com/Download) installé sur votre ordinateur local.

  > [!NOTE]
  > Veillez à installer la [dernière version d’Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools/releases) afin de garantir la prise en charge de SQL. Pour cela, utilisez la version Microsoft Installer (MSI), à savoir `func-cli-X.X.XXXX-x*.msi`. Pour plus d’informations sur la configuration requise pour installer Visual Studio Code, passez en revue [Créer des workflows d’intégration avec Azure Logic Apps monolocataire (Standard) dans Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md).

<a name="set-up-sql-environment"></a>

## <a name="set-up-your-sql-environment"></a>Configurer votre environnement SQL

1. Avant de configurer le fournisseur de stockage SQL, effectuez les étapes nécessaires listées dans [Prérequis](#prerequisites).

1. Configurez les autorisations pour votre serveur SQL.

   À l’heure actuelle, le fournisseur de stockage SQL prend en charge l’authentification SQL dans les chaînes de connexion. Vous pouvez également utiliser l’authentification Windows pour le développement et les tests au niveau local. À ce stade, Azure Active Directory (Azure AD) et les identités managées ne sont pas pris en charge.

   Vous devez utiliser une identité autorisée à créer et à gérer des artefacts liés au workflow dans la base de données SQL cible. Par exemple, un administrateur dispose de toutes les autorisations nécessaires pour créer et gérer ces artefacts. La liste suivante décrit les artefacts que le runtime Azure Logic Apps monolocataire tente de créer avec la chaîne de connexion SQL que vous fournissez. Vérifiez que l’identité utilisée dans la chaîne de connexion SQL dispose des autorisations nécessaires pour créer les artefacts suivants :

   - Créez et supprimez les schémas suivants :  `dt`,  `dc` et  `dq`.
   - Ajoutez, modifiez et supprimez des tables dans ces schémas.
   - Ajoutez, modifiez et supprimez des types de tables définis par l’utilisateur dans ces schémas.

   Pour plus d’informations sur les autorisations ciblées, passez en revue les [autorisations SQL Server dans le moteur de base de données](/sql/relational-databases/security/permissions-database-engine).

1. Connectez-vous à SQL.

   - Vérifiez que votre base de données SQL autorise l’accès nécessaire pour le développement.

   - Si vous utilisez Azure SQL Database, tenez compte des exigences suivantes :

     - Pour le développement et les tests au niveau local, autorisez explicitement les connexions à partir de l’adresse IP de votre ordinateur local. Vous pouvez  [définir vos règles de pare-feu IP dans Azure SQL Server](../azure-sql/database/network-access-controls-overview.md#ip-firewall-rules).

     - Dans le [portail Azure](https://portal.azure.com), autorisez votre ressource d’application logique à accéder à la base de données SQL avec une chaîne de connexion fournie en  [autorisant les services Azure](../azure-sql/database/network-access-controls-overview.md#allow-azure-services).

     - Configurez tout autre  [contrôle d’accès réseau à la base de données SQL](../azure-sql/database/network-access-controls-overview.md)  en fonction des besoins de votre scénario.

   - Si vous utilisez Azure SQL Managed Instance, autorisez les services Azure (`logicapp`) à [se connecter à votre base de données SQL par le biais de points de terminaison publics sécurisés](../azure-sql/managed-instance/public-endpoint-overview.md).

<a name="set-up-sql-logic-app-creation-azure-portal"></a>

## <a name="set-up-sql-during-creation-in-the-azure-portal"></a>Configurer SQL durant la création dans le portail Azure

Quand vous créez votre application logique en utilisant le type de ressource **Application logique (Standard)** dans Azure, vous pouvez configurer SQL en tant que fournisseur de stockage.

1. Sur le [portail Azure](https://portal.azure.com) connectez-vous avec votre compte Azure.

1. Dans la zone de recherche du portail Azure, entrez `logic apps`, puis sélectionnez **Logic Apps**.

   ![Capture d’écran montrant la zone de recherche du portail Azure contenant le texte de recherche « applications logiques » et la catégorie « Application logique (Standard) » sélectionnée.](./media/set-up-sql-db-storage-single-tenant-standard-workflows/find-logic-app-resource-template.png)

1. Dans la page **Applications logiques**, sélectionnez **Ajouter**.

1. Sur la page **Créer une application logique**, sous l’onglet **De base**, fournissez les informations suivantes sur votre ressource d’application logique :

   | Propriété | Obligatoire | Valeur | Description |
   |----------|----------|-------|-------------|
   | **Abonnement** | Oui | <*Azure-subscription-name*> | Abonnement Azure à utiliser pour votre application logique. |
   | **Groupe de ressources** | Oui | <*nom-groupe-de-ressources-Azure*> | Groupe de ressources Azure dans lequel vous créez votre application logique et les ressources associées. Ce nom de ressource doit être unique d’une région à l’autre et peut uniquement contenir des lettres, des chiffres, des traits d’union ( **-** ), des traits de soulignement ( **_** ), des parenthèses [ **()** ] et des points ( **.** ). <p><p>Cet exemple crée un groupe de ressources nommé `Fabrikam-Workflows-RG`. |
   | **Type** | Oui | **Standard** | Ce type de ressource d’application logique s’exécute dans l’environnement Azure Logic Apps monolocataire et utilise le [modèle d’utilisation, de facturation et de tarification Standard](logic-apps-pricing.md#standard-pricing). |
   | **Nom de l’application logique** | Oui | <*logic-app-name*> | Nom à utiliser pour votre application logique. Ce nom de ressource doit être unique d’une région à l’autre et peut uniquement contenir des lettres, des chiffres, des traits d’union ( **-** ), des traits de soulignement ( **_** ), des parenthèses [ **()** ] et des points ( **.** ). <p><p>Cet exemple crée une application logique nommée `Fabrikam-Workflows`. <p><p>**Remarque** : Le nom de votre application logique obtient automatiquement le suffixe, `.azurewebsites.net`, car la ressource **Application logique (Standard)** repose sur le runtime Azure Logic Apps monolocataire qui utilise le modèle d’extensibilité d’Azure Functions et qui est hébergé en tant qu’extension sur le runtime Azure Functions. Azure Functions utilise la même convention d’affectation de noms d’application. |
   | **Publier** | Oui | <*deployment-environment*> | Destination de déploiement pour votre application logique. Par défaut, le **flux de travail** est sélectionné pour le déploiement sur un seul locataire Azure Logic Apps. Azure crée une ressource d’application logique vide dans laquelle vous devez ajouter votre premier flux de travail. <p><p>**Remarque** : actuellement, l’option de **conteneur Docker** requiert un [*emplacement personnalisé*](../azure-arc/kubernetes/conceptual-custom-locations.md) sur un cluster Kubernetes avec Azure Arc, que vous pouvez utiliser sur [Azure Arc avec Logic Apps (préversion)](azure-arc-enabled-logic-apps-overview.md). Les emplacements de ressources doivent tous être identiques pour votre application logique, votre emplacement personnalisé et votre cluster. |
   | **Région** | Oui | <*Azure-region*> | L’emplacement à utiliser pour la création de votre groupe de ressources et de vos ressources. Cet exemple déploie l’exemple d’application logique sur Azure et utilise **USA Ouest**. <p>– Si vous avez sélectionné **Conteneur Docker**, sélectionnez votre emplacement personnalisé. <p>– Pour déployer sur une ressource [ASEv3](../app-service/environment/overview.md), qui doit d’abord exister, sélectionnez cette ressource d’environnement dans la liste **Région**. |
   |||||

   L’exemple suivant montre la page **Créer une application logique** avec l’onglet **Informations de base** :

   ![Capture d’écran montrant le portail Azure et la page « Créer une application logique » avec l’onglet « Informations de base ».](./media/set-up-sql-db-storage-single-tenant-standard-workflows/create-logic-app-resource-portal.png)

1. Quand vous êtes prêt, sélectionnez **Suivant : Hébergement**. Sous l’onglet **Hébergement**, fournissez les informations suivantes sur la solution de stockage et le plan d’hébergement à utiliser pour votre application logique.

   | Propriété | Obligatoire | Valeur | Description |
   |----------|----------|-------|-------------|
   | **Type de stockage** | Oui | **SQL et Stockage Azure** | Type de stockage que vous souhaitez utiliser pour les artefacts et les données liés aux workflows. <p><p>- Si vous avez précédemment choisi un emplacement personnalisé comme région, sélectionnez **SQL**. <p><p>- Si vous avez précédemment choisi une région Azure ou un emplacement ASEv3, sélectionnez **SQL et Stockage Azure**. <p><p>**Remarque** : Si votre déploiemenent cible une région Azure, vous avez toujours besoin d’un compte Stockage Azure. Celui-ci est utilisé à une seule reprise pour héberger la configuration de l’application logique sur la plateforme Azure Logic Apps. La définition, l’état, l’historique des exécutions et les autres artefacts de runtime du workflow sont stockés dans votre base de données SQL. <p><p>Pour les déploiements sur un emplacement personnalisé hébergé sur un cluster Azure Arc, vous n’avez besoin que de SQL comme fournisseur de stockage. |
   | <bpt id="p1">**</bpt>Storage account<ept id="p1">**</ept> | Oui | <*Azure-storage-account-name*> | [Compte de stockage Azure](../storage/common/storage-account-overview.md) à utiliser pour les transactions de stockage. <p><p>Ce nom de ressource doit être unique d’une région à l’autre et comporter entre 3 et 24 caractères avec uniquement des chiffres et des lettres minuscules. Sélectionnez un compte existant ou créez un nouveau compte. <p><p>Cet exemple crée un compte de stockage nommé `fabrikamstorageacct`. |
   | **Chaîne de connexion SQL** | Oui | <*sql-connection-string*> | Votre chaîne de connexion SQL, qui prend actuellement en charge uniquement l’authentification SQL, et non OAuth ou l’authentification avec une identité managée. <p><p>**Remarque** : Veillez à entrer une chaîne de connexion correcte, car le portail Azure ne validera pas cette chaîne pour vous. |
   | **Type de plan** | Oui | <*hosting-plan*> | Plan d’hébergement à utiliser pour déployer votre application logique. <p><p>Pour plus d’informations, consultez [Plans d’hébergement et niveaux tarifaires](logic-apps-pricing.md#standard-pricing). |
   | **Plan Windows** | Oui | <*plan-name*> | Nom du plan à utiliser. Sélectionnez un plan existant ou fournissez ou nom de nouveau plan. <p><p>L’exemple suivant utilise le nom `Fabrikam-Service-Plan`. |
   | **SKU et taille** | Oui | <*pricing-tier*> | [Niveau tarifaire](../app-service/overview-hosting-plans.md) à utiliser pour votre application logique. Votre sélection affecte la tarification, le calcul, la mémoire et le stockage que votre application logique et vos flux de travail utilisent. <p><p>Pour modifier le niveau par défaut, sélectionnez **Modifier la taille**. Vous pouvez ensuite sélectionner d’autres niveaux tarifaires en fonction de la charge de travail dont vous avez besoin. <p><p>Pour plus d’informations, consultez [Plans d’hébergement et niveaux tarifaires](logic-apps-pricing.md#standard-pricing). |
   |||||

   L’exemple suivant montre la page **Créer une application logique** avec l’onglet **Hébergement** :

   ![Capture d’écran montrant le portail Azure et la page « Créer une application logique » avec l’onglet « Hébergement ».](./media/set-up-sql-db-storage-single-tenant-standard-workflows/set-up-sql-storage-details.png)

1. Effectuez les étapes de création restantes dans [Créer un workflow d’intégration avec Azure Logic Apps monolocataire (Standard) dans le portail Azure](create-single-tenant-workflows-azure-portal.md).

Quand vous avez terminé, votre nouvelle ressource d’application logique et votre workflow sont actifs dans Azure et utilisent votre base de données SQL comme fournisseur de stockage.

<a name="set-up-sql-local-dev"></a>

## <a name="set-up-sql-for-local-development-in-visual-studio-code"></a>Configurer SQL pour le développement local dans Visual Studio Code

Les étapes suivantes montrent comment configurer SQL en tant que fournisseur de stockage pour le développement et les tests au niveau local dans Visual Studio Code :

1. Configurez votre environnement de développement de façon à ce qu’il fonctionne avec Azure Logic Apps monolocataire.

   1. Tenez compte des [prérequis](create-single-tenant-workflows-visual-studio-code.md#prerequisites) pour utiliser Visual Studio Code avec l’extension Azure Logic Apps (Standard).

   1. [Configurez Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md#set-up) pour fonctionner avec l’extension Azure Logic Apps (Standard).

   1. Dans Visual Studio Code, [connectez-vous à votre compte Azure](create-single-tenant-workflows-visual-studio-code.md#connect-azure-account) et [créez un projet d’application logique vide](create-single-tenant-workflows-visual-studio-code.md#create-project).

1. Dans Visual Studio Code, ouvrez le volet Explorateur si cela n’est pas déjà fait.

1. Dans le volet Explorateur, à la racine de votre projet d’application logique, placez le pointeur de la souris sur une zone vide sous tous les fichiers et dossiers du projet, ouvrez le menu contextuel, puis sélectionnez **Utiliser le stockage SQL pour votre projet d’application logique**.

   ![Capture d’écran montrant Visual Studio Code, le volet Explorateur et le pointeur de la souris à la racine du projet dans une zone vide, le menu contextuel ouvert et l’option « Utiliser le stockage SQL pour votre projet d’application logique » sélectionnée.](./media/set-up-sql-db-storage-single-tenant-standard-workflows/use-sql-storage-logic-app-project.png)

1. À l’invite, entrez votre chaîne de connexion SQL. Vous pouvez choisir d’utiliser une instance SQL Express locale ou toute autre base de données SQL dont vous disposez.

   ![Capture d’écran montrant Visual Studio Code et l’invite permettant d’entrer la chaîne de connexion SQL.](./media/set-up-sql-db-storage-single-tenant-standard-workflows/enter-sql-connection-string.png)

   Après confirmation, Visual Studio Code crée le paramètre suivant dans le fichier **local.settings.json** de votre projet. Vous pouvez mettre à jour ce paramètre à tout moment.  

   ![Capture d’écran montrant Visual Studio Code, le projet d’application logique et le fichier « local.settings.json » ouvert avec le paramètre de chaîne de connexion SQL.](./media/set-up-sql-db-storage-single-tenant-standard-workflows/local-settings-json-file.png)

<a name="set-up-sql-logic-app-deployment-visual-studio-code"></a>

## <a name="set-up-sql-during-deployment-from-visual-studio-code"></a>Configurer SQL pendant le déploiement à partir de Visual Studio Code

Vous pouvez publier directement votre projet d’application logique sur Azure à partir de Visual Studio Code. Cette action déploie votre projet d’application logique en utilisant le type de ressource  **Application logique (Standard)**  .

- Si vous publiez le projet en tant que nouvelle ressource **Application logique (Standard)** dans Azure et que vous souhaitez utiliser SQL comme fournisseur de stockage, entrez votre chaîne de connexion SQL quand vous publiez votre application. Pour obtenir les étapes complètes, suivez [Configurer SQL pour le déploiement d’une nouvelle application logique](#deploy-new-logic-app-visual-studio-code).

- Si vous avez déjà configuré vos paramètres SQL, vous pouvez publier votre projet d’application logique sur une ressource **Application logique (Standard)** déjà déployée dans Azure. Cette action remplace votre application logique existante.

  > [!NOTE]
  > Une instance SQL Express locale ne fonctionne pas avec les applications logiques déployées et hébergées dans Azure.

<a name="deploy-new-logic-app-visual-studio-code"></a>

### <a name="set-up-sql-for-new-logic-app-standard-resource-deployment"></a>Configurer SQL pour le déploiement d’une nouvelle ressource Application logique (Standard)

1. Dans la barre d’activités de Visual Studio Code, sélectionnez l’icône Azure.

1. Dans la barre d’outils du volet  **Azure : Logic Apps (Standard)**  , sélectionnez  **Déployer sur Logic Apps**.

   ![Capture d’écran montrant le volet « Azure : Logic Apps (Standard) » et l’icône « Déployer sur Logic Apps » sélectionnée.](./media/set-up-sql-db-storage-single-tenant-standard-workflows/deploy-to-logic-app.png)

1. En cas d’invite, sélectionnez l’abonnement Azure à utiliser pour votre déploiement d’application logique.

1. Dans la liste ouverte par Visual Studio Code, veillez à sélectionner l’option Avancé pour **Créer une application logique (Standard) dans Azure - Avancé**. Sinon, vous n’êtes pas invité à configurer SQL.

   ![Capture d’écran montrant l’option de déploiement « Créer une application logique (Standard) dans Azure - Avancé » sélectionnée.](./media/set-up-sql-db-storage-single-tenant-standard-workflows/select-create-logic-app-advanced.png)

1. À l’invite, entrez un nom global unique pour votre nouvelle application logique, qui est le nom à utiliser pour la ressource  **Application logique (Standard)**  . Cet exemple utilise  `Fabrikam-Workflows-App`.

   ![Capture d’écran montrant l’invite permettant d’entrer un nom global unique pour votre application logique.](./media/set-up-sql-db-storage-single-tenant-standard-workflows/enter-logic-app-name.png)

1. Sélectionnez un emplacement pour votre application logique. Vous pouvez également commencer à taper du texte pour filtrer la liste.

   - Pour déployer sur Azure, sélectionnez la région Azure dans laquelle vous souhaitez effectuer le déploiement. Si vous avez précédemment créé une ressource App Service Environment v3 (ASEv3) et que vous souhaitez déployer à cet endroit, sélectionnez votre ASEv3.

   - Pour déployer sur Logic Apps avec Azure Arc, sélectionnez votre emplacement personnalisé précédemment configuré.

   L’exemple suivant montre la liste des emplacements filtrée sur **USA Ouest**.

   ![Capture d’écran montrant l’invite permettant de sélectionner un emplacement de déploiement avec les régions Azure disponibles et l’emplacement personnalisé pour les déploiements Azure Arc.](./media/set-up-sql-db-storage-single-tenant-standard-workflows/select-location.png)

1. Sélectionnez le type de plan d’hébergement pour votre nouvelle application logique.

   1. Si vous avez sélectionné un ASEv3 comme emplacement pour votre application, sélectionnez **Plan App Service**, puis sélectionnez votre ressource ASEv3. Sinon, sélectionnez **Workflow Standard**.

      ![Capture d’écran montrant l’invite permettant de sélectionner « Workflow Standard » ou « Plan App Service ».](./media/set-up-sql-db-storage-single-tenant-standard-workflows/select-hosting-plan.png)

   1. Créez un nom pour votre plan ou sélectionnez un plan existant.

      Cet exemple sélectionne  **Créer un plan App Service**, car aucun plan existant n’est disponible.

      ![Capture d’écran montrant l’invite permettant de créer un nom pour le plan d’hébergement avec l’option « Créer un plan App Service » sélectionnée.](./media/set-up-sql-db-storage-single-tenant-standard-workflows/create-app-service-plan.png)

1. Entrez un nom pour votre plan d’hébergement, puis sélectionnez un niveau tarifaire pour votre plan sélectionné.

   Pour plus d’informations, consultez [Plans d’hébergement et niveaux tarifaires](logic-apps-pricing.md#standard-pricing).

1. Quand vous êtes invité à entrer un groupe de ressources Azure, pour des performances optimales, sélectionnez le même groupe de ressources Azure que votre projet pour votre déploiement.

   > [!NOTE]
   > Bien que vous puissiez créer ou utiliser un groupe de ressources différent, cela peut affecter les performances. Si vous créez ou choisissez un groupe de ressources différent, mais annulez l’opération après l’affichage de l’invite de confirmation, votre déploiement est également annulé.

1. Quand vous êtes invité à sélectionner un compte de stockage pour votre application logique, choisissez l’une des options suivantes :

   - Si vous avez précédemment choisi un emplacement personnalisé, sélectionnez l’option **SQL**.

   - Si vous souhaitez déployer sur Azure, sélectionnez l’option **SQL et stockage Azure**.

     > [!NOTE]
     > Cette option est nécessaire uniquement pour les déploiements Azure. Dans Azure, le Stockage Azure est utilisé à une seule reprise pour héberger la configuration de l’application logique sur la plateforme Azure Logic Apps. L’état, l’historique des exécutions et les autres artefacts de runtime du workflow en cours sont stockés dans votre base de données SQL.
     >
     > Pour les déploiements sur un emplacement personnalisé hébergé sur un cluster Azure Arc, vous n’avez besoin que de SQL comme fournisseur de stockage.  

1. À l’invite, sélectionnez **Créer un compte de stockage** ou choisissez un compte de stockage existant le cas échéant.

   ![Capture d’écran montrant le volet « Azure : Logic Apps (standard) » et une invite à créer ou sélectionner un compte de stockage.](./media/set-up-sql-db-storage-single-tenant-standard-workflows/create-storage.png)

1. À l’invite de confirmation du stockage SQL, sélectionnez **Oui**. À l’invite de la chaîne de connexion, entrez votre chaîne de connexion SQL.

   > [!NOTE]
   > Veillez à entrer une chaîne de connexion correcte, car Visual Studio Code ne validera pas cette chaîne pour vous.

   ![Capture d’écran montrant Visual Studio Code et l’invite permettant d’entrer la chaîne de connexion SQL.](./media/set-up-sql-db-storage-single-tenant-standard-workflows/enter-sql-connection-string.png)

1. Effectuez les étapes de déploiement restantes dans [Publier dans une nouvelle ressource Application logique (Standard)](create-single-tenant-workflows-visual-studio-code.md#publish-new-logic-app).

Quand vous avez terminé, votre nouvelle ressource d’application logique et votre workflow sont actifs dans Azure et utilisent votre base de données SQL comme fournisseur de stockage.

## <a name="validate-deployments"></a>Valider les déploiements

Après avoir déployé votre ressource **Application logique (Standard)** sur Azure, vous pouvez vérifier si vos paramètres sont corrects :

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre ressource d’application logique.

1. Dans le menu de navigation de la ressource, sélectionnez **Configuration** sous **Paramètres**.

1. Dans le volet **Configuration**, sous **Paramètres de l’application**, recherchez le paramètre d’application **Workflows.Sql.ConnectionString**, puis vérifiez que votre chaîne de connexion SQL s’affiche et est correcte.

1. Dans votre environnement SQL, vérifiez que les tables SQL ont été créées avec un nom de schéma commençant par « dt » et « dq ».

Par exemple, la capture d’écran suivante montre les tables que le runtime Azure Logic Apps monolocataire a créé pour une ressource d’application logique avec un seul workflow :

![Capture d’écran montrant les tables SQL créées par le runtime Azure Logic Apps monolocataire.](./media/set-up-sql-db-storage-single-tenant-standard-workflows/runtime-created-tables-sql.png)

Le service Azure Logic Apps monolocataire crée également des types de tables définis par l’utilisateur. Par exemple, la capture d’écran suivante montre les types de tables définis par l’utilisateur que le runtime Azure Logic Apps monolocataire a créé pour une ressource d’application logique avec un seul workflow :

![Capture d’écran montrant les types de tables SQL définis par l’utilisateur créés par le runtime Azure Logic Apps monolocataire.](./media/set-up-sql-db-storage-single-tenant-standard-workflows/runtime-created-user-defined-tables-sql.png)

## <a name="next-steps"></a>Étapes suivantes

- [Créer des workflows d’intégration avec Azure Logic Apps monolocataire (Standard) dans Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md)
- [Modifier les paramètres de l’hôte et de l’application pour les applications logiques dans un service Azure Logic Apps à un seul locataire](edit-app-settings-host-settings.md)
