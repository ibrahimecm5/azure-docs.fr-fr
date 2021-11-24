---
title: Résolution les problèmes liés aux appareils hybrides joints au Répertoire actif Azure
description: Cet article vous aide à résoudre les problèmes liés aux appareils hybrides Windows&nbsp; 10 et Windows Server 2016 joints au Répertoire actif Azure.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: f5320812be623437099ed0ab587dbbe3d4abe6ef
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158760"
---
# <a name="troubleshoot-hybrid-azure-ad-joined-devices"></a>Résoudre les problèmes liés aux appareils hybrides joints Azure AD

Cet article fournit des conseils de dépannage pour vous aider à résoudre les problèmes potentiels liés aux appareils qui exécutent Windows &nbsp; 10 ou Windows Server 2016.

La jointure du Répertoire actif hybride Azure (Azure AD) prend en charge la mise à jour Windows&nbsp; du 10 novembre 2015 et versions ultérieures.

Pour résoudre les problèmes liés à d’autres clients Windows, consultez [Résoudre les problèmes liés aux appareils hybrides de niveau inférieur joints Azure AD](troubleshoot-hybrid-join-windows-legacy.md).

Cet article suppose que vous avez [configuré des appareils hybrides joints Azure AD](hybrid-azuread-join-plan.md) pour prendre en charge les scénarios suivants :

- Accès conditionnel basé sur les appareils
- [Enterprise State Roaming](./enterprise-state-roaming-overview.md)
- [Windows Hello Entreprise](/windows/security/identity-protection/hello-for-business/hello-identity-verification)


> [!NOTE] 
> Pour résoudre les problèmes d’inscription d’appareils les plus courants, utilisez [Outil de résolution des problèmes d’inscription des appareils](https://aka.ms/DSRegTool).


## <a name="troubleshoot-join-failures"></a>Résoudre les problèmes d’échecs de jointure

### <a name="step-1-retrieve-the-join-status"></a>Étape 1 : Récupérer l’état de jonction

1. Ouvrez une fenêtre d’invite de commandes en tant qu’administrateur.
1. Tapez `dsregcmd /status`.

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+

    AzureAdJoined: YES
 EnterpriseJoined: NO
         DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7
       Thumbprint: B753A6679CE720451921302CA873794D94C6204A
   KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9
      KeyProvider: Microsoft Platform Crypto Provider
     TpmProtected: YES
     KeySignTest: : MUST Run elevated to test.
              Idp: login.windows.net
         TenantId: 72b988bf-xxxx-xxxx-xxxx-2d7cd011xxxx
       TenantName: Contoso
      AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize
   AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token
           MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
        MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
  dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance
      SettingsUrl: eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ==
   JoinSrvVersion: 1.0
       JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/
        JoinSrvId: urn:ms-drs:enterpriseregistration.windows.net
    KeySrvVersion: 1.0
        KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/
         KeySrvId: urn:ms-drs:enterpriseregistration.windows.net
     DomainJoined: YES
       DomainName: CONTOSO

+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

             NgcSet: YES
           NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
    WorkplaceJoined: NO
      WamDefaultSet: YES
WamDefaultAuthority: organizations
       WamDefaultId: https://login.microsoft.com
     WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)
         AzureAdPrt: YES
```

### <a name="step-2-evaluate-the-join-status"></a>Étape 2 : Évaluer l’état de jonction

Passez en revue les champs du tableau suivant et assurez-vous qu’ils ont les valeurs attendues :

| Champ | Valeur attendue | Description |
| --- | --- | --- |
| DomainJoined | YES | Ce champ indique si l’appareil est joint à un Répertoire actif local ou non. <br><br>Si la valeur est *NON*, l’appareil ne peut pas effectuer de jonction hybride Azure AD. |
| WorkplaceJoined | Non | Ce champ indique si l’appareil est inscrit auprès d’Azure AD mais en tant qu’appareil personnel (avec la mention *Joint à l’espace de travail*). Cette valeur doit être *NON* pour un ordinateur joint à un domaine qui est également hybride et joint à Azure AD. <br><br>Si la valeur est *OUI*, un compte professionnel ou scolaire a été ajouté avant l’achèvement d’une jonction hybride Azure AD. Dans ce cas, le compte est ignoré lorsque vous utilisez Windows &nbsp; 10 version 1607 ou ultérieure. |
| AzureAdJoined | YES | Ce champ indique si l’appareil est joint. La valeur sera *OUI* si l’appareil est un appareil joint à Azure AD ou un appareil hybride joint à Azure AD. <br><br>Si la valeur est *NON*, la jointure à Azure AD n’est pas encore terminée. |
|  |  |

Passez aux étapes suivantes pour poursuivre le dépannage.

### <a name="step-3-find-the-phase-in-which-the-join-failed-and-the-error-code"></a>Étape 3 : Rechercher la phase à laquelle la jointure a échoué ainsi que le code d’erreur

**Pour Windows &nbsp; 10 version 1803 ou ultérieure**

Recherchez la sous-section « Inscription précédente » dans la section « Données de diagnostic » de la sortie de l’état de la jointure. Cette section s’affiche uniquement si l’appareil est joint à un domaine et ne peut pas être hybride Azure AD.

Le champ « Phase d’erreur » indique la phase de l’échec de la jointure, et « Client ErrorCode » désigne le code d’erreur de l’opération de jointure.

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-xxxx-xxxx-xxxx-94ae875d5245) isn't found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

**Pour les versions antérieures de Windows &nbsp; 10**

Servez-vous des journaux de l’Observateur d’événements pour identifier la phase et le code d’erreur des échecs de jointure.

1. Dans l’Observateur d’événements, ouvrez les journaux des événements d’**Inscription des appareils de l’utilisateur** . Ils sont stockés dans le **Journal des Applications et des Services** > **Microsoft** > **Windows** > **Inscription des appareils de l’utilisateur**.
1. Recherchez les événements avec les ID d’événement suivants : 304, 305 et 307.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/1.png" alt-text="Capture d’écran de l’Observateur d’événements, avec l’ID d’événement 304 sélectionné, les informations affichées et le code d’erreur et la phase mis en surbrillance." border="false":::

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/2.png" alt-text="Capture d’écran de l’Observateur d’événements, avec l’ID d’événement 305 sélectionné, les informations affichées et le code d’erreur mis en surbrillance." border="false":::

### <a name="step-4-check-for-possible-causes-and-resolutions"></a>Étape 4 : Rechercher les causes et les résolutions possibles

#### <a name="pre-check-phase"></a>Phase de vérification préalable

Causes possibles de l’échec :

- L’appareil n’a pas de ligne de vue sur le contrôleur de domaine.
   - L’appareil doit se trouver sur le réseau interne ou le VPN de l’organisation avec une ligne de vue réseau sur le contrôleur de domaine du Répertoire actif (AD) local.

#### <a name="discover-phase"></a>Phase de découverte

Causes possibles de l’échec :

-  L’objet point de connexion de service est mal configuré ou ne peut pas être lu à partir du contrôleur de domaine.
   - Un objet point de connexion de service valide est requis dans la forêt du Répertoire actif à laquelle appartient l’appareil, qui pointe vers un nom de domaine vérifié dans Azure AD.
   - Pour plus d’informations, consultez la section « Configurer un point de connexion de service » du [Didacticiel : Configurer la jonction hybride du Répertoire actif Azure pour les domaines fédérés](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join).
- Échec de la connexion et de l’extraction des métadonnées de détection à partir du point de terminaison de détection.
   - L’appareil doit pouvoir accéder à `https://enterpriseregistration.windows.net`, dans le contexte du système, pour détecter les points de terminaison d’inscription et d’autorisation.
   - Si l’environnement local nécessite un proxy sortant, l’administrateur informatique doit vérifier que le compte d’ordinateur de l’appareil peut découvrir le proxy sortant et s’y authentifier en mode silencieux.
