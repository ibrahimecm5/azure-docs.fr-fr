---
title: Instructions pour la résolution des problèmes du Connecteur de services
description: Liste d’erreurs et actions suggérées du Connecteur de services
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8a1379495dacba7d2c0cf21af3a1e5ec2f45ed41
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132283180"
---
# <a name="how-to-troubleshoot-with-service-connector"></a>Comment résoudre des problèmes liés au Connecteur de services

Si vous rencontrez un problème, vous pouvez vous référer au message d’erreur pour rechercher des actions ou correctifs suggérés. Ce guide pratique présente les options permettant de résoudre des problèmes liés au Connecteur de services.

## <a name="error-message-and-suggested-actions-from-portal"></a>Message d’erreur et actions suggérées en provenance du portail

| Message d’erreur | Action suggérée |
| --- | --- |
| Type de ressource inconnu | <ul><li>Contrôlez les ressources source et cible pour vérifier si le Connecteur de services prend en charge les types de services.</li><li>Vérifiez si le Connecteur de services prend en charge la combinaison de connexion source-cible spécifiée.</li></ul> |
| Type de ressource inconnu | <ul><li>Vérifiez si la ressource cible existe.</li><li>Vérifiez l’exactitude de l’ID de la ressource cible.</li></ul> |
| Ressource non prise en charge | <ul><li>Vérifiez si la combinaison de connexion source-cible spécifiée prend en charge le type d’authentification.</li></ul> |

### <a name="error-typeerror-message-and-suggested-actions-using-azure-cli"></a>Type d’erreur, message d’erreur et actions suggérées à l’aide d’Azure CLI

### <a name="invalidargumentvalueerror"></a>InvalidArgumentValueError


| Message d’erreur | Action suggérée |
| --- | --- |
| L’ID de la ressource source n’est pas valide : `{SourceId}` | <ul><li>Vérifiez si le Connecteur de services prend en charge l’ID de la ressource source.</li><li>Vérifiez l’exactitude de l’ID de la ressource source.</li></ul> |
| L’ID de la ressource cible n’est pas valide : `{TargetId}` | <ul><li>Vérifiez si le Connecteur de services prend en charge le type de service.</li><li>Vérifiez l’exactitude de l’ID de la ressource cible.</li></ul> |
| L’ID de connexion n’est pas valide : `{ConnectionId}` | <ul><li>Vérifiez l’exactitude de l’ID de connexion.</li></ul> |


### <a name="requiredargumentmissingerror"></a>RequiredArgumentMissingError

| Message d’erreur | Action suggérée |
| --- | --- |
| `{Argument}` ne peut pas être vide. | L’utilisateur doit fournir une valeur d’argument pour une entrée interactive |
| Clés requises manquantes pour le paramètre `{Parameter}`. Toutes les clés possibles sont les suivantes : `{Keys}` | Fournissez une valeur pour le paramètre d’informations d’authentification, généralement sous la forme `--param key1=val1 key2=val2`. |
| L’argument requis est manquant. Fournissez les arguments suivants : `{Arguments}` | Fournissez l’argument requis. | 

### <a name="validationerror"></a>ValidationError

| Message d’erreur | Action suggérée |
| --- | --- |
| Une seule information d’authentification est nécessaire | L’utilisateur ne peut fournir qu’un seul paramètre d’informations d’authentification. Vérifiez si les informations d’authentification ne sont pas fournies ou si plusieurs paramètres d’informations d’authentification sont fournis. |
| L’argument des informations d’authentification doit être fourni lors de la mise à jour de la connexion : `{ConnectionName}` | Lors de la mise à jour d’une connexion de type secret, un paramètre d’informations d’authentification doit être fourni. Cela est dû au fait que le secret de l’utilisateur n’est pas accessible via l’API ARM. |
| Le type de client ou les informations d’authentification doivent être spécifiés pour la mise à jour | Lors de la mise à jour d’une connexion, vous devez fournir un type de client ou des informations d’authentification. |
| Erreur d’utilisation : {} [KEY=VALUE ...] | Vérifiez les clés disponibles et fournissez des valeurs pour le paramètre des informations d’authentification, généralement sous la forme `--param key1=val1 key2=val2`. |
| Une clé non prise en charge `{Key}` est fournie pour le paramètre `{Parameter}`. Toutes les clés possibles sont les suivantes : `{Keys}` | Vérifiez les clés disponibles et fournissez des valeurs pour le paramètre des informations d’authentification, généralement sous la forme `--param key1=val1 key2=val2`. |
| L’approvisionnement a échoué. Créez la ressource cible manuellement, puis créez la connexion. Détails de l’erreur : `{ErrorTrace}` | <ul><li>Reprise</li><li>Créez la ressource cible manuellement, puis créez la connexion.</li></ul> |

## <a name="next-steps"></a>Étapes suivantes

Suivez les tutoriels ci-dessous pour en savoir plus sur Service Connector.

> [!div class="nextstepaction"]
> [En savoir plus sur les concepts Service Connector](./concept-service-connector-internals.md)
