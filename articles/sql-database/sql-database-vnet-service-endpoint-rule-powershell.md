---
title: PowerShell för VNet-slutpunkter och regler för databaser med enkel och pool
description: Tillhandahåller PowerShell-skript för att skapa och hantera slut punkter för virtuella tjänster för Azure SQL Database och SQL Data Warehouse.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, vanto
ms.date: 03/12/2019
ms.openlocfilehash: 76c4ea6c3fc5f415316e2b5cfcdf80c0681cc3f6
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422487"
---
# <a name="powershell--create-a-virtual-service-endpoint-and-vnet-rule-for-sql"></a>PowerShell: skapa en virtuell tjänst slut punkt och VNet-regel för SQL

*Regler för virtuella nätverk* är en brand Väggs säkerhetsfunktion som styr om databas servern för dina enskilda databaser och elastisk pool i Azure [SQL Database](sql-database-technical-overview.md) eller för dina databaser i [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) accepterar kommunikation som skickas från särskilda undernät i virtuella nätverk.

> [!IMPORTANT]
> Den här artikeln gäller för Azure SQL Server och för både SQL Database och SQL Data Warehouse databaser som skapas på Azure SQL-servern. För enkelhetens skull används SQL Database när det gäller både SQL Database och SQL Data Warehouse. Den här artikeln gäller *inte* för distribution av **hanterade instanser** i Azure SQL Database eftersom den inte har någon associerad tjänst slut punkt.

Den här artikeln innehåller och förklarar ett PowerShell-skript som vidtar följande åtgärder:

1. Skapar en Microsoft Azure *virtuell tjänst slut punkt* i ditt undernät.
2. Lägger till slut punkten i brand väggen för din Azure SQL Database-Server för att skapa en *regel för virtuella nätverk*.

Dina motivation för att skapa en regel beskrivs i: [Virtual service-slutpunkter för Azure SQL Database][sql-db-vnet-service-endpoint-rule-overview-735r].

