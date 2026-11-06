# Objects, Prototypes & Classes

JavaScript's object system is **prototype-based**. Classes are syntax over prototypes, not a separate inheritance model. Understanding the prototype chain explains method lookup, `instanceof`, and most "weird" object behavior.

---

## 1. Creating Objects

```js
// Object literal
const user = {
  name: "Ada",
  age: 36,
  greet() { return `Hi, ${this.name}`; },
};

// Constructor function (legacy pattern)
function User(name, age) {
  this.name = name;
  this.age = age;
}
User.prototype.greet = function () {
  return `Hi, ${this.name}`;
};

// Object.create (explicit prototype)
const admin = Object.create(user);
admin.role = "admin";

// Class syntax (modern)
class Member {
  constructor(name) { this.name = name; }
  greet() { return `Hi, ${this.name}`; }
}
```

---

## 2. Property Access and Descriptors

```js
const config = { theme: "dark" };

config.theme;          // "dark"
config["theme"];       // "dark"
config.missing;        // undefined

// Safe access
config?.nested?.value; // undefined

// Check existence
"theme" in config;                   // true
Object.hasOwn(config, "theme");      // true (preferred over hasOwnProperty)
```

### Property descriptors

```js
const obj = {};

Object.defineProperty(obj, "id", {
  value: 42,
  writable: false,
  enumerable: false,
  configurable: false,
});

obj.id = 99;        // silently fails (throws in strict mode)
console.log(obj.id); // 42
```

```js
Object.getOwnPropertyDescriptor(obj, "id");
// { value: 42, writable: false, enumerable: false, configurable: false }
```

| Flag | Default in literals | Meaning |
| :--- | :--- | :--- |
| `writable` | `true` | Can be reassigned |
| `enumerable` | `true` | Appears in `for...in` / `Object.keys` |
| `configurable` | `true` | Can be deleted or redefined |

---

## 3. The Prototype Chain

Every object has an internal `[[Prototype]]` link. Property lookup walks this chain until it finds the property or reaches `null`.

```js
const animal = {
  eats: true,
  walk() { console.log("walking"); },
};

const rabbit = Object.create(animal);
rabbit.jumps = true;

rabbit.eats;   // true (inherited)
rabbit.walk(); // "walking" (inherited)
rabbit.jumps;  // true (own)

Object.getPrototypeOf(rabbit) === animal; // true
```

```
rabbit ──[[Prototype]]──▶ animal ──[[Prototype]]──▶ Object.prototype ──▶ null
```

```js
// Own vs inherited
Object.keys(rabbit);            // ["jumps"]
for (const key in rabbit) {}    // "jumps", "eats", "walk"
Object.hasOwn(rabbit, "eats");  // false
```

> **Gotcha:** Never use `for...in` to iterate an object's own data without a `hasOwn` guard. It includes inherited enumerable properties.

---

## 4. Classes

```js
class Animal {
  #sound = "..."; // private field

  constructor(name) {
    this.name = name;
  }

  speak() {
    return `${this.name} says ${this.#sound}`;
  }

  get description() {
    return `${this.constructor.name}: ${this.name}`;
  }

  static create(name) {
    return new this(name);
  }
}

class Dog extends Animal {
  #sound = "Woof";

  constructor(name, breed) {
    super(name);
    this.breed = breed;
  }

  speak() {
    return `${super.speak()} (${this.breed})`;
  }
}

const rex = new Dog("Rex", "Labrador");
rex.speak();          // "Rex says Woof (Labrador)"
rex.description;      // "Dog: Rex"
rex instanceof Dog;   // true
rex instanceof Animal;// true
```

### Class fields and private members

```js
class Counter {
  count = 0;          // public instance field
  static instances = 0; // public static field
  #secret = "hidden"; // private field

  increment() {
    this.count++;
    return this;
  }

  static {
    // static initialization block
    console.log("Counter loaded");
  }
}
```

> **Important:** Class bodies run in strict mode. Methods are non-enumerable and live on `prototype`. Class declarations are **not hoisted** like function declarations (they are in the TDZ).

---

## 5. Inheritance vs Composition

Inheritance is not always the answer. Prefer composition when behavior is shared across unrelated types.

```js
// Inheritance: "is-a"
class Admin extends User {}

