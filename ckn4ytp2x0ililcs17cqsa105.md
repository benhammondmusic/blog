## Studying CompSci by Actually Writing Code

> Often the best way to learn isn't by watching a JavaScript influencer or by reading theory in a decades old book. It is simply using the tools you have to build the thing you want, and filling in the knowledge gaps as you go!

In the computer science courses I took during college (go McGill!), the overwhelming focus was on _theory_, while practical applications were few and far between. This is understandable in terms of a classical University approach, however it was often frustrating feeling like I had learned all this theory yet had nothing tangible I could really show for it. To the contrary, GA (the bootcamp I am currently attending) jumps right in with JavaScript and only after months of creating actual, deployed full stack web apps are we beginning to touch on basic computer science concepts like data structures (e.g. linked lists), and algorithms (e.g. merge sort, bubble sort, binary search). These are provided in the form of extra modules to be completed independently, separate from actual course work. This is surely due to the fact that modern web development handles many of these low-level concepts automatically (dynamically adding to arrays, build in search methods, etc), and a developer could go their entire career never facing these questions outside of technical interviews. Like most things, the perfect balance of these two extremes will depend on the individual student, and their goals.

There are many ways of learning coding concepts: watching endless YouTube videos, asking a mentor or instructor how to do it, sifting through StackOverflow threads, and many more. However, in my experience the most productive way to truly absorb something is to jump in and start writing code. For me personally, this means firing up VSCode and clicking away until:

1. _it actually works_
2. _I actually understand why_

I am personally not a fan of using CodePen or similar as a sandboxed coding environment; it seems too far removed from my actual development process and I'd rather have my intellisense, my extensions, and my git and GitHub integration to track and record my progress. That being said, if you are still pretty new, CodePen is an amazing tool to easily whip up some code and start working. I think once we, as devs, have our systems and workflows in place, we forget just how difficult it was to even enter the space and get everything configured properly.

After watching the video tutorials on sorting and searching algorithms, I began implementing my own versions. I made some decisions early on to get started:
- I would use JavaScript since that is the language I'm currently most experienced in. For my next data structure / algorithm implementation, I may use Python instead for the hands-on experience of this other language I'm learning.
- I would use VSCode as my editor, which provides lots of guidance on syntax, formatting, etc. but without making it feel like I was just completing someone else's tutorial.
- I would execute my JS files using Node locally, and specifically run the programs with `nodemon` which is a great way to reflect saved changes without having to kill your process and restart every time you make a change. Since this code exercise was for me alone, having a fancy GUI via HTML/React wasn't important, at least to start. I would love to implement my own visualization of the sorting and searching process myself, as those have been helpful tools for me in this process.

<iframe src="https://giphy.com/embed/KETFJXwXzMOeGFUvPT" width="480" height="270" frameBorder="0" class="giphy-embed" allowFullScreen></iframe>

## Bubble Sort

Bubble Sort has a fun name, a pretty approachable implantation, and a pretty poor execution speed compared to some sorting alternative. The `Big(O) time complexity` of Bubble Sort is `O(n²)`, which means that as the size of of the values to sort increases, the processing time will increase **exponentially**. You don't have to be a math major to understand that's pretty bad. However, it's a great place to start because it's fairly easy to understand exactly what is happening in a bubble sort:
- start at the beginning of your collection of unsorted items
- compare the 1st and the 2nd item; swap them if needed so that they are in order
- repeat this 1 to 1 comparison with the now 2nd and 3rd items, the now 3rd and 4th items, etc until you reach the end of your collection
- loop back ALL the way to the beginning of your collection, and sift through again, swapping adjacent elements when they are out of order
- eventually you will be able to pass all the way through your list without doing any neighbor swapping, at that point your collection is sorted!

For my implementation, I used nested loops, which should be an immediate flag that this program is not going to be very efficient. The _inner_ loop is a `for` loop that walks through the list and makes the neighbor comparisons. The _outer_ loop is a `while()` loop, and keeps the inner `for` running over and over until the array is finally sorted. I am using a boolean flag to keep track of whether a swap was executed on each pass; the flag resets on each pass through, and gets triggered when any items were re-ordered:

