サンプルプログラムの説明
===============================

`SimpleSamples <https://github.com/ShoheiKobata/orin_bcap_python_samples/tree/master/SimpleSamples>`_ の
サンプルプログラム内容を解説します. 



接続処理
------------------

ロボットコントローラへ接続するためのプログラムの実装手順です. 

.. code-block:: python

    import pybcapclient.bcapclient as bcapclient

    host = "192.168.0.1"
    m_bcapclient = bcapclient.BCAPClient(host=host, port=5007, timeout=2000)
    m_bcapclient.service_start("")
    hCtrl = m_bcapclient.controller_connect(name="", provider="CaoProv.DENSO.VRC", machine="localhost", option="")

bcapclientクラスを定義します.  :code:`m_bcapclient = bcapclient.BCAPClient(host=host, port=5007, timeout=2000)` 接続先のロボットのIPアドレスと, ポート番号, タイムアウト時間を指定します. 
IPアドレスは接続先のロボットコントローラ, ポートは :code:`port=5007`  で固定です.  タイムアウト時間は任意の整数を設定してください. 単位は[msec]で推奨は2000[msec]以上です. 
bcapclient.pyを見るとわかりますが,  BCAPClient クラス宣言時にsocket通信を開始します. ここまでは標準のpythonのコマンドを使用しています. もし, この段階でエラーが発生した場合はb-cap or ORiN独自の
エラーではなく, PCの通信に関するエラーが発生します. 

:code:`m_bcapclient.service_start("")` で接続したロボットコントローラのbCAPListenerを起動します. これ以降, ロボットコントローラはbcapコマンドを受け取ることが可能になります. 

:code:`hCtrl = m_bcapclient.controller_connect(name="", provider="CaoProv.DENSO.VRC", machine="localhost", option="")` でロボットコントローラ内のプロバイダへの接続を開始します. 
VRCプロバイダ = RC8プロバイダ です.  RC9やCOBOTTAPROへ接続する場合は :code:`provider="CaoProv.DENSO.VRC9"` としてください. 

:code:`hCtrl`  戻り値は Controller Object のハンドル番号が渡されます.  Controller Object のメソッドを使用する場合に使用します. 

:code:`machine="localhost"` は固定です. bcaplistenerを開始したロボットコントローラ内のプロバイダを使用します. 

:code:`name=""` と :code:`option=""` については以下のように関連性があります. 

:code:`name` は接続単位で重複しない任意の文字列を指定します. 異なるアプリケーションや別PC から同一の名前を指定した場合はエラー(0x80000205)になります．
空文字列(““)を指定した場合, Cao エンジンが自動的にユニークなコントローラ名を割り当てます．

:code:`option` オプション文字列に :code:`"@IfNotMember"` オプションを指定した場合はコントローラ名に対応する既存オブジェクトを返します.
同名のオブジェクトがない場合は,指定されたコントローラ名のオブジェクトを新規に作成します.
コントローラ名に空白文字列("")を指定した場合は@IfNotMember の指定は無視されます.

つまり,想定外の切断, 終了処理が不十分な場合, 再接続する際に :code:`name="", option=""` としてしまうと, 新規に接続を開始する形になります. 後述で出てくる軸の制御権(TakeArm)などの処理をした場合,
新たな接続をすると, 軸の制御権を取得, 解放できない場合があります. 
:code:`name="any_string", option="@IfNotMember"` としておくことで, 予期せぬ切断, 終了処理不十分な場合でも再度, アプリケーションを起動し接続すれば, 同じオブジェクトとして接続が開始されます. 

.. tip:: 
    controller_connect を :code:`@IfNotMember` オプション付きで実施した場合, 以降の robot object , task object , variable object , file object 
    を追加するコマンドにもすべて :code:`@IfNotMember`  オプションを付与すると良い


終了処理
------------------------

通信の終了部分について説明します. 

.. code-block:: python

    m_bcapclient.controller_disconnect(hCtrl)
    m_bcapclient.service_stop()


プロバイダでの接続を終了します.  :code:`m_bcapclient.controller_disconnect(hCtrl)` で終了します. 引数にはController Object を取得した際のハンドルを指定します. 

:code:`m_bcapclient.service_stop()` で接続したロボットコントローラの bCAPListenerを終了します. 

.. tip:: 
    | 終了処理の順番は接続順の 反対 から実施します. 
    | 接続を Controller -> Robot -> robot_variable とした場合, 
    | 切断は robot_variable -> Robot -> Controller の順で実施します. 



