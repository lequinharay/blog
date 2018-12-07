+++
date = "2016-06-07T18:00:00+09:00"
draft = true
title = "Laravel勉強会@CreatorsMatch"

+++

# Laravel勉強会@CreatorsMatch

* CREATORS MATCH LTD
* OMNIS
* Openlogi

## CreatorsMatch - 清家良太さん ASP事業部CTO

### サービス紹介 - AdFlow

* 広告クリエイティブの進行管理ができる
    * クリエイティブ制作
    * 効果測定

### サービス紹介 - AdTest

* 広告出稿前にパネラーに対してテストできる

### システム構成 - AdFlow

    ユーザー ------ Elastic Load Balancing ------ EC2 ------MySQL5.6
                    |
                    --- S3（画像など）※Lambdaは重かった……


### Laravel採用の経緯

* 2016年2月末に切り替わった
* 負の遺産 in PurePHPからの脱却
    * docなし
    * testなし

### FormRequestの活用

* データのアクセス制御が複雑
    * 所属企業、組織、アカウント属性、データの属性……関係が多岐にわたる<br>
    →Controllerに書くと大変なことに
* FormRequestとは
    * コントローラーとリクエストのバリデーション(と認証)を完全に切り離せる
    * コントローラーに処理をDIできる
    * authorizeの前にvalidateしたい
        * authorize()でもリクエストパラメータのチェックをしたい！
            * FormRequestの親のApp\Requestのvalidate()をoverrideして対処した

### 質疑応答

* rules
    * 力技で頑張る。foreachとかで回しまくる
* ビジネスロジックの切り出しはしてない
    * モデルに書く
    * コントローラーのテストはしないと決めている
* 支払い関連
    * 決済処理以外はプロジェクト内にある

## OMNIS（SUSTINA） - 遠藤和弥さん

### サービス概観

* Laravel運用実績なし
* アパレル・アクセサリー・バッグの定額レンタル
    * 配送料・クリーニング料など無料！
* 2013年11月~

### これまでの開発体制

* CodeIgniterによるインド式（オフショア）開発
    * マジでヤバイ
* ハッカソンと称して改善を行うなど
* 遠藤さんのjoinをキッカケに逐次改善を行ってきたが辛くなってきた→Laravel

### 質疑応答

* ECSは？
    * Dockerイメージ突っ込んで動いてる
    * オートスケールは別途実装が必要
    * 無停止デプロイできる？
        * 別インスタンスを立てて、動き終わったら切り替える感じ
    * ES2との比較はまだちゃんと出来てない
* リプレイス
    * サービス側は7,8割は完了
* ハッカソン
    * 一晩かけてカレーを作って食べる会にする。
* メール配信
    * 自前だと送られないとか遅延とかおきちゃう@CreatorsMatch
    * EC2だと配信上限があるし、インフラ担当からはAmazonSES必須と言われた

## Openlogi - 五十嵐さん

### Laravel

* バランスの良い思想（DDD）
* Railsは枠組み（CoC, DRY）
* レールの有無
* Modelディレクトリがない
* Service~~~

### レイヤー化アーキテクチャ（Layered Architecture）

* ユーザーインターフェース層（View / Console）
* アプリケーション層（Controller / Command）
* ドメイン層（Model / Service）
* インフラストラクチャ層
* ※上層への依存はNG

### イベント駆動型プログラミング

* PHPは手続き、同期的になりがち
* 処理をJobとして記述→非同期化しやすい
* 処理の関連性はイベントドリブンで記述（Event / Listener）
* フロントエンドと同じアーキテクチャ

### Flux / CQRS

* Action→Dispatcher→Store→View

## データ更新にJobを使う / Openlogi - 丸井さん

### Jobの良さ

* 自然と疎結合になる
* 重い処理はShouldQueueでいつでも非同期化可能
* 処理の再利用性が高まる
* 更新処理を行うクラスがApp\Jobs以下にまとまって把握しやすい
* 全てのJboの基底クラスを敬称&呼び出し方も統一されることで、共通的な処理を挟み込みやすくなる
    * DBトランザクション管理、ロギング……

* DBトランザクション
    * BusServiceProvider

### 注意点

* プロパティデータの欠損
    大量のデータをプロパティにもたせてenqueueすると、queueが保存されるjobsテーブルの文字数制限に引っかかってjobが壊れる
* dispatchする側のトランザクション管理
    * 新規作成したEloquentを渡して非同期Jobをdispatchするときに、更新処理が終わってなかったりすると……
* 実行結果が標準では残らない
    jobs、failed_jobsのみ管理され、正常処理した履歴は残らない
