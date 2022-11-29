---
id: json-rpc-net
title: Net
description: "List of JSON RPC commands for Polygon Edge."
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

## net_version

Returns the current network id.

---

<h4><i>Parameters:</i></h4>

None

<h4><i>Returns:</i></h4>

* <b> String </b> - The current network id.

<h4><i>Example:</i></h4>

Run the command and see live results from our testnet.


````bash
curl  https://rpc.poa.psdk.io:8545 -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"net_version","params":[],"id":83}'
````

<JsonRpcTerminal method="net_version" params={[]} network="https://rpc.poa.psdk.io:8545"/>

## net_listening

Returns true if a client is actively listening for network connections.

---

<h4><i>Parameters:</i></h4>

None

<h4><i>Returns:</i></h4>

*  <b> Boolean </b> - true when listening, otherwise false.

<h4><i>Example:</i></h4>

Run the command and see live results from our testnet.


````bash
curl  https://rpc.poa.psdk.io:8545 -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"net_listening","params":[],"id":83}'
````

<JsonRpcTerminal method="net_listening" params={[]} network="https://rpc.poa.psdk.io:8545"/>

## net_peerCount

Returns number of peers currently connected to the client.

---

<h4><i>Parameters:</i></h4>

None

<h4><i>Returns:</i></h4>

*  <b> QUANTITY </b> - integer of the number of connected peers.

<h4><i>Example:</i></h4>

Run the command and see live results from our testnet.


````bash
curl  https://rpc.poa.psdk.io:8545 -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"net_peerCount","params":[],"id":1}'
````

<JsonRpcTerminal method="net_peerCount" params={[]} network="https://rpc.poa.psdk.io:8545"/>
