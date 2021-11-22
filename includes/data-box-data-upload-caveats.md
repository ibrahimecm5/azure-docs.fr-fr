---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 11/10/2021
ms.author: alkohli
ms.openlocfilehash: d02eb129dd66234cf7c8522ca8204fb0026da092
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132353943"
---
- Conteneurs, partages et dossiers :
  - Ne copiez pas les fichiers directement sur les partages précréés. Vous devez créer un dossier dans le partage, puis copier les fichiers dans ce dossier.
  - Un dossier sous *StorageAccount_BlockBlob* et *StorageAccount_PageBlob* est un conteneur. Par exemple, les conteneurs sont créés sous la forme suivante *StorageAccount_BlockBlob/container* et *StorageAccount_PageBlob/container*.
  - Chaque dossier créé directement sous *StorageAccount_AzureFiles* est traduit dans un Partage de fichiers Azure.
  - Le stockage Blob Azure ne prend pas en charge les répertoires. Si vous créez un dossier sous le dossier *StorageAccount_BlockBlob*, des dossiers virtuels sont alors créés dans le nom de l’objet blob. Pour Azure Files, la structure du répertoire est conservée.
- Fusion du contenu du dossier :
  - Chaque fichier écrit dans les partages *StorageAccount_BlockBlob* et *StorageAccount_PageBlob* est chargé respectivement en tant qu’objet blob de blocs et objet blob de pages.
  - Si un dossier porte le même nom qu’un conteneur existant, le contenu du dossier est fusionné avec le contenu du conteneur. Les fichiers ou les objets BLOB qui ne se trouvent pas déjà dans le Cloud sont ajoutés au conteneur. Si un fichier ou un objet BLOB porte le même nom qu’un fichier ou un objet BLOB qui se trouve déjà dans le conteneur, le fichier ou l’objet BLOB existant est remplacé.
  - Une hiérarchie de répertoires vides (sans fichiers) créée sous les dossiers *StorageAccount_BlockBlob* et *StorageAccount_PageBlob* n’est pas chargée.
- Si vous utilisez les protocoles SMB et NFS pour les copies de données, nous vous recommandons ce qui suit :
  - Utilisez différents comptes de stockage pour SMB et NFS.
  - Ne copiez pas les mêmes données vers la même destination finale dans Azure en utilisant SMB et NFS. En effet, le résultat final ne pourrait pas être déterminé.
  - Même si la copie via SMB et NFS en parallèle peut fonctionner, nous vous déconseillons de le faire, car elle est sujette aux erreurs humaines. Attendez la fin de la copie des données SMB avant de démarrer une copie de données NFS.
- Gestion des chargements : 
  - Pour améliorer les performances lors des chargements de données, nous vous recommandons d’[activer les partages de fichiers volumineux sur le compte de stockage et d’augmenter la capacité de partage à 100 Tio](../articles/storage/files/storage-how-to-create-file-share.md#enable-large-files-shares-on-an-existing-account). Les partages de fichiers volumineux sont uniquement pris en charge pour les comptes de stockage avec un stockage localement redondant (LRS).
  - S’il se produit des erreurs lors du chargement des données sur Azure, un journal des erreurs est créé dans le compte de stockage cible. Le chemin menant à ce journal des erreurs est disponible à l’issue du chargement ; vous pouvez consulter ce journal afin de procéder aux corrections. Ne supprimez pas les données de la source sans avoir préalablement vérifié les données chargées.
  - Les métadonnées de fichier et les autorisations NTFS peuvent être conservées lorsque les données sont chargées vers Azure Files en utilisant les instructions de [conservation des listes de contrôle d’accès, des attributs et des horodateurs de fichiers avec Azure Data Box](../articles/databox/data-box-file-acls-preservation.md).
  - La hiérarchie des fichiers est conservée durant le chargement vers le cloud pour les objets blob et Azure Files. Par exemple, vous avez copié un fichier à ce chemin d’accès : `<container folder>\A\B\C.txt`. Ce fichier est chargé vers le même chemin dans le cloud.
  
