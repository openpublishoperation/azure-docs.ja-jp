---
title: '例: 予測エンドポイントを使用してプログラムで分類器を指定してテストする - Custom Vision'
titlesuffix: Azure Cognitive Services
description: API を使用して、Custom Vision Service の分類子によるイメージのテストをプログラムから行う方法を説明します。
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: sample
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 3a81f3cef6aaeb5c98022d9fc93f4d84f3f58a6e
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/19/2018
ms.locfileid: "46363651"
---
# <a name="use-the-prediction-endpoint-to-test-images-programmatically-with-a-custom-vision-service-classifier"></a>予測エンドポイントを利用し、Custom Vision Service の分類子を使用してプログラムからイメージをテストします

モデルをトレーニングした後、イメージを Prediction API に送信することで、プログラムからイメージをテストできます。 

> [!NOTE]
> このドキュメントでは、C# を使用して Prediction API にイメージを送信する方法を示します。 この API の詳細と使用例については、[Prediction API のリファレンス](https://go.microsoft.com/fwlink/?linkid=865445)を参照してください。

## <a name="get-the-url-and-prediction-key"></a>URL と予測キーを取得する

[Custom Vision の Web ページ](https://customvision.ai)から自分のプロジェクトを選択し、__[Performance]__ \(パフォーマンス) タブを選択します。__予測キー__を含む Prediction API の使用に関する情報を表示するには、__[Prediction URL]\(Prediction の URL\)__ を選択します。 Azure リソースに関連付けられているプロジェクトの場合、__予測キー__は、関連付けられている Azure リソースの [Azure portal](https://portal.azure.com) ページの __[キー]__ で確認することもできます。 アプリケーションで使用するため、次の情報をコピーします。

* __[URL]__: __イメージファイル__ を使用するため。
* __[Prediction-key]__(予測キー) の値。

> [!TIP]
> 複数のイテレーションがある場合は、既定値として設定することで、どのイテレーションを使用するかを制御できます。 __[Iterations]__(イテレーション) セクションから目的のイテレーションを選択し、ページの上部にある __[Make default]__(既定値に設定) を選択します。

![パフォーマンスのタブが、[Prediction URL]\(Prediction の URL) を囲む赤色の四角形と共に表示されています。](./media/use-prediction-api/prediction-url.png)

## <a name="create-the-application"></a>アプリケーションを作成する

1. Visual Studio から、新しい C# コンソール アプリケーションを作成します。

2. __Program.cs__ ファイルの本文として次のコードを使用します。

    > [!IMPORTANT]
    > 次の情報を変更します。
    >
    > * __namespace__ はプロジェクトの名前に設定します。
    > * 前に受け取った __予測キー__ の値を、`client.DefaultRequestHeaders.Add("Prediction-Key",` で始まる行の中に設定します。
    > * 前に受け取った __URL__ の値を、`string url =` で始まる行の中に設定します。

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace CSPredictionSample
    {
        static class Program
        {
            static void Main()
            {
                Console.Write("Enter image file path: ");
                string imageFilePath = Console.ReadLine();

                MakePredictionRequest(imageFilePath).Wait();

                Console.WriteLine("\n\n\nHit ENTER to exit...");
                Console.ReadLine();
            }

            static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }

            static async Task MakePredictionRequest(string imageFilePath)
            {
                var client = new HttpClient();

                // Request headers - replace this example key with your valid subscription key.
                client.DefaultRequestHeaders.Add("Prediction-Key", "13hc77781f7e4b19b5fcdd72a8df7156");

                // Prediction URL - replace this example URL with your valid prediction URL.
                string url = "http://southcentralus.api.cognitive.microsoft.com/customvision/v1.0/prediction/d16e136c-5b0b-4b84-9341-6a3fff8fa7fe/image?iterationId=f4e573f6-9843-46db-8018-b01d034fd0f2";

                HttpResponseMessage response;

                // Request body. Try this sample with a locally stored image.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                using (var content = new ByteArrayContent(byteData))
                {
                    content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
                    response = await client.PostAsync(url, content);
                    Console.WriteLine(await response.Content.ReadAsStringAsync());
                }
            }
        }
    }
    ```

## <a name="use-the-application"></a>アプリケーションを使用する

アプリケーションを実行するときには、イメージ ファイルへのパスを入力します。 そのイメージが API に送信され、結果が JSON ドキュメントとして返されます。 次の JSON ドキュメントは応答の例です

```json
{
    "Id":"3f76364c-b8ae-4818-a2b2-2794cfbe377a",
    "Project":"2277aca4-7aff-4742-8afb-3682e251c913",
    "Iteration":"84105bfe-73b5-4fcc-addb-756c0de17df2",
    "Created":"2018-05-03T14:15:22.5659829Z",
    "Predictions":[
        {"TagId":"35ac2ad0-e3ef-4e60-b81f-052a1057a1ca","Tag":"dog","Probability":0.102716163},
        {"TagId":"28e1a872-3776-434c-8cf0-b612dd1a953c","Tag":"cat","Probability":0.02037274}
    ]
}
```

## <a name="next-steps"></a>次の手順

[モバイル利用のためにモデルをエクスポートする](export-your-model.md)
