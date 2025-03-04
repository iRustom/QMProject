# Project1

## Distribution plan:

### Part (A) split three way:

- iRustom will complete 1 and 2
- BavlyRemon will complete 3
- OmarElfouly will complete 4 and 5

### Part (B), (C), (D) will be evenly split across members

## Plan so far:

- [x] Confirm Distrubition with Professor
- [x] Decide on data structure(s)
- [x] Decide on function headers i.e. input and output
- [x] Plan basic algorithm flow
- [x] Person A, B, C, begin programming
- [x] Test cases

## Algorithim Requirments

Part 1:  Read in (and validate) a Boolean function given as a SoP (for example: abc + a’b’c).
Assume that the variable name is a single Latin letter starting from “a”. Think about the checkers
needed to validate the input and list them in your report.

Part 2: Print the truth table of the function as well as the canonical SoP and PoS.

Part 3: Generate and print all prime implicants (PIs). For each PI show the minterms it covers as
well as its binary representation.

Part 4:  Using the PIs generated in part 2, obtain and print all the essential prime implicants EPIs
(as Boolean expressions). Also, print the minterms that are not covered by the essential PIs.

Part 5:  Solve the PI table and print the minimized Boolean expression of the Boolean
function using the 3 step heuristic discussed in the lecture.

## Program Design
### Part 1: iRustom
Takes input validates SoP:
- latin letters a to z.
- if there are no brackets then it is SoP.
- unmatched brackets result in an automatic fail.
- Operator at start or finish is a fail.
- Cannot be (ab)' or (a+b)' i.e. no multi var not operations or not that acts on a sum.
- any ( SUM ) must be preceded and followed by a "+" or a bracket or nothing at all.
- we will consider " AA+AA' " to be valid SoP.
- must be products with only sums between them.
- must return a list or vector of the variables in the input for other functions to use.

Input: User input

Output: Clean input

Part 1 is the section of the program concerned with input validation. It consists of 1 main function, named validation,
which takes in 3 parameters, the input string, a vector of strings, and a map of characters to integers, and it returns a boolean,
which evaluates to true if the input is valid, and false otherwise, and 2 supporting functions, which will be explained later on.

The way this function works is as follows: First of all, it removes any spaces in the input, and makes all the letters in the input lowercase,
which makes it easier to handle the input down the line. Then, we start validation.

The first simple validation made is making sure that the input does not start with anything other than either letters or an opening bracket,
and that the input does not end with anything other than a letter, a closing bracket or a negation. If any one of these conditions is true,
the function instantly returns false.

If the function is still running, meaning these conditions are not met, then we go on to the next checker, which simply makes sure
the input is not empty. Once that is done, we make sure that the input does not contain more than 10 variables, and this is done
using the first supporting function, called varList, which takes the input as a parameter, and returns a vector of characters,
which are the variables included in the input. It does this by looping through the input, and every time a character is found,
it checks that the character is not already in the vector, and if it is not, it is pushed back. Once the entire input string is looped through,
the vector is sorted, so that the most significant bit (MSB) is always the first character alphabetically (a).

Once we have a vector of the variables, we simply check that its size does not exceed 10. Then we make sure that the input does not have the following:
Firstly, that there are no invalid characters in the input, secondly, that no opening bracket has a closing bracket,
a '+', or a negation right after it, third, that no closing bracket has an opening bracket or a '+' immediately after it,
no NOT directly after a '+', and lastly, if the number of opening and closing brackets are unequal, then the input is invalid.
If there are no brackets at all and only the valid characters were input, then it is in SoP form, and thus valid. Throughout this loop,
all the brackets were indices were added to a vector of integers, and this was to prepare the function for bracket handling,
which is discussed next.

For bracket handling, I used a stack to find every matching pair of brackets, which is implemented using my second helper function, bracketPairs.
This works since for an innermost opening bracket, its match is the closest closing bracket to it. Using a stack means that
once this pair of innermost brackets is popped, the next pair of brackets is determined in the same way, where the innermost
opening is matched with the innermost closing, excluding the pair which was popped.

Now, for each pair, I determined whether it contains purely a product, or if it contains a sum, and stored this in a pair of a pair
of integers and a characters, where the first integer in the pair is the opening bracket, the second the closing, and the character denotes what each
bracket contained; ‘s’ if it contained a sum, and ‘p’ if it did not. While finding these pairs, I removed the indices of
the pairs formed from the vector of all bracket indices mentioned earlier, and this allows me to test whether or not there are “loose” brackets,
which is determined by checking if the vector of all brackets is empty or not, since if all brackets have a matching bracket,
then all of them would be erased from the vector of all integers, and the check is a simple check to make sure that vector is empty.
If it is not, then it is an invalid input.

Now, once each pair is determined, I only check the bracket pairs which contained a “+” within them, since these are the
potential problem-causing terms, and I check that it is not being multiplied before or after that term. Finally, if all these checks
are passed, the function is evaluated as true, and I am able to now move on to part 2.

