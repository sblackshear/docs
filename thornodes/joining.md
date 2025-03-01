---
description: How to join THORChain as an Node.
---

# Joining

## **Joining THORChain**

Now that you have a THORNode deployed in your Kubernetes cluster, you need to start operating your node to join the network.

There are a couple of steps to follow to do so.

### 1. Check your current node status

The first step would be to make sure your deployment was successful and your node is running correctly. \_\*\*\_To check the current status of your node, you can run the command status from the `node-launcher`repository on your terminal:

```text
make status
```

You will get an output along those lines, the example below is for a testnet node:

```text
 ________ ______  ___  _  __        __
/_  __/ // / __ \/ _ \/ |/ /__  ___/ /__
 / / / _  / /_/ / , _/    / _ \/ _  / -_)
/_/ /_//_/\____/_/|_/_/|_/\___/\_,_/\__/
ADDRESS     thor13hh6qyj0xgw0gv7qpay8thfucxw8hqkved9vr2
IP
VERSION     0.0.0
STATUS      Unknown
BOND        0.00
REWARDS     0.00
SLASH       0
PREFLIGHT   { "status": "Standby", "reason": "node account has invalid registered IP address", "code": 1 }
API         http://:1317/thorchain/doc/
RPC         http://:27147
MIDGARD     http://:8080/v2/doc
CHAIN       SYNC       BLOCKS
THOR        55.250%    37,097/67,144
BNB         99.915%    155,426,471/155,559,013
BTC         2.399%     227,986/678,340
ETH         7.764%     947,182/12,199,994
LTC         0.012%     6,526/1,818,000
BCH         2.293%     197,340/682,404
```

Your node is running but as you can see in the \`Preflight\` section, your node is not yet ready to be churned in and currently is in standby status, since your node has no IP address setup yet.

But to be able to set up the node IP address, you first need to get it registered in the chain by sending your BOND.

{% hint style="warning" %}
Before sending the BOND, verify that your THORNode is fully synced with connected chains. Connected chains such as Bitcoin, may take a day to sync. If you join THORChain without fully syncing a connected chain, you will immediately get slashed for missing observations, and lose money.
{% endhint %}

### 2 - Send a small BOND \(recommend 100-1000\)

1\) You will do a "Deposit" transaction. There is no destination address -- use an appropriate wallet. The Bond is locked in a module controlled by the state machine. 

2\) Deposit your BOND using the memo `BOND:<thornode-address>` \(or use an appropriate GUI that does this memo for you\). Start small, the bond will be picked up. 

![Bonding using BOND option in ASGARDEX](../.gitbook/assets/image%20%288%29.png)

Give the network 3-5 seconds to pick up your bond. To verify it has received your bond, run the following:

```text
curl http://thornode.thorchain.info/thorchain/node/<node-address>
```

If you run `make status` again, you should see this:

```text
 ________ ______  ___  _  __        __
/_  __/ // / __ \/ _ \/ |/ /__  ___/ /__
 / / / _  / /_/ / , _/    / _ \/ _  / -_)
