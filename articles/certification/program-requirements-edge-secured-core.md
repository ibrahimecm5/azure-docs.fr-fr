---
title: Conditions requises pour la certification Edge Secured-Core
description: Conditions requises pour la certification Edge Secured-Core
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: Edge Secured-core Certification Requirements
ms.service: certification
ms.openlocfilehash: 35091e1ccde554ce897dc629c3c372af05650458
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132293855"
---
# <a name="edge-secured-core-certification-requirements-preview"></a>Exigences pour la certification Edge Secured-Core (Préversion) #

Ce document décrit les capacités et les exigences spécifiques aux appareils qui doivent être satisfaites pour obtenir la certification et inscrire un appareil dans le catalogue Azure Certified Device avec l’étiquette Edge Secured-Core.

## <a name="program-purpose"></a>Objet du programme ##
Edge Secured-Core est une certification incrémentielle du programme Azure Certified Device pour les appareils IoT exécutant un système d’exploitation complet, tel que Linux ou Windows 10 IoT. Ce programme permet aux partenaires de différencier leurs appareils en répondant à un ensemble supplémentaire de critères de sécurité. Les appareils répondant à ces critères permettent de tenir les promesses suivantes :
1. Identité d’appareil basée sur le matériel 
2. Apte à appliquer l’intégrité du système 
3. Reste à jour et peut être géré à distance
4. Assure la protection des données au repos
5. Assure la protection des données en transit
6. Agent de sécurité intégré et renforcement de la sécurité
## <a name="requirements"></a>Configuration requise ##

---
|Nom|SecuredCore.Built-in.Security|
|:---|:---|
|Statut|Obligatoire|
|Description|L’objectif du test est de s’assurer que les appareils peuvent signaler des informations et des événements de sécurité en envoyant des données à Microsoft Defender pour IoT.|
|Disponibilité cible|2021|
|S'applique à|N’importe quel appareil|
|Système d’exploitation|Sans dépendance|
|Type de validation|Manuel/outils|
|Validation |L’appareil doit générer des alertes et des journaux de sécurité. Consigne les messages de l’appareil et les alertes dans Microsoft Defender pour le cloud.<ol><li>Téléchargez et déployez l’agent de sécurité à partir de GitHub.</li><li>Validez le message d’alerte de Microsoft Defender pour IoT.</li></ol>|
|Ressources|[Documents Azure Defender pour IoT](../defender-for-iot/how-to-configure-agent-based-solution.md)|

---
|Nom|SecuredCore.Encryption.Storage|
|:---|:---|
|Statut|Obligatoire|
|Description|L’objectif du test est de valider que les données sensibles peuvent être chiffrées sur un stockage non volatile.|
|Disponibilité cible|2021|
|S'applique à|N’importe quel appareil|
|Système d’exploitation|Sans dépendance|
|Type de validation|Manuel/outils|
|Validation|Appareil à valider par le biais de l’ensemble d’outils pour vérifier que le chiffrement du stockage est activé et que l’algorithme par défaut est XTS-AES, avec une longueur de clé de 128 bits ou plus. </br></br>Remarque : la version préliminaire de juin 2021 vérifie uniquement que DM-Crypt est installée sur l’appareil et que celui-ci dispose d’une partition chiffrée.|
|Ressources||

---
|Nom|SecuredCore.Hardware.SecureEnclave|
|:---|:---|
|Statut|Facultatif|
|Description|L’objectif du test est de valider l’existence d’une enclave sécurisée et que l’enclave est accessible à partir d’un agent sécurisé.|
|Disponibilité cible|2021|
|S'applique à|N’importe quel appareil|
|Système d’exploitation|Sans dépendance|
|Type de validation|Manuel/outils|
|Validation|Appareil à valider par le biais de l’ensemble d’outils pour garantir que l’agent de sécurité Azure peut communiquer avec l’enclave sécurisée.|
|Ressources|https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesLinux.md|