```
// implementation of a bubble sort in JS

const bubble = (data) => {
  // boolean to keep track if a swap was needed. if not, the array is sorted
  let didSwap = true;

  // keep looping the bubbling swaps until an entire pass  with no swaps
  while (didSwap) {

    // reset flag on each new bubble pass
    didSwap = false;

    // iterate inner loop over array, avoiding out of range errors
    for (let i = 1; i < data.length; i++) {
      let a = data[i - 1];
      let b = data[i];

      // swap pair if they're not in order
      // send "didSwap" flag to outer loop
      if (a > b) {
        data[i - 1] = b;
        data[i] = a;
        didSwap = true;
      }
    }
  }
 // once it's sorted
  return data;
};

exports.bubble = bubble;
// NOTE: I'm exporting this function, so that it can be used in my other JS programs.
// It is included in `scripts.js` using `const sort = require('./sort');` and then instantiated with `data = sort.bubble(data);`
```

It was extremely rewarding to actually watch my `console.log`s start spitting out sorted data! Yes, the bubble sort is so "bad" that even Obama took a dig at it, but it felt great to see something I had just made from scratch actually do what it was supposed to do, rather than just read about it theoretically or watch yet another sorting algorithm visualization. So great in fact, that I was motivated to now **search** this sorted array and see if I could implement one of the more useful search algorithms.

%[https://www.youtube.com/watch?v=k4RRi_ntQc8]


## Binary Search

Unlike bubble sort, a binary search algorithm is actually pretty close to how you might look something up in real life; this is the basic procedure:
- start in the middle of your **sorted** data set, and see if you got lucky and that middle value happens to be the one you're looking for. In real like this might be trying to find where you had left off reading in a story. The arbitrary middle page of the book probably isn't exactly it, so...
- rule out either the first half of the data set or the second half, based upon whether your desired search value comes before or after based on it's sorting. In the case of our neglected novel, if you've already read the passage on this center page, you know you need to look in the back half of the book. If you haven't yet read the center page, you know you left out in the earlier half of the book.
- perform the same "divide and conquer" method on the remaining chunk from the data set, and keep halving the searched data each time until you reach the exact spot that should contain your searched element. 
- At a certain point you'll either find the last thing you read, or perhaps realize that the "left-off" spot doesn't exist, perhaps because you completed the book or never started reading it at all!

<iframe src="https://giphy.com/embed/JUh0yTz4h931K" width="480" height="322" frameBorder="0" class="giphy-embed" allowFullScreen></iframe>

This algorithm is a perfect candidate to implement using **recursion**, however I actually decided to go with an iterative approach instead (even though using recursion always make me feel clever and like some `l33t` hacker). I ultimately chose iterative over recursive because the implementation seemed cleaner, easier to follow, and importantly because I could step through the process simply by changing the _indexes_, rather than recursively passing actual subsets of the data using `slice()` which is fairly memory intensive. I'm sure there is a recursive implementation that also uses dynamic `start` and `end` index markers rather then recursing entire sub-arrays; I will attempt this at a later date and investigate the trade offs. Here is my iterative implementation:

```
const binary = (searchItem, sortedArray) => {
  // start with the entire array
  let startIdx = 0;
  let endIdx = sortedArray.length - 1;

  // loop until you pinpoint a single item (or find a match below)
  while (startIdx <= endIdx) {

    // find the center slot
    let middleIdx = Math.floor((endIdx + startIdx) / 2);
    let centerItem = sortedArray[middleIdx];

    // if that's your search item then great! return that index
    // otherwise restrict your search to the remaining half the smaller or larger than the middle point
    if (sortedArray[middleIdx] === searchItem) return middleIdx;
    else if (searchItem < centerItem) {
      endIdx = middleIdx - 1;
    } else if (searchItem > centerItem) {
      startIdx = middleIdx + 1;
    }
  }
  // if program exits the while loop and gets here, that means the search got to the location that should have held the searchItem
  // return -1 to indicate value isn't present in array
  return -1;
};

exports.binary = binary;
```

As much as I hope you enjoyed reading this, I'd love even more to hear from you if you have gone ahead and implemented any classic data structures and algorithms in your language of choice! Please let me know in the comments if you have, and any things you learned or suggestions you have for me in my learning process. Next step: **merge sort** using **Python**


![bubble sort binary search.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1617660301584/uzU3kRnuG.gif)

