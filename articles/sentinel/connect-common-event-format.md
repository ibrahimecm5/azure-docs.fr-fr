---
title: Transférer les journaux au format CEF de votre appareil ou dispositif dans Microsoft Sentinel | Microsoft Docs
description: Utilisez l’agent de Log Analytics, installé sur un redirecteur de journal Linux, pour ingérer les journaux envoyés au format CEF (Common Event format) sur Syslog dans votre espace de travail Microsoft Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 734ed8aaa1ad3557981c76f380227e7574861086
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521887"
---
# <a name="get-cef-formatted-logs-from-your-device-or-appliance-into-microsoft-sentinel"></a>Transférer les journaux au format CEF de votre appareil ou dispositif dans Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

De nombreux appareils et appareils de sécurité et de mise en réseau envoient leurs journaux système sur le protocole syslog dans un format spécialisé connu sous le nom de CEF (Common Event format). Ce format contient plus d’informations que le format Syslog standard, et il présente les informations dans une organisation clé-valeur analysée. L’agent de Log Analytics accepte les journaux CEF et les met en forme en particulier pour une utilisation avec Microsoft Sentinel, avant de les transférer vers votre espace de travail Microsoft Sentinel.

Cet article décrit le processus d’utilisation des journaux au format CEF pour connecter vos sources de données. Pour plus d'informations sur les connecteurs de données qui utilisent cette méthode, voir la [référence des connecteurs de données Microsoft Sentinel](data-connectors-reference.md).

Il existe deux étapes principales pour établir cette connexion, qui sont expliquées ci-dessous en détail :

- Désigner une machine Linux ou une VM en tant que transitaire de journaux dédié, y installer l'agent Log Analytics et configurer l'agent pour qu'il transmette les journaux à votre espace de travail Microsoft Sentinel. L’installation et la configuration de l’agent sont gérées par un script de déploiement.

- Configuration de votre appareil pour envoyer ses journaux au format CEF vers un serveur Syslog.

> [!NOTE]
> Les données sont stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Microsoft Sentinel.

## <a name="supported-architectures"></a>Architectures prises en charge

Le diagramme suivant décrit la configuration dans le cas d’une machine virtuelle Linux dans Azure :

 ![CEF dans Azure](./media/connect-cef/cef-syslog-azure.png)

Sinon, vous utiliserez la configuration suivante si vous utilisez une VM dans un autre cloud, ou une machine sur site :

 ![CEF local](./media/connect-cef/cef-syslog-onprem.png)

## <a name="prerequisites"></a>Prérequis

Un espace de travail Microsoft Sentinel est requis pour ingérer des données CEF dans Log Analytics.

- Vous devez avoir des droits de lecture et d'écriture sur cet espace de travail.

- Vous devez disposer d’autorisations de lecture sur les clés partagées pour accéder à l’espace de travail. [En savoir plus sur les clés d’espace de travail](../azure-monitor/agents/agent-windows.md).

## <a name="designate-a-log-forwarder-and-install-the-log-analytics-agent"></a>Désignation d’un redirecteur de journal et installation de l’agent de Log Analytics

Cette section décrit comment désigner et configurer la machine Linux qui transmettra les journaux de votre appareil à votre espace de travail Microsoft Sentinel.

Votre machine Linux peut être une machine physique ou virtuelle dans votre environnement sur site, une VM Azure ou une VM dans un autre cloud.

Utilisez le lien fourni sur la **page du connecteur de données CEF (Common Event format)** pour exécuter un script sur l’ordinateur désigné et effectuer les tâches suivantes :

- **Installe l’agent Log Analytics pour Linux** (également appelé agent OMS) et le configure aux fins suivantes :
    - l’écoute des messages CEF à partir du démon Syslog Linux intégré sur le port TCP 25226
    - l’envoi sécurisé des messages via TLS à votre espace de travail Microsoft Sentinel, où ils sont analysés et enrichis

- **Configure le démon Syslog Linux intégré** (rsyslog.d/syslog-ng) aux fins suivantes :
    - l’écoute des messages Syslog de vos solutions de sécurité sur le port TCP 514
    - le transfert des seuls messages qu’il identifie comme CEF à l’agent Log Analytics sur localhost à l’aide du port TCP 25226

Pour plus d'informations, voir [Déployer un transitaire de journaux pour ingérer les journaux Syslog et CEF dans Microsoft Sentinel](connect-log-forwarder.md).

### <a name="security-considerations"></a>Considérations relatives à la sécurité

Veillez à configurer la sécurité de la machine en fonction de la stratégie de sécurité de votre organisation. Par exemple, vous pouvez configurer votre réseau de sorte qu’il s’accorde à la stratégie de sécurité de votre réseau d’entreprise, et modifier les ports et les protocoles dans le démon pour les adapter à vos besoins.

