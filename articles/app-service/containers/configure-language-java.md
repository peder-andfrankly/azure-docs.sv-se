---
title: Konfigurera Linux Java-appar
description: Lär dig hur du konfigurerar en fördefinierad Java-behållare för din app. Den här artikeln visar de vanligaste konfigurations åtgärderna.
keywords: azure app service, web app, linux, oss, java, java ee, jee, javaee
author: bmitchell287
manager: barbkess
ms.devlang: java
ms.topic: article
ms.date: 11/22/2019
ms.author: brendm
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: 571d4cd395cd0cec0982fedf267a88143fd73872
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805747"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>Konfigurera en Linux Java-app för Azure App Service

Med Azure App Service i Linux kan Java-utvecklare snabbt bygga, distribuera och skala sina Tomcat-, WildFly-eller Java Standard Edition-paketerade webb program på en fullständigt hanterad Linux-baserad tjänst. Distribuera program med maven-plugin-program från kommando raden eller i redigerings program som IntelliJ, Sol förmörkelse eller Visual Studio Code.

Den här guiden innehåller viktiga begrepp och instruktioner för Java-utvecklare som använder en inbyggd Linux-behållare i App Service. Om du aldrig har använt Azure App Service följer du själv studie kursen [Java snabb start](quickstart-java.md) och [Java med postgresql](tutorial-java-enterprise-postgresql-app.md) .

## <a name="deploying-your-app"></a>Distribuera din app

