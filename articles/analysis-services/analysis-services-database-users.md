---
title: Hantera databas roller och användare i Azure Analysis Services | Microsoft Docs
description: Lär dig hur du hanterar databas roller och användare på en Analysis Services-server i Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9d1f3387fcea732e002689a4cdeaaf1d50d8a56f
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73147005"
---
# <a name="manage-database-roles-and-users"></a>Hantera databas roller och användare

På modell databas nivå måste alla användare tillhöra en roll. Roller definierar användare med särskilda behörigheter för modell databasen. Alla användare eller säkerhets grupper som läggs till i en roll måste ha ett konto i en Azure AD-klient i samma prenumeration som servern. 

Hur du definierar roller varierar beroende på vilket verktyg du använder, men resultatet är detsamma.

Roll behörigheter är:
*  **Administratör** – användare har fullständig behörighet för databasen. Databas roller med administratörs behörighet skiljer sig från Server administratörer.
*  **Process** -användare kan ansluta till och utföra process åtgärder på databasen och analysera modell databas data.
*  **Read** -Users kan använda ett klient program för att ansluta till och analysera modell databas data.

När du skapar ett tabell modell projekt skapar du roller och lägger till användare eller grupper i rollerna med hjälp av roll hanteraren i Visual Studio med Analysis Services projekt. När du har distribuerat till en server använder du SQL Server Management Studio (SSMS), [Analysis Services PowerShell-cmdletar](https://docs.microsoft.com/analysis-services/powershell/analysis-services-powershell-reference)eller TMSL ( [tabell modell skript språk](https://docs.microsoft.com/bi-reference/tmsl/tabular-model-scripting-language-tmsl-reference) ) för att lägga till eller ta bort roller och användar medlemmar.

**Säkerhets grupper** måste vara [e-postaktiverade](https://docs.microsoft.com/exchange/recipients-in-exchange-online/manage-mail-enabled-security-groups) med egenskapen `MailEnabled` inställd på `True`. Använd `obj:groupid@tenantid`när du anger en grupp per e-postadress.


## <a name="to-add-or-manage-roles-and-users-in-visual-studio"></a>Lägga till eller hantera roller och användare i Visual Studio  
  
1.  Högerklicka på **roller**i **tabell modell Utforskaren**.  
  
2.  Klicka på **Ny** i **rollhanteraren**.  
  
3.  Ange ett namn för rollen.  
  
     Som standard är namnet på standard rollen stegvist numrerat för varje ny roll. Vi rekommenderar att du anger ett namn som tydligt identifierar medlems typen, till exempel ekonomi chefer eller personal specialister.  
  
4.  Välj en av följande behörigheter:  
  
    |Behörighet|Beskrivning|  
    |----------------|-----------------|  
    |**Alternativet**|Medlemmar kan inte ändra modell schema och kan inte fråga efter data.|  
    |**Läsa**|Medlemmar kan fråga data (baserat på rad filter) men inte ändra modell schema.|  
    |**Läsa och bearbeta**|Medlemmar kan fråga data (baserat på rad nivå filter) och köra process och bearbeta alla åtgärder, men kan inte ändra modell schemat.|  
    |**Uppgraderingen**|Medlemmar kan köra process och bearbeta alla åtgärder. Det går inte att ändra modell schema och kan inte fråga efter data.|  
    |**Administratörstoken**|Medlemmar kan ändra modell schemat och fråga alla data.|   
  
5.  Om rollen som du skapar har Läs-eller Läs-och process behörighet kan du lägga till rad filter med hjälp av en DAX-formel. Klicka på fliken **rad filter** , Välj en tabell och klicka sedan på fältet **DAX-filter** och skriv sedan en DAX-formel.
  
6.  Klicka på **medlemmar** > **Lägg till extern**.  
  
8.  I **Lägg till extern medlem**, anger du användare eller grupper i din klient organisation Azure AD via e-postadress. När du klickar på OK och stänger roll hanteraren visas roller och roll medlemmar i tabell modell Utforskaren. 
 
     ![Roller och användare i tabell modell Utforskaren](./media/analysis-services-database-users/aas-roles-tmexplorer.png)

9. Distribuera till din Azure Analysis Services-server.


## <a name="to-add-or-manage-roles-and-users-in-ssms"></a>Lägga till eller hantera roller och användare i SSMS

Om du vill lägga till roller och användare i en distribuerad modell databas måste du vara ansluten till servern som Server administratör eller redan i en databas roll med administratörs behörighet.

1. I objekt Exporer högerklickar du på **roller** > **ny roll**.

2. Ange ett rollnamn och en beskrivning i **skapa roll**.

3. Välj en behörighet.

   |Behörighet|Beskrivning|  
   |----------------|-----------------|  
   |**Fullständig behörighet (administratör)**|Medlemmar kan ändra modell schema, bearbeta och kan fråga alla data.| 
   |**Process databas**|Medlemmar kan köra process och bearbeta alla åtgärder. Det går inte att ändra modell schema och kan inte fråga efter data.|  
   |**Läsa**|Medlemmar kan fråga data (baserat på rad filter) men inte ändra modell schema.|  
  
4. Klicka på **medlemskap**och ange sedan en användare eller grupp i din klient organisations Azure AD via e-postadress.

     ![Lägga till användare](./media/analysis-services-database-users/aas-roles-adduser-ssms.png)

5. Om rollen som du skapar har Läs behörighet kan du lägga till rad filter genom att använda en DAX-formel. Klicka på **rad filter**, Välj en tabell och skriv sedan en DAX-formel i fältet **DAX-filter** . 

## <a name="to-add-roles-and-users-by-using-a-tmsl-script"></a>Lägga till roller och användare med hjälp av ett TMSL-skript

Du kan köra ett TMSL-skript i XMLA-fönstret i SSMS eller med hjälp av PowerShell. Använd kommandot [CreateOrReplace](https://docs.microsoft.com/bi-reference/tmsl/createorreplace-command-tmsl) och objektet [roller](https://docs.microsoft.com/bi-reference/tmsl/roles-object-tmsl) .

**Exempel på TMSL-skript**

I det här exemplet läggs en extern B2B-användare och en grupp till i analytiker rollen med Läs behörighet för SalesBI-databasen. Både den externa användaren och gruppen måste finnas i samma klient Azure AD.

```
{
  "createOrReplace": {
    "object": {
      "database": "SalesBI",
      "role": "Analyst"
    },
    "role": {
      "name": "Users",
      "description": "All allowed users to query the model",
      "modelPermission": "read",
      "members": [
        {
          "memberName": "user1@contoso.com",
          "identityProvider": "AzureAD"
        },
        {
          "memberName": "group1@adventureworks.com",
          "identityProvider": "AzureAD"
        }
      ]
    }
  }
}
```

## <a name="to-add-roles-and-users-by-using-powershell"></a>Lägga till roller och användare med hjälp av PowerShell

[SQLServer](https://docs.microsoft.com/analysis-services/powershell/analysis-services-powershell-reference) -modulen innehåller verksamhetsspecifika databas hanterings-cmdletar och den allmänna cmdleten Invoke-ASCmd som accepterar en fråga eller ett skript för tabell modell skript språk (TMSL). Följande cmdletar används för att hantera databas roller och användare.
  
|Cmdlet|Beskrivning|
|------------|-----------------| 
|[Add-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|Lägg till en medlem i en databas roll.| 
|[Remove-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/remove-rolemember)|Ta bort en medlem från en databas roll.|   
|[Invoke-ASCmd](https://docs.microsoft.com/powershell/module/sqlserver/invoke-ascmd)|Kör ett TMSL-skript.|

## <a name="row-filters"></a>Rad filter  

Rad filter definierar vilka rader i en tabell som kan frågas efter medlemmar i en viss roll. Rad filter definieras för varje tabell i en modell med hjälp av DAX-formler.  
  
Rad filter kan bara definieras för roller med behörigheterna Läs och läsa och bearbeta. Som standard, om ett rad filter inte har definierats för en viss tabell, kan medlemmar fråga alla rader i tabellen om inte kors filtrering tillämpas från en annan tabell.
  
 Rad filter kräver en DAX-formel som måste utvärderas till TRUE/FALSe-värdet för att definiera vilka rader som kan frågas av medlemmar i den specifika rollen. Det går inte att skicka frågor till rader som inte ingår i DAX-formeln. Till exempel kan tabellen kunder med följande rad filter uttryck, *= kunder [land] = "USA"* , medlemmar i Sälj rollen bara se kunder i USA.  
  
Rad filter gäller för de angivna raderna och relaterade rader. När en tabell har flera relationer, använder filter säkerhet för den aktiva relationen. Rad filter korsas med andra raddata som definierats för relaterade tabeller, till exempel:  
  
|Tabell|DAX-uttryck|  
|-----------|--------------------|  
|Region|=Region[Country]="USA"|  
|ProductCategory|=ProductCategory[Name]="Bicycles"|  
|Transaktioner|= Transaktioner [Year] = 2016|  
  
 Netto resultatet är att medlemmar kan fråga rader med data där kunden är i USA, produkt kategorin är cyklar och året är 2016. Användare kan inte fråga transaktioner utanför USA, transaktioner som inte är cyklar eller transaktioner som inte är i 2016, om de inte är medlemmar i en annan roll som ger behörighet.
  
 Du kan använda filtret, *= falskt ()* för att neka åtkomst till alla rader för en hel tabell.

## <a name="next-steps"></a>Nästa steg

  [Hantera Server administratörer](analysis-services-server-admins.md)   
  [Hantera Azure Analysis Services med PowerShell](analysis-services-powershell.md)  
  [TMSL-referens (tabell modell skript språk)](https://docs.microsoft.com/bi-reference/tmsl/tabular-model-scripting-language-tmsl-reference)

