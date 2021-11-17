---
title: Collecte d’événements du micro-agent (préversion)
description: Les agents de sécurité IoT collectent des données et événements système de votre appareil local, puis envoient ces données au cloud Azure à des fins de traitement et d’analyse.
ms.date: 11/09/2021
ms.topic: conceptual
ms.openlocfilehash: 36cce317c4e53f7578dace7faba5e2fb567f42cc
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132289974"
---
# <a name="micro-agent-event-collection-preview"></a>Collecte d’événements du micro-agent (préversion)

Les agents de sécurité IoT collectent des données et événements système de votre appareil local, puis envoient ces données au cloud Azure à des fins de traitement et d’analyse. Le micro-agent Defender pour IoT collecte de nombreux types d’événements d’appareil, dont des événements liés à de nouveaux processus et connexions. De tels événements peuvent se produire fréquemment sur un appareil. Si cette capacité est importante pour assurer une sécurité complète, le nombre de messages que les agents de sécurité envoient peut rapidement atteindre ou dépasser les limites de quotas et de coûts de votre IoT Hub. Ces messages et ces événements contiennent des informations de sécurité très précieuses qui sont essentielles à la protection de votre appareil.

Pour réduire le nombre de messages et les coûts tout en maintenant la sécurité de votre appareil, les agents Defender pour IoT regroupent les types d’événements suivants :

- ProcessCreate (Linux uniquement)

- Activité de connexion (Linux uniquement)

- ConnectionCreate réseau

- Informations système

- Ligne de base

Les collecteurs basés sur les événements sont des collecteurs déclenchés en fonction de l’activité correspondante à partir de l’appareil. Par exemple : ``a process was started in the device``.  

Les collecteurs basés sur les déclencheurs sont des collecteurs qui sont déclenchés de manière planifiée en fonction des configurations du client.

## <a name="how-does-event-aggregation-work"></a>Fonctionnement de l’agrégation d’événements

Les agents Defender pour IoT agrègent les événements pour la fenêtre de temps ou la période de l’intervalle. Une fois la période d’intervalle écoulée, l’agent envoie les événements agrégés au cloud Azure pour une analyse plus poussée. Les événements agrégés sont stockés en mémoire jusqu’à ce qu’ils soient envoyés au cloud Azure.

L’agent collecte des événements identiques à ceux qui sont déjà stockés en mémoire. Cette collection oblige l’agent à augmenter le nombre d’accès de cet événement spécifique pour réduire l’empreinte mémoire de l’agent. Quand la fenêtre de temps d’agrégation se termine, l’agent envoie le nombre d’accès à chaque type d’événement survenu. L’agrégation d’événements est simplement l’agrégation des nombres d’accès de chaque type collecté d’événement.

## <a name="process-events-event-based"></a>Événements de processus (basés sur un événement)

Les événements de processus sont pris en charge sur les système d'exploitation Linux.

Ceux-ci sont considérés comme identiques quand la *ligne de commande* et le  *userid* sont identiques.

La mémoire tampon par défaut des événements de processus s’élève à 256 processus. Lorsque cette limite est atteinte, la mémoire tampon est recyclée et l’événement de processus le plus ancien est ignoré afin de libérer de l’espace pour le dernier événement traité. Un avertissement pour augmenter la taille du cache est journalisé.

Données collectées pour chaque événement :

| Paramètre | Description|
|--|--|
| **Timestamp** | Première fois que le processus a été observé. |
| **process_id** | PID Linux. |
| **parent_process_id** | PID parent de Linux, s’il existe. |
| **Commandline** | Ligne de commande. |
| **Type** | Peut être `fork` ou `exec`. |
| **hit_count** | Nombre agrégé. Nombre d’exécutions du même processus, pendant le même laps de temps, jusqu’à ce que les événements soient envoyés au cloud. |

## <a name="network-connection-events-event-based-collector"></a>Événements de connexion réseau (collecteur basé sur les événements)

Les événements de connexion réseau sont considérés comme identiques lorsque le port local, le port distant, le protocole de transport, l’adresse locale et l’adresse distante sont identiques.

La mémoire tampon par défaut pour un événement de connexion réseau est de 256. Dans les situations où le cache est plein :

- **Appareils Azure RTOS** : aucun nouvel événement réseau n’est mis en cache jusqu’au début du cycle de collecte suivant.  

- **Appareils Linux** : l’événement le plus ancien sera remplacé par chaque événement nouveau. Un avertissement pour augmenter la taille du cache est journalisé.

