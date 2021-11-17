---
title: Comparer Azure Database pour PostgreSQL – Serveur unique et Serveur flexible
description: Comparaison détaillée des fonctionnalités et des capacités entre Azure Database pour PostgreSQL Serveur unique et Serveur flexible
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/02/2021
ms.openlocfilehash: c5b4f6611dc5ad5a514eaf1affd0517c14fcd353
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132337109"
---
# <a name="comparison-chart---azure-database-for-postgresql-single-server-and-flexible-server"></a>Tableau de comparaison : Azure Database pour PostgreSQL Serveur unique et Serveur flexible

> [!IMPORTANT]
> Le serveur flexible Azure Database pour PostgreSQL est en préversion

Le tableau suivant fournit une comparaison générale des fonctionnalités et des capacités entre Serveur unique et Serveur flexible.

| **Fonctionnalité/capacité** | **Serveur unique** | **Serveur flexible** |
| ---- | ---- | ---- |
| **Général**  | | |
| Disponibilité générale | GA depuis 2018 | Version préliminaire publique |
| PostgreSQL | Communauté | Communauté |
| Versions | 9.6, 10, 11 | 11, 12, 13 |
| SE sous-jacent | Windows | Linux  |
| Sélection d’AZ pour la colocation d’applications | Non | Oui |
| Dispositif intégré de regroupement des connexions | No | Oui (PgBouncer)|
| **Connectivité** | | |
| Nom d’utilisateur dans la chaîne de connexion | `<user_name>@server_name`. Par exemple, `pgadmusr@mypgServer` | Nom d’utilisateur uniquement. Par exemple, `pgadmusr` | 
| lc_collate  | English_United States.1252 | en_US.utf8 |
| lc_ctype    | English_United States.1252 |en_US.utf8 |
|lc_messages | English_United States.1252 |en_US.utf8|
| lc_monetary | English_United States.1252 |  en_US.utf-8 |
| lc_numeric  | English_United States.1252 |  en_US.utf-8 |
| lc_time     | English_United States.1252 | en_US.utf8 |
| Port de connexion | 5432 | 5432 (DB), 6432 (PgBouncer) |
| Bande passante connections | 1982 | 5 000 |
| Limite des connexions configurable ? | No | Oui (paramètre `max_connections`) |
| **Calcul et stockage** | | |
| Niveaux de calcul | De base, Usage général, À mémoire optimisée | Burstable, Usage général, À mémoire optimisée |
| Références SKU Burstable | Non | Oui |
| Mise à l’échelle sur plusieurs niveaux de calcul | Impossible de mettre à l’échelle le niveau De base | Oui. Possibilité de mise à l’échelle sur plusieurs niveaux |
| Arrêter/démarrer | No | Oui (pour toutes les références SKU de calcul). Seul le calcul est arrêté/démarré |
| Bande passante Taille de stockage | 1 To (De base), 4 To ou 16 To (Usage général, À mémoire optimisée). Remarque : Toutes les régions ne prennent pas en charge la taille de 16 To. | 16 To |
| Taille de stockage minimale | 5 Go (De base), 100 Go (Usage général, À mémoire optimisée) | 32 Go |
| Croissance automatique du stockage | Oui (incréments de 1 Go) | No |
| Nb max. d’IOPS | De base : variable. Usage général/À mémoire optimisée : jusqu’à 20 ko  | Jusqu’à 20 ko |
| **Mise en réseau et sécurité** | | |
| Mise en réseau prise en charge | Réseau virtuel, liaison privée, accès public | Accès privé (injection de réseau virtuel dans un sous-réseau délégué), accès public |
| Contrôle d’accès public | Pare-feu | Pare-feu |
| Prise en charge des liaisons privées | Oui |Non|
| Prise en charge de la zone DNS privée | Non | Oui |
| Possibilité de passer d’un accès privé à un accès public | Non | Non |
| Prise en charge de TLS | TLS 1.2 | TLS 1.2, 1.3 appliqué|
| Possibilité de désactiver SSL | Oui | Non |
| Authentification SCRAM | Non | Oui |
| **Haute disponibilité** | | |
| Haute disponibilité redondante interzone | No | Oui (une mise en veille synchrone est établie sur une autre zone au sein d’une région) |
| Configuration de la haute disponibilité | Intégrée avec stockage épinglé à une zone. Le calcul peut flotter entre les régions. | Calcul et stockage physiquement séparés, approvisionnés sur deux zones |
| Coût | 1x | 2x (calcul + stockage) |
| Disponibilité avec une configuration sans haute disponibilité | Redémarrage automatique, réadressage de calcul | Redémarrage automatique, réadressage de calcul
| Protection contre les défaillances de zone | Calcul : oui. Stockage : non | Calcul et stockage : oui |
| Protection contre les défaillances de région | Non | Non |
| Mode de réplication à haute disponibilité | N/A | Réplication physique Postgres en streaming en mode SYNC
| La mise en veille peut être utilisée à des fins de lecture | N/A | Non |
| Impact sur les performances des applications | Non (pas de réplication) | Oui (en raison de la réplication de synchronisation. Dépend de la charge de travail) |
| Basculement automatique | Oui (tourne un autre serveur)| Oui |
| Chaîne de connexion d’application après le basculement | Aucun changement | Aucun changement |
| **Réplication logique** | | |
| Prise en charge du décodage logique | Oui | Oui |
| Prise en charge de la réplication logique native | Non | Oui |
| Prise en charge de l’extension PgLogical | Non | Oui |
| Prise en charge la réplication logique avec HA | N/A | Limité |
| **Récupération d’urgence** | | |
| Récupération d’urgence inter-région | Utilisation de réplicas en lecture, sauvegarde géoredondante | N/A |
| Récupération d’urgence à l’aide de réplica | Utilisation de la réplication physique asynchrone | N/A |
| Basculement automatique | Non | N/A |
| Possibilité d’utiliser le même point de terminaison lecture/écriture | Non | N/A |
| **Sauvegarde et récupération** | | |
| Sauvegardes automatisées | Oui | Oui |
| Rétention des sauvegardes | 7 à 35 jours | 7 à 35 jours |
| Capacité de restauration à un instant dans le passé à tout moment pendant la période de rétention | Oui | Oui
| Possibilité de restauration sur une autre zone | N/A | Oui |
| Possibilité de restauration sur un autre réseau virtuel | Non | Oui |
| Possibilité de restauration dans une autre région | Oui (Géoredondant) | No |
| Possibilité de restaurer un serveur supprimé | Limité via l’API | No |
| **Réplica en lecture** | | |
| Prise en charge des réplicas en lecture | Oui | Non |
| Nombre de réplicas en lecture | 5 | N/A |
| Mode de réplication | Async | N/A |
| Prise en charge interrégionale | Oui | N/A |
| **Fenêtre de maintenance** | | |
| Fenêtre de planification du système | Oui | Oui |
| Fenêtre planifiée par le client| No | Oui (peut choisir n’importe quelle heure n’importe quel jour) |
| Période de notification | 3 jours | 5 jours |
| Période de maintenance | À tout moment dans la fenêtre de 15 heures | Fenêtre de 1 h | 
| **Métriques** | | |
| Erreurs | Connexions ayant échoué | Connexions ayant échoué|
| Latence | Décalage maximal entre les réplicas, décalage de réplica | N/A |
| Saturation | Stockage Sauvegarde utilisé, pourcentage de l’UC, pourcentage d’E/S, pourcentage de mémoire, limite de stockage du journal du serveur, pourcentage de stockage du journal du serveur, stockage du journal du serveur utilisé, limite de stockage, pourcentage de stockage, stockage utilisé | Stockage Sauvegarde utilisé, crédits d’UC consommés, crédits d’UC restants, pourcentage de l’UC, profondeur de la file d’attente du disque, IOPS, pourcentage de mémoire, IOPS de lecture, débit de lecture en octets/s, stockage libre, pourcentage de stockage, stockage utilisé, stockage du journal des transactions utilisé, IOPS d’écriture, débit d’écriture en octets/s |
| Trafic | Connexions actives, Entrée réseau, Sortie réseau | Connexions actives, ID de transaction maximale utilisée, Entrée réseau, Sortie réseau, connexions réussies |
| **Extensions** | | (offre les versions les plus récentes)|
| TimescaleDB, orafce, plv8 | Oui | Non |
| PgCron, lo, pglogical | Non | Oui |
| pgAudit | PRÉVERSION | Oui |
| **Autres fonctionnalités** | | |
| Clé gérée par le client (BYOK) | Oui | Non |
| Alertes | Oui | Oui |
| Microsoft Defender pour le cloud | Oui | Non |
| Intégrité des ressources | Oui | Non |
| État d’intégrité du service | Oui | Oui |
| Insights des performances (iPerf) | Oui | Oui (préversion) |
| Prise en charge des mises à jour de version principale | Non | Non |
| Mises à niveau de version mineure | Oui. Automatique pendant la fenêtre de maintenance | Oui. Automatique pendant la fenêtre de maintenance |


## <a name="next-steps"></a>Étapes suivantes

- Comprendre [ce qui est disponible pour les options de calcul et de stockage – Serveur flexible](concepts-compute-storage.md)
- En savoir plus sur les [versions de bases de données PostgreSQL prises en charge dans Serveur flexible](concepts-supported-versions.md)
- En savoir plus sur les [limites actuelles dans Serveur flexible](concepts-limits.md)
