---
title: Gestionnaire de sécurité/runtime de module Azure IoT Edge – Azure IoT Edge
description: Gère le principe de sécurité d’appareil IoT Edge et l’intégrité des services de sécurité.
services: iot-edge
keywords: sécurité, élément sécurisé, enclave, TEE, IoT Edge
author: kgremban
ms.author: kgremban
ms.reviewer: eustacea
ms.date: 09/17/2021
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: a7c714a818282ef42905e711c9f016c2e9c3e31e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286409"
---
# <a name="azure-iot-edge-security-manager"></a>Gestionnaire de sécurité Azure IoT Edge

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Le Gestionnaire de sécurité Azure IoT Edge est un cœur de sécurité bien délimité de protection de l’appareil IoT Edge et tous ses composants en faisant abstraction du matériel de silicium de sécurité. Le Gestionnaire de sécurité, point focal du renforcement de la sécurité, fournit un point d’intégration technologique aux fabricants OEM.

<!--1.1-->
:::moniker range="iotedge-2018-06"
Le gestionnaire de sécurité fait abstraction du matériel de silicium de sécurité sur un appareil IoT Edge.

![Gestionnaire de sécurité Azure IoT Edge](media/edge-security-manager/iot-edge-security-manager.png)
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
Le gestionnaire de sécurité fait abstraction du matériel de silicium de sécurité sur un appareil IoT Edge et fournit une infrastructure d’extensibilité pour des services de sécurité supplémentaires.

<!-- add new graphic here -->
:::moniker-end

Le gestionnaire de sécurité IoT Edge vise à protéger l’intégrité de l’appareil IoT Edge et toutes les opérations logicielles inhérentes. Le gestionnaire de sécurité fait passer l’approbation de la racine matérielle sous-jacente du matériel de confiance (s’il est disponible) pour démarrer le runtime IoT Edge et superviser les opérations en cours.  Le gestionnaire de sécurité IoT Edge est un logiciel qui fonctionne avec des processeurs en silicium sécurisés (si disponibles) pour offrir les garanties de sécurité les plus élevées possible.  

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
En outre, le gestionnaire de sécurité IoT Edge fournit une infrastructure sécurisée pour les extensions de service de sécurité par le biais de modules au niveau de l’hôte. Ces services incluent la surveillance et les mises à jour de sécurité qui nécessitent des agents à l’intérieur de l’appareil avec un accès privilégié à certains composants de l’appareil. L’infrastructure d’extensibilité garantit que ces intégrations maintiennent de manière cohérente la sécurité globale du système.
:::moniker-end

Les responsabilités du gestionnaire de sécurité IoT Edge sont notamment :

<!--1.1-->
:::moniker range="iotedge-2018-06"

* Démarrer l’appareil Azure IoT Edge.
* Contrôler l’accès à la racine matérielle d’appareil de confiance via des services de notaire.
* Surveiller l’intégrité des opérations IoT Edge lors de l’exécution.
* Recevoir la délégation de confiance du module de sécurité du matériel (HSM).
* Approvisionner l’identité d’appareil et gérer la transition de confiance, le cas échéant.
* Héberger et protéger les composants de l’appareil de services cloud comme Service Device Provisioning.
* Approvisionner des modules IoT Edge avec des identités uniques.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"

* Démarrer l’appareil Azure IoT Edge.
* Contrôler l’accès à la racine matérielle d’appareil de confiance via des services de notaire.
* Surveiller l’intégrité des opérations IoT Edge lors de l’exécution.
* Approvisionner l’identité d’appareil et gérer la transition de confiance, le cas échéant.
* Assurer le fonctionnement sécurisé des agents clients pour les services, notamment Device Update pour IoT Hub et Microsoft Defender pour IoT.
:::moniker-end

Le gestionnaire de sécurité IoT Edge est constitué de trois composants :

<!--1.1-->
:::moniker range="iotedge-2018-06"

