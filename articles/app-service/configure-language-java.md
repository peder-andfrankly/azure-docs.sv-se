---
title: Konfigurera Windows Java-appar
description: Lär dig hur du konfigurerar Java-appar så att de körs på virtuella Windows-instanser i Azure App Service. Den här artikeln visar de vanligaste konfigurations åtgärderna.
keywords: Azure App Service, Web App, Windows, oss, Java
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: dec5d1c7c7664bf72d92e5aca4333ba64db26d02
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671914"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Konfigurera en Windows java-app för Azure App Service

Med Azure App Service kan Java-utvecklare snabbt bygga, distribuera och skala sina Tomcat-webbprogram på en fullständigt hanterad Windows-baserad tjänst. Distribuera program med maven-plugin-program från kommando raden eller i redigerings program som IntelliJ, Sol förmörkelse eller Visual Studio Code.

Den här guiden innehåller viktiga begrepp och instruktioner för Java-utvecklare som använder i App Service. Om du aldrig har använt Azure App Service bör du läsa igenom Java- [snabb](app-service-web-get-started-java.md) starten först. Allmänna frågor om att använda App Service som inte är särskilt för Java-utveckling besvaras i [vanliga frågor och svar om App Service Windows](faq-configuration-and-management.md).

## <a name="deploying-your-app"></a>Distribuera din app

