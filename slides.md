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
# #2 組み立て工場見学!ざっくりとパイプライン、ビルド、デプロイの中を覗く

---
transition: fade-out
---

# この勉強会の目標

<br>
<h2>・CodePipeline,Build,Deploy,Artifactがどのような役割を担うかを理解する</h2>
<br>
<h2>・CodeBuildとDeployに関して、それぞれに必要な設定ファイルの種類と中身をざっくり理解する</h2>
<br>
<h2>・マネコン/CDKでの実装方法をふわっと理解する</h2>

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

## 0. 軽く前回のおさらい
<br>

## 1. CodePipeline
<br>

## 2. CodeBuild/CodeArtifact
<br>

## 3. CodeDeploy
---
transition: fade-out
---

# 0.前回のおさらい

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

# 1.CodePipeline/CodeCommit

## ある機械製品が出来上がるまでの流れ

<br>


<p></p>
```mermaid
flowchart LR
    A1[原材料調達] --> A2[加工場]
    A2 --> A4[集積所]
    A4 --> A5[組み立て場]
    A5 --> A3[配送]

    style A1 fill:#FF5555,stroke:#FFF,stroke-width:2px,color:#FFF
    style A2 fill:#FFAA55,stroke:#FFF,stroke-width:2px,color:#FFF
    style A3 fill:#FFFF55,stroke:#000,stroke-width:2px,color:#000
    style A4 fill:#55AAFF,stroke:#FFF,stroke-width:2px,color:#FFF
    style A5 fill:#FFAA55,stroke:#FFF,stroke-width:2px,color:#FFF

```

<br>


<h2 v-click><span style="color:red">CodePipeline</span>はこの一連のプロセス設計を担う</h2>

<p v-click>
```mermaid
flowchart LR
    B1[CodeCommit] --> B2[CodeBuild]
    B2 --> B4[CodeArtifact and ECR]
    B4 --> B3[CodeDeploy]

    style B1 fill:#FF5555,stroke:#FFF,stroke-width:2px,color:#FFF
    style B2 fill:#FFAA55,stroke:#FFF,stroke-width:2px,color:#FFF
    style B3 fill:#FFFF55,stroke:#000,stroke-width:2px,color:#000
    style B4 fill:#55AAFF,stroke:#FFF,stroke-width:2px,color:#FFF

```
</p>

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