* Démon de sécurité IoT Edge
* L’abstraction calque de la plateforme du module de sécurité matérielle (PAL HSM)
* Une racine matérielle de silicium de confiance ou HSM (facultatif, mais fortement recommandée)
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"

* Le runtime de module IoT Edge
* Des abstractions de module de sécurité matérielle (HSM) par le biais d’implémentations standard telles que PKCS#11 et Trusted Platform Module (TPM)
* Une racine matérielle de silicium de confiance ou HSM (facultatif, mais fortement recommandée)

## <a name="changes-in-version-12"></a>Changements dans la version 1.2

Dans les versions 1.0 et 1.1 d’IoT Edge, un composant appelé **démon de sécurité** était responsable des opérations de sécurité logique du gestionnaire de sécurité. Dans la mise à jour de la version 1.2, plusieurs responsabilités clés ont été déléguées au sous-système de sécurité [Azure IoT Identity Service](https://azure.github.io/iot-identity-service/). Une fois que ces tâches liées à la sécurité ont été supprimées du démon de sécurité, le nom de ce dernier n’était plus pertinent. Pour mieux refléter le travail effectué par ce composant dans les versions 1.2 et ultérieures, nous l’avons renommé **runtime de module**.
:::moniker-end

<!--1.1-->
:::moniker range="iotedge-2018-06"
## <a name="the-iot-edge-security-daemon"></a>Démon de sécurité IoT Edge

Le démon de sécurité IoT Edge est responsable des opérations de sécurité logique du gestionnaire de sécurité. Il représente une partie significative du TCB (Trusted Computing Base) de l’appareil IoT Edge.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
## <a name="the-iot-edge-module-runtime"></a>Le runtime de module IoT Edge

Le runtime de module IoT Edge délègue l’approbation du sous-système de sécurité [Azure IoT Identity Service](https://azure.github.io/iot-identity-service/) pour protéger l’environnement d’exécution du conteneur IoT Edge. Le runtime de module est responsable des opérations de sécurité logique du gestionnaire de sécurité. Il représente une partie significative du TCB (Trusted Computing Base) de l’appareil IoT Edge. Le runtime de module utilise les services de sécurité du service d’identité IoT, qui est lui-même renforcé par le module de sécurité matérielle (HSM) choisi par le fabricant de l’appareil. Nous vous recommandons fortement d’utiliser des modules HSM pour renforcer la sécurité des appareils.
:::moniker-end

### <a name="design-principles"></a>Principes de conception

IoT Edge suit deux principes de base : optimiser l’intégrité opérationnelle et réduire le ballonnement et l’évolution.

#### <a name="maximize-operational-integrity"></a>Optimiser l’intégrité opérationnelle

<!--1.1-->
:::moniker range="iotedge-2018-06"
Le démon de sécurité IoT Edge fonctionne avec l’intégrité la plus élevée possible dans la fonctionnalité de défense de n’importe quelle racine donnée du matériel de confiance. Avec l’intégration appropriée, la racine du matériel de confiance mesure et surveille le démon de sécurité statiquement et lors de l’exécution pour résister aux falsifications.
:::moniker-end

<!--1.1-->
:::moniker range=">=iotedge-2020-11"
Le runtime de module IoT Edge fonctionne avec l’intégrité la plus élevée possible dans la capacité de défense de n’importe quelle racine donnée du matériel de confiance. Avec l’intégration appropriée, la racine du matériel de confiance mesure et surveille le démon de sécurité statiquement et lors de l’exécution pour résister aux falsifications.
:::moniker-end

L’accès physique malveillant aux appareils est toujours une menace dans IoT. La racine matérielle de confiance joue un rôle important dans la protection de l’intégrité de l’appareil IoT Edge.  La racine matérielle de confiance se présente dans deux versions :

* Éléments sécurisés pour la protection des informations sensibles telles que les secrets et les clés de chiffrement.
* Enclaves sécurisées pour la protection des secrets, comme les clés, et les charges de travail sensibles, comme les modèles Machine Learning confidentiels et les opérations de mesure.

Il existe deux types d’environnements d’exécution pour utiliser la racine matérielle de confiance :

* L’environnement d’exécution standard ou riche (EER) qui s’appuie sur l’utilisation d’éléments sécurisés pour protéger les informations sensibles.
* L’environnement d’exécution approuvé (EEA) qui s’appuie sur l’utilisation de la technologie d’enclave sécurisée pour protéger les informations sensibles et offrir une protection à l’exécution de logiciels.

<!--1.1-->
:::moniker range="iotedge-2018-06"
Pour les appareils qui utilisent des enclaves sécurisées comme racine matérielle de confiance, la logique sensible au sein du démon de sécurité IoT Edge doit se trouver à l’intérieur de l’enclave.  Les parties non sensibles du démon de sécurité peuvent se trouver en dehors de l’environnement d’exécution approuvé.  Dans tous les cas, nous recommandons vivement aux fabricants ODM et aux fabricants OEM d’élargir la confiance à partir de leur HSM pour mesurer et défendre l’intégrité du démon de sécurité IoT Edge au moment du démarrage et de l’exécution.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
Pour les appareils qui utilisent des enclaves sécurisées comme racine matérielle de confiance, la logique sensible au sein du runtime de module IoT Edge doit se trouver à l’intérieur de l’enclave.  Les parties non sensibles du runtime de module peuvent se trouver en dehors de l’environnement d’exécution de confiance.  Dans tous les cas, nous recommandons vivement aux fabricants ODM et aux fabricants OEM d’élargir la confiance à partir de leur HSM pour mesurer et défendre l’intégrité du runtime de module IoT Edge au moment du démarrage et de l’exécution.
:::moniker-end

#### <a name="minimize-bloat-and-churn"></a>Réduire le ballonnement et l’évolution

<!--1.1-->
:::moniker range="iotedge-2018-06"
Un autre principe de base pour le démon de sécurité IoT Edge consiste à réduire l’évolution.  Pour le niveau le plus élevé de confiance, le démon de sécurité IoT Edge peut étroitement s’associer à la racine matérielle de confiance de l’appareil et fonctionner comme du code natif.  Dans ce cas, il est courant de mettre à jour le logiciel IoT Edge par le biais des chemins de mise à jour sécurisés de la racine matérielle de confiance plutôt que par les mécanismes de mise à jour du système d’exploitation, qui peuvent être difficiles.  Le renouvellement de la sécurité est recommandé pour les appareils IoT, mais des exigences de mise à jour excessives ou des charges utiles de mise à jour importantes peuvent étendre la surface d’attaque de nombreuses façons. Par exemple, vous pouvez être tenté d’ignorer certaines mises à jour afin de maximiser la disponibilité de l’appareil. À ce titre, la conception du démon de sécurité IoT Edge est concise, de façon à conserver une petite base informatique de confiance bien isolée qui encourage les mises à jour fréquentes.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
Un autre principe de base du runtime de module IoT Edge consiste à réduire l’évolution.  Pour le niveau le plus élevé de confiance, le runtime de module IoT Edge peut étroitement s’associer à la racine matérielle de confiance de l’appareil et fonctionner comme du code natif.  Dans ce cas, il est courant de mettre à jour le logiciel IoT Edge par le biais des chemins de mise à jour sécurisés de la racine matérielle de confiance plutôt que par les mécanismes de mise à jour du système d’exploitation, qui peuvent être difficiles.  Le renouvellement de la sécurité est recommandé pour les appareils IoT, mais des exigences de mise à jour excessives ou des charges utiles de mise à jour importantes peuvent étendre la surface d’attaque de nombreuses façons. Par exemple, vous pouvez être tenté d’ignorer certaines mises à jour afin de maximiser la disponibilité de l’appareil. À ce titre, la conception du runtime de module IoT Edge est concise, de façon à conserver une petite base informatique de confiance bien isolée qui encourage les mises à jour fréquentes.
:::moniker-end

### <a name="architecture"></a>Architecture

<!--1.1-->
:::moniker range="iotedge-2018-06"

Le démon de sécurité IoT Edge est conçu pour tirer parti de n’importe quelle technologie de racine matérielle de confiance disponible pour le renforcement de la sécurité.  Il permet également un fonctionnement distinct entre un environnement d’exécution Standard/riche (REE) et un environnement d’exécution approuvé (TEE) quand celui-ci est proposé par des technologies matérielles. Les interfaces spécifiques à un rôle permettent aux composants principaux d’IoT Edge d’assurer l’intégrité de l’appareil IoT Edge et de son fonctionnement.

![Architecture du démon de sécurité IoT Edge](media/edge-security-manager/iot-edge-security-daemon.png)
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"

Le runtime de module IoT Edge est conçu pour tirer parti de n’importe quelle technologie de racine matérielle de confiance disponible pour le renforcement de la sécurité.  Il permet également un fonctionnement distinct entre un environnement d’exécution Standard/riche (REE) et un environnement d’exécution approuvé (TEE) quand celui-ci est proposé par des technologies matérielles. Les interfaces spécifiques à un rôle permettent aux composants principaux d’IoT Edge d’assurer l’intégrité de l’appareil IoT Edge et de son fonctionnement.

![Architecture du runtime de module IoT Edge](media/edge-security-manager/iot-edge-module-runtime.png)
:::moniker-end

#### <a name="cloud-interface"></a>Interface Cloud

L’interface cloud permet d’accéder à des services cloud qui complètent la sécurité des appareils.  Par exemple, cette interface permet d’accéder au [Service Device Provisioning](../iot-dps/index.yml) pour la gestion du cycle de vie des identités d’appareil.  

#### <a name="management-api"></a>API de gestion

<!--1.1-->
:::moniker range="iotedge-2018-06"
L’API de gestion est appelée par l’agent IoT Edge lors de la création, du démarrage, de l’arrêt et de la suppression d’un module IoT Edge. Le démon de sécurité stocke les « inscriptions » pour tous les modules actifs. Ces inscriptions associent l’identité d’un module à certaines propriétés du module. Ces propriétés du module sont, par exemple, l’identificateur du processus (pid) en cours d’exécution dans le conteneur ou le code de hachage du contenu du conteneur Docker.

Elles sont utilisées par l’API de charge de travail (décrite ci-dessous) pour vérifier que l’appelant est autorisé à effectuer une action.

L’API de gestion est une API avec privilèges, qui peut être appelée seulement depuis l’agent IoT Edge.  Étant donné que le démon de sécurité IoT Edge amorce et démarre l’agent IoT Edge, il vérifie que l’agent IoT Edge n’a pas été falsifié, puis il peut créer une inscription implicite pour l’agent IoT Edge. Le même processus d’attestation qui utilise des API de charge de travail est utilisé pour restreindre l’accès de l’API de gestion à l’agent IoT Edge uniquement.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
L’API de gestion est appelée par l’agent IoT Edge lors de la création, du démarrage, de l’arrêt et de la suppression d’un module IoT Edge. Le runtime de module stocke les « inscriptions » pour tous les modules actifs. Ces inscriptions associent l’identité d’un module à certaines propriétés du module. Ces propriétés du module sont, par exemple, l’identificateur du processus (pid) en cours d’exécution dans le conteneur ou le code de hachage du contenu du conteneur Docker.

Elles sont utilisées par l’API de charge de travail (décrite ci-dessous) pour vérifier que l’appelant est autorisé à effectuer une action.

L’API de gestion est une API avec privilèges, qui peut être appelée seulement depuis l’agent IoT Edge.  Étant donné que le runtime de module IoT Edge amorce et démarre l’agent IoT Edge, il vérifie que l’agent IoT Edge n’a pas été falsifié, puis il peut créer une inscription implicite pour l’agent IoT Edge. Le même processus d’attestation qui utilise des API de charge de travail est utilisé pour restreindre l’accès de l’API de gestion à l’agent IoT Edge uniquement.
:::moniker-end

#### <a name="container-api"></a>API de conteneur

L’API de conteneur interagit avec le système de conteneur utilisé pour la gestion des modules, comme Moby ou Docker.

#### <a name="workload-api"></a>API de charge de travail

L’API de charge de travail est accessible à tous les modules. Il fournit à un module une preuve d’identité (un jeton signé avec la racine HSM ou un certificat X509) et le bundle de confiance correspondant. Le bundle de confiance contient les certificats d’autorité de certification de tous les autres serveurs que les modules doivent approuver.

<!--1.1-->
:::moniker range="iotedge-2018-06"
Le démon de sécurité IoT Edge utilise un processus d’attestation pour protéger cette API. Quand un module appelle cette API, le démon de sécurité tente de trouver une inscription pour l’identité. En cas de réussite, il utilise les propriétés de l’inscription pour mesurer le module. Si le résultat du processus de mesure correspond à l’inscription, une nouvelle preuve d’identité est générée. Les certificats d’autorité de certification correspondants (bundle de confiance) sont retournés au module.  Le module utilise ce certificat pour vous connecter à IoT Hub, les autres modules, ou démarrer un serveur. Quand la date d’expiration du jeton signé ou du certificat approche, il est de la responsabilité du module de demander un nouveau certificat.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
Le runtime de module IoT Edge utilise un processus d’attestation pour protéger cette API. Quand un module appelle cette API, le runtime de module tente de trouver une inscription pour l’identité. En cas de réussite, il utilise les propriétés de l’inscription pour mesurer le module. Si le résultat du processus de mesure correspond à l’inscription, une nouvelle preuve d’identité est générée. Les certificats d’autorité de certification correspondants (bundle de confiance) sont retournés au module.  Le module utilise ce certificat pour vous connecter à IoT Hub, les autres modules, ou démarrer un serveur. Quand la date d’expiration du jeton signé ou du certificat approche, il est de la responsabilité du module de demander un nouveau certificat.
:::moniker-end

### <a name="integration-and-maintenance"></a>Intégration et maintenance

<!--1.1-->
:::moniker range="iotedge-2018-06"
Microsoft tient à jour le code principal pour le [démon de sécurité IoT Edge sur GitHub](https://github.com/Azure/iotedge/tree/release/1.1/edgelet).
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
Microsoft gère la principale codebase du [runtime de module IoT Edge](https://github.com/Azure/iotedge/tree/master/edgelet) et du [service d’identité Azure IoT](https://github.com/Azure/iot-identity-service) sur GitHub.

Lorsque vous lisez la codebase IoT Edge, n’oubliez pas que le **runtime de module** a évolué à partir du **démon de sécurité**. La codebase peut encore contenir des références au démon de sécurité.
:::moniker-end

#### <a name="installation-and-updates"></a>Installation et mises à jour

<!--1.1-->
:::moniker range="iotedge-2018-06"
L’installation et les mises à jour du démon de sécurité IoT Edge sont gérées via le système de gestion des packages du système d’exploitation. Les appareils IoT Edge avec racine matérielle de confiance doivent fournir un renforcement de l’intégrité du démon en gérant le cycle de vie de celui-ci via le démarrage sécurisé et les systèmes de gestion des mises à jour. Les fabricants d’appareils doivent explorer ces voies en fonction des capacités de leurs appareils respectifs.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
L’installation et les mises à jour du runtime de module IoT Edge sont gérées via le système de gestion des packages du système d’exploitation. Les appareils IoT Edge avec racine matérielle de confiance doivent fournir un renforcement supplémentaire de l’intégrité du runtime de module en gérant le cycle de vie de celui-ci via le démarrage sécurisé et les systèmes de gestion des mises à jour. Les fabricants d’appareils doivent explorer ces voies en fonction des capacités de leurs appareils respectifs.
:::moniker-end

#### <a name="versioning"></a>Contrôle de version

<!--1.1-->
:::moniker range="iotedge-2018-06"
Le runtime IoT Edge effectue le suivi et signale la version du démon de sécurité IoT Edge. La version est signalée comme l’attribut *runtime.platform.version* de la propriété rapportée du module d’agent IoT Edge.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
Le runtime IoT Edge suit et signale la version du runtime de module IoT Edge. La version est signalée comme l’attribut *runtime.platform.version* de la propriété rapportée du module d’agent IoT Edge.
:::moniker-end

## <a name="hardware-security-module"></a>Module de sécurité matériel

<!--1.1-->
:::moniker range="iotedge-2018-06"
L’abstraction calque de la plateforme du module de sécurité matérielle (PAL HSM) fait abstraction de toute la racine du matériel de confiance afin d’isoler le développeur ou l’utilisateur d’IoT Edge de leurs complexités.  Il comprend une combinaison d’API et de procédures de communications entre domaines, par exemple la communication entre un environnement d’exécution standard et une enclave sécurisée.  L’implémentation réelle de la PAL HSM varie selon le matériel sécurisé en cours d’utilisation. Son existence permet d’utiliser n’importe quel processeur de silicium sécurisé.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
Le gestionnaire de sécurité IoT Edge implémente les normes d’interface Trusted Platform Module et PKCS#11 pour l’intégration de modules de sécurité matériels (HSM). Grâce à ces normes, pratiquement tous les modules HSM, y compris ceux dotés d’interfaces propriétaires, peuvent être intégrés. Nous vous recommandons fortement d’utiliser des modules HSM pour renforcer la sécurité.
:::moniker-end

## <a name="secure-silicon-root-of-trust-hardware"></a>Racine de silicium sécurisé du matériel de confiance

Le silicium sécurisé est nécessaire à l’approbation d’ancrage dans le matériel de l’appareil IoT Edge.  Les siliciums sécurisés se présentent sous diverses formes : Trusted Platform Module (TPM), embedded Secure Element (eSE), Arm TrustZone, Intel SGX et technologies personnalisées de silicium sécurisé.  L’utilisation de la racine de confiance « silicium » sécurisée sur les appareils est recommandée, en raison des menaces associées à l’accessibilité physique des appareils IoT.

Le gestionnaire de sécurité IoT Edge a pour but d’identifier et d’isoler les composants chargés d’assurer la sécurité et l’intégrité de la plateforme Azure IoT Edge pour un renforcement personnalisé. Les tierces parties, telles que les fabricants d’appareils, doivent utiliser les fonctionnalités de sécurité personnalisées qui sont disponibles pour les composants matériels de leurs appareils.  

Découvrez comment renforcer le gestionnaire de sécurité d’Azure IoT avec la technologie Trusted Platform Module (TPM) à l’aide de TPM logiciels ou virtuels :  

Créer et provisionner un [appareil IoT Edge à l’aide d’un TPM virtuel sur une machine virtuelle Linux](how-to-provision-devices-at-scale-linux-tpm.md)

<!--1.1-->
:::moniker range="iotedge-2018-06"
Créer et provisionner un [appareil IoT Edge avec un TPM simulé sur Windows](how-to-provision-devices-at-scale-windows-tpm.md)
:::moniker-end

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la sécurisation de vos appareils IoT Edge, lisez les billets de blog suivants :

* [Sécurisation de la périphérie intelligente](https://azure.microsoft.com/blog/securing-the-intelligent-edge/)
* [Blueprint pour résoudre en toute sécurité l’insaisissable approvisionnement zero-touch des appareils IoT à grande échelle](https://azure.microsoft.com/blog/the-blueprint-to-securely-solve-the-elusive-zerotouch-provisioning-of-iot-devices-at-scale/)
* [Résolution de la sécurité des appareils IoT à grande échelle grâce à des normes](https://azure.microsoft.com/blog/solving-iot-device-security-at-scale-through-standards/)
