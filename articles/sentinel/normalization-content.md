---
title: Contenu ASIM (Advanced SIEM Information Model) | Microsoft Docs
description: Cet article décrit le contenu Microsoft Sentinel qui utilisait ASIM (Advanced SIEM Information Model)
services: sentinel
cloud: na
documentationcenter: na
author: oshezaf
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: ofshezaf
ms.custom: ignite-fall-2021
ms.openlocfilehash: 78b8615a959a5913b171b8f52b5f6ee0caa85578
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517479"
---
# <a name="advanced-siem-information-model-asim-security-content--public-preview"></a>Contenu de sécurité ASIM (Advanced SIEM Information Model) (préversion publique)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Le contenu de sécurité normalisé dans Microsoft Sentinel comprend des règles d’analyse, des requêtes de chasse et des classeurs qui fonctionnent avec des analyseurs de normalisation indépendants du code source.

<a name="builtin"></a>Vous pouvez trouver du contenu normalisé et intégré dans les galeries et les [solutions](sentinel-solutions-catalog.md)Microsoft Sentinel, créer votre propre contenu normalisé ou modifier le contenu existant pour utiliser des données normalisées.

Cet article répertorie les contenus Microsoft Sentinel intégrés qui ont été configurés pour prendre en charge ASIM.  Vous pouvez trouver ces règles à partir des liens vers le référentiel GitHub Microsoft Sentinel fourni ci-dessous comme référence, mais également dans la [galerie de règles Microsoft Sentinel Analytics](detect-threats-built-in.md). Utilisez les pages GitHub liées pour copier toutes les requêtes de chasse appropriées.

