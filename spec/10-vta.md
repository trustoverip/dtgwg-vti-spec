## The Verifiable Trust Agent

This section is normative.

This chapter specifies what a VTA does beyond the common model. It is the key,
credential and authority root for its principal, and every requirement here
follows from something else in a deployment depending on that.

### Custody

**VTI-VTA-001** — A VTA MUST hold its root derivation material within a
protection boundary, and MUST NOT export it other than through the backup
mechanism specified in this chapter.

**VTI-VTA-002** — A VTA MUST perform key operations on behalf of an authorized
caller rather than exporting the key for the caller to use.

**VTI-VTA-003** — Where a VTA supports exporting derived key material, that
export MUST be gated by a capability distinct from the capability to use the key,
and MUST be audited.

*Rationale.* The distinction in VTI-VTA-002 is what makes revocation
meaningful. A caller that holds a key retains it after its authority is
withdrawn; a caller that can ask for an operation loses the ability at the
moment the entry changes.

### The signing oracle

A VTA signs on behalf of its principal at the request of a delegate. What it
agrees to sign is therefore an authorization decision, not a cryptographic one.

**VTI-VTA-004** — A VTA MUST NOT sign material whose structure it cannot parse
and constrain. A request to sign opaque octets MUST be refused.

**VTI-VTA-005** — Before signing, a VTA MUST verify that the material is
well-formed for its stated type, that its stated issuer is the principal, and
that its audience and subject are within what the requesting entry authorizes.

**VTI-VTA-006** — A VTA MUST record what it signed, for which caller, under
which authority.

**VTI-VTA-007** — A capability to request signing of a constrained document
type MUST be distinguishable from a capability to request signing generally,
and a node MUST NOT satisfy a request of the second kind under a grant of the
first.

*Rationale.* A signing oracle that will sign anything is a general-purpose
forgery service for its principal, reachable by whichever delegate holds the
capability. The constraint is what makes delegation to an agent survivable: a
compromised agent can produce documents of the shapes it was authorized to
produce, addressed to the parties it was authorized to address, and nothing
else. Blind signing removes every one of those bounds at once, and does so
invisibly, because the resulting signature is indistinguishable from an
intended one.

### Credentials held for the principal

**VTI-VTA-010** — Every credential a VTA holds MUST belong to a trust context.

**VTI-VTA-011** — The ability to receive a credential and the ability to remove
one MUST be separately gated.

**VTI-VTA-012** — A VTA MUST NOT present a credential on the principal's behalf
except under authority that covers both the credential's context and the act of
presenting.

*Rationale for VTI-VTA-011.* Receiving a credential adds to what a principal
holds; removing one destroys evidence the principal may need. A consumer that
should be able to do the first is not thereby a consumer that should be able to
do the second.

### Context and key authority

**VTI-VTA-020** — A VTA MUST be the only node that creates trust contexts and
derives keys for its principal, as required by VTI-ACL-090.

### Provisioning integrations

A VTA provisions the identities of the infrastructure its principal depends on
— mediators, hosts, agents — and hands each one its key material.

**VTI-VTA-030** — Private key material for a provisioned identity MUST be
delivered sealed to the holder, such that a party relaying it cannot read it.

**VTI-VTA-031** — A provisioning bundle MUST be accompanied by a verification
value transmitted over a channel distinct from the bundle, and the holder MUST
verify it before use.

**VTI-VTA-032** — Provisioning MUST NOT confer authority. Any authority the
provisioned identity is to hold MUST be recorded as an access control entry.

*Rationale for VTI-VTA-031.* Sealing protects the material from the relayer;
it does not tell the holder that the bundle they opened is the one that was
sealed for them. The second channel is what closes that, and it is required
rather than recommended because the relaying party is precisely the party in a
position to substitute.

### Delegation to software agents

**VTI-VTA-040** — A software agent acting for the principal MUST have its own
identifier and its own access control entry, and MUST NOT operate under the
principal's own credential.

**VTI-VTA-041** — An agent SHOULD be scoped to a trust context of its own.

**VTI-VTA-042** — A VTA MUST provide a means of immediately withdrawing an
agent's authority, and that means MUST cover every grant within the agent's
scope, as required by VTI-ACL-081 and VTI-ACL-082.

**VTI-VTA-043** — Every action a VTA performs at an agent's request MUST be
recorded such that the principal's operator can observe what the agent did.

*Rationale for VTI-VTA-040.* An agent operating under the principal's own
credential cannot be revoked without revoking the principal, cannot be
distinguished from the principal in any audit trail, and cannot be narrowed. It
is not a delegate; it is a copy.

### Backup, restore and succession

**VTI-VTA-050** — A backup MUST be encrypted, and MUST NOT be usable without
material held separately from it.

**VTI-VTA-051** — A restore MUST be recorded in the audit trail, and a node MUST
be able to report that its current state derives from a restore and from when.

**VTI-VTA-052** — Where a VTA supports succession — the transfer of control to
another party on the principal's incapacity — the process MUST require
authority at least equal to the authority it confers, and MUST be auditable
after the fact by the receiving party.

*Rationale for VTI-VTA-051.* A restore silently reinstates authority as it
stood at the time of the backup, including entries revoked since. Recording the
restore is what allows the difference to be reconciled deliberately rather than
discovered later as a grant nobody remembers making.

### The operator surface

**VTI-VTA-060** — An operator MUST be able to enumerate every access control
entry, every delegated agent and every active session on their VTA.

**VTI-VTA-061** — An operator MUST be able to terminate any session and withdraw
any entry.

**VTI-VTA-062** — Every action a VTA performs on the principal's behalf MUST be
recorded such that the operator can observe it.

*Rationale.* An action that cannot be observed cannot be governed, and a
control the operator cannot reach is a control that belongs to whoever operates
the software rather than to the principal it acts for.
