---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 09/09/2021
ms.author: victorh
ms.openlocfilehash: 76fed7a6f79dafd42c6262f4f73bd21a7c2e45a7
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131860355"
---
| Ressource | Limite | Remarque |
| --- | --- | --- |
| Azure Application Gateway |1 000 par abonnement | |
| Configuration d’adresses IP frontales |2 |1 publique et 1 privée |
| Ports frontaux |100<sup>1</sup> | |
| Pool d'adresses principales |100<sup>1</sup> | |
| Serveurs principaux par pool |1,200 | |
| Écouteurs HTTP |200<sup>1</sup> |Limité à 100 écouteurs actifs qui routent le trafic. Écouteurs actifs = nombre total d’écouteurs - écouteurs non actifs.<br>Si une configuration par défaut à l’intérieur d’une règle de routage est définie pour router le trafic (avec, par exemple, un écouteur, un pool de back-ends et des paramètres HTTP), elle est également considérée comme un écouteur. Consultez les [Questions fréquemment posées sur Application Gateway](../articles/application-gateway/application-gateway-faq.yml#what-is-considered-an-active-listener-versus-inactive-listener) pour plus de détails.|
| Règles d’équilibrage de charge HTTP |400<sup>1</sup> | |
| Paramètres HTTP du serveur principal |100<sup>1</sup> | |
| Instances par passerelle |Référence SKU V1 - 32<br>Référence SKU V2 - 125 | |
| Certificats SSL |100<sup>1</sup> |1 par écouteur HTTP |
| Taille maximale du certificat SSL |RÉFÉRENCE SKU V1 : 10 Ko<br>RÉFÉRENCE SKU V2 : 16 Ko| |
| Certificats d’authentification |100 | |
| Certificats racines approuvés |100 | |
| Délai minimum d’expiration de la demande |1 seconde | |
| Délai maximal d’expiration de la demande pour un serveur principal privé |24 heures | |
| Délai maximal d’expiration de la demande pour un serveur principal externe |4 minutes | |
| Nombre de sites |100<sup>1</sup> |1 par écouteur HTTP |
| Mappages d’URL par écouteur |1 | |
| Nombre maximal de règles basées sur le chemin par mappage d’URL|100||
| Configurations de redirection |100<sup>1</sup>| |
| Nombre d’ensembles de règles de réécriture |400| |
| Nombre de configurations d’en-tête ou d’URL par ensemble de règles de réécriture|40| |
| Nombre de conditions par ensemble de règles de réécriture|40| |
| Connexions WebSocket simultanées |Passerelles moyennes 20 000<sup>2</sup><br> Grandes passerelles 50 000<sup>2</sup>| |
| Longueur maximale d’URL|32 Ko| |
| Taille d’en-tête maximale pour HTTP/2 |16 Ko| |
| Taille de téléchargement maximale (SKU Standard) |V2 - 4 Go<br>V1 - 2 Go | |
| Taille de téléchargement maximale (SKU WAF) |V1 moyenne - 100 Mo<br>V1 volumineuse - 500 Mo<br>V2 - 750 Mo<br>V2 (avec CRS 3.2 ou version ultérieure) - 4 Go| |
| Limite de taille de corps de WAF (sans les fichiers)|V1 ou V2 (avec CRS 3.1 et versions antérieures) - 128 Ko<br>V2 (avec CRS 3.2 ou version ultérieure) - 2 Mo| |
| Règles personnalisées WAF maximales|100||
| Exclusions WAF maximales par passerelle applicative|40||

<sup>1</sup> Dans le cas des références SKU compatibles WAF, vous devez limiter le nombre de ressources à 40.

<sup>2</sup> La limite est par instance Application Gateway, et non par ressource Application Gateway.