> [!TIP]
> Regardez également le [webinaire de formation approfondie sur la normalisation des analyseurs et le contenu normalisé Microsoft Sentinel](https://www.youtube.com/watch?v=zaqblyjQW6k) ou passez en revue les [diapositives](https://1drv.ms/b/s!AnEPjr8tHcNmjGtoRPQ2XYe3wQDz?e=R3dWeM). Pour plus d’informations, consultez [Étapes suivantes](#next-steps).
>

> [!IMPORTANT]
> ASIM n’est actuellement disponible qu’en PRÉVERSION. Les [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluent des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore disponibles dans la version en disponibilité générale.
>

## <a name="authentication-security-content"></a>Contenu de sécurité de l’authentification

Le contenu d’authentification intégré suivant est pris en charge pour la normalisation ASIM.

### <a name="analytics-rules"></a>Règles analytiques

 - [Attaque par pulvérisation de mot de passe potentielle (utilise la normalisation de l’authentification)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imAuthPasswordSpray.yaml)
 - [Attaque par force brute contre les informations d’identification de l’utilisateur (utilise la normalisation de l’authentification)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imAuthBruteForce.yaml)
 - [Connexion de l’utilisateur depuis différents pays dans un délai de 3 heures (utilise la normalisation de l’authentification)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imAuthSigninsMultipleCountries.yaml)
 - [Connexions à partir d’adresses IP qui tentent de se connecter à des comptes désactivés (utilise la normalisation de l’authentification)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imSigninAttemptsByIPviaDisabledAccounts.yaml)


## <a name="dns-query-security-content"></a>Contenu de sécurité des requêtes DNS

Le contenu de requête DNS intégré suivant est pris en charge pour la normalisation ASIM.

### <a name="analytics-rules"></a>Règles analytiques

 - (Préversion) TI mappe l'entité Domain aux événements DNS (DNS normalisé)
 - (Préversion) TI mappe l'entité IP aux événements DNS (DNS normalisé)
 - [DGA potentielle détecté (ASimDNS)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDns_HighNXDomainCount_detection.yaml)
  - [Trop de requêtes DNS NXDOMAIN (DNS normalisé)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDns_ExcessiveNXDOMAINDNSQueries.yaml)
 - [Événements DNS liés aux pools d’exploration (DNS normalisé)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDNS_Miners.yaml)
 - [Événements DNS liés aux proxys ToR (DNS normalisé)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDNS_TorProxies.yaml)
 - [Domaines Barium connus](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/BariumDomainIOC112020.yaml)
 - [Adresses IP Barium connues](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/BariumIPIOC112020.yaml) 
 - [Des vulnérabilités Exchange Server ont divulgué la correspondance IoC de mars 2021](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ExchangeServerVulnerabilitiesMarch2021IoCs.yaml)
 - [Domaines et hachages GALLIUM connus](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/GalliumIOCs.yaml)
 - [Known IRIDIUM IP (Adresse IP IRIDIUM connue)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/IridiumIOCs.yaml)
 - [NOBELIUM - Domaine et IOC IP - Mars 2021](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_DomainIOCsMarch2021.yaml)
 - [Domaines/adresses IP de groupe Phosphorus connus](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/PHOSPHORUSMarch2019IOCs.yaml)
 - [Domaines de groupe STRONTIUM connus – Juillet 2019](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/STRONTIUMJuly2019IOCs.yaml)
 - [Balise réseau Solorigate](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/Solorigate-Network-Beacon.yaml)
 - [Domaines THALLIUM inclus dans DCU](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ThalliumIOCs.yaml)
 - [Codes de hachage de programme malveillant Comebacker et Klackring de ZINC connus](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)

## <a name="file-activity-security-content"></a>Contenu de sécurité de l’activité de fichier

Le contenu d’activité du fichier intégré suivant est pris en charge pour la normalisation ASIM.

### <a name="analytic-rules"></a>Règles analytiques

- [Codes de hachage de porte dérobée SUNBURST et SUPERNOVA (événements de fichier normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimFileEvent/imFileESolarWindsSunburstSupernova.yaml)
- [Des vulnérabilités Exchange Server ont divulgué la correspondance IoC de mars 2021](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ExchangeServerVulnerabilitiesMarch2021IoCs.yaml)
- [Service de messagerie unifiée HAFNIUM écrivant un fichier suspect](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/HAFNIUMUmServiceSuspiciousFile.yaml)
- [NOBELIUM – Domaine, hachage et IOC IP – Mai 2021](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_IOCsMay2021.yaml)
- [Création du fichier journal SUNSPOT ](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/SUNSPOTLogFile.yaml)
- [Codes de hachage de programme malveillant Comebacker et Klackring de ZINC connus](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)

## <a name="process-activity-security-content"></a>Contenu de sécurité de l’activité de processus

Le contenu d’activité du processus intégré suivant est pris en charge pour la normalisation ASIM.

### <a name="analytics-rules"></a>Règles analytiques

 - [Utilisation probable de l’outil de rapprochement AdFind (événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_AdFind_Usage.yaml)
 - [Lignes de commande du processus Windows encodées en Base64 (événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_base64_encoded_pefile.yaml)
 - [Programme malveillant dans la corbeille (événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_malware_in_recyclebin.yaml)
 - [NOBELIUM – Exécution suspecte rundll32.exe de vbscript (événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_NOBELIUM_SuspiciousRundll32Exec.yaml)
 - [Processus enfants SolarWinds suspects SUNBURST (événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_SolarWinds_SUNBURST_Process-IOCs.yaml)

### <a name="hunting-queries"></a>Requêtes de chasse

 - [Décomposition du résumé quotidien de script cscript (événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_cscript_summary.yaml)
 - [Énumération des utilisateurs et des groupes (événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_enumeration_user_and_group.yaml)
 - [Ajout du composant logiciel enfichable PowerShell Exchange (événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_ExchangePowerShellSnapin.yaml)
 - [Hôte exportant la boîte aux lettres et supprimant l’exportation (événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_HostExportingMailboxAndRemovingExport.yaml)
 - [Utilisation d’Invoke-PowerShellTcpOneLine (événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_Invoke-PowerShellTcpOneLine.yaml)
 - [Interpréteur de commandes TCP inverse Nishang dans Base64 (Normalized Process Events)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_NishangReverseTCPShellBase64.yaml)
 - [Résumé des utilisateurs créés à l’aide de commutateurs de ligne de commande non communs/non documentés (événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_persistence_create_account.yaml)
 - [Téléchargement Powercat (événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_PowerCatDownload.yaml)
 - [Téléchargements PowerShell (événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_powershell_downloads.yaml)
 - [Entropie pour les processus d’un hôte donné (événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_ProcessEntropy.yaml)
 - [Inventaire SolarWinds (événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_SolarWindsInventory.yaml)
 - [Énumération suspecte à l’aide de l’outil Adfind (événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_Suspicious_enumeration_using_adfind.yaml)
 - [Arrêt/redémarrage du système Windows (événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_Windows%20System%20Shutdown-Reboot(T1529).yaml)
 - [Certutil (LOLBins et LOLScripts, événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_Certutil-LOLBins.yaml)
 - [Rundll32 (LOLBins et LOLScripts, événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/inProcess_SignedBinaryProxyExecutionRundll32.yaml)
 - [Processus inhabituels – 5 % inférieurs (événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_uncommon_processes.yaml)
 - [Obfuscation Unicode dans la ligne de commande](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/MultipleDataSources/UnicodeObfuscationInCommandLine.yaml)

## <a name="registry-activity-security-content"></a>Contenu de sécurité de l’activité de registre

Le contenu d’activité du registre intégré suivant est pris en charge pour la normalisation ASIM.

### <a name="hunting-queries"></a>Requêtes de chasse

- [Persistance via la clé de Registre IFEO](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/MultipleDataSources/PersistViaIFEORegistryKey.yaml)

## <a name="modify-your-content-to-use-normalized-data"></a><a name="modify"></a>Modifier votre contenu pour utiliser des données normalisées

Pour permettre à votre contenu personnalisé d’utiliser la normalisation :

- Modifiez vos requêtes pour utiliser les analyseurs qui sont indépendants de la source en rapport avec la requête.
- Modifiez les noms de champ dans votre requête pour utiliser les noms de champs de schéma normalisés.
- Le cas échéant, modifiez les conditions pour utiliser les valeurs normalisées des champs de votre requête.

Par exemple, considérons la règle d’analyse DNS **Client rare observé avec un nombre de recherches DNS inversée élevé**, qui fonctionne sur les événements DNS envoyés par les serveurs DNS Infoblox :

```kusto
let threshold = 200;
InfobloxNIOS
| where ProcessName =~ "named" and Log_Type =~ "client"
| where isnotempty(ResponseCode)
| where ResponseCode =~ "NXDOMAIN"
| summarize count() by Client_IP, bin(TimeGenerated,15m)
| where count_ > threshold
| join kind=inner (InfobloxNIOS
    | where ProcessName =~ "named" and Log_Type =~ "client"
    | where isnotempty(ResponseCode)
    | where ResponseCode =~ "NXDOMAIN"
    ) on Client_IP
| extend timestamp = TimeGenerated, IPCustomEntity = Client_IP
```

Le code suivant est la version indépendante de la source, qui utilise la normalisation pour fournir la même détection pour toute source fournissant des événements de requête DNS :

```kusto
imDns(responsecodename='NXDOMAIN')
| summarize count() by SrcIpAddr, bin(TimeGenerated,15m)
| where count_ > threshold
| join kind=inner (imDns(responsecodename='NXDOMAIN')) on SrcIpAddr
| extend timestamp = TimeGenerated, IPCustomEntity = SrcIpAddr```
```

La version normalisée, indépendante de la source, présente les différences suivantes :

- L'`imDns`analyseur normalisé est utilisé à la place de l’analyseur Infoblox.

- `imDns` récupère uniquement les événements de requête DNS. Il n’est donc pas nécessaire de vérifier le type d’événement, tel qu’il est effectué par `where ProcessName =~ "named" and Log_Type =~ "client"` dans la version Infoblox.

- Le `SrcIpAddr` champ est utilisé à la place de `Client_IP` .
 
- Le filtrage des paramètres de l'analyseur est utilisé pour ResponseCodeName, ce qui élimine le besoin de clauses where explicites.


Outre la prise en charge d’une source DNS normalisée, la version normalisée est plus concise et plus facile à comprendre. 

Si le schéma ou les analyseurs ne prennent pas en charge le filtrage des paramètres, les modifications sont similaires, à l’exclusion de la dernière. Au lieu de cela, les conditions de filtrage sont conservées à partir de la requête d’origine, comme indiqué ci-dessous :

```kusto
let threshold = 200;
imDns
| where isnotempty(ResponseCodeName)
| where ResponseCodeName =~ "NXDOMAIN"
| summarize count() by SrcIpAddr, bin(TimeGenerated,15m)
| where count_ > threshold
| join kind=inner (imDns
    | where isnotempty(ResponseCodeName)
    | where ResponseCodeName =~ "NXDOMAIN"
    ) on SrcIpAddr
| extend timestamp = TimeGenerated, IPCustomEntity = SrcIpAddr
```

## <a name="next-steps"></a><a name="next-steps"></a>Étapes suivantes

Cet article présente le contenu du modèle ASIM (Advanced SIEM Information Model).

Pour plus d'informations, consultez les pages suivantes :

- Regardez le [webinaire de formation approfondie sur la normalisation des analyseurs et le contenu normalisé Microsoft Sentinel](https://www.youtube.com/watch?v=zaqblyjQW6k) ou passez en revue les [diapositives](https://1drv.ms/b/s!AnEPjr8tHcNmjGtoRPQ2XYe3wQDz?e=R3dWeM)
- [Présentation du modèle ASIM](normalization.md)
- [Schémas relatifs au modèle ASIM](normalization-about-schemas.md)
- [Analyseurs de modèle ASIM](normalization-about-parsers.md)