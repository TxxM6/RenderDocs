## はじめに

フロントエンド開発 において**レンダリング**という言葉はさまざまな場面で登場します。さらに、Server-Side Rendering(SSR)のようにレンダリングという言葉が名前に含まれているものを含め、Single-Page Application(SPA)など、レンダリングに関連した様々な用語が多く存在しており、それらの意味はフロントエンドの技術の進化に伴い、徐々にニュアンスが変化してきているように感じています。  
他のエンジニアとこれらの用語について話す中で、「自分はそれをSSRとは呼ばない」「SPAにはいくつかの意味があるよね」といった意見を耳にし、人によって解釈が異なることを実感しました。

では SSR や SPAなどといった用語は今日どういった意味で使われているのでしょう。そもそも、レンダリングとはそもそもどういう意味なのでしょうか。SSR における「レンダリング」とは、何を指しているのでしょうか。

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
|**ハードナビゲーション(Hard Navigation)**|ソフトナビゲーションに対して、ブラウザの標準的なナビゲーション方法を指す。ブラウザがWebサーバーから送信されたHTMLレスポンスを受け取り、現在のページ全体を新しいHTMLに置き換えることで画面遷移が行われる。|
|**初回表示時**|本記事では、他サイトから流入してきた場合や、ページがリロードされた場合など、WebサーバーからHTMLレスポンスが返却される状況を指して「初回表示時」と呼ぶ。サイト訪問時や「First Load」などに相当する。JavaScriptによるナビゲーションであるソフトナビゲーションが発生した時と差別化するために使用する。|


[^サーバーサイド]:本記事ではサーバーサイドとビルド時の実行環境は区別しています。

[^ソフトナビゲーション]:https://developer.chrome.com/docs/web-platform/soft-navigations-experiment?hl=ja#what_is_a_soft_navigation

## 要約
- **広義的なレンダリングの意味**
  - データを計算して変換すること
- **ブラウザにおけるレンダリングの意味**
  - Web コンテンツを画面上に描画すること
- **React におけるレンダリングの意味**
  - React がコンポーネントを実行すること

基本的にレンダリングはデータ変換をする工程のことであり、レンダリングをする対象によってニュアンスが変化します。

- **Client-Side Rendering(CSR)**
  - クライアントサイドでDOMを更新する手法
- **Server-Side Rendering(SSR)**
  - 1. リクエスト時にサーバーサイドでHTMLを生成する手法
  - 2. 初回表示のリクエスト時にHTMLをサーバーサイドで生成し、クライアントサイドでハイドレーションを行う手法
- **Static Site Generation(SSG)**
  - ビルド時に HTMLを生成する手法
- **Partial Prerendering(PPR)**
  - 同一のルート(ページ)内でSSR/SSGのようにレンダリングするコンポーネントを両立する手法

初回表示時や画面遷移時などのページを表示するタイミングに、**ページおよびコンテンツをどのようにを生成、表示するか**というレンダリング手法・レンダリング技術を指す用語です。

- **Single-Page Application(SPA)**
  - 1. ソフトナビゲーションを行うアプリケーション全般(SSR/SSG と共存する概念)
  - 2. ソフトナビゲーションを行う、初回表示もCSRのアプリケーション(SSR/SSG と排他的関係の概念)
- **Multi-Page Application(MPA)**
  - ハードナビゲーションを行い、サーバーで生成したHTMLを表示するアプリケーション

アプリケーション全体の動作の仕組みやアーキテクチャを指す用語です。特に、ナビゲーション方法の違いが両者の特徴を決定づける重要な要素となります。

## 広義的なレンダリングの意味

**意味: データを計算して変換すること**

レンダリングという言葉は、Web技術に限らずIT分野全般で使用される用語です。この用語は、3DCG、動画、音声（作曲）など、さまざまなデータ処理の分野で使われています。

- 3DCG: モデルやシーンの 3D データを計算し、画像や映像として出力すること
- 動画: 各フレームを順番に描画し、最終的なビデオクリップを生成すること。
- 音声（作曲）: 複数の MIDI データやデジタルオーディオ信号を、音声ファイルデータ(mp3 など)として出力すること。

