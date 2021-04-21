## Build a Hash Map in Python

Until last night I really didn't understand hash tables. I've watched videos and read about them computer science textbooks, but some part of my brain always said "this is confusing and not something I really need to understand.... too many words ... too many weird terms.... ignore for now". I’m pretty sure I vaguely (and wrongly) thought the items themselves were split apart into chunks and somehow distributed throughout a structure. So I decided it was finally time to dig in and figure it out, and as in my previous posts in this series [studying computer science concepts by coding](https://blog.benhammond.tech/series/comp-sci-coding), the best way to learn it to **actually write some code**. This blog is **not** intended to help you have your own hashmap epiphany; instead it will serve as a way for me to document my thought process and code implementation in hopes it might assist both of us in learning. 

# Some Terminology

Understanding a new concept is often frustrating as the resources you reference might include unfamiliar terminology. Even worse are the circular definitions where they “explain” one term you don’t know by using 3 other terms you also have never heard of. Here is a brief run down of the key elements of a hash map and how they relate to one another:

- **Hash (Hash Function)**: a process of converting an item (or _value_) into a _key_ (or location index) in a predictable way; in this case determining a table location based on some property of the item that needs to be stored there
- **Hash Map**: a data structure that stores items efficiently by using a **hash** function to determine items locations
- **Hash Table**: an implementation of a **Hash Map** that specifically uses a table-like structure (as opposed to another type of data structure)
- **Collision**: when your **Hash** function assigns the same location to multiple items
- **Probing**: one way of resolving a collision: iterate through the rest of your table (looping around to the beginning if needed) until you find a new, open location for the item that caused the collision
- **Chaining**: another way of resolving a collision; insert the new colliding item into the location along with the existing item(s), and chain them together using a linked list or similar sub-structure

# Getting Started

I personally start these computer science coding exercises the same way I start my web application projects: by writing my **user stories**. What exactly will I, as a user of this data structure, want it to do? Once I have these stories in place, I start to determine what **classes** I might use to best represent the entities involved. Finally, it's a matter of creating **methods** for the relevant *classes* that will allow me, as a user, to interact with this object. The final step is creating a test program that can instantiate this class and test its functionality by walking through the execution of each user story. I look forward to learning more about Jest, PyTest, and other similar automated testing tools to improve this part of my process.

## User Stories

These are the basic things someone would want a hash map to do:
- accept a value and store it using a hash function
- inform you whether a value is present in the map or not
- remove a particular value from the map (if it is present)

## Classes

The classes I used in my implementation were pretty limited:
- `Hash_Map()`
- `Stack()` - in a [previous blog post](https://blog.benhammond.tech/linked-list-stack-in-python), I had created a **stack** structure utilizing a linked list. It needed some updates, but worked well for resolving collisions using **chaining**
- `Node` - I had already created this class when creating the [stack with linked list](https://blog.benhammond.tech/linked-list-stack-in-python) implementation, and didn't even need to touch it for this exercise. Modularity!

## Functionality

### Brainstorming
I find it helpful to sketch out all of the functionality I can think of that's needed to realize each user story. Sometimes it's 1-to-1, but often a user stories might contain several steps that can be broken down. In my case, all of these _functions_ will actually be _methods_ since they will exist as properties of the classes I defined above. Some of these **class methods** will be _public_ and accessible directly by the user, while others will be _private_ and simply accessed internally by other **class methods**. To start, I often just create empty functions (or methods if I know which class they will belong in) and simply have the function log its own name:
``` python
def hash(self, value):
     # placeholder method - but this will eventually do something cool!
     print(f'Hash')
     return
```
Once I start to flush out those functions, I'll write the steps out in `#comments` to be sure my logic makes sense. Another step to take is to log out any arguments the function with some descriptive text: `print(f'Hash(): hashing {value}"). Once these are logging properly, it's easy to start using real data in your tests, and you can more quickly isolate any errors as being in the business logic rather than the routing or setup.

# Class Methods of Hash_Map()

## Public Methods (called by the user in our `test.py` or any implementation program)
- `insert()` - **User Story #1**. Accepts an item, hashes to calculate the key, and places the item in the structure (in my code, a list within `Hash_Map` called referenced with `self.table`)
- `search()` - **User Story #2**. Accepts an item, and returns a Boolean (`True` or `False`) indicating whether the item was found in the hash map.
- `remove()` - **User Story #3**. Accepts an item, removes and returns the item if found in the hash map, otherwise returns `False`

## Internal Methods (called from other methods within the same instantiated class)
- `get_hash()` - take an item and calculate a _key_ or table index based on the item's _value(s)_.
> Python Note: do not name this method `hash()`; this took me a while to solve, as apparently `hash` is already a keyword in Python and was causing some very strange errors.
- `__str__()` - this is a special method in Python, referred to as a "dunder" method (`d`ouble-`under`scores), and will allow you to add a customized response when your object is converted to a string (using `print(object)` or `str(object)` in the test code for example). Normal Python behavior for objects of instantiated classes would be to return a memory address, but we would like to return a formatted string so we can view our beautiful new hash map! 

# Class Methods of Stack()

Since our `Hash_Map` class will actually rely on inner linked lists (to resolve collisions), we will load up a previously written [stack with a linked list](https://blog.benhammond.tech/linked-list-stack-in-python) module using `from linked_list_stack import Stack`. We can remove some of the methods from `Stack` as they won't be needed, e.g. `reverse()`, `to_list()`, and `peek()`. 

We also will need to add some additional methods to complete the user stories' `search()` and `remove()` methods on table locations that are storing more than one item. So `Hash_Map.search()` will find the correct location on the table where the item should be, but `Stack.contains()` will then check every item stored at that particular location for a match.
> Note: I decided to implement a Stack() on _every_ table location, regardless of if there were multiple, one or even zero items at that location. This may be inefficient; I'll continue my studying and see if it's better practice to store the item "naked" at first, and then place the naked item and any new items into a linked list only _after_ the collisions have been detected.

## Public Methods (called from within `Hash_Map`)

- `push()` - classic stack behavior which plops the value right on top of the existing (or empty) stack. This is used to extend `Hash_Map.insert()` in my code.
- `contains()` - this method walks node by node through the particular stack looking for a match
- `remove()` - this method is similar to `contains()` in that it walks through the stack checking for a match before removing that match. However, implementation gets a bit trickier; first, you must check your match against the next node in line, not the current, since to remove the current you'll need to connect the _previous_ node to the _next_. This is very similar to the idea of "don't drop your rope" I discussed in [Reverse a Stack with Python](https://blog.benhammond.tech/reverse-a-stack-with-python); you need to take care not to lose track of your _previous_ and _next_ nodes as those are the connections that hold the entire structure together
- `__str__()`: similar to the special method within our hash map class, we need a custom method when an instance of our stack is converted to a string
 
> Note: as in our [Snake Tac Toe](https://blog.benhammond.tech/snaketactoe-in-two-hours) game; it's very easy as a beginner to get tripped up by referring to an instantiated _object_, when you really meant to refer to a particular _property_ of that object. Here, in both `contains()` and `remove()`, we need to compare the search item (a string) to the _string value_ of each particular node, not to the node itself. In my code there are two ways I do this, the first is by calling `.value` on the node, other places I use `str(node)`, which internally calls the special `__str__()` method we added. Either way, you must be sure you are comparing values of the same **type**. You can confirm a variable's type by logging: `print(type(a_variable))` and seeing what you are actually dealing with. 

``` python
def contains(self, item):
        current_head = self.head
        while current_head != None:

            # 👎This won't work 👎
            # if current_head == item: return True

            if current_head.value == item: return True
            # 👍 But this will! 👍  
                
            current_head = current_head.next
    
        return False
```

# Testing our Hash Map

First, one of the big benefits to using classes as we have above is that each of these class definitions can live in its own `.py` file, and can be easily imported into other files and scripts as needed. So for this project I have 3 files:
1. `hash_table.py` - contains our new hash map class which utilizes a table and stacks internally
2. `linked_list_stack.py` - contains our `Stack` class and the very simple `Node` class which is used within our linked list stack
3. `test.py` - which starts with some arbitrary data, instantiates an object of class `Hash_Map()`, then adds, searches for and removes various data from that structure. 

To import one file into another, you simply need to use the `import` statement (as opposed to JavaScript you must explicitly `export` as well, or Node.js where the equivalent is `require` and `module.exports`.

# Performance

I've seen it recommended to make your table about 30% larger than your data set when storing in a hash table; this is a good starting point to minimize wasted resources (either having the table too large and with lots of empty locations taking up room in memory, or the table too small and containing many collisions, which makes it operate more slowly). I therefor send in the `TABLE_SIZE` as a calculated argument using this line: `TABLE_SIZE = int(len(data) * 1.3)`

A properly hashed table should be _nearly_ as fast as a simple array since the values are mainly a single lookup away in both structures (which yields BigO(1) for arrays and best-case hash maps). However, if the table you chose if too small, there will be more collisions (multiple items overlapping and stored in the same locations). While our `insert()` method still will be BigO(1) since it's a single operation to get to the correct location and a second operation to push our item on to the top of that stack, retrieving and deleting that item will requiring walking through every item within the stack. It's possible that every item we need to store could end up in the same table location, meaning our hash map is really just a giant linked list. In this worst-case scenario, our hash map would have BigO(n), since it would walk through every single item we added to the structure. 

# More To Come...

This implementation is very simple, as my goal was simply to get it working. The hash algorithm I chose simply sums the ascii value of a string's characters, and then chops it down until that number will fit inside the table. There are many other ways to hash, in ways that optimize performance and manage required space in memory. I'm excited to have a basic version up and going, and a much more complete mental model of what is happening. I plan to continue implementing these data structures and algorithms, and would love to hear from you if you have any comments, corrections or feedback. Thanks for reading! 

To view the code or suggest changes, [visit the GitHub repo](https://github.com/benhammondmusic/hash_table). 



Photo in cover image by <a href="https://unsplash.com/@janbaborak?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Jan Baborák</a> on <a href="https://unsplash.com/s/photos/hashtag?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>
  