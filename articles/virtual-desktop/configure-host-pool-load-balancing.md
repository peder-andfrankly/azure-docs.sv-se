---
title: Konfigurera belastnings utjämning för Windows Virtual Desktop – Azure
description: Så här konfigurerar du belastnings Utjämnings metoden för en Windows Virtual Desktop-miljö.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: 61c11e342f3b4f906b453e0962018a8f8c34acd4
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605877"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>Konfigurera metoden för belastningsutjämning för Windows Virtual Desktop

Genom att konfigurera belastnings Utjämnings metoden för en värddator kan du justera Windows Virtual Desktop-miljön så att den bättre passar dina behov.

>[!NOTE]
> Detta gäller inte för en beständig Skriv bords värd eftersom användarna alltid har en 1:1-mappning till en sessionsnyckel i poolen.

## <a name="configure-breadth-first-load-balancing"></a>Konfigurera bredd – första belastnings utjämning

Bredd – första belastnings utjämning är standard konfigurationen för nya icke-permanent värdbaserade pooler. Bredd – den första belastnings utjämningen distribuerar nya användarsessioner över alla tillgängliga sessionsbaserade värdar i poolen. När du konfigurerar den första belastnings utjämningen kan du ange en högsta sessionsgräns per session-värd i värd gruppen.

Börja med att [Hämta och importera Windows Virtual Desktop PowerShell-modulen](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) som ska användas i PowerShell-sessionen om du inte redan gjort det. Sedan kör du följande cmdlet för att logga in på ditt konto:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Kör följande PowerShell-cmdlet för att konfigurera en adresspool för att utföra bredd-första belastnings utjämning utan att justera den högsta tillåtna sessionsgränsen:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

Kör följande PowerShell-cmdlet för att konfigurera en adresspool för att utföra bredd-första belastnings utjämning och för att använda en ny maxgräns för sessionen:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Konfigurera djup-första belastnings utjämning

Djup – den första belastnings utjämningen distribuerar nya användarsessioner till en tillgänglig sessionsvariabel med det högsta antalet anslutningar men har inte uppnått gränsen för högsta antal sessioner. När du konfigurerar djup-första belastnings utjämning, **måste** du ange en högsta sessionsgräns per session-värd i poolen.

Kör följande PowerShell-cmdlet för att konfigurera en adresspool för att utföra djup-första belastnings utjämning:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```
