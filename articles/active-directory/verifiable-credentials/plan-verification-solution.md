---
title: Planifier votre solution de vérification Justificatifs vérifiables Azure Active Directory (préversion)
description: Découvrez les informations fondamentales vous permettant de planifier et concevoir votre solution de vérification.
documentationCenter: ''
author: barbaraselden
manager: martinco
ms.service: active-directory
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 07/20/2021
ms.author: baselden
ms.custom: references_regions
ms.openlocfilehash: 36eb4676c63a539004aba9c000702ba964f05d3c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132332418"
---
# <a name="plan-your-azure-active-directory-verifiable-credentials--verification-solution-preview"></a>Planifier votre solution de vérification Justificatifs vérifiables Azure Active Directory (préversion)

>[!IMPORTANT]
> Les justificatifs vérifiables Azure Active Directory sont actuellement en préversion publique. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Le service Justificatifs vérifiables Azure Active Directory (Azure AD VC) de Microsoft vous permet de faire confiance aux preuves d’identité des utilisateurs sans étendre votre limite de confiance en créant des comptes ou en vous fédérant avec un autre fournisseur d’identité. En utilisant des justificatifs vérifiables basés sur une norme ouverte, un échange de vérification permet aux applications de demander des informations d’identification qui ne sont pas liées à un domaine spécifique. Il est ainsi plus facile de demander et de vérifier des informations d’identification à grande échelle.

Si vous ne l’avez pas encore fait, nous vous suggérons de consulter la [présentation de l’architecture Justificatifs vérifiables Azure AD](introduction-to-verifiable-credentials-architecture.md). Vous pouvez également consulter [Planifier votre solution d’émission de justificatifs vérifiables Azure AD](plan-issuance-solution.md).

## <a name="scope-of-guidance"></a>Étendue des recommandations

Ce contenu couvre les aspects techniques de la planification d’une solution de vérification de justificatifs vérifiables à l’aide des produits et services Microsoft. La solution interagit avec le réseau Identity Overlay Network (ION) qui fait office d’infrastructure à clé publique décentralisée (DPKI). 

Les technologies connexes qui ne sont pas spécifiques aux solutions de vérification sont hors du champ d’application. Par exemple, les sites web sont utilisés dans une solution de vérification de justificatifs vérifiables mais la planification du déploiement d’un site web n’est pas traitée en détail.

Lorsque vous planifiez votre solution de vérification, vous devez tenir compte de la capacité commerciale qui est ajoutée ou modifiée et des capacités informatiques qui peuvent être exploitées ou qui doivent être ajoutées pour créer la solution. Vous devez également tenir compte de la formation nécessaire pour les personnes impliquées dans le processus commercial ainsi que pour les personnes qui assistent les utilisateurs finaux et le personnel de la solution. Ces sujets ne sont pas abordés ici. Nous vous recommandons de consulter l’infrastructure [Microsoft Azure Well-Architected Framework](/azure/architecture/framework/) pour obtenir des informations sur ces sujets.

## <a name="components-of-the-solution"></a>Composants de la solution

Dans le cadre de votre plan pour une solution de vérification, vous devez activer les interactions entre le vérificateur, le sujet et l’émetteur. Dans cet article, les termes « partie de confiance » et « vérificateur » sont utilisés de manière interchangeable. Le diagramme suivant montre les composants de votre architecture de vérification.

![Composants d’une solution de vérification](media/plan-verification-solution/verification-solution-architecture.png)


### <a name="azure-ad-verifiable-credentials-service"></a>Service d’informations d’identification vérifiables Azure AD

Dans le contexte d’une solution de vérification, le service Justificatifs vérifiables Azure AD est l’interface entre les composants Microsoft de la solution et ION. Le service configure la clé définie sur Key Vault, configure l’identifiant décentralisé (DID) et écrit le document DID dans ION, où il peut être utilisé par des sujets et des émetteurs. 

### <a name="azure-active-directory-tenant"></a>Locataire Azure Active Directory 

Le service nécessite un locataire Azure AD qui fournit un plan de contrôle de la gestion des identités et des accès (IAM) pour les ressources Azure qui font partie de la solution. Il n’y a qu’une seule instance du service Justificatifs vérifiables Azure AD au sein d’un locataire, et elle émet un seul document DID représentant le vérificateur. Si plusieurs parties de confiance utilisent votre service de vérification, elles utilisent toutes le même DID de vérificateur. Le DID du vérificateur fournit des pointeurs vers la clé publique qui permet aux sujets et aux émetteurs de valider les messages provenant de la partie de confiance.

### <a name="azure-key-vault"></a>Azure Key Vault

![Azure Key Vault](./media/plan-verification-solution/verification-solution-key-vault.png)

