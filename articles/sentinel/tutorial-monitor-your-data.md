---
title: Övervaka dina data med hjälp av instrument paneler baserade på Azure Monitor arbets böcker i Azure Sentinel | Microsoft Docs
description: I den här självstudien får du lära dig hur du övervakar dina data med hjälp av instrument paneler baserade på arbets böcker i Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/17/2019
ms.author: rkarlin
ms.openlocfilehash: c5f4bd079fa215e3fbd4241f603aa29d19f7aad7
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158863"
---
# <a name="tutorial-monitor-your-data"></a>Självstudie: övervaka dina data



När du har [anslutit data källorna](quickstart-onboard.md) till Azure Sentinel kan du övervaka data med hjälp av Azure Sentinel-integrering med Azure Monitor arbets böcker, vilket ger mångsidighet i att skapa anpassade instrument paneler. Även om arbets böckerna visas på olika sätt i Azure Sentinel, kan det vara praktiskt att se hur du [skapar interaktiva rapporter med Azure Monitor arbets böcker](../azure-monitor/app/usage-workbooks.md). Med Azure Sentinel kan du skapa anpassade arbets böcker i dina data och även med inbyggda mallar för arbets böcker så att du snabbt kan få insikter om dina data så snart du ansluter en data källa.


Den här självstudien hjälper dig att övervaka dina data i Azure Sentinel.
> [!div class="checklist"]
> * Använda inbyggda arbets böcker
> * Skapa nya arbets böcker

## <a name="prerequisites"></a>Krav

- Du måste ha minst behörigheter för arbets boks läsare eller arbets bok deltagare för resurs gruppen i Azure Sentinel-arbetsytan.

> [!NOTE]
> Arbets böckerna som du kan se i Azure Sentinel sparas i resurs gruppen för Azure Sentinel-arbetsytan och märks av arbets ytan där de skapades.

## <a name="use-built-in-workbooks"></a>Använda inbyggda arbets böcker

1. Gå till **arbets böcker** och välj sedan **mallar** för att se en fullständig lista över inbyggda Azure Sentinel-arbetsböcker. För att se vilka som är relevanta för de data typer som du har anslutit, visar fältet **obligatoriska data typer** i varje arbets bok data typen bredvid en grön bock markering om du redan strömmar relevanta data till Azure Sentinel.
  ![gå till arbets böcker](./media/tutorial-monitor-data/access-workbooks.png)
1. Klicka på **Visa arbets bok** för att se mallen som är ifylld med dina data.
  
1. Om du vill redigera arbets boken väljer du **Spara**och väljer sedan den plats där du vill spara JSON-filen för mallen. 

   > [!NOTE]
   > Detta skapar en Azure-resurs som baseras på den relevanta mallen och sparar själva mall-JSON-filen och inte data.


1. Välj **Visa arbets bok**. Klicka sedan på knappen **Redigera** högst upp. Nu kan du redigera arbets boken och anpassa den efter dina behov. Mer information om hur du anpassar arbets boken finns i så här [skapar du interaktiva rapporter med Azure Monitor arbets böcker](../azure-monitor/app/usage-workbooks.md).
![Visa arbets böcker](./media/tutorial-monitor-data/workbook-graph.png)
1. När du har gjort ändringarna kan du spara arbets boken. 

1. Du kan också klona arbets boken: Välj **redigera** och **Spara som**, och se till att spara den med ett annat namn, under samma prenumeration och resurs grupp. Dessa arbets böcker visas under fliken **Mina arbets böcker** .


## <a name="create-new-workbook"></a>Skapa ny arbets bok

1. Gå till **arbets böcker** och välj sedan **Lägg till arbets bok** för att skapa en ny arbets bok från grunden.
  ![gå till arbets böcker](./media/tutorial-monitor-data/create-workbook.png)

1. Om du vill redigera arbets boken väljer du **Redigera**och lägger sedan till text, frågor och parametrar vid behov. Mer information om hur du anpassar arbets boken finns i så här [skapar du interaktiva rapporter med Azure Monitor arbets böcker](../azure-monitor/app/usage-workbooks.md). 

1. När du skapar en fråga ställer du in **data källan** på **loggar**, **resurs typen** anges till **Log Analytics** och väljer sedan de relevanta arbets ytorna. 

1. När du har skapat din arbets bok sparar du arbets boken och ser till att du sparar den under prenumerationen och resurs gruppen på Azure Sentinel-arbetsytan.

1. Om du vill låta andra i organisationen använda arbets boken går du till Välj **delade rapporter**under **Spara** . Om du vill att den här arbets boken bara ska vara tillgänglig för dig väljer du **Mina rapporter**.

1. Om du vill växla mellan arbets böcker i arbets ytan kan du välja **öppna** ![växla arbets böcker](./media/tutorial-monitor-data/switch.png)i det övre fönstret i alla arbets böcker. Växla mellan arbets böcker i fönstret som öppnas till höger.

   ![Växla arbets böcker](./media/tutorial-monitor-data/switch-workbooks.png)


## <a name="how-to-delete-workbooks"></a>Ta bort arbets böcker

Du kan ta bort arbets böcker som har skapats från en Azure Sentinel-mall. 

Om du vill ta bort en anpassad arbets bok väljer du den sparade arbets bok som du vill ta bort på sidan arbets böcker och väljer **ta bort**. Den sparade arbets boken tas bort.

> [!NOTE]
> Detta tar bort resursen samt eventuella ändringar som du har gjort i mallen. Den ursprungliga mallen är fortfarande tillgänglig.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du visar dina data i Azure Sentinel.

Information om hur du automatiserar dina svar på hot finns i [Konfigurera automatiserade hot svar i Azure Sentinel](tutorial-respond-threats-playbook.md).
