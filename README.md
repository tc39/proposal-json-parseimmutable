# JSON.parse Options Proposal

## Status

Stage 2

**Champions:**

- Nicolò Ribaudo (Igalia)
- Ashley Claymore (Bloomberg)
- Peter Klecha (Bloomberg)

## Overview

This proposal identifies a need in ECMAScript for a function which parses [JSON][json-mdn] strings but returns deeply immutable objects. This can currently be accomplished with a reviver, at the cost of performance and static analyzability. The current state of the proposal is to add an options bag argument to the existing `JSON.parse` function, allowing for various "canned" revivers to be used, including one which ensures the returned object is deeply frozen.

```javascript
const obj = JSON.parse('{ "one": { "two": 3 } }', { freeze: true });
assert(Object.isFrozen(obj));
assert(Object.isFrozen(obj.one));
```

To achieve a similar result today a reviver can be used:

```javascript
JSON.parse(data, (key, value) => Object.freeze(value));
```

But a native implementation could be much faster than a reviver-based implementation, and static analysis would greatly benfit from the knowledge that the result of `JSON.parse(..., { freeze: true })` is always deeply frozen.

Frozen plain (non-array) objects returned by `JSON.parse(..., { freeze: true })` would by default also have `null` prototypes, but this behavior can also be configured independently of the freezing behavior. So in total this proposal would add an options bag to `JSON.parse` with two boolean properties: `freeze` and `nullPrototype`. The default values for these options would be `false`, so that the behavior of `JSON.parse` remains unchanged unless the options are explicitly passed.

This proposal also opens the door for other canned revivers, e.g., one which converts date strings into `Temporal` objects, or one which converts numeric strings into `BigInt` values. However, the scope of this proposal is limited to the two options described above.


## History

This proposal was originally part of the [Records and Tuples Proposal][rec-tup-proposal] but split off into a separate proposal to reduce the scope of the core Records and Tuples proposal. [#330](https://github.com/tc39/proposal-record-tuple/issues/330). At this point the proposal was to add a new `JSON.parseImmutable` function which would behave just like `JSON.parse` but return a Record or a Tuple, those being the two types proposed by the Records and Tuples Proposal. The Records and Tuples Proposal was withdrawn, requiring a change of scope for this proposal.

<!-- References -->
[rec-tup-proposal]: https://github.com/tc39/proposal-record-tuple
[json-mdn]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON
[object-freeze-mdn]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze
