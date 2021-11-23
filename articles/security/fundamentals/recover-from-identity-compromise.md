---
title: Utilisez les ressources de sécurité Microsoft et Azure pour faciliter la récupération après une compromission de l’identité système | Microsoft Docs
description: Découvrez comment utiliser les ressources de sécurité Microsoft et Azure, telles que Microsoft 365 Defender, Microsoft Sentinel, Azure Active Directory et Microsoft Defender pour le cloud, ainsi que les recommandations de Microsoft pour sécuriser votre système contre les compromissions d’identités système semblables à l’attaque Nobelium (Solorigate) de décembre 2020.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2021
ms.author: bagol
ms.openlocfilehash: b858cb8d2830d305a90dfb8ecbda9fe2154e11f9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132323194"
---
# <a name="recovering-from-systemic-identity-compromise"></a>Récupération après une compromission de l’identité système

Cet article décrit les ressources Microsoft et les recommandations pour la récupération suite à une attaque par compromission de l’identité du système contre votre organisation, telle que l’attaque [Nobelium](https://aka.ms/solorigate) de décembre 2020.

Le contenu de cet article est basé sur les instructions fournies par l’équipe de détection et de réponse de Microsoft (DART), qui permet de répondre aux compromissions et d’aider les clients à devenir résistants aux menaces. Pour plus d’informations de l’équipe DART, consultez la série de blogs sur la [Sécurité de Microsoft](https://www.microsoft.com/security/blog/microsoft-detection-and-response-team-dart-blog-series/).

De nombreuses organisations ont migré vers une approche basée sur le Cloud pour renforcer la sécurité de leur gestion des identités et des accès. Toutefois, votre organisation peut également avoir des systèmes locaux en place et utiliser différentes méthodes d’architecture hybride. Cet article reconnaît que les attaques d’identités système affectent les systèmes Cloud, locaux et hybrides, et fournit des recommandations et des références pour tous ces environnements.

> [!IMPORTANT]
> Ces informations sont fournies telles quelles et constituent un guide généralisé. la meilleure détermination de l’application de ces instructions à votre environnement informatique et à vos locataires doit prendre en compte votre environnement et vos besoins uniques, que chaque client est dans la meilleure position à déterminer.
>

## <a name="about-systemic-identity-compromise"></a>À propos de la compromission de l’identité système

Une attaque par le biais d’une identité système compromettre une organisation se produit lorsqu’une personne malveillante parvient à se faire une brèche dans l’administration de l’infrastructure d’identité d’une organisation.

Si cela s’est produit dans votre organisation, vous êtes en concurrence avec la personne malveillante pour sécuriser votre environnement avant que des dommages supplémentaires ne soient effectués.

- **Les attaquants disposant d’un contrôle administratif de l’infrastructure d’identité d’un environnement** peuvent utiliser ce contrôle pour créer, modifier ou supprimer des identités et des autorisations d’identité dans cet environnement.

    Dans le cas d'une compromission sur site, si les certificats de signature de jeton SAML de confiance ne sont *pas* stockés dans un [HSM](../../key-vault/keys/hsm-protected-keys.md), l'attaque comprend l'accès à ce certificat de signature de jeton SAML de confiance.

- **Les attaquants peuvent ensuite utiliser le certificat pour falsifier les jetons SAML** pour emprunter l’identité des utilisateurs et comptes existants de l’organisation sans avoir besoin d’accéder aux informations d’identification du compte et sans laisser de traces.

- L’accès à un **compte à privilèges élevés** peut également être utilisé pour ajouter des informations d’identification contrôlées par des attaquants à des applications existantes, ce qui permet aux attaquants d’accéder à votre système non détecté, par exemple pour appeler des API, à l’aide de ces autorisations.

## <a name="responding-to-the-attack"></a>Réponse à l’attaque


La réponse aux violations d’identités système doit inclure les étapes indiquées dans l’image et la table suivantes :

:::image type="content" source="media/recover-from-identity-compromise/recover-identity-compromise.png" alt-text="Étapes pour récupérer après une compromission d’identité.":::


|Étape  |Description  |
|---------|---------|
|**Établir des communications sécurisées**     |  Une organisation ayant connu une compromission de l’identité système doit supposer que toutes les communications sont affectées. Avant d’effectuer une action de récupération, vous devez vous assurer que les membres de votre équipe qui sont essentiels à votre travail d’investigation et de réponse [peuvent communiquer en toute sécurité](#establish-secure-communications). <br><br>*La sécurisation des communications doit être la première étape pour que vous puissiez continuer sans la connaissance de l’attaquant.*|
|**Examinez votre environnement**   | Une fois que vous avez sécurisé les communications sur votre équipe d’investigation de base, vous pouvez commencer à rechercher les points d’accès initiaux et les techniques de persistance. [Identifiez vos indications de compromission](#identify-indications-of-compromise), puis recherchez les points d’accès et la persistance initiaux. Dans le même temps, commencez à [mettre en place des opérations de surveillance continue](#establish-continuous-monitoring) pendant vos efforts de récupération.        |
|**Planifier la posture de sécurité**     | [Activez les fonctionnalités et capacités](#improve-security-posture) de sécurité à la suite des recommandations recommandées pour améliorer la sécurité du système.  <br><br>Veillez à poursuivre vos efforts de [surveillance continue](#establish-continuous-monitoring) au fil du temps et de l'évolution du paysage de la sécurité.    |
|**Regagner/conserver le contrôle**     |  Vous devez récupérer le contrôle administratif de votre environnement à partir de l’attaquant. Une fois que vous avez le contrôle à nouveau et que vous avez actualisé l’état de sécurité de votre système, veillez à [corriger ou bloquer](#remediate-and-retain-administrative-control) toutes les techniques de persistance possibles et les nouvelles attaques d’accès initial.       |
|     |         |

## <a name="establish-secure-communications"></a>Établir des communications sécurisées

Avant de commencer à répondre, vous devez être sûr de pouvoir communiquer en toute sécurité sans que l’attaquant n’écoute. Veillez à isoler toutes les communications liées à l’incident afin que l’attaquant ne soit pas dirigé vers votre investigation et soit pris en charge à vos actions de réponse.

Par exemple :

1. Pour les communications initiales d’un groupe et d’un groupe, vous pouvez utiliser des appels RTPC, des ponts de conférence qui ne sont pas connectés à l’infrastructure d’entreprise et des solutions de messagerie chiffrée de bout en bout.

    Les communications en dehors de ces infrastructures doivent être considérées comme compromises et non approuvées, sauf si elles sont vérifiées via un canal sécurisé.

2. Après ces conversations initiales, vous souhaiterez peut-être créer un locataire entièrement nouveau Microsoft 365, isolé du locataire de production de l’organisation. Créez des comptes uniquement pour le personnel principal qui doit faire partie de la réponse.

Si vous créez un nouveau locataire Microsoft 365, veillez à suivre toutes les meilleures pratiques pour le locataire, et en particulier pour les comptes et les droits d’administration. Limitez les droits d’administration, sans approbations pour les applications externes ou les fournisseurs.

> [!IMPORTANT]
> Assurez-vous de ne pas communiquer avec votre nouveau locataire sur vos comptes de messagerie existants et potentiellement compromis. 

Pour plus d'informations, consultez [Meilleures pratiques pour la sécurisation de Microsoft 365](https://www.microsoft.com/security/blog/2019/01/10/best-practices-for-securely-using-microsoft-365-the-cis-microsoft-365-foundations-benchmark-now-available/).

## <a name="identify-indications-of-compromise"></a>Identifier les indications de compromission

Nous recommandons aux clients de suivre les mises à jour des fournisseurs système, y compris Microsoft et ses partenaires, et d’implémenter les nouvelles détections et protections fournies et d’identifier les incidents publiés de compromission (IOCs).

Recherchez les mises à jour dans les produits de sécurité Microsoft suivants et implémentez les modifications recommandées :

- [Microsoft Sentinel](../../sentinel/index.yml)
- [Solutions et services de sécurité Microsoft 365](/microsoft-365/security/)
- [Sécurité Windows 10 Entreprise](/windows/security/)
- [Microsoft Defender for Cloud Apps ](/cloud-app-security/)

L’implémentation de nouvelles mises à jour vous aidera à identifier les campagnes précédentes et à empêcher les futures campagnes sur votre système. Gardez à l’esprit que les listes de IOCs peuvent ne pas être exhaustives et peuvent se développer au fur et à mesure que des investigations se poursuivent.

Par conséquent, nous vous recommandons également d’effectuer les actions suivantes :

- Assurez-vous que vous avez appliqué la [documentation sur les points de référence en matière de sécurité d’Azure](/security/benchmark/azure/) et que vous contrôlez la conformité via [Microsoft Defender pour le cloud](../../security-center/index.yml).

- Incorporez des flux d’informations sur les menaces dans votre SIEM, par exemple en configurant Microsoft 365 les connecteurs de données dans [Microsoft Sentinel](../../sentinel/understand-threat-intelligence.md).

Pour plus d’informations, consultez la documentation relative à la sécurité de Microsoft :

- [Documentation sur la sécurité de Microsoft](/security/)
- [Documentation sur la sécurité Azure](../index.yml)

## <a name="investigate-your-environment"></a>Examinez votre environnement

Une fois que vos répondeurs d’incident et le personnel de la clé ont un emplacement sécurisé pour collaborer, vous pouvez commencer à examiner l’environnement compromis.

Vous devez vous équilibrer jusqu’au bas de chaque comportement anormal et prendre des mesures rapides pour arrêter toute activité supplémentaire de la part de l’attaquant. Toute mise à jour réussie requiert une compréhension de la méthode initiale des méthodes d’entrée et de persistance que l’attaquant a utilisées, comme c’est possible à la fois. Toutes les méthodes de persistance manquées pendant l’investigation peuvent entraîner un accès continu par l’attaquant et une éventuelle réévaluation.

À ce stade, vous souhaiterez peut-être effectuer une analyse des risques pour hiérarchiser vos actions. Pour plus d'informations, consultez les pages suivantes :

- [Menace, vulnérabilité et évaluation des risques dans le centre de centres](/compliance/assurance/assurance-threat-vulnerability-risk-assessment)
- [Suivre les menaces émergentes avec l’analyse des menaces et y répondre](/microsoft-365/security/defender-endpoint/threat-analytics)
- [Gestion des menaces et des vulnérabilités](/microsoft-365/security/defender-endpoint/next-gen-threat-and-vuln-mgt)

Les services de sécurité de Microsoft fournissent des ressources complètes pour effectuer des investigations détaillées. Les sections suivantes décrivent les principales actions recommandées.


> [!NOTE]
> Si vous constatez qu’une ou plusieurs des sources de journalisation listées ne font pas partie de votre programme de sécurité, nous vous recommandons de les configurer dès que possible pour activer les détections et les futures révisions des journaux.
>
> Veillez à configurer la rétention des journaux pour prendre en charge les objectifs d’investigation de votre organisation. Conserver les preuves nécessaires à des fins juridiques, réglementaires ou d’assurance.
>

### <a name="investigate-and-review-cloud-environment-logs"></a>Examiner les journaux de l’environnement cloud

Examinez les journaux de l’environnement cloud en vue de détecter les actions suspectes et les indications de compromission. Par exemple, vérifiez les journaux suivants :

- [Journaux d’audit unifiés (UAL)](/powershell/module/exchange/search-unifiedauditlog)
- [Journaux Azure Active Directory (Azure AD)](../../active-directory/reports-monitoring/overview-monitoring.md)
- [Journaux Microsoft Exchange locaux](/exchange/mail-flow/transport-logs/transport-logs)
- Journaux VPN, tels que[la passerelle VPN](../../vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)
- Journaux de systèmes d’ingénierie
- Journaux de détection de point de terminaison et antivirus

### <a name="review-endpoint-audit-logs"></a>Analyse des journaux d’audit du point de terminaison

Passez en revue les journaux d’audit des points de terminaison pour les modifications locales, telles que les types d’actions suivants :

- Appartenance au groupe modifiée
- Création d’un compte d'utilisateur
- Délégations avec Active Directory

En particulier, considérez l’une de ces modifications qui se produisent en même temps que d’autres signes de compromission ou d’activité.

### <a name="review-administrative-rights-in-your-environments"></a>Passer en revue les droits d’administration dans vos environnements

Passez en revue les droits d’administration dans vos environnements Cloud et locaux. Par exemple :

|Environnement  |Description  |
|---------|---------|
|**Tous les environnements cloud**    |       -Passer en revue tous les droits d’accès privilégiés dans le Cloud et supprimer les autorisations inutiles<br>    - Implémenter Privileged Identity Management (PIM)<br>    -Configurer des stratégies d’accès conditionnel pour limiter l’accès administratif lors de la sécurisation renforcée      |
|**Tous les environnements locaux**     |       -Vérifier l’accès privilégié en local et supprimer les autorisations inutiles<br>   -Réduire l’appartenance des groupes prédéfinis<br>    -Vérifier les délégations Active Directory<br>    -Renforcer votre environnement de niveau 0 et limiter l’accès aux ressources de niveau 0      |
|**Toutes les applications d’entreprise**     | Passez en revue les autorisations déléguées et les octrois de consentement qui autorisent l’une des actions suivantes : <br><br>  - Modification des utilisateurs et des rôles privilégiés <br>- Lecture ou accès à toutes les boîtes aux lettres <br>- Envoi ou transfert de courrier électronique pour le compte d’autres utilisateurs <br>- Accès à l’ensemble du contenu du site OneDrive ou SharePoint <br>- Ajout de principaux de service qui peuvent lire/écrire dans l’annuaire      |
|**Environments Microsoft 365**     |Passez en revue les paramètres d’accès et de configuration de votre environnement Microsoft 365, notamment : <br>- Partage SharePoint Online <br>- Microsoft Teams <br>- Power Apps <br>- Microsoft OneDrive Entreprise          |
| **Examiner les comptes d’utilisateur dans vos environnements**   |- Examinez et supprimez les comptes d’utilisateurs invités qui ne sont plus nécessaires. <br>- Passez en revue les configurations de messagerie pour les délégués, les autorisations de dossier de boîte aux lettres, les inscriptions d’appareils mobiles ActiveSync, les règles de boîte de réception et les Outlook sur les options Web. <br>- Passez en revue les droits ApplicationImpersonation et réduisez autant que possible toute utilisation de l’authentification héritée. <br>- Vérifiez que l’authentification multifacteur est appliquée et que les informations de contact de l’authentification MFA et de la réinitialisation du mot de passe en libre-service (SSPR) pour tous les utilisateurs sont correctes.         |
|     |         |

## <a name="establish-continuous-monitoring"></a>Établir une surveillance continue

La détection du comportement d’un attaquant comprend plusieurs méthodes, et dépend des outils de sécurité que votre organisation a mis à votre disposition pour répondre à l’attaque.

Par exemple, les services de sécurité Microsoft peuvent avoir des ressources et des conseils spécifiques qui sont pertinents pour l’attaque, comme décrit dans les sections ci-dessous.

> [!IMPORTANT]
> Si votre investigation trouve des preuves d’autorisations d’administration acquises par le biais de la compromission de votre système, qui ont donné accès au compte d’administrateur général de votre organisation et/ou au certificat de signature de jetons SAML approuvé, nous vous recommandons de prendre des mesures pour [corriger et conserver le contrôle administratif](#remediate-and-retain-administrative-control).
>

### <a name="monitoring-with-microsoft-sentinel"></a>Analyse avec Microsoft Sentinel

Microsoft Sentinel offre de nombreuses ressources intégrées pour vous aider dans votre investigation, telles que la chasse des classeurs et des règles d’analyse qui peuvent aider à détecter les attaques dans les domaines pertinents de votre environnement.

Pour plus d'informations, consultez les pages suivantes :

- [Visualiser et analyser votre environnement](../../sentinel/get-visibility.md)
- [Détection des menaces prête à l’emploi](../../sentinel/detect-threats-built-in.md).

### <a name="monitoring-with-microsoft-365-defender"></a>Surveillance avec Microsoft 365 Defender

Nous vous recommandons de vérifier Microsoft 365 Defender pour le point de terminaison et Antivirus Microsoft Defender pour obtenir des conseils spécifiques concernant votre attaque.

Recherchez d’autres exemples de détections, de recherches de la chasse et de rapports threat analytics dans le centre de sécurité Microsoft, comme dans Microsoft 365 Defender, Microsoft 365 Defender pour l’identité et Microsoft Defender for Cloud Apps. Pour garantir la couverture, veillez à installer l' [agent Microsoft Defender pour Identity](/defender-for-identity/install-step4) sur les serveurs ADFS en plus de tous les contrôleurs de domaine.

Pour plus d'informations, consultez les pages suivantes :

- [Suivre les menaces émergentes avec l’analyse des menaces et y répondre](/windows/security/threat-protection/microsoft-defender-atp/threat-analytics)
- [Comprendre le rapport d’analyste dans l’analyse des menaces](/microsoft-365/security/defender/threat-analytics-analyst-reports)

### <a name="monitoring-with-azure-active-directory"></a>Analyse avec Azure Active Directory

Les journaux d'activité Azure Active Directory peuvent indiquer si l’authentification multifacteur est utilisée correctement. Accédez aux journaux d’ouverture de session directement à partir de la zone Azure Active Directory du portail Azure, utilisez la cmdlet **Get-AzureADAuditSignInLogs** ou consultez-les dans la zone des **journaux** de Microsoft Sentinel.

Par exemple, recherchez ou filtrez les résultats lorsque le champ de résultats de **l'authentification multifacteur** contient la valeur **Exigence de l'authentification multifacteur satisfaite par une réclamation dans le jeton**. Si votre organisation utilise ADFS et que les revendications journalisées ne sont pas incluses dans la configuration ADFS, ces revendications peuvent indiquer une activité malveillante.

Recherchez ou filtrez vos résultats plus en détail pour exclure le bruit supplémentaire. Par exemple, vous souhaiterez peut-être inclure des résultats uniquement à partir de domaines fédérés. Si vous trouvez des connexions suspectes, explorez encore plus en détail en fonction des adresses IP, des comptes d’utilisateur, etc.

Le tableau suivant décrit d’autres méthodes d’utilisation des journaux Azure Active Directory dans votre investigation :

|Méthode  |Description  |
|---------|---------|
|**Analyser les événements de connexion risquée**     |  Azure Active Directory et sa plateforme Identity Protection peuvent générer des événements à risque associés à l’utilisation de jetons SAML générés par un attaquant. <br><br>Ces événements peuvent être qualifiés de *propriétés inconnues*, *d'adresse IP anonyme*, de *voyage impossible*, etc. <br><br>Nous vous recommandons d’analyser attentivement tous les événements à risque associés à des comptes disposant de privilèges d’administration, y compris ceux qui peuvent avoir été automatiquement ignorés ou corrigés. Par exemple, un événement à risque ou une adresse IP anonyme peut être corrigé automatiquement, car l’utilisateur a passé l’authentification multifacteur. <br><br>Veillez à utiliser [ADFS Connect Health](../../active-directory/hybrid/how-to-connect-health-adfs.md) pour que tous les événements d'authentification soient visibles dans Azure AD. |
|**Détecter les propriétés d’authentification du domaine**     |  Toute tentative d’une personne malveillante de manipuler des stratégies d’authentification de domaine sera enregistrée dans les journaux d’audit Azure Active Directory et reflétée dans le journal d’audit unifié. <br><br> Par exemple, examinez les événements associés à **Définition d’une authentification de domaine** dans le journal d’audit unifié, les journaux d’audit Azure AD et/ou votre environnement Siem pour vérifier que toutes les activités listées étaient attendues et planifiées.   |
|**Détecter les informations d’identification pour les applications OAuth**     |  Les attaquants qui ont gagné le contrôle d’un compte privilégié peuvent rechercher une application ayant la possibilité d’accéder à la messagerie de l’utilisateur dans l’organisation, puis d’ajouter des informations d’identification contrôlées par l’attaquant à cette application. <br><br>Par exemple, vous souhaiterez peut-être Rechercher les activités suivantes, qui sont cohérentes avec le comportement des attaquants : <br>- Ajout ou mise à jour des informations d’identification du principal du service <br>- Mise à jour des certificats et des secrets d’application <br>- Ajout d’une attribution de rôle d’application à un utilisateur <br>- Ajouter Oauth2PermissionGrant |
|**Détecter l’accès au courrier électronique par les applications**     |  Recherchez l’accès à la messagerie électronique par les applications dans votre environnement. Par exemple, utilisez les [caractéristiques d'audit avancé de Microsoft 365](/microsoft-365/compliance/mailitemsaccessed-forensics-investigations) pour enquêter sur les comptes compromis. |
|**Détecter les connexions non interactives aux principaux de service**     | Les rapports de connexion Azure Active Directory fournissent des détails sur les connexions non interactives qui utilisaient des informations d’identification de principal de service.  Par exemple, vous pouvez utiliser les rapports de connexion pour rechercher des données précieuses pour votre investigation, telles qu’une adresse IP utilisée par l’attaquant pour accéder aux applications de messagerie.        |
|     |         |


## <a name="improve-security-posture"></a>Améliorer la posture de sécurité

Si un événement de sécurité s’est produit dans vos systèmes, nous vous recommandons de réfléchir à votre stratégie de sécurité et à vos priorités actuelles.

Les répondeurs d’incidents sont souvent invités à fournir des recommandations sur les investissements que l’organisation doit donner en priorité, à présent qu’elle est face à de nouvelles menaces.

En plus des recommandations documentées dans cet article, nous vous recommandons de privilégier les zones de focus qui répondent aux techniques postérieures à l’exploitation utilisées par cette personne malveillante et les lacunes courantes en matière de sécurité qui les activent.

Les sections suivantes répertorient les recommandations pour améliorer la sécurité générale et la position de sécurité des identités.

### <a name="improve-general-security-posture"></a>Améliorer la sécurité générale

Nous vous recommandons d’effectuer les actions suivantes pour garantir votre position générale en matière de sécurité :

- **Consultez le [score Microsoft Secure](/microsoft-365/security/mtp/microsoft-secure-score) pour connaître** les recommandations de base sur la sécurité personnalisées pour les produits et services Microsoft que vous consommez.

- **Assurez-vous que votre organisation dispose de solutions EDR et SIEM**, telles que [Microsoft 365 Defender for Endpoint](/microsoft-365/security/defender/microsoft-365-defender) et [Microsoft Sentinel](../../sentinel/overview.md).

- **Analyser le [modèle de Microsoft](/security/compass/privileged-access-access-model) Enterprise access**.

### <a name="improve-identity-security-posture"></a>Améliorer la posture de sécurité des identités

Nous vous recommandons d’effectuer les actions suivantes pour garantir votre position générale en matière de sécurité :

- **Passez en revue les [cinq étapes de Microsoft en matière de sécurisation de votre infrastructure d’identité](steps-secure-identity.md)** et hiérarchisez les étapes appropriées pour votre architecture d’identité.

- **[Envisagez de migrer vers les paramètres de sécurité par défaut Azure AD](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md)** pour votre stratégie d’authentification.

- **Éliminez l’utilisation de l’authentification héritée par votre organisation**, si les systèmes ou les applications en ont encore besoin. Comment [bloquer l’authentification héritée sur Azure AD avec l’accès conditionnel](../../active-directory/conditional-access/block-legacy-authentication.md) pour en savoir plus.

    > [!NOTE]
    > L’équipe Exchange envisage de [désactiver l’authentification de base pour les protocoles EAS, EWS, POP, IMAP et RPS](https://developer.microsoft.com/en-us/office/blogs/deferred-end-of-support-date-for-basic-authentication-in-exchange-online/) au cours de la seconde moitié de 2021.
    >
    > Par souci de clarté, les paramètres de sécurité par défaut et les stratégies d’authentification sont distincts, mais fournissent des fonctionnalités complémentaires.
    >
    > Nous recommandons aux clients d’utiliser des stratégies d’authentification pour désactiver l’authentification de base pour un sous-ensemble de protocoles Exchange Online ou pour désactiver progressivement l’authentification de base dans une grande organisation.
    >

- **Traitez votre infrastructure ADFS et votre infrastructure AD Connect en tant que ressource de niveau 0**.

- **Limitez l’accès administrateur local au système**, y compris le compte utilisé pour exécuter le service ADFS.

    Le moindre privilège nécessaire pour le compte qui exécute l'ADFS est *l'attribution de droits d'utilisateur de connexion* en tant que service.

- **Limitez l’accès administratif à des utilisateurs limités et à des plages d’adresses IP limitées** à l’aide de stratégies de pare-feu Windows pour les Bureau à distance.

    Nous vous recommandons de configurer une zone de saut ou un système équivalent de niveau 0.

- **Bloquer tout accès SMB entrant** aux systèmes à partir de n’importe quel emplacement de l’environnement. Pour plus d’informations, consultez la rubrique [Au-delà de la périphérie : Comment sécuriser le trafic SMB dans Windows](https://techcommunity.microsoft.com/t5/itops-talk-blog/beyond-the-edge-how-to-secure-smb-traffic-in-windows/ba-p/1447159). Nous vous recommandons également de diffuser les journaux du pare-feu Windows vers SIEM pour une surveillance historique et proactive.

- Si vous utilisez un compte de service et que votre environnement le prend en charge, effectuez une **migration à partir d’un compte de service vers un compte de service géré par le groupe (gMSA)** . Si vous ne pouvez pas passer à un gMSA, faites pivoter le mot de passe du compte de service en un mot de passe complexe.

- .**Vérifiez que la journalisation détaillée est activée sur vos systèmes ADFS**. Par exemple, exécutez les commandes suivantes :

    ```powershell
    Set-AdfsProperties -AuditLevel verbose
    Restart-Service -Name adfssrv
    Auditpol.exe /set /subcategory:”Application Generated” /failure:enable /success:enable
    ```

## <a name="remediate-and-retain-administrative-control"></a>Corriger et conserver le contrôle administratif

Si votre investigation a identifié que la personne malveillante dispose d’un contrôle administratif dans le Cloud ou l’environnement local de l’organisation, vous devez récupérer le contrôle de façon à ce que la personne malveillante ne soit pas persistante.

Cette section fournit les méthodes possibles et les étapes à prendre en compte lors de la création de votre plan de récupération de contrôle d’administration.

> [!IMPORTANT]
> Les étapes exactes requises dans votre organisation dépendent de la persistance que vous avez découverte dans votre investigation et de la confiance que vous avez apportée à votre investigation, et ont découvert toutes les méthodes d’entrée et de persistance possibles.
>
> Assurez-vous que toutes les actions effectuées sont effectuées à partir d’un appareil de confiance, créé à partir d’une [source propre](/security/compass/privileged-access-access-model). Par exemple, utilisez une nouvelle [station de travail à accès privilégié](/security/compass/privileged-access-deployment).
>

Les sections suivantes présentent les types de recommandations suivants pour la correction et la conservation du contrôle administratif :

- Suppression de l’approbation sur vos serveurs actuels
- Faire pivoter votre certificat de signature de jetons SAML ou remplacer vos serveurs ADFS si nécessaire
- Activités de correction spécifiques pour les environnements Cloud ou locaux

### <a name="remove-trust-on-your-current-servers"></a>Suppression de l’approbation sur vos serveurs actuels

Si votre organisation a perdu le contrôle des certificats de signature de jetons ou de l’approbation fédérée, l’approche la plus fiable consiste à supprimer la confiance et à passer à l’identité de la maîtrise du Cloud lors de la correction locale.

La suppression de l’approbation et le passage à l’identité sous forme de Cloud nécessite une planification minutieuse et une compréhension approfondie des effets de l’isolation de l’identité. Pour plus d’informations, consultez [Protection de Microsoft 365 contre les attaques locales](../../active-directory/fundamentals/protect-m365-from-on-premises-attacks.md).

### <a name="rotate-your-saml-token-signing-certificate"></a>Faire pivoter votre certificat de signature de jetons SAML

Si votre organisation décide de *ne pas*[Supprimer l’approbation](#remove-trust-on-your-current-servers) lors de la récupération du contrôle d’administration en local, vous devrez faire pivoter votre certificat de signature de jetons SAML après avoir regagné le contrôle administratif localement et bloqué la capacité des attaquants à accéder de nouveau au certificat de signature.

Faire pivoter le certificat de signature de jetons une seule fois permet toujours au certificat de signature de jetons précédent de fonctionner. La poursuite de l’utilisation des certificats précédents pour le travail est une fonctionnalité intégrée pour les rotations de certificats normales, qui permet aux organisations de mettre à jour les approbations de partie de confiance avant l’expiration du certificat.

En cas d’attaque, vous ne voulez pas que l’attaquant conserve l’accès. Veillez à suivre les étapes ci-dessous pour vous assurer que l’attaquant n’a pas la possibilité de falsifier des jetons pour votre domaine.

> [!CAUTION]
> La dernière étape de cette procédure déconnecte les utilisateurs de leurs téléphones, les sessions webmail actuelles et tout autre élément qui utilise les jetons et les jetons d’actualisation associés.
>

> [!TIP]
> L’exécution de ces étapes dans votre environnement ADFS crée un certificat principal et un certificat secondaire, et promeut automatiquement le certificat secondaire au serveur principal après une période par défaut de 5 jours.
>
> Si vous avez des approbations de partie de confiance, cela peut avoir des effets 5 jours après la modification de l’environnement ADFS initial et doit être comptabilisé dans votre plan. Vous pouvez également résoudre ce dernier en remplaçant le certificat principal une troisième fois, en utilisant à nouveau l’indicateur **Urgent** et en supprimant le certificat secondaire ou en désactivant la rotation automatique des certificats.
>

**Pour faire pivoter entièrement le certificat de signature de jetons et empêcher la falsification de nouveaux jetons par une personne malveillante**

1. Vérifiez que votre paramètre **AutoCertificateRollover** a la valeur **True** :

    ``` powershell
    Get-AdfsProperties | FL AutoCert*, Certificate*
    ```
    Si **AutoCertificateRollover** n’est pas paramétré sur **True**, réglez la valeur ainsi :

    ``` powershell
    Set-ADFSProperties -AutoCertificateRollover $true
    ```

1. Se connecter au service en ligne Microsoft :

    ``` powershell
    Connect-MsolService
    ```

1. Exécutez la commande suivante et prenez note de vos dates d’expiration et de l’empreinte numérique du certificat de signature de jetons Cloud et local :

    ``` powershell
    Get-MsolFederationProperty -DomainName <domain>
    ```

    Par exemple :

    ```powershell
    ...
    [Not Before]
        12/9/2020 7:57:13 PM

    [Not After]
        12/9/2021 7:57:13 PM

    [Thumbprint]
        3UD1JG5MEFHSBW7HEPF6D98EI8AHNTY22XPQWJFK6
    ```

1. Remplacez le certificat de signature de jetons primaire en utilisant le commutateur **Urgent**. Cette commande permet à ADFS de remplacer immédiatement le certificat principal, sans en faire un certificat secondaire :

    ```powershell
    Update-AdfsCertificate -CertificateType Token-Signing -Urgent
    ```

1. Créez un certificat secondaire de signature de jeton, sans le commutateur **Urgent**. Cette commande autorise deux certificats de signature de jetons locaux avant la synchronisation avec Azure Cloud.

    ```powershell
    Update-AdfsCertificate -CertificateType Token-Signing
    ```

1. Mettez à jour l’environnement Cloud avec les certificats principaux et secondaires localement pour supprimer immédiatement le certificat de signature de jetons publié dans le Cloud.

    ```powershell
    Update-MsolFederatedDomain -DomainName <domain>
    ```

    > [!IMPORTANT]
    > Si cette étape n’est pas effectuée à l’aide de cette méthode, l’ancien certificat de signature de jetons peut toujours être en mesure d’authentifier les utilisateurs.

1. Pour vous assurer que ces étapes ont été effectuées correctement, vérifiez que le certificat affiché avant à l’étape 3 est maintenant supprimé :

    ```powershell
    Get-MsolFederationProperty -DomainName <domain>
    ```

1. Révoquez vos jetons d’actualisation via PowerShell pour empêcher l’accès avec les anciens jetons. 

    Pour plus d'informations, consultez les pages suivantes :

    - [Révoquer les accès utilisateur dans Azure Active Directory](../../active-directory/enterprise-users/users-revoke-access.md)
    - [Revoke-AzureADUserAllRefreshToken PowerShell docs](/powershell/module/azuread/revoke-azureaduserallrefreshtoken)



### <a name="replace-your-adfs-servers"></a>Remplacer vos serveurs ADFS

Si, au lieu de faire [pivoter votre certificat de signature de jetons SAML](#rotate-your-saml-token-signing-certificate), vous décidez de remplacer les serveurs ADFS par des systèmes propres, vous devez supprimer les services ADFS existants de votre environnement, puis en créer un nouveau. 

Pour plus d'informations, voir [Supprimer une configuration](../../active-directory/cloud-sync/how-to-configure.md#remove-a-configuration). 

### <a name="cloud-remediation-activities"></a>Activités de correction du Cloud

En plus des recommandations répertoriées plus haut dans cet article, nous vous recommandons également d’utiliser les activités suivantes pour vos environnements Cloud :

|Activité  |Description  |
|---------|---------|
|**Réinitialiser les mots de passe**     |   Réinitialisez les mots de passe sur tous les [comptes de coupe](../../active-directory/roles/security-emergency-access.md) et réduisez le nombre de comptes de secours au minimum requis.    |
|**Restreindre les comptes d'accès privilégiés**     |    Vérifiez que les comptes de service et d’utilisateur disposant d’un accès privilégié sont des comptes Cloud uniquement et n’utilisez pas de comptes locaux synchronisés ou fédérés pour Azure Active Directory.  |
|**Authentification multifacteur**     | Appliquez Multi-Factor Authentication (MFA) à tous les utilisateurs élevés du locataire. Nous vous recommandons d’appliquer MFA pour tous les utilisateurs du locataire.       |
|**Limiter l'accès administratif**     |    Mettre en œuvre la [gestion des identités privilégiées](../../active-directory/privileged-identity-management/pim-configure.md) (PIM) et l'accès conditionnel pour limiter l'accès administratif.  <br><br>Pour les utilisateurs Microsoft 365, implémentez [Privileged Access Management](https://techcommunity.microsoft.com/t5/microsoft-security-and/privileged-access-management-in-office-365-is-now-generally/ba-p/261751) (PAM) pour limiter l’accès aux capacités sensibles, telles que eDiscovery, l’administrateur général, l’administration des comptes, etc.    |
|**Examiner/réduire les autorisations déléguées et les octrois de consentement**     |  Examinez et réduisez toutes les Applications Enterprise les autorisations déléguées ou les [octrois de consentement](/graph/auth-limit-mailbox-access) qui autorisent les fonctionnalités suivantes : <br><br>- Modification des utilisateurs et des rôles privilégiés <br>- Lecture, envoi d’e-mails ou accès à toutes les boîtes aux lettres <br>- Accès au contenu OneDrive, Teams ou SharePoint <br>- Ajout de principaux de service qui peuvent lire/écrire dans l’annuaire <br>- Autorisations d’application et accès délégué       |
|     |         |

### <a name="on-premises-remediation-activities"></a>Activités de correction locales

En plus des recommandations énumérées plus haut dans cet article, nous recommandons également les activités suivantes pour vos environnements sur site :

|Activité  |Description  |
|---------|---------|
|**Reconstruire les systèmes affectés**     |   Reconstruisez les systèmes qui ont été identifiés comme compromis par l’attaquant pendant votre investigation.      |
|**Supprimer les utilisateurs administrateurs inutiles**     |   Supprimez les membres inutiles des administrateurs de domaine, des opérateurs de sauvegarde et des groupes d’administrateurs de Enterprise. Pour plus d’informations, consultez [Sécurisation de l’accès privilégié](/security/compass/overview). |
|**Réinitialiser les mots de passe des comptes privilégiés**     |  Réinitialiser les mots de passe de tous les comptes privilégiés dans l’environnement. <br><br>**Remarque**: les comptes privilégiés ne sont pas limités à des groupes prédéfinis, mais peuvent également être des groupes qui disposent d’un accès délégué à l’administration de serveur, à l’administration de station de travail ou à d’autres zones de votre environnement.      |
|**Réinitialisation du compte krbtgt**     | Réinitialisez le compte **krbtgt** deux fois en utilisant le script [New-KrbtgtKeys](https://github.com/microsoft/New-KrbtgtKeys.ps1/blob/master/New-KrbtgtKeys.ps1). <br><br>**Remarque**: Si vous utilisez des contrôleurs de domaine Read-Only, vous devez exécuter le script séparément pour les contrôleurs de domaine Read-Write et pour les contrôleurs de domaine Read-Only.        |
|**Planifier un redémarrage du système**     |   Après avoir validé qu’aucun mécanisme de persistance créé par l’attaquant n’existe ou n’est resté sur votre système, planifiez un redémarrage du système pour vous aider à supprimer les logiciels malveillants résidant en mémoire. |
|**Réinitialiser le mot de passe DSRM**     |  Réinitialisez le mot de passe DSRM (mode de restauration des services d’annuaire) du contrôleur de domaine sur une valeur unique et complexe.       |
|     |         |

### <a name="remediate-or-block-persistence-discovered-during-investigation"></a>Corriger ou bloquer la persistance détectée pendant l’investigation

L’investigation est un processus itératif, et vous devez trouver un équilibre entre le souhait de mettre à jour l’organisation et la correction à mesure que vous identifiez les anomalies et le risque que la mise à jour informe la personne malveillante de votre Détection et lui donne le temps de réagir.

Par exemple, une personne malveillante qui prend connaissance de la détection peut changer les techniques ou créer plus de persistance.

Veillez à corriger toutes les techniques de persistance que vous avez identifiées lors des étapes précédentes de l’investigation.

### <a name="remediate-user-and-service-account-access"></a>Résoudre l’accès au compte d’utilisateur et de service

En plus des actions recommandées mentionnées ci-dessus, nous vous recommandons de prendre en compte les étapes suivantes pour corriger et restaurer les comptes d’utilisateur :

- **Appliquer l’accès conditionnel en fonction des appareils de confiance**. Si possible, nous vous recommandons d'appliquer un *accès conditionnel basé sur l'emplacement* pour répondre à vos besoins organisationnels.

- **Réinitialisez les mots de passe** après éviction pour tous les comptes d’utilisateur qui ont peut-être été compromis. Veillez également à mettre en œuvre un plan à moyen terme pour réinitialiser les informations d'identification de tous les comptes de votre répertoire.

- **Révoquez les jetons d’actualisation** immédiatement après avoir pivoté vos informations d’identification.

    Pour plus d'informations, consultez les pages suivantes :

    - [Révoquer les accès utilisateur lors d’une urgence dans Azure Active Directory](../../active-directory/enterprise-users/users-revoke-access.md)
    - [Revoke-AzureADUserAllRefreshToken PowerShell documentation](/powershell/module/azuread/revoke-azureaduserallrefreshtoken)

## <a name="next-steps"></a>Étapes suivantes

- **Obtenez de l’aide à l’intérieur des produits Microsoft** , y compris le portail Microsoft 365 Defender, le centre de conformité Microsoft 365 et le centre de sécurité et de conformité Microsoft 365 et le centre de sécurité Microsoft Defender en sélectionnant le bouton **Aide** ( **?** ) dans la barre de navigation supérieure.

- **Pour une assistance au déploiement**, contactez-nous à [FastTrack](https://fasttrack.microsoft.com)

- **Si vous avez des besoins en matière de support technique**, prenez le [cas du support Microsoft](https://support.microsoft.com/contactus).

    > [!IMPORTANT]
    > Si vous pensez que vous avez été compromis et que vous avez besoin d’aide par le biais d’une réponse à un incident, ouvrez une **gravité un cas de** support Microsoft.
    >
