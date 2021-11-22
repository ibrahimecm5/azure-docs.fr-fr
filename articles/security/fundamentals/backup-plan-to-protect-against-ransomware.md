---
title: Plan de sauvegarde et de restauration Azure pour la protection contre les ransomware | Microsoft Docs
description: Découvrez ce qu’il faut faire avant et pendant une attaque de ransomware pour protéger vos systèmes métier critiques et garantir une récupération rapide des opérations de l’entreprise.
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 08/27/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5affe181185d726432596bfda6dad6f4f041de80
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132277920"
---
# <a name="backup-and-restore-plan-to-protect-against-ransomware"></a>Plan de sauvegarde et de restauration pour la protection contre les ransomware

Les attaques par ransomware cryptent ou effacent délibérément les données et les systèmes pour obliger votre organisation à payer l’argent aux attaquants. Ces attaques ciblent vos données, vos sauvegardes, ainsi que la documentation clé nécessaire à la récupération sans payer les attaquants (comme un moyen d’augmenter les chances de payer par votre organisation).

Cet article aborde ce qu’il faut faire avant une attaque pour protéger vos systèmes métier critiques et durant une attaque pour garantir une récupération rapide des opérations de l’entreprise.

> [!NOTE]
> Cette préparation améliore également la résilience aux catastrophes naturelles et aux attaques rapides comme [WannaCry](https://en.wikipedia.org/wiki/WannaCry_ransomware_attack) & [(Not)Petya](https://attack.mitre.org/software/S0368/).

## <a name="what-is-ransomware"></a>Qu’est-ce qu’un ransomware ?

Un ransomware est un type d’attaque par extorsion qui chiffre les fichiers et les dossiers, ce qui empêche d’accéder aux données et aux systèmes importants. Les attaquants utilisent des ransomware pour extorquer de l’argent aux victimes en exigeant de l’argent, généralement sous la forme de cryptomonnaies, en échange d’une clé de déchiffrement ou en échange de la promesse de ne pas publier de données sensibles sur le dark Web ou sur l’Internet public.

Tandis que les ransomware précoces utilisaient souvent des logiciels malveillants qui se répandaient avec l’hameçonnage ou entre des appareils, les [ransomwares humains](/security/compass/human-operated-ransomware) sont apparus lorsqu’un groupe d’attaquants actifs, dirigés par des opérateurs d’attaques humains, ciblait tous les systèmes d’une organisation (au lieu d’un seul appareil ou ensemble d’appareils). Une attaque peut :

- Chiffrer vos données
- Exfiltrer vos données
- Corrompre vos sauvegardes

Les ransomware utilisent la connaissance des attaquants de la mauvaise configuration des systèmes et de la sécurité et des vulnérabilités pour s’infiltrer dans l’organisation, parcourir le réseau d’entreprise et s’adapter à l’environnement et à ses faiblesses au fur et à mesure.

Les ransomware peuvent être mis en place pour exfiltrer vos données en premier, pendant plusieurs semaines ou mois, avant que les ransomware ne s’exécutent en réalité à une date spécifique.

Le ransomware peut également chiffrer lentement vos données tout en conservant votre clé sur le système. Une fois votre clé toujours disponible, vos données peuvent vous être utiles et les ransomware ne sont pas remarqués. Toutefois, vos sauvegardes sont des données chiffrées. Une fois que toutes vos données sont chiffrées et que les sauvegardes récentes sont également des données chiffrées, votre clé est supprimée afin que vous ne puissiez plus lire vos données.

Les dommages réels sont souvent causés lorsque l’attaque expose des fichiers tout en laissant des portes dérobées dans le réseau pour les activités malveillantes futures, et ces risques persistent si le ransomware est payé. Ces attaques peuvent être catastrophiques pour les activités de l’entreprise et sont difficiles à effacer, nécessitant une éviction complète de l’adversaire pour se protéger contre des attaques futures. Contrairement aux premières formes de ransomware qui ne demandaient qu’une correction de programme malveillant, les ransomware contrôlés par l’homme peuvent continuer de menacer les activités de votre entreprise même après la rencontre initiale.

### <a name="impact-of-an-attack"></a>Impact d’une attaque

L’impact d’une attaque de ransomware sur une organisation est difficile à quantifier avec précision. En fonction de l’étendue de l’attaque, l’impact peut être :

- Perte d’accès aux données
- Perturbation de l’opération commerciale
- Pertes financières
- Vol de propriété intellectuelle
- Confiance des clients compromise ou réputation ternie
- Frais légaux

## <a name="how-can-you-protect-yourself"></a>Comment pouvez-vous vous protéger ?

La meilleure façon d’empêcher d’être la victime d’un ransomware est d’implémenter des mesures préventives et d’avoir des outils qui protègent votre organisation de chaque étape que les attaquants prennent pour infiltrer vos systèmes.

Vous pouvez réduire votre exposition locale en déplaçant votre organisation vers un service Cloud. Microsoft a investi dans des fonctionnalités de sécurité natives qui rendent Microsoft Azure résilient contre les attaques par ransomware et aide les organisations à contourner les techniques d’attaque par ransomware. Pour obtenir une vue d’ensemble globale des ransomwares et de l’extorsion et pour savoir comment protéger votre organisation, consultez la présentation PowerPoint décrivant le [plan de projet d’atténuation des risques liés aux ransomwares contrôlés par l’homme](https://download.microsoft.com/download/7/5/1/751682ca-5aae-405b-afa0-e4832138e436/RansomwareRecommendations.pptx) .

Vous devez supposer que, à un moment donné, vous êtes victime d’une attaque par ransomware. L’une des étapes les plus importantes que vous pouvez suivre pour protéger vos données et éviter de payer un ransomware consiste à disposer d’un plan de sauvegarde et de restauration fiable pour vos informations stratégiques. Étant donné que les attaquants de ransomware ont beaucoup investi dans la neutralisation des applications de sauvegarde et des fonctionnalités de système d’exploitation telles que le cliché instantané de volume, il est essentiel d’avoir des sauvegardes qui ne sont pas accessibles à un attaquant malveillant.

### <a name="azure-backup"></a>Sauvegarde Azure

Le service [Sauvegarde Azure](../../backup/backup-overview.md) assure la sécurité de votre environnement de sauvegarde, tant lorsque vos données sont en transit que quand elles sont au repos. Avec Sauvegarde Azure, [vous pouvez sauvegarder](../../backup/backup-overview.md#what-can-i-back-up) :

- Fichiers, dossiers et état du système de l’environnement local
- Toutes les machines virtuelles Windows/Linux
- Disques managés Azure
- Des partages de fichiers Azure sur un compte de stockage
- Des bases de données SQL Server s’exécutant sur des machines virtuelles Azure

Les données de sauvegarde sont stockées dans le stockage Azure et l’invité ou l’attaquant n’a pas d’accès direct au stockage de sauvegarde ou son contenu. Lors d’une sauvegarde de machine virtuelle, la création et le stockage de l’instantané de sauvegarde sont effectués par la structure Azure où l’invité n’a aucune implication autre que la suspension de la charge de travail pour les sauvegardes cohérentes d’applications. Avec SQL et SAP HANA, l’extension de sauvegarde obtient un accès temporaire pour écrire dans des blobs spécifiques. De cette façon, même dans un environnement compromis, l’attaquant ne peut ni falsifier ni supprimer des sauvegardes existantes.

le service Sauvegarde Azure intègre des fonctionnalités de surveillance et d’alerte permettant d’afficher et de configurer des actions pour des événements liés au service Sauvegarde Azure. Les Rapports de sauvegarde font office de destination unique pour le suivi de l’utilisation, l’audit des sauvegardes et des restaurations, ainsi que l’identification de tendances clés à différents niveaux de précision. Les outils de surveillance et signalement de Sauvegarde Azure peuvent vous avertir d’activités non autorisées, suspectes ou malveillantes dès qu’elles se produisent.

Des vérifications ont été ajoutées pour que seuls les utilisateurs valides puissent effectuer diverses opérations. Cela comprend l’ajout d’une couche supplémentaire d’authentification. Dans le cadre de l’ajout d’une couche supplémentaire d’authentification pour les opérations critiques, vous êtes invité à entrer un code PIN de sécurité avant de [modifier les sauvegardes en ligne](../../backup/backup-azure-security-feature.md#prevent-attacks).

En savoir plus sur les [fonctionnalités de sécurité](../../backup/security-overview.md) intégrées à la Sauvegarde Azure.

### <a name="validate-backups"></a>Valider les sauvegardes

Vérifiez que votre sauvegarde est correcte lorsque votre sauvegarde est créée et avant la restauration. Nous vous recommandons d’utiliser un [coffre Recovery Services](../../backup/backup-azure-recovery-services-vault-overview.md). C’est une entité de stockage dans Azure qui héberge des données. Les données sont généralement des copies de données ou des informations de configuration pour des machines virtuelles, des charges de travail, des serveurs ou des stations de travail. Vous pouvez utiliser des coffres Recovery Services afin de stocker des données de sauvegarde pour divers services Azure tels que des machines virtuelles IaaS (Windows ou Linux) et des bases de données Azure SQL, ainsi que des ressources locales. Les coffres Recovery Services facilitent l’organisation de vos données de sauvegarde et offrent des fonctionnalités telles que :

- Des fonctionnalités améliorées vous garantissant de pouvoir sécuriser vos sauvegardes et récupérer en toute sécurité des données même si des serveurs de production et de sauvegarde sont compromis. [Plus d’informations](../../backup/backup-azure-security-feature.md)
- Surveillance de votre environnement informatique hybride (machines virtuelles Azure IaaS et ressources locales) à partir d’un portail central. [Plus d’informations](../../backup/backup-azure-monitoring-built-in-monitor.md)
- Compatibilité avec le contrôle d’accès en fonction du rôle Azure (Azure RBAC), qui restreint l’accès aux opérations de sauvegarde et restauration à un ensemble défini de rôles d’utilisateur. Azure RBAC offre différents rôles intégrés, et Sauvegarde Microsoft Azure comprend trois rôles intégrés pour gérer les points de récupération. [Plus d’informations](../../backup/backup-rbac-rs-vault.md)
- La protection contre la suppression réversible, même si un acteur malveillant supprime une sauvegarde (ou si les données de sauvegarde sont supprimées par inadvertance). Les données de sauvegarde sont conservées pendant 14 jours supplémentaires, ce qui permet la récupération de cette sauvegarde sans perte de données. [Plus d’informations](../../backup/backup-azure-security-feature-cloud.md)
- La restauration inter-région peut être utilisée pour restaurer des machines virtuelles Azure dans une région secondaire, qui est une région jumelée Azure. Vous pouvez restaurer les données répliquées dans la région secondaire à tout moment. Cela vous permet de restaurer les données de la région secondaire pour la conformité à l’audit et, pendant les pannes, sans attendre qu’Azure déclare un incident (contrairement aux paramètres de stockage géoredondant du coffre). [Plus d’informations](../../backup/backup-azure-arm-restore-vms.md#cross-region-restore)

> [!NOTE]
> Il existe deux types de coffres dans Sauvegarde Azure. Outre les coffres de Recovery Services, il existe également des [coffres de sauvegarde](../../backup/backup-vault-overview.md) qui hébergent des données pour les charges de travail plus récentes prises en charge par Azure Backup.

## <a name="what-to-do-before-an-attack"></a>Que faire avant une attaque

Comme nous l’avons déjà dit, vous devez supposer que, à un moment donné, vous serez victime d’une attaque par ransomware. L’identification de vos systèmes stratégiques et l’application des meilleures pratiques avant une attaque vous permettra de sauvegarder et d’exécuter le plus rapidement possible. 

### <a name="determine-what-is-most-important-to-you"></a>Déterminez ce qui est le plus important pour vous

Les ransomware peuvent attaquer pendant que vous planifiez une attaque donc votre première priorité devrait être d’identifier les systèmes critiques les plus importants pour vous et de commencer à effectuer des sauvegardes régulières sur ces systèmes.

Dans notre expérience, les cinq applications les plus importantes pour les clients sont classées dans les catégories suivantes dans cet ordre de priorité :

- Systèmes d’identité : requis pour que les utilisateurs accèdent à tous les systèmes (y compris tous les autres décrits ci-dessous), tels que Active Directory, [Azure AD Connect](../../active-directory/hybrid/whatis-azure-ad-connect.md), les contrôleurs de domaine AD
- Vie humaine : tout système qui prend en charge la vie humaine ou qui peut la mettre en péril, comme les systèmes de support médical ou de vie, les systèmes de sécurité (ambulance, systèmes de répartition, contrôle du feu du trafic), les machines de grande taille, les systèmes chimiques/biologiques, la production de produits alimentaires ou personnels, etc.
- Systèmes financiers : systèmes qui traitent les transactions monétaires et qui assurent le fonctionnement de l’entreprise, tels que les systèmes de paiement et les bases de données associées, le système financier pour les rapports trimestriels
- Activation de produits ou de services : tous les systèmes nécessaires pour fournir les services professionnels ou produire/livrer des produits physiques que vos clients vous paient, systèmes de contrôle d’usine, systèmes de livraison/distribution de produits et similaires
- Sécurité (minimum) : vous devez également hiérarchiser les systèmes de sécurité requis pour surveiller les attaques et fournir des services de sécurité minimaux. Cela doit se concentrer sur la garantie que les attaques actuelles (ou plus faciles opportunes) ne sont pas immédiatement en mesure d’obtenir (ou de récupérer) un accès à vos systèmes restaurés.

Votre liste de sauvegarde hiérarchisée devient également votre liste de restauration hiérarchisée. Une fois que vous avez identifié vos systèmes critiques et que vous effectuez des sauvegardes régulières, prenez les mesures nécessaires pour réduire votre niveau d’exposition.

### <a name="steps-to-take-before-an-attack"></a>Étapes à suivre avant une attaque

Appliquez ces meilleures pratiques avant une attaque.

| Tâche | Detail |
| --- | --- |
| Identifiez les systèmes importants que vous devez mettre en ligne en premier (à l’aide des cinq principales catégories ci-dessus) et commencez immédiatement à effectuer des sauvegardes régulières de ces systèmes. | Pour obtenir une sauvegarde et une exécution aussi rapidement que possible après une attaque, déterminez aujourd’hui ce qui est le plus important pour vous. |
| Migrez votre organisation vers le cloud. <br><br>Envisagez d’acheter un plan de Support unifié Microsoft ou de collaborer avec un partenaire Microsoft pour vous aider à prendre en charge votre migration vers le Cloud. | Réduisez votre exposition locale en déplaçant les données vers les services Cloud avec la sauvegarde automatique et la restauration en libre-service. Microsoft Azure dispose d’un ensemble d’outils robustes pour vous aider à sauvegarder vos systèmes critiques et à restaurer vos sauvegardes plus rapidement. <br><br>Le [Support unifié Microsoft](https://www.microsoft.com/en-us/msservices/unified-support-solutions) est un modèle de prise en charge des services Cloud qui vous aide quand vous en avez besoin. Support unifié : <br><br>Fournit une équipe désignée qui est disponible 24/24 h et 7/7 j avec la résolution des problèmes en fonction des besoins et une escalade critique des incidents <br><br>Vous aide à surveiller l’intégrité de votre environnement informatique et fonctionne de manière proactive pour vous assurer que les problèmes sont empêchés avant qu’ils ne se produisent |
| Déplacez des données utilisateur vers des solutions Cloud comme OneDrive et SharePoint pour tirer parti des [fonctionnalités de gestion des versions et de corbeille](/compliance/assurance/assurance-malware-and-ransomware-protection#sharepoint-online-and-onedrive-for-business-protection-against-ransomware). <br><br>Éduquez les utilisateurs sur la façon de récupérer leurs fichiers par eux-mêmes afin de réduire les délais et le coût de la récupération.   Par exemple, si les fichiers de OneDrive d’un utilisateur ont été infectés par un programme malveillant, ils peuvent [restaurer](https://support.microsoft.com/office/restore-your-onedrive-fa231298-759d-41cf-bcd0-25ac53eb8a15?ui=en-US&rs=en-US&ad=US) leur OneDrive entier à une heure précédente. <br><br>Envisagez une stratégie de défense, telle que [Microsoft 365 Defender](/microsoft-365/security/defender/microsoft-365-defender), avant de permettre aux utilisateurs de restaurer leurs fichiers. | Les données utilisateur présentes dans le cloud Microsoft peuvent être protégées par des fonctionnalités intégrées de gestion des données et de sécurité. <br><br>Il est judicieux d’apprendre aux utilisateurs à restaurer leurs propres fichiers, mais vous devez veiller à ce que vos utilisateurs ne restaurent pas les logiciels malveillants utilisés pour effectuer l’attaque. Vous devez : <br><br>Vérifier que vos utilisateurs ne restaurent pas leurs fichiers tant que vous n’êtes pas certain que l’attaquant a été expulsé <br><br>Avoir une atténuation en place au cas où un utilisateur restaurerait un programme malveillant <br><br>Microsoft 365 Defender utilise des actions automatiques et des règles de mise à l’aide de l’intelligence artificielle pour résoudre les ressources affectées dans un état sécurisé. Microsoft 365 Defender tire parti des fonctionnalités de correction automatique des produits de suite pour garantir que toutes les ressources affectées en lien avec un incident sont automatiquement corrigées dans la mesure du possible. |
| Implémenter le [benchmark de sécurité Azure](/security/benchmark/azure/introduction). | Le benchmark de sécurité Azure est l’infrastructure de contrôle de sécurité propre à Azure, basée sur des infrastructures de contrôle de sécurité basées sur le secteur telles que NIST SP800-53, CIS Controls v 7.1. Il fournit des conseils aux organisations sur la configuration d’Azure et des services Azure et l’implémentation des contrôles de sécurité. Voir [Sauvegarde et récupération](/security/benchmark/azure/security-controls-v2-backup-recovery). |
| Testez régulièrement votre plan de continuité d’activité/reprise d’activité (BC/DR). <br><br>Simulez les scénarios de réponse aux incidents. Les exercices que vous effectuez lors de la préparation d’une attaque doivent être planifiés et menés dans le cadre de vos listes de sauvegarde et de restauration hiérarchisées. <br><br>Testez régulièrement la « récupération à partir de zéro » pour vous assurer que votre BC/DR peut rapidement mettre en ligne des opérations critiques en ligne à partir de zéro (tous les systèmes hors-service). | Garantit une récupération rapide des opérations métier en traitant une attaque par ransomware ou extorsion avec la même importance qu’une catastrophe naturelle. <br><br>Effectuer des exercices pratiques pour valider des processus et des procédures techniques inter-équipes, notamment des communications avec les employés et les clients hors bande (supposons que tous les e-mails et la conversation instantanée sont hors service). |
| Envisagez de créer un registre de risque pour identifier les risques potentiels et savoir comment service d’intermédiaire grâce aux actions et contrôles de prévention. Ajoutez un ransomware à l’enregistrement des risques comme un scénario à forte probabilité et impact élevé. | Un registre des risques peut vous aider à hiérarchiser les risques en fonction de la probabilité que ce risque se produise et de la gravité de votre entreprise en cas de risque. <br><br>Suivez l’état d’atténuation grâce au cycle d’évaluation [Enterprise Risk Management (ERM)](/compliance/assurance/assurance-risk-management). |
| Sauvegardez automatiquement tous les systèmes d’entreprise critiques selon une planification régulière (y compris la sauvegarde de dépendances critiques comme Active Directory). <br><br>Vérifiez que votre sauvegarde est correcte lors de la création de la sauvegarde. | Vous permet de récupérer des données jusqu’à la dernière sauvegarde. |
| Protégez (ou imprimez) les documents et systèmes nécessaires pour la récupération, comme les documents de procédure de restauration, la base de données CMDB, les diagrammes de réseau et les instances SolarWinds. | Les attaquants ciblent délibérément ces ressources, car cela a un impact sur votre capacité à effectuer une récupération. |
| Vérifiez que vous disposez de procédures bien documentées pour prendre en charge les fournisseurs tiers, notamment la prise en charge des fournisseurs de renseignement sur les menaces, des fournisseurs de solution anti-programme malveillant et du fournisseur d’analyse de logiciels malveillants. Protégez (ou imprimez) ces procédures. | Les contacts tiers peuvent être utiles si la variante du ransomware donné présente des faiblesses connues ou si des outils de déchiffrement sont disponibles. |
| Assurez-vous que la stratégie de sauvegarde et de récupération comprend : <br><br>Possibilité de sauvegarder des données à un point spécifique dans le temps. <br><br>Plusieurs copies de sauvegardes sont stockées dans des emplacements isolés, hors connexion (hermétiques). <br><br>Les objectifs de temps de récupération qui déterminent la vitesse à laquelle les informations sauvegardées peuvent être récupérées et mises dans un environnement de production. <br><br>Restauration rapide de la sauvegarde dans un environnement de production/bac à sable (sandbox). | Les sauvegardes sont essentielles pour la résilience après la violation d’une organisation. Appliquez la règle 3-2-1 pour une protection et une disponibilité maximales : 3 copies (original + 2 sauvegardes), 2 types de stockage et 1 copie hors site ou à froid. |
| Protégez les sauvegardes contre l’effacement délibéré et le chiffrement : <br><br>Stocker les sauvegardes dans un stockage hors ligne ou hors site et/ou un stockage immuable. <br><br>Exigez des étapes hors bande (par exemple, [MFA](../../active-directory/authentication/concept-mfa-howitworks.md) ou un code PIN de sécurité) avant d’autoriser la modification ou l’effacement d’une sauvegarde en ligne. <br><br>Créez des points de terminaison privés au sein de votre réseau virtuel Azure pour sauvegarder et restaurer en toute sécurité les données de votre coffre Recovery Services. | Les sauvegardes accessibles aux attaquants peuvent être rendues inutilisables pour la reprise de l’activité. <br><br>Ce stockage hors connexion garantit un transfert robuste des données de sauvegarde sans utiliser de bande passante réseau. La Sauvegarde Azure prend en charge la [sauvegarde hors connexion](../../backup/offline-backup-overview.md), laquelle transfère les données de sauvegarde initiales hors connexion, sans utiliser la bande passante réseau. Le service fournit un mécanisme permettant de copier des données de sauvegarde sur des appareils de stockage physiques. Les appareils sont ensuite envoyés à un centre de données Azure voisin et chargés dans un [coffre Recovery Services](../../backup/backup-azure-recovery-services-vault-overview.md). <br><br>Le stockage immuable en ligne (tel que [Stockage Blob Azure](../../storage/blobs/immutable-storage-overview.md)) vous permet de stocker des objets de données critiques pour l’entreprise dans un état WORM (disque optique non réinscriptible). Cet état les rend non effaçables et immuables pour une durée spécifiée par l’utilisateur. <br><br>[L’authentification multifacteur (MFA)](../../active-directory/authentication/concept-mfa-howitworks.md) doit être obligatoire pour tous les comptes Administrateur et est fortement recommandée pour tous les utilisateurs. La méthode recommandée consiste à utiliser une application d’authentificateur plutôt qu’un SMS ou la voix dans la mesure du possible. Quand vous configurez la sauvegarde Azure, vous pouvez configurer vos services de récupération pour activer la MFA à l’aide d’un code PIN de sécurité généré dans le Portail Azure. Cela garantit la génération d’un code PIN de sécurité pour effectuer des opérations critiques, telles que la mise à jour ou la suppression d’un point de récupération. |
| Désignez des [dossiers protégés](/windows/security/threat-protection/microsoft-defender-atp/controlled-folders). | Rend plus difficile, pour les applications non autorisées, la modification des données se trouvant dans ces dossiers. |
| Vérifiez vos autorisations : <br><br>Découvrez les autorisations d’écriture/de suppression étendues sur les partages de fichiers, SharePoint et d’autres solutions. Par « étendues », il faut entendre un grand nombre d’utilisateurs disposant d’autorisations en écriture/suppression pour les données critiques pour l’entreprise. <br><br>Réduisez les autorisations étendues tout en répondant aux exigences de collaboration métier. <br><br>Effectuez des audits et des supervisions pour garantir que les autorisations étendues ne réapparaissent pas. | Réduit le risque des activités de ransomware permettant l’accès. |
| Protégez-vous contre une tentative d’hameçonnage : <br><br>Effectuez régulièrement des formations de sensibilisation à la sécurité pour aider les utilisateurs à identifier une tentative d’hameçonnage et à éviter de cliquer sur un élément capable de créer un point d’entrée initial pour un compromis. <br><br>Appliquez des contrôles de filtrage de sécurité à la messagerie électronique pour détecter et réduire la probabilité d’une tentative de hameçonnage réussie. | La méthode la plus courante utilisée par les attaquants pour infiltrer une organisation est la tentative d’hameçonnage par e-mail. [Exchange Online Protection (EOP)](/microsoft-365/security/office-365-security/exchange-online-protection-overview) est le service de filtrage basé sur le Cloud qui protège votre organisation contre les courriers indésirables, les logiciels malveillants et autres menaces relatives aux e-mails. La fonction EOP est incluse dans toutes les organisations Microsoft 365 avec des boîte aux lettres Exchange Online. <br><br>Un exemple de contrôle de filtrage de sécurité pour la messagerie électronique est [Safe Links](/microsoft-365/security/office-365-security/safe-links). Safe Links est une fonctionnalité de Defender pour Office 365 qui fournit l’analyse d’URL et la réécriture des e-mails entrants dans le flux de courrier, ainsi que la vérification des URL et des liens dans les e-mails et d’autres emplacements. L’analyse de Safe Links se produit en plus de la protection anti-spam et anti-programme malveillant normale dans les e-mails entrants dans EOP. L’analyse Safe Links peut vous aider à protéger votre organisation contre les liens malveillants utilisés dans le hameçonnage et d’autres attaques. <br><br>En savoir plus sur la [protection anti-hameçonnage](/microsoft-365/security/office-365-security/tuning-anti-phishing). |

## <a name="what-to-do-during-an-attack"></a>Que faire pendant une attaque

Si vous êtes attaqués, votre liste de sauvegarde hiérarchisée devient également votre liste de restauration hiérarchisée. Avant de procéder à la restauration, vérifiez à nouveau que votre sauvegarde est correcte. Vous serez peut-être en mesure de rechercher des programmes malveillants à l’intérieur de la sauvegarde.

### <a name="steps-to-take-during-an-attack"></a>Étapes à suivre pendant une attaque

Appliquez ces meilleures pratiques durant une attaque.

| Tâche | Detail |
| --- | --- |
| Au début de l’attaque, contactez le support tiers, en particulier la prise en charge des fournisseurs de renseignement sur les menaces, des fournisseurs de solutions anti-programme malveillant et du fournisseur d’analyse des logiciels malveillants. | Ces contacts peuvent être utiles si la variante du ransomware donné présente des faiblesses connues ou si des outils de déchiffrement sont disponibles. <br><br>[DART (Microsoft Detection and Response Team)](https://www.microsoft.com/security/blog/2019/03/25/dart-the-microsoft-cybersecurity-team-we-hope-you-never-meet/) peut vous aider à vous protéger contre les attaques. DART s’est engagé avec des clients dans le monde entier, il contribue à se protéger et à se renforcer contre les attaques avant qu’elles ne se produisent, ainsi qu’à examiner et à corriger le moment quand une attaque a eu lieu. <br><br>Microsoft fournit également des services de récupération de ransomware rapides. Les services sont fournis exclusivement par [Compromise Recovery Security Practice (CRSP)](https://www.microsoft.com/security/blog/2021/06/09/crsp-the-emergency-team-fighting-cyber-attacks-beside-customers/) de Microsoft Global. L’objectif de cette équipe au cours d’une attaque par ransomware consiste à restaurer le service d’authentification et à limiter l’impact du ransomware. <br><br>DART et CRSP font partie de la ligne de services de sécurité [Industry Solutions Delivery](https://www.microsoft.com/en-us/msservices/security) de Microsoft. |
| Contactez vos organismes d’application de la loi locaux ou fédéraux. | Si vous êtes aux États-Unis, contactez le FBI pour signaler une violation par ransomware à l’aide du [formulaire de référence de réclamation IC3](https://ransomware.ic3.gov/default.aspx). |
| Prenez les mesures nécessaires pour supprimer la charge utile des logiciels malveillants ou des ransomware de votre environnement et arrêter la propagation. <br><br>Exécutez une analyse antivirus complète et en cours sur tous les ordinateurs et périphériques suspects pour détecter et supprimer la charge utile associée au ransomware. <br><br>Analyser les périphériques qui synchronisent les données ou les cibles des lecteurs réseau mappés. | Vous pouvez utiliser [Windows Defender](https://www.microsoft.com/windows/comprehensive-security) ou (pour les clients plus anciens) [Microsoft Security Essentials](https://www.microsoft.com/download/details.aspx?id=5201). <br><br>L'[outil de suppression des logiciels malveillants (MSRT)](https://www.microsoft.com/download/details.aspx?id=9905) est une alternative qui vous permet également de supprimer un ransomware ou un programme malveillant. |
| Restaurez d’abord les systèmes critiques pour l’entreprise. Avant de procéder à la restauration, vérifiez à nouveau que votre sauvegarde est correcte.| À ce stade, vous n’avez pas besoin de restaurer tout. Concentrez-vous sur les cinq principaux systèmes critiques de votre liste de restauration. |
| Si vous disposez de sauvegardes en mode hors connexion, vous pouvez probablement restaurer les données chiffrées **après** avoir supprimé la charge utile du ransomware (programme malveillant) de votre environnement. | Pour éviter les attaques ultérieures, assurez-vous qu’aucun ransomware ou programme malveillant ne se trouve sur la sauvegarde en mode hors connexion avant d’effectuer la restauration. |
| Identifiez une image de sauvegarde à un point dans le temps qui est connue pour ne pas être infectée. <br><br>Si vous utilisez Recovery Services Vault, examinez attentivement la chronologie des incidents pour comprendre le point dans le temps nécessaire à la restauration d’une sauvegarde. | Pour éviter les attaques ultérieures, analysez la sauvegarde du ransomware ou des programmes malveillants avant la restauration. |
| Utilisez un scanner de sécurité et d’autres outils pour la restauration complète du système d’exploitation ainsi que les scénarios de restauration des données. | Le [Scanner de sécurité Microsoft](/windows/security/threat-protection/intelligence/safety-scanner-download) est un outil d’analyse conçu pour rechercher et supprimer des programmes malveillants sur des ordinateurs Windows. Téléchargez-le et exécutez une analyse pour rechercher les programmes malveillants et essayer d’inverser les modifications apportées par les menaces identifiées. |
| Assurez-vous que votre solution antivirus ou votre solution de détection des points de terminaison et de réponse (EDR) est à jour. Vous devez également avoir des correctifs à jour. | Une solution EDR, telle que [Microsoft Defender for Endpoint](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint), est préférable. |
| Une fois les systèmes critiques opérationnels fonctionnent, restaurez d’autres systèmes. <br><br>À mesure que les systèmes sont restaurés, commencez à collecter des données de télémétrie afin de prendre des décisions formative sur ce que vous restaurez. | Les données de télémétrie doivent vous aider à identifier si des programmes malveillants sont toujours sur vos systèmes. |

## <a name="post-attack-or-simulation"></a>Après une attaque ou une simulation

Après une attaque de ransomware ou une simulation de réponse aux incidents, suivez les étapes ci-dessous pour améliorer vos plans de sauvegarde et de restauration, ainsi que votre position de sécurité :

1. Identifiez les leçons apprises lorsque le processus ne fonctionnait pas correctement (et les possibilités de simplifier, d’accélérer ou d’améliorer le processus)
2. Effectuez une analyse de la cause racine sur les défis les plus importants (suffisamment de détails pour s’assurer que les solutions répondent au bon problème, en tenant compte des personnes, des processus et de la technologie)
3. Cherchez et corrigez la violation d’origine (utilisez [Microsoft Detection and Response Team (DART)](https://www.microsoft.com/security/blog/2019/03/25/dart-the-microsoft-cybersecurity-team-we-hope-you-never-meet/) pour obtenir de l’aide)
4. Mettez à jour votre stratégie de sauvegarde et de restauration en fonction des leçons apprises et des opportunités : priorité basée sur l’impact le plus élevé et les étapes d’implémentation les plus rapides en premier

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à améliorer votre plan de sauvegarde et de restauration pour vous protéger contre les ransomware. Pour connaître les meilleures pratiques en matière de déploiement de la protection contre les ransomware, consultez [Protection rapide contre les ransomware et l’extorsionment](/security/compass/protect-against-ransomware).

Informations clés sur le secteur :

- [Microsoft Digital Defense Report](https://www.microsoft.com/security/business/security-intelligence-report?rtc=1) (voir les pages 22 à 24)

Microsoft Azure :

- [Protégez vos applications contre les ransomware avec la Sauvegarde Microsoft Azure ](https://www.youtube.com/watch?v=VhLOr2_1MCg) (vidéo de 26 minutes)
- [Récupération après une compromission de l’identité système](./recover-from-identity-compromise.md)
- [Détection avancée des attaques multiphases dans Microsoft Sentinel](../../sentinel/fusion.md#fusion-for-ransomware)

Microsoft 365 :

- [Récupération après une attaque par ransomware](/microsoft-365/security/office-365-security/recover-from-ransomware)
- [Protection contre les programmes malveillants et les ransomware](/compliance/assurance/assurance-malware-and-ransomware-protection)
- [Protégez votre PC Windows 10 contre les ransomware](https://support.microsoft.com/windows/protect-your-pc-from-ransomware-08ed68a7-939f-726c-7e84-a72ba92c01c3)
- [Gestion des ransomware dans SharePoint en ligne](/sharepoint/troubleshoot/security/handling-ransomware-in-sharepoint-online)

Microsoft 365 Defender :

- [Rechercher des ransomware avec la chasse avancée](/microsoft-365/security/defender/advanced-hunting-find-ransomware)

Billets de blog de l’équipe de sécurité Microsoft :

- [Becoming resilient by understanding cybersecurity risks: Part 4—navigating current threats](https://www.microsoft.com/security/blog/2021/05/26/becoming-resilient-by-understanding-cybersecurity-risks-part-4-navigating-current-threats/) (Devenir résilient en identifiant les cybermenaces : 4e partie, point sur les menaces actuelles) (mai 2021). Voir la section Ransomware
- [Human-operated ransomware attacks: A preventable disaster](https://www.microsoft.com/security/blog/2020/03/05/human-operated-ransomware-attacks-a-preventable-disaster/) (Attaques par ransomware dirigées par une main humaine : un incident évitable) (mars 2020). Comprend une analyse de la chaîne d’attaque pour des exemples réels d’attaques par ransomware dirigées par une main humaine
- [Ransomware response—to pay or not to pay?](https://www.microsoft.com/security/blog/2019/12/16/ransomware-response-to-pay-or-not-to-pay/) (Réponse aux ransomware : payer ou ne pas payer ?) (décembre 2019)
- [Norsk Hydro responds to ransomware attack with transparency](https://www.microsoft.com/security/blog/2019/12/17/norsk-hydro-ransomware-attack-transparency/) (Norsk Hydro répond avec transparence à une attaque par ransomware) (décembre 2019)
