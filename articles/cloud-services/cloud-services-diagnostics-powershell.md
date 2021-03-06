---
title: Aktivera diagnostik i Azure Cloud Services med PowerShell | Microsoft Docs
description: Lär dig hur du aktiverar diagnostik för Cloud Services med PowerShell
services: cloud-services
documentationcenter: .net
author: georgewallace
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.date: 09/06/2016
ms.author: gwallace
ms.openlocfilehash: f2b7e51971cc2e540ee7745b3b44571c58359613
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860213"
---
# <a name="enable-diagnostics-in-azure-cloud-services-using-powershell"></a>Aktivera diagnostik i Azure Cloud Services med PowerShell
Du kan samla in diagnostikdata som program loggar, prestanda räknare osv. från en moln tjänst med hjälp av Azure-diagnostik tillägget. I den här artikeln beskrivs hur du aktiverar Azure-diagnostik-tillägget för en moln tjänst med hjälp av PowerShell.  Se [så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview) för de krav som krävs för den här artikeln.

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Aktivera diagnostiktillägget som en del av distributionen av en molntjänst
Den här metoden gäller för kontinuerlig integrations typ av scenarier där diagnostikprogrammet kan aktive ras som en del av distributionen av moln tjänsten. När du skapar en ny moln tjänst distribution kan du aktivera tillägget för diagnostik genom att skicka in parametern *ExtensionConfiguration* till cmdleten [New-AzureDeployment](/powershell/module/servicemanagement/azure/new-azuredeployment?view=azuresmps-3.7.0) . Parametern *ExtensionConfiguration* tar en matris med diagnostiska konfigurationer som kan skapas med cmdleten [New-AzureServiceDiagnosticsExtensionConfig](/powershell/module/servicemanagement/azure/new-azureservicediagnosticsextensionconfig?view=azuresmps-3.7.0) .

I följande exempel visas hur du kan aktivera diagnostik för en moln tjänst med en webrole-och WorkerRole, som var och en har en annan diagnostisk konfiguration.

```powershell
$service_name = "MyService"
$service_package = "CloudService.cspkg"
$service_config = "ServiceConfiguration.Cloud.cscfg"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)
```

Om diagnostikens konfigurations fil anger `StorageAccount` ett element med ett lagrings konto namn `New-AzureServiceDiagnosticsExtensionConfig` använder cmdleten automatiskt det lagrings kontot. För att detta ska fungera måste lagrings kontot finnas i samma prenumeration som moln tjänsten som distribueras.

Från Azure SDK 2,6 kommer det att finnas lagrings konto namnet baserat på den diagnostiska konfigurations sträng som anges i tjänst konfigurations filen (. cscfg). Skriptet nedan visar hur du kan parsa konfigurationsfilerna för tillägg från publicerings målets utdata och konfigurera diagnostikprogrammet för varje roll när du distribuerar moln tjänsten.

```powershell
$service_name = "MyService"
$service_package = "C:\build\output\CloudService.cspkg"
$service_config = "C:\build\output\ServiceConfiguration.Cloud.cscfg"

#Find the Extensions path based on service configuration file
$extensionsSearchPath = Join-Path -Path (Split-Path -Parent $service_config) -ChildPath "Extensions"

$diagnosticsExtensions = Get-ChildItem -Path $extensionsSearchPath -Filter "PaaSDiagnostics.*.PubConfig.xml"
$diagnosticsConfigurations = @()
foreach ($extPath in $diagnosticsExtensions)
{
    #Find the RoleName based on file naming convention PaaSDiagnostics.<RoleName>.PubConfig.xml
    $roleName = ""
    $roles = $extPath -split ".",0,"simplematch"
    if ($roles -is [system.array] -and $roles.Length -gt 1)
    {
        $roleName = $roles[1]
        $x = 2
        while ($x -le $roles.Length)
            {
               if ($roles[$x] -ne "PubConfig")
                {
                    $roleName = $roleName + "." + $roles[$x]
                }
                else
                {
                    break
                }
                $x++
            }
        $fullExtPath = Join-Path -path $extensionsSearchPath -ChildPath $extPath
        $diagnosticsconfig = New-AzureServiceDiagnosticsExtensionConfig -Role $roleName -DiagnosticsConfigurationPath $fullExtPath
        $diagnosticsConfigurations += $diagnosticsconfig
    }
}
New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration $diagnosticsConfigurations
```

