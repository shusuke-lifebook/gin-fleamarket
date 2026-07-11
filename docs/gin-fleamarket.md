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

- 参考サイト
  - [Gin Web Framework](https://gin-gonic.com/ja/docs/quickstart/)
  - [air](https://github.com/air-verse/air)
  - [Validator](https://pkg.go.dev/github.com/go-playground/validator/v10#section-readme)