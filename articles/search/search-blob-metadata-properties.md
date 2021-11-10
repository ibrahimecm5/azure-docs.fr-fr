---
title: Propriétés des métadonnées de contenu
titleSuffix: Azure Cognitive Search
description: Les propriétés des métadonnées de documents peuvent fournir du contenu aux champs d’un index de recherche, ou des informations qui renseignent sur le comportement de l’indexation au moment de l’exécution. Cet article répertorie les propriétés de métadonnées prises en charge dans Recherche cognitive Azure.
author: nitinme
manager: nitinme
ms.author: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/22/2021
ms.openlocfilehash: c6f02a50160d650f44e9793dbfa59e0dbf58ebbc
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132055566"
---
# <a name="content-metadata-properties-used-in-azure-cognitive-search"></a>Propriétés des métadonnées de contenu utilisées dans la Recherche cognitive Azure

SharePoint Online et le stockage Blob Azure peuvent présenter différents contenus ; bon nombre de ces types de contenu disposent de propriétés de métadonnées pouvant être utiles dans l’indexation. Tout comme vous pouvez créer des champs de recherche pour les propriétés standard des blobs comme **`metadata_storage_name`** , vous pouvez créer des champs pour les propriétés de métadonnées spécifiques à un format de document.

## <a name="supported-document-formats"></a>Formats de document pris en charge

La Recherche cognitive prend en charge l’indexation d’objets blob et l’indexation de documents SharePoint Online pour les formats de document suivants :

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="properties-by-document-format"></a>Propriétés par format de document

Le tableau ci-après récapitule le traitement appliqué pour chaque format de document, il décrit les propriétés de métadonnées extraites par un indexeur d’objets blob et l’indexeur SharePoint Online.

| Format de document/type de contenu | Métadonnées extraites | Détails du traitement |
| --- | --- | --- |
| HTML (texte/HTML ou application/XHTML+XML) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |Suppression du balisage HTML et extraction du texte |
| PDF (application/pdf) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title`<br/>`metadata_creation_date` |Extraction du texte, y compris les documents incorporés (à l’exclusion des images) |
| DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extraction du texte, y compris les documents incorporés |
| DOC (application/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extraction du texte, y compris les documents incorporés |
| DOCM (application/vnd.ms-word.document.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extraction du texte, y compris les documents incorporés |
| WORD XML (application/vnd.ms-word2006ml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Suppression du balisage XML et extraction du texte |
| WORD 2003 XML (application/vnd.ms-wordml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |Suppression du balisage XML et extraction du texte |
| XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extraction du texte, y compris les documents incorporés |
| XLS (application/vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extraction du texte, y compris les documents incorporés |
| XLSM (application/vnd.ms-excel.sheet.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extraction du texte, y compris les documents incorporés |
| PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extraction du texte, y compris les documents incorporés |
| PPT (application/vnd.ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extraction du texte, y compris les documents incorporés |
| PPTM (application/vnd.ms-powerpoint.presentation.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extraction du texte, y compris les documents incorporés |
| MSG (application/vnd.ms-outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Extrayez le texte, y compris celui des pièces jointes. `metadata_message_to_email`, `metadata_message_cc_email` et `metadata_message_bcc_email` sont des collections de chaînes ; les autres champs sont des chaînes.|
| ODT (application/vnd.oasis.opendocument.text) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extraction du texte, y compris les documents incorporés |
| ODS (application/vnd.oasis.opendocument.spreadsheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extraction du texte, y compris les documents incorporés |
| ODP (application/vnd.oasis.opendocument.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_title` |Extraction du texte, y compris les documents incorporés |
| ZIP (application/zip) |`metadata_content_type` |Extraction du texte de tous les documents figurant dans l’archive |
| GZ (application/gzip) |`metadata_content_type` |Extraction du texte de tous les documents figurant dans l’archive |
| EPUB (application/epub+zip) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_title`<br/>`metadata_description`<br/>`metadata_language`<br/>`metadata_keywords`<br/>`metadata_identifier`<br/>`metadata_publisher` |Extraction du texte de tous les documents figurant dans l’archive |
| XML (application/xml) |`metadata_content_type`<br/>`metadata_content_encoding`<br/>`metadata_language`<br/> |Suppression du balisage XML et extraction du texte |
| KML (application/vnd.google-earth.kml+xml) |`metadata_content_type`<br/>`metadata_content_encoding`<br/>`metadata_language`<br/> |Suppression du balisage XML et extraction du texte |
| JSON (application/json) |`metadata_content_type`<br/>`metadata_content_encoding` |Extraction du texte<br/>REMARQUE :  si vous devez extraire plusieurs champs de document à partir d’un objet blob JSON, consultez [Indexation d’objets blob JSON](search-howto-index-json-blobs.md) pour plus de détails |
| EML (message/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Extraction du texte, y compris les pièces jointes |
| RTF (application/rtf) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count`<br/> | Extraction du texte|
| Texte brut (text/plain) |`metadata_content_type`<br/>`metadata_content_encoding`<br/>`metadata_language`<br/> | Extraction du texte|
| CSV (texte/CSV) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> | Extraction du texte<br/>REMARQUE : Si vous devez extraire plusieurs champs de document à partir d’un objet blob CSV, consultez [Indexation d’objets blob CSV](search-howto-index-csv-blobs.md) pour plus de détails |

## <a name="see-also"></a>Voir aussi

* [Indexeurs dans Recherche cognitive Azure](search-indexer-overview.md)
* [Comprendre les blobs avec l’intelligence artificielle](search-blob-ai-integration.md)
* [Vue d’ensemble de l’indexation de blobs](search-blob-storage-integration.md)
* [Indexation de SharePoint Online](search-howto-index-sharepoint-online.md)