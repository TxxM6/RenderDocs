## はじめに

FE(フロントエンド) においてレンダー、レンダリングという言葉はさまざまな場面で登場します。
自分は元々なんとなく描画する、画面上に表示するというイメージでレンダーと口にしていていましたが、例えば React ではコンポーネントがレンダーされても画面上の変化が起こらないことがあります。
では、レンダリングとはそもそもどういう意味なのでしょうか。また、SSR(Server-Side Rendering) という用語についても、サーバーサイドで実行されれば SSR と呼べるのでしょうか。

本記事は、この言葉の定義はこうであるべきだと強く主張または是正するような意図はありません。しかしこの記事が、レンダーという言葉の意味や、SSR ってどんな文脈な言葉だったかという疑問を解消する助けになれば幸いです。

(筆者は React/Next.js での開発が主な業務であるため、React に偏った内容になってしまっていますがご了承ください。)

## 要約

レンダリングの意味

- 広義
  - データを計算して変換すること
- ブラウザにおいて
  - Web コンテンツを画面上に描画すること
- React において
  - コンポーネントを呼び出す(実行する)こと
- CSR(Client-Side Rendering)において
  - クライアントサイドで DOM を更新すること
- SSR(Server-Side Rendering)において
  - サーバーサイドで HTML を生成すること

## 広義的なレンダリングの意味

**意味. データを計算して変換すること**

Web フロントエンド以外でも、3DCG、動画、音声(作曲)のようなデータを取り扱う分野の用語としてもレンダリングという言葉は使われています。

- 3DCG: モデルやシーンの 3D データを計算し画像や映像として出力すること
- 動画: 各フレームを順番に描画して最終的なビデオクリップを生成すること。
- 音声（作曲）: 複数の MIDI データやデジタルオーディオ信号を音声ファイルデータ(mp3 など)として出力すること。

これらの用語では共通して、**データを計算して変換すること**という意味合いを持ち合わせています。
しかし悪戯に変換しているのではなく、コンピュータ目線では次の段階の処理で解釈できる形へ、人間目線では人間が理解(利用)可能な形式へ向かって変換します。

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

ブラウザのレンダリングの責務の範囲は、厳密には OS のグラフィックライブラリに HTML などを変換したものを渡すところまででありますが、多くの文脈において、ブラウザレンダリングは **単に HTML、DOM を画面上に表示すること** と捉えても差し支えないです。(後述する React においては、ブラウザレンダリングは混乱を避けるために このことは Paint と呼ばれています。)

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

細かい内部的な仕組みを掘り下げたい方は以下の記事が参考になりましたのでお勧めさせていただきます。(なお、本投稿とフェーズの区分が異なることにご注意ください。)
https://zenn.dev/aishift/articles/d046335a98bc34

[^4]: https://react.dev/learn/render-and-commit

## CSR(Client-Side Rendering)において

**レンダリングの意味. クライアントサイドで DOM を更新すること**

ページのコンテンツをクライアントサイド(ブラウザ)でレンダリング(コードを実行して生成)しているためクライアントサイドレンダリングといいます。CSR という言葉は初期表示でも画面回遊時(ナビゲーション時)でも使われます。

CSR の登場以前は、Web サーバーから送られてくる HTML ファイルに、表示したいコンテンツが既に含まれていました。HTML ページ遷移の際も、新たな HTML ファイルが送られてきて画面全体が書き換わるような遷移をします。

一方で、サイト訪問時の表示が CSR である場合は、まずはコンテンツが空の HTML が送られてきます。コンテンツが画面上に反映されるのは、JavaScript や JSON データをサーバーから取得し読み込まれた後で、JavaScript がコンテンツを生成し、後から DOM に反映します。
CSR によるナビゲーションでも JavaScript によって history や DOM を書き換えることで画面遷移を行います。Web サーバーから受け取るのは HTML ファイルではなく、 JSON データになります。

なお、サイト訪問時に空の HTML を返すアプリケーション([^5]SPA)の欠点の一つに、SEO 対策が困難であるという点がありました。(他にもパフォーマンス面やデータ転送面などの欠点もあります。)

[^5]: ここでは、サーバーサイドでレスポンスが返却される前に事前にレンダリングを全く行わない CSR のみのアプリケーションのこと

:::message
Next.js では、CSR による遷移は **ソフトナビゲーション(Soft Navigation)** と呼ばれています。またソフトナビゲーションとは対照に Web サーバから送信された HTML ファイルに置き換えるような MPA 的な遷移は**ハードナビゲーション(Hard Navigation)** と言います。
:::

## SPA(Single-page application)

ここで SPA について触れておきたいと思います。私の観測する限り、SPA という用語の使われ方は大きく分けて二つに分かれています。

1. ソフトナビゲーションで画面回遊するアプリケーション全般(SSR/SSG と共存する概念)
2. CSR のみのサーバーサイドでレンダリングをしないアプリケーション(SSR/SSG と排他的関係の概念)

1 については、HTML ファイルがサイト訪問時の一回を除いては２度と送られてこないアプリケーションということになり、ページごとに HTML ファイルを都度配信する MPA(Multi-page application)との対比として捉えることができます。

