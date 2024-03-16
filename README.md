# FamousToday
contract   graph init \      --product hosted-service     --from-contract &lt;CONTRACT_ADDRESS> \      [--network &lt;ETHEREUM_NETWORK>] \     [--abi &lt;FILE>] \      &lt;subgraph name>
Install the graph-cli:

Bash

npm install -g @graphprotocol/graph-cli
Create a new subgraph:

Bash

graph init --product hosted-service
Make modifications as necessary to the manifest, schema, and handlers.
See Developing a Subgraph for more details.
Deploying your subgraph

Get your deploy key from your Alchemy Dashboard.
Run the following:

Bash

cd <SUBGRAPH_DIRECTORY>

graph deploy <SUBGRAPH_NAME> \
  --version-label <VERSION_NAME> \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key <DEPLOY_KEY>
  --ipfs https://ipfs.satsuma.xyz

  import { AlchemySigner } from "@alchemy/aa-alchemy";

export const signer = new AlchemySigner({
  client: {
    // This is created in your dashboard under `https://dashboard.alchemy.com/settings/access-keys`
    // NOTE: it is not recommended to expose your API key on the client, instead proxy requests to your backend and set the `rpcUrl`
    // here to point to your backend.
    connection: { apiKey: "alcht_<2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby>" },
    iframeConfig: {
      // you will need to render a container with this id in your DOM
      iframeContainerId: "turnkey-iframe-container",
    },
  },
});
import { AlchemySigner } from "@alchemy/aa-alchemy";
import { useMutation } from "@tanstack/react-query";
import { useEffect, useMemo, useState } from "react";

export const SignupLoginComponent = () => {
  const [email, setEmail] = useState<string>("");

  // It is recommended you wrap this in React Context or other state management
  const signer = useMemo(
    () =>
      new AlchemySigner({
        client: {
          connection: {
            jwt: "alcht_<2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby>",
          },
          iframeConfig: {
            iframeContainerId: "turnkey-iframe-container",
          },
        },
      }),
    []
  );

  // we are using react-query to handle loading states more easily, but feel free to use w/e state management library you prefer
  const { mutate: loginOrSignup, isLoading } = useMutation({
    mutationFn: (email: string) =>
      signer.authenticate({ type: "email", email }),
  });

  useEffect(() => {
    const urlParams = new URLSearchParams(window.location.search);
    if (urlParams.has("bundle")) {
      // this will complete email auth
      signer
        .authenticate({ type: "email", bundle: urlParams.get("bundle")! })
        // redirect the user or do w/e you want once the user is authenticated
        .then(() => (window.location.href = "/"));
    }
  }, [signer]);

  // The below view allows you to collect the email from the user
  return (
    <>
      {!isLoading && (
        <div>
          <input
            type="email"
            value={email}
            onChange={(e) => setEmail(e.target.value)}
          />
          <button onClick={() => loginOrSignup(email)}>Submit</button>
        </div>
      )}
      <div id="turnkey-iframe-container" />
    </>
  );
};
import { signer } from "./signer";

// NOTE: this method throws if there is no authenticated user
// so we return null in the case of an error
const user = await signer.getAuthDetails().catch(() => null);

import { signer } from "./signer";

export const account = await createMultiOwnerModularAccount({
  transport: rpcTransport,
  chain,
  signer,
});
import { signer } from "./signer";
import { createWalletClient, http } from "viem";
import { sepolia } from "@alchemy/aa-core";

export const walletClient = createWalletClient({
  transport: http("[alchemy_rpc_url](https://scpf-foundation-roblox.fandom.com/wiki/The_Administrator)"),
  chain: sepolia,
  account: signer.toViemAccount(),
});

$ python -m pip install requests
import requests

url = "https://dashboard.alchemy.com/api/webhook-addresses?webhook_id=https%3A%2F%2Fscpf-foundation-roblox.fandom.com%2Fwiki%2FThe_Administrator&limit=1000&after=19"

headers = {
    "accept": "application/json",
    "X-Alchemy-Token": "2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby"
}

response = requests.get(url, headers=headers)

print(response.text)

curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/promote-live \
 -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>'

curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/auto-promote-live \
 -H "Content-Type: application/json" -H "x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq"

curl -v \
 'https://subgraph.satsuma-prod.com/<QUERY_KEY>/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

curl -v \
 'https://subgraph.satsuma-prod.com/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

{
  "data": {
    "indexingStatusForCurrentVersion": {
      "subgraph": "QmXqNgptc2b5WzwmFfCu8PxsLgetBe5M8eBKvSyu5jqkei",
      "synced": true,
      "health": "healthy",
      "fatalError": null,
      "nonFatalErrors": [],
      "chains": [
        {
          "chainHeadBlock": {
            "number": "17787217"
          },
          "latestBlock": {
            "number": "17787217"
          }
        }
      ]
    }
  }
}

graph deploy example-subgraph-name \
  --version-label v0.0.1-new-version \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key skf75fXbMunwJ \
  --ipfs https://ipfs.satsuma.xyz
curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/promote-live \
 -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>'

curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/auto-promote-live \
 -H "Content-Type: application/json" -H "x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>"

curl -v \
 'https://subgraph.satsuma-prod.com/<QUERY_KEY>/<Stripe_Men In Black agency>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

