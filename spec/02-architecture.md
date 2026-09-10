## Architecture and Conformance Targets

This section is informative.

This chapter describes the shape of a [[ref: VTI]] and names
the node types that the normative chapters bind requirements to. It defines no
requirements of its own: it fixes the vocabulary those requirements are written
in.

### The problem this specification addresses

A DTG component specification can be conformed to on its own. An identifier
method, a credential data model, a task definition, a transport — each is
implementable and testable in isolation, and that isolation is what allows them
to evolve independently.

An operating trust system is not any of those things. It is a set of running
nodes that hold keys on behalf of people and organisations, grant and revoke
authority over parts of what they hold, admit and remove members, delegate to
software agents, and act on evidence produced somewhere else by someone else.
Several properties that a relying party depends on are properties of that
running arrangement rather than of any component within it. A credential can be
cryptographically impeccable and issued by a party that no longer holds the
authority it asserts. A task can complete exactly as specified while the
outcome it was invoked to achieve remains unestablished. Two components, each
privacy-preserving, can compose into an interaction that is not.

Those properties need an owner. This specification is that owner, and the
architecture below exists to give the ownership somewhere to attach.

### Conformance targets

Every normative requirement in this specification binds to at least one of the
following targets. The Conformance chapter states which profiles each target
implements.

| Target | Role |
|---|---|
| **Verifiable Trust Agent (VTA)** | The key, credential and authority root for a principal — a person, an organisation, or a software agent. Owns the trust context tree and key derivation within it. |
| **Verifiable Trust Community (VTC)** | A community authority: admission, membership state, recognition and publication. Provisioned on top of an existing VTA. |
| **Client** | Any consumer acting under authority granted by a VTA or VTC: an interactive operator client, a headless application, an AI-agent runtime, or a mobile authorizer. |
| **Mediator** | A store-and-forward transport node. Observes routing metadata; does not observe content. |
| **Host service** | An identifier-log host, a witness, a push gateway, or a room host — infrastructure a node depends on but does not embody. |
| **Trust registry** | Authority lookup for issuers and communities. |

#### Verifiable Trust Agent

A VTA is the root of everything else. It holds the key hierarchy for its
principal, organises what it holds into trust contexts, decides who may act in
which of them, holds and presents credentials on the principal's behalf, and
delegates narrowed authority to the clients and software agents that act for
the principal. A principal with no VTA has no way to be represented in a VTI
deployment; a VTA with no principal is not a thing this specification
describes.

#### Verifiable Trust Community

A [[ref: VTC]] is a community authority rather than a personal one. It decides who is a
member, what membership means over time, and which other communities it
recognises. It is provisioned on top of an existing VTA and derives its
identity from it, which makes the dependency one-way: a VTA operates with no
VTC anywhere in sight, and a VTC without its underlying VTA has no identity to
act with.

The division of authority between the two is a recurring source of confusion,
so this specification states it rather than leaving it to be inferred: the VTA
is the key and context authority, and the VTC is the membership authority. A
VTC references context identifiers as membership metadata; it does not create
contexts and does not derive keys.

#### Client

A client is anything acting under granted authority rather than under its own.
The category deliberately spans an operator typing at a terminal, a headless
process running unattended, an AI-agent runtime acting between human
interactions, and a phone used to approve something. What varies across them is
who holds the private key, whether a human is present at the moment of action,
and whether the thing can be woken. What does not vary is that a client's
authority is an artefact held by the node that granted it, revocable at that
node, and never wider than what that artefact says.

#### Mediator, host, registry

These are infrastructure a deployment depends on. They are named as conformance
targets because requirements attach to them — a mediator observes routing
metadata and therefore has privacy obligations; a host holds an identifier log
and therefore has integrity and availability obligations; a registry answers
authority questions and therefore has currency obligations — and because a
deployment's properties depend on their behaviour even though they are
typically operated by someone else.

### The trust boundary

The trust boundary in a VTI deployment is drawn **per operator, not per
service**.

Two services run by the same operator sit inside one boundary: compromise of
one is, for threat-modelling purposes, compromise of both, and a security
argument that depends on them being separate is not sound. Two instances of the
same software run by different operators sit in different boundaries even
though they share every line of code.

