---
title: Förstå support för Azure IoT Hub MQTT | Microsoft Docs
description: 'Guide för utvecklare – stöd för enheter som ansluter till en IoT Hub enhets slut punkt med MQTT-protokollet. Innehåller information om inbyggt MQTT-stöd i SDK: er för Azure IoT-enheter.'
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: robinsh
ms.openlocfilehash: 183b85ad8a61c76942981ebb764512b8a090b0a8
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890446"
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>Kommunicera med IoT-hubben med MQTT-protokollet

IoT Hub gör det möjligt för enheter att kommunicera med IoT Hub enhets slut punkter med:

* [MQTT v 3.1.1](https://mqtt.org/) på port 8883
* MQTT v 3.1.1 över WebSocket på port 443.

IoT Hub är inte en komplett MQTT-Broker och har inte stöd för alla beteenden som anges i MQTT v 3.1.1-standarden. I den här artikeln beskrivs hur enheter kan använda MQTT-beteenden som stöds för att kommunicera med IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

All enhets kommunikation med IoT Hub måste skyddas med TLS/SSL. Därför stöder IoT Hub inte anslutningar som inte är säkra via port 1883.

## <a name="connecting-to-iot-hub"></a>Ansluter till IoT Hub

En enhet kan använda MQTT-protokollet för att ansluta till en IoT-hubb med något av följande alternativ.

* Bibliotek i [Azure IoT-SDK](https://github.com/Azure/azure-iot-sdks): er.
* MQTT-protokollet direkt.

## <a name="using-the-device-sdks"></a>Använda enhets-SDK: er

[Enhets-SDK](https://github.com/Azure/azure-iot-sdks) : er som stöder MQTT-protokollet är tillgängligt för Java, Node. C#js, C, och python. Enhets-SDK: erna använder standard IoT Hub anslutnings sträng för att upprätta en anslutning till en IoT-hubb. Om du vill använda MQTT-protokollet måste klient protokoll parametern vara inställd på **MQTT**. Som standard ansluter enhets-SDK: erna till en IoT Hub med flaggan **CleanSession** inställd på **0** och använder **QoS 1** för meddelande utbyte med IoT Hub.

När en enhet är ansluten till en IoT-hubb ger enhets-SDK metoder som gör det möjligt för enheten att utbyta meddelanden med en IoT-hubb.

Följande tabell innehåller länkar till kod exempel för varje språk som stöds och anger den parameter som ska användas för att upprätta en anslutning till IoT Hub med MQTT-protokollet.

| Språk | Protokoll parameter |
| --- | --- |
| [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js) |Azure-IoT-Device-MQTT |
| [Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java) |IotHubClientProtocol. MQTT |
| [C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm) |MQTT_Protocol |
| [C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples) |TransportType. MQTT |
| [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) |Stöder alltid MQTT som standard |

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Migrera en enhets app från AMQP till MQTT

Om du använder [enhets-SDK: erna](https://github.com/Azure/azure-iot-sdks)måste du växla från att använda AMQP till MQTT för att ändra protokoll parametern i klient initieringen som du angav tidigare.

När du gör det, se till att kontrol lera följande objekt:

* AMQP returnerar fel för många villkor, medan MQTT avslutar anslutningen. Som ett resultat kan din strategi för undantags hantering kräva vissa ändringar.

* MQTT stöder inte *avvisnings* åtgärder vid mottagning av [meddelanden från molnet till enheten](iot-hub-devguide-messaging.md). Om din backend-app måste ta emot ett svar från Device-appen bör du överväga att använda [direkta metoder](iot-hub-devguide-direct-methods.md).

* AMQP stöds inte i python SDK

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Använda MQTT-protokollet direkt (som en enhet)

Om en enhet inte kan använda enhets-SDK: erna kan den fortfarande ansluta till den offentliga enhetens slut punkter med MQTT-protokollet på port 8883. I **Connect** -paketet ska enheten använda följande värden:

* Använd **deviceId**för fältet **ClientId** .

* För fältet **användar namn** använder du `{iothubhostname}/{device_id}/?api-version=2018-06-30`, där `{iothubhostname}` är det fullständiga CNAME för IoT Hub.

    Om namnet på din IoT Hub t. ex. är **contoso.Azure-Devices.net** och om namnet på enheten är **MyDevice01**, ska fältet fullständigt **användar namn** innehålla:

    `contoso.azure-devices.net/MyDevice01/?api-version=2018-06-30`

* I fältet **lösen ord** använder du en SAS-token. Formatet på SAS-token är detsamma som för både HTTPS-och AMQP-protokollen:

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > Om du använder autentisering med X. 509-certifikat krävs inte lösen ord för SAS-token. Mer information finns i [Konfigurera X. 509-säkerhet i Azure IoT Hub](iot-hub-security-x509-get-started.md) och följ kod anvisningarna [nedan](#tlsssl-configuration).

  Mer information om hur du genererar SAS-token finns i enhets avsnittet i [använda IoT Hub säkerhetstoken](iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app).

  När du testar kan du också använda plattforms oberoende [Azure IoT-verktyg för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) eller [Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) -verktyget för att snabbt skapa en SAS-token som du kan kopiera och klistra in i din egen kod:

### <a name="for-azure-iot-tools"></a>För Azure IoT-verktyg

1. Expandera fliken **Azure IoT Hub-enheter** i det nedre vänstra hörnet av Visual Studio Code.
  
2. Högerklicka på enheten och välj **skapa SAS-token för enheten**.
  
3. Ange **förfallo tid** och tryck på RETUR.
  
4. SAS-token skapas och kopieras till Urklipp.

### <a name="for-device-explorer"></a>För Device Explorer

1. Gå till fliken **hantering** i **Device Explorer**.

2. Klicka på **SAS-token** (längst upp till höger).

3. På **SASTokenForm**väljer du din enhet i list rutan **DeviceID** . Ange din **TTL**.

4. Klicka på **generera** för att skapa din token.

   SAS-token som genereras har följande struktur:

   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

   Den del av denna token som ska användas som **lösen ords** fält för att ansluta med MQTT är:

   `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

För att MQTT ska kunna ansluta och koppla från paket utfärdar IoT Hub en händelse på **drift övervaknings** kanalen. Den här händelsen innehåller ytterligare information som kan hjälpa dig att felsöka anslutnings problem.

Device-appen kan ange ett **meddelande i** **Connect** -paketet. Device-appen bör använda `devices/{device_id}/messages/events/` eller `devices/{device_id}/messages/events/{property_bag}` **som ämnes namn** för **att definiera meddelanden** som ska vidarebefordras som ett telemetri meddelande. I så fall, om nätverks anslutningen är stängd, men ett **från kopplings** paket inte tidigare togs emot från enheten, IoT Hub **skickar meddelandet som** anges i **Connect** -paketet till telemetri-kanalen. Telemetri-kanalen kan vara antingen standard slut punkt för **händelser** eller en anpassad slut punkt som definieras av IoT Hub routning. Meddelandet har egenskapen **iothub-MessageType** med värdet **som tilldelas.**

### <a name="an-example-of-c-code-using-mqtt-without-azure-iot-c-sdk"></a>Ett exempel på C-kod med MQTT utan Azure IoT C SDK
I den här [lagrings platsen](https://github.com/Azure-Samples/IoTMQTTSample)hittar du ett par C/C++ demo-projekt som visar hur du skickar telemetridata, tar emot händelser med IoT Hub utan att använda Azure IoT C SDK. 

I de här exemplen används Mosquitto-biblioteket för Sol förmörkelse för att skicka meddelandet till MQTT-utjämningen som implementerats i IoT Hub.

Den här lagrings platsen innehåller:

**För Windows:**

• TelemetryMQTTWin32: innehåller kod för att skicka ett telemetri-meddelande till en Azure IoT Hub som skapats och körs på en Windows-dator.

• SubscribeMQTTWin32: innehåller kod för att prenumerera på händelser för en viss IoT-hubb på en Windows-dator.

• DeviceTwinMQTTWin32: innehåller kod för att fråga och prenumerera på enhetens dubbla händelser på en enhet i Azure IoT Hub på en Windows-dator.

• PnPMQTTWin32: innehåller kod för att skicka ett telemetri-meddelande med IoT Plug & Play-funktioner för för hands version till en Azure IoT-hubb som skapats och körs på en Windows-dator. Mer på IoT-plugin & spela [här](https://docs.microsoft.com/azure/iot-pnp/overview-iot-plug-and-play)

**För Linux:**

• MQTTLinux: innehåller kod och build-skript som ska köras på Linux (WSL, Ubuntu och Raspbian har testats hittills).

• LinuxConsoleVS2019: innehåller samma kod men i ett VS2019 Project targeting WSL (Windows Linux-undersystem). Med det här projektet kan du felsöka koden som körs i Linux steg för steg från Visual Studio.

**För mosquito_pub:**

• Den här mappen innehåller två exempel kommandon som används med mosquitto_pub verktygs verktyget från Mosquitto.org.

Mosquitto_sendmessage: för att skicka ett enkelt textmeddelande till en Azure IoT-hubb som fungerar som en enhet.

Mosquitto_subscribe: om du vill se händelser som inträffar i en Azure IoT Hub.


## <a name="using-the-mqtt-protocol-directly-as-a-module"></a>Använda MQTT-protokollet direkt (som en modul)

Att ansluta till IoT Hub över MQTT med hjälp av en moduls identitet liknar enheten (beskrivs [ovan](#using-the-mqtt-protocol-directly-as-a-device)) men du måste använda följande:

* Ange klient-ID: t till `{device_id}/{module_id}`.

* Om du autentiserar med användar namn och lösen ord anger du användar namnet `<hubname>.azure-devices.net/{device_id}/{module_id}/?api-version=2018-06-30` och använder SAS-token som är associerad med modulens identitet som lösen ord.

* Använd `devices/{device_id}/modules/{module_id}/messages/events/` som ämne för att publicera telemetri.

* Använd `devices/{device_id}/modules/{module_id}/messages/events/` som ämne.

* De dubbla GET-och PATCH-avsnitten är identiska för moduler och enheter.

* Avsnittet med dubbla status är identiskt för moduler och enheter.

## <a name="tlsssl-configuration"></a>TLS/SSL-konfiguration

Om du vill använda MQTT-protokollet direkt *måste* klienten ansluta via TLS/SSL. Försök att hoppa över det här steget Miss lyckas med anslutnings fel.

För att upprätta en TLS-anslutning kan du behöva ladda ned och referera till DigiCert Baltimore-rotcertifikatet. Det här certifikatet är det som Azure använder för att skydda anslutningen. Du kan hitta det här certifikatet i [Azure-IoT-SDK-c-](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) lagringsplatsen. Mer information om dessa certifikat finns på [DigiCert webbplats](https://www.digicert.com/digicert-root-certificates.htm).

Ett exempel på hur du implementerar detta med python-versionen av [PAHO MQTT-biblioteket](https://pypi.python.org/pypi/paho-mqtt) av sol förmörkelses stiftelsen kan se ut så här.

Först installerar du PAHO-biblioteket från kommando rads miljön:

```cmd/sh
pip install paho-mqtt
```

Implementera sedan klienten i ett Python-skript. Ersätt plats hållarna enligt följande:

* `<local path to digicert.cer>` är sökvägen till en lokal fil som innehåller rot certifikatet för DigiCert-Baltimore. Du kan skapa den här filen genom att kopiera certifikat informationen från [certifikaten. c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) i Azure IoT SDK för c. ta med raderna `-----BEGIN CERTIFICATE-----` och `-----END CERTIFICATE-----`, ta bort `"` märken i början och slutet av varje rad och ta bort `\r\n` tecknen i slutet av varje rad.

* `<device id from device registry>` är ID: t för en enhet som du har lagt till i din IoT Hub.

* `<generated SAS token>` är en SAS-token för enheten som skapats på det sätt som beskrivs ovan i den här artikeln.

* `<iot hub name>` namnet på din IoT-hubb.

```python
from paho.mqtt import client as mqtt
import ssl

path_to_root_cert = "<local path to digicert.cer file>"
device_id = "<device id from device registry>"
sas_token = "<generated SAS token>"
iot_hub_name = "<iot hub name>"


def on_connect(client, userdata, flags, rc):
    print("Device connected with result code: " + str(rc))


def on_disconnect(client, userdata, rc):
    print("Device disconnected with result code: " + str(rc))


def on_publish(client, userdata, mid):
    print("Device sent message")


client = mqtt.Client(client_id=device_id, protocol=mqtt.MQTTv311)

client.on_connect = on_connect
client.on_disconnect = on_disconnect
client.on_publish = on_publish

client.username_pw_set(username=iot_hub_name+".azure-devices.net/" +
                       device_id + "/?api-version=2018-06-30", password=sas_token)

client.tls_set(ca_certs=path_to_root_cert, certfile=None, keyfile=None,
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1_2, ciphers=None)
client.tls_insecure_set(False)

client.connect(iot_hub_name+".azure-devices.net", port=8883)

client.publish("devices/" + device_id + "/messages/events/", "{id=123}", qos=1)
client.loop_forever()
```

Följande är installations anvisningarna för kraven.

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

Om du vill autentisera med ett enhets certifikat uppdaterar du kodfragmentet ovan med följande ändringar (se [så här hämtar du ett X. 509 CA-certifikat](./iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate) om hur du förbereder för certifikatbaserad autentisering):

```python
# Create the client as before
# ...

# Set the username but not the password on your client
client.username_pw_set(username=iot_hub_name+".azure-devices.net/" +
                       device_id + "/?api-version=2018-06-30", password=None)

# Set the certificate and key paths on your client
cert_file = "<local path to your certificate file>"
key_file = "<local path to your device key file>"
client.tls_set(ca_certs=path_to_root_cert, certfile=cert_file, keyfile=key_file,
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1_2, ciphers=None)

# Connect as before
client.connect(iot_hub_name+".azure-devices.net", port=8883)
```

## <a name="sending-device-to-cloud-messages"></a>Skicka meddelanden från enhet till moln

När anslutningen har upprättats kan en enhet skicka meddelanden till IoT Hub med hjälp av `devices/{device_id}/messages/events/` eller `devices/{device_id}/messages/events/{property_bag}` som **ämnes namn**. Med `{property_bag}`-elementet kan enheten skicka meddelanden med ytterligare egenskaper i ett URL-kodat format. Till exempel:

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> Det här `{property_bag}`-elementet använder samma kodning som frågesträngar i HTTPS-protokollet.

Följande är en lista över IoT Hub implementations-speciella beteenden:

* IoT Hub stöder inte QoS 2-meddelanden. Om en enhets app publicerar ett meddelande med **QoS 2**, stänger IoT Hub nätverks anslutningen.

* IoT Hub bevarar inte Kvarhåll meddelanden. Om en enhet skickar ett meddelande med flaggan **Behåll** flagga inställd på IoT Hub 1 läggs egenskapen **x-opt-kvarhållning** till i meddelandet. I det här fallet i stället för att behålla det kvarhållna meddelandet skickar IoT Hub det till backend-appen.

* IoT Hub stöder endast en aktiv MQTT-anslutning per enhet. Alla nya MQTT-anslutningar på uppdrag av samma enhets-ID gör att IoT Hub släppa den befintliga anslutningen.

Mer information finns i [meddelande guide för utvecklare](iot-hub-devguide-messaging.md).

## <a name="receiving-cloud-to-device-messages"></a>Ta emot meddelanden från moln till enhet

Om du vill ta emot meddelanden från IoT Hub bör en enhet prenumerera med `devices/{device_id}/messages/devicebound/#` som ett **ämnes filter**. Jokertecken på flera nivåer `#` i ämnes filtret används bara för att tillåta att enheten tar emot ytterligare egenskaper i ämnes namnet. IoT Hub tillåter inte användning av `#` eller `?` jokertecken för filtrering av underavsnitt. Eftersom IoT Hub inte är en generell publicerings-och publicerings tjänst för pub, stöder den bara de dokumenterade ämnes namn och ämnes filter.

Enheten får inga meddelanden från IoT Hub förrän den har prenumererat på den enhetsspecifika slut punkten som representeras av `devices/{device_id}/messages/devicebound/#` ämnes filtret. När en prenumeration har upprättats tar enheten emot meddelanden från molnet till enheten som skickades till den efter prenumerations tiden. Om enheten ansluter med flaggan **CleanSession** inställd på **0**behålls prenumerationen mellan olika sessioner. I det här fallet, nästa gången enheten ansluter med **CleanSession 0** , tar den emot eventuella väntande meddelanden som skickas till den medan den kopplades från. Om enheten använder **CleanSession** -flaggan inställd på **1** , tar den inte emot några meddelanden från IoT Hub förrän den prenumererar på sin enhets slut punkt.

IoT Hub levererar meddelanden med **ämnes namnet** `devices/{device_id}/messages/devicebound/`eller `devices/{device_id}/messages/devicebound/{property_bag}` när det finns meddelande egenskaper. `{property_bag}` innehåller URL-kodade nyckel/värde-par med meddelande egenskaper. Endast program egenskaper och system egenskaper som kan anges av användaren (till exempel **messageId** eller **correlationId**) ingår i egenskaps uppsättningen. System egenskaps namn har prefixet **$** , program egenskaperna använder det ursprungliga egenskaps namnet utan prefix.

När en enhets app prenumererar på ett ämne med **QoS 2**, tilldelar IoT Hub högsta QoS-nivå 1 i **SUBACK** -paketet. Därefter levererar IoT Hub meddelanden till enheten med QoS 1.

## <a name="retrieving-a-device-twins-properties"></a>Hämta en enhets dubbla egenskaper

Först prenumererar en enhet på `$iothub/twin/res/#`för att ta emot åtgärdens svar. Sedan skickar den ett tomt meddelande till ämnet `$iothub/twin/GET/?$rid={request id}`, med ett fyllt värde för **begärande-ID**. Tjänsten skickar sedan ett svarsmeddelande som innehåller enhetens dubbla data på avsnittet `$iothub/twin/res/{status}/?$rid={request id}`, med samma ID för **begäran** som begäran.

Begärande-ID kan vara ett giltigt värde för ett meddelande egenskaps värde, enligt [IoT Hub meddelande guidens guide](iot-hub-devguide-messaging.md)och status är verifierad som ett heltal.

Svars texten innehåller avsnittet Egenskaper för enheten, som du ser i följande svars exempel:

```json
{
    "desired": {
        "telemetrySendFrequency": "5m",
        "$version": 12
    },
    "reported": {
        "telemetrySendFrequency": "5m",
        "batteryLevel": 55,
        "$version": 123
    }
}
```

Möjliga status koder är:

|Status | Beskrivning |
| ----- | ----------- |
| 204 | Lyckades (inget innehåll returneras) |
| 429 | För många begär Anden (begränsas) enligt [IoT Hub begränsning](iot-hub-devguide-quotas-throttling.md) |
| 5 * * | Server fel |

Mer information finns i [enhets guide för utvecklare](iot-hub-devguide-device-twins.md).

## <a name="update-device-twins-reported-properties"></a>Uppdatera enhetens egenskaper för dubbla rapporter

Om du vill uppdatera rapporterade egenskaper skickar enheten en begäran om att IoT Hub via en publikation över ett angivet MQTT-avsnitt. När du har bearbetat begäran, svarar IoT Hub att åtgärden lyckades eller misslyckades för uppdaterings åtgärden via en publikation till ett annat avsnitt. Det här avsnittet kan prenumereras på enheten för att meddela den om resultatet av den dubbla uppdaterings förfrågan. För att implementera den här typen av interaktions-/svars interaktion i MQTT, utnyttjar vi begreppet ID för begäran (`$rid`) som ursprungligen tillhandahölls av enheten i dess uppdateringsbegäran. Detta ID för begäran ingår också i svaret från IoT Hub för att tillåta att enheten korrelerar svaret till en viss tidigare begäran.

I följande sekvens beskrivs hur en enhet uppdaterar de rapporter som rapporteras i enheten, dubbla i IoT Hub:

1. En enhet måste först prenumerera på `$iothub/twin/res/#` ämnet för att ta emot åtgärdens svar från IoT Hub.

2. En enhet skickar ett meddelande som innehåller enhetens dubbla uppdateringar till `$iothub/twin/PATCH/properties/reported/?$rid={request id}` avsnittet. Det här meddelandet innehåller ett **ID-** värde för begäran.

3. Tjänsten skickar sedan ett svarsmeddelande som innehåller det nya ETag-värdet för den rapporterade egenskaps samlingen i avsnittet `$iothub/twin/res/{status}/?$rid={request id}`. Det här svarsmeddelandet använder samma **ID för begäran** som begäran.

Meddelande texten innehåller ett JSON-dokument som innehåller nya värden för rapporterade egenskaper. Varje medlem i JSON-dokumentet uppdaterar eller lägger till motsvarande medlem i enhetens dubbla dokument. En medlems uppsättning som `null`, tar bort medlemmen från objektet som innehåller. Till exempel:

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

Möjliga status koder är:

|Status | Beskrivning |
| ----- | ----------- |
| 200 | Lyckades |
| 400 | Felaktig begäran. Felaktig JSON |
| 429 | För många begär Anden (begränsas) enligt [IoT Hub begränsning](iot-hub-devguide-quotas-throttling.md) |
| 5 * * | Server fel |

I python-kodfragmentet nedan visas de dubbla rapporterade egenskaperna för uppdaterings processen över MQTT (med PAHO MQTT-klienten):

```python
from paho.mqtt import client as mqtt

# authenticate the client with IoT Hub (not shown here)

client.subscribe("$iothub/twin/res/#")
rid = "1"
twin_reported_property_patch = "{\"firmware_version\": \"v1.1\"}"
client.publish("$iothub/twin/PATCH/properties/reported/?$rid=" +
               rid, twin_reported_property_patch, qos=0)
```

Efter att ha lyckats med uppdaterings åtgärden för dubbla rapporter ovan, kommer meddelandet från IoT Hub att ha följande avsnitt: `$iothub/twin/res/204/?$rid=1&$version=6`, där `204` är status koden som visar att det lyckades `$rid=1` motsvarar det begär ande-ID som anges av enheten i koden, och `$version` motsvarar den version av avsnittet med rapporterade egenskaper i enheten.

Mer information finns i [enhets guide för utvecklare](iot-hub-devguide-device-twins.md).

## <a name="receiving-desired-properties-update-notifications"></a>Tar emot önskade egenskaper uppdatera meddelanden

När en enhet är ansluten skickar IoT Hub meddelanden till avsnittet `$iothub/twin/PATCH/properties/desired/?$version={new version}`, som innehåller innehållet i uppdateringen som utförs av lösningens Server del. Till exempel:

```json
{
    "telemetrySendFrequency": "5m",
    "route": null,
    "$version": 8
}
```

Precis som för egenskaps uppdateringar innebär `null` värden att JSON-objektmodellen tas bort. Observera också att `$version` anger den nya versionen av det önskade egenskaps avsnittet i den dubbla.

> [!IMPORTANT]
> IoT Hub genererar endast ändrings meddelanden när enheterna är anslutna. Se till att implementera [flödet för enhets åter anslutning](iot-hub-devguide-device-twins.md#device-reconnection-flow) för att behålla önskade egenskaper som synkroniseras mellan IoT Hub och enhets appen.

Mer information finns i [enhets guide för utvecklare](iot-hub-devguide-device-twins.md).

## <a name="respond-to-a-direct-method"></a>Svara på en direkt metod

Först måste en enhet prenumerera på `$iothub/methods/POST/#`. IoT Hub skickar metod begär anden till avsnittet `$iothub/methods/POST/{method name}/?$rid={request id}`, med antingen en giltig JSON-eller tom brödtext.

För att svara skickar enheten ett meddelande med en giltig JSON-eller tom brödtext till avsnittet `$iothub/methods/res/{status}/?$rid={request id}`. I det här meddelandet måste **förfrågnings-ID: t** matcha det som finns i begär ande meddelandet, och **statusen** måste vara ett heltal.

Mer information finns i [Direct Method Developer ' s guide](iot-hub-devguide-direct-methods.md).

## <a name="additional-considerations"></a>Annat som är bra att tänka på

Om du behöver anpassa MQTT-protokollets beteende på moln sidan bör du granska [Azure IoT Protocol Gateway](iot-hub-protocol-gateway.md). Med den här program varan kan du distribuera en anpassad protokoll-Gateway med höga prestanda som gränssnitt direkt med IoT Hub. Med Azure IoT Protocol Gateway kan du anpassa enhets protokollet för att hantera brownfield MQTT-distributioner eller andra anpassade protokoll. Den här metoden kräver dock att du kör och använder en anpassad protokoll-Gateway.

## <a name="next-steps"></a>Nästa steg

Mer information om MQTT-protokollet finns i MQTT- [dokumentationen](https://mqtt.org/documentation).

Mer information om hur du planerar din IoT Hub-distribution finns i:

* [Azure Certified for IoT-enhetskatalog](https://catalog.azureiotsolutions.com/)
* [Stöd för ytterligare protokoll](iot-hub-protocol-gateway.md)
* [Jämför med Event Hubs](iot-hub-compare-event-hubs.md)
* [Skalning, HA och DR](iot-hub-scaling.md)

För att ytterligare utforska funktionerna i IoT Hub, se:

* [Guide för IoT Hub utvecklare](iot-hub-devguide.md)
* [Distribuera AI till gräns enheter med Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
