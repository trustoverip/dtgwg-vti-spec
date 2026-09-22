## Audit

This section is normative.

### In plain terms

An audit trail is how a deployment answers, after the fact, the question the
rest of this specification is built to keep answerable: who did this, under
what authority, and was it allowed? It is only useful if it records the refusals
as well as the successes, if nobody can quietly edit it, and if keeping it does
not force an operator to choose between integrity and a person's right to be
forgotten.

### What this chapter defines

This chapter defines which authority events a node records, what each record
contains, and the properties the trail as a whole must hold. Other chapters add
events to the set — sessions in the Sessions and Authentication chapter, and
operator obligations in the Operational and Assurance Requirements chapter.

**VTI-AUD-001** — A node MUST record an audit event for: the creation,
modification, removal and rotation of an access control entry; the creation and
deletion of a context; the creation, modification and removal of an approval
rule; and every approval decision.

**VTI-AUD-002** — An audit event MUST identify the subject that acted, the
authority that permitted the action, the resource acted on, the time, and the
outcome.

**VTI-AUD-003** — A node MUST audit refusals as well as successful actions.

**VTI-AUD-004** — The audit trail MUST be tamper-evident: an observer MUST be
able to detect the modification or removal of a previously recorded event.

**VTI-AUD-005** — An audit record MUST NOT embed personal data in a form that
cannot later be erased. Where a record must refer to personal data, it MUST do
so by reference or by a salted commitment, such that erasing the data leaves
the record's integrity verifiable.

**VTI-AUD-006** — Access to the audit trail MUST itself be authorized, and MUST
itself be audited.

*Rationale for VTI-AUD-005.* Tamper-evidence and erasure are in genuine
tension: a chain that cannot be altered cannot have a name removed from it. The
tension is resolved at write time or not at all. A record that commits to a
reference rather than embedding the data can honour an erasure without breaking
the chain; a record that embedded the data leaves an operator choosing between
two obligations, and whichever they choose they will be in breach of the other.

*Rationale for VTI-AUD-006.* The audit trail is the most complete description
of a principal's activity that a deployment holds. Reading it is a privileged
act, and a control that records everything except who read it has a gap exactly
where an insider would stand.

*Rationale for VTI-AUD-003.* The refused attempts are the half of the record
that answers whether a control is working, and a trail containing only
successes cannot distinguish a system nobody attacked from one whose gate is
open.
