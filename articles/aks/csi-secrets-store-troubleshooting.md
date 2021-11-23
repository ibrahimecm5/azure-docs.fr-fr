---
title: Résoudre des problèmes de fournisseur Azure Key Vault pour le pilote CSI du magasin de secrets sur Azure Kubernetes Service (AKS)
description: Découvrez comment résoudre des problèmes courants lors de l’utilisation du fournisseur Azure Key Vault pour le pilote CSI du magasin de secrets avec Azure Kubernetes Service (AKS).
author: nickomang
ms.service: container-service
ms.topic: troubleshooting
ms.date: 10/18/2021
ms.author: nickoman
ms.openlocfilehash: 94532cd6d0aa8f65bbd17b26886da4ee05a5860e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132335933"
---
# <a name="troubleshooting-azure-key-vault-provider-for-secrets-store-csi-driver"></a>Résolution de problèmes de fournisseur Azure Key Vault pour le pilote CSI du magasin de secrets

Cet article fournit une vue d’ensemble des composants qui peuvent faciliter la résolution des problèmes, ainsi qu’une liste des problèmes courants et de leurs résolutions.

## <a name="logging"></a>Journalisation

Les journaux du fournisseur Azure Key Vault sont disponibles dans les pods du fournisseur. Pour résoudre des problèmes liés au fournisseur, vous pouvez consulter les journaux du pod du fournisseur s’exécutant sur le même nœud que votre pod d’application :

```bash
# find the secrets-store-provider-azure pod running on the same node as your application pod
kubectl get pods -l app=secrets-store-provider-azure -n kube-system -o wide
kubectl logs -l app=secrets-store-provider-azure -n kube-system --since=1h | grep ^E
```

Les journaux du pilote CSI du magasin de secrets sont également accessibles :

```bash
# find the secrets-store-csi-driver pod running on the same node as your application pod
kubectl get pods -l app=secrets-store-csi-driver -n kube-system -o wide
kubectl logs -l app=secrets-store-csi-driver -n kube-system --since=1h | grep ^E
```

## <a name="common-issues"></a>Problèmes courants

### <a name="failed-to-get-key-vault-token-nmi-response-failed-with-status-code-404"></a>Échec de l’extraction du jeton de coffre de clés : la réponse nmi a échoué avec le code d’état : 404

Si vous avez reçu le message d’erreur suivant dans les journaux/événements :

```bash
Warning  FailedMount  74s    kubelet            MountVolume.SetUp failed for volume "secrets-store-inline" : kubernetes.io/csi: mounter.SetupAt failed: rpc error: code = Unknown desc = failed to mount secrets store objects for pod default/test, err: rpc error: code = Unknown desc = failed to mount objects, error: failed to get keyvault client: failed to get key vault token: nmi response failed with status code: 404, err: <nil>
```

Cela signifie que le composant NMI dans aad-pod-identity a retourné une erreur pour la demande de jeton. Pour obtenir plus d’informations sur l’erreur, consultez les journaux du pod NMI et reportez-vous au [guide de résolution des problèmes][aad-troubleshooting] d’identité de pod AAD pour résoudre le problème.

### <a name="keyvaultbaseclientgetsecret-failure-sending-request-statuscode0--original-error-context-canceled"></a>keyvault.BaseClient#GetSecret: Failure sending request: StatusCode=0 – Original Error: context canceled

Si vous avez reçu le message d’erreur suivant dans les journaux/événements :

```bash
E1029 17:37:42.461313       1 server.go:54] failed to process mount request, error: keyvault.BaseClient#GetSecret: Failure sending request: StatusCode=0 -- Original Error: context deadline exceeded
```

Cela signifie que le pod du fournisseur ne peut pas accéder à l’instance AKV, car :

- Une règle de pare-feu bloque le trafic de sortie du fournisseur.
- Stratégies réseau configurées dans le cluster, qui bloquent le trafic de sortie.
- Les pods du fournisseur s’exécutent sur hostNetwork. Ainsi, s’il existe une stratégie bloquant ce trafic, ou des instabilités du réseau sur le nœud, cela peut entraîner la défaillance ci-dessus. Vérifiez les stratégies configurées pour bloquer le trafic et la liste d’autorisation des pods de fournisseur. Assurez-vous également qu’il existe une connectivité à Azure AD et au Key Vault à partir du nœud.

Vous pouvez tester la connectivité de Key Vault à partir du pod s’exécutant sur le réseau hôte comme suit :

- Créez un pod

  ```bash
  cat <<EOF | kubectl apply -f -
  apiVersion: v1
  kind: Pod
  metadata:
    name: curl
  spec:
    hostNetwork: true
    containers:
    - args:
      - tail
      - -f
      - /dev/null
      image: curlimages/curl:7.75.0
      name: curl
    dnsPolicy: ClusterFirst
    restartPolicy: Always
  EOF
  ```

- Exécutez le pod créé ci-dessus

  ```bash
  kubectl exec -it curl -- sh
  ```

- Authentifiez-vous auprès d’AKV

  ```bash
  curl -X POST 'https://login.microsoftonline.com/<AAD_TENANT_ID>/oauth2/v2.0/token' -d 'grant_type=client_credentials&client_id=<AZURE_CLIENT_ID>&client_secret=<AZURE_CLIENT_SECRET>&scope=https://vault.azure.net/.default'
  ```

- Essayez d’obtenir un secret déjà créé dans AKV

  ```bash
  curl -X GET 'https://<KEY_VAULT_NAME>.vault.azure.net/secrets/<SECRET_NAME>?api-version=7.2' -H "Authorization: Bearer <ACCESS_TOKEN_ACQUIRED_ABOVE>"
  ```

<!-- LINKS EXTERNAL -->
[aad-troubleshooting]: https://azure.github.io/aad-pod-identity/docs/troubleshooting/
