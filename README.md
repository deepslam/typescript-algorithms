# Collection of typescript algorithms

* Count bits in a number

```javascript
export function countBits(n: number): number {
  return n.toString(2).replace(/0/g, '').length;
}
```

* Anagram finder

```javascript
function anagram(string1, string2) {
    if (string1.toLowerCase() === string2.toLowerCase()) {
       return 'anagram'
    } else if (string1.length === string2.length) {

    if (string1.toLowerCase().split("").sort().join() === string2.toLowerCase().split("").sort().join()) {
      return 'anagram'
    }
  } else { return 'not a anagram' }
}

console.log(anagram('abc','bca'))
```
