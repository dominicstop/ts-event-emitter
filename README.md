# `ts-event-emitter`

(yet another) event emitter written in typescript 😔

<br>

## Installation

```sh
# install via npm...
npm install @dominicstop/ts-event-emitter

# or install via yarn.
yarn add @dominicstop/ts-event-emitter
```

<br>

## Usage

### Basic Usage

```typescript
type EventKeys = 'Foo' | 'Baz';

export const emitter: TSEventEmitter<EventKeys, {
  Foo: string;
  Baz: { age: number } | null;
}> = new TSEventEmitter();

emitter.once('Foo', (event) => { console.log(event) });
emitter.emit('Foo', 'hello world');

emitter.once('Baz', (event) => { console.log(event?.age) });
emitter.emit('Baz', { age: 23 });
```



### In-Depth Usage

```typescript
// 1. Create a string enum...
enum Events { Foo = 'Foo', Bar = 'Bar', Baz = 'Baz' };

// 1.1. ...or alt., a union of strings (whichever you prefer).
type EventKeys = 'Foo' | 'Bar' | 'Baz';

// 2. Create the emitter + event map...
// * For each key in the event enum, define the type of the 
//    `event` argument that the listener will receive.
// * As mentioned in 1.1. you can also use the `EventKeys` union 
//   in place of the `Events` enum.
export const emitter: TSEventEmitter<EventKeys, {

  // 2.1. For the event `TestEnum.Foo`, this will be its event object.
  Foo: { name: string },

  // 2.2. If you don't want to pass an event argument, 
  //      then set it to `null` or `undefined`.
  // * This means the event listener won't receive any arguments
  //   and you can't pass a data argument to `emit` (see step 4.2.)
  Bar: null, 

  // 2.3. or alt., you can make the event param "optional".
  Baz: null | { age: number }
}> = new TSEventEmitter();

// 3.1. `event` arg. will be inferred as `(event: { name: string }) => void`
emitter.once('Foo', (event) => { console.log(event.name) });

// 3.2.`event` arg. will be inferred as `() => void`
// * Note: If you try to add an event param, TS produces an error.
emitter.once('Bar', () => { /** no-op */ });

// 3.3. `event` will be inferred as 
//      `(event: { age: number } | null) => void`.
// * As such, we need to use the optional chaining operator to access `event`.
emitter.once(Events.Baz, (event) => { console.log(event?.age) });
   
// 4. Next, lets broadcast some events.
emitter.emit(Events.Foo, { name: 'd'});
emitter.emit('Baz', { age: 1 });
 
// 4.1. Note: You can't pass a data argument with the 'Bar' event
// because in step 2.2., the event object is defined as `null`.
emitter.emit('Bar');
 
// 4.2. We must explicitly pass a null/undefined value for the data
// argument since it can be optional (e.g. step 2.3.)
emitter.emit('Baz', null);
```



## Documentation

### `TSEvenEmitter`

| Property/Method                                              | Description |
| ------------------------------------------------------------ | ----------- |
| 🔤 `addListener`<br/><br/>⚛️ `(eventKey, listener) => { unsubscribe: () => void }` |             |
| 🔤 `removeListener`<br/><br/>⚛️ `(eventKey, listenerToRemove) => void` |             |
| 🔤 `once`<br/><br/>⚛️ `(eventKey, listener) => void`           |             |
| 🔤 `removeAllListeners`<br/><br/>⚛️ `() => void`               |             |
| 🔤 `emit`<br/><br/>⚛️ `(eventKey, data) => void`               |             |

