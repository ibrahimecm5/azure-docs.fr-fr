---
title: Planifier une offre SaaS pour la place de marché commerciale Microsoft – Place de marché Azure
description: Planifier une nouvelle offre de SaaS (Software as a service) pour référencement ou vente dans Microsoft AppSource, la Place de marché Azure, ou par le biais du programme de fournisseur de solutions cloud (CSP) à l’aide du programme de la place de marché commerciale Microsoft dans l’Espace partenaires Microsoft.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/26/2021
ms.openlocfilehash: bcfeb7b4baa0b9eae419bb8535869053d63ba57d
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132055809"
---
# <a name="plan-a-saas-offer-for-the-commercial-marketplace"></a>Planifier une offre SaaS pour la place de marché commerciale

Cet article explique les différentes options et exigences relatives à la publication d’offres SaaS sur le marketplace commercial de Microsoft. Les offres SaaS vous permettent de livrer et concéder sous licence des solutions logicielles à vos clients par le biais d’abonnements en ligne. En tant qu’éditeur SaaS, vous gérez et payez l’infrastructure requise pour prendre en charge l’utilisation de votre offre par vos clients. Cet article vous aidera à préparer votre offre en vue de sa publication sur la place de marché commerciale avec l’Espace partenaires.

> [!TIP]
> Pour voir l’affichage client de l’achat d’une offre SaaS dans le portail Azure, consultez [Acheter une offre SaaS dans le portail Azure](/marketplace/purchase-saas-offer-in-azure-portal) et [Acheter des applications SaaS sur Microsoft AppSource](/marketplace/purchase-software-appsource).

## <a name="listing-options"></a>Options de référencement

Lors de la préparation de la publication d’une nouvelle offre SaaS, vous devez choisir une option de _référencement_. L’option de référencement que vous choisissez détermine les informations supplémentaires que vous devrez fournir lorsque vous créerez votre offre dans Espace partenaires. Vous allez définir votre option de référencement sur la page **Configuration de l’offre**, comme expliqué dans [Créer une offre SaaS dans la place de marché commerciale](create-new-saas-offer.md).

Le tableau suivant présente les options de référencement des offres SaaS dans la place de marché commerciale.

