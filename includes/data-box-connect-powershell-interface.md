---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 11/15/2021
ms.author: alkohli
ms.openlocfilehash: fa4d3974a016fc57624c2c51d9aaf703583f8764
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557337"
---
Selon la version du logiciel que votre Data Box exécute, il se peut que vous deviez peut-être effectuer différentes opérations pour vous connecter à l’interface PowerShell de l’appareil. Dans tous les cas, la première étape consiste à identifier la version du logiciel s’exécutant sur votre Data Box.

### <a name="identify-software-version-of-the-device"></a>Identifier la version logicielle de l’appareil

> [!NOTE]
> Le numéro de version est affiché dans l’interface utilisateur locale à partir de la versions 4.9. Si vous ne voyez pas de numéro de version comme décrit ici dans votre interface utilisateur locale, contactez le Support Microsoft pour identifier le numéro de version, puis suivez les étapes pour vous connecter à l’interface PowerShell de l’appareil.

Utilisez l’une des méthodes suivantes pour identifier la version du logiciel qui s’exécute sur votre appareil et le numéro de série de celui-ci.

 - Accédez à la page **Se connecter**. La version du logiciel est affichée sur cette page.
 
    ![Numéro de série et version du logiciel sur la page de connexion dans l’interface utilisateur web locale de Data Box](media/data-box-connect-powershell-interface/device-serial-number-software-version-sign-in-local-ui.png)

 - Dans l’interface utilisateur web locale, sélectionnez l’icône **Aide**. Dans le volet **Aide**, la version du logiciel et le numéro de série de l’appareil s’affichent. Prenez note du numéro de série de l’appareil et de la version du logiciel.
 
    ![Numéro de série et version du logiciel dans le volet Aide de l’interface utilisateur web locale de Data Box](media/data-box-connect-powershell-interface/device-serial-number-software-version-help-pane-local-ui.png)



Les étapes suivantes sont déterminées par la version du logiciel que votre Data Box exécute.
 
### <a name="v41-and-later"></a>[v4.1 et versions ultérieures](#tab/c)

Pour ces versions du logiciel, vous devez commencer par installer des certificats sur l’hôte qui accède à votre Data Box, puis vous connecter à l’interface PowerShell de l’appareil. Lorsque vous installez des certificats, vous pouvez utiliser les certificats par défaut fournis avec l’appareil, ou apporter vos propres certificats. Dans chaque cas, les étapes à suivre sont légèrement différentes.


### <a name="step-1-install-certificate-on-host-or-client"></a>Étape 1 : Installer le certificat sur l’hôte ou le client

#### <a name="use-default-certificates"></a>Utiliser les certificats par défaut 

Procédez comme suit si vous utilisez les certificats par défaut (fournis avec l’appareil) sur Data Box.

1. Dans le portail Azure, accédez à votre ressource de commande de Data Box. Accédez à **Général > Détails de l’appareil**. Sous l’onglet **Accès d’API à l’appareil**, sélectionnez **Télécharger**.

    ![Télécharger le certificat pour Data Box dans le portail Azure](media/data-box-connect-powershell-interface/download-certificate-data-box-portal.png)

1. Copiez le certificat vers l’hôte qui se connecte à votre Data Box. Si vous utilisez un hôte Windows, ouvrez l’Explorateur. Cliquez avec le bouton droit sur le fichier de certificat que vous avez copié vers l’hôte, puis sélectionnez **Installer le certificat**. 

