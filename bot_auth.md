# Mixin bot access Mixin Route API demo

1. use https://github.com/MixinNetwork/bot-api-go-client/blob/master/botauth.go

2. include 2 headers when making HTTP requests

```go
mixinRouteBotId := "61cb8dd4-16b1-4744-ba0c-7b2d2e52fc59"
client := bot.NewDefaultClient(su, logger)
ts := time.Now().Unix()
signature, _ := client.SignRequest(ctx, ts, mixinRouteBotId, request)
request.Header.Add("MR-ACCESS-TIMESTAMP", fmt.Sprintf("%d", ts))
request.Header.Add("MR-ACCESS-SIGN", signature)
```