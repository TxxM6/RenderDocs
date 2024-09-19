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
- ブラウザ
  - Web コンテンツを画面上に描画すること
- React
  - コンポーネントを呼び出す(実行する)こと
- CSR
  - クライアントサイドで javaScript によってコンテンツを生成し DOM を更新すること
- SSR(事前レンダリング)
  - Isomolphic なコードをサーバーサイドで実行して事前に HTML を生成すること

## レンダリングの広義的な意味

**意味. データ、情報を計算して変換すること**

Web フロントエンド以外でも、3DCG、動画、音声(作曲)のようなデータを取り扱う分野の用語としてもレンダリングという言葉は使われています。

> - 3DCG: モデルやシーンの 3D データを計算し画像や映像として出力すること
> - 動画: 各フレームを順番に描画して最終的なビデオクリップを生成すること。
> - 音声（作曲）: 複数の MIDI データやデジタルオーディオ信号を音声ファイルデータ(mp3 など)として出力すること。

これらの用語では共通して、**データを計算して変換すること**という意味合いを持ち合わせています。
しかし悪戯に変換しているのではなく、コンピュータ目線では次の段階の処理で解釈できる形へ、人間目線では人間が理解(利用)可能な形式へ向かって変換します。

楽曲ファイルの場合は、最終的に人間が楽曲ファイルを再生することができる状態にすることがゴールとなり、そのための音声ファイルデータへの書き出し作業がレンダリングになります。

フロントエンドの文脈では、最終的に視覚情報として、画面上に要素を描画することが目的になるため、レンダリングとは画面に要素を表示するための一過程であると考えることができそうです。

## ブラウザにおけるレンダリング(The Pixel Pipeline、Rendering Pipeline)

**意味. HTML(あるいは DOM)を画面上に描画すること**

ブラウザの役割はコンテンツ(HTML など)を画面上に描画することです。具体的には CSS の見た目を適用させたりラスタライズ(ピクセル化)などの処理を行い、最終的には OpenGL のような OS 標準のグラフィックライブラリに描画したページを渡します。
この一連の処理は [^1]**_The Pixel Pipeline_** や、 [^2]**_Rendering Pipeline_** などと呼ばれます。(コンテンツが動的に更新されるということがウェブの特徴であり、画面の更新を最適化するために DOM を含めいくつかの中間表現を持っています。)

この処理には、ブラウザに搭載されているレンダリングエンジン(Blink など)やグラフィックライブラリ(Skia など)が関与しており、chrome の例では、[^3]以下のスライドが各処理の概略図として参考になります。

