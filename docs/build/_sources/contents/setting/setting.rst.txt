===================================
Environment
===================================

b-capでロボットと通信するための環境です。

OS
===============================

- windows
- linux
- MacOS

TCP/IPのソケット通信が可能であるデバイスであればb-cap通信を実装することが可能です。

開発言語
========================

開発言語も TCP/IP ソケット通信が可能であるデバイスであればb-cap通信を実装可能です。

b-capライブラリサンプル : `DENSORobot/orin_bcap <https://github.com/DENSORobot/orin_bcap>`_ 

デンソーウェーブgithubページにてb-capライブラリを公開しています。通信プロトコルを使いやすい形でライブラリにしています。
公開されている言語

- C
- CSharp
- python
- java


ROBOT Controller
===============================

b-cap通信に対応しているロボットコントローラ

- RC8/RC8A/COBOTTA
- RC9/COBOTTAPro

プログラム中に出てくる **プロバイダ名** を

- RC8/RC8A/COBOTTA の場合、 **VRC**
- RC9/COBOTTAPro の場合、 **VRC9**

としてください。
