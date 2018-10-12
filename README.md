# spec-pattern

Implementation of the [Specification Pattern](https://en.wikipedia.org/wiki/Specificationpattern) for JavaScript and TypeScript.

[![Build Status](https://travis-ci.org/thiagodp/spec-pattern.svg?branch=master)](https://travis-ci.org/thiagodp/spec-pattern) [![Greenkeeper badge](https://badges.greenkeeper.io/thiagodp/spec-pattern.svg)](https://greenkeeper.io/)

> Build complex filters and rules easily.

- No external dependencies.
- Fully [tested](tests/index.spec.ts).
- Uses [semantic versioning](https://semver.org). *Forks are welcome!*

## Install

```console
$ npm install spec-pattern --save
```

## Examples

#### A simple Between rule
 ```js
import { Between } from 'spec-pattern';

let rules = new Between( 1, 3 );

console.log( rules.isSatisfiedBy( 2 ) ); // true
```


#### A little more complex Between rule
 ```js
import { Between } from 'spec-pattern';

let rules = new Between( 1, 3 )
    .or( new Between( 6, 9 ) );

console.log( rules.isSatisfiedBy( 2 ) ); // true
console.log( rules.isSatisfiedBy( 7 ) ); // true
console.log( rules.isSatisfiedBy( 5 ) ); // false
```

#### Composing rules
 ```js
import { Between, In, GreaterThan } from 'spec-pattern';

let rules = new Between( 1, 3 )
    .or( new Between( 6, 9 ) )
    .or( new In( [ 11, 25, 31 ] )
    .or( new GreaterThan( 50 ) );

console.log( rules.isSatisfiedBy( 2 ) ); // true
console.log( rules.isSatisfiedBy( 7 ) ); // true
console.log( rules.isSatisfiedBy( 5 ) ); // false
console.log( rules.isSatisfiedBy( 11 ) ); // true
console.log( rules.isSatisfiedBy( 50 ) ); // false
console.log( rules.isSatisfiedBy( 51 ) ); // true
```

#### Not only numbers
```js
import { StartsWith, Contains } from 'spec-pattern';

let rules = new StartsWith( 'Hello' )
    .andNot( new Contains( 'world' ) );

console.log( rules.isSatisfiedBy( 'Hello Bob' ) ); // true
console.log( rules.isSatisfiedBy( 'Hello world' ) ); // false
```
```js
import { LengthBetween, EqualTo } from 'spec-pattern';

let rules = new LengthBetween( 2, 5 )
    .andNot( new EqualTo( 'Hello' ) );

console.log( rules.isSatisfiedBy( '' ) ); // false
console.log( rules.isSatisfiedBy( 'Hi' ) ); // true
console.log( rules.isSatisfiedBy( 'Hello' ) ); // false
console.log( rules.isSatisfiedBy( 'Howdy' ) ); // true
console.log( rules.isSatisfiedBy( 'Hello world' ) ); // false
```

## Available classes

- `EqualTo( value: any )`
- `GreaterThan( value: any )`
- `GreaterThanOrEqualTo( value: any )`
- `LessThan( value: any )`
- `LessThanOrEqualTo( value: any )`
- `Between( min: any, max: any )`
- `In( values: array )`
- `StartsWith( value: string, ignoreCase: boolean = false )`
- `EndsWith( value: string, ignoreCase: boolean = false )`
- `Contains( value: string, ignoreCase: boolean = false )`
- `LengthBetween( min: any, max: any )`
- `Matches( regex: RegExp )`

All these classes extend the abstract class `Composite`, which in turn implements the interface `Spec`:

```typescript
interface Spec< T > {

    isSatisfiedBy( candidate: T ): boolean;

    and( other: Spec< T > ): Spec< T >;

    andNot( other: Spec< T > ): Spec< T >;

    or( other: Spec< T > ): Spec< T >;

    orNot( other: Spec< T > ): Spec< T >;

    not(): Spec< T >;
}
```

## Creating your own class

Creating your own class is **very easy**. Just extends *abstract* class `Composite`, like in the following example. Of course, you can also extend one of the aforementioned classes or implement the interface `Spec` *(but why reinventing the wheel, right?)*.

Let's create a class `DifferentFrom` ...

*...in TypeScript:*
```typescript
import { Composite } from 'spec-pattern';

export class DifferentFrom< T > extends Composite< T > {

    constructor( private value: T ) {
        super();
    }

    isSatisfiedBy( candidate: T ): boolean {
        return this.value != candidate;
    }

    toString(): string {
        return 'different from ' + this.value;
    }
}
```

*...or in JavaScript 6+:*
```js
import { Composite } from 'spec-pattern';

class DifferentFrom extends Composite {

    constructor( value ) {
        this.value = value;
    }

    isSatisfiedBy( candidate ) {
        return this.value != candidate;
    }

    toString() {
        return 'different from ' + this.value;
    }
}
```


*...or in JavaScript 5+:*
```js
var Composite  = require( 'spec-pattern' ).Composite;

function DifferentFrom( value ) {

    Composite.call( this ); // super()

    this.value = value;

    this.isSatisfiedBy = function ( candidate ) {
        return this.value != candidate;
    };

    this.toString = function() {
        return 'different from ' + this.value;
    };
}

DifferentFrom.prototype = Object.create( Composite.prototype );
DifferentFrom.prototype.constructor = DifferentFrom;
```

*That's it!* Just three methods: `constructor`, `isSatisfiedBy`, and `toString()`.

## License

[MIT](LICENSE) © [Thiago Delgado Pinto](https://github.com/thiagodp)