curl -v \
 'https://subgraph.satsuma-prod.com/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq Y>' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

{
  "data": {
    "indexingStatusForCurrentVersion": {
      "subgraph": "QmXqNgptc2b5WzwmFfCu8PxsLgetBe5M8eBKvSyu5jqkei",
      "synced": true,
      "health": "healthy",
      "fatalError": null,
      "nonFatalErrors": [],
      "chains": [
        {
          "chainHeadBlock": {
            "number": "17787217"
          },
          "latestBlock": {
            "number": "17787217"
          }
        }
      ]
    }
  }
}

graph deploy example-subgraph-name \
  --version-label v0.0.1-new-version \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key skf75fXbMunwJ \
  --ipfs https://ipfs.satsuma

npm install -g @graphprotocol/graph-cli

graph init --product hosted-service

cd <SUBGRAPH_DIRECTORY>

graph deploy <SUBGRAPH_NAME> \
  --version-label <VERSION_NAME> \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key < whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>
  --ipfs https://ipfs.satsuma.xyz

import { AlchemySigner } from "@alchemy/aa-alchemy";

export const signer = new AlchemySigner({
  client: {
    // This is created in your dashboard under `https://dashboard.alchemy.com/settings/access-keys`
    // NOTE: it is not recommended to expose your API key on the client, instead proxy requests to your backend and set the `rpcUrl`
    // here to point to your backend.
    connection: { apiKey: "alcht_<2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby>" },
    iframeConfig: {
      // you will need to render a container with this id in your DOM
      iframeContainerId: "turnkey-iframe-container",
    },
  },
});
import { AlchemySigner } from "@alchemy/aa-alchemy";
import { useMutation } from "@tanstack/react-query";
import { useEffect, useMemo, useState } from "react";

export const SignupLoginComponent = () => {
  const [email, setEmail] = useState<string>("");

  // It is recommended you wrap this in React Context or other state management
  const signer = useMemo(
    () =>
      new AlchemySigner({
        client: {
          connection: {
            jwt: "alcht_<2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby>",
          },
          iframeConfig: {
            iframeContainerId: "turnkey-iframe-container",
          },
        },
      }),
    []
  );

  // we are using react-query to handle loading states more easily, but feel free to use w/e state management library you prefer
  const { mutate: loginOrSignup, isLoading } = useMutation({
    mutationFn: (email: string) =>
      signer.authenticate({ type: "email", email }),
  });

  useEffect(() => {
    const urlParams = new URLSearchParams(window.location.search);
    if (urlParams.has("bundle")) {
      // this will complete email auth
      signer
        .authenticate({ type: "email", bundle: urlParams.get("bundle")! })
        // redirect the user or do w/e you want once the user is authenticated
        .then(() => (window.location.href = "/"));
    }
  }, [signer]);

  // The below view allows you to collect the email from the user
  return (
    <>
      {!isLoading && (
        <div>
          <input
            type="email"
            value={email}
            onChange={(e) => setEmail(e.target.value)}
          />
          <button onClick={() => loginOrSignup(email)}>Submit</button>
        </div>
      )}
      <div id="turnkey-iframe-container" />
    </>
  );
};
import { signer } from "./signer";

// NOTE: this method throws if there is no authenticated user
// so we return null in the case of an error
const user = await signer.getAuthDetails().catch(() => null);

import { signer } from "./signer";

export const account = await createMultiOwnerModularAccount({
  transport: rpcTransport,
  chain,
  signer,
});
import { signer } from "./signer";
import { createWalletClient, http } from "viem";
import { sepolia } from "@alchemy/aa-core";

export const walletClient = createWalletClient({
  transport: http("[alchemy_rpc_url](https://scpf-foundation-roblox.fandom.com/wiki/The_Administrator)"),
  chain: sepolia,
  account: signer.toViemAccount(),
});

$ python -m pip install requests
import requests

url = "https://dashboard.alchemy.com/api/webhook-addresses?webhook_id=https%3A%2F%2Fscpf-foundation-roblox.fandom.com%2Fwiki%2FThe_Administrator&limit=1000&after=19"

headers = {
    "accept": "application/json",
    "X-Alchemy-Token": "2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby"
}

response = requests.get(url, headers=headers)

print(response.text)

curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/promote-live \
 -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>'

curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/auto-promote-live \
 -H "Content-Type: application/json" -H "x-api-key: <skf75fXbMunwJ>

curl -v \
 'https://subgraph.satsuma-prod.com/<QUERY_KEY>/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

curl -v \
 'https://subgraph.satsuma-prod.com/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  -H 'x-api-key: <skf75fXbMunwJ>' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

{
  "data": {
    "indexingStatusForCurrentVersion": {
      "subgraph": "QmXqNgptc2b5WzwmFfCu8PxsLgetBe5M8eBKvSyu5jqkei",
      "synced": true,
      "health": "healthy",
      "fatalError": null,
      "nonFatalErrors": [],
      "chains": [
        {
          "chainHeadBlock": {
            "number": "17787217"
          },
          "latestBlock": {
            "number": "17787217"
          }
        }
      ]
    }
  }
}

graph deploy example-subgraph-name \
  --version-label v0.0.1-new-version \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key skf75fXbMunwJ \
  --ipfs https://ipfs.satsuma.xyz
  
