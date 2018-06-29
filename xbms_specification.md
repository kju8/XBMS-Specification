<!--
---
toc:
	depth_from: 1
	depth_to: 6
	ordered: true
---
-->

XBMS Specification (Draft 0.2)



# 要素
## xbms
- Contents : ( head | body | include | trunk )*

XBMS文書の最上位要素です。

## head
- Contents : ( ( title+ & artist+ & genre* & level* ) & ( sound+ & image* & layer* & resource* ) & basebpm? & ( ( keys? & gauge* ) | ( multitype? & player+ ) ) & judge* & include* & trunk* & identity? )

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
- Contents : ( key+ | controller+ )

XBMS文書がどのような音楽ゲームに対応するかを定義します。

### key
- Attribute
	- position = NUMBER
	- type? = ( key | scratch | footpedal )

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
- Contents : ( key+ | controller+ ) & gauge+
- Attribute
	- team? = NUMBER

プレイヤーごとにどのような入力装置を使用するか定義します。  
team属性は<playtype>がteamの時必須であり、チーム分けを定義します。

### judge
- Contents : setjudge+
- Attribute
	- player? = NUMBER
	- controller? = NUMBER
	- key? = NUMBER
	- group? = GROUP

XBMS文書の判定を定義します。  
player,controller,key属性は判定が適用される入力を指定します。省略した場合や0とした場合、全ての入力装置に適応されます。  
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
複数定義された場合、合計値となります。

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

judge属性は、\<judge\>タグのtype属性と対応する。   
`calc="relative"`の場合、1ノートあたりの回復量を1.0とした相対値が適用される。
`calc="failed"`の場合、その判定が出た時にプレイを途中終了する。

### identity
- Contents : STRING
差分譜面を管理する際に扱う、一意的な文字列。同じBMSであれば、全ての譜面で共有する必要がある。

基本的にはユーザが認識できないよう、エディタ側で自動処理する必要がある。  
発行するナンバーの生成処理はエディタ側に任せるが、最低限サイズだけは共通化しておきたい。

## body
- Contents : ( bar | bpm | bgm | note | bga | scroll | include | trunk )*

XBMS文書として再生される要素を定義します。

### bar
- Contents : ( bpm | bgm | note | bga | stop | include )*
- Attribute
	- time = TIME
	- length? = TIME
	- hidden?
	- group? = GROUP

1小節を定義します。  
time属性は曲頭からどの位置にこの小節を置くかである。省略された場合、0となります。  
length属性はこの小節の長さであり、省略された場合4拍、つまり4/4拍子となります。  
hidden属性がある場合、始点、終点に小節線が表示されなくなります。  
group属性がある場合、その中に含まれる全てのノーツがそのグループに振り分けられます。

### bpm
- Contents : NUMBER
- Attribute
	- time? = TIME

BPMを定義します。  
time属性はどの位置からこのBPMで再生するかであり、省略された場合0となります。

### bgm
- Attribute
	- time? = TIME
	- sound = STRING
	- lane? = NUMBER

演奏に依らず再生される音声を定義します。  
sound属性と同じidの\<sound\>要素のファイルを再生します。  
time属性はどの位置から再生するかであり、省略された場合0となります。  
lane属性はXBMSエディタ上での位置を定義する。time属性とlane属性がどちらも同じ\<bgm\>が複数あっても本体側では何の問題もなく動き、エディタでも重なったまま保存することが望ましい。

### note
#### note / note type="note"
- Attribute
	- time = TIME
	- sound = STRING
	- position = POSITION
	- group? = GROUP

timeで指定された位置にノーツを定義します。  
このノーツが演奏された時、sound属性と同じidの\<sound\>要素のファイルを再生します。  
position属性は演奏する入力装置を選択します。

#### note type="long"
- Attribute
	- time = TIME
	- length = TIME
	- sound = STRING
	- position = POSITION
	- group? = GROUP
	- release? = STRING
	- start? = (down | keep)
	- end? = (up | keep)

