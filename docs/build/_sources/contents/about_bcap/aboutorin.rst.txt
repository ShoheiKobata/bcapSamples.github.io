===================================
ORiNについて
===================================

ORiN : Open Resource interface for the Network



ORiNとは
===============================

ORiN協議会により制定された工場ITシステムのための標準ミドルウェア仕様の総称.
このORiNを利用することにより,メーカや機種に依存しないアプリケーションを開発することができるようになります.

詳細はORiN協議会のページを参照ください。
https://www.orin.jp/



特徴
===============================

様々なデバイスに接続のお作法を知らなくても接続、制御、情報の取得が可能になります。
同じ系統のデバイス(バージョン違い,後継機など)への接続の場合、プロバイダ名の変更のみで接続先の対応が可能になります。

例: RC8への接続の場合

.. code-block:: python

    import win32com.client
    caoEng = win32com.client.Dispatch("CAO.CaoEngine")
    caoCtrl = caoEng.Workspaces(0).AddController("", "CaoProv.DENSO.RC8", "", "Server=192.168.0.1")
    VarI = caoCtrl.AddVariable("I1", "")
    print("I[1]の現在の値は" + str(VarI.Value))


例: RC9への接続の場合

.. code-block:: python
    
    import win32com.client
    caoEng = win32com.client.Dispatch("CAO.CaoEngine")
    caoCtrl = caoEng.Workspaces(0).AddController("", "CaoProv.DENSO.RC9", "", "Server=192.168.0.1")
    VarI = caoCtrl.AddVariable("I1", "")
    print("I[1]の現在の値は" + str(VarI.Value))


ORiNを使用する場合,クライアントアプリケーションはデバイスを意識せず,ORiNのプロバイダに関する
プログラミングのお作法で通信が可能になります。

https://www.orin.jp/images/merit/reduction_fig.png

これを可能にしているのはCAOのオブジェクト構造です。
同じ系統の機器同士では同じオブジェクト内に似たような関数をひとまとめにしています。



.. uml::

    @startuml
    title CAOAのオブジェクト構造
    package CAO_Engine {
        class CAOEngine {
        {static} Collection CaoWorkSpaces
        }
        
        class CAOworkspace {
        {static} Collection CaoControllers
        }

        class CaoController {
        {static} Collection CaoVariables
        {static} Collection CaoFiles
        {static} Collection CaoTasks
        {static} Collection CaoRobots
        {static} Collection CaoExtensions
        --
        method AddVariable
        method AddFile
        method AddTask
        method AddRobot
        method AddExtension
        --
        }

        class CaoVariable{
        {method} getvalue
        {method} putvalue
        {method} getid
        {method} putid
        }
        class CaoFile{
        {method} getvalue
        {method} putvalue
        {method} getname
        {method} putname
        }
        class CaoTask{
        {method} start
        {method} stop
        {method} getname
        {method} putname
        }
        class CaoRobot{
        {method} move
        {method} execute
        {method} getname
        {method} putname
        }
        class CaoExtension{
        {method} execute
        }
    }

    package CAP_Provider{
        class CAPProvider{
        }
        class CaoProvController{
        }
        class CaoProvVariavle{
        }
        class CaoProvFile{
        }
        class CaoProvTask{
        }
        class CaoProvRobot{
        }
        class CaoProvExtension{
        }
    }

    CAOEngine <-- CAOworkspace
    CAOworkspace <-- CaoController
    CaoController <-- CaoVariable
    CaoController <-- CaoFile
    CaoController <-- CaoTask
    CaoController <-- CaoRobot
    CaoController <-- CaoExtension
    CaoVariable -[hidden]- CaoFile
    CaoFile -[hidden]- CaoTask
    CaoTask -[hidden]- CaoRobot
    CaoRobot -[hidden]- CaoExtension

    CAO_Engine -right- CAP_Provider

    CAPProvider <-- CaoProvController
    CaoProvController <-- CaoProvVariavle
    CaoProvController <-- CaoProvFile
    CaoProvController <-- CaoProvTask
    CaoProvController <-- CaoProvRobot
    CaoProvController <-- CaoProvExtension

    CaoProvController -[hidden]- CaoProvVariavle
    CaoProvVariavle -[hidden]- CaoProvFile
    CaoProvFile -[hidden]- CaoProvTask
    CaoProvTask -[hidden]- CaoProvRobot
    CaoProvRobot -[hidden]- CaoProvExtension

    CaoController --> CaoProvController
    CaoVariable --> CaoProvVariavle
    CaoFile --> CaoProvFile
    CaoTask --> CaoProvTask
    CaoRobot --> CaoProvRobot
    CaoExtension --> CaoProvExtension

    @enduml