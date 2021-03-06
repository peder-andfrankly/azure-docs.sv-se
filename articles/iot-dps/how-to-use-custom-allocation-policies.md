---
title: Principer för anpassad allokering med Azure IoT Hub Device Provisioning Service
description: Så här använder du principer för anpassad allokering med Azure IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 11/14/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: b6b7d4614d3c63fe93e213fb830b85d0b7f9c474
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974878"
---
# <a name="how-to-use-custom-allocation-policies"></a>Använda anpassade principer för allokering

Med en anpassad resursallokeringsprincip får du mer kontroll över hur enheter tilldelas till en IoT-hubb. Detta åstadkommer du genom att använda anpassad kod i en [Azure-funktion](../azure-functions/functions-overview.md) för att tilldela enheter till en IoT-hubb. Enhets etablerings tjänsten anropar din Azure Function-kod och ger all relevant information om enheten och registreringen. Funktions koden körs och returnerar IoT Hub-informationen som används för att tillhandahålla enheten.

Genom att använda anpassade allokeringsregler definierar du egna allokeringsregler när principerna som tillhandahålls av enhets etablerings tjänsten inte uppfyller kraven för ditt scenario.

Till exempel kanske du vill undersöka certifikatet som en enhet använder under etableringen och tilldela enheten till en IoT-hubb baserat på en certifikat egenskap. Eller så kanske du har information lagrad i en databas för dina enheter och behöver fråga databasen för att avgöra vilken IoT-hubb en enhet ska tilldelas till.

Den här artikeln visar en anpassad resursallokeringsprincip som använder en Azure-funktion C#som skrivits i. Två nya IoT-hubbar skapas som representerar en *contoso-popups-avdelning* och en *contoso värme pumpar-avdelning*. Enheter som begär etablering måste ha ett registrerings-ID med något av följande suffix som ska godkännas för etablering:

* **– contoso-tstrsd-007**: Contosos popups-avdelning
* **– contoso-hpsd-088**: contoso värme pumpar-avdelning

Enheterna kommer att tillhandahållas baserat på något av de nödvändiga suffixen i registrerings-ID: t. De här enheterna kommer att simuleras med hjälp av ett etablerings exempel som ingår i [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c).

Du utför följande steg i den här artikeln:

