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

- 参考サイト
  - [Gin Web Framework](https://gin-gonic.com/ja/docs/quickstart/)
  - [air](https://github.com/air-verse/air)