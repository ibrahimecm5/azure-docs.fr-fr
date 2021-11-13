---
title: Nouveautés des justificatifs vérifiables Azure Active Directory (préversion)
description: Mises à jour récentes des justificatifs vérifiables Azure Active Directory
author: barclayn
manager: karenh444
ms.service: active-directory
ms.subservice: verifiable-credentials
ms.topic: reference
ms.date: 10/08/2021
ms.author: barclayn
ms.openlocfilehash: 4a00a787e61e6de2eda5753262b94d8a316c9b01
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131440504"
---
# <a name="whats-new-in-azure-active-directory-verifiable-credentials-preview"></a>Nouveautés des justificatifs vérifiables Azure Active Directory (préversion)

Cet article répertorie les dernières fonctionnalités, améliorations et modifications apportées au service des informations d’identification vérifiables d’Azure Active Directory (Azure AD).

## <a name="october-2021"></a>Octobre 2021

Vous pouvez désormais utiliser l’[API REST du service de demande](get-started-request-api.md) pour créer des applications qui peuvent émettre et vérifier des informations d’identification à partir de n’importe quel langage de programmation que vous utilisez. Cette nouvelle API REST fournit une couche d’abstraction améliorée et une intégration au service Justificatifs vérifiables Azure AD.

Il est judicieux de commencer à utiliser l’API sans tarder car le Kit de développement logiciel (SDK) NodeJS sera déconseillé dans les prochains mois. La documentation et les exemples utilisent désormais l’API REST du service de demande. Pour plus d’informations, consultez [API REST du service de demande (préversion)](get-started-request-api.md).

## <a name="april-2021"></a>Avril 2021

Vous pouvez désormais émettre des [informations d’identification vérifiables](decentralized-identifier-overview.md) dans Azure AD. Ce service est utile lorsque vous devez présenter un preuve d’emploi, d’éducation ou toute autre revendication afin que le détenteur de ces justificatifs puisse décider de quand et avec qui les partager. Chaque information d’identification est signée à l’aide de clés de chiffrement associées à l’identité décentralisée que l’utilisateur possède et contrôle.
