---
title: Arkitektur för Azure Migrates utrustning
description: Innehåller en översikt över Azure Migrate-installationen som används i Server utvärdering och migrering.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 49545ca6c43c272c3fd84f8bee59b8617aae136d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232574"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate-installation

I den här artikeln beskrivs Azure Migrate-utrustningen. Du distribuerar enheten när du använder Azure Migrate utvärderings-och Migreringsverktyg för att identifiera, utvärdera och migrera appar, infrastruktur och arbets belastningar till Microsoft Azure. 

[Azure Migrate](migrate-services-overview.md) tillhandahåller en central hubb för att spåra identifiering, utvärdering och migrering av dina lokala appar och arbets belastningar och privata/offentliga virtuella moln datorer till Azure. Hubben innehåller Azure Migrate verktyg för utvärdering och migrering samt oberoende program varu leverantörer från tredje part (ISV).



## <a name="appliance-overview"></a>Apparat översikt

De Azure Migrate utrustnings typerna och användningen är följande.

**Distribuerad som** | **Används för** | **Information**
--- | --- |  ---
Virtuell VMware-dator | VIRTUELL VMware-utvärdering med verktyget Azure Migrate bedömning.<br/><br/> VMware VM-migrering utan agent med Migreringsverktyg för Azure Migrate Server | Hämta en webbplatsmall-mall och importera till vCenter Server för att skapa den virtuella dator enheten.
Virtuell Hyper-V-dator | Utvärdering av virtuella Hyper-V-datorer med verktyget Azure Migrate bedömning. | Hämta en komprimerad virtuell hård disk och importera till Hyper-V för att skapa den virtuella dator enheten.

## <a name="appliance-access"></a>Åtkomst till enheten

När du har konfigurerat installationen kan du fjärrans luta till den virtuella datorn via TCP-port 3389. Du kan också fjärrans luta till webb hanterings appen för installationen på port 44368 med URL: `https://<appliance-ip-or-name>:44368`.

## <a name="appliance-license"></a>Produkt licens
Enheten levereras med en utvärderings licens för Windows Server 2016, som är giltig i 180 dagar. Om utvärderings perioden ligger nära förfallo datum, rekommenderar vi att du laddar ned och distribuerar en ny installation, eller att du aktiverar operativ Systems licensen för den virtuella dator enheten.

## <a name="appliance-agents"></a>Utrustnings agenter
De här agenterna är installerade på enheten.

**Agent** | **Information**
--- | ---
Identifierings agent | Samlar in konfigurations data för lokala virtuella datorer
Utvärderingsagent | Profiler den lokala miljön för att samla in prestanda data för virtuella datorer.
Migration adapter | Dirigerar VM-replikering och samordnar kommunikation mellan virtuella datorer och Azure.
Gateway för migrering | Skickar replikerade VM-data till Azure.


## <a name="appliance-deployment-requirements"></a>Krav för installation av utrustning

