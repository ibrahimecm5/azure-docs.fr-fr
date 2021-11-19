---
title: Clé CEF (Common Event format) et mappage de champs CommonSecurityLog
description: Cet article mappe les clés CEF aux noms de champs correspondants dans le CommonSecurityLog dans Microsoft Sentinel.
services: sentinel
author: batamig
ms.author: bagol
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: reference
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 710b20dcf4f1ac94816881c4851ddfafc0f84872
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522096"
---
# <a name="cef-and-commonsecuritylog-field-mapping"></a>Mappage de champs CEF et CommonSecurityLog

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Les tableaux suivants mappent les noms de champs CEF (Common Event format) aux noms qu’ils utilisent dans le CommonSecurityLog de Microsoft Sentinel et peuvent être utiles lorsque vous travaillez avec une source de données CEF dans Microsoft Sentinel.

Pour plus d’informations, consultez [Connecter votre solution externe à l’aide de Common Event Format](connect-common-event-format.md).

> [!NOTE]
> Un espace de travail Microsoft Sentinel est requis pour  [ingérer des données CEF](connect-common-event-format.md#prerequisites) dans Log Analytics.
>

## <a name="a---c"></a>A - C

|Nom de la clé CEF  |Nom du champ CommonSecurityLog  |Description  |
|---------|---------|---------|
| agir    |    <a name="deviceaction"></a> DeviceAction     |  L’action mentionnée dans l’événement.       |
|   app  |    ApplicationProtocol     |  Protocole utilisé dans l’application, tel que HTTP, HTTPS, SSHv2, Telnet, POP, IMPA, IMAP, etc.   |
| cnt    |    EventCount     |  Nombre associé à l’événement, qui indique le nombre de fois que le même événement a été observé.       |
| | | |

## <a name="d"></a>D

|Nom de la clé CEF  |Nom CommonSecurityLog  |Description  |
|---------|---------|---------|
|Fournisseur de l’appareil     |  DeviceVendor       | Chaîne qui, associée aux définitions de version et de produit du périphérique, identifie de façon unique le type d’appareil émetteur.       |
|Produit de l’appareil     |   DeviceProduct      |   Chaîne qui, associée aux définitions de version et de fournisseur du périphérique, identifie de façon unique le type d’appareil émetteur.        |
|Version de l’appareil     |   DeviceVersion      |      Chaîne qui, associée aux définitions de fournisseur et de produit du périphérique, identifie de façon unique le type d’appareil émetteur.     |
| destinationDnsDomain    | DestinationDnsDomain        |   La partie DNS du nom de domaine complet (FQDN).      |
| destinationServiceName | DestinationServiceName | Le service ciblé par l’événement. Par exemple : `sshd`.|
| destinationTranslatedAddress | DestinationTranslatedAddress | Identifie la destination traduite référencée par l’événement dans un réseau IP, sous la forme d’une adresse IP IPv4. |
| destinationTranslatedPort | DestinationTranslatedPort | Port, après traduction, tel qu’un pare-feu. <br>Numéros de port valides : `0` - `65535` |
| deviceDirection | <a name="communicationdirection"></a> CommunicationDirection | Toutes les informations sur le sens de la communication observée. Valeurs valides : <br>- `0` = Entrant <br>- `1` = Sortant |
| deviceDnsDomain | DeviceDnsDomain | Partie du domaine DNS du nom de domaine complet (FQDN) |
|DeviceEventClassID     |   DeviceEventClassID     |   Chaîne ou entier qui sert d’identificateur unique par type d’événement.      |
| deviceExternalID | DeviceExternalID | Nom qui identifie de façon unique l’appareil générant l’événement. |
| deviceFacility | DeviceFacility | La structure générant l’événement.|
| deviceInboundInterface | DeviceInboundInterface |Interface sur laquelle le paquet ou les données sont entrés sur l’appareil.  |
| deviceNtDomain | DeviceNtDomain | Domaine Windows de l’adresse de l’appareil |
| deviceOutboundInterface | DeviceOutboundInterface |Interface sur laquelle le paquet ou les données ont quitté l’appareil. |
| devicePayloadId |DevicePayloadId |Identificateur unique de la charge utile associée à l’événement. |
| deviceProcessName | ProcessName | Nom du processus associé à l'événement. <br><br>Par exemple, dans UNIX, le processus générant l’entrée syslog. |
| deviceTranslatedAddress | DeviceTranslatedAddress | Identifie l’adresse de l’appareil traduit à laquelle l’événement fait référence, sur un réseau IP. <br><br>Le format est une adresse Ipv4. |
| dhost |DestinationHostName | Destination à laquelle l’événement fait référence dans un réseau IP.  <br>Le format doit être un nom de domaine complet associé au nœud de destination, lorsqu’un nœud est disponible. Par exemple, `host.domain.com` ou `host`. |
| dmac | DestinationMacAddress | L’adresse MAC de destination (FQDN) |
| dntdom | DestinationNTDomain | Nom de domaine Windows de l’adresse de destination.|
| dpid | DestinationProcessId |L’ID du processus de destination associé à l’événement.|
| dpriv | DestinationUserPrivileges | Définit les privilèges de l’utilisation de la destination. <br>Valeurs valides : `Admninistrator`, `User`, `Guest` |
| dproc | DestinationProcessName | Nom du processus de destination de l’événement, tel que `telnetd` ou `sshd.` |
| dpt | DestinationPort | Port de destination. <br>Valeurs valides : `*0` - `65535` |
| dst | DestinationIP | L’adresse IpV4 de destination à laquelle l’événement fait référence dans un réseau IP. |
| dtz | DeviceTimeZone | Fuseau horaire de l’appareil générant l’événement |
| duid |DestinationUserId | Identifie l’utilisateur de destination par son ID. |
| duser | DestinationUserName |Identifie l’utilisateur de destination par son nom.|
| dvc | DeviceAddress | Adresse IPv4 de l’appareil générant l’événement. |
| dvchost | DeviceName | Nom de domaine complet associé au nœud de l’appareil, lorsqu’un nœud est disponible. Par exemple, `host.domain.com` ou `host`.| 
| dvcmac | DeviceMacAddress | Adresse MAC de l’appareil générant l’événement. |
| dvcpid | ID du processus | Définit l’ID du processus sur l’appareil générant l’événement. |

## <a name="e---i"></a>E - I

|Nom de la clé CEF  |Nom CommonSecurityLog  |Description  |
|---------|---------|---------|
|externalId    |   ExternalID      | Un ID utilisé par l’appareil d’origine. En règle générale, ces valeurs ont des valeurs de plus en plus associées à un événement.        |
|fileCreateTime     |  FileCreateTime      | Heure de création du fichier.        |
|fileHash     |   FileHash      |   Hachage d’un fichier.      |
|fileId     |   FileID      |Un ID associé à un fichier, tel que l’inode.         |
| fileModificationTime | FileModificationTime |Heure à laquelle le fichier a été modifié pour la dernière fois. |
| filePath | FilePath | Chemin complet d'accès du fichier, y compris le nom de fichier. Par exemple, `C:\ProgramFiles\WindowsNT\Accessories\wordpad.exe` ou `/usr/bin/zip`.|
| filePermission |FilePermission |Les autorisations du fichier. |
| fileType | FileType | Type de fichier (par exemple, canal, Socket, etc.).|
|fname | FileName| Nom du fichier, sans le chemin d’accès. |
| fsize | FileSize | Taille du fichier. |
|Host    |  Computer       | Hôte, de Syslog        |
|in     |  ReceivedBytes      |Nombre d’octets transférés entrants.         |
| | | |

## <a name="m---p"></a>M - P

|Nom de la clé CEF  |Nom CommonSecurityLog  |Description  |
|---------|---------|---------|
|msg   |  Message       | Message qui donne plus de détails sur l’événement.        |
|Nom     |  Activité       |   Chaîne qui représente une description explicite et compréhensible de l’événement.     |
|oldFileCreateTime     |  OldFileCreateTime       | Heure de création de l’ancien fichier.        |
|oldFileHash     |   OldFileHash      |   Hachage de l’ancien fichier.      |
|oldFileId     |   OldFileId     |   Et l’ID associé à l’ancien fichier, tel que l’inode.      |
| oldFileModificationTime | OldFileModificationTime |Heure à laquelle l’ancien fichier a été modifié pour la dernière fois. |
| oldFileName |  OldFileName |Nom de l’ancien fichier. |
| oldFilePath | OldFilePath | Chemin complet d'accès à l’ancien fichier, y compris le nom de fichier. <br>Par exemple, `C:\ProgramFiles\WindowsNT\Accessories\wordpad.exe` ou `/usr/bin/zip`.|
| oldFilePermission | OldFilePermission |Autorisations de l’ancien fichier. |
|oldFileSize | OldFileSize | Taille de l’ancien fichier.|
| oldFileType | OldFileType | Type de fichier de l’ancien fichier, tel qu’un canal, un socket, etc.|
| out | SentBytes | Nombre d’octets transférés sortants. |
| Résultat | Résultat | Résultat de l’événement, tel que `success` ou `failure`.|
|proto    |  Protocol       | Protocole de transport qui identifie le protocole de couche 4 utilisé. <br><br>Les valeurs possibles incluent les noms de protocole, tels que `TCP` ou `UDP` .        |
| | | |

## <a name="r---t"></a>R - T

|Nom de la clé CEF  |Nom CommonSecurityLog  |Description  |
|---------|---------|---------|
|Requête     |   RequestURL      | URL accessible pour une requête HTTP, y compris le protocole. Par exemple : `http://www/secure.com`        |
|requestClientApplication     |   RequestClientApplication      |   L'agent utilisateur associé à la requête.      |
| requestContext | RequestContext | Décrit le contenu d’où provient la requête, tel que le référent HTTP. |
| requestCookies | RequestCookies |Cookies associés à la requête. |
| requestMethod | RequestMethod | Méthode utilisée pour accéder à une URL. <br><br>Les valeurs valides incluent des méthodes telles que `POST`, `GET`, et ainsi de suite. |
| rt | ReceiptTime | Heure à laquelle l’événement associé à l’activité a été reçu. |
|Gravité     |  <a name="logseverity"></a> LogSeverity       |  Chaîne ou entier qui décrit l’importance de l’événement.<br><br> Valeur de chaîne valide : `Unknown`, `Low`, `Medium`, `High`, `Very-High` <br><br>Les valeurs d’entier valides sont :<br> - `0`-`3` = Faible <br>- `4`-`6` = Moyenne<br>- `7`-`8` = Élevée<br>- `9`-`10` = Très élevée |
| shost    | SourceHostName        |Identifie la source à laquelle l’événement fait référence dans un réseau IP. Le format doit être un nom de domaine complet (FQDN) associé au nœud source, lorsqu’un nœud est disponible. Par exemple, `host` ou `host.domain.com`. |
| smac | SourceMacAddress | Adresse MAC source. |
| sntdom | SourceNTDomain | Nom de domaine Windows pour l’adresse source. |
| sourceDnsDomain | SourceDnsDomain | Partie du domaine DNS du FQDN complet. |
| sourceServiceName | SourceServiceName | Service responsable de la génération de l’événement. |
| sourceTranslatedAddress | SourceTranslatedAddress | Identifie la source traduite à laquelle l’événement fait référence dans un réseau IP. |
| sourceTranslatedPort | SourceTranslatedPort | Port source après traduction, tel qu’un pare-feu. <br>Des numéros de port valides sont `0` - `65535`. |
| spid | SourceProcessId | L’ID du processus de source associé à l’événement.|
| spriv | SourceUserPrivileges | Privilèges de l’utilisateur source. <br><br>Les valeurs valides sont : `Administrator`, `User`, `Guest` |
| sproc | SourceProcessName | Nom du processus source de l’événement.|
| spt | SourcePort | Numéro de port source. <br>Des numéros de port valides sont `0` - `65535`. |
| src | SourceIP |Source à laquelle un événement fait référence dans un réseau IP, comme une adresse IPv4. |
| suid | SourceUserID | Identifie l’utilisateur source par ID. |
| suser | SourceUserName | Identifie l’utilisateur source par nom. |
| type | Type d’événement | Type d'événement. Les valeurs de valeur incluent : <br>- `0` : événement de base <br>- `1` : agrégés <br>- `2` : événement de corrélation <br>- `3` : événement d’action <br><br>**Remarque** : cet événement peut être omis pour les événements de base. |
| | | |

## <a name="custom-fields"></a>Champs personnalisés

Les tableaux suivants mappent les noms des clés CEF et des champs CommonSecurityLog qui peuvent être utilisés par les clients pour les données qui ne s’appliquent à aucun des champs prédéfinis.

### <a name="custom-ipv6-address-fields"></a>Champs d’adresse IPv6 personnalisés

Le tableau suivant mappe les noms de clé CEF et CommonSecurityLog pour les champs d’adresse *IPv6* disponibles pour les données personnalisées.

|Nom de la clé CEF  |Nom CommonSecurityLog  |
|---------|---------|
|     c6a1    |     DeviceCustomIPv6Address1       |
|     c6a1Label    |     DeviceCustomIPv6Address1Label    |
|     c6a2    |     DeviceCustomIPv6Address2    |
|     c6a2Label    |     DeviceCustomIPv6Address2Label    |
|     c6a3    |     DeviceCustomIPv6Address3    |
|     c6a3Label    |     DeviceCustomIPv6Address3Label    |
|     c6a4    |     DeviceCustomIPv6Address4    |
|     c6a4Label    |     DeviceCustomIPv6Address4Label    |
|     cfp1    |     DeviceCustomFloatingPoint1    |
|     cfp1Label    |     deviceCustomFloatingPoint1Label    |
|     cfp2    |     DeviceCustomFloatingPoint2    |
|     cfp2Label    |     deviceCustomFloatingPoint2Label    |
|     cfp3    |     DeviceCustomFloatingPoint3    |
|     cfp3Label    |     deviceCustomFloatingPoint3Label    |
|     cfp4    |     DeviceCustomFloatingPoint4    |
|     cfp4Label    |     deviceCustomFloatingPoint4Label    |
| | |

### <a name="custom-number-fields"></a>Champs de nombre personnalisé

Le tableau suivant mappe les noms de clé CEF et CommonSecurityLog pour les champs *numériques* disponibles pour les données personnalisées.

|Nom de la clé CEF  |Nom CommonSecurityLog  |
|---------|---------|
|     cn1    |     DeviceCustomNumber1       |
|     cn1Label    |     DeviceCustomNumber1Label       |
|     cn2    |     DeviceCustomNumber2       |
|     cn2Label    |     DeviceCustomNumber2Label       |
|     cn3    |     DeviceCustomNumber3       |
|     cn3Label    |     DeviceCustomNumber3Label       |
| | |

### <a name="custom-string-fields"></a>Champs de chaîne personnalisés

Le tableau suivant mappe les noms de clé CEF et CommonSecurityLog pour les champs de *chaîne* disponibles pour les données personnalisées.

|Nom de la clé CEF  |Nom CommonSecurityLog  |
|---------|---------|
|     cs1    |     DeviceCustomString1 <sup>[1](#use-sparingly)</sup>    |
|     cs1Label    |     DeviceCustomString1Label <sup>[1](#use-sparingly)</sup>    |
|     cs2    |     DeviceCustomString2 <sup>[1](#use-sparingly)</sup>   |
|     cs2Label    |     DeviceCustomString2Label   <sup>[1](#use-sparingly)</sup> |
|     cs3    |     DeviceCustomString3  <sup>[1](#use-sparingly)</sup>  |
|     cs3Label    |     DeviceCustomString3Label   <sup>[1](#use-sparingly)</sup> |
|     cs4    |     DeviceCustomString4   <sup>[1](#use-sparingly)</sup> |
|     cs4Label    |     DeviceCustomString4Label  <sup>[1](#use-sparingly)</sup>  |
|     cs5    |     DeviceCustomString5 <sup>[1](#use-sparingly)</sup>   |
|     cs5Label    |     DeviceCustomString5Label <sup>[1](#use-sparingly)</sup>    |
|     cs6    |     DeviceCustomString6   <sup>[1](#use-sparingly)</sup> |
|     cs6Label    |     DeviceCustomString6Label   <sup>[1](#use-sparingly)</sup> |
|     flexString1    |     FlexString1    |
|     flexString1Label    |     FlexString1Label    |
|     flexString2    |     FlexString2    |
|     flexString2Label    |     FlexString2Label    |
| | |

> [!TIP]
> <a name="use-sparingly"></a><sup>1</sup> Nous vous recommandons d’utiliser les champs **DeviceCustomString** avec modération et d’utiliser des champs plus spécifiques et intégrés lorsque cela est possible.
> 

### <a name="custom-timestamp-fields"></a>Champs timestamp personnalisés

Le tableau suivant mappe les noms de clé CEF et CommonSecurityLog pour les champs de *timestamp* disponibles pour les données personnalisées.

|Nom de la clé CEF  |Nom CommonSecurityLog  |
|---------|---------|
|     deviceCustomDate1    |     DeviceCustomDate1    |
|     deviceCustomDate1Label    |     DeviceCustomDate1Label    |
|     deviceCustomDate2       |     DeviceCustomDate2    |
|     deviceCustomDate2Label    |     DeviceCustomDate2Label    |
|     flexDate1    |     FlexDate1    |
|     flexDate1Label    |     FlexDate1Label    |
| | |

### <a name="custom-integer-data-fields"></a>Champs de données d’entier personnalisés

Le tableau suivant mappe les noms de clé CEF et CommonSecurityLog pour les champs *entiers* disponibles pour les données personnalisées.

|Nom de la clé CEF  |Nom CommonSecurityLog  |
|---------|---------|
|     flexNumber1    |     FlexNumber1    |
|     flexNumber1Label    |     FlexNumber1Label    |
|     flexNumber2    |     FlexNumber2    |
|     flexNumber2Label    |     FlexNumber2Label    |
| | |

## <a name="enrichment-fields"></a>Champs d’enrichissement

Les champs **CommonSecurityLog** suivants sont ajoutés par Microsoft Sentinel pour enrichir les événements d’origine reçus à partir des appareils sources. Ils n’ont pas de mappages dans les clés CEF :

### <a name="threat-intelligence-fields"></a>Champs d’informations sur les menaces

|Nom du champ CommonSecurityLog  |Description  |
|---------|---------|
|   **IndicatorThreatType**  |  Type de menace [MaliciousIP](#MaliciousIP), selon le flux d’informations sur les menaces.       |
| <a name="MaliciousIP"></a>**MaliciousIP** | Répertorie toutes les adresses IP du message corrélées avec le flux d’informations sur les menaces actuel. |
|  **MaliciousIPCountry**   | Le pays de [MaliciousIP](#MaliciousIP), en fonction des informations géographiques au moment de l’ingestion de l’enregistrement.        |
| **MaliciousIPLatitude**    |   La longitude de [MaliciousIP](#MaliciousIP), en fonction des informations géographiques au moment de l’ingestion de l’enregistrement.      |
| **MaliciousIPLongitude**    |  La longitude de [MaliciousIP](#MaliciousIP), en fonction des informations géographiques au moment de l’ingestion de l’enregistrement.       |
| **ReportReferenceLink**    |    Lien vers le rapport d’informations sur les menaces.     |
|  **ThreatConfidence**   |   Confiance de menace de [MaliciousIP](#MaliciousIP), selon le flux d’informations sur les menaces.      |
| **ThreatDescription**    |   Description de menace de [MaliciousIP](#MaliciousIP), selon le flux d’informations sur les menaces.      |
| **ThreatSeverity** | Le niveau de gravité de la menace pour [MaliciousIP](#MaliciousIP), en fonction du flux d’informations sur les menaces au moment de l’ingestion des enregistrements. |
|     |         |

### <a name="additional-enrichment-fields"></a>Champs d’enrichissement supplémentaires

|Nom du champ CommonSecurityLog  |Description  |
|---------|---------|
|**OriginalLogSeverity**     |  Toujours vide, pris en charge pour l’intégration avec CiscoASA. <br>Pour plus d’informations sur les valeurs de gravité du journal, consultez le champ [LogSeverity](#logseverity).       |
|**RemoteIP**     |     Adresse IP distante. <br>Cette valeur est basée sur le champ [CommunicationDirection](#communicationdirection), si possible.     |
|**RemotePort**     |   Le port distant. <br>Cette valeur est basée sur le champ [CommunicationDirection](#communicationdirection), si possible.      |
|**SimplifiedDeviceAction**     |   Simplifie la valeur [DeviceAction](#deviceaction) sur un ensemble statique de valeurs, tout en conservant la valeur d’origine dans le champ [DeviceAction](#deviceaction). <br>Par exemple : `Denied` > `Deny`.      |
|**SourceSystem**     | Toujours défini comme **OpsManager**.        |
|     |         |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez [Connecter votre solution externe à l’aide de Common Event Format](connect-common-event-format.md).
