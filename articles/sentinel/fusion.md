---
title: Avancerad attack identifiering i Multistage i Azure Sentinel
description: Använd fusions teknik i Azure Sentinel för att minska varnings utmattningen och skapa åtgärds bara incidenter som baseras på avancerad attack identifiering i multiskede.
services: sentinel
documentationcenter: na
author: cabailey
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 9/24/2019
ms.author: cabailey
ms.openlocfilehash: e6ddb1b01b705d2a7857682bd84e9482e064a8db
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240075"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Avancerad attack identifiering i Multistage i Azure Sentinel

Genom att använda fusions teknik som är baserad på maskin inlärning kan Azure Sentinel automatiskt identifiera angrepp i flera steg genom att kombinera avvikande beteenden och misstänkta aktiviteter som observeras i olika steg i Kill-kedjan. Azure Sentinel genererar sedan incidenter som annars skulle vara svåra att fånga. Dessa incidenter har två eller flera aviseringar eller aktiviteter. Som design är dessa incidenter små volymer, hög kvalitet och hög allvarlighets grad.

Den här identifieringen är anpassad för din miljö och minskar bara falsk positiv taxa men kan också identifiera attacker med begränsad eller saknad information.

Mer information om aviseringar för de scenarier som stöds finns i avsnittet [scenarier som stöds för sårbarhets identifiering i Multistage](#scenarios-supported-for-advanced-multistage-attack-detection) på den här sidan.

## <a name="configuration-for-advanced-multistage-attack-detection"></a>Konfiguration för avancerad attack identifiering i Multistage

Den här identifieringen är aktive rad som standard i Azure Sentinel. Om du vill kontrol lera statusen eller inaktivera det kanske eftersom du använder en annan lösning för att skapa incidenter baserade på flera aviseringar, Använd följande instruktioner:

1. Om du inte redan gjort det loggar du in på [Azure Portal](https://portal.azure.com).

2. Navigera till > **konfigurations** > **analys** för **Azure Sentinel**

3. Välj **aktiva regler** och leta upp **Avancerad sårbarhets identifiering i Multistage** i kolumnen **namn** . Kontrol lera kolumnen **status** för att bekräfta om identifieringen är aktive rad eller inaktive rad.

4. Om du vill ändra status väljer du den här posten och på bladet **Avancerad sårbarhets identifiering i Multistage** väljer du **Redigera**.

5. På bladet **guiden Skapa regel** väljs ändringen av status automatiskt åt dig, så välj **nästa: Granska**och **Spara**. 

Regelmallar gäller inte för den avancerade identifieringen av skadlig kod.

## <a name="scenarios-supported-for-advanced-multistage-attack-detection"></a>Scenarier som stöds för avancerad attack identifiering i Multistage

Med hjälp av avancerad attack identifiering i flera steg stöder Azure Sentinel följande scenarier som kombinerar avvikelser från Azure Active Directory Identity Protection och Microsoft Cloud App Security:

- [Omöjlig resa till ovanlig plats följt av avvikande Office 365-aktivitet](#impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity)
- [Inloggnings aktivitet för okänd plats följt av avvikande Office 365-aktivitet](#sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity)
- [Inloggnings aktivitet från angripen enhet följt av avvikande Office 365-aktivitet](#sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity)
- [Inloggnings aktivitet från anonym IP-adress följt av avvikande Office 365-aktivitet](#sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity)
- [Inloggnings aktivitet från användare med läckta autentiseringsuppgifter följt av avvikande Office 365-aktivitet](#sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity)

Du måste ha [Azure AD Identity Protection data koppling](connect-azure-ad-identity-protection.md) och de [Cloud App Security](connect-cloud-app-security.md) anslutningarna har kon figurer ATS.

I beskrivningarna som följer visar Azure Sentinel det faktiska värdet från dina data som visas på den här sidan som variabler inom hakparenteser. Till exempel det faktiska visnings namnet för ett konto i \<stället för *konto namnet*> och det \<faktiska antalet i stället för *nummer*>.

### <a name="impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity"></a>Omöjlig resa till ovanlig plats följt av avvikande Office 365-aktivitet

Det finns sju möjliga Azure Sentinel-incidenter som kombinerar omöjlig resa till ovanlig plats varningar från Azure AD Identity Protection och avvikande Office 365-aviseringar som genererats av Microsoft Cloud App Security:

- **Omöjlig resa till ovanlig-platser som leder till Office 365 Mailbox exfiltrering**
    
    Den här aviseringen är en indikation på en inloggnings händelse \<efter *konto namn*> från en omöjlig resa \<till *plats*>, en ovanlig plats, följt av en misstänkt vidarebefordrings regel för Inkorgen angavs i en användares inkorg.
    
    Detta kan tyda på att kontot komprometteras och att post lådan används för att stjäla information från din organisation. Namnet på \<användar *kontot*> skapade eller uppdaterade en vidarebefordrings regel för Inkorgen som vidarebefordrar alla inkommande e- \<postmeddelanden till *e-postadressen*för den externa adressen >.

- **Omöjlig resa till ovanlig platser som leder till misstänkt administrativ aktivitet i Cloud App**
    
    Den här aviseringen är en indikation på en inloggnings händelse \<efter *konto namn*> från en omöjlig resa \<till *plats*>, en ovanlig plats.
    
    Sedan >s konto \< *namnet*s över \< *nummer*> administrativa aktiviteter i en enda session.

- **Omöjlig resa till ovanlig platser som leder till Mass borttagning av filer**
    
    Den här aviseringen är en indikation på en inloggnings händelse \<efter *konto namn*> \<till *plats*>, en ovanlig plats. 
    
    Sedan > \<borttaget *antal*> unika filer i en enda session. \<

- **Omöjlig resa till ovanlig-platser som leder till Mass nedladdning av filer**
    
    Den här aviseringen är en indikation på en inloggnings händelse \<efter *konto namn*> från en omöjlig resa \<till *plats*>, en ovanlig plats. 
    
    Sedan > hämtas konto \< *namnet*som hämtats över \< *antalet*> unika filer i en enda session.

- **Omöjlig resa till ovanlig-platser som leder till Office 365-personifiering**
    
    Den här aviseringen är en indikation på en inloggnings händelse \<efter *konto namn*> från en omöjlig resa \<till *plats*>, en ovanlig plats. 
    
    Därefter utfördes konto \< *namnet*> ett ovanligt belopp (\<*antalet aktiviteter*>) för personifiering i en enda session.

- **Omöjlig resa till ovanlig platser som leder till Mass fildelning**
    
    Den här aviseringen är en indikation på en inloggnings händelse \<efter *konto namn*> från en omöjlig resa \<till *plats*>, en ovanlig plats. 
    
    Därefter \<> konto *namnet*delat över \< *antalet*> unika filer i en enda session.

- **Omöjlig resa till ovanlig-platser som leder till utpressnings troprogram i Cloud App**
    
    Den här aviseringen är en indikation på en inloggnings händelse \<efter *konto namn*> från en omöjlig resa \<till *plats*>, en ovanlig plats. 
    
    \<Sedan har konto *namnet*> uppladdat \< *antalet*> filer och tagit bort totalt \< *antal*>-filer. 
    
    Det här aktivitets mönstret är en indikation på en potentiell utpressnings attack.


### <a name="sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity"></a>Inloggnings aktivitet för okänd plats följt av avvikande Office 365-aktivitet

Det finns sju möjliga Azure Sentinel-incidenter som kombinerar inloggnings aktivitet för okända plats varningar från Azure AD Identity Protection och avvikande Office 365-aviseringar som genererats av Microsoft Cloud App Security.

- **Inloggnings händelse från en okänd plats som leder till Exchange Online-brevlåda exfiltrering**
    
    Den här aviseringen är en indikation på en inloggnings händelse \<efter *konto namn*> \<från *plats*>, en okänd plats, följt av en misstänkt vidarebefordrings regel för Inkorgen angavs i en användares inkorg.
    
    Detta kan tyda på att kontot komprometteras och att post lådan används för att stjäla information från din organisation. Namnet på \<användar *kontot*> skapade eller uppdaterade en vidarebefordrings regel för Inkorgen som vidarebefordrar alla inkommande e- \<postmeddelanden till *e-postadressen*för den externa adressen >. 

- **Inloggnings händelse från en okänd plats som leder till misstänkt administrativ aktivitet i Cloud App**
    
    Den här aviseringen är en indikation på en inloggnings händelse \<efter *konto namn*> \<från *plats*>, en okänd plats. 
    
    Sedan >s konto \< *namnet*som utfördes över \< *antalet*> administrativa aktiviteter i en enda session.

- **Inloggnings händelse från en okänd plats som leder till Mass borttagning av filer**
    
    Den här aviseringen är en indikation på en inloggnings händelse \<efter *konto namn*> \<från *plats*>, en okänd plats. 
    
    Sedan > \<borttaget *antal*> unika filer i en enda session. \<

- **Inloggnings händelse från en okänd plats som leder till Mass hämtning av filer**
    
    Den här aviseringen är en indikation på en inloggnings händelse \<efter *konto namn*> \<från *plats*>, en okänd plats. 
    
    Sedan > hämtas konto \< *namnet*som hämtats över \< *antalet*> unika filer i en enda session.

- **Inloggnings händelse från en okänd plats som leder till Office 365-personifiering**
    
    Den här aviseringen är en indikation på en inloggnings händelse \<efter *konto namn*> \<från *plats*>, en okänd plats.
    
    Sedan > personifieras \<konto *namnet*personifierat över \< *antalet*> olika konton i en enda session.

- **Inloggnings händelse från en okänd plats som leder till Mass delning av filer**
    
    Den här aviseringen är en indikation på en inloggnings händelse \<efter *konto namn*> \<från *plats*>, en okänd plats. 
    
    Därefter \<> konto *namnet*delat över \< *antalet*> unika filer i en enda session.

- **Inloggnings händelse från en okänd plats som leder till utpressnings troprogram i Cloud App**
    
    Den här aviseringen är en indikation på en inloggnings händelse \<efter *konto namn*> \<från *plats*>, en okänd plats. 
    
    \<Sedan har konto *namnet*> uppladdat \< *antalet*> filer och tagit bort totalt \< *antal*>-filer. 
    
    Det här aktivitets mönstret är en indikation på en potentiell utpressnings attack.

### <a name="sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity"></a>Inloggnings aktivitet från angripen enhet följt av avvikande Office 365-aktivitet

Det finns sju möjliga Azure Sentinel-incidenter som kombinerar inloggnings aktivitet från infekterade enhets aviseringar från Azure AD Identity Protection och avvikande Office 365-aviseringar som genererats av Microsoft Cloud App Security:

- **Inloggnings händelse från en infekterad enhet som leder till Office 365 Mailbox exfiltrering**
    
    Den här aviseringen är en indikation på en inloggnings händelse \<efter *konto namn*> från en enhet som potentiellt infekteras av skadlig kod, följt av en misstänkt vidarebefordrings regel för Inkorgen angavs i en användares inkorg.
    
    Detta kan tyda på att kontot komprometteras och att post lådan används för att stjäla information från din organisation. Namnet på \<användar *kontot*> skapade eller uppdaterade en vidarebefordrings regel för Inkorgen som vidarebefordrar alla inkommande e- \<postmeddelanden till *e-postadressen*för den externa adressen >. 

- **Inloggnings händelse från en infekterad enhet som leder till misstänkt administrativ aktivitet i Cloud App**
    
    Den här aviseringen är en indikation på en inloggnings händelse \<efter *konto namn*> från en enhet som potentiellt infekteras av skadlig kod.
    
    Sedan >s konto \< *namnet*som utfördes över \< *antalet*> administrativa aktiviteter i en enda session.

- **Inloggnings händelse från en infekterad enhet som leder till Mass borttagning av filer**
    
    Den här aviseringen är en indikation på en inloggnings händelse \<efter *konto namn*> från en enhet som potentiellt infekteras av skadlig kod. 
    
    Sedan > \<borttaget *antal*> unika filer i en enda session. \<

- **Inloggnings händelse från en infekterad enhet som leder till Mass fil hämtning**
    
    Den här aviseringen är en indikation på en inloggnings händelse \<efter *konto namn*> från en enhet som potentiellt infekteras av skadlig kod. 
    
    Sedan > hämtas konto \< *namnet*som hämtats över \< *antalet*> unika filer i en enda session.

- **Inloggnings händelse från en infekterad enhet som leder till Office 365-personifiering**
    
    Den här aviseringen är en indikation på en inloggnings händelse \<efter *konto namn*> från en enhet som potentiellt infekteras av skadlig kod. 
    
    Sedan > personifieras \<konto *namnet*personifierat över \< *antalet*> olika konton i en enda session.

- **Inloggnings händelse från en infekterad enhet som leder till Mass fil delning**
    
    Den här aviseringen är en indikation på en inloggnings händelse \<efter *konto namn*> från en enhet som potentiellt infekteras av skadlig kod. 
    
    Därefter \<> konto *namnet*delat över \< *antalet*> unika filer i en enda session.

- **Inloggnings händelse från en infekterad enhet som leder till utpressnings troprogram i Cloud App**
    
    Den här aviseringen är en indikation på en inloggnings händelse \<efter *konto namn*> från en enhet som potentiellt infekteras av skadlig kod. 
    
    \<Sedan har konto *namnet*> uppladdat \< *antalet*> filer och tagit bort totalt \< *antal*>-filer. 
    
    Det här aktivitets mönstret är en indikation på en potentiell utpressnings attack.

### <a name="sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity"></a>Inloggnings aktivitet från anonym IP-adress följt av avvikande Office 365-aktivitet

Det finns sju möjliga Azure Sentinel-incidenter som kombinerar inloggnings aktivitet från anonyma IP-aviseringar från Azure AD Identity Protection och avvikande Office 365-aviseringar som genererats av Microsoft Cloud App Security:

- **Inloggnings händelse från en anonym IP-adress som leder till Office 365 Mailbox exfiltrering**
    
    Den här aviseringen är en indikation på en inloggnings händelse \<efter *konto namn*> från en IP-adress \<för anonym proxy IP- *adress >,* följt av en misstänkt vidarebefordrings regel för Inkorgen angavs i en användares inkorg.
    
    Detta kan tyda på att kontot komprometteras och att post lådan används för att stjäla information från din organisation. Namnet på \<användar *kontot*> skapade eller uppdaterade en vidarebefordrings regel för Inkorgen som vidarebefordrar alla inkommande e- \<postmeddelanden till *e-postadressen*för den externa adressen >. 

- **Inloggnings händelse från en anonym IP-adress som leder till misstänkt administrativ aktivitet i Cloud App**
    
    Den här aviseringen är en indikation på en inloggnings händelse \<efter *konto namn*> från en IP-adress \<med anonyma IP- *adresser >.* 
    
    Sedan >s konto \< *namnet*som utfördes över \< *antalet*> administrativa aktiviteter i en enda session.

- **Inloggnings händelse från en anonym IP-adress som leder till Mass borttagning av filer**
    
    Den här aviseringen är en indikation på en inloggnings händelse \<efter *konto namn*> från en IP-adress \<med anonyma IP- *adresser >.* 
    
    Sedan > \<borttaget *antal*> unika filer i en enda session. \<

- **Inloggnings händelse från en anonym IP-adress som leder till Mass nedladdning av filer**
    
    Den här aviseringen är en indikation på en inloggnings händelse \<efter *konto namn*> från en IP-adress \<med anonyma IP- *adresser >.* 
    
    Sedan > hämtas konto \< *namnet*som hämtats över \< *antalet*> unika filer i en enda session.

- **Inloggnings händelse från en anonym IP-adress som leder till Office 365-personifiering**
    
    Den här aviseringen är en indikation på en inloggnings händelse \<efter *konto namn*> från en IP-adress \<med anonyma IP- *adresser >.* 
    
    Sedan > personifieras \<konto *namnet*personifierat över \< *antalet*> olika konton i en enda session.

- **Inloggnings händelse från en anonym IP-adress som leder till Mass delning av filer**
    
    Den här aviseringen är en indikation på en inloggnings händelse \<efter *konto namn*> från en IP-adress \<med anonyma IP- *adresser >.* 
    
    Därefter \<> konto *namnet*delat över \< *antalet*> unika filer i en enda session.

- **Inloggnings händelse från en anonym IP-adress till utpressnings program vara i Cloud App**
    
    Den här aviseringen är en indikation på en inloggnings händelse \<efter *konto namn*> från en IP-adress \<med anonyma IP- *adresser >.* 
    
    \<Sedan har konto *namnet*> uppladdat \< *antalet*> filer och tagit bort totalt \< *antal*>-filer. 
    
    Det här aktivitets mönstret är en indikation på en potentiell utpressnings attack.

### <a name="sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity"></a>Inloggnings aktivitet från användare med läckta autentiseringsuppgifter följt av avvikande Office 365-aktivitet

Det finns sju möjliga Azure Sentinel-incidenter som kombinerar inloggnings aktivitet från användare med läckta autentiseringsuppgifter från Azure AD Identity Protection och avvikande Office 365-aviseringar som genererats av Microsoft Cloud App Security:

- **Inloggnings händelse från användare med läckta autentiseringsuppgifter som leder till Office 365 Mailbox exfiltrering**
    
    Den här varningen anger att inloggnings händelsen efter \< *konto namn*> använt läckta autentiseringsuppgifter följt av en misstänkt vidarebefordrings regel för Inkorgen angavs i en användares inkorg. 
    
    Detta kan tyda på att kontot komprometteras och att post lådan används för att stjäla information från din organisation. Namnet på \<användar *kontot*> skapade eller uppdaterade en vidarebefordrings regel för Inkorgen som vidarebefordrar alla inkommande e- \<postmeddelanden till *e-postadressen*för den externa adressen >. 

- **Inloggnings händelse från användare med läckta autentiseringsuppgifter som leder till misstänkt administrativ aktivitet i Cloud App**
    
    Den här varningen anger att inloggnings händelsen efter \< *konto namn*> använt läckta autentiseringsuppgifter.
    
    Sedan >s konto \< *namnet*som utfördes över \< *antalet*> administrativa aktiviteter i en enda session.

- **Inloggnings händelse från användare med läckta autentiseringsuppgifter som leder till Mass borttagning av filer**
    
    Den här varningen anger att inloggnings händelsen efter \< *konto namn*> använt läckta autentiseringsuppgifter.
    
    Sedan > \<borttaget *antal*> unika filer i en enda session. \<

- **Inloggnings händelse från användare med läckta autentiseringsuppgifter som leder till Mass nedladdning av filer**
    
    Den här varningen anger att inloggnings händelsen efter \< *konto namn*> använt läckta autentiseringsuppgifter.
    
    Sedan > hämtas konto \< *namnet*som hämtats över \< *antalet*> unika filer i en enda session.

- **Inloggnings händelse från användare med läckta autentiseringsuppgifter som leder till Office 365-personifiering**
    
    Den här varningen anger att inloggnings händelsen efter \< *konto namn*> använt läckta autentiseringsuppgifter. 
    
    Sedan > personifieras \<konto *namnet*personifierat över \< *antalet*> olika konton i en enda session.

- **Inloggnings händelse från användare med läckta autentiseringsuppgifter som leder till Mass delning av filer**
    
    Den här varningen anger att inloggnings händelsen efter \< *konto namn*> använt läckta autentiseringsuppgifter.
    
    Därefter \<> konto *namnet*delat över \< *antalet*> unika filer i en enda session.

- **Inloggnings händelse från användare med läckta autentiseringsuppgifter till utpressnings program vara i Cloud App**
    
    Den här varningen anger att inloggnings händelsen efter \< *konto namn*> använt läckta autentiseringsuppgifter. 
    
    \<Sedan har konto *namnet*> uppladdat \< *antalet*> filer och tagit bort totalt \< *antal*>-filer. 
    
    Det här aktivitets mönstret är en indikation på en potentiell utpressnings attack.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig mer om avancerad sårbarhets identifiering i flera steg kan du vara intresse rad av följande snabb start för att lära dig hur du får insyn i dina data och potentiella hot: [Kom igång med Azure Sentinel](quickstart-get-visibility.md).

Om du är redo att undersöka de incidenter som skapas för dig, kan du läsa följande självstudie: [Undersök incidenter med Azure Sentinel](tutorial-investigate-cases.md).

