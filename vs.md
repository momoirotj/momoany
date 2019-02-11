# Visual Studio

---------------------------------------------------------------------------------------------------

## 作成できるアプリケーション

 - コマンドラインアプリケーション

	コマンドプロンプトを表示するCUIアプリケーション


 - デスクトップアプリケーション

	ウィンドウをもつGUIアプリケーション

	 - 次のスタイルを選べる

		 - 従来のC言語のループにプログラミングをするスタイル(構造型)
		 - C++でライブラリを使用するスタイル(オブジェクト指向)
		 - C#でMFCを使用する.NETスタイル(オブジェクト指向)

	 - どのスタイルを選ぶか

		 - 通常はC# MFCスタイルを選ぶ(簡単)
		 - 高度なパフォーマンスを要求する場合はC/C++スタイルを選ぶ(難しい)


 - COMコンポーネント

	Windows OS上で動作する、いろんな言語で書かれたプログラムから参照することができるプログラム


 - UWP(Universal Windows Platform)

	Windows10上で動作する、タッチ対応のWindows API


 - ゲーム

	DirectXを使用したPC/Xboxのためのゲーム


 - C++ライブラリ用の.NETラッパ
 - Windowsデバイドライバ
 - Windowsサービス(バックグラウンド実行プログラムを作る場合に必要)
 - SDK/ライブラリ/ヘッダ
 - 開発ツール

---------------------------------------------------------------------------------------------------

## データ型

WinDef.hで定義されている

変数名はハンガリアン記法で記述する


### 数値

| データ型 | サイズ | 符号 |  
|----------|--------|------|  
| BYTE | 8bit | Unsigned |  
| DWORD | 32bit | Unsigned |  
| INT32 | 32bit | Signed |  
| INT64 | 64bit | Signed |  
| LONG | 32bit | Signed |  
| LONGLONG | 64bit | Signed |  
| UINT32 | 32bit | Unsigned |  
| UINT64 | 64bit | Unsigned |  
| ULONG  | 32bit | Unsigned |  
| ULONGLONG | 64bit | Unsigned |  
| WORD | 16bit | Unsigned |  

 - 32bit/64bitプラットフォームに依存してサイズが変わらない
 - 重複して定義されているのはWindowsの歴史的な理由


### BOOL型

FALSE = 0, TRUE = 1


### ポインタ型

型名の先頭にP/LPがついたものは、ポインタ型を示す  
`RECT*`と`LPRECT`と`PRECT`はすべてRECT構造体のポインタを示す

LPとPは同じ  
LPは16bit Windowsを32bit Windowsに移行するために歴史的に用いられた


型名の後ろに`_PTR`とするものは、常にプラットフォームに依存したポインタサイズになる  
32bit Windowsでは常に32bitサイズ、64bit Windowsでは常に64bitサイズとなる


### 文字列型

UTF-16によるユニコード文字列(ワイド文字列)をサポートしている  
なので、文字列は16bit(2byte)で表される

Visual C++では、`wchar_t`型を用意している(WinNT.h)  
ワイド文字を記述するとき、文字列の前にLをつける(例：L"string ...")

| 型名 | 定義 |  
|------|------|  
| CHAR | char |  
| PSTR/LPSTR | char* |  
| PCSTR/LPCSTR | const char* |  
| WCHAR | wchar_t |  
| PWSTR/LPWSTR | wchar_t* |  
| PCWSTR/LPCWSTR | const wchar_t* |  

ANSIからUNICODEに移行するために両方をサポートしているが、内部的にUNICODEに変換している
文字列を使う関数は、ANSI用とUNICODE用にそれぞれ用意されているが、`UNICODE`により同じ関数名で使用できる
通常UNICODEを使用するべき、ANSIをしよする場合はローカライゼーションをアプリケーションで行う

	#ifdef  UNICODE
	#define SetWindowText SetWindowTextW
	#else
	#define SetWindowText SetWindowTextA
	#endif

UNICODEとANSIをの互換として次のマクロが用意されている  
WindowsNT以前との互換が必要な場合に使用する

