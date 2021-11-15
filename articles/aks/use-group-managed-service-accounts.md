---
title: Activer les comptes de service administrés de groupe (gMSA) pour vos nœuds de serveur Windows sur votre cluster Azure Kubernetes Service (AKS) (préversion)
description: Découvrez comment activer les comptes de service administrés de groupe (gMSA) pour vos nœuds de serveur Windows sur votre cluster Azure Kubernetes Service (AKS) pour sécuriser vos pod.
services: container-service
ms.topic: article
ms.date: 11/01/2021
ms.openlocfilehash: f0acd8cc19ab8a0994b6218a171d8343eef21096
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478981"
---
# <a name="enable-group-managed-service-accounts-gmsa-for-you-windows-server-nodes-on-your-azure-kubernetes-service-aks-cluster-preview"></a>Activer les comptes de service administrés de groupe (gMSA) pour vos nœuds de serveur Windows sur votre cluster Azure Kubernetes Service (AKS) (préversion)

Un [compte de service administré de groupe (gMSA)][gmsa-overview] est un compte de domaine managé pour plusieurs serveurs qui fournit la gestion automatique des mots de passe, la gestion simplifiée du nom de principal du service (SPN) et la possibilité de déléguer la gestion à d’autres administrateurs. AKS permet d’activer gMSA sur vos nœuds de serveur Windows, ce qui permet aux conteneurs s’exécutant sur des nœuds de serveur Windows de s’intégrer à gMSA et d’être gérés par gMSA.

L’activation de gMSA avec des nœuds de serveur Windows sur AKS est en version préliminaire.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="pre-requisites"></a>Conditions préalables

L’activation de gMSA avec des nœuds de serveur Windows sur AKS requiert :

* Kubernetes 1.19 ou version ultérieure.
* L’extension `aks-preview` version 0.5.37 ou ultérieure.
* Le runtime du conteneur Docker, qui est actuellement la valeur par défaut.
* [Identités managées][aks-managed-id] avec votre cluster AKS.
* Autorisations pour créer ou mettre à jour un Azure Key Vault.
* Autorisations pour configurer gMSA sur Active Directory Domain Services ou Active Directory local.
* Le contrôleur de domaine doit avoir Active Directory Services Web activé et doit être accessible sur le port 9389 par le cluster AKS.

### <a name="install-the-aks-preview-azure-cli"></a>Installez l’interface de ligne de commande Azure `aks-preview`

Vous aurez besoin de l’extension Azure CLI *aks-preview*. Installez l’extension d’Azure CLI *aks-preview* à l’aide de la commande [az extension add][az-extension-add]. Ou installez toutes les mises à jour disponibles à l’aide de la commande [az extension update][az-extension-update].

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-the-akswindowsgmsapreview-preview-feature"></a>Inscrire la fonctionnalité d’évaluation `AKSWindowsGmsaPreview`

Pour utiliser la fonctionnalité, vous devez activer l’indicateur de fonctionnalité `AKSWindowsGmsaPreview` sur votre abonnement.

Inscrivez l’indicateur de fonctionnalité `AKSWindowsGmsaPreview` à l’aide de la commande [az feature register][az-feature-register], comme indiqué dans l’exemple suivant :

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKSWindowsGmsaPreview"
```

Quelques minutes sont nécessaires pour que l’état s’affiche *Registered* (Inscrit). Vérifiez l’état de l’inscription à l’aide de la commande [az feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSWindowsGmsaPreview')].{Name:name,State:properties.state}"
```

