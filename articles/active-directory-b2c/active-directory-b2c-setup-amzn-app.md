---
title: Konfigurera registrering och inloggning med ett Amazon-konto
titleSuffix: Azure AD B2C
description: Tillhandahålla registrering och inloggning till kunder med Amazon-konton i dina program med hjälp av Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e43c880fb2e38a235c38e19854da9e2b6e2a10c0
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74947731"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett Amazon-konto med hjälp av Azure Active Directory B2C

## <a name="create-an-amazon-application"></a>Skapa ett Amazon-program

Om du vill använda ett Amazon-konto som [identitets leverantör](active-directory-b2c-reference-oauth-code.md) i Azure Active Directory B2C (Azure AD B2C) måste du skapa ett program i din klient som representerar det. Om du inte redan har ett Amazon-konto kan du registrera dig på [https://www.amazon.com/](https://www.amazon.com/).

1. Logga in på [Amazon Developer Center](https://login.amazon.com/) med dina Amazon-kontoautentiseringsuppgifter.
1. Om du inte redan har gjort det klickar du på **Registrera**, följer utvecklings registrerings stegen och godkänner principen.
1. Välj **Registrera nytt program**.
1. Ange ett **namn**, en **Beskrivning**och **en URL för sekretess meddelande**och klicka sedan på **Spara**. Sekretess meddelandet är en sida som du hanterar som ger sekretess information till användarna.
1. I avsnittet **webb inställningar** kopierar du värdena för **klient-ID**. Välj **Visa hemlighet** för att hämta klient hemligheten och kopiera den sedan. Du behöver båda dessa för att konfigurera ett Amazon-konto som en identitets leverantör i din klient organisation. **Klient hemlighet** är en viktig säkerhets autentiseringsuppgift.
1. I avsnittet **webb inställningar** väljer du **Redigera**och anger sedan `https://your-tenant-name.b2clogin.com` i **tillåtna JavaScript-ursprung** och `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` i **tillåtna retur-URL: er**. Ersätt `your-tenant-name` med namnet på din klient. Du måste använda små bokstäver när du anger ditt klient namn även om klienten har definierats med versaler i Azure AD B2C.
1. Klicka på **Save** (Spara).

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Konfigurera ett Amazon-konto som en identitets leverantör

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD B2C-klientorganisationen.
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **identitets leverantörer**och välj sedan **Amazon**.
1. Ange ett **namn**. Till exempel *Amazon*.
1. För **klient-ID**anger du klient-ID för det Amazon-program som du skapade tidigare.
1. Ange den klient hemlighet som du har spelat in för **klient hemligheten**.
1. Välj **Spara**.
