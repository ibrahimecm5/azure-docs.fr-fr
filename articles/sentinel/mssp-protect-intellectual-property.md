---
title: Protection de la propriété intellectuelle des fournisseurs de services de sécurité gérés (MSSP) dans Microsoft Sentinel | Microsoft Docs
description: Découvrez la façon dont les fournisseurs de services de sécurité gérés (MSSP) peuvent protéger la propriété intellectuelle qu’ils ont créée dans Microsoft Sentinel.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.assetid: 10cce91a-421b-4959-acdf-7177d261f6f2
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc, ignite-fall-2021
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: bagol
ms.openlocfilehash: 11ee855d2606376ab893d581a091afeca3dd6713
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518866"
---
# <a name="protecting-mssp-intellectual-property-in-microsoft-sentinel"></a>Protection de la propriété intellectuelle des MSSP dans Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Cet article décrit les méthodes que les fournisseurs de services de sécurité gérés (MSSP) peuvent utiliser pour protéger la propriété intellectuelle qu’ils ont développée dans Microsoft Sentinel, par exemple les règles d’analyse, les requêtes de chasse, les playbooks et les classeurs de Microsoft Sentinel.

La méthode que vous choisissez dépend de la manière dont chacun de vos clients achète Azure : si vous agissez en tant que [fournisseur de solutions Cloud (CSP)](#cloud-solutions-providers-csp) ou si le client dispose d’un compte [Contrat Entreprise (EA)/Paiement à l’utilisation (PAYG)](#enterprise-agreements-ea--pay-as-you-go-payg). Les sections ci-dessous décrivent chacune de ces méthodes séparément.

## <a name="cloud-solutions-providers-csp"></a>Fournisseurs de solutions Cloud (CSP)

Si vous revendez Azure en tant que fournisseur de solutions Cloud (CSP), vous gérez l’abonnement Azure du client. Grâce à la [fonctionnalité AOBO (Admin-On-Behalf-Of)](/partner-center/azure-plan-manage), les utilisateurs du groupe Agents administrateurs de votre locataire MSSP disposent d’un accès Propriétaire à l’abonnement Azure du client, tandis que le client n’a aucun accès par défaut.

Si d’autres utilisateurs du locataire MSSP, en dehors du groupe Agents administrateurs, doivent accéder à l’environnement du client, nous vous recommandons d’utiliser [Azure Lighthouse](multiple-tenants-service-providers.md). Azure Lighthouse vous permet d’accorder aux utilisateurs ou aux groupes l’accès à une étendue spécifique, comme un groupe de ressources ou un abonnement, en utilisant l’un des rôles intégrés.

Si vous devez fournir aux utilisateurs clients un accès à l’environnement Azure, nous vous recommandons de leur accorder un accès au niveau du *groupe de ressources*, plutôt qu’à l’intégralité de l’abonnement, afin de pouvoir afficher/masquer des parties de l’environnement le cas échéant.

Par exemple :

- Vous pouvez accorder au client l’accès à plusieurs groupes de ressources où se trouvent ses applications, tout en gardant l’espace de travail Microsoft Sentinel dans un groupe de ressources distinct, auquel le client n’a pas accès.

- Utilisez cette méthode pour permettre aux clients d’afficher certains classeurs et playbooks, qui sont des ressources distinctes pouvant résider dans leur propre groupe de ressources.

Même en accordant l’accès au niveau du groupe de ressources, les clients auront toujours accès aux données de journal des ressources auxquelles ils peuvent accéder, par exemple les journaux d’une machine virtuelle, même sans accès à Microsoft Sentinel. Pour plus d’informations, consultez [Gérer l’accès aux données de Microsoft Sentinel par ressource](resource-context-rbac.md).

> [!TIP]
> Si vous devez fournir à vos clients un accès à l’ensemble de l’abonnement, vous pouvez consulter l’aide dans [Contrats Entreprise (EA)/Paiement à l’utilisation (PAYG)](#enterprise-agreements-ea--pay-as-you-go-payg).
>

### <a name="sample-microsoft-sentinel-csp-architecture"></a>Exemple d’architecture CSP Microsoft Sentinel

L’image suivante illustre le fonctionnement des autorisations décrites dans la [section précédente](#cloud-solutions-providers-csp) lorsque vous fournissez un accès aux clients CSP :

:::image type="content" source="media/mssp-protect-intellectual-property/csp-customers.png" alt-text="Protégez votre propriété intellectuelle Microsoft Sentinel à l’aide de clients CSP.":::

Dans cette image :

- Les utilisateurs auxquels est accordé un accès **Propriétaire** à l’abonnement CSP sont les utilisateurs du groupe Agents administrateurs, dans le locataire Azure AD du MSSP.
- D’autres groupes du MSSP ont accès à l’environnement du client via Azure Lighthouse.
- L’accès des clients aux ressources Azure est géré par Azure RBAC au niveau du groupe de ressources.

    Cela permet aux MSSP de masquer les composants Microsoft Sentinel le cas échéant, comme les règles d’analyse et les requêtes de chasse.

Pour plus d’informations, consultez également la [documentation d’Azure Lighthouse](../lighthouse/concepts/cloud-solution-provider.md).

## <a name="enterprise-agreements-ea--pay-as-you-go-payg"></a>Contrats Entreprise (EA)/Paiement à l’utilisation (PAYG)

Si votre client achète directement auprès de Microsoft, il a déjà un accès complet à l’environnement Azure, et vous ne pouvez pas masquer quoi que ce soit qui se trouve dans l’abonnement Azure du client.

Au lieu de cela, protégez la propriété intellectuelle que vous avez développée dans Microsoft Sentinel comme suit, selon le type de ressource que vous devez protéger :

### <a name="analytics-rules-and-hunting-queries"></a>Règles d’analyse et requêtes de chasse

Les règles d’analyse et les requêtes de chasse sont contenues dans Microsoft Sentinel et ne peuvent donc pas être séparées de l’espace de travail Microsoft Sentinel.

Même si un utilisateur dispose uniquement des autorisations Lecteur Microsoft Sentinel, il peut toujours visualiser la requête. Dans ce cas, nous vous recommandons d’héberger vos règles d’analyse et vos requêtes de chasse dans votre propre locataire MSSP, au lieu du locataire du client.

Pour ce faire, vous avez besoin d’un espace de travail dans votre propre locataire pour lequel Microsoft Sentinel est activé, et vous devez également voir l’espace de travail du client via [Azure Lighthouse](multiple-tenants-service-providers.md).

Pour créer une règle d’analyse ou une requête de chasse dans le locataire MSSP qui fait référence aux données dans le locataire du client, vous devez utiliser l’instruction `workspace` comme suit :

```kql
workspace('<customer-workspace>').SecurityEvent
| where EventID == ‘4625’
```

Lorsque vous ajoutez une instruction `workspace` à vos règles d’analyse, tenez compte des points suivants :

- **Aucune alerte dans l’espace de travail du client**. Les règles créées de cette façon ne créent pas d’alertes ni d’incidents dans l’espace de travail du client. Les alertes et les incidents existeront uniquement dans votre espace de travail MSSP.

- **Créer des alertes distinctes pour chaque client**. Lorsque vous utilisez cette méthode, nous vous recommandons également d’utiliser des règles d’alerte distinctes pour chaque client et chaque détection, car l’instruction workspace sera différente dans chaque cas.

    Vous pouvez ajouter le nom du client au nom de la règle d’alerte pour identifier facilement le client où l’alerte est déclenchée. Des alertes distinctes peuvent donner lieu à un grand nombre de règles, que vous pouvez gérer à l’aide de scripts ou de [Microsoft Sentinel en tant que code](https://techcommunity.microsoft.com/t5/azure-sentinel/deploying-and-managing-azure-sentinel-as-code/ba-p/1131928).

    Par exemple :

    :::image type="content" source="media/mssp-protect-intellectual-property/mssp-rules-per-customer.png" alt-text="Créez des règles distinctes dans votre espace de travail MSSP pour chaque client.":::

- **Créer des espaces de travail MSSP distincts pour chaque client**. En créant des règles distinctes pour chaque client et chaque détection, vous risquez d’atteindre le nombre maximal de règles d’analyse pour votre espace de travail (512). Si vous avez de nombreux clients et que vous pensez atteindre cette limite, vous pouvez créer un espace de travail MSSP distinct pour chaque client.

    Par exemple :

    :::image type="content" source="media/mssp-protect-intellectual-property/mssp-rules-and-workspace-per-customer.png" alt-text="Créez un espace de travail et des règles dans votre locataire MSSP pour chaque client.":::

> [!IMPORTANT]
> La clé de la réussite de cette méthode consiste à utiliser l’automatisation pour gérer un large ensemble de règles dans vos espaces de travail.
>
> Pour plus d’informations, consultez l’article [Règles d’analyse pour plusieurs espaces de travail](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-cross-workspace-analytics-rules/ba-p/1664211).
>

### <a name="workbooks"></a>Classeurs

Si vous avez développé un classeur Microsoft Sentinel que vous ne souhaitez pas que votre client copie, hébergez le classeur dans le locataire de votre MSSP. Assurez-vous d’avoir accès aux espaces de travail de votre client via Azure Lighthouse, puis veillez à modifier le classeur pour utiliser ces espaces de travail.

Par exemple :

:::image type="content" source="media/mssp-protect-intellectual-property/cross-workspace-workbook.png" alt-text="Classeurs pour plusieurs espaces de travail":::

Pour plus d’informations, consultez [Classeurs pour plusieurs espaces de travail](extend-sentinel-across-workspaces-tenants.md#cross-workspace-workbooks).

Si vous souhaitez que le client puisse afficher les visualisations du classeur tout en gardant le code secret, nous vous recommandons d’exporter le classeur vers Power BI.

Exporter votre classeur vers Power BI :

- **Facilite le partage des visualisations du classeur**. Vous pouvez envoyer au client un lien vers le tableau de bord Power BI, où il peut afficher les données rapportées, sans avoir besoin des autorisations d’accès Azure.
- **Permet la planification**. Configurez Power BI pour qu’il envoie régulièrement des e-mails contenant un instantané du tableau de bord pour cette période.

Pour plus d’informations, consultez [Importation de données de journal Azure Monitor dans Power BI](../azure-monitor/logs/log-powerbi.md).

### <a name="playbooks"></a>Playbooks

Vous pouvez protéger vos playbooks comme suit, en fonction de l’emplacement où les règles d’analyse qui déclenchent le playbook ont été créées :

- **Règles d’analyse créées dans l’espace de travail MSSP**.  Assurez-vous de créer vos playbooks dans le locataire MSSP et vérifiez que vous recevez toutes les données d’incident et d’alerte de l’espace de travail MSSP. Vous pouvez attacher les playbooks chaque fois que vous créez une nouvelle règle dans votre espace de travail.

    Par exemple :

    :::image type="content" source="media/mssp-protect-intellectual-property/rules-in-mssp-workspace.png" alt-text="Règles créées dans l’espace de travail MSSP.":::

- **Règles d’analyse créées dans l’espace de travail du client**. Utilisez Azure Lighthouse pour attacher des règles d’analyse de l’espace de travail du client à un playbook hébergé dans votre espace de travail MSSP. Dans ce cas, le playbook obtient les données d’alerte et d’incident, ainsi que toute autre information sur le client, à partir de l’espace de travail du client.

    Par exemple :

    :::image type="content" source="media/mssp-protect-intellectual-property/rules-in-customer-workspace.png" alt-text="Règles créées dans l’espace de travail du client.":::

Dans les deux cas, si le playbook doit accéder à l’environnement Azure du client, utilisez un utilisateur ou un principal de service disposant de cet accès via Lighthouse.

Toutefois, si le playbook doit accéder à des ressources non Azure dans le locataire du client, comme Azure AD, Office 365 ou Microsoft 365 Defender, vous devrez créer un principal de service avec les autorisations appropriées dans le locataire du client, puis ajouter cette identité dans le playbook.

> [!NOTE]
> Si vous utilisez des règles d’automatisation avec vos playbooks, vous devez définir les autorisations des règles d’automatisation sur le groupe de ressources dans lequel se trouvent les playbooks.
> Pour plus d’informations, consultez [Autorisations de règles d’automatisation pour l’exécution de playbooks](automate-incident-handling-with-automation-rules.md#permissions-for-automation-rules-to-run-playbooks).
>

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez les pages suivantes :

- [Playbook technique Microsoft Sentinel pour MSSP](https://cloudpartners.transform.microsoft.com/download?assetname=assets/Azure-Sentinel-Technical-Playbook-for-MSSPs.pdf&download=1)
- [Gérer plusieurs locataires dans Microsoft Sentinel en tant que MSSP](multiple-tenants-service-providers.md)
- [Étendre Microsoft Sentinel dans les espaces de travail et les locataires](extend-sentinel-across-workspaces-tenants.md)
- [Visualiser et superviser vos données](monitor-your-data.md)
- [Tutoriel : Configurer des réponses automatisées aux menaces dans Microsoft Sentinel](tutorial-respond-threats-playbook.md)
