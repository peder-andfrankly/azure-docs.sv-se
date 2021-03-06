---
title: Skicka push-meddelanden till Android med Azure Notification Hubs och Firebase Cloud Messaging | Microsoft Docs
description: I de här självstudierna lär du dig att använda Azure Notification Hubs och Google Firebase Cloud Messaging för att skicka push-meddelanden till Android-enheter.
services: notification-hubs
documentationcenter: android
keywords: push-meddelanden, push-meddelande, push-meddelande för android, fcm, firebase cloud messaging
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 02298560-da61-4bbb-b07c-e79bd520e420
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/11/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 09/11/2019
ms.openlocfilehash: 1af109405118754daa08adf848f1da3f04534455
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387510"
---
# <a name="tutorial-send-push-notifications-to-android-devices-using-google-firebase-cloud-messaging"></a>Självstudie: skicka push-meddelanden till Android-enheter med Google Firebase Cloud Messaging

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

I de här självstudierna får du lära dig hur du använder Azure Notification Hubs och Firebase Cloud Messaging (FCM) för att skicka meddelanden till ett Android-program. I den här självstudien skapar du en tom Android-app som tar emot push-meddelanden via Firebase Cloud Messaging (FCM).

Den färdiga koden för den här självstudien kan laddas ned [från GitHub](https://github.com/Azure/azure-notificationhubs-android/tree/master/samples/FCMTutorialApp).

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Skapa ett Android Studio-projekt.
> * Skapa ett Firebase-projekt som har stöd för Firebase Cloud Messaging.
> * Skapa en hubb.
> * Anslut din app till hubben.
> * Testa appen.

## <a name="prerequisites"></a>Krav

Du måste ha ett aktivt Azure-konto för att slutföra den här självstudiekursen. Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information finns i [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/free/). 

Du behöver också följande objekt: 

* Den senaste versionen av [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797)
* Android 2,3 eller högre för Firebase Cloud Messaging
* Google databas revision 27 eller högre för Firebase Cloud Messaging
* Google Play Services 9.0.2 eller senare för Firebase Cloud Messaging

Du måste slutföra den här själv studie kursen för att göra alla andra Notification Hubs själv studie kurser för Android-appar.

## <a name="create-an-android-studio-project"></a>Skapa ett Android Studio-projekt

1. Starta Android Studio.
2. Välj **fil**, peka på **nytt**och välj sedan **nytt projekt**. 
2. På sidan **Välj ditt projekt väljer du** **Tom aktivitet**och väljer sedan **Nästa**. 
3. Utför följande steg på sidan **Konfigurera ditt projekt** : 
    1. Ange ett namn på programmet.
    2. Ange en plats där du vill spara projektfilerna. 
    3. Välj **Slutför**. 

        ![Konfigurera projektet)](./media/notification-hubs-android-push-notification-google-fcm-get-started/configure-project.png)

## <a name="create-a-firebase-project-that-supports-fcm"></a>Skapa ett Firebase-projekt som har stöd för FCM

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-hub"></a>Konfigurera en hubb

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>Konfigurera Firebase Cloud Messaging-inställningar för hubben

