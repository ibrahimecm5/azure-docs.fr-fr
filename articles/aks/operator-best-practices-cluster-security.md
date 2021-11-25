---
title: Bonnes pratiques relatives à la sécurité du cluster
titleSuffix: Azure Kubernetes Service
description: Découvrir les meilleures pratiques de l’opérateur relatives à la gestion des mises à jour et de la sécurité du cluster dans Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 73c9ac0780c6a1217e58f40b4e64f290e8904457
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132492433"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Meilleures pratiques relatives aux mises à jour et à la sécurité du cluster dans Azure Kubernetes Service (AKS)

Quand vous gérez des clusters dans AKS (Azure Kubernetes Service), la sécurité des charges de travail et des données est un point important. Lorsque vous exécutez des clusters multilocataires à l’aide de l’isolation logique, vous devez tout particulièrement sécuriser l’accès aux ressources et aux charges de travail. Réduisez le risque d’attaque en appliquant les dernières mises à jour de sécurité du système d’exploitation de nœud et de Kubernetes.

Cet article est dédié à la sécurisation de votre cluster AKS. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Utiliser Azure Active Directory et le contrôle d’accès en fonction du rôle Kubernetes (RBAC Kubernetes) pour sécuriser l’accès au serveur d’API.
> * Sécuriser l’accès du conteneur aux ressources de nœud.
> * Mettre à niveau un cluster AKS avec la dernière version de Kubernetes.
> * Maintenir les nœuds à jour et appliquer automatiquement des correctifs de sécurité.

Vous pouvez également consulter les meilleures pratiques relatives à la [gestion des images conteneur][best-practices-container-image-management] et à la [sécurité du pod][best-practices-pod-security].

Vous pouvez également utiliser l’[intégration des services Azure Kubernetes à Defender pour le cloud][security-center-aks] pour détecter les menaces et afficher des recommandations pour sécuriser vos clusters AKS.

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>Sécuriser l’accès aux nœuds de cluster et au serveur d’API

> **Conseils sur les bonnes pratiques** 
>
> L’une des manières les plus fiables de sécuriser votre cluster consiste à sécuriser l’accès au serveur d’API Kubernetes. Pour contrôler l’accès au serveur d’API, intégrez RBAC Kubernetes à Azure AD (Azure Active Directory). Avec ces contrôles, vous pouvez sécuriser AKS de la même façon que vous sécurisez l’accès à vos abonnements Azure.

Le serveur d’API Kubernetes propose un point de connexion unique pour que les requêtes exécutent des actions dans un cluster. Pour sécuriser et auditer l’accès au serveur d’API, limitez l’accès et accordez les niveaux d’autorisation les plus faibles possibles. Bien que cette approche ne soit pas unique à Kubernetes, elle est particulièrement importante lorsque vous avez isolé votre cluster AKS de façon logique pour une utilisation multilocataire.

Azure AD fournit une solution de gestion des identités d’entreprise qui s’intègre aux clusters AKS. Kubernetes ne procurant pas de solution de gestion des identités, vous serez peut-être contraint de restreindre de façon précise l’accès au serveur d’API. Avec les clusters intégrés Azure AD dans AKS, vous utilisez vos comptes groupe et utilisateur existants pour authentifier les utilisateurs sur le serveur d’API.

![Intégration Azure Active Directory aux clusters AKS](media/operator-best-practices-cluster-security/aad-integration.png)

À l’aide de RBAC Kubernetes et de l’intégration à Azure AD, vous pouvez sécuriser le serveur d’API et fournir les autorisations minimales requises pour un ensemble de ressources délimité, comme un espace de noms unique. Vous pouvez accorder à différents utilisateurs ou groupes Azure AD différents rôles Kubernetes. Avec des autorisations précises, vous pouvez restreindre l’accès au serveur d’API et fournir une piste d’audit claire des actions effectuées.

