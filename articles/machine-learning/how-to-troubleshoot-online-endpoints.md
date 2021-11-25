---
title: Résolution des problèmes de déploiement de points de terminaison en ligne (préversion)
titleSuffix: Azure Machine Learning
description: Découvrez comment résoudre certaines erreurs de déploiement et de scoring courantes avec des points de terminaison en ligne.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
author: petrodeg
ms.author: petrodeg
ms.reviewer: laobri
ms.date: 11/03/2021
ms.topic: troubleshooting
ms.custom: devplatv2
ms.openlocfilehash: 02f65a5c07536afb1fb20c3f85c444f2376c9b34
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137570"
---
# <a name="troubleshooting-online-endpoints-deployment-and-scoring-preview"></a>Résolution des problèmes de déploiement et de scoring de points de terminaison en ligne (préversion)

Découvrez comment résoudre les problèmes courants de déploiement et de scoring de points de terminaison en ligne Azure Machine Learning (préversion).

La structure de ce document est axée sur l’approche à adopter pour la résolution des problèmes :

1. Utilisez un [déploiement local](#deploy-locally) pour tester et déboguer vos modèles localement avant le déploiement dans le cloud.
1. Utilisez des [journaux de conteneur](#get-container-logs) pour déboguer les problèmes plus facilement.
1. Appréhendez les [erreurs de déploiement courantes](#common-deployment-errors) et la façon de les résoudre.

La section [Codes d’état HTTP](#http-status-codes) explique comment les erreurs d’appel et de prédiction sont mappées aux codes d’état HTTP quand le scoring des points de terminaison est effectué avec des requêtes REST.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Prérequis

* Un **abonnement Azure**. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://azure.microsoft.com/free/).
* [Interface de ligne de commande Azure](/cli/azure/install-azure-cli).
* [L’installation, la configuration et l’utilisation de l’interface CLI (v2) (préversion)](how-to-configure-cli.md).

## <a name="deploy-locally"></a>Déploiement local

Le déploiement local consiste à déployer un modèle dans un environnement Docker local. Le déploiement local est utile pour le test et le débogage avant le déploiement dans le cloud.

> [!TIP]
> Utilisez Visual Studio Code pour tester et déboguer vos points de terminaison localement. Pour plus d’informations, consultez [Déboguer les points de terminaison en ligne localement dans Visual Studio Code](how-to-debug-managed-online-endpoints-visual-studio-code.md).

Le déploiement local prend en charge la création, la mise à jour et la suppression d’un point de terminaison local. Il vous permet également d’appeler et d’obtenir des journaux à partir du point de terminaison. Pour utiliser le déploiement local, ajoutez `--local` à la commande CLI appropriée :

```azurecli
az ml online-deployment create --endpoint-name <endpoint-name> -n <deployment-name> -f <spec_file.yaml> --local
```

Le déploiement local inclut les étapes suivantes :

- Docker génère une nouvelle image conteneur ou tire (pull) une image existante du cache Docker local. Si une image existante correspond à la partie environnement du fichier de spécification, elle est utilisée.
- Docker démarre un nouveau conteneur avec les artefacts locaux montés tels que les fichiers de code et de modèle.

Pour plus d’informations, consultez [Déployer localement dans Déployer et scorer un modèle Machine Learning avec un point de terminaison en ligne managé (préversion)](how-to-deploy-managed-online-endpoints.md#deploy-and-debug-locally-by-using-local-endpoints).

## <a name="get-container-logs"></a>Obtenir les journaux de conteneur

Vous ne pouvez pas accéder directement à la machine virtuelle sur laquelle le modèle est déployé. Toutefois, vous pouvez obtenir les journaux de certains des conteneurs qui s’exécutent sur la machine virtuelle. La quantité d’informations dépend de l’état du provisionnement du déploiement. Si le conteneur spécifié est opérationnel, vous voyez sa sortie de console. Sinon, vous obtenez un message vous invitant à réessayer plus tard.

Pour voir la sortie de journal du conteneur, utilisez la commande CLI suivante :

```azurecli
az ml online-deployment get-logs -e <endpoint-name> -n <deployment-name> -l 100
```

ou

```azurecli
    az ml online-deployment get-logs --endpoint-name <endpoint-name> --name <deployment-name> --lines 100
```

Ajoutez `--resource-group` et `--workspace-name` aux commandes ci-dessus si vous n’avez pas déjà défini ces paramètres avec `az configure`.

Pour obtenir des informations sur la définition de ces paramètres, et si des valeurs sont déjà définies, exécutez :

```azurecli
az ml online-deployment get-logs -h
```

Par défaut, les journaux sont tirés du serveur d’inférence. Les journaux incluent le journal de console du serveur d’inférence, qui contient les instructions print/log de votre code « score.py ».

> [!NOTE]
> Si vous utilisez la journalisation Python, veillez à utiliser l’ordre de niveau de journalisation approprié pour les messages à publier dans les journaux. Par exemple, INFO.


Vous pouvez également obtenir des journaux à partir du conteneur de l’initialiseur de stockage en passant `–-container storage-initializer`. Ces journaux contiennent des informations indiquant si le code et les données de modèle ont été correctement téléchargés vers le conteneur.

Ajoutez `--help` et/ou `--debug` aux commandes pour voir plus d’informations. Incluez l’en-tête `x-ms-client-request-id` pour faciliter la résolution des problèmes.

## <a name="common-deployment-errors"></a>Erreurs de déploiement courantes

Vous trouverez ci-dessous une liste des erreurs de déploiement courantes signalées, liées à l’état de l’opération de déploiement.

* [OutOfQuota](#error-outofquota)
* [OutOfCapacity](#error-outofcapacity)
* [BadArgument](#error-badargument)
* [ResourceNotReady](#error-resourcenotready)
* [ResourceNotFound](#error-resourcenotfound)
* [OperationCancelled](#error-operationcancelled)
* [InternalServerError](#error-internalservererror)

### <a name="error-outofquota"></a>ERREUR : OutOfQuota

Voici une liste des ressources courantes qui peuvent dépasser les quotas lors de l’utilisation des services Azure :

* [UC](#cpu-quota)
* [Affectations de rôle](#role-assignment-quota)
* [Points de terminaison](#endpoint-quota)
* [Kubernetes](#kubernetes-quota)
* [Autres](#other-quota)

#### <a name="cpu-quota"></a>Quota de processeur

Avant de déployer un modèle, vous devez disposer d’un quota de calcul suffisant. Ce quota définit le nombre de vCores disponibles par abonnement, par espace de travail, par référence SKU et par région. Chaque déploiement puise dans le quota disponible (qu’il rétablit après la suppression) en fonction du type de référence SKU.

Pour atténuer le risque de quota insuffisant, vous pouvez notamment vérifier s’il existe des déploiements inutilisés qui pourraient être supprimés. Vous pouvez également envoyer une [demande d’augmentation du quota](how-to-manage-quotas.md#request-quota-increases).

#### <a name="role-assignment-quota"></a>Quota d’attribution de rôle

Essayez de supprimer les attributions de rôle non utilisées dans cet abonnement. Vous pouvez vérifier toutes les attributions de rôle dans le portail Azure, dans le menu Contrôle d'accès.

#### <a name="endpoint-quota"></a>Quota de point de terminaison

Essayez de supprimer les points de terminaison non utilisés dans cet abonnement.

#### <a name="kubernetes-quota"></a>Quota Kubernetes

Ni le processeur ni la mémoire demandés ne peuvent être satisfaits. Ajustez votre demande ou le cluster.

#### <a name="other-quota"></a>Autre quota

Pour exécuter le code `score.py` fourni dans le cadre du déploiement, Azure crée un conteneur qui comprend toutes les ressources qui lui sont nécessaires, puis exécute le script de scoring sur ce conteneur.

Si votre conteneur n’a pas pu démarrer, cela signifie que le scoring n’a pas être effectué. Cela peut être dû au fait que le conteneur demande une quantité de ressources supérieure à celle prise en charge par `instance_type`. Si c’est le cas, envisagez de mettre à jour le `instance_type` du déploiement en ligne.

Pour obtenir la raison exacte d’une erreur, exécutez : 

```azurecli
az ml online-deployment get-logs -e <endpoint-name> -n <deployment-name> -l 100
```

### <a name="error-outofcapacity"></a>ERREUR : OutOfCapacity

La taille de machine virtuelle spécifiée n’a pas pu être approvisionnée en raison d’un manque de capacité Azure Machine Learning. Réessayez ultérieurement ou essayez d’effectuer le déploiement dans une autre région.

### <a name="error-badargument"></a>ERREUR : BadArgument

Vous trouverez ci-dessous une liste des causes possibles pour cette erreur :

* [La demande de ressource était supérieure aux limites](#resource-requests-greater-than-limits)
* [Impossible de télécharger les ressources](#unable-to-download-resources)

#### <a name="resource-requests-greater-than-limits"></a>Demandes de ressource supérieures aux limites

Les demandes de ressources doivent être inférieures ou égales aux limites. Si vous ne définissez pas de limites, nous définissons les valeurs par défaut lorsque vous associez votre calcul à un espace de travail Azure Machine Learning. Vous pouvez vérifier les limites dans le portail Azure ou à l’aide de la commande `az ml compute show`.

#### <a name="unable-to-download-resources"></a>Impossible de télécharger les ressources

Après avoir provisionné la ressource de calcul, lors de la création du déploiement, Azure essaie de tirer (pull) l’image conteneur de l’utilisateur à partir du registre ACR (Azure Container Registry) privé de l’espace de travail, puis monte les artefacts de modèle et de code utilisateur dans le conteneur utilisateur à partir du compte de stockage de l’espace de travail.

Tout d’abord, vérifiez s’il existe un problème d’autorisation lors de l’accès au registre ACR.

Pour tirer les blobs, Azure utilise des [identités managées](../active-directory/managed-identities-azure-resources/overview.md) afin d’accéder au compte de stockage.

  - Si vous avez créé le point de terminaison associé avec SystemAssigned, l’autorisation RBAC (contrôle d’accès en fonction du rôle) Azure est accordée automatiquement et aucune autre autorisation n’est nécessaire.

  - Si vous avez créé le point de terminaison associé avec UserAssigned, l’identité managée de l’utilisateur doit disposer de l’autorisation Lecteur de données blob de stockage sur le compte de stockage de l’espace de travail.

Au cours de ce processus, vous pouvez rencontrer différents problèmes en fonction de l’étape à laquelle l’opération a échoué :

* [Impossible de télécharger l’image conteneur utilisateur](#unable-to-download-user-container-image)
* [Impossible de télécharger les artefacts de modèle ou de code utilisateur](#unable-to-download-user-model-or-code-artifacts)

Pour obtenir plus de détails sur ces erreurs, exécutez :

```azurecli
az ml online-deployment get-logs -n <endpoint-name> --deployment <deployment-name> --l 100
``` 

#### <a name="unable-to-download-user-container-image"></a>Impossible de télécharger l’image conteneur utilisateur

Il est possible que le conteneur utilisateur soit introuvable.

Vérifiez que l’image conteneur est disponible dans le registre ACR de l’espace de travail.

Par exemple, si l’image est `testacr.azurecr.io/azureml/azureml_92a029f831ce58d2ed011c3c42d35acb:latest`, vérifiez le dépôt avec `az acr repository show-tags -n testacr --repository azureml/azureml_92a029f831ce58d2ed011c3c42d35acb --orderby time_desc --output table`.

#### <a name="unable-to-download-user-model-or-code-artifacts"></a>Impossible de télécharger les artefacts de modèle ou de code utilisateur

Il est possible que les artefacts de modèle ou de code utilisateur soient introuvables.

Vérifiez que les artefacts de modèle et de code sont inscrits auprès du même espace de travail que le déploiement. Utilisez la commande `show` pour voir les détails d’un artefact de modèle ou de code dans un espace de travail. 

- Par exemple : 
  
  ```azurecli
  az ml model show --name <model-name>
  az ml code show --name <code-name> --version <version>
  ```
 
  Vous pouvez également vérifier si les blobs sont présents dans le compte de stockage de l’espace de travail.

- Par exemple, si l’objet blob est `https://foobar.blob.core.windows.net/210212154504-1517266419/WebUpload/210212154504-1517266419/GaussianNB.pkl`, vous pouvez utiliser cette commande pour vérifier s’il existe :

  `az storage blob exists --account-name foobar --container-name 210212154504-1517266419 --name WebUpload/210212154504-1517266419/GaussianNB.pkl --subscription <sub-name>`

### <a name="error-resourcenotready"></a>ERREUR : ResourceNotReady

Pour exécuter le code `score.py` fourni dans le cadre du déploiement, Azure crée un conteneur qui comprend toutes les ressources qui lui sont nécessaires, puis exécute le script de scoring sur ce conteneur. Dans ce scénario, l’erreur est la suivante : ce conteneur se bloque à l’exécution, ce qui signifie que le scoring ne peut pas être effectué. Cette erreur se produit dans les cas suivants :

- Il y a une erreur dans `score.py`. Utilisez `get-logs` pour aider à diagnostiquer les problèmes courants :
    - Package qui a été importé mais qui n’est pas dans l’environnement Conda.
    - Erreur de syntaxe.
    - Échec dans la méthode `init()`.
- Si `get-logs` ne produit aucun journal, cela signifie généralement que le conteneur n’a pas pu démarrer. Pour déboguer ce problème, essayez plutôt de [déployer localement](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/machine-learning/how-to-troubleshoot-online-endpoints.md#deploy-locally).
- Les probes readiness ou liveness ne sont pas configurées correctement.
- Il y a une erreur dans la configuration de l’environnement du conteneur, par exemple, une dépendance manquante.

### <a name="error-resourcenotfound"></a>ERREUR : ResourceNotFound

Cette erreur se produit quand Azure Resource Manager ne trouve pas une ressource nécessaire. Par exemple, vous recevez cette erreur si un compte de stockage est référencé, mais introuvable dans le chemin spécifié. Vérifiez attentivement les ressources qui peuvent avoir été fournies par un chemin exact ou l’orthographe des noms.

Pour plus d’informations, consultez [Résoudre les erreurs liées à des ressources introuvables](../azure-resource-manager/troubleshooting/error-not-found.md). 

### <a name="error-operationcancelled"></a>ERREUR : OperationCancelled

Les opérations Azure disposent d’un certain niveau de priorité. Elles sont exécutées dans l’ordre de la priorité la plus élevée à la plus faible. Cette erreur se produit quand votre opération a été remplacée par une autre opération disposant d’une priorité plus élevée. Une nouvelle tentative de l’opération peut permettre de l’exécuter sans annulation.

### <a name="error-internalservererror"></a>ERREUR : InternalServerError

Nous faisons de notre mieux pour fournir un service stable et fiable. Cependant, il arrive que les opérations ne se déroulent pas comme prévu. Si vous recevez cette erreur, cela signifie qu’il y a un problème de notre côté et que nous devons le corriger. Envoyez un [ticket de support client](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) avec toutes les informations associées et nous résoudrons le problème. 

## <a name="autoscaling-issues"></a>Problèmes de mise à l’échelle automatique

Si vous rencontrez des problèmes de mise à l’échelle automatique, consultez \[Résolution des problèmes liés à la mise à l’échelle automatique Azure](../azure-monitor/autoscale/autoscale-troubleshoot.md).

## <a name="http-status-codes"></a>Codes d’état HTTP

Quand vous accédez à des points de terminaison en ligne avec des requêtes REST, les codes d’état retournés correspondent aux [codes d’état HTTP](https://aka.ms/http-status-codes) standard. Vous trouverez ci-dessous des informations détaillées sur la façon dont les erreurs d’appel et de prédiction de points de terminaison sont mappées aux codes d’état HTTP.

| Code d’état| Motif |  Raison pour laquelle ce code peut être retourné |
| --- | --- | --- |
| 200 | OK | Votre modèle a été exécuté correctement et dans le cadre de votre limite de latence. |
| 401 | Non autorisé | Vous n’avez pas l’autorisation d’effectuer l’action demandée, par exemple un scoring, ou votre jeton a expiré. |
| 404 | Introuvable | Votre URL est incorrecte. |
| 408 | Délai d’expiration de la demande | L’exécution du modèle a dépassé le délai d’expiration spécifié dans `request_timeout_ms` sous `request_settings` dans la configuration du déploiement de votre modèle.|
| 413 | Charge utile trop importante | La charge utile de votre requête est supérieure à 1,5 mégaoctet. |
| 424 | Erreur de modèle | Si votre conteneur de modèle retourne une réponse autre que 200, Azure retourne un code 424. Vérifiez les en-têtes de réponse `ms-azureml-model-error-statuscode` et `ms-azureml-model-error-reason` pour plus d’informations. |
| 424 | Charge utile de la réponse trop importante | Si votre conteneur retourne une charge utile supérieure à 1,5 mégaoctet, Azure retourne un code 424. |
| 429 | Limitation du débit | Vous avez tenté d’envoyer plus de 100 requêtes par seconde à votre point de terminaison. |
| 429 | Requêtes en attente trop nombreuses | Votre modèle obtient plus de requêtes qu’il ne peut en gérer. Nous autorisons 2 * `max_concurrent_requests_per_instance` * `instance_count` requêtes simultanées. Les requêtes supplémentaires sont rejetées. Vous pouvez vérifier ces paramètres dans la configuration du déploiement de votre modèle sous `request_settings` et `scale_settings`. Si vous utilisez le scaling automatique, votre modèle obtient les requêtes trop rapidement par rapport à la vitesse de scale-up du système. Avec le scaling automatique, vous pouvez essayer de renvoyer les requêtes avec le [backoff exponentiel](https://aka.ms/exponential-backoff). Ceci peut donner au système le temps de s’ajuster. |
| 500 | Erreur interne du serveur | Échec de l’infrastructure provisionnée Azure ML. |

## <a name="next-steps"></a>Étapes suivantes

- [Déployer et scorer un modèle de machine learning avec un point de terminaison en ligne managé (préversion)](how-to-deploy-managed-online-endpoints.md)
- [Déploiement sûr pour les points de terminaison en ligne (préversion)](how-to-safely-rollout-managed-endpoints.md)
- [Informations de référence YAML sur les points de terminaison en ligne managés (préversion)](reference-yaml-endpoint-managed-online.md)

