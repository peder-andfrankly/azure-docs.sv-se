---
title: 'Använda hanterade identiteter på en virtuell Azure-dator med Azure-SDK: er – Azure AD'
description: 'Kod exempel för att använda Azure-SDK: er med en virtuell Azure-dator som har hanterade identiteter för Azure-resurser.'
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 422b4c63a86904721fc6910193ffa63a8fe46f0a
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184157"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-with-azure-sdks"></a>Använda hanterade identiteter för Azure-resurser på en virtuell Azure-dator med Azure SDK: er 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Den här artikeln innehåller en lista över SDK-exempel som demonstrerar användningen av deras respektive Azure SDK-stöd för hanterade identiteter för Azure-resurser.

## <a name="prerequisites"></a>Krav

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - All exempel kod/skript i den här artikeln förutsätter att klienten körs på en virtuell dator med hanterade identiteter för Azure-resurser aktiverade. Använd funktionen för att ansluta till den virtuella datorn i Azure Portal för att fjärrans luta till den virtuella datorn. Mer information om hur du aktiverar hanterade identiteter för Azure-resurser på en virtuell dator finns i [Konfigurera hanterade identiteter för Azure-resurser på en virtuell dator med hjälp av Azure Portal](qs-configure-portal-windows-vm.md)eller en av variant-artiklarna (med POWERSHELL, CLI, mall eller Azure SDK). 

## <a name="sdk-code-samples"></a>SDK-kod exempel

| SDK             | Kodexempel |
| --------------- | ----------- |
| .NET            | [Distribuera en Azure Resource Manager-mall från en virtuell Windows-dator med hanterade identiteter för Azure-resurser](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core       | [Anropa Azure-tjänster från en virtuell Linux-dator med hanterade identiteter för Azure-resurser](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js         | [Hantera resurser med hanterade identiteter för Azure-resurser](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python          | [Använd hanterade identiteter för Azure-resurser för att autentisera sig helt från en virtuell dator](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [Hantera resurser från en virtuell dator med hanterade identiteter för Azure-resurser aktiverade](https://github.com/Azure-Samples/resources-ruby-manage-resources-with-msi/) |

## <a name="next-steps"></a>Nästa steg

- Se [Azure-SDK](https://azure.microsoft.com/downloads/) : er för en fullständig lista över Azure SDK-resurser, inklusive biblioteks hämtningar, dokumentation och mycket annat.
- Om du vill aktivera hanterade identiteter för Azure-resurser på en virtuell Azure-dator, se [Konfigurera hanterade identiteter för Azure-resurser på en virtuell dator med hjälp av Azure Portal](qs-configure-portal-windows-vm.md).








