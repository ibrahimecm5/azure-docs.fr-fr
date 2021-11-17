---
title: Vue d’ensemble technique et fonctionnelle - Azure Active Directory B2C
description: Présentation détaillée des fonctionnalités et technologies d’Azure Active Directory B2C. Azure Active Directory B2C a une haute disponibilité à l’échelle globale.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/01/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: a40801c03721016a47b28ca5fd273b448297e0b1
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132055604"
---
# <a name="technical-and-feature-overview-of-azure-active-directory-b2c"></a>Vue d’ensemble technique et fonctionnelle d’Azure Active Directory B2C

En complément à la rubrique[À propos d’Azure Active Directory B2C](overview.md), cet article présente une introduction plus détaillée du service. Les ressources principales que vous utilisez dans le service et ses fonctionnalités sont présentées ici. Découvrez comment ces fonctionnalités vous permettent de fournir à vos clients une expérience d’identité entièrement personnalisée dans vos applications.

## <a name="azure-ad-b2c-tenant"></a>Locataire Azure AD B2C

Dans Azure Active Directory B2C (Azure AD B2C), un *locataire* représente votre organisation, et constitue un annuaire d’utilisateurs. Chaque locataire Azure AD B2C est distinct et indépendant des autres locataires Azure AD B2C. Un locataire Azure AD B2C n’est pas un locataire Azure Active Directory ; d’ailleurs vous disposez peut-être déjà de ce dernier.

Les ressources principales que vous utilisez dans un locataire Azure AD B2C sont les suivantes :

* **Annuaire** – L’*annuaire* est là où Azure AD B2C stocke les informations d’identification et les données de profil de vos utilisateurs, ainsi que les inscriptions de vos applications.
* **Inscriptions d’applications** : inscrivez vos applications web, mobiles et natives auprès d’Azure AD B2C pour activer la gestion des identités. Vous pouvez également inscrire toute API que vous souhaitez protéger avec Azure AD B2C.
* **Flux d’utilisateurs** et **stratégies personnalisées** : créez des expériences d’identité pour vos applications avec des flux d’utilisateurs intégrés et des stratégies personnalisées entièrement configurables :
  * Les **flux d’utilisateurs** permettent de rapidement activer des tâches d’identité courantes, telles que l’inscription, la connexion et la modification de profil.
  * Les **stratégies personnalisées** permettent de créer des workflows d’identité complexes, propres à votre organisation, à vos clients, employés, partenaires et citoyens.
* **Options de connexion** : Azure AD B2C offre différentes [options d’inscription et de connexion](sign-in-options.md) pour les utilisateurs de vos applications :
  * **Nom d’utilisateur, adresse de messagerie et connexion par téléphone** : configurez vos comptes locaux Azure AD B2C pour autoriser l’inscription et la connexion avec un nom d’utilisateur, une adresse de messagerie, un numéro de téléphone ou une combinaison de méthodes.
  * **Fournisseurs d’identité sociale** : fédérez avec des fournisseurs de réseaux sociaux tels que Facebook, LinkedIn ou Twitter.
  * **Fournisseurs d’identité externes** : fédérez avec des protocoles d’identité standard, tels que OAuth 2.0, OpenID Connect, etc.
* **Clés** : ajoutez et gérez des clés de chiffrement pour la signature et la validation des jetons, des secrets clients, des certificats et des mots de passe.

Un locataire Azure AD B2C est la première ressource que vous devez créer pour commencer à utiliser Azure AD B2C. Découvrez comment :

* [Créer un locataire Azure Active Directory B2C](tutorial-create-tenant.md).
* [Gérer votre locataire Azure AD B2C](tenant-management.md)

## <a name="accounts-in-azure-ad-b2c"></a>Comptes dans Azure AD B2C

Azure AD B2C définit plusieurs types de comptes utilisateur. Azure Active Directory, Azure Active Directory B2B et Azure Active Directory B2C partagent ces types de comptes.

* **Compte professionnel** : les utilisateurs dotés de comptes professionnels peuvent accéder aux ressources d’un locataire et, lorsqu’ils sont dotés d’un rôle d’administrateur, peuvent aussi gérer des locataires. Les utilisateurs disposant de comptes professionnels peuvent créer des comptes consommateur, réinitialiser des mots de passe, bloquer/débloquer des comptes et définir des autorisations ou attribuer un compte à un groupe de sécurité.
* **Compte invité** : les utilisateurs externes que vous invitez dans votre locataire en tant qu’invités. Un scénario standard pour inviter un utilisateur dans votre locataire Azure AD B2C consiste à partager les responsabilités d’administration.
* **Compte de consommateur** – Comptes gérés par les flux d’utilisateurs et les stratégies personnalisées Azure AD B2C.

