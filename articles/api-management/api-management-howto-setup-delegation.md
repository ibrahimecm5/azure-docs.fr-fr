---
title: Délégation de l'inscription des utilisateurs et des abonnements aux produits
description: Découvrez comment déléguer à un tiers l'enregistrement de l'utilisateur et la souscription à des produits dans la gestion des API Azure.
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2021
ms.author: danlep
ms.openlocfilehash: dbb948e45473bf091cb2d69e82ad2adf5fcfe8a2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131058632"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Délégation de l'inscription des utilisateurs et des abonnements aux produits

La délégation permet à votre site web de conserver les données utilisateur et d’effectuer une validation personnalisée. Avec la délégation, vous pouvez gérer la connexion/inscription des développeurs et l’abonnement aux produits en utilisant votre site web existant, au lieu de la fonctionnalité intégrée du portail pour développeurs. 

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegating-developer-sign-in-and-sign-up"></a><a name="delegate-signin-up"> </a>Délégation de la connexion et de l’inscription des développeurs

Pour déléguer la connexion et l’inscription des développeurs à votre site web existant, créez un point de terminaison de délégation spécifique sur votre site. Cette délégation spéciale fait office de point d’entrée pour toute demande de connexion/inscription lancée à partir du portail des développeurs Gestion des API.

Workflow final :

1. Le développeur clique sur le lien de connexion ou d’inscription sur le portail des développeurs Gestion des API.
2. Le navigateur redirige vers le point de terminaison de délégation.
3. Le point final de la délégation, en retour, redirige l’utilisateur vers ou présente à l’utilisateur une connexion/inscription. 
4. Une fois la connexion/inscription réussie, l’utilisateur est redirigé vers le portail des développeurs Gestion des API à l’emplacement qu’il a quitté.

### <a name="set-up-api-management-to-route-requests-via-delegation-endpoint"></a>Configurer Gestion des API pour acheminer les demandes via le point de terminaison de délégation

1. Dans le Portail Azure, recherchez **Portail des développeurs** dans votre ressource Gestion des API.
2. Cliquez sur l’élément **Délégation**. 
3. Cochez la case pour activer **Déléguer la connexion et l’inscription**.

:::image type="content" source="media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png" alt-text="Vue d’ensemble de la délégation":::

4. Déterminez l’URL de votre point de terminaison de délégation spécial, puis entrez-la dans le champ **URL du point de terminaison de délégation**. 
5. Dans le champ **Clé de validation de la délégation**, vous avez les deux options suivantes :
    * Entrez un secret utilisé pour calculer une signature fournie à des fins de vérification que la demande tire de Gestion des API. 
    * Cliquez sur le bouton **Générer** pour que Gestion des API génère une clé aléatoire pour vous.
6. Cliquez sur **Enregistrer**.

### <a name="create-your-delegation-endpoint"></a>Créer votre point de terminaison de délégation

>[!NOTE]
> Tandis que la procédure suivante fournit des exemples de l’opération **SignIn**, vous pouvez effectuer la gestion des comptes à l’aide de l’une des opérations disponibles en suivant les étapes ci-dessous. 

Étapes recommandées pour la création d’un nouveau point de terminaison de délégation à implémenter sur votre site :

1. Recevoir une demande au format suivant :
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl={URL of source page}&salt={string}&sig={string}*
   
    Paramètres de requête pour la connexion/inscription :

   | Paramètre | Description |
   | --------- | ----------- |
   | **opération** | Identifie le type de demande de délégation. Opérations disponibles : **SignIn**, **ChangePassword**, **ChangeProfile**, **CloseAccount** et **SignOut**. |
   | **returnUrl** | URL de l’endroit où l’utilisateur a cliqué sur un lien de connexion ou d’inscription |
   | **salt** | Chaîne salt spéciale utilisée pour calculer un code de hachage de sécurité. |
   | **sig** | Code de hachage de sécurité calculé à comparer avec votre propre code de hachage calculé. |
   
