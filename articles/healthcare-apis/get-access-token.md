---
title: Obtient un jeton d’accès à l’aide de Azure CLI ou Azure PowerShell
description: Cet article explique comment obtenir un jeton d’accès pour les API de santé à l’aide de l’Azure CLI ou Azure PowerShell.
services: healthcare-apis
author: SteveWohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 11/17/2021
ms.author: zxue
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d350669abca87156cc5e735151f4a905d98745a8
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132814585"
---
# <a name="get-access-token-using-azure-cli-or-azure-powershell"></a>Obtient un jeton d’accès à l’aide de Azure CLI ou Azure PowerShell

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Dans cet article, vous allez apprendre à obtenir un jeton d’accès pour le service FHIR et le service DICOM à l’aide de PowerShell et de l’Azure CLI. Pour plus d’informations sur la prise en main des API de santé, consultez [la page prise]() en main de FHIR ou prise en main de [DICOM](). 

---
## <a name="obtain-a-token-for-the-fhir-service"></a>Obtenir un jeton pour le service FHIR

Le service FHIR utilise un `resource`  ou un `Audience` URI égal à l’URI du serveur FHIR `https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com` . Vous pouvez obtenir un jeton et le stocker dans une variable (nommée `$token`) avec la commande suivante :

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
token=$(az account get-access-token --resource=https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com --query accessToken --output tsv)
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$token = (Get-AzAccessToken -ResourceUrl 'https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com').Token
```

---

## <a name="use-the-token-with-the-fhir-service"></a>Utiliser le jeton avec le service FHIR

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
curl -X GET --header "Authorization: Bearer $token" https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$headers = @{Authorization="Bearer $token"}
Invoke-WebRequest -Method GET -Headers $headers -Uri 'https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient'
```

## <a name="obtain-a-token-for-the-dicom-service"></a>Obtenir un jeton pour le service DICOM

Le service DICOM utilise un `resource` ou un `Audience` URI égal à l’URI du serveur DICOM  `https://dicom.healthcareapis.azure.com` . Vous pouvez obtenir un jeton et le stocker dans une variable (nommée `$token`) avec la commande suivante :


```Azure CLICopy
$token=$(az account get-access-token --resource=https://dicom.healthcareapis.azure.com --query accessToken --output tsv)
```

## <a name="use-the-tokenb-with-the-dicom-service"></a>Utilisation de tokenb avec le service DICOM

```Azure CLICopy
curl -X GET --header "Authorization: Bearer $token"  https://<workspacename-dicomservicename>.dicom.azurehealthcareapis.com/v<version of REST API>/changefeed
```

## <a name="next-steps"></a>Étapes suivantes

- [Accéder à FHIR à l’aide de poster](use-postman.md)
- [Accéder au client Rest FHIR chanter](using-rest-client.md)
- [Accéder à DICOM à l’aide de la boucle](dicom/dicomweb-standard-apis-curl.md)

