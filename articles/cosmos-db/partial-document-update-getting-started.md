---
title: Bien démarrer avec la Mise à jour partielle de document Azure Cosmos DB
description: Cet article fournit des exemples d’utilisation de la Mise à jour partielle de document avec les kits SDK .NET, Java et Node.
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 08/25/2021
ms.author: abhishgu
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3ceebe9f537dcf2f496594d6a6c63972fcb7af92
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131434101"
---
# <a name="azure-cosmos-db-partial-document-update-getting-started"></a>Mise à jour partielle de document Azure Cosmos DB : Bien démarrer
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Cet article fournit des exemples d’utilisation de la Mise à jour partielle de document avec les kits SDK .NET, Java et Node, ainsi que les erreurs courantes que vous pouvez rencontrer. Des exemples de code pour les scénarios suivants ont été fournis :

- Exécution d’une seule opération de patch
- Combinaison de plusieurs opérations de patch
- Syntaxe de patch conditionnel basée sur le prédicat de filtre
- Exécution d’une opération de patch dans le cadre d’une transaction

## <a name="net"></a>.NET

Vous pouvez télécharger la version *3.22.1-preview* du Kit de développement logiciel (SDK) .NET v3 Azure Cosmos DB à partir de la [galerie NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.22.1-preview).

> [!NOTE]
> Un exemple de mise à jour partielle des documents est disponible dans le [dépôt d’exemples .NET v3](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/3fa885fdd84e2f8852d2a1d5c75c56b642b5bba3/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs) sur GitHub.

**Exécution d’une seule opération de patch**

```csharp
ItemResponse<SalesOrder> response = await container.PatchItemAsync<SalesOrder>(
    id: order.Id,
    partitionKey: new PartitionKey(order.AccountNumber),
    patchOperations: new[] { PatchOperation.Replace("/TotalDue", 0) });

SalesOrder updated = response.Resource;
```

**Combinaison de plusieurs opérations de patch**

```csharp
List<PatchOperation> patchOperations = new List<PatchOperation>();
patchOperations.Add(PatchOperation.Add("/nonExistentParent/Child", "bar"));
patchOperations.Add(PatchOperation.Remove("/cost"));
patchOperations.Add(PatchOperation.Increment("/taskNum", 6));
patchOperations.Add(patchOperation.Set("/existingPath/newproperty",value));

container.PatchItemAsync<item>(
                id: 5,
                partitionKey: new PartitionKey("task6"),
                patchOperations: patchOperations );
```

**Syntaxe de patch conditionnel basée sur le prédicat de filtre**

```csharp
PatchItemRequestOptions patchItemRequestOptions = new PatchItemRequestOptions
{
    FilterPredicate = "from c where (c.TotalDue = 0 OR NOT IS_DEFINED(c.TotalDue))"
};
response = await container.PatchItemAsync<SalesOrder>(
    id: order.Id,
    partitionKey: new PartitionKey(order.AccountNumber),
    patchOperations: new[] { PatchOperation.Replace("/ShippedDate", DateTime.UtcNow) },
    patchItemRequestOptions);

SalesOrder updated = response.Resource;
```

**Exécution d’une opération de patch dans le cadre d’une transaction**


```csharp
List<PatchOperation> patchOperationsUpdateTask = new List<PatchOperation>()
            {
                PatchOperation.Add("/children/1/pk", "patched"),
                PatchOperation.Remove("/description"),
                PatchOperation.Add("/taskNum", 8),
                PatchOperation.Replace("/taskNum", 12)
            };

TransactionalBatchPatchItemRequestOptions requestOptionsFalse = new TransactionalBatchPatchItemRequestOptions()
            {
                FilterPredicate = "from c where c.taskNum = 3"
            };

TransactionalBatchInternal transactionalBatchInternalFalse = (TransactionalBatchInternal)containerInternal.CreateTransactionalBatch(new Cosmos.PartitionKey(testItem.pk));
transactionalBatchInternalFalse.PatchItem(id: testItem1.id, patchOperationsUpdateTaskNum12, requestOptionsFalse);
transactionalBatchInternalFalse.PatchItem(id: testItem2.id, patchOperationsUpdateTaskNum12, requestOptionsFalse);
transactionalBatchInternalFalse.ExecuteAsync());
```

## <a name="java"></a>Java

L’API Patch est actuellement en préversion dans la version 4.20.0 du Kit de développement logiciel (SDK) Java. Une fois en disponibilité générale, elle figurera dans la version 4.21.0. Pour utiliser des opérations d’application de correctif, ajoutez la version *4.20.0* du [Kit de développement logiciel (SDK) Java v4 Azure Cosmos DB](sql-api-sdk-java-v4.md) à la liste des dépendances dans votre `pom.xml`, ou téléchargez-la directement à partir de [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos).

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-cosmos</artifactId>
    <version>4.20.0</version>