1. Effectuez les étapes d’installation via l’**Assistant Importation de certificat**. Pour obtenir des étapes détaillées, consultez [Importer un certificat sur le client](../articles/databox/data-box-bring-your-own-certificates.md#import-certificates-to-client). 

1. Ajoutez une entrée au fichier hôte qui mappe l’adresse IP de l’appareil à son nom de domaine complet. Le format de l’entrée est le suivant : 

    `<Device IP address>  <Device serial number>.microsoftdatabox.com`

    Indiquez le numéro de série de l’appareil que vous avez noté à l’étape précédente. 

    ![Récupérer le numéro de série de Data Box à partir de l’interface utilisateur locale](media/data-box-connect-powershell-interface/get-device-serial-number-portal.png)
 

#### <a name="use-your-own-certificates"></a>Utiliser vos propres certificats 

Si vous apportez vos propres certificats sur Data Box, procédez comme suit.
 
1. Installez le certificat de chaîne de signatures que vous apportez sur l’ordinateur hôte que vous allez connecter à la Data Box. Ouvrez le certificat via l’Explorateur, cliquez avec le bouton droit sur le fichier, puis sélectionnez **Installer le certificat**. 
1. Suivez les étapes décrites dans [Importer des certificats sur le client](../articles/databox/data-box-bring-your-own-certificates.md#import-certificates-to-client), puis installez le certificat sur la machine local/racine. 
1. Ajoutez une entrée au fichier hôte qui mappe l’adresse IP de l’appareil à son nom de domaine complet. Le format de l’entrée est le suivant : 

    `<Device IP address>  <Name>.<DNS domain>` 

    Pour obtenir le **Nom** et le **Domaine DNS** de votre appareil, dans l’interface utilisateur locale, accédez à la page **Certificats**. 
1. Pour charger les certificats, dans l’interface utilisateur locale, accédez à la page **Certificats**. Sélectionnez **+ Ajouter des certificats**, puis fournissez votre certificat. Consultez [Charger des certificats](../articles/databox/data-box-bring-your-own-certificates.md#add-certificates-to-device).
 

### <a name="step-2-connect-to-the-powershell-interface"></a>Étape 2 : Se connecter à l’interface PowerShell


1. Définissez le paramètre `$Name`.

    ```powershell
    $Name = <Name>.<DNS domain>
    ```
    Fournissez le **Nom** et le **Domaine DNS** de votre appareil, obtenus aux étapes précédentes.

1. Ajoutez votre Data Box à la liste `TrustedHosts` à l’aide du paramètre `$Name`. Tapez :
 
    ```powershell
    Set-Item wsman:\localhost\Client\TrustedHosts $Name 
    ```

1. Récupérez le mot de passe pour l’interface utilisateur locale à partir du portail Azure. Créez une chaîne sécurisée contenant le mot de passe.

    ```powershell
    $Pwd = ConvertTo-SecureString <Password from Azure portal> -AsPlainText -Force 
    ```
   <!--

    ```powershell
    $Cred = New-Object System.Management.Automation.PSCredential("<ipv4_address of databox>\StorSimpleAdmin",$Pwd) 
    ```-->

1. Établissez la connexion.

    ```powershell
    Enter-PSSession -ComputerName $Name -ConfigurationName Minishell -Credential ~\StorSimpleAdmin -UseSSL  
    ```
    Voici un exemple de sortie :

    ```powershell
    PS C:\Users\Administrator> winrm quickconfig
    WinRM service is already running on this machine.
    WinRM is already set up for remote management on this computer.
    PS C:\Users\Administrator> $Name = "by506b4b5d0790.microsoftdatabox.com"
    PS C:\Users\Administrator> Set-Item WSMan:\localhost\Client\TrustedHosts $Name -Concatenate -Force
    PS C:\Users\Administrator> Enter-PSSession -ComputerName $Name -Credential ~\StorSimpleAdmin -ConfigurationName Minishell -UseSSL
    WARNING: Please engage Microsoft Support if you need to access this interface
    to troubleshoot any potential issues you may be experiencing.
    Changes made through this interface without involving Microsoft
    Support could result in an unsupported configuration.
    [by506b4b5d0790.microsoftdatabox.com]: PS>
    ```

### <a name="skip-certificate-validation"></a>Ignorer la validation du certificat

Si vous n’utilisez pas les certificats (nous vous recommandons fortement de les utiliser !), vous pouvez ignorer la vérification de la validation du certificat en utilisant les options de session : `-SkipCACheck -SkipCNCheck -SkipRevocationCheck`.

Pour ignorer la validation du certificat, procédez comme suit :

1. Définissez le paramètre `$Name`.

    ```powershell
    $Name = <Name>.<DNS domain>
    ```
 
1. Utilisez les options de session lors de l’ouverture d’une session PowerShell.

    ```powershell
    $sessOptions = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck 
    ```
1. Établissez la connexion.

    ```powershell
    Enter-PSSession -ComputerName $Name -ConfigurationName Minishell -Credential ~\StorSimpleAdmin -UseSSL -SessionOption $sessOptions 
    ```

### <a name="v30-to-v40"></a>[v3.0 vers v4.0](#tab/b)

1. Ouvrez une session PowerShell en utilisant **Exécuter en tant qu’administrateur**. 
1. Ajoutez votre Data Box à la liste `TrustedHosts` à l’aide de l’adresse IP. Tapez :

    ```powershell
    Set-Item Wsman:\localhost\Client\TrustedHosts <IPv4_address for your Data Box> 
    ``` 

1. Récupérez le mot de passe pour votre ressource de commande de Data Box dans le portail Azure. 

1. Convertissez le mot de passe fourni en texte brut en une chaîne sécurisée. Tapez :

    ```powershell
    $Pwd = ConvertTo-SecureString <Password from Azure portal> -AsPlainText -Force 
    ```
1. Entrez le nom d’utilisateur et le mot de passe pour la session, puis créez un objet d’informations d’identification. Le nom d’utilisateur par défaut est `StorSimpleAdmin`, et le mot de passe est celui que vous avez obtenu à l’étape précédente.

    ```powershell
    $Cred = New-Object System.Management.Automation.PSCredential("~\StorSimpleAdmin",$Pwd)
    ``` 
1. Démarrez une session avec l’appareil.

    ```powershell
    Enter-PSSession -Computer <IPv4_address for your Data Box> -ConfigurationName Minishell -Credential $Cred 
    ```

    Voici un exemple de sortie :
    
    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    Try the new cross-platform PowerShell https://aka.ms/pscore6
    
    PS C:\WINDOWS\system32> Set-Item wsman:\localhost\Client\TrustedHosts "10.128.45.52"
    
    WinRM Security Configuration.
    This command modifies the TrustedHosts list for the WinRM client. The computers in the
    TrustedHosts list might not be authenticated. The client might send credential information to
     these computers. Are you sure that you want to modify this list?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
    PS C:\WINDOWS\system32> $Pwd = ConvertTo-SecureString "Password1" -AsPlainText -Force
    PS C:\WINDOWS\system32> $Cred = New-Object System.Management.Automation.PSCredential("~\StorSimpleAdmin",$Pwd)
    PS C:\WINDOWS\system32> Enter-PSSession -Computer "10.128.45.52" -ConfigurationName Minishell -Credential $Cred
    WARNING: Please engage Microsoft Support if you need to access this interface
    to troubleshoot any potential issues you may be experiencing.
    Changes made through this interface without involving Microsoft
    Support could result in an unsupported configuration.
    [10.128.45.52]: PS>
    ```




  

