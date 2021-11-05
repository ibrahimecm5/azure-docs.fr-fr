---
title: Se connecter à une machine virtuelle Linux dans Azure à l’aide d’Azure Active Directory (préversion)
description: Connexion Azure AD sur une machine virtuelle Azure exécutant Linux
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 10/21/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sandeo
ms.custom: references_regions, devx-track-azurecli, subject-rbac-steps
ms.collection: M365-identity-device-management
ms.openlocfilehash: aeca09f5763cf11edc13cecd2df0c267fad8a23d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131049745"
---
# <a name="preview-login-to-a-linux-virtual-machine-in-azure-with-azure-active-directory-using-ssh-certificate-based-authentication"></a>Préversion : Se connecter à une machine virtuelle Linux dans Azure avec Azure Active Directory à l’aide de l’authentification par certificat SSH

Pour améliorer la sécurité des machines virtuelles Linux dans Azure, vous pouvez intégrer l’authentification Azure Active Directory (Azure AD). Vous pouvez désormais utiliser Azure AD comme plateforme d’authentification principale et autorité de certification pour vous connecter par protocole SSH à une machine virtuelle Linux avec Azure AD et l’authentification par certificat SSH. Cette fonctionnalité permet aux organisations de contrôler et d’appliquer de manière centralisée le contrôle d’accès en fonction du rôle (RBAC) Azure et les stratégies d’accès conditionnel qui gèrent l’accès aux machines virtuelles. Cet article vous explique comment créer et configurer une machine virtuelle Linux et comment vous connecter avec Azure AD en utilisant l’authentification par certificat SSH.

> [!IMPORTANT]
> Pour le moment, cette fonctionnalité est disponible dans la version préliminaire publique. [La version précédente qui utilisait le flux de code d’appareil a été dépréciée le 15 août 2021](../../virtual-machines/linux/login-using-aad.md). Pour migrer de l’ancienne version vers cette dernière, consultez la section [Migration à partir de la préversion précédente](#migration-from-previous-preview).
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Utilisez cette fonctionnalité sur une machine virtuelle de test que vous prévoyez d’abandonner après le test. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

L’utilisation d’Azure AD avec l’authentification par certificat SSH pour se connecter aux machines virtuelles Linux dans Azure présente de nombreux avantages en termes de sécurité, notamment :

- Utilisez vos informations d’identification Azure AD pour vous connecter aux machines virtuelles Linux Azure.
- Bénéficiez d’une authentification par clé SSH sans avoir à distribuer de clés SSH aux utilisateurs ni à approvisionner de clés publiques SSH sur toute machine virtuelle Linux Azure que vous déployez. Cette expérience est beaucoup plus simple que de devoir s’inquiéter de la prolifération de clés publiques SSH obsolètes susceptibles d’entraîner un accès non autorisé.
- Réduisez votre dépendance à l’égard des comptes administrateur locaux, du vol d’informations d’identification et des informations d’identification faibles.
- Les stratégies portant sur la complexité et la durée de vie du mot de passe qui sont configurées pour Azure AD contribuent également à sécuriser les machines virtuelles Linux.
- Avec le contrôle d’accès en fonction du rôle Azure, spécifiez qui peut se connecter à une machine virtuelle en tant qu’utilisateur standard ou avec des privilèges d’administrateur. Lorsque des utilisateurs rejoignent ou quittent votre équipe, vous pouvez mettre à jour la stratégie Azure RBAC pour la machine virtuelle pour accorder les accès appropriés. Lorsque des employés quittent votre organisation et que leur compte d’utilisateur est désactivé ou supprimé d’Azure AD, ils n’ont plus accès à vos ressources.
- Avec l’accès conditionnel, configurez des stratégies pour exiger une authentification multifacteur et/ou exiger que l’appareil client que vous utilisez pour vous connecter par protocole SSH soit un appareil géré (par exemple : appareil conforme ou jointure hybride Azure AD) avant de pouvoir vous connecter par protocole SSH à des machines virtuelles Linux. 
- Utilisez les stratégies de déploiement et d’audit Azure pour exiger une connexion Azure AD pour les machines virtuelles Linux et pour signaler l’utilisation de comptes locaux non approuvés sur les machines virtuelles.
- La connexion aux machines virtuelles Linux avec Azure Active Directory fonctionne également pour les clients qui utilisent des services de fédération.

## <a name="supported-linux-distributions-and-azure-regions"></a>Régions Azure et distributions Linux prises en charge

Les distributions Linux suivantes sont actuellement prises en charge dans la préversion de cette fonctionnalité lorsqu’elle est déployée dans une région prise en charge :

| Distribution | Version |
| --- | --- |
| CentOS | CentOS 7, CentOS 8 |
| Debian | Debian 9, Debian 10 |
| OpenSUSE | openSUSE Leap 42.3, openSUSE Leap 15.1+ |
| RedHat Enterprise Linux | RHEL 7.4 à RHEL 7.10, RHEL 8.3+ |
| SUSE Linux Enterprise Server | SLES 12, SLES 15.1+ |
| Serveur Ubuntu | Ubuntu Server 16.04 à Ubuntu Server 20.04 |

Les régions Azure suivantes sont actuellement prises en charge dans la préversion de cette fonctionnalité :

- Azure Global

 
L’utilisation de cette extension n’est pas prise en charge sur les clusters Azure Kubernetes Service (AKS). Pour plus d’informations, consultez [Stratégies de support pour AKS](../../aks/support-policies.md).

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, ce tutoriel demande au minimum la version 2.22.1 d’Azure CLI. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli).

