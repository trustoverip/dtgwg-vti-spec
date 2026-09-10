## Credentials and Presentations

This section is normative.

This chapter binds the DTG credential specifications to VTI's requirements. It
does not restate credential data models: it states what a VTI node does when it
issues, holds, presents or verifies one.

### Issuance

**VTI-CRD-001** — An issuer MUST hold authority to issue in the context the
credential belongs to.

**VTI-CRD-002** — A credential MUST identify its issuer by a resolvable
identifier, and a verifier MUST resolve it rather than relying on a name
carried in the credential.

**VTI-CRD-003** — A credential MUST identify its subject in a form that permits
a verifier to establish, at presentation, that the presenter controls it.

### Status and revocation

**VTI-CRD-010** — A credential whose validity can change during its lifetime
MUST carry a status mechanism.

**VTI-CRD-011** — A verifier MUST check status before relying on such a
credential.

**VTI-CRD-012** — Where status cannot be determined, the outcome MUST be
indeterminate as required by VTI-CMP-080, and MUST NOT default to valid or to
revoked.

**VTI-CRD-013** — A status mechanism MUST NOT require a query that identifies
the individual credential or its subject to the party answering it.

*Rationale for VTI-CRD-013.* A status check that names the credential tells the
issuer, on every use, which of its subjects is transacting and roughly with
whom. The privacy cost is paid by the subject and collected by the issuer, and
it accrues precisely because the verifier did the responsible thing.

### Presentation

**VTI-CRD-020** — A presentation MUST name its audience, and the audience MUST
be the party the presentation is made to.

**VTI-CRD-021** — A verifier MUST establish that the presenter controls the
subject of each credential presented. A binding declared in the payload MUST
NOT be accepted in place of one established by the protocol.

**VTI-CRD-022** — The verifier MUST issue a single-use challenge bound to
itself, and MUST refuse a presentation whose holder proof does not commit to
that challenge.

**VTI-CRD-023** — A credential or set of credentials MUST NOT be accepted as
evidence of the presenter's identity without the proof of possession required by
VTI-CRD-021 and VTI-CRD-022.

*Rationale.* Without proof of possession, a credential is a bearer token:
anyone who captures it — from a relayed exchange, an audit log, a compromised
device — holds a replayable impersonation of its subject. The credential
formats do not make this mistake; compositions do, by treating a valid
signature over a valid credential as though it were an authenticated session.

### What verification establishes

**VTI-CRD-030** — Successful verification MUST be treated as establishing the
provenance and integrity of the credential at the time it was issued, and
nothing further. See VTI-CMP-030.

**VTI-CRD-031** — Where a relying decision depends on the issuer's authority at
the time of the decision, the verifier MUST establish that separately, as
required by VTI-CMP-032.

### Minimisation

**VTI-CRD-040** — A verifier MUST NOT require disclosure beyond what the
relying decision needs.

**VTI-CRD-041** — Where a credential supports selective disclosure, a holder
SHOULD present only the attributes the decision requires, and a verifier MUST
NOT refuse a presentation solely because it was selectively disclosed.

**VTI-CRD-042** — A verifier MUST NOT retain presented attributes beyond what
the decision and its audit record require.
