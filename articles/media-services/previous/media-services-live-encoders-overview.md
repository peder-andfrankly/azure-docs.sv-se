---
title: Konfigurera lokala kodare med Azure Media Services för att skapa dataströmmar med flera bithastigheter | Microsoft Docs
description: Det här avsnittet innehåller en lokal livekodare som du kan använda för att spara din live-händelser och skicka en direktsänd dataström med enkel bithastighet till AMS-kanaler (som är direktsänd kodning aktiverat) för vidare bearbetning. Innehåller avsnittslänkar till självstudier om hur du konfigurerar listade kodare.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 0ec6f046-0841-4673-9057-883bdbc30d5c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 8bd0014518dc93abb952114a1728f8fc1d3fb4a5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64708073"
---
# <a name="how-to-configure-on-premises-encoders-when-using-azure-media-services-to-create-multi-bitrate-streams"></a>Så här konfigurerar du lokala kodare med Azure Media Services för att skapa dataströmmar med flera bithastigheter
Det här avsnittet innehåller en lokal livekodare som du kan använda för att spara din live-händelser och skicka en direktsänd dataström med enkel bithastighet till AMS-kanaler (som är direktsänd kodning aktiverat) för vidare bearbetning. Avsnittet innehåller också länkar till självstudier om hur du konfigurerar listade kodare.

> [!NOTE]
> Vid direktuppspelning via RTMP ska du kontrollera inställningarna för brandvägg och /eller proxy för att bekräfta att de utgående TCP-portarna 1935 och 1936 är öppna.

## <a name="flash-media-live-encoder"></a>Flash Media Live Encoder
Information om hur du konfigurerar den [Flash Media Live Encoder](https://www.adobe.com/products/flash-media-encoder.html) encoder (FMLE) att skicka en direktsänd dataström med enkel bithastighet till en kanal för AMS finns i [konfigurera FMLE](media-services-configure-fmle-live-encoder.md).

## <a name="haivision-kb-encoder"></a>Haivision KB-kodare
Information om hur du konfigurerar den [Haivision KB-kodare](https://www.haivision.com/products/kb-series/) encoder att skicka en direktsänd dataström med enkel bithastighet till en kanal för AMS finns i [konfigurera Haivision KB-kodare](media-services-configure-kb-live-encoder.md).

## <a name="telestream-wirecast"></a>Telestream Wirecast
Information om hur du konfigurerar den [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) encoder att skicka en direktsänd dataström med enkel bithastighet till en kanal för AMS finns i [konfigurera Wirecast](media-services-configure-wirecast-live-encoder.md).

## <a name="newtek-tricaster"></a>NewTek TriCaster
Information om hur du konfigurerar den [Tricaster](https://newtek.com/products/tricaster-40.html) encoder att skicka en direktsänd dataström med enkel bithastighet till en kanal för AMS finns i [konfigurera Tricaster](media-services-configure-tricaster-live-encoder.md).

## <a name="elemental-live"></a>Elemental Live
Mer information finns i [Elemental Live](https://www.elementaltechnologies.com/products/elemental-live).

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Nästa steg

[Liveuppspelning med Azure Media Services för att skapa dataströmmar med flera bithastigheter](media-services-manage-live-encoder-enabled-channels.md).

