---
title: Planifier votre solution d’émission de justificatifs vérifiables Azure Active Directory (préversion)
description: Apprenez à planifier votre solution d’émission de bout en bout.
documentationCenter: ''
author: barbaraselden
manager: martinco
ms.service: active-directory
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 07/20/2021
ms.author: baselden
ms.custom: references_regions
ms.openlocfilehash: fdf6ef5910e31dc21929cf484b2b1ad580badef7
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319717"
---
# <a name="plan-your-azure-active-directory-verifiable-credentials-issuance-solution-preview"></a>Planifier votre solution d’émission de justificatifs vérifiables Azure Active Directory (préversion)

 >[!IMPORTANT]
> Les justificatifs vérifiables Azure Active Directory sont actuellement en préversion publique. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [**Conditions supplémentaires relatives à l’utilisation des préversions de Microsoft Azure**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Il est important de planifier votre solution d’émission afin qu’en plus d’émettre des informations d’identification, vous disposiez d’une vue complète de l’impact sur l’architecture et l’activité de votre solution. Si vous ne l’avez pas encore fait, nous vous recommandons de consulter la [présentation de l’architecture de justificatifs vérifiables Azure Active Directory](introduction-to-verifiable-credentials-architecture.md) pour obtenir des informations de base.

## <a name="scope-of-guidance"></a>Étendue des recommandations

Cet article aborde les aspects techniques de la planification d’une solution d’émission de justificatifs vérifiables utilisant des produits Microsoft pour interagir avec le réseau d’identités (Identity Overlay Network - ION). La solution Microsoft pour les justificatifs d’identité vérifiables suit les normes [Verifiable Credentials Data Model 1.0](https://www.w3.org/TR/vc-data-model/) et [Decentralized Identifiers (DIDs) V1.0](https://www.w3.org/TR/did-core/) du World Wide Web Consortium (W3C), de sorte à être interopérable avec des services non Microsoft. Toutefois, les exemples de ce contenu reflètent la pile de solutions Microsoft pour les justificatifs vérifiables. 

Les rubriques relatives à la prise en charge des technologies ne sont pas spécifiques aux solutions d’émission. Par exemple, des sites web sont utilisés dans une solution d’émission de justificatifs vérifiables, mais la planification d’un déploiement de site web n’est pas traitée en détail.

## <a name="components-of-the-solution"></a>Composants de la solution

Dans le cadre de votre plan pour une solution d’émission, vous devez concevoir une solution qui permet les interactions entre l’émetteur, l’utilisateur et le vérificateur. Vous pouvez émettre plus d’un justificatif vérifiable. Le diagramme suivant montre les composants de votre architecture d’émission.

### <a name="microsoft-vc-issuance-solution-architecture"></a>Architecture de la solution d’émission de justificatifs vérifiables Microsoft

![Composants d’une solution d’émission](media/plan-issuance-solution/plan-issuance-solution-architecture.png)


### <a name="azure-active-directory-tenant"></a>Locataire Azure Active Directory

Une condition préalable à l’exécution du service de justificatifs vérifiables Azure AD est qu’il est hébergée dans un locataire Azure Active Directory (Azure AD). Le locataire Azure AD fournit un plan de contrôle de gestion des identités et des accès (IAM) pour les ressources Azure qui font partie de la solution.

Chaque locataire possède une seule instance du service de justificatifs vérifiables Azure AD et un seul identificateur décentralisé (DID). Le DID fournit une preuve que l’émetteur possède le domaine incorporé dans le DID. Le DID est utilisé par le sujet et le vérificateur pour valider l’émetteur. 

### <a name="microsoft-azure-services"></a>Services Microsoft Azure

![Composants d’une solution d’émission, en se concentrant sur les services Azure](media/plan-issuance-solution/plan-issuance-solution-azure-services.png)

Le service **Azure Key Vault** stocke vos clés d’émetteur, qui sont générées lorsque vous lancez le service d’émission de justificatifs vérifiables Azure AD. Les clés et métadonnées sont utilisées pour exécuter des opérations de gestion des informations d’identification et assurer la sécurité des messages.

Chaque émetteur possède un ensemble de clés unique utilisé pour la signature, la mise à jour et la récupération. Ce jeu de clés est utilisé pour chaque émission de tous les justificatifs vérifiables que vous produisez. 

**Stockage Azure** est utilisé pour stocker les métadonnées et les définitions des justificatifs d’identité ; plus précisément, les règles et les fichiers d’affichage de vos justificatifs d’identité. 

* Les fichiers d’affichage déterminent les revendications qui sont stockées dans le justificatif vérifiable et comment elles s’affichent dans le portefeuille du titulaire. Le fichier d’affichage comprend également la personnalisation et d’autres éléments. La taille des fichiers de règles est limitée à 50 ko, tandis que les fichiers d’affichage sont limités à 150 ko. Consultez [Comment personnaliser vos justificatifs vérifiables](../verifiable-credentials/credential-design.md).

* Les règles sont un modèle défini par l’émetteur qui décrit les entrées requises des justificatifs vérifiables, les sources approuvées des entrées et le mappage des revendications d’entrée aux revendications de sortie. 

   * **Entrée** - Un sous-ensemble du modèle dans le fichier de règles pour la consommation du client. Le sous-ensemble doit décrire le jeu d’entrées, où obtenir les entrées et le point de terminaison à appeler pour obtenir des justificatifs vérifiables.

* Des règles et fichiers d’affichage pour différentes informations d’identification peuvent être configurés pour utiliser des conteneurs, des abonnements et un stockage différents. Par exemple, vous pouvez déléguer des autorisations à différentes équipes qui possèdent la gestion de justificatifs vérifiables spécifiques. 

### <a name="azure-ad-verifiable-credentials-service"></a>Service d’informations d’identification vérifiables Azure AD

![Service de justificatifs vérifiables Microsoft Azure AD](media/plan-issuance-solution/plan-issuance-azure-active-directory-verifiable-credential-services.png)

Le service de justificatifs vérifiables Azure AD vous permet d’émettre et de révoquer des justificatifs vérifiables en fonction de votre configuration. Le service :

* Approvisionne l’identificateur décentralisé (DID) et écrit le document DID dans ION, où il peut être utilisé par des sujets et vérificateurs. Chaque émetteur a un seul DID par locataire. 

* Approvisionne les jeux de clés pour Key Vault. 

* Stocke les métadonnées de configuration utilisées par le service d’émission et Microsoft Authenticator.

### <a name="ion"></a>ION

![ION](media/plan-issuance-solution/plan-issuance-solution-ion.png)

Microsoft utilise le [Identity Overlay Network (ION)](https://identity.foundation/ion/), [un réseau basé sur Sidetree](https://identity.foundation/sidetree/spec/) qui utilise la blockchain de Bitcoin pour la mise en œuvre d’un identifiant décentralisé (DID). Le document DID de l’émetteur est stocké dans ION et est utilisé pour effectuer des vérifications de signature de chiffrement par les tiers à la transaction.

### <a name="microsoft-authenticator-application"></a>Application Microsoft Authenticator

![Application Microsoft Authenticator](media/plan-issuance-solution/plan-issuance-solution-authenticator.png)

Microsoft Authenticator est l’application mobile qui orchestre les interactions entre l’utilisateur, le service de justificatifs vérifiables Azure AD et les dépendances décrites dans le contrat utilisé pour émettre les justificatifs vérifiables. Il agit comme un portefeuille numérique dans lequel le détenteur des justificatifs vérifiables stocke les justificatifs vérifiables, y compris la clé privée de l’objet des justificatifs vérifiables. Authenticator est également le mécanisme utilisé pour présenter les justificatifs vérifiables à des fins de vérification.

### <a name="issuance-business-logic"></a>Logique métier d’émission 

![Logique métier d’émission](media/plan-issuance-solution/plan-issuance-solution-business-logic.png)

Votre solution d’émission comprend un serveur web frontal dans lequel les utilisateurs demandent des justificatifs vérifiables, un magasin d’identités et ou un autre magasin d’attributs pour obtenir des valeurs pour les revendications relatives au sujet et d’autres services principaux. 

Un serveur web frontal sert les requêtes d’émission au portefeuille du sujet en générant des liens profonds ou des codes QR. En fonction de la configuration du contrat, d’autres composants peuvent être requis pour répondre aux conditions requises pour créer un circuit virtuel.

Ces services fournissent des rôles de soutien qui n’ont pas nécessairement besoin d’être intégrés à ION ou au service d’émission de justificatifs vérifiables Azure AD. Cette couche comprend généralement les éléments suivants :

* **Un ou des services conformes Open ID Connect (OIDC)** sont utilisés pour obtenir les id_tokens nécessaire pour émettre les justificatifs vérifiables. Les systèmes d’identité existants comme Azure AD ou Azure AD B2C peuvent fournir le service conforme à OIDC, tout comme les solutions personnalisées comme le serveur d’identités.

* **Magasins d’attributs** - Ils peuvent se trouver en dehors des services d’annuaire et fournir les attributs nécessaires pour émettre des justificatifs vérifiables. Par exemple, un système d’informations sur les étudiants peut fournir des revendications sur les diplômes obtenus. 

* **Des services de niveau intermédiaire supplémentaires** qui contiennent des règles d’entreprise pour les recherches, la validation, la facturation et tout autre contrôle et workflow nécessaire à l’émission d’informations d’identification.

Pour plus d’informations sur la configuration de votre frontal web, consultez le tutoriel [Configurer Azure AD pour émettre des justificatifs vérifiables](../verifiable-credentials/enable-your-tenant-verifiable-credentials.md). 

## <a name="credential-design-considerations"></a>Considérations relatives à la conception des informations d’identification

Vos cas d’utilisation spécifiques déterminent la conception de vos informations d’identification. Le cas d’usage détermine :

* Les exigences relatives à l’interopérabilité

* La façon dont les utilisateurs doivent prouver leur identité pour accéder à leurs justificatifs vérifiables

* Les revendications nécessaires dans les informations d’identification

* Si les informations d’identification doivent être révoquées

 

### <a name="credential-use-cases"></a>Cas d’utilisation des justificatifs

Avec les justificatifs vérifiables Azure AD, les cas d’utilisation d’informations d’identification les plus courants sont les suivants :

**Vérification de l’identité** : Des informations d’identification sont émises en fonction de plusieurs critères. Cela peut inclure la vérification de l’authenticité des documents émis par le gouvernement, comme un passeport ou un permis de conduire, et la corrélation des informations contenues dans ce document avec d’autres informations comme :

* Un selfie de l’utilisateur 

* Une vérification de l’activité

Ce type d’informations d’identification est adapté aux scénarios d’intégration d’identité de nouveaux employés, partenaires, fournisseurs de services, étudiants et autres instances où la vérification d’identité est essentielle.

 

![Cas d’utilisation : vérification d’identité](media/plan-issuance-solution/plan-issuance-solution-identity-verification-use-cases.png)

**Preuve d’emploi/appartenance** : Des informations d’identification sont émises pour prouver une relation entre l’utilisateur et une institution. Ce type d’informations d’identification est un bon choix pour accéder aux applications interentreprises faiblement couplées, comme les détaillants proposant des remises aux employés ou étudiants. L’une des principales valeurs des justificatifs vérifiables est leur portabilité : une fois émis, l’utilisateur peut utiliser les justificatifs vérifiables dans de nombreux scénarios. 

![Cas d’usage de preuve d’emploi](media/plan-issuance-solution/plan-issuance-solution-employment-proof-use-cases.png)

Pour plus de cas d’utilisation, consultez [Cas d’utilisation des justificatifs vérifiables (w3.org)](https://www.w3.org/TR/vc-use-cases/).

### <a name="credential-interoperability"></a>Interopérabilité des informations d’identification

Dans le cadre du processus de conception, recherchez des schémas, des espaces de noms et des identificateurs spécifiques au secteur que vous pouvez aligner pour optimiser l’interopérabilité et l’utilisation. Vous trouverez des exemples sur [Schema.org](https://schema.org/) et [DIF - Claims and Credentials Working Group](https://identity.foundation/working-groups/claims-credentials.html).

Notez que les schémas courants sont un domaine dont les normes restent émergentes. Un exemple d’un tel effort est [Verifiable Credentials for Education Task Force](https://github.com/w3c-ccg/vc-ed). Nous vous encourageons à étudier et à contribuer aux normes émergentes dans le secteur de votre organisation.

### <a name="credential-attributes"></a>Attributs des informations d’identification 

Après avoir établi le cas d’usage pour les informations d’identification, vous devez déterminer les attributs à inclure dedans. Les vérificateurs peuvent lire les revendications dans les justificatifs vérifiables présentés par les utilisateurs.

Outre les normes et schémas spécifiques au secteur qui peuvent s’appliquer à vos scénarios, tenez compte des aspects suivants :

* **Minimiser les informations privées** : Répondez aux cas d’usage avec le minimum d’informations privées nécessaires. Par exemple, des justificatifs vérifiables utilisés pour des sites web d’e-commerce qui offrent des remises aux employés et étudiants peuvent être remplis en présentant les justificatifs avec uniquement les revendications de prénom et nom de famille. Des informations supplémentaires comme la date d’embauche, le titre, le service, etc., ne sont pas nécessaires.

* **Privilégier les revendications abstraites** : Chaque revendication doit répondre au besoin tout en minimisant les détails. Par exemple, une revendication nommée « ageOver » avec des valeurs discrètes comme « 13 », « 21 », « 60 » est plus abstraite qu’une revendication de date de naissance.

* **Planifier la révocabilité** : Nous vous recommandons de définir une revendication d’index pour permettre aux mécanismes de trouver et de révoquer les informations d’identification. Vous êtes limité à la définition d’une revendication d’index par contrat. Il est important de noter que les valeurs des revendications indexées ne sont pas stockées dans le serveur principal : seul le hachage de la valeur de la revendication l’est. Pour plus d’informations, consultez [Révoquer un des justificatifs vérifiables précédemment émis](../verifiable-credentials/how-to-issuer-revoke.md).

Pour des considérations supplémentaires sur les attributs des justificatifs, consultez la spécification [Verifiable Credentials Data Model 1.0 (w3.org)](https://www.w3.org/TR/vc-data-model/).

## <a name="plan-quality-attributes"></a>Attributs de qualité du plan

### <a name="plan-for-performance"></a>Planifier les performances

Comme pour n’importe quelle solution, vous devez planifier les performances. Les points clés à prendre en compte sont la latence, le stockage, le débit et l’évolutivité. Pendant les phases initiales d’un cycle de mise en production, les performances ne doivent pas être un problème. Toutefois, lorsque l’adoption de votre solution d’émission entraîne l’émission de nombreux justificatifs vérifiables, la planification des performances peut devenir un élément essentiel de votre solution.

Vous trouverez ci-dessous les points à prendre en compte lors de la planification des performances :

* Le service d’émission de justificatifs vérifiables Azure AD est déployé dans les régions Europe Ouest, Europe Nord, USA Ouest 2 et USA Centre-Ouest. Vous ne sélectionnez pas une région dans laquelle déployer le service. 

* Pour limiter la latence, déployez votre site web frontal d’émission, le coffre de clés et le stockage dans la région répertoriée ci-dessus, qui est la plus proche de l’emplacement d’où les requêtes sont supposées provenir.

Modèle basé sur le débit :
* Le service Émetteur est soumis aux [limites du service Azure Key Vault](../../key-vault/general/service-limits.md). 

*  Pour Azure Key Vault, il existe trois opérations de signature impliquées dans chaque émission de justificatifs vérifiables :

      * Une pour la requête d’émission à partir du site web

      * Une pour les justificatifs vérifiables créés

      * Une pour le téléchargement du contrat

* Les performances maximales de signature d’une instance Key Vault sont de 2 000 signatures/environ 10 secondes. Cela représente environ 12 000 connexions par minute. Cela signifie que votre solution peut prendre en charge jusqu’à 4 000 émissions de justificatifs vérifiables par minute.

* Vous ne pouvez pas contrôler la limitation. Cependant, nous vous recommandons de lire nos [Conseils sur la limitation d’Azure Key Vault](../../key-vault/general/overview-throttling.md). 

* Si vous planifiez un déploiement et une intégration de justificatifs vérifiables à grande échelle, envisagez de créer un traitement par lot pour vous assurer que vous ne dépassez pas les limites.

* Le service d’émission est soumis aux limites de stockage d’Azure. Dans les cas d’utilisation classiques, le stockage ne devrait pas être un problème. Toutefois, si vous pensez que vous risquez de dépasser les limites de stockage ou que le stockage peut être un goulot d’étranglement, passez en revue les éléments suivants : 

   * Nous vous recommandons de lire [Objectifs d’extensibilité et de performances du Stockage Blob](../../storage/blobs/scalability-targets.md) dans le cadre de votre processus de planification. Le service d’émission de justificatifs vérifiables Azure AD lit les règles et affiche les fichiers, et les résultats sont mis en cache par le service.

   * Nous vous conseillons également de passer en revue la [Liste de contrôle des performances et de la scalabilité pour le stockage Blob - Stockage Azure](../../storage/blobs/storage-performance-checklist.md). 

Dans le cadre de votre plan de performances, déterminez ce que vous allez surveiller pour mieux comprendre les performances de la solution. En plus de la surveillance des sites web au niveau de l’application, tenez compte des éléments suivants lorsque vous définissez votre stratégie d’analyse des émissions de justificatifs vérifiables :

Pour l’évolutivité, envisagez d’implémenter des métriques pour les éléments suivants :

   * Définissez les phases logiques de votre processus d’émission. Par exemple :

   * Demande initiale 

   * Remise du code QR ou lien profond

   * Recherche d’attribut

   * Appels au service d’émission de justificatifs vérifiables Azure AD

   * Justificatifs émis

   * Définissez les métriques en fonction des phases :

      * Nombre total de requêtes (volume)

      * Requêtes par unité de temps (débit)

      * Temps passé (latence)

* Surveillez Azure Key Vault et Stockage Azure à l’aide des éléments suivants :

   * [Surveillance et alertes pour Azure Key Vault](../../key-vault/general/alert.md)

   * [Supervision du Stockage Blob Azure](../../storage/blobs/monitor-blob-storage.md)

* Surveillez les composants utilisés pour votre couche de logique métier. 

### <a name="plan-for-reliability"></a>Planifier pour la fiabilité

Pour planifier la fiabilité, nous vous recommandons ceci :

* Une fois que vous avez défini vos objectifs de disponibilité et de redondance, utilisez les guides suivants pour comprendre comment atteindre vos objectifs :

   * [Disponibilité et redondance d’Azure Key Vault - Azure Key Vault](../../key-vault/general/disaster-recovery-guidance.md)

   * [Reprise d’activité après sinistre et basculement de compte de stockage - Stockage Azure](../../storage/common/storage-disaster-recovery-guidance.md)

* Pour les couches frontale et métier, votre solution peut se manifester de façons illimitées. Comme pour toute solution, pour les dépendances que vous identifiez, assurez-vous qu’elles sont résilientes et surveillées. 

Dans le cas rare où le service de justificatifs vérifiables Azure AD, Azure Key Vault ou Stockage Azure devient indisponible, la solution entière deviendra indisponible.

### <a name="plan-for-compliance"></a>Planifier pour la conformité

Votre organisation peut avoir des besoins spécifiques en matière de conformité en rapport avec votre secteur, le type de transactions ou le pays d’exploitation. 

**Résidence des données** : Le service d’émission de justificatifs vérifiables Azure AD est déployé dans un sous-ensemble de régions Azure. Le service est utilisé uniquement pour les fonctions de calcul. Nous ne stockons pas les valeurs des justificatifs vérifiables dans les systèmes Microsoft. Toutefois, dans le cadre du processus d’émission, les données personnelles sont envoyées et utilisées lors de l’émission des justificatifs vérifiables. L’utilisation du service de justificatifs vérifiables ne doit pas avoir d’incidence sur la résidence des données. Si, dans le cadre de la vérification d’identité, vous stockez des informations personnelles, celles-ci doivent être stockées d’une façon et dans une région répondant à vos exigences en matière de conformité. Pour obtenir des instructions relatives à Azure, visitez le site web du Centre de gestion de la confidentialité Microsoft. 

**Révoquer les informations d’identification** : Déterminez si votre organisation doit révoquer les informations d’identification. Par exemple, un administrateur peut avoir besoin de révoquer les informations d’identification lorsqu’un employé quitte l’entreprise. Ou bien, si des justificatifs sont émis pour un permis de conduire et que le détenteur est arrêté pour avoir fait quelque chose qui entraînerait la suspension de son permis, les justificatifs vérifiables devront peut-être être révoqués. Pour plus d’informations, consultez [Révoquer un des justificatifs vérifiables précédemment émis](how-to-issuer-revoke.md).

**Expiration des informations d’identification** : Déterminez si vous allez faire expirer les informations d’identification, et si oui, dans quelles circonstances. Par exemple, si vous émettez des justificatifs vérifiables comme preuve de permis, ce dernier peut expirer au bout de quelques années. Si vous émettez des justificatifs vérifiables comme vérification d’une association avec un utilisateur, vous souhaiterez peut-être l’envoyer annuellement pour vous assurer que les utilisateurs reviennent chaque année pour obtenir la version la plus récente des justificatifs vérifiables.

## <a name="plan-for-operations"></a>Planifier les opérations

Lors de la planification des opérations, il est essentiel de développer un schéma à utiliser pour la résolution des problèmes, la création de rapports et la distinction entre les différents clients que vous prenez en charge. En outre, si l’équipe d’exploitation est responsable de l’exécution de la révocation des justificatifs vérifiables, ce processus doit être défini. Chaque étape du processus doit être corrélée afin que vous puissiez déterminer les entrées de journal qui peuvent être associées à chaque requête d’émission unique. Pour l’audit, nous vous recommandons de capturer chaque tentative d’émission d’informations d’identification individuellement. En particulier :

* Générez des ID de transaction uniques auxquels les clients et ingénieurs du support peuvent faire référence en fonction des besoins.

* Élaborez un mécanisme permettant de corréler les journaux des transactions d’Azure Key Vault aux ID de transaction de la partie émission de la solution.

* Si vous êtes un service de vérification des identités émettant des justificatifs vérifiables pour le compte de plusieurs clients, surveillez et corrigez par ID de client ou de contrat pour la création de rapports et la facturation côté client.

* Si vous êtes un service de vérification des identités émettant des justificatifs vérifiables pour le compte de plusieurs clients, utilisez l’ID de client ou de contrat pour la création de rapports et la facturation, la surveillance et la correction côté client. 

## <a name="plan-for-security"></a>Planifier la sécurité

Dans le cadre de vos considérations de conception axées sur la sécurité, nous vous recommandons ce qui suit :

* Pour la gestion des clés :

   * Créez une instance Key Vault dédiée pour l’émission de justificatifs vérifiables. Limitez les autorisations Azure Key Vault au service d’émission de justificatifs vérifiables Azure AD et au principal de service du site web frontal d’émission. 

   * Traitez Azure Key Vault comme un système à privilèges élevés - Azure Key Vault émet des informations d’identification pour les clients. Nous vous recommandons de ne pas avoir d’autorisations permanentes sur le service Azure Key Vault. Les administrateurs ne doivent avoir que l’accès juste-à-temps à Key Vault. Pour plus de bonnes pratiques concernant l’utilisation d’Azure Key Vault, consultez [Ligne de base de sécurité Azure pour Key Vault](/security/benchmark/azure/baselines/key-vault-security-baseline).

* Pour le principal du service qui représente le site web frontal d’émission :

   * Définissez un principal de service dédié pour autoriser l’accès à Azure Key Vault. Si votre site web est sur Azure, nous vous recommandons d’utiliser une [Identité managée Azure](../managed-identities-azure-resources/overview.md). 

   * Traitez le principal de service qui représente le site web et l’utilisateur comme une limite d’approbation unique. Bien qu’il soit possible de créer plusieurs sites web, il n’y a qu’un seul jeu de clés pour la solution d’émission. 

Pour la journalisation et la surveillance de la sécurité, nous recommandons ce qui suit :

* Activez la journalisation et les alertes d’Azure Key Vault pour suivre les opérations d’émission d’informations d’identification, les tentatives d’extraction de clé, les modifications d’autorisation, et pour surveiller et envoyer une alerte pour les modifications de configuration. Plus d’informations peuvent être trouvées dans [Comment activer la journalisation Key Vault](../../key-vault/general/howto-logging.md). 

* Activez la journalisation de votre compte Stockage Azure pour surveiller et envoyer une alerte pour les modifications de configuration. Plus d’informations peuvent être trouvées dans [Supervision du service Stockage Blob Azure](../../storage/blobs/monitor-blob-storage.md).

* Archivez des journaux dans un système de gestion des informations et des événements de sécurité (SIEM), comme [Microsoft Sentinel](https://azure.microsoft.com/services/azure-sentinel) pour une conservation à long terme.

* Atténuez les risques d’usurpation d’identité à l’aide des éléments suivants

   * Vérification DNS pour aider les clients à identifier la personnalisation de l’émetteur.

   * Noms de domaine significatifs pour les utilisateurs finaux.

   * La personnalisation de confiance que l’utilisateur final reconnaît.

* Atténuez les risques d’épuisement de ressources Key Vault et de déni de service distribué (DDOS). Chaque requête qui déclenche une requête d’émission de justificatifs vérifiables génère des opérations de signature Key Vault qui s’accumulent sur les limites du service. Nous vous recommandons de protéger le trafic en incorporant l’authentification ou un captcha avant de générer des requêtes d’émission.

Pour obtenir des conseils sur la gestion de votre environnement Azure, nous vous recommandons de consulter [Benchmark de sécurité Azure](/security/benchmark/azure/) et [Sécuriser les environnements Azure avec Azure Active Directory](https://aka.ms/AzureADSecuredAzure). Ces guides fournissent les meilleures pratiques pour la gestion des ressources Azure sous-jacentes, y compris Azure Key Vault, Stockage Azure, les sites web et d’autres services et fonctionnalités liés à Azure.

## <a name="additional-considerations"></a>Considérations supplémentaires

Lorsque vous avez terminé votre preuve de concept, rassemblez toutes les informations et la documentation générées, puis envisagez de détruire la configuration de l’émetteur. Cela permet d’éviter l’émission de justificatifs vérifiables après l’expiration de votre période de preuve de concept. 

Pour plus d’informations sur la mise en œuvre et le fonctionnement de Key Vault, reportez-vous aux [Meilleures pratiques pour utiliser Key Vault](../../key-vault/general/best-practices.md). Pour plus d’informations sur la sécurisation des environnements Azure avec Active Directory, reportez-vous à [Sécurisation des environnements Azure avec Azure Active Directory](https://aka.ms/AzureADSecuredAzure). 

## <a name="next-steps"></a>Étapes suivantes

[Lire la présentation de l’architecture](introduction-to-verifiable-credentials-architecture.md)

[Planifier votre solution de vérification](plan-verification-solution.md)

[Prise en main des justificatifs vérifiables](get-started-verifiable-credentials.md)
