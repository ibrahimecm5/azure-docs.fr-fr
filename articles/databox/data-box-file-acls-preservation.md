---
title: Conservation des listes de contrôle d’accès, des attributs et des horodatages de fichiers avec Azure Data Box
description: Listes de contrôle d’accès, horodatages et attributs conservés pendant la copie des données via SMB vers Azure Data Box. Copie de métadonnées avec les outils de copie de données Windows et Linux.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: conceptual
ms.date: 07/16/2021
ms.author: alkohli
ms.openlocfilehash: 96d3957a7626e393728d4a309bc56ecaa19d4e83
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2021
ms.locfileid: "114400918"
---
# <a name="preserving-file-acls-attributes-and-timestamps-with-azure-data-box"></a>Conservation des listes de contrôle d’accès, des attributs et des horodatages de fichiers avec Azure Data Box

Azure Data Box vous permet de conserver les listes de contrôle d’accès (ACL, access-control list), les horodatages et les attributs de fichier lors de l’envoi de données à Azure. Cet article décrit les métadonnées que vous pouvez transférer lors de la copie de données vers Data Box via le protocole SMB (Server Message Block) pour les charger sur Azure Files. 

Des étapes spécifiques sont fournies pour copier les métadonnées à l’aide d’outils de copie de données Windows et Linux. Les métadonnées ne sont pas conservées lors du transfert de données vers Stockage Blob.

Dans cet article, les ACL, les horodatages et les attributs de fichier transférés sont désignés collectivement sous le nom de *métadonnées*.

## <a name="transferred-metadata"></a>Métadonnées transférées

Les métadonnées suivantes sont transférées lorsque les données dans Data Box sont chargées vers Azure Files.

#### <a name="timestamps"></a>Horodatages

Les horodatages suivants sont transférés :
- CreationTime
- LastWriteTime

L’horodatage suivant n’est pas transféré :
- LastAccessTime
  
#### <a name="file-attributes"></a>Attributs de fichier

Sauf indication contraire, les attributs de fichier des fichiers et des répertoires sont transférés.

Les attributs de fichier suivants sont transférés :
- FILE_ATTRIBUTE_READONLY (fichier uniquement)
- FILE_ATTRIBUTE_HIDDEN
- FILE_ATTRIBUTE_SYSTEM
- FILE_ATTRIBUTE_DIRECTORY (répertoire uniquement)
- FILE_ATTRIBUTE_ARCHIVE
- FILE_ATTRIBUTE_TEMPORARY (fichier uniquement)
- FILE_ATTRIBUTE_NO_SCRUB_DATA

Les attributs de fichier suivants ne sont pas transférés :
- FILE_ATTRIBUTE_OFFLINE
- FILE_ATTRIBUTE_NOT_CONTENT_INDEXED
  
Les attributs de lecture seule sur les répertoires ne sont pas transférés.

#### <a name="acls"></a>listes de contrôle d'accès

Toutes les ACL pour les répertoires et les fichiers que vous copiez sur Data Box via SMB sont copiées et transférées. Les transferts incluent à la fois des listes de contrôle d’accès discrétionnaires (DACL) et des listes de contrôle d’accès système (SACL). Pour Linux, seules les ACL Windows NT sont transférées.

Les ACL ne sont pas transférées pendant les copies de données via le système de fichiers réseau (NTS) ni lorsque vous utilisez le service de copie de données pour transférer vos données. Le service de copie de données lit les données directement dans vos partages et ne peut pas lire les ACL.

Même si votre outil de copie de données ne copie pas les ACL, les ACL par défaut des répertoires et des fichiers sont transférées vers Azure Files. Les ACL par défaut ont des autorisations pour le compte administrateur intégré, le compte SYSTÈME et le compte d’utilisateur de partage SMB qui a été utilisé pour monter et copier des données dans Data Box.

Les ACL contiennent des descripteurs de sécurité avec les propriétés suivantes : ACL, Propriétaire, Groupe, SACL.

Le transfert des ACL est activé par défaut. Vous pouvez désactiver ce paramètre dans l’interface utilisateur web locale de Data Box. Pour plus d’informations, consultez [Utiliser l’interface utilisateur web locale pour administrer vos instances Data Box et Data Box Heavy](./data-box-local-web-ui-admin.md).

> [!NOTE]
> Les fichiers dont les ACL contiennent des chaînes d’entrée de contrôle d’accès conditionnel (ACE) ne sont pas copiés. Il s’agit d’un problème connu. Pour le contourner, copiez ces fichiers dans le partage Azure Files manuellement en montant le partage, puis en utilisant un outil de copie qui prend en charge la copie des ACL.

## <a name="copying-data-and-metadata"></a>Copie de données et de métadonnées

Pour transférer les ACL, les horodatages et les attributs de vos données, utilisez les procédures suivantes pour copier des données dans Data Box. 

### <a name="windows-data-copy-tool"></a>Outil de copie de données Windows

Pour copier des données dans Data Box via SMB, utilisez un outil de copie de fichiers SMB, tel que `robocopy`. L’exemple de commande suivant copie tous les fichiers et répertoires, en transférant les métadonnées avec les données.

