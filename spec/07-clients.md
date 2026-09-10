## Client Onboarding and Lifecycle

{{This section is normative.}}

This chapter specifies how a client obtains standing against a VTA, and how it
holds that standing over time. The onboarding sequence has four steps and the
ordering carries the security properties, so the chapter is written in that
order.

### Client classes

{{Interactive operator client, headless application, AI-agent runtime, mobile
authorizer. What differs between them — who holds the private key, whether a
human is present at the moment of action, whether the client can be woken — and
what does not differ.}}

### Step 1 — mint an ephemeral identifier client-side

{{The client generates its own key pair from a cryptographically secure random
source. The private key MUST NOT leave the client. No VTA participation is
required and no VTA need be reachable.

The resulting identifier is a **bootstrap identity**, not the client's
long-term identity, and the client MUST record it as such in local state.

Rationale to preserve: because the VTA never holds a client's private key, a
compromised VTA cannot impersonate its own clients; and because the client
generates it locally, enrolment requires neither a pre-shared secret nor a
pre-existing secure channel.}}

### Step 2 — enrol the identifier in the access control list

{{The bootstrap identifier is conveyed out-of-band to an administrator — a chat
message, an email, a scanned code — who creates the access control entry: role,
contexts, capabilities, expiry.

Requirements follow from the nature of that channel:

- the bootstrap identifier MUST be treated as low-trust, because it has crossed
  a channel outside the trust system;
- its entry SHOULD be short-lived, and MUST be single-purpose;
- the grant MUST be least-privilege at creation — a context per purpose, and
  capabilities narrowed within the role. Enrolment is where scope is decided;
  widening it later is a separate, audited act;
- the enrolling administrator MUST be authorized to act in the target context.
  A context administrator may enrol into its own subtree and nowhere else.}}

### Step 3 — connect, then roll the key

{{On its first successful authentication, a client SHOULD rotate off the
bootstrap identifier onto a freshly minted one and cause the original to be
dropped.

The strength of that recommendation tracks the exposure of the bootstrap
identifier. A deployment that delivered the identity inside a sealed
provisioning bundle has a defensible reason not to roll; a deployment whose
identifier was pasted into a chat window does not.

Regardless of whether a given deployment rolls, the following are REQUIRED:

- a conforming VTA MUST implement the key-swap operation, so that the choice
  belongs to the deployment rather than to the implementation;
- a bootstrap entry that is not rolled MUST carry an expiry;
- a roll that does occur MUST follow the sequence below exactly.}}

#### Mint the replacement

{{The client generates the replacement key pair locally, as in Step 1.}}

#### Prove reachability before committing

{{Before anything is committed, the new identifier MUST establish that it can
be reached over the transport the client will reconnect on, under a bounded
timeout.

This is not an authorization check — before the swap the new identifier has no
access control entry and cannot authenticate at all. It exists because a
rotation that commits to an unreachable identifier is **unrecoverable**: the
bootstrap entry is already gone. A failure at this point costs nothing and the
operation is retried; a failure after the commit costs the credential.}}

#### Swap atomically

{{A single operation moves the existing entry onto the new subject. The request
carries a short-lived, audience-bound proof of control of the new identifier,
and the service MUST verify three bindings:

- the authenticated caller is the declared current subject;
- the declared new subject is the holder proven by the accompanying proof;
- the entry's role, contexts, capabilities and approve scope are preserved
  exactly.

A swap is a rotation, never an escalation. An operation that would change the
reach of the entry MUST be refused.}}

#### Commit, then persist, before anything else

{{Between the swap and the client persisting its new key material, the previous
identifier no longer has standing while the new private key exists only in
volatile memory. No optional work — reporting, discovery, registration — may be
performed in that window, because anything that can block there can leave an
operator with no usable credential.}}

### Step 4 — steady-state use

{{Session refresh and token handling; operating within granted capabilities;
responding to a step-up demand, including what the client presents to a human;
interpreting a refusal — a refusal names the rule that produced it, so a client
can distinguish *not permitted* from *not yet approved* from *failed*;
idempotency on retry; tolerance for clock skew.}}

### Lifecycle

{{Voluntary re-roll; expiry and renewal; a revocation observed mid-session;
offboarding. Recovery of a lost client key is out of scope by design — the
client re-enrols, which is what makes per-client revocation meaningful.

A client identity belongs to one client **instance**. Sharing an identifier
across installations defeats per-client revocation and creates a durable
cross-device correlator, and MUST NOT be done.}}

### Client conformance checklist

{{The requirement identifiers from this chapter, in onboarding order, as an
implementer's checklist. Sequence diagrams, a worked example and test vectors
appear in Appendix A.}}
