---
title: Détection avancée des attaques multiphases dans Microsoft Sentinel
description: Utilisez la technologie Fusion de Microsoft Sentinel pour réduire l’épuisement des alertes et créer des incidents actionnables basés sur la détection avancée des attaques multiphases.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8fa4fea5502f2e034aa6b5ba3f672cf8012dcd5a
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520044"
---
# <a name="advanced-multistage-attack-detection-in-microsoft-sentinel"></a>Détection avancée des attaques multiphases dans Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
> Certaines détections de Fusion (voir celles indiquées ci-dessous) sont actuellement en **préversion**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Microsoft Sentinel utilise Fusion, un moteur de corrélation basé sur des algorithmes évolutifs de Machine Learning, pour détecter automatiquement les attaques multiphases (également connues sous le nom de menaces persistantes avancées ou APT) en identifiant des combinaisons de comportements anormaux et d’activités suspectes observés à différentes étapes de la chaîne de destruction. Sur la base de ces découvertes, Microsoft Sentinel génère des incidents qui seraient autrement difficiles à intercepter. Ces incidents comprennent au moins deux alertes ou activités. Le système est conçu de façon à ce que ces incidents soient peu volumineux, soient détectés avec une haute fidélité, et présentent un niveau de gravité élevé.

Personnalisée pour votre environnement, cette technologie de détection, en plus de réduire la fréquence de [faux positifs](false-positives.md), peut détecter des attaques même si les informations sont limitées ou si certaines informations sont manquantes.

Comme Fusion met en corrélation de multiple signaux provenant de divers produits pour détecter les attaques multiphases avancées, les détections de Fusion réussies sont présentées en tant qu’**Incidents de Fusion** sur la page **Incidents** de Microsoft Sentinel, et non en tant qu’**Alertes**, et sont stockées dans la table *Incidents* dans **Journaux**, et non dans la table *Alertes de sécurité*.

### <a name="configure-fusion"></a>Configurer Fusion

