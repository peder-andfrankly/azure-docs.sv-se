---
title: Anslut en virtuell Ubuntu-dator till Azure AD Domain Services | Microsoft Docs
description: Lär dig hur du konfigurerar och ansluter till en Ubuntu Linux virtuell dator till en Azure AD Domain Services hanterad domän.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/15/2019
ms.author: iainfou
ms.openlocfilehash: 9fb41b08cb29a68b39fb416b4b7b7bcce9e821dd
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754353"
---
# <a name="join-an-ubuntu-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Ansluta en Ubuntu Linux virtuell dator till en Azure AD Domain Services hanterad domän

För att användarna ska kunna logga in på virtuella datorer i Azure med en enda uppsättning autentiseringsuppgifter, kan du ansluta virtuella datorer till en Azure Active Directory Domain Services (AD DS)-hanterad domän. När du ansluter en virtuell dator till en Azure AD DS-hanterad domän kan användar konton och autentiseringsuppgifter från domänen användas för att logga in och hantera servrar. Grupp medlemskap från den hanterade domänen i Azure AD DS tillämpas också så att du kan kontrol lera åtkomsten till filer och tjänster på den virtuella datorn.

Den här artikeln visar hur du ansluter en Ubuntu Linux virtuell dator till en hanterad Azure AD DS-domän.

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du följande resurser och behörigheter:

* En aktiv Azure-prenumeration.
    * [Skapa ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)om du inte har någon Azure-prenumeration.