3. Vérifiez que la demande émane bien de Gestion des API Azure (facultatif, mais fortement recommandé pour assurer la sécurité).
   
   * Calculez un code de hachage HMAC-SHA512 d’une chaîne basée sur les paramètres de requête **returnUrl** et **salt**. Pour plus de détails, consultez notre [exemple de code].
     
     ```
         HMAC(salt + '\n' + returnUrl)
     ```
   * Comparez le code de hachage calculé plus haut avec la valeur du paramètre de requête **sig**. Si les deux codes de hachage correspondent, passez à l’étape suivante. Sinon, rejetez la demande.
4. Vérifiez que vous recevez une demande de connexion/inscription.
    * Le paramètre de requête **operation** sera défini sur « **SignIn** ».
5. Présentez une interface de connexion ou d’inscription à l’utilisateur.
    * Si l’utilisateur s’inscrit, créez-lui un compte correspondant dans Gestion des API. 
      * [Créez un utilisateur] avec l'API REST de gestion de Gestion des API. 
      * Définissez l’ID utilisateur sur la même valeur dans votre magasin d’utilisateurs ou un nouvel ID facile à suivre.
6. Lorsque l'utilisateur est bien authentifié :
   
   * [Demandez un jeton d’accès partagé] via l’API REST Gestion des API.
   * Ajoutez un paramètre de requête **returnUrl** à l’URL SSO que vous reçue de l’appel d’API ci-dessus. Par exemple :
     
     > `https://contoso.developer.azure-api.net/signin-sso?token=<URL-encoded token>&returnUrl=%2Freturn%2Furl` 
     
   * Redirigez l’utilisateur vers l’URL générée ci-dessus.

>[!NOTE]
> Pour les opérations de gestion de compte (**ChangePassword**, **ChangeProfile** et **CloseAccount**), transmettez les paramètres de requête suivants :
> 
>    | Paramètre | Description |
>    | --------- | ----------- |
>    | **opération** | Identifie le type de demande de délégation. |
>    | **userId** | Identifiant utilisateur du compte à gérer. |
>    | **salt** | Chaîne salt spéciale utilisée pour calculer un code de hachage de sécurité. |
>    | **sig** | Code de hachage de sécurité calculé à comparer avec votre propre code de hachage calculé. |

## <a name="delegating-product-subscription"></a><a name="delegate-product-subscription"> </a>Délégation de l’abonnement aux produits

La délégation des abonnements aux produits fonctionne de la même manière que la délégation de la connexion/inscription d’un utilisateur. Le processus final se présente comme suit :

1. Le développeur sélectionne un produit dans le portail des développeurs Gestion des API, puis clique sur le bouton **S’abonner**.
2. Le navigateur redirige vers le point de terminaison de délégation.
3. Le point de terminaison de délégation effectue les étapes nécessaires pour l’abonnement au produit, que vous concevez. Elles peuvent inclure : 
   * La redirection vers une autre page pour demander des informations de facturation.
   * Des questions supplémentaires.
   * Le stockage des informations, sans intervention de l’utilisateur.

### <a name="enable-the-api-management-functionality"></a>Activer la fonctionnalité Gestion des API

Dans la page **Délégation**, cliquez sur **Déléguer l’abonnement au produit**.

### <a name="create-your-delegation-endpoint"></a>Créer votre point de terminaison de délégation

Étapes recommandées pour la création d’un nouveau point de terminaison de délégation à implémenter sur votre site :

