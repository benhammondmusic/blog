## Recursive Merge Sort in Vanilla JavaScript

Continuing with the theme of my previous post:  [Studying Comp Sci by Actually Coding](https://blog.benhammond.tech/studying-compsci-by-actually-writing-code), where I built a Bubble Sort and iterative Binary Search in Javascript, I decided to attempt another classic compsci algorithm: the Merge Sort. This is also a relatively "easy" sort function in terms of understanding the general concept, but proved plenty challenging for me to implement from scratch. The part that confused me the most after just watching the animated gif demos and reading high-level descriptions wasn't the recursive `sort`, it was actually the subsequent `merge` step and how it specifically functioned. All of the descriptions glossed over that phase as "simply merge the two back together"... well that's not super helpful when you're actually just trying to figure out how to merge two things in the first place! Recursion in programming? **cool**. Recursive terminology in documentation? **uncool**. 

<iframe src="https://giphy.com/embed/RHPxe7KPnb8KaAtBbE" width="480" height="316" frameBorder="0" class="giphy-embed" allowFullScreen></iframe>

I started as a I always do... simply throwing around as many `console.log()` as possible and adding logic step by step to be sure I'm doing what I think I'm doing! One early "gotcha" was that the way this "divide" needs two sub-arrays, as opposed to the way I was doing binary search which technically had three sub-arrays (treating the center element as it's own sub-array). My logic was off as I was defining the "greater than" half sub-array as starting as `middleIndex + 1`, when it really needed to be simply `middleIndex`. To test that my recursive `sort` steps were working, I logged out each step, and then built a fake `merge` step that simply concatenated the two arrays using the spread operators: `let mergedHalves = [...leftHalf, ...rightHalf]`. This fake function was helpful because if I had done everything correct in the first step, the entire `mergeSort` function would break apart the initial array into 1-item sublists, and then rebuild them back in the same order. This was how I discovered my error above, where I was neglecting to included the item at `middleIndex`.

Once I was sure the recursive sort was working fine, I needed to figure out how the merge step was actually working, and then implement it in JavaScript. I tried to find a more detailed guide that I could study step by step, but without seeing someone's actual solution in JavaScript; I finally landed on the [merge sort wikipedia page](https://en.wikipedia.org/wiki/Merge_sort#Top-down_implementation_using_lists)  which had a detailed pseudo-code implementation. This was super helpful (perhaps a little more detailed than I was hoping in terms of challenging myself) and allowed me to create a functioning merge step. If you're curious, I'll detail this merge step below, and not simply gloss over like all of the other write-ups I encountered:
#### Sort Step: `recursiveSort()`
1. break apart your array recursively into two pieces (`leftHalf` and `rightHalf` in my code) until all you have left are 1-item sub-arrays
2. `return` the value from each 1-item array

#### Merge Step: `mergeHalves()` within `recursiveSort()` 
1. confirm there are items in both the `leftHalf` and the `rightHalf`
2. compare the first element from the two halves
3. remove the smaller of the two from its respective half and place in the `mergedArray` which is collecting the ordered items
4. keep looping back to step 1 until one or both of the half arrays are empty
5. if the halves were unequal in length at the start, one will still have some items left in it; one by one removed the first element of that remaining sub array and push onto your `mergedArray

Here is the JavaScript implementation I ended up with: 
```
const merge = (data) => {
  console.log('_______MERGE SORTING_______');

  /*  */
  /* method to run 2nd, AFTER the recursive splitting down to 1 item sub lists */
  /*  */
  const mergeHalves = (leftHalf, rightHalf) => {
    console.log('***MERGING', leftHalf, rightHalf);

    // array to be filled merged items
    let mergedArray = [];

    // keep looping while both arrays still have items
    while (leftHalf.length && rightHalf.length) {

      // compare first items of both halves, remove the smaller one and place in the merged array
      if (leftHalf[0] <= rightHalf[0]) {
        console.log('LEFT IS SMALLER');
        mergedArray.push(leftHalf.shift());
      } else {
        console.log('RIGHT IS SMALLER');
        mergedArray.push(rightHalf.shift());
      }
    }

    // if the two halves were uneven, merge in the remaining items from the longer half
    while (leftHalf.length) {
      console.log('REMAINING ITEMS IN LEFT HALF', leftHalf);
      mergedArray.push(leftHalf.shift());
    }
    while (rightHalf.length) {
      console.log('REMAINING ITEMS IN right HALF', rightHalf);
      mergedArray.push(rightHalf.shift());
    }

    return mergedArray;
  };

  /*  */
  /* recursively break apart until they are 1 item sublists */
  /*  */
  const recursiveSort = (data) => {
    if (data.length <= 1) {
      //   if (data === undefined || data.length <= 1) {
      console.log('sublist has 1 or 0 items');
      return data;
    }
    // find the middle or just left of middle if even number of elements
    let middleIndex = Math.floor(data.length / 2);

    // recursive call on first half (slice has an exclusive ending index)
    let leftHalf = recursiveSort(data.slice(0, middleIndex));

    // recursive call on second half
    let rightHalf = recursiveSort(data.slice(middleIndex));

    // merge sorted left and rights
    let merged = mergeHalves(leftHalf, rightHalf);
    return merged;
  };

  // initial call of recursive fn
  return recursiveSort(data);
};
```

Again, _actually_ writing this code from scratch has done more for my understanding than all of the YouTube and Twitter visualizations I had seen to date. Those were certainly helpful to get a baseline of understanding, but implementing it really solidified each single step and the reasons they are important. Add to that little addictive "spark" you get when something you're building finally comes alive and you've got a recipe for success! Speaking of instant gratification, I added in a small command to my code to allow viewing of over 100 items in a `console.log` array:
```
const util = require('util');
console.log(util.inspect(dataArray, { maxArrayLength: null }));
```
 Let me know if you've also implemented any comp sci concepts in your favorite language, and what you think I should attempt next; maybe I'll actually do one in Python finally!

![merge sort.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1617723874049/PxJBgGrq0.gif)