![Page de gestion des utilisateurs Azure AD B2C dans le portail Azure](media/technical-overview/portal-01-users.png)<br/>*Figure : Annuaire d’utilisateurs d’un locataire Azure AD B2C dans le portail Azure*

### <a name="consumer-accounts"></a>Comptes consommateur

Avec un compte *consommateur*, les utilisateurs peuvent se connecter aux applications que vous avez sécurisées au moyen d’Azure AD B2C. Toutefois, les utilisateurs disposant de comptes consommateur ne peuvent pas accéder aux ressources Azure, par exemple au portail Azure.

Un compte consommateur peut être associé à ces types d’identités :

* **Identité locale**, avec le nom d’utilisateur et le mot de passe stockés localement dans l’annuaire Azure AD B2C. Nous faisons souvent référence à ces identités en parlant de « comptes locaux ».
* Identités **sociales** ou **d’entreprise**, pour lesquelles l’identité de l’utilisateur est managée par un fournisseur d’identité fédéré. Par exemple, Facebook, Google, Microsoft, ADFS ou Salesforce.

Un utilisateur disposant d’un compte consommateur peut se connecter avec plusieurs identités. Par exemple, le nom d’utilisateur, l’adresse e-mail, l’ID d’employé, l’ID gouvernemental, etc. Un compte unique peut avoir plusieurs identités, qu’elles soient locales et de réseaux sociaux.



:::image type="content" source="media/technical-overview/identities.png" alt-text="Identités d’un compte consommateur.":::
<br/>*Figure : Compte consommateur unique doté de plusieurs identités dans Azure AD B2C*

Pour plus d’informations, consultez [Vue d’ensemble des comptes d’utilisateur dans Azure Active Directory B2C](user-overview.md).

## <a name="local-account-sign-in-options"></a>Options de connexion du compte local

Azure AD B2C propose différents moyens d’authentifier des utilisateurs. Les utilisateurs peuvent se connecter à un compte local en utilisant un nom d’utilisateur et un mot de passe, et une vérification par téléphone (également appelée authentification sans mot de passe). L’inscription par e-mail est activée par défaut dans les paramètres du fournisseur d’identité de votre compte local.

Apprenez-en davantage sur les [options de connexion](sign-in-options.md) ou la [configuration du fournisseur d’identité de compte local](identity-provider-local.md).

## <a name="user-profile-attributes"></a>Attributs de profil utilisateur

Azure AD B2C vous permet de gérer les attributs communs des profils de compte de consommateur. Par exemple, le nom d’affichage, le nom de famille, le prénom, la ville, etc.

Vous pouvez également étendre le schéma Azure AD pour stocker des informations supplémentaires sur vos utilisateurs, comme leur pays/région de résidence, leur langue par défaut et leurs préférences (par exemple, s’ils souhaitent s’abonner à un bulletin d’informations ou activer l’authentification multifacteur). Pour plus d'informations, consultez les pages suivantes :

* [Attributs de profil utilisateur](user-profile-attributes.md)
* [Ajouter des attributs utilisateur et personnaliser l’entrée utilisateur](configure-user-input.md)

## <a name="sign-in-with-external-identity-providers"></a>Se connecter avec des fournisseurs d’identité externes

Vous pouvez configurer Azure AD B2C pour permettre aux utilisateurs de se connecter à votre application avec des informations d’identification de fournisseurs d’identités de réseau social et d’entreprise. Azure AD B2C peut fédérer avec les fournisseurs d’identité qui prennent en charge les protocoles OAuth 1.0, OAuth 2.0, OpenID Connect et SAML. Par exemple, Facebook, un compte Microsoft, Google, Twitter et AD FS.



:::image type="content" source="media/technical-overview/external-idps.png" alt-text="Fournisseurs d’identité externes.":::

Avec la fédération des fournisseurs d’identités externes, vous pouvez donner à vos consommateurs la possibilité de se connecter avec leurs comptes de réseaux sociaux ou d’entreprise existants, sans avoir à créer de compte spécialement pour votre application.

