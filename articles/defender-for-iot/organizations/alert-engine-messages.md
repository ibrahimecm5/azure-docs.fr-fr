---
title: Types et descriptions d’alertes
description: Consultez les descriptions d’alertes de Defender pour IoT.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: ee5e3b32d0a716b9ce05ec2b7bb1ed7c39ddada6
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132283920"
---
# <a name="alert-types-and-descriptions"></a>Types et descriptions d’alertes

Cet article donne des informations sur les alertes qui peuvent être générées à partir de moteurs Defender pour IoT, notamment leur type, leur description et leur gravité. Ces informations peuvent être utilisées pour relier des alertes à des guides opérationnels, définir des règles de transfert, des règles d’exclusion et des alertes personnalisées, et définir les règles appropriées au sein d’un système SIEM. Les alertes s’affichent dans la fenêtre Alertes, qui vous permet de gérer les événements d’alerte. 

## <a name="policy-engine-alerts"></a>Alertes du moteur de stratégie

Les alertes du moteur de stratégie décrivent les écarts détectés par rapport au comportement de base appris.

>[!NOTE]
> Cet article contient des références au terme *esclave*, un terme que Microsoft n’utilise plus. Lorsque le terme sera supprimé du logiciel, nous le supprimerons de cet article.

| Intitulé  | Description | Gravité |
|--|--|--|
| Utilisation anormale d’adresses MAC | Un nouvel appareil source détecté sur le réseau n’a pas été autorisé. | Secondaire |
| Logiciel Beckhoff modifié | Le microprogramme a été mis à jour sur un appareil source. Il peut s’agir d’une activité autorisée, par exemple une procédure de maintenance planifiée. | Majeure |
| Échec de connexion à la base de données | Un échec de tentative de connexion d’un appareil source à un serveur de destination a été détecté. Cela peut être dû à une erreur humaine, mais peut également indiquer une tentative malveillante de compromission du serveur ou des données qu’il contient. | Majeure |
| Version du microprogramme ROC Emerson modifiée | Le microprogramme a été mis à jour sur un appareil source. Il peut s’agir d’une activité autorisée, par exemple une procédure de maintenance planifiée. | Majeure |
| Une adresse externe sur le réseau a communiqué avec Internet | Un appareil source défini comme faisant partie de votre réseau communique avec des adresses Internet. La source n’est pas autorisée à communiquer avec des adresses Internet. | Critique |
| Périphérique de champ découvert de façon inattendue | Un nouvel appareil source détecté sur le réseau n’a pas été autorisé. | Majeure |
| Changement de microprogramme détecté | Le microprogramme a été mis à jour sur un appareil source. Il peut s’agir d’une activité autorisée, par exemple une procédure de maintenance planifiée. | Majeure |
| Version du microprogramme modifiée | Le microprogramme a été mis à jour sur un appareil source. Il peut s’agir d’une activité autorisée, par exemple une procédure de maintenance planifiée. | Majeure |
| Opération Foxboro I/A non autorisée | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Échec d’ouverture de session FTP | Un échec de tentative de connexion d’un appareil source à un serveur de destination a été détecté. Cela peut être dû à une erreur humaine, mais peut également indiquer une tentative malveillante de compromission du serveur ou des données qu’il contient. | Majeure |
| Exception non autorisée levée dans le code de fonction | Un appareil source (esclave) a retourné une exception à un appareil de destination (maître). | Majeure |
| Paramètres de type de message GOOSE | Des paramètres du message (identifiés par l’ID de protocole) ont été modifiés sur un appareil source. | Avertissement |
| Version du microprogramme Honeywell modifiée | Le microprogramme a été mis à jour sur un appareil source. Il peut s’agir d’une activité autorisée, par exemple une procédure de maintenance planifiée. | Majeure |
| Communication HTTP interdite | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Accès à Internet détecté | Un appareil source défini comme faisant partie de votre réseau communique avec des adresses Internet. La source n’est pas autorisée à communiquer avec des adresses Internet. | Majeure |
| Version du microprogramme Mitsubishi modifiée | Le microprogramme a été mis à jour sur un appareil source. Il peut s’agir d’une activité autorisée, par exemple une procédure de maintenance planifiée. | Majeure |
| Violation de la plage d’adresses Modbus | Un appareil maître a demandé l’accès à une nouvelle adresse mémoire esclave. | Majeure |
| Version du microprogramme Modbus modifiée | Le microprogramme a été mis à jour sur un appareil source. Il peut s’agir d’une activité autorisée, par exemple une procédure de maintenance planifiée. | Majeure |
| Nouvelle activité détectée – Classe CIP | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Nouvelle activité détectée – Service de classe CIP | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Nouvelle activité détectée – Commande CIP PCCC | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Nouvelle activité détectée – Symbole CIP | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Nouvelle activité détectée – Connexion E/S EtherNet/IP | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Nouvelle activité détectée – Commande de protocole EtherNet/IP | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Nouvelle activité détectée – Code de message GSM | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Nouvelle activité détectée – Codes de commande LonTalk | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Découverte de nouveau port | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Avertissement |
| Nouvelle activité détectée – Variable réseau LonTalk | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Nouvelle activité détectée – Demande de données Ovation | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Nouvelle activité détectée – Commande de lecture/écriture (groupe d’index AMS) | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Nouvelle activité détectée – Commande de lecture/écriture (décalage d’index AMS) | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Nouvelle activité détectée – Type de message DeltaV non autorisé | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Nouvelle activité détectée – Opération ROC DeltaV non autorisée | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Nouvelle activité détectée – Type de message RPC non autorisé | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Nouvelle activité détectée – Appel de procédure RPC non autorisé | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Nouvelle activité détectée – Utilisation de la commande de protocole AMS | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Nouvelle activité détectée – Utilisation de la commande SICAM Siemens | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Nouvelle activité détectée – Utilisation de la commande de protocole Suitelink | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Nouvelle activité détectée – Utilisation de sessions de protocole Suitelink | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Nouvelle activité détectée – Utilisation de la commande VNetIP Yokogawa | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Nouvelle ressource détectée | Un nouvel appareil source détecté sur le réseau n’a pas été autorisé. | Majeure |
| Nouvelle configuration d’appareil LLDP | Un nouvel appareil source détecté sur le réseau n’a pas été autorisé. | Majeure |
| Commande Omron FINS non autorisée | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Microprogramme S7 Plus PLC modifié | Le microprogramme a été mis à jour sur un appareil source. Il peut s’agir d’une activité autorisée, par exemple une procédure de maintenance planifiée. | Majeure |
| Paramètres de type de message des valeurs échantillonnées | Des paramètres du message (identifiés par l’ID de protocole) ont été modifiés sur un appareil source. | Avertissement |
| Suspicion d’analyse d’intégrité interdite | Une analyse a été détectée sur un appareil source DNP3 (station à distance). Cette analyse n’a pas été autorisée en tant que trafic appris sur votre réseau. | Majeure |
| Commande non autorisée de lien d’ordinateur Toshiba | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Secondaire |
| Opération de fichier ABB Totalflow non autorisée | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Opération de registre ABB Totalflow non autorisée | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Accès non autorisé à un bloc de données Siemens S7 Plus | Un appareil source a tenté d’accéder à une ressource sur un autre appareil. Une tentative d’accès à cette ressource entre ces deux appareils n’a pas été autorisée en tant que trafic appris sur votre réseau. | Avertissement |
| Accès non autorisé à un objet Siemens S7 Plus | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Accès non autorisé à une étiquette Wonderware | Un appareil source a tenté d’accéder à une ressource sur un autre appareil. Une tentative d’accès à cette ressource entre ces deux appareils n’a pas été autorisée en tant que trafic appris sur votre réseau. | Majeure |
| Accès à un objet BACNet non autorisé | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Itinéraire BACNet non autorisé | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Connexion à la base de données non autorisée | Une tentative de connexion entre un client source et un serveur de destination a été détectée. La communication entre ces appareils n’a pas été autorisée en tant que trafic appris sur votre réseau. | Majeure |
| Opération de base de données non autorisée | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Opération ROC Emerson non autorisée | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Accès au fichier GE SRTP non autorisé | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Commande de protocole GE SRTP non autorisée | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Opération de mémoire système GE SRTP non autorisée | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Activité HTTP non autorisée | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Serveur HTTP non autorisé | Une application non autorisée a été détectée sur un appareil source. L’application n’a pas été autorisée en tant qu’application apprise sur votre réseau. | Majeure |
| Action HTTP SOAP non autorisée | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Agent utilisateur HTTP non autorisé | Une application non autorisée a été détectée sur un appareil source. L’application n’a pas été autorisée en tant qu’application apprise sur votre réseau. | Majeure |
| Connectivité Internet non autorisée détectée | Un appareil source défini comme faisant partie de votre réseau communique avec des adresses Internet. La source n’est pas autorisée à communiquer avec des adresses Internet. | Critique |
| Commande MELSEC Mitsubishi non autorisée | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Accès au programme MMS non autorisé | Un appareil source a tenté d’accéder à une ressource sur un autre appareil. Une tentative d’accès à cette ressource entre ces deux appareils n’a pas été autorisée en tant que trafic appris sur votre réseau. | Majeure |
| Service MMS non autorisé | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Connexion de multidiffusion/diffusion non autorisée | Une connexion de multidiffusion/diffusion a été détectée entre un appareil source et d’autres appareils. La communication par multidiffusion/diffusion n’est pas autorisée. | Critique |
| Requête de nom non autorisée | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Activité OPC UA non autorisée | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Demande/réponse OPC UA non autorisée | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Une opération non autorisée a été détectée par une règle définie par l’utilisateur | Un trafic a été détecté entre deux appareils. Cette activité n’est pas autorisée en vertu d’une règle d’alerte personnalisée définie par un utilisateur. | Majeure |
| Lecture de la configuration PLC non autorisée | L’appareil source n’est pas défini en tant qu’appareil de programmation, mais a effectué une opération de lecture/écriture sur un contrôleur de destination. Les modifications de programmation doivent être effectuées uniquement par des appareils de programmation. Une application de programmation a peut-être été installée sur cet appareil. | Avertissement |
| Écriture dans la configuration PLC non autorisée | L’appareil source a envoyé une commande pour lire/écrire le programme d’un contrôleur de destination. Cette activité n’a pas été observée précédemment. | Majeure |
| Chargement de programme PLC non autorisé | L’appareil source a envoyé une commande pour lire/écrire le programme d’un contrôleur de destination. Cette activité n’a pas été observée précédemment. | Majeure |
| Programmation de PLC non autorisée | L’appareil source n’est pas défini en tant qu’appareil de programmation, mais a effectué une opération de lecture/écriture sur un contrôleur de destination. Les modifications de programmation doivent être effectuées uniquement par des appareils de programmation. Une application de programmation a peut-être été installée sur cet appareil. | Critique |
| Type de trame Profinet non autorisé | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Commande SAIA S-Bus non autorisée | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Exécution de la fonction de contrôle Siemens S7 non autorisée | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Exécution de la fonction définie par l’utilisateur Siemens S7 non autorisée | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Accès au bloc Siemens S7 Plus non autorisé | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Opération Siemens S7 Plus non autorisée | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Connexion SMB non autorisée | Une tentative de connexion entre un client source et un serveur de destination a été détectée. La communication entre ces appareils n’a pas été autorisée en tant que trafic appris sur votre réseau. | Majeure |
| Opération SNMP non autorisée | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Accès SSH non autorisé | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Processus Windows non autorisé | Une application non autorisée a été détectée sur un appareil source. L’application n’a pas été autorisée en tant qu’application apprise sur votre réseau. | Majeure |
| Service Windows non autorisé | Une application non autorisée a été détectée sur un appareil source. L’application n’a pas été autorisée en tant qu’application apprise sur votre réseau. | Majeure |
| Une opération non autorisée a été détectée par une règle définie par l’utilisateur | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres enfreint une règle définie par l’utilisateur | Majeure |
| Extension Modbus Schneider Electric non autorisée | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Utilisation non autorisée de types d’ASDU | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Utilisation non autorisée de code de fonction DNP3 | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |
| Utilisation non autorisée d’une indication interne (IIN) | Un appareil source DNP3 (station à distance) a signalé une indication interne (IIN) qui n’est pas autorisée en tant que trafic appris sur votre réseau. | Majeure |
| Utilisation non autorisée de code de fonction Modbus | De nouveaux paramètres de trafic ont été détectés. Cette combinaison de paramètres n’a pas été autorisée comme trafic appris sur votre réseau. La combinaison suivante n’est pas autorisée. | Majeure |

