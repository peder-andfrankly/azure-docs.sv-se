---
title: Koda anpassade transformering med Media Services v3 CLI - Azure | Microsoft Docs
description: Det här avsnittet visar hur du använder Azure Media Services v3 att koda en anpassad transformering med hjälp av CLI.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/14/2019
ms.author: juliako
ms.openlocfilehash: 42b7c2d86525c428253137b424fe58bb61edba70
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65762011"
---
# <a name="how-to-encode-with-a-custom-transform---cli"></a>Koda med en anpassad transformering – CLI

När kodning med Azure Media Services, du kan komma igång snabbt med en av de rekommenderade inbyggda förinställningar, baserat på branschens bästa praxis, som visas i den [Streaming filer](stream-files-cli-quickstart.md#create-a-transform-for-adaptive-bitrate-encoding) Snabbstart. Du kan också skapa en anpassad förinställning om du vill rikta in dina specifika krav för scenario eller enhet.

## <a name="considerations"></a>Överväganden

När du skapar anpassade förinställningar, gäller följande:

* Alla värden för höjd och bredd på AVC innehåll måste vara en multipel av 4.
* I Azure Media Services v3 är alla kodning bithastighet i bitar per sekund. Detta skiljer sig från förinställningar med våra v2 API: er, som används kilobit per sekund som enheten. Till exempel om bithastigheten i v2 har angetts som 128 (kbit/s) skulle i v3 det ställas in till 128000 (bitar per sekund).

## <a name="prerequisites"></a>Nödvändiga komponenter 

[Skapa ett Media Services-konto](create-account-cli-how-to.md). <br/>Se till att komma ihåg resursgruppens namn och namnet på Media Services-konto. 

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-custom-preset"></a>Definiera en anpassad förinställning

I följande exempel definierar begärandetexten för en ny omvandling. Vi definierar en uppsättning utdata som vi vill genereras när den här transformeringen används. 

I det här exemplet vi först lägga till ett AacAudio lager för ljud encoding och två H264Video lager för den videokodning. I video lager tilldela vi etiketter, så att de kan användas i utdata-filnamn. Nu ska vill vi utdata till att även omfatta miniatyrer. I exemplet nedan anger vi bilder i PNG-format, genereras på 50% av lösningen på indatavideon och på tre tidsstämplar - {25%, 50%, 75} med längden på indatavideon. Slutligen kan vi ange formatet för utdatafilerna – en för video och ljud, och en annan för miniatyrbilderna. Eftersom vi har flera H264Layers, måste vi använda makron som producerar unika namn per lager. Vi kan använda en `{Label}` eller `{Bitrate}` makro exemplet visar tidigare.

Det dags att spara den här transformeringen i en fil. I det här exemplet vi ge filen namnet `customPreset.json`. 

```json
{
    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
    "codecs": [
        {
            "@odata.type": "#Microsoft.Media.AacAudio",
            "channels": 2,
            "samplingRate": 48000,
            "bitrate": 128000,
            "profile": "AacLc"
        },
        {
            "@odata.type": "#Microsoft.Media.H264Video",
            "keyFrameInterval": "PT2S",
            "stretchMode": "AutoSize",
            "sceneChangeDetection": false,
            "complexity": "Balanced",
            "layers": [
                {
                    "width": "1280",
                    "height": "720",
                    "label": "HD",
                    "bitrate": 3400000,
                    "maxBitrate": 3400000,
                    "bFrames": 3,
                    "slices": 0,
                    "adaptiveBFrame": true,
                    "profile": "Auto",
                    "level": "auto",
                    "bufferWindow": "PT5S",
                    "referenceFrames": 3,
                    "entropyMode": "Cabac"
                },
                {
                    "width": "640",
                    "height": "360",
                    "label": "SD",
                    "bitrate": 1000000,
                    "maxBitrate": 1000000,
                    "bFrames": 3,
                    "slices": 0,
                    "adaptiveBFrame": true,
                    "profile": "Auto",
                    "level": "auto",
                    "bufferWindow": "PT5S",
                    "referenceFrames": 3,
                    "entropyMode": "Cabac"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Media.PngImage",
            "stretchMode": "AutoSize",
            "start": "25%",
            "step": "25%",
            "range": "80%",
            "layers": [
                {
                    "width": "50%",
                    "height": "50%"
                }
            ]
        }
    ],
    "formats": [
        {
            "@odata.type": "#Microsoft.Media.Mp4Format",
            "filenamePattern": "Video-{Basename}-{Label}-{Bitrate}{Extension}",
            "outputFiles": []
        },
        {
            "@odata.type": "#Microsoft.Media.PngFormat",
            "filenamePattern": "Thumbnail-{Basename}-{Index}{Extension}"
        }
    ]
}

```

## <a name="create-a-new-transform"></a>Skapa en ny omvandling  

I det här exemplet skapar vi en **transformera** som baseras på anpassad förinställning som vi definierade tidigare. När du skapar en transformering, bör du först kontrollera om det redan finns. Om vi finns återanvända den. Följande `show` kommandot returnerar de `customTransformName` transformera om den finns:

```cli
az ams transform show -a amsaccount -g amsResourceGroup -n customTransformName
```

Följande CLI-kommando skapar transformeringen baserat på anpassad förinställning (definieras tidigare). 

```cli
az ams transform create -a amsaccount -g amsResourceGroup -n customTransformName --description "Basic Transform using a custom encoding preset" --preset customPreset.json
```

Du måste skicka ett jobb under den transformeringen för Media Services att tillämpa transformering på angivna video eller ljud. Ett komplett exempel som visar hur du skickar ett jobb under en transformering, se [snabbstarten: Stream videofiler - CLI](stream-files-cli-quickstart.md).

## <a name="see-also"></a>Se också

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
