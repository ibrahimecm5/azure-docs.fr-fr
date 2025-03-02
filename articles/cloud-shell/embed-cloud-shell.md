---
title: Incorporer Azure Cloud Shell | Microsoft Docs
description: Découvrez comment incorporer Azure Cloud Shell.
services: cloud-shell
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: damaerte
ms.openlocfilehash: 6f0f6f7a743796c825870de0c7cac668f67372e2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132310126"
---
# <a name="embed-azure-cloud-shell"></a>Incorporer Azure Cloud Shell

L’incorporation de Cloud Shell permet aux développeurs et aux rédacteurs de contenu d’ouvrir directement Cloud Shell à partir d’une URL dédiée, [shell.azure.com](https://shell.azure.com). Ainsi, vos utilisateurs bénéficient immédiatement de toute la puissance de l’authentification et des outils de Cloud Shell, et des outils Azure CLI/Azure PowerShell les plus récents.

Bouton de taille normale

[![Lancement normal](https://shell.azure.com/images/launchcloudshell.png "Lancement d’Azure Cloud Shell")](https://shell.azure.com)

Bouton de grande taille

[![Lancement volumineux](https://shell.azure.com/images/launchcloudshell@2x.png "Lancement d’Azure Cloud Shell")](https://shell.azure.com)

## <a name="how-to"></a>Aide et Astuces

Intégrez le bouton de lancement de Cloud Shell aux fichiers Markdown en copiant ce qui suit :

```markdown
[![Launch Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "Launch Cloud Shell")](https://shell.azure.com)
```

Le code HTML suivant permet d’incorporer une fenêtre contextuelle Cloud Shell :
```html
<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><img alt="Launch Azure Cloud Shell" src="https://shell.azure.com/images/launchcloudshell.png"></a>
```

## <a name="customize-experience"></a>Personnaliser l’expérience

Définissez une expérience Cloud Shell spécifique en augmentant votre URL.

|Expérience   |URL   |
|---|---|
|Shell utilisé le plus récemment   |[shell.azure.com](https://shell.azure.com)           |
|Bash                       |[shell.azure.com/bash](https://shell.azure.com/bash)       |
|PowerShell                 |[shell.azure.com/powershell](https://shell.azure.com/powershell) |

## <a name="next-steps"></a>Étapes suivantes
[Démarrage rapide de Bash dans Cloud Shell](quickstart.md)<br>
[Démarrage rapide de PowerShell dans Cloud Shell](quickstart-powershell.md)
