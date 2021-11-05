---
title: 'Bonnes pratiques : réponse aux questions'
description: Suivez ces bonnes pratiques pour améliorer votre projets et fournir des résultats plus pertinents aux utilisateurs finaux de votre application/bot conversationnel.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 552e3d014ffecf5f43977420c9aa8c6bb1b59080
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097748"
---
# <a name="question-answering-best-practices"></a>Bonnes pratiques pour répondre aux questions

Suivez ces bonnes pratiques pour améliorer votre base de connaissances et fournir des résultats plus pertinents aux utilisateurs finaux de l’application client ou du bot conversationnel.

## <a name="extraction"></a>Extraction

Le service de réponses aux questions améliore en permanence les algorithmes qui extraient des entités de réponses aux questions à partir du contenu, et étend la liste des formats HTML et des formats de fichiers pris en charge. En général, les pages FAQ doivent être autonomes et ne pas être combinées avec d’autres informations. Les manuels de produits doivent avoir des titres clairs et, si possible, une page d’index.

## <a name="creating-good-questions-and-answers"></a>Créer de bonnes questions et réponses

### <a name="good-questions"></a>Bonnes questions

Les meilleures questions sont simples. Identifiez le mot ou l’expression clé de chaque question, puis créez une question simple pour ce mot ou cette expression.

Ajoutez autant de questions alternatives que vous le souhaitez, en restant toutefois sur des modifications simples. Le fait d’ajouter des mots ou des formulations qui ne font pas partie de l’objectif principal de la question n’aide pas les réponses aux questions à trouver une correspondance.

### <a name="add-relevant-alternative-questions"></a>Ajouter d’autres questions appropriées

Votre utilisateur peut saisir des questions avec un style de texte de type conversation : `How do I add a toner cartridge to my printer?` ou une recherche par mot clé, comme `toner cartridge`. Le projet doit avoir inclure des questions présentant ces deux styles, afin de pouvoir renvoyer la réponse la plus appropriée. Si vous ne savez pas quels mots clés le client saisit, utilisez les données d’[Azure Monitor](../how-to/analytics.md) pour analyser les requêtes.

### <a name="good-answers"></a>Bonnes réponses

Les réponses les plus appropriées sont simples, mais pas trop. N’utilisez pas de réponses comme `yes` et `no`. Si votre réponse doit comporter des liens vers d’autres sources ou offrir une expérience enrichie avec des contenus multimédias et des liens, utilisez le balisage des métadonnées pour faire la distinction entre les réponses, puis envoyez la requête avec des balises de métadonnées dans la propriété `strictFilters` pour obtenir la bonne version de la réponse.

|Réponse|Invites de suivi|
|--|--|
|À l'aide du bouton d'alimentation situé sur le clavier, mettez le Surface Laptop hors tension.|* Combinaisons de touches pour la mise en veille, l’arrêt et le redémarrage.<br>* Comment démarrer à froid un Surface Laptop<br>* Comment changer le BIOS d’un Surface Laptop<br>* Différences entre la mise en veille, l’arrêt et le redémarrage|
|Le service client est disponible par téléphone, Skype et SMS 24 heures sur 24.|Informations de contact - Ventes.<br> * Adresses des bureaux et magasins et horaires des visites.<br> * Accessoires pour Surface Laptop.|

## <a name="chit-chat"></a>Échanges de conversation (chit-chat)

Ajoutez facilement des échanges à votre bot afin de rendre ses conversations plus naturelles et engageantes. Vous pouvez aisément ajouter des sources de données d’échanges de conversation pour des personnalités prédéfinies lors de la création de votre projet, et les modifier à tout moment. Découvrez comment [ajouter des échanges de conversation à votre base de connaissances](../How-To/chit-chat.md).

