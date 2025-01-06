## はじめに

フロントエンド開発 において**レンダリング**という言葉はさまざまな場面で登場します。さらに、Server-Side Rendering(SSR)のようにレンダリングという言葉が名前に含まれているものを含め、Single-page application(SPA)など、レンダリングに関連した様々な用語が多く存在しており、それらの意味はフロントエンドの技術の進化に伴い、徐々にニュアンスが変化してきているように感じています。  
実際、同僚とこれらの用語について話す中で、「自分はそれをSSRとは呼ばない」「SPAにはいくつかの意味があるよね」といった意見を耳にし、人によって解釈が異なることを実感しました。

では SSR や SPAなどといった用語は今日どういった意味で使われているのでしょう。そもそも、レンダリングとはそもそもどういう意味なのでしょうか。Server-Side Rendering における「レンダリング」とは、何を指しているのでしょうか。

本記事では「レンダリング」という言葉を中心に、フロントエンドの用語や概念について振り返ります。

:::message
- 本記事ではこの言葉の定義はこうであるべきだと強く主張または是正するような意図はありません。
- キャッシュ戦略やパフォーマンスなどの最適化観点の話は深くは扱いませんのでご了承ください。
:::

## 利用する用語の意味

| 名称                           | 意味                                                            |
| ------------------------------ | --------------------------------------------------------------- |
|**サーバーサイド**| Web クライアント（ブラウザなど）に対して、Node.js などのランタイムのこと。React のコードの実行、データのフェッチ、HTML レスポンスの生成などの処理を担い、主に BFF（Backend for Frontend）として区別される層にことを指す。[^サーバーサイド]|
|**クライアントサイド**| ブラウザなどの Web クライアントのこと。|
|**ソフトナビゲーション(Soft Navigation)**|ブラウザのJavaScriptを利用して、URL、Historyを直接書き換えながら、DOMを操作して画面を更新するナビゲーション（画面遷移）のことを指す。SPA 的な画面遷移 などとも呼ばれる。[^ソフトナビゲーション]|
|**ハードナビゲーション(Hard Navigation)**|ソフトナビゲーションに対して、ブラウザの標準的なナビゲーション方法を指す。ブラウザがWebサーバから送信されたHTMLレスポンスを受け取り、現在のページ全体を新しいHTMLに置き換えることで画面遷移が行われる。|
|**初回表示時**|本記事では、他サイトから流入してきた場合や、ページがリロードされた場合など、WebサーバーからHTMLレスポンスが返却される状況を指して「初回表示時」と呼ぶ。サイト訪問時や「First Load」などに相当する。javaScriptによるナビゲーションであるソフトナビゲーションが発生した時と差別化するために使用する。|


[^サーバーサイド]:本記事ではサーバーサイドとビルド時の実行環境は区別しています。

[^ソフトナビゲーション]:https://developer.chrome.com/docs/web-platform/soft-navigations-experiment?hl=ja#what_is_a_soft_navigation

## 要約

### レンダリングの意味

- **広義的なレンダリングの意味**
  - データを計算して変換すること
- **ブラウザにおけるレンダリングの意味**
  - Web コンテンツを画面上に描画すること
- **React におけるレンダリングの意味**
  - コンポーネントを呼び出す(実行する)こと

基本的にレンダリングはデータ変換をする工程のことであり、レンダリングをする対象によってニュアンスが変化します。

### レンダリングパターンに関連する用語の意味

- **Client-Side Rendering(CSR)**
  - 1. クライアントサイドのみでデータ取得・コンテンツ生成・DOM更新を行うレンダリングパターン(≒SPA)(≒SPA)
  - 2. クライアントサイドで DOM を更新すること
- **Server-Side Rendering(SSR)**
  - 1. リクエスト時にサーバーサイドでHTMLを生成するレンダリングパターン全般
  - 2. 初回表示時のリクエスト時にはHTMLをサーバーサイドで生成し、クライアントサイドでハイドレーションを行うレンダリングパターン
  - 3. リクエスト時にサーバーサイドでHTMLを生成すること
- **Static Site Generation(SSG)(別称: Pre-rendering)**
  - ビルド時に HTML (およびそのコンテンツ)を生成すること、あるいはそのレンダリングパターン
