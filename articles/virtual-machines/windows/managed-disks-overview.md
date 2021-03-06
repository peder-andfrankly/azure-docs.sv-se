---
title: Översikt över Azure-disklagring Managed disk för virtuella Windows-datorer
description: Översikt över Azure Managed disks, som hanterar lagrings kontona åt dig när du använder virtuella Azure-datorer i Windows
author: roygara
ms.service: virtual-machines-windows
ms.topic: overview
ms.date: 08/15/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: c23cfbc418cca82393a0a66b0ceace622b2833f5
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038161"
---
# <a name="introduction-to-azure-managed-disks"></a>Introduktion till Azure Managed disks

En Azure-hanterad disk är en virtuell hård disk (VHD). Du kan tänka på det som en fysisk disk på en lokal server, men virtualiserad. Azure Managed disks lagras som Page BLOB-objekt, som är ett slumpmässigt IO-lagringssystem i Azure. Vi kallar en hanterad disk hanterad eftersom det är en abstraktion över Page blobbar, BLOB-behållare och Azure Storage-konton. Med hanterade diskar behöver du bara etablera disken och Azure tar hand om resten.

När du väljer att använda Azure Managed disks med dina arbets belastningar, skapar och hanterar Azure disken åt dig. De tillgängliga disk typerna är Ultra disk, Premium solid state-hårddisk (SSD), Standard SSD och standard hård disk (HDD). Mer information om varje enskild disk typ finns i [Välj en disktyp för virtuella IaaS-datorer](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Välj en disktyp för virtuella IaaS-datorer](disks-types.md)