---
title: Inviter des utilisateurs internes sur B2B Collaboration - Azure AD
description: Si vous disposez de comptes d’utilisateurs internes pour les partenaires, les distributeurs, les fournisseurs, les prestataires et autres invités, vous pouvez passer à Azure AD B2B Collaboration en les invitant à se connecter avec leurs propres informations d’identification ou informations de connexion externes. Utilisez PowerShell ou l’API d’invitation Microsoft Graph.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 09/10/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: a696b7c95129f14182e0b0e59161ce49c00566c8
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892283"
---
# <a name="invite-internal-users-to-b2b-collaboration"></a>Inviter des utilisateurs internes sur B2B Collaboration

Avant la mise à disposition d’Azure AD B2B Collaboration, les organisations pouvaient collaborer avec les distributeurs, les fournisseurs, les prestataires et autres utilisateurs invités en définissant leurs informations d’identification internes. Si vous avez de tels utilisateurs invités internes, vous pouvez les inviter à utiliser B2B Collaboration à la place. Ces utilisateurs invités B2B peuvent utiliser leurs propres identités et informations d’identification pour se connecter. Vous n’avez donc pas besoin de conserver des mots de passe ou de gérer les cycles de vie des comptes.

L’envoi d’une invitation à un compte interne existant vous permet de conserver l’ID d’objet, l’UPN, les appartenances aux groupes ainsi que les affectations d’applications de l’utilisateur. Vous n’avez pas besoin de supprimer et de réinviter manuellement l’utilisateur ou de réaffecter des ressources. Pour inviter l’utilisateur, vous utilisez l’API d’invitation afin de transmettre à la fois l’objet utilisateur interne et l’adresse e-mail de l’utilisateur invité avec l’invitation. Quand l’utilisateur accepte l’invitation, le service B2B remplace l’objet utilisateur interne existant par un utilisateur B2B. Par la suite, l’utilisateur doit se connecter aux services de ressources cloud à l’aide de ses informations d’identification B2B.

## <a name="things-to-consider"></a>Points importants à prendre en compte

- **Accès aux ressources locales** : une fois que l’utilisateur est invité à B2B Collaboration, il peut toujours utiliser ses informations d’identification internes pour accéder aux ressources locales. Vous pouvez éviter cela en réinitialisant ou en modifiant le mot de passe du compte interne. L’exception est l’[Authentification par envoi d’un code secret à usage unique par e-mail](one-time-passcode.md). Si la méthode d’authentification de l’utilisateur est un code secret à usage unique, l’utilisateur ne peut plus utiliser ses informations d’identification internes.

