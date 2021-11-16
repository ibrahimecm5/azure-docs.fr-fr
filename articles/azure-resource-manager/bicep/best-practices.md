---
title: Découvrez les meilleures pratiques liées au développement de fichiers Bicep
description: Décrit les pratiques à suivre lors de la création de fichiers Bicep afin qu'ils fonctionnent bien et soient faciles à gérer.
author: johndowns
ms.author: jodowns
ms.topic: conceptual
ms.date: 11/02/2021
ms.openlocfilehash: 65f55208f0a2e09db39cedc8e5074b622b232834
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057667"
---
# <a name="best-practices-for-bicep"></a>Meilleures pratiques en matière de fichiers Bicep

Cet article présente les pratiques à suivre lors du développement de fichiers Bicep. Ces pratiques facilitent la compréhension et l'utilisation des fichiers Bicep.

### <a name="microsoft-learn"></a>Microsoft Learn

Pour en savoir plus sur les meilleures pratiques en lien avec Bicep et pour obtenir des conseils pratiques, consultez [Structurer votre code Bicep pour la collaboration](/learn/modules/structure-bicep-code-collaboration/) dans **Microsoft Learn**.

## <a name="parameters"></a>Paramètres

* Utilisez un nommage correct des noms pour la déclaration du paramètre. Des noms pertinents rendent vos modèles faciles à lire et à comprendre. Assurez-vous que vous utilisez des noms clairs et descriptifs, et que le nommage est cohérent.

* Réfléchissez bien aux paramètres utilisés par votre modèle. Essayez d’utiliser des paramètres pour les valeurs qui varient entre les déploiements. Les variables et les valeurs codées en dur peuvent être utilisées pour les paramètres qui ne changent pas entre les déploiements.

* Faites attention aux valeurs par défaut que vous utilisez. Assurez-vous que les valeurs par défaut peuvent être déployées en toute sécurité. Par exemple, envisagez d'utiliser des niveaux tarifaires et des références SKU à faible coût afin d'éviter des frais inutilement élevés si quelqu'un déploie le modèle dans un environnement de test.

* Utilisez l’élément décoratif `@allowed` avec parcimonie. Si vous utilisez cet élément décoratif trop souvent, vous risquez de bloquer des déploiements valides. Comme les services Azure ajoutent des références SKU et des tailles, votre liste autorisée peut ne pas être à jour. Par exemple, le fait d'autoriser uniquement les références SKU Premium v3 peut avoir du sens en production, mais cela vous empêche d'utiliser le même modèle dans des environnements hors production.

* Il est recommandé de fournir des descriptions pour vos paramètres. Essayez d’utiliser des descriptions utiles et fournissez toutes les informations importantes sur les exigences du modèle pour les valeurs de paramètres.

  Vous pouvez également utiliser des commentaires `//` pour certaines informations.

* Vous pouvez placer ces déclarations de paramètres n'importe où dans le fichier de modèle. Cependant, il est généralement plus judicieux de les placer en haut du fichier afin de faciliter la lecture de votre code Bicep.

* Il est recommandé de spécifier la longueur minimale et maximale des caractères pour les paramètres qui contrôlent le nommage. Ces limitations permettent d'éviter les erreurs ultérieures lors du déploiement.

Pour plus d'informations sur les paramètres Bicep, consultez [Paramètres dans Bicep](parameters.md).

## <a name="variables"></a>Variables

* Lorsque vous définissez une variable, le [type de données](data-types.md) n'est pas nécessaire. Les variables déduisent le type à partir de la valeur de résolution.

* Vous pouvez utiliser les fonctions Bicep pour créer une variable.

* Après avoir défini une variable dans votre fichier Bicep, vous faites référence à la valeur en utilisant le nom de la variable.

Pour plus d'informations sur les variables Bicep, consultez [Variables dans Bicep](variables.md).

## <a name="names"></a>Noms

* Utilisez une casse mixte pour des noms de variables tels que `myVariableName` ou `myResource`.

