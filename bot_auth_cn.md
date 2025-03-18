# Mixin bot 访问 Mixin Route 认证示例

1. 使用 https://github.com/MixinNetwork/bot-api-go-client/blob/master/botauth.go

2. 在发起 http 请求的时候带上 2 个 header

```go
mixinRouteBotId := "61cb8dd4-16b1-4744-ba0c-7b2d2e52fc59"
client := bot.NewDefaultClient(su, logger)
ts := time.Now().Unix()
signature, _ := client.SignRequest(ctx, ts, mixinRouteBotId, request)
request.Header.Add("MR-ACCESS-TIMESTAMP", fmt.Sprintf("%d", ts))
request.Header.Add("MR-ACCESS-SIGN", signature)
```