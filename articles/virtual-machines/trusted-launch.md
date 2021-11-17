---
title: Lancement fiable pour les machines virtuelles Azure
description: Découvrez le lancement fiable pour les machines virtuelles Azure.
author: khyewei
ms.author: khwei
ms.service: virtual-machines
ms.subservice: trusted-launch
ms.topic: conceptual
ms.date: 10/26/2021
ms.reviewer: cynthn
ms.custom: template-concept; references_regions
ms.openlocfilehash: 03bbb681c61f28c2b4fbed580094fd8f47017de0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131466768"
---
# <a name="trusted-launch-for-azure-virtual-machines"></a>Lancement fiable pour les machines virtuelles Azure

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles

Azure propose le lancement fiable pour améliorer de manière fluide la sécurité des machines virtuelles de [2e génération](generation-2.md). Le lancement fiable protège contre les techniques d’attaque avancées et persistantes. Le lancement fiable se compose de plusieurs technologies d’infrastructure coordonnées qui peuvent être activées indépendamment. Chaque technologie offre une couche de défense supplémentaire contre les menaces sophistiquées.

> [!IMPORTANT]
> Le lancement fiable requiert la création de nouvelles machines virtuelles. Vous ne pouvez pas activer le lancement fiable sur les machines virtuelles existantes qui ont été créées initialement sans cette fonctionnalité.



## <a name="benefits"></a>Avantages 

- Déployer en toute sécurité des machines virtuelles avec des chargeurs de démarrage, des noyaux de système d’exploitation et des pilotes vérifiés.
- Protéger efficacement les clés, les certificats et les secrets dans les machines virtuelles.
- Obtenir des insights et avoir confiance en l’intégrité de toute la chaîne de démarrage.
- S’assurer que les charges de travail sont approuvées et vérifiables.

## <a name="limitations"></a>Limites

**Prise en charge de la taille de machine virtuelle** :
- Série B
- Séries Dav4 et Dasv4
- série DCsv2
- Séries Dv4, Dsv4, Dsv3 et Dsv2
- Séries Ddv4 et Ddsv4
- Série Fsv2
- Séries Eav4 et Easv4
- Séries Ev4, Esv4 et Esv3
- Séries Edv4 et Edsv4
- Série Lsv2

**Prise en charge du système d’exploitation** :
- Red Hat Enterprise Linux 8.3
- SUSE 15 SP2
- Ubuntu 20.04 LTS
- Ubuntu 18.04 LTS
- Debian 11
- CentOS 8.4
- Oracle Linux 8.3
- CBL-Mariner
- Windows Server 2022
- Windows Server 2019
- Windows Server 2016
- Windows 11 Pro
- Windows 11 Entreprise
- Windows 11 Entreprise multisession
- Windows 10 Professionnel
- Windows 10 Entreprise
- Windows 10 Entreprise multisession

**Régions** : 
- Toutes les régions publiques

**Tarifs** : aucun coût supplémentaire par rapport à la tarification existante des machines virtuelles.

**Les fonctionnalités suivantes ne sont pas prises en charge** :
- Sauvegarde
- Azure Site Recovery
- Galerie Azure Compute Gallery (anciennement Shared Image Gallery)
- Disque de système d’exploitation éphémère
- Disque partagé
- Disque Ultra
- Image managée
- Azure Dedicated Host 
- Virtualisation imbriquée

## <a name="secure-boot"></a>Démarrage sécurisé

À la racine du lancement fiable, on trouve le démarrage sécurisé pour votre machine virtuelle. Ce mode, qui est implémenté dans le microprogramme de la plateforme, protège contre l’installation de rootkits et kits de démarrage basés sur des programmes malveillants. Le démarrage sécurisé garantit que seuls les systèmes d’exploitation et pilotes signés peuvent démarrer. Il établit une « racine de confiance » pour la pile logicielle de votre machine virtuelle. Quand le démarrage sécurisé est activé, tous les composants de démarrage du système d’exploitation (chargeur de démarrage, noyau, pilotes de noyau) doivent être signés par des éditeurs approuvés. Le démarrage sécurisé est pris en charge par Windows et certaines distributions Linux. Si le démarrage sécurisé ne parvient pas à authentifier que l’image a été signée par un éditeur approuvé, la machine virtuelle n’est pas autorisée à démarrer. Pour plus d'informations, voir [Démarrage sécurisé](/windows-hardware/design/device-experiences/oem-secure-boot).

## <a name="vtpm"></a>vTPM

