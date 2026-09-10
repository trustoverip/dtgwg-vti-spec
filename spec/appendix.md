## Appendices

### Appendix A: Client onboarding

{{Sequence diagrams for the four-step onboarding flow, a worked example, and
test vectors for the key roll — including the failure cases either side of the
commit point.}}

### Appendix B: Access control entry schema

{{The entry shape referenced throughout the Trust Contexts chapter.}}

### Appendix C: Role and capability annex

{{The role set, and the versioned annex of registered capabilities, as of this
version of this specification. The registry is extensible — see VTI-ACL-032 for
the rule that makes extension safe.

Table 1, roles: for each role, the capability ceiling it confers. Referenced by
VTI-ACL-010 and VTI-ACL-011.

Table 2, capabilities: for each registered capability, its identifier, the
power it gates, the roles whose ceiling includes it, and whether it is additive
(VTI-ACL-033).}}

### Appendix D: Context path grammar

This appendix is normative. It states the grammar referenced by VTI-CTX-010
through VTI-CTX-013.

```abnf
context-path  = segment *7( "/" segment )
segment       = 1*64( segment-char )
segment-char  = ALPHA / DIGIT / "." / "_" / "-"
```

`ALPHA` and `DIGIT` are as defined in [IETF RFC 5234]. The repetition limits
carry two of the constraints directly: a path holds between one and eight
segments, and a segment holds between one and sixty-four characters. Because
`/` cannot appear inside a segment, the grammar admits no leading, trailing or
doubled separator, and no empty segment.

**Ancestry.** Let `segments(x)` be the ordered sequence of segments of a valid
context path `x`. A path `a` is an *ancestor-or-self* of a path `d` exactly
when `segments(a)` is a prefix of `segments(d)`, where segments are compared
octet by octet (VTI-CTX-015).

Note that ancestry is defined over the segment sequences and not over the
strings: `acme` is an ancestor-or-self of `acme/eng` and of `acme`, and is not
an ancestor of `acme-evil`, which shares a leading substring but not a leading
segment. See VTI-CTX-016.

### Appendix E: Composition proposition catalogue

This appendix is informative, and is the record required by VTI-CMP-120.

Each requirement in the Composition Requirements chapter is recorded here with
its ownership classification, the evidence supporting it, the legitimate
counter-cases considered, and a pointer to any executable pressure test.

#### Ownership classification

| Classification | Meaning |
|---|---|
| `COMPONENT-OWNED` | The requirement belongs to a component specification, and appears here only as a cross-reference. |
| `COMPOSITION-OWNED` | No component can establish it; this specification owns it. |
| `JOINTLY-OWNED` | A component owns the underlying semantics, and this specification owns their survival across composition. |
| `ASSURANCE-ONLY` | Not a normative requirement on an implementation; a property an assessment programme examines. |
| `UNRESOLVED` | Supported by evidence, not yet resolved to an owner. Published with that status visible. |

`JOINTLY-OWNED` carries most of the difficulty. A component specification may
properly own the semantics of authority, delegation, lifecycle state, privacy,
provenance or evidence, while the question of whether those semantics survive a
multi-component interaction belongs here. Recording a requirement as jointly
owned is not a step toward moving it into this specification.

#### Evidence families

{{Each family records a set of related propositions carried through the same
path: threat proposition, falsifiable invariant, executable pressure test,
legitimate counter-case, disposition. Families are added as the evidence is
produced.}}

**Family 1 — false independence.** Seven threat classes sharing one unsafe
inference, each with an executable test and a legitimate counter-case. Supports
VTI-CMP-070 through VTI-CMP-074.

| Threat class | Preserved judgment |
|---|---|
| Sybil | multiplicity is not independence |
| False diversity | nominal diversity is not governance independence |
| Trust laundering | provenance depth is not assurance depth |
| Sock puppet | persona multiplicity is not social independence |
| Quorum capture | threshold arithmetic is not independent or legitimate approval |
| Collusion | distinct actors are not necessarily independent actors |
| Selective evidence | valid evidence is not necessarily complete evidence |

Corpus-level result: apparent multiplicity, depth, threshold satisfaction, actor
distinctness or artefact validity is not evidence independence or evidence
completeness. The corpus equally preserves the opposite boundary — legitimate
plurality, pseudonymity, shared infrastructure, transformation, coalitions,
independent agreement, selective disclosure and privacy-preserving minimisation
are not failures merely for resembling an adversarial pattern (VTI-CMP-074).

{{Current disposition: `UNRESOLVED`, with strong `COMPOSITION-OWNED` evidence.
Source material is cited in the Informative References.}}

#### Assessment submissions

{{Submissions received under the assessment interface defined in the
Conformance chapter, each citing a requirement identifier, a disposition of
`supported`, `refuted` or `indeterminate`, the method and artefacts, and the
assessor and date. Where two submissions disagree, both are recorded.}}

### Appendix F: Acknowledgements

{{The final appendix should contain any additional acknowledgements}}

Copyright © 2026 Trust Over IP (ToIP) Contributors  
This work is licensed under a Creative Commons Attribution 4.0 International License.
