---
title: Azure Migrate の依存関係の視覚化 | Microsoft Docs
description: Azure Migrate サービスにおけるアセスメントの計算の概要を説明します。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: raynew
ms.openlocfilehash: 923a2a137bb4510e9490ce4077f744a43619a2c6
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2018
ms.locfileid: "47165026"
---
# <a name="dependency-visualization"></a>依存関係の視覚化

[Azure Migrate](migrate-overview.md) サービスは、Azure への移行についてオンプレミスのマシンのグループを評価します。 Azure Migrate の依存関係可視化機能を使用して、グループを作成することができます。 この記事では、次の機能に関する情報を提供します。


## <a name="overview"></a>概要

Azure Migrate の依存関係可視化機能を使用すると、移行評価用の信頼性の高いグループを作成できます。 依存関係の可視化を使用すると、マシンのネットワーク依存関係を表示したり、Azure に一緒に移行するために必要な関連するマシンを特定することができます。 この機能は、アプリケーションが導入されているマシンのノーハウをまったく持っておらず、そのアプリケーションを一緒にAzureに移行する必要がある状況で役立ちます。

## <a name="how-does-it-work"></a>それはどのように機能しますか?

Azure Migrate は、依存関係の視覚化のために [Log Analytics](../log-analytics/log-analytics-overview.md) の [Service Map](../operations-management-suite/operations-management-suite-service-map.md) ソリューションを使用します。
- 依存関係の可視化を活かすために、新規または既存の Log Analytics ワークスペースを各 Azure Migrate プロジェクトに関連付けることが必要になります。
- 移行プロジェクトが作成された同じサブスクリプション内にのみ、ワークスペースを作成またはアタッチできます。
- プロジェクトに Log Analytics ワークスペースをアタッチするには、**プロジェクト概要ページで** Essentials **セクションに遷移し**、**「構成が必要です」** をクリックします

    ![Log Analytics ワークスペースを関連付けする](./media/concepts-dependency-visualization/associate-workspace.png)

- 新しいワークスペースを作成する場合は、ワークスペースの名前を指定する必要があります。 移行プロジェクトと同様の [Azure 地理的環境](https://azure.microsoft.com/global-infrastructure/geographies/)のリージョンでワークスペースが作成されます。
- Azure ポータルで検索に使用できるキー**移行プロジェクト**と値**プロジェクト名**に関連付けられているワークスペースがタグ付けされます。
- プロジェクトに関連付けられているワークスペースに移動するためには、プロジェクト**概要**ページの **Essentials** セクションに遷移し、ワークスペースをアクセスできます。

    ![Log Analytics ワークスペースを操作する](./media/concepts-dependency-visualization/oms-workspace.png)

依存関係の視覚化を使用するには、分析するオンプレミスの各マシンにエージェントをダウンロードしてインストールする必要があります。  

## <a name="do-i-need-to-pay-for-it"></a>使用料金が必要になる場合

Azure Migrate は、追加料金なしで利用できます。 Azure Migrate で依存関係可視化機能を使用するには、サービス マップが必要であり、新規または既存の Log Analytics ワークスペースを Azure Migrate プロジェクトに関連付ける必要があります。 Azure Migrate の依存関係可視化機能は、Azure Migrate の最初の 180 日間無料です。

1. この Log Analytics ワークスペース内で Service Map 以外のソリューションを使用すると、[標準の Log Analytics ](https://azure.microsoft.com/pricing/details/log-analytics/)料金が適用されます。
2. 追加コストなしの移行シナリオをサポートするため、Service Map ソリューションでは、Azure Migrate プロジェクトを Log Analyticsワークスペースに関連付けした後の最初の 180 日間は料金が発生しません。 180 日が経過すると、Log Analytics の標準の料金が適用されます。

ワークスペースにエージェントを登録する際には、エージェントのインストール手順のページで、プロジェクトによって指定された ID とキーを使用します。

Azure Migrate プロジェクトが削除される際、それに伴ってワークスペースが削除されることはありません。 プロジェクトが削除された後は、Service Map を無料で使用することはできず、Log Analytics ワークスペースの有料階層に応じて各ノードが課金されます。

> [!NOTE]
> 依存関係の視覚化機能では、サービス マップは Log Analytics ワークスペース経由で使用されます。 Azure Migrate の一般提供が発表された 2018 年 2 月 28 日以降は、この機能を追加料金なしで使用できるようになりました。 無料のワークスペースを使用するには、新しいプロジェクトを作成する必要があります。 一般提供が開始される前の既存のワークスペースは課金対象のままとなるため、新しいプロジェクトに移動することをお勧めします。

Azure Migrate の価格については、[こちら](https://azure.microsoft.com/pricing/details/azure-migrate/)を参照してください。

## <a name="how-do-i-manage-the-workspace"></a>ワークスペースの管理方法

Azure Migrate 以外で Log Analytics ワークスペースを使用できます。 作成した移行プロジェクトを削除しても、ワークスペースは削除されません。 ワークスペースが不要になった場合は手動で[削除](../log-analytics/log-analytics-manage-access.md)します。

移行プロジェクトを削除する場合を除き、Azure Migrate で作成されたワークスペースは削除しないでください。 削除した場合は、依存関係可視化機能は、期待どおりに機能しません。

## <a name="next-steps"></a>次の手順
- [マシンの依存関係マッピングを使用したマシンのグループ化](how-to-create-group-machine-dependencies.md)
- [依存関係の可視化の詳細については](https://docs.microsoft.com/azure/migrate/resources-faq#dependency-visualization)、よく寄せられる質問を確認します。
