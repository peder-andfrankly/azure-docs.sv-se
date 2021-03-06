---
title: Metoder för att skapa certifikat
description: Sätt att skapa ett certifikat i Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 908f02807d5a3f7c2c1391c3c59a54fc88bbd831
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70884157"
---
# <a name="certificate-creation-methods"></a>Metoder för att skapa certifikat

 Ett Key Vault (KV)-certifikat kan antingen skapas eller importeras till ett nyckel valv. När ett KV-certifikat skapas, skapas den privata nyckeln i nyckel valvet och aldrig exponeras för certifikat ägaren. Följande är sätt att skapa ett certifikat i Key Vault:  

-   **Skapa ett självsignerat certifikat:** Då skapas ett offentligt privat privat nyckel par och associeras med ett certifikat. Certifikatet kommer att signeras av en egen nyckel.  

-    **Skapa ett nytt certifikat manuellt:** Detta skapar en offentlig och privat nyckel och genererar en begäran om att signera en X. 509-certifikat. Signerings förfrågan kan signeras av din registrerings utfärdare eller certifikat utfärdare. Det signerade x509-certifikatet kan slås samman med det väntande nyckel paret för att slutföra KV-certifikatet i Key Vault. Även om den här metoden kräver fler steg ger den bättre säkerhet eftersom den privata nyckeln skapas i och är begränsad till Key Vault. Detta beskrivs i diagrammet nedan.  

![Skapa ett certifikat med din egen certifikat utfärdare](media/certificate-authority-1.png)  

Följande beskrivningar motsvarar de gröna bokstavs stegen i föregående diagram.

1. I diagrammet ovan skapar programmet ett certifikat som börjar med att skapa en nyckel i ditt nyckel valv.
2. Key Vault återgår till programmet en certifikat signerings förfrågan (CSR)
3. Ditt program skickar CSR till din valda CA.
4. Din valda CA svarar med ett X509-certifikat.
5. Ditt program Slutför den nya skapande av certifikatet med en sammanslagning av X509-certifikatet från din certifikat utfärdare.

-   **Skapa ett certifikat med en känd utfärdare-provider:** Den här metoden kräver att du utför en eng ång slö aktivitet för att skapa ett Issuer-objekt. När ett Issuer-objekt skapas i ditt nyckel valv, kan namnet refereras till i principen för KV-certifikatet. En begäran om att skapa ett sådant KV-certifikat skapar ett nyckel par i valvet och kommunicerar med tjänsten Issuer Provider med hjälp av informationen i det refererade Issuer-objektet för att hämta ett x509-certifikat. X509-certifikatet hämtas från Issuer-tjänsten och slås samman med nyckel paret för att slutföra skapandet av KV-certifikatet.  

![Skapa ett certifikat med en Key Vault-partner certifikat utfärdare](media/certificate-authority-2.png)  

Följande beskrivningar motsvarar de gröna bokstavs stegen i föregående diagram.

1. I diagrammet ovan skapar programmet ett certifikat som börjar med att skapa en nyckel i ditt nyckel valv.
2. Key Vault skickar och SSL-certifikatbegäran till certifikat utfärdaren.
3. Programmet avsöker, i en slinga och väntar, för din Key Vault för att slutföra certifikatet. Certifikatet har skapats när Key Vault tar emot CA: ns svar med x509-certifikat.
4. CA: n svarar på Key Vault SSL-certifikatbegäran med ett X509 SSL-certifikat.
5. Ditt nya certifikat har skapats med sammanslagningen av X509-certifikatet för CA: n.

## <a name="asynchronous-process"></a>Asynkron process
Att skapa KV-certifikat är en asynkron process. Den här åtgärden skapar en KV-certifikatbegäran och returnerar HTTP-statuskoden 202 (accepterad). Status för begäran kan spåras genom att avsöka det väntande objekt som skapas av den här åtgärden. Den fullständiga URI: n för det väntande objektet returneras i plats rubriken.  

När en begäran om att skapa ett KV-certifikat har slutförts ändras statusen för det väntande objektet till "slutförd" från "pågår" och en ny version av KV-certifikatet skapas. Detta kommer att bli den aktuella versionen.  

## <a name="first-creation"></a>Första skapande
 När ett KV-certifikat skapas för första gången skapas även en adresserad nyckel och hemlighet med samma namn som certifikatet. Om namnet redan används kan åtgärden inte utföras med HTTP-statuskod 409 (konflikt).
Den adresser bara nyckeln och hemligheten hämtar attributen från attributen för KV-certifikat. Den adresser bara nyckeln och hemligheten som skapas på det här sättet har marker ATS som hanterade nycklar och hemligheter, vars livs längd hanteras av Key Vault. Hanterade nycklar och hemligheter är skrivskyddade. Obs! Om ett KV-certifikat går ut eller inaktive ras, kommer motsvarande nyckel och hemlighet att sluta fungera.  

 Om det här är den första åtgärden för att skapa ett KV-certifikat krävs en princip.  En princip kan också tillhandahållas med efterföljande skapande åtgärder för att ersätta princip resursen. Om ingen princip anges används princip resursen på tjänsten för att skapa en nästa version av KV-certifikatet. Observera att när en begäran om att skapa en nästa version pågår, är det aktuella KV-certifikatet och motsvarande adresser bara nyckel och hemlighet oförändrat.  

## <a name="self-issued-certificate"></a>Självutfärdat certifikat
 Om du vill skapa ett självutfärdat certifikat ställer du in utfärdarens namn som "Self" i certifikat principen som visas i följande kodfragment från certifikat principen.  

```  
"issuer": {  
       "name": "Self"  
    }  

```  

 Om utfärdarens namn inte anges anges utfärdarens namn till "okänd". När utfärdaren är "okänd" måste certifikat ägaren manuellt hämta ett x509-certifikat från utfärdaren av hans/hennes val och sedan sammanfoga det offentliga x509-certifikatet med det väntande objektet i Key Vault-certifikatet för att slutföra det skapade certifikatet.

```  
"issuer": {  
       "name": "Unknown"  
    }  

```  

## <a name="partnered-ca-providers"></a>Partner leverantörer av certifikat utfärdare
Skapande av certifikat kan slutföras manuellt eller med en "egen" utfärdare. Key Vault också partner med vissa utfärdare för att förenkla skapandet av certifikat. Följande typer av certifikat kan beställas för nyckel valv med dessa leverantörer av partner utfärdare.  

|Leverantör|Certifikat typ|  
|--------------|----------------------|  
|DigiCert|Key Vault erbjuder OV eller EV SSL-certifikat med DigiCert|
|GlobalSign|Key Vault erbjuder OV eller EV SSL-certifikat med GlobalSign|

 En certifikat utfärdare är en entitet som representeras i Azure Key Vault (KV) som en CertificateIssuer-resurs. Den används för att tillhandahålla information om källan till ett KV-certifikat; utfärdarens namn, Provider, autentiseringsuppgifter och annan administrativ information.

Observera att när en beställning placeras hos utfärdaren kan den ta eller åsidosätta x509-certifikatets tillägg och certifikatets giltighets period baserat på certifikat typen.  

 Auktoriseringsregeln Kräver behörigheten certifikat/skapa.

## <a name="see-also"></a>Se även
 - [Om nycklar, hemligheter och certifikat](about-keys-secrets-and-certificates.md)
 - [Övervaka och hantera processen för att skapa certifikat](create-certificate-scenarios.md)
