---
title: Utiliser l’activité Exécuter le package SSIS à partir du portail
description: Cet article décrit comment exécuter un package SQL Server Integration Services (SSIS) dans un pipeline Azure Data Factory à l’aide de l’activité Exécuter le package SSIS et du portail Azure Data Factory Studio.
ms.service: data-factory
ms.subservice: integration-services
ms.devlang: powershell
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.date: 10/22/2021
ms.openlocfilehash: ef334cf16502e6c2bd39193dbd377758a90ddb24
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131842432"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-the-azure-data-factory-studio-portal"></a>Exécuter un package SSIS avec l’activité Exécuter le package SSIS dans le portail Azure Data Factory Studio

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Cet article décrit comment exécuter un package SQL Server Integration Services (SSIS) dans un pipeline Azure Data Factory à l’aide de l’activité Exécuter le package SSIS et de l’interface utilisateur du portail Azure Data Factory Studio. 

## <a name="prerequisites"></a>Prérequis

Créez un runtime d’intégration Azure-SSIS si vous n’en avez pas encore en suivant les instructions pas à pas fournies dans le [Tutoriel : Approvisionnement de votre IR Azure-SSIS](./tutorial-deploy-ssis-packages-azure.md).

## <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Créer un pipeline avec une activité Exécuter le Package SSIS
Lors de cette étape, vous utilisez l’interface utilisateur ou l’application Data Factory pour créer un pipeline. Vous ajoutez une activité Exécuter le Package SSIS au pipeline et la configurez pour exécuter votre package SSIS. 

1. Sur votre vue d’ensemble Data Factory ou sur la page d’accueil du portail Azure, sélectionnez la vignette **Créer et surveiller** pour démarrer l’application ou l’interface utilisateur Data Factory dans un onglet séparé. 

   :::image type="content" source="./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png" alt-text="Page d’accueil Data Factory":::

   Dans la page d’accueil, sélectionnez **Orchestrer**. 

   :::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="Capture d’écran montrant la page d’accueil ADF.":::

1. Dans la boîte à outils **Activités**, étendez **Général**. Ensuitez, faites glisser une activité **Execute SSIS Package** jusqu’à l’aire du concepteur de pipeline. 

   :::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png" alt-text="Faire glisser une activité Execute SSIS Package jusqu’à l’aire du concepteur"::: 

   Sélectionnez l'objet d'activité Exécuter le package SSIS pour configurer les onglets **Général**, **Paramètres**, **Paramètres SSIS**, **Gestionnaires de connexions** et **Substitutions de propriété** correspondants.

### <a name="general-tab"></a>Onglet Général

Sous l'onglet **Général** de l'activité Exécuter le package SSIS, procédez comme suit.

:::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png" alt-text="Définissez les propriétés sous l’onglet Général":::

   1. Dans le champ **Nom**, entrez le nom de votre activité Exécuter le package SSIS.

   1. Dans le champ **Description**, entrez la description de votre activité Exécuter le package SSIS.

   1. Dans le champ **Délai d'expiration**, entrez la durée maximale pendant laquelle votre activité Exécuter le package SSIS peut être exécutée. La valeur par défaut est de 7 jours, et le format est J.HH:MM:SS.

   1. Dans le champ **Nouvelle tentative**, entrez le nombre maximal de tentatives d'exécution de votre activité Exécuter le package SSIS.

   1. Dans le champ **Intervalle avant nouvelle tentative**, entrez le nombre de secondes qui doivent s'écouler entre chaque tentative d'exécution de votre activité Exécuter le package SSIS. La valeur par défaut est de 30 secondes.

   1. Cochez la case **Sortie sécurisée** pour déterminer si vous souhaitez exclure la sortie de votre activité Exécuter le package SSIS de la journalisation.

   1. Cochez la case **Entrée sécurisée** pour déterminer si vous souhaitez exclure l'entrée de votre activité Exécuter le package SSIS de la journalisation.

### <a name="settings-tab"></a>Onglet Paramètres

Sous l'onglet **Paramètres** de l'activité Exécuter le package SSIS, procédez comme suit.

:::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png" alt-text="Définir les propriétés sous l’onglet Paramètres - Automatique":::

   1. Dans le champ **Azure-SSIS IR**, sélectionnez le runtime d'intégration Azure-SSIS désigné pour lancer votre activité Exécuter le package SSIS.

   1. Dans le champ **Description**, entrez la description de votre activité Exécuter le package SSIS.

   1. Cochez la case **Authentification Windows** afin de déterminer si vous souhaitez utiliser l'authentification Windows pour accéder aux magasins de données, comme les serveurs SQL/partages de fichiers locaux ou Azure Files.
   
      Si vous cochez cette case, entrez les valeurs des informations d'identification requises pour l'exécution de votre package dans les champs **Domaine**, **Nom d'utilisateur** et **Mot de passe**. Par exemple, pour accéder à Azure Files, le domaine, le nom d'utilisateur et le mot de passe sont respectivement `Azure`, `<storage account name>` et `<storage account key>`.

      Vous pouvez également utiliser les secrets stockés dans votre coffre de clés Azure Key Vault comme valeurs. Pour ce faire, cochez la case **AZURE KEY VAULT** en regard de ceux-ci. Sélectionnez ou modifiez votre service de coffre de clés lié existant ou créez-en un. Sélectionnez ensuite la version et le nom du secret correspondant à votre valeur. Lorsque vous créez ou modifiez votre service de coffre de clés lié, vous pouvez sélectionner ou modifier votre coffre de clés existant ou en créer un nouveau. Veillez à accorder à Data Factory l’accès géré à votre coffre de clés si vous ne l’avez pas déjà fait. Vous pouvez également entrer votre secret directement au format suivant : `<key vault linked service name>/<secret name>/<secret version>`.
      
   1. Cochez la case **Runtime 32 bits** pour déterminer si votre package a besoin d'un runtime 32 bits pour s'exécuter.

   1. Dans le champ **Emplacement du package**, sélectionnez **SSISDB**, **Système de fichiers (Package)** , **Système de fichiers (Projet)** , **Package incorporé** ou **Magasin de packages**. 

