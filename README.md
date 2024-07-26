<a name='1'></a>
## 1 - Data Preprocessing 

```python
import re
from collections import Counter
import numpy as np
import pandas as pd

```

<a name='1.1'></a>
### 1.1 - process_data
Implement the function `process_data` which 

1) Reads in a corpus (text file)

2) Changes everything to lowercase

3) Returns a list of words. 

#### Options and Hints
- If you would like more of a real-life practice, don't open the 'Hints' below (yet) and try searching the web to derive your answer.
- If you want a little help, click on the green "General Hints" section by clicking on it with your mouse.
- If you get stuck or are not getting the expected results, click on the green 'Detailed Hints' section to get hints for each step that you'll take to complete this function.

```python
# UNQ_C1 GRADED FUNCTION: process_data
def process_data(file_name):
    #Open the file, read its contents into a string variable
    with open(file_name) as f:
        file_name_data = f.read()
    # convert all letters to lower case
    file_name_data = file_name_data.lower()
    #Convert every word to lower case and return them in a list.
    words = re.findall('\w+', file_name_data)
    
    return words
```

```python
#DO NOT MODIFY THIS CELL
word_l = process_data('./data/shakespeare.txt')
vocab = set(word_l)  # this will be your new vocabulary
print(f"The first ten words in the text are: \n{word_l[0:10]}")
print(f"There are {len(vocab)} unique words in the vocabulary.")
```

<a name='2.1'></a>
### 2.1 - get_count

Implement a `get_count` function that returns a dictionary
- The dictionary's keys are words
- The value for each word is the number of times that word appears in the corpus. 

For example, given the following sentence: **"I am happy because I am learning"**, your dictionary should return the following: 
<table style="width:20%">

  <tr>
    <td> <b>Key </b>  </td>
    <td> <b>Value </b> </td> 


  </tr>
  <tr>
    <td> I  </td>
    <td> 2</td> 
 
  </tr>
   
  <tr>
    <td>am</td>
    <td>2</td> 
  </tr>

  <tr>
    <td>happy</td>
    <td>1</td> 
  </tr>
  
   <tr>
    <td>because</td>
    <td>1</td> 
  </tr>
  
   <tr>
    <td>learning</td>
    <td>1</td> 
  </tr>
</table>


**Instructions**: 
Implement a `get_count` which returns a dictionary where the key is a word and the value is the number of times the word appears in the list.  

```python
def get_count(word_l):
    word_count_dict = {}  # fill this with word counts
    
    word_count_dict = Counter(word_l)
    
    return word_count_dict
```

```python
#DO NOT MODIFY THIS CELL
word_count_dict = get_count(word_l)
print(f"There are {len(word_count_dict)} key values pairs")
print(f"The count for the word 'thee' is {word_count_dict.get('thee',0)}")
```

<a name='3.1'></a>
### 3.1 - get_probs
Given the dictionary of word counts, compute the probability that each word will appear if randomly selected from the corpus of words.

$$P(w_i) = \frac{C(w_i)}{M} \tag{Eqn-2}$$
where 

$C(w_i)$ is the total number of times $w_i$ appears in the corpus.

$M$ is the total number of words in the corpus.

For example, the probability of the word 'am' in the sentence **'I am happy because I am learning'** is:

$$P(am) = \frac{C(w_i)}{M} = \frac {2}{7} \tag{Eqn-3}.$$

**Instructions:** Implement `get_probs` function which gives you the probability 
that a word occurs in a sample. This returns a dictionary where the keys are words, and the value for each word is its probability in the corpus of words.

```python
# UNQ_C3 GRADED FUNCTION: get_probs
def get_probs(word_count_dict):

    probs = {}  # return this variable correctly
    m = sum(word_count_dict.values())
    # get the total count of words for all words in the dictionary
    for key in word_count_dict.keys():
        probs[key] = word_count_dict[key] / m

    return probs
```

```python
#DO NOT MODIFY THIS CELL
probs = get_probs(word_count_dict)
print(f"Length of probs is {len(probs)}")
print(f"P('thee') is {probs['thee']:.4f}")
```

<a name='2'></a>
## 2 - String Manipulations

Now that you have computed $P(w_i)$ for all the words in the corpus, you will write a few functions to manipulate strings so that you can edit the erroneous strings and return the right spellings of the words. In this section, you will implement four functions: 

* `delete_letter`: given a word, it returns all the possible strings that have **one character removed**. 
* `switch_letter`: given a word, it returns all the possible strings that have **two adjacent letters switched**.
* `replace_letter`: given a word, it returns all the possible strings that have **one character replaced by another different letter**.
* `insert_letter`: given a word, it returns all the possible strings that have an **additional character inserted**. 


