---
name: fathom-analytics-api-go-sdk
description: "Go SDK for Fathom Analytics API. Use when writing Go code that calls Fathom Analytics API with the fathom-analytics package: installing it, constructing and authenticating the client, and calling API operations."
---

# Fathom Analytics API Go SDK

Generated Go client for Fathom Analytics API, published as `fathom-analytics`. Use the generated client instead of hand-writing HTTP requests.

## Install

```sh
go get fathom-analytics
```

## Client setup and authentication

```go
import (
	"context"
	"fmt"

	sdk "fathom-analytics"
)

client := sdk.NewClient()
```

Provide credentials using the options below. Environment variables are read automatically when the target runtime supports them:

- `option.WithBearerAuth` (env: `BEARER_AUTH`) — Authenticate with a personal API token created at https://app.usefathom.com/api, sent as `Authorization: Bearer <token>`.

## Calling operations

```go
package main

import (
	"context"
	"os"

	sdk "fathom-analytics"
	"fathom-analytics/option"
)

func main() {
	client := sdk.NewClient(
		option.WithBearerAuth(os.Getenv("BEARER_AUTH")),
	)

	err := client.Account.List(context.Background())
	if err != nil {
		panic(err)
	}
}
```

Method names, parameter shapes, and response types are generated from the API description — do not guess them. Look up the exact call signature in [api.md](./api.md) before writing a call.

## Error handling

Non-success responses return generated API errors. Error objects expose status, headers, response body, and request metadata where the target runtime supports it.

```go
err := client.Account.List(context.Background())
if err != nil {
	var apiErr *sdk.Error
	if errors.As(err, &apiErr) {
		fmt.Println(apiErr.StatusCode, apiErr.RawJSON())
	}
	panic(err)
}

// imports: "context", "errors", "fmt", sdk "fathom-analytics"
```

## Requirements

- Go 1.22 or newer

## Reference files

- [README.md](./README.md) — full feature tour: client options, request options, retries and timeouts, logging.
- [api.md](./api.md) — complete catalogue of every operation with request and response types.
