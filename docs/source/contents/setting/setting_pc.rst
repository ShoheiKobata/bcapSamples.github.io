===================================
Setting PC
===================================

PC で必要な設定の説明

RC8,RC8A,COBOTTAの場合
===============================

推奨環境は以下の通りです。



RC9,COBOTTA Proの場合
===============================
aaaaa


test

.. uml::


    A -> B: request
    return response


test2


.. uml::


    @startuml


    node ノード1 {
    node ノード2
    }

    rectangle 四角形1 {
    rectangle 四角形2
    }

    frame フレーム1 {
    frame フレーム2
    }

    component コンポーネント1 {
    component コンポーネント2
    }

    artifact アーティファクト1 {
    artifact アーティファクト2
    }

    cloud クラウド1 {
    cloud クラウド2
    }

    database データベース1 {
    database データベース2
    }

    folder フォルダ1 {
    folder フォルダ2
    }

    file ファイル1 {
    file ファイル2
    }

    package パッケージ1 {
    package パッケージ2
    }

    queue キュー1 {
    queue キュー2
    }

    ノード1 -[hidden]-> 四角形1
    四角形1 -[hidden]-> フレーム1
    コンポーネント1 -[hidden]-> アーティファクト1
    アーティファクト1 -[hidden]-> クラウド1
    データベース1 -[hidden]-> フォルダ1
    フォルダ1 -[hidden]-> ファイル1
    パッケージ1 -[hidden]-> キュー1

    @enduml


end 