/_/ /_//_/\____/_/|_/_/|_/\___/\_,_/\__/
ADDRESS     thor13hh6qyj0xgw0gv7qpay8thfucxw8hqkved9vr2
IP
VERSION     0.0.0
STATUS      Whitelisted
BOND        1000.00
REWARDS     0.00
SLASH       0
PREFLIGHT   { "status": "Standby", "reason": "node account has invalid registered IP address", "code": 1 }
API         http://:1317/thorchain/doc/
RPC         http://:27147
MIDGARD     http://:8080/v2/doc
CHAIN       SYNC       BLOCKS
THOR        55.250%    37,097/67,144
BNB         99.915%    155,426,471/155,559,013
BTC         2.399%     227,986/678,340
ETH         7.764%     947,182/12,199,994
LTC         0.012%     6,526/1,818,000
BCH         2.293%     197,340/682,404
```

As you can see, it is in standby but does not have an IP registered yet. This is needed for peer discovery.

### 3 - Setup Node IP Address

You must tell THORChain your IP-Address for its address book and seed-service to run properly:

```text
make set-ip-address
```

If you run the status command again, you should now see a different message for the Preflight section saying you need to set your node keys.

{% hint style="info" %}
Once your IP address has been registered for discovery, you can use your own host for queries.
{% endhint %}

### 4 - Setup Node keys

Tell THORChain about your public keys for signing sessions:

```text
make set-node-keys
```

If you run the status command again, you should now see that your node is in status “ready” and is now ready to be churned in the next rotation. 

### 5 - Set Version

Make sure your node broadcasts its latest version, else you won't churn in since THORChain enforces a version requirement. This version will appear in your `make status`. If you are on `0.0.0` then you haven't set your version:

```text
make set-version
```

### 6 - Send Final Bond

If you followed steps 1-5 above, your preflight will be saying:

```text
PREFLIGHT   { "status": "Standby", "reason": "node account does not have minimum bond requirement: 1000000000/100000000000000, "code": 1 }
```

To address this, send the remaining bond, that is higher than the minimum bond. You can find that quantity on [https://thornode.thorchain.info/thorchain/constants](https://thornode.thorchain.info/thorchain/constants) and look for `MinimumBondInRune` If you finally run `make status` you should see this, with keyword **"Ready":**

```text
 ________ ______  ___  _  __        __
/_  __/ // / __ \/ _ \/ |/ /__  ___/ /__
 / / / _  / /_/ / , _/    / _ \/ _  / -_)
/_/ /_//_/\____/_/|_/_/|_/\___/\_,_/\__/
ADDRESS     thor13hh6qyj0xgw0gv7qpay8thfucxw8hqkved9vr2
IP          1.2.3.4
VERSION     0.38.0
STATUS      Standby
BOND        1,000,001.00
REWARDS     0.00
SLASH       0
PREFLIGHT   { "status": "Ready", "reason": "OK", "code": 0 }
API         http://1.2.3.4:1317/thorchain/doc/
RPC         http://1.2.3.4:27147
MIDGARD     http://1.2.3.4:8080/v2/doc
CHAIN       SYNC       BLOCKS
THOR        100.000%   67,144/67,144
BNB         100.000%   155,559,013/155,559,013
BTC         100.000%   678,340/678,340
ETH         100.000%   12,199,994/12,199,994
LTC         100.000%   1,818,000/1,818,000
BCH         100.000%   682,404/682,404
```

## Bonding The Right Amount

Although your node is ready to be churned in, it doesn’t mean it will be the next one to be selected since someone else could have posted a higher bond than you. To maximise chances of a quick entry, monitor Midgard to see what everyone else is bonding and try to outbid them. Keep an eye on `maximumStandbyBond` and make sure you are bonding that amount.

```text
curl http://52.221.153.64:8080/v1/network | json_pp

resp:
 "bondMetrics" : {
      "minimumActiveBond" : "10001000000000",
      "medianStandbyBond" : "1010000000000",
      "medianActiveBond" : "15001000000000",
      "averageStandbyBond" : "1010000000000",
      "maximumActiveBond" : "15001000000000",
      "averageActiveBond" : "12006800000000",
      "maximumStandbyBond" : "1010000000000",
      "totalStandbyBond" : "1010000000000",
      "totalActiveBond" : "60034000000000",
      "minimumStandbyBond" : "1010000000000"
   }
```

The endpoint will show data on average, median, total, minimum and maximum bond amounts. For fastest entry, bond higher than the current maximum.

{% hint style="info" %}
RUNE is always displayed in 1e8 format, 100000000 = 1 RUNE
{% endhint %}

### Bonding More

At any time during standby, you can bond more by making an additional BOND transaction with memo:

`BOND:<thornode-address>`

You can also [remove some of your bond](https://docs.thorchain.org/thornodes/leaving) whilst you are on standby, using the UNBOND memo.

