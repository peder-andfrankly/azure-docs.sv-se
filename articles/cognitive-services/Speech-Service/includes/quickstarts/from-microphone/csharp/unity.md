---
title: 'Snabb start: identifiera tal från en mikrofon C# , (Unit) – tal-service'
titleSuffix: Azure Cognitive Services
description: Inte klart
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: b0a9eddf147eedfa972e0574e62c9e7c2eace84f
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818711"
---
> [!NOTE]
> Talet SDK för Unit stöder Windows Desktop (x86 och x64) eller Universell Windows-plattform (x86, x64, ARM/ARM64), Android (x86, ARM32/64) och iOS (x64 simulator, ARM32 och ARM64)

## <a name="prerequisites"></a>Krav

Innan du börjar:

> [!div class="checklist"]
> * [Skapa en Azure tal-resurs](../../../../get-started.md)
> * [Konfigurera utvecklings miljön](../../../../quickstarts/setup-platform.md?tabs=unity)
> * [Skapa ett tomt exempel projekt](../../../../quickstarts/create-project.md?tabs=unity)
> * Kontrol lera att du har åtkomst till en mikrofon för ljud inspelning

Om du redan har gjort detta är det bra. Vi fortsätter.

## <a name="create-a-unity-project"></a>Skapa ett Unity-projekt

