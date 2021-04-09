## Linked List Stack in Python

> Part of my "Learn Computer Science by Coding" series; also check out some of my posts on [bubble sort, binary search](https://blog.benhammond.tech/studying-compsci-by-actually-writing-code) and [recursive merge sort](https://blog.benhammond.tech/recursive-merge-sort-in-vanilla-javascript) algorithms using JavaScript.

## Coding in Python instead of JavaScript

We have been completing modules on computer science topics in the course of my General Assembly bootcamp, and I have been making a point to implement some of the most famous data structures and algorithms from scratch, using actual code. This process continues to be super helpful in understanding the concepts at a deeper level, and I'd recommend it to anyone learning (or relearning) these ideas. 

Since we just started a new course unit on Python (which is famous for its ability to work with data), I thought it'd be an interesting challenge to step out of my new JavaScript comfort zone and explore Python while also learning these data structure concepts. I had used Python a few years ago to cobble together my  [GigUploader](https://blog.benhammond.tech/giguploader)  project, but my coding skills in general are so much stronger now that I was excited to see my progress in this language.

### Getting Started

I already had a pretty decent mental model for arrays, linked lists, stacks and queues, from various college courses and several videos. Making the connection between how they work _in theory_ to how they work _in code_ can be quite a challenge however, and I did rely on several resources to finally get this implemented. The first was this tutorial implementing a [linked list stack in Javascript](https://learnersbucket.com/tutorials/data-structures/implement-stack-using-linked-list/) which was super useful as a "cheat sheet" that I needed to translate into Python. They also clearly outlined the methods that a stack offers, which served as my user stories to get the ball rolling:

- Push: Adds the item in the stack at the top.
- Pop: Removes the top item from the stack and returns it.
- Peek: Shows the top item from the stack.
- toArray: Convert the stack to the array.
- size: Returns the size of the stack.
- isEmpty: Returns true if stack is empty, false other wise.
- clear: Clears the stack.

_from [learnersbucket.com](https://learnersbucket.com/tutorials/data-structures/implement-stack-using-linked-list/)_

## Using Classes

Despite covering the idea of classes early on, the concept certainly isn't front and center in my programming brain. Even once we started working in React with the idea of small, self-container reusable object, we touched on the "old way" of using class-based components and then quickly shifted focus to using hooks and functional components. I have even heard that JavaScript classes aren't technically classes, and simply exist to appease programmers migrating in from other class-based object-oriented languages like Java and C. This is somewhat unclear to me since JavaScript is considered "object-oriented", however the objects it's oriented towards are at the _prototypes_ level, rather than the class level.

### Two classes: **Stack** and **Node**

In any case, this low-level concept of a linked list seemed a good opportunity for using classes, and I assumed Python must use them as a true object-oriented language. In object-oriented programming (OOP), you can define a class for each conceptual entity in your program (similar to how we define our models/schemas when working with MongoDB). In a stack implemented using a linked list, there's the **stack** at the top level, and then the **links** of the linked list (usually referred to as 'nodes'). 

### Python Class Idiosyncrasies:  `__init()__` and `self`

This one took some careful copy-pasting 😂, but eventually it became clear what was actually going on: the constructor method (the one that automatically runs once any time a member of a class is instantiated) is called `__init()__` in Python (short for initialization?). In JavaScript we instead call it the `constructor`. 

Also interesting was how we need to explicitly include `self` as an argument within the definition of every class method. `self` seems to function very similarly to JavaScript's `this`, but must be present in those incoming arguments.

## Class Methods

Beyond the actual coding differences, it's been a small challenge to try and maintain a pythonic style: for instance using the word `list` instead of `array`, and naming variables and functions with snake_case and not camelCase (e.g. `to_list()` in Python vs. `toArray()` in JavaScript). Now that I had my "nouns" defined with classes `Node` and `Stack`, it's time to flesh out the "verbs" using class methods.

### `is_empty()`

This was the first method I implemented, as the logic from several other methods would depend on the state of the stack.  It was also pretty simple: check if the current `head` is equal to `None`, and if so it's empty! Note: `None` in Python is similar (but not identical) to the ideas of `undefined` and `null` in JavaScript. It's a _**thing**_ that represents _**nothing**_.

### `push()` and `pop()`

I'll admit I needed to frequently reference the tutorial to get these working; I just had a bit of trouble wrapping my head around the syntax and how `self`, `head`, `next` and `value` were all connected. Here's what I ended up with:

- `self`: the current stack
    - `head`: the "top" of the stack
        - `next`: the pointer that connects the current node to the next one
            - `value`: the data or item that is being stored in each node

The processes for `push()` and `pop()` are very similar, and require care to not break the links of the chain. It actuals reminds me of rock-climbing, when you are switching from secure anchor #1 to secure anchor #2, you must assure #2 is connected before you disconnect from #1. Similarly, when you create a new node, you set its `next` to point to the current head first, and then swap the value of `head` to reference this new node after. 


### `peek()` and `display()`

Also related, these methods allow you to view the first and the entire stack respectively. `peek()` simply returns the `head.value` if the stack isn't empty, while `display()` continues to walk through each node->next->node->next->node and display each node's value as it gets there.

### `size()` and `to_list()`

These two methods both involve keeping track of a variable outside of your node-traverse, and updating that variable as you walk through: the integer `stack_size` for `size()`, and the list `stack_as_list[]` for the `to_list()` method (described as `toArray()` in the JavaScript implementation.

I also needed to reverse the final list before returning from the method; since the items are extracted one-by-one from the last item added and added to the end of the tally list using `append()`, I needed to do a final `stack_as_list.reverse()` to put items in the correct order. Another approach would be to directly place the items into beginning of the tally list using `stack_as_list.insert(0, item)` where `0` specifies the first position (`stack_as_list[0]` since Python lists are  [zero-indexed](https://en.wikipedia.org/wiki/Zero-based_numbering) ). 

### `clear()`

To finish the set of methods listed at the beginning, we'll finish as simply as we began by simply clearing the array. This mimics the behavior of the stack's `__init__()` method, by simply setting the `head` to `None`. I suspect this might require some sort of garbage collection since all those nodes are still in memory, just simply no longer connected to our stack. If anyone has answers please let me know, and I'll update as I learn more!

## Conclusion

Thanks again for reading; I will be attempting some more data structure implementations using Python in future blog posts so stayed tuned. Here are some more of the pages I referenced while learning this material:
-  [Python Classes and Objects](https://openbookproject.net/thinkcs/python/english3e/classes_and_objects_I.html) 
-  [Gist from Dinesh](https://gist.github.com/dineshrajpurohit/0bb67d29a039f85f2f10) 
-  [W3Schools Python Classes](https://www.w3schools.com/python/python_classes.asp) 
-  [Geeks for Geeks](https://www.geeksforgeeks.org/implement-a-stack-using-singly-linked-list/) 




Photo in cover image by <a href="https://unsplash.com/@conti_photos?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Fabrizio Conti</a> on <a href="https://unsplash.com/s/photos/link-rock-climbing?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>
  


