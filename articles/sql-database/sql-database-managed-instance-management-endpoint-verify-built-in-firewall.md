---
title: Identifiera inbyggd brand vägg för hanterad instans
description: Lär dig hur du verifierar det inbyggda brand Väggs skyddet i Azure SQL Database Hanterad instans.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 555ef56aafa37a1e1d384f945b04f9237adc5f7d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821806"
---
# <a name="verifying-the-managed-instance-built-in-firewall"></a>Verifiera den inbyggda brand väggen för den hanterade instansen

De [obligatoriska inkommande säkerhets reglerna](sql-database-managed-instance-connectivity-architecture.md#mandatory-inbound-security-rules) för den hanterade instansen kräver att hanterings portarna 9000, 9003, 1438, 1440, 1452 kan öppnas från **vilken källa som helst** i nätverks säkerhets gruppen (NSG) som skyddar den hanterade instansen. Även om dessa portar är öppna på NSG-nivån, skyddas de på nätverks nivå av den inbyggda brand väggen.

## <a name="verify-firewall"></a>Verifiera brand väggen

Verifiera portarna genom att använda ett säkerhets skanner verktyg för att testa portarna. Följande skärm bild visar hur du använder något av dessa verktyg.

![Verifiera inbyggd brand vägg](./media/sql-database-managed-instance-management-endpoint/03_verify_firewall.png)

## <a name="next-steps"></a>Nästa steg

Mer information om hanterade instanser och anslutningar finns [Azure SQL Database arkitektur för hanterad instans anslutning](sql-database-managed-instance-connectivity-architecture.md).