---
title: Connecteurs de données Azure Sentinel | Microsoft Docs
description: Découvrez comment connecter des sources de données comme Microsoft 365 Defender (anciennement Protection Microsoft contre les menaces), Microsoft 365 et Office 365, Azure AD, ATP et Cloud App Security à Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc, ignite-fall-2021
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2021
ms.author: yelevin
ms.openlocfilehash: 46a5bcf0db580d652f045efa0c4a381bdbd7143a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131075464"
---
# <a name="azure-sentinel-data-connectors"></a>Connecteurs de données Azure Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Après l’intégration d’Azure Sentinel à votre espace de travail, connectez des sources de données pour commencer à ingérer vos données dans Azure Sentinel. Azure Sentinel est fourni avec plusieurs connecteurs pour les produits Microsoft, prêts à l’emploi et offrant une intégration en temps réel. Par exemple, les connecteurs de service à service incluent des connecteurs Microsoft 365 Defender et des sources Microsoft 365, comme Office 365, Azure Active Directory (Azure AD), Microsoft Defender pour Identity et Microsoft Cloud App Security.

Vous pouvez également activer des connecteurs prêts à l’emploi aux écosystèmes de sécurité élargis pour les produits non Microsoft. Par exemple, vous pouvez utiliser [Syslog](#syslog), le [format CEF (Common Event Format)](#common-event-format-cef) ou les [API REST](#rest-api-integration) pour connecter vos sources de données à Azure Sentinel.

La page **Connecteurs de données**, accessible à partir du menu de navigation Azure Sentinel, affiche la liste complète des connecteurs fournis par Azure Sentinel, et leur état dans votre espace de travail. Sélectionnez le connecteur que vous souhaitez connecter, puis sélectionnez **Ouvrir la page du connecteur**.

![Galerie des connecteurs de données](./media/collect-data/collect-data-page.png)

Cet article décrit les méthodes de connexion de données prises en charge. Pour plus d’informations, consultez [Référence des connecteurs de données Azure Sentinel](data-connectors-reference.md) et le [catalogue de solutions Azure Sentinel](sentinel-solutions-catalog.md).


<a name="agent-options"></a>
<a name="data-connection-methods"></a>
<a name="map-data-types-with-azure-sentinel-connection-options"></a>

## <a name="enable-a-data-connector"></a>Active un connecteur de données

La page **Connecteurs de données**, accessible à partir du menu de navigation Azure Sentinel, affiche la liste complète des connecteurs fournis par Azure Sentinel, et leur état. Sélectionnez le connecteur que vous souhaitez connecter, puis sélectionnez **Ouvrir la page du connecteur**.

![Galerie des connecteurs de données](./media/collect-data/collect-data-page.png)

Vous devez avoir rempli toutes les conditions préalables, et vous verrez des instructions complètes sur la page du connecteur pour ingérer les données dans Azure Sentinel. Les données peuvent mettre un certain temps à apparaître. Une fois connecté, vous voyez un récapitulatif des données dans le graphe **Données reçues**, ainsi que l’état de connectivité des types de données.

![Configurer des connecteurs de données](./media/collect-data/opened-connector-page.png)

Dans l’onglet **Étapes suivantes**, vous verrez du contenu supplémentaire fourni par Azure Sentinel pour des exemples de type de données spécifiques : requêtes d’exemple, classeurs de visualisation et modèles de règle d’analyse, pour vous aider à détecter et à examiner les menaces.

![Étapes suivantes pour les connecteurs](./media/collect-data/data-insights.png)

Pour plus d’informations, consultez la section correspondant à votre connecteur de données sur la page [Référence des connecteurs de données](data-connectors-reference.md).

## <a name="rest-api-integration"></a>Intégration de l’API REST

De nombreuses technologies de sécurité fournissent un jeu d’API pour la récupération des fichiers journaux et certaines sources de données peuvent utiliser ces API pour se connecter à Azure Sentinel.

Les connecteurs de données qui utilisent des API s’intègrent du côté du fournisseur ou s’intègrent à l’aide d’Azure Functions, comme décrit dans les sections suivantes.

Pour obtenir une liste complète et des informations sur ces connecteurs, consultez [Référence des connecteurs de données](data-connectors-reference.md).

### <a name="rest-api-integration-on-the-provider-side"></a>Intégration de l’API REST côté fournisseur

Une intégration d’API qui est créée par le fournisseur se connecte aux sources de données du fournisseur et envoie les données dans des tables de journaux personnalisées Azure Sentinel à l’aide de l’[API du collecteur de données Azure Monitor](../azure-monitor/logs/data-collector-api.md).

Pour plus d’informations, consultez la documentation de votre fournisseur et la page [Connecter votre source de données à l’API REST d’Azure Sentinel pour ingérer des données](connect-rest-api-template.md).

### <a name="rest-api-integration-using-azure-functions"></a>Intégration d’API REST à l’aide d’Azure Functions

Les intégrations qui utilisent [Azure Functions](../azure-functions/index.yml) pour se connecter à une API de fournisseur mettent tout d’abord en forme les données, puis les envoient aux tables de journal personnalisées Azure Sentinel à l’aide de l’[API du collecteur de données Azure Monitor](../azure-monitor/logs/data-collector-api.md).

Afin de configurer ces connecteurs de données pour qu’ils se connectent à l’API du fournisseur et pour collecter des journaux dans Azure Sentinel, suivez les étapes indiquées pour chaque connecteur de données dans Azure Sentinel.

Pour plus d’informations, consultez [Utiliser Azure Functions pour connecter votre source de données à Azure Sentinel](connect-azure-functions-template.md).

> [!IMPORTANT]
> Les intégrations qui utilisent Azure Functions peuvent entraîner des coûts d’ingestion de données supplémentaires, car vous hébergez Azure Functions sur votre locataire Azure. Pour plus d’informations, consultez la page [Tarification d’Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="agent-based-integration"></a>Intégration basée sur l’agent

Azure Sentinel peut utiliser le protocole Syslog pour se connecter via un agent à n’importe quelle source de données capable d’effectuer un streaming de journaux en temps réel. Par exemple, la plupart des sources de données locales se connectent par le biais de l’intégration basée sur l’agent.
Les sections suivantes décrivent les différents types de connecteurs de données basés sur l’agent d’Azure Sentinel. Suivez les étapes de chaque page connecteur de données Azure Sentinel pour configurer les connexions à l’aide de mécanismes basés sur des agents.

Pour obtenir la liste complète des pare-feu, proxies et points de terminaison qui se connectent à Azure Sentinel via CEF ou Syslog, consultez la page [Référence des connecteurs de données](data-connectors-reference.md).

### <a name="syslog"></a>syslog

Vous pouvez diffuser des événements en continu à partir d’appareils Linux prenant en charge Syslog vers Azure Sentinel à l’aide de l’agent Log Analytics pour Linux, anciennement appelé agent OMS. L’agent Log Analytics est pris en charge pour tout appareil qui vous permet d’installer l’agent Log Analytics directement sur l’appareil.

Le démon Syslog intégré de l’appareil collecte les événements locaux des types spécifiés et les transfère localement à l’agent, qui les diffuse en continu dans votre espace de travail Log Analytics. Une fois la configuration réussie, les données s’affichent dans la table Syslog de Log Analytics.

En fonction du type d’appareil, l’agent est installé directement sur l’appareil ou sur un redirecteur de journaux Linux dédié. L’agent de Log Analytics reçoit les événements du démon Syslog via UDP. Si une machine Linux est supposée collecter un volume important d’événements Syslog, elle envoie les événements via le protocole TCP du démon Syslog à l’agent, puis de là à Log Analytics.

Pour plus d’informations, consultez [Connecter des appliances Syslog à Azure Sentinel](connect-syslog.md). 

### <a name="common-event-format-cef"></a>CEF (Common Event Format)

Le format des journaux varie, mais de nombreuses sources prennent en charge le format CEF. L’agent Azure Sentinel, qui est en fait l’agent Azure Monitor Log Analytics, convertit les journaux au format CEF dans un format que Log Analytics peut ingérer.

Pour les sources de données qui émettent des données au format CEF, configurez l’agent Syslog, puis configurez le flux de données CEF. Une fois la configuration réussie, les données s’affichent dans la table **CommonSecurityLog**.

Pour plus d’informations, consultez [Connecter des appliances CEF à Azure Sentinel](connect-common-event-format.md).

### <a name="custom-logs"></a>Journaux d’activité personnalisés

Certaines sources de données disposent de journaux pour la collecte de fichiers sur Windows ou Linux. Vous pouvez collecter ces journaux à l’aide de l’agent de collecte de journaux personnalisé Log Analytics.

Suivez les étapes de chaque page du connecteur de données Azure Sentinel pour vous connecter à l’aide de l’agent de collecte de journaux personnalisé Log Analytics. Une fois la configuration réussie, les données s’affichent dans des tables personnalisées.

Pour plus d’information, reportez-vous à [Collecter des données dans des formats de journaux personnalisés vers Azure Sentinel avec l’agent Log Analytics](connect-custom-logs.md).

## <a name="service-to-service-integration"></a>Intégration de service à service

Azure Sentinel utilise la fondation Azure pour assurer la prise en charge prête à l’emploi de service à service pour les services Microsoft et Amazon Web Services.

Pour plus d’informations, reportez-vous à [Connecter des services Azure, Windows, Microsoft et Amazon](connect-azure-windows-microsoft-services.md) ainsi qu’à la page [Référence des connecteurs de données](data-connectors-reference.md).

## <a name="deploy-as-part-of-a-solution"></a>Déployer en tant que partie d’une solution

Les [solutions Azure Sentinel](sentinel-solutions.md) fournissent des packages de contenu de sécurité, notamment des connecteurs de données, des classeurs, des règles d’analyse, des playbooks et bien plus. Lorsque vous déployez une solution avec un connecteur de données, vous obtenez le connecteur de données et le contenu associé dans le même déploiement. 

Pour plus d’informations, consultez [Découvrir et déployer de manière centralisée du contenu et des solutions Azure Sentinel prêts à l’emploi](sentinel-solutions-deploy.md) et le [Catalogue de solutions Azure Sentinel](sentinel-solutions-catalog.md).
## <a name="data-connector-support"></a>Prise en charge du connecteur de données

Microsoft et d’autres organisations créent des connecteurs de données Azure Sentinel. Chaque connecteur de données dispose de l’un des types de prise en charge suivants :

| Type de support| Description|
|-------------|------------|
|**Pris en charge par Microsoft**|S’applique à :<ul><li>Connecteurs de données pour les sources de données où Microsoft est le fournisseur de données et l’auteur.</li><li>Certains connecteurs de données créés par Microsoft pour des sources de données autres que Microsoft.</li></ul>Microsoft prend en charge et gère les connecteurs de données dans cette catégorie conformément aux [Plans de Support Microsoft Azure](https://azure.microsoft.com/support/options/#overview).<br><br>Les partenaires ou la communauté assurent le support des connecteurs de données créés par les tiers, autres que Microsoft.|
|**Support par un partenaire**|S’applique aux connecteurs de données créés par des tiers, autres que Microsoft.<br><br>La société partenaire assure le support ou la maintenance de ces connecteurs de données. Cette société partenaire peut être un éditeur de logiciels indépendant, un fournisseur de services gérés (MSP/MSSP), un intégrateur de systèmes ou toute organisation dont les coordonnées sont fournies sur la page Azure Sentinel de ce connecteur de données.<br><br>Pour tout problème lié à un connecteur de données pris en charge par un partenaire, contactez le contact de support du connecteur de données concerné.|
|**Support par la communauté**|S’applique aux connecteurs de données créés par Microsoft ou par les développeurs partenaires qui n’ont pas de contacts pour le support et la maintenance du connecteur de données dans la page connecteur de données spécifiée dans Azure Sentinel.<br><br>Pour toute question ou tout problème liés à ces connecteurs de données, vous pouvez [entrer un problème](https://github.com/Azure/Azure-Sentinel/issues/new/choose) dans la [communauté Azure Sentinel GitHub](https://aka.ms/threathunters).|

### <a name="find-the-support-contact-for-a-data-connector"></a>Rechercher le contact de support pour un connecteur de données

Pour rechercher le contact de support pour un connecteur de données :

1. Dans le menu de gauche d’Azure Sentinel, sélectionnez **Connecteurs de données**.

1. Sélectionnez le connecteur pour lequel vous souhaitez obtenir les informations de support.

1. Consultez le champ **Support assuré par** dans le volet latéral du connecteur de données.

   ![Capture d’écran montrant le champ Support assuré par d’un connecteur de données dans Azure Sentinel.](./media/collect-data/connectors.png)

   Le champ **Support assuré par** contient un lien de contact du support technique pour accéder au support et à la maintenance du connecteur de données sélectionné.

## <a name="next-steps"></a>Étapes suivantes

- Pour utiliser Azure Sentinel, vous devez disposer d’un abonnement à Microsoft Azure. Si vous n’avez pas d’abonnement, vous pouvez vous inscrire à un [essai gratuit](https://azure.microsoft.com/free/).
- Découvrez comment [intégrer vos données à Azure Sentinel](quickstart-onboard.md) et [obtenir une visibilité de vos données et des menaces potentielles](get-visibility.md).
