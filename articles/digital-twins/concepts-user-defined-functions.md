---
title: Data bearbetning och användardefinierade funktioner – Azure Digitals flätar | Microsoft Docs
description: Översikt över data bearbetning,-matchningar och användardefinierade funktioner med Azure Digitals dubbla.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 76ff8a63c2fbda3ddbaed794d24f7adb66a4dd95
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73930365"
---
# <a name="data-processing-and-user-defined-functions"></a>Databearbetning och användardefinierade funktioner

Azure Digitals dubbla ger avancerade beräknings funktioner. Utvecklare kan definiera och köra anpassade funktioner mot inkommande telemetridata för att skicka händelser till fördefinierade slut punkter.

## <a name="data-processing-flow"></a>Data bearbetnings flöde

När enheterna skickar telemetridata till Azure Digitals, kan utvecklare bearbeta data i fyra faser: *validera*, *matcha*, *Beräkna*och *Skicka*.

[![Azure Digitals informations bearbetnings flöde](media/concepts/digital-twins-data-processing-flow.png)](media/concepts/digital-twins-data-processing-flow.png#lightbox)

1. Validerings fasen transformerar meddelandet inkommande telemetri till ett vanligt [data överförings objekt](https://docs.microsoft.com/aspnet/web-api/overview/data/using-web-api-with-entity-framework/part-5) format. I den här fasen körs även enhets-och sensor validering.
1. Matchnings fasen söker efter lämpliga användardefinierade funktioner som ska köras. Fördefinierade matchningar söker efter användardefinierade funktioner baserat på information om enhet, sensor och utrymme från meddelandet inkommande telemetri.
1. Beräknings fasen kör de användardefinierade funktioner som matchar i föregående fas. Dessa funktioner kan läsa och uppdatera beräknade värden på spatiala diagram-noder och kan generera anpassade meddelanden.
1. Sändnings fasen dirigerar eventuella anpassade meddelanden från beräknings fasen till slut punkter som definierats i diagrammet.

## <a name="data-processing-objects"></a>Data bearbetnings objekt

Data bearbetning i digitala Digital-objekt i Azure består av att definiera tre objekt: *motsvarigheter*, *användardefinierade funktioner*och *roll tilldelningar*.

[![Azure Digitals dubbla data bearbetnings objekt](media/concepts/digital-twins-user-defined-functions.png)](media/concepts/digital-twins-user-defined-functions.png#lightbox)

### <a name="matchers"></a>Matchningar

Motsvarigheter definierar en uppsättning villkor som utvärderar vilka åtgärder som sker baserat på inkommande sensor för telemetri. Villkor för att fastställa matchningen kan omfatta egenskaper från sensorn, sensorns överordnade enhet och sensorns överordnade utrymme. Villkoren uttrycks som jämförelser mot en [JSON-sökväg](https://jsonpath.com/) som beskrivs i det här exemplet:

- Alla sensorer av data typs **temperatur** som representeras av det undantagna sträng värdet `\"Temperature\"`
- Ha `01` i sin port
- Som tillhör enheter med **tillverkaren** av den utökade egenskaps nyckeln har angetts till det undantagna sträng värdet `\"Contoso\"`
- Som hör till blank steg av den typ som anges av den undantagna strängen `\"Venue\"`
- Som är underordnade för överordnade **SpaceId** `DE8F06CA-1138-4AD7-89F4-F782CC6F69FD`

```JSON
{
  "id": "23535afafd-f39b-46c0-9b0c-0dd3892a1c30",
  "name": "My custom matcher",
  "spaceId": "DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "description": "All sensors of datatype Temperature with 01 in their port that belong to devices with the extended property key Manufacturer set to the value Contoso and that belong to spaces of type Venue that are somewhere below space Id DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "conditions": [
    {
      "id": "43898sg43-e15a-4e9c-abb8-2gw464364",
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    },
    {
      "id": "wt3th44-e15a-35sg-seg3-235wf3ga463",
      "target": "Sensor",
      "path": "$.port",
      "value": "01",
      "comparison": "Contains"
    },
    {
      "id": "735hs33-e15a-37jj-23532-db901d550af5",
      "target": "SensorDevice",
      "path": "$.properties[?(@.name == 'Manufacturer')].value",
      "value": "\"Contoso\"",
      "comparison": "Equals"
    },
    {
      "id": "222325-e15a-49fg-5744-463643644",
      "target": "SensorSpace",
      "path": "$.type",
      "value": "\"Venue\"",
      "comparison": "Equals"
    }
  ]
}
```

> [!IMPORTANT]
> - JSON-sökvägar är Skift läges känsliga.
> - JSON-nyttolasten är samma som den nytto last som returneras av:
>   - `/sensors/{id}?includes=properties,types` för sensorn.
>   - `/devices/{id}?includes=properties,types,sensors,sensorsproperties,sensorstypes` för sensorns överordnade enhet.
>   - `/spaces/{id}?includes=properties,types,location,timezone` för sensorns överordnade utrymme.
> - Jämförelserna är Skift läges okänsliga.

### <a name="user-defined-functions"></a>Användardefinierade funktioner

En användardefinierad funktion är en anpassad funktion som körs i en isolerad Azure digital-miljö. Användardefinierade funktioner har åtkomst till meddelanden om RAW-telemetri när de tas emot. Användardefinierade funktioner har även åtkomst till spatial graf och dispatcher-tjänsten. När den användardefinierade funktionen har registrerats i ett diagram måste en matchandeare (detaljerad beskrivning [ovan](#matchers)) skapas för att ange när funktionen ska köras. Exempel: när Azure Digitals dubbla mottagare tar emot ny telemetri från en viss sensor, kan den matchade användardefinierade funktionen Beräkna ett glidande medelvärde för de senaste sensor avläsningarna.

Användardefinierade funktioner kan skrivas i Java Script. Hjälp metod metoder interagerar med grafen i den användardefinierade körnings miljön. Utvecklare kan köra anpassade kod avsnitt mot meddelanden om sensor telemetri. Exempel på rekommendationer:

- Ange sensorn som läser direkt till objektet sensor i grafen.
- Utför en åtgärd baserat på olika sensor avläsningar inom ett utrymme i grafen.
- Skapa ett meddelande när vissa villkor uppfylls för en inkommande sensor läsning.
- Bifoga metadata till sensorn innan du skickar ut ett meddelande.

Mer information finns i [så här använder du användardefinierade funktioner](./how-to-user-defined-functions.md).

#### <a name="examples"></a>Exempel

[GitHub-lagrings platsen för C# digitala dubbla exempel](https://github.com/Azure-Samples/digital-twins-samples-csharp/) innehåller några exempel på användardefinierade funktioner:
- [Den här funktionen](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) söker efter värden för koldioxid, rörelse och temperatur för att avgöra om ett rum är tillgängligt med dessa värden inom räckhåll. I [självstudierna för digitala dubbla](tutorial-facilities-udf.md) , utforskar du den här funktionen i mer information. 
- [Den här funktionen](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/multiplemotionsensors.js) söker efter data från flera rörelse sensorer och avgör att utrymmet är tillgängligt om ingen av dem identifierar någon rörelse. Du kan enkelt ersätta den användardefinierade funktionen som används i [snabb](quickstart-view-occupancy-dotnet.md)starten eller [självstudierna](tutorial-facilities-setup.md)genom att göra ändringarna som anges i avsnittet kommentarer i filen. 

### <a name="role-assignment"></a>Rolltilldelning

En användardefinierad funktions åtgärder är föremål för Azure Digitals [-rollbaserad åtkomst kontroll](./security-role-based-access-control.md) för att skydda data i tjänsten. Roll tilldelningar definierar vilka användardefinierade funktioner som har rätt behörighet för att interagera med Spatial graf och dess entiteter. En användardefinierad funktion kan till exempel ha behörighet att *skapa*, *läsa*, *Uppdatera*eller *ta bort* diagram data under ett givet utrymme. En användardefinierad funktions nivå för åtkomst kontrol leras när den användardefinierade funktionen ställer grafen för data eller försöker utföra en åtgärd. Mer information finns i [rollbaserad åtkomst kontroll](./security-create-manage-role-assignments.md).

Det är möjligt för en matchning att utlösa en användardefinierad funktion som inte har några roll tilldelningar. I det här fallet kan den användardefinierade funktionen inte läsa några data från grafen.

## <a name="next-steps"></a>Nästa steg

- Om du vill veta mer om hur du dirigerar händelser och telemetri till andra Azure-tjänster kan du läsa [dirigera händelser och meddelanden](./concepts-events-routing.md).

- Om du vill veta mer om hur du skapar motsvarigheter, användardefinierade funktioner och roll tilldelningar läser du [guiden för att använda användardefinierade funktioner](./how-to-user-defined-functions.md).

- Granska den [användardefinierade funktionen klient biblioteks referens dokumentation](./reference-user-defined-functions-client-library.md).
