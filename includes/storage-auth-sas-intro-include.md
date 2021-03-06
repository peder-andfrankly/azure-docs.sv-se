---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/25/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 3af8077627d56ce44c5e2959e2c79b967b09d9e5
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "69011942"
---
Med en signatur för delad åtkomst (SAS) kan du ge begränsad åtkomst till behållare och blobbar i ditt lagrings konto. När du skapar en SAS anger du dess begränsningar, inklusive vilka Azure Storage resurser som en klient har åtkomst till, vilka behörigheter de har på dessa resurser och hur länge SAS är giltig.

Varje SAS signeras med en nyckel. Du kan signera en SAS på ett av två sätt:

- Med en nyckel som skapats med hjälp av Azure Active Directory (autentiseringsuppgifter för Azure AD). En SAS som är signerad med Azure AD-autentiseringsuppgifter är en *användar* Delegerings-SAS.
- Med lagrings konto nyckeln. Både en *tjänst-SAS* och ett *konto säkerhets associationer* signeras med lagrings konto nyckeln.
