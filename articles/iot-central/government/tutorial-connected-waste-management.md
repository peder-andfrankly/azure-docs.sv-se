---
title: 'Självstudie: skapa en ansluten hanterings app för slöseri med Azure IoT Central'
description: 'Självstudie: Lär dig hur du skapar ett anslutet program för hantering av program med hjälp av Azure IoT Central programmallar.'
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 38da444779a56f39d4119f3797ddb5bd2f31aeae
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112636"
---
# <a name="tutorial-create-a-connected-waste-management-application-in-iot-central"></a>Självstudie: skapa ett anslutet hanterings program för skräp hantering i IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

I den här självstudien får du hjälp med att skapa ett program för hantering av program som är kopplat till Azure IoT Central från program mal len IoT Central **anslutet** 

I självstudien får du lära dig att: 

> [!div class="checklist"]
> * Använd mallen Azure IoT Central- **ansluten avfalls hantering** för att skapa ett anslutet hanterings program för skräp post
> * Utforska och anpassa instrument panelen för operatör 
> * Utforska enhets mal len för den anslutna avfalls platsen
> * Utforska simulerade enheter
> * Utforska och konfigurera regler
> * Konfigurera jobb
> * Anpassa din program anpassning med adresser

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du:
-  En Azure-prenumeration rekommenderas. Du kan välja att använda en kostnads fri 7-dagars utvärderings version. Om du inte har någon Azure-prenumeration kan du skapa en på [sidan för Azure-registrering](https://aka.ms/createazuresubscription).

## <a name="create-connected-waste-management-app-in-iot-central"></a>Skapa ansluten app för avfalls hantering i IoT Central

I det här avsnittet ska vi använda mallen Azure IoT Central- **ansluten avfalls hantering** för att skapa ett anslutet hanterings program för skräp post i IoT Central.

Så här skapar du ett nytt Azure IoT Central-anslutet hanterings program för skräp hantering:  

1. Gå till webbplatsen för [Azure IoT Central start sida](https://aka.ms/iotcentral) .

    Om du har en Azure-prenumeration loggar du in med de autentiseringsuppgifter som du använder för att komma åt den, annars loggar du in med en Microsoft-konto:

    ![Ange ditt organisationskonto](./media/tutorial-connectedwastemanagement/sign-in.png)

2. Klicka på **build (Bygg** in) i den vänstra rutan och välj fliken **myndigheter** . Sidan myndigheter visar flera myndighets programmallar.

    ![Bygg app-mallar för myndigheter](./media/tutorial-connectedwastemanagement/iotcentral-government-tab-overview.png)

1. Välj program mal len **ansluten avfalls hantering** . Den här mallen innehåller exempel på en ansluten enhets mall för avfalls lager, simulerad enhet, operatörs instrument panel och förkonfigurerade övervaknings regler.    

2. Klicka på **skapa app**, så öppnas formuläret för att skapa **nya program** med följande fält:
    * **Program namn**. Som standard använder programmet *ansluten avfalls hantering* följt av en unik ID-sträng som IoT Central genererar. Du kan också välja ett eget program namn. Du kan senare ändra program namnet.
    * **URL** – du kan också välja att ange önskad URL. Du kan ändra URL: en senare. 
    * Om du har en Azure-prenumeration anger du din *katalog, din Azure-prenumeration och region*. Om du inte har någon prenumeration kan du aktivera den **kostnads fria utvärderings versionen på 7 dagar** och slutföra den kontakt information som krävs.  

    Mer information om kataloger och prenumerationer finns i [snabbstarten om att skapa ett program](../preview/quick-deploy-iot-central.md).

5. Klicka på knappen **skapa** längst ned på sidan. 

    ![Sidan för att skapa ett anslutet avfall i Azure IoT Central](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement.png)

6. Nu har du skapat en ansluten app för avfalls hantering med hjälp av **mallen Azure IoT Central ansluten avfalls hantering**. 

Gratulerar! Det nyligen skapade programmet levereras med förkonfigurerade:
* Exempel instrument panels instrument paneler
* Exempel på fördefinierad enhet med fördefinierat antal anslutna avfalls platser
* Simulerade anslutna avfalls lager enheter
* Förkonfigurerade regler och jobb
* Exempel anpassning med vita etiketter 

Det är ditt program och du kan ändra det när som helst. Nu ska vi utforska programmet och göra några anpassningar.  

## <a name="explore-and-customize-operator-dashboard"></a>Utforska och anpassa instrument panelen för operatör 
När du har skapat det program som du har skapat i den **breda anslutet avfalls hanterings instrument panel**.

   ![Instrument panel för ansluten avfalls hantering](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard1.png)

Som ett verktyg kan du skapa och anpassa vyer på instrument panelen för operatörer. Vi går igenom instrument panelen innan du försöker anpassa. 

> [!NOTE]
> Alla data som visas i instrument panelen baseras på simulerade enhets data som kommer att utforskas i nästa avsnitt. 

Instrument panelen består av olika typer av paneler:

* ***Bild panel för wide-verktyg för avfalls verktyg***: den första panelen på instrument panelen är en bild panel med ett fiktivt avinstallations verktyg "Wide World avfall". Du kan anpassa panelen och placera en egen avbildning eller ta bort den. 

* ***Bild panel för avfalls fack***: du kan använda bild-och innehålls paneler för att skapa en visuell representation av enheten som övervakas tillsammans med en beskrivande text. 

* ***KPI-panel på fyllnings nivå***: panelen visar ett värde som rapporteras av en sensor för *fyllnads nivåer* på en pappers korg. *Fyllnings nivå* och andra sensorer som *odor-mätare* eller *vikt* på en avfalls lager plats kan fjärrövervakas. En operatör kan vidta åtgärder, t. ex. sändning av SOP insamlings Truck. 

*  ***Områdes karta för avfalls övervakning***: kartan använder Azure Maps, som du kan konfigurera direkt i Azure IoT Central. Kart panelen visar enhetens plats. Försök att hovra över kartan och testa kontrollerna över kartan, t. ex. Zooma in, zooma ut eller expandera.

     ![Översikt över ansluten instrument panel för avfalls hantering](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-map.png)


* ***fyllning, odor, stapeldiagram på viktnivå**: du kan visualisera en eller flera telemetridata för enheter i ett stapeldiagram. Du kan också expandera stapeldiagrammet.  

  ![Stapeldiagram för instrument panelen för anslutna avfalls hantering](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-barchart.png)


* **Innehålls panelen fält tjänster**: instrument panelen innehåller länkar till hur du integrerar med Dynamics 365-fälttjänster från ditt Azure IoT Central-program. Som exempel kan du använda fält tjänster för att skapa biljetter för att skicka iväg skräp insamlings tjänster. 


### <a name="customize-dashboard"></a>Anpassa instrument panelen 

Som ett verktyg kan du anpassa vyer på instrument panelen för operatörer. Du kan prova:
1. Klicka på **Redigera** om du vill anpassa den **bred anslutna instrument panelen för avfalls hantering**. Du kan anpassa instrument panelen genom att klicka på **Redigera** -menyn. När instrument panelen är i **redigerings** läge kan du lägga till nya paneler, eller så kan du konfigurera 

    ![Redigera instrument panel](./media/tutorial-connectedwastemanagement/edit-dashboard.png)

2. Du kan också klicka på **+ ny** för att skapa en ny instrument panel och konfigurera från början. Du kan ha flera instrument paneler och du kan navigera mellan dina instrument paneler på instrument panelens meny. 

## <a name="explore-connected-waste-bin-device-template"></a>Utforska enhets mal len för den anslutna avfalls platsen

En enhets mall i Azure IoT Central definierar funktionen hos en enhet, som kan vara telemetri, egenskaper eller kommando. Som ett verktyg kan du definiera enhetsspecifika mallar som representerar kapaciteten för de enheter som du ska ansluta till. 
 

**Anslutet hanterings program för avfalls hantering** levereras med en exempel enhets mal len för avkopplad avfalls plats.

Så här visar du enhets mal len:

1. Klicka på **mallar för enheter** i det vänstra navigerings fönstret i programmet i IoT Central. 

    ![Enhets mall](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate.png)

2. I listan mallar för enhets information visas **anslutet pappers lager**. Öppna genom att klicka på namnet.

3. Familirize med enhets mal len funktioner. Du kan se att den definierar sensorer som *Fyllnings nivå*, *odor-mätare*, *vikt*, *plats* osv.

   ![Enhets mall](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-connectedbin.png)

   

### <a name="customizing-the-device-template"></a>Anpassa enhets mal len

Försök att anpassa följande:
1. Navigera för att **Anpassa** från menyn på enhets mal len
2. Hitta typen av telemetri för `Odor meter`
3. Uppdatera **visnings namnet** för `Odor meter` till `Odor level`
4. Du kan också prova att uppdatera mått enheten eller ange *minsta värde* och *Max värde*
5. **Spara** ändringar 

### <a name="add-a-cloud-property"></a>Lägg till en moln egenskap 

1. Gå till **moln egenskap** från enhetens mall-menyn
2. Lägg till en ny moln egenskap genom att klicka på **+ Lägg till moln egenskap**. I IoT Central kan du lägga till en egenskap som är relevant för enheten men som inte förväntas skickas av en enhet. Som exempel kan en moln egenskap vara en aviserings tröskel som är speciell för installations områden, till gångs information eller underhålls information osv. 
3. **Spara** ändringar 
 
### <a name="views"></a>Vyer 
* Enhets mal len för den anslutna avfalls platsen levereras med fördefinierade vyer. Utforska vyerna så kan du göra uppdateringar. Vyerna definierar hur operatörer ser enhets data, men som även anger moln egenskaper. 

  ![Vyer för enhets mal len](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-views.png)

### <a name="publish"></a>Publicera 

* Om du har gjort några ändringar måste du **publicera** enhets mal len. 

### <a name="create-a-new-device-template"></a>Skapa en ny enhetsmall 

* Välj **+ nytt** för att skapa en ny enhets mall och följ proceduren för att skapa. Du kommer att kunna skapa en anpassad enhets mall från början eller välja en enhets mall från Azures enhets katalog. 

## <a name="explore-simulated-devices"></a>Utforska simulerade enheter

I IoT Central kan du skapa simulerade enheter för att testa enhetens mall och program. 

**Anslutet hanterings** program för program vara har två simulerade enheter som är kopplade till enhets mal len för den anslutna avfalls platsen. 

### <a name="to-view-the-devices"></a>Så här visar du enheterna:

1. Navigera till **enheten** från IoT Central vänstra navigerings fönstret. 

   ![Enheter](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices.png)

2. Välj och klicka på ansluten spill lager enhet.  

     ![Enhet 1](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices-bin1.png)

3. Navigera till fliken **moln egenskaper** och försök att uppdatera `Bin full alert threshold` värde från `95` till `100`. 
* Utforska fliken **enhets egenskaper** och fliken **enhets instrument panel** . 

> [!NOTE]
> Observera att alla flikar har kon figurer ATS från **Device Template-vyerna**.

### <a name="add-new-devices"></a>Lägg till nya enheter

* Du kan lägga till nya enheter genom att klicka på **+ ny** på fliken **enheter** . 

## <a name="explore-and-configure-rules"></a>Utforska och konfigurera regler

I Azure IoT Central kan du skapa regler för automatisk övervakning på enhetens telemetri och utlösa åtgärder när ett eller flera villkor uppfylls. Åtgärderna kan omfatta att skicka e-postmeddelanden, utlösa en Microsoft Flow åtgärd eller en webhook-åtgärd för att skicka data till andra tjänster.

**Anslutet hanterings program för skräp hantering** har fyra exempel regler.

### <a name="to-view-rules"></a>Så här visar du regler:
1. Navigera till **regler** från IoT Central vänstra navigerings fönstret

   ![Regler](./media/tutorial-connectedwastemanagement/connectedwastemanagement-rules.png)

2. Välj **fullständig avisering för bin**

     ![Fullständig avisering för bin](./media/tutorial-connectedwastemanagement/connectedwastemanagement-binfullalert.png)

 3. `Bin full alert` kontrollerar när **villkoret** `Fill level is greater than or equal to Bin full alert threshold`.

    `Bin full alert threshold` är en *moln egenskap* som definieras i enhets mal len `Connected waste bin`. 

Nu ska vi skapa en e-poståtgärd.

### <a name="create-an-email-action"></a>Skapa en e-poståtgärd
Så här konfigurerar du en e-poståtgärd i regelns åtgärds lista:
1. Välj **+ e-post**. 
2. Ange *hög pH-avisering* som det egna **visnings namnet** för åtgärden.
3. Ange den e-postadress som är kopplad till ditt IoT Central konto i **till**. 
4. Du kan också ange en anteckning som ska inkluderas i e-postmeddelandets text.
5. Välj **klar** för att slutföra åtgärden.
6. Välj **Spara** för att spara och aktivera den nya regeln. 

Du bör få ett e-postmeddelande när det konfigurerade **villkoret** är uppfyllt.

> [!NOTE]
> Programmet kommer att skicka ett e-postmeddelande varje gången ett villkor är uppfyllt. **Inaktivera** regeln om du vill sluta ta emot e-post från den automatiserade regeln. 
  
Så här skapar du en ny regel: 
1. Välj **+ nytt** i **reglerna** i det vänstra navigerings fönstret.

## <a name="configure-jobs"></a>Konfigurera jobb

I IoT Central kan du använda jobb för att utlösa enhets-eller moln egenskaper uppdateringar på flera enheter. Förutom egenskaper kan du också använda jobb för att utlösa enhets kommandon på flera enheter. IoT Central automatiserar arbets flödet åt dig. 

1. Gå till **jobb** från det vänstra navigerings fönstret. 
2. Klicka på **+ ny** och konfigurera ett eller flera jobb. 


## <a name="customize-your-application"></a>Anpassa ditt program 

Som verktyg kan du ändra flera inställningar för att anpassa användar upplevelsen i ditt program.

### <a name="to-change-the-application-theme"></a>Ändra programmets tema:

1. Gå till **Administration > anpassa ditt program**.
3. Använd knappen **ändra** för att välja en bild att ladda upp som **program logo typ**.
4. Använd knappen **ändra** för att välja en **ikon för en webb läsar ikon** som visas på flikar i webbläsaren.
5. Du kan också ersätta standard **webbläsarens färger** genom att lägga till HTML-hexadecimala färg koder.

   ![Azure IoT Central anpassa ditt program](./media/tutorial-connectedwastemanagement/connectedwastemanagement-customize-your-application.png)

6. Du kan också ändra program avbildningarna genom att gå till **administrations > program inställningar** och **välja knappen bild** för att välja en bild som ska laddas upp som program avbildning.
7. Slutligen kan du även ändra **temat** genom att klicka på **Inställningar** på program huvud rubriken för programmet.

  
## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet tar du bort programmet med följande steg:

1. Öppna fliken Administration i det vänstra fönstret i ditt IoT Central-program.
2. Välj program inställningar och klicka på knappen Ta bort längst ned på sidan.

  

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [anslutna metoder för avfalls hantering](./concepts-connectedwastemanagement-architecture.md)