---
|Nom|SecuredCore.Hardware.Identity|
|:---|:---|
|Statut|Obligatoire|
|Description|L’objectif du test est de valider que l’identification de l’appareil est enracinée dans le matériel.|
|Disponibilité cible|2021|
|S'applique à|N’importe quel appareil|
|Système d’exploitation|Sans dépendance|
|Type de validation|Manuel/outils|
|Validation|Appareil à valider par le biais de l’ensemble d’outils pour s’assurer que l’appareil dispose d’un TPM et qu’il peut être approvisionné via IoT Hub à l’aide de la paire de clés de type EK (Endorsement Key).|
|Ressources|[Configurer l’approvisionnement automatique avec DPS](../iot-dps/quick-setup-auto-provision.md)|

---
|Nom|SecuredCore.Update|
|:---|:---|
|Statut|Obligatoire|
|Description|L’objectif du test est de valider que l’appareil peut recevoir et mettre à jour son microprogramme et son logiciel.|
|Disponibilité cible|2021|
|S'applique à|N’importe quel appareil|
|Système d’exploitation|Sans dépendance|
|Type de validation|Manuel/outils|
|Validation|Partenaire confirmant qu’il a pu envoyer une mise à jour à l’appareil via Microsoft Update, [Device Update for IoT Hub (ADU)](../iot-hub-device-update/understand-device-update.md). Pour les appareils Linux qui utilisent la mise à jour des appareils pour IoT Hub, la certification nécessite de fournir un fichier de mise à jour. swu pendant le processus de test de base sécurisé et des informations spécifiques à l’appareil pour le service de certification afin de générer un fichier [manifeste de mise à jour\](../iot-hub-device-update/update-manifest.md).|
|Ressources|[Device Update pour IoT Hub](../iot-hub-device-update/index.yml)|

---
|Nom|SecuredCore.Manageability.Configuration|
|:---|:---|
|Statut|Obligatoire|
|Description|L’objectif du test est de valider la prise en charge de la gestion de la sécurité à distance par les appareils.|
|Disponibilité cible|2021|
|S'applique à|N’importe quel appareil|
|Système d’exploitation|Sans dépendance|
|Type de validation|Manuel/outils|
|Validation|Appareil à valider par le biais de l’ensemble d’outils pour garantir que l’appareil prend en charge la possibilité d’être géré à distance et les configurations de sécurité spécifiques. L’état est rapporté à IoT Hub/Microsoft Defender pour IoT.|
|Ressources||

---
|Nom|SecuredCore.Manageability.Reset|
|:---|:---|
|Statut|Obligatoire|
|Description|L’objectif de ce test est de valider l’appareil par rapport à deux cas d’usage : a) Capacité à réaliser une réinitialisation (supprimer les données et les configurations de l’utilisateur), b) Restaurer l’appareil au dernier état fonctionnel connu dans le cas d’une mise à jour entraînant des problèmes.|
|Disponibilité cible|2021|
|S'applique à|N’importe quel appareil|
|Système d’exploitation|Sans dépendance|
|Type de validation|Manuel/outils|
|Validation|L’appareil doit être validé par une combinaison d’outils et de documents envoyés pour prouver que l’appareil prend en charge cette fonctionnalité. Le fabricant de l’appareil peut déterminer s’il faut implémenter ces capacités pour prendre en charge la réinitialisation à distance ou uniquement la réinitialisation locale.|
|Ressources||

---
|Nom|SecuredCore.Updates.Duration|
|:---|:---|
|Statut|Obligatoire|
|Description|L’objectif de cette stratégie est de s’assurer que l’appareil reste sécurisé.|
|Disponibilité cible|2021|
|S'applique à|N’importe quel appareil|
|Système d’exploitation|Sans dépendance|
|Type de validation|Manuel|
|Validation|Un engagement de la part des appareils certifiés sera requis pour maintenir à jour les appareils pendant 60 mois à compter de la date d’envoi. Les spécifications mises à la disposition de l’acheteur et des appareils eux-mêmes doivent indiquer d’une manière ou d’une autre la durée pendant laquelle leurs logiciels seront mis à jour.|
|Ressources||