- Échec de connexion au point de terminaison de domaine de l’utilisateur et d’exécution de la détection du domaine (Windows &nbsp; 10 version 1809 et versions ultérieures uniquement).
   - L’appareil doit pouvoir accéder à `https://login.microsoftonline.com`, dans le contexte du système, pour effectuer la détection du domaine vérifié et déterminer le type de domaine (managé ou fédéré).
   - Si l’environnement local nécessite un proxy sortant, l’administrateur informatique doit vérifier que le contexte du système sur l’appareil peut détecter le proxy sortant et s’y authentifier en mode silencieux.

**Codes d’erreur courants :**

| Code d'erreur | Motif | Résolution |
| --- | --- | --- |
| **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED** (0x801c001d/-2145648611) | Impossible de lire l’objet SCP et d’obtenir les informations concernant le locataire Azure AD. | Reportez-vous à la section [Configurer un point de connexion de service](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join) . |
| **DSREG_AUTOJOIN_DISC_FAILED** (0x801c0021/-2145648607) | Échec de la détection générique. Échec de l’extraction des métadonnées de détection à partir du service de réplication de données (DRS). | Pour approfondir les recherches, recherchez la sous-erreur dans les sections suivantes. |
| **DSREG_AUTOJOIN_DISC_WAIT_TIMEOUT**  (0x801c001f/-2145648609) | L’opération est arrivée à expiration pendant la détection. | Vérifiez que `https://enterpriseregistration.windows.net` est accessible dans le contexte du système. Pour plus d’informations, consultez la section [Configuration requise pour la connectivité réseau](hybrid-azuread-join-managed-domains.md#prerequisites) . |
| **DSREG_AUTOJOIN_USERREALM_DISCOVERY_FAILED** (0x801c003d/-2145648579) | Échec de la détection de domaine générique Impossible de déterminer le type de domaine (managé/fédéré) à partir de STS. | Pour approfondir les recherches, recherchez la sous-erreur dans les sections suivantes. |
| | |

**Codes de sous-erreur courants :**

Pour rechercher le code de sous-erreur du code d’erreur de détection, utilisez l’une des méthodes suivantes.

##### <a name="windowsnbsp10-version-1803-or-later"></a>Windows &nbsp; 10 version 1803 ou ultérieure

Recherchez « test de détection DRS » dans la section « Données de diagnostic » de la sortie d’état de la jointure. Cette section s’affiche uniquement si l’appareil est joint à un domaine et ne peut pas être hybride Azure AD.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : UN-ELEVATED User
               Client Time : 2019-06-05 08:25:29.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x80072ee2]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