Pour les appareils Linux, seul IPv4 est pris en charge.

Données collectées pour chaque événement :

| Paramètre | Description|
|--|--|
| **Adresse locale** | Adresse source de la connexion. |
| **Adresse distante** | Adresse de destination de la connexion. |
| **Port local** | Port source de la connexion. |
| **Port distant** | Port de destination de la connexion. |
| **Bytes_in** | Somme des octets RX agrégés de la connexion. |
| **Bytes_out** | Somme des octets TX agrégés de la connexion. |
| **Transport_protocol** | Peut être TCP, UDP ou ICMP. |
| **Protocole d’application** | Protocole d’application associé à la connexion. |
| **Propriétés étendues** | Détails supplémentaires de la connexion. Par exemple : `host name`. |

## <a name="login-collector-event-based-collector"></a>Collecteur de connexion (collecteur basé sur les événements)

Le collecteur de connexion collecte les connexions utilisateur, les déconnexions et les échecs de tentatives de connexion.

Actuellement, les protocoles SSH et Telnet sont entièrement pris en charge.  

Les données suivantes sont collectées :

| Paramètre | Description|
|--|--|
| **opération** | Connexion, déconnexion ou échec de connexion (LoginFailed). |
| **process_id** | PID Linux. |
| **user_name** | Utilisateur Linux. |
| **Exécutable** | Appareil terminal. Par exemple, tty1..6 ou pts/n. |
| **remote_address** | Source de la connexion, soit une adresse IP distante au format IPv6 ou IPv4, soit 127.0.0.1/0.0.0.0 pour indiquer une connexion locale. |

> [!Note]
> Un événement de connexion est capturé lorsqu’un terminal est ouvert sur un appareil, avant que l’utilisateur ne se connecte. Cet événement est associé à un processus TTY, à un type d’événement de connexion et à un nom d’utilisateur. Par exemple : `LOGIN`.

## <a name="system-information-trigger-based-collector"></a>Informations système (collecteur basé sur un déclencheur)

Données collectées pour chaque événement :

| Paramètre | Description|
|--|--|
| **hardware_vendor** | Nom du fournisseur de l’appareil. |
| **hardware_model** | Numéro de modèle du périphérique. |
| **os_dist** | Distribution du système d’exploitation. Par exemple : `Linux`. |
| **os_version** | Version du système d’exploitation. Par exemple, `Windows 10` ou `Ubuntu 20.04.1`. |
| **os_platform** | Système d’exploitation de l’appareil. |
| **os_arch** | Architecture du système d’exploitation. Par exemple : `x86_64`. |
| **Cartes réseau** | Contrôleur d’interface réseau. La liste complète des propriétés est indiquée ci-dessous. |

Les propriétés des **cartes réseau** sont composées des éléments suivants :

| Paramètre | Description|
|--|--|
|**type** | l’une des valeurs suivantes : `UNKNOWN`, `ETH`, `WIFI`, `MOBILE` ou `SATELLITE`. |
| **vlans** | Réseau local virtuel associé à l’interface réseau. |
| **vendor** | Fournisseur du contrôleur de réseau. |
| **info** | Adresses IP et Mac associées au contrôleur de réseau. Notamment les champs suivants : <br> - **ipv4_address** : adresse IPv4. <br> - **ipv6_address** : adresse IPv6. <br> - **mac** : adresse MAC.|

## <a name="baseline-trigger-based"></a>Ligne de base (basée sur un déclencheur)

Le collecteur de ligne de base effectue régulièrement des vérifications de CIS. Seuls les résultats ayant échoué sont envoyés vers le cloud. Le cloud agrège les résultats et fournit des recommandations.

### <a name="data-collection"></a>Collecte de données

Données collectées pour chaque événement :

| Paramètre | Description|
|--|--|
| **ID de contrôle** | Au format CIS. Par exemple : `CIS-debian-9-Filesystem-1.1.2`. |
| **Résultat de la vérification** | Peut être `Error` ou `Fail`. Par exemple, `Error` dans une situation où le contrôle ne peut pas être effectué. |
| **Error** | Information et description de l’erreur. |
| **Description** | Description du contrôle depuis CIS. |
| **Correction** | Recommandation pour la correction depuis CIS. |
| **Gravité** | Niveau de gravité. |

## <a name="next-steps"></a>Étapes suivantes

Examinez vos [Alertes de sécurité IoT](concept-security-alerts.md).
