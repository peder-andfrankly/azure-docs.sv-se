---
title: Uppgifts för inställning för Azure Media Indexer
description: Det här avsnittet innehåller en översikt över uppgifts för inställningar för Azure Media Services Media Indexer.
services: media-services
documentationcenter: ''
author: Asolanki
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: 29753759af341f82429f12b6710ae9c32dcb4103
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896027"
---
# <a name="task-preset-for-azure-media-indexer"></a>Uppgifts för inställning för Azure Media Indexer 

Azure Media Indexer är en medie processor som du använder för att utföra följande uppgifter: gör mediefiler och innehåll sökbart, generera textning för dold textning och nyckelord, index till gångs filer som är en del av din till gång.

I det här avsnittet beskrivs den uppgifts för inställning som du behöver skicka till ditt indexerings jobb. Fullständiga exempel finns i [Indexera mediefiler med Azure Media Indexer](media-services-index-content.md).

## <a name="azure-media-indexer-configuration-xml"></a>Azure Media Indexer konfigurations-XML

I följande tabell beskrivs element och attribut för konfigurations-XML.

|Namn|Kräv|Beskrivning|
|---|---|---|
|Indata|sant|Till gångs fil (er) som du vill indexera.<br/>Azure Media Indexer stöder följande medie fil format: MP4, MOV, WMV, MP3, M4A, WMA, AAC, WAV. <br/><br/>Du kan ange fil namnen i **indata** -elementets **namn** eller **list** -attribut (se nedan). Om du inte anger vilken till gångs fil som ska indexeras, kommer den primära filen att plockas. Om ingen primär till gångs fil anges indexeras den första filen i indata till gången.<br/><br/>Om du uttryckligen vill ange namnet på till gångs filen gör du följande:<br/>```<input name="TestFile.wmv" />```<br/><br/>Du kan också indexera flera till gångs filer samtidigt (upp till 10 filer). Gör så här:<br/>– Skapa en textfil (manifest fil) och ge den fil tillägget LST.<br/>– Lägg till en lista över alla till gångs fil namn i din indata till gång till manifest filen.<br/>– Lägg till (överför) manifest filen till till gången.<br/>-Ange namnet på manifest filen i indatans List-attribut.<br/>```<input list="input.lst">```<br/><br/>**Obs:** Om du lägger till fler än 10 filer i manifest filen kommer indexerings jobbet att Miss 2006-felkoden.|
|metadata|false|Metadata för de angivna till gångs fil (er).<br/>```<metadata key="..." value="..." />```<br/><br/>Du kan ange värden för fördefinierade nycklar. <br/><br/>För närvarande stöds följande nycklar:<br/><br/>**rubrik** och **Beskrivning** – används för att uppdatera språk modellen för att förbättra tal igenkännings precisionen.<br/>```<metadata key="title" value="[Title of the media file]" /><metadata key="description" value="[Description of the media file]" />```<br/><br/>**användar namn** och **lösen ord** – används för autentisering vid hämtning av Internet-filer via http eller https.<br/>```<metadata key="username" value="[UserName]" /><metadata key="password" value="[Password]" />```<br/>Värdena för användar namn och lösen ord gäller för alla media-URL: er i manifestet för indatamängden.|
|funktioner<br/><br/>Tillagt i version 1,2. För närvarande är den enda funktionen som stöds tal igenkänning ("ASR").|false|Tal igenkännings funktionen har följande inställnings nycklar:<br/><br/>Språk:<br/>– Det naturliga språk som ska identifieras i multimedie filen.<br/>– Engelska, spanska<br/><br/>CaptionFormats:<br/>– en semikolonavgränsad lista med önskade format för text remsor (om det finns några)<br/>-ttml; webvtt<br/><br/><br/>GenerateKeywords:<br/>-En boolesk flagga som anger om en nyckelords-XML-fil krävs eller inte.<br/>Värdet !.|

## <a name="azure-media-indexer-configuration-xml-example"></a>Exempel på XML-Azure Media Indexer konfiguration

``` 
<?xml version="1.0" encoding="utf-8"?>  
<configuration version="2.0">  
  <input>  
    <metadata key="title" value="[Title of the media file]" />  
    <metadata key="description" value="[Description of the media file]" />  
  </input>  
  <settings>  
  </settings>  
  
  <features>  
    <feature name="ASR">    
      <settings>  
        <add key="Language" value="English"/>  
        <add key="GenerateKeywords" value ="true" />  
      </settings>  
    </feature>  
  </features>  
  
</configuration>  
```
  
## <a name="next-steps"></a>Nästa steg

Se [Indexera mediefiler med Azure Media Indexer](media-services-index-content.md).

