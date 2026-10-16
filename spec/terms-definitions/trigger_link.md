[[def: trigger link]]

~ The text an inviter shows as a QR code or a link to start an exchange with a wallet: an `https` URL whose fragment names the inviter, a handle for the pending exchange and, optionally, an expiry and a flow. It carries no task, endpoint, key or authority.

~ A wallet treats every field of a trigger link as an untrusted hint, and takes everything the exchange needs from the inviter's verified DID document.
