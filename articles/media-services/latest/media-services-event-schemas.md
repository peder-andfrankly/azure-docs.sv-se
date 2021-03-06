---
title: Azure Event Grid-scheman för Media Services-händelser
description: Beskriver de egenskaper som har angetts för Media Services-händelser med Azure Event Grid
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: reference
ms.date: 02/13/2019
ms.author: juliako
ms.openlocfilehash: 2d1e648a9ea33beb1347a4a635388ee04e46215b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449761"
---
# <a name="azure-event-grid-schemas-for-media-services-events"></a>Azure Event Grid-scheman för Media Services-händelser

Den här artikeln innehåller scheman och egenskaper för Media Services-händelser.

En lista över exempel på skript och självstudier finns i [medietjänster händelsekälla](../../event-grid/event-sources.md#azure-subscriptions).

## <a name="job-related-event-types"></a>Jobbet relaterade händelsetyper

Media Services genererar den **jobbet** relaterade händelsetyper som beskrivs nedan. Det finns två kategorier för de **jobbet** relaterade händelser: ”Övervakning jobbets status ändras” och ”övervakning jobbets utdata status ändras”. 

Du kan registrera dig för alla händelser genom att prenumerera på händelsen JobStateChange. Eller så kan du prenumerera på specifika händelser endast (till exempel sluttillstånd som JobErrored, JobFinished och JobCanceled). 

### <a name="monitoring-job-state-changes"></a>Övervakning av tillståndsändringar för jobbet

| eventType | Beskrivning |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| Hämta en händelse för alla ändringar för jobbets status. |
| Microsoft.Media.JobScheduled| Få en händelse när jobbet övergår till schemalagda tillstånd. |
| Microsoft.Media.JobProcessing| Få en händelse när jobbet övergår till status för bearbetning. |
| Microsoft.Media.JobCanceling| Få en händelse när jobbet övergår till avbryter tillstånd. |
| Microsoft.Media.JobFinished| Få en händelse när jobbet övergår till slutfört tillstånd. Det här är ett slutgiltigt tillstånd som innehåller utdata för jobbet.|
| Microsoft.Media.JobCanceled| Få en händelse när jobbet övergår till avbrutna tillstånd. Det här är ett slutgiltigt tillstånd som innehåller utdata för jobbet.|
| Microsoft.Media.JobErrored| Få en händelse när jobbet övergår till feltillstånd. Det här är ett slutgiltigt tillstånd som innehåller utdata för jobbet.|

Se [schemat exempel](#event-schema-examples) fram.

### <a name="monitoring-job-output-state-changes"></a>Tillståndsändringar för övervakning jobbutdata

| eventType | Beskrivning |
| ---------- | ----------- |
| Microsoft.Media.JobOutputStateChange| Hämta en händelse för alla jobbutdata tillstånd ändras. |
| Microsoft.Media.JobOutputScheduled| Få en händelse när jobbutdata övergår till schemalagda tillstånd. |
| Microsoft.Media.JobOutputProcessing| Få en händelse när jobbutdata övergår till status för bearbetning. |
| Microsoft.Media.JobOutputCanceling| Få en händelse när jobbutdata övergår till avbryter tillstånd.|
| Microsoft.Media.JobOutputFinished| Få en händelse när jobbet utdata övergår till slutfört tillstånd.|
| Microsoft.Media.JobOutputCanceled| Få en händelse när jobbet utdata övergår till har avbrutits tillstånd.|
| Microsoft.Media.JobOutputErrored| Få en händelse när jobbutdata övergår till feltillstånd.|

Se [schemat exempel](#event-schema-examples) fram.

### <a name="monitoring-job-output-progress"></a>Förlopp för övervakning jobbutdata

| eventType | Beskrivning |
| ---------- | ----------- |
| Microsoft.Media.JobOutputProgress| Den här händelsen visar jobbet bearbetningsförlopp från 0% till 100%. Tjänsten försöker skicka en händelse om det har förekommit 5% eller större ökning av värdet för pågående eller så har mer än 30 sekunder sedan den senaste händelsen (pulsslag). Värdet för pågående är inte säkert att starta på 0% eller för att nå 100% eller är det säkert att öka med ett jämnt över tid. Den här händelsen ska inte användas för att fastställa att bearbetningen har slutförts – i stället bör du använda tillståndsändringshändelser.|

Se [schemat exempel](#event-schema-examples) fram.

## <a name="live-event-types"></a>Typer av Live-händelse

Media Services genererar även den **Live** händelsetyper som beskrivs nedan. Det finns två kategorier för de **Live** händelser: stream på händelser och spåra på händelser. 

### <a name="stream-level-events"></a>Stream-på händelser

Stream-nivå händelser aktiveras per stream eller anslutning. Varje händelse har en `StreamId` parameter som identifierar anslutning eller stream. Varje stream eller anslutningen har en eller flera spårar av olika typer. En anslutning från en kodare kan till exempel ha ett ljudspår och fyra video spår. Händelsetyper stream är:

| eventType | Beskrivning |
| ---------- | ----------- |
| Microsoft.Media.LiveEventConnectionRejected | Kodarens anslutningsförsöket. |
| Microsoft.Media.LiveEventEncoderConnected | Kodaren upprättar anslutningen med live-händelse. |
| Microsoft.Media.LiveEventEncoderDisconnected | Kodaren kopplar från. |

Se [schemat exempel](#event-schema-examples) fram.

### <a name="track-level-events"></a>Spåra på händelser

Spåra på händelser aktiveras per spår. 

> [!NOTE]
> Alla servernivå spåra händelser aktiveras när en live-kodare är ansluten.

Spåra på servernivå händelsetyper är:

| eventType | Beskrivning |
| ---------- | ----------- |
| Microsoft.Media.LiveEventIncomingDataChunkDropped | Media-servern avslutar datagruppen eftersom den är för sent eller har ett överlappande tidsstämpel (tidsstämpel av nya data-segmentet är mindre än sluttiden tidigare datagruppen). |
| Microsoft.Media.LiveEventIncomingStreamReceived | Media-servern tar emot första data-segmentet för varje spår i stream eller anslutning. |
| Microsoft.Media.LiveEventIncomingStreamsOutOfSync | Media-server identifierar ljud och video strömmar är inte synkroniserade. Använda som en varning eftersom användarupplevelsen inte påverkas. |
| Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync | Media-server identifierar någon av de två videoflöden som kommer från externa kodaren är inte synkroniserade. Använda som en varning eftersom användarupplevelsen inte påverkas. |
| Microsoft.Media.LiveEventIngestHeartbeat | Publicerade var 20: e sekund för varje spår när direktsändningen körs. Tillhandahåller mata in hälsoöversikt.<br/><br/>När kodaren anslöts inledningsvis, fortsätter pulsslag händelsen att generera var 20 SEK om kodaren fortfarande är ansluten eller inte. |
| Microsoft.Media.LiveEventTrackDiscontinuityDetected | Media-server identifierar avbrott i den inkommande kursen. |

Se [schemat exempel](#event-schema-examples) fram.

## <a name="event-schema-examples"></a>Exempel för Event-schema

### <a name="jobstatechange"></a>JobStateChange

I följande exempel visas schemat för den **JobStateChange** händelse: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
    "eventType": "Microsoft.Media.JobStateChange",
    "eventTime": "2018-04-20T21:26:13.8978772",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Dataobjektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| previousState | string | Status för jobbet innan händelsen. |
| state | string | Det nya läget för jobbet som ett meddelande i den här händelsen. Till exempel ”schemalagd: Jobbet är redo att börja ”eller” slutfört: Jobbet har slutförts ”.|

Där jobbets status kan vara något av värdena: *I kö*, *schemalagda*, *bearbetning*, *klar*, *fel*, *har avbrutits*, *Avbryts*

> [!NOTE]
> *I kö* endast kommer att finnas i den **previousState** egenskapen men inte i den **tillstånd** egenskapen.

### <a name="jobscheduled-jobprocessing-jobcanceling"></a>JobScheduled, JobProcessing, JobCanceling

För varje-slutlig jobbet tillståndsändring (till exempel JobScheduled, JobProcessing, JobCanceling) ut exempel-schemat ungefär så här:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobProcessing",
  "eventTime": "2018-10-12T16:12:18.0839935",
  "id": "a0a6efc8-f647-4fc2-be73-861fa25ba2db",
  "data": {
    "previousState": "Scheduled",
    "state": "Processing",
    "correlationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="jobfinished-jobcanceled-joberrored"></a>JobFinished, JobCanceled, JobErrored

För varje sista tillståndsändring jobb (till exempel JobFinished, JobCanceled, JobErrored) ut exempel schemat ungefär så här:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobFinished",
  "eventTime": "2018-10-12T16:25:56.4115495",
  "id": "9e07e83a-dd6e-466b-a62f-27521b216f2a",
  "data": {
    "outputs": [
      {
        "@odata.type": "#Microsoft.Media.JobOutputAsset",
        "assetName": "output-7640689F",
        "error": null,
        "label": "VideoAnalyzerPreset_0",
        "progress": 100,
        "state": "Finished"
      }
    ],
    "previousState": "Processing",
    "state": "Finished",
    "correlationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Dataobjektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| outputs | Array | Hämtar utdata för jobbet.|

### <a name="joboutputstatechange"></a>JobOutputStateChange

I följande exempel visas schemat för den **JobOutputStateChange** händelse:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobOutputStateChange",
  "eventTime": "2018-10-12T16:25:56.0242854",
  "id": "dde85f46-b459-4775-b5c7-befe8e32cf90",
  "data": {
    "previousState": "Processing",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 100,
      "state": "Finished"
    },
    "jobCorrelationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="joboutputscheduled-joboutputprocessing-joboutputfinished-joboutputcanceling-joboutputcanceled-joboutputerrored"></a>JobOutputScheduled, JobOutputProcessing, JobOutputFinished, JobOutputCanceling, JobOutputCanceled, JobOutputErrored

För varje JobOutput i tillståndsändring ut exempel schemat ungefär så här:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobOutputProcessing",
  "eventTime": "2018-10-12T16:12:18.0061141",
  "id": "f1fd5338-1b6c-4e31-83c9-cd7c88d2aedb",
  "data": {
    "previousState": "Scheduled",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 0,
      "state": "Processing"
    },
    "jobCorrelationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```
### <a name="joboutputprogress"></a>JobOutputProgress

Exempel schemat ser ut ungefär så här:

 ```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/belohGroup/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/job-5AB6DE32",
  "eventType": "Microsoft.Media.JobOutputProgress",
  "eventTime": "2018-12-10T18:20:12.1514867",
  "id": "00000000-0000-0000-0000-000000000000",
  "data": {
    "jobCorrelationData": {
      "TestKey1": "TestValue1",
      "testKey2": "testValue2"
    },
    "label": "VideoAnalyzerPreset_0",
    "progress": 86
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

I följande exempel visas schemat för den **LiveEventConnectionRejected** händelse: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventConnectionRejected",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "b303db59-d5c1-47eb-927a-3650875fded1",
    "data": { 
      "streamId":"Mystream1",
      "ingestUrl": "http://abc.ingest.isml",
      "encoderIp": "118.238.251.xxx",
      "encoderPort": 52859,
      "resultCode": "MPE_INGEST_CODEC_NOT_SUPPORTED"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Dataobjektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| streamId | string | Identifierare för stream eller anslutning. Kodaren eller kunden är ansvarig för att lägga till detta ID i URL: en inmatning. |  
| ingestUrl | string | Mata in URL: en som tillhandahålls av live-händelse. |  
| encoderIp | string | IP-Adressen för kodaren. |
| encoderPort | string | Porten till kodaren från där den här strömmen är på gång. |
| resultCode | string | Orsaken till anslutningen avvisades. Resultatkoderna visas i följande tabell. |

Resultatkoderna är:

| Resultatkod | Beskrivning |
| ----------- | ----------- |
| MPE_RTMP_APPID_AUTH_FAILURE | Felaktig infognings-URL |
| MPE_INGEST_ENCODER_CONNECTION_DENIED | Kodaren IP finns inte rotcertifikatuppgifterna i IP-lista över konfigurerade tillåtna |
| MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED | Kodaren kunde inte skicka metadata om dataströmmen. |
| MPE_INGEST_CODEC_NOT_SUPPORTED | Codec som angetts stöds inte. |
| MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED | Tog emot ett fragment innan du tar emot och rubrik för dataströmmen. |
| MPE_INGEST_MEDIA_QUALITIES_EXCEEDED | Antalet egenskaper som angetts överskrider tillåtna maxgränsen. |
| MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED | Sammanställda bithastighet överskrider högsta tillåtna gränsen. |
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | Tidsstämpel för video- eller ljudinnehåll FLVTag kan inte användas med RTMP-kodare. |

### <a name="liveeventencoderconnected"></a>LiveEventEncoderConnected

I följande exempel visas schemat för den **LiveEventEncoderConnected** händelse: 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderConnected",
    "eventTime": "2018-08-07T23:08:09.1710643",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Dataobjektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| streamId | string | Identifierare för stream eller anslutning. Kodaren eller kunden ansvarar för att tillhandahålla detta ID i URL: en för inmatning. |
| ingestUrl | string | Mata in URL: en som tillhandahålls av live-händelse. |
| encoderIp | string | IP-Adressen för kodaren. |
| encoderPort | string | Porten till kodaren från där den här strömmen är på gång. |

### <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

I följande exempel visas schemat för den **LiveEventEncoderDisconnected** händelse: 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderDisconnected",
    "eventTime": "2018-08-07T23:08:09.1710872",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "resultCode": "S_OK"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Dataobjektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| streamId | string | Identifierare för stream eller anslutning. Kodaren eller kunden är ansvarig för att lägga till detta ID i URL: en inmatning. |  
| ingestUrl | string | Mata in URL: en som tillhandahålls av live-händelse. |  
| encoderIp | string | IP-Adressen för kodaren. |
| encoderPort | string | Porten till kodaren från där den här strömmen är på gång. |
| resultCode | string | Orsaken till kodaren kopplar från. Det kan vara korrekt koppla från eller från ett fel. Resultatkoderna visas i följande tabell. |

Resultatkoder för fel är:

| Resultatkod | Beskrivning |
| ----------- | ----------- |
| MPE_RTMP_SESSION_IDLE_TIMEOUT | RTMP sessionen avbröts efter att ha varit inaktiv i tillåtna tidsgränsen. |
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | Tidsstämpel för video- eller ljudinnehåll FLVTag kan inte användas med RTMP-kodare. |
| MPE_CAPACITY_LIMIT_REACHED | Kodare som skickar data för snabbt. |
| Okänd felkoder | Dessa felkoder kan variera mellan minnesfel till dubbla poster i hash-kartan. |

Resultatkoder för korrekt frånkoppling är:

| Resultatkod | Beskrivning |
| ----------- | ----------- |
| S_OK | Kodaren har frånkopplats. |
| MPE_CLIENT_TERMINATED_SESSION | Kodaren frånkopplad (RTMP). |
| MPE_CLIENT_DISCONNECTED | Kodaren frånkopplad (FMP4). |
| MPI_REST_API_CHANNEL_RESET | Kanalen Återställ kommando tas emot. |
| MPI_REST_API_CHANNEL_STOP | Kanalen stoppkommando togs emot. |
| MPI_REST_API_CHANNEL_STOP | Kanalen underhållsarbeten. |
| MPI_STREAM_HIT_EOF | EOF stream skickas av kodaren. |

### <a name="liveeventincomingdatachunkdropped"></a>LiveEventIncomingDataChunkDropped

I följande exempel visas schemat för den **LiveEventIncomingDataChunkDropped** händelse: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingDataChunkDropped",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "03da9c10-fde7-48e1-80d8-49936f2c3e7d",
    "data": { 
      "trackType": "Video",
      "trackName": "Video",
      "bitrate": 300000,
      "timestamp": 36656620000,
      "timescale": 10000000,
      "resultCode": "FragmentDrop_OverlapTimestamp"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Dataobjektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| trackType | string | Typ av spåret (ljud / Video). |
| trackName | string | Namnet på kursen. |
| bitrate | integer | Bithastighet av kursen. |
| timestamp | string | Tidsstämpel datagruppen släppts. |
| timescale | string | Tidsskalan för tidsstämpeln. |
| resultCode | string | Orsaken till i listrutan för data-segmentet. **FragmentDrop_OverlapTimestamp** eller **FragmentDrop_NonIncreasingTimestamp**. |

### <a name="liveeventincomingstreamreceived"></a>LiveEventIncomingStreamReceived

I följande exempel visas schemat för den **LiveEventIncomingStreamReceived** händelse: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamReceived",
    "eventTime": "2018-08-07T23:08:10.5069288Z",
    "id": "7f939a08-320c-47e7-8250-43dcfc04ab4d",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml/Streams(15864-stream0)15864-stream0",
      "trackType": "video",
      "trackName": "video",
      "bitrate": 2962000,
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "timestamp": "15336831655032322",
      "duration": "20000000",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Dataobjektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| trackType | string | Typ av spåret (ljud / Video). |
| trackName | string | Namnet på spåret (antingen tillhandahållna av kodaren eller, i händelse av RTMP genererar i *TrackType_Bitrate* format). |
| bitrate | integer | Bithastighet av kursen. |
| ingestUrl | string | Mata in URL: en som tillhandahålls av live-händelse. |
| encoderIp | string  | IP-Adressen för kodaren. |
| encoderPort | string | Porten till kodaren från där den här strömmen är på gång. |
| timestamp | string | Första tidsstämpel datagruppen som tagits emot. |
| timescale | string | Tidsskalan där timestamp representeras. |

### <a name="liveeventincomingstreamsoutofsync"></a>LiveEventIncomingStreamsOutOfSync

I följande exempel visas schemat för den **LiveEventIncomingStreamsOutOfSync** händelse: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamsOutOfSync",
    "eventTime": "2018-08-10T02:26:20.6269183Z",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "minLastTimestamp": "319996",
      "typeOfStreamWithMinLastTimestamp": "Audio",
      "maxLastTimestamp": "366000",
      "typeOfStreamWithMaxLastTimestamp": "Video",
      "timescaleOfMinLastTimestamp": "10000000", 
      "timescaleOfMaxLastTimestamp": "10000000"       
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Dataobjektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| minLastTimestamp | string | Minst senaste tidsstämplar bland alla spår (ljud eller video). |
| typeOfTrackWithMinLastTimestamp | string | Typ av spåra (ljud eller video) med minsta senaste tidsstämpel. |
| maxLastTimestamp | string | Högst alla tidsstämplar bland alla spår (ljud eller video). |
| typeOfTrackWithMaxLastTimestamp | string | Typ av spåra (ljud eller video) med maximalt senaste tidsstämpel. |
| timescaleOfMinLastTimestamp| string | Hämtar tidsskalan ”MinLastTimestamp” visas.|
| timescaleOfMaxLastTimestamp| string | Hämtar tidsskalan ”MaxLastTimestamp” visas.|

### <a name="liveeventincomingvideostreamsoutofsync"></a>LiveEventIncomingVideoStreamsOutOfSync

I följande exempel visas schemat för den **LiveEventIncomingVideoStreamsOutOfSync** händelse: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/LiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "6dd4d862-d442-40a0-b9f3-fc14bcf6d750",
    "data": {
      "firstTimestamp": "2162058216",
      "firstDuration": "2000",
      "secondTimestamp": "2162057216",
      "secondDuration": "2000",
      "timescale": "10000000"      
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Dataobjektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| firstTimestamp | string | Tidsstämpel som togs emot för en av spår/kvalitetsnivå av typen video. |
| firstDuration | string | Varaktighet datagruppen med första tidsstämpel. |
| secondTimestamp | string  | Tidsstämpel som togs emot för vissa andra spåra/kvalitetsnivå av typen video. |
| secondDuration | string | Varaktighet datagruppen med andra tidsstämpel. |
| timescale | string | Tidsskalan tidsstämplar och varaktighet.|

### <a name="liveeventingestheartbeat"></a>LiveEventIngestHeartbeat

I följande exempel visas schemat för den **LiveEventIngestHeartbeat** händelse: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIngestHeartbeat",
    "eventTime": "2018-08-07T23:17:57.4610506",
    "id": "7f450938-491f-41e1-b06f-c6cd3965d786",
    "data": {
      "trackType": "audio",
      "trackName": "audio",
      "bitrate": 160000,
      "incomingBitrate": 155903,
      "lastTimestamp": "15336837535253637",
      "timescale": "10000000",
      "overlapCount": 0,
      "discontinuityCount": 0,
      "nonincreasingCount": 0,
      "unexpectedBitrate": false,
      "state": "Running",
      "healthy": true
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Dataobjektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| trackType | string | Typ av spåret (ljud / Video). |
| trackName | string | Namnet på spåret (antingen tillhandahållna av kodaren eller, i händelse av RTMP genererar i *TrackType_Bitrate* format). |
| bitrate | integer | Bithastighet av kursen. |
| incomingBitrate | integer | Beräknad bithastighet baserat på datasegment som kommer från kodaren. |
| lastTimestamp | string | Senaste tidsstämpel togs emot för ett spår i senaste 20 sekunder. |
| timescale | string | Tidsskalan där tidsstämplar uttrycks. |
| overlapCount | integer | Antal datasegment hade överlappas tidsstämplar i senaste 20 sekunder. |
| DiscontinuityCount | integer | Antal avbrott som observerats under de senaste 20 sekunder. |
| NonIncreasingCount | integer | Antal datasegment med tidsstämplar som tidigare har tagits emot senaste 20 sekunder. |
| unexpectedBitrate | bool | Om förväntade och faktiska bithastighet skiljer sig åt i mer än tillåtna gränsen på senaste 20 sekunder. Det är SANT om och bara om, incomingBitrate > = 2 * bithastighet eller incomingBitrate < = bithastighet/2 eller IncomingBitrate = 0. |
| state | string | Status för live-händelse. |
| healthy | bool | Anger om mata in är felfri baserat på antalet och flaggor. Felfri är SANT om overlapCount = 0 & & discontinuityCount = 0 & & nonIncreasingCount = 0 & & unexpectedBitrate = false. |

### <a name="liveeventtrackdiscontinuitydetected"></a>LiveEventTrackDiscontinuityDetected

I följande exempel visas schemat för den **LiveEventTrackDiscontinuityDetected** händelse: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventTrackDiscontinuityDetected",
    "eventTime": "2018-08-07T23:18:06.1270405Z",
    "id": "5f4c510d-5be7-4bef-baf0-64b828be9c9b",
    "data": {
      "trackName": "video",
      "previousTimestamp": "15336837615032322",
      "trackType": "video",
      "bitrate": 2962000,
      "newTimestamp": "15336837619774273",
      "discontinuityGap": "575284",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Dataobjektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| trackType | string | Typ av spåret (ljud / Video). |
| trackName | string | Namnet på spåret (antingen tillhandahållna av kodaren eller, i händelse av RTMP genererar i *TrackType_Bitrate* format). |
| bitrate | integer | Bithastighet av kursen. |
| previousTimestamp | string | Tidsstämpel för det föregående fragmentet. |
| newTimestamp | string | Tidsstämpel för det aktuella fragmentet. |
| discontinuityGap | string | Mellanrummet mellan ovan två tidsstämplar. |
| timescale | string | Tidsskalan i vilka både tidsstämpel och avbrott mellanrum representeras. |

### <a name="common-event-properties"></a>Gemensamma händelseegenskaper

En händelse har följande översta data:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| subject | string | EventGrid-avsnittet. Den här egenskapen har resurs-ID för Media Services-kontot. |
| subject | string | Resursens sökväg för Media Services-kanalen i Media Services-kontot. Sammanfoga det avsnittet och ämne ger du resursen med ID för jobbet. |
| eventType | string | En av typerna som registrerade händelsen för den här händelsekällan. Till exempel ”Microsoft.Media.JobStateChange”. |
| eventTime | string | Den tid som händelsen genereras baserat på leverantörens UTC-tid. |
| id | string | Unik identifierare för händelsen. |
| data | object | Media Services händelsedata. |
| dataVersion | string | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| metadataVersion | string | Schemaversion för händelsemetadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |

## <a name="next-steps"></a>Nästa steg

[Registrera dig för jobbet tillståndsändringshändelser](job-state-events-cli-how-to.md)

## <a name="see-also"></a>Se också

- [EventGrid .NET SDK, som innehåller Media Service-händelser](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definitioner av Media Services-händelser](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)
