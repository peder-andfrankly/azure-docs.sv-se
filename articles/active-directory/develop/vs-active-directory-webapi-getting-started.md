---
title: Kom igång med Azure AD i Visual Studio WebApi-projekt
description: Komma igång med att använda Azure Active Directory i WebApi-projekt efter anslutning till eller skapa en Azure AD med hjälp av Visual Studio Connected Services
author: ghogen
manager: jillfra
ms.assetid: bf1eb32d-25cd-4abf-8679-2ead299fedaa
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: 225940052e1f40f8050059532e4657df2b5410a3
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68851906"
---
# <a name="get-started-with-azure-active-directory-webapi-projects"></a>Kom igång med Azure Active Directory (WebApi-projekt)

> [!div class="op_single_selector"]
> - [Komma igång](vs-active-directory-webapi-getting-started.md)
> - [Vad hände](vs-active-directory-webapi-what-happened.md)

Den här artikeln ger ytterligare vägledning när du har lagt till Active Directory i ett ASP.NET WebAPI-projekt med kommandot **project > Connected Services** i Visual Studio. Om du inte redan har lagt till tjänsten i projektet kan du göra det när som helst.

Se [vad som hände med mitt WebAPI-projekt?](vs-active-directory-webapi-what-happened.md) för de ändringar som gjorts i projektet när du lade till den anslutna tjänsten.

## <a name="requiring-authentication-to-access-controllers"></a>Kräver autentisering för åtkomst till kontrollanter

Alla kontrollanter i projektet har dekorationer med `[Authorize]` attributet. Det här attributet kräver att användaren autentiseras innan de får åtkomst till de API: er som definierats av dessa styrenheter. Om du vill tillåta att styrenheten används anonymt tar du bort attributet från kontroll enheten. Om du vill ange behörigheterna på en mer detaljerad nivå använder du attributet för varje metod som kräver auktorisering i stället för att använda den i kontroll enhets klassen.

## <a name="next-steps"></a>Nästa steg

- [Autentiserings scenarier för Azure Active Directory](authentication-scenarios.md)
- [Lägg till inloggning med Microsoft i en ASP.NET-webbapp](quickstart-v1-aspnet-webapp.md)