Dans la page d’inscription ou de connexion, Azure AD B2C affiche une liste de fournisseurs d’identités externes à partir de laquelle l’utilisateur peut faire son choix pour se connecter. Dès lors qu’il a sélectionné un des fournisseurs d’identités externes, il est emmené (redirigé) sur le site web du fournisseur sélectionné pour terminer le processus de connexion. Une fois l’utilisateur dûment connecté, il est retourné à Azure AD B2C pour l’authentification du compte dans votre application.



:::image type="content" source="media/technical-overview/external-idp.png" alt-text="Exemple de connexion sur un appareil mobile avec un compte de réseau social (Facebook).":::

Pour voir comment ajouter des fournisseurs d’identité dans Azure AD B2C, consultez [Ajouter des fournisseurs d’identité à vos applications dans Azure Active Directory B2C](add-identity-provider.md).

## <a name="identity-experiences-user-flows-or-custom-policies"></a>Expériences d’identité : flux utilisateur ou stratégies personnalisées

Dans Azure AD B2C, vous pouvez définir la logique métier que les utilisateurs suivent pour accéder à votre application. Par exemple, vous pouvez déterminer la séquence des étapes suivies par les utilisateurs lorsqu’ils se connectent, s’inscrivent, modifient un profil ou réinitialisent un mot de passe. À l’issue de la séquence, l’utilisateur acquiert un jeton et accède à votre application.

Dans Azure AD B2C, il existe deux moyens de fournir des expériences d’utilisateurs d’identités :

* Les **flux utilisateur** sont des stratégies prédéfinies, intégrées et configurables que nous fournissons, afin que vous puissiez créer des expériences d’inscription, de connexion et de modification de stratégie en quelques minutes.

* Les **stratégies personnalisées** vous permettent de créer vos propres parcours utilisateur pour des scénarios d’expérience d’identité complexes.

La capture d’écran suivante montre l’interface utilisateur des paramètres de flux d’utilisateurs et les fichiers de configuration de stratégies personnalisées.

![La capture d’écran montre l’interface utilisateur des paramètres de flux d’utilisateurs et les fichiers de configuration de stratégies personnalisées.](media/technical-overview/user-flow-vs-custom-policy.png)

Lisez l’article [Vue d’ensemble des flux d’utilisateurs et des stratégies personnalisées](user-flow-overview.md). Il donne une vue d’ensemble des flux d’utilisateurs et des stratégies personnalisées, et vous aide à décider quelle méthode correspond le mieux à vos besoins métier.

## <a name="user-interface"></a>Interface utilisateur

Dans Azure AD B2C, vous pouvez concevoir les expériences d’identité de vos utilisateurs de manière à ce que les pages s’affichent harmonieusement avec l’apparence personnalisée de votre marque. Vous bénéficiez d’un contrôle presque total sur le contenu HTML et CSS présenté à vos utilisateurs lorsqu’ils suivent les parcours d’identité de votre application. Grâce à cette flexibilité, vous pouvez conserver la marque et la cohérence visuelle entre votre application et Azure AD B2C.



:::image type="content" source="media/technical-overview/seamless-ux.png" alt-text="Captures d’écran de la page d’inscription et de connexion personnalisée avec une marque.":::

Pour plus d’informations sur la personnalisation de l’interface utilisateur, consultez :

* [Personnaliser l’interface utilisateur](customize-ui.md)
* [Personnaliser l’interface utilisateur avec des modèles HTML](customize-ui-with-html.md)
* [Activer JavaScript et sélectionner une version de mise en page](javascript-and-page-layout.md)

## <a name="custom-domain"></a>Domaine personnalisé

Vous pouvez personnaliser votre domaine Azure AD B2C dans les URL de redirection pour Azure AD B2C. Un domaine personnalisé vous permet de créer une expérience fluide afin que les pages s’affichent en combinaison avec le nom de domaine de votre application.

![Captures d’écran d’un domaine personnalisé Azure AD B2C](media/technical-overview/custom-domain.png)

Du point de vue de l’utilisateur, elles restent dans votre domaine pendant le processus de connexion au lieu de rediriger vers le domaine par défaut Azure AD B2C .b2clogin.com. Pour plus d’informations, consultez [Activer des domaines personnalisés](custom-domain.md).
 
## <a name="localization"></a>Localisation

