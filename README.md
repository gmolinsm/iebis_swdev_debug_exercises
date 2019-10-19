# iebis_swdev_debugging
Source code to test debugging

## Instructions
First, **Fork** this project.

There are three exercises splitted in three branches of this repository. You must switch branches to checkout the code of each exercise.
Then, find the bugs that appear in each branch.
Fix the bugs if you can and answer to the questions proposed below.
Commit the code before checking out a different branch to avoid loosing the fixes that you have made to the code.

Once that you are done fixing bugs, **to score you must**:
1. Switch to the master branch.
2. Type below in this README.md file the answer to each question and paste some code that you have used to solve them.
3. Commit the changes
4. Push to your GitHub repository
5. **Finally place a Pull Request so I can see your proposed answers**


## Exercises
### Exercise 1
In this code there is a class called WordAnalyzer that contains several methods that analyzes some characteristics of the word passed as argument when the object WordAnalyzer is created.

For some reason, the methods are not working properly, sometimes they return the correct value and others don't. You need to answer the next questions.

#### Why the method _firstMultipleCharacter_ is returning "c" for the word _comprehensive_, when the correct answer should be "e"?
_firstMultipleCharacter_ calls a function called _find_. Inside _find_ the for loop starts with i = pos which always fulfils the if statement below, making the first letter always the output.
To fix it we must simply advance the for loop 1 iteration as shown below:
```
private int find(char c, int pos) {
    for (int i = pos+1; i < word.length(); i++)
    {
        if (word.charAt(i) == c)
        {
            return i;
        }
    }
    return -1;
}
```
#### Why the method _firstRepeatedCharacter_ is throwing an exception?
_firstRepeatedCharacter_ has an if statement that tries to access the string value in the position number 13 (a null value) and hence the "String index out of range" error.
To fix this we simply reduce the total length by 1 as shown:
```
public char firstRepeatedCharacter() {
    for (int i = 0; i < word.length()-1; i++)
    {
        char ch = word.charAt(i);
        if (ch == word.charAt(i + 1))
            return ch;
    }
    return 0;
}
```
#### Why the method _countGroupsRepeatedCharacters_ returns 3 in one case when it should be 4?
**Strategy**: Place breakpoints before the methods are executed, step into them and see what happens.

_countGroupsRepeatedCharacters_ has a for loop that starts at i = 1. Due this fact if we input _aabbcdaaaabb_ the first group of a's never gets registered.
If we set i = 0 we fix the issue but we generate an error due to the index being out of bounds (-1) in the first iteration.
To fix this we add an additional if statement for this scenario as shown below:
```
public int countGroupsRepeatedCharacters() {
    int c = 0;
    for (int i = 0; i < word.length() - 1; i++)
    {
        if (word.charAt(i) == word.charAt(i + 1)) // found a repetition
        {
            if (i == 0) {
                c++;
                continue;
            }

            if (word.charAt(i - 1) != word.charAt(i)) // it't the start
                c++;
        }
    }
    return c;
}
```

### Exercise 2
In this code we are placing mines in a board game where we have several spaces that can be mined. 
The boards can contain _Element_ objects, and since _Space_ and _Mine_ inherits from _Element_, the boards can contain this kind as well.

We have two boards of different size and place a different number of mines on each one. But in the second case it takes longer to place all the mines.

#### Why placing less bombs takes longer in the second case?
We need to keep in mind that the RED_BOARD is significantly bigger than the BLUE_BOARD.  
Given the fact that bombs are placed at random, it is significantly harder to allocate these in the BLUE_BOARD than in the RED_BOARD and hence the difference in time.
#### Knowing that usually there are going to be more bombs than spaces in the final boards, how would you change the method _minningTheBoard_ to be more efficient?
**Strategy**: Understand well what the code does. Use conditionals breakpoints.

Assuming there is more bombs than spaces, it only makes sense to start with a board full of bombs and then assign the spaces like so:
```
public static void minningTheBoard(int numberMines) {
    Random random = new Random();
 
     for (int i = 0; i < myBoard.size(); i++) {
         myBoard.put(i, new Mine());
     }
 
     int allocate = myBoard.size() - numberMines;
     while (allocate > 0) {
         Integer trial = random.nextInt(myBoard.size());
 
         if (myBoard.get(trial) instanceof Mine) {
             myBoard.put(trial, new Space());
             allocate--;
         }
     }
 }
```

### Exercise 3
In this case this code looks really simple. When the "d" reaches the value 1.0, the program should end, but it never does.

#### Why does not appear a message indicating that "d is 1"?
Because of how double variables work in Java (see [IEEE Standard for Floating-Point Arithmetic](https://en.wikipedia.org/wiki/IEEE_754))  
it is imposible for us to add those numbers due to the inaccurate way they are being stored in memory. This causes the while loop to never end since it never hits the intended value.
#### How will you fix it?
The commonly accepted solution for this problem is to use the BigDecimal class instead. But for such a simple exercise its way simpler to work with integers and divide the result by 10 as seen here:
```
public static void main(String [] args) {
    double d = 0;

    while (d != 10) {
        d += 1;
    }
    d = d/10;
    System.out.println("d is 1.0");
}
```