#### <a name="package-location-ssisdb"></a>Emplacement du package : SSISDB

**SSISDB** est automatiquement sélectionné comme emplacement de votre package si votre runtime d'intégration Azure-SSIS a été approvisionné avec un catalogue SSIS (SSISDB) hébergé par un serveur Azure SQL Database/Managed Instance. Vous pouvez également le sélectionner vous-même. S'il est sélectionné, procédez comme suit.

   1. Si votre runtime d'intégration Azure-SSIS est en cours d'exécution et que la case **Entrées manuelles** n'est pas cochée, vous pouvez parcourir et sélectionner vos dossiers, projets, packages et environnements existants dans SSISDB. Sélectionnez **Actualiser** pour récupérer vos nouveaux dossiers, projets, packages ou environnements ajoutés dans SSISDB afin qu'ils soient sélectionnables lors de la navigation. Pour parcourir et sélectionner les environnements à des fins d'exécution de vos packages, vous devez configurer vos projets au préalable de manière à ajouter ces environnements en tant que références à partir des mêmes dossiers sous SSISDB. Pour plus d’informations, consultez [Créer et mapper des environnements SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages).

   1. Concernant **Niveau de journalisation**, sélectionnez un périmètre prédéfini de journalisation pour l’exécution de votre package. Sélectionnez la case **Personnalisé** si vous préférez entrer un nom de journalisation personnalisé. 

   1. Si votre Azure-SSIS IR n’est pas en cours d’exécution ou que la case **Entrées manuelles** est sélectionnée, entrez les chemins de votre package et de votre environnement directement à partir de SSISDB aux formats suivants : `<folder name>/<project name>/<package name>.dtsx` et `<folder name>/<environment name>`.

      :::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png" alt-text="Définir les propriétés sous l’onglet Paramètres - Manuel":::

#### <a name="package-location-file-system-package"></a>Emplacement du package : Système de fichiers (Package)

**Système de fichiers (Package)** est automatiquement sélectionné comme emplacement de votre package si votre runtime d'intégration Azure-SSIS a été approvisionné sans SSISDB. Vous pouvez également le sélectionner vous-même. S'il est sélectionné, procédez comme suit.

