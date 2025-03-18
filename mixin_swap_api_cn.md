# 交易 APIs

Endpoint: https://api.route.mixin.one/

## 获取 token 列表
GET /web3/tokens?source=mixin

列出支持的 token 列表

response
```go
type TokenView struct {
	AssetId     string          `json:"assetId"`
	Name        string          `json:"name"`
	Symbol      string          `json:"symbol"`
	Icon        string          `json:"icon"`
	Chain       TokenChain      `json:"chain"`
}

type TokenChain struct {
	ChainId  string `json:"chainId"`
	Symbol   string `json:"symbol"`
	Name     string `json:"name"`
	Icon     string `json:"icon"`
	Decimals int    `json:"decimals"`
}
```

## 估算价格
GET /web3/quote

估算 outputMint 的数量

request query 参数
- inputMint: Mixin 里的 asset id
- outputMint: Mixin 里的 asset id
- amount: 表示 inputMint 的数量
- source: 固定为字符串 `mixin`

如果是 3 ETH -> USDT，则为
"web3/quote?source=mixin&inputMint=43d61dcd-e413-450d-80b8-101d5e903357&outputMint=4d8c508b-91c5-375b-92b0-ee702ed2dac5&amount=3"

response
- outAmount: 预估的 outputMint 兑换数量
- payload: 兑换路径，如果返回则在 POST `/web3/swap` 的时候需要携带
```go
type QuoteRespView struct {
	InputMint  string `json:"inputMint"`
	InAmount   string `json:"inAmount"`
	OutputMint string `json:"outputMint"`
	OutAmount  string `json:"outAmount"`
	Payload    string `json:"payload"`
}
```

## 生成交易
POST /web3/swap

request body 参数
- payer: Mixin 里的 user id
- inputMint: Mixin 里的 asset id
- outputMint: Mixin 里的 asset id
- inputAmount: 表示 inputMint 的数量
- payload: GET `/web3/quote` 返回的兑换路径
- referral: Mixin 里的 user id，可选参数

```go
type SwapReq struct {
	Payer       string `json:"payer"`
	InputMint   string `json:"inputMint"`
	InputAmount string `json:"inputAmount"`
	OutputMint  string `json:"outputMint"`
	Payload     string `json:"payload"`
    Referral    string `json:"referral"`
}
```

response
- tx: Mixin 的支付链接，格式是 `mixin://mixin.one/pay/{routeBotId}?asset={assetUuid}&amount=xxx&trace={traceUuid}&memo={orderId}`
	- trace 是支付的 uuid
	- memo 里是 orderId
- quote: 对应的 GET `/web3/quote` 的 response
```go
type SwapRespView struct {
	Tx     string        `json:"tx"`
	Quote  QuoteRespView `json:"quote"`
}
```

## 错误码
| 错误码 | 描述 |
| -------- | ------- |
| 10611 | Invalid swap |
| 10614 | Invalid quote amount |
| 10615 | No available quote found |

如果是错误 10614，那么可能会包含一个`extra`字段提示用户安全的输入范围
```go
type QuoteRange struct {
	AssetId string        `json:"asset_id"`
	Min     string        `json:"min"`
	Max     string        `json:"max"`
	Source  se.SwapSource `json:"source"`
}
```