---
author: bwren
ms.author: bwren
ms.service: azure-monitor
ms.topic: include
ms.date: 09/17/2021
ms.openlocfilehash: 9db1bd652fed38cb03bca6a590ea217d0fadf169
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350495"
---
Configurez des paramètres différents pour la règle d’alerte dans la section **Détails de la règle d’alerte**.

- **Nom de la règle d’alerte** qui doit être descriptif, car il s’affiche lorsque l’alerte est déclenchée. 
- Vous pouvez également fournir une **description** qui est incluse dans les détails de l’alerte.
- **Abonnement** et **groupe de ressources** dans lesquels la règle d’alerte sera stockée. Il ne doit pas nécessairement s’agir du même groupe de ressources que celui de la ressource en cours de monitoring.
- **Gravité** de l’alerte. Elle permet de regrouper les alertes présentant une importance relative similaire. Une gravité de niveau **Erreur** est appropriée pour une machine virtuelle qui ne répond pas.
- Maintenez la case **Activer l’alerte dès la création** cochée.
- Maintenez la case **Résoudre automatiquement les alertes** cochée. Cette option permet de résoudre automatiquement l’alerte lorsque la valeur de la métrique passe sous le seuil. 
