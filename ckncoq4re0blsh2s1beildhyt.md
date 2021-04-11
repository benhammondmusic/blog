## Snake_Tac_Toe in Two Hours

# Snake_Tac_Toe

> Command line Tic-Tac-Toe, written in Python in 2 hours as a group project

Today in class we dug into object-oriented programming in Python using classes. I had recently investigated this a bit on my own while implementing a  [stack using linked lists in Python](https://blog.benhammond.tech/linked-list-stack-in-python). Though I would have preferred a different game since I somewhat recently coded [React-Tac-Toe](https://blog.benhammond.tech/react-tac-toe), it was another fun experience to work as a group; thinking out loud through our issues and bouncing ideas off of one another. Also, since the basics of the game were pretty clear, we could focus more on how to implement functions and magic "dunder" methods like `__str__()` and how they can be helpful in a Python application. 

![Screen grab of command line game play](https://cdn.hashnode.com/res/hashnode/image/upload/v1618115689143/o94AyT9W_.gif)

## Tech

- Python

## Classes

We were required to implement classes in this Python coding exercises, so our group started by brainstorming the various entities present in a game of Tic Tac Toe, and which of those would be best represented by a class, and which would be better represented as properties of a class instance. The two classes we decided on were:

- `Game`: would represent rounds of the game, and would keep track of who's turn it was, which squares were filled, etc
- `GamePiece`: would represent the objects filling each square on the `Game`'s `gameboard[]` list.

## Tricky Bits

### Making Game "Playable" in Command Line

There's a neat little snippet of code that's been helpful recently, since we are mainly coding on the terminal with no GUI or view engine. It's a one-liner (plus a package import) that clears the terminal (similar to **CMD+K** or `clear`). If you'd like your code to display a clear screen (it actually just autoscrolls it down), simply:

1. `import os`
2. use `os.system('clear')` (on Mac)

We utilized this to clear the screen before each redraw of the screen in our `Game`'s `while()` loop.

### Checking for Win

The logic behind win a "win" relies on iterating over an array of all possible win conditions, and checking the current state of the board against each. We struggled getting these checks to work, until we finally isolated the bug: we were comparing the string `'O'` or `'X'` to the _**object**_ `GamePiece`, rather than the string _**property**_`GamePiece.mark` as we intended.

### Checking for Tie

Another issue with the logic involved the tie condition; there were two ways we discussed that could check for a tie:

- Track total played pieces, and if it reaches 9 and there's no winner, it's a tie
- Scan for empty spaces; once 0 (and no winner), it's a tie

### Cat's Eye

I truly enjoy working in groups, and in particular pair-programming exercises. Being able to have one person "think" through their typing, while the other can theorize more abstractly, and even go off to google for deeper research without interrupting the coding flow. Obviously another key benefit is simply having more eyeballs on your code; as long as communication is good and you aren't second-guessing each other too often, you can exterminate bugs before they even take hold, saving hours if not days of frustration. I look forward to continuing collaboration with fellow member of my General Assembly cohort, during the remainder of this course and long after. 


