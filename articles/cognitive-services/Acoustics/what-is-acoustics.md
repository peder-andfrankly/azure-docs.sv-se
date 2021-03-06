---
title: Översikt över Project-Akustiskhet
titlesuffix: Azure Cognitive Services
description: Project-Akustisker är en akustisk motor för interaktiva 3D-upplevelser, som integrerar bakade Wave fysik-simulering med interaktiva design kontroller.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: overview
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 65678f08399f378b8580eed79e49197dd4d84c64
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351144"
---
# <a name="what-is-project-acoustics"></a>Vad är Project Acoustics?
Project-Akustisker är en våg akustisk motor för interaktiva 3D-upplevelser. Den modellerar våg effekter som ocklusion, hinder, Portal-och Reverberation-effekter i komplexa scener utan att kräva manuell zon markering eller processor intensiv raytracing. Den omfattar också integrering av spel motor och ljud mellan mellanprogram. Project-Akustisker-filosofi liknar statisk belysning: bageri detaljerad fysik offline för att tillhandahålla en fysisk bas linje och Använd en förenklad körning med lättfattliga programspecifika design kontroller för att möta dina konstnärliga mål för de akustiska datorerna i din virtuella värld.

![Skärm bild från växlar av krigs 4 visar akustiska voxels](media/gears-with-voxels.jpg)

## <a name="using-wave-physics-for-interactive-acoustics"></a>Använda Wave fysik för interaktiva akustiska ljud
Raybaserade akustiska metoder kan söka efter ocklusion med en enda källa-till-lyssnande Ray-Cast eller driva reverb genom att uppskatta den lokala scen volymen med några strålar. Men dessa tekniker kan vara otillförlitliga eftersom en Pebble occludes så mycket som en Boulder. Strålar tar inte hänsyn till hur ljudet böjer sig runt objekt, ett fenomen som kallas Diffraction. Simuleringen av Project-Akustisker fångar dessa effekter med en Wave-baserad simulering. De akustiska ljuden är mer förutsägbara, korrekta och sömlösa.

Project-akustiska nyckel innovationer är att skapa en Real Sound Wave-baserad akustisk-simulering med traditionella ljud design koncept. Den översätter simulerings resultat till traditionella ljud-DSP-parametrar för ocklusion, portaling och reverb. Designern använder kontroller över den här översättnings processen. Mer information om kärn teknikerna bakom projekt akustiskt finns på sidan om [forsknings projekt](https://www.microsoft.com/en-us/research/project/project-triton/).

![Animering som visar ett vågrätt 2D-segment av våg-spridningen via en scen](media/wave-simulation.gif)

## <a name="video-presentation-from-gdc-2019-30-min"></a>Video presentation från GDC 2019 (~ 30 min)
[Video om ![Project akustiskt](https://img.youtube.com/vi/uY4G-GUAQIE/0.jpg)](https://www.youtube.com/watch?v=uY4G-GUAQIE "Klicka för att spela upp video")

## <a name="setup"></a>Konfiguration
[Project Akustiske Uniting-integrering](unity-integration.md) är dra och släpp och innehåller ett enhets ljud motor plugin-program. Utöka enhetens bild käll kontroller genom att koppla ett projekt akustiskt C# kontroll komponent till varje ljud objekt.

[Project Akustiske Unreal-integrering](unreal-integration.md) innehåller redigerings-och spel-plugin-program för Unreal och ett Wwise mixer-pluginprogram. En anpassad ljud komponent utökar bekanta Wwise-funktioner i Unreal med direkt akustiska design kontroller. Design kontroller visas också i Wwise i mixer-plugin-programmet.

## <a name="workflow"></a>Arbetsflöde
* **Före bageri:** Börja med att konfigurera bagerien genom att välja vilken geometri som svarar på akustisker, t. ex. ignorerar ljusa axlar. Redigera sedan automatiska material tilldelningar och välj navigerings områden för att vägleda lyssnings sampling. Det finns ingen manuell markering för reverb/Portal/Room-zoner.
* **Skapa** Ett analys steg körs lokalt, som voxelization och annan geometrisk analys på scenen baserat på valen ovan. Resultaten visualiseras i redigerings programmet för att verifiera scen inställningarna. Vid insändningen skickas Voxel data till Azure och du får tillbaka en akustisk spel till gång.
* **Flöde** Läs in till gången till din nivå och du är redo att lyssna på akustiska på din nivå. Utforma de akustiska ljuden i redigeraren med detaljerade kontroller per källa. Kontrollerna kan också köras från nivå skript.

## <a name="runtime-platforms"></a>Kör tids plattformar
Plugin-program för Project Akustiske runtime kan för närvarande distribueras till följande plattformar:
* Windows
* MacOS
* Android
* Xbox One

## <a name="editor-platforms"></a>Redaktörs plattformar
Plugin-programmet för Project Akustiske Editor är tillgängligt för följande plattformar:
* Windows
* MacOS (endast Unity)

## <a name="download"></a>Ladda ned
* [Plugin-program och exempel för Project Akustiske Uniting](https://www.microsoft.com/en-us/download/details.aspx?id=57346)
* [Projekt akustiska Unreal & Wwise-plugin-program och exempel](https://www.microsoft.com/download/details.aspx?id=58090)
  * Kontakta oss via [forumet](https://github.com/microsoft/ProjectAcoustics/issues)för binärfiler i Xbox och annan support.

## <a name="contact-us"></a>Kontakta oss
* [Projekt-akustiskt diskussion och ärende rapportering](https://github.com/microsoft/ProjectAcoustics/issues)

## <a name="next-steps"></a>Nästa steg
* Prova ett [projekt akustiskt snabb start för](unity-quickstart.md) enhets-eller [Unreal](unreal-quickstart.md)
* Utforska [ljud design filosofin om projekt akustiska data](design-process.md)

