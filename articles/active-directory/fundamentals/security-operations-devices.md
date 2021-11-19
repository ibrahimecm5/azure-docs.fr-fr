---
title: Opérations de sécurité Azure Active Directory pour les appareils
description: Découvrez comment établir des lignes de base, effectuer un monitoring et créer des rapports sur les appareils pour identifier les potentiels risques de sécurité associés.
services: active-directory
author: BarbaraSelden
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 07/15/2021
ms.author: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: a80564d341ad6e3fbefe1500a222ad20fc3a482c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132349278"
---
# <a name="azure-active-directory-security-operations-for-devices"></a>Opérations de sécurité Azure Active Directory pour les appareils

Les appareils ne sont généralement pas ciblés par les attaques basées sur l’identité. En revanche, ils *peuvent* être utilisés pour répondre à des contrôles de sécurité et usurper l’identité des utilisateurs. Ils peuvent entretenir quatre relations avec Azure AD : 

* Désinscrit

* [Inscription auprès d’Azure Active Directory (Azure AD)](../devices/concept-azure-ad-register.md)

* [Appareil joints à Azure AD](../devices/concept-azure-ad-join.md)

* [Jointure hybride Azure AD](../devices/concept-azure-ad-join-hybrid.md)   
‎

Les appareils inscrits et joints reçoivent un [jeton d’actualisation principal](../devices/concept-primary-refresh-token.md), qui peut être utilisé comme artefact d’authentification principale et, dans certains cas, comme artefact d’authentification multifacteur. Les attaquants peuvent essayer d’inscrire leurs propres appareils, d’utiliser des jetons d’actualisation principaux sur des appareils légitimes pour accéder aux données de l’entreprise, de voler des jetons d’actualisation principaux sur les appareils d’utilisateurs légitimes ou de trouver des erreurs de configuration dans les contrôles basés sur les appareils dans Azure Active Directory. Avec les appareils à jointure hybride Azure AD, le processus de jointure est amorcé et contrôlé par les administrateurs, ce qui réduit les techniques d’attaque disponibles.

Pour plus d’informations sur les méthodes d’intégration des appareils, consultez [Choix des méthodes d’intégration](../devices/plan-device-deployment.md) dans l’article [Planification du déploiement d’appareils Azure AD](../devices/plan-device-deployment.md).

Pour réduire le risque d’attaque de votre infrastructure par des acteurs malveillants au moyen d’appareils, effectuez un monitoring des éléments suivants :

* Inscription des appareils et jointure Azure AD

* Accès aux applications par des appareils non conformes

* Extraction de clés BitLocker

* Rôles Administrateur d’appareils

* Connexions aux machines virtuelles

## <a name="where-to-look"></a>Emplacement des fichiers

Les fichiers journaux que vous pouvez utiliser pour l’investigation et la supervision sont les suivants : 

* [Journaux d’audit Azure AD](../reports-monitoring/concept-audit-logs.md)

* [Journaux de connexion](../reports-monitoring/concept-all-sign-ins.md)

* [Journaux d’audit Microsoft 365](/microsoft-365/compliance/auditing-solutions-overview) 