:::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png" alt-text="Définir les propriétés sous l'onglet Paramètres - Système de fichiers (Package)":::
   
   1. Désignez le package à exécuter en fournissant un chemin d'accès UNC (Universal Naming Convention) à votre fichier de package (avec `.dtsx`) dans le champ **Chemin d'accès du package**. Vous pouvez accéder à votre package en sélectionnant **Parcourir le stockage de fichiers** ou entrer manuellement le chemin d'accès de celui-ci. Par exemple, si vous stockez votre package dans Azure Files, son chemin d'accès est `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`. 
   
   1. Si vous configurez votre package dans un fichier distinct, vous devez également fournir le chemin d'accès UNC de votre fichier de configuration (avec `.dtsConfig`) dans le champ **Chemin d'accès de la configuration**. Vous pouvez accéder à votre configuration en sélectionnant **Parcourir le stockage de fichiers** ou entrer manuellement le chemin d'accès de celle-ci. Par exemple, si vous stockez votre configuration dans Azure Files, son chemin d'accès est `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`.

   1. Entrez les informations d'identification requises pour accéder aux fichiers de votre package et de votre configuration. Si vous avez précédemment entré les valeurs des informations d'identification requises pour l'exécution de votre package (**authentification Windows**), vous pouvez les réutiliser en cochant la case **Identiques aux informations d'identification requises pour l'exécution du package**. Dans le cas contraire, entrez les valeurs pour vos informations d’identification pour l’accès au package dans les zones **Domaine**, **Nom d'utilisateur** et **Mot de passe**. Par exemple, si vous stockez votre package et votre configuration dans Azure Files, le domaine, le nom d'utilisateur et le mot de passe sont respectivement `Azure`, `<storage account name>` et `<storage account key>`. 

      Vous pouvez également utiliser les secrets stockés dans votre coffre de clés Azure Key Vault comme valeurs. Pour ce faire, cochez la case **AZURE KEY VAULT** en regard de ceux-ci. Sélectionnez ou modifiez votre service de coffre de clés lié existant ou créez-en un. Sélectionnez ensuite la version et le nom du secret correspondant à votre valeur. Lorsque vous créez ou modifiez votre service de coffre de clés lié, vous pouvez sélectionner ou modifier votre coffre de clés existant ou en créer un nouveau. Veillez à accorder à Data Factory l’accès géré à votre coffre de clés si vous ne l’avez pas déjà fait. Vous pouvez également entrer votre secret directement au format suivant : `<key vault linked service name>/<secret name>/<secret version>`. 

      Ces informations d'identification sont également utilisées pour accéder aux packages enfants de la Tâche d'exécution de package qui sont référencés par leur propre chemin d'accès et d'autres configurations spécifiées dans vos packages. 

   1. Si vous avez utilisé le niveau de protection **EncryptAllWithPassword** ou **EncryptSensitiveWithPassword** lorsque vous avez créé votre package via SQL Server Data Tools (SSDT), entrez la valeur de votre mot de passe dans le champ **Mot de passe de chiffrement**. Vous pouvez également utiliser un secret stocké dans votre coffre de clés Azure Key Vault comme valeur (voir précédemment).
      
      Si vous avez utilisé le niveau de protection **EncryptSensitiveWithUserKey**, entrez de nouveau vos valeurs sensibles dans les fichiers de configuration ou dans les onglets **Paramètres SSIS**, **Gestionnaires de connexions** ou **Substitutions de propriété** (voir ci-dessous).
      
      Si vous avez utilisé le niveau de protection **EncryptAllWithUserKey** , cela n’est pas pris en charge. Vous devez reconfigurer votre package pour utiliser un autre niveau de protection via SSDT ou l'utilitaire de ligne de commande `dtutil`. 

   1. Concernant **Niveau de journalisation**, sélectionnez un périmètre prédéfini de journalisation pour l’exécution de votre package. Sélectionnez la case **Personnalisé** si vous préférez entrer un nom de journalisation personnalisé. 
   
   1. Si vous souhaitez journaliser vos exécutions de package au-delà à l’aide de modules fournisseurs d’informations, spécifiez votre dossier de journaux en fournissant son chemin d’accès UNC dans la zone **Chemin d’accès de journalisation**. Vous pouvez accéder à votre dossier journal en sélectionnant **Parcourir le stockage de fichiers** ou entrer manuellement le chemin d'accès de celui-ci. Par exemple, si vous stockez vos journaux dans Azure Files, votre chemin d’accès de journalisation est `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`. Un sous-dossier est créé à cet emplacement pour l'exécution de chaque package, et nommé d'après l'ID d'exécution de l'activité Exécuter le package SSIS. Des fichiers journaux y sont générés toutes les cinq minutes. 
   
   1. Entrez les informations d'identification pour accéder à votre dossier journal. Si vous avez précédemment entré les valeurs des informations d'identification requises pour l'accès au package (voir précédemment), vous pouvez les réutiliser en cochant la case **Identiques aux informations d'identification requises pour l'accès au package**. Dans le cas contraire, entrez les valeurs pour vos informations d’identification pour l’accès aux journaux dans les zones **Domaine**, **Nom d'utilisateur** et **Mot de passe**. Par exemple, si vous stockez vos journaux dans Azure Files, le domaine est `Azure` ; le nom d’utilisateur est `<storage account name>` ; et le mot de passe est `<storage account key>`. Vous pouvez également utiliser les secrets stockés dans votre coffre de clés Azure Key Vault comme valeurs (voir précédemment).
   
Pour tous les chemins d’accès UNC mentionnés précédemment, le nom de fichier complet doit être inférieur à 260 caractères. Le nom de répertoire ne doit pas dépasser 248 caractères.

#### <a name="package-location-file-system-project"></a>Emplacement du package : Système de fichiers (Projet)

Si vous sélectionnez **Système de fichiers (Projet)** comme emplacement de votre package, procédez comme suit.

