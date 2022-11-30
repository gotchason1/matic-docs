---
id: request-api-guide
title: Authentication Request
sidebar_label: Authentication Request
description: "Basic Auth and Query-based Auth requests."
keywords: 
  - docs
  - polygon
  - id
  - auth
image: https://wiki.polygon.technology/img/thumbnail/polygon-id.png
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

The first step to interact with a wallet involves presenting an authentication request. In this tutorial, we will explain the difference between two types of authentication requests: **Basic Auth** and **Query-based Auth**.

A Basic Auth requires users to prove that they are **the owners of an identity**. No further requirements are set. 

A Query-based Auth requires users to prove that:

- They are the owners of an identity. 
- They own a claim that satisfies a certain property.

## Basic Auth

The Basic Auth Request allows verifiers to interact with a wallet and authenticate the user it by its identifier.  
Polygon ID Basic Auth can be implemented by any platform that is interested in providing a seamless web2-like login experience to its users without setting any specific requirements.

### CreateAuthorizationRequest

Generate an Auth Request to the user that includes a *reason* for authenticating. The *audience* represents the identifier of the requester, while the URL is the callback URL where the user must send the response for verification.

<Tabs
  defaultValue="golang"
  values={[
    { label: 'Golang', value: 'golang', },
    { label: 'Javascript', value: 'javascript', },
  ]
}>

<TabItem value="golang">

#### GoLang

```go
var request protocol.AuthorizationRequestMessage
request = auth.CreateAuthorizationRequest(reason,  audience, url)
```

</TabItem>
<TabItem value="javascript">

#### Javascript

```js
const request : protocol.AuthorizationRequestMessage = auth.createAuthorizationRequest(reason, audience, url)
```

</TabItem>
</Tabs>

### CreateAuthorizationRequestWithMessage

The same functionality of CreateAuthorizationRequest is applicable here but it also includes a *messageToSign*. This message will be shown to the users inside their wallets and will be signed as part of the response.

<Tabs
  defaultValue="golang"
  values={[
    { label: 'Golang', value: 'golang', },
    { label: 'Javascript', value: 'javascript', },
  ]
}>

<TabItem value="golang">

#### GoLang

```go
var request protocol.AuthorizationRequestMessage
request = auth.CreateAuthorizationRequestWithMessage(reason, messageToSign, audience, url)
```

</TabItem>
<TabItem value="javascript">

#### Javascript

```js
const request : protocol.AuthorizationRequestMessage = auth.createAuthorizationRequestWithMessage(reason, messageToSign,audience, url)
```

</TabItem>
</Tabs>

## Query-based Auth 

Generate a request to proof that satisfies certain requirements. 

The Query-based Auth Request allows verifiers to interact with a wallet by setting up specific requirements for authentication. These requirements are the conditions that the user has to satisfy based on the claims held in his/her wallet.

:::note
The Query has to be attached to the Basic Auth Request output of the previous API. An example of its usage can be found [<ins>here</ins>](https://github.com/0xPolygonID/tutorial-examples/tree/main/verifier-integration/js/index.js#L50).
:::

`id` represents the request id: ideally, in production, it should be a unique value for each request. `circuit_id` represents the identifier of the circuit used by the user to generate the requested proof: it can be either `credentialAtomicQuerySig` or `credentialAtomicQueryMTP`. In this case, the user has to provide a proof that he/she owns a claim issued by the `allowedIssuer` of schema `type` **KYCCountryOfResidenceCredential** described in `url`. This claim contains details of the country of residence of the receiver. In this scenario, the user has to prove that the value contained in the attribute `countryCode` is not in `nin` 840, 120, 340, 509.

<Tabs
  defaultValue="golang"
  values={[
    { label: 'Golang', value: 'golang', },
    { label: 'Javascript', value: 'javascript', },
  ]
}>

<TabItem value="golang">

#### GoLang

```go
var mtpProofRequest protocol.ZeroKnowledgeProofRequest
mtpProofRequest.ID = 1 
mtpProofRequest.CircuitID = string(circuits.AtomicQuerySigCircuitID)
mtpProofRequest.Rules = map[string]interface{}{
    "query": pubsignals.Query{
        AllowedIssuers: []string{"11AbuG9EKnWVXK1tooT2NyStQod2EnLhfccSajkwJA"},
        Req: map[string]interface{}{
            "countryCode": map[string]interface{}{
                "$nin": []int{840, 120, 340, 509},
            },
        },
        Schema: protocol.Schema{
            URL:  "https://raw.githubusercontent.com/iden3/claim-schema-vocab/main/schemas/json-ld/kyc-v2.json-ld",
            Type: "KYCCountryOfResidenceCredential",
        },
    },
}
request.Body.Scope = append(request.Body.Scope, mtpProofRequest)       
```

</TabItem>
<TabItem value="javascript">

#### Javascript

```js
const proofRequest: protocol.ZKPRequest = {
id: 1,
circuit_id: 'credentialAtomicQuerySig',
rules: {
    query: {
    allowedIssuers: ['11AbuG9EKnWVXK1tooT2NyStQod2EnLhfccSajkwJA'],
    schema: {
        type: 'KYCCountryOfResidenceCredential',
        url: 'https://raw.githubusercontent.com/iden3/claim-schema-vocab/main/schemas/json-ld/kyc-v2.json-ld',
    },
    req: {
        countryCode: {
        $nin: [840, 120, 340, 509],
        },
    },
    },
},
};
request.body.scope = [...scope, proofRequest];
```
</TabItem>
</Tabs>

:::tip
Check out our [<ins>Query Language guide</ins>](./zk-query-language.md) to design other types of query request.
:::
