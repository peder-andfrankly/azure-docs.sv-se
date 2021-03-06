---
title: Självstudie – Skapa en Azure Active Directory Domain Services-instans | Microsoft Docs
description: I den här självstudien får du lära dig hur du skapar och konfigurerar en Azure Active Directory Domain Services-instans med hjälp av Azure Portal.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: iainfou
ms.openlocfilehash: ef203eec1398e9f23fb162845b9d570316083ecf
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74703697"
---
# <a name="tutorial-create-and-configure-an-azure-active-directory-domain-services-instance"></a>Självstudie: skapa och konfigurera en Azure Active Directory Domain Services-instans

Azure Active Directory Domain Services (Azure AD DS) tillhandahåller hanterade domän tjänster som domän anslutning, grup princip, LDAP, Kerberos/NTLM-autentisering som är helt kompatibelt med Windows Server Active Directory. Du använder dessa domän tjänster utan att distribuera, hantera och korrigera domänkontrollanter själv. Azure AD DS integreras med din befintliga Azure AD-klient. Med den här integreringen kan användarna logga in med sina företags uppgifter, och du kan använda befintliga grupper och användar konton för att skydda åtkomsten till resurser.

Du kan skapa en hanterad domän med hjälp av standard konfigurations alternativ för nätverk och synkronisering, eller [definiera inställningarna manuellt][tutorial-create-instance-advanced]. Den här självstudien visar hur du använder standard alternativ för att skapa och konfigurera en Azure AD DS-instans med hjälp av Azure Portal.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Förstå DNS-krav för en hanterad domän
> * Skapa en Azure AD DS-instans
> * Aktivera hashsynkronisering för lösenord

