---
title: Attribuer des rôles à des noms de principal du service Contrat Entreprise Azure
description: Cet article vous aide à attribuer des rôles aux noms de principal de service à l’aide de PowerShell et des API REST.
author: bandersmsft
ms.reviewer: sapnakeshari
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/22/2021
ms.author: banders
ms.openlocfilehash: 65f37b3f071286d143dc83674a7c7108a37cff44
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130260376"
---
# <a name="assign-roles-to-azure-enterprise-agreement-service-principal-names"></a>Attribuer des rôles à des noms de principal du service Contrat Entreprise Azure

Vous pouvez gérer votre inscription Contrat Entreprise (EA) dans [Microsoft Azure Enterprise Portal](https://ea.azure.com/). Un client Entreprise direct peut désormais gérer l’inscription d’Accord Entreprise (EA) dans le [portail Azure](https://portal.azure.com/).
Vous pouvez créer différents rôles pour gérer votre organisation, afficher les coûts et créer des abonnements. Cet article vous aide à automatiser certaines de ces tâches en utilisant Azure PowerShell et les API REST avec des noms de principal de service (SPN) Azure.

Avant de commencer, assurez-vous que vous êtes familiarisé avec les articles suivants :

- [Rôles du contrat Entreprise](understand-ea-roles.md)
- [Se connecter avec Azure PowerShell](/powershell/azure/authenticate-azureps)
- [Appeler des API REST avec Postman](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

## <a name="create-and-authenticate-your-service-principal"></a>Créer et authentifier votre principal de service

Pour automatiser les actions EA au moyen d’un SPN, vous devez créer une application Azure Active Directory (Azure AD). Elle peut s’authentifier de manière automatisée.

Suivez les étapes de ces articles pour créer et authentifier votre principal de service.

- [Créer un principal du service](../../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
- [Obtenir les valeurs d’ID de locataire et d’ID d’application pour la connexion](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)

Voici un exemple de page d’inscription d’une application.

:::image type="content" source="./media/assign-roles-azure-service-principals/register-an-application.png" alt-text="Capture d’écran montrant la page Inscrire une application." lightbox="./media/assign-roles-azure-service-principals/register-an-application.png" :::

### <a name="find-your-spn-and-tenant-id"></a>Rechercher votre SPN et votre ID de locataire

Vous avez également besoin de l’ID d’objet du SPN et de l’ID de locataire de l’application. Vous devrez spécifier ces informations pour les opérations d’assignation d’autorisation plus loin dans cet article.

1. Ouvrez Azure Active Directory, puis sélectionnez **Applications d’entreprise**.
1. Recherchez votre application dans la liste.

   :::image type="content" source="./media/assign-roles-azure-service-principals/enterprise-application.png" alt-text="Capture d’écran montrant un exemple d’application d’entreprise." lightbox="./media/assign-roles-azure-service-principals/enterprise-application.png" :::

1. Sélectionnez l’application pour rechercher l’ID d’application et l’ID d’objet associés :

   :::image type="content" source="./media/assign-roles-azure-service-principals/application-id-object-id.png" alt-text="Capture d’écran montrant un ID d’application et un ID d’objet pour une application d’entreprise." lightbox="./media/assign-roles-azure-service-principals/application-id-object-id.png" :::

1. Accédez à la page **Vue d’ensemble** de Microsoft Azure AD pour rechercher l’ID de locataire.

   :::image type="content" source="./media/assign-roles-azure-service-principals/tenant-id.png" alt-text="Capture d’écran montrant l’ID de locataire." lightbox="./media/assign-roles-azure-service-principals/tenant-id.png" :::

>[!NOTE]
>L’ID de locataire peut s’appeler ID de principal, SPN ou ID d’objet selon l’endroit où il apparaît. La valeur de votre ID de locataire Azure AD ressemble à un GUID au format suivant : `11111111-1111-1111-1111-111111111111`.

## <a name="permissions-that-can-be-assigned-to-the-spn"></a>Autorisations qui peuvent être attribuées au SPN

Plus loin dans cet article, vous donnerez à l’application Azure AD l’autorisation d’effectuer des actions en utilisant un rôle EA. Vous pouvez uniquement attribuer les rôles suivants au SPN, et vous devez fournir l’ID de définition de rôle exactement comme indiqué.

| Rôle | Actions autorisées | ID de définition de rôle |
| --- | --- | --- |
| EnrollmentReader | Permet d’afficher l’utilisation et les frais pour tous les comptes et abonnements. Permet d’afficher le solde du paiement anticipé Azure (précédemment appelé engagement financier) associé à l’inscription. | 24f8edb6-1668-4659-b5e2-40bb5f3a7d7e |
| Acheteur EA | Acheter des ordres de réservation et afficher les transactions de réservation. Permet d’afficher l’utilisation et les frais pour tous les comptes et abonnements. Permet d’afficher le solde du paiement anticipé Azure (précédemment appelé engagement financier) associé à l’inscription. | da6647fb-7651-49ee-be91-c43c4877f0c4  |
| DepartmentReader | Télécharger les détails d’utilisation pour le service qu’il administre. Permet d’afficher l’utilisation et les frais associés à son service. | db609904-a47f-4794-9be8-9bd86fbffd8a |
| SubscriptionCreator | Créer des abonnements dans l’étendue de compte donnée. | a0bcee42-bf30-4d1b-926a-48d21664ef71 |

- Un rôle EnrollmentReader peut être attribué à un SPN uniquement par un utilisateur qui a le rôle d’auteur d’inscription.
- Un rôle DepartmentReader peut être attribué à un SPN uniquement par un utilisateur qui a le rôle d’auteur d’inscription ou d’auteur de service.
- Un rôle SubscriptionCreator peut être attribué à un SPN uniquement par un utilisateur qui est propriétaire du compte d’inscription. Le rôle n’apparaît pas dans le portail EA. Il est créé programmatiquement et ne sert qu’à des fins de programmation.
- Le rôle Acheteur EA n’apparaît pas dans le portail EA. Il est créé programmatiquement et ne sert qu’à des fins de programmation.

## <a name="assign-enrollment-account-role-permission-to-the-spn"></a>Attribuer l’autorisation du rôle de compte d’inscription au SPN

1. Lisez l’article sur l’API REST [Attributions de rôles – PUT](/rest/api/billing/2019-10-01-preview/role-assignments/put). Pendant la lecture de l’article, sélectionnez **Essayer** pour commencer à utiliser le SPN.

   :::image type="content" source="./media/assign-roles-azure-service-principals/put-try-it.png" alt-text="Capture d’écran montrant l’option Essayer dans l’article Put." lightbox="./media/assign-roles-azure-service-principals/put-try-it.png" :::

1. Utilisez les informations d’identification de votre compte pour vous connecter au locataire avec l’accès d’inscription que vous souhaitez attribuer.

1. Fournissez les paramètres suivants dans le cadre de la demande d’API.

   - `billingAccountName` : ce paramètre est l’**ID du compte de facturation**. Vous le trouverez dans le portail Azure, dans la page de présentation de **Cost Management + Billing**.

      :::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="Capture d’écran montrant l’ID du compte de facturation." lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

   - `billingRoleAssignmentName` : ce paramètre est un GUID unique que vous devez fournir. Vous pouvez générer un GUID à l’aide de la commande PowerShell [New-Guid](/powershell/module/microsoft.powershell.utility/new-guid). Vous pouvez aussi utiliser le site web [Online GUID/UUID Generator](https://guidgenerator.com/) pour générer un GUID unique.

   - `api-version` : utilisez la version **2019-10-01-preview**. Utilisez l’exemple de corps de demande fourni dans la section [Exemples dans Attributions de rôles – PUT](/rest/api/billing/2019-10-01-preview/role-assignments/put#examples).

      Le corps de la demande contient du code JSON avec trois paramètres que vous devez utiliser.

      | Paramètre | Comment y accéder |
      | --- | --- |
      | `properties.principalId` | Il s’agit de la valeur de l’ID d’objet. Voir [Rechercher votre SPN et votre ID de locataire](#find-your-spn-and-tenant-id). |
      | `properties.principalTenantId` | Voir [Rechercher votre SPN et votre ID de locataire](#find-your-spn-and-tenant-id). |
      | `properties.roleDefinitionId` | `/providers/Microsoft.Billing/billingAccounts/{BillingAccountName}/billingRoleDefinitions/24f8edb6-1668-4659-b5e2-40bb5f3a7d7e` |

      Le nom du compte de facturation est le même paramètre que celui que vous avez utilisé dans les paramètres de l’API. Il s’agit de l’ID d’inscription que vous voyez dans le portail EA et le portail Azure.

      Notez que `24f8edb6-1668-4659-b5e2-40bb5f3a7d7e` est un ID de définition de rôle de facturation pour un EnrollmentReader.

1. Sélectionnez **Exécuter** pour lancer la commande.

   :::image type="content" source="./media/assign-roles-azure-service-principals/roleassignments-put-try-it-run.png" alt-text="Capture d’écran montrant un exemple d’attribution de rôle PUT, avec le bouton Essayer mis en évidence et des exemples d’informations, prêt à être exécuté." lightbox="./media/assign-roles-azure-service-principals/roleassignments-put-try-it-run.png" :::

   Une réponse `200 OK` indique que le SPN a bien été ajouté.

Vous pouvez maintenant utiliser le SPN pour accéder automatiquement aux API EA. Le SPN a le rôle EnrollmentReader.

## <a name="assign-ea-purchaser-role-permission-to-the-spn"></a>Attribuer l’autorisation du rôle d’acheteur EA au SPN

Pour le rôle d’acheteur EA, utilisez les mêmes étapes que pour le rôle de lecteur d’inscription. Spécifiez le paramètre `roleDefinitionId`, comme indiqué dans l’exemple suivant :

`"/providers/Microsoft.Billing/billingAccounts/1111111/billingRoleDefinitions/ da6647fb-7651-49ee-be91-c43c4877f0c4"`

## <a name="assign-the-department-reader-role-to-the-spn"></a>Attribuer le rôle de lecteur de service au SPN

1. Lisez cet article sur l’API REST : [Attributions de rôles de service d’inscription – Put](/rest/api/billing/2019-10-01-preview/enrollment-department-role-assignments/put). Pendant la lecture de l’article, sélectionnez **Essayer**.

   :::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" alt-text="Capture d’écran montrant l’option Essayer dans l’article Attributions de rôles de service d’inscription – PUT." lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" :::

1. Utilisez les informations d’identification de votre compte pour vous connecter au locataire avec l’accès d’inscription que vous souhaitez attribuer.

1. Fournissez les paramètres suivants dans le cadre de la demande d’API.

   - `billingAccountName` : ce paramètre est l’**ID du compte de facturation**. Vous le trouverez dans le portail Azure, dans la page de présentation de **Cost Management + Billing**.

      :::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="Capture d’écran montrant l’ID du compte de facturation." lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

   - `billingRoleAssignmentName` : ce paramètre est un GUID unique que vous devez fournir. Vous pouvez générer un GUID à l’aide de la commande PowerShell [New-Guid](/powershell/module/microsoft.powershell.utility/new-guid). Vous pouvez aussi utiliser le site web [Online GUID/UUID Generator](https://guidgenerator.com/) pour générer un GUID unique.

   - `departmentName` : ce paramètre est l’ID de service. Vous trouverez les ID de service dans le portail Azure, dans la page **Cost Management + Billing** > **Departments**.

      Pour cet exemple, nous avons utilisé le service ACE. L’ID de l’exemple est `84819`.

      :::image type="content" source="./media/assign-roles-azure-service-principals/department-id.png" alt-text="Capture d’écran montrant un exemple d’ID de service." lightbox="./media/assign-roles-azure-service-principals/department-id.png" :::

   - `api-version` : utilisez la version **2019-10-01-preview**. Utilisez l’exemple dans[Attributions de rôles de service d’inscription – PUT](/rest/api/billing/2019-10-01-preview/enrollment-department-role-assignments/put).

      Le corps de la demande contient du code JSON avec trois paramètres que vous devez utiliser.

      | Paramètre | Comment y accéder |
      | --- | --- |
      | `properties.principalId` | Il s’agit de la valeur de l’ID d’objet. Voir [Rechercher votre SPN et votre ID de locataire](#find-your-spn-and-tenant-id). |
      | `properties.principalTenantId` | Voir [Rechercher votre SPN et votre ID de locataire](#find-your-spn-and-tenant-id). |
      | `properties.roleDefinitionId` | `/providers/Microsoft.Billing/billingAccounts/{BillingAccountName}/billingRoleDefinitions/db609904-a47f-4794-9be8-9bd86fbffd8a` |

      Le nom du compte de facturation est le même paramètre que celui que vous avez utilisé dans les paramètres de l’API. Il s’agit de l’ID d’inscription que vous voyez dans le portail EA et le portail Azure.

      L’ID de définition de rôle de facturation `db609904-a47f-4794-9be8-9bd86fbffd8a` est destiné à un lecteur de service.

1. Sélectionnez **Exécuter** pour lancer la commande.

   :::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it-run.png" alt-text="Capture d’écran montrant un exemple d’API REST Attributions de rôles de service d’inscription – PUT, avec le bouton Essayer mis en évidence et des exemples d’informations, prêt à être exécuté." lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it-run.png" :::

   Une réponse `200 OK` indique que le SPN a bien été ajouté.

Vous pouvez maintenant utiliser le SPN pour accéder automatiquement aux API EA. Le SPN a le rôle DepartmentReader.

## <a name="assign-the-subscription-creator-role-to-the-spn"></a>Attribuer le rôle de créateur d’abonnement au SPN

1. Lisez l’article [Attributions de rôles de compte d’inscription – PUT](/rest/api/billing/2019-10-01-preview/enrollment-account-role-assignments/put). Pendant la lecture, sélectionnez **Essayer** pour attribuer le rôle de créateur d’abonnement au SPN.

   :::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" alt-text="Capture d’écran montrant l’option Essayer dans l’article Attributions de rôles de compte d’inscription – PUT." lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" :::

1. Utilisez les informations d’identification de votre compte pour vous connecter au locataire avec l’accès d’inscription que vous souhaitez attribuer.

1. Fournissez les paramètres suivants dans le cadre de la demande d’API. Lisez la section [Paramètres URI dans Attributions de rôles de compte d’inscription – PUT](/rest/api/billing/2019-10-01-preview/enrollment-account-role-assignments/put#uri-parameters).

   - `billingAccountName` : ce paramètre est l’**ID du compte de facturation**. Vous le trouverez dans le portail Azure, dans la page de présentation de **Cost Management + Billing**.

      :::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="Capture d’écran montrant l’ID du compte de facturation." lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

   - `billingRoleAssignmentName` : ce paramètre est un GUID unique que vous devez fournir. Vous pouvez générer un GUID à l’aide de la commande PowerShell [New-Guid](/powershell/module/microsoft.powershell.utility/new-guid). Vous pouvez aussi utiliser le site web [Online GUID/UUID Generator](https://guidgenerator.com/) pour générer un GUID unique.

   - `enrollmentAccountName` : ce paramètre est l’**ID** de compte. Vous trouverez l’ID de compte associé au nom de compte dans le portail Azure, dans la page **Cost Management + Billing**.

      Pour cet exemple, nous avons utilisé le compte GTM Test. L’ID est `196987`.

      :::image type="content" source="./media/assign-roles-azure-service-principals/account-id.png" alt-text="Capture d’écran montrant l’ID de compte." lightbox="./media/assign-roles-azure-service-principals/account-id.png" :::

   - `api-version` : utilisez la version **2019-10-01-preview**. Utilisez l’exemple fourni la section [Exemples dans Attributions de rôles de service d’inscription – PUT](/rest/api/billing/2019-10-01-preview/enrollment-department-role-assignments/put#examples).

      Le corps de la demande contient du code JSON avec trois paramètres que vous devez utiliser.

      | Paramètre | Comment y accéder |
      | --- | --- |
      | `properties.principalId` | Il s’agit de la valeur de l’ID d’objet. Voir [Rechercher votre SPN et votre ID de locataire](#find-your-spn-and-tenant-id). |
      | `properties.principalTenantId` | Voir [Rechercher votre SPN et votre ID de locataire](#find-your-spn-and-tenant-id). |
      | `properties.roleDefinitionId` | `/providers/Microsoft.Billing/billingAccounts/{BillingAccountID}/enrollmentAccounts/196987/billingRoleDefinitions/a0bcee42-bf30-4d1b-926a-48d21664ef71` |

      Le nom du compte de facturation est le même paramètre que celui que vous avez utilisé dans les paramètres de l’API. Il s’agit de l’ID d’inscription qui est indiqué dans le portail EA et le portail Azure.

      L’ID de définition de rôle de facturation `a0bcee42-bf30-4d1b-926a-48d21664ef71` est destiné au rôle de créateur d’abonnement.

1. Sélectionnez **Exécuter** pour lancer la commande.

   :::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-account-role-assignments-put-try-it.png" alt-text="Capture d’écran montrant l’option Essayer dans l’article Attributions de rôles de compte d’inscription – PUT" lightbox="./media/assign-roles-azure-service-principals/enrollment-account-role-assignments-put-try-it.png" :::

   Une réponse `200 OK` indique que le SPN a bien été ajouté.

Vous pouvez maintenant utiliser le SPN pour accéder automatiquement aux API EA. Le SPN a le rôle SubscriptionCreator.

## <a name="troubleshoot"></a>Dépanner

Vous devez identifier et utiliser l’ID d’objet d’application Enterprise auquel vous avez accordé le rôle EA. Si vous utilisez l’ID d’objet d’une autre application, les appels d’API échouent. Vérifiez que vous utilisez le ID d’objet d’application Enterprise correct.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur l’[administration du portail Azure EA](ea-portal-administration.md).
