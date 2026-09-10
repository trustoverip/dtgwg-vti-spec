{{INSTRUCTIONS ABOUT THE USE OF THIS TEMPLATE: All editorial instructions are enclosed in {{double curly braces}} and MUST be either removed from or replaced in the specification document. All other text MUST be included in the specification document. NOTE: As of 2026, ToIP specifications are required to use [Spec-Up-T](https://trustoverip.github.io/spec-up-t-website/). It will automatically generate a table of contents for the entire specification document.}}

# Verifiable Trust Infrastructure (VTI) Specification

_Version:_ 0.1  
_Document Status:_ Working Draft  
_DOI:_ {{see [this wiki page](https://lf-toip.atlassian.net/wiki/spaces/HOME/pages/767787009/ToIP+Approved+Deliverable+Process#Persistent-DOI-Link) for instructions about how to add a DOI}}  
_GitHub:_ <https://github.com/trustoverip/dtgwg-vti-spec>  
_Published:_ <https://trustoverip.github.io/dtgwg-vti-spec/>

_Editors:_

- Glenn Gore, Affinidi
- {{additional editors, as the Working Group appoints them: full name, optional OrcID, official LF affiliation}}

_Contributors:_ {{MUST list the full names and official LF affiliations of each substantial contributor — all other acknowledgements go in the Acknowledgements Appendix at the end.}}

- {{Contributor 1, Org C}}
- {{Contributor 2, Org A}}

**Abstract**

Decentralized Trust Graph (DTG) specifications are deliberately modular: each
defines one component — an identifier, a credential, a message, a task, a
registry — and each can be conformed to on its own. A working trust system is
none of those things individually. It is a set of running nodes that hold keys,
grant and revoke authority, admit and remove members, delegate to software
agents, and act on evidence produced elsewhere.

This specification defines that infrastructure. It specifies the nodes of a
Verifiable Trust Infrastructure — the Verifiable Trust Agent (VTA), the
Verifiable Trust Community (VTC), their clients and supporting hosts — together
with the model of trust contexts and authority they share, the way a client is
enrolled and operated against them, and the requirements that apply to the
composed system rather than to any one component. That last part matters
because some properties a relying party depends on cannot be established by
inspecting a single specification: they are properties of the composition, and
this document is where they are owned.

**Intellectual Property Rights**

This specification is provided under the [Joint Development Foundation (JDF) charter](https://cdn.platform.linuxfoundation.org/agreements/ToIP.pdf) for [Trust Over IP](https://trustoverip.org) (ToIP) and is subject to the intellectual property rights policy of the **{{insert name of}} Working Group**:

{{modify the following bullets to reflect the IPR terms of the Working Group}}  
_Copyright:_ [Creative Commons Attribution 4.0 International (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/)  
_Patent:_ W3C Mode (based on the [W3C Patent Policy](https://www.w3.org/Consortium/Patent-Policy-20040205/))  
_Source Code:_ [Apache License, Version 2.0](https://www.apache.org/licenses/LICENSE-2.0)

THESE MATERIALS ARE PROVIDED “AS IS.” The parties expressly disclaim any warranties (express, implied, or otherwise), including implied warranties of merchantability, non-infringement, fitness for a particular purpose, or title, related to the materials. The entire risk as to implementing or otherwise using the materials is assumed by the implementer and user. IN NO EVENT WILL THE PARTIES BE LIABLE TO ANY OTHER PARTY FOR LOST PROFITS OR ANY FORM OF INDIRECT, SPECIAL, INCIDENTAL, OR CONSEQUENTIAL DAMAGES OF ANY CHARACTER FROM ANY CAUSES OF ACTION OF ANY KIND WITH RESPECT TO THIS DELIVERABLE OR ITS GOVERNING AGREEMENT, WHETHER BASED ON BREACH OF CONTRACT, TORT (INCLUDING NEGLIGENCE), OR OTHERWISE, AND WHETHER OR NOT THE OTHER MEMBER HAS BEEN ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
