---
title: Självstudie för att använda funktions flaggor i en våren Boot app-Azure App konfiguration | Microsoft Docs
description: I den här självstudien får du lära dig hur du implementerar funktions flaggor i våren Boot Apps.
services: azure-app-configuration
documentationcenter: ''
author: mrm9084
manager: zhenlwa
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: java
ms.topic: tutorial
ms.date: 09/26/2019
ms.author: mametcal
ms.custom: mvc
ms.openlocfilehash: 8c66e2995462701f7ddaefc3a2623c02fee883ef
ms.sourcegitcommit: 6013bacd83a4ac8a464de34ab3d1c976077425c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/30/2019
ms.locfileid: "71687173"
---
# <a name="tutorial-use-feature-flags-in-a-spring-boot-app"></a>Självstudie: använda funktions flaggor i en våren Boot-app

Biblioteken för våren Boot Core Feature Management innehåller stöd för att implementera funktions flaggor i ett våren Boot-program. Med de här biblioteken kan du lägga till funktions flaggor i koden.

Biblioteken för funktions hantering hanterar även livscykler i funktions flaggor i bakgrunden. Till exempel, biblioteks uppdaterings-och cache-flaggan eller garanterar att ett flagg tillstånd kan inte ändras under ett begär ande anrop. Dessutom erbjuder vår start bibliotek integreringar, inklusive åtgärder för MVC-kontrollant, vägar och mellanprogram.

Med [funktionen Lägg till funktions flaggor i snabb starten för en fjäder Boot-app](./quickstart-feature-flag-spring-boot.md) visas flera sätt att lägga till funktions flaggor i ett våren Boot-program. I den här självstudien beskrivs dessa metoder i detalj.

I den här självstudien får du lära dig hur man:

> [!div class="checklist"]
> * Lägg till funktions flaggor i huvud delar av ditt program för att kontrol lera funktions tillgänglighet.
> * Integrera med app-konfiguration när du använder den för att hantera funktions flaggor.

## <a name="set-up-feature-management"></a>Konfigurera funktions hantering

Funktionen våren Boot Feature Manager `FeatureManager` hämtar funktions flaggor från ramverkets inbyggda konfigurations system. Det innebär att du kan definiera programmets funktions flaggor genom att använda valfri konfigurations källa som har stöd för start, inklusive den lokala filen *bootstrap. yml* eller miljövariabler. `FeatureManager` förlitar sig på beroende inmatning. Du kan registrera funktions hanterings tjänsterna genom att använda standard konventioner:

```java
private FeatureManager featureManager;

public HelloController(FeatureManager featureManager) {
    this.featureManager = featureManager;
}
```

Vi rekommenderar att du behåller funktions flaggor utanför programmet och hanterar dem separat. Om du gör det kan du när som helst ändra flaggans tillstånd och ändringarna börjar gälla i programmet direkt. Med app Configuration får du en central plats för att ordna och kontrol lera alla funktions flaggor via ett dedikerat Portal gränssnitt. App-konfigurationen levererar också flaggorna till ditt program direkt via sina klient bibliotek för sin våren-start.

Det enklaste sättet att ansluta ditt våren Boot-program till app-konfigurationen är via konfigurationsprovidern:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
    <version>1.1.0.M4</version>
</dependency>
```

## <a name="feature-flag-declaration"></a>Deklaration för funktions flagga

Varje funktions flagga har två delar: ett namn och en lista med ett eller flera filter som används för att utvärdera om en funktions status är *aktiv* (det vill säga när värdet är `True`). Ett filter definierar ett användnings fall för när en funktion ska aktive ras.

När en funktions flagga har flera filter, så genomsöks filter listan i ordning tills ett av filtren bestämmer att funktionen ska aktive ras. Nu är funktions flaggan *på*och eventuella kvarvarande filter resultat hoppas över. Om inget filter anger att funktionen ska aktive ras, är funktions flaggan *avstängd*.

Funktions hanteraren stöder *Application. yml* som en konfigurations källa för funktions flaggor. I följande exempel visas hur du ställer in funktions flaggor i en YAML-fil:

```yml
feature-management:
  featureSet:
    features:
      FeatureA: true
      FeatureB: false
      FeatureC:
        EnabledFor:
          -
            name: Percentage
            parameters:
              value: 50
