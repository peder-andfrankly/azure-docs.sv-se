---
title: Design mönster för utlämnande
titleSuffix: Azure Cognitive Services
description: Design mönster och metod tips för avslöjande.
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: angle
ms.openlocfilehash: 3e7d8ee2b156a30b11cda79798a8af8a8ecf4f64
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74776630"
---
# <a name="disclosure-design-patterns"></a>Utlämnande av designmönster
Nu när du&#39;har bestämt rätt [nivå för utlämnande](concepts-disclosure-guidelines.md#disclosure-assessment) av den syntetiska röst upplevelsen&#39;, är det en bättre tid att utforska potentiella design mönster.
## <a name="overview"></a>Översikt
Det finns ett spektrum av design mönster för inlämning som du kan använda för din syntetiska röst upplevelse. Om resultatet av din utlämnande av utvärderingen var "högt utlämnat", rekommenderar vi [**uttrycklig**](#explicit-disclosure)information, vilket innebär att den syntetiska röstens ursprung kommunicerar. [**Implicit avslöjande**](#implicit-disclosure) innehåller tips och interaktions mönster som förmånen röst upplevelser oavsett om nivåerna är höga eller låga.
![spektrum av utlämnade mönster](media/responsible-ai/disclosure-patterns/affordances.png)






| Explicita utlämnade mönster                                                                                                                                                                                    | Mönster för implicit avslöjande                                                                 |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|
|[Transparent introduktion](#transparent-introduction)<br> [Muntlig introduktion](#verbal-transparent-introduction)<br>  [Explicit Bylinen](#explicit-byline)<br>  [Anpassning och kalibrering](#customization-and-calibration)<br> [Överordnad sekretess](#parental-disclosure)<br> [Tillhandahålla möjligheter att lära sig mer om hur rösten har gjorts](#providing-opportunities-to-learn-more-about-how-the-voice-was-made) | [Utlämnande av funktioner](#capability-disclosure)<br>[Implicita ikoner och feedback](#implicit-cues--feedback)<br> [Konversations genomskinlighet](#conversational-transparency) |



Använd följande diagram för att referera direkt till mönstren som gäller för den syntetiska rösten. Några av de andra villkoren i det här diagrammet kan också gälla för ditt scenario:<br/>



| Om din syntetiska röst upplevelse... | Rekommendationer | Designmönster |
| --- | --- | --- |
| Kräver högt utlämnande  | Använd minst ett explicit mönster och en implicit genom gång för att hjälpa användarna att skapa associationer. |[Uttryckligt röjande](#explicit-disclosure)<br>[Implicit avslöjande](#implicit-disclosure)  |
| Kräver låg upplysning | Utlämnande kan vara minimalt eller onödigt, men kan dra nytta av vissa implicita mönster. | [Utlämnande av funktioner](#capability-disclosure)<br>[Konversations genomskinlighet](#conversational-transparency)  |
| Har en hög nivå av engagemang | Utveckla för lång sikt och erbjud flera start punkter för att avslöja information längs användar resan. Vi rekommenderar starkt att du har en onboarding-upplevelse. | [Transparent introduktion](#transparent-introduction)<br>[Anpassning och kalibrering](#customization-and-calibration)<br>[Utlämnande av funktioner](#capability-disclosure) |
| Innehåller underordnade som primär riktad mål grupp | Fokusera på föräldrar som den primära mål gruppen och se till att de effektivt kan kommunicera till barn.  | [Överordnad sekretess](#parental-disclosure)<br>[Muntlig introduktion](#verbal-transparent-introduction)<br> [Implicit avslöjande](#implicit-disclosure)<br> [Konversations genomskinlighet](#conversational-transparency)  |
| Inkluderar synskadade användare eller personer med nedsatt syn som primär riktad mål grupp  | Inkludera alla användare och se till att alla former av visuell information har tillhör ande alternativ text eller ljud effekter. Följ hjälpmedels standarder för kontrast förhållande och visnings storlek. Använd gransknings-och undervisnings tips för att kommunicera.  | [Muntlig introduktion](#verbal-transparent-introduction) <br>[Gransknings ikoner](#implicit-cues--feedback)<br>[Haptic-stack](#implicit-cues--feedback)<br>[Konversations genomskinlighet](#conversational-transparency)<br>[Hjälpmedels standarder](https://www.microsoft.com/accessibility) |
| Är en skärm som är mindre, enhets lös eller använder röst som primärt eller enda interaktions läge | Använd gransknings-och undervisnings tips för att kommunicera. | [Muntlig introduktion](#verbal-transparent-introduction) <br> [Gransknings ikoner](#implicit-cues--feedback)  |
| Kan innehålla flera användare/lyssnare (t. ex. en personlig assistent i flera hushåll)  | Vara mindful av olika användar kontexter och nivåer av förståelse och erbjuder flera möjligheter till utlämnande av användar resan.  | [Transparent introduktion (returnera användare)](#transparent-introduction)<br> [Tillhandahålla möjligheter att lära sig mer om hur rösten har gjorts](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)<br> [Konversations genomskinlighet](#conversational-transparency)  |



## <a name="explicit-disclosure"></a>Uttryckligt röjande
Om den syntetiska rösten kräver hög utlämnande, är det bäst att använda minst ett av följande uttryckliga mönster för att tydligt ange den syntetiska typen.
### <a name="transparent-introduction"></a>Transparent introduktion

Innan röst upplevelsen börjar ska du introducera den digitala assistenten genom att vara helt transparent om ursprunget för rösten och dess funktioner. Det bästa tillfället att använda det här mönstret är när du registrerar en ny användare eller när du introducerar nya funktioner i en returnerad användare. Implementering av implicita stackar under en introduktion hjälper användare att bilda en psykiska modell om den syntetiska typen av digital agent.

#### <a name="first-time-user-experience"></a>Användar upplevelse i första gången

![transparent introduktion vid första körnings upplevelsen](media/responsible-ai/disclosure-patterns/transparent-intro-first.png) <br>
*Den syntetiska rösten introduceras när en ny användare registreras.*

Rekommendationer
- Beskriv att rösten är artificiell (t. ex. &quot;Digital&quot;)
- Beskriv vad agenten kan utföra
- Ange ett explicit tillstånd&#39;för Voice s-ursprung
- Erbjud en start punkt för att lära dig mer om den syntetiska rösten

#### <a name="returning-user-experience"></a>Returnerar användar upplevelsen

Om en användare hoppar över onboarding-upplevelsen fortsätter du att erbjuda start punkter till den transparenta introduktionen tills användaren utlöser rösten för första gången.
<br/>

![transparent introduktion under Return User Experience](media/responsible-ai/disclosure-patterns/transparent-intro-return.png)<br/>
*Ange en konsekvent start punkt för den syntetiska röst upplevelsen. Tillåt att användaren återgår till onboarding-upplevelsen när de utlöser rösten för första gången vid något tillfälle i användar resan.*


### <a name="verbal-transparent-introduction"></a>Muntlig introduktion

En talad prompt som anger ursprunget i den digitala&#39;assistenten s röst är tillräckligt tydligt för att få lämna ut information. Det här mönstret är bäst för scenarier med hög utlämnande av information där röst är det enda tillgängliga läget för interaktion.
<br/>

![intalat om transparent introduktion](media/responsible-ai/disclosure-patterns/spoken-prompt-1.png)
<br/>*Använd en genomskinlig introduktion när det finns en stund i användar upplevelsen där du kanske redan har introducerat eller&#39;attributerar en person s röst.*


![intalat om genomskinlig introduktion i den första personen](media/responsible-ai/disclosure-patterns/spoken-prompt-2.png)<br/>
*För ytterligare insyn kan röst skådespelaren avslöja ursprunget för den syntetiska rösten i den första personen.*

### <a name="explicit-byline"></a>Explicit Bylinen

Använd det här mönstret om användaren ska interagera med en ljuds pelare eller en interaktiv komponent för att utlösa rösten.


![explicita bylinen i ett nyhets medie scenario](media/responsible-ai/disclosure-patterns/explicit-byline.png) <br/>
*En explicit bylinen är behörigheten för var rösten kommer från.*

Rekommendationer

- Erbjudande start punkt för att lära dig mer om den syntetiskt rösten

### <a name="customization-and-calibration"></a>Anpassning och kalibrering

Ge användarna kontroll över hur den digitala assistenten svarar på dem (t. ex. Hur röst ljuden).  När en användare interagerar med ett system på egna villkor och med specifika mål i åtanke, sedan efter definition, har de redan förstått att det&#39;inte är en riktig person.

#### <a name="user-control"></a>Användar kontroll

Erbjud alternativ som har en meningsfull och märkbar inverkan på den syntetiska röst upplevelsen.

![användar inställningar](media/responsible-ai/disclosure-patterns/customization-user-control.png)<br/>
*Med användar inställningar kan användarna anpassa och förbättra sin upplevelse.*

Rekommendationer

- Tillåt användare att anpassa rösten (t. ex., Välj språk och röst typ)
- Ge användarna ett sätt att lära sig systemet för att svara på sin unika röst (t. ex. röst kalibrering, anpassade kommandon)
- Optimera för användardefinierade eller sammanhangsbaserade interaktioner (t. ex. påminnelser)

#### <a name="persona-customization"></a>Person anpassning

Med kan du anpassa Digital Assistant&#39;s röst. Om rösten är baserad på en kändis eller en mycket identifierbar person kan du överväga att använda både visuella och talade introduktioner när användarna för hands Grans kar rösten.

![röst anpassning](media/responsible-ai/disclosure-patterns/customization-voice-type.png)<br/>
*Genom att erbjuda möjligheten att välja från en uppsättning röster kan du förmedla artificiell natur.*

Rekommendationer
- Tillåt att användare för hands Grans kar ljudet för varje röst
- Använd en äkta introduktion för varje röst
- Erbjud start punkter för att lära dig mer om den syntetiskt rösten

### <a name="parental-disclosure"></a>Överordnad sekretess

Utöver att följa COPPA-regler, kan du lämna ut information till föräldrar om din primära mål grupp är små barn och din exponerings nivå är hög. För känsliga användnings områden bör du överväga att hantera upplevelse tills en vuxen har godkänt användningen av den syntetiska rösten. Uppmuntra föräldrar att kommunicera meddelandet till deras underordnade.

![information om föräldrar](media/responsible-ai/disclosure-patterns/parental-disclosure.png)<br/>
*En transparent introduktion som är optimerad för föräldrar ser till att en vuxen har blivit medveten om röstens syntetiska natur innan ett barn interagerar med den.*

Rekommendationer

- Fokusera på föräldrar som primär mål grupp för avslöjande
- Uppmuntra föräldrar att förmedla inlämnade uppgifter till deras underordnade
- Erbjud start punkter för att lära dig mer om den syntetiskt rösten
- Grind upplevelsen genom att be föräldrarna att få en enkel &quot;skydds&quot; fråga för att visa att de har läst meddelandet

### <a name="providing-opportunities-to-learn-more-about-how-the-voice-was-made"></a>Tillhandahålla möjligheter att lära sig mer om hur rösten har gjorts

Erbjud Sammanhangs beroende start punkter till en sida, ett popup-fönster eller en extern webbplats som innehåller mer information om den syntetiska röst tekniken. Du kan till exempel lägga till en länk för att lära dig mer under onboarding eller när användaren uppmanas att ange mer information under konversationen.

![start punkt för att läsa mer](media/responsible-ai/disclosure-patterns/learn-more-entry-point.png)<br/>
*Exempel på en start punkt som ger möjlighet att lära sig mer om den syntetiskt rösten.*

När en användare begär mer information om den syntetiska rösten är det primära målet att utbilda dem om ursprunget för den syntetiska rösten och vara transparent om tekniken.

![ge användare mer information om syntetisk röst](media/responsible-ai/disclosure-patterns/learn-more.png)<br/>
*Mer information finns på hjälp webbplatsen för en extern webbplats.*

Rekommendationer

- Förenkla komplexa koncept och Undvik att använda legalese och tekniska jargong
- Bury inte det här innehållet i sekretess policy och användnings villkor
- Behåll innehållet koncist och Använd bilder när det är tillgängligt

## <a name="implicit-disclosure"></a>Implicit avslöjande

Konsekvens är nyckeln för att få ut utlämnande av information implicit under användar resan. Konsekvent användning av visualiserings-och gransknings tips över enheter och interaktions sätt kan hjälpa till att bygga associationer mellan implicita mönster och uttryckliga upplysningar.

![Konsekvens för implicita stackar](media/responsible-ai/disclosure-patterns/consistency.png)

### <a name="implicit-cues--feedback"></a>Implicita tips & feedback

Anthropomorphism kan manifesta på olika sätt, från den faktiska visuella representationen av agenten, till röst, ljud, mönster av ljusa, Bouncing former eller till och med en enhets vibration. När du definierar din person kan du använda implicita tips och feedback-mönster i stället för att rikta in sig på en mycket mänsklig person. Detta är ett sätt att minimera behovet av mer uttryckliga upplysningar.

![visuella tips och feedback](media/responsible-ai/disclosure-patterns/visual-affordances.png)<br/>
*Dessa stack-ikoner hjälper anthropomorphize-agenten utan att vara för mänsklig. De kan också bli effektiva inlämnande mekanismer på egen hand när de används konsekvent över tid.*

Överväg olika sätt att interagera med din upplevelse när du införlivar följande typer av signaler:

| Visuella ikoner                                                                                                                                                               | Gransknings ikoner                                                      | Haptic-stack |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-------------|
|  Avatarikonen <br>Svar på real tids ikoner (t. ex. animeringar)<br> Ej skärmade ikoner (t. ex. lampor och mönster på en enhet)<br>  | Sonicon (t. ex. ett kort särskiljande ljud, en rad musik anteckningar) | Vibrationer   |

### <a name="capability-disclosure"></a>Utlämnande av funktioner

Utlämnande kan uppnås implicit genom att ställa in exakta förväntningar för vad den digitala assistenten kan hantera. Tillhandahåll exempel kommandon så att användarna kan lära sig att interagera med den digitala assistenten och erbjuda Sammanhangs beroende hjälp för att lära sig mer om den syntetiska rösten under de tidiga stegen i upplevelsen.

![Visuella ikoner och feedback](media/responsible-ai/disclosure-patterns/capability-disclosure.png)<br/>

### <a name="conversational-transparency"></a>Konversations genomskinlighet

När konversationer är i oväntade sökvägar bör du överväga att ställa in standardsvar som kan hjälpa till att återställa förväntningar, förstärka genomskinlighet och styra användare mot lyckade sökvägar. Det finns även möjligheter att använda explicita upplysningar i konversationen.

![Hantera oväntade sökvägar](media/responsible-ai/disclosure-patterns/conversational-transparency-1.png)<br/>

<br/>
Frågor som rör sig om inaktive ring eller &quot;personliga&quot; som skickas till agenten är en tid att påminna användare om agentens syntetiska natur och styra dem så att de kan engagera dem på rätt sätt eller att omdirigera dem till en riktig person.

![Hantering av uppgifts frågor](media/responsible-ai/disclosure-patterns/conversational-transparency-2.png)<br/>

## <a name="when-to-disclose"></a>När du ska lämna ut

Det finns många möjligheter att avslöja information under hela användar resan. Design för första användning, andra användning, n:te användning..., men omfattar även en &quot;fel&quot; för att markera genomskinlighet, t. ex. När systemet gör ett misstag eller när användaren upptäcker en begränsning av agentens&#39;funktioner.

![Utlämnande av användar möjligheter i en användar resa](media/responsible-ai/disclosure-patterns/touchpoints.png)<br/>

Exempel på en vanlig användar resa i digital Assistant som markerar olika utlämnande möjligheter.

### <a name="up-front"></a>Upp-fram

Den optimala tidpunkten för avslöjande är första gången en person interagerar med den syntetiska rösten.  I ett scenario med en personlig röst assistent skulle detta under onboarding, eller första gången användaren är i stort sett en ruta. I andra fall kan det vara första gången en syntetisk röst läser innehåll på en webbplats eller första gången en användare interagerar med ett virtuellt Character.

- [Transparent introduktion](#transparent-introduction)
- [Utlämnande av funktioner](#capability-disclosure)
- [Anpassning och kalibrering](#customization-and-calibration)
- [Implicita ikoner](#implicit-cues--feedback)

### <a name="upon-request"></a>Vid begäran

Användare bör kunna komma åt ytterligare information, kontrol lera inställningar och ta emot transparent kommunikation när som helst under användar resan när de begärs.

- [Tillhandahålla möjligheter att lära sig mer om hur rösten har gjorts](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [Anpassning och kalibrering](#customization-and-calibration)
- [Konversations genomskinlighet](#conversational-transparency)

### <a name="continuously"></a>Stadig

Använd de implicita design mönster som förbättrar användar upplevelsen kontinuerligt.

- [Utlämnande av funktioner](#capability-disclosure)
- [Implicita ikoner](#implicit-cues--feedback)

### <a name="when-the-system-fails"></a>När systemet Miss lyckas

Använd avslöjad som en möjlighet att fungera utan problem.

- [Konversations genomskinlighet](#conversational-transparency)
- [Tillhandahålla möjligheter att lära sig mer om hur rösten har gjorts](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [Leverans till mänsklig](#conversational-transparency)



## <a name="additional-resources"></a>Ytterligare resurser
- [Rikt linjer för Microsoft bot](https://www.microsoft.com/research/uploads/prod/2018/11/Bot_Guidelines_Nov_2018.pdf)
- [Design rikt linjer för Cortana](https://docs.microsoft.com/cortana/voice-commands/voicecommand-design-guidelines)
- [Design rikt linjer för Microsoft Windows UWP tal](https://docs.microsoft.com/windows/uwp/design/input/speech-interactions)
- [Rikt linjer för röst kommando i Microsoft Windows Mixed Reality](https://docs.microsoft.com/windows/mixed-reality/voice-design#top-things-users-should-know-about-speech-in-mixed-reality)

## <a name="reference-docs"></a>Referensdokument

* [Utlämnande av röst personal](https://aka.ms/disclosure-voice-talent)
* [Rikt linjer för ansvarig distribution av syntetisk röst teknik](concepts-guidelines-responsible-deployment-synthetic.md)
* [Översikt över hantera](concepts-gating-overview.md)
* [Lämna ut](concepts-disclosure-guidelines.md)

## <a name="next-steps"></a>Nästa steg

* [Utlämnande av röst personal](https://aka.ms/disclosure-voice-talent)
