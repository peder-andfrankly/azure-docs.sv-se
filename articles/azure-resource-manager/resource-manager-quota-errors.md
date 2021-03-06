---
title: Kvot fel
description: Beskriver hur du löser resurs kvot fel när du distribuerar resurser med Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.openlocfilehash: 8760b027d815dfc70000db5aea534bf10f60b759
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149428"
---
# <a name="resolve-errors-for-resource-quotas"></a>Lösa fel för resurs kvoter

I den här artikeln beskrivs kvot fel som kan uppstå när du distribuerar resurser.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>Symptom

Om du distribuerar en mall som skapar resurser som överstiger dina Azure-kvoter får du ett distributions fel som ser ut så här:

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

Eller så kan du se:

```
Code=ResourceQuotaExceeded
Message=Creating the resource of type <resource-type> would exceed the quota of <number>
resources of type <resource-type> per resource group. The current resource count is <number>,
please delete some resources of this type before creating a new one.
```

## <a name="cause"></a>Orsak

Kvoter tillämpas per resursgrupp, prenumerationer, konton och andra omfång. Din prenumeration kan till exempel konfigureras så att antalet kärnor för en region begränsas. Om du försöker distribuera en virtuell dator med fler kärnor än det tillåtna antalet visas ett felmeddelande om att kvoten har överskridits.
Fullständig kvot information finns i [Azure-prenumeration och tjänst begränsningar, kvoter och begränsningar](../azure-subscription-service-limits.md).

## <a name="troubleshooting"></a>Felsökning

### <a name="azure-cli"></a>Azure CLI

För Azure CLI använder du kommandot `az vm list-usage` för att hitta kvoter för virtuella datorer.

```azurecli
az vm list-usage --location "South Central US"
```

Returnerar:

```azurecli
[
  {
    "currentValue": 0,
    "limit": 2000,
    "name": {
      "localizedValue": "Availability Sets",
      "value": "availabilitySets"
    }
  },
  ...
]
```

### <a name="powershell"></a>PowerShell

För PowerShell använder du kommandot **Get-AzVMUsage** för att hitta kvoter för virtuella datorer.

```powershell
Get-AzVMUsage -Location "South Central US"
```

Returnerar:

```powershell
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional Cores                         0   100 Count
Virtual Machines                             0 10000 Count
```

## <a name="solution"></a>Lösning

Om du vill begära en kvot ökning går du till portalen och filen ett support ärende. I support ärendet kan du begära en ökning av kvoten för den region som du vill distribuera till.

> [!NOTE]
> Kom ihåg att för resurs grupper är kvoten för varje enskild region, inte för hela prenumerationen. Om du behöver distribuera 30 kärnor i västra USA måste du be om 30 Resource Manager-kärnor i västra USA. Om du behöver distribuera 30 kärnor i någon av de regioner som du har åtkomst till, bör du fråga efter 30 Resource Manager-kärnor i alla regioner.
>
>

1. Välj **Prenumerationer**.

   ![Prenumerationer](./media/resource-manager-quota-errors/subscriptions.png)

2. Välj den prenumeration som behöver en ökad kvot.

   ![Välj en prenumeration](./media/resource-manager-quota-errors/select-subscription.png)

3. Välj **användning + kvoter**

   ![Välj användning och kvoter](./media/resource-manager-quota-errors/select-usage-quotas.png)

4. I det övre högra hörnet väljer du **begär ökning**.

   ![Begär ökning](./media/resource-manager-quota-errors/request-increase.png)

5. Fyll i formulären för den typ av kvot som du vill utöka.

   ![Fyll i formulär](./media/resource-manager-quota-errors/forms.png)