Du kan använda [maven-plugin-programmet för Azure App Service för](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) att distribuera dina War-filer. Distribution med populära IDE: er stöds också med [Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) eller [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

Annars beror distributions metoden på din Arkiv typ:

- Om du vill distribuera. War-filer till Tomcat använder du `/api/wardeploy/` slut punkten för att publicera Arkiv filen. Mer information om det här API: et finns i [den här dokumentationen](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).

Distribuera inte din. War med FTP. FTP-verktyget är utformat för att ladda upp start skript, beroenden eller andra runtime-filer. Det är inte det bästa valet för att distribuera webbappar.

## <a name="logging-and-debugging-apps"></a>Logga och Felsök appar

Prestanda rapporter, trafik visualiseringar och hälso checkups är tillgängliga för varje app via Azure Portal. Mer information finns i [Översikt över Azure App Service-diagnostik](overview-diagnostics.md).

### <a name="stream-diagnostic-logs"></a>Strömma diagnostikloggar

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

Mer information finns i [Stream-loggar i Cloud Shell](troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Loggning av app

Aktivera [program loggning](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) via Azure Portal eller [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) för att konfigurera App Service att skriva programmets standard-och standard konsol fel strömmar till det lokala fil systemet eller Azure-Blob Storage. Loggning till den lokala App Service fil Systems instansen är inaktive rad 12 timmar efter att den har kon figurer ATS Om du behöver kvarhållare kan du konfigurera programmet att skriva utdata till en Blob Storage-behållare. Du hittar dina program loggar för Java och Tomcat i katalogen */LogFiles/Application/* .

Om ditt program använder [logback](https://logback.qos.ch/) eller [log4j](https://logging.apache.org/log4j) för spårning kan du vidarebefordra spårningarna för granskning till Azure Application insikter med hjälp av konfigurations anvisningar för loggnings ramverk i [utforska Java trace-loggar i Application Insights](/azure/application-insights/app-insights-java-trace-logs).


## <a name="customization-and-tuning"></a>Anpassning och justering

Azure App Service stöder anpassning och anpassning av rutorna i Azure Portal och CLI. Läs följande artiklar för konfiguration av icke-Java-en webbapp:

- [Konfigurera appinställningar](configure-common.md#configure-app-settings)
- [Konfigurera en anpassad domän](app-service-web-tutorial-custom-domain.md)
- [Konfigurera SSL-bindningar](configure-ssl-bindings.md)
- [Lägg till en CDN](../cdn/cdn-add-to-web-app.md)
- [Konfigurera kudu-webbplatsen](https://github.com/projectkudu/kudu/wiki/Configurable-settings)

### <a name="set-java-runtime-options"></a>Ange Java Runtime-alternativ

Om du vill ange allokerat minne eller andra alternativ för JVM-körning skapar du en [app-inställning](configure-common.md#configure-app-settings) med namnet `JAVA_OPTS` med alternativen. App Service skickar den här inställningen som en miljö variabel till Java-körningsmiljön när den startas.

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

### <a name="pre-compile-jsp-files"></a>Kompilera JSP-filer i förväg

För att förbättra prestanda för Tomcat-program kan du kompilera JSP-filerna innan du distribuerar till App Service. Du kan använda [plugin-programmet maven](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) som tillhandahålls av Apache sling eller med hjälp av den här [Ant build-filen](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Säkra program

Java-program som körs i App Service har samma uppsättning [rekommenderade säkerhets metoder](/azure/security/security-paas-applications-using-app-services) som andra program.

### <a name="authenticate-users-easy-auth"></a>Autentisera användare (enkel autentisering)

Konfigurera app-autentisering i Azure Portal med alternativet **autentisering och auktorisering** . Därifrån kan du aktivera autentisering med Azure Active Directory eller sociala inloggningar som Facebook, Google eller GitHub. Azure Portal konfiguration fungerar bara när du konfigurerar en enda autentiseringsprovider. Mer information finns i [Konfigurera din app service app för att använda Azure Active Directory inloggning](configure-authentication-provider-aad.md) och relaterade artiklar för andra identitets leverantörer. Om du behöver aktivera flera inloggnings leverantörer följer du anvisningarna i artikeln [anpassa App Service-autentisering](app-service-authentication-how-to.md) .

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

### <a name="configure-tlsssl"></a>Konfigurera TLS/SSL

Följ anvisningarna i avsnittet [skydda ett anpassat DNS-namn med en SSL-bindning i Azure App Service](configure-ssl-bindings.md) för att ladda upp ett befintligt SSL-certifikat och bind det till ditt programs domän namn. Som standard tillåter programmet fortfarande HTTP-anslutningar – Följ de specifika stegen i självstudien för att genomdriva SSL och TLS.

### <a name="use-keyvault-references"></a>Använda nyckel Valvs referenser

Azure-nyckels [valvet](../key-vault/key-vault-overview.md) tillhandahåller centraliserad hemlig hantering med åtkomst principer och gransknings historik. Du kan lagra hemligheter (till exempel lösen ord eller anslutnings strängar) i ett nyckel valv och komma åt dessa hemligheter i ditt program via miljövariabler.

Börja med att följa anvisningarna för [att ge appen åtkomst till Key Vault](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) och [skapa en nyckel valv referens till din hemlighet i en program inställning](app-service-key-vault-references.md#reference-syntax). Du kan kontrol lera att referensen matchar hemligheten genom att skriva ut miljövariabeln och fjärrans luta till App Service terminalen.

Om du vill mata in de här hemligheterna i din våren-eller Tomcat-konfigurationsfil använder du syntaxen för miljö variabel insprutning (`${MY_ENV_VAR}`). För våren-konfigurationsfiler läser du den här dokumentationen om de [externa konfigurationerna](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).


## <a name="configure-apm-platforms"></a>Konfigurera APM-plattformar

Det här avsnittet visar hur du ansluter Java-program som distribuerats på Azure App Service i Linux med NewRelic-och AppDynamics-plattformarna för program prestanda övervakning (APM).

### <a name="configure-new-relic"></a>Konfigurera nya Relic

1. Skapa ett nytt Relic-konto på [NewRelic.com](https://newrelic.com/signup)
2. Hämta Java-agenten från NewRelic kommer den att ha ett fil namn som liknar *newrelic-Java-x. x. x. zip*.
3. Kopiera licens nyckeln, du behöver den för att konfigurera agenten senare.
4. Använd [kudu-konsolen](https://github.com/projectkudu/kudu/wiki/Kudu-console) för att skapa en ny katalog- */Home/site/wwwroot/APM*.
5. Överför de packade nya Relic Java agent-filerna till en katalog under */Home/site/wwwroot/APM*. Filerna för din agent ska vara i */Home/site/wwwroot/APM/newrelic*.
6. Ändra YAML-filen på */Home/site/wwwroot/APM/newrelic/newrelic.yml* och ersätt licens värdet för plats hållaren med din egen licens nyckel.
7. I Azure Portal bläddrar du till ditt program i App Service och skapar en ny program inställning.
    - Om din app använder **Java se**skapar du en miljö variabel med namnet `JAVA_OPTS` med värdet `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Om du använder **Tomcat**skapar du en miljö variabel med namnet `CATALINA_OPTS` med värdet `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.

### <a name="configure-appdynamics"></a>Konfigurera AppDynamics

1. Skapa ett AppDynamics-konto på [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Hämta Java-agenten från AppDynamics-webbplatsen kommer fil namnet att likna *AppServerAgent-x. x. x. XXXXX. zip*
3. Använd [kudu-konsolen](https://github.com/projectkudu/kudu/wiki/Kudu-console) för att skapa en ny katalog- */Home/site/wwwroot/APM*.
4. Överför Java-agentens filer till en katalog under */Home/site/wwwroot/APM*. Filerna för din agent ska vara i */Home/site/wwwroot/APM/AppDynamics*.
5. I Azure Portal bläddrar du till ditt program i App Service och skapar en ny program inställning.
    - Om du använder **Java se**skapar du en miljö variabel med namnet `JAVA_OPTS` med värdet `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` där `<app-name>` är App Service namn.
    - Om du använder **Tomcat**skapar du en miljö variabel med namnet `CATALINA_OPTS` med värdet `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` där `<app-name>` är App Service namn.

>  Om du redan har en miljö variabel för `JAVA_OPTS` eller `CATALINA_OPTS`lägger du till alternativet `-javaagent:/...` till slutet av det aktuella värdet.

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

#### <a name="finalize-configuration"></a>Slutför konfiguration

Slutligen kommer vi att placera driv rutins jar v7 i Tomcat-classpath och starta om App Service. Se till att JDBC-drivrutinsfiler är tillgängliga för Tomcat-classloader genom att placera dem i */Home/Tomcat/lib* -katalogen. (Skapa den här katalogen om den inte redan finns.) Utför följande steg för att överföra filerna till din App Service-instans:

1. I [Cloud Shell](https://shell.azure.com)installerar du webapp-tillägget:

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. Kör följande CLI-kommando för att skapa en SSH-tunnel från det lokala systemet till App Service:

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. Anslut till den lokala tunnel porten med din SFTP-klient och överför filerna till mappen */Home/Tomcat/lib* .

Alternativt kan du använda en FTP-klient för att ladda upp JDBC-drivrutinen. Följ de här [anvisningarna för att hämta dina FTP-autentiseringsuppgifter](deploy-configure-credentials.md).

## <a name="configuring-tomcat"></a>Konfigurera Tomcat

Om du vill redigera Tomcat `server.xml` eller andra konfigurationsfiler ska du först anteckna din tomcat-huvud version i portalen.

1. Hitta Tomcat-hemkatalogen för din version genom att köra kommandot `env`. Sök efter den miljö variabel som börjar med `AZURE_TOMCAT`och som matchar huvud versionen. `AZURE_TOMCAT85_HOME` till exempel pekar på Tomcat-katalogen för Tomcat 8,5.
1. När du har identifierat Tomcat-arbetskatalogen för din version kan du kopiera konfigurations katalogen till `D:\home`. Om `AZURE_TOMCAT85_HOME` till exempel hade värdet `D:\Program Files (x86)\apache-tomcat-8.5.37`, skulle den nya sökvägen till den kopierade katalogen vara `D:\home\apache-tomcat-8.5.37`.

Slutligen startar du om din App Service. Dina distributioner ska gå till `D:\home\site\wwwroot\webapps` precis som tidigare.

## <a name="java-runtime-statement-of-support"></a>Stöd för Java Runtime-sats

### <a name="jdk-versions-and-maintenance"></a>JDK-versioner och underhåll

Azures Java Development Kit (JDK) som stöds är [Zulu](https://www.azul.com/downloads/azure-only/zulu/) via [Azul system](https://www.azul.com/).

Huvud versions uppdateringar kommer att tillhandahållas via nya körnings alternativ i Azure App Service för Windows. Kunder uppdaterar till dessa nyare versioner av Java genom att konfigurera sina App Service-distributioner och ansvarar för att testa och se till att den viktiga uppdateringen uppfyller deras behov.

JDKs som stöds korrigeras automatiskt en gång i kvartalet i januari, april, juli och oktober varje år. Mer information om java i Azure finns i [det här support dokumentet](https://docs.microsoft.com/azure/java/jdk/).

### <a name="security-updates"></a>Säkerhetsuppdateringar

Korrigeringar och korrigeringar för viktiga säkerhets problem kommer att släppas så snart de blir tillgängliga från Azul system. En "större" sårbarhet definieras av en baspoäng på 9,0 eller högre på [NIST vanliga sårbarhets bedömnings system, version 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Utfasning och pensionering

Om en Java-körning som stöds dras tillbaka kommer Azure-utvecklare som använder den berörda körningen att få ett meddelande om förvarning minst sex månader innan körningen dras tillbaka.

### <a name="local-development"></a>Lokal utveckling

Utvecklare kan ladda ned Production Edition of Azul Zulu Enterprise JDK för lokal utveckling från [Azuls nedladdnings plats](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Utvecklings support

Produkt support för det [Azure-Azul Zulu-JDK som stöds](https://www.azul.com/downloads/azure-only/zulu/) är tillgängligt via Microsoft när du utvecklar för azure eller [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) med ett [kvalificerat support](https://azure.microsoft.com/support/plans/)avtal för Azure.

### <a name="runtime-support"></a>Stöd för körning

Utvecklare kan [öppna ett problem](/azure/azure-supportability/how-to-create-azure-support-request) med Azul Zulu-JDKs via Azure-support om de har ett [kvalificerat support](https://azure.microsoft.com/support/plans/)avtal.

## <a name="next-steps"></a>Nästa steg

Det här avsnittet innehåller Java Runtime-satsen för stöd för Azure App Service i Windows.

- Om du vill veta mer om att vara värd för webb program med Azure App Service se [Översikt över App Service](overview.md).
- Information om Java på Azure-utveckling finns i [Azure för java dev Center](https://docs.microsoft.com/java/azure/?view=azure-java-stable).
