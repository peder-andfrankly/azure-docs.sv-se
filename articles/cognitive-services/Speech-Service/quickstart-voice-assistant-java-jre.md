---
title: 'Snabb start: anpassad röst assistent för Java (Windows, Linux) – tal service'
titleSuffix: Azure Cognitive Services
description: I den här snabb starten får du lära dig hur du använder Cognitive Services Speech SDK i ett Java-konsolprogram. Du får lära dig hur du kan ansluta klient programmet till en tidigare skapad bot Framework-robot som kon figurer ATS för att använda den direkta linje igenkännings kanalen och aktivera en röst assistent upplevelse.
services: cognitive-services
author: bidishac
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: bidishac
ms.openlocfilehash: 29f6596408a825a571682d14aceca01fa8108515
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815850"
---
# <a name="quickstart-create-a-voice-assistant-with-the-speech-sdk-java-preview"></a>Snabb start: skapa en röst assistent med talet SDK, Java (för hands version)

Snabb Starter är också tillgängliga för [tal-till-text](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=jre), [text till tal](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-java&tabs=jre)och [tal översättning](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-java&tabs=jre).

I den här artikeln skapar du ett Java-konsol program med hjälp av [Azure Cognitive Services Speech SDK](speech-sdk.md). Programmet ansluter till en tidigare skapad robot som kon figurer ATS för att använda den direkta linjens tal kanal, skickar en röst förfrågan och returnerar en röst svars aktivitet (om den har kon figurer ATS). Programmet har skapats med maven-paketet för tal-SDK och Sol förmörkelse Java IDE på Windows, Ubuntu Linux eller macOS. Det körs i en 64-bitars Java 8-körningsmiljö (JRE).

## <a name="prerequisites"></a>Krav

För den här snabbstarten krävs:

