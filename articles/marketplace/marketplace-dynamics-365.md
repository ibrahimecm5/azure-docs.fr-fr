---
title: Planifier des offres Dynamics 365 pour Microsoft AppSource - Azure
description: Planifier des offres Dynamics 365 pour Microsoft AppSource
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: vamahtan
ms.author: vamahtan
ms.date: 04/30/2021
ms.openlocfilehash: 0d31c44a5daa21c16cd7c598f407501104dc53c6
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546307"
---
# <a name="plan-a-microsoft-dynamics-365-offer"></a>Planifier une offre Microsoft Dynamics 365

Cet article explique les différentes options et fonctionnalités d’une offre Dynamics 365 dans Microsoft AppSource sur le marketplace commercial. AppSource comprend des offres ou des extensions des produits Dynamics 365, Microsoft 365, Power BI et Power Apps.

Avant de commencer, créez un compte de marketplace commercial dans [Espace partenaires](./create-account.md) et vérifiez qu’il est inscrit au programme de marketplace commercial. Examinez également le [processus et les instructions de publication](/office/dev/store/submit-to-appsource-via-partner-center).

## <a name="licensing-options"></a>Options de licence

Lorsque vous vous préparez à publier une nouvelle offre, vous devez choisir une option de licence. Cela détermine les informations supplémentaires que vous devrez fournir lors de la création de l’offre dans Espace partenaires.

Voici les types de licence disponibles pour les offres Dynamics 365 :

| Type d’offre | Option de référencement |
| --- | --- |
| Dynamics 365 for Operations | Me contacter |
| Dynamics 365 Customer Engagement et Power Apps | Obtenir maintenant<br>Obtenez-en une maintenant (gratuit)<br>Essai gratuit (annonce)<br>Me contacter |
| Dynamics 365 Business Central | Obtenez-en une maintenant (gratuit)<br>Essai gratuit (annonce)<br>Me contacter |
|||

Le tableau suivant décrit le processus de transaction de chaque option de liste.

| Option de licence | Processus de transaction |
| --- | --- |
| Me contacter | Collectez des informations de contact client en connectant votre système de gestion de la relation client (CRM). Le client devra autoriser le partage de ses informations. Ces informations client, ainsi que le nom de l’offre, son ID et la place de marché sur laquelle il a trouvé votre offre, seront envoyées au système CRM que vous avez configuré. Pour plus d’informations sur la configuration de votre système CRM, consultez la section **Prospects** de la page **Configuration de l’offre** de votre type d’offre. |
| Essai gratuit (annonce) | Offrez à vos clients une évaluation gratuite de 1, 3 ou 6 mois. Les versions d’évaluation gratuites sont créées, gérées et configurées par votre service et n’ont pas d’abonnements gérés par Microsoft. |
| Obtenez-en une maintenant (gratuit) | Référencez votre offre gratuitement pour les clients. |
| Obtenir maintenant | Cela vous permet de gérer vos licences app0 ISV dans l’Espace partenaires.<br>Actuellement disponible pour le type d’offre suivant uniquement :<ul><li>Dynamics 365 pour Customer Engagement et Power Apps</li></ul><br>Pour plus d’informations sur cette option, consultez [Gestion de la licence d’application ISV](isv-app-license.md). |
|||

## <a name="test-drive"></a>Test drive

[!INCLUDE [Test drives section](includes/test-drives.md)]

## <a name="customer-leads"></a>Prospects

Quand vous publiez une offre sur le marketplace commercial par le biais d’Espace partenaires, connectez-la à votre système de Gestion des relations avec la clientèle (CRM). Cela vous permet de recevoir les coordonnées des clients dès qu’une personne exprime son intérêt ou utilise votre produit. La connexion à un CRM est obligatoire si vous souhaitez activer une version d’évaluation ; sinon, la connexion à un CRM est facultative. Espace partenaires prend en charge les tables Azure, Dynamics 365 Customer Engagement, le point de terminaison HTTPS, Marketo et Salesforce.

## <a name="legal"></a>Informations juridiques

Créez vos **conditions générales**. Les clients devront les accepter avant de pouvoir essayer votre offre. Microsoft dispose d’un contrat standard, mais il ne s’applique pas aux offres Dynamics 365.

## <a name="offer-listing-details"></a>Détails du référencement de l’offre

Voici un exemple de la façon dont les informations de l’offre s’affichent dans Microsoft AppSource (les prix indiqués sont donnés à titre d’exemple uniquement et ne reflètent pas les coûts réels) :

:::image type="content" source="media/dynamics-365/example-dynamics-365-operations.png" alt-text="Illustre la façon dont cette offre s’affiche dans Microsoft AppSource.":::

> [!NOTE]
> Le contenu du référencement de l’offre ne doit pas nécessairement être en anglais si la description de l’offre commence par la phrase « This application is available only in [langue autre que l’anglais] ».

Pour faciliter la création de votre offre, préparez ces éléments à l’avance. Tous sont obligatoires, sauf indication contraire.

