---
title: Konfigurera en VPN-tunnel som alltid är på
titleSuffix: Azure VPN Gateway
description: Steg för att konfigurera Always on VPN-tunnel för VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: cherylmc
ms.openlocfilehash: f22b29cfcaf1d4c4ce28b2b0557d70b281b6891f
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146394"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>Konfigurera en VPN-enhetstunnel för AlwaysOn

En av de nya funktionerna i VPN-klienten (Virtual Private Network) i Windows 10 är möjligheten att underhålla en VPN-anslutning. Always On är en funktion i Windows 10 som gör det möjligt för den aktiva VPN-profilen att ansluta automatiskt och förblir ansluten baserat på utlösare – nämligen användar inloggning, ändring av nätverks tillstånd eller enhets skärm aktiv.

Virtuella Azure-nätverksgateway kan användas med Windows 10 Always on för att upprätta permanenta användar tunnlar samt enhets tunnlar till Azure. Den här artikeln hjälper dig att konfigurera en tunnel för Always ON VPN-enheter.

Alltid på VPN-anslutningar är två typer av tunnlar:

* **Enhets tunneln** ansluter till angivna VPN-servrar innan användarna loggar in på enheten. Anslutnings scenarier för inloggning och enhets hantering använder enhets tunnel.

* **Användar tunneln** ansluter bara efter att en användare har loggat in på enheten. Användar tunnel ger användare åtkomst till organisations resurser via VPN-servrar.

Både enhets tunnel och användar tunnel körs oberoende av sina VPN-profiler. De kan vara anslutna samtidigt, och kan använda olika autentiseringsmetoder och andra inställningar för VPN-konfigurationen efter behov.

## <a name="1-configure-the-gateway"></a>1. Konfigurera gatewayen

Konfigurera VPN-gatewayen att använda IKEv2 och certifikatbaserad autentisering med hjälp av den här [punkt-till-plats-artikeln](vpn-gateway-howto-point-to-site-resource-manager-portal.md).

## <a name="2-configure-the-device-tunnel"></a>2. Konfigurera enhets tunneln

Följande krav måste uppfyllas för att du ska kunna upprätta en enhets tunnel:

* Enheten måste vara en domänansluten dator som kör Windows 10 Enterprise eller Education version 1709 eller senare.
* Tunneln kan bara konfigureras för den inbyggda VPN-lösningen i Windows och upprättas med hjälp av IKEv2 med autentisering med dator certifikat. 
* Endast en enhets tunnel kan konfigureras per enhet.

1. Installera klient certifikat på Windows 10-klienten som visas i [artikeln punkt-till-plats-VPN-klient](point-to-site-how-to-vpn-client-install-azure-cert.md). Certifikatet måste finnas i den lokala datorns Arkiv.
1. Använd [de här anvisningarna](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration) för att skapa en VPN-profil och konfigurera enhets tunneln i kontexten för det lokala system kontot.

### <a name="configuration-example-for-device-tunnel"></a>Konfigurations exempel för enhets tunnel

När du har konfigurerat den virtuella Nätverksgatewayen och installerat klient certifikatet i den lokala datorns Arkiv på Windows 10-klienten, använder du följande exempel för att konfigurera en klient enhets tunnel.

1. Kopiera följande text och spara den som ***devicecert. ps1***.

   ```
   Param(
   [string]$xmlFilePath,
   [string]$ProfileName
   )

   $a = Test-Path $xmlFilePath
   echo $a

   $ProfileXML = Get-Content $xmlFilePath

   echo $XML

   $ProfileNameEscaped = $ProfileName -replace ' ', '%20'

   $Version = 201606090004

   $ProfileXML = $ProfileXML -replace '<', '&lt;'
   $ProfileXML = $ProfileXML -replace '>', '&gt;'
   $ProfileXML = $ProfileXML -replace '"', '&quot;'

   $nodeCSPURI = './Vendor/MSFT/VPNv2'
   $namespaceName = "root\cimv2\mdm\dmmap"
   $className = "MDM_VPNv2_01"

   $session = New-CimSession

   try
   {
   $newInstance = New-Object Microsoft.Management.Infrastructure.CimInstance $className, $namespaceName
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ParentID", "$nodeCSPURI", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("InstanceID", "$ProfileNameEscaped", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ProfileXML", "$ProfileXML", 'String', 'Property')
   $newInstance.CimInstanceProperties.Add($property)

   $session.CreateInstance($namespaceName, $newInstance)
   $Message = "Created $ProfileName profile."
   Write-Host "$Message"
   }
   catch [Exception]
   {
   $Message = "Unable to create $ProfileName profile: $_"
   Write-Host "$Message"
   exit
   }
   $Message = "Complete."
   Write-Host "$Message"
   ```
1. Kopiera följande text och spara den som ***VPNProfile. XML*** i samma mapp som **devicecert. ps1**. Redigera följande text för att matcha din miljö.

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>`
   * `<Address>192.168.3.5</Address>`
   * `<Address>192.168.3.4</Address>`

   ```
   <VPNProfile>  
     <NativeProfile>  
   <Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>  
   <NativeProtocolType>IKEv2</NativeProtocolType>  
   <Authentication>  
     <MachineMethod>Certificate</MachineMethod>  
   </Authentication>  
   <RoutingPolicyType>SplitTunnel</RoutingPolicyType>  
    <!-- disable the addition of a class based route for the assigned IP address on the VPN interface -->
   <DisableClassBasedDefaultRoute>true</DisableClassBasedDefaultRoute>  
     </NativeProfile> 
     <!-- use host routes(/32) to prevent routing conflicts -->  
     <Route>  
   <Address>192.168.3.5</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
     <Route>  
   <Address>192.168.3.4</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
   <!-- need to specify always on = true --> 
     <AlwaysOn>true</AlwaysOn> 
   <!-- new node to specify that this is a device tunnel -->  
    <DeviceTunnel>true</DeviceTunnel>
   <!--new node to register client IP address in DNS to enable manage out -->
   <RegisterDNS>true</RegisterDNS>
   </VPNProfile>
   ```
1. Ladda ned **PsExec** från [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) och extrahera filerna till **C:\PSTools**.
1. Starta PowerShell från en administratörs kommando tolk genom att köra:

   ```
   C:\PsTools\PsExec.exe Powershell for 32-bit Windows
   C:\PsTools\PsExec64.exe Powershell for 64-bit Windows
   ```

   ![PowerShell](./media/vpn-gateway-howto-always-on-device-tunnel/powershell.png)
1. I PowerShell växlar du till den mapp där **devicecert. ps1** och **VPNProfile. XML** finns och kör följande kommando:

   ```powershell
   C:\> .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-device-tunnel/machinecerttest.png)
1. Kör **Rasphone**.

   ![RASPHONE](./media/vpn-gateway-howto-always-on-device-tunnel/rasphone.png)
1. Leta efter **MachineCertTest** -posten och klicka på **Anslut**.

   ![Anslut](./media/vpn-gateway-howto-always-on-device-tunnel/connect.png)
1. Om anslutningen lyckas startar du om datorn. Tunneln kommer att ansluta automatiskt.

## <a name="cleanup"></a>Rensa

Om du vill ta bort profilen kör du följande kommando:

![Rensa](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Nästa steg

Information om fel sökning finns i [problem med Azure punkt-till-plats-anslutning](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
