---
title: Så här fungerar Personanpassare – Personanpassare
titleSuffix: Azure Cognitive Services
description: En personanpassare använder Machine Learning för att identifiera vilken åtgärd som ska användas i en kontext. Varje inlärnings slinga har en modell som har tränats uteslutande på data som du har skickat till den via rang-och belönings samtal. Alla inlärnings slingor är helt oberoende av varandra.
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: 902bf84ebf090cf9f0f886ad1e774ff7bdfeca93
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490752"
---
# <a name="how-personalizer-works"></a>Så här fungerar Personanpassning

En personanpassare använder Machine Learning för att identifiera vilken åtgärd som ska användas i en kontext. Varje inlärnings slinga har en modell som har tränats uteslutande på data som du har skickat till den via **rang** -och **belönings** samtal. Alla inlärnings slingor är helt oberoende av varandra. Skapa en inlärnings slinga för varje del eller beteende för ditt program som du vill anpassa.

För varje slinga **anropar du rang-API: et med** baserat på aktuell kontext, med:

* Lista över möjliga åtgärder: innehålls objekt från vilka du väljer bästa åtgärd.
* Lista över [Sammanhangs funktioner](concepts-features.md): Sammanhangs beroende data, till exempel användare, innehåll och kontext.

**Ranknings** -API: et bestämmer att du vill använda antingen:

* _Sårbarhet_: den aktuella modellen för att avgöra den bästa åtgärden baserat på tidigare data.
* _Utforska_: Välj en annan åtgärd i stället för den översta åtgärden.

**Belönings** -API: et:

* Samlar in data för att träna modellen genom att registrera funktionerna och belönings poängen för varje rang samtal.
* Använder dessa data för att uppdatera modellen baserat på konfigurationen som anges i _inlärnings principen_.

## <a name="architecture"></a>Arkitektur

Följande bild visar det arkitektoniska flödet för anrop av rang-och belönings samtal:

![alternativ text](./media/how-personalizer-works/personalization-how-it-works.png "Så här fungerar anpassning")

1. Personanpassaren använder en intern AI-modell för att fastställa rangordningen för åtgärden.
1. Tjänsten bestämmer om du vill utnyttja den aktuella modellen eller utforska nya val för modellen.  
1. Ranknings resultatet skickas till EventHub.
1. När Personanpassaren tar emot belöningen skickas belöningen till EventHub. 
1. Rankningen och belöningen korreleras.
1. AI-modellen uppdateras utifrån korrelations resultatet.
1. Härlednings motorn uppdateras med den nya modellen. 

## <a name="research-behind-personalizer"></a>Forskning bakom personanpassa

Personanpassan är baserad på vetenskaps-och forsknings verksamhet i området för [förstärknings undervisning](concepts-reinforcement-learning.md) , inklusive arbeten, forsknings aktiviteter och fort löp ande forsknings områden i Microsoft Research.

## <a name="terminology"></a>Terminologi

* **Inlärnings slinga**: du kan skapa en inlärnings slinga för varje del av ditt program som kan dra nytta av anpassning. Om du har mer än en upplevelse att anpassa, skapar du en slinga för var och en. 

* **Åtgärder**: åtgärder är innehålls objekt, till exempel produkter eller kampanjer, för att välja bland. Personanpassare väljer den främsta åtgärden som ska visas för dina användare, som kallas _belönings åtgärd_, via rang-API: et. Varje åtgärd kan ha funktioner som skickas med rang-begäran.

* **Kontext**: Ange en mer exakt rangordning genom att ange information om din kontext, till exempel:
    * Användaren.
    * Enheten som de är på. 
    * Aktuell tid.
    * Andra data om den aktuella situationen.
    * Historiska data om användaren eller kontexten.

    Ditt specifika program kan ha annan Sammanhangs information. 

* **[Funktioner](concepts-features.md)** : en enhet med information om ett innehålls objekt eller en användar kontext.

* **Belöning**: ett mått på hur användaren svarade på den ranknings-API som returnerade åtgärden, som ett resultat mellan 0 och 1. Värdet 0 till 1 anges av din affärs logik, baserat på hur valet hjälpte dig att uppnå dina affärs mål för anpassning. 

