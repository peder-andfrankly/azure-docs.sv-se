---
title: 'Snabb start: skapa en iOS-app'
description: I den här snabbstarten lär du dig att skapa en iOS-app med hjälp av Spatial Anchors.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 39732ef785fc610572f917be77dec3f560698fb7
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277193"
---
# <a name="quickstart-create-an-ios-app-with-azure-spatial-anchors-in-either-swift-or-objective-c"></a>Snabb start: skapa en iOS-app med molnbaserade ankare, antingen i Swift eller mål-C

I den här snabbstarten beskrivs hur du skapar en iOS-app med hjälp av [Azure Spatial Anchors](../overview.md) i antingen Swift eller Objective-C. Azure Spatial Anchors är en plattformsoberoende utvecklartjänst som du kan använda för att skapa upplevelser med mixad verklighet med hjälp av objekt som bevarar sin plats mellan enheter över tid. När du är klar har du en ARKit iOS-app som kan spara och återkalla en spatial fästpunkt.

Du lär dig följande:

> [!div class="checklist"]
> * Skapa ett Spatial Anchors-konto
> * Konfigurera konto-ID och kontonyckel för Spatial Anchors
> * Distribuera och köra på en iOS-enhet

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

Kontrollera att du har följande för genomföra den här snabbstarten:

- En utvecklare aktiverade macOS-datorn med den senaste versionen av <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a> och <a href="https://cocoapods.org" target="_blank">CocoaPods</a> installerad.
- Git installerat via HomeBrew. Ange följande kommando i en enskild rad i terminalen: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Kör sedan `brew install git`.
- En utvecklaraktiverad <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">ARKit-kompatibel</a> iOS-enhet.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Öppna exempelprojektet

Använd terminalen för att utföra följande åtgärder.

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Installera nödvändiga poddar med hjälp av CocoaPods:

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

Navigera till `iOS/Swift/`.

```bash
cd ./iOS/Swift/
```

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

Navigera till `iOS/Objective-C/`.

```bash
cd ./iOS/Objective-C/
```

---

Kör `pod install --repo-update` för att installera CocoaPods för projektet.

Öppna nu `.xcworkspace` i Xcode.

> [!NOTE]
> Se fel söknings stegen [här](#cocoapods-issues-on-macos-catalina-1015) om du har CocoaPod problem efter uppgraderingen till MacOS Catalina (10,15).

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

```bash
open ./SampleSwift.xcworkspace
```

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

```bash
open ./SampleObjC.xcworkspace
```

---

## <a name="configure-account-identifier-and-key"></a>Konfigurera kontoidentifierare och nyckel

Nästa steg är att konfigurera appen så att den använder din konto identifierare och konto nyckeln. Du kopierade dem till en text redigerare när du [ställer in resursen för spatiala ankare](#create-a-spatial-anchors-resource).

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

Öppna `iOS/Swift/SampleSwift/ViewControllers/BaseViewController.swift`.

Leta upp fältet `spatialAnchorsAccountKey` och ersätt `Set me` med kontonyckeln.

Leta upp fältet `spatialAnchorsAccountId` och ersätt `Set me` med konto-ID.

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

Öppna `iOS/Objective-C/SampleObjC/BaseViewController.m`.

Leta upp fältet `SpatialAnchorsAccountKey` och ersätt `Set me` med kontonyckeln.

Leta upp fältet `SpatialAnchorsAccountId` och ersätt `Set me` med konto-ID.

---

## <a name="deploy-the-app-to-your-ios-device"></a>Distribuera appen till din iOS-enhet

Ansluta iOS-enheten till Mac-datorn och ange **det aktiva schemat** till iOS-enheten.

![Välja enheten](./media/get-started-ios/select-device.png)

Välj **Build and then run the current scheme** (Skapa och kör sedan det aktuella schemat).

![Distribuera och köra](./media/get-started-ios/deploy-run.png)

> [!NOTE]
> Om du ser felet `library not found for -lPods-SampleObjC` har du sannolikt öppnat filen `.xcodeproj` i stället för `.xcworkspace`. Öppna `.xcworkspace` och försök igen.

I Xcode stoppar du appen genom att trycka på **Stoppa**.

## <a name="troubleshooting"></a>Felsökning

### <a name="cocoapods-issues-on-macos-catalina-1015"></a>CocoaPods problem på macOS Catalina (10,15)

Om du nyligen har uppdaterat till macOS Catalina (10,15) och hade CocoaPods installerat i förväg, kan CocoaPods vara i ett trasigt tillstånd och inte konfigurera poddar och `.xcworkspace` projektfiler korrekt. För att lösa det här problemet måste du installera om CocoaPods genom att köra följande kommandon:

```shell
brew update
brew install cocoapods --build-from-source
brew link --overwrite cocoapods
```

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Självstudie: dela spatiala ankare mellan enheter](../tutorials/tutorial-share-anchors-across-devices.md)
