---
title: Enquêter sur les incidents à l'aide des données UEBA | Microsoft Docs
description: Apprenez à utiliser les données UEBA lorsque vous enquêtez sur les incidents afin d'obtenir du contexte sur les activités potentiellement malveillantes qui se produisent dans votre organisation.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5cf711abf0373c2c7681d71426c0590b9561708e
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522875"
---
# <a name="tutorial-investigate-incidents-with-ueba-data"></a>Tutoriel : Enquêter sur les incidents à l’aide des données UEBA

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Cet article décrit des méthodes courantes d'[analyse du comportement des utilisateurs et des entités (UEBA)](identify-threats-with-entity-behavior-analytics.md) dans vos workflows d'enquête habituels, et fournit des exemples de procédures d'utilisation.

> [!IMPORTANT]
>
> Les fonctionnalités mentionnées dans cet article sont actuellement disponibles en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.
>

> [!NOTE]
> Ce tutoriel fournit des procédures basées sur des scénarios pour une tâche client de premier plan : enquêter à l’aide de données UEBA. Pour plus d’informations, consultez [Examiner les incidents avec Microsoft Sentinel](investigate-cases.md).
>
## <a name="prerequisites"></a>Prérequis

Avant de pouvoir utiliser des données UEBA dans vos enquêtes, vous devez [activer l’analyse du comportement des utilisateurs et des entités (UEBA) dans Microsoft Sentinel](enable-entity-behavior-analytics.md).

Commencez à rechercher les insights basés sur la machine environ une semaine après l'activation de l'analyse UEBA.

## <a name="run-proactive-routine-searches-in-entity-data"></a>Effectuer des recherches proactives de routine dans les données d'entité

Nous vous recommandons d'effectuer des recherches régulières et proactives sur l'activité des utilisateurs afin d'obtenir des pistes pour approfondir l'enquête.

Vous pouvez utiliser le [classeur Analyse du comportement des utilisateurs et des entités](identify-threats-with-entity-behavior-analytics.md#hunting-queries-and-exploration-queries) de Microsoft Sentinel pour interroger vos données, par exemple :

- **Utilisateurs les plus à risque**, avec anomalies ou incidents associés
- **Données sur des utilisateurs spécifiques**, pour déterminer si le sujet a effectivement été compromis ou s'il existe une menace interne due à une action s'écartant du profil de l'utilisateur

Vous pouvez également capturer des actions non routinières dans le classeur UEBA, et les utiliser pour détecter des activités anormales et des pratiques potentiellement non conformes.

### <a name="investigate-an-anomalous-sign-in"></a>Enquêter sur une connexion anormale

Par exemple, les étapes ci-dessous suivent une enquête menée sur un utilisateur qui s'est connecté à un VPN qu'il n'avait encore jamais utilisé, ce qui constitue une activité anormale.

1. Dans la zone **Classeurs** d'Azure Sentinel, recherchez et ouvrez le classeur **Analyse du comportement des utilisateurs et des entités**.
1. Recherchez le nom de l'utilisateur sur lequel vous souhaitez enquêter et sélectionnez-le dans le tableau **Principaux utilisateurs à examiner**.
1. Faites défiler les tableaux **Répartition des incidents** et **Répartition des anomalies** pour consulter les incidents et anomalies associés à l'utilisateur sélectionné.
1. Dans l'anomalie, par exemple celle qui est nommée **Ouverture de session réussie anormale**, examinez les détails présentés dans le tableau afin d'enquêter. Par exemple :

    |Étape  |Description  |
    |---------|---------|
    |**Consultez la description à droite**     |    Chaque anomalie est accompagnée d'une description, avec un lien pour en savoir plus dans la [base de connaissances MITRE ATT&CK](https://attack.mitre.org/). <br>Par exemple : <br><br>        **_Accès initial_* _ <br>_L'adversaire essaie de pénétrer dans votre réseau.* <br>* L'accès initial englobe des techniques qui utilisent différents vecteurs d'entrée pour s'implanter dans un réseau. Parmi ces techniques utilisées figurent le harponnage ciblé et l'exploitation des faiblesses des serveurs web accessibles au public. L'implantation acquise grâce à l'accès initial peut permettre un accès continu, notamment par le biais de comptes valides et de services distants externes, ou il peut s'agir d'un usage limité en raison de possibles changements de mots de passe.*     |
    |**Notez le texte figurant dans la colonne Description**     |   Faites défiler la ligne de l'anomalie vers la droite pour afficher une description supplémentaire. Sélectionnez le lien pour afficher le texte intégral. Par exemple : <br><br> *Les adversaires peuvent voler les informations d'identification d'un utilisateur ou d'un compte de service spécifique à l'aide de techniques d'accès aux informations d'identification, ou capturer les informations d'identification plus tôt dans leur processus de reconnaissance via une méthode d'ingénierie sociale pour obtenir un accès initial. APT33, par exemple, a utilisé des comptes valides pour l'accès initial. La requête ci-dessous génère une sortie de connexion réussie initiée par un utilisateur à partir d'un nouvel emplacement géographique depuis lequel lui et ses homologues ne s'étaient encore jamais connectés.*     |
    |**Notez les données UsersInsights**     |  Faites défiler la ligne de l'anomalie vers la droite pour afficher les données utilisateur, comme le nom d'affichage et l'ID d'objet du compte. Sélectionnez le texte pour afficher les données complètes à droite.         |
    |**Notez les données de preuve**     |  Faites défiler la ligne de l'anomalie vers la droite pour afficher les données de preuve de l'anomalie. Sélectionnez le texte pour afficher les données complètes sur la droite, comme les champs suivants : <br><br>-  **ActionUncommonlyPerformedByUser** <br>- **UncommonHighVolumeOfActions** <br>- **FirstTimeUserConnectedFromCountry** <br>- **CountryUncommonlyConnectedFromAmongPeers** <br>- **FirstTimeUserConnectedViaISP** <br>- **ISPUncommonlyUsedAmongPeers** <br>- **CountryUncommonlyConnectedFromInTenant** <br>- **ISPUncommonlyUsedInTenant** |
    |     |         |

Utilisez les données trouvées dans le classeur **Analyse du comportement des utilisateurs et des entités** pour déterminer si l'activité de l'utilisateur est suspecte et nécessite une action supplémentaire.

## <a name="use-ueba-data-to-analyze-false-positives"></a>Utiliser les données UEBA pour analyser les faux positifs

Parfois, un incident capturé dans le cadre d'une enquête est un faux positif.

La détection d'une activité liée à un voyage impossible est un exemple courant de faux positif (ex : utilisateur qui s'est connecté à une application ou à un portail depuis New York et Londres au cours de la même heure). Alors que Microsoft Sentinel signale le voyage impossible comme une anomalie, une enquête auprès de l’utilisateur peut montrer qu’un VPN a été utilisé avec un emplacement autre que celui où se trouvait réellement l’utilisateur.