* **Utforskning**: tjänsten personanpassa är en utforskande av när du i stället för att returnera den bästa åtgärden väljer en annan åtgärd för användaren. Tjänsten personanpassa förhindrar drift, stagnation och kan anpassas till pågående användar beteende genom att utforska. 

* **Experimentets varaktighet**: den tid som tjänsten personanpassa förväntar sig en belöning, med början från det ögonblick då rang samtalet inträffade för händelsen.

* **Inaktiva händelser**: en inaktiv händelse är en där du anropade rang, men du är inte säker på att användaren någonsin kommer att se resultatet, på grund av klient programs beslut. Inaktiva händelser gör att du kan skapa och lagra anpassnings resultat och sedan välja att ta bort dem senare utan att påverka Machine Learning-modellen.

* **Modell**: en personanpassa modell som samlar in alla data som lärts om användarens beteende, hämtar tränings data från kombinationen av argument som du skickar till ranknings-och belönings samtal och med ett inlärnings beteende som fastställs av inlärnings principen. 

* **Inlärnings princip**: hur personanpassa tågen en modell i varje händelse kommer att fastställas av vissa meta-parametrar som påverkar hur Machine Learning-algoritmer fungerar. Nya inlärnings slingor börjar med en standard inlärnings princip som kan ge måttliga prestanda. När du kör [utvärderingarna](concepts-offline-evaluation.md)kan du skapa nya inlärnings principer som är specifikt optimerade för loopens användnings fall. Personanpassaren kommer att utföra avsevärt bättre med principer som är optimerade för varje enskild slinga, som genereras under utvärderingen.

## <a name="example-use-cases-for-personalizer"></a>Exempel på användnings fall för Personanpassare

* Avsikt att klargöra & untvetydighet: hjälpa dina användare att få en bättre upplevelse när deras avsikt inte är klar genom att tillhandahålla ett alternativ som är anpassat för varje användare.
* Standard förslag för menyer & alternativ: låt bot-roboten föreslå det mest sannolika objektet på ett personligt sätt som ett första steg, i stället för att presentera en egen meny eller en lista med alternativ.
* Bot-traiter & tonen: för robotar som kan variera ton, utförlighet och skriv stil, bör du överväga att variera dessa egenskaper på ett personligt sätt.
* Aviserings & aviserings innehåll: Bestäm vilken text som ska användas för aviseringar för att engagera användare.
* Aviserings & tids inställning för aviseringar: har anpassad inlärning av när du ska skicka meddelanden till användare för att kunna engagera dem.

## <a name="how-to-use-personalizer-in-a-web-application"></a>Så här använder du Personanpassare i ett webb program

Att lägga till en loop till ett webb program innehåller:

* Bestäm vilken upplevelse som ska anpassas, vilka åtgärder och funktioner du har, vilka Sammanhangs funktioner som ska användas och vilken belöning du ställer in.
* Lägg till en referens till anpassnings-SDK: n i ditt program.
* Anropa rang-API: et när du är redo att anpassa.
* Lagra eventId. Du skickar en belöning med belönings-API: et senare.
1. Anropa aktivera för händelsen när du är säker på att användaren har sett din anpassade sida.
1. Vänta på att användare väljer Rankat innehåll. 
1. Ring belönings-API för att ange hur väl utdata från rang-API: n gjorde.

## <a name="how-to-use-personalizer-with-a-chat-bot"></a>Så här använder du en Personanpassare med en chatt-robot

I det här exemplet får du se hur du använder anpassning för att göra ett standard förslag istället för att skicka användaren en serie menyer eller alternativ varje gång.

* Hämta [koden](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/ChatbotExample) för det här exemplet.
* Konfigurera din bot-lösning. Se till att publicera ditt LUIS-program. 
* Hantera rang-och belönings-API-anrop för bot.
    * Lägg till kod för att hantera bearbetning av LUIS-avsikt. Om **ingen** returneras som den främsta avsikten eller den främsta avsikts poängen är lägre än din affärs logiks tröskel, skickar du listan med intentor till personanpassaren för att rangordna intentarna.
    * Visa avsikts lista till användare som valbara länkar med det första syftet som det främsta ordnade syftet från rang-API-svar.
    * Avbilda användarens val och skicka detta i belönings-API-anropet. 

### <a name="recommended-bot-patterns"></a>Rekommenderade robot mönster

