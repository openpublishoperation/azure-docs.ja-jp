---
title: Azure Container Registry マルチ ステップ タスクによってイメージのビルド、テスト、および修正プログラムの適用を自動化する
description: Azure Container Registry の ACR タスクの機能である、マルチ ステップ タスクの概要。これは、クラウドでコンテナー イメージのビルド、テスト、および修正プログラムの適用を行うため、タスク ベースのワークフローを提供するものです。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 09/24/2018
ms.author: danlep
ms.openlocfilehash: cdabafc4f70b08076820e7e0d39300b3eb0bc1e7
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2018
ms.locfileid: "48856720"
---
# <a name="run-multi-step-build-test-and-patch-tasks-in-acr-tasks"></a>ACR タスクでビルド、テスト、および修正プログラムの適用を行うマルチ ステップ タスクを実行する

マルチ ステップ タスクは、複数のステップから成る複数コンテナー ベースのワークフローを持つ、ACR タスクの単一イメージのビルドおよびプッシュ機能を拡張します。 マルチ ステップ タスクを使用して、いくつかのイメージのビルドとプッシュを順番に、または並列で行い、それらのイメージを 1 回のタスク実行中にコマンドとして実行します。 各ステップでは、コンテナー イメージのビルド操作またはプッシュ操作を定義します。また、コンテナーの実行を定義することもできます。 マルチ ステップ タスクの各ステップは、その実行環境としてコンテナーを使用します。

> [!IMPORTANT]
> 以前、プレビュー期間中に `az acr build-task` コマンドを使用してタスクを作成した場合、それらのタスクは [az acr task][az-acr-task] コマンドを使用して再作成する必要があります。

たとえば、以下を自動化するステップを含むタスクを実行できます。

1. Web アプリケーション イメージをビルドする
1. Web アプリケーション コンテナーを実行する
1. Web アプリケーションのテスト イメージをビルドする
1. 実行中のアプリケーション コンテナーに対するテストを実行する Web アプリケーション テスト コンテナーを実行する
1. テストに成功した場合は Helm グラフのアーカイブ パッケージをビルドする
1. 新しい Helm グラフのアーカイブ パッケージを使用して `helm upgrade` を実行する

すべてのステップは Azure 内で実行されて、作業を Azure のコンピューティング リソースにオフロードし、ユーザーはインフラストラクチャの管理から解放されます。 Azure コンテナー レジストリのほかに、使用するリソースに対してのみ支払いを行います。 価格については、「[Azure Container Registry の価格][pricing]」の「**コンテナー ビルド**」セクションを参照してください。

> [!IMPORTANT]
> 現在、この機能はプレビュー段階にあります。 プレビュー版は、[追加使用条件][terms-of-use]に同意することを条件に使用できます。 この機能の一部の側面は、一般公開 (GA) 前に変更される可能性があります。

## <a name="common-task-scenarios"></a>一般的なタスク シナリオ

マルチ ステップ タスクでは、以下のようなシナリオが実現されます。

* 1 つ以上のコンテナー イメージのビルド、タグ付け、プッシュを順番または並列に行います。
* 単体テストとコード カバレッジを実行して結果をキャプチャします。
* 機能テストを実行してキャプチャします。 ACR タスクは複数のコンテナーの実行をサポートしており、それらの間で一連の要求を実行します。
* コンテナー イメージのビルドの前ステップと後ステップを含むタスク ベースの回を実行します。
* お気に入りのデプロイ エンジンを使用して、1 つ以上のコンテナーをターゲット環境にデプロイします。

## <a name="multi-step-task-definition"></a>マルチ ステップ タスクの定義

ACR タスクのマルチ ステップ タスクは、YAML ファイル内で一連のステップとして定義されます。 各ステップでは、前の 1 つ以上のステップが正常に完了することへの依存関係を指定できます。 以下に示すタスク ステップの種類を使用できます。

