## The Verifiable Trust Agent

{{This section is normative.}}

This chapter specifies what a VTA MUST do beyond the common model: it is the
key, credential and authority root for its principal.

### Custody

{{The key hierarchy a VTA holds, and the boundary it maintains around it.}}

### The credential store and holder surface

{{Receiving, holding, presenting and removing credentials on behalf of the
principal; the distinction between receiving a credential and being able to
remove one.}}

### Context and key authority

{{The VTA is the authority for the context tree and for key derivation within
it. This section states the operations that follow from that and the ones no
other node may perform.}}

### Provisioning integrations

{{Standing up mediators, hosts and agent identities from the VTA; sealed
transfer of a provisioned identity, such that the party relaying the material
cannot read it; and the out-of-band verification that accompanies it.}}

### Delegation to software agents

{{Provisioning an agent its own identity, its own context and a narrowed
capability set; the wake and heartbeat surface; and the kill switch, including
what a revocation MUST reach.}}

### Backup, restore and succession

{{What is backed up, what a restore re-establishes, and what succession means
for an identity whose principal is no longer able to operate it.}}

### The operator surface

{{What a human operator MUST be able to observe and stop. An action that cannot
be observed cannot be governed.}}
