## はじめに

FE(フロントエンド) においてレンダー、レンダリングという言葉はさまざまな場面で登場します。さらに、SSR(Server-Side Rendering)のようにレンダリングに関連した用語も多く存在しており、それらの用語の意味も、フロントエンドの技術の進歩に追従して、微妙にニュアンスのようなものが変化してきているように感じています。
では、レンダリングとはそもそもどういう意味なのでしょうか。また SSR や SPA(Single-Page-Application)などといった用語は今日どういった意味で使われているのでしょう。SSR のレンダーは何を指して Rendering としているのでしょうか。
本記事ではレンダリングという言葉を中心に、フロントエンドの用語や概念について振り返っていきます。

なお、本記事ではこの言葉の定義はこうであるべきだと強く主張または是正するような意図はありません。しかし、レンダーという言葉の意味や、例えば SSR という言葉がどのような文脈で使われてきたか、といった疑問を解消する助けになれば幸いです。

(筆者は React/Next.js による開発が主な業務であるため、React に偏った内容になってしまっていますがご了承ください。また、キャッシュ戦略やパフォーマンスなどの最適化観点の話は深くは扱いませんのでご了承ください。)

## 利用する用語の意味

- **サーバーサイド**: Web クライアント（ブラウザなど）に対して、Node.js などのランタイムのこと。React のコードの実行、データのフェッチ、HTML ファイルの生成などの処理を担い、主に BFF（Backend for Frontend）として区別される層にことを指す。
- **クライアントサイド**: ブラウザなどの Web クライアントのこと。

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
- **SSG(Static Site Generation)(Pre-rendering)において**
  - HTML ファイルを生成すること

### レンダリングモデルに関連する用語の意味

- **CSR(Client-Side Rendering)**
  - クライアントサイドで DOM を更新すること
- **SSR(Server-Side Rendering)**
  - サーバーサイドで、毎ページリクエスト時に HTML ファイル(およびそのコンテンツ)を生成すること、あるいはそのレンダリングパターン
- **SSG(Static Site Generation)(Pre-rendering)**
  - build時に HTML ファイル(およびそのコンテンツ)を生成すること、あるいはそのレンダリングパターン
- **SPA(Single-page application)**
  - ソフトナビゲーションで画面回遊するアプリケーション全般
  - サーバーサイドでレンダリングをしない CSR のみのアプリケーション

## 広義的なレンダリングの意味

**意味. データを計算して変換すること**

Web フロントエンド以外でも、3DCG、動画、音声(作曲)のようなデータを取り扱う分野の用語としてもレンダリングという言葉は使われています。

- 3DCG: モデルやシーンの 3D データを計算し画像や映像として出力すること
- 動画: 各フレームを順番に描画して最終的なビデオクリップを生成すること。
- 音声（作曲）: 複数の MIDI データやデジタルオーディオ信号を音声ファイルデータ(mp3 など)として出力すること。

これらの文脈では共通して、**データを計算して変換すること**という意味合いを持ち合わせています。
しかし悪戯に変換しているのではなく、コンピュータ目線では次の段階の処理で解釈可能な形へ、また人間目線では理解や利用が可能な形式へと変換します。

楽曲ファイルの場合は、最終的に人間が楽曲ファイルを再生することができる状態にすることがゴールとなり、そのための音声ファイルデータへの書き出し作業がレンダリングになります。

フロントエンドの文脈では、最終的に視覚情報として、画面上に要素を描画することが目的になるため、レンダリングとは画面に要素を表示するための一過程であると考えることができそうです。

## ブラウザにおいて(The Pixel Pipeline、Rendering Pipeline)

**レンダリングの意味. Web コンテンツを画面上に描画することと**

ブラウザの役割はコンテンツ(HTML など)を画面上に描画することです。具体的には CSS の見た目を適用させたりラスタライズ(ピクセル化)などの処理を行い、最終的には OpenGL のような OS 標準のグラフィックライブラリに描画したページを渡します。
この一連の処理は [^1]**_The Pixel Pipeline_** や、 [^2]**_Rendering Pipeline_** などと呼ばれます。この処理には、ブラウザに搭載されているレンダリングエンジン(Blink など)やグラフィックライブラリ(Skia など)が関与しており、chrome の例では、[^3]以下のスライドが各処理の概略図として参考になります。

