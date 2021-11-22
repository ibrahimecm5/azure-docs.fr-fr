---
title: Protocoles pris en charge par Microsoft Defender pour IoT
description: Découvrez les protocoles pris en charge par Microsoft Defender pour IoT.
ms.date: 11/09/2021
ms.topic: article
ms.openlocfilehash: ebfd983baf2e925fda66c5fc23edc380420db591
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132305957"
---
# <a name="support-for-iot-ot-ics-and-scada-protocols"></a>Prise en charge des protocoles pour IoT, OT, ICS et SCADA

Microsoft Defender pour IoT offre une plateforme de cybersécurité de technologie opérationnelle (OT, Operational Technology) ouverte et interopérable. Defender pour IoT est déployé dans de nombreux endroits différents et réduit les risques liés à l’IoT, à l’informatique (IT) et au partage de connexion Internet (ICS) avec des déploiements dans des environnements exigeants et complexes dans l’ensemble des secteurs verticaux et géographiques.

## <a name="supported-protocols"></a>Protocoles pris en charge

Microsoft Defender pour IoT prend en charge un large choix de protocoles dans une entreprise diversifiée et comprend des équipements d’automatisation industrielle pour l’ensemble des secteurs industriels, des réseaux d’entreprise et des environnements BMS (Building Management System). Pour les protocoles personnalisés ou propriétaires, Microsoft offre un SDK qui facilite le développement, le test et le déploiement de dissecteurs de protocoles personnalisés sous forme de plug-ins. Le SDK effectue tout cela sans divulguer les informations propriétaires, comme la façon dont les protocoles sont conçus, ou en partageant des PCAP qui peuvent contenir des informations sensibles. Les protocoles pris en charge sont listés ci-dessous.

### <a name="supported-protocols-passive-monitoring"></a>Protocoles pris en charge (supervision passive)

Cette section liste les protocoles détectés en utilisant une supervision passive.

**ABB :** IEC61850 MMS (y compris l’extension ABB)

**ASHRAE :** BACnet, BACnet BACapp, BACnet BVLC

**Beckhoff :** AMS (ADS), Twincat 

**Cisco :** CAPWAP Control, CAPWAP Data, CDP,  LWAPP

**DNP. org :**  DNP3

**Emerson :** DeltaV, Emerson OpenBSI/BSAP, Ovation DCS ADMD,Ovation DCS DPUSTAT, Ovation DCS SSRPC

**Emerson Fischer :**  ROC

**Eurocontrol :** ASTERIX

**GE :**  Bentley Nevada (System 1),  EGD,  GSM (GE MarkVI et MarkVIe),  SRTP (GE)

**Honeywell :** ENAP, Experion DCS CDA, Experion DCS FDA

**IEC :** Codesys V3, ICCP TASE.2/IEC-60870, IEC60870-5 (IEC104/101), IEC60870-5-103 (sériel encapsulé), IEC61850 GOOSE, IEC61850 MMS, IEC61850 SMV (SAMPLED-VALUES), LonTalk (LonWorks)

**IEEE** : LLC, STP, VLAN

**IETF :** ARP, DCE RPC, DNS, FTP (FTP_ADAT, FTP_DATA), GSSAPI (RFC2743), HTTP, ICMP, IPv4, IPv6, LLDP, MDNS, NBNS, NTLM (Protocole d’authentification NTLMSSP), RPC, SMB / Browse / NBDGM, SMB / CIFS, SNMP, SPNEGO (RFC4178), SSH, Syslog, Telnet, TFTP, TPKT, UDP

**ISO :** CLNP (ISO 8473), COTP (ISO 8073), Protocole industriel ISO, MQTT (IEC 20922)

**Medical :** ASTM, HL7

**Microsoft :** Dissecteurs de la communauté Horizon, Dissecteurs propriétaires Horizon (développés par des clients). Pour plus d’informations, consultez [Dissecteur de protocole propriétaire Horizon](references-horizon-sdk.md).

**Mitsubishi :** Melsoft / Melsec (Mitsubishi Electric)

**Omron :** FINS

**Oracle :** TDS, TNS

**Rockwell Automation :** ENIP, EtherNet/IP CIP (y compris l’extension Rockwell), EtherNet/IP CIP FW version 27 et ultérieure