これらの例に共通しているのは、**データを計算して別の形式に変換する**という点です。ただし、この変換は単なるフォーマット変更ではなく、次の処理で解釈可能な形や、**人間にとって利用しやすい形式**に変えることを目的としています。

例えば、楽曲データの場合、最終的に人間が楽曲を再生することを目指して、音声ファイル形式への変換（レンダリング）が行われます。

フロントエンドの文脈では、最終的に視覚情報として画面上に要素を描画することが目的になるため、レンダリングとは画面に要素を表示するための一過程であると考えることができます。

## ブラウザにおけるレンダリングの意味

**意味: Web コンテンツを画面上に描画することと**

ブラウザの役割はコンテンツ(HTML など)を画面上に描画することです。この過程では、 CSS のスタイルを適用し、ラスタライズ(ピクセル化)を行い、最終的に OpenGL のような OS 標準のグラフィックライブラリに描画データを渡します。
この一連の処理は **_The Pixel Pipeline_** [^ThePixelPipeline]や、 **_Rendering Pipeline_**[^RenderingPipeline] などと呼ばれます。この処理には、ブラウザに搭載されているレンダリングエンジン(例: Blink)やグラフィックライブラリ(例: Skia)がなどが関与しています。chrome の具体例については、以下のスライドが処理の概略を示しています。[^LifeOfAPixel]

