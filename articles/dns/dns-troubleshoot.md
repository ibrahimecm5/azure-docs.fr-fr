---
title: Guide de résolution des problèmes – Azure DNS
description: Dans ce parcours d’apprentissage, prenez en main la résolution des problèmes courants liés à Azure DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: troubleshooting
ms.date: 11/10/2021
ms.author: rohink
ms.openlocfilehash: bc003c640e51b855f446878aa0b5829b2d2c09c5
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132400354"
---
# <a name="azure-dns-troubleshooting-guide"></a>Guide de résolution des problèmes d’Azure DNS

Cet article fournit des solutions aux problèmes fréquemment rencontrés avec Azure DNS.

Si cette procédure ne résout pas votre problème, vous pouvez également rechercher ou publier votre problème sur notre [page de questions Microsoft Q&A pour obtenir l’aide de la communauté](/answers/topics/azure-virtual-network.html). Vous pouvez également effectuer une demande de support Azure.

## <a name="i-cant-create-a-dns-zone"></a>Impossible de créer une zone DNS

Pour résoudre les problèmes courants, essayez une ou plusieurs des étapes suivantes :

1.  Consultez les journaux d’audit Azure DNS pour déterminer la cause de l’échec.
2.  Chaque nom de zone DNS doit être unique au sein de son groupe de ressources. Autrement dit, deux zones DNS portant le même nom ne peuvent pas partager un même groupe de ressources. Essayez d’utiliser un autre nom de zone ou un autre groupe de ressources.
3.  Il se peut que l'erreur « Vous avez atteint ou dépassé le nombre maximal de zones dans l'abonnement {subscription ID} » s'affiche. Utilisez un autre abonnement Azure, supprimez certaines zones ou contactez le support Azure pour augmenter votre limite d’abonnement.
4.  Il se peut que l'erreur « La zone {Nom de la zone} n’est pas disponible » s'affiche. Cette erreur signifie qu’Azure DNS n’a pas pu allouer des serveurs de noms de cette zone DNS. Essayez d’utiliser un autre nom de zone. Sinon, si vous êtes le propriétaire du nom de domaine, vous pouvez contacter le support Azure qui peut vous allouer des serveurs de noms.

### <a name="recommended-articles"></a>Articles recommandés

* [Enregistrements et zones DNS](dns-zones-records.md)
* [Création d’une zone DNS](./dns-getstarted-portal.md)

## <a name="i-cant-create-a-dns-record"></a>Impossible de créer un enregistrement DNS

Pour résoudre les problèmes courants, essayez une ou plusieurs des étapes suivantes :

1.  Consultez les journaux d’audit Azure DNS pour déterminer la cause de l’échec.
2.  Le jeu d’enregistrements existe-t-il déjà ?  Azure DNS gère les enregistrements à l’aide de *jeux* d’enregistrement, qui sont une collection d’enregistrements ayant le même nom et le même type. Si un enregistrement ayant le même nom et le même type existe déjà, pour ajouter un autre enregistrement vous devez modifier le jeu d’enregistrements existant.
3.  Essayez-vous de créer un enregistrement à l’extrémité de la zone DNS (la « racine » de la zone) ? Si c’est le cas, la convention DNS exige d’utiliser le caractère ’@’ comme nom d’enregistrement. Notez également que les normes DNS n’autorisent pas les enregistrements CNAME au niveau de l’extrémité de la zone.
4.  Constatez-vous un conflit d’enregistrement CNAME ?  Les normes DNS n’autorisent pas l’utilisation d’un même nom pour deux enregistrements CNAME de type différent. Si vous avez un enregistrement CNAME existant, la création d’un enregistrement ayant le même nom mais de type différent échoue.  De même, la création d’un enregistrement CNAME échoue si le nom correspond à un enregistrement existant d’un autre type. Supprimez le conflit en supprimant l’autre enregistrement ou en choisissant un nom d’enregistrement différent.
5.  Vous avez atteint la limite du nombre de jeux d’enregistrements autorisé dans une zone DNS ? Le nombre actuel de jeux d’enregistrement et le nombre maximal de jeux d’enregistrements sont affichés dans le portail Azure, sous les « Propriétés » de la zone. Si vous avez atteint cette limite, supprimez des jeux d’enregistrements ou contactez le support Azure pour augmenter votre limite pour cette zone, puis réessayez. 

