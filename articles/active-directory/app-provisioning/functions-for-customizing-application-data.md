---
title: Informations de référence sur l’écriture d’expressions pour les mappages d’attributs dans l’approvisionnement des applications Azure Active Directory
description: Découvrez comment utiliser les mappages d’expressions pour transformer des valeurs d’attributs dans un format acceptable lors de l’approvisionnement automatique des objets d’application SaaS dans Azure Active Directory. Contient une liste de références de fonctions.
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 10/27/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 83b6e19a1e67e2e7e018aaa43ba4cf6149940ed7
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132292677"
---
# <a name="reference-for-writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Informations de référence sur l’écriture d’expressions pour les mappages d’attributs dans Azure Active Directory

Quand vous configurez l’approvisionnement pour une application SaaS, l’un des types de mappages d’attributs que vous pouvez spécifier est un mappage d’expression. Pour ces mappages, vous devez écrire une expression semblable à un script, qui vous permet de transformer les données des utilisateurs dans des formats plus acceptables pour l’application SaaS.

## <a name="syntax-overview"></a>Vue d’ensemble de la syntaxe

La syntaxe des expressions pour les mappages d’attributs rappelle celle des fonctions Visual Basic pour Applications (VBA).

* L’expression entière doit être définie en termes de fonctions, qui sont constituées d’un nom suivi d’arguments entre parenthèses : *FunctionName(`<<argument 1>>`,`<<argument N>>`)*
* Vous pouvez imbriquer des fonctions dans d’autres. Par exemple :  *FunctionOne(FunctionTwo(`<<argument1>>`))*
* Vous pouvez passer trois différents types d’arguments dans des fonctions :
  
  1. Des attributs, qui doivent être placés entre crochets. Par exemple : [nom_attribut]
  2. Des constantes de chaîne, qui doivent être placées entre des guillemets doubles. Par exemple : « États-Unis »
  3. D’autres fonctions. Par exemple : FunctionOne(`<<argument1>>`, FunctionTwo(`<<argument2>>`))