### 2.1 - delete_letter

**Instructions for delete_letter():** Implement a `delete_letter()` function that, given a word, returns a list of strings with one character deleted. 

For example, given the word **nice**, it would return the set: `{'ice', 'nce', 'nic', 'nie'}`. 

**Step 1:** Create a list of 'splits'. This is all the ways you can split a word into Left and Right. For example,   
`'nice'` is split into: `[('', 'nice'), ('n', 'ice'), ('ni', 'ce'), ('nic', 'e'), ('nice', '')]`


**Step 2:** This is specific to `delete_letter`. Here, we are generating all words that result from deleting one character.  
This can be done in a single line with a list comprehension. You can make use of this type of syntax:  
`[f(a,b) for a, b in splits if condition]`  

For our 'nice' example you get: 
['ice', 'nce', 'nie', 'nic']

```python
def delete_letter(word, verbose=False):
   
    delete_l = []
    split_l = []
    
    for c in range(len(word)):
        split_l.append((word[:c], word[c:]))
    for a, b in split_l:
        delete_l.append(a + b[1:])

    if verbose: print(f"input word {word}, \nsplit_l = {split_l}, \ndelete_l = {delete_l}")

    return  delete_l
```

```python
delete_word_l = delete_letter(word="cans",
                        verbose=True)
```

<a name='2.2'></a>
### 2.2 - switch_letter

**Instructions for switch_letter()**: Now implement a function that switches two letters in a word. It takes in a word and returns a list of all the possible switches of two letters **that are adjacent to each other**. 
- For example, given the word 'eta', it returns {'eat', 'tea'}, but does not return 'ate'.

**Step 1:** is the same as in delete_letter()  
**Step 2:** A list comprehension or for loop which forms strings by swapping adjacent letters. This is of the form:  
`[f(L,R) for L, R in splits if condition]`  where 'condition' will test the length of R in a given iteration. See below.

```python
def switch_letter(word, verbose=False):
    switch_l = []
    split_l = []
    
    for c in range(len(word)):
        split_l.append((word[:c], word[c:]))
    switch_l = [a + b[1] + b[0] + b[2:] for a, b in split_l if len(b) >= 2]
    
    if verbose: print(f"Input word = {word} \nsplit_l = {split_l} \nswitch_l = {switch_l}") 
    
    return switch_l
```

```python
switch_word_l = switch_letter(word="eta",
                         verbose=True)
```

<a name='2.3'></a>
### 2.3 - replace_letter
**Instructions for replace_letter()**: Now implement a function that takes in a word and returns a list of strings with one **replaced letter** from the original word. 

**Step 1:** is the same as in `delete_letter()`

**Step 2:** A list comprehension or for loop which form strings by replacing letters.  This can be of the form:  
`[f(a,b,c) for a, b in splits if condition for c in string]`   Note the use of the second for loop.  
It is expected in this routine that one or more of the replacements will include the original word. For example, replacing the first letter of 'ear' with 'e' will return 'ear'.

**Step 3:** Remove the original input letter from the output.

```python
def replace_letter(word, verbose=False):
 
    letters = 'abcdefghijklmnopqrstuvwxyz'
    
    replace_l = []
    split_l = []
    
    for c in range(len(word)):
        split_l.append((word[0:c], word[c:]))
    replace_l = [a + l + (b[1:] if len(b) > 1 else '') for a, b in split_l if b for l in letters]
    replace_set = set(replace_l)
    replace_set.remove(word)
    
    # turn the set back into a list and sort it, for easier viewing
    replace_l = sorted(list(replace_set))
    
    if verbose: print(f"Input word = {word} \nsplit_l = {split_l} \nreplace_l {replace_l}")   
    
    return replace_l
```

```python
replace_l = replace_letter(word='can',
                              verbose=True)
```

<a name='2.4'></a>
### 2.4 - insert_letter

**Instructions for insert_letter()**: Now implement a function that takes in a word and returns a list with a letter inserted at every offset.

**Step 1:** is the same as in `delete_letter()`

**Step 2:** This can be a list comprehension of the form:  
`[f(a,b,c) for a, b in splits if condition for c in string]`   

```python
def insert_letter(word, verbose=False):
 
    letters = 'abcdefghijklmnopqrstuvwxyz'
    insert_l = []
    split_l = []
    
    for c in range(len(word) + 1):
        split_l.append((word[0:c], word[c:]))
    insert_l = [a + l + b for a, b in split_l for l in letters]
    
    if verbose: print(f"Input word {word} \nsplit_l = {split_l} \ninsert_l = {insert_l}")
    
    return insert_l
```

