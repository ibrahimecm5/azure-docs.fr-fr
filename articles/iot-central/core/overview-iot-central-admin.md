---
title: Guide de l’administrateur Azure IoT Central
description: Azure IoT Central est une plateforme d’applications IoT qui simplifie la création de solutions IoT. Cet article propose une vue d’ensemble du rôle Administrateur dans IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 03/25/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: b91a9c451c0160523029cd8688dfa9a91f433b37
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131085921"
---
# <a name="iot-central-administrator-guide"></a>Guide de l’administrateur IoT Central

Une application IoT Central vous permet de surveiller et de gérer des millions d’appareils tout au long de leur cycle de vie. Ce guide est destiné aux administrateurs qui gèrent des applications IoT Central.

Dans IoT Central, un administrateur :

- Gère les utilisateurs et les rôles dans l’application.
- Crée et gère des organisations.
- Gère la sécurité, telle que l’authentification des appareils.
- Configure les paramètres d’application.
- Met à niveau les applications.
- Exporte et partage les applications.
- Supervise l’intégrité des applications.

## <a name="users-and-roles"></a>Utilisateurs et rôles

IoT Central utilise un système de contrôle d’accès en fonction du rôle pour gérer les autorisations des utilisateurs dans une application. IoT Central a trois rôles intégrés pour les administrateurs, les créateurs de solutions et les opérateurs. Un administrateur peut créer des rôles personnalisés avec des ensembles spécifiques d’autorisations. Un administrateur est chargé d’ajouter des utilisateurs à une application et de leur attribuer un rôle.

Pour en savoir plus, consultez [Gérer les utilisateurs et les rôles dans votre application IoT Central](howto-manage-users-roles.md).

## <a name="organizations"></a>Organisations

Les organisations vous permettent de définir une hiérarchie que vous utilisez pour gérer quels utilisateurs peuvent voir quels appareils dans votre application IoT Central. Le rôle de l’utilisateur détermine ses autorisations sur les appareils qu’il voit et les expériences auxquelles il peut accéder.

Pour plus d’informations, consultez [Créer une application IoT Central](howto-create-organizations.md).

## <a name="application-security"></a>Sécurité des applications

Les appareils qui se connectent à votre application IoT Central utilisent généralement des certificats X.509 ou des signatures d’accès partagé (SAP) en guise d’informations d’identification. L’administrateur gère les certificats ou les clés de groupe dont les informations d’identification des appareils sont issues.

Pour plus d’informations, consultez [Inscription de groupe par certificat X.509](concepts-get-connected.md#x509-group-enrollment), [Inscription de groupe par SAP](concepts-get-connected.md#sas-group-enrollment) et [Renouvellement des certificats d’appareil X.509](how-to-roll-x509-certificates.md).

L’administrateur peut également créer et gérer les jetons d’API qu’une application cliente utilise pour s’authentifier auprès de votre application IoT Central. Les applications clientes utilisent l’API REST pour interagir avec IoT Central.

Pour les exportations de données, l’administrateur peut configurer des [identités gérées](../../active-directory/managed-identities-azure-resources/overview.md) pour sécuriser les connexions aux [destinations d’exportation](howto-export-data.md). Pour plus d'informations, consultez les rubriques suivantes :

- [Configurer une identité managée (Portail Azure)](howto-manage-iot-central-from-portal.md#configure-a-managed-identity)
- [Configurer une identité managée (API REST)](howto-manage-iot-central-with-rest-api.md)
- [Configurer une identité managée (Azure CLI)](howto-manage-iot-central-from-cli.md#configure-a-managed-identity)

## <a name="configure-an-application"></a>Configurer une application

L’administrateur peut configurer le comportement et l’apparence d’une application IoT Central. Pour plus d'informations, consultez les rubriques suivantes :

- [Modifier le nom et l’URL de l’application](howto-administer.md#change-application-name-and-url)
- [Personnaliser l’interface utilisateur](howto-customize-ui.md)
- [Déplacer une application vers un autre plan tarifaire](howto-faq.yml#how-do-i-move-from-a-free-to-a-standard-pricing-plan-)
- [Configurer les chargements de fichiers](howto-configure-file-uploads.md)

## <a name="export-an-application"></a>Exporter une application

Un administrateur peut :

- Créer une copie d’une application s’il a juste besoin d’une copie dupliquée de l’application. Par exemple, il peut avoir besoin d’une copie dupliquée pour effectuer des tests.
- Créer un modèle d’application à partir d’une application existante s’il envisage de créer plusieurs copies.

Pour en savoir plus, consultez [Créer et utiliser un modèle d’application personnalisé](howto-create-iot-central-application.md#create-and-use-a-custom-application-template).

## <a name="migrate-to-a-new-version"></a>Migrer vers une nouvelle version

Un administrateur peut migrer une application vers une version plus récente. Actuellement, une application nouvellement créée est une application v3. Un administrateur peut avoir besoin de migrer une application v2 vers une application v3.

Pour en savoir plus, consultez [Migrer votre application IoT Central v2 vers v3](howto-migrate.md).

## <a name="monitor-application-health"></a>Superviser l’intégrité de l’application

Un administrateur peut utiliser les métriques d’IoT Central pour évaluer l’intégrité des appareils connectés et l’intégrité des exportations de données en cours d’exécution.

Pour afficher les métriques, un administrateur peut utiliser des graphiques dans le portail Azure, une API REST ou des requêtes PowerShell ou Azure CLI.

Pour en savoir plus, consultez [Superviser l’intégrité de l’application](howto-manage-iot-central-from-portal.md#monitor-application-health).

## <a name="monitor-connected-iot-edge-devices"></a>Superviser les appareils IoT Edge connectés

Pour savoir comment superviser à distance votre flotte IoT Edge à l’aide d’Azure Monitor et de l’intégration de métriques prédéfinies, consultez [Collecter et transporter des métriques](../../iot-edge/how-to-collect-and-transport-metrics.md).

## <a name="tools"></a>Outils

La plupart des outils que vous utilisez en tant qu’administrateur sont disponibles dans la section **Administration** de chaque application IoT Central. Vous pouvez également utiliser les outils suivants pour effectuer certaines tâches administratives :

- [Ligne de commande Azure](howto-manage-iot-central-from-cli.md)
- [Azure portal](howto-manage-iot-central-from-portal.md)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à administrer votre application Azure IoT Central, nous vous recommandons d’en apprendre davantage sur la [gestion des utilisateurs et des rôles](howto-manage-users-roles.md) dans Azure IoT Central.
