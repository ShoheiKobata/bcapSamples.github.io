===================================
b-capについて
===================================

b-capとは
===============================

b-CAPは,CAPの概念を踏襲しつつ,通信速度の向上を狙ったプロトコルである,
これにより,b-CAPはCAPファミリと同様な以下の特徴を持っている.
- CAOプロバイダのオブジェクトモデルと同様なサービス構造
- オブジェクトIDで対象オブジェクトを指定しての関数呼び出し
- サーバからのイベントをポーリングで実現




CAPとは
-----------------------

CAP とは,Controller Access Protocol の略で,インターネット経由で CAO プロバイダにアクセスするための
「インターネット向け通信プロトコル」です.CAOエンジンを利用して,リモートのCAOプロバイダにアクセスするためには DCOM を利用していますが,
DCOM はセキュリティ上の問題があるために,インターネットを介したアクセスは実質上不可能です.そこで,この問題を解決するために ORiN2 では CAP を開発しました.

CAP は,SOAP(Simple Object Access Protocol)を使用して,インターネット経由でオブジェクト間のリモート
アクセスを実現するためのプロトコルです.ORiN2 SDK では,CAP プロバイダを提供することにより,CAP の仕様に従ったメッセージを作成,送信することができます.
これにより,HTTP で接続可能な環境であれば,容易にインターネットを介してリモートのプロバイダに接続することが可能となります

システム構成図
-----------------------

b-CAPプロトコルでの通信を使用してCAPリスナーが動作しているシステム(PC or RC8)へアクセスし、そのシステム内のプロバイダを使用する。
あるいはそのシステムの関数や変数を実行することができる。


.. uml::

  @startuml
  title 概念図
  package BcapClientApplication {
    node BcapClient
  }
  package Machine {
    node Controller {
      node BcapServer
    }
  }
  package CAOApplication {
    node CAOClient
    node CAOEngine
    node BcapProvider
    CAOClient -- CAOEngine
    CAOEngine -- BcapProvider
  }
  package RobotController {
    node BcapListener
    node CAO
    Node VRCProvider
    node RobotCtrl
    BcapListener -- CAO
    CAO -- VRCProvider
    VRCProvider -- RobotCtrl
  }

  
  BcapClient -d-> BcapServer:TCP/IP 
  BcapProvider -d-> BcapListener:TCP/IP 
  BcapClient -d-> BcapListener:TCP/IP 
  BcapProvider -d-> BcapServer:TCP/IP 


  
  @enduml


b-CAPライブラリについて
===============================

デンソーウェーブ作成の `b-capライブラリサンプル <https://github.com/DENSORobot/orin_bcap>`_ を活用することが可能です。

C , C# , Java , python のライブラリサンプルを提供しています。

