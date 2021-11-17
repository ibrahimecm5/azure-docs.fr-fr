---
title: Liste des alertes intégrées et personnalisées
description: Découvrez les alertes de sécurité et les actions de correction recommandées en utilisant les fonctionnalités et le service Defender pour IoT Hub.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 89fb77bed6116fcbae847b19b79e90d4dff34a23
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132293532"
---
# <a name="defender-for-iot-hub-security-alerts"></a>Alertes de sécurité Defender pour IoT Hub

Defender pour IoT analyse continuellement votre solution IoT à l’aide d’analyses avancées et d’informations sur les menaces pour vous alerter en cas d’activités malveillantes.
En outre, vous pouvez créer des alertes personnalisées en fonction de votre connaissance du comportement attendu de l’appareil.
Une alerte sert d’indicateur de compromission potentielles et doit être examinée et corrigée.

Dans cet article, vous trouverez une liste d’alertes intégrées qui peuvent être déclenchées sur votre hub IoT.
Outre les alertes intégrées, Defender pour IoT vous permet de définir des alertes personnalisées basées sur le comportement attendu d’un IoT Hub et/ou d’un appareil.
Pour plus d’informations, consultez [Alertes personnalisables](concept-customizable-security-alerts.md).

## <a name="built-in-alerts-for-iot-hub"></a>Alertes intégrées pour IoT Hub

### <a name="medium-severity"></a>Niveau de gravité Moyen

| Nom | severity | source de données | Description | Correction suggérée | AlertType |
|--|--|--|--|--|--|
| Ajout d’un nouveau certificat à un IoT Hub | Moyenne | IoT Hub | Un certificat a été ajouté à un IoT Hub. Si cette action a été effectuée par un tiers autorisé, cela peut être le signe d’une activité malveillante. | 1. Assurez-vous que le certificat a été ajouté par un tiers autorisé. <br> 2. S’il n’a pas été ajouté par un tiers autorisé, supprimez-le et signalez l’alerte à l’équipe responsable de la sécurité de l’organisation. | IoT_CertificateSuccessfullyAddedToHub |
| Suppression d’un certificat d’un IoT Hub | Moyenne | IoT Hub | Un certificat a été supprimé d’un IoT Hub. Si cette action a été effectuée par un tiers autorisé, cela peut être le signe d’une activité malveillante. | 1. Assurez-vous que le certificat a été supprimé par un tiers autorisé. <br> 2. S’il n’a pas été supprimé par un tiers autorisé, ajouter le à nouveau et signalez l’alerte à l’équipe responsable de la sécurité de l’organisation. | IoT_CertificateSuccessfullyDeletedFromHub |
| Détection d’une tentative infructueuse d’ajout d’un certificat à un IoT Hub | Moyenne | IoT Hub | Une tentative infructueuse d’ajout d’un certificat à un IoT Hub a eu lieu. Si cette action a été effectuée par un tiers autorisé, cela peut être le signe d’une activité malveillante. | Assurez-vous que les autorisations de modification des certificats sont accordées uniquement aux tiers autorisés. | Hub_CertificateFailedToBeAddedToHub |
| Détection d’une tentative infructueuse de suppression d’un certificat d’un IoT Hub | Moyenne | IoT Hub | Une tentative infructueuse de suppression d’un certificat d’un IoT Hub a eu lieu. Si cette action a été effectuée par un tiers autorisé, cela peut être le signe d’une activité malveillante. | Assurez-vous que les autorisations de modification des certificats sont accordées uniquement aux tiers autorisés. | IoT.Hub_CertificateFailedToBeDeletedFromHub |
| Non-concordance de l’empreinte du certificat de l’appareil x.509 | Moyenne | IoT Hub | L’empreinte du certificat de l’appareil x.509 ne correspondait pas à la configuration. | Examinez les alertes sur les appareils. Aucune action supplémentaire n’est requise. | IoT_Cert_Print_Mismatch |
| Le certificat x.509 a expiré | Moyenne | IoT Hub | Le certificat de l’appareil X.509 a expiré. | Il peut s’agir d’un appareil légitime dont le certificat est arrivé à expiration ou d’une tentative d’emprunt d’identité d’un appareil légitime. Si l’appareil légitime communique correctement, il s’agit probablement d’une tentative d’emprunt d’identité. | IoT_Cert_Expired |

### <a name="low-severity"></a>Gravité faible

| Nom | severity | source de données | Description | Correction suggérée | AlertType |
|--|--|--|--|--|--|
| Détection d’une tentative d’ajout ou de modification d’un paramètre de diagnostic d’un IoT Hub | Faible | IoT Hub | Une tentative d’ajout ou de modification d’un paramètre de diagnostic d’un IoT Hub a été détectée. Les paramètres de diagnostic permettent de recréer les pistes d’activité à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau. Si cette action n'a pas été effectuée par une partie autorisée, cela peut indiquer une activité malveillante. | 1. Assurez-vous que le certificat a été supprimé par un tiers autorisé. <br> 2. S’il n’a pas été supprimé par un tiers autorisé, ajoutez-le à nouveau et signalez l’alerte à l’équipe responsable de la sécurité de l’organisation. | IoT_DiagnosticSettingAddedOrEditedOnHub |
| Détection d’une tentative de suppression d’un paramètre de diagnostic d’un IoT Hub | Faible | IoT Hub | Une tentative d’ajout ou de modification d’un paramètre de diagnostic d’un IoT Hub a été détectée. Les paramètres de diagnostic permettent de recréer les pistes d’activité à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau. Si cette action n'a pas été effectuée par une partie autorisée, cela peut indiquer une activité malveillante. | Assurez-vous que les autorisations de modification des paramètres de diagnostic sont accordées uniquement aux tiers autorisés. | IoT_DiagnosticSettingDeletedFromHub |
| Jeton SAS expiré | Faible | IoT Hub | Un jeton SAS ayant expiré est utilisé par un appareil | Il peut s’agir d’un appareil légitime dont le jeton est arrivé à expiration ou d’une tentative d’emprunt d’identité d’un appareil légitime. Si l’appareil légitime communique correctement, il s’agit probablement d’une tentative d’emprunt d’identité. | IoT_Expired_SAS_Token |
| Signature de jeton SAS non valide | Faible | IoT Hub | Le jeton SAS utilisé par un appareil comporte une signature non valide. Le jeton SAS ne correspond ni à la clé primaire ni à la clé secondaire. | Examinez les alertes sur les appareils. Aucune action supplémentaire n’est requise. | IoT_Invalid_SAS_Token |

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble](overview.md) du service Defender pour IoT
