---
title: Koda en till gång genom att använda Media Encoder Standard i Azure Portal | Microsoft Docs
description: Den här självstudien vägleder dig genom stegen för att koda en till gång genom att använda Media Encoder Standard i Azure Portal.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 107d9e9a-71e9-43e5-b17c-6e00983aceab
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 7c147d99eaabee70316521d1f2bdc41933162ab1
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69542608"
---
# <a name="encode-an-asset-by-using-media-encoder-standard-in-the-azure-portal"></a>Koda en till gång genom att använda Media Encoder Standard i Azure Portal

> [!NOTE]
> Du behöver ett Azure-konto för att slutföra den här självstudien. Mer information finns i avsnittet om [den kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Ett av de vanligaste scenarierna när du arbetar med Azure Media Services levererar direkt uppspelning med anpassningsbar bit hastighet till dina klienter. Media Services stöder följande strömmande tekniker med anpassningsbar bit hastighet: Apple HTTP Live Streaming (HLS), Microsoft Smooth Streaming och dynamisk adaptiv strömning via HTTP (bindestreck, kallas även MPEG-streck). För att förbereda dina videor för strömning med anpassningsbar bit hastighet måste du först koda din käll video som filer med flera bit hastigheter. Du kan använda Media Encoder Standard för att koda dina videor.  

Media Services ger dig dynamisk paketering. Med dynamisk paketering kan du leverera Multibits-hastigheter i HLS, Smooth Streaming och MPEG-streck, utan att packa om dessa strömmande format. När du använder dynamisk paketering kan du lagra och betala för filerna i ett enda lagrings format. Media Services skapar och hanterar lämpligt svar baserat på en klients begäran.

Om du vill använda dynamisk paketering måste du koda källfilen i en uppsättning MP4-filer med flera bithastigheter. Kodnings stegen visas längre fram i den här artikeln.

Information om hur du skalar medie bearbetning finns i [skala medie bearbetning med hjälp av Azure Portal](media-services-portal-scale-media-processing.md).

## <a name="encode-in-the-azure-portal"></a>Koda i Azure Portal

För att koda ditt innehåll med hjälp av Media Encoder Standard:

1. Välj ditt Azure Media Services-konto i [Azure-portalen](https://portal.azure.com/).
2. Välj **Inställningar** > **Tillgångar**. Välj den tillgång som du vill koda.
3. Välj knappen **Koda**.
4. I fönstret **Koda en tillgång** väljer du processorn **Media Encoder Standard** och en förinställning. Information om förinställningar finns i [Auto-generate a bitrate ladder](media-services-autogen-bitrate-ladder-with-mes.md) (Autogenerera en bithastighetsstege) och [Task Presets for Media Encoder Standard](media-services-mes-presets-overview.md) (Uppgiftsförinställningar för Media Encoder Standard). Det är viktigt att välja den förinställning som fungerar bäst för indatavideon. Om du till exempel vet att indatavideon har en upplösning på 1 920 &#215; 1 080 bildpunkter kan du välja förinställningen **H264 multibithastighet 1080p**. Om du har en video med låg upplösning (640 &#215; 360) ska du inte använda förinställningen **H264 multibithastighet 1080p**.
   
   Du kan underlätta hanteringen av resurserna genom att redigera namnet på utdatatillgången och namnet på jobbet.
   
   ![Koda tillgångar](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Välj **Skapa**.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Nästa steg
* [Övervaka förloppet för kodnings jobbet](media-services-portal-check-job-progress.md) i Azure Portal.  