- **Single-page application(SPA)**
  - 1. ソフトナビゲーションで画面遷移するアプリケーション全般(SSR/SSG と共存する概念)
  - 2. クライアントサイドのみでデータ取得・コンテンツ生成・DOM更新を行うレンダリングパターン(SSR/SSG と排他的関係の概念)
- **PPR(Partial Prerendering)**
  - 同一のルート(ページ)内でSSR/SSGのようにレンダリングするコンポーネントを両立するレンダリングパターン

これらの用語が関心を持つのは、初回表示時や画面遷移時などのページを表示するタイミングにどのようにコンテンツを生成、表示するか です。
レンダリングパターンとしての意味では、アプリケーション全体で初回表示時と画面遷移時にそれぞれどのようにページの生成・表示するパターンに当てはまるかを指す言葉になります。
一方で、CSRやSSRといった用語では、初回表示時、画面遷移時に区別をつけず、ページおよびコンテンツを生成・表示することそのものの意味としても利用されます。


## 広義的なレンダリングの意味

**意味: データを計算して変換すること**

レンダリングという言葉は、Web技術に限らずコンピュータサイエンス全般で使用される用語です。この用語は、3DCG、動画、音声（作曲）など、さまざまなデータ処理の分野で使われています。

- 3DCG: モデルやシーンの 3D データを計算し、画像や映像として出力すること
- 動画: 各フレームを順番に描画し、最終的なビデオクリップを生成すること。
- 音声（作曲）: 複数の MIDI データやデジタルオーディオ信号を、音声ファイルデータ(mp3 など)として出力すること。

これらの例に共通しているのは、**データを計算して別の形式に変換する**という点です。ただし、この変換は単なるフォーマット変更ではなく、次の処理で解釈可能な形や、**人間にとって利用しやすい形式**に変えることを目的としています。

例えば、楽曲データの場合、最終的に人間が楽曲を再生することを目指して、音声ファイル形式への変換（レンダリング）が行われます。

フロントエンドの文脈では、最終的に視覚情報として画面上に要素を描画することが目的になるため、レンダリングとは画面に要素を表示するための一過程であると考えることができます。

## ブラウザにおけるレンダリングの意味

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

## React におけるレンダリングの意味

**レンダリングの意味: React がコンポーネントを呼び出すこと**

React の公式ドキュメントでは次のように説明されています。なお、「コンポーネントを呼び出す」という表現は、「実行する」と解釈しても大きな齟齬はありません。

> “Rendering” is React calling your components.
> レンダーとは React がコンポーネントを呼び出すことです


https://ja.react.dev/learn/render-and-commit

React ではレンダリングがトリガーされた後、以下の 3 つのフェーズを経て、要素が画面に描画または再描画されます。

| 名称                           | 意味                                                            |
| ------------------------------ | --------------------------------------------------------------- |
| レンダーフェーズ               | コンポーネントの呼び出しが行われる                              |
| コミットフェーズ               | レンダーフェーズで差分があった場合、 変更すべき DOM ノード を変更する |
| ペイント(ブラウザレンダリング) | ブラウザが画面を描画する。(React の責務ではない)                |