> [!TIP]
> Om allt du behöver är att utvärdera eller lägga till namnet på den virtuella tjänstens slut punkts *typ* för SQL Database till ditt undernät kan du gå vidare till vårt mer [direkt PowerShell-skript](#a-verify-subnet-is-endpoint-ps-100).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska.

## <a name="major-cmdlets"></a>Huvud-cmdletar

Den här artikeln betonar cmdleten **New-AzSqlServerVirtualNetworkRule** som lägger till under nätets slut punkt i åtkomst kontrol listan (ACL) för din Azure SQL Database Server, vilket skapar en regel.

I följande lista visas sekvensen för andra *större* cmdlets som du måste köra för att förbereda för ditt anrop till **New-AzSqlServerVirtualNetworkRule**. I den här artikeln sker dessa anrop i [skript 3 "regel för virtuellt nätverk"](#a-script-30):

1. [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig): skapar ett under näts objekt.
2. [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork): skapar ditt virtuella nätverk, vilket ger det under nätet.
3. [Set-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/Set-azVirtualNetworkSubnetConfig): tilldelar en virtuell tjänst slut punkt till ditt undernät.
4. [Set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/Set-azVirtualNetwork): behåller uppdateringar som gjorts i det virtuella nätverket.
5. [New-AzSqlServerVirtualNetworkRule](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlservervirtualnetworkrule): när under nätet är en slut punkt lägger du till ditt undernät som en virtuell nätverks regel i åtkomst kontrol listan för din Azure SQL Database Server.
   - Denna cmdlet erbjuder parametern **-IgnoreMissingVNetServiceEndpoint**, som startar i Azure RM PowerShell-modul version 5.1.1.

## <a name="prerequisites-for-running-powershell"></a>Krav för att köra PowerShell

- Du kan redan logga in på Azure, till exempel via [Azure Portal][http-azure-portal-link-ref-477t].
- Du kan redan köra PowerShell-skript.

> [!NOTE]
> Kontrol lera att tjänstens slut punkter är aktiverade för det virtuella nätverk/undernät som du vill lägga till på servern. det går inte att skapa en brand Väggs regel för VNet.

## <a name="one-script-divided-into-four-chunks"></a>Ett skript är uppdelat i fyra segment

PowerShell-skriptet i demonstrationen är indelat i en sekvens med mindre skript. Divisionen underlättar inlärningen och ger flexibilitet. Skripten måste köras i angiven ordning. Om du inte har tid att köra skripten visas vårt faktiska test resultat efter skript 4.

<a name="a-script-10" />

### <a name="script-1-variables"></a>Skript 1: variabler

Det första PowerShell-skriptet tilldelar variabler värden. De efterföljande skripten är beroende av dessa variabler.

> [!IMPORTANT]
> Innan du kör det här skriptet kan du redigera värdena om du vill. Om du till exempel redan har en resurs grupp kanske du vill redigera resurs gruppens namn som det tilldelade värdet.
>
> Ditt prenumerations namn bör redige ras i skriptet.

### <a name="powershell-script-1-source-code"></a>PowerShell-skript 1 käll kod

```powershell
######### Script 1 ########################################
##   LOG into to your Azure account.                     ##
##   (Needed only one time per powershell.exe session.)  ##
###########################################################

$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Connect-AzAccount; }

###########################################################
##  Assignments to variables used by the later scripts.  ##
###########################################################

# You can edit these values, if necessary.
$SubscriptionName = 'yourSubscriptionName';
Select-AzSubscription -SubscriptionName $SubscriptionName;

$ResourceGroupName = 'RG-YourNameHere';
$Region            = 'westcentralus';

$VNetName            = 'myVNet';
$SubnetName          = 'mySubnet';
$VNetAddressPrefix   = '10.1.0.0/16';
$SubnetAddressPrefix = '10.1.1.0/24';
$VNetRuleName        = 'myFirstVNetRule-ForAcl';

$SqlDbServerName         = 'mysqldbserver-forvnet';
$SqlDbAdminLoginName     = 'ServerAdmin';
$SqlDbAdminLoginPassword = 'ChangeYourAdminPassword1';

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql';  # Official type name.

Write-Host 'Completed script 1, the "Variables".';
```

<a name="a-script-20" />

### <a name="script-2-prerequisites"></a>Skript 2: krav

Det här skriptet förbereder för nästa skript, där slut punkts åtgärden är. Det här skriptet skapar för dig följande objekt i listan, men endast om de inte redan finns. Du kan hoppa över skript 2 om du är säker på att dessa objekt redan finns:

- Azure-resursgrupp
- Azure SQL Database Server

### <a name="powershell-script-2-source-code"></a>Käll kod för PowerShell-skript 2

```powershell
######### Script 2 ########################################
##   Ensure your Resource Group already exists.          ##
###########################################################

Write-Host "Check whether your Resource Group already exists.";

$gottenResourceGroup = $null;

$gottenResourceGroup = Get-AzResourceGroup `
  -Name        $ResourceGroupName `
  -ErrorAction SilentlyContinue;

if ($null -eq $gottenResourceGroup)
{
    Write-Host "Creating your missing Resource Group - $ResourceGroupName.";

    $gottenResourceGroup = New-AzResourceGroup `
      -Name $ResourceGroupName `
      -Location $Region;

    $gottenResourceGroup;
}
else { Write-Host "Good, your Resource Group already exists - $ResourceGroupName."; }

$gottenResourceGroup = $null;

###########################################################
## Ensure your Azure SQL Database server already exists. ##
###########################################################

Write-Host "Check whether your Azure SQL Database server already exists.";

$sqlDbServer = $null;

$sqlDbServer = Get-AzSqlServer `
  -ResourceGroupName $ResourceGroupName `
  -ServerName        $SqlDbServerName `
  -ErrorAction       SilentlyContinue;

if ($null -eq $sqlDbServer) {
    Write-Host "Creating the missing Azure SQL Database server - $SqlDbServerName.";
    Write-Host "Gather the credentials necessary to next create an Azure SQL Database server.";

    $sqlAdministratorCredentials = New-Object `
      -TypeName     System.Management.Automation.PSCredential `
      -ArgumentList `
        $SqlDbAdminLoginName, `
        $(ConvertTo-SecureString `
            -String      $SqlDbAdminLoginPassword `
            -AsPlainText `
            -Force `
         );

    if ($null -eq $sqlAdministratorCredentials) {
        Write-Host "ERROR, unable to create SQL administrator credentials.  Now ending.";
        return;
    }

    Write-Host "Create your Azure SQL Database server.";

    $sqlDbServer = New-AzSqlServer `
      -ResourceGroupName $ResourceGroupName `
      -ServerName        $SqlDbServerName `
      -Location          $Region `
      -SqlAdministratorCredentials $sqlAdministratorCredentials;

    $sqlDbServer;
}
else {
    Write-Host "Good, your Azure SQL Database server already exists - $SqlDbServerName."; 
}

$sqlAdministratorCredentials = $null;
$sqlDbServer                 = $null;

Write-Host 'Completed script 2, the "Prerequisites".';
```

<a name="a-script-30" />

## <a name="script-3-create-an-endpoint-and-a-rule"></a>Skript 3: skapa en slut punkt och en regel

Det här skriptet skapar ett virtuellt nätverk med ett undernät. Sedan tilldelar skriptet **Microsoft. SQL** -slutpunkts typen till ditt undernät. Slutligen lägger skriptet till ditt undernät i åtkomst kontrol listan (ACL) för SQL Database-servern, vilket skapar en regel.

### <a name="powershell-script-3-source-code"></a>PowerShell-skript 3 käll kod

```powershell
######### Script 3 ########################################
##   Create your virtual network, and give it a subnet.  ##
###########################################################

Write-Host "Define a subnet '$SubnetName', to be given soon to a virtual network.";

$subnet = New-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Create a virtual network '$VNetName'." `
  "  Give the subnet to the virtual network that we created.";

$vnet = New-AzVirtualNetwork `
  -Name              $VNetName `
  -AddressPrefix     $VNetAddressPrefix `
  -Subnet            $subnet `
  -ResourceGroupName $ResourceGroupName `
  -Location          $Region;

###########################################################
##   Create a Virtual Service endpoint on the subnet.    ##
###########################################################

Write-Host "Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.";

$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Persist the updates made to the virtual network > subnet.";

$vnet = Set-AzVirtualNetwork `
  -VirtualNetwork $vnet;

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

###########################################################
##   Add the Virtual Service endpoint Id as a rule,      ##
##   into SQL Database ACLs.                             ##
###########################################################

Write-Host "Get the subnet object.";

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

$subnet = Get-AzVirtualNetworkSubnetConfig `
  -Name           $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Add the subnet .Id as a rule, into the ACLs for your Azure SQL Database server.";

$vnetRuleObject1 = New-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnet.Id;

$vnetRuleObject1;

Write-Host "Verify that the rule is in the SQL DB ACL.";

$vnetRuleObject2 = Get-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName;

$vnetRuleObject2;

Write-Host 'Completed script 3, the "Virtual-Network-Rule".';
```

<a name="a-script-40" />

## <a name="script-4-clean-up"></a>Skript 4: Rensa

Detta slutliga skript tar bort de resurser som tidigare skript skapade för demonstrationen. Skriptet ber dock om bekräftelse innan följande tas bort:

- Azure SQL Database Server
- Azure-resursgrupp

Du kan köra skript 4 när som helst efter att skript 1 har slutförts.

### <a name="powershell-script-4-source-code"></a>Käll kod för PowerShell-skript 4

```powershell
######### Script 4 ########################################
##   Clean-up phase A:  Unconditional deletes.           ##
##                                                       ##
##   1. The test rule is deleted from SQL DB ACL.        ##
##   2. The test endpoint is deleted from the subnet.    ##
##   3. The test virtual network is deleted.             ##
###########################################################

Write-Host "Delete the rule from the SQL DB ACL.";

Remove-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -ErrorAction            SilentlyContinue;

Write-Host "Delete the endpoint from the subnet.";

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

Remove-AzVirtualNetworkSubnetConfig `
  -Name $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Delete the virtual network (thus also deletes the subnet).";

Remove-AzVirtualNetwork `
  -Name              $VNetName `
  -ResourceGroupName $ResourceGroupName `
  -ErrorAction       SilentlyContinue;

###########################################################
##   Clean-up phase B:  Conditional deletes.             ##
##                                                       ##
##   These might have already existed, so user might     ##
##   want to keep.                                       ##
##                                                       ##
##   1. Azure SQL Database server                        ##
##   2. Azure resource group                             ##
###########################################################

$yesno = Read-Host 'CAUTION !: Do you want to DELETE your Azure SQL Database server AND your Resource Group?  [yes/no]';
if ('yes' -eq $yesno) {
    Write-Host "Remove the Azure SQL DB server.";

    Remove-AzSqlServer `
      -ServerName        $SqlDbServerName `
      -ResourceGroupName $ResourceGroupName `
      -ErrorAction       SilentlyContinue;

    Write-Host "Remove the Azure Resource Group.";

    Remove-AzResourceGroup `
      -Name        $ResourceGroupName `
      -ErrorAction SilentlyContinue;
}
else {
    Write-Host "Skipped over the DELETE of SQL Database and resource group.";
}

Write-Host 'Completed script 4, the "Clean-Up".';
```

<a name="a-actual-output" />

<a name="a-verify-subnet-is-endpoint-ps-100" />

## <a name="verify-your-subnet-is-an-endpoint"></a>Verifiera att ditt undernät är en slut punkt

Du kan ha ett undernät som redan har tilldelats namnet **Microsoft. SQL** -typ, vilket innebär att det redan är en virtuell tjänst slut punkt. Du kan använda [Azure Portal][http-azure-portal-link-ref-477t] för att skapa en regel för virtuella nätverk från slut punkten.

Eller så kanske du är osäker på om ditt undernät har namnet **Microsoft. SQL** -typ. Du kan köra följande PowerShell-skript för att vidta följande åtgärder:

1. Kontrol lera om ditt undernät har namnet **Microsoft. SQL** -typ.
2. Du kan också tilldela typ namnet om det saknas.
    - Skriptet uppmanar dig att *Bekräfta*, innan det använder det saknas typ namnet.

### <a name="phases-of-the-script"></a>Faser i skriptet

Här är faserna i PowerShell-skriptet:

1. Logga in på ditt Azure-konto, som krävs bara en gång per PS-session.  Tilldela variabler.
2. Sök efter ditt virtuella nätverk och sedan för ditt undernät.
3. Är ditt undernät märkt som **Microsoft. SQL** -slutpunkt Server typ?
4. Lägg till en virtuell tjänst slut punkt av typen namn **Microsoft. SQL**, i ditt undernät.

> [!IMPORTANT]
> Innan du kör det här skriptet måste du redigera värdena som tilldelats $-variablerna, nästan överst i skriptet.

### <a name="direct-powershell-source-code"></a>Direkt PowerShell-Källkod

Det här PowerShell-skriptet uppdaterar ingenting, om du inte svarar Ja om du uppmanas att bekräfta det. Skriptet kan lägga till typ namnet **Microsoft. SQL** i ditt undernät. Men skriptet försöker bara lägga till om ditt undernät saknar typ namnet.

```powershell
### 1. LOG into to your Azure account, needed only once per PS session.  Assign variables.
$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Connect-AzAccount; }

