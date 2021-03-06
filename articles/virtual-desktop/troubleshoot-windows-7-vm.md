---
title: Windows 7 Virtual Machines Windows Virtual Desktop – Azure
description: Så här löser du problem med virtuella Windows 7-datorer (VM) i en Windows Virtual Desktop-miljö.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.author: helohr
ms.openlocfilehash: ee641a694371ffd3fbc691a05edac707e37e959d
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607333"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-windows-virtual-desktop"></a>Felsöka en virtuell Windows 7-dator i Windows Virtual Desktop

Använd den här artikeln för att felsöka problem som du har med när du konfigurerar virtuella Windows-sessioner för fjärrskrivbordssessioner värd för virtuella datorer (VM).

## <a name="known-issues"></a>Kända problem

Windows 7 på virtuella Windows-datorer har inte stöd för följande funktioner:

- Virtualiserade program (RemoteAppar)
- Omdirigering av tidszon
- Automatisk DPI-skalning

Virtuella Windows-datorer kan bara virtualisera fullständiga skriv bord för Windows 7.

Även om automatisk DPI-skalning inte stöds, kan du manuellt ändra lösning på den virtuella datorn genom att högerklicka på ikonen i fjärr skrivbords klienten och välja **lösning**.

## <a name="error-cant-access-the-remote-desktop-user-group"></a>Fel: det går inte att komma åt användar gruppen fjärr skrivbord

Om det inte går att hitta dig eller dina användares autentiseringsuppgifter i det virtuella Windows-skrivbordet kan du se något av följande fel meddelanden:

- "Den här användaren är inte medlem i användar gruppen fjärr skrivbord"
- "Du måste beviljas behörighet att logga in via Fjärrskrivbordstjänster"

Du kan åtgärda det här felet genom att lägga till användaren i användar gruppen fjärr skrivbord:

1. Öppna Azure Portal.
2. Välj den virtuella dator som du såg fel meddelandet på.
3. Välj **kör ett kommando**.
4. Kör följande kommando med `<username>` ersatt med namnet på den användare som du vill lägga till:
   
   ```cmd
   net localgroup "Remote Desktop Users" <username> /add
   ```