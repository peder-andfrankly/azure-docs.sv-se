---
title: Installera MySQL på en virtuell OpenSUSE-dator i Azure
description: Lär dig att installera MySQL på en virtuell OpenSUSE Linux-dator i Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 1594e10e-c314-455a-9efb-a89441de364b
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 07/11/2018
ms.author: cynthn
ms.openlocfilehash: d5f2ef2d82cbcced6202ad2c09f23dd734d373b3
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035587"
---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>Installera MySQL på en virtuell dator som kör OpenSUSE Linux i Azure

[MySQL](https://www.mysql.com) är en populär SQL-databas med öppen källkod. Den här självstudien visar hur du skapar en virtuell dator som kör OpenSUSE Linux och sedan installerar MySQL.


Om du väljer att installera och använda CLI-gränssnittet lokalt måste du ha Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>Skapa en virtuell dator som kör OpenSUSE Linux

Skapa först en resurs grupp. I det här exemplet heter resurs gruppen *mySQSUSEResourceGroup* och skapas i regionen *USA, östra* .

```azurecli-interactive
az group create --name mySQLSUSEResourceGroup --location eastus
```

Skapa den virtuella datorn. I det här exemplet heter den virtuella datorn *myVM* och storleken på den virtuella datorn är *Standard_D2s_v3*, men du bör välja den [VM-storlek](sizes.md) som du tror är mest lämplig för din arbets belastning.

```azurecli-interactive
az vm create --resource-group mySQLSUSEResourceGroup \
   --name myVM \
   --image openSUSE-Leap \
   --size Standard_D2s_v3 \
   --generate-ssh-keys
```

Du måste också lägga till en regel i nätverks säkerhets gruppen för att tillåta trafik över Port 3306 för MySQL.

```azurecli-interactive
az vm open-port --port 3306 --resource-group mySQLSUSEResourceGroup --name myVM
```

## <a name="connect-to-the-vm"></a>Anslut till VM:en

Du använder SSH för att ansluta till den virtuella datorn. I det här exemplet är den offentliga IP-adressen för den virtuella datorn *10.111.112.113*. Du kan se IP-adressen i utdata när du skapade den virtuella datorn.

```azurecli-interactive  
ssh 10.111.112.113
```

 
## <a name="update-the-vm"></a>Uppdatera den virtuella datorn
 
När du är ansluten till den virtuella datorn installerar du system uppdateringar och uppdateringar. 
   
```bash
sudo zypper update
```

Följ anvisningarna för att uppdatera den virtuella datorn.

## <a name="install-mysql"></a>Installera MySQL 


Installera MySQL på den virtuella datorn via SSH. Svara på begär Ande vid behov.

```bash
sudo zypper install mysql
```
 
Ange att MySQL ska starta när systemet startas. 

```bash
sudo systemctl enable mysql
```
Kontrol lera att MySQL är aktiverat.

```bash
systemctl is-enabled mysql
```

Detta bör returnera: aktiverat.

Starta om servern.

```bash
sudo reboot
```


## <a name="mysql-password"></a>MySQL-lösenord

Efter installationen är MySQL-rot lösen ordet tomt som standard. Kör **installations skriptet mysql\_secure\_** för att skydda MySQL. Skriptet gör att du kan ändra MySQL-rot lösen ordet, ta bort anonyma användar konton, inaktivera fjärrinloggning, ta bort test databaser och läsa in behörighets tabellen igen. 

När servern har startats om, SSH till den virtuella datorn igen.

```azurecli-interactive  
ssh 10.111.112.113
```



```bash
mysql_secure_installation
```

## <a name="sign-in-to-mysql"></a>Logga in på MySQL

Nu kan du logga in och ange MySQL-prompten.

```bash  
mysql -u root -p
```
Detta växlar till MySQL-prompten där du kan utfärda SQL-uttryck för att interagera med databasen.

Skapa nu en ny MySQL-användare.

```sql
CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Semikolon (;) i slutet av raden är det viktigt att du avslutar kommandot.


## <a name="create-a-database"></a>Skapa en databas


Skapa en databas och ge `mysqluser` användar behörighet.

```sql
CREATE DATABASE testdatabase;
GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Användar namn och lösen ord för databasen används endast av skript som ansluter till databasen.  Databasens användar konto namn representerar inte nödvändigt vis faktiska användar konton i systemet.

Aktivera inloggning från en annan dator. I det här exemplet är IP-adressen till datorn som tillåter inloggning från *10.112.113.114*.

```sql
GRANT ALL ON testdatabase.* TO 'mysqluser'@'10.112.113.114' IDENTIFIED BY 'password';
```
   
Om du vill avsluta administrations verktyget för MySQL-databasen skriver du:

```    
quit
```


## <a name="next-steps"></a>Nästa steg
Mer information om MySQL finns i [MySQL-dokumentationen](https://dev.mysql.com/doc).




