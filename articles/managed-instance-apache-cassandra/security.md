---
title: Sécurité dans Azure Managed Instance pour Apache Cassandra – Vue d’ensemble
description: Découvrez les meilleures pratiques en matière de sécurité des bases de données et les principales fonctionnalités d’Azure Managed Instance pour Apache Cassandra. Elles vous permettent d’éviter, de détecter et de contrer des violations de base de données.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: conceptual
ms.date: 10/29/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: a5a34f539c8f2820c46736783f6d23d5b265164c
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131858653"
---
# <a name="security-in-azure-managed-instance-for-apache-cassandra---overview"></a>Sécurité dans Azure Managed Instance pour Apache Cassandra – Vue d’ensemble

Cet article décrit les meilleures pratiques en matière de sécurité de la base de données et les principales fonctionnalités d’Azure Managed Instance pour Apache Cassandra qui vous aident à prévenir, détecter et contrer les violations de la base de données.

## <a name="how-do-i-secure-my-database"></a>Comment sécuriser une base de données

La sécurité des données est une responsabilité partagée entre vous, le client et le fournisseur de votre base de données. Selon le fournisseur de base de données que vous choisissez, la part de votre responsabilité peut varier. Si vous choisissez une solution locale, vous devez fournir tous les éléments de la protection de point de terminaison à la sécurité physique de votre matériel, ce qui n’est pas une tâche facile. Si vous choisissez un service géré comme Azure Managed Instance pour Apache Cassandra, votre domaine de préoccupation diminue. 

## <a name="how-does-azure-managed-instance-secure-my-database"></a>Comment Azure Managed Instance sécurise-t-il ma base de données ?


|Exigence de sécurité|Approche de la sécurité d’Azure Managed Instance pour Apache Cassandra|
|---|---|
|Sécurité du réseau| Les ressources d’Azure Managed Instance pour Apache Cassandra sont hébergées sur un client Microsoft, avec des cartes d’interface réseau (NIC) pour chaque ressource injectée exclusivement dans des réseaux virtuels utilisant des adresses IP privées. Aucune adresse IP publique n’est exposée avec ce service.|
|Sauvegardes en ligne automatisées|Les centres de données d’Azure Managed Instance pour Apache Cassandra sont sauvegardés toutes les 4 heures et conservés pendant deux jours. Les sauvegardes sont conservées dans des comptes de stockage locaux.|
|Restauration de données supprimées|Les sauvegardes en ligne automatisées permettent de récupérer des données supprimées accidentellement. Vous pouvez sauvegarder les données à n’importe quel point de la période de deux jours suivant l’événement de suppression.|
|HTTPs/SSL/TLS et chiffrement de disque | Dans Azure Managed Instance pour Apache Cassandra, toutes les données sont chiffrées au repos. Le protocole SSL (TLS 1 2) du serveur et un chiffrement de nœud à nœud sont appliqués. Le protocole SSL du client est une configuration facultative. Les clés gérées par le client pour le chiffrement des données sur disque sont prises en charge. Pour plus d’informations, consultez l’article [ici](customer-managed-keys.md). |
|Surveillance des attaques|Azure Managed Instance pour Apache Cassandra est intégré avec [Azure Monitor](../azure-monitor/overview.md). À l’aide des enregistrements d’audit et des journaux d’activité, vous pouvez surveiller les activités normales et anormales de votre compte. Vous pouvez afficher les opérations effectuées sur vos ressources, la personne qui a initié une opération, le moment auquel l’opération a été réalisée, l’état de l’opération et bien plus encore.|
|Réponse aux attaques|Une fois que vous avez contacté le support Azure pour signaler une attaque potentielle, un processus de réponse aux incidents en cinq étapes est lancé. L’objectif de ce processus en cinq étapes est de restaurer aussi rapidement que possible dans des conditions normales les opérations et la sécurité du service suite à la détection d’un problème et au lancement d’une investigation.|
|Serveurs corrigés|En tant que base de données gérée, Azure Managed Instance pour Apache Cassandra ne nécessite aucune gestion ou correction des serveurs. Tout se fait automatiquement.|
|Certifications| Pour obtenir la liste la plus récente des certifications, consultez le [site Conformité Azure](https://www.microsoft.com/trustcenter/compliance/complianceoffering).


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les certifications Microsoft, consultez le [Centre de confidentialité Azure](https://azure.microsoft.com/support/trust-center/).