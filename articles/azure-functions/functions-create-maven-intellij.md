---
title: Java と IntelliJ を使用して Azure 関数アプリを作成する | Microsoft Docs
description: Java と IntelliJ を使用して、単純な HTTP によってトリガーしたサーバーレス アプリを Azure Functions に公開するためのハウツー ガイド。
services: functions
documentationcenter: na
author: jeffhollan
manager: jpconnock
keywords: Azure Functions, 関数, イベント処理, コンピューティング, サーバーレス アーキテクチャ, Java
ms.service: azure-functions
ms.devlang: java
ms.topic: conceptual
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: b28e7b158af939defd37734c3ff9fe2309e3ab85
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2018
ms.locfileid: "44094401"
---
# <a name="create-your-first-function-with-java-and-intellij-preview"></a>Java と IntelliJ を使用して初めての関数を作成する (プレビュー)

> [!NOTE] 
> Azure Functions 用の Java は現在プレビュー段階です。

この記事では、IntelliJ IDEA と Apache Maven を使用して、[サーバーレス](https://azure.microsoft.com/overview/serverless-computing/)関数プロジェクトを作成し、IDE からユーザーのコンピューター上でそれをテストおよびデバッグして、Azure Functions にデプロイする方法を説明します。 

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>開発環境を設定する

Java と IntelliJ を使用して関数アプリを開発するには、以下のものがインストールされている必要があります。

-  [Java Developer Kit](https://www.azul.com/downloads/zulu/) バージョン 8。
-  [Apache Maven](https://maven.apache.org) バージョン 3.0 以降。
-  [IntelliJ IDEA](https://www.jetbrains.com/idea/download)、コミュニティまたは Maven ツールが付属した Ultimate。
-  [Azure CLI](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT] 
> このクイックスタートを行うには、JAVA_HOME 環境変数を JDK のインストール場所に設定する必要があります。

Azure Functions を記述、実行、デバッグするためのローカル開発環境を提供する、[Azure Functions Core Tools、バージョン 2](functions-run-local.md#v2) もインストールすることを強くお勧めします。 


## <a name="create-a-functions-project"></a>Functions プロジェクトを作成する

1. IntelliJ IDEA で、**[新しいプロジェクトの作成]** をクリックします。  
1. **[Maven]** から選択して作成します。
1. **[Create from archetype]\(アーキタイプからの作成\)** チェック ボックスをオンにして、**[Add Archetype]\(アーキタイプの追加\)** に [[azure-functions-archetype]](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype) を選択します。
    - GroupId: com.microsoft.azure
    - ArtifactId: azure-functions-archetype
    - バージョン: [中央リポジトリ](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)から最新バージョンを使用
    ![IntelliJ Maven create](media/functions-create-first-java-intellij/functions-create-intellij.png)  
1. **[次へ]** をクリックして、現在のプロジェクトの詳細を入力し、最後に **[完了]** をクリックします。

Maven は、_artifactId_ という名前の新しいフォルダーに、プロジェクト ファイルを作成します。 プロジェクトで生成されるコードは、トリガーする HTTP 要求の本文をエコーする、[HTTP によってトリガーされる](/azure/azure-functions/functions-bindings-http-webhook)単純な関数です。

## <a name="run-functions-locally-in-the-ide"></a>IDE で関数をローカルで実行する

> [!NOTE]
> 関数をローカルで実行およびデバッグするには、[Azure Functions Core Tools、バージョン 2](functions-run-local.md#v2) をインストールする必要があります。

1. 変更のインポートを選択するか、[自動インポート](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html)が有効になっていることを確認します。
1. **[Maven Projects]\(Maven プロジェクト\)** ツールバーを開きます。
1. [Lifecycle] の下の **[package]** をダブルクリックして、ソリューションをパッケージ化してビルドし、ターゲット ディレクトリを作成します。
1. [Plugins] -> [azure-functions] の下で **[azure-functions:run]** をダブルクリックして、Azure Functions ローカル ランタイムを開始します。  
  ![Azure Functions の Maven ツールバー](media/functions-create-first-java-intellij/functions-intellij-java-maven-toolbar.png)  

関数のテストが終了したら、実行ダイアログを閉じます。 アクティブにして同時にローカルで実行できる関数ホストは 1 つだけです。

### <a name="debug-the-function-in-intellij"></a>IntelliJ で関数をデバッグする
デバッグ モードで関数ホストを開始するには、関数の実行時に **-DenableDebug** を引数として追加します。 ターミナルで以下のコマンド ラインを実行するか、または [maven 目標](https://www.jetbrains.com/help/idea/maven-support.html#run_goal)で構成できます。 関数ホストは、5005 でデバッグ ポートを開きます。 

```
mvn azure-functions:run -DenableDebug
```

IntelliJ でデバッグするには、**[実行]** メニューで **[構成の編集]** を選択します。 **+** をクリックして **[リモート]** を追加します。 **[名前]** と **[設定]** を入力してから、**[OK]** をクリックして構成を保存します。 設定後は、'お使いのリモート構成名' の **[デバッグ]** をクリックするか、または **Shift+F9** を押してデバッグを開始します。

![IntelliJ での関数のデバッグ](media/functions-create-first-java-intellij/debug-configuration-intellij.PNG)

完了したら、デバッガーと実行中のプロセスを停止します。 アクティブにして同時にローカルで実行できる関数ホストは 1 つだけです。

## <a name="deploy-the-function-to-azure"></a>関数を Azure にデプロイする

Azure Functions へのデプロイ プロセスでは、Azure CLI からアカウントの資格情報を使います。 コンピューターのコマンド プロンプトを使用し続ける前に、[Azure CLI でログイン](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)します。

```azurecli
az login
```

`azure-functions:deploy` Maven ターゲットを使用して新しい Function アプリにコードをデプロイするか、[Maven Projects]\(Maven プロジェクト\) ウィンドウで [azure-functions:deploy] オプションを選択します。

```
mvn azure-functions:deploy
```

デプロイが完了すると、Azure 関数アプリへのアクセスに使うことができる URL が表示されます。

```output
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
```

## <a name="next-steps"></a>次の手順

- 「[Azure Functions Java developer guide](functions-reference-java.md)」(Azure Functions Java 開発者ガイド) で、Java 関数の開発の詳細について確認します。
- `azure-functions:add` Maven ターゲットを使って、異なるトリガーの新しい関数をプロジェクトに追加します。
