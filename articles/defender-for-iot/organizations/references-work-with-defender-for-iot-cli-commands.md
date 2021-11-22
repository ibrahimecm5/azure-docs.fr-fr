---
title: Utiliser les commandes CLI Defender pour IoT
description: Cet article décrit les commandes CLI Defender pour IoT pour les capteurs et les consoles de gestion locales.
ms.date: 11/09/2021
ms.topic: article
ms.openlocfilehash: 4561f0e9697826fff808d03e3bf38f0d546d7c8a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347177"
---
# <a name="work-with-defender-for-iot-cli-commands"></a>Utiliser les commandes CLI Defender pour IoT

Cet article décrit les commandes CLI pour les capteurs et les consoles de gestion locales. Les commandes sont accessibles aux utilisateurs suivants :

- Administrateur
- CyberX 
- Support

Pour commencer à travailler dans l’interface CLI, connectez-vous en utilisant un terminal. Par exemple, le nom de terminal `Putty` et l’utilisateur `Support`. 

## <a name="create-local-alert-exclusion-rules"></a>Créer des règles d’exclusion d’alerte locales

Vous pouvez créer une règle d’exclusion d’alerte locale en entrant la commande suivante dans l’interface CLI :

```azurecli-interactive
alerts exclusion-rule-create [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

Vous pouvez utiliser les attributs suivants avec les règles d’exclusion d’alerte :

| Attribut | Description |
|--|--|
| [-h] | Imprimer les informations d’aide pour la commande. |
| -n NAME | Nom de la règle en cours de création. |
| [-ts TIMES] | Intervalle de temps pendant lequel la règle est active. Cet attribut doit être spécifié comme suit :<br />`xx:yy-xx:yy`<br />Vous pouvez définir plusieurs intervalles en les séparant à l’aide d’une virgule. Par exemple : `xx:yy-xx:yy, xx:yy-xx:yy`. |
| [-dir DIRECTION] | Sens d’application de la règle. Cet attribut doit être spécifié comme suit :<br />`both | src | dst` |
| [-dev DEVICES] | Adresse IP et type d’adresse des appareils que la règle doit exclure, spécifiés comme suit :<br />`ip-x.x.x.x`<br />`mac-xx:xx:xx:xx:xx:xx`<br />`subnet: x.x.x.x/x` |
| [-a ALERTS] | Nom de l’alerte que la règle exclura :<br />`0x00000`<br />`0x000001` |

## <a name="append-local-alert-exclusion-rules"></a>Créer des règles d’exclusion d’alerte locales

Vous pouvez ajouter des règles d’exclusion d’alerte locale en entrant la commande suivante dans l’interface CLI :

```azurecli-interactive
alerts exclusion-rule-append [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

Les attributs utilisés ici sont les mêmes que ceux décrits dans la section Créer des règles d’exclusion d’alerte locale. La différence d’utilisation réside dans le fait qu’ici, les attributs sont appliqués aux règles existantes.

## <a name="show-local-alert-exclusion-rules"></a>Afficher les règles d’exclusion d’alerte locales

Entrez la commande suivante pour présenter la liste existante des règles d’exclusion :

```azurecli-interactive
alerts exclusion-rule-list [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

## <a name="delete-local-alert-exclusion-rules"></a>Supprimer des règles d’exclusion d’alerte locales

Vous pouvez supprimer une règle d’exclusion d’alerte existante en entrant la commande suivante :

```azurecli-interactive
alerts exclusion-rule-remove [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

Vous pouvez utiliser l’attribut suivant avec les règles d’exclusion d’alerte :

| Attribut | Description|
| --------- | ---------------------------------- |
| -n NAME | Nom de la règle à supprimer. |

## <a name="sync-time-from-the-ntp-server"></a>Synchroniser l’heure à partir du serveur NTP

Vous pouvez activer ou désactiver la synchronisation de l’heure à partir d’un serveur NTP spécifié.

### <a name="enable-ntp-sync"></a>Activer la synchronisation NTP

Entrez la commande suivante pour récupérer périodiquement l’heure auprès du serveur NTP spécifié :

```azurecli-interactive
ntp enable IP
```

L’attribut que vous pouvez définir dans la commande est l’adresse IP du serveur NTP.

### <a name="disable-ntp-sync"></a>Désactiver la synchronisation NTP

Entrez la commande suivante pour désactiver la synchronisation de l’heure avec le serveur NTP spécifié :

```azurecli-interactive
ntp disable IP
```

L’attribut que vous pouvez définir dans la commande est l’adresse IP du serveur NTP.

## <a name="network-configuration"></a>Configuration réseau

Le tableau suivant décrit les commandes disponibles pour configurer vos options de réseau pour Microsoft Defender pour IoT :

|Nom|Commande|Description|
|-----------|-------|-----------|
|Ping|`ping IP`| Effectuer un test ping sur une adresse en dehors de la plateforme Defender pour IoT.|
|Blink|`network blink`| Localiser une connexion en provoquant le clignotement des lumières de l’interface. |
|Reconfigurer le réseau |`network edit-settings`| Activer une modification dans les paramètres de configuration du réseau. |
|Afficher les paramètres du réseau |`network list`|Affiche les paramètres de la carte réseau. |
|Valider la configuration du réseau |`network validate` |Affiche les paramètres réseau de sortie. <br /> <br />Par exemple : <br /> <br />Current Network Settings: <br /> interface: eth0 <br /> ip: 10.100.100.1 <br />subnet: 255.255.255.0 <br />default gateway: 10.100.100.254 <br />dns: 10.100.100.254 <br />monitor interfaces: eth1|
|Importation d’un certificat |`certificate import FILE` |Importe le certificat HTTPS. Vous devrez spécifier le chemin d’accès complet à un fichier \*.crt. |
|Afficher la date |`date` |Retourne la date actuelle de l’hôte au format GMT. |

## <a name="network-capture-filter-configuration"></a>Configuration du filtre de capture réseau

La commande `network capture-filter` permet aux administrateurs d’éliminer le trafic réseau qui n’a pas besoin d’être analysé. Vous pouvez filtrer le trafic en utilisant une liste d’inclusion ou une liste d’exclusion.

```azurecli-interactive
network capture-filter
```

Une fois que vous avez entré la commande, vous êtes invité à répondre à la question suivante :

>`Would you like to supply devices and subnet masks you wish to include in the capture filter? [Y/N]:`

Sélectionnez `Y` pour ouvrir un fichier nano où vous pouvez ajouter un appareil, un canal, un port et un sous-réseau en utilisant la syntaxe suivante :

| Attribut | Description |
|--|--|
| 1.1.1.1 | Inclut tout le trafic pour cet appareil. |
| 1.1.1.1,2.2.2.2 | Inclut tout le trafic pour ce canal. |
| 1.1.1,2.2.2 | Inclut tout le trafic pour ce sous-réseau. |

Pour séparer les arguments, sautez une ligne.

Lorsque vous incluez un appareil, un canal ou un sous-réseau, le capteur traite tout le trafic valide pour cet argument, y compris les ports et le trafic qui ne seraient pas habituellement traités.

Vous êtes ensuite invité à répondre à la question suivante :

>`Would you like to supply devices and subnet masks you wish to exclude from the capture filter? [Y/N]:`

Sélectionnez `Y` pour ouvrir un fichier nano où vous pouvez ajouter un appareil, un canal, un port et des sous-réseaux en utilisant la syntaxe suivante :

| Attribut | Description |
|--|--|
| 1.1.1.1 | Exclut tout le trafic pour cet appareil. |
| 1.1.1.1,2.2.2.2 | Exclut tout le trafic pour ce canal (tout le trafic entre deux appareils). |
| 1.1.1.1,2.2.2.2,443 | Exclut tout le trafic pour ce canal par port. |
| 1.1.1 | Exclut tout le trafic pour ce sous-réseau. |
| 1.1.1,2.2.2 | Exclut tout le trafic entre les sous-réseaux. |

Pour séparer les arguments, sautez une ligne.

Lorsque vous excluez un appareil, un canal ou un sous-réseau, le capteur exclut tout le trafic valide pour cet argument.

### <a name="ports"></a>Ports

Incluez ou excluez les ports UDP et TCP pour tout le trafic.

>`502` : un port

>`502,443` : les deux ports

>`Enter tcp ports to include (delimited by comma or Enter to skip):`

>`Enter udp ports to include (delimited by comma or Enter to skip):`

>`Enter tcp ports to exclude (delimited by comma or Enter to skip):`

>`Enter udp ports to exclude (delimited by comma or Enter to skip):`

### <a name="components"></a>Composants

Vous êtes invité à répondre à la question suivante :

>`In which component do you wish to apply this capture filter?`

Les options suivantes sont disponibles : `all`, `dissector`, `collector`, `statistics-collector`, `rpc-parser` et `smb-parser`.

Le plus souvent, vous pouvez sélectionner `all`.

### <a name="custom-base-capture-filter"></a>Filtre de capture de base personnalisé

Le filtre de capture de base correspond à la ligne de base des composants. Par exemple, le filtre détermine les ports disponibles pour le composant.

Sélectionnez `Y` pour toutes les options suivantes. Tous les filtres sont ajoutés à la ligne de base une fois les modifications définies. Les modifications éventuelles que vous apportez remplaceront la ligne de base existante.

>`Would you like to supply a custom base capture filter for the dissector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the collector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the statistics-collector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the rpc-parser component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the smb-parser component? [Y/N]:`

>`Type Y for "internal" otherwise N for "all-connected" (custom operation mode enabled) [Y/N]:`

Si vous choisissez d’exclure un sous-réseau tel que 1.1.1 :

- `internal` exclura uniquement ce sous-réseau.

- `all-connected` exclura ce sous-réseau et tout le trafic provenant de ce sous-réseau et en direction de celui-ci.

Nous vous recommandons de sélectionner la valeur `internal`.

> [!NOTE]
> Vos choix sont utilisés pour tous les filtres dans l’outil et ne dépendent pas de la session. En d’autres termes, vous ne pouvez jamais choisir `internal` pour certains filtres et `all-connected` pour d’autres.

### <a name="comments"></a>Commentaires

Vous pouvez afficher les filtres dans ```/var/cyberx/properties/cybershark.properties``` :

- **statistics-collector** : `bpf_filter property` dans ```/var/cyberx/properties/net.stats.collector.properties```

- **dissector** : propriété`override.capture_filter`  dans ```/var/cyberx/properties/cybershark.properties```

- **rpc-parser** : propriété `override.capture_filter` dans ```/var/cyberx/properties/rpc-parser.properties```

- **smb-parser** : propriété `override.capture_filter` dans ```/var/cyberx/properties/smb-parser.properties```

- **collector** : propriété`general.bpf_filter` dans ```/var/cyberx/properties/collector.properties```

Vous pouvez restaurer la configuration par défaut en entrant le code suivant pour l’utilisateur de CyberX :

```azurecli-interactive
sudo cyberx-xsense-capture-filter -p all -m all-connected
```

## <a name="define-client-and-server-hosts"></a>Définir les hôtes du client et du serveur

Si Defender pour IoT n’a pas détecté automatiquement les hôtes du client et du serveur, entrez la commande suivante pour définir les hôtes du client et du serveur :

```azurecli-interactive
directions [-h] [--identifier IDENTIFIER] [--port PORT] [--remove] [--add]  
[--tcp] [--udp]
```

Vous pouvez utiliser les attributs suivants à l’aide de la commande `directions` :

| Attribut | Description |
|--|--|
| [-h] | Imprime les informations d’aide pour la commande. |
| [--identifier IDENTIFIER] | Identificateur de serveur. |
| [--port PORT] | Port du serveur. |
| [--remove] | Supprime un hôte de client ou serveur de la liste. |
| [--add] | Ajoute un hôte de client ou serveur à la liste. |
| [--tcp] | Utilise le protocole TCP pour communiquer avec cet hôte. |
| [--udp] | Utilise le protocole UDP pour communiquer avec cet hôte. |

## <a name="system-actions"></a>Actions système
Le tableau suivant décrit les commandes disponibles pour effectuer diverses actions système dans Defender pour IoT :

|Nom|Code|Description|
|----|----|-----------|
|Afficher la date|`date`|Retourne la date actuelle de l’hôte au format GMT.|
|Redémarrer l’hôte|`system reboot`|Redémarre l’appareil hôte.|
|Arrêter l’hôte|`system shutdown`|Arrête l’hôte.|
|Sauvegarder le système|`system backup`|Lance une sauvegarde immédiate (sauvegarde non planifiée).|
|Restaurer le système à partir d’une sauvegarde|`system restore`|Effectue une restauration à partir de la sauvegarde la plus récente.|
|Répertorier les fichiers de sauvegarde|`system backup-list`|Répertorie les attributs de sauvegarde disponibles.|
|Afficher l’état de tous les services de la plateforme Defender pour IoT|`system sanity`|Vérifie les performances du système en répertoriant l’état actuel de tous les services de la plateforme Defender pour IoT.|
|Afficher la version logicielle|`system version`|Affiche la version du logiciel actuellement exécuté sur le système.|

## <a name="deploy-ssl-and-tls-certificates-to-appliances"></a>Déployer des certificats SSL et TLS sur des appliances

Entrez la commande suivante pour importer des certificats d’entreprise SSL et TLS dans l’interface CLI :

```azurecli-interactive
cyberx-xsense-certificate-import
```
Pour utiliser l’outil, vous devez charger les fichiers de certificat sur l’appareil. Pour ce faire, vous pouvez utiliser des outils tels que WinSCP ou Wget. 

La commande prend en charge les indicateurs d’entrée suivants :

| Indicateur | Description |
|--|--|
| -H | Affiche la syntaxe d’aide de la ligne de commande. |
| --crt | Chemin d’accès au fichier de certificat (extension .crt). |
| --key | Fichier \*.key. La longueur de la clé doit être de 2 048 bits minimum. |
| --chain | Chemin d’accès au fichier de chaîne de certificat (facultatif). |
| --pass | Phrase secrète utilisée pour chiffrer le certificat (facultatif). |
| --passphrase-set | La valeur par défaut est **False**, **non utilisé**. <br />Définir sur **True** pour utiliser la phrase secrète précédente fournie avec le certificat précédent (facultatif). | 

Lorsque vous utilisez l’outil :

- Vérifiez que les fichiers de certificat sont lisibles sur l’appliance. 

- Vérifiez auprès du service informatique le domaine de l’appliance (tel qu’il apparaît dans le certificat) avec votre serveur DNS et l’adresse IP correspondante. 
    
## <a name="see-also"></a>Voir aussi

[API de capteur et de console de gestion Defender pour IoT](references-work-with-defender-for-iot-apis.md)
