---
title: Catégories web du pare-feu Azure
description: En savoir plus sur les catégories web du pare-feu Azure et leurs descriptions.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.date: 07/19/2021
ms.author: victorh
ms.openlocfilehash: c8ca827495f9ccfdd2786ee3b2bbf33e939c6a16
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131068624"
---
# <a name="azure-firewall-web-categories"></a>Catégories web du pare-feu Azure

Les catégories web permettent aux administrateurs d’autoriser ou de refuser aux utilisateurs l’accès aux catégories de sites web telles que les sites web de jeux d’argent, les sites web de réseaux sociaux, etc. Les catégories sont organisées en fonction de leur gravité sous Responsabilité, Bande passante élevée, Utilisation métier, Perte de productivité, Navigation générale et Sans catégorie.

## <a name="liability"></a>Dettes


|Category |Description |
|---------|---------|
|Alcool + tabac    |Sites contenant, promouvant ou vendant des produits ou services liés à l’alcool ou au tabac.|
|Images d’exploitation d’enfants   |Sites qui présentent ou discutent d’enfants dans des actes injurieux ou sexuels.|
|Inapproprié pour les enfants    |Sites qui ne conviennent pas pour les enfants, qui peuvent contenir du contenu adulte ou de mauvais goût, ou de la vulgarité.|
|Activité criminelle|Sites qui promeuvent ou conseillent de valider une activité illicite ou criminelle, ou d’éviter la détection d’une telle activité. L’activité criminelle comprend les meurtres, les bombes, les manipulations illégales d’appareils électroniques, le piratage, les fraudes et la distribution illégale des logiciels.|
|Rencontres    |Sites qui favorisent la création de relations, par exemple rendez-vous amoureux ou mariage, comme les sites de rencontres et matrimoniaux en ligne.|
|Jeux d’argent    |Sites qui offrent ou sont associés à des jeux en ligne, des loteries, des agences de paris impliquant la chance ou à des casinos.|
|Piratage    |Sites qui promeuvent ou conseillent d’obtenir un accès non autorisé à des systèmes informatiques propriétaires, voler des informations, engager des fraudes, créer des virus ou valider d’autres activités illégales liées au vol ou à l’information numérique.|
|Haine + intolérance|Sites qui favorisent un agenda politique suprémaciste, encourageant l’oppression de personnes ou de groupes de personnes sur la base de leur race, religion, sexe, âge, invalidité, orientation sexuelle ou nationalité.|
|Drogue illégale    |Sites contenant des informations sur l’achat, la fabrication et l’utilisation de drogues illicites ou récréatives, ainsi que l’utilisation abusive de médicaments sur prescription et d’autres composés.|
|Logiciel illégal    |Sites qui distribuent illégalement des logiciels ou documents protégés par des droits d’auteur, comme des films, de la musique, des logiciels piratés, des numéros de série illicites ou encore des générateurs de clés de licence.|
|Lingerie + maillots de bain|Sites qui offrent des images de mannequins dans des tenues suggestives, avec une semi-nudité autorisée. Comprend les sites offrant de la lingerie ou des vêtements de bain.|
|Marijuana   |Sites qui contiennent des informations, des discussions ou la vente de marijuana et produits ou services associés, y compris la légalisation de la marijuana et/ou l’utilisation de marijuana à des fins médicales.|
|Nudité    | Sites qui contiennent de la nudité complète ou partielle sans nécessairement avoir de caractère sexuel.|
|Pornographie/Caractère sexuel    |Sites qui contiennent du contenu à caractère sexuel. Comprend des produits adultes tels que les sex toys, des CD-ROM, des vidéos, des services adultes comme des vidéoconférences, des services d’escort, des clubs de striptease, des histoires érotiques et des descriptions textuelles d’actes sexuels.  |
|Tricherie scolaire    | Sites qui favorisent des pratiques non éthiques telles que la triche ou le plagiat en fournissant des réponses aux tests, essais écrits, documents de recherche ou travaux scolaires.        |
|Automutilation      |Sites qui encouragent les actions liées à l’atteinte de soi, comme le suicide, l’anorexie, la boulimie, etc.          |
|Éducation sexuelle     |  Sites relatifs à l’éducation sexuelle, y compris des sujets tels que le respect des partenaires, l’avortement, les contraceptifs, les maladies sexuellement transmissibles et la grossesse.       |
|De mauvais goût     |Sites dont le contenu est offensif ou de mauvais goût, y compris la vulgarité.          |
|Violence   | Sites contenant des images ou du texte qui décrivent ou préconisent une agression physique contre des êtres humains, des animaux ou des établissements. Sites de nature macabre.         |
|Armes      |Sites qui décrivent, vendent, évaluent ou décrivent des armes, y compris pour le sport.         |


## <a name="high-bandwidth"></a>Bande passante élevée

