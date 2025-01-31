---
title: Collator
weight: 9002
draft: false
menu:
  docs:
    parent: "maintain"
---

{{< tip "warning" >}}
Collators on Phala (Khala) are supposed to be Gatekeepers, who also run the `pRuntime` as the Gatekeeper role, helping manage the keys in the network. However, Gatekeeper is not open for election now.
{{< /tip >}}

## Concepts

Phala Network is currently running its collators on Aura consensus. Aura is a simple block authoring engine that rotates the block producers among the registered collator candidates one by one.

```
            Host              Session
            Api               Manager
 Collator <===== [Session]  <--------- [CollatorSelection]
   node            pallet                     pallet
```

On the blockchain, the block authoring is handled collaboratively by `Session` and `CollatorSelection` pallets. The `Session` pallet controls how the blocks are produced and validated. Therefore it has a list of the selected block authors. However, the `Session` doesn't decide the list on its own. Instead, it delegates that to the `CollatorSelection` pallet.

> The `CollatorSelection` pallet implements the `SessionManager` trait and is injected as the manager of the `Session` pallet in the runtime.

The `CollatorSelection` pallet is a minimal PoS system. It has a configurable bound amount for all the collator candidates. Anyone can run a collator as long as they meet the minimal bound. It also kicks the unresponsive collator out after each session. Besides the public collators, it also holds a special group of collators called `invulnerables`, who are not vulnerable to kicks even if they are offline.

## Prepare a new collator

1. Create an account with a minimum amount of bound available (16 PHA by default)
2. Deploy a collator full node with the `--collator` flag enabled
3. Generate a session key. Two options:
   1. Call RPC `api.rpc.author.rotateKeys()` to generate a new session key in the node, and note the public key it outputs
   2. Generate the session key externally (dangerous), calculate the public key, and note it
4. Set the session (public) key of the collator account by calling `session.setKeys()`
5. Start the collator node

## Enable an invulnerable collator

Call `collatorSelection.setInvulnerables(list)`, where `list` is the full list of all the invulnerables collators including the old ones. The current invulnerable collator list can be found from `collatorSelection.invulnerables()`.

This operation can only be done with the Sudo permission.

## Apply for a public collator

(Not officially supported yet.)
