# BCReader API

URI: `https://<domain>/5g/<salon_hash>/api/bcreader/`


## レスポンスヘッダー

ステータスが正常の時に200系、エラーの場合は400系もしくは500系を返します。
詳細があればボディで返します。ボディがない場合もありますのでご注意ください。
ステータスで判断してください。

返すステータスは 200, 204, 400, 401, 403, 500です。

レスポンスボディはJSONです (Content-Type: application/json)
JSONデータは全てのAPIで共通の構造になっており以下のようなものです。
`application/json`以外のタイプは無視してください。

### JSONの例

正常の場合:

```javascript/json
{
  "data": {
    "staff": {
      id: "STF00001",
      name: "権平"
    },
    ...
  },
  "errors": []
}
```

エラーの場合:

```javascript/json
{
  "data": {},
  "errors": [
    {
      "name": "param1",
      "error": "数字以外の文字が利用されています。"
    },
    {
      "name": "param2",
      "error": "100文字以内にしてください。"
    }
  ]
}
```

## エラー

- 400:
  パラメーターが間違っている時
- 401 / 403:
  認証エラー
- 500:
  サーバーエラー

janCode: 4900000000000 (13桁) でGETで叩いたら500を返します。

## JANコード

- 13桁以内の数字
- 重複可能なのでいつも配列を返します

## アプリ認証

- 認証タイプ: BASIC
- Username: BCReader
- Password: <>

## ユーザ認証

HTTP Cookieを有効にしてください

`/login`を参考

## POST /login

- (必) userId: String
- (必) userPw: String

返り値:

```javascript/json
{
  "data": {
    "staff": {
      "id": String,
      "name": String
    },
    "accessiblePlaces": [  // 参照可能店舗
      {
        "id": String,
        "name": String
      },
      ...
    ],
    "allPlaces": [  // 全店舗
      {
        "id": String,
        "name": String
      },
      ...
    ],
    "dealers": [  // 仕入先
      {
        "id": String,
        "name": String
      },
      ...
    ]
  },
  "errors": []
}
```

## (入荷) GET /arrival

- (必) placeId: String  // 入荷店舗ID
- (必) janCode: String

返り値:

```javascript/json
{
  "data": {
    "products": [
      {
        "id": String,  // 商品ID
        "name": String,  // 商品名
        "schedules": [  // 予定があった場合
          {
            "id": String,  // 入荷予定ID
            "quantity": Number,  // 数量
            "place": {  // 出荷元
              "type": String,  // "dealer"または"place"
              "id": String
            },
            "memo": String  // 備考
          },
          ...
        ]
      },
      ...
    ]
  },
  "errors": []
}
```

## (入荷) POST /arrival

- (必) productId: String
- (必) quantity: Number
- (必) placeId: String  (入荷店舗ID)
- scheduledId: String
- memo: String  (1000文字以下)

## (出荷) GET /shipping

- (必) placeId: String  // 出荷店舗ID
- (必) janCode: String

返り値:

```javascript/json
{
  "data": {
    "products": [
      {
        "id": String,
        "name": String,
        "schedules": [  // 予定があった場合
          {
            "id": String,  // 出荷予定ID
            "quantity": Number,  // 数量
            "place": {  // 入荷先
              "type": String,  // "dealer"または"place"
              "id": String
            },
            "memo": String  // 備考
          },
          ...
        ]
      ...
      },
    ]
  },
  "errors": []
}
```

## (出荷) POST /shipping

- (必) productId: String
- (必) quantity: Number
- (必) fromPlaceId: String
- (必) toPlaceId: String  (店舗IDまたは仕入先ID)
- scheduledId: String
- memo: String

## (棚卸し) GET /inventory

- (必) placeId: String  // 棚卸し店舗ID
- (必) janCode: String

返り値:

```javascript/json
{
  "data": {
    "products": [
      {
        "id": String,
        "name": String,
        "quantity": Number  // 現在数量
      },
      ...
    ]
  },
  "errors": []
}
```

## (棚卸し) POST /inventory

- (必) productId: String
- (必) quantity: Number
- (必) placeId: String
- memo: String

## (業務使用) GET /internal_use

- (必) placeId: String  // 業務使用店舗ID
- (必) janCode: String

返り値:

```javascript/json
{
  "data": {
    "products": [
      {
        "id": String,
        "name": String
      },
      ...
    ]
  },
  "errors": []
}
```

## (業務使用) POST /internal_use

- (必) productId: String
- (必) quantity: Number
- (必) placeId: String
- memo: String

## (廃棄) GET /disposal

- (必) placeId: String  // 廃棄店舗ID
- (必) janCode: String

返り値:

```javascript/json
{
  "data": {
    "products": [
      {
        "id": String,
        "name": String
      },
      ...
    ]
  },
  "errors": []
}
```

## (廃棄) POST /disposal

- (必) productId: String
- (必) quantity: Number
- (必) placeId: String
- memo: String
