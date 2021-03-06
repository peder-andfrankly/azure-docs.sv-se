---
title: Så här konfigurerar du OpenVPN-klienter för Azure VPN Gateway | Microsoft Docs
description: Steg för att konfigurera OpenVPN-klienter för Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 06/14/2019
ms.author: cherylmc
ms.openlocfilehash: 3366f3470e01e455acacf8748830f2b15c826f49
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74997181"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Konfigurera OpenVPN-klienter för Azure VPN Gateway

Den här artikeln hjälper dig att konfigurera **OpenVPN-®-protokoll** klienter.

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Kontrol lera att du har slutfört stegen för att konfigurera OpenVPN för din VPN-gateway. Mer information finns i [konfigurera OpenVPN för Azure VPN gateway](vpn-gateway-howto-openvpn.md).

## <a name="windows"></a>Windows-klienter

1. Hämta och installera OpenVPN-klienten (version 2,4 eller senare) från den officiella [OpenVPN-webbplatsen](https://openvpn.net/index.php/open-source/downloads.html).
2. Ladda ned VPN-profilen för gatewayen. Detta kan göras från fliken punkt-till-plats-konfiguration i Azure Portal, eller "New-AzVpnClientConfiguration" i PowerShell.
3. Packa upp profilen. Öppna sedan konfigurations filen *vpnconfig. ovpn* från mappen OpenVPN med anteckningar.
4. [Exportera](vpn-gateway-certificates-point-to-site.md#clientexport) det P2s-klientcertifikat som du har skapat och laddat upp till din P2s-konfiguration på gatewayen.
5. Extrahera den privata nyckeln och base64-tumavtrycket från *. pfx*-filen. Det finns flera sätt att göra detta på. Att använda OpenSSL på din dator är ett sätt. Filen *profileinfo. txt* innehåller den privata nyckeln och TUMAVTRYCK för ca: n och klient certifikatet. Se till att använda tumavtrycket för klient certifikatet.

   ```
   openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
   ```
6. Öppna *profileinfo. txt* i anteckningar. Om du vill hämta tumavtrycket för klientens (underordnat) certifikat väljer du texten (inklusive och mellan) "-----BEGIN CERTIFICATe-----" och "-----END CERTIFICATe-----" för det underordnade certifikatet och kopierar det. Du kan identifiera det underordnade certifikatet genom att titta på subject =/rad.
7. Växla till filen *vpnconfig. ovpn* som du öppnade i anteckningar från steg 3. Hitta avsnittet som visas nedan och Ersätt allt mellan "cert" och "/cert".

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Öppna filen *profileinfo. txt* i anteckningar. Om du vill hämta den privata nyckeln markerar du texten (inklusive och mellan) "-----börjar privat nyckel-----" och "-----END PRIVATE KEY-----" och kopierar den.
9. Gå tillbaka till filen vpnconfig. ovpn i anteckningar och hitta det här avsnittet. Klistra in den privata nyckeln och Ersätt allt mellan och "Key" och "/Key".

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```
10. Ändra inte några andra fält. Använd den ifyllda konfigurationen i klientindata för att ansluta till VPN.
11. Kopiera filen vpnconfig.ovpn till mappen C:\Program Files\OpenVPN\config.
12. Högerklicka på OpenVPN-ikonen i systemfältet och klicka på Anslut.

## <a name="mac"></a>Mac-klienter

1. Hämta och installera en OpenVPN-klient, till exempel [TunnelBlick](https://tunnelblick.net/downloads.html). 
2. Ladda ned VPN-profilen för gatewayen. Detta kan göras från fliken punkt-till-plats-konfiguration i Azure Portal, eller med hjälp av "New-AzVpnClientConfiguration" i PowerShell.
3. Packa upp profilen. Öppna konfigurations filen vpnconfig. ovpn från mappen OpenVPN i anteckningar.
4. Fyll i avsnittet för P2S-klientcertifikatet med P2S-klientcertifikatets offentliga nyckel i base64. I ett PEM-formaterat certifikat öppnar du bara CER-filen och kopierar över base64-nyckeln mellan certifikathuvudena. Se [Exportera den offentliga nyckeln](vpn-gateway-certificates-point-to-site.md#cer) för information om hur du exporterar ett certifikat för att hämta den kodade offentliga nyckeln.
5. Fyll i avsnittet för den privata nyckeln med P2S-klientcertifikatets privata nyckel i base64. Mer information om hur du extraherar en privat nyckel finns i [exportera din privata nyckel](https://openvpn.net/community-resources/how-to/#pki) .
6. Ändra inte några andra fält. Använd den ifyllda konfigurationen i klientindata för att ansluta till VPN.
7. Dubbelklicka på profil filen för att skapa profilen i Tunnelblick.
8. Starta Tunnelblick från mappen program.
9. Klicka på ikonen Tunnelblick i system fältet och Välj Anslut.

> [!IMPORTANT]
>Endast iOS 11,0 och senare och MacOS 10,13 och senare stöds med OpenVPN-protokollet.
>

## <a name="linux"></a>Linux-klienter

1. Öppna en ny Terminal-session. Du kan öppna en ny session genom att trycka på Ctrl + Alt + t samtidigt.
2. Ange följande kommando för att installera nödvändiga komponenter:

   ```
   sudo apt-get install openvpn
   sudo apt-get -y install network-manager-openvpn
   sudo service network-manager restart
   ```
3. Ladda ned VPN-profilen för gatewayen. Detta kan göras från fliken punkt-till-plats-konfiguration i Azure Portal.
4. [Exportera](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-certificates-point-to-site#clientexport) det P2s-klientcertifikat som du har skapat och laddat upp till din P2s-konfiguration på gatewayen. 
5. Extrahera den privata nyckeln och base64-tumavtrycket från. pfx-filen. Det finns flera sätt att göra detta på. Att använda OpenSSL på din dator är ett sätt.

    ```
    openssl.exe pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
    ```
   Filen *profileinfo. txt* kommer att innehålla den privata nyckeln och TUMAVTRYCK för ca: n och klient certifikatet. Se till att använda tumavtrycket för klient certifikatet.

6. Öppna *profileinfo. txt* i en text redigerare. Om du vill hämta tumavtrycket för klientens (underordnat) certifikat väljer du texten inklusive och mellan "-----BEGIN CERTIFICATe-----" och "-----END CERTIFICATe-----" för det underordnade certifikatet och kopierar det. Du kan identifiera det underordnade certifikatet genom att titta på subject =/rad.

7. Öppna filen *vpnconfig. ovpn* och hitta avsnittet som visas nedan. Ersätt allt mellan och "cert" och "/cert".

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Öppna profileinfo. txt i en text redigerare. Om du vill hämta den privata nyckeln markerar du texten inklusive och mellan "-----börjar privat nyckel-----" och "-----avsluta den privata nyckeln-----" och kopierar den.

9. Öppna filen vpnconfig. ovpn i en text redigerare och leta upp det här avsnittet. Klistra in den privata nyckeln och Ersätt allt mellan och "Key" och "/Key".

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```

10. Ändra inte några andra fält. Använd den ifyllda konfigurationen i klientindata för att ansluta till VPN.
11. Om du vill ansluta med kommando raden skriver du följande kommando:
  
    ```
    sudo openvpn –-config <name and path of your VPN profile file>&
    ```
12. Om du vill ansluta med det grafiska användar gränssnittet går du till Systeminställningar.
13. Klicka på **+** för att lägga till en ny VPN-anslutning.
14. Välj **Importera från fil** under **Lägg till VPN**.
15. Bläddra till profil filen och dubbelklicka på eller Välj **Öppna**.
16. Klicka på **Lägg till** i fönstret **Lägg till VPN** .
  
    ![Importera från fil](./media/vpn-gateway-howto-openvpn-clients/importfromfile.png)
17. Du kan ansluta genom att aktivera VPN **på** sidan **nätverks inställningar** eller under nätverks ikonen i system fältet.

## <a name="next-steps"></a>Nästa steg

Om du vill att VPN-klienter ska kunna komma åt resurser i ett annat VNet följer du anvisningarna i artikeln [VNet-till-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) för att konfigurera en VNet-till-VNET-anslutning. Se till att aktivera BGP på gatewayarna och anslutningarna, annars flödar inte trafiken.

**"OpenVPN" är ett varumärke som tillhör OpenVPN Inc.**
