---
title: .NET Core と VS Code を使用する Azure Dev Spaces でのチーム開発 | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 07/09/2018
ms.topic: tutorial
description: Azure のコンテナーとマイクロサービスを使用した迅速な Kubernetes 開発
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー
manager: douge
ms.openlocfilehash: 1448acf9a9e45b23b714a3a314526c5e6bb7752b
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2018
ms.locfileid: "47404587"
---
# <a name="team-development-with-azure-dev-spaces"></a>Azure Dev Spaces を使用したチーム開発

このチュートリアルでは、複数の開発スペースを使用して異なる開発環境で同時に作業し、個別の作業を同じクラスター内の独立した開発スペースに分離させておく方法を説明します。

## <a name="call-a-service-running-in-a-separate-container"></a>別のコンテナーで実行されているサービスを呼び出す

このセクションでは、2 つ目のサービス (`mywebapi`) を作成し、`webfrontend` でそのサービスを呼び出します。 各サービスは別々のコンテナーで実行されます。 その後、両方のコンテナーでデバッグします。

![複数のコンテナー](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>*mywebapi* のサンプル コードをダウンロードする
時間を節約するために、GitHub リポジトリからサンプル コードをダウンロードしましょう。 https://github.com/Azure/dev-spaces に移動し、**[Clone or download]** をクリックして GitHub リポジトリをダウンロードします。 このセクションのコードは、`samples/dotnetcore/getting-started/mywebapi` にあります。

### <a name="run-mywebapi"></a>*mywebapi* を実行する
1. "*別の VS Code ウィンドウ*" で、`mywebapi` フォルダーを開きます。
1. (**[表示 | コマンド パレット]** メニューを使用して) **コマンド パレット**を開き、オート コンプリートを使用して次のコマンドを入力および選択します: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`。 このコマンドと、デプロイ用にプロジェクトを構成する `azds prep` コマンドを混同しないでください。
1. F5 キーを押し、サービスがビルドされ、展開されるまで待ちます。 準備ができると、VS Code デバッグ バーが表示されます。
1. エンドポイント URL は http://localhost:\<portnumber\> のように表記されます。 **ヒント: VS Code のステータス バーに、クリック可能な URL が表示されます。** コンテナーはローカルで実行されているように見えますが、実際には Azure の開発空間で実行されています。 localhost アドレスである理由は、`mywebapi` でパブリック エンドポイントが定義されておらず、Kubernetes インスタンス内からしかアクセスできないためです。 利便性を考慮し、また、ローカル コンピューターからのプライベート サービスとの対話を容易にするために、Azure Dev Spaces では、Azure で実行されているコンテナーへの一時的な SSH トンネルが作成されます。
1. `mywebapi` の準備ができたら、ブラウザーで localhost アドレスを開きます。 URL に `/api/values` を追加して、`ValuesController` の既定の GET API を呼び出します。 
1. すべての手順が正常に完了すると、`mywebapi` サービスからの応答を確認できます。

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>*webfrontend* から *mywebapi* に対して要求を行う
次に、`mywebapi` に対して要求を行う `webfrontend` のコードを記述しましょう。
1. `webfrontend` の VS Code ウィンドウに切り替えます。
1. 次のように、About メソッドのコードを*置き換えます*。

    ```csharp
    public async Task<IActionResult> About()
    {
        ViewData["Message"] = "Hello from webfrontend";
        
        using (var client = new System.Net.Http.HttpClient())
            {
                // Call *mywebapi*, and display its response in the page
                var request = new System.Net.Http.HttpRequestMessage();
                request.RequestUri = new Uri("http://mywebapi/api/values/1");
                if (this.Request.Headers.ContainsKey("azds-route-as"))
                {
                    // Propagate the dev space routing header
                    request.Headers.Add("azds-route-as", this.Request.Headers["azds-route-as"] as IEnumerable<string>);
                }
                var response = await client.SendAsync(request);
                ViewData["Message"] += " and " + await response.Content.ReadAsStringAsync();
            }

        return View();
    }
    ```

前述のコード例は、`azds-route-as` ヘッダーを受信要求から送信要求に転送します。 これがチームによる共同開発にどのように役立つかについては、後ほど説明します。

### <a name="debug-across-multiple-services"></a>複数のサービスでデバッグする
1. この時点で、`mywebapi` は引き続きデバッガーがアタッチされた状態で実行されています。 そうでない場合は、`mywebapi` プロジェクトで F5 キーを押します。
1. `api/values/{id}` の GET 要求を処理する `Get(int id)` メソッドにブレークポイントを設定します。
1. `webfrontend` プロジェクトで、GET 要求を `mywebapi/api/values` に送信する直前にブレークポイントを設定します。
1. `webfrontend` プロジェクトで F5 キーを押します。
1. Web アプリを起動し、両方のサービスでコードをステップ実行します。
1. Web アプリの About ページに、2 つのサービスによって連結されたメッセージ ("Hello from webfrontend and Hello from mywebapi") が表示されます。


お疲れさまでした。 これで、各コンテナーを個別に開発して展開できる、複数コンテナー アプリケーションが作成されました。

## <a name="learn-about-team-development"></a>チーム開発について学ぶ

[!INCLUDE [](../../includes/team-development-1.md)]

実際の動作を見てみましょう。 `mywebapi` の VS Code ウィンドウに移動し、`string Get(int id)` メソッドのコードを編集します。次に例を示します。

```csharp
[HttpGet("{id}")]
public string Get(int id)
{
    return "mywebapi now says something new";
}
```


[!INCLUDE [](../../includes/team-development-2.md)]

### <a name="well-done"></a>お疲れさまでした。
ファースト ステップ ガイドを修了しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * Azure でマネージド Kubernetes クラスターを使用して Azure Dev Spaces をセットアップする。
> * コンテナー内のコードを繰り返し開発する。
> * 2 つのサービスを個別に開発し、Kubernetes の DNS サービス検索を使用して別のサービスを呼び出す。
> * チーム環境でコードを生産的に開発してテストする。

Azure Dev Spaces の概要を理解できたら、[開発空間をチーム メンバーと共有](how-to/share-dev-spaces.md)し、共同作業の容易さについてチームの理解を深めましょう。

## <a name="clean-up"></a>クリーンアップ
すべての開発スペースとその内部で実行されているサービスを含め、クラスター上の Azure Dev Spaces インスタンスを完全に削除するには、`az aks remove-dev-spaces` コマンドを使用します。 この操作は元に戻せないことに注意してください。 Azure Dev Spaces のサポートをクラスターに再度追加することはできますが、始めからやり直すようになります。 古いサービスとスペースは復元されません。

次の例では、アクティブなサブスクリプションの Azure Dev Spaces コントローラーを一覧表示し、リソース グループ "myaks-rg" の AKS クラスター "myaks" に関連付けられている Azure Dev Spaces コントローラーを削除します。

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
