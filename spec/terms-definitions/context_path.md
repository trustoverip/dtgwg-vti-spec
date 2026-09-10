[[def: context path]]

~ The materialized path of a [[ref: trust context]] within its tree, and the context's identifier — for example `acme/eng/team-a`.

~ Segments are separated by a single solidus and compared octet by octet. Ancestry is evaluated over whole segments, never over the identifier as a string, so `acme` is an ancestor of `acme/eng` and is not an ancestor of `acme-evil`.
