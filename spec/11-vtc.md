## The Verifiable Trust Community

{{This section is normative.}}

This chapter specifies the community node: what it is, how it is stood up, and
which authority decisions belong to it rather than to the VTA beneath it.

### What a VTC is

{{A community authority rather than a personal one: membership, lifecycle,
recognition and publication. How it differs from a VTA, stated in terms of what
each one owns.}}

### Provisioning

{{A VTC is provisioned on top of an existing VTA and derives its identity from
it. The dependency is normative and one-way: a VTA does not depend on a VTC.}}

### Which authority lives where

{{The VTC is not a key authority: it creates no contexts and derives no keys.
It references context identifiers as membership metadata, and any filter it
applies over them MUST use the ancestry predicate defined in the Trust Contexts
chapter — the two nodes MUST NOT be able to disagree about the reach of a
scope.

Where the VTC does own an authority decision — admission, membership state,
recognition, publication — this section says so explicitly. The purpose of the
section is that no decision is left with two plausible owners and no actual
one.}}

### The community access control model

{{The VTC's role set, expressed against the model in the Trust Contexts chapter
rather than beside it.}}

### Governance binding

{{Where a governance framework attaches to a community, and what a policy
credential can and cannot change about the behaviour specified here.}}
