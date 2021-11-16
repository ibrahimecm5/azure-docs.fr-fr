---
title: Vue d’ensemble des applications de machine virtuelle dans Azure Compute Gallery (préversion)
description: En savoir plus sur les packages d’application de machine virtuelle dans une galerie Azure Compute Gallery.
author: cynthn
ms.service: virtual-machines
ms.subservice: gallery
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 11/02/2021
ms.author: cynthn
ms.reviewer: amjads
ms.custom: ''
ms.openlocfilehash: b43d2c5f20afa67c40115430903b97d49fef4e08
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478996"
---
# <a name="vm-applications-overview-preview"></a>Vue d’ensemble des applications de machine virtuelle (préversion)

Les applications de machine virtuelle sont un type de ressource dans Azure Compute Gallery (anciennement Shared Image Gallery) qui simplifie la gestion, le partage et la distribution globale des applications pour vos machines virtuelles.

> [!IMPORTANT]
> Les **applications de machine virtuelle dans Azure Compute Gallery** sont actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Si vous pouvez créer une image d’une machine virtuelle avec des applications préinstallées, vous devez mettre à jour votre image chaque fois que vous modifiez l’application. En séparant l’installation de votre application de vos images de machine virtuelle, il n’est pas nécessaire de publier une nouvelle image pour chaque modification de ligne de code.

Les packages d’application offrent des avantages par rapport aux autres méthodes de déploiement et de packaging :

- Regroupement et contrôle de version de vos packages

- Les applications de machine virtuelle peuvent être répliquées globalement pour être plus proches de votre infrastructure. vous n’avez donc pas besoin d’utiliser AzCopy ou d’autres mécanismes de copie de stockage pour copier les bits entre les régions Azure.

- Partage avec d’autres utilisateurs via le contrôle d’accès en fonction du rôle (RBAC)

- Prise en charge des machines virtuelles et des groupes identiques flexibles et uniformes

- Si vous avez appliqué des règles de groupe de sécurité réseau (NSG) sur votre machine virtuelle ou votre groupe identique, le téléchargement des packages à partir d’un référentiel Internet peut ne pas être possible. De plus, avec les comptes de stockage, le téléchargement de packages sur des machines virtuelles verrouillées nécessite la configuration de liens privés.
- Les applications de machine virtuelle peuvent être utilisées avec la stratégie [DeployIfNotExists](/azure/governance/policy/concepts/effects).


## <a name="what-are-vm-app-packages"></a>Que sont les packages d’application de machine virtuelle ?

Les packages d’application de machine virtuelle utilisent plusieurs types de ressources :

| Ressource | Description|
|----------|------------|
| **Azure Compute Gallery** | Une galerie est un référentiel pour la gestion et le partage de packages d’applications. Les utilisateurs peuvent partager la ressource de galerie et toutes les ressources enfants sont partagées automatiquement. Le nom de la galerie doit être unique par abonnement. Par exemple, vous pouvez avoir une galerie pour stocker toutes vos images de système d’exploitation et une autre galerie pour stocker toutes vos applications de machine virtuelle.|
| **Application de machine virtuelle** | Il s’agit de la définition de votre application de machine virtuelle. Il s’agit d’une ressource *logique* qui stocke les métadonnées communes pour toutes les versions qu’elle contient. Par exemple, vous pouvez avoir une définition d’application pour Apache Tomcat et y avoir plusieurs versions. |
| **Version d’application de machine virtuelle** | Il s’agit de la ressource déployable. Vous pouvez répliquer globalement vos versions d’application de machine virtuelle vers des régions cibles plus proches de votre infrastructure de machine virtuelle. La version de l’application de machine virtuelle doit être répliquée dans une région avant d’être déployée sur une machine virtuelle de cette région. |


## <a name="limitations"></a>Limites

- **Pas plus de 3 réplicas par région** : Lors de la création d’une version d’application de machine virtuelle, le nombre maximal de réplicas par région est de trois. 

- **Nouvelle tentative d’installations ayant échoué** : Actuellement, la seule façon de réessayer une installation ayant échoué consiste à supprimer l’application du profil, puis à l’ajouter à nouveau.

