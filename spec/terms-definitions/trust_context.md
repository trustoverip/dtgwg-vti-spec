[[def: trust context]]

~ The isolation boundary within which a [[ref: VTA]] holds keys, credentials, stored data, agent memory and delegations, and the unit in which authority is granted.

~ Compromise of a credential authorized in one trust context reaches what that context holds and nothing else. Contexts are nested: a context identifier is its [[ref: context path]], and authority over a context extends to every descendant of it.
