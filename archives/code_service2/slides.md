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

# 中村勉強会　<br>〜AWSインフラ編〜 <br> Codeサービス系 
# #2 組み立て工場見学!ざっくりとパイプラインを外観する

---
transition: fade-out
---

# この勉強会の目標

<br>
<h2>・CodePipelineがどのようにしてCI/CDフローを形成するかを理解する</h2>
<br>
<h2>・CodePipelineの主要概念を理解する</h2>
<br>
<h2>・マネコン/CDKでの実装方法を理解する</h2>

<style>
h1 {
  fontsize: 20px;
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

## 0. 軽く前回のおさらい
<br>

## 1. CodePipelineの役割
<br>

## 2. 工場見学①原料調達
<br>

## 3. 工場見学②加工場
<br>

## 4. CodePipelineの主要概念
---
transition: fade-out
layout: center
class: text-center
---

<h1>0.前回のおさらい</h1>


---
transition: fade-out
---

# CI/CDとは？

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
    G(Installation, Optimization, Minification, Linting etc.)
    
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

# 高速なフィードバックループの実現

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

# もう一つの幸せスパイラル

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

# 実はCI/CDツールはたくさんある
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

# AWSのCode系サービス
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
layout: center
class: text-center
---

<h1 style="fontsize: 50px;">1.CodePipelineの役割</h1> 

---
transition: fade-out
---
## ある機械製品が出来上がるまでの流れ

<br>
<p v-click >
```mermaid
flowchart LR
    A1[原料調達] --> A6[集積所]
    A6 --> A2[加工場]
    A2 --> A4[集積所]
    A4 --> A5[組み立て場]
    A5 --> A7[集積所]
    A7 --> A3[配送業者]

    subgraph 集積所エリア
        A6
        A4
        A7
    end

    style A1 fill:#FF5555,stroke:#FFF,stroke-width:2px,color:#FFF
    style A2 fill:#FFAA55,stroke:#FFF,stroke-width:2px,color:#FFF
    style A3 fill:#FFFF55,stroke:#000,stroke-width:2px,color:#000
    style A4 fill:#55AAFF,stroke:#FFF,stroke-width:2px,color:#FFF
    style A5 fill:#FFAA55,stroke:#FFF,stroke-width:2px,color:#FFF
    style A6 fill:#55AAFF,stroke:#FFF,stroke-width:2px,color:#FFF
    style A7 fill:#55AAFF,stroke:#FFF,stroke-width:2px,color:#FFF

```
</p>

<p v-click >

```mermaid
flowchart LR
    B0[GitHub] -. "CodeStar" .-> B6[Artifact:ソースコード]
    B1[CodeCommit] --> B6
    B6 --> B2[CodeBuild]
    B2 -- "設定ファイル①" --> B4[Artifact]
    B4 -- "設定ファイル①" --> B5[CodeBuild]
    B5 --> B7[Artifact]
    B7 --> B3[CodeDeploy]

    subgraph Artifactエリア
        B6
        B4
        B7
    end

    style B0 fill:#FF5555,stroke:#FFF,stroke-width:2px,color:#FFF
    style B1 fill:#FF5555,stroke:#FFF,stroke-width:2px,color:#FFF
    style B2 fill:#FFAA55,stroke:#FFF,stroke-width:2px,color:#FFF
    style B3 fill:#FFFF55,stroke:#000,stroke-width:2px,color:#000
    style B4 fill:#55AAFF,stroke:#FFF,stroke-width:2px,color:#FFF
    style B5 fill:#FFAA55,stroke:#FFF,stroke-width:2px,color:#FFF
    style B6 fill:#55AAFF,stroke:#FFF,stroke-width:2px,color:#FFF
    style B7 fill:#55AAFF,stroke:#FFF,stroke-width:2px,color:#FFF


```
</p>

---
transition: fade-out
---

# 原料調達

<div grid="~ cols-2 gap-3" style="margin-bottom: 100px;">
<div>

<br>
```mermaid
flowchart LR
    B0[GitHub] -. "CodeStar" .-> B2[CodeBuild]
    B1[CodeCommit] --> B2

    style B0 fill:#FF5555,stroke:#FFF,stroke-width:2px,color:#FFF
    style B1 fill:#FF5555,stroke:#FFF,stroke-width:2px,color:#FFF
    style B2 fill:#FFAA55,stroke:#FFF,stroke-width

```

<br>

## ①接続先の設定

<br>

## ②外部プロバイダーを挟む場合、一工夫必要(GitHubとの連携にはCodeStarが簡単)
</div>

   <div style="margin-bottom: 100px;">
   <img border="rounded" src="/ref/image/code_services2/source_codecommit.png" width="600">
   </div>
</div>

---
transition: fade-out
---

# CodeStar Connectionsを利用してGitHubと接続する

<div grid="~ cols-2 gap-3" style="margin-bottom: 100px;">
   <div style="margin-bottom: 100px;">
   <img border="rounded" src="/ref/image/code_services2/source_github.png" width="600">
   </div>
   <div>

   ## ①Sourceステージでトリガー先の設定をする

   <br>
   
   ## ②CodeStar Connectionsで取得した接続ARN(後述)を入力

   <br>

   ## ③トリガー先リポジトリ、ブランチを選択

   </div>
</div>

---
transition: fade-out
---

# CodeStar Connectionsの接続を作成する

<div grid="~ cols-2 gap-3" style="margin-bottom: 100px;">
   <div style="margin-bottom: 100px;">

   ### ①接続の作成

   <img border="rounded" src="/ref/image/code_services2/codestar1.png" width="600">
   </div>
   <div>

   ### ②GitHubを選択し、接続名を入力
   <img border="rounded" src="/ref/image/code_services2/codestar2.png" width="600">
   </div>
</div>

---
transition: fade-out
---

# 使用する認証アプリの設定

<div grid="~ cols-2 gap-3" style="margin-bottom: 100px;">
   <div style="margin-bottom: 100px;">

   ### ③どのアカウントにインストールされたアプリを使うか選択

   <img border="rounded" src="/ref/image/code_services2/codestar3.png" width="600">
   </div>
   <div>

   ### ④新しいアプリをインストールを選択すると設定にいける
   <img border="rounded" src="/ref/image/code_services2/codestar4.png" width="500" height="100">
   </div>

</div>

---
transition: fade-out
---

# 設定確認

<div grid="~ cols-2 gap-3" style="margin-bottom: 100px;">
   <div style="margin-bottom: 100px;">

   ### ⑤パスワード入力

   <img border="rounded" src="/ref/image/code_services2/codestar5.png" width="400">
   </div>
   <div>

   ### ⑥ インストールアプリの設定変更
   <img border="rounded" src="/ref/image/code_services2/codestar6.png" width="600" height="200">

   </div>

</div>

---
transition: fade-out
---

# 対象リポジトリの追加とARNの確認

<div grid="~ cols-2 gap-3" style="margin-bottom: 100px;">
   <div style="margin-bottom: 100px;">

   ### ⑤対象リポジトリの追加

   <img border="rounded" src="/ref/image/code_services2/codestar7.png" width="400">
   </div>
   <div>

   ### ⑥ ARN確認
   <img border="rounded" src="/ref/image/code_services2/codestar8.png" width="600" height="200">

   </div>

</div>

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

---
transition: fade-out
---

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
    G(Installation, Optimization, Minification, Linting etc.)
    
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

# 0.前回のおさらい

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

# 0.前回のおさらい

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

# 0.前回のおさらい

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

# 0.前回のおさらい
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
