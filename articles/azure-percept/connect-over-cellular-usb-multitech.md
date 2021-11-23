---
title: Connecter Azure Percept DK sur LTE à l’aide d’un modem USB MultiTech MultiConnect
description: Cet article explique comment connecter Azure Percept DK via des réseaux 5G ou LTE à l’aide d’un modem USB MultiTech MultiConnect.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 5000c7be25a82069da0585a40252c91262a7ea12
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130252437"
---
# <a name="connect-azure-percept-dk-over-lte-by-using-a-multitech-multiconnect-usb-modem"></a>Connecter Azure Percept DK sur LTE à l’aide d’un modem USB MultiTech MultiConnect 

Cet article explique comment connecter votre Azure Percept DK à l’aide d’un modem USB MultiTech MultiConnect (MTCM-LNA3-B03). 

> [!Note]
> Il existe différents modèles de modem USB MultiTech MultiConnect. Dans cet article, nous avons utilisé le modèle LNA3, qui fonctionne entre autres avec les cartes SIM Verizon et Vodafone. À ce stade, nous ne pouvons pas nous connecter à un réseau AT&T, mais nous étudions le problème et nous mettrons à jour cet article si et quand nous trouvons la cause racine. Pour plus d’informations sur le modem USB Multitech MultiConnect, visitez le site de [MultiTech](https://www.multitech.com/brands/multiconnect-microcell).

## <a name="prepare-to-connect-azure-percept-dk"></a>Préparer la connexion d’Azure Percept DK
Pour savoir comment préparer Azure Percept DK, accédez à [Connecter Azure Percept DK sur des réseaux 5G ou LTE à l’aide d’un modem USB](./connect-over-cellular-usb.md). Veillez à noter les commentaires concernant les câbles USB à utiliser. 

### <a name="prepare-the-modem"></a>Préparer le modem
Avant de commencer, votre modem doit être en mode MBIM (Mobile Broadband Interface Model). Pour savoir comment préparer le modem, consultez le [guide de référence des commandes AT des solutions sans fil Telit](
https://www.telit.com/wp-content/uploads/2018/01/Telit-LE910-V2-Modules-AT-Commands-Reference-Guide-r3.pdf).

Dans cet article, pour activer l’interface MBIM, nous utilisons la commande AT `AT#USBCFG=<mode>` pour configurer le mode USB correct.

Le guide de référence des commandes AT liste tous les modes possibles, mais dans le cadre de cet article c’est le mode `3` qui nous intéresse. Le mode par défaut est `0`.

Le moyen le plus simple de configurer le mode consiste à connecter le modem MultiTech à un PC et à utiliser un logiciel de terminal comme TeraTerm ou PuTTY. Vous pouvez utiliser le Gestionnaire de périphériques de Windows pour vérifier le port USB affecté au modem. S’il y a plusieurs ports, vous devrez peut-être effectuer un test pour voir lequel répond aux commandes AT. Les paramètres de port COM doivent être les suivants :
* **Vitesse (en bauds)**  : 9 600 (ou 115 200)
* **Bits d’arrêt** : 1
* **Parité** : Aucune
* **Taille en octets**: 8
* **Flux de contrôle** : Aucun flux de contrôle

Voici les commandes AT :

Pour vérifier dans quel mode USB le périphérique Multitech s’exécute actuellement, utilisez :

```
AT#USBCFG?
```

Pour passer en mode 3, utilisez :

```
AT#USBCFG=3
```

Si vous vérifiez à nouveau à l’aide de la première commande AT, vous devez obtenir ce qui suit : `#USBCFG: 3`

Une fois que vous avez défini le mode USB correct, vous devez émettre une réinitialisation avec :

```
AT#REBOOT
```

À ce stade, le modem doit se déconnecter et se reconnecter ultérieurement au port USB en utilisant le mode précédemment défini.

## <a name="use-the-modem-to-connect"></a>Utiliser le modem pour se connecter

Assurez-vous d’avoir terminé les préparations de l’Azure Percept DK présentées dans l’article [Se connecter à l’aide d’un modem USB](./connect-over-cellular-usb.md).   

1. Insérez une carte SIM dans le modem MultiTech.

1. Branchez le modem MultiTech dans le port USB A de l’Azure Percept DK.

1. Mettez l’Azure Percept DK sous tension.

1. Connectez-vous à l’Azure Percept DK à l’aide du protocole réseau SSH (Secure Shell).

1. Vérifiez que ModemManager s’exécute en écrivant la commande suivante à votre invite SSH :

    ```
    systemctl status ModemManager
    ```
    Si tout est correct, vous obtiendrez un résultat similaire à ce qui suit :

    *ModemManager.service - Modem Manager* *Loaded: loaded (/lib/systemd/system/ModemManager.service; enabled; vendor preset: enabled)* *Active: active (running) since Mon 2021-08-09 20:52:03 UTC; 23 s ago*

1. Listez les modems actifs.

    Pour vérifier que ModemManager peut reconnaître votre modem, exécutez :

    ```
    mmcli --list-modems
    ```

    Vous devez obtenir un résultat similaire à ce qui suit :

    ```
    /org/freedesktop/ModemManager1/Modem/0 [Telit] FIH7160
    ```

1. Récupérez les détails du modem.

    L’ID de modem ici est `0`, mais votre résultat peut être différent. L’ID de modem (`--modem 0`) est utilisé dans les commandes ModemManager comme suit :
    
    ```
    mmcli --modem 0
    ```
    
    Par défaut, le modem est désactivé (`Status -> state: disabled`).

    ```
    --------------------------------
      General  |                 path: /org/freedesktop/ModemManager1/Modem/0
              |            device id: f89a480d73f1a9cfef28102a0b44be2a47329c8b
      --------------------------------
      Hardware |         manufacturer: Telit
              |                model: FIH7160
              |    firmware revision: 20.00.525
              |         h/w revision: XMM7160_V1.1_HWID437_MBIM_NAND
              |            supported: gsm-umts, lte
              |              current: gsm-umts, lte
              |         equipment id: xxxx
      --------------------------------
      System   |               device: /sys/devices/platform/soc@0/38200000.usb/xhci-hcd.1.auto/usb3/3-1/3-1.1
              |              drivers: cdc_acm, cdc_mbim
              |               plugin: telit
              |         primary port: cdc-wdm0
              |                ports: cdc-wdm0 (mbim), ttyACM1 (at), ttyACM2 (ignored),
              |                       ttyACM3 (ignored), ttyACM4 (at), ttyACM5 (ignored), ttyACM6 (ignored),
              |                       wwan0 (net)
      --------------------------------
      Status   |       unlock retries: sim-pin2 (3)
              |                state: disabled
              |          power state: on
              |       signal quality: 0% (cached)
      --------------------------------
      Modes    |            supported: allowed: 3g; preferred: none
              |                       allowed: 4g; preferred: none
              |                       allowed: 3g, 4g; preferred: none
              |              current: allowed: 3g, 4g; preferred: none
      --------------------------------
      Bands    |            supported: utran-5, utran-2, eutran-2, eutran-4, eutran-5, eutran-12,
              |                       eutran-13, eutran-17
              |              current: utran-2, eutran-2
      --------------------------------
      IP       |            supported: ipv4, ipv6, ipv4v6
      --------------------------------
      3GPP     |                 imei: xxxxxxxxxxxxxxx
              |        enabled locks: fixed-dialing
      --------------------------------
      3GPP EPS | ue mode of operation: csps-2
      --------------------------------
      SIM      |     primary sim path: /org/freedesktop/ModemManager1/SIM/0
    ```

1. Activez le modem.

    Avant d’établir une connexion, activez la ou les radios du modem en exécutant le code suivant :

    ```
    mmcli --modem 0 --enable
    ```

    Vous devriez obtenir une réponse du type « successfully enabled the modem ».

    Après un certain temps, le modem doit s’inscrire auprès d’une antenne, et l’état du modem doit basculer à `Status -> state: registered` une fois que vous avez exécuté le code suivant :

    ```
    mmcli --modem 0
    ```

1. Connectez-vous à l’aide des informations de nom du point d’accès (APN).

    Votre fournisseur de téléphone portable fournit un APN, par exemple l’APN suivant pour Verizon :

    ```
    mmcli --modem 0 --simple-connect="apn=vzwinternet"  
    ```

    Vous devriez obtenir une réponse du type « successfully enabled the modem ».

1. Récupérez l’état du modem.

    À présent, vous devez voir l’état `Status -> state: connected` et une nouvelle catégorie `Bearer` à la fin du message d’état.

    ```
    mmcli --modem 0
    ```

    ```
    --------------------------------
      General  |                 path: /org/freedesktop/ModemManager1/Modem/0
              |            device id: f89a480d73f1a9cfef28102a0b44be2a47329c8b
      --------------------------------
      Hardware |         manufacturer: Telit
              |                model: FIH7160
              |    firmware revision: 20.00.525
              |         h/w revision: XMM7160_V1.1_HWID437_MBIM_NAND
              |            supported: gsm-umts, lte
              |              current: gsm-umts, lte
              |         equipment id: xxxx
      --------------------------------
      System   |               device: /sys/devices/platform/soc@0/38200000.usb/xhci-hcd.1.auto/usb3/3-1/3-1.1
              |              drivers: cdc_acm, cdc_mbim
              |               plugin: telit
              |         primary port: cdc-wdm0
              |                ports: cdc-wdm0 (mbim), ttyACM1 (at), ttyACM2 (ignored),
              |                       ttyACM3 (ignored), ttyACM4 (at), ttyACM5 (ignored), ttyACM6 (ignored),
              |                       wwan0 (net)
      --------------------------------
      Numbers  |                  own: +1xxxxxxxx
      --------------------------------
      Status   |       unlock retries: sim-pin2 (3)
              |                state: connected
              |          power state: on
              |          access tech: lte
              |       signal quality: 16% (recent)
      --------------------------------
      Modes    |            supported: allowed: 3g; preferred: none
              |                       allowed: 4g; preferred: none
              |                       allowed: 3g, 4g; preferred: none
              |              current: allowed: 3g, 4g; preferred: none
      --------------------------------
      Bands    |            supported: utran-5, utran-2, eutran-2, eutran-4, eutran-5, eutran-12,
              |                       eutran-13, eutran-17
              |              current: utran-2, eutran-2
      --------------------------------
      IP       |            supported: ipv4, ipv6, ipv4v6
      --------------------------------
      3GPP     |                 imei: xxxxxxxxxxxxxxx
              |        enabled locks: fixed-dialing
              |          operator id: 311480
              |        operator name: Verizon
              |         registration: home
      --------------------------------
      3GPP EPS | ue mode of operation: csps-2
      --------------------------------
      SIM      |     primary sim path: /org/freedesktop/ModemManager1/SIM/0
      --------------------------------
      Bearer   |                paths: /org/freedesktop/ModemManager1/Bearer/0
    ```

1. Récupérez les détails du porteur.

    Vous avez besoin des détails du porteur pour connecter le système d’exploitation à la connexion de données de paquets que le modem a maintenant établie avec le réseau cellulaire. À ce stade, le modem a une connexion IP, mais le système d’exploitation n’est pas encore configuré pour l’utiliser.
  
    ```
    mmcli --bearer 0
    ```

    Les détails du porteur sont indiqués dans le code suivant :

    ```
    ------------------------------------
      General            |           path: /org/freedesktop/ModemManager1/Bearer/0
                        |           type: default
      ------------------------------------
      Status             |      connected: yes
                        |      suspended: no
                        |      interface: wwan0
                        |     ip timeout: 20
      ------------------------------------
      Properties         |            apn: vzwinternet
                        |        roaming: allowed
      ------------------------------------
      IPv4 configuration |         method: static
                        |        address: 100.112.107.46
                        |         prefix: 24
                        |        gateway: 100.112.107.1
                        |            dns: 198.224.166.135, 198.224.167.135
      ------------------------------------
      Statistics         |       duration: 119
                        |       attempts: 1
                        | total-duration: 119
    ```

1. Ouvrez l’interface réseau.

    ```
    sudo ip link set dev wwan0 up
    ```

1. Configurez l’interface réseau.

    En utilisant les informations fournies par le porteur, remplacez l’adresse IP (par exemple, nous utilisons ici 100.112.107.46/24) par celle de votre porteur :

    ```
    sudo ip address add 100.112.107.46/24 dev wwan0
    ```

1. Vérifiez les informations IP.

    La configuration IP de cette interface doit correspondre aux détails du porteur ModemManager. Exécutez :

    ```
    sudo ip address show dev wwan0
    ```

    L’adresse IP du porteur est listée comme indiqué ici :

    ```
    6: wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1428 qdisc pfifo_fast state UNKNOWN group default qlen 1000
        link/ether 1e:fb:08:e9:2a:25 brd ff:ff:ff:ff:ff:ff
        inet 100.112.107.46/24 scope global wwan0
          valid_lft forever preferred_lft forever
        inet6 fe80::1cfb:8ff:fee9:2a25/64 scope link
          valid_lft forever preferred_lft forever
    ```

1. Définissez la route par défaut.

    Là encore, à l’aide des informations fournies par le porteur et en utilisant la passerelle du modem (remplacez 100.112.107.1) comme destination par défaut des paquets réseau, exécutez :

    ```
    sudo ip route add default via 100.112.107.1 dev wwan0
    ```

    Azure Percept DK est maintenant connecté au modem USB.

1. Testez la connectivité.

    Dans cet article, vous exécutez une requête `ping` par le biais de l’interface `wwan0`. Vous pouvez également utiliser Azure Percept Studio et vérifier si les messages de télémétrie arrivent. Vérifiez que vous n’utilisez pas de câble Ethernet et que le Wi-Fi n’est pas activé, pour être certain que vous utilisez LTE. Exécutez :

    ```
    ping -I wwan0 8.8.8.8
    ```

    Vous devez obtenir un résultat similaire à ce qui suit :

    ```
    PING 8.8.8.8 (8.8.8.8) from 162.177.2.0 wwan0: 56(84) bytes of data.
    64 bytes from 8.8.8.8: icmp_seq=1 ttl=114 time=111 ms
    64 bytes from 8.8.8.8: icmp_seq=2 ttl=114 time=92.0 ms
    64 bytes from 8.8.8.8: icmp_seq=3 ttl=114 time=88.8 ms
    ^C
    --- 8.8.8.8 ping statistics ---
    3 packets transmitted, 3 received, 0% packet loss, time 4ms
    rtt min/avg/max/mdev = 88.779/97.254/110.964/9.787 ms
    ```


## <a name="debugging"></a>Débogage

Pour obtenir des informations générales sur le débogage, consultez [Se connecter à l’aide d’un modem USB](./connect-over-cellular-usb.md).

## <a name="next-steps"></a>Étapes suivantes

En fonction de l’appareil mobile auquel vous avez accès, vous pouvez vous connecter de l’une des deux manières suivantes :

* [Se connecter à l’aide d’un modem USB](./connect-over-cellular-usb.md)
* [Se connecter à l’aide de 5G ou LTE](./connect-over-cellular.md)
