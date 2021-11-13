---
title: 'Azure Front Door : mise en cache'
description: Cet article explique le comportement d’Azure Front Door Standard/Premium avec les règles de routage ayant la mise en cache activée.
services: front-door
author: duongau
manager: KumudD
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 687a18ff1f66f5bd14a76b620c4fda04e6b7a6f0
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130234974"
---
# <a name="caching-with-azure-front-door-standardpremium-preview"></a>Mise en cache avec Azure Front Door Standard/Premium (préversion)

> [!Note]
> Cette documentation est destinée à Azure Front Door Standard/Premium (préversion). Vous recherchez des informations sur Azure Front Door ? Affichez [ici](../front-door-overview.md).

Dans cet article, vous allez découvrir comment les routes et un ensemble de règles Front Door Standard/Premium (préversion) se comportent quand la mise en cache est activée. Azure Front Door est un réseau de distribution de contenu (CDN) moderne qui prend en charge l’accélération de site dynamique et l’équilibrage de charge.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (préversion) est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="request-methods"></a>Méthodes de demande

Seule la méthode de demande GET peut générer le contenu mis en cache dans Azure Front Door. Toutes les autres méthodes de demande sont toujours traitées par proxy sur le réseau.

## <a name="delivery-of-large-files"></a>Distribution de fichiers volumineux

Front Door Standard/Premium (préversion) permet de distribuer des fichiers volumineux sans aucune limite de taille de fichier. Front Door utilise une technique appelée segmentation d’objets. Quand un fichier volumineux est demandé, l’instance Front Door récupère les petits éléments du fichier auprès de l’origine. Après avoir reçu une demande de fichier complet ou de plage d’octets, l’environnement Front Door demande le fichier de l’origine par blocs de 8 Mo.

Dès qu’un bloc parvient à l’environnement Front Door, il est mis en cache et immédiatement servi à l’utilisateur. La porte d’entrée se prépare ensuite à récupérer le bloc suivant en parallèle. Cette prérécupération est l’assurance que le contenu a un bloc d’avance sur l’utilisateur, ce qui réduit la latence. Ce processus se poursuit jusqu’à ce que le fichier entier soit téléchargé (si nécessaire) ou que le client ferme la connexion.