* Använd Azure CLI för att skapa två IoT-hubbar i Contoso-avdelning (**Contosos popups-Division** och **contoso värme pumpar**)
* Skapa en ny grupp registrering med en Azure-funktion för den anpassade allokeringsregeln
* Skapa enhets nycklar för två enhets simuleringar.
* Konfigurera utvecklings miljön för Azure IoT C SDK
* Simulera enheterna och kontrol lera att de är etablerade enligt exempel koden i den anpassade allokeringsregeln

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 eller senare med arbets belastningen ["Skriv C++bords utveckling med"](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) aktiverat.
* Senaste versionen av [Git](https://git-scm.com/download/) installerad.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-provisioning-service-and-two-divisional-iot-hubs"></a>Skapa etablerings tjänsten och två avdelnings IoT-hubbar

I det här avsnittet använder du Azure Cloud Shell för att skapa en etablerings tjänst och två IoT-hubbar som representerar **contoso-popup** -indelningen och **contoso värme pumpar-indelningen**.

> [!TIP]
> Kommandona som används i den här artikeln skapar etablerings tjänsten och andra resurser på platsen USA, västra. Vi rekommenderar att du skapar dina resurser i den region som är närmast dig som har stöd för enhets etablerings tjänsten. Du kan se en lista med tillgängliga platser genom att köra kommandot `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table`, eller genom att gå till sidan [Azure-status](https://azure.microsoft.com/status/) och söka efter ”enhetsetableringstjänst”. I kommandon kan platser anges antingen i ett ord eller med flera ord. exempel: väst, västra USA, västra USA osv. Värdet är inte Skift läges känsligt. Om du använder flera ord för att ange platsen skriver du värdet inom citattecken; till exempel `-- location "West US"`.
>

1. Använd Azure Cloud Shell för att skapa en resurs grupp med kommandot [AZ Group Create](/cli/azure/group#az-group-create) . En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

    I följande exempel skapas en resurs grupp med namnet *contoso-US-Resource-Group* i regionen *västra* USA. Vi rekommenderar att du använder den här gruppen för alla resurser som skapats i den här artikeln. Den här metoden blir tydligare när du är klar.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location westus
    ```

2. Använd Azure Cloud Shell för att skapa en enhets etablerings tjänst med kommandot [AZ IoT DPS Create](/cli/azure/iot/dps#az-iot-dps-create) . Etablerings tjänsten kommer att läggas till i *contoso-US-Resource-Group*.

    I följande exempel skapas en etablerings tjänst med namnet *contoso-Provisioning-service-1098* på platsen för *västkusten* . Du måste använda ett unikt tjänst namn. Skapa ditt eget suffix i tjänst namnet i stället för **1098**.

    ```azurecli-interactive 
    az iot dps create --name contoso-provisioning-service-1098 --resource-group contoso-us-resource-group --location westus
    ```

    Det kan ta några minuter att slutföra kommandot.

3. Använd Azure Cloud Shell för att skapa **Contosos indelning** IoT Hub med kommandot [AZ IoT Hub Create](/cli/azure/iot/hub#az-iot-hub-create) . IoT Hub kommer att läggas till i *contoso-US-Resource-Group*.

    I följande exempel skapas en IoT-hubb med namnet *contoso-popups – Hub-1098* på platsen för *väst* . Du måste använda ett unikt namn på hubben. Skapa ditt eget suffix i hubben i stället för **1098**. Exempel koden för den anpassade allokeringsregeln måste `-toasters-` i hubbens namn.

    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Det kan ta några minuter att slutföra kommandot.

4. Använd Azure Cloud Shell för att skapa **Contosos** IoT Hub-hubb med [AZ IoT Hub](/cli/azure/iot/hub#az-iot-hub-create) . Den här IoT-hubben läggs också till i *contoso-US-Resource-Group*.

    I följande exempel skapas en IoT-hubb med namnet *contoso-heatpumps-Hub-1098* på platsen för *västkusten* . Du måste använda ett unikt namn på hubben. Skapa ditt eget suffix i hubben i stället för **1098**. Exempel koden för den anpassade allokeringsregeln måste `-heatpumps-` i hubbens namn.

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Det kan ta några minuter att slutföra kommandot.

## <a name="create-the-custom-allocation-function"></a>Skapa en anpassad tilldelnings funktion

I det här avsnittet skapar du en Azure-funktion som implementerar din anpassade resursallokeringsprincip. Den här funktionen bestämmer vilken avdelnings IoT-hubb en enhet ska registreras på baserat på om dess registrerings-ID innehåller strängen **-contoso-tstrsd-007** eller **-contoso-hpsd-088**. Den anger också den ursprungliga statusen för enheten, baserat på om enheten är en toaster eller en värme pump.

1. Logga in på [Azure-portalen](https://portal.azure.com). Från start sidan väljer du **+ skapa en resurs**.

2. Skriv "Funktionsapp" i Sök rutan *Sök i Marketplace* . Välj **Funktionsapp**i list rutan och välj sedan **skapa**.

3. På sidan för **Funktionsapp** skapa, under fliken **grundläggande** , anger du följande inställningar för den nya Function-appen och väljer **Granska + skapa**:

    **Resurs grupp**: Välj **contoso-US-Resource-Group** för att hålla alla resurser som skapats i den här artikeln tillsammans.

    **Funktionsapp namn**: Ange ett unikt namn för Function-appen. I det här exemplet används **contoso-Function-app-1098**.

    **Publicera**: kontrol lera att **koden** är markerad.

    **Körnings stack**: Välj **.net Core** i list rutan.

    **Region**: Välj samma region som din resurs grupp. I det här exemplet används **västra USA**.

    > [!NOTE]
    > Som standard är Application Insights aktive rad. Application Insights krävs inte för den här artikeln, men det kan hjälpa dig att förstå och undersöka eventuella problem med den anpassade allokeringen. Om du vill kan du inaktivera Application Insights genom att välja fliken **övervakning** och sedan välja **nej** för **Aktivera Application Insights**.

    ![Skapa en Azure-Funktionsapp som värd för den anpassade tilldelnings funktionen](./media/how-to-use-custom-allocation-policies/create-function-app.png)

4. På sidan **Sammanfattning** väljer du **skapa** för att skapa Function-appen. Distributionen kan ta flera minuter. När den är klar väljer **du gå till resurs**.

5. I det vänstra fönstret på **översikts** sidan funktionens app väljer du **+** bredvid **funktioner** för att lägga till en ny funktion.

    ![Lägg till en funktion i Funktionsapp](./media/how-to-use-custom-allocation-policies/create-function.png)

6. På sidan **Azure Functions för .net-komma igång** går du till steget **Välj en distributions miljö** , väljer panelen **i portalen** och väljer **Fortsätt**.

    ![Välj portal utvecklings miljö](./media/how-to-use-custom-allocation-policies/function-choose-environment.png)

7. På nästa sida, för steget **skapa en funktion** , väljer du panel för **webhook + API** och väljer sedan **skapa**. En funktion med namnet **HttpTrigger1** skapas och portalen visar innehållet i filen **Run. CSX** .

8. Referens krävs NuGet-paket. För att skapa den första enheten, använder funktionen för anpassad allokering klasser som är definierade i två NuGet-paket som måste läsas in i värd miljön. Med Azure Functions refereras NuGet-paket med hjälp av en *Function. Host* -fil. I det här steget sparar och överför du en *funktion. Host* -fil.

    1. Kopiera följande rader till din favorit redigerare och spara filen på datorn som *funktion. Host*.

        ```xml
        <Project Sdk="Microsoft.NET.Sdk">  
            <PropertyGroup>  
                <TargetFramework>netstandard2.0</TargetFramework>  
            </PropertyGroup>  
            <ItemGroup>  
                <PackageReference Include="Microsoft.Azure.Devices.Provisioning.Service" Version="1.5.0" />  
                <PackageReference Include="Microsoft.Azure.Devices.Shared" Version="1.16.0" />  
            </ItemGroup>  
        </Project>
        ```

    2. I **HttpTrigger1** -funktionen expanderar du fliken **Visa filer** till höger i fönstret.

        ![Öppna Visa filer](./media/how-to-use-custom-allocation-policies/function-open-view-files.png)

    3. Välj **överför**, bläddra till filen **Function. proj** och välj **Öppna** för att ladda upp filen.

        ![Välj Ladda upp fil](./media/how-to-use-custom-allocation-policies/function-choose-upload-file.png)

9. Ersätt koden för **HttpTrigger1** -funktionen med följande kod och välj **Spara**:

    ```csharp
    #r "Newtonsoft.Json"

    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    using Microsoft.Azure.Devices.Shared;               // For TwinCollection
    using Microsoft.Azure.Devices.Provisioning.Service; // For TwinState

    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);

        log.LogInformation("Request.Body:...");
        log.LogInformation(requestBody);

        // Get registration ID of the device
        string regId = data?.deviceRuntimeContext?.registrationId;

        string message = "Uncaught error";
        bool fail = false;
        ResponseObj obj = new ResponseObj();

        if (regId == null)
        {
            message = "Registration ID not provided for the device.";
            log.LogInformation("Registration ID : NULL");
            fail = true;
        }
        else
        {
            string[] hubs = data?.linkedHubs.ToObject<string[]>();

            // Must have hubs selected on the enrollment
            if (hubs == null)
            {
                message = "No hub group defined for the enrollment.";
                log.LogInformation("linkedHubs : NULL");
                fail = true;
            }
            else
            {
                // This is a Contoso Toaster Model 007
                if (regId.Contains("-contoso-tstrsd-007"))
                {
                    //Find the "-toasters-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-toasters-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No toasters hub found for the enrollment.";
                        log.LogInformation(message);
                        fail = true;
                    }
                    else
                    {
                        // Specify the initial tags for the device.
                        TwinCollection tags = new TwinCollection();
                        tags["deviceType"] = "toaster";

                        // Specify the initial desired properties for the device.
                        TwinCollection properties = new TwinCollection();
                        properties["state"] = "ready";
                        properties["darknessSetting"] = "medium";

                        // Add the initial twin state to the response.
                        TwinState twinState = new TwinState(tags, properties);
                        obj.initialTwin = twinState;
                    }
                }
                // This is a Contoso Heat pump Model 008
                else if (regId.Contains("-contoso-hpsd-088"))
                {
                    //Find the "-heatpumps-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-heatpumps-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No heat pumps hub found for the enrollment.";
                        log.LogInformation(message);
                        fail = true;
                    }
                    else
                    {
                        // Specify the initial tags for the device.
                        TwinCollection tags = new TwinCollection();
                        tags["deviceType"] = "heatpump";

                        // Specify the initial desired properties for the device.
                        TwinCollection properties = new TwinCollection();
                        properties["state"] = "on";
                        properties["temperatureSetting"] = "65";

                        // Add the initial twin state to the response.
                        TwinState twinState = new TwinState(tags, properties);
                        obj.initialTwin = twinState;
                    }
                }
                // Unrecognized device.
                else
                {
                    fail = true;
                    message = "Unrecognized device registration.";
                    log.LogInformation("Unknown device registration");
                }
            }
        }

        log.LogInformation("\nResponse");
        log.LogInformation((obj.iotHubHostName != null) ? JsonConvert.SerializeObject(obj) : message);

        return (fail)
            ? new BadRequestObjectResult(message) 
            : (ActionResult)new OkObjectResult(obj);
    }

    public class ResponseObj
    {
        public string iotHubHostName {get; set;}
        public TwinState initialTwin {get; set;}
    }
    ```

## <a name="create-the-enrollment"></a>Skapa registreringen

I det här avsnittet ska du skapa en ny registrerings grupp som använder den anpassade allokeringsregeln. För enkelhetens skull använder den här artikeln [symmetrisk nyckel attestering](concepts-symmetric-key-attestation.md) med registreringen. För en säkrare lösning bör du överväga att använda [X. 509 certifikat attestering](concepts-security.md#x509-certificates) med en förtroende kedja.

1. Öppna etablerings tjänsten fortfarande på [Azure Portal](https://portal.azure.com).

2. Välj **Hantera registreringar** i den vänstra rutan och välj sedan knappen **Lägg till registrerings grupp** överst på sidan.

3. Ange följande information i **Lägg till registrerings grupp**och välj knappen **Spara** .

    **Grupp namn**: ange **contoso-Custom-allokerade – enheter**.

    **Attesterings typ**: Välj **symmetrisk nyckel**.

    **Generera nycklar automatiskt**: den här kryss rutan ska redan vara markerad.

    **Välj hur du vill tilldela enheter till hubbar**: Välj **Anpassad (Använd Azure Function)** .

    ![Lägg till anpassad grupp registrerings grupp för symmetrisk nyckel attestering](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. I **Lägg till registrerings grupp**väljer du **Länka en ny IoT-hubb** för att länka båda dina nya avdelnings IoT-hubbar.

    Kör det här steget för båda dina avdelnings IoT-hubbar.

    **Prenumeration**: om du har flera prenumerationer väljer du den prenumeration där du skapade avdelningens IoT-hubbar.

    **IoT Hub**: Välj ett av de divisions nav som du har skapat.

    **Åtkomst princip**: Välj **iothubowner**.

    ![Länka avdelningens IoT-hubbar med etablerings tjänsten](./media/how-to-use-custom-allocation-policies/link-divisional-hubs.png)

5. När båda avdelnings IoT-hubbarna har länkats i **Lägg till registrerings grupp**måste du välja dem som IoT Hub grupp för registrerings gruppen enligt nedan:

    ![Skapa delnings nav gruppen för registreringen](./media/how-to-use-custom-allocation-policies/enrollment-divisional-hub-group.png)

6. I **Lägg till registrerings grupp**bläddrar du ned till avsnittet **Välj Azure-funktion** , väljer den Function-app som du skapade i föregående avsnitt. Välj sedan den funktion som du skapade och välj Spara för att spara registrerings gruppen.

    ![Välj funktionen och spara registrerings gruppen](./media/how-to-use-custom-allocation-policies/save-enrollment.png)

7. När du har sparat registreringen kan du öppna den igen och anteckna den **primära nyckeln**. Du måste spara registreringen innan du genererar nycklarna. Den här nyckeln kommer att användas för att generera unika enhets nycklar för simulerade enheter senare.

## <a name="derive-unique-device-keys"></a>Härled unika enhets nycklar

I det här avsnittet skapar du två unika enhets nycklar. En nyckel kommer att användas för en simulerad toaster-enhet. Den andra nyckeln kommer att användas för en simulerad värme Pumps enhet.

Om du vill generera enhets nyckeln använder du den **primära nyckeln** som du noterade tidigare för att beräkna [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) för enhets registrerings-ID: t för varje enhet och konvertera resultatet till base64-format. Mer information om hur du skapar härledda enhets nycklar med registrerings grupper finns i avsnittet grupp registrering i [symmetrisk nyckel attestering](concepts-symmetric-key-attestation.md).

I exemplet i den här artikeln använder du följande två enhets registrerings-ID: n och beräknar en enhets nyckel för båda enheterna. Båda registrerings-ID: n har ett giltigt suffix för att fungera med exempel koden för den anpassade allokeringsregeln:

* **breakroom499-contoso-tstrsd-007**
* **mainbuilding167-contoso-hpsd-088**

### <a name="linux-workstations"></a>Linux-arbetsstationer

Om du använder en Linux-arbetsstation kan du använda OpenSSL för att generera dina härledda enhets nycklar på det sätt som visas i följande exempel.

1. Ersätt värdet för **Key** med den **primära nyckel** du noterade tidigare.

    ```bash
    KEY=oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA==

    REG_ID1=breakroom499-contoso-tstrsd-007
    REG_ID2=mainbuilding167-contoso-hpsd-088

    keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
    devkey1=$(echo -n $REG_ID1 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)
    devkey2=$(echo -n $REG_ID2 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)

    echo -e $"\n\n$REG_ID1 : $devkey1\n$REG_ID2 : $devkey2\n\n"
    ```

    ```bash
    breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
    mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
    ```

### <a name="windows-based-workstations"></a>Windows-baserade arbets stationer

Om du använder en Windows-baserad arbets Station kan du använda PowerShell för att generera en härledd enhets nyckel som visas i följande exempel.

1. Ersätt värdet för **Key** med den **primära nyckel** du noterade tidigare.

    ```powershell
    $KEY='oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA=='

    $REG_ID1='breakroom499-contoso-tstrsd-007'
    $REG_ID2='mainbuilding167-contoso-hpsd-088'

    $hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
    $hmacsha256.key = [Convert]::FromBase64String($key)
    $sig1 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID1))
    $sig2 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID2))
    $derivedkey1 = [Convert]::ToBase64String($sig1)
    $derivedkey2 = [Convert]::ToBase64String($sig2)

    echo "`n`n$REG_ID1 : $derivedkey1`n$REG_ID2 : $derivedkey2`n`n"
    ```

    ```powershell
    breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
    mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
    ```

De simulerade enheterna kommer att använda de härledda enhets nycklarna med varje registrerings-ID för att utföra symmetrisk nyckel attestering.

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Förbereda en utvecklingsmiljö för Azure IoT C SDK

I det här avsnittet förbereder du utvecklings miljön som används för att skapa [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). SDK innehåller exempel koden för den simulerade enheten. Den här simulerade enheten försöker etablera under enhetens startsekvens.

Det här avsnittet är riktat mot en Windows-baserad arbets Station. Ett Linux-exempel finns i konfiguration av de virtuella datorerna i [hur du etablerar för flera innehavare](how-to-provision-multitenant.md).

1. Ladda ned [cmake build-systemet](https://cmake.org/download/).

    Det är viktigt att Visual Studio-förutsättningarna (Visual Studio och "Skriv bords utveckling med C++" arbets belastning) är installerade på datorn **innan** du startar `CMake` installationen. När kraven är på plats och hämtningen har verifierats, installerar du CMake build-systemet.

2. Öppna en kommandotolk eller Git Bash-gränssnittet. Kör följande kommando för att klona Azure IoT C SDK GitHub-lagringsplatsen:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

    Den här åtgärden kan förväntas ta flera minuter att slutföra.

3. Skapa en `cmake`-underkatalog i rotkatalogen på git-lagringsplatsen och navigera till den mappen. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Kör följande kommando som skapar en version av SDK:t som är specifik för plattformen i din utvecklingsklient. En Visual Studio-lösning för den simulerade enheten genereras i `cmake`-katalogen. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    Om `cmake` inte hittar C++ kompilatorn kan du få build-fel när du kör kommandot. Om det händer kan du försöka köra kommandot i [kommando tolken för Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

    När bygget är klart ser de sista utdataraderna ut ungefär som följande utdata:

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

## <a name="simulate-the-devices"></a>Simulera enheterna

I det här avsnittet uppdaterar du ett etablerings exempel med namnet **bevisa\_dev\_klient\_exempel** som finns i Azure IoT C SDK som du har skapat tidigare.

Den här exempel koden simulerar en enhets startsekvens som skickar etablerings förfrågan till din enhets etablerings tjänst instans. Startsekvensen gör att toaster-enheten identifieras och tilldelas IoT-hubben med hjälp av den anpassade principen för tilldelning.

1. I Azure-portalen väljer du fliken **Översikt** för enhetsetableringstjänsten och noterar värdet för **_ID-omfång_** .

    ![Extrahera information om enhetsetableringstjänstens slutpunkt från bladet på portalen](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. Öppna lösnings filen **azure_iot_sdks. SLN** som genererades genom att köra cmake tidigare i Visual Studio. Lösningsfilen bör vara på följande plats:

    ```
    azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. I fönstret *Solution Explorer* i Visual Studio går du till mappen **Provision (Etablera)\_Exempel**. Expandera exempelprojektet som heter **prov\_dev\_client\_sample**. Expandera **Källfiler** och öppna **prov\_dev\_client\_sample.c**.