| Option de référencement | Processus de transaction |
| ------------ | ------------- |
| Me contacter | Le client vous contacte directement à partir des informations fournies dans votre référencement.``*``<br>Vous pouvez passer à une autre option de mise en vente après la publication de l’offre. |
| Essai gratuit | Le client est redirigé vers votre URL cible via Azure Active Directory (Azure AD).``*``<br>Vous pouvez passer à une autre option de mise en vente après la publication de l’offre. |
| Obtenez-en une maintenant (gratuit) | Le client est redirigé vers votre URL cible via Azure AD.``*``<br>Vous pouvez passer à une autre option de mise en vente après la publication de l’offre. |
| Vendre via Microsoft  | Les offres vendues par le biais de Microsoft sont qualifiées de _pouvant faire l’objet d’une transaction_. Une offre pouvant faire l’objet d’une transaction est une offre dans laquelle Microsoft facilite l’échange d’argent contre l’acquisition d’une licence logicielle au nom de l’éditeur. Nous facturons les offres SaaS selon le modèle de tarification de votre choix, et vous gérez les transactions client en votre nom. En tant que partenaire, les frais d’utilisation de l’infrastructure Azure vous sont directement facturés. Vous devez tenir compte des frais d’infrastructure dans votre modèle de tarification. Cette procédure est expliquée de façon plus détaillée dans [Facturation de SaaS](#saas-billing).<br><br>**Remarque** : vous ne pouvez pas modifier cette option une fois que votre offre est publiée.  |
|||

``*`` Les éditeurs sont responsables de la prise en charge de tous les aspects de la transaction de licence logicielle, notamment, la commande, la réalisation, le contrôle, la facturation, le paiement et la collecte.

Pour plus d’informations sur ces options de référencement, consultez [Fonctionnalités de transaction de la place de marché commerciale](marketplace-commercial-transaction-capabilities-and-considerations.md).

Une fois votre offre publiée, l’option de référencement que vous avez choisie pour votre offre s’affiche sous la forme d’un bouton dans l’angle supérieur gauche de la page de référencement de l’offre. Par exemple, la capture d’écran suivante montre une page de référencement d’offre dans la Place de marché Azure avec le bouton **Obtenir maintenant**.

![Illustre un référencement d’offre dans le magasin en ligne.](./media/saas/listing-options-saas.png)

## <a name="technical-requirements"></a>Exigences techniques

Les exigences techniques varient en fonction de l’option de référencement que vous choisissez pour votre offre.

L’option de référencement _Me contacter_ n’est assortie d’aucune exigence technique. Vous avez la possibilité de connecter un système de Gestion des relations avec la clientèle (CRM) pour gérer les prospects. Ce point est décrit dans la section [Prospects](#customer-leads), plus loin dans cet article.

Les options de référencement _Obtenir maintenant (gratuit)_ ,  _Essai gratuit_ et _Vendre via Microsoft_ sont assortie des exigences techniques suivantes :

- Vous devez activer les Comptes Microsoft (MSA) et [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) pour l’authentification des acheteurs sur votre site. Vous devez permettre aux acheteurs disposant d’un compte Azure AD de se connecter à votre application en utilisant Azure AD avec une authentification unique (SSO).
- Vous devez créer une page d’arrivée offrant une expérience de connexion et d’intégration fluide pour un client ayant acheté votre offre. La page d’arrivée l’aidera à effectuer tout approvisionnement ou toute configuration supplémentaires requis. Pour des conseils sur la création de la page d’arrivée, consultez les articles suivants :
  - [Créer la page d'accueil de votre offre SaaS avec transaction dans la Place de marché commerciale](azure-ad-transactable-saas-landing-page.md)
  - [Créer la page d'accueil de votre offre SaaS gratuite ou d’évaluation dans la Place de marché commerciale](azure-ad-free-or-trial-landing-page.md)

Ces exigences techniques supplémentaires s’appliquent uniquement à l’option de référencement _Vendre via Microsoft_ (pouvant faire l’objet d’une transaction) :

- Vous devez utiliser les [API de traitement SaaS](./partner-center-portal/pc-saas-fulfillment-apis.md) pour l’intégration avec la Place de marché Azure et Microsoft AppSource. Vous devez exposer un service qui peut interagir avec l’abonnement SaaS pour créer, mettre à jour et supprimer un compte d’utilisateur et un plan de service. Les modifications critiques de l’API doivent être effectuées dans les 24 heures. Des modifications non critiques de l’API sont publiées régulièrement. Des diagrammes et des explications détaillées décrivant l’utilisation des champs collectés sont disponibles dans la documentation des [API](./partner-center-portal/pc-saas-fulfillment-apis.md).
- Vous devez créer au moins un plan pour votre offre. Votre plan est facturé en fonction du modèle de tarification que vous sélectionnez avant la publication : _forfaitaire_ ou _par utilisateur_. Vous trouverez des détails supplémentaires sur les [plans](#plans) plus loin dans cet article.
- Le client peut annuler votre offre à tout moment.

### <a name="technical-information"></a>Informations techniques

Si vous créez une offre pouvant faire l’objet d’une transaction, vous devez collecter les informations suivantes pour la page **Configuration technique**. Si vous choisissez de traiter les transactions de façon indépendante au lieu de créer une offre pouvant faire l’objet d’une transaction, ignorez cette section et accédez à [Versions d’évaluation](#test-drives).

- **URL de la page d’arrivée** : URL du site SaaS (par exemple, `https://contoso.com/signup`) vers lequel les utilisateurs sont dirigés après avoir acquis votre offre sur la place de marché commerciale, déclenchant le processus de configuration à partir du nouvel abonnement SaaS créé. Cette URL reçoit un jeton utilisable pour appeler les API de traitement afin d’obtenir les détails de l’approvisionnement pour votre page d’inscription interactive.

  Cette URL sera appelée avec le paramètre de jeton d’identification d’achat de la place de marché, qui identifie de façon unique l’achat de SaaS du client spécifique. Vous devez échanger ce jeton contre les détails de l’abonnement SaaS correspondant à l’aide de l’[API de résolution](./partner-center-portal/pc-saas-fulfillment-subscription-api.md#resolve-a-purchased-subscription). Ces détails et ceux que vous souhaitez collecter dans le cadre d’une page web interactive peuvent être utilisés pour lancer l’expérience d’intégration du client, qui doit se terminer par un appel d’activation sur l’API pour le démarrage de la période d’abonnement. Sur cette page, l’utilisateur doit s’inscrire via une authentification en un clic à l’aide d’Azure Active Directory (Azure AD).

  Cette URL avec le paramètre de jeton d’identification d’achat de la place de marché est également appelée lorsque le client lance une expérience SaaS gérée à partir du portail Azure ou du centre d’administration Microsoft 365. Vous devez gérer les deux flux : lorsque le jeton est fourni pour la première fois après un achat par un nouveau client, et quand il est à nouveau fourni pour un client existant qui gère sa solution SaaS.

    La page d’arrivée que vous configurez ici doit être opérationnelle en permanence. C’est la seule façon pour vous d’être averti des nouveaux achats de vos offres SaaS effectués sur la place de marché commerciale, ou des demandes de configuration pour un abonnement actif à une offre.

- **Webhook de connexion** : pour tous les événements asynchrones que Microsoft doit vous envoyer (par exemple, lors de l’annulation d’un abonnement SaaS), nous vous demandons de [fournir une URL de webhook de connexion](./partner-center-portal/pc-saas-fulfillment-webhook.md). Nous appellerons cette URL pour vous informer de l’événement.

  Le webhook que vous fournissez doit être opérationnel 24 heures sur 24, 7 jours sur 7. C’est la seule façon pour vous d’être informé des mises à jour concernant les abonnements SaaS de vos clients achetés via le marketplace commercial.

  > [!NOTE]
  > Dans le portail Azure, nous vous recommandons de créer une [inscription d’application Azure Active Directory (Azure AD)](../active-directory/develop/howto-create-service-principal-portal.md) monolocataire. Utilisez les détails de l’inscription d’application pour authentifier votre solution lors de l’appel des API de la Place de marché. Pour trouver l’[ID de locataire](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in), accédez à votre Azure Active Directory, sélectionnez **Propriétés**, puis cherchez le numéro d’ID de répertoire répertorié. Par exemple : `50c464d3-4930-494c-963c-1e951d15360e`.

- **ID de locataire Azure Active Directory** : (également appelé ID de répertoire). À l’intérieur du portail Azure, nous exigeons que vous [inscriviez une application Azure Active Directory (AD)](../active-directory/develop/howto-create-service-principal-portal.md) afin de pouvoir l’ajouter à la liste de contrôle d’accès (ACL) de l’API pour vous assurer que vous êtes autorisé à l’appeler. Pour trouver l’ID de locataire de votre application Azure Active Directory (AD), accédez au panneau [Inscriptions d’applications](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) dans Azure Active Directory. Dans la colonne **Nom d’affichage**, sélectionnez l’application. Recherchez ensuite le numéro d’**ID de répertoire (locataire)** référencé (par exemple, `50c464d3-4930-494c-963c-1e951d15360e`).

- **ID d’application Azure Active Directory** : vous avez également besoin de votre [ID d’application](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in). Pour en connaître la valeur, accédez au panneau [Inscriptions d’applications](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) dans Azure Active Directory. Dans la colonne **Nom d’affichage**, sélectionnez l’application. Recherchez ensuite le numéro d’ID d’application (client) référencé (par exemple, `50c464d3-4930-494c-963c-1e951d15360e`).

  L’ID d’application Azure AD est associé à votre ID d’éditeur dans votre compte de l’Espace partenaires. Vous devez utiliser le même ID d’application pour toutes les offres dans ce compte.

  > [!NOTE]
  > Si l’éditeur a au moins deux comptes différents dans l’Espace partenaires, les détails de l’inscription d’application Azure AD ne peuvent être utilisés que dans un seul compte. L’utilisation de la même paire ID de locataire/ID d’application pour une offre sous un compte d’éditeur différent n’est pas prise en charge.

## <a name="test-drives"></a>Versions d’évaluation
Vous pouvez choisir d’activer une version d’évaluation pour votre application SaaS. Les versions d’évaluation permettent aux clients d’accéder à un environnement préconfiguré pendant un nombre d’heures fixe. Vous pouvez activer des versions d’évaluation pour n’importe quelle option de publication, mais cette fonctionnalité est assortie d’exigences supplémentaires. Pour en savoir plus sur les versions d’évaluation, consultez [Qu’est-ce qu’une version d’évaluation ?](what-is-test-drive.md). Pour plus d’informations sur la configuration de différents types de versions d’évaluation, consultez [Configuration technique de la version d’évaluation](test-drive-technical-configuration.md).

> [!TIP]
> Une version d’évaluation n’est pas un [essai gratuit](plans-pricing.md#free-trials). Vous pouvez proposer une version d’évaluation, un essai gratuit ou les deux. Dans les deux cas, vos clients disposent de votre solution pendant une période fixe. Cependant, une version d’évaluation propose en outre une visite autoguidée concrète des principales fonctionnalités de votre produit, ainsi qu’une démonstration des avantages de celui-ci au travers d’un scénario d’implémentation réel.

## <a name="customer-leads"></a>Prospects

Vous devez connecter votre offre à votre système de gestion des relations avec la clientèle (Customer Relationship Management, CRM) pour collecter des informations sur les clients. Le client devra autoriser le partage de ses informations. Ces détails du client, ainsi que le nom de l’offre, son ID et le magasin en ligne dans lequel il a trouvé votre offre, seront envoyés au système CRM que vous avez configuré. La place de marché commerciale prend en charge un vaste éventail de systèmes CRM, ainsi que la possibilité d’utiliser une table Azure ou de configurer un point de terminaison HTTPS à l’aide de Power Automate.

Vous pouvez ajouter ou modifier une connexion CRM à tout moment pendant ou après la création de l’offre. Pour obtenir des instructions détaillées, consultez [Prospects de votre offre de marketplace commercial](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="selecting-an-online-store"></a>Sélection d’un magasin en ligne

Lorsque vous publiez une offre SaaS, celle-ci est référencée dans Microsoft AppSource, la Place de marché Azure ou les deux. Chaque magasin en ligne répond aux exigences uniques des clients. AppSource est destiné aux solutions métier, et la Place de marché Azure aux solutions informatiques. Votre type d’offre, vos fonctionnalités de transaction et vos catégories déterminent l’emplacement de publication de votre offre. Les catégories et sous-catégories sont mappées à chaque magasin en ligne en fonction du type de votre solution. 

Si votre offre Saas est *à la fois* une solution informatique (Place de marché Azure) et une solution commerciale (AppSource), sélectionnez une catégorie et une sous-catégorie applicable à chaque magasin en ligne. Les offres publiées sur les deux magasins en ligne doivent avoir une proposition de valeur à la fois en tant que solution informatique *et* solution d’entreprise.

> [!IMPORTANT]
> Les offres SaaS avec [facturation à l’usage](partner-center-portal/saas-metered-billing.md) sont disponibles sur Place de marché Azure et le portail Azure. Les offres SaaS avec des plans privés uniquement sont disponibles sur le portail Azure et AppSource.

| Facturation à l’usage | Plan public | Plan privé | Disponible sur : |
|---|---|---|---|
| Oui             | Oui         | Non           | Place de marché Azure et Portail Azure |
| Oui             | Oui         | Oui          | Place de marché Azure et Portail Azure* |
| Oui             | Non          | Oui          | Portail Azure uniquement |
| Non              | Non          | Oui          | Portail Azure et AppSource |
|||||

&#42; Le plan privé de l’offre ne sera disponible que via le portail Azure et AppSource.

Par exemple, une offre avec facturation à l’usage et un plan privé uniquement (aucun plan public) est achetée par les clients du portail Azure. En savoir plus sur les [offres privées dans le marketplace commercial de Microsoft](private-offers.md).

Pour plus d’informations sur les options de référencement prises en charge par les magasins en ligne, consultez [Options de référencement et de tarification par magasin en ligne](determine-your-listing-type.md#listing-and-pricing-options-by-online-store). Pour plus d’informations sur les catégories et les sous-catégories, consultez [Catégories et sous-catégories dans le marketplace commercial](categories.md).

## <a name="legal-contracts"></a>Contrats juridiques

Pour simplifier le processus d’approvisionnement pour les clients et réduire la complexité juridique pour les éditeurs de logiciels, Microsoft propose un contrat Standard que vous pouvez utiliser pour vos offres dans la place de marché commerciale. Lorsque vous proposez votre logiciel dans le cadre du contrat standard, les clients ne doivent lire et accepter ce contrat qu’une seule fois, et vous n’avez pas besoin d’élaborer des conditions générales personnalisées.

Si vous choisissez d’utiliser le contrat standard, vous avez la possibilité d’ajouter à celui-ci des conditions d’avenant universel et jusqu’à 10 avenants personnalisés. Vous pouvez également utiliser vos propres conditions générales à la place du contrat standard. Vous devez gérer ces détails dans la page **Propriétés**. Pour plus d’informations, consultez [Contrat Standard pour la place de marché commerciale de Microsoft](standard-contract.md).

> [!NOTE]
> Après avoir publié une offre à l’aide du contrat standard pour la place de marché commerciale, vous ne pouvez pas utiliser vos propres conditions générales personnalisées. Vous devez faire un choix entre les deux. Vous proposez votre solution avec le contrat standard ou vos propres conditions générales. Si vous souhaitez modifier les conditions du contrat standard, vous le pouvez par le biais d’avenants au contrat standard.

## <a name="microsoft-365-integration"></a>Intégration de Microsoft 365

L’intégration avec Microsoft 365 permet à votre offre SaaS de fournir une expérience connectée sur plusieurs surfaces d’application Microsoft 365 par le biais de compléments gratuits associés, comme les applications Teams, les compléments Office et les solutions SharePoint Framework. Vous pouvez aider vos clients à découvrir facilement toutes les facettes de votre solution E2E (service web + compléments associés) et à les déployer dans un seul processus en fournissant les informations suivantes. 
  - Si votre offre SaaS s’intègre à Microsoft Graph, fournissez l’ID d’application Azure Active Directory (AAD) utilisé par votre offre SaaS pour l’intégration. Les administrateurs peuvent passer en revue les autorisations d’accès requises pour le bon fonctionnement de votre offre SaaS telles qu’elles sont définies dans l’ID d’application AAD et accorder l’accès si une autorisation administrateur avancée est nécessaire au moment du déploiement. 
    
     Si vous choisissez de vendre votre offre par le biais de Microsoft, il s’agit du même ID d’application AAD que celui que vous avez enregistré pour une utilisation sur votre page d’accueil afin d’obtenir les informations utilisateur de base nécessaires à l’activation de l’abonnement client. Pour obtenir une aide détaillée, consultez [Créer la page d’accueil de votre offre SaaS avec transaction dans la place de marché commerciale](azure-ad-transactable-saas-landing-page.md). 
    
   -    Fournissez une liste de compléments associés qui fonctionnent avec votre offre SaaS et que vous souhaitez lier. Les clients pourront découvrir votre solution E2E sur Microsoft AppSource et les administrateurs peuvent déployer l’offre SaaS et tous les compléments associés que vous avez liés dans le même processus via le centre d’administration Microsoft 365.
    
        Pour lier des compléments associés, vous devez fournir le lien AppSource du complément, ce qui signifie que le complément doit d’abord être publié sur AppSource. Les types de compléments pris en charge que vous pouvez lier sont : applications Teams, compléments Office et solutions SharePoint Framework (SPFx). Chaque complément lié doit être unique pour une offre SaaS. 

Pour les produits liés, la recherche sur AppSource retourne un résultat qui comprend à la fois l’offre SaaS et tous les compléments liés. Le client peut naviguer entre les pages des détails sur le produit de l’offre SaaS et les compléments liés. Les administrateurs informatiques peuvent réviser et déployer l’offre SaaS et les compléments liés au cours du même processus via une expérience intégrée et connectée dans le centre d’administration Microsoft 365. Pour plus d’informations, consultez [Tester et déployer des applications Microsoft 365 - Administrateur Microsoft 365](/microsoft-365/admin/manage/test-and-deploy-microsoft-365-apps).

### <a name="microsoft-365-integration-support-limitations"></a>Limitations de la prise en charge de l’intégration Microsoft 365

La détection d’une seule solution E2E est prise en charge sur AppSource dans tous les cas. Toutefois, le déploiement simplifié de la solution E2E, comme décrit ci-dessus, via le centre d’administration Microsoft 365 n’est pas pris en charge dans les scénarios suivants :

   - Offres « Me contacter » référencées uniquement. 
   - Le même complément est lié à plusieurs offres SaaS.
   - L’offre SaaS est liée à des compléments, mais elle ne s’intègre pas à Microsoft Graph et aucun ID d’application AAD n’est fourni.
  - L’offre SaaS est liée à des compléments, mais l’ID d’application AAD fourni pour l’intégration Microsoft Graph est partagé entre plusieurs offres SaaS.
 
## <a name="offer-listing-details"></a>Détails du référencement de l’offre

Lorsque vous [créez une offre SaaS](create-new-saas-offer.md) dans l’Espace partenaires, vous devez entrer du texte, des images, des vidéos facultatives et d’autres détails dans la page de **référencement de l’offre**. Il s’agit des informations que les clients verront quand ils découvriront le référencement de votre offre dans la place de marché commerciale, comme illustré dans l’exemple suivant.

:::image type="content" source="./media/saas/example-saas-1.png" alt-text="Illustre la façon dont cette offre s’affiche dans Microsoft AppSource.":::

**Descriptions de légende**

1. Logo
2. Catégories
3. Secteurs d’activité
4. Adresse du support technique (lien)
5. Conditions d’utilisation
6. Politique de confidentialité
7. Nom de l’offre
8. Résumé
9. Description
10. Captures d’écran/vidéos
11. Documents

L’exemple suivant montre un référencement d’offre dans le portail Azure.

![Illustre un référencement d’offre dans le portail Azure.](./media/example-managed-service-azure-portal.png)

**Descriptions des renvois**

1. Intitulé
1. Description
1. Liens utiles
1. Captures d’écran.

> [!NOTE]
> Le contenu du référencement de l’offre ne doit pas nécessairement être en anglais si la description de l’offre commence par la phrase « This application is available only in [langue autre que l’anglais] ».

Pour faciliter la création de votre offre, préparez certains de ces éléments à l’avance. Sauf indication contraire, les éléments suivants sont requis.

- **Name** : ce nom apparaîtra en tant que titre du référencement de votre annonce dans la place de marché commerciale. Le nom peut être une marque. Il ne peut pas contenir d’emojis (sauf s’il s’agit du symbole de marque ou de copyright) et ne doit pas dépasser 50 caractères.
- **Résultats de recherche récapitulatifs** : décrivez l’objectif ou la fonction de votre offre sous la forme d’une phrase sans saut de ligne de maximum 100 caractères. Ce récapitulatif est utilisé dans les résultats de recherche dans les référencements de la place de marché commerciale.
- **Description** : cette description s’affichera dans la vue d’ensemble des référencements de la place de marché commerciale. Vous pouvez inclure une proposition de valeur, des avantages clés, une base utilisateur visée, des associations de catégorie ou de secteur, des opportunités d’achats dans l’application, des informations requises et un lien pour en savoir plus.

    Cette zone de texte contient des contrôles d’éditeur de texte riches, que vous pouvez utiliser pour rendre votre description plus attrayante. Vous pouvez également utiliser des balises HTML pour mettre en forme votre description. Vous pouvez entrer dans cette zone de texte jusqu’à 3 000 caractères, balisage HTML compris. Pour obtenir plus d’astuces, consultez [Rédiger une bonne description d’application](/windows/uwp/publish/write-a-great-app-description).

- **Instructions de prise en main** : si vous choisissez de vendre votre offre par via Microsoft (offre négociable), ce champ est obligatoire. Ces instructions aident les clients à se connecter à votre offre SaaS. Vous pouvez ajouter jusqu’à 3 000 caractères de texte et des liens vers une documentation en ligne plus détaillée.
- **Mots clés de recherche** (facultatif) : Entrez jusqu’à trois mots clés que les clients pourront utiliser pour rechercher votre offre dans les magasins en ligne. Vous n’avez pas besoin d’inclure le **Nom** et la **Description** l’offre : ce texte est inclus automatiquement dans la recherche.
- **Lien vers la politique de confidentialité** : URL de la politique de confidentialité de votre société. Vous devez renseigner une politique de confidentialité conforme et veiller à ce que votre application respecte les lois et réglementations en matière de confidentialité.
- **Informations de contact** : Vous devez fournir les contacts suivants de votre organisation :
  - **Contact de support** : indiquez le nom, le numéro de téléphone et l’adresse e-mail des partenaires Microsoft à utiliser quand vos clients ouvrent des tickets. Vous devez également inclure l’URL de votre site web de support.
  - **Contact d’ingénierie** : indiquez le nom, le numéro de téléphone et l’adresse e-mail de Microsoft à utiliser directement en cas de problème avec votre offre. Ces informations de contact ne sont pas répertoriées dans la place de marché commerciale.
  - **Contact du programme du fournisseur de solutions cloud** (facultatif) : indiquez le nom, le numéro de téléphone et l’adresse e-mail si vous optez pour le programme CSP, afin que ces partenaires puissent vous contacter pour toute question. Vous pouvez également inclure une URL pour vos documents marketing.
- **Liens utiles** (facultatif) : vous pouvez fournir des liens vers diverses ressources destinées aux utilisateurs de votre offre. Par exemple, forums, FAQ et notes de publication.
- **Documents associés** : vous pouvez fournir jusqu’à trois documents destinés aux clients, tels que des livres blancs, des brochures, des listes de contrôle ou des présentations PowerPoint.
- **Média – Logos** : fournissez un fichier PNG pour le logo **Grande taille**. L'Espace partenaires l'utilisera pour créer un logo de **Petite taille**  et un logo de **Taille moyenne**. Plus tard, vous pourrez éventuellement les remplacer par d'autres images.

   - Grande taille (de 216 x 216 à 350 x 350 px, obligatoire)
   - Moyen (90 x 90 px, facultatif)
   - Petite taille (48 x 48 px, facultatif)

  Ces logos sont utilisés à différents emplacements dans les magasins en ligne :

  - Le petit logo s’affiche dans les résultats de recherche sur Place de marché Azure, ainsi que dans la page principale et celle des résultats de recherche de Microsoft AppSource.
  - Le logo moyen s’affiche lorsque vous créez une nouvelle ressource dans Microsoft Azure.
  - Le grand logo apparaît sur la page de référencement de votre offre dans Place de marché Azure et AppSource.

- **Média – Captures d’écran** : vous devez ajouter au moins une et cinq captures d’écran avec les exigences suivantes, qui illustrent le fonctionnement de votre offre :
  - 1280 x 720 pixels
  - Type de fichier PNG
  - Doit inclure une légende
- **Média – Vidéos** (facultatif) : vous pouvez ajouter jusqu’à quatre vidéos avec les exigences suivantes, qui présentent votre offre :
  - Nom
  - URL : doit être hébergée sur YouTube ou Vimeo uniquement.
  - Miniature : fichier PNG 1280 x 720

> [!Note]
> Pour être publiées sur la place de marché commerciale, votre offre doit respecter les [stratégies de certification de la place de marché commerciale](/legal/marketplace/certification-policies#100-general) et les [stratégies SaaS](/legal/marketplace/certification-policies#1000-software-as-a-service-saas).

> [!NOTE]
> Le public d’une préversion diffère de celui d’un plan privé. Un plan privé est un plan que vous mettez à la disposition d’un public spécifique que vous choisissez. Cela vous permet de négocier un plan personnalisé avec des clients spécifiques. Pour plus d’informations, consultez la section suivante : Plans.

Vous pouvez envoyer des invitations à des adresses e-mail de Compte Microsoft (MSA) ou d’Azure Active Directory (Azure AD). Ajoutez jusqu’à 10 adresses e-mail manuellement, ou importez-en jusqu’à 20 à l’aide d’un fichier .csv. Si votre offre est déjà publiée, vous pouvez toujours définir un public pour la préversion en vue de tester d’éventuels changements ou mises à jour.

## <a name="plans"></a>Plans

Les offres pouvant faire l’objet d’une transaction nécessitent au moins un plan. Un plan définit l’étendue et les limites de la solution, ainsi que la tarification associée. Vous pouvez créer plusieurs plans pour votre offre afin d’offrir à vos clients différentes options techniques et de tarification. Si vous choisissez de traiter les transactions de façon indépendante au lieu de créer une offre pouvant faire l’objet d’une transaction, la page **Plans** n’est pas visible. Dans ce cas, ignorez cette section et accéder à [Opportunités de ventes supplémentaires](#additional-sales-opportunities).

Pour obtenir des conseils généraux sur les plans, notamment des modèles de tarification, des essais gratuits et des plans privés, consultez [Plans et tarification des offres de la Place de marché commerciale](plans-pricing.md). Les sections suivantes présentent des informations supplémentaires spécifiques des offres SaaS.

### <a name="saas-pricing-models"></a>Modèles tarifaires de SaaS

Les offres SaaS peuvent utiliser l’un des deux modèles de tarification avec chaque plan : _forfaitaire_ ou _par utilisateur_. Tous les plans d’une même offre doivent être associés au même modèle de tarification. Par exemple, une offre ne peut pas avoir un plan forfaitaire et un autre plan par utilisateur.

**Forfaitaire** : autorisez l’accès à votre offre pour un prix forfaitaire mensuel ou annuel. Cela est parfois désigné sous le nom de « tarification en fonction du site ». Avec ce modèle de tarification, vous pouvez éventuellement définir des plans contrôlés qui utilisent l’API du service de contrôle de la place de marché afin de facturer les clients pour l’utilisation non couverte par le prix forfaitaire. Pour plus d’informations sur la facturation contrôlée, consultez [Facturation à l’usage pour SaaS à l’aide du service de contrôle de la place de marché commerciale](./partner-center-portal/saas-metered-billing.md). Vous devez également utiliser cette option si le comportement d’utilisation votre service SaaS s’effectue en rafales.

**Par utilisateur** : autorisez l’accès à votre offre pour un prix basé sur le nombre d’utilisateurs pouvant accéder à l’offre ou occuper des sièges. Avec ce modèle basé sur les utilisateurs, vous pouvez définir les nombres minimal et maximal d’utilisateurs que le plan prend en charge. Vous pouvez créer plusieurs plans pour configurer différents niveaux de prix en fonction du nombre d’utilisateurs. Ces champs sont facultatifs. Si vous n’en sélectionnez aucun, le nombre d’utilisateurs est considéré comme illimité (avec au minimum 1 utilisateur et au maximum d’autant d’utilisateurs que votre service le permet). Ces champs peuvent être modifiés dans le cadre d’une mise à jour de votre plan.

> [!IMPORTANT]
> Une fois votre offre publiée, vous ne pouvez plus modifier le modèle de tarification. En outre, tous les plans d’une même offre doivent partager le même modèle de tarification.

### <a name="saas-billing"></a>Facturation SaaS

Pour les applications SaaS qui s’exécutent dans votre abonnement Azure (d’éditeur), l’utilisation de l’infrastructure vous est facturée directement. Les clients ne voient pas les frais réels liés à l’utilisation de l’infrastructure. Vous devez intégrer les frais d’utilisation de l’infrastructure Azure dans la tarification de vos licences logicielles pour compenser le coût de l’infrastructure que vous avez déployée pour exécuter la solution.

Les offres d’application SaaS vendues via Microsoft prennent en charge une facturation mensuelle ou annuelle basée sur un tarif forfaitaire, par utilisateur, ou une facturation à l’utilisation à l’aide du [service de facturation contrôlée](./partner-center-portal/saas-metered-billing.md). La place de marché commerciale fonctionne selon un modèle d’agence. Les prix sont définis par l’éditeur, les factures sont établies par Microsoft, et l’éditeur est payé par Microsoft après déduction des frais d’agence.

Vous trouverez ci-dessous un exemple de ventilation des coûts et des paiements pour illustrer le modèle de l’agence. Dans cet exemple, Microsoft facture 100 $ au client pour votre licence logicielle et verse 80 $ à l’éditeur.

| Coût de votre licence | 100 $/mois |
| ------------ | ------------- |
| Coût d’utilisation Azure (D1/1 cœur) | Facturé directement à l’éditeur au lieu du client |
| Microsoft facture au client le montant suivant : | 100 $/mois (l’éditeur doit inclure les coûts d’infrastructure encourus ou transmis dans les frais de licence) |
| **Microsoft facture** | **100 $/mois** |
| Microsoft facture des frais de service de la place de marché de 3% et vous paie 97% du coût de votre licence | 97 $/mois |
|

Un public de préversion peut accéder à votre offre avant sa publication dans les boutiques en ligne. Il peut voir comment votre offre s’affichera dans la place de marché commerciale et tester la fonctionnalité de bout en bout avant sa publication en direct. 

Dans la page **Public de la préversion**, vous pouvez définir un public de préversion limité. Ce paramètre n’est pas disponible si vous choisissez de traiter les transactions de façon indépendante au lieu de vendre votre offre via Microsoft. Dans ce cas, vous pouvez ignorer cette section et accéder à [Opportunités de ventes supplémentaires](#additional-sales-opportunities).

## <a name="test-offer"></a>Offre test

Avant de publier votre offre en direct, nous vous conseillons d’utiliser la fonctionnalité en préversion pour développer votre implémentation technique, tester et expérimenter différents modèles de tarification.

Pour développer et tester votre offre SaaS avec le moins de risque possible, nous vous recommandons de créer une offre de test et de développement (DEV) à des fins d’expérimentation et de test. L’offre DEV est distincte de votre offre en production (PROD).

Pour éviter tout achat accidentel de l’offre DEV, vous n’appuierez jamais sur le bouton **Go live** pour publier l’offre DEV.

![Illustre la page Vue d’ensemble de l’offre pour une offre dans l’Espace partenaires. Le bouton Go Live et les liens d’aperçu s’affichent. Le lien Afficher le rapport de validation s’affiche également sous Validation automatique.](./media/review-publish-offer/publish-status-saas.png)

Voici quelques raisons de créer une offre DEV distincte que l’équipe de développement peut utiliser pour le développement et le test de l’offre PROD :

- Éviter les frais accidentels pour les clients
- Évaluer les modèles de tarification
- Pas d’ajout de plans qui ne ciblent pas de clients réels

### <a name="avoid-accidental-customer-charges"></a>Éviter les frais accidentels pour les clients

En utilisant une offre DEV au lieu de l’offre PROD et en les traitant comme des environnements de développement et de production, vous pouvez éviter les frais accidentels pour les clients.

Nous vous recommandons d’inscrire deux applications Azure AD différentes pour appeler les API de la place de marché. Les développeurs utiliseront une application Azure AD avec les paramètres de l’offre DEV et l’équipe des opérations utilisera l’inscription d’application PROD. En procédant ainsi, vous pouvez éviter que l’équipe de développement ne commette des erreurs accidentelles, comme appeler l’API pour annuler l’abonnement d’un client qui paie 100 000 $ par mois. Vous pouvez également éviter de facturer un client pour une utilisation limitée qu’il n’a pas consommée.

### <a name="evaluate-pricing-models"></a>Évaluer les modèles de tarification

Le test des modèles de tarification dans l’offre DEV réduit les risques lorsque les développeurs testent des modèles de tarification différents.

Les éditeurs peuvent créer les plans dont ils ont besoin dans l’offre DEV pour déterminer le modèle de tarification le mieux adapté à leur offre. Les développeurs peuvent souhaiter créer plusieurs plans dans l’offre DEV pour tester différentes combinaisons de tarification. Par exemple, vous pouvez créer des plans avec différents jeux de dimensions limitées personnalisées. Vous pouvez créer un plan différent avec un mélange de dimensions limitées personnalisées et de tarification forfaitaire.

Pour tester plusieurs options de tarification, vous devez créer un plan pour chaque modèle de tarification unique. Pour en savoir plus, consultez [Plans](#plans).

### <a name="not-adding-plans-that-do-not-target-actual-customers"></a>Pas d’ajout de plans qui ne ciblent pas de clients réels

En utilisant une offre DEV pour le développement et le test, vous pouvez réduire l’encombrement inutile dans l’offre PROD. Par exemple, vous ne pouvez pas supprimer les plans que vous créez pour tester des modèles de tarification ou des configurations techniques différents (sans créer un ticket de support). Donc, en créant des plans de test dans l’offre DEV, vous réduisez l’encombrement dans l’offre PROD.

L’encombrement dans l’offre PROD est source de frustration pour les équipes produit et marketing, car elles s’attendent à ce que tous les projets ciblent des clients réels. En particulier dans le cas de grandes équipes disséminées qui veulent toutes utiliser des bacs à sable différents, la création de deux offres fournit deux environnements différents pour DEV et PROD. Dans certains cas, vous souhaiterez peut-être créer plusieurs offres DEV pour prendre en charge une équipe plus grande dont des membres exécutent des scénarios de test différents. Permettre à différents membres de l’équipe de travailler dans l’offre DEV distincte de l’offre PROD aide à ce que les plans de production soient aussi prêts pour la production que possible.

Le test d’une offre DEV permet d’éviter la limite de 30 dimensions limitées personnalisées par offre. Les développeurs peuvent essayer différentes combinaisons de compteurs dans l’offre DEV sans affecter la limite de dimension limitée personnalisée dans l’offre PROD.

## <a name="additional-sales-opportunities"></a>Opportunités de ventes supplémentaires

Vous pouvez opter pour des canaux marketing et de vente pris en charge par Microsoft. Lorsque vous créez votre offre dans l’Espace partenaires, deux onglets s’affichent vers la fin du processus :

- **Revendre via des fournisseurs de solutions cloud** : utilisez cette option pour permettre aux partenaires fournisseurs de solutions cloud (CSP) Microsoft de revendre votre solution dans le cadre d’une offre groupée. Pour plus d’informations sur ce programme, consultez [Programme des fournisseurs de solutions Cloud](cloud-solution-providers.md).

- **Co-vendre avec Microsoft** : cette option permet aux équipes de vente de Microsoft de tenir compte de votre solution éligible de co-vente IP lors de l’évaluation des besoins de leurs clients. Pour plus d’informations sur l’éligibilité à la co-vente, consultez [Prérequis relatifs à l’état de co-vente](/legal/marketplace/certification-policies#3000-requirements-for-co-sell-status). Pour plus d’informations sur la façon de préparer votre offre à des fins d’évaluation, consultez [Option de co-vente sur Espace partenaires](co-sell-configure.md).

## <a name="next-steps"></a>Étapes suivantes

- [Créer une offre SaaS dans la place de marché commerciale](create-new-saas-offer.md)
- [Bonnes pratiques pour le référencement des offres](gtm-offer-listing-best-practices.md)
