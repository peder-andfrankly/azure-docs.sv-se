---
title: Koncept – säkerhet i Azure Kubernetes Services (AKS)
description: Lär dig mer om säkerhet i Azure Kubernetes service (AKS), inklusive Master-och Node-kommunikation, nätverks principer och Kubernetes hemligheter.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: mlearned
ms.openlocfilehash: 1d100f17130594ace6169f5840915c88435cb9a8
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "67615779"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Säkerhets begrepp för program och kluster i Azure Kubernetes service (AKS)

För att skydda dina kund uppgifter när du kör program arbets belastningar i Azure Kubernetes service (AKS) är säkerheten för klustret en viktig faktor. Kubernetes innehåller säkerhets komponenter som *nätverks principer* och *hemligheter*. Azure lägger sedan till i komponenter som nätverks säkerhets grupper och dirigerade kluster uppgraderingar. Dessa säkerhets komponenter kombineras för att låta ditt AKS-kluster köra de senaste säkerhets uppdateringarna för operativ systemet och Kubernetes-versioner och med säker Pod-trafik och åtkomst till känsliga autentiseringsuppgifter.

Den här artikeln beskriver de viktigaste begreppen som skyddar dina program i AKS:

- [Säkerhet för huvud komponenter](#master-security)
- [Nods säkerhet](#node-security)
- [Klusteruppgradering](#cluster-upgrades)
- [Nätverkssäkerhet](#network-security)
- [Kubernetes hemligheter](#kubernetes-secrets)

## <a name="master-security"></a>Huvud säkerhet

I AKS är Kubernetes Master-komponenterna en del av den hanterade tjänst som tillhandahålls av Microsoft. Varje AKS-kluster har sin egen, dedikerad Kubernetes-huvudhanterare för att tillhandahålla API-servern, Scheduler osv. Den här huvud servern hanteras och underhålls av Microsoft.

Som standard använder Kubernetes-API-servern en offentlig IP-adress och med fullständigt kvalificerat domän namn (FQDN). Du kan kontrol lera åtkomsten till API-servern med hjälp av Kubernetes-rollbaserade åtkomst kontroller och Azure Active Directory. Mer information finns i [Azure AD-integrering med AKS][aks-aad].

## <a name="node-security"></a>Nods säkerhet

AKS-noder är virtuella Azure-datorer som du hanterar och underhåller. Linux-noder kör en optimerad Ubuntu-distribution med Moby container Runtime. Windows Server-noder (för närvarande i för hands version i AKS) kör en optimerad Windows Server 2019-version och använder även Moby container Runtime. När ett AKS-kluster skapas eller skalas, distribueras noderna automatiskt med de senaste säkerhets uppdateringarna och konfigurationerna för operativ systemet.

Azure-plattformen tillämpar automatiskt OS-säkerhetskorrigeringsfiler på Linux-noder på en natt basis. Om en säkerhets uppdatering för Linux-operativsystem kräver en värd omstart utförs inte den här omstarten automatiskt. Du kan starta om Linux-noderna manuellt eller en vanlig metod är att använda [Kured][kured], en daemon för omstart med öppen källkod för Kubernetes. Kured körs som en [DaemonSet][aks-daemonsets] och övervakar varje nod för att visa en fil som anger att en omstart krävs. Omstarter hanteras i klustret med samma [Cordon och tömnings processen](#cordon-and-drain) som en kluster uppgradering.

För Windows Server-noder (för närvarande i för hands version i AKS) körs inte Windows Update automatiskt och tillämpar de senaste uppdateringarna. I ett regelbundet schema kring Windows Updates lanserings cykel och din egen verifierings process bör du utföra en uppgradering på Windows Server-pool (er) i ditt AKS-kluster. Den här uppgraderings processen skapar noder som kör den senaste Windows Server-avbildningen och uppdateringar och tar sedan bort de äldre noderna. Mer information om den här processen finns [i uppgradera en Node-pool i AKS][nodepool-upgrade].

Noder distribueras till ett privat virtuellt nätverk under nät, utan att några offentliga IP-adresser tilldelats. För fel sökning och hanterings syfte är SSH aktiverat som standard. Den här SSH-åtkomsten är endast tillgänglig med den interna IP-adressen.

Noderna använder Azure Managed Disks för att tillhandahålla lagring. För de flesta VM-storlekar är dessa Premium diskar som backas upp av SSD med höga prestanda. Data som lagras på hanterade diskar krypteras automatiskt i vila på Azure-plattformen. För att förbättra redundans replikeras även dessa diskar på ett säkert sätt i Azure-datacentret.

Kubernetes-miljöer, i AKS eller någon annan stans, är för närvarande inte helt säkra för att skydda användningen av flera klienter. Ytterligare säkerhetsfunktioner, till exempel *Pod säkerhets principer* eller mer detaljerade rollbaserade åtkomst kontroller (RBAC) för noder gör det svårare att utnyttja dem. Men för verklig säkerhet när du kör en skydds arbets belastning med flera innehavare, är en hypervisor den enda säkerhets nivå som du bör lita på. Säkerhets domänen för Kubernetes blir hela klustret, inte en enskild nod. För dessa typer av farliga arbets belastningar med flera klienter bör du använda fysiskt isolerade kluster. Mer information om hur du isolerar arbets belastningar finns i [metod tips för kluster isolering i AKS][cluster-isolation].

## <a name="cluster-upgrades"></a>Kluster uppgraderingar

För säkerhet och efterlevnad, eller för att använda de senaste funktionerna, tillhandahåller Azure verktyg för att dirigera uppgraderingen av ett AKS-kluster och-komponenter. Den här uppgraderings dirigeringen omfattar både Kubernetes huvud-och agent komponenter. Du kan visa en [lista över tillgängliga Kubernetes-versioner](supported-kubernetes-versions.md) för ditt AKS-kluster. Du startar uppgraderings processen genom att ange någon av dessa tillgängliga versioner. Azure gör sedan en säker cordons och tömmer varje AKS-nod och genomför uppgraderingen.

### <a name="cordon-and-drain"></a>Cordon och dränering

Under uppgraderings processen är AKS-noder individuellt avspärrade från klustret så att nya poddar inte är schemalagda för dem. Noderna töms sedan och uppgraderas enligt följande:

- En ny nod distribueras till Node-poolen. Den här noden kör den senaste OS-avbildningen och korrigeringarna.
- En av de befintliga noderna identifieras för uppgradering. Poddar på den här noden avslutas och schemaläggs på andra noder i Node-poolen.
- Den här befintliga noden tas bort från AKS-klustret.
- Nästa nod i klustret är avspärrade och töms med samma process tills alla noder har ersatts som en del av uppgraderings processen.

Mer information finns i [uppgradera ett AKS-kluster][aks-upgrade-cluster].

## <a name="network-security"></a>Nätverkssäkerhet

För anslutning och säkerhet med lokala nätverk kan du distribuera ditt AKS-kluster till befintliga Azure Virtual Network-undernät. Dessa virtuella nätverk kan ha en Azure plats-till-plats VPN-anslutning eller Express Route-anslutning tillbaka till ditt lokala nätverk. Kubernetes ingångs styrenheter kan definieras med privata, interna IP-adresser så att tjänsterna endast är tillgängliga via den här interna nätverks anslutningen.

### <a name="azure-network-security-groups"></a>Nätverks säkerhets grupper i Azure

Azure använder regler för nätverks säkerhets grupper för att filtrera trafik flödet i virtuella nätverk. Dessa regler definierar käll-och mål-IP-intervall, portar och protokoll som tillåts eller nekas åtkomst till resurser. Standard regler skapas för att tillåta TLS-trafik till Kubernetes-API-servern. När du skapar tjänster med belastningsutjämnare, Port mappningar eller ingångs vägar ändrar AKS automatiskt nätverks säkerhets gruppen för trafik så att den flödar korrekt.

## <a name="kubernetes-secrets"></a>Kubernetes-hemligheter

En Kubernetes- *hemlighet* används för att mata in känsliga data i poddar, t. ex. autentiseringsuppgifter eller nycklar för åtkomst. Du skapar först en hemlighet med Kubernetes-API: et. När du definierar din POD eller distribution kan du begära en speciell hemlighet. Hemligheter anges bara för noder som har en schemalagd Pod som kräver det, och hemligheten lagras i *tmpfs*, som inte skrivs till disk. När den sista Pod på en nod som kräver en hemlighet tas bort, tas hemligheten bort från nodens tmpfs. Hemligheter lagras inom ett angivet namn område och kan endast nås av poddar inom samma namnrymd.

Användningen av hemligheter minskar känslig information som definieras i manifestet POD eller service YAML. I stället begär du hemligheten som lagras i Kubernetes API-servern som en del av ditt YAML-manifest. Den här metoden ger endast den speciella Pod åtkomst till hemligheten. Obs! manifest filen för RAW Secret innehåller hemliga data i base64-format (se den [officiella dokumentationen][secret-risks] för mer information). Därför bör den här filen behandlas som känslig information och aldrig allokeras till käll kontroll.

## <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång med att skydda dina AKS-kluster finns i [uppgradera ett AKS-kluster][aks-upgrade-cluster].

För associerade metod tips, se [metod tips för kluster säkerhet och uppgraderingar i AKS][operator-best-practices-cluster-security].

Mer information om kärn Kubernetes-och AKS-koncept finns i följande artiklar:

- [Kubernetes/AKS-kluster och arbets belastningar][aks-concepts-clusters-workloads]
- [Kubernetes/AKS-identitet][aks-concepts-identity]
- [Kubernetes/AKS virtuella nätverk][aks-concepts-network]
- [Kubernetes/AKS-lagring][aks-concepts-storage]
- [Kubernetes/AKS-skala][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[secret-risks]: https://kubernetes.io/docs/concepts/configuration/secret/#risks

<!-- LINKS - Internal -->
[aks-daemonsets]: concepts-clusters-workloads.md#daemonsets
[aks-upgrade-cluster]: upgrade-cluster.md
[aks-aad]: azure-ad-integration.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[cluster-isolation]: operator-best-practices-cluster-isolation.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
