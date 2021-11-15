---
title: Se connecter à Azure Purview et analyser des sources de données de manière privée et sécurisée
description: Cet article décrit comment vous pouvez configurer un point de terminaison privé pour vous connecter à votre compte Purview et analyser les sources de données du réseau restreint pour un isolement de bout en bout.
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 2d0a74b5674bba6850cbe38f1811c1204acb5e69
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131848208"
---
# <a name="connect-to-your-azure-purview-and-scan-data-sources-privately-and-securely"></a>Se connecter à Azure Purview et analyser des sources de données de manière privée et sécurisée

Dans ce guide, vous allez apprendre à déployer des points de terminaison privés de _compte_, de _portail_ et d’_ingestion_ pour votre compte Azure Purview afin d’accéder au compte Purview et d’analyser des sources de données à l’aide d’un runtime d’intégration auto-hébergé en toute sécurité et de façon privée, ce qui permet l’isolement du réseau de bout en bout.

Le point de terminaison privé du _compte_ Azure Purview est utilisé pour ajouter une couche de sécurité supplémentaire en permettant des scénarios où seuls les appels client provenant du réseau virtuel sont autorisés à accéder au compte Azure Purview. Ce point de terminaison privé est également un prérequis pour le point de terminaison privé du portail.

Le point de terminaison privé du _portail_ Azure Purview est nécessaire pour permettre la connectivité à [Azure Purview Studio](https://web.purview.azure.com/resource/) via un réseau privé.

Azure Purview peut analyser des sources de données dans un environnement Azure ou local à l’aide de points de terminaison privés d’_ingestion_. Trois ressources de point de terminaison privé supplémentaires doivent être déployées et liées aux ressources managées Azure Purview quand des points de terminaison privés d’ingestion sont déployés :

 - Le point de terminaison privé d’objet blob est lié à un compte de stockage géré Azure Purview.
 - Le point de terminaison privé de file d’attente est lié à un compte de stockage managé Azure Purview.
 - Le point de terminaison privé de l’espace de noms est lié à l’espace de noms Event Hub managé Azure Purview.

  :::image type="content" source="media/catalog-private-link/purview-private-link-architecture.png" alt-text="Le diagramme qui affiche l’architecture d’Azure Purview et Private Link.":::

## <a name="deployment-checklist"></a>Liste de vérification de déploiement
À l’aide de l’une des options de déploiement expliquées plus loin dans ce guide, vous pouvez déployer un nouveau compte Azure Purview avec des points de terminaison privés de _compte_, de _portail_ et d’_ingestion_, ou vous pouvez choisir de déployer ces points de terminaison privés pour un compte Azure Purview existant :

1. Choisissez un réseau virtuel Azure approprié et un sous-réseau pour déployer des points de terminaison privés Azure Purview. Sélectionnez l’une des options suivantes :
   - Déployez un [nouveau réseau virtuel](../virtual-network/quick-create-portal.md) dans votre abonnement Azure.
   - Localisez un réseau virtuel Azure existant et un sous-réseau dans votre abonnement Azure.
  
2. Définissez une [méthode de résolution de noms DNS](./catalog-private-link-name-resolution.md#deployment-options), de sorte que vous puissiez accéder au compte Azure Purview et analyser des sources de données à l’aide d’un réseau privé. Vous pouvez utiliser une des options suivantes :
   - Déployez de nouvelles zones Azure DNS à l’aide des étapes décrites plus loin dans ce guide.
   - Ajoutez les enregistrements DNS requis aux zones Azure DNS existantes à l’aide des étapes décrites plus loin dans ce guide.
   - Après avoir effectué les étapes de ce guide, ajoutez manuellement les enregistrements A DNS requis dans vos serveurs DNS existants.
3. Déployez un [nouveau compte Purview](#option-1---deploy-a-new-azure-purview-account-with-account-portal-and-ingestion-private-endpoints) avec des points de terminaison privés de compte, de portail et d’ingestion, ou déployez des points de terminaison privés pour un [compte Purview existant](#option-2---enable-account-portal-and-ingestion-private-endpoint-on-existing-azure-purview-accounts).
4. [Activez l’accès à Azure Active Directory](#enable-access-to-azure-active-directory) si votre réseau privé a des règles de groupe de sécurité réseau définies sur refuser pour l’ensemble du trafic Internet public.
5. Déployez et inscrivez le [runtime d’intégration auto-hébergé](#deploy-self-hosted-integration-runtime-ir-and-scan-your-data-sources) à l’intérieur du même réseau virtuel où les points de terminaison privés d’ingestion Azure Purview sont déployés.
6. À la fin de ce guide, ajustez les configurations DNS si nécessaire.
7. Validez votre réseau et la résolution de noms entre l’ordinateur de gestion, les machines virtuelles IR auto-hébergées et les sources de données sur Azure Purview. 

## <a name="option-1---deploy-a-new-azure-purview-account-with-_account_-_portal_-and-_ingestion_-private-endpoints"></a>Option 1 : déployer un nouveau compte Azure Purview avec le point de terminaison privé de _compte_, de _portail_ et d’_ingestion_

1. Accédez au [Portail Azure](https://portal.azure.com), puis accédez à la page **Comptes Purview**. Sélectionnez **+ Créer** afin de créer un compte Azure Purview.

2. Entrez les informations de base, puis, sous l’onglet **Mise en réseau**, définissez la méthode de connectivité sur **Point de terminaison privé**. Définissez le point de terminaison privé sur **Compte, portail et ingestion**.

3. Sous **Compte et portail**, sélectionnez **+ Ajouter** afin d’ajouter un point de terminaison privé pour votre compte Azure Purview.

   :::image type="content" source="media/catalog-private-link/purview-pe-deploy-end-to-end.png" alt-text="Capture d’écran qui montre les sélections de pages de bout en bout de la création de points de terminaison privés.":::

4. Sur la page **Créer un point de terminaison privé**, pour la **sous-ressource Purview**, choisissez votre emplacement, fournissez un nom pour le point de terminaison privé du _compte_ et sélectionnez **compte**. Sous **mise en réseau**, sélectionnez votre réseau virtuel et votre sous-réseau, puis, si vous le souhaitez, sélectionnez **Intégrer à une zone DNS privé** pour créer une zone DNS privé Azure. 

   :::image type="content" source="media/catalog-private-link/purview-pe-deploy-account.png" alt-text="Capture d’écran montrant la page créer un point de terminaison privé de compte.":::

   > [!NOTE]
   > Vous pouvez également utiliser vos zones Azure DNS privé existantes ou créer des enregistrements DNS dans vos serveurs DNS manuellement par la suite. Pour plus d’informations, consultez [Configurer la résolution de noms DNS pour les points de terminaison privés](./catalog-private-link-name-resolution.md).

5. Sélectionnez **OK**.
   
6. Sous l’Assistant **Compte et portail**, sélectionnez à nouveau **+Ajouter** pour ajouter un point de terminaison privé de _portail_. 
  
7. Sur la page **Créer un point de terminaison privé**, pour la **sous-ressource Purview**, choisissez votre emplacement, fournissez un nom pour le point de terminaison privé du _portail_ et sélectionnez **portail**. Sous **mise en réseau**, sélectionnez votre réseau virtuel et votre sous-réseau, puis, si vous le souhaitez, sélectionnez **Intégrer à une zone DNS privé** pour créer une zone DNS privé Azure. 

   :::image type="content" source="media/catalog-private-link/purview-pe-deploy-portal.png" alt-text="Capture d’écran montrant la page de création d’un point de terminaison privé de portail.":::
   
   > [!NOTE]
   > Vous pouvez également utiliser vos zones Azure DNS privé existantes ou créer des enregistrements DNS dans vos serveurs DNS manuellement par la suite. Pour plus d’informations, consultez [Configurer la résolution de noms DNS pour les points de terminaison privés](./catalog-private-link-name-resolution.md).

8. Sélectionnez **OK**.

9.  Sous **Ingestion**, configurez vos points de terminaison privés d’ingestion en fournissant des détails pour **Abonnement**, **Réseau virtuel** et **Sous-réseau** que vous souhaitez appairer avec votre point de terminaison privé.

10. Si vous le souhaitez, sélectionnez **Intégration de DNS privé** pour utiliser les zones DNS privé Azure.
   
      :::image type="content" source="media/catalog-private-link/purview-pe-deploy-ingestion.png" alt-text="Capture d’écran montrant la page de présentation de la création d’un point de terminaison privé.":::

      > [!IMPORTANT]
      > Il est important de sélectionner les zones de DNS privé Azure appropriées pour permettre une résolution de nom correcte entre Azure Purview et les sources de données. Vous pouvez également utiliser vos zones Azure DNS privé existantes ou créer des enregistrements DNS dans vos serveurs DNS manuellement par la suite. Pour plus d’informations, consultez [Configurer la résolution de noms DNS pour les points de terminaison privés](./catalog-private-link-name-resolution.md).

11. Sélectionnez **Vérifier + créer**. Sur la page **Revoir + créer**, Azure valide votre configuration.

12. Quand le message « Validation réussie » s’affiche, sélectionnez **Créer**.
   

## <a name="option-2---enable-_account_-_portal_-and-_ingestion_-private-endpoint-on-existing-azure-purview-accounts"></a>Option 2 : activer les points de terminaison privé de _compte_, de _portail_ et d’_ingestion_ sur les comptes Azure Purview existants

1. Accédez au [portail Azure](https://portal.azure.com), sélectionnez votre compte Azure Purview, puis sous **Paramètres**, sélectionnez **Réseau** et ensuite **Connexions de point de terminaison privé**.

    :::image type="content" source="media/catalog-private-link/purview-pe-add-to-existing.png" alt-text="Capture d’écran montrant comment créer un point de terminaison privé de compte.":::

2. Sélectionnez **+ Point de terminaison privé** pour créer un point de terminaison privé.

3. Entrez les informations de base.

4. Sous l’onglet **Ressource**, pour **Type de ressource**, sélectionnez **Microsoft.Purview/accounts**.

5. Pour **Ressource**, sélectionnez le compte Azure Purview et pour **Sous-ressource cible**, sélectionnez **compte**.

6. Sous l’onglet **Configuration**, sélectionnez le réseau virtuel et, si vous le souhaitez, sélectionnez Zone DNS privé Azure pour créer une zone DNS Azure.
   
   > [!NOTE]
   > Pour la configuration DNS, vous pouvez également utiliser vos zones DNS privé Azure existantes à partir de la liste déroulante ou ajouter manuellement les enregistrements DNS requis à vos serveurs DNS ultérieurement. Pour plus d’informations, consultez [Configurer la résolution de noms DNS pour les points de terminaison privés](./catalog-private-link-name-resolution.md).

7. Accédez à la page de résumé, puis sélectionnez **Créer** pour créer le point de terminaison privé du portail.
   
8. Suivez les mêmes étapes lorsque vous sélectionnez le **portail** pour la **Sous-ressource cible**.
   
9. À partir de votre compte Azure Purview, sous **Paramètres**, sélectionnez **Mise en réseau**, puis sélectionnez **Connexions de point de terminaison privé d’ingestion**.

10. Sous Connexions de point de terminaison privé d’ingestion, sélectionnez **+ Nouveau** pour créer un point de terminaison privé d’ingestion.

      :::image type="content" source="media/catalog-private-link/purview-pe-add-ingestion-to-existing.png" alt-text="Capture d’écran qui montre comment ajouter un point de terminaison privé à un compte existant.":::

11. Renseignez les informations de base, en sélectionnant votre réseau virtuel existant et les détails du sous-réseau. Si vous le souhaitez, sélectionnez **Intégration de DNS privé** pour utiliser les zones DNS privé Azure. Sélectionnez les zones DNS privé Azure appropriées dans chaque liste.

      > [!NOTE]
      > Vous pouvez également utiliser vos zones Azure DNS privé existantes ou créer des enregistrements DNS dans vos serveurs DNS manuellement par la suite. Pour plus d’informations, consultez [Configurer la résolution de noms DNS pour les points de terminaison privés](./catalog-private-link-name-resolution.md).

12. Sélectionnez **Créer** pour terminer le processus.

## <a name="enable-access-to-azure-active-directory"></a>Activer l’accès à Azure Active Directory

> [!NOTE]
> Si votre machine virtuelle, votre passerelle VPN ou votre passerelle Peering de réseau virtuel dispose d’un accès Internet public, elle peut accéder au Portail Azure Purview et au compte Azure Purview activé avec des points de terminaison privés. Pour cette raison, vous n’êtes pas obligé de suivre le reste des instructions. Si votre réseau privé a des règles de groupe de sécurité réseau définies pour refuser tout trafic Internet public, vous allez devoir ajouter des règles pour activer l’accès à Azure Active Directory (Azure AD). Pour ce faire, suivez les instructions.

Les instructions permettent d’accéder à Azure Purview en toute sécurité à partir d’une machine virtuelle Azure. Vous devez suivre des étapes similaires si vous utilisez un VPN ou d’autres passerelles de Peering de réseaux virtuels.

1. Accédez à votre machine virtuelle dans le Portail Azure, puis, sous **Paramètres**, sélectionnez **Mise en réseau**. Sélectionnez ensuite **Règles de port de sortie** > **Ajouter une règle de port de sortie**.

   :::image type="content" source="media/catalog-private-link/outbound-rule-add.png" alt-text="Capture d’écran montrant l’ajout d’une règle de trafic sortant.":::

1. Dans le volet **Ajouter une règle de sécurité de trafic sortant** :

   1. Sous **Destination**, sélectionnez **Étiquette de service**.
   1. Sous **Étiquette de service de destination**, sélectionnez **AzureActiveDirectory**.
   1. Sous **Plages de port de destination**, sélectionnez *.
   1. Sous **Action**, sélectionnez **Autoriser**.
   1. Sous **Priorité**, la valeur doit être supérieure à la règle qui a refusé tout le trafic Internet.
   
   Créez la règle.

   :::image type="content" source="media/catalog-private-link/outbound-rule-details.png" alt-text="Capture d’écran montrant l’ajout des détails d’une règle de trafic sortant.":::

1. Suivez les mêmes étapes pour créer une autre règle afin d’autoriser l’étiquette de service **AzureResourceManager**. Si vous avez besoin d’accéder au Portail Azure, vous pouvez également ajouter une règle pour l’étiquette de service **AzurePortal**.

1. Connectez-vous à la machine virtuelle et ouvrez le navigateur. Accédez à la console du navigateur en sélectionnant Ctrl+Maj+J et basculez vers l’onglet réseau pour surveiller les requêtes réseau. Entrez web.purview.azure.com dans la zone URL, puis essayez de vous connecter à l’aide de vos informations d’identification Azure AD. Il est probable que la connexion échoue et, sous l’onglet **Réseau** dans la console, vous pouvez voir Azure AD tenter d’accéder à aadcdn.msauth.net mais être bloqué.

   :::image type="content" source="media/catalog-private-link/login-fail.png" alt-text="Capture d’écran montrant les détails de l’échec de la connexion.":::

1. Dans ce cas, ouvrez une invite de commandes sur la machine virtuelle, effectuez un test ping sur aadcdn.msauth.net, récupérez son adresse IP, puis ajoutez une règle de port de sortie pour l’adresse IP dans les règles de sécurité réseau de la machine virtuelle. Définissez la **Destination** sur **Adresses IP** et définissez les **Adresses IP de destination** sur l’adresse IP aadcdn. À cause d’Azure Load Balancer et d’Azure Traffic Manager, l’adresse IP du réseau de distribution de contenu d’Azure AD peut être dynamique. Une fois que vous avez obtenu son adresse IP, il est préférable de l’ajouter dans le fichier hôte de la machine virtuelle pour forcer le navigateur à visiter cette adresse IP afin d’obtenir le réseau de distribution de contenu Azure AD.

   :::image type="content" source="media/catalog-private-link/ping.png" alt-text="Capture d’écran montrant le test Ping.":::

   :::image type="content" source="media/catalog-private-link/aadcdn-rule.png" alt-text="Capture d’écran montrant la règle du réseau de distribution de contenu Azure AD.":::

1. Une fois la nouvelle règle créée, revenez à la machine virtuelle et essayez de vous connecter à l’aide de vos informations d’identification Azure AD. Si la connexion réussit, le portail Azure Purview est prêt à être utilisé. Toutefois, dans certains cas, Azure AD effectue une redirection vers d’autres domaines pour se connecter en fonction du type de compte d’un client. Par exemple, pour un compte live.com, Azure AD effectue une redirection vers live.com pour se connecter, puis ces requêtes sont à nouveau bloquées. Pour les comptes d’employés Microsoft, Azure AD accède à msft.sts.microsoft.com pour obtenir des informations de connexion.

   Vérifiez les requêtes de mise en réseau sous l’onglet **Mise en réseau** du navigateur pour afficher les requêtes du domaine qui sont bloquées, recommencez l’étape précédente pour obtenir son adresse IP, puis ajoutez des règles de port de sortie dans le groupe de sécurité réseau afin d’autoriser les requêtes pour cette adresse IP. Si possible, ajoutez l’URL et l’adresse IP au fichier hôte de la machine virtuelle pour corriger la résolution DNS. Si vous connaissez les plages exactes d’adresses IP du domaine de connexion, vous pouvez également les ajouter directement dans les règles de mise en réseau.

1. Votre connexion Azure AD doit maintenant réussir. Le portail Azure Purview va se charger correctement, mais la liste de tous les comptes Azure Purview ne s’affichera pas, car il ne peut accéder qu’à un compte Azure Purview spécifique. Entrez `web.purview.azure.com/resource/{PurviewAccountName}` pour accéder directement au compte Azure Purview pour lequel vous avez correctement configuré un point de terminaison privé.

## <a name="deploy-self-hosted-integration-runtime-ir-and-scan-your-data-sources"></a>Déployez le runtime d’intégration auto-hébergé (IR) et analysez vos sources de données.
Une fois que vous avez déployé des points de terminaison privés d’ingestion pour votre Azure Purview, vous devez configurer et inscrire au moins un runtime d’intégration auto-hébergé (IR) :

- Tous les types de sources locales, tels que Microsoft SQL Server, Oracle, SAP et autres, ne sont actuellement pris en charge que via des analyses basées sur un IR auto-hébergé. L’IR auto-hébergé doit s’exécuter au sein de votre réseau privé, et être appairé avec votre réseau virtuel dans Azure. 
   
- Pour tous les types de sources Azure comme Stockage Blob Azure et Azure SQL Database, vous devez explicitement choisir d’exécuter l’analyse à l’aide d’un runtime d’intégration auto-hébergé qui est déployé dans le même réseau virtuel que le point de terminaison privé d’ingestion Azure Purview. 

Suivez les étapes dans [Création et gestion d’un runtime d’intégration auto-hébergé](manage-integration-runtimes.md) pour configurer un IR auto-hébergé. Configurez ensuite votre analyse sur la source Azure en sélectionnant cet IR auto-hébergé dans la liste déroulante **Se connecter via le runtime d’intégration** pour garantir l’isolement réseau.
    
   :::image type="content" source="media/catalog-private-link/shir-for-azure.png" alt-text="Capture d’écran montrant l’exécution d’une analyse Azure à l’aide du runtime d’intégration auto-hébergé.":::

> [!IMPORTANT]
> Veillez à télécharger et à installer la dernière version du runtime d’intégration auto-hébergé à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=39717).

## <a name="firewalls-to-restrict-public-access"></a>Pare-feu pour restreindre l’accès public

Pour couper complètement l’accès au compte Azure Purview à partir de l’Internet public, procédez comme suit. Ce paramétrage s’applique tant aux connexions de point de terminaison privé qu’aux connexions de point de terminaison privé d’ingestion.

1. Accédez au compte Azure Purview à partir du Portail Azure, et sous **Paramètres** > **Mise en réseau**, sélectionnez **Connexions de point de terminaison privé**.

1. Accédez à l’onglet **Pare-feu** et vérifiez que le bouton bascule est défini sur **Refuser**.

   :::image type="content" source="media/catalog-private-link/private-endpoint-firewall.png" alt-text="Capture d’écran montrant les paramètres du pare-feu du point de terminaison privé.":::

## <a name="next-steps"></a>Étapes suivantes

-  [Vérifier la résolution des points de terminaison privés](./catalog-private-link-name-resolution.md)
-  [Gérer des sources de données dans Azure Purview](./manage-data-sources.md)
-  [Résolution des problèmes de configuration des point de terminaison privés pour votre compte Azure Purview](./catalog-private-link-troubleshoot.md)
