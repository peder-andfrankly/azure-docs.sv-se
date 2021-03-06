---
title: Konfigurera din egen nyckel för att kryptera Azure Service Bus data i vila
description: Den här artikeln innehåller information om hur du konfigurerar din egen nyckel för att kryptera Azure Service Bus data rest.
services: service-bus-messaging
ms.service: service-bus
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: aschhab
ms.openlocfilehash: 356f825524192c3b6cf7df7f0460975f23ea4f7c
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74852294"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal-preview"></a>Konfigurera Kundhanterade nycklar för att kryptera Azure Service Bus data i vila med hjälp av Azure Portal (förhands granskning)
Azure Service Bus Premium tillhandahåller kryptering av data i vila med Azure Storage Service Encryption (Azure SSE). Service Bus Premium är beroende av Azure Storage för att lagra data och som standard krypteras alla data som lagras med Azure Storage med hjälp av Microsoft-hanterade nycklar. 

## <a name="overview"></a>Översikt
Azure Service Bus stöder nu möjligheten att kryptera data i vila med antingen Microsoft-hanterade nycklar eller Kundhanterade nycklar (Bring Your Own Key-BYOK). med den här funktionen kan du skapa, rotera, inaktivera och återkalla åtkomst till de Kundhanterade nycklar som används för att kryptera Azure Service Bus i vila.

Att aktivera funktionen BYOK är en tids inställnings process i namn området.

> [!NOTE]
> Det finns vissa varningar om kundens hanterade nyckel för kryptering på tjänst sidan. 
>   * Den här funktionen stöds av [Azure Service Bus Premium](service-bus-premium-messaging.md) -nivån. Det går inte att aktivera den för standard nivå Service Bus namn områden.
>   * Kryptering kan bara aktive ras för nya eller tomma namn områden. Om namn området innehåller data kommer krypterings åtgärden att Miss sen.
>   * Om [tjänst slut punkter för virtuella nätverk (VNet)](service-bus-service-endpoints.md) konfigureras på Azure Key Vault för din Service Bus-namnrymd, stöds inte BYOK. 

Du kan använda Azure Key Vault för att hantera dina nycklar och granska din nyckel användning. Du kan antingen skapa egna nycklar och lagra dem i ett nyckel valv, eller så kan du använda Azure Key Vault API: er för att generera nycklar. Mer information om Azure Key Vault finns i [Vad är Azure Key Vault?](../key-vault/key-vault-overview.md)

Den här artikeln visar hur du konfigurerar ett nyckel valv med Kundhanterade nycklar med hjälp av Azure Portal. Information om hur du skapar ett nyckel valv med hjälp av Azure Portal finns i [snabb start: Ange och hämta en hemlighet från Azure Key Vault med hjälp av Azure Portal](../key-vault/quick-create-portal.md).

> [!IMPORTANT]
> Att använda Kundhanterade nycklar med Azure Service Bus kräver att nyckel valvet har två obligatoriska egenskaper konfigurerade. De är: **mjuk borttagning** och **Rensa inte**. De här egenskaperna är aktiverade som standard när du skapar ett nytt nyckel valv i Azure Portal. Men om du behöver aktivera dessa egenskaper i ett befintligt nyckel valv måste du använda antingen PowerShell eller Azure CLI.

## <a name="enable-customer-managed-keys"></a>Aktivera Kundhanterade nycklar
Följ dessa steg om du vill aktivera Kundhanterade nycklar i Azure Portal:

