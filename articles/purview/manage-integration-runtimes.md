---
title: Création et gestion de runtimes d’intégration
description: Cet article décrit la procédure à suivre pour créer et gérer des runtimes d’intégration dans Azure Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 10/22/2021
ms.openlocfilehash: 10e893886d98fc5ea6d79bf8092cc5b0d948d84a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132309715"
---
# <a name="create-and-manage-a-self-hosted-integration-runtime"></a>Création et gestion d’un runtime d’intégration auto-hébergé

Cet article explique comment créer et gérer un runtime d’intégration auto-hébergé qui vous permet d’analyser des sources de données dans Azure Purview.

> [!NOTE]
> Le runtime d’intégration Purview ne peut pas être partagé avec un runtime d’intégration Azure Synapse Analytics ou Azure Data Factory sur la même machine. Il doit être installé sur une machine distincte.

## <a name="prerequisites"></a>Prérequis

- Les versions de Windows Server prises en charge sont les suivantes :
  - Windows 8.1
  - Windows 10
  - Windows Server 2012
  - Windows Server 2012 R2
  - Windows Server 2016
  - Windows Server 2019

L’installation du runtime d’intégration auto-hébergé sur un contrôleur de domaine n’est pas prise en charge.

- Le runtime d’intégration auto-hébergé nécessite un système d’exploitation 64 bits avec .NET Framework 4.7.2 ou ultérieur. Consultez [Configuration système requise pour .NET Framework](/dotnet/framework/get-started/system-requirements) pour plus d’informations.
- La configuration minimale recommandée pour la machine exécutant le runtime d’intégration auto-hébergé correspond à un processeur à 2 GHz avec 4 cœurs, 8 Go de RAM et 80 Go d’espace disponible sur le disque dur. Pour plus d'informations sur la configuration requise, consultez [Télécharger](https://www.microsoft.com/download/details.aspx?id=39717).
- Si la machine hôte est en veille prolongée, le runtime d’intégration auto-hébergé ne répond pas aux demandes de données. Configurez un plan d’alimentation approprié sur l’ordinateur avant d’installer le runtime d’intégration auto-hébergé. Si la machine est configurée pour se mettre en veille prolongée, le programme d'installation du runtime d’intégration auto-hébergé ouvre un message.
- Vous devez disposer de droits d'administrateur sur la machine pour correctement installer et configurer le runtime d’intégration auto-hébergé.
- Les analyses sont effectuées à une fréquence spécifique, selon le calendrier que vous avez établi. L'utilisation du processeur et de la RAM sur la machine suit le même modèle avec des périodes de pointe et d’inactivité. L'utilisation des ressources dépend aussi fortement de la quantité de données qui sont analysées. Lorsque plusieurs tâches de numérisation sont en cours, vous constatez une augmentation de l'utilisation des ressources aux heures de pointe.
- Les tâches peuvent échouer lors de l’extraction de données aux formats Parquet, ORC ou Avro.

> [!IMPORTANT]
> Si vous souhaitez utiliser le runtime d'intégration auto-hébergé pour analyser des fichiers Parquet, vous devez installer **JRE 8 (Java Runtime Environment) 64 bits ou OpenJDK** sur votre machine IR. Consultez notre [section Java Runtime Environment au bas de la page](#java-runtime-environment-installation) pour obtenir un guide d’installation.

## <a name="setting-up-a-self-hosted-integration-runtime"></a>Installation d’un runtime d’intégration auto-hébergé

Pour créer et installer un runtime d’intégration auto-hébergé, suivez les procédures ci-dessous.

## <a name="create-a-self-hosted-integration-runtime"></a>Créer un runtime d’intégration auto-hébergé

