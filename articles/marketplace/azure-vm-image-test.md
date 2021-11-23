---
title: Tester une image de machine virtuelle Azure pour la Place de marché Azure
description: Testez et soumettez une offre de machine virtuelle Azure sur la Place de marché Azure.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: ebolton-cyber
ms.author: edewebolton
ms.date: 03/10/2021
ms.openlocfilehash: 4e3e3b281e90236704ebd50cd8ee1420947c4d56
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132331240"
---
# <a name="test-a-virtual-machine-image"></a>Tester une image de machine virtuelle

Cette rubrique décrit les étapes permettant de tester une image de machine virtuelle pour le déploiement sur la Place de marché Azure.

## <a name="deploy-an-azure-vm"></a>Déployer une machine virtuelle Azure

Pour déployer une machine virtuelle à partir de l’image Azure Compute Gallery (anciennement Galerie d’images partagées) :

1. Accédez à la version de l’image Azure Compute Gallery.
1. Sélectionnez **Créer une machine virtuelle**.
1. Fournissez un nom de machine virtuelle et sélectionnez une taille de machine virtuelle.
1. Sélectionnez **Revoir + créer**. Une fois la validation réussie, sélectionnez **Créer**.

> [!NOTE]
> Si vous avez besoin de créer une machine virtuelle à partir d’un fichier VHD, suivez les instructions des articles suivants, [Préparer un modèle de Azure Resource Manager](#connect-the-certification-tool-to-a-vm-image) ou [Déployer une machine virtuelle Azure à l’aide de PowerShell](#how-to-use-powershell-to-consume-the-self-test-api).

Cet article explique comment tester et envoyer une image de machine virtuelle vers la Place de marché commerciale afin de vérifier qu’elle répond aux exigences de publication les plus récentes de la Place de marché Azure.

Effectuez les étapes suivantes avant d’envoyer votre offre de machine virtuelle :

- Déployez une machine virtuelle Azure à l’aide de votre image généralisée : consultez [créer une machine virtuelle à l’aide d’une base approuvée](azure-vm-create-using-approved-base.md) ou [créer une machine virtuelle à l’aide de votre propre image](azure-vm-create-using-own-image.md).
- Effectuez les validations.

## <a name="run-validations"></a>Effectuer les validations

Il existe deux façons d’effectuer des validations sur l’image déployée.

### <a name="use-certification-test-tool-for-azure-certified"></a>Utiliser l’outil Certification Test Tool for Azure Certified

#### <a name="download-and-run-the-certification-test-tool"></a>Télécharger et exécuter l’outil Certification Test

L’outil Certification Test Tool for Azure Certified s’exécute sur une machine Windows locale, mais teste une machine virtuelle Windows ou Linux basée sur Azure. Il vérifie que votre image de machine virtuelle utilisateur peut être utilisée avec Microsoft Azure, autrement dit que les recommandations et conditions requises de la préparation de votre VHD ont été satisfaites.

1. Téléchargez et installez la version la plus récente de l’outil [Certification Test Tool for Azure Certified](https://www.microsoft.com/download/details.aspx?id=44299).
2. Ouvrez l’outil de certification, puis sélectionnez **Démarrer un nouveau test**.
3. Dans l’écran Test Information (Informations sur le test), entrez un nom de test dans le champ **Test Name** (Nom du test) pour la série de tests.
4. Dans le champ Plateforme, sélectionnez **Windows Server** ou **Linux** pour votre machine virtuelle. La plateforme que vous choisissez a une incidence sur le reste des options.
5. Si votre machine virtuelle utilise ce service de base de données, cochez la case **Test pour Azure SQL Database**.

#### <a name="connect-the-certification-tool-to-a-vm-image"></a>Connecter l’outil de certification à une image de machine virtuelle

1. Sélectionnez le mode SSH Authentication (Authentification SSH) : Password Authentication (authentification par mot de passe) ou Key File Authentication (authentification par fichier de clé).
2. Si vous utilisez une authentification par mot de passe, renseignez les champs **Nom du DNS de machine virtuelle**, **Nom d’utilisateur** et **Mot de passe**. Vous pouvez également modifier la valeur par défaut du champ SSH Port (Port SSH).

    :::image type="content" source="media/vm/azure-vm-cert-2.png" alt-text="Affiche la sélection des informations de test de machine virtuelle.":::

3. Si vous utilisez l’authentification par fichier de clé, renseignez les champs VM DNS Name (Nom du DNS de machine virtuelle), User Name (Nom d’utilisateur) et Private key (Clé privée). Vous pouvez également renseigner le champ Passphrase (Phrase secrète) ou modifier la valeur par défaut du champ SSH Port (Port SSH).
4. Saisissez le nom de domaine (DNS) complet de la machine virtuelle (par exemple, MyVMName.Cloudapp.net).
5. Saisissez le **nom d’utilisateur** et le **mot de passe**.

    :::image type="content" source="media/vm/azure-vm-cert-4.png" alt-text="Affiche la sélection du nom d’utilisateur et du mot de passe de la machine virtuelle.":::

6. Sélectionnez **Suivant**.

#### <a name="run-a-certification-test"></a>Exécuter un test de certification

Après avoir fourni les valeurs des paramètres pour votre image de machine virtuelle dans l’outil de certification, sélectionnez Tester la connexion pour créer une connexion valide vers votre machine virtuelle. Une fois la connexion vérifiée, sélectionnez **Next** (Suivant) pour démarrer le test. Une fois le test terminé, les résultats s’affichent dans un tableau. La colonne Status (État) affiche Pass/Fail/Warning (Réussite/Échec/Avertissement) pour chaque test. Si l’un des tests échoue, votre image n’est pas certifiée. Dans ce cas, examinez les exigences et les messages d’échec, apportez les modifications suggérées, puis réexécutez le test.

Une fois le test automatisé terminé, fournissez des informations supplémentaires sur votre image de machine virtuelle sous les deux onglets de l’écran Questionnaire que sont General Assessment (Évaluation générale) et Kernel Customization (Personnalisation du noyau), puis sélectionnez Next (Suivant).

Le dernier écran vous permet de fournir des informations supplémentaires, telles que les informations d’accès SSH pour une image de machine virtuelle Linux, ainsi qu’une explication des éventuels échecs d’évaluation si vous recherchez des exceptions.

Enfin, sélectionnez Generate Report (Générer le rapport) afin de télécharger les résultats de test et les fichiers journaux pour les cas de test exécutés, ainsi que vos réponses au questionnaire.
> [!Note]
> peu d’éditeurs ont des scénarios dans lesquels les machines virtuelles doivent être verrouillées, car des logiciels tels que des pare-feu sont installés sur la machine virtuelle. Dans ce cas, téléchargez l’[outil de test certifié](https://aka.ms/AzureCertificationTestTool) ici et envoyez le rapport dans l’espace partenaires [support](https://aka.ms/marketplacepublishersupport).

## <a name="how-to-use-powershell-to-consume-the-self-test-api"></a>Comment utiliser PowerShell pour consommer l’API d’auto-test

### <a name="on-linux-os"></a>Sur le système d’exploitation Linux

Appelez l’API dans PowerShell :

1. Utilisez la commande Invoke-WebRequest pour appeler l’API.
2. La méthode est POST et le type de contenu est JSON, comme illustré dans la capture d’écran et l’exemple de code suivants.
3. Spécifiez les paramètres de corps au format JSON.

L’exemple suivant montre un appel PowerShell à l’API :

```POWERSHELL
$accesstoken = "token"
$headers = @{ "Authorization" = "Bearer $accesstoken" }
$DNSName = "<Machine DNS Name>"
$UserName = "<User ID>"
$Password = "<Password>"
$OS = "Linux"
$PortNo = "22"
$CompanyName = "ABCD"
$AppID = "<Application ID>"
$TenantId = "<Tenant ID>"

$body = @{
   "DNSName" = $DNSName
   "UserName" = $UserName
   "Password" = $Password
   "OS" = $OS
   "PortNo" = $PortNo
   "CompanyName" = $CompanyName
   "AppID" = $AppID
   "TenantId" = $TenantId
} | ConvertTo-Json

$body

$uri = "URL"

$res = (Invoke-WebRequest -Method "Post" -Uri $uri -Body $body -ContentType "application/json" -Headers $headers).Content
```

<br>Voici un exemple d’appel de l’API dans PowerShell :

[![Screen example for calling the API in PowerShell.](media/vm/call-api-in-powershell.png)](media/vm/call-api-in-powershell.png#lightbox)

<br>À l’aide de l’exemple précédent, vous pouvez récupérer le fichier JSON et l’analyser pour obtenir les détails suivants :

```PowerShell
$resVar = $res | ConvertFrom-Json
$actualresult = $resVar.Response | ConvertFrom-Json

Write-Host "OSName: $($actualresult.OSName)"
Write-Host "OSVersion: $($actualresult.OSVersion)"
Write-Host "Overall Test Result: $($actualresult.TestResult)"

For ($i = 0; $i -lt $actualresult.Tests.Length; $i++) {
   Write-Host "TestID: $($actualresult.Tests[$i].TestID)"
   Write-Host "TestCaseName: $($actualresult.Tests[$i].TestCaseName)"
   Write-Host "Description: $($actualresult.Tests[$i].Description)"
   Write-Host "Result: $($actualresult.Tests[$i].Result)"
   Write-Host "ActualValue: $($actualresult.Tests[$i].ActualValue)"
}
```

<br>Cet exemple d’écran, qui affiche `$res.Content`, montre les détails de vos résultats de test au format JSON :

[![Screen example for calling the API in PowerShell with details of test results.](media/vm/call-api-in-powershell-details.png)](media/vm/call-api-in-powershell-details.png#lightbox)

<br>Voici un exemple de résultats de test JSON affichés dans une visionneuse JSON en ligne (par exemple, [Code Beautify](https://codebeautify.org/jsonviewer) ou [Visionneuse JSON](https://jsonformatter.org/json-viewer)).

![D’autres résultats de test dans une visionneuse JSON en ligne.](media/vm/test-results-json-viewer-1.png)

### <a name="on-windows-os"></a>Sur le système d’exploitation Windows

Appelez l’API dans PowerShell :

1. Utilisez la commande Invoke-WebRequest pour appeler l’API.
2. La méthode est POST et le type de contenu est JSON, comme illustré dans la capture d’écran et l’exemple de code suivants.
3. Créez les paramètres de corps au format JSON.

Cet exemple de code montre un appel PowerShell à l’API :

```PowerShell
$accesstoken = "Get token for your Client AAD App"
$headers = @{ "Authorization" = "Bearer $accesstoken" }
$Body = @{ 
   "DNSName" = "XXXX.westus.cloudapp.azure.com"
   "UserName" = "XXX"
   "Password" = "XXX@123456"
   "OS" = "Windows"
   "PortNo" = "5986"
   "CompanyName" = "ABCD"
   "AppID" = "XXXX-XXXX-XXXX"
   "TenantId" = "XXXX-XXXX-XXXX"
} | ConvertTo-Json

$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers;
$Content = $res | ConvertFrom-Json
```

Ces captures d’écran montrent un exemple d’appel de l’API dans PowerShell :

**Avec clé SSH** :

 :::image type="content" source="media/vm/call-api-with-ssh-key.png" alt-text="Appel de l’API dans PowerShell avec une clé SSH.":::

**Avec mot de passe** :

 :::image type="content" source="media/vm/call-api-with-password.png" alt-text="Appel de l’API dans PowerShell avec un mot de passe.":::

<br>À l’aide de l’exemple précédent, vous pouvez récupérer le fichier JSON et l’analyser pour obtenir les détails suivants :

```PowerShell
$resVar = $res | ConvertFrom-Json
$actualresult = $resVar.Response | ConvertFrom-Json

Write-Host "OSName: $($actualresult.OSName)"
Write-Host "OSVersion: $($actualresult.OSVersion)"
Write-Host "Overall Test Result: $($actualresult.TestResult)"

For ($i = 0; $i -lt $actualresult.Tests.Length; $i++) {
   Write-Host "TestID: $($actualresult.Tests[$i].TestID)"
   Write-Host "TestCaseName: $($actualresult.Tests[$i].TestCaseName)"
   Write-Host "Description: $($actualresult.Tests[$i].Description)"
   Write-Host "Result: $($actualresult.Tests[$i].Result)"
   Write-Host "ActualValue: $($actualresult.Tests[$i].ActualValue)"
}
```

<br>Cet écran, qui affiche `$res.Content`, montre les détails de vos résultats de test au format JSON :

 :::image type="content" source="media/vm/test-results-json-format.png" alt-text="Détails des résultats de test au format JSON.":::

<br>Voici un exemple de résultats de test affichés dans une visionneuse JSON en ligne (par exemple, [Code Beautify](https://codebeautify.org/jsonviewer) ou [Visionneuse JSON](https://jsonformatter.org/json-viewer)).

![Résultats de test dans une visionneuse JSON en ligne.](media/vm/test-results-json-viewer-2.png)

## <a name="how-to-use-curl-to-consume-the-self-test-api-on-linux-os"></a>Comment utiliser cURL pour consommer l’API d’auto-test sur le système d’exploitation Linux

Dans cet exemple, curl est utilisée pour effectuer un appel d’API POST à Azure Active Directory et à la machine virtuelle auto-hébergée.

1. Demander un jeton Azure AD pour s’authentifier auprès d’une machine virtuelle auto-hébergée

   Vérifiez que les valeurs correctes sont remplacées dans la demande curl.

   ```JSON
   curl --location --request POST 'https://login.microsoftonline.com/{TENANT_ID}/oauth2/token' \
   --header 'Content-Type: application/x-www-form-urlencoded' \
   --data-urlencode 'grant_type=client_credentials' \
   --data-urlencode 'client_id={CLIENT_ID} ' \
   --data-urlencode 'client_secret={CLIENT_SECRET}' \
   --data-urlencode 'resource=https://management.core.windows.net'
   ```

   Voici un exemple de réponse de la demande :

   ```JSON
   {
       "token_type": "Bearer",
       "expires_in": "86399",
       "ext_expires_in": "86399",
       "expires_on": "1599663998",
       "not_before": "1599577298",
       "resource": "https://management.core.windows.net",
       "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJS…"
   }
   ```

2. Envoyer une demande pour la machine virtuelle d’auto-test

   Vérifiez que le jeton du porteur et les paramètres sont remplacés par les valeurs correctes.

   ```JSON
   curl --location --request POST 'https://isvapp.azurewebsites.net/selftest-vm' \
   --header 'Content-Type: application/json' \
   --header 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJS…' \
   --data-raw '{
       "DNSName": "avvm1.eastus.cloudapp.azure.com",
       "UserName": "azureuser",
       "Password": "SECURE_PASSWORD_FOR_THE_SSH_INTO_VM",
       "OS": "Linux",
       "PortNo": "22",
       "CompanyName": "COMPANY_NAME",
       "AppId": "CLIENT_ID_SAME_AS_USED_FOR_AAD_TOKEN ",
       "TenantId": "TENANT_ID_SAME_AS_USED_FOR_AAD_TOKEN"
   }'
   ```

   Exemple de réponse de l’appel d’API de la machine virtuelle d’auto-test

   ```JSON
   {
       "TrackingId": "9bffc887-dd1d-40dd-a8a2-34cee4f4c4c3",
       "Response": "{\"SchemaVersion\":1,\"AppCertificationCategory\":\"Microsoft Single VM Certification\",\"ProviderID\":\"050DE427-2A99-46D4-817C-5354D3BF2AE8\",\"OSName\":\"Ubuntu 18.04\",\"OSDistro\":\"Ubuntu 18.04.5 LTS\",\"KernelVersion\":\"5.4.0-1023-azure\",\"KernelFullVersion\":\"Linux version 5.4.0-1023-azure (buildd@lgw01-amd64-053) (gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)) #23~18.04.1-Ubuntu SMP Thu Aug 20 14:46:48 UTC 2020\\n\",\"OSVersion\":\"18.04\",\"CreatedDate\":\"09/08/2020 01:13:47\",\"TestResult\":\"Pass\",\"APIVersion\":\"1.5\",\"Tests\":[{\"TestID\":\"48\",\"TestCaseName\":\"Bash History\",\"Description\":\"Bash history files should be cleared before creating the VM image.\",\"Result\":\"Passed\",\"ActualValue\":\"No file Exist\",\"RequiredValue\":\"1024\"},{\"TestID\":\"39\",\"TestCaseName\":\"Linux Agent Version\",\"Description\":\"Azure Linux Agent Version 2.2.41 and above should be installed.\",\"Result\":\"Passed\",\"ActualValue\":\"2.2.49\",\"RequiredValue\":\"2.2.41\"},{\"TestID\":\"40\",\"TestCaseName\":\"Required Kernel Parameters\",\"Description\":\"Verifies the following kernel parameters are set console=ttyS0, earlyprintk=ttyS0, rootdelay=300\",\"Result\":\"Warning\",\"ActualValue\":\"Missing Parameter: rootdelay=300\\r\\nMatched Parameter: console=ttyS0,earlyprintk=ttyS0\",\"RequiredValue\":\"console=ttyS0#earlyprintk=ttyS0#rootdelay=300\"},{\"TestID\":\"41\",\"TestCaseName\":\"Swap Partition on OS Disk\",\"Description\":\"Verifies that no Swap partitions are created on the OS disk.\",\"Result\":\"Passed\",\"ActualValue\":\"No. of Swap Partitions: 0\",\"RequiredValue\":\"swap\"},{\"TestID\":\"42\",\"TestCaseName\":\"Root Partition on OS Disk\",\"Description\":\"It is recommended that a single root partition is created for the OS disk.\",\"Result\":\"Passed\",\"ActualValue\":\"Root Partition: 1\",\"RequiredValue\":\"1\"},{\"TestID\":\"44\",\"TestCaseName\":\"OpenSSL Version\",\"Description\":\"OpenSSL Version should be >=0.9.8.\",\"Result\":\"Passed\",\"ActualValue\":\"1.1.1\",\"RequiredValue\":\"0.9.8\"},{\"TestID\":\"45\",\"TestCaseName\":\"Python Version\",\"Description\":\"Python version 2.6+ is highly recommended. \",\"Result\":\"Passed\",\"ActualValue\":\"2.7.17\",\"RequiredValue\":\"2.6\"},{\"TestID\":\"46\",\"TestCaseName\":\"Client Alive Interval\",\"Description\":\"It is recommended to set ClientAliveInterval to 180. On the application need, it can be set between 30 to 235. \\nIf you are enabling the SSH for your end users this value must be set as explained.\",\"Result\":\"Warning\",\"ActualValue\":\"120\",\"RequiredValue\":\"ClientAliveInterval 180\"},{\"TestID\":\"49\",\"TestCaseName\":\"OS Architecture\",\"Description\":\"Only 64-bit operating system should be supported.\",\"Result\":\"Passed\",\"ActualValue\":\"x86_64\\n\",\"RequiredValue\":\"x86_64,amd64\"},{\"TestID\":\"50\",\"TestCaseName\":\"Security threats\",\"Description\":\"Identifies OS with recent high profile vulnerability that may need patching.  Ignore warning if system was patched as appropriate.\",\"Result\":\"Passed\",\"ActualValue\":\"Ubuntu 18.04\",\"RequiredValue\":\"OS impacted by GHOSTS\"},{\"TestID\":\"51\",\"TestCaseName\":\"Auto Update\",\"Description\":\"Identifies if Linux Agent Auto Update is enabled or not.\",\"Result\":\"Passed\",\"ActualValue\":\"# AutoUpdate.Enabled=y\\n\",\"RequiredValue\":\"Yes\"},{\"TestID\":\"52\",\"TestCaseName\":\"SACK Vulnerability patch verification\",\"Description\":\"Checks if the running Kernel Version has SACK vulnerability patch.\",\"Result\":\"Passed\",\"ActualValue\":\"Ubuntu 18.04.5 LTS,Linux version 5.4.0-1023-azure (buildd@lgw01-amd64-053) (gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)) #23~18.04.1-Ubuntu SMP Thu Aug 20 14:46:48 UTC 2020\",\"RequiredValue\":\"Yes\"}]}"
   }
   ```

## <a name="next-steps"></a>Étapes suivantes

- Connectez-vous à l’[Espace partenaires](https://go.microsoft.com/fwlink/?linkid=2166002).
