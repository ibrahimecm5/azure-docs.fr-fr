---
title: Configurer la supervision de SNMP MIB
description: Vous pouvez effectuer une analyse du fonctionnement du capteur à l’aide de SNMP. Le capteur répond aux requêtes SNMP envoyées à partir d’un serveur de supervision autorisé.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: dc9baa68c39ec049f1ca717f1bb34a364d6bad10
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278718"
---
# <a name="set-up-snmp-mib-monitoring"></a>Configurer la supervision de SNMP MIB

Vous pouvez effectuer une analyse du fonctionnement du capteur à l’aide du protocole SNMP (Simple Network Management Protocol). Le capteur répond aux requêtes SNMP envoyées à partir d’un serveur de supervision autorisé. Le moniteur SNMP interroge régulièrement les OID de capteur (jusqu’à 50 fois par seconde).

Les versions SNMP prises en charge sont SNMP v2 ou SNMP v3. SNMP utilise le protocole UDP comme protocole de transport avec le port 161 (SNMP).

Avant de commencer la configuration de l’analyse SNMP, vous devez ouvrir le port UDP 161 dans le pare-feu.

## <a name="oids"></a>OID

| Console de gestion et capteur | OID | Format | Description |
|--|--|--|--|
| Nom de l’appliance | 1.3.6.1.2.1.1.5.0 | STRING | Nom de l’appliance pour la console de gestion locale |
| Fournisseur | 1.3.6.1.2.1.1.4.0 | STRING | Support Microsoft (support.microsoft.com) |
| Plateforme | 1.3.6.1.2.1.1.1.0 | STRING | Capteur ou console de gestion locale |
| Numéro de série | 1.3.6.1.4.1.53313.1 |STRING | Chaîne utilisée par la licence |
| Version du logiciel | 1.3.6.1.4.1.53313.2 | STRING | Chaîne de version complète Xsense et chaîne de version complète de gestion |
| Utilisation de l’UC | 1.3.6.1.4.1.53313.3.1 | GAUGE32 | Indication de 0 à 100 |
| Température de l’UC | 1.3.6.1.4.1.53313.3.2 | STRING | Indication en degrés Celsius de 0 à 100 sur la base de l’entrée Linux. « Aucun capteur trouvé » sera renvoyé à partir de toute machine qui n’a pas de capteur de température physique réel (par exemple, des machines virtuelles)|
| Utilisation de la mémoire | 1.3.6.1.4.1.53313.3.3 | GAUGE32 | Indication de 0 à 100 |
| Utilisation du disque | 1.3.6.1.4.1.53313.3.4 | GAUGE32 | Indication de 0 à 100 |
| État du service | 1.3.6.1.4.1.53313.5  |STRING | En ligne ou hors connexion si l’un des quatre composants essentiels est défaillant |
| État du service | 1.3.6.1.4.1.53313.5  |STRING | En ligne ou hors connexion si l’un des quatre composants essentiels est défaillant |
| Connecté localement/au cloud | 1.3.6.1.4.1.53313.6   |STRING | Indique si le capteur est connecté à Defender pour IoT dans Azure ou géré localement uniquement |
| État de la licence | 1.3.6.1.4.1.53313.5  |STRING | Indique si le fichier d’activation a expiré ou non |

   - Les clés non existantes répondent avec la valeur Null, HTTP 200, selon [Stack Overflow](https://stackoverflow.com/questions/51419026/querying-for-non-existing-record-returns-null-with-http-200).
    
   - Les MIB relatives au matériel (utilisation de l’UC, température de l’UC, utilisation de la mémoire, utilisation du disque) doivent être testées sur toutes les architectures et tous les capteurs physiques. La température de l’UC sur les machines virtuelles est supposée être sans objet.

Vous pouvez télécharger le journal qui contient toutes les requêtes SNMP reçues par le capteur, notamment les données de connexion et les données brutes du paquet.

Pour définir l’analyse du fonctionnement SNMP v2 :

1. Dans le menu latéral, sélectionnez **Paramètres système**.

2. Dans le volet **Détection active**, sélectionnez **Analyse SNMP MIB** :::image type="icon" source="media/how-to-set-up-snmp-mib-monitoring/snmp-icon.png" border="false":::.

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/edit-snmp.png" alt-text="Modifier votre fenêtre SNMP.":::

3. Dans la section **Hôtes autorisés**, sélectionnez **Ajouter un hôte** et entrez l’adresse IP du serveur qui effectue l’analyse du fonctionnement du système.

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/snmp-allowed-ip-addess.png" alt-text="Entrez l’adresse IP des hôtes autorisés.":::

4. Dans la section **Authentification**, dans la zone **Chaîne de communauté SNMP v2**, entrez la chaîne. La chaîne de communauté SNMP peut contenir jusqu’à 32 caractères et inclure toute combinaison de caractères alphanumériques (lettres majuscules, lettres minuscules et chiffres). Les espaces ne sont pas autorisés.

5. Sélectionnez **Enregistrer**.

Pour définir l’analyse du fonctionnement SNMP v3 :

1. Dans le menu latéral, sélectionnez **Paramètres système**.

2. Dans le volet **Détection active**, sélectionnez **Analyse SNMP MIB** :::image type="icon" source="media/how-to-set-up-snmp-mib-monitoring/snmp-icon.png" border="false":::.

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/edit-snmp.png" alt-text="Modifier votre fenêtre SNMP.":::

3. Dans la section **Hôtes autorisés**, sélectionnez **Ajouter un hôte** et entrez l’adresse IP du serveur qui effectue l’analyse du fonctionnement du système.

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/snmp-allowed-ip-addess.png" alt-text="Entrez l’adresse IP des hôtes autorisés.":::

4. Dans la section **Authentification**, procédez comme suit :

    | Paramètre | Description |
    |--|--|
    | **Nom d’utilisateur** | Le nom d’utilisateur SNMP peut contenir jusqu’à 32 caractères et inclure toute combinaison de caractères alphanumériques (lettres majuscules, lettres minuscules et chiffres). Les espaces ne sont pas autorisés. <br /> <br />Le nom d’utilisateur pour l’authentification SNMP v3 doit être configuré sur le système et sur le serveur SNMP. |
    | **Mot de passe** | Entrez un mot de passe d’authentification respectant la casse. Le mot de passe d’authentification peut contenir entre 8 et 12 caractères et inclure toute combinaison de caractères alphanumériques (lettres majuscules, lettres minuscules et chiffres). <br /> <br/>Le nom d’utilisateur pour l’authentification SNMP v3 doit être configuré sur le système et sur le serveur SNMP. |
    | **Type d’authentification** | Sélectionnez MD5 ou SHA. |
    | **Chiffrement** | Sélectionnez DES ou AES. |
    | **Clé secrète** | La clé doit contenir exactement huit caractères et peut inclure toute combinaison de caractères alphanumériques (lettres majuscules, lettres minuscules et chiffres). |

5. Sélectionnez **Enregistrer**.

## <a name="see-also"></a>Voir aussi

[Exporter les journaux de dépannage](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md)
