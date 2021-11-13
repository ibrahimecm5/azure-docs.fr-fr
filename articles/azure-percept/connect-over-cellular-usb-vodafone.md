---
title: Connecter Azure Percept DK par le biais de réseaux 5G et LTE à l’aide d’un modem USB Vodafone
description: Cet article explique comment connecter Azure Percept DK par le biais de réseaux 5G et LTE à l’aide d’un modem USB Vodafone.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 226fc6890253bd4701b400dcd42c420618701630
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130252361"
---
# <a name="connect-azure-percept-dk-over-5g-and-lte-by-using-a-vodafone-usb-connect-4g-v2-modem"></a>Connecter Azure Percept DK par le biais de réseaux 5G et LTE à l’aide d’un modem USB Vodafone Connect 4G v2

Cet article explique comment connecter Azure Percept DK à l’aide d’un modem Vodafone USB Connect 4G v2.

Pour plus d’informations sur ce modem, accédez à la page des [terminaux intégrés Vodafone](https://www.vodafone.com/business/iot/iot-devices/integrated-terminals).

## <a name="use-the-modem-to-connect"></a>Utiliser le modem pour se connecter

Avant de commencer, vérifiez que vous avez préparé Azure Percept DK pour la [connexion à l’aide d’un modem USB](./connect-over-cellular-usb.md). Aucune préparation n’est nécessaire pour le modem USB lui-même.   

1. Branchez une carte SIM dans le modem Vodafone.

1. Branchez le modem Vodafone dans le port USB A d’Azure Percept.

1. Mettez l’Azure Percept DK sous tension.

1. Connectez-vous à l’Azure Percept DK à l’aide du protocole réseau SSH (Secure Shell).

1. Vérifiez que ModemManager s’exécute en écrivant la commande suivante à votre invite SSH :

    ```
    systemctl status ModemManager
    ```

    Si tout est correct, vous obtiendrez un résultat similaire à ce qui suit :

    ```
    ModemManager.service - Modem Manager
    Loaded: loaded (/lib/systemd/system/ModemManager.service; enabled; vendor preset: enabled)
    Active: active (running) since Mon 2021-08-09 20:52:03 UTC; 23 s ago
    ```

1. Listez les modems actifs.

    Pour vérifier que ModemManager peut reconnaître votre modem, exécutez :

    ```
    mmcli --list-modems
    ```

    Vous devez obtenir un résultat similaire à ce qui suit : Ici, l’ID de modem est `0`, mais votre résultat peut être différent.

    ```
    /org/freedesktop/ModemManager1/Modem/0 [Alcatel] Mobilebroadband
    ```

1. Récupérez les détails du modem.

    Pour récupérer les détails de l’état du modem, exécutez la commande suivante (où l’ID du modem est `0`).

    ```
    mmcli --modem 0
    ```

    Par défaut, le modem est désactivé (`Status -> state: disabled`).

    ```
      --------------------------------
      General  |                 path: /org/freedesktop/ModemManager1/Modem/0
              |            device id: 20a6021958444bcb6f6589b47fd264932c340e69
      --------------------------------
      Hardware |         manufacturer: Alcatel
              |                model: Mobilebroadband
              |    firmware revision: MPSS.JO.2.0.2.c1.7-00004-9607_
              |       carrier config: default
              |         h/w revision: 0
              |            supported: gsm-umts, lte
              |              current: gsm-umts, lte
              |         equipment id: xxx
      --------------------------------
      System   |               device: /sys/devices/platform/soc@0/38200000.usb/xhci-hcd.1.auto/usb3/3-1/3-1.2
              |              drivers: option, cdc_mbim
              |               plugin: generic
              |         primary port: cdc-wdm0
              |                ports: cdc-wdm0 (mbim), ttyUSB0 (at), ttyUSB1 (qcdm), 
              |                       ttyUSB2 (at), wwan0 (net)
      --------------------------------
      Status   |       unlock retries: sim-pin2 (3)
              |                state: disabled
              |          power state: on
              |       signal quality: 0% (cached)
      --------------------------------
      Modes    |            supported: allowed: 2g; preferred: none
              |                       allowed: 3g; preferred: none
              |                       allowed: 4g; preferred: none
              |                       allowed: 2g, 3g; preferred: 3g
              |                       allowed: 2g, 3g; preferred: 2g
              |                       allowed: 2g, 4g; preferred: 4g
              |                       allowed: 2g, 4g; preferred: 2g
              |                       allowed: 3g, 4g; preferred: 4g
              |                       allowed: 3g, 4g; preferred: 3g
              |                       allowed: 2g, 3g, 4g; preferred: 4g
              |                       allowed: 2g, 3g, 4g; preferred: 3g
              |                       allowed: 2g, 3g, 4g; preferred: 2g
              |              current: allowed: 2g, 3g, 4g; preferred: 2g
      --------------------------------
      Bands    |            supported: egsm, dcs, pcs, g850, utran-4, utran-5, utran-2, eutran-2, 
              |                       eutran-4, eutran-5, eutran-7, eutran-12, eutran-13, eutran-71
              |              current: egsm, dcs, pcs, g850, utran-4, utran-5, utran-2, eutran-2, 
              |                       eutran-4, eutran-5, eutran-7, eutran-12, eutran-13, eutran-71
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

    Nous vous recommandons de commencer avec le paramètre par défaut : 
    
    `Modes: current: allowed: 2g, 3g, 4g; preferred: 2g`. 
    
    Si vous n’utilisez pas déjà ce paramètre, exécutez :
    
     `mmcli --modem 0 --set-allowed-modes='2g|3g|4g' --set-preferred-mode='2g'`.

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

    Votre fournisseur de téléphone portable fournit un APN, par exemple l’APN suivant pour Vodafone :

    ```
    mmcli --modem 0 --simple-connect="apn=internet4gd.gdsp"  
    ```

    Vous devriez obtenir une réponse du type « successfully connected the modem ».

1. Récupérez l’état du modem.

    À présent, vous devez voir l’état `Status -> state: connected` et une nouvelle catégorie `Bearer` à la fin du message d’état.

    ```
    mmcli --modem 0
    ```

    ```
      --------------------------------
      General  |                 path: /org/freedesktop/ModemManager1/Modem/0-mobile.
              |            device id: 20a6021958444bcb6f6589b47fd264932c340e69
      --------------------------------
      Hardware |         manufacturer: Alcatel
              |                model: Mobilebroadband
              |    firmware revision: MPSS.JO.2.0.2.c1.7-00004-9607_
              |       carrier config: default
              |         h/w revision: 0
              |            supported: gsm-umts, lte
              |              current: gsm-umts, lte
              |         equipment id: xxx
      --------------------------------
      System   |               device: /sys/devices/platform/soc@0/38200000.usb/xhci-hcd.1.auto/usb3/3-1/3-1.2
              |              drivers: option, cdc_mbim
              |               plugin: generic
              |         primary port: cdc-wdm0
              |                ports: cdc-wdm0 (mbim), ttyUSB0 (at), ttyUSB1 (qcdm), 
              |                       ttyUSB2 (at), wwan0 (net)
      --------------------------------
      Numbers  |                  own: xxx
      --------------------------------
      Status   |       unlock retries: sim-pin2 (10)
              |                state: connected
              |          power state: on
              |          access tech: lte
              |       signal quality: 19% (recent)
      --------------------------------
      Modes    |            supported: allowed: 2g; preferred: none
              |                       allowed: 3g; preferred: none
              |                       allowed: 4g; preferred: none
              |                       allowed: 2g, 3g; preferred: 3g
              |                       allowed: 2g, 3g; preferred: 2g
              |                       allowed: 2g, 4g; preferred: 4g
              |                       allowed: 2g, 4g; preferred: 2g
              |                       allowed: 3g, 4g; preferred: 4g
              |                       allowed: 3g, 4g; preferred: 3g
              |                       allowed: 2g, 3g, 4g; preferred: 4g
              |                       allowed: 2g, 3g, 4g; preferred: 3g
              |                       allowed: 2g, 3g, 4g; preferred: 2g
              |              current: allowed: 2g, 3g, 4g; preferred: 2g
      --------------------------------
      Bands    |            supported: egsm, dcs, pcs, g850, utran-4, utran-5, utran-2, eutran-2, 
              |                       eutran-4, eutran-5, eutran-7, eutran-12, eutran-13, eutran-71
              |              current: egsm, dcs, pcs, g850, utran-4, utran-5, utran-2, eutran-2, 
              |                       eutran-4, eutran-5, eutran-7, eutran-12, eutran-13, eutran-71
      --------------------------------
      IP       |            supported: ipv4, ipv6, ipv4v6
      --------------------------------
      3GPP     |                 imei: xxxxxxxxxxxxxxx
              |        enabled locks: fixed-dialing
              |          operator id: 302220
              |        operator name: TELUS
              |         registration: roaming
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
      --------------------------------
      General            |       path: /org/freedesktop/ModemManager1/Bearer/0
                        |       type: default
      --------------------------------
      Status             |  connected: yes
                        |  suspended: no
                        |  interface: wwan0
                        | ip timeout: 20
      --------------------------------
      Properties         |        apn: internet4gd.gdsp
                        |    roaming: allowed
      --------------------------------
      IPv4 configuration |     method: static
                        |    address: 162.177.2.0
                        |     prefix: 22
                        |    gateway: 162.177.2.1
                        |        dns: 10.177.0.34, 10.177.0.210
                        |        mtu: 1500
      --------------------------------
      Statistics         |   attempts: 1
    ```

1. Ouvrez l’interface réseau.

    ```
    sudo ip link set dev wwan0 up
    ```

1. Configurez l’interface réseau.

    En utilisant les informations fournies par le porteur, remplacez l’adresse IP (par exemple, nous utilisons ici 162.177.2.0/22) par celle de votre porteur :

    ```
    sudo ip address add 162.177.2.0/22 dev wwan0
    ```

1. Vérifiez les informations IP.

    La configuration IP de cette interface doit correspondre aux détails du porteur ModemManager. Exécutez :

    ```
    sudo ip address show dev wwan0
    ```

    L’adresse IP du porteur est listée comme indiqué ici :

    ```
    wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 1000
        link/ether c2:12:44:c4:27:3c brd ff:ff:ff:ff:ff:ff
        inet 162.177.2.0/22 scope global wwan0
          valid_lft forever preferred_lft forever
        inet6 fe80::c012:44ff:fec4:273c/64 scope link 
          valid_lft forever preferred_lft forever
    ```

1. Définissez la route par défaut.

    Là encore, à l’aide des informations fournies par le porteur et en utilisant la passerelle du modem (remplacez 162.177.2.1) comme destination par défaut des paquets réseau, exécutez :

    ```
    sudo ip route add default via 162.177.2.1 dev wwan0
    ```

    Azure Percept DK est maintenant activé pour la connexion à Azure à l’aide du modem LTE.


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
   
### <a name="vodafone-modem-rules-to-mitigate-enumeration-issues"></a>Règles de modem Vodafone pour résoudre les problèmes d’énumération

Afin d’empêcher le modem d’effectuer une énumération dans un mode non pris en charge, nous vous suggérons d’appliquer les règles userspace/dev (udev) ci-dessous afin que ModemManager ignore les interfaces indésirables.

Créez un fichier */usr/lib/udev/rules.d/77-mm-vodafone-port-types.rules* avec le contenu suivant :

```
ACTION!="add|change|move|bind", GOTO="mm_vodafone_port_types_end"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="1bbb", GOTO="mm_vodafone_generic_vendorcheck"
GOTO="mm_vodafone_port_types_end"

LABEL="mm_vodafone_generic_vendorcheck"
SUBSYSTEMS=="usb", ATTRS{bInterfaceNumber}=="?*", ENV{.MM_USBIFNUM}="$attr{bInterfaceNumber}"

# Interface 1 is QDCM (ignored) and interfaces 3 and 4 are MBIM Control and Data.
ATTRS{idVendor}=="1bbb", ATTRS{idProduct}=="00b6", ENV{.MM_USBIFNUM}=="00", ENV{ID_MM_PORT_TYPE_AT_PRIMARY}="1"
ATTRS{idVendor}=="1bbb", ATTRS{idProduct}=="00b6", ENV{.MM_USBIFNUM}=="01", ENV{ID_MM_PORT_IGNORE}="1"
ATTRS{idVendor}=="1bbb", ATTRS{idProduct}=="00b6", ENV{.MM_USBIFNUM}=="02", ENV{ID_MM_PORT_AT_SECONDARY}="1"

GOTO="mm_vodafone_port_types_end"

LABEL="mm_vodafone_port_types_end"
```

Une fois les règles udev installées, rechargez-les et redémarrez ModemManager :

```
udevadm control -R
systemctl restart ModemManager
```

## <a name="next-steps"></a>Étapes suivantes

En fonction de l’appareil mobile auquel vous avez accès, vous pouvez vous connecter de l’une des deux manières suivantes :

* [Établir une connexion avec un modem USB](./connect-over-cellular-usb.md)
* [Établir une connexion 5G ou LTE](./connect-over-cellular.md)
