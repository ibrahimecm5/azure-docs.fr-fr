---
title: Résolution des problèmes de configuration des points de terminaison privés pour vos comptes Purview
description: Cet article explique comment résoudre les problèmes avec votre compte Purview liés aux configurations des points de terminaison privés
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/15/2021
ms.openlocfilehash: 1557b229c4db50dca2115fc9c11123c76c5adc50
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131851816"
---
# <a name="troubleshooting-private-endpoint-configuration-for-purview-accounts"></a>Résolution des problèmes de configuration des points de terminaison privés pour vos comptes Purview

> [!IMPORTANT]
> Si vous avez créé un point de terminaison privé de _portail_ pour votre compte Purview **avant le 27 septembre 2021 à 15h30 UTC**, vous devez effectuer les actions requises détaillées dans [Reconfigurer DNS pour les points de terminaison privés du portail](./catalog-private-link.md#reconfigure-dns-for-portal-private-endpoints). **Ces actions doivent être effectuées avant le 12 novembre 2021. Sinon, les points de terminaison privés existants du portail cesseront de fonctionner**.

Ce guide résume les limitations connues liées à l’utilisation de points de terminaison privés pour Azure Purview et fournit une liste des étapes et des solutions permettant de résoudre certains des problèmes les plus courants. 

## <a name="known-limitations"></a>Limitations connues

- Actuellement, nous ne prenons pas en charge les points de terminaison privés d’ingestion qui fonctionnent avec vos sources AWS.
- L’analyse de plusieurs sources Azure à l’aide du runtime d’intégration auto-hébergé n’est pas prise en charge.
- L’utilisation du runtime d’intégration Azure pour analyser les sources de données derrière le point de terminaison privé n’est pas prise en charge.
- Au moyen du Portail Azure, vous pouvez créer des points de terminaison privés d’ingestion via le Portail Azure Purview décrit dans les étapes précédentes. Vous ne pouvez pas le faire à partir du Centre Private Link.
- Création d’enregistrements DNS A pour les points de terminaison privés d’ingestion dans des zones Azure DNS existantes, alors que les zones Azure DNS privé se trouvent dans un abonnement différent de celui où les points de terminaison privés ne sont pas pris en charge via l’expérience du Portail Azure Purview. Les enregistrements A peuvent être ajoutés manuellement dans les zones DNS de destination dans l’autre abonnement. 
- L’ordinateur du runtime d’intégration auto-hébergé doit être déployé dans le même réseau virtuel que celui où le point de terminaison privé d’ingestion Azure Purview est déployé.
- Pour le moment, nous ne prenons pas en charge l’analyse d’un locataire Power BI disposant d’un point de terminaison privé configuré avec un accès public bloqué.
- Pour connaître les limitations liées au service Private Link, consultez [Limites Azure Private Link](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits).

## <a name="recommended-troubleshooting-steps"></a>Procédure de résolution des problèmes recommandée  

1. Une fois que vous avez déployé des points de terminaison privés pour votre compte Purview, examinez votre environnement Azure pour vous assurer que les ressources de point de terminaison privées sont déployées avec succès. Selon votre scénario, un ou plusieurs des points de terminaison privés Azure suivants doivent être déployés dans votre abonnement Azure :

    |Point de terminaison privé  |Point de terminaison privé affecté à | Exemple|
    |---------|---------|---------|
    |Compte  |Compte Azure Purview         |mypurview-private-account  |
    |Portail     |Compte Azure Purview         |mypurview-private-portal  |
    |Ingestion     |Compte de stockage managé (blob)         |mypurview-ingestion-blob  |
    |Ingestion     |Compte de stockage managé (file d’attente)         |mypurview-ingestion-queue  |
    |Ingestion     |Espace de noms Event Hubs managé         |mypurview-ingestion-namespace  |

2. Si le point de terminaison privé du portail est déployé, veillez également à déployer le point de terminaison privé du compte.

3. Si le point de terminaison privé du portail est déployé et que l’accès réseau public est défini de sorte à être refusé dans votre compte Azure Purview, veillez à lancer [Azure Purview Studio](https://web.purview.azure.com/resource/) à partir du réseau interne.
  <br>
    - Pour vérifier la résolution de noms correcte, vous pouvez utiliser un outil de ligne de commande **NSlookup.exe** pour interroger `web.purview.azure.com`. Le résultat doit retourner une adresse IP privée qui appartient au point de terminaison privé du portail. 
    - Pour vérifier la connectivité réseau, vous pouvez utiliser n’importe quel outil de test réseau pour tester la connectivité sortante vers le point de terminaison `web.purview.azure.com` sur le port **443**. La connexion doit être réussie.    

3. Si des zones Azure DNS privé sont utilisées, assurez-vous que les zones Azure DNS requises sont déployées et qu’il existe un enregistrement DNS (A) pour chaque point de terminaison privé.

4. Testez la connectivité réseau et la résolution de noms de l’ordinateur de gestion vers le point de terminaison Purview et l’URL web Purview. Si des points de terminaison privés de compte et de portail sont déployés, les points de terminaison doivent être résolus par le biais d’adresses IP privées.


    ```powershell
    Test-NetConnection -ComputerName web.purview.azure.com -Port 443
    ```

    Exemple de connexion sortante réussie via une adresse IP privée :

    ```
    ComputerName     : web.purview.azure.com
    RemoteAddress    : 10.9.1.7
    RemotePort       : 443
    InterfaceAlias   : Ethernet 2
    SourceAddress    : 10.9.0.10
    TcpTestSucceeded : True
    ```

    ```powershell
    Test-NetConnection -ComputerName purview-test01.purview.azure.com -Port 443
    ```

    Exemple de connexion sortante réussie via une adresse IP privée :

    ```
    ComputerName     : purview-test01.purview.azure.com
    RemoteAddress    : 10.9.1.8
    RemotePort       : 443
    InterfaceAlias   : Ethernet 2
    SourceAddress    : 10.9.0.10
    TcpTestSucceeded : True
    ```
    
5. Si vous avez créé votre compte Azure Purview après le 18 août 2021, veillez à télécharger et à installer la dernière version du runtime d’intégration auto-hébergé à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=39717).
   
6. À partir de la machine virtuelle du runtime d’intégration auto-hébergé, testez la connectivité réseau et la résolution de noms sur le point de terminaison Purview.

7. À partir du runtime d’intégration auto-hébergé, testez la connectivité réseau et la résolution de noms aux ressources managées Azure Purview, telles que la file d’attente d’objets blob et Event Hub via le port 443 et les adresses IP privées. (Remplacez le compte de stockage managé et l’espace de noms Event Hubs par le nom de ressource managée correspondant affecté à votre compte Azure Purview).

    ```powershell
    Test-NetConnection -ComputerName `scansoutdeastasiaocvseab`.blob.core.windows.net -Port 443
    ```
    Exemple de connexion sortante réussie vers le stockage d’objets BLOB managé via une adresse IP privée :

    ```
    ComputerName     : scansoutdeastasiaocvseab.blob.core.windows.net
    RemoteAddress    : 10.15.1.6
    RemotePort       : 443
    InterfaceAlias   : Ethernet 2
    SourceAddress    : 10.15.0.4
    TcpTestSucceeded : True
    ```

    ```powershell
    Test-NetConnection -ComputerName `scansoutdeastasiaocvseab`.queue.core.windows.net -Port 443
    ```
    Exemple de connexion sortante réussie vers le stockage de file d’attente managé via une adresse IP privée :

    ```
    ComputerName     : scansoutdeastasiaocvseab.blob.core.windows.net
    RemoteAddress    : 10.15.1.5
    RemotePort       : 443
    InterfaceAlias   : Ethernet 2
    SourceAddress    : 10.15.0.4
    TcpTestSucceeded : True
    ```

    ```powershell
    Test-NetConnection -ComputerName `Atlas-1225cae9-d651-4039-86a0-b43231a17a4b`.servicebus.windows.net -Port 443
    ```
    Exemple de connexion sortante réussie vers l’espace de noms Event Hub via une adresse IP privée :

    ```
    ComputerName     : Atlas-1225cae9-d651-4039-86a0-b43231a17a4b.servicebus.windows.net
    RemoteAddress    : 10.15.1.4
    RemotePort       : 443
    InterfaceAlias   : Ethernet 2
    SourceAddress    : 10.15.0.4
    TcpTestSucceeded : True
    ```

8. À partir du réseau où se trouve la source de données, testez la connectivité réseau et la résolution de noms sur le point de terminaison Purview et les points de terminaison de ressources managées.

9.  Si les sources de données se trouvent dans un réseau local, vérifiez votre configuration de redirecteur DNS. Testez la résolution de noms au sein du même réseau où les sources de données sont situées sur le runtime d’intégration auto-hébergé, les points de terminaison et les ressources managées Azure Purview. Il est censé obtenir une adresse IP privée valide à partir d’une requête DNS pour chaque point de terminaison.
    
    Pour plus d’informations, consultez [Charges de travail de réseau virtuel sans serveur DNS personnalisé](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server) et [Charges de travail locales à l’aide de scénarios de redirecteur DNS](../private-link/private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder) dans [Configuration DNS de point de terminaison privé Azure](../private-link/private-endpoint-dns.md).

10. Si l’ordinateur de gestion et les machines virtuelles du runtime d’intégration auto-hébergé sont déployés dans un réseau local et que vous avez configuré le redirecteur DNS dans votre environnement, vérifiez les paramètres DNS et réseau dans votre environnement. 

11. Si le point de terminaison privé d’ingestion est utilisé, assurez-vous que le runtime d’intégration auto-hébergé est correctement inscrit dans le compte Purview et qu’il s’exécute à la fois à l’intérieur de la machine virtuelle du runtime d’intégration auto-hébergé et dans [Purview Studio](https://web.purview.azure.com/resource/).

## <a name="common-errors-and-messages"></a>Erreurs et messages courants

### <a name="issue"></a>Problème 
Vous pouvez recevoir le message d’erreur suivant lors de l’exécution d’une analyse :

`Internal system error. Please contact support with correlationId:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx System Error, contact support.`

### <a name="cause"></a>Cause 
Il peut s’agir d’une indication de problèmes liés à la connectivité ou à la résolution de noms entre la machine virtuelle exécutant le runtime d’intégration auto-hébergé et le compte de stockage des ressources managées d’Azure Purview ou Event Hub.

### <a name="resolution"></a>Résolution 
Vérifiez la résolution de noms entre les machines virtuelles exécutant le runtime d’intégration auto-hébergé.


### <a name="issue"></a>Problème 
Vous pouvez recevoir le message d’erreur suivant lors de l’exécution d’une nouvelle analyse :

  `message: Unable to setup config overrides for this scan. Exception:'Type=Microsoft.WindowsAzure.Storage.StorageException,Message=The remote server returned an error: (404) Not Found.,Source=Microsoft.WindowsAzure.Storage,StackTrace= at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndExecuteAsync[T](IAsyncResult result)`

### <a name="cause"></a>Cause 
Il peut s’agir d’une indication de l’exécution d’une version antérieure du runtime d’intégration auto-hébergé. Vous devez utiliser la version du runtime d’intégration auto-hébergé 5.9.7885.3 ou une version ultérieure.

### <a name="resolution"></a>Résolution 
Mettez à nouveau le runtime d’intégration auto-hébergé à la version 5.9.7885.3.


### <a name="issue"></a>Problème 
Échec du déploiement du compte Azure Purview avec le point de terminaison privé avec l’erreur de validation Azure Policy pendant le déploiement.

### <a name="cause"></a>Cause
Cette erreur suggère qu’il existe peut-être une affectation Azure Policy existante sur votre abonnement Azure qui empêche le déploiement des ressources Azure requises. 

### <a name="resolution"></a>Résolution 
Passez en revue vos affectations Azure Policy existantes et assurez-vous que le déploiement des ressources Azure suivantes est autorisé dans votre abonnement Azure. 
   
> [!NOTE]
> Selon votre scénario, vous devrez peut-être déployer un ou plusieurs des types de ressources Azure suivants : 
>  -   Azure Purview (Microsoft.Purview/Accounts)
>  -   Point de terminaison privé (Microsoft.Network/privateEndpoints)
>  -   Zones DNS privé (Microsoft.Network/privateDnsZones)
>  -   Espace de noms Event Hub (Microsoft.EventHub/namespaces)
>  -   Compte de stockage (Microsoft.Storage/storageAccounts)


### <a name="issue"></a>Problème
Non autorisé à accéder à ce compte Purview. Ce compte Purview est derrière un point de terminaison privé. Accédez au compte à partir d’un client dans le même réseau virtuel (VNet) que celui configuré pour le point de terminaison privé du compte Purview.

### <a name="cause"></a>Cause 
L’utilisateur tente de se connecter à Azure Purview à partir d’un point de terminaison public ou à l’aide de points de terminaison publics Azure Purview où **Accès au réseau public** est défini sur **Refuser**.

### <a name="resolution"></a>Résolution
Dans ce cas, pour ouvrir Azure Purview Studio, utilisez soit une machine qui est déployée dans le même réseau virtuel que le point de terminaison privé du Portail Azure Purview, soit une machine virtuelle qui est connectée à votre CorpNet dans lequel la connectivité hybride est autorisée.

### <a name="issue"></a>Problème
Vous pouvez recevoir le message d’erreur suivant lors de l’analyse d’un serveur SQL utilisant un runtime d’intégration auto-hébergé :

  `Message=This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms`

### <a name="cause"></a>Cause 
L’ordinateur du runtime d’intégration auto-hébergé a activé le mode FIPS.
FIPS (Federal Information Processing Standards) définit un certain ensemble d’algorithmes de chiffrement qui peuvent être utilisés. Lorsque le mode FIPS est activé sur l’ordinateur, certaines classes de chiffrement dont dépendent les processus appelés sont bloquées dans certains scénarios.

### <a name="resolution"></a>Résolution
Désactivez le mode FIPS sur le serveur d’intégration auto-hébergé.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne pouvez pas le résoudre, accédez au support en visitant l’un des canaux suivants :

- Obtenez des réponses d’experts par le biais de [Microsoft Q&A](/answers/topics/azure-purview.html).
- Contactez [@AzureSupport](https://twitter.com/azuresupport). Cette ressource Microsoft Azure officielle sur Twitter aide à améliorer l’expérience client en orientant la communauté Azure vers les réponses, le support et les experts appropriés.
- Si vous avez encore besoin d’aide, accédez au [site de support Azure](https://azure.microsoft.com/support/options/) et sélectionnez **Soumettre une demande de support**.
