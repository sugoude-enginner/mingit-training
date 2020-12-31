# mingit-training

最低限のGitを段階的に作ることでGitの理解を深めることを目標に、その仕様とチュートリアルをまとめていくリポジトリです。

## VCS（Version Control System)としての最小要件

「あるバージョンの状態を参照できること」がVCSとしての最小要件になります。

バージョン管理をする目的は、バージョンを付与した時の状態（例えば、あるバージョンのソースコード）を再現することだからです。

Gitの場合、バージョンに対応する概念として”コミット”があります。

### コミットとは？

コミットはRPGのセーブデータのようなものです。

勇者をレベル２にしたときのセーブデータのように、ある状態のソースをコミットとして保存することができるのです。

そして、特定のセーブデータを起動するように、特定のコミットをチェックアウトすることができます。

では、勇者をレベル２にしたときのセーブデータから初めて、勇者をレベル３にあげたセーブデータを保存したとします。

一般的なRPGでは、レベル２とレベル３のセーブデータは全く別のセーブデータであり、それぞれが状態を丸ごと保存していると思いますが、Gitはもっとスマートです。

前回のコミットからの変更分のみを保存するので、容量を削減することができるし、速度も速くなるのです。

#### コミットグラフ

コミットは前回のコミットからの変更分が入っていることを説明しました。

よって、あるコミットには差分の情報に加えて、前回のコミットを特定する情報も含まれます。

あるコミットから見た前回のコミットのことを”親コミット”と言います。

２つのコミットの関係は、

```
（A）←（B）
```

というように表されます。（Aが親、Bが子）

時系列を考えれば矢印が逆なのですが、子は親コミットへの参照を持っているので、こういう向きになっていると考えられます。（そもそも矢印を使ってなかったり、時系列の矢印になっていることもよくあるので、気にしなくても良さそう）

RPGの例えだと、レベル２の勇者のセーブデータ（A)　← レベル３の勇者のセーブデータ（B)　というような関係を表しているのですが、例えばレベル２の勇者のセーブデータから始めて「レベル２の勇者とレベル２の魔法使いのセーブデータ」を作る場合もあると思います。

Gitのコミットにおいても同様な構造が表現できます。

```
（A）←（B）
　　 ↖︎（C）
```

「Aから始めてB」と「Aから始めてC」という２つの時系列があるわけで、このような状態は（A)のコミットから分岐していると言えます。

この状態だと、横向きですが「ツリー」状なのでコミットツリーと呼ばれることがあります。

しかし、実はBとCを合流した新しいコミットDを作ることもできます。

RPGで例えるなら、

- レベル３の勇者のセーブデータ（B)
- レベル２の勇者とレベル２の魔法使いのセーブデータ（C)

を合成して、

- レベル３の勇者とレベル２の魔法使いのセーブデータ（D)

という全く新しいセーブデータを作成してしまうことができます。

その結果、

```
（A）←（B）←（D）
　　 ↖︎（C）↙︎
```

という構造ができます。この時にDの親はBとCの２つになります。

また、AはBとCの共通の祖先という言い方もします。

こうすると、図はツリーではなくグラフになります。

なのでコミットグラフと呼べますが、簡易的にコミットツリーと呼ばれていることも多いです。

Gitを理解する上で、現在作業しているコミット周辺のグラフの形が想像できないと、余計な落とし穴にハマってしまうことになります。

自分がしている操作がどのようにコミットグラフに影響を与えるかを考られることが、Gitをシンプルに理解することへの近道なのです。

そして、Gitそのものを作成する上でも各操作を実装する際、どのようにコミットグラフを書き換えれば良いかを常に考えることになります。
