---
title: Hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer
description: Den här artikeln innehåller en översikt över hur du använder Azure Automation Uppdateringshantering för att hantera uppdateringar och korrigeringar för dina virtuella Azure-och icke-Azure-datorer.
services: automation
author: mgoedtel
ms.service: automation
ms.subservice: update-management
ms.topic: tutorial
ms.date: 12/03/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 44372f32227bbfef46a72afa6f9b0bd88b29905b
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806566"
---
# <a name="manage-updates-and-patches-for-your-azure-vms"></a>Hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer

Du kan använda uppdateringshanteringen för att hantera uppdateringar och korrigeringar av virtuella datorer. I den här självstudien får du lära dig hur du utvärderar statusen för tillgängliga uppdateringar snabbt, planerar installation av uppdateringar som krävs, granskar distributionsresultat och skapar en avisering för att verifiera uppdateringar.

Prisinformation finns i [Automation-priser för uppdateringshantering](https://azure.microsoft.com/pricing/details/automation/).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Publicera en virtuell dator för hantering av uppdateringar
> * Visa en uppdateringsbedömning
> * Konfigurera aviseringar
> * Schemalägga en uppdateringsdistribution
> * Visa resultatet av en distribution

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina månatliga Azure-krediter för Visual Studio-prenumeranter ](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ett [Azure Automation-konto](automation-offering-get-started.md) för bevakaren samt runbooks-flöden för åtgärd och bevakaraktivitet.
* En [virtuell dator](../virtual-machines/windows/quick-create-portal.md) som du vill publicera.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="enable-update-management"></a>Aktivera uppdateringshantering

Först aktiverar du uppdateringshantering på din virtuella datorn för att kunna genomföra den här självstudien:

1. Välj **virtuella datorer** på [Azure Portal](https://portal.azure.com) -menyn eller Sök efter och välj **virtuella datorer** på **Start** sidan.
1. Välj den virtuella dator som du vill aktivera Uppdateringshantering för.
1. På sidan för virtuell dator under **ÅTGÄRDER** väljer du **Uppdateringshantering**. Fönstret **Aktivera uppdateringshantering** öppnas.

Verifieringen utförs för att fastställa om uppdateringshantering är aktiverat för den här virtuella datorn. Den här verifieringen innehåller kontroller för en Log Analytics arbets yta och ett länkat Automation-konto och om Uppdateringshanterings lösningen är aktive rad i arbets ytan.

En [Log Analytics](../azure-monitor/platform/data-platform-logs.md)-arbetsyta används för att samla in data som genereras av funktioner och tjänster som uppdateringshantering. Arbetsytan tillhandahåller en enda plats för att granska och analysera data från flera källor.

Verifierings processen kontrollerar också om den virtuella datorn är etablerad med Log Analytics agent och automation Hybrid Runbook Worker. Den här agenten används för att kommunicera med Azure Automation och hämta information om uppdateringsstatus. Agenten kräver att port 443 till öppnas för att kommunicera med Azure Automation-tjänsten och för att ladda ner uppdateringar.

Om några av följande krav saknades under publiceringen läggs de till automatiskt:

* [Log Analytics](../azure-monitor/platform/data-platform-logs.md)-arbetsyta
* Ett [Automation-konto](./automation-offering-get-started.md)
* En [Hybrid Runbook Worker](./automation-hybrid-runbook-worker.md) (aktiverad på den virtuella datorn)

Under **Uppdateringshantering**, ange platsen, Log Analytics-arbetsytan och Automation-kontot som ska användas. Välj sedan **Aktivera**. Om dessa alternativ inte är tillgängliga, innebär det att en annan automation-lösning är aktiverad för den virtuella datorn. I så fall måste samma arbetsyta och Automation-konto användas.

![Aktivera fönstret Uppdateringshanteringslösning](./media/automation-tutorial-update-management/manageupdates-update-enable.png)

Det kan ta några minuter att aktivera lösningen. Under tiden ska du inte stänga webbläsaren. När lösningen har aktiverats flödar information om saknade uppdateringar på den virtuella datorn till Azure Monitor-loggar. Det kan ta mellan 30 minuter och 6 timmar innan data blir tillgängliga för analys.

## <a name="view-update-assessment"></a>Visa kontroll av uppdateringar

När uppdateringshantering är aktiverat visas fönstret **Uppdateringshantering**. Om några uppdateringar identifieras som saknade visas en lista med uppdateringar som saknas på fliken uppdateringar som **saknas** .

Under **informations länk**väljer du länken Uppdatera för att öppna Support artikeln för uppdateringen. Du kan läsa viktig information om uppdateringen.

![Visa uppdateringsstatus](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Klicka någon annanstans på uppdateringen för att öppna fönstret **Loggsökning** för den markerade uppdateringen. Frågan för loggsökningen är fördefinierad för den specifika uppdateringen. Du kan ändra den här frågan eller skapa en egen fråga om du vill visa detaljerad information om uppdateringarna som har distribuerats eller som saknas i din miljö.

![Visa uppdateringsstatus](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Konfigurera varningar

I det här steget lär du dig hur du ställer in en avisering som meddelar dig om statusen för en uppdateringsdistribution.

### <a name="alert-conditions"></a>Aviseringsvillkor

Gå till **Aviseringar** under **Övervakning** i ditt Automation-konto och klicka sedan på **+ Ny aviseringsregel**.

Ditt Automation-konto har redan valts som resursen. Om du vill ändra den kan du klicka på **Välj** och välja **Automation-konton** i listrutan **Filtrera efter resurstyp** på sidan **Välj en resurs**. Välj ditt Automation-konto och välj **Klar**.

Klicka på **Lägg till villkor** för att välja lämplig signal för uppdateringsdistributionen. Följande tabell visar information om de två tillgängliga signalerna för uppdateringsdistributioner:

|Signalnamn|Dimensioner|Beskrivning|
|---|---|---|
|**Antal körningar av uppdateringsdistributionen**|– Namnet på uppdateringsdistributionen</br>– Status|Den här signalen används för att avisera om uppdateringsdistributionens övergripande status.|
|**Antal datorspecifika körningar av uppdateringsdistributionen**|– Namnet på uppdateringsdistributionen</br>– Status</br>– Måldator</br>-Uppdatera körnings-ID för distribution|Den här signalen används för att avisera om statusen för en uppdateringsdistribution som är riktad mot specifika datorer|

För dimensionsvärdena väljer du ett giltigt värde i listan. Om värdet som du letar efter inte visas i listan klickar du på **\+** -tecknet bredvid dimensionen och skriver ett anpassat namn. Därefter kan du välja det värde som du vill söka efter. Om du vill välja alla värden från en dimension klickar du på knappen **Välj \*** . Om du inte väljer ett värde för en dimension ignoreras den dimensionen under utvärderingen.

![Konfigurera signallogiken](./media/automation-tutorial-update-management/signal-logic.png)

Under **Aviseringslogik**, för **Tröskelvärde**, ange **1**. När du är klar väljer du **Klar**.

### <a name="alert-details"></a>Aviseringsinformation

Under **2. Definiera aviserings information**, ange ett namn och en beskrivning för aviseringen. Ange **allvarlighetsgrad** till **Information (allvarlighetsgrad 2)** för en lyckad körning eller **Information (allvarlighetsgrad 1)** för en misslyckad körning.

![Konfigurera signallogiken](./media/automation-tutorial-update-management/define-alert-details.png)

Välj **Skapa ny** under **Åtgärdsgrupper**. En åtgärdsgrupp är en grupp av åtgärder som kan användas i flera aviseringar. Dessa åtgärder kan inkludera, men är inte begränsade till, e-postmeddelanden, runbooks, webhooks och mycket mer. Läs mer om åtgärdsgrupper i [Skapa och hantera åtgärdsgrupper](../azure-monitor/platform/action-groups.md).

I rutan **Åtgärdsgruppnamn** anger du ett namn för aviseringen och ett kortnamn. Det korta namnet används i stället för ett fullständigt åtgärdsgruppnamn när meddelanden skickas med den här gruppen.

Under **Åtgärder**, ange ett namn för åtgärden, såsom **e-postmeddelanden**. Under **ÅTGÄRDSTYP**, välj **e-post/SMS/Push/röst**. Under **INFORMATION** väljer du **Redigera detaljer**.

I rutan **e-post/SMS/Push/röst**, ange ett namn. Välj kryssrutan **e-post** och ange sedan en giltig e-postadress.

![Konfigurera en e-poståtgärdsgrupp](./media/automation-tutorial-update-management/configure-email-action-group.png)

I rutan **e-post/SMS/Push/röst**, välj **OK**. I fönstret **Lägg till åtgärdsgrupp**, välj **OK**.

Anpassa föremål för aviserings-e-postmeddelandet under **Skapa regel**, under **Anpassa åtgärder**, välj **e-postmeddelandets ämne**. När du är klar väljer du **Skapa varningsregel**. Varningen berättar när en distribution lyckas och vilka datorer som var en del av denna uppdaterade distributionskörning.

## <a name="schedule-an-update-deployment"></a>Schemalägga en uppdateringsdistribution

För att installera uppdateringar schemalägger du en distribution som passar ditt schema och servicefönster. Du kan välja vilka uppdateringstyper som ska tas med i distributionen. Du kan till exempel ta med kritiska uppdateringar eller säkerhetsuppdateringar och exkludera samlade uppdateringar.

>[!NOTE]
>När du schemalägger en uppdaterings distribution skapar den en [schema](shared-resources/schedules.md) resurs som är länkad till **MicrosoftOMSComputers-** runbooken som hanterar uppdaterings distributionen på mål datorerna. Om du tar bort schema resursen från Azure Portal eller använder PowerShell när du har skapat distributionen bryts den schemalagda uppdaterings distributionen och ett fel uppstår när du försöker konfigurera om den från portalen. Du kan bara ta bort schema resursen genom att ta bort motsvarande distributions schema.  
>

Schemalägg en ny uppdateringsdistribution för den virtuella datorn, gå till **Uppdateringshantering** och välj sedan **Distribution av schemauppdatering**.

Under **Ny uppdateringsdistribution** anger du följande information:

* **Namn**: Ange ett unikt namn på uppdateringsdistributionen.

* **Operativsystem**: Välj operativsystem som mål för uppdateringsdistributionen.

* **Grupper att uppdatera (förhandsversion)** : definiera en fråga som baseras på en kombination av prenumeration, resursgrupper, platser och taggar för att skapa en dynamisk grupp med virtuella Azure-datorer som ska ingå i din distribution. Mer information finns i [Dynamiska grupper](automation-update-management-groups.md)

* **Datorer som ska uppdateras**: Välj en sparad sökning eller en importerad grupp, eller välj Dator i listrutan och välj enskilda datorer. Om du väljer **Datorer** visas beredskapen för datorn i kolumnen **Uppdatera agentberedskap**. Information om de olika metoderna för att skapa datorgrupper i Azure Monitor-loggar finns i [datorgrupper i Azure Monitor-loggar](../azure-monitor/platform/computer-groups.md)

* **Uppdatera klassificering**: Välj vilka typer av programvara som ska tas med i uppdateringsdistributionen. Låt alla typer vara markerade för den här självstudien.

  Klassificeringstyper:

   |OS  |Typ  |
   |---------|---------|
   |Windows     | Kritiska uppdateringar</br>Säkerhetsuppdateringar</br>Samlade uppdateringar</br>Funktionspaket</br>Service pack</br>Definitionsuppdateringar</br>Verktyg</br>Uppdateringar        |
   |Linux     | Kritiska uppdateringar och säkerhetsuppdateringar</br>Övriga uppdateringar       |

   En beskrivning av klassificeringstyper finns i [uppdatera klassificeringar](automation-view-update-assessments.md#update-classifications).

* **Uppdateringar att inkludera/exkludera** – detta öppnar sidan **Inkludera/exkludera**. Uppdateringar som ska inkluderas eller exkluderas visas på en separat flik.

> [!NOTE]
> Det är viktigt att veta att undantagen åsidosätter inkluderingar. Om du till exempel definierar en undantags regel för `*`installeras inga korrigeringar eller paket eftersom de undantas. Undantagna uppdateringar visas fortfarande som saknas på datorn. För Linux-datorer om ett paket ingår men har ett beroende paket som uteslutits, installeras inte paketet.

* **Schemainställningar**: Sidan **Schemainställningar** öppnas. Starttiden är som standard 30 minuter efter den aktuella tiden. Du kan ange starttiden till helst från 10 minuter i framtiden.

   Du kan också ange om distributionen ska ske en gång eller ange ett schema med återkommande tider. Under **Återkommande**, välj **En gång**. Lämna standardvärdet till 1 dag och välj **OK**. Då ställs ett återkommande schema in.

* **Skript före och efter**: Välj skript som ska köras före och efter distributionen. Mer information finns i [Hantera skript före och efter](pre-post-scripts.md).

* **Underhållsperiod (minuter)** : Låt standardvärdet stå kvar. Windows-underhåll styr den tids period som tillåts för att installera uppdateringar. Tänk på följande när du anger en underhålls period.

  * Underhålls fönster styr hur många uppdateringar som försöker installeras.
  * Uppdateringshantering slutar inte att installera nya uppdateringar om slutet av en underhålls period närmar sig.
  * Uppdateringshantering avslutar inte pågående uppdateringar om underhålls perioden har överskridits.
  * Om underhålls perioden överskrids i Windows beror det ofta på att en service pack uppdatering tar lång tid att installera.

  > [!NOTE]
  > Om du vill undvika att uppdateringar tillämpas utanför en underhålls period på Ubuntu konfigurerar du om det obevakade uppgraderings paketet så att automatiska uppdateringar inaktive ras. Information om hur du konfigurerar paketet finns i [avsnittet automatiska uppdateringar i Ubuntu Server guide](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

* **Omstartsalternativ**: Den här inställningen avgör hur omstarter ska hanteras. De tillgängliga alternativen är:
  * Starta om vid behov (standard)
  * Starta alltid om
  * Starta aldrig om
  * Endast omstart – uppdateringar installeras inte

> [!NOTE]
> De register nycklar som listas under [register nycklar som används för att hantera omstart](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) kan orsaka en händelse för omstart om **kontroll av omstart** är inställd på **aldrig omstart**.

När du är klar med att konfigurera schemat väljer du **Skapa**.

![Inställningsfönster för uppdateringsschema](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

Du kommer tillbaka till statusinstrumentpanelen. Välj **Schemalagda uppdateringsdistributioner** om du vill visa distributionsschemat du har skapat.

> [!NOTE]
> Uppdateringshantering har stöd för distribution av förstapartsuppdateringar och förnedladdning av korrigeringar. Detta kräver ändringar på de system som korrigeras. Information om hur du konfigurerar dessa inställningar på systemet finns i avsnittet om [support för förstapart och förnedladdning](automation-configure-windows-update.md).

**Uppdaterings distributioner** kan också skapas program mässigt. Information om hur du skapar en **uppdaterings distribution** med REST API finns i [program uppdaterings konfiguration – skapa](/rest/api/automation/softwareupdateconfigurations/create). Det finns också en exempel-Runbook som kan användas för att skapa en veckovis **uppdaterings distribution**. Mer information om denna Runbook finns i [skapa en veckovis uppdaterings distribution för en eller flera virtuella datorer i en resurs grupp](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

## <a name="view-results-of-an-update-deployment"></a>Visa resultat för en uppdateringsdistribution

När den schemalagda distributionen startar kan du se status för distributionen på fliken **Uppdateringsdistributioner** under **Uppdateringshantering**. Statusen är **Pågår** när distributionen körs. När distributionen är klar, om den lyckas, ändras statusen till **Lyckades**. Om det uppstod några fel med en eller flera uppdateringar i distributionen visas status **Misslyckades delvis**.

Välj den slutförda uppdateringsdistributionen för att visa instrumentpanelen för distributionen.

![Statusinstrumentpanel för Uppdatera distribution för en specifik distribution](./media/automation-tutorial-update-management/manageupdates-view-results.png)

Under **Uppdateringsresultat** visas en sammanfattning av det totala antalet uppdateringar och distributionsresultat för den virtuella datorn. Tabellen till höger visar detaljer för varje uppdatering och installationsresultaten.

I följande lista visas tillgängliga värden:

* **Inget försök har gjorts**: Uppdateringen installerades inte eftersom det inte fanns tillräckligt med tid utifrån det underhållsfönster som definierats.
* **Lyckades**: Uppdateringen lyckades.
* **Misslyckades**: Uppdateringen misslyckades.

Välj **Alla loggar** om du vill se alla loggposter som har skapats för distributionen.

Välj **Utdata** om du vill se jobbströmmen för den runbook som ansvarar för att hantera uppdateringsdistributionen på den virtuella måldatorn.

Välj **Fel** om du vill se detaljerad information om fel som uppstått vid distributionen.

När uppdateringsdistributionen av är klar skickas e-post som liknar följande exempel för att visa resultatet av distributionen:

![Konfigurera e-poståtgärdsgrupp](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Publicera en virtuell dator för hantering av uppdateringar
> * Visa en uppdateringsbedömning
> * Konfigurera aviseringar
> * Schemalägga en uppdateringsdistribution
> * Visa resultatet av en distribution

Fortsätt till översikten för uppdateringshanteringslösningen.

> [!div class="nextstepaction"]
> [Uppdateringshanteringslösning](../operations-management-suite/oms-solution-update-management.md?toc=%2fazure%2fautomation%2ftoc.json)

