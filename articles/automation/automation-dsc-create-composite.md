---
title: Konvertera konfigurationer till sammansatta resurser för tillstånds konfiguration – Azure Automation
description: Lär dig hur du konverterar konfigurationer till sammansatta resurser för tillstånds konfiguration i Azure Automation.
keywords: DSC, PowerShell, konfiguration, installation
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 85c13a7175bca015ab24c8b09500b47e3ea846ed
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231655"
---
# <a name="convert-configurations-to-composite-resources"></a>Konvertera konfigurationer till sammansatta resurser

> Gäller för: Windows PowerShell 5,1

När du har börjat redigera konfigurationer kan du snabbt skapa "scenarier" som hanterar grupper av inställningar.
Exempel:

- skapa en webb server
- skapa en DNS-Server
- skapa en SharePoint-Server
- Konfigurera ett SQL-kluster
- hantera brand Väggs inställningar
- hantera lösen ords inställningar

Om du är intresse rad av att dela detta arbete med andra, är det bästa alternativet att paketera konfigurationen som en [sammansatt resurs](/powershell/scripting/dsc/resources/authoringresourcecomposite).
Det kan vara en överbelastande att skapa sammansatta resurser för första gången.

> [!NOTE]
> Den här artikeln hänvisar till en lösning som underhålls av communityn för öppen källkod.
> Support är endast tillgängligt i form av GitHub-samarbete, inte från Microsoft.

## <a name="community-project-compositeresource"></a>Community-projekt: CompositeResource

En community-baserad lösning med namnet [CompositeResource](https://github.com/microsoft/compositeresource) har skapats för att lösa den här utmaningen.

CompositeResource automatiserar processen med att skapa en ny modul från din konfiguration.
Du börjar med [punkt källa](https://blogs.technet.microsoft.com/heyscriptingguy/2010/08/10/how-to-reuse-windows-powershell-functions-in-scripts/) konfigurations skriptet på din arbets Station (eller bygg Server) så att det läses in i minnet.
I stället för att köra konfigurationen för att generera en MOF-fil använder du funktionen som tillhandahålls av CompositeResource-modulen för att automatisera en konvertering.
Cmdleten läser in innehållet i konfigurationen, hämtar listan över parametrar och genererar en ny modul med allt du behöver.

När du har genererat en modul kan du öka versionen och lägga till viktig information varje gång du gör ändringar och publicera den till din egen [PowerShellGet-lagringsplats](https://kevinmarquette.github.io/2018-03-03-Powershell-Using-a-NuGet-server-for-a-PSRepository/?utm_source=blog&utm_medium=blog&utm_content=psscriptrepo).

När du har skapat en sammansatt resurspool som innehåller din konfiguration (eller flera konfigurationer) kan du använda dem i den sammanställnings bara [redigerings upplevelsen](/azure/automation/compose-configurationwithcompositeresources) i Azure, eller lägga till dem i [DSC-konfigurations skript](/powershell/scripting/dsc/configurations/configurations) för att generera MOF-filer och [överföra MOF-filerna till Azure Automation](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Registrera sedan dina servrar från antingen [lokalt](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws) eller [i Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) för att hämta konfigurationer.
Den senaste uppdateringen av projektet har också publicerat [Runbooks](https://www.powershellgallery.com/packages?q=DscGallerySamples) för att Azure Automation automatisera processen med att importera konfigurationer från PowerShell-galleriet.

Om du vill prova att automatisera skapandet av sammansatta resurser för DSC går du till [PowerShell-galleriet](https://www.powershellgallery.com/packages/compositeresource/) och laddar ned lösningen eller klickar på "Project site" för att visa [dokumentationen](https://github.com/microsoft/compositeresource).

## <a name="next-steps"></a>Nästa steg

- [Översikt över önskad tillstånds konfiguration i Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [DSC-resurser](/powershell/scripting/dsc/resources/resources)
- [Konfigurera den lokala Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaconfig)
