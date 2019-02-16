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


## Volume Control

 - ストリームのボリュームを操作・監視するには、WASAPIの`ISimpleAudioVolume`、`IAudioSessionEvent`インタフェースを使用する。

