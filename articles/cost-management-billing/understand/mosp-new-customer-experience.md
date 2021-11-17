---
title: Bien démarrer avec votre compte de facturation Azure mis à jour
description: Bien démarrer avec votre compte de facturation Azure mis à jour pour comprendre les modifications apportées à la nouvelle expérience de facturation et de gestion des coûts
author: bandersmsft
ms.reviewer: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: banders
ms.openlocfilehash: 07c8b711ae32311cceba0c6ebe3353974b867356
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061765"
---
# <a name="get-started-with-your-updated-azure-billing-account"></a>Bien démarrer avec votre compte de facturation Azure mis à jour

La gestion des coûts et des factures est l’un des principaux composants de votre expérience cloud. Elle vous aide à contrôler et à comprendre vos dépenses sur le cloud. Pour faciliter la gestion de vos coûts et factures, Microsoft met à jour votre compte de facturation Azure afin d’inclure des fonctionnalités de facturation et de gestion des coûts améliorées. Cet article décrit les mises à jour de votre compte de facturation et vous présente les nouvelles fonctionnalités.

> [!IMPORTANT]
> Votre compte sera mis à jour lorsque vous recevrez un e-mail de Microsoft vous informant des mises à jour de votre compte. La notification est envoyée 60 jours avant la mise à jour de votre compte.

## <a name="more-flexibility-with-your-new-billing-account"></a>Plus de flexibilité avec votre nouveau compte de facturation

Le diagramme suivant compare l’ancien compte de facturation avec le nouveau :

:::image type="content" source="./media/mosp-new-customer-experience/comparison-old-new-account.png" alt-text="Diagramme comparant la hiérarchie de facturation de l’ancien et du nouveau compte." border="false" lightbox="./media/mosp-new-customer-experience/comparison-old-new-account.png":::

Votre nouveau compte de facturation contient un ou plusieurs profils de facturation qui vous permettent de gérer vos factures et modes de paiement. Chaque profil de facturation contient une ou plusieurs sections de facture qui vous permettent d'organiser les coûts sur la facture du profil de facturation.

:::image type="content" source="./media/mosp-new-customer-experience/new-billing-account-hierarchy.png" alt-text="Diagramme montrant la nouvelle hiérarchie de facturation." border="false" lightbox="./media/mosp-new-customer-experience/new-billing-account-hierarchy.png":::