変数の読み書き
------------------------

コントローラオブジェクトの変数の読み書きについて
変数の種類についてはマニュアルを参照ください.  
https://www.fa-manuals.denso-wave.com/subfolder/jp/usermanuals/img/001511/RC8_ProvGuide_ja.pdf の
コントローラクラス ユーザ変数一覧, コントローラクラス システム変数一覧 を参照ください. 

.. code-block:: python

    h_i1 = 0
    h_i1 = m_bcapclient.controller_getvariable(hCtrl, "I1", "")
    # read value of I[1]
    retI = m_bcapclient.variable_getvalue(h_i1)
    print("Read Variable I[1] = %d" % retI)
    newval = retI + 1
    # write value of I[1]
    m_bcapclient.variable_putvalue(h_i1, newval)
    

ロボットコントローラ内のグローバル変数にアクセスします. 
:code:`m_bcapclient.controller_getvariable(hCtrl, "I1", "")` でグローバル変数 I[1] へのハンドルを取得します. 

第二引数には 変数名  ``"<変数名>[<番号>]"`` を指定します. 

    - <変数名> : I, F, V, P, J, D, T, S または IO, IOB, IOW, IOD, IOF を指定します. 
    - <番号> : 各変数の番号を指定します.  ``*`` を指定するとワイルドカードでの指定となります. 初期値 0 として扱われ, コマンドで番号を変更することができます. 

IO はBit, IOB はByte, IOWはWord, IOD はDouble Word (Long), IOFはFloat(Single)としてI/O の値を処理します．

| :code:`retI = m_bcapclient.variable_getvalue(h_i1)` で I[1]の値を取得します. 
| :code:`m_bcapclient.variable_putvalue(h_i1, newval)` で I[1]に値を書き込みます. 


`01_00_ReadWrite.py <https://github.com/ShoheiKobata/orin_bcap_python_samples/blob/master/SimpleSamples/01_00_ReadWrite.py>`_ では同様に
F,D,P,S の値の読み書きを実施しています. 

.. tip:: ロボット特有の変数について 

    :P型: P(X,Y,Z,Rx,Ry,Rz,Fig) を表します.  pythonでは要素7の配列として扱われます. 
    :T型: T(X, Y, Z, Ox, Oy, Oz, Ax, Ay, Az, Fig) を表します.  pythonでは要素10の配列として扱われます. 
    :J型: J(J1, J2, J3, J4, J5, J6, J7, J8)  を表します.  pythonでは要素8の配列として扱われます. 
    :V型: V(X, Y, Z) を表します.  pythonでは要素3の配列として扱われます. 
    

変数のインデックス番号を変更しながら値を取得する
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

`01_01_ReadWrite.py <https://github.com/ShoheiKobata/orin_bcap_python_samples/blob/master/SimpleSamples/01_01_ReadWrite.py>`_ では
ロボットコントローラの変数の長さを取得し, すべての変数の値を取得しています.  アクセスする変数の番号を変更しながら値を取得することで, 
取得するハンドルを各変数に一つのもにしています. 

.. code-block:: python

    IHandl = 0
    IHandl = m_bcapclient.controller_getvariable(hCtrl, "I*", "")
    VAR_I_Handl = 0
    VAR_I_Handl = m_bcapclient.controller_getvariable(hCtrl, "@VAR_I_LEN", "")
    # Get System Variables "@VAR_I_LEN" Value
    ret_I_Len = m_bcapclient.variable_getvalue(VAR_I_Handl)
    for IDnum in range(ret_I_Len):
        # Change ID Number I[*]
        m_bcapclient.variable_putid(IHandl, IDnum)
        # read value of I[*]
        retI = m_bcapclient.variable_getvalue(IHandl)
        print("Read Variable I[%d] = %d" % (IDnum, retI))
    
    m_bcapclient.variable_release(VAR_I_Handl)
    m_bcapclient.variable_release(IHandl)

グローバルI 型変数のサイズを :code:`m_bcapclient.controller_getvariable(hCtrl, "@VAR_I_LEN", "")` でアクセスし,  :code:`ret_I_Len = m_bcapclient.variable_getvalue(VAR_I_Handl)` で
取得しています. 