```python
insert_l = insert_letter('at', True)
print(f"Number of strings output by insert_letter('at') is {len(insert_l)}")
```

<a name='3'></a>
## 3 - Combining the Edits

Now that you have implemented the string manipulations, you will create two functions that, given a string, will return all the possible single and double edits on that string. These will be `edit_one_letter()` and `edit_two_letters()`.

<a name='ex-8'></a>
### 3.1 - edit_one_letter

**Instructions**: Implement the `edit_one_letter` function to get all the possible edits that are one edit away from a word. The edits  consist of the replace, insert, delete, and optionally the switch operation. You should use the previous functions you have already implemented to complete this function. The 'switch' function  is a less common edit function, so its use will be selected by an "allow_switches" input argument.

Note that those functions return *lists* while this function should return a *python set*. Utilizing a set eliminates any duplicate entries.

```python
def edit_one_letter(word, allow_switches = True):
   
    edit_one_set = set()
    
    edit_one_set.update(delete_letter(word))
    if allow_switches:
        edit_one_set.update(switch_letter(word))
    edit_one_set.update(replace_letter(word))
    edit_one_set.update(insert_letter(word))
    
    # return this as a set and not a list
    return set(edit_one_set)
```

```python
tmp_word = "at"
tmp_edit_one_set = edit_one_letter(tmp_word)
# turn this into a list to sort it, in order to view it
tmp_edit_one_l = sorted(list(tmp_edit_one_set))

print(f"input word {tmp_word} \nedit_one_l \n{tmp_edit_one_l}\n")
print(f"The type of the returned object should be a set {type(tmp_edit_one_set)}")
print(f"Number of outputs from edit_one_letter('at') is {len(edit_one_letter('at'))}")
```

<a name='3.2'></a>
### 3.2 - edit_two_letters

Now you can generalize this to implement to get two edits on a word. To do so, you would have to get all the possible edits on a single word and then for each modified word, you would have to modify it again. 

**Instructions**: Implement the `edit_two_letters` function that returns a set of words that are two edits away. Note that creating additional edits based on the `edit_one_letter` function may 'restore' some one_edits to zero or one edits. That is allowed here. This is accounted for in get_corrections.

```python
def edit_two_letters(word, allow_switches = True):
    
    edit_two_set = set()
    
    edit_one = edit_one_letter(word, allow_switches = allow_switches)
    for w in edit_one:
        if w:
            edit_two = edit_one_letter(w, allow_switches = allow_switches)
            edit_two_set.update(edit_two)
    
    # return this as a set instead of a list
    return set(edit_two_set)
```

```python
tmp_edit_two_set = edit_two_letters("a")
tmp_edit_two_l = sorted(list(tmp_edit_two_set))
print(f"Number of strings with edit distance of two: {len(tmp_edit_two_l)}")
print(f"First 10 strings {tmp_edit_two_l[:10]}")
print(f"Last 10 strings {tmp_edit_two_l[-10:]}")
print(f"The data type of the returned object should be a set {type(tmp_edit_two_set)}")
print(f"Number of strings that are 2 edit distances from 'at' is {len(edit_two_letters('at'))}")
```

<a name='3.3'></a>
### 3.3 - get_corrections

Now you will use your `edit_two_letters` function to get a set of all the possible 2 edits on your word. You will then use those strings to get the most probable word you meant to type a.k.a your typing suggestion.


**Instructions**: Implement `get_corrections`, which returns a list of zero to n possible suggestion tuples of the form (word, probability_of_word). 

**Step 1:** Generate suggestions for a supplied word: You'll use the edit functions you have developed. The 'suggestion algorithm' should follow this logic: 
* If the word is in the vocabulary, suggest the word. 
* Otherwise, if there are suggestions from `edit_one_letter` that are in the vocabulary, use those. 
* Otherwise, if there are suggestions from `edit_two_letters` that are in the vocabulary, use those. 
* Otherwise, suggest the input word.*  
* The idea is that words generated from fewer edits are more likely than words with more edits.


Note: 
- Edits of two letters may 'restore' strings to either zero or one edit. This algorithm accounts for this by preferentially selecting lower distance edits first.

