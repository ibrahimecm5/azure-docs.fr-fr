---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 09899ce768daca6dbd960d67d8d03aca5cec56a3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128578857"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les domaines gérés par Azure Active Directory Domain Services doivent utiliser le mode TLS 1.2 uniquement](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3aa87b5a-7813-4b57-8a43-42dd9df5aaa7) |Utilisez le mode TLS 1.2 uniquement pour vos domaines gérés. Par défaut, Azure AD Domain Services permet d’utiliser des méthodes de chiffrement comme NTLM v1 et TLS v1. Certaines applications héritées peuvent avoir besoin de ces chiffrements, mais étant considérés comme faibles, vous pouvez les désactiver si vous n’en avez pas l’utilité. Quand le mode TLS 1.2 uniquement est activé, les clients effectuant une demande qui n’utilise pas TLS 1.2 échouent. Pour en savoir plus, rendez-vous sur [https://docs.microsoft.com/azure/active-directory-domain-services/secure-your-domain](../../../../articles/active-directory-domain-services/secure-your-domain.md). |Audit, Refuser, Désactivé |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Active%20Directory/AADDomainServices_TLS_Audit.json) |