Le lancement fiable introduit également le module vTPM pour les machines virtuelles Azure. Il s’agit d’une version virtualisée d’un [module de plateforme sécurisée (TPM)](/windows/security/information-protection/tpm/trusted-platform-module-overview) matériel, conforme à la spécification TPM2.0. Il sert de coffre sécurisé dédié pour les clés et les mesures. Le lancement fiable permet à votre machine virtuelle d’utiliser sa propre instance TPM dédiée, s’exécutant dans un environnement sécurisé en dehors de la portée de toute machine virtuelle. Le module vTPM permet d’effectuer l’[attestation](/windows/security/information-protection/tpm/tpm-fundamentals#measured-boot-with-support-for-attestation) en mesurant la chaîne de démarrage complète de votre machine virtuelle (UEFI, système d’exploitation, système et pilotes). 

Le lancement fiable utilise le module vTPM pour effectuer l’attestation distante par le cloud. Il est utilisé pour les contrôles d’intégrité de la plateforme et pour permettre de prendre des décisions basées sur la confiance. En guise de contrôle d’intégrité, le lancement fiable peut certifier par chiffrement que votre machine virtuelle a démarré correctement. Si le processus échoue, par exemple parce que votre machine virtuelle exécute un composant non autorisé, Azure Security Center émet des alertes d’intégrité. Les alertes incluent des détails sur les composants concernés par l’échec des contrôles d’intégrité.

## <a name="virtualization-based-security"></a>Sécurité basée sur la virtualisation

La [sécurité basée sur la virtualisation](/windows-hardware/design/device-experiences/oem-vbs) (VBS) utilise l’hyperviseur pour créer une région sécurisée et isolée de la mémoire. Windows utilise ces régions pour exécuter diverses solutions de sécurité avec une protection renforcée contre les vulnérabilités et les attaques malveillantes. Le lancement fiable vous permet d’activer l’intégrité du code de l’hyperviseur (HVCI) et Windows Defender Credential Guard.

La fonctionnalité HVCI constitue une atténuation puissante des risques système et protège les processus en mode noyau de Windows contre l’injection et l’exécution de code malveillant ou non vérifié. Elle vérifie les fichiers binaires et les pilotes en mode noyau avant leur exécution, ce qui empêche le chargement des fichiers non signés dans la mémoire. Cela garantit que du code exécutable de ce type ne puisse pas être modifié après avoir été autorisé à charger. Pour plus d’informations sur les fonctionnalités VBS et HVCI, consultez [Virtualization Based Security (VBS) et Hypervisor Enforced Code Integrity (HVCI)](https://techcommunity.microsoft.com/t5/windows-insider-program/virtualization-based-security-vbs-and-hypervisor-enforced-code/m-p/240571).

Avec le lancement fiable et la sécurité basée sur la virtualisation (VBS), vous pouvez activer Windows Defender Credential Guard. Cette fonctionnalité isole et protège les secrets afin que seuls les logiciels système privilégiés puissent y accéder. Cela permet d’empêcher l’accès non autorisé aux secrets et le vol des informations d’identification, comme les attaques de type Pass-the-Hash (PtH). Pour plus d’informations, consultez [Credential Guard](/windows/security/identity-protection/credential-guard/credential-guard).


## <a name="security-center-integration"></a>Intégration Security Center

Le lancement fiable est intégré à Azure Security Center pour garantir que vos machines virtuelles sont correctement configurées. Azure Security Center évalue continuellement les machines virtuelles compatibles et émet des recommandations pertinentes.

- **Recommandation pour activer le démarrage sécurisé** : cette recommandation s’applique uniquement aux machines virtuelles qui prennent en charge le lancement fiable. Azure Security Center identifie les machines virtuelles qui peuvent activer la fonctionnalité de démarrage sécurisé, mais qui ont cette fonctionnalité désactivée. Le système émet une recommandation de faible gravité pour suggérer de l’activer.
- **Recommandation pour activer vTPM** : si vTPM est activé sur votre machine virtuelle, Azure Security Center peut l’utiliser pour effectuer une attestation d’invité et identifier les modèles de menaces avancés. Si Azure Security Center identifie des machines virtuelles qui prennent en charge le lancement fiable et dont le module vTPM est désactivé, il émet une recommandation de faible gravité pour suggérer de l’activer. 
- **Recommandation pour installer l’extension d’attestation d’invité** : si votre machine virtuelle dispose d’un démarrage sécurisé et d’un vTPM activé, mais que l’extension d’attestation d’invité n’est pas installée, Azure Security Center émet une recommandation de faible gravité pour installer l’extension d’attestation d’invité. Cette extension permet à Azure Security Center d’attester et de surveiller de manière proactive l’intégrité du démarrage de vos machines virtuelles. L’intégrité du démarrage est attestée via une attestation à distance.  
- **Évaluation de l’intégrité de l’attestation** : si un vTPM est activé et une extension d’attestation installée sur votre machine virtuelle, Azure Security Center peut confirmer à distance que votre machine virtuelle a démarré de manière saine. C’est ce que l’on appelle l’attestation distante. Azure Security Center émet une évaluation, indiquant l’état de l’attestation distante.

## <a name="azure-defender-integration"></a>Intégration d’Azure Defender

Si vos machines virtuelles sont correctement configurées avec la fonctionnalité de lancement fiable, Azure Defender peut détecter des problèmes d’intégrité des machines virtuelles et vous en informer.

- **Alerte d’échec d’attestation de la machine virtuelle** : Azure Defender effectue régulièrement une attestation sur vos machines virtuelles. Cela se produit également après le démarrage de votre machine virtuelle. Si l’attestation échoue, une alerte de gravité moyenne est déclenchée.
    L’attestation de la machine virtuelle peut échouer pour les raisons suivantes :
    - Les informations attestées, qui comprennent un journal de démarrage, diffèrent d’une ligne de base approuvée. Cela peut indiquer que des modules non approuvés ont été chargés et que le système d’exploitation peut être compromis.
    - Il n’a pas été possible de vérifier que la déclaration d’attestation provient du module vTPM de la machine virtuelle attestée. Cela peut indiquer qu’un logiciel malveillant est présent et qu’il intercepte le trafic vers vTPM.
    
    > [!NOTE]
    >  Cette alerte est disponible pour les machines virtuelles sur lesquelles vTPM est activé et l’extension d’attestation installée. Le démarrage sécurisé doit être activé pour que l’attestation réussisse. L’attestation échoue si le démarrage sécurisé est désactivé. Si vous devez désactiver le démarrage sécurisé, vous pouvez supprimer cette alerte pour éviter les faux positifs.

- **Alerte pour le module noyau Linux non approuvé** : si le lancement fiable avec démarrage sécurisé est activé, une machine virtuelle peut démarrer même si un pilote du noyau échoue à la validation et que son chargement est interdit. Dans ce cas, Azure Defender émet une alerte de faible gravité. Bien qu’il n’y ait pas de menace immédiate car le pilote non approuvé n’a pas été chargé, ces événements doivent être examinés. Tenez compte des éléments suivants :
    - Quel pilote du noyau a échoué ? Est-ce que je connais ce pilote ? Est-ce que je m’attends à ce qu’il soit chargé ?
    - S’agit-il de la version exacte du pilote attendu ? Les fichiers binaires du pilote sont-ils intacts ? S’il s’agit d’un pilote tiers, le fournisseur a-t-il réussi les tests de conformité du système d’exploitation pour obtenir la signature ?


## <a name="faq"></a>Forum aux questions

Questions fréquentes sur le lancement fiable.

### <a name="why-should-i-use-trusted-launch-what-does-trusted-launch-guard-against"></a>Pourquoi utiliser le lancement fiable ? De quoi protège le lancement fiable ?

Le lancement fiable protège contre les kits de démarrage, les rootkits et les logiciels malveillants au niveau du noyau. Ce type de programmes malveillants sophistiqués s’exécutent en mode noyau et restent masqués pour les utilisateurs. Par exemple :
- Rootkits de microprogramme : ces kits remplacent le microprogramme du BIOS de la machine virtuelle, ce qui permet au rootkit de démarrer avant le système d’exploitation. 
- Kits de démarrage : ces kits remplacent le chargeur de démarrage du système d’exploitation afin que la machine virtuelle charge le kit de démarrage avant le système d’exploitation.
- Rootkits de noyau : ces kits remplacent une partie du noyau du système d’exploitation afin que le rootkit puisse démarrer automatiquement lors du chargement du système d’exploitation.
- Rootkits de pilote : ces kits prétendent être l’un des pilotes approuvés que le système d’exploitation utilise pour communiquer avec les composants de la machine virtuelle.

### <a name="what-are-the-differences-between-secure-boot-and-measured-boot"></a>Quelles sont les différences entre le démarrage sécurisé et le démarrage mesuré ?

Dans une chaîne de démarrage sécurisée, chaque étape du processus de démarrage vérifie la signature de chiffrement pour les étapes suivantes. Par exemple, le BIOS vérifie une signature sur le chargeur et le chargeur vérifie les signatures sur tous les objets de noyau qu’il charge, et ainsi de suite. Si l’un des objets est compromis, la signature ne correspond pas et la machine virtuelle ne démarre pas. Pour plus d'informations, voir [Démarrage sécurisé](/windows-hardware/design/device-experiences/oem-secure-boot). Le démarrage mesuré n’interrompt pas le processus de démarrage, il mesure ou calcule le hachage des objets suivants dans la chaîne et stocke les hachages dans les registres de configuration de plateforme (PCR) sur le module vTPM. Les enregistrements de démarrage mesuré sont utilisés pour l’analyse de l’intégrité du démarrage.

### <a name="what-happens-when-an-integrity-fault-is-detected"></a>Que se passe-t-il quand une erreur d’intégrité est détectée ?

La présence de menaces avancées est surveillée dans le lancement fiable pour les machines virtuelles Azure. Si de telles menaces sont détectées, une alerte est déclenchée. Les alertes sont disponibles uniquement dans le [Niveau Standard](../security-center/security-center-pricing.md) d’Azure Security Center.
Azure Security Center effectue régulièrement une attestation. Si l’attestation échoue, une alerte de gravité moyenne est déclenchée. L’attestation de lancement fiable peut échouer pour les raisons suivantes : 
- Les informations attestées, qui comprennent un journal TCB (Trusted Computing Base), diffèrent d’une ligne de base approuvée (comme quand le démarrage sécurisé est activé). Cela peut indiquer que des modules non approuvés ont été chargés et que le système d’exploitation peut être compromis.
- Il n’a pas été possible de vérifier que la déclaration d’attestation provient du module vTPM de la machine virtuelle attestée. Cela peut indiquer qu’un logiciel malveillant est présent et qu’il intercepte le trafic vers le module TPM. 
- L’extension d’attestation sur la machine virtuelle ne répond pas. Cela peut indiquer une attaque par déni de service par un programme malveillant ou un administrateur du système d’exploitation.

  
### <a name="how-does-trusted-launch-compared-to-hyper-v-shielded-vm"></a>Quelles sont les performances du lancement fiable par rapport à la machine virtuelle dotée d’une protection maximale Hyper-V ?

La machine virtuelle dotée d’une protection maximale Hyper-V est actuellement disponible uniquement sur Hyper-V. La [machine virtuelle dotée d’une protection maximale Hyper-V](/windows-server/security/guarded-fabric-shielded-vm/guarded-fabric-and-shielded-vms) est généralement déployée conjointement avec l’infrastructure protégée. Une infrastructure protégée est constituée d’un service Guardian hôte (SGH), d’un ou plusieurs hôtes protégés et d’un ensemble de machines virtuelles dotées d’une protection maximale. Les machines virtuelles dotées d’une protection maximale Hyper-V sont destinées à être utilisées dans des structures où les données et l’état de la machine virtuelle doivent être protégés des administrateurs de l’infrastructure et des logiciels non approuvés qui pourraient s’exécuter sur les hôtes Hyper-V. En revanche, le lancement fiable peut être déployé en tant que machine virtuelle autonome ou en tant que groupes de machines virtuelles identiques sur Azure sans déploiement ni gestion supplémentaires du service SGH. Toutes les fonctionnalités du lancement fiable peuvent être activées via une simple modification dans le code de déploiement ou à l’aide d’une case à cocher sur le portail Azure.  

### <a name="how-can-i-convert-existing-vms-to-trusted-launch"></a>Comment convertir des machines virtuelles existantes au lancement fiable ?

Pour les machines virtuelles de 2e génération, le chemin de migration pour la conversion au lancement fiable est prévu pour la disponibilité générale (GA).

### <a name="what-is-vm-guest-state-vmgs"></a>Qu’est-ce que l’état invité de machine virtuelle (VMGS) ?  

L’état invité de machine virtuelle (VMGS) est spécifique d’une machine virtuelle de lancement fiable. Il s’agit d’un blob géré par Azure, qui contient les bases de données de signature de démarrage sécurisé UEFI (Unified Extensible Firmware Interface) et d’autres informations de sécurité. Le cycle de vie du blob VMGS est lié à celui du disque du système d’exploitation.  

## <a name="next-steps"></a>Étapes suivantes

Déployez une [machine virtuelle avec lancement fiable à l’aide du portail](trusted-launch-portal.md).
