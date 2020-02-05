# 日実績から月実績作成


## 概要
中継サーバーのRDSへ、月別実績を作成する。
管理画面にて実績表示するために必要。

## 内容

### 対象テーブル
テーブル名： `store_monthly_sales`  店舗別月別売上実績

|  Column  |  Type  |  Attribute  |  Comment  |
| ---- | ---- | ---- | ---- |
| id                    | serial  |  PRIMARY KEY |  id  |
| company_code          | varchar(9) | NOT NULL  | 企業コード |
| store_code            | varchar(9) | NOT NULL  | 店舗コード |
| sale_month            | timestamp  | NOT NULL  | 年月（YYYY-MM-01として登録） |
| total_ex_vat          | integer    |           | 税抜合計 |
| total_in_vat          | integer    |           | 税込合計 |
| quantity              | integer    |           | 売上点数 |
| customer              | integer    |           | 客数（＝精算済カート数） |
| total_per_customer    | integer    |           | 客単価 |
| quantity_per_customer | integer    |           | 平均購入点数 |
| created_at            | timestamp  |           | 作成日時 |
| updated_at            | timestamp  |           | 更新日時 |


### 処理内容
店舗別日別売上実績（`store_daily_sales`) から店舗別・月別の実績を合計して店舗別月別売上実績（`store_monthly_sales`) へ各column値の登録を行う。

##### 合計（SUM）する対象column
* total_ex_vat
* total_in_vat
* quantity
* customer
* total_per_customer
* quantity_per_customer


#### 参考
テーブル名： `store_daily_sales`  店舗別日別売上実績

|  Column  |  Type  |  Attribute  |  Comment  |
| ---- | ---- | ---- | ---- |
| id                    | serial  |  PRIMARY KEY |  id  |
| company_code          | varchar(9) | NOT NULL  | 企業コード |
| store_code            | varchar(9) | NOT NULL  | 店舗コード |
| sale_date             | timestamp  | NOT NULL  | 年月日 |
| total_ex_vat          | integer    |           | 税抜合計 |
| total_in_vat          | integer    |           | 税込合計 |
| quantity              | integer    |           | 売上点数 |
| customer              | integer    |           | 客数（＝精算済カート数） |
| total_per_customer    | integer    |           | 客単価 |
| quantity_per_customer | integer    |           | 平均購入点数 |
| created_at            | timestamp  |           | 作成日時 |
| updated_at            | timestamp  |           | 更新日時 |


### 実行タイミング
1日1回。
prismatixから日別実績を登録した後に実行する。
夜間想定。
