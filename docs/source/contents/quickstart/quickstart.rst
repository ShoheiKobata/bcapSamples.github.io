===================================
Quick Start Enviroment
===================================

GitHub上のリポジトリプログラムを使用し、ロボットコントローラへアクセスするための使用環境を説明します。


使用環境
===============================

推奨環境は以下の通りです。

PC
-----------------------
OS : windows , Mac , Linux

言語
-----------------------
Python

ロボットコントローラ
-----------------------
RC8,RC8A,COBOTTA,RC9,COBOTTA Pro


ネットワーク設定
-----------------------
PCとロボットコントローラのIPアドレスです。PCとロボットコントローラは有線LANで接続してください。
PC ---(有線LAN)--- RC

- PC : 192.168.0.2
- RC8: 192.168.0.1 (default)


準備
===============================

bcapを使用したサンプルプログラムとPC環境準備、ロボットコントローラの準備の手順です。

ロボットコントローラの準備
----------------------------------------------

1. IPアドレスを設定する
2. 


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

    cd cd orin_bcap_python_samples/SimpleSamples
    python 01_00_ReadWrite.py

ロボットコントローラのグローバル変数が書く変わっていることを確認してください。

.. code-block:: python

    # set IP Address , Port number and Timeout of connected Robot Controller (RC8,RC8A,COBOTTA,RC9)
    host = "192.168.0.1"

IPアドレスを指定している部分を変更することで、接続対象のロボットコントローラのIPアドレスの指定を変更できます。