This has a consequence that runs through the whole specification. A property
enforced by a node about itself is worth less than a property a relying party
can check, because the first is inside a boundary the relying party does not
control. Where this specification can require the checkable form, it does.

### Layering

```
                 governance frameworks
                          |
    +---------------------+---------------------+
    |     this specification (VTI)              |
    |  contexts and authority | nodes | composition |
    +---------------------+---------------------+
                          |
   identifier methods · credential data models · task
   definitions · transports · registries · proof mechanisms
```

Components below define what an artefact is and what a message means.
Governance frameworks above define what a particular ecosystem expects of its
participants. This specification occupies the layer between: it defines the
infrastructure the components run inside, and it is where a requirement lands
when it belongs to the composition of components rather than to any one of
them.

The layering also settles where a requirement is edited when it turns out to be
wrong. A requirement about what a credential contains belongs to the credential
specification even when it was discovered here. A requirement about what must
remain true when that credential is presented over a transport, by a delegate,
in a context whose authority changed since issuance, belongs here.

### The common model

Every node type shares one model, defined in Part B:

- **trust contexts** — the isolation boundary that keys, credentials, data,
  delegations and grants are organised into, and the unit in which authority is
  granted;
- **the authority model** built over contexts — access control entries, roles,
  capabilities, the separation between acting and approving, and the approval
  and step-up requirements that sit in front of sensitive operations;
- **identity and keys** — the identifier methods each node uses and the
  cryptography behind them;
- **transports and delivery** — how nodes reach each other, and what a sender
  is entitled to conclude when a send returns;
- **sessions** — how a client's authority is established for a period of use;
- **the operation surface** — the task catalogue that *is* the API, and the
  transport bindings of it;
- **credentials and presentations** — what a node issues, holds and presents,
  and what verifying one of them establishes.

A node type's own chapter in Part C says what it adds to this model, not what
it replaces in it.

### A request, end to end

The following walkthrough orients the chapters that follow. It describes a
single ordinary operation — an AI-agent runtime asking its principal's VTA to
sign something on the principal's behalf — and names the chapter that governs
each step.

1. **The agent holds granted authority.** Some time earlier it minted its own
   identifier, an administrator recorded an access control entry naming a role,
   a context and a narrowed capability set, and the agent rotated onto a
   long-term identifier. *Client onboarding and lifecycle.*
2. **The agent opens a session.** It answers a challenge from the VTA, which
   resolves its authority from the entry at that moment rather than from
   anything cached. *Sessions and authentication.*
3. **The agent sends the operation.** It uses the canonical task for signing if
   one exists, over whichever transport both ends support, and the request is
   authenticated by the transport. *Operation surface; transports.*
4. **The VTA locates the authority.** It resolves the entry again, computes the
   effective capability set from the role's ceiling and the entry's own
   narrowing, and checks that the target key is in a context the entry's scope
   covers. *Trust contexts and the authority model.*
5. **The VTA evaluates approval rules.** If a rule names this operation, the
   VTA requires either re-authentication of the caller or consent from a
   different party, and does so before the operation's handler runs. *Trust
   contexts and the authority model.*
6. **The operation runs, or is refused.** Either outcome is audited, and a
   refusal names the rule that produced it so the agent can distinguish "not
   permitted" from "not yet approved" from "failed". *Trust contexts; operation
   surface.*
7. **The reply reaches the agent, or does not.** The agent concludes delivery
   only on the evidence the transport chapter says counts as delivery, and
   retries under the idempotency contract. *Transports, messaging and
   delivery.*
8. **A relying party later evaluates the result.** What it may conclude from a
   valid signature, a valid credential and a registry entry — and what it may
   not — is governed by the composition requirements. *Composition
   requirements.*

Every step above is where at least one real system has gone wrong, which is why
each one has a chapter.

### How requirements are stated

Normative statements carry stable identifiers of the form `VTI-<AREA>-<nnn>`.
Identifiers are permanent: a withdrawn requirement is marked withdrawn rather
than renumbered, and an identifier is never reused for different text. Areas in
use are listed in the Conformance chapter.

Paragraphs marked *Rationale* record why a requirement is written the way it is.
They carry no normative force, and they are kept in the document because the
common failure mode for the requirements in this specification is a reader
reasonably concluding that the obvious simpler formulation would do.
