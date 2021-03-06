---
title: Komma igång med Android Map-kontroll i Azure Maps | Microsoft Docs
description: Kontrollen Android Map i Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 934fe2219ccca917999cf49cb9c9826276545e73
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70915664"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Komma igång med Azure Maps Android SDK

Azure Maps Android SDK är ett vektor kart bibliotek för Android. Den här artikeln vägleder dig genom processerna för att installera Azure Maps Android SDK och läsa in en karta.

## <a name="prerequisites"></a>Förutsättningar

### <a name="create-an-azure-maps-account"></a>Skapa ett Azure Maps-konto

För att slutföra procedurerna i den här artikeln måste du först [skapa ett Azure Maps-konto](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account) på pris nivån S1.

### <a name="download-android-studio"></a>Hämta Android Studio

Hämta Android Studio och skapa ett projekt med en tom aktivitet innan du installerar Azure Maps Android SDK. Du kan [Ladda ned Android Studio](https://developer.android.com/studio/) kostnads fritt från Google. 

## <a name="create-a-project-in-android-studio"></a>Skapa ett projekt i Android Studio

Börja med att skapa ett nytt projekt med en tom aktivitet. Utför de här stegen för att skapa ett Android Studio-projekt:

1. Välj **telefon och surfplatta**under **Välj ditt projekt**. Programmet kommer att köras på den här formulär faktorn.
2. På fliken **telefon och surfplatta** väljer du **Tom aktivitet**och väljer sedan **Nästa**.
3. Under **Konfigurera ditt projekt**väljer `API 21: Android 5.0.0 (Lollipop)` du som lägsta SDK. Detta är den tidigaste versionen som stöds av Azure Maps Android SDK.
4. Godkänn standard `Activity Name` och `Layout Name` Välj **Slutför**.

Se [Android Studio-dokumentationen](https://developer.android.com/studio/intro/) för att få hjälp med att installera Android Studio och skapa ett nytt projekt.

![Skapa ett projekt](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Konfigurera en virtuell enhet

Med Android Studio kan du konfigurera en virtuell Android-enhet på datorn. På så sätt kan du testa ditt program under utveckling. Om du vill konfigurera en virtuell enhet väljer du ikonen för AVD-hanteraren (Android Virtual Device) i det övre högra hörnet på projekt skärmen och väljer sedan **Skapa virtuell enhet**. Du kan också gå till avd Manager genom att välja **verktyg** > **Android** > **avd Manager** i verktygsfältet. I kategorin **telefoner** väljer du **Nexus 5x**, och väljer sedan **Nästa**.

Du kan lära dig mer om hur du konfigurerar en AVD i [Android Studio-dokumentationen](https://developer.android.com/studio/run/managing-avds).

![Android-emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Installera Azure Maps Android SDK

Nästa steg i att skapa ditt program är att installera Azure Maps Android SDK. Slutför de här stegen för att installera SDK:

1. Öppna filen **build. gradle** på den översta nivån och Lägg till följande kod i avsnittet **alla projekt**, **databaser** :

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Uppdatera **appen/build. gradle** och Lägg till följande kod i den:
    
    1. Kontrol lera att projektets **minSdkVersion** är i API 21 eller högre.

    2. Lägg till följande kod i Android-avsnittet:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Uppdatera beroende blocket och Lägg till en ny implementerings beroende linje för den senaste Azure Maps Android SDK:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

    > [!Note]
    > Azure Maps Android SDK regelbundet uppgraderas och förbättras. Du kan se dokumentationen för att [komma igång med Android Map-kontroll](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) för att hämta det senaste Azure Maps implementeringens versions nummer. Du kan också ställa in versions numret från "0,2" till "0 +" för att alltid peka på den senaste versionen.

3. Redigera **res** > layoutactivity_main >  **. XML** och ersätt den med koden:
    
    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <FrameLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        >

        <com.microsoft.azure.maps.mapcontrol.MapControl
            android:id="@+id/mapcontrol"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            />
    </FrameLayout>
    ```

4. I filen **MainActivity. java** måste du:
    
    * lägga till importer för Azure Maps SDK
    * Ange information om Azure Maps-autentisering
    * Hämta kart kontroll instansen i **onCreate** -metoden

    Att ange autentiseringsinformation för `AzureMaps` klassen globalt med hjälp av `setSubscriptionKey` metoderna eller `setAadProperties` gör det så att du inte behöver lägga till autentiseringsinformation i alla vyer. 

    Kart kontrollen innehåller sina egna livs cykel metoder för hantering av Android: s OpenGL-livscykel, som måste anropas direkt från den som innehåller aktiviteten. För att appen ska fungera korrekt ska du anropa kart kontrollens livs cykel metoder, du måste åsidosätta följande livs cykel metoder i aktiviteten som innehåller kart kontrollen och anropa respektive kart kontroll metod. 

    * onCreate (paket) 
    * onStart () 
    * onResume() 
    * onPause () 
    * onStop () 
    * onDestroy() 
    * onSaveInstanceState (paket) 
    * onLowMemory() 

    Redigera filen **MainActivity. java** enligt följande:
    
    ```java
    package com.example.myapplication;

    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;

    public class MainActivity extends AppCompatActivity {
        
        static {
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);
    
            //Wait until the map resources are ready.
            mapControl.onReady(map -> {
                //Add your post map load code here.
    
            });
        }

        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }

        @Override
        protected void onStart(){
            super.onStart();
            mapControl.onStart();
        }

        @Override
        public void onPause() {
            super.onPause();
            mapControl.onPause();
        }

        @Override
        public void onStop() {
            super.onStop();
            mapControl.onStop();
        }

        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapControl.onLowMemory();
        }

        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapControl.onDestroy();
        }

        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapControl.onSaveInstanceState(outState);
        }
    }

    ```

## <a name="import-classes"></a>Importera klasser

När du har slutfört de föregående stegen får du förmodligen varningar från Android Studio om en del av koden. Om du vill lösa de här varningarna importerar du de `MainActivity.java`klasser som refereras till i.

Du kan importera dessa klasser automatiskt genom att välja Alt + Retur (alternativ + retur på en Mac).

Välj knappen Kör, som du ser i följande bild (eller tryck på CTRL + R på en Mac) för att skapa programmet.

![Klicka på Kör](./media/how-to-use-android-map-control-library/run-app.png)

Android Studio tar några sekunder att skapa programmet. När versionen har slutförts kan du testa programmet i den emulerade Android-enheten. Du bör se en karta som den här:

<center>

![Android-karta](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="localizing-the-map"></a>Lokalisera kartan

Azure Maps Android SDK innehåller tre olika sätt att ange språk och regional vy för kartan. Följande kod visar hur du ställer in språket på franska ("fr-FR") och den regionala vyn till "Auto". 

Det första alternativet är att skicka språket och Visa regional information i `AzureMaps` klassen med hjälp av metoderna static `setLanguage` och `setView` global. Detta anger standard språket och den regionala vyn för alla Azure Maps kontroller som läses in i din app.

```Java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("auto");
}
```

Det andra alternativet är att skicka språket och Visa information i kart kontrollens XML-fil.

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

Det tredje alternativet är att program mässigt ange språket och den regionala vyn för kartan med hjälp av Maps `setStyle` -metoden. Detta kan göras när som helst för att ändra språket och den regionala vyn för kartan.

```Java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Här är ett exempel på Azure Maps med språket "fr-FR" och regional View inställd på "Auto".

<center>

![Kart bild som visar etiketter på franska](./media/how-to-use-android-map-control-library/android-localization.png)
</center>

En fullständig lista över språk som stöds och nationella vyer beskrivs [här](supported-languages.md).

## <a name="next-steps"></a>Nästa steg

Lär dig hur du lägger till överläggs data på kartan:

> [!div class="nextstepaction"]
> [Lägga till ett symbol lager i en Android-karta](https://review.docs.microsoft.com/azure/azure-maps/how-to-add-symbol-to-android-map)

> [!div class="nextstepaction"]
> [Lägga till former i en Android-karta](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Ändra kart format i Android Maps](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)
