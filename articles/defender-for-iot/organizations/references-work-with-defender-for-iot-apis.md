---
title: Utiliser les API Defender pour IoT
description: Utilisez une API REST externe pour accéder aux données découvertes par les capteurs et les consoles de gestion et effectuer des actions avec ces données.
ms.date: 11/09/2021
ms.topic: reference
ms.openlocfilehash: 3d7fdf855e33c84ce966bbe89e564434b2a8a748
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132325293"
---
# <a name="defender-for-iot-sensor-and-management-console-apis"></a>API de capteur et de console de gestion Defender pour IoT

Les API Defender pour IoT sont régies par les [conditions d’utilisation et la licence d’API Microsoft](/legal/microsoft-apis/terms-of-use).

Utilisez une API REST externe pour accéder aux données découvertes par les capteurs et les consoles de gestion et effectuer des actions avec ces données.

Les connexions sont sécurisées via SSL.

## <a name="getting-started"></a>Prise en main

En général, lorsque vous utilisez une API externe sur la console de gestion locale ou le capteur Microsoft Defender pour IoT, vous devez générer un jeton d’accès. Les jetons ne sont pas requis pour les API d’authentification que vous utilisez sur le capteur et la console de gestion locale.

Pour générer un jeton :

1. Dans la fenêtre **Paramètres système**, sélectionnez **Jetons d’accès**.
  
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-tokens.png" alt-text="Capture d’écran de la fenêtre Paramètres système mettant en surbrillance le bouton Jetons d’accès.":::

1. Sélectionnez **Générer un nouveau jeton**.

   :::image type="content" source="media/references-work-with-defender-for-iot-apis/new-token.png" alt-text="Sélectionnez le bouton pour créer un nouveau jeton.":::

1. Décrivez l’objectif du nouveau jeton et sélectionnez **Suivant**.

   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-name.png" alt-text="Générez un nouveau jeton et entrez le nom de l’intégration qui lui est associée.":::

1. Le jeton d'accès apparaît. Copiez-le, car il ne s’affichera plus.

   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-code.png" alt-text="Copiez votre jeton d’accès pour votre intégration.":::

1. Sélectionnez **Terminer**. Les jetons que vous créez apparaissent dans la boîte de dialogue **Jetons d’accès**.

   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-token-window.png" alt-text="Capture d’écran de la boîte de dialogue Jetons d’appareil avec des jetons remplis":::

   **Utilisé** indique l’heure à laquelle le dernier appel externe avec ce jeton a été reçu.

   Si **N/A** est affiché dans le champ **Utilisé** pour ce jeton, cela signifie que la connexion entre le capteur et le serveur connecté ne fonctionne pas.

1. Ajoutez un en-tête HTTP intitulé **Autorisation** à votre requête et définissez sa valeur sur le jeton que vous avez généré.

## <a name="sensor-api-specifications"></a>Spécifications de l’API de capteur

Cette section décrit les API de capteur suivantes :

