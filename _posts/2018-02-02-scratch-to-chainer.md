---
layout: post
title: ゼロから作るDeep LearningのMNISTをChainerで書く
---

書籍[ゼロから作るDeep Learning](https://www.oreilly.co.jp/books/9784873117584/)で[MNISTを解くニューラルネット](https://github.com/oreilly-japan/deep-learning-from-scratch/tree/master/ch05)を実装するが、近年のフレームワークの理解を深めるためChainerで書き直してみた。Chainerを選択した理由は二点

1. ゼロから作るDeep Learningのサンプルコードの構造に近い印象を受けた
    1. 主観
1. `pip install chainer`で自分の環境には簡単に入った
    1. 詰まっていたら他の環境に移っただろう

である。

Chainerには[MNISTのサンプル](https://github.com/chainer/chainer/tree/v3.3.0/examples/mnist)が存在するが、これを参考に書き直した。結果が[train.py](https://github.com/krotokype/chainer-mnist/blob/05bc94f536c62508ca02eb52d3c436c9001fe926/train.py)である。これはChainerの[examples/mnist/train_mnist.py](https://github.com/chainer/chainer/blob/v3.3.0/examples/mnist/train_mnist.py)よりも機能を削減したもので、学習を行いつつ、テスト用データでの検証をログに取りつつ、モデルデータは全部捨てる構成となっている。これをベースに各種機能を理解しつつ実装していく。

なお、自分の環境でtrain.pyを（CPUで）実行した結果だが、最後のログだけ貼っておくと

```json
    {
        "main/loss": 0.21782624963670968,
        "elapsed_time": 129.20798607403412,
        "epoch": 100,
        "validation/main/accuracy": 0.9376000022888183,
        "validation/main/loss": 0.2145057866256684,
        "iteration": 60000,
        "main/accuracy": 0.9381500013669332
    }
```

となった。
