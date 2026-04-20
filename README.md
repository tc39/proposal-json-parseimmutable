# JSON.parseImmutable Proposal

## Status

Stage 2

**Champions:**

- Nicolò Ribaudo (Igalia)
- Ashley Claymore (Bloomberg)
- Peter Klecha (Bloomberg)

## Overview

This proposal identifies a need in ECMAScript for a function which parses [JSON][json-mdn] strings but returns deeply immutable objects. The current state of the proposal is to add a `JSON.parseImmutable` function to the specification which takes a string and optionally a reviver function, and returns an object which is deeply frozen, i.e., an object which is configured as if [`Object.freeze`][object-freeze-mdn] had been recursively called on it.

```javascript
const obj = JSON.parseImmutable('{ "one": { "two": 3 } }');
assert(Object.isFrozen(obj));
assert(Object.isFrozen(obj.one));
```

A similar result can now only be achieved by use of a reviver:

```javascript
JSON.parse(data, (key, value) => {
  if (typeof value === 'object' && value !== null) {
      return Object.freeze(value);
  }
  return value;
});
```

## History

This proposal was originally part of the [Records and Tuples Proposal][rec-tup-proposal] but split off into a separate proposal to reduce the scope of the core Records and Tuples proposal. [#330](https://github.com/tc39/proposal-record-tuple/issues/330). At this point the proposal was that the `JSON.parseImmutable` function would return a Record or a Tuple, those being the two types proposed by the Records and Tuples Proposal. The Records and Tuples Proposal was withdrawn, requiring a change of scope for this proposal.

<!-- References -->
[rec-tup-proposal]: https://github.com/tc39/proposal-record-tuple
[json-mdn]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON
[object-freeze-mdn]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze
