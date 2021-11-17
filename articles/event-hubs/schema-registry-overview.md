---
title: Azure Schema Registry dans Azure Event Hubs
description: Cet article fournit une vue d’ensemble de la prise en charge du registre de schémas par Azure Event Hubs.
ms.topic: overview
ms.date: 11/02/2021
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: f9e405a82b73530cc53ec98f22b2c1f4473b2550
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132398477"
---
# <a name="azure-schema-registry-in-azure-event-hubs"></a>Azure Schema Registry dans Azure Event Hubs
Dans de nombreux scénarios de diffusion en continu d’événements et de messagerie, la charge utile de message ou d’événement contient des données structurées. Le format piloté par schéma, par exemple [Apache Avro](https://avro.apache.org/), est souvent utilisé pour sérialiser ou désérialiser ces données structurées. 

:::image type="content" source="./media/schema-registry-overview/schema-driven-ser-de.svg" alt-text="Sérialisation/désérialisation pilotée par schéma":::

Les applications de producteur utilisent un document de schéma pour sérialiser la charge utile d’événement et la publier sur un répartiteur d’événements tel que Event Hubs. De même, les applications de consommateur lisent la charge utile d’événement du répartiteur et la désérialisent à l’aide du même document de schéma. Ainsi, les producteurs et les consommateurs peuvent valider l’intégrité des données à l’aide d’un document de schéma. 


## <a name="why-we-need-a-schema-registry"></a>Pourquoi un registre de schéma est-il nécessaire ? 
Lorsque vous utilisez des formats pilotés par schéma, les applications de producteur veulent que les schémas des événements publiés soient mis à la disposition des consommateurs. Il est possible de partager le schéma correspondant avec chaque donnée d’événement, mais cela est inefficace. Lorsque les nouveaux consommateurs souhaitent consommer des données d’événement, ils doivent avoir un moyen de comprendre le format des données publiées. Nous devons également nous assurer que nous prenons en charge l’évolution du schéma afin que les producteurs et les consommateurs puissent évoluer à des taux différents. 


## <a name="azure-schema-registry"></a>Azure Schema Registry
Le **Registre de schéma Azure** est une fonctionnalité de Event Hubs, qui fournit un référentiel central pour les documents de schéma pour les applications pilotées par les événements et les applications axées sur la messagerie. Il offre aux producteurs et aux consommateurs la flexibilité nécessaire pour échanger des données sans qu’ils doivent gérer et partager le schéma entre eux. Le registre de schémas fournit également une infrastructure de gouvernance simple pour les schémas réutilisables et définit la relation entre les schémas par le biais d’une construction de regroupement (groupes de schémas).

:::image type="content" source="./media/schema-registry-overview/schema-registry.svg" alt-text="Registre de schémas":::

Avec les infrastructures de sérialisation basées sur des schémas comme Apache Avro, l’externalisation des métadonnées de sérialisation dans des schémas partagés peut également réduire considérablement la surcharge par message des informations sur le type et des noms de champs inclus avec chaque jeu de données, comme c’est le cas avec des formats étiquetés tels que JSON. Le fait de disposer de schémas stockés avec les événements et à l’intérieur de l’infrastructure d’événements permet de s’assurer que les métadonnées requises pour la sérialisation/désérialisation sont toujours accessibles et que les schémas ne peuvent pas être mal placés. 

> [!NOTE]
> La fonctionnalité n’est pas disponible au niveau **de base**.

## <a name="schema-registry-information-flow"></a>Flux d’informations de Schema Registry 
Le flux d’informations lorsque vous utilisez Schema Registry est le même pour tous les protocoles que vous utilisez pour publier ou consommer des événements à partir d’Azure Event Hubs. Le diagramme suivant illustre le flux d’un producteur d’événements Kafka et d’un scénario de consommateur qui utilise Schema Registry. 

:::image type="content" source="./media/schema-registry-overview/information-flow.svg" lightbox="./media/schema-registry-overview/information-flow.svg" alt-text="Image qui montre le workflow des informations du Registre du schéma.":::


Le workflow part du producteur, où les producteurs Kafka sérialisent les données à l’aide du document de schéma. 
- L’application Kafka Producer utilise ``KafkaAvroSerializer`` pour sérialiser des données d’événement à l’aide du schéma spécifié côté client. 
- L’application Producer doit fournir les détails du point de terminaison du Registre du schéma et d’autres paramètres facultatifs qui sont requis pour la validation du schéma. 
- Le sérialiseur effectue une recherche dans le registre de schéma à l’aide du contenu de schéma que le producteur utilise pour sérialiser les données d’événement. 
- S’il trouve un tel schéma, l’ID de schéma correspondant est retourné. Vous pouvez configurer l’application Producer si le schéma n’existe pas, l’application Producer peut configurer le registre de schémas client pour inscrire automatiquement le schéma. 
- Ensuite, le sérialiseur utilise cet ID de schéma et l’ajoute aux données sérialisées qui sont publiées dans le Event Hubs. 
- Côté client, ``KafkaAvroDeserializer`` utilise l’ID de schéma pour récupérer le contenu du schéma à partir du registre de schéma. 
- Le désérialiseur utilise ensuite le contenu de schéma pour désérialiser les données d’événement qu’il lit à partir du concentrateur d’événements. 
- Les clients de registre de schéma utilisent la mise en cache pour empêcher les recherches de registre de schéma redondantes.  


## <a name="schema-registry-elements"></a>Éléments du registre de schéma

Un espace de noms Event Hubs peut désormais héberger des groupes de schémas avec Event Hubs (ou des rubriques Kafka). Il héberge un registre de schémas et peut comporter plusieurs groupes de schémas. En dépit de l’hébergement dans Azure Event Hubs, le registre de schémas peut être utilisé de manière universelle avec tous les services de messagerie Azure et tout autre message ou répartiteur d’événements. Chacun de ces groupes de schémas est un référentiel sécurisable distinct pour un ensemble de schémas. Les groupes peuvent être alignés sur une application ou une unité d’organisation particulière. 

:::image type="content" source="./media/schema-registry-overview/elements.png" alt-text="Image qui montre les éléments du Registre du schéma.":::


### <a name="schema-groups"></a>Groupes de schémas
Le groupe de schémas est un groupe logique de schémas similaires basés sur les critères de votre entreprise. Un groupe de schémas peut contenir plusieurs versions d’un schéma. Le paramètre de mise en œuvre de la compatibilité sur un groupe de schémas peut assurer la compatibilité descendante des versions de schéma plus récentes.

La limite de sécurité imposée par le mécanisme de regroupement permet de s’assurer que les secrets commerciaux ne fuient pas par inadvertance à travers les métadonnées dans des situations où l’espace de noms est partagé entre plusieurs partenaires. Cela permet également aux propriétaires d’applications de gérer les schémas indépendamment d’autres applications qui partagent le même espace de noms.

### <a name="schemas"></a>Schémas
Les schémas définissent le contrat entre les producteurs et les consommateurs. Un schéma défini dans un registre de schémas Event Hubs permet de gérer le contrat en dehors des données d’événement, ce qui supprime la surcharge de charge utile. Un schéma a un nom, un type (exemple : enregistrement, tableau, etc.), le mode de compatibilité (aucune, compatibilité ascendante, compatibilité descendante, totale) et le type de sérialisation (uniquement Avro pour le moment). Vous pouvez créer plusieurs versions d’un schéma et récupérer et utiliser une version spécifique d’un schéma. 

## <a name="schema-evolution"></a>Évolution du schéma 

Les schémas doivent évoluer en fonction des besoins de l’entreprise des producteurs et des consommateurs. Le registre de schéma Azure prend en charge l’évolution du schéma en introduisant des modes de compatibilité au niveau du groupe de schémas. Lorsque vous créez un groupe de schémas, vous pouvez spécifier le mode de compatibilité des schémas que vous incluez dans ce groupe de schémas. Lorsque vous mettez à jour un schéma, la modification doit se conformer au mode de compatibilité affecté, puis elle crée uniquement une nouvelle version du schéma. 

Le registre de schéma Azure pour Event Hubs prendre en charge les modes de compatibilité suivants. 

### <a name="backward-compatibility"></a>Compatibilité descendante
Le mode de compatibilité descendante permet au code du consommateur d’utiliser une nouvelle version du schéma, mais il peut traiter les messages avec l’ancienne version du schéma. Lorsque vous utilisez le mode de compatibilité descendante dans un groupe de schémas, les modifications suivantes peuvent être apportées à un schéma. 

- Supprimer des champs. 
- Ajouter des champs facultatifs. 


### <a name="forward-compatibility"></a>Compatibilité ascendante
La compatibilité ascendante permet au code du consommateur d’utiliser une ancienne version du schéma, mais il peut lire les messages avec le nouveau schéma. Le mode de compatibilité ascendante permet d’effectuer les modifications suivantes sur un schéma. 
- Ajouter des champs 
- Supprimer les champs facultatifs 


### <a name="no-compatibility"></a>Aucune compatibilité
Lorsque le mode de compatibilité ``None`` est utilisé, le registre de schéma n’effectue aucune vérification de compatibilité lorsque vous mettez à jour des schémas. 

## <a name="client-sdks"></a>Kits de développement logiciel (SDK) client

Vous pouvez utiliser l’une des bibliothèques suivantes qui incluent un sérialiseur Avro, qui vous permettent de sérialiser et désérialiser des charges utiles contenant des identificateurs de schéma de registre de schémas et des données encodées Avro.

- [.NET - Microsoft.Azure.Data.SchemaRegistry.ApacheAvro](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/schemaregistry/Microsoft.Azure.Data.SchemaRegistry.ApacheAvro)
- [Java - azure-data-schemaregistry-avro](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/schemaregistry/azure-data-schemaregistry-apacheavro)
- [Python - azure-schemaregistry-avroserializer](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/schemaregistry/azure-schemaregistry-avroserializer)
- [JavaScript - @azure/schema-registry-avro](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/schemaregistry/schema-registry-avro)
- [Apache Kafka](https://github.com/Azure/azure-schema-registry-for-kafka/) - Exécuter des sérialiseurs et des désérialiseurs Apache Avro intégrés sauvegardés par des registres de schémas Azure. Le sérialiseur client Apache Kafka du client Java pour le registre de schémas Azure peut être utilisé dans n’importe quel scénario Apache Kafka et avec un déploiement Apache Kafka® ou un service cloud. 

## <a name="limits"></a>limites
Pour connaître les limites (par exemple, le nombre de groupes de schémas dans un espace de noms) d’Event Hubs, consultez [Quotas et limites d’Azure Event Hubs](event-hubs-quotas.md)

## <a name="azure-role-based-access-control"></a>Contrôle d'accès en fonction du rôle Azure
Quand vous accédez au registre de schémas programmatiquement, vous devez inscrire une application dans Azure Active Directory (Azure AD) et ajouter le principal de sécurité de l’application à l’un des rôles de contrôle d’accès en fonction du rôle Azure (Azure RBAC) :

| Role | Description | 
| ---- | ----------- | 
| Propriétaire | Lire, écrire et supprimer des groupes de registres de schémas et des schémas. |
| Contributeur | Lire, écrire et supprimer des groupes de registres de schémas et des schémas. |
| [Lecteur du registre de schémas](../role-based-access-control/built-in-roles.md#schema-registry-reader-preview) | Lire et répertorier les groupes de registres de schémas et les schémas. |
| [Contributeur du registre de schémas](../role-based-access-control/built-in-roles.md#schema-registry-reader-preview) | Lire, écrire et supprimer des groupes de registres de schémas et des schémas. |

Pour obtenir des instructions sur la création et l’inscription d’une application à l’aide du portail Azure, consultez [Inscrire une application avec Azure AD](../active-directory/develop/quickstart-register-app.md). Notez l’ID client (ID d’application), l’ID de locataire et le secret à utiliser dans le code. 

## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment créer un registre de schémas à l’aide du portail Azure, consultez [Créer un registre de schémas Event Hubs à l’aide du portail Azure](create-schema-registry.md).
- Reportez-vous aux exemples suivants de la **Bibliothèque client Avro de registre de schémas**.
    - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/schemaregistry/Microsoft.Azure.Data.SchemaRegistry.ApacheAvro/tests/Samples)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/schemaregistry/azure-data-schemaregistry-apacheavro/src/samples)
    - [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/schemaregistry/schema-registry-avro/samples )
    - [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/schemaregistry/azure-schemaregistry-avroserializer/samples )
    - [Intégration de Kafka Avro pour le registre de schémas Azure](https://github.com/Azure/azure-schema-registry-for-kafka/tree/master/csharp/avro/samples)
