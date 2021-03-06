---
title: Så här distribuerar du OPC, sammanflätad modul för Azure från grunden | Microsoft Docs
description: Den här artikeln beskriver hur du distribuerar OPC från grunden med hjälp av Azure Portal IoT Edge-bladet och även med AZ CLI.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 96a4afff3e58bfa1ebf661909f380aa525fea76e
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820140"
---
# <a name="deploy-opc-twin-module-and-dependencies-from-scratch"></a>Distribuera OPC, sammanflätade moduler och beroenden från grunden

Den OPC dubbla modulen körs på IoT Edge och ger flera Edge-tjänster till OPC-enhetens dubbla och register tjänster. 

Det finns flera alternativ för att distribuera moduler till din [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) -Gateway, bland dem

- [Distribuera från Azure Portal IoT Edge bladet](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal)
- [Distribuera med AZ CLI](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor-cli)

> [!NOTE]
> Mer information om distributions information och instruktioner finns i GitHub- [lagringsplatsen](https://github.com/Azure/azure-iiot-components).

## <a name="deployment-manifest"></a>Distributionsmanifest

Alla moduler distribueras med hjälp av ett distributions manifest.  Ett exempel på ett manifest för att distribuera både [OPC-utgivare](https://github.com/Azure/iot-edge-opc-publisher) och [OPC-dubbla](https://github.com/Azure/azure-iiot-opc-twin-module) visas nedan.

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "runtime": {
            "type": "docker",
            "settings": {
              "minDockerVersion": "v1.25",
              "loggingOptions": "",
              "registryCredentials": {}
            }
          },
          "systemModules": {
            "edgeAgent": {
              "type": "docker",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                "createOptions": ""
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "opctwin": {
              "version": "1.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/iotedge/opc-twin:latest",
                "createOptions": "{\"NetworkingConfig\": {\"EndpointsConfig\": {\"host\": {}}}, \"HostConfig\": {\"NetworkMode\": \"host\" }}"
              }
            },
            "opcpublisher": {
              "version": "2.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/iotedge/opc-publisher:latest",
                "createOptions": "{\"Hostname\":\"publisher\",\"Cmd\":[\"publisher\",\"--pf=./pn.json\",\"--di=60\",\"--to\",\"--aa\",\"--si=0\",\"--ms=0\"],\"ExposedPorts\":{\"62222/tcp\":{}},\"NetworkingConfig\":{\"EndpointsConfig\":{\"host\":{}}},\"HostConfig\":{\"NetworkMode\":\"host\",\"PortBindings\":{\"62222/tcp\":[{\"HostPort\":\"62222\"}]}}}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
            "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      }
    }
  }
}
```

## <a name="deploying-from-azure-portal"></a>Distribuera från Azure Portal

Det enklaste sättet att distribuera modulerna till en Azure IoT Edge gateway-enhet är genom Azure Portal.  

### <a name="prerequisites"></a>Nödvändiga komponenter

1. Distribuera OPC dubbla [beroenden](howto-opc-twin-deploy-dependencies.md) och hämta den resulterande `.env` filen. Observera den distribuerade `hub name` för `PCS_IOTHUBREACT_HUB_NAME`-variabeln i den resulterande `.env`-filen.

2. Registrera och starta en [Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) -eller [Windows](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-windows) IoT Edge-gateway och anteckna dess `device id`.

### <a name="deploy-to-an-edge-device"></a>Distribuera till en Edge-enhet

1. Logga in på [Azure Portal](https://portal.azure.com/) och navigera till din IoT-hubb.

2. Välj **IoT Edge** på menyn till vänster.

3. Klicka på mål enhetens ID i listan över enheter.

4. Välj **Ange moduler**.

5. I avsnittet **distributions moduler** på sidan väljer du **lägg till** och **IoT Edge modul.**

6. I dialog rutan **IoT Edge anpassad modul** använder du `opctwin` som namn för modulen och anger sedan URI för behållar *avbildningen* som

   ```bash
   mcr.microsoft.com/iotedge/opc-twin:latest
   ```

   Använd följande JSON som *behållar alternativ för att skapa behållare*:

   ```json
   {"NetworkingConfig": {"EndpointsConfig": {"host": {}}}, "HostConfig": {"NetworkMode": "host" }}
   ```

   Fyll i de valfria fälten om det behövs. För ytterligare information om behållar skapande alternativ, starta om princip och önskad status, se [EdgeAgent önskade egenskaper](https://docs.microsoft.com/azure/iot-edge/module-edgeagent-edgehub#edgeagent-desired-properties). Mer information om modulen finns i [definiera eller uppdatera önskade egenskaper](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties).

7. Välj **Spara** och upprepa steg **5**.  

8. I dialog rutan IoT Edge anpassad modul använder du `opcpublisher` som namn för modulen och behållar *avbildningens URI* som 

   ```bash
   mcr.microsoft.com/iotedge/opc-publisher:latest
   ```

   Använd följande JSON som *behållar alternativ för att skapa behållare*:

   ```json
   {"Hostname":"publisher","Cmd":["publisher","--pf=./pn.json","--di=60","--to","--aa","--si=0","--ms=0"],"ExposedPorts":{"62222/tcp":{}},"HostConfig":{"PortBindings":{"62222/tcp":[{"HostPort":"62222"}] }}}
   ```

9. Välj **Spara** och **Nästa** för att fortsätta till avsnittet vägar.

10. På fliken vägar klistrar du in följande 

    ```json
    {
      "routes": {
        "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
        "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
      }
    }
    ```

    och välj **Nästa**

11. Granska distributions informationen och manifestet.  Den bör se ut som ovan distributions manifestet.  Välj **Skicka**.

12. När du har distribuerat moduler till din enhet kan du Visa alla på **enhets informations** sidan i portalen. Den här sidan visar namnet på varje distribuerad modul, samt användbar information, till exempel distributions status och avslutnings kod.

## <a name="deploying-using-azure-cli"></a>Distribuera med Azure CLI

### <a name="prerequisites"></a>Nödvändiga komponenter

1. Installera den senaste versionen av [Azures kommando rads gränssnitt (AZ)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) [här](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="quickstart"></a>Snabbstart

1. Spara distributions manifestet ovan i en `deployment.json`-fil.  

2. Använd följande kommando för att tillämpa konfigurationen på en IoT Edge enhet:

   ```bash
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content ./deployment.json
   ```

   Parametern `device id` är Skift läges känslig. Innehålls parametern pekar på den distributions manifest fil som du sparade. 
    ![AZ IoT Edge set-modules utdata](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/set-modules.png)

3. När du har distribuerat moduler till din enhet kan du Visa alla med följande kommando:

   ```bash
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

   Parametern för enhets-ID är Skift läges känslig. ![AZ IoT Hub-modul – utdata för identitets lista](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du distribuerar OPC från grunden är det föreslagna nästa steg:

> [!div class="nextstepaction"]
> [Distribuera OPC-dubbla till ett befintligt projekt](howto-opc-twin-deploy-existing.md)
