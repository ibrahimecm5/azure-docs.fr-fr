---
title: Migrer automatiquement à partir d’Azure Virtual Desktop (classique) [préversion] – Azure
description: Comment migrer automatiquement d’Azure Virtual Desktop (classique) vers Azure Virtual Desktop à l’aide du module de migration.
author: Heidilohr
ms.topic: how-to
ms.date: 09/15/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: a82d67cf0bb1644e31ff3d87d315cd920a92749a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131074590"
---
# <a name="migrate-automatically-from-azure-virtual-desktop-classic-preview"></a>Migrer automatiquement à partir d’Azure Virtual Desktop (classique) [préversion]

> [!IMPORTANT]
> L’outil du module de migration pour Azure Virtual Desktop est actuellement en préversion publique.
> Pour connaître les conditions juridiques qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou plus généralement non encore en disponibilité générale, consultez [l’Avenant aux conditions d’utilisation des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

L’outil du module de migration (préversion) vous permet de migrer automatiquement votre organisation d’Azure Virtual Desktop (classique) vers Azure Virtual Desktop. Cet article vous montre comment utiliser cet outil. 

## <a name="requirements"></a>Configuration requise

Avant d’utiliser le module de migration, assurez-vous que vous disposez des éléments suivants :

- Un abonnement Azure dans lequel vous allez créer de nouveaux objets de service Azure.

- Le rôle Contributeur doit vous être attribué pour créer des objets Azure sur votre abonnement, et le rôle Administrateur de l’accès utilisateur pour attribuer des utilisateurs à des groupes d’applications.

- Au minimum des autorisations de Contributeur Remote Desktop Services (RDS) sur un locataire RDS ou sur les pools d’hôtes spécifiques que vous migrez.

- La version la plus récente du module PowerShell Microsoft.RdInfra.RDPowershell. 

- La version la plus récente du module PowerShell Az.DesktopVirtualization. 

- La version la plus récente du module PowerShell Az.Resources. 

- Le module de migration installé sur votre ordinateur.

- PowerShell ou PowerShell ISE pour exécuter les scripts que vous verrez dans cet article. Le module Microsoft.RdInfra.RDPowershell ne fonctionne pas dans PowerShell Core.

>[!IMPORTANT]
>La migration crée uniquement des objets de service dans la zone géographique des États-Unis. Si vous tentez de migrer vos objets de service vers une autre zone géographique, cela ne fonctionnera pas. En outre, si votre déploiement d’Azure Virtual Desktop (classique) compte plus de 200 groupes d’applications, vous ne pourrez pas effectuer la migration. Vous pourrez effectuer une migration uniquement si vous régénérez votre environnement afin de réduire le nombre de groupes d’applications au sein de votre locataire Azure Active Directory (Azure AD).

## <a name="prepare-your-powershell-environment"></a>Préparation de votre environnement PowerShell

Tout d’abord, vous devez préparer votre environnement PowerShell pour le processus de migration.

Pour préparer votre environnement PowerShell :

1. Avant de commencer, assurez-vous que vous disposez de la version la plus récente des modules Az.Desktop Virtualization et Az.Resources en exécutant les cmdlets suivantes :

    ```powershell
    Get-Module Az.Resources
    Get-Module Az.DesktopVirtualization
    https://www.powershellgallery.com/packages/Az.DesktopVirtualization/
    https://www.powershellgallery.com/packages/Az.Resources/
    ```

    Si ce n’est pas le cas, installez et importez les modules en exécutant ces cmdlets :

    ```powershell
    Install-module Az.Resources
    Import-module Az.Resources
    Install-module Az.DesktopVirtualization
    Import-module Az.DesktopVirtualization
    ```

2. Ensuite, désinstallez le module PowerShell RDInfra actuel en exécutant cette cmdlet :

    ```powershell
    Uninstall-Module -Name Microsoft.RDInfra.RDPowershell -AllVersions
    ```

3. Après cela, installez le module RDPowershell avec cette cmdlet :

    ```powershell
    Install-Module -Name Microsoft.RDInfra.RDPowershell -RequiredVersion 1.0.3414.0 -force
    Import-module Microsoft.RDInfra.RDPowershell
    ```

4. Une fois que vous avez fini de tout installer, exécutez cette cmdlet pour vous assurer que vous disposez des versions appropriées des modules :

    ```powershell
    Get-Module Microsoft.RDInfra.RDPowershell
    ```

5. À présent, installons et importons le module de migration en exécutant ces cmdlets :

    ```powershell
    Install-Module -Name PackageManagement -Repository PSGallery -Force
    Install-Module -Name PowerShellGet -Repository PSGallery -Force
    # Then restart shell
    Install-Module -Name Microsoft.RdInfra.RDPowershell.Migration -RequiredVersion 1.0.3725-Prerelease -AllowPrerelease -AllowClobber
    Import-Module <Full path to the location of the migration module>\Microsoft.RdInfra.RDPowershell.Migration.psd1
    ```

6. Une fois que vous avez terminé, connectez-vous à Windows Virtual Desktop (classique) dans votre fenêtre PowerShell :

    ```powershell
    Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
    ```

7. Connectez-vous à Azure Resource Manager :

    ```powershell
    Login-AzAccount
    ```

8. Si vous avez plusieurs abonnements, sélectionnez celui vers lequel vous souhaitez migrer vos ressources avec cette cmdlet :

    ```powershell
    Select-AzSubscription -Subscriptionid <subID>
    ```

9. Inscrivez le fournisseur de ressources dans le portail Azure pour l’abonnement sélectionné.

10. Enfin, vous devez inscrire le fournisseur. Il existe deux façons de procéder :
    - Si vous souhaitez utiliser PowerShell, alors exécutez cette cmdlet :
       
       ```powershell
       Register-AzResourceProvider -ProviderNamespace Microsoft.DesktopVirtualization
       ```
    
    - Si vous préférez utiliser le portail Azure, ouvrez ce dernier et connectez-vous, puis accédez à **Abonnements** et sélectionnez le nom de l’abonnement que vous souhaitez utiliser. Ensuite, allez dans **Fournisseur de ressources** > **Microsoft.DesktopVirtualization** et sélectionnez **Réinscrire**. Vous ne verrez rien changer dans l’interface utilisateur pour le moment, mais votre environnement PowerShell devrait maintenant être prêt à exécuter le module.

## <a name="migrate-azure-virtual-desktop-classic-resources-to-azure-resource-manager"></a>Migrer des ressources Azure Virtual Desktop (classique) vers Azure Resource Manager

Maintenant que votre environnement PowerShell est prêt, vous pouvez commencer le processus de migration.

Pour migrer vos ressources Azure Virtual Desktop (classique) vers Azure Resource Manager :

1. Avant de procéder à la migration, si vous souhaitez comprendre la manière dont les ressources classiques existantes seront mappées aux nouvelles ressources Azure Resource Manager, exécutez cette cmdlet :
    
    ```powershell
    Get-RdsHostPoolMigrationMapping
    ```

    Grâce à **Get-RdsHostPoolMigrationMapping**, vous pouvez créer un fichier CSV qui mappe l’emplacement futur de vos ressources. Par exemple, si le nom de votre locataire est « Contoso » et que vous souhaitez stocker votre fichier de mappage dans le fichier « contosouser », vous devez exécuter une cmdlet qui ressemble à ceci :

    ```powershell
    Get-RdsHostPoolMigrationMapping -Tenant Contoso -HostPool Office -Location EastUS -OutputFile 'C:\\Users\contosouser\OneDrive - Microsoft\Desktop\mapping.csv'
    ```

2. Ensuite, exécutez la cmdlet **Start-RdsHostPoolMigration** pour choisir de migrer un seul pool d’hôtes ou tous les pools d’hôtes d’un locataire.

    Par exemple :

    ```powershell
    Start-RdsHostPoolMigration -Tenant Contoso -Location WestUS
    ```

    Si vous souhaitez migrer les ressources d’un pool d’hôtes spécifique, incluez le nom du pool d’hôtes. Par exemple, si vous voulez déplacer le pool d’hôtes nommé « Office », exécutez une commande comme celle-ci :

    ```powershell
    Start-RdsHostPoolMigration -Tenant Contoso -HostPool Office -CopyUserAssignments $false -Location EastUS
    ```

    Si vous ne fournissez pas de nom d’espace de travail, le module en créera automatiquement un pour vous en fonction du nom du locataire. Toutefois, si vous préférez utiliser un espace de travail spécifique, vous pouvez entrer son ID de ressource comme ceci :

    ```powershell
    Start-RdsHostPoolMigration -Tenant Contoso -HostPool Office -CopyUserAssignments -Location EastUS -Workspace <Resource ID of workspacename>
    ```
    
    Si vous souhaitez utiliser un espace de travail spécifique, mais que vous ne connaissez pas son ID de ressource, exécutez cette cmdlet :

    ```powershell
    Get-AzWvdWorkspace -WorkspaceName <workspace> -ResourceGroupName <resource group> |fl
    ```

  Vous devrez également spécifier un mode d’affectation d’utilisateur pour les affectations d’utilisateur existantes :

  - Utilisez **Copy** pour copier toutes les affectations d’utilisateur de vos anciens groupes d’applications vers les groupes d’applications Azure Resource Manager. Les utilisateurs pourront voir les flux pour les deux versions de leurs clients.
  - Utilisez **Aucun** si vous ne souhaitez pas modifier les affectations d’utilisateur. Plus tard, vous pourrez affecter des utilisateurs ou des groupes d’utilisateurs à des groupes d’applications à l’aide du portail Azure, de PowerShell ou de l’API. Les utilisateurs pourront voir les flux uniquement en utilisant les clients Azure Virtual Desktop (classique).

  Vous pouvez uniquement copier 2 000 affectations d’utilisateur par abonnement. Votre limite dépendra donc du nombre d’affectations déjà présentes dans votre abonnement. Le module calcule la limite en fonction du nombre d’affectations que vous avez déjà. Si vous n’avez pas assez d’affectations à copier, vous obtiendrez le message d’erreur suivant : « Quota d’attributions de rôle insuffisant pour copier les attributions utilisateur. Réexécutez la commande sans le commutateur -CopyUserAssignments pour effectuer la migration. »

3. Une fois que vous avez exécuté les commandes, le module peut prendre jusqu’à 15 minutes pour créer les objets de service. Si vous avez copié ou déplacé des affectations d’utilisateur, cela augmentera le temps nécessaire au module pour terminer la configuration de tous les éléments.

   Une fois que la cmdlet **Start-RdsHostPoolMigration** est terminée, vous devriez voir les éléments suivants :

      - Des objets de service Azure pour le locataire ou le pool d’hôtes que vous avez spécifié.

      - Deux nouveaux groupes de ressources :

         - Un groupe de ressources appelé « Tenantname », qui contient votre espace de travail.

         - Un groupe de ressources appelé « Tenantname_originalHostPoolName », qui contient le pool d’hôtes et les groupes d’applications de bureau.

      - Tous les utilisateurs que vous avez publiés dans les groupes d’applications nouvellement créés.

      - Les machines virtuelles seront disponibles à la fois dans les pools d’hôtes actuels et dans les nouveaux pools d’hôtes afin d’éviter les temps d’arrêt des utilisateurs pendant le processus de migration. Cela permet aux utilisateurs de se connecter à la même session utilisateur.

   Étant donné que ces nouveaux objets de service Azure sont des objets Azure Resource Manager, le module ne peut pas définir d’autorisations de contrôle d’accès en fonction du rôle (RBAC) ni de paramètres de diagnostic sur celles-ci. Par conséquent, vous devez mettre à jour les autorisations RBAC et les paramètres de ces objets manuellement.

   Une fois que le module a validé les connexions initiales des utilisateurs, vous pouvez également publier le groupe d’applications sur d’autres utilisateurs ou groupes d’utilisateurs, si vous le souhaitez.

   >[!NOTE]
   >Après la migration, si vous déplacez des groupes d’applications vers un autre groupe de ressources après avoir attribué des autorisations aux utilisateurs, cela supprimera tous les rôles RBAC. Vous devrez de nouveau attribuer les autorisations RBAC des utilisateurs.

4. Si vous souhaitez supprimer tous les objets de service Azure Virtual Desktop (classique), exécutez **Complete-RdsHostPoolMigration** pour terminer le processus de migration. Cette cmdlet supprimera tous les objets Azure Virtual Desktop (classique), ne laissant que les nouveaux objets Azure. Les utilisateurs ne pourront voir que les flux des groupes d’applications nouvellement créés sur leurs clients. Une fois cette commande terminée, vous pouvez supprimer en toute sécurité le locataire Azure Virtual Desktop (classique) pour terminer le processus.

   Par exemple :

   ```powershell
   Complete-RdsHostPoolMigration -Tenant Contoso -Location EastUS
   ```

   Si vous voulez terminer un pool d’hôtes spécifique, vous pouvez inclure le nom du pool d’hôtes dans la cmdlet. Par exemple, si vous souhaitez terminer un pool d’hôtes nommé « Office », vous devez utiliser une commande comme celle-ci :

    ```powershell
    Complete-RdsHostPoolMigration -Tenant Contoso -HostPool Office -Location EastUS
    ```

    Cette opération supprimera tous les objets de service créés par Azure Virtual Desktop (classique). Vous n’aurez plus que les nouveaux objets Azure, et les utilisateurs ne pourront voir que les flux des groupes d’applications nouvellement créés sur leurs clients. Une fois que vous avez finalisé votre migration, vous devez supprimer explicitement le locataire dans Azure Virtual Desktop (classique).

5. Si vous avez changé d’avis sur la migration et que vous souhaitez rétablir le processus, exécutez la cmdlet **Revert-RdsHostPoolMigration**.
    
   Par exemple :

   ```powershell
   Revert-RdsHostPoolMigration -Tenant Contoso -Location EastUS
   ```

   Si vous souhaitez rétablir un pool d’hôtes spécifique, vous pouvez inclure le nom du pool d’hôtes dans la commande. Par exemple, si vous voulez rétablir un pool d’hôtes nommé « Office », vous devez entrer quelque chose comme ceci :

   ```powershell
   Revert-RdsHostPoolMigration -Tenant Contoso -HostPool Office -Location EastUS
   ```

   Cette cmdlet supprimera tous les objets de service Azure nouvellement créés. Vos utilisateurs verront uniquement le flux des objets Azure Virtual Desktop (classique) dans leurs clients.

   Toutefois, la cmdlet ne supprimera pas l’espace de travail créé par le module ni le groupe de ressources qui lui est associé. Vous devrez supprimer manuellement ces éléments pour vous en débarrasser.

6. Si vous ne voulez pas supprimer vos objets de service (classiques) Azure Virtual Desktop pour le moment, mais que vous souhaitez tester la migration, vous pouvez exécuter **Set-RdsHostPoolHidden**.

    Par exemple :

    ```powershell
    Set-RdsHostPoolHidden -Tenant Contoso -Hostpool Office -Hidden $true -Location WestUS
    ```

    Si vous attribuez la valeur « true » à cet état, les ressources Azure Virtual Desktop (classique) seront masquées. S’il a la valeur « false », les ressources seront visibles pour vos utilisateurs.

    Le paramètre *-Hostpool* est facultatif. Vous pouvez utiliser ce paramètre si vous souhaitez masquer un pool d’hôtes Azure Virtual Desktop (classique) spécifique.

    Cette cmdlet masquera le flux utilisateur et les objets de service Azure Virtual Desktop (classique) au lieu de les supprimer. Toutefois, cela est généralement utilisé uniquement à des fins de test et n’est pas considéré comme une migration terminée. Pour terminer la migration, vous devez exécuter la commande **Complete-RdsHostPoolMigration**. Sinon, restaurez votre déploiement en exécutant la commande **Revert-RdsHostPoolMigration**.

## <a name="troubleshoot-automatic-migration"></a>Résoudre les problèmes de migration automatique

Cette section explique comment résoudre les problèmes fréquemment rencontrés dans le module de migration.

### <a name="i-cant-access-the-tenant"></a>Je ne peux pas accéder au locataire

Tout d’abord, essayez ces deux opérations :

- Assurez-vous que votre compte administrateur dispose des autorisations nécessaires pour accéder au locataire.
- Essayez d’exécuter **Get-RdsTenant** sur le locataire.

Si ces deux opérations fonctionnent, essayez d’exécuter le cmdlet **Set-RdsMigrationContext** pour définir le contexte RDS et le contexte ADAL pour votre migration :

1. Créez le contexte RDS en exécutant la cmdlet **Add-RdsAccount**.       

2. Recherchez le contexte RDS dans la variable globale *$rdMgmtContext*.         

3. Recherchez le contexte ADAL dans la variable globale *$AdalContext*.

4. Exécutez **Set-RdsMigrationContext** avec les variables que vous avez trouvées au format suivant :

   ```powershell
   Set-RdsMigrationContext -RdsContext <rdscontext> -AdalContext <adalcontext>
   ```

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez savoir comment migrer manuellement votre déploiement, consultez [Migrer manuellement à partir d’Azure Virtual Desktop (classique)](manual-migration.md).

Une fois la migration effectuée, découvrez comment fonctionne Azure Virtual Desktop en consultant [nos tutoriels](create-host-pools-azure-marketplace.md). Apprenez-en davantage sur les fonctionnalités de gestion avancées décrites dans [Développer un pool d’hôtes existant](expand-existing-host-pool.md) et [Personnaliser les propriétés RDP](customize-rdp-properties.md).

Pour plus d’informations sur les objets de service, consultez [Environnement Azure Virtual Desktop](environment-setup.md).
