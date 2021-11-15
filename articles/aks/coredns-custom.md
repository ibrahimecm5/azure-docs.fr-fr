---
title: Personnaliser CoreDNS pour AKS (Azure Kubernetes Service)
description: Découvrir comment personnaliser CoreDNS pour ajouter des sous-domaines ou étendre des points de terminaison DNS personnalisés à l’aide d’AKS (Azure Kubernetes Service)
services: container-service
author: palma21
ms.topic: article
ms.date: 03/15/2019
ms.author: jpalma
ms.openlocfilehash: c62e269ae20b8da974b4ba7ef72ac7171ee0e88d
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577970"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>Personnaliser CoreDNS avec Azure Kubernetes Service

AKS (Azure Kubernetes Service) utilise le projet [CoreDNS][coredns] pour la gestion et la résolution DNS de cluster avec tous les clusters de version *1.12.x* et supérieures. Avant, le projet utilisé était kube-dns. Ce projet kube-dns est désormais déconseillé. Pour plus d’informations sur la personnalisation de CoreDNS et sur Kubernetes, consultez la [documentation officielle en amont][corednsk8s].

AKS étant un service managé, vous ne pouvez pas modifier la configuration principale pour CoreDNS (fichier *CoreFile*). Au lieu de cela, vous utilisez un fichier *ConfigMap* Kubernetes pour remplacer les paramètres par défaut. Pour afficher les ConfigMaps CoreDNS AKS par défaut, utilisez la commande `kubectl get configmaps --namespace=kube-system coredns -o yaml`.

Cet article vous montre comment utiliser les ConfigMaps pour les options de personnalisation de base de CoreDNS dans AKS. Cette approche diffère de la configuration de CoreDNS dans d’autres contextes comme l’utilisation de CoreFile. Vérifiez la version de CoreDNS que vous exécutez, car les valeurs de configuration peuvent changer d’une version à l’autre.

> [!NOTE]
> `kube-dns` proposait différentes [options de personnalisation][kubednsblog] via une carte de configuration Kubernetes. CoreDNS n’est **pas** rétrocompatible avec kube-dns. Les personnalisations que vous avez utilisées précédemment doivent être mises à jour pour pouvoir être utilisées avec CoreDNS.

## <a name="before-you-begin"></a>Avant de commencer

Cet article suppose que vous avez un cluster AKS existant. Si vous avez besoin d’un cluster AKS, consultez le guide de démarrage rapide d’AKS [avec Azure CLI][aks-quickstart-cli]ou avec le [Portail Azure][aks-quickstart-portal].

Quand vous créez une configuration semblable aux exemples ci-dessous, les noms dans la section *data* doivent se terminer par *.server* ou *.override*. La convention de nommage est définie dans le Configmap CoreDNS AKS par défaut, que vous pouvez voir à l’aide de la commande `kubectl get configmaps --namespace=kube-system coredns -o yaml`.

## <a name="what-is-supportedunsupported"></a>Ce qui est pris en charge/non pris en charge

Tous les plug-ins CoreDNS intégrés sont pris en charge. Aucun module complémentaire/plug-in tiers n’est pris en charge.

## <a name="rewrite-dns"></a>Réécriture DNS

Voici un scénario qui consiste à effectuer des réécritures de noms DNS à la volée. Dans l’exemple suivant, remplacez `<domain to be written>` par votre propre nom de domaine complet. Créez un fichier nommé `corednsms.yaml` et collez l’exemple de configuration suivant :

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
  <domain to be rewritten>.com:53 {
  log
  errors
  rewrite stop {
    name regex (.*)\.<domain to be rewritten>.com {1}.default.svc.cluster.local
    answer name (.*)\.default\.svc\.cluster\.local {1}.<domain to be rewritten>.com
  }
  forward . /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10, but that server must be able to resolve the rewritten domain name
}
```

> [!IMPORTANT]
> Si vous redirigez vers un serveur DNS, tel que l’adresse IP du service CoreDNS, ce serveur DNS doit être en mesure de résoudre le nom de domaine réécrit.

Créez le ConfigMap à l’aide de la commande [kubectl apply configmap][kubectl-apply] et spécifiez le nom de votre manifeste YAML :

```console
kubectl apply -f corednsms.yaml
```

Pour vérifier que les personnalisations ont bien été appliquées, utilisez la commande [kubectl get configmaps][kubectl-get] et spécifiez votre ConfigMap *coredns-custom* :

```
kubectl get configmaps --namespace=kube-system coredns-custom -o yaml
```

Maintenant, forcez CoreDNS à recharger le ConfigMap. La commande [kubectl delete pod][kubectl delete] n’est pas destructrice et n’entraîne aucun temps d’arrêt. Une fois supprimés, les pods `kube-dns` sont recréés par Kubernetes Scheduler. Ces nouveaux pods intègrent le changement de valeur de durée de vie (TTL).

```console
kubectl delete pod --namespace kube-system -l k8s-app=kube-dns
```

> [!Note]
> La commande ci-dessus est correcte. Bien que nous modifions `coredns`, le déploiement s’effectue sous l’étiquette **kube-dns**.

## <a name="custom-forward-server"></a>Serveur de transfert personnalisé

Si vous avez besoin de spécifier un serveur de transfert pour votre trafic réseau, vous pouvez créer un ConfigMap pour personnaliser le DNS. Dans l’exemple suivant, mettez à jour le nom et l’adresse du `forward` avec les valeurs de votre propre environnement. Créez un fichier nommé `corednsms.yaml` et collez l’exemple de configuration suivant :

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        forward foo.com 1.1.1.1
    }
```