- Operativ system: Windows (64-bitars), Ubuntu Linux 16.04/18.04 (64-bit) eller macOS 10,13 eller senare.
- [Sol förmörkelse Java IDE](https://www.eclipse.org/downloads/).
- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) eller [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
- En Azure-prenumerationsnyckel för tjänsten Speech. [Hämta ett kostnads fritt](get-started.md) eller skapa det i [Azure Portal](https://portal.azure.com).
- En förkonfigurerad robot som skapats med hjälp av bot Framework version 4,2 eller senare. Roboten måste prenumerera på den nya direkt linjens tal kanal för att ta emot röst inmatningar.

  > [!NOTE]
  > Se [listan över regioner som stöds för röst assistenter](regions.md#voice-assistants) och se till att dina resurser distribueras i någon av dessa regioner.

Om du kör Ubuntu 16.04/18.04 ser du till att dessa beroenden är installerade innan du startar Sol förmörkelse:

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2 wget
```

Om du kör Windows (64-bitars) ser du till att du har installerat Microsoft Visual C++ Redistributable för din plattform:

- [Ladda ned Microsoft Visual C++ Redistributable for Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="optional-get-started-fast"></a>Valfritt: kom igång snabbt

I den här snabb starten beskrivs steg för steg hur du gör ett enkelt klient program att ansluta till din tal-aktiverade bot. Om du vill få rätt i, är den fullständiga käll koden som används i den här snabb starten tillgänglig i [exemplen för tal-SDK](https://aka.ms/csspeech/samples) under `quickstart` mappen.

## <a name="create-and-configure-project"></a>Skapa och konfigurera projektet

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

Du kan också aktivera loggning genom att uppdatera filen _Pom. XML_ så att den inkluderar följande beroende:

```xml
 <dependency>
     <groupId>org.slf4j</groupId>
     <artifactId>slf4j-simple</artifactId>
     <version>1.7.5</version>
 </dependency>
```

## <a name="add-sample-code"></a>Lägg till exempelkod

1. Du lägger till en ny tom klass i Java-projektet genom att välja **Arkiv** > **Nytt** > **Klass**.

1. I fönstret **ny Java-klass** anger du _speechsdk. snabb start_ i **paket** fältet och _main_ i fältet **namn** .

   ![Skärmbild av fönstret New Java Class (Ny Java-klass)](media/sdk/qs-java-jre-06-create-main-java.png)

1. Öppna den nyligen skapade `Main`-klassen och ersätt innehållet i `Main.java`-filen med följande start kod:

   ```java
   package speechsdk.quickstart;

   import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
   import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;
   import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConfig;
   import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConnector;
   import org.slf4j.Logger;
   import org.slf4j.LoggerFactory;

   import javax.sound.sampled.AudioFormat;
   import javax.sound.sampled.AudioSystem;
   import javax.sound.sampled.DataLine;
   import javax.sound.sampled.SourceDataLine;
   import java.io.InputStream;

   public class Main {
       final Logger log = LoggerFactory.getLogger(Main.class);

       public static void main(String[] args) {
           // New code will go here
       }

       private void playAudioStream(PullAudioOutputStream audio) {
           ActivityAudioStream stream = new ActivityAudioStream(audio);
           final ActivityAudioStream.ActivityAudioFormat audioFormat = stream.getActivityAudioFormat();
           final AudioFormat format = new AudioFormat(
                   AudioFormat.Encoding.PCM_SIGNED,
                   audioFormat.getSamplesPerSecond(),
                   audioFormat.getBitsPerSample(),
                   audioFormat.getChannels(),
                   audioFormat.getFrameSize(),
                   audioFormat.getSamplesPerSecond(),
                   false);
           try {
               int bufferSize = format.getFrameSize();
               final byte[] data = new byte[bufferSize];

               SourceDataLine.Info info = new DataLine.Info(SourceDataLine.class, format);
               SourceDataLine line = (SourceDataLine) AudioSystem.getLine(info);
               line.open(format);

               if (line != null) {
                   line.start();
                   int nBytesRead = 0;
                   while (nBytesRead != -1) {
                       nBytesRead = stream.read(data);
                       if (nBytesRead != -1) {
                           line.write(data, 0, nBytesRead);
                       }
                   }
                   line.drain();
                   line.stop();
                   line.close();
               }
               stream.close();

           } catch (Exception e) {
               e.printStackTrace();
           }
       }

   }
   ```

1. I `main`-metoden konfigurerar du först din `DialogServiceConfig` och använder den för att skapa en `DialogServiceConnector`-instans. Den här instansen ansluter till den direkta linjens tal kanal för att interagera med din robot. En `AudioConfig` instans används också för att ange källan för ljud inspelning. I det här exemplet används standard mikrofonen med `AudioConfig.fromDefaultMicrophoneInput()`.

   - Ersätt strängen `YourSubscriptionKey` med din prenumerations nyckel, som du kan hämta från [den här webbplatsen](get-started.md).
   - Ersätt strängen `YourServiceRegion` med den [region](regions.md) som är associerad med din prenumeration.

   > [!NOTE]
   > Se [listan över regioner som stöds för röst assistenter](regions.md#voice-assistants) och se till att dina resurser distribueras i någon av dessa regioner.

   ```java
   final String subscriptionKey = "YourSubscriptionKey"; // Your subscription key
   final String region = "YourServiceRegion"; // Your speech subscription service region
   final BotFrameworkConfig botConfig = BotFrameworkConfig.fromSubscription(subscriptionKey, region);

   // Configure audio input from a microphone.
   final AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();

   // Create a DialogServiceConnector instance.
   final DialogServiceConnector connector = new DialogServiceConnector(botConfig, audioConfig);
   ```

1. Anslutnings `DialogServiceConnector` förlitar sig på flera händelser för att kommunicera sina robot aktiviteter, tal igenkännings resultat och annan information. Lägg till dessa händelse lyssnare härnäst.

   ```java
   // Recognizing will provide the intermediate recognized text while an audio stream is being processed.
   connector.recognizing.addEventListener((o, speechRecognitionResultEventArgs) -> {
       log.info("Recognizing speech event text: {}", speechRecognitionResultEventArgs.getResult().getText());
   });

   // Recognized will provide the final recognized text once audio capture is completed.
   connector.recognized.addEventListener((o, speechRecognitionResultEventArgs) -> {
       log.info("Recognized speech event reason text: {}", speechRecognitionResultEventArgs.getResult().getText());
   });

   // SessionStarted will notify when audio begins flowing to the service for a turn.
   connector.sessionStarted.addEventListener((o, sessionEventArgs) -> {
       log.info("Session Started event id: {} ", sessionEventArgs.getSessionId());
   });

   // SessionStopped will notify when a turn is complete and it's safe to begin listening again.
   connector.sessionStopped.addEventListener((o, sessionEventArgs) -> {
       log.info("Session stopped event id: {}", sessionEventArgs.getSessionId());
   });

   // Canceled will be signaled when a turn is aborted or experiences an error condition.
   connector.canceled.addEventListener((o, canceledEventArgs) -> {
       log.info("Canceled event details: {}", canceledEventArgs.getErrorDetails());
       connector.disconnectAsync();
   });

   // ActivityReceived is the main way your bot will communicate with the client and uses Bot Framework activities.
   connector.activityReceived.addEventListener((o, activityEventArgs) -> {
       final String act = activityEventArgs.getActivity().serialize();
           log.info("Received activity {} audio", activityEventArgs.hasAudio() ? "with" : "without");
           if (activityEventArgs.hasAudio()) {
               playAudioStream(activityEventArgs.getAudio());
           }
       });
   ```

1. Anslut `DialogServiceConnector` till direkt linje tal genom att anropa `connectAsync()`-metoden. Om du vill testa din robot kan du anropa metoden `listenOnceAsync` för att skicka ljud inspelning från mikrofonen. Dessutom kan du också använda metoden `sendActivityAsync` för att skicka en anpassad aktivitet som en serialiserad sträng. Dessa anpassade aktiviteter kan ge ytterligare data som din robot använder i konversationen.

   ```java
   connector.connectAsync();
   // Start listening.
   System.out.println("Say something ...");
   connector.listenOnceAsync();

   // connector.sendActivityAsync(...)
   ```

1. Spara ändringarna i `Main`s filen.

1. Om du vill stödja uppspelning av svar lägger du till ytterligare en klass som transformerar PullAudioOutputStream-objektet som returnerades från getAudio () API till ett Java-InputStream för enkel hantering. Den här `ActivityAudioStream` är en specialiserad klass som hanterar ljud svar från den direkta linjens tal kanal. Den ger till gång till information som krävs för att kunna hantera uppspelning. För det väljer du **fil** > **ny** > - **klass**.

1. I fönstret **ny Java-klass** anger du _speechsdk. snabb start_ i **paket** fältet och _ActivityAudioStream_ i fältet **namn** .

1. Öppna den nyligen skapade `ActivityAudioStream`-klassen och ersätt den med följande kod:

   ```java
   package com.speechsdk.quickstart;

   import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;

   import java.io.IOException;
   import java.io.InputStream;

    public final class ActivityAudioStream extends InputStream {
        /**
         * The number of samples played per second (16 kHz).
         */
        public static final long SAMPLE_RATE = 16000;
        /**
         * The number of bits in each sample of a sound that has this format (16 bits).
         */
        public static final int BITS_PER_SECOND = 16;
        /**
         * The number of audio channels in this format (1 for mono).
         */
        public static final int CHANNELS = 1;
        /**
         * The number of bytes in each frame of a sound that has this format (2).
         */
        public static final int FRAME_SIZE = 2;

        /**
         * Reads up to a specified maximum number of bytes of data from the audio
         * stream, putting them into the given byte array.
         *
         * @param b   the buffer into which the data is read
         * @param off the offset, from the beginning of array <code>b</code>, at which
         *            the data will be written
         * @param len the maximum number of bytes to read
         * @return the total number of bytes read into the buffer, or -1 if there
         * is no more data because the end of the stream has been reached
         */
        @Override
        public int read(byte[] b, int off, int len) {
            byte[] tempBuffer = new byte[len];
            int n = (int) this.pullStreamImpl.read(tempBuffer);
            for (int i = 0; i < n; i++) {
                if (off + i > b.length) {
                    throw new ArrayIndexOutOfBoundsException(b.length);
                }
                b[off + i] = tempBuffer[i];
            }
            if (n == 0) {
                return -1;
            }
            return n;
        }

        /**
         * Reads the next byte of data from the activity audio stream if available.
         *
         * @return the next byte of data, or -1 if the end of the stream is reached
         * @see #read(byte[], int, int)
         * @see #read(byte[])
         * @see #available
         * <p>
         */
        @Override
        public int read() {
            byte[] data = new byte[1];
            int temp = read(data);
            if (temp <= 0) {
                // we have a weird situation if read(byte[]) returns 0!
                return -1;
            }
            return data[0] & 0xFF;
        }

        /**
         * Reads up to a specified maximum number of bytes of data from the activity audio stream,
         * putting them into the given byte array.
         *
         * @param b the buffer into which the data is read
         * @return the total number of bytes read into the buffer, or -1 if there
         * is no more data because the end of the stream has been reached
         */
        @Override
        public int read(byte[] b) {
            int n = (int) pullStreamImpl.read(b);
            if (n == 0) {
                return -1;
            }
            return n;
        }

        /**
         * Skips over and discards a specified number of bytes from this
         * audio input stream.
         *
         * @param n the requested number of bytes to be skipped
         * @return the actual number of bytes skipped
         * @throws IOException if an input or output error occurs
         * @see #read
         * @see #available
         */
        @Override
        public long skip(long n) {
            if (n <= 0) {
                return 0;
            }
            if (n <= Integer.MAX_VALUE) {
                byte[] tempBuffer = new byte[(int) n];
                return read(tempBuffer);
            }
            long count = 0;
            for (long i = n; i > 0; i -= Integer.MAX_VALUE) {
                int size = (int) Math.min(Integer.MAX_VALUE, i);
                byte[] tempBuffer = new byte[size];
                count += read(tempBuffer);
            }
            return count;
        }

        /**
         * Closes this audio input stream and releases any system resources associated
         * with the stream.
         */
        @Override
        public void close() {
            this.pullStreamImpl.close();
        }

        /**
         * Fetch the audio format for the ActivityAudioStream. The ActivityAudioFormat defines the sample rate, bits per sample, and the # channels.
         *
         * @return instance of the ActivityAudioFormat associated with the stream
         */
        public ActivityAudioStream.ActivityAudioFormat getActivityAudioFormat() {
            return activityAudioFormat;
        }

        /**
         * Returns the maximum number of bytes that can be read (or skipped over) from this
         * audio input stream without blocking.
         *
         * @return the number of bytes that can be read from this audio input stream without blocking.
         * As this implementation does not buffer, this will be defaulted to 0
         */
        @Override
        public int available() {
            return 0;
        }

        public ActivityAudioStream(final PullAudioOutputStream stream) {
            pullStreamImpl = stream;
            this.activityAudioFormat = new ActivityAudioStream.ActivityAudioFormat(SAMPLE_RATE, BITS_PER_SECOND, CHANNELS, FRAME_SIZE, AudioEncoding.PCM_SIGNED);
        }

        private PullAudioOutputStream pullStreamImpl;

        private ActivityAudioFormat activityAudioFormat;

        /**
         * ActivityAudioFormat is an internal format which contains metadata regarding the type of arrangement of
         * audio bits in this activity audio stream.
         */
        static class ActivityAudioFormat {

            private long samplesPerSecond;
            private int bitsPerSample;
            private int channels;
            private int frameSize;
            private AudioEncoding encoding;

            public ActivityAudioFormat(long samplesPerSecond, int bitsPerSample, int channels, int frameSize, AudioEncoding encoding) {
                this.samplesPerSecond = samplesPerSecond;
                this.bitsPerSample = bitsPerSample;
                this.channels = channels;
                this.encoding = encoding;
                this.frameSize = frameSize;
            }

            /**
             * Fetch the number of samples played per second for the associated audio stream format.
             *
             * @return the number of samples played per second
             */
            public long getSamplesPerSecond() {
                return samplesPerSecond;
            }

            /**
             * Fetch the number of bits in each sample of a sound that has this audio stream format.
             *
             * @return the number of bits per sample
             */
            public int getBitsPerSample() {
                return bitsPerSample;
            }

            /**
             * Fetch the number of audio channels used by this audio stream format.
             *
             * @return the number of channels
             */
            public int getChannels() {
                return channels;
            }

            /**
             * Fetch the default number of bytes in a frame required by this audio stream format.
             *
             * @return the number of bytes
             */
            public int getFrameSize() {
                return frameSize;
            }

            /**
             * Fetch the audio encoding type associated with this audio stream format.
             *
             * @return the encoding associated
             */
            public AudioEncoding getEncoding() {
                return encoding;
            }
        }

        /**
         * Enum defining the types of audio encoding supported by this stream.
         */
        public enum AudioEncoding {
            PCM_SIGNED("PCM_SIGNED");

            String value;

            AudioEncoding(String value) {
                this.value = value;
            }
        }
    }

   ```

1. Spara ändringarna i `ActivityAudioStream`s filen.

## <a name="build-and-run-the-app"></a>Skapa och kör appen

Välj F11 eller Välj **kör** > **Felsök**.
-Konsolen visar meddelandet "Säg något".
I det här skedet pratar vi om en engelsk fras eller mening som din robot kan förstå. Ditt tal överförs till din robot via direkt linjens tal kanal där den känns igen och bearbetas av din robot. Svaret returneras som en aktivitet. Om din robot returnerar tal som ett svar spelas ljudet upp med hjälp av `AudioPlayer`-klassen.

![Skärmbild av konsolutdata efter lyckad taligenkänning](media/sdk/qs-java-jre-08-console-output.png)

## <a name="next-steps"></a>Nästa steg

Ytterligare exempel, till exempel hur man läser tal från en ljudfil, finns på GitHub.

> [!div class="nextstepaction"]
> [Skapa och distribuera en grundläggande robot](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>Se också

- [Om röst assistenter](voice-assistants.md)
- [Hämta en prenumerations nyckel för Speech service kostnads fritt](get-started.md)
- [Anpassade nyckelord](speech-devices-sdk-create-kws.md)
- [Anslut direkt linje tal till din robot](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [Utforska Java-exempel på GitHub](https://aka.ms/csspeech/samples)
