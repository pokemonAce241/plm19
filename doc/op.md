
[home](http://tiny.cc/plm19) |
[copyright](https://github.com/txt/plm19/blob/master/license.md) &copy;2019, timm&commat;ieee.org
<br>
<a href="http://tiny.cc/plm19"><img width=900 src="https://raw.githubusercontent.com/txt/plm19/master/etc/img/banner.png"></a>
<br>
[syllabus](https://github.com/txt/plm19/blob/master/doc/syllabus.md) |
[src](https://github.com/txt/plm19/tree/master/src) |
[submit](http://tiny.cc/plm19give) |
[chat](https://plm19.slack.com/)


# Precedence-driven Grammars

Useful for very small languages (for big stuff, aimed at massive industrial use,
see [LLVM](llvm.md)).

For examples the following, see [op.py](op.py).

We can:

- execute trees.
- built from postfix expressions
- which in turn are built from infix expressions

Terminology:

- prefix: operators before operands
- postfix: operands before operators

## Traversals:

inorder: (e.g. print a tree)

- first inorder(left)
- then node
- then order (right)

postorder : (e.g. code to place on stack for evaluations)

- left postorder(left)
- postorder(right)
- then node

preorder:

-first node,
-the preorder left 
-then pre-order right

## Evaluate the tree

<img width=500 src="https://ruslanspivak.com/lsbasi-part7/lsbasi_part7_parsetree_01.png">

```
Let t be the expression tree
If  t is not null then
      If t.value is operand then  
		Return  t.value
      Return calculate(t.operator, eval(t.elft), eval(t.right)   
```

But how to build the tree?

- built from postfix expressions
- which in turn are built from infix expressions


## Shunting Yard Algorithm

[Full notes](https://en.wikipedia.org/wiki/Shunting-yard_algorithm).

[Sample implementation](http://www.martinbroadhurst.com/shunting-yard-algorithm-in-python.html)

Infix to postfix (precursor to execution

- Tokens: tokenzied infix expression e.g, [a,+,b,\*,x,-,d]
- STACK: a FIFO stack
- Queue: output

Three operations

- LEFT : pop Input (running left to right), push onto Output
- DOWN : pop Input, push to Temp
- UP   : pop Temp, push to Input

![](../etc/img/shunting.png)

The following does not do prefixs or. infix operators

Also, don't memorize the following (not examinable). But if given the above diagram, tell me why we did
(e.g.) a DOWN or a LEFT in a particular case.

While there are tokens to be read:

-      Read a token
-      If it's a number add it to queue
-      If it's an operator
             - While there's an operator on the top of the stack with greater precedence:
                     Pop operators from the stack onto the queue
             - Push the current operator onto the stack
- If it's a left bracket push it onto the stack
-  If it's a right bracket 
           - While there's not a left bracket at the top of the stack:
                    Pop operators from the stack onto the output queue.
           - Pop the left bracket from the stack and discard it
- While there are operators on the stack, pop them to the queue 

## Postfix to Infix tree

Examples:

```
Input : abc++
Output : (a + (b + c))

Input  : ab*c+
Output : ((a*b)+c)
```


1. While there are input symbol left
     - Read the next symbol from the left of the input.
2. If the symbol is an operand
     - Push it onto the stack.
3. Otherwise,
     - the symbol is an operator.
     - Pop the top 2 values from the stack.
     - Put the operator, with the values as arguments and form a tree.
     - Push the resulted tree back to stack.
4. If there is only one value in the stack
     - That value in the stack is the desired infix string.

The following code does strings, not trees. Exercise for the reader: convert to a tree generator.


```c++
// CPP program to find infix for 
// a given postfix. 
#include <bits/stdc++.h> 
using namespace std; 
  
bool isOperand(char x) 
{ 
   return (x >= 'a' && x <= 'z') || 
          (x >= 'A' && x <= 'Z'); 
} 
  
// Get Infix for a given postfix 
// expression 
string getInfix(string exp) 
{ 
    stack<string> s; 
  
    for (int i=0; exp[i]!='\0'; i++) 
    { 
        // Push operands 
        if (isOperand(exp[i])) 
        { 
           string op(1, exp[i]); 
           s.push(op); 
        } 
  
        // We assume that input is 
        // a valid postfix and expect 
        // an operator. 
        else
        { 
            string op1 = s.top(); 
            s.pop(); 
            string op2 = s.top(); 
            s.pop(); 
            s.push("(" + op2 + exp[i] + 
                   op1 + ")"); 
        } 
    } 
  
    // There must be a single element 
    // in stack now which is the required 
    // infix. 
    return s.top(); 
} 
  
// Driver code 
int main() 
{ 
    string exp = "ab*c+"; 
    cout << getInfix(exp); 
    return 0; 
} 
```

