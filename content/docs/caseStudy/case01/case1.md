---
title: "事例紹介１"
weight: 1
bookToc: true
---

# 事例紹介１

## 規模

12名

＜内訳＞  
顧客：7名  
当社：5名  

## 契約形態

準委任

## 顧客文化・風土

アジャイル開発に関する理解度は高いです。
必要な環境やツールの準備であったり、開発の進め方も柔軟です。
アジャイル開発を採用・継続しているプロジェクトも多数存在しています。

## 体制

![体制図](../team.PNG)

## ロケーション

オフサイトがメインです。Zoomを使用しています。
たまにオンサイトで集まって催し物を行ったり、オンラインの交流会を行ったりしています。

💡オフサイトでコミュニケーションを取りやすくするポイント💡

* 業務時間は全員Zoomに入りっぱなし
* ペアや一緒に作業する人と同じブレイクアウトルームにいるようにする
* Slack等のチャットツールを併用する
* 可能な限り顔を出す

## 手法

スクラム × ウォーターフォールのハイブリッド開発をしています。

| 開発手法           | 担当領域       |
| ------------------ | -------------- |
| スクラム           | フロントエンド |
| ウォーターフォール | バックエンド   |

スクラム側のスプリントを終えた後、バックエンド側と合流してテストを行い、
サービスイン日になったらリリースします。（スプリント毎のリリースはしていません。）

## 大規模対応フレームワーク

採用していません。

## スケジュール（イテレート／スプリント）

1sprint：2週間としています。
合計12sprintで開発を行いました。

## スケジュール（sprint以外の要素）

### 要件整理

* 要件の優先順位付け（1か月）
* PBIと受け入れ条件を作成（1か月）

### ST-UAT

* バックエンドとつなげて行うシステムテストや受け入れテスト（6か月）

## 適用プラクティス

