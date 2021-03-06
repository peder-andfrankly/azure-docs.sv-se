---
title: Skydda PaaS-databaser i Azure | Microsoft Docs
description: 'Lär dig mer om Azure SQL Database och SQL Data Warehouse rekommenderade säkerhets metoder för att skydda dina PaaS webb-och mobil program. '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: terrylan
ms.openlocfilehash: f7d993799fed637fbec55afc8f06d90c8fc6910f
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726785"
---
# <a name="best-practices-for-securing-paas-databases-in-azure"></a>Metod tips för att skydda PaaS-databaser i Azure

I den här artikeln diskuterar vi en samling [Azure SQL Database](../../sql-database/sql-database-technical-overview.md) och [SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) säkerhets metoder för att skydda din PaaS-webb (Platform-as-a-Service) och mobila program. Dessa bästa metoder härleds från vår erfarenhet av Azure och våra kunders upplevelser som du själv har.

Azure SQL Database och SQL Data Warehouse tillhandahålla en Relations databas tjänst för dina Internetbaserade program. Nu ska vi titta på tjänster som hjälper dig att skydda dina program och data när du använder Azure SQL Database och SQL Data Warehouse i en PaaS-distribution:

- Azure Active Directory autentisering (i stället för SQL Server autentisering)
- Azure SQL-brandvägg
- Transparent datakryptering (TDE)

## <a name="use-a-centralized-identity-repository"></a>Använd en central identitets databas
Azure SQL-databaser kan konfigureras för att använda en av två typer av autentisering:

- **SQL-autentisering** använder ett användar namn och lösen ord. När du skapade den logiska servern för databasen angav du en "serveradministratörsinloggning” med ett användarnamn och lösenord. Med dessa autentiseringsuppgifter kan du autentisera till valfri databas på servern som databasens ägare.

- **Azure Active Directory autentisering** använder identiteter som hanteras av Azure Active Directory och stöds för hanterade och integrerade domäner. Om du vill använda Azure Active Directory autentisering måste du skapa en annan Server administratör som kallas "Azure AD-administratör", som har behörighet att administrera Azure AD-användare och-grupper. Den här administratören kan också utföra alla åtgärder som en vanlig serveradministratören kan.

[Azure Active Directory autentisering](../../active-directory/develop/authentication-scenarios.md) är en mekanism för att ansluta till Azure SQL Database och SQL Data Warehouse med hjälp av identiteter i Azure Active Directory (AD). Azure AD är ett alternativ till att SQL Server autentisering så att du kan stoppa spridningen av användar identiteter på databas servrar. Med Azure AD-autentisering kan du centralt hantera identiteter för databas användare och andra Microsoft-tjänster på en central plats. Hantering av central-ID är en enda plats för att hantera databas användare och för att förenkla behörighets hanteringen.  

### <a name="benefits-of-using-azure-ad-instead-of-sql-authentication"></a>Fördelar med att använda Azure AD i stället för SQL-autentisering
- Tillåter lösen ords rotation på en enda plats.
- Hanterar databas behörigheter med hjälp av externa Azure AD-grupper.
- Eliminerar lagring av lösen ord genom att aktivera integrerad Windows-autentisering och andra former av autentisering som stöds av Azure AD.
- Använder inneslutna databas användare för att autentisera identiteter på databas nivå.
- Stöder tokenbaserad autentisering för program som ansluter till SQL Database.
- Stöder domän federation med Active Directory Federation Services (AD FS) (ADFS) eller autentisering med intern användare/lösenordsautentisering för en lokal Azure AD utan domän-synkronisering.
- Stöder anslutningar från SQL Server Management Studio som använder Active Directory Universal Authentication, som innehåller [Multi-Factor Authentication (MFA)](/azure/active-directory/authentication/multi-factor-authentication). I MFA används stark autentisering via en rad enkla verifieringsalternativ – telefonsamtal, SMS, smarta kort med PIN-kod eller avisering i mobilappen. Mer information finns i [Universal Authentication with SQL Database och SQL Data Warehouse](../../sql-database/sql-database-ssms-mfa-authentication.md).

Mer information om Azure AD-autentisering finns i:

