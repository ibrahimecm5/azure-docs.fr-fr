---
title: Créer des workflows avec Azure Logic Apps monolocataire (standard) dans le Portail Azure
description: Créez des workflows automatisés pour intégrer des applications, des données, des services et des systèmes avec le service Azure Logic Apps monolocataire (standard) dans le Portail Azure.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 10/05/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 507e7a97c0ec884580b0a29fd8a8691035221751
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131085977"
---
# <a name="create-an-integration-workflow-with-single-tenant-azure-logic-apps-standard-in-the-azure-portal"></a>Créer un workflow d’intégration avec Azure Logic Apps monolocataire (Standard) dans le Portail Azure

Cet article explique comment créer un exemple de workflows d’intégration automatisé qui s’exécute dans l’environnement Azure Logic Apps *monolocataire* à l’aide du type de ressource **Logic App (Standard)** et du Portail Azure. Ce type de ressource peut héberger plusieurs [flux de travail avec état et sans état](single-tenant-overview-compare.md#stateful-stateless). En outre, les workflows dans la même application logique et le même abonné s’exécutent dans le même processus que le runtime Azure Logic Apps reconçu, de sorte qu’ils partagent les mêmes ressources et offrent un meilleur niveau de performance. Pour plus d’informations sur l’offre d’Azure Logic Apps monolocataire, consultez l’[environnement monolocataire contre le multilocataire et les services d’intégration](single-tenant-overview-compare.md).

Bien que cet exemple de flux de travail soit basé sur le cloud et ne comporte que deux étapes, vous pouvez créer des flux de travail à partir de centaines d’opérations qui peuvent connecter un vaste éventail d’applications, de données, de services et de systèmes dans des environnements cloud, locaux et hybrides. L’exemple de flux de travail démarre avec le déclencheur de demande intégré et suit une action Office 365 Outlook. Le déclencheur crée un point de terminaison pouvant être appelé pour le flux de travail, et attend une demande HTTPS entrante en provenance d’un appelant quelconque. Lorsque le déclencheur reçoit une demande et se déclenche, l’action suivante s’exécute en envoyant un e-mail à l’adresse e-mail spécifiée avec des sorties sélectionnées à partir du déclencheur.

> [!TIP]
> Si vous n’avez pas de compte Office 365, vous pouvez utiliser toute autre action disponible pouvant envoyer des messages à partir de votre compte e-mail, par exemple, Outlook.com.
>
> Pour créer cet exemple de flux de travail en utilisant plutôt Visual Studio Code, suivez les étapes décrites dans [Créer des workflows d’intégration à l’aide d’Azure Logic Apps monolocataire et de Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md). 
> Les deux options permettent de développer, d’exécuter et de déployer des flux de travail d’application logique dans les mêmes genres d’environnements. 
> Toutefois, avec Visual Studio Code, vous pouvez développer, tester et exécuter *localement* des flux de travail dans votre environnement de développement.

![Capture d’écran montrant le portail Azure et le concepteur de flux de travail pour la ressource « Application logique (standard) ».](./media/create-single-tenant-workflows-azure-portal/azure-portal-logic-apps-overview.png)

À mesure que vous allez progresser, vous allez effectuer les tâches de haut niveau suivantes :

* Créez la ressource d’application logique et ajoutez un flux de travail [*avec état*](single-tenant-overview-compare.md#stateful-stateless) vide.
* Ajoutez un déclencheur et une action.
* Déclenchez une exécution de workflow.
* Affichez l’historique de déclencheur et d’exécution de votre application logique.
* Activez ou ouvrez Application Insights après le déploiement.
* Activez l’historique des exécutions pour les workflows sans état.

## <a name="prerequisites"></a>Prérequis

* Un compte et un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [vous inscrire pour obtenir un compte Azure gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un [compte de stockage Azure](../storage/common/storage-account-overview.md). Si vous n’en avez pas, vous pouvez créer un compte de stockage à l’avance ou lors de la création de l’application logique.

  > [!NOTE]
  > Le type de ressource **Application logique (standard)** est optimisé par Azure Functions et a des [exigences de stockage similaires à celles des applications de fonction](../azure-functions/storage-considerations.md). 
  > Les [flux de travail avec état](single-tenant-overview-compare.md#stateful-stateless) effectuent des transactions de stockage, telles que l’utilisation de files d’attente pour la planification et le stockage d’états de flux de travail dans des tables et des blobs. Ces transactions occasionnent des [frais de stockage](https://azure.microsoft.com/pricing/details/storage/). Pour plus d’informations sur la façon dont les flux de travail avec état stockent les données dans un stockage externe, consultez [Workflows avec état et sans état](single-tenant-overview-compare.md#stateful-stateless).

* Pour créer le même exemple de flux de travail que celui présenté dans cet article, vous avez besoin d’un compte de courrier Office 365 Outlook qui utilise un compte Microsoft professionnel ou scolaire pour se connecter.

  Si vous choisissez un [autre connecteur de courrier](/connectors/connector-reference/connector-reference-logicapps-connectors), tel que Outlook.com, vous pouvez toujours suivre l’exemple. Les étapes globales générales sont les mêmes. Toutefois, vos options peuvent différer de plusieurs manières. Par exemple, si vous utilisez le connecteur Outlook.com, utilisez votre compte Microsoft personnel au lieu de vous connecter.

* Pour tester l’exemple de flux de travail dans cet article, vous avez besoin d’un outil pouvant envoyer des appels au point de terminaison créé par le déclencheur de demande. Si vous n’avez pas d’outil de ce type, vous pouvez télécharger, installer et utiliser [Postman](https://www.postman.com/downloads/).

* Si vous créez vos ressources d’application logique avec des paramètres qui prennent en charge l’utilisation d’[Application Insights](../azure-monitor/app/app-insights-overview.md), vous pouvez choisir d’activer la journalisation et le suivi des diagnostics pour votre application logique. Vous pouvez le faire lorsque vous créez votre application logique ou après le déploiement. Vous devez disposer d’une instance d’Application Insights, mais vous pouvez créer cette ressource [à l’avance](../azure-monitor/app/create-workspace-resource.md), lorsque vous créez votre application logique, ou après le déploiement.

* Pour déployer votre ressource **Logic App (Standard)** sur un [environnement ASEv3 (App Service Environment v3)](../app-service/environment/overview.md), vous devez d’abord créer cette ressource d’environnement. Vous pouvez ensuite sélectionner cet environnement comme emplacement de déploiement quand vous créez votre ressource d’application logique. Pour plus d’informations, consultez [Types de ressources et environnements](single-tenant-overview-compare.md#resource-environment-differences) et [Créer un environnement App Service Environment](../app-service/environment/creation.md).

<a name="create-logic-app-resource"></a>

## <a name="create-the-logic-app-resource"></a>Créer la ressource d’application logique

1. Dans le [portail Azure](https://portal.azure.com) connectez-vous avec les informations d’identification de votre compte Azure.

1. Dans la zone de recherche du portail Azure, entrez `logic apps`, puis sélectionnez **Logic Apps**.

   ![Capture d’écran montrant la zone de recherche Portail Azure avec le terme de recherche « Logic Apps » et le groupe « Logic Apps » sélectionné.](./media/create-single-tenant-workflows-azure-portal/find-logic-app-resource-template.png)

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

   L’exemple suivant illustre la page **créer une application logique (standard)** :

   ![Capture d’écran montrant le portail Azure et la page « Créer une application logique ».](./media/create-single-tenant-workflows-azure-portal/create-logic-app-resource-portal.png)

1. Sous l’onglet **Hébergement**, fournissez les informations suivantes sur la solution de stockage et le plan d’hébergement à utiliser pour votre application logique.

   | Propriété | Obligatoire | Valeur | Description |
   |----------|----------|-------|-------------|
   | **Type de stockage** | Oui | - **SQL et Stockage Azure** <br>- **Stockage Azure** | Type de stockage que vous souhaitez utiliser pour les artefacts et les données liés aux workflows. <p><p>- Pour déployer uniquement sur Azure, sélectionnez **Stockage Azure**. <p><p>- Pour utiliser SQL comme stockage principal et stockage Azure en tant que stockage secondaire, sélectionnez **SQL et stockage Azure**, puis consultez [Configurer le stockage de base de données SQL pour les applications logiques Standard dans les applications logiques Azure à client unique](set-up-sql-db-storage-single-tenant-standard-workflows.md). <p><p>**Remarque** : Si votre déploiemenent cible une région Azure, vous avez toujours besoin d’un compte Stockage Azure. Celui-ci est utilisé à une seule reprise pour héberger la configuration de l’application logique sur la plateforme Azure Logic Apps. L’état, l’historique des exécutions et les autres artefacts de runtime du workflow en cours sont stockés dans votre base de données SQL. <p><p>Pour les déploiements sur un emplacement personnalisé hébergé sur un cluster Azure Arc, vous n’avez besoin que de SQL comme fournisseur de stockage. |
   | **Compte de stockage** | Oui | <*Azure-storage-account-name*> | [Compte de stockage Azure](../storage/common/storage-account-overview.md) à utiliser pour les transactions de stockage. <p><p>Ce nom de ressource doit être unique d’une région à l’autre et comporter entre 3 et 24 caractères avec uniquement des chiffres et des lettres minuscules. Sélectionnez un compte existant ou créez un nouveau compte. <p><p>Cet exemple crée un compte de stockage nommé `fabrikamstorageacct`. |
   | **Type de plan** | Oui | <*hosting-plan*> | Plan d’hébergement à utiliser pour déployer votre application logique. <p><p>Pour plus d’informations, consultez [Plans d’hébergement et niveaux tarifaires](logic-apps-pricing.md#standard-pricing). |
   | **Plan Windows** | Oui | <*plan-name*> | Nom du plan à utiliser. Sélectionnez un plan existant ou fournissez ou nom de nouveau plan. <p><p>L’exemple suivant utilise le nom `Fabrikam-Service-Plan`. |
   | **SKU et taille** | Oui | <*pricing-tier*> | [Niveau tarifaire](../app-service/overview-hosting-plans.md) à utiliser pour votre application logique. Votre sélection affecte la tarification, le calcul, la mémoire et le stockage que votre application logique et vos flux de travail utilisent. <p><p>Pour modifier le niveau par défaut, sélectionnez **Modifier la taille**. Vous pouvez ensuite sélectionner d’autres niveaux tarifaires en fonction de la charge de travail dont vous avez besoin. <p><p>Pour plus d’informations, consultez [Plans d’hébergement et niveaux tarifaires](logic-apps-pricing.md#standard-pricing). |
   |||||

1. Ensuite, si vos paramètres de création et de déploiement prennent en charge l’utilisation d’[Application Insights](../azure-monitor/app/app-insights-overview.md), vous pouvez activer en option la journalisation et le suivi des diagnostics pour votre application logique.

   1. Dans l’onglet **Surveillance**, sous **Application Insights**, définissez **Activer Application Insights** sur **Oui** si ce n’est pas déjà fait.

   1. Pour le paramètre **Application Insights**, sélectionnez une instance existante d’Application Insights ou, si vous souhaitez créer une nouvelle instance, sélectionnez **Créer nouveau** et indiquez le nom que vous souhaitez utiliser.

1. Une fois qu’Azure a validé les paramètres de votre application logique, sous l’onglet **Vérifier + créer**, sélectionnez **Créer**. Par exemple :

   ![Capture d’écran montrant le portail Azure et les nouveaux paramètres de ressource d’application logique.](./media/create-single-tenant-workflows-azure-portal/check-logic-app-resource-settings.png)

   > [!TIP]
   > Si vous recevez une erreur de validation après cette étape, ouvrez et passez en revue les détails de l’erreur. Par exemple, si votre région sélectionnée atteint un quota pour les ressources que vous essayez de créer, essayez éventuellement une autre région.

   Une fois qu’Azure a terminé le déploiement, votre application logique est automatiquement opérationnelle, mais elle ne fait rien encore car la ressource est vide du fait que vous n’avez pas encore ajouté de flux de travail.

1. Sur la page d’achèvement du déploiement, sélectionnez **Accéder à la ressource** afin de pouvoir ajouter une flux de travail vide.

   ![Capture d’écran montrant le portail Azure et le déploiement terminé.](./media/create-single-tenant-workflows-azure-portal/logic-app-completed-deployment.png)

<a name="add-workflow"></a>

## <a name="add-a-blank-workflow"></a>Ajouter un workflow vide

Après avoir créé votre ressource d’application logique vide, vous devez ajouter votre premier flux de travail.

1. Une fois qu’Azure a ouvert la ressource, dans le menu de votre application logique, sélectionnez **Workflow**. Dans la barre d’outils **Workflows**, sélectionnez **Ajouter**.

   ![Capture d’écran montrant le menu de la ressource d’application logique avec l’option « Workflows » sélectionnée, puis, dans la barre d’outils, « Ajouter » est sélectionné.](./media/create-single-tenant-workflows-azure-portal/logic-app-add-blank-workflow.png)

1. Quand le volet **Nouveau workflow** qui s’ouvre, entrez un nom pour votre flux de travail et choisissez le type d’état [**Avec état** ou **Sans état**](single-tenant-overview-compare.md#stateful-stateless). Sélectionnez **Créer** lorsque vous avez terminé.

   Cet exemple ajoute un workflow avec état vide nommé `Fabrikam-Stateful-Workflow`. Par défaut, le workflow est activé, mais ne fait rien tant que vous n’avez pas ajouté un déclencheur et des actions.

   ![Capture d’écran montrant le workflow avec état vide récemment ajouté « Fabrikam-Stateful-Workflow ».](./media/create-single-tenant-workflows-azure-portal/logic-app-blank-workflow-created.png)

1. Ensuite, ouvrez le workflow vide dans le concepteur afin d’ajouter un déclencheur et une action.

   1. Dans la liste des workflows, sélectionnez le workflow vide.

   1. Dans le menu des workflows, sous **Développeur**, sélectionnez **Concepteur**.

      Sur l’aire du concepteur, l’invite **Choisir une opération** s’affiche déjà et est sélectionnée par défaut de sorte que le volet **Ajouter un déclencheur** apparaît également ouvert.

      ![Capture d’écran montrant le concepteur de workflow ouvert avec l’option « Choisir une opération » sélectionnée sur l’aire du concepteur.](./media/create-single-tenant-workflows-azure-portal/opened-logic-app-designer-blank-workflow.png)

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-an-action"></a>Ajouter un déclencheur et une action

Cet exemple crée un workflow qui comporte les étapes suivantes :

* [Déclencheur de demande](../connectors/connectors-native-reqres.md) intégré, **Lors de la réception d’une requête HTTP**, qui reçoit les appels ou requêtes entrants, et crée un point de terminaison que d’autres services ou applications logiques peuvent appeler.

* [Action Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), **Envoyer un e-mail**.

### <a name="add-the-request-trigger"></a>Ajout du déclencheur Request

Avant de pouvoir ajouter un déclencheur à un workflow vide, assurez-vous que le concepteur de workflow est ouvert et que l’invite **Choisir une opération** est sélectionnée sur l’aire du concepteur.

1. À côté de l’aire du concepteur, dans le volet **Ajouter un déclencheur**, sous la zone de recherche **Choisir une opération**, vérifiez que l’onglet **Intégré** est sélectionné. Cet onglet affiche les déclencheurs qui s’exécutent en mode natif dans Azure Logic Apps.

1. Dans la zone de recherche **Choisir une opération**, entrez `when a http request`, puis sélectionnez le déclencheur de demande intégré nommé **Lors de la réception d’une requête HTTP**.

   ![Capture d’écran montrant le concepteur et le volet **Ajouter un déclencheur** avec le déclencheur « Lors de la réception d’une requête HTTP » sélectionné.](./media/create-single-tenant-workflows-azure-portal/find-request-trigger.png)

   Lorsque le déclencheur s’affiche sur le concepteur, le volet d’informations du déclencheur s’ouvre pour afficher les propriétés, les paramètres et d’autres actions du déclencheur.

   ![Capture d’écran montrant le concepteur avec le déclencheur « Lors de la réception d’une requête HTTP » sélectionné et le volet d’informations du déclencheur ouvert.](./media/create-single-tenant-workflows-azure-portal/request-trigger-added-to-designer.png)

   > [!TIP]
   > Si le volet d’informations ne s’affiche pas, assurez-vous que le déclencheur est sélectionné sur le concepteur.

1. Si vous devez supprimer un élément du concepteur, [procédez comme suit](#delete-from-designer).

1. Pour enregistrer votre travail, sélectionnez **Enregistrer** sur la barre d’outils du concepteur.

   Lorsque vous enregistrez un workflow pour la première fois et que celui-ci démarre avec un déclencheur Requête, le service Logic Apps génère automatiquement une URL pour un point de terminaison créé par le déclencheur Requête. Plus tard, lorsque vous testez votre workflow, vous envoyez une requête à cette URL, qui active le déclencheur et démarre l’exécution du workflow.

### <a name="add-the-office-365-outlook-action"></a>Ajouter l’action Office 365 Outlook

1. Dans le concepteur, sous le déclencheur que vous avez ajouté, sélectionnez le signe plus ( **+** ) > **Ajouter une action**.

   L’invite **Choisir une opération** s’affiche sur le concepteur, et le volet **Ajouter une action** se rouvre pour vous permettre de sélectionner l’action suivante.

   > [!NOTE]
   > Si le volet **Ajouter une action** affiche le message d’erreur « Impossible de lire la propriété "filtre" de non défini », enregistrez votre workflow, rechargez la page, rouvrez votre workflow, puis réessayez.

1. Dans le volet **Ajouter une action**, sous la zone de recherche **Choisir une opération**, sélectionnez **Azure**. Cet onglet affiche les connecteurs gérés disponibles hébergés dans Azure.

   > [!NOTE]
   > Si le volet **Ajouter une action** affiche le message d’erreur `The access token expiry UTC time '{token-expiration-date-time}' is earlier than current UTC time '{current-date-time}'`, enregistrez votre workflow, rechargez la page, rouvrez votre workflow, puis réessayez d’ajouter l’action.

   Cet exemple utilise l’action Office 365 Outlook intitulée **Envoyer un e-mail (V2)** .

   ![Capture d’écran montrant le concepteur et le volet **Ajouter une action** avec l’action Office 365 Outlook « Envoyer un e-mail » sélectionnée.](./media/create-single-tenant-workflows-azure-portal/find-send-email-action.png)

1. Dans le volet d’informations de l’action, dans l’onglet **Créer une connexion**, sélectionnez **Se connecter** pour pouvoir créer une connexion à votre compte de messagerie.

   ![Capture d’écran montrant le concepteur et le volet d’informations « Envoyer un e-mail (V2) » avec l’option « Se connecter » sélectionnée.](./media/create-single-tenant-workflows-azure-portal/send-email-action-sign-in.png)

1. Quand vous êtes invité à accéder à votre compte de courrier, connectez-vous avec les informations d’identification de celui-ci.

   > [!NOTE]
   > Si vous recevez le message d’erreur `Failed with error: 'The browser is closed.'. Please sign in again`, vérifiez si votre navigateur bloque les cookies tiers. Si ces cookies sont bloqués, essayez d’ajouter `https://portal.azure.com` à la liste des sites autorisés à utiliser des cookies. Si vous utilisez le mode Incognito, assurez-vous que les cookies tiers ne sont pas bloqués.
   > 
   > Si nécessaire, rechargez la page, ouvrez votre workflow, rajoutez l’action d’e-mail et essayez de créer la connexion.

   Une fois qu’Azure a créé la connexion, l’action **Envoyer un e-mail** apparaît sur le concepteur et est sélectionnée par défaut. Si l’action n’est pas sélectionnée, sélectionnez l’action afin que son volet d’informations soit également ouvert.

1. Dans le volet Détails de l’action, sous l’onglet **Paramètres**, fournissez les informations requises pour l’action, par exemple :

   ![Capture d’écran montrant le concepteur et le volet d’informations « Envoyer un e-mail » avec l’onglet « Paramètres » sélectionné.](./media/create-single-tenant-workflows-azure-portal/send-email-action-details.png)

   | Propriété | Obligatoire | Valeur | Description |
   |----------|----------|-------|-------------|
   | **To** | Oui | <*your-email-address*> | Destinataire de l’e-mail, qui peut être votre adresse de messagerie à des fins de test. Cet exemple utilise l’e-mail fictif `sophiaowen@fabrikam.com`. |
   | **Subject** | Oui | `An email from your example workflow` | L’objet de l’e-mail |
   | **Corps** | Oui | `Hello from your example workflow!` | Contenu du corps de l’e-mail. |
   ||||

   > [!NOTE]
   > Lorsque vous apportez des modifications dans le volet d’informations sous l’onglet **Paramètres**, **Résultat statique** ou **Exécuter après**, veillez à sélectionner **Terminé** pour valider ces modifications avant de changer d’onglet ou de passer au concepteur. Dans le cas contraire, le concepteur ne conservera pas vos modifications.

1. Enregistrez votre travail. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**.

1. Si votre environnement présente des exigences réseau strictes ou des pare-feu limitant le trafic, vous devez définir des autorisations pour les connexions de déclencheur ou d’action présentes dans votre workflow. Pour trouver les noms de domaine complets, consultez [Rechercher les noms de domaine pour l’accès au pare-feu](#firewall-setup).

   Sinon, pour tester votre workflow, [déclenchez manuellement une exécution](#trigger-workflow).

<a name="firewall-setup"></a>

## <a name="find-domain-names-for-firewall-access"></a>Rechercher les noms de domaine pour l’accès au pare-feu

Avant de déployer votre application logique et d’exécuter votre workflow dans le portail Azure, si votre environnement présente des exigences réseau strictes ou des pare-feu limitant le trafic, vous devez définir des autorisations pour les connexions de déclencheur ou d’action présentes dans votre application logique.

Pour trouver les adresses IP entrantes et sortantes qu’utilisent votre application logique et vos flux de travail, procédez comme suit :

1. Dans le menu de votre application logique, sous **Paramètres**, sélectionnez **Mise en réseau (préversion)** .

1. Sur la page Mise en réseau, recherchez et examinez les sections **Trafic entrant** et **Trafic sortant** .

Pour trouver les noms de domaine complets (FQDN) de ces connexions, procédez comme suit :

1. Dans le menu de votre application logique, sous **Workflows**, sélectionnez **Connexions**. Sous l’onglet **Connexions d’API**, sélectionnez le nom de ressource de la connexion, par exemple :

   ![Capture d’écran montrant le portail Azure et le menu de l’application logique avec « Connexions » et le nom de ressource de la connexion « office365 » sélectionnés.](./media/create-single-tenant-workflows-azure-portal/logic-app-connections.png)

1. Développez suffisamment votre navigateur pour afficher **Vue JSON** dans l’angle supérieur droit, puis sélectionnez **Vue JSON**.

   ![Capture d’écran montrant le portail Azure et le volet Connexion d’Api avec «Vue JSON» sélectionnée.](./media/create-single-tenant-workflows-azure-portal/logic-app-connection-view-json.png)

1. Copiez et enregistrez la valeur de la propriété `connectionRuntimeUrl` dans un lieu sûr pour configurer votre pare-feu à l’aide de ces informations.

   ![Capture d’écran montrant la valeur de la propriété « connectionRuntimeUrl » sélectionnée.](./media/create-single-tenant-workflows-azure-portal/logic-app-connection-runtime-url.png)

1. Pour chaque connexion, répétez les étapes qui conviennent.

<a name="trigger-workflow"></a>

## <a name="trigger-the-workflow"></a>Déclencher le workflow

Dans cet exemple, le workflow s’exécute lorsque le déclencheur Requête reçoit une demande entrante, qui est envoyée à l’URL du point de terminaison créé par le déclencheur. Lorsque vous avez enregistré le workflow pour la première fois, le service Logic Apps a automatiquement généré cette URL. Ainsi, avant de pouvoir envoyer cette demande pour déclencher le workflow, vous devez trouver cette URL.

1. Dans le concepteur de workflow, sélectionnez le déclencheur Requête intitulé **Lors de la réception d’une requête HTTP**.

1. Une fois que le volet d’informations s’ouvre, sous l’onglet **Paramètres**, recherchez la propriété **HTTP POST URL**. Pour copier l’URL générée, sélectionnez **Copier l’URL** (icône de copie de fichier) et enregistrez l’URL ailleurs pour le moment. L’URL suit ce format :

   `http://<logic-app-name>.azurewebsites.net:443/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

   ![Capture d’écran montrant le concepteur avec le déclencheur Requête et l’URL du point de terminaison dans la propriété « HTTP POST URL ».](./media/create-single-tenant-workflows-azure-portal/find-request-trigger-url.png)

   Pour cet exemple, l’URL se présente comme suit :

   `https://fabrikam-workflows.azurewebsites.net:443/api/Fabrikam-Stateful-Workflow/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=xxxxxXXXXxxxxxXXXXxxxXXXXxxxxXXXX`

   > [!TIP]
   > Vous pouvez également trouver l’URL du point de terminaison dans le volet **Vue d’ensemble** de votre application logique dans la propriété **URL du workflow**.
   >
   > 1. Dans le menu des ressources, sélectionnez **Vue d’ensemble**.
   > 1. Dans le volet **Vue d’ensemble**, recherchez la propriété **URL du workflow**.
   > 1. Pour copier l’URL du point de terminaison, déplacez votre pointeur sur la fin du texte de l’URL du point de terminaison et sélectionnez **Copier dans le presse-papiers** (icône de copie de fichier).

1. Pour tester l’URL en envoyant une demande, ouvrez [Postman](https://www.postman.com/downloads/) ou l’outil de votre choix pour créer et envoyer des demandes.

   Cet exemple se poursuit en utilisant Postman. Pour plus d’informations, consultez [Prise en main de Postman](https://learning.postman.com/docs/getting-started/introduction/).

   1. Dans la barre d’outils de Postman, sélectionnez **Nouveau**.

      ![Capture d’écran montrant Postman avec le bouton Nouveau sélectionné.](./media/create-single-tenant-workflows-azure-portal/postman-create-request.png)

   1. Dans le volet **Créer**, sous **Blocs de construction**, sélectionnez **Requête**.

   1. Dans la fenêtre **Enregistrer la requête**, sous **Nom de la requête**, entrez un nom pour la requête, par exemple `Test workflow trigger`.

   1. Sous **Sélectionner une collection ou un dossier dans lesquels enregistrer**, sélectionnez **Créer une collection**.

   1. Sous **Toutes les collections**, fournissez un nom pour la collection à créer afin d’organiser vos demandes, appuyez sur Entrée, puis sélectionnez **Enregistrer dans <*nom-collection*>** . Cet exemple utilise `Logic Apps requests` comme nom de collection.

      Dans l’application Postman, la volet de la requête s’ouvre pour que vous puissiez envoyer une requête à l’URL du point de terminaison du déclencheur de Requête.

      ![Capture d’écran montrant Postman avec le volet de demande ouvert](./media/create-single-tenant-workflows-azure-portal/postman-request-pane.png)

   1. Dans le volet de demande, dans la zone d’adresse à côté de la liste de méthodes qui affiche actuellement la méthode de demande par défaut **GET**, collez l’URL précédemment copiée, puis sélectionnez **Envoyer**.

      ![Capture d’écran montrant Postman et l’URL du point de terminaison dans la zone d’adresse avec le bouton Envoyer sélectionné.](./media/create-single-tenant-workflows-azure-portal/postman-test-endpoint-url.png)

      Lorsque le déclencheur est activé, l’exemple de workflow s’exécute et envoie un e-mail qui ressemble à l’exemple suivant :

      ![Capture d’écran montrant l’e-mail Outlook décrit dans l’exemple.](./media/create-single-tenant-workflows-azure-portal/workflow-app-result-email.png)

<a name="view-run-history"></a>

## <a name="review-run-history"></a>Examiner l’historique des exécutions

Pour un workflow avec état, après chaque exécution du workflow, vous pouvez afficher l’historique des exécutions, y compris l’état de l’exécution globale, du déclencheur et de chaque action, ainsi que leurs entrées et les sorties. Dans le portail Azure, l’historique des exécutions et les historiques des déclencheurs s’affichent au niveau du workflow, et non au niveau de l’application logique. Pour passer en revue les historiques des déclencheurs en dehors du contexte de l’historique des exécutions, consultez [Examiner les historiques des déclencheurs](#view-trigger-histories).

1. Dans le portail Azure, dans le menu du flux de travail, sélectionnez **Vue d’ensemble**.

1. Dans le volet **Vue d’ensemble**, sélectionnez **Historique des exécutions** pour afficher l’historique d’exécution de ce flux de travail.

   ![Capture d’écran montrant le volet « Vue d’ensemble » du flux de travail avec l’option « Historique des exécutions » sélectionnée.](./media/create-single-tenant-workflows-azure-portal/find-run-history.png)

   > [!TIP]
   > Si l’état d’exécution le plus récent ne s’affiche pas, dans la barre d’outils du volet **Vue d’ensemble**, sélectionnez **Actualiser**. Aucune exécution ne se produit pour un déclencheur ignoré en raison de critères non respectés ou d’une absence de données.

   | État de l’exécution | Description |
   |------------|-------------|
   | **Abandonné** | L’exécution s’est arrêtée ou ne s’est pas terminée en raison de problèmes externes, par exemple, une panne système ou un abonnement Azure expiré. |
   | **Annulé** | L’exécution a été déclenchée et a démarré, mais a reçu une demande d’annulation. |
   | **Échec** | Au moins une action de l’exécution a échoué. Aucune action subséquente n’était configurée dans le flux de travail pour gérer l’échec. |
   | **Exécution** | L’exécution a été déclenchée et est en cours, mais cet état peut également apparaître pour une exécution limitée en raison de [limites d’action](logic-apps-limits-and-config.md) ou du [plan de tarification actuel](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>**Conseil** : Si vous configurez la [journalisation des diagnostics](monitor-logic-apps-log-analytics.md), vous pouvez obtenir des informations sur tous les événements de limitation qui se produisent. |
   | **Réussi** | L’exécution a réussi. Si une action a échoué, une action subséquente dans le flux de travail a géré cet échec. |
   | **Délai dépassé** | L’exécution a expiré parce qu’elle a dépassé la limite de durée d’exécution qui est contrôlée par le [paramètre **Run history retention in days** (rétention de l’historique d’exécution en jours)](logic-apps-limits-and-config.md#run-duration-retention-limits). La durée d’une exécution est calculée en utilisant l’heure de début et la limite de durée d’exécution à cette heure. <p><p>**Remarque** : Si la durée de l’exécution dépasse aussi la *limite actuelle de rétention de l’historique d’exécution*, qui est également contrôlée par le paramètre [**Run history retention in days** (Rétention de l’historique d’exécution en jours)](logic-apps-limits-and-config.md#run-duration-retention-limits), l’exécution est effacée de l’historique d’exécution par un travail de nettoyage quotidien. Que l’exécution expire ou se termine, la période de rétention est toujours calculée sur la base de l’heure de début de l’exécution et de la limite de rétention *actuelle*. Par conséquent, si vous réduisez la limite de durée pour une exécution en cours, l’exécution expire. Toutefois, l’exécution reste dans l’historique des exécutions ou est effacée de celui-ci selon que la durée de l’exécution a ou non dépassé la limite de rétention. |
   | **En attente** | L’exécution n’a pas démarré ou est suspendue, par exemple, en raison d’une instance de flux de travail antérieure qui est toujours en cours d’exécution. |
   |||

1. Pour passer en revue l’état de chaque étape d’une exécution, sélectionnez l’exécution que vous souhaitez examiner.

   L’affichage détaillé de l’exécution s’ouvre et indique l’état de chaque étape de l’exécution.

   ![Capture d’écran montrant l’affichage détaillé de l’exécution avec l’état de chaque étape du workflow.](./media/create-single-tenant-workflows-azure-portal/review-run-details.png)

   Voici les états possibles que chaque étape du flux de travail :

   | État d’action | Description |
   |---------------|-------------|
   | **Abandonné** | L’action a été arrêtée ou ne s’est pas terminée en raison de problèmes externes, par exemple, une panne du système ou l’expiration d’un abonnement Azure. |
   | **Annulé** | L’action était en cours d’exécution mais a reçu une demande d’annulation. |
   | **Échec** | L’action a échoué. |
   | **Exécution** | L’action est en cours d’exécution. |
   | **Ignoré** | L’action a été ignorée, car les conditions `runAfter` n’étaient pas remplies. Par exemple, une action précédente a échoué. Chaque action possède un objet `runAfter` dans lequel vous pouvez configurer les conditions nécessaires pour que l’action en cours puisse s’exécuter. |
   | **Réussi** | L’action a réussi. |
   | **Réussie avec de nouvelles tentatives** | L’action a réussi, mais uniquement au bout de deux tentatives ou plus. Pour consulter l’historique des tentatives, dans la vue des détails de l’historique des exécutions, sélectionnez cette action afin de voir les entrées et sorties. |
   | **Délai dépassé** | L’action a été arrêtée en raison du délai d’expiration spécifié par les paramètres de cette action. |
   | **En attente** | S’applique à une action de webhook qui attend une demande entrante d’un appelant. |
   |||

   [aborted-icon]: ./media/create-single-tenant-workflows-azure-portal/aborted.png
   [cancelled-icon]: ./media/create-single-tenant-workflows-azure-portal/cancelled.png
   [failed-icon]: ./media/create-single-tenant-workflows-azure-portal/failed.png
   [running-icon]: ./media/create-single-tenant-workflows-azure-portal/running.png
   [skipped-icon]: ./media/create-single-tenant-workflows-azure-portal/skipped.png
   [succeeded-icon]: ./media/create-single-tenant-workflows-azure-portal/succeeded.png
   [succeeded-with-retries-icon]: ./media/create-single-tenant-workflows-azure-portal/succeeded-with-retries.png
   [timed-out-icon]: ./media/create-single-tenant-workflows-azure-portal/timed-out.png
   [waiting-icon]: ./media/create-single-tenant-workflows-azure-portal/waiting.png

1. Pour examiner les entrées et les sorties d’une étape spécifique, sélectionnez cette dernière.

   ![Capture d’écran montrant les entrées et sorties de l’action « Envoyer un e-mail » sélectionnée.](./media/create-single-tenant-workflows-azure-portal/review-step-inputs-outputs.png)

1. Pour examiner plus en détail les entrées et sorties brutes de cette étape, sélectionnez **Afficher les entrées brutes** ou **Afficher les sorties brutes**.

<a name="view-trigger-histories"></a>

## <a name="review-trigger-histories"></a>Examiner les historiques des déclencheurs

Pour un workflow avec état, vous pouvez consulter l’historique des déclencheurs pour chaque exécution, notamment l’état des déclencheurs ainsi que les entrées et sorties, en dehors du [contexte de l’historique des exécutions](#view-run-history). Dans le portail Azure, l’historique des déclencheurs et l’historique des exécutions s’affichent au niveau du workflow, et non au niveau de l’application logique. Pour trouver ces données d’historique, suivez ces étapes :

1. Dans le portail Azure, dans le menu du flux de travail, sélectionnez **Vue d’ensemble**.

1. Dans la page **Vue d’ensemble**, sélectionnez **Historiques des déclencheurs**.

   Le volet **Historiques des déclencheurs** affiche les historiques des déclencheurs pour les exécutions de votre workflow.

1. Pour consulter un historique des déclencheurs spécifique, sélectionnez l’ID de l’exécution correspondante.

<a name="enable-open-application-insights"></a>

## <a name="enable-or-open-application-insights-after-deployment"></a>Activer ou ouvrir Application Insights après le déploiement

Pendant l’exécution du flux de travail, votre application logique émet des données de télémétrie ainsi que d’autres événements. Vous pouvez utiliser ces données de télémétrie pour obtenir une meilleure visibilité sur l’exécution de votre workflow et sur les différentes façons dont fonctionne le runtime de Logic Apps. Vous pouvez surveiller votre workflow à l’aide d’[Application Insights](../azure-monitor/app/app-insights-overview.md), qui fournit des données de télémétrie en quasi-temps réel (métriques en temps réel). Cette capacité vous permet d’examiner plus facilement les défaillances et les problèmes de performances lorsque vous utilisez ces données pour diagnostiquer des problèmes, configurer des alertes et créer des graphiques.

Si les paramètres de création et de déploiement de votre application logique prennent en charge l’utilisation d’[Application Insights](../azure-monitor/app/app-insights-overview.md), vous pouvez activer en option la journalisation et le suivi des diagnostics pour votre application logique. Vous pouvez le faire lorsque vous créez votre application logique dans le portail Azure ou après le déploiement. Vous devez disposer d’une instance d’Application Insights, mais vous pouvez créer cette ressource [à l’avance](../azure-monitor/app/create-workspace-resource.md), lorsque vous créez votre application logique, ou après le déploiement.

Pour activer Application Insights sur une application logique déployée ou pour ouvrir le tableau de bord Application Insights si la fonctionnalité est déjà activée, procédez comme suit :

1. Dans le portail Azure, recherchez votre application logique déployée.

1. Dans le menu de l’application logique, sous **Paramètres**, sélectionnez **Application Insights**.

1. Si la fonctionnalité Application Insights n’est pas activée, dans le volet **Application Insights**, sélectionnez **Activer Application Insights**. Une fois le volet mis à jour, dans la partie inférieure, sélectionnez **Appliquer** > **Oui**.

   Si la fonctionnalité Application Insights est activée, dans le volet **Application Insights**, sélectionnez **Afficher les données Application Insights**.

Après l’ouverture d’Application Insights, vous pouvez passer en revue les différentes métriques de votre application logique. Pour plus d’informations, consultez les rubriques suivantes :

* [Azure Logic Apps s’exécutant partout – Superviser avec Application Insights (première partie)](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/1877849)
* [Azure Logic Apps s’exécutant partout – Superviser avec Application Insights (deuxième partie)](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/2003332)

<a name="enable-run-history-stateless"></a>

## <a name="enable-run-history-for-stateless-workflows"></a>Activer l’historique des exécutions pour les workflows sans état

Pour déboguer plus facilement un workflow sans état, vous pouvez activer l’historique des exécutions du workflow en question, puis le désactiver lorsque vous avez terminé. Procédez comme suit pour le portail Azure ou, si vous travaillez dans Visual Studio Code, consultez [Créer des workflows avec état et sans état dans Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md#enable-run-history-stateless).

1. Dans le [Portail Azure](https://portal.azure.com), ouvrez votre ressource **Application logique (standard)** .

1. Dans le menu de l’application logique, sous **Paramètres**, sélectionnez **Configuration**.

1. Sous l’onglet **Paramètre d’application**, sélectionnez **Nouveau paramètre d’application**.

1. Dans le volet **Ajouter/modifier un paramètre d’application**, dans la zone **Nom**, entrez le nom de l’option d’opération : 

   `Workflows.{yourWorkflowName}.OperationOptions`

1. Dans la zone **Valeur**, entrez la valeur suivante : `WithStatelessRunHistory`

   Par exemple :

   ![Capture d’écran montrant le portail Azure et la ressource Application logique (standard) avec le volet « Configuration » > « Nouveau paramètre d’application » > « Ajouter/modifier un paramètre d’application » ouvert et l’option « Workflows.{yourWorkflowName}.OperationOptions » définie sur « WithStatelessRunHistory ».](./media/create-single-tenant-workflows-azure-portal/stateless-operation-options-run-history.png)

1. Pour terminer cette tâche, sélectionnez **OK**. Dans la barre d’outils du volet **Configuration**, sélectionnez **Enregistrer**.

1. Pour désactiver l’historique des exécutions lorsque vous avez terminé, supprimez la propriété `Workflows.{yourWorkflowName}.OperationOptions` et sa valeur, ou définissez-la sur `None`.

<a name="delete-from-designer"></a>

## <a name="delete-items-from-the-designer"></a>Supprimer des éléments du concepteur

Pour supprimer un élément de votre workflow à partir du concepteur, procédez de l’une des façons suivantes :

* Sélectionnez l’élément, ouvrez le menu contextuel de l’élément (Maj+F10), puis sélectionnez **Supprimer**. Pour confirmer, sélectionnez **OK**.

* Sélectionnez l’élément, puis appuyez sur la touche Suppr. Pour confirmer, sélectionnez **OK**.

* Sélectionnez l’élément afin que le volet d’informations le concernant s’ouvre. Dans l’angle supérieur droit du volet, ouvrez le menu de sélection ( **…** ), puis sélectionnez **Supprimer**. Pour confirmer, sélectionnez **OK**.

  ![Capture d’écran montrant un élément sélectionné dans le concepteur avec le volet d’informations ouvert, ainsi que le bouton de sélection et la commande « Supprimer » sélectionnés.](./media/create-single-tenant-workflows-azure-portal/delete-item-from-designer.png)

  > [!TIP]
  > Si le menu de sélection n’est pas visible, développez la fenêtre de votre navigateur suffisamment pour que le volet d’informations affiche le bouton de sélection ( **…** ) dans le coin supérieur droit.

<a name="restart-stop-start"></a>

## <a name="restart-stop-or-start-logic-apps"></a>Redémarrer, arrêter ou démarrer des applications logiques

Vous pouvez arrêter ou démarrer une [application logique](#restart-stop-start-single-logic-app) ou [plusieurs applications logiques simultanément](#stop-start-multiple-logic-apps). Vous pouvez également redémarrer une application logique spécifique sans l’arrêter au préalable. Votre application logique à locataire unique peut inclure plusieurs flux de travail. Vous pouvez donc arrêter l’application logique entière ou [désactiver uniquement des workflows](#disable-enable-workflows).

> [!NOTE]
> Les opérations d’arrêt de l’application logique et de désactivation de workflows ont des effets différents. Pour plus d’informations, consultez [Considérations relatives à l’arrêt des applications logiques](#considerations-stop-logic-apps) et [Considérations relatives à la désactivation des workflows](#disable-enable-workflows).

<a name="considerations-stop-logic-apps"></a>

### <a name="considerations-for-stopping-logic-apps"></a>Considérations relatives à l’arrêt des applications logiques

L’arrêt d’une application logique affecte les instances de workflow de différentes manières :

* Le service Azure Logic Apps annule immédiatement toutes les exécutions en cours et en attente.

* Le service Azure Logic Apps ne crée ni n’exécute pas de nouvelles instances de flux de travail.

* Les déclencheurs ne sont pas activés la prochaine fois que les conditions sont remplies. Cependant, les états des déclencheurs mémorisent les points auxquels l’application logique a été arrêtée. Ainsi, si vous redémarrez l’application logique, les déclencheurs s’activent pour tous les éléments non traités depuis la dernière exécution.

  Pour arrêter le déclenchement de chaque workflow pour les éléments non traités depuis la dernière exécution, effacez l’état du déclencheur avant de redémarrer l’application logique en procédant comme suit :

  1. Dans le Portail Azure, ouvrez votre application logique.
  1. Dans le menu de l’application logique, sous **Workflows**, sélectionnez **Workflows**.
  1. Ouvrez un workflow, puis modifiez une partie du déclencheur de ce workflow.
  1. Enregistrez vos modifications. Cette étape réinitialise l’état actuel du déclencheur.
  1. Répétez cette opération pour chaque workflow.
  1. Quand vous avez terminé, [redémarrez votre application logique](#restart-stop-start-single-logic-app).

<a name="restart-stop-start-single-logic-app"></a>

### <a name="restart-stop-or-start-a-single-logic-app"></a>Redémarrer, arrêter ou démarrer une application logique spécifique

1. Dans le Portail Azure, ouvrez votre application logique.

1. Dans le menu de l’application logique, sélectionnez **Vue d’ensemble**.

   * Pour redémarrer une application logique sans l’arrêter, dans la barre d’outils du volet Vue d’ensemble, sélectionnez **Redémarrer**.
   * Pour arrêter une application logique en cours d’exécution, dans la barre d’outils du volet Vue d’ensemble, sélectionnez **Arrêter**. Confirmez votre sélection.
   * Pour démarrer une application logique arrêtée, dans la barre d’outils du volet Vue d’ensemble, sélectionnez **Démarrer**.

   > [!NOTE]
   > Si votre application logique est déjà arrêtée, vous voyez uniquement l’option **Démarrer**. Si votre application logique est déjà en cours d’exécution, vous voyez uniquement l’option **Arrêter**.
   > Vous pouvez redémarrer votre application logique à tout moment.

1. Pour vérifier si votre opération a réussi ou échoué, dans la barre d’outils Azure principale, ouvrez la liste **Notifications** (icône de cloche).

<a name="stop-start-multiple-logic-apps"></a>

### <a name="stop-or-start-multiple-logic-apps"></a>Arrêter ou démarrer plusieurs applications logiques

Vous pouvez arrêter ou démarrer plusieurs applications logiques en même temps, mais vous ne pouvez pas redémarrer plusieurs applications logiques sans les arrêter au préalable.

1. Dans la zone de recherche principale du portail Azure, entrez `logic apps`, puis sélectionnez **Logic Apps**.

1. Dans la page **Logic Apps**, passez en revue la colonne **État** de l’application logique.

1. Dans la colonne des cases à cocher, sélectionnez les applications logiques que vous souhaitez arrêter ou démarrer.

   * Pour arrêter les applications logiques en cours d’exécution sélectionnées, dans la barre d’outils du volet Vue d’ensemble, sélectionnez **Désactiver/Arrêter**. Confirmez votre sélection.
   * Pour démarrer les applications logiques arrêtées sélectionnées, sélectionnez **Activer/démarrer** dans la barre d’outils du volet Vue d’ensemble.

1. Pour vérifier si votre opération a réussi ou échoué, dans la barre d’outils Azure principale, ouvrez la liste **Notifications** (icône de cloche).

<a name="disable-enable-workflows"></a>

## <a name="disable-or-enable-workflows"></a>Désactiver ou activer des workflows

Pour empêcher le déclencheur de s’activer la prochaine fois que la condition de déclenchement est remplie, désactivez votre workflow. Vous pouvez désactiver ou activer un workflow spécifique, mais vous ne pouvez pas activer ni désactiver plusieurs workflows simultanément. Voici de quelles manières la désactivation d’un workflow affecte les instances de workflow :

* Le service Azure Logic Apps continue toutes les exécutions en cours et en attente jusqu’à ce qu’elles se terminent. En fonction du volume ou du backlog, ce processus peut prendre du temps.

* Le service Azure Logic Apps ne crée ni n’exécute pas de nouvelles instances de flux de travail.

* Le déclencheur ne se déclenche pas la prochaine fois que ses conditions sont remplies. Toutefois, l’état du déclencheur mémorise le point auquel le workflow a été arrêté. Ainsi, si vous réactivez le workflow, le déclencheur s’active pour tous les éléments non traités depuis la dernière exécution.

  Pour empêcher le déclencheur de se mettre en œuvre pour les éléments non traités depuis la dernière exécution, effacez l’état du déclencheur avant de réactiver le workflow :

  1. Dans le workflow, modifiez toute partie du déclencheur du workflow.
  1. Enregistrez vos modifications. Cette étape réinitialise l’état actuel de votre déclencheur.
  1. [Réactivez votre workflow](#disable-enable-workflows).

> [!NOTE]
> Les opérations de désactivation de workflow et d’arrêt de l’application logique ont des effets différents. Pour plus d’informations, consultez [Considérations relatives à l’arrêt des applications logiques](#considerations-stop-logic-apps).

<a name="disable-workflow"></a>

### <a name="disable-workflow"></a>Désactiver le workflow

1. Dans le menu de l’application logique, sous **Workflows**, sélectionnez **Workflows**. Dans la colonne des cases à cocher, sélectionnez le workflow à désactiver.

1. Dans la barre d’outils du volet **Workflows**, sélectionnez **Désactiver**.

1. Pour vérifier si votre opération a réussi ou échoué, dans la barre d’outils Azure principale, ouvrez la liste **Notifications** (icône de cloche).

<a name="enable-workflow"></a>

### <a name="enable-workflow"></a>Activer le workflow

1. Dans le menu de l’application logique, sous **Workflows**, sélectionnez **Workflows**. Dans la colonne des cases à cocher, sélectionnez le workflow à activer.

1. Dans la barre d’outils du volet **Workflows**, sélectionnez **Activer**.

1. Pour vérifier si votre opération a réussi ou échoué, dans la barre d’outils Azure principale, ouvrez la liste **Notifications** (icône de cloche).

<a name="delete"></a>

## <a name="delete-logic-apps-or-workflows"></a>Supprimer des applications logiques ou des workflows

Vous pouvez [supprimer une application logique spécifique ou plusieurs applications logiques simultanément](#delete-logic-apps). Votre application logique à locataire unique peut inclure plusieurs flux de travail. Vous pouvez donc supprimer l’application logique entière ou [supprimer uniquement des workflows](#delete-workflows).

<a name="delete-logic-apps"></a>

### <a name="delete-logic-apps"></a>Supprimer des applications logiques

La suppression d’une application logique entraîne l’annulation immédiate des exécutions en cours et en attente, mais pas l’exécution de tâches de nettoyage au niveau du stockage utilisé par l’application.

1. Dans la zone de recherche principale du portail Azure, entrez `logic apps`, puis sélectionnez **Logic Apps**.

1. Dans la colonne des cases à cocher de la liste **Applications logiques**, sélectionnez une ou plusieurs applications logiques à supprimer. Dans la barre d’outils, sélectionnez **Supprimer**.

1. Quand la boîte de confirmation s’affiche, entrez `yes`, puis sélectionnez **Supprimer**.

1. Pour vérifier si votre opération a réussi ou échoué, dans la barre d’outils Azure principale, ouvrez la liste **Notifications** (icône de cloche).

<a name="delete-workflows"></a>

### <a name="delete-workflows"></a>Supprimer des workflows

Voici de quelles manières la suppression d’un workflow affecte les instances de workflow :

* Le service Azure Logic Apps annule immédiatement les exécutions en cours et en attente, mais exécute des tâches de nettoyage au niveau du stockage que le flux de travail utilise.

* Le service Azure Logic Apps ne crée ni n’exécute pas de nouvelles instances de flux de travail.

* Si, après avoir supprimé un workflow, vous recréez le même, les métadonnées de ce dernier sont différentes de celles du workflow supprimé. Pour actualiser les métadonnées, vous devez enregistrer de nouveau les workflows qui appelaient le workflow supprimé. L’appelant obtient ainsi les bonnes informations sur le workflow recréé. Dans le cas contraire, les appels au workflow recréé échouent avec une erreur `Unauthorized`. Ce comportement s’applique aussi aux workflows qui utilisent des artefacts dans les comptes d’intégration et aux workflows qui appellent des fonctions Azure.

1. Dans le Portail Azure, ouvrez votre application logique.

1. Dans le menu de l’application logique, sous **Workflows**, sélectionnez **Workflows**. Dans la colonne des cases à cocher, sélectionnez un ou plusieurs workflows à supprimer.

1. Dans la barre d’outils, sélectionnez **Supprimer**.

1. Pour vérifier si votre opération a réussi ou échoué, dans la barre d’outils Azure principale, ouvrez la liste **Notifications** (icône de cloche).

<a name="recover-deleted"></a>

## <a name="recover-deleted-logic-apps"></a>Récupérer les applications logiques supprimées

Si vous utilisez le contrôle de code source, vous pouvez redéployer sans problème une ressource d’**Application logique (standard)** supprimée sur le service Azure Logic Apps monolocataire. Toutefois, si vous n’utilisez pas le contrôle de code source, procédez comme suit pour récupérer votre application logique supprimée.

> [!NOTE]
> Avant de tenter de récupérer votre application logique supprimée, prenez en compte les considérations suivantes :
>
> * Vous pouvez récupérer uniquement des ressources d’**Application logique (standard)** supprimées qui utilisent le plan d’hébergement **Workflow standard**. 
> Vous ne pouvez pas récupérer les ressources d’**Application logique (consommation)** supprimé.
>
> * Si votre flux de travail démarre avec le déclencheur de demande, l’URL de rappel de l’application logique récupérée diffère de l’URL de l’application logique supprimée.
>
> * L’historique des exécutions de l’application logique supprimée n’est pas disponible dans l’application logique récupérée.>

1. Vérifiez que le compte de stockage de votre application logique existe toujours. Si le compte de stockage a été supprimé, vous devez [commencer par récupérer le compte de stockage supprimé](../storage/common/storage-account-recover.md).

1. Dans le menu du compte de stockage, sous **Sécurité + réseau**, sélectionnez **Clés d’accès**.

1. Dans la page **Clés d’accès**, copiez la chaîne de connexion principale du compte, puis enregistrez-la pour une utilisation ultérieure, par exemple :

   `DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<accesskey>;EndpointSuffix=core.windows.net`

1. Dans le menu du compte de stockage, sous **Stockage des données**, sélectionnez **Partages de fichiers**, copiez le nom du partage de fichiers associé à votre application logique, puis enregistrez-le pour une utilisation ultérieure.

1. Créez une ressource d’**Application logique (standard)** en utilisant les mêmes plan d’hébergement et niveau tarifaire. Vous pouvez utiliser un nouveau nom ou réutiliser le nom de l’application logique supprimée.

1. Avant de continuer, arrêtez l’application logique. Dans le menu de l’application logique, sélectionnez **Vue d’ensemble**. Dans la barre d’outils de la page **Vue d’ensemble**, sélectionnez **Arrêter**.

1. Dans le menu de l’application logique, sous **Paramètres**, sélectionnez **Configuration**.

1. Dans la page **configuration**, mettez à jour les valeurs de paramètre d’application suivantes, et n’oubliez pas d’enregistrer vos modifications lorsque vous avez terminé.

   | Paramètre d’application | Valeur de remplacement |
   |-------------|-------------------|
   | `AzureWebJobsStorage` | Remplacez la valeur existante par la chaîne de connexion précédemment copiée à partir de votre compte de stockage. |
   | `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` | Remplacez la valeur existante par la chaîne précédemment copiée à partir de votre compte de stockage. |
   | `WEBSITE_CONTENTSHARE` | Remplacez la valeur existante par le nom de partage de fichiers précédemment copié. |
   |||

1. Dans le menu de votre application logique, sous **Workflows**, sélectionnez **Connexions**.

1. Ouvrez chaque connexion, puis sous **Paramètres**, sélectionnez **Stratégies d’accès**.

1. Supprimez la stratégie d’accès pour l’application logique supprimée, puis ajoutez une stratégie d’accès pour l’application logique de remplacement.

1. Revenez à la page de **configuration** de l’application logique et ajoutez tous les paramètres personnalisés qui existaient sur l’application logique supprimée.

1. Quand vous avez terminé, redémarrez votre application logique.

<a name="troubleshoot"></a>

## <a name="troubleshoot-problems-and-errors"></a>Résoudre les problèmes et les erreurs

<a name="missing-triggers-actions"></a>

### <a name="new-triggers-and-actions-are-missing-from-the-designer-picker-for-previously-created-workflows"></a>De nouveaux déclencheurs et actions sont absents du sélecteur de concepteur pour les workflows créés précédemment

Le service Azure Logic Apps monolocataire prend en charge les actions intégrées pour Azure Function Operations, Liquid Operations et XML Operations, telles que **Validation XML** et **Transformer au format XML**. Toutefois, pour les applications logiques créées précédemment, ces actions peuvent ne pas apparaître dans le concepteur en vue d’une sélection si votre application logique utilise une version obsolète du pack d’extension, `Microsoft.Azure.Functions.ExtensionBundle.Workflows`.

Pour corriger ce problème, procédez comme suit pour supprimer la version obsolète afin que le pack d’extension puisse être automatiquement mis à jour vers la version la plus récente.

> [!NOTE]
> Cette solution spécifique s’applique uniquement aux ressources d’**Application logique (standard)** que vous créez à l’aide du portail Azure, pas aux applications logiques que vous créez et déployez à l’aide de Visual Studio Code et de l’extension Azure Logic Apps (standard). Voir [Des déclencheurs et actions pris en charge sont absents du concepteur dans Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md#missing-triggers-actions).

1. Dans le portail Azure, arrêtez votre application logique.

   1. Dans le menu de votre application logique, sélectionnez **Vue d’ensemble**.

   1. Dans la barre d’outils du volet **Vue d’ensemble**, sélectionnez **Arrêter**.

1. Dans le menu de l’application logique, sélectionnez **Outils avancés** sous **Outils de développement**.

1. Dans le volet **Outils avancés**, sélectionnez **Accéder**, ce qui ouvre l’environnement Kudu pour votre application logique.

1. Dans la barre d’outils Kudu, ouvrez le menu **Console de débogage**, puis sélectionnez **CMD**.

   Une fenêtre de console s’ouvre pour vous permettre d’accéder au dossier du pack à l’aide de l’invite de commandes. Vous pouvez également parcourir la structure de répertoire qui s’affiche au-dessus de la fenêtre de console.

1. Accédez au dossier suivant, qui contient les dossiers de version du pack existant :

   `...\home\data\Functions\ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows`

1. Supprimez le dossier de version pour le pack existant. Dans la fenêtre de la console, vous pouvez exécuter cette commande dans laquelle vous remplacez `{bundle-version}` par la version existante :

   `rm -rf {bundle-version}`

   Par exemple : `rm -rf 1.1.3`

   > [!TIP]
   > Si vous recevez une erreur de type « autorisation refusée » ou « fichier en cours d’utilisation », actualisez la page de votre navigateur, puis réessayez d’exécuter les étapes précédentes jusqu’à ce que le dossier soit supprimé.

1. Dans le portail Azure, revenez à la page **Vue d’ensemble** de votre application logique, puis sélectionnez **Redémarrer**.

   Le portail récupère et utilise automatiquement le pack le plus récent.

## <a name="next-steps"></a>Étapes suivantes

Nous aimerions que vous nous fassiez part de votre expérience de ce scénario.

* Pour tout bogue ou problème que vous rencontreriez, [créez un problème dans GitHub](https://github.com/Azure/logicapps/issues).
* Pour toute question, demande, commentaire ou retour d’expérience, utilisez de [formulaire de commentaires](https://aka.ms/lafeedback).
