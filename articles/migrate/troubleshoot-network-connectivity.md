---
title: Résoudre les problèmes de connectivité réseau | Microsoft Docs
description: Fournit des conseils de dépannage pour les erreurs courantes lors de l’utilisation de Azure Migrate avec des points de terminaison privés.
author: SGSneha
ms.author: v-ssudhir
ms.manager: deseelam
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/03/2021
ms.openlocfilehash: 4c23367862728d42148971b4941d6c94a750c227
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131504604"
---
# <a name="troubleshoot-network-connectivity"></a>Résoudre les problèmes de connectivité réseau
Cet article vous aide à résoudre les problèmes de connectivité réseau à l’aide d’Azure Migrate avec des points de terminaison privés.

## <a name="validate-private-endpoints-configuration"></a>Valider la configuration des points de terminaison privés

Assurez-vous que le point de terminaison privé présente l’état Approuvé.  

1. Accédez à **Azure Migrate** : pages **Découverte et évaluation** et **Migration de serveur**.

2. La page de propriétés contient la liste des points de terminaison privés et des FQDN de liaison privée créés automatiquement par Azure Migrate.  

3. Sélectionnez le point de terminaison privé que vous souhaitez diagnostiquer.  
      a. Vérifiez que l’état de la connexion indique Approuvée.           
      b. Si la connexion présente l’état En attente, vous devez l’approuver.                         
      c. Vous pouvez également accéder à la ressource de point de terminaison privé et vérifier si le réseau virtuel correspond au réseau virtuel de point de terminaison privé du projet Migrate.                                                        

     ![Afficher une connexion de point de terminaison privé](./media/how-to-use-azure-migrate-with-private-endpoints/private-endpoint-connection.png)


## <a name="validate-the-data-flow-through-the-private-endpoints"></a>Valider le flux de données via les points de terminaison privés
Passez en revue les métriques de flux de données pour vérifier le flux de trafic via les points de terminaison privés. Sélectionnez le point de terminaison privé dans la page Azure Migrate : évaluation du serveur et propriétés Server Migration. Ce faisant, vous êtes redirigé vers la section de présentation des points de terminaison privés dans le centre Azure Private Link. Dans le menu de gauche, sélectionnez **Métriques** pour afficher les informations _Octets de données entrants_ et _Octets de données sortants_ et visualiser le flux de trafic.

## <a name="verify-dns-resolution"></a>Vérifier la résolution de DNS

L’appliance locale (ou le fournisseur de réplication) accède aux ressources Azure Migrate à l’aide de leur nom de domaine de liaison privée complet (FQDN). Vous pouvez avoir besoin de paramètres DNS supplémentaires pour résoudre l’adresse IP privée du point de terminaison privé à partir de l’environnement source. [Consultez cet article](../private-link/private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder) permet de comprendre les scénarios de configuration DNS qui peuvent vous aider à résoudre les problèmes de connectivité réseau.  