Pour plus d’informations sur la demande de plage d’octets, consultez [RFC 7233](https://www.rfc-editor.org/info/rfc7233).
Front Door met en cache les blocs au fur et à mesure de leur réception, ce qui évite d’avoir à mettre l’intégralité du fichier dans le cache Front Door. Les demandes subséquentes du fichier ou de plages d’octets sont servies à partir du cache. Si certains blocs ne sont pas mis en cache, ils sont demandés au serveur principal via une prérécupération. Cette optimisation s’appuie sur la capacité de l’origine à prendre en charge les demandes de plages d’octets. Si l’origine ne prend pas en charge les demandes de plages d’octets, cette optimisation n’est pas effective.

## <a name="file-compression"></a>Compression de fichiers

Reportez-vous à la documentation sur [l’amélioration des performances en compressant les fichiers](how-to-compression.md) dans Azure Front Door.

## <a name="query-string-behavior"></a>Comportement des chaînes de requête

Avec Front Door, vous pouvez contrôler la manière dont les fichiers sont mis en cache pour une requête web qui contient une chaîne de requête. Dans une requête web contenant une chaîne de requête, la chaîne de requête représente la partie de la demande qui apparaît après le point d’interrogation (?). Une chaîne de requête peut contenir une ou plusieurs paires clé-valeur où le nom du champ et sa valeur sont séparés par un signe égal (=). Chaque paire clé-valeur est séparée par une esperluette (&). Par exemple : `http://www.contoso.com/content.mov?field1=value1&field2=value2`. S’il existe plusieurs paires clé-valeur dans la chaîne de requête d’une demande, leur ordre n’a pas d’importance.

* **Ignorer les chaînes de requête** : Dans ce mode, Front Door transmet les chaînes de requête du demandeur à l’origine à la première demande et met en cache l’actif multimédia. Toutes les demandes subséquentes portant sur l’élément multimédia, qui sont traitées par l’environnement Front Door, ignorent les chaînes de requête tant que l’élément multimédia mis en cache n’est pas arrivé à expiration.

* **Mettre en cache chaque URL unique** : dans ce mode, chaque demande contenant une URL unique, y compris la chaîne de requête, est traitée comme une ressource unique avec son propre cache. Par exemple, la réponse de l’origine à une demande pour `www.example.ashx?q=test1` est mise en cache dans l’environnement Front Door et retournée pour les mises en cache subséquentes avec la même chaîne de requête. Une demande pour `www.example.ashx?q=test2` est mise en cache en tant que ressource distincte avec son propre paramètre de durée de vie.
* Vous pouvez également utiliser un ensemble de règles pour spécifier le comportement de la **chaîne de requête de clé de cache**, afin d’inclure ou d’exclure des paramètres spécifiés lors de la génération de la clé de cache. Par exemple, la clé de cache par défaut est /foo/image/asset.html et la demande est `https://contoso.com//foo/image/asset.html?language=EN&userid=100&sessionid=200`. Une règle de l’ensemble de règles exclut la chaîne de requête « userid ». La clé de cache de la chaîne de requête est alors `/foo/image/asset.html?language=EN&sessionid=200`.

## <a name="cache-purge"></a>Vidage du cache

Reportez-vous à la documentation sur le vidage du cache.

## <a name="cache-expiration"></a>Expiration du cache
L’ordre suivant des en-têtes sert à déterminer la durée de stockage d’un élément dans le cache :</br>
1. Cache-Control: s-maxage=\<seconds>
2. Cache-Control: max-age=\<seconds>
3. Expire : \<http-date>

Les en-têtes de réponse Cache-Control qui indiquent que la réponse n’est pas mise en cache, comme Cache-Control: private, Cache-Control: no cache et Cache-Control: no-store, sont respectés.  En l’absence de Cache-Control, par défaut, Front Door met en cache la ressource pendant une durée X, où X est choisi de manière aléatoire entre 1 et 3 jours.

## <a name="request-headers"></a>En-têtes de requête

Les en-têtes de demande suivants ne sont pas transférés à une origine quand la mise en cache est utilisée.
* Content-Length
* Transfer-Encoding

## <a name="cache-behavior-and-duration"></a>Comportement et durée de mise en cache

Le comportement et la durée de la mise en cache peuvent être configurés à la fois dans la règle d’acheminement du concepteur Front Door et dans le moteur de règles. La configuration de la mise en cache du moteur de règles aura toujours la priorité sur la configuration de la règle d’acheminement du concepteur Front Door.

* Lorsque la *mise en cache* est **désactivée**, Front Door ne met pas en cache le contenu de la réponse, quelles que soient les directives de réponse de l’origin.

* Lorsque la *mise en cache* est **activée**, le comportement de mise en cache est différent selon les valeurs de l’option *Utiliser la durée de mise en cache par défaut*.
    * Lorsque l’option *Utiliser la durée de mise en cache par défaut* est définie sur **Oui**, Front Door respecte toujours la directive d’en-tête de réponse de l’origin. Si la directive de l’origin est absente, Front Door mettra le contenu en cache entre un et trois jours.
    * Lorsque l’option *Utiliser la durée de mise en cache par défaut* est définie sur **Non**, Front Door utilise toujours la *durée de mise en cache* (champs obligatoires), ce qui signifie qu’il met en cache le contenu pendant la durée de mise en cache en ignorant les valeurs des directives de réponse de l’origin. 

> [!NOTE]
> * La *durée de mise en cache* définie dans la règle d’acheminement du concepteur Front Door correspond à la **durée minimale de mise en cache**. Ce remplacement ne fonctionnera pas si l’en-tête de contrôle du cache de l’origin a une durée de vie supérieure à la valeur de remplacement.
> * Azure Front Door n’offre aucune garantie quant à la durée minimale pendant laquelle l’objet sera stocké dans le cache. Le contenu mis en cache peut être supprimé du cache de périphérie avant son expiration s’il n’est pas demandé aussi fréquemment, afin de faire de la place pour le contenu plus fréquemment demandé.
>

## <a name="next-steps"></a>Étapes suivantes

* Découvrez-en plus sur les [conditions de correspondance d’un ensemble de règles](concept-rule-set-match-conditions.md).
* Découvrez-en plus sur les [actions d’un ensemble de règles](concept-rule-set-actions.md).