## <a name="anomaly-engine-alerts"></a>Alertes du moteur d’anomalies

Les alertes du moteur d’anomalies décrivent les anomalies détectées dans l’activité réseau.

| Titre | Description | Gravité |
|--|--|--|
| Modèle d’exception anormale dans l’esclave | Un nombre excessif d’erreurs a été détecté sur un appareil source. Cela peut être dû à un problème opérationnel. | Secondaire |
| Longueur anormale d’en-tête HTTP | L’appareil source a envoyé un message anormal. Cela peut indiquer une tentative d’attaque de l’appareil de destination. | Critique |
| Nombre anormal de paramètres dans l’en-tête HTTP | L’appareil source a envoyé un message anormal. Cela peut indiquer une tentative d’attaque de l’appareil de destination. | Critique |
| Comportement périodique anormal dans le canal de communication | Une modification de la fréquence de communication entre les appareils source et de destination a été détectée. | Secondaire |
| Arrêt anormal d’applications | Un nombre excessif de commandes d’arrêt a été détecté sur un appareil source. Cela peut être dû à un problème opérationnel ou à une tentative de manipulation de l’appareil. | Majeure |
| Bande passante de trafic anormale | Une bande passante anormale a été détectée sur un canal. La bande passante semble être considérablement inférieure/supérieure à celle détectée précédemment. Pour plus d’informations, utilisez le widget Bande passante totale. | Avertissement |
| Bande passante de trafic anormale entre des appareils | Une bande passante anormale a été détectée sur un canal. La bande passante semble être considérablement inférieure/supérieure à celle détectée précédemment. Pour plus d’informations, utilisez le widget Bande passante totale. | Avertissement |
| Analyse d’adresses détectée | Un appareil source a été détecté des périphériques réseau effectuant une analyse. Cet appareil n’a pas été autorisé en tant que périphérique d’analyse de réseau. | Critique |
| Analyse d’adresse ARP détectée | Un appareil source a été détecté en train d’analyser les périphériques réseau à l’aide du protocole ARP. Cette adresse d’appareil n’a pas été autorisée en tant qu’adresse d’analyse ARP valide. | Critique |
| Analyse d’adresse ARP détectée | Un appareil source a été détecté en train d’analyser les périphériques réseau à l’aide du protocole ARP. Cette adresse d’appareil n’a pas été autorisée en tant qu’adresse d’analyse ARP valide. | Critique |
| Usurpation d’ARP | Une quantité anormale de paquets a été détectée sur le réseau. Cela peut indiquer une attaque, par exemple une attaque par usurpation d’ARP ou par saturation ICMP. | Avertissement |
| Nombre excessif de tentatives de connexion | Un appareil source a été observé en train d’effectuer un nombre excessif de tentatives de connexion à un serveur de destination. Il peut s’agir d’une attaque par force brute. Le serveur peut être compromis par un acteur malveillant. | Critique |
| Nombre excessif de sessions | Un appareil source a été observé en train d’effectuer un nombre excessif de tentatives de connexion à un serveur de destination. Il peut s’agir d’une attaque par force brute. Le serveur peut être compromis par un acteur malveillant. | Critique |
| Taux de redémarrage excessif d’une station à distance | Un nombre excessif de commandes de redémarrage ont été détectées sur un appareil source. Cela peut être dû à un problème opérationnel ou à une tentative de manipulation de l’appareil. | Majeure |
| Nombre excessif de tentatives de connexion SMB | Un appareil source a été observé en train d’effectuer un nombre excessif de tentatives de connexion à un serveur de destination. Il peut s’agir d’une attaque par force brute. Le serveur peut être compromis par un acteur malveillant. | Critique |
| Saturation ICMP | Une quantité anormale de paquets a été détectée sur le réseau. Cela peut indiquer une attaque, par exemple une attaque par usurpation d’ARP ou par saturation ICMP. | Avertissement |
| Contenu d’en-tête HTTP interdit | L’appareil source a initié une demande non valide. | Critique |
| Canal de communication inactif | Un canal de communication entre deux appareils a été inactif durant une période pendant laquelle une activité est généralement observée. Cela peut indiquer que le programme qui génère ce trafic a été modifié ou n’est pas disponible. Il est recommandé d’examiner la configuration du programme installé et de vérifier s’il est correctement configuré. | Avertissement |
| Analyse d’adresse de longue durée détectée | Un appareil source a été détecté des périphériques réseau effectuant une analyse. Cet appareil n’a pas été autorisé en tant que périphérique d’analyse de réseau. | Critique |
| Tentative de deviner un mot de passe détectée | Un appareil source a été observé en train d’effectuer un nombre excessif de tentatives de connexion à un serveur de destination. Il peut s’agir d’une attaque par force brute. Le serveur peut être compromis par un acteur malveillant. | Critique |
| Analyse PLC détectée | Un appareil source a été détecté des périphériques réseau effectuant une analyse. Cet appareil n’a pas été autorisé en tant que périphérique d’analyse de réseau. | Critique |
| Analyse de port détectée | Un appareil source a été détecté des périphériques réseau effectuant une analyse. Cet appareil n’a pas été autorisé en tant que périphérique d’analyse de réseau. | Critique |
| Longueur de message inattendue | L’appareil source a envoyé un message anormal. Cela peut indiquer une tentative d’attaque de l’appareil de destination. | Critique |
| Trafic inattendu pour un port standard | Du trafic a été détecté sur un appareil à l’aide d’un port réservé pour un autre protocole. | Majeure |

