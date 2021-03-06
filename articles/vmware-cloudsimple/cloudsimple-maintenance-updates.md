---
title: Azure VMware-lösning av CloudSimple-CloudSimple-underhåll och uppdateringar
description: Beskriver CloudSimple-tjänstens process för schemalagt underhåll och uppdateringar
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 92f02c0abef6755213d4c73189c7e0a593867ef6
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877939"
---
# <a name="cloudsimple-maintenance-and-updates"></a>Underhåll och uppdateringar av CloudSimple

Den privata moln miljön har utformats för att inte ha någon enskild felpunkt.

* ESXi-kluster konfigureras med vSphere hög tillgänglighet (HA). Det finns minst en reserv nod för återhämtning i klustret.
* Redundant primär lagring tillhandahålls av virtuellt San, vilket kräver minst tre noder för att ge skydd mot ett enskilt haveri. Virtuellt SAN kan konfigureras för att ge högre återhämtning för större kluster.
* vCenter-, PSC-och NSX Manager-VM: ar konfigureras med RAID-10-lagring för att skydda mot lagrings problem. De virtuella datorerna skyddas mot nod-/nätverks problem av vSphere HA.
* ESXi-värdar har redundanta fläktar och nätverkskort.
* Växlarna TOR och rygg har kon figurer ATS i HA par för att ge återhämtning.

CloudSimple övervakar kontinuerligt följande virtuella datorer för drift tid och tillgänglighet och ger tillgänglighets service avtal:

* ESXi-värdar
* vCenter
* PSC
* NSX Manager

CloudSimple övervakar också följande kontinuerligt för problem:

* Hård diskar
* Fysiska NIC-portar
* Servrar
* Fläktar
* Befogenhet
* Parametrar
* Växla portar

Om en disk eller nod Miss lyckas läggs en ny nod automatiskt till i det berörda VMware-klustret för att få tillbaka tillståndet omedelbart.

CloudSimple säkerhetskopierar, underhåller och uppdaterar dessa VMware-element i de privata molnen:

* ESXi
* vCenter Platform-tjänster
* Kontrollenhet
* vSAN
* NSX

## <a name="back-up-and-restore"></a>Säkerhetskopiera och återställa

CloudSimple säkerhets kopiering inkluderar:

* Stegvisa säkerhets kopieringar av vCenter-, PSC-och DVS-regler.
* vCenter inbyggda API: er för att säkerhetskopiera komponenter i program skiktet.
* Automatisk säkerhets kopiering före uppdatering eller uppgradering av VMware Management-programvaran.
* vCenter Data Encryption vid källan innan data överförs via en TLS 1.2-krypterad kanal till Azure. Data lagras i en Azure-Blob där den replikeras mellan regioner.

Du kan begära en återställning genom att öppna en [supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="maintenance"></a>Underhåll

CloudSimple gör flera typer av planerat underhåll.

### <a name="backendinternal-maintenance"></a>Server del/internt underhåll

Det här underhållet omfattar vanligt vis omkonfigurering av fysiska till gångar eller installation av program varu uppdateringar. Den påverkar inte normal förbrukning av de till gångar som servas. Med redundanta nätverkskort som ska gå till varje fysiskt rack påverkas inte normala nätverks trafik och privata moln åtgärder. Du kanske upptäcker att en prestanda påverkas endast om organisationen förväntar sig att använda den fullständiga redundanta bandbredden under underhålls intervallet.

### <a name="cloudsimple-portal-maintenance"></a>Underhåll av CloudSimple-portalen

Vissa begränsade avbrott i tjänsten krävs när kontroll planet eller infrastrukturen för CloudSimple uppdateras. För närvarande kan underhålls intervall vara så frekventa som ofta en gång per månad. Frekvensen förväntas nekas över tid. CloudSimple tillhandahåller ett meddelande om Portal underhåll och håller intervallet så kort som möjligt. Följande tjänster fortsätter att fungera utan påverkan under ett underhålls intervall för portalen:

* Hanterings plan och program för VMware
* vCenter-åtkomst
* Alla nätverk och lagring
* All Azure-trafik

### <a name="vmware-infrastructure-maintenance"></a>Underhåll av VMware-infrastruktur

Ibland är det nödvändigt att göra ändringar i konfigurationen av VMware-infrastrukturen.  För närvarande kan dessa intervall inträffa var 1-2 månad, men frekvensen förväntas avböjas över tid. Den här typen av underhåll kan vanligt vis utföras utan att den normala förbrukningen av CloudSimple-tjänsterna avbryts. Under ett underhålls intervall för VMware fortsätter följande tjänster att fungera utan påverkan:

* Hanterings plan och program för VMware
* vCenter-åtkomst
* Alla nätverk och lagring
* All Azure-trafik

## <a name="updates-and-upgrades"></a>Uppdateringar och uppgraderingar

CloudSimple ansvarar för livs cykel hantering av VMware-programvara (ESXi, vCenter, PSC och NSX) i det privata molnet.

Program uppdateringar omfattar:

* **Korrigeringar**. Säkerhets korrigeringar eller fel korrigeringar som publicerats av VMware.
* **Uppdateringar**. Lägre versions ändring av en VMware stack-komponent.
* **Uppgraderingar**. Huvud versions ändring av en VMware stack-komponent.

CloudSimple testar en kritisk säkerhets korrigering så snart den blir tillgänglig från VMware. Enligt SLA samlar CloudSimple ut säkerhets korrigeringen till privata moln miljöer inom en vecka.

CloudSimple tillhandahåller kvartals Visa uppdateringar av VMware-programkomponenter. När en ny huvud version av VMware-programvaran är tillgänglig fungerar CloudSimple med kunder för att samordna en lämplig underhålls period för uppgradering.

## <a name="next-steps"></a>Nästa steg

[Säkerhetskopiera virtuella arbets belastnings datorer med Veeam](backup-workloads-veeam.md)
