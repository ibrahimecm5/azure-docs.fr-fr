---
title: Effectuer le monitoring de hubs Surface Hub avec Azure Monitor | Microsoft Docs
description: La solution Surface Hub permet de suivre l’intégrité de vos Surface Hubs et de comprendre comment ils sont utilisés.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2018
ms.openlocfilehash: 50e1a5b98607046f8f57699e49be764439baa6f2
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114474145"
---
# <a name="monitor-surface-hubs-with-azure-monitor-to-track-their-health"></a>Effectuer le monitoring de hubs Surface Hub avec Azure Monitor pour suivre leur intégrité

![Symbole de Surface Hub](./media/surface-hubs/surface-hub-symbol.png)

Cet article explique comment utiliser la solution Surface Hub d’Azure Monitor pour effectuer le monitoring d’appareils Microsoft Surface Hub. Cette solution permet de suivre l’intégrité des hubs Surface Hub et aide à comprendre comment ils sont utilisés.

Chaque Surface Hub a un Microsoft Monitoring Agent installé. C’est cet agent qui permet d’envoyer des données de Surface Hub à un espace de travail Log Analytics dans Azure Monitor. Les fichiers journaux sont lus à partir de vos hubs Surface Hub, puis envoyés à Azure Monitor. Des problèmes comme des serveurs hors connexion, un calendrier qui ne se synchronise pas ou un compte d’appareil incapable de se connecter à Skype s’affichent sur le tableau de bord Surface Hub d’Azure Monitor. Les données du tableau de bord vous permettent d’identifier les appareils qui ne sont pas en cours d’exécution ou qui rencontrent d’autres problèmes, voire d’appliquer des correctifs pour les problèmes détectés.

## <a name="install-and-configure-the-solution"></a>Installer et configurer la solution
Utilisez les informations suivantes pour installer et configurer la solution. Les éléments suivants sont nécessaires pour gérer des hubs Surface Hub dans Azure Monitor :

* Un niveau d’[abonnement Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) prenant en charge le nombre d’appareils à analyser. La tarification de Log Analytics varie selon le nombre d’appareils inscrits et la quantité de données traitées. Vous devez prendre cela en compte lors de la planification de votre déploiement de Surface Hub.

Vous allez ensuite ajouter un espace de travail Log Analytics existant ou en créer un. Pour des instructions détaillées concernant l’utilisation de chaque méthode, voir [Créer un espace de travail Log Analytics dans le portail Azure](../logs/quick-create-workspace.md). Après avoir configuré l’espace de travail Log Analytics, vous pouvez inscrire vos appareils Surface Hub de deux façons différentes :

* automatiquement via Intune ;
* manuellement, via les **Paramètres** de votre appareil Surface Hub.

## <a name="set-up-monitoring"></a>Configurez l’analyse
Vous pouvez effectuer le monitoring de l’intégrité et de l’activité de Surface Hub avec Azure Monitor. Vous pouvez inscrire le Surface Hub via Intune, ou localement via les **Paramètres** du Surface Hub.

## <a name="connect-surface-hubs-to-azure-monitor-through-intune"></a>Connecter des hubs Surface Hub à Azure Monitor avec Intune
Vous devez disposer de l’ID et de la clé de l’espace de travail Log Analytics devant gérer vos Surface Hubs. Ces derniers sont disponibles dans les paramètres de l’espace de travail dans le portail Azure.

Intune est un produit Microsoft permettant de gérer de manière centralisée les paramètres de configuration de l’espace de travail Log Analytics appliqués à un ou plusieurs appareils. Pour configurer vos appareils via Intune, procédez comme suit :

1. Connectez-vous au [Centre d’administration Microsoft Endpoint Manager](https://endpoint.microsoft.com/).
2. Accédez à **Appareils** > **Profils de configuration**.
3. Créez un nouveau profil Windows 10, puis sélectionnez **modèles**.
4. Dans la liste des modèles, sélectionnez **Restrictions de l’appareil (Windows 10 Collaboration)** .
5. Entrez un nom et une description pour le profil.
6. Pour **Azure Operational Insights**, sélectionnez **Activer**.
7. Entrez l’**ID d’espace de travail** Log Analytics et entrez la **clé d’espace de travail** pour la stratégie.
8. Affectez la stratégie à votre groupe d’appareils Surface Hub et enregistrez la stratégie.

    :::image type="content" source="./media/surface-hubs/intune.png" alt-text="Capture d’écran montrant la définition d’une stratégie Intune":::

Intune synchronise ensuite les paramètres Log Analytics avec les appareils du groupe cible en inscrivant ceux-ci dans votre espace de travail Log Analytics.

## <a name="connect-surface-hubs-to-azure-monitor-using-the-settings-app"></a>Connecter des hubs Surface Hub à Azure Monitor avec l’application Paramètres
Vous devez disposer de l’ID et de la clé de l’espace de travail Log Analytics devant gérer vos Surface Hubs. Ces derniers sont disponibles dans les paramètres de l’espace de travail Log Analytics dans le portail Azure.

Si vous n’utilisez pas Intune pour gérer votre environnement, vous pouvez inscrire des appareils manuellement via les **Paramètres** de chaque Surface Hub :

1. Sur votre Surface Hub, ouvrez **Paramètres**.
2. Entrez les informations d’identification d’administrateur de l’appareil lorsque vous y êtes invité.
3. Cliquez sur **Cet appareil**, puis, sous **Analyse**, cliquez sur **Configurer les paramètres Log Analytics**.
4. Sélectionnez **Activer l’analyse**.
5. Dans la boîte de dialogue Paramètres Log Analytics, entrez **l’ID de l’espace de travail** et la **Clé de l’espace de travail**. 

    ![Screenshot shows the Microsoft Operations Manager Suite settings with Enable monitoring selected and text boxes for Workspace ID and Workspace Key.](./media/surface-hubs/settings.png)
1. Cliquez sur **OK** pour achever la configuration.

Une confirmation s’affiche, vous indiquant si la configuration a été correctement appliquée à l’appareil. Si c’est le cas, un message indique que l’agent est bien connecté à Azure Monitor. L’appareil commence alors à envoyer des données à Azure Monitor, où vous pouvez les consulter et les exploiter.

## <a name="monitor-surface-hubs"></a>Analyser les Surface Hubs
Le monitoring de hubs Surface Hub avec Azure Monitor est très similaire au monitoring d’autres appareils inscrits.

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Lorsque vous cliquez sur la vignette Surface Hub, l’intégrité de votre appareil s’affiche.

   ![Tableau de bord de Surface Hub](./media/surface-hubs/surface-hub-dashboard.png)

Vous pouvez créer des [alertes](../alerts/alerts-overview.md) basées sur des recherches de journal existantes ou personnalisées. Grâce aux données collectées par Azure Monitor à partir de vos hubs Surface Hub, vous pouvez rechercher des problèmes et mettre en place des alertes selon des conditions définies par vos soins pour vos appareils.

## <a name="next-steps"></a>Étapes suivantes
* Utilisez les [Requêtes de journal dans Azure Monitor](../logs/log-query-overview.md) pour voir des données Surface Hub détaillées.
* Créer des [alertes](../alerts/alerts-overview.md) pour être averti en cas de problèmes avec vos Surface Hubs.
