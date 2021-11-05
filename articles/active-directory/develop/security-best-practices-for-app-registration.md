---
title: Meilleures pratiques pour la configuration de l’inscription des applications Azure AD - Plateforme d’identités Microsoft
description: En savoir plus sur un ensemble de meilleures pratiques et des conseils généraux sur la configuration de l’inscription des applications Azure AD.
services: active-directory
author: Chrispine-Chiedo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/8/2021
ms.custom: template-concept
ms.author: cchiedo
ms.reviewer: saumadan, marsma
ms.openlocfilehash: cd03531e35d51bafeceb2df913e17fc4814e911c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131050568"
---
# <a name="azure-ad-application-registration-security-best-practices"></a>Meilleures pratiques de sécurité pour l’inscription d’applications Azure AD

L’inscription d’une application Azure Active Directory (Azure AD) est une partie essentielle de votre application métier. Tout problème de configuration ou de défaillance dans l’hygiène de votre application peut entraîner des temps d’arrêt ou une compromission.

Il est important de comprendre que l’inscription de votre application a un impact plus étendu que l’application métier en raison de sa surface d’exposition. En fonction des autorisations ajoutées à votre application, une application compromise peut avoir un effet sur l’ensemble de l’organisation.
Étant donné qu’une inscription d’application est essentielle pour la connexion de vos utilisateurs, tout temps d’arrêt peut affecter votre entreprise ou un service critique dont dépend votre entreprise. Il est donc important d’allouer du temps et des ressources pour garantir que l’inscription de votre application demeure dans un état d’intégrité irréprochable. Nous vous recommandons de procéder à une évaluation périodique de la sécurité et de l’intégrité de vos applications, à l’instar d’une évaluation du modèle de menaces de sécurité pour votre code. Pour une perspective plus large sur la sécurité des organisations, consultez le [cycle de vie de développement de la sécurité](https://www.microsoft.com/securityengineering/sdl) (SDL).

Cet article décrit les meilleures pratiques de sécurité pour les propriétés d’inscription d’application suivantes.

- URI de redirection
- Workflow d’octroi implicite pour le jeton d’accès
- Informations d'identification
- URI AppId
- Propriété de l’application
- Check-list

## <a name="redirect-uri-configuration"></a>Configuration de l’URI de redirection

Il est important de tenir à jour les URI de redirection de votre application. Une brèche dans la propriété de l’un des URI de redirection peut compromettre l’application. Assurez-vous que tous les enregistrements DNS sont régulièrement mis à jour et que leurs modifications sont surveillées. En plus de conserver la propriété de tous les URI, n’utilisez pas d’URL de réponse générique ou de schémas d’URI non sécurisés tels que http ou URN.

![URI de redirection](media/active-directory-application-registration-best-practices/redirect-uri.png)

### <a name="redirect-uri-summary"></a>Résumé de l’URI de redirection

| Pratiques conseillées                                    | Pratiques déconseillées          |
| ------------------------------------- | -------------- |
| Conserver la propriété de tous les URI        | Utiliser des caractères génériques  |
| Tenir DNS à jour                   | Utiliser le schéma URN |
| Maîtriser la taille de la liste                   |   -----        |
| Supprimer les URI inutiles             |   -----        |
| Mettre à jour les URL du schéma http vers https |   -----        |

## <a name="implicit-flow-token-configuration"></a>Configuration du jeton de flux implicite

Les scénarios qui requièrent un **flux implicite** peuvent désormais utiliser le **flux de code d’authentification** pour réduire le risque de compromission d’une utilisation incorrecte du flux d’octroi implicite. Si vous avez configuré l’inscription de votre application pour recevoir des jetons d’accès à l’aide du flux implicite, mais que vous ne l’utilisez pas activement, nous vous recommandons de désactiver le paramètre pour le protéger contre toute utilisation incorrecte.

![Jetons d’accès utilisés pour les flux implicites](media/active-directory-application-registration-best-practices/implict-grant-flow.png)

### <a name="implicit-grant-flow-summary"></a>Résumé du flux d’octroi implicite

| Pratiques conseillées                                                                    | Pratiques déconseillées                                                                  |
| --------------------------------------------------------------------- | ---------------------------------------------------------------------- |
| Comprendre si le [flux implicite est requis](./v2-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant) | Utiliser le flux implicite sauf spécification [requise explicitement](./v2-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant) |
| Inscription d’application distincte pour les scénarios de flux implicite (valide) |   -----                                                |
| Désactiver le flux implicite inutilisé | -----              |

## <a name="credential-configuration"></a>Configuration des informations d’identification

Les informations d’identification constituent une partie essentielle de l’inscription d’une application lorsque celle-ci est utilisée comme client confidentiel. Si votre inscription d’application est utilisée uniquement comme une application cliente publique (permettant aux utilisateurs de se connecter à l’aide d’un point de terminaison public), évitez d’avoir des informations d’identification sur votre objet d’application. Passez en revue les informations d’identification utilisées dans vos applications en vue de pouvoir les actualiser et de connaître leur date d’expiration. Une information d’identification non utilisée sur une application peut entraîner une violation de la sécurité.
Bien qu’il soit pratique d’utiliser des secrets de mot de passe comme informations d’identification, nous vous recommandons fortement d’utiliser des certificats x509 comme seul type d’informations d’identification afin d’obtenir des jetons pour votre application. Surveillez vos pipelines de production pour vous assurer que les informations d’identification de n’importe quel type ne sont jamais validées dans des référentiels de code. Si vous utilisez Azure, nous vous recommandons fortement d’utiliser l’identité managée pour que les informations d’identification de l’application soient gérées automatiquement. Pour plus d’informations, consultez la [documentation des identités managées](../managed-identities-azure-resources/overview.md). [Credential Scanner](../../security/develop/security-code-analysis-overview.md#credential-scanner) est un outil d’analyse statique qui vous permet de détecter les informations d’identification, (et tout autre contenu sensible) dans votre code source et la sortie de votre build.

![Certificats et secrets sur le Portail Azure](media/active-directory-application-registration-best-practices/credentials.png)

| Pratiques conseillées                                                                     | Pratiques déconseillées                             |
| ---------------------------------------------------------------------- | --------------------------------- |
| Utilisez les [informations d’identification de certificat](./active-directory-certificate-credentials.md)              | Utiliser les informations d’identification du mot de passe          |
| Utiliser Key Vault avec des [identités managées](../managed-identities-azure-resources/overview.md) | Partager des informations d’identification entre les applications     |
| Effet de substitution fréquent                                                    | Avoir de nombreuses informations d’identification sur une application  |
|     -----                                                              | Conserver les informations d’identification obsolètes disponibles |
|     -----                                                              | Valider les informations d’identification dans le code        |

## <a name="appid-uri-configuration"></a>Configuration de l’URI AppId

Certaines applications peuvent exposer des ressources (via WebAPI) et, par conséquent, doivent définir un URI AppId qui identifie de façon unique la ressource dans un locataire. Nous vous recommandons d’utiliser l’un des schémas d’URI suivants : API ou HTTPS, et de définir l’URI AppId aux formats suivants pour éviter les collisions d’URI dans votre organisation.
L’URI AppId fait office de préfixe pour les étendues référencées dans le code de l’API, et il doit utiliser un domaine appartenant à un client vérifié. Pour les applications mutualisées, la valeur doit aussi être globalement unique.

[!INCLUDE [active-directory-identifierUri](../../../includes/active-directory-identifier-uri-patterns.md)]

![URI d’ID d’application](media/active-directory-application-registration-best-practices/app-id-uri.png)

### <a name="appid-uri-summary"></a>Résumé de l’URI AppId

| Pratiques conseillées                                           | Pratiques déconseillées                  |
| -------------------------------------------- | ---------------------- |
| Éviter les collisions à l’aide de formats d’URI valides | Utiliser l’URI de l’AppId générique |
| Utiliser un domaine vérifié dans des applications métier | URI incorrect    |
| Inventorier vos URI AppId                    |      -----             |
| Utiliser l’URI AppId pour exposer des WebApi dans votre organisation| Utiliser l’URI AppId pour identifier l’application, utiliser la propriété appId à la place|

## <a name="app-ownership-configuration"></a>Configuration de la propriété de l’application

Veillez à ce que la propriété des applications soit limitée à un nombre minimal de personnes au sein de l’organisation. Il est recommandé de vérifier la liste des propriétaires tous les deux mois pour s’assurer que les propriétaires font toujours partie de l’organisation et que leurs comptes de charte correspondent à la propriété de l’enregistrement de l’application. Consultez [Révisions d’accès Azure AD](../governance/access-reviews-overview.md) pour en savoir plus.

![utilisateurs approvisionnant le service - propriétaires](media/active-directory-application-registration-best-practices/app-ownership.png)

### <a name="app-ownership-summary"></a>Résumé de la propriété de l’application

| Pratiques conseillées                  | Pratiques déconseillées |
| ------------------- | ----- |
| Rester petit       | ----- |
| Surveiller la liste des propriétaires | ----- |

## <a name="checklist"></a>Check-list

Les développeurs d’applications peuvent utiliser la _liste de contrôle_ disponible dans le Portail Microsoft Azure pour s’assurer que leur inscription d’application répond à une exigence de haute qualité et fournit des conseils pour une intégration sécurisée. L’assistant d’intégration présente les meilleures pratiques et recommandations qui évitent les oublis courants lors de l’intégration à la plateforme d’identités Microsoft.

![Liste de contrôle de l’Assistant d’intégration sur le Portail Microsoft Azure](media/active-directory-application-registration-best-practices/checklist.png)

### <a name="checklist-summary"></a>Résumé de la liste de contrôle

| Pratiques conseillées                                                 | Pratiques déconseillées |
| -------------------------------------------------- | ----- |
| Utiliser la liste de contrôle pour accéder à la recommandation basée sur un scénario | ----- |
| Lien profond vers les panneaux d’inscription d’application             | ----- |


## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur le flux de code d’autorisation, consultez [Flux du code d’autorisation OAuth 2.0](./v2-oauth2-auth-code-flow.md).
