---
title: Arbetsflöden för att utforma princip som kod
description: Lär dig att utforma arbets flöden för att distribuera dina Azure Policy-definitioner som kod och validera resurserna automatiskt.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 0914ba6510c9d2ef87d3f83417f97340d42c8bce
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74267265"
---
# <a name="design-policy-as-code-workflows"></a>Arbetsflöden för att utforma princip som kod

När du fortskrider på resan med moln styrning vill du byta från manuellt hantering av varje princip definition i Azure Portal eller genom de olika SDK: erna till något mer hanterbart och upprepat i företags skala. Två av de dominerande metoderna för att hantera system i skala i molnet är:

- Infrastruktur som kod: metoden att behandla innehållet som definierar dina miljöer, allt från Resource Manager-mallar för att Azure Policy definitioner till Azure-ritningar som käll kod.
- DevOps: föreningen av personer, processer och produkter för att möjliggöra kontinuerlig leverans av värde till våra slutanvändare.

Princip som kod är kombinationen av dessa idéer. Behåll i princip definitionerna i käll kontrollen och när en ändring görs, testa och validera den ändringen. Det bör dock inte vara den omfattning av principer som används i infrastrukturen som kod eller DevOps.

Validerings steget bör också vara en komponent i andra kontinuerliga integreringar eller arbets flöden för kontinuerlig distribution. Exempel på detta är att distribuera en program miljö eller virtuell infrastruktur. Genom att göra Azure Policy validera en tidig komponent i bygg-och distributions processen kan program-och drift teamen upptäcka om deras ändringar inte är av klagomål, långa innan det är för sent och de försöker distribuera i produktion.

## <a name="workflow-overview"></a>Översikt över arbets flöde

Det rekommenderade allmänna arbets flödet som kod ser ut som i det här diagrammet:

![Princip som kod arbets flöde – översikt](../media/policy-as-code/policy-as-code-workflow.png)

### <a name="create-and-update-policy-definitions"></a>Skapa och uppdatera princip definitioner

Princip definitionerna skapas med JSON och lagras i käll kontroll. Varje princip har en egen uppsättning filer, till exempel parametrar, regler och miljö parametrar, som ska lagras i samma mapp. Följande struktur är ett rekommenderat sätt att behålla princip definitionerna i käll kontrollen.

```text
.
|
|- policies/  ________________________ # Root folder for policies
|  |- policy1/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
|  |- policy2/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
```

När en ny princip läggs till eller en befintlig har uppdaterats, bör arbets flödet automatiskt uppdatera princip definitionen i Azure. Testning av den nya eller uppdaterade princip definitionen kommer i ett senare steg.

### <a name="create-and-update-initiative-definitions"></a>Skapa och uppdatera initiativ definitioner

Initiativ har också sin egen JSON-fil och relaterade filer som ska lagras i samma mapp. Initiativ definitionen kräver att princip definitionen redan finns, så det går inte att skapa eller uppdatera förrän principen har uppdaterats i käll kontroll och sedan uppdaterats i Azure. Följande struktur är ett rekommenderat sätt att hålla dina initiativ definitioner i käll kontroll:

```text
.
|
|- initiatives/ ______________________ # Root folder for initiatives
|  |- init1/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
|  |- init2/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
```

Precis som princip definitioner, när du lägger till eller uppdaterar ett befintligt initiativ, bör arbets flödet automatiskt uppdatera initiativ definitionen i Azure. Testning av den nya eller uppdaterade initiativ definitionen finns i ett senare steg.

### <a name="test-and-validate-the-updated-definition"></a>Testa och validera den uppdaterade definitionen

När Automation har vidtagit de nyligen skapade eller uppdaterade definitionerna för principer eller initiativ och gjort uppdateringen till objektet i Azure, är det dags att testa ändringarna som har gjorts. Antingen principen eller de initiativ som den är en del av bör sedan tilldelas resurser i miljön längst bort från produktionen. Den här miljön är vanligt vis _dev_.

