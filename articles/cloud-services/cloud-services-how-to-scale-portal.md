---
title: Skala en moln tjänst automatiskt i portalen | Microsoft Docs
description: Lär dig hur du använder portalen för att konfigurera regler för automatisk skalning för en webb roll eller arbets roll i en moln tjänst i Azure.
services: cloud-services
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 05/18/2017
ms.author: gwallace
ms.openlocfilehash: 7e106dbd237be79be924afadbe893669c4f3daf8
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359617"
---
# <a name="how-to-configure-auto-scaling-for-a-cloud-service-in-the-portal"></a>Så här konfigurerar du automatisk skalning för en moln tjänst i portalen

Villkor kan anges för en arbets roll för en moln tjänst som utlöser en skalning i eller ut-åtgärd. Villkoren för rollen kan baseras på CPU, disk eller nätverks belastning för rollen. Du kan också ange ett villkor baserat på en meddelandekö eller mått för en annan Azure-resurs som är associerad med din prenumeration.

> [!NOTE]
> Den här artikeln fokuserar på webb-och arbets roller i moln tjänster. När du skapar en virtuell dator (klassisk) direkt är den värdbaserad i en moln tjänst. Du kan skala en virtuell standard dator genom att associera den med en [tillgänglighets uppsättning](../virtual-machines/windows/classic/configure-availability-classic.md) och aktivera eller inaktivera dem manuellt.

## <a name="considerations"></a>Överväganden
Du bör fundera över följande information innan du konfigurerar skalning för programmet:

* Skalning påverkas av kärn användning.

    Större roll instanser använder fler kärnor. Du kan bara skala ett program inom gränsen för kärnor för din prenumeration. Anta till exempel att din prenumeration har en gräns på 20 kärnor. Om du kör ett program med två medel stora moln tjänster (totalt 4 kärnor) kan du bara skala upp andra moln tjänst distributioner i din prenumeration med de återstående 16 kärnorna. Mer information om storlekar finns i [moln tjänst storlekar](cloud-services-sizes-specs.md).

* Du kan skala baserat på ett tröskel för Queue meddelande. Mer information om hur du använder köer finns i [så här använder du tjänsten Queue Storage](../storage/queues/storage-dotnet-how-to-use-queues.md).

* Du kan också skala andra resurser som är kopplade till din prenumeration.

* Om du vill aktivera hög tillgänglighet för ditt program bör du kontrol lera att det har distribuerats med två eller fler roll instanser. Mer information finns i [service nivå avtal](https://azure.microsoft.com/support/legal/sla/).

* Automatisk skalning sker bara när alla roller har statusen **klar** .  


## <a name="where-scale-is-located"></a>Där skalan finns
När du har valt moln tjänsten bör du se till att moln tjänst bladet är synligt.

1. På bladet moln tjänst väljer du namnet på moln tjänsten på panelen **roller och instanser** .   
   **VIKTIGT!** Se till att klicka på moln tjänst rollen, inte roll instansen som är under rollen.

    ![](./media/cloud-services-how-to-scale-portal/roles-instances.png)
2. Välj **skalnings** panelen.

    ![](./media/cloud-services-how-to-scale-portal/scale-tile.png)

## <a name="automatic-scale"></a>Automatisk skalning
Du kan konfigurera skalnings inställningar för en roll med antingen två lägen **manuellt** eller **automatiskt**. Manuellt är så som du förväntar dig, ställer du in det absoluta antalet instanser. Med automatisk kan du ange regler som styr hur mycket du ska skala.

Ställ in alternativet **skala enligt** för **schema-och prestanda regler**.

![Skalnings inställningar för moln tjänster med profil och regel](./media/cloud-services-how-to-scale-portal/schedule-basics.png)

1. En befintlig profil.
2. Lägg till en regel för den överordnade profilen.
3. Lägg till en annan profil.

Välj **Lägg till profil**. Profilen avgör vilket läge du vill använda för skalan: Always , upprepnings **datum och fast datum**.

När du har konfigurerat profilen och reglerna väljer du ikonen **Spara** längst upp.

#### <a name="profile"></a>Profil
Profilen anger minsta och högsta antal instanser för skalan och även när det här skalnings intervallet är aktivt.

* **Alltid**

    Behåll alltid det här instans intervallet tillgängligt.  

    ![Moln tjänst som alltid skalar](./media/cloud-services-how-to-scale-portal/select-always.png)
* **Frekvens**

    Välj en uppsättning dagar i veckan som ska skalas.

    ![Moln tjänst skala med ett upprepnings schema](./media/cloud-services-how-to-scale-portal/select-recurrence.png)
* **Fast datum**

    Ett fast datum intervall för att skala rollen.

    ![Moln tjänst skala med ett fast datum](./media/cloud-services-how-to-scale-portal/select-fixed.png)

När du har konfigurerat profilen väljer du knappen **OK** längst ned på bladet profil.

#### <a name="rule"></a>Regel
Regler läggs till i en profil och representerar ett villkor som utlöser skalan.

Regel utlösaren baseras på ett mått för moln tjänsten (processor användning, disk aktivitet eller nätverks aktivitet) som du kan lägga till ett villkorligt värde i. Du kan också ha utlösaren baserat på en meddelandekö eller en annan Azure-resurs som är associerad med din prenumeration.

![](./media/cloud-services-how-to-scale-portal/rule-settings.png)

När du har konfigurerat regeln väljer du knappen **OK** längst ned på bladet regel.

## <a name="back-to-manual-scale"></a>Tillbaka till manuell skalning
Navigera till [skalnings inställningarna](#where-scale-is-located) och ange alternativet **skala efter** till **ett instans antal som jag anger manuellt**.

![Skalnings inställningar för moln tjänster med profil och regel](./media/cloud-services-how-to-scale-portal/manual-basics.png)

Den här inställningen tar bort automatisk skalning från rollen och du kan sedan ange antalet instanser direkt.

1. Alternativet skala (manuellt eller automatiskt).
2. Ett skjutreglage för roll instans som anger vilka instanser som ska skalas till.
3. Instanser av rollen som ska skalas till.

När du har konfigurerat skalnings inställningarna väljer du ikonen **Spara** längst upp.
