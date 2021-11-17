---
title: Limites d’Azure Data Box Disk | Microsoft Docs
description: Décrit les limites système et les tailles recommandées pour Microsoft Azure Data Box Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 09/28/2021
ms.author: alkohli
ms.openlocfilehash: ce8cc86727c92f8c0091abafa8f5d91ee1ab21ad
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347629"
---
# <a name="azure-data-box-disk-limits"></a>Limites d’Azure Data Box Disk


Tenez compte de ces limites quand vous déployez et utilisez votre solution Microsoft Azure Data Box Disk.

## <a name="data-box-service-limits"></a>Limites du service Data Box

 - Le service Data Box est disponible dans les régions Azure répertoriées dans la section sur la [disponibilité des régions](data-box-disk-overview.md#region-availability).
 - Un seul compte de stockage est pris en charge avec Data Box Disk.

## <a name="data-box-disk-performance"></a>Performances de Data Box Disk

Lors de tests utilisant des disques connectés via USB 3.0, les performances des disques ont atteint 430 Mo/s. Les chiffres réels varient en fonction de la taille de fichier utilisée. Pour les fichiers plus petits, vous pouvez constater des performances moins élevées.

## <a name="azure-storage-limits"></a>Limites du stockage Azure

Cette section décrit les limites du service Stockage Azure et les conventions de nommage en vigueur pour les fichiers Azure, les objets blob de blocs Azure et les objets blob de pages Azure, telles qu’applicables au service Data Box. Examinez soigneusement les limites de stockage et suivez toutes les recommandations.

Pour les informations les plus récentes sur les limites du service de stockage Azure et les bonnes pratiques pour nommer les partages, les conteneurs et les fichiers, accédez à :

- [Affectation de noms et de références aux conteneurs](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Affectation de noms et de références aux partages](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Conventions d’objets blob de blocs et d’objets blob de pages](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> S’il existe des fichiers ou des répertoires qui dépassent les limites du service Stockage Azure, ou qui ne sont pas conformes aux conventions de nommage des fichiers/objets blob Azure, ces fichiers ou répertoires ne sont pas ingérés dans Stockage Azure par le biais du service Data Box.

## <a name="data-upload-caveats"></a>Mises en garde relatives au chargement de données

- Ne copiez pas les données directement sur les disques. Copiez les données dans des dossiers *ObjetBlobDeBlocs*, *ObjetBlobDePages* et *FichierAzure* créés au préalable.
- Un dossier présent sous *ObjetBlobDeBlocs* et *ObjetBlobDePages* est un conteneur. Par exemple, les conteneurs sont créés sous la forme suivante *ObjetBlobDeBlocs/conteneur* et *ObjetBlobDePages/conteneur*.
- Si un dossier porte le même nom qu’un conteneur existant, le contenu du dossier est fusionné avec le contenu du conteneur. Les fichiers ou les objets BLOB qui ne se trouvent pas déjà dans le Cloud sont ajoutés au conteneur. Si un fichier ou un objet blob porte le même nom qu’un fichier ou un objet blob qui se trouve déjà dans le conteneur, le fichier ou l’objet blob existant est remplacé.
- Chaque fichier écrit dans les partages *ObjetBlobDeBlocs* et *ObjetBlobDePages* est chargé respectivement en tant qu’objet blob de blocs et objet blob de pages. 
- La hiérarchie des fichiers est conservée lors du chargement vers le cloud pour les objets BLOB et les Azure Files. Par exemple, vous avez copié un fichier à ce chemin d’accès : `<container folder>\A\B\C.txt`. Ce fichier est chargé vers le même chemin d'accès virtuel dans le cloud.
- Une hiérarchie de répertoires vides (sans fichiers) créée sous les dossiers *ObjetBlobDeBlocs* et *ObjetBlobDePages* n’est pas chargée.
- Pour améliorer les performances lors des chargements de données, nous vous recommandons d’[activer les partages de fichiers volumineux sur le compte de stockage et d’augmenter la capacité de partage à 100 Tio](../../articles/storage/files/storage-how-to-create-file-share.md#enable-large-files-shares-on-an-existing-account). Les partages de fichiers volumineux sont uniquement pris en charge pour les comptes de stockage avec un stockage localement redondant (LRS).
- S’il se produit des erreurs lors du chargement des données sur Azure, un journal des erreurs est créé dans le compte de stockage cible. Le chemin menant à ce journal des erreurs est disponible dans le portail à l’issue du chargement ; vous pouvez consulter ce journal afin de procéder aux corrections. Ne supprimez pas les données de la source sans avoir préalablement vérifié les données chargées.
- Les métadonnées des fichiers et les autorisations NTFS ne sont pas préservées pendant le chargement des données sur Azure Files. Par exemple, l’attribut *Dernière modification* des fichiers ne sera pas conservé lors de la copie des données.
- Si vous avez spécifié des disques managés dans la commande, passez en revue les considérations supplémentaires suivantes :

    - Vous pouvez avoir un seul disque managé du même nom dans un groupe de ressources sur l’ensemble des dossiers précréés et de Data Box Disk. Les disques durs virtuels chargés dans les dossiers pré-créés doivent donc avoir des noms uniques. Assurez-vous que le nom donné n’est pas déjà attribué à un autre disque managé existant dans un groupe de ressources. Si plusieurs disques durs virtuels ont le même nom, un seul de ces disques est converti en disque managé avec ce nom. Les autres disques durs virtuels sont chargés comme objets blob de pages dans le compte de stockage de préproduction.
    - Vous devez toujours copier les disques durs virtuels dans l’un des dossiers pré-créés. Si vous copiez les disques durs virtuels ailleurs que dans ces dossiers ou dans un dossier que vous avez créé vous-même, les disques durs virtuels sont chargés dans le compte de stockage Azure comme objets blob de pages au lieu de disques non managés.
    - Seuls les disques durs virtuels fixes peuvent être chargés pour créer des disques managés. Les disques durs virtuels dynamiques, les disques durs virtuels de différenciation ou les fichiers VHDX ne sont pas pris en charge.
    - Les fichiers non VHD copiés dans les dossiers de disque managé précréés ne seront pas convertis en disque managé.

## <a name="azure-storage-account-size-limits"></a>Limites de la taille du compte de stockage Azure

Voici les limites de la taille des données qui peuvent être copiées sur un compte de stockage. Assurez-vous que les données que vous chargez sont conformes à ces limites. 

| Type de données             | Limite par défaut          |
|--------------------------|------------------------|
| objet blob de blocs, objet blob de pages.    | Pour des informations actualisées sur ces limites, consultez [Objectifs de mise à l’échelle du stockage Azure Blob](../storage/blobs/scalability-targets.md#scale-targets-for-blob-storage), [Objectifs de mise à l’échelle du stockage Azure standard](../storage/common/scalability-targets-standard-account.md#scale-targets-for-standard-storage-accounts) et [Objectifs de mise à l’échelle du stockage Azure Files](../storage/files/storage-files-scale-targets.md). <br /><br /> Les limites comprennent les données à partir de toutes les sources, notamment Data Box Disk.|


## <a name="azure-object-size-limits"></a>Limites de taille des objets Azure

Voici les tailles des objets Azure qui peuvent être écrits. Assurez-vous que tous les fichiers chargés sont en conformité avec ces limites.

| Type d’objet Azure | Limite par défaut                                             |
|-------------------|-----------------------------------------------------------|
| Objet blob de blocs        | ~4.75 Tio                                                 |
| Objet blob de pages         | 8 Tio <br> Chaque fichier chargé dans le format d’objet blob de pages doit être de 512 octets alignés, sinon le chargement échoue. <br> Les disques VHD et VHDX sont de 512 octets alignés.) |
|Azure Files        | 1 Tio <br> Bande passante taille de partage est de 5 Tio     |
| Disques managés     |4 Tio <br> Pour plus d’informations sur la taille et les limites, consultez : <li>[Objectifs de scalabilité pour les disques managés](../virtual-machines/disks-scalability-targets.md#managed-virtual-machine-disks)</li>|


## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Conventions de nommage des objets blob de blocs,des objets blob de pages et des fichiers Azure

| Entité                                       | Conventions                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Noms de conteneur pour les objets blob de blocs et les objets blob de pages <br> Noms de partages de fichiers pour Azure Files | Doit être un nom DNS valide dont la longueur est comprise entre 3 et 63 caractères. <br>  Doit commencer par une lettre ou un chiffre. <br> Ne peut contenir que des lettres minuscules, des chiffres et le trait d’union (-). <br> Chaque trait d’union (-) doit être immédiatement précédé et suivi d’une lettre ou d’un chiffre. <br> Les traits d’union consécutifs ne sont pas autorisés dans les noms. |
| Noms de répertoires et de fichiers pour les fichiers Azure     |<li> Conservent et respectent la casse, et ne doivent pas dépasser 255 caractères. </li><li> Ne peuvent pas se terminer par une barre oblique avant (/). </li><li>Si renseignée, elle sera automatiquement supprimée. </li><li> Les caractères suivants ne sont pas autorisés : <code>" \\ / : \| < > * ?</code></li><li> Les caractères d’URL réservées doivent être correctement placés dans une séquence d’échappement. </li><li> Les caractères de chemin d’URL illégal ne sont pas autorisés. Les codes de caractères tels que \\uE000 ne sont pas des caractères Unicode valides. Certains caractères Unicode ou ASCII, comme les caractères de contrôle (0x00 à 0x1F, \\u0081, etc.) ne sont pas autorisés. Pour les règles concernant les chaînes Unicode dans HTTP/1.1, consultez RFC 2616, Section 2.2 : Règles de base et RFC 3987. </li><li> Les noms de fichiers suivants ne sont pas autorisés : LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK$, le point (.) et les deux points (..).</li>|
| Noms d’objet blob pour les objets blob de blocs et les objets blob de pages      | Les noms d’objet blob respectent la casse et peuvent contenir une combinaison de caractères. <br> Le nom d’objet blob doit comprendre entre 1 et 1 024 caractères. <br> Les caractères d’URL réservées doivent être correctement placés dans une séquence d’échappement. <br>Le nombre de segments de ligne comprenant le nom d’objet blob ne peut pas dépasser 254. Un segment de chemin représente la chaîne située entre des caractères délimiteurs consécutifs (par exemple, la barre oblique « / ») qui correspond au nom d’un répertoire virtuel. |

## <a name="managed-disk-naming-conventions"></a>Conventions d’affectation de noms des disques managés

| Entité | Conventions                                             |
|-------------------|-----------------------------------------------------------|
| Noms de disques managés       | <li> Le nom doit contenir de 1 à 80 caractères. </li><li> Le nom doit commencer par une lettre ou un chiffre et se terminer par une lettre, un chiffre ou un trait de soulignement. </li><li> Le nom peut contenir uniquement des lettres, des chiffres, des traits d’union, des traits de soulignement et des points. </li><li>     Le nom ne doit pas contenir d’espaces ni de `/`.                     |

## <a name="next-steps"></a>Étapes suivantes

- Passez en revue la [configuration système requise pour Data Box Disk](data-box-disk-system-requirements.md)