timeで指定された位置から始まるロングノーツを定義します。  
このノーツが演奏されている間、sound属性と同じidの\<sound\>要素のファイルを再生します。  
また、release属性が定義されている場合、離された時そのidの\<sound\>要素のファイルを再生します。  

start及びend属性は始・終端の判定について指定します。

- `start="down"`(デフォルト値)の場合、始端で入力開始判定を行います。
- `start="keep"`の場合、始端以前から入力されていれば最善の判定を行います。
- `end="up"`の場合、終端で入力終了判定を行います。**ノーツ数は1増加します。**
- `end="keep"`(デフォルト値)の場合、終端で特別な判定は行いません。

#### note type="mine"
- Attribute
	- time = TIME
	- sound? = STRING
	- position = POSITION
	- group? = GROUP
	- damage = NUMBER

地雷ノーツを定義します。

#### note type="invisible"
- Attribute
	- time = TIME
	- sound? = STRING
	- position = POSITION
	- group? = GROUP

不可視ノーツを定義します。

#### note type="rotation"
- Attribute
	- time = TIME
	- sound? = STRING
	- position = POSITION
	- length = TIME
	- angle = NUMBER
	- group? = GROUP

一回転(角度指定)ノーツを定義します。  
lengthの区間にかけて特定の角度ちょうど回転させることを要求します。
角度が入力できない入力装置であった場合、ロングノーツと同等であれば良いとします。

#### note type="freezone"
- Contents : note*
- Attribute
	- time = TIME
	- id = STRING
	- position+ = POSITION
	- length = TIME
	- group? = GROUP

フリーゾーンを定義します。フリーゾーン内の他のノーツが降ってくるまでの間、演奏した時、同じidの<sound>を再生します。  
フリーゾーン内にいくつのノーツがあったとしても、フリーゾーン一つで1つのノーツとなります。  
判定については以下の条件を満たすことを要求します。

- 中にノーツが含まれている場合、以下のうち良い方の判定を採用する。
	- 中のノーツの判定の中で最悪の判定
	- フリーゾーン内で一度でも操作していた場合、コンボが切れない最良の判定
- 中にノーツが含まれていない場合、以下のうち良い方の判定を採用する。
	- 一度でも入力装置を操作していた場合、最良の判定となる
	- その他の場合、最悪の判定

ただし、ここでの「フリーゾーン内のノーツ」には地雷ノーツ及び不可視ノーツは含みません。  
また、この要素には\<note type="freezone"\>及び同じposition属性を持たない<note>、group属性を持つ\<note\>を含むことが出来ません。

### bga
- Attribute
	- time? = TIME
	- image? = STRING
	- layer = NUMBER

BGAを定義します。  
time属性で指定された位置からimage属性をlayer属性で指定した\<layer\>に表示する。

### scroll
- Attribute
	- time = TIME
	- speed = NUMBER
	- group? = GROUP

譜面の速度を見た目だけ変更する。  
speed属性は現在のBPMから計算されるスクロールスピードを1とした相対的な速度で、0では停止、負数では逆走する。

## include
- Contents : FILE

内容のファイル名のXBMSファイルを読み込みます。

## trunk
- Contents : branch+

分岐を定義します。

### branch
- Contents : condition? , 最も近いtrunk/brunch以外の先祖要素が含むことの出来るすべての要素*
- Attribute
	- priority? = NUMBER
	- multiplicity? = NUMBER

<condition>の条件を満たした場合、中の要素を解釈します。  
ただし、一つの<trunk>要素の中の<brunch>要素はpriorityが大きい一つのみ解釈されます。  
priorityが等しく、<condition>の条件を満たしている<brunch>が複数ある場合、ランダムで選択されます。この時、multiplicityがその分岐に進む割合となります。  
priorityは省略された場合0、multiplicityは省略された場合1となります。  

#### condition
（未記述）
