# 手動テストでもがんばる!
## 趣旨
テスト自動化! とかが流行りだけど、なんでも自動化できるわけじゃない。特に画面周りのテストは自動化が難しい。

このスライドでは、画面の単体テストを自動化するのではなく、手動の画面の単体テストの効率を向上させる方法の一つを紹介します。

また、この方法は、画面のテストを自動化した後も、使い勝手を詰めるための試行錯誤でもかなり使えます。


## 単体テストの基本
### 単体テストパターン
手動とか自動とかに限らず、単体テストのパターンはこんな感じです。

```
|テストドライバー| -> [対象コンポーネント] -> |スタブ|
```

対象コンポーネントはメソッドかもしれませんし、オブジェクトかもしれませんし、画面かもしれません。

テストドライバーは対象コンポーネントを呼び出すためのプログラムです。対象コンポーネントを呼び出すために必要な初期化処理を含みます。

スタブは対象コンポーネントが呼び出す外部コンポーネントの代わりになるものです。


### 単体テストで守るべきこと
単体テストを行うときに必ず守らなければいけないことがあります。テストで利用するテスト対象のコンポーネント以外のコンポーネントはテスト済みである必要があります。

A/B/C という依存関係のあるコンポーネントがある場合を例にします。

最初は全て未テストの状態です。これら三つのコンポーネントをいっぺんにテストしようとしてはいけません。

```
|利用者| -> [A (未テスト)] -> [B (未テスト)] -> [C (未テスト)]
```

方法としてはボトムアップとトップダウンの二種類がああります。

ボトムアップでは一番利用者が遠いコンポーネントから順次テストドライバーを書いてテストしていきます。

```
|テストドライバー (C)| -> [C (未テスト)]  // <1> まずは C をテストする

|テストドライバー (B)| -> [B (未テスト)] -> [C (テスト済み)]  // <2> 次に B をテストする

[A (未テスト)] -> [B (テスト済み)] -> [C (テスト済み)]  // <3> 最後に A をテストする
```

トップダウンでは逆に利用者に近いコンポーネントからスタブを書いてテストしていきます。

```
[A (未テスト)] -> |スタブ (A)| //  <1> まずは A をテストする

[A (テスト済み)] -> [B (未テスト)] -> |スタブ (B)|  // <2> 次に B をテストする

[A (テスト済み)] -> [B (テスト済み)] -> [C (未テスト)]  // <3> 最後に B をテストする
```


### 個人的によくやる方法
ボトムアップもトップダウンも一長一短があります。ですので、テストドライバーとスタブの両方を使う方法をよく使います。(なんて言うかを知らない)

```
|テストドライバー (B)| -> [B (未テスト)] -> |スタブ (B)|  // いきなり B をテストしちゃう
```

この方法はコンポーネント数が多いプログラムで真価を発揮します。今回のコンポーネントは三つですが、例えばコンポーネント B が画面だったとして、その画面までたどり着くのにログインを含めて操作に 10 手順以上かかることも珍しくありません。

この方法であれば、テストの前提条件をすっ飛ばしていきなりテストを行うことができます。また、コンポーネントのテスト順番を自由にすることができます。

画面の手動テストでこの方法を使う場合は、テストドライバーとしてこういう画面を作り、ワンクリックでテスト対象の画面を呼び出せるようにします。

```
| [画面 A を起動] |
| [画面 B を起動] |
| [画面 C を起動] |
```

スタブは通常、ふりをするプログラムを書きますが、画面を持つこともあります。


### デモ
今回は、とあるプログラムの郵便番号検索機能の単体テストを中心にデモをします。トップダウンでテストする場合のテストはこんな感じになります。

* 前提条件
  - user1 が登録されている
  - customer1 が登録されている
  - 1. アプリを起動する
  - 2. ログイン画面で user1/password1 を入力しログインボタンをクリック
  - 3. メインメニューで顧客管理をクリック
  - 4. 顧客管理メニューで顧客一覧をクリック
  - 5. customer1 をの表示ボタンをクリック
* テスト操作
  1. 郵便番号検索ボタンをクリック
  2. 郵便番号に 0000000 を入力
  3. 検索ボタンをクリック
  4. 住所 xxx が表示されたことを確認
  5. OK ボタンを入力
  6. 住所に 4 が反映されていることを確認

前提条件を満たすためにちょっと時間がかかりますね...

デモでは、この郵便番号検索の画面を呼び出すためのテストドライバーと、郵便番号検索ウェブサービスを呼び出すスタブを利用して、この画面をテストしています。

これを使うことで、前提条件を無視して、テスト操作にいきなり入ることができます。6 の代わりに、ログを目視で確認します。