```

Per konvention används `feature-management` avsnittet i det här YML-dokumentet för inställningar för funktions flagga. I föregående exempel visas tre funktions flaggor med de filter som definierats i egenskapen `EnabledFor`:

* `FeatureA` är *på*.
* `FeatureB` är *avstängd*.
* `FeatureC` anger ett filter med namnet `Percentage` med en `Parameters`-egenskap. `Percentage` är ett konfigurerbart filter. I det här exemplet anger `Percentage` en sannolikhet på 50 procent för att `FeatureC` flagga ska vara *på*.

## <a name="feature-flag-checks"></a>Funktions flagga kontrollerar

Det grundläggande mönstret för funktions hantering är att först kontrol lera om en funktions flagga har angetts till *på*. I så fall kör funktions hanteraren de åtgärder som funktionen innehåller. Exempel:

```java
private FeatureManager featureManager;
...
if (featureManager.isEnabled("FeatureA"))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Beroendeinmatning

I våren boot kan du komma åt funktions hanteraren `FeatureManager` via beroende inmatning:

```java
@Controller
@ConfigurationProperties("controller")
public class HomeController {
    private FeatureManager featureManager;

    public HomeController(FeatureManager featureManager) {
        this.featureManager = featureManager;
    }
}
```

## <a name="controller-actions"></a>Åtgärder för styrenhet

I MVC-styrenheter använder du attributet `@FeatureGate` för att kontrol lera om en speciell åtgärd är aktive rad. Följande `Index`s åtgärd kräver *`FeatureA` innan den* kan köras:

```java
@GetMapping("/")
@FeatureGate(feature = "FeatureA")
public String index(Model model) {
    ...
}
```

När en MVC-styrenhet eller-åtgärd blockeras eftersom kontroll funktions flaggan är *inaktive rad*, anropas ett registrerat `IDisabledFeaturesHandler`-gränssnitt. Standard `IDisabledFeaturesHandler`-gränssnittet returnerar en 404-statuskod till klienten utan svars text.

## <a name="mvc-filters"></a>MVC-filter

Du kan konfigurera MVC-filter så att de aktive ras baserat på status för en funktions flagga. Följande kod lägger till ett MVC-filter med namnet `FeatureFlagFilter`. Det här filtret utlöses endast i MVC-pipeline om `FeatureA` har Aktiver ATS.

```java
@Component
public class FeatureFlagFilter implements Filter {

    @Autowired
    private FeatureManager featureManager;

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        if(!featureManager.isEnabled("FeatureA")) {
            chain.doFilter(request, response);
            return;
        }
        ...
        chain.doFilter(request, response);
    }
}
```

## <a name="routes"></a>Vägar

Du kan använda funktions flaggor för att omdirigera vägar. Följande kod kommer att omdirigera en användare från `FeatureA` är aktive rad:

```java
@GetMapping("/redirect")
@FeatureGate(feature = "FeatureA", fallback = "/getOldFeature")
public String getNewFeature() {
    // Some New Code
}

@GetMapping("/getOldFeature")
public String getOldFeature() {
    // Some New Code
}
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du implementerar funktions flaggor i ditt våren Boot-startprogram med hjälp av `spring-cloud-azure-feature-management-web`-biblioteken. Mer information om stöd för funktions hantering i vår start-och app-konfiguration finns i följande resurser:

* [Exempel kod för vår start funktion flagga](/azure/azure-app-configuration/quickstart-feature-flag-spring-boot)
* [Hantera funktionsflaggor](./manage-feature-flags.md)