</dependency>
```

> [!NOTE]
> L’exemple complet est disponible dans le [dépôt d’exemples du SDK Java v4](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/) sur GitHub.

**Exécution d’une seule opération de patch**

```java
CosmosPatchOperations cosmosPatchOperations = CosmosPatchOperations.create();
cosmosPatchOperations.add("/registered", true);

CosmosPatchItemRequestOptions options = new CosmosPatchItemRequestOptions();

CosmosItemResponse<Family> response = this.container.patchItem(id, new PartitionKey(partitionKey),
                                      cosmosPatchOperations, options, Family.class);
```

**Combinaison de plusieurs opérations de patch**

```java
CosmosPatchOperations cosmosPatchOperations = CosmosPatchOperations.create();
cosmosPatchOperations.add("/registered", true)
                     .replace("/parents/0/familyName", "Doe");
CosmosPatchItemRequestOptions options = new CosmosPatchItemRequestOptions();

CosmosItemResponse<Family> response = this.container.patchItem(id, new PartitionKey(partitionKey),
                                      cosmosPatchOperations, options, Family.class);
```

**Syntaxe de patch conditionnel basée sur le prédicat de filtre**

```java
CosmosPatchOperations cosmosPatchOperations = CosmosPatchOperations.create();
                                                                   .add("/vaccinated", true);
CosmosPatchItemRequestOptions options = new CosmosPatchItemRequestOptions();
options.setFilterPredicate("from f where f.registered = true");

CosmosItemResponse<Family> response = this.container.patchItem(id, new PartitionKey(partitionKey),
                                      cosmosPatchOperations, options, Family.class);
```

**Exécution d’une opération de patch dans le cadre d’une transaction**

```java
CosmosBatch batch = CosmosBatch.createCosmosBatch(new PartitionKey(family.getLastName()));
batch.createItemOperation(family);

CosmosPatchOperations cosmosPatchOperations = CosmosPatchOperations.create().add("/registered", false);
batch.patchItemOperation(family.getId(), cosmosPatchOperations);

CosmosBatchResponse response = container.executeCosmosBatch(batch);
if (response.isSuccessStatusCode()) {
    // if transactional batch succeeds   
}
```

## <a name="node"></a>Nœud

**Exécution d’une seule opération de patch**

```javascript
const patchSource = itemDefList[1];

const replaceOperation: PatchOperation[] = 
    [{ 
      op: "replace", 
      path: "/lastName", 
      value: "Martin" 
    }];

const { resource: patchSource1 } = await container.item(patchSource.lastName).patch(replaceOperation); 
console.log(`Patched ${patchSource1.lastName} to new ${patchSource1.lastName}.`); 
```

**Combinaison de plusieurs opérations de patch**

```javascript
const multipleOperations: PatchOperation[] = [ 
    { 
      op: "add", 
      path: "/aka", 
      value: "MeFamily" 
    }, 
    { 
      op: "add", 
      path: "/years", 
      value: 12 
    }, 
    { 
      op: "replace", 
      path: "/lastName", 
      value: "Jose" 
    }, 
    { 
      op: "remove", 
      path: "/parents" 
    }, 
    { 
      op: "set", 
      path: "/children/firstName", 
      value: "Anderson" 
    }, 
    { 
      op: "incr", 
      path: "/years", 
      value: 5 
    } 
  ]; 

const { resource: patchSource2  } = await container.item(patchSource.id).patch(multipleOperations); 
 ```

**Syntaxe de patch conditionnel basée sur le prédicat de filtre**

```javascript
const operations : PatchOperation[] = [ 
    { 
      op: "add", 
      path: "/newImproved", 
      value: "it works" 
    } 
  ]; 

const condition = "from c where NOT IS_DEFINED(c.newImproved)"; 

const { resource: patchSource3 } = await container 
    .item(patchSource.id) 
    .patch({ condition, operations }); 

