# Collection of typescript algorithms

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
