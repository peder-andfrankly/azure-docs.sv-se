---
title: Konfigurera GPU för Windows Virtual Desktop – Azure
description: Aktivera GPU-accelererad åter givning och kodning i Windows Virtual Desktop.
services: virtual-desktop
author: gundarev
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: a0965dc4011b449e617f6dbaeafb68bfa796b620
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953955"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop"></a>Konfigurera GPU-acceleration (Graphics Processing Unit) för Windows Virtual Desktop

Windows Virtual Desktop stöder GPU-accelererad åter givning och kodning för bättre prestanda och skalbarhet för appar. GPU-acceleration är särskilt viktigt för grafik intensiva appar.

Följ anvisningarna i den här artikeln för att skapa en GPU-optimerad virtuell Azure-dator, lägga till den i din värd pool och konfigurera den för att använda GPU-acceleration för åter givning och kodning. Den här artikeln förutsätter att du redan har konfigurerat en Windows-klient för virtuella skriv bord.

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>Välj en GPU-optimerad storlek på virtuell Azure-dator

Azure erbjuder ett antal [GPU-optimerade storlekar på virtuella datorer](/azure/virtual-machines/windows/sizes-gpu). Det rätta valet för din värd pool är beroende av ett antal faktorer, inklusive dina specifika arbets belastningar för appar, önskad kvalitet på användar upplevelsen och kostnad. I allmänhet erbjuder större och mer kompatibla GPU: er en bättre användar upplevelse vid en specifik användar täthet.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>Skapa en värdbaserad pool, etablera den virtuella datorn och konfigurera en app-grupp

Skapa en ny adresspool med en virtuell dator med den storlek som du har valt. Mer information finns i [Självstudier: skapa en värdbaserad pool med Azure Marketplace](/azure/virtual-desktop/create-host-pools-azure-marketplace).

Windows Virtual Desktop stöder GPU-accelererad åter givning och kodning i följande operativ system:

* Windows 10 version 1511 eller senare
* Windows Server 2016 eller senare

Du måste också konfigurera en app-grupp eller använda standard gruppen för Skriv bords appar (med namnet "Skriv bords grupp") som skapas automatiskt när du skapar en ny adresspool. Mer information finns i [Självstudier: hantera program grupper för Windows Virtual Desktop](/azure/virtual-desktop/manage-app-groups).

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>Installera grafik driv rutiner som stöds på den virtuella datorn

Om du vill dra nytta av GPU-funktionerna i virtuella datorer i Azure N-serien i Windows Virtual Desktop måste du installera NVIDIA Graphics-drivrutiner. Följ anvisningarna på [Installera nVidia GPU-drivrutiner på virtuella datorer i N-serien som kör Windows](/azure/virtual-machines/windows/n-series-driver-setup) för att installera driv rutiner, antingen manuellt eller med hjälp av [nVidia GPU-drivrutinen](/azure/virtual-machines/extensions/hpccompute-gpu-windows).

Observera att endast [driv rutiner för NVIDIA-rutnät](/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers) som distribueras med Azure stöds för virtuella Windows-datorer.

Efter installationen av driv rutinen krävs en omstart av datorn. Använd verifierings stegen i ovanstående instruktioner för att bekräfta att grafik driv rutinerna har installerats.

## <a name="configure-gpu-accelerated-app-rendering"></a>Konfigurera åter givning av GPU-accelererad app

Som standard återges appar och skriv bord som körs i konfigurationer med flera sessioner med processorn och utnyttjar inte tillgängliga GPU: er för åter givning. Konfigurera grupprincip för sessions värden för att aktivera GPU-accelererad åter givning:

