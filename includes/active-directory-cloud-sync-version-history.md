---
ms.openlocfilehash: 9dabeaa9dd8e22e63b356768e34ebf828ac43632
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131860689"
---
Cet article liste les versions et les fonctionnalités de l’agent de provisionnement Azure Active Directory Connect qui ont été publiées. L’équipe Azure AD met régulièrement à jour l’agent d’approvisionnement avec de nouvelles fonctions et fonctionnalités. 

Microsoft offre une prise en charge directe de la version la plus récente de l’agent et de la version précédente.

## <a name="download-link"></a>Télécharger le lien
Vous pouvez télécharger la dernière version de l’agent en utilisant [ce lien](https://download.msappproxy.net/Subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/Connector/provisioningAgentInstaller).

## <a name="115870"></a>1.1.587.0

2 novembre 2021 – publié pour téléchargement

Améliorations et nouvelles fonctionnalités

- Cmdlet pour configurer la réécriture du mot de passe


## <a name="115840"></a>1.1.584.0 

20 août 2021 - publiée pour téléchargement

### <a name="fixed-issues"></a>Problèmes résolus

- Nous avons résolu un bogue où, lorsqu’un domaine est renommé, la synchronisation du hachage de mot de passe échoue avec une erreur indiquant « un cast spécifié n’est pas valide » dans le journal des événements. Il s’agit d’une régression par rapport aux versions antérieures.

## <a name="115820"></a>1.1.582.0

8 août, 2021-publiée en téléchargement

>[!NOTE] 
>Il s’agit d’une version de mise à jour de sécurité de Azure AD Connect. Cette version corrige une vulnérabilité, comme décrit dans[cette CVE](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2021-36949). Pour plus d’informations au sujet de cette vulnérabilité, reportez-vous à cette CVE.

## <a name="113590"></a>1.1.359.0

### <a name="new-features-and-improvements"></a>Améliorations et nouvelles fonctionnalités
- Cmdlets GMSA pour définir/réinitialiser l’autorisation

### <a name="fixed-issues"></a>Problèmes résolus
- Correctif du bogue de l’autorisation du dossier GMSA (à l’origine, le problème entraînait des problèmes de démarrage)
- Correctif de bogue pour la gestion des modifications multiples apportées à un attribut de référence à valeur unique (par exemple, manager)
- Correctif de bogue pour l’échec de l’énumération initiale et amélioration du suivi de l’échec.
- Optimisation des mises à jour de l’appartenance à un groupe d’étendue. Grâce à cela, les clients peuvent désormais synchroniser un groupe comptant jusqu’à 50 000 membres en utilisant le filtre d’étendue de groupe 
- Prise en charge de la récupération d’un seul objet par DN avec l’étendue utilisée par l’approvisionnement à la demande pour obéir à la logique d’étendue





## <a name="113540"></a>1.1.354.0

20 janvier 2021 : publiée pour téléchargement

### <a name="new-features-and-improvements"></a>Améliorations et nouvelles fonctionnalités
- Amélioration de l’expérience GMSA, y compris la prise en charge du compte GMSA personnalisé préalablement créé
- Prise en charge des [applets de commande PowerShell](../articles/active-directory/cloud-sync/how-to-gmsa-cmdlets.md) pour la configuration de GMSA
- [Prise en charge de l’interface CLI](../articles/active-directory/cloud-sync/how-to-install-pshell.md) pour l’installation de l’agent (installation sans assistance)
- Diagnostics supplémentaires pour les problèmes de mise en quarantaine de la source de l’agent
- Correctifs de bogues qui incluent la réduction de l’utilisation de la mémoire des filtres d’étendue d’unité d'organisation (UO), l’exécution de PHS uniquement pour les utilisateurs concernés, la gestion des objets imbriqués dans l’unité d’organisation en cas d’utilisation de l’étendue d’UO, etc. 


### <a name="fixed-issues"></a>Problèmes résolus
-    Empêcher la mise en quarantaine lorsque le groupe d’étendue est hors de portée
-   Lorsque les filtres d’étendue sont configurés, la tâche PHS ne fonctionne désormais que pour les utilisateurs concernés
-   L’agent arrête parfois de répondre pendant la mise à niveau
-   Synchronisation initiale des objets dans les unités d’organisation imbriquées lors de l’utilisation de l’étendue d’UO
-   Renforcement de Repair-AADCloudSyncToolsAccount
-   Réduire l’utilisation importante de la mémoire des filtres d’étendue d’UO
-   La vérification du rôle d’administrateur échoue si les membres d’un rôle contiennent un groupe de sécurité
-   Résolution du problème d’autorisation du dossier GMSA qui empêche le renouvellement du certificat de l’agent







## <a name="112810"></a>1.1.281.0

### <a name="release-status"></a>État de la version

23 novembre 2020 : publiée pour téléchargement

### <a name="new-features-and-improvements"></a>Améliorations et nouvelles fonctionnalités

* Prise en charge de [gMSA](../articles/active-directory/cloud-sync/how-to-prerequisites.md#group-managed-service-accounts)
* Prise en charge des groupes dont la taille est inférieure à 1500 membres pendant le cycle de synchronisation incrémentielle ou différentielle. Cela s’applique en cas d’utilisation du filtre de délimitation du groupe
* Prise en charge des grands groupes ayant une taille allant jusqu’à 15 000 membres
* Améliorations de la synchronisation initiale
* Journalisation détaillée avancée
* Présentation du [module PowerShell AADCloudSyncTools](../articles/active-directory/cloud-sync/reference-powershell.md)
* Correction des limitations pour autoriser l’installation de l’agent sur un serveur non anglais
* Prise en charge du filtrage par PHS uniquement pour les objets de l’étendue (à l’origine, nous synchronisons les hachages de mot de passe pour tous les objets)
* Correction du problème de fuite de mémoire dans l’agent
* Amélioration des journaux d’approvisionnement
* Support de la configuration du [délai de connexion LDAP](../articles/active-directory/cloud-sync/how-to-manage-registry-options.md#configure-ldap-connection-timeout) 
* Support de la configuration du [repérage de références](../articles/active-directory/cloud-sync/how-to-manage-registry-options.md#configure-referral-chasing) 


## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>État de la version

4 décembre 2019 : publiée pour téléchargement

### <a name="new-features-and-improvements"></a>Améliorations et nouvelles fonctionnalités

* Prend en charge la [synchronisation cloud Azure AD Connect](../articles/active-directory/cloud-sync/what-is-cloud-sync.md) pour synchroniser les données sur les utilisateurs, contacts et groupes de l’instance Active Directory locale avec Azure AD


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>État de la version

9 septembre 2019 : Publié pour la mise à jour automatique

### <a name="new-features-and-improvements"></a>Améliorations et nouvelles fonctionnalités

* Possibilité de configurer un traçage et une journalisation supplémentaires pour le débogage des problèmes de l’agent de provisionnement
* Capacité de récupérer uniquement les attributs Azure AD configurés dans le mappage pour améliorer les performances de synchronisation

### <a name="fixed-issues"></a>Problèmes résolus

* Correction d’un bogue à cause duquel l’agent ne répondait pas en cas de problème avec les échecs de connexion Azure AD
* Correction d’un bogue qui entraînait des problèmes lors de la lecture des données binaires à partir d’Azure Active Directory
* Correction d’un bogue à cause duquel l’agent ne pouvait pas renouveler l’approbation avec le service d’identité hybride cloud

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>État de la version

23 janvier 2019 : publiée pour téléchargement

### <a name="new-features-and-improvements"></a>Améliorations et nouvelles fonctionnalités

* Optimisation de l’architecture du connecteur et de l’agent de provisionnement à des fins de performances, de stabilité et de fiabilité 
* Simplification de la configuration de l’agent de provisionnement avec l’Assistant Installation géré par l’interface utilisateur
