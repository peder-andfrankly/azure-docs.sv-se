---
title: Felsöka ett allmänt RDP-fel till en virtuell Windows-dator i Azure | Microsoft Docs
description: Lär dig hur du felsöker ett RDP-allmänt fel till en virtuell Windows-dator i Azure | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 7fc0fbf3362d18284ad6a80afa6396b6be1270a9
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058002"
---
# <a name="troubleshoot-an-rdp-general-error-in-azure-vm"></a>Felsöka ett RDP-allmänt fel i virtuell Azure-dator

I den här artikeln beskrivs ett allmänt fel som kan uppstå när du skapar en Remote Desktop Protocol-anslutning (RDP) till en virtuell Windows-dator (VM) i Azure.

## <a name="symptom"></a>Symtom

När du gör en RDP-anslutning till en virtuell Windows-dator i Azure kan du få följande allmänna fel meddelande:

**Fjärrskrivbord kan inte ansluta till fjärrdatorn för något av följande skäl:**

1. **Fjärråtkomst till servern är inte aktiverat**

2. **Fjärrdatorn är avstängd**

3. **Fjärrdatorn är inte tillgänglig i nätverket**

**Kontrollera att fjärrdatorn är påslagen och ansluten till nätverket och att fjärråtkomst har aktiverats.**

## <a name="cause"></a>Orsak

Det här problemet kan bero på följande orsaker:

### <a name="cause-1"></a>Orsak 1

RDP-komponenten är inaktive rad på följande sätt:

- På komponent nivå
- På lyssnar nivån
- På Terminal-servern
- På värd rollen värd för fjärrskrivbordssession

### <a name="cause-2"></a>Orsak 2

Fjärrskrivbordstjänster (TermService) körs inte.

### <a name="cause-3"></a>Orsak 3

RDP-lyssnaren är felkonfigurerad.

## <a name="solution"></a>Lösning

Lös problemet genom att [säkerhetskopiera operativ system disken](../windows/snapshot-copy-managed-disk.md)och [koppla operativ system disken till en räddnings dator](troubleshoot-recovery-disks-portal-windows.md)och följ sedan stegen.

### <a name="serial-console"></a>Seriekonsol

#### <a name="step-1-open-cmd-instance-in-serial-console"></a>Steg 1: Öppna CMD-instans i Seriell konsol

1. Öppna [serie konsolen](serial-console-windows.md) genom att välja **Support & fel söknings** > **seriell konsol (för hands version)** . Om funktionen är aktive rad på den virtuella datorn kan du ansluta den virtuella datorn.

2. Skapa en ny kanal för en CMD-instans. Skriv **cmd** för att starta kanalen för att hämta kanal namnet.

3. Växla till kanalen som kör CMD-instansen, i det här fallet ska den vara kanal 1.

   ```
   ch -si 1
   ```

#### <a name="step-2-check-the-values-of-rdp-registry-keys"></a>Steg 2: Kontrol lera värdena för RDP-register nycklar:

1. Kontrol lera om RDP har inaktiverats av principer.

      ```
      REM Get the local policy 
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server " /v fDenyTSConnections

      REM Get the domain policy if any
      reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections
      ```

      - Om domän principen finns skrivs inställningen i den lokala principen över.
      - Om domän principen anger att RDP är inaktive rad (1) uppdaterar du AD-principen från domänkontrollanten.
      - Om domän principen anger att RDP är aktiverat (0) behövs ingen uppdatering.
      - Om domän principen inte finns och den lokala principen anger att RDP är inaktive rad (1) aktiverar du RDP genom att använda följande kommando: 
      
            reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
                  

2. Kontrol lera den aktuella konfigurationen av Terminal-servern.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled
      ```

      Om kommandot returnerar 0 inaktive ras Terminal-servern. Aktivera sedan Terminal Server på följande sätt:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f
      ```

3. Terminal Server modulen är inställd på dränerings läge om servern finns i en Terminal Server-servergrupp (RDS eller Citrix). Kontrol lera det aktuella läget för Terminal Server-modulen.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode
      ```

      Om kommandot returnerar 1 är Terminal Server-modulen inställd på dränerings läge. Ange sedan modulen till arbets läge enligt följande:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f
      ```

4. Kontrol lera om du kan ansluta till Terminal-servern.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled
      ```

      Om kommandot returnerar 1 kan du inte ansluta till Terminal-servern. Aktivera sedan anslutningen enligt följande:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f
      ```
