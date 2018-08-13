# 画像の変形

すべての人に実サイズの画像アップロードを要求するのではなく、Craft が代わりにどう処理するかのルールをセットした「画像の変形」を定義することができます。トランスフォームは _非破壊的_ で、アップロードされた元画像には影響しません。

## コントロールパネルからトランスフォームの定義

「設定 > アセット > 画像の変形」に移動し、「新しい画像変換」ボタンをクリックすることで、コントロールパネルからトランスフォームを定義することができます。

トランスフォームには、次の設定があります。

* **名前** – ユーザーに対する画像変形の名前
* **ハンドル** – テンプレートに対する画像変形のハンドル
* **モード** – 変換モード
* **幅** – 変換結果の幅
* **高さ** – 変換結果の高さ
* **品質** - 変換結果の画像品質（0 から 100）
* **画像フォーマット** – 変換結果の画像フォーマット

**モード** には、次の値をセットできます。

* **切り抜き** – 指定された幅と高さに画像を切り抜き、必要であれば画像を拡大します。（これがデフォルトのモードです。）
* **フィット**– すべての寸法が指定された幅と高さに収まる範囲で、可能な限り大きいサイズになるよう画像を拡大・縮小します。
* **ストレッチ** – 指定された幅と高さに画像を伸張します。

**モード** に「切り抜き」がセットされている場合、切り抜く際に画像のどのエリアを焦点として扱うかを定義できる「デフォルトの焦点」 プルダウンが表示されます。オプションには、次のものが含まれます。

* 上左
* 上中
* 上右
* 中央 - 左
* 中央 - 中央
* 中央 - 右
* 下部左
* 下部中央
* 下部右

**幅** または **高さ** のいずれかを空白のままにすると、その寸法は画像の縦横比を維持するようセットされます。例えば、600 x 400 ピクセルの画像があり、変形の幅を 60 に設定し、高さを空白のままにした場合、変形した画像の高さは 40 になります。

**品質** を空白のままにすると、Craft はコンフィグ設定の <config:defaultImageQuality> にセットされた品質を使用します。

**画像フォーマット** には、次の値をセットできます。

* jpg
* png
* gif

**画像フォーマット** を空欄のままにすると、Craft は web-safe（.jpg、 .png、または .gif） なら元画像のフォーマットを使い、そうでなければ、そのジョブに最適な画像フォーマットを見つけようと試みます。（おそらく、ImageMagik がインストールされていないために）それを決定できない場合は、.jpg として処理されます。

### CP で定義された画像の変形を適用する

トランスフォームを適用した画像を出力するには、トランスフォームのハンドルをアセットの [getUrl()](api:craft\elements\Asset::getUrl())、[getWidth()](api:craft\elements\Asset::getWidth())、および  [getHeight()](api:craft\elements\Asset::getHeight()) ファンクションに渡します。

```twig
<img src="{{ asset.getUrl('thumb') }}" width="{{ asset.getWidth('thumb') }}" height="{{ asset.getHeight('thumb') }}">
```

## テンプレート内でトランスフォームを定義する

テンプレート内で直接トランスフォームを定義することもできます。

はじめに、トランスフォームのパラメータを定義したオブジェクトを作成する必要があります。

```twig
{% set thumb = {
 mode: 'crop',
 width: 100,
 height: 100,
 quality: 75,
 position: 'top-center'
} %}
```

次に、そのオブジェクトをアセットモデルの `getUrl()`、`getWidth()`、および `getHeight()` ファンクションへ渡します。

```twig
<img src="{{ asset.getUrl(thumb) }}" width="{{ asset.getWidth(thumb) }}" height="{{ asset.getHeight(thumb) }}">
```

ここでは、最初の例のように「`thumb`」の周りに引用符がないことに注意してください。最初の例では、CP で定義されたトランスフォームのハンドルを _文字列_ として渡しているのに対して、この例ではテンプレート内で作成した ‘thumb’ オブジェクトを参照するための _変数_ として渡しています。

### 利用可能な値

CP で定義されたトランスフォームで利用可能なすべて設定は、同様にテンプレートで定義されたトランスフォームでも利用できます。

* `mode` プロパティには、`'crop'`、`'fit'`、または `'stretch'` をセットすることができます。
* `mode` に `'crop'` をセットした場合、`position` プロパティに `'top-left'`、`'top-center'`、 `'top-right'`、`'center-left'`、`'center-center'`、`'center-right'`、`'bottom-left'`、`'bottom-center'`、または `'bottom-right'` のいずれかをセットして渡すことができます。
* `width` と `height` は、整数をセットするか、省略できます。
* `quality` は、0 から 100 の間の数値をセットするか、省略できます。
* `format` には、`'jpg'`、`'gif'`、`'png'`、または `'Auto'` をセットできます。
