---
title: Exécuter une application avec Fortanix Confidential Computing Manager
description: Apprenez à utiliser Fortanix Confidential Computing Manager pour convertir vos images conteneurs.
services: virtual-machines
author: stempesta
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: how-to
ms.date: 03/24/2021
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: fcf3e3a1aab9114ceccc9eb32f96347ce3ed0fc2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306413"
---
# <a name="run-an-application-by-using-fortanix-confidential-computing-manager"></a>Exécuter une application en utilisant Fortanix Confidential Computing Manager

Apprenez à exécuter votre application dans l’informatique confidentielle Azure en utilisant [Fortanix Confidential Computing Manager](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.em_managed?tab=Overview) et [Node Agent](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.rte_node_agent) de [Fortanix](https://www.fortanix.com/).


Fortanix est un fournisseur de logiciels tiers qui offre des produits et des services qui fonctionnent avec l’infrastructure Azure. D’autres fournisseurs tiers proposent des services d’informatique confidentielle similaires pour Azure.

> [!Note]
> Certains des produits référencés dans ce document ne sont pas fournis par Microsoft. Microsoft ne fournit ces informations que par souci de commodité. Les références à ces produits non Microsoft ne signifient pas que Microsoft les approuve.

Ce tutoriel explique comment convertir l’image de votre application en image protégée par l’informatique confidentielle. L’environnement utilise des logiciels [​​Fortanix](https://www.fortanix.com/), avec des machines virtuelles Intel compatibles SGX de la série DCsv2 d’Azure. La solution orchestre les stratégies de sécurité critiques telles que la vérification d’identité et le contrôle d’accès aux données.

Pour un support Fortanix, rejoignez la [communauté Fortanix Slack](https://fortanix.com/community/). Utilisez le canal `#enclavemanager`.

## <a name="prerequisites"></a>Prérequis

- Si vous ne possédez pas de compte Fortanix Confidential Computing Manager, [inscrivez-vous](https://ccm.fortanix.com/auth/sign-up) avant de commencer.
- Vous avez besoin d’un registre [Docker](https://docs.docker.com/) privé pour envoyer (push) les images des applications converties.
- Si vous n’avez pas d’abonnement Azure, [créez un compte](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) avant de commencer.

> [!NOTE]
> Les comptes associés à un essai gratuit n’ont pas accès aux machines virtuelles utilisées dans ce tutoriel. Pour suivre ce tutoriel, vous avez besoin d’un abonnement avec paiement à l’utilisation.

## <a name="add-an-application-to-fortanix-confidential-computing-manager"></a>Ajouter une application à Fortanix Confidential Computing Manager

1. Connectez-vous à [Fortanix Confidential Computing Manager (Fortanix CCM)](https://ccm.fortanix.com).
1. Accédez à la page **Accounts** (Comptes) et sélectionnez **ADD ACCOUNT** (Ajouter un compte) pour créer un compte.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/create-account-new.png" alt-text="Capture d’écran montrant comment créer un compte.":::

1. Une fois votre compte créé, cliquez sur **SELECT ACCOUNT** (Sélectionner le compte) pour sélectionner le nouveau compte. Vous pouvez maintenant commencer à inscrire des nœuds de calcul et à créer des applications.
1. Dans l’onglet **Applications**, sélectionnez **+ APPLICATION** pour ajouter une application. Dans cet exemple, nous allons ajouter une application Enclave OS qui exécute un serveur Python Flask.

1. Sélectionnez le bouton **ADD** (Ajouter) pour l’**application Enclave OS** :

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/add-enclave-application.png" alt-text="Capture d'écran montrant comment ajouter une application.":::

   > [!NOTE]
   > Ce tutoriel couvre uniquement l’ajout d’applications Enclave OS. Pour plus d’informations sur l’ajout d’applications EDP Rust, consultez [Bringing EDP Rust Apps to Confidential Computing Manager](https://support.fortanix.com/hc/en-us/articles/360044746932-Bringing-EDP-Rust-Apps-to-Confidential-Computing-Manager) (Apporter des applications EDP Rust à Confidential Computing Manager).

1. Dans ce tutoriel, nous allons utiliser le registre Docker de Fortanix pour l’exemple d’application. Entrez les valeurs spécifiées pour les paramètres suivants. Utilisez votre registre Docker privé pour stocker l’image de sortie.

    - **Nom de l'application** : Python Application Server
    - **Description** : Serveur Python Flask
    - **Nom de l'image d'entrée** : fortanix/python-flask
    - **Nom de l’image de sortie** : fortanix-private/python-flask-sgx (Remplacez par votre propre registre.)
    - **ISVPRODID** : 1
    - **ISVSVM** : 1
    - **Taille de la mémoire** : 1 Go
    - **Nombre de Threads** : 128

      *Facultatif* : exécutez l'application non convertie.
    - **Docker Hub** : [https://hub.docker.com/u/fortanix](https://hub.docker.com/u/fortanix)
    - **Application** : fortanix/python-flask

      Exécutez la commande suivante :

      ```bash
         sudo docker run fortanix/python-flask
      ```
      > [!NOTE]
      > Nous vous déconseillons d’utiliser votre registre Docker privé pour stocker l’image de sortie.

1. Ajoutez un certificat. Entrez les valeurs suivantes et sélectionnez **NEXT** (Suivant) :
    - **Domaine** : myapp.domain.com
    - **Type** : Certificat émis par Confidential Computing Manager
    - **Chemin de la clé** : /run/key.pem
    - **Type de clé** : RSA
    - **Chemin du certificat** : /run/cert.pem
    - **Taille de clé RSA** : 2048 bits

## <a name="create-an-image"></a>Créer une image

Une image Fortanix CCM est une version d’un logiciel ou d’une application. Chaque image est associée à un code de hachage d'enclave (MRENCLAVE).

1. Sur la page **Add image** (Ajouter une image), entrez les informations d’identification du registre pour **Output image name** (Nom de l’image de sortie). Ces informations d’identification sont utilisées pour accéder au registre Docker privé dans lequel l’image sera envoyée (push). Comme l’image d’entrée est stockée dans un registre public, vous n’avez pas besoin de fournir d’informations d’identification pour celle-ci.
1. Entrez l’étiquette de l’image et sélectionnez **CREATE** (Créer) :

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/create-image.png" alt-text="Capture d’écran montrant comment créer une image.":::


## <a name="domain-and-image-allowlist"></a>Liste d’autorisation de domaines et d'images

Une application dont le domaine est ajouté à la liste d'autorisation reçoit un certificat TLS de Fortanix Confidential Computing Manager. Lorsqu'une application Enclave OS démarre, elle contactera Fortanix Confidential Computing Manager pour recevoir ce certificat TLS.

Dans l’onglet **Tasks** (Tâches) sur le côté gauche de l’écran, approuvez les demandes en attente pour autoriser le domaine et l’image.

## <a name="enroll-the-compute-node-agent-in-azure"></a>Inscrire l’agent du nœud de calcul dans Azure

### <a name="create-and-copy-a-join-token"></a>Créer et copier un jeton de jointure

Vous allez maintenant créer un jeton dans Fortanix Confidential Computing Manager. Ce jeton permettra à un nœud de calcul Azure de s'authentifier. Votre machine virtuelle Azure aura besoin de ce jeton.

1. Dans l’onglet **Compute Nodes** (Nœuds de calcul), sélectionnez **ENROLL NODE** (Inscrire un nœud).
1. Sélectionnez le bouton **COPY** (Copier) pour copier le jeton de jointure. Le nœud de calcul utilise ce jeton de jointure pour s’authentifier.

### <a name="enroll-nodes-into-fortanix-node-agent"></a>Inscrire des nœuds dans Fortanix Node Agent

La création d’un agent nœud Fortanix déploiera une machine virtuelle, une interface réseau, un réseau virtuel, un groupe de sécurité réseau et une IP publique dans votre groupe de ressources Azure. Votre abonnement Azure sera facturé à l'heure pour la machine virtuelle. Avant de créer un agent de nœud Fortanix, consultez la [page de tarification des machines virtuelles](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) Azure de la série DCsv2. Supprimez toutes les ressources Azure que vous n’utilisez pas.

1. Accédez à la [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/) et connectez-vous à l'aide de vos informations d'identification Azure.
1. Dans la barre de recherche, entrez **Fortanix Confidential Computing Node Agent**. Dans les résultats de la recherche, sélectionnez **Fortanix Confidential Computing Node Agent** pour accéder à la [page d’accueil de l’application](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.rte_node_agent?tab=OverviewFortanix) :

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/search-fortanix-marketplace.png" alt-text="Capture d’écran montrant comment accéder à la page d’accueil de l’application.":::
1. Sélectionnez **Obtenir maintenant**, fournissez vos informations si nécessaire, puis sélectionnez **Continuer**. Vous serez redirigé vers le Portail Azure.
1. Sélectionnez **Créer** pour accéder à la page de déploiement de Fortanix Confidential Computing Node Agent.
1. Sur cette page, vous allez entrer les informations nécessaires au déploiement d’une machine virtuelle. Il s’agit d’une machine virtuelle Intel compatible SGX de la série DCsv2 d’Azure sur laquelle le logiciel Fortanix Node Agent est installé. L’agent de nœud permettra à votre image convertie de s’exécuter avec une sécurité accrue sur les nœuds Intel SGX dans Azure. Sélectionnez l'abonnement et le groupe de ressources dans lesquels vous souhaitez déployer la machine virtuelle et les ressources associées.

   > [!NOTE]
   > Des contraintes s’appliquent lorsque vous déployez des machines virtuelles de la série DCsv2 dans Azure. Vous devrez peut-être demander un quota pour bénéficier de cœurs supplémentaires. Pour plus d'informations, consultez [Solutions informatiques confidentielles sur les machines virtuelles Azure](./virtual-machine-solutions-sgx.md).

1. Sélectionnez une région disponible.
1. Dans la zone **Nom du nœud**, entrez un nom pour votre machine virtuelle.
1. Entrez un nom d’utilisateur et un mot de passe (ou une clé SSH) pour vous authentifier sur la machine virtuelle.
1. Gardez la **taille de disque du système d’exploitation** par défaut de **200**. Sélectionnez une **taille de machine virtuelle**. (**Standard_DC4s_v2** fonctionnera pour ce tutoriel.)
1. Dans la zone **Jeton de jointure**, collez le jeton que vous avez créé précédemment dans ce tutoriel :

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/deploy-fortanix-node-agent-protocol.png" alt-text="Capture d’écran montrant comment déployer une ressource.":::

1. Sélectionnez **Revoir + créer**. Assurez-vous que la validation est réussie, puis sélectionnez **Créer**. Quand toutes les ressources sont déployées, le nœud de calcul est inscrit dans Fortanix Confidential Computing Manager.

## <a name="run-the-application-image-on-the-compute-node"></a>Exécuter l'image de l'application sur le nœud de calcul

Exécutez l’application à l’aide de la commande suivante. Veillez à remplacer l’adresse IP du nœud, le port et le nom de l’image convertie par les valeurs de votre application.

Pour ce tutoriel, voici la commande à exécuter :

```bash
    sudo docker run \
        --device /dev/isgx:/dev/isgx \
        --device /dev/gsgx:/dev/gsgx \
        -v /var/run/aesmd/aesm.socket:/var/run/aesmd/aesm.socket \
        -e NODE_AGENT_BASE_URL=http://52.152.206.164:9092/v1/ fortanix-private/python-flask-sgx
```

Dans cette commande :

- `52.152.206.164` est l’IP hôte de l’agent de nœud.
- `9092` est le port d’écoute par défaut de l’agent de nœud.
- `fortanix-private/python-flask-sgx` est l’application convertie. Vous pouvez la trouver sur le portail web de Fortanix Confidential Computing Manager. Elle se trouve dans l’onglet **Images**, dans la colonne **Image Name** (Nom de l’image) de la table **Images**.

## <a name="verify-and-monitor-the-running-application"></a>Vérifier et surveiller l'application en cours d'exécution

1. Revenez sur [Fortanix Confidential Computing Manager](https://ccm.fortanix.com/console).
1. Assurez-vous que vous travaillez dans le **compte** où vous avez inscrit le nœud.
1. Dans l’onglet **Applications**, vérifiez qu’une application est en cours d’exécution avec un nœud de calcul associé.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’en avez plus besoin, vous pouvez supprimer le groupe de ressources, la machine virtuelle et les ressources associées. La suppression du groupe de ressources annulera l'inscription des nœuds associés à votre image convertie.

Sélectionnez le groupe de ressources de la machine virtuelle, puis sélectionnez **Supprimer**. Confirmez le nom du groupe de ressources pour terminer la suppression des ressources.

Pour supprimer le compte Fortanix Confidential Computing Manager que vous avez créé, accédez à la page [Accounts ](https://ccm.fortanix.com/accounts) (Comptes) dans Fortanix Confidential Computing Manager. Pointez sur le compte que vous voulez supprimer. Sélectionnez les points noirs verticaux dans le coin supérieur droit, puis sélectionnez **DELETE ACCOUNT** (Supprimer le compte).

:::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/delete-account.png" alt-text="Capture d’écran montrant comment supprimer un compte.":::

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez utilisé les outils Fortanix pour convertir l’image de votre application afin qu’elle s’exécute sur une machine virtuelle de l’informatique confidentielle. Pour plus d’informations sur les machines virtuelles d’informatique confidentielle sur Azure, consultez [Solutions sur les machines virtuelles](virtual-machine-solutions-sgx.md).

Pour en savoir plus sur les offres d’Azure en matière d’informatique confidentielle, consultez [Vue d’ensemble de l’informatique confidentielle Azure](overview.md).

Vous pouvez également apprendre à effectuer des tâches similaires en utilisant d’autres offres tierces disponibles sur Azure, comme [Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) et [Scone](https://sconedocs.github.io).
