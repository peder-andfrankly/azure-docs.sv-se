---
title: ta med fil
description: ta med fil
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: f189843e865863d9b4088363e691ebc42ad9f2a9
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187489"
---
### <a name="network-interfaces"></a>Nätverksgränssnitt

|  |  |
|---------|---------|
| [NSG X på varje NIC](../articles/governance/policy/samples/nsg-on-nic.md) | Kräver att en viss nätverkssäkerhetsgrupp används med varje gränssnitt för virtuellt nätverk. Du anger ID för nätverkssäkerhetsgruppen som ska användas. |
| [Använda godkända undernät för VM-nätverksgränssnitt](../articles/governance/policy/samples/use-approved-subnet-vm-nics.md) | Kräver att nätverksgränssnitt använder ett godkänt undernät. Du anger ID för det godkända undernätet. |
| [Använda godkända vNet för VM-nätverksgränssnitt](../articles/governance/policy/samples/use-approved-vnet-vm-nics.md) | Kräver att nätverksgränssnitt använder ett godkänt virtuellt nätverk. Du anger ID för det godkända virtuella nätverket. |