:code:`m_bcapclient.variable_putid(IHandl, IDnum)` でアクセスする変数の番号を変更しながら,  :code:`m_bcapclient.variable_getvalue(IHandl)` で値を取得しています. 

IDの変更は変数オブジェクトを定義する際に :code:`m_bcapclient.controller_getvariable(hCtrl, "I*", "")` のように **ワイルドカードで指定する必要** があります. 


タスクの制御
------------------------


タスク(ロボットコントローラ内にあるロボット言語(PacScript)プログラム)の制御方法について

ロボット言語がわからない場合, こちらのサンプルプログラムをご使用ください.  ただし, 対象は6軸で, 十分にロボットが動作できる範囲を確保して実施ください. 



.. caution:: 安全に注意して実施してください. 
    
    この章の内容はロボットコントローラ内のPro1.pcs , Pro2.pcsの内容次第ではロボットが動作します. 
    安全には十分注意して実行ください. また, ロボットの速度を十分に落し, 非常停止をすぐに押下できる状態で実施することを推奨します. 


.. code-block:: python

    HTask = 0
    HTask = m_bcapclient.controller_gettask(hCtrl, "Pro1", "")
    # Start pro1.pcs in RC8
    # mode  1:One cycle execution, 2:Continuous execution, 3:Step forward
    mode = 1
    hr = m_bcapclient.task_start(HTask, mode, "")
    m_bcapclient.task_release(HTask)

`03_00_Task.py <https://github.com/ShoheiKobata/orin_bcap_python_samples/blob/master/SimpleSamples/03_00_Task.py>`_ はプログラムを起動するサンプルです. 


コントローラオブジェクトのメソッド :code:`m_bcapclient.controller_gettask(hCtrl, "Pro1", "")` で
コントローラ内にある ``Pro1.pcs`` のタスクオブジェクトを取得します. 戻り値はハンドル番号です. 

:code:`m_bcapclient.task_start(HTask, mode, "")` で Pro1.pcsをスタートします. modeはそれぞれ

:mode=1: サイクル実行
:mode=2: 連続実行
:mode=3: ステップ送り

となります. 


`03_01_Task.py <https://github.com/ShoheiKobata/orin_bcap_python_samples/blob/master/SimpleSamples/03_01_Task.py>`_ はプログラムを起動し, 
起動したプログラムの状態を監視しながら, ボタン入力が入ると起動したプログラムを停止させるサンプルです. 

.. code-block:: python

    mode = 2
    hr = m_bcapclient.task_start(HTask, mode, "")
    while loopflg:
        # Status (VT_I4), 0:TASK_NON_EXISTENT, Task is not exist. , 1:TASK_SUSPEND, Hold-stopped , 2:TASK_READY, Ready , 3:TASK_RUN, Running , 4:TASK_STEPSTOP, Step-stopped
        TaskStatus = m_bcapclient.task_execute(HTask, "GetStatus")
        print("TaskStatus : ", TaskStatus)
        if(TaskStatus != 3):
            loopflg = False
        if getkey(ESC):  # If push the ESC key,task stop
            print("push the ESC key")
            # mode:: 0: Default stop, 1: Instant stop, 2: Step stop, 3: Cycle stop, 4: Initialized stop
            mode = 1
            hr = m_bcapclient.task_stop(HTask, mode, "")
            print("task stop")

まず, 初めにプログラムを 連続実行 で起動します. ロボットコンロとローラ内のプログラムはプログラムが終了すると, 自動敵に先頭に戻り再度プログラムが先頭から開始されます. 

:code:`m_bcapclient.task_execute(HTask, "GetStatus")` で起動したプログラムの状態を取得します. 戻り値は


:0: TASK_NON_EXISTENT, タスクが存在しない
:1: TASK_SUSPEND, 一時停止中
:2: TASK_READY, レディ
:3: TASK_RUN, 実行中
:4: TASK_STEPSTOP, ステップ停止

となります.  ループ中タスクの状態が 3:実行中 の場合は監視を続けます. 

ESCキーが入力された時,  :code:`m_bcapclient.task_stop(HTask, mode, "")` を実施し, タスクを停止します. modeは

:0: デフォルト停止
:1: 瞬時停止
:2: ステップ停止
:3: サイクル停止
:4: 初期化停止

となります.  停止の種類の詳細についてはロボットマニュアルを参照ください. ( `ID:1709 <https://www.fa-manuals.denso-wave.com/jp/usermanuals/001709/>`_ )


動作コマンド発行
------------------------

