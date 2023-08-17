===================================
シミュレーションについて
===================================

b-cap通信を使用したソフトウェアの動作確認をするために,実機へ接続することができます. 
シミュレーションを使用して,PC上のロボットコントローラシミュレータに接続し,通信確認をすることが可能です. 
このページではシミュレーションへb-cap通信を使用したアプリケーションでの接続のためのシミュレーション環境構築について説明します. 


必要なソフトウェア
===============================

- (WINCAPSⅢ)
- ORiN2 SDK (デンソー製品版 以上のライセンス)
- VRC

※ 上記のソフトウェアは Windows のみ実行可能です.  つまり,ロボットのシミュレーションをする場合, windows PCが必須になります. 


必要なライセンス
===============================

必要なライセンスは2パターンから選択できます。

- WINCAPSⅢ + VRC Kernel
- VRC Professional

次に、各パターンでどのようなソフトウェア構成になるかを説明します。

1. WINCAPSⅢ + VRC Kernel

    WINCAPSⅢでロボットやロボットコントローラの状態を確認します. VRC Kernelでロボットやロボットコントローラの状況をエミュレーションします. 

    .. uml::

        @startuml
        node "PC" as PC  {
            frame "VRC kernel" as VRC {    
                component "b-cap Listener" as BcapListener
                component "Controller" as ctrl
                component "RobotMotion" as rob

                BcapListener -r- ctrl
                ctrl -do- rob

            }
            interface bcap 
            frame "Your App" as App{
                component "bcap client" as bcapclient
            }
            frame "WINCPAS III" as wincaps

            bcap -- BcapListener
            bcapclient -- bcap
            VRC -- wincaps

            note top of wincaps: View
            note bottom of VRC: Calcrate and emuration 
        }

        @enduml


2. VRC Professional

    VRC Professionalを使用することでVRC TP + VRC Kernel でロボットやロボットコントローラの状態を確認します. 
    VRC TP は PC上で仮想のTP(ティーチングペンダント)を表示するソフトウェアです. ロボットやロボットコントローラの状態を表示します. 
    VRC Kernelでロボットやロボットコントローラの状況をエミュレーションします. 

    .. uml::

        @startuml
        node "PC" as PC  {
            frame "VRC kernel" as VRC {    
                component "b-cap Listener" as BcapListener
                component "Controller" as ctrl
                component "RobotMotion" as rob

                BcapListener -r- ctrl
                ctrl -do- rob

            }
            interface bcap 
            frame "Your App" as App{
                component "bcap client" as bcapclient
            }
            frame "VRC TP" as VRCTP

            bcap -- BcapListener
            bcapclient -- bcap
            VRC -- VRCTP

            note top of VRCTP: View
            note bottom of VRC: Calcrate and emuration 
        }

        @enduml


3. windows 以外のOSのPCを使用する場合,

    ロボットシミュレーションソフトウェアはWindowsPCでのみ実行可能です. よって,Windows以外でb-capクライアントアプリケーションを構築,実行する場合,
    シミュレーションへ接続する場合,別のWindowsPCが必要になります. 


    .. uml::

        @startuml
        node "PC" as PC  {
            frame "Your App" as App{
                component "bcap client" as bcapclient
            }
        }
        note left of PC: not windows os pc
        interface bcap
        
        node "windows PC" as winPC  {
            frame "VRC kernel" as VRC {
                component "b-cap Listener" as BcapListener
                component "Controller" as ctrl
                component "RobotMotion" as rob
                BcapListener -r- ctrl
                ctrl -do- rob
            }
            frame "VRC TP" as VRCTP

            note top of VRCTP: View
            note bottom of VRC: Calcrate and emuration 
        }
        bcap -- BcapListener
        bcapclient -- bcap
        VRC -- VRCTP

        @enduml


シミュレーション方法の差分
===============================

シミュレーション方法によって操作可能な範囲が異なります. 自作アプリケーションを接続する際に
使用する可能性の高い項目を抜粋して,操作可能かどうかの差分を下の表にしています. 

.. list-table::

   * - 項目
     - wincaps+VRC Kernel
     - VRC Pro
   * - 手動操作
     - [ ]
     - [✓]
   * - プログラム起動
     - [✓]
     - [✓]
   * - モータON
     - [✓]
     - [✓]
   * - 非常停止
     - [ ]
     - [✓]
   * - モード切り替え
     - [ ]
     - [✓]
   * - ロボットプロジェクトデータ新規作成
     - [✓]
     - [ ]


準備
===============================

wincaps + VRC Kernel , VRC Professional で操作方法が異なります. 

共通の設定
------------------------------

VRCソフトウェアに同梱されているドキュメント, VRC_Users_Guide_ja.pdf ないの [3.4.2. ORiN2 インターフェイスを介した通信]
の内容を実施します. 

1. bCapService で **サービス** として登録する．
  #. Windows のコマンドプロンプトを管理者権限で起動する
  #. ``sc.exe create bCapService binpath= “<パス>¥bCapService”`` を入力してサービス登録する

#. bCapConfig でbCAP サービスの初期設定を行う．
  #. bCapConfig.exe を管理者権限で起動する
  #. [File]メニュー-[Import…]を選択し, rc8_bcap_cnf.xml を開く
  #. [File]メニュー-[Save]を実行する. ※bCAP サービスの初期設定は変更しない限り，再度実行する必要はありません

#. bCapConfig でbCAP サービスの開始する．
  #. bCapConfig.exe を管理者権限で起動する
  #. [Action]メニュー-[Service Start]を実行する.この時, bCapService がbCAP サーバーとして起動される

ロボットシミュレーションにb-capで接続をする場合,必ず,b-cap Serviceが実行していないといけません. 


wincaps + VRC Kernel
--------------------------------

wincaps + VRC Kernelでシミュレーション環境を動作させる方法

- bCapConfig でbCAP サービスの開始します
- WINCAPSⅢで任意のプロジェクトデータを開きます。
- WINCAPSⅢをシミュレーションモードへ移行します。 
- 作成したb-capクライアントアプリケーションを起動する. 接続,操作を開始します. 

.. note:: シミュレーションモードについて

    | 必ずb-capクライアントアプリケーションを接続する前に必ず、シミュレーションモードにしてください。
    | 順序が変わるとb-capクライアントアプリケーションから操作した情報がWINCPASⅢで表示されない場合があります。


VRC Professional
---------------------------

VRC Pro でシミュレーション環境を動作させる方法

ロボットプロジェクトデータ

VRCソフトウェアのインストーラの ``VRC_V****\SamplePrj`` 内にロボットのプロジェクトデータが格納されています. 
ロボットのプロジェクトデータがない場合,このプロジェクトデータを使用してください. 

- bCapConfig でbCAP サービスの開始します。
- VRC TP を起動します.  デフォルトパス ``C:\Program Files (x86)\DENSO ROBOTICS\VRC\TP\Bin\TP.exe`` 
- VRC TPが起動し,ロボットプロジェクトデータを入力する画面が表示される. 
- VRC TP画面でロボットプロジェクトデータ(例: ``VRC_V2200\SamplePrj\VS-050A3-A\VS-050A3-A.WPJ``)を選択し,起動する. 
- VRC TP画面が立ち上がる
- 作成したb-capクライアントアプリケーションを起動する. 接続,操作を開始します. 