- [Récupérer des informations sur les appareils – /api/v1/devices](#retrieve-device-information---apiv1devices)

- [Récupérer les informations de connexion des appareils – /api/v1/devices/connections](#retrieve-device-connection-information---apiv1devicesconnections)

- [Récupérer des informations sur les CVE – /api/v1/devices/cves](#retrieve-information-on-cves---apiv1devicescves)

- [Récupérer des informations sur les alertes – /api/v1/alerts](#retrieve-alert-information---apiv1alerts)

- [Récupérer les événements de chronologie – /api/v1/events](#retrieve-timeline-events---apiv1events)

- [Récupérer des informations sur les vulnérabilités – /api/v1/reports/vulnerabilities/devices](#retrieve-vulnerability-information---apiv1reportsvulnerabilitiesdevices)

- [Récupérer les failles de sécurité – /api/v1/reports/vulnerabilities/security](#retrieve-security-vulnerabilities---apiv1reportsvulnerabilitiessecurity)

- [Récupérer les vulnérabilités opérationnelles – /api/v1/reports/vulnerabilities/operational](#retrieve-operational-vulnerabilities---apiv1reportsvulnerabilitiesoperational)

- [Valider les informations d’identification d’utilisateur – /api/external/authentication/validation](#validate-user-credentials---apiexternalauthenticationvalidation)

- [Modifier le mot de passe – /external/authentication/set_password](#change-password---externalauthenticationset_password)

- [Mise à jour du mot de passe de l’utilisateur par l’administrateur système – /external/authentication/set_password_by_admin](#user-password-update-by-system-admin---externalauthenticationset_password_by_admin)

- [Récupérer une alerte PCAP – /api/v2/alerts/pcap](#retrieve-alert-pcap---apiv2alertspcap)

### <a name="retrieve-device-information---apiv1devices"></a>Récupérer des informations sur les appareils – /api/v1/devices

Utilisez cette API pour demander une liste de tous les appareils détectés par un capteur Defender pour IoT.

#### <a name="method"></a>Méthode

- **GET**

Demande une liste de tous les appareils détectés par le capteur Defender pour IoT.

#### <a name="query-parameters"></a>Paramètres de requête

- **autorisé** : Pour filtrer uniquement les appareils autorisés et non autorisés.

  **Exemples** :

  `/api/v1/devices?authorized=true`

  `/api/v1/devices?authorized=false`

#### <a name="response-type"></a>Type de réponse

- **JSON**

#### <a name="response-content"></a>Contenu de la réponse

Tableau d'objets JSON qui représentent les appareils.

#### <a name="device-fields"></a>Champs de l’appareil

| Nom | Type | Nullable | Liste de valeurs |
|--|--|--|--|
| **id** | Numérique | Non | - |
| **ipAddresses** | Tableau JSON | Oui | Adresses IP (il peut y avoir plusieurs adresses en cas d’adresses Internet ou d’appareil avec deux cartes réseau) |
| **name** | String | Non | - |
| **type** | String | Non | Inconnu, Station d’ingénierie, PLC, HMI, Historien, Contrôleur de domaine, Serveur de base de données, Point d’accès sans fil, Routeur, Commutateur, Serveur, Station de travail, Caméra IP, Imprimante, Pare-feu, Station de terminal, Passerelle VPN, Internet, ou Multidiffusion et Diffusion |
| **macAddresses** | Tableau JSON | Oui | Adresses MAC (il peut y avoir plusieurs adresses en cas d’appareil avec deux cartes réseau) |
| **operatingSystem** | String | Oui | - |
| **engineeringStation** | Boolean | Non | True ou false |
| **scanner** | Boolean | Non | True ou false |
| **autorisé** | Boolean | Non | True ou false |
| **vendor** | String | Oui | - |
| **protocols** | Tableau JSON | Oui | Objet de protocole |
| **firmware** | Tableau JSON | Oui | Objet de microprogramme |

#### <a name="protocol-fields"></a>Champs de protocole

| Nom | Type | Nullable | Liste de valeurs |
|--|--|--|--|
| **Nom** | String | Non |  |
| **Adresses** | Tableau JSON | Oui | Valeurs de type Master ou numérique |

#### <a name="firmware-fields"></a>Champs du microprogramme

| Nom | Type | Nullable | Liste de valeurs |
|--|--|--|--|
| **serial** | String | Non | N/A ou la valeur réelle |
| **model** | String | Non | N/A ou la valeur réelle |
| **firmwareVersion** | Double | Non | N/A ou la valeur réelle |
| **additionalData** | String | Non | N/A ou la valeur réelle |
| **moduleAddress** | String | Non | N/A ou la valeur réelle |
| **rack** | String | Non | N/A ou la valeur réelle |
| **slot** | String | Non | N/A ou la valeur réelle |
| **address** | String | Non | N/A ou la valeur réelle |

#### <a name="response-example"></a>Exemple de réponse

```rest
[

    {
    
    "vendor": null,
    
    "name": "10.4.14.102",
    
    "firmware": [
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 0, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 0, Unit: Unknown (0x3)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 3, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: 3, Node: 0, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        }
    
    ],
    
    "id": 79,
    
    "macAddresses": null,
    
    "authorized": true,
    
    "ipAddresses": [
    
        "10.4.14.102"
    
    ],
    
    "engineeringStation": false,
    
    "type": "PLC",
    
    "operatingSystem": null,
    
    "protocols": [
    
        {
        
            "addresses": [],
            
            "id": 62,
            
            "name": "Omron FINS"
        
        }
    
    ],
    
    "scanner": false
    
}

]
```

#### <a name="curl-command"></a>Commande Curl

| Type | API | Exemple |
|--|--|--|
| GET | `curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/devices` | `curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https://127.0.0.1/api/v1/devices?authorized=true` |

### <a name="retrieve-device-connection-information---apiv1devicesconnections"></a>Récupérer les informations de connexion des appareils – /api/v1/devices/connections

Utilisez cette API pour demander une liste de toutes les connexions par appareil.

#### <a name="method"></a>Méthode

- **GET**

#### <a name="query-parameters"></a>Paramètres de requête

Si vous ne définissez pas les paramètres de la requête, toutes les connexions de l’appareil sont retournées.

**Exemple** :

`/api/v1/devices/connections`

- **deviceId** : Filtrez par un ID d’appareil spécifique pour afficher ses connexions.

  **Exemple** :

  `/api/v1/devices/<deviceId>/connections`

- **lastActiveInMinutes** : Laps de temps à rebours à partir de maintenant, par minute, pendant lequel les connexions étaient actives.

  **Exemple** :

  `/api/v1/devices/2/connections?lastActiveInMinutes=20`

- **discoveredBefore** : Filtrez uniquement les connexions qui ont été détectées avant une heure spécifique (en millisecondes, UTC).

  **Exemple** :

  `/api/v1/devices/2/connections?discoveredBefore=<epoch>`

- **discoveredAfter** : Filtrez uniquement les connexions qui ont été détectées après une heure spécifique (en millisecondes, UTC).

  **Exemple** :

  `/api/v1/devices/2/connections?discoveredAfter=<epoch>`

#### <a name="response-type"></a>Type de réponse

- **JSON**

#### <a name="response-content"></a>Contenu de la réponse

Tableau d'objets JSON qui représentent les connexions de l’appareil.

#### <a name="fields"></a>Champs

| Nom | Type | Nullable | Liste de valeurs |
|--|--|--|--|
| **firstDeviceId** | Numérique | Non | - |
| **secondDeviceId** | Numérique | Non | - |
| **lastSeen** | Numérique | Non | Époque (UTC) |
| **discovered** | Numérique | Non | Époque (UTC) |
| **ports** | Tableau de chiffres | Non | - |
| **protocols** | Tableau JSON | Non | Champ de protocole |

#### <a name="protocol-field"></a>Champ de protocole

| Nom | Type | Nullable | Liste de valeurs |
|--|--|--|--|
| **name** | String | Non | - |
| **commands** | Tableau de chaînes | Non | - |

#### <a name="response-example"></a>Exemple de réponse

```rest
[

    {
    
        "firstDeviceId": 171,
        
        "secondDeviceId": 22,
        
        "lastSeen": 1511281457933,
        
        "discovered": 1511872830000,
        
        "ports": [
        
            502
        
        ],
    
        "protocols": [
        
        {
        
            name: "modbus",
            
            commands: [
            
                "Read Coils"
        
            ]
        
        },
    
        {
        
            name: "ams",
            
            commands: [
            
                "AMS Write"
        
            ]
        
        },
    
        {
        
            name: "http",
            
            commands: [
        
            ]
        
        }
    
    ]
    
    },
    
    {
    
        "firstDeviceId": 171,
        
        "secondDeviceId": 23,
        
        "lastSeen": 1511281457933,
        
        "discovered": 1511872830000,
        
        "ports": [
        
            502
        
        ],
        
        "protocols": [
        
            {
            
                name: "s7comm",
                
                commands: [
                
                    "Download block",
                    
                    "Upload"
            
                ]
            
            }
        
        ]
    
    }

]
```

#### <a name="curl-command"></a>Commande Curl

> [!div class="mx-tdBreakAll"]
> | Type | API | Exemple |
> |--|--|--|
> | GET | `curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/devices/connections` | `curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https://127.0.0.1/api/v1/devices/connections` |
> | GET | `curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<IP_ADDRESS>/api/v1/devices/<deviceId>/connections?lastActiveInMinutes=&discoveredBefore=&discoveredAfter='` | `curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" 'https://127.0.0.1/api/v1/devices/2/connections?lastActiveInMinutes=20&discoveredBefore=1594550986000&discoveredAfter=1594550986000'` |

### <a name="retrieve-information-on-cves---apiv1devicescves"></a>Récupérer des informations sur les CVE – /api/v1/devices/cves

Utilisez cette API pour demander une liste de tous les CVE connus découverts sur les appareils du réseau.

#### <a name="method"></a>Méthode

- **GET**

#### <a name="query-parameters"></a>Paramètres de requête

Par défaut, cette API fournit la liste de toutes les adresses IP d’appareil avec CVE, jusqu’à 100 CVE de score supérieur pour chaque adresse IP.

**Exemple** :

`/api/v1/devices/cves`

- **deviceId** : Filtrez en fonction d’une adresse IP d’appareil spécifique pour obtenir jusqu’à 100 CVE identifiés sur cet appareil spécifique.

  **Exemple** :

  `/api/v1/devices/<ipAddress>/cves`

- **top** : Nombre de CVE de score supérieur à récupérer pour chaque adresse IP de l’appareil.

  **Exemple** :

  `/api/v1/devices/cves?top=50`

  `/api/v1/devices/<ipAddress>/cves?top=50`

#### <a name="response-type"></a>Type de réponse

- **JSON**

#### <a name="response-content"></a>Contenu de la réponse

Tableau d’objets JSON qui représentent les CVE identifiés sur les adresses IP.

#### <a name="fields"></a>Champs

| Nom | Type | Nullable | Liste de valeurs |
|--|--|--|--|
| **cveId** | String | Non | - |
| **ipAddress** | String | Non | Adresse IP |
| **score** | String | Non | 0.0 - 10.0 |
| **attackVector** | String | Non | Réseau, Réseau adjacent, Local ou Physique |
| **description** | String | Non | - |

#### <a name="response-example"></a>Exemple de réponse

```rest
[

    {
    
        "cveId": "CVE-2007-0099",
        
        "score": "9.3",
        
        "ipAddress": "10.35.1.51",
        
        "attackVector": "NETWORK",
        
        "description": "Race condition in the msxml3 module in Microsoft XML Core
        
        Services 3.0, as used in Internet Explorer 6 and other
        
        applications, allows remote attackers to execute arbitrary
        
        code or cause a denial of service (application crash) via many
        
        nested tags in an XML document in an IFRAME, when synchronous
        
        document rendering is frequently disrupted with asynchronous
        
        events, as demonstrated using a JavaScript timer, which can
        
        trigger NULL pointer dereferences or memory corruption, aka
        
        \"MSXML Memory Corruption Vulnerability.\""
    
    },
    
    {
    
        "cveId": "CVE-2009-1547",
        
        "score": "9.3",
        
        "ipAddress": "10.35.1.51",
        
        "attackVector": "NETWORK",
        
        "description": "Unspecified vulnerability in Microsoft Internet Explorer 5.01
        
        SP4, 6, 6 SP1, and 7 allows remote attackers to execute
        
        arbitrary code via a crafted data stream header that triggers
        
        memory corruption, aka \"Data Stream Header Corruption
        
        Vulnerability.\""
    
    }

]
```

#### <a name="curl-command"></a>Commande Curl

| Type | API | Exemple |
|--|--|--|
| GET | `curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/devices/cves` | `curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https://127.0.0.1/api/v1/devices/cves` |
| GET | `curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/devices/<deviceIpAddress>/cves?top=` | `curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https://127.0.0.1/api/v1/devices/10.10.10.15/cves?top=50` |

### <a name="retrieve-alert-information---apiv1alerts"></a>Récupérer des informations sur les alertes – /api/v1/alerts

Utilisez cette API pour demander une liste de toutes les alertes détectées par le capteur Defender pour IoT.

#### <a name="method"></a>Méthode

- **GET**

#### <a name="query-parameters"></a>Paramètres de requête

- **state** : Pour filtrer uniquement les alertes gérées ou non gérées.

  **Exemple** :

  `/api/v1/alerts?state=handled`

- **fromTime** : Pour filtrer les alertes créées à partir d’une heure spécifique (en millisecondes, UTC).

  **Exemple** :

  `/api/v1/alerts?fromTime=<epoch>`

- **toTime** : Pour filtrer les alertes créées uniquement avant une heure spécifique (en millisecondes, UTC).

  **Exemple** :

  `/api/v1/alerts?toTime=<epoch>`

- **type** : Pour filtrer les alertes selon un type spécifique. Types existants pour filtrer : nouveaux appareils inattendus, déconnexions.

  **Exemple** :

  `/api/v1/alerts?type=disconnections`

#### <a name="response-type"></a>Type de réponse

- **JSON**

#### <a name="response-content"></a>Contenu de la réponse

Tableau d'objets JSON qui représentent les alertes.

#### <a name="alert-fields"></a>Champs d’alerte

| Nom | Type | Nullable | Liste de valeurs |
|--|--|--|--|
| **Identifiant** | Numérique | Non | - |
| **time** | Numérique | Non | Époque (UTC) |
| **title** | String | Non | - |
| **message** | String | Non | - |
| **severity** | String | Non | Avertissement, Mineur, Majeur ou Critique |
| **engine** | String | Non | Violation de protocole, Violation de stratégie, Programme malveillant, Anomalie ou Opérationnelle |
| **sourceDevice** | Numérique | Oui | ID de périphérique |
| **destinationDevice** | Numérique | Oui | ID de périphérique |
| **sourceDeviceAddress** | Numérique | Oui | IP, MAC |
| **destinationDeviceAddress** | Numérique | Oui | IP, MAC |
| **remediationSteps** | String | Oui | Étapes de correction décrites dans l’alerte |
| **additionalInformation** | Objet d’informations supplémentaires | Oui | - |

Notez que /api/v2/ est nécessaire pour les informations suivantes :

- sourceDeviceAddress 
- destinationDeviceAddress
- remediationSteps

#### <a name="additional-information-fields"></a>Champs d’informations supplémentaires

| Nom | Type | Nullable | Liste de valeurs |
|--|--|--|--|
| **description** | String | Non | - |
| **information** | Tableau JSON | Non | String |

#### <a name="response-example"></a>Exemple de réponse

```rest
[

    {
    
        "engine": "Policy Violation",
        
        "severity": "Major",
        
        "title": "Internet Access Detected",
        
        "additionalinformation": {
        
            "information": [
            
                "170.60.50.201 over port BACnet (47808)"
            
            ],
            
            "description": "External Addresses"
        
        },
    
        "sourceDevice": null,
        
        "destinationDevice": null,
        
        "time": 1509881077000,
        
        "message": "Device 192.168.0.13 tried to access an external IP address which is an address in the Internet and is not allowed by policy. It is recommended to notify the security officer of the incident.",
        
        "id": 1
    
    },
    
    {
    
        "engine": "Protocol Violation",
        
        "severity": "Major",
        
        "title": "Illegal MODBUS Operation (Exception Raised by Master)",
        
        "sourceDevice": 3,
        
        "destinationDevice": 4,
        
        "time": 1505651605000,
        
        "message": "A MODBUS master 192.168.110.131 attempted to initiate an illegal operation.\nThe operation is considered to be illegal since it incorporated function code \#129 which should not be used by a master.\nIt is recommended to notify the security officer of the incident.",
        
        "id": 2,
        
        "additionalInformation": null,
    
    }

]

```

#### <a name="curl-command"></a>Commande Curl

> [!div class="mx-tdBreakAll"]
> | Type | API | Exemple |
> |--|--|--|
> | GET | `curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<IP_ADDRESS>/api/v1/alerts?state=&fromTime=&toTime=&type='` | `curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" 'https://127.0.0.1/api/v1/alerts?state=unhandled&fromTime=1594550986000&toTime=1594550986001&type=disconnections'` |

### <a name="retrieve-timeline-events---apiv1events"></a>Récupérer les événements de chronologie – /api/v1/events

Utilisez cette API pour demander une liste des événements signalés à la chronologie des événements.

#### <a name="method"></a>Méthode

- **GET**

#### <a name="query-parameters"></a>Paramètres de requête

- **minutesTimeFrame** : Laps de temps à rebours à partir de maintenant, par minute, pendant lequel les événements ont été signalés.

  **Exemple** :

  `/api/v1/events?minutesTimeFrame=20`

- **type** : Pour filtrer la liste d’événements selon un type spécifique.

  **Exemples** :

  `/api/v1/events?type=DEVICE_CONNECTION_CREATED`

  `/api/v1/events?type=REMOTE_ACCESS&minutesTimeFrame`

#### <a name="response-type"></a>Type de réponse

- **JSON**

#### <a name="response-content"></a>Contenu de la réponse

Tableau d'objets JSON qui représentent les alertes.

#### <a name="event-fields"></a>Champs de l’événement

| Nom | Type | Nullable | Liste de valeurs |
|--|--|--|--|--|
| **timestamp** | Numérique | Non | Époque (UTC) |
| **title** | String | Non | - |
| **severity** | String | Non | INFO, REMARQUE, ou ALERTE |
| **propriétaire** | String | Oui | Si l’événement a été créé manuellement, ce champ inclut le nom de l’utilisateur qui a créé l’événement. |
| **content** | String | Non | - |

#### <a name="response-example"></a>Exemple de réponse

```rest
[

    {
    
        "severity": "INFO",
        
        "title": "Back to Normal",
        
        "timestamp": 1504097077000,
        
        "content": "Device 10.2.1.15 was found responsive, after being suspected as disconnected",
        
        "owner": null,
        
        "type": "BACK_TO_NORMAL"
    
    },
    
    {
    
        "severity": "ALERT",
        
        "title": "Alert Detected",
        
        "timestamp": 1504096909000,
        
        "content": "Device 10.2.1.15 is suspected to be disconnected (unresponsive).",
        
        "owner": null,
        
        "type": "ALERT_REPORTED"
    
    },
    
    {
    
        "severity": "ALERT",
        
        "title": "Alert Detected",
        
        "timestamp": 1504094446000,
        
        "content": "A DNP3 Master 10.2.1.14 attempted to initiate a request which is not allowed by policy.\nThe policy indicates the allowed function codes, address ranges, point indexes and time intervals.\nIt is recommended to notify the security officer of the incident.",
        
        "owner": null,
        
        "type": "ALERT_REPORTED"
    
    },
    
    {
    
        "severity": "NOTICE",
        
        "title": "PLC Program Update",
        
        "timestamp": 1504094344000,
        
        "content": "Program update detected, sent from 10.2.1.25 to 10.2.1.14",
        
        "owner": null,
        
        "type": "PROGRAM_DEVICE"
    
    }

]

```

#### <a name="curl-command"></a>Commande Curl

| Type | API | Exemple |
|--|--|--|
| GET | `curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<IP_ADDRESS>/api/v1/events?minutesTimeFrame=&type='` | `curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" 'https://127.0.0.1/api/v1/events?minutesTimeFrame=20&type=DEVICE_CONNECTION_CREATED'` |

### <a name="retrieve-vulnerability-information---apiv1reportsvulnerabilitiesdevices"></a>Récupérer des informations sur les vulnérabilités – /api/v1/reports/vulnerabilities/devices

Utilisez cette API pour demander les résultats de l’évaluation de vulnérabilité pour chaque appareil.

#### <a name="method"></a>Méthode

- **GET**

#### <a name="response-type"></a>Type de réponse

- **JSON**

#### <a name="response-content"></a>Contenu de la réponse

Tableau d'objets JSON qui représentent les appareils évalués.

L’objet d’appareil contient :

- Données générales

- Score d’évaluation

- Vulnérabilités

#### <a name="device-fields"></a>Champs de l’appareil

| Nom | Type | Nullable | Liste de valeurs |
|--|--|--|--|
| **name** | String | Non | - |
| **ipAddresses** | Tableau JSON | Non | - |
| **securityScore** | Numérique | Non | - |
| **vendor** | String | Oui |  |
| **firmwareVersion** | String | Oui | - |
| **model** | String | Oui | - |
| **isWirelessAccessPoint** | Boolean | Non | True ou false |
| **operatingSystem** | Objet du système d’exploitation | Oui | - |
| **vulnerabilities** | Objet de vulnérabilités | Oui | - |

#### <a name="operating-system-fields"></a>Champs de système d'exploitation

| Nom | Type | Nullable | Liste de valeurs |
|--|--|--|--|
| **Nom** | String | Oui | - |
| **Type** | String | Oui | - |
| **Version** | String | Oui | - |
| **latestVersion** | String | Oui | - |

#### <a name="vulnerabilities-fields"></a>Champs de vulnérabilités
 
| Nom | Type | Nullable | Liste de valeurs |
|--|--|--|--|
| **antiViruses** | Tableau JSON | Oui | Nom des antivirus |
| **plainTextPasswords** | Tableau JSON | Oui | Objets de mot de passe |
| **remoteAccess** | Tableau JSON | Oui | Objets d’accès distant |
| **isBackupServer** | Boolean | Non | True ou false |
| **openedPorts** | Tableau JSON | Oui | Objets de port ouvert |
| **isEngineeringStation** | Boolean | Non | True ou false |
| **isKnownScanner** | Boolean | Non | True ou false |
| **cves** | Tableau JSON | Oui | Objets de CVE |
| **isUnauthorized** | Boolean | Non | True ou false |
| **malwareIndicationsDetected** | Boolean | Non | True ou false |
| **weakAuthentication** | Tableau JSON | Oui | Applications détectées qui utilisent une authentification faible |

#### <a name="password-fields"></a>Champs de mot de passe

| Nom | Type | Nullable | Liste de valeurs |
|--|--|--|--|
| **mot de passe** | String | Non | - |
| **protocol** | String | Non | - |
| **strength** | String | Non | Très faible, Faible, Moyen ou Fort |

#### <a name="remote-access-fields"></a>Champs d'accès à distance

| Nom | Type | Nullable | Liste de valeurs |
|--|--|--|--|
| **port** | Numérique | Non | - |
| **transport** | String | Non | TCP ou UDP |
| **client** | String | Non | Adresse IP |
| **clientSoftware** | String | Non | SSH, VNC, Bureau à distance ou Team Viewer |

#### <a name="open-port-fields"></a>Champs de port ouvert

| Nom | Type | Nullable | Liste de valeurs |
|--|--|--|--|
| **port** | Numérique | Non | - |
| **transport** | String | Non | TCP ou UDP |
| **protocol** | String | Oui | - |
| **isConflictingWithFirewall** | Boolean | Non | True ou false |

#### <a name="cve-fields"></a>Champs CVE

| Nom | Type | Nullable | Liste de valeurs |
|--|--|--|--|
| **Identifiant** | String | Non | - |
| **score** | Numérique | Non | Double |
| **description** | String | Non | - |

#### <a name="response-example"></a>Exemple de réponse

```rest
[

    {
    
        "name": "IED \#10",
        
        "ipAddresses": ["10.2.1.10"],
        
        "securityScore": 100,
        
        "vendor": "ABB Switzerland Ltd, Power Systems",
        
        "firmwareVersion": null,
        
        "model": null,
        
        "operatingSystem": {
        
            "name": "ABB Switzerland Ltd, Power Systems",
            
            "type": "abb",
            
            "version": null,
            
            "latestVersion": null
        
        },
        
        "vulnerabilities": {
            
        "antiViruses": [
            
        "McAfee"
            
        ],
            
        "plainTextPasswords": [
            
            {
            
                "password": "123456",
                
                "protocol": "HTTP",
                
                "lastSeen": 1462726930471,
                
                "strength": "Very Weak"
            
            }
            
        ],
            
        "remoteAccess": [
            
            {
            
                "port": 5900,
                
                "transport": "TCP",
                
                "clientSoftware": "VNC",
                
                "client": "10.2.1.20"
            
            }
            
        ],
            
        "isBackupServer": true,
            
        "openedPorts": [
            
            {
            
                "port": 445,
                
                "transport": "TCP",
                
                "protocol": "SMP Over IP",
                
                "isConflictingWithFirewall": false
            
            },
            
            {
            
                "port": 80,
                
                "transport": "TCP",
                
                "protocol": "HTTP",
                
                "isConflictingWithFirewall": false
            
            }
            
        ],
            
        "isEngineeringStation": false,
            
        "isKnownScanner": false,
            
        "cves": [
            
            {
            
                "id": "CVE-2015-6490",
                
                "score": 10,
                
                "description": "Frosty URL - Stack-based buffer overflow on Allen-Bradley MicroLogix 1100 devices before B FRN 15.000 and 1400 devices through B FRN 15.003 allows remote attackers to execute arbitrary code via unspecified vectors"
            
            },
            
            {
            
                "id": "CVE-2012-6437",
                
                "score": 10,
                
                "description": "MicroLogix 1100 and 1400 do not properly perform authentication for Ethernet firmware updates, which allows remote attackers to execute arbitrary code via a Trojan horse update image"
            
            },
            
            {
            
                "id": "CVE-2012-6440",
                
                "score": 9.3,
                
                "description": "MicroLogix 1100 and 1400 allows man-in-the-middle attackers to conduct replay attacks via HTTP traffic."
        
            }
        
        ],
        
        "isUnauthorized": false,
        
        "malwareIndicationsDetected": true
        
        }
    
    }

]
```

#### <a name="curl-command"></a>Commande Curl

| Type | API | Exemple |
|--|--|--|
| GET | `curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/reports/vulnerabilities/devices` | `curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https://127.0.0.1/api/v1/reports/vulnerabilities/devices` |

### <a name="retrieve-security-vulnerabilities---apiv1reportsvulnerabilitiessecurity"></a>Récupérer les failles de sécurité – /api/v1/reports/vulnerabilities/security

Utilisez cette API pour demander les résultats de l’évaluation de vulnérabilité générale. Cette évaluation fournit des informations sur le niveau de sécurité de votre système.

Cette évaluation est basée sur des informations générales sur le réseau et le système, et non sur l’évaluation d’un appareil spécifique.

#### <a name="method"></a>Méthode

- **GET**

#### <a name="response-type"></a>Type de réponse

- **JSON**

#### <a name="response-content"></a>Contenu de la réponse

Objet JSON qui représente les résultats évalués. Chaque clé peut accepter la valeur Null. Sinon, elle contient un objet JSON avec des clés qui n’acceptent pas la valeur Null.

#### <a name="result-fields"></a>Champs de résultats

- **Clés**

    **unauthorizedDevices**

    | Nom du champ | Type | Liste de valeurs |
    | ---------- | ---- | -------------- |
    | **address** | String | Adresse IP |
    | **name** | String | - |
    | **firstDetectionTime** | Numérique | Époque (UTC) |
    | lastSeen | Numérique | Époque (UTC) |

    **illegalTrafficByFirewallRules**

    | Nom du champ | Type | Liste de valeurs |
    | ---------- | ---- | -------------- |
    | **server** | String | Adresse IP |
    | **client** | String | Adresse IP |
    | **port** | Numérique | - |
    | **transport** | String | TCP, UDP ou ICMP |

    **weakFirewallRules**

    | Nom du champ | Type | Liste de valeurs |
    | ---------- | ---- | -------------- |
    | **sources** | Tableau de sources JSON. Chaque source peut être dans l’un des quatre formats. | « Any », « ip address (Host) », « from ip-to ip (RANGE) », « ip address, subnet mask (NETWORK) » |
    | **destinations** | Tableau de destinations JSON. Chaque destination peut être dans l’un des quatre formats. | « Any », « ip address (Host) », « from ip-to ip (RANGE) », « ip address, subnet mask (NETWORK) » |
    | **ports** | Tableau de ports JSON dans l’un des trois formats | « Any », « port (protocol, if detected) », « from port-to port (protocol, if detected) » |

    **accessPoints**

    | Nom du champ | Type | Liste de valeurs |
    | ---------- | ---- | -------------- |
    | **macAddress** | String | Adresse MAC |
    | **vendor** | String | Nom du fournisseur |
    | **ipAddress** | String | Adresse IP ou N/A |
    | **name** | String | Nom de l’appareil ou N/A |
    | **wireless** | String | Non, Soupçonné ou Oui |

    **connectionsBetweenSubnets**

    | Nom du champ | Type | Liste de valeurs |
    | ---------- | ---- | -------------- |
    | **server** | String | Adresse IP |
    | **client** | String | Adresse IP |

    **industrialMalwareIndicators**

    | Nom du champ | Type | Liste de valeurs |
    | ---------- | ---- | -------------- |
    | **detectionTime** | Numérique | Époque (UTC) |
    | **alertMessage** | String | - |
    | **description** | String | - |
    | **devices** | Tableau JSON | Noms des appareils |

    **internetConnections**

    | Nom du champ | Type | Liste de valeurs |
    | ---------- | ---- | -------------- |
    | **internalAddress** | String | Adresse IP |
    | **autorisé** | Boolean | Oui ou Non |
    | **externalAddresses** | Tableau JSON | Adresse IP |

#### <a name="response-example"></a>Exemple de réponse

```rest
{

    "unauthorizedDevices": [
    
        {
        
            "address": "10.2.1.14",
            
            "name": "PLC \#14",
            
            "firstDetectionTime": 1462645483000,
            
            "lastSeen": 1462645495000,
        
        }
    
    ],
    
    "redundantFirewallRules": [
    
        {
        
            "sources": "170.39.3.0/255.255.255.0",
            
            "destinations": "Any",
            
            "ports": "102"
        
        }
    
    ],
    
    "connectionsBetweenSubnets": [
    
        {
        
            "server": "10.2.1.22",
            
            "client": "170.39.2.0"
        
        }
    
    ],
    
    "industrialMalwareIndications": [
    
        {
        
            "detectionTime": 1462645483000,
            
            "alertMessage": "Suspicion of Malicious Activity (Regin)",
            
            "description": "Suspicious network activity was detected. Such behavior might be attributed to the Regin malware.",
            
            "addresses": [
            
                "10.2.1.4",
                
                "10.2.1.5"
            
            ]
        
        }
    
    ],
    
    "illegalTrafficByFirewallRules": [
    
        {
        
            "server": "10.2.1.7",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        },
    
        {
        
            "server": "10.2.1.8",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        },
    
        {
        
            "server": "10.2.1.9",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        }
    
    ],
    
    "internetConnections": [
    
        {
        
            "internalAddress": "10.2.1.1",
            
            "authorized": "Yes",
            
            "externalAddresses": ["10.2.1.2",”10.2.1.3”]
        
        }
    
    ],
    
    "accessPoints": [
    
        {
        
            "macAddress": "ec:08:6b:0f:1e:22",
            
            "vendor": "TP-LINK TECHNOLOGIES",
            
            "ipAddress": "173.194.112.22",
            
            "name": "Enterprise AP",
            
            "wireless": "Yes"
        
        }
    
    ],
    
    "weakFirewallRules": [
    
        {
        
            "sources": "170.39.3.0/255.255.255.0",
            
            "destinations": "Any",
            
            "ports": "102"
        
        }
    
    ]

}
```

#### <a name="curl-command"></a>Commande Curl

| Type | API | Exemple |
|--|--|--|
| GET | `curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/reports/vulnerabilities/security` | `curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https://127.0.0.1/api/v1/reports/vulnerabilities/security` |

### <a name="retrieve-operational-vulnerabilities---apiv1reportsvulnerabilitiesoperational"></a>Récupérer les vulnérabilités opérationnelles – /api/v1/reports/vulnerabilities/operational

Utilisez cette API pour demander les résultats de l’évaluation de vulnérabilité générale. Cette évaluation fournit un aperçu de l’état opérationnel de votre réseau. Elle est basée sur des informations générales sur le réseau et le système, et non sur l’évaluation d’un appareil spécifique.

#### <a name="method"></a>Méthode

- **GET**

#### <a name="response-type"></a>Type de réponse

- **JSON**

#### <a name="response-content"></a>Contenu de la réponse

Objet JSON qui représente les résultats évalués. Chaque clé contient un tableau de résultats JSON.

#### <a name="result-fields"></a>Champs de résultats

- **Clés**

    **backupServer**

    | Nom du champ | Type | Liste de valeurs |
    |--|--|--|
    | **source** | String | Adresse IP |
    | **destination** | String | Adresse IP |
    | **port** | Numérique | - |
    | **transport** | String | TCP ou UDP |
    | **backupMaximalInterval** | String | - |
    | **lastSeenBackup** | Numérique | Époque (UTC) |

    **ipNetworks**

    | Nom du champ | Type | Liste de valeurs |
    |--|--|--|
    | **addresse** s | Numérique | - |
    | **network** | String | Adresse IP |
    | **mask** | String | Masque de sous-réseau |

    **protocolProblems**

    | Nom du champ | Type | Liste de valeurs |
    |--|--|--|
    | **protocol** | String | - |
    | **addresses** | Tableau JSON | Adresses IP |
    | **alert** | String | - |
    | **reportTime** | Numérique | Époque (UTC) |

    **protocolDataVolumes**

    | Nom du champ | Type | Liste de valeurs |
    |--|--|--|
    | protocol | String | - |
    | volume | String | « volume number MB » |

    **disconnections**

    | Nom du champ | Type | Liste de valeurs |
    |--|--|--|
    | **assetAddress** | String | Adresse IP |
    | **assetName** | String | - |
    | **lastDetectionTime** | Numérique | Époque (UTC) |
    | **backToNormalTime** | Numérique | Époque (UTC) |

#### <a name="response-example"></a>Exemple de réponse

```rest
{

    "backupServer": [
    
        {
        
            "backupMaximalInterval": "1 Hour, 29 Minutes",
            
            "source": "10.2.1.22",
            
            "destination": "170.39.2.14",
            
            "port": 10000,
            
            "transport": "TCP",
            
            "lastSeenBackup": 1462645483000
        
        }
    
    ],

    "ipNetworks": [
    
        {
        
            "addresses": "21",
            
            "network": "10.2.1.0",
            
            "mask": "255.255.255.0"
        
        },
        
        {
        
            "addresses": "3",
            
            "network": "170.39.2.0",
            
            "mask": "255.255.255.0"
        
        }
    
    ],

    "protocolProblems": [
    
        {
        
            "protocol": "DNP3",
            
            "addresses": [
            
                "10.2.1.7",
                
                "10.2.1.8"
            
            ],
            
            "alert": "Illegal DNP3 Operation",
            
            "reportTime": 1462645483000
        
        },
        
        {
        
            "protocol": "DNP3",
            
            "addresses": [
            
                "10.2.1.15"
            
            ],
            
            "alert": "Master Requested an Application Layer Confirmation",
            
            "reportTime": 1462645483000
        
        }
        
    ],

    "protocolDataVolumes": [
    
        {
        
            "protocol": "MODBUS (502)",
            
            "volume": "21.07 MB"
        
        },
        
        {
        
            "protocol": "SSH (22)",
            
            "volumn": "0.001 MB"
        
        }
    
    ],
    
    "disconnections": [
    
        {
        
            "assetAddress": "10.2.1.3",
            
            "assetName": "PLC \#3",
            
            "lastDetectionTime": 1462645483000,
            
            "backToNormalTime": 1462645484000
        
        }
    
    ]

}
```

#### <a name="curl-command"></a>Commande Curl

| Type | API | Exemple |
|--|--|--|
| GET | `curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/reports/vulnerabilities/operational` | `curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https://127.0.0.1/api/v1/reports/vulnerabilities/operational` |

### <a name="validate-user-credentials---apiexternalauthenticationvalidation"></a>Valider les informations d’identification d’utilisateur – /api/external/authentication/validation

Utilisez cette API pour valider un nom d’utilisateur et un mot de passe Defender pour IoT. Tous les rôles d’utilisateur Defender pour IoT peuvent fonctionner avec l’API.

Vous n’avez pas besoin d’un jeton d’accès Defender pour IoT pour utiliser cette API.

#### <a name="method"></a>Méthode

- **POST**

#### <a name="request-type"></a>Type de demande

- **JSON**

#### <a name="query-parameters"></a>Paramètres de requête

| **Nom** | **Type** | **Nullable** |
|--|--|--|
| **username** | String | Non |
| **mot de passe** | String | Non |

#### <a name="request-example"></a>Exemple de requête

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!"

}

```

#### <a name="response-type"></a>Type de réponse

- **JSON**

#### <a name="response-content"></a>Contenu de la réponse

Chaîne de message avec les détails de l’état de l’opération :

- **Success - msg** : Authentification réussie

- **Failure - error** : Échec de la validation des informations d'identification

#### <a name="response-example"></a>Exemple de réponse

```rest
response:

{

    "msg": "Authentication succeeded."

}
```

#### <a name="curl-command"></a>Commande Curl

| Type | API | Exemple |
|--|--|--|
| GET | `curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/external/authentication/validation` | `curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https://127.0.0.1/api/external/authentication/validation` |

### <a name="change-password---externalauthenticationset_password"></a>Modifier le mot de passe – /external/authentication/set_password

Utilisez cette API pour permettre aux utilisateurs de modifier leur mot de passe. Tous les rôles d’utilisateur Defender pour IoT peuvent fonctionner avec l’API. Vous n’avez pas besoin d’un jeton d’accès Defender pour IoT pour utiliser cette API.

#### <a name="method"></a>Méthode

- **POST**

#### <a name="request-type"></a>Type de demande

- **JSON**

#### <a name="request-example"></a>Exemple de requête

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}
```

#### <a name="response-type"></a>Type de réponse

- **JSON**

#### <a name="response-content"></a>Contenu de la réponse

Chaîne de message avec les détails de l’état de l’opération :

- **Success – msg** : Le mot de passe a été remplacé

- **Failure – error** : Échec d'authentification de l’utilisateur

- **Failure – error** : Le mot de passe ne respecte pas la stratégie de sécurité

#### <a name="response-example"></a>Exemple de réponse

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}
```

#### <a name="device-fields"></a>Champs de l’appareil

| **Nom** | **Type** | **Nullable** |
|--|--|--|
| **username** | String | Non |
| **mot de passe** | String | Non |
| **new_password** | String | Non |

#### <a name="curl-command"></a>Commande Curl

| Type | API | Exemple |
|--|--|--|
| POST | `curl -k -d '{"username": "<USER_NAME>","password": "<CURRENT_PASSWORD>","new_password": "<NEW_PASSWORD>"}' -H 'Content-Type: application/json'  https://<IP_ADDRESS>/api/external/authentication/set_password` | `curl -k -d '{"username": "myUser","password": "1234@abcd","new_password": "abcd@1234"}' -H 'Content-Type: application/json'  https://127.0.0.1/api/external/authentication/set_password` |

### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>Mise à jour du mot de passe de l’utilisateur par l’administrateur système – /external/authentication/set_password_by_admin

Utilisez cette API pour permettre aux administrateurs système de modifier les mots de passe des utilisateurs spécifiés. Tous les rôles d’utilisateur Administrateur Defender pour IoT peuvent fonctionner avec l’API. Vous n’avez pas besoin d’un jeton d’accès Defender pour IoT pour utiliser cette API.

#### <a name="method"></a>Méthode

- **POST**

#### <a name="request-type"></a>Type de demande

- **JSON**

#### <a name="request-example"></a>Exemple de requête

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}
```

#### <a name="response-type"></a>Type de réponse

- **JSON**

#### <a name="response-content"></a>Contenu de la réponse

Chaîne de message avec les détails de l’état de l’opération :

- **Success – msg** : Le mot de passe a été remplacé

- **Failure – error** : Échec d'authentification de l’utilisateur

- **Failure – error** : L'utilisateur n'existe pas

- **Failure – error** : Le mot de passe ne respecte pas la stratégie de sécurité

- **Failure – error** : L'utilisateur n'a pas l'autorisation de changer de mot de passe

#### <a name="response-example"></a>Exemple de réponse

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "The user 'test_user' doesn't exist",
        
        "internalSystemErrorMessage": "The user 'yoavfe' doesn't exist"
    
    }

}

```

#### <a name="device-fields"></a>Champs de l’appareil

| **Nom** | **Type** | **Nullable** |
|--|--|--|
| **admin_username** | String | Non |
| **admin_password** | String | Non |
| **username** | String | Non |
| **new_password** | String | Non |

#### <a name="curl-command"></a>Commande Curl

> [!div class="mx-tdBreakAll"]
> | Type | API | Exemple |
> |--|--|--|
> | POST | `curl -k -d '{"admin_username":"<ADMIN_USERNAME>","admin_password":"<ADMIN_PASSWORD>","username": "<USER_NAME>","new_password": "<NEW_PASSWORD>"}' -H 'Content-Type: application/json'  https://<IP_ADDRESS>/api/external/authentication/set_password_by_admin` | `curl -k -d '{"admin_user":"adminUser","admin_password": "1234@abcd","username": "myUser","new_password": "abcd@1234"}' -H 'Content-Type: application/json'  https://127.0.0.1/api/external/authentication/set_password_by_admin` |

### <a name="retrieve-alert-pcap---apiv2alertspcap"></a>Récupérer une alerte PCAP – /api/v2/alerts/pcap

Utilisez cette API pour récupérer un fichier PCAP lié à une alerte.

Ce point de terminaison n’utilise pas de jeton d’accès ordinaire pour l’autorisation. Au lieu de cela, il nécessite un jeton spécial créé par le point de terminaison d’API `/external/v2/alerts/pcap` sur le CM.

#### <a name="method"></a>Méthode

- **GET**

#### <a name="query-parameters"></a>Paramètres de requête

- id : Identifiant de l’alerte Xsense  
Exemple :  
`/api/v2/alerts/pcap/<id>`

#### <a name="response-type"></a>Type de réponse

- **JSON**

#### <a name="response-content"></a>Contenu de la réponse

- **Success** : Fichier binaire contenant des données PCAP
- **Failure** : Objet JSON qui contient un message d’erreur

#### <a name="response-example"></a>Exemple de réponse

#### <a name="error"></a>Erreur

```json
{
  "error": "PCAP file is not available"
}
```

#### <a name="curl-command"></a>Commande Curl

|Type|API|Exemple|
|-|-|-|
|GET|`curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<IP_ADDRESS>/api/v2/alerts/pcap/<ID>'`|`curl -k -H "Authorization: d2791f58-2a88-34fd-ae5c-2651fe30a63c" 'https://10.1.0.2/api/v2/alerts/pcap/1'`|

## <a name="on-premises-management-console-api-specifications"></a>Spécifications des API de la console de gestion locale

Cette section décrit les API de la console de gestion locale pour :

- [Exclusions des alertes](#alert-exclusions)

- [Récupérer des informations sur les appareils – /external/v1/devices](#retrieve-device-information---externalv1devices)

- [Récupérer des informations sur les alertes – /external/v1/alerts](#retrieve-alert-information---externalv1alerts)

- [Alertes QRadar](#qradar-alerts)

- [Exclusions d’alerte (fenêtre de maintenance) – /external/v1/maintenanceWindow](#alert-exclusions-maintenance-window---externalv1maintenancewindow)

- [Modifier le mot de passe – /external/authentication/set_password (1)](#change-password---externalauthenticationset_password-1)

- [Mise à jour du mot de passe de l’utilisateur par l’administrateur système – /external/authentication/set_password_by_admin](#user-password-update-by-system-admin---externalauthenticationset_password_by_admin)

- [Demander une alerte PCAP – /external/v2/alerts/pcap](#request-alert-pcap---externalv2alertspcap)

### <a name="alert-exclusions"></a>Exclusions des alertes

Définissez les conditions dans lesquelles les alertes ne seront pas envoyées. Par exemple, définissez et mettez à jour les heures d’arrêt et de démarrage, les appareils ou sous-réseaux qui doivent être exclus lors du déclenchement d’alertes ou les moteurs Defender pour IoT qui doivent être exclus. Par exemple, au cours d’une fenêtre de maintenance, vous souhaiterez peut-être arrêter l’envoi de toutes les alertes, à l’exception des alertes de programmes malveillants sur les appareils critiques. Les éléments que vous définissez ici apparaissent dans la fenêtre Exclusions d’alertes de la console de gestion locale en tant que règles d’exclusion en lecture seule.

#### <a name="externalv1maintenancewindow"></a>/external/v1/maintenanceWindow

- **/external/authentication/validation**

- **Exemple de réponse**

- **réponse :**

```rest
{
    "msg": "Authentication succeeded."
}
```

### <a name="retrieve-device-information---externalv1devices"></a>Récupérer des informations sur les appareils – /external/v1/devices

Cette API demande une liste de tous les appareils détectés par les capteurs Defender pour IoT connectés à une console de gestion locale.

#### <a name="method"></a>Méthode

- **GET**

#### <a name="response-type"></a>Type de réponse

- **JSON**

#### <a name="response-content"></a>Contenu de la réponse

Tableau d'objets JSON qui représentent les appareils.

#### <a name="query-parameters"></a>Paramètres de requête

- **autorisé** : Pour filtrer uniquement les appareils autorisés et non autorisés.

- **siteId** : Pour filtrer uniquement les appareils liés à des sites spécifiques.

- **zoneId** : Pour filtrer uniquement les appareils liés à des zones spécifiques. [1](#1)

- **sensorId** : Pour filtrer uniquement les appareils détectés par des capteurs spécifiques. [1](#1)

###### <a name="you-might-not-have-the-site-and-zone-id-if-this-is-the-case-query-all-devices-to-retrieve-the-site-and-zone-id"></a><a id="1">1</a> *Vous ne disposez peut-être pas de l’ID de site et de zone. Si tel est le cas, interrogez tous les appareils pour récupérer l’ID de site et de zone.*

#### <a name="query-parameters-example"></a>Exemples de paramètres de requête

`/external/v1/devices?authorized=true`

`/external/v1/devices?authorized=false`

`/external/v1/devices?siteId=1,2`

`/external/v1/devices?zoneId=5,6`

`/external/v1/devices?sensorId=8`

#### <a name="device-fields"></a>Champs de l’appareil

| Nom | Type | Nullable | Liste de valeurs |
|--|--|--|--|
| **sensorId** | Numérique | Non | - |
| **zoneId** | Numérique | Oui | - |
| **siteId** | Numérique | Oui | - |
| **ipAddresses** | Tableau JSON | Oui | Adresses IP (il peut y avoir plusieurs adresses en cas d’adresses Internet ou d’appareil avec deux cartes réseau) |
| **name** | String | Non | - |
| **type** | String | Non | Inconnu, Station d’ingénierie, PLC, HMI, Historien, Contrôleur de domaine, Serveur de base de données, Point d’accès sans fil, Routeur, Commutateur, Serveur, Station de travail, Caméra IP, Imprimante, Pare-feu, Station de terminal, Passerelle VPN, Internet, ou Multidiffusion et Diffusion |
| **macAddresses** | Tableau JSON | Oui | Adresses MAC (il peut y avoir plusieurs adresses en cas d’appareil avec deux cartes réseau) |
| **operatingSystem** | String | Oui | - |
| **engineeringStation** | Boolean | Non | True ou false |
| **scanner** | Boolean | Non | True ou false |
| **autorisé** | Boolean | Non | True ou false |
| **vendor** | String | Oui | - |
| **Protocoles** | Tableau JSON | Oui | Objet de protocole |
| **firmware** | Tableau JSON | Oui | Objet de microprogramme |

#### <a name="protocol-fields"></a>Champs de protocole

| Nom | Type | Nullable | Liste de valeurs |
|--|--|--|--|
| Nom | String | Non | - |
| Adresses | Tableau JSON | Oui | Valeurs de type Master ou numérique |

#### <a name="firmware-fields"></a>Champs du microprogramme

| Nom | Type | Nullable | Liste de valeurs |
|--|--|--|--|
| **serial** | String | Non | N/A ou la valeur réelle |
| **model** | String | Non | N/A ou la valeur réelle |
| **firmwareVersion** | Double | Non | N/A ou la valeur réelle |
| **additionalData** | String | Non | N/A ou la valeur réelle |
| **moduleAddress** | String | Non | N/A ou la valeur réelle |
| **rack** | String | Non | N/A ou la valeur réelle |
| **slot** | String | Non | N/A ou la valeur réelle |
| **address** | String | Non | N/A ou la valeur réelle |

#### <a name="response-example"></a>Exemple de réponse

```rest
[

    {
    
    "sensorId": 7,
    
    "zoneId": 1,
    
    "siteId": 1,
    
    "vendor": null,
    
    "name": "10.4.14.102",
    
    "firmware": [
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 0, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 0, Unit: Unknown (0x3)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 3, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: 3, Node: 0, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    }

],

"id": 79,

"macAddresses": null,

"authorized": true,

"ipAddresses": [

    "10.4.14.102"

],

"engineeringStation": false,

"type": "PLC",

"operatingSystem": null,

"protocols": [

    {
    
        "addresses": [],
        
        "id": 62,
        
        "name": "Omron FINS"
    
    }

],

"scanner": false

}

]
```

#### <a name="curl-command"></a>Commande Curl

| Type | API | Exemple |
|--|--|--|
| GET | `curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<>IP_ADDRESS>/external/v1/devices?siteId=&zoneId=&sensorId=&authorized='` | `curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" 'https://127.0.0.1/external/v1/devices?siteId=1&zoneId=2&sensorId=5&authorized=true'` |

### <a name="retrieve-alert-information---externalv1alerts"></a>Récupérer des informations sur les alertes – /external/v1/alerts

Utilisez cette API pour récupérer toutes les alertes ou les alertes filtrées à partir d’une console de gestion locale.

#### <a name="method"></a>Méthode

- **GET**

#### <a name="query-parameters"></a>Paramètres de requête

- **state** : Pour filtrer uniquement les alertes gérées et non gérées.

  **Exemple** :

  `/api/v1/alerts?state=handled`

- **fromTime** : Pour filtrer les alertes créées à partir d’une heure spécifique (en millisecondes, UTC).

  **Exemple** :

  `/api/v1/alerts?fromTime=<epoch>`

- **toTime** : Pour filtrer les alertes créées uniquement avant une heure spécifique (en millisecondes, UTC).

  **Exemple** :

  `/api/v1/alerts?toTime=<epoch>`

- **siteId** : Site sur lequel l’alerte a été découverte.
- **zoneId** : Zone dans laquelle l’alerte a été découverte.
- **sensor** : Capteur sur lequel l’alerte a été découverte.

*Vous ne disposez peut-être pas de l’ID de site et de zone. Si tel est le cas, interrogez tous les appareils pour récupérer l’ID de site et de zone.*

#### <a name="alert-fields"></a>Champs d’alerte 

| Nom | Type | Nullable | Liste de valeurs |
|--|--|--|--|
| **Identifiant** | Numérique | Non | - |
| **time** | Numérique | Non | Époque (UTC) |
| **title** | String | Non | - |
| **message** | String | Non | - |
| **severity** | String | Non | Avertissement, Mineur, Majeur ou Critique |
| **engine** | String | Non | Violation de protocole, Violation de stratégie, Programme malveillant, Anomalie ou Opérationnelle |
| **sourceDevice** | Numérique | Oui | ID de périphérique |
| **destinationDevice** | Numérique | Oui | ID de périphérique |
| **sourceDeviceAddress** | Numérique | Oui | IP, MAC |
| **destinationDeviceAddress** | Numérique | Oui | IP, MAC |
| **remediationSteps** | String | Oui | Étapes de correction affichées dans l’alerte|
| **sensorName** | String | Oui | Le nom du capteur défini par l’utilisateur |
|**zoneName** | String | Oui | Le nom de la zone associée au capteur|
| **siteName** | String | Oui | Le nom du site associé au capteur |
| **additionalInformation** | Objet d’informations supplémentaires | Oui | - |

Notez que /api/v2/ est nécessaire pour les informations suivantes :

- sourceDeviceAddress 
- destinationDeviceAddress
- remediationSteps
- sensorName
- zoneName
- siteName

#### <a name="additional-information-fields"></a>Champs d’informations supplémentaires

| Nom | Type | Nullable | Liste de valeurs |
|--|--|--|--|
| **description** | String | Non | - |
| **information** | Tableau JSON | Non | String |

#### <a name="response-example"></a>Exemple de réponse

```rest
[
    {
        "engine": "Operational",
        "handled": false,
        "title": "Traffic Detected on sensor Interface",
        "additionalInformation": null,
        "sourceDevice": 0,
        "zoneId": 1,
        "siteId": 1,
        "time": 1594808245000,
        "sensorId": 1,
        "message": "The sensor resumed detecting network traffic on ens224.",
        "destinationDevice": 0,
        "id": 1,
        "severity": "Warning"
    },
    {
        "engine": "Anomaly",
        "handled": false,
        "title": "Address Scan Detected",
        "additionalInformation": null,
        "sourceDevice": 4,
        "zoneId": 1,
        "siteId": 1,
        "time": 1594808260000,
        "sensorId": 1,
        "message": "Address scan detected.\nScanning address: 10.10.10.22\nScanned subnet: 10.11.0.0/16\nScanned addresses: 10.11.1.1, 10.11.20.1, 10.11.20.10, 10.11.20.100, 10.11.20.2, 10.11.20.3, 10.11.20.4, 10.11.20.5, 10.11.20.6, 10.11.20.7...\nIt is recommended to notify the security officer of the incident.",
        "destinationDevice": 0,
        "id": 2,
        "severity": "Critical"
    },
    {
        "engine": "Operational",
        "handled": false,
        "title": "Suspicion of Unresponsive MODBUS Device",
        "additionalInformation": null,
        "sourceDevice": 194,
        "zoneId": 1,
        "siteId": 1,
        "time": 1594808285000,
        "sensorId": 1,
        "message": "Outstation device 10.13.10.1 (Protocol Address 53) seems to be unresponsive to MODBUS requests.",
        "destinationDevice": 0,
        "id": 3,
        "severity": "Minor"
    }
]
```

#### <a name="curl-command"></a>Commande Curl

> [!div class="mx-tdBreakAll"]
> | Type | API | Exemple |
> |--|--|--|
> | GET | `curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<>IP_ADDRESS>/external/v1/alerts?state=&zoneId=&fromTime=&toTime=&siteId=&sensor='` | `curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" 'https://127.0.0.1/external/v1/alerts?state=unhandled&zoneId=1&fromTime=0&toTime=1594551777000&siteId=1&sensor=1'` |

### <a name="qradar-alerts"></a>Alertes QRadar

L’intégration de QRadar à Defender pour IoT vous aide à identifier les alertes générées par Defender pour IoT et à effectuer des actions avec ces alertes. QRadar reçoit les données de Defender pour IoT, puis contacte le composant de la console de gestion locale de l’API publique.

Pour envoyer les données découvertes par Defender pour IoT à QRadar, définissez une règle de transfert dans le système Defender pour IoT et sélectionnez l’option **Gestion des alertes de support à distance**.

:::image type="content" source="media/references-work-with-defender-for-iot-apis/edit-forwarding-rules.png" alt-text="Modifiez les règles de transfert en fonction de vos besoins.":::

Lorsque vous sélectionnez cette option pendant le processus de configuration des règles de transfert, les champs supplémentaires suivants s’affichent dans QRadar :

- **UUID** : Identificateur d’alerte unique, par exemple 1-1555245116250.

- **Site**: Site sur lequel l’alerte a été découverte.

- **Zone** : Zone dans laquelle l’alerte a été découverte.

Exemple de la charge utile envoyée à QRadar :

```
<9>May 5 12:29:23 sensor_Agent LEEF:1.0|CyberX|CyberX platform|2.5.0|CyberX platform Alert|devTime=May 05 2019 15:28:54 devTimeFormat=MMM dd yyyy HH:mm:ss sev=2 cat=XSense Alerts title=Device is Suspected to be Disconnected (Unresponsive) score=81 reporter=192.168.219.50 rta=0 alertId=6 engine=Operational senderName=sensor Agent UUID=5-1557059334000 site=Site zone=Zone actions=handle dst=192.168.2.2 dstName=192.168.2.2 msg=Device 192.168.2.2 is suspected to be disconnected (unresponsive).
```

#### <a name="externalv1alertsltuuidgt"></a>/external/v1/alerts/&lt;UUID&gt;

#### <a name="method"></a>Méthode

**PUT**

#### <a name="request-type"></a>Type de demande

- **JSON**

#### <a name="request-content"></a>Contenu de la demande

Objet JSON qui représente l’action à effectuer sur l’alerte qui contient l’UUID.

#### <a name="action-fields"></a>Champs d’action

| Nom | Type | Nullable | Liste de valeurs |
|--|--|--|--|
| **action** | String | Non | handle ou handleAndLearn |

#### <a name="request-example"></a>Exemple de requête

```rest
{
    "action": "handle"
}

```

#### <a name="response-type"></a>Type de réponse

- **JSON**

#### <a name="response-content"></a>Contenu de la réponse

Tableau d'objets JSON qui représentent les appareils.

#### <a name="response-fields"></a>Champs de réponse

| Nom | Type | Nullable | Description |
|--|--|--|--|
| **content / error** | String | Non | Si la requête réussit, la propriété content s’affiche. Dans le cas contraire, la propriété error s’affiche. |

#### <a name="possible-content-values"></a>Valeurs content possibles

| Code d’état | Valeur content | Description |
|--|--|--|
| 200 | La requête de mise à jour d’alerte s’est terminée correctement. | La requête de mise à jour s’est terminée correctement. Aucun commentaire. |
| 200 | L’alerte a déjà été gérée (**handle**). | L’alerte a déjà été gérée lors de la réception d’une requête handle pour l’alerte.<br />L’alerte reste à l’état **handled**. |
| 200 | L’alerte a déjà été gérée et acquise (**handleAndLearn**). | L’alerte a déjà été gérée et acquise lors de la réception d’une requête **handleAndLearn**.<br />L’alerte reste à l’état **handledAndLearn**. |
| 200 | L’alerte a déjà été gérée (**handled**).<br />La gestion et l’acquisition (**handleAndLearn**) ont été exécutées sur l’alerte. | L’alerte a déjà été gérée lors de la réception d’une requête **handleAndLearn**.<br />L’alerte prend l’état **handleAndLearn**. |
| 200 | L’alerte a déjà été gérée et acquise (**handleAndLearn**). Requête handle ignorée. | L’alerte a déjà pris l’état **handleAndLearn** lors de la réception d’une requête de gestion de l’alerte. L’alerte reste à l’état **handleAndLearn**. |
| 500 | Action non valide. | L’action qui a été envoyée n’est pas une action valide à effectuer sur l’alerte. |
| 500 | Une erreur inattendue s'est produite. | Une erreur inattendue s’est produite. Pour résoudre le problème, contactez le support technique. |
| 500 | Impossible d’exécuter la requête, car aucune alerte n’a été trouvée pour cet UUID. | L’UUID d’alerte spécifié est introuvable dans le système. |

#### <a name="response-example"></a>Exemple de réponse

**Réussi**

```rest
{
    "content": "Alert update request finished successfully"
}
```

**Échec**

```rest
{
    "error": "Invalid action"
}
```

#### <a name="curl-command"></a>Commande Curl

| Type | API | Exemple |
|--|--|--|
| PUT | `curl -k -X PUT -d '{"action": "<ACTION>"}' -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/external/v1/alerts/<UUID>` | `curl -k -X PUT -d '{"action": "handle"}' -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https://127.0.0.1/external/v1/alerts/1-1594550943000` |

### <a name="alert-exclusions-maintenance-window---externalv1maintenancewindow"></a>Exclusions d’alerte (fenêtre de maintenance) – /external/v1/maintenanceWindow

Définissez les conditions dans lesquelles les alertes ne seront pas envoyées. Par exemple, définissez et mettez à jour les heures d’arrêt et de démarrage, les appareils ou sous-réseaux qui doivent être exclus lors du déclenchement d’alertes ou les moteurs Defender pour IoT qui doivent être exclus. Par exemple, au cours d’une fenêtre de maintenance, vous souhaiterez peut-être arrêter l’envoi de toutes les alertes, à l’exception des alertes de programmes malveillants sur les appareils critiques.

Les API que vous définissez ici apparaissent dans la fenêtre de la console de gestion locale **Exclusions d’alerte** comme une règle d’exclusion en lecture seule.

:::image type="content" source="media/references-work-with-defender-for-iot-apis/alert-exclusion-window.png" alt-text="La fenêtre Exclusions d’alertes, qui présente la liste de toutes les règles d’exclusion. ":::

#### <a name="method---post"></a>Méthode - POST

#### <a name="query-parameters"></a>Paramètres de requête

- **ticketId** : Définit l’ID du ticket de maintenance dans les systèmes de l’utilisateur.

- **ttl** : Définit la durée de vie (TTL), qui correspond à la durée de la fenêtre de maintenance, en minutes. Au terme de la période définie par ce paramètre, le système commence automatiquement à envoyer des alertes.

- **engines** : Définit à partir de quel moteur de sécurité supprimer les alertes pendant le processus de maintenance :

    - ANOMALY

    - MALWARE

    - OPERATIONAL

    - POLICY_VIOLATION

    - PROTOCOL_VIOLATION

- **sensorIds** : Définit à partir de quel capteur Defender pour IoT supprimer les alertes pendant le processus de maintenance. Il s’agit du même ID que celui récupéré à partir de /api/v1/appliances (GET).

- **subnets** : Définit à partir de quel sous-réseau supprimer les alertes pendant le processus de maintenance. Le sous-réseau est créé au format suivant : 192.168.0.0/16.

#### <a name="error-codes"></a>Codes d’erreur

- **201 (Créé)** : L’action s’est effectuée correctement.

- **400 (Demande incorrecte)** : Apparaît dans les cas suivants :

   - Le paramètre **ttl** n’est pas numérique ou n’est pas positif.
   - Le paramètre **subnets** a été défini dans un format incorrect.
   - Le paramètre **ticketId** est manquant.
   - Le paramètre **engine** ne correspond pas aux moteurs de sécurité existants.

- **404 (Introuvable)** : L’un des capteurs n’existe pas.

- **409 (Conflit)** : L’ID de ticket est lié à une autre fenêtre de maintenance ouverte.

- **500 (Erreur interne du serveur)** : Autre erreur inattendue.

> [!NOTE]
> Assurez-vous que l’ID de ticket n’est pas lié à une fenêtre ouverte existante. La règle d’exclusion suivante est générée : Maintenance-{nom du jeton}-{ID de ticket}.

#### <a name="method---put"></a>Méthode - PUT

Permet la mise à jour de la durée de la fenêtre de maintenance après le démarrage du processus de maintenance en modifiant le paramètre **ttl**. La nouvelle définition de durée remplace l’ancienne.

Cette méthode est utile lorsque vous souhaitez définir une durée plus longue que la durée actuellement configurée.

#### <a name="query-parameters"></a>Paramètres de requête

- **ticketId** : Définit l’ID du ticket de maintenance dans les systèmes de l’utilisateur.

- **ttl** : Définit la durée de la fenêtre en minutes.

#### <a name="error-code"></a>Code d'erreur

- **200 (OK)** : L’action s’est effectuée correctement.

- **400 (Demande incorrecte)** : Apparaît dans les cas suivants :

   - Le paramètre **ttl** n’est pas numérique ou n’est pas positif.
   - Le paramètre **ticketId** est manquant.
   - Le paramètre **ttl** est manquant.

- **404 (Introuvable)** : L’ID de ticket n’est pas lié à une fenêtre de maintenance ouverte.

- **500 (Erreur interne du serveur)** : Autre erreur inattendue.

> [!NOTE]
> Assurez-vous que l’ID de ticket est lié à une fenêtre ouverte existante.

#### <a name="method---delete"></a>Méthode - DELETE

Ferme une fenêtre de maintenance existante.

#### <a name="query-parameters"></a>Paramètres de requête

- **ticketId** : Enregistre l’ID du ticket de maintenance dans les systèmes de l’utilisateur.

#### <a name="error-code"></a>Code d'erreur

- **200 (OK)** : L’action s’est effectuée correctement.

- **400 (Demande incorrecte)** : Le paramètre **ticketId** est manquant.

- **404 (Introuvable)** : L’ID de ticket n’est pas lié à une fenêtre de maintenance ouverte.

- **500 (Erreur interne du serveur)** : Autre erreur inattendue.

> [!NOTE]
> Assurez-vous que l’ID de ticket est lié à une fenêtre ouverte existante.

#### <a name="method---get"></a>Méthode - GET

Récupérez un journal de toutes les actions d’ouverture, de fermeture et de mise à jour effectuées dans le système au cours de la maintenance. Vous pouvez récupérer un journal uniquement pour les fenêtres de maintenance qui étaient actives dans le passé et qui ont été fermées.

#### <a name="query-parameters"></a>Paramètres de requête

- **fromDate** : Filtre les journaux à partir de la date prédéfinie. Le format est 2019-12-30.

- **toDate** : Filtre les journaux jusqu’à la date prédéfinie. Le format est 2019-12-30.

- **ticketId** : Filtre les journaux liés à un ID de ticket spécifique.

- **tokenName** : Filtre les journaux liés à un jeton spécifique.

#### <a name="error-code"></a>Code d'erreur 

- **200 (OK)** : L’action s’est effectuée correctement.

- **400 (Demande incorrecte)** : Le format de date est incorrect.

- **204 (Pas de contenu)** : Aucune donnée à afficher.

- **500 (Erreur interne du serveur)** : Autre erreur inattendue.

#### <a name="response-type"></a>Type de réponse

- **JSON**

#### <a name="response-content"></a>Contenu de la réponse

Tableau d’objets JSON qui représentent les opérations de fenêtre de maintenance.

#### <a name="response-structure"></a>Structure de réponse

| Nom | Type | Commentaire | Nullable |
|--|--|--|--|
| **dateTime** | String | Exemple : « 2012-04-23T18:25:43.511Z » | non |
| **ticketId** | String | Exemple : « 9a5fe99c-d914-4bda-9332-307384fe40bf » | non |
| **tokenName** | String | - | non |
| **engines** | Tableau de chaînes | - | Oui |
| **sensorIds** | Tableau de chaînes | - | Oui |
| **Sous-réseaux** | Tableau de chaînes | - | Oui |
| **ttl** | Numérique | - | Oui |
| **operationType** | String | Les valeurs sont « OPEN », « UPDATE » et « CLOSE » | non |

#### <a name="curl-command"></a>Commande Curl

| Type | API | Exemple |
|--|--|--|
| POST | `curl -k -X POST -d '{"ticketId": "<TICKET_ID>",ttl": <TIME_TO_LIVE>,"engines": [<ENGINE1, ENGINE2...ENGINEn>],"sensorIds": [<SENSOR_ID1, SENSOR_ID2...SENSOR_IDn>],"subnets": [<SUBNET1, SUBNET2....SUBNETn>]}' -H "Authorization: <AUTH_TOKEN>" https://127.0.0.1/external/v1/maintenanceWindow` | `curl -k -X POST -d '{"ticketId": "a5fe99c-d914-4bda-9332-307384fe40bf","ttl": "20","engines": ["ANOMALY"],"sensorIds": ["5","3"],"subnets": ["10.0.0.3"]}' -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https://127.0.0.1/external/v1/maintenanceWindow` |
| PUT | `curl -k -X PUT -d '{"ticketId": "<TICKET_ID>",ttl": "<TIME_TO_LIVE>"}' -H "Authorization: <AUTH_TOKEN>" https://127.0.0.1/external/v1/maintenanceWindow` | `curl -k -X PUT -d '{"ticketId": "a5fe99c-d914-4bda-9332-307384fe40bf","ttl": "20"}' -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https://127.0.0.1/external/v1/maintenanceWindow` |
| DELETE | `curl -k -X DELETE -d '{"ticketId": "<TICKET_ID>"}' -H "Authorization: <AUTH_TOKEN>" https://127.0.0.1/external/v1/maintenanceWindow` | `curl -k -X DELETE -d '{"ticketId": "a5fe99c-d914-4bda-9332-307384fe40bf"}' -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https://127.0.0.1/external/v1/maintenanceWindow` |
| GET | `curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<IP_ADDRESS>/external/v1/maintenanceWindow?fromDate=&toDate=&ticketId=&tokenName='` | `curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" 'https://127.0.0.1/external/v1/maintenanceWindow?fromDate=2020-01-01&toDate=2020-07-14&ticketId=a5fe99c-d914-4bda-9332-307384fe40bf&tokenName=a'` |

### <a name="authenticate-user-credentials---externalauthenticationvalidation"></a>Authentifier les informations d’identification d’utilisateur – /external/authentication/validation

Utilisez cette API pour valider les informations d’identification de l’utilisateur. Tous les rôles d’utilisateur Defender pour IoT peuvent fonctionner avec l’API. Vous n’avez pas besoin d’un jeton d’accès Defender pour IoT pour utiliser cette API.

#### <a name="method"></a>Méthode

**POST**

#### <a name="request-type"></a>Type de demande

- **JSON**

#### <a name="request-example"></a>Exemple de requête

```rest
request:

{

    "username": "test",

    "password": "Test12345\!"

}
```

#### <a name="response-type"></a>Type de réponse

- **JSON**

#### <a name="response-content"></a>Contenu de la réponse

Chaîne de message avec les détails de l’état de l’opération :

- **Success – msg** : Authentification réussie

- **Failure – error** : Échec de la validation des informations d'identification

#### <a name="device-fields"></a>Champs de l’appareil

| **Nom** | **Type** | **Nullable** |
|--|--|--|
| **username** | String | Non |
| **mot de passe** | String | Non |

#### <a name="response-example"></a>Exemple de réponse

```rest
response:

{

    "msg": "Authentication succeeded."

}
```

#### <a name="curl-command"></a>Commande Curl

| Type | API | Exemple |
|--|--|--|
| POST | `curl -k -d '{"username":"<USER_NAME>","password":"PASSWORD"}' 'https://<IP_ADDRESS>/external/authentication/validation'` | `curl -k -d '{"username":"myUser","password":"1234@abcd"}' 'https://127.0.0.1/external/authentication/validation'` |

### <a name="change-password---externalauthenticationset_password"></a>Modifier le mot de passe – /external/authentication/set_password

Utilisez cette API pour permettre aux utilisateurs de modifier leur mot de passe. Tous les rôles d’utilisateur Defender pour IoT peuvent fonctionner avec l’API. Vous n’avez pas besoin d’un jeton d’accès Defender pour IoT pour utiliser cette API.

#### <a name="method"></a>Méthode

**POST**

#### <a name="request-type"></a>Type de demande

- **JSON**

#### <a name="request-example"></a>Exemple de requête

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}

```

#### <a name="response-type"></a>Type de réponse

- **JSON**

#### <a name="response-content"></a>Contenu de la réponse

Chaîne de message avec les détails de l’état de l’opération :

- **Success – msg** : Le mot de passe a été remplacé

- **Failure – error** : Échec d'authentification de l’utilisateur

- **Failure – error** : Le mot de passe ne respecte pas la stratégie de sécurité

#### <a name="response-example"></a>Exemple de réponse

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}

```

#### <a name="device-fields"></a>Champs de l’appareil

| **Nom** | **Type** | **Nullable** |
|--|--|--|
| **username** | String | Non |
| **mot de passe** | String | Non |
| **new_password** | String | Non |

#### <a name="curl-command"></a>Commande Curl

| Type | API | Exemple |
|--|--|--|
| POST | `curl -k -d '{"username": "<USER_NAME>","password": "<CURRENT_PASSWORD>","new_password": "<NEW_PASSWORD>"}' -H 'Content-Type: application/json'  https://<IP_ADDRESS>/external/authentication/set_password` | `curl -k -d '{"username": "myUser","password": "1234@abcd","new_password": "abcd@1234"}' -H 'Content-Type: application/json'  https://127.0.0.1/external/authentication/set_password` |

### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>Mise à jour du mot de passe de l’utilisateur par l’administrateur système – /external/authentication/set_password_by_admin

Utilisez cette API pour permettre aux administrateurs système de modifier les mots de passe des utilisateurs spécifiés. Tous les rôles d’utilisateur administrateur Defender pour IoT peuvent fonctionner avec l’API. Vous n’avez pas besoin d’un jeton d’accès Defender pour IoT pour utiliser cette API.

#### <a name="method"></a>Méthode

- **POST**

#### <a name="request-type"></a>Type de demande

- **JSON**

#### <a name="request-example"></a>Exemple de requête

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}
```

#### <a name="response-type"></a>Type de réponse

- **JSON**

#### <a name="response-content"></a>Contenu de la réponse

Chaîne de message avec les détails de l’état de l’opération :

- **Success – msg** : Le mot de passe a été remplacé

- **Failure – error** : Échec d'authentification de l’utilisateur

- **Failure – error** : L'utilisateur n'existe pas

- **Failure – error** : Le mot de passe ne respecte pas la stratégie de sécurité

- **Failure – error** : L'utilisateur n'a pas l'autorisation de changer de mot de passe

#### <a name="response-example"></a>Exemple de réponse

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "The user 'test_user' doesn't exist",
        
        "internalSystemErrorMessage": "The user 'yoavfe' doesn't exist"
    
    }

}

```

#### <a name="device-fields"></a>Champs de l’appareil

| **Nom** | **Type** | **Nullable** |
|--|--|--|
| **admin_username** | String | Non |
| **admin_password** | String | Non |
| **username** | String | Non |
| **new_password** | String | Non |

#### <a name="curl-command"></a>Commande Curl

> [!div class="mx-tdBreakAll"]
> | Type | API | Exemple |
> |--|--|--|
> | POST | `curl -k -d '{"admin_username":"<ADMIN_USERNAME>","admin_password":"<ADMIN_PASSWORD>","username": "<USER_NAME>","new_password": "<NEW_PASSWORD>"}' -H 'Content-Type: application/json'  https://<IP_ADDRESS>/external/authentication/set_password_by_admin` | `curl -k -d '{"admin_user":"adminUser","admin_password": "1234@abcd","username": "myUser","new_password": "abcd@1234"}' -H 'Content-Type: application/json'  https://127.0.0.1/external/authentication/set_password_by_admin` |

### <a name="request-alert-pcap---externalv2alertspcap"></a>Demander une alerte PCAP – /external/v2/alerts/pcap

Utilisez cette API pour demander un fichier PCAP lié à une alerte.

#### <a name="method"></a>Méthode

- **GET**

#### <a name="query-parameters"></a>Paramètres de requête

- id : Identifiant de l’alerte CM  
Exemple :  
`/external/v2/alerts/pcap/<id>`

#### <a name="response-type"></a>Type de réponse

- **JSON**

#### <a name="response-content"></a>Contenu de la réponse

- **Success** : Objet JSON qui contient les données relatives au fichier PCAP demandé
- **Failure** : Objet JSON qui contient un message d’erreur

#### <a name="data-fields"></a>Champs de données

|Nom|Type|Nullable|
|-|-|-|
|id|Numérique|Non|
|xsenseId|Numérique|Non|
|xsenseAlertId|Numérique|Non|
|downloadUrl|String|Non|
|token|String|Non|

#### <a name="response-example"></a>Exemple de réponse

#### <a name="success"></a>Succès

```json
{
  "downloadUrl": "https://10.1.0.2/api/v2/alerts/pcap/1",
  "xsenseId": 1,
  "token": "d2791f58-2a88-34fd-ae5c-2651fe30a63c",
  "id": 1,
  "xsenseAlertId": 1
}
```

#### <a name="error"></a>Error

```json
{
  "error": "alert not found"
}
```

### <a name="curl-command"></a>Commande Curl

|Type|API|Exemple|
|-|-|-|
|GET|`curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<IP_ADDRESS>/external/v2/alerts/pcap/<ID>'`|`curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" 'https://10.1.0.1/external/v2/alerts/pcap/1'`

## <a name="next-steps"></a>Étapes suivantes

- [Examiner toutes les détections de capteur d’un inventaire d’appareils](how-to-investigate-sensor-detections-in-a-device-inventory.md)

- [Examiner toutes les détections de capteur de l’entreprise d’un inventaire d’appareils](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)
