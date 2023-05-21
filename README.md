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
/**
 * @param {Function} fn
 * @param {number} t milliseconds
 * @return {Function}
 */
var debounce = function(fn, t) {
    let timeout;
    return function(...args) {
        clearTimeout(timeout);
        timeout = setTimeout(() => fn(...args), t);
    }
};
```

# Object / Array deep clone

```javascript
function (obj) {
  return JSON.parse(JSON.stringify(obj));
}
```

# Time limited function

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
      fn(...args).then(resolve).catch((e) => {reject(e.toString())});
      setTimeout(() => reject('Time Limit Exceeded'), t)
    })      
  }
};
```

# Currying a function

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

# Promise pool (with a treshold)

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

# Check instance of object (including primitive types)

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