* Pour les constantes de chaîne, si vous avez besoin d’une barre oblique inverse (\) ou d’un guillemet (") dans la chaîne, vous devez le faire précéder du symbole de barre oblique inverse (\). Par exemple : « Nom de l’entreprise : \\"Contoso"\\ »
* La syntaxe respecte la casse, ce qui doit être pris en compte lors de leur saisie sous forme de chaînes dans une fonction, plutôt que de les copier-coller directement à partir de cet emplacement. 

## <a name="list-of-functions"></a>Liste des fonctions

[Append](#append) &nbsp;&nbsp;&nbsp;&nbsp; [AppRoleAssignmentsComplex](#approleassignmentscomplex) &nbsp;&nbsp;&nbsp;&nbsp; [BitAnd](#bitand) &nbsp;&nbsp;&nbsp;&nbsp; [CBool](#cbool) &nbsp;&nbsp;&nbsp;&nbsp; [CDate](#cdate) &nbsp;&nbsp;&nbsp;&nbsp; [Coalesce](#coalesce) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToBase64](#converttobase64) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToUTF8Hex](#converttoutf8hex) &nbsp;&nbsp;&nbsp;&nbsp; [Count](#count) &nbsp;&nbsp;&nbsp;&nbsp; [CStr](#cstr) &nbsp;&nbsp;&nbsp;&nbsp; [DateAdd](#dateadd) &nbsp;&nbsp;&nbsp;&nbsp; [DateDiff](#datediff) &nbsp;&nbsp;&nbsp;&nbsp; [DateFromNum](#datefromnum) &nbsp;[FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [Guid](#guid) &nbsp;&nbsp;&nbsp;&nbsp; [IgnoreFlowIfNullOrEmpty](#ignoreflowifnullorempty) &nbsp;&nbsp;&nbsp;&nbsp;[IIF](#iif) &nbsp;&nbsp;&nbsp;&nbsp;[InStr](#instr) &nbsp;&nbsp;&nbsp;&nbsp; [IsNull](#isnull) &nbsp;&nbsp;&nbsp;&nbsp; [IsNullOrEmpty](#isnullorempty) &nbsp;&nbsp;&nbsp;&nbsp; [IsPresent](#ispresent) &nbsp;&nbsp;&nbsp;&nbsp; [IsString](#isstring) &nbsp;&nbsp;&nbsp;&nbsp; [Item](#item) &nbsp;&nbsp;&nbsp;&nbsp; [Join](#join) &nbsp;&nbsp;&nbsp;&nbsp; [Left](#left) &nbsp;&nbsp;&nbsp;&nbsp; [Mid](#mid) &nbsp;&nbsp;&nbsp;&nbsp; [NormalizeDiacritics](#normalizediacritics) &nbsp;&nbsp; &nbsp;&nbsp; [Not](#not) &nbsp;&nbsp;&nbsp;&nbsp; [Now](#now) &nbsp;&nbsp;&nbsp;&nbsp; [NumFromDate](#numfromdate) &nbsp;&nbsp;&nbsp;&nbsp; [PCase](#pcase) &nbsp;&nbsp;&nbsp;&nbsp; [RandomString](#randomstring) &nbsp;&nbsp;&nbsp;&nbsp; [RemoveDuplicates](#removeduplicates) &nbsp;&nbsp;&nbsp;&nbsp; [Replace](#replace) &nbsp;&nbsp;&nbsp;&nbsp; [SelectUniqueValue](#selectuniquevalue)&nbsp;&nbsp;&nbsp;&nbsp; [SingleAppRoleAssignment](#singleapproleassignment)&nbsp;&nbsp;&nbsp;&nbsp; [Split](#split)&nbsp;&nbsp;&nbsp;&nbsp;[StripSpaces](#stripspaces) &nbsp;&nbsp;&nbsp;&nbsp; [Switch](#switch)&nbsp;&nbsp;&nbsp;&nbsp; [ToLower](#tolower)&nbsp;&nbsp;&nbsp;&nbsp; [ToUpper](#toupper)&nbsp;&nbsp;&nbsp;&nbsp; [Word](#word)

---
### <a name="append"></a>Ajouter

**Fonction :**  Append(source, suffixe)

**Description :**  prend une valeur de chaîne source et ajoute le suffixe à la fin de celle-ci.

**Paramètres :**

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **source** |Obligatoire |String |Généralement le nom de l’attribut de l’objet source. |
| **suffix** |Obligatoire |String |Chaîne que vous souhaitez ajouter à la fin de la valeur source. |


#### <a name="append-constant-suffix-to-user-name"></a>Ajouter un suffixe de constante à un nom d’utilisateur
Exemple : si vous utilisez un bac à sable Salesforce, vous devrez peut-être ajouter un autre suffixe à tous les noms d’utilisateurs avant de les synchroniser.

**Expression :**  
`Append([userPrincipalName], ".test")`

**Exemple d’entrée/sortie :** 

* **ENTRÉE** : (userPrincipalName) : "John.Doe@contoso.com"
* **SORTIE**:  "John.Doe@contoso.com.test"

---
### <a name="approleassignmentscomplex"></a>AppRoleAssignmentsComplex

**Fonction :** AppRoleAssignmentsComplex([appRoleAssignments])

**Description :** utilisée pour attribuer plusieurs rôles à un utilisateur Pour plus de détails, consultez [Tutoriel - Personnaliser les mappages d'attributs d'attribution d'utilisateurs pour les applications SaaS dans Azure Active Directory](customize-application-attributes.md#provisioning-a-role-to-a-scim-app).

**Paramètres :** 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |Obligatoire |String |Objet **[appRoleAssignments]** . |

---
### <a name="bitand"></a>BitAnd
**Fonction :** BitAnd(value1, value2)

**Description :**  Cette fonction convertit les deux paramètres de la représentation binaire et définit un bit sur :

- 0 - si un des bits, ou les deux bits correspondants dans value1 et value2 ont pour valeur 0
- 1 - si les deux bits correspondants sont définis sur 1.

En d’autres termes, elle renvoie 0 dans tous les cas, sauf si les bits correspondants de ces deux paramètres sont définis sur 1.

**Paramètres :** 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **value1** |Obligatoire |Num |Valeur numérique qui doit être liée par AND avec value2|
| **value2** |Obligatoire |Num |Valeur numérique qui doit être liée par AND avec value1|

**Exemple :** 
`BitAnd(&HF, &HF7)`

11110111 AND 00000111 = 00000111 donc `BitAnd` retourne 7, la valeur numérique correspondant à 00000111.

---
### <a name="cbool"></a>CBool
**Fonction :**  
`CBool(Expression)`

**Description :**  
`CBool` retourne une valeur booléenne basée sur l’expression évaluée. Si l’expression retourne une valeur autre que zéro, `CBool` retourne la valeur *True* ; sinon elle retourne *False*.

**Paramètres :** 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **Expression** |Obligatoire | expression | Toute expression valide |

**Exemple :** 
`CBool([attribute1] = [attribute2])`                                                                    
Retourne True si les attributs ont la même valeur.

---
### <a name="cdate"></a>CDate
**Fonction :**  
`CDate(expression)`

**Description :**  
La fonction CDate renvoie une valeur DateTime UTC à partir d’une chaîne. DateTime n'est pas un type d'attribut natif, mais il peut être utilisé dans des fonctions de date telles que [FormatDateTime](#formatdatetime) et [DateAdd](#dateadd).

**Paramètres :** 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **Expression** |Obligatoire | Expression | Toute chaîne valide représentant une date/heure. Pour connaître les formats pris en charge, reportez-vous à [Chaînes de format de date et d'heure personnalisées .NET](/dotnet/standard/base-types/custom-date-and-time-format-strings). |

**Remarques :**  
La chaîne renvoyée est toujours au format UTC **M/j/aaaa h:mm:ss tt**.

**Exemple 1 :** <br> 
`CDate([StatusHireDate])`  
**Exemple d’entrée/sortie :** 

* **INPUT** (StatusHireDate): "2020-03-16-07:00"
* **OUTPUT**:  "3/16/2020 7:00:00 AM" <-- *Notez que l'équivalent UTC de la valeur DateTime ci-dessus est renvoyé*

**Exemple 2 :** <br> 
`CDate("2021-06-30+08:00")`  
**Exemple d’entrée/sortie :** 

* **INPUT**: "2021-06-30+08:00"
* **OUTPUT**:  "6/29/2021 4:00:00 PM" <-- *Notez que l'équivalent UTC de la valeur DateTime ci-dessus est renvoyé*

**Exemple 3 :** <br> 
`CDate("2009-06-15T01:45:30-07:00")`  
**Exemple d’entrée/sortie :** 

* **INPUT**: "2009-06-15T01:45:30-07:00"
* **OUTPUT**:  "6/15/2009 8:45:30 AM" <-- *Notez que l'équivalent UTC de la valeur DateTime ci-dessus est renvoyé*

---
### <a name="coalesce"></a>Coalesce
**Fonction :** Coalesce(source1, source2, ..., defaultValue)

**Description :** Renvoie la première valeur source qui n’est pas NULL. Si tous les arguments ont la valeur NULL et que defaultValue est présent, defaultValue est renvoyé. Si tous les arguments ont la valeur NULL et que defaultValue n’est pas présent, Coalesce renvoie la valeur NULL.

**Paramètres :** 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **source1  … sourceN** | Obligatoire | String |Requis, nombre de fois variable. Généralement le nom de l’attribut de l’objet source. |
| **defaultValue** | Facultatif | String | Valeur par défaut à utiliser lorsque toutes les valeurs sources sont NULL. Peut être une chaîne vide ("").

#### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>Valeur de courrier dynamique si non NULL ; dans le cas contraire, transmission de userPrincipalName
Exemple : vous souhaitez transmettre l’attribut de messagerie, s’il est présent. Si ce n’est pas le cas, vous souhaitez transmettre la valeur de userPrincipalName à la place.

**Expression :**  
`Coalesce([mail],[userPrincipalName])`

**Exemple d’entrée/sortie :** 

* **ENTRÉE** (mail) : NULL
* **ENTRÉE** (userPrincipalName) : « John.Doe@contoso.com »
* **SORTIE**:  "John.Doe@contoso.com"


---
### <a name="converttobase64"></a>ConvertToBase64
**Fonction :** ConvertToBase64(source)

**Description :**  La fonction ConvertToBase64 convertit une chaîne en chaîne Unicode base64.

**Paramètres :** 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **source** |Obligatoire |String |Chaîne à convertir en base 64|

**Exemple :** 
`ConvertToBase64("Hello world!")`

 Renvoie « SABlAGwAbABvACAAdwBvAHIAbABkACEA ».

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Fonction :** ConvertToUTF8Hex(source)

**Description :**  La fonction ConvertToUTF8Hex convertit une chaîne en valeur hexadécimale encodée UTF8.

**Paramètres :** 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **source** |Obligatoire |String |Chaîne à convertir en hexadécimale UTF8|

**Exemple :** 
`ConvertToUTF8Hex("Hello world!")`

 Renvoie 48656C6C6F20776F726C6421.

---
### <a name="count"></a>Count
**Fonction :** Count(attribute)

**Description :**  La fonction Count renvoie le nombre d’éléments dans un attribut à valeurs multiples.

**Paramètres :** 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **attribute** |Obligatoire |attribut |Attribut à valeurs multiples dont les éléments seront comptés|

---
### <a name="cstr"></a>CChaîne
**Fonction :** CStr(value)

**Description :** la fonction CStr convertit une valeur en un type de données de chaîne.

**Paramètres :** 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **value** |Obligatoire | numérique, référence ou booléenne | Peut être une valeur numérique, un attribut de référence ou une valeur booléenne. |

**Exemple :** 
`CStr([dn])`

Renvoie « cn=Joe,dc=contoso,dc=com »

---
### <a name="dateadd"></a>AjDate
**Fonction :**  
`DateAdd(interval, value, dateTime)`

**Description :**  
Renvoie une chaîne date/heure qui représente une date à laquelle un intervalle de temps spécifié a été ajouté. La date renvoyée est au format : **M/j/aaaa h:mm:ss tt**.

**Paramètres :** 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **interval** |Obligatoire | String | Intervalle de temps que vous souhaitez ajouter. Voir les valeurs acceptées sous ce tableau. |
| **value** |Obligatoire | Number | Nombre d’unités que vous souhaitez ajouter. Elle peut être positive (pour obtenir des dates dans le futur) ou négative (pour obtenir des dates dans le passé). |
| **dateTime** |Obligatoire | DateTime | DateTime représentant la date à laquelle l’intervalle est ajouté. |

La chaîne **interval** doit comporter l'une des valeurs suivantes : 
 * yyyy Année 
 * m Mois
 * s Jour
 * ww Semaine
 * h Heure
 * n Minute
 * s Seconde

**Exemple 1 : Ajouter 7 jours à la date d'embauche**  
`DateAdd("d", 7, CDate([StatusHireDate]))`
* **INPUT** (StatusHireDate): 2012-03-16-07:00
* **OUTPUT**: 3/23/2012 7:00:00 AM

**Exemple 2 : Obtenir une date dix jours avant la date d’embauche**  
`DateAdd("d", -10, CDate([StatusHireDate]))`
* **INPUT** (StatusHireDate): 2012-03-16-07:00
* **OUTPUT**: 3/6/2012 7:00:00 AM

**Exemple 3 : Ajouter deux semaines à la date d’embauche**  
`DateAdd("ww", 2, CDate([StatusHireDate]))`
* **INPUT** (StatusHireDate): 2012-03-16-07:00
* **OUTPUT**: 3/30/2012 7:00:00 AM

**Exemple 4 : Ajouter 10 mois à la date d’embauche**  
`DateAdd("m", 10, CDate([StatusHireDate]))`
* **INPUT** (StatusHireDate): 2012-03-16-07:00
* **OUTPUT**: 1/16/2013 7:00:00 AM

**Exemple 5 : Ajouter deux ans à la date d’embauche**  
`DateAdd("yyyy", 2, CDate([StatusHireDate]))`
* **INPUT** (StatusHireDate): 2012-03-16-07:00
* **OUTPUT**: 3/16/2014 7:00:00 AM
---
### <a name="datediff"></a>DiffDate
**Fonction :**  
`DateDiff(interval, date1, date2)`

**Description :**  
Cette fonction utilise le paramètre *intervalle* pour renvoyer un nombre indiquant la différence entre les deux dates d’entrée. Il retourne  
  * un nombre positif si date2 > date1, 
  * un nombre négatif si date2 < date1, 
  * 0 si date2 = = date1

**Paramètres :** 

| Nom | Obligatoire ou facultatif | Type | Notes |
| --- | --- | --- | --- |
| **interval** |Obligatoire | String | Intervalle de temps à utiliser pour calculer la différence. |
| **date1** |Obligatoire | DateTime | DateTime représentant une date valide. |
| **date2** |Obligatoire | DateTime | DateTime représentant une date valide. |

La chaîne **interval** doit comporter l'une des valeurs suivantes : 
 * yyyy Année 
 * m Mois
 * s Jour
 * ww Semaine
 * h Heure
 * n Minute
 * s Seconde

**Exemple 1 : comparer la date actuelle avec la date d’embauche de Workday avec des intervalles différents** <br>
`DateDiff("d", Now(), CDate([StatusHireDate]))`

| Exemple | interval | date1 | date2 | sortie |
| --- | --- | --- | --- | --- |
| Différence positive en jours entre deux dates | d | 2021-08-18+08:00 | 2021-08-31+08:00 | 13 |
| Différence négative en jours entre deux dates | d | 25/08/2021 17:41:18 | 2012-03-16-07:00 | -3449 |
| Différence en semaines entre deux dates | ww | 25/08/2021 17:41:18 | 2012-03-16-07:00 | -493 | 
| Différence en mois entre deux dates | m | 25/08/2021 17:41:18 | 2012-03-16-07:00 | -113 | 
| Différence en années entre deux dates | aaaa | 25/08/2021 17:41:18 | 2012-03-16-07:00 | -9 | 
| Différence lorsque les deux dates sont identiques | d | 2021-08-31+08:00 | 2021-08-31+08:00 | 0 | 
| Différence en heures entre deux dates | h | 2021-08-24 | 2021-08-25 | 24 | 
| Différence en minutes entre deux dates | n | 2021-08-24 | 2021-08-25 | 1440 | 
| Différence en secondes entre deux dates | s | 2021-08-24 | 2021-08-25 | 86400 | 

**Exemple 2 : combiner DateDiff avec la fonction IIF pour définir la valeur de l’attribut** <br>
Si un compte est actif dans Workday, affectez à l’attribut *accountEnabled* de l’utilisateur la valeur true uniquement si la date d’embauche est comprise dans les cinq prochains jours. 

```
Switch([Active], , 
  "1", IIF(DateDiff("d", Now(), CDate([StatusHireDate])) > 5, "False", "True"), 
  "0", "False")
```

---

### <a name="datefromnum"></a>DateFromNum
**Fonction :** DateFromNum(value)

**Description :** la fonction DateFromNum convertit une valeur au format de date AD en un type DateTime.

**Paramètres :** 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **value** |Obligatoire | Date | Date AD à convertir en type DateTime |

**Exemple :** 
`DateFromNum([lastLogonTimestamp])`

`DateFromNum(129699324000000000)`

Retourne une valeur DateTime représentant le 1er janvier 2012 à 23:00.

---
### <a name="formatdatetime"></a>FormatDateTime
**Fonction :** FormatDateTime(source, dateTimeStyles, inputFormat, outputFormat)

**Description :**  prend une chaîne de date dans un format et la convertit dans un autre format.

**Paramètres :** 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **source** |Obligatoire |String |Généralement le nom de l’attribut de l’objet source. |
| **dateTimeStyles** | Facultatif | String | Utilisez ceci pour indiquer les options de mise en forme qui personnalisent l’analyse de chaîne pour certaines méthodes d’analyse de la date et de l’heure. Pour les valeurs prises en charge, consultez le [document DateTimeStyles](/dotnet/api/system.globalization.datetimestyles). Si vous laissez le champs vide, la valeur par défaut utilisée est DateTimeStyles.RoundtripKind; DateTimeStyles.AllowLeadingWhite, DateTimeStyles.AllowTrailingWhite  |
| **inputFormat** |Obligatoire |String |Format attendu de la valeur source. Pour connaître les formats pris en charge, consultez [Chaînes de format de date et d'heure personnalisées .NET](/dotnet/standard/base-types/custom-date-and-time-format-strings). |
| **outputFormat** |Obligatoire |String |Format de la date de sortie. |



#### <a name="output-date-as-a-string-in-a-certain-format"></a>Sortir une date sous la forme d’une chaîne dans un certain format
Exemple : vous souhaitez envoyer des dates à une application SaaS comme ServiceNow dans un format spécifique. Vous pouvez envisager d’utiliser l’expression suivante. 

**Expression :** 

`FormatDateTime([extensionAttribute1], , "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Exemple d’entrée/sortie :**

* **ENTRÉE** (extensionAttribute1) : "20150123105347.1Z"
* **SORTIE** :  "2015-01-23"


---
### <a name="guid"></a>Guid
**Fonction :** Guid()

**Description :** La fonction Guid génère un nouveau GUID aléatoire.

**Exemple :** <br>
`Guid()`<br>
Exemple de sortie : « 1088051a-cd4b-4288-84F8-e02042ca72bc »

---
### <a name="ignoreflowifnullorempty"></a>IgnoreFlowIfNullOrEmpty
**Fonction :** IgnoreFlowIfNullOrEmpty(expression)

**Description :** la fonction IgnoreFlowIfNullOrEmpty indique au service d'approvisionnement d'ignorer l'attribut et de le supprimer du flux si la fonction ou l'attribut inclus est NULL ou vide.

**Paramètres :** 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **Expression** | Obligatoire | Expression | Expression à évaluer |

**Exemple 1 : Ne pas inclure un attribut dans le flux s'il est Null** <br>
`IgnoreFlowIfNullOrEmpty([department])` <br>
L'expression ci-dessus supprimera l'attribut « department » du flux d'approvisionnement s'il est Null ou vide. <br>

**Exemple 2 : Ne pas inclure un attribut dans le flux si le mappage d'expressions correspond à une chaîne vide ou Null** <br>
Supposons que l'attribut SuccessFactors *prefix* soit mappé à l'attribut *personalTitle* de l'instance locale d'Active Directory à l'aide du mappage d'expressions suivant : <br>
`IgnoreFlowIfNullOrEmpty(Switch([prefix], "", "3443", "Dr.", "3444", "Prof.", "3445", "Prof. Dr."))` <br>
L'expression ci-dessus évalue d'abord la fonction [Switch](#switch). Si l'attribut *prefix* ne possède aucune des valeurs répertoriées dans la fonction *Switch*, *Switch* renvoie une chaîne vide et l'attribut *personalTitle* n'est pas inclus dans le flux d'approvisionnement de l'instance locale d'Active Directory.

---
### <a name="iif"></a>IIF
**Fonction :** IIF(condition,valueIfTrue,valueIfFalse)

**Description :**  La fonction IIF renvoie une valeur parmi un ensemble de valeurs possibles en fonction d’une condition spécifiée.

**Paramètres :** 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **condition** |Obligatoire |Variable ou expression |Toute valeur ou expression pouvant prendre une valeur true ou false. |
| **valueIfTrue** |Obligatoire |Variable ou chaîne | La valeur renvoyée si la condition prend la valeur true. |
| **valueIfFalse** |Obligatoire |Variable ou chaîne |La valeur renvoyée si la condition prend la valeur false.|

**Exemple :** 
`IIF([country]="USA",[country],[department])`

---
### <a name="instr"></a>InStr
**Fonction :** InStr(value1, value2, start, compareType)

**Description :**  La fonction InStr recherche la première occurrence d’une sous-chaîne dans une chaîne.

**Paramètres :** 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **value1** |Obligatoire |String |Chaîne dans laquelle rechercher |
| **value2** |Obligatoire |String |Chaîne à trouver |
| **start** |Facultatif |Integer |Position de départ pour trouver la sous-chaîne|
| **compareType** |Facultatif |Enum |Peut être vbTextCompare ou vbBinaryCompare |

**Exemple :** 
`InStr("The quick brown fox","quick")`

Prend la valeur 5

`InStr("repEated","e",3,vbBinaryCompare)`

 Prend la valeur 7.

---
### <a name="isnull"></a>IsNull
**Fonction :** IsNull(Expression)

**Description :**  La fonction IsNull renvoie true si l’expression correspond à la valeur Null.  Dans le cas d’un attribut, la valeur Null est exprimée par l’absence de ce dernier.

**Paramètres :** 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **Expression** |Obligatoire |Expression |Expression à évaluer |

**Exemple :** 
`IsNull([displayName])`

Retourne True si l’attribut est absent.

---
### <a name="isnullorempty"></a>IsNullorEmpty
**Fonction :** IsNullOrEmpty(Expression)

**Description :**  La fonction IsNullOrEmpty renvoie la valeur true si l’expression a pour valeur Null ou s’il s’agit d’une chaîne vide.  Dans le cas d’un attribut, cela donne la valeur True si l’attribut est absent ou est présent mais qu’il s’agit d’une chaîne vide.
L’inverse de cette fonction est nommé IsPresent.

**Paramètres :** 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **Expression** |Obligatoire |Expression |Expression à évaluer |

**Exemple :** 
`IsNullOrEmpty([displayName])`

Retourne True si l’attribut est absent ou s’il s’agit d’une chaîne vide.

---
### <a name="ispresent"></a>IsPresent
**Fonction :** IsPresent(Expression)

**Description :**  La fonction IsPresent renvoie true si l’expression correspond à une chaîne qui n’a pas la valeur Null et n’est pas vide.  L’inverse de cette fonction est appelé IsNullOrEmpty.

**Paramètres :** 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **Expression** |Obligatoire |Expression |Expression à évaluer |

**Exemple :** 
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="isstring"></a>IsString
**Fonction :** IsString(Expression)

**Description :**  La fonction IsString prend la valeur True si l’expression peut être évaluée en tant que type de chaîne.

**Paramètres :** 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **Expression** |Obligatoire |Expression |Expression à évaluer |

---
### <a name="item"></a>Élément
**Fonction :** Item(attribute, index)

**Description :**  La fonction Item renvoie un élément à partir d’une chaîne/d’un attribut à valeurs multiples.

**Paramètres :** 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **attribute** |Obligatoire |Attribut |Attribut à valeurs multiples à rechercher |
| **index** |Obligatoire |Integer | Index d’un élément dans la chaîne à valeurs multiples|

**Exemple :** 
`Item([proxyAddresses], 1)` retourne le premier élément dans l’attribut à valeurs multiples. Index 0 ne doit pas être utilisé. 

---
### <a name="join"></a>Join
**Fonction :**  Join(séparateur, source1, source2, …)

**Description :** Join() est similaire à Append(), mais elle peut combiner plusieurs valeurs de chaîne **sources** dans une même chaîne et chaque valeur sera séparée par une chaîne de **séparation**.

Si l’une des valeurs sources est un attribut à valeurs multiples, toutes les valeurs de cet attribut seront jointes, séparées par la valeur de séparation.

**Paramètres :** 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **separator** |Obligatoire |String |Chaîne utilisée pour séparer les valeurs sources quand elles sont concaténées en une seule chaîne. Peut être "" si aucun séparateur n’est requis. |
| **source1  … sourceN** |Requis, nombre de fois variable |String |Valeurs de chaîne à joindre ensemble. |

---
### <a name="left"></a>Gauche
**Fonction :** Left(String, NumChars)

**Description :**  La fonction Left renvoie un nombre spécifié de caractères en partant de la gauche d’une chaîne. Si numChars = 0, retourne une chaîne vide.
Si numChars < 0, retourne une chaîne d’entrée.
Si la chaîne est null, retourne une chaîne vide.
Si la chaîne contient moins de caractères que le nombre spécifié dans numChars, une chaîne identique à la chaîne (c’est-à-dire, contenant tous les caractères du paramètre 1) est renvoyée.

**Paramètres :** 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **Chaîne** |Obligatoire |Attribut | Chaîne à partir de laquelle renvoyer des caractères |
| **NumChars** |Obligatoire |Integer | Un nombre identifiant le nombre de caractères à retourner à partir du début (gauche) de la chaîne|

**Exemple :** 
`Left("John Doe", 3)`

Renvoie « Joh ».

---
### <a name="mid"></a>ExtracChaîne
**Fonction :**  Mid(source, début, longueur)

**Description :**  retourne une sous-chaîne de la valeur source. Une sous-chaîne est une chaîne qui ne contient que certains des caractères de la chaîne source.

**Paramètres :** 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **source** |Obligatoire |String |Généralement le nom de l’attribut. |
| **start** |Obligatoire |Integer |Index dans la chaîne **source** où la sous-chaîne doit commencer. Le premier caractère dans la chaîne aura l’index 1, le deuxième caractère aura l’index 2, et ainsi de suite. |
| **length** |Obligatoire |Integer |Longueur de la sous-chaîne. Si la longueur se termine à l’extérieur de la chaîne **source**, la fonction retourne la sous-chaîne de l’index **start** jusqu’à la fin de la chaîne **source**. |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Fonction :** NormalizeDiacritics(source)

**Description :** Nécessite un argument de chaîne. Retourne la chaîne, où les caractères diacritiques ont été remplacés par leurs équivalents non diacritiques. Généralement utilisé pour convertir les prénoms et les noms contenant des caractères diacritiques (accents) en valeurs autorisées pouvant être utilisées dans différents identificateurs d’utilisateurs, tels que les noms d’utilisateurs principaux, les noms de compte SAM et les adresses e-mail.

**Paramètres :** 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **source** |Obligatoire |String | Généralement un attribut de nom ou de prénom. |


| Caractère avec signe diacritique  | Caractère normalisé | Caractère avec signe diacritique  | Caractère normalisé | 
| --- | --- | --- | --- | 
| ä, à, â, ã, å, á, ą, ă, ā, ā́, ā̀, ā̂, ā̃, ǟ, ā̈, ǡ, a̱, å̄ | a | Ä, À, Â, Ã, Å, Á, Ą, Ă, Ā, Ā́, Ā̀, Ā̂, Ā̃, Ǟ, Ā̈, Ǡ, A̱, Å̄ | Un | 
| æ, ǣ | ae | Æ, Ǣ | AE | 
| ç, č, ć, c̄, c̱ | c | Ç, Č, Ć, C̄, C̱ | C | 
| ď, d̄, ḏ | d | Ď, D̄, Ḏ | D | 
| ë, è, é, ê, ę, ě, ė, ē, ḗ, ḕ, ē̂, ē̃, ê̄, e̱, ë̄, e̊̄ | e | Ë, È, É, Ê, Ę, Ě, Ė, Ē, Ḗ, Ḕ, Ē̂, Ē̃, Ê̄, E̱, Ë̄, E̊̄ | E | 
| ğ, ḡ, g̱ | g | Ğ, Ḡ, G̱ | G | 
| ï, î, ì, í, ı, ī, ī́, ī̀, ī̂, ī̃, i̱ | i | Ï, Î, Ì, Í, İ, Ī, Ī́, Ī̀, Ī̂, Ī̃, I̱ | I |  
| ľ, ł, l̄, ḹ, ḻ | l |  Ł, Ľ, L̄, Ḹ, Ḻ | L | 
| ñ, ń, ň, n̄, ṉ | n |  Ñ, Ń, Ň, N̄, Ṉ | N | 
| ö, ò, ő, õ, ô, ó, ō, ṓ, ṑ, ō̂, ō̃, ȫ, ō̈, ǭ, ȭ, ȱ, o̱ | o |  Ö, Ò, Ő, Õ, Ô, Ó, Ō, Ṓ, Ṑ, Ō̂, Ō̃, Ȫ, Ō̈, Ǭ, Ȭ, Ȱ, O̱ | O | 
| ø, ø̄, œ̄  | oe |  Ø, Ø̄, Œ̄ | OE | 
| ř, r̄, ṟ, ṝ | r |  Ř, R̄, Ṟ, Ṝ | R | 
| ß | ss | | | 
| š, ś, ș, ş, s̄, s̱ | s |  Š, Ś, Ș, Ş, S̄, S̱ | S | 
| ť, ț, t̄, ṯ | t | Ť, Ț, T̄, Ṯ | T | 
| ü, ù, û, ú, ů, ű, ū, ū́, ū̀, ū̂, ū̃, u̇̄, ǖ, ṻ, ṳ̄, u̱ | u |  Ü, Ù, Û, Ú, Ů, Ű, Ū, Ū́, Ū̀, Ū̂, Ū̃, U̇̄, Ǖ, Ṻ, Ṳ̄, U̱ | U | 
| ÿ, ý, ȳ, ȳ́, ȳ̀, ȳ̃, y̱ | y | Ÿ, Ý, Ȳ, Ȳ́, Ȳ̀, Ȳ̃, Y̱ | O | 
| ź, ž, ż, z̄, ẕ | z | Ź, Ž, Ż, Z̄, Ẕ | Z | 


#### <a name="remove-diacritics-from-a-string"></a>Supprimer les signes diacritiques d’une chaîne
Exemple : remplacer les caractères accentués par leurs équivalents non accentués.

**Expression :** NormalizeDiacritics([givenName])

**Exemple d’entrée/sortie :** 

* **ENTRÉE** (givenName) : "Zoë"
* **SORTIE** :  "Zoe"


---
### <a name="not"></a>Not
**Fonction :**  Not(source)

**Description :** inverse la valeur booléenne de la **source**. Si la valeur **source** est True, la fonction retourne False. Sinon, elle retourne True.

**Paramètres :** 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **source** |Obligatoire |Chaîne de type Boolean |Les valeurs **sources** attendues sont « True » ou « False ». |

---
### <a name="now"></a>maintenant
**Fonction :** Now()

**Description :**  
la fonction Now renvoie une chaîne représentant la valeur DateTime UTC actuelle au format **M/j/aaaa h:mm:ss tt**.

**Exemple :** 
`Now()` <br>
Exemple de valeur renvoyée : *7/2/2021 3:33:38 PM*

---
### <a name="numfromdate"></a>NumFromDate
**Fonction :** NumFromDate(value)

**Description :** La fonction NumFromDate convertit une valeur DateTime au format Active Directory requis pour définir des attributs comme [accountExpires](/windows/win32/adschema/a-accountexpires). Utilisez cette fonction pour convertir les valeurs DateTime reçues d’applications cloud destinées aux ressources humaines telles que Workday et SuccessFactors en leur représentation AD équivalente. 

**Paramètres :** 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **value** |Obligatoire | String | Chaîne de date et d’heure dans le format pris en charge. Pour connaitre les formats pris en charge, consultez https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx. |

**Exemple :**
* Exemple Workday En supposant que vous souhaitiez mapper l’attribut *ContractEndDate* de Workday, qui est au format *2020-12-31-08:00*, au champ *accountExpires* dans AD, voici comment vous pouvez utiliser cette fonction et modifier le décalage de fuseau horaire pour qu’il corresponde à vos paramètres régionaux. 
  `NumFromDate(Join("", FormatDateTime([ContractEndDate], ,"yyyy-MM-ddzzz", "yyyy-MM-dd"), " 23:59:59-08:00"))`

* Exemple SuccessFactors En supposant que vous souhaitiez mapper l’attribut *endDate* de SuccessFactors, qui est au format *M/j/aaaa hh:mm:ss tt*, au champ *accountExpires* dans AD, voici comment vous pouvez utiliser cette fonction et modifier le décalage de fuseau horaire pour qu’il corresponde à vos paramètres régionaux.
  `NumFromDate(Join("",FormatDateTime([endDate], ,"M/d/yyyy hh:mm:ss tt","yyyy-MM-dd")," 23:59:59-08:00"))`


---
### <a name="pcase"></a>PCase
**Fonction :** PCase (source, wordSeparators)

**Description :** la fonction PCase met en majuscule le premier caractère de chaque mot d’une chaîne, et tous les autres caractères sont convertis en minuscules.

**Paramètres :** 

| Nom | Obligatoire ou facultatif | Type | Notes |
| --- | --- | --- | --- |
| **source** |Obligatoire |String |Valeur **source** à convertir dans la casse adéquate. |
| **wordSeparators** |Facultatif |String |Spécifiez un jeu de caractères à utiliser comme séparateurs de mots (exemple : " ,-'") |

**Remarques :**

* Si le paramètre *wordSeparators* n’est pas spécifié, PCase appelle en interne la fonction .NET [ToTitleCase](/dotnet/api/system.globalization.textinfo.totitlecase) pour convertir la chaîne *source* dans la casse adéquate. La fonction .NET *ToTitleCase* prend en charge un ensemble complet de [catégories de caractères Unicode](https://www.unicode.org/reports/tr44/#General_Category_Values) comme séparateurs de mots. 
  * Espace
  * Caractère de nouvelle ligne
  * Caractères de *contrôle* tels que CRLF
  * Caractères de contrôle de *format*
  * Caractères *ConnectorPunctuation* comme le trait de soulignement
  * Caractères *DashPunctuation* tels que le tiret et le trait d’union (y compris les caractères tels que le tiret demi-cadratin, le tiret cadratin, le double tiret, etc.)
  * Caractères *OpenPunctuation* et *ClosePunctuation* qui apparaissent par paires, comme les parenthèses, les accolades, les chevrons, etc. 
  * Caractères *InitialQuotePunctuation* et *FinalQuotePunctuation*, comme les guillemets simples, les guillemets doubles et les guillemets angulaires. 
  * Caractères *OtherPunctuation*, comme le point d’exclamation, le signe dièse, le signe pourcentage, l’esperluette, l’astérisque, la virgule, le point, le signe deux-points, le point-virgule, etc. 
  * Caractères *MathSymbol*, comme le signe plus, le signe inférieur à et le signe supérieur à, la ligne verticale, le tilde, le signe égal, etc.
  * Caractères *CurrencySymbol*, comme le signe du dollar, le signe du cent, le signe de la livre, le signe de l’euro, etc. 
  * Caractères *ModifierSymbol*, comme le macron, les accents, les flèches, etc. 
  * Caractères *OtherSymbol*, comme le signe de copyright, le signe de degré, le signe de marque déposée, etc. 
* Si le paramètre *wordSeparators* est spécifié, PCase utilise uniquement les caractères spécifiés comme séparateurs de mots. 

**Exemple :**

Supposons que vous approvisionniez les attributs *FirstName* et *LastName* auprès de SAP SuccessFactors et que dans HR, ces deux attributs sont en majuscules. À l’aide de la fonction PCase, vous pouvez convertir le nom dans la casse adéquate comme illustré ci-dessous. 

| Expression | Entrée | Sortie | Notes |
| --- | --- | --- | --- |
| `PCase([firstName])` | *firstName* = "PABLO GONSALVES (SECOND)" | "Pablo Gonsalves (Second)" | Comme le paramètre *wordSeparators* n’est pas spécifié, la fonction *PCase* utilise le jeu de caractères de séparation de mots par défaut. |
| `PCase([lastName]," '-")` | *lastName* = "PINTO-DE'SILVA" | "Pinto-De'Silva" | La fonction *PCase* utilise les caractères dans le paramètre *wordSeparators* pour identifier les mots et les convertir dans la casse adéquate. |
| `PCase(Join(" ",[firstName],[lastName]))` | *firstName* = GREGORY, *lastName* = "JAMES" | "Gregory James" | Vous pouvez imbriquer la fonction Join dans PCase. Comme le paramètre *wordSeparators* n’est pas spécifié, la fonction *PCase* utilise le jeu de caractères de séparation de mots par défaut.  |


---

### <a name="randomstring"></a>RandomString
**Fonction :** RandomString(Length, MinimumNumbers, MinimumSpecialCharacters , MinimumCapital, MinimumLowerCase, CharactersToAvoid)

**Description :** la fonction RandomString génère une chaîne aléatoire basée sur les conditions spécifiées. Les caractères autorisés sont énumérés [ici](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements#reference).

**Paramètres :** 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **Durée** |Obligatoire |Number |Longueur totale de la chaîne aléatoire. Cette valeur doit être supérieure ou égale à la somme de MinimumNumbers, MinimumSpecialCharacters et MinimumCapital. Maximum 256 caractères.|
| **MinimumNumbers** |Obligatoire |Number |Minimum de chiffres dans la chaîne aléatoire.|
| **MinimumSpecialCharacters** |Obligatoire |Number |Nombre minimal de caractères spéciaux.|
| **MinimumCapital** |Obligatoire |Number |Nombre minimal de lettres majuscules dans la chaîne aléatoire.|
| **MinimumLowerCase** |Obligatoire |Number |Nombre minimal de lettres minuscules dans la chaîne aléatoire.|
| **CharactersToAvoid** |Facultatif |String |Caractères à exclure lors de la génération de la chaîne aléatoire.|


**Exemple 1 :** générer une chaîne aléatoire sans restriction de caractères spéciaux : `RandomString(6,3,0,0,3)`
Génère une chaîne aléatoire de 6 caractères. La chaîne contient 3 chiffres et 3 caractères minuscules (1a73qt).

**Exemple 2 :** générer une chaîne aléatoire avec des restrictions de caractères spéciaux : `RandomString(10,2,2,2,1,"?,")`
Génère une chaîne aléatoire de 10 caractères. La chaîne contient au moins 2 chiffres, 2 caractères spéciaux, 2 lettres majuscules, 1 lettre minuscule, et exclut les caractères «  ? » et « , » (1@!2BaRg53).

---

### <a name="removeduplicates"></a>RemoveDuplicates
**Fonction :** RemoveDuplicates(attribute)

**Description :**  La fonction RemoveDuplicates prend une chaîne à valeurs multiples et vérifie que chaque valeur est unique.

**Paramètres :** 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **attribute** |Obligatoire |Attribut à valeurs multiples |Attribut à valeurs multiples dont les doublons seront supprimés|

**Exemple :** 
`RemoveDuplicates([proxyAddresses])`  Renvoie un attribut proxyAddress expurgé duquel toutes les valeurs en double ont été supprimées.

---
### <a name="replace"></a>Replace
**Fonction :** Remplacer (source, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, template)

**Description :** Remplace des valeurs dans une chaîne en respectant la casse. La fonction se comporte différemment selon les paramètres fournis :

* Quand **oldValue** et **replacementValue** sont fournis :
  
  * Remplace toutes les occurrences d’**oldValue** dans la **source** par  **replacementValue**.
* Quand **oldValue** et **template** sont fournis :
  
  * Remplace toutes les occurrences d’**oldValue** dans le **template** par la valeur **source**.
* Quand **regexPattern** et **replacementValue** sont fournis :

  * La fonction applique **regexPattern** à la chaîne **source** et vous pouvez utiliser les noms de groupes regex pour construire la chaîne pour **replacementValue**.
* Quand **regexPattern**, **regexGroupName** et **replacementValue** sont fournis :
  
  * La fonction applique **regexPattern** à la chaîne **source** et remplace toutes les valeurs correspondant à **regexGroupName** par **replacementValue**.
* Quand **regexPattern**, **regexGroupName** et **replacementAttributeName** sont fournis :
  
  * Si **source** n’a pas de valeur, **source** est retourné
  * Si **source** a une valeur, la fonction applique **regexPattern** à la chaîne **source** et remplace toutes les valeurs correspondant à **regexGroupName** par la valeur associée à **replacementAttributeName**.

**Paramètres :** 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **source** |Obligatoire |String |Généralement, nom de l’attribut de l’objet **source**. |
| **oldValue** |Facultatif |String |Valeur à remplacer dans **source** ou **template**. |
| **regexPattern** |Facultatif |String |Modèle d’expression régulière pour la valeur à remplacer dans **source**. Ou, quand **replacementPropertyName** est utilisé, modèle pour extraire la valeur de **replacementPropertyName**. |
| **regexGroupName** |Facultatif |String |Nom du groupe à l’intérieur de **regexPattern**. Nous extrayons la valeur de ce groupe en tant que **replacementValue** à partir de **replacementPropertyName** uniquement quand **replacementPropertyName** est utilisé. |
| **replacementValue** |Facultatif |String |Nouvelle valeur par laquelle remplacer l’ancienne. |
| **replacementAttributeName** |Facultatif |String |Nom de l’attribut à utiliser pour la valeur de remplacement. |
| **template** |Facultatif |String |Lorsque la valeur **template** est fournie, nous recherchons la valeur **oldValue** dans le modèle et la remplaçons par la valeur **source**. |

#### <a name="replace-characters-using-a-regular-expression"></a>Remplacer des caractères à l’aide d’une expression régulière
Exemple : vous devez rechercher des caractères correspondant à une valeur d’expression régulière et les supprimer.

**Expression :** 

Replace([mailNickname], , "[a-zA-Z_]*", , "", , )

**Exemple d’entrée/sortie :**

* **ENTRÉE** (mailNickname: "john_doe72"
* **SORTIE** : "72"


---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**Fonction :** SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, …)

**Description :** Nécessite un minimum de deux arguments, qui sont définis à l’aide d’expressions de règles de génération de valeur unique. La fonction évalue chaque règle, puis vérifie la valeur générée pour l’unicité dans le répertoire/application cible. La première valeur unique trouvée est celle retournée. Si toutes les valeurs existent déjà dans la cible, l’entrée sera déposée et le motif de cette action consigné dans les journaux d’audit. Il n’existe aucune limite supérieure au nombre d’arguments qui peuvent être fournis.


 - Cette fonction doit être au niveau supérieur et ne peut pas être imbriquée.
 - Cette fonction ne peut pas s’appliquer à des attributs qui ont une priorité de correspondance.     
 - Cette fonction est uniquement destinée à être utilisée pour les créations d’entrées. Lorsque vous l’utilisez avec un attribut, définissez la propriété **Appliquer le mappage** sur **Uniquement durant la création d’objet**.
 - Cette fonction est actuellement prise en charge uniquement pour l’option « Approvisionnement d’utilisateurs de Workday vers Active Directory » et « Approvisionnement d’utilisateurs de SuccessFactors vers Active Directory ». Elle ne peut pas être utilisée avec d’autres applications de provisionnement. 
 - La recherche LDAP qu’effectue la fonction *SelectUniqueValue* dans un Active Directory local n’échappe pas les caractères spéciaux tels que les signes diacritiques. Si vous transmettez une chaîne telle que « Jéssica Smith », qui contient un caractère spécial, vous rencontrerez des erreurs de traitement. Imbriquez la fonction [NormalizeDiacritics](#normalizediacritics) comme dans l’exemple ci-dessous pour normaliser les caractères spéciaux. 


**Paramètres :** 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **uniqueValueRule1  … uniqueValueRuleN** |Au moins 2 requis, aucune limite supérieure |String | Liste des règles de génération de valeur unique à évaluer. |

#### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Générer une valeur unique pour l’attribut userPrincipalName (UPN)
Exemple : en fonction du prénom, du deuxième prénom et du nom de famille de l’utilisateur, vous devez générer une valeur pour l’attribut UPN et vérifier son caractère unique dans le répertoire AD cible avant d’attribuer la valeur à l’attribut UPN.

**Expression :** 

```ad-attr-mapping-expr
    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )
```

**Exemple d’entrée/sortie :**

* **ENTRÉE** (PreferredFirstName) : "John"
* **ENTRÉE** (PreferredLastName) : "Smith"
* **SORTIE** : « John.Smith@contoso.com » si la valeur UPN de John.Smith@contoso.com n’existe pas déjà dans le répertoire
* **SORTIE** : « J.Smith@contoso.com » si la valeur UPN de John.Smith@contoso.com existe déjà dans le répertoire
* **SORTIE** : « Jo.Smith@contoso.com » si les deux valeurs UPN précédentes existent déjà dans le répertoire



---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Fonction :** SingleAppRoleAssignment([appRoleAssignments])

**Description :** Retourne un appRoleAssignment unique parmi la liste de tous les appRoleAssignments affectés à un utilisateur pour une application donnée. Cette fonction est nécessaire pour convertir l’objet appRoleAssignments en une chaîne de nom de rôle unique. La meilleure pratique consiste à s’assurer qu’un seul appRoleAssignment est attribué à un seul utilisateur à la fois, et si plusieurs rôles sont attribués, la chaîne de rôle retournée ne doit pas être prévisible. 

**Paramètres :** 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |Obligatoire |String |Objet **[appRoleAssignments]** . |

---
### <a name="split"></a>Split
**Fonction :** Split (source, délimiteur)

**Description :** Fractionne une chaîne en un tableau à plusieurs valeurs, en utilisant le caractère délimiteur spécifié.

**Paramètres :** 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **source** |Obligatoire |String |**source** à mettre à jour. |
| **délimiteur** |Obligatoire |String |Spécifie le caractère qui sera utilisé pour fractionner la chaîne (exemple : « , ») |

#### <a name="split-a-string-into-a-multi-valued-array"></a>Fractionner une chaîne en un tableau à valeurs multiples
Exemple : vous devez prendre une liste de chaînes délimitées par des virgules, et la fractionner en un tableau pouvant être raccordé à un attribut à valeurs multiples comme l’attribut PermissionSets de Salesforce. Dans cet exemple, une liste des jeux d’autorisations a été remplie dans extensionAttribute5 dans Azure AD.

**Expression :** Split([extensionAttribute5], ",")

**Exemple d’entrée/sortie :** 

* **INPUT** (extensionAttribute5): "PermissionSetOne, PermissionSetTwo"
* **SORTIE** :  ["PermissionSetOne", "PermissionSetTwo"]


---
### <a name="stripspaces"></a>StripSpaces
**Fonction :**  StripSpaces(source)

**Description :**  supprime tous les caractères d’espacement (" ") de la chaîne source.

**Paramètres :** 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **source** |Obligatoire |String |**source** à mettre à jour. |

---
### <a name="switch"></a>Commutateur
**Fonction :**  Switch(source, defaultValue, key1, value1, key2, value2, …)

**Description :** quand la valeur **source** correspond à une **clé**, retourne la **valeur** de cette **clé**. Si la valeur **source** ne correspond à aucune clé, retourne **defaultValue**.  Les paramètres **key** et **value** doivent toujours être fournis par paires. La fonction attend toujours un nombre pair de paramètres. La fonction ne doit pas être utilisée pour les attributs référentiels tels que le gestionnaire. 

**Paramètres :** 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **source** |Obligatoire |String |**Source** à mettre à jour. |
| **defaultValue** |Facultatif |String |Valeur par défaut à utiliser quand la source ne correspond à aucune clé. Peut être une chaîne vide (""). |
| **key** |Obligatoire |String |**Key** avec laquelle comparer la valeur **source**. |
| **value** |Obligatoire |String |Valeur de remplacement pour la **source** correspondant à la clé. |

#### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Remplacer une valeur en fonction d’un ensemble d’options prédéfini
Exemple : définissez le fuseau horaire de l’utilisateur en fonction du code d’état stocké dans Azure AD.  Si le code d’état ne correspond à aucune des options prédéfinies, utilisez la valeur par défaut « Australia/Sydney ».

**Expression :**  
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Exemple d’entrée/sortie :**

* **ENTRÉE** (état) : "QLD"
* **SORTIE** : "Australia/Brisbane"


---
### <a name="tolower"></a>ToLower
**Fonction :** ToLower(source, culture)

**Description :** prend une valeur de la chaîne *source* et la convertit en minuscules à l’aide des règles de culture spécifiées. Si aucune information de *culture* n'est spécifiée, une culture invariante est utilisée.

Si vous souhaitez que les valeurs existantes du système cible soient en minuscules, [mettez à jour le schéma de votre application cible](./customize-application-attributes.md#editing-the-list-of-supported-attributes) et définissez la propriété caseExact sur « true » pour l'attribut qui vous intéresse. 

**Paramètres :** 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **source** |Obligatoire |String |Généralement le nom de l’attribut de l’objet source |
| **culture** |Facultatif |String |Le format du nom de culture basé sur RFC 4646 est *languagecode2-country/regioncode2*, où *languagecode2* correspond au code de langue à deux lettres et *country/regioncode2* au code de sous-culture à deux lettres, par exemple, ja-JP pour le japonais (Japon) et en-US pour l’anglais (États-Unis). Si un code de langue à deux lettres n'est pas disponible, un code à trois lettres dérivé de la norme ISO 639-2 est utilisé.|

#### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>Convertir la valeur userPrincipalName (UPN) générée en minuscules
Exemple : vous souhaitez générer la valeur UPN en concaténant les champs source PreferredFirstName et PreferredLastName et en convertissant tous les caractères en minuscules. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Exemple d’entrée/sortie :**

* **ENTRÉE** (PreferredFirstName) : "John"
* **ENTRÉE** (PreferredLastName) : "Smith"
* **SORTIE** : "john.smith@contoso.com"


---
### <a name="toupper"></a>ToUpper
**Fonction :** ToUpper(source, culture)

**Description :** prend une valeur de la chaîne *source* et la convertit en majuscule à l’aide des règles de culture spécifiées. Si aucune information de *culture* n'est spécifiée, une culture invariante est utilisée.

Si vous souhaitez que les valeurs existantes du système cible soient en majuscules, [mettez à jour le schéma de votre application cible](./customize-application-attributes.md#editing-the-list-of-supported-attributes) et définissez la propriété caseExact sur « true » pour l’attribut qui vous intéresse. 

**Paramètres :** 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **source** |Obligatoire |String |Généralement le nom de l’attribut de l’objet source. |
| **culture** |Facultatif |String |Le format du nom de culture basé sur RFC 4646 est *languagecode2-country/regioncode2*, où *languagecode2* correspond au code de langue à deux lettres et *country/regioncode2* au code de sous-culture à deux lettres, par exemple, ja-JP pour le japonais (Japon) et en-US pour l’anglais (États-Unis). Si un code de langue à deux lettres n'est pas disponible, un code à trois lettres dérivé de la norme ISO 639-2 est utilisé.|

---
### <a name="word"></a>Word
**Fonction :** Word(String,WordNumber,Delimiters)

**Description :**  La fonction Word retourne un mot contenu dans une chaîne, en fonction des paramètres qui décrivent les délimiteurs à utiliser et le nombre de mots à retourner.  Chaque chaîne de caractères contenue dans la chaîne séparée par l’un des caractères figurant dans delimiters est identifiée en tant que mot :

Si number < 1, retourne une chaîne vide.
Si string a la valeur null, renvoie une chaîne vide.
Si la chaîne contient moins de mots ou ne contient pas les mots identifiés par les séparateurs, une chaîne vide est renvoyée.

**Paramètres :** 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **Chaîne** |Obligatoire |Attribut à valeurs multiples |Chaîne à partir de laquelle renvoyer un mot.|
| **WordNumber** |Obligatoire | Integer | Nombre identifiant le nombre de mots à renvoyer|
| **delimiters** |Obligatoire |String| Une chaîne représentant le ou les délimiteurs à utiliser pour identifier les mots|

**Exemple :** 
`Word("The quick brown fox",3," ")`

Retourne « brown ».

`Word("This,string!has&many separators",3,",!&#")`

Retourne « has »

---

## <a name="examples"></a>Exemples
Cette section fournit d’autres exemples d’utilisation des fonctions d’expression. 

### <a name="strip-known-domain-name"></a>Supprimer un nom de domaine connu
Supprimez un nom de domaine connu de l’adresse de messagerie d’un utilisateur pour obtenir un nom d’utilisateur.  Par exemple, si le domaine est « contoso.com », vous pouvez utiliser l’expression suivante :

**Expression :**  
`Replace([mail], "@contoso.com", , ,"", ,)`

**Exemple d’entrée/sortie :** 

* **ENTRÉE** (e-mail) : "john.doe@contoso.com"
* **SORTIE** : « john.doe »


### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Générer des alias d’utilisateurs en concaténant des parties du prénom et du nom
Générez un alias d’utilisateur en prenant les trois premières lettres du prénom de l’utilisateur et les cinq premières lettres de son nom de famille.

**Expression :**  
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Exemple d’entrée/sortie :** 

* **ENTRÉE** (givenName) : "John"
* **ENTRÉE** (surname) : "Doe"
* **SORTIE** :  "JohDoe"


## <a name="related-articles"></a>Articles connexes
* [Automatiser l’approvisionnement/le déprovisionnement des utilisateurs pour les applications SaaS](../app-provisioning/user-provisioning.md)
* [Personnalisation des mappages d’attributs pour l’approvisionnement des utilisateurs](../app-provisioning/customize-application-attributes.md)
* [Filtres d’étendue pour l’approvisionnement des utilisateurs](define-conditional-rules-for-provisioning-user-accounts.md)
* [Utilisation de SCIM pour activer la configuration automatique des utilisateurs et des groupes d’Azure Active Directory sur des applications](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Notifications d’approvisionnement de comptes](../app-provisioning/user-provisioning.md)
* [Liste des didacticiels sur l’intégration des applications SaaS](../saas-apps/tutorial-list.md)
