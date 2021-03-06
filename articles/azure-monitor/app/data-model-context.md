---
title: Azure Application insikter telemetri data modell – telemetri-kontext | Microsoft Docs
description: Application Insights telemetri kontext data modell
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/15/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 0351f4fe65745242da58d3c3fb2f9fbe5c722d06
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677444"
---
# <a name="telemetry-context-application-insights-data-model"></a>Telemetri-kontext: Application Insights data modell

Varje telemetri-objekt kan ha ett starkt angivet Sammanhangs fält. Varje fält aktiverar ett bestämt övervaknings scenario. Använd samlingen anpassade egenskaper för att lagra anpassad eller programspecifik kontext information.


## <a name="application-version"></a>Program version

Information i program kontexts fälten är alltid om det program som skickar Telemetrin. Program versionen används för att analysera trend ändringar i programmets beteende och dess korrelation till distributionerna.

Max längd: 1024


## <a name="client-ip-address"></a>Klientens IP-adress

IP-adressen för klient enheten. IPv4 och IPv6 stöds. När telemetri skickas från en tjänst är plats kontexten för den användare som initierade åtgärden i tjänsten. Application Insights extrahera information om geo-platsen från klientens IP-adress och sedan trunkera den. Klientens IP-adress kan inte användas som identifierbar information för slutanvändare. 

Max längd: 46


## <a name="device-type"></a>Enhets typ

Ursprungligen användes det här fältet för att ange vilken typ av enhet som slutanvändaren använder. Används idag främst för att skilja på JavaScript-telemetri med enhets typen Browser från telemetri på Server sidan med enhets typen PC.

Max längd: 64


## <a name="operation-id"></a>Åtgärds-ID

En unik identifierare för rot åtgärden. Den här identifieraren gör det möjligt att gruppera telemetri över flera komponenter. Se [korrelation för telemetri](../../azure-monitor/app/correlation.md) för mer information. Åtgärds-ID: t skapas antingen i en begäran eller i en sid visning. All annan telemetri anger det här fältet till värdet för den som innehåller begäran eller sid visning. 

Max längd: 128


## <a name="parent-operation-id"></a>ID för överordnad åtgärd

Den unika identifieraren för telemetri-objektets direkta överordnade objekt. Se [korrelation för telemetri](../../azure-monitor/app/correlation.md) för mer information.

Max längd: 128


## <a name="operation-name"></a>Åtgärds namn

Åtgärdens namn (grupp). Åtgärds namnet skapas antingen av en begäran eller en sidvy. Alla andra telemetridata ställer in det här fältet till värdet för den som innehåller begäran eller sid visningen. Åtgärds namnet används för att hitta alla telemetri-objekt för en grupp med åtgärder (till exempel "GET Home/index"). Den här kontext egenskapen används för att besvara frågor som "vilka är de vanligaste undantagen som utlösts på den här sidan".

Max längd: 1024


## <a name="synthetic-source-of-the-operation"></a>Åtgärdens syntetiska källa

Namn på syntetisk källa. Vissa telemetri från programmet kan representera syntetisk trafik. Det kan vara webbcrawler som indexera webbplatsen, test av plats tillgänglighet eller spår från diagnostiska bibliotek som Application Insights SDK.

Max längd: 1024


## <a name="session-id"></a>Sessions-ID

Sessions-ID – instansen för användarens interaktion med appen. Informationen i fälten för sessions kontexten är alltid om slutanvändaren. När telemetri skickas från en tjänst är det den användare som initierade åtgärden i-sessionen.

Max längd: 64


## <a name="anonymous-user-id"></a>Anonymt användar-ID

Anonymt användar-ID. Representerar slutanvändaren för programmet. När telemetri skickas från en tjänst är användar kontexten för den användare som initierade åtgärden i tjänsten.

[Sampling](../../azure-monitor/app/sampling.md) är en av metoderna för att minimera mängden insamlad telemetri. Samplings algoritmen försöker antingen ta ett prov i eller ut hela den korrelerade Telemetrin. Anonymt användar-ID används för generering av samplings poäng. Anonymt användar-ID bör vara ett slumpmässigt tillräckligt värde. 

Att använda anonymt användar-ID för att lagra användar namn är ett missbruk av fältet. Använd autentiserat användar-ID.

Max längd: 128


## <a name="authenticated-user-id"></a>Autentiserat användar-ID

Autentiserat användar-ID. Motsatsen till anonymt användar-ID, det här fältet representerar användaren med ett eget namn. Eftersom dess PII-information inte samlas in som standard av de flesta SDK: er.

Max längd: 1024


## <a name="account-id"></a>Konto-ID

I program med flera klient organisationer är detta det konto-ID eller namn som användaren agerar med. Exempel kan vara prenumerations-ID för Azure Portal eller blogg plattform för blogg namn.

Max längd: 1024


## <a name="cloud-role"></a>Moln roll

Namnet på rollen som programmet ingår i. Mappar direkt till roll namnet i Azure. Kan också användas för att särskilja Micro-tjänster, som ingår i ett enda program.

Max längd: 256


## <a name="cloud-role-instance"></a>Moln roll instans

Namnet på den instans där programmet körs. Dator namn för lokalt instans namn för Azure.

Max längd: 256


## <a name="internal-sdk-version"></a>Internt: SDK-version

SDK-version. Se https://github.com/Microsoft/ApplicationInsights-Home/blob/master/SDK-AUTHORING.md#sdk-version-specification för information.

Max längd: 64


## <a name="internal-node-name"></a>Internt: nodnamn

Det här fältet representerar det nodnamn som används för fakturerings syfte. Använd den för att åsidosätta standard identifiering av noder.

Max längd: 256


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [utökar och filtrerar telemetri](../../azure-monitor/app/api-filtering-sampling.md).
- Se [data modell](data-model.md) för Application Insights typer och data modell.
- Kolla i [konfiguration](../../azure-monitor/app/configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet)av standard kontext egenskaper samling.
