---
# try also 'default' to start simple
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://source.unsplash.com/collection/94734566/1920x1080
# apply any windi css classes to the current slide
class: 'text-center'
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# show line numbers in code blocks
lineNumbers: false
# some information about the slides, markdown enabled
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# persist drawings in exports and build
drawings:
  persist: false
# page transition
transition: slide-left
# use UnoCSS
css: unocss
fonts:
  # basically the text
  sans: 'Noto Sans JP'
  # use with `font-serif` css class from windicss
  serif: 'Noto Serif JP'
  # for code blocks, inline code, etc.
  mono: 'Noto Sans Mono'
---

# APIシナリオテストを書くべき10の理由

PHPカンファレンス福岡2023  
June 24, 2023（v0.0.5）

<div class="pt-12">
  <span @click="$slidev.nav.next" class="px-2 py-1 rounded cursor-pointer" hover="bg-white bg-opacity-10">
    Press Space for next page <carbon:arrow-right class="inline"/>
  </span>
</div>

<div class="abs-br m-6 flex gap-2">
  <button @click="$slidev.nav.openInEditor()" title="Open in Editor" class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
    <carbon:edit />
  </button>
  <a href="https://github.com/k2tzumi/10-reasons-to-write-api-scenario-tests/blob/main/slides.md" target="_blank" alt="GitHub"
    class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
    <carbon-logo-github />
  </a>
</div>

---
transition: fade-out
layout: two-cols-header
---

# 自己紹介

katzumiと申します  

「障害のない社会をつくる」をビジョンに掲げている「りたりこ」という会社に所属しています
<a href="https://litalico.co.jp/">
<img src="https://litalico.co.jp/ogp.png" class="w-40" />
</a>

以下のアカウントで活動しています

::left::

<img src="https://pbs.twimg.com/profile_images/799890486773170176/KN4gKfS2_400x400.jpg" class="rounded-full w-40 mt-16 mr-12　float-left"/>  
<img src="/twitter_qr.png" class="mt-16 ml-8 w-25"/>

