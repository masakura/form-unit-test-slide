画面単体テストって面倒じゃないですか?

![目的の画面まで遠い](./resources/forms.png) <!-- .element: style="width: 70%;" -->

NOTE:
* 対象の画面を開くのに手間がかかる
  - ビルド -> 実行 -> ログイン -> ぼたんぽちぽち...
  - 最初の方の画面はいいんだけどね...
* テストのためのデータ入力だるい
* しかもこれを何十回も!

ウェブアプリだとかなりましだけど、画面のデザインをちょこっとだけいじってそれを確かめるために、ビルドしなおして実行してログインして画面を開いて開いて... とかってやってると、書きなおすのに 5 秒で、それを確かめるために 5 分とか、ありませんか?
