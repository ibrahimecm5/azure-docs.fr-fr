---
title: Résoudre les problèmes d’exportation de données à partir d’Azure IoT Central | Microsoft Docs
description: Résoudre les problèmes liés aux exportations de données dans IoT Central
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 10/26/2021
ms.topic: troubleshooting
ms.service: iot-central
ms.openlocfilehash: 0fd283c1f3740e3e06f7a41cc66874596159e8f1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131098197"
---
# <a name="troubleshoot-issues-with-data-exports-from-your-azure-iot-central-application"></a>Résoudre les problèmes liés aux exportations de données à partir de votre application Azure IoT Central

Ce document vous aide à déterminer la raison pour laquelle les données de votre application IoT Central n’atteignent pas la destination prévue ou n’arrivent pas dans le bon format.

## <a name="managed-identity-issues"></a>Problèmes liés aux identités managées

Vous utilisez une identité managée pour autoriser la connexion à une destination d’exportation. Les données n’arrivent pas à la destination de l’exportation.

Avant de configurer ou d’activer la destination d’exportation, assurez-vous d’effectuer les étapes suivantes :

- Activez l’identité managée pour l’application.
- Configurez les autorisations pour l’identité managée.
- Configurez des réseaux virtuels, des points de terminaison privés et des stratégies de pare-feu.

Pour en savoir plus, consultez [Exporter des données](howto-export-data.md?tabs=managed-identity).

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’aide supplémentaire, vous avez la possibilité de contacter les experts Azure sur les [forums MSDN Azure et Stack Overflow](https://azure.microsoft.com/support/community/). Vous pouvez également soumettre un [ticket de support Azure](https://portal.azure.com/#create/Microsoft.Support).

Pour plus d’informations, consultez [Options d’aide et de support Azure IoT](../../iot-fundamentals/iot-support-help.md).
