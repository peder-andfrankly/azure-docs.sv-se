---
title: 'Självstudier: Använd Azure Database Migration Service för en online-migrering av RDS-PostgreSQL till Azure Database for PostgreSQL | Microsoft Docs'
description: Lär dig att utföra en online-migrering från RDS PostgreSQL till Azure Database for PostgreSQL med hjälp av Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 09/06/2019
ms.openlocfilehash: 6cb10f09772bf6666e197a4b622792c5b62d3ace
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70734791"
---
# <a name="tutorial-migrate-rds-postgresql-to-azure-database-for-postgresql-online-using-dms"></a>Självstudier: Migrera RDS-PostgreSQL till Azure Database for PostgreSQL online med DMS

Du kan använda Azure Database Migration Service för att migrera databaser från en RDS PostgreSQL-instans till [Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/) medan käll databasen förblir online under migreringen. Med andra ord kan migreringen uppnås med minimal stillestånds tid för programmet. I den här självstudien migrerar du exempel databasen för **DVD-hyra** från en instans av RDS postgresql 9,6 till Azure Database for PostgreSQL med hjälp av aktiviteten online-migrering i Azure Database migration service.

I den här guiden får du lära dig att:
> [!div class="checklist"]
>
> * Migrera exempel schema med hjälp av pg_dump-verktyget.
> * Skapa en instans av Azure Database Migration Service.
> * Skapa ett migreringsjobb med hjälp av Azure Database Migration Service.
> * Köra migreringen.
> * Övervaka migreringen.

