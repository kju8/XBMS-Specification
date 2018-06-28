---
toc:
  depth_from: 1
  depth_to: 6
  ordered: true
---

XBMS Specification (Draft 0.2)

[TOC]

# 要素
## xbms
 - Contents : ( head | body | include | trunk )*

XBMS文書の最上位要素です。

## head
 - Contents : ( ( title+ | artist+ | genre* | level* ) | ( sound+ | image* | layer* | resource* ) | basebpm? | keys? | judge* | gauge* | include* | trunk* | identity? )

XBMS文書のタイトルなどのヘッダ情報及び使用する音ファイルなどの情報を記述します。

### title
 - Contents : STRINGS
 - Attribute
	- priority? = NUMBER
	- type? = STRINGS

XBMS文書のタイトル／サブタイトルを定義します。  
priorityが小さいほどメインのタイトルとなります。  
typeはどのようなタイトルであるかを表現します。  
以下に例を提示します。

| type | 説明 |
|:-----|:-----|
| 未指定 | 通常のタイトル |
| subtitle | 副題 |
| remixtitle | リミックス名 |

### artist
 - Contents : STRINGS
 - Attribute
	- priority? = NUMBER
	- type? = STRINGS

XBMS文書のアーティスト／サブアーティストを定義します。  
priorityが小さいほどメインのアーティストとなります。  
typeはどのようなアーティストであるかを表現します。  
以下に例を提示します。

| type | 説明 |
|:-----|:-----|
| 未指定 | BMS作者・作曲者 |
| bga<br/>mov<br/>movie | 動画製作者 |
| obj<br/>object | 譜面作者 |

### genre
 - Contents : STRINGS
 - Attribute
	- priority? = NUMBER
	- type? = STRINGS

XBMS文書のジャンルを定義します。  
priorityが小さいほどメインのタイトルとなります。  
typeはどのようなジャンルであるかを表現します。

### sound
- Attribute
	- id = NUMBER
	- src = FILE
	- start? = LENGTH
	- end? = LENGTH

XBMS文書で使用する音ファイルを定義します。  
id属性はノーツと音ファイルを連携させます。自然数を要求します。  
src属性は読み込む音ファイルを定義します。

startとendは波形ファイルの部分切り出しを定義する。どちらも省略した場合は0になる。  
ただし、`start < end`でない場合は部分切り出しを有効化せず、全て再生する。

### image
- Attribute
	- id = NUMBER
	- src = FILE

XBMS文書で使用する画像／動画ファイルを定義します。  
ノーツと画像を対応させるとき、idを参照します。  
src属性は読み込む画像／動画ファイルを定義します。

<!--	TODO
		画像の部分切り出しをサポートする。 -->

### resource
- Attribute
	- type = STRING
	- src = FILE

暫定的に追加するタグ。  
雑多なデータファイルの定義。

既存のBMSフォーマットとの互換性を確保するため、本体には以下の属性に対する処理を実装することを推奨する。

- type = "backbmp"
- type = "banner"
- type = "stagefile"
- type = "preview"

### layer
- Attribute
	- priority = NUMBER
	- judge = STRINGS
	- length? = TIME

BGAのレイヤーを定義する。

judge属性が定義されていない場合、そのレイヤは常時表示される。  
judge属性が定義されている場合、その判定が出た時間からlengthの間だけ、このlayerを表示する。  
`judge="miss"`の場合のみ、ゲージ変動量が0未満の判定全てに対して条件が適用される。

レイヤの描画順はpriority属性によって決まり、値の小さいものから順に描画される。

### basebpm
- Attribute
	- main : NUMBER
	- max? : NUMBER
	- min? : NUMBER

暫定的に追加するタグ。  
開始時のBPMや、選曲画面に表示するBPM、自動BPM設定機能の倍率計算等に使用する。

### keys
 - Contents : (key+ | controller+ | (multitype , player+))

XBMS文書がどのような音楽ゲームに対応するかを定義します。

### key
 - Attribute
	- position = NUMBER
	- type? = (key | scratch | footpedal)

このXBMS文書をプレイするときに必要な入力装置を定義します。  
type属性が省略、もしくはkeyであった時、鍵盤もしくはボタンを一つ使うことを定義します。  
type属性がscratchであった時スクラッチを一つ使うことを定義します。  
type属性がfootpedalであった時フットペダルを一つ使うことを定義します。  
position属性は入力装置の順番を示します。小さいほど左側にあります。

### controller
 - Contents : key+
 - Attribute
	- position = NUMBER

