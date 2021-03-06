---
title: Azure IoT Hub への利用統計情報の送信に関するクイック スタート (C) | Microsoft Docs
description: このクイック スタートでは、2 つのサンプル C アプリケーションを実行して、IoT ハブにシミュレートされた利用統計情報を送信し、クラウドで処理するために IoT ハブから利用統計情報を読み取ります。
author: wesmc7777
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 08/27/2018
ms.author: wesmc
ms.openlocfilehash: 3fa4c536313375ed88f6f0223218a663d4be3eb3
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2018
ms.locfileid: "49364777"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-the-telemetry-from-the-hub-with-a-back-end-application-c"></a>クイック スタート: デバイスから IoT ハブに利用統計情報を送信し、バックエンド アプリケーション (C) でハブから利用統計情報を読み取る

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub は、保管や処理のために IoT デバイスから大量の利用統計情報をクラウドに取り込むことを可能にする Azure サービスです。 このクイック スタートでは、シミュレートされたデバイス アプリケーションから、IoT ハブを経由して、処理のためのバックエンド アプリケーションに、利用統計情報を送信します。

クイック スタートでは、[C 対応の Azure IoT device SDK ](iot-hub-device-sdk-c-intro.md) から C サンプル アプリケーションを使用して、IoT ハブに利用統計情報を送信します。 Azure IoT device SDK は、移植性と広範なプラットフォーム互換性のために、[ANSI C (C99)](https://wikipedia.org/wiki/C99) で作成されています。 サンプル コードを実行する前に、IoT ハブを作成して、そのハブを使ってシミュレートされたデバイスを登録します。

この記事は Windows 用に書かれていますが、Linux 上でも、このクイック スタートを完了できます。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

* ["C++ によるデスクトップ開発"](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) ワークロードを有効にした [Visual Studio 2017](https://www.visualstudio.com/vs/) をインストールします。
* 最新バージョンの [Git](https://git-scm.com/download/) をインストールします。

## <a name="prepare-the-development-environment"></a>開発環境の準備

このクイック スタートでは、[C 対応の Azure IoT device SDK](iot-hub-device-sdk-c-intro.md) を使用します。 

次の環境にパッケージとライブラリをインストールして、SDK を使用できます。

* **Linux**: CPU アーキテクチャ amd64、arm64、armhf、および i386 を使用して、Ubuntu 16.04 および 18.04 用に apt-get パッケージが利用可能です。 詳細については、「[Using apt-get to create a C device client project on Ubuntu](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/ubuntu_apt-get_sample_setup.md)」(apt-get を使用して Ubuntu 上に C デバイス クライアント プロジェクトを作成する) を参照してください。

* **mbed**: mbed プラットフォーム上にデバイス アプリケーションを作成している開発者向けに、ライブラリとサンプルを公開しています。これを利用して、Azure IoT Hub の使用をすぐに開始できます。 詳細については、[mbed ライブラリの使用](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#mbed)に関するページを参照してください。

* **Arduino**: Arduino で開発している場合、Arduino IDE ライブラリ マネージャーで利用可能な Azure IoT ライブラリを活用できます。 詳細については、[Arduino 対応の Azure IoT Hub ライブラリ](https://github.com/azure/azure-iot-arduino)に関するページを参照してください。

* **iOS**: IoT Hub Device SDK は、Mac および iOS デバイス開発の CocoaPods として利用可能です。 詳細については、[Microsoft Azure IoT の iOS サンプル](https://cocoapods.org/pods/AzureIoTHubClient)に関するページを参照してください。

ただし、このクイック スタートでは、クローンに使用される開発環境を準備して、GitHub から [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) をビルドします。 GitHub 上の SDK には、このクイック スタートで使用されるサンプル コードが含まれます。 


1. [CMake ビルド システム](https://cmake.org/download/) バージョン 3.11.4 をダウンロードします。 ダウンロードしたバイナリを、対応する暗号化ハッシュ値を使用して検証します。 次の例では、Windows PowerShell を使用して、x64 MSI 配布のバージョン 3.11.4 の暗号化ハッシュを検証しています。

    ```PowerShell
    PS C:\Downloads> $hash = get-filehash .\cmake-3.11.4-win64-x64.msi
    PS C:\Downloads> $hash.Hash -eq "56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869"
    True
    ```
    
    この記事の執筆時点では、CMake サイトにバージョン 3.11.4 用に次のハッシュ値が一覧表示されていました。

    ```
    6dab016a6b82082b8bcd0f4d1e53418d6372015dd983d29367b9153f1a376435  cmake-3.11.4-Linux-x86_64.tar.gz
    72b3b82b6d2c2f3a375c0d2799c01819df8669dc55694c8b8daaf6232e873725  cmake-3.11.4-win32-x86.msi
    56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869  cmake-3.11.4-win64-x64.msi
    ```

    `CMake` のインストールを開始する**前に**、Visual Studio の前提条件 (Visual Studio と "C++ によるデスクトップ開発" ワークロード) が マシンにインストールされていることが重要です。 前提条件を満たし、ダウンロードを検証したら、CMake ビルド システムをインストールします。

2. コマンド プロンプトまたは Git Bash シェルを開きます。 次のコマンドを実行して、[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) の GitHub リポジトリを複製します。
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    このリポジトリのサイズは現在約 220 MB です。 この操作は、完了するまでに数分かかります。


3. git リポジトリのルート ディレクトリに `cmake` サブディレクトリを作成し、そのフォルダーに移動します。 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. 次のコマンドを実行して、開発クライアント プラットフォームに固有の SDK のバージョンをビルドします。 シミュレートされたデバイスの Visual Studio ソリューションが `cmake` ディレクトリに生成されます。 

    ```cmd
    cmake ..
    ```
    
    `cmake` で C++ コンパイラが見つからない場合は、上記のコマンドの実行中にビルド エラーが発生している可能性があります。 これが発生した場合は、[Visual Studio コマンド プロンプト](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)でこのコマンドを実行してください。 

    ビルドが成功すると、最後のいくつかの出力行は次のようになります。

    ```cmd/sh
    $ cmake ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```


## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]

## <a name="register-a-device"></a>デバイスの登録

デバイスを IoT ハブに接続するには、あらかじめ IoT ハブに登録しておく必要があります。 このセクションでは、[IoT 拡張機能](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest)と共に Azure Cloud Shell を使用して、シミュレートされたデバイスを登録します。

1. Azure Cloud Shell で次のコマンドを実行して IoT Hub CLI 拡張機能を追加し、デバイス ID を作成します。 

   **YourIoTHubName**: このプレースホルダーは、実際の IoT ハブに対して選んだ名前に置き換えてください。

   **MyCDevice**: これは、登録済みデバイスに付けられた名前です。 示されているように、MyCDevice を使用します。 デバイスに別の名前を選択した場合は、この記事全体でその名前を使用する必要があります。また、サンプル アプリケーションを実行する前に、アプリケーション内のデバイス名を更新してください。

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyCDevice
    ```

2. Azure Cloud Shell で次のコマンドを実行して、登録したデバイスの "_デバイス接続文字列_" を取得します。

   **YourIoTHubName**: このプレースホルダーは、実際の IoT ハブに対して選んだ名前に置き換えてください。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyCDevice --output table
    ```

    次のようなデバイス接続文字列をメモしておきます。

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    この値は、このクイック スタートの後の方で使います。

## <a name="send-simulated-telemetry"></a>シミュレートされた利用統計情報の送信

シミュレートされたデバイス アプリケーションは、IoT ハブ上のデバイス固有のエンドポイントに接続し、シミュレートされた利用統計情報として文字列を送信します。

1. テキスト エディターを使用して、iothub_convenience_sample.c ソース ファイルを開き、利用統計情報を送信するサンプル コードを確認します。 ファイルは、次の場所にあります。

    ```
    \azure-iot-sdk-c\iothub_client\samples\iothub_convenience_sample\iothub_convenience_sample.c
    ```

2. 次の `connectionString` 定数の宣言を探します。

    ```C
    /* Paste in your device connection string  */
    static const char* connectionString = "[device connection string]";
    ```
    `connectionString` 定数の値を、前にメモしたデバイス接続文字列に置き換えます。 その後、**iothub_convenience_sample.c** への変更を保存します。

3. ローカル ターミナル ウィンドウで、Azure IoT C SDK で作成した CMake ディレクトリの *iothub_convenience_sample* プロジェクト ディレクトリに移動します。

    ```
    cd /azure-iot-sdk-c/cmake/iothub_client/samples/iothub_convenience_sample
    ```

4. ローカル ターミナル ウィンドウで CMake を実行して、更新した `connectionString` 値を使用してサンプルをビルドします。

    ```cmd/sh
    cmake --build . --target iothub_convenience_sample --config Debug
    ```

5. ローカル ターミナル ウィンドウで次のコマンドを実行して、シミュレートされたデバイス アプリケーションを実行します。

    ```cmd/sh
    Debug\iothub_convenience_sample.exe
    ```

    次のスクリーンショットは、シミュレートされたデバイス アプリケーションが IoT ハブに利用統計情報を送信したときの出力を示しています。

    ![シミュレートされたデバイスを実行する](media/quickstart-send-telemetry-c/simulated-device-app.png)

## <a name="read-the-telemetry-from-your-hub"></a>ハブから利用統計情報を読み取る


このセクションでは、[IoT 拡張機能](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest)と共に Azure Cloud Shell を使用して、シミュレートされたデバイスから送信されるデバイス メッセージを監視します。

1. Azure Cloud Shell を使用して、次のコマンドを実行して接続し、お使いの IoT ハブからのメッセージを読み取ります。

   **YourIoTHubName**: このプレースホルダーは、実際の IoT ハブに対して選んだ名前に置き換えてください。

    ```azurecli-interactive
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

    ![Azure CLI を使用してデバイス メッセージを読み取る](media/quickstart-send-telemetry-c/read-device-to-cloud-messages-app.png)

    

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>次の手順

このクイック スタートでは、IoT ハブをセットアップして、デバイスを登録し、C アプリケーションを使ってハブにシミュレートされた利用統計情報を送信し、Azure Cloud Shell を使用してハブから利用統計情報を読み取りました。

Azure IoT Hub C SDK による開発の詳細については、引き続き次の攻略ガイドを参照してください。

> [!div class="nextstepaction"]
> [Azure IoT Hub C SDK を使用した開発](iot-hub-devguide-develop-for-constrained-devices.md)