## <a name="protocol-violation-engine-alerts"></a>Alertes du moteur de violation de protocole

Les alertes du moteur de protocole décrivent les écarts détectés dans la structure de paquets ou les valeurs de champs par rapport aux spécifications de protocole.

| Titre | Description | Gravité |
|--|--|--|
| Nombre excessif de paquets incorrects dans une seule session | Nombre anormal de paquets mal formés envoyés de l’appareil source à l’appareil de destination. Cela peut indiquer des communications erronées ou une tentative de manipulation de l’appareil ciblé. | Majeure |
| Mise à jour du microprogramme | Un appareil source a envoyé une commande de mise à jour du microprogramme sur un appareil de destination. Vérifiez que les mises à jour récentes de la programmation, de la configuration et du microprogramme effectuées sur l’appareil de destination sont valides. | Avertissement |
| Code de fonction non pris en charge par une station à distance | L’appareil de destination a reçu une demande non valide. | Majeure |
| Message BACNet interdit | L’appareil source a initié une demande non valide. | Majeure |
| Tentative de connexion interdite sur le port 0 | Un appareil source a tenté de se connecter à l’appareil de destination sur le port numéro zéro (0). Pour le protocole TCP, le port 0 est réservé et ne peut pas être utilisé. Pour le protocole UDP, le port est facultatif et la valeur 0 signifie aucun port. Il n’y a généralement aucun service sur un système qui écoute sur le port 0. Cet événement peut indiquer une tentative d’attaque de l’appareil de destination, ou qu’une application a été programmée de manière incorrecte. | Secondaire |
| Opération DNP3 interdite | L’appareil source a initié une demande non valide. | Majeure |
| Opération MODBUS interdite (exception déclenchée par le maître) | L’appareil source a initié une demande non valide. | Majeure |
| Opération MODBUS interdite (code de fonction zéro) | L’appareil source a initié une demande non valide. | Majeure |
| Version de protocole interdite | L’appareil source a initié une demande non valide. | Majeure |
| Paramètre incorrect envoyé à une station externe | L’appareil de destination a reçu une demande non valide. | Majeure |
| Lancement d’un code de fonction obsolète (Initialiser des données) | L’appareil source a initié une demande non valide. | Secondaire |
| Lancement d’un code de fonction obsolète (Enregistrer la configuration) | L’appareil source a initié une demande non valide. | Secondaire |
| Le maître a demandé une confirmation de la couche Application | L’appareil source a initié une demande non valide. | Avertissement |
| Exception Modbus | Un appareil source (esclave) a retourné une exception à un appareil de destination (maître). | Majeure |
| Un appareil esclave a reçu un type d’ASDU interdit | L’appareil de destination a reçu une demande non valide. | Majeure |
| Un appareil esclave a reçu une commande interdite à l’origine de la transmission | L’appareil de destination a reçu une demande non valide. | Majeure |
| Un appareil esclave a reçu une adresse courante interdite | L’appareil de destination a reçu une demande non valide. | Majeure |
| Un appareil esclave a reçu un paramètre d’adresse de données interdit | L’appareil de destination a reçu une demande non valide. | Majeure |
| L’appareil esclave a reçu un paramètre de valeur de données interdit | L’appareil de destination a reçu une demande non valide. | Majeure |
| Un appareil esclave a reçu un code de fonction interdit | L’appareil de destination a reçu une demande non valide. | Majeure |
| Un appareil esclave a reçu une adresse d’objet d’information interdite | L’appareil de destination a reçu une demande non valide. | Majeure |
| Objet inconnu envoyé à une station à distance | L’appareil de destination a reçu une demande non valide. | Majeure |
| Utilisation d’un code de fonction réservé | L’appareil source a initié une demande non valide. | Majeure |
| Utilisation d’une mise en forme incorrecte par une station à distance | L’appareil source a initié une demande non valide. | Avertissement |
| Utilisation des indicateurs d’état réservés (IIN) | Un appareil source DNP3 (station à distance) a utilisé l’indicateur interne réservé 2.6. Il est recommandé de vérifier la configuration de l’appareil. | Avertissement |

