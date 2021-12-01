---
title: Didacticiel-centres pour les services ass et Medicaid (CMS) Introduction-API Azure pour FHIR
description: Cette vue d’ensemble présente une série de didacticiels qui se rapportent au centre de l’interopérabilité entre les services de l’assurance maladie et des services Medicaid (CMS) et à la règle d’accès aux patients.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 11/29/2021
ms.openlocfilehash: 1390ed2316c4891a23684223644bb976ff230fbc
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133359661"
---
# <a name="centers-for-medicare-and-medicaid-services-cms-interoperability-and-patient-access-rule-introduction"></a>Présentation de l’interopérabilité et des règles d’accès des patients aux centres pour les services d’assurance maladie et Medicaid (CMS)

Dans cette série de didacticiels, nous aborderons une synthèse générale du Centre pour l’interopérabilité et la règle d’accès aux services Medicaid (CMS), ainsi que les exigences techniques décrites dans cette règle. Nous allons examiner les différents guides d’implémentation référencés pour cette règle. Nous fournirons également des informations sur la configuration de l’API Azure pour FHIR afin de prendre en charge ces guides d’implémentation.


## <a name="rule-overview"></a>Présentation de la règle

Le CMS a publié l' [interopérabilité et la règle d’accès aux patients](https://www.cms.gov/Regulations-and-Guidance/Guidance/Interoperability/index) le 1er mai 2020. Cette règle nécessite un workflow de données gratuit et sécurisé entre toutes les parties impliquées dans les soins des patients (patients, fournisseurs et payeurs) pour permettre aux patients d’accéder à leurs informations d’intégrité lorsqu’ils en ont besoin. L’interopérabilité a submergé le secteur de la santé depuis des dizaines d’années, entraînant des données en silo provoquant des résultats d’intégrité négatifs avec des coûts plus élevés et imprévisibles. CMS utilise son autorité pour réglementer l’avantage de l’assurance maladie (MA), le Medicaid, le programme d’assurance maladie des enfants (CHIP) et les émetteurs du plan de santé qualifié (QHP) sur les échanges (FFEs) facilités pour appliquer cette règle. 

En août 2020, CMS détaille la façon dont les organisations peuvent respecter le mandat. Pour vous assurer que les données peuvent être échangées en toute sécurité et de manière standardisée, CMS a identifié la version FHIR version 4 (R4) comme norme de base requise pour l’échange de données. 

Il existe trois éléments principaux à l’interopérabilité et à la décision d’accès aux patients :

* **API d’accès aux patients (le 1er juillet 2021)** : les contribuables dépendants du CMS (tels que définis ci-dessus) sont requis pour mettre en œuvre et gérer une API sécurisée et normalisée qui permet aux patients d’accéder facilement à leurs revendications et d’obtenir des informations, y compris le coût, ainsi qu’un sous-ensemble défini de leurs informations cliniques par le biais d'  

* **API du répertoire du fournisseur (le 1er juillet 2021)** : les conversions du CMS sont requises par cette partie de la règle pour rendre les informations de l’annuaire du fournisseur accessibles au public via une API basée sur des normes. Grâce à la mise à disposition de ces informations, les développeurs d’applications tierces pourront créer des services qui aident les patients à trouver des fournisseurs pour des besoins spécifiques en matière de soins et des médecins à trouver d’autres fournisseurs pour la coordination des soins.  

* **Exchange de données du payeur à payer (le 1er janvier 2022)** : des payeurs réglementés CMS sont requis pour échanger certaines données cliniques médicales à la demande du patient avec d’autres contribuables. Bien qu’il ne soit pas nécessaire de suivre un type de norme, il est recommandé d’appliquer FHIR pour échanger ces données. 

## <a name="key-fhir-concepts"></a>Concepts clés de FHIR

