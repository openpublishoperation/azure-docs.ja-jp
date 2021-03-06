---
title: SAP HANA on Azure (L インスタンス) のインフラストラクチャと接続 | Microsoft Docs
description: SAP HANA on Azure (L インスタンス) を使用するために必要な接続インフラストラクチャを構成します。
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c61dffc6fd9d0c65f5e925daebdf2a02cfb5d6ba
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2018
ms.locfileid: "44161358"
---
# <a name="sap-hana-large-instances-deployment"></a>SAP HANA (L インスタンス) のデプロイ 

お客様と Microsoft のエンタープライズ アカウント チームとの間で SAP HANA on Azure (L インスタンス) の購入手続きが完了した後、Microsoft が HANA L インスタンス ユニットをデプロイするためには、次の情報が必要となります。

- お客様名
- 会社の連絡先情報 (電子メール アドレスや電話番号など)
- 技術担当者の連絡先情報 (電子メール アドレスや電話番号など)
- 技術担当者のネットワーキング用の連絡先情報 (電子メール アドレスや電話番号など)
- Azure デプロイ リージョン (2017 年 7 月時点では、米国西部、米国東部、オーストラリア東部、オーストラリア南東部、西ヨーロッパ、北ヨーロッパ)
- SAP HANA on Azure (L インスタンス) の SKU (構成) の確認
- デプロイ先のすべての Azure リージョンについて次の情報が必要となります (HANA L インスタンスの概要とアーキテクチャについてのドキュメントを参照)。
    - Azure VNet を HANA L インスタンスに接続する ER-P2P 接続の /29 IP アドレス範囲
    - HANA L インスタンスのサーバー IP プールに使用する /24 CIDR ブロック
- HANA L インスタンスに接続するすべての Azure VNet の "VNet のアドレス空間" 属性に使用される IP アドレス範囲の値
- 各 HANA L インスタンス システムのデータ:
  - 適切なホスト名 (できれば完全修飾ドメイン名を含める)。
  - HANA L インスタンス ユニットの適切な IP アドレス (サーバー IP プールのアドレス範囲から)。サーバー IP プール アドレス範囲に含まれる最初の 30 個の IP アドレスは、HANA L インスタンスの内部的な用途で予約されています。
  - SAP HANA インスタンス用の SAP HANA SID 名 (SAP HANA に関連する必要なディスク ボリュームの作成に必要)。 HANA SID は、NFS ボリューム上の sidadm のアクセス許可を作成するために必要です。これらのアクセス許可は、HANA L インスタンス ユニットに関連付けられているところです。 また、マウントされるディスク ボリュームの名前の構成要素の 1 つとしても使用されます。 ユニット上で複数の HANA インスタンスを実行する場合は、複数の HANA SID の一覧を指定する必要があります。 それぞれに個別のボリュームのセットが割り当てられます。
  - Linux OS における sidadm ユーザーのグループ ID (SAP HANA に関連する必要なディスク ボリュームの作成に必要)。 SAP HANA のインストールでは通常、グループ ID 1001 の sapsys グループが作成されます。 sidadm ユーザーは、そのグループに属しています。
  - Linux OS における sidadm ユーザーのユーザー ID (SAP HANA に関連する必要なディスク ボリュームの作成に必要)。 ユニット上で複数の HANA インスタンスを実行している場合は、すべての <sid>adm ユーザーの一覧を指定する必要があります。 
- SAP HANA on Azure (L インスタンス) が直接接続する Azure サブスクリプションの Azure サブスクリプション ID。 このサブスクリプション ID は、Azure サブスクリプションを参照します。そのサブスクリプションに HANA L インスタンス ユニットが課金されます。

これらの情報を入力すると、Microsoft によって SAP HANA on Azure (L インスタンス) がプロビジョニングされ、Azure VNet と HANA L インスタンスとのリンクや HANA L インスタンス ユニットへのアクセスに必要な情報が返されます。

この記事を読む前に、[HANA L インスタンスの一般的な用語](hana-know-terms.md)と [HANA L インスタンスの SKU](hana-available-skus.md) について理解しておいてください。

Microsoft によって HANA L インスタンスがデプロイされた後、次の手順を使用して HANA L インスタンスに接続できます。

1. [HANA L インスタンスへの Azure VM の接続](hana-connect-azure-vm-large-instances.md)
2. [HANA L インスタンスの ExpressRoute への VNet の接続](hana-connect-vnet-express-route.md)
3. [追加のネットワーク要件 (オプション)](hana-additional-network-requirements.md)

**次のステップ**

- 「[HANA L インスタンスへの Azure VM の接続](hana-connect-azure-vm-large-instances.md)」を参照してください。
- 「[HANA L インスタンスの ExpressRoute への VNet の接続](hana-connect-vnet-express-route.md)」を参照してください。