| マクロ | UNICODE | ANSI |  
|--------|---------|------|  
| TCHAR | wchar_t | char |  
| TEXT("x") | L"x" | "x" |  


## Window

### Windowとは

Windowは単にアプリケーションを管理するWindowのみではなく、
ボタンなどのUI ControlもWindowとして考える

WindowsでのWindowは次の概念

 - 画面の特定の部分を占める
 - 見えていても見えていなくてもいい
 - それ自体が描画可能
 - ユーザまたはOSからのイベントに応答する


WindowにはアプリケーションウィンドウとUI Controlがある

 - アプリケーションウィンドウ(メインウィンドウ)

	 - タイトルバーに最小化、最大化がある  
	 - フレーム部分をOSが管理するnon-client areaという
	 - フレーム以外の部分はプログラムが管理するclient areaという

 - UI Control

	 - UIコントロールのみでは存在せず、Windowに配置される
	 - アプリケーションウィンドウと一緒に動く
	 - アプリケーションウィンドウと通信ができる


親ウィンドウと子ウィンドウ

 - UI ControlのControl Windowは、子ウィンドウとも呼ばれる
 - 逆にアプリケーションウィンドウを親ウィンドウと呼ぶ
 - 親ウィンドウは子ウィンドウの座標系を持つ


アプリケーションウィンドウとモーダルダイアログ

 - アプリケーションウィンドウがオーナーウィンドウ、ダイアログがオーナーウィンドウの所有ウィンドウとなる
 - オーナーウィンドウの所有ウィンドウは常にオーナーウィンドウの前面に表示される
 - オーナーウィンドウの所有ウィンドウはオーナーウィンドウと同時に表示、非表示、破棄される


### Window Handle

 - Windowはobjectでクラスではないので参照できない
 - なのでhandleで参照する
 - handleは見えない
 - handleはOSがオブジェクトを識別する番号
 - handleはHWND(エイチウィンド)型
 - handleはCreateWindow、CreateWindowExでWindowを作成するときに受け取る
 - Windowを操作する関数の引数にHWND値を渡す
 - handleはポインタではない、hwndがhandleを含む変数の場合、handleを関節参照するとエラーになる

### スクリーンと座標系

 - 座標はデバイスに依存しないピクセルで測定される
 - 座標には、Screen座標、フレームを含むWindow座標、Windowのクライアント領域の座標がある
 - 原点はいずれも左上となる


## アプリケーションエントリポイント

 - Windowsのプログラムのエントリポイントは、WinMain/wWinMain

	`int WINAPI wWinMain(HINSTANCE hInstance, HINSTANCE hPrevInstance, PWSTR pCmdLine, int nCmdShow)`

	 - hInstance : OSが実行可能ファイルをロードされたときに実行可能ファイルを識別するために使用する値
	 - hPrevInstance : 16bit Windowsで使用されていた値で現在では使われていない。常に0を設定
	 - pCmdLine : コマンドライン引数をUnicode文字列として受け取る
	 - nCmdShow : アプリケーションウィンドウの初期表示状態をどうするかを渡す(通常表示、最小化、最大化)

 - 戻り値はint型のステータスコードを返す
	 - OSでは使用されない
	 - プログラムの結果を他のプログラムに渡すための値

 - WINAPIは呼び出し規約
	 - 関数が呼び出し元からパラメータを受け取る方法を定義する

 - WinMain関数とwWinMain関数の違いは、コマンドライン引数で渡す文字列がANSIかUnicodeかが違う
 - WinMain関数とwWinMain関数は、コンパイル時の文字コードに依存せず両方使うことができる
 - コマンドライン引数は、GetCommandLine関数で単一の文字列として受け取ることができる
 - argvスタイルの配列として受け取る場合は、GetCommandLine関数の結果をCommandLineToArgvW関数に渡す。

 - 通常、Cプログラムはmain関数がエントリポイントとなるが、CRT(Microsoft C Runtime Library)によって
	そのなかでwWinMain、WinMainを呼び出す実装を提供される




