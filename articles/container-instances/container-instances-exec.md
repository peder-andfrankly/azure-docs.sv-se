---
title: Kör kommandon i kör behållar instans
description: Lär dig hur du kör ett kommando i en behållare som för närvarande körs i Azure Container Instances
ms.topic: article
ms.date: 03/30/2018
ms.openlocfilehash: 10d0ea0c2dfa60aad64d0ae11532aff24a7ce773
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74481585"
---
# <a name="execute-a-command-in-a-running-azure-container-instance"></a>Köra ett kommando i en Azure Container instance som körs

Azure Container Instances stöder körning av ett kommando i en behållare som körs. Att köra ett kommando i en behållare som du redan har startat är särskilt användbart vid program utveckling och fel sökning. Den vanligaste användningen av den här funktionen är att starta ett interaktivt gränssnitt så att du kan felsöka problem i en behållare som körs.

## <a name="run-a-command-with-azure-cli"></a>Köra ett kommando med Azure CLI

Köra ett kommando i en behållare som körs med [AZ container exec][az-container-exec] i [Azure CLI][azure-cli]:

```azurecli
az container exec --resource-group <group-name> --name <container-group-name> --exec-command "<command>"
```

Till exempel för att starta ett bash-gränssnitt i en nginx-behållare:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
```

I exemplet nedan startas bash-gränssnittet i en Linux-behållare med en terminal där `ls` körs:

```console
$ az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# exit
exit
Bye.
```

I det här exemplet startas kommando tolken i en Nanoserver-behållare som körs:

```console
$ az container exec --resource-group myResourceGroup --name myiis --exec-command "cmd.exe"
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

C:\>dir
 Volume in drive C has no label.
 Volume Serial Number is 76E0-C852

 Directory of C:\

03/23/2018  09:13 PM    <DIR>          inetpub
11/20/2016  11:32 AM             1,894 License.txt
03/23/2018  09:13 PM    <DIR>          Program Files
07/16/2016  12:09 PM    <DIR>          Program Files (x86)
03/13/2018  08:50 PM           171,616 ServiceMonitor.exe
03/23/2018  09:13 PM    <DIR>          Users
03/23/2018  09:12 PM    <DIR>          var
03/23/2018  09:22 PM    <DIR>          Windows
               2 File(s)        173,510 bytes
               6 Dir(s)  21,171,609,600 bytes free

C:\>exit
Bye.
```

## <a name="multi-container-groups"></a>Grupper med flera container

Om [behållar gruppen](container-instances-container-groups.md) har flera behållare, till exempel en program behållare och en loggning av sidvagn, anger du namnet på behållaren där kommandot ska köras med `--container-name`.

I behållar gruppen *mynginx* finns till exempel två behållare, *nginx-app* och *loggar*. Starta ett gränssnitt i behållaren *nginx-app* :

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --container-name nginx-app --exec-command "/bin/bash"
```

## <a name="restrictions"></a>Begränsningar

Azure Container Instances stöder för närvarande att starta en enda process med [AZ container exec][az-container-exec]och du kan inte skicka kommando argument. Du kan till exempel inte kedje kommandon som i `sh -c "echo FOO && echo BAR"`eller köra `echo FOO`.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om andra fel söknings verktyg och vanliga distributions problem i [fel sökning av problem med behållare och distribution i Azure Container instances](container-instances-troubleshooting.md).

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[azure-cli]: /cli/azure