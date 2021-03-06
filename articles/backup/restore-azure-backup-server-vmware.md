---
title: Återställa virtuella VMware-datorer med Azure Backup Server
description: Använd Azure Backup Server (MABS) för att återställa virtuella VMware-datorer som körs på en VMware vCenter/ESXi-Server.
ms.topic: conceptual
ms.date: 08/18/2019
ms.openlocfilehash: 7c93c3100d8756fd9faf8cf02152a870bd0c106c
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171910"
---
# <a name="restore-vmware-virtual-machines"></a>Återställa virtuella VMware-datorer

Den här artikeln förklarar hur du använder Microsoft Azure Backup Server (MABS) för att återställa återställnings punkter för virtuella VMware-datorer. En översikt över hur du använder MABS för att återställa data finns i [återställa skyddade data](https://docs.microsoft.com/azure/backup/backup-azure-alternate-dpm-server). I MABS-Administratörskonsol finns det två sätt att hitta återställnings bara data – Sök eller bläddra. När du återställer data kan du kanske eller inte vill återställa data eller en virtuell dator till samma plats. Därför stöder MABS tre återställnings alternativ för säkerhets kopiering av virtuella VMware-datorer:

* **Återställning av ursprunglig plats (OLR)** – Använd OLR för att återställa en skyddad virtuell dator till dess ursprungliga plats. Du kan bara återställa en virtuell dator till dess ursprungliga plats om inga diskar har lagts till eller tagits bort, eftersom säkerhets kopieringen uppstod. Om diskarna har lagts till eller tagits bort måste du använda alternativ plats återställning.

* **Återställning av alternativ plats (återställning till)** – när den ursprungliga virtuella datorn saknas eller om du inte vill störa den ursprungliga virtuella datorn återställer du den virtuella datorn till en annan plats. Om du vill återställa en virtuell dator till en alternativ plats måste du ange platsen för en ESXi-värd, resurspool, mapp och lagrings plats och sökväg för lagring. För att skilja den återställda virtuella datorn från den ursprungliga virtuella datorn lägger MABS till "-återställd" till namnet på den virtuella datorn.

* **Individuell återställning av fil plats (ILR)** – om den skyddade virtuella datorn är en virtuell Windows Server-dator kan enskilda filer/mappar i den virtuella datorn återställas med Mabs ILR-funktion. Om du vill återställa enskilda filer, se proceduren senare i den här artikeln.

## <a name="restore-a-recovery-point"></a>Återställa en återställnings punkt

1. I MABS Administratörskonsol klickar du på återställnings visning.

2. Använd fönstret Bläddra, bläddra eller filtrera för att hitta den virtuella dator som du vill återställa. När du har valt en virtuell dator eller mapp visas tillgängliga återställnings punkter i rutan återställnings punkter för.

    ![Tillgängliga återställnings punkter](./media/restore-azure-backup-server-vmware/recovery-points.png)

3. I fältet **återställnings punkter för** använder du kalender-och list menyerna för att välja ett datum när en återställnings punkt skulle utföras. Kalender datum i fetstil har tillgängliga återställnings punkter.

4. I menyfliksområdet verktyg klickar du på **Återställ** för att öppna **återställnings guiden**.

    ![Återställnings guiden, granska val av återställning](./media/restore-azure-backup-server-vmware/recovery-wizard.png)

5. Klicka på **Nästa** för att gå vidare till skärmen **Ange återställnings alternativ** .

6. På skärmen **Ange återställnings alternativ** , om du vill aktivera begränsning av nätverks bandbredd, klickar du på **ändra**. Om du vill lämna nätverks begränsningen inaktive rad klickar du på **Nästa**. Inga andra alternativ för den här guide skärmen är tillgängliga för virtuella VMware-datorer. Om du väljer att ändra nätverks bandbredds begränsningen i dialog rutan begränsa väljer du **Aktivera begränsning av nätverks bandbredds användning** för att aktivera det. När du har aktiverat konfigurerar du **inställningarna** och **arbets schemat**.

7. På skärmen **Välj återställnings typ** väljer du om du vill återställa till den ursprungliga instansen eller till en ny plats och klickar på **Nästa**.

     * Om du väljer **Återställ till ursprunglig instans**behöver du inte göra några fler val i guiden. Data för den ursprungliga instansen används.

     * Om du väljer **Återställ som virtuell dator på en värd**anger du informationen för **ESXi-värden, resurspool, mappar** och **sökväg**på skärmen **Ange mål** .

      ![Välj återställnings typ](./media/restore-azure-backup-server-vmware/recovery-type.png)

8. På sidan **Sammanfattning** granskar du inställningarna och klickar på **Återställ** för att starta återställnings processen. Skärmen **återställnings status** visar förloppet för återställnings åtgärden.

## <a name="restore-an-individual-file-from-a-vm"></a>Återställa en enskild fil från en virtuell dator

Du kan återställa enskilda filer från en skyddad VM-återställnings punkt. Den här funktionen är endast tillgänglig för virtuella Windows Server-datorer. Att återställa enskilda filer liknar att återställa hela den virtuella datorn, förutom att du bläddrar till VMDK och letar upp de filer som du vill ha innan du påbörjar återställnings processen. Så här återställer du en enskild fil eller väljer filer från en virtuell Windows Server-dator:

1. I MABS Administratörskonsol klickar du på **återställnings** visning.

2. Använd fönstret **Bläddra** , bläddra eller filtrera för att hitta den virtuella dator som du vill återställa. När du har valt en virtuell dator eller mapp visas tillgängliga återställnings punkter i rutan återställnings punkter för.

    ![Tillgängliga återställnings punkter](./media/restore-azure-backup-server-vmware/recovery-points.png)

3. I rutan **återställnings punkter för:** använder du kalendern för att välja det datum som innehåller önskade återställnings punkt (er). Beroende på hur säkerhets kopierings principen har kon figurer ATS kan datum ha fler än en återställnings punkt. När du har valt den dag då återställnings punkten togs, se till att du har valt rätt **återställnings tid**. Om det valda datumet har flera återställnings punkter väljer du återställnings punkten genom att välja den i list rutan återställnings tid. När du har valt återställnings punkten visas listan över återställnings bara objekt i rutan **sökväg:** .

4. Du hittar de filer som du vill återställa genom att dubbelklicka på objektet i kolumnen **återställnings Bart objekt** i rutan **sökväg** för att öppna det. Välj den fil, de filer eller mappar som du vill återställa. Om du vill markera flera objekt trycker du på **CTRL** -tangenten när du markerar varje objekt. Använd **Sök vägs** fönstret för att söka i listan över filer eller mappar som visas i kolumnen **återställnings Bart objekt** . **Sök listan nedan** söker inte i undermappar. Om du vill söka i undermappar dubbelklickar du på mappen. Använd **upp** -knappen för att flytta från en underordnad mapp till den överordnade mappen. Du kan välja flera objekt (filer och mappar), men de måste finnas i samma överordnade mapp. Du kan inte återställa objekt från flera mappar i samma återställnings jobb.

5. När du har valt objekt för återställning går du till menyfliksområdet Administratörskonsol verktyg och klickar på **Återställ** för att öppna **återställnings guiden**. I återställnings guiden visar sidan **Granska val av återställning** de valda objekten som ska återställas.
    ![granska val av återställning](./media/restore-azure-backup-server-vmware/review-recovery.png)

6. På skärmen **Ange återställnings alternativ** , om du vill aktivera begränsning av nätverks bandbredd, klickar du på **ändra**. Om du vill lämna nätverks begränsningen inaktive rad klickar du på **Nästa**. Inga andra alternativ för den här guide skärmen är tillgängliga för virtuella VMware-datorer. Om du väljer att ändra nätverks bandbredds begränsningen i dialog rutan begränsa väljer du **Aktivera begränsning av nätverks bandbredds användning** för att aktivera det. När du har aktiverat konfigurerar du **inställningarna** och **arbets schemat**.
7. På skärmen **Välj återställnings typ** klickar du på **Nästa**. Du kan bara återställa dina filer eller mappar till en nätverksmapp.
8. På skärmen **Ange mål** klickar du på **Bläddra** för att hitta en nätverks plats för dina filer eller mappar. MABS skapar en mapp där alla återställda objekt kopieras. Mappnamnet har prefixet MABS_day-månad-år. När du väljer en plats för de återställda filerna eller mappen, anges information för den platsen (mål, mål Sök väg och tillgängligt utrymme).

       ![Specify location to recover files](./media/restore-azure-backup-server-vmware/specify-destination.png)

9. På skärmen **Ange återställnings alternativ** väljer du vilken säkerhets inställning som ska användas. Du kan välja att ändra nätverks bandbreddens användnings begränsning, men begränsningen är inaktive rad som standard. Dessutom är **San-återställning** och **meddelanden** inte aktiverade.
10. På sidan **Sammanfattning** granskar du inställningarna och klickar på **Återställ** för att starta återställnings processen. Skärmen **återställnings status** visar förloppet för återställnings åtgärden.

## <a name="next-steps"></a>Nästa steg

Om du vill felsöka problem när du använder Azure Backup Server kan du läsa [fel söknings guiden för Azure Backup Server](./backup-azure-mabs-troubleshoot.md).
