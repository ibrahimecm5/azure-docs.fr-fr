---
title: Azure AD Connect Health avec classeur de rapport sur les adresses IP à risque AD FS | Microsoft Docs
description: Décrit le rapport sur les adresses IP à risque AD FS d’Azure Active Directory Connect Health avec des classeurs Azure Monitor.
services: active-directory
documentationcenter: ''
ms.reviewer: ''
author: billmath
manager: karen444
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/14/2021
ms.author: billmath
ms.custom: ''
ms.collection: ''
ms.openlocfilehash: 8463da51788c7faa680f42aaeb1999c174419366
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479435"
---
# <a name="risky-ip-report-workbook"></a>Classeur de rapport sur les adresses IP à risque 
> [!NOTE]
> Pour utiliser le classeur de rapport sur les adresses IP à risque, vous devez activer « ADFSSignInLogs » dans le panneau Paramètres de diagnostic. Il s’agit d’un flux Log Analytics avec les connexions AD FS envoyées à Azure AD par Connecter Health. Pour en savoir plus sur les connexions AD FS dans Azure AD, consultez notre documentation ici.

Les clients AD FS peuvent exposer des points de terminaison d’authentification par mot de passe à Internet pour fournir des services d’authentification permettant aux utilisateurs finaux d’accéder aux applications SaaS telles que Microsoft 365. Dans ce cas, il est possible pour un mauvais acteur de tenter de se connecter à votre système AD FS pour deviner le mot de passe d’un utilisateur final et accéder aux ressources de l’application. AD FS fournit la fonctionnalité de verrouillage de compte extranet pour éviter ce type d’attaques depuis AD FS dans Windows Server 2012 R2. Si vous utilisez une version antérieure, nous vous recommandons vivement de mettre à niveau votre système AD FS vers Windows Server 2016. <br />

En outre, il est possible qu’une seule adresse IP tente de se connecter plusieurs fois à la place de plusieurs utilisateurs. Dans ce cas, le nombre de tentatives par utilisateur peut se trouver sous le seuil pour la protection par verrouillage de compte dans AD FS. Azure AD Connect Health fournit désormais le « rapport d’adresse IP risquée » qui détecte cette condition et informe les administrateurs lorsque cela se produit. Voici les principaux avantages de ce rapport : 
- Détection des adresses IP qui dépassent un seuil d’échecs de connexion basée sur mot de passe
- Prise en charge des échecs de connexion dus à un mot de passe incorrect ou à un état de verrouillage extranet
- Prise en charge de l’activation des alertes via les alertes Azure
- Paramètres de seuil personnalisables qui correspondent à la stratégie de sécurité d’une organisation
- Requêtes personnalisables et visualisations étendues pour une analyse plus poussée
- Fonctionnalité étendue du rapport précédent sur les adresses IP à risque, qui sera déconseillée après le 24 janvier 2022.

## <a name="requirements"></a>Spécifications
1.  Connect Health pour AD FS installé et mis à jour vers le dernier agent.
2.  Espace de travail Log Analytics avec le flux « ADFSSignInLogs » activé.
3.  Autorisations d’utiliser les classeurs Azure AD Monitor. Pour utiliser des classeurs, vous avez besoin des éléments suivants :
- Un locataire Azure Active Directory avec une licence Premium (P1 ou P2).
- Accès à un espace de travail Log Analytics et aux rôles suivants dans Azure AD (en cas d’accès à Log Analytics via le portail Azure AD) : Administrateur de la sécurité, Lecteur de sécurité, Lecteur de rapports, Administrateur général


## <a name="what-is-in-the-report"></a>Contenu du rapport
Le classeur de rapport sur les adresses IP à risque est alimenté par les données du flux ADFSSignInLogs et dispose de requêtes préexistantes pour pouvoir visualiser et analyser rapidement les adresses IP à risque. Les paramètres peuvent être configurés et personnalisés pour les nombres de seuils. Le classeur est également configurable en fonction des requêtes. Chaque requête peut être mise à jour et modifiée en fonction des besoins de l’organisation.

Le classeur des adresses IP à risque analyse les données de ADFSSignInLogs pour vous aider à détecter les attaques par vaporisation de mot de passe ou par force brute. Le classeur comporte deux parties. La première partie « Analyse des adresses IP à risque » identifie les adresses IP à risque en fonction des seuils d’erreur désignés et de la longueur de la fenêtre de détection. La deuxième partie fournit les détails de connexion et le nombre d’erreurs pour les adresses IP sélectionnées.

