---
title: Fråga Azure blockchain Workbench-data med SQL Server Management Studio
description: Lär dig hur du ansluter till SQL Database i Azure Blockchain Workbench från SQL Server Management Studio.
ms.date: 11/20/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
ms.openlocfilehash: f87d1880c90202fa26b0477e3b4dfbed5965bb82
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326028"
---
# <a name="using-azure-blockchain-workbench-data-with-sql-server-management-studio"></a>Använda Azure Blockchain Workbench Data med SQL Server Management Studio

Microsoft SQL Server Management Studio ger möjlighet att snabbt skriva och testa frågor mot Azure blockchain Workbench SQL DB. Det här avsnittet innehåller en stegvis genom gång av hur du ansluter till Azure blockchain Workbenchs SQL Database inifrån SQL Server Management Studio.

## <a name="prerequisites"></a>Krav

* Ladda ned [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="connecting-sql-server-management-studio-to-data-in-azure-blockchain-workbench"></a>Ansluta SQL Server Management Studio till data i Azure Blockchain Workbench

1. Öppna SQL Server Management Studio och välj **Anslut**.
2. Välj **Databasmotor**.

    ![Databasmotor](./media/data-sql-management-studio/database-engine.png)

3. Ange servernamnet och autentiseringsuppgifterna för databasen i dialogrutan **Anslut till Server**.

    Om du använder autentiseringsuppgifterna som skapades i samband med Azure Blockchain Workbench-distributionen är användarnamnet **dbadmin** och lösenordet är det lösenord som du angav under distributionen.

    ![Ange autentiseringsuppgifterna för SQL](./media/data-sql-management-studio/sql-creds.png)

   1. SQL Server Management Studio visar listan över databaser, databasvyer och lagrade procedurer i Azure Blockchain Workbench-databasen.

      ![Lista över databaser](./media/data-sql-management-studio/db-list.png)

5. Om du vill visa data som associeras med någon av databasvyerna kan du automatiskt generera en select-instruktion genom att följa stegen nedan.
6. Högerklicka på någon av databas vyerna i Object Explorer.
7. Välj **Skriptvy som**.
8. Välj **SELECT to** (SELECT till).
9. Välj **Nytt frågeredigeringsfönster**.
10. Du kan skapa en ny fråga genom att välja **Ny fråga**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Databasvyer i Azure Blockchain Workbench](database-views.md)
