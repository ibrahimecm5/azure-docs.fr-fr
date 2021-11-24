---
title: Envoi (push) et tirage (pull) d’artefacts de chaîne d’approvisionnement
description: Envoyez (push) et tirez (pull) des artefacts de chaîne d’approvisionnement avec un registre de conteneurs privé dans Azure.
author: SteveLasker
manager: gwallace
ms.topic: article
ms.date: 11/11/2021
ms.author: stevelas
ms.custom: references_regions
ms.openlocfilehash: 4a8d3a4b73993a386d2cd49e2daa784a8bb7aced
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132495432"
---
# <a name="push-and-pull-supply-chain-artifacts-using-a-private-container-registry-in-azure-preview"></a>Envoi (push) et tirage (pull) d’artefacts de chaîne d’approvisionnement avec un registre de conteneurs privé dans Azure (version préliminaire)

Utilisez Azure Container Registry pour stocker et gérer un graphe d’artefacts, notamment les signatures, les nomenclatures logicielles, les résultats de l’analyse de sécurité ou d’autres types. 

![Graphe d’artefacts incluant une image conteneur, une signature et une nomenclature logicielle signée.](./media/container-registry-artifacts/oras-artifact-graph.svg)

Afin d’illustrer cette fonctionnalité, cet article montre comment utiliser l’outil [ORAS (OCI Registry as Storage)](https://oras.land) pour envoyer (push) un graphe d’artefacts vers un registre de conteneurs Azure, et le tirer (pull).

La prise en charge d’ORAS Artifacts constitue une fonctionnalité d'évaluation soumise à des [limitations](#preview-limitations). Elle exige une [redondance de zone](zone-redundancy.md), qui est disponible dans le niveau de service Premium. Pour plus d’informations sur les niveaux de service et les limites de registre, consultez [Niveaux de service d’Azure Container Registry](container-registry-skus.md).

## <a name="prerequisites"></a>Prérequis

* **Interface CLI ORAS** : l’interface de ligne de commande ORAS permet l’envoi (push), la découverte et le tirage (pull) d’artefacts vers un registre compatible ORAS Artifacts.
* **Azure CLI** : pour créer une identité, afficher la liste des référentiels et en supprimer, vous avez besoin d’une installation locale d’Azure CLI. La version 2.29.1 ou une version ultérieure est recommandée. Exécutez `az --version ` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli).
* **Docker (facultatif)** : pour suivre la procédure pas à pas, vous devez faire référence à une image conteneur. Vous pouvez utiliser Docker en local pour créer et envoyer (push) une image conteneur, ou faire référence à une image conteneur existante. Docker fournit des packages qui le configurent facilement sur n’importe quel système [macOS][docker-mac], [Windows][docker-windows] ou [Linux][docker-linux].

## <a name="preview-limitations"></a>Limitations de la version préliminaire

La prise en charge d’ORAS Artifacts est limitée à la région USA Centre Sud avec zones de disponibilité.

* Les registres géorépliqués ne répliquent pas les artefacts auxquels d’autres régions font référence. Les artefacts en question sont répliqués à mesure que des régions supplémentaires prennent en charge ORAS Artifacts.

## <a name="oras-installation"></a>Installation d’ORAS

Téléchargez et installez une version préliminaire d’ORAS pour votre système d’exploitation. Pour savoir comment extraire et installer le fichier pour votre système d’exploitation, en faisant référence à une version d’évaluation Alpha.1 issue du [référentiel GitHub ORAS][oras-preview-install], consultez [Instructions d’installation d’ORAS][oras-install-docs].

## <a name="configure-a-private-registry"></a>Configuration d’un registre privé

