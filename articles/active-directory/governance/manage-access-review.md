---
title: Gérer l’accès à l’aide des révisions d’accès – Azure AD
description: Découvrez comment gérer les accès utilisateurs et invités en tant qu’appartenance à un groupe ou en tant qu’affectation à une application avec les révisions d’accès Azure Active Directory.
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 08/20/2021
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cf6b15b68c864c3f98d3a4866931148cfcd6411
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097614"
---
# <a name="manage-user-and-guest-user-access-with-access-reviews"></a>Gérer l’accès des utilisateurs et des utilisateurs invités à l’aide des révisions d’accès – Azure AD
 
Grâce à Azure Active Directory (Azure AD), vous pouvez facilement vous assurer que les utilisateurs ou les invités ont un accès approprié. Vous pouvez demander directement aux utilisateurs ou bien à un décisionnaire de prendre part à une révision d’accès et de renouveler la certification (ou d’effectuer une « attestation ») pour l’accès utilisateur. Les réviseurs peuvent donner leur avis sur les besoins de chaque utilisateur en matière d’accès continu, en fonction des suggestions d’Azure AD. Lorsqu’une révision d’accès est terminée, vous pouvez effectuer des modifications et supprimer l’accès des utilisateurs qui n’en ont plus besoin.
 
> [!NOTE]
> Cet article explique comment réaliser des révisions d’accès pour les utilisateurs et les applications. Pour obtenir des informations sur la réalisation d’une révision d’accès pour plusieurs ressources dans des packages d’accès, consultez ici [Révision de l’accès d’un package d’accès dans la gestion des droits d’utilisation Azure AD](entitlement-management-access-reviews-review-access.md). Si vous souhaitez réviser l’accès du principal du service ou de l’utilisateur aux rôles de ressources Azure AD ou Azure, consultez [Démarrer une révision d’accès dans Azure AD Privileged Identity Management](../privileged-identity-management/pim-how-to-start-security-review.md).
 
## <a name="prerequisites"></a>Prérequis
 
- Azure AD Premium P2
 
