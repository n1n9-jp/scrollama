<img src="https://russellsamora.github.io/scrollama/logo.png" width="160" alt="scrollama.js"/>

**Scrollama** は、スクロール・イベントに優先して [IntersectionObserver](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API) を使用する、スクローリーテリング用の最新の軽量 JavaScript ライブラリです。 *現在のバージョン: 3.2.0*

## 3.0
#### Why 3.0?
The core parts of the Scrollama code base are being refactored for 3.0 to simplfy and clarify the logic. The goal behind this to ease make future maintainance, bug fixing, and feature additions easier moving forward.

#### New Fetaures
* Built-in resize using resize observers.
* Custom offsets on steps with data attributes

#### Deprecated Features
* the `order` option

## Important Changes
- **Version 3.0.0+**: `order` has been deprecated.
- **Version 2.0.0+**: `.onContainerEnter` and `.onContainerExit` have been deprecated in favor of CSS property `position: sticky;`. [How to use position sticky.](https://pudding.cool/process/scrollytelling-sticky/)
- **Version 1.4.0+**: you must manually add the IntersectionObserver polyfill for cross-browser support. See [installation](https://github.com/russellsamora/scrollama#installation) for details.

[Jump to examples.](https://github.com/russellsamora/scrollama#examples)

## なぜ?

スクロールテリングは実装が複雑で、パフォーマンスを上げるのが難しい場合があります。
このライブラリの目標は、スクロール駆動型のインタラクティブを作成するためのシンプルなインターフェースを提供することです。Scrollama は、要素の位置検出を処理するために [IntersectionObserver](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API)
を使用することで、パフォーマンスに重点を置いています。

[![scrollytelling pattern](https://thumbs.gfycat.com/FearfulHotArabianoryx-size_restricted.gif)](https://pudding.cool/process/how-to-implement-scrollytelling)

## 事例

_注: これらの例のほとんどは、コードを簡潔にするために D3 を使用していますが、任意のライブラリ、またはライブラリなしでも使用できます。_

- [Basic](https://russellsamora.github.io/scrollama/basic) - just step
  triggers
- [Progress](https://russellsamora.github.io/scrollama/progress) -
  incremental step progress callback
- [Sticky Graphic (Side by Side)](https://russellsamora.github.io/scrollama/sticky-side) -
  using CSS `position: sticky;` to create a fixed graphic to the side of the text.
- [Sticky Graphic (Overlay)](https://russellsamora.github.io/scrollama/sticky-overlay) -
  using CSS `position: sticky;` to create a fixed graphic with fullscreen graphic with text overlayed.
- [Custom Offset](https://russellsamora.github.io/scrollama/custom-offset) -
  Adding a data attribute to an element to provide a unique offset for a step.
- [Mobile Pattern](https://russellsamora.github.io/scrollama/mobile-pattern) -
  using pixels instead of percent for offset value so it doesn't jump around on scroll direction change
- [iframe Embed](https://russellsamora.github.io/scrollama/iframe) -
  Embedding a Scrollama instance inside an iframe using `root` option

## インストール
**注: バージョン 1.4.0 以降、IntersectionObserver ポリフィルはビルドから削除されました。クロスブラウザ サポートのためには、自分でポリフィルを含める必要があります。** [こちら](https://caniuse.com/#feat=intersectionobserver) をチェックして、ポリフィルを含める必要があるかどうかを確認してください。

旧式 (`scrollama` グローバルを公開):

```html
<script src="https://unpkg.com/scrollama"></script>
```

新式:

```sh
npm install scrollama intersection-observer --save
```

そして import/require します:

```js
import scrollama from "scrollama"; // or...
const scrollama = require("scrollama");
```

## 使い方

#### 基本

このライブラリを使用すると、単にステップをトリガーできます。[Waypoints](http://imakewebthings.com/waypoints/) のようなものに似ています。これは、インタラクティブをさらに制御する必要がある場合、または固定スクロール テリング パターンに従いたくない場合に便利です。

任意の ID/クラス命名規則を使用できます。HTML 構造は次のようになります:

```html
<!--you don't need the "data-step" attr, but can be useful for storing instructions for JS -->
<div class="step" data-step="a"></div>
<div class="step" data-step="b"></div>
<div class="step" data-step="c"></div>
```

```js
// instantiate the scrollama
const scroller = scrollama();

// setup the instance, pass callback functions
scroller
  .setup({
    step: ".step",
  })
  .onStepEnter((response) => {
    // { element, index, direction }
  })
  .onStepExit((response) => {
    // { element, index, direction }
  });
```

## API

#### scrollama.setup([options])

_options:_

| オプション | タイプ | 説明 | デフォルト値 |
| --- | --- | --- | --- |
| step | string or HTMLElement[] | **required**  変更をトリガーするステップ要素のセレクター (または要素の配列) |
| offset | number (0 - 1, or string with "px") | ビューポートの上部からステップをトリガーする距離。 | 0.5 |
| progress | boolean | 増分ステップの進行状況更新を実行するかどうか。 | false |
| threshold | number (1 or higher) | ピクセル単位の進行間隔の粒度 (小さいほど粒度が細かくなります)。 | 4 |
| once | boolean | ステップを 1 回だけトリガーして、その後リスナーを削除します。 | false || 
| debug | boolean | ビジュアル デバッグ ツールを表示するかどうか。 | false |
| parent | HTMLElement[] | ステップセレクターの親要素（ステップが Shadow DOM 内にある場合に使用）。 | undefined |
| container | HTMLElement | スクロール ストーリーの親要素 (scrollama が、overflow が `scroll` または `auto` に設定された HTML 要素にネストされている場合に使用します) | undefined |
| root | HTMLElement | ターゲットの可視性をチェックするためのビューポートとして使用される要素。ターゲットの祖先である必要があります。指定されていない場合、または null の場合は、デフォルトでブラウザーのビューポートになります。ルートの使用法の詳細については、[MDN](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API#intersection_observer_concepts_and_usage) を参照してください。 | undefined |

#### scrollama.onStepEnter(callback)

ステップ要素の上端または下端がオフセットしきい値に入ったときに発生するコールバック。

コールバックの引数はオブジェクトです: `{ element: DOMElement, index: number, direction: string }`

`element`: トリガーされたステップ要素

`index`: そのステップのインデックス

`direction`: 'up' か 'down'

#### scrollama.onStepExit(callback)

ステップ要素の上端または下端がオフセットしきい値を出たときに発生するコールバック。

コールバックの引数はオブジェクトです: `{ element: DOMElement, index: number, direction: string }`

`element`: トリガーされたステップ要素

`index`: そのステップのインデックス

`direction`: 'up' か 'down'

#### scrollama.onStepProgress(callback)

しきい値を超えたステップの進行状況 (0 - 1) を起動するコールバック。

コールバックの引数はオブジェクトです: `{ element: DOMElement, index: number, progress: number }`

`element`: トリガーされたステップ要素

`index`: そのステップのインデックス

`progress`: ステップの完了率 (0 - 1)

`direction`: 'up' か 'down'

#### scrollama.offsetTrigger([number or string])

オフセットのパーセンテージを取得または設定します。値は 0 ～ 1 (0 = ビューポートの上部、1 = 下部) または「px」を含む文字列 (例:「200px」) である必要があります。設定されている場合、scrollama インスタンスを返します。

#### scrollama.resize()

**組み込みのサイズ変更オブザーバーが追加されたため、これは不要になりました**。ブラウザ/DOM の最新の寸法を取得するように scrollama に指示します。コード内でサイズ変更を抑制し、DOM 要素を更新してから、最後にこの関数を呼び出すのがベスト プラクティスです。

#### scrollama.enable()

scrollama にトリガーの変更の監視を再開するように指示します。以前に無効にしていた場合にのみ呼び出す必要があります。

#### scrollama.disable()

scrollama にトリガーの変更の監視を停止するように指示します。

#### scrollama.destroy()

すべてのオブザーバーとコールバック関数を削除します。

#### custom offset

オプションで渡されたオフセットをオーバーライドするには、データ属性を使用して個々の要素にカスタム オフセットを設定します。例: `<div class="step" data-offset="0.25">` または `data-offset="100px"`。

## スクローラマの非公式利用例
* [The Billionaire Playbook - ProPublica](https://www.propublica.org/article/the-billionaire-playbook-how-sports-owners-use-their-teams-to-avoid-millions-in-taxes)
* [Women's Pockets are Inferior - The Pudding](https://pudding.cool/2018/08/pockets/)
* [Trump approval rating - Politico](https://www.politico.com/interactives/2019/trump-approval-rating-polls/)
* [How the opioid epidemic evolved - Washington Post](https://www.washingtonpost.com/graphics/2019/investigations/opioid-pills-overdose-analysis/)
* [US Covid-19 deaths, explained in 8 charts and maps - Vox](https://www.vox.com/22252693/covid-19-deaths-us-who-died)
* [Life After Death on Wikipedia - The Pudding](https://pudding.cool/2018/08/wiki-death/)
* [YouTube With Me - YouTube](https://youtube.com/trends/articles/with-me-interactive/)
* [Unchecked Power - ProPublica](https://projects.propublica.org/nypd-unchecked-power/) 
* [Trump's environmental policies rule only part of America - Politico](https://www.politico.com/interactives/2018/trump-environmental-policies-rollbacks/)
* [The story of New Zealand’s Covid-19 lockdown, in graphs - Stuff](https://interactives.stuff.co.nz/2020/05/coronavirus-covid-19-data-new-zealand/)
* [Trump and Biden's Paths to Victory in the 2020 Election - Wall Street Journal](https://www.wsj.com/graphics/the-paths-to-victory/)
* [The sicence of superspreading - Science](https://vis.sciencemag.org/covid-clusters/)
* [El dominio histórico de la derecha en Madrid - elDiario.es](https://www.eldiario.es/madrid/gana-derecha-elecciones-madrid-mayoritaria-30-rico_1_7347696.html)
* [The Permutation Test - Jared Wilber](https://www.jwilber.me/permutationtest/)
* [Constellations - Nadieh Bremer](https://nbremer.github.io/planet-constellations/)
* [Remote Triggering of Earthquakes - Will Chase](https://www.williamrchase.com/vizrisk/vizrisk_main/)
* [Scrollytelling - Mapbox](https://demos.mapbox.com/scrollytelling/)

## ティップス
- CSS で `viewport height` (vh) を使用しないでください。上下にスクロールすると vh が頻繁に変更され、ウィンドウのサイズ変更もトリガーされます。

## Alternatives
- [Scroll Trigger](https://greensock.com/scrolltrigger/)
- [Waypoints](http://imakewebthings.com/waypoints/)
- [ScrollMagic](http://scrollmagic.io/)
- [graph-scroll.js](https://1wheel.github.io/graph-scroll/)
- [ScrollStory](https://sjwilliams.github.io/scrollstory/)
- [enter-view](https://github.com/russellsamora/enter-view)

## Logo

Logo by the awesome [Elaina Natario](https://twitter.com/elainanatario)

## License

MIT License

Copyright (c) 2022 Russell Samora

Permission is hereby granted, free of charge, to any person obtaining a copy of
this software and associated documentation files (the "Software"), to deal in
the Software without restriction, including without limitation the rights to
use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of
the Software, and to permit persons to whom the Software is furnished to do so,
subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS
FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR
COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER
IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN
CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
