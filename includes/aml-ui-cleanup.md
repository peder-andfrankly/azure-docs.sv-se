---
title: ta med fil
description: ta med fil
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 11/06/2019
ms.openlocfilehash: 96ede63b097999247675364217cf458a268e54d9
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929626"
---
>[!IMPORTANT]
>Du kan använda de resurser som du har skapat som krav för andra Azure Machine Learning självstudier och instruktions artiklar.

### <a name="delete-everything"></a>Ta bort allt

Om du inte planerar att använda något som du har skapat tar du bort hela resurs gruppen så att du inte debiteras några avgifter.

1. I Azure Portal väljer du **resurs grupper** på vänster sida av fönstret.
 
   ![Ta bort resursgrupp i Azure-portalen](./media/aml-ui-cleanup/delete-resources.png)

1. I listan väljer du den resurs grupp som du skapade.

1. Välj **Ta bort resursgrupp**.

Om du tar bort resurs gruppen raderas även alla resurser som du skapade i designern. 

### <a name="delete-individual-assets"></a>Ta bort enskilda till gångar

I designern där du skapade experimentet kan du ta bort enskilda till gångar genom att markera dem och sedan välja knappen **ta bort** .

Beräknings målet som du skapade här *automatiskt skalar automatiskt* till noll noder när det inte används. Den här åtgärden vidtas för att minimera kostnaderna. Gör så här om du vill ta bort beräknings målet:

![Ta bort till gångar](./media/aml-ui-cleanup/delete-asset.png)

Du kan avregistrera data uppsättningar från din arbets yta genom att markera varje data uppsättning och välja **avregistrera**.

![Avregistrera data uppsättning](./media/aml-ui-cleanup/unregister-dataset.png)

Om du vill ta bort en data uppsättning går du till lagrings kontot genom att använda Azure Portal eller Azure Storage Explorer och manuellt ta bort dessa till gångar.


