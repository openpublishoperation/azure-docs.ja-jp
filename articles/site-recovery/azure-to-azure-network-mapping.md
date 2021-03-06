---
title: Azure Site Recovery の 2 つの Azure リージョン間で仮想ネットワークをマッピングする | Microsoft Docs
description: Azure Site Recovery は、仮想マシンと物理サーバーのレプリケーション、フェールオーバー、回復を調整します。 Azure またはセカンダリ データセンターへのフェールオーバーについて説明します。
services: site-recovery
documentationcenter: ''
author: mayurigupta13
manager: rochakm
editor: ''
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/16/2018
ms.author: mayg
ms.openlocfilehash: 95e6a388d0638d2fd477d33aaf7c39cf120e29aa
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2018
ms.locfileid: "49353435"
---
# <a name="map-virtual-networks-in-different-azure-regions"></a>異なる Azure リージョン間で仮想ネットワークをマッピングする


この記事では、異なる Azure リージョンに配置されている Azure 仮想ネットワークのインスタンスを相互にマッピングする方法について説明します。 ネットワーク マッピングによって、レプリケートされた仮想マシンがターゲットの Azure リージョンに作成されると、その仮想マシンは、ソース仮想マシンの仮想ネットワークにマッピングされている仮想ネットワークにも作成されます。  

## <a name="prerequisites"></a>前提条件
ネットワークをマッピングする前に、ソース リージョンとターゲットの Azure リージョンの両方で [Azure 仮想ネットワーク](../virtual-network/virtual-networks-overview.md)が作成されていることを確認します。

## <a name="map-virtual-networks"></a>仮想ネットワークのマッピング

Azure リージョンに配置されている Azure 仮想ネットワーク (ソース ネットワーク) を、別のリージョンに配置されている仮想ネットワーク (ターゲット ネットワーク) にマッピングするには、Azure Virtual Machines の場合、**[Site Recovery インフラストラクチャ]**  >  **[ネットワーク マッピング]** に移動します。 ネットワーク マッピングを作成します。

![[ネットワーク マッピング] ウィンドウ - ネットワーク マッピングの作成](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)


次の例では、仮想マシンは東アジア リージョンで実行されています。 仮想マシンは、東南アジア リージョンにレプリケートされています。

東アジア リージョンから東南アジア リージョンへのネットワーク マッピングを作成するには、ソース ネットワークの場所とターゲット ネットワークの場所を選択します。 **[OK]** をクリックします。

![[ネットワーク マッピングの追加] ウィンドウ - ソース ネットワークのソースとターゲットの場所を選択する](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)


前の手順を繰り返して、東南アジア リージョンから東アジア リージョンへのネットワーク マッピングを作成します。

