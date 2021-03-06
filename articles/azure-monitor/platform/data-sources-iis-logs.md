---
title: IIS-loggar i Azure Monitor | Microsoft Docs
description: Internet Information Services (IIS) lagrar användar aktivitet i loggfiler som kan samlas in av Azure Monitor.  Den här artikeln beskriver hur du konfigurerar insamling av IIS-loggar och information om de poster som de skapar i Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: a865f43585ccbb31569e2ca0987aae62a89a9281
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932491"
---
# <a name="collect-iis-logs-in-azure-monitor"></a>Samla in IIS-loggar i Azure Monitor
Internet Information Services (IIS) lagrar användar aktivitet i loggfiler som kan samlas in av Azure Monitor och lagras som [loggdata](data-platform.md).

![IIS-loggar](media/data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Konfigurera IIS-loggar
Azure Monitor samlar in poster från loggfiler som skapats av IIS, så du måste [Konfigurera IIS för loggning](https://technet.microsoft.com/library/hh831775.aspx).

Azure Monitor stöder endast IIS-loggfiler som lagras i W3C-format och inte stöder anpassade fält eller avancerad IIS-loggning. De samlar inte in loggar i NCSA eller IIS-ursprungligt format.

Konfigurera IIS-loggar i Azure Monitor från [menyn Avancerade inställningar](agent-data-sources.md#configuring-data-sources).  Ingen konfiguration krävs förutom att välja **samla in W3C-format IIS-loggfiler**.


## <a name="data-collection"></a>Datainsamling
Azure Monitor samlar in IIS-loggfiler från varje agent varje gången loggens tidsstämpel ändras. Loggen läses var **5: e minut**. Om IIS inte uppdaterar tidsstämpeln före förnyelse tiden när en ny fil skapas, kommer posterna att samlas in när den nya filen skapas. Frekvensen för att skapa nya filer styrs av inställningen för **förnyelse av logg filen** för IIS-platsen, vilket är en gång om dagen som standard. Om inställningen anges **per timme**Azure Monitors loggen varje timme. Om inställningen är **dagligen**samlar Azure monitor in loggen var 24: e timme.


## <a name="iis-log-record-properties"></a>Egenskaper för logg poster i IIS
Poster i IIS-loggen har en typ av **W3CIISLog** och har egenskaperna i följande tabell:

| Egenskap | Beskrivning |
|:--- |:--- |
| Dator |Namnet på datorn som händelsen samlades in från. |
| cIP |Klientens IP-adress. |
| csMethod |Metod för begäran, till exempel GET eller POST. |
| csReferer |Platsen som användaren följt av en länk från till den aktuella platsen. |
| csUserAgent |Webbläsarens typ av klient. |
| csUserName |Namnet på den autentiserade användare som har åtkomst till servern. Anonyma användare anges med ett bindestreck. |
| csUriStem |Målet för begäran, till exempel en webb sida. |
| csUriQuery |Fråga, om det finns, som klienten försökte utföra. |
| ManagementGroupName |Namnet på hanterings gruppen för Operations Managers agenter.  För andra agenter är detta AOI-\<arbetsyte-ID\> |
| RemoteIPCountry |Land/region för klientens IP-adress. |
| RemoteIPLatitude |Latitud för klientens IP-adress. |
| RemoteIPLongitude |Longitud för klientens IP-adress. |
| scStatus |HTTP-statuskod. |
| scSubStatus |Felkod för under status. |
| scWin32Status |Windows-statuskod. |
| sIP |IP-adressen för webb servern. |
| SourceSystem |OpsMgr |
| sPort |Port på den server som klienten är ansluten till. |
| sSiteName |Namnet på IIS-webbplatsen. |
| TimeGenerated |Datum och tid då posten loggades. |
| timeTaken |Tids längd för att bearbeta begäran i millisekunder. |

## <a name="log-queries-with-iis-logs"></a>Logga frågor med IIS-loggar
Följande tabell innehåller olika exempel på logg frågor som hämtar poster i IIS-loggen.

| Söka i data | Beskrivning |
|:--- |:--- |
| W3CIISLog |Alla logg poster i IIS. |
| W3CIISLog &#124; där scStatus = = 500 |Alla poster i IIS-loggen med retur status 500. |
| W3CIISLog &#124; summerings antal () efter cIP |Antal IIS-loggfiler efter klient-IP-adress. |
| W3CIISLog &#124; där csHost = = "www\.contoso.com" &#124; sammanfatta antal () av csUriStem |Antal IIS-loggfiler efter URL för värd-www-\.contoso.com. |
| W3CIISLog &#124; sammanfatta sum (csBytes) per dator &#124; tar 500000 |Totalt antal byte som tagits emot av varje IIS-dator. |

## <a name="next-steps"></a>Nästa steg
* Konfigurera Azure Monitor för att samla in andra [data källor](agent-data-sources.md) för analys.
* Lär dig mer om [logg frågor](../log-query/log-query-overview.md) för att analysera data som samlas in från data källor och lösningar.