### Part 2: iRustom
Input: clean user input
Output: Vector of min terms (x = {1,5,9}), and string of variables ("ACF").

Since we have validated that the input is in SoP form, I can now remove all brackets without altering the meaning of the input.
The main function used in this part is truthTable, which takes the same parameters as the validation function.

First of all, the input is split into its terms using a splitting function, which takes the input and a delimiter, and splits the input between
each delimiter and stores each part as an element in a vector of strings.

Once that is done, I loop over each term, and first of all make sure that a variable and its negation do not both
exist simultaneously in one term. If they do, then that term is equivalent to zero, so I remove that term from my term list.

I then proceed by converting each string term to a binary term, which I then use to determine the minterms of the function. I do this by
setting the variables in a term to 1, I set them to 0 if they are negated, and to 2 if they are not present at all. I then loop from
0 to 2^variable_count and use the bitset library to create every possibility of binary terms in my function (basically, the rows of the truth table),
and I compare only the 1s and 0s (not the 2s since they do not affect the terms) of my binary-converted terms with the bitset,
and if they match, I add the term to my vector of minterms.

Once that is done, I now have my vector of minterms and maxterms, and I call a simple printing function to print the canonical forms
of the function, along with their shorthand notations.

### Part 3: BavlyRemon

#### Inputs:

- minterms: a vector of integers representing the minterms of a boolean function

- var: a vector of characters representing the variables of the boolean function

- print: a string that determines whether to print intermediate steps of the function
#### Output:

- A map that maps prime implicants to their corresponding minterms

#### Functions used
The functions use a structure called implicant that stores the minterms covered by the implicant,
the implicant itself, and whether or not it is combined.

Also, several helper functions are used, including:

- count_ones which counts the number of ones in a binary number

- differ_by_one, which checks if two binary numbers differ by exactly one bit 

- combinestring, which combines two binary strings into a new string where '-' is used to represent differing bits.

- to_binary_string function converts a decimal number into a binary string and pads it with leading zeroes to reach a specified length.

- split1 function is a string splitting function that splits a string using some delimiter ("," in our case) and puts the splitted up terms into a vector.

