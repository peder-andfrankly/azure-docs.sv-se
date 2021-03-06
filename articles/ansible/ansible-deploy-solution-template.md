---
title: Snabb start – distribuera Ansible lösnings mal len för Azure till CentOS
description: I den här snabb starten lär du dig hur du distribuerar Ansible-lösningen på en virtuell CentOS-dator som finns på Azure, tillsammans med verktyg som kon figurer ATS för att fungera med Azure.
keywords: ansible, azure, devops, solution template, virtual machine, managed identities for azure resources, centos, red hat
ms.topic: quickstart
ms.date: 04/30/2019
ms.openlocfilehash: ebd01411ff27b0e95be21ef147098a90b6f62742
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156328"
---
# <a name="quickstart-deploy-the-ansible-solution-template-for-azure-to-centos"></a>Snabb start: Distribuera Ansible lösnings mal len för Azure till CentOS

Ansible-lösningsmallen för Azure är utformad att konfigurera en Ansible-instans på en virtuell CentOS-dator med Ansible och en uppsättning verktyg som konfigurerats att fungera med Azure. Här är några av verktygen:

- **Ansible-moduler för Azure** – [Ansible-moduler för Azure](./ansible-matrix.md) är en uppsättning moduler som hjälper dig att skapa och hantera infrastrukturen i Azure. Den senaste versionen av dessa moduler distribueras som standard. Under distributionen av lösningsmallen kan du ange ett versionsnummer som är lämpligt för din miljö.
- **Azure Command-Line Interface (CLI) 2.0** – [Azure CLI 2.0](/cli/azure/?view=azure-cli-latest) är en plattformsoberoende kommandoradsmiljö för hantering av Azure-resurser. 
- **Hanterade identiteter för Azure-resurser** – Funktionen [Hanterade identiteter för Azure-resurser](/azure/active-directory/managed-identities-azure-resources/overview) hjälper dig att skydda autentiseringsuppgifterna för molnprogram.

## <a name="prerequisites"></a>Krav

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="deploy-the-ansible-solution-template"></a>Distribuera mallen för Ansible-lösning

1. Bläddra till [Ansible-lösningsmallen på Azure Marketplace](https://azuremarketplace.microsoft.com/en-%20%20us/marketplace/apps/azure-oss.ansible?tab=Overview).

1. Välj **GET IT NOW** (Hämta nu).

1. Ett fönster visas som beskriver användningsvillkoren, sekretesspolicyn och Azure Marketplace-användningsvillkoren. Välj **Fortsätt**.

1. Azure-portalen öppnas och visar Ansible-sidan med en beskrivning av lösningsmallen. Välj **Skapa**.

1. Sidan **Skapa Ansible** innehåller flera flikar. Ange nödvändig information på fliken **Grundinställningar**:

   - **Namn** – Ange namnet på din Ansible-instans. I den här demonstrationen används namnet `ansiblehost`.
   - **Användarnamn** – Ange användarnamnet som ska ha åtkomst till Ansible-instansen. I den här demonstrationen används namnet `ansibleuser`.
   - **Autentiseringstyp** – Välj antingen **Lösenord** eller **Offentlig SSH-nyckel**. I den här demonstrationen används **Offentlig SSH-nyckel**.
   - **Lösenord** och **Bekräfta lösenord** – Om du väljer **Lösenord** för **Autentiseringstyp** anger du ditt lösenord för dessa värden.
   - **Offentlig SSH-nyckel** – Om du väljer **Offentlig SSH-nyckel** för **Autentiseringstyp** anger du din offentliga RSA-nyckel i det enradiga formatet – börja med `ssh-rsa`.
   - **Prenumeration** – Välj din Azure-prenumeration i listrutan.
   - **Resursgrupp** – Välj en befintlig resursgrupp i listrutan eller välj **Skapa ny** och ange ett namn för en ny resursgrupp. I den här demonstrationen används en ny resursgrupp med namnet `ansiblerg`.
   - **Plats** – Välj den plats i listrutan som passar ditt scenario.

     ![Grundläggande Ansible-inställningar på en flik på Azure-portalen](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-1.png)

1. Välj **OK**.

1. På fliken **Ytterligare inställningar** anger du nödvändig information:

   - **Storlek** – En standardstorlek används på Azure-portalen. Om du vill använda en annan storlek som passar ditt scenario väljer du pilen för att visa en lista med olika storlekar.
   - **VM-disktyp** – Välj antingen **SSD** (Premium Solid-State Drive) eller **HDD** (hårddisk). I den här demonstrationen används **SSD** eftersom SSD har flera prestandafördelar. Mer information om dessa typer av disklagring finns i följande artiklar:
       - [Premium Storage och hanterade diskar för virtuella datorer med höga prestanda](/azure/virtual-machines/windows/premium-storage)
       - [Standard SSD Managed Disks för arbetslastningar för virtuella Azure-datorer](/azure/virtual-machines/windows/disks-standard-ssd)
   - **Offentlig IP-adress** – Ange den här inställningen om du vill kommunicera med den virtuella datorn utanför den virtuella datorn. Standardvärdet är en ny offentlig IP-adress med namnet `ansible-pip`. Om du vill ange en annan IP-adress väljer du pilen för att ange attributen, till exempel namn, SKU och tilldelning för IP-adressen. 
   - **Domännamnsetikett** – Ange det offentliga domännamnet för den virtuella datorn. Namnet måste vara unikt och uppfylla namngivningskraven. Mer information om hur du anger ett namn för den virtuella datorn finns i [Namngivningskonventioner för Azure-resurser](/azure/architecture/best-practices/resource-naming).
   - **Ansible-version** – Ange ett versionsnummer eller värdet `latest` för att distribuera den senaste versionen. Välj informationsikonen bredvid **Ansible-version** för att visa mer information om tillgängliga versioner.

     ![Ytterligare inställningar för Ansible på en flik på Azure-portalen](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-2.png)

1. Välj **OK**.

1. Ange autentiseringstypen på fliken **Integrationsinställningar för Ansible**. Mer information om hur du skyddar Azure-resurser finns i [Vad är hanterade identiteter för Azure-resurser?](/azure/active-directory/managed-identities-azure-resources/overview).

    ![Integrationsinställningar för Ansible på en flik på Azure-portalen](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-3.png)

1. Välj **OK**.

1. Sidan **Sammanfattning** öppnas och visar verifieringsprocessen med en lista över de angivna kriterierna för Ansible-distributionen. Du kan använda en länk längst ned på fliken för att **ladda ned mallen och parametrarna** för användning med språk och plattformar som stöds i Azure. 

     ![Fliken Sammanfattning för Ansible på en flik på Azure-portalen](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-4.png)

1. Välj **OK**.

1. När fliken **Skapa** visas väljer du **OK** för att distribuera Ansible.

1. Välj ikonen **Meddelanden** överst på portalsidan för att spåra Ansible-distributionen. När distributionen är klar väljer du **Gå till resursgrupp**. 

     ![Fliken Sammanfattning för Ansible på en flik på Azure-portalen](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-complete.png)

1. Hämta IP-adressen för din Ansible-värd på sidan för resursgrupper och logga in för att hantera dina Azure-resurser med hjälp av Ansible.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Snabb start: Konfigurera en virtuell Linux-dator i Azure med Ansible](/azure/virtual-machines/linux/ansible-create-vm)