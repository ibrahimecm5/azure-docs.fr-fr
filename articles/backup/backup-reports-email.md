---
title: Envoyer des rapports de sauvegarde Azure par e-mail
description: Créer des tâches automatisées pour recevoir régulièrement des rapports par e-mail
ms.topic: conceptual
ms.date: 10/19/2021
ms.openlocfilehash: dd5e31663262463449df4377329edc6604ffb691
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130235978"
---
# <a name="email-azure-backup-reports"></a>Envoyer des rapports de sauvegarde Azure par e-mail

La fonctionnalité **Rapport de messagerie** disponible dans Rapports de sauvegarde vous permet de créer des tâches automatisées pour recevoir des rapports périodiques par e-mail. Cette fonctionnalité fonctionne en déployant une application logique dans votre environnement Azure, qui interroge les données de vos espaces de travail Log Analytics (LA) sélectionnés, en fonction des entrées que vous faites. [En savoir plus sur Logic Apps et sur ses tarifs](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="getting-started"></a>Mise en route

Pour configurer les e-mails dans Rapports de sauvegarde, effectuez les étapes suivantes :

1.  Accédez à **Centre de sauvegarde** > **Rapports de sauvegarde**, puis cliquez sur l’onglet **Envoyer le rapport par e-mail**.
2.  Créez une tâche en spécifiant les informations suivantes :
    * **Détails de la tâche** : le nom de l’application logique à créer, ainsi que l’abonnement, le groupe de ressources et l’endroit où elle doit être créée. Notez que l’application logique peut interroger des données dans plusieurs abonnements, groupes de ressources et localisations (celles sélectionnées dans la section Filtres du rapport), mais qu’elle est créée dans le contexte d’un seul abonnement, d’un seul groupe de ressources et d’une seule localisation.
    * **Données à exporter** : onglet qui contient les données à exporter. Vous pouvez soit créer une application monotâche pour chaque onglet, soit envoyer par e-mail tous les onglets à l’aide d’une même tâche, en sélectionnant l’option **Tous les onglets**.
    * **Options des e-mails** : fréquence des e-mails, ID des destinataires et objet de l’e-mail.

    ![Onglet E-mail](./media/backup-azure-configure-backup-reports/email-tab.png)

3.  Une fois que vous avez cliqué sur **Envoyer** et sur **Confirmer**, l’application logique est créée. L’application logique et les connexions d’API associées sont créées avec l’étiquette **UsedByBackupReports: true** afin qu’elles soient facilement découvrables. Vous devez effectuer une étape d’autorisation ponctuelle pour que l’application logique s’exécute correctement, comme l’explique la section ci-dessous.

## <a name="authorize-connections-to-azure-monitor-logs-and-office-365"></a>Autoriser les connexions aux journaux Azure Monitor et à Office 365

L’application logique utilise le connecteur [azuremonitorlogs](/connectors/azuremonitorlogs/) pour interroger les espaces de travail Log Analytics, et utilise le connecteur [Office 365 Outlook](/connectors/office365connector/) pour envoyer des e-mails. Vous devrez accorder une autorisation ponctuelle pour ces deux connecteurs. 
 
Pour accorder l’autorisation, suivez les étapes ci-dessous :

1.  Accédez à **Logic Apps** dans le portail Azure.
2.  Recherchez le nom de l’application logique que vous avez créée, puis accédez à la ressource.

    ![Logic Apps](./media/backup-azure-configure-backup-reports/logic-apps.png)

3.  Cliquez sur l’élément de menu **Connexions d’API**.

    ![Connexions d’API](./media/backup-azure-configure-backup-reports/api-connections.png)

4.  Vous verrez deux connexions aux formats `<location>-azuremonitorlogs` et `<location>-office365` : _eastus-azuremonitorlogs_ et _eastus-office365_.
5.  Accédez à chacune de ces connexions et sélectionnez l’élément de menu **Modifier une connexion d’API**. Dans l’écran qui s’affiche, sélectionnez **Autoriser**, puis enregistrez la connexion une fois l’autorisation obtenue.

    ![Autoriser la connexion](./media/backup-azure-configure-backup-reports/authorize-connections.png)

6.  Pour vérifier si l’application logique fonctionne après l’autorisation, vous pouvez revenir à l’application logique, ouvrir **Vue d’ensemble** puis sélectionner **Déclencheur d’exécution** dans le volet supérieur afin de voir si un e-mail est bien généré.

## <a name="contents-of-the-email"></a>Contenu de l’e-mail

* Tous les graphiques et graphes qui s’affichent dans le portail sont disponibles sous la forme de contenu inséré dans l’e-mail. [Apprenez-en davantage](configure-reports.md) sur les informations affichées dans les rapports de sauvegarde.
* Les grilles qui s’affichent dans le portail sont disponibles sous la forme de pièces jointes au format *.csv dans l’e-mail.
* Les données de l’e-mail utilisent tous les filtres de rapport qui sont sélectionnés par l’utilisateur dans le rapport au moment de la création de l’e-mail.
* Les filtres au niveau de l’onglet comme **Nom de l’instance de sauvegarde**, **Nom de la stratégie**, etc. ne sont pas appliqués. La seule exception à cela est la grille **Optimisations des conservations** sous l’onglet **Optimiser**, où les filtres de conservation **Quotidienne**, **Hebdomadaire**, **Mensuelle** et **Annuelle** des points de conservation sont appliqués.
* L’intervalle de temps et le type d’agrégation (pour les graphiques) sont basés sur l’intervalle de temps que l’utilisateur a sélectionné dans les rapports. Par exemple, si l’intervalle de temps sélectionné correspond aux 60 derniers jours (avec le type d’agrégation hebdomadaire) et que la fréquence des e-mails est quotidienne, le destinataire recevra un e-mail tous les jours avec des graphiques regroupant les données qui ont été collectées au cours des 60 derniers jours, avec une agrégation au niveau hebdomadaire.

## <a name="troubleshooting-issues"></a>Résolution des problèmes

Si vous ne recevez pas les e-mails comme prévu, même après avoir correctement déployé l’application logique, vous pouvez suivre les étapes ci-dessous pour résoudre les problèmes de configuration :

### <a name="scenario-1-receiving-neither-a-successful-email-nor-an-error-email"></a>Scénario 1 : Aucun e-mail reçu (réussite ou échec)

* Ce problème peut se produire parce que le connecteur d’API Outlook n’est pas autorisé. Pour autoriser la connexion, suivez les étapes d’autorisation fournies ci-dessus.

* Ce problème peut également se produire si vous n’avez pas spécifié le bon destinataire lors de la création de l’application logique. Pour vérifier que le bon destinataire a été spécifié, vous pouvez accéder à l’application logique dans le portail Azure, ouvrir le concepteur d’application logique, puis sélectionner l’étape E-mail pour voir si les bons ID d’e-mail sont utilisés.

### <a name="scenario-2-receiving-an-error-email-that-says-that-the-logic-app-failed-to-execute-to-completion"></a>Scénario 2 : Réception d’un e-mail indiquant que l’application logique n’a pas pu s’exécuter jusqu’au bout

Pour résoudre ce problème :
1.  Accédez à l’application logique dans le portail Azure.
2.  Au bas de l’écran **Vue d’ensemble**, la section **Historique des exécutions** s’affiche. Vous pouvez ouvrir la dernière exécution pour voir quelles étapes du workflow ont échoué. Les causes possibles sont les suivantes :
    * Le **connecteur des journaux Azure Monitor n’a pas été autorisé** : pour résoudre ce problème, suivez les étapes d’autorisation fournies ci-dessus.
    * **Erreur dans la requête Log Analytics** : si vous avez personnalisé l’application logique avec vos propres requêtes, une erreur dans l’une des requêtes Log Analytics peut provoquer l’échec de l’application logique. Vous pouvez sélectionner l’étape concernée pour voir l’erreur qui empêche la bonne exécution de la requête.

Si le problème persiste, contactez le support technique Microsoft.

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus sur la solution Rapports de sauvegarde](./configure-reports.md)