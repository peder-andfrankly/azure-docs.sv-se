---
title: Konfiguration efter distribution med hjälp av tillägg
description: Lär dig hur du använder Azure Resource Manager mall-tillägg för att tillhandahålla konfigurationer efter distribution.
author: mumian
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: jgao
ms.openlocfilehash: c7896efb453cac478202efedb268c4bc838aef3a
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150373"
---
# <a name="provide-post-deployment-configurations-by-using-extensions"></a>Ange konfigurationer efter distribution med hjälp av tillägg

Mall tillägg är små program som ger konfigurations-och automatiserings åtgärder efter distributionen på Azure-resurser. Det mest populära är att tillägg för virtuella datorer. Se [tillägg och funktioner för virtuella datorer för Windows](../virtual-machines/extensions/features-windows.md)och [tillägg och funktioner för virtuella datorer för Linux](../virtual-machines/extensions/features-linux.md).

## <a name="extensions"></a>Tillägg

De befintliga tilläggen är:

- [Microsoft.Compute/virtualMachines/extensions](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachines/extensions)
- [Microsoft. Compute virtualMachineScaleSets/Extensions](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)
- [Microsoft. HDInsight-kluster/-tillägg](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/2018-06-01-preview/clusters/extensions)
- [Microsoft. SQL-servrar/-databaser/-tillägg](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions) 
- [Microsoft.Web/sites/siteextensions](https://docs.microsoft.com/azure/templates/microsoft.web/2016-08-01/sites/siteextensions)

Om du vill ta reda på vilka tillägg som är tillgängliga bläddrar du till [mal len referens](https://docs.microsoft.com/azure/templates/). I **Filtrera efter rubrik**, ange **tillägg**.

Information om hur du använder dessa tillägg finns i:

- [Självstudie: distribuera tillägg för virtuella datorer med Azure Resource Manager mallar](./resource-manager-tutorial-deploy-vm-extensions.md).
- [Självstudie: importera SQL BACPAC-filer med Azure Resource Manager mallar](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: distribuera tillägg för virtuella datorer med Azure Resource Manager mallar](./resource-manager-tutorial-deploy-vm-extensions.md)
