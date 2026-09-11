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

**Credentials**

- [VC-DATA-MODEL] *Verifiable Credentials Data Model.*
  <https://www.w3.org/TR/vc-data-model/>
- [DTG-CRED] *DTG Credentials Specification* — the credential families a VTI
  node issues, holds and presents. *(pending)*
- [STATUS] The credential status mechanism relied on by VTI-CRD-010 through
  VTI-CRD-013. *(pending)*

**Operations and transports**

- [TRUST-TASKS] *Trust Tasks Specification* — the canonical operation
  catalogue whose precedence is required by VTI-OPS-001. *(pending)*
- [DIDCOMM] *DIDComm Messaging v2.*
- [TSP] *Trust Spanning Protocol.* *(pending)*

**Cryptography**

- [RFC 8032] *Edwards-Curve Digital Signature Algorithm (EdDSA)* — Ed25519, as
  required by VTI-KEY-010. <https://datatracker.ietf.org/doc/html/rfc8032>
- [RFC 7748] *Elliptic Curves for Security* — X25519, as required by
  VTI-KEY-010. <https://datatracker.ietf.org/doc/html/rfc7748>

### Informative References

- [RFC 6973] *Privacy Considerations for Internet Protocols.*
  <https://datatracker.ietf.org/doc/html/rfc6973>
- [TOIP-GOV] *ToIP Governance Metamodel Specification.*
  <https://trustoverip.org/wp-content/uploads/ToIP-Governance-Metamodel-Specification-V1.0-2021-12-21.pdf>
- [TOIP-GLOSSARY] *ToIP Main Glossary.* <https://glossary.trustoverip.org>
- [TOIP-IT-GLOSSARY] *ToIP General IT Glossary.*
  <https://trustoverip.github.io/ctwg-general-glossary>
- [COMPOSITION-EVIDENCE] Composition assurance evidence informing the
  Composition Requirements chapter and Appendix E, including the
  false-independence corpus recorded there. *(pending — the working group is to
  fix a citable reference for each evidence family it admits.)*
