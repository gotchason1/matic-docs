---
id: config
title: Configuration
sidebar_label: Configuration
description: "Configuration APIs."
keywords: 
  - docs
  - polygon
  - id
  - configuration
image: https://wiki.polygon.technology/img/thumbnail/polygon-id.png
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

The set of APIs included in the verification library can be split into two steps:

- [Authentication Request APIs](./request-api-guide) allow to design the criteria that the user must satisfy to authenticate.
- [Verification APIs](./verification-api-guide) allow verifying the proof sent by the user. The proof is generated as a response to the request. 

:::note
Check the [<ins>workflow</ins>](./verifier-library-intro) to disambiguate between these two processes.
:::

Both the APIs are available either in Golang or Javascript:

<Tabs
  defaultValue="golang"
  values={[
    { label: 'Golang', value: 'golang', },
    { label: 'Javascript', value: 'javascript', },
  ]
}>

<TabItem value="golang">

## GoLang

Install dependencies with the following command:

```bash
go get github.com/iden3/go-iden3-auth && github.com/iden3/go-circuits && github.com/iden3/iden3comm/protocol
```

Import the libraries:

```go
import (
    "github.com/iden3/go-circuits"
    auth "github.com/iden3/go-iden3-auth"
    "github.com/iden3/go-iden3-auth/loaders"
    "github.com/iden3/go-iden3-auth/pubsignals"
    "github.com/iden3/go-iden3-auth/state"
    "github.com/iden3/iden3comm/protocol"
)
```    
</TabItem>
<TabItem value="javascript">

## Javascript

Install dependencies with the following command:

```bash
npm i @iden3/js-iden3-auth --save
```

Import the libraries:

```js
const {auth, resolver, protocol, loaders, circuits} = require('@iden3/js-iden3-auth')
```
</TabItem>
</Tabs>