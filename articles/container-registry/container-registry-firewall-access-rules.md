---
title: Åtkomst regler för brand vägg
description: Konfigurera regler för åtkomst till ett Azure Container Registry från bakom en brand vägg.
ms.topic: article
ms.date: 07/17/2019
ms.openlocfilehash: 6a0a169f7e5a7e07771cb9fee474b7f4a9391a4e
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455181"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>Konfigurera regler för åtkomst till ett Azure Container Registry bakom en brand vägg

Den här artikeln förklarar hur du konfigurerar regler i brand väggen för att tillåta åtkomst till ett Azure Container Registry. Till exempel kan en Azure IoT Edge enhet bakom en brand vägg eller proxyserver behöva komma åt ett behållar register för att hämta en behållar avbildning. Eller så kan en låst server i ett lokalt nätverk behöva åtkomst för att skicka en avbildning.

Om du i stället vill konfigurera regler för inkommande nätverks åtkomst i ett behållar register för att tillåta åtkomst endast inom ett virtuellt Azure-nätverk eller ett offentligt IP-adressintervall, se [begränsa åtkomsten till ett Azure Container Registry från ett virtuellt nätverk](container-registry-vnet.md).

## <a name="about-registry-endpoints"></a>Om register slut punkter

För att hämta eller skicka avbildningar eller andra artefakter till ett Azure Container Registry måste en klient, till exempel en Docker-daemon, interagera över HTTPS med två distinkta slut punkter.

* **Registrets REST API slut punkt** – autentiserings-och register hanterings åtgärder hanteras via registrets offentliga REST API-slutpunkt. Den här slut punkten är inloggnings serverns URL för registret eller ett associerat IP-adressintervall. 

* **Lagrings slut punkt** – Azure [allokerar blob-lagring](container-registry-storage.md) i Azure Storage-konton för varje registers räkning för att hantera behållar avbildningar och andra artefakter. När en klient ansluter till avbildnings lager i ett Azure Container Registry, begär det begär Anden med hjälp av en slut punkt för lagrings kontot som tillhandahålls av registret.

Om ditt register är [geo-replikerat](container-registry-geo-replication.md)kan en klient behöva INTERAGERA med rest-och lagrings slut punkter i en angiven region eller i flera replikerade regioner.

## <a name="allow-access-to-rest-and-storage-urls"></a>Tillåt åtkomst till REST-och lagrings-URL: er

* **REST-slutpunkt** – Tillåt åtkomst till register serverns URL, till exempel `myregistry.azurecr.io`
* **Lagrings slut punkt** – Tillåt åtkomst till alla Azure Blob Storage-konton med jokertecknet `*.blob.core.windows.net`


## <a name="allow-access-by-ip-address-range"></a>Tillåt åtkomst med IP-adressintervall

Om du behöver tillåta åtkomst till vissa IP-adresser laddar du ned [Azure IP-intervall och service märken – offentligt moln](https://www.microsoft.com/download/details.aspx?id=56519).

Du hittar IP-intervallen för ACR REST-slutpunkt genom att söka efter **AzureContainerRegistry** i JSON-filen.

> [!IMPORTANT]
> IP-adressintervall för Azure-tjänster kan ändras och uppdateringar publiceras varje vecka. Ladda ned JSON-filen regelbundet och gör nödvändiga uppdateringar i dina åtkomst regler. Om ditt scenario innefattar konfigurering av regler för nätverks säkerhets grupper i ett virtuellt Azure-nätverk för att få åtkomst till Azure Container Registry, använder du **AzureContainerRegistry** [-tjänst tag gen](#allow-access-by-service-tag) i stället.
>

### <a name="rest-ip-addresses-for-all-regions"></a>REST IP-adresser för alla regioner

```json
{
  "name": "AzureContainerRegistry",
  "id": "AzureContainerRegistry",
  "properties": {
    "changeNumber": 10,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.66.140.72/29",
    [...]
```

### <a name="rest-ip-addresses-for-a-specific-region"></a>REST IP-adresser för en angiven region

Sök efter den angivna regionen, till exempel **AzureContainerRegistry. AustraliaEast**.

```json
{
  "name": "AzureContainerRegistry.AustraliaEast",
  "id": "AzureContainerRegistry.AustraliaEast",
  "properties": {
    "changeNumber": 1,
    "region": "australiaeast",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.70.72.136/29",
    [...]
```

### <a name="storage-ip-addresses-for-all-regions"></a>Lagrings-IP-adresser för alla regioner

```json
{
  "name": "Storage",
  "id": "Storage",
  "properties": {
    "changeNumber": 19,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "13.65.107.32/28",
    [...]
```

### <a name="storage-ip-addresses-for-specific-regions"></a>Lagrings-IP-adresser för vissa regioner

Sök efter den angivna regionen, till exempel **Storage. AustraliaCentral**.

```json
{
  "name": "Storage.AustraliaCentral",
  "id": "Storage.AustraliaCentral",
  "properties": {
    "changeNumber": 1,
    "region": "australiacentral",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "52.239.216.0/23"
    [...]
```

## <a name="allow-access-by-service-tag"></a>Tillåt åtkomst per service tag-tagg

I ett virtuellt Azure-nätverk använder du nätverks säkerhets regler för att filtrera trafik från en resurs, till exempel en virtuell dator i ett behållar register. För att förenkla skapandet av reglerna för Azure-nätverk använder du **AzureContainerRegistry** [service tag](../virtual-network/security-overview.md#service-tags). En service-tagg representerar en grupp IP-adressprefix för att få åtkomst till en Azure-tjänst globalt eller per Azure-region. Taggen uppdateras automatiskt när adresserna ändras. 

Du kan till exempel skapa en regel för utgående nätverks säkerhets grupp med mål- **AzureContainerRegistry** för att tillåta trafik till ett Azure Container Registry. Om du bara vill tillåta åtkomst till tjänst tag gen i en angiven region anger du regionen i följande format: **AzureContainerRegistry**. [*regions namn*].

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [Azures metod tips för nätverks säkerhet](../security/fundamentals/network-best-practices.md)

* Lär dig mer om [säkerhets grupper](/azure/virtual-network/security-overview) i ett virtuellt Azure-nätverk



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

