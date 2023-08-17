pythonライブラリで実装する.
===============================

| b-cap libarry for python : https://github.com/DENSORobot/orin_bcap/tree/master/Python/bCAPClient 
| sample programs : https://github.com/ShoheiKobata/orin_bcap_python_samples 

サンプルプログラムをローカルに準備してください. 

.. code-block:: bash
    
    cd workspace
    git clone https://github.com/ShoheiKobata/orin_bcap_python_samples.git
    cd orin_bcap_python_samples


処理の流れ
===============================

実際にアプリケーションを作成する際の処理の流れになります. 


.. uml::

    @startuml
    title アプリケーション処理フロー

    start

    :接続処理;

    note right
    BCAPClient
    BCAPClient.service_start
    BCAPClient.controller_connect
    end note

    while (finish == False)
        :操作, 処理;
    endwhile

    note right
    変数の読み書き
    ロボット動作
    タスク操作
    など
    end note

    :切断処理;

    note right
    BCAPClient.controller_disconnect
    BCAPClient.service_stop
    end note

    stop

    

    @enduml

