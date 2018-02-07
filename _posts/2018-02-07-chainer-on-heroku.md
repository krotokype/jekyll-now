---
layout: post
title: MNISTにモデルを使ったウェブアプリケーションを作り、Herokuで公開した
---

先日書いた[test.py](https://github.com/krotokype/chainer-mnist/blob/75d7ded96b9b299422807d394131e80c3c2304e3/test.py)を改造して、[ウェブアプリケーションを作った](https://github.com/krotokype/chainer-mnist-flask/tree/9d82adbfeb931c842e677066cd256c13c790208c)。また、このウェブアプリケーションを[Heroku上に公開した](https://chainer-mnist-flask.herokuapp.com/)。

![スクリーンショット](https://user-images.githubusercontent.com/36023293/35922115-095d7438-0c60-11e8-81bc-696fadc66382.png)

[前回記事](/write-model-and-use-and-resume/)に書いた通り、モデルを記述したpythonファイルとパラメータが記録されたバイナリファイルの二つをレポジトリに入れておく必要がある。巨大なバイナリになる場合はgitではない別の方法で管理が必要になると思うが、その時、モデルを記述したファイルをどう管理するかは考えた方が良いだろう。

ウェブアプリケーションは `GET /` と `POST /predict` のみである。 `GET /` は静的HTMLの配信のみを行っている。このHTMLにはJavaScriptも含まれており、 `canvas` に字を書け、predictボタンを押すと長さ784の配列（MNIST仕様）に変換し、APIに送信、判定結果を描画する。 `POST /predict` はJSON型式で受け取った配列を型変換し、predictorの結果をJSON型式で返す。predictの中で重要なのは `dtype=np.float32` と `.tolist()` である。まず、前者は無いと `np.float64` になり、警告され実行が止まる。現状がどのような型かは配列の形を見る `x.shape` と同じように `x.dtype` を確認すれば良い。後者はnumpyの配列はJSON型式にできないのでリストにためである。

Herokuでの公開は[Flask を触ってみる + Heroku で動かす](https://qiita.com/sqrtxx/items/2ae41d5685e07c16eda5)の通りにやった。server.pyの `python server.py` のみで使う機能に[トップレベルのガード](https://docs.python.jp/3/library/__main__.html)をつける。これはgunicornはpythonファイルをモジュールとして読み込む（ `gunicorn <ファイル名>:<flaskの変数名>` のように使う）からである。これで立ち上げるということを既定のフォーマットでProcfileに書けば、Herokuで動作する。

さて、実際試すとよく分かるが、数字の2が1だとか7だとかに誤認識されることが極めて高い。これはこのモデルはaccuracyが93.7%ぐらいのもので、雑な手書きが入るとダメさが顕著になる。というわけで、次は精度向上を目指していきたい。
