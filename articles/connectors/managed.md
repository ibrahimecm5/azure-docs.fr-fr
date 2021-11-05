---
title: Opérations du connecteur managé
description: Utilisez des actions et des déclencheurs gérés par Microsoft pour créer des workflows automatisés qui intègrent d’autres applications, données, services et systèmes à l’aide d’Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 05/16/2021
ms.openlocfilehash: 4f0bb89992f76e7a8eb9311d733a3b791b73114a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131086599"
---
# <a name="managed-connectors-in-azure-logic-apps"></a>Connecteurs managés dans Azure Logic Apps

Les [connecteurs gérés](apis-list.md) vous permettent d’accéder à d’autres services et systèmes sur lesquels les [actions et déclencheurs intégrés](built-in.md) ne sont pas disponibles. Vous pouvez utiliser ces déclencheurs et actions pour créer des workflows qui intègrent des données, des applications, des services basés sur le cloud et des systèmes locaux. Par rapport aux actions et déclencheurs intégrés, ces connecteurs sont généralement liés à un service ou à un système spécifique, comme le stockage d’objets blob Azure, les serveurs Office 365, SQL, Salesforce ou SFTP. Gérés par Microsoft et hébergés dans Azure, les connecteurs gérés requièrent généralement la création d’une connexion à partir de votre workflow et l’authentification de votre identité. Des déclencheurs basés sur une périodicité et un webhook sont disponibles. Par conséquent, si vous utilisez un déclencheur basé sur la périodicité, passez en revue la [Vue d’ensemble du comportement de périodicité](apis-list.md#recurrence-behavior).

Pour un petit nombre de services, de systèmes et de protocoles, Azure Logic Apps fournit des opérations intégrées avec leurs [versions de connecteur managés](managed.md). Le nombre et la plage disponibles varient selon que vous créez une ressource d’application logique basée sur un plan de consommation qui s’exécute dans Azure Logic Apps mutualisé ou une ressource d’application logique basée sur un plan Standard qui s’exécute dans Azure Logic Apps avec un locataire unique. Pour plus d’informations, consultez [Architecture monolocataire ou multilocataire et environnement de service d’intégration (ISE)](../logic-apps/single-tenant-overview-compare.md). Dans la plupart des cas, la version intégrée offre de meilleures performances, fonctionnalités, conditions tarifaires, et ainsi de suite.

Par exemple, si vous créez une application logique à locataire unique, les opérations intégrées sont disponibles pour Azure Service Bus, Azure Event Hubs, SQL Server et MQ. Dans certains cas, une version intégrée et une version de connecteur managé sont toutes deux disponibles. Dans la plupart des cas, la version intégrée offre de meilleures performances, fonctionnalités, conditions tarifaires, et ainsi de suite. Si vous créez une application logique mutualisée, les opérations intégrées sont disponibles pour Azure Functions, Azure App Services et Gestion des API Azure.

Certains connecteurs managés pour Azure Logic Apps appartiennent à plusieurs sous-catégories. Par exemple, le connecteur SAP est à la fois un [connecteur d’entreprise](#enterprise-connectors) et un [connecteur local](#on-premises-connectors).

* Les [connecteurs standard](#standard-connectors) fournissent un accès à des services tels que le stockage d’objets blob Azure, Office 365, SharePoint, Salesforce, Power BI, OneDrive et bien d’autres.
* Les [connecteurs entreprise](#enterprise-connectors) permettent d’accéder aux systèmes d’entreprise tels que SAP, IBM MQ et IBM 3270.
* Les [connecteurs locaux fournissent](#on-premises-connectors) fournissent un accès à des systèmes locaux tels que SQL Server, SharePoint Server, SAP, Oracle DB, les partages de fichiers et d’autres.
* Les [connecteurs de compte d’intégration](#integration-account-connectors) vous aident à transformer et valider le code XML, encodent et décodent les fichiers plats, et traitent les messages entreprise-entreprise (B2B) avec les protocoles AS2, EDIFACT et X12.
* Les [connecteurs d’environnement de service d’intégration](#ise-connectors) et sont conçus pour s’exécuter spécifiquement dans un environnement ISE et offrent des avantages par rapport à leurs versions non-ISE.

## <a name="standard-connectors"></a>Connecteurs standard

Azure Logic Apps fournit ces connecteurs standard populaires pour créer des workflows automatisés à l’aide de ces services et systèmes. Certains connecteurs standard prennent également en charge les [systèmes locaux](#on-premises-connectors) ou les [comptes d’intégration](#integration-account-connectors).

:::row:::
    :::column:::
        [![Icône Azure Service Bus][azure-service-bus-icon]][azure-service-bus-doc]
        \
        \
        [**Azure Service Bus**][azure-service-bus-doc]
        \
        \
        Gérez les messages asynchrones, les sessions et les abonnements à une rubrique avec le connecteur le plus couramment utilisé dans Logic Apps.
    :::column-end:::
    :::column:::
        [![Icône SQL Server][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server**][sql-server-doc]
        \
        \
        Connectez-vous à votre serveur SQL local ou à Azure SQL Database dans le cloud pour gérer les enregistrements, exécuter des procédures stockées ou exécuter des requêtes.
    :::column-end:::
    :::column:::
        [![Icône Azure Blog Storage][azure-blob-storage-icon]][azure-blob-storage-doc]
        \
        \
        [**Stockage Blob Azure**][azure-blob-storage-doc]
        \
        \
        Connectez-vous à votre compte de stockage Azure afin de créer et de gérer du contenu d’objet blob.
    :::column-end:::
    :::column:::
        [![Icône Office 365 Outlook][office-365-outlook-icon]][office-365-outlook-doc]
        \
        \
        [**Office 365 Outlook**][office-365-outlook-doc]
        \
        \
        Connectez-vous à votre compte de courrier professionnel ou scolaire pour créer et gérer des e-mails, des tâches, des événements de calendrier, des réunions, des contacts, des requêtes et bien plus encore.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Icône STFP-SSH][sftp-ssh-icon]][sftp-ssh-doc]
        \
        \
        [**STFP-SSH**][sftp-ssh-doc]
        \
        \
        Connectez-vous aux serveurs SFTP auxquels vous avez accès à partir d’Internet via une connexion SSH afin de pouvoir utiliser vos fichiers et vos dossiers.
    :::column-end:::
    :::column:::
        [![Icône SharePoint Online][sharepoint-online-icon]][sharepoint-online-doc]
        \
        \
        [**SharePoint Online**][sharepoint-online-doc]
        \
        \
        Connectez-vous à SharePoint Online pour gérer des fichiers, des pièces jointes, des dossiers et bien plus encore.
    :::column-end:::
    :::column:::
        [![Icône Files d’attente Azure][azure-queues-icon]][azure-queues-doc]
        \
        \
        [**Files d’attente Azure**][azure-queues-doc]
        \
        \
        Connectez-vous à votre compte de stockage Azure pour créer et gérer des files d’attente et des messages.
    :::column-end:::
    :::column:::
        [![Icône FTP][ftp-icon]][ftp-doc]
        \
        \
        [**FTP**][ftp-doc]
        \
        \
        Connectez-vous aux serveurs FTP auxquels vous avez accès à partir d’Internet pour utiliser vos fichiers et vos dossiers.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Icône système de fichiers][file-system-icon]][file-system-doc]
        \
        \
        [**Système de fichiers**][file-system-doc]
        \
        \
        Connectez-vous à votre partage de fichiers local afin de créer et de gérer des fichiers.
    :::column-end:::
    :::column:::
        [![Icône Azure Event Hubs][azure-event-hubs-icon]][azure-event-hubs-doc]
        \
        \
        [**Azure Event Hubs**][azure-event-hubs-doc]
        \
        \
        Consommez et publiez des événements via un Event Hub. Par exemple, obtenez une sortie à partir de votre application logique à l’aide des Event Hubs, puis envoyez-la à un fournisseur d’analyses en temps réel.
    :::column-end:::
    :::column:::
        [![Icône Azure Event Grid][azure-event-grid-icon]][azure-event-grid-doc]
        \
        \
        [**Azure Event Grid**][azure-event-grid-doc]
        \
        \
        Surveillez les événements publiés par une grille d’événements, par exemple, lorsque les ressources Azure ou les ressources tierces changent.
    :::column-end:::
    :::column:::
        [![Icône Salesforce][salesforce-icon]][salesforce-doc]
        \
        \
        [**Salesforce**][salesforce-doc]
        \
        \
        Connectez-vous à votre compte Salesforce pour créer et gérer des éléments tels que des enregistrements, des travaux, des objets et bien plus encore.
    :::column-end:::
:::row-end:::

## <a name="on-premises-connectors"></a>Connecteurs locaux

Avant de créer une connexion à un système local, vous devez d’abord [télécharger, installer et configurer une passerelle de données locale][gateway-doc]. Cette passerelle fournit un canal de communication sécurisé sans avoir à configurer l’infrastructure réseau nécessaire.

Les [connecteurs standard](#standard-connectors) suivants sont si utilisés qu’Azure Logic Apps fournit pour accéder aux données et aux ressources des systèmes locaux. Pour obtenir la liste des connecteurs locaux, consultez [Sources de données prises en charge](../logic-apps/logic-apps-gateway-connection.md#supported-connections).

:::row:::
    :::column:::
        [![Icône BizTalk Server][biztalk-server-icon]][biztalk-server-doc]
        \
        \
        [**Biztalk Server**][biztalk-server-doc]
    :::column-end:::
    :::column:::
        [![Icône système de fichiers][file-system-icon]][file-system-doc]
        \
        \
        [**Système de fichiers**][file-system-doc]
    :::column-end:::
    :::column:::
        [![Icône IBM DB2][ibm-db2-icon]][ibm-db2-doc]
        \
        \
        [**IBM DB2**][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![Icône IBM Informix][ibm-informix-icon]][ibm-informix-doc]
        \
        \
        [**IBM Informix**][ibm-informix-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Icône MySQL][mysql-icon]][mysql-doc]
        \
        \
        [**MySQL**][mysql-doc]
    :::column-end:::
    :::column:::
        [![Icône Oracle DB][oracle-db-icon]][oracle-db-doc]
        \
        \
        [**Oracle DB**][oracle-db-doc]
    :::column-end:::
    :::column:::
        [![Icône PostgreSQL][postgre-sql-icon]][postgre-sql-doc]
        \
        \
        [**PostgreSQL**][postgre-sql-doc]
    :::column-end:::
    :::column:::
        [![Icône SharePoint Server][sharepoint-server-icon]][sharepoint-server-doc]
        \
        \
        [**SharePoint Server**][sharepoint-server-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Icône SQL Server][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server**][sql-server-doc]
    :::column-end:::
    :::column:::
        [![Icône Teradata][teradata-icon]][teradata-doc]
        \
        \
        [**Teradata**][teradata-doc]
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>Connecteurs de compte d’intégration

Les connecteurs de compte d’intégration prennent en charge les [scénarios de communication B2B](../logic-apps/logic-apps-enterprise-integration-overview.md) dans Azure Logic Apps. Après avoir [créé un compte d’intégration](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) et défini vos artefacts B2B, tels que les partenaires commerciaux, les contrats, les mappages et les schémas, vous pouvez utiliser des connecteurs de compte d’intégration pour encoder et décoder des messages, transformer du contenu, et bien plus encore.

Par exemple, si vous utilisez Microsoft BizTalk Server, vous pouvez créer une connexion à partir de votre workflow à l’aide du [connecteur local BizTalk Server](#on-premises-connectors). Vous pouvez ensuite étendre ou exécuter des opérations de type BizTalk dans votre workflow à l’aide de connecteurs de compte d’intégration.

> [!NOTE]
> Avant de pouvoir utiliser des connecteurs de compte d’intégration dans des Azure Logic Apps basés sur un plan de consommation mutualisée, vous devez [lier votre ressource d’application logique à un compte d’intégration](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md). 

:::row:::
    :::column:::
        [![Icône décodage AS2][as2-icon]][as2-doc]
        \
        \
        [**Décodage AS2**][as2-doc]
    :::column-end:::
    :::column:::
        [![Icône encodage AS2][as2-icon]][as2-doc]
        \
        \
        [**Encodage AS2**][as2-doc]
    :::column-end:::
    :::column:::
        [![Icône décodage EDIFACT][edifact-icon]][edifact-decode-doc]
        \
        \
        [**Décodage EDIFACT**][edifact-decode-doc]
    :::column-end:::
    :::column:::
        [![Icône encodage EDIFACT][edifact-icon]][edifact-encode-doc]
        \
        \
        [**Codage EDIFACT**][edifact-encode-doc]
    :::column-end:::
    :::column:::
        [![Icône décodage X12][x12-icon]][x12-decode-doc]
        \
        \
        [**Décodage X12**][x12-decode-doc]
    :::column-end:::
    :::column:::
        [![Icône encodage X12][x12-icon]][x12-encode-doc]
        \
        \
        [**Codage X12**][x12-encode-doc]
    :::column-end:::
:::row-end:::

## <a name="enterprise-connectors"></a>Connecteurs d’entreprise

Les connecteurs suivants fournissent un accès aux systèmes d’entreprise pour un coût supplémentaire :

:::row:::
    :::column:::
        [![Icône IBM 3270][ibm-3270-icon]][ibm-3270-doc]
        \
        \
        [**IBM 3270**][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![Icône IBM MQ][ibm-mq-icon]][ibm-mq-doc]
        \
        \
        [**IBM MQ**][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![Icône SAP][sap-icon]][sap-connector-doc]
        \
        \
        [**SAP**][sap-connector-doc]
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="ise-connectors"></a>Connecteurs ISE

Dans un environnement de service d’intégration (ISE), ces connecteurs managés disposent également de [versions ISE](apis-list.md#ise-and-connectors), qui ont des fonctionnalités différentes de celles de leurs versions mutualisées :

> [!NOTE]
> Les applications logiques qui s’exécutent dans un environnement ISE, ainsi que leurs connecteurs, quel que soit l’endroit où ceux-ci s’exécutent, suivent un plan tarifaire fixe et non un plan tarifaire basé sur la consommation. Pour plus d’informations, consultez le [Modèle tarifaire de Logic Apps](../logic-apps/logic-apps-pricing.md) et [Informations tarifaires sur Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/).

:::row:::
    :::column:::
        [![Icône AS2 ISE][as2-icon]][as2-doc]
        \
        \
        [**AS2** ISE][as2-doc]
    :::column-end:::
    :::column:::
        [![Icône Azure Automation ISE][azure-automation-icon]][azure-automation-doc]
        \
        \
        [**Azure Automation** ISE][azure-automation-doc]
    :::column-end:::
    :::column:::
        [![Icône Stockage Blob Azure ISE][azure-blob-storage-icon]][azure-blob-storage-doc]
        \
        \
        [**Azure Blob Storage** ISE][azure-blob-storage-doc]
    :::column-end:::
    :::column:::
        [![Icône Azure Cosmos DB ISE][azure-cosmos-db-icon]][azure-cosmos-db-doc]
        \
        \
        [**Azure Cosmos DB** ISE][azure-cosmos-db-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Icône Azure Event Hubs ISE][azure-event-hubs-icon]][azure-event-hubs-doc]
        \
        \
        [**Azure Event Hubs** ISE][azure-event-hubs-doc]
    :::column-end:::
    :::column:::
        [![Icône Azure Event Grid ISE][azure-event-grid-icon]][azure-event-grid-doc]
        \
        \
        [**Azure Event Grid** ISE][azure-event-grid-doc]
    :::column-end:::
    :::column:::
        [![Icône Azure Files ISE][azure-file-storage-icon]][azure-file-storage-doc]
        \
        \
        [**Azure Files** ISE][azure-file-storage-doc]
    :::column-end:::
    :::column:::
        [![Icône Azure Key Vault ISE][azure-key-vault-icon]][azure-key-vault-doc]
        \
        \
        [**Azure Key Vault** ISE][azure-key-vault-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Icône Journaux d’activité Azure Monitor][azure-monitor-logs-icon]][azure-monitor-logs-doc]
        \
        \
        [**Journaux d’activité Azure Monitor** ISE][azure-monitor-logs-doc]
    :::column-end:::
    :::column:::
        [![Icône Azure Service Bus ISE][azure-service-bus-icon]][azure-service-bus-doc]
        \
        \
        [**Azure Service Bus** ISE][azure-service-bus-doc]
    :::column-end:::
    :::column:::
        [![Icône Azure Synapse Analytics ISE][azure-sql-data-warehouse-icon]][azure-sql-data-warehouse-doc]
        \
        \
        [**Azure Synapse Analytics** ISE][azure-sql-data-warehouse-doc]
    :::column-end:::
    :::column:::
        [![Icône Stockage Table Azure ISE][azure-table-storage-icon]][azure-table-storage-doc]
        \
        \
        [**Stockage Table Azure** ISE][azure-table-storage-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Icône Files d’attente Azure ISE][azure-queues-icon]][azure-queues-doc]
        \
        \
        [**Files d’attente Azure** ISE][azure-queues-doc]
    :::column-end:::
    :::column:::
        [![Icône EDIFACT ISE][edifact-icon]][edifact-doc]
        \
        \
        [**EDIFACT** ISE][edifact-doc]
    :::column-end:::
    :::column:::
        [![Icône Système de fichiers ISE][file-system-icon]][file-system-doc]
        \
        \
        [**Système de fichiers** ISE][file-system-doc]
    :::column-end:::
    :::column:::
        [![Icône FTP ISE][ftp-icon]][ftp-doc]
        \
        \
        [**FTP** ISE][ftp-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Icône IBM 3270 ISE][ibm-3270-icon]][ibm-3270-doc]
        \
        \
        [**IBM 3270** ISE][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![Icône IBM DB2 ISE][ibm-db2-icon]][ibm-db2-doc]
        \
        \
        [**IBM DB2** ISE][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![Icône IBM MQ ISE][ibm-mq-icon]][ibm-mq-doc]
        \
        \
        [**IBM MQ** ISE][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![Icône SAP ISE][sap-icon]][sap-connector-doc]
        \
        \
        [**SAP** ISE][sap-connector-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Icône SFTP-SSH ISE][sftp-ssh-icon]][sftp-ssh-doc]
        \
        \
        [**SFTP-SSH** ISE][sftp-ssh-doc]
    :::column-end:::
    :::column:::
        [![Icône SMTP ISE][smtp-icon]][smtp-doc]
        \
        \
        [**SMTP** ISE][smtp-doc]
    :::column-end:::
    :::column:::
        [![Icône SQL Server ISE][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server** ISE][sql-server-doc]
    :::column-end:::
    :::column:::
        [![Icône X12 ISE][x12-icon]][x12-doc]
        \
        \
        [**X12** ISE][x12-doc]
    :::column-end:::
:::row-end:::

Pour plus d’informations, consultez les rubriques suivantes :

* [Accéder à des ressources de réseau virtuel Azure à partir d’Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Modèle de tarification de Logic Apps](../logic-apps/logic-apps-pricing.md)
* [Se connecter à des réseaux virtuels Azure à partir d’Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer des API personnalisées que vous pouvez appeler à partir de Logic Apps](../logic-apps/logic-apps-create-api-app.md)

<!--Managed connector icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-cosmos-db-icon]: ./media/apis-list/azure-cosmos-db.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-devops-icon]: ./media/apis-list/azure-devops.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-file-storage-icon]: ./media/apis-list/azure-file-storage.png
[azure-key-vault-icon]: ./media/apis-list/azure-key-vault.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-monitor-logs-icon]: ./media/apis-list/azure-monitor-logs.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
[azure-sql-data-warehouse-icon]: ./media/apis-list/azure-sql-data-warehouse.png
[azure-table-storage-icon]: ./media/apis-list/azure-table-storage.png
[basecamp-3-icon]: ./media/apis-list/basecamp.png
[bitbucket-icon]: ./media/apis-list/bitbucket.png
[bitly-icon]: ./media/apis-list/bitly.png
[biztalk-server-icon]: ./media/apis-list/biztalk.png
[blogger-icon]: ./media/apis-list/blogger.png
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
[file-system-icon]: ./media/apis-list/file-system.png
[ftp-icon]: ./media/apis-list/ftp.png
[github-icon]: ./media/apis-list/github.png
[google-calendar-icon]: ./media/apis-list/google-calendar.png
[google-drive-icon]: ./media/apis-list/google-drive.png
[google-sheets-icon]: ./media/apis-list/google-sheet.png
[google-tasks-icon]: ./media/apis-list/google-tasks.png
[hipchat-icon]: ./media/apis-list/hipchat.png
[ibm-3270-icon]: ./media/apis-list/ibm-3270.png
[ibm-db2-icon]: ./media/apis-list/ibm-db2.png
[ibm-informix-icon]: ./media/apis-list/ibm-informix.png
[ibm-mq-icon]: ./media/apis-list/ibm-mq.png
[insightly-icon]: ./media/apis-list/insightly.png
[instagram-icon]: ./media/apis-list/instagram.png
[instapaper-icon]: ./media/apis-list/instapaper.png
[jira-icon]: ./media/apis-list/jira.png
[mandrill-icon]: ./media/apis-list/mandrill.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
[salesforce-icon]: ./media/apis-list/salesforce.png
[sap-icon]: ./media/apis-list/sap.png
[send-grid-icon]: ./media/apis-list/sendgrid.png
[sftp-ssh-icon]: ./media/apis-list/sftp.png
[sharepoint-online-icon]: ./media/apis-list/sharepoint-online.png
[sharepoint-server-icon]: ./media/apis-list/sharepoint-server.png
[slack-icon]: ./media/apis-list/slack.png
[smartsheet-icon]: ./media/apis-list/smartsheet.png
[smtp-icon]: ./media/apis-list/smtp.png
[sparkpost-icon]: ./media/apis-list/sparkpost.png
[sql-server-icon]: ./media/apis-list/sql.png
[teradata-icon]: ./media/apis-list/teradata.png
[todoist-icon]: ./media/apis-list/todoist.png
[twilio-icon]: ./media/apis-list/twilio.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[wordpress-icon]: ./media/apis-list/wordpress.png
[youtube-icon]: ./media/apis-list/youtube.png