* [`build`](container-registry-tasks-reference-yaml.md#build): 使い慣れた `docker build` 構文を順番または並列で使用して、1 つ以上のコンテナー イメージをビルドします。
* [`push`](container-registry-tasks-reference-yaml.md#push): ビルドされたイメージをコンテナー レジストリにプッシュします。 パブリック Docker Hub と同様に、Azure Container Registry のようなプライベート レジストリがサポートされています。
* [`cmd`](container-registry-tasks-reference-yaml.md#cmd): 実行中のタスクのコンテキスト内で関数として動作できるように、コンテナーを実行します。 コンテナーの `[ENTRYPOINT]` にパラメーターを渡すことができます。また、env や detach などのプロパティや、その他の使い慣れた `docker run` パラメーターを指定できます。 `cmd` ステップの種類では、単体テストや機能テストに加え、コンテナーの同時実行が可能です。

マルチ ステップ タスクは、1 つのイメージのビルドやプッシュと同様にシンプルなものにできます。

```yaml
version: 1.0-preview-1
steps:
  - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
  - push: ["{{.Run.Registry}}/hello-world:{{.Run.ID}}"]
```

または、このタスクのように、より複雑なタスクです。これには、パッケージのビルド、テスト、helm でのパッケージ化、helm でのデプロイが含まれます。

```yaml
version: 1.0-preview-1
steps:
  - id: build-web
    build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
    when: ["-"]
  - id: build-tests
    build -t {{.Run.Registry}}/hello-world-tests ./funcTests
    when: ["-"]
  - id: push
    push: ["{{.Run.Registry}}/helloworld:{{.Run.ID}}"]
    when: ["build-web", "build-tests"]
  - id: hello-world-web
    cmd: {{.Run.Registry}}/helloworld:{{.Run.ID}}
  - id: funcTests
    cmd: {{.Run.Registry}}/helloworld:{{.Run.ID}}
    env: ["host=helloworld:80"]
  - cmd: {{.Run.Registry}}/functions/helm package --app-version {{.Run.ID}} -d ./helm ./helm/helloworld/
  - cmd: {{.Run.Registry}}/functions/helm upgrade helloworld ./helm/helloworld/ --reuse-values --set helloworld.image={{.Run.Registry}}/helloworld:{{.Run.ID}}
```

## <a name="run-a-sample-task"></a>サンプル タスクを実行する

タスクは、"クイック実行" と呼ばれる手動での実行と、Git でのコミット時および基本イメージの更新時の自動実行の両方をサポートしています。

タスクを実行するには、最初に YAML ファイルでタスクのステップを定義してから、Azure CLI コマンドの [az acr run][az-acr-run] を実行します。

以下に、サンプル タスクの YAML ファイルを使用してタスクを実行する Azure CLI コマンドの例を示します。 そのステップでは、イメージをビルドし、その後プッシュします。 コマンドを実行する前に、お使いの Azure コンテナー レジストリの名前で `\<acrName\>` を更新します。

```azurecli
az acr run --registry <acrName> -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

タスクを実行すると、出力には、YAML ファイルで定義されている各ステップの進行状況が表示されるはずです。 次の出力では、ステップは `acb_step_0` および `acb_step_1` として表示されています。

```console
$ az acr run --registry myregistry -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
Sending context to registry: myregistry...
Queued a run with ID: yd14
Waiting for an agent...
2018/09/12 20:08:44 Using acb_vol_0467fe58-f6ab-4dbd-a022-1bb487366941 as the home volume
2018/09/12 20:08:44 Creating Docker network: acb_default_network
2018/09/12 20:08:44 Successfully set up Docker network: acb_default_network
2018/09/12 20:08:44 Setting up Docker configuration...
2018/09/12 20:08:45 Successfully set up Docker configuration
2018/09/12 20:08:45 Logging in to registry: myregistry.azurecr-test.io
2018/09/12 20:08:46 Successfully logged in
2018/09/12 20:08:46 Executing step: acb_step_0
2018/09/12 20:08:46 Obtaining source code and scanning for dependencies...
2018/09/12 20:08:47 Successfully obtained source code and scanned for dependencies
Sending build context to Docker daemon  109.6kB
Step 1/1 : FROM hello-world
 ---> 4ab4c602aa5e
Successfully built 4ab4c602aa5e
Successfully tagged myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:48 Executing step: acb_step_1
2018/09/12 20:08:48 Pushing image: myregistry.azurecr-test.io/hello-world:yd14, attempt 1
The push refers to repository [myregistry.azurecr-test.io/hello-world]
428c97da766c: Preparing
428c97da766c: Layer already exists
yd14: digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812 size: 524
2018/09/12 20:08:55 Successfully pushed image: myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:55 Step id: acb_step_0 marked as successful (elapsed time in seconds: 2.035049)
2018/09/12 20:08:55 Populating digests for step id: acb_step_0...
2018/09/12 20:08:57 Successfully populated digests for step id: acb_step_0
2018/09/12 20:08:57 Step id: acb_step_1 marked as successful (elapsed time in seconds: 6.832391)
The following dependencies were found:
- image:
    registry: myregistry.azurecr-test.io
    repository: hello-world
    tag: yd14
    digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:0add3ace90ecb4adbf7777e9aacf18357296e799f81cabc9fde470971e499788
  git: {}


Run ID: yd14 was successful after 19s
```

Git でのコミット時または基本イメージの更新時のビルド自動化に関する詳細については、チュートリアル記事の「[イメージのビルドを自動化する](container-registry-tutorial-build-task.md)」と「[基本イメージ更新ビルド](container-registry-tutorial-base-image-update.md)」を参照してください。

## <a name="preview-feedback"></a>プレビューのフィードバック

ACR タスクのマルチ ステップ タスク機能はプレビュー段階ですが、フィードバックをご提供ください。 いくつかのフィードバック チャネルが用意されています。

* [問題](https://aka.ms/acr/issues) - 既存のバグや問題を参照したり、新しいものを記録したりする
* [UserVoice](https://aka.ms/acr/uservoice) - 既存の機能要求について提議したり、新しい要求を作成したりする
* [説明](https://aka.ms/acr/feedback) - Stack Overflow コミュニティで Azure Container Registry のディスカッションに参加する

## <a name="next-steps"></a>次の手順

マルチ ステップ タスクのリファレンスと例は以下に用意されています。

* [タスクのリファレンス](container-registry-tasks-reference-yaml.md) - タスク ステップの種類、プロパティ、使い方。
* [タスクの例][task-examples] - 簡単なものから複雑なものまで、いくつかのシナリオに向けた `task.yaml` ファイルの例。

<!-- IMAGES -->

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[task-examples]: https://github.com/Azure-Samples/acr-tasks
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-run]: /cli/azure/acr/run#az-acr-run
[az-acr-task]: /cli/azure/acr#az-acr-task