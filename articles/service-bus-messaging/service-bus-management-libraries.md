---
title: Bibliotek för Azure Service Bus hantering | Microsoft Docs
description: Hantera Service Bus namnrymder och meddelande enheter från .NET.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/05/2019
ms.author: aschhab
ms.openlocfilehash: faf0a5893b7de276b9a411745500daef4d39da6b
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816078"
---
# <a name="service-bus-management-libraries"></a>Service Bus-hanteringsbibliotek

Azure Service Bus hanterings bibliotek kan etablera Service Bus namn områden och entiteter dynamiskt. Detta möjliggör komplexa distributioner och meddelande scenarier och gör det möjligt att program mässigt avgöra vilka entiteter som ska etableras. Dessa bibliotek är tillgängliga för .NET.

## <a name="supported-functionality"></a>Funktioner som stöds

* Skapa en Namespace-, update-, borttagning
* Skapa kö, uppdatera, ta bort
* Att skapa, uppdatera och ta bort ämnen
* Skapa prenumeration, uppdatera, ta bort

## <a name="prerequisites"></a>Förutsättningar

För att komma igång med Service Bus hanterings bibliotek måste du autentisera med tjänsten Azure Active Directory (Azure AD). Azure AD kräver att du autentiseras som tjänstens huvud namn, vilket ger åtkomst till dina Azure-resurser. Information om hur du skapar ett tjänstens huvudnamn finns i någon av följande artiklar:  

* [Använd Azure-portalen för att skapa Active Directory-program och tjänstens huvudnamn som kan komma åt resurser](/azure/azure-resource-manager/resource-group-create-service-principal-portal)
* [Använd Azure PowerShell för att skapa ett huvudnamn för tjänsten för resursåtkomst](/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Använd Azure CLI för att skapa ett huvudnamn för tjänsten för resursåtkomst](/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)

De här självstudierna får du en `AppId` (klient-ID), `TenantId`, och `ClientSecret` (autentiseringsnyckeln) som används för autentisering av hanteringsbiblioteken. Du måste ha **ägar** behörigheter för den resurs grupp som du vill köra.

## <a name="programming-pattern"></a>Mönster för programmering

Mönstret för att manipulera eventuella Service Bus resurser följer ett gemensamt protokoll:

1. Hämta en token från Azure AD med hjälp av **Microsoft. IdentityModel. clients. ActiveDirectory** -biblioteket:
   ```csharp
   var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

   var result = await context.AcquireTokenAsync("https://management.azure.com/", new ClientCredential(clientId, clientSecret));
   ```
2. `ServiceBusManagementClient` Skapa objektet:

   ```csharp
   var creds = new TokenCredentials(token);
   var sbClient = new ServiceBusManagementClient(creds)
   {
       SubscriptionId = SettingsCache["SubscriptionId"]
   };
   ```
3. Ange de `CreateOrUpdate` angivna värdena som parametrar:

   ```csharp
   var queueParams = new QueueCreateOrUpdateParameters()
   {
       Location = SettingsCache["DataCenterLocation"],
       EnablePartitioning = true
   };
   ```
4. Kör anropet:

   ```csharp
   await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, QueueName, queueParams);
   ```

## <a name="complete-code-to-create-a-queue"></a>Slutför kod för att skapa en kö
Här är den fullständiga koden för att skapa en Service Bus kö: 

```csharp
using System;
using System.Threading.Tasks;

using Microsoft.Azure.Management.ServiceBus;
using Microsoft.Azure.Management.ServiceBus.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;

namespace SBusADApp
{
    class Program
    {
        static void Main(string[] args)
        {
            CreateQueue().GetAwaiter().GetResult();
        }

        private static async Task CreateQueue()
        {
            try
            {
                var subscriptionID = "<SUBSCRIPTION ID>";
                var resourceGroupName = "<RESOURCE GROUP NAME>";
                var namespaceName = "<SERVICE BUS NAMESPACE NAME>";
                var queueName = "<NAME OF QUEUE YOU WANT TO CREATE>";

                var token = await GetToken();

                var creds = new TokenCredentials(token);
                var sbClient = new ServiceBusManagementClient(creds)
                {
                    SubscriptionId = subscriptionID,
                };

                var queueParams = new SBQueue();

                Console.WriteLine("Creating queue...");
                await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, queueName, queueParams);
                Console.WriteLine("Created queue successfully.");
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not create a queue...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

        private static async Task<string> GetToken()
        {
            try
            {
                var tenantId = "<AZURE AD TENANT ID>";
                var clientId = "<APPLICATION/CLIENT ID>";
                var clientSecret = "<CLIENT SECRET>";

                var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

                var result = await context.AcquireTokenAsync(
                    "https://management.azure.com/",
                    new ClientCredential(clientId, clientSecret)
                );

                // If the token isn't a valid string, throw an error.
                if (string.IsNullOrEmpty(result.AccessToken))
                {
                    throw new Exception("Token result is empty!");
                }

                return result.AccessToken;
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not get a token...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

    }
}
```

> [!IMPORTANT]
> Ett fullständigt exempel finns i [.net Management-exemplet på GitHub](https://github.com/Azure-Samples/service-bus-dotnet-management/). 

## <a name="next-steps"></a>Nästa steg
[API-referens för Microsoft. Azure. Management. Service Bus](/dotnet/api/Microsoft.Azure.Management.ServiceBus)
