---
title: Validation du jeton d’accès au service FHIR
description: Procédure de validation des jetons d’accès et guide de dépannage pour le service FHIR
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 08/05/2021
ms.author: cavoeg
ms.openlocfilehash: 32aec1819e230415b60805c32a78820bfac39a80
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132814588"
---
# <a name="fhir-service-access-token-validation"></a>Validation du jeton d’accès au service FHIR

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

La manière dont le service FHIR dans les API de santé Azure (par le biais du service FHIR) valide le jeton d’accès dépend de l’implémentation et de la configuration. Cet article décrit les étapes de validation qui peuvent être utiles lors de la résolution de problèmes d’accès.

## <a name="validate-token-has-no-issues-with-identity-provider"></a>Valider le fait que le jeton ne rencontre pas de problèmes avec le fournisseur d’identité

La première étape de la validation du jeton consiste à vérifier que le jeton a été émis par le fournisseur d’identité correct et qu’il n’a pas été modifié. Le serveur FHIR sera configuré pour utiliser un fournisseur d’identité spécifique appelé l’autorité `Authority`. Le serveur FHIR récupère les informations sur le fournisseur d’identité à partir du point de terminaison `/.well-known/openid-configuration`. Lorsque vous utilisez Azure AD, l’URL complète est la suivante :

```
GET https://login.microsoftonline.com/<TENANT-ID>/.well-known/openid-configuration
```

où `<TENANT-ID>` est le locataire Azure AD spécifique (un ID de locataire ou un nom de domaine).

Azure AD renvoie un document comme celui ci-dessous au serveur FHIR.

```json
{
    "authorization_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/token",
    "token_endpoint_auth_methods_supported": [
        "client_secret_post",
        "private_key_jwt",
        "client_secret_basic"
    ],
    "jwks_uri": "https://login.microsoftonline.com/common/discovery/keys",
    "response_modes_supported": [
        "query",
        "fragment",
        "form_post"
    ],
    "subject_types_supported": [
        "pairwise"
    ],
    "id_token_signing_alg_values_supported": [
        "RS256"
    ],
    "http_logout_supported": true,
    "frontchannel_logout_supported": true,
    "end_session_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/logout",
    "response_types_supported": [
        "code",
        "id_token",
        "code id_token",
        "token id_token",
        "token"
    ],
    "scopes_supported": [
        "openid"
    ],
    "issuer": "https://sts.windows.net/<TENANT-ID>/",
    "claims_supported": [
        "sub",
        "iss",
        "cloud_instance_name",
        "cloud_instance_host_name",
        "cloud_graph_host_name",
        "msgraph_host",
        "aud",
        "exp",
        "iat",
        "auth_time",
        "acr",
        "amr",
        "nonce",
        "email",
        "given_name",
        "family_name",
        "nickname"
    ],
    "microsoft_multi_refresh_token": true,
    "check_session_iframe": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/checksession",
    "userinfo_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/openid/userinfo",
    "tenant_region_scope": "WW",
    "cloud_instance_name": "microsoftonline.com",
    "cloud_graph_host_name": "graph.windows.net",
    "msgraph_host": "graph.microsoft.com",
    "rbac_url": "https://pas.windows.net"
}
``` 
Les propriétés importantes du serveur FHIR sont `jwks_uri`, ce qui indique au serveur où récupérer les clés de chiffrement nécessaires pour valider la signature du jeton, et `issuer`, ce qui indique au serveur ce qui figurera dans la revendication de l’émetteur (`iss`) des jetons émis par ce serveur. Le serveur FHIR peut utiliser cela pour vérifier qu’il reçoit un jeton authentique.

## <a name="validate-claims-of-the-token"></a>Valider les revendications du jeton

Une fois que le serveur a vérifié l’authenticité du jeton, le serveur FHIR effectue valide le fait que le client dispose des revendications requises pour accéder au jeton.

Lorsque vous utilisez le service FHIR, le serveur valide :

1. Le jeton a l’`Audience` appropriée (revendication `aud`).
1. L’utilisateur ou le principal pour lequel le jeton a été émis est autorisé à accéder au plan de données du serveur FHIR. La revendication `oid` du jeton contient un ID d’objet d’identité qui identifie de façon unique l’utilisateur ou le principal.

Nous recommandons que le service FHIR soit configuré pour utiliser un RBAC Azure pour gérer les attributions de rôle de plan de données. Toutefois, vous pouvez également configurer un RBAC local si votre service FHIR utilise un locataire Azure Active Directory externe ou secondaire. 

Lorsque vous utilisez le serveur OSS Microsoft FHIR pour Azure, le serveur valide les points suivants :

1. Le jeton a l’`Audience` appropriée (revendication `aud`).
1. Le jeton a un rôle dans la revendication `roles` qui est autorisé à accéder au serveur FHIR.

Consultez les détails sur la façon de [définir des rôles sur le serveur FHIR](https://github.com/microsoft/fhir-server/blob/master/docs/Roles.md).

Un serveur FHIR peut également valider le fait qu’un jeton d’accès a les étendues (dans la revendication de jeton `scp`) pour accéder à la partie de l’API FHIR à laquelle un client tente d’accéder. Actuellement, le service FHIR ne valide pas les étendues de jeton.