Configurez les variables d’environnement pour copier-coller facilement des commandes dans votre interpréteur de commandes. Les commandes peuvent être exécutées dans [Azure Cloud Shell](https://shell.azure.com/).

```console
ACR_NAME=myregistry
REGISTRY=$ACR_NAME.azurecr.io
REPO=net-monitor
TAG=v1
IMAGE=$REGISTRY/${REPO}:$TAG
```

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Si besoin, exécutez la commande [az group create](/cli/azure/group#az_group_create) afin de créer un groupe de ressources pour le registre.

```azurecli
az group create --name $ACR_NAME --location southcentralus
```
### <a name="create-oras-artifact-enabled-registry"></a>Création d’un registre compatible ORAS Artifact

La prise en charge en version préliminaire d’ORAS Artifacts exige une redondance de zone, qui requiert un niveau de service Premium dans la région USA Centre Sud. Exécutez la commande [az acr create](/cli/azure/acr#az_acr_create) pour créer un registre compatible ORAS Artifacts. Pour connaître d’autres options de registre, consultez l’aide de la commande `az acr create`.

```azurecli
az acr create \
  --resource-group $ACR_NAME \
  --name $ACR_NAME \
  --zone-redundancy enabled \
  --sku Premium \
  --output jsonc
```

Dans la sortie de la commande, repérez la propriété `zoneRedundancy` du registre. Lorsqu’elle est activée, le registre est redondant interzone et compatible ORAS Artifacts :

```JSON
{
  [...]
  "zoneRedundancy": "Enabled",
}
```

### <a name="sign-in-with-azure-cli"></a>Se connecter avec Azure CLI

[Connectez-vous](/cli/azure/authenticate-azure-cli) à Azure CLI avec votre identité pour envoyer (push) et tirer (pull) des artefacts à partir du registre de conteneurs.

Ensuite, utilisez la commande Azure CLI [az acr login](/cli/azure/acr#az_acr_login) pour accéder au registre.

```azurecli
az login
az acr login --name $ACR_NAME
```

> [!NOTE]
> `az acr login` utilise le client Docker pour définir un jeton Azure Active Directory dans le fichier `docker.config`. Le client Docker doit être installé et en cours d’exécution pour terminer le flux d’authentification individuel.

## <a name="sign-in-with-oras"></a>Se connecter avec ORAS

Cette section présente les solutions possibles pour se connecter au registre. Choisissez la méthode appropriée pour votre environnement.

Exécuter `oras login` pour s’authentifier auprès du registre. Vous pouvez transmettre les [informations d’identification de registre](container-registry-authentication.md) correspondant à votre scénario : des informations d’identification de principal de service, l’identité de l’utilisateur ou un jeton de portée de référentiel (version préliminaire).

- Authentifiez-vous avec votre [identité Azure AD individuelle](container-registry-authentication.md?tabs=azure-cli#individual-login-with-azure-ad) pour utiliser un jeton AD.

  ```bash
  USER_NAME="00000000-0000-0000-0000-000000000000"
  PASSWORD=$(az acr login --name $ACR_NAME --expose-token --output tsv --query accessToken)
  ```

- Authentifiez-vous avec un [jeton de portée de référentiel](container-registry-repository-scoped-permissions.md) (version préliminaire) pour utiliser des jetons non liés à AD.

  ```bash
  USER_NAME="oras-token"
  PASSWORD=$(az acr token create -n $USER_NAME \
                    -r $ACR_NAME \
                    --repository $REPO content/write \
                    --only-show-errors \
                    --query "credentials.passwords[0].value" -o tsv)
  ```

- Authentifiez-vous avec un [principal de service Azure Active Directory doté d’autorisations de tirage (pull) et d’envoi (push)](container-registry-auth-service-principal.md#create-a-service-principal) (rôle AcrPush) sur le registre.

  ```bash
  SERVICE_PRINCIPAL_NAME="oras-sp"
  ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)
  PASSWORD=$(az ad sp create-for-rbac --name $SERVICE_PRINCIPAL_NAME \
            --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
             --role acrpush \
            --query "password" --output tsv)
  USER_NAME=$(az ad sp list --display-name $SERVICE_PRINCIPAL_NAME --query "[].appId" --output tsv)
  ```

### <a name="sign-in-with-oras"></a>Se connecter avec ORAS

Fournissez les informations d’identification à `oras login`.

  ```bash
  oras login $REGISTRY \
    --username $USER_NAME \
    --password $PASSWORD
  ```

Pour lire le mot de passe à partir de Stdin, utilisez `--password-stdin`.

## <a name="push-a-container-image"></a>Envoi (push) d’une image conteneur

Cet exemple associe un graphe d’artefacts à une image conteneur. Créez et envoyez (push) une image conteneur, ou faites référence à une image existante du registre privé.

```bash
docker build -t $IMAGE https://github.com/wabbit-networks/net-monitor.git#main
docker push $IMAGE
```

## <a name="create-a-sample-signature-to-the-container-image"></a>Création d’un exemple de signature pour l’image conteneur

```bash
echo '{"artifact": "'${IMAGE}'", "signature": "pat hancock"}' > signature.json
```

### <a name="push-a-signature-to-the-registry-as-a-reference-to-the-container-image"></a>Envoi (push) d’une signature vers le registre, comme référence à l’image conteneur

La commande ORAS envoie (push) la signature à un référentiel, en faisant référence à un autre artefact avec le paramètre `subject`. `--artifact-type` fournit des artefacts de différenciation, comme des extensions de fichier qui permettent de créer différents types de fichiers. Il est possible d’envoyer (push) un ou plusieurs fichiers en spécifiant `file:mediaType`.

```bash
oras push $REGISTRY/$REPO \
    --artifact-type 'signature/example' \
    --subject $IMAGE \
    ./signature.json:application/json
```

Pour plus d’informations sur l’envoi (push) ORAS, consultez [Documentation d’ORAS][oras-push-docs].

## <a name="push-a-multi-file-artifact-as-a-reference"></a>Envoi (push) d’un artefact à plusieurs fichiers comme référence

Créez une documentation autour d’un artefact.

```bash
echo 'Readme Content' > readme.md
echo 'Detailed Content' > readme-details.md
```

Envoyez (push) l’artefact à plusieurs fichiers comme référence.

```bash
oras push $REGISTRY/$REPO \
    --artifact-type 'readme/example' \
    --subject $IMAGE \
    ./readme.md:application/markdown \
    ./readme-details.md:application/markdown
```

## <a name="discovering-artifact-references"></a>Découverte des références aux artefacts

La spécification d’ORAS Artifacts définit une [API de référents][oras-artifacts-referrers] pour la découverte de références à un artefact `subject`. La commande `oras discover` peut afficher la liste des références à l’image conteneur.

Utilisez `oras discover` pour afficher le graphe des artefacts maintenant stockés dans le registre.

```bash
oras discover -o tree $IMAGE
```

La sortie indique le début d’un graphe d’artefacts, dans lequel la signature et les documents apparaissent comme les enfants de l’image conteneur.

```output
myregistry.azurecr.io/net-monitor:v1
├── signature/example
│   └── sha256:555ea91f39e7fb30c06f3b7aa483663f067f2950dcb...
└── readme/example
    └── sha256:1a118663d1085e229ff1b2d4d89b5f6d67911f22e55...
```

## <a name="creating-a-deep-graphs-of-artifacts"></a>Création d’un graphe profond d’artefacts

La spécification ORAS Artifacts autorise les graphes profonds, ce qui donne accès à la nomenclature logicielle signée et à d’autres types d’artefacts.

### <a name="create-a-sample-sbom"></a>Création d’un exemple de nomenclature logicielle

```bash
echo '{"version": "0.0.0.0", "artifact": "'${IMAGE}'", "contents": "good"}' > sbom.json
```

### <a name="push-a-sample-sbom-to-the-registry"></a>Envoi (push) d’un exemple de nomenclature logicielle dans le registre

```bash
oras push $REGISTRY/$REPO \
  --artifact-type 'sbom/example' \
  --subject $IMAGE \
  ./sbom.json:application/json
```

### <a name="sign-the-sbom"></a>Signature de la nomenclature logicielle

Les artefacts envoyés (push) comme références ne possèdent généralement pas de balises, car ils sont considérés comme faisant partie de l’artefact subject. Pour envoyer (push) une signature à un artefact enfant d’un autre artefact, utilisez `oras discover` avec filtrage `--artifact-type` permettant de rechercher le condensé.

```bash
SBOM_DIGEST=$(oras discover -o json \
                --artifact-type sbom/example \
                $IMAGE | jq -r ".references[0].digest")
```

Créez une signature d’une nomenclature logicielle.

```bash
echo '{"artifact": "'$REGISTRY/${REPO}@$SBOM_DIGEST'", "signature": "pat hancock"}' > sbom-signature.json
```

### <a name="push-the-sbom-signature"></a>Envoi (push) de la signature de la nomenclature logicielle

```bash
oras push $REGISTRY/$REPO \
  --artifact-type 'signature/example' \
  --subject $REGISTRY/$REPO@$SBOM_DIGEST \
  ./sbom-signature.json:application/json
```

### <a name="view-the-graph"></a>Affichage du graphe

```bash
oras discover -o tree $IMAGE
```

Voici la sortie générée :

```output
myregistry.azurecr.io/net-monitor:v1
├── signature/example
│   └── sha256:555ea91f39e7fb30c06f3b7aa483663f067f2950dcb...
├── readme/example
│   └── sha256:1a118663d1085e229ff1b2d4d89b5f6d67911f22e55...
└── sbom/example
    └── sha256:4280eef9adb632b42cf200e7cd5a822a456a558e4f3142da6b...
        └── signature/example
            └── sha256:a31ab875d37eee1cca68dbb14b2009979d05594d44a075bdd7...
```

## <a name="pull-a-referenced-artifact"></a>Tirage (pull) d’un artefact auquel il est fait référence

Pour tirer (pull) un type auquel il est fait référence, le condensé de la référence est découvert avec la commande `oras discover`.

```bash
DOC_DIGEST=$(oras discover -o json \
              --artifact-type 'readme/example' \
              $IMAGE | jq -r ".references[0].digest")
```

### <a name="create-a-clean-directory-for-downloading"></a>Création d’un répertoire propre pour le téléchargement

```bash
mkdir ./download
```

### <a name="pull-the-docs-into-the-download-directory"></a>Tirage (pull) des documents dans le répertoire de téléchargement
```bash
oras pull -a -o ./download $REGISTRY/$REPO@$DOC_DIGEST
```
### <a name="view-the-docs"></a>Affichage des documents

```bash
ls ./download
```

## <a name="view-the-repository-and-tag-listing"></a>Affichage du référentiel et de la liste des balises

ORAS Artifacts permet d’envoyer (push), de découvrir, de tirer (pull) et de copier des graphes d’artefacts sans avoir à assigner des balises. La liste de balises peut ainsi se concentrer sur les artefacts que les utilisateurs ont en tête, par opposition aux signatures et aux nomenclatures logicielles associées aux images conteneur, aux charts Helm et à d’autres artefacts.

### <a name="view-a-list-of-tags"></a>Affichage d’une liste de balises

```azurecli
az acr repository show-tags \
  -n $ACR_NAME \
  --repository $REPO \
  -o jsonc
```

### <a name="view-a-list-of-manifests"></a>Affichage d’une liste de manifestes

Un référentiel peut comporter une liste de manifestes, balisés ou non.

```azurecli
az acr repository show-manifests \
  -n $ACR_NAME \
  --repository $REPO \
  --detail -o jsonc
```

Comme vous pouvez le constater, les manifestes d’image conteneur incluent `"tags":`.

```json
{
  "architecture": "amd64",
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "configMediaType": "application/vnd.docker.container.image.v1+json",
  "createdTime": "2021-11-12T00:18:54.5123449Z",
  "digest": "sha256:a0fc570a245b09ed752c42d600ee3bb5b4f77bbd70d8898780b7ab4...",
  "imageSize": 2814446,
  "lastUpdateTime": "2021-11-12T00:18:54.5123449Z",
  "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
  "os": "linux",
  "tags": [
    "v1"
  ]
}
```

La signature n’est pas balisée, mais suivie comme référence `oras.artifact.manifest` à l’image conteneur.

```json
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2021-11-12T00:19:10.987156Z",
  "digest": "sha256:555ea91f39e7fb30c06f3b7aa483663f067f2950dcbcc0b0d...",
  "imageSize": 85,
  "lastUpdateTime": "2021-11-12T00:19:10.987156Z",
  "mediaType": "application/vnd.cncf.oras.artifact.manifest.v1+json"
}
```
## <a name="delete-all-artifacts-in-the-graph"></a>Suppression de tous les artefacts du graphe

La prise en charge de la spécification ORAS Artifacts permet de supprimer le graphe d’artefacts associé à l’artefact racine. Utilisez la commande [az acr repository delete][az-acr-repository-delete] pour supprimer la signature, la nomenclature logicielle et la signature de celle-ci.

```bash
az acr repository delete \
  -n $ACR_NAME \
  -t ${REPO}:$TAG -y
```

### <a name="view-the-remaining-manifests"></a>Affichage des manifestes restants

```azurecli
az acr repository show-manifests \
  -n $ACR_NAME \
  --repository $REPO \
  --detail -o jsonc
```

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur [l’interface CLI ORAS](https://oras.land)
* En savoir plus sur [ORAS Artifacts][oras-artifacts], et notamment comment envoyer (push), découvrir, tirer (pull) et copier un graphe d’artefacts de chaîne d’approvisionnement

<!-- LINKS - external -->
[docker-linux]:         https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]:           https://docs.docker.com/docker-for-mac/
[docker-windows]:       https://docs.docker.com/docker-for-windows/
[oras-install-docs]:    https://oras.land/cli/
[oras-preview-install]: https://github.com/oras-project/oras/releases/tag/v0.2.1-alpha.1
[oras-push-docs]:       https://oras.land/cli/1_pushing/
[oras-artifacts]:       https://github.com/oras-project/artifacts-spec/
<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az_acr_repository_show
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
