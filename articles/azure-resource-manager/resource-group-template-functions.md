---
title: Mallfunktioner
description: Beskriver de funktioner som används i en Azure Resource Manager mall för att hämta värden, arbeta med strängar och siffror och hämta distributions information.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: cf263bff72677778433d4ef2f3cee8135fe3ab06
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224185"
---
# <a name="azure-resource-manager-template-functions"></a>Funktioner för Azure Resource Manager mallar

I den här artikeln beskrivs alla funktioner som du kan använda i en Azure Resource Manager-mall. Information om hur du använder funktioner i din mall finns i [syntax för mallar](template-expressions.md).

Information om hur du skapar dina egna funktioner finns i [användardefinierade funktioner](resource-group-authoring-templates.md#functions).

<a id="array" aria-hidden="true" />
<a id="coalesce" aria-hidden="true" />
<a id="concatarray" aria-hidden="true" />
<a id="contains" aria-hidden="true" />
<a id="createarray" aria-hidden="true" />
<a id="empty" aria-hidden="true" />
<a id="first" aria-hidden="true" />
<a id="intersection" aria-hidden="true" />
<a id="json" aria-hidden="true" />
<a id="last" aria-hidden="true" />
<a id="length" aria-hidden="true" />
<a id="min" aria-hidden="true" />
<a id="max" aria-hidden="true" />
<a id="range" aria-hidden="true" />
<a id="skip" aria-hidden="true" />
<a id="take" aria-hidden="true" />
<a id="union" aria-hidden="true" />

## <a name="array-and-object-functions"></a>Funktioner för matris och objekt
Resource Manager innehåller flera funktioner för att arbeta med matriser och objekt.

* [lagringsmatriser](resource-group-template-functions-array.md#array)
* [coalesce](resource-group-template-functions-array.md#coalesce)
* [concat](resource-group-template-functions-array.md#concat)
* [ingår](resource-group-template-functions-array.md#contains)
* [createArray](resource-group-template-functions-array.md#createarray)
* [saknas](resource-group-template-functions-array.md#empty)
* [förstagångskörningen](resource-group-template-functions-array.md#first)
* [överlappning](resource-group-template-functions-array.md#intersection)
* [utgör](resource-group-template-functions-array.md#json)
* [pågå](resource-group-template-functions-array.md#last)
* [krävande](resource-group-template-functions-array.md#length)
* [minimum](resource-group-template-functions-array.md#min)
* [bekräftat](resource-group-template-functions-array.md#max)
* [intervall](resource-group-template-functions-array.md#range)
* [Ignorera](resource-group-template-functions-array.md#skip)
* [gå](resource-group-template-functions-array.md#take)
* [Union](resource-group-template-functions-array.md#union)

<a id="equals" aria-hidden="true" />
<a id="less" aria-hidden="true" />
<a id="lessorequals" aria-hidden="true" />
<a id="greater" aria-hidden="true" />
<a id="greaterorequals" aria-hidden="true" />

## <a name="comparison-functions"></a>Jämförelsefunktioner
Resource Manager innehåller flera funktioner för att göra jämförelser i dina mallar.

* [är lika med](resource-group-template-functions-comparison.md#equals)
* [minskad](resource-group-template-functions-comparison.md#less)
* [lessOrEquals](resource-group-template-functions-comparison.md#lessorequals)
* [störst](resource-group-template-functions-comparison.md#greater)
* [Större](resource-group-template-functions-comparison.md#greaterorequals)

<a id="deployment" aria-hidden="true" />
<a id="parameters" aria-hidden="true" />
<a id="variables" aria-hidden="true" />

## <a name="deployment-value-functions"></a>Funktioner för distributions värde
Resource Manager innehåller följande funktioner för att hämta värden från avsnitt i mallen och värden som är relaterade till distributionen:

* [spridningen](resource-group-template-functions-deployment.md#deployment)
* [miljö](resource-group-template-functions-deployment.md#environment)
* [parameters](resource-group-template-functions-deployment.md#parameters)
* [användarvariabler](resource-group-template-functions-deployment.md#variables)

<a id="and" aria-hidden="true" />
<a id="bool" aria-hidden="true" />
<a id="if" aria-hidden="true" />
<a id="not" aria-hidden="true" />
<a id="or" aria-hidden="true" />

## <a name="logical-functions"></a>Logiska funktioner
Resource Manager innehåller följande funktioner för att arbeta med logiska villkor:

* [särskilt](resource-group-template-functions-logical.md#and)
* [booleska](resource-group-template-functions-logical.md#bool)
* [eventuella](resource-group-template-functions-logical.md#if)
* [Ogiltigt](resource-group-template-functions-logical.md#not)
* [eller](resource-group-template-functions-logical.md#or)

<a id="add" aria-hidden="true" />
<a id="copyindex" aria-hidden="true" />
<a id="div" aria-hidden="true" />
<a id="float" aria-hidden="true" />
<a id="int" aria-hidden="true" />
<a id="minint" aria-hidden="true" />
<a id="maxint" aria-hidden="true" />
<a id="mod" aria-hidden="true" />
<a id="mul" aria-hidden="true" />
<a id="sub" aria-hidden="true" />

## <a name="numeric-functions"></a>Numeriska funktioner
Resource Manager innehåller följande funktioner för att arbeta med heltal:

* [skapa](resource-group-template-functions-numeric.md#add)
* [copyIndex](resource-group-template-functions-numeric.md#copyindex)
* [tagg](resource-group-template-functions-numeric.md#div)
* [flyta](resource-group-template-functions-numeric.md#float)
* [int](resource-group-template-functions-numeric.md#int)
* [minimum](resource-group-template-functions-numeric.md#min)
* [bekräftat](resource-group-template-functions-numeric.md#max)
* [rest](resource-group-template-functions-numeric.md#mod)
* [mul](resource-group-template-functions-numeric.md#mul)
* [Build](resource-group-template-functions-numeric.md#sub)

<a id="extensionResourceId" aria-hidden="true" />
<a id="listkeys" aria-hidden="true" />
<a id="list" aria-hidden="true" />
<a id="providers" aria-hidden="true" />
<a id="reference" aria-hidden="true" />
<a id="resourcegroup" aria-hidden="true" />
<a id="resourceid" aria-hidden="true" />
<a id="subscription" aria-hidden="true" />
<a id="subscriptionResourceId" aria-hidden="true" />
<a id="tenantResourceId" aria-hidden="true" />

## <a name="resource-functions"></a>Resursfunktioner
Resource Manager tillhandahåller följande funktioner för att hämta resurs-värden:

* [extensionResourceId](resource-group-template-functions-resource.md#extensionresourceid)
* [listAccountSas](resource-group-template-functions-resource.md#list)
* [Listnycklar](resource-group-template-functions-resource.md#listkeys)
* [listSecrets](resource-group-template-functions-resource.md#list)
* [lista](resource-group-template-functions-resource.md#list)
* [finansiär](resource-group-template-functions-resource.md#providers)
* [förhållande](resource-group-template-functions-resource.md#reference)
* [resourceGroup](resource-group-template-functions-resource.md#resourcegroup)
* [resourceId](resource-group-template-functions-resource.md#resourceid)
* [prenumeration](resource-group-template-functions-resource.md#subscription)
* [subscriptionResourceId](resource-group-template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](resource-group-template-functions-resource.md#tenantresourceid)

<a id="base64" aria-hidden="true" />
<a id="base64tojson" aria-hidden="true" />
<a id="base64tostring" aria-hidden="true" />
<a id="concat" aria-hidden="true" />
<a id="containsstring" aria-hidden="true" />
<a id="datauri" aria-hidden="true" />
<a id="datauritostring" aria-hidden="true" />
<a id="emptystring" aria-hidden="true" />
<a id="endswith" aria-hidden="true" />
<a id="firststring" aria-hidden="true" />
<a id="guid" aria-hidden="true" />
<a id="indexof" aria-hidden="true" />
<a id="laststring" aria-hidden="true" />
<a id="lastindexof" aria-hidden="true" />
<a id="lengthstring" aria-hidden="true" />
<a id="padleft" aria-hidden="true" />
<a id="replace" aria-hidden="true" />
<a id="skipstring" aria-hidden="true" />
<a id="split" aria-hidden="true" />
<a id="startswith" aria-hidden="true" />
<a id="string" aria-hidden="true" />
<a id="substring" aria-hidden="true" />
<a id="takestring" aria-hidden="true" />
<a id="tolower" aria-hidden="true" />
<a id="toupper" aria-hidden="true" />
<a id="trim" aria-hidden="true" />
<a id="uniquestring" aria-hidden="true" />
<a id="uri" aria-hidden="true" />
<a id="uricomponent" aria-hidden="true" />
<a id="uricomponenttostring" aria-hidden="true" />

## <a name="string-functions"></a>Strängfunktioner
Resource Manager innehåller följande funktioner för att arbeta med strängar:

* [Base64](resource-group-template-functions-string.md#base64)
* [base64ToJson](resource-group-template-functions-string.md#base64tojson)
* [base64ToString](resource-group-template-functions-string.md#base64tostring)
* [concat](resource-group-template-functions-string.md#concat)
* [ingår](resource-group-template-functions-string.md#contains)
* [dataUri](resource-group-template-functions-string.md#datauri)
* [dataUriToString](resource-group-template-functions-string.md#datauritostring)
* [saknas](resource-group-template-functions-string.md#empty)
* [endsWith](resource-group-template-functions-string.md#endswith)
* [förstagångskörningen](resource-group-template-functions-string.md#first)
* [formatering](resource-group-template-functions-string.md#format)
* [LED](resource-group-template-functions-string.md#guid)
* [indexOf](resource-group-template-functions-string.md#indexof)
* [pågå](resource-group-template-functions-string.md#last)
* [lastIndexOf](resource-group-template-functions-string.md#lastindexof)
* [krävande](resource-group-template-functions-string.md#length)
* [newGuid](resource-group-template-functions-string.md#newguid)
* [padLeft](resource-group-template-functions-string.md#padleft)
* [bytt](resource-group-template-functions-string.md#replace)
* [Ignorera](resource-group-template-functions-string.md#skip)
* [split](resource-group-template-functions-string.md#split)
* [startsWith](resource-group-template-functions-string.md#startswith)
* [nollängd](resource-group-template-functions-string.md#string)
* [under sträng](resource-group-template-functions-string.md#substring)
* [gå](resource-group-template-functions-string.md#take)
* [toLower](resource-group-template-functions-string.md#tolower)
* [toUpper](resource-group-template-functions-string.md#toupper)
* [reducera](resource-group-template-functions-string.md#trim)
* [uniqueString](resource-group-template-functions-string.md#uniquestring)
* [URI](resource-group-template-functions-string.md#uri)
* [uriComponent](resource-group-template-functions-string.md#uricomponent)
* [uriComponentToString](resource-group-template-functions-string.md#uricomponenttostring)
* [utcNow](resource-group-template-functions-string.md#utcnow)

## <a name="next-steps"></a>Nästa steg

* En beskrivning av avsnitten i en Azure Resource Manager mall finns i [redigera Azure Resource Manager mallar](resource-group-authoring-templates.md)
* Om du vill slå samman flera mallar, se [använda länkade mallar med Azure Resource Manager](resource-group-linked-templates.md)
* Om du vill iterera ett visst antal gånger när du skapar en typ av resurs, se [skapa flera instanser av resurser i Azure Resource Manager](resource-group-create-multiple.md)
* Information om hur du distribuerar mallen som du har skapat finns i [distribuera ett program med Azure Resource Manager mall](resource-group-template-deploy.md)
