---
title: 'Snabb start: ett personligt klient bibliotek för .NET | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Kom igång med personanpassa klient biblioteket för .NET med hjälp av en inlärnings slinga.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 10/24/2019
ms.author: diberry
ms.openlocfilehash: 411bd82ade2ca7b904b36a3a4408c1a00852fc2c
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927826"
---
# <a name="quickstart-personalizer-client-library-for-net"></a>Snabb start: ett personligt klient bibliotek för .NET

Visa personligt innehåll i den C# här snabb starten med tjänsten personanpassa.

Kom igång med personanpassa klient biblioteket för .NET. Följ de här stegen för att installera paketet och prova exempel koden för grundläggande uppgifter.

 * Rangordna en lista med åtgärder för anpassning.
 * Rapportera belönings Poäng som indikerar att det främsta antalet rankade åtgärder lyckades.

[Referens dokumentation](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer?view=azure-dotnet-preview) | [Library Source Code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Personalizer) | [Package (NuGet) | -](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Personalizer/) [exempel](https://github.com/Azure-Samples/cognitive-services-personalizer-samples)

## <a name="prerequisites"></a>Krav

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
* Den aktuella versionen av [.net Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="using-this-quickstart"></a>Använd den här snabb starten

Det finns flera steg för att använda den här snabb starten:

* I Azure Portal skapar du en personanpassar-resurs
* I Azure Portal för personanpassa resursen, på sidan **konfiguration** , ändrar du modell uppdaterings frekvensen
* Skapa en kod fil i en kod redigerare och redigera kod filen
* På kommando raden eller terminalen installerar du SDK från kommando raden
* Kör kod filen i kommando raden eller terminalen

## <a name="create-a-personalizer-azure-resource"></a>Skapa en personanpassa Azure-resurs

Skapa en resurs för Personanpassare med hjälp av [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) eller [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) på den lokala datorn. Du kan också:

* Få en [utvärderings nyckel](https://azure.microsoft.com/try/cognitive-services) som är giltig i 7 dagar utan kostnad. När du har registrerat dig är den tillgänglig på [Azure-webbplatsen](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Visa din resurs på [Azure Portal](https://portal.azure.com/).

När du har skaffat en nyckel från din utvärderings prenumeration eller resurs skapar du två [miljö variabel](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `PERSONALIZER_RESOURCE_KEY` för resurs nyckeln.
* `PERSONALIZER_RESOURCE_ENDPOINT` för resurs slut punkten.

I Azure Portal är både nyckel-och slut punkts värden tillgängliga från **snabb starts** sidan.

## <a name="change-the-model-update-frequency"></a>Ändra modell uppdaterings frekvensen

I Azure Portal i gruppen personanpassa på sidan **konfiguration** ändrar du **modell uppdaterings frekvensen** till 10 sekunder. Den här korta varaktigheten kommer att träna tjänsten snabbt, så att du kan se hur de viktigaste åtgärderna ändras för varje iteration.

![Ändra modell uppdaterings frekvens](./media/settings/configure-model-update-frequency-settings.png)

När en säkerhetsslinga först instansieras finns det ingen modell eftersom det inte har skett några belönings-API-anrop att träna från. Ranknings anrop returnerar lika många sannolikheter för varje objekt. Ditt program borde fortfarande alltid rangordna innehåll med hjälp av utdata från RewardActionId.

## <a name="create-a-new-c-application"></a>Skapa ett nytt C# program

Skapa ett nytt .NET Core-program i önskat redigerings program eller IDE. 

I ett konsol fönster (till exempel cmd, PowerShell eller bash) använder du kommandot dotNet `new` för att skapa en ny konsol-app med namnet `personalizer-quickstart`. Det här kommandot skapar ett enkelt "Hello World C# "-projekt med en enda källfil: `Program.cs`. 

```console
dotnet new console -n personalizer-quickstart
```

Ändra katalogen till mappen nyligen skapade appar. Du kan bygga programmet med:

```console
dotnet build
```

Build-utdata får inte innehålla varningar eller fel. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

## <a name="install-the-sdk"></a>Installera SDK:n

I program katalogen installerar du ett installations program för personanpassa klient bibliotek för .NET med följande kommando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Personalizer --version 0.8.0-preview
```

Om du använder Visual Studio IDE är klient biblioteket tillgängligt som ett nedladdnings Bart NuGet-paket.

## <a name="object-model"></a>Objekt modell

Personanpassa klienten är ett [PersonalizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclient?view=azure-dotnet) -objekt som autentiserar till Azure med hjälp av Microsoft. rest. ServiceClientCredentials, som innehåller din nyckel.

Om du vill be om innehållets rangordning skapar du en [RankRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rankrequest?view=azure-dotnet-preview)och skickar den sedan till [klienten. Rangordnings](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.rank?view=azure-dotnet-preview) metod. Rangordnings metoden returnerar en RankResponse som innehåller det rankade innehållet. 

Om du vill skicka en belöning till Personanpassan skapar du en [RewardRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rewardrequest?view=azure-dotnet-preview)och skickar den till [klienten. Belönings](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.reward?view=azure-dotnet-preview) metod. 

Att fastställa belöningen i den här snabb starten är trivial. I ett produktions system kan du bestämma vad som påverkar [belönings poängen](concept-rewards.md) och hur mycket som kan vara en komplicerad process som du kan välja att ändra med tiden. Det här design beslutet bör vara ett av de viktigaste besluten i din personanpassa arkitektur. 

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du utför följande uppgifter med personanpassa klient biblioteket för .NET:

* [Skapa en personanpassa klient](#create-a-personalizer-client)
* [Begär en rang](#request-a-rank)
* [Skicka en belöning](#send-a-reward)

## <a name="add-the-dependencies"></a>Lägg till beroenden

Från projekt katalogen öppnar du **program.cs** -filen i önskat redigerings program eller IDE. Ersätt den befintliga `using`s koden med följande `using`-direktiv:

[!code-csharp[Using statements](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Lägg till information om personanpassa resurser

I **program** -klassen skapar du variabler för din resurs Azure-nyckel och slut punkt som hämtats från miljövariablerna, med namnet `PERSONALIZER_RESOURCE_KEY` och `PERSONALIZER_RESOURCE_ENDPOINT`. Om du har skapat miljövariablerna när programmet har startats måste redigeraren, IDE eller gränssnittet som kör det stängas och läsas in igen för att få åtkomst till variabeln. Metoderna kommer att skapas senare i den här snabb starten.

[!code-csharp[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=classVariables)]

## <a name="create-a-personalizer-client"></a>Skapa en personanpassa klient

Skapa sedan en metod för att returnera en personanpassa klient. Parametern till metoden är `PERSONALIZER_RESOURCE_ENDPOINT` och ApiKey är `PERSONALIZER_RESOURCE_KEY`.

[!code-csharp[Create the Personalizer client](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=authorization)]

## <a name="get-food-items-as-rankable-actions"></a>Hämta mat objekt som ranknings bara åtgärder

Åtgärder representerar de innehålls val som du vill att en Personanpassare ska rangordna. Lägg till följande metoder i program-klassen för att representera den uppsättning åtgärder som ska rangordnas.

[!code-csharp[Food items as actions](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createAction)]

## <a name="get-user-preferences-for-context"></a>Hämta användar inställningar för kontext

Lägg till följande metoder i program-klassen för att få en användares indata från kommando raden för tid på dag och aktuell kost preferens. Dessa kommer att användas som sammanhang vid rangordning av åtgärder.

[!code-csharp[Present time out day preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTimeOfDay)]

[!code-csharp[Present food taste preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTastePreference)]

Båda metoderna använder metoden `GetKey` för att läsa användarens val från kommando raden. 

[!code-csharp[Read user's choice from the command line](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=readCommandLine)]

## <a name="create-the-learning-loop"></a>Skapa inlärnings slingan

Inlärnings-loopen för inlärning är en cykel av rang-och belönings samtal. I den här snabb starten, som varje rang anrop, för att anpassa innehållet, följs av ett belönings samtal för att berätta för personanpassa hur väl tjänsten rangordnade innehållet. 

Följande kod i metoden `main` i program slinga genom en cykel som frågar användaren om sina inställningar på kommando raden, skickar informationen till en person som ska rangordnas, presenterar det rankade valet för kunden att välja bland listan, och sedan skicka en belöning till en personlig signalering av hur väl tjänsten gjorde en rangordning av valet.

[!code-csharp[Learning loop](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=mainLoop)]

Lägg till följande metoder, som [hämtar innehålls valen](#get-food-items-as-rankable-actions), innan du kör kod filen:

* `GetActions`
* `GetUsersTimeOfDay`
* `GetUsersTastePreference`
* `GetKey`

## <a name="request-a-rank"></a>Begär en rang

För att slutföra ranknings förfrågan ställer programmet till användarens inställningar för att skapa en `currentContent` av innehålls valen. Processen kan skapa innehåll som ska undantas från rankningen, som visas som `excludeActions`. Ranknings förfrågan behöver åtgärderna, currentContext, excludeActions och ett unikt ID för ranknings händelse (som GUID) för att ta emot det rankade svaret. 

Den här snabb starten har enkla Sammanhangs funktioner i tid på dygnet och användarens mat preferenser. I produktions system kan det vara en icke-trivial sak att fastställa och [utvärdera](concept-feature-evaluation.md) [åtgärder och funktioner](concepts-features.md) .  

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=rank)]

## <a name="send-a-reward"></a>Skicka en belöning

För att slutföra belönings förfrågan hämtar programmet användarens val från kommando raden, tilldelar ett numeriskt värde till varje val och skickar sedan det unika ID: t för rangordning och det numeriska värdet till belönings metoden.

Den här snabb starten tilldelar ett enkelt tal som en belöning, antingen noll eller 1. I produktions system kan du fastställa när och vad som ska skickas till [belönings](concept-rewards.md) anropet som en icke-trivial fråga, beroende på dina behov. 

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=reward)]

## <a name="run-the-program"></a>Köra programmet

Kör programmet med kommandot dotNet `run` från program katalogen.

```console
dotnet run
```

![Snabb start programmet ber några frågor om att samla in användar inställningar, kallas funktioner, och ger sedan den främsta åtgärden.](media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

[Käll koden för den här snabb](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/csharp/PersonalizerExample/Program.cs) starten är tillgänglig i GitHub-lagringsplatsen för personanpassa exempel.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portalen](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
>[Så här fungerar Personanpassaren](how-personalizer-works.md)

* [Vad är Personanpassare?](what-is-personalizer.md)
* [Var kan du använda Personanpassare?](where-can-you-use-personalizer.md)
* [Troubleshooting](troubleshooting.md) (Felsökning)

