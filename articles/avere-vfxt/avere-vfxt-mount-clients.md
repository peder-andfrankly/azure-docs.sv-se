---
title: Montera AVERT vFXT – Azure
description: Montera klienter med AVERT vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: c461b379629927e8f367fad9bfc70b87413f47b7
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255379"
---
# <a name="mount-the-avere-vfxt-cluster"></a>Montera Avere vFXT-klustret  

Följ de här stegen för att ansluta klient datorer till ditt vFXT-kluster.

1. Bestäm hur du ska belastningsutjämna klient trafik mellan klusternoderna. Läs [utjämning klient belastning](#balance-client-load)nedan för mer information. 
1. Identifiera IP-adressen och kopplings Sök vägen som ska monteras.
1. Utfärda [monterings kommandot](#mount-command-arguments)med lämpliga argument.

## <a name="balance-client-load"></a>Utjämna klient belastning

För att balansera klient begär Anden mellan alla noder i klustret, bör du montera klienter till alla IP-adresser som klienten riktas mot. Det finns flera enkla sätt att automatisera den här uppgiften.

> [!TIP] 
> Andra metoder för belastnings utjämning kan vara lämpliga för stora eller komplicerade system. [öppna ett support ärende](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) om du behöver hjälp.)
> 
> Om du föredrar att använda en DNS-server för automatisk belastnings utjämning på Server sidan måste du konfigurera och hantera din egen DNS-server i Azure. I så fall kan du konfigurera resursallokering (Round-Robin) för vFXT-klustret enligt detta dokument: [AVERT kluster-DNS-konfiguration](avere-vfxt-configure-dns.md).

### <a name="sample-balanced-client-mounting-script"></a>Exempel på balanserat klient monterings skript

I det här kod exemplet används klient-IP-adresser som ett slumpmässigt element för att distribuera klienter till alla vFXT-klustrets tillgängliga IP-adresser.

```bash
function mount_round_robin() {
    # to ensure the nodes are spread out somewhat evenly the default 
    # mount point is based on this node's IP octet4 % vFXT node count.
    declare -a AVEREVFXT_NODES="($(echo ${NFS_IP_CSV} | sed "s/,/ /g"))"
    OCTET4=$((`hostname -i | sed -e 's/^.*\.\([0-9]*\)/\1/'`))
    DEFAULT_MOUNT_INDEX=$((${OCTET4} % ${#AVEREVFXT_NODES[@]}))
    ROUND_ROBIN_IP=${AVEREVFXT_NODES[${DEFAULT_MOUNT_INDEX}]}

    DEFAULT_MOUNT_POINT="${BASE_DIR}/default"

    # no need to write again if it is already there
    if ! grep --quiet "${DEFAULT_MOUNT_POINT}" /etc/fstab; then
        echo "${ROUND_ROBIN_IP}:${NFS_PATH}    ${DEFAULT_MOUNT_POINT}    nfs hard,nointr,proto=tcp,mountproto=tcp,retry=30 0 0" >> /etc/fstab
        mkdir -p "${DEFAULT_MOUNT_POINT}"
        chown nfsnobody:nfsnobody "${DEFAULT_MOUNT_POINT}"
    fi
    if ! grep -qs "${DEFAULT_MOUNT_POINT} " /proc/mounts; then
        retrycmd_if_failure 12 20 mount "${DEFAULT_MOUNT_POINT}" || exit 1
    fi   
} 
```

Funktionen ovan är en del av batch-exemplet som är tillgängligt i exempel webbplatsen för [aver-vFXT](https://github.com/Azure/Avere#tutorials) .

## <a name="create-the-mount-command"></a>Skapa monterings kommandot 

> [!NOTE]
> Om du inte skapade en ny BLOB-behållare när du skapade ditt AVERT vFXT-kluster, följer du stegen i [Konfigurera lagring](avere-vfxt-add-storage.md) innan du försöker montera klienter.

Från klienten mappar kommandot ``mount`` den virtuella servern (vserver) i vFXT-klustret till en sökväg i det lokala fil systemet. Formatet är ``mount <vFXT path> <local path> {options}``

Monterings kommandot innehåller tre element: 

* vFXT-sökväg – (en kombination av sökväg för IP-adress och namespace-förgrening som beskrivs nedan)
* lokal sökväg – sökvägen till klienten 
* Monterings kommando alternativ – (anges i [kommando argument för montering](#mount-command-arguments))

### <a name="junction-and-ip"></a>Knut punkt och IP

Vserver-sökvägen är en kombination av dess *IP-adress* plus sökvägen till en *Knut punkt för namn områden*. Namn områdes knuten är en virtuell sökväg som definierades när lagrings systemet lades till.

Om klustret skapades med Blob Storage, är namn områdes Sök vägen `/msazure`

Exempel: ``mount 10.0.0.12:/msazure /mnt/vfxt``

Om du har lagt till lagring efter att klustret har skapats, motsvarar sökvägen för namn områdes Knut punkten det värde som du angav i **namn områdes Sök** vägen när du skapar Knut punkten. Om du till exempel använde ``/avere/files`` som namn områdes Sök väg skulle dina klienter montera *IP*-adress:/avere/Files till sin lokala monterings punkt.

![Dialog rutan Lägg till ny Knut punkt med/avere/Files i fältet namn områdes Sök väg](media/avere-vfxt-create-junction-example.png)


IP-adressen är en av klientens IP-adresser som har definierats för vserver. Du hittar intervallet för klientbaserade IP-adresser på två platser i kontroll panelen aver:

* **VServers** -tabell (fliken instrument panel) – 

  ![Fliken instrument panel på kontroll panelen för att kontrol lera att fliken VServer har marker ATS i data tabellen under grafen och avsnittet IP-adress är inringat](media/avere-vfxt-ip-addresses-dashboard.png)

* Sidan **klient nätverks** inställningar- 

  ![Inställningar > VServer > klient med en cirkel runt avsnittet adress intervall i tabellen för en viss vserver](media/avere-vfxt-ip-addresses-settings.png)

Förutom Sök vägarna inkluderar du [kommando argumenten för montering](#mount-command-arguments) som beskrivs nedan när du monterar varje klient.

### <a name="mount-command-arguments"></a>Montera kommando argument

För att säkerställa en sömlös klient montering måste du överföra dessa inställningar och argument i monterings kommandot: 

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``


| Nödvändiga inställningar | |
--- | --- 
``hard`` | Mjuka monteringar till vFXT-klustret är kopplade till program fel och eventuell data förlust. 
``proto=netid`` | Det här alternativet stöder lämplig hantering av NFS-nätverks fel.
``mountproto=netid`` | Det här alternativet stöder lämplig hantering av nätverks fel för monterings åtgärder.
``retry=n`` | Ange ``retry=30`` för att undvika tillfälliga monterings problem. (Ett annat värde rekommenderas i förgrunds monteringar.)

| Önskade inställningar  | |
--- | --- 
``nointr``            | Alternativet "nointr" rekommenderas för klienter med äldre kernel (före 2008) som har stöd för det här alternativet. Observera att alternativet "intr" är standardvärdet.


## <a name="next-steps"></a>Nästa steg

När du har monterat klienter kan du använda dem för att fylla backend-datalagringen (Core-filer). Läs mer om ytterligare konfigurations aktiviteter i dessa dokument:

* [Flytta data till kluster core-filer](avere-vfxt-data-ingest.md) – hur man använder flera klienter och trådar för att effektivt Ladda upp dina data
* [Anpassa kluster justering](avere-vfxt-tuning.md) – skräddarsy kluster inställningarna så att de passar din arbets belastning
* [Hantera klustret](avere-vfxt-manage-cluster.md) – så här startar eller stoppar du klustret och hanterar noder