---
|Nom|SecuredCore.Policy.Vuln.Disclosure|
|:---|:---|
|Statut|Obligatoire|
|Description|L’objectif de cette stratégie est de s’assurer qu’il existe un mécanisme de collecte et de distribution des rapports de vulnérabilités dans le produit.|
|Disponibilité cible|2021|
|S'applique à|N’importe quel appareil|
|Système d’exploitation|Sans dépendance|
|Type de validation|Manuel|
|Validation|La documentation relative au processus d’envoi et de réception des rapports de vulnérabilité pour les appareils certifiés sera examinée.|
|Ressources||

---
|Nom|SecuredCore.Policy.Vuln.Fixes|
|:---|:---|
|Statut|Obligatoire|
|Description|L’objectif de cette stratégie est de s’assurer que les vulnérabilités élevées/critiques (selon CVSS 3.0) sont traitées dans les 180 jours suivant la disponibilité du correctif.|
|Disponibilité cible|2021|
|S'applique à|N’importe quel appareil|
|Système d’exploitation|Sans dépendance|
|Type de validation|Manuel|
|Validation|La documentation relative au processus d’envoi et de réception des rapports de vulnérabilité pour les appareils certifiés sera examinée.|
|Ressources||


---
|Nom|SecuredCore.Encryption.TLS|
|:---|:---|
|Statut|Obligatoire|
|Description|L’objectif du test est de valider la prise en charge des versions et des suites de chiffrement TLS requises.|
|Disponibilité cible|2021|
|S'applique à|N’importe quel appareil|
|Système d’exploitation|Sans dépendance|
|Type de validation|Manuel/outils|
Validation|Appareil à valider par le biais de l’ensemble d’outils pour garantir que l’appareil prend en charge au minimum la version TLS 1.2 et qu’il prend en charge les suites de chiffrement TLS requises suivantes.<ul><li>TLS_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_RSA_WITH_AES_128_CBC_SHA256</li><li>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256</li><li>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256</li><li>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256</li></ul>|
|Ressources| [Prise en charge du protocole TLS dans IoT Hub](../iot-hub/iot-hub-tls-support.md) <br /> [Suites de chiffrement TLS dans Windows 10](/windows/win32/secauthn/tls-cipher-suites-in-windows-10-v1903) |

---
|Nom|SecuredCore.Protection.SignedUpdates|
|:---|:---|
|Statut|Obligatoire|
|Description|L’objectif du test est de valider que les mises à jour doivent être signées.|
|Disponibilité cible|2021|
|S'applique à|N’importe quel appareil|
|Système d’exploitation|Sans dépendance|
|Type de validation|Manuel/outils|
|Validation|Appareil à valider par le biais de l’ensemble d’outils pour s’assurer que les mises à jour du système d’exploitation, des pilotes, des logiciels d’application, des bibliothèques, des packages et du microprogramme ne seront pas appliquées si elles ne sont pas correctement signées et validées.
|Ressources||

---
|Nom|SecuredCore.Firmware.SecureBoot|
|:---|:---|
|Statut|Obligatoire|
|Description|L’objectif du test est de valider l’intégrité du démarrage de l’appareil.|
|Disponibilité cible|2021|
|S'applique à|N’importe quel appareil|
|Système d’exploitation|Sans dépendance|
|Type de validation|Manuel/outils|
|Validation|Appareil à valider par le biais de l’ensemble d’outils pour garantir que les signatures du microprogramme et du noyau sont validées à chaque démarrage de l’appareil. <ul><li>UEFI : Le démarrage sécurisé est activé</li><li>Uboot : Le démarrage vérifié est activé</li></ul> </br> </br>Remarque : la version préliminaire de juin 2021 vérifie uniquement l’existence d’UEFI.|
|Ressources||

---
|Nom|SecuredCore.Protection.CodeIntegrity|
|:---|:---|
|Statut|Obligatoire|
|Description|L’objectif de ce test est de valider que l’intégrité du code est disponible sur cet appareil.|
|Disponibilité cible|2021|
|S'applique à|N’importe quel appareil|
|Système d’exploitation|Sans dépendance|
|Type de validation|Manuel/outils|
|Validation|Appareil à valider par le biais de l’ensemble d’outils pour garantir que l’intégrité du code est activée. </br> Windows : HVCI </br> Linux : dm-verity et IMA|
|Ressources||

