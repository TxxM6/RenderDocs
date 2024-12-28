## はじめに

フロントエンド(FE) において**レンダー**、**レンダリング**という言葉はさまざまな場面で登場します。さらに、SSR(Server-Side Rendering)のようにレンダリングに関連した用語も多く存在しており、それらの用語の意味もフロントエンドの技術の進歩に追従して、徐々にニュアンスのようなものが変化してきているように感じています。  

では、レンダリングとはそもそもどういう意味なのでしょうか。また SSR や SPA(Single-Page-Application)などといった用語は今日どういった意味で使われているのでしょう。  
SSR のレンダーは何を指して Rendering としているのでしょうか。
本記事ではレンダリングという言葉を中心に、フロントエンドの用語や概念について振り返っていきます。

:::message
- 本記事ではこの言葉の定義はこうであるべきだと強く主張または是正するような意図はありません。
- キャッシュ戦略やパフォーマンスなどの最適化観点の話は深くは扱いませんのでご了承ください。
:::

## 利用する用語の意味

| 名称                           | 意味                                                            |
| ------------------------------ | --------------------------------------------------------------- |
|**サーバーサイド**| Web クライアント（ブラウザなど）に対して、Node.js などのランタイムのこと。React のコードの実行、データのフェッチ、HTML ファイルの生成などの処理を担い、主に BFF（Backend for Frontend）として区別される層にことを指す。[^サーバーサイド]|
|**クライアントサイド**| ブラウザなどの Web クライアントのこと。|

[^サーバーサイド]:本記事ではサーバーサイドとビルド時の実行環境は区別しています。

## 要約

### レンダリングの意味

- **広義**
  - データを計算して変換すること
- **ブラウザにおいて**
  - Web コンテンツを画面上に描画すること
- **React において**
  - コンポーネントを呼び出す(実行する)こと
- **CSR(Client-Side Rendering)において**
  - クライアントサイドで DOM を更新すること
- **SSR(Server-Side Rendering)において**
  - HTML ファイルを生成すること
- **SSG(Static Site Generation)(別称: Pre-rendering)において**
  - HTML ファイルを生成すること

### レンダリングモデルに関連する用語の意味

- **CSR**
  - クライアントサイドで DOM を更新すること
- **SSR**
  - サーバーサイドで、毎ページリクエスト時に HTML ファイル(およびそのコンテンツ)を生成すること、あるいはそのレンダリングパターン
- **SSG**
  - ビルド時に HTML ファイル(およびそのコンテンツ)を生成すること、あるいはそのレンダリングパターン
- **SPA(Single-page application)**
  - ソフトナビゲーションで画面回遊するアプリケーション全般
  - サーバーサイドでレンダリングをしない CSR のみのアプリケーション

## 広義的なレンダリングの意味

**意味: データを計算して変換すること**

レンダリングという言葉は、Web技術に限らずコンピュータサイエンス全般で使用される用語です。この用語は、3DCG、動画、音声（作曲）など、さまざまなデータ処理の分野で使われています。

- 3DCG: モデルやシーンの 3D データを計算し、画像や映像として出力すること
- 動画: 各フレームを順番に描画し、最終的なビデオクリップを生成すること。
- 音声（作曲）: 複数の MIDI データやデジタルオーディオ信号を、音声ファイルデータ(mp3 など)として出力すること。

これらの例に共通しているのは、**データを計算して別の形式に変換する**という点です。ただし、この変換は単なるフォーマット変更ではなく、次の処理で解釈可能な形や、**人間にとって利用しやすい形式**に変えることを目的としています。

例えば、楽曲データの場合、最終的に人間が楽曲を再生することを目指して、音声ファイル形式への変換（レンダリング）が行われます。

フロントエンドの文脈では、最終的に視覚情報として画面上に要素を描画することが目的になるため、レンダリングとは画面に要素を表示するための一過程であると考えることができます。