La personnalisation de la langue dans Azure AD B2C vous permet de prendre en charge plusieurs langues pour répondre aux besoins de votre client. Microsoft fournit les traductions en 36 langues, mais vous pouvez également utiliser vos propres traductions pour n’importe quelle langue. Même si votre expérience est disponible dans une seule langue, vous pouvez personnaliser n’importe quel texte sur les pages.

![Trois pages d’inscription et de connexion montrant le texte de l’interface utilisateur dans différentes langues](media/technical-overview/localization.png)

Découvrez comment fonctionne la localisation dans [Personnalisation de la langue dans Azure Active Directory B2C](language-customization.md).

## <a name="email-verification"></a>Vérification par e-mail

Azure AD B2C garantit des adresses e-mail valides en demandant aux clients de les vérifier pendant le processus d’inscription et permet des flux de réinitialisation des mots de passe. Il empêche également des acteurs malveillants d’utiliser des processus automatisés pour générer des comptes frauduleux dans vos applications.

![Captures d’écran de la vérification d’e-mail dans Azure AD B2C](media/technical-overview/email-verification.png)

Vous pouvez personnaliser l’e-mail pour les utilisateurs qui s’inscrivent pour utiliser vos applications. En utilisant le fournisseur d’e-mails tiers, vous pouvez utiliser votre propre modèle d’e-mail, votre propre adresse De : et votre propre Objet ainsi que prendre en charge la localisation et les paramètres personnalisés des mots de passe à usage unique. Pour plus d'informations, consultez les pages suivantes :

* [Vérification des e-mails personnalisée avec Mailjet](custom-email-mailjet.md)
* [Vérification des e-mails personnalisée avec SendGrid](custom-email-sendgrid.md)

## <a name="add-your-own-business-logic-and-call-restful-api"></a>Ajouter votre propre logique métier et appeler l’API RESTful

Vous pouvez intégrer une API RESTful à la fois dans les flux d’utilisateurs et les stratégies personnalisées. La différence est que, dans les flux d’utilisateurs, vous effectuez des appels à des emplacements spécifiés, alors que dans les stratégies personnalisées, vous ajoutez votre propre logique métier au parcours. Cette fonctionnalité vous permet de récupérer et d’utiliser des données provenant de sources d’identité externes. Azure AD B2C peut échanger des données avec un service RESTful pour effectuer les actions suivantes :

* Afficher des messages d’erreur conviviaux, personnalisés.
* Valider les entrées utilisateur pour empêcher la persistance de données mal formées dans votre annuaire d’utilisateurs. Vous pouvez, par exemple, modifier les données entrées par l’utilisateur, comme la mise en majuscules de son prénom s’il l’a entré en minuscules.
* Enrichir les données utilisateur en intégrant davantage votre application métier d’entreprise.
* À l’aide d’appels RESTful, vous pouvez envoyer des notifications Push, mettre à jour des bases de données d’entreprise, exécuter un processus de migration utilisateur, gérer les autorisations, auditer des bases de données, etc.

Les programmes de fidélité sont un autre scénario activé par la prise en charge d’Azure AD B2C pour l’appel d’API REST. Par exemple, votre service RESTful peut recevoir l’adresse e-mail d’un utilisateur, interroger votre base de données de clients, puis retourner le numéro de fidélité de l’utilisateur à Azure AD B2C. 

Les données retournées peuvent être stockées dans le compte d’annuaire de l’utilisateur dans Azure AD B2C. Elles peuvent ensuite être évaluées dans les étapes suivantes de la stratégie ou être incluses dans le jeton d’accès.



:::image type="content" source="media/technical-overview/lob-integration.png" alt-text="Intégration métier dans une application mobile.":::

Vous pouvez ajouter un appel d’API REST à n’importe quelle étape du parcours utilisateur défini par une stratégie personnalisée. Par exemple, vous pouvez appeler une API REST :

* lors de la connexion, juste avant la validation des informations d’identification par Azure AD B2C ;
* immédiatement après la connexion ;
* avant la création par Azure AD B2C d’un compte dans l’annuaire ;
* après la création par Azure AD B2C d’un compte dans l’annuaire ;
* avant l’émission d’un jeton d’accès par Azure AD B2C.

Pour savoir comment utiliser les stratégies personnalisées qui permettent l’intégration de l’API RESTful dans Azure AD B2C, consultez [Intégrer les échanges de revendications d’API REST dans votre stratégie personnalisée Azure AD B2C](api-connectors-overview.md).