<!--Managed connector doc links-->
[azure-automation-doc]: /connectors/azureautomation/ "Créer et gérer des travaux d’automatisation pour l’infrastructure locale et cloud"
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Gérer les fichiers de votre conteneur d’objets blob avec le connecteur Azure Blob Storage"
[azure-cosmos-db-doc]: ./connectors-create-api-cosmos-db.md "Se connecter à Azure Cosmos DB afin de pouvoir accéder aux documents Azure Cosmos DB et les gérer"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md " Superviser les événements publiés par Event Grid, par exemple, lorsque les ressources Azure ou les ressources tierces changent"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Se connecter à Azure Event Hubs pour l’envoi et la réception d’événements entre vos applications logiques et Event Hubs"
[azure-file-storage-doc]: /connectors/azurefile/ "Se connecter à votre compte de stockage Azure pour créer, mettre à jour, récupérer et supprimer des fichiers"
[azure-key-vault-doc]: /connectors/keyvault/ "Se connecter à Azure Key Vault pour pouvoir gérer les secrets et les clés"
[azure-monitor-logs-doc]: /connectors/azuremonitorlogs/ "Exécuter des requêtes sur des journaux d’activité Azure Monitor dans les espaces de travail Log Analytics et les composants Application Insights"
[azure-queues-doc]: /connectors/azurequeues/ "Se connecter à votre compte de stockage Azure pour créer et gérer des files d’attente et des messages"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Gérer les messages des files d’attente, rubriques et abonnements aux rubriques Service Bus"
[azure-sql-data-warehouse-doc]: /connectors/sqldw/ "Se connecter à Azure Synapse Analytics pour voir vos données"
[azure-table-storage-doc]: /connectors/azuretables/ "Se connecter à votre compte de stockage Azure pour créer, mettre à jour et interroger des tables"
[biztalk-server-doc]: /connectors/biztalk/ "Se connecter à BizTalk Server pour exécuter des applications BizTalk en même temps qu’Azure Logic Apps"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "Se connecter à un système de fichiers local"
[ftp-doc]: ./connectors-create-api-ftp.md "Se connecter à un serveur FTP/FTPS pour les tâches FTP (notamment télécharger, obtenir et supprimer des fichiers)."
[github-doc]: ./connectors-create-api-github.md "Se connecter à GitHub et suivre les problèmes"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Se connecter à Google Agenda et gérer un agenda"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Se connecter à Google Sheets pour modifier vos feuilles"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Se connecter à Google Tasks pour gérer vos tâches"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "Se connecter à des applications 3270 sur des mainframes IBM"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Se connecter à IBM DB2 dans le nuage ou sur site. Mettre à jour une ligne, obtenir une table et bien plus encore"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Se connecter à Informix dans le nuage ou sur site. Lire une ligne, répertorier les tables et bien plus encore"
[ibm-mq-doc]: ./connectors-create-api-mq.md "Se connecter à IBM MQ en local ou dans Azure pour envoyer et recevoir des messages"
[instagram-doc]: ./connectors-create-api-instagram.md "Se connecter à Instagram. Déclencher ou agir sur les événements"
[mandrill-doc]: ./connectors-create-api-mandrill.md "Se connecter à Mandrill pour la communication"
[mysql-doc]: /connectors/mysql/ "Se connecter à votre base de données MySQL locale pour lire et écrire des données"
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "Se connecter à votre compte professionnel ou scolaire pour envoyer et recevoir des e-mails, gérer votre calendrier et vos contacts, etc."
[onedrive-doc]: ./connectors-create-api-onedrive.md "Se connecter à votre Microsoft OneDrive personnel pour charger, supprimer, lister des fichiers, etc."
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "Se connecter à votre Microsoft OneDrive professionnel pour charger, supprimer, lister des fichiers, etc."
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Se connecter à une base de données Oracle pour ajouter, insérer, supprimer des lignes, etc."
[outlook.com-doc]: ./connectors-create-api-outlook.md "Se connecter à votre boîte aux lettres Outlook pour gérer vos e-mails, vos calendriers, vos contacts, etc."
[postgre-sql-doc]: /connectors/postgresql/ "Se connecter à votre base de données PostgreSQL pour lire des données à partir des tables"
[salesforce-doc]: ./connectors-create-api-salesforce.md "Se connecter à votre compte Salesforce. Gérer les comptes, les prospects, les opportunités et bien plus encore"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "Se connecter à un système SAP local"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "Se connecter à SendGrid. Envoyer un courrier électronique et gérer les listes des destinataires"
[sftp-ssh-doc]: ./connectors-sftp-ssh.md "Se connecter à votre compte SFTP via SSH. Télécharger, obtenir, supprimer des fichiers et bien plus encore"
[sharepoint-server-doc]: ./connectors-create-api-sharepoint.md "Se connecter au serveur local SharePoint. Gérer des documents, des éléments de liste et bien plus encore"
[sharepoint-online-doc]: ./connectors-create-api-sharepoint.md "Se connecter à SharePoint Online. Gérer des documents, des éléments de liste et bien plus encore"
[slack-doc]: ./connectors-create-api-slack.md "Se connecter à Slack et publier des messages sur les canaux Slack"
[smtp-doc]: ./connectors-create-api-smtp.md "Se connecter à un serveur SMTP et envoyer du courrier électronique avec des pièces jointes"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Se connecter à SparkPost pour la communication"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Se connecter à Azure SQL Database ou SQL Server. Créer, mettre à jour, obtenir et supprimer des entrées dans une table de base de données SQL"
[teradata-doc]: /connectors/teradata/ "Se connecter à votre base de données Teradata pour lire des données à partir des tables"
[twilio-doc]: ./connectors-create-api-twilio.md "Se connecter à Twilio. Envoyer et obtenir des messages, obtenir des numéros disponibles, gérer des numéros de téléphone entrants et bien plus encore"
[youtube-doc]: ./connectors-create-api-youtube.md "Se connecter à YouTube. Gérer vos vidéos et vos canaux"

<!--Integration account connector icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[x12-icon]: ./media/apis-list/x12.png

<!-- Integration account connector docs -->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Encoder et décoder des messages qui utilisent le protocole AS2"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "Encoder et décoder des messages qui utilisent le protocole EDIFACT"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "Décoder les messages qui utilisent le protocole EDIFACT"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "Encoder les messages qui utilisent le protocole EDIFACT"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Encoder et décoder des messages qui utilisent le protocole X12"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Décoder les messages qui utilisent le protocole X12"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Encoder les messages qui utilisent le protocole X12"

<!--Other doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Connexion à des sources de données locales à partir d’applications logiques avec la passerelle de données locale"