Comme dans les exemples suivants, créez le ConfigMap à l’aide de la commande [kubectl apply configmap][kubectl-apply] et spécifiez le nom de votre manifeste YAML. Forcez ensuite CoreDNS à recharger le ConfigMap à l’aide de la commande [kubectl delete pod][kubectl delete] pour permettre à Kubernetes Scheduler de le recréer :

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>Utiliser des domaines personnalisés

Vous pouvez décider de configurer des domaines personnalisés qui ne peuvent être résolus qu’en interne. Par exemple, vous pouvez souhaiter résoudre le domaine personnalisé *puglife.local*, qui n’est pas un domaine de niveau supérieur valide. Sans un ConfigMap de domaine personnalisé, le cluster AKS ne peut pas résoudre l’adresse.

Dans l’exemple suivant, mettez à jour le domaine personnalisé et l’adresse IP vers lesquels le trafic doit être dirigé avec les valeurs de votre propre environnement. Créez un fichier nommé `corednsms.yaml` et collez l’exemple de configuration suivant :

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  puglife.server: | # you may select any name here, but it must end with the .server file extension
    puglife.local:53 {
        errors
        cache 30
        forward . 192.11.0.1  # this is my test/dev DNS server
    }
```

Comme dans les exemples suivants, créez le ConfigMap à l’aide de la commande [kubectl apply configmap][kubectl-apply] et spécifiez le nom de votre manifeste YAML. Forcez ensuite CoreDNS à recharger le ConfigMap à l’aide de la commande [kubectl delete pod][kubectl delete] pour permettre à Kubernetes Scheduler de le recréer :

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="stub-domains"></a>Domaines de stub

CoreDNS peut aussi être utilisé pour configurer des domaines de stub. Dans l’exemple suivant, mettez à jour les domaines personnalisés et les adresses IP avec les valeurs de votre propre environnement. Créez un fichier nommé `corednsms.yaml` et collez l’exemple de configuration suivant :

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    abc.com:53 {
        errors
        cache 30
        forward . 1.2.3.4
    }
    my.cluster.local:53 {
        errors
        cache 30
        forward . 2.3.4.5
    }

```

Comme dans les exemples suivants, créez le ConfigMap à l’aide de la commande [kubectl apply configmap][kubectl-apply] et spécifiez le nom de votre manifeste YAML. Forcez ensuite CoreDNS à recharger le ConfigMap à l’aide de la commande [kubectl delete pod][kubectl delete] pour permettre à Kubernetes Scheduler de le recréer :

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="hosts-plugin"></a>Plug-in Hosts

Comme tous les plug-ins intégrés sont pris en charge, le plug-in [Hosts][coredns hosts] de CoreDNS peut aussi être personnalisé :

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom # this is the name of the configmap you can overwrite with your changes
  namespace: kube-system
data:
    test.override: | # you may select any name here, but it must end with the .override file extension
          hosts { 
              10.0.0.1 example1.org
              10.0.0.2 example2.org
              10.0.0.3 example3.org
              fallthrough
          }
```

## <a name="troubleshooting"></a>Résolution des problèmes

Pour connaître les étapes de dépannage générales de CoreDNS, telles que la vérification des points de terminaison ou de la résolution, consultez [Déboguer la résolution DNS][coredns-troubleshooting].

Pour activer la journalisation de requête DNS, appliquez la configuration suivante dans votre ConfigMap coredns-custom :

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  log.override: | # you may select any name here, but it must end with the .override file extension
        log
```

Après avoir appliqué les modifications de configuration, utilisez la commande `kubectl logs` pour afficher la journalisation de débogage de CoreDNS. Par exemple :

```console
kubectl logs --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="next-steps"></a>Étapes suivantes

Cet article vous a présenté quelques exemples de scénarios de personnalisation de CoreDNS. Pour obtenir des informations sur le projet CoreDNS, consultez [la page du projet en amont CoreDNS][coredns].

Pour en savoir plus sur les principaux concepts de réseau, consultez [Concepts de réseau pour les applications dans AKS][concepts-network].

<!-- LINKS - external -->
[kubednsblog]: https://www.danielstechblog.io/using-custom-dns-server-for-domain-specific-name-resolution-with-azure-kubernetes-service/
[coredns]: https://coredns.io/
[corednsk8s]: https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/#coredns
[dnscache]: https://coredns.io/plugins/cache/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[coredns hosts]: https://coredns.io/plugins/hosts/
[coredns-troubleshooting]: https://kubernetes.io/docs/tasks/administer-cluster/dns-debugging-resolution/

<!-- LINKS - internal -->
[concepts-network]: concepts-network.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
