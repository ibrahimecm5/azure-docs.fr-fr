---
title: 'Démarrage rapide : Créer un serveur flexible Azure Database pour MySQL – portail Azure'
description: Cet article vous guide dans l’utilisation du portail Azure pour créer un serveur flexible Azure Database pour MySQL en quelques minutes.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/22/2020
ms.openlocfilehash: e1469c2d7cbc1be4aac2ec73a1f04138ef9d7fb1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131429281"
---
# <a name="quickstart-use-the-azure-portal-to-create-an-azure-database-for-mysql-flexible-server"></a>Démarrage rapide : Utiliser le portail Azure pour créer un serveur flexible Azure Database pour MySQL

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]


Le serveur flexible Azure Database pour MySQL est un service managé qui vous permet d’exécuter, de gérer et de mettre à l’échelle des serveurs MySQL hautement disponibles dans le cloud. Ce guide de démarrage rapide explique comment créer un serveur flexible à l’aide du portail Azure.

Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.
Accédez au [portail Azure](https://portal.azure.com/). Entrez vos informations d’identification pour vous connecter au portail. Il s’ouvre par défaut sur le tableau de bord des services.

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>Créer un serveur flexible Azure Database pour MySQL

Vous créez un serveur flexible avec un ensemble défini de [ressources de calcul et de stockage](./concepts-compute-storage.md). Vous créez ce serveur dans un [groupe de ressources Azure](../../azure-resource-manager/management/overview.md).

Pour créer un serveur flexible, procédez comme suit :

1. Dans le portail, cherchez et sélectionnez **Serveurs Azure Database pour MySQL** :

    > :::image type="content" source="./media/quickstart-create-server-portal/find-mysql-portal.png" alt-text="Capture d’écran montrant une recherche de serveurs Azure Database pour MySQL.":::

2. Sélectionnez **Create** (Créer).

3. Dans la page **Sélectionner une option de déploiement Azure Database pour MySQL**, sélectionnez **Serveur flexible** comme option de déploiement :

    > :::image type="content" source="./media/quickstart-create-server-portal/deployment-option.png" alt-text="Capture d’écran montrant l’option Serveur flexible.":::

4. Sous l’onglet **Informations de base**, entrez les informations suivantes :

    > :::image type="content" source="./media/quickstart-create-server-portal/create-form.png" alt-text="Capture d’écran montrant l’onglet De base de la page Serveur flexible.":::

    |**Paramètre**|**Valeur suggérée**|**Description**|
    |---|---|---|
    Abonnement|Votre nom d’abonnement|Abonnement Azure que vous souhaitez utiliser pour votre serveur. Si vous avez plusieurs abonnements, choisissez celui dans lequel vous souhaitez que la ressource soit facturée.|
    Resource group|**myresourcegroup**| Un nouveau nom de groupe de ressources ou un nom de groupe existant dans votre abonnement.|
    Nom du serveur |**mydemoserver**|Nom unique qui identifie votre serveur flexible. Le nom de domaine `mysql.database.azure.com` est ajouté au nom de serveur que vous fournissez. Le nom de serveur ne peut contenir que des lettres minuscules, des chiffres et le caractère de trait d’union (-). Il doit contenir entre 3 et 63 caractères.|
    Region|La région la plus proche de vos utilisateurs| L’emplacement géographique le plus proche de vos utilisateurs.|
    Type de charge de travail| Développement | Pour une charge de travail de production, vous pouvez choisir Petite/moyenne taille ou Grande taille en fonction des exigences de [max_connections](concepts-server-parameters.md#max_connections).|
    Zone de disponibilité| Aucune préférence | Si votre application exécutée sur des machines virtuelles Azure, dans des groupes de machines virtuelles identiques ou sur une instance d'AKS est approvisionnée dans une zone de disponibilité spécifique, vous pouvez spécifier votre serveur flexible dans la même zone de disponibilité afin de colocaliser l'application et la base de données de manière à améliorer les performances en réduisant la latence du réseau entre les zones.|
    Haute disponibilité| Désactivé | Pour les serveurs de production, choisissez entre [haute disponibilité d’une zone redondante](concepts-high-availability.md#zone-redundant-ha-architecture) et [haute disponibilité dans la même zone](concepts-high-availability.md#same-zone-ha-architecture). Cela est fortement recommandé pour la continuité des activités et la protection contre les défaillances de machines virtuelles|
    |Zone de disponibilité en attente| Aucune préférence| Choisissez l’emplacement de la zone du serveur de secours et colocalisez-le avec le serveur de secours de l’application en cas de défaillance de la zone |
    Version de MySQL|**5.7**| Version principale MySQL.|
    Nom d’utilisateur administrateur |**mydemouser**| Votre compte de connexion à utiliser lorsque vous vous connectez au serveur. Le nom d’utilisateur administrateur ne peut pas être **azure_superuser**, **admin**, **administrator**, **root**, **guest** ni **public**.|
    Mot de passe |Votre mot de passe| Un nouveau mot de passe pour le compte Administrateur du serveur. Il doit contenir entre 8 et 128 caractères. Il doit également contenir des caractères de trois des catégories suivantes : lettres majuscules, lettres minuscules, chiffres (0 à 9) et caractères non alphanumériques (!, $, #, %, etc.).|
    Calcul + stockage | **Burstable**, **Standard_B1ms**, **10 Gio**, **100 iops**, **7 jours** | Les configurations de calcul, de stockage, d'IOPS et de sauvegarde de votre nouveau serveur. Sélectionnez **Configurer le serveur**. **Burstable**, **Standard_B1ms**, **10 Gio**, **100 iops**, et **7 jours** sont les valeurs par défaut des paramètres **Niveau de calcul**, **Taille de calcul**, **Taille du stockage**, **IOPS** et **Période de conservation** des sauvegardes. Vous pouvez laisser ces valeurs en l’état ou les ajuster. Pour accélérer le chargement des données pendant la migration, il est recommandé de définir le nombre d'IOPS par seconde sur la taille maximale prise en charge par la taille de calcul et de le remettre ultérieurement à l'échelle pour réduire les coûts. Pour enregistrer cette sélection de calcul et de stockage, sélectionnez **Enregistrer** afin de poursuivre la configuration. La capture d’écran ci-dessous montre les options de calcul et de stockage.|

    > :::image type="content" source="./media/quickstart-create-server-portal/compute-storage.png" alt-text="Capture d’écran montrant les options de calcul et de stockage.":::

5. Configurez les options de mise en réseau.

    Sous l’onglet **Mise en réseau**, vous pouvez choisir la façon dont votre serveur est accessible. Un serveur flexible Azure Database pour MySQL vous fournit deux possibilités de vous connecter à votre serveur :
   - Accès public (adresses IP autorisées)
   - Accès privé (intégration au réseau virtuel)

    Lorsque vous utilisez l’accès public, l’accès à votre serveur est limité aux adresses IP autorisées que vous ajoutez à une règle de pare-feu. Cette méthode empêche les applications et les outils externes de se connecter au serveur et à toute base de données sur ce serveur, sauf si vous créez une règle pour ouvrir le pare-feu pour une adresse IP ou une plage d’adresses IP spécifique. Lorsque vous utilisez l’accès privé (intégration au réseau virtuel), l’accès à votre serveur est limité à votre réseau virtuel. [Pour en savoir plus sur les méthodes de connectivité, consultez l’article consacré aux concepts.](./concepts-networking.md)

     Dans ce guide de démarrage rapide, vous découvrirez comment activer l’accès public pour vous connecter au serveur. Sous l’**onglet Mise en réseau**, pour **Méthode de connectivité**, sélectionnez **Accès public**. Pour configurer les **Règles de pare-feu**, sélectionnez **Ajouter l’adresse IP actuelle du client**.

    > [!NOTE]
    > Vous ne pouvez pas modifier la méthode de connectivité après avoir créé le serveur. Par exemple, si vous sélectionnez **Accès public (adresses IP autorisées)** quand vous créez le serveur, vous ne pouvez pas passer à **Accès privé (intégration au réseau virtuel)** une fois le serveur créé. Nous vous recommandons vivement de créer votre serveur avec un accès privé pour contribuer à sécuriser l’accès à votre serveur via l’intégration au réseau virtuel. [Pour en savoir plus sur l’accès privé, consultez l’article consacré aux concepts.](./concepts-networking.md)

    > :::image type="content" source="./media/quickstart-create-server-portal/networking.png" alt-text="Capture d’écran montrant l’onglet Mise en réseau.":::

6. Sélectionnez **Vérifier + créer** pour passer en revue la configuration de votre serveur flexible.

7. Sélectionnez **Créer** pour approvisionner le serveur. L'approvisionnement peut prendre quelques minutes.

8. Dans la barre d’outils, sélectionnez **Notifications** (icône de cloche) pour surveiller le processus de déploiement. Une fois le déploiement effectué, vous pouvez sélectionner **Épingler au tableau de bord**. Une vignette est alors créée pour ce serveur flexible dans le tableau de bord du portail Azure. Cette vignette est un raccourci vers la page **Vue d’ensemble** du serveur. Lorsque vous sélectionnez **Accéder à la ressource**, la page **Vue d’ensemble** du serveur s’ouvre.

Par défaut, ces bases de données sont créées sous votre serveur : information_schema, mysql, performance_schema et sys.

> [!NOTE]
> Afin d’éviter des problèmes de connectivité, vérifiez que votre réseau autorise le trafic sortant via le port 3306 qu’utilise le serveur flexible Azure Database pour MySQL.

## <a name="connect-to-the-server-by-using-mysqlexe"></a>Se connecter au serveur à l’aide de mysql.exe

Si vous avez créé votre serveur flexible à l’aide d’un accès privé (intégration au réseau virtuel), vous devez vous connecter à votre serveur à partir d’une ressource figurant au sein du même réseau virtuel que votre serveur. Vous pouvez créer une machine virtuelle et l’ajouter au réseau virtuel créé avec votre serveur flexible. Pour plus d’informations, reportez-vous à la [documentation de la configuration des accès privés](how-to-manage-virtual-network-portal.md).

Si vous avez créé votre serveur flexible en utilisant un accès public (adresses IP autorisées), vous pouvez ajouter votre adresse IP locale à la liste des règles de pare-feu sur votre serveur. Pour obtenir des instructions pas à pas, consultez la [documentation sur la création ou la gestion de règles de pare-feu](how-to-manage-firewall-portal.md).

Vous pouvez utiliser [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) ou [MySQL Workbench](./connect-workbench.md) pour vous connecter au serveur à partir de votre environnement local. Le serveur flexible Azure Database pour MySQL prend en charge la connexion de vos applications clientes au service MySQL en utilisant le protocole TLS (Transport Layer Security), anciennement SSL (Secure Sockets Layer). TLS est un protocole standard qui garantit la sécurité des connexions réseau entre votre serveur de base de données et vos applications clientes, ce qui vous permet de respecter les exigences de conformité. Pour vous connecter à votre serveur flexible MySQL, vous devez télécharger le [certificat SSL public](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) pour la vérification de l'autorité de certification.

L'exemple suivant montre comment vous connecter à votre serveur flexible à l'aide de l'interface de ligne de commande mysql. Si ce n'est déjà fait, vous devez d'abord installer l'interface de ligne de commande mysql. Vous devez télécharger le certificat DigiCertGlobalRootCA requis pour les connexions SSL. Utilisez le paramètre de chaîne de connexion --ssl-mode=REQUIRED pour appliquer la vérification du certificat TLS/SSL. Passez le chemin d'accès du fichier de certificat local au paramètre --ssl-ca. Remplacez les valeurs par le nom et le mot de passe réels de votre serveur.

```bash
sudo apt-get install mysql-client
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl-mode=REQUIRED --ssl-ca=DigiCertGlobalRootCA.crt.pem
```

Si vous avez provisionné votre serveur flexible en utilisant un **accès public**, vous pouvez également utiliser [Azure Cloud Shell](https://shell.azure.com/bash) pour vous connecter à votre serveur flexible en utilisant le client MySQL préinstallé, comme indiqué ci-dessous :

Pour pouvoir utiliser Azure Cloud Shell pour vous connecter à votre serveur flexible, vous devez autoriser l’accès réseau depuis Azure Cloud Shell vers votre serveur flexible. Pour ce faire, accédez au panneau **Mise en réseau** de votre serveur flexible MySQL sur le portail Azure, puis sous la section **Pare-feu**, cochez la case « Autoriser l'accès public à ce serveur à partir de n'importe quel service Azure », comme illustré dans la capture d'écran ci-dessous, et cliquez sur Enregistrer pour enregistrer le paramètre.

 > :::image type="content" source="./media/quickstart-create-server-portal/allow-access-to-any-azure-service.png" alt-text="Capture d'écran montrant comment autoriser l'accès d'Azure Cloud Shell au serveur flexible MySQL pour la configuration du réseau d'accès public.":::

> [!NOTE]
> Cocher la case **Autoriser l’accès public à partir de n’importe quel service Azure dans Azure à ce serveur** doit être utilisé seulement à des fins de développement ou de test. Elle configure le pare-feu pour autoriser les connexions à partir d’adresses IP allouées à n’importe quel service ou ressource Azure, y compris les connexions depuis les abonnements d’autres clients.

Cliquez sur **Essayer** pour lancer Azure Cloud Shell, puis utilisez les commandes suivantes pour vous connecter à votre serveur flexible. Utilisez le nom du serveur, le nom d’utilisateur et le mot de passe dans la commande.

```azurecli-interactive
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl=true --ssl-ca=DigiCertGlobalRootCA.crt.pem
```
> [!IMPORTANT]
>Lorsque vous vous connectez à votre serveur flexible à l'aide d'Azure Cloud Shell, vous devez utiliser le paramètre --ssl=true et non --ssl-mode=REQUIRED.
> Ceci est principalement dû au fait que le client mysql.exe de la distribution MariaDB est préinstallé sur Azure Cloud Shell et que celui-ci requiert le paramètre --ssl, alors que le client mysql de la distribution Oracle requiert le paramètre --ssl-mode.

Si le message d’erreur suivant s’affiche lors de la connexion à votre serveur flexible à la suite de la commande précédente, c’est que vous n’avez pas défini la règle de pare-feu en utilisant l’option « Autoriser l’accès public à partir de n’importe quel service Azure dans Azure à ce serveur » mentionnée précédemment ou que cette option n’est pas enregistrée. Reconfigurez le pare-feu, puis réessayez.

ERREUR 2002 (HY000) : Connexion impossible au serveur MySQL sur \<servername\> (115)

## <a name="clean-up-resources"></a>Nettoyer les ressources
Vous venez de créer un serveur flexible Azure Database pour MySQL dans un groupe de ressources. Si vous ne pensez pas avoir besoin de ces ressources à l’avenir, vous pouvez les supprimer en supprimant le groupe de ressources ou en supprimant simplement le serveur MySQL. Pour supprimer le groupe de ressources, effectuez ces étapes :

1. Depuis le portail Azure, recherchez et sélectionnez **Groupes de ressources**.
1. Dans la liste des groupes de ressources, sélectionnez le nom de votre groupe de ressources.
1. Dans la page **Vue d’ensemble** de votre groupe de ressources, sélectionnez **Supprimer le groupe de ressources**.
1. Dans la boîte de dialogue de confirmation, entrez le nom de votre groupe de ressources, puis sélectionnez **Supprimer**.

Pour supprimer le serveur, vous pouvez sélectionner **Supprimer** dans la page **Vue d’ensemble** de votre serveur, comme indiqué ici :

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-server-portal/delete-server.png" alt-text="Capture d’écran montrant comment supprimer un serveur.":::

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application web PHP (Laravel) avec MySQL](tutorial-php-database-app.md)