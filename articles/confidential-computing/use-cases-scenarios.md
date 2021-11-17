---
title: Scénarios et cas d’usage courants de l’informatique confidentielle Azure
description: Découvrez comment utiliser l’informatique confidentielle dans votre scénario.
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.topic: overview
ms.date: 11/04/2021
ms.author: jencook
ms.custom: ignite-fall-2021
ms.openlocfilehash: 408f619f34942839bedcc75c6ab66044b4f90323
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852702"
---
# <a name="use-cases-and-scenarios"></a>Cas d'utilisation et scénarios
L’informatique confidentielle s’applique à différents cas d’utilisation pour la protection des données dans les secteurs réglementés, tels que les administrations, les services financiers et les établissements de santé. Par exemple, la prévention de l’accès aux données sensibles contribue à protéger l’identité numérique des citoyens de toutes les parties concernées, y compris le fournisseur de Cloud qui les stocke. Les mêmes données sensibles peuvent contenir des données biométriques utilisées pour rechercher et supprimer des images connues de l’exploitation enfant, pour empêcher le trafic humain et pour assister des investigations d’investigation numérique.

:::image type="content" source="media/use-cases-scenarios/use-cases.png" alt-text="Capture d’écran des cas d’usage de l’informatique confidentielle Azure, notamment dans le secteur public, les services financiers et la santé.":::

Cet article présente plusieurs scénarios courants pour l’informatique confidentielle Azure. Les recommandations de cet article constituent le point de départ du développement de votre application à l’aide de services et de frameworks d’informatique confidentielle.

Après avoir lu cet article, vous serez en mesure de répondre aux questions suivantes :

- Quels sont les scénarios pour l’informatique confidentielle Azure ?
- Quels sont les avantages de l'utilisation de l'informatique confidentielle Azure pour les scénarios multipartites, la confidentialité accrue des données des clients et les réseaux blockchain ?

## <a name="motivations"></a>Motivations
L’informatique confidentielle Azure vous permet de tirer parti des avantages de l’informatique confidentielle dans un environnement virtualisé. Vous pouvez désormais utiliser des outils, des logiciels et une infrastructure cloud pour créer des applications sur du matériel sécurisé.  

**Empêcher les accès non autorisés** : Traitez des données sensibles dans le cloud. N’ayez aucun doute sur le fait qu’Azure fournit la meilleure protection possible des données, avec peu ou pas de modifications par rapport à ce qui se fait aujourd’hui.

**Conformité réglementaire** : Migrez vers le cloud et gardez le contrôle total des données de façon à satisfaire aux réglementations légales relatives à la protection des informations personnelles et à la sécurité des adresses IP de l’organisation.

**Collaboration sécurisée et non fiable** : Attaquez-vous aux problèmes des travaux à l’échelle du secteur en examinant les données entre différentes organisations, même concurrentes, pour permettre des analyses des données de grande envergure et obtenir des insights plus approfondis.

**Traitement isolé** : Offrez une nouvelle vague de produits qui suppriment la responsabilité légale sur les données privées avec un traitement à l’aveugle. Les données utilisateur ne peuvent même pas être récupérées par le fournisseur de services. 

## <a name="secure-multi-party-computation"></a>Sécuriser le traitement multipartite

Les transactions commerciales et la collaboration à des projets nécessitent le partage d'informations entre plusieurs parties. Souvent, les données partagées sont confidentielles. Les données peuvent être des informations personnelles, des dossiers financiers, des dossiers médicaux, des données de citoyen privé, etc. Les organisations publiques et privées requièrent que leurs données soient protégées contre tout accès non autorisé. Parfois, ces organisations veulent même protéger les données des opérateurs ou ingénieurs de l’infrastructure informatique, des architectes de sécurité, des conseillers commerciaux et des scientifiques des données.

Par exemple, l’utilisation de Machine Learning pour les services de santé a augmenté massivement, à mesure que nous avons obtenu l’accès à des jeux de données plus importants et à l’imagerie des patients capturés par des appareils médicaux. Le diagnostic des maladies et le développement de la drogue bénéficient de plusieurs sources de données. Les hôpitaux et les instituts d’hygiène peuvent collaborer en partageant leurs dossiers médicaux de patients avec un environnement d’exécution sécurisé (TEE) centralisé. Les services Machine Learning s’exécutant dans l’agrégat TEE et analysent les données. Cette analyse de données agrégée peut fournir une plus grande précision de prédiction en raison des modèles d’apprentissage sur les jeux de données consolidés. Avec l’informatique confidentielle, les hôpitaux peuvent réduire les risques de compromission de la confidentialité de leurs patients.

L’informatique confidentielle Azure vous permet de traiter des données issues de plusieurs sources sans exposer les données d’entrée à d’autres parties. Ce type de calcul sécurisé permet des scénarios tels que la lutte contre le blanchiment d'argent, la détection de la fraude et l'analyse sécurisée des données relatives aux soins de santé.

Les différentes sources peuvent charger leurs données dans une seule et même enclave sur une machine virtuelle. Une partie demande à l’enclave d’effectuer des opérations de calcul ou de traitement sur les données. Aucune partie (pas même celle qui exécute l’analyse) ne peut voir les données d’une autre partie qui ont été chargées dans l’enclave.

Dans un environnement informatique sécurisé à plusieurs tiers, les données chiffrées sont placées dans l’enclave. L’enclave déchiffre les données à l’aide d’une clé, effectue une analyse, obtient un résultat, puis renvoie un résultat chiffré qu’un tiers peut déchiffrer avec la clé désignée.

### <a name="anti-money-laundering"></a>Lutte contre le blanchiment d’argent