つまり、React ではコンポーネントが呼び出されたとしても、仮想 DOM(ReactTree)上で差分がなければ DOM が更新されず、画面には反映されません。また、[「ブラウザにおけるレンダリングの意味」](#ブラウザにおけるレンダリングの意味)の章で述べた**ブラウザレンダリングは**、Reactでは混乱を避けるために **ペイント(Paint)** と呼ばれています。

今回の解説では上記の 3 つのフェーズで説明していますが、より細かい実装が気になる方は以下のような他の記事を合わせて参考にしていただければと思います。

https://zenn.dev/aishift/articles/d046335a98bc34



## Client-Side Rendering(CSR)

**意味**
1. **クライアントサイドのみでデータ取得・コンテンツ生成・DOM更新を行うレンダリングパターン(≒SPA)**
2. **クライアントサイドで DOM を更新すること**

CSRにおける「レンダリング」とは　**クライアントサイドで DOM を更新すること** を指します。

一般的には1の意味が未だ広く使われる一方、筆者の周囲では2の意味で用いられることが多い印象です。

### 1つ目の意味: クライアントサイドのみでデータ取得・コンテンツ生成・DOM更新を行うレンダリングパターン(≒SPA)
後述する[SPA(Single-page application)](#single-page-application(spa))の一つの意味と一致します。

**従来のWebアプリケーション**では、HTMLレスポンスにあらかじめコンテンツが含まれており、画面遷移時には新しいHTMLレスポンスを受け取り、画面全体を更新していました(MPA[^MPA])。APIサーバからのデータ取得などもHTMLを配信するWebサーバによって行われます。

| タイミング                           | 挙動                                                           |
| ------------------------------ | --------------------------------------------------------------- |
|初回表示時・ページ遷移時| サーバー側でデータ取得・ページ生成を行い、完成したHTMLをブラウザに送信して画面全体を更新する。|

それに対して、**CSR**では以下のような挙動を示します。従来のアプリケーションがサーバー側で行ってきた処理はクライアントサイドで行われます。

| タイミング                           | 挙動                                                           |
| ------------------------------ | --------------------------------------------------------------- |
|初回表示時| 空のHTMLレスポンスが送られ、その後、JavaScriptがサーバーからJSONデータを取得し、コンテンツを生成しDOMを更新する。|
|ページ遷移時| ソフトナビゲーションによって、**JavaScriptが history や DOM を操作**し画面遷移を実現する。この際、サーバーから取得するデータは主にJSONデータ。|


現在でもCSRという用語は、初期のこのパターンを指して使われ続けています。例えば、Next.jsの[Client-side Rendering (CSR)](https://nextjs.org/docs/pages/building-your-application/rendering/client-side-rendering)のページでも、ブラウザは最小限のHTML(minimal HTML)をダウンロードし、ブラウザのJavaScriptがDOMを更新するパターンをCSRであるとしています。[^NextjsにおけるCSR]

![](https://images.prismic.io/prismic-main/ZjhPlEMTzAJOCi_n_CSR.png?auto=format%2Ccompress&fit=max&w=1920)
*CSRの概略図(Client-side Rendering (CSR) vs. Server-side Rendering (SSR)より引用 ) https://prismic.io/blog/client-side-vs-server-side-rendering*

### 2つ目の意味: クライアントサイドで DOM を更新すること
「初回表示時」や「ソフトナビゲーション時」など特定の場面で、**クライアントサイドでコンテンツ生成およびDOMを更新すること**を指します。
初回表示におけるCSRは、上述した空のHTMLレスポンスがWebサーバーから返却されるような挙動を指し、ナビゲーションがCSRである場合は、ソフトナビゲーションや[Client-side navigation](https://nextjs.org/learn-pages-router/basics/navigate-between-pages/client-side)と同等の意味になります。

(この2つ目の意味でCSRを使う場合、１つ目の意味のようなパターンは　CSR Only　のアプリケーションであるという呼び方ができます。)

なお、初回表示時に空の HTML を返すアプリケーション(SPA ≒ CSR Only)の欠点の一つに、SEO 対策が困難であるという点がありました。（現在ではGoogleのクローラではJavaScriptを実行できるためSEOの問題は改善されたようです。また、CSR Onlyのアプリケーションにはパフォーマンスやデータ転送の効率といった課題があります）。

[^MPA]:MPA: Multi-page application。画面遷移時に新しい HTML ファイルが送られ、画面全体が書き換わるようなアプリケーションのこと。
[^NextjsにおけるCSR]:NextjsにおけるCSRでは、NextJsのサーバー側のデータ取得機能である　getStaticProps や getServerSidePropsを使わず、クライアントサイドに寄せることで典型的なCSRの挙動を再現しています。

## Server-Side Rendering(SSR)

**意味**
1.**リクエスト時にサーバーサイドでHTMLを生成するレンダリングパターン全般**
2.**初回表示時のリクエスト時にはHTMLをサーバーサイドで生成し、クライアントサイドでハイドレーションを行うレンダリングパターン**
3.**リクエスト時にサーバーサイドでHTMLを生成すること**

1つ目、2つ目の意味ではレンダリングパターンのことを指しますが、3つ目の意味のようにリクエスト時にサーバーサイドでHTMLを生成する行為を指すこともあります。
SSRにおける「レンダリング」とは **リクエスト時にサーバーサイドで HTMLを生成すること** を指します。SSR をする主な目的は以下の2点です:

1. 動的サイト[^動的サイト]を作ること
2. コンテンツを含む HTML を作ること

SSR という言葉は CSR(SPA)が登場したときに生まれた**レトロ二ム**[^レトロニム]でした。しかし現在では、CSR だけでは解決できない課題を補うために、**サーバーサイドを活用するモダンなレンダリングパターンを指してSSRと呼ぶようになりました**(上記2の意味)。このモダンなパターンは、元々 「**SSR with Hydration**」 と呼ばれていたもので、サーバーサイドでの HTML 生成は初回表示時に限られ、その後のナビゲーションはソフトナビゲーションで行われます。WebクライアントからのページリクエストごとにHTMLレスポンスを生成することから、「Request-Time Rendering」などという呼び方をした方がわかりやすいかもしれません。

この章では、SSRのアプリケーションやその言葉の意味の変遷について述べていきます。

:::message
サーバーサイドで生成される HTML はファーストビューとして表示するための静的なものにすぎないため、Reactベースのフレームワークなどでは、ブラウザ上でも再度コードを実行してページをインタラクティブにする ハイドレーション(Hydration) という工程が必要になります。
:::

:::message
(ラフにReactなどのコンポーネントをサーバーサイドでレンダリングすることを SSR という使われ方がされることもあります。)
:::

[^動的サイト]:動的サイト: アクセスしたユーザーの属性や条件に応じて、表示内容を動的に生成して変化させるWebサイト。対義語の静的サイトは、「ユーザーや時間帯などによらず同じ内容を表示させるWebサイト」という意味。

### SSR という言葉の変遷

#### 初期の SSR の意味

**意味: リクエスト時にサーバーサイドで HTML ファイルを生成すること**

もともと SSR という言葉は、[Single-page application(SPA)](#single-page-applicationspa)、が台頭してきたときに、**CSR(Client-Side Rendering)のレトロニム**[^レトロニム] として使われ始めた言葉のようです。
CSR のようにクライアントサイドで コンテンツを生成しDOM を直接書き換える方法は一般的ではなく、この頃の SSR は、サーバーサイドのテンプレートエンジン(Java,PHP,Ruby on Rails 等)で HTMLを生成する従来の手法を指していました。
この方法では、初回表示もナビゲーション時もサーバーサイドでコンテンツを含む HTML を生成し、ブラウザでは HTML 全体を入れ替える形で画面を更新します(MPA[^MPA])。

CSRやSSRという言葉が使われ始めたのは、そもそもReactやNext.jsなどが台頭してきた後なのでは?と疑問を持つ方も多いと思います。
しかし、以下の記事は、Reactや次章で紹介するRendrが登場する前の記事であり、 「**Client-Side RenderingからServer-Side Renderingに戻す**」 という内容になっています。

https://www.openmymind.net/2012/5/30/Client-Side-vs-Server-Side-Rendering/


また、以下の最近の記事でも古典的なレンダリングパターンもServer-Side Renderingであると解釈しています。(なお、この記事ではSSRとCSRは排他的な関係ではなく、例えばSSR with Hydration のパターンはSSRとCSRが合体した姿であると解釈しています。) 

https://484.cs.uic.edu/readings/chapter-3-server-side-web-development/full-stack-react/

[^レトロニム]: レトロニム: 時代の変化により新しい事物が生まれたことから、既存の事物を新しいものと区別するため「後から」つくられた言葉

#### SSR with Hydration の出現

現在のような SSR フレームワークの起源の一つであると考えられるものとして Airbnb 社の Backbone.js ベースのフレームワーク、**Rendr** があります。
Backbone.js は、クライアントサイドの MVC モデルを採用した、古典的な SPA フレームワークの一つです。

当時の CSR Only のアプリケーションでは、ページ訪問時にコンテンツが空の HTML ファイルが最初に送られるため、**SEO**において課題がありました。

その対策のために、サイト訪問時用に 別言語(Ruby など)で開発したサーバーを用意したり、ヘッドレスブラウザのようなものを挟み JavaScript を事前に実行する(**ダイナミックレンダリング**)などをして、 コンテンツを含む HTML を生成していました。

(ダイナミックレンダリング)
https://developers.google.com/search/docs/crawling-indexing/JavaScript/dynamic-rendering?hl=ja

(JavaScript を実行して HTML を生成してくれるサービス)
https://prerender.io/

そこで Rendr が採用したアプローチは、Node.js を活用することで、それまで Ruby と JavaScript で重複していたロジックを一本化し、**クライアントサイドとサーバーサイドの双方で実行可能な共通コードを実現することでした。**

:::message
当時は**同じコード**がサーバーサイドでもクライアントサイドでも実行されることを **Isomorphic**　と呼びました。現在でもサーバーサイド、クライアントサイドの両側で実行される概念に対して Isomorphic という言葉が使われることがあります。[参考: Isomorphic Loaders](https://frontendmasters.com/blog/introducing-tanstack-start/#isomorphic-loaders)
:::


なお、Rendr の作者の Rendr の公開スライドに、すでに "Hydrate" という用語が登場しており、クライアントサイドで要素をインタラクティブにするフェーズを ハイドレーション と呼ぶ文化が、この時点で存在していたことが伺えます。

以下のRenderの記事は、React が公開される 2013 年 5 月以前に書かれたものであり、現在のような SSR フレームワークの形態がその時点ですでに完成していたことを示唆しています。しかし、Rendr は React の普及によって主流となることはなく、Airbnb も最終的に React へ移行したようです。

https://medium.com/airbnb-engineering/our-first-node-js-app-backbone-on-the-client-and-server-c659abb0e2b4

https://www.slideshare.net/slideshow/introducing-rendr-run-your-backbonejs-apps-on-the-client-and-server/19106546

このような、サーバーサイドとクライアントサイドで共通のコードを実行するタイプの SSR は、従来型の SSR と区別するため、**SSR with Hydration** と呼ばれました。

#### 現在の SSR

現在では、フロントエンド開発において React のような SPA の技術が普及し、さらには Next.js や Remix といった サーバーサイドでも JavaScript/React のコードが動作するフレームワークが主流となりました。これらのフレームワークはサーバーサイドにランタイムを設けることで、SPA の利点を享受しつつ、従来のSPAでは解決が難しかった課題の克服や最適化を実現しようとしています。
近年 SSR が言及される文脈は、主にこれらのフレームワークに関連する場合が多く、SPA登場当初とは想定場面などの前提が変化したため、初期に **SSR with Hydration** と呼ばれていたものが、現在では**単に SSR** と呼ばれるようになってきています。

![](https://images.prismic.io/prismic-main/ZfuQggmzsfHOGJkR_ssr.png?auto=format%2Ccompress&fit=max&w=3840)
*SSRの概略図(Client-side Rendering (CSR) vs. Server-side Rendering (SSR)より引用 ) https://prismic.io/blog/client-side-vs-server-side-rendering*

もともと SSR という言葉が誕生したのは、CSR（SPA）技術が台頭し、サーバーサイドで行われていたコンテンツ生成がクライアントサイドへ移行した時期でした。
しかし、現代におけるSSRという用語は、**本来クライアントサイドで実行されるコード**を、**サイト訪問時に限りサーバーサイドでレンダリングする**という、方向性の逆転が起きた後の意味合いを持つようになったと解釈できそうです。

:::message
Astro と SSR
ここまでモダンなフレームワークにおけるSSRという用語が基本的にソフトナビゲーションなどの概念とセットになるかのように述べてきました。
MPA[^MPA] のフレームワークである Astro でも SSR は [On-demand Rendering](https://docs.astro.ビルド/en/guides/on-demand-rendering/) という機能として存在しており、ページリクエストに応じてサーバーサイドで HTML を生成する仕組みを指します。 
Astro は [Static Site Generation(SSG)](#static-site-generation(ssg)(%E5%88%A5%E7%A7%B0%3A-pre-rendering)) に特化したフレームワークとして登場し、SPA に対抗し、従来の MPA のようにページ遷移を行いながらも、JavaScript 以外の言語との二重開発が不要であることを利点としています。
SSG の静的ページのみであれば、ビルド時に生成した静的 HTML を配信し、外部 API はブラウザで処理できるため、Node.js 等のランタイムは不要です。しかしOn-demand Rendering が追加され、本来持たなかったサーバーサイドランタイムを持つという手段をとることで、これまでクライアントサイドで行っていた動的なコンテンツ生成 (CSR) をサーバーサイドで行い、コンテンツを含む HTML ファイルを配信できるようになりました。
よって Astro の On-demand Rendering が解決した問題も MPAでありながらも、動的サイトをサーバーサイドから直接配信したいという点で、Next.Js などの SSR with Hydration と共通する部分が多いと筆者は考えています。　
:::


### RSC の ServerComponents は SSR なのか？

Reactにおいて RSC（React Server Components）の登場以前は、コンポーネントは基本的にすべてクライアントサイドでレンダーされ、SSRや SSG ではサーバーサイド または ビルド時にも実行されていました。しかし、RSC では従来通りの **Client Components**(CC)に加え、完全にサーバーサイド または ビルド時のみ実行される **Server Components**(SC)が導入されました。

では、**SC は SSR と同じ概念なのか？** という疑問については、React 公式は、併用できる別の概念であると説明しています。
SSR はサーバーサイドで HTML を事前に生成する技術を指し、SC のレンダリングをそのまま SSR と呼ぶわけではありません。

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

## Static Site Generation(SSG)(別称: Pre-rendering)

**用語の意味: ビルド時に HTML (およびそのコンテンツ)を生成すること、あるいはそのレンダリングパターン**

Static Site Generatorsとも。海外では、SSGは単にPre-renderingとも呼ばれてきました。

SSGでは、ビルド時にフレームワークによるデータフェッチやコンポーネントのレンダーが行われ、HTML(コンテンツ)が生成されます。よってSSGにおける「レンダリング」とは　**HTML を生成すること** と言えます。

staticという名前の通り静的なHTML(コンテンツ)を取り扱うことになるため、CDNなどで比較的容易にキャッシュができるといった利点が挙げられますが、SSRのようにリクエストに応じてレスポンスを都度生成することはできません。また、SSGという用語自体は、ハイドレーションの有無に限らず広く使われているようです[^stateOfJs2024]。
  

### 事前レンダリング
Pre-renderingという呼び方をそのまま日本語に直すと **事前レンダリング** となりますが、この言葉はSSR、SSGをまとめて指す言葉として少なからず使われてきました。クライアントサイドにレスポンスが返却されるよりも前の段階でレンダリングされるという意味合いであると捉えることができそうです。
しかし、近年 Next.jsやRemix の機能などで、Pre-renderingという用語は**ビルド時にレンダリングする意味**で明確に使われているように、海外でのPre-renderingではSSRを包含した意味はないようです。この場合の「事前」は**リクエスト時よりも前**、つまりビルド時を指していると解釈できます。
  
:::message
SSG、SSRのアプリケーションでソフトナビゲーションをする際は、Webサーバーからは初回表示とは異なりHTMLファイルではなくjsonデータなどが配信されます。よってSSGの場合はビルド時に初回表示用のHTMLとソフトナビゲーション用のファイルの両方が生成されることになります。
:::

:::message
ServerComponents(SC)のドキュメントに、[サーバを利用しないサーバーコンポーネント](https://ja.react.dev/reference/rsc/server-components#server-components-without-a-server) という~~一見意味が分かりにくい~~段落が存在しますが、ここにおけるサーバーはWebサーバ(BFFなどに該当するサーバーサイドのランタイム)のことを指しており、SSGのようにビルド時にSCを実行することを指しています。(つまり、実行前のSCのコードがブラウザに渡って、クライアントサイドで実行されるわけではありません。)
:::

[^stateOfJs2024]: [state of js 2024](https://survey.devographics.com/en-US/survey/state-of-js/2024/outline/9)ではSSGは、*Static content pre-rendered at build time, with or without a client-side dynamic element*　とされています。


## Single-page application(SPA)

1. **ソフトナビゲーションで画面遷移するアプリケーション全般(SSR/SSG と共存する概念)**
2. **クライアントサイドのみで動作するレンダリングパターン(SSR/SSG と排他的関係の概念)**

 Single-page applicarion(SPA)は、レンダリングパターンに関する用語で、筆者が観測する限り大きく分けて二つの意味で使用されているように見受けられます。

### 1つ目の意味: ソフトナビゲーションで画面遷移するアプリケーション全般(SSR/SSG と共存する概念)
初回アクセス時を除き、**HTMLをサーバーから送信しないアプリケーション**を指します。ページ遷移は、ソフトナビゲーション、つまりブラウザ上JavaScriptによって行われ、あたかも単一のページ内で動作しているかのように見えます。これは、ページ遷移ごとにHTMLファイルをサーバーから取得する**MPA（Multi-Page Application）[^MPA]との対比**として理解できます。また、サーバーサイドランタイムの有無について定義されておらず、SSR（Server-Side Rendering）やSSG（Static Site Generation）と同時に成立し得る概念になっています。


:::
message
SSRやSSGが「シングルページ」ではないという反対意見の一つに、これらのフレームワークがエントリーポイント（ページのパスやルート）ごとにHTMLレスポンスやHTMLファイルを生成される点で単一なページではないという主張もあるようです。
:::

### 2つ目の意味:　クライアントサイドのみでデータ取得・コンテンツ生成・dom更新を行うレンダリングパターン(SSR/SSG と排他的関係の概念)

[CSRの章](#client-side-rendering(csr))でも述べた、**初回表示時には空のHTMLがされ**、その後のデータ取得やコンテンツの生成などをクライアントサイドでのみ実行するパターンのことを指します。サーバーサイドのランタイムを持つ場合はSPAではないとする考え方もされているようです。
この用例は海外で特に見られる傾向にあり、例えば State of JavaScript の 2022 年[^stateOfJs2022]から今年度[^stateOfJs2024]に至るまでの SPA の定義や [Remix の SPA モード](https://remix-docs-ja.techtalk.jp/guides/spa-mode/)という機能が示すものが該当し、 SSR/SSG と横並びになるようなものとなっています。

> SPA: Apps that run entirely in the browser
> MPA: Apps that run entirely in the server, with minimal client-side dynamic behavior

[^stateOfJs2022]:(State of JavaScript 2022) https://survey.devographics.com/en-US/survey/state-of-js/2022/outline/11
[^stateOfJs2024]:(State of JavaScript 2024)https://survey.devographics.com/en-US/survey/state-of-js/2024/outline/9

### SPA、SSR、SSG
SPAの意味を 2つ目の意味の **サーバーサイドでレンダリングをしない クライアントサイドのみで動作するアプリケーション**(CSR Only) のような意味で捉えた場合、SSR、SSGとの差異は、初回アクセス時に配信するコンテンツ(HTML)のレンダリング方法の違いになります。

| 名称 | 初回アクセス時のコンテンツのレンダリング方法                                                                                                |
| ---- | --------------------------------------------------------------------------------------------------- |
| SPA  | ブラウザ上のみ(事前にレンダリングしない)                       |
| SSR  | 毎アクセス時にサーバーサイドでレンダリングする |
| SSG  | ビルド時にレンダリングする     |

:::message alert
Reactなどの場合、SSR、SSGではブラウザ上でもコンポーネントの実行(ハイドレーション)が必要です。
:::


## PPR(Partial Prerendering)

:::message　alert
Partial Prerendering はNext.js v15.1.2時点で実験的な機能で、canary でのみ利用可能であり、PPRがstableな機能として提供される際には機能の一部が変更されている可能性がありますので、ご注意下さい
:::

**意味: 同一のルート(ページ)内でSSR/SSGのようにレンダリングするコンポーネントを両立するレンダリングパターン**

PPRはNext.jsの提供する実験的な機能であり、新たなレンダリングパターンです。そしてレンダリングの焦点はサーバーサイド(ビルド時も含む)にあります。

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
