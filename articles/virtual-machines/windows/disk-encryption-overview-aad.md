---
title: Azure Disk Encryption med Azure AD (tidigare version)
description: Den här artikeln innehåller förutsättningar för att använda Microsoft Azure Disk Encryption för virtuella IaaS-datorer.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 33b257e9d344fc31df072509f105d2e8fd1bd29b
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72245180"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Azure Disk Encryption med Azure AD (tidigare version)

**Den nya versionen av Azure Disk Encryption eliminerar kravet på att tillhandahålla en Azure AD-programparameter för att aktivera disk kryptering för virtuella datorer. Med den nya versionen behöver du inte längre ange autentiseringsuppgifter för Azure AD under steget aktivera kryptering. Alla nya virtuella datorer måste krypteras utan Azure AD-programmets parametrar med den nya versionen. Om du vill visa instruktioner för att aktivera disk kryptering för virtuella datorer med den nya versionen, se [Azure Disk Encryption för virtuella Windows-datorer](disk-encryption-overview.md). Virtuella datorer som redan har krypterats med Azure AD-programparametrar stöds fortfarande och bör fortsätta att behållas med AAD-syntaxen.**

Den här artikeln kompletterar [Azure Disk Encryption för virtuella Windows-datorer](disk-encryption-overview.md) med ytterligare krav och krav för Azure Disk Encryption med Azure AD (tidigare version). Avsnittet [virtuella datorer och operativ system som stöds](disk-encryption-overview.md#supported-vms-and-operating-systems) förblir desamma.

## <a name="networking-and-group-policy"></a>Nätverks-och grupprincip

**Om du vill aktivera Azure Disk Encryption-funktionen med hjälp av äldre AAD måste frågesträngparameterns syntax, IaaS-datorer uppfylla följande slutpunkt configuration nätverkskraven:** 
  - Om du vill hämta en token för att ansluta till ditt nyckelvalv, IaaS VM måste vara kan ansluta till en Azure Active Directory-slutpunkt \[login.microsoftonline.com\].
  - Om du vill skriva dem till ditt nyckelvalv kunna IaaS VM ansluta till slutpunkten för nyckelvalvet.
  - IaaS VM måste kunna ansluta till en Azure storage-slutpunkt som är värd för lagringsplatsen Azure-tillägget och ett Azure storage-konto som är värd för VHD-filer.
  -  Om din säkerhetsprincip begränsar åtkomst från virtuella Azure-datorer till Internet, kan du matcha den föregående URI: N och konfigurera en specifik regel som tillåter utgående anslutning till IP-adresserna. Mer information finns i [Azure Key Vault bakom en brandvägg](../../key-vault/key-vault-access-behind-firewall.md).
  - På Windows, om TLS 1.0 har inaktiverats explicit och .NET-versionen har inte uppdaterats till 4.6 eller senare, kommer följande registerändring aktivera ADE att välja den nya TLS-versionen:
    
        [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001

        [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001` 
     

**Grupprincip:**
 - Azure Disk Encryption-lösningen använder de externa nyckelskyddet för BitLocker för Windows virtuella IaaS-datorer. Domänanslutna virtuella datorer, inte skicka någon grupprinciper som tillämpar TPM-skydd. Läs om hur en grupprincip för ”Tillåt BitLocker utan en kompatibel TPM” [gruppolicy referens för BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

-  BitLocker-principen på domänanslutna virtuella datorer med anpassad grup princip måste innehålla följande inställning: [Konfigurera användar lagring av BitLocker-återställningsinformation – > tillåt 256-bitars återställnings nyckel](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption Miss fungerar när anpassade grup princip inställningar för BitLocker inte är kompatibla. Tillämpa den nya principen på datorer som inte har rätt principinställningen tvinga den nya principen för att uppdatera (gpupdate.exe/Force) och sedan omstart kan krävas.  

## <a name="encryption-key-storage-requirements"></a>Lagrings krav för krypterings nyckel  

Azure Disk Encryption kräver ett Azure Key Vault för att kontrol lera och hantera disk krypterings nycklar och hemligheter. Ditt nyckel valv och virtuella datorer måste finnas i samma Azure-region och prenumeration.

Mer information finns i [skapa och konfigurera ett nyckel valv för Azure Disk Encryption med Azure AD (tidigare version)](disk-encryption-key-vault-aad.md).
 
## <a name="next-steps"></a>Nästa steg

- [Skapa och konfigurera ett nyckel valv för Azure Disk Encryption med Azure AD (tidigare version)](disk-encryption-key-vault-aad.md)
- [Aktivera Azure Disk Encryption med Azure AD på virtuella Windows-datorer (tidigare version)](disk-encryption-windows-aad.md)
- [Azure Disk Encryption nödvändiga CLI-skript](https://github.com/ejarvi/ade-cli-getting-started)
- [PowerShell-skript för Azure Disk Encryption krav](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)