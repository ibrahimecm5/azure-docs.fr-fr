---
title: Versions de Kubernetes prises en charge dans Azure Kubernetes Service
description: Comprendre la stratégie de prise en charge des versions de Kubernetes et le cycle de vie des clusters dans Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 08/09/2021
author: palma21
ms.author: jpalma
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3c094a40cd0150fe4134bf1352dfbe418346d63b
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577704"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Versions de Kubernetes prises en charge dans Azure Kubernetes Service (AKS)

La communauté Kubernetes publie des versions mineures à peu près tous les trois mois. Récemment, la communauté Kubernetes a [prolongé la fenêtre de prise en charge de chaque version de 9 mois à 12 mois](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/), à compter de la version 1.19.

Les versions mineures contiennent de nouvelles fonctionnalités et des améliorations. Les publications de correctifs sont plus fréquentes (parfois hebdomadaires) et sont destinées aux correctifs de bogues critiques dans une version mineure. Les versions de correctif comportent des correctifs pour les failles de sécurité et les bogues majeurs.

## <a name="kubernetes-versions"></a>Version de Kubernetes

Kubernetes utilise le schéma de contrôle de version standard [Semantic Versioning](https://semver.org/) pour chaque version :

```
[major].[minor].[patch]

Example:
  1.17.7
  1.17.8
```

Chaque chiffre de la version indique la compatibilité générale avec la version précédente :

* Les **versions principales** changent lorsque des mises à jour incompatibles de l’API ou la rétrocompatibilité peuvent être rompues.
* Les **versions mineures** changent lorsque des mises à jour de fonctionnalités rétrocompatibles avec les autres versions mineures sont apportées.
* Les **versions des patchs** changent quand des résolutions de bogues ont une compatibilité descendante.

Essayez d’exécuter la dernière version corrective de la version mineure que vous exécutez. Par exemple, supposons que votre cluster de production est sur **`1.17.7`** . **`1.17.8`** est la dernière version corrective disponible pour la série *1.17*. Vous devez effectuer la mise à niveau vers dès **`1.17.8`** que possible pour vous assurer que votre cluster est entièrement corrigé et pris en charge.

## <a name="kubernetes-version-support-policy"></a>Stratégie de prise en charge des versions de Kubernetes

AKS définit une version en disponibilité générale (GA) comme étant activée dans toutes les mesures SLO ou SLA et disponible dans toutes les régions. Il prend en charge trois versions mineures GA de Kubernetes :

* La dernière version mineure GA publiée dans AKS (que nous appellerons N).
* Deux versions mineures précédentes.
    * Chaque version mineure prise en charge gère également deux (2) correctifs stables au maximum.

AKS peut également prendre en charge des préversions, qui sont explicitement étiquetées et soumises aux [Conditions générales des préversions][preview-terms].

> [!NOTE]
> AKS applique des pratiques de déploiement sécurisé qui impliquent un déploiement graduel des régions. Par conséquent, la mise à disposition d’une nouvelle version dans toutes les régions peut prendre jusqu’à 10 jours ouvrés.

La fenêtre de prise en charge des versions de Kubernetes sur AKS est appelée « N-2 » : (N (dernière publication) - 2 (versions mineures)).

Par exemple, si AKS introduit *1.17.a* aujourd’hui, une prise en charge est assurée pour les versions suivantes :

Nouvelle version mineure    |    Liste des versions prises en charge
-----------------    |    ----------------------
1.17.a               |    1.17.a, 1.17.b, 1.16.c, 1.16.d, 1.15.e, 1.15.f

où « .lettre » est représentatif des versions correctives.

Quand une nouvelle version mineure est introduite, la version mineure et les publications des correctifs les plus anciennes prises en charge sont déconseillées et mises hors service. Par exemple, si la liste des versions actuellement prises en charge est :

```
1.17.a
1.17.b
1.16.c
1.16.d
1.15.e
1.15.f
```

AKS publie la version 1.18.\*, supprimant toutes les versions 1.15.\* du support dans après 30 jours.

> [!NOTE]
> Si les clients utilisent une version de Kubernetes non supportée, il leur sera demandé de mettre à jour lors de leur demande de support pour le cluster. Les clusters exécutant des versions de Kubernetes non supportées ne sont pas couverts par les politiques de support [AKS](./support-policies.md).

En plus de ce qui précède, AKS prend en charge au maximum deux versions **correctives** d’une version mineure donnée. Prenons les versions prise en charge suivantes :

```
Current Supported Version List
------------------------------
1.17.8, 1.17.7, 1.16.10, 1.16.9
```

Si AKS publie les versions `1.17.9` et `1.16.11`, les versions correctives les plus anciennes sont dépréciées et supprimées, et la liste des versions prises en charge devient :

```
New Supported Version List
----------------------
1.17.*9*, 1.17.*8*, 1.16.*11*, 1.16.*10*
```

### <a name="supported-kubectl-versions"></a>Versions de `kubectl` prises en charge

Vous pouvez utiliser une version mineure de `kubectl` plus ancienne ou plus récente que votre version de *kube-apiserver*, conforme à la [stratégie de prise en charge de Kubernetes pour kubectl](https://kubernetes.io/docs/setup/release/version-skew-policy/#kubectl).

Par exemple, si vous avez la version *1.17* de *kube-apiserver*, vous pouvez utiliser les versions *1.16* à *1.18* de `kubectl`.

Pour installer ou mettre à jour `kubectl` vers la dernière version, exécutez :

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az aks install-cli
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```powershell
Install-AzAksKubectl -Version latest
```
---

## <a name="release-and-deprecation-process"></a>Processus de publication et de dépréciation

Pour consulter la référence sur les versions à venir et les dépréciations, consultez le [Calendrier de publication AKS Kubernetes](#aks-kubernetes-release-calendar).

Pour les nouvelles versions **mineures** de Kubernetes :
  * AKS publie une annonce préalable avec la date prévisionnelle d’une nouvelle version et la dépréciation de l’ancienne version correspondante dans les [Notes de publication AKS](https://aka.ms/aks/releasenotes) au moins 30 jours avant la suppression.
  * AKS utilise [Azure Advisor](../advisor/advisor-overview.md) pour alerter les utilisateurs si une nouvelle version pose des problèmes dans leur cluster en raison d’API déconseillées. Azure Advisor est également utilisé pour alerter l’utilisateur s’il n’est plus pris en charge.
  * AKS publie une [notification d’intégrité des services](../service-health/service-health-overview.md) accessible à tous les utilisateurs disposant d’un accès à AKS et au portail, et envoie un e-mail aux administrateurs des abonnements avec les dates prévisionnelles de suppression des versions.

    > [!NOTE]
    > Pour savoir qui sont les administrateurs de votre abonnement ou pour les modifier, consultez [Gérer les abonnements Azure](../cost-management-billing/manage/add-change-subscription-administrator.md#assign-a-subscription-administrator).

  * Les utilisateurs ont **30 jours** à compter de la suppression d’une version pour effectuer une mise à niveau vers une version mineure prise en charge afin de continuer à bénéficier du support.

Pour les nouvelles versions de **correctif** de Kubernetes :
  * En raison de leur nature urgente, les versions correctives peuvent être introduites dans le service dès qu’elles sont disponibles.
  * En général, AKS ne communique pas beaucoup lors de la publication de nouvelles versions correctives. Toutefois, il surveille et valide constamment les correctifs CVE disponibles pour les prendre en charge en temps utile. Si un correctif critique est trouvé ou qu’une action des utilisateurs est requise, AKS avertit ces derniers qu’ils peuvent effectuer une mise à niveau vers le nouveau correctif.
  * Les utilisateurs ont **30 jours** à compter de la suppression d’une version corrective d’AKS pour effectuer une mise à niveau vers un correctif pris en charge et ainsi continuer de bénéficier du support.

### <a name="supported-versions-policy-exceptions"></a>Exceptions de la stratégie de version prise en charge

AKS se réserve le droit d’ajouter ou de supprimer de nouvelles versions ou des versions existantes avec une ou plusieurs productions critiques ayant un impact sur des bogues ou des problèmes de sécurité sans préavis.

Certaines versions de correctifs spécifiques peuvent être ignorées, ou le déploiement peut être accéléré en fonction de la gravité du bogue ou du problème de sécurité.

## <a name="azure-portal-and-cli-versions"></a>Versions du Portail Azure et de l’interface CLI

Quand vous déployez un cluster AKS dans le portail, avec Azure CLI ou avec Azure PowerShell, le cluster est toujours défini par défaut sur la version mineure n-1 et le dernier patch. Par exemple, si AKS prend en charge *1.17.a*, *1.17.b*, *1.16.c*, *1.16.d*, *1.15.e* et *1.15.f*, la version sélectionnée par défaut est *1.16.c*.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour savoir quelles sont les versions disponibles pour vos abonnement et région, utilisez la commande [az aks get-versions][az-aks-get-versions]. L’exemple suivant répertorie les versions Kubernetes disponibles pour la région *EastUS* :

```azurecli-interactive
az aks get-versions --location eastus --output table
```


### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Pour savoir quelles sont les versions disponibles pour vos abonnement et région, utilisez la cmdlet [Get-AzAksVersion][get-azaksversion]. L’exemple suivant répertorie les versions Kubernetes disponibles pour la région *EastUS* :

```azurepowershell-interactive
Get-AzAksVersion -Location eastus
```

---

## <a name="aks-kubernetes-release-calendar"></a>Calendrier des versions d’AKS Kubernetes

Pour connaître l’historique des versions antérieures, cliquez sur [Kubernetes](https://en.wikipedia.org/wiki/Kubernetes#History).

|  Version de K8s | Sortie en amont  | Préversion d’AKS  | Version GA d’AKS  | Fin de vie |
|--------------|-------------------|--------------|---------|-------------|
| 1,19  | 4 août 20  | Septembre 2020   | Nov. 2020  | 1.22 GA |
| 1.20  | 8 décembre2020  | Janvier 2021   | Mars 2021  | 1.23 GA |
| 1.21  | 8 avril 2021 | Mai 2021   | Juil. 2021  | 1.24 GA |
| 1,22  | 04 août 21 | Septembre 2021   | Novembre 2021  | 1.25 GA |
| 1.23  | Décembre 2021 | Janvier 2022   | Février 2022  | 1.26 GA |

## <a name="faq"></a>Questions fréquentes (FAQ)

**Comment Microsoft m’informe-t-il des nouvelles versions de Kubernetes ?**

L’équipe d’AKS publie des annonces préalables avec les dates prévues des nouvelles versions de Kubernetes dans notre documentation, sur notre [GitHub](https://github.com/Azure/AKS/releases) ainsi que dans des e-mails adressés aux administrateurs d’abonnements qui possèdent des clusters qui ne seront plus pris en charge.  Outre les annonces, AKS utilise également [Azure Advisor](../advisor/advisor-overview.md) pour informer le client dans le Portail Microsoft Azure afin de prévenir les utilisateurs s’ils ne sont plus pris en charge, ainsi que pour les avertir des API déconseillées qui auront une incidence sur leur application ou leur processus de développement.

**À quelle fréquence dois-je prévoir de mettre à niveau les versions de Kubernetes pour continuer à bénéficier de la prise en charge ?**

Avec Kubernetes 1.19, la [communauté open source a étendu la durée de prise en charge à une année](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/). AKS s’engage à activer les correctifs et à prendre en charge le respect des engagements en amont. Pour les clusters AKS sur 1.19 et versions ultérieures, vous pourrez effectuer une mise à niveau au moins une fois par an pour rester sur une version prise en charge.

**Que se passe-t-il quand un utilisateur met à niveau un cluster Kubernetes avec une version mineure non prise en charge ?**

Si vous utilisez la version *n-3* ou une version antérieure, vous êtes hors support et il vous sera demandé d’effectuer une mise à niveau. Une fois votre mise à niveau de la version n-3 à la version n-2 réussie, vous bénéficierez à nouveau de nos stratégies de support. Par exemple :

- Si la plus ancienne version d’AKS prise en charge est *1.15.a* et que vous utilisez la version *1.14.b* ou une version antérieure, vous êtes hors support.
- Une fois la mise à niveau de la version *1.14.b* à la version *1.15.a* ou à une version ultérieure réussie, vous bénéficierez à nouveau de nos stratégies de support.

Les passages à une version antérieure ne sont pas pris en charge.

**Que signifie « Ne plus disposer du support technique » ?**

« Ne plus disposer du support technique » signifie que :
* La version que vous exécutez n’est pas dans la liste des versions prises en charge.
* Vous serez invité à mettre à niveau le cluster vers une version prise en charge lors de la demande d’assistance, sauf si vous êtes dans la période de grâce de 30 jours après la dépréciation de la version.

Par ailleurs, AKS n’offre aucune garantie d’exécution ou autre pour les clusters qui ne figurent pas dans la liste des versions prises en charge.

**Que se passe-t-il quand un utilisateur fait évoluer (scaling) un cluster Kubernetes avec une version mineure non prise en charge ?**

Pour les versions mineures non prises en charge par AKS, le scale-in ou le scale-out devrait continuer à fonctionner. Étant donné qu’il n’existe aucune garantie de qualité de service, nous vous recommandons d’effectuer une mise à niveau pour que votre cluster soit pris en charge.

**Un utilisateur peut-il rester éternellement sur une même version de Kubernetes ?**

Si un cluster ne bénéficie plus du support depuis plus de trois (3) versions mineures et se révèle présenter des risques de sécurité, Azure vous contacte pour mettre à niveau votre cluster de manière proactive. À défaut d’action supplémentaire de votre part, Azure se réserve le droit d’effectuer automatiquement la mise à niveau de votre cluster à votre place.

**Quelle version le plan de contrôle prend-il en charge si le pool de nœuds n’est pas dans une des versions d’AKS prises en charge ?**

Le plan de contrôle doit se trouver dans une fenêtre de versions pour tous les pools de nœuds. Pour plus d’informations sur la mise à niveau du plan de contrôle ou des pools de nœuds, consultez la documentation sur la [mise à niveau des pools de nœuds](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools).

**Puis-je ignorer plusieurs versions d’AKS durant la mise à niveau d’un cluster ?**

Quand vous mettez à niveau un cluster AKS pris en charge, les versions mineures de Kubernetes ne peuvent pas être ignorées. La [stratégie d’asymétrie de version](https://kubernetes.io/releases/version-skew-policy/) des plans de contrôle Kubernetes ne prend pas en charge l’omission de la version mineure. Par exemple, les mises à niveau entre :

  * *1.12.x* -> *1.13.x* : autorisées.
  * *1.13.x* -> *1.14.x* : autorisées.
  * *1.12.x* -> *1.14.x* : non autorisées.

Pour mettre à niveau de *1.12.x* -> *1.14.x* :
1. Mettre à niveau de *1.12.x* -> *1.13.x*.
1. Mettre à niveau de *1.13.x* -> *1.14.x*.

Vous pouvez ignorer plusieurs versions seulement quand vous mettez à niveau une version non prise en charge vers la version minimale prise en charge. Par exemple, vous pouvez mettre à niveau une version *1.10.x* non prise en charge vers une version *1.15.x* prise en charge si *1.15* est la version mineure minimale prise en charge.

**Puis-je créer un nouveau cluster 1.xx.x pendant la période de prise en charge de 30 jours ?**

Non. Une fois qu’une version est déconseillée/supprimée, il n’est plus possible de créer de cluster avec cette version. Quand la modification est déployée, l’ancienne version est supprimée de votre liste de versions. Ce processus peut durer jusqu’à deux semaines après l’annonce et s’effectue progressivement par région.

**Je travaille sur une version désormais déconseillée, puis-je toujours ajouter de nouveaux pools de nœuds ? Ou une mise à niveau est-elle nécessaire ?**

Non. Vous n’êtes pas autorisé à ajouter des pools de nœuds de la version déconseillée à votre cluster. Vous pouvez ajouter des pools de nœuds d’une nouvelle version. Toutefois, vous devrez peut-être d’abord mettre à jour le plan de contrôle.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la mise à niveau de votre cluster, consultez [Mettre à niveau un cluster Azure Kubernetes Service (AKS)][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az_aks_get_versions
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[get-azaksversion]: /powershell/module/az.aks/get-azaksversion
