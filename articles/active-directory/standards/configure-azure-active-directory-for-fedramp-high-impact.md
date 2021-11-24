---
title: Configurer Azure Active Directory pour atteindre un niveau d’impact FedRAMP High
description: Vue d’ensemble sur la manière dont vous pouvez atteindre un niveau d’impact FedRAMP High pour votre organisation à l’aide d’Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: barbaraselden
ms.author: baselden
manager: mtillman
ms.reviewer: martinco
ms.date: 4/26/2021
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1cc2d2bf4a51a0d50c47d41982ced9962a977e2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132302043"
---
# <a name="configure-azure-active-directory-to-meet-fedramp-high-impact-level"></a>Configurer Azure Active Directory pour atteindre un niveau d’impact FedRAMP High

Le [Federal Risk and Authorization Management Program](https://www.fedramp.gov/) (FedRAMP) est un processus d’évaluation et d’autorisation pour les fournisseurs de services cloud (CSP). Plus précisément, le processus est destiné aux CSP qui créent des offres de solution cloud pour une utilisation avec les agences fédérales. Azure et Azure Government ont obtenu une [autorité provisoire d’exploitation (P-ATO) de niveau d’impact élevé](/compliance/regulatory/offering-fedramp) auprès du Joint Authorization Board, l’échelon le plus haut en matière d’accréditation FedRAMP.

Azure fournit la possibilité de satisfaire à toutes les exigences de contrôle, afin d’obtenir une qualification élevée FedRAMP pour votre offre de solutions Cloud, ou en tant qu’organisme fédéral. Il est de la responsabilité de votre organisation d’effectuer les configurations ou processus supplémentaires pour assurer la conformité. Cette responsabilité s’applique aux fournisseurs de services cloud qui recherchent une autorisation FedRAMP élevée pour leur offre de solutions cloud, et aux organismes fédéraux qui recherchent une autorisation d’exploitation (ATO, Authority to Operate). 

## <a name="microsoft-and-fedramp"></a>Microsoft et FedRAMP 

Microsoft Azure prend en charge plus de services aux niveaux d’[impact FedRAMP High](../../azure-government/compliance/azure-services-in-fedramp-auditscope.md) que n’importe quel autre fournisseur de services cloud. Et même si ce niveau répond aux besoins de nombreux clients Secteur Public des États-Unis dans le cloud public Azure, les organismes avec des exigences plus rigoureuses peuvent s’appuyer sur le cloud Azure Government. Azure Government fournit des protections supplémentaires, telles que le filtrage renforcé du personnel. 

Microsoft est tenu de renouveler la certification de ses services cloud tous les ans pour conserver ses autorisations. Pour ce faire, Microsoft supervise et évalue en permanence ses contrôles de sécurité et démontre que la sécurité de ses services reste conforme. Pour plus d’informations, consultez [Autorisations FedRAMP des services cloud Microsoft](https://marketplace.fedramp.gov/) et [Rapports d’audit FedRAMP de Microsoft](https://aka.ms/MicrosoftFedRAMPAuditDocuments). Pour recevoir d’autres rapports FedRAMP, envoyez un e-mail à la [Documentation fédérale Azure](mailto:AzFedDoc@microsoft.com).

Plusieurs chemins mènent à l’autorisation FedRAMP. Vous pouvez réutiliser le package d’autorisation existant d’Azure et l’aide fournie ici pour réduire de manière significative le temps et les efforts nécessaires à l’obtention d’autorisations ATO ou P-ATO. 

## <a name="scope-of-guidance"></a>Étendue des recommandations

La ligne de base de FedRAMP High se compose de 421 contrôles et améliorations de contrôle issus du [NIST 800-53 Security Controls Catalog Revision 4](https://csrc.nist.gov/publications/detail/sp/800-53/rev-4/final). Lorsque cela est possible, nous avons inclus des éclaircissements tirés de la publication [800-53 Revision 5](https://csrc.nist.gov/publications/detail/sp/800-53/rev-5/final). Cet ensemble d’articles définit un sous-ensemble de ces contrôles relatifs à l’identité et que vous devez configurer. 

Nous vous fournissons une aide normative pour vous mettre en conformité avec les contrôles que vous êtes chargé de configurer dans Azure Active Directory (Azure AD). Pour répondre entièrement à certaines exigences de contrôle d’identité, vous devrez peut-être utiliser d’autres systèmes. Les autres systèmes peuvent disposer d’un outil SIEM (Informations de sécurité et gestion d’événements), tel que Microsoft Sentinel. Si vous utilisez des services Azure en dehors d’Azure Active Directory, vous devez prendre en compte d’autres contrôles. Vous pouvez également utiliser les fonctionnalités qu’Azure a déjà mises en place pour assurer les contrôles.

La liste suivante répertorie les ressources FedRAMP :

* [Federal Risk and Authorization Management Program](https://www.fedramp.gov/)

* [FedRAMP Security Assessment Framework](https://www.fedramp.gov/assets/resources/documents/FedRAMP_Security_Assessment_Framework.pdf)

* [Agency Guide for FedRAMP Authorizations](https://www.fedramp.gov/assets/resources/documents/Agency_Guide_for_Reuse_of_FedRAMP_Authorizations.pdf)

* [Gestion de la conformité dans le cloud chez Microsoft](https://www.microsoft.com/trustcenter/common-controls-hub)

* [Cloud de Microsoft pour le Secteur Public](https://go.microsoft.com/fwlink/p/?linkid=2087246)

* [Offres de conformité Azure](https://aka.ms/azurecompliance)

* [Définition d’initiative intégrée de la Stratégie Azure FedRAMP High](../../governance/policy/samples/fedramp-high.md)

* [Centre de conformité Microsoft 365](/microsoft-365/compliance/microsoft-365-compliance-center)

* [Gestionnaire de conformité Microsoft](/microsoft-365/compliance/compliance-manager)

## <a name="next-steps"></a>Étapes suivantes

[Configurer des contrôles d’accès](fedramp-access-controls.md)

[Configurer les contrôles d’identification et d’authentification](fedramp-identification-and-authentication-controls.md)

[Configurer d’autres contrôles](fedramp-other-controls.md)
