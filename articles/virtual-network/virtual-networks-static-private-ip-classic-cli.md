---
title: Konfigurera privata IP-adresser för virtuella datorer (klassisk) – CLI
titlesuffix: Azure Virtual Network
description: Lär dig hur du konfigurerar den privata IP-adresser för virtuella datorer (klassisk) med hjälp av Azure klassiska kommandoradsgränssnittet (CLI).
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: 9deaf4b1d80ad4e55e7c971998e8b1f5ea562257
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196584"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-classic-cli"></a>Konfigurera privata IP-adresser för en virtuell dator (klassisk) med den klassiska Azure CLI

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Den här artikeln beskriver hur du gör om du använder den klassiska distributionsmodellen. Du kan också [hantera en statisk privat IP-adress i distributions modellen för Resource Manager](virtual-networks-static-private-ip-arm-cli.md).

Exemplet Azure klassiskt CLI-kommandon fram förväntar sig en enkel miljö som redan har skapats. Om du vill köra kommandona som de visas i det här dokumentet skapar du först test miljön som beskrivs i [skapa ett VNet](virtual-networks-create-vnet-classic-cli.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Så här anger du en statisk privat IP-adress när du skapar en virtuell dator
Följ dessa steg om du vill skapa en ny virtuell dator med namnet *DNS01* i en ny moln tjänst med namnet *TestService* baserat på scenariot ovan:

1. Om du aldrig har använt Azure CLI, se [installera och konfigurera Azure CLI](/cli/azure/install-cli-version-1.0) och följ instruktionerna upp till den punkt där du väljer Azure-konto och prenumeration.
2. Kör kommandot **Azure Service Create** för att skapa moln tjänsten.
   
        azure service create TestService --location uscentral
   
    Förväntad utdata:
   
        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name TestService
        info:    service create command OK
3. Kör kommandot **Azure Create VM** för att skapa den virtuella datorn. Lägg märke till värdet för en statisk privat IP-adress. Listan som visas efter alla utdata förklarar parametrarna som använts.
   
        azure vm create -l centralus -n DNS01 -w TestVNet -S "192.168.1.101" TestService bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 adminuser AdminP@ssw0rd
   
    Förväntad utdata:
   
        info:    Executing command vm create
        warn:    --vm-size has not been specified. Defaulting to "Small".
        info:    Looking up image bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
        info:    Looking up virtual network
        info:    Looking up cloud service
        warn:    --location option will be ignored
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Retrieving storage accounts
        info:    Creating VM
        info:    OK
        info:    vm create command OK
   
   * **-l (eller --location)** . Azure-region där den virtuella datorn kommer att skapas. I vårt scenario, *centralus*.
   * **-n (eller--VM-Name)** . Namnet på den virtuella datorn skapas.
   * **-w (eller--Virtual-Network-Name)** . Namnet på det virtuella nätverket där den virtuella datorn kommer att skapas. 
   * **-S (eller--statisk IP)** . Statiska privata IP-adress för den virtuella datorn.
   * **TestService**. Namnet på Molntjänsten där den virtuella datorn kommer att skapas.
   * **bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v-14.2**. Bild som används för att skapa den virtuella datorn.
   * **adminuser**. Lokal administratör för den virtuella Windows-datorn.
   * <strong>AdminP@ssw0rd</strong>. Lokala administratörslösenordet för den virtuella Windows-datorn.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Hur du hämtar statiska privata IP-adressinformation för en virtuell dator
Om du vill visa information om statisk privat IP-adress för den virtuella datorn som har skapats med skriptet ovan kör du följande Azure CLI-kommando och noterar värdet för *nätverks StaticIP*:

    azure vm static-ip show DNS01

Förväntad utdata:

    info:    Executing command vm static-ip show
    info:    Getting virtual machines
    data:    Network StaticIP "192.168.1.101"
    info:    vm static-ip show command OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Ta bort en statisk privat IP-adress från en virtuell dator
Ta bort den statiska privata IP-adressen har lagt till till den virtuella datorn i skriptet ovan, kör du följande Azure CLI-kommando:

    azure vm static-ip remove DNS01

Förväntad utdata:

    info:    Executing command vm static-ip remove
    info:    Getting virtual machines
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip remove command OK

## <a name="how-to-add-a-static-private-ip-to-an-existing-vm"></a>Hur du lägger till en statisk privat IP-adress till en befintlig virtuell dator
Om du vill lägga till en statisk privat IP-adress till den virtuella datorn som skapades med skriptet ovan kör du följande kommando:

    azure vm static-ip set DNS01 192.168.1.101

Förväntad utdata:

    info:    Executing command vm static-ip set
    info:    Getting virtual machines
    info:    Looking up virtual network
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip set command OK

## <a name="set-ip-addresses-within-the-operating-system"></a>Ange IP-adresser i operativsystemet

Vi rekommenderar att du inte statiskt tilldelar privat IP-adress som tilldelats virtuella Azure-datorer i operativsystemet på en virtuell dator, om inte behövs. Om du manuellt anger den privata IP-adressen i operativsystemet, kontrollera att det är samma adress som den privata IP-adressen som tilldelas den virtuella Azure-datorn, eller du kan förlora anslutningen till den virtuella datorn. Tilldela inte den offentliga IP-adressen som tilldelats till en virtuell Azure-dator manuellt i den virtuella datorns operativ system.

## <a name="next-steps"></a>Nästa steg
* Läs mer om [reserverade offentliga IP-](virtual-networks-reserved-public-ip.md) adresser.
* Lär dig mer om [offentliga IP-adresser på instans nivå (ILPIP)](virtual-networks-instance-level-public-ip.md) .
* Läs [reserverad IP REST-API: er](https://msdn.microsoft.com/library/azure/dn722420.aspx).