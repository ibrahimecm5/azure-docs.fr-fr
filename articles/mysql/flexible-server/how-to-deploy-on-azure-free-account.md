---
title: Utilisez un compte gratuit Azure pour essayer gratuitement la Base de données Azure pour MySQL - Serveur Flexible
description: Aide sur le déploiement d’une Base de données Azure pour MySQL - Serveur Flexible pour l’utilisation gratuite d’un Compte gratuit Azure.
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.topic: how-to
ms.date: 08/16/2021
ms.custom: template-how-to
ms.openlocfilehash: 619c65cb2f86c946d0e85573a967a23d718b38c9
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501435"
---
# <a name="use-an-azure-free-account-to-try-azure-database-for-mysql---flexible-server-for-free"></a>Utilisez un compte gratuit Azure pour essayer gratuitement la Base de données Azure pour MySQL - Serveur Flexible

Azure Database pour MySQL - Serveur flexible est un service managé qui vous permet d’exécuter, de gérer et de mettre à l’échelle des bases de données MySQL hautement disponibles dans le cloud.  Avec un compte gratuit Azure, vous pouvez utiliser un Serveur Flexible **gratuitement pendant 12 mois** avec **des limites mensuelles** jusqu’à :
- **750 heures** d’instance **Burstable B1MS**, suffisamment d’heures pour exécuter une instance de base de données en continu chaque mois.
- **stockage de 32 Go et stockage de sauvegarde de 32 Go**. 

