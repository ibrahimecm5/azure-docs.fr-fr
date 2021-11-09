---
title: Architecture du provisionnement d’applications local Azure AD | Microsoft Docs
description: Donne une vue d’ensemble de l’architecture du provisionnement d’applications local.
services: active-directory
author: billmath
manager: karenh444
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 05/28/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89a0f2bd6b48d195836303a89143593c4d959bfb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131028003"
---
# <a name="azure-ad-on-premises-application-provisioning-architecture"></a>Architecture de provisionnement d’applications local Azure AD

>[!IMPORTANT]
> Actuellement, la préversion du provisionnement local est sur invitation uniquement. Pour demander l’accès à la fonctionnalité, utilisez le [formulaire de demande d’accès](https://aka.ms/onpremprovisioningpublicpreviewaccess). Nous allons ouvrir la préversion à un plus grand nombre de clients et de connecteurs au cours des prochains mois, car nous préparons la disponibilité générale (GA).

## <a name="overview"></a>Vue d’ensemble

Le diagramme suivant présente une vue d’ensemble du fonctionnement du provisionnement d’applications local.

![Diagramme qui montre l’architecture de l’approvisionnement d’applications locales.](.\media\on-premises-application-provisioning-architecture\arch-3.png)

Il existe trois composants principaux pour provisionner les utilisateurs dans une application locale :

- L’agent de provisionnement assure la connectivité entre Azure Active Directory (Azure AD) et votre environnement local.
- L’hôte ECMA convertit les demandes de provisionnement d’Azure AD en demandes adressées à votre application cible. Il sert de passerelle entre Azure AD et votre application. Vous pouvez l’utiliser pour importer les connecteurs ECMA2 existants utilisés avec Microsoft Identity Manager. L’hôte ECMA n’est pas obligatoire si vous avez créé une application SCIM ou une passerelle SCIM.
- Le service de provisionnement Azure AD sert de moteur de synchronisation.

>[!NOTE]
> La synchronisation de Microsoft Identity Manager n’est pas obligatoire. Toutefois, vous pouvez l’utiliser pour créer et tester votre connecteur ECMA avant de l’importer dans l’hôte ECMA.


### <a name="firewall-requirements"></a>Configuration requise du pare-feu

Vous n’avez pas besoin d’ouvrir de connexion sortante sur le réseau d’entreprise. Les agents de provisionnement utilisent uniquement des connexions sortantes vers le service de provisionnement, ce qui signifie qu’il n’est pas nécessaire d’ouvrir des ports de pare-feu pour les connexions entrantes. Vous n’avez pas non plus besoin de réseau de périmètre (DMZ) parce que toutes les connexions sont sortantes et sur un canal sécurisé.

## <a name="ecma-connector-host-architecture"></a>Architecture de l’hôte connecteur ECMA
L’hôte connecteur ECMA utilise plusieurs zones pour effectuer la configuration locale.  Le diagramme ci-dessous est un dessin conceptuel qui présente ces zones individuelles.  La table ci-dessous décrit les zones plus en détail.

[![Hôte connecteur ECMA](.\media\on-premises-application-provisioning-architecture\ecma-2.png)](.\media\on-premises-application-provisioning-architecture\ecma-2.png#lightbox)



|Domaine|Description|
|-----|-----|
|Points de terminaison|Responsable de la communication et du transfert de données avec le service d’approvisionnement Azure AD|
|Cache en mémoire|Utilisé pour stocker les données importées à partir de la source de données locale|
|Synchronisation automatique|Assure la synchronisation asynchrone des données entre l’hôte connecteur ECMA et la source de données locale|
|Logique métier|Utilisé pour coordonner toutes les activités d’hôte connecteur ECMA.  L’heure de synchronisation automatique est configurable dans l’hôte ECMA. Elle se trouve dans la page Propriétés.|

### <a name="about-anchor-attributes-and-distinguished-names"></a>À propos des attributs d’ancre et des noms uniques
Les informations suivantes sont fournies pour mieux expliquer les attributs d’ancre et les noms uniques, en particulier utilisés par le connecteur genericSQL.

L’attribut d’ancre est un attribut unique d’un type d’objet qui ne change pas et représente cet objet dans le cache en mémoire de l’hôte connecteur ECMA.

Le nom unique (DN) est un nom qui identifie de façon unique un objet en indiquant son emplacement actuel dans la hiérarchie de répertoires.  Ou, dans le cas de SQL, dans la partition. Le nom est formé en concaténant l’attribut d’ancre à la racine de la partition du répertoire. 

Lorsque nous considérons les DN traditionnels dans un format traditionnel, par exemple, Active Directory ou LDAP, nous pensons à quelque chose de similaire à :

  `CN=Lola Jacobson,CN=Users,DC=contoso,DC=com`

Toutefois, pour une source de données telle que SQL, qui est plate et non hiérarchique, le DN doit être déjà présent dans l’une des tables ou créé à partir des informations que nous fournissons à l’hôte connecteur ECMA.  

Cela peut être réalisé en cochant la case **Autogenerated** au moment de la configuration du connecteur genericSQL. Lorsque vous choisissez le DN a générer automatiquement, l’hôte ECMA sera généré dans un format LDAP : CN=&lt;anchorvalue&gt;,OBJECT=&lt;type&gt;. Cela suppose également que le DN Ancre est **décoché** dans la page Connectivité. 
 
 [![Le DN est Ancre décoché](.\media\on-premises-application-provisioning-architecture\user-2.png)](.\media\on-premises-application-provisioning-architecture\user-2.png#lightbox)

Le connecteur genericSQL s’attend à ce que le DN soit rempli à l’aide d’un format LDAP.  Le connecteur SQL générique utilise le style LDAP avec le nom de composant « OBJECT = ». Cela lui permet d’utiliser des partitions (chaque type d’objet est une partition).

Puisque l’hôte connecteur ECMA ne prend actuellement en charge que le type d’objet USER, l’OBJECT=&lt;type&gt; sera OBJECT=USER.  Le DN d’un utilisateur avec une valeur d’ancre ljacobson serait donc :

  CN=ljacobson,OBJECT=USER


### <a name="user-creation-workflow"></a>Workflow de création de l’utilisateur

1.  Le service d’approvisionnement Azure AD interroge l’hôte connecteur ECMA pour déterminer si l’utilisateur existe.  Il utilise l’**attribut correspondant** comme filtre.  Cet attribut est défini dans le portail Azure AD sous Applications d'entreprise -> Approvisionnement local -> approvisionnement -> correspondance d’attributs.  Il est indiqué par le 1 pour la priorité de correspondante.
Vous pouvez définir un ou plusieurs attributs correspondants et les classer par ordre de priorité.  Si vous souhaitez modifier l’attribut correspondant, vous pouvez également le faire.
 [![Attribut correspondant](.\media\on-premises-application-provisioning-architecture\match-1.png)](.\media\on-premises-application-provisioning-architecture\match-1.png#lightbox)

2.  L’hôte connecteur ECMA reçoit la requête GET et interroge son cache interne pour déterminer si l’utilisateur existe et s’il a été importé.  Cette opération est effectuée avec l’**attribut de la requête**. L’attribut de la requête est défini dans la page Types d’objets.  
 [![Attribut de la requête](.\media\on-premises-application-provisioning-architecture\match-2.png)](.\media\on-premises-application-provisioning-architecture\match-2.png#lightbox)


3. Si l’utilisateur n’existe pas, Azure AD effectue une requête POST pour créer l’utilisateur.  L’hôte connecteur ECMA répond à Azure AD avec le HTTP 201 et fournit un ID à l’utilisateur. Cet ID est dérivé de la valeur d’ancre définie dans la page Types d’objets. Cette ancre sera utilisée par Azure AD pour interroger l’hôte connecteur ECMA pour les requêtes ultérieures et suivantes. 
4. Si une modification est apportée à l’utilisateur dans Azure AD, Azure AD effectuera une requête GET pour récupérer l’utilisateur à l’aide de l’ancre de l’étape précédente, plutôt que l’attribut correspondant à l’étape 1. Cela permet, par exemple, de modifier l’UPN sans rompre le lien entre l’utilisateur dans Azure AD et dans l’application.  


## <a name="agent-best-practices"></a>Bonnes pratiques concernant l’agent
- Vérifiez que le service de mise à jour automatique de l’agent de provisionnement Azure AD Connect est en cours d’exécution. Elle est activée par défaut lorsque vous installez l’agent. La mise à jour automatique est nécessaire pour que Microsoft prenne en charge votre déploiement.
- Évitez toute forme d’inspection inline sur les communications TLS sortantes établies entre les agents et Azure. Ce type d’inspection inline entraîne une dégradation du flux de communication.
- L’agent doit communiquer avec Azure et votre application, donc le placement de l’agent affecte la latence de ces deux connexions. Vous pouvez réduire la latence du trafic de bout en bout en optimisant chaque connexion réseau. Chaque connexion peut être optimisée en :
  - Réduisant la distance entre les deux extrémités du tronçon.
  - Choisissant le réseau approprié à parcourir. Par exemple, parcourir un réseau privé au lieu de l’Internet public peut être plus rapide en raison des liaisons dédiées.



## <a name="provisioning-agent-questions"></a>Questions relatives à l'agent d'approvisionnement
Certaines questions courantes sont traitées ici.

### <a name="what-is-the-ga-version-of-the-provisioning-agent"></a>Quelle est la version mise à la disposition générale de l'agent d'approvisionnement ?

Pour obtenir la dernière version GA de l’agent d’approvisionnement, consultez [Azure ad Connect Configuration agent : historique de publication des versions](provisioning-agent-release-version-history.md).

### <a name="how-do-i-know-the-version-of-my-provisioning-agent"></a>Comment connaître la version de mon agent d'approvisionnement ?

 1. Connectez-vous au serveur Windows sur lequel l’agent d’approvisionnement est installé.
 2. Accédez à **Panneau de configuration** > **Désinstaller ou modifier un programme**.
 3. Recherchez la version correspondant à l'entrée **Agent d'approvisionnement Microsoft Azure AD Connect**.

### <a name="does-microsoft-automatically-push-provisioning-agent-updates"></a>Microsoft fournit-il automatiquement les mises à jour de l'agent d'approvisionnement ?

Oui. Microsoft met à jour automatiquement l’agent de provisionnement si le service Windows Microsoft Azure AD Connect Agent Updater est en cours d’exécution. Vous devez vous assurer que votre agent est à jour pour pouvoir résoudre les problèmes.

### <a name="can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect-or-microsoft-identity-manager"></a>Est-ce que je peux installer l’agent de provisionnement sur le même serveur qui exécute Azure AD Connect ou Microsoft Identity Manager ?

Oui. Vous pouvez installer l’agent de provisionnement sur le même serveur qui exécute Azure AD Connect ou Microsoft Identity Manager, mais ils ne sont pas requis.

### <a name="how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication"></a>Comment configurer l'agent d'approvisionnement afin qu'il utilise un serveur proxy pour la communication HTTP sortante ?

L'agent d'approvisionnement prend en charge l'utilisation du proxy sortant. Vous pouvez le configurer en modifiant le fichier de configuration **C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config** de l'agent. Ajoutez les lignes suivantes à la fin du fichier, juste avant la balise `</configuration>` de fermeture. Remplacez les variables `[proxy-server]` et `[proxy-port]` par le nom de votre serveur proxy et les valeurs de port.

```
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```
### <a name="how-do-i-ensure-the-provisioning-agent-can-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent"></a>Comment savoir que l'agent d'approvisionnement peut communiquer avec le locataire Azure AD et qu'aucun pare-feu ne bloque les ports requis par l'agent ?

Vous pouvez également vérifier si tous les ports requis sont ouverts.

### <a name="how-do-i-uninstall-the-provisioning-agent"></a>Comment désinstaller l'agent d'approvisionnement ?
 1. Connectez-vous au serveur Windows sur lequel l’agent d’approvisionnement est installé.
 2. Accédez à **Panneau de configuration** > **Désinstaller ou modifier un programme**.
 3. Désinstallez les programmes suivants :
     - Agent d'approvisionnement Microsoft Azure AD Connect
     - Programme de mise à jour de l'agent Microsoft Azure AD Connect
     - Package Agent d'approvisionnement Microsoft Azure AD Connect





## <a name="next-steps"></a>Étapes suivantes

- [Provisionnement d’applications](user-provisioning.md)
