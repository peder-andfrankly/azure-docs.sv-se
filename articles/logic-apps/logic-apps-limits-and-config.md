---
title: Begränsningar och konfiguration
description: Tjänst begränsningar, till exempel varaktighet, data flöde och kapacitet, plus konfigurations värden, till exempel IP-adresser som ska tillåtas, för Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 11/19/2019
ms.openlocfilehash: 13bced5cb692366f46032d51c3b635533110d781
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74791971"
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Gränser och konfigurations information för Azure Logic Apps

I den här artikeln beskrivs begränsningar och konfigurations information för att skapa och köra automatiserade arbets flöden med Azure Logic Apps. För automatisk energi förbrukning, se [gränser och konfiguration i energi spar läge](https://docs.microsoft.com/flow/limits-and-config).

<a name="definition-limits"></a>

## <a name="definition-limits"></a>Definitions gränser

Här är gränserna för en enda Logic app-definition:

| Namn | Gräns | Anteckningar |
| ---- | ----- | ----- |
| Åtgärder per arbets flöde | 500 | Om du vill utöka den här gränsen kan du lägga till kapslade arbets flöden efter behov. |
| Tillåtet kapslings djup för åtgärder | 8 | Om du vill utöka den här gränsen kan du lägga till kapslade arbets flöden efter behov. |
| Arbets flöden per region per prenumeration | 1,000 | |
| Utlösare per arbets flöde | 10 | När du arbetar i kodvyn, inte i designern |
| Gräns för växel omfattnings fall | 25 | |
| Variabler per arbets flöde | 250 | |
| Tecken per uttryck | 8 192 | |
| Maximal storlek för `trackedProperties` | 16 000 tecken |
| Namn för `action` eller `trigger` | 80 tecken | |
| `description` längd | 256 tecken | |
| Maximal `parameters` | 50 | |
| Maximal `outputs` | 10 | |
||||

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-limits"></a>Tids gränser för körning och kvarhållning

Här följer gränserna för en enda Logic app-körning:

| Namn | Gräns för flera innehavare | Miljö gräns för integrerings tjänst | Anteckningar |
|------|--------------------|---------------------------------------|-------|
| Körnings tid | 90 dagar | 366 dagar | Om du vill ändra standard gränsen, se [ändra körnings tid](#change-duration). |
| Kvarhållning av lagring | 90 dagar från körningens start tid | 366 dagar | Om du vill ändra standard gränsen läser du [ändra kvarhållning av lagring](#change-retention). |
| Lägsta upprepnings intervall | 1 sekund | 1 sekund ||
| Högsta upprepnings intervall | 500 dagar | 500 dagar ||
|||||

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-storage-retention"></a>Ändra körnings tid och lagrings kvarhållning

Följ dessa steg om du vill ändra standard gränsen för körnings tid och lagrings kvarhållning. Om du vill öka Max gränsen [kontaktar du Logic Appss teamet](mailto://logicappsemail@microsoft.com) för att få hjälp med dina krav.

1. I Azure Portal på din Logic Apps-meny väljer du **arbets flödes inställningar**.

1. Under **körnings alternativ**, från listan **körnings historik för kvarhållning i dagar** , väljer du **anpassad**.

1. Ange eller dra skjutreglaget för det antal dagar som du vill ha. 

   > [!NOTE]
   > För logi Kap par i Azure med flera innehavare, är standard gränsen på 90 samma som den maximala gränsen. Du kan bara minska det här värdet.
   > För Logic Apps i en integrerings tjänst miljö kan du minska eller öka standard gränsen på 90 dagar.

<a name="looping-debatching-limits"></a>

## <a name="concurrency-looping-and-debatching-limits"></a>Samtidighets gränser, slingor och avbatchorder

Här följer gränserna för en enda Logic app-körning:

| Namn | Gräns | Anteckningar |
| ---- | ----- | ----- |
| Utlös samtidighet | * Obegränsat när samtidighets kontrollen är inaktive rad <p><p>* 25 är standard gränsen när samtidighets kontrollen är aktive rad, som inte kan återställas när du har aktiverat kontrollen. Du kan ändra standardvärdet till ett värde mellan 1 och 50. | Den här gränsen beskriver det högsta antalet Logic App-instanser som kan köras samtidigt eller parallellt. <p><p>**Obs!** när samtidighet har Aktiver ATS minskas SplitOn-gränsen till 100 objekt för [debatchering av matriser](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch). <p><p>Om du vill ändra standard gränsen till ett värde mellan 1 och 50, se [ändra utlösarens samtidighets gräns](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) eller [Utlös instansen i tur och ordning](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger). |
| Maximalt antal väntande körningar | När samtidighets kontrollen är aktive rad är det minsta antalet väntande körningar 10 plus antalet samtidiga körningar (Utlös samtidighet). Du kan ändra det maximala antalet upp till 100. | Den här gränsen beskriver det högsta antalet Logic App-instanser som kan vänta på att köras när din Logic app redan kör maximalt antal samtidiga instanser. <p><p>Om du vill ändra standard gränsen, se [begränsningen för ändrings väntande körningar](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs). |
| Förgrunds mat ris objekt | 100 000 | Den här gränsen beskriver det högsta antalet mat ris objekt som en "for each"-loop kan bearbeta. <p><p>Du kan använda [åtgärden fråga](../connectors/connectors-native-query.md)för att filtrera större matriser. |
| Samtidighets samtidighet | 20 är standard gränsen när samtidighets kontrollen är inaktive rad. Du kan ändra standardvärdet till ett värde mellan 1 och 50. | Den här gränsen är det högsta antalet upprepningar av slingor som kan köras samtidigt eller parallellt. <p><p>Om du vill ändra standard gränsen till ett värde mellan 1 och 50, se [ändra "för varje" samtidighets gräns](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) eller [Kör "för varje" slingor i följd](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each). |
| SplitOn objekt | * 100 000 utan utlösarens samtidighet <p><p>* 100 med utlösarens samtidighet | För utlösare som returnerar en matris kan du ange ett uttryck som använder en ' SplitOn '-egenskap som [delar upp eller avgruppera mat ris objekt i flera arbets flödes instanser](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) för bearbetning, i stället för att använda en "förgrunds"-slinga. Det här uttrycket refererar till matrisen som används för att skapa och köra en arbets flödes instans för varje mat ris objekt. <p><p>**Obs!** när samtidighet har Aktiver ATS minskas SplitOn-gränsen till 100 objekt. |
| Tills iterationer | 5 000 | |
||||

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Data flödes gränser

Här är gränserna för en enda Logic app-definition:

### <a name="multi-tenant-logic-apps-service"></a>Logic Apps tjänst för flera innehavare

| Namn | Gräns | Anteckningar |
| ---- | ----- | ----- |
| Åtgärd: körningar per 5 minuter | 100 000 är standard gränsen, men 300 000 är max gränsen. | Om du vill ändra standard gränsen läser du köra din Logi Kap par [i läget "hög genom strömning"](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode), som finns i för hands version. Eller så kan du distribuera arbets belastningen i mer än en Logic app vid behov. |
| Åtgärd: utgående utgående samtal | ~ 2 500 | Du kan minska antalet samtidiga förfrågningar eller minska varaktigheten om det behövs. |
| Runtime-slutpunkt: samtidiga inkommande samtal | ~ 1 000 | Du kan minska antalet samtidiga förfrågningar eller minska varaktigheten om det behövs. |
| Runtime-slutpunkt: Läs anrop per 5 minuter  | 60 000 | Du kan distribuera arbets belastningen i mer än en app vid behov. |
| Runtime-slutpunkt: anropa anrop per 5 minuter | 45 000 | Du kan distribuera arbets belastningen i mer än en app vid behov. |
| Innehålls data flöde per 5 minuter | 600 MB | Du kan distribuera arbets belastningen i mer än en app vid behov. |
||||

### <a name="integration-service-environment-ise"></a>Integrerings tjänst miljö (ISE)

Här är begränsningarna för data flödet för Premium SKU: n:

| Namn | Gräns | Anteckningar |
|------|-------|-------|
| Bas enhets körnings gräns | Systemet begränsas när infrastruktur kapaciteten når 80% | Innehåller ~ 4 000 åtgärds körningar per minut, vilket är ~ 160 000 000 åtgärds körningar per månad | |
| Skalnings enhetens körnings gräns | Systemet begränsas när infrastruktur kapaciteten når 80% | Varje skalnings enhet kan ge ~ 2 000 ytterligare åtgärds körningar per minut, vilket är ~ 80 000 000 fler åtgärds körningar per månad | |
| Högsta antal skalnings enheter som du kan lägga till | 10 | |
||||

Om du vill gå över dessa gränser i normal bearbetning eller köra belastnings test som kan gå över dessa gränser kan [du kontakta Logic Apps-teamet](mailto://logicappsemail@microsoft.com) för att få hjälp med dina krav.

> [!NOTE]
> [Developer SKU: n](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) har inga publicerade gränser eftersom detta SKU inte har något service avtal (SLA) eller funktioner för att skala upp. Använd endast den här SKU: n för experimentering, utveckling och testning, inte produktion eller prestanda testning.

<a name="gateway-limits"></a>

## <a name="gateway-limits"></a>Gateway-gränser

Azure Logic Apps stöder Skriv åtgärder, inklusive infogningar och uppdateringar via gatewayen. Dessa åtgärder har dock [gränser för deras nytto Last storlek](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations).

<a name="request-limits"></a>

## <a name="http-limits"></a>HTTP-gränser

Här är gränserna för ett enda utgående eller inkommande HTTP-anrop:

#### <a name="timeout"></a>Standardvärde

Vissa kopplings åtgärder gör asynkrona anrop eller lyssnar efter webhook-begäranden, så tids gränsen för dessa åtgärder kan vara längre än dessa gränser. Mer information finns i teknisk information för den aktuella anslutningen och även för [arbets flödes utlösare och åtgärder](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action).

| Namn | Gräns för flera innehavare | Miljö gräns för integrerings tjänst | Anteckningar |
|------|--------------------|---------------------------------------|-------|
| Utgående begäran | 120 sekunder <br>(2 minuter) | 240 sekunder <br>(4 minuter) | Exempel på utgående begär Anden är anrop gjorda av HTTP-utlösare. <p><p>**Tips**: Använd ett [asynkront avsöknings mönster](../logic-apps/logic-apps-create-api-app.md#async-pattern) eller en [until-slinga](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action)för längre drift åtgärder. |
| Inkommande begäran | 120 sekunder <br>(2 minuter) | 240 sekunder <br>(4 minuter) | Exempel på inkommande förfrågningar inkluderar anrop som tagits emot av begär ande utlösare och webhook-utlösare. <p><p>**Obs!** för att den ursprungliga anroparen ska få svaret måste alla steg i svaret slutföras inom gränsen, om du inte anropar en annan Logic app som ett kapslat arbets flöde. Mer information finns i [anropa, Utlös ande eller kapsla Logic Apps](../logic-apps/logic-apps-http-endpoint.md). |
|||||

#### <a name="message-size"></a>Meddelandestorlek

| Namn | Gräns för flera innehavare | Miljö gräns för integrerings tjänst | Anteckningar |
|------|--------------------|---------------------------------------|-------|
| Meddelandestorlek | 100 MB | 200 MB | För att undvika den här gränsen, se [hantera stora meddelanden med segment](../logic-apps/logic-apps-handle-large-messages.md). Vissa anslutningar och API: er kanske inte stöder segment koppling eller till och med standard gränsen. |
| Meddelande storlek med segment | 1 GB | 5 GB | Den här gränsen gäller för åtgärder som har inbyggt stöd för segmentering eller som låter dig aktivera segment i körnings konfigurationen. <p>För integrerings tjänst miljön stöder Logic Apps motor den här gränsen, men kopplingarna har sina egna segment gränser upp till motor gränsen, till exempel, se [Azure-Blob Storage Connectors API-referens](https://docs.microsoft.com/connectors/azureblob/). Mer information om segment finns i [hantera stora meddelanden med segment](../logic-apps/logic-apps-handle-large-messages.md). |
| Utvärderings gräns för uttryck | 131 072 tecken | 131 072 tecken | `@concat()`, `@base64()`, `@string()` uttryck får inte vara längre än den här gränsen. |
|||||

#### <a name="retry-policy"></a>Återförsöksprincip

| Namn | Gräns | Anteckningar |
| ---- | ----- | ----- |
| Antal återförsök | 90 | Standardvärdet är 4. Om du vill ändra standardvärdet använder du [princip parametern för att försöka igen](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Högsta fördröjning för återförsök | 1 dag | Om du vill ändra standardvärdet använder du [princip parametern för att försöka igen](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Försök igen, min fördröjning | 5 sekunder | Om du vill ändra standardvärdet använder du [princip parametern för att försöka igen](../logic-apps/logic-apps-workflow-actions-triggers.md). |
||||

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Begränsningar för anpassade anslutningar

Här är gränserna för anpassade anslutningar som du kan skapa från webb-API: er.

| Namn | Gräns för flera innehavare | Miljö gräns för integrerings tjänst | Anteckningar |
|------|--------------------|---------------------------------------|-------|
| Antal anpassade anslutningar | 1 000 per Azure-prenumeration | 1 000 per Azure-prenumeration ||
| Antal begär Anden per minut för en anpassad anslutning | 500 förfrågningar per minut per anslutning | 2 000 förfrågningar per minut per *anpassad anslutning* ||
|||

<a name="managed-identity"></a>

## <a name="managed-identities"></a>Hanterade identiteter

| Namn | Gräns |
| ---- | ----- |
| Antal Logic Apps som har den systemtilldelade identiteten i en Azure-prenumeration per region | 100 |
|||

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Integrations konto gränser

Varje Azure-prenumeration har följande gränser för integrations kontot:

* Ett integrations konto på [kostnads fri nivå](../logic-apps/logic-apps-pricing.md#integration-accounts) per Azure-region

* 1 000 sammanlagt integrerings konton, inklusive integrations konton i alla [integrerings tjänst miljöer (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) i både [Developer-och Premium-SKU: er](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level).

* Varje ISE, vare sig [utvecklare eller Premium](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level), är begränsat till 5 sammanlagt antal integrations konton:

  | ISE SKU | Integrations konto gränser |
  |---------|----------------------------|
  | **Premium** | 5 totalt – [standard](../logic-apps/logic-apps-pricing.md#integration-accounts) konton, inklusive ett standard konto kostnads fritt. Inga kostnads fria eller grundläggande konton är tillåtna. |
  | **Developer** | 5 totalt – [kostnads fritt](../logic-apps/logic-apps-pricing.md#integration-accounts) (begränsat till 1 konto) och [standard](../logic-apps/logic-apps-pricing.md#integration-accounts) kombinations lager eller alla standard konton. Inga grundläggande konton är tillåtna. Använd [Developer-SKU: n](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) för att experimentera, utveckla och testa, men inte för produktions-eller prestanda testning. |
  |||

Ytterligare kostnader gäller för integrations konton som du lägger till utöver de integrations konton som ingår i en ISE. Information om hur priser och fakturering fungerar för ISEs finns i [pris modellen Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Pris nivåer finns i [Logic Apps prissättning](https://azure.microsoft.com/pricing/details/logic-apps/).

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Artefakt gränser per integrations konto

Här följer gränserna för antalet artefakter för varje integrations konto nivå. Pris nivåer finns i [Logic Apps prissättning](https://azure.microsoft.com/pricing/details/logic-apps/). Information om hur priser och fakturering fungerar för integrations konton finns i [Logic Apps prissättnings modell](../logic-apps/logic-apps-pricing.md#integration-accounts).

> [!NOTE]
> Använd endast den kostnads fria nivån för exempel scenarier, inte produktions scenarier. Den här nivån begränsar data flödet och användningen och har inget service nivå avtal (SLA).

| Artefakt | Kostnadsfri | Basic | Standard |
|----------|------|-------|----------|
| EDI handels avtal | 10 | 1 | 1,000 |
| EDI-handels partner | 25 | 2 | 1,000 |
| Maps | 25 | 500 | 1,000 |
| Scheman | 25 | 500 | 1,000 |
| Sammansättningar | 10 | 25 | 1,000 |
| Certifikat | 25 | 2 | 1,000 |
| Batch-konfigurationer | 5 | 1 | 50 |
||||

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Artefakt kapacitets gränser

| Artefakt | Gräns | Anteckningar |
| -------- | ----- | ----- |
| Sammansättning | 8 MB | Om du vill ladda upp filer som är större än 2 MB använder du ett [Azure Storage-konto och en BLOB-behållare](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
| Mappa (XSLT-fil) | 8 MB | Om du vill ladda upp filer som är större än 2 MB använder du [Azure Logic Apps REST API-Maps](https://docs.microsoft.com/rest/api/logic/maps/createorupdate). |
| Schema | 8 MB | Om du vill ladda upp filer som är större än 2 MB använder du ett [Azure Storage-konto och en BLOB-behållare](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
||||

| Runtime-slutpunkt | Gräns | Anteckningar |
|------------------|-------|-------|
| Läs anrop per 5 minuter | 60 000 | Du kan distribuera arbets belastningen i mer än ett konto vid behov. |
| Anropa anrop per 5 minuter | 45 000 | Du kan distribuera arbets belastningen i mer än ett konto vid behov. |
| Spåra anrop per 5 minuter | 45 000 | Du kan distribuera arbets belastningen i mer än ett konto vid behov. |
| Blockerar samtidiga anrop | ~ 1 000 | Du kan minska antalet samtidiga förfrågningar eller minska varaktigheten om det behövs. |
||||

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>B2B-protokoll (AS2, X12, EDIFACT) meddelande storlek

Här är de meddelande storleks gränser som gäller för B2B-protokoll:

| Namn | Gräns för flera innehavare | Miljö gräns för integrerings tjänst | Anteckningar |
|------|--------------------|---------------------------------------|-------|
| AS2 | v2 – 100 MB<br>v1 – 50 MB | v2 – 200 MB <br>v1 – 50 MB | Gäller för avkoda och koda |
| X12 | 50 MB | 50 MB | Gäller för avkoda och koda |
| EDIFACT | 50 MB | 50 MB | Gäller för avkoda och koda |
||||

<a name="disable-delete"></a>

## <a name="disabling-or-deleting-logic-apps"></a>Inaktivera eller ta bort Logic Apps

När du inaktiverar en Logic app instansieras inga nya körningar. Alla pågående och väntande körningar fortsätter tills de har slutförts, vilket kan ta lång tid att slutföra.

När du tar bort en logikapp instantieras inga nya körningar. Alla pågående och väntande körningar avbryts. Om du har flera tusen körningar kan det ta relativt lång tid att avbryta dem.

<a name="configuration"></a>

## <a name="firewall-configuration-ip-addresses"></a>Brand Väggs konfiguration: IP-adresser

De IP-adresser som Azure Logic Apps använder för inkommande och utgående samtal beror på den region där din Logic app finns. *Alla* Logic-appar som finns i samma region använder samma IP-adressintervall.

> [!NOTE]
> Vissa automatiserade kommunikationer, till exempel **http-** och **http + openapi** -begäranden, går direkt genom Azure Logic Apps tjänsten och kommer från de IP-adresser som visas här. Mer information om IP-adresser som används av Power automatisering finns i [gränser och konfiguration i energi spar läge](https://docs.microsoft.com/flow/limits-and-config#ip-address-configuration).

* För att stödja anrop som dina Logi Kap par direkt gör med [http](../connectors/connectors-native-http.md), [http + Swagger](../connectors/connectors-native-http-swagger.md)och andra HTTP-förfrågningar, ställer du in brand väggen med *alla* [inkommande](#inbound) *och* [utgående](#outbound) IP-adresser som används av den Logic Apps tjänsten, baserat på de regioner där dina Logi Kap par finns. De här adresserna visas under de **inkommande** och **utgående** rubrikerna i det här avsnittet och sorteras efter region.

* För att stödja anrop som [Microsoft-hanterade anslutningar](../connectors/apis-list.md) gör ställer du in brand väggen med alla [utgående](#outbound) IP-adresser som används av de här anslutningarna, baserat på de regioner där dina Logic Apps finns. De här adresserna visas under den **utgående** rubriken i det här avsnittet och sorteras efter region. 

* Om du vill aktivera kommunikation för logi Kap par som körs i en integrerings tjänst miljö (ISE) ser du till att du [öppnar dessa portar](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#network-ports-for-ise).

* Om dina Logi Kap par har problem med att komma åt Azure Storage-konton som använder [brand väggar och brand Väggs regler](../storage/common/storage-network-security.md), har du [flera alternativ för att aktivera åtkomst](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls).

  Logic Apps kan till exempel inte komma åt lagrings konton som använder brand Väggs regler och som finns i samma region. Men om du tillåter [utgående IP-adresser för hanterade anslutningar i din region](../logic-apps/logic-apps-limits-and-config.md#outbound), kan dina Logi Kap par komma åt lagrings konton som finns i en annan region, förutom när du använder Azure Table Storage-eller Azure Queue Storage-anslutningarna. Om du vill komma åt Table Storage eller Queue Storage kan du använda HTTP-utlösaren och åtgärderna i stället. Andra alternativ finns i [åtkomst till lagrings konton bakom brand väggar](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls).

* För anpassade anslutningar, [Azure Government](../azure-government/documentation-government-overview.md)och [Azure Kina 21Vianet](https://docs.microsoft.com/azure/china/)är fasta eller reserverade IP-adresser inte tillgängliga.

> [!IMPORTANT]
> Om du har brand Väggs konfigurationer som du ställer in före den 1 september 2018 kontrollerar du att de stämmer överens med de aktuella IP-adresserna i listorna för de regioner där dina Logic Apps finns.

<a name="inbound"></a>

### <a name="inbound-ip-addresses---logic-apps-service-only"></a>Inkommande IP-adresser-endast Logic Apps tjänst

| Region | IP |
|--------|----|
| Australien, östra | 13.75.153.66, 104.210.89.222, 104.210.89.244, 52.187.231.161 |
| Australien, sydöstra | 13.73.115.153, 40.115.78.70, 40.115.78.237, 52.189.216.28 |
| Brasilien, södra | 191.235.86.199, 191.235.95.229, 191.235.94.220, 191.234.166.198 |
| Kanada, centrala | 13.88.249.209, 52.233.30.218, 52.233.29.79, 40.85.241.105 |
| Kanada, östra | 52.232.129.143, 52.229.125.57, 52.232.133.109, 40.86.202.42 |
| Indien, centrala | 52.172.157.194, 52.172.184.192, 52.172.191.194, 104.211.73.195 |
| USA, centrala | 13.67.236.76, 40.77.111.254, 40.77.31.87, 104.43.243.39 |
| Asien, östra | 168.63.200.173, 13.75.89.159, 23.97.68.172, 40.83.98.194 |
| USA, östra | 137.135.106.54, 40.117.99.79, 40.117.100.228, 137.116.126.165 |
| USA, östra 2 | 40.84.25.234, 40.79.44.7, 40.84.59.136, 40.70.27.253 |
| Frankrike, centrala | 52.143.162.83, 20.188.33.169, 52.143.156.55, 52.143.158.203 |
| Frankrike, södra | 52.136.131.145, 52.136.129.121, 52.136.130.89, 52.136.131.4 |
| Japan, östra | 13.71.146.140, 13.78.84.187, 13.78.62.130, 13.78.43.164 |
| Japan, västra | 40.74.140.173, 40.74.81.13, 40.74.85.215, 40.74.68.85 |
| Sydkorea, centrala | 52.231.14.182, 52.231.103.142, 52.231.39.29, 52.231.14.42 |
| Sydkorea, södra | 52.231.166.168, 52.231.163.55, 52.231.163.150, 52.231.192.64 |
| USA, norra centrala | 168.62.249.81, 157.56.12.202, 65.52.211.164, 65.52.9.64 |
| Europa, norra | 13.79.173.49, 52.169.218.253, 52.169.220.174, 40.112.90.39 |
| Sydafrika, norra | 102.133.228.4, 102.133.224.125, 102.133.226.199, 102.133.228.9 |
| Sydafrika, västra | 102.133.72.190, 102.133.72.145, 102.133.72.184, 102.133.72.173 |
| USA, södra centrala | 13.65.98.39, 13.84.41.46, 13.84.43.45, 40.84.138.132 |
| Indien, södra | 52.172.9.47, 52.172.49.43, 52.172.51.140, 104.211.225.152 |
| Asien, sydöstra | 52.163.93.214, 52.187.65.81, 52.187.65.155, 104.215.181.6 |
| Storbritannien, södra | 51.140.79.109, 51.140.78.71, 51.140.84.39, 51.140.155.81 |
| Storbritannien, västra | 51.141.48.98, 51.141.51.145, 51.141.53.164, 51.141.119.150 |
| USA, västra centrala | 52.161.26.172, 52.161.8.128, 52.161.19.82, 13.78.137.247 |
| Europa, västra | 13.95.155.53, 52.174.54.218, 52.174.49.6, 52.174.49.6 |
| Indien, västra | 104.211.164.112, 104.211.165.81, 104.211.164.25, 104.211.157.237 |
| USA, västra | 52.160.90.237, 138.91.188.137, 13.91.252.184, 157.56.160.212 |
| USA, västra 2 | 13.66.224.169, 52.183.30.10, 52.183.39.67, 13.66.128.68 |
|||

<a name="outbound"></a>

### <a name="outbound-ip-addresses---logic-apps-service--managed-connectors"></a>Utgående IP-adresser – Logic Apps tjänst & hanterade anslutningar

| Region | Logic Apps IP | IP för hanterade anslutningar |
|--------|---------------|-----------------------|
| Australien, östra | 13.75.149.4, 104.210.91.55, 104.210.90.241, 52.187.227.245, 52.187.226.96, 52.187.231.184, 52.187.229.130, 52.187.226.139 | 13.70.72.192 - 13.70.72.207, 13.72.243.10, 40.126.251.213 |
| Australien, sydöstra | 13.73.114.207, 13.77.3.139, 13.70.159.205, 52.189.222.77, 13.77.56.167, 13.77.58.136, 52.189.214.42, 52.189.220.75 | 13.70.136.174, 13.77.50.240 - 13.77.50.255, 40.127.80.34 |
| Brasilien, södra | 191.235.82.221, 191.235.91.7, 191.234.182.26, 191.237.255.116, 191.234.161.168, 191.234.162.178, 191.234.161.28, 191.234.162.131 | 104.41.59.51, 191.232.38.129, 191.233.203.192 - 191.233.203.207 |
| Kanada, centrala | 52.233.29.92, 52.228.39.241, 52.228.39.244, 40.85.250.135, 40.85.250.212, 13.71.186.1, 40.85.252.47, 13.71.184.150 | 13.71.170.208 - 13.71.170.223, 13.71.170.224 - 13.71.170.239, 52.228.33.76, 52.228.34.13, 52.228.42.205, 52.233.26.83, 52.233.31.197, 52.237.24.126 |
| Kanada, östra | 52.232.128.155, 52.229.120.45, 52.229.126.25, 40.86.203.228, 40.86.228.93, 40.86.216.241, 40.86.226.149, 40.86.217.241 | 40.69.106.240 - 40.69.106.255, 52.229.120.52, 52.229.120.131, 52.229.120.178, 52.229.123.98, 52.229.126.202, 52.242.35.152 |
| Indien, centrala | 52.172.154.168, 52.172.186.159, 52.172.185.79, 104.211.101.108, 104.211.102.62, 104.211.90.169, 104.211.90.162, 104.211.74.145 | 52.172.211.12, 104.211.81.192 - 104.211.81.207, 104.211.98.164 |
| USA, centrala | 13.67.236.125, 104.208.25.27, 40.122.170.198, 40.113.218.230, 23.100.86.139, 23.100.87.24, 23.100.87.56, 23.100.82.16 | 13.89.171.80 - 13.89.171.95, 40.122.49.51, 52.173.245.164 |
| Asien, östra | 13.75.94.173, 40.83.127.19, 52.175.33.254, 40.83.73.39, 65.52.175.34, 40.83.77.208, 40.83.100.69, 40.83.75.165 | 13.75.36.64 - 13.75.36.79, 23.99.116.181, 52.175.23.169 |
| USA, östra | 13.92.98.111, 40.121.91.41, 40.114.82.191, 23.101.139.153, 23.100.29.190, 23.101.136.201, 104.45.153.81, 23.101.132.208 | 40.71.11.80 - 40.71.11.95, 40.71.249.205, 191.237.41.52 |
| USA, östra 2 | 40.84.30.147, 104.208.155.200, 104.208.158.174, 104.208.140.40, 40.70.131.151, 40.70.29.214, 40.70.26.154, 40.70.27.236 | 40.70.146.208 - 40.70.146.223, 52.232.188.154, 104.208.233.100 |
| Frankrike, centrala | 52.143.164.80, 52.143.164.15, 40.89.186.30, 20.188.39.105, 40.89.191.161, 40.89.188.169, 40.89.186.28, 40.89.190.104 | 40.79.130.208 - 40.79.130.223, 40.89.135.2 |
| Frankrike, södra | 52.136.132.40, 52.136.129.89, 52.136.131.155, 52.136.133.62, 52.136.139.225, 52.136.130.144, 52.136.140.226, 52.136.129.51 | 40.79.178.240 - 40.79.178.255, 52.136.133.184 |
| Japan, östra | 13.71.158.3, 13.73.4.207, 13.71.158.120, 13.78.18.168, 13.78.35.229, 13.78.42.223, 13.78.21.155, 13.78.20.232 | 13.71.153.19, 13.78.108.0 - 13.78.108.15, 40.115.186.96 |
| Japan, västra | 40.74.140.4, 104.214.137.243, 138.91.26.45, 40.74.64.207, 40.74.76.213, 40.74.77.205, 40.74.74.21, 40.74.68.85 | 40.74.100.224 - 40.74.100.239, 40.74.130.77, 104.215.61.248 |
| Sydkorea, centrala | 52.231.14.11, 52.231.14.219, 52.231.15.6, 52.231.10.111, 52.231.14.223, 52.231.77.107, 52.231.8.175, 52.231.9.39 | 52.231.18.208 - 52.231.18.223, 52.141.36.214 |
| Sydkorea, södra | 52.231.204.74, 52.231.188.115, 52.231.189.221, 52.231.203.118, 52.231.166.28, 52.231.153.89, 52.231.155.206, 52.231.164.23 | 52.231.147.0 - 52.231.147.15, 52.231.163.10 |
| USA, norra centrala | 168.62.248.37, 157.55.210.61, 157.55.212.238, 52.162.208.216, 52.162.213.231, 65.52.10.183, 65.52.9.96, 65.52.8.225 | 52.162.107.160 - 52.162.107.175, 52.162.242.161, 65.52.218.230 |
| Europa, norra | 40.113.12.95, 52.178.165.215, 52.178.166.21, 40.112.92.104, 40.112.95.216, 40.113.4.18, 40.113.3.202, 40.113.1.181 | 13.69.227.208 - 13.69.227.223, 52.178.150.68, 104.45.93.9 |
| Sydafrika, norra | 102.133.231.188, 102.133.231.117, 102.133.230.4, 102.133.227.103, 102.133.228.6, 102.133.230.82, 102.133.231.9, 102.133.231.51 | 13.65.86.57, 104.214.19.48 - 104.214.19.63, 104.214.70.191 |
| Sydafrika, västra | 102.133.72.98, 102.133.72.113, 102.133.75.169, 102.133.72.179, 102.133.72.37, 102.133.72.183, 102.133.72.132, 102.133.75.191 | 13.65.86.57, 104.214.19.48 - 104.214.19.63, 104.214.70.191 |
| USA, södra centrala | 104.210.144.48, 13.65.82.17, 13.66.52.232, 23.100.124.84, 70.37.54.122, 70.37.50.6, 23.100.127.172, 23.101.183.225 | 13.65.86.57, 104.214.19.48 - 104.214.19.63, 104.214.70.191 |
| Indien, södra | 52.172.50.24, 52.172.55.231, 52.172.52.0, 104.211.229.115, 104.211.230.129, 104.211.230.126, 104.211.231.39, 104.211.227.229 | 13.71.125.22, 40.78.194.240 - 40.78.194.255, 104.211.227.225 |
| Asien, sydöstra | 13.76.133.155, 52.163.228.93, 52.163.230.166, 13.76.4.194, 13.67.110.109, 13.67.91.135, 13.76.5.96, 13.67.107.128 | 13.67.8.240 - 13.67.8.255, 13.76.231.68, 52.187.68.19 |
| Storbritannien, södra | 51.140.74.14, 51.140.73.85, 51.140.78.44, 51.140.137.190, 51.140.153.135, 51.140.28.225, 51.140.142.28, 51.140.158.24 | 51.140.80.51, 51.140.148.0 - 51.140.148.15 |
| Storbritannien, västra | 51.141.54.185, 51.141.45.238, 51.141.47.136, 51.141.114.77, 51.141.112.112, 51.141.113.36, 51.141.118.119, 51.141.119.63 | 51.140.211.0 - 51.140.211.15, 51.141.47.105 |
| USA, västra centrala | 52.161.27.190, 52.161.18.218, 52.161.9.108, 13.78.151.161, 13.78.137.179, 13.78.148.140, 13.78.129.20, 13.78.141.75 | 13.71.195.32 - 13.71.195.47, 52.161.24.128, 52.161.26.212, 52.161.27.108, 52.161.29.35, 52.161.30.5, 52.161.102.22 |
| Europa, västra | 40.68.222.65, 40.68.209.23, 13.95.147.65, 23.97.218.130, 51.144.182.201, 23.97.211.179, 104.45.9.52, 23.97.210.126 | 13.69.64.208 - 13.69.64.223, 40.115.50.13, 52.174.88.118 |
| Indien, västra | 104.211.164.80, 104.211.162.205, 104.211.164.136, 104.211.158.127, 104.211.156.153, 104.211.158.123, 104.211.154.59, 104.211.154.7 | 104.211.146.224 - 104.211.146.239, 104.211.161.203, 104.211.189.218 |
| USA, västra | 52.160.92.112, 40.118.244.241, 40.118.241.243, 157.56.162.53, 157.56.167.147, 104.42.49.145, 40.83.164.80, 104.42.38.32 | 40.112.243.160 - 40.112.243.175, 104.40.51.248, 104.42.122.49 |
| USA, västra 2 | 13.66.210.167, 52.183.30.169, 52.183.29.132, 13.66.210.167, 13.66.201.169, 13.77.149.159, 52.175.198.132, 13.66.246.219 | 13.66.140.128 - 13.66.140.143, 13.66.218.78, 13.66.219.14, 13.66.220.135, 13.66.221.19, 13.66.225.219, 52.183.78.157 |
||||

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapar din första Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md)  
* Lär dig mer om [Vanliga exempel och scenarier](../logic-apps/logic-apps-examples-and-scenarios.md)