- [Använd Azure Active Directory autentisering för autentisering med SQL Database, hanterad instans eller SQL Data Warehouse](../../sql-database/sql-database-aad-authentication.md)
- [Autentisera till Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-authentication.md)
- [Stöd för tokenbaserad autentisering för Azure SQL DB med Azure AD-autentisering](../../sql-database/sql-database-aad-authentication.md)

> [!NOTE]
> För att säkerställa att Azure Active Directory passar bra för din miljö kan du läsa mer i [Azure AD-funktioner och begränsningar](../../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations).
>
>

## <a name="restrict-access-based-on-ip-address"></a>Begränsa åtkomst baserat på IP-adress
Du kan skapa brand Väggs regler som anger intervall med acceptabla IP-adresser. Dessa regler kan nås både på Server-och databas nivå. Vi rekommenderar att du använder brand Väggs regler på databas nivå när det är möjligt för att förbättra säkerheten och göra databasen mer portabel. Brand Väggs regler på server nivå används bäst för administratörer och när du har många databaser med samma åtkomst krav, men du vill inte ägna tid åt att konfigurera varje databas individuellt.

SQL Database standard begränsningar för käll-IP-adress tillåter åtkomst från valfri Azure-adress, inklusive andra prenumerationer och klienter. Du kan begränsa detta så att dina IP-adresser bara får åtkomst till instansen. Även om du använder din SQL-brand vägg och IP-adressbegränsningar krävs stark autentisering fortfarande. Se rekommendationerna som gjordes tidigare i den här artikeln.

Mer information om Azure SQL-brandväggen och IP-begränsningar finns i:

- [Azure SQL Database och SQL Data Warehouse åtkomst kontroll](../../sql-database/sql-database-control-access.md)
- [Azure SQL Database och SQL Data Warehouse brand Väggs regler](../../sql-database/sql-database-firewall-configure.md)


## <a name="encrypt-data-at-rest"></a>Kryptera data i vila
[Transparent datakryptering (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) är aktiverat som standard. TDE krypterar SQL Server, Azure SQL Database och Azure SQL Data Warehouse data och loggfiler transparent. TDE skyddar mot en kompromiss med direkt åtkomst till filerna eller deras säkerhets kopiering. På så sätt kan du kryptera data i vila utan att ändra befintliga program. TDE bör alltid vara aktive rad. Detta kommer dock inte att stoppa en angripare som använder normal åtkomst Sök väg. TDE ger möjlighet att följa många lagar, förordningar och rikt linjer som har upprättats i olika branscher.

Azure SQL hanterar viktiga relaterade problem för TDE. Precis som med TDE måste lokala särskilda vård vidtas för att säkerställa återställnings möjlighet och när du flyttar databaser. I mer avancerade scenarier kan nycklarna hanteras explicit i Azure Key Vault genom utöknings bar nyckel hantering. Se [Aktivera TDE på SQL Server med EKM](/sql/relational-databases/security/encryption/enable-tde-on-sql-server-using-ekm). Detta gör det också möjligt för Bring Your Own Key (BYOK) via BYOK-kapacitet i Azure Key Vault.

Azure SQL tillhandahåller kryptering för kolumner via [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Detta gör att endast auktoriserade program får åtkomst till känsliga kolumner. Att använda den här typen av kryptering begränsar SQL-frågor för krypterade kolumner till värden som är lika baserade.

Kryptering på program nivå bör också användas för selektiva data. Data suveränitets frågor kan ibland minskas genom att kryptera data med en nyckel som behålls i rätt land/region. Detta förhindrar även oavsiktlig data överföring från att orsaka ett problem eftersom det är omöjligt att dekryptera data utan nyckeln, förutsatt att en stark algoritm används (till exempel AES 256).

Du kan använda ytterligare försiktighets åtgärder för att skydda databasen, till exempel utforma ett säkert system, Kryptera konfidentiella till gångar och skapa en brand vägg runt databas servrarna.

## <a name="next-steps"></a>Nästa steg
I den här artikeln har vi introducerat en samling av SQL Database och SQL Data Warehouse säkerhets metoder för att skydda dina PaaS webb-och mobil program. Mer information om hur du skyddar dina PaaS-distributioner finns i:

- [Skydda PaaS-distributioner](paas-deployments.md)
- [Skydda PaaS-webb-och mobil program med hjälp av Azure App Services](paas-applications-using-app-services.md)
