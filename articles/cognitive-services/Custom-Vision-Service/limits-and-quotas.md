---
title: Gränser och kvoter-Custom Vision Service
titleSuffix: Azure Cognitive Services
description: I den här artikeln beskrivs de olika typerna av licensierings nycklar och begränsningar och kvoter för Custom Vision Service.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: anroth
ms.openlocfilehash: e4fc3555d7d880dcbd4f76d8f507300e98c237db
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718365"
---
# <a name="limits-and-quotas"></a>Begränsningar och kvoter

Det finns två nivåer av nycklar för Custom Visions tjänsten. Du kan registrera dig för en F0 (kostnads fri) eller S0-prenumeration (standard) via Azure Portal. Se motsvarande [Cognitive Services prissättnings sida](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) för information om priser och transaktioner.

Antalet utbildnings bilder per projekt och taggar per projekt förväntas öka med tiden för S0-projekt.

||**F0**|**S0**|
|-----|-----|-----|
|samarbetsprojekt|2|100|
|Utbildnings bilder per projekt |5 000|100 000|
|Förutsägelser/månad|10 000 |Obegränsat|
|Taggar/projekt|50|500|
|Iterationer |10|10|
|Minsta antal märkta bilder per tagg, klassificering (50 + rekommenderas) |5|5|
|Minsta antal märkta bilder per tagg, objekt identifiering (50 + rekommenderas)|15|15|
|Hur länge förutsägelse bilder lagras|30 dagar|30 dagar|
|[Förutsägelse](https://go.microsoft.com/fwlink/?linkid=865445) åtgärder med lagring (transaktioner per sekund)|2|10|
|[Förutsägelse](https://go.microsoft.com/fwlink/?linkid=865445) åtgärder utan lagring (transaktioner per sekund)|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (API-anrop per sekund)|2|10|
|[Andra API-anrop](https://go.microsoft.com/fwlink/?linkid=865446) (transaktioner per sekund)|10|10|
|Godkända avbildnings typer|jpg, PNG, BMP, GIF|jpg, PNG, BMP, GIF|
|Minsta bild höjd/bredd i bild punkter|256 (se anmärkning)|256 (se anmärkning)|
|Maximal bild höjd/bredd i bild punkter|obegränsat|obegränsat|
|Maximal bild storlek (uppladdning av inlärnings avbildning) |6 MB|6 MB|
|Maximal bild storlek (förutsägelse)|4 MB|4 MB|
|Bild för högsta antal regioner per objekts avkänning|200|200|
|Bild av Max Taggar per klassificering|30|30|

> [!NOTE]
> Bilder som är mindre än 256 bild punkter godkänns, men skalas upp.
