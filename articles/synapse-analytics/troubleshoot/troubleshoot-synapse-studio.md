---
title: Résoudre les problèmes liés à Synapse Studio
description: Résoudre les problèmes liés à Synapse Studio
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: troubleshooting
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: fd560856ab087727d73317eaef5de01950281db9
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399469"
---
# <a name="synapse-studio-troubleshooting"></a>Résolution des problèmes liés à Synapse Studio

Ce guide de résolution des problèmes fournit des instructions sur les informations à fournir lors de l’ouverture d’un ticket de support concernant des problèmes de connectivité réseau. Avec les informations appropriées, nous pouvons peut-être résoudre le problème plus rapidement.

## <a name="serverless-sql-pool-service-connectivity-issue"></a>Problème de connectivité avec le service Pool SQL serverless

### <a name="symptom-1"></a>Symptôme 1

L’option « Pool SQL serverless » est grisée dans la liste déroulante « Se connecter à ».

![Symptôme 1](media/troubleshooting-synapse-studio/symptom1v2.png)

### <a name="symptom-2"></a>Symptôme 2

L’exécution de la requête contenant « Pool SQL serverless » génère le message d’erreur « Échec de l’établissement de la connexion au serveur ».

![Symptôme 2](media/troubleshooting-synapse-studio/symptom2.png)
 

### <a name="troubleshooting-steps"></a>Étapes de dépannage

> [!NOTE] 
>    Les étapes de résolution des problèmes suivantes sont destinées à Chromium Edge et Chrome. Vous pouvez utiliser d’autres navigateurs (tels que FireFox) avec les mêmes étapes de résolution des problèmes, mais la disposition de la fenêtre « Outil développeur » peut différer des captures d’écran de ce guide de résolution des problèmes. Autant que possible, N’UTILISEZ PAS la version classique de Edge pour la résolution des problèmes, car elle peut afficher des informations inexactes dans certaines situations.

Ouvrez le panneau « Informations de diagnostic », puis sélectionnez le bouton « Télécharger le diagnostic ». Conservez les informations téléchargées pour le signalement d’erreurs. Au lieu de cela, vous pouvez copier l’« ID de session » et l’attacher lors de l’ouverture du ticket de support.

![infos-diagnostic](media/troubleshooting-synapse-studio/diagnostic-info-download.png)

Pour commencer la résolution des problèmes, réessayez l’opération que vous avez effectuée dans Synapse Studio.

- Pour le symptôme 1, sous l’onglet « Script SQL », sélectionnez le bouton « Actualiser » à droite de la liste déroulante « Utiliser une base de données », et vérifiez si vous pouvez voir « pool SQL serverless ».
- Pour le symptôme 2, essayez de réexécuter la requête pour voir si elle fonctionne correctement.

Si le problème persiste, appuyez sur F12 dans votre navigateur pour ouvrir « Outils de développement » (DevTools).

Dans la fenêtre « Outils de développement », basculez vers le panneau « Réseau ». Sélectionnez le bouton « Effacer » dans la barre d’outils du panneau « Réseau », si nécessaire.
Assurez-vous que l’option « Désactiver le cache » dans le panneau « Réseau » est activée.

Réessayez l’opération que vous avez effectuée dans Azure Synapse Studio. Vous pouvez voir les nouveaux éléments affichés dans la liste « Réseau » dans « Outils de développement ». Notez votre heure système actuelle à fournir dans le ticket de support.

![panneau-réseau 1](media/troubleshooting-synapse-studio/network-panel.png)

Recherchez l’élément dont la colonne URL correspond au modèle suivant :

`https://[*A*]-ondemand.database.windows.net:1443/databases/[*B*]/query?api-version=2018-08-01-preview&application=ArcadiaSqlOnDemandExplorer`

Où [*A*] est le nom de votre espace de travail, « -ondemand » pourrait être « -sqlod », et où [*B*] doit être un nom de base de données tel que « master ». Il doit y avoir au plus deux éléments avec la même valeur d’URL, mais des valeurs de méthode différentes : OPTIONS et POST. Vérifiez si ces deux éléments contiennent « 200 » ou « 20x » dans la colonne d’état, où « x » peut être n’importe quel chiffre unique.

Si l’un d’eux autre chose que « 20x », et que :

- l’état commence par « (failed) », élargissez la colonne « État » ou pointez sur le texte de l’état pour le voir en entier. Incluez le texte et/ou la capture d’écran lors de l’ouverture du ticket de support.

    ![texte d’état](media/troubleshooting-synapse-studio/status-text.png)

    - Si vous voyez ERR_NAME_NOT_RESOLVED et avez créé votre espace de travail en 10 minutes, patientez 10 minutes, puis réessayez pour voir si le problème persiste.
    - Si vous voyez ERR_INTERNET_DISCONNECTED ou ERR_NETWORK_CHANGED, cela peut indiquer que la connexion réseau de votre PC rencontre des problèmes. Vérifiez votre connexion réseau, puis retentez l’opération.
    - Si vous voyez ERR_CONNECTION_RESET, ERR_SSL_PROTOCOL_ERROR ou d’autres codes d’erreur contenant « SSL », cela peut indiquer que votre configuration SSL locale rencontre des problèmes ou que votre administrateur réseau a bloqué l’accès au serveur du pool SQL serverless. Ouvrez un ticket de support et joignez le code d’erreur dans la description.
    - Si vous voyez ERR_NETWORK_ACCESS_DENIED, vous devrez peut-être vérifier auprès de l’administrateur si la stratégie de votre pare-feu local a bloqué l’accès au domaine *.database.windows.net ou au port distant 1443.
    - Vous pouvez tenter la même opération immédiatement sur un autre ordinateur et/ou un autre environnement réseau pour écarter tout problème de configuration réseau sur votre PC.