---
|Nom|SecuredCore.Protection.NetworkServices|
|:---|:---|
|Statut|Obligatoire|
|Description|L’objectif du test est de valider que les applications qui acceptent des entrées du réseau ne s’exécutent pas avec des privilèges élevés.|
|Disponibilité cible|2021|
|S'applique à|N’importe quel appareil|
|Système d’exploitation|Sans dépendance|
|Type de validation|Manuel/outils|
|Validation|Appareil à valider par le biais de l’ensemble d’outils pour s’assurer que les services qui acceptent les connexions réseau ne sont pas exécutés avec les privilèges SYSTEM ou racine.|
|Ressources||

---
|Nom|SecuredCore.Protection.Baselines|
|:---|:---|
|Statut|Obligatoire|
|Description|L’objectif du test est de valider que le système est conforme à une configuration de sécurité de base.|
|Disponibilité cible|2021|
|S'applique à|N’importe quel appareil|
|Système d’exploitation|Sans dépendance|
|Type de validation|Manuel/outils|
|Validation|Appareil à valider par le biais de l’ensemble d’outils pour s’assurer que les points de référence des configurations de système IoT Defender pour le cloud ont été exécutés.|
|Ressources| https://techcommunity.microsoft.com/t5/microsoft-security-baselines/bg-p/Microsoft-Security-Baselines <br> https://www.cisecurity.org/cis-benchmarks/ |

---
|Nom|SecuredCore.Firmware.Protection|
|:---|:---|
|Statut|Obligatoire|
|Description|L’objectif du test est de s’assurer que l’appareil dispose d’atténuations appropriées par rapport aux menaces de sécurité du microprogramme.|
|Disponibilité cible|2021|
|S'applique à|N’importe quel appareil|
|Système d’exploitation|Sans dépendance|
|Type de validation|Manuel/outils|
|Validation|Appareil à valider par le biais de l’ensemble d’outils pour confirmer qu’il est protégé contre les menaces de sécurité du microprogramme par l’une des approches suivantes : <ul><li>Atténuations du mode d’administration DRTM + UEFI</li><li>Renforcement du mode d’administration DRTM + UEFI</li><li>Microprogramme approuvé qui effectue le renforcement du microprogramme SRTM + runtime</li></ul> |
|Ressources| https://trustedcomputinggroup.org/ |

---
|Nom|SecuredCore.Firmware.Attestation|
|:---|:---|
|Statut|Obligatoire|
|Description|L’objectif du test est de vérifier que l’appareil peut attester à distance auprès du service Microsoft Azure Attestation.|
|Disponibilité cible|2021|
|S'applique à|N’importe quel appareil|
|Système d’exploitation|Sans dépendance|
|Type de validation|Manuel/outils|
|Validation|Appareil à valider par le biais de l’ensemble d’outils pour s’assurer que les journaux de démarrage de la plateforme et les mesures de l’activité de démarrage peuvent être collectés et attestés à distance auprès du service Microsoft Azure Attestation.|
|Ressources| [Microsoft Azure Attestation](../attestation/index.yml) |

---
|Nom|SecuredCore.Hardware.MemoryProtection|
|:---|:---|
|Statut|Obligatoire|
|Description|L’objectif du test est de valider que l’accès direct à la mémoire (DMA) n’est pas activé sur les ports accessibles de l’extérieur.|
|Disponibilité cible|2021|
|S'applique à|N’importe quel appareil|
|Système d’exploitation|Sans dépendance|
|Type de validation|Manuel/outils|
|Validation|Si des ports externes compatibles avec le DMA existent sur l’appareil, l’ensemble d’outils doit valider l’activation et la configuration d’IOMMU ou de SMMU pour ces ports.|
|Ressources||

---
|Nom|SecuredCore.Protection.Debug|
|:---|:---|
|Statut|Obligatoire|
|Description|L’objectif du test est de valider que la fonctionnalité de débogage sur l’appareil est désactivée.|
|Disponibilité cible|2021|
|S'applique à|N’importe quel appareil|
|Système d’exploitation|Sans dépendance|
|Type de validation|Manuel/outils|
|Validation|Appareil à valider par le biais de l’ensemble d’outils pour garantir que la fonctionnalité de débogage requiert une autorisation pour être activée.|
|Ressources||
