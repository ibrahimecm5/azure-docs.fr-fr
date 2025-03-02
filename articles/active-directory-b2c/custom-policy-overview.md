---
title: Vue d’ensemble des stratégies personnalisées Azure Active Directory B2C
description: Rubrique sur les stratégies personnalisées Azure Active Directory B2C et l’infrastructure d’expérience d’identité.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/14/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 51ed0b6bd0f5bd5eabf76c6789ab36a922653fd1
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130222833"
---
# <a name="azure-ad-b2c-custom-policy-overview"></a>Vue d’ensemble de la stratégie personnalisée Azure AD B2C

Les stratégies personnalisées sont des fichiers de configuration qui définissent le comportement de votre locataire Azure Active Directory B2C (Azure AD B2C). Si les [flux d’utilisateurs](user-flow-overview.md) sont prédéfinis sur le portail Azure AD B2C pour les tâches d’identité les plus courantes, les stratégies personnalisées, elles, peuvent être entièrement modifiés par un développeur d’identité pour effectuer de nombreuses tâches différentes.

Une stratégie personnalisée est entièrement configurable et basée sur des stratégies. Une stratégie personnalisée orchestre l’approbation entre les entités dans les protocoles standard. Par exemple, OpenID Connect, OAuth, SAML et quelques autres non standard, comme les échanges de revendications système à système basés sur une API REST. L’infrastructure crée des expériences conviviales en marque blanche.

Une stratégie personnalisée est représentée par un ou plusieurs fichiers au format XML qui se font mutuellement référence dans une chaîne hiérarchique. Les éléments XML définissent les blocs de construction, l’interaction avec l’utilisateur et d’autres parties, ainsi que la logique métier. 

## <a name="custom-policy-starter-pack"></a>Pack de démarrage de stratégie personnalisée