Comme indiqué ci-dessus, FHIR R4 est requis pour respecter ce mandat. En outre, plusieurs guides d’implémentation ont été développés pour fournir des conseils sur la règle. Les [guides d’implémentation](https://www.hl7.org/fhir/implementationguide.html) fournissent un contexte supplémentaire en plus de la spécification FHIR de base. Cela comprend la définition de paramètres de recherche supplémentaires, de profils, d’extensions, d’opérations, de jeux de valeurs et de systèmes de code.

L’API Azure pour FHIR offre les fonctionnalités suivantes pour vous aider à configurer votre base de données pour les différents guides d’implémentation :

* [Prise en charge des interactions RESTful](fhir-features-supported.md)
* [Stockage et validation des profils](validation-against-profiles.md)
* [Définition et indexation des paramètres de recherche personnalisés](how-to-do-custom-search.md)
* [Conversion des données](convert-data.md)

## <a name="patient-access-api-implementation-guides"></a>Guides d’implémentation des API d’accès aux patients

L’API d’accès aux patients décrit l’adhésion à quatre guides d’implémentation de FHIR :

* [Carin GI pour le bouton bleu®](http://hl7.org/fhir/us/carin-bb/STU1/index.html): les contribuables sont requis pour faire valoir des patients et les données sont disponibles conformément au Guide d’implémentation du bouton Carin GI pour Blue (C4BB GI). Le C4BB GI fournit un ensemble de ressources que les contribuables peuvent afficher aux consommateurs via une API FHIR et inclut les détails requis pour les données de revendication dans l’API d’interopérabilité et d’accès aux patients. Ce guide d’implémentation utilise la ressource ExplanationOfBenefit (EOB) comme ressource principale, en extrayant d’autres ressources à mesure qu’elles sont référencées.
* [HL7 FHIR Da Vinci PDex gi](http://hl7.org/fhir/us/davinci-pdex/STU1/index.html): le Guide d’implémentation du Exchange des données du payeur (PDex gi) est axé sur la garantie que les contribuables fournissent toutes les données cliniques médicales pertinentes pour répondre aux exigences de l’API d’accès aux patients. Cela utilise les profils de base US pour les ressources R4 et comprend (au minimum) des compteurs, des fournisseurs, des organisations, des emplacements, des dates de service, des diagnostics, des procédures et des observations. Bien que ces données soient disponibles au format FHIR, elles peuvent également provenir d’autres systèmes au format de données de revendications, de messages HL7 V2 et de documents C-CDA.
* [HL7 US Core GI](https://www.hl7.org/fhir/us/core/toc.html): le Guide de mise en œuvre du noyau HL7 US (Core GI) est l’épine dorsale de l’PDex GI décrite ci-dessus. Alors que le PDex GI limite certaines ressources encore plus loin que l’IG du Centre des États-Unis, de nombreuses ressources suivent simplement les normes de l’IG du cœur des États-Unis.

* [HL7 FHIR da Vinci-PDEX US médicament Formulas GI](http://hl7.org/fhir/us/Davinci-drug-formulary/index.html): la partie D les plans d’avantages médicaux doivent mettre à disposition les informations sur les formules par le biais de l’API du patient. Pour ce faire, ils utilisent le Guide d’implémentation PDex des formules médicamenteuses US (USDF GI). Le USDF GI définit une interface FHIR pour les informations de la formule de la drogue d’un assureur d’intégrité, qui est une liste de noms de marquage et de médicaments de prescription génériques qu’un assureur d’intégrité accepte de payer. Le cas d’usage principal est de permettre aux patients de savoir s’il existe un autre médicament disponible pour un autre médicament qui lui a été imposé et pour comparer les coûts des drogues.

## <a name="provider-directory-api-implementation-guide"></a>Guide d’implémentation de l’API du répertoire du fournisseur

L’API d’annuaire du fournisseur décrit le respect d’un guide d’implémentation :

* [HL7 da Vinci PDex plan réseau GI](http://build.fhir.org/ig/HL7/davinci-pdex-plan-net/): ce guide d’implémentation définit une interface FHIR pour les plans d’assurance d’un assureur d’intégrité, les réseaux associés et les organisations et fournisseurs qui participent à ces réseaux.

## <a name="touchstone"></a>Touchstone

Pour tester le respect des divers guides d’implémentation, [Touchstone](https://touchstone.aegis.net/touchstone/) est une ressource intéressante. Dans les prochains didacticiels, nous nous concentrerons sur la façon dont l’API Azure pour FHIR est configurée pour réussir différents tests Touchstone. Le site Touchstone contient de nombreuses précieuses documentation pour vous aider à être opérationnel.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez une connaissance élémentaire de la règle d’accès à l’interopérabilité et des patients, des guides d’implémentation et de l’outil de test disponible (Touchstone), nous allons vous guider dans la configuration de l’API Azure pour FHIR pour le bouton CARIN GI for Blue. 

>[!div class="nextstepaction"]
>[Guide d’implémentation de CARIN pour Blue Button](carin-implementation-guide-blue-button-tutorial.md)  