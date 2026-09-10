## Security Considerations

This section is informative.

This section summarises the security reasoning behind the normative
requirements, and records the threats they address. It introduces no
requirements of its own; each item names the requirements that carry it.

1. **The trust boundary is per operator, not per service.** Two services run by
   one operator are one boundary. A security argument that depends on their
   separation is not sound. See the Architecture chapter.
2. **Authority is data, not position.** Nothing is authorized by configuration,
   deployment location, or possession of a transport credential
   (VTI-ACL-001). The recurring alternative — a service trusted because of
   where it sits — cannot be revoked, audited or scoped.
3. **Ancestry is the security gate.** The segment-wise comparison and the
   prohibition on string-prefix matching (VTI-CTX-016) are what keep authority
   over one subtree from reaching another that shares a leading substring.
4. **Empty is not everywhere.** The scope-and-role pairing (VTI-ACL-021) closes
   a defect class in which an entry authorized nowhere is read as authorized
   everywhere.
5. **Enrolment is the exposed step.** A bootstrap identifier crosses a channel
   outside the trust system, so the threat is substitution rather than
   disclosure; second-channel confirmation and a distinguishable
   no-entry failure (VTI-CLT-015, VTI-CLT-016) are what detect it.
6. **Rotation is the recoverable direction.** Reachability is proven before the
   commit (VTI-CLT-023) because a rotation onto an unreachable identifier
   cannot be undone, and the swap preserves authority exactly (VTI-CLT-029) so
   that a self-service operation cannot escalate.
7. **Revocation must reach every grant beneath it.** A sweep that reports
   success while a descendant grant survives (VTI-ACL-082) ends the incident
   response that would have found it.
8. **Consent is bound to what was approved.** Elevating the requester's session
   on the strength of another party's approval (VTI-APV-005) converts approval
   of one act into a window in which every gated act passes.
9. **Sender authentication is not document authentication.** A transport that
   authenticates its sender does not relieve a producer of signing what it
   sends (VTI-OPS-021); the two answer different questions.
10. **A send that lies is worse than a send that fails.** Reporting success for
    a dropped frame (VTI-TRN-030) makes every layer above it wrong in the same
    direction, including the health signal meant to detect it.
11. **Credentials without proof of possession are bearer tokens**
    (VTI-CRD-021 through VTI-CRD-023), and recognition is where they travel
    furthest from their holder (VTI-REG-020).
12. **Verification is not authority** (VTI-CMP-030). A cryptographically
    impeccable credential can be issued by a party that no longer holds the
    authority it asserts.
13. **Containment cannot require cooperation** (VTI-OPR-022), and cannot
    destroy the record of what it contained (VTI-OPR-023).
14. **A signing oracle that signs anything is a forgery service.** A VTA parses
    and constrains what it signs, checks that the issuer, audience and subject
    are within the requesting entry's authority, and never signs opaque octets
    (VTI-VTA-004 through VTI-VTA-007). Blind signing removes every bound at
    once, and invisibly: the resulting signature is indistinguishable from an
    intended one.
15. **A signed document is replayable unless something remembers it**
    (VTI-OPS-025 through VTI-OPS-027) — and the record has to be shared across
    bindings, or the node's replay protection is as good as its least-used
    transport.
16. **Negotiation is an attacker's lever.** Discovery responses are
    authenticated and each operation has a version floor a peer cannot argue a
    node below (VTI-OPS-046, VTI-OPS-047); without the floor, retiring a
    version is advisory.
17. **Refusals are answers.** A refusal that distinguishes *not found* from
    *not permitted* turns an authorization boundary into a directory
    (VTI-OPS-035), and a challenge endpoint that answers differently for a
    known subject is an enumeration oracle (VTI-SES-007).
18. **What the approver saw must be what was committed to** (VTI-APV-013), and
    unrestricted authority — the grant from which every other grant can be
    made — requires a second party (VTI-APV-014).
19. **An algorithm is retired at the moment it is broken**, not at the next
    release (VTI-KEY-013).

## Privacy Considerations

This section is informative.

1. **The context boundary is the primary privacy control.** A context per
   purpose limits both what a compromise reaches and what a single credential
   correlates.
2. **Correlation is a property of the interaction, not of the proof mechanism**
   (VTI-CMP-060, VTI-CMP-061). An unlinkable proof carried beside a stable name,
   over an observable route, after a revealing lookup, is a linkable
   interaction.