Om du inte har en Azure-prenumeration kan du [skapa ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du följande resurser och behörigheter:

* En aktiv Azure-prenumeration.
    * [Skapa ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)om du inte har någon Azure-prenumeration.
* En Azure Active Directory klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog som endast är moln.
    * Om det behövs kan du [skapa en Azure Active Directory klient][create-azure-ad-tenant] eller [associera en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* Du behöver *Global administratörs* behörighet i Azure AD-klienten för att aktivera Azure AD DS.
* Du behöver *deltagar* behörighet i din Azure-prenumeration för att skapa de nödvändiga Azure AD DS-resurserna.

Även om det inte krävs för Azure AD DS rekommenderar vi att du [konfigurerar återställning av lösen ord för självbetjäning (SSPR)][configure-sspr] för Azure AD-klienten. Användare kan ändra sina lösen ord utan SSPR, men SSPR hjälper om de glömmer bort sitt lösen ord och behöver återställa det.

> [!IMPORTANT]
> När du har skapat en Azure AD DS-hanterad domän kan du inte flytta instansen till en annan resurs grupp, ett virtuellt nätverk, en prenumeration osv. Var noga med att välja den lämpligaste prenumerationen, resurs gruppen, regionen och det virtuella nätverket när du distribuerar Azure AD DS-instansen.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

I den här självstudien skapar du och konfigurerar Azure AD DS-instansen med hjälp av Azure Portal. Börja med att logga in på [Azure Portal](https://portal.azure.com)för att komma igång.

## <a name="create-an-instance"></a>Skapa en instans

Utför följande steg för att starta guiden för att **aktivera Azure AD Domain Services** :

1. På Azure Portal-menyn eller på **Start** sidan väljer du **skapa en resurs**.
1. Ange *domän tjänster* i Sök fältet och välj sedan *Azure AD Domain Services* från Sök förslagen.
1. På sidan Azure AD Domain Services väljer du **skapa**. Guiden **aktivera Azure AD Domain Services** startas.
1. Välj den Azure- **prenumeration** där du vill skapa den hanterade domänen.
1. Välj den **resurs grupp** som den hanterade domänen ska tillhöra. Välj att **skapa en ny** eller Välj en befintlig resurs grupp.

När du skapar en Azure AD DS-instans anger du ett DNS-namn. Det finns några saker att tänka på när du väljer det här DNS-namnet:

* **Inbyggt domän namn:** Som standard används det inbyggda domän namnet för katalogen (a *. onmicrosoft.com* suffix). Om du vill aktivera säker LDAP-åtkomst till den hanterade domänen via Internet kan du inte skapa ett digitalt certifikat för att skydda anslutningen till den här standard domänen. Microsoft äger *onmicrosoft.com* -domänen så att en certifikat utfärdare (ca) inte utfärdar ett certifikat.
* **Anpassade domän namn:** Det vanligaste tillvägagångs sättet är att ange ett anpassat domän namn, vanligt vis ett som du redan äger och som är dirigerbart. När du använder en dirigerbart, anpassad domän kan trafik flöda korrekt efter behov för att stödja dina program.
* **Icke-dirigerbart domänsuffix:** Vi rekommenderar vanligt vis att du undviker ett icke-dirigerbart domänsuffix, t. ex. *contoso. local*. *Lokalt* suffix kan inte dirigeras och kan orsaka problem med DNS-matchning.

> [!TIP]
> Om du skapar ett eget domän namn bör du ta hand om befintliga DNS-namnområden. Vi rekommenderar att du lägger till ett unikt prefix för domän namnet. Om ditt DNS-rotcertifikat till exempel är *contoso.com*skapar du en Azure AD DS-hanterad domän med det anpassade domän namnet *Corp.contoso.com* eller *DS.contoso.com*. I en hybrid miljö med en lokal AD DS-miljö kanske dessa prefix redan används. Använd ett unikt prefix för Azure AD DS.
>
> Du kan använda rot-DNS-namnet för din Azure AD DS-hanterade domän, men du kan behöva skapa ytterligare DNS-poster för andra tjänster i din miljö. Om du till exempel kör en webb server som är värd för en plats som använder rot-DNS-namnet, kan det finnas namn konflikter som kräver ytterligare DNS-poster.
>
> I dessa självstudier och instruktions artiklar används den anpassade domänen för *aadds.contoso.com* som ett kort exempel. I alla kommandon anger du ett eget domän namn, som kan innehålla ett unikt prefix.
>
> Mer information finns i [Välj ett namngivnings prefix för domänen][naming-prefix].

Följande DNS-namn begränsningar gäller också:

* **Begränsningar för domänautentiseringsuppgifter:** Du kan inte skapa en hanterad domän med ett prefix som är längre än 15 tecken. Prefixet för det angivna domän namnet (t. ex. *contoso* i *contoso.com* -domännamnet) måste innehålla högst 15 tecken.
* **Nätverks namns konflikter:** DNS-domännamnet för den hanterade domänen bör inte redan finnas i det virtuella nätverket. Mer specifikt kan du söka efter följande scenarier som leder till en namn konflikt:
    * Om du redan har en Active Directory domän med samma DNS-domännamn i det virtuella Azure-nätverket.
    * Om det virtuella nätverket där du planerar att aktivera den hanterade domänen har en VPN-anslutning till ditt lokala nätverk. I det här scenariot ser du till att du inte har en domän med samma DNS-domännamn i det lokala nätverket.
    * Om du har en befintlig Azure-moln tjänst med det namnet i det virtuella Azure-nätverket.

Fyll i fälten i fönstret *Basics* i Azure Portal för att skapa en Azure AD DS-instans:

1. Ange ett **DNS-domännamn** för din hanterade domän, och ta hänsyn till föregående punkter.
1. Välj den Azure- **plats** där den hanterade domänen ska skapas. Om du väljer en region som stöder Tillgänglighetszoner fördelas Azure AD DS-resurserna mellan zoner för ytterligare redundans.

    Tillgänglighetszoner är unika fysiska platser inom en Azure-region. Varje zon utgörs av ett eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverk. För att säkerställa återhämtning finns det minst tre separata zoner i alla aktiverade regioner.

    Det finns inget som du kan konfigurera för att Azure AD DS ska distribueras mellan zoner. Azure-plattformen hanterar automatiskt zon distributionen av resurser. Mer information och mer information om regions tillgänglighet finns i [Vad är Tillgänglighetszoner i Azure?][availability-zones]

1. En *skog* är en logisk konstruktion som används av Active Directory Domain Services för att gruppera en eller flera domäner. Som standard skapas en Azure AD DS-hanterad domän som en *användar* skog. Den här typen av skog synkroniserar alla objekt från Azure AD, inklusive alla användar konton som skapats i en lokal AD DS-miljö. En *resurs* skog synkroniserar bara användare och grupper som skapats direkt i Azure AD. Resurs skogar är för närvarande i för hands version. Mer information om *resurs* skogar, inklusive varför du kan använda en och hur du skapar skogs förtroenden med lokala AD DS-domäner finns i [Översikt över Azure AD DS resurs skogar][resource-forests].

    I den här självstudien väljer du att skapa en *användar* skog.

    ![Konfigurera grundläggande inställningar för en Azure AD Domain Services instans](./media/tutorial-create-instance/basics-window.png)

Om du snabbt vill skapa en Azure AD DS-hanterad domän kan du välja **Granska + skapa** för att acceptera ytterligare standard konfigurations alternativ. Följande standardinställningar konfigureras när du väljer det här alternativet för att skapa:

* Skapar ett virtuellt nätverk med namnet *aadds-VNet* som använder IP-adressintervallet *10.0.1.0/24*.
* Skapar ett undernät med namnet *aadds-Subnet* med IP-adressintervallet *10.0.1.0/24*.
* Synkroniserar *alla* användare från Azure AD till den hanterade Azure AD DS-domänen.

1. Välj **Granska + skapa** för att godkänna standard konfigurations alternativen.

## <a name="deploy-the-managed-domain"></a>Distribuera den hanterade domänen

Granska konfigurations inställningarna för den hanterade domänen på sidan **Sammanfattning** i guiden. Du kan gå tillbaka till valfritt steg i guiden för att göra ändringar. Om du vill distribuera om en Azure AD DS-hanterad domän till en annan Azure AD-klient på ett konsekvent sätt med hjälp av dessa konfigurations alternativ kan du också **Hämta en mall för Automation**.

1. Välj **skapa**för att skapa den hanterade domänen. En anteckning visas att vissa konfigurations alternativ, till exempel DNS-namn eller virtuellt nätverk, inte kan ändras när Azure AD DS Managed har skapats. Fortsätt genom att välja **OK**.
1. Processen för etablering av din hanterade domän kan ta upp till en timme. Ett meddelande visas i portalen som visar förloppet för din Azure AD DS-distribution. Välj aviseringen om du vill visa detaljerad förloppet för distributionen.

    ![Meddelande i pågående distributions Azure Portal](./media/tutorial-create-instance/deployment-in-progress.png)

1. Sidan kommer att läsas in med uppdateringar i distributions processen, inklusive att skapa nya resurser i katalogen.
1. Välj din resurs grupp, till exempel *myResourceGroup*, och välj sedan din Azure AD DS-instans i listan över Azure-resurser, till exempel *aadds.contoso.com*. Fliken **Översikt** visar att den hanterade domänen för närvarande *distribuerar*. Du kan inte konfigurera den hanterade domänen förrän den är helt etablerad.

    ![Status för domän tjänster under etablerings tillståndet](./media/tutorial-create-instance/provisioning-in-progress.png)

1. När den hanterade domänen är helt etablerad visar fliken **Översikt** den domän status som *körs*.

    ![Status för domän tjänster när den har kon figurer ATS](./media/tutorial-create-instance/successfully-provisioned.png)

Den hanterade domänen är kopplad till din Azure AD-klient. Under etablerings processen skapar Azure AD DS två företags program med namnet *domänkontrollant tjänster* och *AZUREACTIVEDIRECTORYDOMAINCONTROLLERSERVICES* i Azure AD-klienten. De här företags programmen behövs för att underhålla din hanterade domän. Ta inte bort de här programmen.

## <a name="update-dns-settings-for-the-azure-virtual-network"></a>Uppdatera DNS-inställningarna för det virtuella Azure-nätverket

När Azure AD DS har distribuerats kan du nu konfigurera det virtuella nätverket så att andra anslutna virtuella datorer och program kan använda den hanterade domänen. För att tillhandahålla den här anslutningen ska du uppdatera DNS-serverinställningarna för ditt virtuella nätverk så att de pekar på de två IP-adresserna där Azure AD DS har distribuerats.

1. På fliken **Översikt** för din hanterade domän visas några **nödvändiga konfigurations steg**. Det första konfigurations steget är att uppdatera DNS-serverinställningarna för ditt virtuella nätverk. När DNS-inställningarna har kon figurer ATS korrekt visas inte längre det här steget.

    Adresserna i listan är domän kontrol Lanterna som används i det virtuella nätverket. I det här exemplet är dessa adresser *10.1.0.4* och *10.1.0.5*. Du kan senare hitta de här IP-adresserna på fliken **Egenskaper** .

    ![Konfigurera DNS-inställningar för ditt virtuella nätverk med Azure AD Domain Services IP-adresser](./media/tutorial-create-instance/configure-dns.png)

1. Om du vill uppdatera DNS-serverinställningar för det virtuella nätverket väljer du knappen **Konfigurera** . DNS-inställningarna konfigureras automatiskt för det virtuella nätverket.

> [!TIP]
> Om du har valt ett befintligt virtuellt nätverk i föregående steg får alla virtuella datorer som är anslutna till nätverket bara de nya DNS-inställningarna efter en omstart. Du kan starta om virtuella datorer med Azure Portal, Azure PowerShell eller Azure CLI.

## <a name="enable-user-accounts-for-azure-ad-ds"></a>Aktivera användar konton för Azure AD DS

För att autentisera användare på den hanterade domänen behöver Azure AD DS lösen ords-hashar i ett format som är lämpligt för NT LAN Manager (NTLM) och Kerberos-autentisering. Azure AD genererar eller lagrar inte lösen ordets hash-värden i det format som krävs för NTLM-eller Kerberos-autentisering förrän du aktiverar Azure AD DS för din klient. Av säkerhets skäl lagrar Azure AD inte heller lösen ords referenser i klartext-format. Därför kan inte Azure AD automatiskt generera dessa NTLM-eller Kerberos-hashvärden utifrån användarnas befintliga autentiseringsuppgifter.

> [!NOTE]
> När det är korrekt konfigurerat lagras de användbara lösen ords hasharna i den hanterade domänen i Azure AD DS. Om du tar bort den hanterade Azure AD DS-domänen, raderas även alla lösen ords-hashar som lagras i den punkten. Det går inte att använda synkroniserad autentiseringsinformation i Azure AD igen om du senare skapar en Azure AD DS-hanterad domän. du måste konfigurera om lösen ordets hash-synkronisering för att lagra lösen ordets hash-meddelanden igen. Tidigare domänanslutna virtuella datorer eller användare kan inte omedelbart autentisera – Azure AD behöver generera och lagra lösen ordets hash-värden i den nya Azure AD DS-hanterade domänen. Mer information finns i [synkronisering av lösen ords-hash för Azure AD DS och Azure AD Connect][password-hash-sync-process].

Stegen för att generera och lagra dessa lösen ords-hashar skiljer sig åt för molnbaserade användar konton som skapats i Azure AD kontra användar konton som synkroniseras från din lokala katalog med hjälp av Azure AD Connect. Ett endast molnbaserat användarkonto är ett konto som skapats i Azure AD-katalogen med antingen Azure Portal eller Azure AD PowerShell-cmdletar. Dessa användar konton synkroniseras inte från en lokal katalog. I den här självstudien får vi arbeta med ett grundläggande användar konto i molnet. Mer information om de ytterligare steg som krävs för att använda Azure AD Connect finns i [Synkronisera lösen ords-hashar för användar konton som synkroniserats från din lokala AD till din hanterade domän][on-prem-sync].

> [!TIP]
> Om din Azure AD-klient har en kombination av endast molnbaserade användare och användare från din lokala AD måste du slutföra båda uppsättningarna.

För endast molnbaserade användar konton måste användare ändra sina lösen ord innan de kan använda Azure AD DS. Den här processen för lösen ords ändring gör att lösen ords hashar för Kerberos-och NTLM-autentisering skapas och lagras i Azure AD. Du kan antingen ange att lösen orden för alla användare i klienten som behöver använda Azure AD DS ska förfalla, vilket tvingar en lösen ords ändring vid nästa inloggning eller instruera dem att manuellt ändra sina lösen ord. I den här självstudien ändrar vi manuellt ett användar lösen ord.

Innan en användare kan återställa sina lösen ord måste Azure AD-klienten [konfigureras för lösen ords återställning via självbetjäning][configure-sspr].

Användaren måste utföra följande steg för att ändra lösen ordet för en endast molnbaserad användare:

1. Gå till sidan för Azure AD Access-panelen på [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. Välj ditt namn i det övre högra hörnet och välj sedan **profil** på den nedrullningsbara menyn.

    ![Välj profil](./media/tutorial-create-instance/select-profile.png)

1. På sidan **profil** väljer du **ändra lösen ord**.
1. På sidan **ändra lösen ord** anger du det befintliga (gamla) lösen ordet och anger och bekräftar sedan ett nytt lösen ord.
1. Välj **Skicka**.

Det tar några minuter efter att du har ändrat ditt lösen ord för att det nya lösen ordet ska kunna användas i Azure AD DS och för att logga in på datorer som är anslutna till den hanterade domänen.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Förstå DNS-krav för en hanterad domän
> * Skapa en Azure AD DS-instans
> * Lägga till administrativa användare till domän hantering
> * Aktivera användar konton för Azure AD DS och generera lösen ords-hashar

Innan du ansluter till virtuella datorer och distribuerar program som använder den hanterade domänen i Azure AD DS konfigurerar du ett virtuellt Azure-nätverk för program arbets belastningar.

> [!div class="nextstepaction"]
> [Konfigurera Azure Virtual Network för program arbets belastningar att använda din hanterade domän](tutorial-configure-networking.md)

<!-- INTERNAL LINKS -->
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[network-considerations]: network-considerations.md
[create-dedicated-subnet]: ../virtual-network/virtual-network-manage-subnet.md#add-a-subnet
[scoped-sync]: scoped-synchronization.md
[on-prem-sync]: tutorial-configure-password-hash-sync.md
[configure-sspr]: ../active-directory/authentication/quickstart-sspr.md
[password-hash-sync-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[skus]: overview.md
[resource-forests]: concepts-resource-forest.md
[availability-zones]: ../availability-zones/az-overview.md

<!-- EXTERNAL LINKS -->
[naming-prefix]: /windows-server/identity/ad-ds/plan/selecting-the-forest-root-domain#selecting-a-prefix
