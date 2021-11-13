---
title: Connecter Azure Percept DK sur 5G ou LTE à l’aide d’un modem Quectel RM500 5G
description: Cet article explique comment connecter Azure Percept DK par le biais de réseaux 5G ou LTE à l’aide d’un modem Quectel 5G.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/03/2021
ms.custom: template-how-to
ms.openlocfilehash: de4f889883b7c3da4e8129fc66d31aed094ebda6
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130252418"
---
# <a name="connect-azure-percept-dk-over-5g-or-lte-by-using-a-quectel-rm500-gl-5g-modem"></a>Connecter Azure Percept DK sur 5G ou LTE à l’aide d’un modem Quectel RM500-GL 5G  

Cet article explique comment connecter Azure Percept DK sur 5G ou LTE à l’aide d’un modem Quectel RM500-GL 5G. 

Pour plus d’informations sur ce kit de développement de modem 5G, contactez votre équipe de vente locale Quectel :

* Pour les clients en Amérique du Nord : northamerica-sales@quectel.com
* Pour les clients dans le reste du monde : sales@quectel.com

> [!Note] 
> **À propos des câbles USB pour les modems 5G** :  
> Les modems 5G requièrent plus d’énergie que les modems LTE, et un câble USB incorrect peut être un goulot d’étranglement pour l’obtention des meilleurs taux de données 5G. Pour fournir une alimentation suffisante et cohérente à un modem 5G, vérifiez que le câble USB répond aux normes suivantes :  
> **Puissance :**
> - L’intensité maximale doit être supérieure ou égale à 3 ampères
> - La longueur du câble doit être inférieure à un mètre
> - Lors de l’utilisation d’un modem 5G, un seul port USB sur Azure Percept DK doit être actif
>  
> **Débit :**
> - USB 3.1 Gen2
> - Certifié USB-IF

## <a name="prepare-to-connect-azure-percept-dk"></a>Préparer la connexion d’Azure Percept DK
Pour savoir comment préparer Azure Percept DK, accédez à [Connecter Azure Percept DK sur des réseaux 5G ou LTE à l’aide d’un modem USB](./connect-over-cellular-usb.md). Veillez à noter les commentaires concernant les câbles USB à utiliser. 

### <a name="prepare-the-modem"></a>Préparer le modem
Avant de commencer, votre modem doit être en mode MBIM (Mobile Broadband Interface Model). Pour ce faire, vous pouvez utiliser une commande non documentée mais standard Quectel Attention (AT) : `AT+QCFG="usbnet"`.

La propriété `usbnet` peut être définie sur quatre valeurs différentes, de `0` à `3` :
- `0` pour le **mode NDIS/PPP/QMI** (pris en charge par le pilote `qmi_wwan`, activé avec `CONFIG_USB_NET_QMI_WWAN=y|m` )
- `1` pour le **mode Ethernet CDC** (pris en charge dans Linux lorsque `CONFIG_USB_NET_CDCETHER=y|m`)
- `2` pour le **mode MBIM** (pris en charge dans Linux lorsque `CONFIG_USB_NET_CDC_MBIM=y|m`)
- `3` pour le **mode RNDIS**

Le moyen le plus simple de configurer le mode consiste à connecter le modem Quectel 5G à un PC et à utiliser un logiciel terminal tel que TeraTerm ou le logiciel PC propre à Quectel, QCOM. Vous pouvez utiliser le Gestionnaire de périphériques de Windows pour vérifier le port USB affecté au modem. Les paramètres de port COM doivent être les suivants :
* **Vitesse (en bauds)**  : 115 200
* **Bits d’arrêt** : 1
* **Parité** : Aucune
* **Taille en octets**: 8
* **Flux de contrôle** : Aucun flux de contrôle

Voici les commandes AT :

Pour vérifier dans quel mode USB le périphérique Quectel s’exécute actuellement, utilisez :

```
AT+QCFG="usbnet"
```

Pour passer en mode 2, utilisez :

```
AT+QCFG="usbnet",2
```

Si vous vérifiez à nouveau à l’aide de la première commande AT, vous devez obtenir ce qui suit :

