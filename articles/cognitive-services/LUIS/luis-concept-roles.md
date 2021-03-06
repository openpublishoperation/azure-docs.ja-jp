---
title: パターン ベースのエンティティでのロールの使用方法について
titleSuffix: Azure Cognitive Services
description: ロールは、パターンでのみ使用されるエンティティの名前付きコンテキスト サブタイプです。 たとえば、発話「ニューヨーク発ロンドン着の切符を買う」では、ロンドンとニューヨークは両方とも都市ですが、文章内での意味はそれぞれ異なります。 ニューヨークは出発地、ロンドンは目的地です。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 9bbbb797cd7e7d1cea52f1d5b1b491998b595db7
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638086"
---
# <a name="entity-roles-in-patterns-are-contextual-subtypes"></a>パターンのエンティティ ロールはコンテキスト サブタイプです
ロールは、[パターン](luis-concept-patterns.md)でのみ使用されるエンティティの名前付きコンテキスト サブタイプです。

たとえば、発話 `buy a ticket from New York to London` では、ロンドンとニューヨークは両方とも都市ですが、文章内での意味はそれぞれ異なります。 ニューヨークは出発地、ロンドンは目的地です。 

ロールでは、こうした違いに対して名前が付けられます。

|エンティティ|Role|目的|
|--|--|--|
|Location|origin|飛行機が離陸する場所|
|Location|destination|飛行機が着陸する場所|
|事前構築済みの datetimeV2|to|終了日|
|事前構築済みの datetimeV2|from|開始日|

## <a name="how-are-roles-used-in-patterns"></a>パターンでのロールの使用方法
パターンのテンプレートの発話では、ロールが発話内で使用されます。 

|エンティティ ロールのあるパターン|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>パターンでのロールの構文
エンティティとロールは、かっこ `{}` で囲まれています。 エンティティとロールは、コロンで区切られています。 

## <a name="roles-versus-hierarchical-entities"></a>ロールと階層エンティティ
階層エンティティはロールと同じコンテキスト情報を提供しますが、提供先は**意図**の発話だけになります。 同様に、ロールも階層エンティティと同じコンテキスト情報を提供します。ただし、その情報は、**パターン**でのみ提供されます。

|コンテキストでの学習|使用される場所|
|--|--|
|階層エンティティ|意図|
|roles|パターン|

## <a name="roles-with-prebuilt-entities"></a>ロールと事前構築済みエンティティ

ロールを事前構築済みエンティティと共に使用して、発話内の事前構築済みエンティティのさまざまなインスタンスに意味を与えます。 

### <a name="roles-with-datetimev2"></a>ロールと datetimeV2

事前構築済みエンティティ datetimeV2 は、発話内のさまざまな日付と時刻の解釈に能力を発揮します。 事前構築済みエンティティの既定の解釈とは異なる日付と日付範囲を指定できます。 

## <a name="next-steps"></a>次の手順

* [ロール](luis-how-to-add-entities.md#add-role-to-pattern-based-entity)を追加する方法を確認します