1. Välj **Google (GCM/FCM)** i den vänstra rutan under **Inställningar** . 
2. Ange **Server nyckeln** för det FCM-projekt som du sparade tidigare. 
3. I verktygsfältet väljer **spara**. 

    ![Azure Notification Hub – Google (FCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)
4. Azure Portal visar ett meddelande i aviseringar om att navet har uppdaterats. Knappen **Spara** är inaktiverad. 

Navet har nu kon figurer ATS för att fungera med Firebase Cloud Messaging. Du har också de anslutnings strängar som krävs för att skicka meddelanden till en enhet och registrera en app för att ta emot meddelanden.

## <a id="connecting-app"></a>Anslut appen till meddelandehubben

### <a name="add-google-play-services-to-the-project"></a>Lägga till Google Play-tjänster till projektet

1. I Android Studio väljer du **verktyg** på menyn och sedan **SDK-hanteraren**. 
2. Välj mål versionen av Android SDK som används i projektet. Välj sedan **Visa paket information**. 

    ![Android SDK Manager – välj målversion](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Välj **Google API: er**om det inte redan är installerat.

    ![Android SDK Manager – Google APIs valt](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. Växla till fliken **SDK-verktyg** . Om du inte redan har installerat Google Play-tjänster väljer du **Google Play Services** som visas i följande bild. Välj sedan **Använd** för att installera. Anteckna SDK-sökvägen för användning i ett senare steg.

    ![Android SDK Manager – Google Play Services valt](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. Om du ser dialogrutan **Bekräfta ändringen** väljer du **OK**. De begärda komponenterna installeras. Välj **Slutför** när komponenterna har installerats.
4. Välj **OK** för att stänga dialogrutan **Inställningar för nya projekt**.  
1. Öppna filen AndroidManifest. xml och Lägg sedan till följande tagg i *program* tag gen.

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```


### <a name="add-azure-notification-hubs-libraries"></a>Lägg till Azure Notification Hubs-bibliotek

1. I filen build. Gradle för appen lägger du till följande rader i avsnittet beroenden.

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    ```

2. Lägg till följande lagringsplats efter avsnittet beroenden.

    ```gradle
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```

### <a name="add-google-firebase-support"></a>Lägg till stöd för Google Firebase

1. I filen build. Gradle för appen lägger du till följande rader i avsnittet **beroenden** om de inte redan finns. 

    ```gradle
    implementation 'com.google.firebase:firebase-core:16.0.8'
    implementation 'com.google.firebase:firebase-messaging:17.3.4'
    ```

2. Lägg till följande plugin-program i slutet av filen om det inte redan finns där. 

    ```gradle
    apply plugin: 'com.google.gms.google-services'
    ```
3. Välj **Synkronisera nu** i verktygsfältet.

### <a name="update-the-androidmanifestxml-file"></a>Uppdatera filen AndroidManifest. XML

1. När du har fått din FCM kan du använda den för att [Registrera dig för Azure Notification Hubs](notification-hubs-push-notification-registration-management.md). Du har stöd för den här registreringen i bakgrunden genom att använda en `IntentService` med namnet `RegistrationIntentService`. Den här tjänsten uppdaterar också din FCM-registrerings-token. Du skapar också en klass med namnet `FirebaseService` som underordnad `FirebaseMessagingService` och åsidosätter `onMessageReceived`-metoden för att ta emot och hantera meddelanden. 

    Lägg till följande tjänstedefinition i filen AndroidManifest.xml inuti taggen `<application>`.

    ```xml
    <service
        android:name=".RegistrationIntentService"
        android:exported="false">
    </service>
    <service
        android:name=".FirebaseService"
        android:exported="false">
        <intent-filter>
            <action android:name="com.google.firebase.MESSAGING_EVENT" />
        </intent-filter>
    </service>
    ```
3. Lägg till följande nödvändiga FCM-relaterade behörigheter under `</application>`-taggen.

    ```xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    ```

### <a name="add-code"></a>Lägg till kod

1. Expandera  **app** > **src** > **main** > **java** i projektvyn. Högerklicka på mappen paket under **Java**, Välj **ny**och välj sedan **Java-klass**. Ange **NotificationSettings** som namn och välj sedan **OK**.

    Se till att uppdatera dessa tre platshållare i följande kod för klassen `NotificationSettings`:

   * **HubListenConnectionString**: Anslutningssträngen **DefaultListenAccessSignature** för din hubb. Du kan kopiera anslutnings strängen genom att klicka på **åtkomst principer** i hubben i [Azure Portal].
   * **HubName**: Använd namnet på navet som visas på sidan hubb i [Azure Portal].

     `NotificationSettings` kod:

        ```java
        public class NotificationSettings {
            public static String HubName = "<Your HubName>";
            public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
        }
        ```

     > [!IMPORTANT]
     > Ange **namn** och **DefaultListenSharedAccessSignature** för navet innan du fortsätter. 

2. Lägg till ytterligare en ny klass i projektet och ge den namnet `RegistrationIntentService`. Den här klassen implementerar `IntentService`-gränssnittet. Den hanterar också [uppdateringen av FCM-token](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) och [registrering med Notification Hub](notification-hubs-push-notification-registration-management.md).

    Använd följande kod för den här klassen.

    ```java
    import android.app.IntentService;
    import android.content.Intent;
    import android.content.SharedPreferences;
    import android.preference.PreferenceManager;
    import android.util.Log;
    import com.google.android.gms.tasks.OnSuccessListener;
    import com.google.firebase.iid.FirebaseInstanceId;
    import com.google.firebase.iid.InstanceIdResult;
    import com.microsoft.windowsazure.messaging.NotificationHub;
    import java.util.concurrent.TimeUnit;

    public class RegistrationIntentService extends IntentService {

        private static final String TAG = "RegIntentService";
        String FCM_token = null;

        private NotificationHub hub;

        public RegistrationIntentService() {
            super(TAG);
        }

        @Override
        protected void onHandleIntent(Intent intent) {

            SharedPreferences sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this);
            String resultString = null;
            String regID = null;
            String storedToken = null;

            try {
                FirebaseInstanceId.getInstance().getInstanceId().addOnSuccessListener(new OnSuccessListener<InstanceIdResult>() { 
                    @Override 
                    public void onSuccess(InstanceIdResult instanceIdResult) { 
                        FCM_token = instanceIdResult.getToken(); 
                        Log.d(TAG, "FCM Registration Token: " + FCM_token); 
                    } 
                }); 
                TimeUnit.SECONDS.sleep(1);

                // Storing the registration ID that indicates whether the generated token has been
                // sent to your server. If it is not stored, send the token to your server.
                // Otherwise, your server should have already received the token.
                if (((regID=sharedPreferences.getString("registrationID", null)) == null)){

                    NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                            NotificationSettings.HubListenConnectionString, this);
                    Log.d(TAG, "Attempting a new registration with NH using FCM token : " + FCM_token);
                    regID = hub.register(FCM_token).getRegistrationId();

                    // If you want to use tags...
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                    // regID = hub.register(token, "tag1,tag2").getRegistrationId();

                    resultString = "New NH Registration Successfully - RegId : " + regID;
                    Log.d(TAG, resultString);

                    sharedPreferences.edit().putString("registrationID", regID ).apply();
                    sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                }

                // Check to see if the token has been compromised and needs refreshing.
                else if ((storedToken=sharedPreferences.getString("FCMtoken", "")) != FCM_token) {

                    NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                            NotificationSettings.HubListenConnectionString, this);
                    Log.d(TAG, "NH Registration refreshing with token : " + FCM_token);
                    regID = hub.register(FCM_token).getRegistrationId();

                    // If you want to use tags...
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                    // regID = hub.register(token, "tag1,tag2").getRegistrationId();

                    resultString = "New NH Registration Successfully - RegId : " + regID;
                    Log.d(TAG, resultString);

                    sharedPreferences.edit().putString("registrationID", regID ).apply();
                    sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                }

                else {
                    resultString = "Previously Registered Successfully - RegId : " + regID;
                }
            } catch (Exception e) {
                Log.e(TAG, resultString="Failed to complete registration", e);
                // If an exception happens while fetching the new token or updating registration data
                // on a third-party server, this ensures that we'll attempt the update at a later time.
            }

            // Notify UI that registration has completed.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(resultString);
            }
        }
    }
    ```

3. Lägg till följande `import`-uttryck ovanför klass deklarationen i `MainActivity`-klassen.

    ```java
    import com.google.android.gms.common.ConnectionResult;
    import com.google.android.gms.common.GoogleApiAvailability;
    import android.content.Intent;
    import android.util.Log;
    import android.widget.TextView;
    import android.widget.Toast;
    ```

4. Lägg till följande medlemmar högst upp i klassen. Du använder de här fälten för att [kontrollera tillgängligheten för Google Play-tjänster, enligt rekommendationer från Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).

    ```java
    public static MainActivity mainActivity;
    public static Boolean isVisible = false;
    private static final String TAG = "MainActivity";
    private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
    ```

5. I `MainActivity`-klassen lägger du till följande metod för att kontrol lera tillgängligheten för Google Play-tjänster.

    ```java
    /**
    * Check the device to make sure it has the Google Play Services APK. If
    * it doesn't, display a dialog box that enables  users to download the APK from
    * the Google Play Store or enable it in the device's system settings.
    */

    private boolean checkPlayServices() {
        GoogleApiAvailability apiAvailability = GoogleApiAvailability.getInstance();
        int resultCode = apiAvailability.isGooglePlayServicesAvailable(this);
        if (resultCode != ConnectionResult.SUCCESS) {
            if (apiAvailability.isUserResolvableError(resultCode)) {
                apiAvailability.getErrorDialog(this, resultCode, PLAY_SERVICES_RESOLUTION_REQUEST)
                        .show();
            } else {
                Log.i(TAG, "This device is not supported by Google Play Services.");
                ToastNotify("This device is not supported by Google Play Services.");
                finish();
            }
            return false;
        }
        return true;
    }
    ```

6. I `MainActivity`-klassen lägger du till följande kod som söker efter Google Play-tjänster innan du anropar `IntentService` för att hämta din FCM och registrera dig för din hubb:

    ```java
    public void registerWithNotificationHubs()
    {
        if (checkPlayServices()) {
            // Start IntentService to register this application with FCM.
            Intent intent = new Intent(this, RegistrationIntentService.class);
            startService(intent);
        }
    }
    ```

7. I `OnCreate`-metoden för klassen `MainActivity` lägger du till följande kod för att starta registrerings processen när aktiviteten skapas:

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mainActivity = this;
        registerWithNotificationHubs();
        FirebaseService.createChannelAndHandleNotifications(getApplicationContext());
    }
    ```

8. Om du vill kontrol lera appens tillstånd och rapport status i appen lägger du till följande ytterligare metoder i `MainActivity`:

    ```java
    @Override
    protected void onStart() {
        super.onStart();
        isVisible = true;
    }

    @Override
    protected void onPause() {
        super.onPause();
        isVisible = false;
    }

    @Override
    protected void onResume() {
        super.onResume();
        isVisible = true;
    }

    @Override
    protected void onStop() {
        super.onStop();
        isVisible = false;
    }

    public void ToastNotify(final String notificationMessage) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
                TextView helloText = (TextView) findViewById(R.id.text_hello);
                helloText.setText(notificationMessage);
            }
        });
    }
    ```

9. I metoden `ToastNotify` används kontrollen *”Hello World”* `TextView` för att rapportera status och meddelanden på ett beständigt sätt i appen. Lägg till följande ID för den kontrollen i layouten **res** > **layout** > **activity_main.xml**.

    ```java
    android:id="@+id/text_hello"
    ```

    ![Azure Notification Hubs – Prova att skicka](./media/notification-hubs-android-push-notification-google-fcm-get-started/activity-main-xml.png)

10. Härnäst lägger du till en underklass för mottagaren som du definierade i AndroidManifest. xml. Lägg till ytterligare en ny klass i projektet och ge den namnet `FirebaseService`.

11. Lägg till följande importuttryck längst upp i `FirebaseService.java`:

    ```java
    import com.google.firebase.messaging.FirebaseMessagingService;
    import com.google.firebase.messaging.RemoteMessage;
    import android.util.Log;
    import android.app.NotificationChannel;
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.media.RingtoneManager;
    import android.net.Uri;
    import android.os.Build;
    import android.os.Bundle;
    import androidx.core.app.NotificationCompat;
    ```

12. Lägg till följande kod för klassen `FirebaseService`, vilket gör den till en underklass till `FirebaseMessagingService`.

    Den här koden åsidosätter `onMessageReceived` metod och rapporterar meddelanden som tas emot. den skickar även push-meddelandet till Android Notification Manager med hjälp av metoden `sendNotification()`. Anropa metoden `sendNotification()` när appen inte körs och ett meddelande tas emot.

    ```java
    public class FirebaseService extends FirebaseMessagingService
    {
        private String TAG = "FirebaseService";
    
        public static final String NOTIFICATION_CHANNEL_ID = "nh-demo-channel-id";
        public static final String NOTIFICATION_CHANNEL_NAME = "Notification Hubs Demo Channel";
        public static final String NOTIFICATION_CHANNEL_DESCRIPTION = "Notification Hubs Demo Channel";
    
        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        static Context ctx;
    
        @Override
        public void onMessageReceived(RemoteMessage remoteMessage) {
            // ...
    
            // TODO(developer): Handle FCM messages here.
            // Not getting messages here? See why this may be: https://goo.gl/39bRNJ
            Log.d(TAG, "From: " + remoteMessage.getFrom());
    
            String nhMessage;
            // Check if message contains a notification payload.
            if (remoteMessage.getNotification() != null) {
                Log.d(TAG, "Message Notification Body: " + remoteMessage.getNotification().getBody());
    
                nhMessage = remoteMessage.getNotification().getBody();
            }
            else {
                nhMessage = remoteMessage.getData().values().iterator().next();
            }
    
            // Also if you intend on generating your own notifications as a result of a received FCM
            // message, here is where that should be initiated. See sendNotification method below.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(nhMessage);
            }
            sendNotification(nhMessage);
        }
    
        private void sendNotification(String msg) {
    
            Intent intent = new Intent(ctx, MainActivity.class);
            intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
    
            mNotificationManager = (NotificationManager)
                    ctx.getSystemService(Context.NOTIFICATION_SERVICE);
    
            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                    intent, PendingIntent.FLAG_ONE_SHOT);
    
            Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
            NotificationCompat.Builder notificationBuilder = new NotificationCompat.Builder(
                    ctx,
                    NOTIFICATION_CHANNEL_ID)
                    .setContentText(msg)
                    .setPriority(NotificationCompat.PRIORITY_HIGH)
                    .setSmallIcon(android.R.drawable.ic_popup_reminder)
                    .setBadgeIconType(NotificationCompat.BADGE_ICON_SMALL);
    
            notificationBuilder.setContentIntent(contentIntent);
            mNotificationManager.notify(NOTIFICATION_ID, notificationBuilder.build());
        }
    
        public static void createChannelAndHandleNotifications(Context context) {
            ctx = context;
    
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
                NotificationChannel channel = new NotificationChannel(
                        NOTIFICATION_CHANNEL_ID,
                        NOTIFICATION_CHANNEL_NAME,
                        NotificationManager.IMPORTANCE_HIGH);
                channel.setDescription(NOTIFICATION_CHANNEL_DESCRIPTION);
                channel.setShowBadge(true);
    
                NotificationManager notificationManager = context.getSystemService(NotificationManager.class);
                notificationManager.createNotificationChannel(channel);
             }
        }
    }
    ```

13. I Android Studio väljer du **skapa > återskapa** **projekt** på Meny raden för att kontrol lera att det inte finns några fel i koden. Om du får ett fel meddelande om `ic_launcher`-ikonen tar du bort följande instruktion från filen AndroidManifest. XML: 

    ```
        android:icon="@mipmap/ic_launcher"
    ```
14. Se till att du har en virtuell enhet för att köra appen. Om du inte har ett kan du lägga till ett på följande sätt:
    1. ![Öppna enhets hanteraren](./media/notification-hubs-android-push-notification-google-fcm-get-started/open-device-manager.png)
    2. ![Skapa virtuell enhet](./media/notification-hubs-android-push-notification-google-fcm-get-started/your-virtual-devices.PNG)

15. Kör appen på den valda enheten och kontrol lera att den har registrerats med hubben.

    > [!NOTE]
    > Registreringen kan Miss inledas under den första starten tills `onTokenRefresh()` metod för instans-ID-tjänst anropas. Uppdateringen bör initiera en lyckad registrering hos meddelandehubben.

    ![Enhetsregistreringen lyckades](./media/notification-hubs-android-push-notification-google-fcm-get-started/device-registration.png)

## <a name="test-send-notification-from-the-notification-hub"></a>Testa att skicka ett meddelande från meddelandehubben

Du kan skicka push-meddelanden från [Azure Portal] genom att utföra följande steg:

1. På sidan för aviserings hubben på sidan Azure Portal väljer du **testa skicka** i **fel söknings** avsnittet.
3. Välj **Android** under **Plattformar**.
4. Välj **Skicka**.  Du kan inte se ett meddelande på Android-enheten ännu eftersom du inte har kört mobilappen på den. När du har kört mobilappen väljer du knappen **Skicka** igen för att se meddelandet.
5. Se resultatet av åtgärden i listan längst ned.

    ![Azure Notification Hubs – Prova att skicka](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)
6. Du ser meddelandet på enheten. 

    ![Aviseringsmeddelande på enhet](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-on-device.png)
    

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

### <a name="run-the-mobile-app-on-emulator"></a>Kör mobilappen i emulatorn
Innan du testar push-meddelanden i en emulator ser du till att din emulator-avbildning stöder den Google API-nivå som du har valt för din app. Om avbildningen inte stöder interna Google-API: er kan du få **tjänsten\_inte\_tillgängligt** undantag.

Kontrol lera också att du har lagt till ditt Google-konto till din aktiva emulator under **inställningar** > **konton**. Annars kan dina försök att registrera med FCM leda till **autentiseringen\_misslyckat** undantag.

## <a name="next-steps"></a>Nästa steg
I den här självstudien använde du Firebase Cloud Messaging för att skicka meddelanden till alla Android-enheter som har registrerats med tjänsten. Information om hur du skickar meddelanden till specifika enheter finns i följande självstudie:

> [!div class="nextstepaction"]
>[Självstudie: push-meddelanden till vissa Android-enheter](push-notifications-android-specific-devices-firebase-cloud-messaging.md)

<!-- Images. -->

<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: https://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs Guidance]: notification-hubs-push-notification-overview.md
[Azure Portal]: https://portal.azure.com