直接ロボットへ動作コマンドを発行します. 

.. caution:: 安全に注意して実施してください. 
    
    この章の内容は実際にロボットを動作させます. 
    安全には十分注意して実行ください. また, ロボットの速度を十分に落し, 非常停止をすぐに押下できる状態で実施することを推奨します. 


.. tip:: TakeArmを解放設定
    
    この章の実施前に「 **非常停止時PC からのTakearm を解放** 」を **1** に設定しておくと予期せぬタイミングでプログラムが終了した際に復旧が簡単になります。

    | 操作経路、メンテナ権限でログイン後
    | [F6:設定] - [F2:システム情報] - [F6:VRC設定]

    .. image:: /img/setting_vrc_no31.png



`04_00_Move.py <https://github.com/ShoheiKobata/orin_bcap_python_samples/blob/master/SimpleSamples/04_00_Move.py>`_ で使用されているコマンドを解説します. 

.. code-block:: python

    HRobot = m_bcapclient.controller_getrobot(hCtrl, "Arm", "")
    m_bcapclient.robot_execute(HRobot, 'TakeArm', [0, 0])
    m_bcapclient.robot_execute(HRobot, 'Motor', [1, 0])
    Speed = 10
    Accel = 1
    Decel = 1
    Param = [Speed, Accel, Decel]
    m_bcapclient.robot_execute(HRobot, 'ExtSpeed', Param)

動作コマンド発行前に必要な処理です. 
:code:`m_bcapclient.controller_getrobot(hCtrl, "Arm", "")` ロボットオブジェクトを指定します. 第二引数は任意の文字列で, 特に意味はありません. 

:code:`m_bcapclient.robot_execute(HRobot, 'TakeArm', [0, 0])` 軸の制御権を取得します. 
このコマンドを発行しないと, ロボットの制御をPCから実施できず, エラーになります. 



:code:`m_bcapclient.robot_execute(HRobot, 'Motor', [1, 0])` ロボットのサーボをオンします. サンプルではサーボONの完了待ちをしています. 

書式 :code:`Motor ( <State> [,<NoWait>] )` 

:State: [in]モータ状態(VT_I4) 0:モータOFF , 1:モータON
:NoWait: [in]完了待ち(VT_I4) 0:完了待ちする (デフォルト値) , 1:完了待ちしない
:戻り値:  なし

:code:`m_bcapclient.robot_execute(HRobot, 'ExtSpeed', Param)`  ロボットの外部速度を変更しています. 
十分に小さい速度から始めてください. 安全に注意して速度を上昇させてください. 

.. code-block:: python

    # Interpolation
    Comp = 1
    # PoseData (string)
    Pose = "@P P1"
    m_bcapclient.robot_move(HRobot, Comp, Pose, "SPEED=F2,NEXT")
    Pose = [2, "P", "@0"]
    m_bcapclient.robot_move(HRobot, Comp, Pose, "")

    position_Value = [210.0,0.0,260.0,180.0,0.0,180.0,261]
    Pose = [position_Value,"P","@E"]
    m_bcapclient.robot_move(HRobot,Comp,Pose,"")

Moveコマンドの書式

書式 :code:`Move <lComp:LONG >, <vntPose:POSEDATA> [,<vntPose:POSEDATA>…] [, < bstrOpt:BSTR>]`

:lComp: 補間指定 1:MOVE P , 2:MOVE L , 3:MOVE C, 4:MOVE S
:vntPose: ポーズ列 
:bstrOpt: 動作オプション ``[SPEED=n][,ACCEL=n][,DECEL=n][,TIME=n][,NEXT]`` のように指定が可能です. 

ポーズ列の指定方法

1. すべて文字列で指定する  ``[<パス>] [<変数型>]<インデックス> [<付加軸>]`` のように指定します. 

    例: ``"@P P[1]"`` , ``"@E J(0, 0, 90, 0, 90, 0)"`` .

2. 配列で指定する.  ``(<値>[,<変数型>[,<パス>[, <付加軸>]]])`` のように指定します. 

    例: ``[1, "P", "@P"]`` , ``[[0, 0, 90, 0, 90, 0], "J", "@E"]`` .


b-cap slave move
------------------------

| `b-cap slave move <https://www.denso-wave.com/ja/robot/product/function/b-CAP.html>`_ は8msec周期でロボットに指令値を送信し続けることで
| より細かくロボットの軌道を制御することが可能になります。

