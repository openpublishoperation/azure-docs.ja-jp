---
title: Media Services 用の Azure Event Grid スキーマ
description: Azure Event Grid で Media Services イベント用に用意されているプロパティについて説明します
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: reference
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: 44e195055c74babd903cf4fb830167ab92951d4a
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2018
ms.locfileid: "49376790"
---
# <a name="azure-event-grid-schemas-for-media-services-events"></a>Media Services 用の Azure Event Grid スキーマ

この記事では、Media Services イベント用のスキーマとプロパティについて説明します。

サンプル スクリプトとチュートリアルの一覧については、[Media Services のイベント ソース](../../event-grid/event-sources.md#azure-subscriptions)に関する記事を参照してください。

## <a name="available-event-types"></a>使用可能なイベントの種類

### <a name="job-related-event-types"></a>ジョブに関連するイベントの種類

Media Services では、以下の種類の**ジョブ**関連イベントが出力されます。 **ジョブ**関連イベントには 2 つのカテゴリがあります。"ジョブの状態変更の監視" と "ジョブ出力の状態変更の監視" です。 

JobStateChange イベントをサブスクライブすると、すべてのイベントを登録できます。 また、特定のイベントのみ (たとえば、JobErrored、JobFinished、JobCanceled などの最終状態) をサブスクライブすることもできます。 

#### <a name="monitoring-job-state-changes"></a>ジョブの状態変更の監視

| イベントの種類 | 説明 |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| ジョブの状態変更のイベントをすべて取得します。 |
| Microsoft.Media.JobScheduled| ジョブがスケジュール済みの状態に遷移したときにイベントを取得します。 |
| Microsoft.Media.JobProcessing| ジョブが処理中の状態に遷移したときにイベントを取得します。 |
| Microsoft.Media.JobCanceling| ジョブが取り消し中の状態に遷移したときにイベントを取得します。 |
| Microsoft.Media.JobFinished| ジョブが終了中の状態に遷移したときにイベントを取得します。 これは、ジョブの出力を含む最終状態です。|
| Microsoft.Media.JobCanceled| ジョブが取り消し済みの状態に遷移したときにイベントを取得します。 これは、ジョブの出力を含む最終状態です。|
| Microsoft.Media.JobErrored| ジョブがエラー状態に遷移したときにイベントを取得します。 これは、ジョブの出力を含む最終状態です。|

#### <a name="monitoring-job-output-state-changes"></a>ジョブ出力の状態変更の監視

| イベントの種類 | 説明 |
| ---------- | ----------- |
| Microsoft.Media.JobOutputStateChange| ジョブ出力の状態変更のイベントをすべて取得します。 |
| Microsoft.Media.JobOutputScheduled| ジョブ出力がスケジュール済みの状態に遷移したときにイベントを取得します。 |
| Microsoft.Media.JobOutputProcessing| ジョブ出力が処理中の状態に遷移したときにイベントを取得します。 |
| Microsoft.Media.JobOutputCanceling| ジョブ出力が取り消し中の状態に遷移したときにイベントを取得します。|
| Microsoft.Media.JobOutputFinished| ジョブ出力が終了済みの状態に遷移したときにイベントを取得します。|
| Microsoft.Media.JobOutputCanceled| ジョブ出力が取り消し済みの状態に遷移したときにイベントを取得します。|
| Microsoft.Media.JobOutputErrored| ジョブ出力がエラー状態に遷移したときにイベントを取得します。|

### <a name="live-event-types"></a>ライブ イベントの種類

Media Services では、以下の種類の**ライブ** イベントも出力されます。 **Live** イベントには、ストリーム レベル イベントとトラック レベル イベントの 2 つのカテゴリがあります。 

#### <a name="stream-level-events"></a>ストリーム レベル イベント

ストリーム レベル イベントは、ストリームまたは接続ごとに発生します。 各イベントには、接続またはストリームを識別する `StreamId` パラメーターがあります。 各ストリームまたは接続には、さまざまな種類のトラックが少なくとも 1 つ存在します。 たとえば、エンコーダーからの接続 1 つにつき、1 つのオーディオ トラックと 4 つのビデオ トラックが存在する場合もあります。 ストリーム イベントには、次の種類があります。

| イベントの種類 | 説明 |
| ---------- | ----------- |
| Microsoft.Media.LiveEventConnectionRejected | エンコーダーの接続試行が拒否されました。 |
| Microsoft.Media.LiveEventEncoderConnected | エンコーダーがライブ イベントとの接続を確立しました。 |
| Microsoft.Media.LiveEventEncoderDisconnected | エンコーダーが切断されました。 |

#### <a name="track-level-events"></a>トラック レベル イベント

トラック レベル イベントは、トラックごとに発生します。トラック イベントには、次の種類があります。

| イベントの種類 | 説明 |
| ---------- | ----------- |
| Microsoft.Media.LiveEventIncomingDataChunkDropped | データ チャンクが遅すぎるか、タイムスタンプが重複している (新しいデータ チャンクのタイムスタンプが前のデータ チャンクの終了時間よりも古い) ため、メディア サーバーがデータ チャンクをドロップしました。 |
| Microsoft.Media.LiveEventIncomingStreamReceived | メディア サーバーが、ストリームまたは接続において、各トラックの最初のデータ チャンクを受信しました。 |
| Microsoft.Media.LiveEventIncomingStreamsOutOfSync | オーディオ ストリームとビデオ ストリームの同期がずれていることをメディア サーバーが検出しました。ユーザー エクスペリエンスへの影響はないと考えられるため、警告として使用してください。 |
| Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync | 外部エンコーダーから受信している 2 つのビデオ ストリームの同期がずれていることをメディア サーバーが検出しました。ユーザー エクスペリエンスへの影響はないと考えられるため、警告として使用してください。 |
| Microsoft.Media.LiveEventIngestHeartbeat | ライブ イベントの実行中、各トラックについて 20 秒ごとに発行されます。 取り込みの正常性についての概要が得られます。 |
| Microsoft.Media.LiveEventTrackDiscontinuityDetected | メディア サーバーが受信中のトラックの途切れを検出しました。 |

## <a name="event-schemas-and-properties"></a>イベントのスキーマとプロパティ

### <a name="jobstatechange"></a>JobStateChange

次の例は、**JobStateChange** イベントのスキーマを示しています。 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
    "eventType": "Microsoft.Media.JobStateChange",
    "eventTime": "2018-04-20T21:26:13.8978772",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

データ オブジェクトには、次のプロパティがあります。

| プロパティ | type | 説明 |
| -------- | ---- | ----------- |
| previousState | string | イベントの前のジョブの状態。 |
| state | string | このイベントで通知されるジョブの新しい状態。 例: "Queued: Job はリソースを待っています" や "Scheduled: Job は開始する準備ができています"。|

Job の状態は、次の値のいずれかが可能です。*Queued**Scheduled**Processing**Finished**Error**Canceled**Canceling*。

### <a name="jobscheduled"></a>JobScheduled
### <a name="jobprocessing"></a>JobProcessing
### <a name="jobcanceling"></a>JobCanceling

非最終ジョブの状態変更 (JobScheduled、JobProcessing、JobCanceling など) については、 サンプル スキーマは次のようになります。

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobProcessing",
  "eventTime": "2018-10-12T16:12:18.0839935",
  "id": "a0a6efc8-f647-4fc2-be73-861fa25ba2db",
  "data": {
    "previousState": "Scheduled",
    "state": "Processing",
    "correlationData": {
      "TestKey1": "TestValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="jobfinished"></a>JobFinished
### <a name="jobcanceled"></a>JobCanceled
### <a name="joberrored"></a>JobErrored

最終ジョブの状態変更 (JobFinished、JobCanceled、JobErrored など) については、 サンプル スキーマは次のようになります。

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobFinished",
  "eventTime": "2018-10-12T16:25:56.4115495",
  "id": "9e07e83a-dd6e-466b-a62f-27521b216f2a",
  "data": {
    "outputs": [
      {
        "@odata.type": "#Microsoft.Media.JobOutputAsset",
        "assetName": "output-7640689F",
        "error": null,
        "label": "VideoAnalyzerPreset_0",
        "progress": 100,
        "state": "Finished"
      }
    ],
    "previousState": "Processing",
    "state": "Finished",
    "correlationData": {
      "TestKey1": "TestValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

データ オブジェクトには、次のプロパティがあります。

| プロパティ | type | 説明 |
| -------- | ---- | ----------- |
| 出力 | Array | ジョブ出力を取得します。|

### <a name="joboutputstatechange"></a>JobOutputStateChange

次の例は、**JobOutputStateChange** イベントのスキーマを示しています。

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobOutputStateChange",
  "eventTime": "2018-10-12T16:25:56.0242854",
  "id": "dde85f46-b459-4775-b5c7-befe8e32cf90",
  "data": {
    "previousState": "Processing",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 100,
      "state": "Finished"
    },
    "jobCorrelationData": {
      "TestKey1": "TestValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="joboutputscheduled"></a>JobOutputScheduled
### <a name="joboutputprocessing"></a>JobOutputProcessing
### <a name="joboutputfinished"></a>JobOutputFinished
### <a name="joboutputcanceling"></a>JobOutputCanceling
### <a name="joboutputcanceled"></a>JobOutputCanceled
### <a name="joboutputerrored"></a>JobOutputErrored

各 JobOutput の状態変更については、サンプル スキーマは次のようになります。

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobOutputProcessing",
  "eventTime": "2018-10-12T16:12:18.0061141",
  "id": "f1fd5338-1b6c-4e31-83c9-cd7c88d2aedb",
  "data": {
    "previousState": "Scheduled",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 0,
      "state": "Processing"
    },
    "jobCorrelationData": {
      "TestKey1": "TestValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

次の例は、**LiveEventConnectionRejected** イベントのスキーマを示しています。 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventConnectionRejected",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "b303db59-d5c1-47eb-927a-3650875fded1",
    "data": { 
      "StreamId":"Mystream1",
      "IngestUrl": "http://abc.ingest.isml",
      "EncoderIp": "118.238.251.xxx",
      "EncoderPort": 52859,
      "ResultCode": "MPE_INGEST_CODEC_NOT_SUPPORTED"
    },
    "dataVersion": "1.0"
  }
]
```

データ オブジェクトには、次のプロパティがあります。

| プロパティ | type | 説明 |
| -------- | ---- | ----------- |
| StreamId | string | ストリームまたは接続の識別子。 この ID は、エンコーダーまたはカスタマーが取り込み URL に追加します。 |  
| IngestUrl | string | ライブ イベントから提供される取り込み URL。 |  
| EncoderIp | string | エンコーダーの IP。 |
| EncoderPort | string | このストリームの送信元であるエンコーダーのポート。 |
| ResultCode | string | 接続が拒否された理由。 結果コードについては、以下の表をご覧ください。 |

次に示したのは、結果コードの一覧です。

| 結果コード | 説明 |
| ----------- | ----------- |
| MPE_RTMP_APPID_AUTH_FAILURE | 取り込み URL が正しくありません。 |
| MPE_INGEST_ENCODER_CONNECTION_DENIED | 構成されている IP 許可リストにエンコーダー IP が存在しません。 |
| MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED | ストリームに関するメタデータがエンコーダーから送信されませんでした。 |
| MPE_INGEST_CODEC_NOT_SUPPORTED | 指定されたコーデックはサポートされていません。 |
| MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED | ストリームのヘッダーを受信する前にそのフラグメントを受信しました。 |
| MPE_INGEST_MEDIA_QUALITIES_EXCEEDED | 指定された品質の数が、許容される上限を超えています。 |
| MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED | 集約されたビットレートが、許容される上限を超えています。 |
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | RTMP エンコーダーからのビデオまたはオーディオ FLVTag のタイムスタンプが無効です。 |

### <a name="liveeventencoderconnected"></a>LiveEventEncoderConnected

次の例は、**LiveEventEncoderConnected** イベントのスキーマを示しています。 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderConnected",
    "eventTime": "2018-08-07T23:08:09.1710643",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

データ オブジェクトには、次のプロパティがあります。

| プロパティ | type | 説明 |
| -------- | ---- | ----------- |
| StreamId | string | ストリームまたは接続の識別子。 この ID は、エンコーダーまたはカスタマーが取り込み URL に指定します。 |
| IngestUrl | string | ライブ イベントから提供される取り込み URL。 |
| EncoderIp | string | エンコーダーの IP。 |
| EncoderPort | string | このストリームの送信元であるエンコーダーのポート。 |

### <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

次の例は、**LiveEventEncoderDisconnected** イベントのスキーマを示しています。 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderDisconnected",
    "eventTime": "2018-08-07T23:08:09.1710872",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "resultCode": "S_OK"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

データ オブジェクトには、次のプロパティがあります。

| プロパティ | type | 説明 |
| -------- | ---- | ----------- |
| StreamId | string | ストリームまたは接続の識別子。 この ID は、エンコーダーまたはカスタマーが取り込み URL に追加します。 |  
| IngestUrl | string | ライブ イベントから提供される取り込み URL。 |  
| EncoderIp | string | エンコーダーの IP。 |
| EncoderPort | string | このストリームの送信元であるエンコーダーのポート。 |
| ResultCode | string | エンコーダーの切断の理由。 正常な切断とエラーによる切断とがあります。 結果コードについては、以下の表をご覧ください。 |

次に示したのは、エラー結果コードの一覧です。

| 結果コード | 説明 |
| ----------- | ----------- |
| MPE_RTMP_SESSION_IDLE_TIMEOUT | RTMP セッションは、アイドル状態のまま許容制限時間を超えてタイムアウトしました。 |
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | RTMP エンコーダーからのビデオまたはオーディオ FLVTag のタイムスタンプが無効です。 |
| MPE_CAPACITY_LIMIT_REACHED | エンコーダーからデータが送信される速度が速すぎます。 |
| 不明なエラー コード | これらのエラー コードは、メモリ エラーから、ハッシュ マップにおけるエントリの重複まで多岐にわたります。 |

正常な切断の結果コードは次のとおりです。

| 結果コード | 説明 |
| ----------- | ----------- |
| S_OK | エンコーダーが正常に切断されました。 |
| MPE_CLIENT_TERMINATED_SESSION | エンコーダーが切断されました (RTMP)。 |
| MPE_CLIENT_DISCONNECTED | エンコーダーが切断されました (FMP4)。 |
| MPI_REST_API_CHANNEL_RESET | チャンネルのリセット コマンドを受信しました。 |
| MPI_REST_API_CHANNEL_STOP | チャンネルの停止コマンドを受信しました。 |
| MPI_REST_API_CHANNEL_STOP | チャンネルはメンテナンス中です。 |
| MPI_STREAM_HIT_EOF | エンコーダーから EOF ストリームが送信されました。 |

### <a name="liveeventincomingdatachunkdropped"></a>LiveEventIncomingDataChunkDropped

次の例は、**LiveEventIncomingDataChunkDropped** イベントのスキーマを示しています。 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingDataChunkDropped",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "03da9c10-fde7-48e1-80d8-49936f2c3e7d",
    "data": { 
      "TrackType": "Video",
      "TrackName": "Video",
      "Bitrate": 300000,
      "Timestamp": 36656620000,
      "Timescale": 10000000,
      "ResultCode": "FragmentDrop_OverlapTimestamp"
    },
    "dataVersion": "1.0"
  }
]
```

データ オブジェクトには、次のプロパティがあります。

| プロパティ | type | 説明 |
| -------- | ---- | ----------- |
| TrackType | string | 追跡のタイプ (オーディオ/ビデオ)。 |
| TrackName | string | トラックの名前。 |
| Bitrate | integer | トラックのビットレート。 |
| Timestamp | string | ドロップされたデータ チャンクのタイムスタンプ。 |
| タイムスケール | string | タイムスタンプのタイムスケール。 |
| ResultCode | string | データ チャンクがドロップされた理由。 **FragmentDrop_OverlapTimestamp** または **FragmentDrop_NonIncreasingTimestamp**。 |

### <a name="liveeventincomingstreamreceived"></a>LiveEventIncomingStreamReceived

次の例は、**LiveEventIncomingStreamReceived** イベントのスキーマを示しています。 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamReceived",
    "eventTime": "2018-08-07T23:08:10.5069288Z",
    "id": "7f939a08-320c-47e7-8250-43dcfc04ab4d",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml/Streams(15864-stream0)15864-stream0",
      "trackType": "video",
      "trackName": "video",
      "bitrate": 2962000,
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "timestamp": "15336831655032322",
      "duration": "20000000",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

データ オブジェクトには、次のプロパティがあります。

| プロパティ | type | 説明 |
| -------- | ---- | ----------- |
| TrackType | string | 追跡のタイプ (オーディオ/ビデオ)。 |
| TrackName | string | トラックの名前。エンコーダーによって指定されるか、または RTMP の場合は、*TrackType_Bitrate* 形式でサーバーによって生成されます。 |
| Bitrate | integer | トラックのビットレート。 |
| IngestUrl | string | ライブ イベントから提供される取り込み URL。 |
| EncoderIp | string  | エンコーダーの IP。 |
| EncoderPort | string | このストリームの送信元であるエンコーダーのポート。 |
| Timestamp | string | 受信したデータ チャンクの最初のタイムスタンプ。 |
| タイムスケール | string | タイムスタンプの表示に使用されるタイムスケール。 |

### <a name="liveeventincomingstreamsoutofsync"></a>LiveEventIncomingStreamsOutOfSync

次の例は、**LiveEventIncomingStreamsOutOfSync** イベントのスキーマを示しています。 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamsOutOfSync",
    "eventTime": "2018-08-10T02:26:20.6269183Z",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "minLastTimestamp": "319996",
      "typeOfStreamWithMinLastTimestamp": "Audio",
      "maxLastTimestamp": "366000",
      "typeOfStreamWithMaxLastTimestamp": "Video",
      "timescaleOfMinLastTimestamp": "10000000", 
      "timescaleOfMaxLastTimestamp": "10000000"       
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

データ オブジェクトには、次のプロパティがあります。

| プロパティ | type | 説明 |
| -------- | ---- | ----------- |
| MinLastTimestamp | string | 全トラック (オーディオまたはビデオ) における最後のタイムスタンプの最小値。 |
| TypeOfTrackWithMinLastTimestamp | string | 最後のタイムスタンプが最も小さいトラックの種類 (オーディオまたはビデオ)。 |
| MaxLastTimestamp | string | 全トラック (オーディオまたはビデオ) におけるすべてのタイムスタンプの最大値。 |
| TypeOfTrackWithMaxLastTimestamp | string | 最後のタイムスタンプが最も大きいトラックの種類 (オーディオまたはビデオ)。 |
| TimescaleOfMinLastTimestamp| string | "MinLastTimestamp" の表示に使用されるタイムスケールを取得します。|
| TimescaleOfMaxLastTimestamp| string | "MaxLastTimestamp" の表示に使用されるタイムスケールを取得します。|

### <a name="liveeventincomingvideostreamsoutofsync"></a>LiveEventIncomingVideoStreamsOutOfSync

次の例は、**LiveEventIncomingVideoStreamsOutOfSync** イベントのスキーマを示しています。 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/LiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "6dd4d862-d442-40a0-b9f3-fc14bcf6d750",
    "data": {
      "FirstTimestamp": "2162058216",
      "FirstDuration": "2000",
      "SecondTimestamp": "2162057216",
      "SecondDuration": "2000",
      "timescale": "10000000"      
    },
    "dataVersion": "1.0"
  }
]
```

データ オブジェクトには、次のプロパティがあります。

| プロパティ | type | 説明 |
| -------- | ---- | ----------- |
| FirstTimestamp | string | 種類がビデオであるいずれかのトラック/品質レベルについて受信したタイムスタンプ。 |
| FirstDuration | string | 1 つ目のタイムスタンプを持つデータ チャンクの期間。 |
| SecondTimestamp | string  | 種類がビデオである他の何らかのトラック/品質レベルについて受信したタイムスタンプ。 |
| SecondDuration | string | 2 つ目のタイムスタンプを持つデータ チャンクの期間。 |
| タイムスケール | string | タイムスタンプと時間のタイムスケール。|

### <a name="liveeventingestheartbeat"></a>LiveEventIngestHeartbeat

次の例は、**LiveEventIngestHeartbeat** イベントのスキーマを示しています。 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIngestHeartbeat",
    "eventTime": "2018-08-07T23:17:57.4610506",
    "id": "7f450938-491f-41e1-b06f-c6cd3965d786",
    "data": {
      "trackType": "audio",
      "trackName": "audio",
      "bitrate": 160000,
      "incomingBitrate": 155903,
      "lastTimestamp": "15336837535253637",
      "timescale": "10000000",
      "overlapCount": 0,
      "discontinuityCount": 0,
      "nonincreasingCount": 0,
      "unexpectedBitrate": false,
      "state": "Running",
      "healthy": true
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

データ オブジェクトには、次のプロパティがあります。

| プロパティ | type | 説明 |
| -------- | ---- | ----------- |
| TrackType | string | 追跡のタイプ (オーディオ/ビデオ)。 |
| TrackName | string | トラックの名前。エンコーダーによって指定されるか、または RTMP の場合は、*TrackType_Bitrate* 形式でサーバーによって生成されます。 |
| Bitrate | integer | トラックのビットレート。 |
| IncomingBitrate | integer | エンコーダーから送信されるデータ チャンクに基づいて計算されたビットレート。 |
| LastTimestamp | string | トラックに関して直近 20 秒に受信した最新のタイムスタンプ。 |
| タイムスケール | string | タイムスタンプの表示に使用されるタイムスケール。 |
| OverlapCount | integer | 直近 20 秒にタイムスタンプが重複したデータ チャンクの数。 |
| DiscontinuityCount | integer | 直近 20 秒に観察された途切れの数。 |
| NonIncreasingCount | integer | 直近 20 秒に過去のタイムスタンプを受信したデータ チャンクの数。 |
| UnexpectedBitrate | bool | 直近 20 秒における予想ビットレートと実ビットレートの差が、許容されている上限を超えているかどうか。 IncomingBitrate がビットレートの 2 倍以上または IncomingBitrate がビットレートの 1/2 以下または IncomingBitrate が 0 のとき、かつそのときに限り true。 |
| 状態 | string | ライブ イベントの状態。 |
| Healthy | bool | カウントとフラグに基づき、取り込みが正常であるかどうかを示します。 OverlapCount = 0 && DiscontinuityCount = 0 && NonIncreasingCount = 0 && UnexpectedBitrate = false の場合、Healthy は true になります。 |

### <a name="liveeventtrackdiscontinuitydetected"></a>LiveEventTrackDiscontinuityDetected

次の例は、**LiveEventTrackDiscontinuityDetected** イベントのスキーマを示しています。 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventTrackDiscontinuityDetected",
    "eventTime": "2018-08-07T23:18:06.1270405Z",
    "id": "5f4c510d-5be7-4bef-baf0-64b828be9c9b",
    "data": {
      "trackName": "video",
      "previousTimestamp": "15336837615032322",
      "trackType": "video",
      "bitrate": 2962000,
      "newTimestamp": "15336837619774273",
      "discontinuityGap": "575284",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

データ オブジェクトには、次のプロパティがあります。

| プロパティ | type | 説明 |
| -------- | ---- | ----------- |
| TrackType | string | 追跡のタイプ (オーディオ/ビデオ)。 |
| TrackName | string | トラックの名前。エンコーダーによって指定されるか、または RTMP の場合は、*TrackType_Bitrate* 形式でサーバーによって生成されます。 |
| Bitrate | integer | トラックのビットレート。 |
| PreviousTimestamp | string | 前のフラグメントのタイムスタンプ。 |
| NewTimestamp | string | 現在のフラグメントのタイムスタンプ。 |
| DiscontinuityGap | string | 2 つのタイムスタンプの差。 |
| タイムスケール | string | タイムスタンプと不連続性の両方について、差を表すときに使用されるタイムスケール。 |

### <a name="common-event-properties"></a>共通のイベント プロパティ

イベントのトップレベルのデータを次に示します。

| プロパティ | type | 説明 |
| -------- | ---- | ----------- |
| topic | string | EventGrid トピック。 このプロパティは、Media Services アカウントのリソース ID を保持します。 |
| subject | string | Media Services アカウント下の Media Services チャンネルのリソース パス。 トピックとサブジェクトを連結することで、ジョブのリソース ID が得られます。 |
| eventType | string | このイベント ソース用に登録されたイベントの種類のいずれか。 例: "Microsoft.Media.JobStateChange" |
| eventTime | string | プロバイダーの UTC 時刻に基づくイベントの生成時刻。 |
| id | string | イベントの一意識別子。 |
| data | オブジェクト | Media Services イベント データ。 |
| dataVersion | string | データ オブジェクトのスキーマ バージョン。 スキーマ バージョンは発行元によって定義されます。 |
| metadataVersion | string | イベント メタデータのスキーマ バージョン。 最上位プロパティのスキーマは Event Grid によって定義されます。 この値は Event Grid によって指定されます。 |

## <a name="next-steps"></a>次の手順

[ジョブ状態変更イベントを登録する](job-state-events-cli-how-to.md)