La bonne pratique recommandée consiste à utiliser des *groupes* pour fournir un accès aux fichiers et dossiers plutôt que des identités individuelles. Par exemple, utilisez une appartenance de *groupe* Azure AD afin de lier des utilisateurs à des rôles Kubernetes, plutôt que des *utilisateurs* individuels. Lorsque l’appartenance au groupe d’un utilisateur change, ses autorisations d’accès sur le cluster AKS changent en conséquence. 

Par ailleurs, supposez que vous liez l’utilisateur individuel directement à un rôle et que ses fonctions de travail changent. Les appartenances au groupe Azure AD seront mises à jour, tandis que ses autorisations sur le cluster AKS resteront inchangées. Dans ce scénario, l’utilisateur se retrouvera avec plus d’autorisations qu’il n’en a besoin.

Pour plus d’informations sur l’intégration Azure AD, RBAC Kubernetes et RBAC Azure, consultez les [Meilleures pratiques relatives à l’authentification et à l’autorisation dans AKS][aks-best-practices-identity].

## <a name="secure-container-access-to-resources"></a>Sécuriser l’accès du conteneur aux ressources

> **Conseils sur les bonnes pratiques** 
> 
> Limitez l’accès aux actions que les conteneurs peuvent effectuer. Fournissez le plus petit nombre d’autorisations, et évitez d’utiliser l’accès racine ou l’escalade de privilèges.

De la même façon que vous devez accorder aux utilisateurs ou aux groupes le minimum de privilèges requis, vous devez limiter les conteneurs uniquement aux actions et processus nécessaires. Pour réduire le risque d’attaque, évitez de configurer les applications et les conteneurs qui nécessitent une escalade des privilèges ou un accès racine. 

Par exemple, définissez `allowPrivilegeEscalation: false` dans le manifeste de pod. Ces *contextes de sécurité de pod* Kubernetes intégrés vous permettent de définir des autorisations supplémentaires, telles que l’identité du groupe ou de l’utilisateur à exécuter, ou les fonctionnalités Linux à exposer. Pour plus de meilleures pratiques, consultez [Sécuriser l’accès du pod aux ressources][pod-security-contexts].

Pour un contrôle encore plus précis des actions de conteneur, vous pouvez également utiliser des fonctionnalités de sécurité Linux intégrées telles que *AppArmor* et *seccomp*. 
1. Définissez les fonctionnalités de sécurité de Linux au niveau du nœud.
1. Implémentez les fonctionnalités par le biais d’un manifeste de pod. 

Les fonctionnalités de sécurité Linux intégrées sont disponibles sur les nœuds et les pods Linux uniquement.

> [!NOTE]
> Actuellement, les environnements Kubernetes ne sont pas totalement sûrs pour une utilisation multi-locataire hostile. Des fonctionnalités de sécurité supplémentaires, telles que *AppArmor*, *seccomp* et les *stratégies de sécurité de pod*, ou RBAC Kubernetes pour les nœuds, bloquent efficacement les attaques. 
>
>Pour une véritable sécurité lors de l’exécution de charges de travail multilocataires hostiles, ne faites confiance qu’à un hyperviseur. Le domaine de sécurité de Kubernetes devient le cluster, et non un nœud individuel. 
>
> Pour ces types de charges de travail multi-locataires hostiles, vous devez utiliser des clusters physiquement isolés.

### <a name="app-armor"></a>AppArmor

Pour limiter les actions de conteneurs, vous pouvez utiliser le module de sécurité du noyau Linux [AppArmor][k8s-apparmor]. AppArmor est disponible dans le cadre du système d’exploitation de nœud AKS sous-jacent et est activé par défaut. Vous créez des profils AppArmor qui limitent les actions de lecture, d’écriture ou d’exécution, ou des fonctions système telles que le montage de systèmes de fichiers. Les profils AppArmor par défaut limitent l’accès à divers emplacements `/proc` et `/sys`, et fournissent un moyen pour isoler de façon logique les conteneurs à partir du nœud sous-jacent. AppArmor fonctionne pour n’importe quelle application qui s’exécute sur Linux, pas seulement les pods Kubernetes.

