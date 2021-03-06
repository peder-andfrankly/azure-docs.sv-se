---
title: Anslut till Outlook.com
description: 'Hantera e-post, kalendrar och kontakter med Outlook.com REST API: er och Azure Logic Apps'
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/18/2016
tags: connectors
ms.openlocfilehash: 750efc2cb928bf127c4f3c68d5a58c5f52ca7d51
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789381"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-with-azure-logic-apps"></a>Hantera e-post, kalendrar och kontakter i Outlook.com med Azure Logic Apps

Den här artikeln visar hur du kan skapa och hantera ditt Outlook.com-konto i en Logic app med Box Connector. På så sätt kan du skapa Logi Kap par som automatiserar uppgifter och arbets flöden för ditt Outlook.com-konto, till exempel:

* Skicka e-post. 
* Schemalägg möten.
* Lägg till kontakter. 

Om du inte har använt Logic Apps igen kan du läsa mer [Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Krav

* Ett [Outlook.com-konto](https://outlook.live.com/owa/)

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Den Logic app där du vill komma åt ditt Outlook.com-konto. Om du vill starta din Logic app med en Outlook-utlösare behöver du en [Tom Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-outlookcom"></a>Anslut till Outlook.com

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Connect to Outlook.com](../../includes/connectors-create-api-outlook.md)]

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information, till exempel utlösare, åtgärder och gränser, som beskrivs av kopplingens Swagger-fil, finns på [kopplingens referens sida](/connectors/outlook/). 

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)