## <a name="malware-engine-alerts"></a>Alertes du moteur de programme malveillant

Les alertes du moteur de programme malveillant décrivent l’activité réseau malveillante détectée.

| Titre | Description| Gravité |
|--|--|--|
| Tentative de connexion à une adresse IP malveillante connue | Une activité réseau suspecte a été détectée. Cette activité peut être associée à une attaque exploitant une méthode utilisée par un programme malveillant connu. | Majeure |
| Message SMB non valide (implant de porte dérobée DoublePulsar) | Une activité réseau suspecte a été détectée. Cette activité peut être associée à une attaque exploitant une méthode utilisée par un programme malveillant connu. | Critique |
| Demande malveillante de nom de domaine | Une activité réseau suspecte a été détectée. Cette activité peut être associée à une attaque exploitant une méthode utilisée par un programme malveillant connu. | Majeure |
| Fichier de test de programme malveillant détecté – Succès de l’antivirus EICAR | Un fichier de test de l’antivirus EICAR a été détecté dans le trafic entre deux appareils. Le fichier n’est pas un programme malveillant. Il permet de confirmer que le logiciel antivirus est correctement installé. Montrez ce qui se passe en cas de détection d’un virus et consultez les procédures internes et les réactions lors de la détection d’un virus. Le logiciel antivirus doit détecter EICAR comme s’il s’agissait d’un vrai virus. | Majeure |
| Suspicion de programme malveillant Conficker | Une activité réseau suspecte a été détectée. Cette activité peut être associée à une attaque exploitant une méthode utilisée par un programme malveillant connu. | Majeure |
| Suspicion d’attaque par type déni de service | Un appareil source a tenté d’initier un nombre excessif de nouvelles connexions à un appareil de destination. Il s’agit peut-être d’une attaque par déni de service (DOS) contre l’appareil de destination qui pourrait entraîner l’interruption des fonctionnalités de l’appareil, impacter les performances et la disponibilité du service, ou causer des erreurs irrécupérables. | Critique |
| Suspicion d’activité malveillante | Une activité réseau suspecte a été détectée. Cette activité peut être associée à une attaque exploitant une méthode utilisée par un programme malveillant connu. | Majeure |
| Suspicion d’activité malveillante (BlackEnergy) | Une activité réseau suspecte a été détectée. Cette activité peut être associée à une attaque exploitant une méthode utilisée par un programme malveillant connu. | Critique |
| Suspicion d’activité malveillante (DarkComet) | Une activité réseau suspecte a été détectée. Cette activité peut être associée à une attaque exploitant une méthode utilisée par un programme malveillant connu. | Critique |
| Suspicion d’activité malveillante (Duqu) | Une activité réseau suspecte a été détectée. Cette activité peut être associée à une attaque exploitant une méthode utilisée par un programme malveillant connu. | Critique |
| Suspicion d’activité malveillante (Flame) | Une activité réseau suspecte a été détectée. Cette activité peut être associée à une attaque exploitant une méthode utilisée par un programme malveillant connu. | Critique |
| Suspicion d’activité malveillante (Havex) | Une activité réseau suspecte a été détectée. Cette activité peut être associée à une attaque exploitant une méthode utilisée par un programme malveillant connu. | Critique |
| Suspicion d’activité malveillante (Karagany) | Une activité réseau suspecte a été détectée. Cette activité peut être associée à une attaque exploitant une méthode utilisée par un programme malveillant connu. | Critique |
| Suspicion d’activité malveillante (LightsOut) | Une activité réseau suspecte a été détectée. Cette activité peut être associée à une attaque exploitant une méthode utilisée par un programme malveillant connu. | Critique |
| Suspicion d’activité malveillante (Name Queries) | Une activité réseau suspecte a été détectée. Cette activité peut être associée à une attaque exploitant une méthode utilisée par un programme malveillant connu. | Majeure |
| Suspicion d’activité malveillante (Poison Ivy) | Une activité réseau suspecte a été détectée. Cette activité peut être associée à une attaque exploitant une méthode utilisée par un programme malveillant connu. | Critique |
| Suspicion d’activité malveillante (Regin) | Une activité réseau suspecte a été détectée. Cette activité peut être associée à une attaque exploitant une méthode utilisée par un programme malveillant connu. | Critique |
| Suspicion d’activité malveillante (Stuxnet) | Une activité réseau suspecte a été détectée. Cette activité peut être associée à une attaque exploitant une méthode utilisée par un programme malveillant connu. | Critique |
| Suspicion d’activité malveillante (WannaCry) | Une activité réseau suspecte a été détectée. Cette activité peut être associée à une attaque exploitant une méthode utilisée par un programme malveillant connu. | Majeure |
| Suspicion de programme malveillant NotPetya – Paramètres SMB interdits détectés | Une activité réseau suspecte a été détectée. Cette activité peut être associée à une attaque exploitant une méthode utilisée par un programme malveillant connu. | Critique |
| Suspicion de programme malveillant NotPetya – Transaction SMB interdite détectée | Une activité réseau suspecte a été détectée. Cette activité peut être associée à une attaque exploitant une méthode utilisée par un programme malveillant connu. | Critique |
| Suspicion d’exécution de code à distance avec PsExec | Une activité réseau suspecte a été détectée. Cette activité peut être associée à une attaque exploitant une méthode utilisée par un programme malveillant connu. | Majeure |
| Suspicion de gestion à distance du service Windows | Une activité réseau suspecte a été détectée. Cette activité peut être associée à une attaque exploitant une méthode utilisée par un programme malveillant connu. | Majeure |
| Fichier exécutable suspect détecté sur un point de terminaison | Une activité réseau suspecte a été détectée. Cette activité peut être associée à une attaque exploitant une méthode utilisée par un programme malveillant connu. | Majeure |
| Trafic suspect détecté | Une activité réseau suspecte a été détectée. Cette activité peut être associée à une attaque exploitant une méthode utilisée par un programme malveillant connu. | Critique |