### <a name="analyze-a-false-positive"></a>Analyser un faux positif

Par exemple, pour un incident **Voyage impossible**, après avoir vérifié que l'utilisateur a utilisé un VPN, passez de l'incident à la page de l'entité utilisateur. Utilisez les données affichées sur cette page pour déterminer si les emplacements capturés sont inclus dans les emplacements habituels de l'utilisateur.

Par exemple :

[ ![Ouvrez la page de l'entité utilisateur associée à un incident. ](media/ueba/open-entity-pages.png)](media/ueba/open-entity-pages.png#lightbox)

La page de l'entité utilisateur est également accessible via un lien figurant sur la [page de l'incident](investigate-cases.md#how-to-investigate-incidents) lui-même et le [graphique d'enquête](investigate-cases.md#use-the-investigation-graph-to-deep-dive).

> [!TIP]
> Après avoir vérifié les données sur la page de l’entité utilisateur pour l’utilisateur associé à l’incident, accédez à la zone **Chasse** de Microsoft Sentinel pour savoir si les homologues de l’utilisateur ont aussi l’habitude de se connecter à partir des mêmes emplacements. Si tel est le cas, cela renforce l'hypothèse d'un faux positif.
>
> Dans la zone **Chasse**, exécutez la requête **Ouverture de session depuis un emplacement géographique anormal**. Pour plus d’informations, consultez [Repérer les menaces avec Microsoft Sentinel](hunting.md).
>

### <a name="embed-identityinfo-data-in-your-analytics-rules-public-preview"></a>Incorporer des données IdentityInfo dans vos règles d’analyse (préversion publique)

Comme les attaquants utilisent souvent les propres comptes d’utilisateur et de service de l’organisation, les données relatives à ces comptes d’utilisateur, notamment les informations d’identification et les privilèges de l’utilisateur, sont essentielles pour les analystes dans le processus d’investigation.

Incorporez les données de la **table IdentityInfo** pour affiner vos règles d’analyse en fonction de vos cas d’usage, en réduisant les faux positifs et en accélérant éventuellement votre processus d’investigation.

Par exemple :

- Pour mettre en corrélation les événements de sécurité et la table **IdentityInfo** dans une alerte qui se déclenche si une personne extérieure au service **informatique** accède à un serveur :

    ```kusto
    SecurityEvent
    | where EventID in ("4624","4672")
    | where Computer == "My.High.Value.Asset"
    | join kind=inner  (
        IdentityInfo
        | summarize arg_max(TimeGenerated, *) by AccountObjectId) on $left.SubjectUserSid == $right.AccountSID
    | where Department != "IT"
    ```

- Pour mettre en corrélation les journaux de connexion Azure AD et la table **IdentityInfo** dans une alerte qui se déclenche si une personne qui n’est pas membre d’un groupe de sécurité spécifique accède à une application :

    ```kusto
    SigninLogs
    | where AppDisplayName == "GithHub.Com"
    | join kind=inner  (
        IdentityInfo
        | summarize arg_max(TimeGenerated, *) by AccountObjectId) on $left.UserId == $right.AccountObjectId
    | where GroupMembership !contains "Developers"
    ```

La table **IdentityInfo** se synchronise avec votre espace de travail Azure AD pour créer un instantané de vos données de profil utilisateur, telles que les métadonnées utilisateur, les informations de groupe et les rôles Azure AD attribués à chaque utilisateur. Pour plus d’informations, consultez [Table IdentityInfo](ueba-enrichments.md#identityinfo-table-public-preview) dans la documentation de référence sur les enrichissements UEBA.

## <a name="identify-password-spray-and-spear-phishing-attempts"></a>Identifier les tentatives de pulvérisation de mots de passe et de harponnage

Si l'authentification multifacteur (MFA) n'est pas activée, les informations d'identification des utilisateurs sont vulnérables aux attaquants qui cherchent à compromettre les attaques par [pulvérisation de mots de passe](https://www.microsoft.com/security/blog/2020/04/23/protecting-organization-password-spray-attacks/) ou [harponnage](https://www.microsoft.com/security/blog/2019/12/02/spear-phishing-campaigns-sharper-than-you-think/).

### <a name="investigate-a-password-spray-incident-with-ueba-insights"></a>Enquêter sur un incident de pulvérisation de mots de passe à l'aide des insights UEBA

Par exemple, pour enquêter sur un incident de type pulvérisation de mots de passe avec des insights UEBA, vous pouvez effectuer les opérations suivantes pour en savoir plus :

1. Dans l'incident, en bas à gauche, sélectionnez **Enquêter** pour afficher les comptes, les machines et les autres points de données potentiellement ciblés par une attaque.

    En parcourant les données, vous pouvez repérer un compte administrateur présentant un nombre relativement élevé d'échecs d'ouvertures de session. Bien que cela soit suspect, vous ne souhaiterez peut-être pas restreindre le compte sans autre confirmation.

1. Sélectionnez l'entité utilisateur administratif dans la carte, puis sélectionnez **Insights** sur la droite pour afficher plus de détails, comme le graphique des connexions au fil du temps.

1. Sélectionnez **Informations** sur la droite, puis sélectionnez **Afficher tous les détails** pour accéder à la [page de l'entité utilisateur](identify-threats-with-entity-behavior-analytics.md#entity-pages) afin de poursuivre l'exploration. 

    Par exemple, notez s'il s'agit du premier incident potentiel de pulvérisation de mots de passe de l'utilisateur, ou consultez l'historique des connexions de l'utilisateur pour déterminer si les échecs sont anormaux.

> [!TIP]
> Vous pouvez également exécuter la **requête de chasse** [Échecs d'ouverture de session anormaux](hunting.md) pour surveiller tous les échecs de connexion anormaux d'une organisation. Utilisez les résultats de la requête pour commencer à enquêter sur d'éventuelles attaques par pulvérisation de mots de passe.
>

## <a name="url-detonation-public-preview"></a>Détonation d’URL (préversion publique)

Lorsqu’il existe des URL dans les journaux qui sont ingérées dans Microsoft Sentinel, ces URL sont automatiquement détonées afin d’accélérer le processus de triage. 

Le graphique d’investigation comprend un nœud pour l’URL détonée, ainsi que les détails suivants :

- **DetonationVerdict**. Détermination booléenne de haut niveau de la détonation. Par exemple, **Mauvais** signifie qu’il a été déterminé que le côté hébergeait un programme malveillant ou un contenu de hameçonnage.
- **DetonationFinalURL**. URL finale de la page d’arrivée observée, après toutes les redirections à partir de l’URL d’origine.
- **DetonationScreenshot**. Capture d’écran de l’apparence de la page au moment où l’alerte a été déclenchée. Sélectionnez la capture d’écran pour l’agrandir.

Par exemple :

:::image type="content" source="media/investigate-with-ueba/url-detonation-example.png" alt-text="Exemple de détonation d’URL affiché dans le graphique d’investigation.":::

> [!TIP]
> Si vous ne voyez pas d’URL dans vos journaux, vérifiez que la journalisation des URL, également appelée journalisation des menaces, est activée pour vos passerelles web sécurisées, vos proxys web, vos pare-feu ou vos IDS/IPS hérités.
>
> Vous pouvez également créer des journaux personnalisés pour acheminer des URL spécifiques qui vous intéressent vers Microsoft Sentinel pour approfondir votre investigation.
>

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l'analyse UEBA, les enquêtes et la chasse :

- [Identifier les menaces avancées avec l’analyse du comportement des utilisateurs et des entités (User and Entity Behavior Analytics, UEBA) dans Microsoft Sentinel](identify-threats-with-entity-behavior-analytics.md)
- [Informations de référence sur les enrichissements UEBA Microsoft Sentinel](ueba-enrichments.md)
- [Tutoriel : Examiner les incidents avec Microsoft Sentinel](investigate-cases.md)
- [Recherche de menaces avec Microsoft Sentinel](hunting.md)