Les échanges sont pris en charge dans de [nombreuses langues](../how-to/chit-chat.md#language-support).

### <a name="choosing-a-personality"></a>Choix d’une personnalité

Les échanges de conversation sont pris en charge pour plusieurs personnalités prédéfinies :

|Personnalité |Fichier de données de réponses aux questions |
|---------|-----|
|Professional |[qna_chitchat_professional.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|Convivial |[qna_chitchat_friendly.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|Comique |[qna_chitchat_witty.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|Attentif |[qna_chitchat_caring.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|Enthousiaste |[qna_chitchat_enthusiastic.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

Les réponses vont du formel à l’informel, en passant par l’irrévérencieux. Vous devez sélectionner la personnalité qui correspond le plus au ton souhaité pour votre bot. Vous pouvez afficher les [jeux de données](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets), en choisir un qui sert de base pour votre bot, puis personnaliser les réponses.

### <a name="edit-bot-specific-questions"></a>Modifier les questions propres au bot

Il existe certaines questions propres au bot qui font partie du jeu de données d’échanges de conversation et qui ont été remplies avec des réponses génériques. Modifiez ces réponses pour qu’elles reflètent mieux les caractéristiques de votre bot.

Nous recommandons de rendre plus spécifiques les paires de questions-réponses du chit-chat qui suivent :

* qui êtes-vous ?
* que pouvez-vous faire ?
* Quel âge avez-vous ?
* Qui vous a créé ?
* Hello

### <a name="adding-custom-chit-chat-with-a-metadata-tag"></a>Ajout d’échanges de conversation personnalisés avec une balise de métadonnées

Si vous ajoutez vos propres paires de questions-réponses chit-chat, assurez-vous d’ajouter les métadonnées afin que ces réponses soient envoyées. La paire nom/valeur des métadonnées est `editorial:chitchat`.

## <a name="searching-for-answers"></a>Recherche de réponses

L’API REST de réponses aux questions utilise des questions et leur réponse pour rechercher les meilleures réponses à la requête d’un utilisateur.

### <a name="searching-questions-only-when-answer-is-not-relevant"></a>Recherche de questions uniquement lorsque la réponse n’est pas pertinente

Utilisez le paramètre [`RankerType=QuestionOnly`](#choosing-ranker-type) si vous ne voulez pas rechercher des réponses.

Voici un exemple : une base de connaissances est un catalogue d’acronymes sous la forme de questions, avec leur forme complète en tant que réponse. La valeur de la réponse ne facilite pas la recherche de la réponse appropriée.

## <a name="rankingscoring"></a>Classement/scoring

Veillez à tirer le meilleur parti des fonctionnalités de classement prises en charge. Ces fonctionnalités augmentent la probabilité de retourner une réponse pertinente à une requête utilisateur.

### <a name="choosing-a-threshold"></a>Choix d’un seuil

Le [score de confiance](confidence-score.md) par défaut utilisé comme seuil est de 0, mais vous pouvez [le changer](confidence-score.md#set-threshold) pour votre projet, en fonction de vos besoins. Chaque projet étant différente, vous devez procéder à des test et choisir le seuil qui convient le mieux à votre projet.

### <a name="choosing-ranker-type"></a>Sélection du type d’outil de classement

Par défaut, la réponse aux questions permet d'effectuer des recherches dans les questions et les réponses. Pour rechercher dans les questions uniquement afin de générer une réponse, utilisez le paramètre `RankerType=QuestionOnly` dans le corps POST de la requête de l’ApI REST.

### <a name="add-alternate-questions"></a>Ajouter des questions alternatives

Les questions alternatives augmentent la probabilité de trouver une correspondance avec une requête utilisateur. Les questions alternatives sont utiles quand il existe plusieurs manières de poser une question. C’est notamment le cas pour les variantes syntaxiques et lexicales.

|Requête d’origine|Autres requêtes|Change|
|--|--|--|
|Un stationnement est-il disponible ?|Vous avez un parking ?|Variante syntaxique|
 |Salut|Bonjour<br>Ça va ?|Variante lexicale ou argot|

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>Utiliser des balises de métadonnées pour filtrer les questions et les réponses

Les métadonnées ajoutent à une application cliente la possibilité de savoir qu’elle ne doit pas récupérer toutes les réponses, mais réduire les résultats d’une requête utilisateur en fonction des balises de métadonnées. La réponse de la base de connaissances/du projet peut varier en fonction du mot clé de métadonnées, même si la requête est la même. Par exemple, dans le cas d’une chaîne de restaurants, la question *« Où se trouve le parking ? »* peut donner une réponse différente en fonction du lieu du restaurant (ici, les métadonnées sont *Location: Seattle* et *Location: Redmond*.)

### <a name="use-synonyms"></a>Utiliser des synonymes

Les synonymes en langue anglaise sont pris en charge. Utilisez des [variantes de mots](../tutorials/adding-synonyms.md) (sans respect de la casse) afin d'ajouter des synonymes à des mots clés qui prennent différentes formes.

|Mot d’origine|Synonymes|
|--|--|
|buy|purchase<br>net-banking<br>net banking|

---

### <a name="use-distinct-words-to-differentiate-questions"></a>Utiliser des mots distincts pour différencier les questions

L’algorithme de classement qui recherche une correspondance entre une requête utilisateur et les questions de la base de connaissances/du projet, retourne de meilleurs résultats si chaque question répond à un besoin différent. La répétition du même mot défini entre les questions réduit la probabilité de sélection de la réponse appropriée pour une requête utilisateur qui contient ces mots.

Par exemple, vous pouvez avoir deux entités de questions-réponses distinctes avec les questions suivantes :

|Questions|
|--|
|où est l’*emplacement* de stationnement|
|où est *l’emplacement* du distributeur automatique de billets|

Ces deux entités de questions-réponses employant des mots très similaires, elles pourraient générer des scores très similaires pour de nombreuses requêtes d’utilisateur utilisant la tournure de phrase *« où est l’emplacement de `<x>` ».* Essayez plutôt de les distinguer clairement avec des requêtes du type *« où est le parking »* et *« où est le distributeur automatique de billets »* , en évitant des mots comme « emplacement » susceptibles de figurer dans de nombreuses questions du projet.

## <a name="collaborate"></a>Travailler en collaboration

La réponse aux questions permet aux utilisateurs de collaborer sur un projet ou une base de connaissances. Les utilisateurs doivent avoir accès au groupe de ressources Azure associé pour accéder à la base de connaissances/du projet. Certaines organisations souhaitent externaliser la mise à jour et la gestion des bases de connaissances tout en pouvant quand même protéger l’accès à leurs ressources Azure. Pour ce faire, vous configurez deux ressources de langue identiques avec des projets de réponse de question identiques dans différents abonnements et en sélectionnant une pour le cycle de test de modification. Une fois le test terminé, le contenu du projet est exporté et transféré avec un processus d'[importation/exportation](../how-to/migrate-knowledge-base.md) vers la ressource de langue de l’approbateur qui déploiera enfin le projet et mettra à jour le point de terminaison.

## <a name="active-learning"></a>Apprentissage actif

L’[apprentissage actif](../tutorials/active-learning.md) est le plus à même de suggérer d’autres questions quand il dispose d’un large éventail qualitatif et quantitatif de requêtes utilisateur. Il est important d’autoriser les requêtes utilisateur des applications clientes à participer à la boucle de commentaires sur l’apprentissage actif sans censure. Une fois que vous avez suggéré les questions dans le portail Language Studio, vous pouvez passer en revue et accepter ou refuser ces suggestions.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Modifier une base de connaissances](../How-to/manage-knowledge-base.md)