#### テストドライバー
```
// 郵便番号検索ウェブサービスのスタブを設定

// 郵便番号画面を呼び出す

// OK の場合、住所をログに出力する
```


#### スタブ
スタブは郵便番号検索ウェブサービスを呼び出すふりをします。

* 0000000 - 適当な住所を返します
* 0000001 - 例外を発生させます
* それ以外 - 住所が見つかりませんでした

郵便番号検索ウェブサービスにつながらなかったり、サービスがエラーになったりした場合の状況を入力した郵便番号に合わせて作ることができます。

スタブはこういったテストが簡単にできるのがいいですね。


#### プロジェクト分割
第一回ソフトウェアテスト勉強会で解説した方法を使って、テストドライバーとスタブは本番アプリのプロジェクトとは別にしています。

こうすることで、起動するプロジェクトを変えるだけで単体テストと結合テストを切り替えることができます。


#### それ以外の工夫
ウェブサービスを呼び出す際にログを出力するプロキシオブジェクトを挟むようにしています。これによって、ウェブサービスの呼び出しに間違いがないかも目視で確認ができます。

ですが、こういったものはログレベルで切り替えたほうがいいでしょう。


#### メリット
画面をきちんと仕上げるのは結構大変な作業です。今回はまだ作りかけですので放置していますが、まだこんなのが残っています。

* 住所が見つからなかった処理
* 例外が発生した時の処理
* 画面の微調整
* 7 文字の数字を打つまでボタンを無効化
* 数字以外を入力できないように

こういったものを一つ一つ実装していくために必要なテストの操作は非常に多いですが、テストドライバーを書くことでそれを解消しています。

さらに、スタブを使うことで、発生の可能性の低いウェブサービスのエラーもシミュレートしています。

結果的に、テストドライバーとスタブを書くコストはあっさり回収できます。

もっと言うと、この方法はバグレポートの提出時に効果を発揮します。バグレポートで重要なことの一つに再現性があります。そのため、だれでもそのバグを再現できるよう、手順を正確に書く必要があります。

この方法を使うと画面を呼び出すまでの手順が簡略化されますので、バグレポートを書くのが楽になるでしょう。他にも、スタブにバグを誘発するパターンを追加することで、再現させやすくする手段もあります。分かりやすい例だと、とても長い住所などがそれです。


#### 結合テスト
この画面の単体テストが終わったら、ウェブサービスと合わせた結合テストを行います。

テストドライバーで、スタブを使わないようにするだけです。実際に呼び出してウェブサービスとの連携がうまく行くかをテストします。

テストでコンポーネントを二つ (画面とウェブサービス) 利用していますが、画面の方はテスト完了していますので、主にウェブサービスとその結合部分のテストになります。バグがもし見つかっても、画面の方はないことが分かります。見なければいけないスコープが小さい分、デバッグがはかどります。


### まとめ
* テストドライバーとスタブを作ると画面の単体テストができる
* テストにかかる手数を減らせるので、テストがすぐにできる
* テストにかかるコストが低いので、何度も繰り返しテストできる
* 繰り返しができるので小さく実装していける
* バグレポートを書くのも楽になる

いいことづくし!


## おまけ
スタブ側に画面を持たせることもあります。

顧客詳細画面の郵便番号検索ボタンを押した時に、ユビン番号から住所を検索する本来の画面ではなく、にせものの住所一覧の画面を表示させます。

住所をダブルクリックしたらその住所を返すようにしておくことで、郵便番号を入力して検索ボタンを押してとかしなくても、顧客詳細画面に検索された住所が反映されるかをテストすることができます。

他にも、バッチ処理の進捗を表示するプログレスバーだけをテストすることも考えられます。バッチのスタブを時間がかかるもの・短時間で終わるもの・エラーで終了するものなどのパターンに分けて、テストドライバーでどれを使うかを選択できるようにします。

こうすることで、バッチの進捗に合わせてプログレスバーが進んでいるかや、エラーで終了を簡単にテストすることができます。


## 最後に
自動化だけがテストの工夫ではありません。

手動のテストでも、「テストしたいことは何か?」が分かれば工夫はいくらでもできます。私達はプログラマーですので、テストで使うための道具を自作できるのが強みですので、それを活かしてじゃんじゃん効率を上げていきましょう!

特に若い経験がない方は細かい試行錯誤が大事です。タスクを 20 個こなして 20 個テストするのではなく、細かいタスク (0.2 くらい?) をこなしてそこだけテストをするというのを繰り返せないと、技術力はあがりません。

今回の方法は試行錯誤を効率よくこなす方法の一つです。ぜひトライしてみて下さい。