+----------------------------------------------------------------------+
```

##### <a name="earlier-windowsnbsp10-versions"></a>Versions antérieures Windows &nbsp; 10

Servez-vous des journaux de l’Observateur d’événements pour identifier la phase et le code d’erreur des échecs de jointure.

1. Dans l’Observateur d’événements, ouvrez les journaux des événements d’**Inscription des appareils de l’utilisateur** . Ils sont stockés dans le **Journal des Applications et des Services** > **Microsoft** > **Windows** > **Inscription des appareils de l’utilisateur**.
1. Recherchez l’ID d’événement 201.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/5.png" alt-text="Capture d’écran de l’Observateur d’événements, avec l’ID d’événement 201 sélectionné, les informations affichées et le code d’erreur mis en surbrillance." border="false":::

**Erreurs réseau** :

| Code d'erreur | Motif | Résolution |
| --- | --- | --- |
| **WININET_E_CANNOT_CONNECT** (0x80072efd/-2147012867) | La connexion avec le serveur n’a pas pu être établie. | vérifiez la connectivité réseau aux ressources Microsoft nécessaires. Pour plus d’informations, consultez [Exigences de connectivité réseau](hybrid-azuread-join-managed-domains.md#prerequisites). |
| **WININET_E_TIMEOUT** (0x80072ee2/-2147012894) | dépassement du délai d’attente général du réseau. | vérifiez la connectivité réseau aux ressources Microsoft nécessaires. Pour plus d’informations, consultez [Exigences de connectivité réseau](hybrid-azuread-join-managed-domains.md#prerequisites). |
| **WININET_E_DECODING_FAILED** (0x80072f8f/-2147012721) | la pile réseau n’a pas pu décoder la réponse du serveur. | Vérifiez que le proxy de réseau n’interfère pas avec la réponse du serveur et ne la modifie pas |
| | |


**Erreurs HTTP** :

| Code d'erreur | Motif | Résolution |
| --- | --- | --- |
| **DSREG_DISCOVERY_TENANT_NOT_FOUND** (0x801c003a/-2145648582) | L’objet point de connexion de service est configuré avec un ID de locataire incorrect, ou aucun abonnement actif n’a été trouvé dans le locataire. | Vérifiez que l’objet point de connexion de service est configuré avec l’ID de locataire et les abonnements actifs appropriés Azure AD ou que le service est présent dans le locataire. |
| **DSREG_SERVER_BUSY** (0x801c0025/-2145648603) | erreur HTTP 503 du serveur DRS. | Le serveur est actuellement indisponible. Les futures tentatives de jointure aboutiront probablement dès que le serveur sera de nouveau en ligne. |
| | |


**Autres erreurs** :

| Code d'erreur | Motif | Résolution |
| --- | --- | --- |
| **E_INVALIDDATA** (0x8007000d/-2147024883) | Impossible d’analyser la réponse du serveur JSON, probablement parce que le proxy retourne un HTTP 200 avec une page d’autorisation HTML. | Si l’environnement local nécessite un proxy sortant, l’administrateur informatique doit vérifier que le contexte du système sur l’appareil peut détecter le proxy sortant et s’y authentifier en mode silencieux. |
| | |


#### <a name="authentication-phase"></a>Phase d’authentification

Ce contenu s’applique uniquement aux comptes de domaine fédérés.

Causes de l’échec :

- Impossible d’obtenir un jeton d’accès en mode silencieux pour la ressource DRS.
   - Les appareils Windows&nbsp;10 obtiennent le jeton d’authentification à partir du service FS (Federation Service) en utilisant Authentification Windows intégrée sur un point de terminaison WS-Trust actif. Pour plus d’informations, consultez [Configuration du service FS (Federation Service)](hybrid-azuread-join-manual.md#set-up-issuance-of-claims).

**Codes d’erreur courants** :

Utilisez les journaux de l’Observateur d’événements pour rechercher le code d’erreur, le code de sous-erreur, le code d’erreur du serveur et le message d’erreur du serveur.

1. Dans l’Observateur d’événements, ouvrez les journaux des événements d’**Inscription des appareils de l’utilisateur** . Ils sont stockés dans le **Journal des Applications et des Services** > **Microsoft** > **Windows** > **Inscription des appareils de l’utilisateur**.
1. Recherchez l’ID d’événement 305.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/3.png" alt-text="Capture d’écran de l’Observateur d’événements, avec l’ID d’événement 305 sélectionné, les informations affichées, les codes d’erreur A D A L et l’état en surbrillance." border="false":::

**Erreurs de configuration** :

| Code d'erreur | Motif | Résolution |
| --- | --- | --- |
| **ERROR_ADAL_PROTOCOL_NOT_SUPPORTED** (0xcaa90017/-894894057) | Le protocole d’authentification de la Bibliothèque d’authentification Azure AD (ADAL) n’est pas WS-Trust. | Le fournisseur d’identité local doit prendre en charge WS-Trust. |
| **ERROR_ADAL_FAILED_TO_PARSE_XML** (0xcaa9002c/-894894036) | Le service FS (Federation Service) local n’a pas renvoyé de réponse XML. | Assurez-vous que le point de terminaison de l’Échange de métadonnées (MEX) renvoie un fichier XML valide. Assurez-vous que le proxy n’interfère pas et ne renvoie pas de réponses non-XML. |
| **ERROR_ADAL_COULDNOT_DISCOVER_USERNAME_PASSWORD_ENDPOINT** (0xcaa90023/-894894045) | Impossible de détecter un point de terminaison pour l’authentification par nom d’utilisateur/mot de passe. | vérifiez les paramètres du fournisseur d’identité local. Vérifiez que les points de terminaison WS-Trust sont activés et que la réponse MEX contient ces points de terminaison appropriés. |
| | |


**Erreurs réseau** :

| Code d'erreur | Motif | Résolution |
| --- | --- | --- |
| **ERROR_ADAL_INTERNET_TIMEOUT** (0xcaa82ee2/-894947614) | dépassement du délai d’attente général du réseau. | Vérifiez que `https://login.microsoftonline.com` est accessible dans le contexte du système. Assurez-vous que le fournisseur d’identité local est accessible dans le contexte du système. Pour plus d’informations, consultez [Exigences de connectivité réseau](hybrid-azuread-join-managed-domains.md#prerequisites). |
| **ERROR_ADAL_INTERNET_CONNECTION_ABORTED** (0xcaa82efe/-894947586) | La connexion avec le point de terminaison d’autorisation a été abandonnée. | Réessayez la jointure après un certain temps ou essayez à partir d’un autre emplacement réseau stable. |
| **ERROR_ADAL_INTERNET_SECURE_FAILURE** (0xcaa82f8f/-894947441) | Le certificat TLS (Transport Layer Security) (précédemment appelé certificat SSL (Secure Sockets Layer) [SSL]) envoyé par le serveur n’a pas pu être validé. | vérifiez l’asymétrie temporelle du client. Réessayez la jointure après un certain temps ou essayez à partir d’un autre emplacement réseau stable. |
| **ERROR_ADAL_INTERNET_CANNOT_CONNECT** (0xcaa82efd/-894947587) | la tentative de connexion à `https://login.microsoftonline.com` a échoué. | Vérifiez la connexion réseau à `https://login.microsoftonline.com`. |
| | |


