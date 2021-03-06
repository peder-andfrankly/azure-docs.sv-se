---
title: Använda Multi-Factor AAD-autentisering
description: Azure SQL Database och Azure SQL Data Warehouse stöd för anslutningar från SQL Server Management Studio (SSMS) med Active Directory Universal Authentication.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/08/2018
ms.openlocfilehash: 7183193f3639ea809c6e7aa19af7844bd134111e
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820905"
---
# <a name="using-multi-factor-aad-authentication-with-azure-sql-database-and-azure-sql-data-warehouse-ssms-support-for-mfa"></a>Använda Multi-Factor AAD-autentisering med Azure SQL Database och Azure SQL Data Warehouse (SSMS-stöd för MFA)
Azure SQL Database och Azure SQL Data Warehouse stöd för anslutningar från SQL Server Management Studio (SSMS) med *Active Directory Universal Authentication*. I den här artikeln beskrivs skillnaderna mellan olika autentiseringsalternativ och de begränsningar som är associerade med att använda Universal Authentication. 

**Hämta den senaste SSMS** – på klient datorn laddar du ned den senaste versionen av SSMS, från [nedladdnings SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). 


För alla funktioner som beskrivs i den här artikeln använder du minst juli 2017, version 17,2.  Dialog rutan senaste anslutning bör se ut ungefär som på följande bild:
 
  ![1mfa – Universal-Connect](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png "Slutför rutan användar namn.")  

## <a name="the-five-authentication-options"></a>De fem alternativen för autentisering  

Active Directory Universal Authentication stöder de två icke-interaktiva autentiseringsmetoderna:
    - `Active Directory - Password` autentisering
    - `Active Directory - Integrated` autentisering

Det finns två icke-interaktiva autentiseringsscheman och kan användas i många olika program (ADO.NET, JDCB, ODC osv.). Dessa två metoder resulterar aldrig i popup-dialog rutor: 
- `Active Directory - Password` 
- `Active Directory - Integrated` 

Den interaktiva metoden är att även har stöd för Azure Multi-Factor Authentication (MFA): 
- `Active Directory - Universal with MFA` 


Azures MFA bidrar till att skydda åtkomsten till data och program och tillgodoser samtidigt användarens önskemål om en enkel inloggningsprocess. Den ger stark autentisering med en rad enkla verifierings alternativ (telefonsamtal, textmeddelande, smartkort med PIN-kod eller meddelande för mobilapp), så att användarna kan välja den metod de föredrar. Interaktiv MFA med Azure AD kan resultera i en popup-dialogruta för verifiering.

