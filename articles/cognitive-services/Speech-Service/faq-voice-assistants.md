---
title: Vanliga frågor och svar om röst assistenter
titleSuffix: Azure Cognitive Services
description: Få svar på de vanligaste frågorna om röst assistenter med anpassade kommandon (förhands granskning) eller direkt linje tal kanalen.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 8480299c2c889a243150028ac9651f4b62656aec
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2019
ms.locfileid: "74110355"
---
# <a name="voice-assistants-frequently-asked-questions"></a>Vanliga frågor och svar om röst assistenter

Om du inte kan hitta svar på dina frågor i det här dokumentet kan du titta närmare på [andra support alternativ](support.md).

## <a name="general"></a>Allmänt

**F: Vad är en röst assistent?**

**A:** Precis som Cortana, är en röst assistent en lösning som lyssnar på en användares talade yttranden, analyserar innehållet i dessa yttranden för betydelse, utför en eller flera åtgärder som svar på uttrycks avsikt och ger sedan ett svar på användaren som ofta innehåller en talade komponent. Det är en "röst-in-, röst-och"-upplevelse för att interagera med ett system. röst assistents utvecklare skapar ett program på enheten med hjälp av `DialogServiceConnector` i tal-SDK: n för att kommunicera med en assistent som skapats med [anpassade kommandon (för hands version)](custom-commands.md) eller den [direkta linjens tal](direct-line-speech.md) kanal i bot Framework. Dessa assistenter kan använda anpassade nyckelord, anpassat tal och anpassad röst för att ge en upplevelse som är anpassad till ditt varumärke eller din produkt.

**F: ska jag använda anpassade kommandon (förhands granskning) eller direkt linje tal? Vad är skillnaden?**

S **:** [anpassade kommandon (förhands granskning)](custom-commands.md) är en lägre komplexitets uppsättning med verktyg som gör det enkelt att skapa och vara värd för en assistent som är väl lämpad för scenarier med uppgift att slutföra. [Direkt linje tal](direct-line-speech.md) ger bättre och mer sofistikerade funktioner som kan möjliggöra robusta konversations scenarier. Mer information finns i [jämförelse av Assistant-lösningar](voice-assistants.md#choosing-an-assistant-solution) .

**F: Hur gör jag för att kom igång?**

**A:** Det bästa sättet att börja med att skapa ett anpassat kommando (för hands version) eller ett Basic bot-program (Basic bot Framework).

- [Skapa ett anpassat kommando (för hands version)](quickstart-custom-speech-commands-create-new.md)
- [Skapa en grundläggande bot-robot](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
- [Anslut en robot till den direkta linjens tal kanal](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)

## <a name="debugging"></a>Felsökning

**F: var är min kanal hemlighet?**

**A:** Om du har använt för hands versionen av direkt linje tal eller läser relaterad dokumentation kan du vänta på att hitta en hemlig nyckel på registrerings sidan för direkt rad igenkänning av tal kanaler. Den `DialogServiceConfig` fabriks metoden för v 1.7 `FromBotSecret` i tal-SDK förväntar sig även det här värdet.

Den senaste versionen av direkt linje tal fören klar processen med att kontakta din robot från en enhet. På sidan kanal registrering associerar List rutan längst upp din direkta serie för tal kanal registrering med en tal resurs. När det är associerat innehåller v 1.8 tal-SDK en `BotFrameworkConfig::FromSubscription` fabriks metod som konfigurerar ett `DialogServiceConnector` för att kontakta den bot som du har associerat med din prenumeration.

Om du fortfarande migrerar klient programmet från v 1.7 till v 1.8 kan `DialogServiceConfig::FromBotSecret` fortsätta att arbeta med ett värde som inte är tomt för den kanal hemliga parametern, t. ex. den tidigare hemlighet som du använde. Det kommer bara att ignoreras när du använder en tal prenumeration som är associerad med en nyare kanal registrering. Observera att värdet inte _får_ vara null och icke-tomt, eftersom de är markerade för enheten innan associationen på tjänst sidan är relevant.

En mer detaljerad guide finns i [avsnittet om självstudier](tutorial-voice-enable-your-bot-speech-sdk.md#register-the-direct-line-speech-channel) som går igenom kanal registreringen.

**F: Jag får ett 401-fel när jag ansluter och ingenting fungerar. Jag vet att min röst prenumerations nyckel är giltig. Vad är det som händer?**

**A:** När du hanterar din prenumeration på Azure Portal bör du kontrol lera att du använder **tal** resursen (Microsoft. CognitiveServicesSpeechServices, "Speech") och _inte_ **Cognitive Services** resursen ( Microsoft. CognitiveServicesAllInOne, "alla Cognitive Services"). Kontrol lera också [Support för röst assistenten i tjänsten Speech service](regions.md#voice-assistants).

![rätt prenumeration för direkt linje tal](media/voice-assistants/faq-supported-subscription.png "exempel på en kompatibel tal prenumeration")

**F: Jag får igenkännings text tillbaka från `DialogServiceConnector`, men jag ser ett "1011"-fel och inget från min robot. Varför?**

**A:** Det här felet indikerar ett kommunikations problem mellan din assistent och tjänsten röst assistent.

- För anpassade kommandon (för hands version) ser du till att dina anpassade kommandon (för hands version) är publicerade
- För direkt linje tal ser du till att du har [anslutit din robot till den direkta rad igenkännings kanalen](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech), [lagt till stöd för strömnings protokoll](https://aka.ms/botframework/addstreamingprotocolsupport) i din robot (med relaterad WebSocket-support) och kontrollerar sedan att roboten svarar på inkommande begär Anden från kanalen.

**F: den här koden fungerar fortfarande inte och/eller jag får ett annat fel meddelande när du använder en `DialogServiceConnector`. Vad ska jag göra?**

**A:** Filbaserad loggning ger betydligt mer information och kan hjälpa till att påskynda support förfrågningar. Information om hur du aktiverar den här funktionen finns i [så här använder du fil loggning](how-to-use-logging.md).

## <a name="next-steps"></a>Nästa steg

- [Troubleshooting](troubleshooting.md) (Felsökning)
- [Viktig information](releasenotes.md)
