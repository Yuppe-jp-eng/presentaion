# 中村勉強会　<br>〜AWS Codeサービス系 #1 〜

## モチベーション
- 施設予約システムのインフラ全般を担当
- 特に悩んだのがコンテナサービスのCI/CDとIaC化
- かれこれプロトを渡されてから半年間、あーでもないこーでもないとデモ環境を運用しながら構成変更していった。最近ようやく運用と構成が固まってきたので、少しずつ得られた知見を共有していきたい

## アジェンダ
1. CI/CDの重要性
2. AWSサービスで絞った理由
3. CI/CDに関わるCode〇〇サービス


## CI/CDの重要性
1. CI/CDとは
   1. CI(Continuous Integration)-継続的インテグレーション
      1. コードの変更を**継続的に**統合する過程(ビルド、テスト)
   2. CD(Continuous Development or Delivery)-継続的デプロイメント、デリバリー
      1. 統合されたコードを実環境に**継続的に**反映させる過程(デプロイ)
2. 高速なフィードバックループの実現
   1. 手動作業の削減
   2. 開発スピードの向上

## AWSで絞った理由
1. ツール選定
   1. 実績
   2. 他サービス(インフラベンダー,IaCツール)との兼ね合い/相性

## AWSでの選択肢
1. 全体像
2. それぞれ一言で
   1. CodePipeline
   2. CodeCommit/Artifact
   3. CodeBuild
   4. CodeDeploy
   5. CodeGuru
   6. CodeStar
   7. CodeCatalyst

## 次回 CodePipeline〜CodeBuild