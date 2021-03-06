---
title: Säkerhets rekommendationer för Azure Marketplace-avbildningar | Microsoft Docs
description: Den här artikeln innehåller rekommendationer för avbildningar som ingår på marknads platsen
services: security
documentationcenter: na
author: barclayn
manager: barbkess
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: barclayn
ms.openlocfilehash: b82cf957f4bd74cb2c63bfd5a7fe73899b395df6
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795814"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Säkerhets rekommendationer för Azure Marketplace-avbildningar

Din avbildning måste uppfylla dessa rekommendationer för säkerhets konfiguration. Detta hjälper till att upprätthålla en hög säkerhets nivå för partner lösnings avbildningar på Azure Marketplace.

Kör alltid en säkerhets sårbarhets identifiering på avbildningen innan du skickar. Om du upptäcker en säkerhets risk i en egen publicerad avbildning måste du informera dina kunder i rätt tid av både säkerhets problemet och hur du korrigerar det.

## <a name="open-source-based-images"></a>Öppna källbaserade avbildningar

|||
|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Kategori**                                                 | **Utcheckning**                                                                                                                                                                                                                                                                              |
| Säkerhet                                                     | Installera alla de senaste säkerhets korrigeringarna för Linux-distributionen.                                                                                                                                                                                                              |
| Säkerhet                                                     | Följ rikt linjerna för att skydda den virtuella dator avbildningen för den aktuella Linux-distributionen.                                                                                                                                                                                     |
| Säkerhet                                                     | Begränsa angrepps ytan genom att behålla minimalt utrymme med endast nödvändiga Windows Server-roller, funktioner, tjänster och nätverks portar.                                                                                                                                               |
| Säkerhet                                                     | Skanna käll koden och den resulterande VM-avbildningen för skadlig kod.                                                                                                                                                                                                                                   |
| Säkerhet                                                     | VHD-avbildningen innehåller bara nödvändiga låsta konton som inte har standard lösen ord som tillåter interaktiv inloggning. inga back dörrar.                                                                                                                                           |
| Säkerhet                                                     | Inaktivera brand Väggs regler om inte programmet använder dem i funktion, till exempel en brand Väggs enhet.                                                                                                                                                                             |
| Säkerhet                                                     | Ta bort all känslig information från VHD-avbildningen, till exempel testa SSH-nycklar, kända värdar-fil, loggfiler och onödiga certifikat.                                                                                                                                       |
| Säkerhet                                                     | Undvik att använda LVM.                                                                                                                                                                                                                                            |
| Säkerhet                                                     | Ta med de senaste versionerna av de nödvändiga biblioteken: </br> -OpenSSL v 1.0 eller senare </br> – Python 2,5 eller senare (python 2.6 + är starkt rekommenderat) </br> – Python pyasn1-paket om det inte redan har installerats </br> -d. OpenSSL v 1,0 eller senare                                                                |
| Säkerhet                                                     | Rensa poster i bash/shell-historik.                                                                                                                                                                                                                                             |
| Nätverk                                                   | Inkludera SSH-servern som standard. Ange SSH Keep Alive till sshd-konfigurationen med följande alternativ: ClientAliveInterval 180.                                                                                                                                                        |
| Nätverk                                                   | Ta bort eventuell anpassad nätverks konfiguration från avbildningen. Ta bort kommandot matcha. conf: `rm /etc/resolv.conf`.                                                                                                                                                                                |
| Distribution                                                   | Installera den senaste Azure Linux-agenten.</br> – Installera med hjälp av RPM-eller deb-paketet.  </br> -Du kan också använda processen för manuell installation, men installations paketen rekommenderas och föredras. </br> – Om du installerar agenten manuellt från GitHub-lagringsplatsen måste du först kopiera `waagent`-filen till `/usr/sbin` och köra (som rot): </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>Agent konfigurations filen placeras på `/etc/waagent.conf`. |
| Distribution                                                   | Se till att Azure-supporten kan ge våra partners till gång till serie konsolens utdata vid behov och ange lämplig tids gräns för montering av OS-diskar från moln lagring Lägg till följande parametrar i avbildnings start raden för kernel: `console=ttyS0 earlyprintk=ttyS0 rootdelay=300`. |
| Distribution                                                   | Ingen växlings-partition på OS-disken. Du kan begära växling för att skapa en lokal resurs disk av Linux-agenten.         |
| Distribution                                                   | Skapa en enskild rotpartition för OS-disken.      |
| Distribution                                                   | endast 64-bitars operativ system.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Windows Server-baserade avbildningar

|||
|-------------| -------------------------|
| **Kategori**                                                     | **Utcheckning**                                                                                                                                                                |
| Säkerhet                                                         | Använd en säker OS-avbildning. Den virtuella hård disk som används för källan till alla avbildningar som baseras på Windows Server måste vara från Windows Server OS-avbildningarna som tillhandahålls via Microsoft Azure. |
| Säkerhet                                                         | Installera alla de senaste säkerhets uppdateringarna.                                                                                                                                     |
| Säkerhet                                                         | Program bör inte vara beroende av begränsade användar namn som administratör, rot eller administratör.                                                                |
| Säkerhet                                                         | Aktivera BitLocker-diskkryptering för både OS-hårddiskar och data hård diskar.                                                             |
| Säkerhet                                                         | Begränsa angrepps ytan genom att behålla minimalt utrymme med endast nödvändiga Windows Server roller, funktioner, tjänster och nätverks portar aktiverade.                         |
| Säkerhet                                                         | Skanna käll koden och den resulterande VM-avbildningen för skadlig kod.                                                                                                                     |
| Säkerhet                                                         | Ange Windows Server-avbildningar säkerhets uppdatering för automatisk uppdatering.                                                                                                                |
| Säkerhet                                                         | VHD-avbildningen innehåller bara nödvändiga låsta konton som inte har standard lösen ord som tillåter interaktiv inloggning. inga back dörrar.                             |
| Säkerhet                                                         | Inaktivera brand Väggs regler om inte programmet använder dem i funktion, till exempel en brand Väggs enhet.                                                               |
| Säkerhet                                                         | Ta bort all känslig information från VHD-avbildningen, inklusive HOSTs-filer, loggfiler och onödiga certifikat.                                              |
| Distribution                                                       | endast 64-bitars operativ system.                            |

Även om din organisation inte har avbildningar på Azure Marketplace bör du överväga att kontrol lera dina Windows-och Linux-bildkonfigurationer mot dessa rekommendationer.

## <a name="contacting-customers"></a>Kontakta kunder

Så här identifierar du kunder och deras kontakt-e-post:

1.  I Cloud Partner Portal, på vänster järnväg, väljer du **insikter**.
2.  På fliken **order och användning** använder du fälten **start datum** och **slutdatum** för att fråga användningen inom det datum intervall som krävs. Detta visar vilka Azure-prenumerationer som användes för erbjudandet varje dag. Exportera dessa data. 
3.  På samma sätt går du till fliken **kund** och frågar och exporterar din kund bas.
4.  Matcha prenumerations-ID: t från steg 2 till prenumerations-ID: t från steg 3 för att hitta nödvändig kund information.
