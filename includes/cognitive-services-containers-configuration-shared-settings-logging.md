---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 873fd8cbc211f098c93b8fb3fbe701e4a34d8487
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68320529"
---
Den `Logging` inställningar hantera ASP.NET Core loggningsstöd för din behållare. Du kan använda samma konfigurationsinställningar och värden för din behållare som du använder för ett ASP.NET Core-program. 

Följande loggnings leverantörer stöds av behållaren:

|Leverantör|Syfte|
|--|--|
|[Konsol](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|ASP.NET Core `Console` loggningsprovider. Alla konfigurationsinställningar för ASP.NET Core och standardvärden för den här loggningsprovider stöds.|
|[Felsökning](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|ASP.NET Core `Debug` loggningsprovider. Alla konfigurationsinställningar för ASP.NET Core och standardvärden för den här loggningsprovider stöds.|
|[Disk](#disk-logging)|Loggningsprovider JSON. Den här loggningsprovider skriver loggdata till utdata mount.|

Det här behållar kommandot lagrar loggnings information i JSON-format till utmatnings monteringen:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
Logging:Disk:Format=json
```

Det här behållar kommandot visar fel söknings information, `dbug`föregåd av, medan behållaren körs:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
Logging:Console:LogLevel:Default=Debug
```

### <a name="disk-logging"></a>Disk-loggning

Den `Disk` loggningsprovider stöder följande konfigurationsinställningar:

| Namn | Datatyp | Beskrivning |
|------|-----------|-------------|
| `Format` | Sträng | Utdataformat för loggfiler.<br/> **Obs:** Värdet måste anges till `json` om du vill aktivera loggnings leverantören. Ett fel inträffar om det här värdet anges utan att också ange en utdata mount vid instansiering en behållare. |
| `MaxFileSize` | Integer | Den maximala storleken i megabyte (MB) på en loggfil. När storleken på den aktuella loggfilen uppfyller eller överskrider detta värde, har en ny loggfil startats med loggningsprovider. Om -1 anges begränsas storleken på loggfilen bara av den maximala filstorleken för utdata mount. Standardvärdet är 1. |

Läs mer om hur du konfigurerar ASP.NET Core loggningsstöd [inställningar filkonfiguration](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1).

