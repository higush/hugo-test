---
title: "Device Parameter"
date: 2020-01-22T13:40:47+09:00
draft: true
---

# スマートフォンの機種ごとに異なる設定項目を取得する API

## 概要

カゴ抜け対応で、スマートフォンの機種ごとに異なる設定項目が必要となる。  
そこで、機種ごとの設定を S3 バケットにセットしておき、その JSON をそのまま返却するための API を作成する。  
スマホの機種は API 問い合わせ時の HEADER にセットする。

使用する S3 バケットはアプリバージョンチェックのものと同じものを利用する。

## エンドポイント

https://api.digi-cr.com/v1/guest/users/smartphone-model

## HEADER

- X-API-KEY：abcdefg
- X-APP-VERSION：1.0.0
- X-APP-OS-TYPE：iOS 10.2
- X-APP-PHONE-MODEL: Xperia（新規）

## S3 に保存する設定ファイル

ファイル名：「smartphone-model_XXX.json」  
※XXX は環境名。  
prod：本番  
stg：ステージング  
dev：開発

JSON に記載しているバージョンは OS のバージョン。

```JSON
{
  "iPhone": {           // X-APP-PHONE-MODEL（セット値そのまま）
    "iOS 11.0": {       // X-APP-OS-TYPE（セット値そのまま）
      "focusType": 0,
      "diff": {
        "length": 1024,
        "judgeThreshold": 40,
        "upperThreshold": 200,
        "lowerThreshold": 10,
        "intervalTime": 100
      },
      "edge": {
        "size": 3,
        "upperThreshold": 200,
        "middleThreshold": 100,
        "lowerThreshold": 5
      },
      "endTime": 1000,
      "doNotScanJudgeTime": 1000,
      "doNotScanThreshold": 30,
      "picFlameTime": 33,
      "startJudgeValue": 3,
      "startJudgeCount": 3,
      "holdTimeOutTime": 2000
    },
    "iOS 12.1": {
      "focusType": 0,
      "diff": {
        "length": 1024,
        "judgeThreshold": 40,
        "upperThreshold": 200,
        "lowerThreshold": 10,
        "intervalTime": 100
      },
      "edge": {
        "size": 3,
        "upperThreshold": 200,
        "middleThreshold": 100,
        "lowerThreshold": 5
      },
      "endTime": 1000,
      "doNotScanJudgeTime": 1000,
      "doNotScanThreshold": 30,
      "picFlameTime": 33,
      "startJudgeValue": 3,
      "startJudgeCount": 3,
      "holdTimeOutTime": 2000
    }
  },
  "Xperia": {
    "Android 9.0": {
      "focusType": 0,
      ︙
      ︙
    },
    "Android 9.0": {
      "focusType": 0,
      ︙
      ︙
    }
  },
  ︙
  ︙
  "default": {
    "iOS": {
      "focusType": 0,
      ︙
      ︙
    },
    "Android": {
      "focusType": 0,
      ︙
      ︙
    }
  }
}
```

## 返却値

「X-APP-OS-TYPE」と「X-APP-PHONE-MODEL」はリクエストヘッダーにセットされたものをそのまま利用する。  
ファイル内にヒットした情報があれば、その JSON 情報に `result` と `message` を付与して返却する。  
ヒットしなかった場合、デフォルト値を返却する（同一ファイル内に`default`のカラムがセットしてある）

### 正常時

```JSON
{
  "result": 0,
  "message": "success",
  "focusType": 0,
  "diff": {
    "length": 1024,
    "judgeThreshold": 40,
    "upperThreshold": 200,
    "lowerThreshold": 10,
    "intervalTime": 100
  },
  "edge": {
    "size": 3,
    "upperThreshold": 200,
    "middleThreshold": 100,
    "lowerThreshold": 5
  },
  "endTime": 1000,
  "doNotScanJudgeTime": 1000,
  "doNotScanThreshold": 30,
  "picFlameTime": 33,
  "startJudgeValue": 3,
  "startJudgeCount": 3,
  "holdTimeOutTime": 2000
}
```

### エラー時

```JSON
{
  "result": 1,
  "message": "error",
  "errorList": [
    {
      "code": 99999,
      "message": "「ファイル未存在」「対象機種なし」など"
    }
  ]
}
```

### 注意点

JSON 情報は加工することなく、そのまま返却する。JSON ファイルの情報は機種名・OS バージョンのカラムを除いて、カラム名等変わる可能性があるので、サーバー側では中身は特に意識しない。

```JSON
{
  "iPhone": {//機種名
    "iOS 12.1": {//OSバージョン
        ︙
        ★返却値であり、変わる可能性アリ
        ︙
    }
  },
  "Xperia": {//機種名
    "Android 10": {//OSバージョン
        ︙
        ★返却値であり、変わる可能性アリ
        ︙
    }
  }
}
```
