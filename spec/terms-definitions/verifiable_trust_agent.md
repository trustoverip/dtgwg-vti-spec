[[def: VTA, verifiable trust agent]]

~ The key, credential and authority root for a [[ref: principal]] in a [[ref: VTI]] deployment. A VTA holds the principal's key hierarchy, organises what it holds into [[ref: trust context]]s, decides who may act in each of them, holds and presents credentials on the principal's behalf, and delegates narrowed authority to the clients and software agents that act for the principal.

~ A VTA is the authority for its principal's context tree and for key derivation within it. A [[ref: VTC]] is provisioned on top of a VTA and derives its identity from it; the dependency is one-way.