ただし、8msec周期のロボットの位置を指定しますので、間接的にロボットの速度、加速度を考慮した指令値の送信が必要になります。
(8msec後の1軸を30[deg]動かすような指令値を送信しても、速度エラー等で動けません。)


bcapslaveの制御手順概要


.. uml::

    @startuml
    title b-CAPSlaveMove operation flow

    start

    :Connect;

    note right
    BCAPClient
    BCAPClient.service_start
    BCAPClient.controller_connect
    end note

    :Controll;

    note right
    Motor ON
    TakeArm
    Move , Move ...
    end note
    
    :slave mode setting,start;

    note right
    slvSendFormat
    slvRecvFormat
    slvChangeMode
    end note
    

    while (finish == False)
        :指令値送信;
    endwhile

    note right
    slvMove
    end note

    :SlaveMode finish;

    note right
    slvChangeMode
    end note

    :Disconnect;

    note right
    BCAPClient.controller_disconnect
    BCAPClient.service_stop
    end note

    stop

    

    @enduml


:code:`m_bcapclient.robot_execute(handle_robot, "slvSendFormat", 0x0000)`

slvSendFormat slvMove コマンドのパラメータフォーマットを変更します．

.. list-table:: slvSendFormat 引数(VT_I4)
    :stub-columns: 1

    * - 値
      - 拡張
    * - 0x0000
      - なし
    * - 0x0020
      - HandIO状態
    * - 0x0100
      - MiniIO状態
    * - 0x0120
      - MiniIO+HandIO状態
    * - 0x0200
      - 汎用IO状態
    * - 0x0220
      - 汎用IO+HandIO状態


:code:`m_bcapclient.robot_execute(handle_robot, "slvRecvFormat", 0x0000)`

| slvRecvFormat slvMove コマンドの戻り値フォーマットを変更します.
| 引数は配列で [VT_I4, VT_I4] で指定します。省略も可能です。

.. list-table:: slvRecvFormat 第一引数(VT_I4)
    :stub-columns: 1

    * - 値
      - フォーマット
    * - 0x0001
      - P型
    * - 0x0002
      - J型
    * - 0x0003
      - T型
    * - 0x0004
      - P型+J型
    * - 0x0005
      - T型+J型
    * - 0x0010
      - タイムスタンプ
    * - 0x0020
      - HandIO状態
    * - 0x0040
      - 電流値取得
    * - 0x0100
      - MiniIO状態
    * - 0x0200
      - 汎用IO状態


slvRecvFormat 第二引数(VT_I4)
0: タイムスタンプをms で返す(default)
1: タイムスタンプをus で返す
※省略時は0 になります.

| `05_00_slvMove.py <https://github.com/ShoheiKobata/orin_bcap_python_samples/blob/master/SimpleSamples/05_00_slvMove.py>`_ ではロボット動作の情報のみ送信
| `05_01_slvMove.py <https://github.com/ShoheiKobata/orin_bcap_python_samples/blob/master/SimpleSamples/05_01_slvMove.py>`_ ではIO情報を同時に送信する方法を記載しています。


:code:`m_bcapclient.robot_execute(handle_robot, "slvChangeMode", 0x202)`

Slave Mode の設定を変更します.

.. list-table:: slvChangeMode 引数(VT_I4)
    :stub-columns: 1

    * - 値
      - 位置形式
      - 動作
    * - 0x0000
      - ---
      - 解除
    * - 0x001
      - P型
      - モード0設定
    * - 0x002
      - J型
      - モード0設定
    * - 0x003
      - T型
      - モード0設定
    * - 0x101
      - P型
      - モード1設定
    * - 0x102
      - J型
      - モード1設定
    * - 0x103
      - T型
      - モード1設定
    * - 0x201
      - P型
      - モード2設定
    * - 0x202
      - J型
      - モード2設定
    * - 0x203
      - T型
      - モード2設定

モードについて
slave mode のモード設定にはそれぞれ以下のような特徴があります。

