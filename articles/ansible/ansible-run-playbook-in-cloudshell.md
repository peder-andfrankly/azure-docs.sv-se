---
title: Snabb start – köra Ansible spel böcker via bash i Azure Cloud Shell
description: I den här snabb starten får du lära dig hur du utför olika Ansible-uppgifter med bash i Azure Cloud Shell
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.date: 04/30/2019
ms.openlocfilehash: d04708be82a704c2ce20a928380fca1d325493da
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155969"
---
# <a name="quickstart-run-ansible-playbooks-via-bash-in-azure-cloud-shell"></a>Snabb start: köra Ansible spel böcker via bash i Azure Cloud Shell

Azure Cloud Shell är ett interaktivt, webb läsar gränssnitt som är tillgängligt för hantering av Azure-resurser. Med Cloud Shell kan du antingen använda en kommando rad för bash eller PowerShell. I den här artikeln använder du bash i Azure Cloud Shell för att köra en Ansible-Spelbok.

## <a name="prerequisites"></a>Krav

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Konfigurera Azure Cloud Shell** – om du är nybörjare på Azure Cloud Shell kan du läsa [snabb start för bash i Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart).
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Automatisk konfiguration av autentiseringsuppgifter

Ansible autentiseras med Azure när du har loggat in på Cloud Shell för att hantera infrastruktur utan ytterligare konfiguration. 

När du arbetar med flera prenumerationer anger du den prenumerations Ansible som används genom att exportera miljövariabeln `AZURE_SUBSCRIPTION_ID`. 

Kör följande kommando om du vill visa alla dina Azure-prenumerationer:

```azurecli-interactive
az account list
```

Använd ditt ID för Azure-prenumerationen och ange `AZURE_SUBSCRIPTION_ID` enligt följande:

```azurecli-interactive
export AZURE_SUBSCRIPTION_ID=<your-subscription-id>
```

## <a name="verify-the-configuration"></a>Kontrollera konfigurationen
Verifiera konfigurationen genom att använda Ansible för att skapa en Azure-resurs grupp.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Snabb start: Konfigurera virtuell dator i Azure med Ansible](/azure/virtual-machines/linux/ansible-create-vm)