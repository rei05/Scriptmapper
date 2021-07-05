# Script Mapper

このツールでは、以下のように MediocreMapper 上でブックマークに命令（スクリプト）を書いていくことにより、同じタイミングでカメラが動いてくれるようなカメラスクリプトが作成される、というツールです。

![fig1](https://user-images.githubusercontent.com/43929933/124432656-d4d87100-ddac-11eb-810c-150ec4d64882.png)

# コマンドの持続期間

- スクリプトは「`,`」（カンマ）によって 1 つまたは 2 つのコマンドに区切られます。
- コマンドの持続期間は、そのブックマーク位置から、次のブックマーク位置までです。
- コマンドが 1 つのみの場合は、期間中ずっと同じ座標・角度になります
- コマンドが 2 つある場合は、1 つ目のコマンドから 2 つ目のコマンドへ連続的に変化します。

# プリセットコマンド　基本編
スクリプトの先頭に以下の文字が書かれていた場合、プリセットコマンドとして認識されます。

## def
デフォルト値です。真後ろから見下ろす視点です。パラメータは必要としません。

ex) `def`

## random
ランダムな座標にカメラを置きます。カメラアングルは、アバターの方向を見るように設定されます。後に続く文字列を半径（メートル）と認識します。

ex) `random3`, `random4.5`

## front
真正面かつ高さ 1.5m の位置にカメラを置きます。カメラアングルは、後ろ方向を見るように設定されます。後に続く文字列を前方向の位置（メートル）として認識します。

ex) `front1`

# オリジナルコマンド
デフォルトコマンドで表現が難しい場合は、自分でパラメータを指定したオリジナルコマンドを作成します。オリジナルコマンドは以下のような csv ファイルを用意し「`input.csv`」と名付けます。（それ以外の名前ではプログラムが認識しません）。

![fig2](https://user-images.githubusercontent.com/43929933/124432665-d6a23480-ddac-11eb-9735-7a414c847bab.png)

ヘッダー（1行目）には以下の列名を記入してください。

- `label`: コマンドとして認識される名前。これと同じ名前を、ブックマークでは記入
することになります。
- `px`: カメラの x 座標（メートル）
- `py`: y 座標
- `pz`: z 座標
- `lookat`: ここを `true` にした場合、自動的にアバターを向く角度を指定します。以降に記入する角度のパラメータは無視されます。
- `rx`: カメラの x 角度（0～360 度）
- `ry`: y 角度
- `rz`: z 角度

# 実行方法
Scriptmapper.exe を WIP フォルダ直下にコピーしたカスタムマップフォルダに入れてください。

![fig4](https://user-images.githubusercontent.com/43929933/124432683-dace5200-ddac-11eb-8a8e-b63a431fed8a.png)

本ソフトは、ブックマークの編集中にカスタムマップの譜面データを変更してしまう危険性を考慮し、**CustomWIPLevels 直下でないとエラーを起こす**ように設定しています。お手数ですが、必ずマップを CustomWIPLevels にコピーしてから作業を行ってください。

ブックマークでスクリプトを記入した dat ファイルを、Scriptmapper.exe のアイコンにドラッグ＆ドロップしてください。

![fig5](https://user-images.githubusercontent.com/43929933/124432690-dc981580-ddac-11eb-82af-f44095647660.png)

`C:¥Program Files (x86)¥Steam¥steamapps¥common¥Beat Saber¥UserData¥CameraPlus¥Scripts` に `Scriptmapper_output.json` が出力されていれば成功です。後は、`camerascript.cfg` の `movementScriptPath` を`”Scriptmapper_output.json”`に指定すれば、マッピングした通りにカメラが動作してくれるはずです！

また、同時にカスタムマップフォルダに log が生成されます。生成に失敗した場合、また成功しても、意図したカメラの動きになっていない場合、log を見れば原因がわかるかもしれません。

![fig6](https://user-images.githubusercontent.com/43929933/124432709-e02b9c80-ddac-11eb-961a-9613fc0f6da8.png)

---

※これより下の内容は発展的な内容になります。

# プリセットコマンド-中級編

以下のコマンドは、デフォルトコマンドですが、直前の座標に操作を加えるものです。

## before
直前の座標をそのまま返します。パラメータは必要としません。

ex) `before`

## mirror
直前の座標を左右方向に鏡像反転させます。パラメータは必要としません。

ex) `mirror`

## zoom
直前の座標を縮小します（つまりアバターに近づく）。縮小の中心は(0,1,0)です（おおよそアバターの胸の位置）。後に続く文字列を縮小率として認識します。

ex) `zoom2`

※ zoom1 は何の意味も持たないことにご注意ください

## spin
カメラの z 方向角度（ロール）を変化させます。後に続く文字列を反時計周りの角度（0~360 度）として認識します。

ex) `spin-40`

