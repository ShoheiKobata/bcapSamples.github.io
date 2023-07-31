===================================
Quick Start
===================================

GitHub上のリポジトリプログラムを使用し、ロボットコントローラへアクセスする手順を説明します。

------

使用環境
===============================

推奨環境は以下の通りです。

PC
-----------------------

OS : windows


言語
-----------------------

Python


ロボットコントローラ
-----------------------
RC8,RC8A,COBOTTA


ネットワーク設定
-----------------------
PCとロボットコントローラのIPアドレスです。PCとロボットコントローラは有線LANで接続してください。
PC ---(有線LAN)--- RC

- PC : 192.168.0.2
- RC8: 192.168.0.1 (default)

------

準備
===============================

bcapを使用したサンプルプログラムとPC環境準備、ロボットコントローラの準備の手順です。

PCとロボットコントローラをEthernetケーブルで接続してください。


ロボットコントローラの準備 IPアドレスを設定する
------------------------------------------------

ロボットコントローラのティーチングペンダントからロボットコントローラのIPアドレスを設定します。
(DENSO ROBOT USER MANUALS  `ID:1783 <https://www.fa-manuals.denso-wave.com/jp/usermanuals/001783/>`_ )
 
操作経路：[F6 設定] - [F5 通信と起動権] - [F2 ネットワークと通信権]

IPアドレスを 192.168.0.2 に設定します。

img

サンプルプログラムをGitHubから入手する
----------------------------------------------

ShoheiKobataのgithubからpythonサンプルのリポジトリを取得する


.. code-block:: bash

    cd workspace
    git clone https://github.com/ShoheiKobata/orin_bcap_python_samples.git

サンプルプログラムを入手できました

サンプルプログラムを動作させる
----------------------------------------------

ロボットコントローラのグローバル変数の読み書きをするサンプルを動作させます。

.. code-block:: bash

    cd orin_bcap_python_samples/SimpleSamples
    python 01_00_ReadWrite.py

ロボットコントローラのグローバル変数が書く変わっていることを確認してください。
ティーチングペンダントでグローバル変数を確認する方法
(DENSO ROBOT USER MANUALS  `ID:1761 <https://www.fa-manuals.denso-wave.com/jp/usermanuals/001761/>`_ )

基本画面 - [F3 変数]

.. code-block:: python

    # set IP Address , Port number and Timeout of connected Robot Controller (RC8,RC8A,COBOTTA,RC9)
    host = "192.168.0.1"

IPアドレスを指定している部分を変更することで、接続対象のロボットコントローラのIPアドレスの指定を変更できます。
