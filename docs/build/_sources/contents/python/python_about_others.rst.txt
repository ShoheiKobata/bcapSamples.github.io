その他サンプルの説明
===============================

その他アプリケーションによって必要な処理やコマンドの使い方については個別にサンプルプログラムを準備しています. 


マルチスレッド対応について
----------------------------

マルチスレッドでロボットにアクセスし, 
- thread-1 では ロボットの動作プログラム
- thread-2 では ロボットのデータ取得

のようにアプリケーションを設計する場合, `Multithread_bcap_RC8.py <https://github.com/ShoheiKobata/orin_bcap_python_samples/blob/master/others/Multithread_bcap_RC8.py>`_ を
参照してください. 

スレッド毎に ``BCAPClient`` を設定する必要があります. 

