---
title: Créer des attributions de licence SQL Server pour Azure Hybrid Benefit
description: Cet article explique comment créer des attributions de licence SQL Server pour Azure Hybrid Benefit.
author: bandersmsft
ms.author: banders
ms.date: 09/30/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: ahb
ms.reviewer: chrisri
ms.openlocfilehash: e97c4a51366dbfd2c9016de4684129a91df0cbe9
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130218936"
---
# <a name="create-sql-server-license-assignments-for-azure-hybrid-benefit"></a>Créer des attributions de licence SQL Server pour Azure Hybrid Benefit

La nouvelle expérience Azure Hybrid Benefit centralisée dans le portail Azure prend en charge les attributions de licence SQL Server au niveau du compte ou d’un abonnement particulier. Lorsque l’attribution est créée au niveau du compte, les remises Azure Hybrid Benefit sont automatiquement appliquées aux ressources SQL dans tous les abonnements du compte jusqu’au nombre de licences spécifié dans l’attribution.

Pour chaque attribution de licence, une étendue est sélectionnée, puis des licences sont affectées à l’étendue. Chaque étendue peut avoir plusieurs entrées de licence.

## <a name="prerequisites"></a>Prérequis

Les prérequis suivants doivent être remplis pour créer des attributions de licence SQL Server.

- Votre organisation dispose d’un type de contrat et d’une offre qui sont pris en charge.
- Vous êtes membre d’un rôle qui a les autorisations nécessaires pour attribuer des licences SQL.
- Votre organisation dispose de licences de base SQL Server avec Software Assurance ou de licences d’abonnement de base à attribuer à Azure.
- Votre organisation est inscrite à l’inscription automatique des machines virtuelles Azure SQL auprès de l’extension IaaS. Pour en savoir plus, consultez [Inscription automatique auprès de l’extension SQL IaaS Agent](../../azure-sql/virtual-machines/windows/sql-agent-extension-automatic-registration-all-vms.md).
  > [!IMPORTANT]
  > Si vous ne respectez pas cette condition préalable, Azure produira des données incomplètes sur votre utilisation actuelle d’Azure Hybrid Benefit. Cette situation pourrait conduire à des attributions incorrectes de licences et entraîner des frais inutiles de paiement à l’utilisation pour les licences SQL Server.

Les rôles prérequis varient en fonction du type de contrat.

