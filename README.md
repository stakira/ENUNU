# ENUNU

NNSVS用歌声モデルをUTAU音源みたいに使えるようにするUTAUプラグイン

## インストールと使い方の記事

[UTAUでNNSVSモデルを使おう！（ENUNU）](https://note.com/crazy_utau/n/n45db22b33d2c)

## 使い方

1. USTを開き、ENUNU用モデルを含むUTAU音源を原音ファイルセットに設定する。
   例）「おふとんP (ENUNU)」・・・ENUNU向けのNNSVS用おふとんP歌声モデル
2. USTの歌詞をひらがな単独音にする。
3. 再生したい部分を選択し、プラグインとしてENUNUを起動する。
4. ～ 数秒か数分待つ ～
5. 選択部分のWAVファイルがUSTファイルと同一フォルダに生成される。

## 使い方ヒント

- 2021年以前に配布された日本語モデルでは、促音(っ)は、直前のノートに含めることをお勧めします。
  - さっぽろ → \[さっ]\[ぽ]\[ろ]
- 2022年以降に配布された日本語モデルでは、促音(っ)は、独立したノートとすることをお勧めします。
  - さっぽろ → \[さ]\[っ]\[ぽ]\[ろ]

- 促音以外の複数文字の平仮名歌詞には対応していません。
- 音素を空白区切りで直接入力できます。平仮名と併用できますが、1ノート内に混在させることはできません。
  - \[い]\[ら]\[ん]\[か]\[ら]\[ぷ]\[て] → \[i]\[r a]\[N]\[k a]\[ら]\[p]\[て]
- 音素の直接入力により、1ノート内に2音節以上を含めることができます。
  - \[さっ]\[ぽ]\[ろ] → \[さっ]\[p o r o]

## 利用規約

利用時は各キャラクターの規約に従ってください。また、本ソフトウェアの規約は LICENSE ファイルとして別途同梱しています。



---

ここからは開発者向けです

---



## 開発環境

- Windows 10
- Python 3.8（3.9はPytorchが未対応）
  - utaupy 1.18.0
  - numpy 1.21.2（1.19.4 はWindowsのバグで動かない）
  - torch 1.7.0+cu113
  - nnsvs 開発バージョン
  - nnmnkwii
- CUDA 11.3

## ENUNU向けUTAU音源フォルダの作り方

通常のNNSVS用歌声モデルも使えますが、[enunu training kit](https://github.com/oatsu-gh/enunu_training_kit)を使ったほうがすこし安定すると思います。採譜時の音程チェック用に、再配布可のUTAU単独音音源の同梱をお勧めします。

### 通常のモデルを使う場合

モデルのルートディレクトリに enuconfig.yaml を追加し、ENUNU用おふとんP歌声モデルなどを参考にして書き換えてください。`question_path` は学習に使ったものを指定し、同梱してください。

### ENUNU用のモデルを使う場合

モデルのルートディレクトリに enuconfig.yaml を追加し、[波音リツ ENUNU Ver.2] 同梱のファイルを参考にして書き換えてください。`question_path` は学習に使ったものを指定し、同梱してください。

## ラベルファイルに関する備考

フルコンテキストラベルの仕様が Sinsy のものと異なります。重要な相違点は以下です。

- フレーズに関する情報を扱わない（e18-e25,  g,  h,  i,  j3）
- ノートの強弱などの音楽記号を扱わない（e26-e56）
- 小節に関する情報を扱わない（e10-e17,  j2,  j3）
- 拍に関する情報を扱わない（c4,  d4,  e4）
- ノートの相対音高（d2,  e2,  f2）の仕様が異なる
  - ノートのキーを取得できない関係上、オクターブ情報を無視し、Cを0とした相対音高としている。
- ノートのキー（d3,  e3,  f3）を120に固定
  - 手動指定しない場合は120
  - 12の倍数 かつ Sinsyのラベルでは出現しない値 であれば代用できる。（24など）
- **休符を挟んだ場合のノートおよび音節の前後情報（a, c, d, f）が異なる**
  - Sinsyの仕様では、休符の直前のノートが持つ「次のノート」の情報は休符終了後のノートを指しますが、本ツールでは休符を指す設計としています。
  - 休符の直後のノートも同様に、休符開始前ではなく休符そのものを指す設計としています。
  - 音節も同様です。

