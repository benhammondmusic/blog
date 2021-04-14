## Reverse() a Stack with Python.

Having created a **stack** using a linked list in Python (more details in  [my previous blog post](https://blog.benhammond.tech/linked-list-stack-in-python) ), I was excited to expand on the functionality and add a `reverse()` method to this data structure class. Again, as with many of these basic computer science concepts, the idea is easy to understand, but the actual code can be challenging to write, and presents severals "gotchas" along the way that might trip you up. 

## Big Picture

A stack is a collection of data where items are added and removed according to the "LIFO" acronym: _Last In, First Out_. Meaning, the last item that was pushed to the stack will be the first one to be popped off. I think of it like a Pez dispenser.

<iframe src="https://giphy.com/embed/NmovCS9E8pVcY" width="480" height="360" frameBorder="0" class="giphy-embed" allowFullScreen></iframe>

So what would we do if we needed to turn the whole thing around? A simple singly-linked list contains a series of nodes, and those nodes only contain 2 piece of information: the node's `value`, and the location of the `next` node in the list. To reverse, we would have to make all of our `next` pointers point to the previous node. This is where things get tricky however, because we can simply swap them or we would lose track of the `next` node's `next`, and the remainder of the list would just be floating around our computer somewhere, untethered and unreachable. 

## Don't Drop the Rope!

I've used this rock climbing metaphor previously but it's so fitting: when a climber leads and reaches the top of a pitch, they need to be able to get back down to the ground. Their belayer can lower them, but now they've left a carabiner at the top of the pitch (the one that's functioned as their "pulley" and allowed them to be softly lowered to the ground). A common solution to this problem is _rappelling_ down (a.k.a. _abseiling_), instead of belaying; rather than the climber leaving gear at the top, they actually thread their rope through the fixed-bolt into the mountainside, and then are able to use specialized gear to carefully lower themselves down the fixed rope, finally pulling the rope free from it's threaded anchor once they're safely on the ground.

<iframe src="https://giphy.com/embed/3o7TKoLSUWybe97uWA" width="480" height="330" frameBorder="0" class="giphy-embed" allowFullScreen></iframe>

Climbing specifics aside, this switch over from **belay** to **rappel** is extremely dangerous if done incorrectly, and requires careful attention to each simply step. At some point between untying their knot from their harness (on belay) and tying it back on (on rappel), the climber will be unprotected by their safety line, and could easily fall and be killed. For this reason, climbers carry small loops of nylon and extra carabiners, and are able to go "in direct" and have a 2nd, temporary connection to the mountainside that will protect them if they fall. To stretch this metaphor further, a climber will literally tie the end of their safety rope to their harness somewhere, to prevent the untied rope from slipping through the hands or gear and falling to the ground, leaving them stranded on the pitch.

Though far less dramatic, the same applies when reversing a linked-list; you must have a temporary variable to hold the "rope" of your remaining list, while you switch the current node's `next` pointer to reference the `previous` node, and the `next` node's `next` to reference the current node. 

```
            temporary = current.next
            current.next = previous
            previous = current
            current = temporary
```

Notice how at each step, the 2nd variable becomes the first variable of the next line. This also reminds me of how you swap the primitive values of two variables:

```
# task: swap the values of variables a and b

a=1
b=2

# you can't just use a = b or b = a to start because then you'll lose the value stored in the overwritten variable

# instead, use a temporary variable

x = a
a = b
b = x
```

We are doing basically the same thing in our linked-list loop, but each variable is instead a node with a `value` and a pointer to `next`; this loop continues until you reach the end of the linked list; 

## Linked List Reversal: Function vs Class Method

There is one final step left: assign the list's `head` value to point to the last item you found in the list, which would be our `previous`. I followed along with  [a great blog post](https://techsloth.io/crushing-tech-interviews-with-the-linked-list-reversal-pattern)  in working through this concept, and this last step was what tripped me up the most; I couldn't quite figure out why they were sending in the `head` node and returning just the last `previous` node. Some experimenting allowed me to discover the missing step (for my implementation) of explicitly assigning the new `head`. As you can read in the comment's of their post; their particular case was not adding a `reverse()` method to a listed link stack class like mine, instead they were making a function which would reverse a linked list given that list's first node. In that case you'd presumably want the result of the function to be the new "first" node (the `head`) of this newly reversed linked list.

## The complete `reverse()` method

``` python
    # reverse: reverse the linked list
    def reverse(self):

        # start at the top of the stack
        current = self.head

        # initialize
        temporary = None
        previous = None

        # walk through the linked list
        while (current != None):
            
            # don't let go of your rope (the rest of your linked list)!
            temporary = current.next

            # swap the link direction
            current.next = previous

            # attach current to the next nodes "previous"
            previous = current

            # step to what used to be the "next" node; which is stored in temp
            current = temporary

        
        # once reached the end/bottom of the stack
        # it's important to make that bottom item the new head
        self.head = previous
```

Thanks for following along! Hopefully you didn't get too lost on my rock climbing analogies, and hopefully you will do some more research before implementing my rock climbing advice in real life! No warranties or guarantees implied of the performance of your climbing or data structure performance. 

Photo in cover image by <a href="https://unsplash.com/@brookanderson?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Brook Anderson</a> on <a href="https://unsplash.com/s/photos/caribiner?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>
  