* Gör en degrads-API-anrop varje gång en avtvetydighet behövs, i stället för att cachelagra resultat för varje användare. Resultatet av kontrollerades kan ändras med tiden för en person, och att rang-API: et kan upptäcka varianser för att påskynda den övergripande inlärningen.
* Välj en interaktion som är gemensam för många användare så att du har tillräckligt med data för att anpassa. Till exempel kan introduktions frågor vara bättre än mindre tydliga i konversations diagrammet som bara några få användare kan komma åt.
* Använd rang-API-anrop för att aktivera "första förslaget är rätt"-konversationer, där användaren får frågan "vill du ha X?" eller "menade du X?" och användaren kan bara bekräfta. i stället för att ge alternativ till användaren där de måste välja från en meny. Till exempel användare: "Jag vill beställa en kaffe" robot: "vill du ha en dubbel espresso?". På så sätt kan belönings signalen också vara stark eftersom den gäller direkt till ett förslag.

## <a name="how-to-use-personalizer-with-a-recommendation-solution"></a>Så här använder du en Personanpassare med en rekommendations lösning

Använd rekommendations motorn för att filtrera ned en stor katalog till några objekt som sedan kan visas som 30 möjliga åtgärder som skickas till rang-API: et.

Du kan använda rekommendations motorer med Personanpassare:

* Konfigurera [rekommendations lösningen](https://github.com/Microsoft/Recommenders/). 
* När du visar en sida, anropar du rekommendations modellen för att få en kort lista över rekommendationer.
* Ring upp anpassningen för att rangordna utdata från rekommendations lösningen.
* Skicka feedback om din användar åtgärd med belönings-API-anropet.


## <a name="pitfalls-to-avoid"></a>Fall GRO par för att undvika

* Använd inte Personanpassare där det personliga beteendet inte är något som kan upptäckas för alla användare, men i stället något som ska sparas för vissa användare eller kommer från en användarspecifik lista med alternativ. Till exempel är det bra att använda Personanpassare för att föreslå en första pizza-order från en lista med 20 möjliga meny alternativ, men vilken kontakt som ska anropas från användarnas kontakt lista när du behöver hjälp med Childcare (t. ex. "Grandma") är inte något som personalizable över användar basen.


## <a name="adding-content-safeguards-to-your-application"></a>Lägga till innehålls skydd i ditt program

Om ditt program tillåter stora varianser i innehåll som visas för användarna, och en del av innehållet kan vara osäkert eller olämpligt för vissa användare, bör du planera framåt för att se till att rätt skydd är på plats för att förhindra att användarna ser oacceptabelt bra innehåll. Det bästa mönstret för att implementera säkerhets åtgärder är:
    * Hämta listan över åtgärder som ska rangordnas.
    * Filtrera bort de som inte är livskraftiga för mål gruppen.
    * Ranka bara de här lönsamma åtgärderna.
    * Visa den främsta rangordnade åtgärden för användaren.

I vissa arkitekturer kan ordningen ovan vara svår att implementera. I så fall finns det en alternativ metod för att implementera skydd efter rangordning, men en provision måste göras så att åtgärder som faller utanför skyddet inte används för att träna personanpassa modeller.

* Hämta listan med åtgärder som ska rangordnas med inlärning inaktiverat.
* Ranknings åtgärder.
* Kontrol lera om den främsta åtgärden är livskraftig.
    * Om den främsta åtgärden är livskraftig aktiverar du inlärning för den här rangordningen och visar den sedan för användaren.
    * Om den översta åtgärden inte är livskraftig aktiverar du inte inlärning för den här rangordningen och bestämmer dig genom din egen logik eller alternativa metoder vad som ska visas för användaren. Även om du använder det andra bästa rangordnings alternativet ska du inte aktivera inlärning för den här rangordningen.

## <a name="verifying-adequate-effectiveness-of-personalizer"></a>Verifierar tillräcklig effektivitet hos Personanpassaren

Du kan övervaka effektiviteten för Personanpassare regelbundet genom att utföra [offline-utvärderingar](how-to-offline-evaluation.md)

## <a name="next-steps"></a>Nästa steg

Förstå [var du kan använda personanpassare](where-can-you-use-personalizer.md).
Utföra [offline-utvärderingar](how-to-offline-evaluation.md)
