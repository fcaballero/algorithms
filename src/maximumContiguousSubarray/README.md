# Maximum Contiguous Subarray algorithm implementation using TypeScript / JavaScript

> The maximum subarray problem is one of the nicest examples of dynamic programming application.

> In this lesson we cover an example of how this problem might be presented and what your chain of thought should be to tackle this problem efficiently.


```ts
`
[-2, 1, 3, 4, -1, 2, 1, -5, 4]
          [4, -1, 2, 1]         with the sum 6
`
```
The problem statement:
* Given an array of `n` numbers return a contiguous subarray that has the largest sum.
* Consider an example array
* The subarray with the largest sum for this example would be
* There are TWO key observations

```ts
`
Observation 1:
-------------
 * maxInc(i) = maxInc(i - 1) + val(i)
 * BETTER maxInc(i) = maxInc(i - 1) < 0 ? val(i) : maxInc(i - 1) + val(i)
`
```
* The maximum subarray sum for 0 - to - i when an element at i is included can be represented as
* Of course if the previous max is less than zero then there is no point in including that so a better formula would be one that checks for that.


```ts
`
Observation 2:
-------------
 * max(i) = maxInc(i) > max(i - 1) ? maxInc(i) : max(i - 1)
`
```
* The second observation is that the max sum for the subarray 0 - to - i irrespective of if the ith item is included or not can be represented as, the max of when i is included or the maximum that might exist at i-1, whichever is bigger.

```ts
`
Combining
---------
maxInc(i) = maxInc(i - 1) > 0 ? maxInc(i - 1) + val(i) : val(i)
max(i) = maxInc(i) > max(i - 1) ? maxInc(i) : max(i - 1)
`
```
* Combining these observations we have the following two neatly written down relations.


```ts
`
For max(i) we need:
* maxInc( i - 1)
* max(i - 1)
`
```
* Notice that uptill index (i) we can know the answer "max(i)" immediately if we have
* So the answer uptill 0 to i only depends on values that appear earlier in the array.
* When a problem can be easily solved *given* someone gives us the pre-solved answer to a subset of the problem, the problem solving approach is called dynamic programming.


```ts
`
ELEMENT | maxInc |  max
--------|--------|-----
   -2   |   -2   |  -2
    1   |    1   |   1
   -3   |   -2   |   1
    4   |    4   |   4
   -1   |    3   |   4
    2   |    5   |   5
    1   |    6   |   6
   -5   |   -1   |   6
    4   |    5   |   6

So the sum of the maximum subarray for the given input is 6.
`
```

* Lets solve the example array by hand with a simple table
* At element -2, the max including it would be -2, so the max we can hope to achieve is -2, 
* At element  1, the max including it would be 1, as there is no point in including the previous -2. So the max is 1, 
* At element -3, the max including it would be -2, as we should include the previous maxInc of 1. However since this is less than the previous max, so we wouldn't includ `maxInc` for the net max. 
* Lets just fill out the table a bit faster. You basically just follow the two formulas for `maxInc` and `max` at each step. 

***Select the table***
* Given a table like this which we can store in O(n) since its just two additional arrays we
could easily derive the subset array that is giving this answer.

```ts
`
maxStartIndex = i when val(i) => max
maxEndIndex = i when maxInc => max
`
```
* However we can do better by simply observing that
* the subset array would *start* at index i if the val(i) gets copied to the "max" section
* the subset array would expand to include index i if maxInc gets copied to the "max" section

We now have a firm understanding of the problem and we can code it up as a simple iteration from 0 to n.

```ts
/**
 * Given an array of `n` numbers
 * return a contiguous subarray that has the largest sum
 */
export function maximumSubarray(array: number[]): number[] {
  if (!array.length) {
    return [];
  }

  let maxInc = array[0];
  let max = array[0];
  let maxStartIndex = 0;
  let maxEndIndex = 0;

  for (let i = 1; i < array.length; i += 1) {
    const val = array[i];

    maxInc = Math.max(maxInc + val, val);
    max = Math.max(max, maxInc);

    if (val === max) {
      maxStartIndex = i;
    }
    if (maxInc === max) {
      maxEndIndex = i;
    }
  }

  return array.slice(maxStartIndex, maxEndIndex + 1);
}
```
If we run a simple example, you can see that it works as expected.

```ts
console.log(maximumSubarray([-2, 1, -3, 4, -1, 2, 1, -5, 4]));
```

Since this algorithm only does one pass through the input array, it has time complexity O(n).
