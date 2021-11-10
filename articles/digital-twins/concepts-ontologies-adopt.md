---
title: Adoption d’ontologies conformes aux standards du secteur
titleSuffix: Azure Digital Twins
description: En savoir plus sur les ontologies existantes d’un secteur, qui peuvent être adoptées pour Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 10/27/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 936df82dc8c7c5babcb3b1844f2d53f1396dbd8b
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500656"
---
# <a name="adopting-an-industry-ontology"></a>Adoption de l’ontologie d’un secteur

Cet article couvre différents ensembles d’ontologies standard qui peuvent être adoptés pour simplifier des solutions.

Dans la mesure où il est sans doute plus facile de commencer avec une ontologie Digital Twins Definition Language (DTDL) open source qu'à partir d'une page blanche, Microsoft s'associe à des experts de domaine pour publier des ontologies. Celles-ci représentent des conventions largement acceptées dans le secteur et prennent en charge différents cas d'usage de clients. 

Il en résulte un ensemble d'ontologies open source en langage DTDL qui s'inspirent des normes du secteur, s'appuient sur celles-ci ou les utilisent directement. Ces ontologies sont conçues pour répondre aux besoins des développeurs en aval. Elles ont le potentiel nécessaire pour être largement adoptées et étendues par le secteur.

À ce stade, Microsoft collabore avec des partenaires pour développer des ontologies pour les [bâtiments intelligents](#realestatecore-smart-building-ontology), les [villes intelligentes](#smart-cities-ontology)et les [grilles d’énergie](#energy-grid-ontology). Ces ontologies fournissent une base commune pour la modélisation basée sur des normes dans ces secteurs pour éviter le besoin de réinvention. 

Chaque ontologie porte sur un ensemble initial de modèles. Les auteurs de l’ontologie vous invitent à contribuer à l’extension de l’ensemble initial de cas d’usage et à l’amélioration des modèles existants. 

## <a name="realestatecore-smart-building-ontology"></a>Ontologie des bâtiments intelligents RealEstateCore

*Trouvez l’ontologie dans le référentiel suivant :* [Ontologie RealEstateCore basée sur le langage DTDL (Digital Twins Definition Language) pour les bâtiments intelligents](https://github.com/Azure/opendigitaltwins-building).

Microsoft a conclu un partenariat avec [RealEstateCore](https://www.realestatecore.io/) pour fournir cette ontologie DTDL open source pour le secteur immobilier. [RealEstateCore](https://www.realestatecore.io/) est un consortium suédois de propriétaires immobiliers, de fournisseurs de logiciels et d’institutions de recherche.

Cette ontologie des bâtiments intelligents fournit une base commune à la modélisation des bâtiments intelligents en fonction des standards du secteur (par exemple le  [schéma BRICK](https://brickschema.org/ontology/) ou la  [spécification Building Topology Ontology du W3C](https://w3c-lbd-cg.github.io/bot/index.html)) pour éviter de réinventer ce qui existe déjà. Un ensemble de bonnes pratiques accompagne cette ontologie pour qu’elle puisse être consommée et étendue correctement. 

Pour en savoir plus sur la structure et les conventions de modélisation de l’ontologie ainsi que sur la façon de l’utiliser, de l’étendre et d’y contribuer, accédez à son dépôt sur GitHub : [Azure/opendigitaltwins-building](https://github.com/Azure/opendigitaltwins-building). 

Vous pouvez également en savoir plus sur le partenariat avec RealEstateCore et sur les objectifs de cette initiative en consultant le billet de blog suivant ainsi que la vidéo qui l’accompagne : [RealEstateCore, une ontologie des bâtiments intelligents pour les jumeaux numériques, est désormais disponible](https://techcommunity.microsoft.com/t5/internet-of-things/realestatecore-a-smart-building-ontology-for-digital-twins-is/ba-p/1914794).

## <a name="smart-cities-ontology"></a>Ontologie des villes intelligentes

*Trouvez l’ontologie dans le référentiel suivant :* [Ontologie basée sur le langage DTDL (Digital Twins Definition Language) pour les villes intelligentes](https://github.com/Azure/opendigitaltwins-smartcities).

Microsoft a collaboré avec des [OASC (Open Agile Smart Cities)](https://oascities.org/) et [Sirus](https://sirus.be/) pour fournir une ontologie basée sur DTDL pour les villes intelligentes, en commençant par [ETSI CIM NGSI-LD](https://www.etsi.org/committee/cim). Outre ETSI NGSI-LD, nous avons également évalué Saref4City, CityGML et ISO, entre autres.

Pour en savoir plus sur l’ontologie, sur la façon de l’utiliser et sur la façon d’y contribuer, accédez à son dépôt sur GitHub : [Azure/opendigitaltwins-smartcities](https://github.com/Azure/opendigitaltwins-smartcities). 

Vous pouvez également obtenir plus d’informations sur les partenariats et l’approche pour les villes intelligentes dans le billet de blog suivant et la vidéo qui l’accompagne : [Ontologie des villes intelligentes pour Digital Twins](https://techcommunity.microsoft.com/t5/internet-of-things/smart-cities-ontology-for-digital-twins/ba-p/2166585).

## <a name="energy-grid-ontology"></a>Ontologie des réseaux énergétiques

*Cette ontologie est disponible dans le dépôt suivant :* [Ontologie DTDL (Digital Twins Definition Language) pour les réseaux énergétiques](https://github.com/Azure/opendigitaltwins-energygrid/).

Cette ontologie a été créée afin d'aider les fournisseurs de solutions à accélérer le développement de solutions Digital Twins pour les cas d'usage liés à l'énergie (surveillance des ressources réseau, analyse des défaillances et des impacts, simulation et maintenance prédictive). De plus, l’ontologie peut être utilisée pour permettre la transition numérique et la modernisation de la grille de l’énergie. Elle a été adaptée à partir du [Common Information Model (CIM)](https://cimug.ucaiug.org/), norme internationale pour la gestion des ressources des réseaux énergétiques, la modélisation des opérations du réseau électrique et le marché des produits énergétiques physiques.

Pour en savoir plus sur cette ontologie, sur la façon de l’utiliser et la manière d’y contribuer, accédez à son dépôt sur GitHub : [Azure/opendigitaltwins-energygrid](https://github.com/Azure/opendigitaltwins-energygrid/). 

Vous pouvez également en savoir plus sur les partenariats et l’approche pour les réseaux énergétiques dans le billet de blog suivant : [Ontologie des réseaux énergétiques pour Digital Twins](https://techcommunity.microsoft.com/t5/internet-of-things/energy-grid-ontology-for-digital-twins-is-now-available/ba-p/2325134).

## <a name="next-steps"></a>Étapes suivantes

* Découvrez plus en détail comment étendre les ontologies conformes aux normes du secteur pour répondre aux impératifs de vos spécifications : [Extension des ontologies du secteur](concepts-ontologies-extend.md).

* Sinon, passez au développement de modèles à partir d’ontologies : [Utilisation de stratégies d’ontologie pour le développement de modèles](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).