**Autres erreurs** :

| Code d'erreur | Motif | Résolution |
| --- | --- | --- |
| **ERROR_ADAL_SERVER_ERROR_INVALID_GRANT** (0xcaa20003/-895352829) | Lle jeton SAML du fournisseur d’identité local n’a pas été accepté par Azure AD. | Vérifiez les paramètres du Serveur de fédération. Recherchez le code d’erreur du serveur dans les journaux d’authentification. |
| **ERROR_ADAL_WSTRUST_REQUEST_SECURITYTOKEN_FAILED** (0xcaa90014/-894894060) | La réponse WS-Trust du serveur a signalé une exception d’erreur et n’a pas pu obtenir d’assertion. | Vérifiez les paramètres du Serveur de fédération. Recherchez le code d’erreur du serveur dans les journaux d’authentification. |
| **ERROR_ADAL_WSTRUST_TOKEN_REQUEST_FAIL** (0xcaa90006/-894894074) | une erreur s’est produite lors de la tentative d’obtention du jeton d’accès auprès du point de terminaison de jeton. | recherchez l’erreur sous-jacente dans le journal ADAL. |
| **ERROR_ADAL_OPERATION_PENDING** (0xcaa1002d/-895418323) | Échec général d’ADAL | Recherchez le code de sous-erreur ou le code d’erreur du serveur dans les journaux d’authentification. |
| | |


#### <a name="join-phase"></a>Phase de jointure

Causes de l’échec :

Recherchez le type d’inscription et le code d’erreur dans les tables suivantes, en fonction de la version de Windows 10 que vous utilisez.

#### <a name="windowsnbsp10-version-1803-or-later"></a>Windows &nbsp; 10 version 1803 ou ultérieure

Recherchez la sous-section « Inscription précédente » dans la section « Données de diagnostic » de la sortie de l’état de la jointure. Cette section s’affiche uniquement si l’appareil est joint à un domaine et ne peut pas être hybride Azure AD.

Le champ « Type d’enregistrement » indique le type de jointure qui est effectuée.

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-7ac4-4714-88a1-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

#### <a name="earlier-windowsnbsp10-versions"></a>Versions antérieures Windows &nbsp; 10

Servez-vous des journaux de l’Observateur d’événements pour identifier la phase et le code d’erreur des échecs de jointure.

1. Dans l’Observateur d’événements, ouvrez les journaux des événements d’**Inscription des appareils de l’utilisateur** . Ils sont stockés dans le **Journal des Applications et des Services** > **Microsoft** > **Windows** > **Inscription des appareils de l’utilisateur**.
1. Recherchez l’ID d’événement 204.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/4.png" alt-text="Capture d’écran de l’Observateur d’événements, avec l’ID d’événement 204 sélectionné et son code d’erreur, l’état H T T P et le message mis en surbrillance." border="false":::

**Erreurs HTTP retournées à partir du serveur DRS** :

| Code d'erreur | Motif | Résolution |
| --- | --- | --- |
| **DSREG_E_DIRECTORY_FAILURE** (0x801c03f2/-2145647630) | Réponse d’erreur reçue de DRS avec le code d’erreur : « DirectoryError ». | recherchez les causes et les résolutions possibles dans le code d’erreur du serveur. |
| **DSREG_E_DEVICE_AUTHENTICATION_ERROR** (0x801c0002/-2145648638) | Réception d’une réponse d’erreur de DRS avec le code d’erreur : « AuthenticationError » et ErrorSubCode n’est *pas* « DeviceNotFound ». | recherchez les causes et les résolutions possibles dans le code d’erreur du serveur. |
| **DSREG_E_DEVICE_INTERNALSERVICE_ERROR** (0x801c0006/-2145648634) | Réponse d’erreur reçue de DRS avec le code d’erreur : « DirectoryError ». | recherchez les causes et les résolutions possibles dans le code d’erreur du serveur. |
| | |


**Erreurs du module de plateforme sécurisée (TPM)**  :