## ブラウザにおいて(The Pixel Pipeline、Rendering Pipeline)

**レンダリングの意味: Web コンテンツを画面上に描画することと**

ブラウザの役割はコンテンツ(HTML など)を画面上に描画することです。この過程では、 CSS のスタイルを適用し、ラスタライズ(ピクセル化)を行い、最終的に OpenGL のような OS 標準のグラフィックライブラリに描画データを渡します。
この一連の処理は **_The Pixel Pipeline_** [^ThePixelPipeline]や、 **_Rendering Pipeline_**[^RenderingPipeline] などと呼ばれます。この処理には、ブラウザに搭載されているレンダリングエンジン(例: Blink)やグラフィックライブラリ(例: Skia)がなどが関与しています。chrome の具体例については、以下のスライドが処理の概略を示しています。[^LifeOfAPixel]

![](https://storage.googleapis.com/zenn-user-upload/abc28164dc39-20240919.jpg)

:::message
ウェブの特徴の一つとして、コンテンツが動的に更新される点が挙げられます。そのため、ブラウザは DOM を含むいくつかの中間表現を保持し、画面の更新を効率化しています。
:::

ブラウザのレンダリングの責務の範囲は、厳密には OS のグラフィックライブラリに HTML などを変換したものを渡すところまでです。しかし、多くの文脈において、ブラウザレンダリングとは **HTML、DOM を画面上に表示すること** と簡潔に捉えられることが一般的です。

[^ThePixelPipeline]: The Pixel Pipeline の引用元: https://web.dev/articles/rendering-performance
[^RenderingPipeline]: Rendering Pipeline の引用元(RenderingNG): https://developer.chrome.com/docs/chromium/renderingng-architecture?hl=ja
[^LifeOfAPixel]: Life of a Pixel の 79 枚目から引用: https://docs.google.com/presentation/d/1boPxbgNrTU0ddsc144rcXayGA_WF53k96imRH8Mp34Y/edit#slide=id.ga884fe665f_64_1800

## React において

**レンダーの意味: React がコンポーネントを呼び出すこと**

React の公式ドキュメントでも次のように説明されています。なお、「コンポーネントを呼び出す」という表現は、「実行する」と解釈しても大きな齟齬はありません(英語ではレンダーではなく Rendering となっています。[^ReactRendering]) 。

> “Rendering” is React calling your components.
> レンダーとは React がコンポーネントを呼び出すことです


https://ja.react.dev/learn/render-and-commit

React ではレンダーがトリガーされた後、以下の 3 つのフェーズを経て、要素が画面に描画または再描画されます。

| 名称                           | 意味                                                            |
| ------------------------------ | --------------------------------------------------------------- |
| レンダーフェーズ               | コンポーネントの呼び出しが行われる                              |
| コミットフェーズ               | レンダー間で差分があった場合、 変更すべき DOM ノード を変更する |
| ペイント(ブラウザレンダリング) | ブラウザが画面を描画する。(React の責務ではない)                |

つまり、React ではレンダーされたとしても、仮想 DOM(ReactTree)上で差分がなければ DOM が更新されず、画面には反映されない点です。また、ブラウザの章で述べたブラウザレンダリングは、Reactでは混乱を避けるために ペイント(Paint) と呼ばれています。

今回の解説では上記の 3 つのフェーズで説明していますが、より細かい実装が気になる方は下記のような他の記事を合わせて参考にしていただければと思います。

https://zenn.dev/aishift/articles/d046335a98bc34

[^ReactRendering]: https://react.dev/learn/render-and-commit

## CSR(Client-Side Rendering)において

**意味: クライアントサイドで DOM を更新すること**

用語そのものの意味と同様、CSRにおける「レンダリング」とは　**クライアントサイドで DOM を更新すること** と言えます。

このプロセスにより、ページのコンテンツがクライアント側で生成されるため、Client-Side Rendering（クライアントサイドレンダリング）と呼ばれます。CSRという用語は、初期表示と画面回遊（ナビゲーション）の両場面で使用されます。

CSR が登場する以前のWebアプリケーションでは、Web サーバーから送られてくる HTML ファイルに表示するコンテンツがあらかじめ含まれていました。**画面遷移時には新しい HTML ファイルがWebサーバーから送られ、画面全体が書き換わる仕組み**でした(MPA[^MPA])。

一方で、CSROnly[^CSROnly]のアプリケーションでは、初回訪問時にまず**コンテンツが空の HTML** が送られてきます。コンテンツが画面上に反映されるのは、JavaScript や JSON データをサーバーから取得した後で**JavaScript がコンテンツを生成し、 DOM を更新します**。
ナビゲーションにおいても、**JavaScriptが history や DOM を操作して画面遷移を実現します**。この際、Web サーバーから送られるのは HTML ファイルではなく主に JSON データです。

なお、初回訪問時に空の HTML を返すアプリケーション(SPA[^SPA] ≒ CSROnly)の欠点の一つに、SEO 対策が困難であるという点がありました。（ほかにもパフォーマンスやデータ転送の効率といった課題があります）。

[^MPA]:MPA: Multi-page application。画面遷移時に新しい HTML ファイルが送られ、画面全体が書き換わるようなアプリケーションのこと。

[^CSROnly]:CSROnly: サイト訪問時も画面遷移時もCSRで行うような、クライアントサイドでのみ完全に動作するアプリケーション。サーバーサイドランタイムを持たず、ビルド時にコンポーネントをレンダーしてコンテンツを含むHTMLを生成するわけではない。本記事で述べている、SPAの用例うちの一つに該当する。

[^SPA]:SPA: ここでは、サーバーサイドでレスポンスが返却される前に事前にレンダリングを全く行わない CSR のみのアプリケーションのこと

:::message
Next.js では、CSR による遷移は **ソフトナビゲーション(Soft Navigation)** と呼ばれています。またソフトナビゲーションとは対照に Web サーバから送信された HTML ファイルに置き換えるような MPA 的な遷移は**ハードナビゲーション(Hard Navigation)** と言います。
:::

## SSR(Server-Side Rendering)において

**用語の意味: サーバーサイドで、毎ページリクエスト時に HTML ファイル(およびそのコンテンツ)を生成すること、あるいはそのレンダリングパターン**

レンダリング結果として得られるものは、HTML ファイル(およびそのコンテンツ)であるため、SSRにおける「レンダリング」とは　**HTML ファイルを生成すること** と言えます。
SSR をする主な目的は以下の二つになります。

1. 動的サイト[^動的サイト]を作ること
2. コンテンツを含む HTML ファイルを作ること

SSR という言葉は CSR(SPA)の登場と同時にレトロ二ム[^レトロニム]として生まれたものです。しかし現在では、CSROnly[^CSROnly]では解決できない課題を補う手法として認識されています(つまり、SSRするアプリケーションではCSRもします。)。
現代のSSRを行うフレームワークにおいて、サーバーサイドでHTMLファイルを生成するのは初回表示時(サイト訪問時)であり、その後サイト内を回遊する際にはソフトナビゲーション(CSR)をします。[^注意書き-SSR]
このようなレンダリングパターンは、元々 SSR with Hydration と呼ばれていたものであり、現在はそれを単に SSR と呼ぶようになりました。この章では、それらの変遷について述べていきます。

:::message
サーバーサイドで生成できる HTML ファイルは静的なものにすぎないため、ブラウザ上でも再度コードを実行してページをインタラクティブにする ハイドレーション(Hydration) という工程が必要になります。
:::

:::message
(SSR の意味として、ラフにコンポーネントをサーバーサイドでレンダーすることを SSR という使われ方がされることもあります。)
:::

[^注意書き-SSR]: 当記事では、基本は CSR で画面遷移をしつつ、ページ訪問時には HTML をオンデマンドにサーバーサイドで生成するようなアプリケーションのことを SSR あるいは SSR(SSR with hydration) であるとしていますが、例えば、そこから敢えてソフトナビゲーションを封印した場合や、CSR が登場する前のような MPA はなんと呼ぶのかを取り扱うと議論が複雑化してしまうため触れません。


[^動的サイト]:動的サイト: アクセスしたユーザーの属性や条件に応じて、表示内容を動的に生成して変化させるWebサイト。対義語の静的サイトは、「ユーザーや時間帯などによらず同じ内容を表示させるWebサイト」という意味。

### SSR という言葉の変遷

#### 初期の SSR の意味

**意味: サーバーサイドで HTML ファイルを生成すること**

もともと SSR という言葉は、SPA(Single-page application)が台頭してきたときに、CSR(Client-Side Rendering)のレトロニム[^レトロニム]として使われ始めた言葉のようです。
この頃の SSR は、サーバーサイドのテンプレートエンジン(Java,PHP,Ruby on Rails 等)で HTML ファイルを生成する従来の方法を指していました。

当時はCSR のようにサイト訪問時や画面遷移時にクライアントサイドで DOM を直接書き換えてコンテンツを作る方法は一般的ではなく、従来の方法ではサーバーサイドでコンテンツを含んだ HTML ファイルを配信して、ブラウザでは HTML ファイルごと入れ替えるような形で画面を書き換えていました。

[^レトロニム]: レトロニム: 時代の変化により新しい事物が生まれたことから、既存の事物を新しいものと区別するため「後から」つくられた言葉

#### SSR with Hydration の出現

現在のような SSR フレームワークの起源の一つであると考えられるものとして Airbnb 社の Backbone.js のフレームワーク、**Rendr** があります。
Backbone.js は、クライアントサイドの MVC モデルを採用した、古典的な SPA フレームワークの一つです。

当時の CSROnly のアプリケーションでは、ページ訪問時にコンテンツが空の HTML ファイルが最初に送られるため、**SEO**において課題がありました。

その対策のために、サイト訪問時用に 別言語(Ruby など)で開発したサーバーを用意したり、ヘッドレスブラウザのようなものを挟み JavaScript を事前に実行するなどをして、 コンテンツを含む HTML を生成していました。これがいわゆる **ダイナミックレンダリング** と呼ばれる手法です。

(ダイナミックレンダリング)
https://developers.google.com/search/docs/crawling-indexing/JavaScript/dynamic-rendering?hl=ja

(JavaScript を実行して HTML を生成してくれるサービス)
https://prerender.io/

そこで Rendr が採用したアプローチは、Node.js を活用することで、それまで Ruby と JavaScript で重複していたロジックを一本化し、クライアントサイドとサーバーサイドの双方で実行可能な共通コードを実現することでした。

:::message
当時は**同じコード**がサーバーサイドでもクライアントサイドでも実行されることを **Isomorphic**と呼びました。現在でもサーバーサイド、クライアントサイドの両側で実行される概念に対して Isomorphic という言葉が使われることがあります。[参考: Isomorphic Loaders](https://frontendmasters.com/blog/introducing-tanstack-start/#isomorphic-loaders)
:::


なお、Rendr の作者の Rendr の公開スライドに、すでに "Hydrate" という用語が登場しており、クライアントサイドで要素をインタラクティブにするフェーズを "Hydrate" と呼ぶ文化が、この時点で存在していたことが伺えます。

以下のRenderの記事は、React が公開される 2013 年 5 月以前に書かれたものであり、現在のような SSR フレームワークの形態がその時点ですでに完成していたことを示唆しています。しかし、Rendr は React の普及によって主流となることはなく、Airbnb も最終的に React へ移行したようです。

https://medium.com/airbnb-engineering/our-first-node-js-app-backbone-on-the-client-and-server-c659abb0e2b4

https://www.slideshare.net/slideshow/introducing-rendr-run-your-backbonejs-apps-on-the-client-and-server/19106546

このような、サーバーサイドとクライアントサイドで共通のコードを実行するタイプの SSR は、従来型の SSR と区別するため、**SSR with Hydration** と呼ばれることもありました。


#### 現在の SSR

**用語の意味(再掲): サーバーサイドで、毎ページリクエスト時に HTML ファイル(およびそのコンテンツ)を生成すること、あるいはそのレンダリングパターン**

現在では、フロントエンド開発において React のような SPA の技術が普及し、さらには Next.js や Remix といった サーバーサイドでも JavaScript/React のコードが動作するフレームワークが主流となりました。これらのフレームワークはサーバーサイドにランタイムを設けることで、SPA の利点を享受しつつ、従来のSPAでは解決が難しかった課題の克服や最適化を実現しようとしています。

近年 SSR が言及される文脈は、主にこれらのフレームワークに関連する場合が多く、SPA登場当初とは想定場面などの前提が変化したため、初期に SSR with Hydration と呼ばれていたものが、現在では単に SSR と呼ばれるようになってきています。

もともと SSR という言葉が誕生したのは、CSR（SPA）技術が台頭し、サーバーサイドで行われていたコンテンツ生成がクライアントサイドへ移行した時期でした。
しかし、現代におけるSSRという用語は、**本来クライアントサイドで実行されるコード**を、**サイト訪問時に限りサーバーサイドでレンダリングする**という、方向性の逆転が起きた後の意味合いを持つようになったと解釈できそうです。

:::message
Astro と SSR
ここまでSSRという用語が基本的にソフトナビゲーションなどの概念とセットになるかのように述べてきました。
MPA[^MPA] のフレームワークである Astro でも SSR は [On-demand Rendering](https://docs.astro.ビルド/en/guides/on-demand-rendering/) という機能として存在しており、ページリクエストに応じてサーバーサイドで HTML を生成する仕組みを指します。 
Astro はもともと、Static Site Generation (SSG) に特化したフレームワークとして登場し、SPA に対抗し、従来の MPA のようにページ遷移を行いながらも、JavaScript 以外の言語との二重開発が不要であることを利点としています。
SSG の静的ページのみであれば、ビルド時に生成した静的 HTML を配信し、外部 API はブラウザで処理できるため、Node.js 等のランタイムは不要ですが、On-demand Rendering が追加され、本来持たなかったサーバーサイドランタイムを持つという手段をとることで、これまでクライアントサイドで行っていた動的なコンテンツ生成 (CSR) をサーバーサイドで行い、コンテンツを含む HTML ファイルを配信できるようになりました。
よって Astro の On-demand Rendering が解決した問題も 動的サイトをサーバーサイドから直接配信したいという点で、Next.Js などの SSR with Hydration が解決したかった課題と全く違った方向を向いているわけではないと筆者は考えています。　
:::


### RSC の ServerComponents は SSR なのか？

RSC（React Server Components）の登場以前は、コンポーネントは基本的にすべてクライアントサイドでレンダーされ、SSRや SSG ではサーバーサイド または ビルド時にも実行されていました。しかし、RSC では従来通りの **Client Components(CC)**に加え、完全にサーバーサイド または ビルド時のみ実行される **Server Components(SC)**が導入されました。

では、**SC は SSR と同じ概念なのか？** という疑問については、React 公式は、併用できる別の概念であると説明しています。
SSR はサーバーサイドで HTML を事前生成する技術を指し、SC のレンダリングをそのまま SSR と呼ぶわけではありません。

React RFC によると次のように述べられています：

https://github.com/reactjs/rfcs/blob/main/text/0188-server-components.md#does-this-replace-ssr

> Does this replace SSR?
> No, they’re complementary. SSR is primarily a technique to quickly display a non-interactive version of client components. You still need to pay the cost of downloading, parsing, and executing those Client Components after the initial HTML is loaded.
> You can combine Server Components and SSR, where Server Components render first, with Client Components rendering into HTML for fast non-interactive display while they are hydrated. When combined in this way you still get fast startup, but you also dramatically reduce the amount of JS that needs to be downloaded on the client.

> (DeepL 翻訳)
> これは SSR を置き換えるものですか？
> いいえ。SSR は主にクライアントコンポーネントの非インタラクティブバージョンを素早く表示するテクニックです。最初の HTML が読み込まれた後、それらのクライアントコンポーネントをダウンロードし、解析し、実行するコストはまだ必要です。
> サーバコンポーネントと SSR を組み合わせることができます。 サーバコンポーネントが最初にレンダリングされ、クライアントコンポーネントは HTML にレンダリングされます。このように組み合わせることで、高速なスタートアップが得られるだけでなく、 クライアントにダウンロードされる JS の量を劇的に減らすことができます。

:::message
RSC の SC は、RSC 以前のコンポーネント(つまり CC)と異なり、SSR 時にもハイドレーションされず、ソフトナビゲーション時もクライアントサイドでレンダーされません。

第一の理由として、SCはHooksやイベントハンドラなどの**インタラクティブ**な要素を持たないため、クライアントサイドでの状態管理や更新が不要となります。これにより、サーバーサイドやビルド時にCSのレンダリングを完結させることができ、その結果はクライアントサイドの仮想DOM（React Tree）にそのまま適用されます。

第二の理由として、**データ取得方法の変化**が挙げられます。従来のSSRでは、初期表示に必要なデータはフレームワークが提供するデータ取得関数（Next.jsのgetServerSidePropsやRemixのloader関数など）を用いて、コンポーネントの外側で取得されていました。このデータ取得はソフトナビゲーション時にもサーバーサイドで実行され、取得したデータをPropsとしてコンポーネントに渡してレンダリングする、いわゆる「Fetch-then-render」という方式が用いられていました。

**従来: フレームワークによるデータ取得 → コンポーネントのレンダリング**

一方で RSC の場合、SC が非同期コンポーネントとなり、SC 自身がデータ取得を行うことができるようになりました。

これらの理由により、SCはデータ取得からレンダリングまでのプロセスをサーバーサイドやビルド時に完了させることが可能となり、従来のFetch-then-renderとは異なるアプローチを実現しています。  
以上が、RSCにおいてSSRおよびソフトナビゲーション時のコンポーネントのレンダーが行われる場所に変化が生じた理由になります。
:::

## SSG(Static Site Generation)(別称: Pre-rendering)

**用語の意味: ビルド時に HTML ファイル(およびそのコンテンツ)を生成すること、あるいはそのレンダリングパターン**

Static Site Generatorsとも。海外では、SSGは単にPre-renderingとも呼ばれてきました。

SSGでは、ビルド時にフレームワークによるデータフェッチやコンポーネントのレンダーが行われ、HTML(コンテンツ)が生成されます。よってSSR同様、SSGにおける「レンダリング」とは　**HTML ファイルを生成すること** と言えます。

静的なHTML(コンテンツ)を取り扱うことになるため、CDNなどで比較的容易にキャッシュができるといった利点が挙げられます。また、SSGという用語自体は、ハイドレーションの有無に限らず広く使われているようです[^stateOfJs2024]。
  

### 事前レンダリング
Pre-renderingという呼び方をそのまま日本語に直すと **事前レンダリング** となりますが、この言葉はSSRやSSGをまとめて指す言葉として少なからず使われてきました。クライアントサイドにレスポンスが返却されるよりも前の段階でレンダリングされるという意味合いであると捉えることができそうです。
しかし、近年 Next.jsやRemix の機能などで、Pre-renderingという用語は**ビルド時にレンダリングする意味**で明確に使われているように、海外でのPre-renderingではSSRを包含した意味はないようです。この場合の「事前」は**リクエスト時よりも前**、つまりビルド時を指していると解釈できます。
  
:::message
SSG、SSRのアプリケーションでソフトナビゲーションをする際は、Webサーバーからは初回表示とは異なりHTMLファイルではなくjsonデータなどが配信されます。よってSSGの場合はビルド時に初回表示用のHTMLとCSR用のファイルの両方が生成されることになります。
:::

:::message
ServerComponents(SC)のドキュメントに、[サーバを利用しないサーバーコンポーネント](https://ja.react.dev/reference/rsc/server-components#server-components-without-a-server) という一見意味が分かりにくい段落が存在しますが、ここにおけるサーバーはウェブサーバ(BFFなどに該当するサーバーサイドのランタイム)のことを指しており、SSGのようにビルド時にSCをレンダーすることを指しています。(つまり、レンダー前のSCのコードがブラウザに渡って、独立してレンダーするようになるわけではありません。)
:::

[^stateOfJs2024]: [state of js 2024](https://survey.devographics.com/en-US/survey/state-of-js/2024/outline/9)ではSSGは、*Static content pre-rendered at ビルド time, with or without a client-side dynamic element*　とされています。


## SPA(Single-page application)

CSR、SSR と関連して、場面や話者によって意味合いが大きく変わる用語にSPA(Single-page application) があります。筆者の観測では、この用語は大きく分けて二つの意味で使用されているように見受けられます。

1. **ソフトナビゲーションで画面回遊するアプリケーション全般(SSR/SSG と共存する概念)**
2. **サーバーサイドでレンダリングをしない CSR のみのアプリケーション(SSR/SSG と排他的関係の概念)**

1の意味でのSPAは、初回アクセス時を除き、**HTMLファイルをサーバーから送信しないアプリケーション**を指します。ページ遷移はCSR、つまりJavaScriptによって動的に行われ、あたかも単一のページ内で動作しているかのように見えます。これは、ページ遷移ごとにHTMLファイルをサーバーから取得する**MPA（Multi-Page Application）との対比**として理解できます。また、サーバーサイドランタイムの有無について定義されておらず、SSR（Server-Side Rendering）やSSG（Static Site Generation）と組み合わせても成り立つ概念になっています。

2の意味でのSPAは、事前にレンダリングをしない、つまりサーバーサイドランタイムをもたない **完全にブラウザ上でのみ動作する CSR のみのアプリケーション**ということになります。この用例は海外で特に見られる傾向にあり、例えば State of JavaScript の [^8]2022 年から[^9]今年度に至るまでのアンケートでの SPA の定義や [Remix の SPA モード](https://remix-docs-ja.techtalk.jp/guides/spa-mode/)という名前の機能の意味では、 SSR/SSG と横並びになるようなものとなっています。

> SPA: Apps that run entirely in the browser
> MPA: Apps that run entirely in the server, with minimal client-side dynamic behavior

[^8]:(State of JavaScript 2022) https://survey.devographics.com/en-US/survey/state-of-js/2022/outline/11
[^9]:(State of JavaScript 2024)https://survey.devographics.com/en-US/survey/state-of-js/2024/outline/9

### SPA、SSR、SSG
SPAの意味を  **サーバーサイドでレンダリングをしない CSR のみのアプリケーション** の方の意味で捉えた場合、SSR、SSGとの差異は、サイト訪問時に配信するコンテンツ(HTML)のレンダリング方法の違いになります。


| 名称 | サイト訪問時用のコンテンツのレンダリング方法                                                                                                |
| ---- | --------------------------------------------------------------------------------------------------- |
| SPA  | ブラウザ上のみ(事前にレンダリングしない)                       |
| SSR  | 毎アクセス時にサーバーサイドでレンダリングする |
| SSG  | ビルド時にレンダリングする     |

:::message alert
Reactなどの場合、SSR、SSGではブラウザ上でもコンポーネントのレンダー(ハイドレーション)が必要です。
:::


## PPR(Partial Prerendering)

:::message　alert
Partial Prerendering はNext.js v15.1.2時点で実験的な機能で、canary でのみ利用可能であり、PPRがstableな機能として提供される際には機能の一部が変更されている可能性がありますので、ご注意下さい
:::

**用語の意味: 同一のルート(ページ)内でビルド時/毎リクエスト時 にサーバーサイドでレンダーされるコンポーネントを組み合わせるレンダリングモデル**

PPRはNext.jsの提供する実験的な機能であり、新たなレンダリングモデルです。そしてレンダリングの焦点はサーバーサイド(ビルド時も含む)にあります。

PPRを文字通り直訳すると部分的(Partial)な事前レンダリング(Prerendering ≒ SSG)となりますが、公式のドキュメントでは、staticなコンポーネントとdynamicなコンポーネントを同一ルート(ページ)内に共存させることを実現する機能であると説明されています。

この「static」「dynamic」という言葉は公式のドキュメントで使用されており、意味は以下のように解釈できます。
- static: ビルド時にレンダリングされるコンポーネントを指し、従来の SSG (Static Site Generation) に相当する。
- dynamic: リクエストごとに（サーバーランタイムで）レンダリングされるコンポーネントを指し、従来の SSR (Server-Side Rendering) に相当する。

従来のアプリケーションでは、アプリケーション全体あるいはページごとに、static/dynamicなレンダリングをすることを選択する必要がありました。つまり、ページ全体を SSG あるいは SSR で生成するかを選択していました。

> For most web apps built today, you either choose between static and dynamic rendering for your entire application, or for a specific route.

> (DeepL 翻訳)
> 現在構築されているほとんどのWebアプリケーションでは、アプリケーション全体または特定のルートに対して、静的レンダリングと動的レンダリングのどちらかを選択します。

https://nextjs.org/learn/dashboard-app/partial-prerendering#static-vs-dynamic-routes

しかし、PPRにより、ページ内でもより細かくコンポーネントのレンダーの仕方を設定できるようになりました。これにより、ページの一部はビルド時に生成し、残りの部分はリクエストごとに生成するという柔軟な構成が可能になります。 PPRについてより詳しい解説が気になる方は以下の記事などを参考にしてください。

https://zenn.dev/akfm/articles/nextjs-partial-pre-rendering


:::message
ページ内でレンダリング方法を細かく設定できる点で、アイランドアーキテクチャも似たような概念なのではないかと疑問に思う方がいらっしゃるかもしれませんが、筆者はどちらかというとアイランドアーキテクチャはRSCのほうが近い概念だと認識しています。アイランドアーキテクチャとPPRの関係について気になる方はakfm_sato 氏の[『PPRはアイランドアーキテクチャなのか』](https://zenn.dev/morinokami/articles/astro-server-islands-vs-nextjs-ppr)の記事を参考にしてください。また、PPRと似た概念との比較記事としては、Shinya Fujino氏の [『Next.js PPR と比較して理解する Astro Server Islands』](https://zenn.dev/morinokami/articles/astro-server-islands-vs-nextjs-ppr)でAstro v5の Server Islandsと比較されており参考になると思います。
:::


## まとめ

今回はレンダリングという言葉を中心にフロントエンドの用語と概念を解説しました。冒頭に述べた通り、本記事では特定の定義を押し付ける意図はなく、これらの用語は、人や時代背景によって意味が変化しやすいものであると感じています。内容についてご意見であったり感想がございましたら、お気軽にご連絡ください。
本記事がレンダリングという言葉を通して、皆様のフロントエンドへの理解のお役に立てれば幸いです。

## 謝辞

本記事の作成にあたり、壁打ちやレビュー、解説等のご協力をいただいた皆様に深く感謝申し上げます。