- **Nom** : Le nom apparaîtra comme titre de votre offre sur le marketplace commercial. Le nom peut être une marque. Il ne peut pas contenir d’emojis (sauf s’il s’agit du symbole de marque ou de copyright) et ne doit pas dépasser 50 caractères.
- **Résumé des résultats de recherche** : L’objectif ou la fonction de votre offre en une phrase, sans saut de ligne et de 100 caractères maximum. Ce résumé est utilisé dans les résultats de recherche des offres du marketplace commercial.
- **Description** : Cette description s’affiche dans la vue d’ensemble des offres du marketplace commercial. Vous pouvez inclure une proposition de valeur, des avantages clés, une base utilisateur visée, des associations de catégorie ou de secteur, des opportunités d’achats dans l’application, des informations requises et un lien pour en savoir plus. Cette zone de texte contient des contrôles d’éditeur de texte enrichi pour rendre votre description plus attrayante. Si vous le souhaitez, utilisez des balises HTML pour la mise en forme.
- **Mots clés de recherche** (facultatif) : Maximum de trois mots clés que les clients pourront utiliser pour rechercher votre offre. N’incluez pas le **nom** ni la **description** de l’offre : ce texte est inclus automatiquement dans la recherche.
- **Produit compatible votre application** (facultatif) : Le nom d’un maximum de trois produits avec lesquels votre offre fonctionne.
- **Liens vers l’aide et la politique de confidentialité** : URL de l’aide de votre offre et de la politique de confidentialité de votre entreprise. Il vous incombe de veiller à ce que votre application soit conforme aux lois et règlements relatifs à la protection des données personnelles.
- **Informations de contact**
  - **Coordonnées du support** : Le nom, le numéro de téléphone et l’adresse e-mail des partenaires Microsoft à utiliser quand vos clients ouvrent des tickets. Incluez l’URL du site web de votre support.
  - **Coordonnées de l’équipe d’ingénierie** : Le nom, le numéro de téléphone et l’adresse e-mail de Microsoft à utiliser directement en cas de problème avec votre offre. Ces informations de contact ne sont pas répertoriées dans la place de marché commerciale.
- **Documents associés** : Maximum de trois documents destinés aux clients, tels que des livres blancs, des brochures, des listes de contrôle ou des présentations PowerPoint, au format PDF.
- **Média**
    - **Logos** : Fichier PNG pour le logo **Grande taille**. Espace partenaires l’utilisera pour créer d’autres tailles requises du logo. Plus tard, vous pourrez éventuellement les remplacer par d'autres images.
    - **Captures d’écran** : Au moins une et au plus cinq captures d’écran qui illustrent le fonctionnement de votre offre. Les images doivent être de 1280 x 720 pixels, au format PNG, et inclure une légende.
    - **Vidéos** (facultatif) : Jusqu’à quatre vidéos qui illustrent votre offre. Incluez un nom, une URL pour YouTube ou Vimeo et une miniature PNG de 1280 x 720 pixels.

> [!Note]
> Pour être publiées sur la place de marché commerciale, votre offre doit respecter les [stratégies de certification de la place de marché commerciale](/legal/marketplace/certification-policies#100-general).

## <a name="additional-sales-opportunities"></a>Opportunités de ventes supplémentaires

Vous pouvez opter pour des canaux marketing et de vente pris en charge par Microsoft. Lorsque vous créez votre offre dans Espace partenaires, deux onglets s’affichent vers la fin du processus pour **Co-vendre avec Microsoft**. cette option permet aux équipes de vente de Microsoft de tenir compte de votre solution éligible de co-vente IP lors de l’évaluation des besoins de leurs clients. Pour plus d’informations sur la façon de préparer votre offre à des fins d’évaluation, consultez [Option Co-vente de l’Espace partenaires](./co-sell-configure.md).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez étudié les éléments de planification décrits ci-dessus, sélectionnez l’une des options suivantes (également disponibles dans la table des matières à gauche) pour commencer à créer votre offre.

| Guide de publication    | Notes  |
| :------------------- | :-------------------|
| [Dynamics 365 for Operations](./dynamics-365-operations-offer-setup.md) | Lors de la création pour l’édition Entreprise, examinez d’abord ces [processus et instructions de publication](/dynamics365/fin-ops-core/dev-itpro/lcs-solutions/lcs-solutions-app-source) supplémentaires. Les types de produits incluent le Commerce, la Finance, les Ressources humaines, les Opérations de projet et la Gestion de la chaîne d’approvisionnement. |
| [Dynamics 365 pour Business Central](dynamics-365-business-central-offer-setup.md) | n/a |
| [Dynamics 365 pour Customer Engagement et Power Apps](dynamics-365-customer-engage-offer-setup.md) | Examinez d’abord ces [processus et instructions de publication](/dynamics365/customer-engagement/developer/publish-app-appsource) supplémentaires. Les types de produits incluent le service client, l’opinion du client, les opérations de projet, le service après-vente, le marketing, Mixed Reality, Power Apps, Power Automate, Power Virtual Agents, l’automatisation du service de projets et les ventes. |
|
