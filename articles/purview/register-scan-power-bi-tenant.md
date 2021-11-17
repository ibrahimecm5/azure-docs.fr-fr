---
title: Connecter et gérer un locataire Power BI
description: Ce guide explique comment se connecter à un locataire Power BI dans Azure Purview et utiliser les fonctionnalités de Purview pour analyser et gérer votre source de locataire Power BI.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 39b6dd297ad0fbb739272db41900e68c799e790d
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853778"
---
# <a name="connect-to-and-manage-a-power-bi-tenant-in-azure-purview"></a>Connecter et gérer un locataire Power BI dans Azure Purview

Cet article explique comment inscrire in locataire Power BI et comment s’authentifier et interagir avec le locataire dans Azure Purview. Pour plus d’informations sur Azure Purview, consultez l’[article d’introduction](overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

|**Extraction des métadonnées**|  **Analyse complète**  |**Analyse incrémentielle**|**Analyse délimitée**|**Classification**|**Stratégie d'accès**|**Traçabilité**|
|---|---|---|---|---|---|---|
| [Oui](#register)| [Oui](#scan)| Non | Non | Non | Non| [Oui](how-to-lineage-powerbi.md)|

> [!Note]
> Si l’instance Purview et le locataire Power BI se trouvent dans le même locataire Azure, vous pouvez uniquement utiliser l’authentification d’identité managée (MSI) pour configurer l’analyse d’un locataire Power BI.

### <a name="known-limitations"></a>Limitations connues

-   Dans le cas d’un scénario multilocataire, aucune expérience utilisateur n’est actuellement disponible pour inscrire et analyser le multilocataire Power BI.
-   La modification du multilocataire Power BI inscrit auprès de PowerShell à l’aide de Purview Studio altère l’inscription de la source de données et se traduit par un comportement d’analyse incohérent.
-   Passez en revue [Power BI limitations d’analyse des métadonnées](/power-bi/admin/service-admin-metadata-scanning).


## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Une [ressource Purview](create-catalog-portal.md) active.

* Vous devez être un administrateur de source de données et un lecteur de données pour inscrire une source et la gérer dans Purview Studio. Pour plus d’informations, consultez notre [page d’autorisations Azure Purview](catalog-permissions.md).

## <a name="register"></a>Inscrire

Cette section décrit comment inscrire un locataire Power BI dans Azure Purview dans des scénarios [monolocataire](#authentication-for-a-same-tenant-scenario) et [multilocataire](#steps-to-register-cross-tenant).

### <a name="authentication-for-a-same-tenant-scenario"></a>Authentification pour un scénario monolocataire

Pour les scénarios tant monolocataire que multilocataire, pour configurer l’authentification, créez un groupe de sécurité et ajoutez-y l’identité managée par Purview.

1. Dans le [portail Azure](https://portal.azure.com), recherchez **Azure Active Directory**.
1. Créez un groupe de sécurité dans votre annuaire Azure Active Directory, en suivant les instructions fournies dans [Créer un groupe de base et ajouter des membres avec Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    > [!Tip]
    > Vous pouvez ignorer cette étape si vous disposez déjà d’un groupe de sécurité que vous souhaitez utiliser.

1. Sélectionnez **Sécurité** comme **Type de groupe**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="Capture d’écran du type de groupe de sécurité.":::

1. Ajoutez votre identité managée par Purview à ce groupe de sécurité. Sélectionnez **Membres**, puis **+ Ajouter des membres**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="Capture d’écran montrant comment ajouter l’instance gérée du catalogue à un groupe.":::

1. Recherchez votre identité managée par Purview et sélectionnez-la.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="Capture d’écran montrant comment ajouter un catalogue en recherchant son nom.":::

    Vous devez voir une notification de réussite indiquant qu’il a été ajouté.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="Capture d’écran montrant la réussite de l’ajout de l’identité MSI du catalogue.":::

#### <a name="associate-the-security-group-with-the-tenant"></a>Associer le groupe de sécurité au locataire

1. Connectez-vous au [portail d’administration Power BI](https://app.powerbi.com/admin-portal/tenantSettings).
1. Sélectionnez la page **Paramètres du locataire**.

    > [!Important]
    > Vous devez être Administrateur Power BI pour voir la page des paramètres du locataire.

1. Sélectionnez **Paramètres de l’API d’administration** > **Autoriser les principaux de service à utiliser les API d’administration Power BI en lecture seule (préversion)** .
1. Sélectionnez **Groupes de sécurité spécifiques**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="Image illustrant comment permettre aux principaux de service d’obtenir des autorisations d’API d’administration Power BI en lecture seule":::

1. Sélectionnez les **paramètres de l'API d'administration** >  **Améliorer les réponses de l'API d'administration avec des métadonnées détaillées** > Activez la bascule pour permettre à Purview Data Map de découvrir automatiquement les métadonnées détaillées des ensembles de données Power BI dans le cadre de ses analyses.

    > [!IMPORTANT]
    > Après avoir mis à jour les paramètres de l'API Admin sur votre locataire Power BI, attendez environ 15 minutes avant d'enregistrer une analyse et de tester la connexion.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-scan-sub-artifacts.png" alt-text="Image montrant la configuration du portail d’administration Power BI pour activer l’analyse des sous-artefacts.":::

    > [!Caution]
    > Lorsque vous autorisez le groupe de sécurité que vous avez créé (dont l’identité managée Purview est membre) à utiliser des API d’administration Power BI en lecture seule, vous l’autorisez également à accéder aux métadonnées (telles que le tableau de bord et les noms de rapports, les propriétaires, les descriptions) pour tous vos artefacts Power BI dans ce locataire. Une fois que les métadonnées ont été extraites dans Azure Purview, les autorisations de Purview, et non celles de Power BI, déterminent qui peut voir ces métadonnées.
  
    > [!Note]
    > Vous pouvez supprimer le groupe de sécurité de vos paramètres de développeur, mais les métadonnées précédemment extraites ne seront pas supprimées du compte Purview. Vous pouvez les supprimer séparément, si vous le souhaitez.

### <a name="steps-to-register-in-the-same-tenant"></a>Étapes d’inscription dans le même locataire

Maintenant que vous avez accordé à l’identité managée par Purview les autorisations nécessaires pour se connecter à l’API d’administration de votre locataire Power BI, vous pouvez configurer votre analyse à partir d’Azure Purview Studio.

1. Sélectionnez le **Mappage de données** dans le volet de navigation de gauche.

1. Sélectionnez ensuite **Inscription**.

    Sélectionnez **Power BI** comme source de données.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/select-power-bi-data-source.png" alt-text="Image montrant la liste des sources de données pouvant être choisies.":::

1. Donnez un nom convivial à votre instance de Power BI.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-friendly-name.png" alt-text="Image montrant le nom convivial de la source de données Power BI.":::

    Le nom doit comporter entre 3 et 63 caractères, et contenir uniquement des lettres minuscules, des chiffres, des traits de soulignement et des traits d’union.  Les espaces ne sont pas autorisés.

    Par défaut, le système trouvera le locataire Power BI qui existe dans le même abonnement Azure.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-datasource-registered.png" alt-text="Image montrant la source de données Power BI inscrite.":::

    > [!Note]
    > Pour Power BI, l’inscription et l’analyse de la source de données sont autorisées pour une seule instance.

### <a name="steps-to-register-cross-tenant"></a>Étapes d’inscription multilocataire

Dans un scénario multilocataire, vous pouvez utiliser PowerShell pour inscrire et analyser vos locataires Power BI. Vous pouvez parcourir et rechercher des ressources de locataire distant à l’aide d’Azure Purview Studio grâce à l’expérience d’interface utilisateur. 

Pensez à utiliser ce guide si le locataire Azure AD dans lequel se trouve le locataire Power BI est différent du locataire Azure AD où votre compte Azure Purview est approvisionné. Procédez comme suit pour inscrire et analyser un ou plusieurs locataires Power BI dans Azure Purview au sein d’un scénario multilocataire :

1. Téléchargez les [Modules PowerShell d’analyse gérée](https://github.com/Azure/Purview-Samples/blob/master/Cross-Tenant-Scan-PowerBI/ManagedScanningPowerShell.zip) et extrayez leur contenu à l’emplacement de votre choix.

1. Sur votre ordinateur, entrez **PowerShell** dans la zone de recherche de la barre des tâches Windows. Dans la liste de recherche, sélectionnez et maintenez (ou cliquez avec le bouton droit de la souris) **Windows PowerShell**, puis sélectionnez **Exécuter en tant qu'administrateur**.

1. Installez et importez le module sur votre ordinateur s’il n’a pas encore été installé.

   ```powershell
    Install-Module -name az
    Import-Module -name az
    Login-AzAccount
    ```

1. Connectez-vous à votre environnement Azure à l’aide des informations d’identification d’administrateur Azure AD où se trouve votre locataire Power BI.

   ```powershell
    Login-AzAccount
    ```

1. Dans la fenêtre PowerShell, entrez la commande suivante, en remplaçant `<path-to-managed-scanning-powershell-modules>` par le chemin d’accès aux modules extraits, par exemple `C:\Program Files\WindowsPowerShell\Modules\ManagedScanningPowerShell`.

   ```powershell
   dir -Path <path-to-managed-scanning-powershell-modules> -Recurse | Unblock-File
   ```

1. Entrez la commande suivante pour installer les modules PowerShell.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\ManagedScanningPowerShell\Microsoft.DataCatalog.Management.Commands.dll'
   ```

1. Utilisez la même session PowerShell pour définir les paramètres suivants. Mettez à jour `purview_tenant_id` avec l’ID de locataire Azure AD où Azure Purview est déployé et `powerbi_tenant_id` avec votre locataire Azure AD où se trouve le locataire Power BI. `purview_account_name` correspond à votre compte Azure Purview existant.

   ```powershell
   $azuretenantId = '<purview_tenant_id>'
   $powerBITenantIdToScan = '<powerbi_tenant_id>'
   $purviewaccount = '<purview_account_name>'
   ```

1. Créez un principal de service multilocataire.

   1. Créez une inscription d’application dans votre locataire Azure Active Directory où se trouve Power BI. Veillez à mettre à jour le champ `password` avec un mot de passe fort et `app_display_name` avec un nom d’application inexistant dans le locataire Azure AD où le locataire Power BI est hébergé.

      ```powershell   
      $SecureStringPassword = ConvertTo-SecureString -String <'password'> -AsPlainText -Force
      $AppName = '<app_display_name>'
      New-AzADApplication -DisplayName $AppName -Password $SecureStringPassword
      ```

   1. Dans le tableau de bord Azure Active Directory, sélectionnez l’application nouvellement créée, puis **Inscription de l’application**. Attribuez à l’application les autorisations déléguées suivantes et accordez au locataire un consentement d’administrateur :

      - Power BI Service     Tenant.Read.All
      - Microsoft Graph      openid

      :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-delegated-permissions.png" alt-text="Capture d’écran des autorisations déléguées pour le service Power BI et Microsoft Graph.":::

   1. Dans le tableau de bord Azure Active Directory, sélectionnez l’application nouvellement créée, puis **Authentification**. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans un annuaire d’organisation (tout annuaire Azure AD - Multilocataire)** . 

      :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-multitenant.png" alt-text="Capture d’écran montrant la prise en charge mutualisée du type de compte.":::

   1. Sous **Flux d’octroi implicite et flux hybride**, sélectionnez **Jetons d’ID (utilisés pour les flux implicites et hybrides)** .
    
      :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-id-token-hybrid-flows.png" alt-text="Capture d’écran montrant des flux hybrides de jetons d’ID.":::

   1. Créez l’URL de connexion spécifique au locataire pour votre principal du service en exécutant l’URL suivante dans votre navigateur web :

      ```
      https://login.microsoftonline.com/<purview_tenant_id>/oauth2/v2.0/authorize?client_id=<client_id_to_delegate_the_pbi_admin>&scope=openid&response_type=id_token&response_mode=fragment&state=1234&nonce=67890
      ```
    
      Veillez à remplacer les paramètres par des informations correctes :
      
      - `<purview_tenant_id>` est l’ID de locataire Azure Active Directory (GUID) où le compte Azure Purview est approvisionné.
      - `<client_id_to_delegate_the_pbi_admin>` est l’ID d’application correspondant à votre principal de service.
   
   1. Connectez-vous à l’aide d’un compte non-administrateur. Cela est nécessaire pour approvisionner votre principal du service dans le locataire étranger.

   1. Lorsque vous y êtes invité, acceptez l’autorisation requise pour _Afficher votre profil de base_ et _Conserver l’accès aux données auxquelles vous lui avez donné accès_.

1. Mettez à jour `client_id_to_delegate_the_pbi_admin` avec l’ID d’application (client) de l’application nouvellement créée et exécutez la commande suivante dans votre session PowerShell :

   ```powershell
   $ServicePrincipalId = '<client_id_to_delegate_the_pbi_admin>'
   ```

1. Créez un compte d’utilisateur dans le client Azure Active Directory où se trouve le locataire Power BI et attribuez le rôle Azure AD, **Administrateur Power BI**. Mettez à jour `pbi_admin_username` et `pbi_admin_password` avec les informations correspondantes et exécutez les lignes suivantes dans le terminal PowerShell :

    ```powershell
    $UserName = '<pbi_admin_username>'
    $Password = '<pbi_admin_password>'
    ```

    > [!Note]
    > Si vous créez un compte d’utilisateur dans Azure Active Directory à partir du portail, l’option Flux de client public a la valeur **Non** par défaut. Vous devez la définir sur **Oui** :
    > <br>
    > :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-public-client-flows.png" alt-text="Capture d’écran des flux de client public.":::
    
1. Dans Azure Purview Studio, affectez le rôle _Administrateur de source de données_ au principal du service et à l’utilisateur Power BI au niveau de la collection racine. 

1. Pour inscrire le multilocataire Power BI en tant que nouvelle source de données dans le compte Azure Purview, mettez à jour `service_principal_key` et exécutez les cmdlets suivantes dans la session PowerShell :

    ```powershell
    Set-AzDataCatalogSessionSettings -DataCatalogSession -TenantId $azuretenantId -ServicePrincipalAuthentication -ServicePrincipalApplicationId $ServicePrincipalId -ServicePrincipalKey '<service_principal_key>' -Environment Production -DataCatalogAccountName $purviewaccount

    Set-AzDataCatalogDataSource -Name 'pbidatasource' -AccountType PowerBI -Tenant $powerBITenantIdToScan -Verbose
    ```

## <a name="scan"></a>Analyser

Suivez les étapes ci-dessous pour analyser un locataire Power BI afin d’identifier automatiquement les ressources et de classer vos données. Pour plus d’informations sur l’analyse en général, consultez notre [Présentation des analyses et de l’ingestion](concept-scans-and-ingestion.md)

Ce guide couvre les scénarios d’analyse tant [monolocataire](#create-and-run-scan-for-same-tenant-power-bi) que [multilocataire](#create-and-run-scan-for-cross-tenant-power-bi).

### <a name="create-and-run-scan-for-same-tenant-power-bi"></a>Créer et exécuter une analyse pour Power BI monolocataire

Pour créer une analyse et l’exécuter, procédez comme suit :

1. Dans Purview Studio, accédez au **Mappage de données** dans le menu de gauche.

1. Accédez aux **Sources**.

1. Sélectionnez la source Power BI inscrite.

1. Sélectionnez **+ Nouvelle analyse**.

1. Donnez un nom à votre analyse. Sélectionnez ensuite l’option permettant d’inclure ou d’exclure les espaces de travail personnels. Notez que la seule méthode d’authentification prise en charge est **Identité managée**.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-scan-setup.png" alt-text="Image montrant la configuration de l’analyse Power BI.":::

    > [!Note]
    > Le changement de la configuration d’une analyse pour inclure ou exclure un espace de travail personnel déclenche l’analyse complète de la source Power BI.

1. Avant de continuer, sélectionnez **Tester la connexion**. Si **Tester la connexion** échoue, sélectionnez **Afficher le rapport** pour afficher l’état détaillé et résoudre le problème.
    1. Accès - L’état d’échec indique que l’authentification de l’utilisateur a échoué. Les analyses utilisant une identité managée aboutissent systématiquement car aucune authentification de l’utilisateur n’est requise.
    1. Ressources (+ traçabilité) - L’état échec indique que l’autorisation Purview - Power BI a échoué. Assurez-vous que l’identité managée par Purview est ajoutée au groupe de sécurité associé dans le portail d’administration Power BI.
    1. Métadonnées détaillées (améliorées) - L’état d’échec indique que le portail d’administration Power BI est désactivé pour le paramètre suivant - **Améliorer les réponses des API d’administration avec des métadonnées détaillées**

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-test-connection-status-report.png" alt-text="Capture d’écran de la page de rapport de l’état Tester la connexion.":::

1. Configurez un déclencheur d’analyse. Les options disponibles sont **Une fois**, **Tous les 7 jours** et **Tous les 30 jours**.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/scan-trigger.png" alt-text="Capture d’écran du planificateur d’analyse Purview.":::

1. Dans **Vérifier la nouvelle analyse**, sélectionnez **Enregistrer et exécuter** pour lancer l’analyse.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/save-run-power-bi-scan.png" alt-text="Capture d’écran de l’enregistrement et de l’exécution de la source Power BI.":::

### <a name="create-and-run-scan-for-cross-tenant-power-bi"></a>Créer et exécuter une analyse pour Power BI multilocataire

Pour créer et exécuter une nouvelle analyse dans Azure Purview, exécutez les cmdlets suivantes dans la session PowerShell :

   ```powershell
   Set-AzDataCatalogScan -DataSourceName 'pbidatasource' -Name 'pbiscan' -AuthorizationType PowerBIDelegated -ServicePrincipalId $ServicePrincipalId -UserName $UserName -Password $Password -IncludePersonalWorkspaces $true -Verbose

   Start-AzDataCatalogScan -DataSourceName 'pbidatasource' -Name 'pbiscan'
   ```

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez inscrit votre source, suivez les guides ci-dessous pour en savoir plus sur Purview et sur vos données.

- [Insights de données dans Azure Purview](concept-insights.md)
- [Lignage dans Azure Purview](catalog-lineage-user-guide.md)
- [Rechercher dans un catalogue de données](how-to-search-catalog.md)