* La [fonction uniqueString()](bicep-functions-string.md#uniquestring) permet de créer des noms de ressources uniques à l'échelle mondiale. Lorsque vous fournissez les mêmes paramètres, elle renvoie la même chaîne à chaque fois. La transmission de l'ID du groupe de ressources signifie que la chaîne est la même pour tous les déploiements effectués dans le même groupe de ressources, mais qu'elle est différente lorsque vous effectuez des déploiements dans des groupes de ressources ou abonnements différents.

* Parfois, la fonction `uniqueString()` crée des chaînes qui commencent par un chiffre. Certaines ressources Azure, comme les comptes de stockage, n’autorisent pas les noms qui commencent par un chiffre. Cette exigence signifie qu'il est judicieux d'utiliser l'interpolation de chaîne pour créer des noms de ressources. Vous pouvez ajouter un préfixe à la chaîne unique.

* Il est souvent judicieux d’utiliser des expressions de modèle pour créer des noms de ressources. De nombreux types de ressources Azure ont des règles relatives aux caractères autorisés et à la longueur des noms. Le fait d’incorporer la création des noms de ressources dans le modèle signifie que les personnes utilisant le modèle n’ont pas besoin de suivre ces règles elles-mêmes.

* Évitez d’utiliser `name` dans un nom symbolique. Le nom symbolique représente la ressource, pas le nom de la ressource. Par exemple, au lieu de ceci :

  ```bicep
  resource cosmosDBAccountName 'Microsoft.DocumentDB/databaseAccounts@2021-04-15' = {
  ```

  utilisez ceci :

  ```bicep
  resource cosmosDBAccount 'Microsoft.DocumentDB/databaseAccounts@2021-04-15' = {
  ```

* Évitez de faire la distinction entre les variables et les paramètres à l’aide de suffixes.

## <a name="resource-definitions"></a>Définitions de ressources

* Au lieu d'incorporer des expressions complexes directement dans les propriétés des ressources, utilisez des variables pour contenir les expressions. Cette approche rend votre fichier Bicep plus lisible et plus compréhensible. Elle évite d'encombrer vos définitions de ressources avec de la logique.

* Essayez d’utiliser les propriétés de ressource en tant que sorties, plutôt que de faire des hypothèses sur le comportement des ressources. Par exemple, si vous devez sortir l'URL dans une application App Service, utilisez la propriété defaultHostname de l'application au lieu de créer vous-même une chaîne pour l'URL. Ces hypothèses ne sont pas toujours correctes dans des environnements différents, ou les ressources modifient leur fonctionnement. Il est plus sûr de faire en sorte que la ressource vous indique ses propres propriétés.

* Il est judicieux d’utiliser une version d’API récente pour chaque ressource. Certaines nouvelles fonctionnalités des services Azure sont parfois disponibles uniquement dans les versions d’API les plus récentes.

* Dans la mesure du possible, évitez d’utiliser les fonctions [reference](./bicep-functions-resource.md#reference) et [resourceId](./bicep-functions-resource.md#resourceid) dans votre fichier Bicep. Vous pouvez accéder à n'importe quelle ressource Bicep en utilisant le nom symbolique. Par exemple, si vous définissez un compte de stockage dont le nom symbolique est toyDesignDocumentsStorageAccount, vous pouvez accéder à son ID de ressource en utilisant l'expression `toyDesignDocumentsStorageAccount.id`. En utilisant le nom symbolique, vous créez une dépendance implicite entre les ressources.

* Préférez utiliser des dépendances implicites plutôt que des dépendances explicites. Bien que la propriété de ressource `dependsOn` vous permette de déclarer une dépendance explicite entre les ressources, il est généralement possible d’utiliser les propriétés de l’autre ressource à l’aide de son nom symbolique. Cela crée une dépendance implicite entre les deux ressources, et permet à Bicep de gérer la relation proprement dite.

* Si la ressource n'est pas déployée dans le fichier Bicep, vous pouvez toujours obtenir une référence symbolique à la ressource à l'aide du mot clé `existing`.

## <a name="child-resources"></a>Ressources enfants

* Évitez d'imbriquer trop de couches en profondeur. Une imbrication excessive rend votre code Bicep plus difficile à lire et à utiliser.

* Évitez de créer des noms de ressources pour les ressources enfants. Vous perdez les avantages qu'offre Bicep lorsqu'il comprend les relations entre vos ressources. Utilisez plutôt la propriété `parent` ou l'imbrication.

## <a name="outputs"></a>Sorties

* Veillez à ne pas créer de sorties pour les données sensibles. Les valeurs de sortie sont accessibles à toute personne ayant accès à l’historique de déploiement. Elles ne sont pas appropriées pour gérer les secrets.

* Au lieu de transmettre les valeurs des propriétés à travers des sorties, utilisez le mot clé `[existing`(resource-declaration.md#existing-resources) pour rechercher les propriétés des ressources qui existent déjà. Une meilleure pratique consiste à rechercher des clés à partir d’autres ressources de cette manière plutôt que de les transmettre via des sorties. Vous obtiendrez toujours les données les plus actuelles.

Pour plus d'informations sur les sorties Bicep, consultez [Sorties dans Bicep](outputs.md).

## <a name="tenant-scopes"></a>Étendues des locataires

Il est impossible de créer des stratégies ou des attributions de rôles dans l'[étendue d'un locataire](deploy-to-tenant.md). Toutefois, si vous devez accorder des accès ou appliquer des stratégies à l’ensemble de votre organisation, vous pouvez déployer ces ressources dans le groupe d’administration racine.

## <a name="next-steps"></a>Étapes suivantes

* Pour accéder à une présentation de Bicep, consultez [Démarrage rapide de Bicep](quickstart-create-bicep-use-visual-studio-code.md).
* Pour plus d'informations sur les différentes parties d'un fichier Bicep, consultez [Présentation de la structure et de la syntaxe des fichiers Bicep](file.md).