- **Seulement 5 applications par machine virtuelle** : Pas plus de 5 applications peuvent être déployées sur une machine virtuelle à tout moment.

- **1 Go de taille d’application** : La taille de fichier maximale d’une version d’application est de 1 Go. 

- **Aucune garantie au redémarrage dans votre script** : Si votre script requiert un redémarrage, il est recommandé de placer cette application en dernier lors du déploiement. Alors que le code tente de gérer les redémarrages, il peut échouer.

- **Nécessite un agent de machine virtuelle** : L’agent de machine virtuelle doit exister sur la machine virtuelle et être en mesure de recevoir des états d’objectif.

- **Plusieurs versions d’une même application sur la même machine virtuelle** : Vous ne pouvez pas avoir plusieurs versions de la même application sur une machine virtuelle.


## <a name="cost"></a>Coût

Il n’y a pas de frais supplémentaires pour l’utilisation de packages d’application de machine virtuelle, mais vous serez facturé pour les ressources suivantes :

- Coûts de stockage du stockage de chaque package et de tous les réplicas. 
- Coûts de sortie de réseau pour la réplication de la première version d’image de la région source vers les régions répliquées. Les réplicas suivants sont gérés au sein de la région, donc aucun coût supplémentaire n’est prévu. 

Pour plus d’informations sur la sortie du réseau, consultez [Détails de la tarification de la bande passante](https://azure.microsoft.com/pricing/details/bandwidth/).


## <a name="vm-applications"></a>Applications de machine virtuelle

La ressource d’application de machine virtuelle définit les éléments suivants sur votre application de machine virtuelle :

- La galerie Azure Compute Gallery où l’application de machine virtuelle est stockée
- Nom de l’application
- Type de système d’exploitation pris en charge comme Linux ou Windows
- Description de l’application de machine virtuelle

## <a name="vm-application-versions"></a>Versions d’application de machine virtuelle

Les versions d’applications de machine virtuelle sont des ressources déployables. Les versions sont définies avec les propriétés suivantes :
- Numéro de version
- Lien vers le fichier de package d’application dans un compte de stockage
- Chaîne d’installation pour l’installation de l’application
- Chaîne de suppression pour montrer comment supprimer correctement l’application
- Nom du fichier de package à utiliser lors du téléchargement sur la machine virtuelle
- Nom du fichier de configuration à utiliser pour configurer l’application sur la machine virtuelle
- Un lien vers le fichier de configuration pour l’application de machine virtuelle
- Chaîne de mise à jour pour savoir comment mettre à jour l’application de machine virtuelle vers une version plus récente
- Date de fin de vie. Les dates de fin de vie sont fournies à titre d’information. Vous serez toujours en mesure de déployer les versions d’une application de machine virtuelle au-delà de la date de fin de vie.
- Exclure de la plus récente. Vous pouvez empêcher l’utilisation d’une version comme version la plus récente de l’application. 
- Régions cibles pour la réplication
- Nombre de réplicas par région

## <a name="download-directory"></a>Télécharger le répertoire 
 
Les emplacements de téléchargement du package d’application et des fichiers de configuration sont les suivants :
  
- Linux : `/var/lib/waagent/Microsoft.CPlat.Core.VMApplicationManagerLinux/<appname>/<app version> `
- Windows : `C:\Packages\Plugins\Microsoft.CPlat.Core.VMApplicationManagerWindows\1.0.4\Downloads\<appname>\<app version> `


Les commandes d’installation/mise à jour/suppression doivent être écrites en supposant que le package d’application et le fichier de configuration se trouvent dans le répertoire actuel.

## <a name="file-naming"></a>Dénomination du fichier

Pendant la préversion, lorsque le fichier d’application est téléchargé sur la machine virtuelle, le nom de fichier est le même que celui que vous utilisez lorsque vous créez l’application de machine virtuelle. Par exemple, si je nomme mon application de machine virtuelle `myApp`, le fichier qui sera téléchargé sur la machine virtuelle sera également nommé `myApp`, quel que soit le nom de fichier utilisé dans le compte de stockage. Si vous avez également un fichier de configuration pour l’application de machine virtuelle, ce fichier est le nom de l’application avec `_config` ajouté. Si `myApp` a un fichier de configuration, il sera nommé `myApp_config`.

Par exemple, si je nomme mon application de machine virtuelle `myApp` lorsque je la crée dans la Galerie, mais qu’elle est stockée en tant que `myApplication.exe` dans le compte de stockage, le nom de fichier est `myApp` lorsqu’elle est téléchargée sur la machine virtuelle. Ma chaîne d’installation doit commencer par la dénomination du fichier pour qu’il s’exécute sur la machine virtuelle (par exemple myApp.exe).

Les commandes d’installation, de mise à jour et de suppression doivent être écrites en tenant compte de cela. 
 
## <a name="command-interpreter"></a>Interpréteur de commande  

Les interpréteurs de commandes par défaut sont les suivants :
- Linux : `/bin/sh` 
- Windows : `cmd.exe`

Il est possible d’utiliser un interpréteur différent, à condition qu’il soit installé sur la machine, en appelant l’exécutable et en lui transmettant la commande. Par exemple, pour que votre commande s’exécute dans PowerShell sur Windows au lieu de cmd, vous pouvez passer `powershell.exe -Command '<powershell commmand>'`
  

## <a name="how-updates-are-handled"></a>Manière dont les mises à jour sont gérées

Lorsque vous mettez à jour une version de l’application, la commande de mise à jour que vous avez fournie pendant le déploiement sera utilisée. Si la version mise à jour n’a pas de commande de mise à jour, la version actuelle sera supprimée et la nouvelle version sera installée. 

Les commandes de mise à jour doivent être écrites dans l’attente de savoir s’il s’agit d’une mise à jour à partir d’une ancienne version de l’application de machine virtuelle.


## <a name="tips-for-creating-vm-applications-on-linux"></a>Astuces pour la création d’applications de machine virtuelle sur Linux 

Les applications tierces pour Linux peuvent être empaquetées de plusieurs façons. Étudions comment gérer la création de commandes d’installation pour les raisons les plus courantes.

### <a name="tar-and-gz-files"></a>fichiers .tar et.gz 

Il s’agit d’archives compressées qui peuvent simplement être extraites à un emplacement souhaité. Vérifiez les instructions d’installation du package d’origine au cas où elles doivent être extraites dans un emplacement spécifique. Si le fichier. tar.gz contient du code source, reportez-vous aux instructions relatives au package pour savoir comment procéder à l’installation à partir de la source. 

Exemple de commande d’installation pour installer `golang` sur une machine Linux :

```bash
tar -C /usr/local -xzf go_linux
```

Exemple de commande de suppression :

```bash
rm -rf /usr/local/go
```

### <a name="deb-rpm-and-other-platform-specific-packages"></a>.deb, .rpm et autres packages spécifiques à la plateforme 
Vous pouvez télécharger des packages individuels pour des gestionnaires de packages spécifiques à la plateforme, mais ils ne contiennent généralement pas toutes les dépendances. Pour ces fichiers, vous devez également inclure toutes les dépendances dans le package d’application, ou faire en sorte que le gestionnaire de package système télécharge les dépendances via les référentiels disponibles pour la machine virtuelle. Si vous utilisez une machine virtuelle avec accès limité à Internet, vous devez empaqueter toutes les dépendances vous-même.


La mesure des dépendances peut être un peu délicate. Il existe des outils tiers qui peuvent vous montrer l’ensemble de l’arborescence des dépendances. 

Sur Ubuntu, vous pouvez exécuter `apt-get install <name> --simulate` pour afficher tous les packages qui seront installés pour la commande `apt-get install <name>`. Vous pouvez ensuite utiliser cette sortie pour télécharger tous les fichiers .deb afin de créer une archive utilisable en tant que package d’application. L’inconvénient de cette méthode est qu’elle n’affiche pas les dépendances qui sont déjà installées sur la machine virtuelle.
 
Exemple : Pour créer un package d’application de machine virtuelle pour installer PowerShell pour Ubuntu, exécutez la commande `apt-get install powershell --simulate` sur une nouvelle machine virtuelle Ubuntu. Vérifiez la sortie de la ligne **Les NOUVEAUX packages suivants seront installés** qui répertorie les packages suivants :
- `liblttng-ust-ctl4` 
- `liblttng-ust0` 
- `liburcu6` 
- `powershell`. 

Téléchargez ces fichiers à l’aide de `apt-get download` et créez une archive tar avec tous les fichiers au niveau de la racine. Cette archive tar sera le fichier de package d’application. Dans ce cas, la commande d’installation est :

```bash
tar -xf powershell && dpkg -i ./liblttng-ust-ctl4_2.10.1-1_amd64.deb ./liburcu6_0.10.1-1ubuntu1_amd64.deb ./liblttng-ust0_2.10.1-1_amd64.deb ./powershell_7.1.2-1.ubuntu.18.04_amd64.deb
```

Et la commande de suppression est :

```bash
dpkg -r powershell && apt autoremove
```

Utilisez `apt autoremove` plutôt que de tenter explicitement de supprimer toutes les dépendances. Vous avez peut-être installé d’autres applications avec des dépendances qui se chevauchent, et dans ce cas, une commande de suppression explicite échouerait.


Si vous ne souhaitez pas résoudre les dépendances vous-même et que apt/rpm est en mesure de se connecter aux référentiels, vous pouvez installer une application avec un seul fichier .deb/.rpm et laisser apt/rpm gérer les dépendances.

Exemple de commande d’installation :

```bash
dpkg -i <appname> || apt --fix-broken install -y
```
 
## <a name="tips-for-creating-vm-applications-on-windows"></a>Astuces pour la création d’applications de machine virtuelle sur Windows 

La plupart des applications tierces dans Windows sont disponibles en tant que programmes d’installation .exe ou .msi. Certaines sont également disponibles sous forme d’extraction et d’exécution de fichiers zip. Examinons les meilleures pratiques pour chacune d’entre elles.


### <a name="exe-installer"></a>Programme d'installation .exe 

Les exécutables du programme d’installation lancent généralement une interface utilisateur (IU) et requièrent que quelqu’un clique sur l’interface utilisateur. Si le programme d’installation prend en charge un paramètre de mode silencieux, il doit être inclus dans votre chaîne d’installation. 

Cmd.exe s’attend également à ce que les fichiers exécutables aient l’extension .exe. Vous devez donc renommer le fichier pour avoir l’extension .exe.  

Si je voulais créer un package d’application de machine virtuelle pour myApp.exe, qui est fourni comme fichier exécutable, mon application de machine virtuelle serait appelée « myApp ». J’écris donc la commande en supposant que le package d’application se trouve dans le répertoire actif :

```
"move .\\myApp .\\myApp.exe & myApp.exe /S -config myApp_config" 
```
 
Si le fichier exécutable du programme d’installation ne prend pas en charge un paramètre de désinstallation, vous pouvez parfois rechercher le registre sur un ordinateur de test pour savoir où se trouve le programme de désinstallation. 

Dans le registre, la chaîne de désinstallation est stockée dans `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\<installed application name>\UninstallString`. J’utilise donc le contenu comme commande de suppression :

```
'\"C:\\Program Files\\myApp\\uninstall\\helper.exe\" /S'
```

### <a name="msi-installer"></a>Programme d’installation .msi 

Pour l’exécution de la ligne de commande des programmes d’installation `.msi`, les commandes permettant d’installer ou de supprimer une application doivent utiliser `msiexec`. En général, `msiexec` s’exécute en tant que processus distinct et `cmd` n’attend pas qu’il se termine, ce qui peut entraîner des problèmes lors de l’installation de plusieurs applications de machine virtuelle.  La commande `start` peut être utilisée avec `msiexec` pour s’assurer que l’installation se termine avant que la commande ne retourne. Par exemple :

```
start /wait %windir%\\system32\\msiexec.exe /i myapp /quiet /forcerestart /log myapp_install.log
```

Exemple de commande de suppression :

```
start /wait %windir%\\system32\\msiexec.exe /x $appname /quiet /forcerestart /log ${appname}_uninstall.log
```

### <a name="zipped-files"></a>Fichiers compressés 

Pour les fichiers .zip ou d’autres fichiers compressés, décompressez simplement le contenu du package d’application vers la destination souhaitée. 

Exemple de commande d’installation :

```
mkdir C:\\myapp && powershell.exe -Command \"Expand-Archive -Path myapp -DestinationPath C:\\myapp\" 
```

Exemple de commande de suppression :

```
rmdir /S /Q C:\\myapp
```


## <a name="troubleshooting-during-preview"></a>Résolution des problèmes pendant la version préliminaire

Pendant la préversion, l’extension de l’application de machine virtuelle retourne toujours un succès, qu’une application de machine virtuelle ait échoué lors de l’installation, de la mise à jour ou de la suppression. L’extension d’application de machine virtuelle signalera uniquement l’état de l’extension comme un échec en cas de problème avec l’extension ou l’infrastructure sous-jacente. Pour savoir si une application de machine virtuelle particulière a été correctement ajoutée à l’instance de machine virtuelle, vérifiez le message de l’extension VMApplication.

Pour en savoir plus sur l’obtention de l’état des extensions de machine virtuelle, consultez [Extensions et fonctionnalités de machine virtuelle pour Windows](extensions/features-windows.md#view-extension-status).

Pour connaître l’état des extensions de machine virtuelle, utilisez [Get-AzVM](/powershell/module/az.compute/get-azvm) :

```azurepowershell-interactive
Get-AzVM -name <VMSS name> -ResourceGroupName <resource group name> -InstanceView | convertto-json  
```

Pour connaître l’état des extensions de groupe de machines virtuelles identiques (VMSS), utilisez [Get-AzVMSS](/powershell/module/az.compute/get-azvmss) :

```azurepowershell-interactive
Get-AzVmss -name <VMSS name> -ResourceGroupName <resource group name> -InstanceView | convertto-json  
```

## <a name="error-messages"></a>Messages d’erreur

| Message | Description |
|--|--|
| La version actuelle {name} de l'application de machine virtuelle a été dépréciée le {date}. | Vous avez essayé de déployer une version de l’application de machine virtuelle qui a déjà été dépréciée. Essayez d’utiliser `latest` au lieu de spécifier une version spécifique. |
| La version actuelle {name} de l'application de machine virtuelle prend en charge le système d'exploitation {OS}, alors que le système d'exploitation de OSDisk actuel est {OS}. | Vous avez essayé de déployer une application Linux sur une instance Windows, ou vice versa. |
| Le nombre maximal d’applications de machine virtuelle (max=5, current={count}) a été dépassé. Utilisez moins d’applications et relancez la requête. | Actuellement, nous ne prenons en charge que cinq applications de machine virtuelle par machine virtuelle ou VMSS. |
| Plus d'un VMApplication a été spécifié avec le même packageReferenceId. | La même application a été spécifiée plusieurs fois. |
| L’abonnement n’est pas autorisé à accéder à cette image. | L’abonnement n’a pas accès à cette version de l’application. |
| Le compte de stockage n’existe pas dans les arguments. | Il n'existe aucune application pour cet abonnement. |
| L’image de plateforme {image} n’est pas disponible. Vérifiez que tous les champs du profil de stockage sont corrects. Pour plus de détails sur les informations de profil de stockage, consultez https://aka.ms/storageprofile. | L'application n'existe pas. |
| L’image de galerie {image} n’est pas disponible dans la région {region}. Contactez le propriétaire de l’image à répliquer dans cette région, ou modifiez la région demandée. | La version de l’application de la galerie existe, mais elle n’a pas été répliquée dans cette région. |
| La signature d’accès partagé n’est pas valide pour l’URI source {uri}. | Une erreur `Forbidden` a été reçue à partir du stockage lors de la tentative de récupération d’informations sur l’URL (mediaLink ou defaultConfigurationLink). |
| L’objet blob référencé par l’URI source {uri} n’existe pas. | L’objet blib fourni pour les propriétés mediaLink ou defaultConfigurationLink n’existe pas. |
| Impossible d’accéder à l’URL de la version de l’application de la galerie {url} en raison de l’erreur suivante : nom distant introuvable. Assurez-vous que l’objet blob existe et qu’il est accessible publiquement ou qu’il s’agit d’une URL SAS avec des privilèges de lecture. | Le cas le plus probable est qu’un URI SAS avec des privilèges de lecture n’a pas été fourni. |
| Impossible d’accéder à l’URL de la version de l’application de la galerie {url} en raison de l’erreur suivante : {error description}. Assurez-vous que l’objet blob existe et qu’il est accessible publiquement ou qu’il s’agit d’une URL SAS avec des privilèges de lecture. | Un problème est survenu au niveau de l’objet blob de stockage fourni. La description de l’erreur fournit plus d’informations. |
| L’opération {operationName} n’est pas autorisée sur {application}, car elle est marquée pour suppression. Vous pouvez uniquement réessayer l’opération de suppression (ou attendre la fin de l’opération en cours). | Tentative de mise à jour d’une application en cours de suppression. |
| La valeur {value} du paramètre « galleryApplicationVersion.properties.publishingProfile.replicaCount » est hors limites. La valeur doit être comprise entre 1 et 3 inclus. | Uniquement entre 1 et 3 réplicas sont autorisés pour les versions de VMApplication. |
| La modification de la propriété « galleryApplicationVersion.properties.publishingProfile.manageActions.install » n’est pas autorisée. (ou mise à jour, suppression) | Il n’est pas possible de modifier les actions de gestion sur un VmApplication existant. Une nouvelle version de VmApplication doit être créée. |
| La modification de la propriété « galleryApplicationVersion.properties.publishingProfile.settings.packageFileNamel » n’est pas autorisée. (ou configFileName) | Il n’est pas possible de modifier les paramètres, tels que le nom du fichier de package ou le nom du fichier de configuration. Une nouvelle version de VmApplication doit être créée. |
| L’objet blob référencé par l’URI source {uri} est trop grand : taille = {size}. La taille maximale d’objet blob autorisée est de 1 Go. | La taille maximale d’un objet blob référencé par mediaLink ou defaultConfigurationLink est actuellement de 1 Go. |
| L’objet blob référencé par l’URI source {uri} est vide. | Un objet blob vide a été référencé. |
| Le type d’objet blob {type} n’est pas pris en charge pour l’opération {operation}. Seuls les objets blob de pages et les objets blob de blocs sont pris en charge. | VmApplications prend uniquement en charge les objets blob de pages et les objets blob de blocs. |
| La signature d’accès partagé n’est pas valide pour l’URI source {uri}. | L’URI SAS fourni pour mediaLink ou defaultConfigurationLink n’est pas un URI SAS valide. |
| Impossible de spécifier {region} dans les régions cibles, car la fonctionnalité requise {featureName} est manquante dans l’abonnement. Inscrivez votre abonnement avec la fonctionnalité requise ou supprimez la région de la liste des régions cibles. | Pour utiliser VmApplications dans certaines régions restreintes, l’indicateur de fonctionnalité doit être inscrit pour cet abonnement. |
| Les régions {regions} de profil de publication de version d’image de galerie doivent contenir l’emplacement {location} de version d’image. | La liste des régions pour la réplication doit contenir l’emplacement de la version de l’application. |
| Les régions en double ne sont pas autorisées dans les régions de publication cibles. | Les régions de publication peuvent ne pas avoir de doublons. |
| Les ressources de la version de l’application de la galerie ne prennent actuellement pas en charge le chiffrement. | La propriété de chiffrement pour les régions cibles n’est pas prise en charge pour les applications de machine virtuelle |
| Le nom de l’entité ne correspond pas au nom figurant dans l’URL de la demande. | La version d’application de la galerie spécifiée dans l’URL de la demande ne correspond pas à celle spécifiée dans le corps de la demande. |
| Le nom de la version de l’application de la galerie n’est pas valide. Le nom de la version de l’application doit respecter le format Major(int32).Minor(int32).Patch(int32), où int est compris entre 0 et 2 147 483 647 (inclus). Par exemple, 1.0.0, 2018.12.1, etc. | La version de l’application de la galerie doit respecter le format spécifié. |



## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [créer et déployer des packages d’application de machine virtuelle](vm-applications-how-to.md).