![[ネットワーク マッピングの追加] ウィンドウ - ターゲット ネットワークのソースとターゲットの場所を選択する](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-a-network-when-you-enable-replication"></a>レプリケーションを有効にするときにネットワークをマッピングする

初めて Azure リージョンから別のリージョンに仮想マシンをレプリケートするときに、ネットワーク マッピングが存在しない場合は、レプリケーションの設定時にターゲット ネットワークを設定できます。 この設定に基づいて、Azure Site Recovery では、ソース リージョンからターゲット リージョンへのネットワーク マッピングと、ターゲット リージョンからソース リージョンへのネットワーク マッピングが作成されます。   

![[設定の構成] ウィンドウ - ターゲットの場所を選択する](./media/site-recovery-network-mapping-azure-to-azure/network-mapping4.png)

既定では、Site Recovery では、ソース ネットワークと同じターゲット リージョンにネットワークが作成されます。 Site Recovery では、ソース ネットワークの名前にサフィックスとして **-asr** を追加して、ネットワークが作成されます。 作成されているネットワークを選ぶには、**[カスタマイズ]** を選択します。

![[ターゲットの設定のカスタマイズ] ウィンドウ - ターゲット リソース グループの名前とターゲット仮想ネットワークの名前を設定する](./media/site-recovery-network-mapping-azure-to-azure/network-mapping5.png)

ネットワーク マッピングが既に行われている場合、レプリケーションを有効にするときに、ターゲット仮想ネットワークを変更することはできません。 この場合は、ターゲット仮想ネットワークを変更するには、既存のネットワーク マッピングを変更します。  

![[ターゲットの設定のカスタマイズ] ウィンドウ - ターゲット リソース グループの名前を設定する](./media/site-recovery-network-mapping-azure-to-azure/network-mapping6.png)

![[ネットワーク マッピングの変更] ウィンドウ - 既存のターゲット仮想ネットワークを変更する](./media/site-recovery-network-mapping-azure-to-azure/modify-network-mapping.png)

> [!IMPORTANT]
> リージョン A からリージョン B へのネットワーク マッピングを変更する場合は、リージョン B からリージョン A へのネットワーク マッピングも必ず変更してください。
>
>


## <a name="subnet-selection"></a>サブネットの選択
ターゲット仮想マシンのサブネットは、ソース仮想マシンのサブネットの名前に基づいて選択されます。 ソース仮想マシンと同じ名前のサブネットをターゲット ネットワークで利用できる場合は、そのサブネットがターゲット仮想マシンに対して選択されます。 ターゲット ネットワークに同じ名前のサブネットが存在しない場合は、アルファベット順で先にくるサブネットがターゲット サブネットとして設定されます。

サブネットを変更するには、仮想マシンの **[コンピューティングとネットワーク]** 設定に移動します。

![[コンピューティングとネットワーク] の [コンピューティングのプロパティ] ウィンドウ](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="ip-address"></a>IP アドレス

ターゲット仮想マシンの各ネットワーク インターフェイスの IP アドレスは、次のセクションで説明するように設定されます。

### <a name="dhcp"></a>DHCP
ソース仮想マシンのネットワーク インターフェイスが DHCP を使用する場合は、ターゲット仮想マシンのネットワーク インターフェイスも DHCP を使用するように設定されます。

### <a name="static-ip-address"></a>静的 IP アドレス
ソース仮想マシンのネットワーク インターフェイスが静的 IP アドレスを使用する場合は、ターゲット仮想マシンのネットワーク インターフェイスも静的 IP アドレスを使用するように設定されます。 次のセクションでは、静的 IP アドレスがどのように設定されるかについて説明します。

### <a name="ip-assignment-behavior-during-failover"></a>フェールオーバー時の IP 割り当て動作
#### <a name="1-same-address-space"></a>1.同じアドレス空間

ソース サブネットとターゲット サブネットのアドレス空間が同じ場合、ソース仮想マシンのネットワーク インターフェイスの IP アドレスがターゲット IP アドレスとして設定されます。 同じ IP アドレスが使用できない場合は、次に使用可能な IP アドレスがターゲット IP アドレスとして設定されます。

#### <a name="2-different-address-spaces"></a>2.異なるアドレス空間

ソース サブネットとターゲット サブネットのアドレス空間が異なる場合、ターゲット サブネット内の次に使用可能な IP アドレスがターゲット IP アドレスとして設定されます。


### <a name="ip-assignment-behavior-during-test-failover"></a>テスト フェールオーバー時の IP 割り当て動作
#### <a name="1-if-the-target-network-chosen-is-the-production-vnet"></a>1.選択されたターゲット ネットワークが運用 vNet である場合
- 回復用の IP (ターゲット IP) は静的 IP になりますが、フェールオーバー用に予約されているのと**同じ IP アドレスにはなりません**。
- 割り当てられる IP アドレスとしては、サブネット アドレス範囲の末尾から順に IP が順に使用されます。
- たとえば、ソース VM の静的 IP が 10.0.0.19 に構成されているとき、構成済みの運用ネットワーク ***dr-PROD-nw*** とサブネット範囲 10.0.0.0/24 に対してテスト フェールオーバーが試行されたとします。 </br>
サブネット アドレス範囲の末尾から順に IP が使用されるため、フェールオーバーされた VM には 10.0.0.254 が割り当てられます。 </br>

**注意:** **運用 vNet** という用語は、ディザスター リカバリー構成でマップされるターゲット ネットワークを意味します。
#### <a name="2-if-the-target-network-chosen-is-not-the-production-vnet-but-has-the-same-subnet-range-as-production-network"></a>2.選択されたターゲット ネットワークが運用 vNet ではないが、運用ネットワークとサブネット範囲が同じ場合

- 回復用の IP (ターゲット IP) は、フェールオーバー用に予約されているのと**同じ IP アドレス** (静的 IP として構成されている) の静的 IP になります。 これは、同じ IP アドレスが使用可能な場合です。
- 構成済みの静的 IP が既に他の VM/デバイスに割り当てられている場合は、回復用 IP としてサブネット アドレス範囲の末尾から順に IP が順に使用されます。
- たとえば、ソース VM の静的 IP が 10.0.0.19 に構成されているとき、テスト ネットワーク ***dr-NON-PROD-nw*** と、サブネット範囲 10.0.0.0/24 (運用ネットワークと同じ) に対してテスト フェールオーバーが試行されたとします。 </br>
  フェールオーバーされる VM には、次の静的 IP が割り当てられます。 </br>
    - 構成済み静的 IP: 10.0.0.19 (IP が使用可能な場合)
    - 次に使用可能な IP: 10.0.0.254 (IP アドレス 10.0.0.19 が既に使用済みの場合)


各ネットワーク インターフェイスのターゲット IP を変更するには、仮想マシンの **[コンピューティングとネットワーク]** 設定に移動します。</br>
ベスト プラクティスとして、テスト フェールオーバーを実行するには必ずテスト ネットワークを選択することをお勧めします。
## <a name="next-steps"></a>次の手順

* [Azure 仮想マシンのレプリケートに関するネットワーク ガイダンス](site-recovery-azure-to-azure-networking-guidance.md)を確認します。
