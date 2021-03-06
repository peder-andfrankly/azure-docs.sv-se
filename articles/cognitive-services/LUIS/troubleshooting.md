---
title: Vanliga frågor och svar – LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller svar på vanliga frågor och svar om Språkförståelse (LUIS).
author: diberry
manager: nitinme
ms.custom: seodec18
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: a2472064720af0a25568a2f173b971898b1f2e25
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123109"
---
# <a name="language-understanding-frequently-asked-questions-faq"></a>Vanliga frågor och svar om Language Understanding

Den här artikeln innehåller svar på vanliga frågor och svar om Språkförståelse (LUIS).

## <a name="whats-new"></a>Nyheter

[Läs mer](whats-new.md) om vad som är nytt i language Understanding (Luis).

<a name="luis-authoring"></a>

## <a name="authoring"></a>Redigering

### <a name="what-are-the-luis-best-practices"></a>Vilka är de bästa metoderna för LUIS?
Börja med den [redigering cykel](luis-concept-app-iteration.md), läs sedan de [bästa praxis](luis-concept-best-practices.md).

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>Vad är det bästa sättet att börja skapa min app på LUIS?

Det bästa sättet att bygga din app är via en [inkrementella processen](luis-concept-app-iteration.md).

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>Vad är en bra idé att modellera avsikter i min app? Bör jag skapa mer specifika eller mer allmänt avsikter?

Välj avsikter som inte är allmän så att de är överlappande, men inte så specifik som den gör det svårt för LUIS för att skilja mellan liknande avsikter. Skapa discriminative specifika avsikter är en av de bästa metoderna för LUIS modellering.

### <a name="is-it-important-to-train-the-none-intent"></a>Är det viktigt att träna avsikt ingen?

Ja, det är bra att träna din **ingen** avsikt med mer yttranden när du lägger till fler etiketter till andra avsikter. Ett bra förhållande är 1 eller 2 etiketter som lagts till i **ingen** för varje 10 etiketter som lagts till i en avsikt. Det här förhållandet ökar discriminative kraften hos LUIS.

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>Hur kan jag för att rätta stavfel i yttranden?