- l’état est « 40x », « 50 » ou un autre nombre, sélectionnez le ou les éléments pour afficher les détails. Vous devez voir les détails de l’élément à droite. Recherchez la section « Response Header », puis vérifiez s’il existe un élément nommé « access-control-allow-origin ». Si c’est le cas, vérifiez s’il a l’une des valeurs suivantes :

    - `*` (astérisque unique)
    - https://web.azuresynapse.net/ (ou autre valeur par laquelle commence le texte dans la barre d’adresse de votre navigateur)

Si l’en-tête de réponse contient l’une des valeurs ci-dessus, cela signifie que nous devrions avoir déjà collecté les informations d’échec. Vous pouvez ouvrir un ticket de support si nécessaire et éventuellement joindre la capture d’écran des détails de l’élément.

Si vous ne voyez pas l’en-tête ou si l’en-tête n’a pas l’une des valeurs mentionnées ci-dessus, joignez une capture d’écran des détails de l’élément lorsque vous ouvrez le ticket.

 
![détails de l’élément](media/troubleshooting-synapse-studio/item-details.png)
 
Si les étapes ci-dessus ne résolvent pas votre problème, vous devrez peut-être ouvrir un ticket de support. Lorsque vous soumettez votre ticket de support, incluez l’« ID de session » ou les « informations de diagnostic » téléchargés au début de ce guide.

Lorsque vous signalez le problème, vous pouvez éventuellement prendre une capture d’écran de l’onglet « console » dans « Outils de développement » et l’attacher au ticket de support. Faites défiler le contenu et prenez plusieurs captures d’écran si nécessaire pour capturer l’intégralité du message.

![console d’outils de développement](media/troubleshooting-synapse-studio/developer-tool-console.png)

Si vous joignez des captures d’écran, indiquez l’heure (ou un intervalle de temps estimé) à laquelle vous avez pris les captures d’écran. Cela nous aidera à examiner le problème.

Certains navigateurs prennent en charge l’affichage des horodatages sous l’onglet « Console ». Pour Chromium Edge/Chrome, ouvrez la boîte de dialogue « Paramètres » dans « Outils de développement », puis vérifiez « Afficher les horodateurs » sous l’onglet « Préférences ».

![paramètres de la console d’outils de développement](media/troubleshooting-synapse-studio/developer-tool-console-settings.png)

![afficher l’horodatage](media/troubleshooting-synapse-studio/show-time-stamp.png)

## <a name="notebook-websocket-connection-issue"></a>Problème de connexion du notebook avec WebSocket

### <a name="symptom"></a>Symptôme
Le message d’erreur suivant s’affiche : la connexion du notebook s’est fermée de manière inattendue. Pour rétablir la connexion, réexécutez le notebook. Informations de diagnostic : websocket_close_error (ID de corrélation) 

![Problème de connexion du notebook avec WebSocket](media/troubleshooting-synapse-studio/notebook-websocket-connection-issue.png)

### <a name="root-cause"></a>Cause racine : 
L’exécution du notebook dépend de l’établissement d’une connexion WebSocket à l’URL suivante 
``` 
wss://{workspace}.dev.azuresynapse.net/jupyterApi/versions/1/sparkPools/{spark-pool}/api/kernels/{kernel-id}/channels 
``` 

+ **{workspace}** est le nom de l’espace de travail Synapse, 
+ **{spark-pool}** est le nom du pool Spark que vous utilisez actuellement, 
+ **{kernel-id}** est un identificateur unique (GUID) utilisé pour distinguer les sessions de notebook. 

Lors de la configuration de la connexion WebSocket, Synapse Studio inclut un jeton d’accès (jeton du porteur JWT) dans l’en-tête Sec-WebSocket-Protocol de la requête WebSocket. 

Parfois, la requête WebSocket peut être bloquée, ou le jeton JWT dans l’en-tête de la requête peut être rédigé dans votre environnement réseau. Le notebook Synapse n’est alors pas en mesure d’établir la connexion à notre serveur et d’exécuter votre notebook. 

### <a name="action"></a>Action : 

Si possible, essayez de basculer votre environnement réseau, par exemple en passant à l’intérieur/l’extérieur du réseau d’entreprise ou d’accéder au notebook Synapse sur une autre station de travail. 

+ Si vous pouvez exécuter le notebook sur la même station de travail, mais dans un autre environnement réseau, contactez votre administrateur réseau pour déterminer si la connexion WebSocket a été bloquée. 

+ Si vous pouvez exécuter le bloc-notes sur une autre station de travail, mais dans le même environnement réseau, vérifier si vous n’avez pas installé un plug-in de navigateur qui pourrait bloquer la requête WebSocket. 

Dans le cas contraire, contactez votre administrateur réseau et vérifiez que les demandes WebSocket sortantes avec le modèle d’URL suivant sont autorisées et que l’en-tête de la requête n’est pas rédigé : 

``` 
wss://{workspace}.dev.azuresynapse.net/{path} 
``` 
+ **{workspace}** est le nom de l’espace de travail Synapse ; 

+ **{path}** indique tout sous-chemin dans l’URI (par exemple un caractère barre oblique est inclus). 

Ce modèle d’URL est plus souple que celui indiqué dans la section « Cause racine », car il permet d’ajouter de nouvelles fonctionnalités dépendantes de WebSocket à Synapse, sans aucun problème de connectivité potentiel à l’avenir. 


## <a name="next-steps"></a>Étapes suivantes
Si les étapes précédentes ne permettent pas de résoudre le problème, [créez un ticket de support](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)
