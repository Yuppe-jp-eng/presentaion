---
theme: seriph
background: 
class: text-center
highlighter: shiki
lineNumbers: false
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
drawings:
  persist: false
transition: slide-left
title: Welcome to Slidev
---

# 中村勉強会　<br>〜AWSインフラ編〜 <br> 「Codeサービス系 #1」

---
transition: fade-out
---

# この勉強会の意図

<br>
<h2>・施設予約システムのインフラ全般を担当</h2>
<br>
<h2>・特に悩んだのがコンテナサービスのCI/CDとインフラIaC化</h2>
<br>
<h2>・約半年やってきたことの整理と生まれた知見の共有</h2>
<h2>→インフラ構築・運用属人化を防ぐ</h2>
<br>
<h2>・今後の課題整理</h2>


<br>
<h1><strong>あとついでに目標の達成!!!</strong></h1>

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
transition: fade-out
---

# 本日のお題目

## 1. CI/CDの重要性
<br>

## 2. 実現手段と選定理由
<br>

## 3. CI/CDに関わるCode〇〇サービス
---
transition: fade-out
---

# 1.CI/CDの重要性

## CI/CDとは？

<br>

## ①CI(Continuous Integration)
→ コードの変更を<span style="color: red;">継続的に</span>統合する過程(ビルド、テスト)

```mermaid
graph TD
    A[Build]
    B[Bundle]
    C[Compile]
    D[Other Tasks]
    E(Merge multiple files into one)
    F(Convert source code to executable code)
    G(Optimization, Minification, Linting etc.)
    
    A --> B
    A --> C
    A --> D
    B --> E
    C --> F
    D --> G
```

<br>

## ②CD(Continuous Deployment/Delivery)
→ 統合されたコードを実環境に<span style="color: red;">継続的に</span>反映させる過程(デプロイ)

---
transition: fade-out
---

# 1.CI/CDの重要性

## 高速なフィードバックループの実現

<div grid="~ cols-2 gap-3" style="margin-bottom: 100px;">
<div>

<br>
```mermaid
graph LR
    A[開発] --> B[テスト]
    B --> C[デプロイ]
    C --> D[フィードバック]
    D --> A

```

</div>
<div>

## ①アプリケーションの品質安定

<br>

## ②手動運用時に起こりうるヒューマンエラーの削減


</div>
</div>

### だけではなく...


---
transition: fade-out
---

# 1.CI/CDの重要性

## もう一つの幸せスパイラルを生む

<br>

```mermaid
graph LR
    A[CI/CDの導入/進化] --> B[アプリの品質の安定]
    B --> C[心と時間の余裕]
    C --> D[開発、テスト、運用整理などに割ける時間の増加]
    D --> B

```
<div grid="~ cols-2 gap-3">

<img border="rounded" src="/ref/image/yopparai_beach.png" width="120" height="150">

<img border="rounded" src="/ref/image/yopparai_beach.png" width="120" height="150">

</div>

---
transition: fade-out
---

# 2.実現手段と選定理由

## 実はCI/CDツールはたくさんある
## →CircleCI,Jenkins,GitLab,GitHub Actions, Azure,GCP系サービス


## 結論:AWSのサービス群を選択

<br>

## 1.信頼と実績

### ・すでにプロトが用意してあって、最低限のCI構築はできていた
### ・CodeDeployだけ一応使ったことがあった
<br>

## 2.応用可能性/親和性

### ・その他インフラリソースとの親和性

### ・IaCサービス(AWS Cloud Development Kit)との親和性

## 3.学習コスト
### ・学習コストのことだけ考えれば、基本的にサードパーティ製のツールはない方がいい

---
transition: fade-out
---

# 3.CI/CDに関わるCode〇〇サービス
1. CodePipeline...パイプライン(流れ)を定義
2. CodeCommit/Artifact...GitHubのAWS版、ソースストレージ
3. CodeBuild...ビルドのための環境を素早く用意、ビルドプロセスの構築
4. CodeDeploy...デプロイ

____
よりマネージドなCI/CDサービス

5. CodeGuru...リッチでニッチな使い道(機械学習使ったコードレビュー)
6. CodeStar...CI/CDめっちゃマネージド(テンプレから選べる)
7. CodeCatalyst...メンバーオンボーディング、IDE連携、CodeStar+CDK構築まで一気通貫(マネージドの鬼)、コツを掴めば、爆速開発環境、インフラ、パイプライン構築可能かも

---
transition: fade-out
---

# 3.CI/CDに関わるCode〇〇サービス

改めて整理する(していただく)と

<img border="rounded" src="/ref/image/flow.png" >
引用元:https://pages.awscloud.com/rs/112-TZM-766/images/20210126_BlackBelt_CodeDeploy.pdf

---
transition: fade-out
---

# まとめ

## ・AWSのCodeなんちゃらサービスはたくさんある

<br>

## ・CI/CDというか、運用構築は早めに取り組んだ方がいい
<br>

## ・Codeなんちゃらは整理すれば意外と分かりやすい

<br>

## ・次回はCodePipeline、CodeBuildをやりたい