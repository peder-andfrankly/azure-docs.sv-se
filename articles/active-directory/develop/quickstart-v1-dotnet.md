---
title: 'Logga in användare & anropa Microsoft Graph API: er från .NET Desktop (WPF) | Azure'
description: Lär dig hur du skapar ett .NET Windows Desktop-program som integreras med Azure AD för inloggning och anropar Azure AD-skyddade API:er med hjälp av OAuth 2.0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: ed33574f-6fa3-402c-b030-fae76fba84e1
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f1aca48c547fc8c298128b35d9c571512fcbdf8
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965950"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-a-net-desktop-wpf-app"></a>Snabb start: Logga in användare och anropa Microsoft Graph-API från en .NET Desktop-app (WPF)

[Microsoft Identity Platform](v2-overview.md) är en utveckling av Azure Active Directory (Azure AD) Developer Platform. Den hjälper utvecklare att bygga program som loggar in alla Microsoft-identiteter och hämtar tokens för att anropa Microsoft API:er som Microsoft Graph eller API:er som utvecklare har byggt.

Med [Microsoft Authentication Library (MSAL)](msal-overview.md) kan utvecklare Hämta tokens från slut punkten för Microsoft Identity Platform för att få åtkomst till säkra webb-API: er. Active Directory-autentiseringsbibliotek (ADAL) integreras med slut punkten för Azure AD för utvecklare (v 1.0), där MSAL integreras med slut punkten för Microsoft Identity Platform (v 2.0).

För nya Skriv bords program rekommenderar vi att du använder Microsoft Identity Platform (v 2.0) och MSAL för att hämta token och åtkomst till säkra webb-API: er: [snabb start: Hämta en token och anropa Microsoft Graph API från en Windows Desktop-app](quickstart-v2-windows-desktop.md)