* En Azure Active Directory klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog som endast är moln.
    * Om det behövs kan du [skapa en Azure Active Directory klient][create-azure-ad-tenant] eller [associera en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En Azure Active Directory Domain Services hanterad domän aktive rad och konfigurerad i Azure AD-klienten.
    * Vid behov [skapar och konfigurerar][create-azure-ad-ds-instance]den första självstudien en Azure Active Directory Domain Services-instans.
* Ett användar konto som är medlem i *Administratörs gruppen för Azure AD DC* i din Azure AD-klient.

## <a name="create-and-connect-to-an-ubuntu-linux-vm"></a>Skapa och Anslut till en Ubuntu Linux virtuell dator

Om du har en befintlig Ubuntu Linux virtuell dator i Azure ansluter du till den med SSH och fortsätter sedan till nästa steg för att [börja konfigurera den virtuella datorn](#configure-the-hosts-file).

Om du behöver skapa en Ubuntu Linux virtuell dator, eller om du vill skapa en virtuell test dator för användning med den här artikeln, kan du använda någon av följande metoder:

* [Azure-portalen](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

När du skapar den virtuella datorn ska du tänka på inställningarna för det virtuella nätverket för att se till att den virtuella datorn kan kommunicera med den hanterade Azure AD DS-domänen:

* Distribuera den virtuella datorn till samma eller ett peer-kopplat virtuellt nätverk där du har aktiverat Azure AD Domain Services.
* Distribuera den virtuella datorn i ett annat undernät än Azure AD Domain Services-instansen.

När den virtuella datorn har distribuerats följer du stegen för att ansluta till den virtuella datorn med SSH.

## <a name="configure-the-hosts-file"></a>Konfigurera värd filen

För att se till att namnet på den virtuella datorns värd är korrekt konfigurerat för den hanterade domänen redigerar du */etc/hosts* -filen och anger värd namnet:

```console
sudo vi /etc/hosts
```

I *hosts* -filen uppdaterar du adressen till *localhost* . I följande exempel:

* *contoso.com* är DNS-domännamnet för din Azure AD DS-hanterade domän.
* *Ubuntu* är värd namnet för din Ubuntu-VM som du ansluter till den hanterade domänen.

Uppdatera namnen med dina egna värden:

```console
127.0.0.1 ubuntu.contoso.com ubuntu
```

När du är färdig sparar du och avslutar *hosts* -filen med hjälp av `:wq`-kommandot för redigeraren.

## <a name="install-required-packages"></a>Installera de paket som krävs

Den virtuella datorn behöver vissa ytterligare paket för att ansluta den virtuella datorn till den hanterade Azure AD DS-domänen. Om du vill installera och konfigurera de här paketen uppdaterar du och installerar de verktyg för domän anslutning som använder `apt-get`

Under Kerberos-installationen uppmanas du att ange sfär namnet i *krb5-user-* paketet i alla versaler. Om namnet på din Azure AD DS-hanterade domän till exempel är *contoso.com*, anger du *contoso.com* som sfär. Vid installationen skrivs `[realm]` och `[domain_realm]` avsnitt i konfigurations filen för */etc/krb5.conf* . Se till att du anger sfären alla VERSALer:

```console
sudo apt-get update
sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
```

## <a name="configure-network-time-protocol-ntp"></a>Konfigurera Network Time Protocol (NTP)

För att domän kommunikationen ska fungera korrekt måste datum och tid för den virtuella datorn Ubuntu synkroniseras med den hanterade Azure AD DS-domänen. Lägg till din Azure AD DS-hanterade domäns NTP-värdnamn i */etc/NTP.conf* -filen.

1. Öppna filen *NTP. conf* med en redigerare:

    ```console
    sudo vi /etc/ntp.conf
    ```

1. I filen *NTP. conf* skapar du en rad för att lägga till din Azure AD DS-hanterade domäns DNS-namn. I följande exempel läggs en post för *contoso.com* till. Använd ditt eget DNS-namn:

    ```console
    server contoso.com
    ```

    När du är färdig sparar du och avslutar filen *NTP. conf* med hjälp av `:wq`-kommandot för redigeraren.

1. Följande steg krävs för att se till att den virtuella datorn är synkroniserad med den hanterade Azure AD DS-domänen:

    * Stoppa NTP-servern
    * Uppdatera datum och tid från den hanterade domänen
    * Starta NTP-tjänsten

    Utför de här stegen genom att köra följande kommandon. Använd ditt eget DNS-namn med kommandot `ntpdate`:

    ```console
    sudo systemctl stop ntp
    sudo ntpdate contoso.com
    sudo systemctl start ntp
    ```

## <a name="join-vm-to-the-managed-domain"></a>Anslut den virtuella datorn till den hanterade domänen

Nu när de nödvändiga paketen har installerats på den virtuella datorn och NTP har kon figurer ATS, ansluter du den virtuella datorn till den hanterade domänen i Azure AD DS.

1. Använd kommandot `realm discover` för att identifiera den hanterade domänen i Azure AD DS. I följande exempel identifieras sfär- *contoso.com*. Ange ditt eget Azure AD DS-hanterade domän namn med VERSALer:

    ```console
    sudo realm discover CONTOSO.COM
    ```

   Om kommandot `realm discover` inte hittar din Azure AD DS-hanterade domän kan du läsa följande fel söknings steg:

    * Kontrol lera att domänen kan kommas åt från den virtuella datorn. Försök `ping contoso.com` för att se om ett positivt svar returneras.
    * Kontrol lera att den virtuella datorn har distribuerats till samma eller ett peer-kopplat virtuella nätverk där Azure AD DS-hanterad domän är tillgänglig.
    * Bekräfta att DNS-serverinställningarna för det virtuella nätverket har uppdaterats så att de pekar på domän kontrol Lanterna för den hanterade domänen i Azure AD DS.

1. Initiera nu Kerberos med kommandot `kinit`. Ange en användare som tillhör gruppen *AAD DC-administratörer* . Om det behövs [lägger du till ett användar konto i en grupp i Azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Azure AD DS-hanterade domän namnet måste anges i alla VERSALer. I följande exempel används kontot som heter `contosoadmin@contoso.com` för att initiera Kerberos. Ange ditt eget användar konto som är medlem i *Administratörs* gruppen för AAD-domänkontrollant:

    ```console
    kinit contosoadmin@CONTOSO.COM
    ```

1. Slutligen ansluter du datorn till den hanterade Azure AD DS-domänen med hjälp av kommandot `realm join`. Använd samma användar konto som är medlem i *Administratörs gruppen för AAD-domänkontrollanten* som du angav i föregående `kinit` kommando, till exempel `contosoadmin@CONTOSO.COM`:

    ```console
    sudo realm join --verbose CONTOSO.COM -U 'contosoadmin@CONTOSO.COM' --install=/
    ```

Det tar en stund att ansluta den virtuella datorn till den hanterade Azure AD DS-domänen. Följande exempel på utdata visar att den virtuella datorn har anslutit till den hanterade Azure AD DS-domänen:

```output
Successfully enrolled machine in realm
```

Om den virtuella datorn inte kan slutföra processen för domän anslutning, se till att den virtuella datorns nätverks säkerhets grupp tillåter utgående Kerberos-trafik på TCP + UDP-port 464 till det virtuella nätverkets undernät för din Azure AD DS-hanterade domän.

## <a name="update-the-sssd-configuration"></a>Uppdatera SSSD-konfigurationen

Ett av paketen som har installerats i ett tidigare steg var för SSSD (system Security Services daemon). När en användare försöker logga in på en virtuell dator med domänautentiseringsuppgifter, vidarebefordrar SSSD begäran till en autentiseringsprovider. I det här scenariot använder SSSD Azure AD DS för att autentisera begäran.

1. Öppna filen *SSSD. conf* med en redigerare:

    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

1. Kommentera ut raden för *use_fully_qualified_names* enligt följande:

    ```console
    # use_fully_qualified_names = True
    ```

    När du är färdig sparar du och avslutar filen *SSSD. conf* med hjälp av `:wq`-kommandot för redigeraren.

1. Om du vill tillämpa ändringen startar du om SSSD-tjänsten:

    ```console
    sudo service sssd restart
    ```

## <a name="configure-user-account-and-group-settings"></a>Konfigurera inställningar för användar konto och grupper

Med den virtuella datorn ansluten till den Azure AD DS-hanterade domänen och konfigurerad för autentisering, finns det några användar konfigurations alternativ att slutföra. Dessa konfigurations ändringar omfattar att tillåta lösenordsbaserad autentisering och automatiskt skapa hem kataloger på den lokala virtuella datorn när domän användare loggar in för första gången.

### <a name="allow-password-authentication-for-ssh"></a>Tillåt lösenordsautentisering för SSH

Som standard kan användare bara logga in på en virtuell dator med hjälp av offentlig SSH-baserad autentisering. Lösenordsbaserad autentisering Miss lyckas. När du ansluter den virtuella datorn till en Azure AD DS-hanterad domän måste dessa domän konton använda lösenordsbaserad autentisering. Uppdatera SSH-konfigurationen så att den tillåter lösenordsbaserad autentisering enligt följande.

1. Öppna *sshd_conf* -filen med en redigerare:

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. Uppdatera raden för *PasswordAuthentication* till *Ja*:

    ```console
    PasswordAuthentication yes
    ```

    När du är färdig sparar och avslutar du *sshd_conf* -filen med hjälp av kommandot `:wq` för redigeraren.

1. Om du vill tillämpa ändringarna och låta användarna logga in med ett lösen ord, startar du om SSH-tjänsten:

    ```console
    sudo systemctl restart ssh
    ```

### <a name="configure-automatic-home-directory-creation"></a>Konfigurera automatisk skapande av arbets katalog

Om du vill aktivera automatisk generering av hem katalogen när en användare först loggar in, utför du följande steg:

1. Öppna */etc/pam.d/common-session* -filen i en redigerare:

    ```console
    sudo vi /etc/pam.d/common-session
    ```

1. Lägg till följande rad i den här filen under raden `session optional pam_sss.so`:

    ```console
    session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
    ```

    När du är färdig sparar du och avslutar filen *common-session* med hjälp av `:wq`-kommandot för redigeraren.

### <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Bevilja gruppen "AAD DC-administratörer" sudo behörigheter

Om du vill bevilja medlemmar i administratörs gruppen *AAD DC-administratörer* administrativa privilegier på den virtuella datorn Ubuntu lägger du till en post i */etc/sudoers*. När de har lagts till kan medlemmar i *Administratörs* gruppen för AAD-domänkontrollanter använda kommandot `sudo` på den virtuella datorn Ubuntu.

1. Öppna *sudoers* -filen för redigering:

    ```console
    sudo visudo
    ```

1. Lägg till följande post i slutet av */etc/sudoers* -filen:

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

    När du är färdig sparar du och stänger redigeraren med kommandot `Ctrl-X`.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Logga in på den virtuella datorn med ett domän konto

Verifiera att den virtuella datorn har anslutits till den hanterade Azure AD DS-domänen genom att starta en ny SSH-anslutning med ett domän användar konto. Bekräfta att en arbets katalog har skapats och att grupp medlemskapet från domänen används.

1. Skapa en ny SSH-anslutning från-konsolen. Använd ett domän konto som tillhör den hanterade domänen med kommandot `ssh -l`, till exempel `contosoadmin@contoso.com` och ange adressen till den virtuella datorn, till exempel *Ubuntu.contoso.com*. Om du använder Azure Cloud Shell använder du den offentliga IP-adressen för den virtuella datorn i stället för det interna DNS-namnet.

    ```console
    ssh -l contosoadmin@CONTOSO.com ubuntu.contoso.com
    ```

1. När du har anslutit till den virtuella datorn kontrollerar du att arbets katalogen har initierats korrekt:

    ```console
    pwd
    ```

    Du bör vara i katalogen */Home* med din egen katalog som matchar användar kontot.

1. Kontrol lera nu att grupp medlemskapen har lösts korrekt:

    ```console
    id
    ```

    Du bör se dina grupp medlemskap från den hanterade domänen i Azure AD DS.

1. Om du har loggat in på den virtuella datorn som medlem i *Administratörs* gruppen för AAD-domänkontrollanter, kontrollerar du att du kan använda `sudo` kommandot på rätt sätt:

    ```console
    sudo apt-get update
    ```

## <a name="next-steps"></a>Nästa steg

Om du har problem med att ansluta den virtuella datorn till den hanterade domänen i Azure AD DS eller logga in med ett domän konto, se [fel sökning av problem med domän anslutning](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
