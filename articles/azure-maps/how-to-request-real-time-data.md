---
title: Så här begär du real tids data i Azure Maps | Microsoft Docs
description: Begär real tids data med tjänsten Azure Maps Mobility.
author: walsehgal
ms.author: v-musehg
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 4c53d1c1ffbc80e694a9a7b423b2aaf9c6d38b48
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914384"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>Begär real tids data med tjänsten Azure Maps Mobility

Den här artikeln visar hur du använder Azure Maps [mobilitets tjänst](https://aka.ms/AzureMapsMobilityService) för att begära överförings data i real tid.

I den här artikeln får du lära dig att:


 * Begär nästa mottagna real tid för alla rader som kommer vid det aktuella stoppet
 * Begär real tids information för en specifik cykel docknings Station.


## <a name="prerequisites"></a>Förutsättningar

Om du vill göra anrop till API: erna för Azure Maps offentlig överföring behöver du ett Maps-konto och nyckel. Om du vill ha information om hur du skapar ett konto följer du anvisningarna i [Hantera konto](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account) och följer stegen i [Hämta primär nyckel](./tutorial-search-location.md#getkey) för att hämta en primär prenumerations nyckel för ditt konto.

I den här artikeln används [Postman-appen](https://www.getpostman.com/apps) för att bygga rest-anrop. Du kan använda valfri API utvecklings miljö som du föredrar.


## <a name="request-real-time-arrivals-for-a-stop"></a>Begär real tids mottagningar för ett stopp

För att kunna begära att data tas emot i real tid för en viss offentlig överförings stopp, måste du göra en begäran till [real tids ingångs-API: t](https://aka.ms/AzureMapsMobilityRealTimeArrivals) för [tjänsten Azure Maps mobilitet](https://aka.ms/AzureMapsMobilityService). Du behöver **metroID** och **stopID** för att slutföra begäran. Mer information om hur du begär dessa parametrar finns i vår instruktions guide för att [begära offentliga överförings vägar](https://aka.ms/AMapsHowToGuidePublicTransitRouting). 

Vi använder "522" som vårt tunnelbane-ID, som är Metro-ID för "Seattle – Tacoma – Bellevue, WA" och använder stopp-ID: t "522---2060603", som är ett buss avbrott på "ne 24 st & 162nd Ave Ne, Bellevue WA". Om du vill begära nästa fem real tids mottagna data för alla nästa Live-ankomst i detta steg, utför följande steg:

1. Skapa en samling där förfrågningarna ska lagras. I Postman-appen väljer du **ny**. I fönstret **Skapa nytt** väljer du **samling**. Namnge samlingen och välj knappen **skapa** .

2. Välj **nytt** om du vill skapa en begäran. I fönstret **Skapa nytt** väljer du **begäran**. Ange ett **namn** för begäran, Välj den samling som du skapade i föregående steg som den plats där du vill spara begäran och välj sedan **Spara**.

    ![Skapa en begäran i Postman](./media/how-to-request-transit-data/postman-new.png)

3. Välj metoden Hämta HTTP på fliken Builder och ange följande URL för att skapa en GET-begäran.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. Efter en lyckad begäran visas följande svar.  Observera att parametern "scheduleType" definierar om den uppskattade införsel tiden baseras på real tids data eller statiska data.

    ```JSON
    {
        "results": [
            {
                "arrivalMinutes": 8,
                "scheduleType": "realTime",
                "patternId": "522---4143196",
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            },
            {
                "arrivalMinutes": 25,
                "scheduleType": "realTime",
                "patternId": "522---3510227",
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            }
        ]
    }
    ```


## <a name="real-time-data-for-bike-docking-station"></a>Real tids data för cykel docknings Station

API för att [Hämta information om transport](https://aka.ms/AzureMapsMobilityTransitDock) sidan i Azure Maps Mobility-tjänsten gör det möjligt att begära statisk och real tids information, till exempel tillgänglighet och vakans-information för en specifik cykel eller scooter docknings Station. Vi kommer att göra en begäran om att hämta real tids data för en docknings station för cyklar.

Du behöver **dockId** för den stationen för att kunna göra en begäran till informations-API: t för överförings tjänsten. Du kan hämta dock-ID genom att göra en sökbegäran till [API för att hämta närbelägen i närheten](https://aka.ms/AzureMapsMobilityNearbyTransit) och ange parametern **ObjectType** till "bikeDock". Följ stegen nedan för att hämta real tids data för en docknings station för cyklar.


### <a name="get-dock-id"></a>Hämta dock-ID

Om du vill hämta **dockID**följer du stegen nedan för att göra en begäran till API: et för närliggande överföring:

1. Klicka på **ny begäran** | **Get-begäran** i Postman och ge den ett **dock-ID**.

2.  På fliken Builder väljer du metoden **Hämta** http, anger följande URL för begäran och klickar på **Skicka**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=121&query=40.7663753,-73.9627498&radius=100&objectType=bikeDock
    ```

3. Efter en lyckad begäran visas följande svar. Observera att vi nu har **ID: t** i svaret, som kan användas senare som en frågeparameter i begäran till API: et för att hämta överförings information.

    ```JSON
    {
        "results": [
            {
                "id": "121---4640799",
                "type": "bikeDock",
                "objectDetails": {
                    "availableVehicles": 0,
                    "vacantLocations": 31,
                    "lastUpdated": "2019-09-07T00:55:19Z",
                    "operatorInfo": {
                        "id": "121---80",
                        "name": "Citi Bike"
                    }
                },
                "position": {
                    "latitude": 40.767128,
                    "longitude": -73.962243
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 40.768039,
                        "longitude": -73.963413
                    },
                    "btmRightPoint": {
                        "latitude": 40.766216,
                        "longitude": -73.961072
                    }
                }
            }
        ]
    }
    ```


### <a name="get-real-time-bike-dock-status"></a>Hämta status för cykel Docking i real tid

Följ stegen nedan för att göra en begäran till API: t för att hämta överförings information för att hämta real tids data för den valda dock.

1. I Postman klickar du på **ny begäran** | **Hämta begäran** och namnger den för att **få data i real tid**.

2.  På fliken Builder väljer du metoden **Hämta** http, anger följande URL för begäran och klickar på **Skicka**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/dock/json?subscription-key={subscription-key}&api-version=1.0&query=121---4640799
    ```

3. Efter en lyckad begäran kommer du att få ett svar på följande struktur:

    ```JSON
    {
        "availableVehicles": 0,
        "vacantLocations": 31,
        "position": {
            "latitude": 40.767128,
            "longitude": -73.962246
        },
        "lastUpdated": "2019-09-07T00:55:19Z",
        "operatorInfo": {
            "id": "121---80",
            "name": "Citi Bike"
        }
    }
    ```


## <a name="next-steps"></a>Nästa steg

Lär dig hur du begär överförings data med mobilitets tjänsten:

> [!div class="nextstepaction"]
> [Så här begär du överförings data](how-to-request-transit-data.md)

Utforska dokumentationen för Azure Maps Mobility Service API:

> [!div class="nextstepaction"]
> [API-dokumentation för Mobility Service](https://aka.ms/AzureMapsMobilityService)
