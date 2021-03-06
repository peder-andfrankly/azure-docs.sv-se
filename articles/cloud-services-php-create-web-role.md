---
title: Skapa Azure Web-och Worker-roller för PHP
description: En guide för att skapa PHP Web-och Worker-roller i en moln tjänst i Azure och konfigurera PHP-körningsmiljön.
services: ''
documentationcenter: php
author: msangapu
manager: cfowler
ms.assetid: 9f7ccda0-bd96-4f7b-a7af-fb279a9e975b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.openlocfilehash: 82bb5f153a2c70d3b26f295925f8e48693bc49b9
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146871"
---
# <a name="create-php-web-and-worker-roles"></a>Skapa PHP webb- och arbetsroller

## <a name="overview"></a>Översikt

I den här guiden får du lära dig hur du skapar PHP Web-eller Worker-roller i en Windows-utvecklings miljö, väljer en speciell version av PHP från "inbyggda" versioner, ändrar PHP-konfigurationen, aktiverar tillägg och slutligen distribuerar till Azure. Det beskriver också hur du konfigurerar en webb-eller arbets roll för att använda en PHP-körning (med anpassad konfiguration och tillägg) som du anger.

Azure har tre beräkningsmodeller för körning av program: Azure App Service, Azure Virtual Machines och Azure-Cloud Services. Alla tre modeller har stöd för PHP. Cloud Services, som innehåller webb-och arbets roller, tillhandahåller *PaaS (Platform as a Service)* . I en moln tjänst tillhandahåller en webbroll en dedikerad Internet Information Services (IIS)-webb server som värd för frontend-webbprogram. En arbets roll kan köra asynkrona, långvariga eller beständig uppgifter oberoende av användar interaktion eller indata.

Mer information om de här alternativen finns i avsnittet [Compute hosting-alternativ som tillhandahålls av Azure](cloud-services/cloud-services-choose-me.md).

## <a name="download-the-azure-sdk-for-php"></a>Ladda ner Azure SDK för PHP

