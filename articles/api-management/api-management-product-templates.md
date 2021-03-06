---
title: Produktmallar i Azure API Management | Microsoft Docs
description: Lär dig hur du anpassar innehållet på produkt sidorna i Azure API Management Developer-portalen.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 49f9254c-4c5f-4ed4-9c8d-798f44e805ee
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 393563427e936e07315cd44b78cb793d4292b352
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176578"
---
# <a name="product-templates-in-azure-api-management"></a>Produktmallar i Azure API Management

Med Azure API Management kan du anpassa innehållet i utvecklares Portal sidor med en uppsättning mallar som konfigurerar innehållet. Om du använder [DotLiquid](http://dotliquidmarkup.org/) -syntax och valfritt redigerings program, till exempel [DotLiquid för designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers)och en angiven uppsättning lokaliserade [sträng resurser](api-management-template-resources.md#strings), glyf- [resurser](api-management-template-resources.md#glyphs)och [sid kontroller](api-management-page-controls.md), har du stor flexibilitet att konfigurera innehållet på sidorna som du ser får plats med mallarna.  
  
 Med mallarna i det här avsnittet kan du anpassa innehållet på produkt sidorna i Developer-portalen.  
  
-   [Produkt lista](#ProductList)  
  
-   [Momsproduktbokföringsmallar](#Product)  
  
> [!NOTE]
>  Exempel på standardmallar finns i följande dokumentation, men kan komma att ändras på grund av kontinuerliga förbättringar. Du kan visa standardmallarna för Live i Developer-portalen genom att gå till önskade enskilda mallar. Mer information om hur du arbetar med mallar finns i [anpassa API Management Developer-portalen med hjälp av mallar](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="ProductList"></a>Produkt lista  
 Med **produkt list** mal len kan du anpassa bröd texten på sidan produkt lista i Developer-portalen.  
  
 ![Produkt lista](./media/api-management-product-templates/APIM_ProductsListTemplatePage.png "APIM_ProductsListTemplatePage")  
  
### <a name="default-template"></a>Standardmall  
  
```xml  
<search-control></search-control>  
<div class="row">  
    <div class="col-md-9">  
        <h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
    </div>  
</div>  
<div class="row">  
    <div class="col-md-12">  
    {% if products.size > 0 %}  
    <ul class="list-unstyled">  
    {% for product in products %}  
        <li>  
            <h3><a href="/products/{{product.id}}">{{product.title}}</a></h3>  
            {{product.description}}  
        </li>     
    {% endfor %}  
    </ul>  
    <paging-control></paging-control>  
    {% else %}  
    {% localized "CommonResources|NoItemsToDisplay" %}  
    {% endif %}  
    </div>  
</div>  
```  
  
### <a name="controls"></a>Kontroller  
 `Product list` mal len kan använda följande [sid kontroller](api-management-page-controls.md).  
  
-   [sid kontroll](api-management-page-controls.md#paging-control)  
  
-   [Sök-kontroll](api-management-page-controls.md#search-control)  
  
### <a name="data-model"></a>Datamodell  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Sidindelning|[Växlings](api-management-template-data-model-reference.md#Paging) enhet.|Växlings informationen för produkt samlingen.|  
|Filtrering|[Filtrera](api-management-template-data-model-reference.md#Filtering) entiteten.|Filtrerings informationen för produkt list sidan.|  
|Produkter|Samling av [produkt](api-management-template-data-model-reference.md#Product) enheter.|De produkter som är synliga för den aktuella användaren.|  
  
### <a name="sample-template-data"></a>Exempel mal lin data  
  
```json  
{  
    "Paging": {  
        "Page": 1,  
        "PageSize": 10,  
        "TotalItemCount": 2,  
        "ShowAll": false,  
        "PageCount": 1  
    },  
    "Filtering": {  
        "Pattern": null,  
        "Placeholder": "Search products"  
    },  
    "Products": [  
        {  
            "Id": "56f9445ffaf7560049060001",  
            "Title": "Starter",  
            "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
            "Terms": "",  
            "ProductState": 1,  
            "AllowMultipleSubscriptions": false,  
            "MultipleSubscriptionsCount": 1  
        },  
        {  
            "Id": "56f9445ffaf7560049060002",  
            "Title": "Unlimited",  
            "Description": "Subscribers have completely unlimited access to the API. Administrator approval is required.",  
            "Terms": null,  
            "ProductState": 1,  
            "AllowMultipleSubscriptions": false,  
            "MultipleSubscriptionsCount": 1  
        }  
    ]  
}  
```  
  
##  <a name="Product"></a>Momsproduktbokföringsmallar  
 Med **produkt** mal len kan du anpassa bröd texten på produkt sidan i Developer-portalen.  
  
 ![Produkt sida för utvecklare Portal](./media/api-management-product-templates/APIM_ProductPage.png "APIM_ProductPage")  
  
### <a name="default-template"></a>Standardmall  
  
```xml  
<h2>{{Product.Title}}</h2>  
<p>{{Product.Description}}</p>  
  
{% assign replaceString0 = '{0}' %}  
  
{% if Limits and Limits.size > 0 %}  
<h3>{% localized "ProductDetailsStrings|WebProductsUsageLimitsHeader"%}</h3>  
<ul>  
  {% for limit in Limits %}  
  <li>{{limit.DisplayName}}</li>  
  {% endfor %}  
</ul>  
{% endif %}  
  
{% if apis.size > 0 %}  
<p>  
  <b>  
    {% if apis.size == 1 %}  
    {% capture apisCountText %}{% localized "ProductDetailsStrings|TextblockSingleApisCount" %}{% endcapture %}  
    {% else %}  
    {% capture apisCountText %}{% localized "ProductDetailsStrings|TextblockMultipleApisCount" %}{% endcapture %}  
    {% endif %}  
  
    {% capture apisCount %}{{apis.size}}{% endcapture %}  
    {{ apisCountText | replace : replaceString0, apisCount }}  
  </b>  
</p>  
  
<ul>  
  {% for api in Apis %}  
  <li>  
    <a href="/docs/services/{{api.Id}}">{{api.Name}}</a>  
  </li>  
  {% endfor %}  
</ul>  
{% endif %}  
  
{% if subscriptions.size > 0 %}  
<p>  
  <b>  
    {% if subscriptions.size == 1 %}  
    {% capture subscriptionsCountText %}{% localized "ProductDetailsStrings|TextblockSingleSubscriptionsCount" %}{% endcapture %}  
    {% else %}  
    {% capture subscriptionsCountText %}{% localized "ProductDetailsStrings|TextblockMultipleSubscriptionsCount" %}{% endcapture %}  
    {% endif %}  
  
    {% capture subscriptionsCount %}{{subscriptions.size}}{% endcapture %}  
    {{ subscriptionsCountText | replace : replaceString0, subscriptionsCount }}  
  </b>  
</p>  
  
<ul>  
  {% for subscription in subscriptions %}  
  <li>  
    <a href="/developer#{{subscription.Id}}">{{subscription.DisplayName}}</a>  
  </li>  
  {% endfor %}  
</ul>  
{% endif %}  
{% if CannotAddBecauseSubscriptionNumberLimitReached %}  
<b>{% localized "ProductDetailsStrings|TextblockSubscriptionLimitReached" %}</b>  
{% elsif CannotAddBecauseMultipleSubscriptionsNotAllowed == false %}  
<subscribe-button></subscribe-button>  
{% endif %}  
```  
  
### <a name="controls"></a>Kontroller  
 `Product list` mal len kan använda följande [sid kontroller](api-management-page-controls.md).  
  
-   [Prenumerera – knapp](api-management-page-controls.md#subscribe-button)  
  
### <a name="data-model"></a>Datamodell  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Produkt|[Momsproduktbokföringsmallar](api-management-template-data-model-reference.md#Product)|Den angivna produkten.|  
|IsDeveloperSubscribed|boolesk|Om den aktuella användaren prenumererar på den här produkten.|  
|SubscriptionState|nummer|Prenumerationens tillstånd. Möjliga tillstånd är:<br /><br /> -   `0 - suspended` – prenumerationen är blockerad och prenumeranten kan inte anropa några API: er för produkten.<br />-   `1 - active` – prenumerationen är aktiv.<br />-   `2 - expired` – prenumerationen har nått sitt förfallo datum och inaktiverades.<br />-   `3 - submitted` – prenumerations förfrågan har gjorts av utvecklaren, men har ännu inte godkänts eller avvisats.<br />-   `4 - rejected` – prenumerations förfrågan har nekats av en administratör.<br />-   `5 - cancelled` – prenumerationen har avbrutits av utvecklaren eller administratören.|  
|Begränsningar|matris|Den här egenskapen är föråldrad och ska inte användas.|  
|DelegatedSubscriptionEnabled|boolesk|Om [delegering](https://azure.microsoft.com/documentation/articles/api-management-howto-setup-delegation/) är aktiverat för den här prenumerationen.|  
|DelegatedSubscriptionUrl|sträng|Om delegering är aktiverat är den delegerade prenumerations-URL: en.|  
|IsAgreed|boolesk|Om produkten har villkor, om den aktuella användaren har godkänt villkoren.|  
|Prenumerationer|Samling av entiteter för [prenumerations Sammanfattning](api-management-template-data-model-reference.md#SubscriptionSummary) .|Prenumerationerna på produkten.|  
|N|Samling av [API](api-management-template-data-model-reference.md#API) -entiteter.|API: erna i den här produkten.|  
|CannotAddBecauseSubscriptionNumberLimitReached|boolesk|Om den aktuella användaren är behörig att prenumerera på den här produkten med avseende på prenumerations gränsen.|  
|CannotAddBecauseMultipleSubscriptionsNotAllowed|boolesk|Om den aktuella användaren är behörig att prenumerera på den här produkten med avseende på flera prenumerationer som tillåts eller inte.|  
  
### <a name="sample-template-data"></a>Exempel mal lin data  
  
```json  
{  
    "Product": {  
        "Id": "56f9445ffaf7560049060001",  
        "Title": "Starter",  
        "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
        "Terms": "",  
        "ProductState": 1,  
        "AllowMultipleSubscriptions": false,  
        "MultipleSubscriptionsCount": 1  
    },  
    "IsDeveloperSubscribed": true,  
    "SubscriptionState": 1,  
    "Limits": [],  
    "DelegatedSubscriptionEnabled": false,  
    "DelegatedSubscriptionUrl": null,  
    "IsAgreed": false,  
    "Subscriptions": [  
        {  
            "Id": "56f9445ffaf7560049070001",  
            "DisplayName": "Starter  (default)"  
        }  
    ],  
    "Apis": [  
        {  
            "id": "56f9445ffaf7560049040001",  
            "name": "Echo API",  
            "description": null,  
            "serviceUrl": "http://echoapi.cloudapp.net/api",  
            "path": "echo",  
            "protocols": [  
                2  
            ],  
            "authenticationSettings": null,  
            "subscriptionKeyParameterNames": null  
        }  
    ],  
    "CannotAddBecauseSubscriptionNumberLimitReached": false,  
    "CannotAddBecauseMultipleSubscriptionsNotAllowed": true  
}  
```

## <a name="next-steps"></a>Nästa steg
Mer information om hur du arbetar med mallar finns i [anpassa API Management Developer-portalen med hjälp av mallar](api-management-developer-portal-templates.md).