## <a name="protocols-and-tokens"></a>Protocoles et jetons

- Pour les applications, Azure AD B2C prend en charge les protocoles [OAuth 2.0](protocols-overview.md), [OpenID Connect](openid-connect.md) et [SAML](saml-service-provider.md) pour les parcours utilisateur. Votre application démarre le parcours utilisateur en envoyant des demandes d’authentification à Azure AD B2C. Le résultat d’une demande à Azure AD B2C est un jeton de sécurité, de type [jeton d’ID, jeton d’accès](tokens-overview.md) ou jeton SAML. Ce jeton de sécurité définit l’identité de l’utilisateur dans l’application.

- Pour les identités externes, Azure AD B2C prend en charge la fédération avec tout fournisseur d’identité OAuth 1.0, OAuth 2.0, OpenID Connect et SAML.

Le schéma suivant montre comment Azure AD B2C peut communiquer via divers protocoles au sein d’un même flux d’authentification :

![Schéma de fédération de l’application cliente basée sur OIDC avec un IdP s’appuyant sur SAML](media/technical-overview/protocols.png)

1. L’application par partie de confiance lance une demande d’autorisation à Azure AD B2C à l’aide d’OpenID Connect.
1. Quand un utilisateur de l’application choisit de se connecter avec un fournisseur d’identité externe utilisant le protocole SAML, Azure AD B2C appelle le protocole SAML pour communiquer avec ce fournisseur d’identité.
1. À l’issue de l’opération de connexion de l’utilisateur au fournisseur d’identité externe, Azure AD B2C retourne le jeton à l’application par partie de confiance au moyen d’OpenID Connect.

## <a name="application-integration"></a>Intégration d’applications

Lorsqu’un utilisateur veut se connecter à votre application, l’application initie une requête d’autorisation sur un point de terminaison fourni par un flux d’utilisateurs ou une stratégie personnalisée. Le flux d’utilisateur ou la stratégie personnalisée définit et contrôle l’expérience de l’utilisateur. Lorsque ce dernier termine un flux utilisateur, par exemple le flux *d’inscription ou de connexion*, Azure AD B2C génère un jeton, puis redirige l’utilisateur vers votre application.

:::image type="content" source="media/technical-overview/app-integration.png" alt-text="Application mobile, avec des flèches montrant le flux circulant entre l’application et la page de connexion Azure AD B2C.":::

Plusieurs applications peuvent utiliser le même flux utilisateur ou la même stratégie personnalisée. Une seule application peut utiliser plusieurs flux utilisateur ou stratégies personnalisées.

Par exemple, pour se connecter à une application, l’application utilise le flux utilisateur *d’inscription ou de connexion*. Une fois connecté, l’utilisateur peut avoir envie de modifier son profil ; l’application initie donc une autre requête d’autorisation, cette fois-ci au moyen du flux utilisateur de *modification de profil*.

## <a name="multifactor-authentication-mfa"></a>Authentification multifacteur (MFA)

L’authentification multifacteur (MFA) Azure AD B2C participe à la sécurisation de l’accès aux données et aux applications tout en maintenant une simplicité de gestion pour vos utilisateurs. Elle fournit une sécurité supplémentaire en exigeant une deuxième forme d’authentification, et procure une authentification renforcée en proposant toute une palette de méthodes d’authentification faciles à utiliser. 

Vos utilisateurs peuvent être amenés à s’authentifier par l’intermédiaire de MFA, selon les choix de configuration que vous avez faits en tant qu’administrateur.

Découvrez comment activer MFA dans les flux d’utilisateurs en consultant [Activer l’authentification multifacteur dans Azure Active Directory B2C](multi-factor-authentication.md).

## <a name="conditional-access"></a>Accès conditionnel

Les fonctionnalités de détection des risques d’Azure AD Identity Protection, y compris les utilisateurs à risque et les connexions à risque, sont détectées automatiquement et affichées dans votre locataire Azure AD B2C. Vous pouvez créer des stratégies d’accès conditionnel qui utilisent ces détections des risques afin de déterminer les actions de remédiation et d’appliquer des stratégies organisationnelles. 

![Flux d’accès conditionnel](media/technical-overview/conditional-access-flow.png)