Pour valider la connexion de liaison privée, effectuez une résolution DNS des points de terminaison de ressource d’Azure Migrate (FQDN de ressource de liaison privée) à partir du serveur local hébergeant l’appliance Migrate, et assurez-vous qu’elle soit résolue vers une adresse IP privée.
Les informations sur les détails du point de terminaison privé et sur les FQDN de la ressource de liaison privée sont disponibles dans les pages de propriétés Découverte et évaluation et Migration de serveur. Sélectionnez **Télécharger les paramètres DNS** pour afficher la liste.   

 ![Propriétés Azure Migrate : découverte et évaluation](./media/how-to-use-azure-migrate-with-private-endpoints/server-assessment-properties.png)

 [![Propriétés Azure Migrate : migration de serveur](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-server-migration-properties-inline.png)](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-server-migration-properties-expanded.png#lightbox)

Exemple illustrant la résolution DNS du FQDN de la liaison privée du compte de stockage.  

-  Entrez _nslookup ```<storage-account-name>_.blob.core.windows.net.``` ; remplacez ```<storage-account-name>``` par le nom du compte de stockage utilisé pour Azure Migrate.  

    Vous recevrez un message similaire à celui ci :  

   ![Exemple de résolution DNS](./media/how-to-use-azure-migrate-with-private-endpoints/dns-resolution-example.png)

-  L’adresse IP privée 10.1.0.5 est retournée pour le compte de stockage. Cette adresse appartient au sous-réseau du réseau virtuel du point de terminaison privé.   

Vous pouvez vérifier la résolution DNS pour d’autres artefacts Azure Migrate à l’aide d’une approche similaire.   

Si la résolution DNS est incorrecte, procédez comme suit :  

**Recommandé** pour les tests : Vous pouvez mettre à jour manuellement les enregistrements DNS de votre environnement source en modifiant le fichier des hôtes DNS sur votre appliance locale avec les FQDN des ressources de liaison privée et leurs adresses IP privées associées.
- Si vous utilisez un DNS personnalisé, vérifiez vos paramètres DNS personnalisés et confirmez que la configuration DNS est correcte. Pour obtenir de l’aide, consultez [Vue d’ensemble du point de terminaison privé : configuration DNS](../private-link/private-endpoint-overview.md#dns-configuration).
- Si vous utilisez des serveurs DNS fournis par Azure, reportez-vous à la section ci-dessous pour une résolutions des problèmes plus approfondie.  

> [!Tip]
> Vous pouvez mettre à jour manuellement les enregistrements DNS de votre environnement source en modifiant le fichier hôte DNS sur votre appliance locale avec les FQDN des ressources de liaison privée et leurs adresses IP privées associées. Cette option n’est recommandée qu’à des fins de test. <br/>  


## <a name="validate-the-private-dns-zone"></a>Valider la zone DNS privée   
Si la résolution DNS ne fonctionne pas comme décrit dans la section précédente, il peut y avoir un problème avec votre zone DNS privée.  

### <a name="confirm-that-the-required-private-dns-zone-resource-exists"></a>Confirmer l’existence de la ressource de zone DNS privée requise  
Par défaut, Azure Migrate crée également une zone DNS privée correspondant au sous-domaine *privatelink* pour chaque type de ressource. La zone DNS privée est créée dans le même groupe de ressources Azure que le groupe de ressources du point de terminaison privé. Le groupe de ressources Azure doit contenir des ressources de zone DNS privée au format suivant :
-  pprivatelink.vaultcore.azure.net pour le coffre de clés
-  privatelink.blob.core.windows.net pour le compte de stockage
-  privatelink.siterecovery.windowsazure.com pour le coffre recovery services (pour les réplications Hyper-V et basées sur un agent)
-  privatelink.prod.migration.windowsazure.com : migrez un projet, un projet d’évaluation et un site de détection.   

Azure Migrate crée automatiquement la zone DNS privée (sauf pour le compte de stockage de cache/de réplication sélectionné par l’utilisateur). Vous pouvez localiser la zone DNS privée liée en accédant à la page du point de terminaison privé et en sélectionnant les configurations DNS. Ici, vous devriez voir la zone DNS privée sous la section intégration DNS privée.

[![Capture d’écran de la configuration DNS](./media/how-to-use-azure-migrate-with-private-endpoints/dns-configuration-inline.png)](./media/how-to-use-azure-migrate-with-private-endpoints/dns-configuration-expanded.png#lightbox)

Si la zone DNS n’est pas présente (comme indiqué ci-dessous), [créez une nouvelle ressource de zone DNS privée.](../dns/private-dns-getstarted-portal.md)  

[![Créer une zone DNS privée](./media/how-to-use-azure-migrate-with-private-endpoints/create-dns-zone-inline.png)](./media/how-to-use-azure-migrate-with-private-endpoints/create-dns-zone-expanded.png#lightbox)

### <a name="confirm-that-the-private-dns-zone-is-linked-to-the-virtual-network"></a>Vérifier que la zone DNS privée est liée au réseau virtuel  
La zone DNS privée doit être liée au réseau virtuel qui contient le point de terminaison privé pour que la requête DNS résolve l’adresse IP privée du point de terminaison de la ressource. Si la zone DNS privée n’est pas liée au réseau virtuel approprié, toute résolution DNS à partir de ce réseau virtuel ignore la zone DNS privée.   

Accédez à la ressource de zone DNS privée dans le portail Azure et cliquez sur les liens du réseau virtuel à partir du menu. Vous devez voir les réseaux virtuels liés.

[![Voir les liens de réseau virtuel](./media/how-to-use-azure-migrate-with-private-endpoints/virtual-network-links-inline.png)](./media/how-to-use-azure-migrate-with-private-endpoints/virtual-network-links-expanded.png#lightbox)

Cette opération affiche une liste de liens, chacun avec le nom d’un réseau virtuel dans votre abonnement. Le réseau virtuel qui contient la ressource de point de terminaison privé doit être répertorié ici. Sinon, [suivez cet article](../dns/private-dns-getstarted-portal.md#link-the-virtual-network) pour lier la zone DNS privée à un réseau virtuel.    

Une fois la zone DNS privée liée au réseau virtuel, les requêtes DNS provenant du réseau virtuel recherchent des enregistrements DNS dans la zone DNS privée. C’est obligatoire pour une résolution d’adresse correcte sur le réseau virtuel sur lequel le point de terminaison privé a été créé.   

### <a name="confirm-that-the-private-dns-zone-contains-the-right-a-records"></a>Confirmer que la zone DNS privée contient l’enregistrement A approprié

Accédez à la zone DNS privée que vous souhaitez dépanner. La page de présentation affiche tous les enregistrements DNS pour cette zone DNS privée. Vérifiez qu’un enregistrement A DNS existe pour la ressource. La valeur de l’enregistrement A (l’adresse IP) doit être l’adresse IP privée des ressources. Si vous trouvez l’enregistrement A mais qu’il contient une adresse IP incorrecte, vous devez supprimer l’adresse IP incorrecte et en ajouter une nouvelle. Il est recommandé de supprimer l’intégralité de l’enregistrement A et d’en ajouter un nouveau, puis d’effectuer un vidage DNS sur l’appliance source locale.   

Exemple illustrant l’enregistrement A DNS d’un compte de stockage dans la zone DNS privée :

   ![Enregistrements DNS](./media/how-to-use-azure-migrate-with-private-endpoints/dns-a-records.png)   

Exemple illustrant les enregistrements A DNS pour les microservices du coffre Recovery Services dans la zone DNS privée :

   [![Enregistrements DNS pour le coffre Recovery Services](./media/how-to-use-azure-migrate-with-private-endpoints/rsv-a-records-inline.png)](./media/how-to-use-azure-migrate-with-private-endpoints/rsv-a-records-expanded.png#lightbox)  

>[!Note]
> Lorsque vous supprimez ou modifiez un enregistrement A, la machine peut toujours résoudre l’ancienne adresse IP car la TTL (durée de vie) n’a peut-être pas encore expiré.  

### <a name="items-that-may-affect-private-link-connectivity"></a>Éléments susceptibles d’affecter la connectivité de liaison privée  

Il s’agit d’une liste non exhaustive d’éléments qui peuvent être présents dans des scénarios avancés ou complexes :

-  Les paramètres du Pare-feu, qu’il s’agisse du Pare-feu Azure connecté au réseau virtuel ou d’une solution de pare-feu personnalisée déployée dans la machine de l’appliance.  
-  Le Peering de réseau, qui peut avoir un impact sur les serveurs DNS utilisés et sur la façon dont le trafic est acheminé.  
-  Des solutions de passerelle personnalisée (NAT) peuvent avoir un impact sur la façon dont le trafic est acheminé, notamment le trafic des requêtes DNS.

Pour plus d’informations, consultez le [guide Résoudre les problèmes de connectivité Private Endpoint.](../private-link/troubleshoot-private-endpoint-connectivity.md)  

## <a name="common-issues-while-using-azure-migrate-with-private-endpoints"></a>Problèmes courants lors de l’utilisation d’Azure Migrate avec des points de terminaison privés
Dans cette section, nous allons lister quelques-uns des problèmes les plus fréquents et suggérer des étapes de dépannage à effectuer vous-même pour résoudre le problème.

### <a name="appliance-registration-fails-with-the-error-forbiddentoaccesskeyvault"></a>L’inscription de l’appliance échoue avec l’erreur ForbiddenToAccessKeyVault
L’opération de création ou de mise à jour du coffre de clés Azure pour <_Nom_coffre_clés_> a échoué en raison de l’erreur <_Message_erreur_>

#### <a name="possible-causes"></a>Causes possibles :
Ce problème peut se produire si le compte Azure utilisé pour inscrire l’appliance ne dispose pas des autorisations nécessaires ou si l’appliance Azure Migrate ne peut pas accéder au coffre de clés.

#### <a name="remediation"></a>Correction :

**Étapes de dépannage des problèmes d’accès au coffre de clés :**
1. Vérifiez que le compte d’utilisateur Azure utilisé pour inscrire l’appliance dispose au moins d’autorisations Contributeur pour l’abonnement.
1. Vérifiez que l’utilisateur qui tente d’inscrire l’appliance a accès au coffre de clés et qu’une stratégie d’accès lui est affectée dans la section Coffre de clés>Stratégie d’accès. [En savoir plus](../key-vault/general/assign-access-policy-portal.md)
-  [Apprenez-en davantage](./migrate-appliance.md#appliance---vmware) sur les rôles et autorisations Azure requis.

**Étapes pour résoudre les problèmes de connectivité au coffre de clés :** Si vous avez activé l’appliance pour la connectivité de point de terminaison privé, effectuez les étapes suivantes pour résoudre les problèmes de connectivité réseau :
-  Vérifiez que l’appliance est hébergée dans le même réseau virtuel ou qu’elle est connectée au réseau virtuel Azure cible (où le point de terminaison privé du coffre de clés a été créé) via une liaison privée. Le point de terminaison privé du coffre de clés est créé dans le réseau virtuel sélectionné lors de l’expérience de création du projet. Vous pouvez vérifier les détails du réseau virtuel dans la page **Azure Migrate > Propriétés**.
   ![Propriétés d’Azure Migrate](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-properties-page.png)  

-  Vérifiez que l’appliance dispose d’une connectivité réseau avec le coffre de clés via une liaison privée. Pour vérifier la connectivité de la liaison privée, effectuez une résolution DNS du point de terminaison de la ressource de coffre de clés depuis le serveur local hébergeant l’appliance, et vérifiez qu’il est résolu en une adresse IP privée.
-  Accédez à **Azure Migrate : Découverte et évaluation > Propriétés** pour trouver les détails des points de terminaison privés pour des ressources comme le coffre de clés créé lors de l’étape de génération des clés.  

      ![Propriétés de l’évaluation de serveur d’Azure Migrate](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-server-assessment-properties.png)  
-  Sélectionnez **Télécharger les paramètres DNS** pour télécharger les mappages DNS.

      ![Télécharger les paramètres DNS](./media/how-to-use-azure-migrate-with-private-endpoints/download-dns-settings.png)  

-  Ouvrez la ligne de commande et exécutez la commande nslookup suivante pour vérifier la connectivité réseau avec l’URL du coffre de clés mentionnée dans le fichier des paramètres DNS.   

    ```console
    nslookup <your-key-vault-name>.vault.azure.net
    ```

    Si vous exécutez la commande ns lookup pour résoudre l’adresse IP d’un coffre de clés via un point de terminaison public, vous obtenez un résultat semblable à ceci :

    ```console
    c:\ >nslookup <your-key-vault-name>.vault.azure.net

    Non-authoritative answer:
    Name:    
    Address:  (public IP address)
    Aliases:  <your-key-vault-name>.vault.azure.net
    ```

    Si vous exécutez la commande ns lookup pour résoudre l’adresse IP d’un coffre de clés via un point de terminaison privé, vous obtenez un résultat semblable à ceci :

    ```console
    c:\ >nslookup your_vault_name.vault.azure.net

    Non-authoritative answer:
    Name:    
    Address:  10.12.4.20 (private IP address)
    Aliases:  <your-key-vault-name>.vault.azure.net
              <your-key-vault-name>.privatelink.vaultcore.azure.net
    ```

    La commande nslookup doit être résolue en une adresse IP privée comme mentionné ci-dessus. L’adresse IP privée doit correspondre à celle figurant dans le fichier des paramètres DNS.

Si la résolution DNS est incorrecte, procédez comme suit :

1. Mettez à jour manuellement les enregistrements DNS de l’environnement source en modifiant le fichier des hôtes DNS sur l’appliance locale avec les mappages DNS et les adresses IP privées associées. Cette option est recommandée pour les tests.

      ![Fichier des hôtes DNS](./media/how-to-use-azure-migrate-with-private-endpoints/dns-hosts-file-1.png)

1. Si vous utilisez un serveur DNS personnalisé, vérifiez vos paramètres DNS personnalisés et vérifiez que la configuration DNS est correcte. Pour obtenir de l’aide, consultez [Vue d’ensemble du point de terminaison privé : configuration DNS](../private-link/private-endpoint-overview.md#dns-configuration).

1. **Considérations relatives au serveur proxy** : Si l’appliance utilise un serveur proxy pour la connectivité sortante, vous devrez peut-être valider vos paramètres réseau et vos configurations pour vous assurer que les URL des liaisons privées sont accessibles et peuvent être routées comme prévu.

    - Si le serveur proxy est destiné à la connectivité Internet, vous devrez peut-être ajouter des redirecteurs de trafic ou des règles pour ignorer le serveur proxy pour les noms de domaine complets de la liaison privée. [En savoir plus](/azure/migrate/how-to-use-azure-migrate-with-private-endpoints#set-up-prerequisites) sur l’ajout de règles de contournement de proxy.
    - Si le serveur proxy est utilisé pour tout le trafic sortant, assurez-vous que le serveur proxy peut résoudre les noms de domaine complets de la liaison privée en leurs adresses IP privées respectives. Pour une solution de contournement rapide, vous pouvez mettre à jour manuellement les enregistrements DNS sur le serveur proxy avec les mappages DNS et les adresses IP privées associées, comme indiqué ci-dessus. Cette option est recommandée pour les tests.

1. Si le problème persiste, [reportez-vous à cette section](#validate-the-private-dns-zone).

Une fois que vous avez vérifié la connectivité, réessayez le processus d’inscription.

### <a name="start-discovery-fails-with-the-error-agentnotconnected"></a>Démarrer la découverte échoue avec l’erreur AgentNotConnected
L’appliance n’a pas pu lancer la détection, car l’agent local ne peut pas communiquer avec le point de terminaison de service Azure Migrate : <_Nom_URL_> dans Azure.

   ![Erreur d’agent non connecté](./media/how-to-use-azure-migrate-with-private-endpoints/agent-not-connected-error.png)  

#### <a name="possible-causes"></a>Causes possibles :
Ce problème peut se produire si l’appliance ne parvient pas à atteindre le ou les points de terminaison de service mentionnés dans le message d’erreur.

#### <a name="remediation"></a>Correction :
Vérifiez que l’appliance a une connectivité directe ou via le proxy, et qu’elle peut résoudre le point de terminaison de service fourni dans le message d’erreur.  

Si vous avez activé l’appliance pour la connectivité de point de terminaison privé, vérifiez que l’appliance est connectée au réseau virtuel Azure via une liaison privée, et qu’elle peut résoudre le ou les points de terminaison de service fournis dans le message d’erreur.

**Étapes de résolution des problèmes de connectivité de liaison privée aux points de terminaison de service Azure Migrate :**

Si vous avez activé l’appliance pour la connectivité de point de terminaison privé, effectuez les étapes suivantes pour résoudre les problèmes de connectivité réseau :

-  Vérifiez que l’appliance est hébergée dans le même réseau virtuel ou qu’elle est connectée au réseau virtuel Azure cible (où les points de terminaison privés ont été créés) via une liaison privée. Les points de terminaison privés pour les services Azure Migrate sont créés dans le réseau virtuel sélectionné lors de l’expérience de création du projet. Vous pouvez vérifier les détails du réseau virtuel dans la page **Azure Migrate > Propriétés**.

      ![Propriétés d’Azure Migrate](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-properties-page.png)   

-  Vérifiez que l’appliance a une connectivité réseau vers les URL de point de terminaison de service et les autres URL mentionnées dans le message d’erreur via une connexion de liaison privée. Pour vérifier la connectivité de la liaison privée, effectuez une résolution DNS des URL depuis le serveur local hébergeant l’appliance et vérifiez qu’elles sont résolues en adresses IP privées.
-  Accédez à **Azure Migrate : Découverte et évaluation > Propriétés** pour trouver les détails des points de terminaison privés pour les points de terminaison de service créés lors de l’étape de génération des clés. 

      ![Propriétés de l’évaluation de serveur d’Azure Migrate](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-server-assessment-properties.png)  

- Sélectionnez **Télécharger les paramètres DNS** pour télécharger les mappages DNS.

   ![Télécharger les paramètres DNS](./media/how-to-use-azure-migrate-with-private-endpoints/download-dns-settings.png)   

|**Mappages DNS contenant des URL de point de terminaison privés**  | **Détails** |
|--- | ---|
|*.disc.privatelink.test.migration.windowsazure.com | Point de terminaison du service de découverte d’Azure Migrate
|*.asm.privatelink.test.migration.windowsazure.com  | Point de terminaison du service d’évaluation d’Azure Migrate  
|*.hub.privatelink.test.migration.windowsazure.com  | Point de terminaison du hub Azure Migrate destiné à recevoir des données provenant d’autres offres de Microsoft ou d’[éditeurs de logiciels indépendants (ISV)](./migrate-services-overview.md#isv-integration) externes
|*.vault.azure.net | Point de terminaison Key Vault
|*.blob.core.windows.net | Point de terminaison de compte de stockage pour les données de dépendances et de performances  

En plus des URL ci-dessus, l’appliance doit accéder aux URL suivantes via Internet, directement ou via un proxy.

| **Autres URL de cloud public <br> (URL de point de terminaison public)** | **Détails** |
|--- | ---|
|*. portal.azure.com | Accédez au Portail Azure
|\* .windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com <br/> *.office.com <br/> *.microsoftonline.com <br/> *.microsoftonline-p.com <br/> | Utilisée par Azure Active Directory pour le contrôle d’accès et la gestion des identités
|management.azure.com | Pour déclencher des déploiements Azure Resource Manager
|*.services.visualstudio.com (facultatif) | Chargez les journaux de l’appliance utilisés pour la surveillance interne.
|aka.ms/* (facultatif) | Autoriser l’accès aux liaisons aka ; utilisé pour télécharger et installer les dernières mises à jour pour les services de d’appliance
|download.microsoft.com/download | Autoriser les téléchargements à partir du Centre de téléchargement Microsoft    

-  Ouvrez la ligne de commande et exécutez la commande nslookup suivante pour vérifier la connectivité de liaison privée avec les URL listées dans le fichier des paramètres DNS. Répétez cette étape pour toutes les URL du fichier des paramètres DNS.

    _**Illustration** : Vérification de la connectivité de liaison privée au point de terminaison du service de découverte_

    ```console
    nslookup 04b8c9c73f3d477e966c8d00f352889c-agent.cus.disc.privatelink.prod.migration.windowsazure.com
    ```
    Si la demande peut atteindre le point de terminaison du service de découverte via un point de terminaison privé, vous obtenez un résultat qui se présente comme ceci :

    ```console
    nslookup 04b8c9c73f3d477e966c8d00f352889c-agent.cus.disc.privatelink.prod.migration.windowsazure.com

    Non-authoritative answer:
    Name:    
    Address:  10.12.4.23 (private IP address)
    Aliases:  04b8c9c73f3d477e966c8d00f352889c-agent.cus.disc.privatelink.prod.migration.windowsazure.com
              prod.cus.discoverysrv.windowsazure.com
    ```

    La commande nslookup doit être résolue en une adresse IP privée comme mentionné ci-dessus. L’adresse IP privée doit correspondre à celle figurant dans le fichier des paramètres DNS.

Si la résolution DNS est incorrecte, procédez comme suit :

1. Mettez à jour manuellement les enregistrements DNS de l’environnement source en modifiant le fichier des hôtes DNS sur l’appliance locale avec les mappages DNS et les adresses IP privées associées. Cette option est recommandée pour les tests.

      ![Fichier des hôtes DNS](./media/how-to-use-azure-migrate-with-private-endpoints/dns-hosts-file-1.png)

1. Si vous utilisez un serveur DNS personnalisé, vérifiez vos paramètres DNS personnalisés et vérifiez que la configuration DNS est correcte. Pour obtenir de l’aide, consultez [Vue d’ensemble du point de terminaison privé : configuration DNS](../private-link/private-endpoint-overview.md#dns-configuration).

1. **Considérations relatives au serveur proxy** : Si l’appliance utilise un serveur proxy pour la connectivité sortante, vous devrez peut-être valider vos paramètres réseau et vos configurations pour vous assurer que les URL des liaisons privées sont accessibles et peuvent être routées comme prévu.

    - Si le serveur proxy est destiné à la connectivité Internet, vous devrez peut-être ajouter des redirecteurs de trafic ou des règles pour ignorer le serveur proxy pour les noms de domaine complets de la liaison privée. [En savoir plus](/azure/migrate/how-to-use-azure-migrate-with-private-endpoints#set-up-prerequisites) sur l’ajout de règles de contournement de proxy.
    - Si le serveur proxy est utilisé pour tout le trafic sortant, assurez-vous que le serveur proxy peut résoudre les noms de domaine complets de la liaison privée en leurs adresses IP privées respectives. Pour une solution de contournement rapide, vous pouvez mettre à jour manuellement les enregistrements DNS sur le serveur proxy avec les mappages DNS et les adresses IP privées associées, comme indiqué ci-dessus. Cette option est recommandée pour les tests.

1. Si le problème persiste, [reportez-vous à cette section](#validate-the-private-dns-zone).

Une fois que vous avez vérifié la connectivité, réessayez le processus de découverte.

### <a name="importexport-request-fails-with-the-error-403-this-request-is-not-authorized-to-perform-this-operation"></a>La demande d’importation/exportation échoue avec l’erreur « 403 : Cette demande n’est pas autorisée à effectuer cette opération » 

La demande d’exportation/importation/téléchargement du rapport échoue avec l’erreur *« 403 : Cette demande n’est pas autorisée à effectuer cette opération »* pour les projets avec une connectivité de point de terminaison privé.

#### <a name="possible-causes"></a>Causes possibles : 
Cette erreur peut se produire si la demande d’importation/exportation n’a pas été lancée depuis un réseau autorisé. Ceci peut se produire si la demande d’importation/exportation/téléchargement a été lancée depuis un client qui n’est pas connecté au service Azure Migrate (réseau virtuel Azure) via un réseau privé. 

#### <a name="remediation"></a>Correction
**Option 1** *(recommandée)*  :
  
Pour résoudre cette erreur, réessayez l’opération d’importation/exportation/téléchargement depuis un client résidant sur un réseau virtuel connecté à Azure via une liaison privée. Vous pouvez ouvrir le portail Azure sur votre réseau local ou sur la machine virtuelle de votre appliance, puis réessayer l’opération. 

**Option 2** :

La demande d’importation/exportation/téléchargement établit une connexion à un compte de stockage pour le chargement/téléchargement des rapports. Vous pouvez également modifier les paramètres réseau du compte de stockage utilisé pour l’opération d’importation/exportation/téléchargement et autoriser l’accès au compte de stockage via d’autres réseaux (réseaux publics).  

Pour configurer le compte de stockage pour la connectivité du point de terminaison public,

1. **Recherchez le compte de stockage** : Le nom du compte de stockage est disponible sur la page des propriétés d’Azure Migrate : Découverte et évaluation. Le nom du compte de stockage aura le suffixe *usa*. 

      :::image type="content" source="./media/how-to-use-azure-migrate-with-private-endpoints/server-assessment-properties.png" alt-text="Capture instantanée des paramètres DNS de téléchargement."::: 

2. Accédez au compte de stockage et modifiez les propriétés réseau du compte de stockage pour autoriser l’accès à partir de tous les/autres réseaux. 

    :::image type="content" source="./media/how-to-use-azure-migrate-with-private-endpoints/networking-firewall-virtual-networks.png" alt-text="Capture instantanée des propriétés réseau du compte de stockage.":::

3. Vous pouvez aussi limiter l’accès à des réseaux sélectionnés et ajouter l’adresse IP publique du client à partir de laquelle vous essayez d’accéder au portail Azure.  

      :::image type="content" source="./media/how-to-use-azure-migrate-with-private-endpoints/networking-firewall.png" alt-text="Capture instantanée de l’ajout de l’adresse IP publique du client.":::