Lorsque vous êtes prêt, actualisez l’inscription du fournisseur de ressources *Microsoft.ContainerService* à l’aide de la commande [az provider register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="configure-gmsa-on-active-directory-domain-controller"></a>Configurer gMSA sur le contrôleur de domaine Active Directory

Pour utiliser gMSA avec AKS, vous avez besoin de gMSA et des informations d’identification de l’utilisateur de domaine standard pour accéder aux informations d’identification gMSA configurées sur votre contrôleur de domaine. Pour configurer gMSA sur votre contrôleur de domaine, consultez [Prise en main avec les comptes de service administrés de groupe][gmsa-getting-started]. Pour les informations d’identification de l’utilisateur de domaine standard, vous pouvez utiliser un utilisateur existant ou en créer un, à condition qu’il ait accès aux informations d’identification gMSA.

> [!IMPORTANT]
> Vous devez utiliser domaine Active Directory Domain Services ou Active Directory local. Pour le moment, vous ne pouvez pas utiliser Azure Active Directory pour configurer gMSA avec un cluster AKS.

## <a name="store-the-standard-domain-user-credentials-in-azure-key-vault"></a>Stocker les informations d’identification de l’utilisateur de domaine standard dans Azure Key Vault

Votre cluster AKS utilise les informations d’identification de l’utilisateur de domaine standard pour accéder aux informations d’identification gMSA à partir du contrôleur de domaine. Pour fournir un accès sécurisé à ces informations d’identification pour le cluster AKS, ces informations d’identification doivent être stockées dans Azure Key Vault. Vous pouvez créer un coffre de clés ou utiliser un coffre de clés existant.

Utilisez `az keyvault secret set` pour stocker les informations d’identification de l’utilisateur de domaine standard en tant que secret dans votre coffre de clés. L’exemple suivant stocke les informations d’identification de l’utilisateur de domaine avec la clé *GMSADomainUserCred* dans le coffre de clés *MyAKSGMSAVault*. Vous devez remplacer les paramètres par les informations d’identification de votre propre coffre de clés, de votre clé et de votre utilisateur de domaine.

```azurecli
az keyvault secret set --vault-name MyAKSGMSAVault --name "GMSADomainUserCred&quot; --value &quot;$Domain\\$DomainUsername:$DomainUserPassword"
```

## <a name="optional-use-a-custom-vnet-with-custom-dns"></a>Facultatif : Utilisez un réseau virtuel personnalisé avec un DNS personnalisé

Votre contrôleur de domaine doit être configuré par le biais du DNS afin d’être accessible par le cluster AKS. Vous pouvez configurer votre réseau et DNS en dehors de votre cluster AKS pour permettre à votre cluster d’accéder au contrôleur de domaine. Vous pouvez également configurer un réseau virtuel personnalisé avec un DNS personnalisé à l’aide d’Azure CNI avec votre cluster AKS pour fournir l’accès à votre contrôleur de domaine. Pour plus de détails, consultez [Configurer la mise en réseau d’Azure CNI dans Azure Kubernetes Service (AKS)][aks-cni].

## <a name="optional-use-your-own-kubelet-identity-for-your-cluster"></a>Facultatif : Utilisez votre propre identité kubelet pour votre cluster

Pour permettre au cluster AKS d’accéder à votre coffre de clés, l’identité kubelet du cluster doit accéder à votre coffre de clés. Par défaut, lorsque vous créez un cluster avec une identité managée activée, une identité kubelet est créée automatiquement. Vous pouvez accorder l’accès à votre coffre de clés pour cette identité après la création du cluster, ce qui est effectué dans une étape ultérieure.

Vous pouvez également créer votre propre identité et utiliser cette identité lors de la création du cluster dans une étape ultérieure. Pour plus de détails sur les identités fournies managées par AKS, consultez [Résumé des identités managées][aks-managed-id-kubelet].

Pour créer votre propre identité, utilisez `az identity create` pour créer une identité. Dans l’exemple suivant, une identité *myIdentity* est créée dans le groupe de ressources *myResourceGroup*.

```azurecli
az identity create --name myIdentity --resource-group myResourceGroup
```

Vous pouvez accorder l’accès de votre identité kubelet à votre coffre de clés avant ou après avoir créé votre cluster. L’exemple suivant utilise `az identity list` pour récupérer l’ID de l’identité et lui affecter la valeur *MANAGED_ID* puis utilise `az keyvault set-policy` pour accorder l’accès à l’identité au coffre de clés *MyAKSGMSAVault*.

```azurecli
MANAGED_ID=$(az identity list --query "[].id" -o tsv)
az keyvault set-policy --name "MyAKSGMSAVault" --object-id $MANAGED_ID --secret-permissions get
```

## <a name="create-aks-cluster"></a>Créer un cluster AKS

Pour utiliser gMSA avec votre cluster AKS, utilisez les paramètres *enable-windows-gmsa*, *gmsa-dns-server*, *gmsa-root-domain-name*, et *enable-managed-identity*.

> [!NOTE]
> Lorsque vous créez un cluster avec des pools de nœuds de serveur Windows, vous devez spécifier les informations d’identification de l’administrateur lors de la création du cluster. Les commandes suivantes vous invitent à entrer un nom d’utilisateur et à lui affecter la valeur WINDOWS_USERNAME pour une utilisation dans une commande ultérieure (n’oubliez pas que les commandes de cet article sont entrées dans un interpréteur de commandes BASH).
> 
> ```azurecli
> echo "Please enter the username to use as administrator credentials for Windows Server nodes on your cluster: " && read WINDOWS_USERNAME
> ```

Utilisez `az aks create` pour créer un cluster AKS, puis `az aks nodepool add` pour ajouter un pool de nœuds Windows Server. Dans l’exemple suivant, un cluster *MyAKS* est créé dans le groupe de ressources *MyResourceGroup*, gMSA est activé, puis un nouveau pool de nœuds nommé *npwin* est ajouté.

> [!NOTE]
> Si vous utilisez un réseau virtuel personnalisé, vous devez également spécifier l’ID du réseau virtuel à l’aide de *vnet-subnet-ID*. Vous devrez peut-être également ajouter *docker-bridge-address*, *dns-service-ip* et *service-cidr* en fonction de votre configuration.
> 
> Si vous avez créé votre propre identité pour l’identité kubelet, utilisez le paramètre *assign-kubelet-identity* pour spécifier votre identité.

```azurecli
DNS_SERVER=<IP address of DNS server>
ROOT_DOMAIN_NAME="contoso.com"

az aks create \
    --resource-group MyResourceGroup \
    --name MyAKS \
    --vm-set-type VirtualMachineScaleSets \
    --network-plugin azure \
    --load-balancer-sku standard \
    --windows-admin-username $WINDOWS_USERNAME \
    --enable-managed-identity \
    --enable-windows-gmsa \
    --gmsa-dns-server $DNS_SERVER \
    --gmsa-root-domain-name $ROOT_DOMAIN_NAME

az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKS \
    --os-type Windows \
    --name npwin \
    --node-count 1    
```

Vous pouvez également activer gMSA sur les clusters existants qui ont déjà des nœuds Windows Server et des identités managées activées à l’aide de `az aks update`. Par exemple :

```azurecli
az aks update \
    --resource-group MyResourceGroup \
    --name MyAKS \
    --enable-windows-gmsa \
    --gmsa-dns-server $DNS_SERVER \
    --gmsa-root-domain-name $ROOT_DOMAIN_NAME
```

Après avoir créé votre cluster ou mis à jour votre cluster, utilisez `az keyvault set-policy` pour accorder l’accès d’identité à votre coffre de clés. L’exemple suivant accorde l’identité kubelet créée par l’accès du cluster au coffre de clés *MyAKSGMSAVault*.

> [!NOTE]
> Si vous avez fourni votre propre identité pour l’identité kubelet, ignorez cette étape.

```azurecli
MANAGED_ID=$(az aks show -g MyResourceGroup -n MyAKS --query "identityProfile.kubeletidentity.objectId" -o tsv)

az keyvault set-policy --name "MyAKSGMSAVault" --object-id $MANAGED_ID --secret-permissions get
```

## <a name="install-gmsa-cred-spec"></a>Installer la spécification des informations d’identification gMSA

Pour configurer `kubectl` afin de vous connecter à votre cluster Kubernetes, exécutez la commande [az aks get-credentials][]. L’exemple suivant obtient les informations d’identification du cluster AKS nommé *myAKSCluster* dans *MyResourceGroup* :

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

Créez un *gmsa-spec.yaml* avec les éléments suivants, en remplaçant les espaces réservés par vos propres valeurs.

```yml
apiVersion: windows.k8s.io/v1alpha1
kind: GMSACredentialSpec
metadata:
  name: aks-gmsa-spec  # This name can be changed, but it will be used as a reference in the pod spec
credspec:
  ActiveDirectoryConfig:
    GroupManagedServiceAccounts:
    - Name: $GMSA_ACCOUNT_USERNAME
      Scope: $NETBIOS_DOMAIN_NAME
    - Name: $GMSA_ACCOUNT_USERNAME
      Scope: $DNS_DOMAIN_NAME
    HostAccountConfig:
      PluginGUID: '{CCC2A336-D7F3-4818-A213-272B7924213E}'
      PortableCcgVersion: "1"
      PluginInput: ObjectId=$MANAGED_ID;SecretUri=$SECRET_URI  # SECRET_URI takes the form https://$akvName.vault.azure.net/secrets/$akvSecretName
  CmsPlugins:
  - ActiveDirectory
  DomainJoinConfig:
    DnsName: $DNS_DOMAIN_NAME
    DnsTreeName: $DNS_ROOT_DOMAIN_NAME
    Guid:  $AD_DOMAIN_OBJECT_GUID
    MachineAccountName: $GMSA_ACCOUNT_USERNAME
    NetBiosName: $NETBIOS_DOMAIN_NAME
    Sid: $GMSA_SID
```

Créez un *gmsa-role.yaml* avec les éléments suivants.

```yml
#Create the Role to read the credspec
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: aks-gmsa-role
rules:
- apiGroups: ["windows.k8s.io"]
  resources: ["gmsacredentialspecs"]
  verbs: ["use"]
  resourceNames: ["aks-gmsa-spec"]
```

Créez un *gmsa-role-binding.yaml* avec les éléments suivants.

```yml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: allow-default-svc-account-read-on-aks-gmsa-spec
  namespace: default
subjects:
- kind: ServiceAccount
  name: default
  namespace: default
roleRef:
  kind: ClusterRole
  name: aks-gmsa-role
  apiGroup: rbac.authorization.k8s.io
```

Utilisez `kubectl apply` pour appliquer les modifications de *gmsa-spec.yaml*, *gmsa-role.yaml*, et *gmsa-role-binding.yaml*.

```azurecli
kubectl apply -f gmsa-spec.yaml
kubectl apply -f gmsa-role.yaml
kubectl apply -f gmsa-role-binding.yaml
```

## <a name="verify-gmsa-is-installed-and-working"></a>Vérifier que gMSA est installé et opérationnel

Créez un *gmsa-demo.yaml* avec les éléments suivants.

```yml
---
kind: ConfigMap
apiVersion: v1
metadata:
  labels:
   app: gmsa-demo
  name: gmsa-demo
  namespace: default
data:
  run.ps1: |
   $ErrorActionPreference = "Stop"

   Write-Output "Configuring IIS with authentication."

   # Add required Windows features, since they are not installed by default.
   Install-WindowsFeature "Web-Windows-Auth", "Web-Asp-Net45"

   # Create simple ASP.Net page.
   New-Item -Force -ItemType Directory -Path 'C:\inetpub\wwwroot\app'
   Set-Content -Path 'C:\inetpub\wwwroot\app\default.aspx' -Value 'Authenticated as <B><%=User.Identity.Name%></B>, Type of Authentication: <B><%=User.Identity.AuthenticationType%></B>'

   # Configure IIS with authentication.
   Import-Module IISAdministration
   Start-IISCommitDelay
   (Get-IISConfigSection -SectionPath 'system.webServer/security/authentication/windowsAuthentication').Attributes['enabled'].value = $true
   (Get-IISConfigSection -SectionPath 'system.webServer/security/authentication/anonymousAuthentication').Attributes['enabled'].value = $false
   (Get-IISServerManager).Sites[0].Applications[0].VirtualDirectories[0].PhysicalPath = 'C:\inetpub\wwwroot\app'
   Stop-IISCommitDelay

   Write-Output "IIS with authentication is ready."

   C:\ServiceMonitor.exe w3svc
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: gmsa-demo
  name: gmsa-demo
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      app: gmsa-demo
  template:
    metadata:
      labels:
        app: gmsa-demo
    spec:
      securityContext:
        windowsOptions:
          gmsaCredentialSpecName: aks-gmsa-spec
      containers:
      - name: iis
        image: mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2019
        imagePullPolicy: IfNotPresent
        command:
         - powershell
        args:
          - -File
          - /gmsa-demo/run.ps1
        volumeMounts:
          - name: gmsa-demo
            mountPath: /gmsa-demo
      volumes:
      - configMap:
          defaultMode: 420
          name: gmsa-demo
        name: gmsa-demo
      nodeSelector:
        kubernetes.io/os: windows
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: gmsa-demo
  name: gmsa-demo
  namespace: default
spec:
  ports:
  - port: 80
    targetPort: 80
  selector:
    app: gmsa-demo
  type: LoadBalancer
```

Utilisez `kubectl apply` pour appliquer les modifications sur *gmsa-demo.yaml*

```azurecli
kubectl apply -f gmsa-demo.yaml
```

Utilisez `kubectl get service` pour afficher l’adresse IP de l’exemple d’application.

```console
kubectl get service gmsa-demo --watch
```

Dans un premier temps, la valeur *EXTERNAL-IP* pour le service *gmsa-demo* apparaît comme étant *en attente*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
gmsa-demo          LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Quand l’adresse *EXTERNAL-IP* passe de l’état *pending* à une adresse IP publique réelle, utilisez `CTRL-C` pour arrêter le processus de surveillance `kubectl`. L’exemple de sortie suivant montre une adresse IP publique valide affectée au service :

```output
gmsa-demo  LoadBalancer   10.0.37.27   EXTERNAL-IP   80:30572/TCP   2m
```

Pour vérifier que gMSA fonctionne et qu’il est correctement configuré, ouvrez un navigateur web à l’adresse IP externe du service *gmsa-demo*. Authentifiez-vous avec `$NETBIOS_DOMAIN_NAME\$AD_USERNAME` et le mot de passe, et confirmez que `Authenticated as $NETBIOS_DOMAIN_NAME\$AD_USERNAME, Type of Authentication: Negotiate` s’affiche.

## <a name="troubleshooting"></a>Dépannage

### <a name="no-authentication-is-prompted-when-loading-the-page"></a>Aucune authentification n’est demandée lors du chargement de la page

Si la page se charge, mais que vous n’êtes pas invité à vous authentifier, utilisez `kubelet logs POD_NAME` pour afficher les journaux de votre pod et vérifiez que *IIS avec authentification est prêt* s’affiche.

### <a name="connection-timeout-when-trying-to-load-the-page"></a>Délai d’expiration de la connexion lors de la tentative de chargement de la page

Si vous dépassez le délai d’attente de la connexion en tentant de charger la page, vérifiez que l’exemple d’application s’exécute avec `kubectl get pods --watch`. Parfois, l’adresse IP externe de l’exemple de service d’application est disponible avant l’exécution de l’exemple de pod d’application.

### <a name="pod-fails-to-start-and-an-winapi-error-shows-in-the-pod-events"></a>Le pod ne démarre pas et une erreur *winapi-error* s’affiche dans les événements pod

Si votre pod ne démarre pas après avoir exécuté `kubectl get pods --watch` et avoir attendu quelques minutes, exécutez `kubectl describe pod POD_NAME`. Si vous voyez une erreur *winapi-error* dans les événements pod, il s’agit probablement d’une erreur dans la configuration de vos informations d’identification gMSA. Vérifiez que toutes les valeurs de remplacement dans *gmsa-spec.yaml* sont correctes, réexécutez `kubectl apply -f gmsa-spec.yaml` et redéployez l’exemple d’application.


[aks-cni]: configure-azure-cni.md
[aks-managed-id]: use-managed-identity.md
[aks-managed-id-kubelet]: use-managed-identity.md#summary-of-managed-identities
[az aks get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[gmsa-getting-started]: /windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts
[gmsa-overview]: /windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview
[rdp]: rdp.md