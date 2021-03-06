---
title: Skapa Azure Advisor aviseringar för nya rekommendationer | Microsoft Docs
description: Skapa Azure Advisor aviseringar för ny rekommendation
services: advisor
author: sagupt
ms.service: advisor
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/09/2019
ms.author: sagupt
ms.openlocfilehash: d641008a7caf4a006344d886aec945a6e8da2568
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326622"
---
# <a name="create-azure-advisor-alerts-on-new-recommendations"></a>Skapa Azure Advisor aviseringar om nya rekommendationer 

Den här artikeln visar hur du konfigurerar en avisering för nya rekommendationer från Azure Advisor med hjälp av Azure Portal och Azure Resource Manager mallar. 

När Azure Advisor identifierar en ny rekommendation för en av dina resurser lagras en händelse i [Azure aktivitets loggen](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview). Du kan ställa in aviseringar för dessa händelser från Azure Advisor med hjälp av en rekommendation för att skapa aviseringar. Du kan välja en prenumeration och eventuellt en resurs grupp för att ange de resurser som du vill ta emot aviseringar på. 

Du kan också bestämma rekommendationernas typer genom att använda följande egenskaper:

* Category
* Påverkansnivå
* Rekommendations typ

Du kan också konfigurera den åtgärd som ska vidtas när en avisering utlöses av:  

* Välja en befintlig åtgärds grupp
* Skapa en ny åtgärds grupp

Läs mer om åtgärdsgrupper i [Skapa och hantera åtgärdsgrupper](../azure-monitor/platform/action-groups.md).

> [!NOTE] 
> Advisor-aviseringar är för närvarande endast tillgängliga för hög tillgänglighet, prestanda och kostnads rekommendationer. Säkerhets rekommendationer stöds inte. 

## <a name="in-the-azure-portal"></a>I Azure-portalen
1. I **portalen**väljer du **Azure Advisor**.

    ![Azure Advisor i portalen](./media/advisor-alerts/create1.png)

2. I avsnittet **övervakning** på den vänstra menyn väljer du **aviseringar**. 

    ![Aviseringar i Advisor](./media/advisor-alerts/create2.png)

3. Välj **ny rådgivare-avisering**.

    ![Ny rådgivare-avisering](./media/advisor-alerts/create3.png)

4. I avsnittet **omfattning** väljer du den prenumeration och eventuellt den resurs grupp som du vill bli aviserad om. 

    ![Rådgivarens aviserings omfång](./media/advisor-alerts/create4.png)

5. I avsnittet **villkor** väljer du den metod som du vill använda för att konfigurera aviseringen. Om du vill få en avisering om alla rekommendationer för en viss kategori och/eller effekt nivå väljer du **kategori och effekt nivå**. Om du vill få en avisering om alla rekommendationer av en viss typ väljer du **rekommendations typ**.

    ![Azure Advisor varnings villkor](./media/advisor-alerts/create5.png)

6. Beroende på vilket alternativ som du väljer, kan du ange kriterier. Om du vill ha alla rekommendationer lämnar du bara de återstående fälten tomma. 

    ![Rådgivarens aviserings åtgärds grupp](./media/advisor-alerts/create6.png)

7. I avsnittet **Åtgärds grupper** väljer du **Lägg till befintligt** för att använda en åtgärds grupp som du redan har skapat eller väljer **Skapa ny** för att skapa en ny [Åtgärds grupp](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups). 

    ![Advisor-avisering Lägg till befintlig](./media/advisor-alerts/create7.png)

8. I avsnittet aviserings information, ge aviseringen ett namn och en kort beskrivning. Om du vill att din avisering ska vara aktive rad lämnar du **aktivera regeln när** du har skapat urvalet till **Ja**. Välj sedan den resurs grupp som du vill spara aviseringen till. Detta påverkar inte rekommendationens mål omfång. 

    ![Azure Advisor banderoll](./media/advisor-alerts/create8.png)


## <a name="with-an-azure-resource-manager-template"></a>Med en Azure Resource Manager mall