| Code d'erreur | Motif | Résolution |
| --- | --- | --- |
| **NTE_BAD_KEYSET** (0x80090016/-2146893802) | L’opération de Module de plateforme sécurisée (TPM) (TPM) a échoué ou n’est pas valide. | L’échec est probablement dû à une image Sysprep incorrecte. Assurez-vous que la machine à partir de laquelle l’image Sysprep a été créée n’est pas jointe, hybride ou inscrite auprès d’Azure AD. |
| **TPM_E_PCP_INTERNAL_ERROR** (0x80290407/-2144795641) | erreur TPM générique. | désactivez le module de plateforme sécurisée (TPM) sur les appareils présentant cette erreur. Windows &nbsp; 10 versions 1809 et ultérieures détecte automatiquement les défaillances du module de plateforme sécurisée et complètent la jointure Azure AD hybride sans utiliser le module de plateforme sécurisée. |
| **TPM_E_NOTFIPS** (0x80280036/-2144862154) | Le module TPM en mode FIPS n’est actuellement pas pris en charge. | désactivez le module de plateforme sécurisée (TPM) sur les appareils présentant cette erreur. Windows 10 version 1809 et ultérieures détecte automatiquement les échecs du module TPM et effectue la jointure Azure AD hybride sans utiliser le module TPM. |
| **NTE_AUTHENTICATION_IGNORED** (0x80090031/-2146893775) | Le module TPM est verrouillé. | Erreur temporaire. Attendez la période de temps de recharge. La tentative de jointure devrait réussir après un certain temps. Pour plus d’informations, consultez les [principes de base du module de plateforme sécurisée](/windows/security/information-protection/tpm/tpm-fundamentals#anti-hammering). |
| | |


**Erreurs réseau** :

| Code d'erreur | Motif | Résolution |
| --- | --- | --- |
| **WININET_E_TIMEOUT** (0x80072ee2/-2147012894) | Dépassement du délai d’attente général du réseau pendant la tentative d’inscription de l’appareil sur DRS. | vérifiez la connectivité réseau à `https://enterpriseregistration.windows.net`. |
| **WININET_E_NAME_NOT_RESOLVED** (0x80072ee7/-2147012889) | Le nom ou l’adresse du serveur n’a pas pu être résolue. | vérifiez la connectivité réseau à `https://enterpriseregistration.windows.net`. |
| **WININET_E_CONNECTION_ABORTED** (0x80072efe/-2147012866) | la connexion avec le serveur s’est terminée anormalement. | Réessayez la jointure après un certain temps ou essayez à partir d’un autre emplacement réseau stable. |
| | |


**Autres erreurs** :

| Code d'erreur | Motif | Résolution |
| --- | --- | --- |
| **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED** (0x801c001d/-2145648611) | EventID 220 est présent dans les journaux des événements d’inscription des appareils de l’utilisateur. Windows ne peut pas accéder à l’objet ordinateur dans le répertoire actif. Un code d’erreur Windows peut être inclus dans l’événement. Les codes d’erreur ERROR_NO_SUCH_LOGON_SESSION (1312) et ERROR_NO_SUCH_USER (1317) ont trait à des problèmes de réplication dans un AD local. | Résoudre les problèmes de réplication dans le répertoire actif. Ces problèmes de réplication peuvent être temporaires et peuvent disparaître après un certain temps. |
| | |


**Erreurs du serveur – Jointure fédérée** :

| Code d’erreur du serveur | Message d’erreur du serveur | Causes possibles | Résolution |
| --- | --- | --- | --- |
| DirectoryError | Votre requête est limitée temporairement. Réessayez après 300 secondes. | Il s’agit d’une erreur attendue, peut-être parce que plusieurs demandes d’inscription ont été effectuées en succession rapide. | Tentez à nouveau la jointure après la période de temps de recharge. |
| | |

**Erreurs du serveur – Jointure de synchronisation** :

| Code d’erreur du serveur | Message d’erreur du serveur | Causes possibles | Résolution |
| --- | --- | --- | --- |
| DirectoryError | AADSTS90002 : Locataire `UUID` introuvable. Cette erreur peut se produire s’il n’existe pas d’abonnement actif pour le locataire. Vérifiez avec l’administrateur de votre abonnement. | L’ID de locataire dans l’objet point de connexion de service est incorrect. | Vérifiez que l’objet point de connexion de service est configuré avec l’ID de locataire et les abonnements actifs appropriés Azure AD ou que le service est présent dans le locataire. |
| DirectoryError | L’objet d’appareil correspondant à l’ID donné est introuvable. | Il s’agit d’une erreur attendue pour la jointure de synchronisation. L’objet d’appareil n’a pas été synchronisé d’AD vers Azure AD. | Attendez la fin de la synchronisation Azure AD Connect et la prochaine tentative de jointure postérieure à la synchronisation résoudra le problème. |
| AuthenticationError | Vérification du SID de l’ordinateur cible | Le certificat sur l’appareil Azure AD ne correspond pas au certificat utilisé pour connecter l’objet blob pendant la jointure de synchronisation. Cette erreur signifie généralement que la synchronisation n’est pas encore terminée. |  Attendez la fin de la synchronisation Azure AD Connect et la prochaine tentative de jointure postérieure à la synchronisation résoudra le problème. |

### <a name="step-5-collect-logs-and-contact-microsoft-support"></a>Étape 5 : Récupérer les journaux et contacter le Support Microsoft

1. [Téléchargez le fichier *Auth.zip*](https://cesdiagtools.blob.core.windows.net/windows/Auth.zip).

1. Extrayez les fichiers dans un dossier, tel que *c:\temp*, puis accédez au dossier.
1. À partir d’une session de PowerShell Azure élevée, exécutez `.\start-auth.ps1 -v -accepteula`.
1. Sélectionnez **Basculer le compte** pour basculer vers une autre session avec l’utilisateur posant problème.
1. Reproduisez le problème.
1. Sélectionnez **Basculer le compte** pour revenir à la session d’administration qui exécute le suivi.
1. À partir de la session PowerShell élevées, exécutez `.\stop-auth.ps1` .
1. Compressez et envoyez le dossier *Authlogs* à partir du dossier où les scripts ont été exécutés.
    
## <a name="troubleshoot-post-join-authentication-issues"></a>Résoudre les problèmes d’authentification post-jointure

### <a name="step-1-retrieve-the-prt-status-by-using-dsregcmd-status"></a>Étape 1 : récupérer l’état PRT à l’aide de `dsregcmd /status`

1. Ouvrez une fenêtre d’invite de commandes. 
   > [!NOTE] 
   > Pour connaître l’état du jeton d’actualisation principal (PRT), ouvrez la fenêtre d’invite de commandes dans le contexte de l’utilisateur connecté. 

1. Exécutez `dsregcmd /status`. 

   La section « état de la SSO » fournit l’état PRT actuel. 

   Si le champ AzureAdPrt est défini sur *NON*, une erreur s’est produite lors de l’acquisition de l’état PRT à partir d’Azure AD. 

1. Si la valeur AzureAdPrtUpdateTime est supérieure à 4 heures, il y a probablement un problème d’actualisation du PRT. Verrouillez et déverrouillez l’appareil pour forcer l’actualisation du PRT, puis vérifiez si l’heure a été mise à jour.

```
+----------------------------------------------------------------------+
| SSO State                                                            |
+----------------------------------------------------------------------+

                AzureAdPrt : YES
      AzureAdPrtUpdateTime : 2020-07-12 22:57:53.000 UTC
      AzureAdPrtExpiryTime : 2019-07-26 22:58:35.000 UTC
       AzureAdPrtAuthority : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
             EnterprisePrt : YES
   EnterprisePrtUpdateTime : 2020-07-12 22:57:54.000 UTC
   EnterprisePrtExpiryTime : 2020-07-26 22:57:54.000 UTC
    EnterprisePrtAuthority : https://corp.hybridadfs.contoso.com:443/adfs

+----------------------------------------------------------------------+
```

### <a name="step-2-find-the-error-code"></a>Étape 2 : rechercher le code d’erreur 

**À partir de la `dsregcmd` sortie**

> [!NOTE]
>  La sortie est disponible à partir de Windows &nbsp; mise à jour du 10 mai 2021 (version 21H1).

Le champ « État de la tentative » sous le champ AzureAdPrt fournit l’état de la tentative PRT précédente, ainsi que d’autres informations de débogage requises. Pour les versions antérieures de Windows, extrayez les informations des journaux des opérations et des analyses Azure AD.

```
+----------------------------------------------------------------------+
| SSO State                                                            |
+----------------------------------------------------------------------+

                AzureAdPrt : NO
       AzureAdPrtAuthority : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
     AcquirePrtDiagnostics : PRESENT
      Previous Prt Attempt : 2020-07-18 20:10:33.789 UTC
            Attempt Status : 0xc000006d
             User Identity : john@contoso.com
           Credential Type : Password
            Correlation ID : 63648321-fc5c-46eb-996e-ed1f3ba7740f
              Endpoint URI : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/token/
               HTTP Method : POST
                HTTP Error : 0x0
               HTTP status : 400
         Server Error Code : invalid_grant
  Server Error Description : AADSTS50126: Error validating credentials due to invalid username or password.
```

**À partir de l’analyse des journaux des opérations et des analyses Azure AD**

Utilisez l’Observateur d’événements pour rechercher les entrées de journal qui sont journalisées par le plug-in CloudAP Azure AD lors de l’acquisition de PRT. 

1. Dans l’Observateur d’événements, ouvrez les journaux des événements Operational d’Azure AD. Ils sont stockés sous **Journal Applications et services** > **Microsoft** > **Windows** > **AAD**. 

   > [!NOTE]
   > Le plug-in CloudAP enregistre les événements d’erreur dans les journaux des opérations et enregistre les événements d’informations dans les journaux d’analyse. Les événements des journaux d’analyse et des opérations sont requis pour résoudre les problèmes. 

1. L’événement 1006 dans les journaux d’analyse indique le début du processus d’acquisition de PRT et l’événement 1007 dans les journaux d’analyse indique la fin du flux d’acquisition PRT. Tous les événements des journaux AAD (analytiques et opérationnels) entre les événements 1006 et 1007 ont été enregistrés dans le cadre du flux d’acquisition PRT. 

1. L’événement 1007 enregistre le code d’erreur final.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/event-viewer-prt-acquire.png" alt-text="Capture d’écran de l’Observateur d’événements, avec les ID d’événements 1006 et 1007 sélectionnés et le code d’erreur final mis en surbrillance." border="false":::

### <a name="step-3-troubleshoot-further-based-on-the-found-error-code"></a>Étape 3 : Résoudre les problèmes en fonction du code d’erreur trouvé

| Code d'erreur | Motif | Résolution |
| --- | --- | --- |
| **STATUS_LOGON_FAILURE** (-1073741715/ 0xc000006d)<br>**STATUS_WRONG_PASSWORD** (-1073741718/ 0xc000006a) | <li>L’appareil n’est pas en mesure de se connecter au service d’authentification Azure AD.<li>Réception d’une réponse d’erreur (HTTP 400) à partir du service d’authentification Azure AD ou du point de terminaison WS-Trust.<br>**Remarque** : WS-Trust est requis pour l’authentification fédérée. | <li>Si l’environnement local nécessite un proxy sortant, l’administrateur informatique doit vérifier que le compte d’ordinateur de l’appareil peut découvrir le proxy sortant et s’y authentifier en mode silencieux.<li>Les événements 1081 et 1088 (journaux des opérations Azure AD) contiennent le code d’erreur du serveur pour les erreurs provenant du service d’authentification Azure AD et la description de l’erreur pour les erreurs provenant du point de terminaison de WS-Trust. Les codes d’erreur de serveur courants et leurs résolutions sont répertoriés dans la section suivante. La première instance de l’événement 1022 (journaux d’analyse Azure AD), les événements précédents 1081 ou 1088, contient l’URL à laquelle vous accédez. |
| **STATUS_REQUEST_NOT_ACCEPTED** (-1073741616/ 0xc00000d0) | Réception d’une réponse d’erreur (HTTP 400) à partir du service d’authentification Azure AD ou du point de terminaison WS-Trust.<br>**Remarque** : WS-Trust est requis pour l’authentification fédérée. | Les événements 1081 et 1088 (journaux opérationnels Azure AD) contiennent le code d’erreur du serveur et la description de l’erreur pour les erreurs provenant du service d’authentification Azure AD et du point de terminaison WS-Trust, respectivement. Les codes d’erreur de serveur courants et leurs résolutions sont répertoriés dans la section suivante. La première instance de l’événement 1022 (journaux d’analyse Azure AD), les événements précédents 1081 ou 1088, contient l’URL à laquelle vous accédez. |
| **STATUS_NETWORK_UNREACHABLE** (-1073741252/ 0xc000023c)<br>**STATUS_BAD_NETWORK_PATH** (-1073741634/ 0xc00000be)<br>**STATUS_UNEXPECTED_NETWORK_ERROR** (-1073741628/ 0xc00000c4) | <li>Réception d’une réponse d’erreur (HTTP > 400) à partir du service d’authentification Azure AD ou du point de terminaison WS-Trust.<br>**Remarque** : WS-Trust est requis pour l’authentification fédérée.<li>Problème de connectivité réseau à un point de terminaison requis | <li>Pour les erreurs de serveur, les événements 1081 et 1088 (journaux des opérations Azure AD) contiennent le code d’erreur du service d’authentification Azure AD et la description de l’erreur du point de terminaison de WS-Trust. Les codes d’erreur de serveur courants et leurs résolutions sont répertoriés dans la section suivante.<li>Pour les problèmes de connectivité, les événements 1022 (journaux d’analyse Azure AD) contiennent l’URL à laquelle vous accédez et l’événement 1084 (journaux opérationnels Azure AD) contiennent le code de sous-erreur de la pile réseau. |
| **STATUS_NO_SUCH_LOGON_SESSION**    (-1073741729/ 0xc000005f) | La détection du domaine utilisateur a échoué car le service d’authentification Azure AD n’a pas pu trouver le domaine de l’utilisateur. | <li>Le domaine de l’UPN de l’utilisateur doit être ajouté en tant que domaine personnalisé dans Azure AD. L’événement 1144 (journaux d’analyse Azure AD) contient l’UPN fourni.<li>Si le nom de domaine local n’est pas routable (jdoe@contoso.local), configurez un ID de connexion alternatif (AltID). Références : [Prérequis](hybrid-azuread-join-plan.md) [Configurer un ID de connexion alternatif](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id). |
| **AAD_CLOUDAP_E_OAUTH_USERNAME_IS_MALFORMED**   (-1073445812/ 0xc004844c) | L’UPN de l’utilisateur n’est pas au format attendu.<br>**Remarques**:<li>Pour les appareils joints Azure AD, l’UPN est le texte entré par l’utilisateur dans le LoginUI. <li>Pour les appareils hybrides joints Azure AD, l’UPN est retourné à partir du contrôleur de domaine pendant le processus de connexion. | <li>L’UPN de l’utilisateur doit avoir le format du nom de connexion Internet, en fonction de la norme Internet [RFC 822](https://www.ietf.org/rfc/rfc0822.txt). L’événement 1144 (journaux d’analyse Azure AD) contient l’UPN fourni.<li>Pour les appareils joints hybrides, assurez-vous que le contrôleur de domaine est configuré pour retourner l’UPN au format approprié. Dans le contrôleur de domaine, `whoami /upn` doit afficher l’UPN configuré.<li>Si le nom de domaine local n’est pas routable (jdoe@contoso.local), configurez un ID de connexion alternatif (AltID). Références : [Prérequis](hybrid-azuread-join-plan.md) [Configurer un ID de connexion alternatif](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id). |
| **AAD_CLOUDAP_E_OAUTH_USER_SID_IS_EMPTY** (-1073445822/ 0xc0048442) | Le SID de l’utilisateur est manquant dans le jeton d’ID renvoyé par le service d’authentification Azure AD. | Vérifiez que le proxy de réseau n’interfère pas avec la réponse du serveur et ne la modifie pas |
| **AAD_CLOUDAP_E_WSTRUST_SAML_TOKENS_ARE_EMPTY** (--1073445695/ 0xc00484c1) | Réception d’une erreur du point de terminaison WS-Trust.<br>**Remarque** : WS-Trust est requis pour l’authentification fédérée. | <li>Vérifiez que le proxy de réseau n’interfère pas avec la réponse du serveur et ne la modifie pas<li>L’événement 1088 (journaux opérationnels Azure AD) contient le code d’erreur du serveur et la description de l’erreur du point de terminaison WS-Trust. Les codes d’erreur de serveur courants et leurs résolutions sont répertoriés dans la section suivante. |
| **AAD_CLOUDAP_E_HTTP_PASSWORD_URI_IS_EMPTY** (-1073445749/ 0xc004848b) | Point de terminaison MEX configuré de façon incorrecte. La réponse MEX ne contient aucune URL de mot de passe. | <li>Vérifiez que le proxy de réseau n’interfère pas avec la réponse du serveur et ne la modifie pas<li>Corrigez la configuration MEX pour renvoyer des URL valides en réponse. |
| **WC_E_DTDPROHIBITED** (-1072894385/ 0xc00cee4f) | La réponse XML, du point de terminaison WS-Trust, comprenait une définition de type de document (DTD). La DTD n’est pas attendue dans les réponses XML et l’analyse de la réponse échoue si la DTD est incluse.<br>**Remarque** : WS-Trust est requis pour l’authentification fédérée. | <li>Corrigez la configuration du fournisseur d’identité pour éviter d’envoyer une DTD dans une réponse XML.<li>L’événement 1022 (journaux d’analyse Azure AD) contient l’URL à laquelle l’utilisateur accède, qui renvoie une réponse XML avec une DTD. |
| | |


**Codes d’erreur de serveur courants :**

| Code d'erreur | Motif | Résolution |
| --- | --- | --- |
| **AADSTS50155 : échec de l’authentification de l’appareil** | <li>Azure AD ne peut pas authentifier l’appareil pour émettre un PRT.<li>Vérifiez que l’appareil n’a pas été supprimé ou désactivé dans le portail Azure. Pour plus d’informations sur ce problème, consultez la [FAQ sur la gestion des appareils du Répertoire actif Azure](faq.yml#why-do-my-users-see-an-error-message-saying--your-organization-has-deleted-the-device--or--your-organization-has-disabled-the-device--on-their-windows-10-11-devices). | Suivez les instructions pour ce problème dans la [FAQ sur la gestion des appareils du Répertoire actif Azure](faq.yml#i-disabled-or-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell--but-the-local-state-on-the-device-says-it-s-still-registered--what-should-i-do) pour réinscrire l’appareil en fonction du type de jonction de l’appareil. |
| **AADSTS50034 : le compte d’utilisateur `Account` n’existe pas dans le répertoire `tenant id`** . | Azure AD ne parvient pas à trouver le compte d’utilisateur dans le locataire. | <li>Assurez-vous que l’utilisateur tape l’UPN correct.<li>Assurez-vous que le compte d’utilisateur local est synchronisé avec Azure AD.<li>L’événement 1144 (journaux d’analyse Azure AD) contient l’UPN fourni. |
| **AADSTS50126 : erreur de validation des informations d’identification en raison d’un nom d’utilisateur ou d’un mot de passe non valide.** | <li>Le nom d’utilisateur et le mot de passe entrés par l’utilisateur dans le LoginUI Windows sont incorrects.<li>Si le client a activé la synchronisation de hachage de mot de passe, que l’appareil présente une jointure hybride et que l’utilisateur vient de modifier le mot de passe, il est probable que le nouveau mot de passe n’a pas été synchronisé sur Azure AD. | Pour obtenir un PRT actualisé avec les nouvelles informations d’identification, attendez la fin de la synchronisation du mot de passe Azure AD. |
| | |


**Codes d’erreur réseau courants :**

| Code d'erreur | Motif | Résolution |
| --- | --- | --- |
| **ERROR_WINHTTP_TIMEOUT** (12002)<br>**ERROR_WINHTTP_NAME_NOT_RESOLVED** (12007)<br>**ERROR_WINHTTP_CANNOT_CONNECT** (12029)<br>**ERROR_WINHTTP_CONNECTION_ERROR** (12030) | Problèmes courants liés au réseau général. | <li>Les événements 1022 (journaux d’analyse Azure AD) et 1084 (journaux opérationnels Azure AD) contiennent l’URL à laquelle vous accédez.<li>Si l’environnement local nécessite un proxy sortant, l’administrateur informatique doit vérifier que le compte d’ordinateur de l’appareil peut découvrir le proxy sortant et s’y authentifier en mode silencieux.<br><br>Obtenez des [codes d’erreur réseau](/windows/win32/winhttp/error-messages) supplémentaires. |
| | |


### <a name="step-4-collect-logs"></a>Étape 4 : collecter les journaux

**Journaux standard**

1. Accédez à https://aka.ms/icesdptool pour télécharger automatiquement un fichier *.cab* contenant l’outil de diagnostic.
1. Exécutez l’outil et reproduisez votre scenario.
1. Pour les traces Fiddler, acceptez les demandes de certificat qui s’affichent.
1. L’Assistant vous invite à entrer un mot de passe pour protéger vos fichiers de trace. Fournissez un mot de passe.
1. Enfin, ouvrez le dossier dans lequel sont stockés tous les journaux collectés, par exemple *%LOCALAPPDATA%\ElevatedDiagnostics\numbers*.
1. Contactez le support technique avec le contenu du dernier fichier de *.cab* .

**Suivis réseau**

> [!NOTE]
> Lorsque vous collectez des suivis réseau, il est important de *ne pas* utiliser Fiddler pendant la reproduction.

1.  Exécutez `netsh trace start scenario=internetClient_dbg capture=yes persistent=yes`.
1. Verrouillez et déverrouillez l’appareil. Pour les appareils joints hybrides, attendez plus d’une minute pour permettre l’exécution de la tâche d’acquisition PRT.
1. Exécutez `netsh trace stop`.
1. Partager le fichier *nettrace.cab* avec la prise en charge.

---

## <a name="known-issues"></a>Problèmes connus
- Si vous êtes connecté à un point d’accès mobile ou à un réseau Wi-Fi externe et que vous accédez à **Paramètres** > **Comptes** > **Accès professionnel ou scolaire**, les appareils hybrides Azure AD peuvent afficher deux comptes différents, l’un pour Azure AD et l’autre pour les services AD locaux. Il s’agit d’un problème d’interface utilisateur uniquement et n’affecte pas la fonctionnalité.

## <a name="next-steps"></a>Étapes suivantes

- [Dépannez les appareils à l’aide de la `dsregcmd` commande](troubleshoot-device-dsregcmd.md).
- Accédez à l'[Outil de recherche d’erreurs Microsoft](/windows/win32/debug/system-error-code-lookup-tool).
