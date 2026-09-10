## Sessions and Authentication

This section is normative.

This chapter specifies authentication once, for every transport: challenge,
authenticate, refresh. Each is an operation of the `auth` family in the
required catalogue (VTI-OPS-080), and every requirement here binds that
operation on every binding of it. A session is a period during which a node accepts a
subject's requests without repeating the proof of control that opened it, and
every requirement here exists to bound what that period can be relied on to
mean.

### The challenge

**VTI-SES-001** — A challenge MUST be generated from a cryptographically secure
random source and MUST carry at least 128 bits of entropy.

**VTI-SES-002** — A challenge MUST be bound to the subject it was issued for.

**VTI-SES-003** — A challenge MUST expire, and a node MUST enforce the
expiry.

**VTI-SES-004** — A challenge MUST be redeemable at most once.

**VTI-SES-005** — A node MUST limit the number of outstanding challenges per
subject.

**VTI-SES-006** — A node MUST refuse to issue a challenge to a subject with no
effective access control entry.

**VTI-SES-007** — The refusal required by VTI-SES-006 MUST NOT be
distinguishable, by an unauthenticated caller, from the other refusals a
challenge request can produce. A node MUST NOT disclose whether an entry exists
for a subject to a caller that has not authenticated as that subject.

*Rationale for VTI-SES-007.* A challenge endpoint that answers differently for
a known and an unknown subject is an enumeration oracle: an attacker who can
guess identifiers learns which ones a node holds authority for, which is the
first step in targeting the humans behind them. The refusal is uniform, and the
client's own signal — that it has repeatedly failed to obtain standing — is
what reaches its operator (VTI-CLT-016), because a client knows it enrolled and
an attacker probing identifiers does not.

### Authentication

**VTI-SES-010** — The signer of an authentication response MUST be the subject
the challenge was issued to. A node MUST refuse a response signed by any other
subject.

**VTI-SES-011** — A node MUST compare the presented challenge to the issued
challenge in constant time.

**VTI-SES-012** — Where the transport carries a creation time, a node MUST
enforce a freshness window on it.

**VTI-SES-013** — A node MUST record, in the session it establishes, the means
by which the subject authenticated and the assurance level reached.

*Rationale for VTI-SES-010.* Without this binding, a challenge that leaks
becomes a credential: any subject able to sign can redeem it. It is stated
separately from the challenge's binding to its subject (VTI-SES-002) because
the two are enforced at different moments, and an implementation that has done
the first still has to do the second.

### Resolving authority

**VTI-SES-020** — A node MUST resolve the subject's authority from its access
control entry at authentication, and again at every refresh.

**VTI-SES-021** — A node MUST NOT establish or continue a session for a subject
whose entry has been removed, has expired, or no longer confers any authority.

**VTI-SES-022** — A node MUST NOT rely on authority captured at authentication
when performing an operation later in the session, where this specification
requires the authority to be current.

*Rationale.* A session is a convenience over repeated proof of control, and it
is not a grant. Re-resolving at every refresh is what makes a revocation take
effect on the timescale of a refresh rather than on the timescale of whatever
the longest-lived session happens to be.

### Refresh

**VTI-SES-030** — Claiming a refresh credential MUST be atomic: for a given
credential, exactly one concurrent caller MUST succeed.

**VTI-SES-031** — A refresh MUST invalidate the session it replaces.

**VTI-SES-032** — The assurance level of a session MUST be preserved across
refresh. A session that was elevated MUST NOT silently return to a lower level.

**VTI-SES-033** — The lifetime of an access credential MUST be a function of the
assurance level of the session, and an elevated session's credential MUST NOT
be longer-lived than an unelevated one's.

**VTI-SES-034** — A node MUST enforce a maximum total session lifetime that
refresh cannot extend.

*Rationale for VTI-SES-030.* Without atomic claiming, a refresh credential
replayed concurrently across replicas yields two live sessions from one, and
the second is invisible to anything that assumes the first replaced it.

*Rationale for VTI-SES-032.* Silently dropping the assurance level at refresh
converts an elevation into something that expires without saying so: the caller
continues, the node continues, and the requirement that produced the elevation
is no longer being met by anything.

### Session lifecycle

**VTI-SES-040** — A node MUST be able to terminate a session, and MUST be able
to invalidate an issued access credential before its expiry.

**VTI-SES-041** — A node MUST audit the establishment, refresh, termination and
failed establishment of a session.

**VTI-SES-043** — A node MUST be able to enumerate the sessions active for a
subject, and MUST be able to terminate them collectively.

**VTI-SES-042** — Where the transport supports binding a session to a key held
by the client, a node SHOULD bind it, and MUST refuse a request under a bound
session that does not demonstrate that key.
