---
title: Connecteurs de données Microsoft Sentinel | Microsoft Docs
description: Découvrez comment connecter des sources de données comme Microsoft 365 Defender (anciennement Protection Microsoft contre les menaces), Microsoft 365 et Office 365, Azure AD, ATP et Defender for Cloud Apps à Microsoft Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc, ignite-fall-2021
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.openlocfilehash: 8e8ac4ba23a548138eaffc7a0efeb89082af3475
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517004"
---
# <a name="microsoft-sentinel-data-connectors"></a>Connecteurs de données Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Après l’intégration de Microsoft Sentinel à votre espace de travail, connectez des sources de données pour commencer à ingérer vos données dans Microsoft Sentinel. Microsoft Sentinel est fourni avec plusieurs connecteurs pour les produits Microsoft, prêts à l’emploi et offrant une intégration en temps réel. Par exemple, les connecteurs service à service incluent des connecteurs Microsoft 365 Defender et des sources Microsoft 365, comme Office 365, Azure Active Directory (Azure AD), Microsoft Defender pour Identity et Microsoft Defender for Cloud Apps.

Vous pouvez également activer des connecteurs prêts à l’emploi aux écosystèmes de sécurité élargis pour les produits non Microsoft. Par exemple, vous pouvez utiliser [Syslog](#syslog), le [format CEF (Common Event Format)](#common-event-format-cef) ou les [API REST](#rest-api-integration) pour connecter vos sources de données à Microsoft Sentinel.

La page **Connecteurs de données**, accessible à partir du menu de navigation de Microsoft Sentinel, affiche la liste complète des connecteurs fournis par Microsoft Sentinel et leur état dans votre espace de travail. Sélectionnez le connecteur que vous souhaitez connecter, puis sélectionnez **Ouvrir la page du connecteur**.

![Galerie des connecteurs de données](./media/collect-data/collect-data-page.png)

Cet article décrit les méthodes de connexion de données prises en charge. Pour plus d’informations, consultez [Informations de référence sur les connecteurs de données Microsoft Sentinel](data-connectors-reference.md) et le [catalogue de solutions Microsoft Sentinel](sentinel-solutions-catalog.md).

<a name="agent-options"></a>
<a name="data-connection-methods"></a>
<a name="map-data-types-with-microsoft-sentinel-connection-options"></a>

## <a name="enable-a-data-connector"></a>Active un connecteur de données

La page **Connecteurs de données**, accessible à partir du menu de navigation de Microsoft Sentinel, affiche la liste complète des connecteurs fournis par Microsoft Sentinel et leur état. Sélectionnez le connecteur que vous souhaitez connecter, puis sélectionnez **Ouvrir la page du connecteur**.

![Galerie des connecteurs de données](./media/collect-data/collect-data-page.png)

Vous devez avoir rempli toutes les conditions préalables, et vous verrez des instructions complètes sur la page du connecteur pour ingérer les données dans Microsoft Sentinel. Les données peuvent mettre un certain temps à apparaître. Une fois connecté, vous voyez un récapitulatif des données dans le graphe **Données reçues**, ainsi que l’état de connectivité des types de données.

![Configurer des connecteurs de données](./media/collect-data/opened-connector-page.png)

Dans l’onglet **Étapes suivantes**, vous verrez du contenu supplémentaire fourni par Microsoft Sentinel pour le type de données spécifique (exemples de requêtes, classeurs de visualisation et modèles de règle d’analyse) pour vous aider à détecter et à examiner les menaces.

![Étapes suivantes pour les connecteurs](./media/collect-data/data-insights.png)

Pour plus d’informations, consultez la section correspondant à votre connecteur de données sur la page [Référence des connecteurs de données](data-connectors-reference.md).

## <a name="rest-api-integration"></a>Intégration de l’API REST

De nombreuses technologies de sécurité fournissent un ensemble d’API permettant de récupérer les fichiers journaux, et certaines sources de données peuvent utiliser ces API pour se connecter à Microsoft Sentinel.

Les connecteurs de données qui utilisent des API s’intègrent du côté du fournisseur ou s’intègrent à l’aide d’Azure Functions, comme décrit dans les sections suivantes.

Pour obtenir une liste complète et des informations sur ces connecteurs, consultez [Référence des connecteurs de données](data-connectors-reference.md).

### <a name="rest-api-integration-on-the-provider-side"></a>Intégration de l’API REST côté fournisseur

Une intégration d’API qui est créée par le fournisseur se connecte aux sources de données du fournisseur et envoie les données dans des tables de journal personnalisées de Microsoft Sentinel à l’aide de l’[API de collecte de données Azure Monitor](../azure-monitor/logs/data-collector-api.md).

Pour plus d’informations, consultez la documentation de votre fournisseur et la page [Connecter votre source de données à l’API REST de Microsoft Sentinel pour ingérer des données](connect-rest-api-template.md).

### <a name="rest-api-integration-using-azure-functions"></a>Intégration d’API REST à l’aide d’Azure Functions

Les intégrations qui utilisent [Azure Functions](../azure-functions/index.yml) pour se connecter à une API de fournisseur mettent tout d’abord en forme les données, puis les envoient aux tables de journal personnalisées de Microsoft Sentinel à l’aide de l’[API de collecte de données Azure Monitor](../azure-monitor/logs/data-collector-api.md).

Afin de configurer ces connecteurs de données pour qu’ils se connectent à l’API du fournisseur et collectent des journaux dans Microsoft Sentinel, suivez les étapes indiquées pour chaque connecteur de données dans Microsoft Sentinel.

Pour plus d’informations, consultez [Utiliser Azure Functions pour connecter votre source de données à Microsoft Sentinel](connect-azure-functions-template.md).

> [!IMPORTANT]
> Les intégrations qui utilisent Azure Functions peuvent entraîner des coûts d’ingestion de données supplémentaires, car vous hébergez Azure Functions sur votre locataire Azure. Pour plus d’informations, consultez la page [Tarification d’Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="agent-based-integration"></a>Intégration basée sur l’agent

Microsoft Sentinel peut utiliser le protocole Syslog pour se connecter via un agent à n’importe quelle source de données capable d’effectuer un streaming de journaux en temps réel. Par exemple, la plupart des sources de données locales se connectent par le biais de l’intégration basée sur l’agent.

Les sections suivantes décrivent les différents types de connecteurs de données basés sur l’agent de Microsoft Sentinel. Suivez les étapes de chaque page de connecteur de données Microsoft Sentinel pour configurer les connexions à l’aide de mécanismes basés sur l’agent.

Pour obtenir la liste complète des pare-feu, proxys et points de terminaison qui se connectent à Microsoft Sentinel via CEF ou Syslog, consultez les [informations de référence sur les connecteurs de données](data-connectors-reference.md).

### <a name="syslog"></a>syslog

Vous pouvez diffuser des événements en continu à partir d’appareils Linux prenant en charge Syslog vers Microsoft Sentinel à l’aide de l’agent Log Analytics pour Linux, anciennement appelé agent OMS. L’agent Log Analytics est pris en charge pour tout appareil qui vous permet d’installer l’agent Log Analytics directement sur l’appareil.

Le démon Syslog intégré de l’appareil collecte les événements locaux des types spécifiés et les transfère localement à l’agent, qui les diffuse en continu dans votre espace de travail Log Analytics. Une fois la configuration réussie, les données s’affichent dans la table Syslog de Log Analytics.

En fonction du type d’appareil, l’agent est installé directement sur l’appareil ou sur un redirecteur de journaux Linux dédié. L’agent de Log Analytics reçoit les événements du démon Syslog via UDP. Si une machine Linux est supposée collecter un volume important d’événements Syslog, elle envoie les événements via le protocole TCP du démon Syslog à l’agent, puis de là à Log Analytics.

Pour plus d’informations, consultez [Connecter des appliances Syslog à Microsoft Sentinel](connect-syslog.md).

### <a name="common-event-format-cef"></a>CEF (Common Event Format)

Le format des journaux varie, mais de nombreuses sources prennent en charge le format CEF. L’agent Microsoft Sentinel, qui est en fait l’agent Log Analytics, convertit les journaux au format CEF dans un format que Log Analytics peut ingérer.

Pour les sources de données qui émettent des données au format CEF, configurez l’agent Syslog, puis configurez le flux de données CEF. Une fois la configuration réussie, les données s’affichent dans la table **CommonSecurityLog**.

Pour plus d’informations, consultez [Connecter des appliances CEF à Microsoft Sentinel](connect-common-event-format.md).

### <a name="custom-logs"></a>Journaux d’activité personnalisés

Certaines sources de données disposent de journaux pour la collecte de fichiers sur Windows ou Linux. Vous pouvez collecter ces journaux à l’aide de l’agent de collecte de journaux personnalisé Log Analytics.

Suivez les étapes de chaque page de connecteur de données Microsoft Sentinel pour vous connecter à l’aide de l’agent personnalisé de collecte de journaux Log Analytics. Une fois la configuration réussie, les données s’affichent dans des tables personnalisées.

Pour plus d’information, reportez-vous à [Collecter des données dans des formats de journal personnalisés vers Microsoft Sentinel avec l’agent Log Analytics](connect-custom-logs.md).

## <a name="service-to-service-integration"></a>Intégration service à service

Microsoft Sentinel utilise la fondation Azure pour assurer la prise en charge service à service prête à l’emploi pour les services Microsoft et Amazon Web Services.

Pour plus d’informations, reportez-vous à [Connecter des services Azure, Windows, Microsoft et Amazon](connect-azure-windows-microsoft-services.md) ainsi qu’à la page [Référence des connecteurs de données](data-connectors-reference.md).

## <a name="deploy-as-part-of-a-solution"></a>Déployer en tant que partie d’une solution

Les [solutions Microsoft Sentinel](sentinel-solutions.md) fournissent des packages de contenu de sécurité, notamment des connecteurs de données, des classeurs, des règles d’analyse, des playbooks et bien plus. Lorsque vous déployez une solution avec un connecteur de données, vous obtenez le connecteur de données et le contenu associé dans le même déploiement.

Pour plus d’informations, consultez [Découvrir et déployer de manière centralisée du contenu et des solutions Microsoft Sentinel prêts à l’emploi](sentinel-solutions-deploy.md) et le [catalogue de solutions Microsoft Sentinel](sentinel-solutions-catalog.md).

## <a name="data-connector-support"></a>Prise en charge du connecteur de données

Microsoft et d’autres organisations créent des connecteurs de données Microsoft Sentinel. Chaque connecteur de données dispose de l’un des types de prise en charge suivants :

| Type de support| Description|
|-------------|------------|
|**Pris en charge par Microsoft**|S’applique à :<ul><li>Connecteurs de données pour les sources de données où Microsoft est le fournisseur de données et l’auteur.</li><li>Certains connecteurs de données créés par Microsoft pour des sources de données autres que Microsoft.</li></ul>Microsoft prend en charge et gère les connecteurs de données dans cette catégorie conformément aux [Plans de Support Microsoft Azure](https://azure.microsoft.com/support/options/#overview).<br><br>Les partenaires ou la communauté assurent le support des connecteurs de données créés par les tiers, autres que Microsoft.|
|**Support par un partenaire**|S’applique aux connecteurs de données créés par des tiers, autres que Microsoft.<br><br>La société partenaire assure le support ou la maintenance de ces connecteurs de données. Cette société partenaire peut être un éditeur de logiciels indépendant, un fournisseur de services gérés (MSP/MSSP), un intégrateur de systèmes ou toute organisation dont les coordonnées sont fournies sur la page Microsoft Sentinel de ce connecteur de données.<br><br>Pour tout problème lié à un connecteur de données pris en charge par un partenaire, contactez le contact de support du connecteur de données concerné.|
|**Support par la communauté**|S’applique aux connecteurs de données créés par Microsoft ou par les développeurs partenaires qui n’ont pas de coordonnées répertoriées pour le support et la maintenance du connecteur de données dans la page du connecteur de données spécifié dans Microsoft Sentinel.<br><br>Pour toute question ou tout problème liés à ces connecteurs de données, vous pouvez [signaler un problème](https://github.com/Azure/Azure-Sentinel/issues/new/choose) auprès de la [communauté GitHub de Microsoft Sentinel](https://aka.ms/threathunters).|

### <a name="find-the-support-contact-for-a-data-connector"></a>Rechercher le contact de support pour un connecteur de données

Pour rechercher le contact de support pour un connecteur de données :

1. Dans le menu de gauche de Microsoft Sentinel, sélectionnez **Connecteurs de données**.

1. Sélectionnez le connecteur pour lequel vous souhaitez obtenir les informations de support.

1. Consultez le champ **Support assuré par** dans le volet latéral du connecteur de données.

   ![Capture d’écran montrant le champ « Pris en charge par » d’un connecteur de données dans Microsoft Sentinel.](./media/collect-data/connectors.png)

   Le champ **Support assuré par** contient un lien de contact du support technique pour accéder au support et à la maintenance du connecteur de données sélectionné.

## <a name="next-steps"></a>Étapes suivantes

- Pour utiliser Microsoft Sentinel, vous devez disposer d’un abonnement à Microsoft Azure. Si vous n’avez pas d’abonnement, vous pouvez vous inscrire à un [essai gratuit](https://azure.microsoft.com/free/).
- Découvrez comment [intégrer vos données à Microsoft Sentinel](quickstart-onboard.md) et [obtenir une visibilité de vos données et des menaces](get-visibility.md).
