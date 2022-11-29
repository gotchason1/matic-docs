---
id: json-rpc-txpool
title: TxPool
description: "List of TxPool JSON RPC commands for Polygon Edge."
keywords:
  - docs
  - polygon
  - edge
  - json
  - rpc
  - commands
---
import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';
import {useState} from 'react';

export const JsonRpcTerminal = (props) => {
  const [value, setValue] = useState("");
  const { method, params, network } = props;
  return (
    <div>
      <div>
        {value != "" ? <pre className="json_rpc_terminal">{value}</pre> : null}
      </div>
      <div>
        {value == "" ? (
          <button
            className="json_rpc_terminal_button"
            onClick={() => {
              fetch(network, {
                method: "POST",
                headers: {
                  Accept: "application/json",
                  "Content-Type": "application/json",
                },
                body: JSON.stringify({
                  jsonrpc: "2.0",
                  method: method,
                  params: params,
                  id: 1,
                }),
              })
                .then((res) => res.json())
                .then((response) => {
                  setValue(JSON.stringify(response));
                });
            }}
          >
            Run command
          </button>
        ) : (
          <button
            className="json_rpc_terminal_button"
            onClick={() => {
              setValue("");
            }}
          >
            Clear Terminal
          </button>
        )}
      </div>
    </div>
  );
};

## txpool_content

Returns a list with the exact details of all the transactions currently pending for inclusion in the next block(s), as well as the ones that are being scheduled for future execution only.

---

<h4><i>Parameters:</i></h4>

None


<h4><i>Example:</i></h4>

Run the command and see live results from our testnet.


````bash
curl  https://rpc.poa.psdk.io:8545 -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"txpool_content","params":[],"id":1}'
````

<JsonRpcTerminal method="txpool_content" params={[]} network="https://rpc.poa.psdk.io:8545"/>

## txpool_inspect

Returns a list with a textual summary of all the transactions currently pending for inclusion in the next block(s), as well as the ones that are being scheduled for future execution only. This is a method specifically tailored to developers to quickly see the transactions in the pool and find any potential issues.

---

<h4><i>Parameters:</i></h4>

None


<h4><i>Example:</i></h4>

Run the command and see live results from our testnet.


````bash
curl  https://rpc.poa.psdk.io:8545 -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"txpool_inspect","params":[],"id":1}'
````

<JsonRpcTerminal method="txpool_inspect" params={[]} network="https://rpc.poa.psdk.io:8545"/>

## txpool_status

Returns the number of transactions currently pending for inclusion in the next block(s), as well as the ones that are being scheduled for future execution only.

---

<h4><i>Parameters:</i></h4>

None

<h4><i>Example:</i></h4>

Run the command and see live results from our testnet.


````bash
curl  https://rpc.poa.psdk.io:8545 -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"txpool_status","params":[],"id":1}'
````

<JsonRpcTerminal method="txpool_status" params={[]} network="https://rpc.poa.psdk.io:8545"/>