Les rôles du compte de facturation disposent du plus haut niveau d’autorisations. Ces rôles sont à attribuer aux utilisateurs qui doivent consulter des factures et suivre les coûts pour tout votre compte, comme les responsables des services financiers et informatiques d’une organisation ou la personne qui a souscrit un compte. Pour plus d’informations, consultez [Rôles et tâches liés au compte de facturation](../manage/understand-mca-roles.md#billing-account-roles-and-tasks). Une fois votre compte mis à jour, l’utilisateur doté d’un rôle d’administrateur de compte dans l’ancien compte de facturation reçoit un rôle de propriétaire sur le nouveau compte.

## <a name="billing-profiles"></a>Profils de facturation

Un profil de facturation vous permet de gérer vos factures et modes de paiement. Une facture mensuelle est générée au début du mois pour chaque profil de facturation associé à votre compte. La facture contient les frais respectifs du mois précédent pour tous les abonnements associés au profil de facturation.

Lorsque votre compte est mis à jour, un profil de facturation est automatiquement créé pour chaque abonnement. Les frais liés à l’abonnement sont facturés dans le profil de facturation qui convient et apparaissent sur la facture.

Les rôles associés aux profils de facturation sont autorisés à afficher et à gérer les factures et les modes de paiement. Ces rôles sont à attribuer aux utilisateurs qui paient les factures tels que les membres du service de comptabilité d’une organisation. Pour plus d’informations, consultez [Rôles et tâches liés au profil de facturation](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

Lorsque votre compte est mis à jour, pour chaque abonnement pour lequel vous avez accordé à d’autres utilisateurs l’autorisation d’[afficher des factures](download-azure-invoice.md#allow-others-to-download-your-subscription-invoice), les utilisateurs titulaires d’un rôle Azure propriétaire, contributeur, lecteur ou lecteur de facturation se voient attribuer le rôle lecteur sur le profil de facturation correspondant.

## <a name="invoice-sections"></a>Sections de facture

Une section de facture vous permet d'organiser les coûts sur votre facture. Par exemple, une seule facture peut suffire, mais vous pouvez souhaitez organiser les coûts par service, équipe ou projet. Pour ce scénario, vous disposez d'un seul profil de facturation dans lequel vous créez une section de facture pour chaque service, équipe ou projet.

Une fois votre compte mis à jour, une section de facture est créée pour chaque profil de facturation et l’abonnement associé est attribué à la section de facture. Quand vous ajoutez d’autres abonnements, vous pouvez créer des sections supplémentaires et affecter les abonnements aux sections de facture. Ces sections apparaissent sur la facture et reflètent l’utilisation de chaque abonnement que vous leur avez attribué.

Les rôles figurant dans la section de facture sont autorisés à contrôler qui crée des abonnements Azure. Ces rôles sont à attribuer aux utilisateurs configurant l’environnement Azure pour les équipes de votre organisation, tels que les responsables d'ingénierie et les architectes techniques. Pour plus d’informations, consultez [Rôles et tâches liés aux sections de facture](../manage/understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="enhanced-features-in-your-new-experience"></a>Fonctionnalités améliorées dans votre nouvelle expérience

Votre nouvelle expérience comprend les fonctionnalités de facturation et de gestion des coûts suivantes qui vous permettent de gérer facilement vos coûts et factures :

#### <a name="invoice-management"></a>Gestion des factures

**Période de facturation mensuelle plus prévisible** : dans votre nouveau compte, la période de facturation commence le premier jour du mois et se termine le dernier jour du mois, quelle que soit la date à laquelle vous vous inscrivez pour utiliser Azure. Une facture est générée au début de chaque mois et contient tous les frais du mois précédent.

**Obtenez une seule facture mensuelle pour plusieurs abonnements** : dans votre compte existant, vous recevez une facture pour chaque abonnement Azure. Quand votre compte est mis à jour, le comportement existant est conservé, mais vous avez la possibilité de regrouper les frais de vos différents abonnements dans une seule facture. Une fois que vous avez mis à jour votre compte, suivez les étapes ci-dessous pour regrouper vos frais dans une seule facture :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Recherchez **Gestion des coûts + facturation**.  
   ![Capture d’écran montrant la recherche de « gestion des coûts + facturation » dans le Portail Azure.](./media/mosp-new-customer-experience/billing-search-cost-management-billing.png)
3. Sélectionnez **Abonnements Azure** à gauche de l’écran. 
4. Le tableau liste les abonnements Azure que vous payez. Le profil de facturation qui est facturé pour l’abonnement s’affiche dans la colonne de profil de facturation. Les frais d’abonnement s’affichent dans la facture du profil de facturation. Pour regrouper les frais de tous vos abonnements dans une seule facture, vous devez lier tous vos abonnements au même profil de facturation.  
    :::image type="content" source="./media/mosp-new-customer-experience/list-azure-subscriptions.png" alt-text="Capture d’écran montrant la liste des abonnements Azure." lightbox="./media/mosp-new-customer-experience/list-azure-subscriptions.png" :::
5. Choisissez un profil de facturation à utiliser. 
6. Sélectionnez un abonnement qui n’est pas lié au profil de facturation que vous avez choisi à l’étape 5. Cliquez sur les points de suspension (trois points) correspondant à l’abonnement. Sélectionnez **Modifier la section de facture**.  
    :::image type="content" source="./media/mosp-new-customer-experience/select-change-invoice-section.png" alt-text="Capture d’écran montrant l’option qui permet de changer la section de facture." lightbox="./media/mosp-new-customer-experience/select-change-invoice-section-zoomed-in.png" :::
7. Sélectionnez le profil de facturation que vous avez choisi à l’étape 5.  
    :::image type="content" source="./media/mosp-new-customer-experience/change-invoice-section.png" alt-text="Capture d’écran montrant l’option qui permet de changer la section de facture." lightbox="./media/mosp-new-customer-experience/change-invoice-section-zoomed-in.png" :::
8. Sélectionnez **Changer**.
9. Répétez les étapes 6 à 8 pour tous les autres abonnements. 

**Recevez une seule facture mensuelle pour les abonnements Azure, les plans de support et les produits de la Place de marché Azure** : vous obtenez une facture mensuelle pour tous les frais, y compris les frais d’utilisation des abonnements Azure, les plans de support et les achats effectués sur la Place de marché Azure.

**Regroupez les coûts sur la facture selon vos besoins** : vous pouvez regrouper les coûts sur votre facture selon vos besoins, par services, projets ou équipes.

**Définissez un numéro de bon de commande facultatif sur la facture** : pour associer votre facture à vos systèmes financiers internes, définissez un numéro de bon de commande. Gérez-le et mettez-le à jour à tout moment dans le portail Azure.

#### <a name="payment-management"></a>Gestion des paiements

**Payez les factures immédiatement à l’aide d’une carte de crédit** :vous n’êtes pas tenu d’attendre que le paiement automatique intervienne sur votre carte de crédit. Vous pouvez utiliser n’importe quelle carte de crédit pour payer une facture due ou en retard dans le portail Azure.

**Effectuez le suivi de tous les paiements appliqués au compte** : affichez tous les paiements appliqués à votre compte, notamment le mode de paiement utilisé, le montant payé et la date de paiement dans le portail Azure.

#### <a name="cost-management"></a>la gestion des coûts ;

**Planifiez des exportations mensuelles d’utilisation des données dans un compte de stockage** : publiez automatiquement vos données sur les coûts et l’utilisation dans un compte de stockage, sur une base quotidienne, hebdomadaire ou mensuelle.

#### <a name="account-and-subscription-management"></a>Gestion des comptes et des abonnements

**Affectez plusieurs administrateurs aux opérations de facturation** : attribuez des autorisations de facturation à plusieurs utilisateurs pour gérer la facturation de votre compte. Bénéficiez d’une certaine flexibilité en accordant des autorisations de lecture, d’écriture ou les deux à d’autres utilisateurs.

**Créez des abonnements supplémentaires directement dans le portail Azure** : créez tous vos abonnements en une seule sélection dans le portail Azure.

#### <a name="api-support"></a>prise en charge des API

**Effectuez des opérations de facturation et de gestion des coûts par le biais des API, du kit SDK et de PowerShell** : utilisez les API de gestion des coûts, de facturation et de consommation pour extraire les données de facturation et de coût dans vos outils d’analyse de données préférés.

**Effectuez toutes les opérations d’abonnement par le biais des API, du kit SDK et de PowerShell** : utilisez les API d’abonnement Azure pour automatiser la gestion de vos abonnements Azure, notamment la création, le renommage et l’annulation d’un abonnement.

## <a name="get-prepared-for-your-new-experience"></a>Préparez-vous à votre nouvelle expérience

Pour vous préparer à votre nouvelle expérience, nous vous recommandons d’utiliser les éléments suivants :

**Période de facturation mensuelle et date de facture différente**

Dans la nouvelle expérience, votre facture est générée le neuvième jour de chaque mois et contient tous les frais du mois précédent. Cette date peut différer de la date à laquelle votre facture est générée dans l’ancien compte. Si vous partagez vos factures avec d’autres personnes, informez-les de ce changement de date.


**Factures du premier mois suivant la migration**

Le jour où votre compte est mis à jour, les frais existants non facturés sont calculés et vous recevez les factures correspondantes le jour où vous recevez généralement vos factures. Par exemple, John a deux abonnements Azure : Azure sub 01 avec un cycle de facturation entre le cinquième jour du mois et le quatrième jour du mois suivant, et Azure sub 02 avec un cycle de facturation entre le dixième jour du mois et le neuvième jour du mois suivant. Généralement, John reçoit les factures des deux abonnements Azure le cinq du mois. Si le compte de John est mis à jour le 4 avril, les frais sont calculés entre le 5 mars et le 4 avril pour Azure sub 01, et entre le 10 mars et le 4 avril pour Azure sub 02. John reçoit deux factures, une pour chaque abonnement le 5 avril. Une fois le compte mis à jour, le cycle de facturation de John est basé sur le mois calendaire et couvre tous les frais engagés entre le début et la fin du mois calendaire. La facture des frais d’un mois calendaire est disponible au cours des premiers jours du mois suivant. Ainsi, dans l’exemple ci-dessus, John reçoit au début du mois de mai une facture couvrant la période de facturation du 5 au 30 avril.


**Nouvelles API de facturation et de gestion des coûts**

Si vous utilisez les API Cost Management ou Billing pour interroger et mettre à jour vos données de facturation ou de coût, vous devez utiliser les nouvelles API. Le tableau ci-dessous répertorie les API qui ne fonctionnent pas avec le nouveau compte de facturation, ainsi que les modifications que vous devez apporter à votre nouveau compte de facturation.

|API | Modifications  |
|---------|---------|
|[Comptes de facturation - Liste](/rest/api/billing/2019-10-01-preview/billingaccounts/list) | Dans l’API Comptes de facturation - Liste, votre ancien compte de facturation indique pour agreementType **MicrosoftOnlineServiceProgram**, votre nouveau compte de facturation indiquera pour agreementType **MicrosoftCustomerAgreement**. En cas de dépendance sur agreementType, mettez-la à jour. |
|[Factures - Liste par abonnement de facturation](/rest/api/billing/2019-10-01-preview/invoices/listbybillingsubscription)     | Cette API renvoie uniquement les factures générées avant la mise à jour de votre compte. Utilisez l’API [Factures - Liste par compte de facturation](/rest/api/billing/2019-10-01-preview/invoices/listbybillingaccount) pour obtenir les factures générées dans votre nouveau compte de facturation. |


## <a name="cost-management-updates-after-account-update"></a>Cost Management mis à jour après la mise à jour du compte

Votre compte de facturation Azure mis à jour pour votre Contrat client Microsoft vous donne accès, dans le portail Azure, à des expériences Cost Management nouvelles et enrichies que ne proposait pas votre compte de paiement à l’utilisation.

### <a name="new-capabilities"></a>Nouvelles fonctionnalités

Les nouvelles fonctionnalités suivantes sont disponibles avec votre compte de facturation Azure.

#### <a name="new-billing-scopes"></a>Nouvelles étendues de facturation

Dans le cadre de votre compte mis à jour, vous disposez de nouvelles étendues dans Cost Management + Billing. En plus de faciliter l’organisation hiérarchique et la facturation, elles permettent d’afficher les frais combinés de plusieurs abonnements sous-jacents. Pour plus d’informations sur les étendues de facturation, consultez [Étendues du Contrat client Microsoft](../costs/understand-work-scopes.md#microsoft-customer-agreement-scopes).

Vous pouvez également accéder aux API Cost Management pour bénéficier de vues de coûts combinés avec des étendues plus élevées. Toutes les API Cost Management qui utilisent l’étendue d’abonnement sont toujours disponibles avec des modifications mineures dans le schéma. Pour plus d’informations sur les API, consultez [API Cost Management](/rest/api/cost-management/) et [API Azure Consumption](/rest/api/consumption/).

#### <a name="cost-allocation"></a>Affectation des coûts

Une fois votre compte mis à jour, vous pouvez utiliser les fonctionnalités d’affectation des coûts pour répartir les coûts des services partagés de votre organisation. Pour plus d’informations sur l’affectation des coûts, consultez [Créer et gérer des règles d’affectation des coûts Azure](../costs/allocate-costs.md).

#### <a name="power-bi"></a>Power BI

Le connecteur Cost Management pour Power BI Desktop vous aide à créer des visualisations et des rapports personnalisés concernant votre utilisation d’Azure et vos dépenses. Connectez-vous à votre compte mis à jour pour accéder à vos données d’utilisation et de coût. Pour plus d’informations sur le connecteur Cost Management pour Power BI Desktop, consultez [Créer des visuels et des rapports avec le connecteur Cost Management dans Power BI Desktop](/power-bi/connect-data/desktop-connect-azure-cost-management).

### <a name="updated-capabilities"></a>Fonctionnalités mises à jour

Les fonctionnalités mises à jour suivantes sont disponibles avec votre compte de facturation Azure.

#### <a name="cost-analysis"></a>Analyse des coûts

Vous pouvez continuer à voir et à suivre vos coûts de consommation d’un mois à l’autre, mais vous pouvez désormais voir les coûts des réservations et des achats de la Place de marché dans Analyse des coûts.

Avec votre compte mis à jour, vous recevez une seule facture pour tous les frais Azure. Vous disposez également d’une vue mensuelle simplifiée qui remplace la vue précédente des périodes de facturation.

Par exemple, si votre période de facturation allait du 24 novembre au 23 décembre avec votre ancien compte, elle passe, après la mise à niveau, du 1er novembre au 30 novembre, du 1er décembre au 31 décembre, et ainsi de suite.

:::image type="content" source="./media/mosp-new-customer-experience/billing-periods.png" alt-text="Capture d’écran comparant les anciennes et les nouvelles périodes de facturation." lightbox="./media/mosp-new-customer-experience/billing-periods.png" :::

#### <a name="budgets"></a>Budgets

Vous pouvez maintenant créer des budgets pour le compte de facturation, ce qui vous permet de faire le suivi des coûts sur plusieurs abonnements. Vous pouvez également vous tenir au courant de vos frais d’achat en utilisant des budgets. Pour plus d’informations sur les budgets, consultez [Créer et gérer les budgets Azure](../costs/tutorial-acm-create-budgets.md).

#### <a name="exports"></a>Exports

Votre nouveau compte de facturation propose des fonctionnalités d’exportation améliorées. Par exemple, vous pouvez créer des exportations pour les coûts réels qui incluent les achats ou les coûts amortis (coûts d’achat des réservations répartis sur la période d’achat). Vous pouvez également créer une exportation pour le compte de facturation afin d’obtenir les données d’utilisation et de facturation sur l’ensemble des abonnements du compte de facturation. Pour plus d’informations sur les exportations, consultez [Créer et gérer des données exportées](../costs/tutorial-export-acm-data.md).

> [!NOTE]
> Les exportations créées avant la mise à jour de votre compte avec le type **Exportation mensuelle des coûts du mois dernier** exportent les données du dernier mois civil, et non de la dernière période de facturation.

Par exemple, pour une période de facturation allant du 23 décembre au 22 janvier, le fichier CSV exporté contient des données de coût et d’utilisation pour cette période. Après la mise à jour, l’exportation contient les données du mois civil (par exemple, du 1er janvier au 31 janvier, et ainsi de suite).

:::image type="content" source="./media/mosp-new-customer-experience/export-amortized-costs.png" alt-text="Capture d’écran comparant les anciens et les nouveaux détails d’exportation." lightbox="./media/mosp-new-customer-experience/export-amortized-costs.png" :::

## <a name="additional-information"></a>Informations supplémentaires

Les sections suivantes fournissent des informations supplémentaires sur votre nouvelle expérience.

**Aucune interruption de service.** Les services Azure compris dans votre abonnement continuent de s’exécuter sans aucune interruption. La seule mise à jour porte sur votre expérience de facturation. Les ressources, groupes de ressources ou groupes d’administration existants ne sont pas affectés.

**Aucune modification apportée aux ressources Azure** L’accès aux ressources Azure défini à l’aide du contrôle d’accès en fonction du rôle Azure (Azure RBAC) n’est pas affecté par la mise à jour.

**Les factures antérieures sont disponibles dans la nouvelle expérience** Les factures générées avant la mise à jour de votre compte restent disponibles dans le portail Azure.

**Factures pour le compte mis à jour au milieu du mois** Si votre compte est mis à jour au milieu du mois, vous recevrez une facture pour les frais accumulés jusqu’au jour de la mise à jour de votre compte. Vous recevrez une autre facture pour le reste du mois. Par exemple, si votre compte a un abonnement et qu’il est mis à jour le 15 septembre, vous recevrez une facture pour les frais accumulés jusqu’au 15 septembre. Vous recevrez une autre facture pour la période comprise entre le 15 septembre et le 30 septembre. Au-delà du mois de septembre, vous recevrez une facture par mois.

## <a name="troubleshooting"></a>Dépannage

### <a name="you-get-an-email-to-accept-the-terms-of-the-microsoft-customer-agreement-but-the-link-in-the-email-doesnt-work"></a>Vous recevez un e-mail pour accepter les termes du Contrat client Microsoft, mais le lien dans l’e-mail ne fonctionne pas

Une fois votre compte de facturation Azure mis à jour, vous recevez un e-mail de Microsoft pour accepter les termes du Contrat client Microsoft. Il se peut que le lien dans l’e-mail ne fonctionne pas pour les raisons suivantes :

- Vous avez accès à l’administration de la facturation via l’un de vos autres e-mails. 

    Cela se produit généralement lorsque vous recevez un e-mail vous invitant à accepter les termes du Contrat client Microsoft. Vous cliquez sur le lien et vous affichez un message d’erreur - `You don't have permission to accept the agreement. This typically happens when you sign in with an email, which doesn’t have permission to accept the agreement. Check you’ve signed in with the correct email address. If you are still seeing the error, see Why I can't accept an agreement`. Vérifiez que vous êtes connecté avec l’adresse e-mail autorisée à effectuer l’administration de la facturation.

- Vous avez accès à la facture via une identité différente. 

    Certains clients ont deux identités avec la même adresse e-mail : un compte professionnel et un compte Microsoft. Généralement, une seule de leurs identités dispose des autorisations nécessaires pour administrer la facturation. S’ils se connectent avec une identité non autorisée, ils ne peuvent pas accepter les termes du contrat. Vérifiez que vous utilisez l’identité correcte pour vous connecter.

- Vous êtes connecté au mauvais locataire Azure Active Directory (Azure AD). 

    Votre compte de facturation est associé à un locataire Azure AD. Si vous êtes connecté à un locataire incorrect, vous ne pouvez pas accepter les termes du contrat. Vérifiez que vous êtes connecté au bon locataire Azure AD. Si vous n’êtes pas connecté au bon locataire, utilisez la commande suivante pour changer de locataire dans le portail Azure :

    1. Sélectionnez votre e-mail dans l’angle supérieur droit de la page.

    2. Sélectionnez **Changer de répertoire**.

        ![Capture d’écran montrant la sélection du changement de répertoire dans le portail](./media/mosp-new-customer-experience/select-switch-directory.png)

    3. Sélectionnez l’option **Changer** correspondant à un répertoire dans la section **Tous les répertoires**.

        ![Capture d’écran montrant la sélection d’un répertoire dans le portail](./media/mosp-new-customer-experience/select-directory.png)

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contactez le support technique.

Si vous avez toujours besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants pour en savoir plus sur votre compte de facturation.

- [Comprendre les rôles d’administration associés à votre nouveau compte de facturation](../manage/understand-mca-roles.md)
- [Créer un abonnement Azure supplémentaire associé à votre compte de facturation](../manage/create-subscription.md)
- [Créer des sections sur votre facture pour organiser vos coûts](../manage/mca-section-invoice.md)