Den här Resource Manager-mallen skapar en rekommendations avisering och en ny åtgärds grupp.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "defaultValue": "advisorAlert",
      "type": "string"
    },
    "activityLogAlerts_name": {
      "defaultValue": "AdvisorAlertsTest2",
      "type": "string"
    },
    "emailAddress": {
      "defaultValue": "<email address>",
      "type": "string"
    }
  },
  "variables": {
    "alertScope": "[concat('/','subscriptions','/',subscription().subscriptionId)]"
  },
  "resources": [
    {
      "comments": "Action Group",
      "type": "microsoft.insights/actionGroups",
      "name": "[parameters('actionGroups_name')]",
      "apiVersion": "2017-04-01",
      "location": "Global",
      "tags": {},
      "scale": null,
      "properties": {
        "groupShortName": "[parameters('actionGroups_name')]",
        "enabled": true,
        "emailReceivers": [
          {
            "name": "[parameters('actionGroups_name')]",
            "emailAddress": "[parameters('emailAddress')]"
          }
        ],
        "smsReceivers": [],
        "webhookReceivers": []
      },
      "dependsOn": []
    },
    {
      "comments": "Azure Advisor Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "name": "[parameters('activityLogAlerts_name')]",
      "apiVersion": "2017-04-01",
      "location": "Global",
      "tags": {},
      "scale": null,
      "properties": {
        "scopes": [
          "[variables('alertScope')]"
        ],
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Recommendation"
            },
            {
              "field": "properties.recommendationCategory",
              "equals": "Cost"
            },
            {
              "field": "properties.recommendationImpact",
              "equals": "Medium"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Advisor/recommendations/available/action"
            }
          ]
        },
        "actions": {
          "actionGroups": [
            {
              "actionGroupId": "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]",
              "webhookProperties": {}
            }
          ]
        },
        "enabled": true,
        "description": ""
      },
      "dependsOn": [
        "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
      ]
    }
  ]
}
  ```

## <a name="configure-recommendation-alerts-to-use-a-webhook"></a>Konfigurera rekommendations aviseringar för att använda en webhook
Det här avsnittet visar hur du konfigurerar Azure Advisor aviseringar för att skicka rekommendations data via webhookar till dina befintliga system. 

Du kan ställa in aviseringar så att du får ett meddelande när du har en ny rekommendation för rekommendationer på en av dina resurser. Dessa aviseringar kan meddela dig via e-post eller SMS, men de kan också användas för att integrera med dina befintliga system via en webhook. 


### <a name="using-the-advisor-recommendation-alert-payload"></a>Använda Advisor-aviseringens nytto Last
Om du vill integrera Advisor-aviseringar i dina egna system med en webhook måste du parsa JSON-nyttolasten som skickas från meddelandet. 

När du ställer in din åtgärds grupp för den här aviseringen väljer du om du vill använda det vanliga aviserings schemat. Om du väljer det gemensamma aviserings schemat kommer din nytto last att se ut så här: 

```json
{  
   "schemaId":"azureMonitorCommonAlertSchema",
   "data":{  
      "essentials":{  
         "alertId":"/subscriptions/<subid>/providers/Microsoft.AlertsManagement/alerts/<alerted>",
         "alertRule":"Webhhook-test",
         "severity":"Sev4",
         "signalType":"Activity Log",
         "monitorCondition":"Fired",
         "monitoringService":"Activity Log - Recommendation",
         "alertTargetIDs":[  
            "/subscriptions/<subid>/resourcegroups/<resource group name>/providers/microsoft.dbformariadb/servers/<resource name>"
         ],
         "originAlertId":"001d8b40-5d41-4310-afd7-d65c9d4428ed",
         "firedDateTime":"2019-07-17T23:00:57.3858656Z",
         "description":"A new recommendation is available.",
         "essentialsVersion":"1.0",
         "alertContextVersion":"1.0"
      },
      "alertContext":{  
         "channels":"Operation",
         "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
         "caller":"Microsoft.Advisor",
         "correlationId":"8554b847-2a72-48ef-9776-600aca3c3aab",
         "eventSource":"Recommendation",
         "eventTimestamp":"2019-07-17T22:28:54.1566942+00:00",
         "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
         "eventDataId":"001d8b40-5d41-4310-afd7-d65c9d4428ed",
         "level":"Informational",
         "operationName":"Microsoft.Advisor/recommendations/available/action",
         "properties":{  
            "recommendationSchemaVersion":"1.0",
            "recommendationCategory":"Performance",
            "recommendationImpact":"Medium",
            "recommendationName":"Increase the MariaDB server vCores",
            "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/source/ActivityLog/recommendationTypeId/a5f888e3-8cf4-4491-b2ba-b120e14eb7ce/resourceId/%2Fsubscriptions%<subscription id>%2FresourceGroups%2<resource group name>%2Fproviders%2FMicrosoft.DBforMariaDB%2Fservers%2F<resource name>",
            "recommendationType":"a5f888e3-8cf4-4491-b2ba-b120e14eb7ce"
         },
         "status":"Active",
         "subStatus":"",
         "submissionTimestamp":"2019-07-17T22:28:54.1566942+00:00"
      }
   }
}
  ```

Om du inte använder det gemensamma schemat ser din nytto Last ut ungefär så här: 

```json
{  
   "schemaId":"Microsoft.Insights/activityLogs",
   "data":{  
      "status":"Activated",
      "context":{  
         "activityLog":{  
            "channels":"Operation",
            "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
            "caller":"Microsoft.Advisor",
            "correlationId":"3ea7320f-c002-4062-adb8-96d3bd92a5f4",
            "description":"A new recommendation is available.",
            "eventSource":"Recommendation",
            "eventTimestamp":"2019-07-17T20:36:39.3966926+00:00",
            "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
            "eventDataId":"a12b8e59-0b1d-4003-bfdc-3d8152922e59",
            "level":"Informational",
            "operationName":"Microsoft.Advisor/recommendations/available/action",
            "properties":{  
               "recommendationSchemaVersion":"1.0",
               "recommendationCategory":"Performance",
               "recommendationImpact":"Medium",
               "recommendationName":"Increase the MariaDB server vCores",
               "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/source/ActivityLog/recommendationTypeId/a5f888e3-8cf4-4491-b2ba-b120e14eb7ce/resourceId/%2Fsubscriptions%2F<subscription id>%2FresourceGroups%2F<resource group name>%2Fproviders%2FMicrosoft.DBforMariaDB%2Fservers%2F<resource name>",
               "recommendationType":"a5f888e3-8cf4-4491-b2ba-b120e14eb7ce"
            },
            "resourceId":"/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/microsoft.dbformariadb/servers/<resource name>",
            "resourceGroupName":"<resource group name>",
            "resourceProviderName":"MICROSOFT.DBFORMARIADB",
            "status":"Active",
            "subStatus":"",
            "subscriptionId":"<subscription id>",
            "submissionTimestamp":"2019-07-17T20:36:39.3966926+00:00",
            "resourceType":"MICROSOFT.DBFORMARIADB/SERVERS"
         }
      },
      "properties":{  
 
      }
   }
}
```

I båda schemana kan du identifiera rekommendationer för Advisor-händelser genom att leta `Recommendation` efter eventSource och `Microsoft.Advisor/recommendations/available/action` **operationName** är.

Några av de andra viktiga fälten som du kanske vill använda är: 

* *alertTargetIDs* (i det gemensamma schemat) eller *resourceId* (bakåtkompatibelt schema)
* *recommendationType*
* *recommendationName*
* *recommendationCategory*
* *recommendationImpact*
* *recommendationResourceLink*


## <a name="manage-your-alerts"></a>Hantera aviseringar 

Från Azure Advisor kan du redigera, ta bort eller inaktivera och aktivera aviseringar från rekommendationer. 

1. I **portalen**väljer du **Azure Advisor**.

    ![Azure Advisor banderoll](./media/advisor-alerts/create1.png)

2. I avsnittet **övervakning** på den vänstra menyn väljer du **aviseringar**.

    ![Azure Advisor banderoll](./media/advisor-alerts/create2.png)

3. Om du vill redigera en avisering klickar du på aviseringens namn för att öppna aviseringen och redigera de fält som du vill redigera.

4. Om du vill ta bort, aktivera eller inaktivera en avisering klickar du på ellipsen i slutet av raden och väljer sedan den åtgärd som du vill utföra.
 

