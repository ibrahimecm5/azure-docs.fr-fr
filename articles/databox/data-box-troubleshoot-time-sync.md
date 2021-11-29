---
title: Résoudre des problèmes de synchronisation de l’heure pour des appareils Azure Data Box ou Azure Data Box Heavy
description: Décrit comment résoudre des problèmes de synchronisation de l’heure pour un appareil Azure Data Box ou Azure Data Box Heavy à l’aide de l’interface PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: troubleshooting
ms.date: 11/15/2021
ms.author: alkohli
ms.openlocfilehash: 8999c2873528dd5dd0fefc0b730f4856ac995649
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557342"
---
# <a name="sync-device-time-for-azure-data-box-and-azure-data-box-heavy"></a>Synchroniser l’heure d’un appareil Azure Data Box ou Azure Data Box Heavy

Cet article explique comment diagnostiquer le fait que votre appliance Data Box n’est pas synchronisée, puis modifier l’heure sur celle-ci. Les informations contenues dans cet article s’appliquent aux ordres d’importation et d’exportation sur des appareils Data Box et Data Box Heavy.


## <a name="about-device-time-sync"></a>À propos de la synchronisation de l’heure des appareils

La Data Box synchronise l’heure automatiquement quand elle est connectée à internet, en utilisant le serveur de temps Windows par défaut `time.windows.com`. Toutefois, si la Data Box n’est pas connectée à Internet, il se peut que sont heure ne soit pas synchronisée. Cette situation peut affecter la copie de données à partir des données sources vers la Data Box, en particulier si la copie s’effectue via l’API REST ou certains outils sujets à des contraintes de temps. 

Si vous constatez un écart entre l’heure de la Data Box et celle d’autres périphériques locaux sur votre site, vous pouvez synchroniser l’heure sur votre Data Box en accédant à son interface PowerShell. L’API Set-Date est utilisée pour modifier l’heure de l’appareil. Pour plus d’informations, consultez [API Sat-Date](/powershell/module/microsoft.powershell.utility/set-date?view=powershell-7.1&preserve-view=true).


## <a name="connect-to-powershell-interface"></a>Se connecter à l’interface PowerShell

Pour résoudre des problèmes de synchronisation de l’heure, vous devez commencer par vous connecter à l’interface PowerShell de votre appareil.

[!INCLUDE [Connect to Data Box PowerShell interface](../../includes/data-box-connect-powershell-interface.md)]


## <a name="change-device-time"></a>Modifier l’heure d’un appareil

Pour modifier l’heure d’un appareil, procédez comme suit.

1. Pour diagnostiquer si l’heure de l’appareil est désynchronisée, commencez par récupérer l’heure de l’appareil. Utilisez la cmdlet `Get-Date` pour afficher la date et l’heure de votre Data Box.

    `Get-Date`

1. Si l’heure de l’appareil est désynchronisée, utilisez la cmdlet `Set-Date` pour modifier l’heure sur votre Data Box.

    - Avancez l’heure de 2 minutes.
    
        ```powershell
        Set-Date -Adjust <time change in hours:mins:secs format> -DisplayHint Time
        ```
    - Retardez l’heure de 2 minutes.

        ```powershell
        Set-Date -Adjust -<time change in hours:mins:secs format> -DisplayHint Time
        ```    

        Voici un exemple de sortie :
        
        ```powershell
        [by506b4b5d0790.microsoftdatabox.com]: PS>Get-date
        Friday, September 3, 2021 2:22:50 PM
        [by506b4b5d0790.microsoftdatabox.com]: PS>Set-Date -Adjust 00:02:00 -DisplayHint Time
        2:25:18 PM
        [by506b4b5d0790.microsoftdatabox.com]: PS>Set-Date -Adjust -00:02:00 -DisplayHint Time
        2:23:28 PM
        [by506b4b5d0790.microsoftdatabox.com]: PS>Get-date
        Friday, September 3, 2021 2:23:42 PM
        [by506b4b5d0790.microsoftdatabox.com]: PS>
        ```
        Pour plus d’informations, consultez [API Sat-Date](/powershell/module/microsoft.powershell.utility/set-date?view=powershell-7.1&preserve-view=true).
    
## <a name="next-steps"></a>Étapes suivantes

Pour résoudre d’autres problèmes de Data Box, consultez l’un des articles suivants :

- [Résoudre des erreurs liées au stockage Blob sur Data Box.](data-box-troubleshoot-rest.md)
- [Résoudre des erreurs de copie de données sur Data Box.](data-box-troubleshoot.md)
- [Résoudre des erreurs de chargement de données sur Data Box.](data-box-troubleshoot-data-upload.md)