3. **Correlators arise outside credentials** (VTI-CMP-062): naming, routing,
   session identifiers and registry lookups each introduce them, and a
   deployment is required to be able to identify the ones it creates.
4. **Status checking leaks by construction unless designed not to**
   (VTI-CRD-013). The verifier acting responsibly is what generates the signal.
5. **One identity per client instance** (VTI-CLT-052). A shared client identity
   is a durable cross-device correlator, and it is usually created by cloning a
   machine image rather than by a decision to share.
6. **Minimisation is normative, not aspirational** (VTI-CRD-040 through
   VTI-CRD-042, VTI-CMP-063).
7. **Erasure is a member's right that policy cannot downgrade**
   (VTI-MEM-032), and publication batching must not be repurposed to delay the
   effect of a departure (VTI-MEM-034).
8. **The audit trail is itself personal data.** Tamper-evidence and erasure are
   in genuine tension, and it is resolved at write time: a record refers to
   personal data rather than embedding it, so an erasure leaves the chain
   verifiable (VTI-AUD-005). Reading the trail is a privileged act and is
   itself audited (VTI-AUD-006), and retention has a stated maximum
   (VTI-OPR-031).
9. **An identifier that spans contexts defeats the context boundary** without
   touching a credential: the observer only has to notice the same name twice.
   Identifiers are per relationship where the relationship does not need a
   public identity, and a client identifier is never reused across contexts
   (VTI-KEY-005 through VTI-KEY-007).
10. **Routing is the part the mediator necessarily sees.** Routing identifiers
    differ per relationship so that an intermediary cannot reconstruct a
    principal's activity across contexts from routing alone (VTI-TRN-026), and
    operation-revealing metadata stays inside the envelope (VTI-TRN-025).
11. **A push service is an intermediary nobody chose.** A wake notification
    carries no content and nothing that reveals what is waiting (VTI-TRN-028).
12. **Herd privacy is only privacy while the crowd is real.** A status list
    allocated per subject, or in issuance order, has the shape of a herd and
    the behaviour of a unique identifier (VTI-CRD-014).
13. **A verifier learns things it may not pass on** (VTI-CRD-043).

## Governance Considerations

This section is informative.

1. **Where governance attaches.** A governance framework may impose stricter
   requirements than this specification and may not reduce any of them
   (VTI-VTC-030).
2. **The context administrator is a governance role.** Subtree authority
   (VTI-ACL-022) is how an organisational structure is expressed in the
   authority model, and the approver account (VTI-ACL-041) is how a separation
   of duties is.
3. **Policy is an input to every decision, so its version is part of the
   record** (VTI-VTC-031).
4. **Configuration change is governance change** (VTI-CMP-090). A change that
   alters trust semantics without altering the protocol is materially
   equivalent to a code change and attracts the same review.
5. **Recognition is not transitive** (VTI-REG-024). A community's governance
   reaches the parties it recognises directly and no further.
6. **Ownership of a requirement is recorded, not assumed** (VTI-CMP-120,
   Appendix E). Where this specification is silent about who owns a decision,
   an implementation refuses rather than choosing (VTI-ACL-092).

## Internationalization Considerations

This section is informative.

1. **Identifiers are not human-facing text.** Context path segments are
   restricted to a narrow ASCII set and compared octet by octet
   (VTI-CTX-011, VTI-CTX-015). Confusable-character attacks on an authorization
   boundary are prevented by the restriction rather than by normalization,
   which is itself a source of confusability.
2. **Labels are human-facing.** Where an entry, a context or a community
   carries a display name, it is presentation and carries no authority.
3. **Anything a human approves must be presented in a language they read.** A
   decision surface rendered in a language the approver does not read does not
   satisfy VTI-APV-011.

## Accessibility Considerations

This section is informative.

Several normative requirements in this specification depend on a human
understanding what they are being asked to authorise: the consent ceremony
(VTI-APV-011), the step-up demand a client surfaces (VTI-CLT-042), and the
composed-flow requirement on human control (VTI-CMP-112).

An approval a person cannot perceive or comprehend is not consent. A control
built on one provides assurance to everyone except the person exercising it,
and it fails in the direction that looks like success — the record shows an
approval, and what happened was a person clicking past something they could not
read. The decision surface is therefore within the scope of this specification
rather than a matter for implementations alone, and it is subject to the
accessibility requirements of the jurisdictions the deployment serves.
