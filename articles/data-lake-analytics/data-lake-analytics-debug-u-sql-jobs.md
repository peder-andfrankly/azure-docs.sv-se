---
title: Fel C# söknings kod för Azure Data Lake U-SQL-jobb
description: I den här artikeln beskrivs hur du felsöker ett U-SQL-felaktigt hörn med Azure Data Lake verktyg för Visual Studio.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.topic: conceptual
ms.date: 11/30/2017
ms.openlocfilehash: 72239fc1679d2ebbfd9c9b5be6b79b58efb760cb
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71315803"
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>Felsök användardefinierad C# kod för misslyckade U-SQL-jobb

U-SQL tillhandahåller en utöknings C#modell som använder. I U-SQL-skript är det enkelt att anropa C# funktioner och utföra analys funktioner som SQL-liknande deklarativ språk inte stöder. Mer information om hur du utökningen av U-SQL finns i [programmerings guide för u-SQL](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#use-user-defined-functions-udf). 

I praktiken kan all kod behöva fel sökning, men det är svårt att felsöka ett distribuerat jobb med anpassad kod i molnet med begränsade loggfiler. [Azure Data Lake Tools för Visual Studio](https://aka.ms/adltoolsvs) innehåller en funktion som kallas **fel söknings fel hörn**, som hjälper dig att enkelt Felsöka fel som uppstår i din anpassade kod. När U-SQL-jobbet Miss lyckas, behåller tjänsten fel tillstånd och verktyget hjälper dig att ladda ned moln fel miljön till den lokala datorn för fel sökning. Den lokala hämtningen fångar hela moln miljön, inklusive indata och användar kod.

Följande video visar felaktig fel sökning av hörn i Azure Data Lake verktyg för Visual Studio.

> [!VIDEO https://www.youtube.com/embed/3enkNvprfm4]
>

> [!IMPORTANT]
> Visual Studio kräver följande två uppdateringar för att använda den här funktionen: [Microsoft Visual C++ 2015 Redistributable Update 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) och [Universal C runtime för Windows](https://www.microsoft.com/download/details.aspx?id=50410).
>

## <a name="download-failed-vertex-to-local-machine"></a>Ladda ned misslyckad hörn till lokal dator

När du öppnar ett misslyckat jobb i Azure Data Lake verktyg för Visual Studio visas ett gult aviserings fält med detaljerade fel meddelanden på fliken fel.

1. Klicka på **Ladda ned** för att ladda ned alla nödvändiga resurser och indata strömmar. Om hämtningen inte är klar klickar du på **försök igen**.

2. Klicka på **Öppna** när hämtningen är klar för att generera en lokal fel söknings miljö. En ny fel söknings lösning öppnas, och om du har en befintlig lösning som öppnats i Visual Studio, måste du spara och stänga den innan du felsöker.

![Azure Data Lake Analytics U-SQL fel sökning Visual Studio Hämta hörn](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

## <a name="configure-the-debugging-environment"></a>Konfigurera fel söknings miljön

> [!NOTE]
> Innan du felsöker måste du kontrol lera **vanliga språk körnings undantag** i fönstret undantags inställningar (**CTRL + ALT + E**).

![Inställningar för Azure Data Lake Analytics U-SQL debug Visual Studio](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

I den nya startade Visual Studio-instansen kan du kanske inte hitta den användardefinierade C# käll koden:

1. [Jag kan hitta min käll kod i lösningen](#source-code-is-included-in-debugging-solution)

2. [Jag kan inte hitta min käll kod i lösningen](#source-code-is-not-included-in-debugging-solution)

### <a name="source-code-is-included-in-debugging-solution"></a>Käll koden ingår i fel söknings lösningen

Käll koden samlas in i C# två fall:

1. Användar koden definieras i filen bakomliggande kod (kallas `Script.usql.cs` vanligt vis i ett U-SQL-projekt).

2. Användar koden definieras i C# klass biblioteks projekt för U-SQL-program och registreras som sammansättning med **felsöknings information**.

Om käll koden importeras till lösningen kan du använda Visual Studios fel söknings verktyg (se, variabler osv.) för att felsöka problemet:

1. Starta felsökningen genom att trycka på **F5**. Koden körs tills den stoppas av ett undantag.

2. Öppna käll kods filen och ange Bryt punkter och tryck sedan på **F5** för att felsöka koden steg för steg.

    ![Azure Data Lake Analytics U-SQL debug-undantag](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

### <a name="source-code-is-not-included-in-debugging-solution"></a>Käll koden ingår inte i fel söknings lösningen

Om användar koden inte ingår i filen med bakomliggande kod eller om du inte registrerade sammansättningen med **fel söknings information**, inkluderas inte käll koden automatiskt i fel söknings lösningen. I det här fallet behöver du ytterligare steg för att lägga till din Källkod:

1. Högerklicka på **lösning "VertexDebug" > Lägg till > befintligt projekt...** för att hitta sammansättnings käll koden och lägga till projektet i fel söknings lösningen.

    ![Azure Data Lake Analytics U-SQL-felsökning Lägg till projekt](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. Hämta sökvägen till projektmappen för **FailedVertexDebugHost** -projektet. 

3. Högerklicka på **det tillagda käll kods projektet för sammansättningen > egenskaper**, Välj fliken **Bygg** till vänster och klistra in den kopierade sökvägen som slutar med \Bin\Debug som **utdata > sökväg för utdata**. Den slutgiltiga sökvägen till utdata fungerar `<DataLakeTemp path>\fd91dd21-776e-4729-a78b-81ad85a4fba6\loiu0t1y.mfo\FailedVertexDebug\FailedVertexDebugHost\bin\Debug\`som.

    ![Azure Data Lake Analytics U-SQL-felsökning ange PDB-sökväg](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

Börja felsöka med **F5** och Bryt punkter efter dessa inställningar. Du kan också använda Visual Studios fel söknings verktyg (se, variabler osv.) för att felsöka problemet.

> [!NOTE]
> Återskapa paketets käll kods projekt varje tillfälle när du har ändrat koden för att generera uppdaterade. PDB-filer.

## <a name="resubmit-the-job"></a>Skicka jobbet igen

Efter fel sökningen visas följande meddelande om projektet har slutförts:

    The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).

![Azure Data Lake Analytics U-SQL-felsökning lyckades](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

Så här skickar du om det misslyckade jobbet:

1. För jobb med koder för bakomliggande lösningar kopierar du C# koden till käll filen bakomliggande kod (vanligt vis `Script.usql.cs`).

2. För jobb med sammansättningar högerklickar du på käll kods projektet för sammansättningen i fel söknings lösning och registrerar de uppdaterade. dll-sammansättningarna i Azure Data Lake katalogen.

3. Skicka U-SQL-jobbet igen.

## <a name="next-steps"></a>Nästa steg

- [Programmerings guide för U-SQL](data-lake-analytics-u-sql-programmability-guide.md)
- [Utveckla användardefinierade U-SQL-operatörer för Azure Data Lake Analytics jobb](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [Testa och felsöka U-SQL-jobb med lokal körning och Azure Data Lake U-SQL SDK](data-lake-analytics-data-lake-tools-local-run.md)
- [Så här felsöker du ett onormalt återkommande jobb](data-lake-analytics-data-lake-tools-debug-recurring-job.md)