1. Anslut till Skriv bordet för den virtuella datorn med ett konto med lokal administratörs behörighet.
2. Öppna Start-menyn och skriv "gpedit. msc" för att öppna grupprincip redigeraren.
3. Navigera i trädet till **dator konfiguration** > **administrativa mallar** > **Windows-komponenter** > **Fjärrskrivbordstjänster** > **Border vara värd** för fjärrskrivbordssession > **fjärrsessions miljö**.
4. Välj princip **Använd maskin varans standard grafik kort för alla Fjärrskrivbordstjänster sessioner** och ange att den här principen **är aktive rad** för att aktivera GPU-rendering i fjärrsessionen.

## <a name="configure-gpu-accelerated-frame-encoding"></a>Konfigurera GPU-accelererad ram kodning

Fjärr skrivbord kodar alla bilder som återges av appar och skriv bord (oavsett om de återges med GPU eller CPU) för överföring till fjärr skrivbords klienter. Som standard utnyttjar inte fjärr skrivbord tillgängliga GPU: er för den här kodningen. Konfigurera grupprincip för sessions värden för att aktivera GPU-accelererad ram kodning. Fortsätta med stegen ovan:

1. Välj princip **prioritering h. 264/avc 444-grafik läge för fjärr skrivbords anslutningar** och ange att principen har **Aktiver ATS** för att tvinga H. 264/AVC 444-codec i fjärrsessionen.
2. Välj princip **Konfigurera H. 264/AVC-maskinvarubaserad kodning för fjärr skrivbords anslutningar** och ange att den här principen **är aktive rad** för att aktivera maskin varu kodning för AVC/H. 264 i fjärrsessionen.

    >[!NOTE]
    >I Windows Server 2016 väljer du alternativet **föredra att AVC-maskinvarubaserad kodning** **alltid försöker**.

3. Nu när grup principerna har redigerats måste du framtvinga en uppdatering av grup principen. Öppna kommando tolken och skriv:

    ```batch
    gpupdate.exe /force
    ```

4. Logga ut från fjärrskrivbordssessionen.

## <a name="verify-gpu-accelerated-app-rendering"></a>Verifiera åter givning av GPU-accelererad app

Om du vill kontrol lera att appar använder GPU: n för rendering kan du prova något av följande:

* Använd `nvidia-smi` verktyget enligt beskrivningen i [Verifiera driv rutins installation](/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation) för att kontrol lera om GPU-belastningen körs när dina appar körs.
* I operativ system versioner som stöds kan du använda aktivitets hanteraren för att kontrol lera GPU-användningen. Välj GPU på fliken "prestanda" för att se om appar använder GPU: n.

## <a name="verify-gpu-accelerated-frame-encoding"></a>Kontrol lera att GPU-accelererad ram kodning

Verifiera att fjärr skrivbord använder GPU-accelererad kodning:

1. Anslut till den virtuella datorns Station ära dator med hjälp av Windows Virtual Desktop-klienten.
2. Starta Loggboken och navigera till följande nod: program- **och tjänst loggar** > **Microsoft** > **Windows** > **RemoteDesktopServices-RdpCoreTS** > **Operational**
3. Ta reda på om GPU-accelererad kodning används genom att leta efter händelse-ID 170. Om du ser "AVC Hardware Encoder Enabled: 1" används GPU-kodning.
4. Du kan kontrol lera om AVC 444-läge används genom att leta efter händelse-ID 162. Om du ser "AVC tillgängligt: 1 initial profil: 2048" används AVC 444.

## <a name="next-steps"></a>Nästa steg

De här instruktionerna bör vara igång med GPU-acceleration på en virtuell dator för en enda session. Ytterligare överväganden för att aktivera GPU-acceleration i en större värddator:

* Överväg att använda [nVidia GPU-drivrutinens tillägg](/azure/virtual-machines/extensions/hpccompute-gpu-windows) för att förenkla installationen och uppdateringar av driv rutiner över flera virtuella datorer.
* Överväg att använda Active Directory grupprincip för att förenkla konfigureringen av grup principer på flera virtuella datorer. Information om hur du distribuerar grupprincip i Active Directory-domänen finns i [arbeta med Grupprincip objekt](https://go.microsoft.com/fwlink/p/?LinkId=620889).
