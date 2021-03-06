---
title: Migrera från Azure Media Encoder till Media Encoder Standard | Microsoft Docs
description: I det här avsnittet beskrivs hur du migrerar från Azure Media Encoder till Media Encoder Standard medie processor.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2019
ms.author: juliako
ms.openlocfilehash: e9c83a25147696b5e492241a191b3104df001c7c
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2019
ms.locfileid: "74424019"
---
# <a name="migrate-from-azure-media-encoder-to-media-encoder-standard"></a>Migrera från Azure Media Encoder till Media Encoder Standard

Den här artikeln beskriver stegen för att migrera från det äldre medie processorn för Azure Media Encoder (amn), som dras tillbaka den 1 mars 2020 till den Media Encoder Standard medie processorn.  

När du kodar filer med amn använder kunderna vanligt vis en namngiven förinställd sträng, till exempel `H264 Adaptive Bitrate MP4 Set 1080p`. För att du ska kunna migrera måste koden uppdateras för att använda **Media Encoder Standard** medie processor i stället för amn, och en av motsvarande [system för inställningar](media-services-mes-presets-overview.md) som `H264 Multiple Bitrate 1080p`. 

## <a name="migrating-to-media-encoder-standard"></a>Migrera till Media Encoder Standard

Här är ett typiskt C# kod exempel som använder den äldre medie processorn. 

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("AME Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Adaptive Bitrate MP4 Set 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    " H264 Adaptive Bitrate MP4 Set 1080p", 
    TaskOptions.None); 
```

Här är den uppdaterade versionen som använder Media Encoder Standard.

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("Media Encoder Standard Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Multiple Bitrate 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Multiple Bitrate 1080p", 
    TaskOptions.None); 
```

### <a name="advanced-scenarios"></a>Avancerade scenarier 

Om du har skapat din egen kodnings för inställning för amn med dess schema, finns det ett [motsvarande schema för Media Encoder Standard](media-services-mes-schema.md). Om du har frågor om hur du mappar de äldre inställningarna till den nya kodaren kan du kontakta oss via mailto:amshelp@microsoft.com  
## <a name="known-differences"></a>Kända skillnader 

Media Encoder Standard är stabilare, tillförlitlig, har bättre prestanda och ger bättre kvalitet på utdata än den äldre AMNs koden. Dessutom: 

* Media Encoder Standard producerar utdatafiler med en annan namngivnings konvention än amn.
* Media Encoder Standard skapar artefakter, till exempel filer som innehåller metadata för [indatafilen](media-services-input-metadata-schema.md) och [metadata för utdatafilen](media-services-output-metadata-schema.md).

## <a name="next-steps"></a>Nästa steg

* [Äldre komponenter](legacy-components.md)
* [Sidan prissättning](https://azure.microsoft.com/pricing/details/media-services/#encoding)