![](https://storage.googleapis.com/zenn-user-upload/abc28164dc39-20240919.jpg)

:::message
コンテンツが動的に更新されるということがウェブの特徴であり、画面の更新を最適化するために DOM を含めいくつかの中間表現を持っています。
:::

ブラウザのレンダリングの責務の範囲は、厳密には OS のグラフィックライブラリに HTML などを変換したものを渡すところまでですが、多くの文脈において、ブラウザレンダリングは **単に HTML、DOM を画面上に表示すること** と捉えても差し支えないです。(後述する React においては、ブラウザレンダリングは混乱を避けるために このことは Paint と呼ばれています。)

[^1]: The Pixel Pipeline の引用元: https://web.dev/articles/rendering-performance
[^2]: Rendering Pipeline の引用元(RenderingNG): https://developer.chrome.com/docs/chromium/renderingng-architecture?hl=ja
[^3]: Life of a Pixel の 79 枚目から引用: https://docs.google.com/presentation/d/1boPxbgNrTU0ddsc144rcXayGA_WF53k96imRH8Mp34Y/edit#slide=id.ga884fe665f_64_1800

## React において

**レンダーの意味. React がコンポーネントを呼び出すこと**

これは React の公式ドキュメントにも以下のように明記されています。なお、コンポーネントを「呼び出す」ことを「実行すること」と解釈しても大きな齟齬はないです。(英語ではレンダーではなく Rendering となっています。[^4])

> レンダーとは React がコンポーネントを呼び出すことです
> “Rendering” is React calling your components.

https://ja.react.dev/learn/render-and-commit

そして、React ではレンダーがトリガーされたのち、以下のような 3 つのフェーズに分けて、要素が画面に描画/再描画されます。

| 名称                           | 意味                                                            |
| ------------------------------ | --------------------------------------------------------------- |
| レンダーフェーズ               | コンポーネントの呼び出しが行われる                              |
| コミットフェーズ               | レンダー間で差分があった場合、 変更すべき DOM ノード を変更する |
| ペイント(ブラウザレンダリング) | ブラウザが画面を描画する。(React の責務ではない)                |

つまり、React ではレンダーされたとしても、ReactTree(仮想 DOM)上で差分がなければ DOM が更新されず、画面には反映されないということになります。

:::message
今回の解説では上記の 3 つのフェーズで説明していますが、より細かい実装が気になる方は下記のような他の記事を合わせて参考にしていただければと思います。
:::

https://zenn.dev/aishift/articles/d046335a98bc34

[^4]: https://react.dev/learn/render-and-commit

## CSR(Client-Side Rendering)において

**用語の意味. レンダリングの意味 . クライアントサイドで DOM を更新すること**

ページのコンテンツをクライアントサイド(ブラウザ)でレンダリング(コードを実行して生成)しているためクライアントサイドレンダリングといいます。CSR という言葉は初期表示でも画面回遊時(ナビゲーション時)でも使われます。

CSR の登場以前は、Web サーバーから送られてくる HTML ファイルに、表示したいコンテンツが既に含まれていました。HTML ページ遷移の際も、新たな HTML ファイルが送られてきて画面全体が書き換わるような遷移をします。

一方で、サイト訪問時の表示が CSR である場合は、まずはコンテンツが空の HTML が送られてきます。コンテンツが画面上に反映されるのは、JavaScript や JSON データをサーバーから取得して読み込まれた後で、JavaScript がコンテンツを生成し、後から DOM に反映します。
CSR によるナビゲーションでも JavaScript によって history や DOM を書き換えることで画面遷移を行います。Web サーバーから受け取るのは HTML ファイルではなく、 JSON データになります。

なお、サイト訪問時に空の HTML を返すアプリケーション([^5]SPA)の欠点の一つに、SEO 対策が困難であるという点がありました。(他にもパフォーマンス面やデータ転送面などの欠点もあります。)

[^5]: ここでは、サーバーサイドでレスポンスが返却される前に事前にレンダリングを全く行わない CSR のみのアプリケーションのこと

:::message
Next.js では、CSR による遷移は **ソフトナビゲーション(Soft Navigation)** と呼ばれています。またソフトナビゲーションとは対照に Web サーバから送信された HTML ファイルに置き換えるような MPA 的な遷移は**ハードナビゲーション(Hard Navigation)** と言います。
:::

## SSR(Server-Side Rendering)において

**用語の意味. サーバーサイドで、毎ページリクエスト時に HTML ファイル(およびそのコンテンツ)を生成すること、あるいはそのレンダリングパターン**

**レンダリングの意味. HTML ファイルを生成すること**

レンダリングという言葉の対象は、HTML ファイル(およびそのコンテンツ)です。
SSR をする主な目的は以下の二つになります。

1. 動的サイトを作ること(On-demand)
2. コンテンツを含む HTML ファイルを作ること(SEO 観点)

SSR という言葉は CSR(SPA)の登場と同時に[^6]レトロ二ムとして生まれたものですが、現在では、CSR のみでは解決できない問題を解決するものという立ち位置になっています。
また、昨今はこの用語が利用される場面では、CSR するフレームワークである場面も多く、元々 SSR with Hydration と呼ばれていたレンダリングパターンを単に SSR と呼ぶことが多くなっており、その変遷について述べていきます。

:::message
サーバーサイドで生成できる HTML ファイルは静的なものにすぎないため、ブラウザ上でも再度コードを実行してページをインタラクティブにする Hydration(ハイドレーション) という工程が必要になります。
:::

:::message
(SSR の意味として、ラフにコンポーネントを対象に、サーバーサイドでレンダーすることを SSR という使われ方がされることもあります。)
:::

:::message
当記事では、基本は CSR で画面遷移をしつつ、ページ訪問時の時には HTML をオンデマンドにサーバーサイドで生成するようなアプリケーションのことを **SSR(SSR with hydration)** であるとしていますが、例えば、そこから敢えてソフトナビゲーションを封印した場合や、CSR が登場する前のような MPA はなんと呼ぶのかを取り扱うと議論が複雑化してしまうため触れません。
:::

### SSR という言葉の変遷

#### 初期の SSR の意味

**意味. サーバーサイドで HTML ファイルを生成すること**

もともと SSR という言葉は、SPA(Single-page application)が台頭してきたときに、CSR(Client-Side Rendering)という言葉の[^6]レトロニムとして生まれた言葉のようです。
この頃の SSR は、サーバーサイドのテンプレート言語(Java,PHP,Ruby on Rails 等)で HTML ファイルを生成することを指していました。

CSR のようにサイト訪問時/画面遷移時にクライアントサイドで DOM を直接書き換えてコンテンツを作る方法は当時は一般的ではなく、従来の方法ではサーバーサイドでコンテンツを含んだ HTML ファイルを配信して、ブラウザでは HTML ファイルごと入れ替えるような形で画面遷移をしていました。

[^6]: レトロニム:時代の変化により新しい事物が生まれたことから、既存の事物を新しいものと区別するため「後から」つくられた言葉

#### SSR with Hydration の出現

現在のような SSR フレームワークの起源の一つであると考えられるものとして Airbnb 社の Backbone.js のフレームワーク、**Rendr** があります。
Backbone.js は 古い SPA フレームワークの 1 つで、クライアントサイド MVC モデルです。

当時の CSR のアプリケーションは、ページ訪問時にコンテンツが空の HTML ファイルが最初に送られるため、**SEO**の観点で課題がありました。

その対策のために、サイト訪問時用に 別言語(Ruby など)で開発したサーバーを用意したり、ヘッドレスブラウザのようなものを挟み JavaScript を事前に実行するなどをして、 コンテンツを含む HTML を生成していました。

(ダイナミックレンダリング)
https://developers.google.com/search/docs/crawling-indexing/JavaScript/dynamic-rendering?hl=ja

(JavaScript を実行して HTML を生成してくれるサービス)
https://prerender.io/

そこで Rendr が取ったアプローチは、Node.js を採用し、それまでに Ruby と JavaScript の二重開発で共通していたロジックを、クライアントサイドとサーバーサイドの両方で実行できる共通のコードとすることでした。

:::message
当時は**同じコード**がサーバーサイドでもクライアントサイドでも実行されることを **Isomorphic**と呼びました。現在でもサーバーサイド、クライアントサイドの両側で実行される概念に対して Isomorphic という言葉が使われることがあります。
:::
https://frontendmasters.com/blog/introducing-tanstack-start/#isomorphic-loaders

なお、Rendr の作者の Rendr の公開スライドに、すでに Hydrate という言葉が出ていることから、クライアントサイドで要素をインタラクティブにするフェーズを Hydrate という文化はこの時にはすでに存在していたようです。

以下の Rendr の記事は React が公開される 2013 年 5 月よりも前のものなので、React の SSR フレームワークが普及する遥か前にすでに現在のような SSR フレームワークが一度完成していたと言っても良いかもしれません。(結局この Rendr は React の流行の前に主流となることはなく、Airbnb も React に移行することになったようです。)

https://medium.com/airbnb-engineering/our-first-node-js-app-backbone-on-the-client-and-server-c659abb0e2b4

https://www.slideshare.net/slideshow/introducing-rendr-run-your-backbonejs-apps-on-the-client-and-server/19106546

:::message
この Rendr のような、サーバーサイド、クライアントサイドで共通のコードを実行するような SSR は、 SPA より前から存在した SSR と差別化するため SSR with Hydration などと呼ばれたりしました。
:::

#### 現在の SSR

**用語の意味(再掲). サーバーサイドで、毎ページリクエスト時に HTML ファイル(およびそのコンテンツ)を生成すること、あるいはそのレンダリングパターン**

現在では、フロントエンド開発において React のような SPA の技術が普及し、さらには Next.js や Remix といった サーバーサイドでも JavaScript/React のコードが動作するフレームワークが主流となりました。これらのフレームワークはサーバーサイドにランタイムを設けることで、SPA の利点を享受しつつ、従来のSPAでは解決が難しかった課題の克服や最適化を実現しようとしています。

近年 SSR が言及される文脈は、主にこれらのフレームワークに関連する場合が多く、SPA登場当初とは想定場面などの前提が変化したため、初期に SSR with Hydration と呼ばれていたものが、現在では単に SSR と呼ばれるようになってきています。

元々 SSR という言葉が誕生した CSR(SPA)台頭の時点では、本来サーバーサイドで行われていたコンテンツの生成処理をクライアントサイドで行うという変化がありました。
しかし、現代におけるSSRという用語は、**本来クライアントサイドで実行されるコード**を、**サイト訪問時に限りサーバーサイドでレンダリングする**という、方向性の逆転が起きた後の意味合いを持つようになったと解釈できそうです。

:::message
Astro と SSR
MPA のフレームワークである Astro でも SSR は [On-demand Rendering](https://docs.astro.build/en/guides/on-demand-rendering/) という概念として存在しており、ページリクエストに応じてサーバーサイドで HTML を生成することを意味します。 
Astro は当初、Static Site Generation (SSG) に特化したフレームワークとして登場し、SPA に対抗し、従来の MPA のようにページ遷移を行いながらも、JavaScript 以外の言語との二重開発が不要であることを利点としています。
SSG の静的ページのみであれば、ビルド時に生成した静的 HTML を配信し、外部 API はブラウザで処理できるため、Node.js 等のランタイムは不要ですが、On-demand Rendering が追加され、本来持たなかったサーバーサイドランタイムを持つという手段をとることで、これまでクライアントサイドで行っていた動的なコンテンツ生成 (CSR) をサーバーサイドで行い、コンテンツを含む HTML ファイルを配信できるようになりました。
よって Astro の On-demand Rendering が解決した問題も Next.Js などの SSR with Hydration が解決したかった課題と全く違った方向を向いているわけではないと筆者は考えています。　
:::


### RSC の ServerComponents は SSR なのか？

RSC(React Server Components)の登場前までは、すべてのコンポーネントが一律に基本的にはクライアントサイドで、SSR/SSG の時は、サーバーサイド/ビルド時のランタイムでも実行されていました。しかし、RSC の世界では、従来通りのコンポーネントである **Client Components(CC)**と、完全にサーバーサイドでしか実行されない **Server Components(SC)**に別れました。

では、**SC は SSR と同一の概念なのか？** という疑問に対しては、React 公式に回答があり、併用できる概念であると説明されています。
あくまで、SSR は HTML をサーバーサイドで事前に生成(レンダリング)する従来通りの意味であるため、SC がレンダーされること自体を SSR としていません。

https://github.com/reactjs/rfcs/blob/main/text/0188-server-components.md#does-this-replace-ssr

> Does this replace SSR?
> No, they’re complementary. SSR is primarily a technique to quickly display a non-interactive version of client components. You still need to pay the cost of downloading, parsing, and executing those Client Components after the initial HTML is loaded.
> You can combine Server Components and SSR, where Server Components render first, with Client Components rendering into HTML for fast non-interactive display while they are hydrated. When combined in this way you still get fast startup, but you also dramatically reduce the amount of JS that needs to be downloaded on the client.

> (DeepL 翻訳)
> これは SSR を置き換えるものですか？
> いいえ。SSR は主にクライアントコンポーネントの非インタラクティブバージョンを素早く表示するテクニックです。最初の HTML が読み込まれた後、それらのクライアントコンポーネントをダウンロードし、解析し、実行するコストはまだ必要です。
> サーバコンポーネントと SSR を組み合わせることができます。 サーバコンポーネントが最初にレンダリングされ、クライアントコンポーネントは HTML にレンダリングされます。このように組み合わせることで、高速なスタートアップが得られるだけでなく、 クライアントにダウンロードされる JS の量を劇的に減らすことができます。

:::message
RSC の SC は、RSC 以前のコンポーネント(つまり CC)と異なり、SSR 時にもハイドレーションされず、ソフトナビゲーション時もサーバーサイドでしか実行されません。

第一の理由として、SCはHooksやイベントハンドラなどの**インタラクティブ**な要素を持たないため、クライアントサイドでの状態管理や更新が不要となります。これにより、サーバーサイドでレンダリングを完結させることができ、その結果はクライアントサイドの仮想DOM（React Tree）にそのまま適用されます。

第二の理由として、**データ取得方法の変化**が挙げられます。従来のSSRでは、初期表示に必要なデータはフレームワークが提供するデータ取得関数（Next.jsのgetServerSidePropsやRemixのloader関数など）を用いて、コンポーネントの外側で取得されていました。このデータ取得はソフトナビゲーション時にもサーバーサイドで実行され、取得したデータをPropsとしてコンポーネントに渡してレンダリングする、いわゆる「Fetch-then-render」という方式が用いられていました。

**従来: フレームワークによるデータ取得 → コンポーネントのレンダリング**

一方で RSC の場合、SC が非同期コンポーネントとなり、SC 自身がデータ取得を行うことができるようになりました。

これらの理由により、SCはデータ取得からレンダリングまでをサーバーサイドで完結させることが可能となり、従来のFetch-then-renderとは異なるアプローチを実現しています。  
これが、RSCにおいてSSRおよびソフトナビゲーション時のコンポーネントのレンダリング場所に変化が生じた理由です。
:::

## SSG(Static Site Generation)(Pre-rendering)

**用語の意味. build時に HTML ファイル(およびそのコンテンツ)を生成すること、あるいはそのレンダリングパターン**

**レンダリングの意味. HTML ファイルを生成すること**

Static Site Generatorsとも。
海外では、SSGは単にPre-renderingとも呼ばれてきました。

SSGでは、build時にフレームワークによるデータフェッチやコンポーネントのレンダーが行われ、HTML(コンテンツ)が生成されます。静的なHTML(コンテンツ)を取り扱うことになるため、CDNなどで比較的容易にキャッシュができるといった利点が挙げられます。また、SSGという用語自体は、[^7]ハイドレーションの有無に限らず広く使われているようです。

:::message
SSRやSSGをまとめて事前レンダリングと呼ぶことは、国内では近年に至るまで少なからず見かけました。しかし、近年 Next.jsやRemix の機能などで、Pre-renderingという用語はbuild時にレンダリングをする前提の意味で使われています。
この場合、「事前」はリクエスト時よりも前、つまりビルド時を指していると解釈できます。
:::

:::message
SSG、SSRのアプリケーションでソフトナビゲーションをする際は、Webサーバーからは初期表示とは異なりHTMLファイルではなくjsonデータなどが配信されます。よってSSGの場合はbuild時に、サイト訪問時用のHTMLとCSR時用のファイルの両方が生成されることになります。
:::

:::message
ServerComponents(SC)のドキュメントに、[サーバを利用しないサーバーコンポーネント](https://ja.react.dev/reference/rsc/server-components#server-components-without-a-server) という一見意味が分かりにくい段落が存在しますが、ここにおけるサーバーはウェブサーバ(BFFなどに該当するサーバーサイドのランタイム)のことを指しており、SSGのようにbuild時にSCをレンダーすることを指しています。(つまり、ブラウザ上でSCが独立してレンダーするようになるわけではないです。)
:::

[^7]: [state of js 2024](https://survey.devographics.com/en-US/survey/state-of-js/2024/outline/9)ではSSGは、Static content pre-rendered at build time, with or without a client-side dynamic element　とされています。


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

**用語の意味. 同一のルート(ページ)内でビルド時/毎リクエスト時 にサーバーサイドでレンダーされるコンポーネントを組み合わせるレンダリングモデル**

:::message
従来の用語と意味の粒度が揃っていない理由は、PPR のようなレンダリングモデルでは、従来の用語の定義のようにページ全体がどのようにレンダリングされているのかという大まかな分類では捉えきれなくなってきたためです。
:::

PPRはNext.jsの提供する実験的な機能であり、新たなレンダリングモデルです。そしてレンダリングの焦点はサーバーサイド(ビルド時も含む)にあります。

PPRを文字通り直訳すると部分的(Partial)な事前レンダリング(Prerendering ≒ SSG)となりますが、公式のドキュメントでは、staticなコンポーネントとdynamicなコンポーネントを、同一ルート(ページ)内に共存させることを実現する機能だと説明されています。

ここで、重要なのは「static」「dynamic」の意味です。

- static: ビルド時にレンダリングされるコンポーネントを指し、従来の SSG (Static Site Generation) に相当する。
- dynamic: リクエストごとに（サーバーランタイムで）レンダリングされるコンポーネントを指し、従来の SSR (Server-Side Rendering) に相当する。

従来のアプリケーションでは、アプリケーション全体あるいはページごとに、static/dynamicなレンダリングをすることを選択する必要がありました。つまり、ページ全体を SSG で生成するか、SSR で生成するかを選択していました。

> For most web apps built today, you either choose between static and dynamic rendering for your entire application, or for a specific route.

> (DeepL 翻訳)
> 現在構築されているほとんどのWebアプリケーションでは、アプリケーション全体または特定のルートに対して、静的レンダリングと動的レンダリングのどちらかを選択します。

https://nextjs.org/learn/dashboard-app/partial-prerendering#static-vs-dynamic-routes

しかし、PPRにより、ページ内でもより細かくコンポーネントのレンダーの仕方を設定できるようになりました。これにより、ページの一部はビルド時に生成し、残りの部分はリクエストごとに生成するという柔軟な構成が可能になります。 より詳しい解説が気になる方は以下の記事などを参考にしてください。

https://zenn.dev/akfm/articles/nextjs-partial-pre-rendering


:::message
ページ内でレンダリング方法を細かく設定できる点で、アイランドアーキテクチャも似たような概念なのではないかと疑問に思う方がいらっしゃるかもしれませんが、筆者はどちらかというとアイランドアーキテクチャはRSCのほうが近い概念だと認識しています。アイランドアーキテクチャとPPRの関係について気になる方はakfm_sato 氏の[『PPRはアイランドアーキテクチャなのか』](https://zenn.dev/morinokami/articles/astro-server-islands-vs-nextjs-ppr)の記事を参考にしてください。また、PPRと似た概念との比較記事としては、Shinya Fujino氏の [『Next.js PPR と比較して理解する Astro Server Islands』](https://zenn.dev/morinokami/articles/astro-server-islands-vs-nextjs-ppr)でAstro v5の Server Islandsと比較されており参考になると思います。
:::


## まとめ

今回はレンダリングという言葉を中心にフロントエンドの用語と概念を解説しました。冒頭に述べた通り、本記事では特定の定義を押し付ける意図はなく、これらの用語は、人や時代背景によって意味が変化しやすいものであると感じています。内容についてご意見であったり感想がございましたら、お気軽にご連絡ください。
本記事がレンダリングという言葉を通して、皆様のフロントエンドへの理解のお役に立てれば幸いです。

## 謝辞

本記事の作成にあたり、壁打ちやレビュー、解説等のご協力をいただいた皆様に深く感謝申し上げます。