```
+QCFG: "usbnet",2`
```

Une fois que vous avez défini le mode USB correct, émettez une réinitialisation matérielle avec :

```
AT+CFUN=1,1
```

À ce stade, le modem doit se déconnecter et se reconnecter ultérieurement au port USB.


## <a name="use-the-modem-to-connect"></a>Utiliser le modem pour se connecter

1. Placez une carte SIM dans le modem Quectel.

1. Branchez le modem Quectel dans le port USB de l’Azure Percept DK. Veillez à utiliser un câble USB approprié.

1. Mettez l’Azure Percept DK sous tension.

1. Vérifiez que ModemManager est en cours d’exécution.

      ```
      systemctl status ModemManager
      ```
   
      Si tout est correct, vous obtiendrez un résultat similaire à ce qui suit :

      ```
      * ModemManager.service - Modem Manager
         Loaded: loaded (/lib/systemd/system/ModemManager.service; enabled; vendor pre set: enabled)
         Active: active (running) since Mon 2021-08-09 20:52:03 UTC; 23s ago
      [...]
      ```

      Dans le cas contraire, vérifiez que vous avez bien flashé l’image correcte sur Azure Percept DK (5G activé).

1. Listez les modems actifs.

      Lorsque vous listerez les modems, vous verrez que le modem Quectel a été reconnu et qu’il est maintenant géré par ModemManager.

      ```
      mmcli --list-modems
      ```
   
      Vous devez obtenir un résultat similaire à ce qui suit :

      ```
      /org/freedesktop/ModemManager1/Modem/0 [Quectel] RM500Q-GL
      ```

      L’ID de modem ici est `0` ; il est utilisé dans les commandes suivantes à des fins de résolution (c’est-à-dire `--modem 0`).

1. Récupérez les détails du modem.

      Par défaut, le modem est désactivé (`Status -> state: disabled`). Pour afficher l’état, exécutez :

      ```
      mmcli --modem 0
      ```
      
      Vous devez obtenir un résultat similaire à ce qui suit :

      ```
      General  |                    path: /org/freedesktop/ModemManager1/Modem/0
               |               device id: 8e3fb84e3755524d25dfa6f3f1943dc568958a2b
      -----------------------------------
      Hardware |            manufacturer: Quectel
               |                   model: RM500Q-GL
               |       firmware revision: RM500QGLABR11A04M4G
               |          carrier config: CDMAless-Verizon
               | carrier config revision: 0A010126
               |            h/w revision: RM500Q-GL
               |               supported: gsm-umts, lte, 5gnr
               |                 current: gsm-umts, lte, 5gnr
               |            equipment id: xxxx
      -----------------------------------
      System   |                  device: /sys/devices/platform/soc@0/38200000.usb/xhci-hcd.1.auto/usb4/4-1/4-1.1
               |                 drivers: option, cdc_mbim
               |                  plugin: quectel
               |            primary port: cdc-wdm0
               |                   ports: cdc-wdm0 (mbim), ttyUSB0 (qcdm), ttyUSB1 (gps),
               |                          ttyUSB2 (at), ttyUSB3 (at), wwan0 (net)
      -----------------------------------
      Numbers  |                     own: +1xxxx
      -----------------------------------
      Status   |          unlock retries: sim-pin2 (3)
               |                   state: disabled
               |             power state: on
               |          signal quality: 0% (cached)
      -----------------------------------
      Modes    |               supported: allowed: 3g; preferred: none
               |                          allowed: 4g; preferred: none
               |                          allowed: 3g, 4g; preferred: 4g
               |                          allowed: 3g, 4g; preferred: 3g
               |                          allowed: 5g; preferred: none
               |                          allowed: 3g, 5g; preferred: 5g
               |                          allowed: 3g, 5g; preferred: 3g
               |                          allowed: 4g, 5g; preferred: 5g
               |                          allowed: 4g, 5g; preferred: 4g
               |                          allowed: 3g, 4g, 5g; preferred: 5g
               |                          allowed: 3g, 4g, 5g; preferred: 4g
               |                          allowed: 3g, 4g, 5g; preferred: 3g
               |                 current: allowed: 3g, 4g, 5g; preferred: 5g
      -----------------------------------
      Bands    |               supported: utran-1, utran-3, utran-4, utran-6, utran-5, utran-8,
               |                          utran-2, eutran-1, eutran-2, eutran-3, eutran-4, eutran-5, eutran-7,
               |                          eutran-8, eutran-12, eutran-13, eutran-14, eutran-17, eutran-18,
               |                          eutran-19, eutran-20, eutran-25, eutran-26, eutran-28, eutran-29,
               |                          eutran-30, eutran-32, eutran-34, eutran-38, eutran-39, eutran-40,
               |                          eutran-41, eutran-42, eutran-43, eutran-46, eutran-48, eutran-66,
               |                          eutran-71, utran-19
               |                 current: utran-1, utran-3, utran-4, utran-6, utran-5, utran-8,
               |                          utran-2, eutran-1, eutran-2, eutran-3, eutran-4, eutran-5, eutran-7,
               |                          eutran-8, eutran-12, eutran-13, eutran-14, eutran-17, eutran-18,
               |                          eutran-19, eutran-20, eutran-25, eutran-26, eutran-28, eutran-29,
               |                          eutran-30, eutran-32, eutran-34, eutran-38, eutran-39, eutran-40,
               |                          eutran-41, eutran-42, eutran-43, eutran-46, eutran-48, eutran-66,
               |                          eutran-71, utran-19
      -----------------------------------
      IP       |               supported: ipv4, ipv6, ipv4v6
      -----------------------------------
      3GPP     |                    imei: xxxxxxxxxxxxxxx
               |           enabled locks: fixed-dialing
      -----------------------------------
      3GPP EPS |    ue mode of operation: csps-1
               |      initial bearer apn: ims
               |  initial bearer ip type: ipv4v6
      -----------------------------------
      SIM      |        primary sim path: /org/freedesktop/ModemManager1/SIM/0
      ```

1. Activez le modem.

      Avant d’établir une connexion, activez la ou les radios du modem en exécutant :

      ```
      mmcli --modem 0 --enable
      ```

      Vous devez obtenir une réponse similaire à ce qui suit :

      ```
      successfully enabled the modem
      ```
   
      Après un certain temps, le modem doit s’inscrire auprès d’une antenne, et l’état du modem doit basculer à `Status -> state: registered` une fois que vous avez exécuté le code suivant :
   
      ```
      mmcli --modem 0
      ```

1. Connectez-vous à l’aide des informations de nom du point d’accès (APN).

      En règle générale, les modems indiquent l’APN à utiliser (voir les informations `3GPP EPS -> initial bearer APN`) afin que vous puissiez l’utiliser pour établir une connexion. Si le modem ne fournit pas d’APN, contactez votre fournisseur de téléphone portable pour connaître l’APN à utiliser. 
      
      Voici la commande ModemManager pour la connexion à l’aide de l’APN Verizon, par exemple : `APN=vzwinternet`.

      ```
      mmcli --modem 0 --simple-connect="apn=vzwinternet"
      ```

      Là encore, vous devez obtenir un résultat similaire à ce qui suit :

      ```
      successfully connected the modem
      ```

1. Récupérez l’état du modem.

      À présent, vous devez voir l’état `Status -> state: connected` et une nouvelle catégorie `Bearer` à la fin du message d’état.
 
      ```
      mmcli -m 0
      ```

      ```
      -----------------------------------
      General  |                    path: /org/freedesktop/ModemManager1/Modem/0
               |               device id: 8e3fb84e3755524d25dfa6f3f1943dc568958a2b
      -----------------------------------
      Hardware |            manufacturer: Quectel
               |                   model: RM500Q-GL
               |       firmware revision: RM500QGLABR11A04M4G
               |          carrier config: CDMAless-Verizon
               | carrier config revision: 0A010126
               |            h/w revision: RM500Q-GL
               |               supported: gsm-umts, lte, 5gnr
               |                 current: gsm-umts, lte, 5gnr
               |            equipment id: xxx
      -----------------------------------
      System   |                  device: /sys/devices/platform/soc@0/38200000.usb/xhci-hcd.1.auto/usb4/4-1/4-1.1
               |                 drivers: option, cdc_mbim
               |                  plugin: quectel
               |            primary port: cdc-wdm0
               |                   ports: cdc-wdm0 (mbim), ttyUSB0 (qcdm), ttyUSB1 (gps),
               |                          ttyUSB2 (at), ttyUSB3 (at), wwan0 (net)
      -----------------------------------
      Numbers  |                     own: +1xxxx
      -----------------------------------
      Status   |          unlock retries: sim-pin2 (3)
               |                   state: connected
               |             power state: on
               |             access tech: lte
               |          signal quality: 12% (recent)
      -----------------------------------
      Modes    |               supported: allowed: 3g; preferred: none
               |                          allowed: 4g; preferred: none
               |                          allowed: 3g, 4g; preferred: 4g
               |                          allowed: 3g, 4g; preferred: 3g
               |                          allowed: 5g; preferred: none
               |                          allowed: 3g, 5g; preferred: 5g
               |                          allowed: 3g, 5g; preferred: 3g
               |                          allowed: 4g, 5g; preferred: 5g
               |                          allowed: 4g, 5g; preferred: 4g
               |                          allowed: 3g, 4g, 5g; preferred: 5g
               |                          allowed: 3g, 4g, 5g; preferred: 4g
               |                          allowed: 3g, 4g, 5g; preferred: 3g
               |                 current: allowed: 3g, 4g, 5g; preferred: 5g
      -----------------------------------
      Bands    |               supported: utran-1, utran-3, utran-4, utran-6, utran-5, utran-8,
               |                          utran-2, eutran-1, eutran-2, eutran-3, eutran-4, eutran-5, eutran-7,
               |                          eutran-8, eutran-12, eutran-13, eutran-14, eutran-17, eutran-18,
               |                          eutran-19, eutran-20, eutran-25, eutran-26, eutran-28, eutran-29,
               |                          eutran-30, eutran-32, eutran-34, eutran-38, eutran-39, eutran-40,
               |                          eutran-41, eutran-42, eutran-43, eutran-46, eutran-48, eutran-66,
               |                          eutran-71, utran-19
               |                 current: utran-1, utran-3, utran-4, utran-6, utran-5, utran-8,
               |                          utran-2, eutran-1, eutran-2, eutran-3, eutran-4, eutran-5, eutran-7,
               |                          eutran-8, eutran-12, eutran-13, eutran-14, eutran-17, eutran-18,
               |                          eutran-19, eutran-20, eutran-25, eutran-26, eutran-28, eutran-29,
               |                          eutran-30, eutran-32, eutran-34, eutran-38, eutran-39, eutran-40,
               |                          eutran-41, eutran-42, eutran-43, eutran-46, eutran-48, eutran-66,
               |                          eutran-71, utran-19
      -----------------------------------
      IP       |               supported: ipv4, ipv6, ipv4v6
      -----------------------------------
      3GPP     |                    imei: xxxxxxxxxxxxxxx
               |           enabled locks: fixed-dialing
               |             operator id: 311480
               |           operator name: Verizon
               |            registration: home
               |                     pco: 0: (partial) '27058000FF0100'

      -----------------------------------
      3GPP EPS |    ue mode of operation: csps-1
               |     initial bearer path: /org/freedesktop/ModemManager1/Bearer/0
               |      initial bearer apn: ims
               |  initial bearer ip type: ipv4v6
      -----------------------------------
      SIM      |        primary sim path: /org/freedesktop/ModemManager1/SIM/0
      -----------------------------------
      Bearer   |                   paths: /org/freedesktop/ModemManager1/Bearer/1

      ```

1. Récupérez les détails du porteur.

      Le porteur résultant de l’étape précédente, `--simple-connect`, se situe à l’emplacement suivant : `/org/freedesktop/ModemManager1/Bearer/1`.

      Il s’agit du porteur que nous interrogeons pour connaître les informations de modem relatives à la connexion active. Le porteur initial n’est pas attaché à une connexion active et, par conséquent, ne contient pas d’informations IP.

      ```
      mmcli --bearer 1
      ```

      ```
      --------------------------------
      General            |       path: /org/freedesktop/ModemManager1/Bearer/1
                           |       type: default
      --------------------------------
      Status             |  connected: yes
                           |  suspended: no
                           |  interface: wwan0
                           | ip timeout: 20
      --------------------------------
      Properties         |        apn: fast.t-mobile.com
                           |    roaming: allowed
      --------------------------------
      IPv4 configuration |     method: static
                           |    address: 25.21.113.165
                           |     prefix: 30
                           |    gateway: 25.21.113.166
                           |        dns: 10.177.0.34, 10.177.0.210
                           |        mtu: 1500
      --------------------------------
      Statistics         |   attempts: 1
      ```

      Voici les descriptions de certains détails clés :
      - `Status -> interface: wwan0` : liste l’interface réseau Linux qui correspond à ce modem.
      - `IPv4 configuration` : fournit la configuration IP que l’interface précédente doit définir pour qu’elle soit utilisable.

1. Vérifiez l’état de l’interface réseau du modem.

      Par défaut, l’interface réseau affiche `DOWN`.

      ```
      ip link show dev wwan0
      ```

      Vous devez obtenir un résultat similaire à ce qui suit :

      ```
      4: wwan0: <BROADCAST,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
         link/ether ce:92:c2:b8:1e:f2 brd ff:ff:ff:ff:ff:ff
      ```

1. Affichez l’interface.

      ```
      sudo ip link set dev wwan0 up
      ```

1. Vérifiez les informations IP.

      Par défaut, l’interface affiche `UP,LOWER_UP`, sans informations IP.

      ```
      sudo ip address show dev wwan0
      ```

      ```
      4: wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 1000
         link/ether ce:92:c2:b8:1e:f2 brd ff:ff:ff:ff:ff:ff
         inet6 fe80::cc92:c2ff:feb8:1ef2/64 scope link 
            valid_lft forever preferred_lft forever
      ```

1. Émettez une requête DHCP.

      Cette fonctionnalité est propre au module Quectel, mais ne se limite pas à celui-ci. Les informations IP doivent généralement être définies manuellement sur l’interface ou via un démon gestionnaire réseau qui prend en charge ModemManager (par exemple NetworkManager), mais ici vous pouvez simplement utiliser dhclient sur le modem Quectel :

      ```
      sudo dhclient wwan0
      ```

1. Vérifiez les informations IP.

      La configuration IP de cette interface doit correspondre aux détails du porteur ModemManager.

      ```
      sudo ip address show dev wwan0
      ```

      Vous devez obtenir un résultat similaire à ce qui suit :

      ```
      4: wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 1000
         link/ether ce:92:c2:b8:1e:f2 brd ff:ff:ff:ff:ff:ff
         inet 25.21.113.165/30 brd 25.21.113.167 scope global wwan0
            valid_lft forever preferred_lft forever
         inet6 fe80::cc92:c2ff:feb8:1ef2/64 scope link 
            valid_lft forever preferred_lft forever
      ```

1. Vérifiez les routes de l’interface.

      Notez que le client DHCP définit également une route par défaut pour que les paquets passent par l’interface `wwan0`.

      ```
      ip route show dev wwan0
      ```

      Vous devez obtenir un résultat similaire à ce qui suit :

      ```
      default via 25.21.113.166 
      25.21.113.164/30 proto kernel scope link src 25.21.113.165
      ```

      Vous avez maintenant établi une connexion à Azure Percept DK à l’aide du modem Quectel.


1. Testez la connectivité.

      Exécutez une requête `ping` par le biais de l’interface `wwan0`.

      ```
      ping -I wwan0 8.8.8.8
      ```

      Vous devez obtenir un résultat similaire à ce qui suit :

      ```
      PING 8.8.8.8 (8.8.8.8) from 25.21.113.165 wwan0: 56(84) bytes of data.
      64 bytes from 8.8.8.8: icmp_seq=1 ttl=114 time=137 ms
      64 bytes from 8.8.8.8: icmp_seq=2 ttl=114 time=114 ms
      ^C
      --- 8.8.8.8 ping statistics ---
      2 packets transmitted, 2 received, 0% packet loss, time 2ms
      rtt min/avg/max/mdev = 113.899/125.530/137.162/11.636 ms
      ```

## <a name="debugging"></a>Débogage

Pour obtenir des informations générales sur le débogage, consultez [Se connecter à l’aide d’un modem USB](./connect-over-cellular-usb.md).

## <a name="next-steps"></a>Étapes suivantes

En fonction de l’appareil mobile auquel vous avez accès, vous pouvez vous connecter de l’une des deux manières suivantes :

* [Établir une connexion avec un modem USB](./connect-over-cellular-usb.md)
* [Établir une connexion 5G ou LTE](./connect-over-cellular.md)