- [Granska](migrate-support-matrix-vmware.md#assessment-appliance-requirements) distributions kraven för en VMware-installation och de URL: er som krävs för att komma åt installationen.
- [Granska](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements) distributions kraven för en Hyper-V-enhet och de URL: er som krävs för att komma åt installationen.


## <a name="collected-performance-data-vmware"></a>Insamlade prestanda data – VMware

Här är de prestanda data för VMware VM som enheten samlar in och skickar till Azure.

**Data** | **Medelvärde** | **Utvärderings påverkan**
--- | --- | ---
CPU-användning | processor. Usage. genomsnitt | Rekommenderad storlek/kostnad för virtuell dator
Minnes användning | mem.usage.average | Rekommenderad storlek/kostnad för virtuell dator
Disk läsnings data flöde (MB per sekund) | virtualDisk. Read. Average | Beräkning för disk storlek, lagrings kostnad, VM-storlek
Disk skrivnings data flöde (MB per sekund) | virtualDisk. Write. Average | Beräkning för disk storlek, lagrings kostnad, VM-storlek
Disk läsnings åtgärder per sekund | virtualDisk. numberReadAveraged. Average | Beräkning för disk storlek, lagrings kostnad, VM-storlek
Disk skrivnings åtgärder per sekund | virtualDisk.numberWriteAveraged.average  | Beräkning för disk storlek, lagrings kostnad, VM-storlek
NIC-läst data flöde (MB per sekund) | net.received.average | Beräkning för VM-storlek
NÄTVERKSKORT Skriv data flöde (MB per sekund) | net.transmitted.average  |Beräkning för VM-storlek


## <a name="collected-metadata-vmware"></a>Insamlade metadata – VMware

> [!NOTE]
> Metadata som upptäckts av Azure Migrate-installationen används för att hjälpa dig att ge dina program rätt storlek när du migrerar dem till Azure, utför Azures analys av lämplighet, program beroende analys och kostnads planering. Microsoft använder inte dessa data i förhållande till någon granskning av licens efterlevnad.

Här är en fullständig lista över de virtuella VMware-metadata som enheten samlar in och skickar till Azure.

**Data** | **Medelvärde**
--- | --- 
**Dator information** | 
ID FÖR VIRTUELL DATOR | vm.Config.InstanceUuid 
VM-namn | vm.Config.Name
vCenter Server-ID | VMwareClient.Instance.Uuid
Beskrivning av virtuell dator | vm.Summary.Config.Annotation
Licens produkt namn | vm.Client.ServiceContent.About.LicenseProductName
Typ av operativ system | vm.SummaryConfig.GuestFullName
Start typ | vm.Config.Firmware
Antal kärnor | vm.Config.Hardware.NumCPU
Minne (MB) | datorn. Config. Hardware. MemoryMB
Antal diskar | datorn. Config. Hardware. Device. ToList (). FindAll (x = > är VirtualDisk). Count
Lista över disk storlekar | datorn. Config. Hardware. Device. ToList (). FindAll (x = > är VirtualDisk)
Lista med nätverkskort | datorn. Config. Hardware. Device. ToList (). FindAll (x = > är VirtualEthernet). Count
CPU-användning | processor. Usage. genomsnitt
Minnes användning |mem.usage.average
**Per disk information** | 
Disk nyckel värde | diskdefragmenter. Knapp
Dikunit-nummer | diskdefragmenter. UnitNumber
Nyckel värde för disk styrenhet | disk.ControllerKey.Value
Gigabyte etablerad | virtualDisk.DeviceInfo.Summary
Disk namn | Värde som genereras med disk. UnitNumber, disk. Nyckel, disk. ControllerKey. VAlue
Läs åtgärder per sekund | virtualDisk. numberReadAveraged. Average
Skriv åtgärder per sekund | virtualDisk.numberWriteAveraged.average
Läs data flöde (MB per sekund) | virtualDisk. Read. Average
Skriv data flöde (MB per sekund) | virtualDisk. Write. Average
**Per NIC-information** | 
Nätverkskortets namn | NIC. Knapp
MAC-adress | ((VirtualEthernetCard)nic).MacAddress
IPv4-adresser | vm.Guest.Net
IPv6-adresser | vm.Guest.Net
Läs data flöde (MB per sekund) | net.received.average
Skriv data flöde (MB per sekund) | net.transmitted.average
**Information om lager Sök väg** | 
Name | container.GetType().Name
Typ av underordnat objekt | fönster. ChildType
Referens information | container.MoRef
Överordnad information | Container. parent
Information om mappar per virtuell dator | ((Mapp)-behållare). ChildEntity. Type
Data Center information per virtuell dator | ((Datacenter)container).VmFolder
Data Center information per värd-mapp | ((Data Center)-behållare). HostFolder
Kluster information per värd | ((ClusterComputeResource) container). Värd
Värd information per virtuell dator | ((HostSystem) container). DATORN



## <a name="collected-performance-data-hyper-v"></a>Insamlade prestanda data – Hyper-V

> [!NOTE]
> Metadata som upptäckts av Azure Migrate-installationen används för att hjälpa dig att ge dina program rätt storlek när du migrerar dem till Azure, utför Azures analys av lämplighet, program beroende analys och kostnads planering. Microsoft använder inte dessa data i förhållande till någon granskning av licens efterlevnad.

Här är de prestanda data för virtuella Hyper-datorer som enheten samlar in och skickar till Azure.

**Prestanda räknar klass** | **Medelvärde** | **Utvärderings påverkan**
--- | --- | ---
Virtuell Hyper-V hypervisor-processor | % Kör tid för gäst | Rekommenderad storlek/kostnad för virtuell dator
VIRTUELL Hyper-V-dynamiskt minne | Aktuellt tryck (%)<br/> Fysiskt synligt fysiskt minne (MB) | Rekommenderad storlek/kostnad för virtuell dator
Virtuell lagrings enhet för Hyper-V | Lästa byte per sekund | Beräkning för disk storlek, lagrings kostnad, VM-storlek
Virtuell lagrings enhet för Hyper-V | Skrivna byte/sekund | Beräkning för disk storlek, lagrings kostnad, VM-storlek
Hyper-V Virtual Network Adapter | Mottagna byte per sekund | Beräkning för VM-storlek
Hyper-V Virtual Network Adapter | Skickade byte per sekund | Beräkning för VM-storlek

- PROCESSOR användningen är summan av all användning, för alla virtuella processorer som är kopplade till en virtuell dator.
- Minnes användningen är (aktuellt tryck * fysiskt synligt minne i gäst)/100.
- Värdena för disk-och nätverks användning samlas in från de listade Hyper-V-prestandaräknare.

## <a name="collected-metadata-hyper-v"></a>Insamlade metadata – Hyper-V

Här är en fullständig lista över Hyper-V VM-metadata som enheten samlar in och skickar till Azure.

**Data** | **WMI-klass** | **Egenskap för WMI-klass**
--- | --- | ---
**Dator information** | 
Serie nummer för BIOS _ Msvm_BIOSElement | BIOSSerialNumber
VM-typ (gen 1 eller 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Visnings namn för virtuell dator | Msvm_VirtualSystemSettingData | ElementName
VM-version | Msvm_ProcessorSettingData | VirtualQuantity
Minne (byte) | Msvm_MemorySettingData | VirtualQuantity
Maximalt minne som kan utnyttjas av VM | Msvm_MemorySettingData | Gräns
Dynamiskt minne aktiverat | Msvm_MemorySettingData | DynamicMemoryEnabled
Operativ systemets namn/version/FQDN | Msvm_KvpExchangeComponent | GuestIntrinsicExchangeItems namn data
Energi status för virtuell dator | Msvm_ComputerSystem | EnabledState
**Per disk information** | 
Disk-ID | Msvm_VirtualHardDiskSettingData | VirtualDiskId
Typ av virtuell hård disk | Msvm_VirtualHardDiskSettingData | Typ
Storlek på virtuell hård disk | Msvm_VirtualHardDiskSettingData | MaxInternalSize
Överordnad virtuell hård disk | Msvm_VirtualHardDiskSettingData | ParentPath
**Per NIC-information** | 
IP-adresser (syntetiska nätverkskort) | Msvm_GuestNetworkAdapterConfiguration | IP-adresser
DHCP aktiverat (syntetiska nätverkskort) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
NÄTVERKSKORT-ID (syntetiska nätverkskort) | Msvm_SyntheticEthernetPortSettingData | Instans
MAC-adress för nätverkskort (syntetiska nätverkskort) | Msvm_SyntheticEthernetPortSettingData | Adress
NÄTVERKSKORT-ID (äldre nätverkskort) | MsvmEmulatedEthernetPortSetting Data | Instans
MAC-ID för nätverkskort (äldre nätverkskort) | MsvmEmulatedEthernetPortSetting Data | Adress




## <a name="discovery-and-collection-process"></a>Process för identifiering och insamling

Enheten kommunicerar med vCenter-servrar och Hyper-V-värdar/-kluster med följande process.


1. **Starta identifiering**:
    - När du startar identifieringen på Hyper-V-enheten kommunicerar den med Hyper-V-värdarna på WinRM-portarna 5985 (HTTP) och 5986 (HTTPS).
    - När du startar identifiering på VMware-enheten kommunicerar den med vCenter-servern på TCP-port 443 som standard. OM vCenter-servern lyssnar på en annan port kan du konfigurera den i installations programmets webbapp.
2. **Samla in metadata och prestanda data**:
    - Enheten använder en Common Information Model-session (CIM) för att samla in virtuella Hyper-V-Datadata från Hyper-V-värden på portarna 5985 och 5986.
    - Enheten kommunicerar med port 443 som standard för att samla in virtuella VMware-Datadata från vCenter Server.
3. **Skicka data**: enheten skickar insamlade data till Azure Migrate Server utvärdering och migrering av Azure Migrate Server över SSL-port 443.
    - För prestanda data samlar enheten in real tids användnings data.
        - Prestanda data samlas in var 20: e sekund för VMware och var 30: e sekund för Hyper-V för varje prestanda mått.
        - Insamlade data samlas in för att skapa en enda data punkt i tio minuter.
        - Värdet för högsta användning väljs från alla de 20/30 andra data punkterna och skickas till beräkningen av Azure för bedömning.
        - Baserat på percentilvärdet som anges i bedömnings egenskaperna (50/nittionde/95/99), sorteras 10-minuters punkterna i stigande ordning och lämpligt percentilvärdet används för att beräkna utvärderingen
    - Vid Server migrering börjar installations programmet samla in VM-data och replikerar dem till Azure.
4. **Utvärdera och migrera**: nu kan du skapa utvärderingar från de metadata som samlas in av installations programmet med hjälp av Azure Migrate Server bedömning. Dessutom kan du börja migrera virtuella VMware-datorer med hjälp av Azure Migrate Server-migrering för att dirigera agent lös VM-replikering.


![Arkitektur](./media/migrate-appliance/architecture.png)


## <a name="appliance-upgrades"></a>Installations program

Installationen uppgraderas eftersom Azure Migrate agenter som körs på enheten uppdateras.

- Detta sker automatiskt eftersom den automatiska uppdateringen är aktive rad som standard.
- Du kan ändra den här standardinställningen för att uppdatera agenterna manuellt.
- Om du vill inaktivera den automatiska uppdateringen går du till Registereditorn > HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\AzureAppliance och anger register nyckeln "AutoUpdate" till 0 (DWORD).
 
### <a name="set-agent-updates-to-manual"></a>Konfigurera agent uppdateringar till manuell

För manuella uppdateringar ser du till att du uppdaterar alla agenter på installationen på samma gång, med knappen **Uppdatera** för varje föråldrad agent på enheten. Du kan när som helst växla uppdaterings inställningen tillbaka till automatiska uppdateringar.

## <a name="next-steps"></a>Nästa steg

[Lär dig hur](tutorial-assess-vmware.md#set-up-the-appliance-vm) du konfigurerar-installationen för VMware.
[Lär dig hur](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) du konfigurerar-enheten för Hyper-V.

