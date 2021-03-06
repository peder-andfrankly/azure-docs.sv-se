---
title: Översikt över Azure Kubernetes service (AKS)-diagnostik
description: Lär dig mer om att själv diagnostisera kluster i Azure Kubernetes-tjänsten.
services: container-service
author: yunjchoi
ms.service: container-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: yunjchoi
ms.openlocfilehash: 69ae6766414dbe533500860b01852ee8d25c3a1e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513918"
---
# <a name="azure-kubernetes-service-aks-diagnostics-overview"></a>Översikt över Azure Kubernetes service (AKS)-diagnostik

Fel sökning av problem med Azure Kubernetes service (AKS) är en viktig del av att underhålla klustret, särskilt om klustret kör verksamhets kritiska arbets belastningar. AKS-diagnostik är en intelligent, själv diagnos upplevelse som hjälper dig att identifiera och lösa problem i klustret. AKS-diagnostik är molnbaserad och du kan använda den utan extra konfigurations-eller fakturerings kostnader.

## <a name="open-aks-diagnostics"></a>Öppna AKS-diagnostik

Åtkomst till AKS-diagnostik:

- Navigera till ditt Kubernetes-kluster i [Azure Portal](https://portal.azure.com).
- Klicka på **diagnostisera och lös problem** i det vänstra navigerings fönstret, som öppnar AKS-diagnostik.
- Välj en kategori som bäst beskriver problemet med ditt kluster genom att använda nyckelorden på sidan Start sida eller ange ett nyckelord som bäst beskriver problemet i Sök fältet, till exempel _klusternoder_.

![Start](./media/concepts-diagnostics/aks-diagnostics-homepage.png)

## <a name="view-a-diagnostic-report"></a>Visa en diagnostisk rapport

När du har klickat på en kategori kan du Visa en diagnostisk rapport som är speciell för klustret. Diagnostiska rapporter anropar intelligentt om det finns något problem i klustret med status ikoner. Du kan öka detalj nivån för varje ämne genom att klicka på **Mer information** för att se detaljerad beskrivning av problemet, rekommenderade åtgärder, länkar till användbara dokument, relaterade mått och loggnings data. Diagnostiska rapporter skapas intelligent baserat på klustrets aktuella tillstånd när du har kört flera olika kontroller. Diagnostiska rapporter kan vara ett användbart verktyg för att hitta problem med klustret och hitta nästa steg för att lösa problemet.

![Diagnostisk rapport](./media/concepts-diagnostics/diagnostic-report.png)

![Utökad diagnostisk rapport](./media/concepts-diagnostics/node-issues.png)

## <a name="cluster-insights"></a>Kluster insikter

Följande diagnostiska kontroller är tillgängliga i **kluster insikter**.

### <a name="cluster-node-issues"></a>Problem med klusternod

Problem med klusternoder söker efter tjänstrelaterade problem som kan orsaka att klustret beter sig.

- Problem med nods beredskap
- Nodfel
- Otillräckliga resurser
- Nod saknar IP-konfiguration
- Node CNI-problem
- Noden hittades inte
- Avstängning av nod
- Autentiseringsfel för nod
- Node Kube-proxy inaktuell

### <a name="create-read-update--delete-operations"></a>Skapa, läsa, uppdatera & ta bort åtgärder

CRUD-åtgärder söker efter CRUD-åtgärder som kan orsaka problem i klustret.

- Fel vid borttagning av Använd undernät
- Åtgärds fel vid borttagning av nätverks säkerhets grupp
- Åtgärds fel vid borttagning av Använd väg tabell
- Refererat resurs etablerings fel
- Fel vid borttagning av offentlig IP-adress
- Distributions problem på grund av distributions kvot
- Åtgärds fel på grund av organisations princip
- Prenumerations registrering saknas
- Etablerings fel för VM-tillägg
- Under näts kapacitet
- Kvoten överskreds

### <a name="identity-and-security-management"></a>Identitets- och säkerhetshantering

Identitets-och säkerhets hantering identifierar autentiserings-och auktoriseringsfel som kan förhindra kommunikation till klustret.

- Auktoriseringsfel för nod
- 401 fel
- 403 fel

## <a name="next-steps"></a>Nästa steg

Samla in loggar för att hjälpa dig att felsöka kluster problem genom att använda [AKS Periscope](https://aka.ms/aksperiscope).

Publicera dina frågor eller feedback på [UserVoice](https://feedback.azure.com/forums/914020-azure-kubernetes-service-aks) genom att lägga till [diag] i rubriken.
