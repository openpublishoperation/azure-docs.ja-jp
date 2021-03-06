---
title: Azure Database for PostgreSQL のサーバー ログ
description: この記事では、Azure Database for PostgreSQL がクエリ ログとエラー ログを生成する方法、およびログのリテンション期間を構成する方法について説明します。
services: postgresql
author: rachel-msft
ms.author: raagyema
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 2a6744bdec48e59b820605bb4d1cc01d32702bcf
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2018
ms.locfileid: "48867767"
---
# <a name="server-logs-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL のサーバー ログ 
Azure Database for PostgreSQL ではクエリ ログとエラー ログが生成されます。 クエリとエラー ログを使用して、構成エラーと十分に最適化されていないパフォーマンスの特定、トラブルシューティング、修復を行えます。 トランザクション ログへのアクセスは含まれていません。 

## <a name="configure-logging"></a>ログの構成 
サーバー パラメーターのログを使用して、サーバーでログを構成できます。 新しい各サーバーで、**log_checkpoints** と **log_connections** は既定でオンになります。 ログのニーズに合わせて調整できる追加のパラメーターがあります。 

![Azure Database for PostgreSQL - ログ パラメーター](./media/concepts-server-logs/log-parameters.png)

これらのパラメーターの詳細については、PostgreSQL の「[Error Reporting and Logging](https://www.postgresql.org/docs/current/static/runtime-config-logging.html)」(エラー レポートとログ記録) のドキュメントを参照してください。 Azure Database for PostgreSQL パラメーターを構成する方法については、[portal のドキュメント](howto-configure-server-parameters-using-portal.md)または　[CLI のドキュメント](howto-configure-server-parameters-using-cli.md)を参照してください。

## <a name="access-server-logs-through-portal-or-cli"></a>portal または CLI によるサーバー ログへのアクセス
ログを有効にしている場合、[Azure portal](howto-configure-server-logs-in-portal.md)、[Azure CLI](howto-configure-server-logs-using-cli.md)、および Azure REST API を使用して、Azure Database for PostgreSQL ログ ストレージからそれらにアクセスできます。 ログ ファイルのローテーションは、1 時間ごとか 100 MB ごとのどちらか早い方のタイミングで行われます。 このログ ストレージのリテンション期間を設定するには、サーバーに関連付けられている **log\_retention\_period** パラメーターを使用します。 既定値は 3 日間です。最大値は 7 日間です。 サーバーには、ログ ファイルを保持するために、十分なストレージが割り当てられている必要があります (このリテンション期間パラメーターは、Azure 診断ログに影響しません)。


## <a name="diagnostic-logs"></a>診断ログ
Azure Database for PostgreSQL は、Azure Monitor の診断ログと統合されます。 PostgreSQL サーバーでログを有効にしたら、[Log Analytics](../log-analytics/log-analytics-queries.md)、Event Hubs、または Azure Storage に対して、それらが出力されるように選択できます。 診断ログを有効にする方法の詳細については、[診断ログのドキュメント](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)の操作方法のセクションを参照してください。 


次の表は、各ログの内容を説明しています。 選択した出力エンドポイントに応じて、含まれるフィールドとそれらが表示される順序が異なることがあります。 

|**フィールド** | **説明** |
|---|---|
| TenantId | テナント ID |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | ログが記録されたときのタイムスタンプ (UTC) |
| type | ログの種類。 常に `AzureDiagnostics` |
| SubscriptionId | サーバーが属するサブスクリプションの GUID |
| ResourceGroup | サーバーが属するリソース グループの名前 |
| ResourceProvider | リソース プロバイダーの名前。 常に `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| resourceId | リソース URI |
| リソース | サーバーの名前 |
| Category | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| errorLevel | ログ レベル (LOG、ERROR、NOTICE など) |
| Message | プライマリ ログ メッセージ | 
| ドメイン | サーバーのバージョン (postgres 10 など) |
| 詳細 | セカンダリ ログ メッセージ (該当する場合) |
| ColumnName | 列の名前 (該当する場合) |
| SchemaName | スキーマの名前 (該当する場合) |
| DatatypeName | データ型の名前 (該当する場合) |
| LogicalServerName | サーバーの名前 | 
| _ResourceId | リソース URI |

## <a name="next-steps"></a>次の手順
- [Azure portal](howto-configure-server-logs-in-portal.md) または [Azure CLI](howto-configure-server-logs-using-cli.md) からのログへのアクセスを確認する。
- [Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)を確認する。