.. list-table:: SlaveMode 概要
    :stub-columns: 1

    * - モード
      - パラメータ
      - バッファ数
      - バッファの空き待ち
      - 備考
    * - | モード 0
        | 同期・待機なし
        | (RC7 該当なし) 
      - 0x0**
      - | 3 
        | （バッファリングデータは必ず使用）
      - 無
      - | クライアントから送信されたメッセージをバッファにキューイングします．
        | バッファの状態に応じたリターンコードを即時に返します．
    * - | モード 1
        | 非同期
        | (RC7 の非同期に対応) 
      - 0x1**
      - | 1 
        | （バッファリング時,データ上書き）
      - 無
      - | クライアントから送信されたメッセージで
        | バッファを上書きし続けます．
    * - | モード 2
        | 同期・待機あり
        | (RC7 同期に相当) 
      - 0x2**
      - | 3 
        | （バッファリングデータは必ず使用）
        | （RC7 はバッファ数は１）
      - 有
      - | クライアントから送信されたメッセージをバッファにキューイングします．
        | バッファに空きができるまでリターンコードを返しません．


バッファが空になったとき(バッファアンダーフロー時)の挙動の違い

.. list-table:: バッファアンダーフロー時 挙動
    :stub-columns: 1

    * - スレーブモード
      - ロボット動作状態
      - サーバ挙動
      - 備考
    * - モード0
      - ロボット動作中
      - | エラーを発生する
        | 指令値生成遅延(0x84201482)
      - スレーブモード解除
    * - モード0
      - ロボット停止
      - エラーを発生しない
      - スレーブモード維持
    * - モード1
      - ロボット動作中
      - エラーを発生しない
      - | スレーブモード維持
        | 現在位置停留命令を発行
    * - モード1
      - ロボット停止
      - エラーを発生しない
      - | スレーブモード維持
        | 現在位置停留命令を発行
    * - モード2
      - ロボット動作中
      - | エラーを発生する
        | 指令値生成遅延(0x84201482)
      - スレーブモード解除
    * - モード2
      - ロボット停止
      - | エラーを発生しない
      - スレーブモード維持


.. tip:: slave mode の推奨

   - mode 2 : 通信周期が安定しない環境の場合、mode2を推奨します。バッファ3つをフルに活用する
   - mode 1 : mode2を使用時に指令値生成遅延が発生してしまう場合、mode1を使用する。

   エラー発生を回避できるが、指令値が空になっている場合、その場に停止使用と動作するので、slavemoveの
   動作速度が安定しない。（かくかくした動作になる。）


エラー処理
------------------------

b-cap , ORiNによって発生したエラー処理について.

`06_error_handling.py <https://github.com/ShoheiKobata/orin_bcap_python_samples/blob/master/SimpleSamples/06_error_handling.py>`_ を参考にエラー処理について解説します。

サンプルライブラリではb-capで送信したコマンドでエラーが発生した場合、 ``pybcapclient.orinexception.ORiNException``  クラスでエラー情報を返します。よって、try,exceptで
``ORiNException`` クラスを指定し、エラー情報をキャッチします。(ロボット以外のエラー処理については省略します。)

返却されるエラーコードは10進数で表現されています。また、必ず負の値で返却されます。この値を16進数に直し、 ``0x8□□□□□□`` の形にすることでロボットコントローラで表現されているエラーコード
と同じ値を見ることができます。 ロボットマニュアル エラーリスト `リンク <https://www.fa-manuals.denso-wave.com/subfolder/jp/usermanuals/img/001075/ErrorList.pdf>`_ 

取得したエラーコードの詳細を知りたい場合は、 ``controller_execute``  のコマンド ``"GetErrorDescription"`` でエラーの詳細内容が取得できます。このコマンドはロボットコントローラのエラー状態に関係なく
発行できます。つまり、エラーが発生している状態のままでも、コマンドを発行することが可能です。
``error_description = m_bcapclient.controller_execute(hCtrl, "GetErrorDescription", errorcode_int)`` 引数には取得したエラーコードを設定します。10進数の値で問題ありません。また、戻り値は
エラー詳細が文字列で取得できます。

.. code-block:: python

    except ORiNException as e:
        print('catch ORiN Exception in Robot Controller')
        errorcode_int = int(str(e))
        if errorcode_int < 0:
            errorcode_hex = format(errorcode_int & 0xffffffff, 'x')
        else:
            errorcode_hex = hex(errorcode_int)
        # End if
        print("Error Code : 0x" + str(errorcode_hex))
        error_description = m_bcapclient.controller_execute(hCtrl, "GetErrorDescription", errorcode_int)
        print("Error Description : " + error_description)


エラーをクリアする場合、 ``controller_execute`` の ``"ClearError"`` コマンドを実行するとエラーを解除できます。
