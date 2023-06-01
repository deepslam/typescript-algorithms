# Collection of JavaScript algorithms and techniques

* Count bits in a number

```javascript
export function countBits(n: number): number {
  return n.toString(2).replace(/0/g, '').length;
}
```

* Anagram finder

```javascript
function anagram(string1: string, string2: string): boolean {
    if (string1.toLowerCase() === string2.toLowerCase()) {
       return true
    } else if (string1.length === string2.length) {

    if (string1.toLowerCase().split("").sort().join() === string2.toLowerCase().split("").sort().join()) {
      return true
    }
  }
  return false;
}

console.log(anagram('abc','bca'))
```
# Debounce

```javascript
type F = (...p: any[]) => any

function debounce(fn: F, t: number): F {
    let timeout;
    return function(...args) {
        if (timeout) {
            clearTimeout(timeout);
        }
        timeout = setTimeout(() => fn(...args), t);
    }
};
```

* Object / Array deep clone

```javascript
function (obj) {
  return JSON.parse(JSON.stringify(obj));
}
```

* Time limited function

Given an asyncronous function fn and a time t in milliseconds, return a new time limited version of the input function.

A time limited function is a function that is identical to the original unless it takes longer than t milliseconds to fullfill. In that case, it will reject with "Time Limit Exceeded".  Note that it should reject with a string, not an Error.

```javascript
/**
 * @param {Function} fn
 * @param {number} t
 * @return {Function}
 */
var timeLimit = function(fn, t) {
    return async function(...args) {
	    return new Promise((resolve, reject) => {
	      setTimeout((e) => reject("Time Limit Exceeded"), t);
	      fn(...args).then(resolve).catch(reject);      
	    });   
    }
};
```

* Currying a function

```javascript
/**
 * @param {Function} fn
 * @return {Function}
 */
var curry = function(fn) {
    return function curried(...args) {
        if (args.length === fn.length) {
            return fn(...args);
        }

        return (...nextArgs) => curried(...args,...nextArgs);
    };
};
```

* Promise pool (with a treshold)

```javascript
/**
 * @param {Function[]} functions
 * @param {number} n
 * @return {Function}
 */
var promisePool = async function(functions, n) {
    return new Promise(resolve => {
        let inProgressCount = 0;
        let functionIndex = 0;
        function helper() {
            if (functionIndex >= functions.length) {
                if (inProgressCount === 0) {
                    resolve();
                }
                return;
            }

            while (inProgressCount < n && functionIndex < functions.length) {
                inProgressCount++;
                const promise = functions[functionIndex]();
                functionIndex++;
                promise.then(() => {
                    inProgressCount--;
                    helper();
                })
            }
        }

        helper();
    })
};
```

* Check instance of object (including primitive types)

```javascript
/**
 * @param {any} obj
 * @param {any} classFunction
 * @return {boolean}
 */
var checkIfInstanceOf = function(obj, classFunction) {
    if (obj === null || obj === undefined || typeof classFunction !== "function") {
        return false;
    }

    let potentialPrototype = Object.getPrototypeOf(obj);
    while (potentialPrototype !== null) {
        if (potentialPrototype === classFunction.prototype) {
            return true;
        }
        potentialPrototype = Object.getPrototypeOf(potentialPrototype);
    }

    return false;
};
```

* Functions composition

```javascript
/**
 * @param {Function[]} functions
 * @return {Function}
 */
var compose = function(functions) {
	return function(x) {
        if (functions.length === 0) return x;
        let result = x;
        functions.reverse().forEach(func => {
            result = func(result);
        })

        return result;
    }
};
```

* Call function just once

```javascript
/**
 * @param {Function} fn
 * @return {Function}
 */
var once = function(fn) {
    let called = false;
    return function(...args){
        if (!called) {
            called = true;
            return fn(...args);            
        }
    }
};
```

* Function memoization