Lorsque vous utilisez l’option `/copyall` ou `/dcopy:DAT`, assurez-vous que les privilèges requis d’opérateur de sauvegarde ne sont pas désactivés. Pour plus d’informations, consultez [Utiliser l’interface utilisateur web locale pour administrer vos instances Data Box et Data Box Heavy](./data-box-local-web-ui-admin.md). 

```console
robocopy <Source> <Target> * /copyall /e /dcopy:DAT /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /log+:<LogFile>
```

where

|Option |Description |
|------------------- | ----- |
|`/copyall` |Copie tous les attributs.|
|`/e`      |Copie les sous-répertoires, y compris les répertoires vides.         |
|`/dcopy:DAT`  |Copie les données, les attributs et les horodatages. Remarque : L’option /dcopy:DAT doit être utilisée pour transférer `CreationTime` dans les répertoires. |
|`/r:3`    |Spécifie trois nouvelles tentatives en cas d’échec de la copie.         |
|`/w:60`   |Spécifie un temps d’attente de 60 secondes entre les nouvelles tentatives.         |
|`/is`     |Inclut les mêmes fichiers.         |
|`/nfl`    |Ne consigne pas les noms de fichiers.         |
|`/ndl`    |Ne consigne pas les noms de répertoire.        |
|`/np`     |N’affiche pas la progression de l’opération de copie.         |
|`/MT:32 or 64`  |Utilise le multithreading, avec 32 ou 64 threads.           |
|`/fft`    |Réduit la granularité de l’horodatage pour n’importe quel système de fichiers.        |
|`/log+:<LogFile>`  |Ajoute la sortie au fichier journal existant.|

Pour plus d’informations sur ces paramètres `robocopy`, consultez [Tutoriel : Copier des données sur Azure Data Box Disk par le biais de SMB](./data-box-deploy-copy-data.md).

> [!NOTE]
> Si vous utilisez `/copyall` pour copier vos données, les listes de contrôle d’accès sources des répertoires et des fichiers sont transférées vers Azure Files. Si vous disposez uniquement d’un accès en lecture à vos données sources et n’avez pas pu modifier les données sources, vous ne disposerez d’un accès en lecture que sur les données dans la Data Box. Utilisez `/copyall` uniquement si vous envisagez de copier toutes les listes de contrôle d’accès des répertoires et des fichiers avec les données.

#### <a name="use-robocopy-to-list-copy-modify-files-on-data-box"></a>Utilisez la commande robocopy pour répertorier, copier et modifier des fichiers sur Data Box

Voici quelques-uns des scénarios courants que vous allez utiliser lors de la copie de données à l’aide de la commande `robocopy`.

- **Copier uniquement les données vers Data Box, pas les listes de contrôle d’accès des répertoires et des fichiers**

    Utilisez l’option `/dcopy:DAT` pour copier uniquement les données, les attributs et les horodateurs. Les listes de contrôle d’accès des répertoires et des fichiers ne sont pas copiées.

- **Copier les données et les listes de contrôle d’accès des répertoires et des fichiers vers Data Box**

    Utilisez `/copyall` pour copier toutes les données sources, y compris les listes de contrôle d’accès des répertoires et des fichiers.

- **Répertorier le système de fichiers sur Data Box à l’aide de la commande robocopy**

    Pour répertorier le contenu du répertoire, utilisez la commande suivante :

    `robocopy <source-dir> NULL /l /s /xx /njh /njs /fp /B`

    Notez que l’Explorateur de fichiers ne vous autorise pas à répertorier ces fichiers.
    
- **Copier ou supprimer des dossiers et des fichiers sur Data Box**

    Pour copier un seul fichier, utilisez la commande suivante :

    `robocopy <source-dir> <destination-dir> <file-name> /B`

    Pour supprimer un seul fichier, utilisez la commande suivante :

    `robocopy <source-dir> <destination-dir> <file-name> /purge /B`

    Dans la commande ci-dessus, `<source-dir>` ne doit pas contenir le fichier : `<file-name>` . Ensuite, la commande ci-dessus synchronise la destination avec la source, ce qui entraîne la suppression du fichier de la destination.

    Notez que l’Explorateur de fichiers peut ne pas vous autoriser à effectuer les opérations ci-dessus.

Pour plus d’informations, consultez [Utilisation des commandes robocopy](/windows-server/administration/windows-commands/robocopy).

### <a name="linux-data-copy-tool"></a>Outil de copie de données Linux

Le transfert de métadonnées sous Linux est un processus en deux étapes. Tout d’abord, vous copiez les données sources à l’aide d’un outil tel que `rsync`, qui ne copie pas les métadonnées. Après avoir copié les données, vous pouvez copier les métadonnées à l’aide d’un outil tel que `smbcacls` ou `cifsacl`. 

Les exemples de commandes suivants effectuent la première étape, en copiant les données à l’aide de `rsync`. 

```console
cp -aR /etc /opt/ 
rsync -avP /etc /opt (-a copies a directory)
```

## <a name="next-steps"></a>Étapes suivantes

- [Copier des données sur Azure Data Box Disk par le biais de SMB](./data-box-deploy-copy-data.md)
