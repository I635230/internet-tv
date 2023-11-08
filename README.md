# インターネットTVサービスのDB, SQL

インターネットTVサービスを開発するための、DB設計とデータ取得SQLをまとめたリポジトリです。

株式会社ユーブルが運営するサービス「アプレンティス」における学習の一環で作成したものになります。

## 本リポジトリについて

インターネットTVサービスを新規に作成する想定で、以下3点を含みます。

- DB設計(readme.md)
- DB実装 + サンプルデータ入力の手順書(operation_manual.md)
- データ取得SQL(sql.md)

詳しくは、各ドキュメントをご覧ください。

## インターネットTVサービスの仕様

- ドラマ1、ドラマ2、アニメ1、アニメ2、スポーツ、ペットなど、複数のチャンネルがある
- 各チャンネルの下では時間帯ごとに番組枠が1つ設定されており、番組が放映される
- 番組はシリーズになっているものと単発ものがある。シリーズになっているものはシーズンが1つものと、シーズン1、シーズン2のように複数シーズンのものがある。各シーズンの下では各エピソードが設定されている
- 再放送もあるため、ある番組が複数チャンネルの異なる番組枠で放映されることはある
- 番組の情報として、タイトル、番組詳細、ジャンルが画面上に表示される
- 各エピソードの情報として、シーズン数、エピソード数、タイトル、エピソード詳細、動画時間、公開日、視聴数が画面上に表示される。単発のエピソードの場合はシーズン数、エピソード数は表示されない
- ジャンルとしてアニメ、映画、ドラマ、ニュースなどがある。各番組は1つ以上のジャンルに属する
- KPIとして、チャンネルの番組枠のエピソードごとに視聴数を記録する。なお、一つのエピソードは複数の異なるチャンネル及び番組枠で放送されることがあるので、属するチャンネルの番組枠ごとに視聴数がどうだったかも追えるようにする

## DB設計

table: programs

| カラム名 | データ型 | NULL | キー | 初期値 | AUTO INCREMENT |
| --- | --- | --- | --- | --- | --- |
| id | BIGINT |  | PRIMARY |  | YES |
| channel | VARCHAR(30) |  |  |  |  |
| date | DATE |  |  |  |  |
| time_zone_id | INT |  |  |  |  |
| episode_id | BIGINT |  |  |  |  |
- 外部キー制約：channelに対して、channelsテーブルのchannelカラムから設定
- 外部キー制約：time_zone_idに対して、time_zoneテーブルのidカラムから設定
- ユニークキー制約：{channel, date, time_zone_id}カラムに対して設定
- 外部キー制約：episode_idに対して、episodesテーブルのidカラムから設定

table: tv_shows

| カラム名 | データ型 | NULL | キー | 初期値 | AUTO INCREMENT |
| --- | --- | --- | --- | --- | --- |
| id | INT |  | PRIMARY |  | YES |
| title | VARCHAR(50) |  | INDEX |  |  |
| detail | TEXT |  |  |  |  |

table: episodes

| カラム名 | データ型 | NULL | キー | 初期値 | AUTO INCREMENT |
| --- | --- | --- | --- | --- | --- |
| id | BIGINT |  | PRIMARY |  | YES |
| season | INT |  |  |  |  |
| chapter | INT |  |  |  |  |
| tv_show_id | INT |  | INDEX |  |  |
| title | VARCHAR(50) |  |  |  |  |
| detail | TEXT |  |  |  |  |
| duration | INT |  |  |  |  |
| release_date | DATE | YES |  |  |  |
| views | BIGINT |  |  | 0 |  |
- 単発の場合はseasonを-1とする
- 外部キー制約：tv_show_idに対して、tv_showsテーブルのidカラムから設定
- ユニークキー制約：{season, chapter, tv_show_id}カラムに対して設定

table: tv_show_categories

| カラム名 | データ型 | NULL | キー | 初期値 | AUTO INCREMENT |
| --- | --- | --- | --- | --- | --- |
| id | BIGINT |  | PRIMARY |  | YES |
| title | VARCHAR(30) |  |  |  |  |
| category | VARCHAR(30) |  |  |  |  |
- 外部キー制約：titleに対して、tv_showsテーブルのtitleカラムから設定
- 外部キー制約：categoryに対して、categoriesテーブルのcategoryカラムから設定

table: time_zones

| カラム名 | データ型 | NULL | キー | 初期値 | AUTO INCREMENT |
| --- | --- | --- | --- | --- | --- |
| id | INT |  | PRIMARY |  | YES |
| start | TIME |  |  |  |  |
| end | TIME |  |  |  |  |

table: categories

| カラム名 | データ型 | NULL | キー | 初期値 | AUTO INCREMENT |
| --- | --- | --- | --- | --- | --- |
| category | VARCHAR(30) |  | PRIMARY |  |  |

table: channles

| カラム名 | データ型 | NULL | キー | 初期値 | AUTO INCREMENT |
| --- | --- | --- | --- | --- | --- |
| channel | VARCHAR(30) |  | PRIMARY |  |  |

## 使用方法

DBを実際に作成する場合は、operation_manual.mdの手順に従ってください。

作成したDBにSQLを適用する場合は、sql.mdのソースをコピーして使用してください