:::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png" alt-text="Définir les propriétés sous l'onglet Paramètres - Système de fichiers (Projet)":::

   1. Désignez le package à exécuter en fournissant un chemin d'accès UNC à votre fichier de projet (avec `.ispac`) dans le champ **Chemin d'accès du projet** et un fichier de package (avec `.dtsx`) de votre projet dans le champ **Nom du package**. Vous pouvez accéder à votre projet en sélectionnant **Parcourir le stockage de fichiers** ou entrer manuellement le chemin d'accès de celui-ci. Par exemple, si vous stockez votre projet dans Azure Files, son chemin d'accès est `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`.

   1. Entrez les informations d'identification requises pour accéder aux fichiers de votre projet et de votre package. Si vous avez précédemment entré les valeurs des informations d'identification requises pour l'exécution de votre package (**authentification Windows**), vous pouvez les réutiliser en cochant la case **Identiques aux informations d'identification requises pour l'exécution du package**. Dans le cas contraire, entrez les valeurs pour vos informations d’identification pour l’accès au package dans les zones **Domaine**, **Nom d'utilisateur** et **Mot de passe**. Par exemple, si vous stockez votre projet et votre package dans Azure Files, le domaine, le nom d'utilisateur et le mot de passe sont respectivement `Azure`, `<storage account name>` et `<storage account key>`. 

      Vous pouvez également utiliser les secrets stockés dans votre coffre de clés Azure Key Vault comme valeurs. Pour ce faire, cochez la case **AZURE KEY VAULT** en regard de ceux-ci. Sélectionnez ou modifiez votre service de coffre de clés lié existant ou créez-en un. Sélectionnez ensuite la version et le nom du secret correspondant à votre valeur. Lorsque vous créez ou modifiez votre service de coffre de clés lié, vous pouvez sélectionner ou modifier votre coffre de clés existant ou en créer un nouveau. Veillez à accorder à Data Factory l’accès géré à votre coffre de clés si vous ne l’avez pas déjà fait. Vous pouvez également entrer votre secret directement au format suivant : `<key vault linked service name>/<secret name>/<secret version>`. 

      Ces informations d'identification sont également utilisées pour accéder aux packages enfants de la Tâche d'exécution de package qui sont référencés à partir du même projet. 

   1. Si vous avez utilisé le niveau de protection **EncryptAllWithPassword** ou **EncryptSensitiveWithPassword** lorsque vous avez créé votre package via SSDT, entrez la valeur de votre mot de passe dans le champ **Mot de passe de chiffrement**. Vous pouvez également utiliser un secret stocké dans votre coffre de clés Azure Key Vault comme valeur (voir précédemment).
      
      Si vous avez utilisé le niveau de protection **EncryptSensitiveWithUserKey**, entrez de nouveau vos valeurs sensibles dans les onglets **Paramètres SSIS**, **Gestionnaires de connexions** ou **Substitutions de propriété** (voir ci-dessous).
      
      Si vous avez utilisé le niveau de protection **EncryptAllWithUserKey** , cela n’est pas pris en charge. Vous devez reconfigurer votre package pour utiliser un autre niveau de protection via SSDT ou l'utilitaire de ligne de commande `dtutil`. 

   1. Concernant **Niveau de journalisation**, sélectionnez un périmètre prédéfini de journalisation pour l’exécution de votre package. Sélectionnez la case **Personnalisé** si vous préférez entrer un nom de journalisation personnalisé. 
   
   1. Si vous souhaitez journaliser vos exécutions de package au-delà à l’aide de modules fournisseurs d’informations, spécifiez votre dossier de journaux en fournissant son chemin d’accès UNC dans la zone **Chemin d’accès de journalisation**. Vous pouvez accéder à votre dossier journal en sélectionnant **Parcourir le stockage de fichiers** ou entrer manuellement le chemin d'accès de celui-ci. Par exemple, si vous stockez vos journaux dans Azure Files, votre chemin d’accès de journalisation est `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`. Un sous-dossier est créé à cet emplacement pour l'exécution de chaque package, et nommé d'après l'ID d'exécution de l'activité Exécuter le package SSIS. Des fichiers journaux y sont générés toutes les cinq minutes. 
   
   1. Entrez les informations d'identification pour accéder à votre dossier journal. Si vous avez précédemment entré les valeurs des informations d'identification requises pour l'accès au package (voir précédemment), vous pouvez les réutiliser en cochant la case **Identiques aux informations d'identification requises pour l'accès au package**. Dans le cas contraire, entrez les valeurs pour vos informations d’identification pour l’accès aux journaux dans les zones **Domaine**, **Nom d'utilisateur** et **Mot de passe**. Par exemple, si vous stockez vos journaux dans Azure Files, le domaine est `Azure` ; le nom d’utilisateur est `<storage account name>` ; et le mot de passe est `<storage account key>`. Vous pouvez également utiliser les secrets stockés dans votre coffre de clés Azure Key Vault comme valeurs (voir précédemment).
   
Pour tous les chemins d’accès UNC mentionnés précédemment, le nom de fichier complet doit être inférieur à 260 caractères. Le nom de répertoire ne doit pas dépasser 248 caractères.

#### <a name="package-location-embedded-package"></a>Emplacement du package : Package incorporé

Si vous sélectionnez **Package incorporé** comme emplacement de votre package, procédez comme suit.