[Azure SDK för php](https://github.com/Azure/azure-sdk-for-php) består av flera komponenter. I den här artikeln används två av dem: Azure PowerShell och Azure-emulatorer. Dessa två komponenter kan installeras via installations programmet för Microsoft-webbplattform. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).

## <a name="create-a-cloud-services-project"></a>Skapa ett Cloud Services-projekt

Det första steget i att skapa en PHP Web-eller Worker-roll är att skapa ett Azure Service-projekt. ett Azure Service-projekt fungerar som en logisk behållare för webb-och arbets roller och innehåller projektets [tjänst definitions-(. csdef)] och [tjänst konfigurations fil (. cscfg)] .

Om du vill skapa ett nytt Azure-tjänst projekt kör du Azure PowerShell som administratör och kör följande kommando:

    PS C:\>New-AzureServiceProject myProject

Det här kommandot skapar en ny katalog (`myProject`) som du kan lägga till webb-och arbets roller för.

## <a name="add-php-web-or-worker-roles"></a>Lägg till PHP Web-eller Worker-roller

Om du vill lägga till en PHP-webbroll i ett projekt kör du följande kommando i projektets rot Katalog:

    PS C:\myProject> Add-AzurePHPWebRole roleName

Använd följande kommando för en arbets roll:

    PS C:\myProject> Add-AzurePHPWorkerRole roleName

> [!NOTE]
> `roleName` Parametern är valfri. Om den utelämnas skapas roll namnet automatiskt. Den första webb rollen som skapas `WebRole1`är, den andra `WebRole2`är, och så vidare. Den första arbets rollen som skapas `WorkerRole1`är, den andra `WorkerRole2`är, och så vidare.
>
>

## <a name="specify-the-built-in-php-version"></a>Ange den inbyggda PHP-versionen

När du lägger till en PHP-webbroll eller arbets roll i ett projekt, ändras projektets konfigurationsfiler så att PHP installeras på varje webb-eller arbets instans i programmet när den distribueras. Kör följande kommando för att se vilken version av PHP som ska installeras som standard:

    PS C:\myProject> Get-AzureServiceProjectRoleRuntime

Utdata från kommandot ovan ser ut ungefär som visas nedan. I det här exemplet `IsDefault` är flaggan inställd på `true` för php-5.3.17, vilket indikerar att den kommer att vara standard php-versionen installerad.

```
Runtime Version     PackageUri                      IsDefault
------- -------     ----------                      ---------
Node 0.6.17         http://nodertncu.blob.core...   False
Node 0.6.20         http://nodertncu.blob.core...   True
Node 0.8.4          http://nodertncu.blob.core...   False
IISNode 0.1.21      http://nodertncu.blob.core...   True
Cache 1.8.0         http://nodertncu.blob.core...   True
PHP 5.3.17          http://nodertncu.blob.core...   True
PHP 5.4.0           http://nodertncu.blob.core...   False
```

Du kan ställa in PHP runtime-versionen på någon av de PHP-versioner som visas i listan. Om du till exempel vill ange php-versionen (för en roll med namnet `roleName`) till 5.4.0, använder du följande kommando:

    PS C:\myProject> Set-AzureServiceProjectRole roleName php 5.4.0

> [!NOTE]
> Tillgängliga PHP-versioner kan ändras i framtiden.
>
>

## <a name="customize-the-built-in-php-runtime"></a>Anpassa den inbyggda PHP-körningen

Du har fullständig kontroll över konfigurationen av php-körningsmiljön som installeras när du följer stegen ovan, inklusive ändring av `php.ini` inställningar och aktivering av tillägg.

Följ dessa steg om du vill anpassa den inbyggda PHP-körningen:

1. Lägg till en ny mapp med `php`namnet, `bin` i katalogen för din webb roll. För en arbets roll lägger du till den i rollens rot Katalog.
2. I mappen `php` skapar du en annan mapp som `ext`heter. Placera eventuella `.dll` tilläggs-filer (t `php_mongo.dll`. ex.) som du vill aktivera i den här mappen.
3. Lägg till `php.ini` en fil `php` i mappen. Aktivera eventuella anpassade tillägg och ange eventuella PHP-direktiv i den här filen. Om du till exempel vill aktivera `display_errors` och `php_mongo.dll` aktivera tillägget `php.ini` är innehållet i filen följande:

        display_errors=On
        extension=php_mongo.dll

> [!NOTE]
> Alla inställningar som du inte uttryckligen anger i `php.ini` filen som du anger kommer att anges till standardvärdena automatiskt. Tänk dock på att du kan lägga till en fullständig `php.ini` fil.
>
>

## <a name="use-your-own-php-runtime"></a>Använd din egen PHP-körning

I vissa fall, i stället för att välja en inbyggd PHP-körning och konfigurera den enligt beskrivningen ovan, kanske du vill ange en egen PHP-körning. Du kan till exempel använda samma PHP-körning i en webb-eller arbets roll som du använder i din utvecklings miljö. Detta gör det enklare att se till att programmet inte ändrar beteendet i produktions miljön.

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>Konfigurera en webbroll att använda din egen PHP-körning

Följ dessa steg om du vill konfigurera en webbroll för att använda en PHP-körning som du anger:

1. Skapa ett Azure Service-projekt och Lägg till en PHP-webbroll enligt beskrivningen ovan i det här avsnittet.
2. Skapa en `php` mapp `bin` i mappen som finns i webbrollens rot Katalog och Lägg sedan till php-körningsmiljön (alla binärfiler, konfigurationsfiler, undermappar `php` osv.) i mappen.
3. VALFRITT Om din PHP-körning använder [Microsoft-drivrutinerna för php för SQL Server][sqlsrv drivers]måste du konfigurera din webbroll för att installera [SQL Server Native Client 2012][sql native client] när den har tillhandahållits. Det gör du genom att lägga till [installations programmet SQLNCLI. msi x64] i `bin` mappen i webbrollens rot Katalog. Start skriptet som beskrivs i nästa steg kommer tyst att köra installations programmet när rollen är etablerad. Om din PHP-körning inte använder Microsoft-drivrutinerna för PHP för SQL Server, kan du ta bort följande rad från skriptet som visas i nästa steg:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Definiera en start åtgärd som konfigurerar [Internet Information Services (IIS)][iis.net] att använda din php-körning för att hantera begär `.php` Anden för sidor. Det gör du genom att öppna `setup_web.cmd` filen ( `bin` i filen för webbrollens rot Katalog) i en text redigerare och ersätta innehållet med följande skript:

    ```cmd
    @ECHO ON
    cd "%~dp0"

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    SET PHP_FULL_PATH=%~dp0php\php-cgi.exe
    SET NEW_PATH=%PATH%;%RoleRoot%\base\x86

    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%',maxInstances='12',idleTimeout='60000',activityTimeout='3600',requestTimeout='60000',instanceMaxRequests='10000',protocol='NamedPipe',flushNamedPipe='False']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PATH',value='%NEW_PATH%']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PHP_FCGI_MAX_REQUESTS',value='10000']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/handlers /+"[name='PHP',path='*.php',verb='GET,HEAD,POST',modules='FastCgiModule',scriptProcessor='%PHP_FULL_PATH%',resourceType='Either',requireAccess='Script']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /"[fullPath='%PHP_FULL_PATH%'].queueLength:50000"
    ```
5. Lägg till dina programfiler till din webb Rolls rot Katalog. Detta är webb serverns rot Katalog.
6. Publicera ditt program enligt beskrivningen i avsnittet [publicera ditt program](#publish-your-application) nedan.

> [!NOTE]
> `download.ps1` Skriptet (`bin` i mappen i webbrollens rot Katalog) kan tas bort när du följer stegen som beskrivs ovan för att använda din egen php-körning.
>
>

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>Konfigurera en arbets roll för att använda din egen PHP-körning

Följ dessa steg om du vill konfigurera en arbets roll för att använda en PHP-körning som du anger:

1. Skapa ett Azure Service-projekt och Lägg till en PHP-arbetsroll enligt beskrivningen ovan.
2. Skapa en `php` mapp i arbets rollens rot Katalog och Lägg sedan till php-körningsmiljön (alla binärfiler, konfigurationsfiler, undermappar osv.) `php` i mappen.
3. VALFRITT Om din PHP-körning använder [Microsoft-drivrutiner för php för SQL Server][sqlsrv drivers]måste du konfigurera arbets rollen för att installera [SQL Server Native Client 2012][sql native client] när den är etablerad. Det gör du genom att lägga till [installations programmet SQLNCLI. msi x64] i arbets rollens rot Katalog. Start skriptet som beskrivs i nästa steg kommer tyst att köra installations programmet när rollen är etablerad. Om din PHP-körning inte använder Microsoft-drivrutinerna för PHP för SQL Server, kan du ta bort följande rad från skriptet som visas i nästa steg:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Definiera en start åtgärd som lägger till `php.exe` din körbara fil i miljövariabeln för arbets rollens sökväg när rollen är etablerad. Det gör du genom att öppna `setup_worker.cmd` filen (i arbets rollens rot Katalog) i en text redigerare och ersätta dess innehåll med följande skript:

    ```cmd
    @echo on

    cd "%~dp0"

    echo Granting permissions for Network Service to the web root directory...
    icacls ..\ /grant "Network Service":(OI)(CI)W
    if %ERRORLEVEL% neq 0 goto error
    echo OK

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    setx Path "%PATH%;%~dp0php" /M

    if %ERRORLEVEL% neq 0 goto error

    echo SUCCESS
    exit /b 0

    :error

    echo FAILED
    exit /b -1
    ```
5. Lägg till dina programfiler i arbets rollens rot Katalog.
6. Publicera ditt program enligt beskrivningen i avsnittet [publicera ditt program](#publish-your-application) nedan.

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>Kör ditt program i beräknings-och lagrings emulatorerna

Azure-emulatorer tillhandahåller en lokal miljö där du kan testa ditt Azure-program innan du distribuerar det till molnet. Det finns vissa skillnader mellan emulatorerna och Azure-miljön. För att förstå detta bättre, se [använda Azure Storage-emulatorn för utveckling och testning](storage/common/storage-use-emulator.md).

Observera att du måste ha PHP installerat lokalt för att kunna använda Compute-emulatorn. Compute-emulatorn använder den lokala PHP-installationen för att köra programmet.

Kör projektet i emulatorn genom att köra följande kommando från projektets rot Katalog:

    PS C:\MyProject> Start-AzureEmulator

Resultatet ser ut ungefär så här:

    Creating local package...
    Starting Emulator...
    Role is running at http://127.0.0.1:81
    Started

Du kan se programmet som körs i emulatorn genom att öppna en webbläsare och bläddra till den lokala adressen som visas i utdata (`http://127.0.0.1:81` i exempel resultatet ovan).

Kör följande kommando för att stoppa emulatorn:

    PS C:\MyProject> Stop-AzureEmulator

## <a name="publish-your-application"></a>Publicera programmet

Om du vill publicera ditt program måste du först importera dina publicerings inställningar med hjälp av cmdleten [import-AzurePublishSettingsFile](https://docs.microsoft.com/powershell/module/servicemanagement/azure/import-azurepublishsettingsfile) . Sedan kan du publicera programmet med hjälp av cmdleten [Publish-AzureServiceProject](https://docs.microsoft.com/powershell/module/servicemanagement/azure/publish-azureserviceproject) . Information om hur du loggar in finns i [så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

## <a name="next-steps"></a>Nästa steg

Mer information finns i [php Developer Center](https://azure.microsoft.com/develop/php/).

[install ps and emulators]: https://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[tjänst definitions-(. csdef)]: https://msdn.microsoft.com/library/windowsazure/ee758711.aspx
[tjänst konfigurations fil (. cscfg)]: https://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[iis.net]: https://www.iis.net/
[sql native client]: https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation
[sqlsrv drivers]: https://php.net/sqlsrv
[installations programmet SQLNCLI. msi x64]: https://go.microsoft.com/fwlink/?LinkID=239648