Se den [Bing stavningskontroll kontrollera API V7](luis-tutorial-bing-spellcheck.md) självstudien. LUIS tillämpar begränsningar har införts av Bing stavningskontroll kontrollera API V7.

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>Hur gör jag för att redigera min LUIS-app via programmering?
Redigera LUIS-app via programmering genom att använda den [redigering API](https://go.microsoft.com/fwlink/?linkid=2092087). Se [anropa LUIS redigering API](./get-started-get-model-rest-apis.md) och [skapa en LUIS-app via programmering med hjälp av Node.js](./luis-tutorial-node-import-utterances-csv.md) exempel på hur du anropar API: et redigering. Redigera-API kräver att du använder en [redigering nyckeln](luis-concept-keys.md#azure-resources-for-luis) i stället för en slutpunktsnyckel. Programmässig redigering kan upp till 1 000 000 anrop per månad och fem transaktioner per sekund. Mer information på de nycklar som du använder med LUIS finns i [hantera nycklar](./luis-concept-keys.md).

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Där matchar mönstret-funktion som tillhandahålls reguljärt uttryck?
Den tidigare **mönsterfunktion** för närvarande är inaktuell, ersatts av  **[mönster](luis-concept-patterns.md)** .

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Hur kan jag använda en entitet för att hämta rätt data?
Se [entiteter](luis-concept-entity-types.md) och [dataextrahering](luis-concept-data-extraction.md).

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>Varianter av en exempel-uttryck ska inkludera skiljetecken?
Lägg till olika värden som exempel yttranden med intentionen eller Lägg till mönstret för exempel-uttryck med den [syntaxen för att ignorera](luis-concept-patterns.md#pattern-syntax) skiljetecken.

### <a name="does-luis-currently-support-cortana"></a>Stöder LUIS för närvarande Cortana?

Cortana färdiga appar tagits bort 2017. De stöds inte längre.

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Hur jag för att överföra ägarskapet för en LUIS-app?
Exportera en LUIS-app för att överföra en LUIS-app till Azure-prenumeration, och importera det med ett nytt konto. Uppdatera LUIS-app-ID i klientprogram som anropar den. Den nya appen kan returnera något annorlunda LUIS poäng från den ursprungliga appen.

### <a name="a-prebuilt-entity-is-tagged-in-an-example-utterance-instead-of-my-custom-entity-how-do-i-fix-this"></a>En fördefinierad entitet är taggad i ett exempel-uttryck i stället för min anpassade entitet. Hur gör jag för att åtgärda detta? 

I LUIS-portalen kan du märka text för den exakta entitet som du är intresse rad av. Om LUIS-portalen inte visar rätt enhets förutsägelse kan du behöva lägga till fler yttranden och namnge entiteten i texten eller lägga till en beskrivning (till exempel en funktion). 

### <a name="i-tried-to-import-an-app-or-version-file-but-i-got-an-error-what-happened"></a>Jag försökte importera en app-eller versions fil, men jag fick ett fel meddelande, vad hände? 

Läs mer om [versions import fel](luis-how-to-manage-versions.md#import-errors).

<a name="luis-collaborating"></a>

## <a name="collaborating-and-contributing"></a>Samar beta och bidrag

### <a name="how-do-i-give-collaborators-access-to-luis-with-azure-active-directory-azure-ad-or-role-based-access-control-rbac"></a>Hur gör jag för att ge medarbetarna åtkomst till LUIS med Azure Active Directory (Azure AD) eller rollbaserad åtkomst kontroll (RBAC)?

Se [Azure Active Directory resurser](luis-how-to-collaborate.md#azure-active-directory-resources) och [Azure Active Directory klient användare](luis-how-to-collaborate.md#azure-active-directory-tenant-user) för att lära dig hur du ger samarbets åtkomst. 

<a name="luis-endpoint"></a>

## <a name="endpoint"></a>Slutpunkt

### <a name="i-received-an-http-403-error-status-code-how-do-i-fix-it"></a>Jag har fått en HTTP 403-fel status kod. Hur jag för att åtgärda det?

Du får status koderna 403 och 429 när du överskrider transaktionerna per sekund eller transaktioner per månad för din pris nivå. Öka pris nivån eller Använd Language Understanding [behållare](luis-container-howto.md).

När du använder alla de kostnads fria 1000-slutpunkts frågorna eller om du överskrider din pris nivås kvot för månads transaktioner får du en HTTP 403-fel status kod. 

För att åtgärda det här felet måste du antingen [ändra pris nivån](luis-how-to-azure-subscription.md#change-pricing-tier) till en högre nivå eller [skapa en ny resurs](get-started-portal-deploy-app.md#create-the-endpoint-resource) och [tilldela den till din app](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal).

Lösningar för det här felet är:

* I [Azure Portal](https://portal.azure.com)i language Understanding resurs, på **pris nivån resurs hantering->** , ändrar du pris nivån till en högre TPS-nivå. Du behöver inte göra något i Language Understanding portal om din resurs redan har tilldelats till din Language Understanding-app.
*  Om din användning överskrider den högsta pris nivån lägger du till fler Language Understanding resurser med en belastningsutjämnare framför dem. [Language Understanding containern](luis-container-howto.md) med Kubernetes eller Docker Compose kan hjälpa dig med detta.

### <a name="i-received-an-http-429-error-status-code-how-do-i-fix-it"></a>Jag har fått en HTTP 429-fel status kod. Hur jag för att åtgärda det?

Du får status koderna 403 och 429 när du överskrider transaktionerna per sekund eller transaktioner per månad för din pris nivå. Öka pris nivån eller Använd Language Understanding [behållare](luis-container-howto.md).

Den här status koden returneras när transaktionerna per sekund överskrider pris nivån.  

Lösningarna omfattar:

* Du kan [öka pris nivån](luis-how-to-azure-subscription.md#change-pricing-tier)om du inte är på den högsta nivån.
* Om din användning överskrider den högsta pris nivån lägger du till fler Language Understanding resurser med en belastningsutjämnare framför dem. [Language Understanding containern](luis-container-howto.md) med Kubernetes eller Docker Compose kan hjälpa dig med detta.
* Du kan Grinda klient program begär Anden med en [princip för återförsök](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines) som du implementerar själv när du får den här status koden. 

### <a name="my-endpoint-query-returned-unexpected-results-what-should-i-do"></a>Min endpoint-frågan returnerade ett oväntat resultat. Vad ska jag göra?

Oväntad fråga förutsagda resultaten som baseras på tillståndet för den publicerade modellen. Om du vill korrigera modellen kan du behöva ändra modellen, träna och publicera igen. 

Korrigera modellen börjar med [aktiv inlärning](luis-how-to-review-endpoint-utterances.md).

Du kan ta bort icke-deterministisk utbildning genom att uppdatera den [application version inställningar API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) för att kunna använda alla träningsdata.

Granska den [bästa praxis](luis-concept-best-practices.md) andra tips. 

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Varför LUIS lägga till utrymmen i frågan runt eller i mitten ord?
LUIS [tokenizes](luis-glossary.md#token) i uttryck baserat på den [kultur](luis-language-support.md#tokenization). Både det ursprungliga värdet och principfilerna värdet är tillgängliga för [dataextrahering](luis-concept-data-extraction.md#tokenized-entity-returned).

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Hur jag för att skapa och tilldela en LUIS slutpunktsnyckeln?
[Skapa slutpunktsnyckeln](luis-how-to-azure-subscription.md) i Azure för dina [service](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) nivå. [Tilldela nyckeln](luis-how-to-azure-subscription.md) på sidan **[Azure-resurser](luis-how-to-azure-subscription.md)** . Det finns inga motsvarande API: et för den här åtgärden. Du måste ändra HTTP-begäran till slutpunkten till [använder den nya slutpunktsnyckeln](luis-concept-keys.md).

### <a name="how-do-i-interpret-luis-scores"></a>Hur tolkar LUIS poäng?
Systemet bör använda högsta bedömnings avsikten oavsett dess värde. Till exempel en poäng under 0,5 (mindre än 50%) inte nödvändigtvis att LUIS har låg förtroende. Ger mer utbildning kan öka den [poäng](luis-concept-prediction-score.md) av den mest sannolika avsikten.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Varför ser jag min endpoint träffar i instrumentpanelen för min app?
Totala endpoint träffar i din app instrumentpanelen uppdateras regelbundet, men de mått som är associerade med din LUIS slutpunktsnyckeln i Azure-portalen uppdateras oftare.

Om du inte ser uppdaterade slut punkts träffar på instrument panelen loggar du in på Azure Portal och letar upp den resurs som är kopplad till din LUIS-slutpunkt nyckel och öppnar **mått** för att välja det **totala anrops** måttet. Om slutpunktsnyckeln används för mer än en LUIS-app, visar det sammanlagda antalet anrop från alla LUIS-appar som använder den mått i Azure-portalen.

### <a name="is-there-a-powershell-command-get-to-the-endpoint-quota"></a>Finns det ett PowerShell-kommando som får slut punkts kvoten?

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Du kan använda ett PowerShell-kommando för att Visa slut punkts kvoten:

```powershell
Get-AzCognitiveServicesAccountUsage -ResourceGroupName <your-resource-group> -Name <your-resource-name>
``` 

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>Min LUIS-app fungerar igår men idag jag får 403-fel. Appen ändras inte. Hur jag för att åtgärda det?
Följ de här [anvisningarna](#how-do-i-create-and-assign-a-luis-endpoint-key) för att skapa en Luis-slutpunkt-nyckel och tilldela den till appen. Sedan måste du ändra klient programmets HTTP-begäran till slut punkten för att kunna [använda den nya slut punkts nyckeln](luis-concept-keys.md). Om du har skapat en ny resurs i en annan region, ändrar du även HTTP-klientbegärans region.

### <a name="how-do-i-secure-my-luis-endpoint"></a>Hur skyddar jag min LUIS-slutpunkt
Se [skydda slutpunkten](luis-concept-keys.md#securing-the-endpoint).

## <a name="working-within-luis-limits"></a>Arbeta inom gränserna för LUIS

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>Vad är det maximala antalet intentioner och entiteter som har stöd för en LUIS-app?
Se den [gränser](luis-boundaries.md) referens.

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>Jag vill skapa en LUIS-app med mer än det maximala antalet avsikter. Vad ska jag göra?

Se [bästa praxis för avsikter](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents).

### <a name="i-want-to-build-an-app-in-luis-with-more-than-the-maximum-number-of-entities-what-should-i-do"></a>Jag vill skapa en app i LUIS med mer än det maximala antalet enheter. Vad ska jag göra?

Se [bästa praxis för entiteter](luis-concept-entity-types.md#if-you-need-more-than-the-maximum-number-of-entities)

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>Vilka gränser på antalet och storleken på frasen har visas?
För den maximala längden på en [frasen lista](./luis-concept-feature.md), finns i den [gränser](luis-boundaries.md) referens.

### <a name="what-are-the-limits-on-example-utterances"></a>Vad är begränsningar i exempel yttranden?
Se den [gränser](luis-boundaries.md) referens.

## <a name="testing-and-training"></a>Testning och utbildning

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>Jag ser några fel i batchen testning fönstret för några av modeller i min app. Hur kan jag för att lösa det här problemet?

Felen indikerar att det finns en diskrepans mellan etiketterna och förutsägelser från dina modeller. Gör något av följande åtgärder för att åtgärda problemet:
* För att förbättra diskriminering mellan avsikter THOMAS, lägger du till fler etiketter.
* För att LUIS Lär dig snabbare, lägger du till frasen-funktioner som introducerar domänspecifika ordförråd.

Se den [Batch testning](luis-tutorial-batch-testing.md) självstudien.

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>När en app har exporterat sedan återimporteras till en ny app (med en ny app-ID), är LUIS förutsägelse poängen olika. Varför inträffar det?

Se [förutsägelse skillnaderna mellan kopior av samma app](luis-concept-prediction-score.md#review-intents-with-similar-scores).

### <a name="some-utterances-go-to-the-wrong-intent-after-i-made-changes-to-my-app-the-issue-seems-to-disappear-at-random-how-do-i-fix-it"></a>Vissa yttranden går du till fel avsikten när jag har gjort ändringar i min app. Det verkar som om problemet försvinna slumpmässigt. Hur jag för att åtgärda det? 

Se [träna med alla data](luis-how-to-train.md#train-with-all-data).

## <a name="app-publishing"></a>Publicering av appar

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>Vad är klient-ID i fönstret ”Lägg till en nyckel till din app”?
I Azure representerar en klient klient eller organisation som är associerad med en tjänst. Hitta ditt klient-ID i Azure-portalen i den **katalog-ID** box genom att välja **Azure Active Directory** > **hantera**  >  **Egenskaper**.

![Klient-ID i Azure portal](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

<a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>
<a name="why-are-there-more-endpoint-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>


### <a name="why-are-there-more-endpoint-keys-assigned-to-my-app-than-i-assigned"></a>Varför finns det fler endpoint-nycklar som tilldelats min app än jag har tilldelat?
Varje LUIS-app har redigering/starter-nyckel i listan över slutpunkten bekvämlighets skull. Den här nyckeln kan bara ett par endpoint träffar så att du kan testa LUIS.  

Om din app fanns innan LUIS var allmänt tillgänglig (GA), tilldelas LUIS endpoint nycklar i din prenumeration automatiskt. Detta gjordes för att underlätta GA-migrering. Alla nya LUIS endpoint nycklar i Azure-portalen är _inte_ automatiskt tilldela det till LUIS.

## <a name="key-management"></a>Nyckelhantering

### <a name="how-do-i-know-what-key-i-need-where-i-get-it-and-what-i-do-with-it"></a>Hur gör jag för att vet du vilken nyckel jag behöver, var jag får den och vad jag gör med det? 

Se [Redigera och fråga efter slut punkts nycklar i Luis](luis-concept-keys.md) för att lära dig mer om skillnaderna mellan redigerings nyckeln och den förutsägelse körnings nyckeln. 

### <a name="i-got-an-error-about-being-out-of-quota-how-do-i-fix-it"></a>Jag har fått ett fel meddelande om att kvoten är felaktig. Hur jag för att åtgärda det? 

Se, korrigera HTTP-statuskod [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) och [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) för mer information.

### <a name="i-need-to-handle-more-endpoint-queries-how-do-i-do-that"></a>Jag behöver hantera fler slut punkts frågor. Hur gör jag för att? 

Se, korrigera HTTP-statuskod [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) och [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) för mer information.

### <a name="i-created-an-authoring-key-but-it-isnt-showing-in-the-luis-portal-what-happened"></a>Jag skapade en redigerings nyckel men den visas inte i LUIS-portalen. Vad hände?

Redigerings nycklar är tillgängliga i LUIS-portalen när [du har migrerat till redigerings nyckel upplevelsen](luis-migration-authoring.md).  

## <a name="app-management"></a>Apphantering

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Hur jag för att hämta en logg över användare yttranden?
Som standard loggar LUIS-appen yttranden från användare. Om du vill hämta en logg över yttranden som användarna skickar till LUIS-appen, går du till **Mina appar**, och välj appen. I verktygsfältet sammanhangsberoende väljer **exportera Endpoint loggar**. Loggen formateras som en fil med kommaavgränsade värden (CSV).

### <a name="how-can-i-disable-the-logging-of-utterances"></a>Hur kan jag inaktivera loggning av yttranden?
Du kan stänga av loggning av användaren yttranden genom att ange `log=false` i slutpunkts-URL som ditt klientprogram använder för att fråga LUIS. Men om du inaktiverar loggning inaktiverar LUIS-appens möjlighet att föreslå yttranden eller förbättra prestanda som baseras på [aktiv inlärning](luis-concept-review-endpoint-utterances.md#what-is-active-learning). Om du ställer in `log=false` på grund av data sekretessen, som du kan inte hämta en post med dessa användare yttranden från LUIS eller använder dessa uttryck för att förbättra din app.

Loggning är den enda lagringen av yttranden.

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>Varför vill inte alla mina endpoint yttranden loggas?
Om du använder din logg för förutsägande analys kan du inte skapa test yttranden i loggen.

## <a name="data-management"></a>Datahantering

### <a name="can-i-delete-data-from-luis"></a>Kan jag ta bort data från LUIS?

* Du kan alltid ta bort exempel yttranden som används för att träna LUIS. Om du tar bort en exempel-uttryck från LUIS-appen tas bort från LUIS-webbtjänsten och är inte tillgänglig för export.
* Du kan ta bort yttranden från listan över användare yttranden som LUIS föreslår i den **granska endpoint yttranden** sidan. Tar bort yttranden i den här listan förhindrar du att de ska visas som förslag, men ta bort inte dem från loggar.
* Om du tar bort ett konto raderas alla appar, och deras exempel yttranden och loggar. Dessa data är kvar på servrarna i 60 dagar innan de tas bort permanent.

### <a name="how-does-microsoft-manage-data-i-send-to-luis"></a>Hur hanterar Microsoft data som jag skickar till LUIS?

Den [Säkerhetscenter](https://www.microsoft.com/trustcenter) beskrivs våra åtaganden och dina alternativ för hantering av och åtkomst i Azure-tjänster.

## <a name="language-and-translation-support"></a>Stöd för språk och översättning

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>Jag har en app på ett språk och vill skapa en parallell app i ett annat språk. Vad är det enklaste sättet att göra detta?
1. Exportera din app.
2. Omvandla de märkta yttranden i JSON-filen för den exporterade appen till målspråket.
3. Du kan behöva ändra namnen på avsikter och entiteter eller lämna dem som de är.
4. Slutligen importera appen så att den har en LUIS-app på språket som mål.

## <a name="app-notification"></a>Appmeddelande

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>Varför visas ett e-postmeddelande om jag är nästan slut på kvot för?
Din nyckel för redigering/starter får bara 1000 endpoint frågar en månad. Skapa en LUIS-slutpunktsnyckeln (kostnadsfritt eller betalt) och nyckeln när du skapar frågor för slutpunkten. Om du gör endpoint frågor från en bot eller en annan klientprogram, måste du ändra det slutpunktsnyckeln LUIS.

## <a name="bots"></a>Robotar

### <a name="my-luis-bot-isnt-working-what-do-i-do"></a>Min LUIS-robot fungerar inte. Vad gör jag nu?

Det första problemet är att isolera om problemet är relaterat till LUIS eller sker utanför LUIS mellan. 

#### <a name="resolve-issue-in-luis"></a>Lös problem i LUIS
Skicka samma uttryck till LUIS från Luis- [slutpunkten](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint). Om du får ett fel meddelande kan du lösa problemet i LUIS tills felet inte längre returneras. Vanliga fel är:

* `Out of call volume quota. Quota will be replenished in <time>.` – det här problemet indikerar att du antingen behöver ändra från en redigerings nyckel till en [slut punkts nyckel](luis-how-to-azure-subscription.md) eller om du behöver ändra [tjänst nivåerna](luis-how-to-azure-subscription.md#change-pricing-tier). 

#### <a name="resolve-issue-in-azure-bot-service"></a>Lös problem i Azure Bot Service

Om du använder Azure Bot Service och problemet är att **testet i Web Chat** returnerar `Sorry, my bot code is having an issue`, kontrol lera loggarna:

1. I Azure Portal för din robot, i avsnittet **robot Management** , väljer du **build**.
1. Öppna kod redigeraren online. 
1. I det övre, blå navigerings fältet väljer du namnet på bot (det andra objektet till höger).
1. I den resulterande List rutan väljer du **Öppna kudu-konsolen**.
1. Välj **loggfiler**och välj sedan **program**. Granska alla loggfiler. Om du inte ser felet i programmappen granskar du alla loggfiler under **loggfiler**. 
1. Kom ihåg att återskapa projektet om du använder ett kompilerat språk som C#.

> [!Tip] 
> -Konsolen kan även installera paket. 

#### <a name="resolve-issue-while-debugging-on-local-machine-with-bot-framework"></a>Lös problemet vid fel sökning på den lokala datorn med bot Framework. 

Mer information om lokal fel sökning av en bot finns i [Felsöka en bot](https://docs.microsoft.com/azure/bot-service/bot-service-debug-bot?view=azure-bot-service-4.0).

## <a name="integrating-luis"></a>Integrera LUIS

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Där är min LUIS-app skapas under Azure web app bot-prenumeration?
Om du väljer en LUIS-mall och välj den **Välj** knappen i panelen fönstret vänster ändras för att inkludera malltypen och frågar i vilken region för att skapa LUIS-mall. Web app bot processen skapa inte dock en LUIS-prenumeration.

![Bot webbappregion för LUIS-mall](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Vilka LUIS-regioner har stöd för Bot Framework tal promotor?
[Tal promotor](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming) stöds bara för LUIS-appar i den centrala (USA)-instansen.

## <a name="api-programming-strategies"></a>API-programmerings strategier

### <a name="how-do-i-programmatically-get-the-luis-region-of-a-resource"></a>Hur gör jag för att program mässigt Hämta LUIS-regionen för en resurs? 

Använd LUIS-exemplet för att [hitta region](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/find-region) program mässigt med C# hjälp av eller Node. js. 

## <a name="luis-service"></a>LUIS-tjänsten

### <a name="is-language-understanding-luis-available-on-premises-or-in-private-cloud"></a>Språkförståelse (LUIS) tillgängliga lokalt eller i privata moln?

Ja, du kan använda LUIS [behållare](luis-container-howto.md) för dessa scenarier om du har den nödvändiga anslutningen att mäta användningen. 

## <a name="migrating-to-the-next-version"></a>Migrera till nästa version

### <a name="how-do-i-migrate-to-preview-v3-api"></a>Hur gör jag för att migrera till Preview v3 API? 

Se [API v2 till v3 migration guide för Luis-appar](luis-migration-api-v3.md)

## <a name="build-2019-conference-announcements"></a>Bygg 2019 konferens meddelanden

Följande funktioner släpptes på Build 2019-konferensen:

* [För hands version av v3 API migration guide](luis-migration-api-v3.md)
* [Förbättrad analys instrument panel](luis-how-to-use-dashboard.md)
* [Förbättrade fördefinierade domäner](luis-reference-prebuilt-domains.md) 
* [Dynamiska List enheter](luis-migration-api-v3.md#dynamic-lists-passed-in-at-prediction-time)
* [Externa entiteter](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

Videor:

* [Så här använder du Azure Conversation AI för att skala din verksamhet för nästa generation](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="next-steps"></a>Nästa steg

Mer information om LUIS finns i följande resurser:
* [Stack Overflow frågor som taggats med LUIS](https://stackoverflow.com/questions/tagged/luis)
* [MSDN Språkförståelse Intelligent Services (LUIS)-Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=LUIS)