2 については、事前レンダリングをしない、Node.js サーバーを持たない 完全にブラウザ上でのみ動作する CSR のみのアプリケーションということになります。

2 の用例は海外で特に見られる傾向にあり、例えば State of JavaScript のアンケートでの SPA の定義や Remix の SPA モードの意味では、 SSR/SSG と横並びになるようなものとなっています。

> SPA: Apps that run entirely in the browser
> MPA: Apps that run entirely in the server, with minimal client-side dynamic behavior

https://survey.devographics.com/en-US/survey/state-of-js/2023/outline/9

## SSR(Server-Side Rendering)

**レンダリングの意味. サーバーサイドで HTML ファイルを生成すること**

**現在の SSR の主流の意味. ナビゲーション時にはブラウザ上で実行されるコードを、ページリクエスト時にはサーバーサイドで実行して HTML を生成すること、あるいはそのレンダリングパターン**

ナビゲーション時には CSR である(ブラウザで実行される)ことが前提となります。つまり、同じコードがサーバーサイドでもクライアントサイドでも実行されることになります。

ブラウザに返す HTML を SSR する目的は、サイト訪問時に空の HTML ではなく、コンテンツが含まれた HTML を返すためです。

:::message
サーバーサイドで生成できる HTML ファイルは静的なものにすぎないため、ブラウザ上でも再度コードを実行してページをインタラクティブにする Hydration(ハイドレーション) という工程が必要になります。
:::

### SSR という言葉の変遷

#### 初期の SSR の意味

**意味. サーバーサイドで HTML ファイルを生成すること**

もともと SSR という言葉は、SPA(Single-page application)が台頭してきたときに、CSR(Client-Side Rendering)という言葉の[^6]レトロニムとして生まれた言葉のようです。
この頃の SSR は、サーバーサイドのテンプレート言語(Java,PHP,Ruby on Rails 等)で HTML ファイルを生成するものを指していました。

[^6]: レトロニム:時代の変化により新しい事物が生まれたことから、既存の事物を新しいものと区別するため「後から」つくられた言葉

#### SSR with Hydration の出現

現在の SSR フレームワークの起源の一つであると考えられるものとして Airbnb 社の Backbone.js のフレームワーク、**Rendr** があります。
Backbone.js は 古い SPA フレームワークの 1 つで、クライアントサイド MVC モデルです。

当時の CSR のアプリケーションは、ページ訪問時にコンテンツが空の HTML ファイルが最初に送られるため、**SEO**の観点で課題がありました。

その対策のために、サイト訪問時用に 別言語(Ruby など)で開発したサーバーを用意したり、ヘッドレスブラウザのようなものを挟み JavaScript を事前に実行するなどをして、 コンテンツを含む HTML を生成していました。

(ダイナミックレンダリング)
https://developers.google.com/search/docs/crawling-indexing/JavaScript/dynamic-rendering?hl=ja

(JavaScript を実行して HTML を生成してくれるサービス)
https://prerender.io/

そこで Rendr が取ったアプローチは、Node.js を採用し、それまでに Ruby と JavaScript の二重開発で共通していたロジックを、クライアントサイドとサーバーサイドの両方で実行できる共通のコードとすることでした。

:::message
当時は**同じコード**がサーバーサイドでもクライアントサイドでも実行されることを Isomolphic と呼びました。
:::

なお、Rendr の作者の Rendr の公開スライドに、すでに Hydrate という言葉が出ていることから、クライアントサイドで要素をインタラクティブにするフェーズを Hydrate という文化はこの時にはすでに存在していたようです。

以下の Rendr の記事は React が公開される 2013 年 5 月よりも前のものなので、React の SSR フレームワークが普及する遥か前にすでに現在のような SSR フレームワークが一度完成していたと言っても良いかもしれません。(結局この Rendr は React の流行の前に主流となることはなく、Airbnb も React に移行することになったようです。)

https://medium.com/airbnb-engineering/our-first-node-js-app-backbone-on-the-client-and-server-c659abb0e2b4

https://www.slideshare.net/slideshow/introducing-rendr-run-your-backbonejs-apps-on-the-client-and-server/19106546

:::message
この Rendr のような、サーバーサイド、クライアントサイドで共通のコードを実行するような SSR は、 SPA 以前から存在した SSR と差別化するため**_SSR with Hydration_**などと呼ばれたりしました。
:::

#### 現在の SSR

**現在の SSR の主流の意味(再掲). ナビゲーション時にはブラウザ上で実行されるコードを、ページリクエスト時にはサーバーサイドで実行して HTML を生成すること、あるいはそのレンダリングパターン**

Next.js や Remix のような フレームワーク が主流になり、Node.js でもブラウザでも React のコードが動作することが当たり前となった現在では、**CSR をするアプリケーションであることが前提となる用語の一つ**として使われるようになり、初期に SSR with Hydration と呼ばれているものが単に SSR と呼ばれるようになりました。

