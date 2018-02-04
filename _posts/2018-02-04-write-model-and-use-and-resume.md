---
layout: post
title: MNISTにモデルの書き出しと読み出して評価する機能とレジュームを実装した
---

先日書いたtrain.pyを[改造して](https://github.com/krotokype/chainer-mnist/blob/75d7ded96b9b299422807d394131e80c3c2304e3/train.py)、モデルの書き出しと一定タイミングでのスナップショットの取得ができるようにした。実行すると `result/model.npz` にモデルファイルを書き出すことができる。加えて、[test.py](https://github.com/krotokype/chainer-mnist/blob/75d7ded96b9b299422807d394131e80c3c2304e3/test.py)で書き出したモデルを利用し、指定したテストデータの判別を行えるようにした。これによって、実用的なアプリケーションに組み込む場合の道筋がついた。

どのようにしてモデルを使えば良いかは[Chainer v3 ビギナー向けチュートリアル 保存したモデルを読み込んで推論する](https://qiita.com/mitmul/items/1e35fba085eb07a92560#7-%E4%BF%9D%E5%AD%98%E3%81%97%E3%81%9F%E3%83%A2%E3%83%87%E3%83%AB%E3%82%92%E8%AA%AD%E3%81%BF%E8%BE%BC%E3%82%93%E3%81%A7%E6%8E%A8%E8%AB%96%E3%81%99%E3%82%8B)のコードが参考になった。サンプルではGPUも使えるコードとなっているが、ここではまずは最小限のCPU向けのコードに書き直した。

test.pyの使い方の例は `python test.py result/model.npz 123` となる。引数としてモデルのパスと、testで使いたい画像のインデックスを指定するという方式になっている。注意しなければならないのは、モデルの保存が効くのはパラメータのみであり、その構造は保持されない。つまり、ネットワーク構造のクラスのコードはなんらかの方法で共通化する必要がある。実際のサービスに組み込む場合は複数のファイルの更新が必要となるため、注意すべきだろう。