Le [pack de démarrage](tutorial-create-user-flows.md?pivots=b2c-custom-policy#get-the-starter-pack) de stratégie personnalisée Azure AD B2C est fourni avec plusieurs stratégies prédéfinies pour vous permettre de démarrer rapidement. Chacun de ces packs de démarrage contient le plus petit nombre possible de profils techniques et de parcours utilisateur nécessaires pour réaliser les scénarios décrits :

- **LocalAccounts** : vous permet d’utiliser uniquement des comptes locaux.
- **SocialAccounts** : vous permet d’utiliser uniquement des comptes sociaux (ou fédérés).
- **SocialAndLocalAccounts** : vous permet d’utiliser à la fois des comptes locaux et des comptes sociaux. La plupart de nos exemples font référence à cette stratégie.
- **SocialAndLocalAccountsWithMFA** : active les options social, local et authentification multifacteur.

Dans le [référentiel GitHub d’exemples Azure AD B2C](https://github.com/azure-ad-b2c/samples), vous trouverez des exemples de plusieurs parcours utilisateur CIAM personnalisés Azure AD B2C améliorés. Il s’agit par exemple d’améliorations de stratégie de compte local, d’améliorations de stratégie de compte social, d’améliorations de l’authentification multifacteur, d’améliorations de l’interface utilisateur, d’améliorations génériques, de la migration d’applications, de la migration des utilisateurs, d’un accès conditionnel, d’un test web et de CI/CD.
 
## <a name="understanding-the-basics"></a>Compréhension des concepts de base 

### <a name="claims"></a>Revendications

Une revendication fournit un stockage temporaire de données lors d’une exécution de stratégie Azure AD B2C. Elle peut stocker des informations sur l’utilisateur, telles que son prénom, son nom ou toute autre revendication obtenue de l’utilisateur ou d’autres systèmes (échanges de revendications). Le [schéma de revendications](claimsschema.md) est l’endroit où vous déclarez vos revendications. 

Quand la stratégie s’exécute, Azure AD B2C envoie et reçoit des revendications vers et depuis des parties internes et externes, puis envoie un sous-ensemble de ces revendications à votre application par partie de confiance dans le jeton. Les revendications sont utilisées comme suit : 

- Une revendication est enregistrée, lue ou mise à jour par rapport à l’objet utilisateur de l’annuaire.
- Une revendication est reçue d’un fournisseur d’identité externe.
- Les revendications sont envoyées ou reçues à l’aide d’un service d’API REST personnalisée.
- Les données sont collectées en tant que revendications de l’utilisateur lors de l’inscription ou de la modification des flux de profil.

### <a name="manipulating-your-claims"></a>Manipulation de vos revendications

Les [transformations de revendications](claimstransformations.md) sont des fonctions prédéfinies utilisables pour convertir une revendication donnée en une autre, évaluer une revendication ou définir une valeur de revendication. Citons par exemple l’ajout d’un élément à une collection de chaînes, la modification de la casse d’une chaîne et l’évaluation d’une revendication de date et d’heure. Une transformation de revendications spécifie une méthode de transformation. 

### <a name="customize-and-localize-your-ui"></a>Personnaliser et localiser votre interface utilisateur

Pour collecter des informations de vos utilisateurs en présentant une page dans leur navigateur web, utilisez le [profil technique autodéclaré](self-asserted-technical-profile.md). Vous pouvez modifier votre profil technique autodéclaré pour [ajouter des revendications et personnaliser l’entrée utilisateur](./configure-user-input.md).

Pour [personnaliser l’interface utilisateur](customize-ui-with-html.md) pour votre profil technique autodéclaré, vous spécifiez une URL dans l’élément [définition de contenu](contentdefinitions.md) avec du contenu HTML personnalisé. Dans le profil technique autodéclaré, vous pointez sur cet ID de définition de contenu.

Pour personnaliser des chaînes spécifiques d’une langue, utilisez l’élément [localisation](localization.md). Une définition de contenu peut contenir une référence [localisation](localization.md) qui spécifie une liste de ressources localisées à charger. Azure AD B2C fusionne des éléments d’interface utilisateur avec le contenu HTML chargé à partir de votre URL, puis affiche la page à l’utilisateur. 

## <a name="relying-party-policy-overview"></a>Vue d’ensemble d’une stratégie de partie de confiance.

Une application par partie de confiance, appelée fournisseur de services dans le protocole SAML, appelle la [stratégie de partie de confiance](relyingparty.md) pour exécuter un parcours utilisateur spécifique. La stratégie de partie de confiance spécifie le parcours utilisateur à exécuter, ainsi que la liste des revendications que le jeton inclut. 

![Diagramme indiquant le workflow d’exécution de la stratégie](./media/custom-policy-overview/custom-policy-execution.png)

Toutes les applications par partie de confiance qui utilisent la même stratégie reçoivent les mêmes revendications de jeton. L’utilisateur passe par le même parcours utilisateur.

### <a name="user-journeys"></a>Parcours utilisateur

Un [parcours utilisateur](userjourneys.md) vous permet de définir la logique métier avec un chemin que l’utilisateur doit suivre pour accéder à votre application. L’utilisateur est conduit à travers le parcours utilisateur pour récupérer les revendications à présenter à votre application. Chaque parcours utilisateur est construit à partir d’une séquence d’[étapes d’orchestration](userjourneys.md#orchestrationsteps). Un utilisateur doit atteindre la dernière étape pour acquérir un jeton. 

Les instructions suivantes expliquent comment ajouter des étapes d’orchestration à la stratégie de [pack de démarrage de compte social et local](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts). Voici un exemple d’appel d’API REST qui a été ajouté.

![Parcours utilisateur personnalisé](media/custom-policy-overview/user-journey-flow.png)


### <a name="orchestration-steps"></a>Étapes d’orchestration

Une étape d’orchestration fait référence à une méthode qui implémente son rôle ou sa fonctionnalité prévus. Cette méthode est appelée [profil technique](technicalprofiles.md). Lorsque votre parcours utilisateur a besoin d’une création de branche pour mieux représenter la logique métier, l’étape d’orchestration fait référence à un [sous-parcours](subjourneys.md). Un sous-parcours contient son propre ensemble d’étapes d’orchestration.

Un utilisateur doit atteindre la dernière étape d’orchestration dans le parcours utilisateur pour acquérir un jeton. Toutefois, il se peut que des utilisateurs n’aient pas besoin de parcourir toutes les étapes d’orchestration. Des étapes d’orchestration peuvent être exécutées de manière conditionnelle, en fonction de [conditions préalables](userjourneys.md#preconditions) définies dans l’étape d’orchestration. 

Une fois qu’étape d’orchestration a été accomplie, Azure AD B2C stocke les revendications générées dans le **conteneur de revendications**. Les revendications figurant dans le conteneur de revendications peuvent être utilisées par toutes les autres étapes d’orchestration dans le parcours utilisateur.

Le diagramme suivant montre comment les étapes d’orchestration du parcours utilisateur peuvent accéder au conteneur de revendications.

![Parcours utilisateur Azure AD B2C](media/custom-policy-overview/user-journey-diagram.png)

### <a name="technical-profile"></a>Profil technique

Un profil technique fournit une interface pour communiquer avec différents types de parties. Un parcours utilisateur combine des profils techniques appelants via des étapes d’orchestration pour définir votre logique métier.

Tous les types de profils techniques partagent le même concept. Vous envoyez des revendications d’entrée, exécutez une transformation de revendications et communiquez avec la partie configurée. Une fois le processus terminé, le profil technique retourne les revendications de sortie au conteneur de revendications. Pour plus d’informations, consultez [Vue d’ensemble des profils techniques](technicalprofiles.md).

### <a name="validation-technical-profile"></a>Profil technique de validation

Quand un utilisateur interagit avec l’interface utilisateur, vous pouvez valider les données collectées. Pour interagir avec l’utilisateur, vous devez utiliser un [profil technique autodéclaré](self-asserted-technical-profile.md).

Pour valider l’entrée utilisateur, un [profil technique de validation](validation-technical-profile.md) est appelé à partir du profil technique autodéclaré. Un profil technique de validation est une méthode permettant d’appeler un profil technique non interactif. Dans ce cas, le profil technique peut retourner des revendications de sortie ou un message d’erreur. Le message d’erreur s’affiche à l’utilisateur, permettant à celui-ci de réessayer.

Le diagramme suivant illustre la façon dont Azure AD B2C utilise un profil technique de validation pour valider les informations d’identification de l’utilisateur.

![Diagramme de profil technique de validation](media/custom-policy-overview/validation-technical-profile.png)

## <a name="inheritance-model"></a>Modèle d’héritage

Chaque pack de démarrage comprend les fichiers suivants :

- Fichier **de base** contenant la plupart des définitions. Pour faciliter la résolution des problèmes et la maintenance à long terme de vos stratégies, essayez de limiter le nombre de modifications que vous apportez à ce fichier.
- Fichier de **localisation** qui contient les chaînes de localisation. Ce fichier de stratégie est dérivé du fichier de base. Ce fichier permet de prendre en charge différentes langues en fonction de vos besoins.
- Fichier d’**extensions** contenant les modifications de configuration propres à votre locataire. Ce fichier de stratégie est dérivé du fichier de localisation. Utilisez-le pour ajouter de nouvelles fonctionnalités ou remplacer des fonctionnalités existantes. Par exemple, utilisez-le pour fédérer avec de nouveaux fournisseurs d’identité.
- Fichier de **partie de confiance** qui est le fichier centré sur une tâche unique, qui est appelé directement par une application par partie de confiance, telle que vos applications web, mobiles ou de bureau. Chaque tâche, telle que l’inscription, la connexion ou la modification du profil, nécessite son propre fichier de stratégie de partie de confiance. Ce fichier de stratégie est dérivé du fichier d’extensions.

Le modèle d’héritage est le suivant :

- La stratégie enfant peut hériter, à tous les niveaux, de la stratégie parente et l’étendre en ajoutant de nouveaux éléments.
- Dans des scénarios plus complexes, vous pouvez ajouter des niveaux d’héritage (jusqu’à 10 au total).
- Vous pouvez ajouter des stratégies de partie de confiance. Par exemple, supprimer mon compte, modifier un numéro de téléphone ou une stratégie de partie de confiance SAML, et bien plus encore.

Le diagramme suivant montre la relation entre les fichiers de stratégie et les applications par partie de confiance.

![Diagramme indiquant le modèle d’héritage de stratégie d’infrastructure de confiance](media/custom-policy-overview/policies.png)


## <a name="guidance-and-best-practices"></a>Aide et bonnes pratiques

### <a name="best-practices"></a>Meilleures pratiques

Au sein d’une stratégie personnalisée Azure AD B2C, vous pouvez intégrer votre propre logique métier pour créer les expériences utilisateur dont vous avez besoin et étendre les fonctionnalités du service. Nous proposons un ensemble de meilleures pratiques et de recommandations pour commencer.

- Créez votre logique dans la **stratégie d’extension** ou la **stratégie de partie de confiance**. Vous pouvez ajouter de nouveaux éléments qui remplaceront la stratégie de base en référençant le même ID. Cette approche vous permettra d’effectuer un scale-out de votre projet tout en facilitant la mise à niveau de la stratégie de base par la suite si Microsoft met en production de nouveaux packs de démarrage.
- Dans la **stratégie de base**, nous vous recommandons vivement d’éviter d’apporter des modifications. Si nécessaire, formulez des commentaires à l’emplacement des modifications.
- Lorsque vous remplacez un élément, tel que des métadonnées de profil technique, évitez de copier le profil technique entier à partir de la stratégie de base. Au lieu de cela, copiez uniquement la section requise de l’élément. Pour obtenir un exemple illustrant la manière d’apporter la modification, consultez [Désactiver la vérification par e-mail](./disable-email-verification.md).
- Pour réduire la duplication des profils techniques, où les principales fonctionnalités sont partagées, utilisez une [inclusion de profil technique](technicalprofiles.md#include-technical-profile).
- Évitez d’écrire dans l’annuaire Azure AD lors de la connexion, ce qui peut entraîner des problèmes de limitation.
- Si votre stratégie comporte des dépendances externes (par exemple des API REST), vérifiez qu’elles sont hautement disponibles.
- Pour améliorer l’expérience utilisateur, assurez-vous que vos modèles HTML personnalisés sont déployés globalement en utilisant une [distribution de contenu en ligne](../cdn/index.yml). Azure Content Delivery Network (CDN) permet de réduire les temps de chargement, d’économiser la bande passante et d’améliorer le temps de réponse.
- Si vous souhaitez apporter une modification au parcours utilisateur, copiez-le intégralement de la stratégie de base à la stratégie d’extension. Indiquez un ID unique pour ce parcours utilisateur. Ensuite, dans la [stratégie de partie de confiance](relyingparty.md), modifiez l’élément [parcours utilisateur par défaut](relyingparty.md#defaultuserjourney) pour pointer vers le nouveau parcours utilisateur.

## <a name="troubleshooting"></a>Résolution des problèmes

Lorsque vous développez avec des stratégies Azure AD B2C, vous pouvez rencontrer des erreurs ou des exceptions lors de l’exécution de votre parcours utilisateur. Vous pouvez examiner les problèmes à l’aide d’Application Insights.

- Intégrez Application Insights avec Azure AD B2C pour [diagnostiquer les exceptions](troubleshoot-with-application-insights.md).
- [L’extension Azure AD B2C pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) peut vous aider à [visualiser les journaux](https://github.com/azure-ad-b2c/vscode-extension/blob/master/src/help/app-insights.md) en y accédant à partir d’un nom et d’une heure de stratégie.
- L’erreur la plus courante lors de la configuration des stratégies personnalisées concerne la mise en forme du code XML. Utilisez une [validation de schéma XML](troubleshoot-custom-policies.md) pour identifier des erreurs avant de charger votre fichier XML.

## <a name="continuous-integration"></a>Intégration continue

En utilisant un pipeline d’intégration continue et livraison continue (CI/CD) que vous configurez dans Azure Pipelines, vous pouvez [inclure vos stratégies personnalisées Azure AD B2C dans la livraison de vos logiciels](deploy-custom-policies-devops.md) et l’automatisation du contrôle de leur code. Lorsque vous déployez dans différents environnements Azure AD B2C, par exemple de dev, de test et de production, nous vous recommandons de supprimer les processus manuels et d’effectuer des tests automatisés à l’aide d’Azure Pipelines.

## <a name="prepare-your-environment"></a>Préparation de votre environnement

Vous commencez avec une stratégie personnalisée Azure AD B2C :

1. [Créer un client Azure AD B2C](tutorial-create-tenant.md)
1. [Inscrivez une application web](tutorial-register-applications.md) sur le Portail Azure pour pouvoir tester votre stratégie.
1. Ajoutez les [clés de stratégie](tutorial-create-user-flows.md?pivots=b2c-custom-policy#add-signing-and-encryption-keys-for-identity-experience-framework-applications) nécessaires et [inscrivez les applications Identity Experience Framework](tutorial-create-user-flows.md?pivots=b2c-custom-policy#register-identity-experience-framework-applications).
1. [Procurez-vous le pack de démarrage de stratégie Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy#get-the-starter-pack) et chargez-le sur votre locataire. 
1. Après avoir chargé le pack de démarrage, [testez votre stratégie d’inscription ou de connexion](tutorial-create-user-flows.md?pivots=b2c-custom-policy#test-the-custom-policy).
1. Nous vous recommandons de télécharger et d’installer [Visual Studio Code](https://code.visualstudio.com/) (VS Code). Visual Studio Code est un éditeur de code source léger mais puissant. Il s’exécute sur un poste de travail et est disponible pour Windows, macOS et Linux. Vous pouvez parcourir et modifier rapidement vos fichiers XML de stratégie personnalisée Azure AD B2C en installant [l’extension Azure AD B2C pour VS Code](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c).
 
## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré et testé votre stratégie de Azure AD B2C, vous pouvez commencer à la personnaliser. Pour savoir comment procéder, consultez les articles suivants :

- [Ajouter des revendications et personnaliser l’entrée utilisateur](./configure-user-input.md) dans des stratégies personnalisées. Découvrez comment définir une revendication et l’ajouter à l’interface utilisateur en personnalisant certains profils techniques du pack de démarrage.
- [Personnaliser l’interface utilisateur](customize-ui-with-html.md) de votre application à l’aide d’une stratégie personnalisée. Découvrez comment créer votre propre contenu HTML et personnaliser la définition du contenu.
- [Localiser l’interface utilisateur](./language-customization.md) de votre application à l’aide d’une stratégie personnalisée. Apprenez à configurer la liste des langues prises en charge et fournir des étiquettes spécifiques de la langue en ajoutant l’élément ressources localisées.
- Lors du développement et des tests de votre stratégie, vous pouvez [désactiver la vérification par e-mail](./disable-email-verification.md). Découvrez comment remplacer les métadonnées d’un profil technique.
- [Configurez la connexion avec un compte Google](./identity-provider-google.md) en utilisant des stratégies personnalisées. Découvrez comment créer un fournisseur de revendications avec un profil technique OAuth2. Ensuite, personnalisez le parcours utilisateur pour inclure l’option de connexion Google.
- Pour diagnostiquer des problèmes liés à vos stratégies personnalisées, vous pouvez [Collecter les journaux Azure Active Directory B2C avec Application Insights](troubleshoot-with-application-insights.md). Découvrez comment ajouter de nouveaux profils techniques et configurer votre stratégie de partie de confiance.
