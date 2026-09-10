[[def: bootstrap identity]]

~ The ephemeral identifier a [[ref: client]] mints for itself before it has any standing, and which is conveyed out-of-band to an administrator for enrolment.

~ A bootstrap identity is low-trust by construction: it has crossed a channel outside the trust system. A client should rotate off it on first successful authentication, and an entry naming one that is not rotated must carry an expiry.