![](https://storage.googleapis.com/zenn-user-upload/abc28164dc39-20240919.jpg)

ブラウザのレンダリングの責務の範囲は、厳密には OS のグラフィックライブラリに HTML などを変換したものを渡すところまででありますが、多くの文脈において、ブラウザレンダリングは **単に HTML、DOM を画面上に表示すること** と捉えても差し支えないです。(後述する React においては、ブラウザレンダリングは混乱を避けるために このことは Paint と呼ばれています。)

[^1]: The Pixel Pipeline の引用元: https://web.dev/articles/rendering-performance
[^2]: Rendering Pipeline の引用元(RenderingNG): https://developer.chrome.com/docs/chromium/renderingng-architecture?hl=ja
[^3]: Life of a Pixel の 79 枚目から引用: https://docs.google.com/presentation/d/1boPxbgNrTU0ddsc144rcXayGA_WF53k96imRH8Mp34Y/edit#slide=id.ga884fe665f_64_1800

## React におけるレンダー

**意味. React がコンポーネントを呼び出すこと**

これは React の公式ドキュメントにも以下のように明記されています。なお、コンポーネントを呼び出すことを実行すること、と解釈しても大きな齟齬はないです。(英語ではレンダーではなく Rendering となっています。)

> レンダーとは React がコンポーネントを呼び出すことです
> “Rendering” is React calling your components.

https://ja.react.dev/learn/render-and-commit

https://react.dev/learn/render-and-commit

そして、React では以下のような 4 つのフェーズに分けて、インタラクティブな要素が画面に描画/再描画されます。

| トリガー                                                | レンダーフェーズ                             | コミットフェーズ                                     | ペイント(ブラウザレンダリング)                     |
| ------------------------------------------------------- | -------------------------------------------- | ---------------------------------------------------- | -------------------------------------------------- |
| 初回マウント時や Set 関数など、レンダーをトリガーする。 | レンダー(コンポーネントの呼び出し)が行われる | レンダー間で差があった場合のみ DOM ノード を変更する | ブラウザが画面を再描画する。(React の責務ではない) |

つまり、React ではレンダー(コンポーネントに呼び出し)がされたとしても、ReactTree(仮想 DOM)上で差分がなければ DOM が更新されず、画面には反映されないということになります。

細かい内部的な仕組みを掘り下げたい方は以下の記事が参考になりましたのでお勧めさせていただきます。(なお、本投稿とは別の 4 フェーズの区分の仕方がされていますことにご留意ください。)
https://zenn.dev/aishift/articles/d046335a98bc34

## CSR(Client-Side Rendering)の意味

**意味. クライアントサイドで javaScript によってコンテンツを生成し DOM を更新すること**

ページのコンテンツをクライアントサイド(ブラウザ)でレンダリング(生成)しているためクライアントサイドレンダリングです。
CSR という言葉は初期表示でも画面回遊時(ナビゲーション時)でも使われます。CSR でのナビゲーションは Next.js ではソフトナビゲーション(Soft Navigation)と呼ばれます。(対照的に MPA 的遷移はハードナビゲーションと言います。)

CSR の登場以前は、コンテンツを含む HTML と、ページをインタラクティブにするための JQuery などのファイルを別々に作成するのが主流でした。ページ遷移の際も、新たな HTML ファイルが送られてきてそれと置き換わるような遷移をします。
一方で、 ソフトナビゲーションでは、HTML ファイルではなく js や json のデータをサーバーから取得し、クライアントサイドでスクリプトが実行されコンテンツが生成されます。  
サイト訪問時の表示が CSR である場合は、コンテンツが空の HTML が送られてきたのちに、js や json データをサーバーから取得し読み込まれ、コンテンツをクライアント上で生成します。

なお、サイト訪問時に空の HTML を返すアプリケーション(SPA(※))の欠点の一つに、SEO 対策が困難であるという点がありました。(他にもパフォーマンス面やデータ転送面などの欠点もあります。)

よって、CSR という言葉は、単にコンテンツ描画時の一場面を切り取った言葉として利用されています。

(※)ここでは、CSR のみのサーバーサイドで事前レンダリングを全く行わないアプリケーションのこと

## SPA(Single-page application)

ここで SPA について触れておきたいと思います。私の観測する限り、SPA という用語の使われ方は大きく分けて二つに分かれています。

1. ソフトナビゲーションで画面回遊するアプリケーション全般(SSR/SSG と共存する概念)
2. CSR のみの事前レンダリングをしないアプリケーション(SSR/SSG と排他的関係の概念)

1 については、HTML ファイルがサイト訪問時の一回を除いては２度と送られてこないアプリケーションということになり、ページごとに HTML ファイルを都度配信する MPA(Multi-page application)との対比として捉えることができます。

2 については、事前レンダリングをしない、Node.js サーバーを持たない 完全にブラウザ上でのみ動作する CSR のみのアプリケーションということになります。

2 の用例は海外で特に見られる傾向にあり、例えば State of javaScript のアンケートでの SPA の定義や Remix の SPA モードの意味でも、 SSR/SSG と横並びになるような意味として定義されています。

> SPA: Apps that run entirely in the browser
> MPA: Apps that run entirely in the server, with minimal client-side dynamic behavior

https://survey.devographics.com/en-US/survey/state-of-js/2023/outline/9

## SSR(Server-Side Rendering)

**現在の主流の意味:Isomolphic なコードをサーバーサイドで実行して事前に HTML を生成すること、あるいはそのレンダリングパターン**

Isomolphic というのは、**同じコード**がサーバーサイドでもクライアントサイドでも実行されることです。現在の SSR という言葉は、Isomolphic であることが前提で語られることが多いです。(よって Hydration がないものは SSR ではないという認識のされかたも少なからずされています。)

### SSR という言葉の変遷

#### クラシック SSR

**意味. (CSR ではなく)サーバーサイドでコンテンツ(ページ)を生成すること**
もともと SSR という言葉は、SPA(Single-page application)が台頭してきたときに、CSR(Client-Side Rendering)という言葉のレトロニム(※)として生まれた言葉だとされています。
この頃の SSR が差しているモデルは、テンプレート言語(Java,PHP,Ruby on Rails 等)でサーバーサイドで HTML ファイルを生成するものを指しています。

当時は現在の Next.js や Remix のように、クラサバで同一の javaScript(or Node.js)コードを実行するようなアーキテクチャが主流ではありません。よって、現在のように Hydration が伴うような Isomolphic なアプリケーションのことを指しているわけではなく、純粋に HTML(あるいはページのコンテンツ)を CSR のようなブラウザ上ではなくサーバー上で生成するという意味合いでした。　　
また、後ほど出現する React フレームワーク（Next.js 等）などのサーバーサイドで HTML を事前レンダリングするようなレンダリングパターンは、このクラシックな SSR と差別化するため**_SSR with Hydration_**などと呼ばれたりしました。

(※)レトロニム:時代の変化により新しい事物が生まれたことから、既存の事物を新しいものと区別するため「後から」つくられた言葉

#### SSR with Hydration の出現

現在のような Isomolphic な SSR フレームワークの起源の一つであると考えられるものとして Airbnb 社の Backbone.js のフレームワーク、**Rendr** があります。
Backbone.js は最古の SPA フレームワークで、クライアントサイド MVC モデルです。

当時の CSR のアプリケーションは、**SEO 対策**のために、javaScript と別言語(Ruby など)で二重開発をして、初回アクセス時にはサーバーサイドで HTML ファイルを生成したり、ヘッドレスブラウザのようなものを挟み、そこで javaScript を実行して HTML を生成するなどの対応をしていたようです。

ダイナミックレンダリング
https://developers.google.com/search/docs/crawling-indexing/javascript/dynamic-rendering?hl=ja

js を実行して HTML を生成してくれるサービス
https://prerender.io/

そこで Rendr が取ったアプローチが、Node.js を採用し、Ruby との二重開発で共通していたロジックは Isomolphic なコードとすることでした。
なお、Rendr の作者の Rendr の公開スライドに、すでに hydrate という言葉が出ていることから、クライアントサイドで要素をインタラクティブにするフェーズを hydrate という文化はこの時にはすでに存在していたようです。

以下の Rendr の記事は React が公開される 2013 年 5 月よりも前のものなので、React の事前レンダリングが普及される遥か前にすでに現在のような SSR フレームワークが一度完成していたと言っても良いかもしれません。(結局この Rendr は React の流行の前にさほど流行ることがなく、Airbnb も React に移行することになったようです。)

https://medium.com/airbnb-engineering/our-first-node-js-app-backbone-on-the-client-and-server-c659abb0e2b4

https://www.slideshare.net/slideshow/introducing-rendr-run-your-backbonejs-apps-on-the-client-and-server/19106546

#### 現在の SSR(事前レンダリング)

**現在の主流の意味(再掲):Isomolphic なコードをサーバーサイドで実行して事前に HTML を生成すること、あるいはそのレンダリングパターン**
Next.js や Remix のような FW が流行り、Node.js でもブラウザでも React のコードが動作することが当たり前となった現在では事前レンダリングの用語として使われるようになり、初期に SSR with Hydration と呼ばれているものを単に SSR と呼ぶようになりました。
事前にレンダリングをする目的は、サイト訪問時に空の HTML ではなく、コンテンツが含まれた HTML を返すためです。

> (この時に返す HTML は静的なもので、ページをインタラクティブにするためには、結局ブラウザ上で Hydration(もう一度レンダリングして仮想 DOM を作り、各要素にイベントを付与すること)必要があります。)

CSR(SPA)台頭の時点では、敢えてクライアントでレンダリングする、という世界でしたが、逆に現在では、CSR を前提に**本来はクライアントで動くコード**をサイト訪問時だけ、**敢えてサーバーサイドで事前にレンダリングする**、という意味に変わってきました。

事前レンダリングのタイミングがビルド時の場合は SSG、サイトにリクエストする度に実行される場合は SSR という区分けがされています。海外を中心に、事前レンダリングなしのモデル(完全にクライアントで動作するアプリケーション)を SPA と呼ぶようになっており、これらの用語の差異を決定づけるものは、サイト訪問時の HTML の生成(レンダリング)の仕方になります。

| SPA                                           | SSR                                                                     | SSG                                                                 |
| --------------------------------------------- | ----------------------------------------------------------------------- | ------------------------------------------------------------------- |
| ブラウザ上でのみ動作する CSR アプリケーション | 毎アクセス時にサーバーサイドで事前レンダリングする CSR アプリケーション | ビルド時にサーバーサイドで事前レンダリングする CSR アプリケーション |

#### 未来の SSR の意味

RSC(React Serve Components)の世界では、従来はすべてのコンポーネントが原則クライアントサイドで、事前レンダリング時はサーバーサイドで実行されていたものが、完全にサーバーサイドでしか実行されない Server Components(SC)と従来通りの Client Components(CC)に別れました。

まず、**SC と SSR って一緒なのか？** という疑問に対しては、React 公式が回答しており、併用できる概念であると説明しています。
あくまで、SSR は HTML をサーバーサイドで事前に生成(レンダリング)する従来通りの用語であるため、SC や CC がサーバーサイドでレンダーされること自体を SSR としていません。

https://github.com/reactjs/rfcs/blob/main/text/0188-server-components.md#does-this-replace-ssr

> Does this replace SSR?
> No, they’re complementary. SSR is primarily a technique to quickly display a non-interactive version of client components. You still need to pay the cost of downloading, parsing, and executing those Client Components after the initial HTML is loaded.
> You can combine Server Components and SSR, where Server Components render first, with Client Components rendering into HTML for fast non-interactive display while they are hydrated. When combined in this way you still get fast startup, but you also dramatically reduce the amount of JS that needs to be downloaded on the client.

しかし、RSC という新たな概念により、従来にはなかったレンダリングのパターンが出現し、従来の定義と比べて新たな疑問を抱く人も現れています。
例えば SC は 非インタラクティブであり、Hydration が原理的に行われないため、SC のみで構成したページは SSR と言えるのでしょうか、画面回遊時に RSC 以前とは違いサーバーサイドで SC がレンダーされるようになったが、従来通り SSR/CSR などという言葉が適切なのでしょうか。
本記事では詳しく触れることができませんでしたが、実際に Next.js の v14 以降では PPR(Partial-Pre-Rendering)という新たなレンダリングモデルが登場しました。
https://zenn.dev/akfm/articles/Next.js-partial-pre-rendering

昨今までも SSR という言葉の意味が、用語が使われる文脈が変わり変化してきたように、これからも、今の意味から変わっていく可能性がありそうです。

## まとめ

フロントエンドにおいて画面に要素を表示することが一つの責務になっています。
レンダリングでは処理が実行されているわけですが、文脈によって、対象の範囲がコンポーネントであったり、HTML ファイル(ページ全体)であったり、画面に反映されなかったりすることがわかったと思います。
本記事がレンダリングという言葉を通してフロントエンドの理解の助けとなったなら幸いです。
