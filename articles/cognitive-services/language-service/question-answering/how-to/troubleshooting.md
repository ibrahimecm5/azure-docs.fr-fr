---
title: 'Résolution des problème : Réponses aux questions'
description: La liste organisée des questions fréquentes concernant les réponses aux questions vous aidera à adopter la fonctionnalité plus rapidement et avec de meilleurs résultats.
ms.service: cognitive-services
ms.subservice: language-service
author: mrbullwinkle
ms.author: mbullwin
ms.topic: troubleshooting
ms.date: 11/02/2021
ms.openlocfilehash: dfafd5185c62d3011dfef66eb9d244ebe8a9b549
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478958"
---
# <a name="troubleshooting-for-question-answering"></a>Résolution des problème pour les réponses aux questions

La liste organisée des questions fréquentes concernant les réponses aux questions vous aidera à adopter la fonctionnalité plus rapidement et avec de meilleurs résultats.

## <a name="manage-predictions"></a>Gérer les prédictions

<details>
<summary><b>Comment améliorer les performances de débit pour les prédictions de requête ?</b></summary>

**Réponse** : Les problèmes de performances de débit indiquent que vous devez effectuer un scale-up de votre Recherche cognitive. Envisagez d’ajouter un réplica à votre recherche cognitive pour améliorer les performances.

Apprenez-en davantage sur les [niveaux tarifaires](../Concepts/azure-resources.md).
</details>

## <a name="manage-your-project"></a>Gérer votre projet

<details>
<summary><b>Pourquoi mes URL/mes fichiers n’extraient-ils pas les paires de question-réponse ?</b></summary>

