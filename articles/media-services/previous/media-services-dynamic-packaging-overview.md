---
title: Översikt över Azure Media Services dynamisk paketering | Microsoft Docs
description: I den här artikeln får du en översikt över Microsoft Azure Media Services dynamisk paketering.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: juliako
ms.openlocfilehash: 079094965775c140c0343da98e40fd008995d45a
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74901192"
---
# <a name="dynamic-packaging"></a>Dynamisk paketering

> [!div class="op_single_selector" title1="Välj den version av Media Services som du använder:"]
> * [Version 3](../latest/dynamic-packaging-overview.md)
> * [Version 2](media-services-dynamic-packaging-overview.md)

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Upptäck den senaste versionen, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Se även [vägledning för migrering från v2 till v3](../latest/migrate-from-v2-to-v3.md)

Microsoft Azure Media Services kan användas för att leverera många medie käll fil format, medie direkt uppspelnings format och innehålls skydds format till en mängd olika klient tekniker (till exempel iOS, XBOX, Silverlight, Windows 8). De här klienterna förstår olika protokoll, till exempel om iOS kräver ett HTTP Live Streaming (HLS) v4-format och Silverlight och Xbox kräver Smooth Streaming. Om du har en uppsättning MP4-filer med anpassningsbar bit hastighet (ISO-Base 14496-12) eller en uppsättning anpassade bit hastighets Smooth Streaming filer som du vill använda till klienter som förstår MPEG-streck, HLS eller Smooth Streaming, bör du dra nytta av media Tjänsters dynamiska paket.

Med dynamisk paketering måste du skapa en till gång som innehåller en uppsättning MP4-filer med anpassningsbar bit hastighet eller anpassade bit hastigheter Smooth Streaming filer. Sedan, baserat på det angivna formatet i manifest-eller fragment förfrågningen, ser servern för strömning på begäran att du får data strömmen i det protokoll som du har valt. Detta innebär att du bara behöver lagra och betala för filerna i ett enda lagringsformat, och Media Services-tjänsten skapar och ger lämplig respons baserat på begäranden från en klient.

I följande diagram visas arbets flödet traditionell kodning och statisk paketering.

![Statisk kodning](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

I följande diagram visas arbets flödet för dynamisk paketering.

![Dynamisk kodning](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="common-scenario"></a>Vanligt scenario

1. Ladda upp en indatafil (kallas en mezzaninfil-fil). Till exempel H. 264, MP4 eller WMV (för listan över format som stöds finns [det format som stöds av Media Encoder Standard](media-services-media-encoder-standard-formats.md).
2. Koda din mezzaninfil-fil till H. 264 MP4-anpassade bit hastighets uppsättningar.
3. Publicera till gången som innehåller MP4-uppsättningen med anpassad bit hastighet genom att skapa en lokaliserare på begäran.
4. Bygg URL: erna för strömning för att komma åt och strömma ditt innehåll.

## <a name="preparing-assets-for-dynamic-streaming"></a>Förbereda till gångar för dynamisk strömning

För att förbereda din till gång för dynamisk strömning har du följande alternativ:

- [Ladda upp en huvud fil](media-services-dotnet-upload-files.md).
- [Använd Media Encoder Standard Encoder för att skapa H. 264 MP4-uppsättningar med anpassad bit hastighet](media-services-dotnet-encode-with-media-encoder-standard.md).
- [Strömma ditt innehåll](media-services-deliver-content-overview.md).

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Ljud-codec som stöds av dynamisk paketering

Dynamisk paketering stöder MP4-filer som innehåller ljud som är kodat med [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, HE-AAC v2), [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus)(Enhanced AC-3 eller E-AC3), Dolby Atmos eller [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, DTS LBR, DTS HD, DTS HD-förstörande). Strömning av Dolby Atmos-innehåll stöds för standarder som MPEG-streck-protokollet med antingen common streaming format (CSF) eller common Media Application format (CMAF), fragmenterade MP4 och via HTTP Live Streaming (HLS) med CMAF.

> [!NOTE]
> Dynamisk paketering stöder inte filer som innehåller [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) -ljud (AC3) (det är en äldre codec).

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

