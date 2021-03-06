---
title: Importera certifikat till en behållare som körs på Azure Service Fabric | Microsoft Docs
description: Lär dig nu att importera certifikatfiler till en Service Fabric container service.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: 496d47c88636b11375d97cc4bc50efd9d468d645
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170470"
---
# <a name="import-a-certificate-file-into-a-container-running-on-service-fabric"></a>Importera en certifikat fil till en behållare som körs på Service Fabric

Du kan skydda behållar tjänsterna genom att ange ett certifikat. Service Fabric tillhandahåller en mekanism för tjänster i en behållare för att få åtkomst till ett certifikat som är installerat på noderna i ett Windows-eller Linux-kluster (version 5,7 eller senare). Certifikatet måste installeras i ett certifikat Arkiv under LocalMachine på alla noder i klustret. Den privata nyckeln som motsvarar certifikatet måste vara tillgänglig, tillgänglig och Windows-exporter bar. Certifikat informationen finns i program manifestet under taggen `ContainerHostPolicies` som följande fragment visar:

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

För Windows-kluster, vid start av programmet, exporterar körningen varje refererat certifikat och dess motsvarande privata nyckel till en PFX-fil som skyddas med ett slumpmässigt genererat lösen ord. Både PFX-och Password-filerna är tillgängliga i behållaren med följande miljövariabler: 

* Certificates_ServicePackageName_CodePackageName_CertName_PFX
* Certificates_ServicePackageName_CodePackageName_CertName_Password

För Linux-kluster kopieras certifikaten (PEM) från det lager som anges av X509StoreName till behållaren. Motsvarande miljövariabler i Linux är:

* Certificates_ServicePackageName_CodePackageName_CertName_PEM
* Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey

Alternativt, om du redan har certifikaten i det obligatoriska formuläret och vill komma åt det i behållaren, kan du skapa ett data paket i ditt appaket och ange följande i program manifestet:

```xml
<ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
  <CertificateRef Name="MyCert1" DataPackageRef="[DataPackageName]" DataPackageVersion="[Version]" RelativePath="[Relative Path to certificate inside DataPackage]" Password="[password]" IsPasswordEncrypted="[true/false]"/>
 ```

Behållar tjänsten eller processen ansvarar för att importera certifikatfiler till behållaren. Om du vill importera certifikatet kan du använda `setupentrypoint.sh`-skript eller köra anpassad kod i container processen. Här är exempel kod i C# för att importera PFX-filen:

```csharp
string certificateFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_PFX");
string passwordFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_Password");
X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
string password = File.ReadAllLines(passwordFilePath, Encoding.Default)[0];
password = password.Replace("\0", string.Empty);
X509Certificate2 cert = new X509Certificate2(certificateFilePath, password, X509KeyStorageFlags.MachineKeySet | X509KeyStorageFlags.PersistKeySet);
store.Open(OpenFlags.ReadWrite);
store.Add(cert);
store.Close();
```
Detta PFX-certifikat kan användas för att autentisera programmet eller tjänsten eller säker kommunikation med andra tjänster. Som standard är filerna endast ACLed till SYSTEM. Du kan ACL-använda den till andra konton som krävs av tjänsten.

I nästa steg ska du läsa följande artiklar:

* [Distribuera en Windows-behållare till Service Fabric på Windows Server 2016](service-fabric-get-started-containers.md)
* [Distribuera en Docker-behållare till Service Fabric på Linux](service-fabric-get-started-containers-linux.md)
