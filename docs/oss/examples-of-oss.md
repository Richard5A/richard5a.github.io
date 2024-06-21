# Examples

At the moment this is not finished. I want to add more examples in the future.

## [is-odd](https://www.npmjs.com/package/is-odd)

For example this well known NPM package. It's an unbelievable simple and
often used package. At the moment I'm looking at the statistics it has 423.411 weekly downloads. I know - the source is
just one file:

```javascript
'use strict';


const isNumber = require('is-number');


module.exports = function isOdd(value) {

    const n = Math.abs(value);

    if (!isNumber(n)) {

        throw new TypeError('expected a number');

    }

    if (!Number.isInteger(n)) {

        throw new Error('expected an integer');

    }

    if (!Number.isSafeInteger(n)) {

        throw new Error('value exceeds maximum safe integer');

    }

    return (n % 2) === 1;

};
```

but it is a great package to not have to reinvent the wheel. The functionality is simple but also includes very
important
exceptions and checks you would have forgotten yourself thus leading to bugs and security issues.

What I find fascinating in short:

- It is so simple but still so often used. _(How many hours of work did it save?)_
- The Author (Jon Schlinkert) - a simple person like you and me - has created this package and shared it with the world.
  This shows that everyone can get a hero of a programmer that otherwise would have spent hours of work on the same
  thing.

## [Android](https://source.android.com/)

If you're a poor Apple already bitten, you might not know that Android is open source.

What I find fascinating in short:

- The whole operating system is open source. In the world of operating systems, this is a big exception.
- It is modern and still developed heavily. Despite the fact that it is open source, Google builds a lot of services
  around it that are proprietary.
- There is no "the Android". There are lots of different remixes of Android. Companies like Samsung, Huawei, Xiaomi,
  Nothing and many more are using Android as a base for their own operating system. This is possible because Android is
  open source.
