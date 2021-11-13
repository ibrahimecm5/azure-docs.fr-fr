---
title: Accorder le consentement administrateur au niveau locataire à une application
titleSuffix: Azure AD
description: Découvrez comment accorder un consentement au niveau locataire à une application afin d’éviter que les utilisateurs finaux donnent leur consentement lors de la connexion à une application.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/23/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 486063ba11c0b5854889ee22ef554bcab310ea26
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131447965"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application-in-azure-active-directory"></a>Octroi du consentement administrateur au niveau locataire à une application dans Azure Active Directory

  Dans cet article, vous allez apprendre à accorder le consentement administrateur au niveau du locataire à une application dans Azure Active Directory (Azure AD).

Lorsque vous accordez un consentement administrateur au niveau du locataire à une application, tous les utilisateurs peuvent se connecter à l’application. Pour restreindre à des utilisateurs spécifiques l’accès à une application, configurez l’application pour demander l’affectation d’utilisateurs, puis affectez des utilisateurs ou des groupes à l’application. Pour plus d'informations, voir [Méthodes d'affectation d'utilisateurs et de groupes](./assign-user-or-group-access-portal.md).

Le consentement administrateur au niveau locataire à une application permet à l’application et à l’éditeur de l'application d’accéder aux données de votre organisation. Examinez attentivement les autorisations demandées par l’application avant d’accorder le consentement. Pour plus d’informations sur le consentement des applications, consultez [Infrastructure de consentement d’Azure Active Directory](../develop/consent-framework.md).

L’octroi du consentement administrateur au niveau du locataire peut révoquer toutes les autorisations accordées précédemment à l’ensemble du locataire. Les autorisations qui ont été précédemment accordées par les utilisateurs en leur nom propre ne seront pas concernées.

## <a name="prerequisites"></a>Prérequis

Pour accorder le consentement administrateur au niveau locataire, vous devez vous connecter comme utilisateur autorisé à donner un consentement au nom de l’organisation.

Pour accorder le consentement de l’administrateur au niveau locataire, vous devez avoir :

- Un compte Azure avec l’un des rôles suivants : Administrateur général, Administrateur de rôle privilégié, Administrateur d’application cloud ou Administrateur d’application. Un utilisateur peut également être autorisé à accorder le consentement au niveau locataire si un [rôle d’annuaire personnalisé](../roles/custom-create.md) comprenant l’[autorisation d’accorder des autorisations à des applications](../roles/custom-consent-permissions.md) lui est attribué.

## <a name="grant-tenant-wide-admin-consent-in-enterprise-apps"></a>Accorder le consentement de l’administrateur au niveau locataire dans les applications d’entreprise

Vous pouvez accorder le consentement administrateur au niveau locataire via *Applications d’entreprise* si l’application a déjà été approvisionnée dans votre locataire. Par exemple, une application peut être approvisionnée dans votre locataire si au moins un utilisateur a déjà donné son consentement à l’application. Pour plus d’informations, consultez [Comment et pourquoi les applications sont ajoutées à Azure Active Directory](../develop/active-directory-how-applications-are-added.md).

Pour accorder le consentement administrateur au niveau locataire à une application figurant dans **Applications d’entreprise** :

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec l’un des rôles listés dans la section des prérequis.
1. Sélectionnez **Azure Active Directory**, puis **Applications d’entreprise**.
1. Sélectionnez l’application à laquelle vous souhaitez accorder le consentement administrateur au niveau locataire et sélectionnez **Autorisations**.
   :::image type="content" source="media/grant-tenant-wide-admin-consent/grant-tenant-wide-admin-consent.png" alt-text="Capture d’écran montrant comment accorder le consentement administrateur au niveau locataire.":::

1. Examinez attentivement les autorisations requises par l’application. Si vous acceptez les autorisations requises par l’application, sélectionnez **Accorder le consentement administrateur**.

## <a name="grant-admin-consent-in-app-registrations"></a>Accorder le consentement administrateur dans les inscriptions d’applications

Pour les applications développées par votre organisation ou inscrites directement dans votre locataire Azure AD, vous pouvez également accorder le consentement administrateur au niveau locataire à partir de **Inscriptions d’applications** dans le Portail Azure.

Pour accorder le consentement administrateur au niveau locataire dans **Inscriptions d'applications** :

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec l’un des rôles listés dans la section des prérequis.
1. Sélectionnez **Azure Active Directory**, puis **Inscriptions d’applications**.
1. Sélectionnez l’application à laquelle vous souhaitez accorder le consentement administrateur au niveau locataire.
1. Sélectionnez **Autorisations de l’API**.
1. Examinez attentivement les autorisations requises par l’application. Si vous acceptez, sélectionnez **Accorder le consentement administrateur**.

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>Construire l’URL pour accorder le consentement de l’administrateur au niveau du locataire

Lorsque vous accordez le consentement administrateur au niveau du locataire à l’aide de l’une des méthodes décrites ci-dessus, une fenêtre s’ouvre à partir du Portail Azure pour demander le consentement administrateur au niveau locataire. Si vous connaissez l’ID client (également appelé ID d’application) de l’application, vous pouvez générer la même URL pour accorder le consentement administrateur au niveau locataire.

L’URL de consentement administrateur au niveau locataire suit le format suivant :

```http
https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}
```

où :

- `{client-id}` est l’ID client de l’application (également appelé ID d’application).
- `{tenant-id}` est l’ID de locataire de votre organisation ou un nom de domaine vérifié.

Comme toujours, examinez attentivement les autorisations demandées par une application avant d’accorder le consentement.

## <a name="next-steps"></a>Étapes suivantes

[Configurer le consentement de l’utilisateur final pour une application](configure-user-consent.md)

[Configurer le workflow du consentement administrateur](configure-admin-consent-workflow.md)
