---
title: Azure Blueprint でのリソース ロックについて
description: ブループリントを割り当てるときにリソースを保護するロック オプションについて説明します。
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 718c23b806da5058c042b961077e51ba0d4b6245
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973254"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Azure Blueprint でのリソース ロックについて

一貫性のある環境を大規模に作成することが本当に価値があるのは、一貫性が保持されることを確保するためのメカニズムがある場合だけです。 この記事では、Azure Blueprint でのリソース ロックのしくみについて説明します。

## <a name="locking-modes-and-states"></a>ロック モードと状態

ブループリント割り当てに適用されるロック モードには、**なし**と**すべてのリソース**の 2 つのオプションしかありません。 これはブループリント割り当て時に構成され、割り当てがサブスクリプションに正常に適用された後は変更できません。

サブスクリプションに割り当てられたブループリント内でアーティファクト定義によって作成されたリソースは、**ロックなし**、**読み取り専用**、または**編集/削除不可**の 3 つのいずれかの状態になります。 どの種類のアーティファクトも**ロックなし**の状態になることがあります。 しかし、**読み取り専用**状態になるのはリソース グループ以外のアーティファクトで、**編集/削除不可**状態となるのはリソース グループです。 これは、これらのリソースの管理方法において重要な違いです。

**読み取り専用**状態は、定義されているとおりで、いかなる方法でもリソースを変更することはできません。変更することも削除することもできません。 **編集/削除不可**は、リソース グループの "コンテナー" の性質により、少し異なります。 リソース グループ オブジェクトは読み取り専用ですが、リソース グループ内のリソースを作成、更新、削除することはできます。ただし、リソースが**読み取り専用**のロック状態のブループリント割り当ての一部である場合は除きます。

## <a name="overriding-locking-states"></a>ロック状態をオーバーライドする

"所有者" ロールのように、サブスクリプションで適切な[ロールベースのアクセス制御](../../../role-based-access-control/overview.md) (RBAC) を持つ人であれば、通常は任意のリソースを変更したり削除したりすることができますが、デプロイされた割り当ての一環として Blueprint によってロックが適用される場合には、これは当てはまりません。 **ロック** オプションを使用して割り当てが設定された場合、サブスクリプション所有者であっても含まれているリソースを変更することはできません。

これにより、定義済みのブループリントの一貫性と作成目的で設計された環境が、偶発的またはプログラムによる削除や変更から保護されます。

## <a name="removing-locking-states"></a>ロック状態を削除する

割り当てによって作成したリソースを削除する必要が生じた場合に、リソースを削除する唯一の方法は、まず割り当てを削除することです。 割り当てを削除すると、Blueprint によって作成されたロックが削除されます。 ただし、リソースは残るため、適切なアクセス許可を持つ人が通常の方法で削除する必要があります。

## <a name="how-blueprint-locks-work"></a>ブループリントのロックのしくみ

割り当てで**ロック** オプションを選択した場合、ブループリントの割り当て時に RBAC ロール `denyAssignments` がアーティファクト リソースに適用されます。 このロールは、ブループリント割り当てのマネージド ID によって追加され、アーティファクト リソースから削除するには、同じマネージド ID を使用する必要があります。 これにより、ロック メカニズムを強制して、Blueprint 外からブループリントのロックを解除できないようにします。 ロールおよびロックを削除するには、ブループリント割り当てを削除する必要があり、これは適切な権限を持つ人だけが実行できます。

## <a name="next-steps"></a>次の手順

- [ブループリントのライフサイクル](lifecycle.md)を参照する
- [静的および動的パラメーター](parameters.md)の使用方法を理解する
- [ブループリントの優先順位](sequencing-order.md)のカスタマイズを参照する
- [既存の割り当ての更新](../how-to/update-existing-assignments.md)方法を参照する
- ブルー プリントの割り当て時の問題を[一般的なトラブルシューティング](../troubleshoot/general.md)で解決する