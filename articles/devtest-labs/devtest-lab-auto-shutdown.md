---
title: Configurer une stratégie d’arrêt automatique pour les labs et les machines virtuelles
description: Découvrez comment définir, chaque jour à un moment donné, des planifications et des stratégies d’arrêt automatique des machines virtuelles pour Azure DevTest Labs ou pour des machines virtuelles individuelles.
ms.topic: how-to
ms.date: 11/01/2021
ms.openlocfilehash: 622d9c2da013ad9eb8c3a0eef46a21999f54ee76
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286732"
---
# <a name="configure-auto-shutdown-for-labs-and-vms-in-devtest-labs"></a>Configurer un arrêt automatique pour des labs et des machines virtuelles dans DevTest Labs

En tant que propriétaire d’un lab Azure DevTest Labs, vous pouvez configurer une planification pour arrêter toutes les machines virtuelles incluses dans votre lab à une heure précise de la journée ou de la nuit. Vous économisez ainsi le coût d’exécution des machines inutilisés.

Vous pouvez aussi définir une stratégie d’arrêt automatique centrale pour déterminer si les utilisateurs du lab peuvent planifier un arrêt automatique pour leurs propres machines virtuelles. Les stratégies d’arrêt automatique permettent soit d’octroyer aux propriétaires des machines virtuelles un contrôle total des planifications d’arrêt de leur machine, soit de leur refuser tout contrôle sur ces planifications.

Cet article explique comment définir des planifications d’arrêt automatique pour des labs DevTest Labs et pour des machines virtuelles de lab individuelles. L’article explique aussi comment définir une stratégie d’arrêt automatique de lab et comment configurer des notifications d’arrêt automatique.

## <a name="configure-lab-auto-shutdown-schedule"></a>Configurer une planification d’arrêt automatique de lab

L’arrêt automatique permet de réduire le gaspillage en arrêtant toutes les machines virtuelles d’un lab à une heure précise de la journée ou de la nuit. Pour voir ou modifier la planification de l’arrêt automatique d’un lab, effectuez les étapes suivantes :

