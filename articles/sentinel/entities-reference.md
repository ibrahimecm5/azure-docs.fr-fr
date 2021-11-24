---
title: Informations de référence sur les types d’entités Microsoft Sentinel | Microsoft Docs
description: Cet article présente les types d’entités Microsoft Sentinel et les identificateurs requis pour ceux-ci.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: c5917011bd937e88811c9f0cffaed7f8c1c61f00
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521203"
---
# <a name="microsoft-sentinel-entity-types-reference"></a>Informations de référence sur les types d’entités Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

## <a name="entity-types-and-identifiers"></a>Types d'entités et identificateurs

Le tableau suivant présente les **types d’entités** actuellement disponibles pour le mappage dans Microsoft Sentinel, ainsi que les **attributs** disponibles en tant qu’**identificateurs** pour chaque type d’entité ; ceux-ci apparaissent dans la liste déroulante **Identificateurs** de la section [Mappage d’entités](map-data-fields-to-entities.md) de l’[Assistant de règle analytique](detect-threats-custom.md).

Chacun des identificateurs de la colonne **Identificateurs requis** est nécessaire pour identifier l'entité correspondante. Cela dit, un identifiant requis peut ne pas suffire à lui seul à fournir une identification *unique*. Plus le nombre d'identifiants utilisés est élevé, plus la probabilité d'identification unique est grande. Vous pouvez utiliser trois identificateurs par mappage d'entités.

Pour des résultats optimaux (et une identification unique garantie), utilisez de préférence les identificateurs de la colonne **Identificateurs les plus forts**. L'utilisation de plusieurs identificateurs forts permet d'établir une corrélation entre les identificateurs forts issus de sources de données et de schémas différents. Cela permet à Microsoft Sentinel de fournir des informations plus complètes sur une entité donnée.