1. Dans la page d’accueil de [Purview Studio](https://web.purview.azure.com/resource/), sélectionnez **Data Map** dans le volet de navigation gauche.

2. Sous **Sources et analyse** dans le volet gauche, sélectionnez **Runtimes d’intégration**, puis **+ Nouveau**.

   :::image type="content" source="media/manage-integration-runtimes/select-integration-runtimes.png" alt-text="Sélectionnez sur IR.":::

3. Sur la page **Configuration des runtimes d’intégration**, sélectionnez **Auto-hébergé** pour créer un runtime d’intégration auto-hébergé, puis **Continuer**.

   :::image type="content" source="media/manage-integration-runtimes/select-self-hosted-ir.png" alt-text="Création d’un runtime d’intégration auto-hébergé":::

4. Entrez un nom pour votre runtime d’intégration, puis sélectionnez Créer.

5. Sur la page **Paramètres des runtime d’intégration**, suivez la procédure décrite dans la section **Configuration manuelle**. Vous devrez télécharger le runtime d’intégration sur une machine virtuelle ou l’ordinateur sur lequel vous envisagez de l’exécuter à partir du site de téléchargement.

   :::image type="content" source="media/manage-integration-runtimes/integration-runtime-settings.png" alt-text="Récupération de la clé":::

   - Copiez et collez la clé d’authentification.

   - Téléchargez le runtime intégration auto-hébergé sur une machine Windows locale à partir de [Microsoft Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717). Exécutez le programme d’installation. Les versions du runtime d’intégration auto-hébergées telles que 5.4.7803.1 et 5.6.7795.1 sont prises en charge. 

   - Dans la page **Inscrire le runtime d’intégration (auto-hébergé)** , collez une des deux clés que vous avez enregistrées avant, puis sélectionnez **Inscrire**.

     :::image type="content" source="media/manage-integration-runtimes/register-integration-runtime.png" alt-text="Clé d’entrée":::

   - Dans la page **Nouveau runtime d’intégration (auto-hébergé)** , sélectionnez **Terminer**.

6. Une fois le runtime d’intégration auto-hébergé inscrit, la fenêtre suivante s’affiche :

   :::image type="content" source="media/manage-integration-runtimes/successfully-registered.png" alt-text="Inscription réussie":::

### <a name="configure-proxy-server-settings"></a>Configurer les paramètres du serveur proxy

Si vous sélectionnez l'option **Utiliser le proxy système** pour le proxy HTTP, le runtime d’intégration auto-hébergé utilise les paramètres du proxy dans diahost.exe.config et diawp.exe.config. Si ces fichiers ne spécifient aucun proxy, le runtime d’intégration auto-hébergé se connecte directement au service cloud sans passer par un proxy. La procédure suivante fournit des instructions pour mettre à jour le fichier diahost.exe.config :

1. Dans l’Explorateur de fichiers, effectuez une copie de sauvegarde de C:\Program Files\Microsoft Integration Runtime\5.0\Shared\diahost.exe.config en tant que sauvegarde du fichier d’origine.
1. Ouvrez le Bloc-notes en tant qu’administrateur.
1. Dans le Bloc-notes, ouvrez le fichier texte C:\Program Files\Microsoft Integration Runtime\5.0\Shared\diahost.exe.config.
1. Localisez la balise par défaut **system.net** comme indiqué dans le code suivant :

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```

    Vous pouvez ensuite ajouter les détails du serveur proxy comme illustré dans l’exemple suivant :

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    La balise de proxy permet des propriétés supplémentaires pour spécifier les paramètres requis comme `scriptLocation`. Consultez [\<proxy\>Elément (paramètres réseau)](/dotnet/framework/configure-apps/file-schema/network/proxy-element-network-settings) pour connaître la syntaxe.

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```

1. Enregistrez le fichier config dans son emplacement d’origine. Redémarrez ensuite le service hôte du runtime d’intégration auto-hébergé, qui relève les modifications.

   Pour redémarrer le service, utilisez l’applet des services dans le Panneau de configuration. Ou, dans le Gestionnaire de configuration Integration Runtime, sélectionnez le bouton **Arrêter le service**, puis **Démarrer le service**.

   Si le service ne démarre pas, il est probable que vous ayez ajouté une syntaxe de balise XML incorrecte au fichier de configuration de l’application modifié.

> [!IMPORTANT]
> N’oubliez pas de mettre à jour diahost.exe.config et diawp.exe.config.

Vous devez également vérifier que Microsoft Azure figure dans la liste d’autorisation de votre entreprise. Vous pouvez télécharger la liste des adresses IP Azure valides. Les plages d’adresses IP pour chaque cloud, réparties par région et par les services marqués dans ce cloud, sont désormais disponibles sur MS Download : 
   - Public : https://www.microsoft.com/download/details.aspx?id=56519

### <a name="possible-symptoms-for-issues-related-to-the-firewall-and-proxy-server"></a>Symptômes possibles problèmes liés au pare-feu et au serveur proxy

Si des messages d’erreur semblables aux suivants s’affichent, il est fort probable qu'ils soient dus à une mauvaise configuration du pare-feu ou du serveur proxy. Une telle configuration empêche le runtime d’intégration auto-hébergé de se connecter aux sources de données ou aux comptes de stockage managés Azure. Pour vous assurer que votre pare-feu et votre serveur proxy sont correctement configurés, reportez-vous à la section précédente.

- Lorsque vous tentez d’inscrire le runtime d’intégration auto-hébergé, le message d'erreur suivant s'affiche : Échec d’inscription de ce nœud Runtime d’intégration ! Vérifiez que la clé d’authentification est valide et que le service hôte d’intégration est en cours d’exécution sur cette machine.
- Lorsque vous ouvrez le Gestionnaire de configuration Integration Runtime, l’état indiqué est **Déconnecté** ou **En cours de connexion**. Lorsque vous affichez les journaux des événements Windows, sous **Observateur d’événements** > **Journaux des applications et services** > **Microsoft Integration Runtime**, des messages d’erreur tels que le suivant s’affichent :

  ```output
  Unable to connect to the remote server
  A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
  ```

## <a name="networking-requirements"></a>Configuration requise du réseau

Votre machine de runtime d’intégration auto-hébergé doit se connecter à plusieurs ressources pour fonctionner correctement :

* Les sources que vous souhaitez analyser à l’aide du runtime d’intégration auto-hébergé.
* Tout Azure Key Vault utilisé pour stocker des informations d’identification de la ressource Purview.
* Le compte Stockage managé et les ressources Event Hub créées par Purview.

Les ressources Stockage et Event Hub managées sont accessibles dans votre abonnement sous un groupe de ressources contenant le nom de votre ressource Purview. Azure Purview utilise ces ressources pour ingérer les résultats de l’analyse, entre autres choses, afin que le runtime d’intégration auto-hébergé puisse être en mesure de se connecter directement à ces ressources.

Voici les domaines et les ports qui doivent être autorisés via des pare-feu d’entreprise et d’ordinateur.

> [!NOTE]
> Pour les domaines listés avec « \<managed Purview storage account> », vous devez ajouter le nom du compte de stockage managé associé à votre ressource Purview. Vous pouvez trouver cette ressource dans le portail. Recherchez dans vos groupes de ressources un groupe nommé : managed-rg-\<your Purview Resource name>. Par exemple : managed-rg-contosoPurview. Vous allez utiliser le nom du compte de stockage dans ce groupe de ressources.
> 
> Pour les domaines listés avec « \<managed Event Hub resource> », vous devez ajouter le nom du Event Hub managé associé à votre ressource Purview. Vous pouvez le trouver dans le même groupe de ressources que le compte de stockage managé.

| Noms de domaine                  | Ports sortants | Description                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net` | 443            | L’infrastructure globale que Purview utilise pour exécuter ses analyses. Caractère générique requis, car il n’existe aucune ressource dédiée. |
| `<managed Event Hub resource>.servicebus.windows.net` | 443            | Purview utilise cette valeur pour se connecter au service bus associé. Il sera couvert par l’autorisation du domaine ci-dessus, mais si vous utilisez des points de terminaison privés, vous devrez tester l’accès à ce domaine unique.|
| `*.frontend.clouddatahub.net` | 443            | L’infrastructure globale que Purview utilise pour exécuter ses analyses. Caractère générique requis, car il n’existe aucune ressource dédiée. |
| `<managed Purview storage account>.core.windows.net`          | 443            | Utilisé par le runtime d’intégration auto-hébergé pour se connecter au compte de stockage Azure managé.|
| `<managed Purview storage account>.queue.core.windows.net` | 443            | Files d’attente utilisées par Purview pour exécuter le processus d’analyse. |
| `download.microsoft.com` | 443           | Facultatif pour les mises à jour SHIR. |

En fonction de vos sources, vous devrez peut-être également autoriser les domaines d’autres sources Azure ou externes. Quelques exemples sont fournis ci-dessous, ainsi que le domaine Azure Key Vault, si vous vous connectez à des informations d’identification stockées dans le Key Vault.

| Noms de domaine                  | Ports sortants | Description                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `<storage account>.core.windows.net`          | 443            | Facultatif, pour se connecter à un compte de stockage Azure. |
| `*.database.windows.net`      | 1433           | Facultatif, pour se connecter à Azure SQL Database ou Azure Synapse Analytics. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Facultatif, pour se connecter à Azure Data Lake Store Gen 1. |
| `<datastoragename>.dfs.core.windows.net`    | 443            | Facultatif, pour se connecter à Azure Data Lake Store Gen 2. |
| `<your Key Vault Name>.vault.azure.net` | 443           | Obligatoire si des informations d’identification sont stockées dans Azure Key Vault. |
| Différents domaines | Dépendant          | Domaines pour toutes les autres sources auxquelles le SHIR se connectera. |
  
> [!IMPORTANT]
> Dans la plupart des environnements, vous devrez également vérifier que votre DNS est correctement configuré. Confirmer que vous pouvez utiliser **nslookup** depuis votre machine SHIR pour vérifier la connectivité à chacun des domaines susmentionnés. Chaque nslookup doit renvoyer l’adresse IP de la ressource. Si vous utilisez des [points de terminaison](catalog-private-link.md), c’est l’adresse IP privée qui doit être retournée et non l’adresse IP publique. Si aucune adresse IP n’est retournée, ou si, lors de l’utilisation de points de terminaison privés, l’adresse IP publique est retournée, vous devez gérer votre association DNS/VNET ou votre association point de terminaison privé/réseau virtuel.

## <a name="manage-a-self-hosted-integration-runtime"></a>Gestion d’un runtime d’intégration auto-hébergé

Vous pouvez modifier un runtime d'intégration hébergé par vous-même en accédant à **Integration runtimes** dans le **Management center**, en sélectionnant l'IR, puis en sélectionnant edit. Vous pouvez alors mettre à jour la description, copier la clé ou en regénérer de nouvelles.

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime.png" alt-text="Modification du runtime d’intégration":::

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime-settings.png" alt-text="Modification des détails du runtime d’intégration":::

Vous pouvez supprimer un runtime d'intégration auto-hébergé en accédant à **Integration runtimes** dans le Management center, en sélectionnant l'IR, puis en sélectionnant **Supprimer**. Une fois le runtime d’intégration supprimé, toutes les analyses en cours qui en dépendent échouent.

## <a name="java-runtime-environment-installation"></a>Installation de Java Runtime Environment

Si vous souhaitez analyser des fichiers Parquet à l’aide du runtime d'intégration auto-hébergé avec Purview, vous devez installer Java Runtime Environment ou le OpenJDK sur votre ordinateur de runtime d'intégration auto-hébergé.

Lors de l’analyse des fichiers Parquet à l’aide du runtime d’intégration auto-hébergé, le service localise le runtime Java en vérifiant d’abord le registre *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* pour JRE, et s’il est introuvable, en vérifiant la variable système *`JAVA_HOME`* pour OpenJDK.

- **Pour utiliser JRE** : Le runtime d’intégration de 64 bits requiert la version 64 bits de JRE. Vous pouvez la récupérer [ici](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Pour utiliser OpenJDK** : il est pris en charge à compter de la version 3.13 du runtime d’intégration. Empaquetez jvm.dll avec tous les autres assemblys requis d’OpenJDK dans la machine d’IR auto-hébergé et définissez la variable d’environnement système JAVA_HOME en conséquence.

## <a name="proxy-server-considerations"></a>Considérations relatives aux serveurs proxy

Si votre environnement de réseau d’entreprise utilise un serveur proxy pour accéder à Internet, configurez le runtime d’intégration auto-hébergé pour utiliser les bons paramètres de proxy. Vous pouvez définir le proxy lors de la phase initiale de l’enregistrement.

:::image type="content" source="media/manage-integration-runtimes/self-hosted-proxy.png" alt-text="Spécifiez le proxy.":::

Une fois configuré, le runtime d’intégration auto-hébergé utilise le serveur proxy pour se connecter à la source et à la destination du service cloud (à l'aide du protocole HTTP ou HTTPS). C'est la raison pour laquelle vous sélectionnez **Changer le lien** lors de la configuration initiale.

:::image type="content" source="media/manage-integration-runtimes/set-http-proxy.png" alt-text="Configurer le proxy":::

Il existe trois options de configuration :

- **Ne pas utiliser de proxy** : le runtime d’intégration auto-hébergé n’utilise pas explicitement de proxy pour se connecter aux services cloud.
- **Utiliser le proxy système** : le runtime d’intégration autohébergé utilise le paramètre de proxy configuré dans diahost.exe.config et diawp.exe.config. Si ces fichiers ne spécifient aucune configuration de proxy, le runtime d’intégration auto-hébergé se connecte directement au service cloud sans passer par un proxy.
- **Utiliser un proxy personnalisé** : configurez les paramètres du proxy HTTP à utiliser pour le runtime d’intégration auto-hébergé au lieu d’utiliser les configurations dans diahost.exe.config et diawp.exe.config. Les valeurs **Adresse** et **Port** sont requises. Les valeurs **Nom d’utilisateur** et **Mot de passe** sont facultatives, en fonction des paramètres d’authentification de votre proxy. Tous les paramètres sont chiffrés avec Windows DPAPI sur le runtime d’intégration autohébergé et stockés localement sur l’ordinateur.

Le service hôte du runtime d’intégration auto-hébergé redémarre automatiquement après avoir enregistré les paramètres de proxy mis à jour.

Une fois le runtime d’intégration auto-hébergé inscrit, si vous souhaitez afficher ou mettre à jour les paramètres de proxy, utilisez le Gestionnaire de configuration Microsoft Integration Runtime.

1. Ouvrez le **Gestionnaire de configuration de Microsoft Integration Runtime**.
3. Sous **Proxy HTTP**, sélectionnez le lien **Modifier** pour ouvrir la boîte de dialogue **Définir le proxy HTTP**.
4. Sélectionnez **Suivant**. Vous pouvez voir un avertissement demandant l’autorisation d’enregistrer les paramètres de proxy et de redémarrer le service hôte du runtime d’intégration.

Vous pouvez utiliser l'outil Gestionnaire de configuration pour afficher et mettre à jour le proxy HTTP.

> [!NOTE]
> Si vous configurez un serveur proxy avec l’authentification NTLM, le service hôte du runtime d’intégration s’exécute sous le compte du domaine. Si vous modifiez ultérieurement le mot de passe du compte du domaine, veillez à mettre à jour les paramètres de configuration du service et à redémarrer ce dernier. En raison de cette exigence, nous vous conseillons d'accéder au serveur proxy à l'aide d'un compte de domaine dédié qui ne nécessite pas de mettre à jour le mot de passe fréquemment.

## <a name="installation-best-practices"></a>Bonnes pratiques d’installation

Vous pouvez installer le runtime d’intégration auto-hébergé en téléchargeant un package de configuration d’identité gérée à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=39717).

- Configurez un plan d’alimentation sur la machine hôte du runtime d’intégration auto-hébergé afin d’empêcher la mise en veille prolongée de la machine. Si cette dernière se met en veille prolongée, le runtime d’intégration auto-hébergé passe à l’état hors connexion.
- Sauvegardez régulièrement les informations d’identification associées au runtime d’intégration auto-hébergé.

## <a name="next-steps"></a>Étapes suivantes

- [Comment les analyses détectent les éléments supprimés](concept-scans-and-ingestion.md#how-scans-detect-deleted-assets)

- [Utiliser des points de terminaison privés avec Purview](catalog-private-link.md)
