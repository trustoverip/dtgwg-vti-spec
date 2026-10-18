## Trust Registries and Cross-Community Recognition

This section is normative.

This chapter specifies how communities publish, how they look each other up,
and the limits of what a registry answer establishes. It applies equally to the
answers a [[ref: VTN]] publishes about the parties it lists; the network chapter
states what it adds.

### Publication

**VTI-REG-001** — A community MUST NOT publish personal data, for the purpose
of recognition by other communities, beyond what that recognition requires.

**VTI-REG-002** — A published entry MUST identify the framework under which it
is made, so that a consumer can determine what the entry asserts.

**VTI-REG-003** — A consumer MUST NOT infer any authority from a registry entry
beyond what the entry's framework states it asserts.

**VTI-REG-004** — A community that publishes personal data for any purpose
other than recognition:

1. MUST state the purpose, in the published entry and to the subject;
2. MUST tell the subject, before the data is first published, what will be
   published, for what purpose and to whom;
3. MUST NOT publish anything beyond what that purpose needs; and
4. where the data is the subject's own statement about themselves, such as a
   profile, MUST publish it only as a statement the subject has signed, and
   MUST NOT sign or compose it itself.

*Rationale for VTI-REG-001 and VTI-REG-004.* Recognition is the purpose
registries were first built for, and VTI-REG-001 bounds publication by it. A
community also publishes for other purposes — the rights a member holds over a
repository, so that a forge's checks can verify a commit's signer, or a card a
member chose to make public. Read as covering every publication, VTI-REG-001
forbids those outright; read as covering recognition only, it leaves them with
no bound at all. VTI-REG-004 is the bound: the purpose is stated so a reader can
tell what the entry is for, the subject is told first so that publication is
never how they find out, and what is published is limited by the purpose it
serves. Where the data is the member's own statement — what they call
themselves, what they do — the community mirrors what the member signed rather
than asserting it, so that the community is never the author of a claim about
a person that the person did not make.

### What a registry answer establishes

**VTI-REG-010** — Presence in a registry MUST NOT be treated as evidence of
current authority. See VTI-CMP-032 and VTI-CMP-040.

**VTI-REG-011** — A registry answer MUST carry the time at which it was
determined, and a consumer MUST apply a freshness bound to it.

**VTI-REG-012** — Where a registry cannot be reached, the outcome MUST be
indeterminate as required by VTI-CMP-080. A consumer MUST NOT proceed as though
the lookup had succeeded, and MUST NOT treat unavailability as absence.

**VTI-REG-013** — A deployment MUST state its behaviour on registry
unavailability for each class of decision, as required by VTI-CMP-082.

*Rationale for VTI-REG-012.* Unavailability and absence are different facts
with opposite consequences: one means the registry has no entry for this party,
the other means we do not know. Collapsing them either admits parties that were
never recognised or refuses parties that were, and which of the two happens is
decided by an error handler rather than by policy.

### Cross-community recognition

**VTI-REG-020** — A community recognising a foreign member MUST require the
member to present their community credentials with proof of possession bound to
a single-use challenge issued by the recognising community, as required by
VTI-CRD-022.

**VTI-REG-021** — The recognising community MUST verify that the presenter is
the subject of every credential presented, and MUST refuse where the subjects
do not agree.

**VTI-REG-022** — The recognising community MUST check the status of the
presented credentials with the issuing community, and MUST refuse a revoked
credential.

**VTI-REG-023** — The mapping from a foreign role to local authority MUST be
explicit policy, and MUST NOT grant authority the recognising community would
not grant to a local member of equivalent standing.

**VTI-REG-024** — Recognition MUST NOT be transitive. A community that
recognises another MUST NOT thereby recognise the communities that the second
recognises.

*Rationale for VTI-REG-020.* Community credentials are bearer artefacts: the
pair that evidences membership is, without proof of possession, a replayable
impersonation of the member for anyone who obtains it — from a relayed join, an
audit log, or a compromised device. Recognition is exactly the flow in which
such a pair travels furthest from its holder.

*Rationale for VTI-REG-024.* Transitive recognition composes trust decisions
that were each made about one counterparty into a reach nobody assessed, and it
does so silently: the third community never appears in the second's decision. A
community that wants the transitive effect can recognise the third directly,
which is the same outcome with someone accountable for it.