En beskrivning av Multi-Factor Authentication finns i [Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md).
Konfigurations steg finns i [konfigurera Azure SQL Database Multi-Factor Authentication för SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD-domän namn eller klient-ID-parameter   

Från och med [SSMS version 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)kan användare som importeras till den aktuella Active Directory från andra Azure Active Directory som gäst användare ange Azure AD-domännamnet eller klient-ID när de ansluter. Gäst användare inkluderar användare som har bjudits in från andra Azure-annonser, Microsoft-konton som outlook.com, hotmail.com, live.com eller andra konton som gmail.com. Den här informationen tillåter **Active Directory universell med MFA-autentisering** för att identifiera rätt autentiserings-utfärdare. Det här alternativet krävs också för att stödja Microsoft-konton (MSA) som outlook.com, hotmail.com, live.com eller icke-MSA konton. Alla dessa användare som ska autentiseras med Universal Authentication måste ange sitt Azure AD-domännamn eller klient-ID. Den här parametern representerar det aktuella Azure AD-domännamnet/klient-ID som Azure-servern är länkad till. Om till exempel Azure Server är associerat med Azure AD-domän `contosotest.onmicrosoft.com` där användar `joe@contosodev.onmicrosoft.com` är värd för en importerad användare från Azure AD-domän `contosodev.onmicrosoft.com`, är domän namnet som krävs för att autentisera användaren `contosotest.onmicrosoft.com`. När användaren är en inbyggd användare av Azure AD som är länkad till Azure Server och inte är ett MSA-konto krävs inget domän namn eller klient-ID. Om du vill ange parametern (från och med SSMS version 17,2) går du till dialog rutan **Anslut till databas** , fyller i dialog rutan, väljer **Active Directory-universell med MFA** -autentisering, klickar på **alternativ**, slutför **användar namnet** och klicka sedan på fliken **anslutnings egenskaper** . kontrol lera rutan **AD-domännamn eller klient-ID** och ge autentiserings utfärdare, till exempel domän namnet (**contosotest.onmicrosoft.com**) eller GUID för klient-ID.  
   ![MFA-Tenant-SSMS](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)

Om du kör SSMS 18. x eller senare behövs inte längre AD-domännamnet eller klient-ID: t för gäst användare eftersom 18. x eller senare identifierar det automatiskt.

   ![MFA-Tenant-SSMS](./media/sql-database-ssms-mfa-auth/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Support för Azure AD Business till företag   
Azure AD-användare som stöds för Azure AD B2B-scenarier som gäst användare (se [Vad är Azure B2B-samarbete](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)) kan ansluta till SQL Database och SQL Data Warehouse endast som en del av medlemmar i en grupp som skapats i den aktuella Azure AD och som mappas manuellt med hjälp av Transact-SQL `CREATE USER`-satsen i en specifik databas. Om `steve@gmail.com` till exempel är inbjuden till Azure AD `contosotest` (med Azure AD-domän `contosotest.onmicrosoft.com`), måste en Azure AD-grupp, till exempel `usergroup`, skapas i Azure AD som innehåller `steve@gmail.com`-medlemmen. Sedan måste den här gruppen skapas för en särskild databas (dvs. databas) av Azure AD SQL-administratören eller Azure AD DBO genom att köra en Transact-SQL `CREATE USER [usergroup] FROM EXTERNAL PROVIDER`-instruktion. När databas användaren har skapats kan användaren `steve@gmail.com` logga in på `MyDatabase` med alternativet SSMS-autentisering `Active Directory – Universal with MFA support`. Usergroup har som standard bara Connect-behörigheten och all ytterligare data åtkomst som behöver beviljas på normalt sätt. Observera att användaren `steve@gmail.com` som gäst användare måste markera kryss rutan och lägga till AD-domännamnet `contosotest.onmicrosoft.com` i dialog rutan SSMS **Connection Property** . Alternativet **AD-domännamn eller klient-ID** stöds bara för universellt med MFA-anslutnings alternativ, annars är det nedtonad.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>Begränsningar för Universal-autentisering för SQL Database och SQL Data Warehouse
- SSMS och SqlPackage. exe är de enda verktyg som är aktiverade för MFA genom Active Directory Universal Authentication.
- SSMS version 17,2, stöder samtidig åtkomst till flera användare med hjälp av Universal Authentication med MFA. Version 17,0 och 17,1, begränsad en inloggning för en instans av SSMS med hjälp av Universal Authentication till ett enda Azure Active Directory konto. Om du vill logga in som ett annat Azure AD-konto måste du använda en annan instans av SSMS. (Den här begränsningen är begränsad till Active Directory Universal Authentication. du kan logga in på olika servrar med Active Directory lösenordsautentisering, Active Directory integrerad autentisering eller SQL Server autentisering).
- SSMS stöder Active Directory Universal Authentication för Object Explorer, Frågeredigeraren och visualisering av fråge arkiv.
- SSMS version 17,2 ger stöd för DacFx-guiden för export/extrahering/distribution av data databaser. När en enskild användare har autentiserats via dialog rutan inledande autentisering med hjälp av Universal Authentication, fungerar DacFx-guiden på samma sätt som för alla andra autentiseringsmetoder.
- SSMS-Tabelldesigner stöder inte Universal-autentisering.
- Det finns inga ytterligare program varu krav för Active Directory Universal Authentication, förutom att du måste använda en version av SSMS som stöds.  
- Active Directory-autentiseringsbibliotek-versionen (ADAL) för Universal Authentication har uppdaterats till den senaste ADAL. dll-3.13.9 tillgänglig version. Se [Active Directory-autentiseringsbibliotek 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  


## <a name="next-steps"></a>Nästa steg

- Konfigurations steg finns i [konfigurera Azure SQL Database Multi-Factor Authentication för SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).
- Ge andra åtkomst till databasen: [SQL Database autentisering och auktorisering: bevilja åtkomst](sql-database-manage-logins.md)  
- Se till att andra kan ansluta genom brand väggen: [Konfigurera en Azure SQL Database brand Väggs regel på server nivå med hjälp av Azure Portal](sql-database-configure-firewall-settings.md)  
- [Konfigurera och hantera Azure Active Directory-autentisering med SQL Database eller SQL Data Warehouse](sql-database-aad-authentication-configure.md)  
- [Microsoft SQL Server Data-Tier Application Framework (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage. exe](https://docs.microsoft.com/sql/tools/sqlpackage)  
- [Importera en BACPAC-fil till en ny Azure SQL-databas](../sql-database/sql-database-import.md)  
- [Exportera en Azure SQL-databas till en BACPAC-fil](../sql-database/sql-database-export.md)  
- C#gränssnitt [IUniversalAuthProvider-gränssnitt](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- När du använder **Active Directory-universell med MFA-** autentisering, är ADAL-spårning tillgängligt från och med [SSMS 17,3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Av som standard kan du aktivera ADAL-spårning genom att använda menyn **verktyg**, **alternativ** , under Azure- **tjänster**, **Azure Cloud**, **ADAL fönstret utmatning spårnings nivå**, och sedan aktivera **utdata** i menyn **Visa** . Spårningarna är tillgängliga i fönstret utdata när du väljer **Azure Active Directory alternativ**.  