| Type d'entité | Identificateurs | Identificateurs requis | Identificateurs les plus forts |
| - | - | - | - |
| [**Compte d’utilisateur**](#user-account)<br>*(Account)* | Nom<br>FullName<br>NTDomain<br>DNSDomain<br>UPNSuffix<br>SID<br>AadTenantId<br>AadUserId<br>PUID<br>IsDomainJoined<br>DisplayName<br>ObjectGuid | FullName<br>SID<br>Nom<br>AadUserId<br>PUID<br>ObjectGuid | Name + NTDomain<br>Name + UPNSuffix<br>AADUserId<br>SID |
| [**Host**](#host) | DnsDomain<br>NTDomain<br>HostName<br>FullName<br>NetBiosName<br>AzureID<br>OMSAgentID<br>OSFamily<br>OSVersion<br>IsDomainJoined | FullName<br>HostName<br>NetBiosName<br>AzureID<br>OMSAgentID | HostName + NTDomain<br>HostName + DnsDomain<br>NetBiosName + NTDomain<br>NetBiosName + DnsDomain<br>AzureID<br>OMSAgentID |
| [**Adresse IP**](#ip-address)<br>*(IP)* | Adresse | Adresse | |
| [**Programme malveillant**](#malware) | Nom<br>Category | Nom | |
| [**Fichier**](#file) | Répertoire<br>Nom | Nom | |
| [**Processus**](#process) | ProcessId<br>CommandLine<br>ElevationToken<br>CreationTimeUtc | CommandLine<br>ProcessId | |
| [**Application cloud**](#cloud-application)<br>*(CloudApplication)* | AppId<br>Nom<br>InstanceName | AppId<br>Nom | |
| [**Nom de domaine**](#domain-name)<br>*(DNS)* | DomainName | DomainName | |
| [**Ressource Azure**](#azure-resource) | ResourceId | ResourceId | |
| [**Hachage du fichier**](#file-hash)<br>*(FileHash)* | Algorithm<br>Valeur | Algorithm + Value | |
| [**Clé de Registre**](#registry-key) | Hive<br>Clé : | Hive<br>Clé : | Hive + Key |
| [**Valeur de Registre**](#registry-value) | Nom<br>Valeur<br>ValueType | Nom | |
| [**Groupe de sécurité**](#security-group) | DistinguishedName<br>SID<br>ObjectGuid | DistinguishedName<br>SID<br>ObjectGuid | |
| [**URL**](#url) | Url | Url | |
| [**Appareil IoT**](#iot-device) | IoTHub<br>DeviceId<br>DeviceName<br>IoTSecurityAgentId<br>DeviceType<br>Source<br>SourceRef<br>Fabricant<br>Modéliser<br>OperatingSystem<br>IpAddress<br>MacAddress<br>Protocoles<br>SerialNumber | IoTHub<br>deviceId | IoTHub + DeviceId |
| [**Mailbox**](#mailbox) | MailboxPrimaryAddress<br>DisplayName<br>Upn<br>ExternalDirectoryObjectId<br>RiskLevel | MailboxPrimaryAddress | |
| [**Cluster de messagerie**](#mail-cluster) | NetworkMessageIds<br>CountByDeliveryStatus<br>CountByThreatType<br>CountByProtectionStatus<br>Menaces<br>Requête<br>QueryTime<br>MailCount<br>IsVolumeAnomaly<br>Source<br>ClusterSourceIdentifier<br>ClusterSourceType<br>ClusterQueryStartTime<br>ClusterQueryEndTime<br>ClusterGroup | Requête<br>Source | Query + Source |
| [**Message électronique**](#mail-message) | Recipient<br>Urls<br>Menaces<br>Expéditeur<br>P1Sender<br>P1SenderDisplayName<br>P1SenderDomain<br>SenderIP<br>P2Sender<br>P2SenderDisplayName<br>P2SenderDomain<br>ReceivedDate<br>NetworkMessageId<br>InternetMessageId<br>Objet<br>BodyFingerprintBin1<br>BodyFingerprintBin2<br>BodyFingerprintBin3<br>BodyFingerprintBin4<br>BodyFingerprintBin5<br>AntispamDirection<br>DeliveryAction<br>DeliveryLocation<br>Langage<br>ThreatDetectionMethods | NetworkMessageId<br>Recipient | NetworkMessageId + Recipient |
| [**E-mail d'envoi**](#submission-mail) | SubmissionId<br>SubmissionDate<br>Expéditeur<br>NetworkMessageId<br>Timestamp<br>Recipient<br>Expéditeur<br>SenderIp<br>Objet<br>ReportType | SubmissionId<br>NetworkMessageId<br>Recipient<br>Expéditeur |  |
|

## <a name="entity-type-schemas"></a>Schémas des types d'entités

Vous trouverez ci-dessous une présentation plus détaillée des schémas complets de chaque type d'entité. Beaucoup de ces schémas comportent des liens vers d'autres types d'entités ; par exemple, le schéma Compte d'utilisateur comporte un lien vers le type d'entité Hôte, car l'un des attributs d'un compte d'utilisateur correspond à l'hôte sur lequel il est défini. Ces entités liées en externe ne peuvent pas être utilisées en tant qu'identificateurs pour le mappage des entités, mais elles offrent une image complète des entités sur les pages de celles-ci et sur le graphique d'investigation.

> [!NOTE]
> Le point d'interrogation qui suit certaines valeurs dans la colonne **Type** indique que le champ peut accepter la valeur Null.

## <a name="user-account"></a>Compte d’utilisateur

*Nom de l'entité : Account*

| Champ | Type | Description |
| ----- | ---- | ----------- |
| Type | String | « account » |
| Nom | String | Nom du compte. Ce champ ne doit contenir que le nom, sans domaine. |
| *FullName* | *N/A* | *Ne fait pas partie du schéma, inclus à des fins de compatibilité descendante avec l'ancienne version du mappage d'entités.*
| NTDomain | String | Nom de domaine NETBIOS tel qu'il apparaît dans le format d'alerte -domaine\nom d'utilisateur. Exemples : Finance, AUTORITÉ NT |
| DnsDomain | String | Nom DNS de domaine complet. Exemple : finance.contoso.com |
| UPNSuffix | String | Suffixe du nom de l'utilisateur principal du compte. Dans certains cas, il s'agit également du nom de domaine. Exemple : contoso.com |
| Host | Entité | Hôte sur lequel figure le compte, s'il s'agit d'un compte local. |
| SID | String | Identificateur de sécurité du compte, par exemple S-1-5-18. |
| AadTenantId | Guid? | ID du locataire Azure AD, s'il est connu. |
| AadUserId | Guid? | ID de l'objet compte Azure AD, s'il est connu. |
| PUID | Guid? | ID de l'utilisateur Azure AD Passport, s'il est connu. |
| IsDomainJoined | Bool? | Détermine s'il s’agit d'un compte de domaine. |
| DisplayName | String | Nom d'affichage du compte. |
| ObjectGuid | Guid? | L'attribut objectGUID est un attribut à valeur unique qui constitue l'identificateur unique de l'objet, attribué par Active Directory. |
|

Identificateurs forts d'une entité Compte :

- Name + UPNSuffix
- AadUserId
- Sid + Host (requis pour les SID des comptes intégrés)
- Sid (sauf pour les SID des comptes intégrés)
- Name + NTDomain (sauf si NTDomain est un domaine intégré, par exemple « Groupe de travail »)
- Name + Host (si NTDomain est un domaine intégré, par exemple « Groupe de travail »)
- Name + DnsDomain
- PUID
- ObjectGuid

Identificateurs faibles d'une entité Compte :

- Nom

## <a name="host"></a>Host

| Champ | Type | Description |
| ----- | ---- | ----------- |
| Type | String | « host » |
| DnsDomain | String | Domaine DNS auquel cet hôte appartient. Doit contenir le suffixe DNS complet du domaine, s'il est connu. |
| NTDomain | String | Domaine NT auquel cet hôte appartient. |
| HostName | String | Nom d'hôte sans suffixe de domaine. |
| *FullName* | *N/A* | *Ne fait pas partie du schéma, inclus à des fins de compatibilité descendante avec l'ancienne version du mappage d'entités.*
| NetBiosName | String | Nom d'hôte (antérieur à Windows 2000). |
| IoTDevice | Entité | Entité Appareil IoT (si cet hôte représente un appareil IoT). |
| AzureID | String | ID de ressource Azure de la machine virtuelle, s'il est connu. |
| OMSAgentID | String | ID de l'agent OMS, si celui-ci est installé sur l'hôte. |
| OSFamily | Enum? | Une des valeurs suivantes : <li>Linux<li>Windows<li>Android<li>IOS |
| OSVersion | String | Représentation en texte libre du système d'exploitation.<br>Ce champ est destiné à contenir des versions spécifiques plus précises qu'avec OSFamily, ou des valeurs futures non prises en charge par l'énumération OSFamily. |
| IsDomainJoined | Bool | Détermine si cet hôte appartient à un domaine. |
|

Identificateurs forts d'une entité Hôte :
- HostName + NTDomain
- HostName + DnsDomain
- NetBiosName + NTDomain
- NetBiosName + DnsDomain
- AzureID
- OMSAgentID
- IoTDevice (non pris en charge pour le mappage d'entités)

Identificateurs faibles d'une entité Hôte :
- HostName
- NetBiosName

## <a name="ip-address"></a>Adresse IP

*Nom de l'entité : IP*

| Champ | Type | Description |
| ----- | ---- | ----------- |
| Type | String | « ip » |
| Adresse | String | Adresse IP sous forme de chaîne, par exemple 127.0.0.1 (dans IPv4 ou IPv6). |
| Emplacement | GeoLocation | Contexte de géolocalisation joint à l'entité IP. <br><br>Pour plus d’informations, consultez également [Enrichir des entités dans Microsoft Sentinel avec des données de géolocalisation via l’API REST (préversion publique)](geolocation-data-api.md). |
|

Identificateurs forts d'une entité IP :
- Adresse

## <a name="malware"></a>Programme malveillant

| Champ | Type | Description |
| ----- | ---- | ----------- |
| Type | String | « malware » |
| Nom | String | Nom du programme malveillant tel qu'indiqué par le fournisseur, par exemple `Win32/Toga!rfn`. |
| Category | String | Catégorie du programme malveillant telle qu'indiquée par le fournisseur, par exemple Cheval de Troie. |
| Fichiers | Liste \<Entity> | Liste des entités Fichier liées sur lesquelles le programme malveillant a été trouvé. Les entités Fichier peuvent être incluses ou fournies en tant que référence.<br>Pour plus d'informations sur la structure, consultez l'entité Fichier. |
| Processus | Liste \<Entity> | Liste des entités Processus liées sur lesquelles le programme malveillant a été trouvé. Souvent utilisée lorsque l'alerte est déclenchée sur une activité sans fichier.<br>Pour plus d'informations sur la structure, consultez l'entité [Processus](#process). |
|

Identificateurs forts d'une entité Programme malveillant :

- Name + Category

## <a name="file"></a>Fichier

| Champ | Type | Description |
| ----- | ---- | ----------- |
| Type | String | « file » |
| Répertoire | String | Chemin d'accès complet au fichier. |
| Nom | String | Nom du fichier, sans chemin d'accès (certaines alertes peuvent ne pas inclure de chemin d'accès). |
| Host | Entité | Hôte sur lequel le fichier a été stocké. |
| FileHashes | List&lt;Entity&gt; | Hachages associés à ce fichier. |
|

Identificateurs forts d'une entité Fichier :
- Name + Directory
- Name + FileHash
- Name + Directory + FileHash

## <a name="process"></a>Processus

| Champ | Type | Description |
| ----- | ---- | ----------- |
| Type | String | « process » |
| ProcessId | String | ID de processus. |
| CommandLine | String | Ligne de commande utilisée pour créer le processus. |
| ElevationToken | Enum? | Jeton d'élévation associé au processus.<br>Valeurs possibles :<li>TokenElevationTypeDefault<li>TokenElevationTypeFull<li>TokenElevationTypeLimited |
| CreationTimeUtc | DateTime? | Moment auquel le processus a démarré. |
| ImageFile | Entity (File) | L'entité Fichier peut être incluse ou fournie en tant que référence.<br>Pour plus d'informations sur la structure, consultez l'entité Fichier. |
| Compte | Entité | Compte exécutant les processus.<br>L'entité [Compte](#user-account) peut être incluse ou fournie en tant que référence.<br>Pour plus d'informations sur la structure, consultez l'entité [Compte](#user-account). |
| ParentProcess | Entity (Process) | Entité du processus parent. <br>Peut contenir des données partielles (PID uniquement). |
| Host | Entité | Hôte sur lequel le processus a été exécuté. |
| LogonSession | Entity (HostLogonSession) | Session dans laquelle le processus a été exécuté. |
|

Identificateurs forts d'une entité Processus :

- Host + ProcessId + CreationTimeUtc
- Host + ParentProcessId + CreationTimeUtc + CommandLine
- Host + ProcessId + CreationTimeUtc + ImageFile
- Host + ProcessId + CreationTimeUtc + ImageFile.FileHash

Identificateurs faibles d'une entité Processus :

- ProcessId + CreationTimeUtc + CommandLine (et non Host)
- ProcessId + CreationTimeUtc + ImageFile (et non Host)

## <a name="cloud-application"></a>Application cloud

*Nom de l'entité : CloudApplication*

| Champ | Type | Description |
| ----- | ---- | ----------- |
| Type | String | « cloud-application » |
| AppId | Int | Identificateur technique de l'application. Il doit s'agir de l'une des valeurs définies dans la liste des [identificateurs d'application cloud](#cloud-application-identifiers). La valeur du champ AppId est facultative. |
| Nom | String | Nom de l'application cloud associée. La valeur du nom de l'application est facultative. |
| InstanceName | String | Nom de l'instance tel que défini par l'utilisateur de l'application cloud. Souvent utilisé pour distinguer les différentes applications du même type d'un client. |
|

Identificateurs forts d'une entité Application cloud :
 - AppId (sans InstanceName)
 - Name (sans InstanceName)
 - AppId + InstanceName
 - Name + InstanceName

## <a name="domain-name"></a>Nom de domaine

*Nom de l'entité : DNS*

| Champ | Type | Description |
| ----- | ---- | ----------- |
| Type | String | « dns » |
| DomainName | String | Nom de l'enregistrement DNS associé à l'alerte. |
| IpAddress | List&lt;Entity (IP)&gt; | Entités correspondant aux adresses IP résolues. |
| DnsServerIp | Entity (IP) | Entité représentant le serveur DNS qui résout la requête. |
| HostIpAddress | Entity (IP) | Entité représentant le client de la requête DNS. |
|

Identificateurs forts d'une entité DNS :
- DomainName + DnsServerIp + HostIpAddress

Identificateurs faibles d'une entité DNS :
- DomainName + HostIpAddress

## <a name="azure-resource"></a>Ressource Azure

| Champ | Type | Description |
| ----- | ---- | ----------- |
| Type | String | « azure-resource » |
| ResourceId | String | ID de ressource Azure de la ressource. |
| SubscriptionId | String | ID d’abonnement de la ressource. |
| TryGetResourceGroup | Bool | Valeur du groupe de ressources, le cas échéant. |
| TryGetProvider | Bool | Valeur du fournisseur, le cas échéant. |
| TryGetName | Bool | Valeur du nom, le cas échéant. |
|

Identificateurs forts d'une entité Ressource Azure :
- ResourceId

## <a name="file-hash"></a>Hachage du fichier

*Nom de l'entité : FileHash*

| Champ | Type | Description |
| ----- | ---- | ----------- |
| Type | String | « filehash » |
| Algorithm | Énumération | Type d'algorithme de hachage. Valeurs possibles :<li>Unknown<li>MD5<li>SHA1<li>SHA256<li>SHA256AC |
| Valeur | String | Valeur de hachage. |
|

Identificateurs forts d'une entité Hachage de fichier :
- Algorithm + Value

## <a name="registry-key"></a>Clé de Registre

*Nom de l'entité : RegistryKey*

| Champ | Type | Description |
| ----- | ---- | ----------- |
| Type | String | « registry-key » |
| Hive | Enum? | Une des valeurs suivantes :<li>HKEY_LOCAL_MACHINE<li>HKEY_CLASSES_ROOT<li>HKEY_CURRENT_CONFIG<li>HKEY_USERS<li>HKEY_CURRENT_USER_LOCAL_SETTINGS<li>HKEY_PERFORMANCE_DATA<li>HKEY_PERFORMANCE_NLSTEXT<li>HKEY_PERFORMANCE_TEXT<li>HKEY_A<li>HKEY_CURRENT_USER |
| Clé : | String | Chemin de la clé de Registre. |
|

Identificateurs forts d'une entité Clé de Registre :
- Hive + Key

## <a name="registry-value"></a>Valeur de Registre

*Nom de l'entité : RegistryValue*

| Champ | Type | Description |
| ----- | ---- | ----------- |
| Type | String | « registry-value » |
| Clé : | Entity (RegistryKey) | Entité Clé de Registre. |
| Nom | String | Nom de la valeur de Registre. |
| Valeur | String | Représentation sous forme de chaîne des données de la valeur. |
| ValueType | Enum? | Une des valeurs suivantes :<li>String<li>Binary<li>DWord<li>Qword<li>MultiString<li>ExpandString<li>Aucun<li>Unknown<br>Les valeurs doivent être conformes à l'énumération Microsoft.Win32.RegistryValueKind. |
|

Identificateurs forts d'une entité Valeur de Registre :
- Key + Name

Identificateurs faibles d'une entité Valeur de Registre :
- Name (sans Key)

## <a name="security-group"></a>Groupe de sécurité

*Nom de l’entité : SecurityGroup*

| Champ | Type | Description |
| ----- | ---- | ----------- |
| Type | String | « security-group » |
| DistinguishedName | String | Nom distinctif du groupe. |
| SID | String | L'attribut SID est un attribut à valeur unique qui spécifie l'identificateur de sécurité (SID) du groupe. |
| ObjectGuid | Guid? | L'attribut objectGUID est un attribut à valeur unique qui constitue l'identificateur unique de l'objet, attribué par Active Directory. |
|

Identificateurs forts d'une entité Groupe de sécurité :
 - DistinguishedName
 - SID
 - ObjectGuid

## <a name="url"></a>URL

| Champ | Type | Description |
| ----- | ---- | ----------- |
| Type | String | « url » |
| Url | Uri | URL complète vers laquelle pointe l'entité. |
|

Identificateurs forts d'une entité URL :
- URL (lorsqu'il s'agit d'une URL absolue)

Identificateurs faibles d'une entité URL :
- URL (lorsqu'il s'agit d'une URL relative)

## <a name="iot-device"></a>Appareil IoT

*Nom de l'entité : IoTDevice*

| Champ | Type | Description |
| ----- | ---- | ----------- |
| Type | String | « iotdevice » |
| IoTHub | Entity (AzureResource) | Entité AzureResource représentant l'instance IoT Hub à laquelle appartient l'appareil. |
| deviceId | String | ID de l'appareil dans le contexte d'IoT Hub. |
| DeviceName | String | Nom convivial de l'appareil. |
| IoTSecurityAgentId | Guid? | ID de l'agent *Defender pour IoT* exécuté sur l'appareil. |
| DeviceType | String | Type d'appareil (« capteur de température », « congélateur », « éolienne », etc.). |
| Source | String | Source (Microsoft/Fournisseur) de l'entité d'appareil. |
| SourceRef | Entity (Url) | Référence URL à l'élément source où l'appareil est géré. |
| Fabricant | String | Fabricant de l’appareil |
| Modéliser | String | Modèle de l’appareil |
| OperatingSystem | String | Système d'exploitation que l'appareil utilise. |
| IpAddress | Entity (IP) | Adresse IP actuelle de l'appareil. |
| MacAddress | String | Adresse MAC de l’appareil. |
| Protocoles | List&lt;String&gt; | Liste des protocoles pris en charge par l'appareil. |
| SerialNumber | String | Numéro de série de l’appareil. |
|

Identificateurs forts d'une entité Appareil IoT :
- IoTHub + DeviceId

Identificateurs faibles d'une entité Appareil IoT :
- DeviceId (sans IoTHub)

## <a name="mailbox"></a>Mailbox

| Champ | Type | Description |
| ----- | ---- | ----------- |
| Type | String | « mailbox » |
| MailboxPrimaryAddress | String | Adresse principale de la boîte aux lettres. |
| DisplayName | String | Nom d'affichage de la boîte aux lettres. |
| Upn | String | UPN de la boîte aux lettres. |
| RiskLevel | Enum? | Niveau de risque associé à cette boîte aux lettres. Valeurs possibles :<li>Aucun<li>Faible<li>Moyenne<li>Élevé |
| ExternalDirectoryObjectId | Guid? | Identificateur AzureAD de la boîte aux lettres. Semblable à AadUserId pour l'entité Compte, mais cette propriété est spécifique à l'objet de boîte aux lettres du côté Office. |
|

Identificateurs forts d'une entité Boîte aux lettres :
- MailboxPrimaryAddress

## <a name="mail-cluster"></a>Cluster de messagerie

*Nom de l'entité : MailCluster*

> [!NOTE]
> **Microsoft Defender pour Office 365** était auparavant connu sous le nom d'Office 365 Advanced Threat Protection (O365 ATP).

| Champ | Type | Description |
| ----- | ---- | ----------- |
| Type | String | « mail-cluster » |
| NetworkMessageIds | IList&lt;String&gt; | ID des messages électroniques appartenant au cluster de courrier. |
| CountByDeliveryStatus | IDictionary&lt;String,Int&gt; | Nombre de messages électroniques par représentation sous forme de chaîne DeliveryStatus. |
| CountByThreatType | IDictionary&lt;String,Int&gt; | Nombre de messages électroniques par représentation sous forme de chaîne ThreatType. |
| CountByProtectionStatus | IDictionary&lt;String,long&gt; | Nombre de messages électroniques par état de protection contre les menaces. |
| Menaces | IList&lt;String&gt; | Menaces associées aux messages électroniques appartenant au cluster de courrier. |
| Requête | String | Requête utilisée pour identifier les messages du cluster de courrier. |
| QueryTime | DateTime? | Date/heure de la requête. |
| MailCount | Int? | Nombre de messages électroniques appartenant au cluster de courrier. |
| IsVolumeAnomaly | Bool? | Détermine s'il s'agit d'un cluster de courrier avec anomalie de volume. |
| Source | String | Source du cluster de courrier (la source par défaut est « O365 ATP »). |
| ClusterSourceIdentifier | String | ID de message réseau du courrier qui est la source de ce cluster de courrier. |
| ClusterSourceType | String | Type de source du cluster de courrier. Correspond au paramètre MailClusterSourceType de Microsoft Defender pour Office 365 (voir la remarque ci-dessus). |
| ClusterQueryStartTime | DateTime? | Heure de début du cluster - utilisée comme heure de début pour la requête de comptage du cluster. Les dates correspondent généralement à l'heure de fin moins le paramètre DaysToLookBack de Microsoft Defender pour Office 365 (voir la remarque ci-dessus). |
| ClusterQueryEndTime | DateTime? | Heure de fin du cluster - utilisée comme heure de fin pour la requête de comptage du cluster. En général, il s'agit de l'heure de réception des données de courrier. |
| ClusterGroup | String | Correspond à la clé de requête Kusto utilisée sur Microsoft Defender pour Office 365 (voir la remarque ci-dessus). |
|

Identificateurs forts d'une entité Cluster de courrier :
- Query + Source

## <a name="mail-message"></a>Message électronique

*Nom de l'entité : MailMessage*

| Champ | Type | Description |
| ----- | ---- | ----------- |
| Type | String | « mail-message » |
| Fichiers | IList&lt;File&gt; | Entités Fichier des pièces jointes de ce message électronique. |
| Recipient | String | Destinataire de ce message électronique. Lorsqu'il y a plusieurs destinataires, le message est copié et chaque copie possède un destinataire. |
| Urls | IList&lt;String&gt; | URL contenues dans ce message électronique. |
| Menaces | IList&lt;String&gt; | Menaces contenues dans ce message électronique. |
| Expéditeur | String | Adresse e-mail de l’expéditeur. |
| P1Sender | String | Adresse e-mail de l'utilisateur (délégué) qui a envoyé ce courrier « au nom de l'utilisateur P2 (principal) ». Si l'e-mail n'est pas envoyé par le délégué, cette valeur est égale à P2Sender. |
| P1SenderDisplayName | String | Nom d'affichage de l'utilisateur (délégué) qui a envoyé ce courrier « au nom de l'utilisateur P2 (principal) ». Représenté dans l'en-tête de l'e-mail par la propriété « OnbehalfofSenderDisplayName ». |
| P1SenderDomain | String | Domaine de courrier de l'utilisateur (délégué) qui a envoyé ce courrier « au nom de l'utilisateur P2 (principal) ». Si l'e-mail n'a pas été envoyé par le délégué, cette valeur est égale à P2SenderDomain. |
| P2Sender | String | Adresse e-mail de l'utilisateur (principal) au nom duquel cet e-mail a été envoyé. |
| P2SenderDisplayName | String | Nom d'affichage de l'utilisateur (principal) au nom duquel cet e-mail a été envoyé. Si l'e-mail n'a pas été envoyé par le délégué, il s'agit du nom d'affichage de l'expéditeur. |
| P2SenderDomain | String | Domaine de courrier de l'utilisateur (principal) au nom duquel cet e-mail a été envoyé. Si l'e-mail n'a pas été envoyé par le délégué, il s'agit du domaine de l'expéditeur. |
| SenderIP | String | Adresse IP de l'expéditeur. |
| ReceivedDate | DateTime | Date de réception de ce message. |
| NetworkMessageId | Guid? | ID de message réseau de ce message électronique. |
| InternetMessageId | String | ID de message Internet de ce message électronique. |
| Objet | String | Objet de ce message électronique. |
| BodyFingerprintBin1<br>BodyFingerprintBin2<br>BodyFingerprintBin3<br>BodyFingerprintBin4<br>BodyFingerprintBin5 | UInt? | Utilisé par Microsoft Defender pour Office 365 afin de rechercher des messages électroniques correspondants ou similaires. |
| AntispamDirection | Enum? | Directionnalité de ce message électronique. Valeurs possibles :<li>Unknown<li>Trafic entrant<li>Règle de trafic sortant<li>Intraorg (interne) |
| DeliveryAction | Enum? | Action de remise de ce message électronique. Valeurs possibles :<li>Unknown<li>DeliveredAsSpam<li>Delivered (Livrée)<li>Bloqué<li>Remplacé |
| DeliveryLocation | Enum? | Emplacement de remise de ce message électronique. Valeurs possibles :<li>Unknown<li>Inbox<li>JunkFolder<li>DeletedFolder<li>Mise en quarantaine<li>Externe<li>Échec<li>Supprimé<li>Forwarded |
| Langage | String | Langue dans laquelle le contenu du courrier est rédigé. |
| ThreatDetectionMethods | IList&lt;String&gt; | Liste des méthodes de détection des menaces appliquées à ce courrier. |
|

Identificateurs forts d'une entité Message électronique :
- NetworkMessageId + Recipient

## <a name="submission-mail"></a>E-mail d'envoi

*Nom de l'entité : SubmissionMail*

| Champ | Type | Description |
| ----- | ---- | ----------- |
| Type | String | « SubmissionMail » |
| SubmissionId | Guid? | ID de l'envoi. |
| SubmissionDate | DateTime? | Date et heure indiquées pour cet envoi. |
| Expéditeur | String | Adresse e-mail de l'expéditeur. |
| NetworkMessageId | Guid? | ID de message réseau de l'e-mail auquel l'envoi appartient. |
| Timestamp | DateTime? | Horodatage de réception du message (courrier). |
| Recipient | String | Destinataire du courrier. |
| Expéditeur | String | Expéditeur du courrier. |
| SenderIp | String | Adresse IP de l'expéditeur. |
| Objet | String | Objet du courrier envoyé. |
| ReportType | String | Type d'envoi pour l'instance donnée (Junk, Phish, Malware ou NotJunk). |
|

Identificateurs forts d'une entité SubmissionMail :
- SubmissionId, Submitter, NetworkMessageId, Recipient

## <a name="cloud-application-identifiers"></a>Identificateurs des applications cloud

La liste suivante définit les identificateurs des applications cloud connues. La valeur ID de l'application est utilisée comme identificateur d'entité de l'[application cloud](#cloud-application).

|ID de l’application|Nom|
|------|----|
|10026|DocuSign|
|10 395|Anaplan|
|10489|Box|
|10549|Cisco Webex|
|10618|Atlassian|
|10915|cornerstone ondemand|
|10921|Zendesk|
|10980|Okta|
|11042|Jive Software|
|11114|Salesforce|
|11161|Office 365|
|11162|Microsoft OneNote Online|
|11394|Microsoft Online Services|
|11522|Yammer|
|11599|Amazon Web Services|
|11627|Dropbox|
|11713|Expensify|
|11770|G Suite|
|12005|SuccessFactors|
|12260|Microsoft Azure|
|12275|Workday|
|13843|LivePerson|
|13979|Concur|
|14509|ServiceNow|
|15570|Tableau|
|15600|Microsoft OneDrive Entreprise|
|15782|Citrix ShareFile|
|17152|Amazon|
|17865|Ariba Inc|
|18432|Zscaler|
|19688|Xactly|
|20595|Microsoft Defender for Cloud Apps|
|20892|Microsoft SharePoint Online|
|20893|Microsoft Exchange Online|
|20940|Active Directory|
|20941|Adallom CPanel|
|22110|Google Cloud Platform|
|22930|Gmail|
|23004|Autodesk Fusion Lifecycle|
|23043|Slack|
|23233|Microsoft Office Online|
|25275|Microsoft Skype Entreprise|
|25988|Google Docs|
|26055|Centre d'administration Microsoft Office 365|
|26060|OPSWAT Gears|
|26061|Microsoft Word Online|
|26062|Microsoft PowerPoint Online|
|26063|Microsoft Excel Online|
|26069|Google Drive|
|26206|Workiva|
|26311|Microsoft Dynamics|
|26318|Microsoft Azure AD|
|26320|Microsoft Office Sway|
|26321|Microsoft Delve|
|26324|Microsoft Power BI|
|27548|Microsoft Forms|
|27592|Microsoft Flow|
|27593|Microsoft PowerApps|
|28353|Workplace by Facebook|
|28373|CAS Proxy Emulator|
|28375|Microsoft Teams|
|32780|Microsoft Dynamics 365|
|33626|Google|
|34127|Microsoft AppSource|
|34667|HighQ|
|35395|Microsoft Dynamics Talent|
|

## <a name="next-steps"></a>Étapes suivantes

Ce document vous a permis de vous familiariser avec la structure, les identificateurs et les schémas des entités dans Microsoft Sentinel.

Apprenez-en davantage sur les [entités](entities-in-azure-sentinel.md) et le [mappage des entités](map-data-fields-to-entities.md). 
