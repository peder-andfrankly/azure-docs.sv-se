---
title: Konfigurera tillgänglighets grupps lyssnare & Load Balancer (PowerShell)
description: Konfigurera tillgänglighets grupps lyssnare i Azure Resource Manager modellen med hjälp av en intern belastningsutjämnare med en eller flera IP-adresser.
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
ms.assetid: 14b39cde-311c-4ddf-98f3-8694e01a7d3b
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/06/2019
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: 83910c2209b5d3d3d67578ae41afb902bc885171
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037458"
---
# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>Konfigurera en eller flera Always on tillgänglighets grupps lyssnare – Resource Manager
Det här avsnittet visar hur du:

* Skapa en intern belastningsutjämnare för SQL Server tillgänglighets grupper med hjälp av PowerShell-cmdletar.
* Lägg till ytterligare IP-adresser till en belastningsutjämnare för fler än en tillgänglighets grupp. 

En tillgänglighets grupps lyssnare är ett virtuellt nätverks namn som klienter ansluter till för databas åtkomst. På virtuella Azure-datorer innehåller en belastningsutjämnare IP-adressen för lyssnaren. Belastningsutjämnaren dirigerar trafik till instansen av SQL Server som lyssnar på avsöknings porten. En tillgänglighets grupp använder vanligt vis en intern belastningsutjämnare. En intern Azure-belastningsutjämnare kan vara värd för en eller flera IP-adresser. Varje IP-adress använder en angiven avsöknings port. Det här dokumentet visar hur du använder PowerShell för att skapa en belastningsutjämnare eller lägga till IP-adresser till en befintlig belastningsutjämnare för SQL Server tillgänglighets grupper. 

