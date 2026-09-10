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

{{Each proposition from the Composition Requirements chapter with its ownership
classification (`COMPONENT-OWNED`, `COMPOSITION-OWNED`, `JOINTLY-OWNED`,
`ASSURANCE-ONLY`, `UNRESOLVED`), its supporting evidence, its legitimate
counter-cases, and pointers to any executable pressure test. Propositions that
remain unresolved are published here with that status visible.}}

### Appendix F: Acknowledgements

{{The final appendix should contain any additional acknowledgements}}

Copyright © 2026 Trust Over IP (ToIP) Contributors  
This work is licensed under a Creative Commons Attribution 4.0 International License.
