1. 前回のおさらい
    1. パイプラインの図で今日の範囲の確認
2. Code Pipeline
    1. ある製品の生産・加工・配送ライン
        1. 原材料→CodeCommit(GitHub)
        2. 加工場→CodeBuild
        3. 加工したものの集積所→Code Artifact
        4. 配送業者→Code Deploy
3. CodeBuild
    1. 加工場
        1. どういった設備(ランタイム)や資材を利用するか
        2. どういった加工プロセスを踏むか
    2. 設定ファイル(buildspec.yml)
    3. CDKでどう書くか
4. CodeDeploy
    1. 配送業者
        1. どこに配達するのか
        2. 何個配達するのか
    2. 設定ファイル(appspec.yml)
    3. CDKでどう書くか