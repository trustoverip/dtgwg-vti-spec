## Sessions and Authentication

{{This section is normative.}}

This chapter specifies authentication once, for every transport: challenge,
authenticate, refresh.

### The challenge

{{Issuance, entropy, time-to-live, and per-subject rate limiting.}}

### Authentication

{{The signer MUST be the subject that was challenged — a challenge that leaks
is not thereby a credential. Freshness windows and replay resistance.}}

### Authority resolution

{{Authority MUST be resolved from the access control entry at authentication
**and again** at every refresh, so that a revocation occurring between issuance
and use takes effect.}}

### Refresh

{{The refresh claim MUST be atomic: exactly one caller succeeds for a given
token. Assurance level MUST be preserved across rotation — a session that was
stepped up MUST NOT silently return to a lower level. Token lifetimes as a
function of assurance level.}}

### Session state and audit

{{The session state machine, and the audit events each transition produces.}}
