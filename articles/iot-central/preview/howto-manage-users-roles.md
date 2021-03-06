---
title: Hantera användare och roller i Azure IoT Central program | Microsoft Docs
description: Som administratör kan du hantera användare och roller i ditt Azure IoT Central-program
author: lmasieri
ms.author: lmasieri
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 9729a51c36a520a2c196fb83515c9fa616411cf3
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974435"
---
# <a name="manage-users-and-roles-in-your-iot-central-application-preview-features"></a>Hantera användare och roller i ditt IoT Central program (för hands versions funktioner)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Den här artikeln beskriver hur du, som administratör, kan lägga till, redigera och ta bort användare i ditt Azure IoT Central-program. Artikeln beskriver också hur du hanterar roller i ditt Azure IoT Central-program.

För att få åtkomst till och använda avsnittet **Administration** måste du ha rollen **administratör** för ett Azure IoT Central-program. Om du skapar ett Azure IoT Central-program läggs du automatiskt till i **Administratörs** rollen för programmet.

## <a name="add-users"></a>Lägga till användare

Varje användare måste ha ett användar konto innan de kan logga in och komma åt ett Azure IoT Central-program. Microsoft-konton (MSA: er) och Azure Active Directory-konton (Azure AD) stöds i Azure IoT Central. Azure Active Directory grupper stöds för närvarande inte i Azure IoT Central.