- **Facturation** : cette fonctionnalité ne modifie pas le UserType de l’utilisateur. Par conséquent, elle ne change pas automatiquement le modèle de facturation de l’utilisateur en [tarification d’utilisateur actif mensuel (MAU) d’External Identities](external-identities-pricing.md). Pour activer la tarification MAU pour l’utilisateur, modifiez le UserType de l’utilisateur en `guest`. Notez également que votre locataire Azure AD doit être [lié à un abonnement Azure](external-identities-pricing.md#link-your-azure-ad-tenant-to-a-subscription) pour activer la facturation MAU.

- **L’invitation est unidirectionnelle** : Vous pouvez inviter des utilisateurs internes à utiliser B2B Collaboration, mais vous ne pouvez pas supprimer les informations d’identification B2B une fois qu’elles ont été ajoutées. Pour rétablir l’utilisateur en tant qu’utilisateur interne uniquement, vous devez supprimer l’objet utilisateur et en créer un autre.

- **Teams** : quand l’utilisateur accède à Teams à l’aide de ses informations d’identification externes, son locataire n’est pas disponible initialement dans le sélecteur de locataire de Teams. L’utilisateur peut accéder à Teams à l’aide d’une URL contenant le contexte du locataire, par exemple : `https://teams.microsoft.com/?tenantId=<TenantId>`. Ensuite, le locataire devient disponible dans le sélecteur de locataire de Teams.

- **Utilisateurs synchronisés localement** : Pour les comptes d’utilisateurs synchronisés localement et dans le cloud, l’annuaire local reste la source d’autorité une fois que les utilisateurs ont été invités à accéder à B2B Collaboration. Tous les changements que vous apportez au compte local sont synchronisés avec le compte cloud, notamment la désactivation ou la suppression du compte. Vous ne pouvez donc pas empêcher l’utilisateur de se connecter à son compte local tout en conservant son compte cloud, en supprimant simplement le compte local. À la place, vous pouvez affecter un GUID aléatoire ou toute autre valeur inconnue au mot de passe du compte local.

> [!NOTE]
> Dans la synchronisation Azure AD Connect, il existe une règle par défaut qui écrit l’[attribut onPremisesUserPrincipalName](../hybrid/reference-connect-sync-attributes-synchronized.md#notes) dans l’objet utilisateur. Étant donné que la présence de cet attribut peut empêcher un utilisateur de se connecter à l’aide d’informations d’identification externes, nous bloquons les conversions d’informations internes en informations externes pour les objets utilisateur dotés de cet attribut. Si vous utilisez Azure AD Connect et que vous souhaitez être en mesure d’inviter des utilisateurs internes à B2B Collaboration, vous devez [modifier la règle par défaut](../hybrid/how-to-connect-sync-change-the-configuration.md) afin que l’attribut onPremisesUserPrincipalName ne soit pas écrit dans l’objet utilisateur.
## <a name="how-to-invite-internal-users-to-b2b-collaboration"></a>Guide pratique pour inviter des utilisateurs internes sur B2B Collaboration

Vous pouvez utiliser PowerShell ou l’API d’invitation pour envoyer une invitation B2B à l’utilisateur interne. Vérifiez que l’adresse e-mail de l’invitation est définie en tant qu’adresse e-mail externe pour l’objet utilisateur interne.

- Vous devez utiliser l’adresse e-mail de la propriété User.Mail pour l’invitation.
- Le domaine figurant dans la propriété Mail de l’utilisateur doit correspondre au compte dont il se sert pour se connecter. Sinon, certains services tels que Teams ne pourront pas authentifier l’utilisateur.

Par défaut, l’invitation envoie à l’utilisateur un e-mail l’informant qu’il a été invité, mais vous pouvez supprimer cet e-mail et envoyer le vôtre à la place.

> [!NOTE]
> Pour envoyer votre propre e-mail ou toute autre communication, vous pouvez utiliser `New-AzureADMSInvitation` avec `-SendInvitationMessage:$false` afin d’inviter les utilisateurs en mode silencieux, puis envoyer votre propre e-mail à l’utilisateur converti. Consultez [API et personnalisation d’Azure AD B2B Collaboration](customize-invitation-api.md).

## <a name="use-powershell-to-send-a-b2b-invitation"></a>Utiliser PowerShell pour envoyer une invitation B2B

Vous aurez besoin du module Azure AD PowerShell, version 2.0.2.130 ou ultérieure. Utilisez la commande suivante pour effectuer une mise à jour vers le dernier module Azure AD PowerShell et inviter l’utilisateur interne à B2B Collaboration :

```powershell
Uninstall-Module AzureAD
Install-Module AzureAD
$ADGraphUser = Get-AzureADUser -objectID "UPN of Internal User"
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser
```

## <a name="use-the-invitation-api-to-send-a-b2b-invitation"></a>Utiliser l’API d’invitation pour envoyer une invitation B2B

L’exemple ci-dessous montre comment appeler l’API d’invitation pour inviter un utilisateur interne en tant qu’utilisateur B2B.

```json
POST https://graph.microsoft.com/v1.0/invitations
Authorization: Bearer eyJ0eX...
ContentType: application/json
{
    "invitedUserEmailAddress": "<<external email>>",
    "sendInvitationMessage": true,
    "invitedUserMessageInfo": {
        "messageLanguage": "en-US",
        "ccRecipients": [
            {
                "emailAddress": {
                    "name": null,
                    "address": "<<optional additional notification email>>"
                }
            }
        ],
        "customizedMessageBody": "<<custom message>>"
    },
    "inviteRedirectUrl": "https://myapps.microsoft.com?tenantId=",
    "invitedUser": {
        "id": "<<ID for the user you want to convert>>"
    }
}
```

La réponse à l’API est la même que celle obtenue quand vous invitez un nouvel utilisateur invité à accéder à l’annuaire.
## <a name="next-steps"></a>Étapes suivantes

- [Utilisation d’une invitation B2B Collaboration](redemption-experience.md)
