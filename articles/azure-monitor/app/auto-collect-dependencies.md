---
title: Azure Application Insights-beroende automatisk insamling | Microsoft Docs
description: Application Insights automatiskt samla in och visualisera beroenden
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: reference
author: nikmd23
ms.author: nimolnar
ms.date: 04/29/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6fe43eb5963f2918f41ad15f1904f883b5e9c298
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678305"
---
# <a name="dependency-auto-collection"></a>Automatisk insamling för beroenden

Nedan visas en lista över beroende anrop som identifieras automatiskt som beroenden, utan att det krävs någon ytterligare ändring av programmets kod. Dessa beroenden visualiseras i vyerna Application Insights [program karta](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) och [transaktions-diagnostik](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) . Om ditt beroende inte finns med i listan nedan kan du fortfarande spåra det manuellt med ett [spår beroende anrop](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

## <a name="net"></a>.NET

| App-ramverk| Versioner |
| ------------------------|----------|
| ASP.NET-webbformulär | 4,5 + |
| ASP.NET MVC | 4 + |
| ASP.NET WebAPI | 4,5 + |
| ASP.NET Core | 1.1 + |
| <b>Kommunikations bibliotek</b> |
| [HttpClient](https://www.microsoft.com/net/) | 4,5 +, .NET core 1.1 + |
| [SqlClient](https://www.nuget.org/packages/System.Data.SqlClient) | .NET core 1.0 +, NuGet 4.3.0 |
| [EventHubs-klient-SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1.1.0 |
| [Service Bus-klient-SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) | 3.0.0 |
| <b>Lagrings klienter</b>|  |
| ADO.NET | 4,5 + |

## <a name="java"></a>Java
| App-servrar | Versioner |
|-------------|----------|
| [tomcat](https://tomcat.apache.org/) | 7, 8 | 
| [JBoss-EAP](https://developers.redhat.com/products/eap/download/) | 6, 7 |
| [Jetty](https://www.eclipse.org/jetty/) | 9 |
| <b>App-ramverk</b> |  |
| [Spring](https://spring.io/) | 3.0 |
| [Våren Boot](https://spring.io/projects/spring-boot) | 1.5.9 +<sup> *</sup> |
| Java-servlet | 3.1 + |
| <b>Kommunikations bibliotek</b> |  |
| [Apache HTTP-klient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) | 4.3 +<sup>†</sup> |
| <b>Lagrings klienter</b> | |
| [SQL Server]( https://mvnrepository.com/artifact/com.microsoft.sqlserver/mssql-jdbc) | 1 +<sup>†</sup> |
| [PostgreSQL (beta support)](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/CHANGELOG.md#version-240-beta) | |
| [Oracle]( https://www.oracle.com/technetwork/database/application-development/jdbc/downloads/index.html) | 1 +<sup>†</sup> |
| [MySql]( https://mvnrepository.com/artifact/mysql/mysql-connector-java) | 1 +<sup>†</sup> |
| <b>Loggnings bibliotek</b> | |
| [Logback](https://logback.qos.ch/) | 1 + |
| [Log4j](https://logging.apache.org/log4j/) | 1.2 + |
| <b>Mått bibliotek</b> |  |
| JMX | 1.0 + |

> [!NOTE]
> \* Förutom stöd för omaktivering av program.
> <br>† Kräver installation av [JVM-agenten](https://docs.microsoft.com/azure/application-insights/app-insights-java-agent#install-the-application-insights-agent-for-java).

## <a name="nodejs"></a>Node.js

| Kommunikations bibliotek | Versioner |
| ------------------------|----------|
| [http](https://nodejs.org/api/http.html), [https](https://nodejs.org/api/https.html) | 0,10 + |
| <b>Lagrings klienter</b> | |
| [Redis](https://www.npmjs.com/package/redis) | 2x |
| [MongoDB](https://www.npmjs.com/package/mongodb); [MongoDB-kärna](https://www.npmjs.com/package/mongodb-core) | 2. x-3. x |
| [MySQL](https://www.npmjs.com/package/mysql) | 2.0.0 – 2.16. x |
| [PostgreSql](https://www.npmjs.com/package/pg); | 6. x-7. x |
| [PG-pool](https://www.npmjs.com/package/pg-pool) | 1. x-2. x |
| <b>Loggnings bibliotek</b> | |
| [konsolomdirigering](https://nodejs.org/api/console.html) | 0,10 + |
| [Bunyan](https://www.npmjs.com/package/bunyan) | 1.x |
| [Winston](https://www.npmjs.com/package/winston) | 2. x-3. x |

## <a name="javascript"></a>JavaScript

| Kommunikations bibliotek | Versioner |
| ------------------------|----------|
| [XMLHttpRequest](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) | Alla |

## <a name="next-steps"></a>Nästa steg

- Konfigurera anpassad beroende spårning för [.net](../../azure-monitor/app/asp-net-dependencies.md).
- Konfigurera anpassad beroende spårning för [Java](../../azure-monitor/app/java-agent.md).
- [Skriv anpassad beroende telemetri](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- Se [data modell](../../azure-monitor/app/data-model.md) för Application Insights typer och data modell.
- Kolla ut [plattformar](../../azure-monitor/app/platforms.md) som stöds av Application Insights.