Pour plus d'informations, voir [Sécurisation de la machine virtuelle dans Azure](../virtual-machines/security-policy.md) et [Bonnes pratiques pour la sécurité réseau](../security/fundamentals/network-best-practices.md).

Si vos périphériques envoient des journaux Syslog et CEF via TLS, par exemple lorsque votre transmetteur de journaux se trouve dans le nuage, vous devrez configurer le démon Syslog (rsyslog ou syslog-ng) pour communiquer en TLS. 

Pour plus d'informations, consultez les pages suivantes :

- [Chiffrement du trafic Syslog avec TLS – rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html)
- [Chiffrement des messages de journal avec TLS – syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298)

## <a name="configure-your-device"></a>Configurer votre appareil

Trouvez et suivez les instructions de configuration du fournisseur de votre appareil pour envoyer les journaux au format CEF à un SIEM ou à un serveur de journaux. 

Si votre produit apparaît dans la galerie des connecteurs de données, vous pouvez consulter la [référence des connecteurs de données Microsoft Sentinel](data-connectors-reference.md) pour obtenir de l'aide, où les instructions de configuration devraient inclure les paramètres de la liste ci-dessous.

   - Protocole = TCP
   - Port = 514
   - Format = CEF
   - Adresse IP : assurez-vous d’envoyer les messages CEF à l’adresse IP de la machine virtuelle dédiée à cet effet.

Cette solution prend en charge Syslog RFC 3164 ou RFC 5424.

> [!TIP]
> Définissez un protocole ou un numéro de port différent dans votre appareil si nécessaire, à condition d'effectuer les mêmes modifications dans le démon Syslog sur le transitaire de journaux.
>

## <a name="find-your-data"></a>Recherche de données

Il peut s'écouler jusqu'à 20 minutes après la connexion pour que les données apparaissent dans Log Analytics.

Pour rechercher des événements CEF dans Log Analytics, interrogez la `CommonSecurityLog` table dans la fenêtre de requête.

Certains produits figurant dans la galerie des connecteurs de données nécessitent l'utilisation d'analyseurs syntaxiques supplémentaires pour obtenir de meilleurs résultats. Ces analyseurs sont implémentés par le biais de l’utilisation de fonctions Kusto. Pour plus d’informations, consultez la section relative à votre produit sur la page de [référence des connecteurs de données Microsoft Sentinel](data-connectors-reference.md).

Pour rechercher des événements CEF pour ces produits, entrez le nom de la fonction Kusto comme objet de la requête, au lieu de « CommonSecurityLog ».

Vous pouvez trouver des exemples de requêtes, des classeurs et des modèles de règle d’analyse utiles, en particulier pour votre produit, dans l’onglet **étapes suivantes** de la page connecteur de données de votre produit dans le portail Microsoft Sentinel.

Si vous ne voyez pas de données, consultez la page de [résolution des problèmes de CEF](./troubleshooting-cef-syslog.md) pour obtenir de l’aide.

### <a name="changing-the-source-of-the-timegenerated-field"></a>Modification de la source du champ TimeGenerated

Par défaut, l’agent Log Analytics remplit le champ *TimeGenerated* du schéma en indiquant l’heure à laquelle il a reçu l’événement du démon Syslog. Par conséquent, l’heure à laquelle l’événement a été généré sur le système source n’est pas enregistrée dans Microsoft Sentinel.

Vous pouvez toutefois exécuter la commande suivante, qui permet de télécharger et d’exécuter le script `TimeGenerated.py`. Ce script configure l’agent Log Analytics de sorte qu’il remplisse le champ *TimeGenerated* en indiquant l’heure d’origine de l’événement sur son système source, et non l’heure à laquelle l’agent l’a reçu.

```bash
wget -O TimeGenerated.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/TimeGenerated.py && python TimeGenerated.py {ws_id}
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez découvert la façon dont Microsoft Sentinel collecte les journaux CEF des appliances et des solutions de sécurité. Pour en savoir plus sur la connexion de votre produit à Microsoft Sentinel, consultez les articles suivants :

- [Déployer un forwarder Syslog/CEF](connect-log-forwarder.md)
- [Référence des connecteurs de données Microsoft Sentinel](data-connectors-reference.md)
- [Résoudre les problèmes de connectivité du redirecteur de journal](troubleshooting-cef-syslog.md#validate-cef-connectivity)

Pour en savoir plus sur la procédure à suivre avec les données que vous avez collectées dans Microsoft Sentinel, consultez les articles suivants :

- Découvrez le [mappage de champs CEF et CommonSecurityLog](cef-name-mapping.md).
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Prise en main de la [détection des menaces avec Microsoft Sentinel](./detect-threats-built-in.md).