| Type d'accord | Rôle requis | Offres prises en charge |
| --- | --- | --- |
| Contrat Entreprise | _Administrateur d’entreprise_<p> Pour plus d’informations sur la façon de devenir membre du rôle, consultez [Ajouter un autre administrateur d’entreprise](../manage/ea-portal-administration.md#add-a-department-administrator). | - MS-AZR-0017P (Microsoft Azure Enterprise)<br>- MS-AZR-USGOV-0017P (Azure Government Enterprise) |
| Contrat client Microsoft | *Propriétaire du compte de facturation*<br> *Collaborateur du compte de facturation* <br> *Propriétaire du profil de facturation*<br> *Collaborateur du profil de facturation*<br> Pour plus d’informations sur la façon de devenir membre de ces rôles, consultez [Gérer les rôles de facturation](../manage/understand-mca-roles.md#manage-billing-roles-in-the-azure-portal). | MS-AZR-0017G (Plan Microsoft Azure)|
| WebDirect /Paiement à l’utilisation | Non disponible | None |
| Clients dirigés par des fournisseurs de solutions Cloud ou des partenaires | Non disponible | None |

> [!NOTE]
> L’attribution centralisée de licences à des étendues n’est pas disponible pour les abonnements de type Sponsorisé, Crédit MSDN ou MPN. L’utilisation du logiciel SQL est gratuite pour les abonnements Dev/Test (types d’offres MS-AZR-0148P ou MS-AZR-0023P).

## <a name="create-a-sql-license-assignment"></a>Créer une attribution de licence SQL

Dans la procédure suivante, vous naviguez de **Cost Management and Billing** à **Réservations + Hybrid Benefit**. N’accédez pas à **Réservations** à partir de la page d’accueil d’Azure. Vous ne disposeriez pas de l’étendue nécessaire pour visualiser l’expérience d’attribution de licence. 

1. Connectez-vous au portail Azure et accédez à **Cost Management and Billing**.  
    :::image type="content" source="./media/create-sql-license-assignments/select-cost-management.png" alt-text="Capture d’écran montrant la navigation du portail Azure vers Cost Management and Billing." lightbox="./media/create-sql-license-assignments/select-cost-management.png" :::
 2. Si vous avez un Contrat Entreprise, sélectionnez une étendue de facturation.  
    :::image type="content" source="./media/create-sql-license-assignments/select-billing-scope.png" alt-text="Capture d’écran montrant la sélection de l’étendue de facturation de Contrat Entreprise." lightbox="./media/create-sql-license-assignments/select-billing-scope.png" :::
 3. Si vous avez un Contrat client Microsoft, sélectionnez un profil de facturation.  
    :::image type="content" source="./media/create-sql-license-assignments/select-billing-profile.png" alt-text="Capture d’écran montrant la sélection du profil de facturation." lightbox="./media/create-sql-license-assignments/select-billing-profile.png" :::
 4. Dans le menu de gauche, sélectionnez **Réservations + Hybrid Benefit**.  
    :::image type="content" source="./media/create-sql-license-assignments/select-reservations.png" alt-text="Capture d’écran montrant la sélection de Réservations + Hybrid Benefit."  :::
 5. Sélectionnez **Ajouter**, puis, dans la liste, sélectionnez **Azure Hybrid Benefit (préversion)** .  
    :::image type="content" source="./media/create-sql-license-assignments/select-azure-hybrid-benefit.png" alt-text="Capture d’écran montrant la sélection d’Azure Hybrid Benefit." lightbox="./media/create-sql-license-assignments/select-azure-hybrid-benefit.png" :::
 6. Sur l’écran suivant, sélectionnez **Commencer pour attribuer des licences**.  
    :::image type="content" source="./media/create-sql-license-assignments/get-started-centralized.png" alt-text="Capture d’écran montrant la sélection Ajouter un avantage hybride SQL." lightbox="./media/create-sql-license-assignments/get-started-centralized.png" :::
 7. Choisissez une étendue, puis entrez le nombre de licences à utiliser pour chaque édition de SQL Server. Si vous n’avez aucune licence à attribuer pour une édition spécifique, entrez zéro.  
    > [!NOTE]
    > Il vous incombe de déterminer si les entrées que vous effectuez dans l’expérience de licence gérée au niveau de l’étendue sont exactes et si elles vous permettront de remplir vos obligations en matière de licences. Les informations relatives à l’utilisation des licences sont affichées pour vous aider à effectuer vos attributions de licence. Toutefois, les informations affichées peuvent être incomplètes ou inexactes en raison de différents facteurs.
    >
    > Si le nombre de licences que vous entrez est inférieur à celui que vous utilisez actuellement, un message d’avertissement s’affiche indiquant « _Vous avez entré moins de licences que celles que vous utilisez actuellement pour Azure Hybrid Benefit dans cette étendue. Votre facture pour cette étendue va augmenter._  »  
    
    :::image type="content" source="./media/create-sql-license-assignments/select-assignment-scope-edition.png" alt-text="Capture d’écran montrant la sélection de l’étendue et le nombre de licences." lightbox="./media/create-sql-license-assignments/select-assignment-scope-edition.png" :::
 8. Si vous le souhaitez, sélectionnez l’onglet **Détails de l’utilisation** pour afficher votre utilisation actuelle d’Azure Hybrid Benefit activée dans l’étendue des ressources.  
    :::image type="content" source="./media/create-sql-license-assignments/select-assignment-scope-edition-usage.png" alt-text="Capture d’écran montrant les détails de l’onglet Utilisation." lightbox="./media/create-sql-license-assignments/select-assignment-scope-edition-usage.png" :::
 9. Sélectionnez **Ajouter**.
10. Si vous le souhaitez, modifiez le nom par défaut de l’attribution de licence. La date de révision est automatiquement fixée à la même date l’année suivante et ne peut pas être modifiée. Son objectif est de vous rappeler d’examiner régulièrement vos attributions de licence.  
    :::image type="content" source="./media/create-sql-license-assignments/license-assignment-commit.png" alt-text="Capture d’écran montrant le nom par défaut de l’attribution de licence." lightbox="./media/create-sql-license-assignments/license-assignment-commit.png" :::
11. Après avoir vérifié vos choix, sélectionnez **Suivant : Vérifier + appliquer**.
12. Sélectionnez l’option d’attestation **En sélectionnant &quot;Appliquer&quot;** pour confirmer que vous avez le pouvoir d’appliquer Azure Hybrid Benefit, un nombre suffisant de licences SQL Server, et que vous conserverez les licences à jour tant qu’elles seront attribuées.  
    :::image type="content" source="./media/create-sql-license-assignments/confirm-apply-attestation.png" alt-text="Capture d’écran montrant l’option d’attestation." lightbox="./media/create-sql-license-assignments/confirm-apply-attestation.png" :::
13. Sélectionnez **Appliquer**, puis **Oui**.
14. La liste des licences attribuées s’affiche.  
    :::image type="content" source="./media/create-sql-license-assignments/assigned-licenses.png" alt-text="Capture d’écran montrant la liste des licences attribuées." lightbox="./media/create-sql-license-assignments/assigned-licenses.png" ::: 

## <a name="track-assigned-license-use"></a>Suivre l’utilisation des licences attribuées

Accédez à **Cost Management and Billing**, puis sélectionnez **Réservations + Hybrid Benefit**.

Une liste de toutes les réservations et attributions de licence s’affiche. Si vous souhaitez filtrer les résultats pour n’avoir que les attributions de licences, définissez un filtre **Avantage hybride SQL**.

:::image type="content" source="./media/create-sql-license-assignments/view-the-assignments.png" alt-text="Capture d’écran montrant la liste des réservations et des licences." lightbox="./media/create-sql-license-assignments/view-the-assignments.png" :::

Sous **Utilisation du dernier jour** ou **Utilisation sur 7 jours**, sélectionnez un pourcentage, qui peut être vide ou nul, pour afficher l’historique d’utilisation de l’attribution en détail.

:::image type="content" source="./media/create-sql-license-assignments/assignment-utilization-view.png" alt-text="Capture d’écran montrant les détails d’utilisation de l’attribution." lightbox="./media/create-sql-license-assignments/assignment-utilization-view.png" :::

Si l’utilisation d’une attribution de licence est de 100%, il est probable que certaines ressources de l’étendue encourent des frais de paiement à l’utilisation pour SQL Server. Nous vous recommandons d’utiliser à nouveau l’expérience d’attribution de licence pour vérifier la quantité d’utilisation couverte ou non par des licences attribuées. Par la suite, suivez la même procédure que précédemment, notamment en consultant votre service d’approvisionnement ou de gestion des ressources logicielles, en confirmant que des licences supplémentaires sont disponibles et en attribuant les licences.

## <a name="changes-after-license-assignment"></a>Modifications après l’attribution des licences

Une fois que vous avez créé des attributions de licence SQL, votre expérience avec Azure Hybrid Benefit change dans le portail Azure.

- Toutes les sélections Azure Hybrid Benefit configurées pour les ressources SQL individuelles ne sont plus valables. Elles sont remplacées par l’attribution de licence SQL créée au niveau de l’abonnement ou du compte.
- L’option d’avantage hybride n’apparaît pas comme dans la configuration de votre ressource SQL.
- Les applications ou les scripts qui configurent l’avantage hybride de manière programmatique continuent de fonctionner, mais le paramètre n’a aucun effet.
- Les remises sur les logiciels SQL sont appliquées aux ressources SQL dans l’étendue. L’étendue est basée sur le nombre de licences dans les attributions de licence qui sont créées pour l’abonnement du compte où la ressource a été créée.
- Une ressource spécifique configurée pour un avantage hybride peut ne pas bénéficier de la remise si d’autres ressources consomment toutes les licences. Toutefois, la remise maximale est appliquée à l’étendue, en fonction du nombre de licences. Pour plus d’informations sur l’application des remises, consultez [Qu’est-ce qu’Azure Hybrid Benefit managé de manière centralisée ?](overview-azure-hybrid-benefit-scope.md)

## <a name="cancel-a-license-assignment"></a>Annuler une attribution de licence

Examinez votre situation en matière de licences avant d’annuler vos attributions de licence. Lorsque vous annulez une attribution de licence, vous ne bénéficiez plus de remises pour celles-ci. Par conséquent, votre facture Azure risque d’augmenter. Si vous annulez la dernière attribution de licence restante, la gestion d’Azure Hybrid Benefit revient au niveau des ressources individuelles.

### <a name="to-cancel-a-license-assignment"></a>Pour annuler une attribution de licence

1. Dans la liste des réservations et des attributions de licence, sélectionnez une attribution de licence.
1. Sur la page des détails de l’attribution de licence, sélectionnez **Annuler**.  
    :::image type="content" source="./media/create-sql-license-assignments/cancel-assignment-symbol.png" alt-text="Capture d’écran montrant l’option Annuler." :::
1. Sur la page Annuler, examinez la notification, puis sélectionnez **Oui, annuler**.  
    :::image type="content" source="./media/create-sql-license-assignments/cancel-assignment.png" alt-text="Capture d’écran montrant la page Annuler." lightbox="./media/create-sql-license-assignments/cancel-assignment.png" :::

## <a name="next-steps"></a>Étapes suivantes

- Passez en revue les [questions fréquentes (FAQ) sur Azure Hybrid Benefit managé de manière centralisée](faq-azure-hybrid-benefit-scope.yml).
- Découvrez la façon dont les remises sont appliquées dans [Qu’est-ce qu’Azure Hybrid Benefit managé de manière centralisée ?](overview-azure-hybrid-benefit-scope.md)