console.log(`Patched ${patchSource3} to new ${patchSource3}.`); 
```

## <a name="support-for-server-side-programming"></a>Prise en charge de la programmation côté serveur

Les opérations de Mise à jour partielle de document peuvent également être [exécutées côté serveur](stored-procedures-triggers-udfs.md) à l’aide de procédures stockées, de déclencheurs et de fonctions définies par l’utilisateur.


```javascript
        this.patchDocument = function (documentLink, patchSpec, options, callback) { 
                if (arguments.length < 2) { 
                    throw new Error(ErrorCodes.BadRequest, sprintf(errorMessages.invalidFunctionCall, 'patchDocument', 2, arguments.length)); 
                }
                if (patchSpec === null || !(typeof patchSpec === "object" || Array.isArray(patchSpec))) { 
                    throw new Error(ErrorCodes.BadRequest, errorMessages.patchSpecMustBeObjectOrArray); 
                } 

                var documentIdTuple = validateDocumentLink(documentLink, false); 
                var collectionRid = documentIdTuple.collId; 
                var documentResourceIdentifier = documentIdTuple.docId; 
                var isNameRouted = documentIdTuple.isNameRouted; 

                patchSpec = JSON.stringify(patchSpec); 
                var optionsCallbackTuple = validateOptionsAndCallback(options, callback); 

                options = optionsCallbackTuple.options; 
                callback = optionsCallbackTuple.callback; 

                var etag = options.etag || ''; 
                var indexAction = options.indexAction || ''; 

                return collectionObjRaw.patch( 
                    collectionRid, 
                    documentResourceIdentifier, 
                    isNameRouted, 
                    patchSpec, 
                    etag, 
                    indexAction, 
                    function (err, response) { 
                        if (callback) { 
                            if (err) { 
                                callback(err); 
                            } else { 
                                callback(undefined, JSON.parse(response.body), response.options); 
                            } 
                        } else { 
                            if (err) { 
                                throw err; 
                            } 
                        } 
                    } 
                ); 
            }; 
```

## <a name="troubleshooting"></a>Dépannage

Voici une liste des erreurs courantes que vous pourriez rencontrer quand vous utilisez cette fonctionnalité :

| **Message d’erreur** | **Description** |
| ------------ | -------- |
| Demande de patch non valide : vérifier la syntaxe de la spécification du patch| La syntaxe de l’opération de patch n’est pas valide. Voir la [spécification](partial-document-update.md#partial-document-update-specification)
| Demande de patch non valide : impossible de corriger la propriété système `SYSTEM_PROPERTY`. | La mise à jour corrective des propriétés générées par le système, comme  `_id`, `_ts`, `_etag`, `_rid`, n’est pas prise en charge. Pour en savoir plus : [FAQ sur la Mise à jour partielle de document](partial-document-update-faq.yml#is-partial-document-update-supported-for-system-generated-properties-) 
| Le nombre d’opérations de patch ne peut pas dépasser 10 | Il existe une limite de 10 opérations de patch qui peut être ajoutée dans une seule spécification de patch. Pour en savoir plus : [FAQ sur la Mise à jour partielle de document](partial-document-update-faq.yml#is-there-a-limit-to-the-number-of-partial-document-update-operations-)
| Pour Operation(`PATCH_OPERATION_INDEX`) : l’Index(`ARRAY_INDEX`) à utiliser est en dehors des limites du tableau | L’index de l’élément de tableau à corriger est hors limites 
| Pour Operation(`PATCH_OPERATION_INDEX`) : le Node(`PATH`) à remplacer a été supprimé plus tôt dans la transaction.| Le chemin que vous essayez de corriger n’existe pas.
| Pour Operation(`PATCH_OPERATION_INDEX`) : le Node(`PATH`) à supprimer est absent. Remarque : Il a aussi pu être supprimé plus tôt dans la transaction.  | Le chemin que vous essayez de corriger n’existe pas.
| Pour Operation(`PATCH_OPERATION_INDEX`) : le Node(`PATH`) à remplacer est absent. | Le chemin que vous essayez de corriger n’existe pas.
| Pour Operation(`PATCH_OPERATION_INDEX`) : le Node(`PATH`) n’est pas un chiffre.| L’opération d’incrémentation peut fonctionner avec des valeurs entières et à virgule flottante. Pour en savoir plus : [Opérations prises en charge](partial-document-update.md#supported-operations)
| Pour Operation(`PATCH_OPERATION_INDEX`) : l’opération Add peut uniquement créer un objet enfant d’un nœud existant (tableau ou objet) et ne peut pas créer de chemin de manière récursive, aucun chemin trouvé au-delà de : `PATH`. | Les chemins enfants peuvent être ajoutés à un type de nœud d’objet ou de tableau. De plus, pour créer l’enfant `n`, l’enfant `n-1` doit être présent. 
| Pour Operation(`PATCH_OPERATION_INDEX`) : l’opération donnée peut uniquement créer un objet enfant d’un nœud existant (tableau ou objet) et ne peut pas créer de chemin de manière récursive, aucun chemin trouvé au-delà de : `PATH`. | Les chemins enfants peuvent être ajoutés à un type de nœud d’objet ou de tableau. De plus, pour créer l’enfant `n`, l’enfant `n-1` doit être présent. 

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la vue d’ensemble conceptuelle de la [Mise à jour partielle de document](partial-document-update.md)
