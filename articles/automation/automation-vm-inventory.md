---
title: Hantera en virtuell Azure-dator med inventeringssamling | Microsoft Docs
description: Hantera en virtuell dator med inventeringssamling
services: automation
ms.service: automation
ms.subservice: change-inventory-management
keywords: inventory, automation, change, tracking
author: jennyhunter-msft
ms.author: jehunte
ms.date: 02/06/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 59f36595e0b6cc8b9d9ea0669c9ecb5be1e74b42
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61304150"
---
# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>Hantera en virtuell Azure-dator med inventeringssamling

Du kan aktivera inventeringssamling för en virtuell Azure-dator via den virtuella datorns resurssida. Du kan samla in och visa inventeringar för program, filer, Linux-daemons, Windows-tjänster och Windows-registernycklar på dina datorer. Den här metoden ger ett webbläsarbaserat användargränssnitt för att installera och konfigurera inventeringssamling.

## <a name="before-you-begin"></a>Innan du börjar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/).

Den här artikeln förutsätter att du har en virtuell dator för att konfigurera lösningen på. Om du inte har en virtuell Azure-dator ska du skapa en [virtuell dator](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>Aktivera inventeringssamling via en virtuell dators resurssida

1. I rutan till vänster på Azure Portal väljer du **Virtuella datorer**.
2. Från listan med virtuella datorer väljer du en virtuell dator.
3. På den **Resource** menyn under **Operations**väljer **inventering**.
4. Välj en Log Analytics-arbetsyta för att lagra dina dataloggar.
    Om det inte finns några tillgängliga arbetsytor för dig för en regionen uppmanas du att skapa en standardarbetsyta och ett Automation-konto.
5. Klicka på **Aktivera** för att börjar integrera datorn.

   ![Visa integreringsalternativ](./media/automation-vm-inventory/inventory-onboarding-options.png)

    Ett statusfält meddelar dig att lösningen aktiveras. Processen kan ta upp till 15 minuter. Under denna tid kan du stänga fönstret eller ha det öppet och den meddelar dig när lösningen har aktiverats. Du kan övervaka distributionsstatusen via meddelandefönstret.

   ![Visa inventeringslösningen direkt efter integreringen](./media/automation-vm-inventory/inventory-onboarded.png)

När distributionen är klar försvinner statusfältet. Systemet samlar fortfarande in inventeringsdata och dessa data kanske inte visas än. En fullständig insamling av data kan ta 24 timmar.

## <a name="configure-your-inventory-settings"></a>Konfigurera inventeringsinställningarna

Som standard konfigureras programvara, Windows-tjänster och Linux-daemon för insamling. Om du vill inventera Windows-registret och filer kan du konfigurera inställningarna för inventeringsinsamling.

1. I den **inventering** väljer den **redigera inställningar för** längst upp i fönstret.
2. Om du vill lägga till en ny insamlingsinställning går du till inställningskategorin du vill lägga till genom att välja **Windows-registret**, **Windows-filer** och **Linux-filer**.
3. Välj lämplig kategori och klicka på **Lägg till** överst i fönstret.

Följande tabeller innehåller information om varje egenskap som kan konfigureras i olika kategorier.

### <a name="windows-registry"></a>Windows-registret

|Egenskap  |Beskrivning  |
|---------|---------|
|Enabled     | Fastställer om inställningen tillämpas        |
|Objektnamn     | Eget namn på filen som ska spåras        |
|Grupp     | Ett gruppnamn för att gruppera filer logiskt        |
|Windows-registernyckel   | Sökvägen för att söka efter filen Till exempel: ”HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup”      |

### <a name="windows-files"></a>Windows-filer

|Egenskap  |Beskrivning  |
|---------|---------|
|Enabled     | Fastställer om inställningen tillämpas        |
|Objektnamn     | Eget namn på filen som ska spåras        |
|Grupp     | Ett gruppnamn för att gruppera filer logiskt        |
|Ange sökväg     | Sökvägen för att söka efter filen Till exempel: ”c:\temp\myfile.txt”

### <a name="linux-files"></a>Linux-filer

|Egenskap  |Beskrivning  |
|---------|---------|
|Enabled     | Fastställer om inställningen tillämpas        |
|Objektnamn     | Eget namn på filen som ska spåras        |
|Grupp     | Ett gruppnamn för att gruppera filer logiskt        |
|Ange sökväg     | Sökvägen för att söka efter filen Till exempel: ”/etc/*.conf”       |
|Sökvägstyp     | Typ av objekt som ska spåras, möjliga värden är Fil och Katalog        |
|Rekursion     | Avgör om rekursion används när du letar efter objektet som ska spåras.        |
|Använda Sudo     | Den här inställningen styr om sudo ska användas vid sökningen efter objektet.         |
|Länkar     | Den här inställningen styr hur symboliska länkar ska hanteras när de passerar kataloger.<br> **Ignorera** – Ignorerar symboliska länkar och inkluderar inte refererade filer/kataloger<br>**Följ** – Följer de symboliska länkarna under rekursion och inkluderar refererade filer/kataloger<br>**Hantera** – Följer de symboliska länkarna och tillåter ändring av behandling av returnerat innehåll      |

## <a name="manage-machine-groups"></a>Hantera datorgrupper

Inventering kan du skapa och visa datorgrupper i Azure Monitor-loggar. Datorgrupper är samlingar med datorer som definieras av en fråga i Azure Monitor-loggar.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Att visa din dator grupper väljer du den **Machine grupper** fliken på sidan inventering.

![Visa datorgrupper på sidan inventering](./media/automation-vm-inventory/inventory-machine-groups.png)

Att välja en datorgrupp i listan öppnas sidan datorn grupper. Den här sidan visar information om datorgruppen. Uppgifterna omfattar bland annat log analytics-fråga som används för att definiera gruppen. Längst ned på sidan är en växlade lista över de datorer som ingår i gruppen.

![Visa datorn gruppsidan](./media/automation-vm-inventory/machine-group-page.png)

Klicka på den **+ klona** knappen för att klona datorgruppen. Här måste du ge gruppen ett nytt namn och alias för gruppen. Definitionen kan ändras just nu. När du har ändrat den frågan genom att trycka på **verifiera fråga** att förhandsgranska de datorer som markeras. När du är nöjd med gruppen klickar du på **skapa** att skapa en dator

Om du vill skapa en ny datorgrupp, Välj **+ skapa en datorgrupp**. Den här knappen öppnar den **skapa en dator servergruppssida** där du kan definiera den nya gruppen. Klicka på **skapa** att skapa gruppen.

![Skapa ny datorgrupp](./media/automation-vm-inventory/create-new-group.png)

## <a name="disconnect-your-virtual-machine-from-management"></a>Koppla bort den virtuella datorn från hantering

Ta bort den virtuella datorn från inventeringshantering:

1. Välj **Log Analytics** i den vänstra rutan på Azure Portal och välj sedan den arbetsyta du använde när du integrerade den virtuella datorn.
2. I fönstret **Log Analytics** väljer du **Virtuella datorer** under kategorin **Datakällor för arbetsyta** på **resursmenyn**.
3. Välj den virtuella dator du vill koppla bort från listan. Den virtuella datorn har en grön bock bredvid texten **Den här arbetsytan** i kolumnen **OMS-anslutning**.

   >[!NOTE]
   >OMS kallas nu för Azure Monitor-loggar.
   
4. Överst på nästa sida väljer du **Koppla från**.
5. Välj **Ja** i bekräftelsefönstret.
    Den här åtgärden kopplar från datorn från hanteringen.

## <a name="next-steps"></a>Nästa steg

* Information om hur du hanterar ändringar i inställningarna för filer och registret finns i [Track software changes in your environment with the Change Tracking solution](../log-analytics/log-analytics-change-tracking.md) (Spåra programändringar i miljön med lösningen Ändringsspårning).
* Läs om hur du hanterar Windows- och paketuppdateringar på dina virtuella datorer i [uppdateringshanteringslösningen i Azure](../operations-management-suite/oms-solution-update-management.md).

