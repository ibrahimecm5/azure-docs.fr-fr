---
title: Prendre connaissance de l’authentification unique avec une application locale à l’aide du proxy d’application
description: Prenez connaissance de l’authentification unique avec une application locale utilisant le proxy d’application.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: conceptual
ms.date: 04/27/2021
ms.author: kenwith
ms.reviewer: ashishj, asteen
ms.openlocfilehash: 420851665b1f32a99779b10dcef7ed3a6079e3d1
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129990156"
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Comment configurer l’authentification unique pour une application de proxy d’application

L’authentification unique (SSO) permet à vos utilisateurs d’accéder à une application sans avoir à s’authentifier plusieurs fois. Elle assure une authentification unique dans le cloud auprès d’Azure Active Directory et permet au service ou au connecteur d’emprunter l’identité de l’utilisateur pour effectuer toute autre demande d’authentification à partir de l’application.

## <a name="how-to-configure-single-sign-on"></a>Comment configurer l’authentification unique
Pour configurer l’authentification SSO, assurez-vous d’abord que votre application est configurée pour la pré-authentification par le biais d’Azure Active Directory. Pour effectuer cette configuration, sélectionnez **Azure Active Directory** -&gt; **Applications d’entreprise** -&gt; **Toutes les applications** -&gt; Votre application **-&gt; Proxy d’application**. Sur cette page, assurez-vous que le champ « Pré-authentification » est défini sur « Azure Active Directory ». 

Pour plus d’informations sur les méthodes de pré-authentification, reportez-vous à l’étape 4 du [document sur la publication d’applications](application-proxy-add-on-premises-application.md).

   ![Méthode de pré-authentification dans le Portail Azure](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Configuration des modes d’authentification unique pour les applications de proxy d’application
Configurez le type d’authentification unique spécifique. Les méthodes d’authentification sont classées en fonction du type d’authentification utilisé par l’application principale. Les applications de proxy d’application prennent en charge trois types d’authentification :

-   **Authentification par mot de passe** : l’authentification par mot de passe peut être utilisée pour toute application présentant des champs de nom d’utilisateur et de mot de passe pour l’authentification. Les étapes de configuration se trouvent dans [Configurer l’authentification unique par mot de passe pour une application de la galerie Azure AD](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md).

-   **Authentification Windows intégrée** : pour les applications utilisant l’authentification Windows intégrée, l’authentification unique est activée par le biais de la délégation Kerberos contrainte (KCD, Kerberos Constrained Delegation). Cette méthode donne aux connecteurs de proxy d’application l’autorisation d’emprunter l’identité des utilisateurs dans Active Directory et d’envoyer et recevoir des jetons en leur nom. Vous trouverez plus d’informations sur la configuration de KCD dans la [documentation sur l’authentification unique avec KCD](application-proxy-configure-single-sign-on-with-kcd.md).

-   **Authentification basée sur l’en-tête** : L’authentification basée sur l’en-tête est utilisée pour fournir des fonctionnalités d’authentification unique à l’aide d’en-têtes HTTP. Pour plus d’informations, consultez [Authentification unique basée sur l’en-tête](application-proxy-configure-single-sign-on-with-headers.md).

-   **Authentification unique SAML** : Avec l’authentification unique SAML, Azure AD s’authentifie dans l’application en utilisant le compte Azure AD de l’utilisateur. Azure AD communique les informations d’authentification à l’application via un protocole de connexion. Avec l’authentification unique SAML, vous pouvez mapper les utilisateurs à des rôles d’application spécifiques en fonction de règles que vous définissez dans vos revendications SAML. Pour plus d’informations sur la configuration de l’authentification unique SAML, voir [SAML pour l’authentification unique avec le proxy d’application](application-proxy-configure-single-sign-on-on-premises-apps.md).

Chacune de ces options est accessible dans votre application sous **Applications d’entreprise** (ouvrez la page **Authentification unique** dans le menu de gauche). Notez que, si votre application a été créée dans l’ancien portail, vous ne verrez peut-être pas toutes ces options.

Cette page propose une autre option d’authentification : l’authentification liée. Cette option est également prise en charge par le proxy d’application. Toutefois, cette option n’ajoute pas l’authentification unique à l’application. Cela étant dit, il se peut que l’authentification unique soit déjà implémentée dans l’application par le biais d’un autre service tel qu’Active Directory Federation Services. 

Cette option permet à l’administrateur de créer un lien vers une application présentée préalablement aux utilisateurs lorsqu’ils accèdent à l’application. Par exemple, s’il existe une application qui est configurée pour authentifier les utilisateurs avec Active Directory Federation Services 2.0, l’administrateur peut utiliser l’option d’authentification liée pour créer un lien vers cette application dans Mes applications.

## <a name="next-steps"></a>Étapes suivantes
- [Authentification unique avec le proxy d’application](application-proxy-configure-single-sign-on-password-vaulting.md)
- [Délégation contrainte Kerberos pour l’authentification unique à vos applications avec le proxy d’application](application-proxy-configure-single-sign-on-with-kcd.md)
- [Authentification basée sur l’en-tête pour une authentification unique avec le Proxy d’application et PingAccess](application-proxy-configure-single-sign-on-with-headers.md) 
- [SAML pour l’authentification unique avec le proxy d’application](application-proxy-configure-single-sign-on-on-premises-apps.md)