> [!NOTE]
> Användning av Azure Database Migration Service för att utföra en onlinemigrering kräver att en instans skapas baserad på prisnivån Premium. Mer information finns i på [prissättningssidan](https://azure.microsoft.com/pricing/details/database-migration/) för Azure Database Migration Service.

> [!IMPORTANT]
> För optimala migreringsfunktioner rekommenderar Microsoft att skapa en instans av Azure Database Migration Service i samma Azure-region som måldatabasen. Att flytta data mellan regioner eller geografiska områden kan göra migreringsprocessen långsammare och leda till fel.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Den här artikeln beskriver hur du utför en online-migrering från en lokal instans av PostgreSQL för att Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

* Ladda ned och installera [PostgreSQL Community Edition](https://www.postgresql.org/download/) 9.5, 9.6 eller 10. PostgreSQL-källserverversionen måste vara 9.5.11, 9.6.7, 10 eller senare. Mer information finns i artikeln [Versioner av PostgreSQL Database som stöds](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions).

    Dessutom måste PostgreSQL-versionen för fjärr skrivbords tjänster matcha Azure Database for PostgreSQL-versionen. Till exempel kan RDS PostgreSQL-9.5.11.5 endast migrera till Azure Database for PostgreSQL 9.5.11 och inte till version 9.6.7.

* Skapa en instans av [Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal). Se det här [avsnittet](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal#connect-to-the-postgresql-server-using-pgadmin) av dokumentet för information om hur du ansluter till postgresql-servern med hjälp av pgAdmin.
* Skapa ett Azure-Virtual Network (VNet) för Azure Database Migration Service med hjälp av Azure Resource Manager distributions modell, som tillhandahåller plats-till-plats-anslutning till dina lokala käll servrar genom att använda antingen [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN ](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Mer information om hur du skapar ett VNet finns i [Virtual Network-dokumentationen](https://docs.microsoft.com/azure/virtual-network/)och i synnerhet snabb starts artiklar med stegvisa anvisningar.
* Kontrollera att dina VNET-regler för nätverkssäkerhetsgruppen inte blockerar följande portar för inkommande kommunikation: 443, 53, 9354, 445 och 12000. Mer information om Azure VNet NSG trafik filtrering finns i artikeln [filtrera nätverks trafik med nätverks säkerhets grupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Konfigurera din [Windows-brandvägg för databasmotoråtkomst](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Öppna Windows-brandväggen för att tillåta Azure Database Migration Service att få åtkomst till PostgreSQL-servern, som standard är TCP-port 5432.
* När du använder en brandväggsinstallation framför dina källdatabaser kanske du måste lägga till brandväggsregler för att tillåta Azure Database Migration Service att komma åt källdatabaserna för migrering.
* Skapa en [brand Väggs regel](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) på server nivå för Azure Database for PostgreSQL servern för att tillåta Azure Database migration service åtkomst till mål databaserna. Ange under nätets intervall för det VNet som används för Azure Database Migration Service.

### <a name="set-up-aws-rds-postgresql-for-replication"></a>Konfigurera AWS RDS-PostgreSQL för replikering

1. Om du vill skapa en ny parameter grupp följer du anvisningarna från AWS i artikeln [arbeta med databas parameter grupper](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithParamGroups.html).
2. Använd huvud användar namnet för att ansluta till källan från Azure Database Migration Service. Om du använder ett annat konto än huvud användar kontot måste kontot ha rds_superuser-rollen och rds_replication-rollen. Rds_replication-rollen ger behörighet att hantera logiska platser och strömma data med logiska platser.
3. Skapa en ny parameter grupp med följande konfiguration: a. Ange parametern RDS. logical_replication i DB-parameter gruppen till 1.
    b. max_wal_senders = [antal samtidiga aktiviteter] – parametern max_wal_senders anger antalet samtidiga aktiviteter som kan köras, rekommenderar 10 aktiviteter.
    c. max_replication_slots – = [antal platser], rekommendera inställt på fem platser.
4. Koppla parameter gruppen som du skapade till RDS PostgreSQL-instansen.

## <a name="migrate-the-schema"></a>Migrera schemat

1. Extrahera schemat från käll databasen och tillämpa den på mål databasen för att slutföra migreringen av alla databas objekt, till exempel tabell scheman, index och lagrade procedurer.

    Det enklaste sättet att migrera endast schemat är att använda pg_dump med alternativet-s. Mer information finns i [exemplen](https://www.postgresql.org/docs/9.6/app-pgdump.html#PG-DUMP-EXAMPLES) i själv studie kursen om postgres-pg_dump.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Om du till exempel vill dumpa en schema fil för **dvdrental** -databasen använder du följande kommando:

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

2. Skapa en tom databas i mål tjänsten, som är Azure Database for PostgreSQL. Information om hur du ansluter och skapar en databas finns i någon av följande artiklar:

    * [Skapa en Azure Database for PostgreSQL-server med hjälp av Azure Portal](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal)
    * [Skapa en Azure Database for PostgreSQL med hjälp av Azure-CLI:n](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-azure-cli)

3. Importera schemat till mål tjänsten, som är Azure Database for PostgreSQL. Om du vill återställa schema dumpnings filen kör du följande kommando:

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Exempel:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

4. Om du har sekundärnycklar i ditt schema misslyckas den första inläsningen och den kontinuerliga synkroniseringen av migreringen. Extrahera det externa nyckel skriptet och Lägg till sekundär nyckel skript vid målet (Azure Database for PostgreSQL) genom att köra följande skript i PgAdmin eller i psql:
  
    ```
    SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ',
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema,
        tc.constraint_name as foreignkey,
        tc.table_name as tableName,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
    FROM
        information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    ```

5. Kör sekundär nyckeln Drop (som är den andra kolumnen) i frågeresultatet för att ta bort sekundär nyckeln.

6. Om du har utlösare (infoga eller uppdatera utlösare) i data, kommer den att upprätthålla data integriteten i målet innan data replikeras från källan. Rekommendationen är att inaktivera utlösare i alla tabeller *vid målet* under migreringen och sedan aktivera utlösarna när migreringen är klar.

    Så här inaktiverar du utlösare i mål databasen:

    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrera resursprovidern Microsoft.DataMigration

1. Logga in på Azure-portalen och välj **Alla tjänster** och sedan **Prenumerationer**.

   ![Visa portalprenumerationer](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-subscription1.png)

2. Välj den prenumeration där du vill skapa instansen av Azure Database Migration Service och välj sedan **resurs leverantörer**.

    ![Visa resursprovidrar](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-resource-provider.png)

3. Sök efter migreringen och välj sedan **Registrera** till höger om **Microsoft.DataMigration**.

    ![Registrera resursprovidern](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Skapa en instans av Azure Database Migration Service

1. I Azure Portal väljer du + **Skapa en resurs**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Service** i listrutan.

    ![Azure Marketplace](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-marketplace.png)

2. På sidan **Azure Database Migration Service** väljer du **Skapa**.

    ![Skapa Azure Database Migration Service-instans](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. På sidan **Create Migration Service** anger du ett namn för tjänsten, prenumerationen och en ny eller befintlig resursgrupp.

4. Välj den plats där du vill skapa instansen av Azure Database Migration Service.

5. Välj ett befintligt VNet eller skapa ett nytt.

    VNet ger Azure Database Migration Service åtkomst till käll PostgreSQL-instansen och mål Azure Database for PostgreSQL instansen.

    Mer information om hur du skapar ett VNet i Azure Portal finns i artikeln [skapa ett virtuellt nätverk med hjälp av Azure Portal](https://aka.ms/DMSVnet).

6. Välj en pris nivå; för denna online-migrering, se till att välja Premium: pris nivå för 4vCores.

    ![Konfigurera Azure Database Migration Service-instansinställningar](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-settings4.png)

7. Välj **Skapa** för att skapa tjänsten.

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt

När tjänsten har skapats letar du reda på den i Azure Portal, öppnar den och skapar sedan ett nytt migreringsprojekt.

1. I Azure Portal väljer du **Alla tjänster**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Services**.

      ![Leta reda på alla instanser Azure Database Migration Service](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-search.png)

2. På sidan **Azure Database Migration Services** söker du efter namnet på Azure Database Migration Service-instansen du har skapat och väljer sedan instansen.

     ![Hitta din instans av Azure Database Migration Service](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-instance-search.png)

3. Välj + **Nytt migreringsprojekt**.
4. På skärmen **ny migrerings projekt** anger du ett namn för projektet i text rutan **typ av käll Server** , väljer **AWS RDS för postgresql**och väljer sedan **Azure Database for PostgreSQL** i text rutan **mål server typ** . .
5. I avsnittet **Välj aktivitetstyp** väljer du **Online-datamigrering**.

    > [!IMPORTANT]
    > Se till att välja **migrering av data online**. offline-migrering stöds inte för det här scenariot.

    ![Skapa Database Migration Service-projekt](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-create-project5.png)

    > [!NOTE]
    > Alternativt kan du välja **skapa endast projekt** för att skapa migreringsjobbet nu och utföra migreringen senare.

6. Välj **Spara**.

7. Välj **Skapa och kör aktivitet** för att skapa projektet och köra migreringsaktiviteten.

    > [!NOTE]
    > Anteckna de krav som krävs för att konfigurera migrering online i bladet skapa projekt.

## <a name="specify-source-details"></a>Ange källinformation

* På informations skärmen för **migrerings källa** anger du anslutnings information för källan postgresql-instansen.

   ![Källinformation](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-source-details4.png)

## <a name="specify-target-details"></a>Ange målinformation

1. Välj **Spara**. på skärmen **mål information** anger du sedan anslutnings information för mål Azure Database for PostgreSQL servern, som är fördefinierad och har schemat för **DVD-hyresering** distribuerat med pg_dump.

    ![Välja mål](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-select-target4.png)

2. Välj **Spara** och mappa sedan på sidan **Mappa till måldatabaser** käll- och måldatabasen för migrering.

    Om mål databasen innehåller samma databas namn som käll databasen Azure Database Migration Service väljer mål databasen som standard.

    ![Mappa till måldatabaser](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-map-targets-activity5.png)

3. Välj **Spara**. I rutan **Aktivitetsnamn** på skärmen **Migreringssammanfattning** anger du ett namn för migreringsaktiviteten och granskar sedan sammanfattningen för att se till att informationen för källa och mål matchar det du angav tidigare.

    ![Migreringssammanfattning](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>Köra migreringen

* Välj **Kör migrering**.

    Migreringsaktivitetsfönstret visas och **Status** för aktiviteten är **Initieras**.

## <a name="monitor-the-migration"></a>Övervaka migreringen

1. På migreringsaktivitetssidan väljer du **Uppdatera** för att uppdatera visningen tills **Status** för migreringen är **Körs**.

    ![Aktivitets status-körs](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-activity-status3.png)

2. Under **databas namn**väljer du en annan databas för att komma till migreringsprocessen för **fullständig data inläsning** och **stegvisa data synkronisering** .

    **Fullständig data inläsning** visar statusen för den initiala inläsningen och den **stegvisa datasynkroniseringen** visar status för registrering av ändrings data (CDC).

    ![Inventerings skärm-fullständig data inläsning](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-full-load.png)

    ![Inventerings skärm – stegvis data synkronisering](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Utföra snabbmigrering

När den första fullständiga inläsningen har slutförts markeras databaserna som **klara att start punkt**.

1. När du är redo att slutföra databasmigreringen väljer du **Starta snabb**.

    ![Börja klipp ut](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-start-cutover.png)

2. Stoppa alla inkommande transaktioner till källdatabasen och vänta tills **Väntande ändringar** visar **0**.
3. Välj **Bekräfta** och sedan **Apply** (Använd).
4. När status för databas migreringen är **slutförd**ansluter du dina program till den nya mål Azure Database for PostgreSQLs databasen.

Online-migreringen av en lokal instans av PostgreSQL till Azure Database for PostgreSQL har slutförts.

## <a name="next-steps"></a>Nästa steg

* Mer information om Azure Database Migration Service finns i artikeln [What is the Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview) (Vad är Azure Database Migration Service?).
* Mer information om Azure Database for PostgreSQL finns i artikeln [Vad är Azure Database for PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview).
* Om du har andra frågor kan du skicka ett e-postalias till [Azure Database-migreringen](mailto:AskAzureDatabaseMigrations@service.microsoft.com) .