**Réponse** : Il est possible que les réponses aux questions ne puissent pas extraire automatiquement du contenu question-réponse (QnA) à partir des URL de FAQ valides. Dans ce cas, vous pouvez coller le contenu QnA dans un fichier .txt et voir si l’outil peut l’ingérer. Vous pouvez également ajouter manuellement du contenu à votre projet/base de connaissances par le biais du [portail Language Studio](https://language.azure.com).

</details>

<details>
<summary><b>Quelle taille maximale peut avoir une base de connaissances ?</b></summary>

**Réponse** : La taille de la base de connaissances dépend de la référence SKU de Recherche Azure que vous choisissez lors de la création du service QnA Maker. Lisez plus d’informations [ici](../concepts/azure-resources.md).

</details>

<details>
<summary><b>Comment partager une base de connaissances avec d’autres utilisateurs ?</b></summary>

**Réponse** : Le partage fonctionne au niveau de la ressource de langue, autrement dit, toutes les bases de connaissances associées à une ressource de langue peuvent être partagées.
</details>

<details>
<summary><b>Est-il possible de partager une base de connaissances avec un contributeur qui ne se trouve pas dans le même locataire Azure Active Directory, dans le but de la modifier ?</b></summary>

**Réponse** : Le partage est basé sur le contrôle d’accès en fonction du rôle Azure (Azure RBAC). Si vous pouvez partager _n’importe quelle_ ressource dans Azure avec un autre utilisateur, vous pouvez également partager les réponses aux questions.

</details>

<details>
<summary><b>Est-il possible d’attribuer des droits de lecture/d’écriture à cinq utilisateurs différents afin que chacun d’eux ne puisse accéder qu’à une seule base de connaissances de réponses aux questions ?</b></summary>

**Réponse** : Vous pouvez partager l’ensemble d’une ressource de langue, mais pas des bases de connaissances individuelles.

</details>

<details>
<summary><b>Les mises à jour apportées à ma base de connaissances ne sont pas reflétées en production, pourquoi ?</b></summary>

**Réponse** : Toute opération de modification, qu’elle soit effectuée dans une mise à jour de table, un test ou un paramètre, doit être enregistrée avant d’être déployée. Veillez à sélectionner **Enregistrer** après avoir apporté des modifications, puis à redéployer votre projet pour que ces modifications soient reflétées en production.

</details>

<details>
<summary><b>La base de connaissances prend-elle en charge les données enrichies ou le contenu multimédia ?</b></summary>

**Réponse** :

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>Extraction automatique de contenu multimédia pour les fichiers et URL

* URL - Fonctionnalités de conversion HTML en Markdown limitées.
* Fichiers - Non pris en charge

#### <a name="answer-text-in-markdown"></a>Texte de réponse dans Markdown

Une fois que les paires de Q/R sont dans la base de connaissances, vous pouvez modifier le texte Markdown d’une réponse de manière à inclure des liens vers des médias disponibles depuis des URL publiques.

</details>

<details>
<summary><b>Les réponses aux questions prennent-elles en charge les langues autres que l’anglais ?</b></summary>

**Réponse** : Affichez plus d’informations sur les [langues prises en charge](../language-support.md).

Si vous avez du contenu dans plusieurs langues, veillez à créer un projet distinct pour chaque langue.

</details>

## <a name="manage-service"></a>Gérer le service

<details>
<summary><b>J’ai supprimé mon service de recherche existant. Comment puis-je résoudre ce problème ?</b></summary>

**Réponse** : Si vous supprimez un index Recherche cognitive Azure, l’opération est définitive et l’index ne peut pas être récupéré.

</details>

<details>
<summary><b>J’ai supprimé l’index `testkbv2` de mon service de recherche. Comment puis-je résoudre ce problème ?</b></summary>

**Réponse** : Si vous avez supprimé l’index `testkbv2` dans votre service Search, vous pouvez restaurer les données à partir de la dernière base de connaissances publiée. Utilisez l’outil de récupération [RestoreTestKBIndex](https://github.com/pchoudhari/QnAMakerBackupRestore/tree/master/RestoreTestKBFromProd) disponible sur GitHub.

</details>

<details>
<summary><b>Puis-je utiliser la même ressource Recherche cognitive Azure pour des bases de connaissances utilisant plusieurs langues ?</b></summary>

**Réponse** : Pour utiliser plusieurs langues et plusieurs bases de connaissances, l’utilisateur doit créer un projet pour chaque langue et le premier projet créé pour la ressource de langue doit sélectionner l’option **Je souhaite sélectionner la langue lorsque je crée un projet dans cette ressource**. Cette opération crée un service de recherche Azure distinct par langue.

</details>

## <a name="integrate-with-other-services-including-bots"></a>Intégration aux autres services tels que les bots

<details>
<summary><b>Dois-je utiliser Bot Framework pour pouvoir utiliser les réponses aux questions ?</b></summary>

**Réponse** : Non, vous n’avez pas besoin d’utiliser [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) pour les réponses aux questions. Toutefois, les réponses aux questions sont proposées parmi plusieurs modèles dans [Azure Bot Service](/azure/bot-service/). Bot Service permet le développement rapide de bot intelligent via Microsoft Bot Framework et s’exécute dans un environnement serverless.

</details>

<details>
<summary><b>Comment puis-je créer un nouveau bot avec les réponses aux questions ?</b></summary>

**Réponse** : Suivez les instructions de [cette](../tutorials/bot-service.md) documentation pour créer votre bot avec Azure Bot Service.

</details>

<details>
<summary><b>Comment faire pour utiliser une base de connaissances différente avec un Azure Bot Service existant ?</b></summary>

**Réponse** : Vous devez disposer des informations suivantes concernant votre base de connaissances :

* ID de la base de connaissances.
* Nom de sous-domaine personnalisé du point de terminaison publié de la base de connaissances, appelé `host` et présent dans la page **Paramètres** après la publication.
* Clé de point de terminaison publié de la base de connaissances : affiché sur la page **Paramètres** après la publication.

Avec ces informations, accédez au service d’application de votre bot sur le portail Azure. Sous **Paramètres -> Configuration -> paramètre d’application**, modifiez les valeurs suivantes.

La clé de point de terminaison de la base de connaissances est étiquetée `QnAAuthkey` dans le service ABS.

</details>

<details>
<summary><b>Plusieurs applications clientes peuvent-elles partager une même base de connaissances ?</b></summary>

**Réponse** : Oui, la base de connaissances peut être interrogée par un nombre quelconque de clients.

</details>

<details>
<summary><b>Comment incorporer les réponses aux questions sur mon site web ?</b></summary>

**Réponses** : Procédez comme suit pour incorporer les réponses aux questions en tant que contrôle de conversation web sur votre site web :

1. Créez votre bot de FAQ en suivant les instructions [ici](../tutorials/bot-service.md).
2. Activez la conversation web en suivant [ces](../tutorials/bot-service.md#integrate-the-bot-with-channels) étapes

## <a name="data-storage"></a>Stockage des données

<details>
<summary><b>Où se trouve l’emplacement de stockage et quelles données sont stockées ?</b></summary>

**Réponse** :

Lorsque vous créez votre ressource de langue pour les réponses aux questions, vous avez sélectionné une région Azure. Vos bases de connaissances et vos fichiers journaux sont stockés dans cette région.

</details>
