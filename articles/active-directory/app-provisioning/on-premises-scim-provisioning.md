---
title: Approvisionnement d’applications locales Azure AD vers des applications prenant en charge SCIM
description: Cet article décrit comment utiliser le service d’approvisionnement Azure AD pour approvisionner des utilisateurs dans une application locale compatible avec SCIM.
services: active-directory
author: billmath
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 10/16/2021
ms.author: billmath
ms.reviewer: arvinh
ms.openlocfilehash: 232735af49cd0f38ff169c224a48b76b0707c97d
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130261136"
---
# <a name="azure-ad-on-premises-application-provisioning-to-scim-enabled-apps"></a>Approvisionnement d’applications locales Azure AD vers des applications prenant en charge SCIM

>[!IMPORTANT]
> Actuellement, la préversion du provisionnement local n’est disponible que sur invitation. Pour demander l’accès à la fonctionnalité, utilisez le [formulaire de demande d’accès](https://aka.ms/onpremprovisioningpublicpreviewaccess). Nous allons ouvrir la préversion à un plus grand nombre de clients et de connecteurs au cours des prochains mois, car nous préparons la disponibilité générale.

Le service d’approvisionnement Azure Active Directory (Azure AD) prend en charge un client [SCIM 2.0](https://techcommunity.microsoft.com/t5/identity-standards-blog/provisioning-with-scim-getting-started/ba-p/880010) qui peut être utilisé pour approvisionner automatiquement des utilisateurs dans des applications locales ou dans le cloud. Cet article explique comment vous pouvez utiliser le service d’approvisionnement Azure AD pour approvisionner des utilisateurs dans une application locale compatible avec SCIM. si vous souhaitez approvisionner des utilisateurs dans des applications locales non SCIM qui utilisent SQL en tant que magasin de données, consultez le [didacticiel sur le connecteur SQL générique de l’hôte du connecteur Azure AD ECMA](tutorial-ecma-sql-connector.md). Si vous souhaitez approvisionner des utilisateurs dans des applications cloud, telles que DropBox et Atlassian, passez en revue les [didacticiels](../../active-directory/saas-apps/tutorial-list.md) spécifiques à l’application.

![Diagramme illustrant l’architecture SCIM.](./media/on-premises-scim-provisioning/scim-4.png)

## <a name="prerequisites"></a>Configuration requise
- Locataire Azure AD avec Azure AD Premium P1 ou Premium P2 (ou EMS E3 ou E5). [!INCLUDE [active-directory-p1-license.md](../../../includes/active-directory-p1-license.md)]
- Rôle Administrateur pour l’installation de l’agent. Cette tâche est un effort unique et il doit s’agir d’un compte Azure qui est soit un administrateur hybride, soit un administrateur général. 
- Rôle Administrateur pour la configuration de l’application dans le cloud (administrateur d’application, administrateur d’application cloud, administrateur général ou rôle personnalisé avec autorisations).

## <a name="on-premises-app-provisioning-to-scim-enabled-apps"></a>Approvisionnement d’applications locales vers des applications prenant en charge SCIM
Pour approvisionner des utilisateurs sur des applications SCIM :

 1. Ajoutez l’**application SCIM locale** à partir de la [galerie](../../active-directory/manage-apps/add-application-portal.md).
 1. Accédez à votre application et sélectionnez **Approvisionnement** > **Télécharger l’agent d’approvisionnement**.
 1. Sélectionnez **Connectivité locale** et téléchargez l’agent d’approvisionnement.
 1. Copiez l’agent sur la machine virtuelle ou le serveur sur lequel votre point de terminaison SCIM est hébergé.
 1. Ouvrez le programme d’installation de l’agent d’approvisionnement, acceptez les conditions d’utilisation du service, puis sélectionnez **Installer**.
 1. Ouvrez l’Assistant de l’agent d’approvisionnement et sélectionnez **Approvisionnement local** lorsque vous êtes invité à indiquer l’extension que vous souhaitez activer.
 1. Communiquez les informations d’identification relatives à un administrateur Azure AD lorsque vous êtes invité à donner votre autorisation. L’administrateur hybride ou l’administrateur général est requis.
 1. Sélectionnez **Confirmer** pour valider le succès de l’installation.
 1. Revenez à votre application et sélectionnez **Connectivité locale**.
 1. Dans la liste déroulante, sélectionnez l’agent que vous avez installé, puis sélectionnez **Attribuer des agents**.
 1. Patientez 10 minutes ou redémarrez le service Agent d’approvisionnement Azure AD Connect sur votre serveur ou machine virtuelle.
 1. Indiquez l’URL de votre point de terminaison SCIM dans la zone **URL du locataire**. par exemple https://localhost:8585/scim.
 
     ![Capture d’écran montrant l’affectation d’un agent.](./media/on-premises-scim-provisioning/scim-2.png)
 1. Sélectionnez **Tester la connexion** et enregistrez les informations d’identification.
 1. Configurez les [mappages d’attributs](customize-application-attributes.md) ou les règles d’[étendue](define-conditional-rules-for-provisioning-user-accounts.md) requis pour votre application.
 1. Ajoutez des utilisateurs à l’étendue en [attribuant des utilisateurs et des groupes](../../active-directory/manage-apps/add-application-portal-assign-users.md) à l’application.
 1. Testez l’approvisionnement de quelques utilisateurs [à la demande](provision-on-demand.md).
 1. Ajoutez des utilisateurs supplémentaires dans l’étendue en les attribuant à votre application.
 1. Accédez au volet **Approvisionnement**, puis sélectionnez **Démarrer le provisionnement**.
 1. Analyse à l’aide des [journaux d’approvisionnement](../../active-directory/reports-monitoring/concept-provisioning-logs.md).

## <a name="additional-requirements"></a>Autres conditions requises
* Assurez-vous que votre implémentation [SCIM](https://techcommunity.microsoft.com/t5/identity-standards-blog/provisioning-with-scim-getting-started/ba-p/880010) répond aux [exigences du SCIM Azure AD](use-scim-to-provision-users-and-groups.md).
  
  Azure AD offre un [code de référence](https://github.com/AzureAD/SCIMReferenceCode/wiki) open source que les développeurs peuvent utiliser pour démarrer leur implémentation SCIM. Le code se présente comme suit.
* Prenez en charge le point de terminaison /schemas pour diminuer la configuration requise dans le portail Azure. 

## <a name="next-steps"></a>Étapes suivantes

- [Provisionnement d’applications](user-provisioning.md)
- [Connecteur SQL générique](on-premises-sql-connector-configure.md)
- [Tutoriel : Connecteur SQL générique pour l’Hôte de connecteur ECMA](tutorial-ecma-sql-connector.md)