| #   | カテゴリ                        | プラクティス                                           | 説明                                                                                                                                                               | 採用有無 | 導入ツール、サービス | 補足、特記事項                                                                                                            |
| --- | ------------------------------- | ------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------- | -------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| 1   | プロセス                        | リリース計画ミーティング                               | プロダクトリリースのためのリリース計画ミーティング                                                                                                                 | 〇       | Miro                 |                                                                                                                           |
| 2   |                                 | イテレーション／スプリントプランニング                 | イテレーション（スプリント）ごとのリリース計画やアクティビティなどを計画するミーティング                                                                           | 〇       | Miro                 | sprint初日に行う     プランニング１：POとPBIの最終確認      プランニング２：開発タスクブレイク                            |
| 3   |                                 | イテレーション                                         | ゴールや結果にアプローチするプロセスをくり返すこと。                                                                                                               | 〇       |                      |                                                                                                                           |
| 4   |                                 | プランニングポーカー                                   | スプリント計画時のタスクを見積もるためのプランニングポーカー                                                                                                       | 〇       | Miro                 |                                                                                                                           |
| 5   |                                 | ベロシティ計測                                         | プロジェクトベロシティの計測                                                                                                                                       | 〇       | Miro                 |                                                                                                                           |
| 6   |                                 | 日次ミーティング                                       | 現在の問題を解決するための短いデイリーミーティング                                                                                                                 | 〇       |                      | デイリースクラム     15分厳守を目指して行っている                                                                         |
| 7   |                                 | ふりかえり                                             | 前のスプリント（イテレーション）から学ぶためにふりかえる                                                                                                           | 〇       | Miro                 | ■チーム全体     　1週間に1回、1時間で行っている          ■開発者のみ     　毎日15分、日々の困りごとや進捗を洗い出している |
| 8   |                                 | かんばん                                               | ジャストインタイムの継続的なデリバリを強調した管理手法                                                                                                             |          |                      |                                                                                                                           |
| 9   |                                 | イテレーション／スプリントレビュー                     | 完了した仕事を表明するスプリントレビューミーティング                                                                                                               | 〇       | Zoom                 | 画面の操作は開発者が行う     本当はPOに操作してもらいたいが、環境の都合上開発者が操作している                             |
| 10  |                                 | タスクボード（タスクカード）                           | ボードに貼られたメンバーが継続的に更新するタスク                                                                                                                   | 〇       | Miro                 | 基本的にタスクは付箋にして管理している                                                                                    |
| 11  |                                 | バーンダウンチャート                                   | スプリント進捗をモニターするためのバーンダウンチャート                                                                                                             | 〇       | Miro                 |                                                                                                                           |
| 12  |                                 | 柔軟なプロセス                                         | 状況や対応に対応できる柔軟なプロセスにしている、もしくは、プロセスを柔軟に変更している                                                                             | 〇       |                      | 都度、メンバーから意見が出て改善されて行っている                                                                          |
| 13  | プロダクト                      | ユーザーストーリー                                     | 要求についての会話を行うときの開発チームとプロダクトオーナーのあいだの合意事項                                                                                     | 〇       |                      |                                                                                                                           |
| 14  |                                 | スプリントバックログ                                   | プロダクトオーナーとチーム間でのスプリントバックログへの相互コミットメント                                                                                         | 〇       |                      | リファイメントやプランニングで                                                                                            |
| 15  |                                 | インセプションデッキ                                   | 10の質問によりプロジェクトの属性を明らかする                                                                                                                       |          |                      |                                                                                                                           |
| 16  |                                 | プロダクトバックログ（優先順位付け）                   | プロダクトオーナーによる優先順位（プロダクトバックログ）の管理                                                                                                     | 〇       |                      |                                                                                                                           |
| 17  | プロダクト（Outcomeデリバリー） | バックログ・リファインメント                           |                                                                                                                                                                    | 〇       |                      | 1sprinに3回行っている                                                                                                     |
| 18  |                                 | 共感マッピング                                         | ユーザ像（ペルソナ）を構築し、視覚化する。ペルソナが見ている、考えている、やっている、感じているものを目に見える図として整理する                                   |          |                      |                                                                                                                           |
| 19  |                                 | バリューストリームマッピング                           | 顧客に価値を提供するまでに存在する工程をすべて洗い出し、流れ図で一覧できるようにし、ボトルネックの発見と改善点をあぶりだす                                         | 〇       | Miro                 |                                                                                                                           |
| 20  |                                 | インパクトマッピング                                   | 仮説構築→チーム目標をビジネスゴールと接続し、機能デリバリではなくビジネスゴールのデリバリを図る                                                                    |          |                      |                                                                                                                           |
| 21  |                                 | イベントストーミング                                   | ドメイン（業務領域）イベント、外部システム、接続におけるギャップ確認とフロー化、集約（重複）の抽出、境界コンテキストの定義、などを経て業務ドメインモデルを構築する |          |                      |                                                                                                                           |
| 22  | フィードバック                  | 迅速なフィードバック                                   | 迅速なフィードバックを得られるような取り組みを行っている                                                                                                           |          |                      |                                                                                                                           |
| 23  | 設計開発                        | ペアプログラミング                                     | すべての製品コードはペアプロで開発している                                                                                                                         | 〇       |                      | 1sprintごとにペアを変えている     ペアプロの心得をチームで学習したうえで行っている                                        |
| 24  |                                 | 自動化された回帰テスト                                 | 自動化された回帰テストを行っている                                                                                                                                 | 〇       |                      |                                                                                                                           |
| 25  |                                 | テスト駆動開発                                         | 単体テストを書き、そのテストを通るようなコードを実装する                                                                                                           | 〇       |                      |                                                                                                                           |
| 26  |                                 | ユニットテストの自動化                                 | ユニットテストの自動化                                                                                                                                             | 〇       |                      |                                                                                                                           |
| 27  |                                 | 受入テスト                                             | 受入テストの実施と、その結果を公開している                                                                                                                         | 〇       |                      |                                                                                                                           |
| 28  |                                 | システムメタファ                                       | 関係者全員が、そのシステムがどのように動くかについて伝えることができるストーリー                                                                                   |          |                      |                                                                                                                           |
| 29  |                                 | スパイク・ソリューション                               | リスクを軽減するために、かくれた問題を探索するための簡単なプログラム（スパイク・ソリューション）の試作                                                             |          |                      |                                                                                                                           |
| 30  |                                 | リファクタリング                                       | 定常的なリファクタリング                                                                                                                                           | 〇       |                      |                                                                                                                           |
| 31  |                                 | シンプルデザイン                                       | 設計をシンプルに保つ                                                                                                                                               | 〇       |                      |                                                                                                                           |
| 32  |                                 | 逐次の統合                                             | 一度に統合するコードはひとつだけとする                                                                                                                             |          |                      |                                                                                                                           |
| 33  |                                 | 継続的インテグレーション                               | 継続的イテレーション、または頻繁なインテグレーション                                                                                                               |          |                      |                                                                                                                           |
| 34  |                                 | 集団によるオーナーシップ                               | 全員がすべてのコードに対して責任をもつ                                                                                                                             | 〇       |                      |                                                                                                                           |
| 35  |                                 | コーディング規約                                       | 同意された標準のためのコーディング規約                                                                                                                             |          |                      |                                                                                                                           |
| 36  | 障害対応                        | バグ時の再現テスト                                     | バグが見つかったとき、そのテストがまず最初に作られる                                                                                                               |          |                      |                                                                                                                           |
| 37  | 利用ツール                      | 紙・手書きツール                                       | ポストイット（付箋紙）やCRC（class-responsibilitycollaboration）カードなどの使用                                                                                   |          |                      |                                                                                                                           |
| 38  | 人                              | 顧客プロキシ                                           | 要件や仕様をまとめるために顧客の業務に精通した顧客プロキシの設置                                                                                                   |          |                      |                                                                                                                           |
| 39  |                                 | オンサイト顧客                                         | 顧客といつでも／定期的にやりとりが可能である                                                                                                                       |          |                      |                                                                                                                           |
| 40  |                                 | プロダクトオーナー                                     | プロダクトオーナー役の設置                                                                                                                                         | 〇       |                      |                                                                                                                           |
| 41  |                                 | ファシリテータ（スクラムマスター）                     | スクラムマスターによる開発プロセスとプラクティスのファシリテート                                                                                                   | 〇       |                      | スクラムマスターはもちろんだが、開発者も行っている                                                                        |
| 42  |                                 | アジャイルコーチ                                       | アジャイルコーチがプロジェクトに参加している                                                                                                                       |          |                      |                                                                                                                           |
| 43  |                                 | 自己組織化チーム                                       | チームメンバーがタスクに志願するなど自律的なチームになっている                                                                                                     | 〇       |                      |                                                                                                                           |
| 44  |                                 | ニコニコカレンダー                                     | ニコニコカレンダーを用いてメンバーの気持ちを見える化している                                                                                                       |          |                      |                                                                                                                           |
| 45  | ファシリティ／ワークスペース    | 共通の部屋                                             | オープンスペースがチームに与えられている                                                                                                                           | 〇       |                      |                                                                                                                           |
| 46  |                                 | チーム全体が一つに                                     | チーム全員がひとつのゴールに向かうような取り組みを行っている                                                                                                       | 〇       |                      |                                                                                                                           |
| 47  |                                 | 人材のローテーション                                   | 多能工の育成などのため人材のローテーションを行っている                                                                                                             | 〇       |                      |                                                                                                                           |
| 48  | DevOps                          | Infrastructure as  Code (IaC)                          |                                                                                                                                                                    |          |                      |                                                                                                                           |
| 52  |                                 | リリースマネジメント                                   | リリース管理プロセスの完全自動化と結果の管理                                                                                                                       | 〇       |                      |                                                                                                                           |
| 53  |                                 | アプリパフォーマンス監視／ロギングとモニタリングの実施 |                                                                                                                                                                    | 〇       |                      |                                                                                                                           |
| 54  |                                 | ロードテストと自動スケーリング                         |                                                                                                                                                                    | 〇       |                      |                                                                                                                           |
| 55  |                                 | バージョン管理システムの共有                           | DevチームとOpsチームでバージョン管理を共有する                                                                                                                     | 〇       |                      |                                                                                                                           |
| 56  |                                 | マイクロサービス                                       |                                                                                                                                                                    |          |                      |                                                                                                                           |
| 57  |                                 | コミュニケーションとコラボレーション                   | チャット、メッセンジャー、チケット管理、インシデント管理、etc.様々なツールを用いての促進                                                                           | 〇       |                      | slack、Teamsを使用している                                                                                                |  |

## 開発言語、開発環境（主要なもの）

【開発言語】

* TypeScript

【開発ツール】

* Microsoft Visual Studio Code
* WebStorm（<https://www.jetbrains.com/ja-jp/webstorm/>）

【実行環境】

* node.js

【フレームワーク】

* vue.js
* nuxt.js

【ライブラリ】

* axios

【CI/CD】
AWSのサービスを使用して実現している

* code pipeline
* code build
* code commit
* cloud formation

採用理由は開発基盤がAWSだかです。
サービスの範囲でやりたいことも実現できたし、導入のコストが低かったからです。