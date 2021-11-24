---
title: Créer et utiliser des points de terminaison privés pour Sauvegarde Azure
description: Apprenez à créer des points de terminaison privés pour le service Sauvegarde Azure où l’utilisation des points de terminaison privés contribue à maintenir la sécurité de vos ressources.
ms.topic: conceptual
ms.date: 11/09/2021
ms.custom: devx-track-azurepowershell
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: e474ae10e1461e834312e4ff52b36ece57bcc30e
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137903"
---
# <a name="create-and-use-private-endpoints-for-azure-backup"></a>Créer et utiliser des points de terminaison privés pour Sauvegarde Azure

Cet article traite du processus de création de [points de terminaison privés pour le service Sauvegarde Azure](private-endpoints-overview.md) et les scénarios dans lesquels les points de terminaison privés contribue à maintenir la sécurité de vos ressources.

## <a name="before-you-start"></a>Avant de commencer

Veillez à lire les [conditions préalables](private-endpoints-overview.md#before-you-start) et les [scénarios pris en charge](private-endpoints-overview.md#recommended-and-supported-scenarios) avant de continuer à créer des points de terminaison privés.

Ces détails vous aident à comprendre les limitations et les conditions qui doivent être remplies avant de créer des points de terminaison privés pour vos coffres.

## <a name="get-started-with-creating-private-endpoints-for-backup"></a>Prise en main de la création de points de terminaison privés pour Sauvegarde Azure

Les sections suivantes traitent des étapes de création et d’utilisation des points de terminaison privés pour Sauvegarde Azure au sein de vos réseaux virtuels.

>[!IMPORTANT]
> Nous vous recommandons vivement de suivre l’ordre des étapes établi dans ce document. Si vous ne le faites pas, le coffre rendu risque d’être incompatible avec l’utilisation des points de terminaison privés. Cela vous obligera à recommencer la procédure avec un nouveau coffre.

## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services

Les points de terminaison privés pour Sauvegarde Azure ne peuvent être créés que pour les coffres Recovery Services qui n’ont pas d’éléments protégés (ou qui n’ont pas été tentés d’être protégés ou inscrits auparavant). Nous vous suggérons donc de créer un coffre pour commencer. Pour plus d’informations sur la création d’un coffre, consultez [Créer et configurer un coffre Recovery Services](backup-create-rs-vault.md).

Consultez [cette section](#create-a-recovery-services-vault-using-the-azure-resource-manager-client) pour savoir comment créer un coffre à l’aide du client Azure Resource Manager. Cela crée un coffre dont l’identité managée est déjà activée.

## <a name="enable-managed-identity-for-your-vault"></a>Activer une identité managée sur votre machine virtuelle

Les identités managées permettent au coffre de créer et d’utiliser des points de terminaison privés. Cette section traite de l’activation de l’identité managée pour votre coffre.

1. Accédez à votre coffre Recovery Services, puis au paramètre **Identity** (Identité).

    ![Capture d’écran montrant l’option d’activation de l’état de l’identité](./media/private-endpoints/identity-status-on.png)

1. Cliquez sur le bouton bascule sous **État** pour le définir sur la valeur **Activé**, puis sélectionnez **Enregistrer**.

1. Un **ID d’objet** est généré. Il correspond à l’identité managée du coffre.

    >[!NOTE]
    >Une fois activée, l’identité managée ne doit **pas** être désactivée (même temporairement). La désactivation de l’identité managée peut entraîner un comportement incohérent.

## <a name="grant-permissions-to-the-vault-to-create-required-private-endpoints"></a>Accorder des autorisations au coffre pour créer des points de terminaison privés requis

Pour créer les points de terminaison privés requis pour le service Sauvegarde Azure, le coffre (ou plus exactement, l’identité managée du coffre) doit disposer d’autorisations sur les groupes de ressources suivants :

- Le groupe de ressources qui contient le réseau virtuel (VNet) cible.
- Le groupe de ressources dans lequel les points de terminaison privés doivent être créés.
- Le groupe de ressources qui contient Private DNS Zones, comme discuté en détail [ici](#create-private-endpoints-for-azure-backup)

Nous vous recommandons d’accorder le rôle **Contributor** (Contributeur) à ces trois groupes de ressources pour le coffre (identité managée). Les étapes suivantes expliquent comment effectuer cette opération pour un groupe de ressources particulier (cette opération doit être effectuée pour chacun des trois groupes de ressources) :

1. Accédez au groupe de ressources puis, dans la barre de gauche, accédez à **Access Control (IAM)** (Contrôle d'accès (Gestion des identités et des accès)).
1. Une fois dans **Access Control** (Contrôle d'accès), accédez à **Add a role assignment** (Ajouter une attribution de rôle).

    ![Ajouter une attribution de rôle](./media/private-endpoints/add-role-assignment.png)

1. Dans le volet **Add role assignment** (Ajouter une attribution de rôle), sélectionnez **Contributor** (Contributeur) en tant que **Role** (Rôle), puis utilisez le **Name** (Nom) du coffre en tant que **Principal**. Lorsque vous avez terminé, sélectionnez votre coffre, puis **Enregistrer**.

    ![Choisir un rôle et un principal](./media/private-endpoints/choose-role-and-principal.png)

Pour gérer les autorisations plus précisément, consultez la section [Créer manuellement des rôles et des autorisations](#create-roles-and-permissions-manually).

## <a name="create-private-endpoints-for-azure-backup"></a>Créer des points de terminaison privés pour Sauvegarde Azure

Cette section explique comment créer un point de terminaison privé pour votre coffre.

1. Accédez à votre coffre créé ci-dessus et rendez-vous dans **Connexions de point de terminaison privé** dans la barre de navigation de gauche. Sélectionnez **+Point de terminaison privé** en haut pour commencer à créer un nouveau point de terminaison privé pour ce coffre.

    ![Créer un point de terminaison privé](./media/private-endpoints/new-private-endpoint.png)

1. Une fois dans le processus **Create Private Endpoint** (Créer un point de terminaison privé), vous devez entrer des informations requises pour créer la connexion de votre point de terminaison privé.
  
    1. **Paramètres de base**: Entrez les informations de base de vos points de terminaison privés. La région doit être la même que celle du coffre et de la ressource sauvegardés.

        ![Capture d’écran montrant les champs d’informations de base à remplir](./media/private-endpoints/basics-tab.png)

    1. **Ressource** : Dans cet onglet, vous devez sélectionner la ressource PaaS pour laquelle vous souhaitez créer votre connexion. Sélectionnez **Microsoft.RecoveryServices/vaults** à partir du type de ressource pour l’abonnement souhaité. Lorsque vous avez terminé, choisissez le nom de votre coffre Recovery Services dans la section **Resource** (Ressource) et **AzureBackup** dans la section **Target sub-resource** (Sous-ressource cible).

        ![Sélectionner la ressource pour votre connexion](./media/private-endpoints/resource-tab.png)

    1. **Configuration** : Dans Configuration, spécifiez le réseau virtuel et le sous-réseau où vous souhaitez que le point de terminaison privé soit créé. Il s’agit du réseau virtuel sur lequel se trouve la machine virtuelle.

        Pour vous connecter de manière privée, vous devez disposer des enregistrements DNS requis. En fonction de la configuration de votre réseau, vous pouvez choisir l’une des options suivantes :

          - Intégrer votre point de terminaison privé à une zone DNS privée : sélectionnez **Oui** si vous souhaitez l’intégrer.
          - Utiliser votre serveur DNS personnalisé : sélectionnez **Non** si vous souhaitez utiliser votre propre serveur DNS.

        La gestion des enregistrements DNS pour ces deux options est [décrite plus loin](#manage-dns-records) dans cet article.

          ![Spécifier le réseau virtuel et le sous-réseau](./media/private-endpoints/configuration-tab.png)

    1. Si vous le souhaitez, vous pouvez ajouter des **balises** à votre point de terminaison privé.
    1. Passez à **Vérifier + créer** lorsque vous avez terminé la saisie des informations requises. Une fois la validation terminée, sélectionnez **Créer** pour créer le point de terminaison privé.

## <a name="approve-private-endpoints"></a>Approuver des points de terminaison privés

Si l’utilisateur qui crée le point de terminaison privé est également le propriétaire du coffre Recovery Services, le point de terminaison privé créé précédemment est approuvé automatiquement. Dans le cas contraire, le propriétaire du coffre doit approuver le point de terminaison privé avant de pouvoir l’utiliser. Cette section présente l’approbation manuelle des points de terminaison privés via le portail Azure.

Si vous souhaitez utiliser le client Azure Resource Manager pour approuver vos points de terminaison privés, consultez la section [Approbation manuelle des points de terminaison privés à l’aide du client Azure Resource Manager](#manual-approval-of-private-endpoints-using-the-azure-resource-manager-client) client Azure Resource Manager pour utiliser le client Azure Resource Manager pour l’approbation des points de terminaison privés.

1. Accédez à votre coffre Recovery Services. Dans la barre de gauche, accédez à **Private endpoint connections** (Connexions de point de terminaison privé).
1. Sélectionnez la connexion de point de terminaison privé à approuver
1. Dans la barre supérieure, sélectionnez **Approve** (Approuver). Vous pouvez également sélectionner **Reject** (Rejeter) ou **Remove** (Supprimer) si vous souhaitez rejeter ou supprimer la connexion au point de terminaison.

    ![Capture d’écran montrant la procédure d’approbation d’un point de terminaison privé](./media/private-endpoints/approve-private-endpoints.png)

## <a name="manage-dns-records"></a>Gestion des enregistrements DNS

Comme décrit précédemment, vous avez besoin des enregistrements DNS requis dans vos zones ou serveurs DNS privés afin de vous connecter de manière privée. Vous pouvez intégrer votre point de terminaison privé directement aux zones DNS privées Azure ou utiliser vos serveurs DNS personnalisés pour y parvenir, en fonction de vos préférences réseau. Cela devra être fait pour les trois services : Sauvegarde, Blobs et Files d’attente.

En outre, si votre serveur ou zone DNS est présent dans un abonnement différent de celui contenant le point de terminaison privé, consultez également [Créer des entrées DNS lorsque le serveur DNS/la zone DNS est présent(e) dans un autre abonnement](#create-dns-entries-when-the-dns-serverdns-zone-is-present-in-another-subscription). 

### <a name="when-integrating-private-endpoints-with-azure-private-dns-zones"></a>Lors de l’intégration de points de terminaison privés à des zones DNS privées Azure

Si vous choisissez d’intégrer votre point de terminaison privé à des zones DNS privées, Sauvegarde Azure ajoutera les enregistrements DNS requis. Vous pouvez afficher les zones DNS privées utilisées sous la rubrique **Configuration DNS** du point de terminaison privé. Si ces zones DNS ne sont pas présentes, elles sont créées automatiquement lors de la création du point de terminaison privé. Toutefois, vous devez vérifier que votre réseau virtuel (qui contient les ressources à sauvegarder) est correctement lié aux trois zones DNS privées, comme décrit ci-dessous.

![Configuration DNS dans la zone DNS privée Azure](./media/private-endpoints/dns-configuration.png)

>[!Note]
>Si vous utilisez des serveurs proxy, vous pouvez choisir d’ignorer le serveur proxy ou d’effectuer vos sauvegardes par le biais du serveur proxy. Pour contourner un serveur proxy, lisez les sections suivantes. Pour utiliser le serveur proxy dans le cadre de vos sauvegardes, consultez les [détails de la configuration d’un serveur proxy pour un coffre Recovery Services](#set-up-proxy-server-for-recovery-services-vault-with-private-endpoint).
#### <a name="validate-virtual-network-links-in-private-dns-zones"></a>Valider les liaisons de réseau virtuel dans les zones DNS privées

Pour **chaque zone DNS privée** indiquée ci-dessus (pour Sauvegarde, Blobs et Files d’attente), procédez comme suit :

1. Accédez à l’option **Liens de réseau virtuel** respective dans la barre de navigation de gauche.
1. Vous devriez pouvoir voir une entrée pour le réseau virtuel pour lequel vous avez créé le point de terminaison privé, comme celui illustré ci-dessous :

    ![Réseau virtuel pour le point de terminaison privé](./media/private-endpoints/virtual-network-links.png)

1. Si vous ne voyez pas d’entrée, ajoutez un lien de réseau virtuel à toutes les zones DNS qui n’en ont pas.

    ![Ajouter un lien de réseau virtuel](./media/private-endpoints/add-virtual-network-link.png)

### <a name="when-using-custom-dns-server-or-host-files"></a>Lors de l’utilisation d’un serveur DNS personnalisé ou de fichiers d’hôtes

Si vous utilisez vos serveurs DNS personnalisés, vous devez créer les zones DNS nécessaires et ajouter les enregistrements DNS requis par les points de terminaison privés à vos serveurs DNS. Pour les blobs et les files d’attente, vous pouvez également utiliser des redirecteurs conditionnels.

#### <a name="for-the-backup-service"></a>Pour le service Sauvegarde Azure

1. Sur votre serveur DNS, créez une zone DNS pour Sauvegarde Azure en respectant la convention d’affectation de noms suivante :

    |Zone |Service |
    |---------|---------|
    |`privatelink.<geo>.backup.windowsazure.com`   |  Sauvegarde        |

    >[!NOTE]
    > Dans le texte ci-dessus, `<geo>` fait référence au code de région (par exemple, *eus* et *ne* pour les régions USA Est et Europe Nord, respectivement). Consultez les listes suivantes pour connaître les codes de régions :
    >
    > - [Tous les clouds publics](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx)
    > - [Chine](/azure/china/resources-developer-guide#check-endpoints-in-azure)
    > - [Allemagne](../germany/germany-developer-guide.md#endpoint-mapping)
    > - [Gouvernement des États-Unis](../azure-government/documentation-government-developer-guide.md)
    > - [Liste de codes géographiques - Exemple de fichier XML](scripts/geo-code-list.md)

1. Ensuite, nous devons ajouter les enregistrements DNS requis. Pour afficher les enregistrements qui doivent être ajoutés à la zone DNS de Sauvegarde Azure, accédez au point de terminaison privé que vous avez créé ci-dessus, puis accédez à l’option **Configuration DNS** sous la barre de navigation de gauche.

    ![Configuration DNS pour le serveur DNS personnalisé](./media/private-endpoints/custom-dns-configuration.png)

1. Ajoutez une entrée pour chaque nom de domaine complet et adresse IP affichés sous la forme d’enregistrements de type A dans votre zone DNS pour Sauvegarde Azure. Si vous utilisez un fichier d’hôte pour la résolution de noms, effectuez les entrées correspondantes dans le fichier d’hôte pour chaque adresse IP et nom de domaine complet selon le format suivant :

    `<private ip><space><backup service privatelink FQDN>`

>[!NOTE]
>Comme indiqué dans la capture d’écran ci-dessus, les noms de domaine complets indiquent `xxxxxxxx.<geo>.backup.windowsazure.com` et non `xxxxxxxx.privatelink.<geo>.backup. windowsazure.com`. Dans ce cas, veillez à inclure (et, le cas échéant, à ajouter) le `.privatelink.` conformément au format indiqué.

#### <a name="for-blob-and-queue-services"></a>Pour les services de Blob et de File d’attente

Pour les blobs et les files d’attente, vous pouvez utiliser des redirecteurs conditionnels ou créer des zones DNS sur votre serveur DNS.

##### <a name="if-using-conditional-forwarders"></a>En cas d’utilisation de redirecteurs conditionnels

Si vous utilisez des redirecteurs conditionnels, ajoutez des redirecteurs pour les noms de domaine complets des blobs et des files d’attente comme suit :

|FQDN  |IP  |
|---------|---------|
|`privatelink.blob.core.windows.net`     |  168.63.129.16       |
|`privatelink.queue.core.windows.net`     | 168.63.129.16        |

##### <a name="if-using-private-dns-zones"></a>En cas d’utilisation de zones DNS privées

Si vous utilisez des zones DNS pour les blobs et les files d’attente, vous devez d’abord créer ces zones DNS et ajouter ultérieurement les enregistrements A requis.

|Zone |Service  |
|---------|---------|
|`privatelink.blob.core.windows.net`     |  Objet blob     |
|`privatelink.queue.core.windows.net`     | File d'attente        |

Pour l’instant, nous ne créerons les zones pour les blobs et les files d’attente que lorsque nous utiliserons des serveurs DNS personnalisés. L’ajout d’enregistrements DNS sera effectué plus tard en deux étapes :

1. Lorsque vous inscrivez la première instance de sauvegarde, c’est-à-dire lorsque vous configurez la sauvegarde pour la première fois.
1. Lorsque vous exécutez la première sauvegarde.

Nous effectuerons ces étapes dans les sections suivantes.

## <a name="use-private-endpoints-for-backup"></a>Utiliser des points de terminaison privés pour Sauvegarde Azure

Après l’approbation des points de terminaison privés créés pour le coffre de votre réseau virtuel, vous pouvez commencer à les utiliser pour effectuer vos sauvegardes et restaurations.

>[!IMPORTANT]
>Avant de continuer, vérifiez que vous avez suivi toutes les étapes précédentes de ce document. Voici une liste des de ces étapes :
>
>1. Créer un (nouveau) coffre Recovery Services
>2. Activer le coffre pour utiliser l’identité managée affectée par le système
>3. Attribuer les autorisations appropriées à l’identité managée du coffre
>4. Créer un point de terminaison privé pour votre coffre
>5. Approuver le point de terminaison privé (s’il n’a pas été approuvé automatiquement)
>6. Vérifier que tous les enregistrements DNS sont correctement ajoutés (à l’exception des enregistrements de blob et de file d’attente pour les serveurs personnalisés, qui seront abordés dans les sections suivantes)

### <a name="check-vm-connectivity"></a>Vérifier la connectivité des machines virtuelles

Dans la machine virtuelle du réseau verrouillé, vérifiez les points suivants :

1. La machine virtuelle doit avoir accès à AAD.
2. Exécutez **nslookup** sur l’URL de sauvegarde (`xxxxxxxx.privatelink.<geo>.backup. windowsazure.com`) à partir de votre machine virtuelle pour garantir la connectivité. Cette opération doit renvoyer l’adresse IP privée attribuée dans votre réseau virtuel.

### <a name="configure-backup"></a>Configurer une sauvegarde

Une fois que vous vous êtes assuré que la liste de vérification ci-dessus et l’accès ont été correctement effectués, vous pouvez continuer à configurer la sauvegarde des charges de travail dans le coffre. Si vous utilisez un serveur DNS personnalisé, vous devez ajouter des entrées DNS pour les blobs et les files d’attente disponibles après la configuration de la première sauvegarde.

#### <a name="dns-records-for-blobs-and-queues-only-for-custom-dns-servershost-files-after-the-first-registration"></a>Enregistrements DNS pour les blobs et les files d’attente (uniquement pour les fichiers d’hôtes/serveurs DNS personnalisés) après la première inscription

Une fois que vous avez configuré la sauvegarde pour au moins une ressource d’un coffre pour lequel un point de terminaison privé est activé, ajoutez les enregistrements DNS requis pour les blobs et les files d’attente, comme décrit ci-dessous.

1. Accédez à votre groupe de ressources, puis recherchez le point de terminaison privé que vous avez créé.
1. Hormis le nom du point de terminaison privé que vous avez donné, vous verrez deux autres points de terminaison privés en cours de création. Ces derniers commencent par `<the name of the private endpoint>_ecs` et sont suivis des suffixes `_blob` et `_queue` respectivement.

    ![Ressources de point de terminaison privé](./media/private-endpoints/private-endpoint-resources.png)

1. Accédez à chacun de ces points de terminaison privés. Dans l’option Configuration DNS pour chacun des deux points de terminaison privés, vous verrez un enregistrement avec un nom de domaine complet et une adresse IP. Ajoutez ces deux éléments à votre serveur DNS personnalisé, en plus de ceux décrits précédemment.
Si vous utilisez un fichier d’hôte, effectuez les entrées correspondantes dans le fichier d’hôte pour chaque adresse IP/nom de domaine complet selon le format suivant :

    `<private ip><space><blob service privatelink FQDN>`<br>
    `<private ip><space><queue service privatelink FQDN>`

    ![Configuration DNS de blobs](./media/private-endpoints/blob-dns-configuration.png)

Outre les éléments ci-dessus, une autre entrée est nécessaire après la première sauvegarde, dont il est question [plus loin](#dns-records-for-blobs-only-for-custom-dns-servershost-files-after-the-first-backup).

### <a name="backup-and-restore-of-workloads-in-azure-vm-sql-and-sap-hana"></a>Sauvegarde et restauration de charges de travail dans une machine virtuelle Azure (SQL et SAP HANA)

Une fois le point de terminaison privé créé et approuvé, aucune autre modification n’est requise côté client pour utiliser le point de terminaison privé (à moins que vous n’utilisiez des groupes de disponibilité SQL, cas que nous aborderons plus loin dans cette section). Toutes les communications et tous les transferts de données de votre réseau sécurisé vers le coffre sont effectués via le point de terminaison privé. Toutefois, si vous supprimez des points de terminaison privés associés au coffre après l’inscription d’un serveur (SQL ou SAP HANA), vous devez réinscrire le conteneur auprès du coffre. Vous n’avez pas besoin d’arrêter leur protection.

#### <a name="dns-records-for-blobs-only-for-custom-dns-servershost-files-after-the-first-backup"></a>Enregistrements DNS pour les blobs (uniquement pour les fichiers d’hôtes/serveurs DNS personnalisés) après la première sauvegarde

Une fois que vous avez exécuté la première sauvegarde et que vous utilisez un serveur DNS personnalisé (sans transfert conditionnel), il est probable que votre sauvegarde échoue. Si cela se produit :

1. Accédez à votre groupe de ressources, puis recherchez le point de terminaison privé que vous avez créé.
1. En plus des trois points de terminaison privés abordés précédemment, vous allez maintenant voir un quatrième point de terminaison privé dont le nom commence par `<the name of the private endpoint>_prot` et est suivi du suffixe `_blob`.

    ![Point de terminaison privé avec le suffixe « prot »](./media/private-endpoints/private-endpoint-prot.png)

1. Accédez à ce nouveau point de terminaison privé. Dans l’option Configuration DNS, vous verrez un enregistrement avec un nom de domaine complet et une adresse IP. Ajoutez-les à votre serveur DNS privé, en plus de ceux décrits précédemment.

    Si vous utilisez un fichier d’hôte, effectuez les entrées correspondantes dans le fichier d’hôte pour chaque adresse IP et nom de domaine complet selon le format suivant :

    `<private ip><space><blob service privatelink FQDN>`

>[!NOTE]
>À ce stade, vous devriez être en mesure d’exécuter **nslookup** à partir de la machine virtuelle et de résoudre les adresses IP privées sur les URL de sauvegarde et de stockage du coffre.

### <a name="when-using-sql-availability-groups"></a>Lors de l’utilisation de groupes de disponibilité SQL

Lorsque vous utilisez des groupes de disponibilité (AG) SQL, vous devez configurer le transfert conditionnel dans le DNS AG personnalisé comme décrit ci-dessous :

1. Connectez-vous à votre contrôleur de domaine.
1. Sous l’application DNS, ajoutez des redirecteurs conditionnels pour les trois zones DNS (Sauvegarde, Blobs et Files d’attente) vers l’adresse IP de l’hôte 168.63.129.16 ou l’adresse IP du serveur DNS personnalisé, le cas échéant. Les captures d’écran suivantes illustrent le moment où vous effectuez un transfert vers l’adresse IP de l’hôte Azure. Si vous utilisez votre propre serveur DNS, remplacez-la par l’adresse IP de votre serveur DNS.

    ![Redirecteurs conditionnels dans Gestionnaire DNS](./media/private-endpoints/dns-manager.png)

    ![Nouveau redirecteur conditionnel](./media/private-endpoints/new-conditional-forwarder.png)

### <a name="backup-and-restore-through-mars-agent"></a>Sauvegarde et restauration par le biais de l’agent MARS

Lorsque vous utilisez l’agent MARS pour sauvegarder vos ressources locales, assurez-vous que votre réseau local (contenant vos ressources à sauvegarder) est homologué avec le réseau virtuel Azure qui contient un point de terminaison privé pour le coffre, afin de pouvoir l’utiliser. Vous pouvez ensuite continuer à installer l’agent MARS et configurer la sauvegarde comme indiqué ici. Toutefois, vous devez vous assurer que toutes les communications pour la sauvegarde s’effectuent uniquement par le biais du réseau homologué.

Cependant, si vous supprimez des points de terminaison privés pour le coffre après l’inscription d’un agent MARS, vous devez réinscrire le conteneur auprès du coffre. Vous n’avez pas besoin d’arrêter leur protection.

## <a name="deleting-private-endpoints"></a>Suppression de points de terminaison privés

Consultez [cette section](/rest/api/virtualnetwork/privateendpoints/delete) pour savoir comment supprimer des points de terminaison privés.

## <a name="additional-topics"></a>Rubriques supplémentaires

### <a name="create-a-recovery-services-vault-using-the-azure-resource-manager-client"></a>Utiliser le client Azure Resource Manager pour créer un coffre Recovery Services

Vous pouvez créer un coffre Recovery Services et activer son identité managée (l’activation de l’identité managée est requise, comme nous le verrons plus tard) à l’aide du client Azure Resource Manager. Un exemple de cette procédure est partagé ci-dessous :

```rest
armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>?api-version=2017-07-01-preview @C:\<filepath>\MSIVault.json
```

Le fichier JSON ci-dessus doit avoir le contenu suivant :

Requête JSON :

```json
{
  "location": "eastus2",
  "name": "<vaultname>",
  "etag": "W/\"datetime'2019-05-24T12%3A54%3A42.1757237Z'\"",
  "tags": {
    "PutKey": "PutValue"
  },
  "properties": {},
  "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
  "type": "Microsoft.RecoveryServices/Vaults",
  "sku": {
    "name": "RS0",
    "tier": "Standard"
  },
  "identity": {
    "type": "systemassigned"
  }
}
```

Réponse JSON :

```json
{
   "location": "eastus2",
   "name": "<vaultname>",
   "etag": "W/\"datetime'2020-02-25T05%3A26%3A58.5181122Z'\"",
   "tags": {
     "PutKey": "PutValue"
   },
   "identity": {
     "tenantId": "<tenantid>",
     "principalId": "<principalid>",
     "type": "SystemAssigned"
   },
   "properties": {
     "provisioningState": "Succeeded",
     "privateEndpointStateForBackup": "None",
     "privateEndpointStateForSiteRecovery": "None"
   },
   "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
   "type": "Microsoft.RecoveryServices/Vaults",
   "sku": {
     "name": "RS0",
     "tier": "Standard"
   }
 }
```

>[!NOTE]
>Le coffre créé dans cet exemple via le client Azure Resource Manager est déjà créé avec une identité managée affectée par le système.

### <a name="managing-permissions-on-resource-groups"></a>Gestion des autorisations dans les groupes de ressources

L’identité managée pour le coffre doit disposer des autorisations suivantes dans le groupe de ressources et le réseau virtuel où les points de terminaison privés seront créés :

- `Microsoft.Network/privateEndpoints/*` : cette autorisation est nécessaire pour exécuter CRUD sur des points de terminaison privés dans le groupe de ressources. Elle doit être affectée au niveau du groupe de ressources.
- `Microsoft.Network/virtualNetworks/subnets/join/action` : cette autorisation est nécessaire sur le réseau virtuel où l’adresse IP privée est attachée au point de terminaison privé.
- `Microsoft.Network/networkInterfaces/read` : cette autorisation est nécessaire sur le groupe de ressources pour obtenir l’interface réseau créée pour le point de terminaison privé.
- Private DNS Zone Contributor Role (Rôle Contributeur de zone DNS privée) : ce rôle existe déjà et peut être utilisé pour fournir les autorisations `Microsoft.Network/privateDnsZones/A/*` et `Microsoft.Network/privateDnsZones/virtualNetworkLinks/read`.

Vous pouvez utiliser l’une des méthodes suivantes pour créer des rôles avec les autorisations requises :

#### <a name="create-roles-and-permissions-manually"></a>Créer manuellement des rôles et des autorisations

Créez les fichiers JSON suivants et utilisez la commande PowerShell à la fin de la section pour créer des rôles :

//PrivateEndpointContributorRoleDef.json

```json
{
  "Name": "PrivateEndpointContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows management of Private Endpoint",
  "Actions": [
    "Microsoft.Network/privateEndpoints/*",
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

//NetworkInterfaceReaderRoleDef.json

```json
{
  "Name": "NetworkInterfaceReader",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows read on networkInterfaces",
  "Actions": [
    "Microsoft.Network/networkInterfaces/read"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

//PrivateEndpointSubnetContributorRoleDef.json

```json
{
  "Name": "PrivateEndpointSubnetContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows adding of Private Endpoint connection to Virtual Networks",
  "Actions": [
    "Microsoft.Network/virtualNetworks/subnets/join/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

```azurepowershell
 New-AzRoleDefinition -InputFile "PrivateEndpointContributorRoleDef.json"
 New-AzRoleDefinition -InputFile "NetworkInterfaceReaderRoleDef.json"
 New-AzRoleDefinition -InputFile "PrivateEndpointSubnetContributorRoleDef.json"
```

#### <a name="use-a-script"></a>Utiliser un script

1. Dans le portail Azure, démarrez le **Cloud Shell**, puis sélectionnez **Upload** (Charger) dans la fenêtre PowerShell.

    ![Capture d’écran montrant l’option Upload (Charger) de la fenêtre PowerShell](./media/private-endpoints/upload-file-in-powershell.png)

1. Chargez le script suivant : [VaultMsiPrereqScript](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/VaultMsiPrereqScript.ps1)

1. Accédez à votre dossier de démarrage (par exemple : `cd /home/user`)

1. Exécutez le script suivant :

    ```azurepowershell
    ./VaultMsiPrereqScript.ps1 -subscription <subscription-Id> -vaultPEResourceGroup <vaultPERG> -vaultPESubnetResourceGroup <subnetRG> -vaultMsiName <msiName>
    ```

    Les paramètres sont les suivants :

    - **subscription** : **SubscriptionId contenant le groupe de ressources dans lequel le point de terminaison privé du coffre doit être créé et le sous-réseau auquel le point de terminaison privé de l’archivage sera attaché

    - **vaultPEResourceGroup** : groupe de ressources dans lequel le point de terminaison privé de l’archivage sera créé

    - **vaultPESubnetResourceGroup** : groupe de ressources du sous-réseau auquel le point de terminaison privé sera joint

    - **vaultMsiName** : Nom du fichier MSI du coffre, qui est le même que **VaultName**

1. Terminez l’authentification et le script prendra le contexte de l’abonnement donné, fourni ci-dessus. Il créera les rôles appropriés si le locataire ne les contient pas, puis affectera des rôles au MSI du coffre.

### <a name="creating-private-endpoints-using-azure-powershell"></a>Création d’un point de terminaison privé à l’aide d’Azure PowerShell

#### <a name="auto-approved-private-endpoints"></a>Points de terminaison privés approuvés automatiquement

```azurepowershell
$vault = Get-AzRecoveryServicesVault `
        -ResourceGroupName $vaultResourceGroupName `
        -Name $vaultName
  
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name $privateEndpointConnectionName `
        -PrivateLinkServiceId $vault.ID `
        -GroupId "AzureBackup"  

$vnet = Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $VMResourceGroupName
$subnet = $vnet | Select -ExpandProperty subnets | Where-Object {$_.Name -eq '<subnetName>'}


$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $vmResourceGroupName `
        -Name $privateEndpointName `
        -Location $location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -Force
```

### <a name="manual-approval-of-private-endpoints-using-the-azure-resource-manager-client"></a>Approbation manuelle des points de terminaison privés à l’aide du client Azure Resource Manager

1. Utilisez **GetVault** pour obtenir l’ID de connexion de point de terminaison privé de votre point de terminaison privé.

    ```rest
    armclient GET /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/vaults/<vaultname>?api-version=2017-07-01-preview
    ```

    Cela retourne l’ID de connexion du point de terminaison privé. Vous pouvez récupérer le nom de la connexion à l’aide de la première partie de l’ID de connexion, comme suit :

    `privateendpointconnectionid = {peName}.{vaultId}.backup.{guid}`

1. Dans la réponse, récupérez le paramètre **Private Endpoint Connection ID** (ID de connexion de point de terminaison) (et le paramètre **Private Endpoint Name** (Nom du point de terminaison privé), lorsque cela est nécessaire), remplacez-les par le code JSON et l’URI de Azure Resource Manager suivants, puis essayez de définir le paramètre Status (État) sur « Approved/Rejected/Disconnected » (« Approuvé/Rejeté/Déconnecté », comme illustré dans l’exemple ci-dessous :

    ```rest
    armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>?api-version=2020-02-02-preview @C:\<filepath>\BackupAdminApproval.json
    ```

    JSON :

    ```json
    {
    "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>",
    "properties": {
        "privateEndpoint": {
        "id": "/subscriptions/<subscriptionid>/resourceGroups/<pergname>/providers/Microsoft.Network/privateEndpoints/pename"
        },
        "privateLinkServiceConnectionState": {
        "status": "Disconnected",  //choose state from Approved/Rejected/Disconnected
        "description": "Disconnected by <userid>"
        }
    }
    }
    ```

### <a name="set-up-proxy-server-for-recovery-services-vault-with-private-endpoint"></a>Configurer un serveur proxy pour le coffre de Recovery Services avec un point de terminaison privé

Pour configurer un serveur proxy pour une machine virtuelle Azure ou sur un ordinateur local, procédez comme suit :

1. Ajoutez les domaines suivants qui doivent être accessibles à partir du serveur proxy.
   
   | Service | Noms de domaine | Port |
   | ------- | ------ | ---- |
   | Sauvegarde Azure | *.backup.windowsazure.com | 443 |
   | Stockage Azure | *.blob.core.windows.net <br><br> *.queue.core.windows.net <br><br> *.blob.storage.azure.net | 443 |
   | Azure Active Directory <br><br> URL de domaine mises à jour mentionnées dans les sections 56 et 59 de [Microsoft 365 Common et Office Online](/microsoft-365/enterprise/urls-and-ip-address-ranges?view=o365-worldwide&preserve-view=true#microsoft-365-common-and-office-online). | *.msftidentity.com, *.msidentity.com, account.activedirectory.windowsazure.com, accounts.accesscontrol.windows.net, adminwebservice.microsoftonline.com, api.passwordreset.microsoftonline.com, autologon.microsoftazuread-sso.com, becws.microsoftonline.com, clientconfig.microsoftonline-p.net, companymanager.microsoftonline.com, device.login.microsoftonline.com, graph.microsoft.com, graph.windows.net, login.microsoft.com, login.microsoftonline.com, login.microsoftonline-p.com, login.windows.net, logincert.microsoftonline.com, loginex.microsoftonline.com, login-us.microsoftonline.com, nexus.microsoftonline-p.com, passwordreset.microsoftonline.com, provisioningapi.microsoftonline.com <br><br> 20.190.128.0/18, 40.126.0.0/18, 2603:1006:2000::/48, 2603:1007:200::/48, 2603:1016:1400::/48, 2603:1017::/48, 2603:1026:3000::/48, 2603:1027:1::/48, 2603:1036:3000::/48, 2603:1037:1::/48, 2603:1046:2000::/48, 2603:1047:1::/48, 2603:1056:2000::/48, 2603:1057:2::/48 <br><br> *.hip.live.com, *.microsoftonline.com, *.microsoftonline-p.com, *.msauth.net, *.msauthimages.net, *.msecnd.net, *.msftauth.net, *.msftauthimages.net, *.phonefactor.net, enterpriseregistration.windows.net, management.azure.com, policykeyservice.dc.ad.msft.net | Le cas échéant. |

1. Autorisez l’accès à ces domaines dans le serveur proxy et liez la zone DNS privée (`*.privatelink.<geo>.backup.windowsazure.com`, `*.privatelink.blob.core.windows.net`, `*.privatelink.queue.core.windows.net`) au réseau virtuel sur lequel le serveur proxy est créé ou utilise un serveur DNS personnalisé avec les entrées DNS respectives. <br><br> Le réseau virtuel sur lequel le serveur proxy est en cours d’exécution et le réseau virtuel dans lequel la carte réseau de point de terminaison privée est créée doivent être homologués, ce qui permet au serveur proxy de rediriger les requêtes vers l’adresse IP privée. 

   >[!NOTE]
   >Dans le texte ci-dessus, `<geo>` fait référence au code de région (par exemple, *eus* et *ne* pour les régions USA Est et Europe Nord, respectivement). Consultez les listes suivantes pour connaître les codes de régions :
   >
   >- [Tous les clouds publics](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx)
   >- [Chine](/azure/china/resources-developer-guide#check-endpoints-in-azure)
   >- [Allemagne](../germany/germany-developer-guide.md#endpoint-mapping)
   >- [Gouvernement des États-Unis](../azure-government/documentation-government-developer-guide.md)
   >- [Liste de codes géographiques - Exemple de fichier XML](scripts/geo-code-list.md)

Le diagramme suivant montre une configuration (dans le cadre de l’utilisation de zones DNS privées Azure) avec un serveur proxy dont le réseau virtuel est lié à une zone DNS privée avec les entrées DNS requises. Le serveur proxy peut également avoir son propre serveur DNS personnalisé et les domaines ci-dessus peuvent être transmis de manière conditionnelle à 168.63.129.16. Si vous utilisez un fichier d’hôte/serveur DNS personnalisé pour la résolution DNS, consultez les sections relatives à la [gestion des entrées DNS](#manage-dns-records) et à la [configuration de la protection](#configure-backup).

:::image type="content" source="./media/private-endpoints/setup-with-proxy-server-inline.png" alt-text="Diagramme montrant une configuration avec un serveur proxy." lightbox="./media/private-endpoints/setup-with-proxy-server-expanded.png":::

### <a name="create-dns-entries-when-the-dns-serverdns-zone-is-present-in-another-subscription"></a>Créer des entrées DNS lorsque le serveur DNS/la zone DNS est présent(e) dans un autre abonnement

Dans cette section, nous allons aborder les cas où vous utilisez une zone DNS présente dans un abonnement ou un groupe de ressources différent de celui contenant le point de terminaison privé pour le coffre Recovery Services, tel qu’une topologie en étoile. Étant donné que l’identité managée utilisée pour créer des points de terminaison privés (et les entrées DNS) possède des autorisations uniquement sur le groupe de ressources dans lequel les points de terminaison privés sont créés, les entrées DNS requises sont également nécessaires. Utilisez les scripts PowerShell suivants pour créer des entrées DNS.
  
>[!Note]
>Reportez-vous à l’ensemble du processus décrit ci-dessous pour obtenir les résultats requis. Le processus doit être répété deux fois au cours de la première détection (afin de créer des entrées DNS requises pour les comptes de stockage de communication), puis une fois pendant la première sauvegarde (afin de créer des entrées DNS requises pour les comptes de stockage backend).

#### <a name="step-1-get-required-dns-entries"></a>Étape 1 : obtenir les entrées DNS requises

Utilisez le script [PrivateIP.ps1](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/PrivateIP.ps1) pour répertorier toutes les entrées DNS qui doivent être créées.

>[!Note]
>Dans la syntaxe ci-dessous, `subscription` fait référence à l’abonnement dans lequel le point de terminaison privé du coffre doit être créé.

**Syntaxe d’utilisation du script**

```azurepowershell
./PrivateIP.ps1 -Subscription "<VaultPrivateEndpointSubscriptionId>" -VaultPrivateEndpointName "<vaultPrivateEndpointName>" -VaultPrivateEndpointRGName <vaultPrivateEndpointRGName> -DNSRecordListFile dnsentries.txt
```

**Exemple de sortie**

```
ResourceName                                                                 DNS                                                                       PrivateIP
<vaultId>-ab-pod01-fc1         privatelink.eus.backup.windowsazure.com         10.12.0.15
<vaultId>-ab-pod01-fab1        privatelink.eus.backup.windowsazure.com         10.12.0.16
<vaultId>-ab-pod01-prot1       privatelink.eus.backup.windowsazure.com         10.12.0.17
<vaultId>-ab-pod01-rec2        privatelink.eus.backup.windowsazure.com         10.12.0.18
<vaultId>-ab-pod01-ecs1        privatelink.eus.backup.windowsazure.com         10.12.0.19
<vaultId>-ab-pod01-id1         privatelink.eus.backup.windowsazure.com         10.12.0.20
<vaultId>-ab-pod01-tel1        privatelink.eus.backup.windowsazure.com         10.12.0.21
<vaultId>-ab-pod01-wbcm1       privatelink.eus.backup.windowsazure.com         10.12.0.22
abcdeypod01ecs114        privatelink.blob.core.windows.net       10.12.0.23
abcdeypod01ecs114        privatelink.queue.core.windows.net      10.12.0.24
abcdeypod01prot120       privatelink.blob.core.windows.net       10.12.0.28
abcdeypod01prot121       privatelink.blob.core.windows.net       10.12.0.32
abcdepod01prot110       privatelink.blob.core.windows.net       10.12.0.36
abcdeypod01prot121       privatelink.blob.core.windows.net       10.12.0.30
abcdeypod01prot122       privatelink.blob.core.windows.net       10.12.0.34
abcdepod01prot120       privatelink.blob.core.windows.net       10.12.0.26

```

#### <a name="step-2-create--dns-entries"></a>Étape 2 : créer des entrées DNS

Créez des entrées DNS correspondant à celles ci-dessus. Selon le type de DNS que vous utilisez, vous avez deux possibilités pour créer des entrées DNS.

**Cas 1** : Si vous utilisez un serveur DNS personnalisé, vous devez créer manuellement des entrées pour chaque enregistrement à partir du script ci-dessus et vérifier que le nom de domaine complet (NomRessource.DNS) est résolu en une adresse IP privée dans le réseau virtuel.

**Cas 2** : Si vous utilisez une zone de DNS privé Azure, vous pouvez y créer automatiquement des entrées DNS à l’aide du script [CreateDNSEntries.ps1](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/CreateDNSEntries.ps1). Dans la syntaxe suivante, `subscription` est l’abonnement où la zone de DNS privé réside.

**Syntaxe d’utilisation du script**

```azurepowershell
/CreateDNSEntries.ps1 -Subscription <PrivateDNSZoneSubId> -DNSResourceGroup <PrivateDNSZoneRG> -DNSRecordListFile dnsentries.txt
```

#### <a name="summary-of-the-entire-process"></a>Résumé de l’ensemble du processus

Pour configurer correctement un point de terminaison privé pour RSV par le biais de cette solution de contournement, vous devez :

1. Créer un point de terminaison privé pour le coffre (comme décrit précédemment dans l’article).
1. Déclencher la détection. La détection de SQL/HANA échoue avec _UserErrorVMInternetConnectivityIssue_, car les entrées DNS sont absentes pour le compte de stockage de communication.
1. Exécutez les scripts pour obtenir les entrées DNS et créer les entrées DNS correspondantes pour le compte de stockage de communication mentionné précédemment dans cette section.
1. Redéclenchez la détection. Cette fois, la détection doit réussir normalement.
1. Déclenchez une sauvegarde. La sauvegarde de SQL/HANA et MARS peut échouer parce que les entrées DNS sont absentes pour les comptes de stockage principaux, comme mentionné précédemment dans cette section.
1. Exécutez les scripts pour créer des entrées DNS pour le compte de stockage principal.
1. Redéclenchez la sauvegarde. Cette fois, les sauvegardes doivent réussir.

## <a name="frequently-asked-questions"></a>Forum aux questions

### <a name="can-i-create-a-private-endpoint-for-an-existing-backup-vaultbr"></a>Puis-je créer un point de terminaison privé pour un coffre de sauvegarde existant ?<br>

Non, les points de terminaison privés ne peuvent être créés que pour les nouveaux coffres de sauvegarde. Le coffre ne doit donc pas contenir d’éléments protégés. En fait, aucune tentative de protection des éléments dans le coffre ne peut être effectuée avant de créer des points de terminaison privés.

### <a name="i-tried-to-protect-an-item-to-my-vault-but-it-failed-and-the-vault-still-doesnt-contain-any-items-protected-to-it-can-i-create-private-endpoints-for-this-vaultbr"></a>J’ai essayé de protéger un élément dans mon coffre, mais l’opération a échoué et le coffre ne contient toujours aucun élément protégé. Puis-je créer des points de terminaison privés pour ce coffre ?<br>

Non, le coffre ne doit pas avoir subi de tentatives de protection.

### <a name="i-have-a-vault-thats-using-private-endpoints-for-backup-and-restore-can-i-later-add-or-remove-private-endpoints-for-this-vault-even-if-i-have-backup-items-protected-to-itbr"></a>J’ai un coffre qui utilise des points de terminaison privés pour la sauvegarde et la restauration. Puis-je ajouter ou supprimer ultérieurement des points de terminaison privés pour ce coffre, même si des éléments de sauvegarde y sont protégés ?<br>

Oui. Si vous avez déjà créé des points de terminaison privés pour un coffre et des éléments de sauvegarde protégés, vous pouvez ajouter ou supprimer ultérieurement des points de terminaison privés en fonction des besoins.

### <a name="can-the-private-endpoint-for-azure-backup-also-be-used-for-azure-site-recoverybr"></a>Le point de terminaison privé du service Sauvegarde Azure peut-il également être utilisé pour le service Azure Site Recovery ?<br>

Non, le point de terminaison privé du service Sauvegarde ne peut être utilisé que pour le service Sauvegarde Azure. Vous devez créer un nouveau point de terminaison privé pour le service Azure Site Recovery, si cela est pris en charge par le service.

### <a name="i-missed-one-of-the-steps-in-this-article-and-went-on-to-protect-my-data-source-can-i-still-use-private-endpointsbr"></a>Je n’ai pas suivi l’une des étapes de cet article, mais j’ai pu protéger ma source de données. Puis-je continuer à utiliser des points de terminaison privés ?<br>

Si vous ne suivez pas chaque étape de cet article et que vous continuez à protéger des éléments, le coffre risque de ne pas pouvoir utiliser des points de terminaison privés. C’est pourquoi nous vous recommandons de vous référer à cette liste de vérification avant de continuer à protéger les éléments.

### <a name="can-i-use-my-own-dns-server-instead-of-using-the-azure-private-dns-zone-or-an-integrated-private-dns-zonebr"></a>Puis-je utiliser mon propre serveur DNS au lieu d’utiliser la zone DNS privée Azure ou une zone DNS privée intégrée ?<br>

Oui, vous pouvez utiliser vos propres serveurs DNS. Toutefois, assurez-vous que tous les enregistrements DNS requis sont ajoutés comme indiqué dans cette section.

### <a name="do-i-need-to-perform-any-additional-steps-on-my-server-after-ive-followed-the-process-in-this-articlebr"></a>Dois-je suivre des étapes supplémentaires sur mon serveur après avoir suivi toutes les procédures de cet article ?<br>

Après avoir suivi toutes les procédures de cet article, vous avez besoin de rien d’autre pour utiliser des points de terminaison privés pour la sauvegarde et la restauration.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur toutes les [fonctionnalités de sécurité de Sauvegarde Azure](security-overview.md).