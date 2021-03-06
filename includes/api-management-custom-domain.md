---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 11/09/2018
ms.author: vlvinogr
ms.openlocfilehash: dff01f8bc4a4cf58d1ed503b69a29dadc367fecb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66248799"
---
## <a name="how-apim-proxy-server-responds-with-ssl-certificates-in-the-tls-handshake"></a>Så här APIM-proxyserver svarar med SSL-certifikat i TLS-handskakning

### <a name="clients-calling-with-sni-header"></a>Klienter som anropar med SNI-huvud
Om kunden har en eller flera anpassade domäner som konfigurerats för Proxy, APIM kan svara på HTTPS-begäranden från anpassade domäner (till exempel contoso.com) samt standarddomän (till exempel apim-tjänst-name.azure-api.net). Baserat på informationen i rubriken (Servernamnindikator), svarar APIM med lämplig servercertifikat.

### <a name="clients-calling-without-sni-header"></a>Klienter som anropar utan SNI-huvud
Om kunden använder en klient som inte skickar den [SNI](https://tools.ietf.org/html/rfc6066#section-3) rubrik, skapar APIM-svar baserade på följande logik:

* Om tjänsten har endast en anpassad domän som konfigurerats för Proxy, är standard-certifikatet det certifikat som utfärdades till den anpassade domänen för Proxy.
* Om tjänsten har konfigurerats flera anpassade domäner för Proxy (stöds bara i den **Premium** nivå), kunden kan utse vilket certifikat som ska vara standardcertifikatet. Ange standard-certifikatet i [defaultSslBinding](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/apimanagementservice/createorupdate#hostnameconfiguration) egenskapen ska anges till true (”defaultSslBinding”: ”true”). Om kunden inte har angetts för egenskapen är standardcertifikatet det certifikat som utfärdats till Proxy standarddomän finns på *.azure api.net.

## <a name="support-for-putpost-request-with-large-payload"></a>Stöd för PUT/POST-begäran med stor nyttolast

Begäran med stor nyttolast har stöd för APIM-proxyserver när du använder certifikat för klientsidan i HTTPS (till exempel nyttolast > 40 KB). Om du vill förhindra att serverns begäran låser sig. kunder kan ange egenskapen [”negotiateClientCertificate”: ”true”](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/ApiManagementService/CreateOrUpdate#hostnameconfiguration) på värdnamn för Proxy. Om egenskapen har värdet true, klienten certifikatet har begärts vid anslutningstillfället för SSL/TLS, innan exchange alla HTTP-begäran. Eftersom inställningen som gäller vid den **värdnamn för Proxy** loggningsnivån genom alla anslutningsförfrågningar be om klientcertifikatet. Kunder kan konfigurera upp till 20 anpassade domäner för Proxy (stöds bara i den **Premium** nivån) och lösa problemet.