// Composition: "has-a" / "can-do"
const canLog = (state) => ({
  log() { console.log(state.name); },
});

const canSerialize = (state) => ({
  toJSON() { return { ...state }; },
});

const createUser = (name) => {
  const state = { name };
  return { ...state, ...canLog(state), ...canSerialize(state) };
};
```

> **Rule of thumb:** Use inheritance for genuine taxonomic relationships. Use composition for capabilities. Deep inheritance trees become fragile.

---

## 6. `new`, `instanceof`, and `constructor`

```js
function Person(name) {
  this.name = name;
}
Person.prototype.greet = function () { return this.name; };

const ada = new Person("Ada");
```

What `new` does:

1. Creates a new empty object.
2. Links its `[[Prototype]]` to `Person.prototype`.
3. Calls `Person` with `this` bound to the new object.
4. Returns the object (unless the constructor returns another object).

```js
ada.constructor === Person;     // true (inherited)
ada instanceof Person;          // true
Person.prototype.isPrototypeOf(ada); // true
```

> **Gotcha:** `instanceof` checks the prototype chain, not the class name. It fails across different realms (iframes) and after `Symbol.hasInstance` overrides.

---

## 7. Copying Objects

### Shallow copy

```js
const original = { name: "Ada", address: { city: "London" } };

const shallow1 = { ...original };
const shallow2 = Object.assign({}, original);

shallow1.address.city = "Paris";
console.log(original.address.city); // "Paris" — nested object is shared
```

### Deep copy

```js
// Structured clone (modern, handles Maps, Sets, Dates, ArrayBuffers)
const deep = structuredClone(original);

// JSON trick (loses functions, undefined, Dates, Maps, Sets, prototypes)
const jsonCopy = JSON.parse(JSON.stringify(original));
```

> **Prefer `structuredClone`.** It handles cycles and more types. It still cannot clone functions or DOM nodes.

---

## 8. Immutability

```js
// Freeze is shallow
const frozen = Object.freeze({ name: "Ada", tags: ["a"] });
frozen.name = "Grace";     // fails silently / throws in strict mode
frozen.tags.push("b");     // allowed — nested array is not frozen

// Update immutably
const user = { name: "Ada", age: 36 };
const older = { ...user, age: 37 };

const list = [1, 2, 3];
const added = [...list, 4];
const removed = list.filter((n) => n !== 2);
const updated = list.map((n) => (n === 2 ? 20 : n));
```

> **Why it matters:** Immutable updates make state changes predictable and enable cheap reference equality checks (`prev === next`) in UI libraries.

---

## 9. Useful Object Utilities

```js
Object.keys(obj);          // own enumerable string keys
Object.values(obj);        // own enumerable values
Object.entries(obj);       // [key, value] pairs
Object.fromEntries(pairs); // build an object from pairs
Object.assign(target, ...sources);

// Merge deeply-ish
const merged = {
  ...defaults,
  ...options,
  nested: { ...defaults.nested, ...options.nested },
};
```

```js
// Convert between Map and Object
const map = new Map(Object.entries({ a: 1, b: 2 }));
const obj = Object.fromEntries(map);
```

---

## 10. JSON

```js
const json = JSON.stringify({ name: "Ada", age: 36 }, null, 2);
const data = JSON.parse(json);
```

- Functions, `undefined`, and symbols are omitted.
- Dates become ISO strings.
- `NaN` and `Infinity` become `null`.
- Circular references throw.

```js
// Custom serialization
const user = {
  name: "Ada",
  password: "secret",
  toJSON() {
    return { name: this.name }; // password omitted
  },
};
```

> **Security:** Never `eval` or `new Function` JSON. `JSON.parse` is safe; executable evaluation is not.

---

## ✅ Checkpoint

- [ ] What is the difference between an object's own property and an inherited one?
- [ ] How does the prototype chain resolve a method call?
- [ ] What does `new` actually do?
- [ ] Why is `Object.freeze` shallow?
- [ ] When should you prefer composition over inheritance?
- [ ] What does `structuredClone` handle that `JSON.parse(JSON.stringify())` does not?