* [Journaux Azure Key Vault](../..//key-vault/general/logging.md?tabs=Vault)

Sur le Portail Azure, vous pouvez afficher les journaux d’audit Azure AD et les télécharger sous forme de fichiers CSV (valeurs séparées par des virgules) ou JSON (JavaScript Object Notation). Le Portail Azure offre plusieurs méthodes d’intégration entre les journaux Azure AD et d’autres outils, ce qui permet une plus grande automatisation du monitoring et des alertes :

* **[Microsoft Sentinel](../../sentinel/overview.md)**  : permet une analytique de sécurité intelligente au niveau de l’entreprise en fournissant des fonctionnalités d’informations de sécurité et gestion d’événements (SIEM, Security Information and Event Management). 

* **[Azure Monitor](../..//azure-monitor/overview.md)** – Permet de créer des alertes et supervisions automatisées de diverses conditions. avec possibilité de créer ou d’utiliser des classeurs pour combiner des données provenant de différentes sources.

* **[Azure Event Hubs](../../event-hubs/event-hubs-about.md) avec intégration SIEM** : permet [d’intégrer les journaux Azure AD à d’autres systèmes SIEM](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) comme Splunk, ArcSight, QRadar et Sumo Logic grâce à l’intégration Azure Event Hub.

* **[Microsoft Defender for Cloud Apps](/cloud-app-security/what-is-cloud-app-security)**  : permet de découvrir et de gérer les applications, de gouverner toutes les applications et ressources, et de vérifier la conformité des applications cloud. 

La majeure partie du monitoring et des alertes est produite par les stratégies d’accès conditionnel. Vous pouvez utiliser le [classeur Insights et rapports sur l’accès conditionnel](../conditional-access/howto-conditional-access-insights-reporting.md) pour examiner les effets d’une ou de plusieurs stratégies d’accès conditionnel sur vos connexions, ainsi que leur résultat, notamment l’état de l’appareil. Ce classeur permet de voir un résumé de l’impact et d’identifier l’incidence sur une période donnée. Vous pouvez également vous en servir pour examiner les connexions d’un utilisateur spécifique. 

 Le reste de cet article comprend des recommandations concernant le monitoring et les alertes, organisées par type de menace. Lorsqu’il existe des solutions prédéfinies spécifiques, nous en indiquons le lien ou donnons des exemples après le tableau. Sinon, vous pouvez créer des alertes à l’aide des outils précédents. 

 ## <a name="device-registrations-and-joins-outside-policy"></a>Inscriptions d’appareils et jointures en dehors de la stratégie

Les appareils inscrits auprès d’Azure AD et les appareils à jointure Azure AD possèdent des jetons d’actualisation principaux, équivalant à un seul facteur d’authentification. Ils peuvent parfois contenir des revendications d’authentification forte. Pour plus d’informations sur ce scénario, consultez [Dans quel cas un jeton d’actualisation principal obtient-il une revendication MFA ?](../devices/concept-primary-refresh-token.md). Pour empêcher les acteurs malveillants d’inscrire ou de joindre des appareils, exigez l’authentification multifacteur (MFA, Multifactor Authentication) pour réaliser ces opérations. Ensuite, effectuez un monitoring de tous les appareils inscrits ou joints sans authentification MFA. Vous devez également surveiller les modifications apportées aux paramètres et stratégies MFA, ainsi qu’aux stratégies de conformité des appareils.

 | Éléments à analyser| Niveau de risque| Where| Filtre/Sous-filtre| Notes |
| - |- |- |- |- |
| Inscription ou jointure de l’appareil effectuée sans authentification MFA| Moyenne| Journaux d’activité de connexion| Activité : authentification réussie auprès du service d’inscription des appareils <br>And<br>Aucune authentification MFA requise| Envoyer une alerte lorsque : <br>Tout appareil inscrit ou joint sans authentification MFA |
| Changements apportés à la bascule d’authentification MFA pour l’inscription des appareils dans Azure AD| Élevé| Journal d’audit| Activité : définition de stratégies d’inscription des appareils| Rechercher : <br>Bascule définie sur désactivé Aucune entrée dans le journal d’audit Planification de vérifications périodiques |
| Modifications apportées aux stratégies d’accès conditionnel exigeant un appareil joint à un domaine ou conforme| Élevé| Journal d’audit| Modifications apportées aux stratégies d’autorité de certification<br>| Envoyer une alerte lorsque : <br><li> Modification apportée à des stratégies exigeant une jointure de domaine ou la conformité<li>Modifications apportées aux emplacements approuvés<li> Comptes ou appareils ajoutés aux exceptions des stratégies MFA |


Vous pouvez créer une alerte qui avertit les administrateurs concernés lorsqu’un appareil est inscrit ou joint sans authentification MFA avec Microsoft Sentinel.

```
Sign-in logs

| where ResourceDisplayName == “Device Registration Service”

| where conditionalAccessStatus ==”success”

| where AuthenticationRequirement <> “multiFactorAuthentication”
```

Vous pouvez également utiliser [Microsoft Intune pour définir des stratégies de conformité des appareils et effectuer un monitoring](/mem/intune/protect/device-compliance-get-started).

## <a name="non-compliant-device-sign-in"></a>Connexion d’appareils non conformes

Il n’est pas possible de bloquer l’accès à toutes les applications cloud et SaaS (software as a service) avec des stratégies d’accès conditionnel exigeant des appareils conformes. 

La [gestion des appareils mobiles](/windows/client-management/mdm/) (MDM, Mobile Device Management) permet de maintenir la conformité des appareils Windows 10. Avec la version 1809 de Windows, nous avons publié une [base de référence de sécurité](/windows/client-management/mdm/) des stratégies. Azure Active Directory offre une [intégration MDM](/windows/client-management/mdm/azure-active-directory-integration-with-mdm) pour appliquer la conformité des appareils aux stratégies d’entreprise et signaler leur état de conformité. 

| Éléments à analyser| Niveau de risque| Where| Filtre/Sous-filtre| Notes |
| - |- |- |- |- |
| Connexions d’appareils non conformes| Élevé| Journaux d’activité de connexion| DeviceDetail.isCompliant ==false| Si la connexion à partir d’appareils conformes est exigée, envoyer une alerte lorsque :<br><li> Connexions d’appareils non conformes<li> Accès sans authentification MFA ou sans emplacement approuvé<p>Si vous envisagez d’exiger des appareils, effectuez un monitoring des connexions suspectes. |
| Connexions d’appareils inconnus| Faible| Journaux d’activité de connexion| <li>DeviceDetail vide<li>Authentification à facteur unique<li>Emplacement non approuvé| Rechercher : <br><li>Accès à partir d’appareils non conformes<li>Accès sans authentification MFA ou sans emplacement approuvé |


### <a name="use-loganalytics-to-query"></a>Requêtes avec Log Analytics

**Connexions d’appareils non conformes**

```
SigninLogs

| where DeviceDetail.isCompliant ==false

| where conditionalAccessStatus == “success”
```
 

**Connexions d’appareils inconnus**

```

SigninLogs
| where isempty(DeviceDetail.deviceId)

| where AuthenticationRequirement == "singleFactorAuthentication"

| where ResultType == "0"

| where NetworkLocationDetails == "[]"
```
 
## <a name="stale-devices"></a>Appareils obsolètes

Les appareils obsolètes sont des appareils qui ne se sont pas connectés pendant une période donnée. Ce cas de figure peut se produire lorsqu’un utilisateur perd un appareil, en reçoit un nouveau, ou lorsqu’un appareil à jointure Azure AD est réinitialisé ou reprovisionné. Les appareils peuvent également rester inscrits ou joints lorsque l’utilisateur n’est plus associé au locataire. Les appareils obsolètes doivent être supprimés pour empêcher l’utilisation de leur jeton d’actualisation principal.

| Éléments à analyser| Niveau de risque| Where| Filtre/Sous-filtre| Notes |
| - |- |- |- |- |
| Dernière date de connexion| Faible| API Graph| approximateLastSignInDateTime| Utiliser l’API Graph ou PowerShell pour identifier et supprimer les appareils obsolètes |

## <a name="bitlocker-key-retrieval"></a>Extraction de clés BitLocker

Les attaquants qui ont compromis l’appareil d’un utilisateur peuvent récupérer les clés [BitLocker](/windows/security/information-protection/bitlocker/bitlocker-device-encryption-overview-windows-10) dans Azure AD. Or, il est rare que les utilisateurs récupèrent des clés. Ces actions doivent donc être surveillées et examinées.

| Éléments à analyser| Niveau de risque| Where| Filtre/Sous-filtre| Notes |
| - |- |- |- |- |
| Récupération de clé| Moyenne| Journaux d’audit| OperationName == "Read BitLocker key”| Recherche <br><li>Récupération de clé<li> Autre comportement anormal des utilisateurs qui récupèrent des clés |


Dans Log Analytics, créez une requête de ce type :

```
AuditLogs

| where OperationName == "Read BitLocker key” 
```

## <a name="device-administrator-roles"></a>Rôles Administrateur d’appareils

Les administrateurs généraux et les administrateurs d’appareils cloud obtiennent automatiquement des droits d’administrateur local sur tous les appareils à jointure Azure AD. Il est important d’effectuer un monitoring des rôles qui disposent de ces droits pour sécuriser l’environnement.

| Éléments à analyser| Niveau de risque| Where| Filtre/Sous-filtre| Notes |
| - |- |- |- |- |
| Utilisateurs ajoutés au rôle d’administrateur général ou d’administrateur d’appareils| Élevé| Journaux d’audit| Type d’activité : Ajout d’un membre au rôle| Rechercher :<li> Nouveaux utilisateurs ajoutés à ces rôles Azure AD<li> Comportement ultérieur anormal d’ordinateurs ou d’utilisateurs |


## <a name="non-azure-ad-sign-ins-to-virtual-machines"></a>Connexions hors Azure AD à des machines virtuelles

Les connexions aux machines virtuelles Windows et Linux doivent être surveillées lorsqu’elles sont effectuées par des comptes autres que des comptes Azure AD.

### <a name="azure-ad-sign-in-for-linux"></a>Connexion Azure AD pour Linux

La connexion Azure AD pour Linux permet aux organisations de se connecter à leurs machines virtuelles Linux Azure avec des comptes Azure AD via le protocole SSH (Secure Shell).

| Éléments à analyser| Niveau de risque| Where| Filtre/Sous-filtre| Notes |
| - |- |- |- |- |
| Connexion d’un compte autre qu’Azure AD, en particulier via SSH| Élevé| Journaux d’authentification locale| Ubuntu :  <br>monitoring de l’utilisation du protocole SSH dans /var/log/auth.log<br>RedHat : <br>monitoring de l’utilisation du protocole SSH dans /var/log/sssd/| Rechercher :<li> Entrées [dans lesquelles des comptes autres qu’Azure AD parviennent à se connecter aux machines virtuelles](../devices/howto-vm-sign-in-azure-ad-linux.md) <li>Cf. exemple suivant |


Exemple Ubuntu :

   May 9 23:49:39 ubuntu1804 aad_certhandler[3915]: Version: 1.0.015570001; user: localusertest01

   May 9 23:49:39 ubuntu1804 aad_certhandler[3915]: User ’localusertest01’ is not an AAD user; returning empty result.

   May 9 23:49:43 ubuntu1804 aad_certhandler[3916]: Version: 1.0.015570001; user: localusertest01

   May 9 23:49:43 ubuntu1804 aad_certhandler[3916]: User ’localusertest01’ is not an AAD user; returning empty result.

   May 9 23:49:43 ubuntu1804 sshd[3909]: Accepted publicly for localusertest01 from 192.168.0.15 port 53582 ssh2: RSA SHA256:MiROf6f9u1w8J+46AXR1WmPjDhNWJEoXp4HMm9lvJAQ

   May 9 23:49:43 ubuntu1804 sshd[3909]: pam_unix(sshd:session): session opened for user localusertest01 by (uid=0).

Vous pouvez définir une stratégie pour les connexions aux machines virtuelles Linux, et détecter et signaler les machines virtuelles Linux pour lesquelles des comptes locaux non approuvés ont été ajoutés. Pour plus d’informations, consultez [Respect des normes et évaluation de la conformité avec Azure Policy](../devices/howto-vm-sign-in-azure-ad-linux.md). 

### <a name="azure-ad-sign-ins-for-windows-server"></a>Connexions Azure AD pour Windows Server

La connexion Azure AD pour Windows permet à votre organisation de se connecter à vos machines virtuelles Azure Windows 2019 (et versions ultérieures) avec des comptes Azure AD via le protocole RDP (Remote Desktop Protocol).

| Éléments à analyser| Niveau de risque| Where| Filtre/Sous-filtre| Notes |
| - |- |- |- |- |
| Connexion d’un compte autre qu’Azure AD, en particulier via RDP| Élevé| Journaux des événements Windows Server| Connexion interactive à une machine virtuelle Windows| Événement 528, type d’ouverture de session 10 (RemoteInteractive),<br>qui indique quand un utilisateur se connecte avec les services Terminal Server ou le Bureau à distance |


## <a name="next-steps"></a>Étapes suivantes

Consultez ces articles supplémentaires concernant les opérations de sécurité :

[Vue d’ensemble des opérations de sécurité Azure AD](security-operations-introduction.md)

[Opérations de sécurité pour les comptes d’utilisateur](security-operations-user-accounts.md)

[Opérations de sécurité pour les comptes privilégiés](security-operations-privileged-accounts.md)

[Opérations de sécurité pour Privileged Identity Management](security-operations-privileged-identity-management.md)

[Opérations de sécurité pour les applications](security-operations-applications.md)

[Opérations de sécurité pour les appareils](security-operations-devices.md)

 
[Opérations de sécurité pour l’infrastructure](security-operations-infrastructure.md)