## <a name="operational-engine-alerts"></a>Alertes du moteur opérationnel

Les alertes du moteur opérationnel décrivent les incidents opérationnels détectés, ou les entités défectueuses.

| Titre | Description | Gravité |
|--|--|--|
| Une commande S7 Stop PLC a été envoyée | L’appareil source a envoyé une commande d’arrêt à un contrôleur de destination. Le contrôleur va s’arrêter jusqu’à ce qu’une commande de démarrage soit envoyée. | Avertissement |
| Échec d’opération BACNet | Un serveur a retourné un code d’erreur. Cela indique une erreur de serveur ou une demande non valide d’un client. | Majeure |
| État de l’appareil MMS incorrect | Un appareil de fabrication virtuelle (VMD) MMS a envoyé un message d’état. Le message indique que le serveur n’est peut-être pas configuré correctement, qu’il est partiellement opérationnel ou qu’il n’est pas opérationnel du tout. | Majeure |
| Modification de configuration d’appareil | Une modification de configuration a été détectée sur un appareil source. | Secondaire |
| Dépassement continu de mémoire tampon des événements sur la station à distance | Un événement de dépassement de mémoire tampon a été détecté sur un appareil source. L’événement peut entraîner une altération des données, des défaillances des programmes ou l’exécution d’un code malveillant. | Majeure |
| Réinitialisation du contrôleur | Un appareil source a envoyé une commande de réinitialisation à un contrôleur de destination. Le contrôleur a cessé de fonctionner temporairement, puis a redémarré automatiquement. | Avertissement |
| Arrêt du contrôleur | L’appareil source a envoyé une commande d’arrêt à un contrôleur de destination. Le contrôleur va s’arrêter jusqu’à ce qu’une commande de démarrage soit envoyée. | Avertissement |
| L’appareil n’a pas pu recevoir une adresse IP dynamique | L’appareil source est configuré pour recevoir une adresse IP dynamique d’un serveur DHCP, mais n’a pas reçu d’adresse. Cela indique une erreur de configuration sur l’appareil ou une erreur opérationnelle sur le serveur DHCP. Il est recommandé d’informer l’administrateur réseau de l’incident | Majeure |
| Un appareil est soupçonné d’être déconnecté (ne répond pas) | Un appareil source n’a pas répondu à une commande qui lui a été envoyée. Il a peut-être été déconnecté lors de l’envoi de la commande. | Majeure |
| Échec de la demande de service CIP EtherNet/IP | Un serveur a retourné un code d’erreur. Cela indique une erreur de serveur ou une demande non valide d’un client. | Majeure |
| Échec de la commande du protocole d’encapsulation EtherNet/IP | Un serveur a retourné un code d’erreur. Cela indique une erreur de serveur ou une demande non valide d’un client. | Majeure |
| Dépassement de mémoire tampon des événements dans la station à distance | Un événement de dépassement de mémoire tampon a été détecté sur un appareil source. L’événement peut entraîner une altération des données, des défaillances des programmes ou l’exécution d’un code malveillant. | Majeure |
| Une opération de sauvegarde attendue n’a pas eu lieu | Une activité de transfert de sauvegarde ou de fichier attendue n’a pas eu lieu entre deux appareils. Cela peut indiquer des erreurs dans le processus de transfert de sauvegarde ou de fichier. | Majeure |
| Échec de la commande GE SRTP | Un serveur a retourné un code d’erreur. Cela indique une erreur de serveur ou une demande non valide d’un client. | Majeure |
| Une commande GE SRTP Stop PLC a été envoyée | L’appareil source a envoyé une commande d’arrêt à un contrôleur de destination. Le contrôleur va s’arrêter jusqu’à ce qu’une commande de démarrage soit envoyée. | Avertissement |
| Le bloc de contrôle GOOSE nécessite une configuration supplémentaire | Un appareil source a envoyé un message GOOSE indiquant que l’appareil doit être mis en service. Cela signifie que le bloc de contrôle GOOSE requiert une configuration supplémentaire et que des messages GOOSE sont partiellement ou totalement non opérationnels. | Majeure |
| La configuration du jeu de données GOOSE a été modifiée | Un jeu de données de message (identifié par un ID de protocole) a été modifié sur un appareil source. Cela signifie que l’appareil signalera un jeu de données différent pour ce message. | Avertissement |
| État inattendu du contrôleur Honeywell | Un contrôleur Honeywell a envoyé un message de diagnostic inattendu indiquant un changement d’état. | Avertissement |
| Erreur de client HTTP | L’appareil source a initié une demande non valide. | Avertissement |
| Adresse IP interdite | Le système a détecté du trafic entre un appareil source et une adresse IP qui n’est pas valide. Cela peut indiquer une configuration incorrecte ou une tentative de générer un trafic interdit. | Secondaire |
| Erreur d’authentification maître-esclave | Le processus d’authentification entre un appareil source DNP3 (maître) et un appareil de destination (station à distance) a échoué. | Secondaire |
| Échec de demande de service MMS | Un serveur a retourné un code d’erreur. Cela indique une erreur de serveur ou une demande non valide d’un client. | Majeure |
| Aucun trafic détecté sur l’interface d’un capteur | Un capteur a cessé de détecter le trafic réseau sur une interface réseau. | Critique |
| Le serveur OPC UA a déclenché un événement nécessitant l’attention de l’utilisateur | Un serveur OPC UA a envoyé une notification d’événement à un client. Ce type d’événement requiert l’attention de l’utilisateur | Majeure |
| Échec de demande de service OPC UA | Un serveur a retourné un code d’erreur. Cela indique une erreur de serveur ou une demande non valide d’un client. | Majeure |
| Station à distance redémarrée | Un redémarrage à froid a été détecté sur un appareil source. Cela signifie que l’appareil a été physiquement éteint, puis rallumé. | Avertissement |
| Redémarrages fréquents de station à distance | Un nombre excessif de redémarrages à froid a été détecté sur un appareil source. Cela signifie que l’appareil a été physiquement éteint, puis rallumé un nombre excessif de fois. | Secondaire |
| Configuration de station à distance modifiée | Une modification de configuration a été détectée sur un appareil source. | Majeure |
| Configuration corrompue de station à distance détectée | Cet appareil source DNP3 (station à distance) a signalé une configuration corrompue. | Majeure |
| Échec de commande DCP Profinet | Un serveur a retourné un code d’erreur. Cela indique une erreur de serveur ou une demande non valide d’un client. | Majeure |
| Réinitialisation aux paramètres d’usine d’un appareil Profinet | Un appareil source a envoyé une commande de réinitialisation aux paramètres d’usine à un appareil de destination Profinet. La commande de réinitialisation efface les configurations de l’appareil Profinet et arrête celui-ci. | Avertissement |
| Échec d’opération RPC | Un serveur a retourné un code d’erreur. Cela indique une erreur de serveur ou une demande non valide d’un client. | Majeure |
| La configuration du jeu de données du message des valeurs échantillonnées a été modifiée | Un jeu de données de message (identifié par un ID de protocole) a été modifié sur un appareil source. Cela signifie que l’appareil signalera un jeu de données différent pour ce message. | Avertissement |
| Échec irrécupérable d’appareil esclave | Une erreur de condition irrécupérable a été détectée sur un appareil source. Ce type d’erreur indique généralement une défaillance matérielle ou un échec d’exécution d’une commande spécifique. | Majeure |
| Suspicion de problèmes matériels dans une station à distance | Une erreur de condition irrécupérable a été détectée sur un appareil source. Ce type d’erreur indique généralement une défaillance matérielle ou un échec d’exécution d’une commande spécifique. | Majeure |
| Suspicion d’appareil MODBUS ne répondant pas | Un appareil source n’a pas répondu à une commande qui lui a été envoyée. Il a peut-être été déconnecté lors de l’envoi de la commande. | Secondaire |
| Trafic détecté sur l’interface d’un capteur | Un capteur a repris la détection du trafic réseau sur une interface réseau. | Avertissement |

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez [Gérer les événements d’alerte](how-to-manage-the-alert-event.md).
Découvrez comment [transférer des informations d’alerte](how-to-forward-alert-information-to-partners.md).
