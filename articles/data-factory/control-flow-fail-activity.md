---
title: Exécuter une activité Fail dans Azure Data Factory et Synapse Analytics
titleSuffix: Azure Data Factory & Azure Synapse
description: Cet article explique la façon dont une activité Fail dans Azure Data Factory et Synapse Analytics provoque intentionnellement une erreur dans un pipeline.
author: chez-charlie
ms.author: chez
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/22/2021
ms.openlocfilehash: 2f0acb894d6bdacef69dcf8adb2088214e094415
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517954"
---
# <a name="execute-a-fail-activity-in-azure-data-factory-and-synapse-analytics"></a>Exécuter une activité Fail dans Azure Data Factory et Synapse Analytics

Il se peut que vous souhaitiez parfois générer une erreur dans un pipeline de manière intentionnelle. Une [activité Lookup](control-flow-lookup-activity.md) peut ne renvoyer aucunes données correspondantes, ou une [activité Custom](transform-data-using-dotnet-custom-activity.md) peut se terminer par une erreur interne. Quelle que soit la raison, vous pouvez désormais utiliser une activité Fail dans un pipeline et personnaliser son message et son code d’erreur.

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


## <a name="syntax"></a>Syntaxe

```json
{
    "name": "MyFailActivity",
    "type": "Fail",
    "typeProperties": {
        "message": "500",
        "errorCode": "My Custom Error Message"
    }
}

```

## <a name="type-properties"></a>Propriétés type

| Propriété | Description | Valeurs autorisées | Obligatoire |
| --- | --- | --- | --- |
| name | Nom de l’activité Fail. | String | Oui |
| type | Doit être défini sur **Échec**. | String | Oui |
| message | Message d’erreur qui est apparu dans l’activité Fail. Il peut s’agir d’un contenu dynamique qui est évalué au moment de l’exécution. | String | Oui |
| errorCode | Code d’erreur qui catégorise le type d’erreur de l’activité Fail. Il peut s’agir d’un contenu dynamique qui est évalué au moment de l’exécution. | String | Oui |
| | |

## <a name="understand-the-fail-activity-error-code"></a>Comprendre le code d’erreur de l’activité Fail

Le message et le code d’erreur d’une activité Fail sont généralement définis par les utilisateurs. Pour comprendre les significations spécifiques des codes d’erreur, contactez le développeur du pipeline. Toutefois, dans les cas limites suivants, Azure Data Factory définit le message d’erreur et/ou le code d’erreur.

| Description de la situation | Message d’erreur | Code d'erreur |
| --- | --- | --- |
Le contenu (dynamique) dans `message` et `errorCode` est interprété correctement. | Message d’erreur défini par l’utilisateur | Code d’erreur défini par l’utilisateur |
Le contenu dynamique dans `message` et `errorCode` ne peut pas être interprété. | « N’a pas pu interpréter le message d’échec ou le code d’erreur _<activity_name>_  » | `ErrorCodeNotString` |
| Le contenu dynamique dans `message` ne peut pas être interprété en tant que chaîne. | « Le paramètre de message d’échec _<activity_name>_ n’a pas pu être interprété en tant que chaîne » | Code d’erreur défini par l’utilisateur |
| Le contenu dynamique dans `message` se résout en Null, une chaîne vide ou des espaces blancs. | « N’a pas pu interpréter le message d’échec ou le code d’erreur _<activity_name>_  » | Code d’erreur défini par l’utilisateur |
| Le contenu dynamique dans `errorCode` ne peut pas être interprété en tant que chaîne. | Message d’erreur défini par l’utilisateur | `ErrorCodeNotString` |
| Le contenu dynamique dans `errorCode` se résout en Null, une chaîne vide ou des espaces blancs. | Message d’erreur défini par l’utilisateur | `ErrorCodeNotString` |
| La valeur pour `message` ou `errorCode` fournie par l’utilisateur n’est pas convertible en chaîne.* | Le pipeline _échoue_ avec l’erreur « Valeur non valide de la propriété <`errorCode`/`message`> » | |
| Le champ `message` est manquant.* | « Le message d’échec n’a pas été fourni » | Code d’erreur défini par l’utilisateur |
| Le champ `errorCode` est manquant.* | Message d’erreur défini par l’utilisateur | `ErrorCodeNotString` |
| | |

\* Cette situation ne devraient pas se produire si le pipeline est développé avec l’interface utilisateur web de Data Factory.

## <a name="next-steps"></a>Étapes suivantes

Consultez d’autres activités de flux de contrôle prises en charge, à savoir :

- [Activité If Condition](control-flow-if-condition-activity.md)
- [Activité d’exécution du pipeline](control-flow-execute-pipeline-activity.md)
- [Activité ForEach](control-flow-for-each-activity.md)
- [Activité d’obtention des métadonnées](control-flow-get-metadata-activity.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité Web](control-flow-web-activity.md)
- [Activité Until](control-flow-until-activity.md)
- [Comprendre l’erreur de pipeline](tutorial-pipeline-failure-error-handling.md)
