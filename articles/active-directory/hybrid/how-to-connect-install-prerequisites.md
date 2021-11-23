---
title: 'Azure AD Connect : Conditions préalables et matériel | Microsoft Docs'
description: Cet article décrit les conditions préalables et la configuration matérielle requise pour Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 91b88fda-bca6-49a8-898f-8d906a661f07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/21/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d198a444cfb2c37f4bff815561c5a6dcd95d4a4e
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132135076"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Conditions préalables pour Azure AD Connect
Cet article décrit les conditions préalables et la configuration matérielle requise pour Azure Active Directory (Azure AD) Connect.

## <a name="before-you-install-azure-ad-connect"></a>Avant d’installer Azure AD Connect
Avant d’installer Azure AD Connect, voici ce dont vous avez besoin.

### <a name="azure-ad"></a>Azure AD
* Vous avez besoin d’un locataire Azure AD. Vous pouvez en obtenir un avec un [essai gratuit Azure](https://azure.microsoft.com/pricing/free-trial/). Vous pouvez utiliser un des portails suivants pour gérer Azure AD Connect :
  * Le [portail Azure](https://portal.azure.com).
  * Le [portail Office](https://portal.office.com).
* [Ajoutez et vérifiez le domaine](../fundamentals/add-custom-domain.md) que vous prévoyez d’utiliser dans Azure AD. Par exemple, si vous envisagez d’utiliser contoso.com pour vos utilisateurs, assurez-vous que ce domaine a été vérifié et que vous n’utilisez pas uniquement le domaine par défaut contoso.onmicrosoft.com.
* Un locataire Azure AD prend en charge 50 000 objets par défaut. Lorsque vous vérifiez votre domaine, la limite passe à 300 000 objets. Si vous avez besoin de davantage d’objets dans Azure AD, ouvrez une demande de support pour que la limite soit augmentée en conséquence. Si vous avez besoin de plus de 500 000 objets, il vous faut une licence telle que Microsoft 365, Azure AD Premium ou Enterprise Mobility + Security.

### <a name="prepare-your-on-premises-data"></a>Préparez vos données locales
* Utilisez [IdFix](https://github.com/Microsoft/idfix) pour identifier les erreurs comme les doublons et les problèmes de mise en forme dans votre répertoire avant d’[effectuer la synchronisation avec Azure AD et Microsoft 365](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac).
* Vérifiez [les fonctionnalités de synchronisation facultatives que vous pouvez activer dans Azure AD](how-to-connect-syncservice-features.md) et évaluez quelles fonctionnalités vous devez activer.

### <a name="on-premises-active-directory"></a>Active Directory local
* La version de schéma Active Directory et le niveau fonctionnel de forêt doivent être Windows Server 2003 ou version ultérieure. Les contrôleurs de domaine peuvent exécuter n’importe quelle version aussi longtemps que les exigences relatives à la version de schéma et au niveau de forêt sont remplies.
* Si vous prévoyez d’utiliser la fonctionnalité *Réécriture du mot de passe*, les contrôleurs de domaine doivent être exécutés dans Windows Server 2016 ou une version ultérieure.
* Le contrôleur de domaine utilisé par Azure AD doit être accessible en écriture. L’utilisation d’un contrôleur de domaine en lecture seule (RODC) *n’est pas prise en charge* et Azure AD Connect ne suivra pas les redirections d’écriture.
* L’utilisation de forêts ou de domaines locaux utilisant des noms NetBIOS avec point (le nom contient un point « . ») *n’est pas prise en charge*.
* Nous vous recommandons d’[activer la corbeille d’Active Directory](how-to-connect-sync-recycle-bin.md).

### <a name="powershell-execution-policy"></a>Stratégie d'exécution de PowerShell
Azure Active Directory Connect exécute les scripts PowerShell signés dans le cadre de l’installation. Assurez-vous que la stratégie d’exécution de PowerShell autorise l’exécution de scripts.

La stratégie d’exécution recommandée lors de l’installation est « RemoteSigned ».

Pour plus d’informations sur la définition de la stratégie d’exécution de PowerShell, consultez [Set-ExecutionPolicy](/powershell/module/microsoft.powershell.security/set-executionpolicy).


### <a name="azure-ad-connect-server"></a>Serveur Azure AD Connect
Le serveur Azure AD Connect contient des données d’identité critiques. Il est important que l’accès administratif à ce serveur soit correctement sécurisé. Suivez les instructions de l’article [Sécurisation de l’accès privilégié](/windows-server/identity/securing-privileged-access/securing-privileged-access). 

Le serveur Azure AD Connect doit être traité comme un composant de niveau 0, comme expliqué dans le [modèle de niveau administratif Active Directory](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material). 

Pour en savoir plus sur la sécurisation de votre environnement Active Directory, consultez [Meilleures pratiques pour la sécurisation d’Active Directory](/windows-server/identity/ad-ds/plan/security-best-practices/best-practices-for-securing-active-directory).

#### <a name="installation-prerequisites"></a>Prérequis pour l’installation

- Azure AD Connect doit être installé sur un serveur Windows Server 2016 joint à un domaine ou sur une version ultérieure. 
- Vous ne pouvez pas installer Azure AD Connect sur des serveurs Small Business Server ou Windows Server Essentials dont la version est antérieure à 2019 (Windows Server Essentials 2019 est pris en charge). Le serveur doit utiliser Windows Server Standard ou une version supérieure. 
- Le serveur Azure AD Connect doit disposer d’une interface utilisateur graphique complète. L’installation d’Azure AD Connect sur Windows Server Core n’est pas prise en charge. 
- La stratégie de groupe de transcription PowerShell ne doit pas être activée sur le serveur Azure AD Connect si vous utilisez l’Assistant Azure AD Connect pour gérer la configuration des services de fédération Active Directory (AD FS). Vous pouvez activer la transcription PowerShell si vous utilisez l’Assistant Azure AD Connect pour gérer la configuration de la synchronisation. 
- Si AD FS est déployé : 
    - Les serveurs sur lesquels AD FS ou le proxy d’application web sont installés doivent être des serveurs Windows Server 2012 R2 ou version ultérieure. La gestion à distance de Windows doit être activée sur ces serveurs pour l’installation à distance. 
    - Vous devez configurer des certificats TLS/SSL. Pour plus d’informations, consultez [Gestion des protocoles SSL/TLS et des suites de chiffrement pour AD FS](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs) et [Gestion des certificats SSL dans AD FS](/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap).
    - Vous devez configurer la résolution de noms. 
- Le fractionnement et l’analyse du trafic entre Azure AD Connect et Azure AD ne sont pas pris en charge. Cela pourrait perturber le service.
- Si l’authentification multifacteur est activée pour vos administrateurs généraux, l’URL https://secure.aadcdn.microsoftonline-p.com *doit* figurer dans la liste des sites de confiance. Vous êtes invité à ajouter ce site à la liste des sites de confiance lorsque vous êtes invité à passer un test d’authentification multifacteur et qu’il n’a pas encore été ajouté. Vous pouvez utiliser Internet Explorer pour l’ajouter à vos sites de confiance.
- Si vous envisagez d'utiliser Azure AD Connect Health pour la synchronisation, assurez-vous que les conditions préalables d'Azure AD Connect Health sont également remplies. Pour plus d'informations, consultez [Installation de l'agent Azure AD Connect Health](how-to-connect-health-agent-install.md).

### <a name="harden-your-azure-ad-connect-server"></a>Renforcer votre serveur Azure AD Connect 
Nous vous recommandons de renforcer votre serveur Azure AD Connect afin de réduire la surface d’attaque de sécurité de ce composant essentiel de votre environnement informatique. En suivant ces recommandations, vous contribuez à atténuer certains risques de sécurité pour votre organisation.

- Traitez Azure AD Connect de la même manière qu’un contrôleur de domaine et que d’autres ressources de niveau 0. Pour plus d’informations, consultez [Modèle de niveau d’administration Active Directory](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material).
- Limitez l’accès administrateur au serveur Azure AD Connect aux seuls administrateurs de domaine ou autres groupes de sécurité étroitement contrôlés.
- Créez un [compte dédié pour tous les membres du personnel disposant d’un accès privilégié](/windows-server/identity/securing-privileged-access/securing-privileged-access). Les administrateurs ne doivent pas naviguer sur Internet, consulter leur e-mails ni effectuer des tâches de productivité quotidiennes avec des comptes à privilèges élevés.
- Suivez les instructions fournies dans [Sécurisation de l’accès privilégié](/windows-server/identity/securing-privileged-access/securing-privileged-access). 
- Refuser l’utilisation de l’authentification NTLM avec le serveur AADConnect. Voici quelques façons de procéder : [Restriction de NTLM sur le serveur AADConnect](/windows/security/threat-protection/security-policy-settings/network-security-restrict-ntlm-outgoing-ntlm-traffic-to-remote-servers) et [Restriction de NTLM sur un domaine](/windows/security/threat-protection/security-policy-settings/network-security-restrict-ntlm-ntlm-authentication-in-this-domain)
- Assurez-vous que chaque ordinateur dispose d’un mot de passe d’administrateur local unique. Pour plus d’informations, consultez l’[avis de sécurité Microsoft relatif à une solution de mot de passe administrateur local (LAPS)](https://support.microsoft.com/help/3062591/microsoft-security-advisory-local-administrator-password-solution-laps). Une LAPS peut configurer des mots de passe aléatoires uniques sur chaque station de travail et les stocker sur serveur dans Active Directory, protégé par une liste de contrôle d’accès (ACL, access-control list). Seuls les utilisateurs autorisés éligibles peuvent lire ou demander la réinitialisation de ces mots de passe de compte d’administrateur local. Vous pouvez obtenir LAPS afin de l’utiliser pour les stations de travail à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=46899). Vous trouverez des conseils supplémentaires sur le fonctionnement d’un environnement avec une LAPS et des stations de travail à accès privilégié (PAW) dans [Normes opérationnelles basées sur le principe de source propre](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#operational-standards-based-on-clean-source-principle). 
- Implémentez des [stations de travail à accès privilégié](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) dédiées pour tous les membres du personnel disposant d’un accès privilégié aux systèmes informatiques de votre organisation. 
- Suivez ces [instructions supplémentaires](/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface) pour réduire la surface d’attaque de votre environnement Active Directory.
- Si vous souhaitez configurer des alertes pour superviser les modifications apportées à l’approbation établie entre votre Idp et Azure AD, lisez [Superviser les modifications apportées à la configuration de fédération](how-to-connect-monitor-federation-changes.md). 
- Activez l’Authentification multifacteur (MFA) pour tous les utilisateurs disposant d’un accès privilégié dans Azure AD ou le répertoire actif. L’un des problèmes de sécurité liés à l’utilisation de AADConnect est que si une personne malveillante arrive à contrôler le serveur Azure AD Connect, elle peut également manipuler les utilisateurs dans Azure AD. Pour empêcher une personne malveillante d’utiliser ces fonctionnalités afin de contrôler des comptes Azure AD, MFA offre des protections de sorte que même si une personne malveillante gère le mot de passe d’un utilisateur à l’aide d’Azure AD Connect, elle ne pourra toujours pas contourner le second facteur.

### <a name="sql-server-used-by-azure-ad-connect"></a>SQL Server utilisé par Azure AD Connect
* Azure AD Connect nécessite une base de données SQL Server pour stocker les données d’identité. Par défaut, une base de données locale SQL Server 2019 Express (version légère de SQL Server Express) est installée. SQL Server Express a une limite de 10 Go qui vous permet de gérer environ 100 000 objets. Si vous avez besoin de gérer un volume plus important d’objets annuaire, pointez l’Assistant d’installation vers une autre installation de SQL Server. Le type d’installation de SQL Server peut impacter les [performances d’Azure AD Connect](./plan-connect-performance-factors.md#sql-database-factors).
* Si vous utilisez une installation différente de SQL Server, ces conditions s’appliquent :
  * Azure AD Connect prend en charge toutes les versions de SQL Server à partir de la version 2012 (avec le dernier Service Pack) et jusqu’à SQL Server 2019. Azure SQL Database *n’est pas pris en charge* en tant que base de données.
  * Vous devez utiliser un classement SQL qui ne respecte pas la casse. Ces classements sont identifiés par un \_CI_ dans leur nom. L’utilisation d’un classement qui respecte la casse, identifié par \_CS_ dans le nom, *n’est pas prise en charge*.
  * Vous ne pouvez avoir qu’un seul moteur de synchronisation par instance SQL. Le partage d’une instance SQL avec FIM/MIM Sync, DirSync ou Azure AD Sync *n’est pas pris en charge*.

### <a name="accounts"></a>Comptes
* Vous devez disposer d’un compte Administrateur global Azure AD pour le locataire Azure AD que vous souhaitez intégrer. Ce compte doit être un *compte scolaire ou d’organisation* et non un *compte Microsoft*.
* Si vous utilisez la [configuration rapide](reference-connect-accounts-permissions.md#express-settings-installation) ou effectuez une mise à niveau depuis DirSync, vous devez disposer d’un compte Administrateur d’entreprise pour votre annuaire Active Directory local.
* Si vous utilisez le chemin d’installation des paramètres personnalisés, vous avez plus d’options. Pour plus d’informations, consultez [Paramètres d’installation personnalisée](reference-connect-accounts-permissions.md#custom-installation-settings).

### <a name="connectivity"></a>Connectivité
* Le serveur Azure AD Connect nécessite une résolution DNS Intranet et Internet. Le serveur DNS doit pouvoir résoudre des noms sur votre domaine Active Directory local et sur les points de terminaison Azure AD.
* Azure AD Connect nécessite une connectivité réseau à tous les domaines configurés
* Si vous disposez de pare-feu sur votre Intranet et que vous devez ouvrir des ports entre les serveurs Azure AD Connect et vos contrôleurs de domaine, consultez l’article [Ports Azure AD Connect](reference-connect-ports.md) pour en savoir plus.
* Si votre proxy ou pare-feu limite les URL accessibles, les URL répertoriées dans [URL et plages d’adresses IP Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) doivent être ouvertes. Voir aussi [Mettre sur liste fiable les URL du portail Azure sur votre pare-feu ou serveur proxy](../../azure-portal/azure-portal-safelist-urls.md?tabs=public-cloud).
  * Si vous utilisez le cloud Microsoft en Allemagne ou le cloud Microsoft Azure Government, consultez [Considérations relatives aux instances de service Azure AD Connect Sync](reference-connect-instances.md) à propos des URL.
* Azure AD Connect (version 1.1.614.0 et ultérieures) utilise TLS 1.2 par défaut pour le chiffrement de la communication entre le moteur de synchronisation et Azure AD. Si TLS 1.2 n’est pas disponible sur le système d’exploitation sous-jacent, Azure AD Connect revient de façon incrémentielle aux protocoles plus anciens (TLS 1.1 et TLS 1.0). À partir d’Azure AD Connect version 2.0. Les protocoles TLS 1.0 et 1.1 ne sont plus pris en charge et l’installation échoue si TLS 1.2 n’est pas disponible.
* Avant la version 1.1.614.0, Azure AD Connect utilise TLS 1.0 par défaut pour le chiffrement de la communication entre le moteur de synchronisation et Azure AD. Pour utiliser TLS 1.2, suivez les étapes de l’article [Activer TLS 1.2 pour Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
* Si vous utilisez un proxy sortant pour vous connecter à Internet, le paramètre suivant dans le fichier **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** doit être ajouté pour que l’Assistant d’installation et Azure AD Connect Sync puissent se connecter à Internet et à Azure AD. Ce texte doit être entré en bas du fichier. Dans ce code, *&lt;PROXYADDRESS&gt;* représente l’adresse IP ou le nom d’hôte réels du proxy.

    ```
        <system.net>
            <defaultProxy>
                <proxy
                usesystemdefault="true"
                proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
                bypassonlocal="true"
                />
            </defaultProxy>
        </system.net>
    ```

* Si votre serveur proxy requiert une authentification, le [compte de service](reference-connect-accounts-permissions.md#adsync-service-account) doit se trouver dans le domaine. Utilisez le chemin d’installation des paramètres personnalisés pour spécifier un[ compte de service personnalisé](how-to-connect-install-custom.md#install-required-components). Vous devez également modifier le fichier machine.config. Cette modification dans le fichier machine.config permet à l’Assistant d’installation et au moteur de synchronisation de répondre aux demandes d’authentification du serveur proxy. Dans toutes les pages de l’Assistant d’installation, à l’exclusion de la page **Configurer**, les informations d’identification de l’utilisateur sont utilisées. Dans la page **Configurer** à la fin de l’Assistant d’installation, le contexte bascule vers le [compte de service](reference-connect-accounts-permissions.md#adsync-service-account) que vous avez créé. La section machine.config doit se présenter comme suit :

    ```
        <system.net>
            <defaultProxy enabled="true" useDefaultCredentials="true">
                <proxy
                usesystemdefault="true"
                proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
                bypassonlocal="true"
                />
            </defaultProxy>
        </system.net>
    ```

* Si la configuration du proxy s’effectue dans une configuration existante, le **service Microsoft Azure AD Sync** doit être redémarré une fois pour qu’Azure AD Connect puisse lire la configuration du proxy et mettre à jour le comportement. 
* Lorsque Azure Connect AD envoie une requête web à Azure AD dans le cadre de la synchronisation d’annuaires, Azure AD peut mettre jusqu'à 5 minutes pour répondre. Il est courant qu’un délai d’inactivité de la connexion soit configuré sur les serveurs proxy. Vérifiez que la configuration est définie sur au moins 6 minutes, voire plus.

Consultez MSDN pour plus d’informations sur [l’élément proxy par défaut](/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings).
Pour plus d’informations lorsque vous avez des problèmes de connectivité, consultez [Résoudre les problèmes de connectivité liés à Azure AD Connect](tshoot-connect-connectivity.md).

### <a name="other"></a>Autres
Facultatif : Utilisez un compte d’utilisateur test pour vérifier la synchronisation.

## <a name="component-prerequisites"></a>Configuration requise pour les composants
### <a name="powershell-and-net-framework"></a>PowerShell et .NET Framework
Azure AD Connect repose sur Microsoft PowerShell 5.0 et le .NET Framework 4.5.1. Cette version ou une version ultérieure doit être installée sur votre serveur. 

### <a name="enable-tls-12-for-azure-ad-connect"></a>Activer TLS 1.2 pour Azure AD Connect
Avant la version 1.1.614.0, Azure AD Connect utilise TLS 1.0 par défaut pour le chiffrement de la communication entre le serveur de moteur de synchronisation et Azure AD. Vous pouvez configurer les applications .NET pour qu’elles utilisent TLS 1.2 par défaut sur le serveur. Pour plus d’informations sur TLS 1.2, consultez l’[avis de sécurité Microsoft 2960358](/security-updates/SecurityAdvisories/2015/2960358).

1. Vérifiez que le correctif .NET 4.5.1 est installé pour votre système d’exploitation. Pour plus d’informations, consultez l’[avis de sécurité Microsoft 2960358](/security-updates/SecurityAdvisories/2015/2960358). Il est possible que cette version du correctif ou une version ultérieure soit déjà installée sur votre serveur.

1. Pour tous les systèmes d’exploitation, définissez cette clé de Registre et redémarrez le serveur.
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
    "SchUseStrongCrypto"=dword:00000001
    ```
1. Si vous souhaitez également activer TLS 1.2 entre le serveur de moteur de synchronisation et un serveur SQL distant, vérifiez que vous disposez des versions requises pour la [prise en charge de TLS 1.2 pour Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

### <a name="dcom-prerequisites-on-the-synchronization-server"></a>Prérequis DCOM sur le serveur de synchronisation
Pendant l’installation du service de synchronisation, Azure AD Connect vérifie la présence de la clé de Registre suivante :

- HKEY_LOCAL_MACHINE : Software\Microsoft\Ole

Sous cette clé de Registre, Azure AD Connect vérifiera si les valeurs suivantes sont présentes et non corrompues : 

- [MachineAccessRestriction](/windows/win32/com/machineaccessrestriction)
- [MachineLaunchRestriction](/windows/win32/com/machinelaunchrestriction)
- [DefaultLaunchPermission](/windows/win32/com/defaultlaunchpermission)

## <a name="prerequisites-for-federation-installation-and-configuration"></a>Configuration requise pour l'installation et la configuration de la fédération
### <a name="windows-remote-management"></a>gestion à distance de Windows
Lorsque vous utilisez Azure AD Connect pour déployer AD FS ou le proxy d’application web (WAP), vérifiez les conditions requises suivantes :

* Si le serveur cible est joint à un domaine, assurez-vous que la gestion à distance Windows est activée.
  * Dans une fenêtre de commandes PowerShell avec élévation de privilèges, utilisez la commande `Enable-PSRemoting –force`.
* Si le serveur cible n’est pas un ordinateur WAP joint à un domaine, il existe quelques conditions requises supplémentaires :
  * Sur l'ordinateur cible (ordinateur WAP) :
    * Vérifiez que le service Windows Remote Management/WS-Management (WinRM) s’exécute via le composant logiciel enfichable Services.
    * Dans une fenêtre de commandes PowerShell avec élévation de privilèges, utilisez la commande `Enable-PSRemoting –force`.
  * Sur l’ordinateur sur lequel s’exécute l’Assistant (si l’ordinateur cible n’est pas joint à un domaine ou s’il s’agit d’un domaine non approuvé) :
    * Dans une fenêtre de commandes PowerShell avec élévation de privilèges, utilisez la commande `Set-Item.WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`.
    * Dans le gestionnaire de serveur :
      * Ajoutez un hôte WAP DMZ à un pool de machines. Dans le gestionnaire de serveur, sélectionnez **Gérer** > **Ajouter des serveurs**, puis utilisez l’onglet **DNS**.
      * Sous l’onglet **Tous les serveurs** de Gestionnaire de serveur, cliquez avec le bouton droit sur le serveur WAP, puis sélectionnez **Gérer en tant que**. Entrez les informations d’identification locales (et non de domaine) pour l’ordinateur WAP.
      * Pour valider la connectivité PowerShell distante, dans l’onglet **Tous les serveurs** de Gestionnaire de serveur, cliquez avec le bouton droit sur le serveur WAP et sélectionnez **Windows PowerShell**. Une session PowerShell distante doit s’ouvrir pour garantir le fait que des sessions PowerShell distantes peuvent être établies.

### <a name="tlsssl-certificate-requirements"></a>Configuration requise des certificats TLS/SSL
* Nous vous recommandons d’utiliser le même certificat TLS/SSL sur tous les nœuds de votre batterie de serveurs AD FS, ainsi que sur tous les serveurs de proxy d’application web.
* Il doit s’agir d’un certificat X509.
* Vous pouvez utiliser un certificat auto-signé sur les serveurs de fédération dans un environnement de laboratoire de test. Pour un environnement de production, nous vous recommandons d’obtenir le certificat auprès d’une autorité de certification publique.
  * Si vous utilisez un certificat qui n’est pas approuvé publiquement, assurez-vous que le certificat installé sur chaque serveur de proxy d’application web est approuvé sur le serveur local et sur tous les serveurs de fédération.
* L’identité du certificat doit correspondre au nom du service de fédération (par exemple, sts.contoso.com).
  * L’identité est soit une extension « autre nom de l’objet » (SAN) de type dNSName, soit, à défaut d’entrée SAN, le nom d’objet spécifié comme nom commun.
  * Le certificat peut contenir plusieurs entrées SAN, pour autant que l’une d’elles corresponde au nom des services de fédération.
  * Si vous envisagez d’utiliser Workplace Join, une autre extension SAN est nécessaire avec la valeur **enterpriseregistration.** suivie du suffixe de nom d’utilisateur principal de votre organisation, par exemple, enterpriseregistration.contoso.com.
* Les certificats basés sur les clés CNG (CryptoAPI Next Generation) et les fournisseurs de stockage de clés (KSP) ne sont pas pris en charge. Par conséquent, vous devez utiliser un certificat basé sur un fournisseur de services de chiffrement (CSP) et sur non un KSP.
* Les certificats utilisant des caractères génériques sont pris en charge.

### <a name="name-resolution-for-federation-servers"></a>Résolution de noms pour les serveurs de fédération
* Configurez les enregistrements DNS pour le nom AD FS (par exemple, sts.contoso.com) pour l’Intranet (votre serveur DNS interne) et l’extranet (le DNS public via votre registre de nom de domaine). Pour l’enregistrement DNS intranet, vérifiez que vous utilisez des enregistrements A et non des enregistrements CNAME. L’utilisation d’enregistrements A est requis pour le bon fonctionnement de l’authentification Windows à partir de votre ordinateur joint à un domaine.
* Si vous déployez plusieurs serveurs AD FS ou serveurs de proxy d’application web, vérifiez que vous avez configuré votre équilibreur de charge et que les enregistrements DNS pour le nom AD FS (p. ex., sts.contoso.com) pointent vers l’équilibreur de charge.
* Pour que l’authentification intégrée Windows fonctionne avec les applications de navigateur utilisant Internet Explorer dans votre Intranet, vérifiez que le nom AD FS (par exemple, sts.contoso.com) est ajouté à la zone Intranet dans Internet Explorer. Cette exigence peut être contrôlée par le biais de la stratégie de groupe et déployée sur tous les ordinateurs joints au domaine.

## <a name="azure-ad-connect-supporting-components"></a>Composants de prise en charge d’Azure AD Connect
Azure AD Connect installe les composants suivants sur le serveur sur lequel il est installé. Cette liste est destinée à une installation Express de base. Si vous choisissez d’utiliser un autre serveur SQL dans la page **Installer des services de synchronisation**, SQL Express LocalDB n’est pas installée localement.

* Azure AD Connect Health
* Utilitaires de ligne de commande Microsoft SQL Server 2019
* Base de données locale Microsoft SQL Server 2019 Express
* Client natif Microsoft SQL Server 2019
* Package de redistribution Microsoft Visual C++ 14

## <a name="hardware-requirements-for-azure-ad-connect"></a>Configuration matérielle requise pour Azure AD Connect
Le tableau suivant présente la configuration minimale requise pour l’ordinateur Azure AD Connect Sync.

| Nombre d’objets dans Active Directory | UC | Mémoire | Taille du disque dur |
| --- | --- | --- | --- |
| Moins de 10 000 |1,6 GHz |4 Go |70 Go |
| Entre 10 000 et 50 000 |1,6 GHz |4 Go |70 Go |
| Entre 50 000 et 100 000 |1,6 GHz |16 Go |100 Go |
| À partir de 100 000 objets, la version complète de SQL Server est requise. Pour des raisons de performance, il est préférable de l’installer localement. | | | |
| Entre 100 000 et 300 000 |1,6 GHz |32 Go |300 Go |
| Entre 300 000 et 600 000 |1,6 GHz |32 Go |450 Go |
| Plus de 600 000 |1,6 GHz |32 Go |500 Go |

La configuration minimale requise pour les ordinateurs exécutant AD FS ou les serveurs de proxy d’applications web est la suivante :

* Processeur : double cœur 1,6 GHz ou supérieur
* Mémoire : 2 Go ou plus
* Machine virtuelle Azure : configuration A2 ou supérieure

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur l’ [intégration de vos identités locales avec Azure Active Directory](whatis-hybrid-identity.md).
