---
title: Utiliser la fonctionnalité de diagnostic de connectivité dans le runtime d’intégration SSIS
description: Résolvez les problèmes de connexion dans le runtime d’intégration SSIS à l’aide de la fonctionnalité de diagnostic de connectivité.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.author: meiyl
author: meiyl
ms.reviewer: sawinark
ms.date: 10/22/2021
ms.openlocfilehash: d4e5bfd39cf66733d49e229ccc01342e310775df
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131849194"
---
# <a name="use-the-diagnose-connectivity-feature-in-the-ssis-integration-runtime"></a>Utiliser la fonctionnalité de diagnostic de connectivité dans le runtime d’intégration SSIS

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Vous pouvez rencontrer des problèmes de connectivité en exécutant des packages SQL Server Integration Services (SSIS) dans le runtime d’intégration SSIS. Ces problèmes se produisent surtout si votre runtime d’intégration SSIS rejoint le réseau virtuel Azure.

Résolvez les problèmes de connectivité à l’aide de la fonctionnalité *diagnostiquer la connectivité* pour tester les connexions. Cette fonctionnalité se trouve dans la page d’analyse du runtime d’intégration SSIS du portail Azure Data Factory.

 :::image type="content" source="media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-diagnose-connectivity.png" alt-text="Page Analyser - diagnostiquer la connectivité":::

 :::image type="content" source="media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-test-connection.png" alt-text="Page Analyser - tester la connexion":::

Utilisez les sections suivantes pour en savoir plus sur les erreurs les plus courantes susceptibles de se produire lorsque vous testez les connexions. Chaque section décrit les éléments suivants :

- Code d'erreur
- Message d’erreur
- Cause(s) potentielle(s) de l’erreur
- Solution(s) recommandée(s)

## <a name="error-code-invalidinput"></a>Code d’erreur : InvalidInput

- **Message d’erreur** : « Vérifiez que votre entrée est correcte. »
- **Cause potentielle** : Votre entrée est incorrecte.
- **Recommandation** : Vérifiez votre entrée.

## <a name="error-code-firewallornetworkissue"></a>Code d’erreur : FirewallOrNetworkIssue

- **Message d’erreur** : « Vérifiez que ce port est ouvert sur votre pare-feu/serveur/groupe de sécurité réseau et que le réseau est stable. »
- **Causes potentielles :**
  - Votre serveur n’ouvre pas le port.
  - Le trafic sortant est refusé à votre groupe de sécurité réseau sur ce port.
  - Votre pare-feu NVA/Azure/local n’ouvre pas le port.
- **Recommandations :**
  - Ouvrez le port sur le serveur.
  - Mettez à jour le groupe de sécurité réseau pour autoriser le trafic sortant sur le port.
  - Ouvrez le port sur le pare-feu NVA/Azure/local.

## <a name="error-code-misconfigureddnssettings"></a>Code d’erreur : MisconfiguredDnsSettings

- **Message d’erreur** : « Si vous utilisez votre propre serveur DNS dans le réseau virtuel rejoint par votre Azure-SSIS IR, vérifiez qu’il peut résoudre votre nom d’hôte. »
- **Causes potentielles :**
  -  Problème avec votre DNS personnalisé.
  -  Vous n’utilisez pas un nom de domaine complet (FQDN) pour votre nom d’hôte privé.
- **Recommandations :**
  -  Corrigez votre problème de DNS personnalisé pour vous assurer qu’il peut résoudre le nom d’hôte.
  -  Utilisez le nom de domaine complet. Azure-SSIS IR n’ajoute pas automatiquement votre propre suffixe DNS. Par exemple, utilisez **<your_private_server>.contoso.com** à la place de **<your_private_server>** .

## <a name="error-code-servernotallowremoteconnection"></a>Code d’erreur : ServerNotAllowRemoteConnection

- **Message d’erreur** : « Vérifiez que votre serveur autorise les connexions TCP distantes via ce port. »
- **Causes potentielles :**
  -  Le pare-feu de votre serveur n’autorise pas les connexions TCP distantes.
  -  Votre serveur n’est pas connecté.
- **Recommandations :**
  -  Autorisez les connexions TCP distantes sur le pare-feu du serveur.
  -  Démarrez le serveur.
   
## <a name="error-code-misconfigurednsgsettings"></a>Code d’erreur : MisconfiguredNsgSettings

- **Message d’erreur** : « Vérifiez que le groupe de sécurité réseau de votre réseau virtuel autorise le trafic sortant via ce port. Si vous utilisez Azure ExpressRoute et/ou un itinéraire défini par l’utilisateur, vérifiez que ce port est ouvert sur votre pare-feu/serveur. »
- **Causes potentielles :**
  -  Le trafic sortant est refusé à votre groupe de sécurité réseau sur ce port.
  -  Votre pare-feu NVA/Azure/local n’ouvre pas le port.
- **Recommandation :**
  -  Mettez à jour le groupe de sécurité réseau pour autoriser le trafic sortant sur le port.
  -  Ouvrez le port sur le pare-feu NVA/Azure/local.

## <a name="error-code-genericissues"></a>Code d’erreur : GenericIssues

- **Message d’erreur** : « Échec du test de connexion en raison de problèmes génériques. »
- **Cause potentielle** : Le test de connexion a rencontré un problème temporaire général.
- **Recommandation** : Réessayez le test de connexion ultérieurement. Si la nouvelle tentative échoue, contactez l’équipe du support Azure Data Factory.

## <a name="error-code-pspingexecutiontimeout"></a>Code d’erreur : PSPingExecutionTimeout

- **Message d’erreur** : « Expiration du délai d’attente du test de connexion, réessayez ultérieurement. »
- **Cause potentielle** : Expiration du délai d’attente du test de connectivité.
- **Recommandation** : Réessayez le test de connexion ultérieurement. Si la nouvelle tentative échoue, contactez l’équipe du support Azure Data Factory.

## <a name="error-code-networkinstable"></a>Code d’erreur : NetworkInstable

- **Message d’erreur** : « Le test de connexion a réussi de façon irrégulière en raison d’une instabilité du réseau. »
- **Cause potentielle** : Problème réseau temporaire.
- **Recommandation** : Vérifiez si le réseau du serveur ou du pare-feu est stable.

## <a name="next-steps"></a>Étapes suivantes

- [Migrer les travaux SSIS avec SSMS](how-to-migrate-ssis-job-ssms.md)
- [Exécuter des packages SSIS dans Azure avec SSDT](how-to-invoke-ssis-package-ssdt.md)
- [Planifier des packages SSIS dans Azure](how-to-schedule-azure-ssis-integration-runtime.md)