Tilldelningen ska använda [enforcementMode](./assignment-structure.md#enforcement-mode) _inaktiverat_ så att inga resurser skapas och uppdateringar blockeras, men att befintliga resurser fortfarande granskas för att följa den uppdaterade princip definitionen. Även med enforcementMode rekommenderar vi att tilldelnings omfånget är antingen en resurs grupp eller en prenumeration som används särskilt för att verifiera principer.

> [!NOTE]
> Även om tvingande läge är användbart, är det inte en ersättning för att noggrant testa en princip definition under olika förhållanden. Princip definitionen bör testas med `PUT` och `PATCH` REST API-anrop, kompatibla och icke-kompatibla resurser och Edge-fall som en egenskap som saknas i resursen.

När tilldelningen har distribuerats kan du använda princip-SDK: n för att [Hämta kompatibilitetsinformation](../how-to/get-compliance-data.md) för den nya tilldelningen. Den miljö som används för att testa principerna och tilldelningarna bör ha både kompatibla och icke-kompatibla resurser. Precis som ett bra enhets test för kod vill du testa att resurserna är som förväntat och att du inte har några falska positiva eller falska negativa negativa. Om du testar och validerar enbart för det du förväntar dig kan det uppstå oväntade och oidentifierade konsekvenser från principen. Mer information finns i [utvärdera effekten av en ny Azure-princip](./evaluate-impact.md).

### <a name="enable-remediation-tasks"></a>Aktivera reparations åtgärder

Om valideringen av tilldelningen uppfyller förväntningarna är nästa steg att validera reparation.
Principer som använder antingen [deployIfNotExists](./effects.md#deployifnotexists) eller [Modify](./effects.md#modify) kan omvandlas till en reparations uppgift och korrigera resurser från ett icke-kompatibelt tillstånd.

Det första steget för att göra detta är att bevilja princip tilldelningen den roll tilldelning som definierats i princip definitionen. Den här roll tilldelningen ger princip tilldelningen hanterad identitet tillräckligt med behörighet för att göra nödvändiga ändringar för att göra resursen kompatibel.

När princip tilldelningen har rätt behörighet använder du princip-SDK: n för att utlösa en reparations aktivitet mot en uppsättning resurser som är kända för att vara inkompatibla. Tre tester bör utföras mot dessa åtgärdade uppgifter innan du fortsätter:

- Verifiera att reparations uppgiften har slutförts
- Kör princip utvärdering för att se att dessa resultat för efterlevnad av princip uppdateras som förväntat
- Kör en miljö enhets test mot resurserna direkt för att verifiera att deras egenskaper har ändrats

Genom att testa både de uppdaterade utvärderings resultaten och miljön ger du en bekräftelse på att reparations aktiviteterna har ändrat vad som förväntades och att princip definitionen visade att ändringen förväntades.

### <a name="update-to-enforced-assignments"></a>Uppdatera till framtvingade tilldelningar

När alla verifierings grindar har slutförts uppdaterar du tilldelningen för att använda **enforcementMode** av _aktive rad_. Den här ändringen bör inlednings vis göras i samma miljö som från produktionen. När miljön har verifierats som fungerar som förväntat, kommer ändringen att begränsas till att omfatta nästa miljö och så vidare tills principen distribueras till produktions resurser.

## <a name="process-integrated-evaluations"></a>Bearbeta integrerade utvärderingar

Det allmänna arbets flödet för princip som kod är för att utveckla och distribuera principer och initiativ till en miljö i stor skala. Princip utvärderingen bör dock vara en del av distributions processen för alla arbets flöden som distribuerar eller skapar resurser i Azure, till exempel att distribuera program eller köra Resource Manager-mallar för att skapa infrastruktur.

I dessa fall, när distributionen av program eller infrastrukturen görs i en test prenumeration eller resurs grupp, bör princip utvärderingen utföras för den omfattningen för att kontrol lera valideringen av alla befintliga principer och initiativ. Även om de kan konfigureras som **enforcementMode** _inaktiverade_ i en sådan miljö, är det bra att veta tidigt om en program-eller infrastruktur distribution strider mot princip definitionerna tidigt. Den här princip utvärderingen bör därför vara ett steg i dessa arbets flöden och misslyckade distributioner som skapar icke-kompatibla resurser.

## <a name="review"></a>Granska

Den här artikeln beskriver det allmänna arbets flödet för policy som kod och även var princip utvärderingen ska ingå i andra distributions arbets flöden. Det här arbets flödet kan användas i alla miljöer som har stöd för skriptbaserade steg och automatisering baserat på utlösare.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [princip definitions strukturen](./definition-structure.md).
- Lär dig mer om [princip tilldelnings strukturen](./assignment-structure.md).
- Lär dig att [program mässigt skapa principer](../how-to/programmatically-create.md).
- Lär dig hur du [hämtar efterlevnadsprinciper](../how-to/get-compliance-data.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](../how-to/remediate-resources.md).
- Granska en hanterings grupp med [organisera dina resurser med Azures hanterings grupper](../../management-groups/overview.md).