4. Hitta konstanten `id_scope` och ersätt värdet med ditt värde för **ID-omfång** som du kopierade tidigare. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Hitta definitionen för funktionen `main()` i samma fil. Kontrollera att variabeln `hsm_type` är inställd på `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` enligt nedan:

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. Högerklicka på projektet **prov\_dev\_client\_sample** och välj **Set as Startup Project** (Ange som startprojekt).

### <a name="simulate-the-contoso-toaster-device"></a>Simulera contoso toaster-enheten

1. Om du vill simulera toaster-enheten kan du hitta anropet till `prov_dev_set_symmetric_key_info()` i **bevisa\_dev\_klient\_Sample. c** som är kommenterad.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Ta bort kommentarer till funktions anropet och ersätt plats hållarnas värden (inklusive vinkelparenteser) med toaster registrerings-ID och härledd enhets nyckel som du skapade tidigare. Nyckelvärdet **JC8F96eayuQwwz + PkE7IzjH2lIAjCUnAa61tDigBnSs =** som visas nedan anges bara som ett exempel.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    Spara filen.

2. I Visual Studio-menyn väljer du **Felsökning** > **Starta utan felsökning** för att köra lösningen. I prompten för att återskapa projektet väljer du **Ja**för att återskapa projektet innan det körs.

    Följande utdata är ett exempel på den simulerade toaster-enheten som har startats och anslutits till etablerings tjänst instansen som ska tilldelas till popups IoT Hub med den anpassade allokeringsregeln:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: breakroom499-contoso-tstrsd-007

    Press enter key to exit:
    ```

### <a name="simulate-the-contoso-heat-pump-device"></a>Simulera enheten contoso värme pump

1. Om du vill simulera värme Pumps enheten uppdaterar du anropet till `prov_dev_set_symmetric_key_info()` i **bevisa\_dev\_klient\_exempel. c** igen med värme pumpens registrerings-ID och härledd enhets nyckel som du skapade tidigare. Nyckel värdet **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg =** som visas nedan anges också bara som ett exempel.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("mainbuilding167-contoso-hpsd-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```

    Spara filen.