![](https://storage.googleapis.com/zenn-user-upload/abc28164dc39-20240919.jpg)

:::message
ウェブの特徴の一つとして、コンテンツが動的に更新される点が挙げられます。そのため、ブラウザは DOM を含むいくつかの中間表現を保持し、画面の更新を効率化しています。
:::

ブラウザのレンダリングの責務の範囲は、厳密には HTML などを変換したものを OS のグラフィックライブラリに渡すところまでです。しかし、多くの文脈において、ブラウザレンダリングとは **HTML、DOM を画面上に表示すること** と簡潔に捉えられることが一般的です。

[^ThePixelPipeline]: The Pixel Pipeline の引用元: https://web.dev/articles/rendering-performance
[^RenderingPipeline]: Rendering Pipeline の引用元(RenderingNG): https://developer.chrome.com/docs/chromium/renderingng-architecture?hl=ja
[^LifeOfAPixel]: Life of a Pixel の 79 枚目から引用: https://docs.google.com/presentation/d/1boPxbgNrTU0ddsc144rcXayGA_WF53k96imRH8Mp34Y/edit#slide=id.ga884fe665f_64_1800

## React におけるレンダリングの意味

**意味: React がコンポーネントを実行すること**

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

**意味:クライアントサイドでDOMを更新する手法**

CSRにおける「レンダリング」とは　**コンテンツを生成しDOMを更新すること** を指します。

CSRが登場する前の**従来のWebアプリケーション**では、初回表示時やページ遷移時に、サーバー側でデータ取得・ページ生成を行い、完成したHTMLをブラウザに送信し画面全体を更新していました([MPA](#multi-page-application(mpa)))。

一方、**CSR**で動作するアプリケーションでは、初回表示時やハードナビゲーションでは以下のような流れでページが表示されます: 
1. コンテンツが空のHTMLレスポンスがサーバーから送信され、表示される
2. ブラウザがJavaScriptを読み込み、その後、**データ取得・コンテンツ生成**を**クライアントサイド**で行い**DOMを更新する**

ページ遷移がソフトナビゲーションである場合はHTMLレスポンスを受け取らず、JavaScriptがロードされた後の**データ取得からDOMを更新して画面に表示するまでの過程**が同様に行われます。なお、この場合は現在の画面からDOMを部分的に書き換えることができます。

このように、コンテンツの生成およびDOMの書き換えをする処理が**クライアントで実行される**ことから、この手法は**CSR**と呼ばれます。

なお、初回表示時に空の HTML を返すアプリケーションの欠点の一つに、SEO 対策が困難であるという点がありました。（現在ではGoogleのクローラではJavaScriptを実行できるためSEOの問題は改善されたようです。また、CSR Onlyのアプリケーションにはパフォーマンスやデータ転送効率の面で課題が残ります。）


:::message
CSRの意味が誤解されやすい場面として、CSRが使われているアプリケーションのことを、クライアントサイドだけで動作したり、SPAのようにソフトナビゲーションものであると混同されるケースが挙げられると思います。
これは、CSR という言葉が使われる場面は、初回表示のことであることが多く、初期のSPAのようなクライアントサイドのみで動くアプリケーションが説明に使われることが多いためだと推察します。
例えば、Next.js  Pages Routerの[Client-side Rendering (CSR)](https://nextjs.org/docs/pages/building-your-application/rendering/client-side-rendering)のページでも、ブラウザは最小限のHTML(minimal HTML)をダウンロードし、ブラウザのJavaScriptがDOMを更新するパターンが取り上げられています。

しかし、CSRはあくまでページを表示する場面においての手法であり、初回表示時にサーバーサイドが活用されていても、ナビゲーション時にCSRが用いられることもあります。また、CSRはハードナビゲーションが行われる状況でも成り立つのに対し、SPAはハードナビゲーションと相容れない関係であります。
:::


![](https://images.prismic.io/prismic-main/ZjhPlEMTzAJOCi_n_CSR.png?auto=format%2Ccompress&fit=max&w=1920)
*CSRの概略図(Client-side Rendering (CSR) vs. Server-side Rendering (SSR)より引用 ) https://prismic.io/blog/client-side-vs-server-side-rendering*

[^NextjsにおけるCSR]:Nextjs Pages RouterにおけるCSRでは、NextJsのサーバー側のデータ取得機能である　getStaticProps や getServerSidePropsを使わず、クライアントサイドに寄せることで典型的なCSRの挙動を再現しています。

## Server-Side Rendering(SSR)

**意味**
1. **リクエスト時にサーバーサイドでHTMLを生成する手法**
2. **初回表示のリクエスト時にHTMLをサーバーサイドで生成し、クライアントサイドでハイドレーションを行う手法**

SSRにおける「レンダリング」とは **リクエスト時にサーバーサイドで HTMLを生成すること** を指します。SSR をする主な目的は以下の2点です:

1. 動的サイト[^動的サイト]を作ること
2. コンテンツを含む HTML を作ること

WebクライアントからのページリクエストごとにHTMLレスポンスを生成する点から、「Request-Time Rendering」 と呼んだ方が分かりやすいかもしれません。

SSR という言葉は CSRが登場したときに生まれた**レトロ二ム**[^レトロニム]でした。しかし現在では、CSR だけでは解決できない課題を補うために、**サーバーサイドを活用するモダンなレンダリングモデルを指してSSRと呼ぶようになりました**(上記2の意味)。このパターンは、元々 「**SSR with Hydration**」 と呼ばれていたものです。

この章では、SSRのアプリケーションや用語の意味の変遷について述べていきます。

[^動的サイト]:動的サイト: アクセスしたユーザーの属性や条件に応じて、表示内容を動的に生成して変化させるWebサイト。対義語の静的サイトは、「ユーザーや時間帯などによらず同じ内容を表示させるWebサイト」という意味。

### SSR という言葉の変遷

#### 初期の SSR の意味

**意味: リクエスト時にサーバーサイドで HTML ファイルを生成すること**

もともと SSR という言葉は、[SPA](#single-page-applicationspa)が台頭してきたときに、**CSRのレトロニム**[^レトロニム] として使われ始めた言葉のようです。
CSR のようにクライアントサイドで コンテンツを生成しDOM を直接書き換える方法は当時は一般的ではなく、この頃の Web アプリケーションは、サーバーサイドのテンプレートエンジン(Java,PHP,Ruby on Rails 等)で HTMLを生成していました。この方法では、初回表示もナビゲーション時もサーバーサイドでコンテンツを含む HTML を生成し、ブラウザでは HTML 全体を入れ替える形で画面を更新します([MPA](#multi-page-application(mpa)))。SPAの普及以降、CSRと対比してこの従来の手法がSSRと呼ばれるようになりました。

CSRやSSRという言葉が使われ始めたのは、そもそもReactやNext.jsなどが普及した後なのでは?と疑問を持つ方も多いと思います。
しかし、以下の記事は、Reactや次章で紹介するRendrが登場する前の記事であり、 「**Client-Side RenderingからServer-Side Renderingに戻す**」 という内容になっていることから古典的なレンダリング方法に対してこの用語が使われていたことが伺えます。

https://www.openmymind.net/2012/5/30/Client-Side-vs-Server-Side-Rendering/


また、以下の最近の記事でも古典的なレンダリングモデルもServer-Side Renderingであると解釈しています。
https://484.cs.uic.edu/readings/chapter-3-server-side-web-development/full-stack-react/

[^レトロニム]: レトロニム: 時代の変化により新しい事物が生まれたことから、既存の事物を新しいものと区別するため「後から」つくられた言葉

#### SSR with Hydration の出現

現在のような SSR フレームワークの起源の一つであると考えられるものとして Airbnb 社の Backbone.js ベースのフレームワーク、**Rendr** があります。
Backbone.js は、クライアントサイドの MVC モデルを採用した、古典的な SPA フレームワークの一つです。

当時の CSR Only のアプリケーションでは、初回表示時にコンテンツが空の HTML ファイルが最初に送られるため、**SEO**において課題がありました。

その対策のために、初回表示時用に 別言語(Ruby など)で開発したサーバーを用意したり、ヘッドレスブラウザのようなものを挟み JavaScript を事前に実行する**ダイナミックレンダリング**などの手法により、コンテンツを含む HTML を生成していました。

(ダイナミックレンダリング)
https://developers.google.com/search/docs/crawling-indexing/JavaScript/dynamic-rendering?hl=ja

(JavaScript を実行して HTML を生成してくれるサービス)
https://prerender.io/

そこで Rendr が採用したアプローチは、Node.js を活用することで、それまで Ruby と JavaScript で重複していたロジックを一本化し、**クライアントサイドとサーバーサイドの双方で実行可能な共通コードを実現することでした。**

:::message
当時は同じコードがサーバーサイドでもクライアントサイドでも実行されることを **Isomorphic** と呼びました。現在でもサーバーサイド、クライアントサイドの両側で実行される概念に対して Isomorphic という言葉が使われることがあります。[参考: Isomorphic Loaders](https://frontendmasters.com/blog/introducing-tanstack-start/#isomorphic-loaders)
:::


サーバーサイドで生成される HTML はファーストビューとして表示するための静的なものにすぎないため、ブラウザ上でもJavaScript を実行してページをインタラクティブにする **ハイドレーション**(Hydration) という工程が必要になります。

そして、このような、サーバーサイドとクライアントサイドで共通のコードを実行するタイプの SSR は、従来型の SSR と区別するため、**SSR with Hydration** と呼ばれました。

なお、以下のRenderの記事は、React が公開される 2013 年 5 月以前に書かれたものであり、現在のような SSR フレームワークの形態がその時点ですでに完成していたことを示唆しています。しかし、Rendr は React の普及によって主流となることはなく、Airbnb も最終的に React へ移行したようです。

:::message
Rendr の作者の Rendr の公開スライドに、すでに "Hydrate" という用語が登場しており、クライアントサイドで要素をインタラクティブにするフェーズを ハイドレーション と呼ぶ文化が、この時点で存在していたことが伺えます。
:::

https://medium.com/airbnb-engineering/our-first-node-js-app-backbone-on-the-client-and-server-c659abb0e2b4

https://www.slideshare.net/slideshow/introducing-rendr-run-your-backbonejs-apps-on-the-client-and-server/19106546



#### 現在の SSR

**意味: 初回表示のリクエスト時にHTMLをサーバーサイドで生成し、クライアントサイドでハイドレーションを行う手法**
　　
現在、フロントエンド開発において ReactやVue.js といった SPA構築に適したJavaScriptフレームワークが普及し、さらに、 Next.js や Remix といった サーバーサイドでも JavaScript/React のコード　を実行できるフレームワークが主流となりました。
SPA登場以前とは異なり、モダンなSSRフレームワークでは異なる言語との二重開発の問題は解消されており、SPAの利点を享受しつつ、非インタラクティブバージョンのページを素早く表示する手段を実現しました。
そして、SSRという用語に関しても、初期に **SSR with Hydration** と呼ばれていたものが、**単に SSR** と呼ばれるようになり、その認識が広がってきています。さらには、「**初回表示時にSSR(with hydration)するSPAのこと**」をSSRと呼ぶ用例も目にします。

もともと SSR という言葉が誕生したのは、CSR（SPA）技術が台頭し、サーバーサイドで行われていたコンテンツ生成がクライアントサイドへ移行した時期でした。
しかし、現代におけるSSRという用語は、**本来クライアントサイドで実行されるコード**を、**初回表示時にはサーバーサイドで実行する**という、方向性の逆転が起きた後の意味合いを持つようになったと解釈できそうです。


![](https://images.prismic.io/prismic-main/ZfuQggmzsfHOGJkR_ssr.png?auto=format%2Ccompress&fit=max&w=3840)
*SSRの概略図(Client-side Rendering (CSR) vs. Server-side Rendering (SSR)より引用 ) https://prismic.io/blog/client-side-vs-server-side-rendering*


:::message
[MPA](#multi-page-application(mpa)) のフレームワークである Astro でも、 SSR は [On-demand Rendering](https://docs.astro.build/en/guides/on-demand-rendering/) という機能として存在しており、ページリクエスト時にオンデマンドにサーバーサイドで HTML を生成する手法を指します。
また、Astroにおいてもインタラクティブな部分(island)は、クライアントサイドで部分的にハイドレーションされます。 
:::


### RSC の ServerComponents は SSR なのか？

Reactにおいて RSC（React Server Components）の登場以前は、コンポーネントは基本的にすべてクライアントサイドで実行され、SSRや SSG ではサーバーサイド または ビルド時にも実行されていました。しかし、RSC では従来通りの **Client Components**(CC)に加え、完全にサーバーサイド または ビルド時のみ実行される **Server Components**(SC)が導入されました。

では、**SC は SSR と同じ概念なのか？** という疑問については、React 公式は、併用できる別の概念であると説明しています。
SSRが「初回表示時にHTMLを生成する」用語として使われており、一方RSCでのSCは、以下の点で現状のSSRという用語の枠には収まっておらず、SSRとは区別されます。

- 初回表示時だけでなくソフトナビゲーションでも使われる
- HTMLを生成するとは限らない
  - 初回表示時はHTMLを生成するSSRやSSGの一部 (前段階) になることがある


React RFC によると次のように述べられています：

https://github.com/reactjs/rfcs/blob/main/text/0188-server-components.md#does-this-replace-ssr

> Does this replace SSR?
> No, they’re complementary. SSR is primarily a technique to quickly display a non-interactive version of client components. You still need to pay the cost of downloading, parsing, and executing those Client Components after the initial HTML is loaded.
> You can combine Server Components and SSR, where Server Components render first, with Client Components rendering into HTML for fast non-interactive display while they are hydrated. When combined in this way you still get fast startup, but you also dramatically reduce the amount of JS that needs to be downloaded on the client.

> (DeepL 翻訳)
> これは SSR を置き換えるものですか？
> いいえ。SSR は主にクライアントコンポーネントの非インタラクティブバージョンを素早く表示するテクニックです。最初の HTML が読み込まれた後、それらのクライアントコンポーネントをダウンロードし、解析し、実行するコストはまだ必要です。
> サーバコンポーネントと SSR を組み合わせることができます。 サーバコンポーネントが最初にレンダリングされ、クライアントコンポーネントは HTML にレンダリングされます。このように組み合わせることで、高速なスタートアップが得られるだけでなく、 クライアントにダウンロードされる JS の量を劇的に減らすことができます。


## Static Site Generation(SSG)

**意味: ビルド時に HTMLを生成する手法**

Static Site Generatorsの略称でもあります。海外では、SSGは **Pre-Rendering** とも呼ばれています。
SSGでは、ビルド時にフレームワークによるデータフェッチやコンポーネントの実行が行われ、HTML および そのコンテンツが生成されます。よってSSGにおける「レンダリング」とは　**ビルド時に　HTML を生成すること** と言えます。

staticという名前の通り静的なHTML(コンテンツ)を取り扱うことになるため、CDNなどで比較的容易にキャッシュができるといった利点が挙げられますが、SSRのようにリクエストに応じてレスポンスを都度生成することはできません。また、SSGという用語自体は、ハイドレーションの有無に限らず広く使われているようです[^stateOfJs2024]。
  

### 事前レンダリング
**Pre-Rendering**という呼び方をそのまま日本語に直すと **事前レンダリング** となります。この言葉は**SSR、SSGをまとめて指す言葉**として少なからず使われてきました。
クライアントサイドに**レスポンスが返却されるよりも前**の段階でレンダリングされるという意味合いであると捉えることができそうです。
また、SSRの章で触れた[prerender.io](https://prerender.io/)という名前のサービスは、CSRのページに対してJavaScriptを実行し、得られたHTMLをキャッシュしてクローラー用に配信するサービスであり、prerenderという意味がアプリケーションのビルド時に限定した意味ではなかったように思われます。
しかし、近年 Next.jsやRemix の機能などで、Pre-renderingという用語は**ビルド時にレンダリングする意味**で明確に使われているようになってきました。この場合の「事前」は**リクエスト時よりも前**、つまりビルド時を指していると解釈できます。
  
:::message
SSG、SSRのアプリケーションでソフトナビゲーションをする際は、初回表示とは異なり、WebサーバーからはHTMLファイルではなくjsonデータなどが配信されます。よってSSGの場合はビルド時に初回表示用のHTMLとソフトナビゲーション用のファイルの両方が生成されることになります。
:::

:::message
ServerComponents(SC)のドキュメントに、[サーバを利用しないサーバーコンポーネント](https://ja.react.dev/reference/rsc/server-components#server-components-without-a-server) という~~一見意味が分かりにくい~~段落が存在します。ここにおける「サーバー」はWebサーバー(BFFなどに該当するサーバーサイドのランタイム)のことであり、このドキュメントはSSGのようにビルド時にSCを実行することを指しています。(つまり、実行前のSCのコードがブラウザに渡って、クライアントサイドで実行されるわけではありません。)
:::

[^stateOfJs2024]: [state of js 2024](https://survey.devographics.com/en-US/survey/state-of-js/2024/outline/9)ではSSGは、*Static content pre-rendered at build time, with or without a client-side dynamic element*　とされています。


## Single-Page Application(SPA)

1. **ソフトナビゲーションを行うアプリケーション全般(SSR/SSG と共存する概念)**
2. **ソフトナビゲーションを行う、初回表示もCSRのアプリケーション(SSR/SSG と排他的関係の概念)**

Single-Page Application（SPA）は、Webアプリケーションのアーキテクチャを指す用語です。ただし、文献によっては、Webサイトの通信方式を指すものとして解釈される場合もあります。
筆者が観測する限り大きく分けて二つの意味で使用されているようですが、どちらの意味であっても、ページコンテンツの更新時に「ページ全体の再読み込みを行わない」ことは重要であり、そのためにソフトナビゲーションが画面遷移に用いられている点で共通しているようです。

### 1つ目の意味: ソフトナビゲーションを行うアプリケーション全般(SSR/SSG と共存する概念)
単一のページ内で動き続けるアプリケーション、つまり、ページ遷移時に**サーバーから新たにHTMLを送信されずに動作するアプリケーション**を指します。
このSPAが指すアーキテクチャとは、HTMLページを最初に読み込んだ後、以降のページ遷移ではJavaScriptを使用して画面の一部を動的に更新する仕組みを指します。これにより、画面遷移があたかも1つのページ内で行われているように見えます。この仕組みはページ遷移ごとにHTMLファイルをサーバーから取得する[MPA](#multi-page-application(mpa))**との対比**として解釈が可能です。
サーバーサイドランタイムの有無について区別されておらず、SSRやSSGと同時に成立し得る概念となっており、CSR/SSR/SSGを、SPAを構築するレンダリング技術であるという解釈することができます。

:::message
SSRやSSGが「シングルページ」ではないという反対意見の一つに、これらのフレームワークがエントリーポイント（ページのパスやルート）ごとにHTMLレスポンスやHTMLファイルが生成される点で単一なページではないという主張もあるようです。
:::

### 2つ目の意味:　ソフトナビゲーションを行う、初回表示もCSRのアプリケーション(SSR/SSG と排他的関係の概念)

1つ目の意味同様ソフトナビゲーションで画面遷移しますが、**初回表示時には空のHTMLが返されるような**、クライアントサイドでのみ実行されるパターンのことを指します。
初回表示もCSRの場合、SSRした時などとは異なり、アプリケーション全体を通して、どのルート(ページ)においても共通のHTMLファイルが配信されるため、アプリケーション全体が単一のページで構成されていると言えるかもしれません。

この用例は海外で特に見られる傾向にあり、例えば State of JavaScript の 2022 年[^stateOfJs2022]から今年度[^stateOfJs2024]に至るまでの SPA の定義や [Remix の SPA モード](https://remix-docs-ja.techtalk.jp/guides/spa-mode/)という機能が示すものが該当し、 SSRやSSG と横並びになるようなものとなっています。

> SPA: Apps that run entirely in the browser
> MPA: Apps that run entirely in the server, with minimal client-side dynamic behavior

[^stateOfJs2022]:(State of JavaScript 2022) https://survey.devographics.com/en-US/survey/state-of-js/2022/outline/11
[^stateOfJs2024]:(State of JavaScript 2024)https://survey.devographics.com/en-US/survey/state-of-js/2024/outline/9


### SPA、SSR、SSG
SPAとSSRが対比される場面では、主にソフトナビゲーションを行うアプリケーション同士の比較になるようです。
この場面において SPAとSSR、SSGとの争点は、初回アクセス時に配信するコンテンツ(HTML)の表示方法です。ここでのSPAは **初回表示をCSRで行うアプリケーション**という意味合いになります。(そして、この文脈においては、SPAの代わりにCSRという言葉を用いることができてしまいますが、厳密にはCSRとSPAは別の用語です。)

| 名称 | 初回表示時のコンテンツのレンダリング方法                                                                                                |
| ---- | --------------------------------------------------------------------------------------------------- |
| SPA  | ブラウザ上でDOMを更新                      |
| SSR  | 毎リクエスト時にサーバーサイドでサーバサイドでHTMLを生成 |
| SSG  | ビルド時にHTMLを生成    |

:::message alert
Reactなどの場合、SSR、SSGではブラウザ上でもコンポーネント(CC)の実行(ハイドレーション)が必要です。
:::

## Multi-Page Application(MPA)

**意味: ハードナビゲーションを行い、サーバーで生成したHTMLを表示するアプリケーション**
  
直訳すると 複数のページ(HTML)で構成されるアプリケーション ですが、重要であるのは以下の2点です。MPAがSPAとの対比で使われる言葉であるためです。
1. **ページ遷移時に新たにHTMLレスポンスが送信され、画面全体が再読み込みされる**
2. **HTML(コンテンツ)はブラウザ上ではなくサーバー側で生成される**

MPAは、SPAに対して従来からあったアプリケーションを指す形で誕生したレトロニム[^レトロニム]です。従来の手法では上記のような特徴を持っており、近年でもMPAが「SPAより昔からある」パターンとして説明されることがあります。
なお、HTMLの生成方法がSSRやSSGであっても、ハードナビゲーションを伴う場合は、MPAとして分類されることが多いです。

## Partial Prerendering(PPR)

:::message　alert
Partial Prerendering はNext.jsでは v15.1.2時点で実験的な機能で canary でのみ利用可能であり、PPRがstableな機能として提供される際には機能の一部が変更されている可能性がありますので、ご注意下さい
:::

**意味: 同一のルート(ページ)内でSSR/SSGのようにレンダリングするコンポーネントを両立する手法**

PPRはNext.jsで登場した新しいレンダリング手法です。そしてレンダリングの焦点はサーバーサイド(ビルド時も含む)にあります。
なお、PPRという概念・機能は決してNext.jsに限定したものではなく、以下の記事では Cloudflare Workersと従来のReactのSSRフレームワークで、Reactの prerender() API を使ってPPRを実装する方法が述べられています。
https://sunilpai.dev/posts/ppr-for-everyone/


PPRを文字通り直訳すると部分的(Partial)な事前レンダリング(Prerendering ≒ SSG)となりますが、Next.jsのドキュメントでは、**staticなコンポーネントとdynamicなコンポーネントを同一ルート(ページ)内に共存させることを実現する機能**であると説明されています。

この「static」「dynamic」という言葉の意味は以下のように解釈できます。

| 名称                           | 意味                                                            |
| ------------------------------ | --------------------------------------------------------------- |
| static               | ビルド時にレンダリングされるコンポーネントを指し、従来の SSG (Static Site Generation) に相当する。                              |
| dynamic              | リクエストごとに（サーバーランタイムで）レンダリングされるコンポーネントを指し、従来の SSR (Server-Side Rendering) に相当する。 |

従来のアプリケーションでは、アプリケーション全体あるいはページごとに、static/dynamicなレンダリングをすることを選択する必要がありました。つまり、**ページ全体を SSG あるいは SSR で生成するかを選択していました。**

> For most web apps built today, you either choose between static and dynamic rendering for your entire application, or for a specific route.

> (DeepL 翻訳)
> 現在構築されているほとんどのWebアプリケーションでは、アプリケーション全体または特定のルートに対して、静的レンダリングと動的レンダリングのどちらかを選択します。

https://nextjs.org/learn/dashboard-app/partial-prerendering#static-vs-dynamic-routes

しかし、**PPRではページ内でコンポーネントごとにレンダリングの方法を設定**できるようになりました。これにより、ページの一部はビルド時に生成し、残りの部分はリクエストごとに生成するという柔軟な構成が可能になります。 PPRについてより詳しい解説が気になる方は以下の記事などを参考にしてください。

https://zenn.dev/akfm/articles/nextjs-partial-pre-rendering


:::message
ページ内でレンダリング方法を細かく設定できる点で、アイランドアーキテクチャも似たような概念なのではないかと疑問に思う方がいらっしゃるかもしれませんが、筆者はどちらかというとアイランドアーキテクチャはRSCのほうが近い概念だと認識しています。アイランドアーキテクチャとPPRの関係について気になる方はakfm_sato 氏の[『PPRはアイランドアーキテクチャなのか』](https://zenn.dev/morinokami/articles/astro-server-islands-vs-nextjs-ppr)の記事を参考にしてください。また、PPRと似た概念との比較記事としては、Shinya Fujino氏の [『Next.js PPR と比較して理解する Astro Server Islands』](https://zenn.dev/morinokami/articles/astro-server-islands-vs-nextjs-ppr)でAstro v5の Server Islandsと比較されており参考になると思います。
:::

## 終わりに

今回はレンダリングという言葉を中心にフロントエンドの用語と概念を解説しました。冒頭に述べた通り、本記事では特定の定義を押し付ける意図はなく、これらの用語は人や時代背景によって意味が変化しやすいものであると感じています。内容についてご意見であったり感想がございましたら、お気軽にご連絡ください。
本記事がレンダリングという言葉を通して、皆様のフロントエンドへの理解のお役に立てれば幸いです。

## 謝辞

本記事の作成にあたり、数々のサイトを引用させていただき、また参考にさせていただきました。さらに、何度も壁打ちやレビューにお付き合いいただき、多くの知識を教えてくださった皆様に深くお礼申し上げます。

