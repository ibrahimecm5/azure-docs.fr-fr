---
title: Transférer la propriété de la facturation des abonnements Azure pour un Contrat client Microsoft
description: Découvrez comment transférer la propriété de facturation des abonnements Azure.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/09/2021
ms.author: banders
ms.openlocfilehash: 482db68e048c645eddf16849b32e39f1dc397d09
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132179417"
---
# <a name="transfer-azure-subscription-billing-ownership-for-a-microsoft-customer-agreement"></a>Transférer la propriété de la facturation des abonnements Azure pour un Contrat client Microsoft

Transférez la propriété de facturation de vos abonnements Azure dans les cas suivants :

- Vous souhaitez transférer les responsabilités de facturation des abonnements à un autre propriétaire de facturation.
- Vous transférez vos abonnements Azure d’un contrat de licence à un autre. Par exemple, d’un Contrat Entreprise ou d’un Contrat d’abonnement à Microsoft Online (MOSA) à un Contrat client Microsoft.

[Vérifiez que vous avez accès à un contrat client Microsoft](#check-for-access).

La transition déplace uniquement la responsabilité de facturation pour vos abonnements Azure : les ressources Azure liées à vos abonnements ne sont pas déplacées, la transition n’interrompt donc pas vos services Azure.

Ce processus contient les tâches suivantes, dans lesquelles nous allons vous guider pas à pas :

1. Demander la propriété de facturation
2. Passer en revue/approuver la demande de transfert
3. Vérifier l’état de la demande de transfert

Avant de commencer, assurez-vous que la personne à qui vous demandez la propriété de facturation dispose de l’un des rôles suivants :

- Pour un Contrat client Microsoft, la personne doit avoir un rôle de propriétaire ou de contributeur pour le compte de facturation ou pour la section de profil de facturation ou de facture appropriée. Pour plus d’informations, consultez [rôles et tâches liés à la facturation](understand-mca-roles.md#invoice-section-roles-and-tasks).
- Pour un Contrat Entreprise, la personne doit être propriétaire de compte.
- Pour un Contrat d’abonnement à Microsoft Online, la personne doit être administrateur de compte.

Quand vous êtes prêt, suivez les instructions ci-dessous. Vous pouvez également consulter la vidéo suivante qui décrit chaque étape du processus.

>[!VIDEO https://www.youtube.com/embed/gfiUI2YLsgc]

## <a name="request-billing-ownership"></a>Demander la propriété de facturation

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant que propriétaire ou contributeur de la section de facture d’un compte de facturation pour un contrat client Microsoft. Utilisez les mêmes informations d’identification que celles utilisées pour accepter votre Contrat client Microsoft.
1. Recherchez **Gestion des coûts + facturation**.  
    ![Capture d’écran montrant la recherche de Gestion des coûts + facturation dans le portail Azure](./media/mca-request-billing-ownership/billing-search-cost-management-billing.png)
1. Dans la page des étendues de facturation, sélectionnez **Étendue de facturation**, puis sélectionnez le compte de facturation qui doit être utilisé pour payer l’utilisation d’Azure dans vos abonnements. Sélectionnez le compte de facturation étiqueté **Contrat Client Microsoft**.  
    [![Capture d’écran montrant la recherche de Gestion des coûts + facturation dans le portail](./media/mca-request-billing-ownership/list-of-scopes.png)](./media/mca-request-billing-ownership/list-of-scopes.png#lightbox)  
    > [!NOTE]
    >Le portail Azure mémorise la dernière étendue de facturation à laquelle vous accédez et affiche l’étendue la prochaine fois que vous revenez à la page Cost Management + Billing. Vous ne verrez pas la page des étendues de facturation si vous avez déjà consulté Gestion des coûts + facturation. Si c’est le cas, vérifiez que vous êtes dans l’[étendue appropriée](#check-for-access). Si ce n’est pas le cas, [changez d’étendue](view-all-accounts.md#switch-billing-scope-in-the-azure-portal) pour sélectionner le compte de facturation d’un Contrat client Microsoft.
1. Sélectionnez **Profils de facturation** sur le côté gauche.  
    [![Capture d’écran montrant la sélection de profils de facturation](./media/mca-request-billing-ownership/mca-select-profiles.png)](./media/mca-request-billing-ownership/mca-select-profiles.png#lightbox)
    > [!NOTE]
    > Si vous ne voyez pas les profils de facturation, vous n’êtes pas dans l’étendue de facturation appropriée. Vous devez sélectionner un compte de facturation pour un Contrat client Microsoft, puis les profils de facturation. Pour savoir comment changer d’étendues, consultez [Changer d’étendues de facturation dans le portail Azure](view-all-accounts.md#switch-billing-scope-in-the-azure-portal).
1. Sélectionnez un **profil de facturation** dans la liste. Une fois que vous obtenez la propriété des abonnements, leur utilisation sera facturée à ce profil de facturation.
1. Sélectionnez **Sections de facture** situé à gauche.  
    [![Capture d’écran montrant la sélection des sections de facture](./media/mca-request-billing-ownership/mca-select-invoice-sections.png)](./media/mca-request-billing-ownership/mca-select-invoice-sections.png#lightbox)   
1. Sélectionnez une section de facture dans la liste. Chaque profil de facturation contient une section de facture par défaut. Sélectionnez la facture dans laquelle vous souhaitez déplacer la facturation de votre abonnement Azure. C’est là que la consommation d’abonnement Azure est transférée.
1. Sélectionnez **Demandes de transfert** dans la partie inférieure gauche, puis sélectionnez **Ajouter une nouvelle demande**.  
    [![Capture d’écran montrant la sélection des demandes de transfert](./media/mca-request-billing-ownership/mca-select-transfer-requests.png)](./media/mca-request-billing-ownership/mca-select-transfer-requests.png#lightbox)
1. Entrez l’adresse e-mail de l’utilisateur dont vous demandez la propriété de facturation. L’utilisateur doit avoir un rôle d’administrateur de compte pour les anciens abonnements. Sélectionnez **Envoyer la demande de transfert**.  
    [![Capture d’écran montrant l’envoi d’une demande de transfert](./media/mca-request-billing-ownership/mca-send-transfer-requests.png)](./media/mca-request-billing-ownership/mca-send-transfer-requests.png#lightbox)

## <a name="review-and-approve-transfer-request"></a>Passer en revue et approuver la demande de transfert

1. L’utilisateur reçoit un e-mail contenant des instructions pour l’examen de votre demande de transfert.  
    ![Capture d’écran de l’e-mail invitant à examiner une demande de transfert](./media/mca-request-billing-ownership/mca-review-transfer-request-email.png)
1. Pour approuver la demande de transfert, l’utilisateur sélectionne le lien figurant dans l’e-mail, puis suit les instructions.  

    L’utilisateur sélectionne le compte de facturation à partir duquel il souhaite transférer les produits Azure. Une fois sélectionnés, les produits éligibles qui peuvent être transférés sont affichés. Une fois que les produits Azure à transférer sont sélectionnés, sélectionnez **Valider**.

    >[!NOTE]
    > Les abonnements désactivés ne peuvent pas être transférés et s’affichent dans la liste « Produits Azure non transférables », le cas échéant. 

    [![Capture d’écran montrant l’examen d’une demande de transfert](./media/mca-request-billing-ownership/review-transfer-requests.png)](./media/mca-request-billing-ownership/review-transfer-requests.png#lightbox)
1. La zone **Résultat de la validation du transfert** indique l’état des produits Azure qui vont être transférés. Voici les états possibles :
    * **Réussite** - La validation pour ce produit Azure a réussi et il peut être transféré.
    * **Avertissement** - Un avertissement s’affiche pour le produit Azure sélectionné. Même si le produit peut encore être transféré, cette opération a un impact dont l’utilisateur doit être conscient s’il souhaite prendre des mesures d’atténuation. Par exemple, l’abonnement Azure transféré bénéficie d’une instance réservée. Après le transfert, l’abonnement ne bénéficie plus de cet avantage. Pour optimiser les économies, vérifiez que l’instance réservée est associée à un autre abonnement qui peut tirer parti de ses avantages. Au lieu de cela, l’utilisateur peut également choisir de revenir à la page de sélection et de désélectionner cet abonnement Azure.
    * **Échec** - Le produit Azure sélectionné ne peut pas être transféré en raison d’une erreur. L’utilisateur doit revenir à la page de sélection et désélectionner ce produit pour transférer les autres produits Azure sélectionnés.  
    ![Capture d’écran montrant l’expérience de validation](./media/mca-request-billing-ownership/validate-transfer-request.png)
1. Une fois que la validation est effectuée avec le résultat **Réussite**, sélectionnez **Transférer**. Vous voyez un message `Transfer is in progress`, puis une fois l’opération terminée, un `Transfer completed successfully` message s’affiche.

## <a name="check-the-transfer-request-status"></a>Vérifier l’état d’une demande de transfert

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Recherchez **Gestion des coûts + facturation**.  
    ![Capture d’écran montrant la recherche de Gestion des coûts + facturation dans le portail Azure](./media/mca-request-billing-ownership/billing-search-cost-management-billing.png)
1. Dans la page des étendues de facturation, sélectionnez le compte de facturation pour lequel la demande de transfert a été envoyée.
1. Sélectionnez **Profils de facturation** sur le côté gauche.  
    [![Capture d’écran montrant la sélection de profils de facturation](./media/mca-request-billing-ownership/mca-select-profiles.png)](./media/mca-request-billing-ownership/mca-select-profiles.png#lightbox)
1. Sélectionnez le **profil de facturation** pour lequel la demande de transfert a été envoyée.
1. Sélectionnez **Sections de facture** situé à gauche.  
    [![Capture d’écran montrant la sélection des sections de facture](./media/mca-request-billing-ownership/mca-select-invoice-sections.png)](./media/mca-request-billing-ownership/mca-select-invoice-sections.png#lightbox)   
1. Dans la liste, sélectionnez la section de facture pour laquelle la demande de transfert a été envoyée.
1. Sélectionnez **Demandes de transfert** dans la partie inférieure gauche. La page Demandes de transfert affiche les informations suivantes :  
    [![Capture d’écran montrant la liste des demandes de transfert](./media/mca-request-billing-ownership/mca-select-transfer-requests-for-status.png)](./media/mca-request-billing-ownership/mca-select-transfer-requests-for-status.png#lightbox)

   |Colonne|Définition|
   |---------|---------|
   |Date de la demande|Date à laquelle la demande de transfert a été envoyée|
   |Recipient|Adresse e-mail de l’utilisateur auquel vous avez envoyé la demande de transfert de la propriété de facturation|
   |Date d'expiration|Date d’expiration de la demande|
   |Statut|Statut de la demande de transfert|

    La demande de transfert peut avoir l’un des statuts suivants :

   |Statut|Définition|
   |---------|---------|
   |En cours|L’utilisateur n’a pas accepté la demande de transfert.|
   |Traitement en cours|L’utilisateur a approuvé la demande de transfert. La facturation des abonnements que l’utilisateur a sélectionnés est en cours de transfert vers votre section de facture.|
   |Completed| La facturation des abonnements que l’utilisateur a sélectionnés a été transférée vers votre section de facture.|
   |Transfert terminé avec des erreurs|Le traitement de la demande est terminé, mais la facturation de certains abonnements que l’utilisateur a sélectionnés n’a pas pu être transférée.|
   |Expiré|L’utilisateur n’ayant pas accepté la demande à temps, celle-ci a expiré.|
   |Opération annulée|Une personne ayant accès à la demande de transfert a annulé celle-ci.|
   |Refusée|L’utilisateur a refusé la demande de transfert.|

1. Sélectionnez une demande de transfert pour en afficher les détails. La page des détails du transfert affiche les informations suivantes :  
    [![Capture d’écran montrant la liste des abonnements transférés](./media/mca-request-billing-ownership/mca-transfer-completed.png)](./media/mca-request-billing-ownership/mca-transfer-completed.png#lightbox)

   |Colonne  |Définition|
   |---------|---------|
   |ID de la demande de transfert|ID unique de votre demande de transfert. Si vous envoyez une demande de support, partagez l’ID avec le support Azure pour accélérer le traitement de votre demande|
   |Transfert demandé le|Date à laquelle la demande de transfert a été envoyée|
   |Transfert demandé par|Adresse e-mail de l’utilisateur qui a envoyé la demande de transfert|
   |La requête de transfert expire le| Date d’expiration de la demande de transfert|
   |Adresse e-mail du destinataire|Adresse e-mail de l’utilisateur auquel vous avez envoyé la demande de transfert de la propriété de facturation|
   |Lien du transfert envoyé au destinataire|URL envoyée à l’utilisateur pour l’examen de la demande de transfert|

## <a name="supported-subscription-types"></a>Types d’abonnements pris en charge

Vous pouvez demander à obtenir la propriété de facturation des types d’abonnement répertoriés ci-dessous.

- [Action Pack](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [Azure en licence Open](https://azure.microsoft.com/offers/ms-azr-0111p/)\*
- [Pass Azure Sponsorship](https://azure.microsoft.com/offers/azure-pass/)\*
- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Essai gratuit](https://azure.microsoft.com/offers/ms-azr-0044p/)\*
- [Paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Dev/Test – Paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Offre Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*
- [Offre sponsorisée Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0036p/)\*
- [Contrat Entreprise Microsoft](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Contrat client Microsoft](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/)
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [Plateformes MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/)\*
- [Abonnés Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/)\*
- [Abonnés Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/)\*
- [Abonnés Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)\*
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)\*
- [Abonnés Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)\*

\* Les crédits disponibles sur l’abonnement ne seront pas disponibles dans le nouveau compte après le transfert.

\*\* Uniquement pris en charge pour les abonnements des comptes créés lors de l’inscription sur le site web Azure.

## <a name="additional-information"></a>Informations supplémentaires

Les sections suivantes fournissent des informations supplémentaires sur le transfert d’abonnements.

### <a name="cancel-a-prior-support-plan"></a>Annuler un plan de support antérieur

Si vous disposez d’un plan de support Azure et que vous transférez tous vos abonnements Azure vers un nouveau contrat, vous devez annuler le plan de support, car il n’est pas transféré avec les abonnements. Par exemple, quand vous transférez un Contrat d’abonnement à Microsoft Online (abonnement Azure acheté sur le web) au Contrat client Microsoft. Pour annuler votre plan de support :

Utilisez les informations d’identification d’administrateur de votre ancien compte si les informations d’identification sont différentes de celles utilisées pour accéder à votre nouveau compte Contrat client Microsoft.

1.  Connectez-vous au portail Azure sur https://portal.azure.com.
1.  Accédez à **Cost Management + facturation**.
1.  Sélectionnez **Étendues de facturation** dans le volet gauche.
1.  Sélectionnez le compte de facturation associé à votre plan de support Microsoft.
    - Pour un Contrat client Microsoft :
        - Sélectionnez **Charges périodiques** dans le volet gauche.
        - Dans le volet droit, à droite de la ligne du plan de support, sélectionnez les points de suspension ( **...** ), puis **Désactiver le renouvellement automatique**.
    - Pour un Contrat d’abonnement à Microsoft Online (MOSA) :
        - Sélectionnez **Abonnements** dans le volet gauche.
        - Sélectionnez l’abonnement au plan de support dans le volet droit, puis sélectionnez **Annuler**.

### <a name="access-your-historical-invoices"></a>Accéder à vos factures historiques

Vous pouvez accéder aux factures de votre ancien compte Contrat d’abonnement à Microsoft Online (abonnement Azure acheté sur le web) après avoir transféré la propriété de facturation vers votre nouveau compte Contrat client Microsoft. Pour cela, utilisez les étapes suivantes :

Utilisez les informations d’identification d’administrateur de votre ancien compte si les informations d’identification sont différentes de celles utilisées pour accéder à votre nouveau compte Contrat client Microsoft.

1.  Connectez-vous au portail Azure sur https://portal.azure.com/.
1.  Accédez à **Cost Management + facturation**.
1.  Sélectionnez **Étendues de facturation** dans le volet gauche.
1.  Sélectionnez le compte de facturation associé à votre compte Contrat d’abonnement à Microsoft Online.
1.  Sélectionnez **Factures** dans le volet gauche pour accéder à vos factures historiques.

### <a name="no-service-downtime"></a>Aucune interruption de service.

Les services Azure compris dans l’abonnement ne subissent aucune interruption. Nous effectuons la transition de la relation de facturation uniquement pour les abonnements Azure que l’utilisateur sélectionne pour le transfert.

### <a name="disabled-subscriptions"></a>Abonnements désactivés

Les abonnements désactivés ne peuvent pas être transférés. Le transfert de propriété de facturation ne peut être effectué que pour des abonnements en état inactif.

### <a name="azure-resources-transfer"></a>Transfert de ressources Azure

Toutes les ressources associées aux abonnements, telles que les machines virtuelles, les disques et les sites web sont transférées.

### <a name="azure-marketplace-products-transfer"></a>Transfert de produits de la Place de marché Azure

Les produits de la Place de marché Azure sont transférés avec leurs abonnements respectifs.

### <a name="azure-reservations-transfer"></a>Transfert de réservations Azure

Si vous transférez des abonnements Contrat Entreprise (EA) ou des contrats client Microsoft, les réservations Azure sont automatiquement déplacées avec les abonnements.

### <a name="access-to-azure-services"></a>Accès aux services Azure

La transition n’affecte pas l’accès d’utilisateurs, de groupes ou de principaux de service existants qui a été attribué à l’aide du [contrôle d’accès en fonction du rôle Azure (Azure RBAC)](../../role-based-access-control/overview.md).

### <a name="charges-for-transferred-subscription"></a>Frais de transfert d’abonnement

Le propriétaire de facturation d’origine des abonnements est responsable de tous les frais déclarés jusqu’à ce que le transfert soit terminé. Votre section de facture est responsable des frais déclarés à partir de l’heure de transfert. Certains frais peuvent avoir été exposés avant le transfert mais déclarés ultérieurement. Ces frais apparaissent dans votre section de facture.

### <a name="cancel-a-transfer-request"></a>Annuler une demande de transfert

Vous pouvez annuler une demande de transfert jusqu’à ce qu’elle soit approuvée ou refusée. Pour annuler une demande de transfert, accédez à la [page des détails du transfert](#check-the-transfer-request-status) et sélectionnez Annuler en bas de celle-ci.

### <a name="software-as-a-service-saas-transfer"></a>Transfert SaaS (Software as a Service)

Les produits SaaS ne sont pas transférés avec les abonnements. Demandez à l’utilisateur de [contacter le support Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour transférer la propriété de facturation de produits SaaS. En même temps que la propriété de facturation, l’utilisateur peut transférer la propriété de la ressource. La propriété de ressource vous permet de mener des opérations de gestion telles que la suppression et l’affichage des détails du produit. Pour transférer la propriété d’une ressource, l’utilisateur doit en être le propriétaire sur le produit SaaS.

## <a name="check-for-access"></a>Vérification de l’accès
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez toujours besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.

## <a name="next-steps"></a>Étapes suivantes

- La propriété de facturation des abonnements Azure est transférée vers votre section de facture. Suivez les frais liés à ces abonnements dans le [Portail Microsoft Azure](https://portal.azure.com).
- Autorisez d’autres personnes à afficher et à gérer la facturation de ces abonnements. Pour plus d’informations, voir [Tâches et rôles de la section de facture](understand-mca-roles.md#invoice-section-roles-and-tasks).