- printVector function takes in a vector of vectors of implicants and an integer count(keeps track of which column it's printing so it can be labeled), and prints out the implicants with the minterms they cover and whether or not they are prime.

Then there are two main functions Primeimplicants and generate_column

- primeimplicants is a function that takes in a list of minterms and a list of variables as inputs and returns a map that represents the prime implicants of the Boolean function represented by the minterms.
The third input, print, is a string that determines whether or not the intermediate steps of the algorithm should be printed to the console.

The function first initializes an empty map to store the prime implicants.
It then checks for 2 possible cases that don't need to run the full algorithm to compute 
mainly if all possible minterms are present in the input list, in which case it returns a map with a single entry representing the function being always true.
And If there are no minterms, it returns an empty map.

The function then groups the minterms based on the number of ones in their binary representation, 
creating a set of groups of implicants. It then calls the generate_column function to actually compute the Prime implicants using the qm algorithm

Finally, the function converts the prime implicants into implicants using the variable representation instead of the binary one 
and returns a map with entries representing the prime implicants and the minterms that they cover. 
If the print parameter is set to true, the function also prints the intermediate steps of the algorithm to the console.

- The function generate_column takes in a  vector of vectors of implicants called groups, 
a vector of characters called var which represents the variables used in the Boolean expression, and a string variable print to control whether or not to print intermediate steps. 
The function generates a column of prime implicants using the Quine-McCluskey algorithm.

The function first initializes some variables, including vectors for prime implicants and groups of prime implicants, and a hash set to remove duplicates. 
It then loops through each group of implicants and compares each implicant with every implicant in the next group.
If two implicants differ by only one variable, they are combined into a new implicant and added to the appropriate group of prime implicants. 
The function then flags each combined implicant so it is not included in the final list of prime implicants. The function continues this process until no more implicants can be combined.

The function also has an optional printing feature to show the columns produced by the algorithm. When print is set to "1", the function prints the current column of implicants, followed by a line of dashes to separate the output.

To remove duplicate prime implicants, the function uses a hash set called primes_checker. When a new prime implicant is generated, it is checked against the hash set. 
If the implicant is not in the hash set, it is added to the hash set and to the appropriate group of prime implicants. This ensures that each prime implicant is unique and not repeated in the final output.

Finally, the function returns the list of prime implicants found during the algorithm.

#### Design choices and changes

- Added the implicant struct instead of only using a string for the implicant to be able to store more data about the implicant 
(used struct instead of pair)

-decided to keep track of minterms combined to know the minterms covered by an implicant instead of using the implicant itself and some recursive function (reason why struct was implemented )

-Used an Unordered set instead of a vector to keep track of repeated combined implicants as it is O(1) search

-Used a do while loop instead of for loop to stop when no more implicants can be combined 

-added an extra parameter in the function to add the option to print or not


### Part 4 and 5: OmarElfouly

Since part 4 can be considered an essential subsection of part 5 we will be discussing them together.

Input: Map where key is the PI and the data is a vector of int values to represent the covers of a minterm
Output: Final answer in the form of minimum covers 

This part of the algorithim is controlled and accomplished by the function named part4and5() in part4_5.h

The trivial solutions to this part of the algorithim occur whenever there are no prime implicants i.e. function = 0
and when the function = 1. We account for these using if conditions at the start of the algorithim.

For the sake of clarity we can imagine our data as a table with our rows being the PIs and our columns being minterms.

The program requires us to find essential prime implicants, dominating columns and dominated rows. This immeadily will
require us iterate through the data in terms of columns and rows. Unfortunatly there exists no singular data structure
in c++ that has the ability to interpret the data in terms of rows and columns in reasonable time. Therefore, a reflected
map will be used to allow for effecient access of both columns and rows.

To create the reflected map we only need to iterate through the first map to find every minterm and create a list of the prime implicants that cover it.

Part 4 requires us to find all essential prime implicants, which are prime implicants that are the only covers for one or more minterms.
This will require us to iterate through each minterm and check to see whether it is covered by a single minterm. The advantage of the
reflected map is made clear here since it it reduces the time complexity of this section from n^3 to n^2 by sacrificing some extra storage.

Next we will iterate through each minterm (column) and insert all prime implicants that uniquely cover a single minterm
into a vector of strings named EPIS.

The algortithim then prints these essential prime implicants, satisfying the first section of part 4.

The remaining minterms can then be found by removing the essential prime implicants from the table such that all their minterms
are deleted from both data structures (i.e. removed from both rows and columns).

To modularise the algorithim a function with the purpose of deleting a term from the reflected data structure was created
for both rows and columns to simplify the deletion process.

The remaining minterms are the keys left over in the minterm map after we have removed all the minterms present in the
essential prime implicants.

This completes part 4 which can be described as finding essential prime implicants and updating the table.

Part 5 is completed by the remaining code which can be simplified to the following algorithim:

While there are still uncovered minterms

 - Find the current essential prime implicants and then add them to final answer while removing them from table
 - Remove dominating columns
 - Remove dominated rows

 A dominating column is removed since this minterm is covered by another minterm. This means that we need to delete any
 column that is a superset of another column.

 Similarly a dominated row is removed since its a prime implicant that is fully covered by another prime implicant. This will
 involve deleting any row that is a subset of another row.

 To detect subset and superset relationships we will be using the IsSubset function which returns true if the first argument
 is a subset of the second argument.

 We will then iterate through the rows and columns, while deleting rows and columns as neccessary.

 From the table we are left with we can select the new articial essential prime implicants just as we did in Part 4, adding
 every one of them to our final answer.
 
 This will repeat until there are no minterms left uncovered, at that point the function prints the final answer.


 ### Main.cpp

 When the alorithim launches a welcome line will explain the basic rules for input before the program continues.
 Main involves a very simple while loop that repeats as long as the user asks it to. The user is prompted for input then given the option of printing the QM
 or not printing it. Each part of the function is then called with the neccessary arguments. There is an aditional set of input validation preformed for each
 user input instance.

 ### Build and Run:

 Clone the project from GitHub, and open it on Visual Studio and click run. You will be presented with instructions on the console, simply follow the
 on-screen instructions, and type in your input when prompted to get the truth table, the Canonical and shorthand notations, the QM table, the potential
 prime implicants, and the simplest form of the given function.


## Testing:
### Test 1:
3 variable input, with f = 0. The test input is: aa'+bb'+cc', which is evaluated to 0.

### Test 2:
3 variable input, with f = 1. The test input is a'+a+b+c, which is evaluated to 1.

### Test 3:
Demonstrating variables repeated multiple times in the same term: aaaa+b'b'b', which evaluates to a + b'.

### Test 4:
Demonstarting processing of dominance: a'b'c'd'+a'bc'd'+a'bc'd+a'bcd'+a'bcd+ab'c'd'+ab'c'd+ab'cd'+abc'd+abcd.

### Test 5:
Demonstrating how multiple negations and multiple additions are simplified. a''b ++ c becomes ab + c.

### Test 6:
Demonstrating an inavlid PoS term, which shows bracket handling. ((abbbc)(ac+d)) would be invalid since we are multiplying a sum.

### Test 7:
Demonstrating using multiple negations on the same term, along with handling brackets. (((abbbc'''''))+cdd) becomes abc’ + cd

### Test 8:
Demonstrating a multiple variable negation, which would be invalid. This also includes bracket handling. (a+b)' would be invalid.

### Test 9:
Demonstrating how the program identifies lowercase and uppercase variables to be identical. ac + Ac would be ac + ad

### Test 10:
A final all encompassing input, which contains multiple nested brackets, repeated variables in the same term, multile negations, and multiple additions, all at once. ((((abcd’’’’ef’’’ghij)) + ghi’’’) ++++ abbbbc) would be evaluated as abcdef’ghij + ghi’ + abc.
