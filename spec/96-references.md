## References

The entries below name the specifications this document binds. An entry marked
*(pending)* names one whose stable citation the working group has yet to fix;
those, and the exact titles and locations of the rest, are confirmed against
their current versions before this specification leaves Working Draft.

### Normative References

**Requirements language and notation**

- [RFC 2119] *Key words for use in RFCs to Indicate Requirement Levels.*
  <https://datatracker.ietf.org/doc/html/rfc2119>
- [RFC 5234] *Augmented BNF for Syntax Specifications: ABNF.*
  <https://datatracker.ietf.org/doc/html/rfc5234> — the notation used in
  Appendix D.

**Identifiers**

- [DID-CORE] *Decentralized Identifiers (DIDs) v1.0.*
  <https://www.w3.org/TR/did-1.0/>
- [DID-KEY] *The did:key Method* — the method required for client bootstrap
  identities by VTI-KEY-002.
- [DID-WEBVH] *The did:webvh Method* — the method required for durable node
  identities by VTI-KEY-001.

**Links and encodings**

- [RFC 3986] *Uniform Resource Identifier (URI): Generic Syntax* — relative
  reference resolution for the path form of `_type` (VTI-LNK-041).
  <https://datatracker.ietf.org/doc/html/rfc3986>
- [RFC 4648] *The Base16, Base32, and Base64 Data Encodings* — base64url,
  section 5, for the trigger link handle (VTI-LNK-033).
  <https://datatracker.ietf.org/doc/html/rfc4648>
- [RFC 9110] *HTTP Semantics* — that a client sends no fragment in a request or
  in `Referer`, and that a redirect without a fragment carries the original one
  (VTI-LNK-010, VTI-LNK-083). <https://datatracker.ietf.org/doc/html/rfc9110>
- [URL] *WHATWG URL Standard* — `application/x-www-form-urlencoded` parsing and
  host parsing for trigger links (VTI-LNK-020). <https://url.spec.whatwg.org/>
- [RFC 6761] *Special-Use Domain Names*; [RFC 6762] *Multicast DNS*;
  [RFC 8375] *Special-Use Domain 'home.arpa.'* — names refused by the host
  rules (VTI-LNK-060).
- [ISO 18004] *ISO/IEC 18004:2024, QR Code bar code symbology specification* —
  the capacities behind the producer limits of VTI-LNK-081.

**Credentials**

- [VC-DATA-INTEGRITY] *Verifiable Credential Data Integrity 1.0* — proof sets,
  and the rule that a proof's verification method is listed in the
  relationship its proof purpose names (VTI-KEY-100, VTI-KEY-102).
  <https://www.w3.org/TR/vc-data-integrity/>

- [VC-DATA-MODEL] *Verifiable Credentials Data Model.*
  <https://www.w3.org/TR/vc-data-model/>
- [DTG-CRED] *Decentralized Trust Graph Credentials — Core Specification* — the
  credential families a VTI node issues, holds and presents, and the
  `taskContext` and `taskDigestMultibase` properties by which a credential
  cites the exchange that attests it (VTI-CMP-022).
  <https://github.com/trustoverip/dtgwg-cred-spec>
- [STATUS] The credential status mechanism relied on by VTI-CRD-010 through
  VTI-CRD-013. *(pending)*

**Operations and transports**

- [TRUST-TASKS] *Trust Tasks Specification* — the framework every Trust Task
  specification conforms to, including the outcome evidence of a cited exchange
  (VTI-CMP-022), with the canonical operation catalogue whose precedence is
  required by VTI-OPS-001 published in its registry at <https://trusttasks.org/>.
  <https://github.com/trustoverip/dtgwg-trust-tasks-spec>
- [DIDCOMM] *DIDComm Messaging v2.*
- [TSP] *Trust Spanning Protocol.* *(pending)*

**Cryptography**

- [RFC 8032] *Edwards-Curve Digital Signature Algorithm (EdDSA)* — Ed25519, as
  required by VTI-KEY-010. <https://datatracker.ietf.org/doc/html/rfc8032>
- [RFC 7748] *Elliptic Curves for Security* — X25519, as required by
  VTI-KEY-010. <https://datatracker.ietf.org/doc/html/rfc7748>
- [FIPS-204] *Module-Lattice-Based Digital Signature Standard* — ML-DSA, an
  OPTIONAL algorithm under VTI-KEY-011, named in VTI-KEY-103 as the second
  member of a hybrid `attestation` proof set.
  <https://csrc.nist.gov/pubs/fips/204/final>

### Informative References

- [RFC 8252] *OAuth 2.0 for Native Apps*, section 8.1 — why any app can claim a
  custom scheme (VTI-LNK-013). <https://datatracker.ietf.org/doc/html/rfc8252>
- [KEYRING-LINKS] *One-scan trigger links*, keyring-wallet pull request #354 —
  the proposal the Trigger Links chapter was agreed from, with its annex on
  platform link handling, device tests and conformance vectors.
  <https://github.com/berkmancenter/keyring-wallet/pull/354>
- [RFC 6973] *Privacy Considerations for Internet Protocols.*
  <https://datatracker.ietf.org/doc/html/rfc6973>
- [TOIP-GOV] *ToIP Governance Metamodel Specification.*
  <https://trustoverip.org/wp-content/uploads/ToIP-Governance-Metamodel-Specification-V1.0-2021-12-21.pdf>
- [AS] *Autonomous system (Internet)* — the aggregation and announcement model
  the Verifiable Trust Network chapter draws its comparison from.
  <https://en.wikipedia.org/wiki/Autonomous_system_(Internet)>
- [AYRA] *Ayra.* <https://ayra.forum/about/> — named in the Verifiable Trust
  Network chapter to illustrate a conformance and interoperability body in the
  position of a network owner.
- [FIRSTPERSON] *First Person Network*, First Person Collective.
  <https://www.firstperson.network/> — named in the Verifiable Trust Network
  chapter to illustrate a curation criterion that is neither a sector nor a
  jurisdiction.
- [TOIP-GLOSSARY] *ToIP Main Glossary.* <https://glossary.trustoverip.org>
- [TOIP-IT-GLOSSARY] *ToIP General IT Glossary.*
  <https://trustoverip.github.io/ctwg-general-glossary>
- [KEYRING-REF-07G] *Keyring reference implementation,
  `tsp-reference/ref-07g-outcome-evidence-pairing`* — the executable
  pressure test for Appendix E, Family 2: four pairing rules run
  against seven exchanges built from real Trust Task documents and proofs.
  <https://github.com/berkmancenter/keyring-wallet/tree/c3a7f1d2a547d7a602cf93b87f5672d7a158d4c1/tsp-reference/ref-07g-outcome-evidence-pairing>
- [COMPOSITION-EVIDENCE] Composition assurance evidence informing the
  Composition Requirements chapter and Appendix E, including the
  false-independence corpus recorded there. *(pending — the working group is to
  fix a citable reference for each evidence family it admits.)*