# Assignments to variables used by the later scripts.
# You can EDIT these values, if necessary.

$SubscriptionName  = 'yourSubscriptionName';
Select-AzSubscription -SubscriptionName "$SubscriptionName";

$ResourceGroupName   = 'yourRGName';
$VNetName            = 'yourVNetName';
$SubnetName          = 'yourSubnetName';
$SubnetAddressPrefix = 'Obtain this value from the Azure portal.'; # Looks roughly like: '10.0.0.0/24'

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql';  # Do NOT edit. Is official value.

### 2. Search for your virtual network, and then for your subnet.
# Search for the virtual network.
$vnet = $null;
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

if ($vnet -eq $null) {
    Write-Host "Caution: No virtual network found by the name '$VNetName'.";
    Return;
}

$subnet = $null;
for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++) {
    $subnet = $vnet.Subnets[$nn];
    if ($subnet.Name -eq $SubnetName)
    { break; }
    $subnet = $null;
}

if ($subnet -eq $null) {
    Write-Host "Caution: No subnet found by the name '$SubnetName'";
    Return;
}

### 3. Is your subnet tagged as 'Microsoft.Sql' endpoint server type?
$endpointMsSql = $null;
for ($nn=0; $nn -lt $subnet.ServiceEndpoints.Count; $nn++) {
    $endpointMsSql = $subnet.ServiceEndpoints[$nn];
    if ($endpointMsSql.Service -eq $ServiceEndpointTypeName_SqlDb) {
        $endpointMsSql;
        break;
    }
    $endpointMsSql = $null;
}

if ($endpointMsSql -ne $null) {
    Write-Host "Good: Subnet found, and is already tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";
    Return;
}
else {
    Write-Host "Caution: Subnet found, but not yet tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";

    # Ask the user for confirmation.
    $yesno = Read-Host 'Do you want the PS script to apply the endpoint type name to your subnet?  [yes/no]';
    if ('no' -eq $yesno) { Return; }
}

### 4. Add a Virtual Service endpoint of type name 'Microsoft.Sql', on your subnet.
$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

# Persist the subnet update.
$vnet = Set-AzVirtualNetwork `
  -VirtualNetwork $vnet;

for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++) {
    $vnet.Subnets[0].ServiceEndpoints; }  # Display.
```

<!-- Link references: -->
[sql-db-vnet-service-endpoint-rule-overview-735r]: sql-database-vnet-service-endpoint-rule-overview.md
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