```javascript
/**
 * @param {Function} fn
 */
function memoize(fn) {
    let memoizedValues = {};
    return function(...args) {
        const memoKey = JSON.stringify(args);
        if (memoKey in memoizedValues) {
            return memoizedValues[memoKey];
            
        }
        memoizedValues[memoKey] = fn(...args);
        return memoizedValues[memoKey];
    }
}
```

* Memoization with the map

```typescript
type Fn = (...params: any) => any

function createKeyGenerator() {    
    let count = 0;
    const map = new Map<unknown, number>();
    return function(input: unknown) {
        if (map.has(input)) return map.get(input);
        map.set(input, ++count)
        return count;
    }
}

function memoize(fn: Fn): Fn {
    const keyGenerator = createKeyGenerator();
    const cache = new Map();
    return function(...args) {
        const numbers = args.map(keyGenerator);
        const key = numbers.join(',');
        if (cache.has(key)) {
            return cache.get(key);
        }
        const result = fn(...args);
        cache.set(key, result);
        return result;
    }
}
```

* Throttle a function

```typescript
type F = (...args: any[]) => void

function throttle(fn: F, t: number): F {
    let timeoutInProgress = null;
    let argsToProcess = null;

    const timeoutFunction = () => {
        if (argsToProcess === null) {
            timeoutInProgress = null;
        } else {
            fn(...argsToProcess);
            argsToProcess = null;
            timeoutInProgress = setTimeout(timeoutFunction, t);
        }
    }

    return function throttled(...args) {
        if (timeoutInProgress) {
            argsToProcess = args;
        } else {
            fn(...args);
            timeoutInProgress = setTimeout(timeoutFunction, t);
        }
    }
};
```

* Deep equal

```javascript
/**
 * @param {any} o1
 * @param {any} o2
 * @return {boolean}
 */
function helper(_, value) {
    if (value && typeof value === "object" && !Array.isArray(value))
        return Object.fromEntries(Object.entries(value).sort());
    else
        return value;
}

function areDeeplyEqual(o1: any, o2: any): boolean {
    const stringified1 = JSON.stringify(o1, helper);
    const stringified2 = JSON.stringify(o2, helper);

    return stringified1 === stringified2;
};
```

* JSON stringify without using JSON stringify

```typescript
function jsonStringify(object: any): string {
    if (object === null) {
        return "null";
    }
    if (typeof object === 'object') {
        if (Array.isArray(object)) {
            return "[" + object.map(item => jsonStringify(item)).join(",") + "]"
        } else {
            if (Object.keys(object).length) {
                return "{" + Object.keys(object).map(key => `"${key}":${jsonStringify(object[key])}`).join(",") + "}";
            } 
            
            return "{}";
        }
    }
    if (typeof object === "boolean") {
        return Boolean(object).toString();
    }
    if (typeof object === "number") {
        return object.toString();
    }
    if (typeof object === "string") {
        return `"${object}"`;
    }    

    return object.toString();
};
```

* Chunk array

```javascript
function chunk(arr: any[], size: number): any[][] {
    const chunksCount = Math.ceil(arr.length / size);
    let currentChunk = 0;
    const result: any[][] = [];

    while (currentChunk < chunksCount) {
        result.push(arr.slice(currentChunk*size, (currentChunk+1)*size));
        currentChunk++;
    }

    return result;
};
```

* Array prototype last

```javascript
declare global {
    interface Array<T> {
        last(): T | -1;
    }
}

Array.prototype.last = function() {
    return this[this.length - 1] ?? -1;
};
```

* Flattening an array with optional depth limit

```javascript
type MultiDimensionalArray = (number | MultiDimensionalArray)[];

var flat = function (arr:  MultiDimensionalArray, n: number):  MultiDimensionalArray {
    let result: MultiDimensionalArray = [];
    const getElementsFromArray = (subarr: MultiDimensionalArray, l = 0) => {
        subarr.forEach(item => {
            if (Array.isArray(item) && l > 0) {
                getElementsFromArray(item, l - 1)
            } else {
                result.push(item);
            }
        })
    }
    if (n === 0) return arr;

    getElementsFromArray(arr, n);

    return result;
};
```

