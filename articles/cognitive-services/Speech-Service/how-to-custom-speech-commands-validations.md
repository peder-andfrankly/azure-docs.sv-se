---
title: 'Gör så här: Lägg till valideringar i anpassade kommando parametrar (för hands version)'
titleSuffix: Azure Cognitive Services
description: I den här artikeln lägger du till valideringar i anpassade kommando parametrar
services: cognitive-services
author: donkim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: 64e092405686caca7baeaf58f19d577a3f80e169
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506938"
---
# <a name="how-to-add-validations-to-custom-command-parameters-preview"></a>Gör så här: Lägg till valideringar i anpassade kommando parametrar (för hands version)

I den här artikeln får du lära dig hur du lägger till verifieringar i parametrar och fråga efter korrigering.

## <a name="prerequisites"></a>Förutsättningar

Du måste ha slutfört stegen i följande artiklar:

- [Snabb start: skapa ett anpassat kommando (förhands granskning)](./quickstart-custom-speech-commands-create-new.md)
- [Snabb start: skapa ett anpassat kommando med parametrar (förhands granskning)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>Skapa ett SetTemperature-kommando

För att demonstrera valideringar ska vi skapa ett nytt kommando som gör det möjligt för användaren att ange temperatur.

1. Öppna dina tidigare skapade program för anpassade kommandon i [tal Studio](https://speech.microsoft.com/)
1. Skapa ett nytt kommando **SetTemperature**
1. Lägg till en parameter för mål temperaturen
1. Lägg till en validering för temperatur parametern
   > [!div class="mx-imgBorder"]
   > ![lägga till ett intervall verifierings](media/custom-speech-commands/validations-add-temperature.png)

   | Inställning           | Föreslaget värde                                          | Beskrivning                                                                                      |
   | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
   | Namn              | Temperatur                                              | Ett beskrivande namn för kommando parametern                                                    |
   | Krävs          | true                                                     | Kryss ruta som anger om ett värde för den här parametern krävs innan kommandot slutförs |
   | Svarsmall | "Vilken temperatur vill du ha?"                       | En uppmaning om att fråga efter värdet för den här parametern när den inte är känd                              |
   | Typ              | Tal                                                   | Parameter typ, t. ex. tal, sträng eller datum/tid                                      |
   | Validering        | Minsta värde: 60, Max värde: 80                             | För Number-parametrar är det tillåtna värde intervallet för parametern                              |
   | Svarsmall | "Det går bara att ange mellan 60 och 80 grader"        | Fråga efter ett uppdaterat värde om valideringen Miss lyckas                                       |

1. Lägg till några exempel meningar

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. Lägg till en regel för slut för ande för att bekräfta resultatet

   | Inställning    | Föreslaget värde                                         | Beskrivning                                        |
   | ---------- | ------------------------------------------------------- | -------------------------------------------------- |
   | Regelnamn  | Bekräftelse meddelande                                    | Ett namn som beskriver syftet med regeln          |
   | Villkor | Obligatorisk parameter-temperatur                        | Villkor som avgör när regeln kan köras    |
   | Åtgärder    | SpeechResponse-"OK, ställa in på {temperatur} grader" | Den åtgärd som ska vidtas när regel villkoret är sant |

> [!TIP]
> I det här exemplet används ett tal svar för att bekräfta resultatet. Exempel på hur du slutför kommandot med en klient åtgärd finns i: [så här gör du för att utföra kommandon på klienten med Speech SDK (för hands version)](./how-to-custom-speech-commands-fulfill-sdk.md) .

## <a name="try-it-out"></a>Prova det

Välj panelen test och prova några interaktioner.

- Inmatade: Ange temperatur till 72 grader
- Utdata: "OK, ställa in på 72 grader"

- Inmatade: Ange temperatur till 45 grader
- Output: "Tyvärr, jag kan bara ange mellan 60 och 80 grader"
- Inmatade: gör det till 72 grader i stället
- Utdata: "OK, ställa in på 72 grader"
