---
title: Connecter Azure Percept DK par le biais de réseaux 5G et LTE à l’aide d’un modem USB
description: Cet article explique comment connecter Azure Percept DK par le biais de réseaux 5G et LTE à l’aide d’un modem USB.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 4bb866c25c81b08be7d4769d33b7d49b3ac6849f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131440016"
---
# <a name="connect-azure-percept-dk-over-5g-and-lte-networks-by-using-a-usb-modem"></a>Connecter Azure Percept DK par le biais de réseaux 5G et LTE à l’aide d’un modem USB

Cet article explique comment connecter Azure Percept DK à des réseaux 5G ou LTE à l’aide d’un modem USB. 

> [!NOTE]
> Les informations contenues dans cet article s’appliquent uniquement au logiciel spécial Azure Percept DK que vous pouvez télécharger en suivant les instructions de la section suivante. Une image Azure Percept DK spéciale comprend un logiciel open source ModemManager, qui prend en charge un large éventail de modems USB. L’image ne prend pas en charge les mises à jour OTA (over-the-air) du système d’exploitation ou d’autres logiciels. Avec le logiciel open source ModemManager, vous pouvez utiliser un modem réseau USB LTE simple et économique, ou des modems 5G plus sophistiqués, pour connecter Azure Percept DK à Internet et à Azure. 
>
> Les instructions de cet article sont destinées à être utilisées avec des modems USB qui prennent en charge une interface MBIM (Mobile Broadband Interface Model). Avant d’obtenir un modem USB, vérifiez qu’il prend en charge l’interface MBIM. Vérifiez également qu’il figure dans la liste ModemManager des modems pris en charge. Le logiciel ModemManager peut être utilisé avec d’autres interfaces, mais dans cet article nous nous concentrons sur l’interface MBIM. Pour plus d’informations, accédez à la page [freedesktop.org ModemManager](https://www.freedesktop.org/wiki/Software/ModemManager/).


:::image type="Image" source="media/connect-over-cellular/azure-percept-all-modems-v2.png" alt-text="Illustration d’Azure Percept DK utilisant des modems USB pour se connecter à des réseaux 5G et LTE.":::

## <a name="set-up-azure-percept-dk-to-use-a-usb-modem"></a>Configurer Azure Percept DK pour utiliser un modem USB

1. [Téléchargez l’image logicielle Azure Percept 5G](https://aka.ms/azpercept5gimage) qui prend en charge ModemManager. 

   Ces trois fichiers sont nécessaires pour mettre à jour votre logiciel Azure Percept DK afin de prendre en charge les modems USB.

1. [Mettez à jour le logiciel Azure Percept DK](./how-to-update-via-usb.md) avec le logiciel spécial 5G/LTE Azure Percept DK que vous avez téléchargé à l’étape précédente. 

   > [!IMPORTANT]
   > Suivez les instructions fournies dans [Mettre à jour Azure Percept DK via une connexion USB-C](./how-to-update-via-usb.md), mais veillez à utiliser *uniquement* les fichiers que vous avez téléchargés à l’étape précédente, et non les fichiers mentionnés dans l’article.

1. Suivez le processus normal pour [configurer l’appareil Azure Percept DK](./quickstart-percept-dk-set-up.md), si vous ne savez pas comment faire. 

   L’expérience de configuration n’est pas différente sur cette version compatible avec ModemManager d’Azure Percept DK.

1. [Connectez-vous à l’Azure Percept DK à l’aide du protocole réseau SSH (Secure Shell)](./how-to-ssh-into-percept-dk.md).

## <a name="connect-to-a-modem"></a>Se connecter à un modem

Les trois sections suivantes contiennent des instructions pour se connecter à différents modems USB.  

### <a name="vodafone-usb-connect-4g-v2-modem"></a>Modem Vodafone USB Connect 4G v2

Ce modem USB Vodafone est une simple clé USB LTE CAT-4 qui n’a pas de fonctionnalités spéciales. Les instructions pour ce modem peuvent être utilisées pour d’autres modems USB similaires, simples et rentables.

:::image type="Image" source="media/connect-over-cellular/vodafone-usb-modem-75.png" alt-text="Illustration des vues du dessus et du dessous d’un modem USB Vodafone 4G v2.":::

Pour obtenir des instructions sur la connexion de votre Azure Percept DK à l’aide d’un modem USB simple, tel que le modem USB Vodafone Connect 4G v2, consultez [Connecter Azure Percept sur LTE avec un modem Vodafone USB Connect 4G v2](./connect-over-cellular-usb-vodafone.md).   

### <a name="multitech-multiconnect-usb-modem"></a>Modem MultiTech Multiconnect USB

Ce modem USB Multitech offre plusieurs modes de fonctionnement USB. Pour ce type de modem, vous devez activer le mode USB approprié avant d’activer l’interface MBIM prise en charge par ModemManager.

:::image type="Image" source="media/connect-over-cellular/multitech-usb-modem-75.png" alt-text="Illustration du modem USB MultiTech Multiconnect.":::

Pour connecter Azure Percept DK à l’aide d’un modem USB simple, tel que le modem USB MultiTech (MTCM-LNA3-B03), suivez les instructions fournies dans [Se connecter à l’aide du modem USB MultiTech](./connect-over-cellular-usb-multitech.md).

### <a name="quectel-5g-developer-kit"></a>Kit de développement Quectel 5G

Le troisième modem est le Quectel 5G DK. Il offre également plusieurs modes, et vous devez d’abord activer le mode MBIM approprié.

:::image type="Image" source="media/connect-over-cellular/quectel-5-g-dk-75.png" alt-text="Illustration du modem USB Quectel 5G DK.":::

Pour obtenir des instructions de connexion de votre Azure Percept DK à l’aide d’un modem USB 5G, tel que Quectel RM500Q-GL, consultez [Se connecter à l’aide du kit de développement Quectel 5G](./connect-over-cellular-usb-quectel.md). 

## <a name="help-your-5g-or-lte-connection-recover-from-reboot"></a>Aider votre connexion 5G ou LTE à récupérer suite à un redémarrage 
Vous pouvez configurer le modem USB pour qu’il se connecte au réseau, mais si vous redémarrez votre appareil, vous devez vous reconnecter manuellement. Nous travaillons actuellement sur une solution pour améliorer cette expérience. Pour plus d’informations, contactez [notre équipe de support technique](mailto:azpercept5G@microsoft.com), en incluant une courte remarque mentionnant ce problème. 

## <a name="debugging-information"></a>Informations de débogage 
Vérifiez que votre carte SIM fonctionne sur le matériel spécifique que vous prévoyez d’utiliser. Plusieurs opérateurs limitent les cartes SIM IoT « données uniquement » à un fonctionnement sur un seul appareil. Pour cette raison, vérifiez que le numéro IMEI (International Mobile Equipment Identity) ou le numéro de série de votre appareil figure dans la liste des appareils autorisés de l’opérateur.

### <a name="modemmanager-debug-mode"></a>Mode débogage ModemManager

Vous pouvez activer le mode débogage ModemManager en modifiant le fichier */lib/systemd/system/ModemManager.service* sur la ligne `ExecStart=/usr/sbin/ModemManager [...]` en ajoutant `--debug`, comme indiqué dans l’exemple suivant :

```  
[...]  
ExecStart=/usr/sbin/ModemManager [...] --debug  
[...]  
```

Pour que vos modifications prennent effet, rechargez les services et redémarrez ModemManager, comme indiqué ici :

```
systemctl daemon-reload
systemctl restart ModemManager
```

En exécutant les commandes suivantes, vous pouvez afficher les journaux et nettoyer les fichiers journaux :

```
journalctl -u ModemManager.service
journalctl --rotate
journalctl --vacuum-time=1s

```

### <a name="enhance-reliability-and-stability"></a>Améliorer la fiabilité et la stabilité

Pour empêcher ModemManager d’interagir avec des interfaces série autres que celles du modem, vous pouvez limiter les interfaces à sonder (afin de déterminer quelles sont celles des modems) en modifiant les [stratégies de filtre](https://www.freedesktop.org/software/ModemManager/api/latest/ch03s02.html). 

Nous vous recommandons d’utiliser le mode `STRICT`.

Pour cela, modifiez le fichier */lib/systemd/system/ModemManager.service* sur la ligne `ExecStart=/usr/sbin/ModemManager [...]` en ajoutant `--filter-policy=STRICT`, comme indiqué dans l’exemple suivant :

```
[...]
ExecStart=/usr/sbin/ModemManager --filter-policy=STRICT
[...]
```
Pour que vos modifications prennent effet, rechargez les services et redémarrez ModemManager, comme indiqué ici :

```
systemctl daemon-reload
systemctl restart ModemManager
```

## <a name="next-steps"></a>Étapes suivantes

* [Établir une connexion 5G ou LTE](./connect-over-cellular.md)
* [Établir une connexion avec une passerelle mobile](./connect-over-cellular-gateway.md).
