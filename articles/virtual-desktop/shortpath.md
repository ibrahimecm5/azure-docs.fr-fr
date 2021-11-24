---
title: Azure Virtual Desktop RDP Shortpath pour les réseaux managés
titleSuffix: Azure
description: Comment configurer RDP Shortpath pour les réseaux managés pour Azure Virtual Desktop.
author: gundarev
ms.topic: conceptual
ms.date: 10/18/2021
ms.author: denisgun
ms.openlocfilehash: 43f4a8a1f467637f3a3703704361db525912f750
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132179247"
---
# <a name="azure-virtual-desktop-rdp-shortpath-for-managed-networks"></a>Azure Virtual Desktop RDP Shortpath pour les réseaux managés

RDP Shortpath pour les réseaux managés est une fonctionnalité d’Azure Virtual Desktop qui établit un transport UDP direct entre un client et un hôte de session Bureau à distance. RDP utilise ce transport pour fournir Bureau à distance et RemoteApp tout en offrant une meilleure fiabilité et une latence cohérente.

## <a name="key-benefits"></a>Principaux avantages

* Le transport RDP Shortpath est basé sur un [protocole de contrôle de débit universel (Universal Rate Control Protocol, URCP)](https://www.microsoft.com/en-us/research/publication/urcp-universal-rate-control-protocol-for-real-time-communication-applications/) très efficace. Le protocole URCP améliore le protocole UDP avec la surveillance active des conditions du réseau, et assure une utilisation de liens équitable et complète. Le protocole URCP opère à des niveaux faibles de retard et de perte en fonction des besoins du Bureau à distance. Le protocole URCP permet d’obtenir des performances optimales en apprenant de manière dynamique des paramètres réseau et en fournissant un mécanisme de contrôle de débit.
* RDP Shortpath établit la connectivité directe entre le client et l’hôte de session Bureau à distance. La connectivité directe réduit la dépendance des passerelles d’Azure Virtual Desktop, améliore la fiabilité de la connexion et augmente la bande passante disponible pour chaque session utilisateur.
* La suppression d’un relais supplémentaire réduit le temps d’aller-retour, ce qui améliore l’expérience utilisateur avec des applications et des méthodes d’entrée sensibles à la latence.
* RDP Shortpath apporte une prise en charge d’une priorité en matière de [configuration de la qualité de service (QoS)](./rdp-quality-of-service-qos.md) priorité pour les connexions RDP via des marques DSCP (Differentiated Services Code point)
* Le transport RDP Shortpath permet de [limiter le trafic réseau sortant](./rdp-bandwidth.md#limit-network-bandwidth-use-with-throttle-rate) en spécifiant un taux de limitation pour chaque session.

## <a name="connection-security"></a>Sécurité de la connexion

RDP Shortpath étend les fonctionnalités multitransport du protocole RDP. Il ne remplace pas le transport de connexion inverse mais le complète. L’ensemble de la répartition de session initiale est géré via l’infrastructure d’Azure Virtual Desktop.

Votre déploiement va utiliser seulement le port UDP configuré par l’utilisateur pour le trafic Shortpath entrant authentifié via le transport de connexion inverse. L’écouteur RDP Shortpath va ignorer toutes les tentatives de connexion, sauf si elles correspondent à la session de connexion inverse.

RDP Shortpath utilise une connexion TLS entre le client et l’hôte de session à l’aide des certificats de l’hôte de la session. Par défaut, le certificat utilisé pour le chiffrement RDP est généré automatiquement par le système d’exploitation pendant le déploiement. Si vous le souhaitez, les clients peuvent déployer des certificats gérés de manière centralisée émis par l’autorité de certification d’entreprise. Pour plus d’informations sur les configurations de certificats, consultez la [documentation de Windows Server](/troubleshoot/windows-server/remote/remote-desktop-listener-certificate-configurations).

## <a name="rdp-shortpath-connection-sequence"></a>Séquence de connexion de RDP Shortpath

Après l’établissement du [transport de connexion inverse](./network-connectivity.md), le client et l’hôte de session démarrent la connexion RDP et négocient les fonctionnalités multitransports.

Voici comment l’hôte de session négocie les fonctionnalités multitransports :

1. L’hôte de la session envoie la liste de ses adresses IPv4 et IPv6 privées et publiques au client.
2. Le client démarre le thread d’arrière-plan pour établir un transport UDP parallèle directement vers l’une des adresses IP de l’hôte.
3. Pendant que le client sonde les adresses IP fournies, il continue l’établissement de la connexion initiale sur le transport de connexion inverse pour éviter tout retard dans la connexion utilisateur.
4. Si le client a une ligne de vue directe, le client établit une connexion TLS sécurisée avec l’hôte de session.
5. Après avoir établi le transport Shortpath, le protocole RDP déplace tous les canaux virtuels dynamiques (DVC), y compris les graphiques distants, les entrées et la redirection des appareils, vers le nouveau transport.
6. Si un pare-feu ou une topologie de réseau empêche le client d’établir une connexion UDP directe, le protocole RDP continue avec un transport de connexion inverse.

Le diagramme ci-dessous donne une vue d’ensemble des connexions réseau de RDP Shortpath.

:::image type="content" source="media/rdp-shortpath-connections.svg" alt-text="Diagramme des connexions réseau de RDP Shortpath" lightbox="media/rdp-shortpath-connections.svg":::

## <a name="requirements"></a>Spécifications

Pour prendre en charge RDP Shortpath, le client Azure Virtual Desktop a besoin d’une ligne de vue directe sur l’hôte de la session. Vous pouvez obtenir une ligne de vue directe en utilisant une de ces méthodes :

- Veillez à ce que machines clientes distantes fonctionnent sous Windows 10 ou Windows 7, et que le [client Windows Desktop](/windows-server/remote/remote-desktop-services/clients/windowsdesktop) y soit installé. Actuellement, les clients non-Windows ne sont pas pris en charge.
- Utiliser [le peering privé ExpressRoute](../expressroute/expressroute-circuit-peerings.md)
- Utiliser un [réseau privé virtuel site à site (basé sur IPsec)](../vpn-gateway/tutorial-site-to-site-portal.md)
- Utiliser un [VPN point à site (basé sur IPsec)](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)
- Utiliser une [affectation d’adresses IP publiques](../virtual-network/ip-services/virtual-network-public-ip-address.md)

Si vous utilisez d’autres types de VPN pour vous connecter à Azure, nous vous recommandons d’utiliser un VPN basé sur le protocole UDP (User Datagram Protocol). Bien que la plupart des solutions VPN basées sur le protocole TCP (Transmission Control Protocol) prennent en charge le protocole UDP imbriqué, elles ajoutent une surcharge héritée du contrôle de congestion TCP, ce qui ralentit les performances de RDP.

Le fait de disposer d’une ligne de vue directe signifie que le client peut se connecter directement à l’hôte de session sans être bloqué par les pare-feux.

## <a name="configure-rdp-shortpath-for-managed-networks"></a>Configurer RDP Shortpath pour les réseaux managés

Pour activer RDP Shortpath pour les réseaux managés, vous devez activer l’écouteur RDP Shortpath sur l’hôte de session. Vous pouvez activer RDP Shortpath sur un nombre quelconque d’hôtes de session utilisés dans votre environnement. Cependant, il n’est pas nécessaire d’activer RDP Shortpath sur tous les hôtes de votre pool d’hôtes.

Pour activer l’écouteur RDP Shortpath :

1. Installez d’abord des modèles d’administration qui ajoutent des règles et des paramètres pour Azure Virtual Desktop. Téléchargez le [fichier de modèles de stratégie Azure Virtual Desktop](https://aka.ms/avdgpo) (AVDGPTemplate.cab) et extrayez le contenu du fichier .cab et de l’archive zip.
2. Copiez le fichier **terminalserver-avd.admx**, puis collez-le dans le dossier **%windir%\PolicyDefinitions**.
3. Copiez le fichier **en-us\terminalserver-avd.adml**, puis collez-le dans le dossier **%windir%\PolicyDefinitions\en-us**.
4. Pour vérifier que les fichiers ont été copiés correctement, ouvrez l’**Éditeur de stratégie de groupe** et accédez à **Configuration de l’ordinateur** > **Modèles d’administration** > **Composants Windows** > **Services Bureau à distance** > **Hôte de session Bureau à distance** > **Azure Virtual Desktop**.
5. Vous devez voir une ou plusieurs stratégies Azure Virtual Desktop, comme indiqué dans la capture d’écran suivante.

   :::image type="content" source="media/azure-virtual-desktop-gpo.png" alt-text="Capture d’écran de l’éditeur de stratégie de groupe" lightbox="media/azure-virtual-desktop-gpo.png":::

   > [!NOTE]
   > Vous pouvez également installer des modèles d’administration dans le magasin central des stratégies de groupe dans votre domaine Active Directory.
   > Pour plus d'informations sur le magasin central des modèles d’administration de stratégie de groupe, consultez [Créer et gérer le magasin central des modèles d'administration des stratégies de groupe sous Windows](/troubleshoot/windows-client/group-policy/create-and-manage-central-store).

6. Ouvrez la stratégie **« Activer RDP Shortpath pour les réseaux managés »** et définissez-la sur **Activé**. Si vous activez ce paramètre de stratégie, vous pouvez également configurer le numéro de port que l’hôte de session Azure Virtual Desktop utilisera pour écouter les connexions entrantes. Le port par défaut est 3390.
7. Redémarrez votre hôte de session pour appliquer les modifications.

## <a name="configure-windows-defender-firewall-with-advanced-security"></a>Configurez le Pare-feu Windows Defender avec une sécurité avancée.

Afin d’autoriser le trafic réseau entrant pour RDP Shortpath, utilisez le Pare-feu Windows Defender avec un nœud de sécurité avancée dans le composant logiciel enfichable MMC de Gestion des stratégies de groupe afin de créer des règles de pare-feu.

1. Ouvrez la console Gestion des stratégies de groupe pour le [Pare-feu Windows Defender avec Sécurité avancée](/windows/security/threat-protection/windows-firewall/open-the-group-policy-management-console-to-windows-firewall-with-advanced-security).
2. Dans le volet de navigation, sélectionnez **Règles de trafic entrant**.
3. Sélectionnez **Action**, puis **Nouvelle règle**.
4. Dans la page **Type de règle** de l’Assistant Nouvelle règle de trafic entrant, sélectionnez **Personnalisée**, puis **Suivant**.
5. Dans la page **Programme**, sélectionnez **Ce chemin d’accès de programme**, tapez « %SystemRoot%\system32\svchost.exe », puis sélectionnez **Suivant**.
6. Dans la page **Protocole et ports**, sélectionnez le type de protocole UDP. Dans **Port local**, sélectionnez « Ports spécifiques », puis entrez le port UDP configuré. Si vous avez conservé les paramètres par défaut, le numéro de port sera 3390.
7. Dans la page **Étendue**, vous pouvez spécifier que la règle s'applique seulement au trafic réseau vers ou depuis les adresses IP entrées sur cette page. Configurez conformément à votre conception, puis sélectionnez **Suivant**.
8. Dans la page **Action**, sélectionnez **Autoriser la connexion**, puis **Suivant**.
9. Dans la page **Profil**, sélectionnez les types d’emplacement réseau auxquels cette règle s’applique, puis sélectionnez **Suivant**.
10. Dans la page **Nom**, tapez un nom et une description pour votre règle, puis sélectionnez **Terminer**.

Quand vous avez terminé, vérifiez que la nouvelle règle correspond au format de la capture d’écran suivante.
:::image type="content" source="media/rdp-shortpath-firewall-general-tab.png" alt-text="Capture d’écran de l’onglet Général pour la configuration du pare-feu pour les connexions réseau de RDP Shortpath avec l’option Autoriser la connexion sélectionnée" lightbox="media/rdp-shortpath-firewall-general-tab.png":::

:::image type="content" source="media/rdp-shortpath-firewall-service-settings.png" alt-text="Capture d’écran de l’onglet Programmes et services pour la configuration du pare-feu pour les connexions réseau de RDP Shortpath avec l’option Services Bureau à distance sélectionnée" lightbox="media/rdp-shortpath-firewall-service-settings.png":::

:::image type="content" source="media/rdp-shortpath-firewall-protocol-and-ports.png" alt-text="Capture d’écran de l’onglet Protocoles et ports pour la configuration du pare-feu pour les connexions réseau de RDP Shortpath avec le port UDP 3390 configuré" lightbox="media/rdp-shortpath-firewall-protocol-and-ports.png":::

Vous pouvez également utiliser PowerShell pour configurer le Pare-feu Windows :

```powershell
New-NetFirewallRule -DisplayName 'Remote Desktop - Shortpath (UDP-In)'  -Action Allow -Description 'Inbound rule for the Remote Desktop service to allow RDP traffic. [UDP 3390]' -Group '@FirewallAPI.dll,-28752' -Name 'RemoteDesktop-UserMode-In-Shortpath-UDP'  -PolicyStore PersistentStore -Profile Domain, Private -Service TermService -Protocol udp -LocalPort 3390 -Program '%SystemRoot%\system32\svchost.exe' -Enabled:True
```

### <a name="using-powershell-to-configure-windows-defender-firewall"></a>Utilisation de PowerShell pour configurer le Pare-feu Windows Defender

Vous pouvez également utiliser PowerShell pour configurer la stratégie de groupe en exécutant l’applet de commande suivante.

```powershell
# Replace $domainName value with the name of your Active Directory domain
# Replace $policyName value with the name of existing Group Policy Object
$domainName = "contoso.com"
$policyName = "RDP Shortpath Policy"
$gpoSession = Open-NetGPO -PolicyStore "$domainName\$policyName"
New-NetFirewallRule -DisplayName 'Remote Desktop - Shortpath (UDP-In)'  -Action Allow -Description 'Inbound rule for the Remote Desktop service to allow RDP traffic. [UDP 3390]' -Group '@FirewallAPI.dll,-28752' -Name 'RemoteDesktop-UserMode-In-Shortpath-UDP' -Profile Domain, Private -Service TermService -Protocol udp -LocalPort 3390 -Program '%SystemRoot%\system32\svchost.exe' -Enabled:True -GPOSession $gpoSession
Save-NetGPO -GPOSession $gpoSession
```

## <a name="configuring-azure-network-security-group"></a>Configuration d’un groupe de sécurité réseau Azure

Pour autoriser l’accès à l’écouteur RDP Shortpath dans les limites de sécurité réseau, vous devez configurer le groupe de sécurité réseau Azure de manière à autoriser le port UDP entrant 3390.
Suivez la [documentation sur le groupe de sécurité réseau](../virtual-machines/windows/nsg-quickstart-portal.md) pour créer une règle de sécurité de trafic entrant autorisant le trafic avec les paramètres suivants, :

* **Source** - **Toute** ou la plage d’adresses IP dans laquelle résident les clients
* **Plages de ports source** -  **\***
* **Destination**  -  **Toute**
* **Plages de ports de destination** - **3390**
* **Protocole** - **UDP**
* **Action** - **Autoriser**
* Si vous le souhaitez, modifiez la **Priorité**. La priorité affecte l’ordre dans lequel les règles sont appliquées : plus la valeur numérique est faible, plus la règle est appliquée précocement.
* **Nom** - - **RDP Shortpath**

### <a name="disabling-rdp-shortpath-for-a-specific-subnet"></a>Désactivation de RDP Shortpath pour un sous-réseau spécifique

Si vous devez bloquer des sous-réseaux spécifiques en utilisant le transport RDP Shortpath, vous pouvez configurer un autre groupe de sécurité réseau qui spécifie les plages d’adresses IP sources correctes.

## <a name="verify-your-network-connectivity"></a>Vérifier la connectivité de votre réseau

Ensuite, vous devez vérifier que votre réseau utilise le protocole RDP Shortpath. Vous pouvez le faire avec une boîte de dialogue « Informations de connexion » ou en utilisant Log Analytics.

### <a name="connection-information-dialog"></a>Boîte de dialogue Informations de connexion

Pour vérifier que les connexions utilisent les RDP Shortpath, ouvrez la boîte de dialogue « Informations de connexion » en accédant à la barre d’outils **Connexion** en haut de l’écran et en sélectionnant l’icône d’antenne, comme illustré dans la capture d’écran suivante.

:::image type="content" source="media/rdp-shortpath-connection-bar.png" alt-text="Image de la barre Connexion de Bureau à distance":::

:::image type="content" source="media/rdp-shortpath-connection-info.png" alt-text="Image de la boîte de dialogue Informations de connexion de Bureau à distance":::

### <a name="using-event-logs"></a>Utilisation des journaux des événements

Pour vérifier que votre session utilise le transport RDP Shortpath :

1. Utilisez le client Azure Virtual Desktop de votre choix pour vous connecter au bureau de votre machine virtuelle.
2. Ouvrez **Observateur d’événements**, puis accédez à **Journaux des applications et des services** > **Microsoft** > **Windows** > **RemoteDesktopServices-RdpCoreCDV** > **Microsoft-Windows-RemoteDesktopServices-RdpCoreCDV/Operational**.
3. Si vous pouvez voir l’ID d’événement 131, c’est que votre réseau utilise le transport RDP Shortpath.

### <a name="use-log-analytics"></a>Utiliser Log Analytics

Si vous utilisez [Azure Log Analytics](./diagnostics-log-analytics.md), vous pouvez superviser les connexions en interrogeant la [table WVDConnections](/azure/azure-monitor/reference/tables/wvdconnections). Une colonne nommée UdpUse indique si la pile RDP d’Azure Virtual Desktop utilise le protocole UDP sur la connexion utilisateur actuelle.
Les valeurs possibles sont les suivantes :

* **0** - la connexion utilisateur n’utilise pas RDP Shortpath
- **1** - La connexion utilisateur utilise RDP Shortpath pour les réseaux managés.
  
La liste de requêtes suivante vous permet de consulter les informations de connexion. Vous pouvez exécuter cette requête dans l’[éditeur de requête Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md#write-a-query). Pour chaque requête, remplacez `userupn` par l’UPN de l’utilisateur que vous souhaitez rechercher.

```kusto
let Events = WVDConnections | where UserName == "userupn" ;
Events
| where State == "Connected"
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated, UdpUse, SessionHostName, SessionHostSxSStackVersion
| join (Events
| where State == "Completed"
| project EndTime=TimeGenerated, CorrelationId, UdpUse)
on CorrelationId
| project StartTime, Duration = EndTime - StartTime, ResourceAlias, UdpUse,  SessionHostName, SessionHostSxSStackVersion
| sort by StartTime asc
```

## <a name="troubleshooting"></a>Dépannage

### <a name="verify-shortpath-listener"></a>Vérifier l’écouteur Shortpath

Pour vérifier que l’écouteur UDP est activé, utilisez la commande PowerShell suivante sur l’hôte de la session :

```powershell
Get-NetUDPEndpoint -OwningProcess ((Get-WmiObject win32_service -Filter "name = 'TermService'").ProcessId)  -LocalPort 3390
```

Si cette option est activée, la sortie suivant s’affiche

```dos
LocalAddress                             LocalPort
------------                             ---------
::                                       3390
0.0.0.0                                  3390
```

En cas de conflit, vous pouvez identifier le processus qui bloque le port en exécutant la commande suivante :

```powershell
Get-Process -id (Get-NetUDPEndpoint  -LocalPort 3390 -LocalAddress 0.0.0.0).OwningProcess
```

## <a name="disabling-rdp-shortpath"></a>Désactivation de RDP Shortpath

Dans certains cas, vous devrez peut-être désactiver le transport RDP Shortpath. Vous pouvez désactiver RDP Shortpath à l’aide de la stratégie de groupe.

### <a name="disabling-rdp-shortpath-on-the-client"></a>Désactivation de RDP Shortpath sur le client

Pour désactiver les RDP Shortpath pour un client spécifique, vous pouvez utiliser la stratégie de groupe suivante afin de désactiver la prise en charge du protocole UDP :

1. Sur le client, exécutez **gpedit.msc**.
2. Accédez à **Configuration de l’ordinateur** > **Modèles d’administration** > **Composants Windows** > **Services Bureau à distance** > **Client Connexion Bureau à distance**.
3. Définissez le paramètre de **« Désactiver UDP sur le client »** sur **Activé**.

### <a name="disable-rdp-shortpath-on-the-session-host"></a>Désactiver RDP Shortpath sur l’hôte de session

Afin de désactiver RDP Shortpath pour un hôte de session spécifique, vous pouvez utiliser la stratégie de groupe suivante pour désactiver la prise en charge du protocole UDP :

1. Sur l’hôte de la session, exécutez **gpedit.msc**.
2. Accédez à **Configurationd’ ordinateur > Modèles d’administration > Composants Windows > Services Bureau à distance > Hôte Connexion Bureau à distance > Hôte de la session Bureau à distance > Connexions**.
3. Définissez le paramètre **« Sélectionner des protocoles de transfert RDP »** sur **TCP uniquement**.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la connectivité réseau d’Azure Virtual Desktop, consultez [Comprendre la connectivité réseau d’Azure Virtual Desktop](network-connectivity.md).
* Pour vous familiariser avec QoS (Qualité de service) pour Azure Virtual Desktop, consultez [Implémenter QoS (Qualité de service) pour Azure Virtual Desktop](rdp-quality-of-service-qos.md).