1. Öppna Unity. Om du använder Unity för första gången visas **Unity Hub** *<version number>* fönstret. (Du kan också öppna Unity Hub direkt för att komma till det här fönstret.)

   [![Unity Hub-fönstret](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png#lightbox)
1. Välj **Ny**. Fönstret **skapa ett nytt projekt med** enhets *<version number>* visas.

   [![skapa ett nytt projekt i Unity Hub](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png#lightbox)
1. I **projekt namn**anger du **csharp-Unity**.
1. I **mallar**, om **3D** inte redan är markerat, markerar du det.
1. I **plats**väljer eller skapar du en mapp för att spara projektet i.
1. Välj **Skapa**.

Efter en stund visas fönstret Unity Editor.



## <a name="add-ui"></a>Lägga till användargränssnitt

Nu ska vi lägga till ett minimalt gränssnitt i vår scen. Det här gränssnittet består av en knapp som utlöser tal igenkänning och ett textfält som visar resultatet. I fönstret [ **hierarki** ](https://docs.unity3d.com/Manual/Hierarchy.html)visas ett exempel på en scen som har skapats med det nya projektet.

1. Längst upp i **hierarkin** väljer du **skapa** > **UI** > **knappen**.

   Den här åtgärden skapar tre spel objekt som du kan se i **hierarkifönstret** : ett **knapp** objekt, ett **arbets ytans** objekt som innehåller knappen och ett **Event Events** -objekt.

   [![Unitbar Editor-miljö](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png#lightbox)

1. [Navigera i vyn **scen** ](https://docs.unity3d.com/Manual/SceneViewNavigation.html) så att du har en lämplig vy över arbets ytan och knappen i vyn [ **scen** ](https://docs.unity3d.com/Manual/UsingTheSceneView.html).

1. I fönstret [ **kontrollant** ](https://docs.unity3d.com/Manual/UsingTheInspector.html) (som standard till höger) anger du egenskaperna för **POS X** -och **POS Y** till **0**, så knappen centreras i mitten av arbets ytan.

1. I fönstret **hierarki** väljer du **skapa** > **UI** > **text** för att skapa ett **text** objekt.

1. I fönstret **kontrollant** ställer du in egenskaperna **för POS X** och **pos Y** på **0** och **120**och anger egenskaperna för **Bredd** och **höjd** till **240** och **120**. Dessa värden ser till att textfältet och knappen inte överlappar varandra.

När du är klar bör **scenen** se ut ungefär som den här skärm bilden:

[vyn ![scen i Unity-redigeraren](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png#lightbox)

## <a name="add-the-sample-code"></a>Lägga till exempelkoden

Följ dessa steg om du vill lägga till exempel skript koden för Unity-projektet:

1. I [projekt fönstret](https://docs.unity3d.com/Manual/ProjectView.html)väljer du **skapa** >  **C# skript** för att lägga till ett C# nytt skript.

   [![projekt fönstret i Unity-redigeraren](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png#lightbox)
1. Ge skriptet namnet `HelloWorld`.

1. Dubbelklicka på `HelloWorld` för att redigera det nyligen skapade skriptet.

   > [!NOTE]
   > Om du vill konfigurera kod redigeraren så att den används av Unit för redigering väljer du **redigera** > **Inställningar**och går sedan till inställningarna för **externa verktyg** . Mer information finns i [användar handboken för Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Ersätt det befintliga skriptet med följande kod:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/from-microphone/Assets/Scripts/HelloWorld.cs#code)]

1. Sök efter och ersätt strängen `YourSubscriptionKey` med prenumerations nyckeln för röst tjänsten.

1. Sök efter och ersätt strängen `YourServiceRegion` med den [region](~/articles/cognitive-services/Speech-Service/regions.md) som är associerad med din prenumeration. Om du till exempel använder den kostnadsfria utvärderingsversionen är regionen `westus`.

1. Spara ändringarna i skriptet.

Gå tillbaka till Unity-redigeraren och Lägg till skriptet som en komponent till ett av dina spel objekt:

1. I fönstret **hierarki** väljer du objektet **arbets yta** .

1. I fönstret **kontrollant** väljer du knappen **Lägg till komponent** .

   [fönstret ![kontrollant i Unity-redigeraren](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png#lightbox)

1. I list rutan söker du efter det `HelloWorld`-skript som vi skapade ovan och lägger till det. Ett **Hello World (skript)** visas i fönstret **kontrollant** , som visar två oinitierade egenskaper, **utdata-text** och **knappen starta åters**. Dessa komponenters egenskaper matchar de offentliga egenskaperna i `HelloWorld`-klassen.

1. Välj objekt väljaren för **knappen starta åters** (den lilla cirkel ikonen till höger om egenskapen) och välj det **knapp** objekt som du skapade tidigare.

1. Välj objekt väljar egenskapen **utgående text** och välj **textobjektet som du** skapade tidigare.

   > [!NOTE]
   > Knappen har även ett kapslat textobjekt. Se till att du inte av misstag väljer att använda text utdata (eller byta namn på ett text objekt med fältet **namn** i fönstret **kontrollant** för att undvika förvirring).

## <a name="run-the-application-in-the-unity-editor"></a>Köra programmet i Unity Editor

Nu är du redo att köra programmet i Unity-redigeraren.

1. I verktygsfältet för Unity-redigeraren (under meny raden) väljer du knappen **spela upp** (en högerriktad triangel).

1. Gå till [ **spel** läge](https://docs.unity3d.com/Manual/GameView.html)och vänta tills **textobjektet** visar **knappen Klicka för att känna igen tal**. (Den **nya texten** visas när programmet inte har startats eller inte är klart att svara.)

1. Välj knappen och tala om en engelsk fras eller mening till datorns mikrofon. Ditt tal överförs till tal tjänsten och överförs till text som visas i vyn **spel** .

   [![Game View i Unity-redigeraren](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png#lightbox)

1. Kontrol lera [ **konsol** fönstret](https://docs.unity3d.com/Manual/Console.html) för fel söknings meddelanden. Om **konsol** fönstret inte visas går du till meny raden och väljer **fönster** > **General** > - **konsolen** för att visa den.

1. När du är klar med att känna igen tal väljer du knappen **spela upp** i verktygsfältet Unity Editor för att stoppa programmet.

## <a name="additional-options-to-run-this-application"></a>Ytterligare alternativ för att köra det här programmet

Det här programmet kan också distribueras till som en Android-app, en fristående Windows-app eller ett UWP-program.
Mer information finns i vår [exempel databas](https://aka.ms/csspeech/samples). Mappen `quickstart/csharp-unity` beskriver konfigurationen för dessa ytterligare mål.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]
