# テスト実行
### 概要

testdata/にテスト用データがおいてある。
(草津白根山の熱水系モデル; Matsunaga and Kanda, 2022)

浸透率構造作成に比抵抗構造モデル(Matsunaga et al., 2022)を使用

|ファイル|説明|
|-|-|
|testdata/setting.ini|TOUGH3プログラム本体のパスなど実行環境についての設定|
|<b>testdata/input.ini</b>|浸透率構造・TOUGH3インプットファイル作成のためのすべての設定が含まれるファイル|
|testdata/seed.txt|voronoiメッシュの"seed"となる点データ[x,y]|
|testdata/topo_coarse.dat|地形データ[x,y,elevation] (本白根山が中心)|
|testdata/cellCenterResistivity.txt|比抵抗構造データ[x,y,z,resistivity] (Matsunaga et al., 2022)|
|testdata/initial_1d.ini|シミュレーション初期条件作成に使用|
|testdata/input/initial_1d/SAVE|シミュレーション初期条件作成に使用|

<br>

### インプットファイルの作成

シミュレーションの実行ディレクトリ(すべてのinput/outputが書き出される場所)は設定ファイル(input.ini)の場所によらず以下になる。
    
```.../TOUGH3/(setting.iniのTOUGH_INPUT_DIR)/(input.iniのproblemName)```

input.ini の configini= に、setting.ini のパスを設定することで両者は紐付けられる。

ここでは以下になる。

```.../TOUGH3/testdata/1_reference_case``` 

実行方法

```
#以下、すべてのプログラムはプロジェクトのrootで実行すること
cd ..../TOUGH3
```

```
#メッシュ、浸透率構造の作成
python3 makeGridAmeshVoro.py testdata/input.ini
```
作成されるファイル
|||
|-|-|
|input.ini|testdata/input.iniのコピー|
|testdata/grid.geo|メッシュ定義ファイル(パスはinput.iniの[mesh]mulgridFileFpに指定されたもの)|
|t2data.dat.grid|浸透率構造のファイル|
|layer_surface.pdf|作成したメッシュのplan view|
|permeability_layer-XX.pdf|作成された浸透率構造の断面(index=XX - input.iniの[plot] profile_lines_listで指定する)|
|resistivity_slice-lineXX.pdf|比抵抗構造の断面(index=XX - input.iniの[plot] profile_lines_listで指定する)|
|topo.pdf|作成されたメッシュから書いた地形図|
```
#TOUGH3 インプットファイルの作成
python3 tough3exec_ws.py testdata/input.ini
```

作成されるファイル
|||
|-|-|
|testdata/input/1_reference_case/t2data.dat|TOUGH3のインプットファイル|
|testdata/input/1_reference_case/INCON|TOUGH3の初期条件の設定ファイル|
|testdata/input/1_reference_case/CO2TAB|EOSモジュールがECO2N_v2のとき必要なファイル。プロジェクトルートからコピーされる|

### 本体の実行

```
#実行dirに移動
cd testdata/1_reference_case

#以下は実行環境による(faradayの場合)
module purge
export LD_LIBRARY_PATH=/usr/local/lib/:$LD_LIBRARY_PATH

#TOUGH3本体実行 
mpiexec -n 8 (tough3-eco2n_v2のフルパス) t2data.dat (outputファイル名)
``` 

以下でも同じように実行可能

```
python3 run.py testdata/input.ini
```

出力ファイルについてははTOUGH3マニュアル参照