Pour plus d’informations, consultez [Exigences des licences](access-reviews-overview.md#license-requirements).
 
## <a name="create-and-perform-an-access-review-for-users"></a>Créer et exécuter une révision d’accès pour des utilisateurs
Tout d’abord, un des rôles suivants doit vous être attribué :
- Administrateur général
- Administrateur d’utilisateurs
- Administrateur Identity Governance 
- Administrateur de rôle privilégié (pour les révisions de groupes assignables à un rôle uniquement)
- (Préversion) Propriétaire de groupe de sécurité AAD ou Microsoft 365 pour le groupe à réviser 

Accédez ensuite à la [page Gouvernance des identités](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) pour vous assurer que les révisions d’accès sont prêtes pour votre organisation.

Vous pouvez avoir un ou plusieurs utilisateurs comme réviseurs dans une révision d’accès.  
 
1. Sélectionnez un groupe dans Azure AD comprenant un ou plusieurs membres. Ou bien sélectionnez une application connectée à Azure AD ayant un ou plusieurs utilisateurs attribués. 
 
2. Décidez si vous souhaitez que chaque utilisateur révise son propre accès, ou si vous voulez qu’un ou plusieurs utilisateurs révisent l’accès de tout le monde.
 
3. Dans l’un des rôles listés ci-dessus, accédez à la [page Gouvernance des identités](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).
 
4. Créer la révision d’accès. Pour plus d’informations, consultez [Créer une révision d’accès de groupes ou d’applications](create-access-review.md).
 
5. Lorsque la révision d’accès démarre, demandez aux réviseurs de donner leur avis. Par défaut, chacun d’eux reçoit un e-mail provenant d’Azure AD et contenant un lien vers le panneau d’accès, dans lequel ils peuvent [réviser l’accès à des groupes ou à des applications](self-access-review.md).
 
6. Si les réviseurs n’ont pas donné leur avis, vous pouvez demander à Azure AD de leur envoyer un rappel. Par défaut, Azure AD envoie automatiquement un rappel à mi-parcours de l’échéance fixée pour tous les réviseurs.
 
7. Une fois que les réviseurs ont fait part de leur avis, arrêtez la révision d’accès et appliquez les modifications. Pour plus d’informations, consultez [Effectuer une révision d’accès de groupes ou d’applications](complete-access-review.md).
 
## <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Gérer l’accès invité avec les révisions d’accès Azure AD
 
Avec Azure Active Directory (Azure AD), vous pouvez facilement mettre en place une collaboration au-delà des limites de l’organisation à l’aide de la [fonctionnalité B2B d’Azure AD](../external-identities/what-is-b2b.md). Les utilisateurs invités depuis d’autres locataires peuvent être [invités par les administrateurs](../external-identities/add-users-administrator.md) ou par [d’autres utilisateurs](../external-identities/what-is-b2b.md). Cela s’applique également aux identités de réseaux sociaux tels que des comptes Microsoft.
 
 
 
## <a name="create-and-perform-an-access-review-for-guests"></a>Créer et exécuter une révision d’accès pour des invités
 
Les mêmes rôles nécessaires à la création d’une révision d’accès pour les utilisateurs sont également nécessaires pour créer une révision d’accès pour les invités. Pour plus d’informations, consultez [Créer et exécuter une révision d’accès pour des utilisateurs](manage-access-review.md#create-and-perform-an-access-review-for-users).

Azure AD permet plusieurs scénarios de revue des utilisateurs invités.
 
Vous pouvez réviser :
 
 - Un groupe dans Azure AD comprenant un ou plusieurs invités comme membres.
 - Une application connectée à Azure AD ayant un ou plusieurs utilisateurs invités attribués. 
 
Lors de la révision de l'accès des utilisateurs invités aux groupes Microsoft 365, vous pouvez créer une révision d'accès pour chaque groupe individuellement, ou activer des révisions d'accès automatiques et récurrentes pour les utilisateurs invités dans tous les groupes Microsoft 365. Pour plus d'informations sur les révisions d'accès récurrentes des utilisateurs invités, regardez la vidéo suivante : 
 
> [!VIDEO https://www.youtube.com/embed/3D2_YW2DwQ8]
 
Vous pouvez alors décider de demander à chaque invité de revoir leur propre accès ou de demander à un ou plusieurs utilisateurs de revoir l’accès de chaque invité.
 
 Ces scénarios sont traités dans les sections suivantes.
 
### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>Demander aux invités de revoir leur propre appartenance à un groupe
 
Vous pouvez utiliser les révisions d’accès pour vous assurer que les utilisateurs qui ont été invités et ajoutés à un groupe ont toujours besoin de leur accès. Vous pouvez facilement demander aux invités de revoir leur propre appartenance à ce groupe.
 
1. Pour créer une révision d’accès pour le groupe, choisissez d’y inclure uniquement les utilisateurs invités et faites-leur faire la révision eux-mêmes. Pour plus d’informations, consultez [Créer une révision d’accès de groupes ou d’applications](create-access-review.md).
 
2. Demandez à chaque invité de revoir sa propre appartenance. Par défaut, chaque invité ayant accepté une invitation recevra un message électronique de la part d’Azure AD comprenant un lien vers la révision d’accès. Les instructions d’Azure AD pour les invités concernent la [révision d’accès aux groupes ou aux applications](self-access-review.md).
 
3. Une fois que les réviseurs ont fait part de leur avis, arrêtez la révision d’accès et appliquez les modifications. Pour plus d’informations, consultez [Effectuer une révision d’accès de groupes ou d’applications](complete-access-review.md).
 
4.  En plus des utilisateurs indiquant n’avoir plus besoin d’un accès continu, vous pouvez également retirer les utilisateurs n’ayant pas répondu.
 
5. Si le groupe n’est pas utilisé pour la gestion des accès, vous pouvez également retirer les utilisateurs qui n’ont pas été sélectionnés pour participer à la révision car ils n’ont pas accepté leur invitation. Cela peut être dû à une faute de frappe dans l’adresse e-mail de l’utilisateur invité. Si un groupe est utilisé comme liste de distribution, il est possible que certains utilisateurs invités n’aient pas été sélectionnés pour participer parce qu’ils sont des objets contact.
 
### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>Demander à un commanditaire de revoir l’appartenance d’un invité à un groupe
 
Vous pouvez demander à un commanditaire, tel que le propriétaire d’un groupe, d’évaluer si un invité a toujours besoin d’appartenir à un groupe.
 
1. Pour créer une révision d’accès pour le groupe, choisissez d’y inclure uniquement les utilisateurs invités. Spécifiez ensuite un ou plusieurs réviseurs. Pour plus d’informations, consultez [Créer une révision d’accès de groupes ou d’applications](create-access-review.md).
 
2. Demandez aux réviseurs de donner leur avis. Par défaut, chacun d’eux reçoit un e-mail provenant d’Azure AD et contenant un lien vers le panneau d’accès, dans lequel ils peuvent [réviser l’accès à des groupes ou à des applications](self-access-review.md).
 
3. Une fois que les réviseurs ont fait part de leur avis, arrêtez la révision d’accès et appliquez les modifications. Pour plus d’informations, consultez [Effectuer une révision d’accès de groupes ou d’applications](complete-access-review.md).

> [!NOTE]
>  Vous pouvez empêcher les identités externes de se connecter à votre locataire et supprimer les identités externes de votre locataire après 30 jours. Durant cette période, les paramètres, les résultats, les réviseurs ou les journaux d’audit dans le cadre de la révision actuelle ne seront pas visibles ou configurables. Pour plus d’informations, consultez [Désactiver et supprimer des identités externes à l’aide des révisions d’accès Azure AD](access-reviews-external-users.md#disable-and-delete-external-identities-with-azure-ad-access-reviews).

 
### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>Demander aux invités de revoir leur propre accès à une application
 
Vous pouvez utiliser les révisions d’accès pour vous assurer que les utilisateurs qui ont été invités pour une application spécifique ont toujours besoin de leur accès. Vous pouvez facilement demander aux invités de revoir eux-mêmes leur besoin d’accès.
 
1. Pour créer une révision d’accès pour l’application, choisissez d’y inclure uniquement les invités et de leur faire revoir leur accès eux-mêmes. Pour plus d’informations, consultez [Créer une révision d’accès de groupes ou d’applications](create-access-review.md).
 
2. Demandez à chaque invité de revoir son propre accès à l’application. Par défaut, chaque invité ayant accepté une invitation recevra un message électronique de la part d’Azure AD. Ce message contient un comprenant un lien vers la révision d’accès dans le volet d’accès de votre organisation. Les instructions d’Azure AD pour les invités concernent la [révision d’accès aux groupes ou aux applications](self-access-review.md).
 
3. Une fois que les réviseurs ont fait part de leur avis, arrêtez la révision d’accès et appliquez les modifications. Pour plus d’informations, consultez [Effectuer une révision d’accès de groupes ou d’applications](complete-access-review.md).
 
4. En plus des utilisateurs indiquant n’avoir plus besoin d’un accès continu, vous pouvez également supprimer les utilisateurs invités qui n’ont pas répondu. Vous pouvez également supprimer les utilisateurs invités qui n’ont pas été sélectionnées pour participer, surtout s’ils n’ont pas été récemment invités. N’ayant pas accepté leur invitation, ces utilisateurs n’avaient donc pas accès à l’application. 
 
### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>Demander à un commanditaire de revoir l’accès d’un invité à une application
 
Vous pouvez demander à un commanditaire, tel que le propriétaire d’une application, de revoir le besoin de l’invité à disposer d’un accès continu à l’application.
 
1. Pour créer une révision d’accès pour l’application, choisissez d’y inclure uniquement les invités. Définissez ensuite un ou plusieurs utilisateurs comme réviseurs. Pour plus d’informations, consultez [Créer une révision d’accès de groupes ou d’applications](create-access-review.md).
 
2. Demandez aux réviseurs de donner leur avis. Par défaut, chacun d’eux reçoit un e-mail provenant d’Azure AD et contenant un lien vers le panneau d’accès, dans lequel ils peuvent [réviser l’accès à des groupes ou à des applications](self-access-review.md).
 
3. Une fois que les réviseurs ont fait part de leur avis, arrêtez la révision d’accès et appliquez les modifications. Pour plus d’informations, consultez [Effectuer une révision d’accès de groupes ou d’applications](complete-access-review.md).
 
### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>Demander aux invités de revoir leurs besoins d’accès en général
 
Dans certaines organisations, les invités peuvent ne pas être conscients de leur appartenance à un groupe.
 
 
1. Créez un groupe de sécurité dans Azure AD avec les invités comme membres, si un groupe approprié n’existe pas déjà. Par exemple, vous pouvez créer un groupe pour lequel l’appartenance des invités est gérée manuellement. Ou bien, vous pouvez créer un groupe dynamique doté d’un nom tel que « Invités de Contoso » pour les utilisateurs dans le client Contoso dont la valeur d’attribut UserType est configurée sur Invité. N’oubliez pas qu’un utilisateur invité membre du groupe peut voir les autres membres du groupe.
 
2. Pour créer une révision d’accès pour ce groupe, choisissez les membres comme réviseurs. Pour plus d’informations, consultez [Créer une révision d’accès de groupes ou d’applications](create-access-review.md).
 
3. Demandez à chaque invité de revoir sa propre appartenance. Par défaut, chaque invité ayant accepté une invitation recevra un message électronique de la part d’Azure AD comprenant un lien vers la révision d’accès dans le panneau d’accès de votre organisation. Les instructions d’Azure AD pour les invités concernent la [révision d’accès aux groupes ou aux applications](perform-access-review.md). 
 
4. Une fois que les réviseurs ont fait part de leur avis, arrêtez la révision d’accès. Pour plus d’informations, consultez [Effectuer une révision d’accès de groupes ou d’applications](complete-access-review.md).
 
5. Retirez l’accès invité aux invités ayant été refusés, n’ayant pas terminé la révision ou bien n’ayant pas accepté leur invitation. Si certains des invités sont des contacts sélectionnés pour participer à la révision ou s’ils n’avaient précédemment pas accepté une invitation, vous pouvez désactiver leur compte à l’aide du portail Azure ou de PowerShell. Si l’invité n’a plus besoin d’un accès et n’est pas un contact, vous pouvez supprimer son objet utilisateur de votre répertoire à l’aide du portail Azure ou de PowerShell pour supprimer l’objet utilisateur invité.

 
## <a name="next-steps"></a>Étapes suivantes
 
[Créer une révision d’accès de groupes ou d’applications](create-access-review.md)