<logos-twitter /> [katzchum](https://twitter.com/katzchum)

::right::

<img src="https://avatars.githubusercontent.com/u/1182787?v=4" class="rounded-full w-40 mt-16 mr-12"/>

<logos-github-octocat /> [k2tzumi](https://github.com/k2tzumi)  
<simple-icons-zenn /> [katzumi](https://zenn.dev/katzumi)  



<style>
h1 {
  background-color: #2B90B6;
  background-image: linear-gradient(45deg, #4EC5D4 10%, #146b8c 20%);
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}
</style>


---
transition: slide-up
layout: default
---

# 今日話す内容

- 本セッションの狙い
- APIシナリオテストとは？
- APIシナリオテストを導入したプロジェクト説明
- APIシナリオテストを書くべき理由
- まとめ


<!--
登壇者が実際にAPIシナリオテストを導入して感じたことを推しポイントとして紹介したいと思います。  
-->

---

# 本セッションの狙い

- APIを実装する全てのエンジニアにAPIシナリオテストの便利さを知ってほしい  
- SPAなシステムにE2Eテスト導入しようと考えている方にまずはAPIシナリオテストの導入をオススメしたい

<!--
登壇者は[cypress](https://www.cypress.io/)(サイプレス)を導入しようとして断念した過去があり、その経験も踏まえてAPIシナリオテストのメリットについて紹介したいと思います
-->

---
layout: two-cols-header
transition: slide-up
---

# APIシナリオテストとは？

複数のAPIを連鎖的に呼び出し（Chaining Requests）て実行するテストのことです。  
例えばユーザーの新規登録後に更新させるケースでレスポンス（ユーザーID）を次のAPIのパラメータとして引き継ぎながらAPIを呼び出すテスト。  
<template v-slot:left>

1. ユーザー新規登録（Create User） 
  ```console
  curl -X 'POST' \
    'https://example.com/api/v3/user' \
    -H 'accept: application/json' \
    -H 'Content-Type: application/json' \
    -d '{
    "username": "newUser",
    "firstName": "John",
    "lastName": "James",
    "email": "john@example.com",
    "password": "miserarenaiyo",
    "phone": "12345"
  }'
  ```
</template>
<template v-slot:right>

2. ユーザー更新（Update User）  
  ```console {all|2}
  curl -X 'PUT' \
    'https://example.com/api/v3/user/{userId}' \
    -H 'accept: */*' \
    -H 'Content-Type: application/json' \
    -d '{
    "username": "updatedUser",
    "firstName": "David",
    "lastName": "Smith",
    "email": "john@example.com",
    "password": "miserarenaiyo",
    "phone": "12345"
  }'
  ```
</template>


<!--
例としてcurlでAPIへリクエストしていますが、普段皆さんはどの様にAPIを検証していますでしょうか？  
APIシナリオテストに対応したツールは幾つか存在します。
-->

---
transition: fade
---

# APIシナリオテストをクライアントに依存させずに実行する方法

* 各言語のHTTPライブラリで実装  
なんならcurlでも。。
* APIテストにフォーカスしたツールは幾つかあります
  * <logos-postman /> [Postman](https://www.postman.com/) ([Newman](https://github.com/postmanlabs/newman))  
  * <devicon-karatelabs-wordmark /> [Karate](https://github.com/karatelabs/karate)
  * [Scenarigo](https://github.com/zoncoen/scenarigo)  

<div v-click>

One more...

</div>

<style>
.slidev-vclick-target {
  transition: opacity 200ms ease;
  text-align: center;
  font-size: 80pt;
}
</style>

<!-- 幾つかあるツールを調査・検討していく中で推しツールに出会うことが出来ました -->

---
layout: fact
---

# 推しツール

<Tweet id="1561490586858770432" scale="0.7" />

<!--
ランエヌ（`runn`）というGolangで実装された素晴らしいツールでした  
-->

---
transition: slide-up
---

# APIシナリオテストを導入したプロジェクト説明

- BaaSでREST APIのみ提供
- パラメータが多くテストケース数も多い
- クリーンアーキテクチャで実装
	- 単体テストはレイヤー毎にモックを使ってコントローラーテストまで実装
- OpenAPIでスキーマ駆動開発 [^1]
- APIはLaravel10で実装している
- QA組織がある
- プロジェクトの途中から [runn](https://github.com/k1LoW/runn) を段階的に導入

<br />
<br />
<br />
<br />

[^1]: [実装と乖離させないスキーマ駆動開発フロー / OpenAPI Laravel編](https://zenn.dev/katzumi/articles/schema-driven-development-flow)

<!--
本日は時間が限られており、runnを導入したプロジェクトについて細かい説明は割愛させて頂きます。  
特徴的な内容としてはBaaSでスキーマ駆動開発を行っている点です。  
詳細の内容についてはzenn.devに記事をアップしていますので、もし良かったら読んでみて頂きたいと思います。  
-->

---
transition: fade
---

# お断り

- OpenAPIでスキーマ定義されたAPIに対してrunnによるAPIシナリオテストを1年ぐらい書いてきた所感となります。    
runn固有の機能や特性についての記述は <RunnSvgIcon/>  マークを付けています
- 使用ツールの具体的な使い方について説明は行いません。  

<!--
APIシナリオテストの導入効果は導入するツールやプロジェクトの特性によって変わる可能性があります。  
runnの詳細については最後に参考URLを記載しています。
-->

---
layout: image-left
image: https://source.unsplash.com/collection/94734566/960x1080
---

# APIシナリオテストを書くべき10の理由

<!--
ここからが本題となります。  
次のスライドから推しポイントを説明していきます
-->

---

# <material-symbols-counter-1 />APIのスキーマの品質を向上させることができる

- APIのスキーマ（リクエストとレスポンス）に注目してテストできる  
ユースケースをカバーしてAPIのウォークスルーを行う
- インターフェースの欠陥を早く検知することができる
- <RunnSvgIcon/> OpenAPIのSpec通りになっているか？シナリオテストを書くだけでチェックできる  
<v-clicks depth="2">
```yml {all|5}
desc: Test using HTTP
runners:
  req:
    endpoint: ${TEST_HTTP_END_POINT:-https:example.com}
    openapi3: ../openapi3.yml
steps:
  getusers:
    req:
      /users:
        get:
          body: null
```
  <div v-after>
    OpenAPIのSpecを指定しておくだけ
  </div>
</v-clicks>

<!--
-->

---

# <material-symbols-counter-2 />テストを迅速に開始できる

- 開発サイクルの非常に早い段階で結合テストを始めることができる
- APIの呼び出し元がまだなくてもテストを行うことができる  
→　スキーマ駆動開発とすごく相性が良い

<!--
APIのI/F定義が済んでフロントエンドの実装ができてなくてもテスト可能になります。  
リクエスト受付とルーティングが正しいか？の検証からテストを書き始められて、あとからレスポンスの検証を追加するなど段階的にテストを拡張することができます。 
-->

---

# <material-symbols-counter-3 />UIテストと比べて実行スピードが早い

- APIテストではレンダリング・入力待ちが不要でテスト実行時間が短くて済む  
[UI Automation Vs API Automation - Quality Spectrum](http://quality-spectrum.com/ui-automation-vs-api-automation/) より  
  > To have a one to one comparison, we automated a test in our API suite and UI suite. The execution takes roughly around 7 minutes on the UI level, compared to just 12 seconds on the API level (that’s 35 times faster)
  
  実行時間で35倍ぐらい差があるケースも

導入しているプロジェクトで266scenariosで5min41Sぐらい  
シナリオ内の総リクエスト数は1,183ぐらいで1Secで3つAPIを捌けるぐらい

<!--
単体テストに比べては遅いですが、インフラ層のモックなしコントローラーテストと比べたらそこまで極端に遅くはなってないのではないでしょうか？
-->

---

# <material-symbols-counter-4 />テストの安定度が高い

- テストの安定度が高い＝テストのメンテナンスコストが低くなる
  - システム改修時におけるテストの影響度は、UIが最も高く、次にAPIスキーマ、最後にデータベースとなります  
  - Flaky Testになりづらい
- シナリオテストではデータの独立性を担保しやすい  
各テストケースに異なる一意のデータを作成させて、テスト間の依存関係がなくテストの信頼性が高い  

<!--
ビジネスロジックに大きな変更がない限り、エンドポイントやリクエストパラメーターが変更される可能性が低いため、自動化しやすいです。  
runn ではシナリオのステップの再利用がしやすくデータの独立性を保ちやすいです。
-->

---
layout: two-cols-header
---

# <material-symbols-counter-5 />テストを効率的に書ける

::left::

- サーバーサイドの言語に依存せずにテストを書くことができる  
<RunnSvgIcon/> yamlでOpenAPIのSpecライクに記述できる
- テスト観点が明確で記述量が少ないテストでレビューもし易い  
コントローラーテストをモックなしでテストを書くのに比べても記述量が少なくて済む  
- テストデータの準備もシナリオで定義し、見通しの良いテストとなる  
<RunnSvgIcon/> シナリオを共通化でき、データ駆動テストも可  
更にシナリオ上で直接DBアクセス・操作も可能

::right::

```yml
steps:
  createUser:
    desc: Create User APIのテスト（IDが発行される）
    req:
      /user:
        post:
          body:
            application/json:
              username: "alice"
              email: "alice@example.com"
    test: steps.createUser.res.status == 200
  updateUser:
    desc: Updated User APIのテスト（aliceをbobに変更する）
    req:
      /user/{{ steps.createUser.res.body.id }}:
        put:
          body:
            application/json:
              username: "bob"
    test: steps.updateUser.res.status == 200
```

<!--
runn自体がシナリオテストをUnitテスト並みに簡単にするツールとして [開発されましました](https://speakerdeck.com/k1low/stac2022?slide=28)  
冒頭でcurlでの例をシナリオ化して見ましたが、初見でもなんとなくやろうとしていることがわかるのではないでしょうか？
-->

---

# <material-symbols-counter-6 />シナリオがドキュメント代わりになる

- 一連のユースケースがAPIチェーンで表現されるのでフローを理解し易い  
<RunnSvgIcon/> テストの記述量が少なくて読みやすい
- テストの安定度が高く、信頼度が高く読みやすいのでドキュメントとして相性が良い（ストックしやすい）  
API利用者からのAPIの挙動の問い合わせを受ける度にシナリオを作成していくと良い感じでストックされていく 

<!--
先程の例でもありますが、大変読みやすく学習コストも低いのですぐに使い始めれると思います。
-->

---

# <material-symbols-counter-7 />自動テストへの組み込みが容易

CIに載せやすい

- SPAでフロントまで含めてE2E（ブラウザ）テストは大変だが、APIのみだったら難易度はそこまで高くない  
<RunnSvgIcon/> 1バイナリなのでCIに組み込むのも楽。

Github Actionsも用意しています  
https://github.com/marketplace/actions/run-runn

- ここでもテストの安定度の高さが効いてくる

<!-- <BlogCard url="https://github.com/marketplace/actions/run-runn" /> -->

<!--
ユニットテストの自動化ができていれば、ワークフローを分けてアプリケーションサーバーのみ起動できればOKです。  
-->

---

# <material-symbols-counter-8 />リグレッションテストにも使える

- CIで自動テストしているシナリオを微調整してリグレッションテストとして活用させる  
<RunnSvgIcon/> 環境変数で環境依存部分（エンドポイントやDB接続情報）の切替可、テスト用のステップをskipさせる等が可能。  
- ゴールデンテストとの相性が良い  
リクエストとレスポンスの組合せが明確  
<RunnSvgIcon/> Jsonファイルの読み込みをサポート（テンプレート機能あり）

<!--
ゴールデンテストというのは過去に実行したテストの結果をファイルに保存して再度実行するときはそのファイルと同じ結果が返ってきているかどうかをチェックするテスト手法のことです。  
-->

---

# <material-symbols-counter-9 />ネガティブテストをしやすい

- リクエストの異常パラメータ（脆弱性を突くような）のパターンのテストを行いやすい  
→　APIのクライアント側のValidationによって検証範囲が制限されることはない
- 冪等性確認する為に連続的にリクエストを行うテストもやりやすい  
<RunnSvgIcon/> loopを使って繰り返し実行させることが出来る

<!--
テキストベースで編集が楽なので探索テストもやりやすく、再現性あるテストにすることができる
-->

---

# <fxemoji-ten />負荷テストにも活用できる

- シナリオを繰り返し実行すると負荷テストにもなる  
<RunnSvgIcon/> ロードテスト用の実行モードがあります
- デッドロックの検証も行えます  
シナリオなので実際のアクセスに近い形で検証ができる


<!--
負荷テストはrunnの起動オプションを変更するだけでOKです
-->

---
layout: image-right
image: https://source.unsplash.com/collection/94734566/960x1080
---

# まとめ

<!--
次のスライドで振返りしていきます
-->

---

# APIシナリオテストは時間を節約できる

* 結合テスト以降のサイクルを早めることができる  
システム・コンポーネント（フロントエンド含む）の結合前に最低限のテストが終わっている
* APIの仕様について早期フィードバックを得られやすい  
テストを書くことでセルフレビュー＆ドキュメント化によるフィードバック
* 自動テストでユースケース〜シナリオテストをカバーできる  
手動テストから解放され、複数の環境・ユースケースで安定性の高いテストが繰り返し可能となる。

<!--
リグレッションテスト・負荷テストもカバーできる
-->

---

# APIシナリオテストはバランスが良くコスパが高い

* E2Eテストで効率よくカバレッジを上げることができる
* 実行時間・メンテナンスコスト・テストの信頼指標のバランスが良い　　
<img src="https://res.cloudinary.com/kentcdodds-com/image/upload/f_auto,q_auto,dpr_2.0,w_1600/v1625033547/kentcdodds.com/content/blog/write-tests/2.png" class="h-80 rounded shadow" alt="testing pyramid" />  [Write tests. Not too many. Mostly integration.](https://kentcdodds.com/blog/write-tests)
* テキストベースで書きやすくシナリオを量産しやすい


<arrow x1="550" y1="400" x2="350" y2="335" color="red" width="3" arrowSize="2" />

<!--
テストのカバー範囲として赤矢印のIntegration寄りのE2Eという位置づけという認識です。  
最上位はブラウザテストになりますが、そこまでカバーするのは大変です。  
runnの恩恵を受けている面が強いですが、学習コストが少なく導入も楽なので初期コストが少なく投資収益率 `ROI(Return on Investment)` が高いのもありがたいです。  
コントラクトテストやセキュリティテストの領域もある意味カバーしている  
-->

---
layout: two-cols-header
---

# テスト構成の比率を見直すと良さそう
テスト戦略を見直してみましょう  

::left::

テスティングトロフィー（The Testing Trophy）という考え方

<Tweet id="960723172591992832" scale="0.4" />

::right::

【テスト構成の見直し案】
* レイヤードアーキテクチャのレイヤー毎のテストを補完させるのに良い  
ユニットテスト <ic-baseline-trending-down />
* SPAのフロントエンドのAPI依存の部分のテストを排除させる  
ブラウザテスト <ic-baseline-trending-down />

<!--
-->

---

# 参考URL

* runn  
https://github.com/k1LoW/runn  
* APIシナリオテストの新ツールrunn  
https://zenn.dev/katzumi/articles/api-scenario-testing-with-runn
* runn クックブック  
https://zenn.dev/k1low/books/runn-cookbook
* runnによるAPIのシナリオテストの導入と自動化 / stac2022  
https://speakerdeck.com/k1low/stac2022
* Win Testing Trophy Easily / テスティングトロフィーを獲得する / PHPerKaigi 2023  
https://speakerdeck.com/k1low/phperkaigi-2023

<!--
runnの開発者の [k1low](https://twitter.com/k1LoW) さん著書となるrunnの使い方を紹介している本です。
-->

---
layout: end
---

ご清聴ありがとうございます