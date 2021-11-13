---
title: Se connecter à des systèmes SAP
description: Accéder aux ressources SAP et les gérer en automatisant les workflows avec Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, azla
ms.topic: how-to
ms.date: 11/01/2021
tags: connectors
ms.openlocfilehash: a400c8e5ac118250afedd27f2f8e79b678546727
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131438642"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Se connecter aux systèmes SAP à partir d’Azure Logic Apps

Cet article explique comment vous pouvez accéder à vos ressources SAP à partir d’Azure Logic Apps à l’aide du [connecteur SAP](/connectors/sap/).

## <a name="prerequisites"></a>Prérequis

* Un compte et un abonnement Azure. Si vous n’avez pas encore d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Le workflow d’application logique à partir duquel vous souhaitez accéder à vos ressources SAP. Si vous débutez avec Azure Logic Apps, consultez la [vue d’ensemble du service Azure Logic Apps](logic-apps-overview.md) et le [guide de démarrage rapide pour créer votre premier workflow d’application logique dans le portail Azure](quickstart-create-first-logic-app-workflow.md).

  * Si vous avez utilisé une version précédente du connecteur SAP qui a été déconseillée, vous devez [migrer vers le connecteur actuel](#migrate-to-current-connector) pour pouvoir vous connecter à votre serveur SAP.

  * Si vous exécutez votre workflow d’application logique dans une instance Azure multilocataire, consultez la section [Conditions préalables pour une instance Azure mutualisée](#multi-tenant-azure-prerequisites).

  * Si vous exécutez votre workflow d’application logique dans un [environnement de service d’intégration (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md) de niveau Premium, consultez la section [Prérequis ISE](#ise-prerequisites).

* Un [serveur d’applications SAP](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) ou un [serveur de messages SAP](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm) auquel vous souhaitez accéder à partir d’Azure Logic Apps. Pour plus d’informations sur les serveurs SAP qui prennent en charge ce connecteur, consultez la section [Compatibilité SAP](#sap-compatibility).

  > [!IMPORTANT]
  > Veillez à configurer votre compte d’utilisateur et votre serveur SAP pour qu’ils autorisent l’utilisation de RFC. Pour plus d’informations, notamment sur les types de comptes d’utilisateur pris en charge et l’autorisation minimale requise pour chaque type d’action (RFC, BAPI, IDOC), consultez la note SAP suivante : [460089 - Profils d’autorisation minimum pour les programmes RFC externes](https://launchpad.support.sap.com/#/notes/460089). 
  >
  > * Pour les actions RFC, le compte d’utilisateur a également besoin d’accéder aux modules de fonction `RFC_GROUP_SEARCH` et `DD_LANGU_TO_ISOLA`.
  > * Pour les actions BAPI, le compte d’utilisateur doit également accéder aux modules de fonction suivants : `BAPI_TRANSACTION_COMMIT`, `BAPI_TRANSACTION_ROLLBACK`, `RPY_BOR_TREE_INIT`, `SWO_QUERY_METHODS` et `SWO_QUERY_API_METHODS`.
  > * Pour les actions IDOC, le compte d’utilisateur doit également accéder aux modules de fonction suivants : `IDOCTYPES_LIST_WITH_MESSAGES`, `IDOCTYPES_FOR_MESTYPE_READ`, `INBOUND_IDOCS_FOR_TID`, `OUTBOUND_IDOCS_FOR_TID`, `GET_STATUS_FROM_IDOCNR` et `IDOC_RECORD_READ`.
  > * Pour l’action **Read Table** (Lire la table), le compte d’utilisateur doit également accéder à l’*un* des modules de fonction suivants : `RFC BBP_RFC_READ_TABLE` ou `RFC_READ_TABLE`.

* Contenu du message à envoyer à votre serveur SAP, comme un exemple de fichier IDoc. Ce contenu doit être au format XML et inclure l’espace de noms pour l’[action SAP](#actions) à utiliser. Vous pouvez [envoyer des fichiers IDoc avec un schéma de fichier plat en les encapsulant dans une enveloppe XML](#send-flat-file-idocs).

* Si vous voulez utiliser le déclencheur **Quand un message est reçu de SAP**, vous devez également effectuer les tâches suivantes :

  * Configurez les autorisations de sécurité ou la liste de contrôle d’accès de votre passerelle SAP. Dans les fichiers **secinfo** et **reginfo**, qui sont visibles dans la boîte de dialogue Gateway Monitor (Moniteur de passerelle) (T-Code SMGW), suivez **Goto (Atteindre) > Expert Functions (Fonctions expertes) > External Security (Sécurité externe) > Maintenance of ACL Files (Maintenance des fichiers de liste de contrôle d’accès)** . L’autorisation suivante est requise :

    `P TP=LOGICAPP HOST=<on-premises-gateway-server-IP-address> ACCESS=*`

    Cette ligne est au format suivant :

    `P TP=<trading-partner-identifier-(program-name)-or-*-for-all-partners> HOST=<comma-separated-list-with-external-host-IP-or-network-names-that-can-register-the-program> ACCESS=<*-for-all-permissions-or-a-comma-separated-list-of-permissions>`

    Si vous ne configurez pas les autorisations de sécurité de la passerelle SAP, vous pouvez recevoir le message d’erreur suivant :

    `Registration of tp Microsoft.PowerBI.EnterpriseGateway from host <host-name> not allowed`

    Pour plus d’informations, consultez la [note SAP 1850230 - GW : "Inscription de l’&lt;ID de programme&gt; tp non autorisé"](https://userapps.support.sap.com/sap/support/knowledge/en/1850230).

  * Configurez la journalisation de la sécurité de votre passerelle SAP pour trouver les problèmes en lien avec la liste de contrôle d’accès. Pour plus d’informations, consultez la [rubrique d’aide SAP sur la configuration de la journalisation de la passerelle](https://help.sap.com/erp_hcm_ias2_2015_02/helpdata/en/48/b2a710ca1c3079e10000000a42189b/frameset.htm).

  * Dans la boîte de dialogue **Configuration of RFC Connections (Configuration des connexions RFC)** (T-Code SM59), créez une connexion RFC avec le type **TCP/IP**. **Activation Type (Type d’activation)** doit être défini sur **Registered Server Program (Programme de serveur inscrit)** . Définissez la valeur **Communication Type with Target System (Type de communication avec système cible)** sur **Unicode**.

  * Si vous utilisez ce déclencheur SAP avec le paramètre **IDOC Format (Format IDOC)** défini sur **FlatFile** avec l’[action Flat File Decode (Décodage de fichier plat)](logic-apps-enterprise-integration-flatfile.md), vous devez utiliser la propriété `early_terminate_optional_fields` dans votre schéma de fichier plat en définissant la valeur sur `true`.

    Cette condition est nécessaire car l’enregistrement de données IDOC de fichier plat envoyé par SAP sur l’appel tRFC `IDOC_INBOUND_ASYNCHRONOUS` n’est pas rempli à la longueur de champ SDATA complète. Azure Logic Apps fournit les données d’origine IDoc du fichier plat sans remplissage à partir de SAP. Par ailleurs, lorsque vous combinez ce déclencheur SAP avec l’action de décodage de fichier plat, le schéma fourni à l’action doit correspondre.

  > [!NOTE]
  > Ce déclencheur SAP utilise le même emplacement d’URI pour renouveler un abonnement webhook et l’annuler. L’opération de renouvellement utilise la méthode HTTP `PATCH`, tandis que l’opération d’annulation d’abonnement utilise la méthode HTTP `DELETE`. En raison de ce comportement, une opération de renouvellement peut apparaître comme une opération d’annulation d’abonnement dans l’historique de votre déclencheur, mais l’opération est toujours un renouvellement, car le déclencheur utilise `PATCH` comme méthode HTTP, et non `DELETE`.

### <a name="sap-compatibility"></a>Compatibilité SAP

Le connecteur SAP est compatible avec les types de systèmes SAP suivants :

* Systèmes SAP HANA locaux et basés sur le cloud, comme S/4 HANA.

* Systèmes SAP locaux classiques, comme R/3 et ECC.

Le connecteur SAP prend en charge les types d’intégration de données et de messages suivants des systèmes basés sur SAP NetWeaver :

* IDoc (Intermediate Document)

* BAPI (Business Application Programming Interface)

* RFC (Remote Function Call) et tRFC (Transactional RFC)

Le connecteur SAP utilise la [bibliothèque NCo (.NET Connector) SAP](https://support.sap.com/en/product/connectors/msnet.html).

Pour utiliser le [déclencheur SAP](#triggers) et les [actions SAP](#actions) disponibles, vous devez d’abord authentifier votre connexion. Pour ce faire, indiquez un nom d’utilisateur et un mot de passe. Le connecteur SAP prend également en charge [SAP SNC (Secure Network Communications)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true) pour l’authentification. Vous pouvez utiliser SNC pour l’authentification unique (SSO) SAP NetWeaver ou pour des fonctionnalités de sécurité supplémentaires fournies par des produits externes. Si vous utilisez SNC, passez en revue les [prérequis SNC](#snc-prerequisites) et les [conditions préalables SNC pour le connecteur ISE](#snc-prerequisites-ise).

### <a name="migrate-to-current-connector"></a>Migrer vers le connecteur actuel

Les connecteurs Serveur d’applications SAP et Serveur de messages SAP précédents ont été déconseillés le 29 février 2020. Pour migrer vers le connecteur SAP actuel, procédez comme suit :

1. Mettez à jour votre [passerelle de données locale](https://www.microsoft.com/download/details.aspx?id=53127) vers la version actuelle. Pour plus d’informations, consultez [Installer une passerelle de données locale pour Azure Logic Apps](logic-apps-gateway-install.md).

1. Dans votre workflow d’application logique qui utilise le connecteur SAP déconseillé, supprimez l’action **Envoyer à SAP**.

1. À partir du connecteur SAP actuel, ajoutez l’action **Envoyer un message à SAP**.

1. Reconnectez-vous à votre système SAP dans la nouvelle action.

1. Enregistrez votre workflow d’application logique. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**.

## <a name="multi-tenant-azure-prerequisites"></a>Conditions préalables pour une instance Azure mutualisée

Ces conditions préalables s’appliquent si votre workflow d’application logique s’exécute dans une instance Azure multilocataire. Le connecteur SAP managé ne s’exécute pas en mode natif dans un environnement [ISE](connect-virtual-network-vnet-isolated-environment-overview.md).

> [!TIP]
> Si vous utilisez un environnement ISE de niveau Premium, vous pouvez utiliser le connecteur SAP ISE au lieu du connecteur SAP managé. Pour plus d’informations, consultez la section [Prérequis ISE](#ise-prerequisites).

Le connecteur SAP managé s’intègre aux systèmes SAP via votre [passerelle de données locale](logic-apps-gateway-connection.md). Par exemple, dans les scénarios d’envoi de messages, lorsqu’un message est envoyé d’un workflow d’application logique à un système SAP, la passerelle de données agit comme un client RFC et transfère les demandes reçues du workflow d’application logique à SAP. De même, dans les scénarios de réception de messages, la passerelle de données agit en tant que serveur RFC qui reçoit des demandes de SAP et les transfère au workflow d’application logique.

1. [Téléchargez et installez la passerelle de données locale](logic-apps-gateway-install.md) sur un ordinateur hôte ou une machine virtuelle qui existe dans le même réseau virtuel que le système SAP auquel vous vous connectez.

1. [Créez une ressource de passerelle Azure](logic-apps-gateway-connection.md#create-azure-gateway-resource) pour votre passerelle de données locale dans le portail Azure. Cette passerelle vous permet d’accéder en toute sécurité aux données et ressources locales. Veillez à utiliser une version prise en charge de la passerelle.

  > [!TIP]
  > Si vous rencontrez un problème avec votre passerelle, essayez de[la mettre à niveau vers la dernière version](https://aka.ms/on-premises-data-gateway-installer), qui peut inclure des mises à jour pour résoudre votre problème.

1. [Téléchargez et installez la dernière bibliothèque de client SAP](#sap-client-library-prerequisites) sur le même ordinateur local que votre passerelle de données locale.

1. Configurez les noms d’hôte réseau et la résolution des noms de service pour l’ordinateur hôte sur lequel vous avez installé la passerelle de données locale.

  Si vous envisagez d’utiliser des noms d’hôte ou des noms de service pour la connexion à partir d’Azure Logic Apps, vous devez configurer chaque serveur d’applications, de messages et de passerelle SAP avec leurs services pour la résolution de noms. La résolution du nom d’hôte réseau est configurée dans le fichier `%windir%\System32\drivers\etc\hosts` ou dans le serveur DNS disponible pour votre ordinateur hôte de la passerelle de données locale. La résolution de noms de service est configurée dans `%windir%\System32\drivers\etc\services`. Si vous n’envisagez pas d’utiliser des noms d’hôte réseau ou des noms de service pour la connexion, vous pouvez utiliser les adresses IP et les numéros de port de service de l’hôte à la place.

   Si vous ne disposez pas d’une entrée DNS pour votre système SAP, l’exemple suivant montre un exemple d’entrée pour le fichier hosts :

   ```text
   10.0.1.9           sapserver                   # SAP single-instance system host IP by simple computer name
   10.0.1.9           sapserver.contoso.com       # SAP single-instance system host IP by fully qualified DNS name
   ```

   Voici un exemple d’ensemble d’entrées pour les fichiers de services :

   ```text
   sapdp00            3200/tcp              # SAP system instance 00 dialog (application) service port
   sapgw00            3300/tcp              # SAP system instance 00 gateway service port
   sapmsDV6           3601/tcp              # SAP system ID DV6 message service port
   ```

## <a name="ise-prerequisites"></a>Prérequis ISE

Un ISE fournit un accès aux ressources protégées par un réseau virtuel Azure et offre d’autres connecteurs ISE natifs qui permettent aux workflowx d’application logique d’accéder directement à des ressources locales sans utiliser la passerelle de données locale.

1. Si vous ne disposez pas déjà d’un compte Stockage Azure avec un conteneur d’objets blob, créez un conteneur à l’aide du [portail Azure](../storage/blobs/storage-quickstart-blobs-portal.md) ou d’[Explorateur Stockage Azure](../storage/blobs/quickstart-storage-explorer.md).

1. [Téléchargez et installez la dernière bibliothèque de client SAP](#sap-client-library-prerequisites) sur votre ordinateur local. Vous devez disposer des fichiers d’assembly suivants :

   * libicudecnumber.dll

   * rscp4n.dll

   * sapnco.dll

   * sapnco_utils.dll

1. Créez un fichier .zip qui comprend ces fichiers d’assembly. Chargez le package dans votre conteneur d’objets blob dans Stockage Azure.

1. Dans le portail Azure ou Explorateur Stockage Azure, accédez à l’emplacement du conteneur dans lequel vous avez chargé le fichier .zip.

1. Copiez l’URL de l’emplacement du conteneur. Veillez à inclure le jeton de signature d’accès partagé (SAS), afin que le jeton SAS soit autorisé. Dans le cas contraire, le déploiement du connecteur ISE SAP échoue.

1. Installez et déployez le connecteur SAP dans votre environnement ISE. Pour plus d’informations, consultez [Ajouter des connecteurs ISE](add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment).

   1. Dans le [portail Azure](https://portal.azure.com), recherchez et ouvrez votre ISE.

   1. Dans le menu ISE, sélectionnez **Connecteurs managés** &gt; **Ajouter**. Dans la liste des connecteurs, recherchez et sélectionnez **SAP**.

   1. Dans le volet **Ajouter un nouveau connecteur managé**, dans la zone **Package SAP**, collez l’URL du fichier .zip qui contient les assemblys SAP. De nouveau, veillez à inclure le jeton de signature d’accès partagé (SAS).

   1. Sélectionnez **Créer** pour terminer la création de votre connecteur ISE.

1. Si votre instance SAP et votre environnement ISE se trouvent dans des réseaux virtuels différents, vous devez également [appairer ces réseaux](../virtual-network/tutorial-connect-virtual-networks-portal.md) afin qu’ils soient connectés. Passez également en revue les [conditions préalables SNC pour le connecteur ISE](#snc-prerequisites-ise).

1. Récupérez les adresses IP des serveurs de passerelle, de messages et d’applications SAP que vous prévoyez d’utiliser pour la connexion à partir de votre workflow d’application logique. La résolution de noms réseau n’est pas disponible pour les connexions SAP dans un environnement ISE.

1. Récupérez les numéros de port pour les services de passerelle, de messages et d’applications SAP que vous prévoyez d’utiliser pour la connexion à l’application logique. La résolution de nom de service n’est pas disponible pour le connecteur SAP dans ISE.

### <a name="sap-client-library-prerequisites"></a>Conditions préalables pour la bibliothèque de client SAP

La liste ci-dessous décrit les conditions préalables pour la bibliothèque de client SAP que vous utilisez avec le connecteur.

* Veillez à installer la dernière version du [connecteur SAP (NCo 3.0) pour Microsoft .NET 3.0.24.0 compilé avec .NET Framework 4.0 – Windows 64 bits (x64)](https://support.sap.com/en/product/connectors/msnet.html). Les versions antérieures de SAP NCo peuvent rencontrer les problèmes suivants :

  * Lorsque plusieurs messages IDoc sont envoyés en même temps, cette condition bloque tous les messages ultérieurs envoyés à la destination SAP, ce qui entraîne l’expiration des messages.

  * L’activation de la session peut échouer en raison d’une session perdue. Cette condition peut bloquer les appels envoyés par SAP au déclencheur de workflow d’application logique.

  * La passerelle de données locale (version de juin 2021) dépend de la méthode `SAP.Middleware.Connector.RfcConfigParameters.Dispose()` dans SAP NCo pour libérer des ressources.

* Vous devez disposer de la version 64 bits de la bibliothèque de client SAP installée, car la passerelle de données s’exécute uniquement sur des systèmes 64 bits. L’installation de la version 32 bits non prise en charge génère une erreur « image incorrecte ».

* Copiez les fichiers d’assembly à partir du dossier d’installation par défaut vers un autre emplacement, selon votre scénario, comme suit.

  * Pour un workflow d’application logique qui s’exécute dans un environnement ISE, suivez les [prérequis ISE](#ise-prerequisites) à la place.

  * Pour un workflow d’application logique qui s’exécute dans une instance Azure multilocataire et utilise votre passerelle de données locale, copiez les fichiers d’assembly vers le dossier d’installation de la passerelle de données. 

    > [!NOTE]
    > Si votre connexion SAP échoue avec le message d’erreur **Vérifiez vos informations de compte et/ou vos autorisations, puis réessayez**, vérifiez que vous avez copié les fichiers d’assembly dans le dossier d’installation de la passerelle de données.
    > 
    > Vous pouvez résoudre les autres problèmes à l’aide de la [visionneuse du journal de liaison d’assembly .NET](/dotnet/framework/tools/fuslogvw-exe-assembly-binding-log-viewer). 
    > Cet outil vous permet de vérifier que vos fichiers d’assembly se trouvent à l’emplacement approprié. 
  
  * Si vous le souhaitez, sélectionnez l’option **Inscription du Global Assembly Cache** lors de l’installation de la bibliothèque de client SAP.

Prenez note des relations suivantes entre la bibliothèque de client SAP, le .NET Framework, le runtime .NET et la passerelle :

* L’adaptateur SAP Microsoft et le service hôte de la passerelle utilisent tous deux le .NET Framework 4.7.2.

* Le NCo SAP pour .NET Framework 4.0 fonctionne avec les processus qui utilisent un runtime .NET de la version 4.0 à la version 4.8.

* Le NCo SAP pour .NET Framework 2.0 fonctionne avec les processus qui utilisent un runtime .NET 2.0 à 3.5, mais ne fonctionne plus avec la passerelle la plus récente.

### <a name="snc-prerequisites"></a>Prérequis SNC

Si vous utilisez une passerelle de données locale avec la fonctionnalité SNC en option, qui est uniquement prise en charge dans une instance Azure multilocataire, vous devez configurer ces paramètres supplémentaires. Si vous utilisez un environnement ISE, passez en revue les [conditions préalables SNC pour le connecteur ISE](#snc-prerequisites-ise).

Si vous utilisez la fonctionnalité SNC avec l’authentification unique (SSO), assurez-vous que le service de la passerelle de données s’exécute en tant qu’utilisateur mappé à l’utilisateur SAP. Pour modifier le compte par défaut, sélectionnez **Changer de compte**, puis entrez les informations d’identification de l’utilisateur.

![Capture d’écran montrant le portail Azure avec les paramètres de la passerelle de données locale et la page Paramètres du service avec le bouton permettant de modifier le compte du service de la passerelle sélectionné.](./media/logic-apps-using-sap-connector/gateway-account.png)

Si vous activez la fonctionnalité SNC via un produit de sécurité externe, copiez la bibliothèque ou les fichiers SNC sur l’ordinateur où votre passerelle de données est installée. [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos et NTLM sont des exemples de produits SNC. Pour plus d’informations sur l’activation de SNC pour la passerelle de données, consultez [Activer Secure Network Communications (SNC)](#enable-secure-network-communications).

> [!TIP]
> La version de votre bibliothèque SNC et ses dépendances doivent être compatibles avec votre environnement SAP.
>
> * Vous devez utiliser `sapgenpse.exe` spécifiquement en tant qu’utilitaire SAPGENPSE.
> * Si vous utilisez une passerelle de données locale, copiez également ces mêmes fichiers binaires dans le dossier d’installation.
> * Si PSE est fourni dans votre connexion, vous n’avez pas besoin de copier et de configurer PSE et SECUDIR pour votre passerelle de données locale.
> * Vous pouvez également utiliser votre passerelle de données locale pour résoudre les problèmes de compatibilité de bibliothèque.

#### <a name="snc-prerequisites-ise"></a>Prérequis SNC (ISE)

La version ISE du connecteur SAP prend en charge SNC X.509. Vous pouvez activer SNC pour vos connexions SAP ISE en procédant comme suit :

> [!IMPORTANT]
> Avant de redéployer un connecteur SAP existant pour utiliser SNC, vous devez supprimer toutes les connexions à l’ancien connecteur. Plusieurs workflows d’application logique peuvent utiliser la même connexion à SAP. Par conséquent, vous devez supprimer toutes les connexions SAP de tous vos workflow d’application logique dans l’environnement ISE. Vous devez ensuite supprimer l’ancien connecteur.
>
> Lorsque vous supprimez un ancien connecteur, vous pouvez toujours conserver les workflows d’application logique qui utilisent ce connecteur. Une fois que vous avez redéployé le connecteur, vous pouvez authentifier la nouvelle connexion dans vos déclencheurs et actions SAP dans ces workflows d’application logique.

Tout d’abord, si vous avez déjà déployé le connecteur SAP sans les bibliothèques SNC ou SAPGENPSE, supprimez toutes les connexions et le connecteur.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Supprimez toutes les connexions à votre connecteur SAP à partir de vos workflows d’application logique.

   1. Ouvrez votre ressource d’application logique dans le portail Azure.

   1. Dans le menu de votre application logique, sous **Outils de développement**, sélectionnez **Connexions d’API**.

   1. Dans la page **Connexions d’API**, sélectionnez votre connexion SAP.

   1. Dans le menu de la page de connexions, sélectionnez **Supprimer**.

   1. Acceptez l’invite de confirmation pour supprimer la connexion.

   1. Patientez jusqu’à ce qu’une notification du portail vous indique que la connexion a été supprimée.

1. Ou supprimez les connexions à votre connecteur SAP à partir des connexions d’API de votre environnement ISE.

   1. Ouvrez votre ressource ISE dans le portail Azure.

   1. Dans le menu de votre environnement ISE, sous **Paramètres**, sélectionnez **Connexions d’API**.

   1. Dans la page **Connexions d’API**, sélectionnez votre connexion SAP.

   1. Dans le menu de la page de connexions, sélectionnez **Supprimer**.

   1. Acceptez l’invite de confirmation pour supprimer la connexion.

   1. Patientez jusqu’à ce qu’une notification du portail vous indique que la connexion a été supprimée.

Ensuite, supprimez le connecteur SAP de votre environnement ISE. Vous devez supprimer toutes les connexions à ce connecteur dans toutes vos applications logiques avant de pouvoir supprimer le connecteur. Si vous n’avez pas encore supprimé toutes les connexions, revoyez l’ensemble des étapes précédentes.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Rouvrez votre ressource ISE dans le portail Azure.

1. Dans le menu de votre environnement ISE, sous **Paramètres**, sélectionnez **Connecteurs managés**.

1. Dans la page **Connecteurs managés**, activez la case à cocher correspondant à votre connecteur SAP.

1. Dans la barre d’outils, sélectionnez **Supprimer**.

1. Acceptez l’invite de confirmation pour supprimer le connecteur.

1. Patientez jusqu’à ce qu’une notification du portail vous indique que le connecteur a été supprimé.

Ensuite, déployez ou redéployez le connecteur SAP dans votre environnement ISE :

1. Préparez un nouveau fichier d’archive zip à utiliser dans le déploiement de votre connecteur SAP. Vous devez inclure la bibliothèque SNC et l’utilitaire SAPGENPSE.

   1. Copiez toutes les bibliothèques SNC, SAPGENPSE et NCo dans le dossier racine de votre archive zip. Ne placez pas ces fichiers binaires dans des sous-dossiers.

   1. Vous devez utiliser la bibliothèque SNC 64 bits. Il n’existe aucune prise en charge de la bibliothèque 32 bits.

   1. Votre bibliothèque SNC et ses dépendances doivent être compatibles avec votre environnement SAP. Pour savoir comment vérifier la compatibilité, consultez les [prérequis ISE](#ise-prerequisites).

1. Suivez les étapes de déploiement indiquées dans les [prérequis ISE](#ise-prerequisites) avec votre nouvel archive zip.

Enfin, créez de nouvelles connexions qui utilisent SNC dans toutes vos applications logiques qui utilisent le connecteur SAP. Pour chaque connexion, procédez comme suit :

1. Ouvrez de nouveau votre workflow dans le concepteur de workflow.

1. Créez ou modifiez une étape qui utilise le connecteur SAP.

1. Entrez les informations requises sur votre connexion SAP.

   :::image type="content" source=".\media\logic-apps-using-sap-connector\ise-connector-settings.png" alt-text="Capture d’écran du concepteur de workflow, montrant les paramètres de connexion SAP." lightbox=".\media\logic-apps-using-sap-connector\ise-connector-settings.png":::

   > [!NOTE]
   > Les champs **Nom d’utilisateur SAP** et **Mot de passe SAP** sont facultatifs. Si vous ne fournissez pas de nom d’utilisateur et de mot de passe, le connecteur utilise le certificat client fourni dans une étape ultérieure pour l’authentification.

1. Activez SNC.

   1. Pour **Utiliser SNC**, activez la case à cocher.

   1. Pour **Bibliothèque SNC**, entrez le nom de votre bibliothèque SNC. Par exemple : `sapcrypto.dll`.

   1. Pour **Nom du partenaire SNC**, entrez le nom SNC du backend. Par exemple : `p:CN=DV3, OU=LA, O=MS, C=US`.

   1. Pour **Certificat SNC**, entrez le certificat public de votre client SNC au format codé en base64. N’incluez pas l’en-tête ou le pied de page PEM. N’entrez pas le certificat privé ici, car le PSE peut contenir plusieurs certificats privés, et ce paramètre **Certificat SNC** identifie les certificats qui doivent être utilisés pour cette connexion. Pour plus d’informations, reportez-vous à la note ci-après.

   1. Si besoin, entrez les paramètres SNC pour **SNC My Name**, **SNC Quality of Protection**.

   :::image type="content" source=".\media\logic-apps-using-sap-connector\ise-connector-settings-snc.png" alt-text="Capture d’écran montrant le concepteur de workflow et les paramètres de configuration SNC pour une nouvelle connexion SAP." lightbox=".\media\logic-apps-using-sap-connector\ise-connector-settings-snc.png":::

1. Configurez les paramètres PSE. Pour **PSE**, entrez votre PSE SNC au format binaire codé en base 64.

   * Le PSE doit contenir le certificat de client privé, qui correspond au certificat de client public que vous avez fourni à l’étape précédente.

   * Le PSE peut contenir des certificats clients supplémentaires.

   * Le PSE ne doit pas avoir de code confidentiel. Si nécessaire, définissez le code confidentiel sur l’option Empty à l’aide de l’utilitaire SAPGENPSE.

   Pour la rotation des certificats, procédez comme suit :

   1. Mettez à jour le fichier PSE binaire encodé en base64 pour toutes les connexions qui utilisent SAP ISE X.509 dans votre environnement ISE.

   1. Importez les nouveaux certificats dans votre copie du PSE.

   1. Encodez le fichier PSE en tant que binaire encodé en base64.

   1. Modifiez la connexion d’API pour votre connecteur SAP et enregistrez le nouveau fichier PSE ici.

   Le connecteur détecte la modification de PSE et met à jour sa propre copie lors de la demande de connexion suivante.

   Pour convertir un fichier PSE binaire au format encodé en base64, procédez comme suit :
   
   1. Utilisez un script PowerShell, par exemple :

      ```powershell
      Param ([Parameter(Mandatory=$true)][string]$psePath, [string]$base64OutputPath)
      $base64String = [convert]::ToBase64String((Get-Content -path $psePath -Encoding byte))
      if ($base64OutputPath -eq $null)
      {
          Write-Output $base64String
      }
      else
      {
          Set-Content -Path $base64OutputPath -Value $base64String
          Write-Output "Output written to $base64OutputPath"
      } 
      ```

   1. Enregistrez le script en tant que fichier `pseConvert.ps1`, puis appelez le script, par exemple :

      ```output
      .\pseConvert.ps1 -psePath "C:\Temp\SECUDIR\request.pse" -base64OutputPath "connectionInput.txt"
      Output written to connectionInput.txt 
      ```

      Si le paramètre du chemin de sortie n’est pas fourni, la sortie du script affichée dans la console contiendra des sauts de ligne. Supprimez les sauts de ligne dans la chaîne encodée en base64 pour le paramètre d’entrée de connexion.

   > [!NOTE]
   > Si vous utilisez plus d’un certificat client SNC pour votre environnement ISE, vous devez fournir le même PSE pour toutes les connexions. Le PSE doit contenir le certificat privé client pour chacune des connexions. Vous devez définir le paramètre du certificat public client pour qu’il corresponde au certificat privé spécifique de chaque connexion utilisée dans votre environnement ISE.

1. Sélectionnez **Créer** pour créer votre connexion. Si les paramètres sont corrects, la connexion est créée. En cas de problème avec les paramètres, la boîte de dialogue de création de connexion affiche un message d’erreur.

   > [!TIP]
   > Pour résoudre les problèmes liés aux paramètres de connexion, vous pouvez utiliser une passerelle de données locale et les journaux locaux de la passerelle.

1. Pour enregistrer vos modifications, sélectionnez **Enregistrer** sur la barre d’outils du concepteur de workflow.

## <a name="send-idoc-messages-to-sap-server"></a>Envoyer des messages IDoc au serveur SAP

Suivez ces exemples pour créer un workflow d’application logique qui envoie un message IDoc à un serveur SAP et retourne une réponse :

1. [Créez un workflow d’application logique déclenché par une requête HTTP.](#add-http-request-trigger)

1. [Créez une action dans votre flux de travail pour envoyer un message à SAP.](#create-sap-action-to-send-message)

1. [Créez une action de réponse HTTP dans votre flux de travail.](#create-http-response-action)

1. [Créez un modèle requête-réponse d’appel de fonction distant (RFC), si vous utilisez un appel RFC pour recevoir des réponses de SAP ABAP.](#create-rfc-request-response)

1. [Testez votre application logique.](#test-your-logic-app-workflow)

<a name="add-http-request-trigger"></a>

### <a name="add-an-http-request-trigger"></a>Ajouter un déclencheur de requête HTTP

Pour que votre workflow d’application logique reçoive des IDocs de SAP sur XML HTTP, vous pouvez utiliser le [déclencheur de requête](../connectors/connectors-native-reqres.md).

> [!TIP]
> Pour recevoir des IDocs via CPIC (Common Programming Interface Communication) en tant que XML brut ou en tant que fichier plat, consultez la section [Réception d’un message de SAP](#receive-message-sap).

Cette section se poursuit avec le déclencheur de requête. Par conséquent, commencez par créer un workflow d’application logique avec un point de terminaison dans Azure pour envoyer des requêtes *HTTP POST* à votre workflow. Lorsque votre workflow d’application logique reçoit ces requêtes HTTP, le déclencheur est activé et passe à l’étape suivante de votre workflow.

1. Dans le [portail Azure](https://portal.azure.com), créez une application logique vide, ce qui ouvre le concepteur de workflow.

1. Dans la zone de recherche, entrez `http request` en guise de filtre. Dans la liste **Déclencheurs**, sélectionnez **Lors de la réception d’une requête HTTP**.

   ![Capture d’écran montrant le concepteur de workflow avec un nouveau déclencheur de requête ajouté au workflow d’application logique.](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Enregistrez votre workflow d’application logique, qui génère une URL de point de terminaison qui peut recevoir des demandes. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**. L’URL de point de terminaison s’affiche désormais dans votre déclencheur.

   ![Capture d’écran qui montre le concepteur de workflow avec le déclencheur de requête POST URL générée en cours de copie.](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="create-sap-action-to-send-message"></a>Créer une action SAP pour envoyer un message

Ensuite, créez une action pour envoyer votre message IDoc à SAP lorsque votre [déclencheur de requête](#add-http-request-trigger) se déclenche. Par défaut, le typage fort est utilisé pour rechercher des valeurs non valides en effectuant une validation XML par rapport au schéma. Ce comportement peut vous aider à détecter les problèmes plus tôt. L’option **Types sécurisés** est disponible pour la compatibilité descendante et ne vérifie que la longueur de chaîne. Apprenez-en davantage sur l’[option Types sécurisés](#safe-typing).

1. Dans le concepteur de workflow, sous le déclencheur, sélectionnez **Nouvelle étape**.

   ![Capture d’écran qui montre le concepteur de workflow avec le workflow en cours de modification pour ajouter une nouvelle étape.](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. Dans la zone de recherche, entrez `send message sap` en guise de filtre. Dans la liste **Actions**, sélectionnez **Envoyer un message à SAP**.

   ![Capture d’écran montrant le concepteur de workflow avec l’action « Envoyer un message à SAP » sélectionnée.](./media/logic-apps-using-sap-connector/select-sap-send-action.png)

   Vous pouvez aussi sélectionner l’onglet **Entreprise** et sélectionner l’action SAP.

   ![Capture d’écran montrant le concepteur de workflow avec l’action « Envoyer un message à SAP » sélectionnée sous l’onglet Enterprise.](./media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. Si votre connexion existe déjà, passez à l’étape suivante. Si vous êtes invité à créer une nouvelle connexion, fournissez les informations suivantes pour vous connecter à votre serveur SAP local.

   1. Entrez un nom pour la connexion.

   1. Si vous utilisez la passerelle de données, procédez comme suit :

      1. Dans la section **Passerelle de données**, sous **Abonnement**, sélectionnez d’abord l’abonnement Azure pour la ressource de passerelle de données que vous avez créée dans le portail Azure pour l’installation de votre passerelle de données.

      1. Sous **Passerelle de connexion**, sélectionnez votre ressource de passerelle de données dans Azure.

   1. Pour la propriété **Type d’ouverture de session**, suivez l’étape selon que la propriété est définie sur **Serveur d’applications** ou sur **Groupe**.

      * Pour **Serveur d’applications**, les propriétés suivantes, qui apparaissent habituellement comme facultatives, sont obligatoires :

        ![Capture d’écran montrant comment créer une connexion au serveur d’applications SAP.](./media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Pour **Groupe**, les propriétés suivantes, qui apparaissent habituellement comme facultatives, sont obligatoires :

        ![Capture d’écran montrant comment créer une connexion au serveur de messages SAP.](./media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

        Dans SAP, le groupe d’ouverture de session est maintenu en ouvrant la boîte de dialogue **CCMS: Maintain Logon Groups** (T-Code SMLG). Pour plus d’informations, consultez la [note SAP 26317 : Configurer le groupe d’ouverture de session pour l’équilibrage de charge automatique](https://service.sap.com/sap/support/notes/26317).

      Par défaut, le typage fort est utilisé pour rechercher des valeurs non valides en effectuant une validation XML par rapport au schéma. Ce comportement peut vous aider à détecter les problèmes plus tôt. L’option **Types sécurisés** est disponible pour la compatibilité descendante et ne vérifie que la longueur de chaîne. Apprenez-en davantage sur l’[option Types sécurisés](#safe-typing).

   1. Quand vous avez terminé, sélectionnez **Créer**.

      Azure Logic Apps configure et teste votre connexion pour vérifier son bon fonctionnement.

      > [!NOTE]
      > Si vous recevez l’erreur suivante, il y a un problème avec votre installation de la bibliothèque de client NCo SAP : 
      >
      > **Le test de connexion a échoué. Erreur 'Échec du traitement de la requête. Détails de l’erreur : 'impossible de charger le fichier ou l’assembly 'sapnco, Version=3.0.0.42, Culture=neutral, PublicKeyToken 50436dca5c7f7d23' ou une de ses dépendances. Le système ne peut pas localiser le fichier spécifié.'.'**
      >
      > Veillez à [installer la version requise de la bibliothèque de client NCo SAP et à respecter tous les autres prérequis](#sap-client-library-prerequisites).

1. Maintenant, recherchez et sélectionnez une action à partir de votre serveur SAP.

   1. Dans la zone **Action SAP**, sélectionnez l’icône de dossier. Dans la liste des fichiers, recherchez et sélectionnez l’action que vous voulez utiliser. Pour naviguer dans la liste, utilisez les flèches.

      Cet exemple sélectionne un IDoc avec le type **Commandes**.

      ![Capture d’écran montrant comment rechercher et sélectionner une action IDoc.](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      Si vous ne trouvez pas l’action que vous souhaitez, vous pouvez entrer un chemin d’accès manuellement, par exemple :

      ![Capture d’écran montrant comment fournir manuellement le chemin d’accès à une action IDoc.](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > Spécifiez la valeur de l’**action SAP** via l’éditeur d’expressions. De cette façon, vous pouvez utiliser la même action pour différents types de messages.

      Pour plus d’informations sur les opérations IDoc, consultez [Schémas de message pour les opérations IDoc](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

   1. Cliquez dans la zone **Message d’entrée** pour afficher la liste du contenu dynamique. Dans cette liste, sous **Lors de la réception d’une demande HTTP**, sélectionnez le champ **Corps**.

      Cette étape inclut le contenu du corps de votre déclencheur de requête et envoie ce résultat à votre serveur SAP.

      ![Capture d’écran qui montre la sélection de la propriété « Body » dans le déclencheur de requête.](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      Une fois que vous avez terminé, votre action SAP ressemble à cet exemple :

      ![Capture d’écran montrant la fin de l’action SAP.](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. Enregistrez votre workflow d’application logique. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**.

### <a name="send-flat-file-idocs"></a>Envoyer des fichiers plats IDoc

Vous pouvez utiliser des fichiers IDoc avec un schéma de fichier plat si vous les encapsulez dans une enveloppe XML. Pour envoyer un fichier plat IDoc, utilisez les instructions génériques sur la façon de [créer une action SAP pour envoyer votre message IDoc](#create-sap-action-to-send-message) avec les modifications suivantes.

1. Pour l’action **Envoyer un message à SAP**, utilisez l’URI d’action SAP `http://Microsoft.LobServices.Sap/2007/03/Idoc/SendIdoc`.

1. Mettez en forme votre message d’entrée avec une enveloppe XML.

Pour obtenir un exemple, consultez l’exemple de charge utile XML suivant :

```xml
<SendIdoc xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/">
<idocData>EDI_DC 3000000001017945375750 30INVOIC011BTSVLINV30KUABCABCFPPC LDCA X004010810 4 SAPMSX LSEDI ABCABCFPPC 000d3ae4-723e-1edb-9ca4-cc017365c9fd 20210217054521INVOICINVOIC01ZINVOIC2RE 20210217054520
E2EDK010013000000001017945375000001E2EDK01001000000010 ABCABC1.00000 0060 INVO9988298128 298.000 298.000 LB Z4LR EN 0005065828 L
E2EDKA1 3000000001017945375000002E2EDKA1 000000020 RS ABCABCFPPC 0005065828 ABCABCABC ABCABC Inc. Limited Risk Distributor ABCABC 1950 ABCABCABCA Blvd ABCABAABCAB L5N8L9 CA ABCABC E ON V-ABCABC LDCA
E2EDKA1 3000000001017945375000003E2EDKA1 000000020 AG 0005065828 ABCABCFPPC ABCABC ABCABC ABCABC - FPP ONLY 88 ABCABC Crescent ABCABAABCAB L5R 4A2 CA ABCABC 111 111 1111 E ON ABCABCFPPC EN
E2EDKA1 3000000001017945375000004E2EDKA1 000000020 RE 0005065828 ABCABCFPPC ABCABC ABCABC ABCABC - FPP ONLY 88 ABCABC Crescent ABCABAABCAB L5R 4A2 CA ABCABC 111 111 1111 E ON ABCABCFPPC EN
E2EDKA1 3000000001017945375000005E2EDKA1 000000020 RG 0005065828 ABCABCFPPC ABCABC ABCABC ABCABC - FPP ONLY 88 ABCABC Crescent ABCABAABCAB L5R 4A2 CA ABCABC 111 111 1111 E ON ABCABCFPPC EN
E2EDKA1 3000000001017945375000006E2EDKA1 000000020 WE 0005001847 41 ABCABC ABCABC INC (ABCABC) DC A. ABCABCAB 88 ABCABC CRESCENT ABCABAABCAB L5R 4A2 CA ABCABC 111-111-1111 E ON ABCABCFPPC EN
E2EDKA1 3000000001017945375000007E2EDKA1 000000020 Z3 0005533050 ABCABCABC ABCABC Inc. ABCA Bank Swift Code -ABCABCABCAB Sort Code - 1950 ABCABCABCA Blvd. Acc No -1111111111 ABCABAABCAB L5N8L9 CA ABCABC E ON ABCABCFPPC EN
E2EDKA1 3000000001017945375000008E2EDKA1 000000020 BK 1075 ABCABCABC ABCABC Inc 1950 ABCABCABCA Blvd ABCABAABCAB ON L5N 8L9 CA ABCABC (111) 111-1111 (111) 111-1111 ON
E2EDKA1 3000000001017945375000009E2EDKA1 000000020 CR 1075 CONTACT ABCABCABC 1950 ABCABCABCA Blvd ABCABAABCAB ON L5N 8L9 CA ABCABC (111) 111-1111 (111) 111-1111 ON
E2EDK02 3000000001017945375000010E2EDK02 000000020 0099988298128 20210217
E2EDK02 3000000001017945375000011E2EDK02 000000020 00140-N6260-S 20210205
E2EDK02 3000000001017945375000012E2EDK02 000000020 0026336270425 20210217
E2EDK02 3000000001017945375000013E2EDK02 000000020 0128026580537 20210224
E2EDK02 3000000001017945375000014E2EDK02 000000020 01740-N6260-S
E2EDK02 3000000001017945375000015E2EDK02 000000020 900IAC
E2EDK02 3000000001017945375000016E2EDK02 000000020 901ZSH
E2EDK02 3000000001017945375000017E2EDK02 000000020 9078026580537 20210217
E2EDK03 3000000001017945375000018E2EDK03 000000020 02620210217
E2EDK03 3000000001017945375000019E2EDK03 000000020 00120210224
E2EDK03 3000000001017945375000020E2EDK03 000000020 02220210205
E2EDK03 3000000001017945375000021E2EDK03 000000020 01220210217
E2EDK03 3000000001017945375000022E2EDK03 000000020 01120210217
E2EDK03 3000000001017945375000023E2EDK03 000000020 02420210217
E2EDK03 3000000001017945375000024E2EDK03 000000020 02820210418
E2EDK03 3000000001017945375000025E2EDK03 000000020 04820210217
E2EDK17 3000000001017945375000026E2EDK17 000000020 001DDPDelivered Duty Paid
E2EDK17 3000000001017945375000027E2EDK17 000000020 002DDPdestination
E2EDK18 3000000001017945375000028E2EDK18 000000020 00160 0 Up to 04/18/2021 without deduction
E2EDK28 3000000001017945375000029E2EDK28 000000020 CA BOFACATT Bank of ABCABAB ABCABC ABCABAB 50127217 ABCABCABC ABCABC Inc.
E2EDK28 3000000001017945375000030E2EDK28 000000020 CA 026000082 ABCAbank ABCABC ABCABAB 201456700OLD ABCABCABC ABCABC Inc.
E2EDK28 3000000001017945375000031E2EDK28 000000020 GB ABCAGB2L ABCAbank N.A ABCABA E14, 5LB GB63ABCA18500803115593 ABCABCABC ABCABC Inc. GB63ABCA18500803115593
E2EDK28 3000000001017945375000032E2EDK28 000000020 CA 020012328 ABCABANK ABCABC ABCABAB ON M5J 2M3 2014567007 ABCABCABC ABCABC Inc.
E2EDK28 3000000001017945375000033E2EDK28 000000020 CA 03722010 ABCABABC ABCABABC Bank of Commerce ABCABAABCAB 64-04812 ABCABCABC ABCABC Inc.
E2EDK28 3000000001017945375000034E2EDK28 000000020 IE IHCC In-House Cash Center IHCC1075 ABCABCABC ABCABC Inc.
E2EDK28 3000000001017945375000035E2EDK28 000000020 CA 000300002 ABCAB Bank of ABCABC ABCABAB 0021520584OLD ABCABCABC ABCABC Inc.
E2EDK28 3000000001017945375000036E2EDK28 000000020 US USCC US Cash Center (IHC) city USCC1075 ABCABCABC ABCABC Inc.
E2EDK29 3000000001017945375000037E2EDK29 000000020 0064848944US A CAD CA ABCABC CA United States US CA A Air Air
E2EDKT1 3000000001017945375000038E2EDKT1 000000020 ZJ32E EN
E2EDKT2 3000000001017945375000039E2EDKT2 000038030 GST/HST877845941RT0001 *
E2EDKT2 3000000001017945375000040E2EDKT2 000038030 QST1021036966TQ0001 *
E2EDKT1 3000000001017945375000041E2EDKT1 000000020 Z4VL
E2EDKT2 3000000001017945375000042E2EDKT2 000041030 0.000 *
E2EDKT1 3000000001017945375000043E2EDKT1 000000020 Z4VH
E2EDKT2 3000000001017945375000044E2EDKT2 000043030 *
E2EDK14 3000000001017945375000045E2EDK14 000000020 008LDCA
E2EDK14 3000000001017945375000046E2EDK14 000000020 00710
E2EDK14 3000000001017945375000047E2EDK14 000000020 00610
E2EDK14 3000000001017945375000048E2EDK14 000000020 015Z4F2
E2EDK14 3000000001017945375000049E2EDK14 000000020 0031075
E2EDK14 3000000001017945375000050E2EDK14 000000020 021M
E2EDK14 3000000001017945375000051E2EDK14 000000020 0161075
E2EDK14 3000000001017945375000052E2EDK14 000000020 962M
E2EDP010013000000001017945375000053E2EDP01001000000020 000011 2980.000 EA 298.000 LB MOUSE 298.000 Z4TN 4260
E2EDP02 3000000001017945375000054E2EDP02 000053030 00140-N6260-S 00000120210205 DFUE
E2EDP02 3000000001017945375000055E2EDP02 000053030 0026336270425 00001120210217
E2EDP02 3000000001017945375000056E2EDP02 000053030 0168026580537 00001020210224
E2EDP02 3000000001017945375000057E2EDP02 000053030 9100000 00000120210205 DFUE
E2EDP02 3000000001017945375000058E2EDP02 000053030 911A 00000120210205 DFUE
E2EDP02 3000000001017945375000059E2EDP02 000053030 912PP 00000120210205 DFUE
E2EDP02 3000000001017945375000060E2EDP02 000053030 91300 00000120210205 DFUE
E2EDP02 3000000001017945375000061E2EDP02 000053030 914CONTACT ABCABCABC 00000120210205 DFUE
E2EDP02 3000000001017945375000062E2EDP02 000053030 963 00000120210205 DFUE
E2EDP02 3000000001017945375000063E2EDP02 000053030 965 00000120210205 DFUE
E2EDP02 3000000001017945375000064E2EDP02 000053030 9666336270425 00000120210205 DFUE
E2EDP02 3000000001017945375000065E2EDP02 000053030 9078026580537 00001020210205 DFUE
E2EDP03 3000000001017945375000066E2EDP03 000053030 02920210217
E2EDP03 3000000001017945375000067E2EDP03 000053030 00120210224
E2EDP03 3000000001017945375000068E2EDP03 000053030 01120210217
E2EDP03 3000000001017945375000069E2EDP03 000053030 02520210217
E2EDP03 3000000001017945375000070E2EDP03 000053030 02720210217
E2EDP03 3000000001017945375000071E2EDP03 000053030 02320210217
E2EDP03 3000000001017945375000072E2EDP03 000053030 02220210205
E2EDP19 3000000001017945375000073E2EDP19 000053030 001418VVZ
E2EDP19 3000000001017945375000074E2EDP19 000053030 002RJR-00001 AB ABCABCABC Mouse FORBUS BLUETOOTH
E2EDP19 3000000001017945375000075E2EDP19 000053030 0078471609000
E2EDP19 3000000001017945375000076E2EDP19 000053030 003889842532685
E2EDP19 3000000001017945375000077E2EDP19 000053030 011CN
E2EDP26 3000000001017945375000078E2EDP26 000053030 00459064.20
E2EDP26 3000000001017945375000079E2EDP26 000053030 00352269.20
E2EDP26 3000000001017945375000080E2EDP26 000053030 01052269.20
E2EDP26 3000000001017945375000081E2EDP26 000053030 01152269.20
E2EDP26 3000000001017945375000082E2EDP26 000053030 0126795.00
E2EDP26 3000000001017945375000083E2EDP26 000053030 01552269.20
E2EDP26 3000000001017945375000084E2EDP26 000053030 00117.54
E2EDP26 3000000001017945375000085E2EDP26 000053030 00252269.20
E2EDP26 3000000001017945375000086E2EDP26 000053030 940 2980.000
E2EDP26 3000000001017945375000087E2EDP26 000053030 939 2980.000
E2EDP05 3000000001017945375000088E2EDP05 000053030 + Z400MS List Price 52269.20 17.54 1 EA CAD 2980
E2EDP05 3000000001017945375000089E2EDP05 000053030 + XR1 Tax Jur Code Level 6795.00 13.000 52269.20
E2EDP05 3000000001017945375000090E2EDP05 000053030 + Tax Subtotal1 6795.00 2.28 1 EA CAD 2980
E2EDP05 3000000001017945375000091E2EDP05 000053030 + Taxable Amount + TaxSubtotal1 59064.20 19.82 1 EA CAD 2980
E2EDP04 3000000001017945375000092E2EDP04 000053030 CX 13.000 6795.00 7000000000
E2EDP04 3000000001017945375000093E2EDP04 000053030 CX 0 0 7001500000
E2EDP04 3000000001017945375000094E2EDP04 000053030 CX 0 0 7001505690
E2EDP28 3000000001017945375000095E2EDP28 000053030 00648489440000108471609000 CN CN ABCAB ZZ 298.000 298.000 LB US 400 United Stat KY
E2EDPT1 3000000001017945375000096E2EDPT1 000053030 0001E EN
E2EDPT2 3000000001017945375000097E2EDPT2 000096040 AB ABCABCABC Mouse forBus Bluetooth EN/XC/XD/XX Hdwr Black For Bsnss *
E2EDS01 3000000001017945375000098E2EDS01 000000020 0011
E2EDS01 3000000001017945375000099E2EDS01 000000020 01259064.20 CAD
E2EDS01 3000000001017945375000100E2EDS01 000000020 0056795.00 CAD
E2EDS01 3000000001017945375000101E2EDS01 000000020 01159064.20 CAD
E2EDS01 3000000001017945375000102E2EDS01 000000020 01052269.20 CAD
E2EDS01 3000000001017945375000103E2EDS01 000000020 94200000 CAD
E2EDS01 3000000001017945375000104E2EDS01 000000020 9440.00 CAD
E2EDS01 3000000001017945375000105E2EDS01 000000020 9450.00 CAD
E2EDS01 3000000001017945375000106E2EDS01 000000020 94659064.20 CAD
E2EDS01 3000000001017945375000107E2EDS01 000000020 94752269.20 CAD
E2EDS01 3000000001017945375000108E2EDS01 000000020 EXT
Z2XSK010003000000001017945375000109Z2XSK01000000108030 Z400 52269.20
Z2XSK010003000000001017945375000110Z2XSK01000000108030 XR1 13.000 6795.00 CX
</idocData>
</SendIdoc>
```

### <a name="create-http-response-action"></a>Créer une action de réponse HTTP

Ajoutez maintenant une action de réponse au flux de travail de votre application logique et incluez le résultat de l’action SAP. De cette façon, votre workflow d’application logique renvoie les résultats à partir de votre serveur SAP au demandeur d’origine.

1. Dans le concepteur de workflow, sous l’action SAP, sélectionnez **Nouvelle étape**.

1. Dans la zone de recherche, entrez `response` en guise de filtre. Dans la liste **Actions**, sélectionnez **Réponse**.

1. Cliquez dans la zone **Corps** pour afficher la liste du contenu dynamique. Dans cette liste, sous **Envoyer un message à SAP**, sélectionnez le champ **Corps**.

   ![Capture d’écran montrant la fin de l’action SAP.](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. Enregistrez votre workflow d’application logique.

### <a name="create-rfc-request-response"></a>Créer une requête-réponse RFC

Vous devez créer un modèle de requête et de réponse si vous avez besoin de recevoir des réponses en utilisant un appel de fonction distant (RFC) à Azure Logic Apps à partir de SAP ABAP. Pour recevoir des IDocs dans votre workflow d’application logique, vous devez faire de la première action du workflow une [action de réponse](../connectors/connectors-native-reqres.md#add-a-response-action) avec un code d’état `200 OK` et aucun contenu. Cette étape recommandée termine immédiatement le transfert asynchrone SAP LUW sur tRFC, ce qui rend la conversation SAP CPIC à nouveau disponible. Vous pouvez ensuite ajouter d’autres actions dans votre workflow d’application logique pour traiter l’IDoc reçu sans bloquer d’autres transferts.

> [!NOTE]
> Le déclencheur SAP reçoit des IDocs via tRFC, qui n’a pas de paramètre de réponse par définition.

Pour implémenter un modèle de requête et de réponse, vous devez d’abord découvrir le schéma RFC à l’aide de la [commande `generate schema`](#generate-schemas-for-artifacts-in-sap). Le schéma généré comporte deux nœuds racines possibles : 

* Le nœud de requête, qui est l’appel que vous recevez de SAP.
* Le nœud de réponse, qui est votre réponse à SAP.

Dans l’exemple suivant, un modèle de requête et de réponse est généré à partir du module RFC `STFC_CONNECTION`. Le code XML de la requête est analysé pour extraire une valeur de nœud dans laquelle SAP demande `<ECHOTEXT>`. La réponse insère l’horodatage actuel sous la forme d’une valeur dynamique. Vous recevez une réponse similaire lorsque vous envoyez un RFC `STFC_CONNECTION` à SAP depuis un workflow d’application logique.

```xml
<STFC_CONNECTIONResponse xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <ECHOTEXT>@{first(xpath(xml(triggerBody()?['Content']), '/*[local-name()="STFC_CONNECTION"]/*[local-name()="REQUTEXT"]/text()'))}</ECHOTEXT>
  <RESPTEXT>Azure Logic Apps @{utcNow()}</RESPTEXT>
```

### <a name="test-your-logic-app-workflow"></a>Tester le flux de travail d’une application logique

1. Si votre application logique n’est pas déjà activée, dans le menu associé, sélectionnez **Vue d’ensemble**. Dans la barre d’outils, sélectionnez **Activer**.

1. Dans la barre d’outils du concepteur, sélectionnez **Exécuter**. Cette étape démarre manuellement votre workflow d’application logique.

1. Déclenchez votre workflow d’application logique en envoyant une requête HTTP POST à l’URL de votre déclencheur de requête. Incluez le contenu du message avec votre requête. Pour envoyer la requête, vous pouvez utiliser un outil tel que [Postman](https://www.getpostman.com/apps).

   Pour cet article, la requête envoie un fichier IDoc, qui doit être au format XML et inclure l’espace de noms de l’action SAP que vous utilisez, par exemple :

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
   <idocData>
   <...>
   </idocData>
   </Send>
   ```

1. Une fois votre requête HTTP envoyée, attendez la réponse de votre workflow d’application logique.

   > [!NOTE]
   > Votre workflow d’application logique peut expirer si toutes les étapes nécessaires pour la réponse ne se terminent pas dans la [limite de délai d’attente des requêtes](logic-apps-limits-and-config.md). Si cette situation se produit, les requêtes peuvent être bloquées. Pour vous aider à diagnostiquer les problèmes, découvrez comment vous pouvez [vérifier et surveiller vos applications logiques](monitor-logic-apps.md).

Vous venez de créer un workflow d’application logique qui peut communiquer avec votre serveur SAP. Maintenant que vous avez configuré une connexion SAP pour votre workflow d’application logique, vous pouvez explorer d’autres actions SAP disponibles, telles que BAPI et RFC.

<a name="receive-message-sap"></a>

## <a name="receive-message-from-sap"></a>Recevoir le message de SAP

Cet exemple utilise un workflow d’application logique qui se déclenche quand l’application reçoit un message provenant d’un système SAP.

### <a name="add-an-sap-trigger"></a>Ajouter un déclencheur SAP

1. Dans le portail Azure, créez une application logique vide, ce qui ouvre le concepteur de workflow.

1. Dans la zone de recherche, entrez `when message received sap` en guise de filtre. Dans la liste **Déclencheurs**, sélectionnez **Quand un message est reçu de SAP**.

   ![Capture d’écran montrant l’ajout d’un déclencheur SAP.](./media/logic-apps-using-sap-connector/add-sap-trigger-logic-app.png)

   Vous pouvez aussi sélectionner l’onglet **Entreprise**, puis sélectionner le déclencheur :

   ![Capture d’écran illustrant l’ajout d’un déclencheur SAP à partir de l’onglet Enterprise.](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. Si votre connexion existe déjà, passez à l’étape suivante afin de configurer votre déclencheur SAP. Toutefois, si vous êtes invité à entrer les détails de la connexion, fournissez ces informations pour pouvoir créer une connexion à votre serveur SAP local.

   1. Entrez un nom pour la connexion.

   1. Si vous utilisez la passerelle de données, procédez comme suit :

      1. Dans la section **Passerelle de données**, sous **Abonnement**, sélectionnez d’abord l’abonnement Azure pour la ressource de passerelle de données que vous avez créée dans le portail Azure pour l’installation de votre passerelle de données.

      1. Sous **Passerelle de connexion**, sélectionnez votre ressource de passerelle de données dans Azure.

   1. Continuez à fournir des informations sur la connexion. Pour la propriété **Type de connexion**, suivez l’étape selon que la propriété est définie sur **Serveur d’applications** ou sur **Groupe** :

      * Pour **Serveur d’applications**, les propriétés suivantes, qui apparaissent habituellement comme facultatives, sont obligatoires :

        ![Capture d’écran montrant la création d’une connexion au serveur d’applications SAP.](./media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Pour **Groupe**, les propriétés suivantes, qui apparaissent habituellement comme facultatives, sont obligatoires :

        ![Capture d’écran montrant la création d’une connexion au serveur de messages SAP](./media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      Par défaut, le typage fort est utilisé pour rechercher des valeurs non valides en effectuant une validation XML par rapport au schéma. Ce comportement peut vous aider à détecter les problèmes plus tôt. L’option **Types sécurisés** est disponible pour la compatibilité descendante et ne vérifie que la longueur de chaîne. Apprenez-en davantage sur l’[option Types sécurisés](#safe-typing).

   1. Quand vous avez terminé, sélectionnez **Créer**.

      Azure Logic Apps configure et teste votre connexion pour vérifier son bon fonctionnement.

1. En fonction de la configuration de votre système SAP, fournissez les [paramètres requis](#parameters) et ajoutez tous les autres paramètres disponibles pour ce déclencheur, par exemple :

   * Pour recevoir des IDocs en tant que XML brut, dans le déclencheur **Quand un message est reçu de SAP**, qui prend en charge le format Texte brut de SAP, ajoutez et définissez le paramètre **IDOC Format** sur **SapPlainXml**.

   * Pour recevoir des IDocs en tant que fichier plat à l’aide du même déclencheur SAP, ajoutez et définissez le paramètre **IDOC Format** sur **FlatFile**. Lorsque vous utilisez également l’[action Flat File Decode](logic-apps-enterprise-integration-flatfile.md), dans votre schéma de fichier plat, vous devez utiliser la propriété `early_terminate_optional_fields` et définir la valeur sur `true`.

     Cette condition est nécessaire car l’enregistrement de données IDOC de fichier plat envoyé par SAP sur l’appel tRFC `IDOC_INBOUND_ASYNCHRONOUS` n’est pas rempli à la longueur de champ SDATA complète. Azure Logic Apps fournit les données d’origine IDoc du fichier plat sans remplissage à partir de SAP. Par ailleurs, lorsque vous combinez ce déclencheur SAP avec l’action de décodage de fichier plat, le schéma fourni à l’action doit correspondre.

   * Pour [filtrer les messages que vous recevez de votre serveur SAP, spécifiez une liste d’actions SAP](#filter-with-sap-actions).

     Par exemple, sélectionnez une action SAP dans le sélecteur de fichiers :

     ![Capture d’écran illustrant l’ajout d’une action SAP au workflow d’application logique.](./media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

     Vous pouvez aussi spécifier une action manuellement :

     ![Capture d’écran qui montre la saisie manuelle de l’action SAP que vous souhaitez utiliser.](./media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png)

     Voici un exemple qui montre comment l’action apparaît quand vous configurez le déclencheur pour recevoir plusieurs messages.

     ![Capture d’écran montrant un exemple de déclencheur qui reçoit plusieurs messages.](./media/logic-apps-using-sap-connector/example-trigger.png)

     Pour plus d’informations sur l’action SAP, consultez [Schémas de message pour les opérations IDoc](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

1. Enregistrez maintenant votre workflow d’application logique pour commencer à recevoir des messages de votre système SAP. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**.

   Votre workflow d’application logique est maintenant prêt à recevoir des messages de votre système SAP.

   > [!NOTE]
   > Le déclencheur SAP dans ces étapes est un déclencheur basé sur un webhook, et non un déclencheur d’interrogation. Si vous utilisez la passerelle de données, le déclencheur est appelé depuis la passerelle de données seulement s’il existe un message : aucune interrogation n’est donc nécessaire.

1. Dans l’historique du déclencheur de votre workflow d’application logique, vérifiez que l’inscription du déclencheur a réussi.

Si vous recevez une erreur **500 Passerelle incorrecte** ou **400 Demande incorrecte** avec un message similaire à **Service 'sapgw00' inconnu**, la résolution de noms du service réseau en numéro de port échoue, par exemple :

```json
{
   "body": {
      "error": {
         "code": 500,
         "source": "EXAMPLE-FLOW-NAME.eastus.environments.microsoftazurelogicapps.net",
         "clientRequestId": "00000000-0000-0000-0000-000000000000",
         "message": "BadGateway",
         "innerError": {
            "error": {
               "code": "UnhandledException",
               "message": "\nERROR service 'sapgw00' unknown\nTIME Wed Nov 11 19:37:50 2020\nRELEASE 721\nCOMPONENT NI (network interface)\nVERSION 40\nRC -3\nMODULE ninti.c\nLINE 933\nDETAIL NiPGetServByName: 'sapgw00' not found\nSYSTEM CALL getaddrinfo\nCOUNTER 1\n\nRETURN CODE: 20"
            }
         }
      }
   }
}
```

* **Option 1 :** dans votre connexion d’API et la configuration du déclencheur, remplacez le nom de votre service de passerelle par son numéro de port. Dans l’exemple d’erreur, `sapgw00` doit être remplacé par un numéro de port réel, par exemple `3300`. Il s’agit de la seule option disponible pour l’environnement ISE.

* **Option 2 :** si vous utilisez la passerelle de données locale, vous pouvez ajouter le nom du service de passerelle au mappage de port dans `%windir%\System32\drivers\etc\services` puis redémarrer le service de passerelle de données locale, par exemple :

  ```text
  sapgw00  3300/tcp
  ```

Vous pouvez obtenir une erreur similaire lorsque le nom du serveur d’applications ou du serveur de messages SAP correspond à l’adresse IP. Pour l’environnement ISE, vous devez spécifier l’adresse IP du serveur d’applications ou du serveur de messages SAP. Pour la passerelle de données locale, vous pouvez ajouter le nom au mappage d’adresses IP dans `%windir%\System32\drivers\etc\hosts`, par exemple :

```text
10.0.1.9 SAPDBSERVER01 # SAP System Server VPN IP by computer name
10.0.1.9 SAPDBSERVER01.someguid.xx.xxxxxxx.cloudapp.net # SAP System Server VPN IP by fully qualified computer name
```

### <a name="parameters"></a>Paramètres

Avec les entrées de chaîne et de nombre simples, le connecteur SAP accepte les paramètres de table suivants (entrées `Type=ITAB`) :

* Paramètres de direction de table, d’entrée et de sortie, pour les versions SAP antérieures.

* Modification des paramètres, qui remplacent les paramètres de direction de table pour les versions SAP plus récentes.

* Paramètres de table hiérarchique

### <a name="filter-with-sap-actions"></a>Filtrer avec des actions SAP

Vous pouvez éventuellement filtrer les messages que votre workflow d’application logique reçoit de votre serveur SAP en fournissant une liste, ou un tableau, avec une ou plusieurs actions SAP. Par défaut, ce tableau est vide, ce qui signifie que votre application logique reçoit tous les messages de votre serveur SAP sans filtrage.

Quand vous configurez le filtre du tableau, le déclencheur reçoit uniquement les messages des types d’action SAP spécifiés et rejette tous les autres messages de votre serveur SAP. Toutefois, ce filtre n’a aucune incidence sur le fait que la saisie de la charge utile reçue soit faible ou forte.

Tout filtrage avec actions SAP se produit au niveau de l’adaptateur SAP pour votre passerelle de données locale. Pour plus d’informations, consultez la section [Comment envoyer des IDocs de test à Azure Logic Apps à partir de SAP](#test-sending-idocs-from-sap).

Si vous ne pouvez pas envoyer de paquets IDoc de SAP vers le déclencheur de votre workflow d’application logique, consultez le message de rejet d’appel du RFC transactionnel (tRFC) dans la boîte de dialogue SAP tRFC (T-Code SM58). Dans l’interface SAP, vous pouvez recevoir les messages d’erreur suivants, qui sont découpés en raison des limites de sous-chaîne dans le champ **Texte d’état**.

#### <a name="the-requestcontext-on-the-ireplychannel-was-closed-without-a-reply-being-sent"></a>Le RequestContext sur le IReplyChannel a été fermé sans qu’une réponse ne soit envoyée

Ce message d’erreur signifie que des échecs inattendus se produisent lorsque le gestionnaire catch-all du canal arrête le canal en raison d’une erreur, puis reconstruit le canal pour traiter d’autres messages.

Pour confirmer que votre workflow d’application logique a reçu l’IDoc, [ajoutez une action de réponse](../connectors/connectors-native-reqres.md#add-a-response-action) qui renvoie un code d’état `200 OK`. Laissez le corps vide et ne modifiez pas ou n’ajoutez pas les en-têtes. L’Idoc est transporté via tRFC, ce qui ne permet pas une charge utile de réponse.

Pour rejeter le IDoc à la place, répondez avec n’importe quel code d’état HTTP autre que `200 OK`. L’adaptateur SAP renvoie ensuite une exception à SAP en votre nom. Vous devez uniquement rejeter le IDoc pour signaler les erreurs de transport à SAP, par exemple un IDoc non routé que votre application ne peut pas traiter. Vous ne devez pas rejeter un IDoc pour les erreurs au niveau de l’application, telles que les problèmes liés aux données contenues dans l’IDoc. Si vous retardez l’acceptation du transport pour la validation au niveau de l’application, vous pouvez constater des performances négatives en raison du blocage de votre connexion du transport d’autres IDoc.

Si vous recevez ce message d’erreur et que vous rencontrez des défaillances système en appelant Azure Logic Apps, vérifiez que vous avez configuré les paramètres réseau pour votre service de passerelle de données locale pour votre environnement spécifique. Par exemple, si votre environnement réseau requiert l’utilisation d’un proxy pour appeler des points de terminaison Azure, vous devez configurer votre service de passerelle de données locale pour qu’il utilise votre proxy. Pour plus d’informations, consultez [Configuration du proxy](/dotnet/framework/network-programming/proxy-configuration).

Si vous recevez ce message d’erreur et que vous rencontrez des défaillances intermittentes en appelant Azure Logic Apps, vous devrez peut-être augmenter le nombre de tentatives ou l’intervalle avant nouvelle tentative.

1. Vérifiez les paramètres SAP dans votre fichier de configuration de service de passerelle de données locale, `Microsoft.PowerBI.EnterpriseGateway.exe.config`.

   Le paramètre nombre de nouvelles tentatives ressemble à `WebhookRetryMaximumCount="2"`. Le paramètre intervalle avant nouvelle tentative ressemble à `WebhookRetryDefaultDelay="00:00:00.10"` et le format de TimeSpan est `HH:mm:ss.ff`.

1. Enregistrez vos modifications. Redémarrez votre passerelle de données locale.

#### <a name="the-segment-or-group-definition-e2edk36001-was-not-found-in-the-idoc-meta"></a>Le segment ou la définition de groupe E2EDK36001 est introuvable dans le métavolume IDoc

Ce message d’erreur signifie que les échecs attendus se produisent avec d’autres erreurs. Par exemple, l’échec de génération d’une charge utile XML IDoc, car ses segments ne sont pas libérés par SAP. Par conséquent, les métadonnées de type de segment requises pour la conversion sont manquantes.

Pour que ces segments soient diffusés par SAP, contactez l’ingénieur ABAP pour votre système SAP.

### <a name="asynchronous-request-reply-for-triggers"></a>Requête-réponse asynchrone pour les déclencheurs

Le connecteur SAP prend en charge le [modèle requête-réponse asynchrone](/azure/architecture/patterns/async-request-reply) d’Azure pour les déclencheurs Azure Logic Apps. Vous pouvez utiliser ce modèle pour créer des requêtes réussies qui auraient échoué avec le modèle de requête-réponse synchrone par défaut.

> [!TIP]
> Dans les workflows d’applications logiques avec plusieurs actions de réponse, toutes les actions de réponse doivent utiliser le même modèle requête-réponse. Par exemple, si votre workflow d’application logique utilise un contrôle commutateur avec plusieurs actions de réponse possibles, vous devez configurer toutes les actions de réponse pour qu’elles utilisent le même modèle requête-réponse, qu’il soit synchrone ou asynchrone.

Si vous activez la réponse asynchrone pour votre action de réponse, votre workflow d’application logique peut répondre avec une réponse `202 Accepted` après avoir accepté une demande de traitement. La réponse contient un en-tête d’emplacement que vous pouvez utiliser pour récupérer l’état final de votre requête.

Pour configurer un modèle requête-réponse asynchrone pour votre workflow d’application logique à l’aide du connecteur SAP, procédez comme suit :

1. Ouvrez votre application logique dans le concepteur de workflow.

1. Vérifiez que le connecteur SAP est le déclencheur de votre workflow d’application logique.

1. Ouvrez l’action **Réponse** de votre workflow d’application logique. Dans la barre de titre de l’action, sélectionnez le menu ( **...** ) &gt; **Paramètres**.

1. Dans les **Paramètres** de votre action de réponse, activez le bouton bascule sous **Réponse asynchrone**. Sélectionnez Terminé.

1. Enregistrez les changements apportés à votre workflow d’application logique.

## <a name="find-extended-error-logs"></a>Rechercher des journaux d’erreurs étendus

Pour obtenir les messages d’erreur complets, consultez les journaux étendus de votre adaptateur SAP. Vous pouvez également [activer un fichier journal étendu pour le connecteur SAP](#extended-sap-logging-in-on-premises-data-gateway).

* Pour les versions de la passerelle de données locale d’avril 2020 et versions antérieures, les journaux sont désactivés par défaut.

* Pour les versions de la passerelle de données locale à partir de juin 2020, vous pouvez [activer les journaux de la passerelle dans les paramètres de l’application](/data-integration/gateway/service-gateway-tshoot#collect-logs-from-the-on-premises-data-gateway-app).

  * Le niveau de journalisation par défaut est **Avertissement**.

  * Si vous activez **Journalisation supplémentaire** dans les paramètres de **Diagnostic** de l'application passerelle de données locale, le niveau de journalisation passe à **Informationnel**.

  * Pour passer au niveau de journalisation **Détaillé**, mettez à jour le paramètre suivant dans votre fichier de configuration. En règle générale, le fichier de configuration se trouve sous `C:\Program Files\On-premises data gateway\Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config`.

    ```xml
    <setting name="SapTraceLevel" serializeAs="String">
       <value>Verbose</value>
    </setting>
    ```

### <a name="extended-sap-logging-in-on-premises-data-gateway"></a>Journalisation SAP étendue dans la passerelle de données locale

Si vous utilisez une [passerelle de données locale pour Azure Logic Apps](logic-apps-gateway-install.md), vous pouvez configurer un fichier journal étendu pour le connecteur SAP. Vous pouvez utiliser votre passerelle de données locale pour rediriger les événements ETW (Suivi d’événements pour Windows) dans des fichiers journaux avec rotation qui sont inclus dans les fichiers .zip de journalisation de votre passerelle.

Vous pouvez [exporter tous les journaux de configuration et de service de votre passerelle](/data-integration/gateway/service-gateway-tshoot#collect-logs-from-the-on-premises-data-gateway-app) vers un fichier .zip à partir des paramètres de l’application de passerelle.

> [!NOTE]
> La journalisation étendue peut affecter les performances de vos workflows d’applications logiques quand elle est activée en continu. Il est recommandé de désactiver les fichiers journaux étendus une fois que vous avez terminé l’analyse et le dépannage d’un problème.

#### <a name="capture-etw-events"></a>Capturer des événements ETW

Les utilisateurs avancés peuvent éventuellement capturer des événements ETW directement. Vous pouvez ensuite [consommer vos données dans Diagnostics Azure dans Event Hubs](../azure-monitor/agents/diagnostics-extension-stream-event-hubs.md) ou [collecter vos données dans les journaux Azure Monitor](../azure-monitor/agents/diagnostics-extension-logs.md). Pour plus d’informations, consultez les [meilleures pratiques pour collecter et stocker des données](/azure/architecture/best-practices/monitoring#collecting-and-storing-data). Vous pouvez utiliser [PerfView](https://github.com/Microsoft/perfview/blob/master/README.md) pour travailler avec les fichiers ETL résultants, ou vous pouvez écrire votre propre programme. Cette procédure pas à pas utilise PerfView :

1. Dans le menu PerfView, sélectionnez **Collecter** &gt; **Collecter** pour capturer les événements.

1. Dans le champ **Fournisseur supplémentaire**, entrez `*Microsoft-LobAdapter` pour indiquer au fournisseur SAP de capturer les événements de l’adaptateur SAP. Si vous ne spécifiez pas ces informations, votre suivi n’inclura que les événements ETW généraux.

1. Conservez tous les autres paramètres par défaut. Si vous le souhaitez, vous pouvez modifier le nom ou l’emplacement du fichier dans le champ **Fichier de données**.

1. Sélectionnez **Démarrer la collecte** pour commencer votre suivi.

1. Une fois que vous avez reproduit votre problème ou collecté suffisamment de données d’analyse, sélectionnez **Arrêter la collecte**.

1. Pour partager vos données avec un tiers, comme les ingénieurs du support Azure, compressez le fichier ETL.

1. Pour afficher le contenu de votre suivi :

   1. Dans PerfView, sélectionnez **Fichier** &gt; **Ouvrir** et sélectionnez le fichier ETL que vous venez de générer.

   1. Dans la barre latérale PerfView, la section **Événements** sous votre fichier ETL.

   1. Sous **Filtrer**, filtrez par `Microsoft-LobAdapter` pour afficher uniquement les événements pertinents et les processus de la passerelle.

### <a name="test-your-workflow"></a>Tester votre workflow

1. Pour déclencher votre workflow d’application logique, envoyez un message depuis votre système SAP.

1. Dans le menu de l’application logique, sélectionnez **Vue d’ensemble**. Examinez **Historique des exécutions** pour identifier toutes les nouvelles exécutions de votre workflow d’application logique.

1. Ouvrez la dernière exécution, qui montre le message envoyé depuis votre système SAP dans la section des sorties du déclencheur.

### <a name="test-sending-idocs-from-sap"></a>Test d’envoi d’Idocs à partir de SAP

Pour envoyer des IDocs de SAP à votre workflow d’application logique, vous avez besoin de la configuration minimale suivante :

> [!IMPORTANT]
> Utilisez cette procédure uniquement lorsque vous testez votre configuration SAP avec votre workflow d’application logique. Les environnements de production nécessitent une configuration supplémentaire.

1. [Configurez une destination RFC dans SAP.](#create-rfc-destination)

1. [Créez une connexion ABAP à votre destination RFC.](#create-abap-connection)

1. [Créez un port de réception.](#create-receiver-port)

1. [Créez un port d’envoi.](#create-sender-port)

1. [Créez un partenaire de système logique.](#create-logical-system-partner)

1. [Créez un profil partenaire.](#create-partner-profiles)

1. [Testez l’envoi de messages.](#test-sending-messages)

#### <a name="create-rfc-destination"></a>Créer une destination RFC

1. Pour ouvrir les paramètres **Configuration des connexions RFC**, dans votre interface SAP, utilisez le code de transaction (T-Code) **sm59** avec le préfixe **/n**.

1. Sélectionnez **Connexions TCP/IP** > **Créer**.

1. Créez une destination RFC avec les paramètres suivants :

    1. Pour votre **Destination RFC**, entrez un nom.

    1. Sous l’onglet **Paramètres techniques**, pour **Type d’activation**, sélectionnez **Programme de serveur inscrit**.

    1. Pour votre **ID de programme**, entrez une valeur. Dans SAP, le déclencheur de votre workflow d’application logique est enregistré à l’aide de cet identificateur.

       > [!IMPORTANT]
       > **L’ID de programme** SAP respecte la casse. Veillez à utiliser systématiquement le même format de casse pour votre **ID de programme** lorsque vous configurez votre workflow d’application logique et votre serveur SAP. Dans le cas contraire, vous risquez de recevoir les erreurs suivantes dans le moniteur tRFC (T-Code SM58) lorsque vous tentez d’envoyer un IDoc à SAP :
       >
       > * **Fonction IDOC_INBOUND_ASYNCHRONOUS introuvable**
       > * **Client RFC non ABAP (type de partenaire) non pris en charge**
       >
       > Pour plus d’informations de SAP, consultez les notes suivantes (connexion obligatoire) :
       >
       > * [https://launchpad.support.sap.com/#/notes/2399329](https://launchpad.support.sap.com/#/notes/2399329)
       > * [https://launchpad.support.sap.com/#/notes/353597](https://launchpad.support.sap.com/#/notes/353597)

    1. Sous l’onglet **Unicode**, pour **Type de communication avec le système cible**, sélectionnez **Unicode**.

       > [!NOTE]
       > Les bibliothèques clientes SAP .NET prennent uniquement en charge le codage de caractères Unicode. Si l’erreur `Non-ABAP RFC client (partner type ) not supported` s’affiche lors de l’envoi du IDOC de SAP à Azure Logic Apps, vérifiez que la valeur **Type de communication avec le système cible** est définie sur **Unicode**.

1. Enregistrez vos modifications.

1. Enregistrez votre nouvel **ID de programme** avec Azure Logic Apps en créant un workflow d’application logique qui commence par le déclencheur SAP nommé **Quand un message est reçu de SAP**.

   Ainsi, lorsque vous enregistrez votre workflow, Azure Logic Apps inscrit l’**ID de programme** sur la passerelle SAP.

1. Dans l’historique du déclencheur de votre workflow, les journaux de l’adaptateur SAP de la passerelle de données locale et les journaux de suivi de la passerelle SAP, vérifiez l’état de l’inscription. Dans la boîte de dialogue SAP Gateway monitor (Moniteur de passerelle) (T-Code SMGW), sous **Logged-On Clients (Clients connectés)** , la nouvelle inscription doit apparaître avec la dénomination **Registered Server (Serveur inscrit)** .

1. Pour tester votre connexion, dans l’interface SAP, sous votre nouvelle **Destination RFC**, sélectionnez **Test de connexion**.

#### <a name="create-abap-connection"></a>Créer une connexion ABAP

1. Pour ouvrir les paramètres **Configuration des connexions RFC**, dans votre interface SAP, utilisez le code de transaction (T-Code) **sm59** _ avec le préfixe _ */n**.

1. Sélectionnez **Connexions ABAP** > **Créer**.

1. Pour **Destination RFC**, entrez l’identificateur de [votre système SAP de test](#create-rfc-destination).

1. Enregistrez vos modifications.

1. Pour tester votre connexion, sélectionnez **Test de la connexion**.

#### <a name="create-receiver-port"></a>Créer un port de réception

1. Pour ouvrir les paramètres **Ports pour le traitement des IDocs**, dans votre interface SAP, utilisez le code de transaction (T-Code) **we21** avec le préfixe **/n**.

1. Sélectionnez **Ports** > **RFC transactionnel** > **créer**.

1. Dans la boîte de dialogue de paramètres qui s’ouvre, sélectionnez **Nom de port personnel**. Entrez un **Nom** pour votre port de test. Enregistrez vos modifications.

1. Dans les paramètres de votre nouveau port de réception, pour **Destination RFC**, entrez l’identificateur pour [votre destination RFC de test](#create-rfc-destination).

1. Enregistrez vos modifications.

#### <a name="create-sender-port"></a>Créer un port d’envoi

1. Pour ouvrir les paramètres **Ports pour le traitement des IDocs**, dans votre interface SAP, utilisez le code de transaction (T-Code) **we21** avec le préfixe **/n**.

1. Sélectionnez **Ports** > **RFC transactionnel** > **créer**.

1. Dans la boîte de dialogue de paramètres qui s’ouvre, sélectionnez **Nom de port personnel**. Pour votre port de test, entrez un **Nom** qui commence par **SAP**. Tous les noms des ports d’envoi doivent commencer par les lettres **SAP**, par exemple, **SAPTEST**. Enregistrez vos modifications.

1. Dans les paramètres de votre nouveau port d’envoi, pour **Destination RFC**, entrez l’identificateur de [votre connexion ABAP](#create-abap-connection).

1. Enregistrez vos modifications.

#### <a name="create-logical-system-partner"></a>Créer un partenaire de système logique

1. Pour ouvrir les paramètres **Modifier la vue « Systèmes logiques » : Vue d’ensemble**, dans votre interface SAP, utilisez le code de transaction (T-code) **bd54**.

1. Acceptez le message d’avertissement qui s’affiche : **Attention : La table est un client croisé**

1. Au-dessus de la liste qui affiche vos systèmes logiques existants, sélectionnez **Nouvelles entrées**.

1. Pour votre nouveau système logique, entrez un identificateur **Log.System** et une brève description du **Nom**. Enregistrez vos modifications.

1. Lorsque l’**Invite pour Workbench** s’affiche, créez une nouvelle requête en fournissant une description ou, si vous avez déjà créé une requête, ignorez cette étape.

1. Une fois la requête Workbench créée, liez cette requête à la requête de mise à jour de la table. Pour confirmer la mise à jour de votre table, enregistrez vos modifications.

#### <a name="create-partner-profiles"></a>Créer des profils partenaires

Pour les environnements de production, vous devez créer deux profils partenaires. Le premier profil est destiné à l’expéditeur, qui est votre organisation et le système SAP. Le deuxième profil est pour le destinataire, qui est votre application logique.

1. Pour ouvrir les paramètres **Profils partenaires**, dans votre interface SAP, utilisez le code de transaction (T-Code) **we20** avec le préfixe **/n**.

1. Sous **Profils partenaires**, sélectionnez **Type de partenaire LS** > **Créer**.

1. Créez un profil partenaire avec les paramètres suivants :

    * Pour **N° partenaire**, entrez [l’identificateur de votre partenaire de système logique](#create-logical-system-partner).

    * Pour **Type de partenaire**, entrez **LS**.

    * Pour **Agent**, entrez l’identificateur du compte d’utilisateur SAP à utiliser lorsque vous enregistrez des identificateurs de programme pour Azure Logic Apps ou d’autres systèmes non SAP.

1. Enregistrez vos modifications. Si vous n’avez pas [créé le partenaire de système logique](#create-logical-system-partner), vous obtenez l’erreur **Entrez un numéro de partenaire valide**.

1. Dans les paramètres de votre profil partenaire, sous **Paramètres sortants**, sélectionnez **Créer un paramètre sortant**.

1. Créez un nouveau paramètre sortant avec les paramètres suivants :

    * Entrez votre **Type de message**, par exemple, **CREMAS**.

    * Entrez [l’identificateur de votre port de réception](#create-receiver-port).

    * Entrez une taille d’Idoc pour **Pack - Taille**. Ou, pour [envoyer des Idocs un par un à partir de SAP](#receive-idoc-packets-from-sap), sélectionnez **Transmettre IDoc immédiatement**.

1. Enregistrez vos modifications.

#### <a name="test-sending-messages"></a>Tester l’envoi de messages

1. Pour ouvrir les paramètres **Outil de test pour le traitement des IDocs**, dans votre interface SAP, utilisez le code de transaction (T-Code) **we19** avec le préfixe **/n**.

1. Sous **Modèle de test**, sélectionnez **Via le type de message**, et entrez votre type de message, par exemple **CREMAS**. Sélectionnez **Create** (Créer).

1. Confirmez le message **Quel type d’Idoc ?** en sélectionnant **Continuer**.

1. Sélectionnez le nœud **EDIDC**. Entrez les valeurs appropriées pour les ports d’envoi et de réception. Sélectionnez **Continuer**.

1. Sélectionnez **Traitement sortant standard**.

1. Pour démarrer le traitement d’Idocs sortants, sélectionnez **Continuer**. Une fois le traitement terminé, le message **IDoc envoyé au système SAP ou au programme externe** s’affiche.

1. Pour rechercher les erreurs de traitement, utilisez le code de transaction (T-Code) **sm58** avec le préfixe **/n**.

## <a name="receive-idoc-packets-from-sap"></a>Recevoir des paquets d’IDocs de SAP

Vous pouvez configurer SAP pour [envoyer des IDocs sous forme de paquets](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/4ab38886549a6d8ce10000000a42189c.html), à savoir sous forme de lots ou de groupes d’IDocs. Pour recevoir des paquets d’IDocs, le connecteur SAP, et plus particulièrement le déclencheur, ne nécessite aucune configuration supplémentaire. Toutefois, pour traiter chacun des éléments d’un paquet d’IDocs après la réception du paquet par le déclencheur, des étapes supplémentaires sont nécessaires afin de fractionner le paquet en IDocs individuels.

Voici un exemple montrant comment extraire des IDocs individuels d’un paquet à l’aide de la [fonction `xpath()`](./workflow-definition-language-functions-reference.md#xpath) :

1. Avant de commencer, vous devez disposer d’un workflow d’application logique avec un déclencheur SAP. Si votre workflow d’application logique ne comporte pas déjà ce déclencheur, suivez les étapes précédentes de cette rubrique pour [configurer un workflow d’application logique avec un déclencheur SAP](#receive-message-from-sap).

   > [!IMPORTANT]
   > **L’ID de programme** SAP respecte la casse. Veillez à utiliser systématiquement le même format de casse pour votre **ID de programme** lorsque vous configurez votre workflow d’application logique et votre serveur SAP. Dans le cas contraire, vous risquez de recevoir les erreurs suivantes dans le moniteur tRFC (T-Code SM58) lorsque vous tentez d’envoyer un IDoc à SAP :
   >
   > * **Fonction IDOC_INBOUND_ASYNCHRONOUS introuvable**
   > * **Client RFC non ABAP (type de partenaire) non pris en charge**
   >
   > Pour plus d’informations de SAP, consultez les notes suivantes (connexion obligatoire) : [https://launchpad.support.sap.com/#/notes/2399329](https://launchpad.support.sap.com/#/notes/2399329) et [https://launchpad.support.sap.com/#/notes/353597](https://launchpad.support.sap.com/#/notes/353597).

   Par exemple :

   ![Capture d’écran illustrant l’ajout d’un déclencheur SAP au workflow d’application logique.](./media/logic-apps-using-sap-connector/first-step-trigger.png)

1. [Ajoutez une action de réponse à votre workflow d’application logique](../connectors/connectors-native-reqres.md#add-a-response-action) pour répondre immédiatement à l’état de votre demande SAP. Il est recommandé d’ajouter cette action juste après votre déclencheur, pour libérer le canal de communication avec votre serveur SAP. Choisissez l’un des codes de statut suivants (`statusCode`) à utiliser dans votre action de réponse :

    * **202 Accepté**, qui signifie que la demande a été acceptée pour traitement, mais que celui-ci n’a pas encore été effectué.

    * **204 Aucun contenu**, qui signifie que le serveur a bien répondu à la demande et qu’il n’y a pas de contenu supplémentaire à envoyer dans le corps de la charge utile de la réponse. 

    * **200 OK**. Ce code de statut contient toujours une charge utile, même si le serveur génère un corps de charge utile de longueur nulle. 

1. Récupérez l’espace de noms racine à partir de l’IDoc XML que votre workflow d’application logique reçoit de SAP. Pour extraire cet espace de noms du document XML, ajoutez une étape qui crée une variable de chaîne locale et stocke cet espace de noms à l'aide d'une expression `xpath()` :

   `xpath(xml(triggerBody()?['Content']), 'namespace-uri(/*)')`

   ![Capture d’écran montrant l’obtention de l’espace de noms racine à partir de IDoc.](./media/logic-apps-using-sap-connector/get-namespace.png)

1. Pour extraire un IDoc individuel, ajoutez une étape qui crée une variable de tableau et stocke la collection d’IDocs à l’aide d’une autre expression `xpath()` :

   `xpath(xml(triggerBody()?['Content']), '/*[local-name()="Receive"]/*[local-name()="idocData"]')`

   ![Capture d’écran qui montre l’obtention d’un tableau d’éléments.](./media/logic-apps-using-sap-connector/get-array.png)

   La variable de tableau rend chaque IDoc disponible pour que votre workflow d’application logique puisse les traiter individuellement en procédant à une énumération sur la collection. Dans cet exemple, le workflow d’application logique transfère chaque IDoc vers un serveur SFTP à l’aide d’une boucle :

   ![Capture d’écran qui montre l’envoi d’un IDoc à un serveur SFTP.](./media/logic-apps-using-sap-connector/loop-batch.png)

   Chaque IDoc doit inclure l’espace de noms racine, ce qui explique pourquoi le contenu du fichier est enveloppé dans un élément `<Receive></Receive>` avec l’espace de noms racine avant l’envoi de l’IDoc à l’application située en aval ou, dans ce cas, au serveur SFTP.

Vous pouvez utiliser le modèle de démarrage rapide correspondant à ce modèle en sélectionnant celui-ci dans le concepteur de workflow lorsque vous créez un nouveau workflow d’application logique.

![Capture d’écran qui montre la sélection d’un modèle d’application logique de traitement par lots.](./media/logic-apps-using-sap-connector/select-batch-logic-app-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>Générer des schémas pour les artefacts dans SAP

Cet exemple utilise un workflow d’application logique que vous pouvez déclencher à l’aide d’une requête HTTP. Pour générer les schémas pour l’IDoc et la BAPI spécifiés, l’action SAP **Générer un schéma** envoie une requête à un système SAP.

Cette action SAP renvoie un [schéma XML](#sample-xml-schemas), et non le contenu ou les données du document XML proprement dit. Les schémas renvoyés dans la réponse sont chargés sur un compte d’intégration avec le connecteur Azure Resource Manager. Les schémas contiennent les éléments suivants :

* La structure du message de la requête. Utilisez ces informations pour constituer votre liste BAPI `get`.

* La structure du message de réponse. Utilisez ces informations pour analyser la réponse.

Pour envoyer le message de la requête, utilisez l’action SAP générique **Envoyer un message à SAP** ou les actions ciblées **\[BAPI] Méthode d’appel dans SAP**.

### <a name="sample-xml-schemas"></a>Exemples de schémas XML

Si vous apprenez à générer un schéma XML à utiliser pour créer un document type, consultez les exemples suivants. Ces exemples montrent la manière dont vous pouvez travailler avec de nombreux types de charges utiles, notamment :

* [Requêtes RFC](#xml-samples-for-rfc-requests)

* [Requêtes BAPI](#xml-samples-for-bapi-requests)

* [Requêtes IDoc](#xml-samples-for-idoc-requests)

* Types de données de schéma XML simples ou complexes

* Paramètres de table

* Comportements XML facultatifs

Vous pouvez commencer votre schéma XML par un prologue XML facultatif. Le connecteur SAP fonctionne avec ou sans prologue XML.

```xml
<?xml version="1.0" encoding="utf-8">
```

#### <a name="xml-samples-for-rfc-requests"></a>Exemples XML pour les requêtes RFC

L’exemple suivant est un appel RFC de base. Le nom du RFC est `STFC_CONNECTION`. Cette requête utilise l’espace de noms par défaut `xmlns=`. Toutefois, vous pouvez attribuer et utiliser des alias d’espaces de noms tels que `xmmlns:exampleAlias=`. La valeur de l’espace de noms est l’espace de noms de tous les RFC dans SAP pour les services Microsoft. Il existe un paramètre d’entrée simple dans la requête HTTP, `<REQUTEXT>`.

```xml
<STFC_CONNECTION xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
   <REQUTEXT>exampleInput</REQUTEXT>
</STFC_CONNECTION>
```

L’exemple suivant est un appel RFC avec un paramètre de table. Cet exemple d’appel et son groupe de RFC de test sont disponibles dans le cadre de tous les systèmes SAP. Le nom du paramètre de table est `TCPICDAT`. Le type de ligne de table est `ABAPTEXT`, et cet élément se répète pour chaque ligne de la table. Cet exemple contient une seule ligne, appelée `LINE`. Les requêtes dotées d’un paramètre de table peuvent contenir un nombre quelconque de champs, où le nombre est un entier positif (*n*). 

```xml
<STFC_WRITE_TO_TCPIC xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
   <RESTART_QNAME>exampleQName</RESTART_QNAME>
   <TCPICDAT>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
         <LINE>exampleFieldInput1</LINE>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
         <LINE>exampleFieldInput2</LINE>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
         <LINE>exampleFieldInput3</LINE>
      </ABAPTEXT>
   </TCPICDAT>
</STFC_WRITE_TO_TCPIC>
```

> [!NOTE]
> Observez le résultat de **STFC_WRITE_TO_TCPIC** RFC avec l’Explorateur de données de l’ouverture de session SAP (T-Code SE16.) Utilisez le nom de la table **TCPIC**.

L’exemple suivant est un appel RFC avec un paramètre de table ayant un champ anonyme. Un champ anonyme est un champ auquel aucun nom n’a été attribué. Les types complexes sont déclarés sous un espace de noms distinct, dans lequel la déclaration définit une nouvelle valeur par défaut pour le nœud actuel et tous ses éléments enfants. L’exemple utilise le code hexadécimal `x002F` comme caractère d’échappement à la place du symbole */* , car ce dernier est réservé dans le nom de champ SAP.

```xml
<RFC_XML_TEST_1 xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
   <IM_XML_TABLE>
      <RFC_XMLCNT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
         <_x002F_AnonymousField>exampleFieldInput</_x002F_AnonymousField>
      </RFC_XMLCNT>
   </IM_XML_TABLE>
</RFC_XML_TEST_1>
```

L’exemple suivant comprend des préfixes pour les espaces de noms. Vous pouvez déclarer tous les préfixes à la fois, ou vous pouvez déclarer un nombre quelconque de préfixes comme attributs d’un nœud. L’alias d’espace de noms RFC `ns0` est utilisé comme racine et paramètres pour le type de base.

> [!NOTE]
> Les types complexes sont déclarés sous un espace de noms différent pour les types RFC avec l’alias `ns3` au lieu de l’espace de noms RFC standard avec l’alias `ns0`.

```xml
<ns0:BBP_RFC_READ_TABLE xmlns:ns0="http://Microsoft.LobServices.Sap/2007/03/Rfc/" xmlns:ns3="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc/">
   <ns0:DELIMITER>0</ns0:DELIMITER>
   <ns0:QUERY_TABLE>KNA1</ns0:QUERY_TABLE>
   <ns0:ROWCOUNT>250</ns0:ROWCOUNT>
   <ns0:ROWSKIPS>0</ns0:ROWSKIPS>
   <ns0:FIELDS>
      <ns3:RFC_DB_FLD>
         <ns3:FIELDNAME>KUNNR</ns3:FIELDNAME>
      </ns3:RFC_DB_FLD>
   </ns0:FIELDS>
</ns0:BBP_RFC_READ_TABLE>
```

#### <a name="xml-samples-for-bapi-requests"></a>Exemples XML pour les requêtes BAPI

Les exemples XML suivants sont des exemples de requêtes visant à [appeler la méthode BAPI](#actions).

> [!NOTE]
> SAP met les objets métier à la disposition des systèmes externes en les décrivant en réponse au RFC `RPY_BOR_TREE_INIT`, que Azure Logic Apps émet sans filtre d’entrée. Logic Apps inspecte la table de sortie `BOR_TREE`. Le champ `SHORT_TEXT` est utilisé pour les noms d’objets métier. Les objets métier non retournés par SAP dans la table de sortie ne sont pas accessibles à Azure Logic Apps.
> 
> Si vous utilisez des objets métier personnalisés, vous devez veiller à publier et à mettre en production ces objets métier dans SAP. Dans le cas contraire, SAP ne répertorie pas vos objets métier personnalisés dans la table de sortie `BOR_TREE`. Vous ne pouvez pas accéder à vos objets métier personnalisés dans Logic Apps tant que vous n’exposez pas les objets métier à partir de SAP. 

L’exemple suivant récupère une liste de banques à l’aide de la méthode BAPI `GETLIST`. Cet exemple contient l’objet métier d’une banque, `BUS1011`.

```xml
<GETLIST xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
   <BANK_CTRY>US</BANK_CTRY>
   <MAX_ROWS>10</MAX_ROWS>
</GETLIST>
```

L’exemple suivant crée un objet banque à l’aide de la méthode `CREATE`. Cet exemple utilise le même objet métier que l’exemple précédent, `BUS1011`. Lorsque vous utilisez la méthode `CREATE` pour créer une banque, veillez à valider vos modifications, car cette méthode n’est pas validée par défaut.

> [!TIP]
> Veillez à ce que votre document XML respecte les règles de validation configurées dans votre système SAP. Par exemple, dans cet exemple de document, la clé de la banque (`<BANK_KEY>`) doit être un numéro de routage bancaire, également appelé « numéro ABA » aux États-Unis.

```xml
<CREATE xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_ADDRESS>
    <BANK_NAME xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleBankName</BANK_NAME>
    <REGION xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleRegionName</REGION>
    <STREET xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleStreetAddress</STREET>
    <CITY xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">Redmond</CITY>
  </BANK_ADDRESS>
  <BANK_COUNTRY>US</BANK_COUNTRY>
  <BANK_KEY>123456789</BANK_KEY>
</CREATE>
```

L’exemple suivant récupère les détails d’une banque en utilisant le numéro de routage bancaire, soit la valeur de `<BANK_KEY>`. 

```xml
<GETDETAIL xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_COUNTRY>US</BANK_COUNTRY>
  <BANK_KEY>123456789</BANK_KEY>
</GETDETAIL>
```

#### <a name="xml-samples-for-idoc-requests"></a>Exemples XML pour les requêtes IDoc

Pour générer un schéma XML IDoc SAP simple, utilisez l’application **SAP Logon** et le T-Code `WE-60`. Accédez à la documentation SAP par le biais de l’interface graphique utilisateur et générez des schémas XML au format XSD pour vos extensions et types IDoc. Pour obtenir une explication des formats et charges utiles SAP génériques, ainsi que de leurs boîtes de dialogue intégrées, consultez la [documentation SAP](https://help.sap.com/viewer/index).

Cet exemple déclare le nœud racine et les espaces de noms. L’URI de l’exemple de code, `http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700/Send`, déclare la configuration suivante :

* `/IDoc` est le nœud racine pour tous les IDocs.

* `/3` est la version des types d’enregistrement pour les définitions communes de segments.

* `/ORDERS05` est le type IDoc.

* `//` est un segment vide, car il n’existe pas d’extension IDoc.

* `/700` est la version SAP.

* `/Send` est l’action permettant d’envoyer les informations à SAP.

```xml
<ns0:Send xmlns:ns0="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700/Send" xmlns:ns3="http://schemas.microsoft.com/2003/10/Serialization" xmlns:ns1="http://Microsoft.LobServices.Sap/2007/03/Types/Idoc/Common/" xmlns:ns2="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700">
   <ns0:idocData>
```

Vous pouvez répéter le nœud `idocData` pour envoyer un lot d’IDocs en un seul appel. Dans l’exemple ci-dessous, il existe un enregistrement de contrôle, `EDI_DC40`, et plusieurs enregistrements de données.

```xml
<...>
  <ns0:idocData>
    <ns2:EDI_DC40>
      <ns1:TABNAM>EDI_DC40</ns1:TABNAM>
      <...>
      <ns1:ARCKEY>Cor1908207-5</ns1:ARCKEY>
    </ns2:EDI_DC40>
    <ns2:E2EDK01005>
      <ns2:DATAHEADERCOLUMN_SEGNAM>E23DK01005</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:CURCY>USD</ns2:CURCY>
    </ns2:E2EDK01005>
    <ns2:E2EDK03>
    <...>
  </ns0:idocData>
```

L’exemple suivant est un exemple d’enregistrement de contrôle IDoc, qui utilise le préfixe `EDI_DC`. Vous devez mettre à jour les valeurs pour qu’elles correspondent à votre installation SAP et au type IDoc. Par exemple, votre code client IDoc n’est peut-être pas `800`. Contactez votre équipe SAP pour vous assurer que vous utilisez les valeurs correctes pour votre installation SAP.

```xml
<ns2:EDI_DC40>
  <ns:TABNAM>EDI_DC40</ns1:TABNAM>
  <ns:MANDT>800</ns1:MANDT>
  <ns:DIRECT>2</ns1:DIRECT>
  <ns:IDOCTYP>ORDERS05</ns1:IDOCTYP>
  <ns:CIMTYP></ns1:CIMTYP>
  <ns:MESTYP>ORDERS</ns1:MESTYP>
  <ns:STD>X</ns1:STD>
  <ns:STDVRS>004010</ns1:STDVRS>
  <ns:STDMES></ns1:STDMES>
  <ns:SNDPOR>SAPENI</ns1:SNDPOR>
  <ns:SNDPRT>LS</ns1:SNDPRT>
  <ns:SNDPFC>AG</ns1:SNDPFC>
  <ns:SNDPRN>ABAP1PXP1</ns1:SNDPRN>
  <ns:SNDLAD></ns1:SNDLAD>
  <ns:RCVPOR>BTSFILE</ns1:RCVPOR>
  <ns:RCVPRT>LI</ns1:RCVPRT>
```

L’exemple suivant est un exemple d’enregistrement de données avec des segments ordinaires. Cet exemple utilise le format de date SAP. Les documents fortement typés peuvent utiliser des formats de date XML natifs, tels que `2020-12-31 23:59:59`.

```xml
<ns2:E2EDK01005>
  <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDK01005</ns2:DATAHEADERCOLUMN_SEGNAM>
    <ns2:CURCY>USD</ns2:CURCY>
    <ns2:BSART>OR</ns2:BSART>
    <ns2:BELNR>1908207-5</ns2:BELNR>
    <ns2:ABLAD>CC</ns2:ABLAD>
  </ns2>
  <ns2:E2EDK03>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDK03</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:IDDAT>002</ns2:IDDAT>
      <ns2:DATUM>20160611</ns2:DATUM>
  </ns2:E2EDK03>
```

L’exemple suivant est un enregistrement de données avec des segments groupés. L’enregistrement inclut un nœud parent de groupe, `E2EDKT1002GRP`, et plusieurs nœuds enfants, notamment `E2EDKT1002` et `E2EDKT2001`. 

```xml
<ns2:E2EDKT1002GRP>
  <ns2:E2EDKT1002>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDKT1002</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:TDID>ZONE</ns2:TDID>
  </ns2:E2EDKT1002>
  <ns2:E2EDKT2001>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDKT2001</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:TDLINE>CRSD</ns2:TDLINE>
  </ns2:E2EDKT2001>
</ns2:E2EDKT1002GRP>
```

La méthode recommandée consiste à créer un identificateur IDoc à utiliser avec tRFC. Vous pouvez définir cet identificateur de transaction, `tid`, à l’aide de l’[opération Envoyer un IDoc](/connectors/sap/#send-idoc) dans l’API du connecteur SAP.

L’exemple suivant est une méthode alternative pour définir l’identificateur de transaction, ou `tid`. Dans cet exemple, le dernier nœud de segment d’enregistrement de données et le nœud de données IDoc sont fermés. Ensuite, l’identificateur unique, `guid`, est utilisé comme identificateur tRFC pour détecter les doublons.

```xml
     </E2STZUM002GRP>
  </idocData>
  <guid>8820ea40-5825-4b2f-ac3c-b83adc34321c</guid>
</Send>
```

### <a name="add-the-request-trigger"></a>Ajout du déclencheur Request

1. Dans le portail Azure, créez une application logique vide, ce qui ouvre le concepteur de workflow.

1. Dans la zone de recherche, entrez `http request` en guise de filtre. Dans la liste **Déclencheurs**, sélectionnez **Lors de la réception d’une requête HTTP**.

   ![Capture d’écran illustrant l’ajout du déclencheur de requête.](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Enregistrez maintenant votre application logique pour pouvoir générer une URL de point de terminaison pour votre workflow d’application logique. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**.

   L’URL du point de terminaison s’affiche désormais dans votre déclencheur, par exemple :

   ![Capture d’écran montrant la génération de l’URL de point de terminaison.](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>Ajouter une action SAP pour générer des schémas

1. Dans le concepteur de workflow, sous le déclencheur, sélectionnez **Nouvelle étape**.

   ![Capture d’écran illustrant l’ajout d’une nouvelle étape au workflow d’application logique.](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. Dans la zone de recherche, entrez `generate schemas sap` en guise de filtre. Dans la liste **Actions**, sélectionnez **Générer des schémas**.
  
   ![Capture d’écran qui montre comment ajouter l’action « Générer des schémas » au workflow.](./media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   Vous pouvez aussi sélectionner l’onglet **Entreprise** et sélectionner l’action SAP.

   ![Capture d’écran qui montre la sélection de l’action « Générer des schémas » dans l’onglet Enterprise.](./media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. Si votre connexion existe déjà, passez à l’étape suivante afin de configurer votre action SAP. Toutefois, si vous êtes invité à entrer les détails de la connexion, fournissez ces informations pour pouvoir créer une connexion à votre serveur SAP local.

   1. Entrez un nom pour la connexion.

   1. Dans la section **Passerelle de données**, sous **Abonnement**, sélectionnez d’abord l’abonnement Azure pour la ressource de passerelle de données que vous avez créée dans le portail Azure pour l’installation de votre passerelle de données. 

   1. Sous **Passerelle de connexion**, sélectionnez votre ressource de passerelle de données dans Azure.

   1. Continuez à fournir des informations sur la connexion. Pour la propriété **Type de connexion**, suivez l’étape selon que la propriété est définie sur **Serveur d’applications** ou sur **Groupe** :

      * Pour **Serveur d’applications**, les propriétés suivantes, qui apparaissent habituellement comme facultatives, sont obligatoires :

        ![Capture d’écran montrant la création d’une connexion au serveur d’applications SAP](./media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Pour **Groupe**, les propriétés suivantes, qui apparaissent habituellement comme facultatives, sont obligatoires :

        ![Capture d’écran montrant la création d’une connexion au serveur de messages SAP](./media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

   1. Quand vous avez terminé, sélectionnez **Créer**.

      Azure Logic Apps configure et teste votre connexion pour vérifier son bon fonctionnement.

1. Indiquez le chemin de l’artefact pour lequel vous voulez générer le schéma.

   Vous pouvez sélectionner l’action SAP dans le sélecteur de fichiers :

   ![Capture d’écran montrant la sélection d’une action SAP.](./media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   Vous pouvez aussi entrer l’action manuellement :

   ![Capture d’écran montrant comment entrer manuellement une action SAP.](./media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   Pour générer des schémas pour plusieurs artefacts, spécifiez les détails de l’action SAP pour chaque artefact, par exemple :

   ![Capture d’écran montrant la sélection de l’option Ajouter un nouvel élément.](./media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![Capture d’écran montrant deux éléments.](./media/logic-apps-using-sap-connector/schema-generator-example.png)

   Pour plus d’informations sur l’action SAP, consultez [Schémas de message pour les opérations IDoc](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

1. Enregistrez votre workflow d’application logique. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**.

Par défaut, le typage fort est utilisé pour rechercher des valeurs non valides en effectuant une validation XML par rapport au schéma. Ce comportement peut vous aider à détecter les problèmes plus tôt. L’option **Types sécurisés** est disponible pour la compatibilité descendante et ne vérifie que la longueur de chaîne. Apprenez-en davantage sur l’[option Types sécurisés](#safe-typing).

### <a name="test-your-workflow"></a>Tester votre workflow

1. Dans la barre d’outils du concepteur, sélectionnez **Exécuter** pour déclencher une exécution de votre workflow d’application logique.

1. Ouvrez l’exécution et vérifiez les résultats pour l’action **Générer des schémas**.

   Les sorties montrent les schémas générés pour la liste de messages spécifiée.

### <a name="upload-schemas-to-an-integration-account"></a>Charger des schémas sur un compte d’intégration

Vous pouvez aussi télécharger ou stocker les schémas générés dans des référentiels, comme un objet blob, un stockage ou un compte d’intégration. Les comptes d’intégration offrent une expérience privilégiée avec d’autres actions XML : cet exemple montre donc comment charger des schémas dans un compte d’intégration pour le même workflow d’application logique avec le connecteur Azure Resource Manager.

1. Dans le concepteur de workflow, sous le déclencheur, sélectionnez **Nouvelle étape**.

1. Dans la zone de recherche, entrez `resource manager` en guise de filtre. Sélectionnez **Créer ou mettre à jour une ressource**.

   ![Capture d’écran qui montre comment sélectionner une action Azure Resource Manager.](./media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. Entrez les détails de l’action, notamment votre abonnement Azure, le groupe de ressources Azure et le compte d’intégration. Pour ajouter des jetons SAP aux champs, cliquez dans les zones pour ces champs, puis sélectionnez dans la liste de contenu dynamique qui s’affiche.

   1. Ouvrez la liste **Ajouter un nouveau paramètre**, puis sélectionnez les champs **Emplacement** et **Propriétés**.

   1. Fournissez des détails pour ces nouveaux champs comme indiqué dans cet exemple.

      ![Capture d’écran qui montre les détails de la saisie de l’action Azure Resource Manager.](./media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   L’action SAP **Générer les schémas** génère des schémas sous forme de collection : le concepteur ajoute donc automatiquement une boucle **For each** à l’action. Voici un exemple qui montre comment cette action apparaît :

   ![Capture d’écran qui montre l’action Azure Resource Manager avec une boucle « for each ».](./media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)

   > [!NOTE]
   > Les schémas utilisent un format codé en base64. Pour télécharger les schémas dans un compte d’intégration, ils doivent être décodés avec la fonction `base64ToString()`. Voici un exemple qui montre le code pour l’élément `"properties"` :
   >
   > ```json
   > "properties": {
   >    "Content": "@base64ToString(items('For_each')?['Content'])",
   >    "ContentType": "application/xml",
   >    "SchemaType": "Xml"
   > }
   > ```

1. Enregistrez votre workflow d’application logique. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**.

### <a name="test-your-workflow"></a>Tester votre workflow

1. Dans la barre d’outils du concepteur, sélectionnez **Exécuter** pour déclencher manuellement votre workflow d’application logique.

1. Après une exécution réussie, accédez au compte d’intégration et vérifiez l’existence des schémas générés.

<a name="enable-secure-network-communications"></a>

## <a name="enable-secure-network-communications-snc"></a>Activer SNC (Secure Network Communications)

Avant de commencer, assurez-vous de respecter les [conditions préalables](#prerequisites) précédemment mentionnées, qui s’appliquent uniquement lorsque vous utilisez la passerelle de données et que vos workflows d’applications logiques s’exécutent dans une instance Azure mutualisée :

* Assurez-vous que la passerelle de données locale est installée sur un ordinateur qui se trouve dans le même réseau que votre système SAP.

* Pour l’authentification unique, la passerelle de données s’exécute en tant qu’utilisateur qui est mappé à un utilisateur SAP.

* La bibliothèque SNC qui fournit les fonctions de sécurité supplémentaire est installée sur le même ordinateur que la passerelle de données. [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos et NTLM en sont des exemples.

   Pour activer SNC pour vos demandes vers ou depuis le système SAP, cochez la case **Utiliser SNC** dans la connexion SAP et renseignez ces propriétés :

   ![Capture d’écran montrant comment configurer SNC dans une connexion SAP.](./media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | Propriété | Description |
   |----------| ------------|
   | **SNC Library Path** (Chemin de la bibliothèque SNC) | Nom ou chemin de la bibliothèque SNC relatif au chemin absolu ou à l’emplacement d’installation NCo. `sapsnc.dll`, `.\security\sapsnc.dll` ou `c:\security\sapsnc.dll` en sont des exemples. |
   | **SNC SSO** (Authentification unique SNC) | Lorsque vous vous connectez via SNC, l’identité SNC est généralement utilisée pour authentifier l’appelant. Une autre option consiste à substituer cette identité afin que les informations d’utilisateur et de mot de passe puissent être utilisées pour authentifier l’appelant, mais la ligne est toujours chiffrée. |
   | **Mon nom SNC** | Dans la plupart des cas, vous pouvez omettre cette propriété. La solution SNC installée connaît généralement son propre nom SNC. Uniquement pour les solutions qui prennent en charge plusieurs identités, vous devrez peut-être spécifier l’identité à utiliser pour ce serveur ou cette destination spécifique. |
   | **Nom du partenaire SNC** | Nom du SNC back-end. |
   | **Qualité de protection SNC** | Qualité de service à utiliser pour la communication SNC de ce serveur ou cette destination spécifique. La valeur par défaut est définie par le système back-end. La valeur maximale est définie par le produit de sécurité utilisé pour SNC. |
   |||

   > [!NOTE]
   > Ne définissez pas les variables d’environnement SNC_LIB et SNC_LIB_64 sur l’ordinateur où vous avez la passerelle de données et la bibliothèque SNC. Si elles sont définies, elles sont prioritaires sur la valeur de la bibliothèque SNC passée via le connecteur.

## <a name="safe-typing"></a>Types sécurisés

Par défaut, quand vous créez votre connexion SAP, le typage fort est utilisé pour rechercher des valeurs non valides en effectuant une validation XML par rapport au schéma. Ce comportement peut vous aider à détecter les problèmes plus tôt. L’option **Types sécurisés** est disponible pour la compatibilité descendante et ne vérifie que la longueur de chaîne. Si vous choisissez **Types sécurisés**, les types DATS et TIMS dans SAP sont traités en tant que chaînes plutôt que comme leurs équivalents XML, `xs:date` et `xs:time`, où `xmlns:xs="http://www.w3.org/2001/XMLSchema"`. Les types sécurisés affectent le comportement pour la génération de schémas dans sa globalité, le message d’envoi pour à la fois la charge utile « envoyé » et la réponse « reçu » ainsi que le déclencheur.

Quand le typage fort est utilisé (l’option **Types sécurisés** n’est pas activée), le schéma mappe les types DATS and TIMS à des types XML plus simples :

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

Lorsque vous envoyez des messages en utilisant un typage fort, la réponse DATS et TIMS est conforme au format de type XML correspondant :

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

Quand l’option **Types sécurisés** est activée, le schéma mappe les types DATS and TIMS aux champs de chaîne XML avec des restrictions de longueur uniquement, par exemple :

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="8" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="6" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
```

Lorsque les messages sont envoyés avec l’option **Types sécurisés** activée, la réponse DATS et TIMS ressemble à cet exemple :

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```

## <a name="send-sap-telemetry-foron-premises-data-gateway-to-azure-application-insights"></a>Envoyer la télémétrie SAP pour la passerelle de données locale vers Azure Application Insights

Avec la mise à jour d’août 2021 pour la passerelle de données locale, les opérations de connecteur SAP peuvent envoyer des données de télémétrie à partir de la bibliothèque cliente SAP et des traces de l’adaptateur SAP de Microsoft vers [Application Insights](../azure-monitor/app/app-insights-overview.md), qui est une fonctionnalité de Azure Monitor. Cette télémétrie comprend principalement les données suivantes :

* Métriques et traces basées sur les métriques et les analyses SAP NCo.

* Traces à partir de l’adaptateur Microsoft SAP.

### <a name="metrics-and-traces-from-sap-client-library"></a>Mesures et traces à partir de la bibliothèque cliente SAP

Les *métriques* sont des valeurs numériques qui peuvent ou ne peuvent pas varier sur une période, en fonction de l’utilisation et de la disponibilité des ressources sur la passerelle de données locale. Vous pouvez utiliser ces métriques pour mieux comprendre l’intégrité du système et créer des alertes sur les activités suivantes :

* Indique si l’intégrité du système est en baisse.

* Événements inhabituels.

* Charge importante sur votre système.

Ces informations sont envoyées à la table Application Insights, `customMetrics`. Par défaut, les métriques sont envoyées à intervalles de 30 secondes.

Les métriques et les suivis SAP NCo sont basés sur les métriques SAP NCo, en particulier les classes NCo suivantes :

* RfcDestinationMonitor.

* RfcConnectionMonitor.

* RfcServerMonitor.

* RfcRepositoryMonitor.

Pour plus d’informations sur les métriques fournies par chaque classe, consultez la [documentation SAP NCO (connexion requise)](https://support.sap.com/en/product/connectors/msnet.html#section_512604546).

Les *suivis* incluent des informations de texte utilisées avec les métriques. Ces informations sont envoyées à la table Application Insights nommée `traces`. Par défaut, les traces sont envoyées à intervalles de 10 minutes.

### <a name="set-up-sap-telemetry-for-application-insights"></a>Configuration de la télémétrie SAP pour Application Insights​

Avant de pouvoir envoyer des données de télémétrie SAP pour l’installation de votre passerelle vers Application Insights, vous devez avoir créé et configuré votre ressource Application Insights. Pour plus d’informations, consultez la documentation suivante :

* [Créer une ressource Application Insights (classique)](../azure-monitor/app/create-new-resource.md)

* [Ressources Application Insights basées sur l’espace de travail](../azure-monitor/app/create-workspace-resource.md)

Pour activer l’envoi de données de télémétrie SAP à Application Insights, procédez comme suit :

1. Téléchargez le package NuGet pour **Microsoft.ApplicationInsights.EventSourceListener.dll** à partir de cet emplacement : [https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/2.14.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/2.14.0).

1. Ajoutez le fichier téléchargé à votre répertoire d’installation de la passerelle de données locale.

1. Dans votre répertoire d’installation de la passerelle de données locale, vérifiez que le fichier **Microsoft.ApplicationInsights.dll** a le même numéro de version que le fichier **Microsoft.ApplicationInsights.EventSourceListener.dll** que vous avez ajouté. La passerelle utilise actuellement la version 2.14.0.

1. Dans le fichier **ApplicationInsights.config**, ajoutez votre [clé d’instrumentation Application Insights](../azure-monitor/app/app-insights-overview.md#how-does-application-insights-work) en supprimant les commentaires de la ligne avec l'élément `<InstrumentationKey></Instrumentation>`. Remplacez l’espace réservé, *your-Application-Insights-instrumentation-key*, par votre clé, par exemple :

      ```xml
      <?xml version="1.0" encoding="utf-8"?>
      <ApplicationInsights schemaVersion="2014-05-30" xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
         <!-- Uncomment this element and insert your Application Insights key to receive ETW telemetry about your gateway <InstrumentationKey>*your-instrumentation-key-placeholder*</InstrumentationKey> -->
         <TelemetryModules>
            <Add Type="Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule, Microsoft.ApplicationInsights">
               <IsHeartbeatEnabled>false</IsHeartbeatEnabled>
            </Add>
            <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
               <Sources>
                  <Add Name="Microsoft-LobAdapter" Level="Verbose" />
               </Sources>
            </Add>
         </TelemetryModules>
      </ApplicationInsights>
      ```

1. Dans le fichier **ApplicationInsights.config**, vous pouvez modifier la valeur des suivis requis `Level` pour vos opérations de connecteur SAP, conformément à vos besoins, par exemple :

   ```xml
   <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
         <Add Name="Microsoft-LobAdapter" Level="Verbose" />
      </Sources>
   </Add>
   ```

   Pour plus d’informations, consultez la documentation suivante :

   * `Level` valeurs : [Enum EventLevel](/dotnet/api/system.diagnostics.tracing.eventlevel)

   * [Suivi EventSource](../azure-monitor/app/configuration-with-applicationinsights-config.md#eventsource-tracking)

   * [Événements EventSource](../azure-monitor/app/asp-net-trace-logs.md#use-eventsource-events)

1. Après avoir appliqué vos modifications, redémarrez le service de passerelle de données locale.

### <a name="review-metrics-in-application-insights"></a>Exploration des métriques dans Application Insights

Une fois que vos opérations SAP s’exécutent dans votre flux de travail d’application logique, vous pouvez passer en revue les données de télémétrie envoyées à Application Insights.

1. Ouvrez votre ressource Application Insights dans le portail Azure.

1. Dans le menu de la ressource, sous **Surveillance**, sélectionnez **Journaux d'activité**.

   La capture d’écran suivante montre le portail Azure avec Application Insights, qui est ouvert dans le volet des **Journaux d'activité** :

   [![Capture d’écran montrant le portail Azure avec Application Insights ouvrir le volet « Journaux » pour la création de requêtes.](./media/logic-apps-using-sap-connector/application-insights-query-panel.png)](./media/logic-apps-using-sap-connector/application-insights-query-panel.png#lightbox)

1. Dans le volet **Journaux** , vous pouvez créer une [requête](/azure/data-explorer/kusto/query/) à l’aide du [langage de requête Kusto (KQL)](/azure/data-explorer/kusto/concepts/), en fonction de vos besoins spécifiques.

   Vous pouvez utiliser un modèle de requête semblable à l’exemple de requête suivant :

   ```Kusto
   customMetrics
   | extend DestinationName = tostring(customDimensions["DestinationName"])
   | extend MetricType = tostring(customDimensions["MetricType"])
   | where customDimensions contains "RfcDestinationMonitor"
   | where name contains "MaxUsedCount"
   ```

1. Après avoir exécuté votre requête, passez en revue les résultats.

   La capture d’écran suivante montre le tableau des résultats des métriques de la requête d’exemple :

   [![Capture d’écran montrant Application Insights avec le tableau des résultats de métriques.](./media/logic-apps-using-sap-connector/application-insights-metrics.png)](./media/logic-apps-using-sap-connector/application-insights-metrics.png#lightbox)

   * **MaxUsedCount** est le « nombre maximal de connexions clientes qui ont été utilisées simultanément par la destination analysée ». comme décrit dans la [documentation SAP NCO (connexion requise)](https://support.sap.com/en/product/connectors/msnet.html#section_512604546). Vous pouvez utiliser cette valeur pour comprendre le nombre de connexions ouvertes simultanément.

   * La colonne **valueCount** affiche **2** pour chaque lecture, car les métriques sont générées à intervalles de 30 secondes et Application Insights regroupe ces métriques à la minute.

   * La colonne **DestinationName** contient une chaîne de caractères qui est un nom interne de l’adaptateur Microsoft SAP.

     Pour mieux comprendre cette destination de l’appel de fonction distant (RFC), utilisez cette valeur avec `traces`, par exemple :

     ```Kusto
     customMetrics
     | extend DestinationName = tostring(customDimensions["DestinationName"])
     | join kind=inner (traces
        | extend DestinationName = tostring(customDimensions["DestinationName"]),
        AppServerHost = tostring(customDimensions["AppServerHost"]),
        SncMode = tostring(customDimensions["SncMode"]),
        SapClient = tostring(customDimensions["Client"])
        | where customDimensions contains "RfcDestinationMonitor"
        )
        on DestinationName , $left.DestinationName == $right.DestinationName
     | where customDimensions contains "RfcDestinationMonitor"
     | where name contains "MaxUsedCount"
     | project AppServerHost, SncMode, SapClient, name, valueCount, valueSum, valueMin, valueMax
     ```

Vous pouvez également créer des graphiques de métriques ou des alertes à l’aide de ces fonctionnalités dans Application Insights, par exemple :

[![Capture d’écran montrant Application Insights avec les résultats au format graphique.](./media/logic-apps-using-sap-connector/application-insights-metrics-chart.png)](./media/logic-apps-using-sap-connector/application-insights-metrics-chart.png#lightbox)

### <a name="traces-from-microsoft-sap-adapter"></a>Traces à partir de l’adaptateur Microsoft SAP

Vous pouvez utiliser les traces envoyées à partir de l’adaptateur Microsoft SAP pour les problèmes après l’analyse et rechercher les erreurs système internes existantes qui peuvent ne pas être visibles par les opérations du connecteur SAP. Ces traces ont `message` affecté à `"n\a"`, car elles proviennent d’une infrastructure source d’événement antérieure à Application Insights, par exemple :

```Kusto
traces
| where message == "n/a"
| where severityLevel > 0
| extend ActivityId = tostring(customDimensions["ActivityId"])
| extend fullMessage = tostring(customDimensions["fullMessage"])
| extend shortMessage = tostring(customDimensions["shortMessage"])
| where ActivityId contains "8ad5952b-371e-4d80-b355-34e28df9b5d1"
```

La capture d’écran suivante montre le tableau des résultats des métriques de la requête d’exemple :

[![Capture d’écran montrant Application Insights avec le tableau des résultats de traces.](./media/logic-apps-using-sap-connector/application-insights-traces.png)](./media/logic-apps-using-sap-connector/application-insights-traces.png#lightbox)

## <a name="advanced-scenarios"></a>Scénarios avancés

### <a name="change-language-headers"></a>Modifier les en-têtes de langue

Lorsque vous vous connectez à SAP à partir de Logic Apps, la langue par défaut de la connexion est l’anglais. Vous pouvez définir la langue de votre connexion au moyen de [l’en-tête HTTP standard `Accept-Language`](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.4) avec vos requêtes entrantes.

> [!TIP]
> La plupart des navigateurs web ajoutent un en-tête `Accept-Language` en fonction des paramètres de l’utilisateur. Le navigateur web applique cet en-tête lorsque vous créez une nouvelle connexion SAP dans le concepteur de workflow. Vous pouvez soit mettre à jour les paramètres de votre navigateur web pour utiliser votre langue par défaut, soit créer votre connexion SAP à l’aide d’Azure Resource Manager au lieu du concepteur de workflow.

Par exemple, vous pouvez envoyer une requête avec l’en-tête `Accept-Language` à votre workflow d’application logique à l’aide du déclencheur **Requête**. Toutes les actions de votre workflow d’application logique reçoivent l’en-tête. Ensuite, SAP utilise les langues spécifiées dans ses messages système, tels que les messages d’erreur BAPI.

Les paramètres de connexion SAP pour un workflow d’application logique ne possèdent pas de propriété de langue. Aussi, si vous utilisez l’en-tête `Accept-Language`, vous pouvez obtenir l’erreur suivante : **Veuillez vérifier les informations et/ou autorisations de votre compte, et réessayez.** Dans ce cas, vérifiez plutôt les journaux d’erreurs du composant SAP. L’erreur se produit réellement dans le composant SAP qui utilise l’en-tête. Vous pouvez donc obtenir l’un des messages d’erreur suivants :

* `"SAP.Middleware.Connector.RfcLogonException: Select one of the installed languages"`

* `"SAP.Middleware.Connector.RfcAbapMessageException: Select one of the installed languages"`

### <a name="confirm-transaction-explicitly"></a>Confirmer une transaction explicitement

Quand vous envoyez des transactions à SAP depuis Logic Apps, cet échange se fait en deux étapes, comme décrit dans le document SAP, [Transactional RFC Server Programs](https://help.sap.com/doc/saphelp_nwpi71/7.1/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true). Par défaut, l’action **Envoyer à SAP** gère à la fois les étapes de transfert de la fonction et de la confirmation de la transaction dans un même appel. Le connecteur SAP vous donne la possibilité de découpler ces étapes. Vous pouvez envoyer un IDoc et, au lieu de confirmer automatiquement la transaction, vous pouvez utiliser l’action explicite **\[IDOC]Confirmer l’ID de transaction**.

Cette possibilité de découpler la confirmation de l’ID de transaction est utile quand vous ne voulez pas dupliquer les transactions dans SAP, par exemple dans les scénarios où des échecs peuvent se produire pour des raisons comme des problèmes réseau. En confirmant l’ID de transaction séparément, la transaction n’est effectuée qu’une seule fois dans votre système SAP.

Voici un exemple montrant ce modèle :

1. Créez une application logique vide et ajoutez le déclencheur de requête.

1. À partir du connecteur SAP, ajoutez l’action **\[IDOC] Envoyer un document à SAP**. Spécifiez les détails de l’IDoc que vous envoyez à votre système SAP.

1. Pour confirmer explicitement l’ID de transaction dans une étape distincte, dans le champ **Confirmer le TID**, sélectionnez **Non**. Pour le champ facultatif **GUID de l’ID de transaction**, vous pouvez spécifier manuellement la valeur, ou faire en sorte que le connecteur génère et retourne automatiquement ce GUID dans la réponse de l’action **\[IDOC] Envoyer le document à SAP**.

   ![Capture d’écran montrant les propriétés d’action « [IDOC] Envoyer le document à SAP »](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. Pour confirmer explicitement l’ID de transaction, ajoutez l’action **\[IDOC] Confirmer l’ID de transaction** en veillant à ne pas [envoyer d’IDocs en double à SAP](#avoid-sending-duplicate-idocs). Cliquez dans la zone **ID de transaction** pour faire apparaître la liste des contenus dynamiques. Dans cette liste, sélectionnez la valeur **ID de transaction** qui est retournée depuis l’action **\[IDOC] Envoyer un document à SAP**.

   ![Capture d’écran montrant l’action « Confirmer l’ID de la transaction »](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   Après l’exécution de cette étape, la transaction actuelle est marquée comme terminée aux deux extrémités, sur le côté connecteur SAP et sur le côté système SAP.

#### <a name="avoid-sending-duplicate-idocs"></a>Ne pas envoyer d’IDocs en double

Si vous rencontrez un problème d’envoi d’IDocs en double à SAP à partir de votre workflow d’application logique, procédez comme suit pour créer une variable de chaîne qui servira d’identificateur de transaction IDoc. La création de cet identificateur de transaction permet d’éviter les transmissions réseau en double en cas de problèmes tels que des pannes temporaires, des problèmes réseau ou des accusés de réception perdus.

> [!NOTE]
> Les systèmes SAP oublient un identificateur de transaction après une durée spécifiée, ou 24 heures par défaut. Par conséquent, SAP ne manque jamais de confirmer un identificateur de transaction si l’ID ou le GUID est inconnu.
> Si la confirmation d’un identificateur de transaction échoue, cet échec indique que la communication avec le système SAP a échoué avant que SAP ait pu accuser réception de la confirmation.

1. Dans le concepteur de workflow, ajoutez l’action **Initialiser la variable** à votre workflow d’application logique.

1. Dans l’éditeur de l’action **Initialiser la variable**, configurez les paramètres suivants. Ensuite, enregistrez vos modifications.

   1. Pour **Nom**, entrez un nom pour votre variable. Par exemple : `IDOCtransferID`.

   1. Pour **Type**, sélectionnez **Chaîne** comme type de variable.

   1. Pour **Valeur**, sélectionnez la zone de texte **Entrer la valeur initiale** pour ouvrir le menu de contenu dynamique.

   1. Sélectionnez l’onglet **Expressions**. Dans la liste des fonctions, entrez la fonction `guid()`.

   1. Sélectionnez **OK** pour enregistrer vos modifications. Le champ **Valeur** est maintenant défini sur la fonction `guid()`, qui génère un GUID.

1. Après l’action **Initialiser la variable**, ajoutez l’action **\[IDOC] Envoyer un document à SAP**.

1. Dans l’éditeur de l’action **\[IDOC] Envoyer un document à SAP**, configurez les paramètres suivants. Ensuite, enregistrez vos modifications.

   1. Pour **Type d’IDOC**, sélectionnez votre type de message et, pour **Message IDOC d’entrée**, spécifiez votre message.

   1. Pour **Version SAP**, sélectionnez les valeurs de votre configuration SAP.

   1. Pour **Version des types d’enregistrement**, sélectionnez les valeurs de votre configuration SAP.

   1. Pour **Confirmer le TID**, sélectionnez **Non**.

   1. Sélectionnez **Ajouter une liste de paramètres** > **GUID de l’ID de transaction**.

   1. Sélectionnez la zone de texte pour ouvrir le menu de contenu dynamique. Sous l’onglet **Variables**, sélectionnez le nom de la variable que vous avez créée, par exemple `IDOCtransferID`.

1. Dans la barre de titre de l’action **\[IDOC] Envoyer un document à SAP**, sélectionnez **...**  > **Paramètres**.

   Pour **Stratégie de nouvelle tentative**, il est recommandé de sélectionner **Par défaut** &gt; **Terminé**. Toutefois, vous pouvez à la place configurer une stratégie personnalisée adaptée à vos besoins spécifiques. Pour les stratégies personnalisées, il est recommandé de configurer au moins une nouvelle tentative pour surmonter les pannes réseau temporaires.

1. Après l’action **\[IDOC] Envoyer un document à SAP**, ajoutez l’action **\[ IDOC] Confirmer l’ID de transaction**.

1. Dans l’éditeur de l’action **\[IDOC] Confirmer l’ID de transaction**, configurez les paramètres suivants. Ensuite, enregistrez vos modifications.

1. Pour **ID de transaction**, entrez à nouveau le nom de votre variable. Par exemple : `IDOCtransferID`.

1. Si vous le souhaitez, validez la déduplication dans votre environnement de test.

    1. Répétez l’action **\[IDOC] Envoyer un document à SAP** avec le même GUID **ID de transaction** que celui utilisé à l’étape précédente.
    
    1. Pour valider le numéro IDoc attribué après chaque appel à l’action **\[IDOC] Envoyer un document à SAP**, utilisez l’action **\[IDOC] Obtenir une liste d’IDOC pour la transaction** en spécifiant le même **ID de transaction** et la direction **Receive** (Réception).
    
       Si le même IDoc est envoyé, un seul numéro IDoc est retourné pour les deux appels, IDoc ayant été dédupliqué.

   Lorsque vous envoyez le même IDoc à deux reprises, vous pouvez vérifier que SAP est capable d’identifier la duplication de l’appel tRFC et de résoudre les deux appels dans un seul message IDoc entrant.

## <a name="known-issues-and-limitations"></a>Problèmes connus et limitations

Voici les problèmes et limitations connus pour le connecteur SAP (non-ISE) managé :

* En général, le déclencheur SAP ne prend pas en charge les clusters de passerelles de données. Dans certains cas de basculement, le nœud de la passerelle de données qui communique avec le système SAP peut différer du nœud actif, ce qui entraîne un comportement inattendu.

  * Les clusters de passerelles de données en mode de basculement sont pris en charge dans les scénarios d’envoi.

  * Les clusters de passerelles de données en mode d’équilibrage de charge ne sont pas pris en charge par les [actions SAP](#actions) avec état, à savoir : **\[BAPI - RFC]Création d’une session avec état**, **\[BAPI]Validation d’une transaction**, **\[BAPI] Restauration d’une transaction**, **\[BAPI - RFC] Fermeture d’une session avec état** et toutes les actions qui spécifient une valeur **ID de session**. Les communications avec état doivent rester sur le même nœud de cluster de passerelles de données.

  * Pour les actions SAP avec état, utilisez la passerelle de données en mode non cluster ou dans un cluster configuré pour le basculement uniquement.

* Le connecteur SAP ne prend actuellement pas en charge les chaînes de routeur SAP. La passerelle de données locale doit exister sur le même réseau local que le système SAP que vous voulez connecter.

* Dans l’action **\[BAPI] Appeler une méthode dans SAP**, la fonctionnalité de commit automatique ne commite pas les changements apportés à l’interface BAPI s’il existe au moins un avertissement dans l’objet **CallBapiResponse** retourné par l’action. Pour commiter les changements apportés à l’interface BAPI malgré les avertissements, créez explicitement une session avec l’action **\[BAPI - RFC] Créer une session avec état**, désactivez la fonctionnalité de commit automatique dans l’action **\[BAPI] Appeler une méthode dans SAP**, puis appelez l’action **\[BAPI] Commiter une transaction** à la place.

* Pour les [applications logiques utilisées dans un environnement de service d’intégration (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md), la version de ce connecteur avec l’étiquette ISE applique les [limites de messages de l’ISE](logic-apps-limits-and-config.md#message-size-limits) à la place.

## <a name="connector-reference"></a>Référence de connecteur

Pour plus d’informations sur le connecteur SAP, consultez la [Référence de connecteur](/connectors/sap/). Vous y trouverez des détails relatifs aux limites, paramètres et retours pour le connecteur SAP, les déclencheurs et les actions.

### <a name="triggers"></a>Déclencheurs

:::row:::
    :::column span="1":::
        [**Quand un message est reçu de SAP**](/connectors/sap/#when-a-message-is-received)
    :::column-end:::
    :::column span="3":::
        Quand un message est reçu de SAP, vous devez agir.
    :::column-end:::
:::row-end:::

### <a name="actions"></a>Actions

:::row:::
    :::column span="1":::
        [ **[BAPI - RFC] Fermer une session avec état**](/connectors/sap/#[bapi---rfc]-close-stateful-session-(preview))
    :::column-end:::
    :::column span="3":::
        Fermez une session de connexion avec état existante sur votre système SAP.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[BAPI - RFC] Créer une session avec état**](/connectors/sap/#[bapi---rfc]-create-stateful-session-(preview))
    :::column-end:::
    :::column span="3":::
        Créez une session de connexion avec état sur votre système SAP.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[BAPI] Appeler une méthode dans SAP**](/connectors/sap/#[bapi]-call-method-in-sap-(preview))
    :::column-end:::
    :::column span="3":::
        Appelez la méthode BAPI dans votre système SAP.
        \
        \
        Vous devez utiliser les paramètres suivants avec votre appel :     \
        \
        **Objet métier** (`businessObject`), un menu déroulant pouvant faire l’objet d’une recherche.
        \
        \
        **Méthode** (`method`), qui renseigne les méthodes disponibles une fois que vous avez sélectionné un **Objet métier**. Les méthodes disponibles varient en fonction de la sélection **Objet métier**.
        \
        \
        **Paramètres BAPI d’entrée** (`body`), où vous appelez le document XML qui contient les valeurs des paramètres d’entrée de la méthode BAPI pour l’appel, ou l’URI de l’objet blob de stockage qui contient vos paramètres BAPI.
        \
        \
        Pour obtenir des exemples détaillés d’utilisation de l’action **[BAPI] Appeler une méthode dans SAP**, consultez les [ exemples XML des requêtes BAPI](#xml-samples-for-bapi-requests).
        \
        Si vous utilisez le concepteur de workflow pour modifier votre requête BAPI, vous pouvez utiliser les fonctions de recherche suivantes :     \
        \
        Sélectionnez un objet dans le concepteur afin d’afficher le menu déroulant des méthodes disponibles.
        \
        \
        Filtrez les types d’objets métier par mot clé à l’aide de la liste de recherche fournie par l’appel d’API BAPI.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[BAPI] Valider une transaction**](/connectors/sap/#[bapi]-commit-transaction-(preview))
    :::column-end:::
    :::column span="3":::
        Validez la transaction BAPI pour la session.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[BAPI] Restaurer la transaction**](/connectors/sap/#[bapi]-roll-back-transaction-(preview))
    :::column-end:::
    :::column span="3":::
        Restaurez la transaction BAPI pour la session.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[IDOC - RFC] Confirmer l’ID de transaction**](/connectors/sap/#[idoc---rfc]-confirm-transaction-id-(preview))
    :::column-end:::
    :::column span="3":::
        Envoyez la confirmation de l’identificateur de transaction à SAP.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[IDOC] Obtenir une liste d’IDOC pour la transaction**](/connectors/sap/#[idoc]-get-idoc-list-for-transaction-(preview))
    :::column-end:::
    :::column span="3":::
        Obtenez une liste d’IDOC pour la transaction par identificateur de session ou identificateur de transaction.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[IDOC] Obtenir l’état d’un IDOC**](/connectors/sap/#[idoc]-get-idoc-status-(preview))
    :::column-end:::
    :::column span="3":::
        Obtenez l’état d’un IDOC.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[IDOC] Envoyer un document à SAP**](/connectors/sap/#[idoc]-send-document-to-sap-(preview))
    :::column-end:::
    :::column span="3":::
        Envoyez le message IDOC à votre serveur SAP.
        \
        \
        Vous devez utiliser les paramètres suivants avec votre appel :     \
        \
        **Type IDOC avec extension facultative** (`idocType`), un menu déroulant pouvant faire l’objet d’une recherche.
        \
        \
        **Message IDOC d’entrée** (`body`), où vous appelez le document XML contenant la charge utile IDoc, ou l’URI de l’objet blob de stockage qui contient votre document XML IDoc. Ce document doit être conforme au schéma XML IDoc SAP conformément à la documentation WE60 IDoc ou au schéma généré pour l’URI d’action IDoc SAP correspondant.
        \
        \
        Le paramètre facultatif **Version SAP** (`releaseVersion`) renseigne les valeurs une fois que vous avez sélectionné le type IDoc, et dépend du type IDoc sélectionné.
        \
        \
        Pour obtenir des exemples détaillés sur l’utilisation de l’action Envoyer IDoc, consultez la [procédure pas à pas pour l’envoi de messages IDoc à votre serveur SAP](#send-idoc-messages-to-sap-server).
        \
        \
        Pour savoir comment utiliser le paramètre facultatif **Confirmer TID** (`confirmTid`), consultez la [procédure pas à pas pour la confirmation explicite de la transaction](#confirm-transaction-explicitly).
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[RFC] Ajouter RFC à la transaction**](/connectors/sap/#[rfc]-add-rfc-to-transaction-(preview))
    :::column-end:::
    :::column span="3":::
        Ajoutez un appel RFC à votre transaction.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[RFC] Appeler une fonction dans SAP**](/connectors/sap/#[rfc]-call-function-in-sap-(preview))
    :::column-end:::
    :::column span="3":::
        Appelez une opération RFC (sRFC, tRFC ou qRFC) sur votre système SAP.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[RFC] Valider une transaction**](/connectors/sap/#[rfc]-commit-transaction-(preview))
    :::column-end:::
    :::column span="3":::
        Validez la transaction RFC pour la session et/ou file d’attente.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[RFC] Créer une transaction**](/connectors/sap/#[rfc]-create-transaction-(preview))
    :::column-end:::
    :::column span="3":::
        Créez une transaction par identificateur et/ou nom de file d’attente. Si la transaction existe, obtenez les détails.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[RFC] Obtenir une transaction**](/connectors/sap/#[rfc]-get-transaction-(preview))
    :::column-end:::
    :::column span="3":::
        Obtenez les détails d’une transaction par identificateur et/ou nom de file d’attente. Créez une transaction s’il n’en existe aucune.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [**Générer les schémas**](/connectors/sap/#generate-schemas)
    :::column-end:::
    :::column span="3":::
        générer des schémas pour les artefacts SAP pour IDoc, BAPI ou RFC.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [**Lire une table SAP**](/connectors/sap/#read-sap-table-(preview))
    :::column-end:::
    :::column span="3":::
        Lisez une table SAP.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [**Envoyer un message à SAP**](/connectors/sap/#send-message-to-sap)
    :::column-end:::
    :::column span="3":::
        Envoyez n’importe quel type de message (RFC, BAPI, IDoc) à SAP.
    :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Étapes suivantes

* [Connectez-vous à des systèmes locaux](logic-apps-gateway-connection.md) à partir d’Azure Logic Apps
* Découvrez comment valider, transformer et utiliser d’autres opérations message avec [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)