![Profils AppArmor en cours d’utilisation dans un cluster AKS pour limiter les actions de conteneur](media/operator-best-practices-container-security/apparmor.png)

Pour voir AppArmor en action, l’exemple suivant crée un profil qui empêche l’écriture de fichiers. 
1. [SSH][aks-ssh] vers un nœud AKS.
1. Créez un fichier nommé *deny-write.profile*.
1. Collez le contenu suivant :

    ```
    #include <tunables/global>
    profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
      #include <abstractions/base>
  
      file,
      # Deny all file writes.
      deny /** w,
    }
    ```

Les profils AppArmor sont ajoutés à l’aide de la commande `apparmor_parser`. 
1. Ajoutez le profil à AppArmor.
1. Spécifiez le nom du profil créé à l’étape précédente :

    ```console
    sudo apparmor_parser deny-write.profile
    ```

    Si le profil est analysé correctement et appliqué à AppArmor, vous ne verrez aucune sortie et serez redirigé vers l’invite de commandes.

1. À partir de votre machine locale, créez un manifeste de pod nommé *aks-apparmor.yaml*. Ce manifeste :
    * Définit une annotation pour `container.apparmor.security.beta.kubernetes`.
    * Référence le profil *deny-write* créé aux étapes précédentes.

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: hello-apparmor
      annotations:
        container.apparmor.security.beta.kubernetes.io/hello: localhost/k8s-apparmor-example-deny-write
    spec:
      containers:
      - name: hello
        image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
        command: [ "sh", "-c", "echo 'Hello AppArmor!' && sleep 1h" ]
    ```

1. Une fois le pod déployé, vérifiez que le pod *hello-apparmor* est *bloqué* :

    ```
    $ kubectl get pods

    NAME             READY   STATUS    RESTARTS   AGE
    aks-ssh          1/1     Running   0          4m2s
    hello-apparmor   0/1     Blocked   0          50s
    ```

Pour plus d’informations sur AppArmor, consultez les [Profils AppArmor dans Kubernetes][k8s-apparmor].

### <a name="secure-computing"></a>Sécuriser le calcul

Tandis qu’AppArmor fonctionne pour toutes les applications Linux, [seccomp (*sec* ure *comp* uting)][seccomp] agit au niveau du processus. Seccomp est également un module de sécurité du noyau Linux, pris en charge de façon native par le runtime Docker utilisé par les nœuds AKS. Avec seccomp, vous pouvez limiter les appels au processus de conteneur. Respectez la bonne pratique consistant à accorder l’autorisation minimale au conteneur pour une exécution en :
* Définissant avec des filtres les actions à autoriser ou à refuser.
* Annotant dans un manifeste YAML de pod afin d’associer au filtre seccomp. 

Pour voir seccomp en action, créez un filtre qui empêche la modification des autorisations sur un fichier. 
1. [SSH][aks-ssh] vers un nœud AKS.
1. Créez un filtre seccomp nommé */var/lib/kubelet/seccomp/prevent-chmod*.
1. Collez le contenu suivant :

    ```json
    {
      "defaultAction": "SCMP_ACT_ALLOW",
      "syscalls": [
        {
          "name": "chmod",
          "action": "SCMP_ACT_ERRNO"
        },
        {
          "name": "fchmodat",
          "action": "SCMP_ACT_ERRNO"
        },
        {
          "name": "chmodat",
          "action": "SCMP_ACT_ERRNO"
        }
      ]
    }
    ```

    Dans les versions 1.19 et ultérieures, vous devez configurer les éléments suivants :

    ```json
    {
      "defaultAction": "SCMP_ACT_ALLOW",
      "syscalls": [
        {
          "names": ["chmod","fchmodat","chmodat"],
          "action": "SCMP_ACT_ERRNO"
        }
      ]
    }
    ```

1. À partir de votre machine locale, créez un manifeste de pod nommé *aks-seccomp.yaml* et collez le contenu suivant. Ce manifeste :
    * Définit une annotation pour `seccomp.security.alpha.kubernetes.io`.
    * Référence le filtre *prevent-chmod* créé à l’étape précédente.

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: chmod-prevented
      annotations:
        seccomp.security.alpha.kubernetes.io/pod: localhost/prevent-chmod
    spec:
      containers:
      - name: chmod
        image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
        command:
          - "chmod"
        args:
         - "777"
         - /etc/hostname
      restartPolicy: Never
    ```

    Dans les versions 1.19 et ultérieures, vous devez configurer les éléments suivants :

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: chmod-prevented
    spec:
      securityContext:
        seccompProfile:
          type: Localhost
          localhostProfile: prevent-chmod
      containers:
      - name: chmod
        image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
        command:
          - "chmod"
        args:
         - "777"
         - /etc/hostname
      restartPolicy: Never
    ```

1. Déployez l’exemple de pod à l’aide de la commande [kubectl apply][kubectl-apply] :

    ```console
    kubectl apply -f ./aks-seccomp.yaml
    ```

1. Affichez l’état du pod à l’aide de la commande [kubectl get pods][kubectl-get]. 
    * Le pod signale une erreur. 
    * La commande `chmod` ne peut pas être exécutée en raison du filtre seccomp, comme affiché dans l’exemple de sortie suivant :    

    ```
    $ kubectl get pods

    NAME                      READY     STATUS    RESTARTS   AGE
    chmod-prevented           0/1       Error     0          7s
    ```

Pour plus d’informations sur les filtres disponibles, consultez les [Profils de sécurité seccomp pour Docker][seccomp].

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>Effectuer des mises à jour régulières vers la dernière version de Kubernetes

> **Conseils sur les bonnes pratiques** 
> 
> Pour rester informé des nouvelles fonctionnalités et correctifs de bogues, effectuez des mises à niveau régulières de la version Kubernetes dans votre cluster AKS.

Kubernetes sort de nouvelles fonctionnalités à un rythme plus effréné que les plateformes d’infrastructure plus traditionnelles. Les mises à jour Kubernetes comprennent ce qui suit :
* Nouvelles fonctionnalités
* Correctifs de bogues ou de sécurité 

En général, les nouvelles fonctionnalités passent par un état *alpha*, puis *bêta* ,avant d’atteindre un état *stable*. Une fois stables, elles sont en disponibilité générale et recommandées pour une utilisation en production. Le cycle de publication des nouvelles fonctionnalités Kubernetes vous permet de mettre à jour Kubernetes sans rencontrer régulièrement de changements cassants ou ajuster vos déploiements et modèles.

AKS prend en charge trois versions mineures de Kubernetes. Lorsqu’une nouvelle version de correctif mineure est introduite, la version mineure et les publications des correctifs les plus anciennes prises en charge sont mises hors service. Les mises à jour mineures de Kubernetes se produisent sur une base périodique. Pour rester dans les limites de support, vérifiez que vous disposez d’un processus de gouvernance pour vérifier les mises à niveau nécessaires. Pour plus d’informations, consultez la section [Versions de Kubernetes prises en charge dans AKS][aks-supported-versions].

Pour consulter les versions disponibles pour votre cluster, utilisez la commande [az aks get-upgrades][az-aks-get-upgrades] comme indiqué dans l’exemple suivant :

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Vous pouvez ensuite mettre à niveau votre cluster AKS à l’aide de la commande [az aks upgrade][az-aks-upgrade]. Le processus de mise à niveau effectue les opérations suivantes de manière sécurisée :
* Bouclage et drainage d’un nœud à la fois
* Planification des pods sur les nœuds restants
* Déploiement d’un nouveau nœud exécutant les versions les plus récentes du système d’exploitation et de Kubernetes

>[!IMPORTANT]
> Testez les nouvelles versions mineures dans un environnement de développement de test, et vérifiez que votre charge de travail demeure intègre avec la nouvelle version de Kubernetes. 
>
> Kubernetes est susceptible de déprécier les API (comme dans la version 1.16) sur lesquelles reposent vos charges de travail. Lors de la mise en production de nouvelles versions, envisagez d’utiliser [plusieurs pools de nœuds sur des versions distinctes](use-multiple-node-pools.md) et de mettre à niveau les pools individuels un par un pour déployer progressivement la mise à jour sur l’ensemble du cluster. Si vous exécutez plusieurs clusters, mettez-les à niveau l’un après l’autre de manière à surveiller progressivement l’impact ou les modifications.
>
>```azurecli-interactive
>az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version KUBERNETES_VERSION
>```

Pour plus d’informations sur les mises à niveau dans AKS, consultez [Versions de Kubernetes prises en charge dans Azure Kubernetes Service (AKS)][aks-supported-versions] et [Mise à jour d’un cluster Azure Kubernetes Service (AKS)][aks-upgrade].

## <a name="process-linux-node-updates"></a>Traiter les mises à jour des nœuds Linux

Chaque soir, les nœuds Linux dans AKS obtiennent les correctifs de sécurité par le biais de leur canal de mise à jour de distribution. Ce comportement est configuré automatiquement à mesure que les nœuds sont déployés dans un cluster AKS. Pour minimiser les perturbations et l’impact potentiel sur les charges de travail en cours d’exécution, les nœuds ne sont pas automatiquement redémarrés si un correctif de sécurité ou mise à jour du noyau l’exige. Pour plus d’informations sur le traitement des redémarrages de nœud, consultez la section[Appliquer des mises à jour de sécurité et du noyau à des nœuds dans AKS][aks-kured].

### <a name="node-image-upgrades"></a>Mises à niveau d’images de nœud

Les mises à niveau sans assistance appliquent les mises à jour au système d’exploitation des nœuds Linux, mais l’image utilisée pour créer les nœuds de votre cluster reste inchangée. Si un nouveau nœud Linux est ajouté à votre cluster, l’image d’origine est utilisée pour créer ce nœud. Ce nouveau nœud recevra toutes les mises à jour de sécurité et de noyau disponibles au cours de la vérification automatique chaque nuit, mais restera non corrigé jusqu’à ce que toutes les vérifications et tous les redémarrages soient terminés. Vous pouvez utiliser la mise à niveau des images de nœud pour rechercher et mettre à jour les images de nœud utilisées par votre cluster. Pour plus d'informations sur la mise à niveau des images de nœud, consultez [Mise à niveau des images de nœud Azure Kubernetes Service (AKS)][node-image-upgrade].

## <a name="process-windows-server-node-updates"></a>Traiter les mises à jour des nœuds Windows Server

Pour les nœuds Windows Server, effectuez régulièrement une opération de mise à niveau des images de nœud pour isoler et drainer de façon sécurisée les pods et déployer les nœuds mis à jour.

<!-- EXTERNAL LINKS -->
[k8s-apparmor]: https://kubernetes.io/docs/tutorials/clusters/apparmor/
[seccomp]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#seccomp
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-get-upgrades]: /cli/azure/aks#az_aks_get_upgrades
[az-aks-upgrade]: /cli/azure/aks#az_aks_upgrade
[aks-supported-versions]: supported-kubernetes-versions.md
[aks-upgrade]: upgrade-cluster.md
[aks-best-practices-identity]: concepts-identity.md
[aks-kured]: node-updates-kured.md
[aks-aad]: ./azure-ad-integration-cli.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[pod-security-contexts]: developer-best-practices-pod-security.md#secure-pod-access-to-resources
[aks-ssh]: ssh.md
[security-center-aks]: ../defender-for-cloud/defender-for-kubernetes-introduction.md
[node-image-upgrade]: node-image-upgrade.md