5. Kontrol lera den aktuella konfigurationen av RDP-lyssnaren.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation
      ```

      Om kommandot returnerar 0 inaktive ras RDP-lyssnaren. Aktivera sedan lyssnaren enligt följande:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f
      ```

6. Kontrol lera om du kan ansluta till RDP-lyssnaren.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled
      ```

   Om kommandot returnerar 1 kan du inte ansluta till RDP-lyssnaren. Aktivera sedan anslutningen enligt följande:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f
      ```

7. Starta om den virtuella datorn.

8. Stäng från cmd-instansen `exit`genom att skriva, och tryck sedan på **RETUR** två gånger.

9. Starta om den virtuella datorn `restart`genom att skriva och sedan ansluta till den virtuella datorn.

Om problemet fortfarande uppstår går du vidare till steg 2.

#### <a name="step-2-enable-remote-desktop-services"></a>Steg 2: Aktivera fjärr skrivbords tjänster

Mer information finns i [Fjärrskrivbordstjänster som inte startar på en virtuell Azure-dator](troubleshoot-remote-desktop-services-issues.md).

#### <a name="step-3-reset-rdp-listener"></a>Steg 3: Återställ RDP-lyssnare

Mer information finns i [fjärr skrivbord från koppling ofta i virtuella Azure-datorer](troubleshoot-rdp-intermittent-connectivity.md).

### <a name="offline-repair"></a>Offline-reparation

#### <a name="step-1-turn-on-remote-desktop"></a>Steg 1: Aktivera fjärr skrivbord

1. [Koppla OS-disk till virtuell återställningsdator](../windows/troubleshoot-recovery-disks-portal.md).
2. Starta en fjärrskrivbordsanslutning till den Virtuella återställningsdatorn.
3. Kontrollera att disken flaggas som **Online** i konsolen Diskhantering. Observera den enhetsbeteckning som är tilldelad till den anslutna OS-disken.
4. Starta en fjärrskrivbordsanslutning till den Virtuella återställningsdatorn.
5. Öppna en upphöjd kommandotolk-session (**kör som administratör**). Kör följande skript. I det här skriptet förutsätter vi att den enhetsbeteckning som är tilldelad till den anslutna OS-disken är F. Ersätt enhetsbeteckningen med lämpligt värde för den virtuella datorn.

      ```
      reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv 
      reg load HKLM\BROKENSOFTWARE F:\windows\system32\config\SOFTWARE.hiv 
 
      REM Ensure that Terminal Server is enabled 

      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f 

      REM Ensure Terminal Service is not set to Drain mode 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f 

      REM Ensure Terminal Service has logon enabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f 

      REM Ensure the RDP Listener is not disabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f 

      REM Ensure the RDP Listener accepts logons 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f 

      REM RDP component is enabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0 /f 

      reg unload HKLM\BROKENSYSTEM 
      reg unload HKLM\BROKENSOFTWARE 
      ```

6. Om den virtuella datorn är domänansluten kontrollerar du följande register nyckel för att se om det finns en grup princip som inaktiverar RDP. 

      ```
      HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services\fDenyTSConnectionS
      ```

      Om det här nyckelvärdet är inställt på 1 betyder det att RDP har inaktiverats av principen. Om du vill aktivera fjärr skrivbord via GPO-principen ändrar du följande princip från domänkontrollanten:

   
      **Datorkonfiguration\principer\administrativa-mallar för datorer:**

      Princip definitions\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Connections\Allow användare kan fjärrans luta via Fjärrskrivbordstjänster
  
1. Koppla bort disken från den virtuella datorn för räddning.
1. [Skapa en ny virtuell dator från disken](../windows/create-vm-specialized.md).

Om problemet fortfarande uppstår går du vidare till steg 2.

#### <a name="step-2-enable-remote-desktop-services"></a>Steg 2: Aktivera fjärr skrivbords tjänster

Mer information finns i [Fjärrskrivbordstjänster som inte startar på en virtuell Azure-dator](troubleshoot-remote-desktop-services-issues.md).

#### <a name="step-3-reset-rdp-listener"></a>Steg 3: Återställ RDP-lyssnare

Mer information finns i [fjärr skrivbord från koppling ofta i virtuella Azure-datorer](troubleshoot-rdp-intermittent-connectivity.md).

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du fortfarande behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) så får du hjälp att lösa problemet snabbt.
