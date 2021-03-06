---
title: Datakällor som stöds – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker extraherar automatiskt svars par som lagras som webb sidor, PDF-filer eller MS Word doc-filer eller strukturerade QnA-innehållsfiler.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: diberry
ms.openlocfilehash: dc948629784254c9153f7f48ead7ff253e5f4453
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806396"
---
# <a name="data-sources-for-qna-maker-content"></a>Datakällor för QnA Maker-innehåll

QnA Maker extraherar automatiskt fråga – svar par från halvstrukturerade innehåll, till exempel vanliga frågor och svar, produkthandböcker, riktlinjer, stöd för dokument och principer som lagras som webbsidor, PDF-filer eller filer som Microsoft Word-dokument. Innehållet kan också läggas till i knowledge base från strukturerade innehållsfiler för frågor och svar. 

<a name="data-types"></a>

## <a name="file-and-url-data-types"></a>Fil-och URL-datatyper

Följande tabell sammanfattar typerna av innehåll och filformat som stöds av QnA Maker.

|Källtyp|Innehållstyp| Exempel|
|--|--|--|
|URL|Vanliga frågor och svar<br> (Platt, med avsnitt eller med hjälp av avsnitts start sida)<br>Support sidor <br> (Enkla sid artiklar, fel söknings artiklar osv.)|[Vanliga frågor och svar](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[Vanliga frågor och svar med länkar](https://www.microsoft.com/en-us/software-download/faq),<br> [Vanliga frågor och svar om ämnes Sidan](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Support artikel](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF / DOKUMENT|Vanliga frågor<br> Produkt handbok,<br> Broschyrer,<br> Grön<br> Reklamblads princip,<br> Support guide,<br> Strukturerad QnA,<br> och så vidare.|[Strukturerad QNA. doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> [Exempel på produkt manual. pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Exempel på semi-Structured. doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Exempel White Paper. pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br>[Exempel på multi-turn. docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)|
|\* Excel|Strukturerade QnA-fil<br> (inklusive RTF, HTML-stöd)|[Exempel på frågor och svar om FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|\* TXT/TSV|Strukturerade QnA-fil|[Exemplet chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

### <a name="import-and-export-knowledge-base"></a>Importera och exportera kunskaps bas

**TSV-och XLS-filer**, från exporterade kunskaps baser, kan bara användas genom att importera filerna från sidan **Inställningar** på QNA Maker portalen. De kan inte användas som data källor när du skapar kunskaps basen eller från **+ Lägg till fil** eller **+ Lägg till URL** -funktionen på sidan **Inställningar** . 

## <a name="data-source-locations"></a>Data-källplatser

Platser för data källor är **offentliga URL: er eller filer**som inte kräver autentisering. 

Om du behöver autentisering för din data källa kan du använda följande metoder för att hämta dessa data till QnA Maker:

* [Ladda ned filen manuellt](#download-file-from-authenticated-data-source-location) och importera till QNA Maker
* Importera fil för autentiserad [SharePoint-plats](#import-file-from-authenticated-sharepoint) 

### <a name="download-file-from-authenticated-data-source-location"></a>Hämta filen från den autentiserade data käll platsen

Om du har en autentiserad fil (inte på en autentiserad SharePoint-plats) eller URL är ett alternativt alternativ att ladda ned filen från den autentiserade platsen till din lokala dator. Lägg sedan till filen från den lokala datorn i kunskaps basen.

### <a name="import-file-from-authenticated-sharepoint"></a>Importera filen från autentiserad SharePoint 

[Platser för SharePoint-datakällor](../How-to/add-sharepoint-datasources.md) får tillhandahålla autentiserade **filer**. SharePoint-resurser måste vara filer, inte webb sidor. Om URL: en slutar med ett webb tillägg, till exempel **. ASPX**importeras inte till QNA Maker från SharePoint.


## <a name="faq-urls"></a>Vanliga frågor och svar-URL: er

QnA Maker kan stödja webbsidor för vanliga frågor och svar i 3 olika former: vanlig vanliga frågor och svar-sidor, vanliga frågor och svar sidor med länkar, vanliga frågor och svar sidor med en ämnen startsida.

### <a name="plain-faq-pages"></a>Vanlig vanliga frågor och svar-sidor

Det här är den vanligaste typen av FAQ-sida, som svar direkt efter frågor på samma sida. 

Nedan visas ett exempel på en vanlig FAQ-sida:

![Vanlig vanliga frågor och svar-sida-exempel för en kunskapsbas](../media/qnamaker-concepts-datasources/plain-faq.png) 

 
### <a name="faq-pages-with-links"></a>Vanliga frågor och svar sidor med länkar 

I den här typen av FAQ-sida kan frågor räknas samman tillsammans och har kopplats till svar som finns i olika avsnitt i samma sida eller i olika sidor.

Nedan visas ett exempel på en FAQ-sida med länkar i avsnitten som finns på samma sida:

 ![Avsnittet vanliga frågor och svar länk sidan exempel för en kunskapsbas](../media/qnamaker-concepts-datasources/sectionlink-faq.png) 


### <a name="faq-pages-with-a-topics-homepage"></a>Vanliga frågor och svar sidor med en ämnen startsida

Den här typen av vanliga frågor och svar har en startsida med hjälp av där varje avsnitt är en länk till dess relevanta kunskapsbaser på en annan sida. QnA Maker crawlar här, alla länkade sidor för att extrahera motsvarande frågor och svar.

Nedan visas ett exempel på en FAQ-sida där en ämnen startsida har länkar till avsnitt i vanliga frågor och svar på olika sidor. 

 ![Vanliga frågor och svar för djuplänk sidan exempel för en kunskapsbas](../media/qnamaker-concepts-datasources/topics-faq.png) 


### <a name="support-urls"></a>Support-URL: er

QnA Maker kan bearbeta delvis strukturerade support webb sidor, till exempel webb artiklar som beskriver hur du utför en specifik uppgift, hur du diagnostiserar och löser ett specifikt problem och vilka metoder som är bästa praxis för en specifik process. Extraktion fungerar bäst på innehåll som har en tydlig struktur med hierarkiska rubriker.

> [!NOTE]
> Extrahering av support artiklar är en ny funktion och är i tidiga steg. Det fungerar bäst för enkla sidor, som är väl strukturerade och inte innehåller komplexa sidhuvuden och sid fötter.

![QnA Maker stöder extrahering från halv strukturerade webb sidor där en tydlig struktur visas med hierarkiska rubriker](../media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)


## <a name="pdf-doc-files"></a>PDF / dokumentfiler

QnA Maker kan bearbeta delvis strukturerat innehåll i en PDF- eller DOC-fil och omvandla dem till kunskapsbaser. En bra fil som kan extraheras och är en där innehåll organiseras i någon strukturerade form och representeras i väldefinierade avsnitt. Avsnitten kan ytterligare delas i underavsnitt eller underavsnitt. Extrahering fungerar bäst på dokument som har en tydlig struktur med hierarkisk rubriker.

QnA Maker identifierar avsnitt och underavsnitt och-relationer i filen baserat på visuella LED trådar som tecken storlek, stil, numrering, färger osv. Halv strukturerade PDF-eller DOC-filer kan vara handböcker, vanliga frågor, rikt linjer, principer, broschyrer, reklamblad och många andra typer av filer. Nedan visas några exempel typer av dessa filer.

### <a name="product-manuals"></a>Produkthandböcker

En manuell är vanligtvis anvisningar som medföljer en produkt. Det hjälper användaren att konfigurera, använda, underhålla och felsöka produkten. När QnA Maker bearbetar en manuell, extraheras rubriker och underrubriker som frågor och efterföljande innehållet som svar. Se ett exempel [här](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

Nedan visas ett exempel på en manuell med en indexsida och hierarkisk innehåll

 ![Produkten manuell exempel för en kunskapsbas](../media/qnamaker-concepts-datasources/product-manual.png) 

> [!NOTE]
> Extrahering fungerar bäst på handböcker som har en tabell med innehåll och/eller en indexsida och en tydlig struktur med hierarkisk rubriker.

### <a name="brochures-guidelines-papers-and-other-files"></a>Broschyrer, riktlinjer, rapporter och andra filer

Många andra typer av dokument kan också bearbetas för att generera QA-par, förutsatt att de har en tydlig struktur och layout. Dessa omfattar: broschyrer, rikt linjer, rapporter, fakta blad, vetenskapliga handlingar, principer, böcker osv. Se ett exempel [här](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

Nedan visas ett exempel på ett halvstrukturerade dokument utan ett index:

 ![Azure Blob storage halvstrukturerade dokument](../media/qnamaker-concepts-datasources/semi-structured-doc.png) 

### <a name="structured-qna-document"></a>Strukturerat QnA-dokument

Formatet för strukturerade fråga – svar i dokumentfiler, är i form av alternerande frågor och svar per rad, en fråga per rad följt av dess svar i följande rad, enligt nedan: 

```text
Question1

Answer1

Question2

Answer2
```

Nedan visas ett exempel på ett strukturerade QnA word-dokument:

 ![Strukturerade frågor och svar om dokumentet exempel för en kunskapsbas](../media/qnamaker-concepts-datasources/structured-qna-doc.png) 

## <a name="structured-txt-tsv-and-xls-files"></a>Strukturerade *TXT*, *TSV* och *XLS* filer

Kunskapsbaser i form av strukturerade *.txt*, *.tsv* eller *.xls* filer kan också överföras till QnA Maker att skapa eller utöka en kunskapsbas.  Dessa kan antingen vara oformaterad text eller kan ha innehåll i RTF- eller HTML. 

| Fråga  | Svar  | Metadata (1 nyckel: 1 värde) |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

Alla övriga kolumner i källfilen ignoreras.

### <a name="example-of-structured-excel-file"></a>Exempel på strukturerad Excel-fil

Nedan visas ett exempel på en strukturerade QnA *.xls* filen med HTML-innehåll:

 ![Strukturerade frågor och svar om excel-exempel för en kunskapsbas](../media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Exempel på alternativa frågor för ett enskilt svar i Excel-fil

Nedan visas ett exempel på en strukturerad QnA *. xls* -fil med flera alternativa frågor för ett enda svar:

 ![Exempel på alternativa frågor för ett enskilt svar i Excel-fil](../media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

När filen har importer ATS visas fråga-och-svar-paret i kunskaps basen enligt nedan:

 ![Skärm bild av alternativa frågor för enskilda svar som importeras till kunskaps basen](../media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="structured-data-format-through-import"></a>Format för strukturerade data via import

Importera en kunskapsbas ersätter innehållet i den befintliga kunskapsbasen. Importera kräver en strukturerade TSV-fil som innehåller information om datakällan. Den här informationen hjälper QnA Maker att gruppera frågans svars par och attributerar dem till en viss data källa.

| Fråga  | Svar  | Källa| Metadata (1 nyckel: 1 värde) |          
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Redigering|    `Key:Value`       |

## <a name="editorially-add-to-knowledge-base"></a>Redigeringsmässigt lägga till i kunskapsbas

Om du inte har redan befintliga innehåll för att fylla i knowledge base, kan du lägga till kunskapsbaser redigeringsmässigt i QnA Maker Knowledge base. Lär dig hur du uppdaterar din kunskapsbas [här](../How-To/edit-knowledge-base.md).

<a href="#formatting-considerations"></a>

## <a name="formatting-considerations"></a>Formatering överväganden

När du har importerat en fil eller URL, QnA Maker konvertera och lagra innehållet i [markdown-formatet](https://en.wikipedia.org/wiki/Markdown). Konverterings processen lägger till nya rader i texten, t. ex. `\n\n`. En kunskap om markdown-formatet hjälper dig att förstå det konverterade innehållet och hantera ditt kunskaps bas innehåll. 

Om du lägger till eller redigerar innehållet direkt i din kunskaps bas använder du **markdown-formatering** för att skapa RTF-innehåll eller ändra markdown format innehåll som redan finns i svaret. QnA Maker stöder mycket av markdown-formatet för att få RTF-funktioner till ditt innehåll. Men klient programmet, till exempel en chatt-robot, kanske inte stöder samma uppsättning markdown-format. Det är viktigt att testa klient programmets visning av svar. 

Följande är en lista med markdown-format som du kan använda i QnA Maker: 

|Syfte|Format|Markdown-exempel|Rendering<br>som det visas i Chat-roboten|
|--|--|--|--|
En ny rad mellan 2 meningar.|`\n\n`|`How can I create a bot with \n\n QnA Maker?`|![formatera en ny rad mellan två meningar](../media/qnamaker-concepts-datasources/format-newline.png)|
|Sidhuvuden från H1 till H6, antalet `#` anger vilket sidhuvud. 1 `#` är H1.|`\n# text \n## text \n### text \n####text \n#####text` |`## Creating a bot \n ...text.... \n### Important news\n ...text... \n### Related Information\n ....text...`<br><br>`\n# my h1 \n## my h2\n### my h3 \n#### my h4 \n##### my h5`|![formatera med markdown-rubriker](../media/qnamaker-concepts-datasources/format-headers.png)<br>![formatera med markdown-huvuden H1 till H5](../media/qnamaker-concepts-datasources/format-h1-h5.png)|
|Kursiv |`*text*`|`How do I create a bot with *QnA Maker*?`|![formatera med kursiv stil](../media/qnamaker-concepts-datasources/format-italics.png)|
|Sträng (fet)|`**text**`|`How do I create a bot with **QnA Maker**?`|![formatera med stark markering för fetstil](../media/qnamaker-concepts-datasources/format-strong.png)|
|URL för länk|`[text](https://www.my.com)`|`How do I create a bot with [QnA Maker](https://www.qnamaker.ai)?`|![format för URL (hyperlänk)](../media/qnamaker-concepts-datasources/format-url.png)|
|\* URL för offentlig bild|`![text](https://www.my.com/image.png)`|`How can I create a bot with ![QnAMaker](https://review.docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/media/qnamaker-how-to-key-management/qnamaker-resource-list.png)`|![format för offentlig bild-URL ](../media/qnamaker-concepts-datasources/format-image-url.png)|
|Genomstruken|`~~text~~`|`some ~~questoins~~ questions need to be asked`|![format för genomstruken](../media/qnamaker-concepts-datasources/format-strikethrough.png)|
|Fet och kursiv stil|`***text***`|`How can I create a ***QnA Maker*** bot?`|![format för fet och kursiv stil](../media/qnamaker-concepts-datasources/format-bold-italics.png)|
|Fet URL för länk|`[**text**](https://www.my.com)`|`How do I create a bot with [**QnA Maker**](https://www.qnamaker.ai)?`|![format för fet URL](../media/qnamaker-concepts-datasources/format-bold-url.png)|
|URL för kursiv stil för länk|`[*text*](https://www.my.com)`|`How do I create a bot with [*QnA Maker*](https://www.qnamaker.ai)?`|![format för kursiv stil-URL](../media/qnamaker-concepts-datasources/format-url-italics.png)|
|Escape-markdown symboler|`\*text\*`|`How do I create a bot with \*QnA Maker\*?`|![format för kursiv stil-URL](../media/qnamaker-concepts-datasources/format-escape-markdown-symbols.png)|
|Ordnad lista|`\n 1. item1 \n 1. item2`|`This is an ordered list: \n 1. List item 1 \n 1. List item 2`<br>I föregående exempel används automatisk numrering som är inbyggd i markdown.<br>`This is an ordered list: \n 1. List item 1 \n 2. List item 2`<br>I föregående exempel används explicit numrering.|![format för ordnad lista](../media/qnamaker-concepts-datasources/format-ordered-list.png)|
|Osorterad lista|`\n * item1 \n * item2`<br>eller<br>`\n - item1 \n - item2`|`This is an ordered list: \n * List item 1 \n * List item 2`|![format för osorterad lista](../media/qnamaker-concepts-datasources/format-unordered-list.png)|
|Kapslade listor|`\n * Parent1 \n\t * Child1 \n\t * Child2 \n * Parent2`<br><br>`\n * Parent1 \n\t 1. Child1 \n\t * Child2 \n 1. Parent2`<br><br>Du kan kapsla in sorterade och osorterade listor tillsammans. Fliken `\t`anger den underordnade elementets indrags nivå.|`This is an unordered list: \n * List item 1 \n\t * Child1 \n\t * Child2 \n * List item 2`<br><br>`This is an ordered nested list: \n 1. Parent1 \n\t 1. Child1 \n\t 1. Child2 \n 1. Parent2`|![format för kapslad osorterad lista](../media/qnamaker-concepts-datasources/format-nested-unordered-list.png)<br>![format för kapslad ordnad lista](../media/qnamaker-concepts-datasources/format-nested-ordered-list.png)|

\* QnA Maker bearbetar inte bilden på något sätt. Det är klient programmets roll för att återge avbildningen. 

Om du vill lägga till innehåll med hjälp av uppdatera/Ersätt kunskaps-API: er och innehållet/filen innehåller HTML-taggar kan du bevara HTML-koden i filen genom att se till att öppning och stängning av taggarna konverteras i det kodade formatet.

| Bevara HTML  | Representation i API-begäran  | Representation i KB |
|-----------|---------|-------------------------|
| Ja | \&lt; br\&gt; | &lt;br&gt; |
| Ja | \&lt; H3\&gt; header\&lt;/H3\&gt; | &lt;H3&gt;-huvud&lt;/H3&gt; |

Dessutom konverteras CR LF (\r\n) till \n i KB. LF (\n) behålls i befintligt skick. Om du vill kringgå escape-sekvenser som a \t eller \n kan du använda omvänt snedstreck, till exempel: "\\\\r\\\\n" och "\\\\t"

## <a name="editing-your-knowledge-base-locally"></a>Redigera din kunskaps bas lokalt

När en kunskaps bas har skapats rekommenderar vi att du gör ändringar i kunskaps bas texten i [QNA Maker Portal](https://qnamaker.ai), i stället för att exportera och importera via lokala filer. Det kan dock finnas tillfällen då du behöver redigera en kunskaps bas lokalt. 

Exportera kunskaps basen från sidan **Inställningar** och redigera sedan kunskaps basen med Microsoft Excel. Om du väljer att använda ett annat program för att redigera den exporterade TSV-filen kan det leda till syntaxfel på grund av att det inte är fullständigt TSV-kompatibelt. Microsoft Excel-TSV-filer innehåller vanligt vis inte formateringsfel. 

När du är färdig med redigeringarna importerar du om TSV-filen från sidan **Inställningar** . Detta kommer att ersätta den aktuella kunskaps basen med den importerade kunskaps basen. 

## <a name="testing-your-markdown"></a>Testa din markdown

Använd **[CommonMark](https://commonmark.org/help/tutorial/index.html)** -självstudien för att validera din markdown. I självstudien finns **en funktion för** att snabbt kopiera och klistra in. 

## <a name="version-control-for-data-in-your-knowledge-base"></a>Versions kontroll för data i din kunskaps bas

Versions kontroll för data tillhandahålls via [import/export-funktionen](development-lifecycle-knowledge-base.md#version-control-of-a-knowledge-base) på sidan **Inställningar** . 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Ställ in QnA Maker-tjänsten](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>Se också 

[Översikt över QnA Maker](../Overview/overview.md)