1. Recevez une demande au format suivant.
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation={operation}&productId={product to subscribe to}&userId={user making request}&salt={string}&sig={string}*
   >
   
    Paramètres de requête pour le cas abonnement à un produit :

   | Paramètre | Description |
   | --------- | ----------- |
   | **opération** | Identifie le type de demande de délégation. Voici des options valides de demande d’abonnement aux produits : <ul><li>**Subscribe** : une demande d’abonnement de l’utilisateur à un produit donné avec l’identifiant fourni (voir ci-dessous).</li><li>**Unsubscribe** : une demande de désabonnement de l’utilisateur pour un produit.</li><li>**Renew** : une demande de renouvellement d’abonnement (par exemple, susceptible d’expirer).</li></ul> |
   | **productId** | Sur *Subscribe*, l’ID du produit auquel l’utilisateur a demandé l’abonnement. |
   | **subscriptionId** | Sur *Unsubscribe* et *Renew*, abonnement au produit. |
   | **userId** | Sur *Subscribe*, ID de l’utilisateur qui fait la demande. |
   | **salt** | Chaîne salt spéciale utilisée pour calculer un code de hachage de sécurité. |
   | **sig** | Code de hachage de sécurité calculé à comparer avec votre propre code de hachage calculé. |

2. Vérifiez si la demande émane bien de Gestion des API Azure (facultatif, mais fortement recommandé pour assurer la sécurité).
   
   * Calculez un code de hachage HMAC-SHA512 d’une chaîne basée sur les paramètres de requête **productId**, **userId** et **salt** :
     ```
     HMAC(**salt** + '\n' + **productId** + '\n' + **userId**)
     ```
   * Comparez le code de hachage calculé plus haut avec la valeur du paramètre de requête **sig**. Si les deux codes de hachage correspondent, passez à l’étape suivante. Sinon, rejetez la demande.
3. Traitez l’abonnement au produit en fonction du type d’opération demandé dans **operation** (par exemple : facturation, autres questions, etc.).
4. Après un abonnement correct de l’utilisateur au produit de votre côté, abonnez-le au produit Gestion des API en [appelant l’API REST pour les abonnements].

## <a name="example-code"></a><a name="delegate-example-code"> </a> Exemple de code

Les exemples de code vous montrent comment effectuer les opérations suivantes :

* Prenez la *clé de validation de délégation* qui est définie sur l’écran **Délégation** du Portail Azure.
* Créer un code de hachage HMAC, qui valide la signature, prouvant ainsi la validité de l’élément returnUrl transmis.

En le modifiant légèrement, vous pouvez utiliser le même code pour **productId** et **userId**.

### <a name="c-code-to-generate-hash-of-returnurl"></a>Code C# pour générer le hachage de returnUrl

```csharp
using System.Security.Cryptography;

string key = "delegation validation key";
string returnUrl = "returnUrl query parameter";
string salt = "salt query parameter";
string signature;
using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
{
    signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
}
```

### <a name="nodejs-code-to-generate-hash-of-returnurl"></a>Code NodeJS pour générer le hachage de returnUrl

```
var crypto = require('crypto');

var key = 'delegation validation key'; 
var returnUrl = 'returnUrl query parameter';
var salt = 'salt query parameter';

var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
var digest = hmac.update(salt + '\n' + returnUrl).digest();
// change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
// compare signature to sig query parameter

var signature = digest.toString('base64');
```

> [!IMPORTANT]
> Vous devez [republier le portail des développeurs](api-management-howto-developer-portal-customize.md#publish) pour que les modifications apportées à la délégation prennent effet.

## <a name="next-steps"></a>Étapes suivantes
- [Accédez à plus d’informations sur le portail des développeurs.](api-management-howto-developer-portal.md)
- [S’authentifier avec Azure AD](api-management-howto-aad.md) ou avec [Azure AD B2C](api-management-howto-aad-b2c.md).
- Plus de questions sur le portail des développeurs ? [Trouvez des réponses dans notre FAQ](developer-portal-faq.md).

[Delegating developer sign-in and sign-up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[Demandez un jeton d’accès partagé]: /rest/api/apimanagement/2020-12-01/user/get-shared-access-token
[Créez un utilisateur]: /rest/api/apimanagement/2020-12-01/user/create-or-update
[appel de l’API REST pour les abonnements]: /rest/api/apimanagement/2020-12-01/subscription/create-or-update
[Next steps]: #next-steps
[exemple de code]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png