:::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings5.png" alt-text="Définir les propriétés sous l'onglet Paramètres - Package incorporé":::

   1. Glissez-déplacez le fichier de votre package (avec `.dtsx`) ou **chargez-le** dans la zone prévue à cet effet depuis un dossier de fichiers. Votre package sera automatiquement compressé et incorporé à la charge utile de l'activité. Une fois votre package incorporé, vous pouvez le **télécharger** ultérieurement afin de le modifier. Vous pouvez également **paramétrer** votre package incorporé en l'assignant à un paramètre pipeline qui peut être utilisé dans plusieurs activités, optimisant ainsi la taille de votre charge utile de pipeline. L’incorporation de Fichiers projet (avec `.ispac`) n’est pas prise en charge pour le moment. Vous ne pouvez donc pas utiliser de paramètres SSIS ni gestionnaires de connexions avec une étendue au niveau du projet dans vos packages incorporés.
   
   1. Si votre package incorporé n’est pas entièrement chiffré et que nous détectons l’utilisation de Tâche d’exécution de package, la case **Tâche d’exécution de package**  sera automatiquement cochée et vos packages enfants référencés par leur chemin d’accès au système de fichiers seront automatiquement ajoutés pour que vous puissiez également les incorporer.
   
      Si nous ne parvenons pas à détecter l’utilisation de Tâche d’exécution de package, vous devez sélectionner manuellement la case à cocher **Tâche d’exécution de package** et ajouter un par un les packages enfants qui sont référencés par leur chemin d’accès au système de fichiers afin de les incorporer. Si vos packages enfants sont stockés dans une base de données SQL Server (MSDB), vous ne pouvez pas les incorporer. Vous devez donc vous assurer que votre runtime d’intégration Azure-SSIS peut accéder à la MSDB pour les récupérer à l’aide de leurs références SQL Server. L’incorporation de Fichiers projet (avec `.ispac`) n’est pas prise en charge pour le moment. Vous ne pouvez donc pas utiliser de références de projet pour vos packages enfants.
   
   1. Si vous avez utilisé le niveau de protection **EncryptAllWithPassword** ou **EncryptSensitiveWithPassword** lorsque vous avez créé votre package via SSDT, entrez la valeur de votre mot de passe dans le champ **Mot de passe de chiffrement**. 
   
      Vous pouvez également utiliser un secret stocké dans votre coffre de clés Azure Key Vault comme valeur. Pour ce faire, cochez la case **AZURE KEY VAULT** en regard de celui-ci. Sélectionnez ou modifiez votre service de coffre de clés lié existant ou créez-en un. Sélectionnez ensuite la version et le nom du secret correspondant à votre valeur. Lorsque vous créez ou modifiez votre service de coffre de clés lié, vous pouvez sélectionner ou modifier votre coffre de clés existant ou en créer un nouveau. Veillez à accorder à Data Factory l’accès géré à votre coffre de clés si vous ne l’avez pas déjà fait. Vous pouvez également entrer votre secret directement au format suivant : `<key vault linked service name>/<secret name>/<secret version>`.
      
      Si vous avez utilisé le niveau de protection **EncryptSensitiveWithUserKey**, entrez de nouveau vos valeurs sensibles dans les fichiers de configuration ou dans les onglets **Paramètres SSIS**, **Gestionnaires de connexions** ou **Substitutions de propriété** (voir ci-dessous).
      
      Si vous avez utilisé le niveau de protection **EncryptAllWithUserKey** , cela n’est pas pris en charge. Vous devez reconfigurer votre package pour utiliser un autre niveau de protection via SSDT ou l'utilitaire de ligne de commande `dtutil`.

   1. Concernant **Niveau de journalisation**, sélectionnez un périmètre prédéfini de journalisation pour l’exécution de votre package. Sélectionnez la case **Personnalisé** si vous préférez entrer un nom de journalisation personnalisé. 
   
   1. Si vous souhaitez journaliser vos exécutions de package au-delà à l’aide de modules fournisseurs d’informations, spécifiez votre dossier de journaux en fournissant son chemin d’accès UNC dans la zone **Chemin d’accès de journalisation**. Vous pouvez accéder à votre dossier journal en sélectionnant **Parcourir le stockage de fichiers** ou entrer manuellement le chemin d'accès de celui-ci. Par exemple, si vous stockez vos journaux dans Azure Files, votre chemin d’accès de journalisation est `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`. Un sous-dossier est créé à cet emplacement pour l'exécution de chaque package, et nommé d'après l'ID d'exécution de l'activité Exécuter le package SSIS. Des fichiers journaux y sont générés toutes les cinq minutes. 
   
   1. Spécifiez les informations d'identification requises pour accéder à votre dossier journal en entrant les valeurs correspondantes dans les champs **Domaine**, **Nom d'utilisateur** et **Mot de passe**. Par exemple, si vous stockez vos journaux dans Azure Files, le domaine est `Azure` ; le nom d’utilisateur est `<storage account name>` ; et le mot de passe est `<storage account key>`. Vous pouvez également utiliser les secrets stockés dans votre coffre de clés Azure Key Vault comme valeurs (voir précédemment).
   
Pour tous les chemins d’accès UNC mentionnés précédemment, le nom de fichier complet doit être inférieur à 260 caractères. Le nom de répertoire ne doit pas dépasser 248 caractères.

#### <a name="package-location-package-store"></a>Emplacement du package : Magasin de packages

Si vous sélectionnez **Magasin de packages** comme emplacement de votre package, procédez comme suit.