Du kan använda [maven-plugin-programmet för Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) för att distribuera både jar-och. War-filer. Distribution med populära IDE: er stöds också med [Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) eller [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

Annars beror distributions metoden på din Arkiv typ:

- Om du vill distribuera. War-filer till Tomcat använder du `/api/wardeploy/` slut punkten för att publicera Arkiv filen. Mer information om det här API: et finns i [den här dokumentationen](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Om du vill distribuera. jar-filer på Java SE-avbildningarna använder du `/api/zipdeploy/` slut punkten för kudu-webbplatsen. Mer information om det här API: et finns i [den här dokumentationen](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Distribuera inte. War eller. jar med FTP. FTP-verktyget är utformat för att ladda upp start skript, beroenden eller andra runtime-filer. Det är inte det bästa valet för att distribuera webbappar.

## <a name="logging-and-debugging-apps"></a>Logga och Felsök appar

Prestanda rapporter, trafik visualiseringar och hälso checkups är tillgängliga för varje app via Azure Portal. Mer information finns i [Översikt över Azure App Service-diagnostik](../overview-diagnostics.md).

### <a name="ssh-console-access"></a>Åtkomst till SSH-konsol

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Strömma diagnostikloggar

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

Mer information finns i [Stream-loggar i Cloud Shell](../troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Loggning av app

Aktivera [program loggning](../troubleshoot-diagnostic-logs.md?toc=/azure/app-service/containers/toc.json#enable-application-logging-windows) via Azure Portal eller [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) för att konfigurera App Service att skriva programmets standard-och standard konsol fel strömmar till det lokala fil systemet eller Azure-Blob Storage. Loggning till den lokala App Service fil Systems instansen är inaktive rad 12 timmar efter att den har kon figurer ATS Om du behöver kvarhållare kan du konfigurera programmet att skriva utdata till en Blob Storage-behållare. Du hittar dina program loggar för Java och Tomcat i katalogen */Home/LogFiles/Application/* .

Om ditt program använder [logback](https://logback.qos.ch/) eller [log4j](https://logging.apache.org/log4j) för spårning kan du vidarebefordra spårningarna för granskning till Azure Application insikter med hjälp av konfigurations anvisningar för loggnings ramverk i [utforska Java trace-loggar i Application Insights](/azure/application-insights/app-insights-java-trace-logs).

### <a name="troubleshooting-tools"></a>Verktyg för felsökning

De inbyggda Java-avbildningarna baseras på operativ systemet [Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) . Använd `apk` Package Manager för att installera eventuella fel söknings verktyg eller kommandon.

### <a name="flight-recorder"></a>Flyg inspelning

Alla Linux Java-avbildningar på App Service har Zulu Flight-brännare installerad så att du enkelt kan ansluta till JVM och starta en profilerings inspelning eller generera en heap-dump.

#### <a name="timed-recording"></a>Inspelnings tid

Kom igång genom att använda SSH i din App Service och köra kommandot `jcmd` för att se en lista över alla Java-processer som körs. Förutom jcmd bör du se ditt Java-program med ett process-ID-nummer (PID).

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Kör kommandot nedan för att starta en 30-sekunders inspelning av JVM. Detta kommer att profilera JVM och skapa en JFR-fil med namnet *jfr_example. jfr* i arbets katalogen. (Ersätt 116 med PID för din Java-app.)

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

Under det 30 andra intervallet kan du verifiera att inspelningen sker genom att köra `jcmd 116 JFR.check`. Då visas alla inspelningar för den aktuella Java-processen.

#### <a name="continuous-recording"></a>Kontinuerlig inspelning

Du kan använda Zulu Flight-brännare för att kontinuerligt profilera ditt Java-program med minimal påverkan på körnings prestanda ([källa](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf)). Det gör du genom att köra följande Azure CLI-kommando för att skapa en app-inställning med namnet JAVA_OPTS med den nödvändiga konfigurationen. Innehållet i inställningen JAVA_OPTS app skickas till kommandot `java` när appen startas.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

När inspelningen har startats kan du när som helst dumpa de aktuella inspelnings data med hjälp av kommandot `JFR.dump`.

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

Mer information finns i [kommando referensen för Jcmd](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

### <a name="analyzing-recordings"></a>Analysera inspelningar

Använd [FTPS](../deploy-ftp.md) för att ladda ned jfr-filen till den lokala datorn. Om du vill analysera JFR-filen laddar du ned och installerar [Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/). Anvisningar om hur du Zulu uppdrags kontroll finns i [Azul-dokumentationen](https://docs.azul.com/zmc/) och i [installations anvisningarna](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control).

## <a name="customization-and-tuning"></a>Anpassning och justering

Azure App Service för Linux stöder direkt justering och anpassning genom Azure Portal och CLI. Läs följande artiklar för konfiguration av icke-Java-en webbapp:

- [Konfigurera appinställningar](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings)
- [Konfigurera en anpassad domän](../app-service-web-tutorial-custom-domain.md?toc=/azure/app-service/containers/toc.json)
- [Konfigurera SSL-bindningar](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json)
- [Lägg till en CDN](../../cdn/cdn-add-to-web-app.md?toc=/azure/app-service/containers/toc.json)
- [Konfigurera kudu-webbplatsen](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Ange Java Runtime-alternativ

Om du vill ange allokerat minne eller andra alternativ för JVM körning i båda miljöerna Tomcat och Java SE skapar du en [app-inställning](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings) med namnet `JAVA_OPTS` med alternativen. App Service Linux skickar den här inställningen som en miljö variabel till Java-körningsmiljön när den startas.

I Azure Portal, under **program inställningar** för webbappen, skapar du en ny app-inställning med namnet `JAVA_OPTS` som innehåller ytterligare inställningar, till exempel `-Xms512m -Xmx1204m`.

Om du vill konfigurera appens inställning från maven-plugin-programmet lägger du till inställningar/värde-Taggar i Azure plugin-avsnittet. I följande exempel anges en angiven minsta och högsta Java-Heap-storlek:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Utvecklare som kör ett enda program med en distributions plats i sin App Service plan kan använda följande alternativ:

- B1 och S1-instanser: `-Xms1024m -Xmx1024m`
- B2-och S2-instanser: `-Xms3072m -Xmx3072m`
- B3 och S3-instanser: `-Xms6144m -Xmx6144m`

När du finjusterar inställningarna för programheap granskar du App Service plan information och tar hänsyn till flera program och distributions fack måste hitta den optimala allokeringen av minnet.

Om du distribuerar ett JAR-program ska det heta *app. jar* så att den inbyggda avbildningen kan identifiera din app korrekt. (Plugin-programmet maven byter namn på det automatiskt.) Om du inte vill byta namn på JAR till *app. jar*kan du ladda upp ett shell-skript med kommandot för att köra din jar. Klistra sedan in den fullständiga sökvägen till skriptet i text rutan för [Start filen](app-service-linux-faq.md#built-in-images) i konfigurations avsnittet i portalen. Start skriptet körs inte från den katalog där den placeras. Använd därför alltid absoluta sökvägar för att referera till filer i Start skriptet (till exempel: `java -jar /home/myapp/myapp.jar`).

### <a name="turn-on-web-sockets"></a>Aktivera webb-Sockets

Aktivera stöd för Web Sockets i Azure Portal i **program inställningarna** för programmet. Du måste starta om programmet för att inställningen ska börja gälla.

Aktivera WebSocket-stöd med hjälp av Azure CLI med följande kommando:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

Starta sedan om ditt program:

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>Ange standard tecken kodning

I Azure Portal, under **program inställningar** för webbappen, skapar du en ny app-inställning med namnet `JAVA_OPTS` med värde `-Dfile.encoding=UTF-8`.

Du kan också konfigurera appens inställning med hjälp av App Service maven-plugin-programmet. Lägg till inställnings namn och värde Taggar i plugin-konfigurationen:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="adjust-startup-timeout"></a>Justera tids gräns för start

Om Java-programmet är särskilt stort bör du öka tids gränsen för start. Om du vill göra det skapar du en program inställning `WEBSITES_CONTAINER_START_TIME_LIMIT` och anger den till det antal sekunder som App Service ska vänta innan tids gränsen uppnåddes. Det maximala värdet är `1800` sekunder.

### <a name="pre-compile-jsp-files"></a>Kompilera JSP-filer i förväg

För att förbättra prestanda för Tomcat-program kan du kompilera JSP-filerna innan du distribuerar till App Service. Du kan använda [plugin-programmet maven](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) som tillhandahålls av Apache sling eller med hjälp av den här [Ant build-filen](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Säkra program

Java-program som körs i App Service för Linux har samma uppsättning [rekommenderade säkerhets metoder](/azure/security/security-paas-applications-using-app-services) som andra program.

### <a name="authenticate-users-easy-auth"></a>Autentisera användare (enkel autentisering)

Konfigurera app-autentisering i Azure Portal med alternativet **autentisering och auktorisering** . Därifrån kan du aktivera autentisering med Azure Active Directory eller sociala inloggningar som Facebook, Google eller GitHub. Azure Portal konfiguration fungerar bara när du konfigurerar en enda autentiseringsprovider. Mer information finns i [Konfigurera din app service app för att använda Azure Active Directory inloggning](../configure-authentication-provider-aad.md?toc=/azure/app-service/containers/toc.json) och relaterade artiklar för andra identitets leverantörer. Om du behöver aktivera flera inloggnings leverantörer följer du anvisningarna i artikeln [anpassa App Service-autentisering](../app-service-authentication-how-to.md?toc=/azure/app-service/containers/toc.json) .

#### <a name="tomcat-and-wildfly"></a>Tomcat och WildFly

Ditt Tomcat-eller WildFly-program kan komma åt användarens anspråk direkt från servlet genom att omvandla huvudobjektet till ett kart objekt. Kart-objektet mappar varje anspråks typ till en samling av anspråken för den typen. I koden nedan är `request` en instans av `HttpServletRequest`.

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Nu kan du kontrol lera `Map`-objektet för ett enskilt anspråk. Följande kodfragment itererar till exempel igenom alla anspråks typer och skriver ut innehållet i varje samling.

```java
for (Object key : map.keySet()) {
        Object value = map.get(key);
        if (value != null && value instanceof Collection {
            Collection claims = (Collection) value;
            for (Object claim : claims) {
                System.out.println(claims);
            }
        }
    }
```

Använd `/.auth/ext/logout` Sök väg för att logga ut användare. Om du vill utföra andra åtgärder kan du läsa dokumentationen om [App Service autentisering och auktorisering](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to). Det finns också en officiell dokumentation om Tomcat [HttpServletRequest-gränssnittet](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) och dess metoder. Följande servlet-metoder har också dehydratiseras baserat på din App Service konfiguration:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Om du vill inaktivera den här funktionen skapar du en program inställning med namnet `WEBSITE_AUTH_SKIP_PRINCIPAL` med värdet `1`. Om du vill inaktivera alla servlet-filter som har lagts till av App Service skapar du en inställning med namnet `WEBSITE_SKIP_FILTERS` med värdet `1`.

#### <a name="spring-boot"></a>Spring Boot

Förstarts utvecklare kan använda [Start Start programmet Azure Active Directory våren](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) för att skydda program med hjälp av välbekanta säkerhets anteckningar och API: er. Se till att öka storleken på den maximala sidhuvuden i *program. Properties* -filen. Vi föreslår ett värde för `16384`.

### <a name="configure-tlsssl"></a>Konfigurera TLS/SSL

Följ anvisningarna i avsnittet [skydda ett anpassat DNS-namn med en SSL-bindning i Azure App Service](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json) för att ladda upp ett befintligt SSL-certifikat och bind det till ditt programs domän namn. Som standard tillåter programmet fortfarande HTTP-anslutningar – Följ de specifika stegen i självstudien för att genomdriva SSL och TLS.

### <a name="use-keyvault-references"></a>Använda nyckel Valvs referenser

Azure-nyckels [valvet](../../key-vault/key-vault-overview.md) tillhandahåller centraliserad hemlig hantering med åtkomst principer och gransknings historik. Du kan lagra hemligheter (till exempel lösen ord eller anslutnings strängar) i ett nyckel valv och komma åt dessa hemligheter i ditt program via miljövariabler.

Börja med att följa anvisningarna för [att ge appen åtkomst till Key Vault](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) och [skapa en nyckel valv referens till din hemlighet i en program inställning](../app-service-key-vault-references.md#reference-syntax). Du kan kontrol lera att referensen matchar hemligheten genom att skriva ut miljövariabeln och fjärrans luta till App Service terminalen.

Om du vill mata in de här hemligheterna i din våren-eller Tomcat-konfigurationsfil använder du syntaxen för miljö variabel insprutning (`${MY_ENV_VAR}`). För våren-konfigurationsfiler läser du den här dokumentationen om de [externa konfigurationerna](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

### <a name="using-the-java-key-store"></a>Använda Java-nyckel arkivet

Som standard kommer alla offentliga eller privata certifikat som [laddats upp till App Service Linux](../configure-ssl-certificate.md) att läsas in i Java-nyckel arkivet när behållaren startar. Det innebär att dina uppladdade certifikat är tillgängliga i anslutnings kontexten när du gör utgående TLS-anslutningar. När du har laddat upp certifikatet måste du starta om App Service för att det ska kunna läsas in i Java-nyckel arkivet.

Du kan interagera eller felsöka Java-nyckel verktyget genom att [öppna en SSH-anslutning](app-service-linux-ssh-support.md) till app service och köra kommandot `keytool`. En lista över kommandon finns i [dokumentationen för nyckel verktyget](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) . Certifikaten lagras i Javas standard plats för nyckel lagring, `$JAVA_HOME/jre/lib/security/cacerts`.

Ytterligare konfiguration kan vara nödvändig för att kryptera JDBC-anslutningen. Se dokumentationen för din valda JDBC-drivrutin.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/connecting-with-ssl-encryption?view=sql-server-ver15)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [MongoDB](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/developer/java-driver/4.3/)


#### <a name="manually-initialize-and-load-the-key-store"></a>Initiera och Läs in nyckel lagret manuellt

Du kan initiera nyckel lagringen och lägga till certifikat manuellt. Skapa en app-inställning, `SKIP_JAVA_KEYSTORE_LOAD`, med värdet `1` för att inaktivera App Service från att läsa in certifikaten i nyckel arkivet automatiskt. Alla offentliga certifikat som laddats upp till App Service via Azure Portal lagras under `/var/ssl/certs/`. Privata certifikat lagras under `/var/ssl/private/`.

Mer information om API: t för nyckel Arkiv finns i [den officiella dokumentationen](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html).

## <a name="configure-apm-platforms"></a>Konfigurera APM-plattformar

Det här avsnittet visar hur du ansluter Java-program som distribuerats på Azure App Service i Linux med NewRelic-och AppDynamics-plattformarna för program prestanda övervakning (APM).

### <a name="configure-new-relic"></a>Konfigurera nya Relic

1. Skapa ett NewRelic-konto på [NewRelic.com](https://newrelic.com/signup)
2. Hämta Java-agenten från NewRelic kommer den att ha ett fil namn som liknar *newrelic-Java-x. x. x. zip*.
3. Kopiera licens nyckeln, du behöver den för att konfigurera agenten senare.
4. [Använda SSH i App Service-instansen](app-service-linux-ssh-support.md) och skapa en ny katalog */Home/site/wwwroot/APM*.
5. Överför de uppackade NewRelic Java agent-filerna till en katalog under */Home/site/wwwroot/APM*. Filerna för din agent ska vara i */Home/site/wwwroot/APM/newrelic*.
6. Ändra YAML-filen på */Home/site/wwwroot/APM/newrelic/newrelic.yml* och ersätt licens värdet för plats hållaren med din egen licens nyckel.
7. I Azure Portal bläddrar du till ditt program i App Service och skapar en ny program inställning.
    - Om din app använder **Java se**skapar du en miljö variabel med namnet `JAVA_OPTS` med värdet `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Om du använder **Tomcat**skapar du en miljö variabel med namnet `CATALINA_OPTS` med värdet `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Om du använder **WildFly**kan du läsa mer i den nya Relic [-dokumentationen för att få](https://docs.newrelic.com/docs/agents/java-agent/additional-installation/wildfly-version-11-installation-java) vägledning om hur du installerar Java-agenten och JBoss-konfigurationen.

### <a name="configure-appdynamics"></a>Konfigurera AppDynamics

1. Skapa ett AppDynamics-konto på [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Hämta Java-agenten från AppDynamics-webbplatsen kommer fil namnet att likna *AppServerAgent-x. x. x. XXXXX. zip*
3. [Använda SSH i App Service-instansen](app-service-linux-ssh-support.md) och skapa en ny katalog */Home/site/wwwroot/APM*.
4. Överför Java-agentens filer till en katalog under */Home/site/wwwroot/APM*. Filerna för din agent ska vara i */Home/site/wwwroot/APM/AppDynamics*.
5. I Azure Portal bläddrar du till ditt program i App Service och skapar en ny program inställning.
    - Om du använder **Java se**skapar du en miljö variabel med namnet `JAVA_OPTS` med värdet `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` där `<app-name>` är App Service namn.
    - Om du använder **Tomcat**skapar du en miljö variabel med namnet `CATALINA_OPTS` med värdet `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` där `<app-name>` är App Service namn.
    - Om du använder **WildFly**kan du läsa mer i AppDynamics [-dokumentationen om](https://docs.appdynamics.com/display/PRO45/JBoss+and+Wildfly+Startup+Settings) hur du installerar Java-agenten och JBoss-konfigurationen.

> [!NOTE]
> Om du redan har en miljö variabel för `JAVA_OPTS` eller `CATALINA_OPTS`lägger du till alternativet `-javaagent:/...` till slutet av det aktuella värdet.

## <a name="configure-jar-applications"></a>Konfigurera JAR-program

### <a name="starting-jar-apps"></a>Starta JAR-appar

Som standard förväntar App Service att JAR-programmet ska ha namnet *app. jar*. Om det har det här namnet kommer det att köras automatiskt. För maven-användare kan du ange JAR-namnet genom att inkludera `<finalName>app</finalName>` i avsnittet `<build>` i *Pom. XML*. [Du kan göra samma sak i Gradle](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName) genom att ange egenskapen `archiveFileName`.

Om du vill använda ett annat namn för din JAR måste du också ange [Start kommandot](app-service-linux-faq.md#built-in-images) som kör jar-filen. Till exempel `java -jar my-jar-app.jar`. Du kan ställa in värdet för ditt Start kommando i portalen, under konfiguration > allmänna inställningar eller med en program inställning med namnet `STARTUP_COMMAND`.

### <a name="server-port"></a>Server Port

App Service Linux dirigerar inkommande begär anden till port 80, så att ditt program även lyssnar på port 80. Du kan göra detta i programmets konfiguration (t *. ex. vår app. Properties* -fil) eller i Start kommandot (till exempel `java -jar spring-app.jar --server.port=80`). Se följande dokumentation för vanliga Java-ramverk:

- [Våren Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [SparkJava](http://sparkjava.com/documentation#embedded-web-server)
- [Micronaut](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [Play-ramverk](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
- [Vertx](https://vertx.io/docs/vertx-core/java/#_start_the_server_listening)
- [Quarkus](https://quarkus.io/guides/application-configuration-guide)

## <a name="data-sources"></a>Datakällor

### <a name="tomcat"></a>Tomcat

Dessa anvisningar gäller för alla databas anslutningar. Du måste fylla i plats hållarna med den valda databasens driv rutins klass namn och JAR-fil. Det angivna är en tabell med klass namn och hämtning av driv rutiner för vanliga databaser.

| Databas   | Klass namn för driv rutin                             | JDBC Driver                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Ladda ned](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Hämta](https://dev.mysql.com/downloads/connector/j/) (Välj plattform oberoende) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Ladda ned](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

Om du vill konfigurera Tomcat för att använda Java Database Connectivity (JDBC) eller Java persistence API (JPA) måste du först anpassa `CATALINA_OPTS`-miljövariabeln som läses in av Tomcat vid start. Ange dessa värden via en app-inställning i [maven-plugin-programmet för App Service](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Eller ange miljövariabler på sidan **konfiguration** > **program inställningar** i Azure Portal.

Ta sedan reda på om data källan ska vara tillgänglig för ett program eller för alla program som körs på Tomcat-servlet.

#### <a name="application-level-data-sources"></a>Data källor på program nivå

1. Skapa en *context. XML-* fil i projektets *meta-inf/* katalog. Skapa *meta-inf/-* katalogen om den inte finns.

2. I *context. XML*lägger du till ett `Context`-element för att länka data källan till en JNDI-adress. Ersätt `driverClassName` plats hållaren med driv Rutinens klass namn från tabellen ovan.

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection"
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}"
            password="${connURL}"
        />
    </Context>
    ```

3. Uppdatera programmets *Web. XML* så att data källan används i ditt program.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Delade resurser på server nivå

Om du lägger till en delad data källa på server nivå krävs det att du redigerar Tomcat Server. xml. Börja med att ladda upp ett [Start skript](app-service-linux-faq.md#built-in-images) och ange sökvägen till skriptet i **konfigurations** > **Start kommando**. Du kan ladda upp start skriptet med [FTP](../deploy-ftp.md).

Start skriptet gör en XSL- [transformering](https://www.w3schools.com/xml/xsl_intro.asp) till filen Server. xml och utdata till den resulterande XML-filen för att `/usr/local/tomcat/conf/server.xml`. Start skriptet bör installera libxslt via APK. XSL-filen och start skriptet kan överföras via FTP. Nedan visas ett exempel på ett start skript.

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /usr/local/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /home/tomcat/conf/server.xml
```

Ett exempel på en XSL-fil anges nedan. Exempel-XSL-filen lägger till en ny anslutnings-nod till Tomcat-Server. xml.

```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:output method="xml" indent="yes"/>

  <xsl:template match="@* | node()" name="Copy">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()"/>
    </xsl:copy>
  </xsl:template>

  <xsl:template match="@* | node()" mode="insertConnector">
    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template match="comment()[not(../Connector[@scheme = 'https']) and
                                 contains(., '&lt;Connector') and
                                 (contains(., 'scheme=&quot;https&quot;') or
                                  contains(., &quot;scheme='https'&quot;))]">
    <xsl:value-of select="." disable-output-escaping="yes" />
  </xsl:template>

  <xsl:template match="Service[not(Connector[@scheme = 'https'] or
                                   comment()[contains(., '&lt;Connector') and
                                             (contains(., 'scheme=&quot;https&quot;') or
                                              contains(., &quot;scheme='https'&quot;))]
                                  )]
                      ">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()" mode="insertConnector" />
    </xsl:copy>
  </xsl:template>

  <!-- Add the new connector after the last existing Connnector if there is one -->
  <xsl:template match="Connector[last()]" mode="insertConnector">
    <xsl:call-template name="Copy" />

    <xsl:call-template name="AddConnector" />
  </xsl:template>

  <!-- ... or before the first Engine if there is no existing Connector -->
  <xsl:template match="Engine[1][not(preceding-sibling::Connector)]"
                mode="insertConnector">
    <xsl:call-template name="AddConnector" />

    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template name="AddConnector">
    <!-- Add new line -->
    <xsl:text>&#xa;</xsl:text>
    <!-- This is the new connector -->
    <Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true" 
               maxThreads="150" scheme="https" secure="true" 
               keystroreFile="${{user.home}}/.keystore" keystorePass="changeit"
               clientAuth="false" sslProtocol="TLS" />
  </xsl:template>
  
</xsl:stylesheet>
```

#### <a name="finalize-configuration"></a>Slutför konfiguration

Till sist placerar du driv rutins jar v7 i Tomcat-classpath och startar om App Service.

1. Se till att JDBC-drivrutinsfiler är tillgängliga för Tomcat-classloader genom att placera dem i */Home/Tomcat/lib* -katalogen. (Skapa den här katalogen om den inte redan finns.) Utför följande steg för att överföra filerna till din App Service-instans:

    1. I [Cloud Shell](https://shell.azure.com)installerar du webapp-tillägget:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Kör följande CLI-kommando för att skapa en SSH-tunnel från det lokala systemet till App Service:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Anslut till den lokala tunnel porten med din SFTP-klient och överför filerna till mappen */Home/Tomcat/lib* .

    Alternativt kan du använda en FTP-klient för att ladda upp JDBC-drivrutinen. Följ de här [anvisningarna för att hämta dina FTP-autentiseringsuppgifter](../deploy-configure-credentials.md?toc=/azure/app-service/containers/toc.json).

2. Om du har skapat en data källa på server nivå startar du om App Service Linux-programmet. Tomcat kommer att återställa `CATALINA_BASE` till `/home/tomcat` och använda den uppdaterade konfigurationen.

### <a name="spring-boot"></a>Spring Boot

För att ansluta till data källor i Start program för våren rekommenderar vi att du skapar anslutnings strängar och infogar dem i *appens. egenskaps* fil.

1. I avsnittet "konfiguration" på sidan App Service anger du ett namn för strängen, klistrar in JDBC-anslutningssträngen i fältet värde och anger typen anpassad. Du kan också ange den här anslutnings strängen som plats inställning.

    Den här anslutnings strängen är tillgänglig för vårt program som en miljö variabel med namnet `CUSTOMCONNSTR_<your-string-name>`. Anslutnings strängen som vi skapade ovan får exempelvis namnet `CUSTOMCONNSTR_exampledb`.

2. I din *app. Properties* -fil refererar du till den här anslutnings strängen med miljö variabel namnet. I vårt exempel skulle vi använda följande.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Mer information om det här ämnet finns i [vår Boot-dokumentation om data åtkomst](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) och [externa konfigurationer](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) .

## <a name="configure-java-ee-wildfly"></a>Konfigurera Java EE (WildFly)

> [!NOTE]
> Java Enterprise Edition på App Service Linux är för närvarande en för hands version. Den här stacken rekommenderas **inte** för produktion till sitt arbete.

Med Azure App Service i Linux kan Java-utvecklare bygga, distribuera och skala Java Enterprise-program (Java-program) på en fullständigt hanterad Linux-baserad tjänst.  Den underliggande Java Enterprise runtime-miljön är program servern med öppen källkod [WildFly](https://wildfly.org/) .

Det här avsnittet innehåller följande underavsnitt:

- [Skala med App Service](#scale-with-app-service)
- [Anpassa konfigurationen av program Server](#customize-application-server-configuration)
- [Installera moduler och beroenden](#install-modules-and-dependencies)
- [Konfigurera data källor](#configure-data-sources)
- [Använd Service Bus som en meddelande koordinator](#use-service-bus-as-a-message-broker)

### <a name="scale-with-app-service"></a>Skala med App Service

Den WildFly program server som körs i App Service på Linux körs i fristående läge, inte i en domän konfiguration. När du skalar App Service-planen konfigureras varje WildFly-instans som en fristående server.

Skala ditt program lodrätt eller vågrätt med [skalnings regler](../../monitoring-and-diagnostics/monitoring-autoscale-get-started.md) och genom [att öka antalet instanser](../manage-scale-up.md?toc=/azure/app-service/containers/toc.json).

### <a name="customize-application-server-configuration"></a>Anpassa konfigurationen av program Server

Web App-instanser är tillstånds lösa, så varje ny instans som startas måste konfigureras vid start för att stödja den WildFly-konfiguration som krävs av programmet.
Du kan skriva ett start bash-skript för att anropa WildFly CLI för att:

- Konfigurera data källor
- Konfigurera meddelande leverantörer
- Lägg till andra moduler och beroenden i WildFly Server-konfigurationen.

Skriptet körs när WildFly är igång, men innan programmet startas. Skriptet bör använda [JBoss CLI](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface) som anropas från */opt/JBoss/WildFly/bin/JBoss-cli.sh* för att konfigurera program servern med valfri konfiguration eller ändringar som behövs när servern har startats.

Använd inte det interaktiva läget i CLI för att konfigurera WildFly. I stället kan du ange ett skript för kommandon i JBoss CLI med kommandot `--file`, till exempel:

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

Använd FTP för att ladda upp start skriptet till en plats i App Service-instansen under katalogen */Home* , till exempel */Home/site/Deployments/tools*. Mer information finns i [distribuera din app för att Azure App Service att använda FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp).

Ange fältet **Start skript** i Azure Portal till platsen för start gränssnitts skriptet, till exempel */Home/site/Deployments/tools/Your-Startup-script.sh*.

Ange [appinställningar](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings) i program konfigurationen för att överföra miljövariabler för användning i skriptet. Program inställningarna behåller anslutnings strängar och andra hemligheter som krävs för att konfigurera programmet från versions kontroll.

### <a name="install-modules-and-dependencies"></a>Installera moduler och beroenden

Om du vill installera moduler och deras beroenden i WildFly-classpath via JBoss CLI måste du skapa följande filer i en egen katalog. Vissa moduler och beroenden kan behöva ytterligare konfiguration, t. ex. JNDI namngivning eller annan API-speciell konfiguration, så att listan är en minsta uppsättning av vad du behöver för att konfigurera ett beroende i de flesta fall.

- En [Beskrivning av XML-modulen](https://jboss-modules.github.io/jboss-modules/manual/#descriptors). XML-filen definierar namn, attribut och beroenden för din modul. Den här [exempel filen module. XML](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource) definierar en postgres-modul, dess jar-fil JDBC-beroende och andra beroenden för modul som krävs.
- Alla nödvändiga JAR-filberoenden för modulen.
- Ett skript med dina JBoss CLI-kommandon för att konfigurera den nya modulen. Den här filen innehåller dina kommandon som ska köras av JBoss CLI för att konfigurera servern att använda beroendet. Dokumentation om kommandon för att lägga till moduler, data källor och meddelande leverantörer finns i [det här dokumentet](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
- Ett bash start skript för att anropa JBoss CLI och köra skriptet i föregående steg. Den här filen kommer att köras när App Service instansen startas om eller när nya instanser tillhandahålls under en utskalning. Det här start skriptet är där du kan utföra andra konfigurationer för ditt program eftersom JBoss-kommandon skickas till JBoss CLI. Filen kan vara minst ett enda kommando för att skicka ditt JBoss CLI-kommando skript till JBoss CLI:

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

När du har filer och innehåll för modulen följer du stegen nedan för att lägga till modulen i WildFly-program servern.

1. Använd FTP för att ladda upp filer till en plats i App Service-instansen under katalogen */Home* , till exempel */Home/site/Deployments/tools*. Mer information finns i [distribuera din app för att Azure App Service att använda FTP/S](../deploy-ftp.md).
2. På sidan **konfiguration** > **allmänna inställningar** i Azure Portal ställer du in fältet **Start skript** till platsen för ditt Start gränssnitts skript, till exempel */Home/site/Deployments/tools/startup.sh*.
3. Starta om App Service-instansen genom att trycka på knappen **starta om** i **översikts** avsnittet i portalen eller använda Azure CLI.

### <a name="configure-data-sources"></a>Konfigurera data källor

Om du vill konfigurera WildFly/JBoss för åtkomst till en data källa använder du den allmänna processen som beskrivs ovan i avsnittet "installera moduler och beroenden". Följande avsnitt innehåller detaljerad information om den här processen för PostgreSQL, MySQL och SQL Server data källor.

Det här avsnittet förutsätter att du redan har en app, en App Service instans och en Azure Database Service-instans. Anvisningarna nedan refererar till App Service namn, resurs grupp och information om databas anslutningen. Du hittar den här informationen på Azure Portal.

Om du föredrar att gå igenom hela processen från början med en exempel app, se [Självstudier: bygga en Java EE-och postgres-webbapp i Azure](tutorial-java-enterprise-postgresql-app.md).

I följande steg förklaras kraven för att ansluta befintliga App Service och databaser.

1. Hämta JDBC-drivrutinen för [postgresql](https://jdbc.postgresql.org/download.html), [MySQL](https://dev.mysql.com/downloads/connector/j/)eller [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server). Packa ned det hämtade arkivet för att hämta filen driver. jar.

2. Skapa en fil med ett namn som *module. XML* och Lägg till följande markering. Ersätt `<module name>` placeholder (inklusive vinkelparenteser) med `org.postgres` för PostgreSQL, `com.mysql` för MySQL eller `com.microsoft` för SQL Server. Ersätt `<JDBC .jar file path>` med namnet på. jar-filen från föregående steg, inklusive den fullständiga sökvägen till platsen där du vill placera filen i din App Service-instans. Detta kan vara vilken plats som helst under katalogen */Home* .

    ```xml
    <?xml version="1.0" ?>
    <module xmlns="urn:jboss:module:1.1" name="<module name>">
        <resources>
           <resource-root path="<JDBC .jar file path>" />
        </resources>
        <dependencies>
            <module name="javax.api"/>
            <module name="javax.transaction.api"/>
        </dependencies>
    </module>
    ```

3. Skapa en fil med ett namn som *DataSource-commands. CLI* och Lägg till följande kod. Ersätt `<JDBC .jar file path>` med det värde som du använde i föregående steg. Ersätt `<module file path>` med fil namnet och App Service sökvägen från föregående steg, till exempel */Home/module.XML*.

    **PostgreSQL**

    ```console
    module add --name=org.postgres --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=postgres:add(driver-name=postgres,driver-module-name=org.postgres,driver-class-name=org.postgresql.Driver,driver-xa-datasource-class-name=org.postgresql.xa.PGXADataSource)

    data-source add --name=postgresDS --driver-name=postgres --jndi-name=java:jboss/datasources/postgresDS --connection-url=$DATABASE_CONNECTION_URL --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=org.postgresql.Driver --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLExceptionSorter --jta=true --use-java-context=true --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLValidConnectionChecker

    reload --use-current-server-config=true
    ```

    **MySQL**

    ```console
    module add --name=com.mysql --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=mysql:add(driver-name=mysql,driver-module-name=com.mysql,driver-class-name=com.mysql.cj.jdbc.Driver)

    data-source add --name=mysqlDS --jndi-name=java:jboss/datasources/mysqlDS --connection-url=$DATABASE_CONNECTION_URL --driver-name=mysql --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=com.mysql.cj.jdbc.Driver --jta=true --use-java-context=true --exception-sorter-class-name=com.mysql.cj.jdbc.integration.jboss.ExtendedMysqlExceptionSorter

    reload --use-current-server-config=true
    ```

    **SQL Server**

    ```console
    module add --name=com.microsoft --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=sqlserver:add(driver-name=sqlserver,driver-module-name=com.microsoft,driver-class-name=com.microsoft.sqlserver.jdbc.SQLServerDriver,driver-datasource-class-name=com.microsoft.sqlserver.jdbc.SQLServerDataSource)

    data-source add --name=sqlDS --jndi-name=java:jboss/datasources/sqlDS --driver-name=sqlserver --connection-url=$DATABASE_CONNECTION_URL --validate-on-match=true --background-validation=false --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLValidConnectionChecker --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLExceptionSorter

    reload --use-current-server-config=true
    ```

    Den här filen körs av start skriptet som beskrivs i nästa steg. Den installerar JDBC-drivrutinen som en WildFly-modul, skapar motsvarande WildFly-datakälla och laddar om servern för att se till att ändringarna börjar gälla.

4. Skapa en fil med ett namn som *startup.sh* och Lägg till följande kod. Ersätt `<JBoss CLI script>` med namnet på filen som du skapade i föregående steg. Se till att inkludera den fullständiga sökvägen till platsen där du placerar filen i App Service-instansen, till exempel */Home/DataSource-commands.CLI*.

    ```bash
    #!/usr/bin/env bash
    /opt/jboss/wildfly/bin/jboss-cli.sh -c --file=<JBoss CLI script>
    ```

5. Använd FTP för att ladda upp filen JDBC. jar, modulens XML-fil, JBoss CLI-skriptet och start skriptet till App Service-instansen. Lägg de här filerna på den plats som du angav i föregående steg, till exempel */Home*. Mer information om FTP finns i [distribuera din app för att Azure App Service använda FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp).

6. Använd Azure CLI för att lägga till inställningar till App Service som innehåller information om databas anslutningen. Ersätt `<resource group>` och `<webapp name>` med de värden som din App Service använder. Ersätt `<database server name>`, `<database name>`, `<admin name>`och `<admin password>` med information om databas anslutningen. Du kan hämta App Service-och databas information från Azure Portal.

    **PostgreSQL:**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:postgresql://<database server name>:5432/<database name>?ssl=true \
            DATABASE_SERVER_ADMIN_FULL_NAME=<admin name> \
            DATABASE_SERVER_ADMIN_PASSWORD=<admin password>
    ```

    **MySQL:**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:mysql://<database server name>:3306/<database name>?ssl=true\&useLegacyDatetimeCode=false\&serverTimezone=GMT \
            DATABASE_SERVER_ADMIN_FULL_NAME=<admin name> \
            DATABASE_SERVER_ADMIN_PASSWORD=<admin password>
    ```

    **SQL Server:**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:sqlserver://<database server name>:1433;database=<database name>;user=<admin name>;password=<admin password>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
    ```

    DATABASE_CONNECTION_URL värden skiljer sig åt för varje databas server och skiljer sig från värdena på Azure Portal. URL-formaten som visas här (och i kodfragmenten ovan) krävs för användning av WildFly:

    * **Postgresql:** `jdbc:postgresql://<database server name>:5432/<database name>?ssl=true`
    * **Mysql:** `jdbc:mysql://<database server name>:3306/<database name>?ssl=true\&useLegacyDatetimeCode=false\&serverTimezone=GMT`
    * **SQL Server:** `jdbc:sqlserver://<database server name>:1433;database=<database name>;user=<admin name>;password=<admin password>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;`

7. I Azure Portal navigerar du till App Service och hittar sidan **konfiguration** > **allmänna inställningar** . Ange **Start skript** fältet till namnet och platsen för start skriptet, till exempel */Home/startup.sh*.

Nästa gången App Service startas om körs start skriptet och de nödvändiga konfigurations stegen utförs. För att testa att den här konfigurationen sker korrekt kan du komma åt App Service med SSH och sedan köra start skriptet själv från bash-prompten. Du kan också granska App Service loggar. Mer information om de här alternativen finns i [Logga och Felsöka appar](#logging-and-debugging-apps).

Sedan måste du uppdatera WildFly-konfigurationen för appen och distribuera den igen. Använd följande steg:

1. Öppna filen *src/main/Resources/meta-inf/persisted. XML* för din app och hitta `<jta-data-source>`-elementet. Ersätt innehållet på det sätt som visas här:

    **PostgreSQL**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

    **MySQL**

    ```xml
    <jta-data-source>java:jboss/datasources/mysqlDS</jta-data-source>
    ```

    **SQL Server**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

2. Återskapa och distribuera om din app med hjälp av följande kommando i bash-prompten:

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

3. Starta om App Service-instansen genom att trycka på knappen **starta om** i **översikts** avsnittet i Azure Portal eller med hjälp av Azure CLI.

Din App Service-instans har nu kon figurer ATS för att få åtkomst till din databas.

Mer information om hur du konfigurerar databas anslutning med WildFly finns i [postgresql](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7), [MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource)eller [SQL Server](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898).

### <a name="use-service-bus-as-a-message-broker"></a>Använd Service Bus som en meddelande koordinator

Du kan konfigurera WildFly och dina meddelande drivna bönor för att använda [Azure Service Bus](/azure/service-bus-messaging) som meddelande koordinator. Efter konfigurationen kan du skicka och ta emot meddelanden med [Apache qpid](https://qpid.apache.org) som JMS-klient (Java Message Service). Det finns några steg för att konfigurera ett JMS Resource Adapter (JMS RA) som gör det möjligt för Enterprise Java-bönor (EJBs) att konfigurera en fjärran sluten JMS anslutnings fabrik och kö. Den här fjärrinstallationen pekar på Azure Service Bus och använder Apache qpid JMS-providern för AMQP-protokollet.

Följande steg beskriver den konfiguration och kod som krävs. De här stegen förutsätter att du har skapat en App Service-instans för att vara värd för dina bönor, ett Service Bus namn område, en kö och ett ämne med en prenumeration. Information om hur du skapar dessa resurser finns i:

- [Snabb start: skapa en Java-app på Azure App Service på Linux](/azure/app-service/containers/quickstart-java)
- [Snabb start: Använd Azure CLI för att skapa en Service Bus kö](/azure/service-bus-messaging/service-bus-quickstart-cli)
- [Snabb start: Använd Azure Portal för att skapa ett Service Bus ämne och prenumerationer på ämnet](/azure/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal)

1. Öppna en bash-Terminal och Använd följande kommandon för att spara din Azure Resource-information i miljövariabler. Ersätt plats hållarna (inklusive vinkelparenteser) med de angivna värdena.

    | Variabel            | Värde                                                                      |
    |---------------------|----------------------------------------------------------------------------|
    | RESOURCEGROUP_NAME  | Namnet på den resurs grupp som innehåller App Service-instansen.       |
    | WEBAPP_NAME         | Namnet på App Service-instansen.                                     |
    | REGION              | Namnet på den region där din app finns.                           |
    | DEFAULT_SBNAMESPACE | Namnet på din Service Bus namn område.                                    |
    | SB_SAS_POLICY       | Namnet på principen för signatur för delad åtkomst (SAS) för ditt namn område.   |
    | SB_SAS_KEY          | Den primära eller sekundära nyckeln för köns SAS-princip.                  |
    | SB_QUEUE            | Namnet på din Service Bus-kö.                                        |
    | SB_TOPIC            | Namnet på Service Bus ämnet.                                        |
    | SB_SUBSCRIPTION     | Namnet på prenumerationen på ditt ämne.                                |

    ```bash
    RESOURCEGROUP_NAME=<resource group>
    WEBAPP_NAME=<web app>
    WEBAPP_PLAN_NAME=${WEBAPP_NAME}-appservice-plan
    REGION=<region>
    DEFAULT_SBNAMESPACE=<namespace>
    SB_SAS_POLICY=<SAS policy>
    SB_SAS_KEY=<SAS key>
    SB_QUEUE=<queue>
    SB_TOPIC=<topic>
    SB_SUBSCRIPTION=<subscription>
    PROVIDER_URL=amqps://${DEFAULT_SBNAMESPACE}.servicebus.windows.net?amqp.idleTimeout=120000
    ```

    Du hittar den här informationen i Azure Portal. För SAS-principen och-nyckeln, se till att använda värdena för namn området så att din app kan komma åt både din kö och din ämnes prenumeration. Du hittar de här värdena på Azure Portal genom att navigera till din namn områdes resurs, välja **principer för delad åtkomst**och sedan välja **RootManageSharedAccessKey** -principen.

2. Ladda ned [Apache QPID JMS-providern](https://qpid.apache.org/components/jms/index.html). Leta upp. jar-filerna i *biblioteks* *-och lib-/valfria* kataloger.

3. Skapa en fil med namnet *module. XML* och Lägg till följande markering. Ersätt varje instans av `<version>` placeholder (inklusive vinkelparenteser) med rätt version för varje jar-fil så att fil namnen matchar filerna som du extraherade i steg 1.

    ```xml
    <module xmlns="urn:jboss:module:1.1" name="org.jboss.genericjms.provider">
        <resources>
            <resource-root path="proton-j-<version>.jar"/>
            <resource-root path="qpid-jms-client-<version>.jar"/>
            <resource-root path="slf4j-log4j12-<version>.jar"/>
            <resource-root path="slf4j-api-<version>.jar"/>
            <resource-root path="log4j-<version>.jar"/>
            <resource-root path="netty-buffer-<version>.jar" />
            <resource-root path="netty-codec-<version>.jar" />
            <resource-root path="netty-codec-http-<version>.jar" />
            <resource-root path="netty-common-<version>.jar" />
            <resource-root path="netty-handler-<version>.jar" />
            <resource-root path="netty-resolver-<version>.jar" />
            <resource-root path="netty-transport-<version>.jar" />
            <resource-root path="netty-transport-native-epoll-<version>-linux-x86_64.jar" />
            <resource-root path="netty-transport-native-kqueue-<version>-osx-x86_64.jar" />
            <resource-root path="netty-transport-native-unix-common-<version>.jar" />
            <resource-root path="qpid-jms-discovery-<version>jar" />
        </resources>
        <dependencies>
            <module name="javax.api"/>
            <module name="javax.jms.api"/>
        </dependencies>
    </module>
    ```

4. Skapa en fil med namnet *startup.sh* och Lägg till följande kod.

    ```bash
    echo "Generating jndi.properties file in /home/site/deployments/tools directory"
    echo "connectionfactory.mymdbconnection=amqps://${DEFAULT_SBNAMESPACE}.servicebus.windows.net?amqp.idleTimeout=120000&jms.username=${SB_SAS_POLICY}&jms.password=${SB_SAS_KEY}" > /home/site/deployments/tools/jndi.properties
    echo "queue.mymdbqueue=${SB_QUEUE}" >> /home/site/deployments/tools/jndi.properties
    echo "topic.mymdbtopic=${SB_TOPIC}" >> /home/site/deployments/tools/jndi.properties
    echo "queue.mymdbsubscription=${SB_TOPIC}/Subscriptions/${SB_SUBSCRIPTION}" >> /home/site/deployments/tools/jndi.properties
    echo "====== contents of /home/site/deployments/tools/jndi.properties ======"
    cat /home/site/deployments/tools/jndi.properties
    echo "====== EOF /home/site/deployments/tools/jndi.properties ======"
    echo "Generating commands.cli file for /home/site/deployments/tools directory"
    echo "# Start batching commands" > /home/site/deployments/tools/commands.cli
    echo "batch" >> /home/site/deployments/tools/commands.cli
    echo "# Configure the ee subsystem to enable MDB annotation property substitution" >> /home/site/deployments/tools/commands.cli
    echo "/subsystem=ee:write-attribute(name=annotation-property-replacement,value=true)" >> /home/site/deployments/tools/commands.cli
    echo "# Define system properties to be used in the substititution" >> /home/site/deployments/tools/commands.cli
    echo "/system-property=property.mymdb.queue:add(value=java:global/remoteJMS/mymdbqueue})" >> /home/site/deployments/tools/commands.cli
    echo "/system-property=property.mymdb.topic:add(value=java:global/remoteJMS/mymdbsubscription)" >> /home/site/deployments/tools/commands.cli
    echo "/system-property=property.connection.factory:add(value=java:global/remoteJMS/mymdbconnection)" >> /home/site/deployments/tools/commands.cli
    echo "/subsystem=ee:list-add(name=global-modules, value={\"name\" => \"org.jboss.genericjms.provider\", \"slot\" =>\"main\"}" >> /home/site/deployments/tools/commands.cli
    echo "/subsystem=naming/binding=\"java:global/remoteJMS\":add(binding-type=external-context,module=org.jboss.genericjms.provider,class=javax.naming.InitialContext,environment=[java.naming.factory.initial=org.apache.qpid.jms.jndi.JmsInitialContextFactory,org.jboss.as.naming.lookup.by.string=true,java.naming.provider.url=/home/site/deployments/tools/jndi.properties])" >> /home/site/deployments/tools/commands.cli
    echo "/subsystem=resource-adapters/resource-adapter=generic-ra:add(module=org.jboss.genericjms,transaction-support=XATransaction)" >> /home/site/deployments/tools/commands.cli
    echo "/subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd:add(class-name=org.jboss.resource.adapter.jms.JmsManagedConnectionFactory, jndi-name=java:/jms/mymdbconnection)" >> /home/site/deployments/tools/commands.cli
    echo "/subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd/config-properties=ConnectionFactory:add(value=mymdbconnection)" >> /home/site/deployments/tools/commands.cli
    echo "/subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd/config-properties=JndiParameters:add(value=\"java.naming.factory.initial=org.apache.qpid.jms.jndi.JmsInitialContextFactory;java.naming.provider.url=/home/site/deployments/tools/jndi.properties\")" >> /home/site/deployments/tools/commands.cli
    echo "/subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd:write-attribute(name=security-application,value=true)" >> /home/site/deployments/tools/commands.cli
    echo "/subsystem=ejb3:write-attribute(name=default-resource-adapter-name, value=generic-ra)" >> /home/site/deployments/tools/commands.cli
    echo "# Run the batch commands" >> /home/site/deployments/tools/commands.cli
    echo "run-batch" >> /home/site/deployments/tools/commands.cli
    echo "reload" >> /home/site/deployments/tools/commands.cli
    echo "====== contents of /home/site/deployments/tools/commands.cli ======"
    cat /home/site/deployments/tools/commands.cli
    echo "======= EOF /home/site/deployments/tools/commands.cli ========"
    mkdir /opt/jboss/wildfly/modules/system/layers/base/org/jboss/genericjms/provider
    mkdir /opt/jboss/wildfly/modules/system/layers/base/org/jboss/genericjms/provider/main
    cp  /home/site/deployments/tools/*.jar /opt/jboss/wildfly/modules/system/layers/base/org/jboss/genericjms/provider/main/
    cp /home/site/deployments/tools/module.xml /opt/jboss/wildfly/modules/system/layers/base/org/jboss/genericjms/provider/main/
    cp /home/site/deployments/tools/jndi.properties /opt/jboss/wildfly/standalone/configuration/
    /opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/home/site/deployments/tools/commands.cli
    echo "Startup Run done"
    ```

    App Service-instansen kör skriptet varje gång det startar, vilket ger ytterligare konfiguration som krävs av WildFly. Det här skriptet kopierar dina program beroenden till de platser som krävs. Den genererar också *JNDI. Properties* och *commands. CLI* -filer, som använder de miljövariabler som visas i steg 1. Dessa värden skickas också till din App Service-instans i ett senare steg.

    Filen *commands. CLI* är ett [WildFly CLI](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface) -skript som startas av start skriptet. Kommandona i den här filen konfigurerar JMS och JNDI, vilket gör att filen *JNDI. Properties* används. Dessa kommandon skapar en anslutning mellan din app och din Service Bus kö eller ämne.

5. Använd FTP för att överföra. jar-filerna, filen *module. XML* och filen *startup.sh* till din app service-instans. Lägg *startup.sh* i din */Home* -katalog och Lägg till de andra filerna i katalogen */Home/site/Deployments/tools* . Var noga med att ladda upp varje. jar-fil som anges i *modulens XML-* fil för att uppnå en transitiv stängning av beroenden. Mer information om FTP finns i [distribuera din app för att Azure App Service använda FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp).

6. Uppdatera MessageListener-implementeringen för att lägga till följande `import`-satser:

    ```java
    import javax.ejb.ActivationConfigProperty;
    import javax.ejb.MessageDriven;
    import javax.ejb.TransactionAttribute;
    import javax.ejb.TransactionAttributeType;
    import javax.ejb.TransactionManagement;
    import javax.ejb.TransactionManagementType;
    import javax.jms.JMSException;
    import javax.jms.Message;
    import javax.jms.MessageListener;
    import javax.jms.TextMessage;
    ```

7. Uppdatera sedan lyssnar klass anteckningarna så att de överensstämmer med följande exempel. Den här klassen innehåller en exempel implementering som loggar kvittot på meddelanden.

    ```java
    @TransactionManagement(TransactionManagementType.BEAN)
    @TransactionAttribute(TransactionAttributeType.NOT_SUPPORTED)
    @MessageDriven(name = "MyQueueListener", activationConfig = {
            @ActivationConfigProperty(propertyName = "connectionFactory", propertyValue = "${property.connection.factory}"),
            @ActivationConfigProperty(propertyName = "destinationLookup", propertyValue = "${property.mymdb.queue}"),
            @ActivationConfigProperty(propertyName = "destinationType", propertyValue = "javax.jms.Queue"),
            @ActivationConfigProperty(propertyName = "acknowledgeMode", propertyValue = "Auto-acknowledge") })
    public class MyQueueListener implements MessageListener {

        private static final Logger LOGGER = Logger.getLogger(TopicListener.class.toString());

        public void onMessage(Message rcvMessage) {
            TextMessage msg = null;
            try {
                if (rcvMessage instanceof TextMessage) {
                    msg = (TextMessage) rcvMessage;
                    LOGGER.info("Received Message from topic: " + msg.getText());
                } else {
                    LOGGER.warning("Message of wrong type: " + rcvMessage.getClass().getName());
                }
            } catch (JMSException e) {
                LOGGER.warning("Exception on message : " + e.getMessage());
                throw new RuntimeException(e);
            }
        }
    }
    ```

    Värdena för `connectionFactory` och `destinationLookup` refererar till system egenskaps värden för WildFly som kon figurer ATS av *startup.sh* -skriptet. `destinationType`-värdet är `javax.jms.Queue`, vilket indikerar att du ansluter till en Service Bus-Queue-instans. Det här värdet ska `javax.jms.Topic` när du ansluter till ett Service Bus-ämne, som du ser här:

    ```java
    @TransactionManagement(TransactionManagementType.BEAN)
    @TransactionAttribute(TransactionAttributeType.NOT_SUPPORTED)
    @MessageDriven(name = "MyTopicListener", activationConfig = {
            @ActivationConfigProperty(propertyName = "connectionFactory", propertyValue = "${property.connection.factory}"),
            @ActivationConfigProperty(propertyName = "destinationLookup", propertyValue = "${property.mymdb.topic}"),
            @ActivationConfigProperty(propertyName = "destinationType", propertyValue = "javax.jms.Topic"),
            @ActivationConfigProperty(propertyName = "acknowledgeMode", propertyValue = "Auto-acknowledge") })
        public class MyTopicListener implements MessageListener {
        // ...
    }
    ```

8. Uppdatera `dependencies`-avsnittet i *Pom. XML-* filen för att lägga till följande beroenden:

    ```xml
    <dependencies>
        <dependency>
            <groupId>org.apache.qpid</groupId>
            <artifactId>qpid-jms-client</artifactId>
            <version>0.40.0</version>
        </dependency>
        <dependency>
            <groupId>org.apache.qpid</groupId>
            <artifactId>proton-j</artifactId>
            <version>0.31.0</version>
        </dependency>
        <dependency>
            <groupId>javax.enterprise</groupId>
            <artifactId>cdi-api</artifactId>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.jboss.spec.javax.ejb</groupId>
            <artifactId>jboss-ejb-api_3.2_spec</artifactId>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.jboss.spec.javax.jms</groupId>
            <artifactId>jboss-jms-api_2.0_spec</artifactId>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.jboss.spec.javax.servlet</groupId>
            <artifactId>jboss-servlet-api_4.0_spec</artifactId>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.jboss.spec.javax.annotation</groupId>
            <artifactId>jboss-annotations-api_1.3_spec</artifactId>
            <scope>provided</scope>
        </dependency>
    </dependencies>
    ```

9. Uppdatera `azure-webapp-maven-plugin`-konfigurationen i din *Pom. XML-* fil för att referera till din Service Bus konto information. Om det behövs ändrar du `1.7.0` till den aktuella versionen av [maven-plugin-programmet för Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme).

    ```xml
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.7.0</version>
        <configuration>

            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appServicePlanName>${WEBAPP_PLAN_NAME}</appServicePlanName>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>

            <!-- Java Runtime Stack for Web App on Linux-->
            <linuxRuntime>wildfly 14-jre8</linuxRuntime>

            <appSettings>
                <property>
                    <name>DEFAULT_SBNAMESPACE</name>
                    <value>${DEFAULT_SBNAMESPACE}</value>
                </property>
                <property>
                    <name>SB_SAS_POLICY</name>
                    <value>${SB_SAS_POLICY}</value>
                </property>
                <property>
                    <name>SB_SAS_KEY</name>
                    <value>${SB_SAS_KEY}</value>
                </property>
                <property>
                    <name>PROVIDER_URL</name>
                    <value>${PROVIDER_URL}</value>
                </property>
                <property>
                    <name>SB_QUEUE</name>
                    <value>${SB_QUEUE}</value>
                </property>
                <property>
                    <name>SB_TOPIC</name>
                    <value>${SB_TOPIC}</value>
                </property>
                <property>
                    <name>SB_SUBSCRIPTION</name>
                    <value>${SB_SUBSCRIPTION}</value>
                </property>
            </appSettings>
        </configuration>
    </plugin>
    ```

    De här inställningarna konfigurerar App Service-instansen så att den har samma miljövariabler som du anger lokalt. Den använder miljövariabler för att se till att din konto information är av källfilerna.

10. Återskapa och distribuera om din app.

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

Dina meddelande drivna bönor har nu kon figurer ATS för att använda Service Bus som meddelande funktion.

Nästa gången App Service startas om körs start skriptet och de nödvändiga konfigurations stegen utförs. För att testa att den här konfigurationen sker korrekt kan du komma åt App Service med SSH och sedan köra start skriptet själv från bash-prompten. Du kan också granska App Service loggar. Mer information om de här alternativen finns i [Logga och Felsöka appar](#logging-and-debugging-apps).

Ett exempel som du kan använda för att testa de här anvisningarna finns i avsnittet [migrera-Java-EE-app-till-Azure-2](https://github.com/Azure-Samples/migrate-java-ee-app-to-azure-2) lagrings platsen på GitHub och leta efter `helloworld-mdb-propertysubstitution` exemplet.

## <a name="use-redis-as-a-session-cache-with-tomcat"></a>Använda Redis som en session-cache med Tomcat

Du kan konfigurera Tomcat till att använda ett externt sessionsobjekt, till exempel [Azure cache för Redis](/azure/azure-cache-for-redis/). På så sätt kan du bevara sessionstillstånd för användare (till exempel shopping vagns data) när en användare överförs till en annan instans av appen, till exempel när automatisk skalning, omstart eller redundans inträffar.

Om du vill använda Tomcat med Redis måste du konfigurera appen så att den använder en [PersistentManager](https://tomcat.apache.org/tomcat-8.5-doc/config/manager.html) -implementering. I följande steg förklaras den här processen med hjälp av [pivoting session Manager: Redis-Store](https://github.com/pivotalsoftware/session-managers/tree/master/redis-store) som ett exempel.

1. Öppna en bash-Terminal och Använd `<variable>=<value>` för att ange var och en av följande miljövariabler.

    | Variabel                 | Värde                                                                      |
    |--------------------------|----------------------------------------------------------------------------|
    | RESOURCEGROUP_NAME       | Namnet på den resurs grupp som innehåller App Service-instansen.       |
    | WEBAPP_NAME              | Namnet på App Service-instansen.                                     |
    | WEBAPP_PLAN_NAME         | Namnet på din App Service plan.                                         |
    | REGION                   | Namnet på den region där din app finns.                           |
    | REDIS_CACHE_NAME         | Namnet på Azure-cachen för Redis-instansen.                           |
    | REDIS_PORT               | SSL-porten som Redis-cachen lyssnar på.                             |
    | REDIS_PASSWORD           | Den primära åtkomst nyckeln för din instans.                                  |
    | REDIS_SESSION_KEY_PREFIX | Ett värde som du anger för att identifiera sessionsnycklar som kommer från din app. |

    ```bash
    RESOURCEGROUP_NAME=<resource group>
    WEBAPP_NAME=<web app>
    WEBAPP_PLAN_NAME=<App Service plan>
    REGION=<region>
    REDIS_CACHE_NAME=<cache>
    REDIS_PORT=<port>
    REDIS_PASSWORD=<access key>
    REDIS_SESSION_KEY_PREFIX=<prefix>
    ```

    Du hittar information om namn, port och åtkomst nycklar på Azure Portal genom att titta i avsnitten **Egenskaper** eller **åtkomst nycklar** i tjänst instansen.

2. Skapa eller uppdatera appens *src/main/webapp/meta-inf/context. XML-* fil med följande innehåll:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <Context path="">
        <!-- Specify Redis Store -->
        <Valve className="com.gopivotal.manager.SessionFlushValve" />
        <Manager className="org.apache.catalina.session.PersistentManager">
            <Store className="com.gopivotal.manager.redis.RedisStore"
                   connectionPoolSize="20"
                   host="${REDIS_CACHE_NAME}.redis.cache.windows.net"
                   port="${REDIS_PORT}"
                   password="${REDIS_PASSWORD}"
                   sessionKeyPrefix="${REDIS_SESSION_KEY_PREFIX}"
                   timeout="2000"
            />
        </Manager>
    </Context>
    ```

    Den här filen anger och konfigurerar sessionshanteraren för din app. Den använder de miljövariabler som du angav i föregående steg för att hålla din konto information ut från källfilerna.

3. Använd FTP för att ladda upp sessionshanteraren i App Service-instansen och placera den i */Home/Tomcat/lib* -katalogen. Mer information finns i [distribuera din app för att Azure App Service att använda FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp).

4. Inaktivera [cookien för session-tillhörighet](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) för App Service-instansen. Du kan göra detta från Azure Portal genom att **navigera till din**app och sedan ställa in **konfigurations > allmänna inställningar > arr-tillhörighet** . Alternativt kan du använda följande kommando:

    ```azurecli
    az webapp update -g <resource group> -n <webapp name> --client-affinity-enabled false
    ```

    Som standard använder App Service session tillhörighets-cookies för att se till att klient begär Anden med befintliga sessioner dirigeras till samma instans av programmet. Detta standard beteende kräver ingen konfiguration, men den kan inte bevara sessionstillstånd när din App-instans startas om eller när trafik dirigeras om till en annan instans. När du [inaktiverar den befintliga](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) konfigurationen för arr-tillhörighet för att stänga av sessionen cookie-baserad routning, tillåter du att det konfigurerade sessionstillståndet kan köras utan störningar.

5. Navigera till avsnittet **Egenskaper** i App Service-instansen och hitta **ytterligare utgående IP-adresser**. Detta representerar alla möjliga utgående IP-adresser för din app. Kopiera dessa för användning i nästa steg.

6. För varje IP-adress skapar du en brand Väggs regel i Azure-cachen för Redis-instansen. Du kan göra detta på Azure Portal från **brand Väggs** avsnittet i Redis-instansen. Ange ett unikt namn för varje regel och ange **Start-IP-adress** och **slut-IP-adress** värden till samma IP-adress.

7. Navigera till avsnittet **Avancerade inställningar** i Redis-instansen och ange **Tillåt endast åtkomst via SSL** till **Nej**. Detta gör att App Service-instansen kan kommunicera med din Redis-cache via Azure-infrastrukturen.

8. Uppdatera `azure-webapp-maven-plugin`-konfigurationen i appens *Pom. XML-* fil för att referera till Redis-kontots information. I den här filen används de miljövariabler som du angav tidigare för att behålla din konto information från källfilerna.

    Om det behövs ändrar du `1.7.0` till den aktuella versionen av [maven-plugin-programmet för Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme).

    ```xml
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.7.0</version>
        <configuration>

            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appServicePlanName>${WEBAPP_PLAN_NAME}-${REGION}</appServicePlanName>
            <appName>${WEBAPP_NAME}-${REGION}</appName>
            <region>${REGION}</region>
            <linuxRuntime>tomcat 9.0-jre8</linuxRuntime>

            <appSettings>
                <property>
                    <name>REDIS_CACHE_NAME</name>
                    <value>${REDIS_CACHE_NAME}</value>
                </property>
                <property>
                    <name>REDIS_PORT</name>
                    <value>${REDIS_PORT}</value>
                </property>
                <property>
                    <name>REDIS_PASSWORD</name>
                    <value>${REDIS_PASSWORD}</value>
                </property>
                <property>
                    <name>REDIS_SESSION_KEY_PREFIX</name>
                    <value>${REDIS_SESSION_KEY_PREFIX}</value>
                </property>
                <property>
                    <name>JAVA_OPTS</name>
                    <value>-Xms2048m -Xmx2048m -DREDIS_CACHE_NAME=${REDIS_CACHE_NAME} -DREDIS_PORT=${REDIS_PORT} -DREDIS_PASSWORD=${REDIS_PASSWORD} IS_SESSION_KEY_PREFIX=${REDIS_SESSION_KEY_PREFIX}</value>
                </property>

            </appSettings>

        </configuration>
    </plugin>
    ```

9. Återskapa och distribuera om din app.

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

Din app kommer nu att använda Redis-cache för hantering av sessioner.

Ett exempel som du kan använda för att testa dessa instruktioner finns i avsnittet om [skalning-tillstånds känsligt-Java-Web-App-on-Azure](https://github.com/Azure-Samples/scaling-stateful-java-web-app-on-azure) på GitHub.

## <a name="docker-containers"></a>Docker-containrar

Om du vill använda Azure-Zulu-JDK som stöds i dina behållare, se till att hämta och använda de färdiga avbildningarna enligt dokumentationen från [Azul-Zulu Enterprise för Azures hämtnings sida](https://www.azul.com/downloads/azure-only/zulu/) eller Använd `Dockerfile` exempel från [Microsoft Java GitHub lagrings platsen](https://github.com/Microsoft/java/tree/master/docker).

## <a name="statement-of-support"></a>Support specifikation

### <a name="runtime-availability"></a>Tillgänglighet för körning

App Service för Linux stöder två körningar för hanterad värd för Java-webb program:

- [Tomcat servlet-behållaren](https://tomcat.apache.org/) för att köra program som paketerade som Web Archive (War)-filer. Versioner som stöds är 8,5 och 9,0.
- Java SE körnings miljö för att köra program som paketeras som filer i Java Archive (JAR). Versioner som stöds är Java 8 och 11.

### <a name="jdk-versions-and-maintenance"></a>JDK-versioner och underhåll

Azul Zulu Enterprise builds of OpenJDK är en kostnads färdig distribution med flera plattformar av OpenJDK för Azure och Azure Stack som backas upp av Microsoft-och Azul-system. De innehåller alla komponenter för att skapa och köra Java SE-program. Du kan installera JDK från [Java JDK-installationen](https://aka.ms/azure-jdks).

JDKs som stöds korrigeras automatiskt en gång i kvartalet i januari, april, juli och oktober varje år.

### <a name="security-updates"></a>Säkerhetsuppdateringar

Korrigeringar och korrigeringar för viktiga säkerhets problem kommer att släppas så snart de blir tillgängliga från Azul system. En "större" sårbarhet definieras av en baspoäng på 9,0 eller högre på [NIST vanliga sårbarhets bedömnings system, version 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Utfasning och pensionering

Om en Java-körning som stöds dras tillbaka kommer Azure-utvecklare som använder den berörda körningen att få ett meddelande om förvarning minst sex månader innan körningen dras tillbaka.

## <a name="next-steps"></a>Nästa steg

Besök [Azure för Java Developer](/java/azure/) Center för att hitta Azures snabb starter, självstudier och Java-referens dokumentation.

Allmänna frågor om hur du använder App Service för Linux som inte är särskilt för Java-utveckling besvaras i [vanliga frågor och svar om App Service Linux](app-service-linux-faq.md).