入力装置をグループ分けし、一つのコントローラーとして定義します。  
例えば14鍵盤+2スクラッチの場合、一般的には7鍵盤+1スクラッチを一組として扱われます。  
position属性はコントローラーの位置の順番を示します。小さいほど左側にあります。

### multitype
 - Contents : STRING = (coop | battle | team)

どのような形式で複数人プレイするかを定義します。  
coopの場合、全てのプレイヤーで協力して一つのXBMSをプレイします。  
battleの場合、プレイヤーごとに対戦して一つのXBMSをプレイします。  
teamの場合、チームごとに対戦して一つのXBMSをプレイします。

### player
 - Contents : (key+ | controller+)
 - Attribute
	- team? = NUMBER

プレイヤーごとにどのような入力装置を使用するか定義します。  
team属性は<playtype>がteamの時必須であり、チーム分けを定義します。

### judge
- Contents : setjudge+
- Attribute
	- controller? = NUMBER
	- player? = NUMBER
	- key? = NUMBER
	- group? = GROUP

XBMS文書の判定を定義します。  
controller,player,key属性は判定が適用される入力を指定します。省略した場合や0とした場合、全ての入力装置に適応されます。  
group属性を指定するときそのgroupのノーツのみにこの判定が適応されます。省略した場合0となります。

### setjudge
- Contents? : NUMBER
- Attribute
	- type = STRING
	- priority? = NUMBER
	- center? = TIME
	- combocut?
	- noprocess?

XBMS文書の判定を定義します。  
±TIMEの判定幅を持つことをいいます。  
type属性は定義する判定を指定します。  
XBMSプレイヤーは少なくともperfect,great,good,bad,poorの定義を受け付けなければならない *(MUST)*。さらに他の値が入力されても処理できる必要がある。  
center属性は判定の中央を指定します（+で判定が遅れる）。省略した場合0msです。

要素を省略もしくは0および負数を定義した場合、どの条件にも当てはまらなかった場合の挙動を取る。  
どの条件にも当てはまらなかった場合の挙動は、省略できない。

combocut属性を追加。これがある判定を取った時のみコンボが切れる。  
noprocess属性を追加。これがある判定を取った時はノートが処理されず、引き続き受付を行う。(所謂空poor)

### gauge
- Contents : setgauge+ | NUMBER
- Attribute
	- mode? = STRINGS
	- group? = GROUP

ゲージの増減を定義します。  
内容がNUMBERであった時、これまでのBMSの#TOTALと同じく最良の判定で（group内の）全てのノーツをperfectで演奏した時増加するゲージを指定します。  
mode属性はどのゲームモードの時このゲージ増減を適応するかを指定します。省略された場合NUMBERは全てのゲームモードに定義され、setgaugeはnormalに対応します。  
XBMSのプレイヤーは少なくともnormal、hard、easyに対応する必要があります *(MUST)*。  
group属性を指定するときそのgroupのノーツのみにこのゲージ増減が適応されます。省略した場合0となります。


### setgauge
- Contents : NUMBER
- Attribute
	- type = ( total | start | clear | max | bottom | notes )

<!--	TODO
		totalはモードごとに切り分ける必要が無いが、ほかの要素はモードごとに切り分ける必要がある。どうしたものか -->

#### setgauge type="total"

これまでのBMSの#TOTALと同じく最良の判定で（group内の）全てのノーツをperfectで演奏した時増加するゲージを指定します。

#### setgauge type="start"

ゲーム開始時のゲージ量を指定する。

#### setgauge type="clear"

ゲーム終了時に成功判定となるゲージ量の下限を指定する。

#### setgauge type="max"

ゲージ量の上限を指定する。

#### setgauge type="bottom"

ゲージ量の下限を指定する。  

<!--	TODO
		ゲージが0になった場合は途中終了すると仮定しているが、もっと途中終了条件を柔軟に考えた方がよい気はする。 -->

#### setgauge type="notes"
- Attribute
	- judge = STRING
	- calc = ( relative | absolute | failed )

judge属性は、&lt;judge&gt;タグのtype属性と対応する。   
`calc="relative"`の場合、1ノートあたりの回復量を1.0とした相対値が適用される。
`calc="failed"`の場合、その判定が出た時にプレイを途中終了する。

### identity
- Contents : STRING
差分譜面を管理する際に扱う、一意的な文字列。同じBMSであれば、全ての譜面で共有する必要がある。

基本的にはユーザが認識できないよう、エディタ側で自動処理する必要がある。  
発行するナンバーの生成処理はエディタ側に任せるが、最低限サイズだけは共通化しておきたい。

## body
 - Contents : ( bar | bpm | bgm | note | bga | stop | include | trunk )*

XBMS文書として再生される要素を定義します。
