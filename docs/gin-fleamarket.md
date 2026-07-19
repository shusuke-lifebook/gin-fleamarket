# gin-fleamarket
- プロジェクトの作成
  - mkdir gin-fleamarket
  - code gin-fleamarket
  - VSCodeのターミナルでginをインストール
    - コマンド: go get -u github.com/gin-gonic/gin
  - コマンド： go run main.go
  - コマンド： curl localhost:8080/ping

```go
func main() {
	router := gin.Default()
	router.GET("/ping", func(c *gin.Context) {
		c.JSON(200, gin.H{
			"message": "pong",
		})
	})
	router.Run("localhost:8080")
}
```

- ホットデプロイ
  - go install github.com/air-verse/air@latest
  - air init
  - air
    - サーバー自体も立ち上がる

## 開発するAPIについて

## DTO(Data Transfer Object)とは
- データの受け渡しに使われるオブジェクト
- Gin特有の機能というわけではなく、ソフトウェア開発において広く使われている
- ex
  - データベースとモデル間のデータのやり取り
  - リクエストオブジェクトからのデータ受け取り


## DTOを使うメリット
1. メンテナンス性が高まる
  - データの内容や型などが変更になった場合でも、修正箇所をDTO内に閉じ込めることが出来る
2. 安全性が高まる
  - やり取りするデータをDTOの型に制限することができるので、誤ったデータが扱われるリスクが減る
3. Ginのバリデーション機能が使える
  - 型チェックだではなく複雑なバリデーションも可能

## バリデーション
- リクエストやレスポンスデータの妥当性チェック
- ex)
  - ユーザー名は2文字以上20文字以内
  - 金額は1円以上
  - パスワードは英数字で8文字以上

## Ginでバリデーションを行う方法
- 内部でvalidatorパッケージを使用
  - 構造体のタグに基づいてフィールド値を検証
  - Gitと合わせてインストールされる

## Ginでバリデーションを行う方法
- STEP1 リクエストデータの構造体(DTO)を定義
```go
type CreateItemInput struct {
  Name string `json:"name"`
  Price uint `json:"price"`
  Description string `json:"description"`
}
```
- STEP2 DTOにバリデーションのためのタグを追加
```go
type CreateItemInput struct {
  Name string `json:"name" binding:"required, min=2"`
  Price uint `json:"price" binding:"required, min=1, max=999999"`
  Description string `json:"description"`
}
```
- STEP3 リクエストデータをDTOにバインド
```go
var input dto.CreateItemInput
if err := ctx.ShouldBindJSON(&input); err != nil {
  ctx.JSON(http.StatusBadRequest, gin.H{
    "error": err.Error()
  })
  return
}
```
## データベース環境の構築
- docker-compose.yaml
- sudo chown -R $USER:$USER docker
- sudo chmod -R 755 docker
- http://localhost:81/
  - gin@example.com
  - ginpassword

## ORMとは
- オブジェクト指向の言語とRDBの**非互換なデータをマッピング**する役割
  - オブジェクト指向言語は「**現実世界の物事に即したデータモデル**」
  - RDBは「**検索などRDBとしての役割を果たすために最適化されたモデル**」

## ORMを使うメリット
- SQLを書かなくてもDB操作ができる
- データの定義が１箇所で済むので、メンテナンス性が高まる
- データベースの種類やその特定のSQLの方言に関する心配が不要

## ORMを使うデメリット
- ORMライブラリの操作を学ぶ必要がある
- パフォーマンスチューニングが難しい

## GinのORMライブラリ
- GORM
  - GoのORMの中で非常に人気が高く広く使われる
  - 高機能でフレキシブルなツール

## godotenv
- go get github.com/joho/godotenv

## GORM
- go get -u gorm.io/gorm
- go get -u gorm.io/driver/sqlite
- go get -u gorm.io/driver/postgres
  - [データベースに接続する](https://gorm.io/ja_JP/docs/connecting_to_the_database.html)
- [モデルの宣言](https://gorm.io/ja_JP/docs/models.html#%E3%83%95%E3%82%A3%E3%83%BC%E3%83%AB%E3%83%89%E3%81%AB%E6%8C%87%E5%AE%9A%E5%8F%AF%E8%83%BD%E3%81%AA%E3%82%BF%E3%82%B0)
- DBマイグレーション
  - migrationsディレクトリを作成
  - migration.goを作成
  - go run migrations/migration.go
    - マイグレーションの実行

## JWTとは
- Json Web Tokenの略称で、認証情報をJSONの形式で格納したもの
- 電子署名により、改ざんの検知が可能
- 認証用のトークンとして利用される
  
## JWTの構成
- 3つの要素から構成される
  - ヘッダ： ハッシュアリゴリズムの情報などのメタデータ
  - ペイロード：認証対象の情報で、ユーザー名やIDなどの任意情報
  - 署名：ヘッダとペイロードをエンコードしたものに秘密鍵を加えてハッシュ化した値
- 要素ごとにBase64エンコードされている
- ３つの要素が.（ドット）結合されている

## JWTを使った認証
1. JWTの取得
   1. username/password
      1. 認証情報の検証
      2. Token生成
   2. Token(PCのロカールストレージなどに保存)
2. JWTの検証
   1. Token
      1. Token検証
   2. id、usernameなど
3. JWTのメリット
- 改ざん検知が可能
  - 署名が含まれているため、改ざんをチェックできる
- セキュアトークン発行
  - 有効期限をつけるなど、セキュアなToken発行を行える
- サーバー側での状態管理不要
  - セッションと異なり、状態をサーバーで管理する必要がない。
- 柔軟性
  - 任意のデータをTokenに含めることができる

## JWTのインストール
- go get -u github.com/golang-jwt/jwt/v5
- SECRET_KEY
  - openssl rand -hex 32

- 参考サイト
  - [Gin Web Framework](https://gin-gonic.com/ja/docs/quickstart/)
  - [air](https://github.com/air-verse/air)
  - [Validator](https://pkg.go.dev/github.com/go-playground/validator/v10#section-readme)
  - [godotenv](https://github.com/joho/godotenv#installation)
  - [Gormインストール](https://gorm.io/ja_JP/docs/index.html#%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB)
  - [golang-jwt](https://github.com/golang-jwt/jwt?tab=readme-ov-file#installation-guidelines)