**Schneider Electric :** Modbus/TCP, Modbus TCP–Schneider Unity Extensions, OASYS (Schneider Electric Telvant) 

**Schneider Electric / Invensys :** Foxboro Evo, Foxboro I/A, Trident, TriGP, TriStation

**Schneider Electric / Modicon :** Modbus RTU

**Schneider Electric / Wonderware :** Wonderware Suitelink

**Siemens :** CAMP, PCS7, PCS7 WinCC – Historian, Profinet DCP, Profinet Realtime, Siemens PHD, Siemens S7, Siemens S7-Plus, Siemens S7-Plus, Siemens SICAM, Siemens WinCC

**Toshiba :** Toshiba Computer Link

**Yokogawa :** Centum ODEQ (Centum / ProSafe DCS), HIS Equalize, Vnet/IP

### <a name="supported-protocols-active-monitoring"></a>Protocoles pris en charge (supervision active)

Cette section liste les protocoles détectés en utilisant une détection active, par exemple des balayages et des requêtes ping.

**IETF :** Ping Sweep, SNMP Network Layout Query, SNMP Query

**Microsoft :** Windows WMI Query (req. WMI/WinRM) : matériel, BIOS, version, logiciel, correctifs

**Rockwell Automation :**  ENIP Query, ENIP Scan,  EtherNet/IP CIP (CIP Query)

**Siemens :** Siemens S7

## <a name="quickly-add-support-for-proprietary-restricted-protocols"></a>Ajouter rapidement la prise en charge de protocoles restreints propriétaires

La numérisation conduit au déploiement de milliards d’appareils IoT, suivi d’une augmentation significative de la connectivité entre les réseaux informatiques et les réseaux de technologie opérationnelle (OT). Cela signifie que les surfaces d’attaque augmentent, avec un risque plus élevé d’attaques informatiques dangereuses sur les systèmes de contrôle industriels.

Pour réduire la surface d’attaque, le SDK du protocole Horizon permet une prise en charge rapide et sécurisée de n’importe quel protocole utilisé dans les environnements IoT et ICS.

Cet environnement fournit les solutions suivantes pour les clients et les partenaires technologiques :

- Une prise en charge complète et illimitée des protocoles courants, propriétaires et personnalisés ou des protocoles qui s’écartent de toute norme.

- Un nouveau niveau de flexibilité et de possibilité pour le développement de plug-ins de dissecteur.

- Un outil qui étend de façon exponentielle la visibilité et le contrôle de l’OT, sans nécessité de mettre à niveau les versions de plateforme Defender pour IoT.

- La sécurité de permettre un développement propriétaire sans divulguer d’informations sensibles.

Le SDK Horizon vous permet d’écrire des plug-ins qui permettent l’inspection approfondie des paquets (DPI, Deep Packet Inspection) sur le trafic et la détection des menaces en temps réel. Le SDK Horizon rend également possible des personnalisations supplémentaires. Par exemple, le SDK Horizon permet aux fournisseurs de ressources, aux partenaires ou aux propriétaires de plateforme de localiser et de personnaliser le texte des alertes, des événements et des paramètres de protocole.

[![Le SDK Horizon permet une prise en charge rapide de 100 % des protocoles utilisés dans les environnements IoT et ICS.](media/concept-supported-protocols/sdk-horizon.png)](media/concept-supported-protocols/sdk-horizon-expanded.png#lightbox)

## <a name="collaborate-with-the-horizon-community"></a>Collaborer avec la communauté Horizon

Faites partie d’une communauté qui fait avancer la transformation numérique et la collaboration à l’échelle de l’ensemble de l’industrie dans la prise en charge des protocoles. La communauté Horizon ICS permet le partage des connaissances pour les experts du domaine dans les infrastructures critiques, la gestion des bâtiments, les lignes de production, les systèmes de transport et d’autres leaders de l’industrie.

La communauté fournit des tutoriels, des forums de discussion, des formations données par des instructeurs, des livres blancs pédagogiques, des webinaires, etc.

Nous vous invitons à rejoindre notre communauté ici : <horizon-community@microsoft.com>

## <a name="next-steps"></a>Étapes suivantes

En savoir plis sur le [Dissecteur de protocole propriétaire Horizon](references-horizon-sdk.md).

Consultez notre [API Horizon](references-horizon-api.md).
