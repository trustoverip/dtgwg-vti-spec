[//]: # 'This file, named "terms-and-definitions-intro.md" is mandatory and should not be deleted. However, you can safely delete this comment and replace it with text of your choice.'

## Terminology

This section is informative.

The terms below are used precisely throughout this specification. A term
defined in a referenced glossary is transcluded rather than redefined here.

Two distinctions are worth stating before the list, because conflating either
has produced defects in running systems:

- **Delegation is not authority.** That a party is able to execute an
  operation — it holds a key, it can reach the endpoint, its message is
  well-formed — is evidence about capability and none about whether the
  authority to do so was granted. See the Composition Requirements chapter.
- **An attribute is an attribute.** Where an implementation's internal
  vocabulary uses a different word for a subject's attributes, that word means
  something else at another layer. This specification says *attribute* and
  means the thing a credential asserts about its subject.

Any hyperlinked term not included in this section is referenced from one of the following glossaries:

- [ToIP Main Glossary](https://glossary.trustoverip.org)
- [ToIP General IT Glossary](https://trustoverip.github.io/ctwg-general-glossary)

The glossaries of the DTG component specifications this document binds are
added to that list as the normative references to them are settled.
