# Collection of typescript algorithms

1. Count bit in a number

```javascript
export function countBits(n: number): number {
  return n.toString(2).replace(/0/g, '').length;
}
```
