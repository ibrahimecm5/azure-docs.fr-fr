---
author: kengaderdus
ms.service: active-directory-b2c
ms.topic: include
ms.date: 10/11/2021
ms.author: kengaderdus
ms.openlocfilehash: 18ce90a5c108dc6ff1b6a903e6fe113d33ad4797
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132179508"
---
> [!IMPORTANT]
> Les étapes suivantes vous montrent comment créer vos fichiers XML de stratégie personnalisés. Nous vous recommandons d’utiliser un exemple de stratégie personnalisée de **[vérification des e-mails personnalisés](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol/policy)** disponible sur GitHub. `DisplayControl_TrustFrameworkExtensions.xml` utilise `TrustFrameworkExtensions.xml` comme fichier de base, veillez donc à inclure les fichiers `TrustFrameworkBase.xml`, `TrustFrameworkLocalization.xml` et `TrustFrameworkExtensions.xml` du [pack de démarrage](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack) **SocialAndLocalAccounts** dans votre stratégie.