:::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings6.png" alt-text="Définir les propriétés sous l'onglet Paramètres - Magasin de packages":::
   
   1. Dans le champ **Nom du magasin de packages**, sélectionnez un magasin de packages existant associé à votre runtime d'intégration Azure-SSIS.

   1. Désignez le package à exécuter en fournissant son chemin d'accès (sans `.dtsx`) depuis le magasin de packages sélectionné dans le champ **Chemin d'accès du package**. Si le magasin de packages sélectionné se trouve au sommet du système de fichiers/Azure Files, vous pouvez accéder à votre package en sélectionnant **Parcourir le stockage de fichiers**. Vous pouvez également entrer son chemin au format `<folder name>\<package name>`. Vous avez aussi la possibilité d'importer de nouveaux packages dans le magasin de packages sélectionné via SQL Server Management Studio (SSMS), comme pour le [magasin de packages SSIS hérité](/sql/integration-services/service/package-management-ssis-service). Pour plus d'informations, consultez [Gérer les packages SSIS avec des magasins de packages Azure-SSIS IR](./azure-ssis-integration-runtime-package-store.md).

   1. Si vous configurez votre package dans un fichier distinct, vous devez fournir le chemin d'accès UNC de votre fichier de configuration (avec `.dtsConfig`) dans le champ **Chemin d'accès de la configuration**. Vous pouvez accéder à votre configuration en sélectionnant **Parcourir le stockage de fichiers** ou entrer manuellement le chemin d'accès de celle-ci. Par exemple, si vous stockez votre configuration dans Azure Files, son chemin d'accès est `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`.

   1. Cochez la case **Informations d'identification requises pour l'accès à la configuration** afin de déterminer si des informations d'identification sont nécessaires pour accéder à votre fichier de configuration séparément. Cette opération est nécessaire lorsque le magasin de packages sélectionné se trouve au sommet de la base de données SQL Server (MSDB) hébergée par Azure SQL Managed Instance ou si votre fichier de configuration n'y est pas stocké.
   
      Si vous avez précédemment entré les valeurs des informations d'identification requises pour l'exécution de votre package (**authentification Windows**), vous pouvez les réutiliser en cochant la case **Identiques aux informations d'identification requises pour l'exécution du package**. Si ce n'est pas le cas, entrez les valeurs des informations d'identification requises pour l'accès à la configuration dans les champs **Domaine**, **Nom d'utilisateur** et **Mot de passe**. Par exemple, si vous stockez votre configuration dans Azure Files, le domaine, le nom d'utilisateur et le mot de passe sont respectivement `Azure`, `<storage account name>` et `<storage account key>`. 

      Vous pouvez également utiliser les secrets stockés dans votre coffre de clés Azure Key Vault comme valeurs. Pour ce faire, cochez la case **AZURE KEY VAULT** en regard de ceux-ci. Sélectionnez ou modifiez votre service de coffre de clés lié existant ou créez-en un. Sélectionnez ensuite la version et le nom du secret correspondant à votre valeur. Lorsque vous créez ou modifiez votre service de coffre de clés lié, vous pouvez sélectionner ou modifier votre coffre de clés existant ou en créer un nouveau. Veillez à accorder à Data Factory l’accès géré à votre coffre de clés si vous ne l’avez pas déjà fait. Vous pouvez également entrer votre secret directement au format suivant : `<key vault linked service name>/<secret name>/<secret version>`.

   1. Si vous avez utilisé le niveau de protection **EncryptAllWithPassword** ou **EncryptSensitiveWithPassword** lorsque vous avez créé votre package via SSDT, entrez la valeur de votre mot de passe dans le champ **Mot de passe de chiffrement**. Vous pouvez également utiliser un secret stocké dans votre coffre de clés Azure Key Vault comme valeur (voir précédemment).
      
      Si vous avez utilisé le niveau de protection **EncryptSensitiveWithUserKey**, entrez de nouveau vos valeurs sensibles dans les fichiers de configuration ou dans les onglets **Paramètres SSIS**, **Gestionnaires de connexions** ou **Substitutions de propriété** (voir ci-dessous).
      
      Si vous avez utilisé le niveau de protection **EncryptAllWithUserKey** , cela n’est pas pris en charge. Vous devez reconfigurer votre package pour utiliser un autre niveau de protection via SSDT ou l'utilitaire de ligne de commande `dtutil`. 

   1. Concernant **Niveau de journalisation**, sélectionnez un périmètre prédéfini de journalisation pour l’exécution de votre package. Sélectionnez la case **Personnalisé** si vous préférez entrer un nom de journalisation personnalisé. 
   
   1. Si vous souhaitez journaliser vos exécutions de package au-delà à l’aide de modules fournisseurs d’informations, spécifiez votre dossier de journaux en fournissant son chemin d’accès UNC dans la zone **Chemin d’accès de journalisation**. Vous pouvez accéder à votre dossier journal en sélectionnant **Parcourir le stockage de fichiers** ou entrer manuellement le chemin d'accès de celui-ci. Par exemple, si vous stockez vos journaux dans Azure Files, votre chemin d’accès de journalisation est `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`. Un sous-dossier est créé à cet emplacement pour l'exécution de chaque package, et nommé d'après l'ID d'exécution de l'activité Exécuter le package SSIS. Des fichiers journaux y sont générés toutes les cinq minutes. 
   
   1. Spécifiez les informations d'identification requises pour accéder à votre dossier journal en entrant les valeurs correspondantes dans les champs **Domaine**, **Nom d'utilisateur** et **Mot de passe**. Par exemple, si vous stockez vos journaux dans Azure Files, le domaine est `Azure` ; le nom d’utilisateur est `<storage account name>` ; et le mot de passe est `<storage account key>`. Vous pouvez également utiliser les secrets stockés dans votre coffre de clés Azure Key Vault comme valeurs (voir précédemment).
   
Pour tous les chemins d’accès UNC mentionnés précédemment, le nom de fichier complet doit être inférieur à 260 caractères. Le nom de répertoire ne doit pas dépasser 248 caractères.

### <a name="ssis-parameters-tab"></a>Onglet Paramètres SSIS

Sous l'onglet **Paramètres SSIS** de l'activité Exécuter le package SSIS, procédez comme suit.

:::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png" alt-text="Définir les propriétés sous l’onglet Paramètres SSIS":::

   1. Si votre runtime d'intégration Azure-SSIS est en cours d'exécution, **SSISDB** est sélectionné comme emplacement de votre package, et si la case **Entrées manuelles** de l'onglet **Paramètres** n'est pas cochée, les paramètres SSIS existants du projet et du package sélectionnés à partir de SSISDB apparaissent pour vous permettre de leur attribuer des valeurs. Sinon, vous pouvez affecter manuellement des valeurs une par une. Assurez-vous que les valeurs existent et qu’elles sont correctement entrées pour que l’exécution du package réussisse. 
   
   1. Si vous avez utilisé le niveau de protection **EncryptSensitiveWithUserKey** lorsque vous avez créé votre package via SSDT et que **Système de fichiers (Package)** , **Système de fichiers (Projet)** , **Package incorporé** ou **Magasin de packages** est sélectionné comme emplacement de votre package, vous devez également réentrer vos paramètres sensibles pour leur attribuer des valeurs dans cet onglet. 
   
Lorsque vous attribuez des valeurs à vos paramètres, vous pouvez ajouter du contenu dynamique à l’aide d’expressions, de fonctions, de variables système Data Factory et de paramètres ou variables de pipeline Data Factory.

Vous pouvez également utiliser les secrets stockés dans votre coffre de clés Azure Key Vault comme valeurs. Pour ce faire, cochez la case **AZURE KEY VAULT** en regard de ceux-ci. Sélectionnez ou modifiez votre service de coffre de clés lié existant ou créez-en un. Sélectionnez ensuite la version et le nom du secret correspondant à votre valeur. Lorsque vous créez ou modifiez votre service de coffre de clés lié, vous pouvez sélectionner ou modifier votre coffre de clés existant ou en créer un nouveau. Veillez à accorder à Data Factory l’accès géré à votre coffre de clés si vous ne l’avez pas déjà fait. Vous pouvez également entrer votre secret directement au format suivant : `<key vault linked service name>/<secret name>/<secret version>`. 