[![Capture d’écran d’une vue du classeur avec des emplacements.](./media/how-to-connect-health-adfs-risky-ip-workbook/workbook-template-overview-1.png)](./media/how-to-connect-health-adfs-risky-ip-workbook/workbook-template-overview-1.png#lightbox)

- Le classeur affiche une visualisation de carte et une répartition de région pour une analyse rapide de l’emplacement des adresses IP à risque.
- La table des détails des adresses IP à risque correspond aux fonctionnalités du rapport des adresses IP à risque passé. Pour plus d’informations sur les champs de la table, consultez la section ci-dessous.
- La chronologie des adresses IP à risque affiche un aperçu rapide de toutes les anomalies ou pics dans les demandes dans un affichage chronologique
- Les détails de connexion et le nombre d’erreurs par adresse IP permettent une vue filtrée détaillée par IP ou utilisateur, à développer sur la table des détails. 

Chaque élément de la table de rapport des adresses IP à risque affiche des informations agrégées sur les échecs de connexion AD FS qui dépassent le seuil défini. Il fournit les informations suivantes : [![Capture d’écran montrant le rapport sur les adresses IP à risque avec les en-têtes de colonne mis en évidence.](./media/how-to-connect-health-adfs-risky-ip-workbook/risky-ip-table-1.png)](./media/how-to-connect-health-adfs-risky-ip-workbook/risky-ip-table-1.png#lightbox)

| Élément de rapport | Description |
| ------- | ----------- |
| Heure de début de la fenêtre de détection | Affiche l’horodatage basé sur l’heure locale du portail Azure au démarrage de la fenêtre de temps détection.<br /> Tous les événements quotidiens sont générés à minuit, heure UTC. <br />L’horodatage des événements se produisant toutes les heures est arrondi au début de l’heure. Vous pouvez trouver l’heure de début de la première activité sous l’élément « firstAuditTimestamp » du fichier exporté. |
| Longueur de la fenêtre de détection | Affiche le type de fenêtre de temps de détection. Les types de déclencheurs sont regroupés par heure ou par jour. Cela permet de faire la différence entre une attaque par force brute haute fréquence et une attaque lente, où le nombre de tentatives est distribué sur toute la journée. |
| Adresse IP | L’adresse IP risquée unique présentant une activité de mot de passe incorrect ou de connexion avec verrouillage extranet. Il peut s’agir d’une adresse IPv4 ou IPv6. |
| Nombre de mots de passe incorrects (50126) | Le nombre d’erreurs dues à un mot de passe incorrect sur l’adresse IP lors de la fenêtre de temps de détection. Les erreurs de mot de passe incorrect peuvent se produire plusieurs fois sur certains utilisateurs. Notez que cela n’inclut pas les tentatives ayant échoué en raison de mots de passe expirés. |
| Nombre d’erreurs de verrouillage extranet (30030) | Le nombre d’erreurs dues à un verrouillage extranet sur l’adresse IP lors de la fenêtre de temps de détection. Les erreurs de verrouillage extranet peuvent se produire plusieurs fois sur certains utilisateurs. Ces erreurs ne sont visibles que si le verrouillage extranet est configuré dans AD FS (versions 2012R2 ou une version ultérieure). <b>Remarque</b> Nous vous recommandons fortement d’activer cette fonction si vous autorisez les connexions extranet basées sur des mots de passe. |
| Tentative d’utilisateurs uniques | Le nombre de tentatives d’utilisateurs uniques sur l’adresse IP lors de la fenêtre de temps de détection. Cet élément permet de différencier une attaque d’utilisateur unique et une attaque de plusieurs utilisateurs.  |

Filtrez le rapport par adresse IP ou par nom d’utilisateur pour afficher une vue développée des détails des connexions pour chaque événement d’adresse IP à risque.

## <a name="load-balancer-ip-addresses-in-the-list"></a>Adresses IP de l’équilibreur de charge dans la liste
L’équilibreur de charge agrège les activités de connexion qui ont échoué et atteint le seuil d’alerte. Si vous voyez des adresses d’équilibreur de charge, il est très probable que votre équilibreur de charge externe n’envoie pas l’adresse IP cliente lorsqu’il transfère la requête au serveur proxy d’application web. Veuillez configurer correctement votre équilibreur de charge pour qu’il transfère l’adresse IP cliente. 

## <a name="configure-threshold-settings"></a>Configurer les paramètres de seuil
Le seuil d’alerte peut être mis à jour dans les paramètres de seuil. Le seuil est initialement défini par défaut dans le système. Les paramètres de seuil peuvent être définis par heure ou par jour de détection et peuvent être personnalisés dans les filtres.

[![Filtres de seuil](./media/how-to-connect-health-adfs-risky-ip-workbook/threshold-settings-1.png)](./media/how-to-connect-health-adfs-risky-ip-workbook/threshold-settings-1.png#lightbox)

| Élément de seuil | Description |
| --- | --- |
| Seuil du nombre de mot de passe incorrect et de verrouillages extranet  | Paramètre de seuil permettant de signaler l’activité et de déclencher la notification d’alerte quand le nombre de mots de passe erronés et de verrouillages extranet dépasse ce seuil par **heure ou jour**.|
| Seuil d’erreur de verrouillages extranet | Paramètre de seuil permettant de signaler l’activité et de déclencher la notification d’alerte quand le nombre de verrouillages extranet dépasse ce seuil par **heure ou jour**. La valeur par défaut est 50.|

La fenêtre de détection par **heure ou jour** peut être configurée via le bouton bascule au-dessus des filtres pour la personnalisation des seuils. 

## <a name="configure-notification-alerts-using-azure-monitor-alerts-through-the-azure-portal"></a>Configurer des alertes de notification à l’aide d’alertes d’Azure Monitor via le portail Azure :
[![Règle d’alertes Azure](./media/how-to-connect-health-adfs-risky-ip-workbook/azure-alerts-rule-1.png)](./media/how-to-connect-health-adfs-risky-ip-workbook/azure-alerts-rule-1.png#lightbox)
1.  Dans le portail Azure, recherchez « Monitor » dans la barre de recherche pour accéder au service Azure « Monitor ». Sélectionnez « Alertes » dans le menu de gauche, puis « + Nouvelle règle d’alerte ». 
2.  Dans le panneau « Créer une règle d’alerte » :
   * Étendue : cliquez sur « Sélectionner une ressource », puis sélectionnez votre espace de travail Log Analytics qui contient les ADFSSignInLogs que vous souhaitez superviser.
   * Condition : cliquez sur « Ajouter une condition ». Sélectionnez « Journal » pour le type de signal et « Log Analytics » pour le service Monitor. Choisissez « Recherche personnalisée dans les journaux ». 

3. Configurez la condition de déclenchement de l’alerte. Pour faire correspondre les notifications par e-mail dans le rapport des adresses IP à risque Connect Health, suivez les instructions ci-dessous.
   * Copiez et collez la requête suivante et spécifiez les seuils du nombre d’erreurs. Cette requête génère le nombre d’adresses IP qui dépassent les seuils d’erreur indiqués.

```
ADFSSignInLogs
| extend ErrorCode = ResultType
| where ErrorCode in ("50126", "300030")
| summarize badPasswordErrorCount = countif(ErrorCode == "50126"), extranetLockoutErrorCount = countif(ErrorCode == "300030") by IPAddress
| where extranetLockoutErrorCount > [TODO: put error count threshold here] 
```
Ou pour un seuil combiné :
```
badPasswordErrorCount + extranetLockoutErrorCount > [TODO: put error count threshold here] // Customized thresholds
```

> [!NOTE]
> La logique d’alerte signifie que l’alerte est déclenchée si au moins une adresse IP du nombre d’erreurs de verrouillage ou si le nombre de mots de passe incorrects et d’erreurs de verrouillage extranet dépasse les seuils désignés. Vous pouvez sélectionner la fréquence d’évaluation de la requête pour détecter les adresses IP à risque.


## <a name="faq"></a>Forum aux questions
**Pourquoi des adresses IP d’équilibreur de charge s’affichent-elles dans le rapport ?**  <br />
Si vous voyez des adresses d’équilibreur de charge, il est très probable que votre équilibreur de charge externe n’envoie pas l’adresse IP cliente lorsqu’il transfère la requête au serveur proxy d’application web. Veuillez configurer correctement votre équilibreur de charge pour qu’il transfère l’adresse IP cliente. 

**Que faire pour bloquer l’adresse IP ?**  <br />
Vous devez ajouter l’adresse IP malveillante identifiée dans le pare-feu ou la bloquer dans Exchange.   <br />

**Pourquoi ne vois-je aucun élément dans ce rapport ?** <br />
- Le flux Log Analytics « ADFSSignInLogs » n’est pas activé dans les Paramètres de diagnostic.
- Les activités de connexion ayant échoué ne dépassent pas les paramètres de seuil.
- Assurez-vous qu’aucune alerte vous avertissant que le service Health n’est pas à jour n’est active dans la liste des serveurs AD FS.  En savoir plus sur [la résolution de cette alerte](how-to-connect-health-data-freshness.md)
- Les audits ne sont pas activés dans les batteries de serveurs AD FS.

## <a name="next-steps"></a>Étapes suivantes
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Installation de l’agent Azure AD Connect Health](how-to-connect-health-agent-install.md)