|Category  |Description  |
|---------|---------|
|Partage d’images    |   Sites hébergeant des photographies et images numériques, des albums de photos en ligne et des échanges de photos numériques.       |
|Pair à pair    |  Sites qui permettent l’échange direct de fichiers entre utilisateurs sans dépendre d’un serveur central.        |
|Streaming + téléchargements    |  Sites qui fournissent du contenu en streaming, comme une radio Internet, une TV Internet ou des fichiers MP3, ainsi que les sites de téléchargement de fichiers multimédias en direct ou archivés. Comprend les sites de fans et sites officiels appartenant à des musiciens, groupes ou labels.        |
|Sites de téléchargement      | Sites contenant des logiciels téléchargeables, qu’il s’agisse de partagiciels, de gratuiciels ou de paiement au téléchargement.         |
|Divertissement     |  Sites proposant des grilles de programmes pour la télévision, les films, la musique et les vidéos (y compris à la demande), les sites de célébrités et les actualités de divertissement.        |
|    |         |

## <a name="business-use"></a>Utilisation professionnelle

|Category  |Description  |
|---------|---------|
|Entreprises    |   Sites qui fournissent des informations commerciales, comme les sites web d’entreprise. Informations, services ou produits qui aident les entreprises de toutes tailles à effectuer leurs activités commerciales quotidiennes.       |
|Ordinateurs + technologie   |Sites contenant des informations comme des évaluations de produits, des discussions et des actualités sur les ordinateurs, les logiciels, le matériel, les périphériques et les services informatiques.          |
|Education   |  Sites sponsorisés par des établissements scolaires et des écoles de tous types, y compris l’enseignement à distance. Comprend des documents éducatifs et de référence généraux, comme des dictionnaires, encyclopédies, cours en ligne, formations pédagogiques et guides de discussion.       |
|Finance    | Sites liés aux banques, à la finance, aux paiements ou à l’investissement, y compris les banques, courtiers, opérations de bourse en ligne, cotations boursières, systèmes de gestion des fonds, compagnies d’assurances, caisses de crédit, compagnies de carte de crédit, etc.         |
|Forums + groupes de discussion     | Sites de partage d’informations sous la forme de groupes de discussion, forums et bulletins d’informations. N’inclut pas les blogs personnels.         |
|Administration      |  Sites administrés par des organismes, agences ou services gouvernementaux ou militaires, y compris les services de police, les services d’incendie, les bureaux de douane, les services d’urgence, la défense civile et les organisations antiterroristes.        |
|Santé + médecine      |   Sites contenant des informations relatives à la santé, aux services de santé, à la remise en forme et au bien-être, y compris les informations sur les équipements médicaux, la médecine, les hôpitaux, les pharmacies, les soins, les médicaments, les procédures, etc.       |
|Sécurité des informations     |   Sites qui fournissent des informations légitimes sur la protection des données, y compris les vulnérabilités récemment découvertes et comment les bloquer.       |
|Recherche d’emploi    |  Sites contenant des offres d’emploi, des informations de carrière, de l’aide pour la recherche d’emploi (comme l’écriture de CV, les conseils pour les entretiens, etc.), des agences de travail ou des recruteurs.        |
|Actualités  |    Sites couvrant l’actualité et les événements actuels tels que les journaux, les services de publication, les services d’actualité personnalisés, les sites de diffusion et les magazines.      |
|Associations + ONG     |   Sites consacrés aux clubs, communautés, syndicats et organisations à but non lucratif. Un grand nombre de ces groupes existent à des fins éducatives ou charitables.       |
|Sites personnels     |    Sites sur ou hébergés par des individus, y compris ceux hébergés sur des sites commerciaux tels que Blogger, AOL, etc.      |
|Adresses IP privées      |  Des sites qui sont des adresses IP privées, telles que définies dans la norme RFC 1918, c’est-à-dire des hôtes qui ne nécessitent pas d’accès à des hôtes dans d’autres entreprises (ou qui nécessitent un accès limité) et dont l’adresse IP peut être ambiguë entre entreprises mais bien définies au sein d’une entreprise donnée.        |
|Réseau professionnel   | Sites qui encouragent le réseautage professionnel pour les communautés en ligne.   |
|Moteurs de recherche + portails   |Sites permettant d’effectuer des recherches sur le web, les groupes de discussion, les images, les annuaires et tout autre contenu en ligne. Comprend les sites portails et annuaires, comme les pages blanches/jaunes.    |
|Traducteurs     |   Sites qui traduisent des pages web ou des expressions d’une langue à une autre. Ces sites contournent le serveur proxy, présentant le risque d’accès à du contenu non autorisé, similaire à l’utilisation d’un dispositif d’anonymat.       |
|Dépôt de fichiers     |  Pages web, y compris les collections de partagiciels, gratuiciels, logiciels open source et autres téléchargements de logiciels.        |
|Messagerie web     | Sites permettant aux utilisateurs d’envoyer et de recevoir des e-mails via un compte de messagerie accessible sur le web.         |
|     |         |


## <a name="productivity-loss"></a>Perte de productivité