元々 SSR という言葉が誕生した CSR(SPA)台頭の時点では、むしろ、サーバーサイドでコンテンツ(HTML)を生成することが一般的であり、敢えてクライアントサイドでコンテンツをレンダリングするという点が用語の焦点になっていました。
しかし、現在の SSR という用語は、アプリケーションが CSR をすることを前提に、**本来はクライアントで動くコード**をサイト訪問時だけ、**敢えてサーバーサイドでレンダリングする**というニュアンスに変わったのです。

#### 現在の SSR と SSG と SPA

SSR のレンダリングはサイト訪問時のリクエストのたびに行われます。一方で、HTTP リクエストよりも事前、つまり build 時にレンダリングするパターンを、SSG(StaticSite Generation = pre-Rendering)と言います。さらに、海外を中心に、サーバーサイドで事前にレンダリングしないモデル(完全にクライアントで動作するアプリケーション)のことを SPA と呼ぶようになっており、これらの用語の差異を決定づけるものは、サイト訪問時の HTML の生成(レンダリング)の仕方になっています。

| 名称 | 意味                                                                                                |
| ---- | --------------------------------------------------------------------------------------------------- |
| SPA  | ブラウザ上でのみ動作するクライアントサイドレンダリング (CSR) アプリケーション                       |
| SSR  | 毎アクセス時にサーバーサイドでレンダリングするクライアントサイドレンダリング (CSR) アプリケーション |
| SSG  | ビルド時にサーバーサイドでレンダリングするクライアントサイドレンダリング (CSR) アプリケーション     |

#### 未来の SSR の意味

RSC(React Server Components)の世界では、従来はすべてのコンポーネントが原則クライアントサイドで、事前レンダリング時はサーバーサイドで実行されていたものが、完全にサーバーサイドでしか実行されない Server Components(SC)と従来通りの Client Components(CC)に別れました。

まず、**SC と SSR は一緒なのか？** という疑問に対しては、React 公式が回答しており、併用できる概念であると説明しています。
あくまで、SSR は HTML をサーバーサイドで事前に生成(レンダリング)する従来通りの意味であるため、SC がレンダーされること自体を SSR としていません。

https://github.com/reactjs/rfcs/blob/main/text/0188-server-components.md#does-this-replace-ssr

> Does this replace SSR?
> No, they’re complementary. SSR is primarily a technique to quickly display a non-interactive version of client components. You still need to pay the cost of downloading, parsing, and executing those Client Components after the initial HTML is loaded.
> You can combine Server Components and SSR, where Server Components render first, with Client Components rendering into HTML for fast non-interactive display while they are hydrated. When combined in this way you still get fast startup, but you also dramatically reduce the amount of JS that needs to be downloaded on the client.

> (DeepL 翻訳)
> これは SSR を置き換えるものですか？
> いいえ。SSR は主にクライアントコンポーネントの非インタラクティブバージョンを素早く表示するテクニックです。最初の HTML が読み込まれた後、それらのクライアントコンポーネントをダウンロードし、解析し、実行するコストはまだ必要です。
> サーバコンポーネントと SSR を組み合わせることができます。 サーバコンポーネントが最初にレンダリングされ、クライアントコンポーネントは HTML にレンダリングされます。このように組み合わせることで、高速なスタートアップが得られるだけでなく、 クライアントにダウンロードされる JS の量を劇的に減らすことができます。

しかし、RSC という新たな概念により、従来にはなかったレンダリングのパターンが出現し、従来の用語の定義と比べて新たな疑問を抱く人も現れています。
例えば SC は 非インタラクティブであり、Hydration が原理的に行われないため、SC のみで構成したページは SSR と言えるのでしょうか。RSC では、画面回遊時にも サーバーサイドで レンダーされるコンポーネントが存在するようになりましたが、従来通り CSR という言葉が適切なのでしょうか。
本記事では詳しく触れることができませんでしたが、実際に Next.js の v14 以降では PPR(Partial-Pre-Rendering)という新たなレンダリングモデルが登場しました。

https://zenn.dev/akfm/articles/nextjs-partial-pre-rendering

上記の記事に書かれているように、現在 App Router は SSR/SSG/ISR ではなく、[static rendering](https://nextjs.org/docs/app/building-your-application/rendering/server-components#static-rendering-default)と [dynamic rendering](https://nextjs.org/docs/app/building-your-application/rendering/server-components#dynamic-rendering) という 2 つの概念を使って多くの機能を説明しています。

> - static rendering: 従来の SSG や ISR 相当で、build 時や revalidate 実行後にレンダリング
>   - revalidate なし: SSG 相当
>   - revalidate あり: ISR 相当
> - dynamic rendering: 従来の SSR 相当で、リクエストごとにレンダリング

今日までも SSR という言葉の意味が変化してきたように、これからも今の意味から変わっていくかもしれません。

## まとめ

フロントエンドにおいて画面に要素を表示することが一つの責務になっています。
レンダリングでは処理が実行されているわけですが、文脈によって、対象の範囲がコンポーネントであったり、HTML ファイル(ページ全体)であったり、画面に反映されなかったりすることがわかったと思います。
本記事がレンダリングという言葉を通してフロントエンドの理解の助けとなったなら幸いです。
