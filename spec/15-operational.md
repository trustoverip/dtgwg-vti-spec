## Operational and Assurance Requirements

{{This section is normative, except where marked RECOMMENDED.}}

This chapter covers what an operator of a VTI deployment is required to be able
to do, and what a deployment is required to be able to demonstrate.

### Bootstrap

{{Cold-start of a node, and sealed transfer of provisioned identities.}}

### Backup, restore and succession

{{Requirements common to every node type.}}

### Protection at rest

{{Sealing, secret storage backends, and the OPTIONAL use of
hardware-attested execution.}}

### Kill switch and containment

{{Immediate revocation of a delegated agent, and fail-closed behaviour of
outbound access on containment.}}

### Audit trail integrity

{{Tamper-evidence, retention, and what an auditor MUST be able to reconstruct.}}

### Time

{{Time sources, permitted skew, and the behaviour required when time cannot be
trusted.}}

### What an operator must be able to answer

{{The questions a conforming deployment MUST be able to answer about itself:
who holds authority where, what a given agent may do, what was approved by
whom, and what would happen if a given credential were revoked right now.}}