Azure AD B2C évalue chaque événement de connexion et s’assure que toutes les conditions requises sont remplies avant d’accorder l’accès à l’utilisateur. Les utilisateurs ou les connexions à risque peuvent être bloqués, ou être soumis à une remédiation spécifique comme l’authentification multifacteur (MFA). Pour plus d’informations, consultez [Identity Protection et accès conditionnel](conditional-access-identity-protection-overview.md).

## <a name="password-complexity"></a>Complexité du mot de passe

Lors de l’inscription ou de la réinitialisation de mot de passe, vos utilisateurs doivent fournir un mot de passe qui respecte des règles de complexité. Par défaut, Azure AD B2C applique une stratégie de mot de passe fort. Azure AD B2C fournit également des options de configuration permettant de spécifier les exigences en matière de complexité pour les mots de passe utilisés par vos clients.

![Capture d’écran de l’expérience utilisateur de la complexité du mot de passe](media/technical-overview/password-complexity.png)

Pour plus d’informations, consultez [Configurer les exigences de complexité des mots de passe dans Azure AD B2C](password-complexity.md).

## <a name="force-password-reset"></a>Forcer la réinitialisation du mot de passe

En tant qu’administrateur du locataire Azure AD B2C, vous pouvez [réinitialiser le mot de passe d’un utilisateur](manage-users-portal.md#reset-a-users-password) qui viendrait à l’oublier. Vous pouvez aussi forcer les utilisateurs à réinitialiser leur mot de passe régulièrement. Pour plus d’informations, consultez [Configurer un flux de réinitialisation forcée du mot de passe](force-password-reset.md).



:::image type="content" source="media/technical-overview/force-password-reset-flow.png" alt-text="Flux de réinitialisation forcée du mot de passe.":::

## <a name="smart-account-lockout"></a>Verrouillage intelligent de compte

Pour éviter les tentatives de détection de mot de passe par force brute, Azure AD B2C utilise une stratégie sophistiquée permettant de verrouiller les comptes en fonction de l’adresse IP de la requête, des mots de passe entrés et de plusieurs autres facteurs. La durée du verrouillage est automatiquement augmentée suivant le risque et le nombre de tentatives.

![Compte verrouillé avec le système intelligent](media/technical-overview/smart-lockout1.png)

Pour plus d’informations sur la gestion des paramètres de protection des mots de passe, consultez [Atténuer les attaques sur les informations d’identification dans Azure AD B2C](threat-management.md).

## <a name="protect-resources-and-customer-identities"></a>Protéger les ressources et les identités des clients

Azure AD B2C est conforme à la sécurité, à la confidentialité et aux autres engagements décrits dans le [Centre de confidentialité Azure](https://www.microsoft.com/trustcenter/cloudservices/azure).

Les sessions sont modélisées en tant que données chiffrées, avec la clé de déchiffrement connue uniquement du service d’émission de jeton de sécurité Azure AD B2C. Un algorithme de chiffrement renforcé, AES-192, est utilisé. La confidentialité et l’intégrité de tous les chemins de communication sont protégées au moyen de TLS. Notre service d’émission de jeton de sécurité utilise un certificat de validation étendue (EV) pour TLS. En général, le service d’émission de jeton de sécurité atténue les attaques par scripts intersites (XSS) en ne restituant pas les entrées non approuvées.


:::image type="content" source="media/technical-overview/user-data.png" alt-text="Diagramme des données sécurisées, en transit et au repos.":::

### <a name="access-to-user-data"></a>Accès aux données utilisateur

Les locataires Azure AD B2C partagent de nombreuses caractéristiques avec les locataires Azure Active Directory d’entreprise, utilisés pour les employés et les partenaires. Les aspects partagés incluent des mécanismes d’affichage des rôles d’administration, d’affectation des rôles et d’audit des activités.

Vous pouvez attribuer des rôles pour contrôler quelles personnes peuvent effectuer certaines actions administratives dans Azure AD B2C, y compris :

* Créer et gérer tous les aspects des flux d’utilisateurs
* Créer et gérer le schéma d’attribut disponible pour tous les flux d’utilisateurs
* Configurer des fournisseurs d’identités pour une utilisation dans une fédération directe
* Créer et gérer des stratégies de framework de confiance dans Identity Experience Framework (stratégies personnalisées)
* Gérer les secrets pour la fédération et le chiffrement dans Identity Experience Framework (stratégies personnalisées)

Pour plus d’informations sur les rôles Azure AD, notamment la prise en charge des rôles d’administration Azure AD B2C, consultez [Autorisations des rôles d’administrateur dans Azure Active Directory](../active-directory/roles/permissions-reference.md).

## <a name="auditing-and-logs"></a>Audit et journaux

Azure AD B2C produit des journaux d’audit contenant des informations sur les activités liées à ses ressources, ses jetons émis et ses accès administrateur. Vous pouvez utiliser les journaux d’audit pour comprendre l’activité de la plateforme et diagnostiquer les problèmes. Les entrées du journal d’audit sont disponibles peu de temps après la survenue de l’activité qui a généré l’événement.

Dans un journal d’audit, qui est disponible pour votre locataire Azure AD B2C ou un utilisateur particulier, vous trouvez des informations, notamment :

* Activités relatives à l’autorisation d’un utilisateur d’accéder à des ressources B2C (par exemple, un administrateur qui accède à la liste des stratégies B2C)
* Activités relatives aux attributs d’annuaire récupérés quand un administrateur se connecte en utilisant le Portail Microsoft Azure
* Opérations de création, lecture, mise à jour et suppression (CRUD) sur les applications B2C
* Opérations CRUD sur les clés stockées dans un conteneur de clé B2C
* Opérations CRUD sur les ressources B2C (par exemple, stratégies et fournisseurs d’identité)
* Validation des informations d’identification des utilisateurs et de l’émission des jetons

![Journal d’audit d’un utilisateur particulier affiché dans le portail Azure](media/technical-overview/audit-log.png)

Pour plus d’informations sur les journaux d’audit, consultez [Accès aux journaux d’audit Azure AD B2C](view-audit-logs.md).

## <a name="usage-analytics"></a>Analyse de l'utilisation

Azure AD B2C vous permet de savoir à quel moment les personnes s’inscrivent ou se connectent à votre application, où les utilisateurs se trouvent, ainsi que les navigateurs et les systèmes d’exploitation qu’ils utilisent. 

En intégrant Azure Application Insights dans les stratégies personnalisées Azure AD B2C, vous pouvez obtenir des informations sur la façon dont les utilisateurs s’inscrivent, se connectent, réinitialisent leur mot de passe ou modifient leur profil. Grâce à de telles informations, vous pouvez prendre des décisions fondées sur des données pour les cycles de développement à venir.

Pour plus d’informations, consultez [Suivre le comportement des utilisateurs dans Azure Active Directory B2C à l’aide d’Application Insights](analytics-with-application-insights.md).

## <a name="region-availability-and-data-residency"></a>Disponibilité des régions et résidence des données
Le service Azure AD B2C est généralement disponible dans le monde entier, avec la possibilité de résidence des données dans des régions comme indiqué dans [Produits disponibles par région](https://azure.microsoft.com/regions/services/). La résidence des données est déterminée par le pays ou la région que vous sélectionnez quand vous [créez votre locataire](tutorial-create-tenant.md). 

En savoir plus sur la [Disponibilité des régions et résidence des données Azure Active Directory B2C](data-residency.md) et le [Contrat de niveau de service (SLA) pour Azure Active Directory B2C](https://azure.microsoft.com/support/legal/sla/active-directory-b2c/v1_1).

## <a name="automation-using-microsoft-graph-api"></a>Automatisation à l’aide de l’API Microsoft Graph

Utilisez l’API Microsoft Graph pour gérer votre annuaire Azure AD B2C. Vous pouvez également créer l’annuaire Azure AD B2C lui-même. Vous pouvez gérer les utilisateurs, les fournisseurs d’identité, les flux d’utilisateurs, les stratégies personnalisées et bien plus encore. 

Apprenez-en davantage sur la façon de [gérer Azure AD B2C avec Microsoft Graph](microsoft-graph-operations.md). 

## <a name="azure-ad-b2c-service-limits-and-restrictions"></a>Restrictions et limites du service Azure AD B2C
En savoir plus sur les [Restrictions et limites du service Azure AD B2C](service-limits.md)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous disposez d’une vue plus détaillée sur les fonctionnalités et les aspects techniques d’Azure Active Directory B2C :
- Démarrez avec notre [tutoriel pour créer un locataire Azure Active Directory B2C](tutorial-create-tenant.md).
- [Configurer la connexion pour une application monopage avec Azure Active Directory B2C](quickstart-single-page-app.md)
- [Concepts Azure Active Directory B2C](application-types.md) 
