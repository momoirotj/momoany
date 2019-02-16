# Windowsボリューム設定調査

## Windows Core Audio API

[Core Audio APIs - Windows applications | Microsoft Docs](https://docs.microsoft.com/ja-jp/windows/desktop/CoreAudio/core-audio-apis-in-windows-vista)

 - Core Audio APIはWindows Vista以降のWindowsで使用できる
 - ヘッドフォンやマイクなどのオーディオ終端デバイスへのアクセスを提供する
 - Direct SoundやWindows multimedia waveXxx関数などの高機能なオーディオAPI基盤

Core Audioのコンポーネントは4つのヘッダファイルとインタフェースを定義を含む

 - MMDevice API(Mddeviceapi.h)
 - WASAPI(Audioclient.h/Audiopolicy.h)
 - DeviceTopology API(Devicetopology.h)
 - Endpoint Volume API(Endpointvolume.h)

 - endpoint deviceとは、スピーカー、マイク、ヘッドフォンなどの物理的なオーディオデバイスの抽象概念
 - WASAPIは、endpoint bufferを通じてendpoint deviceへデータを渡す
 - WASAPIは、2種類の方法でendpoint bufferへデータを渡す
 	 - *shared mode* オーディオエンジンへendpoint bufferをいったん渡し、他のプロセスで実行されているアプリケーションのオーディオと共有する。オーディオエンジンは渡されたアプリケーションごとのオーディオをミックスして再生する。
	 - *exclusive mode* オーディオハードウェアへ排他的にアクセスできる(プロ/RTC用なので通常使用しない)
 - Windows7で**low-letance mode**が追加された
	 - オーディオエンジンがpull modeで動作し、待ち時間が短縮される。処理時間に制約がある場合などに使用する。

オーディオエンジン

 - オーディオエンジンは、PCMフォーマットをミックスする
 - 排他モードでは、非PCMフォーマットも対応する
 - オーディオエンジンはアプリケーションとは別の保護されたプロセスで実行される。
 - Windows Audio Serviceがメモリを割り当てる

## endpoint device

 - MultiMediaDevice(MMDevice)APIは、endpoint deviceを検出し、インスタンスを作成する。
 - IMMDeviceEnumeratorインターフェースでアクセスすることができる。
 - IMMDeviceEnumeratorは、CoCreateInstance関数で取得する。
 - CLSID\_MMDeviceEnumeratorとIID\_IMMDeviceEnumeratorは、CoCreateInstanceに渡すGUID

 - endpoint deviceの識別には、endpoint idという文字列を使用する
 - endpoint idは、ヌル文字で終了するワイド文字列
 - endpoint idは、デバイスを一意に識別する。フレンドリ名は、同じ種類のデバイスで共通。
 - IMMDeviceのGetIdでendpoint idを取得できる
 - IMMDeviceEnumeratorのGetDeviceメソッドにendpoint idを渡すことで、別のプロセスでもendpoint deviceを使用できる

 - IMMNotificationClientで、endpoint deviceのステータスの変化を取得できる


## Volume Control

 - ストリームのボリュームを操作・監視するには、WASAPIの`ISimpleAudioVolume`、`IAudioSessionEvent`インタフェースを使用する。