## <a name="requirements-for-login-with-azure-ad-using-ssh-certificate-based-authentication"></a>Configuration requise pour la connexion avec Azure AD à l’aide de l’authentification par certificat SSH

Pour activer la connexion Azure AD à l’aide de l’authentification par certificat SSH pour vos machines virtuelles Linux dans Azure, vous devez vous assurer que la configuration requise pour le réseau, la machine virtuelle et le client (client SSH) ci-dessous est respectée.

### <a name="network"></a>Réseau

La configuration du réseau machines virtuelles doit autoriser l’accès sortant aux points de terminaison suivants sur le port TCP 443 :

Pour Azure Global

- `https://packages.microsoft.com` : Pour l’installation et les mises à niveau des packages.
- `http://169.254.169.254` : Point de terminaison Azure Instance Metadata Service.
- `https://login.microsoftonline.com` : Pour les flux d’authentification basés sur PAM (Pluggable Authentication Modules).
- `https://pas.windows.net` : Pour les flux Azure RBAC.

Pour Azure Government

- `https://packages.microsoft.com` : Pour l’installation et les mises à niveau des packages.
- `http://169.254.169.254` : Point de terminaison Azure Instance Metadata Service.
- `https://login.microsoftonline.us` : Pour les flux d’authentification basés sur PAM (Pluggable Authentication Modules).
- `https://pasff.usgovcloudapi.net` : Pour les flux Azure RBAC.

Pour Azure Chine

- `https://packages.microsoft.com` : Pour l’installation et les mises à niveau des packages.
- `http://169.254.169.254` : Point de terminaison Azure Instance Metadata Service.
- `https://login.chinacloudapi.cn` : Pour les flux d’authentification basés sur PAM (Pluggable Authentication Modules).
- `https://pas.chinacloudapi.cn` : Pour les flux Azure RBAC.

### <a name="virtual-machine"></a>Machine virtuelle

Vérifiez que votre machine virtuelle est configurée avec les fonctionnalités suivantes :

- Identité managée affectée par le système. Cette option est sélectionnée automatiquement lorsque vous utilisez Portail Azure pour créer une machine virtuelle et sélectionnez l’option de connexion Azure AD. Vous pouvez également activer l’identité managée affectée par le système sur une machine virtuelle nouvelle ou existante à l’aide d’Azure CLI.
- `aadsshlogin` et `aadsshlogin-selinux` (selon ce qui est approprié). Ces packages sont installés avec l’extension de machine virtuelle AADSSHLoginForLinux. L’extension est installée lorsque vous utilisez Portail Azure pour créer une machine virtuelle et activez la connexion Azure AD (onglet Gestion) ou via l’interface de ligne de commande Azure.

### <a name="client"></a>Client

Assurez-vous que votre client présente la configuration requise suivante :

- Le client SSH doit prendre en charge les certificats basés sur OpenSSH pour l’authentification. Vous pouvez utiliser Az CLI (2.21.1 ou version ultérieure) avec OpenSSH (inclus dans Windows 10 version 1803 ou ultérieure) ou Azure Cloud Shell pour répondre à cette exigence. 
- Extension SSH pour Az CLI. Vous pouvez l’installer en utilisant `az extension add --name ssh`. Vous n’avez pas besoin d’installer cette extension lors de l’utilisation d’Azure Cloud Shell, car elle est préinstallée dans ce cas.
- Si vous utilisez un client SSH autre qu’Az CLI ou Azure Cloud Shell qui prend en charge les certificats OpenSSH, vous devrez toujours utiliser Az CLI avec l’extension SSH pour récupérer le certificat SSH éphémère et, si vous le souhaitez, un fichier config, puis utiliser ce fichier config avec votre client SSH.
- La connectivité TCP du client à l’adresse IP publique ou privée de la machine virtuelle (il est également possible d’utiliser ProxyCommand ou un transfert SSH vers une machine).