* Group By in an array

```javascript
declare global {
    interface Array<T> {
        groupBy(fn: (item: T) => string): Record<string, T[]>
    }
}

Array.prototype.groupBy = function(fn) {
    const result:Record<string, unknown[]> = {};
    this.forEach(item => {
        const index = fn(item);
        if (!result[index]) {
            result[index] = [];
        }
        result[index].push(item);
    });

    return result;
}
```

* Check instanceof object (with checking all prototypes)

```javascript
function checkIfInstanceOf(obj: any, classFunction: any): boolean {
  if (obj === null || obj === undefined || typeof classFunction !== "function")
    return false;

  let currPotentialPrototype = Object.getPrototypeOf(obj);

  while (currPotentialPrototype !== null) {
    if (currPotentialPrototype === (classFunction as Function).prototype)
      return true;
    currPotentialPrototype = Object.getPrototypeOf(currPotentialPrototype);
  }

  return false;
};
```

* Print ranges from the given array

```javascript
function summaryRanges(nums: number[]): string[] {
    const result: string[] = [];
    const addToResult = (range:number[]) => {
        if (typeof range[0] === 'undefined' || typeof range[1] === 'undefined') {
            return;
        }
        const currentRange = range[0] === range[1] ? 
                                range[0].toString() :
                                range.map(item => item.toString()).join('->');

        if (!result.includes(currentRange)) {
            result.push(currentRange);
        }
    }
    let currentRange = [];
    for (let i=0;i<nums.length;i++) {    
        if (typeof currentRange[0] === 'undefined') {
            currentRange[0] = nums[i];
            currentRange[1] = nums[i];
        } else if (nums[i] - currentRange[1] <= 1) {
            currentRange[1] = nums[i];
        } else {
            addToResult(currentRange);
            currentRange = [nums[i], nums[i]];
        }
    }

    addToResult(currentRange);
    return result;
};
```

* Remove duplicates from array

```javascript
function removeDuplicates(nums: number[]): number {
    let prevElement = undefined;
    for (let i=0;i<=nums.length;i++) {
        if (prevElement === nums[i]) {
            nums.splice(i, 1);
            i--;
        }
        prevElement = nums[i];
    }

    return nums.length;
};
```

* Event emitter with TypeScript

```typescript
type Callback = (...args: any[]) => any;
type Subscription = {
    unsubscribe: () => void
}

class EventEmitter {
  events: Record<string, Callback[]> = {};  
  subscribe(eventName: string, callback: Callback): Subscription {
    if (!this.events[eventName]) {
        this.events[eventName] = [];
    }
    this.events[eventName].push(callback);
    return {
      unsubscribe: () => {
          this.events[eventName] = this.events[eventName].filter(fn => fn !== callback);
      }
    };
  }

  emit(eventName: string, args: any[] = []): any {
      const result: any[] = [];
      if (this.events[eventName]) {
          this.events[eventName].forEach(fn => result.push(fn(...args)));
      }

      return result;
  }
}

/**
 * const emitter = new EventEmitter();
 *
 * // Subscribe to the onClick event with onClickCallback
 * function onClickCallback() { return 99 }
 * const sub = emitter.subscribe('onClick', onClickCallback);
 *
 * emitter.emit('onClick'); // [99]
 * sub.unsubscribe(); // undefined
 * emitter.emit('onClick'); // []
 */
```

* Array wrapper (usage of valueOf and toString)

```typescript
class ArrayWrapper {
    #nums;
	constructor(nums: number[]) {
        this.#nums = nums;
    }

	valueOf() {
        return this.#nums.reduce((acc, current) => acc + current, 0);
    }

	toString() {
        return `[${this.#nums.join(',')}]`;
    }
};
```