2. I Visual Studio-menyn väljer du **Felsökning** > **Starta utan felsökning** för att köra lösningen. I prompten för att återskapa projektet väljer du **Ja** för att återskapa projektet innan det körs.

    Följande utdata är ett exempel på den simulerade värme pump enheten som har startats och anslutits till etablerings tjänst instansen som ska tilldelas contoso värme pumpar IoT Hub med den anpassade allokeringsregeln:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-heatpumps-hub-1098.azure-devices.net, deviceId: mainbuilding167-contoso-hpsd-088

    Press enter key to exit:
    ```

## <a name="troubleshooting-custom-allocation-policies"></a>Felsöka anpassade principer för allokering

Följande tabell visar förväntade scenarier och de resultat fel koder som du kan få. Använd den här tabellen för att felsöka problem med anpassade allokeringsregler med Azure Functions.

| Scenario | Registrerings resultat från etablerings tjänsten | Tillhandahålla SDK-resultat |
| -------- | --------------------------------------------- | ------------------------ |
| Webhooken returnerar 200 OK med ' iotHubHostName ' inställt på ett giltigt värd namn för IoT Hub | Resultat status: tilldelad  | SDK returnerar PROV_DEVICE_RESULT_OK tillsammans med information om hubben |
| Webhooken returnerar 200 OK med "iotHubHostName" i svaret, men är inställt på en tom sträng eller null | Resultat status: misslyckades<br><br> Felkod: CustomAllocationIotHubNotSpecified (400208) | SDK returnerar PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED |
| Webhooken returnerar 401 obehörig | Resultat status: misslyckades<br><br>Felkod: CustomAllocationUnauthorizedAccess (400209) | SDK returnerar PROV_DEVICE_RESULT_UNAUTHORIZED |
| En enskild registrering har skapats för att inaktivera enheten | Resultat status: inaktive rad | SDK returnerar PROV_DEVICE_RESULT_DISABLED |
| Webhooken returnerar felkoden > = 429 | DPS försöker igen ett antal gånger. Principen för återförsök är för närvarande:<br><br>&nbsp;&nbsp;-antal återförsök: 10<br>&nbsp;&nbsp;-start intervall: 1<br>&nbsp;&nbsp;-Increment: nior | SDK kommer att ignorera fel och skicka ett annat get status meddelande inom den angivna tiden |
| Webhooken returnerar annan status kod | Resultat status: misslyckades<br><br>Felkod: CustomAllocationFailed (400207) | SDK returnerar PROV_DEVICE_RESULT_DEV_AUTH_ERROR |

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta arbeta med resurserna som du skapade i den här artikeln kan du lämna dem. Om du inte planerar att fortsätta använda resurserna kan du använda följande steg för att ta bort alla resurser som skapats i den här artikeln för att undvika onödiga kostnader.

Stegen här förutsätter att du har skapat alla resurser i den här artikeln enligt anvisningarna i samma resurs grupp med namnet **contoso-US-Resource-Group**.

> [!IMPORTANT]
> Att ta bort en resursgrupp kan inte ångras. Resursgruppen och alla resurser som ingår i den tas bort permanent. Var noga så att du inte tar bort fel resursgrupp eller resurser av misstag. Om du har skapat IoT Hub:en inuti en befintlig resursgrupp som innehåller resurser som du vill behålla, ta bara bort själva IoT Hub-resursen i stället för att ta bort resursgruppen.
>

Så här tar du bort resurs gruppen efter namn:

1. Logga in på [Azure-portalen](https://portal.azure.com) och välj **Resursgrupper**.

2. I text rutan **Filtrera efter namn...** skriver du namnet på den resurs grupp som innehåller dina resurser, **contoso-US-Resource-Group**. 

3. Till höger om resurs gruppen i resultat listan väljer du **...** och sedan **ta bort resurs grupp**.

4. Du uppmanas att bekräfta borttagningen av resurs gruppen. Skriv namnet på resurs gruppen igen för att bekräfta och välj sedan **ta bort**. Efter en liten stund tas resursgruppen och resurser som finns i den bort.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du reetablerar finns i [IoT Hub metoder för att etablera enheter](concepts-device-reprovision.md) 
* Mer information finns i [så här avetablerar du enheter som tidigare har etablerats](how-to-unprovision-devices.md) 