|Category   |Description  |
|---------|---------|
|Annonces + fenêtres publicitaires    |  Sites qui fournissent des graphiques publicitaires ou autres fichiers de contenu publicitaire qui s’affichent sur des pages web.       |
|Conversation    |   Sites qui permettent l’échange sur le web de messages en temps réel par le biais de services de conversation ou de salles de conversation.       |
|Sectes   |   Les sites se rapportant à une pratique religieuse non traditionnelle, généralement appelée « secte », qui est considérée comme fausse, non orthodoxe, extrémiste ou coercitive, les membres vivant souvent sous la direction d’un leader charismatique.       |
|Jeux    |    Sites relatifs aux jeux pour ordinateurs ou autres, informations sur les producteurs de jeux ou comment obtenir des codes de triche. Sites de publication liés aux jeux.      |
|Messagerie instantanée    | Sites qui permettent de se connecter à des services de messagerie instantanée tels qu’ICQ, AOL Instant Messenger, IRC, MSN, Jabber, Yahoo Messenger et autres.         |
|Achats    |  Sites pour les achats en ligne, les catalogues, les commandes en ligne, les enchères et les petites annonces. Exclut les achats de produits et services exclusivement couverts par une autre catégorie, comme la santé et la médecine.       |
|Réseaux sociaux   |  Sites qui servent de réseaux sociaux pour des communautés en ligne portant sur divers sujets, l’amitié ou les rencontres.        |
|   |         |

## <a name="general-surfing"></a>Navigation générale

|Category   |Description  |
|---------|---------|
|Arts     | Sites avec du contenu artistique ou associés à des institutions artistiques comme des théâtres, des musées, des galeries, des sociétés de danse, des studios de photographie et des ressources graphiques numériques.   |
|Mode + beauté    | Sites relatifs à la mode, aux bijoux, au glamour, à la beauté, aux mannequins, à la cosmétique ou aux produits ou services associés. Comprend des évaluations de produits, des comparaisons et des informations générales pour les consommateurs.   |
|Général    | Les sites qui ne se classent pas clairement dans les autres catégories, par exemple les pages web vides.   |
|Cartes de vœux    |Sites permettant aux utilisateurs d’envoyer et de recevoir des cartes de vœux et des cartes postales.    |
|Loisirs + détente     | Sites liés aux activités récréatives et aux loisirs, y compris les zoos, les centres de loisirs publics, les piscines, les parcs d’attractions et les passe-temps tels que le jardinage, la littérature, les arts et l’artisanat, le bricolage, la décoration d’intérieur, la famille, etc. |
|Nature + protection     | Sites avec des informations relatives aux problèmes environnementaux, aux styles de vie durable, à l’écologie, à la nature et à l’environnement.        |
|Politique     | Sites qui promeuvent les partis politiques ou des idées politiques, ou qui fournissent des informations sur les partis politiques, les groupes d’intérêt, les élections, la législation ou le lobbying. Comprend également les sites qui proposent des informations et des conseils juridiques.       |
|Immobilier  |Sites relatifs aux services immobiliers commerciaux ou résidentiels, y compris la location, l’achat, la vente ou le financement de maisons, de bureaux, etc.    |
|Religion    | Sites qui traitent de la foi, de la spiritualité humaine ou des convictions religieuses, y compris les sites d’églises, synagogues, mosquées et autres lieux de culte.         |
|Restauration  |Sites qui répertorient, évaluent, promeuvent ou publient des services alimentaires, de repas ou de restauration. Comprend les sites de recettes, d’instructions et conseils de cuisine, de produits alimentaires et d’experts en vins.          |
|Sports   | Sites relatifs aux équipes sportives, aux fan clubs, aux résultats et aux actualités sportives. Se rapporte à tous les sports, qu’ils soient professionnels ou récréatifs.        |
|Transport   | Sites qui incluent des informations sur des véhicules, comme les voitures, motos, bateaux, camions, camping-cars et similaires, y compris les sites d’achat en ligne. Comprend les sites des constructeurs, des concessionnaires, de cote, de tarification, de passionnés et des transports publics, etc.    |
|Voyage    | Sites fournissant des informations sur le voyage et le tourisme, la réservation en ligne ou des services de voyage, comme les compagnies aériennes, les logements ou les locations de voitures. Comprend les sites d’informations sur une région ou une ville.         |
|Sans catégorie     |Les sites qui n’ont pas été catégorisés, comme les nouveaux sites web, les sites personnels et ainsi de suite.          |
|    |         |

## <a name="next-steps"></a>Étapes suivantes
- [Démarrage rapide : Créer un pare-feu Azure et une stratégie de pare-feu - Modèle ARM](../firewall-manager/quick-firewall-policy.md)
- [Démarrage rapide : Déployer un pare-feu Azure avec Zones de disponibilité - Modèle ARM](deploy-template.md)
- [Tutoriel : Déployer et configurer un pare-feu Azure à l’aide du portail Azure](tutorial-firewall-deploy-portal.md)

