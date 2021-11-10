---
title: Configurer une entrée HTTPS dans Azure Container Apps (préversion)
description: Activer des points de terminaison publics et privés dans votre application avec Azure Container Apps
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: how-to
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5979588ec977ef7655fab21ef30e4fd233c12b43
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132026422"
---
# <a name="set-up-https-ingress-in-azure-container-apps-preview"></a>Configurer une entrée HTTPS dans Azure Container Apps (préversion)

Azure Container Apps vous permet d’exposer votre application conteneur au web public en activant une entrée (ingress). Lorsque vous activez une entrée, vous n’avez pas besoin de créer un équilibreur de charge Azure, une adresse IP publique ni d’autres ressources Azure pour activer les requêtes HTTPS entrantes.

Une fois l’entrée activée, votre application conteneur présente les caractéristiques suivantes :

- Prise en charge de la terminaison TLS
- Prise en charge de HTTP/1.1 et HTTP/2
- Les points de terminaison utilisent toujours TLS 1.2, avec terminaison au point d’entrée
- Les points de terminaison exposent toujours les ports 80 (pour HTTP) et 443 (pour HTTPS).
  - Par défaut, les requêtes HTTP sur le port 80 sont automatiquement redirigées vers HTTPS sur le port 443.

## <a name="configuration"></a>Configuration

Une entrée est un paramètre à l’échelle de l’application. Les changements dans les paramètres d’entrée s’appliquent à toutes les révisions simultanément et ne génèrent pas de nouvelles révisions.

La section sur la configuration d’une entrée se présente sous la forme suivante :

```json
{
  ...
  "configuration": {
      "ingress": {
          "external": true,
          "targetPort": 80,
          "transport": auto
      }
  }
}
```

Les paramètres suivants sont disponibles lors de la configuration d’une entrée :

| Propriété | Description | Valeurs | Obligatoire |
|---|---|---|---|
| `external` | L’IP et le nom de domaine complet de votre entrée peuvent être visibles en externe sur Internet ou en interne dans un VNET. |`true` pour la visibilité externe, `false` pour la visibilité interne (par défaut) | Oui |
| `targetPort` | Port que votre conteneur écoute pour les demandes entrantes. | Définissez cette valeur sur le numéro de port qu’utilise votre conteneur. Le point de terminaison d’entrée de votre application est toujours exposé sur le port `443`. | Oui |
| `transport` | Vous pouvez utiliser HTTP/1.1 ou HTTP/2, ou vous pouvez le définir pour qu’il détecte automatiquement le type de transport. | `http` pour HTTP/1, `http2` pour HTTP/2, `auto` pour détecter automatiquement le type de transport (par défaut) | Non |
| `allowInsecure` | Autorise le trafic non sécurisé vers votre application conteneur. | `false` (par défaut), `true`<br><br>Si `true` est défini, les requêtes HTTP sur le port 80 ne sont pas automatiquement redirigées vers le port 443 avec HTTPS, ce qui laisse libre cours aux connexions non sécurisées. | Non |

> [!NOTE]
> Pour désactiver une entrée pour votre application, vous pouvez totalement omettre la propriété de configuration `ingress`.

## <a name="ip-addresses-and-domain-names"></a>Adresses IP et noms de domaine

Quand une entrée est activée, un nom de domaine complet (FQDN) est attribué à votre application. Le nom de domaine prend les formes suivantes :

|Paramètre de visibilité de l’entrée | Nom de domaine complet |
|---|---|
| Externe | `<APP_NAME>.<UNIQUE_IDENTIFIER>.<REGION_NAME>.azurecontainerapps.io`|
| Interne | `<APP_NAME>.internal.<UNIQUE_IDENTIFIER>.<REGION_NAME>.azurecontainerapps.io` |

Votre environnement Container Apps a une seule adresse IP publique pour les applications avec une visibilité d’entrée `external`, et une seule adresse IP interne pour les applications avec une visibilité d’entrée `internal`. Par conséquent, toutes les applications d’un environnement Container Apps avec une visibilité d’entrée `external` partagent une même adresse IP publique. De même, toutes les applications d’un environnement Container Apps avec une visibilité d’entrée `internal` partagent une même adresse IP interne. Le trafic HTTP est routé vers des applications individuelles basées sur le nom FQDN de l’en-tête de l’hôte.

Vous pouvez accéder à l’identificateur unique de l’environnement en interrogeant les paramètres d’environnement.

[!INCLUDE [container-apps-get-fully-qualified-domain-name](../../includes/container-apps-get-fully-qualified-domain-name.md)]

> [!div class="nextstepaction"]
> [Gérer la mise à l’échelle](scale-app.md)