Möjligheten att tilldela flera IP-adresser till en intern belastningsutjämnare är ny i Azure och är bara tillgänglig i Resource Manager-modellen. För att slutföra den här uppgiften måste du ha en SQL Server tillgänglighets grupp distribuerad på virtuella Azure-datorer i Resource Manager-modellen. Både SQL Server virtuella datorer måste tillhöra samma tillgänglighets uppsättning. Du kan använda [Microsoft-mallen](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) för att automatiskt skapa tillgänglighets gruppen i Azure Resource Manager. Den här mallen skapar automatiskt tillgänglighets gruppen, inklusive den interna belastningsutjämnaren för dig. Om du vill kan du [Konfigurera en tillgänglighets grupp som alltid är tillgänglig manuellt](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

Det här avsnittet kräver att dina tillgänglighets grupper redan har kon figurer ATS.  

Relaterade ämnen är:

* [Konfigurera AlwaysOn-tillgänglighetsgrupper i Azure VM (GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Konfigurera en VNet-till-VNet-anslutning med hjälp av Azure Resource Manager och PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

[!INCLUDE [Start your PowerShell session](../../../../includes/sql-vm-powershell.md)]

## <a name="verify-powershell-version"></a>Verifiera PowerShell-version

Exemplen i den här artikeln är testade med Azure PowerShell module version 5.4.1.

Kontrol lera att din PowerShell-modul är 5.4.1 eller senare.

Se [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="configure-the-windows-firewall"></a>Konfigurera Windows-brandväggen

Konfigurera Windows-brandväggen så att SQL Server åtkomst tillåts. Brand Väggs reglerna tillåter TCP-anslutningar till portarna som används av SQL Server-instansen och lyssnar avsökningen. Detaljerade instruktioner finns i [Konfigurera en Windows-brandvägg för databas motor åtkomst](https://msdn.microsoft.com/library/ms175043.aspx#Anchor_1). Skapa en regel för inkommande trafik för SQL Server Port och för avsöknings porten.

Om du begränsar åtkomsten med en Azure-nätverks säkerhets grupp kontrollerar du att Tillåt-reglerna omfattar Server dels SQL Server VM IP-adresser och de flytande IP-adresserna för AG-lyssnaren och kluster Core-IP-adressen, om tillämpligt.

## <a name="determine-the-load-balancer-sku-required"></a>Fastställ vilket SKU för belastningsutjämnare som krävs

[Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) är tillgängligt i 2 SKU: er: Basic & standard. Standard belastnings utjämning rekommenderas. Om de virtuella datorerna finns i en tillgänglighets uppsättning tillåts Basic Load Balancer. Standard belastnings utjämning kräver att alla VM IP-adresser använder standard-IP-adresser.

Den aktuella [Microsoft-mallen](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) för en tillgänglighets grupp använder en grundläggande belastningsutjämnare med grundläggande IP-adresser.

I exemplen i den här artikeln anges en standard belastningsutjämnare. I exemplen innehåller skriptet `-sku Standard`.

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe -sku Standard
```

Om du vill skapa en grundläggande belastningsutjämnare tar du bort `-sku Standard` från raden som skapar belastningsutjämnaren. Exempel:

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe
```

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>Exempel skript: skapa en intern belastningsutjämnare med PowerShell

> [!NOTE]
> Om du har skapat din tillgänglighets grupp med [Microsoft-mallen](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)har den interna belastningsutjämnaren redan skapats.

Följande PowerShell-skript skapar en intern belastningsutjämnare, konfigurerar belastnings Utjämnings reglerna och anger en IP-adress för belastningsutjämnaren. Öppna Windows PowerShell ISE och klistra in skriptet i skript fönstret för att köra skriptet. Använd `Connect-AzAccount` för att logga in på PowerShell. Om du har flera Azure-prenumerationer använder du `Select-AzSubscription` för att ställa in prenumerationen. 

```powershell
# Connect-AzAccount
# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<Resource Group Name>" # Resource group name
$VNetName = "<Virtual Network Name>"         # Virtual network name
$SubnetName = "<Subnet Name>"                # Subnet name
$ILBName = "<Load Balancer Name>"            # ILB name
$Location = "<Azure Region>"                 # Azure location
$VMNames = "<VM1>","<VM2>"                   # Virtual machine names

$ILBIP = "<n.n.n.n>"                         # IP address
[int]$ListenerPort = "<nnnn>"                # AG listener port
[int]$ProbePort = "<nnnn>"                   # Probe port

$LBProbeName ="ILBPROBE_$ListenerPort"       # The Load balancer Probe Object Name              
$LBConfigRuleName = "ILBCR_$ListenerPort"    # The Load Balancer Rule Object Name

$FrontEndConfigurationName = "FE_SQLAGILB_1" # Object name for the front-end configuration 
$BackEndConfigurationName ="BE_SQLAGILB_1"   # Object name for the back-end configuration

$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 

$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName 

$FEConfig = New-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.id

$BEConfig = New-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName 

$SQLHealthProbe = New-AzLoadBalancerProbeConfig -Name $LBProbeName -Protocol tcp -Port $ProbePort -IntervalInSeconds 15 -ProbeCount 2

$ILBRule = New-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP 

$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe 

$bepool = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB 

foreach($VMName in $VMNames)
    {
        $VM = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName 
        $NICName = ($vm.NetworkProfile.NetworkInterfaces.Id.split('/') | select -last 1)
        $NIC = Get-AzNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools = $BEPool
        Set-AzNetworkInterface -NetworkInterface $NIC
        start-AzVM -ResourceGroupName $ResourceGroupName -Name $VM.Name 
    }
```

## <a name="Add-IP"></a>Exempel skript: Lägg till en IP-adress till en befintlig belastningsutjämnare med PowerShell
Om du vill använda fler än en tillgänglighets grupp lägger du till ytterligare en IP-adress i belastningsutjämnaren. Varje IP-adress kräver en egen belastnings Utjämnings regel, avsöknings port och klient port.

Klient dels porten är den port som program använder för att ansluta till SQL Server-instansen. IP-adresser för olika tillgänglighets grupper kan använda samma klient dels port.

> [!NOTE]
> För SQL Server tillgänglighets grupper kräver varje IP-adress en speciell avsöknings port. Om till exempel en IP-adress på en belastningsutjämnare använder avsöknings port 59999, kan inga andra IP-adresser på belastningsutjämnaren använda avsöknings port 59999.

* Information om gränser för belastnings utjämning finns i **privat klient-IP per belastningsutjämnare** under [nätverks gränser – Azure Resource Manager](../../../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).
* Information om begränsningar för tillgänglighets grupper finns i [begränsningar (tillgänglighets grupper)](https://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG).

Följande skript lägger till en ny IP-adress till en befintlig belastningsutjämnare. ILB använder lyssnar porten för klient dels porten för belastnings utjämning. Den här porten kan vara den port som SQL Server lyssnar på. För standard instanser av SQL Server är porten 1433. Belastnings Utjämnings regeln för en tillgänglighets grupp kräver en flytande IP-adress (direkt Server retur) så att backend-porten är samma som klient dels porten. Uppdatera variablerna för din miljö. 

```powershell
# Connect-AzAccount
# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<ResourceGroup>"          # Resource group name
$VNetName = "<VirtualNetwork>"                  # Virtual network name
$SubnetName = "<Subnet>"                        # Subnet name
$ILBName = "<ILBName>"                          # ILB name                      

$ILBIP = "<n.n.n.n>"                            # IP address
[int]$ListenerPort = "<nnnn>"                   # AG listener port
[int]$ProbePort = "<nnnnn>"                     # Probe port 

$ILB = Get-AzLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName 

$count = $ILB.FrontendIpConfigurations.Count+1
$FrontEndConfigurationName ="FE_SQLAGILB_$count"  

$LBProbeName = "ILBPROBE_$count"
$LBConfigrulename = "ILBCR_$count"

$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 
$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

$ILB | Add-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id 

$ILB | Add-AzLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 15  | Set-AzLoadBalancer 

$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

$FEConfig = get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB

$SQLHealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

$BEConfig = Get-AzLoadBalancerBackendAddressPoolConfig -Name $ILB.BackendAddressPools[0].Name -LoadBalancer $ILB 

$ILB | Add-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort  $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP | Set-AzLoadBalancer   
```

## <a name="configure-the-listener"></a>Konfigurera lyssnaren

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-the-listener-port-in-sql-server-management-studio"></a>Ange lyssnar porten i SQL Server Management Studio

1. Starta SQL Server Management Studio och Anslut till den primära repliken.

1. Gå till | **tillgänglighets grupper** för **AlwaysOn-hög tillgänglighet** | **tillgänglighets grupps lyssnare**. 

1. Du bör nu se det lyssnar namn som du skapade i Klusterhanteraren för växling vid fel. Högerklicka på namnet på lyssnaren och klicka på **Egenskaper**.

1. I rutan **port** anger du Port numret för tillgänglighets gruppens lyssnare genom att använda $EndpointPort som du använde tidigare (1433 var standard) och klickar sedan på **OK**.

## <a name="test-the-connection-to-the-listener"></a>Testa anslutningen till lyssnaren

Så här testar du anslutningen:

1. RDP till en SQL Server som finns i samma virtuella nätverk, men som inte äger repliken. Detta kan vara den andra SQL Server i klustret.

1. Använd **SQLCMD** -verktyget för att testa anslutningen. Följande skript upprättar till exempel en **SQLCMD** -anslutning till den primära repliken via lyssnaren med Windows-autentisering:
   
    ```
    sqlcmd -S <listenerName> -E
    ```
   
    Om lyssnaren använder en annan port än standard porten (1433) anger du porten i anslutnings strängen. Till exempel ansluter följande SQLCMD-kommando till en lyssnare på port 1435: 
   
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

SQLCMD-anslutningen ansluts automatiskt till den beroende instansen av SQL Server är värd för den primära repliken. 

> [!NOTE]
> Kontrol lera att den port du anger är öppen i brand väggen för båda SQL-servrarna. Båda servrarna kräver en regel för inkommande trafik för TCP-porten som du använder. Mer information finns i [Lägg till eller redigera brand Väggs regel](https://technet.microsoft.com/library/cc753558.aspx) . 
> 
> 

## <a name="guidelines-and-limitations"></a>Riktlinjer och begränsningar
Observera följande rikt linjer för tillgänglighets gruppens lyssnare i Azure med hjälp av en intern belastningsutjämnare:

* Med en intern belastningsutjämnare får du bara åtkomst till lyssnaren från samma virtuella nätverk.

* Om du begränsar åtkomsten med en Azure-nätverks säkerhets grupp kontrollerar du att Tillåt-reglerna omfattar Server dels SQL Server VM IP-adresser och de flytande IP-adresserna för AG-lyssnaren och kluster Core-IP-adressen, om tillämpligt.

## <a name="for-more-information"></a>Mer information
Mer information finns i [Konfigurera Always on Availability Group i Azure VM manuellt](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

## <a name="powershell-cmdlets"></a>PowerShell-cmdletar
Använd följande PowerShell-cmdletar för att skapa en intern belastningsutjämnare för virtuella Azure-datorer.

* [New-AzLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) skapar en belastningsutjämnare. 
* [New-AzLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) skapar en IP-konfiguration på klient sidan för en belastningsutjämnare. 
* [New-AzLoadBalancerRuleConfig](https://msdn.microsoft.com/library/mt619391.aspx) skapar en regel konfiguration för en belastningsutjämnare. 
* [New-AzLoadBalancerBackendAddressPoolConfig](https://msdn.microsoft.com/library/mt603791.aspx) skapar en konfiguration för Server delens adresspool för en belastningsutjämnare. 
* [New-AzLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) skapar en avsöknings konfiguration för en belastningsutjämnare.
* [Remove-AzLoadBalancer](https://msdn.microsoft.com/library/mt603862.aspx) tar bort en belastningsutjämnare från en Azure-resurs grupp.
