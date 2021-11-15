---
title: Vue d’ensemble de Registre confidentiel Microsoft Azure
description: Présentation de Registre confidentiel Azure, service hautement sécurisé pour la gestion des enregistrements de données sensibles
services: confidential-ledger
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: overview
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: 05b84abafb308b51236f5d92aa486e6ed8741204
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131438813"
---
# <a name="microsoft-azure-confidential-ledger-preview"></a>Registre confidentiel Microsoft Azure (préversion)

Registre confidentiel Microsoft Azure est un nouveau service, hautement sécurisé, pour la gestion des enregistrements de données sensibles. Sur la base d’un modèle de blockchain, Registre confidentiel Azure offre des avantages uniques en matière d’intégrité des données. Il s’agit notamment de l’immuabilité, qui autorise uniquement l’ajout au registre, et de l’inviolabilité pour garantir que tous les enregistrements sont conservés intacts.

Le registre confidentiel s’exécute exclusivement sur les enclaves matérielles sécurisées, un environnement d’exécution surveillé et isolé qui protège contre les attaques potentielles. En outre, personne n’est « au-dessus » du registre, pas même Microsoft. En nous excluant nous-mêmes de la solution, Registre confidentiel Azure s’exécute sur une base de calcul de confiance (TCB) minimaliste qui empêche l’accès aux développeurs de services de registre, aux techniciens des centres de données et aux administrateurs cloud.

Registre confidentiel Azure est adapté aux cas d’usage où les enregistrements de métadonnées critiques ne doivent pas être modifiés, sans limite de temps, pour la conformité réglementaire et l’archivage. Voici quelques exemples de choses que vous pouvez stocker sur votre compte :

- Enregistrements liés à vos transactions commerciales (par exemple, les transferts d’argent ou les modifications de documents confidentiels).
- Mises à jour des ressources approuvées (par exemple, applications de base ou contrats).
- Modifications administratives et de contrôle (par exemple, octroi d’autorisations d’accès).
- Événements informatiques opérationnels et de sécurité (par exemple, alertes Azure Security Center).

Pour plus d’informations, vous pouvez consulter la [démonstration de Registre confidentiel Azure au Microsoft Ignite 2020](https://mediusprodstatic.studios.ms/asset-b88de19d-4187-40c4-98f2-a65efc419e2a/OD221_1920x1080_AACAudio_1461.mp4?sv=2018-03-28&sr=b&sig=k5roi6WXnlqK1zP0fs5KYlJd4FD3Nuaf97z%2B2gV0aTs%3D&st=2020-09-22T08%3A05%3A01Z&se=2025-09-22T08%3A10%3A01Z&sp=r&rscd=filename%3DIG20-OD221-Inside%2BAzure%2BDatacenter%2BArchitecture%2Bwith%2BMark%2BRu.mp4).

## <a name="key-features"></a>Principales fonctionnalités

Le registre confidentiel est exposé via des API REST qui peuvent être intégrées dans des applications nouvelles ou existantes. Le registre confidentiel peut être géré par les administrateurs qui utilisent des API d’administration (plan de contrôle). Il peut également être appelé directement par le code d’application par le biais d’API fonctionnelles (plan de données). Les API d’administration prennent en charge les opérations de base, telles que la création, la mise à jour, la récupération et la suppression. Les API fonctionnelles permettent une interaction directe avec votre registre instancié et incluent des opérations telles que le placement et l’obtention de données.

## <a name="ledger-security"></a>Sécurité du registre

Cette section définit les protections de sécurité pour le registre. Les API du registre utilisent l’authentification basée sur les certificats clients. Actuellement, le registre prend en charge le processus d’authentification basé sur les certificats avec les rôles de propriétaire. Nous ajouterons plus tard la prise en charge de l’authentification par Azure Active Directory (AAD) ainsi que l’accès basé sur les rôles (par exemple, propriétaire, lecteur et collaborateur).

Les données du registre sont envoyées via une connexion TLS 1.2 et la connexion TLS 1.2 se termine dans les enclaves de sécurité matérielles (Intel® SGX). Cela garantit que personne ne peut intercepter la connexion entre le client d’un utilisateur et les nœuds serveur du registre confidentiel.

### <a name="ledger-storage"></a>Stockage du registre

Les registres confidentiels sont créés en tant que blocs dans les conteneurs de stockage de blobs appartenant à un compte Stockage Azure. Les données de transaction peuvent être stockées sous forme chiffrée ou en texte clair en fonction de vos besoins. Lorsque vous créez un registre, vous associez un compte de stockage à l’aide de la procédure décrite dans [Inscrire un principal de service Registre confidentiel](register-ledger-service-principal.md).

Le registre confidentiel peut être géré par les administrateurs qui utilisent des API d’administration (plan de contrôle) et peut être appelé directement par votre code d’application par le biais d’API fonctionnelles (plan de données). Les API d’administration prennent en charge les opérations de base, telles que la création, la mise à jour, la récupération et la suppression.

Les API fonctionnelles permettent une interaction directe avec votre registre confidentiel instancié et incluent des opérations telles que PUT et GET sur les données.

## <a name="preview-limitations"></a>Limitations de la préversion

- Une fois qu’un registre confidentiel est créé, vous ne pouvez pas modifier le type du registre.
- Registre confidentiel ne prend pas en charge la récupération d’urgence Azure standard pour l’instant. Toutefois, Registre confidentiel Azure propose une redondance intégrée dans la région Azure, car le registre confidentiel s’exécute sur plusieurs nœuds indépendants.
- La suppression du registre confidentiel Azure entraîne une suppression définitive, de sorte que vos données ne seront pas récupérables après la suppression.
- Les noms des registres confidentiels Azure doivent être uniques au niveau mondial. Les registres portant le même nom, quel que soit leur type, ne sont pas autorisés.

## <a name="terminology"></a>Terminologie

| Terme | Définition |
|--|--|
| ACL | Registre confidentiel Azure |
| Registre | Un enregistrement d’ajout immuable des transactions (également appelé blockchain) |
| Commit | Une confirmation qu’une transaction a été validée localement sur un nœud. Une validation locale par elle-même ne garantit pas qu’une transaction fait partie du registre. |
| Validation globale | Une confirmation que la transaction a été validée globalement et fait partie du registre. |
| Réception | Preuve que la transaction a été traitée par le registre. |

## <a name="next-steps"></a>Étapes suivantes

- [Architecture de Registre confidentiel Microsoft Azure](architecture.md)
- [Démarrage rapide : Portail Azure](quickstart-portal.md)
- [Démarrage rapide : Python](quickstart-python.md)
- [Démarrage rapide : Modèle Azure Resource Manager (ARM)](quickstart-portal.md)
