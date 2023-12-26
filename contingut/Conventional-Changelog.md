El format per fer commit és el següent:
`<codi>: [#<issueId>] <descripcio>`

El `codi` pot ser un dels següents:
- feat (new feature for the user, not a new feature for build script)
- fix (bug fix for the user, not a fix to a build script)
- docs (changes to the documentation)
- style (formatting, missing semi colons, etc; no production code change)
- refactor (refactoring production code, eg. renaming a variable)
- test (adding missing tests, refactoring tests; no production code change)
- chore (updating grunt tasks etc; no production code change)
- nolog (altres)

El `issueId` es opcional, però si s'indica quedarà relacionada a la tasca de git