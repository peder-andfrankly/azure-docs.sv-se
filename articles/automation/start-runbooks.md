---
title: Starta en Runbook i Azure Automation
description: Sammanfattar de olika metoder som kan användas för att starta en Runbook i Azure Automation och innehåller information om hur du använder både Azure Portal och Windows PowerShell.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a6ef8d81e8a2845e62bf25d0bba4d6967cca65a4
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849419"
---
# <a name="start-a-runbook-in-azure-automation"></a>Starta en Runbook i Azure Automation

I följande tabell får du hjälp att avgöra hur du ska starta en Runbook i Azure Automation som passar bäst för ditt specifika scenario. Den här artikeln innehåller information om hur du startar en Runbook med Azure Portal och med Windows PowerShell. Information om andra metoder finns i annan dokumentation som du kan komma åt från länkarna nedan.

| **Metod** | **Förhållanden** |
| --- | --- |
| [Azure-portalen](#start-a-runbook-with-the-azure-portal) |<li>Enklaste metoden med Interactive User Interface.<br> <li>Form för att tillhandahålla enkla parameter värden.<br> <li>Spåra jobbets status enkelt.<br> <li>Åtkomst autentiserad med Azure-inloggning. |
| [Windows PowerShell](/powershell/module/azurerm.automation/start-azurermautomationrunbook) |<li>Anropa från kommando raden med Windows PowerShell-cmdletar.<br> <li>Kan inkluderas i automatiserad lösning med flera steg.<br> <li>Begäran autentiseras med certifikat eller användar huvud/tjänstens huvud namn för OAuth-användare.<br> <li>Ange enkla och komplexa parameter värden.<br> <li>Spåra jobb status.<br> <li>Klienten krävde för att stödja PowerShell-cmdletar. |
| [Azure Automation-API](/rest/api/automation/) |<li>Den mest flexibla metoden, men även mest komplex.<br> <li>Anropa från en anpassad kod som kan göra HTTP-förfrågningar.<br> <li>Begäran autentiserad med certifikat eller användar huvud för OAuth/tjänstens huvud namn.<br> <li>Ange enkla och komplexa parameter värden. *Om du anropar en python-Runbook med API: t måste JSON-nyttolasten serialiseras.*<br> <li>Spåra jobb status. |
| [Webhooks](automation-webhooks.md) |<li>Starta Runbook från en enskild HTTP-begäran.<br> <li>Autentiserad med säkerhetstoken i URL.<br> <li>Klienten kan inte åsidosätta parameter värden som anges när webhooken skapades. En Runbook kan definiera en enda parameter som är ifylld med HTTP-begärans information.<br> <li>Det går inte att spåra jobb status via webhook-URL. |
| [Svara på Azure-avisering](../log-analytics/log-analytics-alerts.md) |<li>Starta en Runbook som svar på Azure-aviseringen.<br> <li>Konfigurera webhook för Runbook och länka till avisering.<br> <li>Autentiserad med säkerhetstoken i URL. |
| [Schema](automation-schedules.md) |<li>Starta automatiskt Runbook på varje timme, varje dag, varje vecka eller månads schema.<br> <li>Manipulera schemat via Azure Portal, PowerShell-cmdletar eller Azure API.<br> <li>Ange parameter värden som ska användas med schemat. |
| [Från en annan Runbook](automation-child-runbooks.md) |<li>Använd en Runbook som en aktivitet i en annan Runbook.<br> <li>Användbart för funktioner som används av flera Runbooks.<br> <li>Ange parameter värden för underordnad Runbook och Använd utdata i överordnad Runbook. |

Följande bild illustrerar en detaljerad steg-för-steg-process i livs cykeln för en Runbook. Den innehåller olika sätt som en Runbook startar i Azure Automation, vilka komponenter som krävs för att Hybrid Runbook Worker ska kunna köra Azure Automation runbooks och samverkan mellan olika komponenter. Information om hur du kör Automation-runbooks i ditt data Center finns i [hybrid Runbook Worker](automation-hybrid-runbook-worker.md)

![Runbook-arkitektur](media/automation-starting-runbook/runbooks-architecture.png)

## <a name="start-a-runbook-with-the-azure-portal"></a>Starta en Runbook med Azure Portal

1. I Azure Portal väljer du **Automation** och klickar sedan på namnet på ett Automation-konto.
2. På menyn hubb väljer du **Runbooks**.
3. På sidan **Runbooks** väljer du en Runbook och klickar sedan på **Starta**.
4. Om runbooken har parametrar uppmanas du att ange värden med en text ruta för varje parameter. Mer information om parametrar finns i [Runbook-parametrar](#runbook-parameters).
5. På sidan **jobb** kan du visa statusen för Runbook-jobbet.

## <a name="start-a-runbook-with-powershell"></a>Starta en Runbook med PowerShell

Du kan använda [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook) för att starta en Runbook med Windows PowerShell. Följande exempelkod startar en Runbook med namnet Test-Runbook.

```azurepowershell-interactive
Start-AzureRmAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

Start-AzureRmAutomationRunbook returnerar ett jobb objekt som du kan använda för att spåra dess status när Runbook startas. Du kan sedan använda detta jobb objekt med [Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob) för att bestämma status för jobbet och [Get-AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) för att få dess utdata. Följande exempelkod startar en Runbook med namnet Test-Runbook, väntar tills den har slutförts och visar sedan dess utdata.

```azurepowershell-interactive
$runbookName = "Test-Runbook"
$ResourceGroup = "ResourceGroup01"
$AutomationAcct = "MyAutomationAccount"

$job = Start-AzureRmAutomationRunbook –AutomationAccountName $AutomationAcct -Name $runbookName -ResourceGroupName $ResourceGroup

$doLoop = $true
While ($doLoop) {
   $job = Get-AzureRmAutomationJob –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup
   $status = $job.Status
   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzureRmAutomationJobOutput –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup –Stream Output
```

Om Runbook kräver parametrar måste du ange dem som en [hash](https://technet.microsoft.com/library/hh847780.aspx)-form. Nyckeln för hash-tabellen måste matcha parameter namnet och värdet är parametervärdet. I följande exempel visas hur du startar en Runbook med två strängparametrar som heter FirstName och LastName, ett heltal som heter RepeatCount och en boolesk parameter som heter Show. Mer information om parametrar finns i [Runbook-parametrar](#runbook-parameters) nedan.

```azurepowershell-interactive
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" –Parameters $params
```

## <a name="runbook-parameters"></a>Runbook-parametrar

När du startar en Runbook från Azure Portal eller Windows PowerShell skickas instruktionen via Azure Automation-webbtjänsten. Den här tjänsten stöder inte parametrar med komplexa data typer. Om du behöver ange ett värde för en komplex parameter måste du anropa den infogad från en annan Runbook enligt beskrivningen i [underordnade Runbooks i Azure Automation](automation-child-runbooks.md).

Azure Automation-webbtjänsten tillhandahåller särskilda funktioner för parametrar med vissa data typer som beskrivs i följande avsnitt:

### <a name="named-values"></a>Namngivna värden

Om parametern är data typen [objekt], kan du använda följande JSON-format för att skicka en lista över namngivna värden: *{Name1: "värde1", Name2: "värde2", Name3: ' Value3 '}* . Dessa värden måste vara enkla typer. Runbooken tar emot parametern som en [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject) med egenskaper som motsvarar varje namngivet värde.

Överväg följande test-Runbook som accepterar en parameter med namnet user.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][object]$user
   )
    $userObject = $user | ConvertFrom-JSON
    if ($userObject.Show) {
        foreach ($i in 1..$userObject.RepeatCount) {
            $userObject.FirstName
            $userObject.LastName
        }
    }
}
```

Följande text kan användas för user-parameter.

```json
{FirstName:'Joe',LastName:'Smith',RepeatCount:'2',Show:'True'}
```

Detta resulterar i följande utdata:

```output
Joe
Smith
Joe
Smith
```

### <a name="arrays"></a>Matriser

Om parametern är en matris som t. ex. [array] eller [string []], kan du använda följande JSON-format för att skicka en lista med värden: *[värde1, värde2, Value3]* . Dessa värden måste vara enkla typer.

Överväg följande test-Runbook som accepterar en parameter med namnet *user*.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][array]$user
   )
    if ($user[3]) {
        foreach ($i in 1..$user[2]) {
            $ user[0]
            $ user[1]
        }
    }
}
```

Följande text kan användas för user-parameter.

```input
["Joe","Smith",2,true]
```

Detta resulterar i följande utdata:

```output
Joe
Smith
Joe
Smith
```

### <a name="credentials"></a>Autentiseringsuppgifter

Om parametern är data typen **PSCredential**kan du ange namnet på en Azure Automation [behörighet till till gång](automation-credentials.md). Runbooken hämtar autentiseringsuppgiften med det namn som du anger.

Överväg följande test-Runbook som accepterar en parameter med namnet credential.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

Följande text kan användas för användar parametern förutsatt att det fanns en Credential-till gång som kallas *mina autentiseringsuppgifter*.

```input
My Credential
```

Förutsatt att användar namnet i autentiseringsuppgiften var *jsmith*, kommer detta att resultera i följande utdata:

```output
jsmith
```

## <a name="next-steps"></a>Nästa steg

* Runbook-arkitekturen i den aktuella artikeln innehåller en översikt över Runbooks som hanterar resurser i Azure och lokalt med Hybrid Runbook Worker. Information om hur du kör Automation-runbooks i ditt data Center finns i [hybrid Runbook Worker](automation-hybrid-runbook-worker.md).
* Mer information om hur du skapar modulära Runbooks som ska användas av andra Runbooks för vissa eller vanliga funktioner finns i [underordnade Runbooks](automation-child-runbooks.md).
* Mer information om PowerShell, inklusive språk referens-och inlärnings moduler finns i [PowerShell-dokumenten](https://docs.microsoft.com/powershell/scripting/overview).
