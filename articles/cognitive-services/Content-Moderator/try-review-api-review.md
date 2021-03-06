---
title: API コンソールでヒューマン レビューを使用してコンテンツをモデレートする - Content Moderator
titlesuffix: Azure Cognitive Services
description: Content Moderator API コンソールでヒューマン レビューを作成する方法を説明します。
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: bb95341a09f09ce8020f34476e720270fd401909
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2018
ms.locfileid: "47219755"
---
# <a name="create-reviews-from-the-api-console"></a>API コンソールでレビューを作成する

Review API の[レビュー操作](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)を使用して、ヒューマン モデレートのために画像レビューまたはテキスト レビューを作成します。 ヒューマン モデレーターは、レビュー ツールを使用してコンテンツを確認します。 モデレート後のビジネス ロジックに応じてこの操作を使用してください。 これを使用するのは、Content Moderator の画像やテキストの API、または他の Cognitive Services API のいずれかを使用してコンテンツをスキャンした後です。 

ヒューマン モデレーターが自動割り当てタグと予測データを確認して、最終的なモデレーション意思決定を送信した後で、Review API がすべての情報を API エンドポイントに送信します。

## <a name="use-the-api-console"></a>API コンソールを使用する
オンライン コンソールを使用して API を試験的に運用するには、次のいくつかの値をコンソールに入力する必要があります。

- **teamName**: レビュー ツール アカウントの設定時に作成したチーム名。 
- **ContentId**: この文字列は API に渡され、コールバックで返されます。 ContentId は、内部識別子またはメタデータをモデレーション ジョブの結果に関連付けるために役立ちます。
- **Metadata**: コールバック時に API エンドポイントに返される、カスタムのキーと値のペア。 キーがレビュー ツールで定義された短いコードの場合は、タグとして表示されます。
- **Ocp-Apim-Subscription-Key**: **[設定]** タブにあります。詳細については、[概要](overview.md)に関するページをご覧ください。

テスト用コンソールにアクセスする最も簡単な方法は、**[資格情報]** ウィンドウを使用することです。

1.  **[資格情報]** ウィンドウで [[Review API reference]\(Review API リファレンス\)](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4) を選択します。

  **[Review - Create]\(Review - Create\)** ページが開きます。

2.  **[Open API testing console]\(API テスト コンソールを開く\)** で、実際の場所に最もあてはまるリージョンを選択します。

  ![[Review - Create]\(Review - Create\) ページのリージョン選択肢](images/test-drive-region.png)

  **[Review - Create]\(Review - Create\)** API コンソールが開きます。
  
3.  必須のクエリ パラメーター、コンテンツ タイプ、サブスクリプション キーの値を入力します。 **[要求本文]** ボックスで、コンテンツ (画像の場所など)、メタデータ、およびコンテンツに関連する他の情報を指定します。

  ![[Review - Create]\(Review - Create\) コンソールの [クエリ パラメーター]、[ヘッダー]、および [要求本文] ボックス](images/test-drive-review-1.PNG)
  
4.  **[送信]** を選択します。 レビュー ID が作成されます。 次の手順で使用するためにこの ID をコピーします。

  ![[Review - Create]\(Review - Create\) コンソールの [応答のコンテンツ] ボックスに表示されるレビュー ID](images/test-drive-review-2.PNG)
  
5.  **[取得]** を選択し、リージョンと一致するボタンを選択して API を開きます。 表示されるページで、**teamName**、**ReviewID**、および **subscription key** の値を入力します。 このページの **[送信]** ボタンを選択します。 

  ![[Review - Create]\(Review - Create\) コンソールの取得結果](images/test-drive-review-3.PNG)
  
6.  スキャンの結果を確認します。

  ![[Review - Create]\(Review - Create\) コンソールの [応答のコンテンツ] ボックス](images/test-drive-review-4.PNG)
  
7.  Content Moderator ダッシュボードで、**[確認]** > **[イメージ]** を選択します。 スキャンした画像がヒューマン レビューのために表示されます。

  ![レビュー ツール画像、サッカー ボール](images/test-drive-review-5.PNG)

## <a name="next-steps"></a>次の手順

コードで REST API を使用するか、[レビューに関する .NET のクイック スタート](moderation-reviews-quickstart-dotnet.md) から開始して、アプリケーションと統合します。
