---
author: anastasia-ms
ms.service: iot-dps
ms.topic: include
ms.date: 10/10/2021
ms.author: v-stharr
ms.openlocfilehash: ac1d2554df675dc055530ab4b721a0510f2fddfb
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399393"
---
> [!NOTE]
> Certaines zones de ce service ont des limites ajustables. Cela est représenté dans les tableaux ci-dessous avec la colonne *Ajustable ?* . Quand la limite peut être ajustée, la valeur *Réglable ?* est *Oui*.
>
>La valeur réelle à laquelle une limite peut être ajustée peut varier en fonction du déploiement de chaque client. Plusieurs instances de DPS peuvent être nécessaires pour les très grands déploiements.
>
> Si votre entreprise nécessite de déclencher une limite ou un quota ajustable au-delà de la limite par défaut, vous pouvez demander des ressources supplémentaires en [ouvrant un ticket de support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

Le tableau suivant répertorie les limites qui s’appliquent aux ressources du 	Service Azure IoT Hub Device Provisioning.

| Ressource | Limite | Ajustable ? |
| --- | --- | --- |
| Nombre maximal de services d’approvisionnement d’appareil par abonnement Azure | 10 | Oui |
| Nombre maximal d’inscriptions | 1 000 000 | Oui |
| Nombre maximal d’inscriptions individuelles | 1 000 000 | Oui |
| Nombre maximal de groupes d’inscription *(certificat X.509)* | 100 | Oui |
| Nombre maximal de groupes d’inscription *(clé symétrique)* | 100 | Non |
| Nombre maximal d’autorités de certification | 25 | Non |
| Nombre maximal de hubs IoT liés | 50 | Non |
| Taille maximale du message | 96 Ko| Non |

> [!TIP]
> Si la limite inconditionnelle sur les groupes d’inscription de clé symétrique est un problème, il est recommandé d’utiliser des inscriptions individuelles comme solution de contournement.

Le service de provisionnement des appareils a les limites de débit suivantes.

| Tarif | Valeur par unité | Ajustable ? |
| --- | --- | --- |
| Operations | 200/min/service | Oui |
| Inscriptions d’appareil | 200/min/service | Oui |
| Opération d’interrogation des appareils | 5/10 s/appareil | Non |

