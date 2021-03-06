---
title: Guide för undersökning av IoT-enheter | Azure Security Center Microsoft Docs
description: I den här guiden beskrivs hur du använder Azure Security Center för IoT för att undersöka en misstänkt IoT-enhet med hjälp av Log Analytics.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: b18b48ae-b445-48f8-9ac0-365d6e065b64
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 8d2fe8d63c7ece6f3b3426d8fc5a3454a61826f8
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596252"
---
# <a name="investigate-a-suspicious-iot-device"></a>Undersök en misstänkt IoT-enhet

Azure Security Center för IoT service-aviseringar ger tydliga indikationer när IoT-enheter misstänks delta i misstänkta aktiviteter eller när det finns indikationer om att en enhet har komprometterats. 

I den här hand boken använder du de undersöknings förslag som du kan använda för att fastställa potentiella risker för din organisation, bestämma hur du ska åtgärda och upptäcka de bästa sätten att förhindra liknande attacker i framtiden.  

> [!div class="checklist"]
> * Hitta enhets data
> * Undersök med hjälp av KQL-frågor


## <a name="how-can-i-access-my-data"></a>Hur kan jag komma åt mina data?

Som standard lagrar Azure Security Center för IoT dina säkerhets aviseringar och rekommendationer i Log Analytics-arbetsytan. Du kan också välja att lagra dina rå säkerhets data.

Hitta din Log Analytics-arbetsyta för data lagring:

1. Öppna din IoT-hubb, 
1. Under **säkerhet**klickar du på **Översikt**och väljer sedan **Inställningar**.
1. Ändra konfigurations information för Log Analytics arbets ytan. 
1. Klicka på **Spara**. 

Följande konfiguration, gör följande för att komma åt data som lagras i din Log Analytics-arbetsyta:

1. Markera och klicka på en Azure Security Center för IoT-avisering i IoT Hub. 
1. Klicka på **ytterligare undersökning**. 
1. Välj **om du vill se vilka enheter som har den här aviseringen Klicka här och visa kolumnen DeviceID**.

## <a name="investigation-steps-for-suspicious-iot-devices"></a>Gransknings steg för misstänkta IoT-enheter

Om du vill visa insikter och rå data om dina IoT-enheter går du till din Log Analytics-arbetsyta [för att komma åt dina data](#how-can-i-access-my-data).

Se exempel på KQL-frågorna nedan för att komma igång med att undersöka aviseringar och aktiviteter på enheten.

### <a name="related-alerts"></a>Relaterade aviseringar

Om du vill ta reda på om andra aviseringar har utlösts runt samma tid använder du följande KQL-fråga:

  ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityAlert
  | where ExtendedProperties contains device and ResourceId contains tolower(hub)
  | project TimeGenerated, AlertName, AlertSeverity, Description, ExtendedProperties
  ```

### <a name="users-with-access"></a>Användare med åtkomst

Om du vill ta reda på vilka användare som har åtkomst till den här enheten använder du följande KQL-fråga: 

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "LocalUsers"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     GroupNames=extractjson("$.GroupNames", EventDetails, typeof(string)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string))
  | summarize FirstObserved=min(TimestampLocal) by GroupNames, UserName
 ```
Använd dessa data för att identifiera: 
- Vilka användare har åtkomst till enheten?
- Har användarna med åtkomst den förväntade behörighets nivån?

### <a name="open-ports"></a>Öppna portar

Använd följande KQL-fråga för att ta reda på vilka portar i enheten som används eller används: 

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ListeningPorts"
     and extractjson("$.LocalPort", EventDetails, typeof(int)) <= 1024 // avoid short-lived TCP ports (Ephemeral)
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Protocol=extractjson("$.Protocol", EventDetails, typeof(string)),
     LocalAddress=extractjson("$.LocalAddress", EventDetails, typeof(string)),
     LocalPort=extractjson("$.LocalPort", EventDetails, typeof(int)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     RemotePort=extractjson("$.RemotePort", EventDetails, typeof(string))
  | summarize MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), AllowedRemoteIPAddress=makeset(RemoteAddress), AllowedRemotePort=makeset(RemotePort) by Protocol, LocalPort
 ```

Använd dessa data för att identifiera:
- Vilka lyssnar Sockets är aktiva på enheten?
- Ska de lyssnande socketarna som för närvarande är aktiva tillåtas?
- Finns det några misstänkta fjärradresser anslutna till enheten?

### <a name="user-logins"></a>Användar inloggningar

Använd följande KQL-fråga om du vill hitta användare som har loggat in på enheten: 
 
 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "Login"
     // filter out local, invalid and failed logins
     and EventDetails contains "RemoteAddress"
     and EventDetails !contains '"RemoteAddress":"127.0.0.1"'
     and EventDetails !contains '"UserName":"(invalid user)"'
     and EventDetails !contains '"UserName":"(unknown user)"'
     //and EventDetails !contains '"Result":"Fail"'
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string)),
     LoginHandler=extractjson("$.Executable", EventDetails, typeof(string)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     Result=extractjson("$.Result", EventDetails, typeof(string))
  | summarize CntLoginAttempts=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), CntIPAddress=dcount(RemoteAddress), IPAddress=makeset(RemoteAddress) by UserName, Result, LoginHandler
 ```

Använd frågeresultaten för att identifiera:
- Vilka användare har loggat in på enheten?
- Ska de användare som är inloggade att logga in?
- Var de användare som loggade in Connect från förväntade eller oväntade IP-adresser?
  
### <a name="process-list"></a>Process lista

Om du vill ta reda på om process listan är förväntat använder du följande KQL-fråga: 

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ProcessCreate"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Executable=extractjson("$.Executable", EventDetails, typeof(string)),
     UserId=extractjson("$.UserId", EventDetails, typeof(string)),
     CommandLine=extractjson("$.CommandLine", EventDetails, typeof(string))
  | join kind=leftouter (
     // user UserId details
     SecurityIoTRawEvent
     | where
        DeviceId == device and AssociatedResourceId contains tolower(hub)
        and RawEventName == "LocalUsers"
     | project
        UserId=extractjson("$.UserId", EventDetails, typeof(string)),
        UserName=extractjson("$.UserName", EventDetails, typeof(string))
     | distinct UserId, UserName
  ) on UserId
  | extend UserIdName = strcat("Id:", UserId, ", Name:", UserName)
  | summarize CntExecutions=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), ExecutingUsers=makeset(UserIdName), ExecutionCommandLines=makeset(CommandLine) by Executable
```

Använd frågeresultaten för att identifiera:

- Fanns det några misstänkta processer som kördes på enheten?
- Genomförde processer av lämpliga användare?
- Innehåller alla kommando rads körningar korrekta och förväntade argument?

## <a name="next-steps"></a>Nästa steg

När du har undersökt en enhet och fått en bättre förståelse för dina risker kanske du vill överväga att [Konfigurera anpassade aviseringar](quickstart-create-custom-alerts.md) för att förbättra din position för IoT-lösningar. Om du inte redan har en enhets agent kan du överväga att [distribuera en säkerhets agent](how-to-deploy-agent.md) eller [ändra konfigurationen för en befintlig enhets agent](how-to-agent-configuration.md) för att förbättra resultaten. 
