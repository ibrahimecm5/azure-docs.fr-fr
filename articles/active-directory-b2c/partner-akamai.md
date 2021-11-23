---
title: Tutoriel pour configurer Azure Active Directory B2C avec le pare-feu d’applications web Akamai
titleSuffix: Azure AD B2C
description: Tutoriel pour configurer le pare-feu d’applications web Akamai avec Azure AD B2C
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/15/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: ea95f9194913b4df00f0b75e7e44ab301b000b95
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132283218"
---
# <a name="tutorial-configure-akamai-with-azure-active-directory-b2c"></a>Tutoriel : Configurer Akamai avec Azure Active Directory B2C

Dans cet exemple de tutoriel, découvrez comment activer la solution [Pare-feu d’applications web Akamai](https://www.akamai.com/us/en/resources/web-application-firewall.jsp) pour le locataire Azure Active Directory (AD) B2C à l’aide de domaines personnalisés. Le pare-feu d’applications web Akamai permet à l’organisation de protéger ses applications web contre des attaques malveillantes visant à exploiter des vulnérabilités telles que l’injection SQL et les scripts intersites.

>[!NOTE]
>Cette fonctionnalité est en version préliminaire publique.

Avantages de l’utilisation de la solution de pare-feu d’applications web Akamai :

- Plateforme Edge permettant la gestion du trafic vers vos services.

- Peut être configurée devant votre locataire Azure AD B2C.

- Permet une manipulation précise du trafic afin de protéger et de sécuriser votre infrastructure d’identité.

Cet exemple de tutoriel s’applique aux solutions de pare-feu d’applications web [Web Application Protector (WAP)](https://www.akamai.com/us/en/products/security/web-application-protector-enterprise-waf-firewall-ddos-protection.jsp) et [Kona Site Defender (KSD)](https://www.akamai.com/us/en/products/security/kona-site-defender.jsp) offertes par Akamai.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous disposez des éléments suivants :

- Un abonnement Azure. Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).

- Un [locataire Azure AD B2C](tutorial-create-tenant.md) lié à votre abonnement Azure.

- Un compte de [pare-feu d’applications web Akamai](https://www.akamai.com/us/en/akamai-free-trials.jsp).

## <a name="scenario-description"></a>Description du scénario

L’intégration du pare-feu d’applications web Akamai inclut les composants suivants :

- **Locataire Azure AD B2C** : serveur d’autorisation chargé de vérifier les informations d’identification de l’utilisateur à l’aide des stratégies personnalisées définies dans le locataire.  Également connu sous le nom de fournisseur d’identité.

- [**Azure Front Door**](../frontdoor/front-door-overview.md) : permet d’activer des domaines personnalisés pour le locataire Azure B2C. Tout le trafic issu du pare-feu d'applications web Cloudflare transite via Azure Front Door avant d’accéder au locataire Azure AD B2C.

- [**Pare-feu d’applications web Akamai**](https://www.akamai.com/us/en/resources/waf.jsp) – Le pare-feu d’applications web qui gère tout le trafic envoyé au serveur d’autorisation.

## <a name="integrate-with-azure-ad-b2c"></a>Intégrer avec Azure AD B2C

1. Pour utiliser des domaines personnalisés dans Azure AD B2C, la fonctionnalité de domaine personnalisé fournie par Azure Front Door doit être utilisée. Découvrez comment [activer des domaines personnalisés Azure AD B2C](./custom-domain.md?pivots=b2c-user-flow).  

2. Une fois le domaine personnalisé pour Azure AD B2C correctement configuré à l’aide d’Azure Front Door, [testez le domaine personnalisé](./custom-domain.md?pivots=b2c-custom-policy#test-your-custom-domain) avant de poursuivre.  

## <a name="onboard-with-akamai"></a>Intégrer avec Akamai

[Inscrivez-vous](https://www.akamai.com) et créez un compte Akamai.

### <a name="create-and-configure-property"></a>Créer et configurer une propriété

1. [Créez une nouvelle propriété](https://control.akamai.com/wh/CUSTOMER/AKAMAI/en-US/WEBHELP/property-manager/property-manager-help/GUID-14BB87F2-282F-4C4A-8043-B422344884E6.html).

2. Configurez les paramètres des propriétés comme suit :

    | Propriété | Valeur |
    |:---------------|:---------------|
    |Version de la propriété | Sélectionnez le protocole TLS standard ou étendu (recommandé). |
    |Noms d’hôtes de la propriété | Ajoutez un nom d’hôte de la propriété. Il s’agit du nom de votre domaine personnalisé, par exemple : login.domain.com. <BR> Créez ou modifiez un certificat avec les paramètres appropriés pour le nom de domaine personnalisé. Pour plus d’informations, consultez [cette page](https://learn.akamai.com/en-us/webhelp/property-manager/https-delivery-with-property-manager/GUID-9EE0EB6A-E62B-4F5F-9340-60CBD093A429.html). |

3. Définissez les paramètres de configuration des propriétés du serveur d’origine comme suit :

    |Propriété| Valeur |
    |:-----------|:-----------|
    | Type d’origine | Votre origine |
    | Nom d’hôte du serveur d’origine | yourafddomain.azurefd.net |
    | En-tête de l’hôte de redirection | En-tête d’hôte entrant |
    | Nom d’hôte de la clé de cache| En-tête d’hôte entrant |

### <a name="configure-dns"></a>Configurer DNS

Créez un enregistrement CNAME dans votre DNS, tel que login.domain.com, qui pointe vers le nom d’hôte Edge dans le champ Nom d’hôte de la propriété.

### <a name="configure-akamai-waf"></a>Configurer le pare-feu d’applications web Akamai

1. [Configurez le pare-feu d’applications web Akamai](https://learn.akamai.com/en-us/webhelp/kona-site-defender/kona-site-defender-quick-start/GUID-6294B96C-AE8B-4D99-8F43-11B886E6C39A.html#GUID-6294B96C-AE8B-4D99-8F43-11B886E6C39A).

2. Assurez-vous que les **actions de règle** pour tous les éléments listés sous le **groupe d’attaques** sont définies sur **Refuser**.

![Image montrant des actions de règle définies sur Refuser](./media/partner-akamai/rule-action-deny.png)

Apprenez-en davantage sur [le fonctionnement du contrôle et les options de configuration](https://control.akamai.com/dl/security/GUID-81C0214B-602A-4663-839D-68BCBFF41292.html).

<!-- docutune:ignore "Security Center" -->

### <a name="test-the-settings"></a>Tester les paramètres

Vérifiez les éléments suivants pour vous assurer que tout le trafic vers Azure AD B2C passe maintenant par le domaine personnalisé :

- Assurez-vous que toutes les demandes entrantes adressées au domaine personnalisé Azure AD B2C sont acheminées via le pare-feu d’applications web Akamai et utilisent une connexion TLS valide.
- Assurez-vous que tous les cookies sont correctement définis par Azure AD B2C pour le domaine personnalisé.
- Le tableau de bord du pare-feu d’applications web Akamai disponible sous la console Defender pour le cloud affiche des graphiques pour tout le trafic transitant par le pare-feu d’applications web, ainsi que tout trafic d’attaque.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer un domaine personnalisé dans Azure AD B2C](./custom-domain.md?pivots=b2c-user-flow)

- [Bien démarrer avec les stratégies personnalisées dans Azure AD B2C](./tutorial-create-user-flows.md?pivots=b2c-custom-policy&tabs=applications)
