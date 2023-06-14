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
June 24, 2023（v0.0.1）

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

<img src="https://pbs.twimg.com/profile_images/799890486773170176/KN4gKfS2_400x400.jpg" class="rounded-full w-40 mt-16 mr-12"/>

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
登壇者が実際にAPIシナリオテストを導入検討して導入して感じたことを推しポイントとして紹介したいと思います。  
-->

---

# 本セッションの狙い

- APIを実装する全てのエンジニアにAPIシナリオテストの便利さを知ってほしい  
- SPAなシステムにE2Eテスト導入しようと考えている方にまずはAPIシナリオテストの導入をオススメしたい

<!--
登壇者は[cypress](https://www.cypress.io/)(サイプレス)を導入しようとして断念した過去があり、APIシナリオテストを導入してみての気づきを紹介したいと思います
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
APIシナリオテストを実現するには幾つかツールが存在します。
-->

---
transition: fade
---

# APIシナリオを実現する方法

* 各言語のHTTPライブラリで実装  
なんならcurlでも。。
* APIのテストが可能なツールは幾つかあります
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

---
layout: fact
---

# 推しツール

<Tweet id="1561490586858770432" scale="0.7" />

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
特徴的な内容としてはBaaSでスキーマ駆動開発を行っている点です。  
プロジェクトの詳細についてはzenn.devに記事をアップしていますので、もし良かったら読んでみて頂きたいと思います。  
-->

---
transition: fade
---

# お断り

- OpenAPIでスキーマ定義されたAPIに対してrunnによるAPIシナリオテストを1年ぐらい書いてきた所感となります。    
- 使用ツールの具体的な使い方については個別に説明は行いません。  

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
次のスライドから推しポイントを説明していきます
-->

---

# <material-symbols-counter-1 />APIのスキーマの品質を向上させることができる

- APIのスキーマ（リクエストとレスポンス）に注目してテストできる  
ユースケースをカバーしてAPIのウォークスルーを行う
- インターフェースの欠陥を早く検知することができる
- 【runn】OpenAPIのSpec通りになっているか？シナリオテストを書くだけでチェックできる  
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
シナリオ内の総リクエスト数は1,183ぐらいで1Secで3つAPIを捌けているぐらい

<!--
単体テストに比べては遅いですが、インフラ層のモックなしコントローラーテストと比べたらそこまで極端に遅くはなってないのではないでしょうか？
-->

---

# <material-symbols-counter-4 />テストの安定度が高い

- テストの安定度が高い＝テストのメンテナンスコストが低くなる
  - アプリケーション改修う時のテストへの影響度は UI > APIスキーマ > データベース の順  
  - Flaky Testになりづらい
- シナリオテストではデータの独立性を担保しやすい  
各テストケースに異なる一意のデータを作成させて、テスト間の依存関係がなくテストの信頼性が高い  

<!--
ビジネスロジックに大きな変更がない限り、エンドポイントやリクエストパラメーターが変更される可能性が低いため、自動化しやすいです。  
runn ではシナリオのステップの再利用がしやすくデータの独立性を保ちやすいです。
-->

---

# <material-symbols-counter-5 />テストを効率的に書ける

- サーバーサイドの言語に依存せずにテストを書くことができる  
→　【runn】yamlでOpenAPIのSpecライクに記述できる
- テスト観点が明確で記述量が少ないテストでレビューもし易い  
コントローラーテストをモックなしでテストを書くのに比べても記述量が少なくて済む  
- テストデータの準備もシナリオで定義し、見通しの良いテストとなる  

<!--
runn自体がシナリオテストをUnitテスト並みに簡単にするツールとして [開発されました])(https://speakerdeck.com/k1low/stac2022?slide=28)。  
runnではシナリオを共通化でき、データ駆動テストも可能になっています。
-->

---

# <material-symbols-counter-6 />シナリオがドキュメント代わりになる

- 一連のユースケースがAPIチェーンで表現されるのでフローを理解し易い  
→　【runn】テストの記述量が少なくて読みやすい
- テストの安定度が高く、信頼度が高く読みやすいのでドキュメントとして相性が良い（ストックしやすい）  
API利用者からのAPIの挙動の問い合わせを受ける度にシナリオを作成していくと良い感じでストックされていく 

<!--
runnではシナリオの各stepにdescriptionを記述できます
-->

---

# <material-symbols-counter-7 />自動テストへの組み込みが容易
CIに載せやすい

- SPAでフロントまで含めてE2Eテストは大変だが、APIのみだったら難易度はそこまで大変ではない  
→　【runn】1バイナリなのでCIに組み込むのも楽

Github Actionも用意しています  
https://github.com/marketplace/actions/run-runn

- ここでもテストの安定度の高さが効いてくる

<!-- <BlogCard url="https://github.com/marketplace/actions/run-runn" /> -->

<!--
ユニットテストの自動化ができていれば、ワークフローを分けてenvを調整してサーバーだけ起動できればいけます。  
外部通信（S3, API）する場合はモックにした方が良いと思います。
-->

---

# <material-symbols-counter-8 />リグレッションテストにも使える

- CIで自動テストしているシナリオを微調整してリグレッションテストとして活用させる
→　【runn】環境変数でHost変更や、テスト用のステップを除外できるように条件分岐できる
- ゴールデンテストとの相性が良い  
→　リクエストとレスポンスの組合せが明確になっている  

<!--
エンドポイントも環境変数で切替可能にしました。  
ゴールデンテストというのは過去に実行したテストの結果をファイルに保存して再度実行するときはそのファイルと同じ結果が返ってきているかどうかをチェックするテスト手法のことです。  
runnではレスポンス比較をjsonファイルで行うことができます
-->

---

# <material-symbols-counter-9 />ネガティブテストをしやすい

- リクエストの異常パラメータ（脆弱性を突くような）のパターンのテストを行いやすい  
→　本来はAPIのクライアント側でValidationされているが、APIテストでは制約はなし
- 冪等性確認する為に連続的にリクエストを行うテストもやりやすい  
→　【runn】loopを使って繰り返し実行させることが出来る

<!--
探索テストにリクエストパラメータを調整して実行しても再現性あるテストにすることができる
-->

---

# <fxemoji-ten />負荷テストにも活用できる

- APIシナリオテストベースで繰り返し実行させれば負荷テストにもなる  
→　【runn】ロードテストとして実行するモードが用意されています
- デッドロックの検証も行えます  
→　シナリオなので実際のアクセスに近い形で検証ができる


<!--
runnの負荷テストはrunコマンドをloadtコマンドに変えるだけ
-->

---
layout: image-right
image: https://source.unsplash.com/collection/94734566/960x1080
---

# まとめ

<!--
次のスライドで振返りしたいと思います。
-->

---

# 時間を節約できる

* 結合テスト以降のサイクルを早めることができる  
システム・コンポーネント（フロントエンド含む）の結合前に最低限のテストが終わっている
* APIの仕様について早期フィードバックを得られやすい  
テストを書くことでセルフレビュー＆ドキュメント化によるフィードバック
* 自動テストでユースケース〜シナリオテストをカバーできる  
curlのコマンドを組み立てるよりも早く記述でき繰り返し使え、手動テストから卒業できる。

<!--
-->

---

# 効率が良いお得なテスト  

* E2Eテストで効率よくカバレッジを上げることができる
* 実行時間・メンテナンスコスト・テストの信頼指標のバランスが良い　　
<img src="https://res.cloudinary.com/kentcdodds-com/image/upload/f_auto,q_auto,dpr_2.0,w_1600/v1625033547/kentcdodds.com/content/blog/write-tests/2.png" class="h-80 rounded shadow" alt="testing pyramid" />  [Write tests. Not too many. Mostly integration.](https://kentcdodds.com/blog/write-tests)
* リグレッションテスト・負荷テストもカバーできる


<arrow x1="550" y1="400" x2="350" y2="335" color="red" width="3" arrowSize="2" />

<!--
テストのカバー範囲として赤矢印のIntegration寄りのE2Eという位置づけという認識です。　　
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
E2Eテスト <ic-baseline-trending-down />

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
runnの開発者の [k1low](https://twitter.com/k1LoW) さん著作なrunnの使い方を紹介している本です。
-->

---
layout: end
---

ご清聴ありがとうございます