Mer information finns i [Microsoft-konto Hjälp](https://support.microsoft.com/products/microsoft-account?category=manage-account) och [snabb start: Lägg till nya användare i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Om du vill lägga till en användare i ett IoT Central-program går du till sidan **användare** i avsnittet **Administration** .
    
    > [!div class="mx-imgBorder"]
    >![Hantera användare](media/howto-manage-users-roles/manage-users-pnp.png)

1. Om du vill lägga till en användare går du till sidan **användare** och väljer **+ Lägg till användare**.

1. Välj en roll för användaren på den nedrullningsbara menyn **roll** . Lär dig mer om roller i avsnittet [hantera roller](#manage-roles) i den här artikeln.

    > [!div class="mx-imgBorder"]
    >![lägga till användare och välj en roll](media/howto-manage-users-roles/add-user-pnp.png)

    > [!NOTE]
    > En användare som har en anpassad roll som ger dem behörighet att lägga till andra användare, kan bara lägga till användare till en roll med samma eller färre behörigheter än den egna rollen.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Redigera de roller som är tilldelade till användare

Roller kan inte ändras efter att de har tilldelats. Om du vill ändra rollen som är tilldelad en användare tar du bort användaren och lägger sedan till användaren igen med en annan roll.

> [!NOTE]
> De roller som är tilldelade är specifika för IoT Central program och kan inte hanteras från Azure Portal.

## <a name="delete-users"></a>Ta bort användare

Om du vill ta bort användare markerar du en eller flera kryss rutor på sidan **användare** . Välj sedan **Ta bort**.

## <a name="manage-roles"></a>Hantera roller

Med roller kan du kontrol lera vem i din organisation som tillåts utföra olika uppgifter i IoT Central. Det finns tre inbyggda roller som du kan tilldela till användare av ditt program. Du kan också [skapa anpassade roller](#create-a-custom-role) om du behöver mer detaljerad kontroll.

> [!div class="mx-imgBorder"]
> ![hantera val av roller](media/howto-manage-users-roles/manage-roles-pnp.png)

### <a name="administrator"></a>Administratör

Användare i rollen **administratör** kan hantera och kontrol lera alla delar av programmet, inklusive fakturering.

Den användare som skapar ett program tilldelas automatiskt rollen **administratör** . Det måste alltid finnas minst en användare i **Administratörs** rollen.

### <a name="builder"></a>Builder

Användare i **Builder** -rollen kan hantera alla delar av appen, men kan inte göra ändringar på flikarna administration eller kontinuerlig data export.

### <a name="operator"></a>Operator

Användare i rollen **operatör** kan övervaka enhetens hälso tillstånd och status. De är inte tillåtna att göra ändringar i enhets mallar eller för att administrera programmet. Operatörer kan lägga till och ta bort enheter, hantera enhets uppsättningar och köra analyser och jobb. 

## <a name="create-a-custom-role"></a>Skapa en anpassad roll

Om din lösning kräver mer detaljerade åtkomst kontroller kan du skapa anpassade roller med anpassade uppsättningar med behörigheter. Om du vill skapa en anpassad roll går du till sidan **roller** i avsnittet **Administration** i ditt program. Välj sedan **+ ny roll**och Lägg till ett namn och en beskrivning för rollen. Välj de behörigheter som din roll kräver och välj sedan **Spara**.

Du kan lägga till användare i den anpassade rollen på samma sätt som du lägger till användare till en inbyggd roll.

> [!div class="mx-imgBorder"]
> ![skapar en anpassad roll](media/howto-manage-users-roles/create-custom-role-pnp.png)

### <a name="custom-role-options"></a>Alternativ för anpassad roll

När du definierar en anpassad roll väljer du den uppsättning behörigheter som en användare beviljas om de är medlemmar i rollen. Vissa behörigheter är beroende av andra. Om du till exempel lägger till behörigheten **Uppdatera program instrument paneler** i en roll läggs behörigheten **Visa program instrument paneler** till automatiskt. Följande tabeller sammanfattar de tillgängliga behörigheterna och deras beroenden, som du kan använda när du skapar anpassade roller.

#### <a name="managing-devices"></a>Hantera enheter

**Behörigheter för enhets mal len**

| Namn | Beroenden |
| ---- | -------- |
| Visa | Inget     |
| Hantera | Visa <br/> Andra beroenden: Visa enhets instanser  |
| Fullständig behörighet | Visa, hantera <br/> Andra beroenden: Visa enhets instanser |

**Behörigheter för enhets instans**

| Namn | Beroenden |
| ---- | -------- |
| Visa | Inget <br/> Andra beroenden: Visa mallar och enhets grupper |
| Uppdatera | Visa <br/> Andra beroenden: Visa mallar och enhets grupper  |
| Create | Visa <br/> Andra beroenden: Visa mallar och enhets grupper  |
| Ta bort | Visa <br/> Andra beroenden: Visa mallar och enhets grupper  |
| Köra kommandon | Uppdatera, Visa <br/> Andra beroenden: Visa mallar och enhets grupper  |
| Fullständig behörighet | Visa, uppdatera, skapa, ta bort, köra kommandon <br/> Andra beroenden: Visa mallar och enhets grupper  |

**Behörigheter för enhets grupper**

| Namn | Beroenden |
| ---- | -------- |
| Visa | Inget <br/> Andra beroenden: Visa mallar för enheter och enhets instanser |
| Uppdatera | Visa <br/> Andra beroenden: Visa mallar för enheter och enhets instanser   |
| Create | Visa, uppdatera <br/> Andra beroenden: Visa mallar för enheter och enhets instanser   |
| Ta bort | Visa <br/> Andra beroenden: Visa mallar för enheter och enhets instanser   |
| Fullständig behörighet | Visa, uppdatera, skapa, ta bort <br/> Andra beroenden: Visa mallar för enheter och enhets instanser |

**Behörigheter för hantering av enhets anslutning**

| Namn | Beroenden |
| ---- | -------- |
| Läs instans | Inget <br/> Andra beroenden: Visa enhetsspecifika, enhets grupper, enhets instanser |
| Hantera instanstjänsten | Inget |
| Läs global | Inget   |
| Hantera globala | Läs global |
| Fullständig behörighet | Läs instans, hantera instans, läsa global, hantera global. <br/> Andra beroenden: Visa enhetsspecifika, enhets grupper, enhets instanser |

**Jobb behörigheter**

| Namn | Beroenden |
| ---- | -------- |
| Visa | Inget <br/> Andra beroenden: Visa enhetsspecifika, enhets instanser och enhets grupper |
| Uppdatera | Visa <br/> Andra beroenden: Visa enhetsspecifika, enhets instanser och enhets grupper |
| Create | Visa, uppdatera <br/> Andra beroenden: Visa enhetsspecifika, enhets instanser och enhets grupper |
| Ta bort | Visa <br/> Andra beroenden: Visa enhetsspecifika, enhets instanser och enhets grupper |
| Genomförande | Visa <br/> Andra beroenden: Visa enhetsspecifika, enhets instanser och enhets grupper; Uppdatera enhets instanser; Köra kommandon på enhets instanser |
| Fullständig behörighet | Visa, uppdatera, skapa, ta bort, köra <br/> Andra beroenden: Visa enhetsspecifika, enhets instanser och enhets grupper; Uppdatera enhets instanser; Köra kommandon på enhets instanser |

**Regler behörigheter**

| Namn | Beroenden |
| ---- | -------- |
| Visa | Inget <br/> Andra beroenden: Visa mallar för enheter |
| Uppdatera | Visa <br/> Andra beroenden: Visa mallar för enheter |
| Create | Visa, uppdatera <br/> Andra beroenden: Visa mallar för enheter |
| Ta bort | Visa <br/> Andra beroenden: Visa mallar för enheter |
| Fullständig behörighet | Visa, uppdatera, skapa, ta bort <br/> Andra beroenden: Visa mallar för enheter |

#### <a name="managing-the-app"></a>Hantera appen

**Behörigheter för program inställningar**

| Namn | Beroenden |
| ---- | -------- |
| Visa | Inget     |
| Uppdatera | Visa   |
| Kopiera | Visa <br/> Andra beroenden: Visa mallar för enheter, enhets instanser, enhets grupper, instrument paneler, data export, anpassning, hjälp länkar, anpassade roller, regler |
| Ta bort | Visa   |
| Fullständig behörighet | Visa, uppdatera, kopiera och ta bort <br/> Andra beroenden: Visa enhetsspecifika, enhets grupper, program instrument paneler, data export, anpassning, hjälp länkar, anpassade roller, regler |

**Export behörigheter för program mal len**

| Namn | Beroenden |
| ---- | -------- |
| Visa | Inget     |
| Exportera | Visa <br/> Andra beroenden: Visa mallar för enheter, enhets instanser, enhets grupper, instrument paneler, data export, anpassning, hjälp länkar, anpassade roller, regler |
| Fullständig behörighet | Visa, exportera <br/> Andra beroenden: Visa enhetsspecifika, enhets grupper, program instrument paneler, data export, anpassning, hjälp länkar, anpassade roller, regler |

**Fakturerings behörigheter**

| Namn | Beroenden |
| ---- | -------- |
| Hantera | Inget     |
| Fullständig behörighet | Hantera |

#### <a name="managing-users-and-roles"></a>Hantera användare och roller

**Behörigheter för anpassade roller**

| Namn | Beroenden |
| ---- | -------- |
| Visa | Inget |
| Uppdatera | Visa |
| Create | Visa, uppdatera |
| Ta bort | Visa |
| Fullständig behörighet | Visa, uppdatera, skapa, ta bort |

**Användar hanterings behörigheter**

| Namn | Beroenden |
| ---- | -------- |
| Visa | Inget <br/> Andra beroenden: Visa anpassade roller |
| Lägg till | Visa <br/> Andra beroenden: Visa anpassade roller |
| Ta bort | Visa <br/> Andra beroenden: Visa anpassade roller |
| Fullständig behörighet | Visa, lägga till, ta bort <br/> Andra beroenden: Visa anpassade roller |

> [!NOTE]
> En användare som har en anpassad roll som ger dem behörighet att lägga till andra användare, kan bara lägga till användare till en roll med samma eller färre behörigheter än den egna rollen.

#### <a name="customizing-the-app"></a>Anpassa appen

**Behörigheter för program instrument panel**

| Namn | Beroenden |
| ---- | -------- |
| Visa | Inget     |
| Uppdatera | Visa   |
| Create | Visa, uppdatera |
| Ta bort | Visa   |
| Fullständig behörighet | Visa, uppdatera, skapa, ta bort |

**Behörigheter för personliga instrument paneler**

| Namn | Beroenden |
| ---- | -------- |
| Visa | Inget     |
| Uppdatera | Visa   |
| Create | Visa, uppdatera   |
| Ta bort | Visa   |
| Fullständig behörighet | Visa, uppdatera, skapa, ta bort |

**Behörigheter för anpassning, favicon och färger**

| Namn | Beroenden |
| ---- | -------- |
| Visa | Inget     |
| Uppdatera | Visa   |
| Fullständig behörighet | Visa, uppdatera |

**Hjälp länkar behörigheter**

| Namn | Beroenden |
| ---- | -------- |
| Visa | Inget     |
| Uppdatera | Visa   |
| Fullständig behörighet | Visa, uppdatera |

#### <a name="extending-the-app"></a>Utöka appen

**Behörigheter för data export**

| Namn | Beroenden |
| ---- | -------- |
| Visa | Inget     |
| Uppdatera | Visa   |
| Create | Visa, uppdatera  |
| Ta bort | Visa   |
| Fullständig behörighet | Visa, uppdatera, skapa, ta bort |

**Behörigheter för API-token**

| Namn | Beroenden |
| ---- | -------- |
| Visa | Inget     |
| Create | Visa   |
| Ta bort | Visa   |
| Fullständig behörighet | Visa, skapa, ta bort |

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du hanterar användare och roller i ditt Azure IoT Central-program är det föreslagna nästa steg att lära dig hur du [hanterar din faktura](howto-view-bill.md).