1. Gå till ditt Service Bus Premium-namnområde.
2. På sidan **Inställningar** i Service Bus namn området väljer du **kryptering (för hands version)** .
3. Välj den **Kundhanterade nyckel krypteringen i vila** , som visas i följande bild.

    ![Aktivera kundhanterad nyckel](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>Konfigurera ett nyckel valv med nycklar

När du har aktiverat Kundhanterade nycklar måste du associera kundens hanterade nyckel med din Azure Service Bus-namnrymd. Service Bus stöder endast Azure Key Vault. Om du aktiverar alternativet för **kryptering med kundhanterad nyckel** i föregående avsnitt måste nyckeln importeras till Azure Key Vault. Nycklarna måste också ha **mjuk borttagning** och **Rensa inte** konfigurerad för nyckeln. Dessa inställningar kan konfigureras med hjälp av [PowerShell](../key-vault/key-vault-soft-delete-powershell.md) eller [CLI](../key-vault/key-vault-soft-delete-cli.md#enabling-purge-protection).

1. Om du vill skapa ett nytt nyckel valv följer du [snabb](../key-vault/key-vault-overview.md)starten för Azure Key Vault. Mer information om hur du importerar befintliga nycklar finns i [om nycklar, hemligheter och certifikat](../key-vault/about-keys-secrets-and-certificates.md).
1. Om du vill aktivera både mjuk borttagning och tömning av skydd när du skapar ett valv använder du kommandot AZ-kommandot för att [skapa](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) ett valv.

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Om du vill lägga till rensnings skydd i ett befintligt valv (som redan har mjuk borttagning aktiverat) använder du kommandot AZ-kommando för att [Uppdatera](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) .

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Skapa nycklar genom att följa dessa steg:
    1. Om du vill skapa en ny nyckel väljer du **generera/importera** från menyn **nycklar** under **Inställningar**.
        
        ![Välj knappen generera/importera](./media/configure-customer-managed-key/select-generate-import.png)

    1. Ange **alternativ** för att **generera** och ge nyckeln ett namn.

        ![Skapa en nyckel](./media/configure-customer-managed-key/create-key.png) 

    1. Nu kan du välja den här nyckeln för att associera med Service Bus namn området för kryptering i list rutan. 

        ![Välj nyckel från Key Vault](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > Du kan lägga till upp till tre nycklar för redundans. Om en av nycklarna har upphört att gälla eller inte går att komma åt, används de andra nycklarna för kryptering.
        
    1. Fyll i informationen om nyckeln och klicka på **Välj**. Detta möjliggör kryptering av data i vila i namn området med en kundhanterad nyckel. 


> [!IMPORTANT]
> Om du vill använda kundhanterad nyckel tillsammans med geo haveri beredskap, granskar du nedan – 
>
> Om du vill aktivera kryptering i vila med kundhanterad nyckel, konfigureras en [åtkomst princip](../key-vault/key-vault-secure-your-key-vault.md) för den Service Bus hanterade identiteten på det angivna Azure-valvet. Detta säkerställer kontrollerad åtkomst till Azure-valvet från Azure Service Bus namn området.
>
> På grund av detta:
> 
>   * Om [geo haveri beredskap](service-bus-geo-dr.md) redan har Aktiver ats för Service Bus-namnrymden och du vill aktivera kundhanterad nyckel, så 
>     * Bryt ihopparningen
>     * [Konfigurera åtkomst principen](../key-vault/managed-identity.md) för den hanterade identiteten för både den primära och sekundära namn rymden till nyckel valvet.
>     * Konfigurera kryptering i det primära namn området.
>     * Para om de primära och sekundära namn områdena.
> 
>   * Om du vill aktivera geo-DR på ett Service Bus namn område där kundhanterad nyckel redan har kon figurer ATS, så-
>     * [Konfigurera åtkomst principen](../key-vault/managed-identity.md) för den hanterade identiteten för det sekundära namn området till nyckel valvet.
>     * Para ihop de primära och sekundära namn områdena.


## <a name="rotate-your-encryption-keys"></a>Rotera dina krypterings nycklar

Du kan rotera din nyckel i nyckel valvet med hjälp av rotations funktionen för Azure Key Vault. Mer information finns i [Konfigurera nyckel rotation och granskning](../key-vault/key-vault-key-rotation-log-monitoring.md). Aktiverings-och utgångs datum kan också ställas in för att automatisera nyckel rotationen. Tjänsten Service Bus identifierar nya nyckel versioner och börjar använda dem automatiskt.

## <a name="revoke-access-to-keys"></a>Återkalla åtkomst till nycklar

Om du återkallar åtkomst till krypterings nycklarna rensas inte data från Service Bus. Men det går inte att komma åt data från namn området Service Bus. Du kan återkalla krypterings nyckeln via åtkomst principen eller genom att ta bort nyckeln. Läs mer om åtkomst principer och skydda nyckel valvet från [säker åtkomst till ett nyckel valv](../key-vault/key-vault-secure-your-key-vault.md).

När krypterings nyckeln har återkallats går Service Buss tjänsten på det krypterade namn området inte att fungera. Om åtkomsten till nyckeln är aktive rad eller om den borttagna nyckeln återställs, kommer Service Bus-tjänsten att välja nyckeln så att du kan komma åt data från namn området krypterad Service Bus.

> [!NOTE]
> Om du tar bort en befintlig krypterings nyckel från nyckel valvet och ersätter den med en ny nyckel i Service Bus namn området, eftersom borttagnings nyckeln fortfarande är giltig (eftersom den cachelagras) i upp till en timme, kan dina gamla data (som krypterades med den gamla nyckeln) fortfarande vara tillgängliga Alon g med nya data som nu är åtkomliga med den nya nyckeln. Det här beteendet är avsiktligt i för hands versionen av funktionen. 

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:
- [Översikt över Service Bus](service-bus-messaging-overview.md)
- [Översikt över Key Vault](../key-vault/key-vault-overview.md)