### <a name="recommended-articles"></a>Articles recommandés

* [Enregistrements et zones DNS](dns-zones-records.md)
* [Création d’une zone DNS](./dns-getstarted-portal.md)

## <a name="i-cant-resolve-my-dns-record"></a>Impossible de résoudre mon enregistrement DNS

La résolution de noms DNS est un processus en plusieurs étapes. Elle peut échouer pour de nombreuses raisons. Les étapes suivantes vous permettent de déterminer quelle résolution DNS échoue pour un enregistrement DNS dans une zone hébergée au sein d’Azure DNS.

1.  Vérifiez que les enregistrements DNS ont été configurés correctement dans Azure DNS. Passez en revue les enregistrements DNS dans le portail Azure, et vérifiez que le nom de la zone, le nom de l’enregistrement et le type d’enregistrement sont corrects.
2.  Vérifiez que les enregistrements DNS sont résolus correctement sur les serveurs de noms Azure DNS.
    - Si vous exécutez des requêtes DNS à partir de votre ordinateur local, vous voyez peut-être les résultats mis en cache qui ne reflètent pas l’état actuel des serveurs de noms.  En outre, les réseaux d’entreprise utilisent des serveurs proxy DNS qui empêchent de diriger les requêtes DNS vers des serveurs de noms spécifique.  Pour éviter ces problèmes, utilisez un service de résolution de noms basé sur le web tel que [digwebinterface](https://digwebinterface.com).
    - Veillez à spécifier les serveurs de noms corrects pour votre zone DNS, comme affiché dans le portail Azure.
    - Vérifiez que le nom DNS est correct (vous devez spécifier le nom complet, notamment le nom de la zone) et que le type d’enregistrement est correct
3.  Vérifiez que le nom de domaine DNS a été correctement [délégué aux serveurs de noms Azure DNS](dns-domain-delegation.md). [De nombreux sites web tiers proposent la validation de délégation DNS](https://www.bing.com/search?q=dns+check+tool). Il s’agit d’un test de délégation de *zone*. Vous devez donc uniquement entrer le nom de zone DNS et pas le nom complet d’enregistrement.
4.  Après avoir suivi les étapes ci-dessus, votre enregistrement DNS devrait se résoudre correctement. Pour vérifier, vous pouvez à nouveau utiliser [digwebinterface](https://digwebinterface.com), cette fois en vous servant des paramètres de serveur de noms par défaut.

### <a name="recommended-articles"></a>Articles recommandés

* [Délégation de domaine à Azure DNS](dns-domain-delegation.md)

## <a name="dns-zone-status-and-unhealthy-delegation-scenarios"></a>État de la zone DNS et scénarios de délégation non saine

L’état de la zone DNS indique l’état actuel de la zone. L’état de la zone DNS peut être **Inconnu**, **Disponible** et **Détérioré**.

### <a name="unknown"></a>Unknown

Lorsqu’une ressource vient d’être créée, les signaux d’intégrité de ces nouvelles ressources ne sont pas immédiatement disponibles. Un maximum de 24 heures peut s’écouler pour obtenir les signaux d’intégrité corrects pour les zones DNS. Pendant ce temps, l’intégrité des zones DNS sera indiquée par **Inconnu**.

Lorsque la vérification de l’intégrité des ressources n’a pas reçu d’informations sur les zones DNS pendant plus de six heures, les zones sont marquées Inconnu. Même si cet état n’est pas une indication définitive de l’état de la ressource, il s’agit d’un point de données important dans le processus de dépannage. Une fois que le signal est reçu et que la ressource fonctionne comme prévu, l’état de la ressource devient **Disponible** après quelques minutes.

La capture d’écran suivante est un exemple de message de vérification de l’intégrité des ressources.

:::image type="content" source="./media/dns-troubleshoot/unknown-status.png" alt-text="Capture d’écran de l’état Inconnu.":::

### <a name="available"></a>Disponible

Un état **Disponible**  indique que la vérification de l’intégrité des ressources n’a pas détecté de problème de délégation avec vos zones DNS. Cet état signifie que les enregistrements de délégation NS sont correctement conservés dans votre zone principale et que les enregistrements destinés aux zones enfants ne sont pas présents dans votre zone principale. 

La capture d’écran suivante est un exemple de message de vérification de l’intégrité des ressources.

:::image type="content" source="./media/dns-troubleshoot/available-status.png" alt-text="Capture d’écran de l’état Disponible.":::

### <a name="degraded"></a>Détérioré

Un état **Détérioré** indique que la vérification de l’intégrité des ressources a détecté un problème de délégation avec vos zones DNS. Corrigez la configuration de la délégation et attendez 24 heures pour que l’état passe à **Disponible**.  

La capture d’écran suivante est un exemple de message de vérification de l’intégrité des ressources.

:::image type="content" source="./media/dns-troubleshoot/degraded-status.png" alt-text="Capture d’écran de l’état Détérioré.":::

Si 24 heures se sont écoulées après la correction de la configuration et que les zones DNS sont toujours à l’état Détérioré, contactez le support.  

### <a name="configuration-error-scenario"></a>Scénario d’erreur de configuration

Le scénario suivant illustre une erreur de configuration qui a entraîné l’état non sain des zones DNS.

**Délégation non saine**

Une zone principale contient des enregistrements de délégation NS qui permettent de déléguer le trafic du serveur principal aux zones enfants. Si un enregistrement de délégation NS est présent dans la zone parente, le serveur DNS est supposé masquer tous les autres enregistrements sous l’enregistrement de délégation NS, à l’exception des enregistrements Glue, et diriger le trafic vers la zone enfant correspondante en fonction de la requête de l’utilisateur. Si une zone parente contient d’autres enregistrements destinés aux zones enfants (zones déléguées) sous l’enregistrement de délégation NS, la zone est marquée comme non saine et son état devient **Détérioré**.

**Que sont les enregistrements Glue ?** Il s’agit d’enregistrements sous l’enregistrement de délégation, qui permettent de diriger le trafic vers les zones déléguées/enfants à l’aide de leurs adresses IP et sont configurés comme indiqué ci-dessous.

| Paramètre | Valeur |
| ------- | ----- |
| **Zone** | contoso.com |
| **Enregistrement de délégation** | NS enfant </br> ns1.child.contoso.com |
| **Enregistrement Glue** | ns1.child A 1.1.1.1 |

#### <a name="example-of-an-unhealthy-zone"></a>Exemple de zone non saine

Voici un exemple de zone contenant des enregistrements sous la délégation NS.

* Nom de zone : contoso.com

| Name | Type | TTL | Valeur |
| ---- | ---- | --- | ----- |
| @ | NS | 3600 | ns1-04.azure-dns.com. |
| @ | SOA | 3600 | _Valeurs SOA_ |
| * | Un | 3600 | 255.255.255.255 |
| **child** | **NS** | **3600** | **ns1-08.azure-dns.com** (enregistrement de délégation NS) |
| _**foo.child**_ | _**A**_ | _**3600**_ | _**10.10.10.10**_ |
| _**txt.child**_ | _**TXT**_ | _**3600**_ | _**« enregistrement texte »**_ |
| abc.test | Un | 3600 | 5.5.5.5 |

Dans l’exemple précédent, **child** correspond aux enregistrements de délégation NS. Les enregistrements _**foo.child**_ et _**txt.child**_ sont des enregistrements qui doivent uniquement être présents dans la zone enfant, **child.contoso.com**. Ces enregistrements peuvent entraîner des incohérences s’ils ne sont pas supprimés de la zone parente, **contoso.com**. Ces incohérences pourraient faire en sorte que la zone soit considérée comme non saine avec un état **Détérioré**.

#### <a name="examples-of-when-a-zone-is-considered-healthy-or-unhealthy"></a>Exemples de cas où une zone est considérée saine ou non saine

| Exemple | État |
| ------- | ------ |
| La zone ne contient pas d’enregistrements de délégation NS, d’enregistrements Glue ni d’autres enregistrements. | **Healthy** |
| La zone contient uniquement des enregistrements de délégation NS. | **Healthy** |
| La zone contient uniquement des enregistrements de délégation NS et des enregistrements Glue. | **Healthy** |
| La zone contient des enregistrements de délégation NS et d’autres enregistrements (à l’exception des enregistrements Glue) sous un enregistrement de délégation, qui doivent être présents dans la zone enfant. | **Non intègre** |
| La zone contient des enregistrements de délégation NS, des enregistrements Glue et d’autres enregistrements (à l’exception des enregistrements Glue). | **Non intègre** |

**Comment résoudre ce problème ?** Pour le résoudre, localisez et supprimez tous les enregistrements à l’exception des enregistrements Glue sous les enregistrements de délégation NS dans votre zone parente.

**Comment localiser les enregistrements de délégation non saine ?** Un script a été créé pour rechercher les enregistrements de délégation non saine dans votre zone.  Le script signale les enregistrements qui ne sont pas sains.

1. Enregistrez le script situé à l’emplacement suivant : [Rechercher des enregistrements DNS non sains dans Azure DNS – Exemple de script PowerShell](./scripts/find-unhealthy-dns-records.md)

2. Exécutez le script comme indiqué dans l’éditeur de script.  Le script peut être modifié pour répondre à vos besoins.

**Informations d’historique**. Vous pouvez accéder à jusqu’à 14 jours d’historique d’intégrité dans la section Historique de l’intégrité de Resource Health. Cette section contient également la raison du temps d’arrêt (lorsqu’elle est disponible) pour les temps d’arrêt signalés par Resource Health. Actuellement, Azure indique le temps d’arrêt de votre ressource de zones DNS avec une granularité de 24 heures.

## <a name="how-do-i-specify-the-service-and-protocol-for-an-srv-record"></a>Comment spécifier le « service » et le « protocole » pour un enregistrement SRV ?

Azure DNS gère les enregistrements DNS en tant que jeux d’enregistrement, la collection d’enregistrements ayant le même nom et le même type. Pour le jeu d’enregistrements SRV, le « service » et le « protocole » doivent être spécifiés dans le nom du jeu d’enregistrements. Les autres paramètres SRV (« priorité », « poids », « port » et « cible ») sont spécifiés pour chaque enregistrement dans le jeu d’enregistrements.

Exemples de noms d’enregistrement SRV (nom de service « sip », protocole « tcp ») :

- \_sip.\_tcp (crée un jeu d’enregistrements à l’extrémité de la zone)
- \_sip.\_tcp.sipservice (crée un jeu d’enregistrements nommé « sipservice »)

### <a name="recommended-articles"></a>Articles recommandés

* [Enregistrements et zones DNS](dns-zones-records.md)
* [Création de jeux d’enregistrements et d’enregistrements DNS à l’aide du portail Azure](./dns-getstarted-portal.md)
* [Type d’enregistrement SRV (Wikipedia)](https://en.wikipedia.org/wiki/SRV_record)

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [Enregistrements et zones DNS](dns-zones-records.md)
* Pour commencer à utiliser le DNS Azure, découvrez comment [créer une zone DNS](./dns-getstarted-portal.md) et [créer des enregistrements DNS](./dns-getstarted-portal.md).
* Pour migrer une zone DNS, découvrez comment [importer et exporter un fichier de zone DNS](dns-import-export.md).