## <a name="enabling-azure-ad-login-in-for-linux-vm-in-azure"></a>Activation de la connexion Azure AD pour les machines virtuelles Linux dans Azure

Pour utiliser la connexion Azure AD pour une machine virtuelle Linux dans Azure, vous devez d’abord activer l’option de connexion Azure AD pour votre machine virtuelle Linux, configurer les attributions de rôles Azure pour les utilisateurs autorisés à se connecter à la machine virtuelle, puis utiliser un client SSH qui prend en charge OpensSSH, par exemple Az CLI ou Az Cloud Shell, pour vous connecter par protocole SSH à votre machine virtuelle Linux. Vous pouvez activer la connexion Azure AD pour votre machine virtuelle Linux de plusieurs façon :

- Expérience Portail Azure lors de la création d’une machine virtuelle Linux
- Expérience Azure Cloud Shell lors de la création d’une machine virtuelle Linux ou pour une machine virtuelle Linux existante

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>À l’aide du Portail Azure, créer une expérience de machine virtuelle pour activer la connexion Azure AD

Vous pouvez activer la connexion Azure AD pour toutes les distributions Linux prises en charge mentionnées ci-dessus en utilisant le portail Azure.

Par exemple, pour créer une machine virtuelle Ubuntu Server 18.04 LTS dans Azure avec ouverture de session Azure AD :

1. Connectez-vous au portail Azure à l’aide d’un compte disposant d’un accès pour créer des machines virtuelles, puis sélectionnez **+ Créer une ressource**.
1. Cliquez sur **Créer** sous **Ubuntu Server 18.04 LTS** dans la vue **Populaire**.
1. Dans l’onglet **Gestion** : 
   1. Cochez la case pour activer **Connexion avec Azure Active Directory (préversion)** .
   1. Vérifiez que l’option **Identité managée affectée par le système** est cochée.
1. Parcourez le reste de l’expérience de création d’une machine virtuelle. Dans cette préversion, vous devrez créer un compte administrateur avec un nom d’utilisateur et un mot de passe ou une clé publique SSH.
 
### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>Utilisation de l’expérience Azure Cloud Shell pour activer la connexion Azure AD

Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte. Cliquez simplement sur le bouton Copier pour copier le code, collez-le dans Cloud Shell, puis appuyez sur Entrée pour l’exécuter. Cloud Shell peut être ouvert de plusieurs façons :

- Sélectionnez Essayer dans le coin supérieur droit d’un bloc de code.
- Ouvrez Cloud Shell dans votre navigateur.
- Sélectionnez le bouton Cloud Shell dans le menu en haut à droite du Portail Azure.

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, cet article vous demande d’exécuter Azure CLI version 2.22.1 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez l’installer ou la mettre à niveau, consultez l’article Installer Azure CLI.

1. Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create).
1. Créez une machine virtuelle avec [az vm create](/cli/azure/vm#az_vm_create&preserve-view=true) à l’aide d’une distribution prise en charge dans une région prise en charge.
1. Installez l’extension de machine virtuelle de connexion Azure AD en utilisant la commande [az vm extension set](/cli/azure/vm/extension?view=azure-cli-latest#az_vm_extension_set&preserve-view=true).

L’exemple suivant illustre le déploiement d’une machine virtuelle nommée *myVM*, utilisant *Ubuntu 18.04 LTS*, dans un groupe de ressources nommé *AzureADLinuxVMPreview* dans la région *southcentralus*. Il installe ensuite l’*extension de machine virtuelle de connexion Azure AD* pour activer la connexion Azure AD pour la machine virtuelle Linux. Les extensions de machine virtuelle sont de petites applications permettant d’exécuter des tâches de configuration et d’automatisation post-déploiement sur des machines virtuelles Azure.

L’exemple peut être personnalisé pour répondre à vos besoins de test, le cas échéant.

```azurecli-interactive
az group create --name AzureADLinuxVMPreview --location southcentralus

az vm create \
    --resource-group AzureADLinuxVMPreview \
    --name myVM \   
    --image UbuntuLTS \
    --assign-identity \
    --admin-username azureuser \
    --generate-ssh-keys

az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADSSHLoginForLinux \
    --resource-group AzureADLinuxVMPreview \
    --vm-name myVM
```

La création de la machine virtuelle et des ressources de support ne nécessite que quelques minutes.

L’extension AADSSHLoginForLinux peut être installée sur une machine virtuelle Linux existante (distribution prise en charge) avec un agent de machine virtuelle en cours d’exécution pour activer l’authentification Azure AD. Si vous déployez cette extension sur une machine virtuelle précédemment créée, assurez-vous que cette dernière dispose d’au moins 1 Go de mémoire allouée, faute de quoi l’installation de l’extension échouera.

Le paramètre provisioningState défini sur Succeeded s’affiche une fois que l’extension est installée sur la machine virtuelle. La machine virtuelle doit avoir un [agent de machine virtuelle](../../virtual-machines/extensions/agent-linux.md) en cours d’exécution pour installer l’extension.

## <a name="configure-role-assignments-for-the-vm"></a>Configurer des attributions de rôle pour la machine virtuelle

Maintenant que vous avez créé la machine virtuelle, vous devez configurer la stratégie RBAC Azure pour déterminer qui peut se connecter à la machine virtuelle. Deux rôles Azure sont utilisés pour autoriser la connexion aux machines virtuelles :

- **Connexion de l’administrateur aux machines virtuelles** : les utilisateurs auxquels ce rôle est attribué peuvent se connecter à une machine virtuelle Azure avec des privilèges Administrateur.
- **Connexion de l’utilisateur aux machines virtuelles** : les utilisateurs auxquels ce rôle est attribué peuvent se connecter à une machine virtuelle Azure avec des privilèges d’utilisateur standard.
 
Pour autoriser un utilisateur à se connecter à la machine virtuelle via le protocole SSH, vous devez lui attribuer le rôle Connexion de l’administrateur aux machines virtuelles ou Connexion de l’utilisateur aux machines virtuelles. Un utilisateur Azure auquel le rôle Propriétaire ou Contributeur est attribué pour une machine virtuelle ne possède pas automatiquement des privilèges de connexion Azure AD à la machine virtuelle via le protocole SSH. Cette séparation vise à assurer une séparation auditée entre l’ensemble des personnes qui contrôlent les machines virtuelles et l’ensemble des personnes qui peuvent accéder aux machines virtuelles. 

Il existe plusieurs façons de configurer des attributions de rôles pour une machine virtuelle. Par exemple, vous pouvez utiliser :

- Expérience Portail Azure AD
- Expérience Azure Cloud Shell

> [!Note]
> Les rôles Connexion de l’administrateur aux machines virtuelles et Connexion de l’utilisateur aux machines virtuelles utilisant dataActions, ils ne peuvent pas être attribués à l’étendue du groupe d’administration. Actuellement, ces rôles peuvent être attribués uniquement à l’étendue de l’abonnement, du groupe de ressources ou de la ressource. Il est recommandé d’attribuer les rôles au niveau de l’abonnement ou de la ressource, et non au niveau de la machine virtuelle individuelle, afin d’éviter le risque de dépasser la [limite d’attributions de rôles Azure](../../role-based-access-control/troubleshooting.md#azure-role-assignments-limit) par abonnement.

### <a name="using-azure-ad-portal-experience"></a>À l’aide de l’expérience du portail Azure AD

Pour configurer les attributions de rôles pour vos machines virtuelles Linux avec Azure AD :

1. Sélectionnez **Contrôle d’accès (IAM)** .

1. Sélectionnez **Ajouter** > **Ajouter une attribution de rôle** pour ouvrir la page Ajouter une attribution de rôle.

1. Attribuez le rôle suivant. Pour connaître les étapes détaillées, consultez [Attribuer des rôles Azure à l’aide du portail Azure](../../role-based-access-control/role-assignments-portal.md).
    
    | Paramètre | Valeur |
    | --- | --- |
    | Role | **Connexion de l’administrateur aux machines virtuelles** ou **Connexion de l’utilisateur aux machines virtuelles** |
    | Attribuer l’accès à | Utilisateur, groupe, principal de service ou identité managée |

    ![Page Ajouter une attribution de rôle dans le portail Azure.](../../../includes/role-based-access-control/media/add-role-assignment-page.png)

Après quelques instants, le principal de sécurité est attribué au rôle dans l’étendue sélectionnée.
 
### <a name="using-the-azure-cloud-shell-experience"></a>À l’aide de l’expérience Azure Cloud Shell

L’exemple suivant illustre l’utilisation de la commande [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) pour attribuer le rôle Connexion de l’administrateur aux machines virtuelles à la machine virtuelle de votre utilisateur Azure actuel. Le nom d’utilisateur de votre compte Azure actuel est obtenu à l’aide de la commande [az account show](/cli/azure/account#az_account_show), et l’étendue est définie sur la machine virtuelle créée lors d’une étape précédente avec la commande [az vm show](/cli/azure/vm#az_vm_show). L’étendue peut également être attribuée au niveau d’un groupe de ressources ou d’un abonnement ; les autorisations d’héritage Azure RBAC normales s’appliquent.

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group AzureADLinuxVMPreview --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Si votre domaine Azure AD et le domaine du nom d’utilisateur d’ouverture de session ne correspondent pas, vous devez spécifier l’ID d’objet de votre compte d’utilisateur avec `--assignee-object-id`, et pas seulement le nom d’utilisateur pour `--assignee`. Vous pouvez obtenir l’ID d’objet de votre compte d’utilisateur avec [az ad user list](/cli/azure/ad/user#az_ad_user_list).

Pour plus d’informations sur l’utilisation d’Azure RBAC pour gérer l’accès à vos ressources d’abonnement Azure, consultez l’article [Procédure d’attribution d’un rôle Azure](../../role-based-access-control/role-assignments-steps.md).

## <a name="install-ssh-extension-for-az-cli"></a>Installer l’extension SSH pour Az CLI

Si vous utilisez Azure Cloud Shell, aucune autre configuration n’est nécessaire, car la version minimale requise d’Az CLI et de l’extension SSH pour Az CLI sont déjà incluses dans l’environnement Cloud Shell.

Exécutez la commande suivante pour ajouter l’extension SSH pour Az CLI :

```azurecli
az extension add --name ssh
```

La version minimale requise pour l’extension est la version 0.1.4. Vérifiez la version de l’extension SSH installée à l’aide de la commande suivante.

```azurecli
az extension show --name ssh
```

## <a name="using-conditional-access"></a>Utilisation d’un accès conditionnel

Vous pouvez appliquer des stratégies d’accès conditionnel telles que l’exigence d’une authentification multifacteur pour l’utilisateur, l’exigence d’un appareil conforme/de jointure hybride Azure AD pour l’appareil exécutant le client SSH ou la vérification du risque faible pour l’utilisateur et la connexion avant d’autoriser l’accès aux machines virtuelles Linux dans Azure sur lesquelles la connexion Azure AD est activée. 

Pour appliquer la stratégie d’accès conditionnel, vous devez sélectionner l’application « Connexion à une machine virtuelle Linux Azure » dans l’option d’attribution d’applications cloud ou d’actions, puis utiliser le risque lié à l’utilisateur et/ou à la connexion comme condition et les contrôles d’accès comme Accorder l’accès après avoir satisfait à l’exigence d’une authentification multifacteur et/ou à l’exigence d’un appareil conforme/de jointure hybride Azure AD.

> [!NOTE]
> L’application de la stratégie d’accès conditionnel nécessitant la conformité de l’appareil ou la jointure hybride Azure AD sur l’appareil client exécutant le client SSH fonctionne uniquement avec Az CLI s’exécutant sur Windows et macOS. Elle n’est pas prise en charge lors de l’utilisation d’Az CLI sur Linux ou Azure Cloud Shell.

## <a name="login-using-azure-ad-user-account-to-ssh-into-the-linux-vm"></a>Connexion avec le compte d’utilisateur Azure AD pour se connecter par protocole SSH à la machine virtuelle Linux

### <a name="using-az-cli"></a>Utilisation d’Az CLI

Tout d’abord, entres les commandes « az login » puis « az ssh vm ».

```azurecli
az login 
```

Cette commande lance une fenêtre de navigateur, et un utilisateur peut se connecter en utilisant son compte Azure AD. 

L’exemple suivant résout automatiquement l’adresse IP appropriée pour la machine virtuelle.

```azurecli
az ssh vm -n myVM -g AzureADLinuxVMPreview
```

Si vous y êtes invité, entrez vos informations d’identification de connexion Azure AD sur la page de connexion et effectuez une authentification multifacteur et/ou les vérifications de l’appareil. Vous y serez invité uniquement si votre session Az CLI n’est pas déjà conforme aux critères d’accès conditionnel requis. Fermez la fenêtre du navigateur, revenez à l’invite SSH et vous serez automatiquement connecté à la machine virtuelle.

Vous êtes à présent connecté à la machine virtuelle Azure Linux avec les autorisations de rôle attribuées, par exemple Utilisateur de machine virtuelle ou Administrateur de machine virtuelle. Si le rôle Connexion de l’administrateur aux machines virtuelles est attribué à votre compte d’utilisateur, vous pouvez utiliser sudo pour exécuter les commandes qui nécessitent des privilèges racine.

### <a name="using-az-cloud-shell"></a>Utilisation d’Az Cloud Shell

Vous pouvez utiliser Az Cloud Shell pour vous connecter aux machines virtuelles sans avoir à installer quoi que ce soit localement sur votre ordinateur client. Démarrez Cloud Shell en cliquant sur l’icône de l’interpréteur de commandes dans le coin supérieur droit du portail Azure.
 
Az Cloud Shell se connecte automatiquement à une session dans le contexte de l’utilisateur connecté. Lors de la préversion de Connexion Azure AD pour Linux, **vous devez à nouveau exécuter la commande « az login » et passer par un flux de connexion interactif**.

```azurecli
az login
```

Vous pouvez ensuite utiliser les commandes « az ssh vm » normales pour vous connecter en utilisant le nom et le groupe de ressources ou l’adresse IP de la machine virtuelle.

```azurecli
az ssh vm -n myVM -g AzureADLinuxVMPreview
```

> [!NOTE]
> L’application de la stratégie d’accès conditionnel nécessitant la conformité ou la jointure hybride Azure AD de l’appareil n’est pas prise en charge lors de l’utilisation d’Az Cloud Shell.

### <a name="login-using-azure-ad-service-principal-to-ssh-into-the-linux-vm"></a>Connexion avec le principal de service Azure AD pour se connecter par protocole SSH à la machine virtuelle Linux

Azure CLI prend en charge l’authentification avec un principal de service au lieu d’un compte d’utilisateur. Étant donné que les principaux de service ne sont pas liés à un utilisateur particulier, les clients peuvent les utiliser pour se connecter par protocole SSH à une machine virtuelle afin de prendre en charge tous les scénarios d’automatisation qu’ils peuvent avoir. Des droits Administrateur de la machine virtuelle ou Utilisateur de la machine virtuelle doivent être attribués au principal de service. Attribuez des autorisations au niveau de l’abonnement ou du groupe de ressources. 

L’exemple suivant attribue des droits Administrateur de la machine virtuelle au principal de service au niveau du groupe de ressources. Remplacez les champs ID d’objet, ID d’abonnement et Nom du groupe de ressources du principal de service.

```azurecli
az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee-object-id <service-principal-objectid> \
    --assignee-principal-type ServicePrincipal \
    --scope “/subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>"
```

Utilisez l’exemple suivant pour vous authentifier auprès d’Azure CLI à l’aide du principal de service. Pour en savoir plus sur la connexion à l’aide d’un principal de service, consultez l’article [Se connecter à Azure CLI avec un principal de service](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal).

```azurecli
az login --service-principal -u <sp-app-id> -p <password-or-cert> --tenant <tenant-id>
```

Une fois l’authentification avec un principal de service terminée, utilisez les commandes SSH Az CLI normales pour vous connecter à la machine virtuelle.

```azurecli
az ssh vm -n myVM -g AzureADLinuxVMPreview
```

### <a name="exporting-ssh-configuration-for-use-with-ssh-clients-that-support-openssh"></a>Exportation de la configuration SSH pour une utilisation avec des clients SSH prenant en charge OpenSSH

La connexion aux machines virtuelles Linux Azure avec Azure AD prend en charge l’exportation du certificat et de la configuration OpenSSH, ce qui vous permet d’utiliser n’importe quel client SSH prenant en charge les certificats basés sur OpenSSH pour vous connecter à Azure AD. L’exemple suivant exporte la configuration de toutes les adresses IP attribuées à la machine virtuelle.

```azurecli
az ssh config --file ~/.ssh/config -n myVM -g AzureADLinuxVMPreview
```

Vous pouvez également exporter la configuration en spécifiant uniquement l’adresse IP. Remplacez l’adresse IP de l’exemple par l’adresse IP publique ou privée (vous devez apporter votre propre connectivité pour les adresses IP privées) de votre machine virtuelle. Saisissez `az ssh config -h` pour obtenir de l’aide sur cette commande.

```azurecli
az ssh config --file ~/.ssh/config --ip 10.11.123.456
```

Vous pouvez ensuite vous connecter à la machine virtuelle en utilisant normalement OpenSSH. La connexion peut être établie par le biais de n’importe quel client SSH qui utilise OpenSSH.

## <a name="sudo-and-azure-ad-login"></a>Sudo et connexion Azure AD

Une fois que les utilisateurs auxquels est attribué le rôle Administrateur de la machine virtuelle ont réussi à se connecter par protocole SSH à une machine virtuelle Linux, ils peuvent exécuter sudo sans autre interaction ou authentification. Les utilisateurs auxquels est attribué le rôle Utilisateur de la machine virtuelle ne pourront pas exécuter sudo.

## <a name="virtual-machine-scale-set-support"></a>Prise en charge de groupes de machines virtuelles identiques

Les groupes de machines virtuelles identiques sont pris en charge, mais les étapes sont légèrement différentes pour l’activation et la connexion aux machines virtuelles du groupe de machines virtuelles identiques.

1. Créez un groupe de machines virtuelles identiques ou choisissez-en un qui existe déjà. Activez une identité managée affectée par le système pour votre groupe de machines virtuelles identiques.

```azurecli
az vmss identity assign --vmss-name myVMSS --resource-group AzureADLinuxVMPreview
```

2. Installez l’extension Azure AD sur votre groupe de machines virtuelles identiques.

```azurecli
az vmss extension set --publisher Microsoft.Azure.ActiveDirectory --name Azure ADSSHLoginForLinux --resource-group AzureADLinuxVMPreview --vmss-name myVMSS
```

Le groupe de machines virtuelles identiques n’a généralement pas d’adresses IP publiques : vous devez donc vous y connecter à partir d’une autre machine qui peut atteindre son réseau virtuel Azure. Cet exemple montre comment utiliser l’adresse IP privée d’une machine virtuelle du groupe de machines virtuelles identiques pour se connecter à partir d’une machine du même réseau virtuel. 

```azurecli
az ssh vm --ip 10.11.123.456
```

> [!NOTE]
> Vous ne pouvez pas déterminer automatiquement les adresses IP de la machine virtuelle du groupe de machines virtuelles identiques à l’aide des commutateurs `--resource-group` et `--name`.

## <a name="migration-from-previous-preview"></a>Migration à partir de la préversion précédente

Pour les clients qui utilisent une version précédente de la connexion Azure AD pour Linux basée sur le flux de code d’appareil, effectuez les étapes suivantes en utilisant Azure CLI.

1. Désinstallez l’extension AADLoginForLinux sur la machine virtuelle.
   
   ```azurecli
   az vm extension delete -g MyResourceGroup --vm-name MyVm -n AADLoginForLinux
   ```

1. Activez l’identité managée affectée par le système sur votre machine virtuelle.

   ```azurecli
   az vm identity assign -g myResourceGroup -n myVm
   ```

1. Installez l’extension AADSSHLoginForLinux sur la machine virtuelle.

    ```azurecli
    az vm extension set \
        --publisher Microsoft.Azure.ActiveDirectory \
        --name AADSSHLoginForLinux \
        --resource-group myResourceGroup \
        --vm-name myVM
    ```

## <a name="using-azure-policy-to-ensure-standards-and-assess-compliance"></a>Utilisation d’Azure Policy pour garantir les normes et évaluer la conformité

Utilisez Azure Policy pour vous assurer que la connexion Azure AD est activée pour vos machines virtuelles Linux nouvelles et existantes, et évaluer la conformité de votre environnement à grande échelle sur votre tableau de bord de conformité Azure Policy. Grâce à cette capacité, vous pouvez utiliser plusieurs niveaux d’application : vous pouvez signaler les machines virtuelles Linux nouvelles et existantes de votre environnement pour lesquelles la connexion Azure AD n’est pas activée. Vous pouvez également utiliser Azure Policy pour déployer l’extension Azure AD sur de nouvelles machines virtuelles Linux pour lesquelles la connexion Azure AD n’est pas activée, ainsi que pour corriger les machines virtuelles Linux existantes afin qu’elles respectent la même norme. Outre ces capacités, vous pouvez également utiliser Azure Policy pour détecter et signaler les machines virtuelles Linux sur lesquelles des comptes locaux non approuvés sont créés. Pour en savoir plus, consultez [Azure Policy](../../governance/policy/overview.md).

## <a name="troubleshoot-sign-in-issues"></a>Résoudre les problèmes de connexion

Certaines erreurs courantes se produisent lorsque vous essayez de vous connecter via le protocole SSH avec des informations d’identification Azure AD, notamment l’absence de l’attribution de rôles Azure et des invites répétées à se connecter. Consultez les sections suivantes pour corriger ces problèmes.

### <a name="could-not-retrieve-token-from-local-cache"></a>Impossible de récupérer le jeton dans le cache local

Vous devez réexécuter la commande « az login » et passer par un flux de connexion interactif. Voir la section [Utilisation d’Az Cloud Shell](#using-az-cloud-shell).

### <a name="access-denied-azure-role-not-assigned"></a>Accès refusé : rôle Azure non attribué

Si vous voyez l’erreur suivante sur l’invite SSH, vérifiez que vous avez configuré des stratégies Azure RBAC pour la machine virtuelle qui accordent à l’utilisateur le rôle Connexion de l’administrateur aux machines virtuelles ou Connexion de l’utilisateur aux machines virtuelles. Si vous rencontrez des problèmes avec des attributions de rôles Azure, consultez l’article [Résoudre les problèmes liés à Azure RBAC](../../role-based-access-control/troubleshooting.md#azure-role-assignments-limit).

### <a name="extension-install-errors"></a>Erreurs d’installation de l’extension

L’installation de l’extension de machine virtuelle AADSSHLoginForLinux sur les ordinateurs existants échoue et génère l’un des codes d’erreur connus suivants :

#### <a name="non-zero-exit-code-22"></a>Code de sortie non nul : 22

L’état de l’extension de machine virtuelle AADSSHLoginForLinux apparaît comme « En cours de transition » dans le portail.

Cause 1 : Cet échec est dû au fait qu’une identité managée affectée par le système est requise.

Solution 1 : Effectuez les actions suivantes :

1. Désinstallez l’extension qui a échoué.
1. Activez une identité managée affectée par le système sur la machine virtuelle Azure.
1. Réexécutez la commande d’installation de l’extension.

#### <a name="non-zero-exit-code-23"></a>Code de sortie non nul : 23

L’état de l’extension de machine virtuelle AADSSHLoginForLinux apparaît comme « En cours de transition » dans le portail.

Cause 1 : Cet échec est dû au fait que l’ancienne extension de machine virtuelle AADLoginForLinux est toujours installée.

Solution 1 : Effectuez les actions suivantes :

1. Désinstallez l’ancienne extension de machine virtuelle AADLoginForLinux de la machine virtuelle. L’état de la nouvelle extension de machine virtuelle AADSSHLoginForLinux passera à « Approvisionnement réussi » dans le portail.

#### <a name="az-ssh-vm-fails-with-keyerror-access_token"></a>La commande « az ssh vm » échoue et génère KeyError: ’access_token’

Cause 1 : Une version obsolète du client Azure CLI est utilisée.

Solution 1 : Mettez à niveau le client Azure CLI vers la version 2.21.0 ou ultérieure.

#### <a name="ssh-connection-closed"></a>Connexion SSH fermée

Une fois que l’utilisateur s’est correctement connecté en utilisant « az login », la connexion à la machine virtuelle en utilisant `az ssh vm -ip <addres>` ou `az ssh vm --name <vm_name> -g <resource_group>` échoue et génère le message *Connexion fermée par <ip_address> port 22*.

Cause 1 : Aucun des rôles Azure RBAC Connexion de l’administrateur/l’utilisateur aux machines virtuelles n’est attribué à l’utilisateur dans l’étendue de cette machine virtuelle.

Solution 1 : Ajoutez l’utilisateur à l’un ou l’autre des rôles Azure RBAC Connexion de l’administrateur/l’utilisateur aux machines virtuelles dans l’étendue de cette machine virtuelle.

Cause 2 : L’utilisateur est affecté à un rôle Azure RBAC obligatoire, mais l’identité managée affectée par le système a été désactivée sur la machine virtuelle.

Solution 2 : Effectuez les actions suivantes :

1. Activez l’identité managée affectée par le système sur la machine virtuelle.
1. Laissez passer plusieurs minutes avant d’essayer de vous connecter en utilisant `az ssh vm --ip <ip_address>`.

### <a name="virtual-machine-scale-set-connection-issues"></a>Problèmes de connexion au groupe de machines virtuelles identiques

Les connexions aux machines virtuelles du groupe de machines virtuelles identiques peuvent échouer si les instances de groupe de machines virtuelles identiques exécutent un ancien modèle. La mise à niveau des instances de groupe de machines virtuelles identiques vers le modèle le plus récent peut résoudre les problèmes, en particulier si aucune mise à niveau n’a été effectuée depuis l’installation de l’extension Connexion Azure AD. La mise à niveau d’une instance applique une configuration standard de groupe de machines virtuelles identiques à l’instance individuelle.

## <a name="preview-feedback"></a>Commentaires de la préversion

Partagez vos commentaires sur cette fonctionnalité d’évaluation ou signalez des problèmes lors de son utilisation sur le [forum de commentaires d’Azure AD](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789).

## <a name="next-steps"></a>Étapes suivantes
