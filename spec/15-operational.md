## Operational and Assurance Requirements

This section is normative.

### In plain terms

This chapter is the questions somebody will ask during an incident, written
down in advance.

Who can do what, right now? What has that agent been doing? If we take this
credential away this minute, what stops working? Which version of which policy
allowed that decision last March?

A deployment that cannot answer those does not acquire the ability while the
incident is running. So they are requirements rather than advice: the
interfaces that answer them have to exist before anyone needs them, and the
record they read from has to still be there and still be trustworthy.

### What this chapter defines

This chapter states what an operator of a VTI deployment is required to be able
to do, and what a deployment is required to be able to demonstrate about
itself.

### Bootstrap

**VTI-OPR-001** — Standing up a node MUST NOT require a shared secret
transmitted in the clear.

**VTI-OPR-002** — Where a node's first administrative authority is established
by an unauthenticated path, that path MUST be closed once the authority exists,
and its use MUST be audited.

**VTI-OPR-003** — A node MUST be able to report whether it is still in a
bootstrap state.

### Protection at rest

**VTI-OPR-010** — Secret material MUST be encrypted at rest.

**VTI-OPR-011** — The material protecting it MUST NOT be stored such that
obtaining the protected data also obtains the means to decrypt it.

**VTI-OPR-012** — Where a node runs in a hardware-attested environment, its
attestation MUST be verifiable by a relying party, and the node MUST NOT
represent an unattested deployment as attested.

### Containment

**VTI-OPR-020** — A deployment MUST provide a means of immediately withdrawing
the authority of a delegated agent, covering every grant within that agent's
scope, as required by VTI-ACL-082.

**VTI-OPR-021** — Containment MUST fail closed: where the containment action
cannot be confirmed, the deployment MUST treat the subject as uncontained and
say so.

**VTI-OPR-022** — A containment action MUST NOT depend on the cooperation of the
component being contained.

**VTI-OPR-023** — Containment MUST be able to suspend a subject's authority
without destroying the audit record of what that subject did.

*Rationale for VTI-OPR-023.* The moment authority is withdrawn is the moment
the record becomes evidence. A containment path that deletes the subject
removes the account of the incident along with the access, and the deletion is
irreversible at exactly the point where nobody yet knows what happened.

*Rationale for VTI-OPR-022.* An agent that must acknowledge its own kill switch
is contained only while it is behaving, which is the state in which containment
is not needed. Withdrawal of authority is enforced by the node that grants it,
not by the party losing it.

### Audit

**VTI-OPR-030** — The audit trail MUST be tamper-evident, as required by
VTI-AUD-004.

**VTI-OPR-031** — A deployment MUST state its audit retention period, and that
period MUST have a maximum. Records beyond it MUST be erased or reduced to a
form carrying no personal data.

**VTI-OPR-032** — An auditor MUST be able to reconstruct, for a past moment,
which subjects held which authority.

*Rationale for VTI-OPR-032.* Reconstructing the authority state is what makes
every other record interpretable: an action recorded without the authority that
permitted it can be read but not assessed.

### Time

**VTI-OPR-040** — A node MUST obtain time from a source it can rely on, and MUST
state the maximum skew it tolerates.

**VTI-OPR-041** — Where time cannot be established within that tolerance, a node
MUST refuse operations whose correctness depends on freshness rather than
proceeding with an unverified clock.

### What an operator must be able to answer

**VTI-OPR-050** — A conforming deployment MUST be able to answer, about itself,
at least:

1. which subjects hold authority, over which contexts, with which capabilities;
2. what each delegated agent is permitted to do, and what it has done;
3. which operations require an additional human decision, who can supply it,
   and whether that requirement can currently be satisfied;
4. what would cease to be possible if a given credential were withdrawn now;
5. which version of which policy governed a given past decision.

*Rationale.* Each of these is a question that is asked for the first time
during an incident, and a deployment that cannot answer it then will not
acquire the ability while the incident is running. They are stated as a
requirement rather than as guidance because the interfaces that answer them
have to be built before they are needed.