#### Short circuit
In Python, logical operations such as `and` and `or` have two useful properties. They can operate on lists and they have ['short-circuit' behavior](https://docs.python.org/3/library/stdtypes.html). Try these:

```python
# example of logical operation on lists or sets
print( [] and ["a","b"] )
print( [] or ["a","b"] )
#example of Short circuit behavior
val1 =  ["Most","Likely"] or ["Less","so"] or ["least","of","all"]  # selects first, does not evalute remainder
print(val1)
val2 =  [] or [] or ["least","of","all"] # continues evaluation until there is a non-empty list
print(val2)
```

The logical `or` could be used to implement the suggestion algorithm very compactly. Alternately, if/elif/else constructs could be used.
 
**Step 2**: Create a 'best_words' dictionary where the 'key' is a suggestion and the 'value' is the probability of that word in your vocabulary. If the word is not in the vocabulary, assign it a probability of 0.

**Step 3**: Select the n best suggestions. There may be fewer than n.

```python
def get_corrections(word, probs, vocab, n=2, verbose = False):
    
    suggestions = []
    n_best = []
    
    #Step 1: create suggestions as described above    
    suggestions = set()
    suggestions = suggestions.union(edit_one_letter(word))
    suggestions = suggestions.union(delete_letter(word))
    suggestions = suggestions.union(replace_letter(word))
    suggestions = suggestions.union(insert_letter(word))
    suggestions = suggestions.intersection(vocab)
    
    #Step 2: determine probability of suggestions
    probs_suggestions = {s: probs.get(s, 0) for s in suggestions}
    
    #Step 3: Get all your best words and return the most probable top n_suggested words as n_best
    n_best = sorted(probs_suggestions.items(), key = lambda x : x[1], reverse = True)[:n]
    
    
    if verbose: print("entered word = ", word, "\nsuggestions = ", suggestions)

    return n_best
```

```python
# Test your implementation - feel free to try other words in my word
my_word = 'dys' 
tmp_corrections = get_corrections(my_word, probs, vocab, 2, verbose=True) # keep verbose=True
for i, word_prob in enumerate(tmp_corrections):
    print(f"word {i}: {word_prob[0]}, probability {word_prob[1]:.6f}")

# CODE REVIEW COMMENT: using "tmp_corrections" insteads of "cors". "cors" is not defined
print(f"data type of corrections {type(tmp_corrections)}")
```

<a name='4'></a>
## 4 - Minimum Edit Distance

Now that you have implemented your auto-correct, how do you evaluate the similarity between two strings? For example: 'waht' and 'what'

Also how do you efficiently find the shortest path to go from the word, 'waht' to the word 'what'?

You will implement a dynamic programming system that will tell you the minimum number of edits required to convert a string into another string.

<a name='4-1'></a>
### 4.1 - Dynamic Programming

Dynamic Programming breaks a problem down into subproblems which can be combined to form the final solution. Here, given a string source[0..i] and a string target[0..j], we will compute all the combinations of substrings[i, j] and calculate their edit distance. To do this efficiently, we will use a table to maintain the previously computed substrings and use those to calculate larger substrings.

You have to create a matrix and update each element in the matrix as follows:  

$$\text{Initialization}$$

\begin{align}
D[0,0] &= 0 \\
D[i,0] &= D[i-1,0] + del\_cost(source[i]) \tag{4}\\
D[0,j] &= D[0,j-1] + ins\_cost(target[j]) \\
\end{align}


$$\text{Per Cell Operations}$$
\begin{align}
 \\
D[i,j] =min
\begin{cases}
D[i-1,j] + del\_cost\\
D[i,j-1] + ins\_cost\\
D[i-1,j-1] + \left\{\begin{matrix}
rep\_cost; & if src[i]\neq tar[j]\\
0 ; & if src[i]=tar[j]
\end{matrix}\right.
\end{cases}
\tag{5}
\end{align}

So converting the source word **play** to the target word **stay**, using an input cost of one, a delete cost of 1, and replace cost of 2 would give you the following table:
<table style="width:20%">

  <tr>
    <td> <b> </b>  </td>
    <td> <b># </b>  </td>
    <td> <b>s </b>  </td>
    <td> <b>t </b> </td> 
    <td> <b>a </b> </td> 
    <td> <b>y </b> </td> 
  </tr>
   <tr>
    <td> <b>  #  </b></td>
    <td> 0</td> 
    <td> 1</td> 
    <td> 2</td> 
    <td> 3</td> 
    <td> 4</td> 
 
  </tr>
  <tr>
    <td> <b>  p  </b></td>
    <td> 1</td> 
 <td> 2</td> 
    <td> 3</td> 
    <td> 4</td> 
   <td> 5</td>
  </tr>
   
  <tr>
    <td> <b> l </b></td>
    <td>2</td> 
    <td>3</td> 
    <td>4</td> 
    <td>5</td> 
    <td>6</td>
  </tr>

  <tr>
    <td> <b> a </b></td>
    <td>3</td> 
     <td>4</td> 
     <td>5</td> 
     <td>4</td>
     <td>5</td> 
  </tr>
  
   <tr>
    <td> <b> y </b></td>
    <td>4</td> 
      <td>5</td> 
     <td>6</td> 
     <td>5</td>
     <td>4</td> 
  </tr>
  

</table>



The operations used in this algorithm are 'insert', 'delete', and 'replace'. These correspond to the functions that you defined earlier: insert_letter(), delete_letter() and replace_letter(). switch_letter() is not used here.

The diagram below describes how to initialize the table. Each entry in D[i,j] represents the minimum cost of converting string source[0:i] to string target[0:j]. The first column is initialized to represent the cumulative cost of deleting the source characters to convert string "EER" to "". The first row is initialized to represent the cumulative cost of inserting the target characters to convert from "" to "NEAR".

Filling in the remainder of the table utilizes the 'Per Cell Operations' in the equation (5) above. Note, the diagram below includes in the table some of the 3 sub-calculations shown in light grey. Only 'min' of those operations is stored in the table in the `min_edit_distance()` function.

Note that the formula for $D[i,j]$ shown in the image is equivalent to:

\begin{align}
 \\
D[i,j] =min
\begin{cases}
D[i-1,j] + del\_cost\\
D[i,j-1] + ins\_cost\\
D[i-1,j-1] + \left\{\begin{matrix}
rep\_cost; & if src[i]\neq tar[j]\\
0 ; & if src[i]=tar[j]
\end{matrix}\right.
\end{cases}
\tag{5}
\end{align}

The variable `sub_cost` (for substitution cost) is the same as `rep_cost`; replacement cost.  We will stick with the term "replace" whenever possible.

Below are some examples of cells where replacement is used. This also shows the minimum path from the lower right final position where "EER" has been replaced by "NEAR" back to the start. This provides a starting point for the optional 'backtrace' algorithm below.

<a name='4.1'></a>
### 4.1 - min_edit_distance

Again, the word "substitution" appears in the figure, but think of this as "replacement".

```python
def min_edit_distance(source, target, ins_cost = 1, del_cost = 1, rep_cost = 2):
    
    # use deletion and insert cost as  1
    m = len(source) 
    n = len(target) 
    #initialize cost matrix with zeros and dimensions (m+1,n+1) 
    D = np.zeros((m+1, n+1), dtype=int) 
    
    
    # Fill in column 0, from row 1 to row m, both inclusive
    for row in range(1,m + 1): # Replace None with the proper range
        D[row, 0] = D[row - 1, 0] + del_cost
        
    # Fill in row 0, for all columns from 1 to n, both inclusive
    for col in range(1,n + 1): # Replace None with the proper range
        D[0, col] = D[0, col - 1] + ins_cost
        
    # Loop through row 1 to row m, both inclusive
    for row in range(1, m + 1):
        
        # Loop through column 1 to column n, both inclusive
        for col in range(1, n + 1):
            
            # Intialize r_cost to the 'replace' cost that is passed into this function
            r_cost = rep_cost
            
            # Check to see if source character at the previous row
            # matches the target character at the previous column, 
            if source[row - 1] == target[col - 1]: # Replace None with a proper comparison
                # Update the replacement cost to 0 if source and target are the same
                r_cost = 0
                
            # Update the cost at row, col based on previous entries in the cost matrix
            # Refer to the equation calculate for D[i,j] (the minimum of three calculated costs)
            D[row,col] = min([D[row - 1, col] + del_cost, D[row, col - 1] + ins_cost, D[row - 1, col - 1] + r_cost])
            
    # Set the minimum edit distance with the cost found at row m, column n 
    med = D[m, n]
    
    return D, med
```

```python
#DO NOT MODIFY THIS CELL
# testing your implementation 
source =  'play'
target = 'stay'
matrix, min_edits = min_edit_distance(source, target)
print("minimum edits: ",min_edits, "\n")
idx = list('#' + source)
cols = list('#' + target)
df = pd.DataFrame(matrix, index=idx, columns= cols)
print(df)
```

```python
#DO NOT MODIFY THIS CELL
# testing your implementation 
source =  'eer'
target = 'near'
matrix, min_edits = min_edit_distance(source, target)
print("minimum edits: ",min_edits, "\n")
idx = list(source)
idx.insert(0, '#')
cols = list(target)
cols.insert(0, '#')
df = pd.DataFrame(matrix, index=idx, columns= cols)
print(df)
```

```python

```