Fusion est activé par défaut dans Microsoft Sentinel, en tant que [règle d’analyse](detect-threats-built-in.md#view-built-in-detections) appelée **Détection d’attaques multiphases avancées**. Vous pouvez afficher et modifier l’état de la règle, configurer des signaux source à inclure dans le modèle de ML de Fusion ou exclure des modèles de détection spécifiques qui peuvent ne pas être applicables à votre environnement à partir de la détection de Fusion. Découvrir comment [configurer la règle Fusion](configure-fusion-rules.md).

> [!NOTE]
> Microsoft Sentinel utilise actuellement 30 jours de données historiques pour former les algorithmes de Machine Learning du moteur Fusion. Ces données sont toujours chiffrées à l’aide des clés de Microsoft à mesure qu’elles passent par le pipeline de Machine Learning. Cependant, les données de formation ne sont pas chiffrées à l’aide de [clés gérées par le client (CMK)](customer-managed-keys.md) si vous avez activé CMK dans votre espace de travail Microsoft Sentinel. Pour désactiver Fusion, accédez à **Microsoft Sentinel** \> **Configuration** \> **Analyse \> Règles actives**, faites un clic droit sur la règle **Détection des attaques multiphases avancées** et sélectionnez **Désactiver**.

## <a name="fusion-for-emerging-threats"></a>Fusion pour les menaces émergentes

> [!IMPORTANT]
>
> - La détection basée sur Fusion pour les menaces émergentes est actuellement en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

Le volume des événements de sécurité continue de croître, et l’étendue et la sophistication des attaques augmentent encore. Nous pouvons définir les scénarios d’attaque connues, mais qu’en-est-il des menaces émergentes et inconnues dans votre environnement ?  

Le moteur Fusion basé sur le ML de Microsoft Sentinel peut vous aider à identifier les **menaces émergentes et inconnues** dans votre environnement en appliquant une **analyse de ML étendue** et en mettant en corrélation **un large éventail de signaux anormaux**, tout en réduisant le niveau de fatigue des alertes.

Les algorithmes de ML du moteur Fusion se forment constamment contre les attaques existantes et appliquent une analyse en fonction de la façon de penser des analystes de sécurité. Il peut donc découvrir des menaces non détectées auparavant à partir de millions de comportements anormaux sur l’ensemble de la chaîne de destruction de votre environnement, ce qui vous permet d’avoir une longueur d’avance sur les attaquants.

**Fusion pour les menaces émergentes** prend en charge la collecte et l’analyse des données à partir des sources suivantes :

- [Détections d’anomalies prêtes à l’emploi](soc-ml-anomalies.md)
- Alertes des produits Microsoft :
  - Azure Active Directory Identity Protection
  - Microsoft Defender pour le cloud
  - Microsoft Defender pour IoT
  - Microsoft 365 Defender
  - Microsoft Defender for Cloud Apps
  - Microsoft Defender for Endpoint
  - Microsoft Defender pour Identity
  - Microsoft Defender pour Office 365
- [**Alertes des règles d’analyse planifiée**](configure-fusion-rules.md#configure-scheduled-analytics-rules-for-fusion-detections), celles [intégrées](detect-threats-built-in.md#scheduled) et celles [créées par vos analystes de sécurité](detect-threats-custom.md). Les règles d’analyse doivent contenir une chaîne de destructions (tactiques) et des informations de mappage d’entité pour pouvoir être utilisées par Fusion.

Vous n’avez pas besoin de connecter *toutes* les sources de données mentionnées ci-dessus pour que Fusion fonctionne sur les menaces émergentes. Toutefois, plus le nombre de sources de données que vous avez connectées est important, plus la couverture est large et Fusion trouvera alors plus de menaces.

Lorsque les corrélations du moteur Fusion entraînent la détection d’une menace émergente, un incident de gravité élevée intitulé « **Activités d’attaques multiphases potentielles détectées par Fusion** » est généré dans la table *incidents* de votre espace de travail Microsoft Sentinel.

## <a name="fusion-for-ransomware"></a>Fusion pour ransomware

Le moteur Fusion de Microsoft Sentinel génère un incident lorsqu’il détecte plusieurs alertes de types différents à partir des sources de données suivantes et détermine qu’elles peuvent être associées à une activité de ransomware :

- [Microsoft Defender pour le cloud](connect-azure-security-center.md)
- [Microsoft Defender for Endpoint](./data-connectors-reference.md#microsoft-defender-for-endpoint)
- [Microsoft Defender pour Identity](./data-connectors-reference.md#microsoft-defender-for-identity)
- [Microsoft Defender for Cloud Apps](./data-connectors-reference.md#microsoft-defender-for-cloud-apps)
- [Règles d’analyse planifiée de Microsoft Sentinel](detect-threats-built-in.md#scheduled). Fusion prend uniquement en compte les règles analytiques planifiées avec des informations de tactique et des entités mappées.

De tels incidents Fusion sont nommés **Alertes multiples possiblement liées à l’activité Ransomware détectée** et sont générés lorsque des alertes connexes sont détectées pendant un délai d’exécution spécifique et sont associées aux étapes **Exécution** et **Évasion défense** sur une attaque.

Par exemple, Microsoft Sentinel génère un incident pour les éventuelles activités de Ransomware si les alertes suivantes sont déclenchées sur le même hôte dans un délai d’exécution spécifique :

| Alerte | Source | severity |
| ----- | ------ | -------- |
| **Événements d'erreur et d'avertissement Windows** | Règles d’analyse planifiée de Microsoft Sentinel | information |
| **Le ransomware « GandCrab » a été évité** | Microsoft Defender pour le cloud | moyenne |
| **Le programme malveillant « Emotet » a été détecté** | Microsoft Defender for Endpoint | information |
| **La Porte dérobée « Tofsee » a été détectée** | Microsoft Defender pour le cloud | low |
| **Le programme malveillant « Parite » a été détecté** | Microsoft Defender for Endpoint | information |

## <a name="scenario-based-fusion-detections"></a>Détections de Fusion basées sur un scénario

La section suivante répertorie les types d'[attaques multiphases basées sur des scénarios](fusion-scenario-reference.md), regroupées par classification des menaces, que Microsoft Sentinel détecte à l’aide du moteur de corrélation Fusion.

Pour activer ces scénarios de détection d’attaques par Fusion, les sources de données associées doivent être ingérées dans votre espace de travail Log Analytics. Sélectionnez les liens dans le tableau ci-dessous pour en savoir plus sur chaque scénario et ses sources de données associées.

> [!NOTE]
> Certains de ces scénarios sont en **PRÉVERSION**. Cela est spécifié.

| Classification des menaces  | Scénarios  |
| -------- | -------- |
| **Abus de ressource de calcul**      | <ul><li>(PRÉVERSION) [Plusieurs activités de création de machine virtuelle *suite à une* connexion suspecte à Azure Active Directory](fusion-scenario-reference.md#multiple-vm-creation-activities-following-suspicious-azure-active-directory-sign-in) |
| **Accès aux informations d’identification**           | <ul><li>(PRÉVERSION) [Plusieurs mots de passe réinitialisés par l’utilisateur *après une* connexion suspecte](fusion-scenario-reference.md#multiple-passwords-reset-by-user-following-suspicious-sign-in) <li>(PRÉVERSION) [Connexion suspecte *coïncidant avec la* réussite d’une connexion à Palo Alto VPN <br>par adresse IP avec plusieurs connexions à Azure AD ayant échoué](fusion-scenario-reference.md#suspicious-sign-in-coinciding-with-successful-sign-in-to-palo-alto-vpn-by-ip-with-multiple-failed-azure-ad-sign-ins) |
| **Collecte des informations d’identification**       | <ul><li>[Exécution de l’outil contre le vol d’informations d’identification *suite à une* connexion suspecte](fusion-scenario-reference.md#malicious-credential-theft-tool-execution-following-suspicious-sign-in)    <li>[Activité suspecte de vol d’informations d’identification *suite à une* connexion suspecte](fusion-scenario-reference.md#suspected-credential-theft-activity-following-suspicious-sign-in)      |
| **Minage de cryptomonnaie**               | <ul><li>[Activité de minage de cryptomonnaie *suite à une* connexion suspecte](fusion-scenario-reference.md#crypto-mining-activity-following-suspicious-sign-in)          |
| **Destruction de données**            | <ul><li>[Suppression de fichiers en masse *suite à une* connexion suspecte à Azure AD](fusion-scenario-reference.md#mass-file-deletion-following-suspicious-azure-ad-sign-in)     <li>(PRÉVERSION) [Suppression de fichiers en masse *suite à une* connexion réussie à Azure AD à partir d’une <br>adresse IP bloquée par un pare-feu Cisco](fusion-scenario-reference.md#mass-file-deletion-following-successful-azure-ad-sign-in-from-ip-blocked-by-a-cisco-firewall-appliance)        <li>(PRÉVERSION) [Suppression de fichiers en masse *suite à une* connexion réussie à Palo Alto VPN par une <br>adresse IP avec plusieurs connexions à Azure AD ayant échoué](fusion-scenario-reference.md#mass-file-deletion-following-successful-sign-in-to-palo-alto-vpn-by-ip-with-multiple-failed-azure-ad-sign-ins)        <li>(PRÉVERSION) [Activité suspecte de suppression d’e-mails *suite à une* connexion suspecte à Azure AD](fusion-scenario-reference.md#suspicious-email-deletion-activity-following-suspicious-azure-ad-sign-in) |
| **Exfiltration de données**           | <ul><li>(PRÉVERSION) [Activités de transfert de courriers *suite à une* nouvelle activité de compte d’administrateur qui n’a pas été vue récemment](fusion-scenario-reference.md#mail-forwarding-activities-following-new-admin-account-activity-not-seen-recently)      <li>[Téléchargement de fichiers en masse *suite à une* connexion suspecte à Azure AD](fusion-scenario-reference.md#mass-file-download-following-suspicious-azure-ad-sign-in)       <li>(PRÉVERSION) [Téléchargement de fichiers en masse *suite à une* connexion réussie à Azure AD à partir d’une <br>adresse IP bloquée par un pare-feu Cisco](fusion-scenario-reference.md#mass-file-download-following-successful-azure-ad-sign-in-from-ip-blocked-by-a-cisco-firewall-appliance)       <li>(PRÉVERSION) [Téléchargement de fichiers en masse *coïncidant avec* l’opération de fichier SharePoint à partir d’une adresse IP auparavant invisible](fusion-scenario-reference.md#mass-file-download-coinciding-with-sharepoint-file-operation-from-previously-unseen-ip)        <li>[Partage de fichiers en masse *suite à une* connexion suspecte à Azure AD](fusion-scenario-reference.md#mass-file-sharing-following-suspicious-azure-ad-sign-in)         <li>(PRÉVERSION) [Activités multiples de partage de rapport Power BI *suite à une* connexion suspecte à Azure AD](fusion-scenario-reference.md#multiple-power-bi-report-sharing-activities-following-suspicious-azure-ad-sign-in)         <li>[Exfiltration de boîte aux lettres Office 365 *suite à* une connexion suspecte à Azure AD](fusion-scenario-reference.md#office-365-mailbox-exfiltration-following-a-suspicious-azure-ad-sign-in)        <li>(PRÉVERSION) [Opération de fichier SharePoint à partir d’une adresse IP auparavant invisible *suite à la* détection d’un programme malveillant](fusion-scenario-reference.md#sharepoint-file-operation-from-previously-unseen-ip-following-malware-detection)         <li>(PRÉVERSION) [Ensemble de règles de manipulation de boîte de réception suspectes *suite à une* connexion suspecte à Azure AD](fusion-scenario-reference.md#suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in)        <li>(PRÉVERSION) [Partage suspect de rapport Power BI *suite à une* connexion suspecte à Azure AD](fusion-scenario-reference.md#suspicious-power-bi-report-sharing-following-suspicious-azure-ad-sign-in)  |
| **Déni de service**           | <ul><li>(PRÉVERSION) [Plusieurs activités de suppression de machines virtuelles *suite à une* connexion suspecte à Azure AD](fusion-scenario-reference.md#multiple-vm-deletion-activities-following-suspicious-azure-ad-sign-in)          |
| **Mouvement latéral**            | <ul><li>[Emprunt d’identité d’Office 365 *suite à une* connexion suspecte à Azure AD](fusion-scenario-reference.md#office-365-impersonation-following-suspicious-azure-ad-sign-in)      <li>(PRÉVERSION) [Ensemble de règles de manipulation de boîte de réception suspectes *suite à une* connexion suspecte à Azure AD](fusion-scenario-reference.md#suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in-1)        |
| **Activité administrative malveillante**     | <ul><li>[Activité administrative d’application cloud suspecte *suite à une* connexion suspecte à Azure AD](fusion-scenario-reference.md#suspicious-cloud-app-administrative-activity-following-suspicious-azure-ad-sign-in)    <li>(PRÉVERSION) [Activités de transfert de courriers *suite à une* nouvelle activité de compte d’administrateur qui n’a pas été vue récemment](fusion-scenario-reference.md#mail-forwarding-activities-following-new-admin-account-activity-not-seen-recently-1)          |
| **Exécution malveillante <br>avec un processus légitime**    | <ul><li>(PRÉVERSION) [PowerShell a établi une connexion réseau suspecte, *suivie d’un* <br>trafic anormal signalé par le pare-feu Palo Alto Networks](fusion-scenario-reference.md#powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall)   <li>(PRÉVERSION) [Exécution suspecte de WMI à distance *suivie d’un* <br>trafic anormal signalé par le pare-feu Palo Alto Networks](fusion-scenario-reference.md#suspicious-remote-wmi-execution-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall)   <li>[Ligne de commande PowerShell suspecte *suite à une* connexion suspecte](fusion-scenario-reference.md#suspicious-powershell-command-line-following-suspicious-sign-in)          |
| **Logiciel malveillant C2 ou téléchargement**      | <ul><li>(PRÉVERSION) [Modèle de balise détecté par Fortinet suite à l’échec de plusieurs connexions à un service par un utilisateur](fusion-scenario-reference.md#beacon-pattern-detected-by-fortinet-following-multiple-failed-user-sign-ins-to-a-service)     <li>(PRÉVERSION) [Modèle de balise détecté par Fortinet *suite à une* connexion suspecte à Azure AD](fusion-scenario-reference.md#beacon-pattern-detected-by-fortinet-following-suspicious-azure-ad-sign-in)       <li>(PRÉVERSION) [Demande réseau au service d’anonymisation TOR *suivie d’un* <br>trafic anormal signalé par le pare-feu de Palo Alto Networks](fusion-scenario-reference.md#network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall)       <li>(PRÉVERSION) [Connexion sortante à l’adresse IP avec un historique des tentatives d’accès non autorisées *suivie d’un* <br>trafic anormal signalé par le pare-feu Palo Alto Networks](fusion-scenario-reference.md#outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall)    |
| **Persistance**                 | <ul><li>(PRÉVERSION) [Consentement d’application rare *suite à* une connexion suspecte](fusion-scenario-reference.md#rare-application-consent-following-suspicious-sign-in)         |
| **Ransomware**                  | <ul><li>[Exécution de ransomware *suite à une* connexion suspecte à Azure AD](fusion-scenario-reference.md#ransomware-execution-following-suspicious-azure-ad-sign-in)          |
| **Exploitation à distance**         | <ul><li>(PRÉVERSION) [Utilisation présumée d’infrastructure d’attaque *suivie d’un* <br>trafic anormal signalé par le pare-feu Palo Alto Networks](fusion-scenario-reference.md#suspected-use-of-attack-framework-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall)          |
| **Piratage des ressources**          | <ul><li>(PRÉVERSION) [Déploiement suspect de ressource/groupe de ressources par un appelant auparavant invisible <br>*suite à une* connexion suspecte à Azure AD](fusion-scenario-reference.md#suspicious-resource--resource-group-deployment-by-a-previously-unseen-caller-following-suspicious-azure-ad-sign-in)          |
|

## <a name="next-steps"></a>Étapes suivantes

Obtenir plus d’informations sur la détection d’attaques multiphases avancées Fusion :

- En savoir plus sur les [détections d’attaques basées sur un scénario Fusion](fusion-scenario-reference.md).
- Découvrir comment [configurer les règles Fusion](configure-fusion-rules.md).

Maintenant que vous en savez plus sur la détection avancée des attaques multiphases, il peut vous être utile de suivre le guide de démarrage suivant pour savoir comment bénéficier d’une visibilité sur vos données et sur les menaces potentielles : [Bien démarrer avec Microsoft Sentinel](get-visibility.md).

Si vous êtes prêt à examiner les incidents créés pour vous, consultez le didacticiel suivant : [Examiner les incidents avec Microsoft Sentinel](investigate-cases.md).
