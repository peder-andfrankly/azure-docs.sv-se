---
title: Anslut till Salesforce från Azure Logic Apps
description: Automatisera uppgifter och arbets flöden som övervakar, skapar och hanterar Salesforce-poster och jobb genom att använda Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 000f4381ef2a7c0a2099a021b991087725ff2070
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789298"
---
# <a name="monitor-create-and-manage-salesforce-resources-by-using-azure-logic-apps"></a>Övervaka, skapa och hantera Salesforce-resurser med hjälp av Azure Logic Apps

Med Azure Logic Apps och Salesforce-anslutningen kan du skapa automatiserade uppgifter och arbets flöden för Salesforce-resurser, till exempel poster, jobb och objekt, till exempel:

* Övervaka när poster skapas eller ändras. 
* Skapa, hämta och hantera jobb och poster, inklusive INSERT-, Update-och Delete-åtgärder.

Du kan använda Salesforce-utlösare som får svar från Salesforce och göra utdata tillgängliga för andra åtgärder. Du kan använda åtgärder i Logi Kap par för att utföra uppgifter med Salesforce-resurser. Om du är nybörjare på Logi Kap par kan du läsa om [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Ett [Salesforce-konto](https://salesforce.com/)

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Den Logic app där du vill komma åt ditt Salesforce-konto. Börja med en Salesforce-utlösare genom att [skapa en tom Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda en Salesforce-åtgärd startar du din Logic-app med en annan utlösare, till exempel utlösaren **upprepning** .

## <a name="connect-to-salesforce"></a>Anslut till Salesforce

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna din Logic app i Logic App Designer, om du inte redan har gjort det.

1. Välj en sökväg: 

   * För tomma Logi Kap par anger du "Salesforce" som filter i rutan Sök. 
   Välj den utlösare som du vill använda under listan utlösare. 

     ELLER

   * För befintliga Logi Kap par väljer du **nytt steg**under steget där du vill lägga till en åtgärd. Skriv "Salesforce" som filter i rutan Sök. Under listan åtgärder väljer du den åtgärd som du vill använda.

1. Om du uppmanas att logga in på Salesforce loggar du in nu och tillåter åtkomst.

   Dina autentiseringsuppgifter ger din Logic-app behörighet att skapa en anslutning till Salesforce och komma åt dina data.

1. Ange nödvändig information för den valda utlösaren eller åtgärden och fortsätt att skapa din Logic Apps-arbetsflöde.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och gränser, som beskrivs av kopplingens OpenAPI (tidigare Swagger) Beskrivning, finns i kopplingens [referens sida](/connectors/salesforce/).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)