Cet article vous montre comment créer et utiliser un serveur flexible gratuitement à l’aide d’un [compte gratuit Azure](https://azure.microsoft.com/free/). 


## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

- Compte gratuit Azure. Si vous n’en avez pas, [créez un compte gratuit](https://azure.microsoft.com/free/) avant de commencer. 


## <a name="create-an-azure-database-for-mysql---flexible-server"></a>Créer un serveur flexible Azure Database pour MySQL

Dans cet article, vous allez utiliser le portail Azure pour créer un serveur flexible avec la méthode de connectivité d’accès public. Vous pouvez également consulter les démarrages rapides respectifs pour créer un serveur flexible à l’aide d’[Azure CLI](./quickstart-create-server-cli.md) ou d’un [modèle ARM](./quickstart-create-arm-template.md), ou [au sein d’un réseau virtuel](./quickstart-create-connect-server-vnet.md).


1. Connectez-vous au [portail Azure](https://portal.azure.com/) avec votre compte gratuit Azure. 
    
    Il s’ouvre par défaut sur le tableau de bord des services. 

1. Pour créer une base de données de Serveur flexible MySQL, recherchez et sélectionnez **Serveurs de Base de données Azure pour MySQL** :
    
    :::image type="content" source="./media/how-to-deploy-on-azure-free-account/search-select-azure-database-for-mysql.png" alt-text="Capture d’écran montrant comment rechercher et sélectionner une Base de données Azure pour MySQL sur le portail Azure.":::


    Alternativement, vous pouvez recherchez et parcourir les **Services gratuits**, puis sélectionner la partie **Base de données Azure pour MySQL** à partir de la liste :
    
    :::image type="content" source="media/how-to-deploy-on-azure-free-account/all-free-services.png" alt-text="Capture d’écran montrant une liste de tous les services gratuits sur le portail Azure.":::

1. Sélectionnez **Create** (Créer).

1. Dans la page **Sélectionner une option de déploiement de Azure Database pour MySQL**, sélectionnez **Serveur flexible**. 

    :::image type="content" source="media/how-to-deploy-on-azure-free-account/select-deployment-option.png" alt-text="Capture d’écran montrant l’Option de déploiement de serveur flexible à choisir.":::

1. Entrez les paramètres de base d’un nouveau **Serveur Flexible**.


    :::image type="content" source="media/how-to-deploy-on-azure-free-account/basic-settings-all.PNG" alt-text="Capture d’écran montrant les Paramètres de base pour la création d’un Serveur Flexible.":::


    |Paramètre    |Valeur suggérée    |Description  |
    |---------|---------|---------|
    |Abonnement    |Votre abonnement    |Sélectionnez l’abonnement Azure version d’évaluation gratuite.|
    |Resource group    |Votre groupe de ressources    |Entrez un groupe de ressources nouveau ou existant dans votre abonnement.|
    |Nom du serveur    |mydemoserver-mysql    |Spécifiez un nom unique pour identifier votre serveur flexible. Le nom de domaine .mysql.database.azure.com est ajouté au nom de serveur que vous fournissez. Le nom de serveur ne peut contenir que des lettres minuscules, des chiffres et le caractère de trait d’union (-). Il doit contenir entre 3 et 63 caractères.|
    |Region    |La région la plus proche de vos utilisateurs    |Sélectionnez un emplacement dans la liste, de préférence l’emplacement le plus proche de vos utilisateurs.|
    |Type de charge de travail    |Développement    |Pour une version d’évaluation gratuite, sélectionnez charge de travail de Développement. Pour une charge de travail de production, choisissez Petite/moyenne taille ou Grande taille en fonction des exigences de [max_connections](concepts-server-parameters.md).|
    |Zone de disponibilité    |Aucune préférence    |Si votre application (hébergée sur des Machines virtuelles Azure, des groupes identiques de machines virtuelles ou une instance AKS) est configurée dans une zone de disponibilité spécifique, créez votre serveur flexible dans la même zone de disponibilité. La colocalisation de l’application et de la base de données améliore les performances en réduisant la latence du réseau entre les zones.|
    |Haute disponibilité    |Default    |Laissez l’option Haute disponibilité désactivée.|
    |Version de MySQL    |La version principale la plus récente    |Utilisez la version principale la plus récente. Pour plus d’informations, consultez [toutes les versions prises en charge](../concepts-supported-versions.md).|
    |Nom d’utilisateur administrateur     |myadmin        |Créer un compte de connexion à utiliser lorsque vous vous connectez au serveur. Le nom d’utilisateur administrateur ne peut pas être azure_superuser, admin, administrateur, racine, invité ou public.         |
    |Mot de passe     |Votre mot de passe         |Entrez un nouveau mot de passe pour le compte administrateur du serveur. Votre mot de passe doit contenir entre 8 et 128 caractères. Il doit également contenir des caractères des trois des catégories suivantes : lettres majuscules, lettres minuscules, chiffres (0 à 9) et caractères non alphanumériques (!, $, #, %, etc.).         |

1. Pour paramétrer **Calcul + Stockage**, sélectionnez **Configurer le Serveur**.
 
    Veillez à sélectionner l’instance Burstable B1MS (1-2 vCores), indiquez si vous souhaitez inclure un stockage inférieur ou égal à 32 Go, puis conservez les paramètres par défaut pour les options restantes.

    :::image type="content" source="media/how-to-deploy-on-azure-free-account/compute-storage.png" alt-text="Capture d’écran montrant le panneau de Configuration du serveur Calcul + Stockage, pour choisir B1MS SKU et Stockage 32 go.":::

1. Sélectionnez **Enregistrer** pour poursuivre la configuration.


1. Sélectionnez l’onglet **Mise en réseau** pour configurer le mode d’accès à votre serveur.

     Base de données Azure pour MySQL - Serveur Flexible vous fournit deux possibilités de vous connecter :
     
    - Accès public (adresses IP autorisées)
    - Accès privé (intégration au réseau virtuel)
     
    Lorsque vous utilisez l’accès public, l’accès à votre serveur est limité aux adresses IP autorisées que vous ajoutez à une règle de pare-feu. Cette méthode empêche les applications et les outils externes de se connecter à ce serveur et à toute base de données sur ce serveur, sauf si vous créez une règle pour ouvrir le pare-feu pour une adresse IP ou une plage d’adresses IP spécifique. 

    Avec l'accès privé (intégration au réseau virtuel), l'accès à votre serveur est limité à votre réseau virtuel. Pour plus d’informations sur les méthodes de connectivité, consultez [Concepts de connectivité et de mise en réseau.](./concepts-networking.md)
     
    Dans le cadre de ce didacticiel, activez l’accès public pour la connexion au serveur. 
    
1. Sous l’onglet **Mise en réseau**, pour **Méthode de connectivité**, sélectionnez **Accès public**. 
1. Pour configurer les **Règles de pare-feu**, sélectionnez **Ajouter l’adresse IP actuelle du client**.
    
    :::image type="content" source="media/how-to-deploy-on-azure-free-account/networking.png" alt-text="Capture d’écran qui montre les options de mise en réseau à choisir et met en surbrillance le bouton Ajouter l’adresse IP du client actuel.":::

    > [!NOTE]
    > Vous ne pouvez pas modifier la méthode de connectivité après avoir créé le serveur. Par exemple, si vous sélectionnez **Accès public (adresses IP autorisées)** quand vous créez le serveur, vous ne pouvez pas passer à l’**Accès privé (intégration au réseau virtuel)** une fois le serveur créé. Nous vous recommandons vivement de créer votre serveur avec un accès privé pour contribuer à sécuriser l’accès à votre serveur via l’intégration au réseau virtuel. Pour plus d’informations sur l’accès privé, consultez [Concepts de connectivité et de mise en réseau.](./concepts-networking.md)

1. Sélectionnez **Vérifier + créer** pour passer en revue la configuration de votre serveur flexible.

    :::image type="content" source="media/how-to-deploy-on-azure-free-account/review-and-create.png" alt-text="Capture d’écran montrant le panneau Vérifier + créer.":::

    >[!IMPORTANT]
    >Lors de la création de l’instance Serveur flexible à partir de votre compte gratuit Azure, vous voyez toujours un **coût estimé par mois** dans le panneau **Calcul + Stockage : Récapitulatif des coûts** et l’onglet **Vérifier + créer**. Toutefois, tant que vous utilisez votre compte gratuit Azure et que votre utilisation gratuite des services est comprise dans les limites mensuelles (pour afficher des informations sur l’utilisation, voir la section [**Surveiller et suivre l’utilisation des services gratuits**](#monitor-and-track-free-services-usage) ci-dessous), vous n’êtes pas facturé pour le service. Nous travaillons actuellement à l’amélioration de l’expérience **Résumé des coûts** pour les services gratuits. 
    
1. Sélectionnez **Créer** pour approvisionner le serveur. 
 
    L’approvisionnement peut prendre quelques minutes

1. Dans la barre d’outils, sélectionnez **Notifications** (icône de cloche) pour surveiller le processus de déploiement. 
    
    Une fois le déploiement effectué, sélectionnez **Épingler au tableau de bord** pour créer une vignette pour ce serveur flexible dans le tableau de bord de votre portail Azure. Cette vignette est un raccourci vers la page **Vue d’ensemble** du serveur. Lorsque vous sélectionnez **Accéder à la ressource**, la page **Vue d’ensemble** du serveur s’ouvre.

    Par défaut, votre serveur inclut les bases de données suivantes : information_schema, mysql, performance_schema et sys.


## <a name="connect-and-query"></a>Se connecter et interroger

Maintenant que vous avez créé une Base de données Azure pour MySQL - Serveur Flexible dans un groupe de ressources, vous pouvez vous connecter au serveur et interroger les bases de données à l’aide des démarrages rapides de connexion et de requête suivants :

- [mysql.exe](quickstart-create-server-portal.md)
- [MySQL Workbench](connect-workbench.md)
- [Azure CLI](connect-azure-cli.md)
- [PHP](connect-php.md) 
- [Java](connect-java.md)
- [Python](connect-python.md)
- [.NET](connect-csharp.md)


## <a name="monitor-and-track-free-services-usage"></a>Surveiller et suivre l’utilisation des services gratuits

Vous n’êtes pas facturé pour les services de la Base de données Azure pour PostgreSQL - Serveur flexible inclus gratuitement avec votre compte gratuit Azure, sauf si vous dépassez les limites de service gratuites. Pour rester dans les limites, utilisez le portail Azure pour suivre et surveiller l’utilisation de vos services gratuits.

1. Dans le portail Azure, recherchez **Abonnements** puis sélectionnez l’abonnement au compte gratuit Azure - **Version d’évaluation gratuite**.

1. Sur la page **Vue d’ensemble**, déroulez le menu pour afficher la vignette **Meilleurs services gratuits par usage**, puis sélectionnez **Visualiser l’ensemble des services gratuits**.

    :::image type="content" source="media/how-to-deploy-on-azure-free-account/free-services-usage-overview.png" alt-text="Capture d’écran montrant la page de présentation de l’abonnement à la version d’évaluation gratuite et mettant en surbrillance la Vue d’ensemble de l’ensemble des services gratuits.":::

1. Recherchez les compteurs ci-dessous en rapport avec la **Base de données Azure pour MySQL – Serveur Flexible** pour suivre l’utilisation.

    :::image type="content" source="media/how-to-deploy-on-azure-free-account/free-services-tracking.png" alt-text="Capture d’écran montrant le panneau Visualiser et suivre les informations d’utilisation sur le portail Azure pour tous les services gratuits.":::

    |Compteur    |Description    |Limite mensuelle  |
    |---------|---------|---------|
    |Base de données Azure pour MySQL, Serveur flexible Calcul de la série BS Burstable, B1MS | Effectue le suivi de l’utilisation du calcul en termes de nombre d’heures d’exécution | 750 heures par mois - niveau de calcul B1MS Burstable|
    |Base de données Azure pour MySQL - Stockage, Données stockées du Serveur flexible | Effectue le suivi du Stockage des données approvisionnées en termes de go utilisés par mois | 32 Go par mois |

    
    - Compteur : identifie l’unité de mesure du service consommé.
    - Utilisation/Limite : utilisation et limite du mois en cours pour le compteur.
    - État : état d’utilisation du service. En fonction de votre utilisation, vous pouvez avoir l’un des états suivants :
    - Non utilisé : vous n’avez pas utilisé le compteur, ou l’utilisation du compteur n’a pas atteint le système de facturation.
    - Dépassement le \<Date\> : vous avez dépassé la limite du compteur le \<Date\>.
    - Dépassement peu probable : il est peu probable que vous dépassiez la limite du compteur.
    - Dépassement le \<Date\> : il est probable que vous dépasserez la limite du compteur le \<Date\>.
    

    >[!IMPORTANT]
    >Avec un compte gratuit Azure, vous obtenez également un crédit de 200 $ à utiliser dans un délai de 30 jours. Pendant ce temps, toute utilisation au-delà des quantités mensuelles gratuites de services sera déduite de ce crédit.
    >À la fin de vos 30 premiers jours ou après avoir dépensé votre crédit de 200 $ (selon la première situation), vous payez uniquement ce que vous utilisez au-delà des quantités mensuelles de services gratuites. Pour continuer à bénéficier de services gratuits au bout de 30 jours, passez au tarif du paiement à l’utilisation. Si vous ne passez pas au paiement à l’utilisation, vous ne pouvez pas acheter de services Azure au-delà de votre crédit de 200 $, et votre compte et vos services seront éventuellement désactivés.
    >Pour plus d’informations, consultez [**FAQ du compte gratuit Azure**](https://azure.microsoft.com/free/free-account-faq/).


## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous utilisez le serveur flexible pour le développement, les tests ou les charges de travail de production limitées dans le temps ou prévisibles, optimisez l’utilisation en démarrant et en arrêtant le serveur à la demande. Une fois que vous avez arrêté le serveur, il reste dans cet état pendant sept jours, sauf si le redémarrage est effectué plus tôt. Pour plus d’informations, consultez [Concepts de serveur](concept-servers.md).
Lorsque votre Serveur Flexible est arrêté, il n’y a aucune utilisation de Calcul, mais l’utilisation de Stockage est toujours prise en compte. 

Si vous ne pensez pas avoir besoin de ces ressources à l’avenir, vous pouvez les supprimer en supprimant le groupe de ressources ou en supprimant simplement le serveur MySQL. 

- Procédez comme suit pour supprimer le groupe de ressources :

    1. Depuis le portail Azure, recherchez et sélectionnez **Groupes de ressources**.
    1. Dans la liste des groupes de ressources, sélectionnez le nom de votre groupe de ressources.
    1. Dans la page **Vue d’ensemble** de votre groupe de ressources, sélectionnez **Supprimer le groupe de ressources**.
    1. Dans la boîte de dialogue de confirmation, entrez le nom de votre groupe de ressources, puis sélectionnez **Supprimer**.

- Pour supprimer le serveur MySQL, sur la page **Vue d’ensemble** pour le serveur, sélectionnez **Supprimer**.


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application web PHP (Laravel) avec MySQL](tutorial-php-database-app.md)

