---
title: Aide aux développeurs pour le contexte d’authentification de l’accès conditionnel d’Azure AD
description: Aide aux développeurs et scénarios pour le contexte d’authentification de l’accès conditionnel d’Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.date: 05/18/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: CelesteDG
ms.reviewer: kkrishna
ms.workload: identity
ms.custom: aaddev
ms.openlocfilehash: 48e9598acac2739b7663fcd9a46006208d44783c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131459498"
---
# <a name="developers-guide-to-conditional-access-authentication-context"></a>Aide aux développeurs pour le contexte d’authentification de l’accès conditionnel

L’[accès conditionnel](../conditional-access/overview.md) est le plan de contrôle Zero Trust qui vous permet de cibler des stratégies d’accès pour toutes vos applications (anciennes ou nouvelles, privées ou publiques, locales ou multiclouds). Grâce au [contexte d’authentification de l’accès conditionnel](../conditional-access/concept-conditional-access-cloud-apps.md#authentication-context-preview), vous pouvez appliquer différentes stratégies au sein de ces applications. 

Le contexte d’authentification de l’accès conditionnel (contexte d’authentification) vous permet d’appliquer des stratégies granulaires aux actions et données sensibles plutôt qu’au niveau de l’application. Vous pouvez affiner vos stratégies Zero Trust pour l’accès le moins privilégié tout en réduisant au minimum la friction de l’utilisateur et en gardant les utilisateurs plus productifs et vos ressources plus sécurisées. Aujourd’hui, il peut être utilisé par les applications utilisant [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) pour l’authentification développée par votre entreprise afin de protéger les ressources sensibles, comme les transactions de grande valeur ou la consultation des données personnelles des employés.

Utilisez la nouvelle fonctionnalité de contexte d’authentification du moteur d’accès conditionnel d’Azure AD pour déclencher une demande d’authentification renforcée depuis votre application et vos services. Les développeurs ont désormais la possibilité d’exiger de leurs utilisateurs finaux une authentification renforcée, de manière sélective (p. ex. : l’authentification multifacteur), à partir de leurs applications. Cette fonctionnalité aide les développeurs à créer des expériences utilisateur plus fluides pour la plupart des parties de leur application, tandis que l’accès à des opérations et des données plus sécurisées demeure derrière des contrôles d’authentification plus forts.

## <a name="problem-statement"></a>Définition du problème

Les administrateurs et régulateurs informatiques ont souvent du mal à trouver un équilibre entre le fait d’inviter trop fréquemment leurs utilisateurs à utiliser des facteurs d’authentification supplémentaires et celui d’assurer une sécurité adéquate et le respect des stratégies pour les applications et les services dont certaines parties contiennent des données et des opérations sensibles. Il faut parfois choisir entre une stratégie forte qui a un impact sur la productivité des utilisateurs lorsqu’ils accèdent à la plupart des données et des actions et une stratégie qui n’est pas suffisamment forte pour les ressources sensibles. 

Ainsi, que se passerait-il si les applications pouvaient combiner les deux, en fonctionnant avec une sécurité relativement moindre et des invites moins fréquentes pour la plupart des utilisateurs et des opérations, tout en renforçant de manière conditionnelle les exigences de sécurité lorsque les utilisateurs accèdent à des parties plus sensibles ? 

## <a name="common-scenarios"></a>Scénarios courants

Par exemple, si les utilisateurs peuvent se connecter à SharePoint en utilisant l’authentification multifacteur, l’accès à la collection de sites dans SharePoint contenant des documents sensibles peut nécessiter un appareil conforme et se faire uniquement à partir de plages d’adresses IP approuvées. 

## <a name="steps"></a>Étapes

Voici les conditions préalables et les étapes à suivre si vous souhaitez utiliser le contexte d’authentification de l’accès conditionnel. 

### <a name="prerequisites"></a>Prérequis

**Tout d’abord**, votre application doit être intégrée à la plateforme d’identités Microsoft à l’aide des protocoles [OpenID Connect](v2-protocols-oidc.md)/ [OAuth 2.0](v2-oauth2-auth-code-flow.md) pour l’authentification et l’autorisation. Nous vous recommandons d’utiliser les [bibliothèques d’authentification de la plateforme d’identités Microsoft](reference-v2-libraries.md) pour intégrer et sécuriser votre application avec Azure Active Directory. La [documentation de la plateforme d’identités Microsoft](index.yml) est un bon point de départ pour apprendre à intégrer vos applications à la plateforme d’identités Microsoft. La prise en charge de la fonctionnalité du contexte d’authentification de l’accès conditionnel repose sur les extensions de protocole fournies par le protocole [OpenID Connect](v2-protocols-oidc.md) standard. Les développeurs utilisent une **valeur** de [référence pour le contexte d’authentification de l’accès conditionnel](/graph/api/conditionalaccessroot-list-authenticationcontextclassreferences) avec le paramètre [Demande de revendications](claims-challenge.md) pour permettre aux applications de déclencher et de satisfaire la stratégie.

**Deuxièmement**, l’[accès conditionnel](../conditional-access/overview.md) nécessite une licence Azure AD Premium P1. Pour plus d’informations sur les licences, consultez la [page de tarification d’Azure AD](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing).

**Troisièmement**, à l’heure actuelle, la fonctionnalité est disponible uniquement pour les applications qui connectent les utilisateurs. Les applications qui s’authentifient elles-mêmes ne sont pas prises en charge. Pour en savoir plus sur les types d’applications et les flux d’authentification pris en charge par la plateforme d’identités Microsoft, utilisez le [guide relatif aux flux d’authentification et aux scénarios d’applications](authentication-flows-app-scenarios.md).

### <a name="integration-steps"></a>Étapes d'intégration

Une fois que votre application est intégrée à l’aide des protocoles d’authentification pris en charge et qu’elle est inscrite dans un locataire Azure AD disposant de la fonctionnalité d’accès conditionnel, vous pouvez commencer à intégrer cette fonctionnalité dans vos applications qui connectent les utilisateurs.

> [!NOTE]
> Une présentation détaillée de cette fonctionnalité est également disponible sous la forme d’une session enregistrée : [Utiliser le contexte d’authentification de l’accès conditionnel dans votre application pour une authentification renforcée](https://www.youtube.com/watch?v=_iO7CfoktTY).

**Premièrement** : Déclarez les contextes d’authentification et rendez-les disponibles dans votre locataire. Pour plus d’informations, consultez [Configurer les contextes d’authentification](../conditional-access/concept-conditional-access-cloud-apps.md#configure-authentication-contexts).

Les valeurs **C1 à C25** peuvent être utilisées comme **ID de contexte d’authentification** dans un locataire. Voici quelques exemples de contexte d’authentification :

- **C1** : Exiger une authentification forte
- **C2** : Exiger des appareils conformes
- **C3** : Exiger des emplacements approuvés

Créez ou modifiez vos stratégies d’accès conditionnel afin d’utiliser les contextes d’authentification d’accès conditionnel. Voici quelques exemples de stratégies :

- Tous les utilisateurs qui se connectent à cette application web doivent avoir réussi l’authentification à deux facteurs pour l’ID de contexte d’authentification **C1**.
- Tous les utilisateurs qui se connectent à cette application web doivent avoir réussi l’authentification à deux facteurs et accéder à l’application web à partir d’une certaine plage d’adresses IP pour l’ID de contexte d’authentification **C3**.

> [!NOTE]
> Les valeurs de contexte d’authentification de l’accès conditionnel sont déclarées et gérées séparément des applications. Il n’est pas recommandé que les applications dépendent fortement des ID de contexte d’authentification. Les stratégies d’accès conditionnel sont généralement conçues par les administrateurs informatiques, car ils ont une meilleure compréhension des ressources disponibles sur lesquelles appliquer les stratégies. Par exemple, pour un locataire Azure AD, les administrateurs informatiques savent combien d’utilisateurs du locataire sont équipés pour utiliser l’authentification à deux facteurs pour l’authentification multifacteur et peuvent donc s’assurer que les stratégies d’accès conditionnel qui requièrent l’authentification à deux facteurs sont limitées à ces utilisateurs équipés. De même, si l’application est utilisée dans plusieurs locataires, les ID de contexte d’authentification utilisés peuvent être différents et, dans certains cas, ne pas être disponibles du tout.

**Deuxièmement** : Il est conseillé aux développeurs d’une application prévoyant d’utiliser un contexte d’authentification de l’accès conditionnel de fournir d’abord aux administrateurs de l’application ou aux administrateurs informatiques un moyen de mapper les actions potentiellement sensibles à des ID de contexte d’authentification. Les étapes sont à peu près les suivantes :

1. Identifiez les actions dans le code qui peuvent être mises à disposition pour être mappées à des ID de contexte d’authentification.
1. Créez un écran dans le portail d’administration de l’application (ou une fonctionnalité équivalente) que les administrateurs informatiques peuvent utiliser pour mapper des actions sensibles à un ID de contexte d’authentification disponible.
1. Consultez l’exemple de code sur la page [Use the Conditional Access Auth Context to perform step-up authentication](https://github.com/Azure-Samples/ms-identity-ca-auth-context/blob/main/README.md) (Utiliser le contexte d’authentification de l’accès conditionnel pour effectuer une authentification renforcée) pour obtenir un exemple sur la façon de procéder.

Ces étapes sont les modifications que vous devez apporter à votre base de code. Les étapes sont essentiellement les suivantes :

- Interrogez MS Graph pour [répertorier tous les contextes d’authentification disponibles](/graph/api/conditionalaccessroot-list-authenticationcontextclassreferences).
- Permettez aux administrateurs informatiques de sélectionner les opérations sensibles ou à privilèges élevés et de les assigner aux contextes d’authentification disponibles à l’aide des stratégies d’autorité de certification. 
- Enregistrez ces informations de mappage dans votre base de données ou votre magasin local.

:::image type="content" source="media/developer-guide-conditional-access-authentication-context/configure-conditional-access-authentication-context.png" alt-text="Flux de configuration pour la création d’un contexte d’authentification":::

**Troisièmement** : Votre application (pour cet exemple, nous supposons qu’il s’agit d’une API web) doit ensuite évaluer les appels par rapport au mappage sauvegardé et déclencher en conséquence les défis de revendication pour ses applications clientes. Pour vous préparer à cette action, procédez comme suit :

1. Dans une opération sensible et protégée par le contexte d’authentification, évaluez les valeurs de la revendication **acrs** par rapport aux mappages d’ID de contexte d’authentification enregistrés précédemment et déclenchez un [défi de revendications](claims-challenge.md) comme indiqué dans l’extrait de code ci-dessous. 

1. Le diagramme suivant illustre l’interaction entre l’utilisateur, l’application cliente et l’API web.

   :::image type="content" source="media/developer-guide-conditional-access-authentication-context/authentication-context-application-flow.png" alt-text="Diagramme montrant l’interaction entre l’utilisateur, l’application web, l’API et Azure AD":::

   L’extrait de code suivant provient de l’exemple de code de la page [Use the Conditional Access Auth Context to perform step-up authentication](https://github.com/Azure-Samples/ms-identity-ca-auth-context/blob/main/README.md) (Utiliser le contexte d’authentification de l’accès conditionnel pour effectuer une authentification renforcée). La première méthode (`CheckForRequiredAuthContext()` dans l’API) : 

      - vérifie si l’action de l’application appelée requiert une authentification renforcée. Pour ce faire, elle recherche dans sa base de données un mappage enregistré pour cette méthode ;
      - si cette action nécessite effectivement un contexte d’authentification élevé, elle vérifie dans la revendication **acrs** s’il existe un ID de contexte d’authentification correspondant ;
      - si elle ne trouve aucun ID de contexte d’authentification correspondant, elle déclenche un [défi de revendications](claims-challenge.md#claims-challenge-header-format).

      ```
      public void CheckForRequiredAuthContext(string method)
      {
          string authType = _commonDBContext.AuthContext.FirstOrDefault(x => x.Operation == method 
                      && x.TenantId == _configuration["AzureAD:TenantId"])?.AuthContextId;

          if (!string.IsNullOrEmpty(authType))
          {
              HttpContext context = this.HttpContext;
              string authenticationContextClassReferencesClaim = "acrs";

              if (context == null || context.User == null || context.User.Claims == null 
                  || !context.User.Claims.Any())
              {
                  throw new ArgumentNullException("No Usercontext is available to pick claims from");
              }

              Claim acrsClaim = context.User.FindAll(authenticationContextClassReferencesClaim).FirstOrDefault(x 
                  => x.Value == authType);

              if (acrsClaim == null || acrsClaim.Value != authType)
              {
                  if (IsClientCapableofClaimsChallenge(context))
                  {
                      string clientId = _configuration.GetSection("AzureAd").GetSection("ClientId").Value;
                      var base64str = Convert.ToBase64String(Encoding.UTF8.GetBytes("{\"access_token\":{\"acrs\":{\"essential\":true,\"value\":\"" + authType + "\"}}}"));

                      context.Response.Headers.Append("WWW-Authenticate", $"Bearer realm=\"\", authorization_uri=\"https://login.microsoftonline.com/common/oauth2/authorize\", client_id=\"" + clientId + "\", error=\"insufficient_claims\", claims=\"" + base64str + "\", cc_type=\"authcontext\"");
                      context.Response.StatusCode = (int)HttpStatusCode.Unauthorized;
                      string message = string.Format(CultureInfo.InvariantCulture, "The presented access tokens had insufficient claims. Please request for claims requested in the WWW-Authentication header and try again.");
                      context.Response.WriteAsync(message);
                      context.Response.CompleteAsync();
                      throw new UnauthorizedAccessException(message);
                  }
                  else
                  {
                      throw new UnauthorizedAccessException("The caller does not meet the authentication  bar to carry our this operation. The service cannot allow this operation");
                  }
              }
          }
      }
      ```

   > [!NOTE]
   > Le format du défi de revendications est décrit dans l’article [Défi de revendications sur la plateforme d’identités Microsoft](claims-challenge.md). 

1. Dans l’application cliente, interceptez le défi de revendications et redirigez l’utilisateur vers Azure AD pour une évaluation plus approfondie des stratégies. L’extrait de code suivant provient de l’exemple de code de la page [Use the Conditional Access Auth Context to perform step-up authentication](https://github.com/Azure-Samples/ms-identity-ca-auth-context/blob/main/README.md) (Utiliser le contexte d’authentification de l’accès conditionnel pour effectuer une authentification renforcée).

   ```
   internal static string ExtractHeaderValues(WebApiMsalUiRequiredException response)
   {
       if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized && response.Headers.WwwAuthenticate.Any())
       {
           AuthenticationHeaderValue bearer = response.Headers.WwwAuthenticate.First(v => v.Scheme == "Bearer");
           IEnumerable<string> parameters = bearer.Parameter.Split(',').Select(v => v.Trim()).ToList();
           var errorValue = GetParameterValue(parameters, "error");

           try
           {
               // read the header and checks if it conatins error with insufficient_claims value.
               if (null != errorValue && "insufficient_claims" == errorValue)
               {
                   var claimChallengeParameter = GetParameterValue(parameters, "claims");
                   if (null != claimChallengeParameter)
                   {
                       var claimChallenge = ConvertBase64String(claimChallengeParameter);

                       return claimChallenge;
                   }
               }
           }
           catch (Exception ex)
           {
               throw ex;
           }
       }
       return null;
   }
   ```

   Traitez l’exception dans l’appel à l’API web. Si une demande de revendications est présentée, redirigez l’utilisateur vers Azure AD pour poursuivre le traitement.

   ```
   try
   {
       // Call the API 
       await _todoListService.AddAsync(todo);
   }
   catch (WebApiMsalUiRequiredException hex)
   {
       // Challenges the user if exception is thrown from Web API.
       try
       {
           var claimChallenge =ExtractHeaderValues(hex);
           _consentHandler.ChallengeUser(new string[] { "user.read" }, claimChallenge);

           return new EmptyResult();
       }
       catch (Exception ex)
       {
           _consentHandler.HandleException(ex);
       }

       Console.WriteLine(hex.Message);
   }
   return RedirectToAction("Index");
   ```
   
1. (Facultatif) Déclarez la capacité du client. Les capacités du client aident les fournisseurs de ressources, comme notre API web ci-dessus, à détecter si l’application cliente appelante comprend le défi de revendications et peut personnaliser sa réponse en conséquence. Cette capacité peut être utile lorsque les clients de l’API ne sont pas tous capables de traiter les défis de revendications et que certains clients plus anciens attendent toujours une réponse différente. Pour plus d’informations, consultez la section [Capacités du client](claims-challenge.md#client-capabilities).

## <a name="caveats-and-recommendations"></a>Mises en garde et recommandations

Ne codez pas en dur les valeurs du contexte d’authentification dans votre application. Les applications doivent lire et appliquer le contexte d’authentification [à l’aide d’appels MS Graph](/graph/api/resources/authenticationcontextclassreference). Cette pratique est essentielle pour les [applications mutualisées](howto-convert-app-to-be-multi-tenant.md). Les valeurs du contexte d’authentification varient selon les locataires d’Azure AD et ne sont pas disponibles dans l’édition gratuite d’Azure AD. Pour plus d’informations sur la façon dont une application doit interroger, définir et utiliser le contexte d’authentification dans son code, consultez l’exemple de code sur la page [Use the Conditional Access Auth Context to perform step-up authentication](https://github.com/Azure-Samples/ms-identity-ca-auth-context/blob/main/README.md) (Utiliser le contexte d’authentification de l’accès conditionnel pour effectuer une authentification renforcée). 

N’utilisez pas le contexte d’authentification lorsque l’application elle-même sera la cible de stratégies d’accès conditionnel. Cette fonctionnalité fonctionne mieux lorsque certaines parties de l’application exigent de l’utilisateur une authentification plus poussée.

## <a name="next-steps"></a>Étapes suivantes

- [Accès conditionnel granulaire pour les actions et les données sensibles (blog)](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/granular-conditional-access-for-sensitive-data-and-actions/ba-p/1751775)
- [Zero Trust avec la plateforme d’identités Microsoft](/security/zero-trust/identity-developer)
- [Création d’applications Confiance Zéro avec la plateforme d’identités Microsoft](/security/zero-trust/identity-developer)
- [Utiliser le contexte d’authentification de l’accès conditionnel afin d’effectuer une authentification renforcée pour les opérations dotées de privilèges élevés dans une application web](https://github.com/Azure-Samples/ms-identity-dotnetcore-ca-auth-context-app/blob/main/README.md)
- [Utiliser le contexte d’authentification de l’accès conditionnel pour effectuer une authentification renforcée pour les opérations à privilèges élevés dans une API web](https://github.com/Azure-Samples/ms-identity-ca-auth-context/blob/main/README.md)
- [Contexte d’authentification de l’accès conditionnel](../conditional-access/concept-conditional-access-cloud-apps.md#authentication-context-preview)
- [Type de ressource authenticationContextClassReference – MS Graph](/graph/api/conditionalaccessroot-list-authenticationcontextclassreferences)
- [Défi de revendications, demande de revendications et capacités du client sur la plateforme d’identités Microsoft](claims-challenge.md)
- [Utilisation du contexte d’authentification avec Microsoft Information Protection et SharePoint](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#more-information-about-the-dependencies-for-the-authentication-context-option)
- [Flux d’authentification et scénarios d’applications](authentication-flows-app-scenarios.md)
- [Guide pratique pour utiliser des API dotées d’Évaluation continue de l’accès dans vos applications](app-resilience-continuous-access-evaluation.md)
