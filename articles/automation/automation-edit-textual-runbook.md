---
title: Redigera text-Runbooks i Azure Automation
description: Den här artikeln innehåller olika procedurer för att arbeta med PowerShell-och PowerShell Workflow-Runbooks i Azure Automation med hjälp av text redigeraren.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5a7eec0a7650f9c8e04a8d1062d32b6feb7d1d99
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850864"
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Redigera text-Runbooks i Azure Automation

Text redigeraren i Azure Automation kan användas för att redigera [PowerShell-Runbooks](automation-runbook-types.md#powershell-runbooks) och [PowerShell-arbetsflöden](automation-runbook-types.md#powershell-workflow-runbooks). Detta har typiska funktioner i andra kod redigerare som IntelliSense och färg kodning med ytterligare special funktioner som hjälper dig att komma åt resurser som är gemensamma för Runbooks. Den här artikeln innehåller detaljerade anvisningar för hur du utför olika funktioner i den här redigeraren.

Text redigeraren innehåller en funktion för att infoga kod för cmdlets, till gångar och underordnade Runbooks i en Runbook. I stället för att skriva in koden själv kan du välja från en lista över tillgängliga resurser och ha rätt kod infogad i runbooken.

Varje Runbook i Azure Automation har två versioner, utkast och publicerat. Du redigerar utkast versionen av runbooken och publicerar den så att den kan köras. Den publicerade versionen kan inte redigeras. Mer information finns i [publicera en Runbook](manage-runbooks.md#publish-a-runbook).

Information om hur du arbetar med [grafiska runbooks](automation-runbook-types.md#graphical-runbooks)finns [i grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md).

## <a name="to-edit-a-runbook-with-the-azure-portal"></a>Redigera en Runbook med Azure Portal

Använd följande procedur för att öppna en Runbook för redigering i text redigeraren.

1. I Azure Portal väljer du ditt Automation-konto.
2. Under **process automatisering**väljer du **Runbooks** för att öppna listan över Runbooks.
3. Välj den Runbook som du vill redigera och klicka sedan på knappen **Redigera** .
4. Redigera runbooken.
5. Klicka på **spara** när ändringarna har slutförts.
6. Klicka på **publicera** om du vill att den senaste Utkastversionen av runbooken som ska publiceras.

### <a name="to-insert-a-cmdlet-into-a-runbook"></a>Så här infogar du en cmdlet i en Runbook

1. Placera markören där du vill placera cmdleten på arbets ytan i text redigeraren.
2. Expandera noden **cmdlets** i biblioteks kontrollen.
3. Expandera den modul som innehåller den cmdlet som du vill använda.
4. Högerklicka på cmdleten för att infoga och välj **Lägg till på arbets ytan**. Om cmdleten har fler än en parameter uppsättning läggs standard uppsättningen till. Du kan också expandera cmdleten om du vill välja en annan parameter uppsättning.
5. Koden för cmdleten infogas med hela listan med parametrar.
6. Ange ett lämpligt värde i stället för data typen som omges av klammerparenteser < > för alla obligatoriska parametrar. Ta bort alla parametrar som du inte behöver.

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>Infoga kod för en underordnad Runbook i en Runbook

1. Placera markören där du vill placera koden för [underordnad Runbook](automation-child-runbooks.md)på arbets ytan i text redigeraren.
2. Expandera noden **Runbooks** i biblioteks kontrollen.
3. Högerklicka på den Runbook som du vill infoga och välj **Lägg till på arbets ytan**.
4. Koden för den underordnade runbooken infogas med plats hållare för alla Runbook-parametrar.
5. Ersätt plats hållarna med lämpliga värden för varje parameter.

### <a name="to-insert-an-asset-into-a-runbook"></a>Så här infogar du en till gång i en Runbook

1. Placera markören där du vill placera koden för underordnad Runbook på arbets ytan i text redigeraren.
2. Expandera noden **till gångar** i biblioteks kontrollen.
3. Expandera noden för den typ av till gång du vill ha.
4. Högerklicka på den till gång som ska infogas och välj **Lägg till på arbets ytan**. För [variabel till gångar](automation-variables.md)väljer du antingen **Lägg till "Hämta variabel" på arbets ytan** eller **Lägg till "ange variabel" på arbets ytan** beroende på om du vill hämta eller ange variabeln.
5. Koden för till gången infogas i runbooken.

## <a name="to-edit-an-azure-automation-runbook-using-windows-powershell"></a>Redigera en Azure Automation Runbook med hjälp av Windows PowerShell

Om du vill redigera en Runbook med Windows PowerShell kan du använda valfri redigerare och spara den i en `.ps1`-fil. Du kan använda cmdleten [export-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Export-AzureRmAutomationRunbook) för att hämta innehållet i runbooken och sedan [Importera-AzureRmAutomationRunbook-](/powershell/module/AzureRM.Automation/import-azurermautomationrunbook) cmdlet för att ersätta den befintliga utkast runbooken med den ändrade.

### <a name="to-retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Så här hämtar du innehållet i en runbook med hjälp av Windows PowerShell

Följande exempelkommandon visar hur du hämtar skriptet för en runbook och spara det i en skriptfil. I det här exemplet hämtas utkastversionen. Det är också möjligt att hämta den publicerade versionen av runbooken även om den här versionen inte kan ändras.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzureRmAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="to-change-the-contents-of-a-runbook-using-windows-powershell"></a>Så här ändrar du innehållet i en runbook med hjälp av Windows PowerShell

Följande exempelkommandon visar hur du ersätter det befintliga innehållet i en Runbook med innehållet från en skriptfil. Det här är samma exempel procedur som i [för att importera en Runbook från en skript fil med Windows PowerShell](manage-runbooks.md#import-a-runbook).

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzureRmAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzureRmAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="related-articles"></a>Relaterade artiklar

* [Hantera Runbooks i Azure Automation](manage-runbooks.md)
* [Utbildnings-PowerShell-arbetsflöde](automation-powershell-workflow.md)
* [Grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md)
* [Certifikat](automation-certificates.md)
* [Anslutningar](automation-connections.md)
* [Autentiseringsuppgifter](automation-credentials.md)
* [Scheman](automation-schedules.md)
* [Variabler](automation-variables.md)

