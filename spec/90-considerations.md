## Security Considerations

{{This section is informative.}}

{{REQUIRED for all ToIP specifications. To be drafted as numbered subsections
once the normative chapters exist to reference. Expected content includes: the
threat model and trust boundary; enrolment-channel exposure and the mitigations
in the Client Onboarding chapter; authority escalation paths and the
fail-closed rules that block them; revocation timeliness; key compromise and
recovery; and the deployment checklist.}}

## Privacy Considerations

{{This section is informative.}}

{{REQUIRED for all ToIP specifications. Expected content includes: correlation
across contexts and the boundary the context model draws; metadata observable
to mediators and hosts; the composed-interaction rule from the Composition
Requirements chapter; data minimisation in presentations; and what an audit
trail necessarily records about a person.}}

## Governance Considerations

{{This section is informative.}}

{{RECOMMENDED for most ToIP specifications, and material here. Expected
content: the relationship between this specification and the ToIP governance
metamodel; the community administrator as a governance role; what a governance
framework may vary and what it may not; and the configuration-materiality
requirement, which makes a governance change and a code change equivalent when
they alter the same semantics.}}

## Internationalization Considerations

{{This section is informative.}}

{{RECOMMENDED. Expected content: identifier and label character handling;
locale-dependent rendering of anything a human approves; and the requirement
that a human decision surface be presented in a language the decider reads.}}

## Accessibility Considerations

{{This section is informative.}}

{{RECOMMENDED, and substantive here rather than pro forma: several requirements
in this specification depend on a human understanding what they are approving.
An approval a person cannot perceive or comprehend is not consent, which makes
the approver's decision surface a matter for this specification rather than for
implementations alone.}}