### <a name="connection-managers-tab"></a>Onglet Connection Managers (Gestionnaires de connexions)

Sous l'onglet **Gestionnaires de connexions** de l'activité Exécuter le package SSIS, procédez comme suit.

:::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png" alt-text="Définir les propriétés sous l’onglet Gestionnaires de connexions":::

   1. Si votre runtime d'intégration Azure-SSIS est en cours d'exécution, **SSISDB** est sélectionné comme emplacement de votre package, et si la case **Entrées manuelles** de l'onglet **Paramètres** n'est pas cochée, les gestionnaires de connexions existants du projet et du package sélectionnés à partir de SSISDB apparaissent pour vous permettre d'attribuer des valeurs à leurs propriétés. Sinon, vous pouvez affecter manuellement des valeurs aux propriétés une par une. Assurez-vous que les valeurs existent et qu’elles sont correctement entrées pour que l’exécution du package réussisse. 
   
      Vous pouvez obtenir les noms **ÉTENDUE**, **NOM** et **PROPRIÉTÉ** corrects pour tout gestionnaire de connexions en ouvrant le package qui le contient sur SSDT. Une fois le package ouvert, sélectionnez le gestionnaire de connexions approprié pour afficher les noms et les valeurs de toutes ses propriétés dans la fenêtre **Propriétés** de SSDT. Grâce à ces informations, vous pouvez remplacer les valeurs de toutes les propriétés du gestionnaire de connexions à l’exécution. 

      :::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssdt-connection-manager-properties.png" alt-text="Obtenir les propriétés du gestionnaire de connexions sur SSDT":::

      Par exemple, sans modifier votre package d’origine sur SSDT, vous pouvez convertir ses flux de données local vers local s’exécutant sur SQL Server en flux de données local vers cloud s’exécutant sur un runtime d’intégration SSIS dans ADF en remplaçant les valeurs des propriétés **ConnectByProxy**, **ConnectionString** et **ConnectUsingManagedIdentity** dans les gestionnaires de connexions existants à l’exécution.
      
      Ces remplacements à l’exécution peuvent activer le runtime d’intégration auto-hébergé (SHIR) comme proxy pour le runtime d’intégration SSIS lors de l’accès aux données localement (voir [Configurer un runtime d’intégration auto-hébergé en tant que proxy pour le runtime d’intégration SSIS](./self-hosted-integration-runtime-proxy-ssis.md)) et aux connexions Azure SQL Database/Managed Instance à l’aide du dernier pilote MSOLEDBSQL qui, à son tour, active l’authentification Azure Active Directory (AAD) avec l’identité managée ADF (voir [Configurer l’authentification AAD avec une identité managée ADF pour les connexions OLEDB](/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)).

      :::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers2.png" alt-text="Définir les propriétés à partir de SSDT sous l’onglet Gestionnaires de connexions":::
   
   1. Si vous avez utilisé le niveau de protection **EncryptSensitiveWithUserKey** lorsque vous avez créé votre package via SSDT et que **Système de fichiers (Package)** , **Système de fichiers (Projet)** , **Package incorporé** ou **Magasin de packages** est sélectionné comme emplacement de votre package, vous devez également réentrer les propriétés sensibles de vos gestionnaires de connexions pour leur attribuer des valeurs dans cet onglet. 

Lorsque vous attribuez des valeurs à vos propriétés de gestionnaire de connexions, vous pouvez ajouter du contenu dynamique à l’aide d’expressions, de fonctions, de variables système Data Factory et de paramètres ou variables de pipeline Data Factory. 

Vous pouvez également utiliser les secrets stockés dans votre coffre de clés Azure Key Vault comme valeurs. Pour ce faire, cochez la case **AZURE KEY VAULT** en regard de ceux-ci. Sélectionnez ou modifiez votre service de coffre de clés lié existant ou créez-en un. Sélectionnez ensuite la version et le nom du secret correspondant à votre valeur. Lorsque vous créez ou modifiez votre service de coffre de clés lié, vous pouvez sélectionner ou modifier votre coffre de clés existant ou en créer un nouveau. Veillez à accorder à Data Factory l’accès géré à votre coffre de clés si vous ne l’avez pas déjà fait. Vous pouvez également entrer votre secret directement au format suivant : `<key vault linked service name>/<secret name>/<secret version>`. 

### <a name="property-overrides-tab"></a>Onglet Substitutions de propriété

Sous l'onglet **Substitutions de propriété** de l'activité Exécuter le package SSIS, procédez comme suit.

