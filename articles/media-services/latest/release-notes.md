---
title: Viktig information om Azure Media Services v3 | Microsoft Docs
description: Den här artikeln innehåller de senaste uppdateringarna för Azure Media Services v3 för att hålla dig uppdaterad med den senaste utvecklingen.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.topic: article
ms.date: 10/07/2019
ms.author: juliako
ms.openlocfilehash: 50c28f86a1ba36ac44a25e047800d14fe314f9bf
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420033"
---
# <a name="azure-media-services-v3-release-notes"></a>Viktig information om Azure Media Services v3

Om du vill hålla dig uppdaterad med den senaste utvecklingen, innehåller den här artikeln information om:

* Den senaste versionen
* Kända problem
* Felkorrigeringar
* Inaktuell funktion

## <a name="known-issues"></a>Kända problem

> [!NOTE]
> För närvarande kan du inte hantera v3-resurser med Azure-portalen. Använd [REST API](https://aka.ms/ams-v3-rest-sdk), CLI eller någon av de SDK: er som stöds.

Mer information finns i [vägledning för migrering för att flytta från Media Services v2 till v3](migrate-from-v2-to-v3.md#known-issues).

## <a name="september-2019"></a>September 2019

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="live-linear-encoding-of-live-events"></a>Direkt linjär kodning av Live-händelser

Media Services v3 presenterar för hands versionen av 24 timmar x 365 dagar av direkt linjär kodning av direktsända händelser.

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>Utfasning av medie processorer

Vi presenterar utfasning av *Azure Media Indexer* och *Azure Media Indexer 2 för hands version*. [Azure Media Indexer](../previous/media-services-index-content.md) medie processorn kommer att dras tillbaka den 1 oktober 2020. Processorerna för för [hands versionen av Azure Media Indexer 2](../previous/media-services-process-content-with-indexer2.md) kommer att dras tillbaka den 1 januari 2020. [Azure Media Services video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) ersätter dessa äldre medie processorer.

Mer information finns i [Migrera från Azure Media Indexer och Azure Media Indexer 2 till Azure Media Services video Indexer](../previous/migrate-indexer-v1-v2.md).

## <a name="august-2019"></a>Augusti 2019

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="south-africa-regional-pair-is-open-for-media-services"></a>Södra Afrika, regionala par är öppet för Media Services 

Media Services är nu tillgängligt i södra Afrika, norra och södra Afrika, västra regioner.

Mer information finns i [moln och regioner där Media Services v3 finns](azure-clouds-regions.md).

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>Utfasning av medie processorer

Vi presenterar utfasningen av medie processorerna för *Windows Azure Media Encoder* (WAME) och *Azure Media Encoder* (amn), som dras tillbaka den 31 mars 2020.

Mer information finns i [MIGRERA WAME till Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101334) och [migrera amn till Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101335).
 
## <a name="july-2019"></a>Juli 2019

### <a name="content-protection"></a>Innehållsskydd

När strömmat innehåll skyddas med begränsning av token måste slutanvändare hämta en token som skickas som en del av begäran om nyckel leverans. Funktionen för att *förhindra repetition av token* tillåter Media Services kunder att ange en gräns för hur många gånger samma token kan användas för att begära en nyckel eller en licens. Mer information finns i avsnittet om att [förhindra repetition av token](content-protection-overview.md#token-replay-prevention).

Den här funktionen är för närvarande tillgänglig i USA, Central och USA, västra centrala.

## <a name="june-2019"></a>Juni 2019

### <a name="video-subclipping"></a>Video under Urklipp

Nu kan du trimma eller under klipp en video när du kodar den med ett [jobb](https://docs.microsoft.com/rest/api/media/jobs). 

Den här funktionen fungerar med alla [transformeringar](https://docs.microsoft.com/rest/api/media/transforms) som har skapats med antingen [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) för inställningar eller [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) för inställningar. 

Se exempel:

* [Klipp en video med .NET](subclip-video-dotnet-howto.md)
* [Klipp en video med REST](subclip-video-rest-howto.md)

## <a name="may-2019"></a>Maj 2019

### <a name="azure-monitor-support-for-media-services-diagnostic-logs-and-metrics"></a>Azure Monitor stöd för Media Services diagnostikloggar och mått

Du kan nu använda Azure Monitor för att Visa telemetri-data emmited genom att Media Services.

* Använd Azure Monitor diagnostikloggar för att övervaka begär Anden som skickas av Media Services Key Delivery-slutpunkten. 
* Övervaka Mät värden som genereras av Media Services [slut punkter för direkt uppspelning](streaming-endpoint-concept.md).   

Mer information finns i [övervaka Media Services statistik och diagnostikloggar](media-services-metrics-diagnostic-logs.md).

### <a name="multi-audio-tracks-support-in-dynamic-packaging"></a>Stöd för flera ljud spår i dynamisk paketering 

När strömmande till gångar som har flera ljud spår med flera codecenheter och språk, stöder [dynamisk paketering](dynamic-packaging-overview.md) nu flera ljud spår för HLS-utdata (version 4 eller senare).

### <a name="korea-regional-pair-is-open-for-media-services"></a>Det regionala paret i Korea är öppet för Media Services 

Media Services är nu tillgängligt i regionerna Korea, centrala och Korea, södra. 

Mer information finns i [moln och regioner där Media Services v3 finns](azure-clouds-regions.md).

### <a name="performance-improvements"></a>Prestandaförbättringar

Nya uppdateringar som omfattar Media Services prestanda förbättringar.

* Den maximala fil storleken som stöds för bearbetning har uppdaterats. Se, [kvoter och begränsningar](limits-quotas-constraints.md).
* [Förbättringar i kodnings hastigheten](media-reserved-units-cli-how-to.md#choosing-between-different-reserved-unit-types).

## <a name="april-2019"></a>April 2019

### <a name="new-presets"></a>Nya för inställningar

* [FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset) har lagts till i de inbyggda för hands inställningarna i Analyzer.
* [ContentAwareEncodingExperimental](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset) har lagts till i de inbyggda kodare-för inställningarna. Mer information finns i [Content medveten encoding](cae-experimental.md). 

## <a name="march-2019"></a>Mars 2019

Dynamisk paketering stöder nu Dolby Atmos. Mer information finns i [codec för ljud som stöds av dynamisk paketering](dynamic-packaging-overview.md#audio-codecs).

Nu kan du ange en lista över till gångs-eller konto filter som ska gälla för din strömmande positionerare. Mer information finns i [associera filter med strömmande positionerare](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="february-2019"></a>Februari 2019

Media Services v3 stöds nu i Azure National-moln. Alla funktioner är inte tillgängliga i alla moln än. Mer information finns i [moln och regioner där Azure Media Services v3 finns](azure-clouds-regions.md).

[Microsoft. Media. JobOutputProgress](media-services-event-schemas.md#monitoring-job-output-progress) -händelsen har lagts till i Azure Event Grid scheman för Media Services.

## <a name="january-2019"></a>Januari 2019

### <a name="media-encoder-standard-and-mpi-files"></a>Media Encoder Standard-och MPI-filer 

När du kodar med Media Encoder Standard för att skapa MP4-filer skapas en ny. MPI-fil och läggs till i utmatnings till gången. Den här MPI-filen är avsedd att förbättra prestanda för dynamiska scenarier för [paketering](dynamic-packaging-overview.md) och strömning.

Du bör inte ändra eller ta bort MPI-filen, eller ta bort beroendet i din tjänst om det finns en sådan fil.

## <a name="december-2018"></a>December 2018

Uppdateringar från GA-versionen av v3-API: et är:
       
* **PresentationTimeRange** -egenskaperna är inte längre obligatoriska för **till gångs filter** och **konto filter**. 
* Alternativen $top och $skips fråga för **jobb** och **transformeringar** har tagits bort och $OrderBy lagts till. Som en del av att lägga till nya funktioner för beställning upptäcktes att $top-och $skip alternativen hade kunnat exponeras tidigare trots att de inte har implementerats.
* Utökningen av uppräkningen aktiverades igen. Den här funktionen har Aktiver ATS i för hands versionerna av SDK och har inaktiverats av misstag i GA-versionen.
* Två fördefinierade strömmande principer har bytt namn. **SecureStreaming** är nu **MultiDrmCencStreaming**. **SecureStreamingWithFairPlay** är nu **Predefined_MultiDrmStreaming**.

## <a name="november-2018"></a>November 2018

Modulen CLI 2,0 är nu tillgänglig för [Azure Media Services v3 ga](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest) – v 2.0.50.

### <a name="new-commands"></a>Nya kommandon

- [AZ AMS-konto](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest)
- [AZ AMS-konto – filter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest)
- [AZ AMS-till gång](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest)
- [AZ AMS Asset-filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)
- [AZ AMS-innehåll – nyckel-princip](https://docs.microsoft.com/cli/azure/ams/content-key-policy?view=azure-cli-latest)
- [AZ AMS-jobb](https://docs.microsoft.com/cli/azure/ams/job?view=azure-cli-latest)
- [AZ AMS live-event](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)
- [AZ AMS Live-output](https://docs.microsoft.com/cli/azure/ams/live-output?view=azure-cli-latest)
- [AZ AMS-direktuppspelning – slut punkt](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest)
- [AZ AMS streaming-Locator](https://docs.microsoft.com/cli/azure/ams/streaming-locator?view=azure-cli-latest)
- [AZ AMS Account MRU](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) – gör att du kan hantera enheter som är reserverade för media. Mer information finns i [skala medie reserverade enheter](media-reserved-units-cli-how-to.md).

### <a name="new-features-and-breaking-changes"></a>Nya funktioner och större ändringar

#### <a name="asset-commands"></a>Till gångs kommandon

- ```--storage-account```-och ```--container```-argument har lagts till.
- Standardvärden för förfallo tid (nu + 23h) och behörigheter (Läs) i ```az ams asset get-sas-url``` kommandot har lagts till.

#### <a name="job-commands"></a>Jobb kommandon

- ```--correlation-data```-och ```--label```-argument har lagts till
- ```--output-asset-names``` bytt namn till ```--output-assets```. Nu accepterar den en blankstegsavgränsad lista med till gångar i formatet "assetName = Label". En till gång utan etikett kan skickas så här: "assetName =".

#### <a name="streaming-locator-commands"></a>Kommandon för streaming Locator

- ```az ams streaming locator``` Base-kommandot ersattes med ```az ams streaming-locator```.
- ```--streaming-locator-id```-och ```--alternative-media-id support```-argument har lagts till.
- ```--content-keys argument``` argumentet har uppdaterats.
- ```--content-policy-name``` bytt namn till ```--content-key-policy-name```.

#### <a name="streaming-policy-commands"></a>Kommandon för direkt uppspelnings princip

- ```az ams streaming policy``` Base-kommandot ersattes med ```az ams streaming-policy```.
- Stöd för krypterings parametrar i ```az ams streaming-policy create``` lagts till.

#### <a name="transform-commands"></a>Omvandlings kommandon

- ```--preset-names``` argumentet ersattes med ```--preset```. Nu kan du bara ange 1 utdata/förval i taget (om du vill lägga till fler måste du köra ```az ams transform output add```). Du kan också ange anpassade StandardEncoderPreset genom att skicka sökvägen till din anpassade JSON.
- ```az ams transform output remove``` kan utföras genom att skicka det utgående index som ska tas bort.
- ```--relative-priority, --on-error, --audio-language and --insights-to-extract``` argument läggs till i ```az ams transform create``` och ```az ams transform output add``` kommandon.

## <a name="october-2018---ga"></a>Oktober 2018 – GA

I det här avsnittet beskrivs Azure Media Services (AMS) uppdateringar från oktober.

### <a name="rest-v3-ga-release"></a>REST v3 GA-version

[Rest v3 ga-versionen](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) innehåller fler API: er för manifest filter för Live, konto/till gångs nivå och DRM-stöd.

#### <a name="azure-resource-management"></a>Azure Resource Management 

Stöd för Azure Resource Management möjliggör Unified Management och Operations-API (nu allt på ett ställe).

Från och med den här versionen kan du använda Resource Manager-mallar för att skapa Live-händelser.

#### <a name="improvement-of-asset-operations"></a>Förbättringar av till gångs åtgärder 

Följande förbättringar introducerades:

- Mata in från HTTP (s) webb adresser eller Azure Blob Storage SAS-URL: er.
- Ange egna behållar namn för till gångar. 
- Enklare stöd för utdata för att skapa anpassade arbets flöden med Azure Functions.

#### <a name="new-transform-object"></a>Nytt Transform-objekt

Det nya **Transform** -objektet fören klar kodnings modellen. Det nya objektet gör det enkelt att skapa och dela koda Resource Manager-mallar och för inställningar. 

#### <a name="azure-active-directory-authentication-and-rbac"></a>Azure Active Directory autentisering och RBAC

Azure AD-autentisering och rollbaserad Access Control (RBAC) möjliggör säker transformering, LiveEvents, principer för innehålls nycklar eller till gångar efter roll eller användare i Azure AD.

#### <a name="client-sdks"></a>Klient-SDK: er  

Språk som stöds i Media Services v3: .NET Core, Java, Node. js, ruby, typescript, python, go.

#### <a name="live-encoding-updates"></a>Live encoding-uppdateringar

Följande Live encoding-uppdateringar introduceras:

- Nytt läge för låg latens för Live (10 sekunders slut punkt till slut punkt).
- Förbättrat stöd för RTMP (ökad stabilitet och fler stöd för käll kodare).
- RTMPs säker inmatning.

    När du skapar en Live-händelse får du nu 4 inmatnings-URL: er. De 4 inmatnings-URL: erna är nästan identiska, har samma strömnings-token (AppId), men endast port nummer delen är annorlunda. Två av URL: erna är primära och säkerhets kopiering för RTMP. 
- stöd för 24-timmarsformat. 
- Förbättrat stöd för AD-signalering i RTMP via SCTE35.

#### <a name="improved-event-grid-support"></a>Förbättrat stöd för Event Grid

Du kan se följande Event Grid support förbättringar:

- Azure Event Grid integration för enklare utveckling med Logic Apps och Azure Functions. 
- Prenumerera på händelser om kodning, Live Channels med mera.

### <a name="cmaf-support"></a>CMAF-stöd

Krypterings stöd för CMAF och CBCS för Apple HLS (iOS 11 +) och MPEG-STRECKs pelare som stöder CMAF.

### <a name="video-indexer"></a>Videoindexering

Video Indexer GA-versionen presenterades i augusti. För ny information om funktioner som stöds för närvarande, se [Vad är video Indexer](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json). 

### <a name="plans-for-changes"></a>Planer för ändringar

#### <a name="azure-cli-20"></a>Azure CLI 2.0
 
Azure CLI 2,0-modulen som innehåller åtgärder för alla funktioner (inklusive Live, innehålls nyckel principer, konto-och till gångs filter, strömmande principer) kommer snart. 

### <a name="known-issues"></a>Kända problem

Endast kunder som använde för hands versionen av API för till gångs-eller AccountFilters påverkas av följande problem.

Om du har skapat till gångar eller konto filter mellan 09/28 och 10/12 med Media Services v3 CLI eller API: er, måste du ta bort alla till gångar och AccountFilters och återskapa dem på grund av en versions konflikt. 

## <a name="may-2018---preview"></a>Maj 2018 – för hands version

### <a name="net-sdk"></a>.NET SDK

Följande funktioner finns i .NET SDK:

* **Transformeringar** och **jobb** för att koda eller analysera medie innehåll. Exempel finns i [strömma filer](stream-files-tutorial-with-api.md) och [analysera](analyze-videos-tutorial-with-api.md).
* **Strömmande positionerare** för att publicera och strömma innehåll till slut användar enheter
* **Strömmande principer** och **innehålls nyckel principer** för att konfigurera nyckel leverans och innehålls skydd (DRM) när innehåll levereras.
* **Live-händelser** och **Live-utdata** för att konfigurera inmatning och arkivering av Live streaming-innehåll.
* **Till gångar** för att lagra och publicera medie innehåll i Azure Storage. 
* **Slut punkter för direkt uppspelning** för att konfigurera och skala dynamisk paketering, kryptering och strömning för både Live-och medie innehåll på begäran.

### <a name="known-issues"></a>Kända problem

* När du skickar ett jobb kan du ange att du vill mata in din käll video med hjälp av HTTPS-URL: er, SAS-URL: er eller sökvägar till filer som finns i Azure Blob Storage. AMS v3 stöder för närvarande inte segmentvis överföringskodning över HTTPS-URL:er.

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, hämta uppdateringar

Kolla in [Azure Media Services community](media-services-community.md) -artikeln för att se olika sätt att ställa frågor, lämna feedback och få uppdateringar om Media Services.

## <a name="next-steps"></a>Nästa steg

- [Översikt](media-services-overview.md)
- [Viktig information om Media Services v2](../previous/media-services-release-notes.md)