Dans cet exemple de traitement multipartite, plusieurs banques partagent des données entre elles sans exposer les données personnelles de leurs clients. Les banques lancent les analyses convenues sur le jeu de données sensibles combinées. L’analytique sur le jeu de données agrégées peut détecter les mouvements d’argent d’un utilisateur entre plusieurs banques, sans que celles-ci aient accès aux données des unes et des autres.

Grâce à l’informatique confidentielle, ces établissements financiers peuvent augmenter les taux de détection des fraudes, lutter contre les problèmes de blanchiment d’argent, réduire les faux positifs et poursuivre l’apprentissage à partir de jeux de données plus volumineux.

:::image type="content" source="media/use-cases-scenarios/mpc-banks.png" alt-text="Graphique du partage de données multipartites pour les banques montrant le déplacement des données rendue possible par l’informatique confidentielle.":::

### <a name="drug-development-in-healthcare"></a>Développement de médicaments dans le secteur de la santé

Les établissements de santé alimentent des jeux de données médicales privés pour participer en partenariat à l’entraînement d’un modèle Machine Learning. Chaque établissement a uniquement accès au jeu de données qui lui appartient. Aucun autre établissement ni même le fournisseur de cloud ne peut voir les données ou le modèle d’entraînement. Tous les établissements bénéficient du modèle entraîné. L’enrichissement du modèle avec plus de données a permis de rendre le modèle plus juste. Chaque établissement ayant contribué à l’entraînement du modèle peut utiliser le modèle et obtenir des résultats utiles.

![Diagramme des scénarios de santé confidentiels, montrant l’attestation entre les scénarios.](media/use-cases-scenarios/confidential_healthcare.png)

## <a name="enhanced-customer-data-privacy"></a>Amélioration de la confidentialité des données des clients

Bien que le niveau de sécurité fourni par Microsoft Azure devienne rapidement l'un des principaux moteurs de l'adoption de l'informatique en nuage, les clients font confiance à leur fournisseur à des degrés divers. Le client demande :

- Un matériel, des logiciels et des TCBs opérationnels minimaux (bases informatiques de confiance) pour les charges de travail sensibles.
- La mise en œuvre technique, plutôt que des stratégies d’entreprise et des processus.
- Transparence sur les garanties, les risques résiduels et les atténuations qu’ils obtiennent.

L'informatique confidentielle va dans ce sens en permettant aux clients de contrôler progressivement la BCT utilisée pour exécuter leurs charges de travail en nuage. Azure Confidential Computing permet aux clients de définir précisément tout le matériel et les logiciels qui ont accès à leurs charges de travail (données et code), et il fournit les mécanismes techniques permettant d’appliquer cette garantie de manière vérifiable. En bref, les clients conservent un contrôle total sur leurs secrets.

### <a name="data-sovereignty"></a>Souveraineté des données

Dans les administrations et les administrations publiques, Azure Confidential Computing est une solution permettant d’élever le niveau de confiance à la capacité à protéger la souveraineté des données dans le cloud public. En outre, grâce à l’adoption croissante des fonctionnalités informatiques confidentielles dans les services PaaS dans Azure, ce degré de confiance plus élevé peut être atteint avec un impact réduit sur la capacité d’innovation fournie par les services de cloud public. Cette combinaison de facteurs permet à Azure Confidential Computing de répondre très efficacement aux besoins de souveraineté et de transformation numérique des services gouvernementaux.

### <a name="reduced-chain-of-trust"></a>Chaîne d’approbation réduite

L’énorme investissement et l’innovation révolutionnaire de l’informatique confidentielle ont permis de supprimer le fournisseur de services Cloud de la chaîne d’approbation à un degré sans précédent. Azure Confidential Computing offre le niveau de souveraineté le plus élevé actuellement disponible sur le marché, ce qui permet aux clients et aux gouvernements de répondre à leurs besoins de souveraineté aujourd’hui tout en tirant parti de l’innovation demain.

L’informatique confidentielle peut augmenter le nombre de charges de travail éligibles pour le déploiement de cloud public. Cela peut entraîner une adoption rapide des services publics pour les migrations et les nouvelles charges de travail, l’amélioration rapide de la position de sécurité des clients et l’activation rapide de scénarios novateurs.

### <a name="byok-bring-your-own-key-scenarios"></a>Scénarios BYOK (Bring Your Own Key)

L'adoption de modules matériels sécurisés (HSM) permet le transfert sécurisé de clés et de certificats vers un stockage en nuage protégé - [Azure Key Vault Managed HSM](\..\key-vault\managed-hsm\overview.md) - sans permettre au fournisseur de services en nuage d'accéder à ces informations sensibles. Les secrets en cours de transfert n’existent jamais en dehors d’un HSM sous forme de texte en clair, permettant des scénarios de souveraineté des clés et certificats qui sont générés et gérés par le client, tout en utilisant un stockage sécurisé basé sur le Cloud.

## <a name="secure-blockchain"></a>Blockchain sécurisée

Un réseau blockchain est un réseau de nœuds décentralisé. Les nœuds sont exécutés et gérés par des opérateurs ou des validateurs dans le but de garantir l’intégrité et d’atteindre un consensus sur l’état du réseau. Les nœuds eux-mêmes sont des réplicas de registres et sont utilisés pour le suivi des transactions blockchain. Chaque nœud dispose d’une copie complète de l’historique des transactions, ce qui garantit l’intégrité et la disponibilité dans un réseau distribué.

Les technologies blockchain sollicitant l’informatique confidentielle peuvent se servir de la protection basée sur le matériel pour assurer la confidentialité des données et la sécurisation des calculs. Dans certains cas, le registre est entièrement chiffré pour protéger l’accès aux données. Parfois, la transaction elle-même peut se produire dans un module de calcul à l’intérieur de l’enclave au sein du nœud.