※数値はマイナスをつけることもできます

## slide
カメラの左右方向の位置を移動させます。後に続く文字列を右方向の移動距離（メートル）として認識します。

ex) `slide.5`

※「.5」はプログラム上では「0.5」と同じです

## shift
カメラの上下方向の位置を移動させます。後に続く文字列を上方向の移動距離（メートル）として認識します。

ex) `shift.5`

# プリセットコマンド　上級編

以下のコマンドは単一の座標を返すのではなく、一定の連続した動きを記述するコマンドです。

## rotate

カメラがアバターを中心に回転します。後に続く文字列（から、スラッシュで区切られた 2つの文字列）をそれぞれ半径、高さ（ともにメートル）と認識します。また、半径は高さを考慮しない水平投影面での半径を示します。

ex) `rotate4/3` （半径 4m、高さ 3m の円軌道）

## vibro
カメラが振動します。パラメータは必要としません。

ex) `vibro`

---

※これより下の内容は更にマニアックになります

# 特殊コマンド

以下はスクリプトそのものを対象とする、発展的な特殊コマンドです。ある程度ツールの操作に慣れた上で、操作を省略するためのものです。

## copy

スクリプトをある範囲で一括コピーします。マップの展開が同じ部分で、以前書いたブックマークを使い回す時に便利です。例えば、100グリッド目で`copy40`と書いたとします。その次のブックマークが120だった場合、コピーを**貼り付ける**範囲は100～120になります。この長さは20です。そしてコピーの**元となる**範囲は40から始まり、同じ長さを持つ部分になります。つまり、40～60の部分が100～120の範囲に転写されます。この時、終点となる60や120丁度のグリッドは転写範囲に**含まれない**ことに注意してください。※

ex) `copy40`

※開始地点から「終了地点までの」範囲ではなく、「次の範囲の開始地点の直前まで」と考えます。例えば、64グリッド目から更に32グリッド分を含む範囲は95グリッドまでですが、64+32=96グリッド目の「直前まで」と考えた方が計算としてはわかりやすいです。

## fill

スクリプトを反復複製します。例えば、`random3`をある区間何回も繰り返す場合、それをすべて書くのは面倒です。そのような場合、例えば`fill1/4,random3`と書くことで、同じ記述をすることができます。つまり、以下の画像はプログラム上では同じものとして認識されます。

![fig3](https://user-images.githubusercontent.com/43929933/124432671-d86bf800-ddac-11eb-927c-23caf4f5bdb2.png)

fill の後に続文字列を1スパンとして認識します。つまり`fill1/4,random3`は次のブックマークまで、マッパー上のグリッドの 1/4 間隔で`random3`というブックマークを記入し続けます。

ex) `fill1/4,random3,zoom2`（グリッド 1/4 間隔で`random3,zoom2`を記入し続ける）

ex) `fill2,mirror`（グリッド 2 間隔で`mirror`を記入し続ける）

※パラメータには分数を指定することもできます。正確には、pythonの`eval`関数で評価できる範囲ということになります。

## copyとfillの併用

copy範囲に他のスクリプトを含めることはできません（そうなった場合、挟まれたコマンドがcopyの終点になってしまいます）。しかし、fill内にcopyを含めることはできます。

ex) `fill2,copy40` fillの終点まで、グリッド40~42の内容が反復されることになります