1. Sur la page d’accueil de votre laboratoire, sélectionnez **Configuration et stratégies**.
1. Dans la section **Planifications** du menu de gauche, sélectionnez **Arrêt automatique**.
1. Dans l’écran **Arrêt automatique**, pour l’option **Activé**, sélectionnez **Activé** pour activer l’arrêt automatique ou **Désactivé** pour le désactiver.
1. Pour **Arrêt planifié** et **Fuseau horaire**, si vous avez activé l’arrêt automatique, spécifiez l’heure et le fuseau horaire de l’arrêt de toutes les machines virtuelles du lab.
1. Pour **Envoyer une notification avant l’arrêt automatique ?** , sélectionnez **Oui** ou **Non** pour l’option d’envoi d’une notification 30 minutes avant l’heure d’arrêt automatique spécifiée. Si vous choisissez **Oui**, entrez un point de terminaison d’URL de webhook sous **URL de webhook** ou des adresses e-mail séparées par des points-virgules sous **Adresse e-mail** pour indiquer la destination de la publication ou de l’envoi de la notification. Pour plus d’informations, consultez la section [Notifications d’arrêt automatique](#auto-shutdown-notifications).
1. Sélectionnez **Enregistrer**.

   ![Capture d’écran qui montre comment définir les détails d’un arrêt automatique pour un lab.](media/devtest-lab-auto-shutdown/auto-shutdown.png)

Par défaut, cette planification s’applique à toutes les machines virtuelles du lab. Pour supprimer cette configuration d’une machine virtuelle spécifique, si elle est autorisée par une stratégie, ouvrez le volet de gestion de la machine virtuelle et modifiez son paramètre **Arrêt automatique**.

> [!NOTE]
> Si vous mettez à jour la planification de l’arrêt automatique de votre lab ou d’une machine virtuelle dans les 30 minutes suivant l’heure d’arrêt précédemment planifiée, la nouvelle heure d’arrêt prend effet dès le lendemain.

## <a name="configure-lab-auto-shutdown-policy"></a>Configurer une stratégie d’arrêt automatique de lab

En tant que propriétaire de lab, vous pouvez contrôler le coût et réduire le gaspillage inhérent à vos labs en gérant les paramètres de stratégie d’arrêt automatique de vos labs. Pour voir comment définir les stratégies de tous les labs, consultez [Définir des stratégies de lab dans Azure DevTest Labs](devtest-lab-set-lab-policy.md).

> [!IMPORTANT]
> Les modifications apportées aux stratégies d’arrêt automatique s’appliquent uniquement aux nouvelles machines virtuelles créées dans le lab et non pas aux machines virtuelles déjà existantes.

1. Sur la page d’accueil de votre laboratoire, sélectionnez **Configuration et stratégies**.

1. Dans la section **Planifications** du menu de gauche, sélectionnez **Stratégie d’arrêt automatique**.

1. Sélectionnez l'une des options.

   ![Capture d’écran montrant les options de stratégie d’arrêt automatique.](./media/devtest-lab-auto-shutdown/policy-options.png)

   - **L’utilisateur définit une planification qu’il peut refuser** : Les utilisateurs du lab peuvent remplacer ou refuser la planification du lab. Cette option octroie un contrôle total aux propriétaires de machines virtuelles sur la définition des planifications d’arrêt automatique de leurs machines virtuelles.

   - **L’utilisateur définit une planification qu’il ne peut pas refuser** : Les utilisateurs du lab peuvent remplacer la planification du lab, mais ils ne peuvent pas refuser la stratégie d’arrêt automatique. Cette option permet de veiller à ce que chaque machine virtuelle du lab soit soumise à une planification d’arrêt automatique. Les propriétaires de machines virtuelles peuvent mettre à jour l’heure de planification et configurer les notifications d’arrêt.

   - **L’utilisateur n’a aucun contrôle sur la planification définie par l’administrateur du lab** : Les utilisateurs du lab ne peuvent pas modifier ni refuser la planification de l’arrêt automatique du lab. Cette option donne à l’administrateur du lab un contrôle total de la planification définie pour toutes les machines virtuelles du lab. Les propriétaires du lab peuvent quand même configurer des notifications d’arrêt automatique pour leurs machines virtuelles.

1. Sélectionnez **Enregistrer**.

## <a name="configure-vm-auto-shutdown-settings"></a>Configurer les paramètres d’arrêt automatique des machines virtuelles

Selon la stratégie d’arrêt automatique, vous pouvez aussi définir une planification d’arrêt automatique pour des machines virtuelles de lab individuelles.

1. Dans la page d’accueil de la machine virtuelle, dans la section **Opérations** du menu de gauche, sélectionnez **Arrêt automatique**.
1. Dans l’écran **Arrêt automatique**, pour l’option **Activé**, sélectionnez **Activer** pour activer l’arrêt automatique ou **Désactiver** pour le désactiver.
1. Pour **Arrêt planifié** et **Fuseau horaire**, si vous avez activé l’arrêt automatique, spécifiez l’heure et le fuseau horaire de l’arrêt de toutes les machines virtuelles du lab.
1. Pour **Envoyer une notification avant l’arrêt automatique ?** , sélectionnez **Oui** ou **Non** pour l’option d’envoi d’une notification 30 minutes avant l’heure d’arrêt automatique spécifiée. Si vous choisissez **Oui**, entrez un point de terminaison d’URL de webhook sous **URL de webhook** ou une adresse e-mail sous **Adresse e-mail** pour indiquer la destination de la publication ou de l’envoi de la notification. Pour plus d’informations, consultez la section [Notifications d’arrêt automatique](#auto-shutdown-notifications).
1. Sélectionnez **Enregistrer**.

   ![Capture d’écran qui montre la définition des détails d’un arrêt automatique pour une machine virtuelle.](media/devtest-lab-auto-shutdown/compute-auto-shutdown.png)

### <a name="view-activity-logs-for-auto-shutdown-updates"></a>Afficher les journaux d'activité pour les mises à jour de l'arrêt automatique

Après avoir mis à jour le paramètre d’arrêt automatique, vous pouvez consulter l’activité enregistrée dans le journal d’activité de la machine virtuelle.

1. Dans la page d’accueil de la machine virtuelle, sélectionnez **Journal d’activité** dans le menu de gauche.
1. Supprimez le filtre **Ressource**, appliquez le filtre **Groupe de ressources** approprié et affichez les entrées pour **Ajouter ou modifier des planifications**.

   ![Capture d’écran qui présente l’option Ajouter ou modifier des planifications dans l’écran Journal d’activité.](media/devtest-lab-auto-shutdown/activity-log-entry.png)

1. Sélectionnez l’opération **Ajouter ou modifier des planifications** pour ouvrir une page récapitulative qui présente les détails de l’opération.

## <a name="auto-shutdown-notifications"></a>Notifications d’arrêt automatique

Quand vous activez les notifications dans la configuration de l’arrêt automatique, les utilisateurs du lab reçoivent une notification 30 minutes avant l’arrêt automatique si l’une de leurs machines virtuelles est concernée. Cette notification donne la possibilité aux utilisateurs du lab d’enregistrer leur travail avant l’arrêt. Si les paramètres d’arrêt automatique spécifient une adresse e-mail, la notification est envoyée à cette adresse e-mail. Si les paramètres spécifient un webhook, la notification est envoyée à l’URL du webhook.

La notification peut aussi fournir des liens permettant d’effectuer les actions suivantes pour chaque machine virtuelle si un utilisateur a besoin de continuer à travailler :

- Ignorer l’arrêt automatique cette fois-ci.
- Retarder l’arrêt automatique d’une heure.
- Retarder l’arrêt automatique de 2 heures.

Vous pouvez utiliser des webhooks pour implémenter vos propres notifications. Vous configurez des intégrations qui s’abonnent à certains événements. Quand l’un de ces événements se produit, une charge utile HTTP POST est envoyée à l’URL du webhook.

Des applications comme [Azure Logic Apps](../logic-apps/logic-apps-overview.md) et Slack prennent largement en charge les webhooks. Pour plus d’informations sur la réponse aux webhooks, consultez [Vue d’ensemble des déclencheurs et des liaisons HTTP Azure Functions](../azure-functions/functions-bindings-http-webhook.md) ou [Ajouter un déclencheur HTTP pour Azure Logic Apps](../connectors/connectors-native-http.md#add-an-http-trigger).

L’exemple suivant montre comment utiliser Logic Apps pour configurer une notification d’arrêt automatique qui envoie un e-mail aux propriétaires de machines virtuelles.

### <a name="create-a-logic-app-that-sends-email-notifications"></a>Créer une application logique qui envoie des notifications par e-mail

Logic Apps fournit de nombreux connecteurs qui facilitent l’intégration d’un service dans d’autres clients, comme Office 365 et Twitter. En règle générale, les étapes de configuration d’une application logique pour une notification par e-mail sont les suivantes :

1. Créez une application logique.
1. Configurez le modèle intégré.
1. Intégrez-le à votre client de messagerie.
1. Obtenez l’URL du webhook à utiliser dans les paramètres de notification d’arrêt automatique.

Pour commencer, créez une application logique dans Azure en effectuant les étapes suivantes :

1. Dans le portail Azure, entrez *logic apps* dans le champ de recherche situé en haut, puis sélectionnez **Logic Apps**.

1. En haut de la page **Logic Apps**, sélectionnez **Ajouter**.

1. Dans la page **Créer une application logique** :

   - Sélectionnez votre **abonnement** Azure.
   - Sélectionnez un **Groupe de ressources** ou créez-en un.
   - Entrez un **nom d’application logique**.
   - Sélectionnez la **Région** de l’application logique.

   ![Capture d’écran montrant la page Créer une application logique.](media/devtest-lab-auto-shutdown/new-logic-app-page.png)

1. Sélectionnez **Vérifier + créer**, puis quand la validation réussit, sélectionnez **Créer**.

1. Une fois le déploiement terminé, sélectionnez **Accéder à la ressource**.

Ensuite, configurez le modèle intégré.

1. Dans la page de l’application logique, sélectionnez **Concepteur d’application logique** sous **Outils de déploiement** dans le volet de navigation gauche.

1. Sélectionnez **Modèles** dans le menu supérieur.

1. Sous **Modèles**, sélectionnez **Demande-réponse HTTP**.

   ![Capture d’écran montrant le modèle Demande-réponse HTTP.](media/devtest-lab-auto-shutdown/select-http-request-response-option.png)

1. Dans la page **Demande-réponse HTTP**, sélectionnez **Utiliser ce modèle**.

   ![Capture d’écran montrant la sélection de l’option Utiliser ce modèle.](./media/devtest-lab-auto-shutdown/select-use-this-template.png)

1. Collez le code JSON suivant dans la section **Schéma JSON du corps de la demande**.

   ![Capture d’écran montrant la section Schéma JSON du corps de la demande dans le concepteur.](media/devtest-lab-auto-shutdown/request-json.png)

    ```json
    {
        "$schema": "http://json-schema.org/draft-04/schema#",
        "properties": {
            "delayUrl120": {
                "type": "string"
            },
            "delayUrl60": {
                "type": "string"
            },
            "eventType": {
                "type": "string"
            },
            "guid": {
                "type": "string"
            },
            "labName": {
                "type": "string"
            },
            "owner": {
                "type": "string"
            },
            "resourceGroupName": {
                "type": "string"
            },
            "skipUrl": {
                "type": "string"
            },
            "subscriptionId": {
                "type": "string"
            },
            "text": {
                "type": "string"
            },
            "vmName": {
                "type": "string"
            },
            "vmUrl": {
                "type": "string"
            },
            "minutesUntilShutdown": {
                "type": "string"
            }
        },
        "required": [
            "skipUrl",
            "delayUrl60",
            "delayUrl120",
            "vmName",
            "guid",
            "owner",
            "eventType",
            "text",
            "subscriptionId",
            "resourceGroupName",
            "labName",
            "vmUrl",
            "minutesUntilShutdown"
        ],
        "type": "object"
    }
    ```

À présent, intégrez-le à votre client de messagerie.

1. Dans le concepteur, sélectionnez **Nouvelle étape**.

   ![Capture d’écran montrant l’option Nouvelle étape dans le concepteur.](media/devtest-lab-auto-shutdown/new-step.png)

1. Dans la page **Choisir une opération**, entrez *Office 365 Outlook – Envoyer un e-mail* dans le champ de recherche, puis sélectionnez **Envoyer un e-mail (V2)** sous **Actions**.

   ![Capture d’écran représentant l’option Envoyer un e-mail (V2).](media/devtest-lab-auto-shutdown/select-send-email.png)

1. Dans le formulaire **Envoyer un e-mail (V2)** , renseignez les champs **À**, **Objet** et **Corps**.

   Sélectionnez **Ajouter du contenu dynamique** pour remplir automatiquement la notification avec les valeurs utilisées par l’application et les connecteurs. Par exemple, pour **À**, sélectionnez **propriétaire**. Renseignez l’**objet** avec **vmName** et **labName**. Ajoutez du contenu comme les valeurs **skipUrl** et **delayUrl** au corps du message.

   ![Capture d’écran montrant un exemple d’e-mail de notification.](media/devtest-lab-auto-shutdown/email-options.png)

1. Sélectionnez **Enregistrer** dans la barre d’outils.

Vous pouvez maintenant copier l’URL du webhook. Sélectionnez l’étape **Lors de la réception d’une requête HTTP**, puis sélectionnez le bouton Copier pour copier l’URL HTTP POST dans le Presse-papiers. Collez cette URL de webhook dans les paramètres de notification d’arrêt automatique.

![Capture d’écran montrant la copie de l’URL du webhook.](media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="next-steps"></a>Étapes suivantes

- [Démarrer automatiquement des machines virtuelles de laboratoire](devtest-lab-auto-startup-vm.md)
- [Définir des stratégies de laboratoire dans Azure DevTest Labs](devtest-lab-set-lab-policy.md)
- [Recevoir des requêtes HTTPS entrantes et y répondre dans Azure Logic Apps](/azure/connectors/connectors-native-reqres)