Le service Azure Key Vault stocke vos clés de vérification, qui sont générées lorsque vous activez le service d’émission de justificatifs vérifiables Azure AD. Les clés sont utilisées pour assurer la sécurité des messages. Chaque vérificateur dispose d’un jeu de clés unique utilisé pour la signature, la mise à jour et la récupération des justificatifs vérifiables. Ce jeu de clés est utilisé chaque fois que vous répondez à une demande de vérification. Le jeu de clés de Microsoft utilise actuellement la cryptographie à courbe elliptique (ECC) [SECP256k1](https://en.bitcoin.it/wiki/Secp256k1). Nous explorons d’autres schémas de signature de chiffrement qui seront adoptés par l’ensemble de la communauté DID.

### <a name="azure-ad-vc-apis-and-sdks"></a>API et Kits de développement logiciel (SDK) Justificatifs vérifiables Azure AD

![API et Kits de développement logiciel (SDK) Justificatifs vérifiables Azure AD](./media/plan-verification-solution/verification-solution-tools.png)

Des interfaces de programmation d’applications (API) et un Kit de développement logiciel (SDK) fournissent aux développeurs une méthode pour extraire les interactions entre les composants de la solution pour exécuter des opérations de vérification. 

### <a name="ion"></a>ION 

![Justificatifs vérifiables Azure AD ION](./media/plan-verification-solution/verification-solution-ion.png)

Les solutions de justificatifs vérifiables utilisent un système de registre décentralisé pour enregistrer les transactions. Microsoft utilise [Identity Overlay Network (ION)](https://identity.foundation/ion/), [un réseau basé sur Sidetree](https://identity.foundation/sidetree/spec/) qui utilise Bitcoin comme registre de type blockchain pour implémenter un identifiant décentralisé (DID). Le document DID de l’émetteur est stocké dans ION et utilisé par les parties à la transaction pour effectuer des vérifications de signature de chiffrement.

### <a name="microsoft-authenticator-application"></a>Application Microsoft Authenticator

![Application Microsoft Authenticator](media/plan-verification-solution/verification-solution-authenticator.png)

Microsoft Authenticator est l’application mobile qui orchestre les interactions entre la partie de confiance, l’utilisateur, le service d’émission de justificatifs vérifiables Azure AD et les dépendances décrites dans le contrat utilisé pour émettre les justificatifs vérifiables. Il agit comme un portefeuille numérique dans lequel le détenteur des justificatifs vérifiables stocke ces derniers. C’est également le mécanisme utilisé pour présenter les justificatifs vérifiables à des fins de vérification.

### <a name="relying-party-rp"></a>Partie de confiance 

![Composants de la partie de confiance](media/plan-verification-solution/verification-solution-relying-party.png)

#### <a name="web-front-end"></a>Web frontal

Le serveur web frontal de la partie de confiance utilise les API ou le Kit de développement logiciel (SDK) Justificatifs vérifiables Azure AD pour vérifier les justificatifs vérifiables en générant des liens profonds ou des codes QR qui sont consommés par le portefeuille du sujet. Selon le scénario, le serveur frontal peut être un site web interne ou accessible publiquement pour permettre aux utilisateurs finaux d’effectuer des vérifications. Toutefois, les points de terminaison auxquels le portefeuille accède doivent être accessibles publiquement. Plus précisément, il contrôle la redirection vers le portefeuille avec des paramètres de demande spécifiques. Pour ce faire, utilisez les API et le Kit de développement logiciel (SDK) fournis par Microsoft.

#### <a name="business-logic"></a>Logique métier 

Vous pouvez créer une nouvelle logique ou utiliser une logique existante qui est spécifique à la partie de confiance et améliorer cette logique avec la présentation de justificatifs vérifiables.

## <a name="scenario-specific-designs"></a>Conceptions spécifiques aux scénarios

Voici des exemples de conceptions visant à satisfaire des cas d’usage spécifiques. Le premier concerne l’intégration de comptes, qui permet de réduire le temps, les coûts et les risques associés à l’intégration de nouveaux employés. Le deuxième concerne la récupération du compte, qui permet à un utilisateur final de récupérer ou de déverrouiller son compte à l’aide d’un mécanisme en libre-service. Le troisième est destiné à l’accès aux applications et aux ressources de grande valeur, notamment dans les cas d’usage interentreprises où l’accès est accordé à des personnes travaillant pour d’autres sociétés. 

### <a name="account-onboarding"></a>Intégration de comptes

Les justificatifs vérifiables peuvent également être utilisés pour accélérer l’intégration en remplaçant certaines interactions humaines. Les justificatifs vérifiables peuvent être utilisés pour permettre aux employés, étudiants, citoyens ou autres d’accéder à des services. Par exemple, au lieu qu’un employé doive se rendre dans un bureau central pour activer son badge, il peut utiliser des justificatifs vérifiables attestant de son identité pour activer un badge qui lui est remis à distance. Au lieu qu’un citoyen reçoive un code qu’il doit accepter pour accéder aux services gouvernementaux, il peut utiliser des justificatifs vérifiables pour prouver son identité et obtenir l’accès. 

![Scénario d’intégration de compte](media/plan-verification-solution/verification-solution-onboarding.png)

#### <a name="additional-elements"></a>Éléments supplémentaires 

**Portail d’intégration** : Il s’agit d’un serveur web frontal qui orchestre les appels aux APIs/SDKs Justificatifs vérifiables Azure AD pour la présentation et la validation des justificatifs vérifiables, ainsi que la logique d’intégration des comptes.

**Logique/flux de travail personnalisés** : Logique spécifique avec des étapes propres à l’organisation avant et après la mise à jour du compte d’utilisateur. Cela peut inclure des flux de travail d’approbation, des validations supplémentaires, la journalisation, les notifications, etc.

**Systèmes d’identité cibles**: Référentiels d’identité propres à l’organisation avec lesquels le portail d’intégration doit interagir lors de l’intégration des sujets. Les systèmes à intégrer sont déterminés en fonction des types d’identité que vous souhaitez intégrer à la validation des justificatifs vérifiables. Voici quelques scénarios courants de vérification d’identité pour l’intégration :

* Les identités externes telles que les vendeurs, les partenaires, les fournisseurs et les clients, qui, dans les systèmes d’identité centralisés, sont intégrées à Azure AD à l’aide d’API pour émettre des invitations interentreprises (business-to-business, B2B) ou l’attribution de la gestion des droits d’utilisation aux packages. 

* Les identités des employés, qui, dans les systèmes d’identité centralisés, sont déjà intégrées à Azure AD par le biais des systèmes de ressources humaines (RH). Dans ce cas, la vérification d’identité peut être intégrée dans le cadre des étapes existantes des flux de travail des RH. 

#### <a name="design-considerations"></a>Remarques sur la conception

* **Émetteur** : L’intégration de compte est un bon choix pour un service de vérification de l’identité externe comme émetteur de justificatifs vérifiables. Voici quelques exemples de vérifications de l’intégration : vérification de liveness, validation de documents délivrés par un gouvernement, confirmation de l’adresse ou du numéro de téléphone, etc.

* **Stockage des attributs de justificatifs vérifiables** : Dans la mesure du possible, ne stockez pas les attributs des justificatifs vérifiables dans le magasin spécifique à votre application. Soyez particulièrement vigilant avec les données personnelles. Si ces informations sont requises par des flux spécifiques au sein de vos applications, envisagez de demander aux justificatifs vérifiables de récupérer les revendications à la demande. 

* **Corrélation entre les attributs de justificatifs vérifiables et les systèmes principaux** : Lorsque vous définissez les attributs des justificatifs vérifiables avec l’émetteur, établissez un mécanisme de mise en corrélation des informations dans le système principal une fois que l’utilisateur a présenté les justificatifs vérifiables. Ce mécanisme utilise généralement un identificateur unique, limité dans le temps, dans le contexte de votre partie de confiance en association avec les revendications que vous recevez. Exemples :

   * **Nouvel employé** : Lorsque le flux de travail des RH atteint le point où la preuve d’identité est requise, la partie de confiance peut générer un lien avec un identificateur unique limité dans le temps et l’envoyer à l’adresse e-mail du candidat dans le système des RH. Cet identificateur unique doit être suffisant pour mettre en corrélation des informations telles que firstName et lastName provenant de la demande de vérification des justificatifs vérifiables avec l’enregistrement des RH ou les données sous-jacentes. Les attributs des justificatifs vérifiables peuvent être utilisés pour compléter les attributs utilisateur dans le système RH ou pour valider l’exactitude des attributs utilisateur concernant l’employé.

   * **Identités externes** (invitation) : Lorsqu’un utilisateur existant de votre organisation invite un utilisateur externe à être intégré au système cible, la partie de confiance peut générer un lien avec un identificateur unique qui représente la transaction d’invitation et l’envoie à l’adresse e-mail de l’utilisateur externe. Cet identificateur unique devrait être suffisant pour corréler la demande de vérification des justificatifs vérifiables avec l’enregistrement de l’invitation ou les données sous-jacentes et poursuivre le flux de travail de configuration. Les attributs des justificatifs vérifiables peuvent être utilisés pour valider ou compléter ceux de l’utilisateur externe.

   * **Identités externes** (libre-service) : Lorsque des identités externes s’inscrivent au système cible en libre-service (par exemple, une application B2C), les attributs des justificatifs vérifiables peuvent être utilisés pour renseigner les attributs initiaux du compte d’utilisateur. Les attributs des justificatifs vérifiables peuvent également être utilisés pour déterminer si un profil existe déjà.

* **Interaction avec les systèmes d’identité cibles** : La communication de service à service entre le serveur web frontal et vos systèmes d’identité cibles doit être sécurisée comme un système à privilèges élevés, car elle peut créer des comptes. Accordez au serveur web frontal des rôles avec le moins de privilèges possibles. Voici quelques exemples :

   * Pour créer un nouvel utilisateur dans Azure AD, le site web de la partie de confiance peut utiliser un principal de service auquel est accordée l’étendue MS Graph « User.ReadWrite.All » pour créer des utilisateurs, et l’étendue « UserAuthenticationMethod.ReadWrite.All » pour réinitialiser la méthode d’authentification.

   * Pour inviter des utilisateurs à Azure AD à l’aide de la collaboration B2B, le site web de la partie de confiance peut utiliser un principal de service auquel est accordée l’étendue MS Graph « User.Invite.All » pour créer des invitations.

   * Si votre partie de confiance s’exécute dans Azure, utilisez des identités managées pour appeler Microsoft Graph ; cela éliminera les risques liés à la gestion des informations d’identification du principal de service dans le code ou les fichiers config. Pour plus d’informations sur les identités managées, consultez [Identités managées pour les ressources Azure](../managed-identities-azure-resources/overview.md).

### <a name="accessing-high-value-applications-inside-organizations"></a>Accès aux applications à haute valeur ajoutée au sein des organisations 

Les justificatifs vérifiables peuvent servir de preuve supplémentaire pour accéder à des applications sensibles au sein de l’organisation. Par exemple, les justificatifs vérifiables peuvent également être utilisés pour permettre aux employés d’accéder aux applications métier sur la base de critères spécifiques, tels qu’une certification.

![Accès à l’intérieur de la limite de confiance](media/plan-verification-solution/inside-trust-boundary-access.png)

#### <a name="additional-elements"></a>Éléments supplémentaires 

**Serveur web frontal de la partie de confiance** : Il s’agit du serveur web frontal de l’application qui est amélioré par le biais d’appels au Kit de développement logiciel (SDK) ou à l’API Justificatifs vérifiables Azure AD pour la présentation et la validation des justificatifs vérifiables, en fonction de vos exigences métier.

**Logique d’autorisation de l’accès utilisateur** : Couche logique de l’application qui autorise l’accès utilisateur et qui est améliorée pour consommer les attributs utilisateur dans les justificatifs vérifiables afin de prendre des décisions en matière d’autorisation. 

**Autres services principaux et dépendances** : Représente le reste de la logique de l’application, qui est généralement laissé inchangé par l’inclusion de la preuve de l’identité via les justificatifs vérifiables.

#### <a name="design-considerations"></a>Remarques sur la conception

* **Objectif** : L’objectif du scénario détermine le type d’informations d’identification et d’émetteur requis. Voici quelques exemples de scénarios courants :

   * **Autorisation** : Dans ce scénario, l’utilisateur présente les justificatifs vérifiables pour prendre une décision en matière d’autorisation. Les justificatifs vérifiables destinés à prouver l’achèvement d’une formation ou la détention d’une certification spécifique conviennent bien à ce scénario. Les attributs des justificatifs vérifiables doivent contenir des informations précises permettant de prendre des décisions en matière d’autorisation et de procéder à des audits. Par exemple, si les justificatifs vérifiables sont utilisés pour certifier que la personne est formée et peut accéder à des applications financières sensibles, la logique de l’application peut vérifier la revendication du service pour obtenir une autorisation de granularité fine et utiliser l’ID d’employé à des fins d’audit. 

   * **Confirmation de la vérification de l’identité** : Dans ce scénario, l’objectif est de confirmer que la personne qui a été intégrée au départ est bien celle qui tente d’accéder à l’application à forte valeur ajoutée. Des informations d’identification provenant d’un émetteur de vérification d’identité seraient un bon choix et la logique de l’application devrait valider que les attributs des justificatifs vérifiables correspondent à l’utilisateur qui s’est connecté à l’application. 

* **Vérification de la révocation** : Lors de l’utilisation de justificatifs vérifiables pour accéder à des ressources sensibles, il est courant de vérifier l’état des justificatifs vérifiables auprès de l’émetteur d’origine et de refuser l’accès aux justificatifs vérifiables révoqués. Lorsque vous travaillez avec les émetteurs, assurez-vous que la révocation est explicitement discutée dans le cadre de la conception de votre scénario. 

* **Expérience de l’utilisateur** : Lorsque vous utilisez des justificatifs vérifiables pour accéder à des ressources sensibles, vous pouvez envisager deux modèles. 

   * **Authentification par étapes** : Les utilisateurs démarrent la session avec l’application à l’aide des mécanismes d’authentification existants. Les utilisateurs doivent présenter des justificatifs vérifiable pour des opérations spécifiques à forte valeur ajoutée au sein de l’application, telles que les approbations de flux de travail d’entreprise. Cette solution convient bien aux scénarios où ces opérations à forte valeur ajoutée sont faciles à identifier et à mettre à jour dans les flux d’application.

   * **Établissement de la session** : Les utilisateurs doivent présenter des justificatifs vérifiables lors de l’ouverture de la session avec l’application. Cette solution convient bien lorsque la nature de l’ensemble de l’application est à forte valeur ajoutée. 

### <a name="accessing-applications-outside-organization-boundaries"></a>Accès aux applications en dehors des limites de l’organisation

Les justificatifs vérifiables peuvent également être utilisés par les parties de confiance qui souhaitent accorder un accès ou des avantages en fonction de l’appartenance ou de la relation de travail d’une autre organisation. Par exemple, un portail d’e-commerce peut offrir des avantages tels que des réductions aux employés d’une entreprise particulière, aux élèves d’une institution donnée, etc. 

La nature décentralisée des justificatifs vérifiables permet ce scénario sans établir de relations de fédération. 

![Accès en dehors de la limite de confiance](media/plan-verification-solution/outside-trust-boundary-access.png)

#### <a name="additional-elements"></a>Éléments supplémentaires 

**Serveur web frontal de la partie de confiance** : Il s’agit du serveur web frontal de l’application qui est amélioré par le biais d’appels au Kit de développement logiciel (SDK) ou à l’API Justificatifs vérifiables Azure AD pour la présentation et la validation des justificatifs vérifiables, en fonction de vos exigences métier.

**Logique d’autorisation de l’accès utilisateur** : Couche logique de l’application qui autorise l’accès utilisateur et qui est améliorée pour consommer les attributs utilisateur dans les justificatifs vérifiables afin de prendre des décisions en matière d’autorisation.

**Autres services principaux et dépendances** : Représente le reste de la logique de l’application, qui est généralement laissé inchangé par l’inclusion de la preuve de l’identité via les justificatifs vérifiables.

#### <a name="design-considerations"></a>Remarques sur la conception

* **Objectif** : L’objectif du scénario détermine le type d’informations d’identification et d’émetteur requis. Voici quelques exemples de scénarios courants :

   * **Authentification** : Dans ce scénario, un utilisateur doit être en possession de justificatifs vérifiables pour prouver son emploi ou sa relation avec une ou plusieurs organisations particulières. Dans ce cas, la partie de confiance doit être configurée pour accepter les justificatifs vérifiables émis par les organisations cibles. 

   * **Autorisation** : En fonction des exigences relatives à l’application, les applications peuvent utiliser les justificatifs vérifiables pour prendre des décisions en matière d’autorisation et effectuer des audits de granularité fine. Par exemple, si un site web d’e-commerce offre des réductions aux employés des organisations situées dans une localisation particulière, il peut valider cette condition sur la base de la revendication relative au pays dans les justificatifs vérifiables (le cas échéant).

* **Vérification de la révocation** : Lors de l’utilisation de justificatifs vérifiables pour accéder à des ressources sensibles, il est courant de vérifier l’état des justificatifs vérifiables auprès de l’émetteur d’origine et de refuser l’accès aux justificatifs vérifiables révoqués. Lorsque vous travaillez avec les émetteurs, assurez-vous que la révocation est explicitement discutée dans le cadre de la conception de votre scénario. 

* **Expérience de l’utilisateur** : Les utilisateurs peuvent présenter des justificatifs vérifiables lors de l’ouverture de la session avec l’application. En général, les applications proposent également une autre méthode pour démarrer la session afin de tenir compte des cas où les utilisateurs n’ont pas de justificatifs vérifiables. 


### <a name="account-recovery"></a>Récupération de compte

Les justificatifs vérifiables peuvent être utilisés comme une approche de la récupération de compte. Par exemple, lorsqu’un utilisateur a besoin de récupérer son compte, il peut accéder à un site web qui lui demande de présenter des justificatifs vérifiables et de lancer une réinitialisation des informations d’identification Azure AD en appelant les API MS Graph, comme le montre le schéma suivant.

Remarque : Si le scénario que nous décrivons dans cette section est spécifique à la récupération des comptes Azure AD, cette approche peut également être utilisée pour récupérer des comptes dans d’autres systèmes.

![Solution de récupération de comptes](media/plan-verification-solution/account-recovery.png)

#### <a name="additional-elements"></a>Éléments supplémentaires

**Portail des comptes** : Il s’agit d’un serveur web frontal qui orchestre les appels d’API ou du Kit de développement logiciel (SDK) pour la présentation et la validation des justificatifs vérifiables. Cette orchestration peut inclure des appels Microsoft Graph pour récupérer des comptes dans Azure AD.

**Logique ou flux de travail personnalisés** : Logique avec des étapes propres à l’organisation avant et après la mise à jour du compte d’utilisateur. Cela peut inclure des flux de travail d’approbation, des validations supplémentaires, la journalisation, les notifications, etc.

**Microsoft Graph** : Expose des API REST (representational state transfer) et des bibliothèques de client pour accéder aux données Azure AD qui sont utilisées pour effectuer la récupération des comptes. 

**Répertoire d’entreprise Azure AD** : Il s’agit du locataire Azure AD qui contient les comptes qui sont créés ou mis à jour via le portail des comptes.

#### <a name="design-considerations"></a>Remarques relatives à la conception

**Corrélation entre les attributs des justificatifs vérifiables et Azure AD** : Lors de la définition des attributs des justificatifs vérifiables en collaboration avec l’émetteur, établissez un mécanisme permettant de mettre en corrélation les informations avec les systèmes internes en fonction des revendications des justificatifs vérifiables et des entrées utilisateur. Par exemple, si vous faites appel à un fournisseur de vérification d’identité (IDV) pour vérifier l’identité avant d’intégrer des employés, assurez-vous que les justificatifs vérifiables émis comprennent des revendications qui seraient également présentes dans un système interne tel qu’un système de ressources humaines pour la corrélation. Il peut s’agir d’un numéro de téléphone, d’une adresse ou d’une date de naissance. En plus des revendications contenus dans les justificatifs vérifiables, la partie de confiance peut demander certaines informations comme les quatre derniers chiffres du numéro de sécurité sociale (USA) dans le cadre de ce processus.

**Rôle des justificatifs vérifiables avec les capacités existantes de réinitialisation des informations d’identification d’Azure AD** : Azure AD dispose d’une capacité intégrée de réinitialisation de mot de passe en libre-service (SSPR). Les justificatifs vérifiables peuvent être utilisés pour fournir un moyen supplémentaire de récupération, notamment dans les cas où les utilisateurs n’ont pas accès à la méthode SSPR ou en ont perdu le contrôle, par exemple s’ils ont perdu à la fois leur ordinateur et leur appareil mobile. Dans ce scénario, l’utilisateur peut obtenir à nouveau des justificatifs vérifiables auprès d’un émetteur de preuve d’identité et les présenter pour récupérer son compte. 

De même, vous pouvez utiliser des justificatifs vérifiables pour générer un passe d’accès temporaire qui permettra aux utilisateurs de réinitialiser leurs méthodes d’authentification multifacteur sans mot de passe. 

**Autorisation** : Créez un mécanisme d’autorisation tel qu’un groupe de sécurité que la partie de confiance vérifie avant de procéder à la récupération des informations d’identification. Par exemple, seuls les utilisateurs de groupes spécifiques sont qualifiés pour récupérer un compte avec des justificatifs vérifiables.

**Interaction avec Azure AD** : La communication service à service entre le serveur frontal web et Azure AD doit être sécurisée en tant que système à privilèges élevés, car elle peut réinitialiser les informations d’identification des employés. Accordez au serveur web frontal des rôles avec le moins de privilèges possibles. Voici quelques exemples :

* Accordez au site web de la partie de confiance la possibilité d’utiliser un principal de service auquel est accordée l’étendue MS Graph « UserAuthenticationMethod.ReadWrite.All » pour réinitialiser les méthodes d’authentification. N’accordez pas l’étendue « User.ReadWrite.All », qui permet de créer et de supprimer des utilisateurs.

* Si votre partie de confiance s’exécute dans Azure, utilisez des identités managées pour appeler Microsoft Graph. Cela élimine les risques liés à la gestion des informations d’identification du principal de service dans le code ou les fichiers config. Pour plus d’informations, consultez [Identités managées pour les ressources Azure](../managed-identities-azure-resources/overview.md).

## <a name="plan-for-identity-management"></a>Plan de gestion des identités 

Voici quelques considérations IAM à prendre en compte lors de l’incorporation de justificatifs vérifiables aux parties de confiance. Les parties de confiance sont généralement des applications. 

### <a name="authentication"></a>Authentication

* Le sujet de justificatifs vérifiables doit être un être humain. 

* La présentation de justificatifs vérifiables doit être effectuée de manière interactive par un détenteur humain de justificatifs vérifiables, qui détient le justificatif dans son portefeuille. Les flux non interactifs tels que les flux OBO (On-Behalf-Of) ne sont pas pris en charge. 

### <a name="authorization"></a>Autorisation 

* Une présentation réussie des justificatifs vérifiables peut être considérée comme une porte d’autorisation grossière. Les attributs des justificatifs vérifiables peuvent également être utilisés pour des décisions affinées en matière d’autorisation.

* Déterminez si des justificatifs vérifiables expirés ont une signification dans votre application ; si c’est le cas, vérifiez la valeur de la revendication « exp » (heure d’expiration) des justificatifs vérifiables dans le cadre des contrôles d’autorisation. Un exemple où l’expiration n’est pas pertinente est la demande d’un document délivré par le gouvernement, comme un permis de conduire, pour valider si le sujet a plus de 18 ans. La revendication de la date de naissance est valide, même si les justificatifs vérifiables sont expirés. 

* Déterminez si des justificatifs vérifiables révoqués ont une signification pour votre décision d’autorisation. 

   * Si ce n’est pas le cas, ignorez l’appel à l’API de vérification de l’état (qui est activé par défaut). 

   * Si cela s’avère approprié, ajoutez le traitement approprié des exceptions dans votre application.

### <a name="user-profiles"></a>Profils utilisateur

Vous pouvez utiliser les informations contenues dans les justificatifs vérifiables présentés pour établir un profil utilisateur. Si vous souhaitez utiliser des attributs pour créer un profil, prenez en compte les éléments suivants.

* Lorsque les justificatifs vérifiables sont émis, ils contiennent un instantané des attributs au moment de l’émission. Les justificatifs vérifiables peuvent avoir de longues périodes de validité, et vous devez déterminer l’âge des attributs que vous accepterez comme suffisamment récents pour être utilisés dans le cadre du profil. 

* Si des justificatifs vérifiables doivent être présentés chaque fois que le sujet démarre une session avec la partie de confiance, envisagez d’utiliser la sortie de la présentation des justificatifs pour créer un profil utilisateur non persistant avec les attributs. Cela permet de réduire les risques pour la confidentialité associés au stockage des propriétés de l’utilisateur au repos. Si les attributs du sujet doivent être conservés localement par l’application, ne stockez que l’ensemble minimal de revendications requis par votre application (au lieu de stocker l’intégralité du contenu des justificatifs vérifiables).

* Si l’application nécessite un stockage persistant du profil utilisateur :

   * Envisagez d’utiliser la revendication « sub » comme identificateur immuable de l’utilisateur. Il s’agit d’un attribut unique opaque qui sera constant pour une paire sujet-partie de confiance donnée. 

   * Définissez un mécanisme pour supprimer les privilèges d’accès du profil utilisateur de l’application. En raison de la nature décentralisée du système de justificatifs vérifiables Azure AD, il n’existe aucun cycle de vie de l’attribution d’utilisateurs de l’application.

   * Ne stockez pas les revendications de données personnelles renvoyées dans le jeton de justificatifs vérifiables.

   * Stockez uniquement les revendications nécessaires à la logique de la partie de confiance. 

## <a name="plan-for-performance"></a>Planifier les performances

Comme pour n’importe quelle solution, vous devez planifier les performances. Les domaines à privilégier sont la latence, le débit, le stockage et la scalabilité. Pendant les phases initiales d’un cycle de mise en production, les performances ne doivent pas être un problème. Toutefois, lorsque l’adoption de votre solution d’émission entraîne l’émission de nombreux justificatifs vérifiables, la planification des performances peut devenir un élément essentiel de votre solution.

Vous trouverez ci-dessous les points à prendre en compte lors de la planification des performances :

* Le service d’émission de justificatifs vérifiables Azure AD est déployé dans les régions Europe Ouest, Europe Nord, USA Ouest 2 et USA Centre-Ouest. Pour limiter la latence, déployez votre serveur frontal de vérification (site web) et votre coffre de clés dans la région indiquée ci-dessus la plus proche de celle d’où les demandes sont censées provenir.

* Modèle basé sur le débit :

   * La capacité de vérification des justificatifs vérifiables est soumise aux [limites du service Azure Key Vault](../../key-vault/general/service-limits.md). 

   * Chaque vérification de justificatifs vérifiables nécessite une opération de signature Key Vault.

   * Les performances maximales de signature d’une instance Key Vault sont de 2000 signatures/environ 10 secondes. Cela signifie que votre solution peut prendre en charge jusqu’à 12 000 demandes de validation de justificatifs vérifiables par minute.

   * Vous ne pouvez pas contrôler la limitation ; cependant, nous vous recommandons de lire [Aide sur la limitation de requêtes Azure Key Vault](../../key-vault/general/overview-throttling.md) afin de comprendre l’impact de la limitation sur les performances. 

## <a name="plan-for-reliability"></a>Planifier pour la fiabilité

Pour planifier au mieux la haute disponibilité et la récupération d’urgence, nous vous suggérons ce qui suit :

* Le service de justificatifs vérifiables Azure AD est déployé dans les régions Europe Ouest, Europe Nord, USA Ouest 2 et USA Centre-Ouest. Envisagez de déployer vos serveurs web et vos applications connexes dans l’une de ces régions, en particulier celles à partir desquelles vous vous attendez à ce que la plupart de votre trafic de validation provienne. 

* Passez en revue et intégrez les meilleures pratiques décrites dans [Disponibilité et redondance d’Azure Key Vault](../../key-vault/general/disaster-recovery-guidance.md) lors de la conception de vos objectifs de disponibilité et de redondance.

## <a name="plan-for-security"></a>Planifier la sécurité

Lors de la conception de la sécurité, tenez compte des points suivants :

* Toutes les parties de confiance d’un même locataire ont la même limite de confiance puisqu’elles partagent le même DID.

* Définissez un principal de service dédié pour un site web accédant au coffre de clés.

* Seuls le service Justificatifs vérifiables Azure AD et les principaux de service du site web doivent avoir l’autorisation d’utiliser Key Vault pour signer des messages avec la clé privée. 

* N’attribuez pas d’autorisations d’administration d’identité humaine au coffre de clés. Pour plus d’informations sur les meilleures pratiques de Key Vault, consultez [Ligne de base de sécurité Azure pour Key Vault](../../key-vault/general/security-baseline.md).

* Consultez [Sécurisation des environnements Azure avec Azure Active Directory](https://azure.microsoft.com/resources/securing-azure-environments-with-azure-active-directory/) pour connaître les meilleures pratiques de gestion des services connexes de votre solution. 

* Atténuez les risques d’usurpation d’identité en :

   * implémentant une vérification DNS pour aider les clients à identifier la personnalisation de l’émetteur ;

   * utilisant des domaines significatifs pour les utilisateurs finaux.

* Atténuez les risques de limitation de ressources Key Vault et de déni de service distribué (DDOS). Chaque demande de présentation de justificatifs vérifiables génère des opérations de signature Key Vault qui s’ajoutent aux limites de service. Nous vous recommandons de protéger le trafic en incorporant une autre authentification ou un captcha avant de générer des requêtes d’émission.

## <a name="plan-for-operations"></a>Planifier les opérations

Lorsque vous planifiez des opérations, nous vous recommandons de capturer chaque tentative de validation des informations d’identification dans le cadre de votre audit. Utilisez ces informations pour l’audit et la résolution des problèmes. En outre, envisagez de générer des identificateurs de transaction uniques (ID) auxquels les clients et les ingénieurs su support peuvent se référer en cas de besoin. 

Dans le cadre de votre planification opérationnelle, pensez à surveiller les éléments suivants :

* **Pour la scalabilité** :

   * Surveillez les échecs de validation des justificatifs vérifiables dans le cadre des métriques de sécurité de bout en bout des applications.

   * Surveillez la latence de bout en bout de la vérification des informations d’identification.

* **Pour la fiabilité et les dépendances** :

   * Surveillez les dépendances sous-jacentes utilisées par la solution de vérification. 

   * Suivez [Surveillance et alertes pour Azure Key Vault](../../key-vault/general/alert.md).

* **Pour la sécurité** :

   * Activez la journalisation pour Key Vault afin de suivre les opérations de signature, ainsi que pour surveiller les changements de configuration et déclencher des alertes à leur sujet. Référez-vous à [Comment activer la journalisation Key Vault](../../key-vault/general/howto-logging.md) pour plus d’informations.

   * Archivez des journaux dans un système de gestion des informations et des événements de sécurité (SIEM), comme [Microsoft Sentinel](https://azure.microsoft.com/services/azure-sentinel/) pour une conservation à long terme.

 

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur l’architecture de solutions justificatives vérifiables

   * [Présentation de Justificatifs vérifiables Azure AD](introduction-to-verifiable-credentials-architecture.md)

   * [Planifier votre solution d’émission de justificatifs vérifiables Azure AD](plan-issuance-solution.md)

Implémenter des justificatifs vérifiables

   * [Présentation des justificatifs vérifiables Azure Active Directory](decentralized-identifier-overview.md) 

   * [Prise en main des justificatifs vérifiables](get-started-verifiable-credentials.md)

[Questions fréquentes (FAQ)](verifiable-credentials-faq.md)
