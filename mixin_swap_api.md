# Mixin Route Swap APIs

Endpoint: https://api.route.mixin.one/

## Get Token List
GET /web3/tokens?source=mixin

List of supported tokens

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

## Quote
GET /web3/quote

Estimate the amount of outputMint

request query parameters
- inputMint: Asset ID in Mixin
- outputMint: Asset ID in Mixin
- amount: Represents the quantity of inputMint
- source: Fixed string `mixin`

For example, for 3 ETH -> USDT, it would be
"web3/quote?source=mixin&inputMint=43d61dcd-e413-450d-80b8-101d5e903357&outputMint=4d8c508b-91c5-375b-92b0-ee702ed2dac5&amount=3"

response
- outAmount: Estimated quantity of outputMint
- payload: Exchange path, which needs to be carried when making a POST request to `/web3/swap`
```go
type QuoteRespView struct {
	InputMint  string `json:"inputMint"`
	InAmount   string `json:"inAmount"`
	OutputMint string `json:"outputMint"`
	OutAmount  string `json:"outAmount"`
	Payload    string `json:"payload"`
}
```

## Swap
POST /web3/swap

request body parameters
- payer: User ID in Mixin
- inputMint: Asset ID in Mixin
- outputMint: Asset ID in Mixin
- inputAmount: Represents the quantity of inputMint
- payload: Exchange path returned from GET `/web3/quote`
- referral: User ID in Mixin, optional

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
- tx: Mixin payment link, format is `https://mixin.one/pay/{routeBotId}?asset={assetUuid}&amount=xxx&trace={traceUuid}&memo={orderId}`
	- trace is pay uuid
	- memo is orderId 
- quote: Corresponding response from GET `/web3/quote`
```go
type SwapRespView struct {
	Tx     string        `json:"tx"`
	Quote  QuoteRespView `json:"quote"`
}
```

## Error Codes
| Error Code | Description |
| ----------- | ----------- |
| 10611      | Invalid swap |
| 10614      | Invalid quote amount |
| 10615      | No available quote found |

For error code 10614, there may be an `extra` field to suggest safe input ranges for users
```go
type QuoteRange struct {
	AssetId string        `json:"asset_id"`
	Min     string        `json:"min"`
	Max     string        `json:"max"`
	Source  se.SwapSource `json:"source"`
}
```