Visual Studio Online använder en liknande metod för automatiserade distributioner av Cloud Services med tillägget Diagnostics. Se [Publish-AzureCloudDeployment. ps1](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureCloudPowerShellDeploymentV1/Publish-AzureCloudDeployment.ps1) för ett fullständigt exempel.

Om inget `StorageAccount` har angetts i diagnostik-konfigurationen måste du skicka in parametern *StorageAccountName* till cmdleten. Om parametern *StorageAccountName* anges använder cmdlet: en alltid det lagrings konto som anges i parametern och inte den som anges i konfigurations filen för diagnostik.

Om ditt lagrings konto för diagnostik finns i en annan prenumeration än moln tjänsten måste du uttryckligen skicka in parametrarna *StorageAccountName* och *StorageAccountKey* till cmdleten. *StorageAccountKey* -parametern behövs inte när ditt lagrings konto för diagnostik finns i samma prenumeration, eftersom cmdleten automatiskt kan fråga efter och ange nyckel värde när du aktiverar tillägget diagnostik. Men om kontot för diagnostik-diagnostik finns i en annan prenumeration kanske cmdleten inte kan hämta nyckeln automatiskt och du måste uttryckligen ange nyckeln via parametern *StorageAccountKey* .

```powershell
$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Aktivera diagnostiktillägg i en befintlig molntjänst
Du kan använda cmdleten [set-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure/set-azureservicediagnosticsextension?view=azuresmps-3.7.0) för att aktivera eller uppdatera diagnostikinställningar på en moln tjänst som redan körs.

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

```powershell
$service_name = "MyService"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

Set-AzureServiceDiagnosticsExtension -DiagnosticsConfiguration @($webrole_diagconfig,$workerrole_diagconfig) -ServiceName $service_name
```

## <a name="get-current-diagnostics-extension-configuration"></a>Hämta den aktuella konfigurationen för diagnostiktillägg
Använd cmdleten [Get-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure/get-azureservicediagnosticsextension?view=azuresmps-3.7.0) för att hämta den aktuella diagnostik-konfigurationen för en moln tjänst.

```powershell
Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

## <a name="remove-diagnostics-extension"></a>Ta bort diagnostiktillägg
Om du vill inaktivera diagnostik på en moln tjänst kan du använda cmdleten [Remove-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure/remove-azureservicediagnosticsextension?view=azuresmps-3.7.0) .

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Om du har aktiverat tillägget för diagnostik med antingen *set-AzureServiceDiagnosticsExtension* eller *New-AzureServiceDiagnosticsExtensionConfig* utan *roll* parameter kan du ta bort tillägget med  *Remove-AzureServiceDiagnosticsExtension* utan *roll* parametern. Om *roll* parametern användes när du aktiverade tillägget måste den också användas när du tar bort tillägget.

Så här tar du bort diagnostiktillägget från varje enskild roll:

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```

## <a name="next-steps"></a>Nästa steg
* Ytterligare vägledning om hur du använder Azure Diagnostics och andra tekniker för att felsöka problem finns i [Aktivera diagnostik i Azure Cloud Services och Virtual Machines](cloud-services-dotnet-diagnostics.md).
* [Konfigurations schemat för diagnostik](/azure/azure-monitor/platform/diagnostics-extension-schema-1dot3) förklarar olika alternativ för XML-konfigurationer för tillägget för diagnostik.
* Information om hur du aktiverar diagnostik-tillägget för Virtual Machines finns i [skapa en virtuell Windows-dator med övervakning och diagnostik med Azure Resource Manager mall](../virtual-machines/windows/extensions-diagnostics-template.md)