:::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png" alt-text="Définir les propriétés sous l’onglet Substitutions de propriété":::

   1. Entrez un par un les chemins d'accès des propriétés existantes dans le package sélectionné pour leur attribuer manuellement des valeurs. Assurez-vous que les valeurs existent et qu’elles sont correctement entrées pour que l’exécution du package réussisse. Par exemple, pour remplacer la valeur de votre variable utilisateur, entrez son chemin d’accès au format suivant : `\Package.Variables[User::<variable name>].Value`. 

      Vous pouvez obtenir le **CHEMIN D’ACCÈS À LA PROPRIÉTÉ** correct pour toute propriété de package en ouvrant le package qui le contient sur SSDT. Une fois le package ouvert, sélectionnez son flux de contrôle et sa propriété **Configurations** dans la fenêtre **Propriétés** de SSDT. Sélectionnez ensuite le bouton de sélection ( **…** ) à côté de sa propriété **Configurations** pour ouvrir la **bibliothèque des configurations du package** qui est normalement utilisée pour [créer des configurations de package dans le modèle de déploiement de package](/sql/integration-services/packages/legacy-package-deployment-ssis#create-package-configurations). 

      :::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties.png" alt-text="Récupérer les propriétés de package à partir de SSDT – Propriété Configurations":::

      Dans la **bibliothèque des configurations du package**, cochez la case **Activer les configurations du package** et sélectionnez le bouton **Ajouter…** pour ouvrir l’**Assistant Configuration de package**. 
      
      Dans l’**Assistant Configuration du package**, sélectionnez l’élément **Fichier config XML** dans menu déroulant **Type de configuration** et le bouton **Spécifier directement les paramètres de configuration**, entrez le nom de votre fichier config, puis sélectionnez le bouton **Suivant >** . 

      :::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties2.png" alt-text="Récupérer les propriétés de package à partir de SSDT – Bibliothèque des configurations":::

      Enfin, sélectionnez les propriétés du package dont vous souhaitez obtenir le chemin d’accès et le bouton **Suivant >** .  Vous pouvez maintenant voir, copier et coller les chemins d’accès aux propriétés du package de votre choix et les enregistrer dans votre fichier config. Grâce à ces informations, vous pouvez remplacer les valeurs de toutes les propriétés du package à l’exécution. 

      :::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties3.png" alt-text="Récupérer les propriétés de package à partir de SSDT – Assistant Configuration":::
   
   1. Si vous avez utilisé le niveau de protection **EncryptSensitiveWithUserKey** lorsque vous avez créé votre package via SSDT et que **Système de fichiers (Package)** , **Système de fichiers (Projet)** , **Package incorporé** ou **Magasin de packages** est sélectionné comme emplacement de votre package, vous devez également réentrer les propriétés sensibles de votre package pour leur attribuer des valeurs dans cet onglet. 
   
Lorsque vous attribuez des valeurs à vos propriétés de package, vous pouvez ajouter du contenu dynamique à l'aide d'expressions, de fonctions, de variables système Data Factory et de paramètres ou variables de pipeline Data Factory.

Les valeurs attribuées dans les fichiers de configuration et dans l’onglet **Paramètres SSIS** peuvent être remplacées à l’aide des onglets **Gestionnaires de connexions** et **Substitutions de propriété**. Vous pouvez également remplacer les valeurs affectées dans l’onglet **Gestionnaires de connexions** dans l’onglet **Substitutions de propriété**.

Pour valider la configuration du pipeline, sélectionnez **Valider** dans la barre d’outils. Pour fermer le **Rapport de validation de pipeline**, sélectionnez **>>** .

Pour publier le pipeline sur Data Factory, sélectionnez **Publier tout**. 

## <a name="run-the-pipeline"></a>Exécuter le pipeline
À cette étape, vous déclenchez une exécution du pipeline. 

1. Pour déclencher une exécution de pipeline, sélectionnez **Déclencher** dans la barre d’outils, puis **Déclencher maintenant**. 

   :::image type="content" source="./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png" alt-text="Déclencher maintenant":::

2. Dans la fenêtre **Exécution du pipeline**, sélectionnez **Terminer**. 

## <a name="monitor-the-pipeline"></a>Surveiller le pipeline

1. Basculez vers l’onglet **Surveiller** sur la gauche. Vous voyez l’exécution de pipeline et son état, ainsi que d’autres informations (telles que l’heure de **Début d’exécution**). Sélectionnez **Actualiser** pour actualiser l’affichage.

   :::image type="content" source="./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png" alt-text="Exécutions de pipeline":::

2. Cliquez sur le lien **Afficher les exécutions d’activités** dans la colonne **Actions**. Une seule exécution d’activité est affichée, étant donné que le pipeline n’a qu’une seule activité. Il s’agit de l’activité Exécuter le package SSIS.

   :::image type="content" source="./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png" alt-text="Exécutions d’activités":::

3. Exécutez la requête suivante par rapport à la base de données SSISDB dans votre serveur SQL pour vérifier que le package s’est exécuté. 

   ```sql
   select * from catalog.executions
   ```

   :::image type="content" source="./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png" alt-text="Vérifier les exécutions de package":::

4. Vous pouvez également obtenir l’ID de l’exécution SSISDB à partir de la sortie de l’exécution de l’activité de pipeline, et utiliser l’ID pour consulter des journaux d’activité d’exécution et des messages d’erreur plus complets dans SQL Server Management Studio.

   :::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png" alt-text="Obtenez l’ID de l'exécution.":::

## <a name="schedule-the-pipeline-with-a-trigger"></a>Planifier le pipeline avec un déclencheur

Vous pouvez également créer un déclencheur planifié pour votre pipeline afin que le pipeline s’exécute selon une planification, par exemple horaire ou quotidienne. Pour obtenir un exemple, consultez [Créer une fabrique de données - Interface utilisateur de Data Factory](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="next-steps"></a>Étapes suivantes
- [Exécuter un package SSIS avec l’activité Exécuter le package SSIS dans Azure Data Factory avec PowerShell](how-to-invoke-ssis-package-ssis-activity-powershell.md)
- [Moderniser et étendre vos flux de travail ETL/ELT avec des activités SSIS dans des pipelines Azure Data Factory](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)