---
title: Erreurs de nom de ressource réservé
description: Explique comment résoudre les erreurs quand vous fournissez un nom de ressource qui inclut un mot réservé.
ms.topic: troubleshooting
ms.date: 11/02/2021
ms.openlocfilehash: 28376ed34e2007c3f6cfb7f57f08199e5a1ade8a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131458833"
---
# <a name="resolve-reserved-resource-name-errors"></a>Résoudre les erreurs de nom de ressource réservé

Cet article décrit l’erreur que vous rencontrez quand vous déployez une ressource dont le nom inclut un mot réservé.

## <a name="symptom"></a>Symptôme

Au cours du déploiement d’une ressource qui est disponible par le biais d’un point de terminaison public, vous pouvez rencontrer l’erreur suivante :

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>Cause

Le nom d’une ressource qui a un point de terminaison public ne peut pas contenir de mots réservés ou de marques.

Les mots suivants sont réservés :

* ACCESS
* APP_CODE
* APP_THEMES
* APP_DATA
* APP_GLOBALRESOURCES
* APP_LOCALRESOURCES
* APP_WEBREFERENCES
* APP_BROWSERS
* AZURE
* BING
* BIZSPARK
* BIZTALK
* CORTANA
* DIRECTX
* DOTNET
* DYNAMICS
* EXCEL
* EXCHANGE
* FOREFRONT
* GROOVE
* HOLOLENS
* HYPERV
* KINECT
* LYNC
* MSDN
* O365
* OFFICE
* OFFICE365
* ONEDRIVE
* ONENOTE
* OUTLOOK
* POWERPOINT
* SHAREPOINT
* SKYPE
* VISIO
* VISUALSTUDIO

Les mots suivants ne peuvent pas être utilisés en tant que mot entier ou sous-chaîne dans le nom :

* MICROSOFT
* WINDOWS

## <a name="solution"></a>Solution

Fournissez un nom qui n’utilise aucun des mots réservés.
