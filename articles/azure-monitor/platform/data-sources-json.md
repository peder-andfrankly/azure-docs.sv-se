---
title: Samlar in anpassade JSON-data i Azure Monitor | Microsoft Docs
description: Anpassade JSON-datakällor kan samlas in i Azure Monitor med hjälp av Log Analytics-agenten för Linux.  De här anpassade data källorna kan vara enkla skript som returnerar JSON, till exempel sväng eller ett av de populäraste, 300 + plugin-programmen. I den här artikeln beskrivs den konfiguration som krävs för den här data insamlingen.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 11/28/2018
ms.openlocfilehash: c7628badb993c26b989c1fe610d2360ff466de39
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932480"
---
# <a name="collecting-custom-json-data-sources-with-the-log-analytics-agent-for-linux-in-azure-monitor"></a>Samla in anpassade JSON-datakällor med Log Analytics-agenten för Linux i Azure Monitor
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

Anpassade JSON-datakällor kan samlas in i [Azure Monitor](data-platform.md) med hjälp av Log Analytics-agenten för Linux.  De här anpassade data källorna kan vara enkla skript som returnerar JSON, till exempel [sväng](https://curl.haxx.se/) eller ett av de populäraste, [300 + plugin](https://www.fluentd.org/plugins/all)-programmen. I den här artikeln beskrivs den konfiguration som krävs för den här data insamlingen.


> [!NOTE]
> Log Analytics agent för Linux v 1.1.0-217 + krävs för anpassade JSON-data

## <a name="configuration"></a>Konfiguration

### <a name="configure-input-plugin"></a>Konfigurera plugin-programmet för indatamängd

Om du vill samla in JSON-data i Azure Monitor lägger du till `oms.api.` i början av en flytande tagg i ett plugin-program för indata.

Följande är till exempel en separat konfigurations fil `exec-json.conf` i `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`.  Detta använder det Fluent-plugin-`exec` för att köra ett spiral kommando var 30: e sekund.  Utdata från det här kommandot samlas in av JSON-utdata-plugin-programmet.

```
<source>
  type exec
  command 'curl localhost/json.output'
  format json
  tag oms.api.httpresponse
  run_interval 30s
</source>

<match oms.api.httpresponse>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api_httpresponse*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```
Den konfigurations fil som läggs till under `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` måste ha ägarskapet ändrad med följande kommando.

`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/exec-json.conf`

### <a name="configure-output-plugin"></a>Konfigurera plugin-program för utdata 
Lägg till följande plugin-konfiguration för utdata i huvud konfigurationen i `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` eller som en separat konfigurations fil som placerats i `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`

```
<match oms.api.**>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```

### <a name="restart-log-analytics-agent-for-linux"></a>Starta om Log Analytics agent för Linux
Starta om Log Analytics agent för Linux-tjänsten med följande kommando.

    sudo /opt/microsoft/omsagent/bin/service_control restart 

## <a name="output"></a>Resultat
Data samlas in i Azure Monitor med en post typ `<FLUENTD_TAG>_CL`.

Till exempel är den anpassade taggen `tag oms.api.tomcat` i Azure Monitor med en post typ `tomcat_CL`.  Du kan hämta alla poster av den här typen med följande logg fråga.

    Type=tomcat_CL

Kapslade JSON-datakällor stöds, men indexeras baserat på överordnat fält. Till exempel returneras följande JSON-data från en log-fråga som `tag_s : "[{ "a":"1", "b":"2" }]`.

```
{
    "tag": [{
        "a":"1",
        "b":"2"
    }]
}
```


## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [logg frågor](../log-query/log-query-overview.md) för att analysera data som samlas in från data källor och lösningar. 