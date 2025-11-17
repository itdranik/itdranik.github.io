---
title: Parsing Math Expressions in C#. Shunting-Yard Algorithm
_wp_old_date: "2020-03-01"
_wp_old_slug: parsing-mathematical-expressions-shunting-yard-algorithm
author: yauheni.shybeka
categories:
  - coding-interview
image: /wp-content/uploads/2020/02/math-expressions-feature-image-1.jpg
date: "2020-02-22T08:00:27+00:00"
language:
  - English
tags:
  - c#
  - mathematical-expressions
  - parser

---
Winter is the coldest time of the year, but even it is not as cold as the interviewer's reaction to your implementation of the calculator for the postfix notation. Nevertheless, we do not have time for emotions, therefore, we're proceeding to analyze the shunting-yard algorithm mentioned in the [previous article](/en/math-expressions-postfix-notation/).

This algorithm is used for transforming the math expressions written in the infix notation to the postfix notation. We're not going to give a complete proof of this algorithm's correctness but only partial explanations to make the implementation more intuitive.

Let's start with the simplest examples: applying a binary operator to operands. For example, for the expression `2 + 3` a similar expression in the postfix notation would be `2 3 +`. The same is true for other binary operators. Let's try to create a simple model in C# for working with such expressions:

- While an expression has tokens take a token:
  - If the token is an operand then add it to the final sequence;
  - If the token is an operator then put it to some temporary storage.
- Take the previously saved operator from the storage and add it to the final sequence.

All this works great until we want to complicate the original expression and add another operator `2 + 3 - 4`. For the following case, a similar expression in the postfix notation would be `2 3 + 4 -`. Note that the previous algorithm cannot store more than one operator in the temporary storage, so we're losing one operator in the calculation process and as a result, we would get `2 3 4 -`. This cannot but upset, therefore, let's try to add an improvement to our conversion algorithm.

- While an expression has tokens take a token:
  - If the token is an operand then add it to the final sequence;
  - If the token is an operator then put it to some temporary storage. _If the storage contains another operator, then we move the previously saved operator to the resulting sequence, and the new operator is saved to the storage_.
- Take the last saved operator from the storage and add it to the final sequence.

Beautiful! But what to do if operators have different priorities? `2 + 3 * 4` this expression should be converted into the following one `2 3 4 * +`, but not to the expression `2 3 + 4 *`. To further improve the algorithm, we also need to consider the expression `2 * 3 + 4` and its counterpart in the postfix notation `2 3 * 4 +`. From where we can make a conclusion that our algorithm does not work properly only for the cases when the next operator has a higher priority than the previous one. Moreover, in the case when the next operator has a higher priority, the operators should, as it were, swap places in the resulting expression. So it must be inferred that our temporary storage should become a stack.

- While an expression has tokens take a token:
  - If the token is an operand then add it to the final sequence;
  - If the token is an operator then put it to some temporary storage. If the storage contains another operator, then we move the previously saved operator to the resulting sequence, and the new operator is saved to the storage.
  - _If the token is an operator:_

    - _While the stack is not empty and the top of the stack contains an operator with an equal or greater priority (>=), move the stored operator from the stack to the resulting sequence;_

    - _Store this new operator to the stack._
- Take the last saved operator from the storage and add it _the saved operators from the stack and move them_ to the final sequence.

And as the final touch is to add support for brackets that can change the priority of the operators execution. Let's consider the following expression `(2 + 3 * 4) / 5` and a counterpart for the postfix notation `2 3 4 * + 5 /`. In other words, the expression in the brackets must be calculated before division. Let's extend the operator types from the previous article with the bracket operators:

The closing bracket token is essentially a signal that notifies that a calculation is necessary at this point. Since the postfix calculator is sequential, the later an operator appears in the postfix notation, the later its execution occurs. Therefore, we extend the algorithm with the following rules:

- While an expression has tokens take a token:

  - If the token is an operand then add it to the final sequence;

  - If the token is an operator _(but not a bracket)_:
    - While the stack is not empty and the top of the stack contains an operator with an equal or greater priority (>=), move the stored operator from the stack to the resulting sequence;
    - Store this new operator to the stack.
  - _If the token is an opening bracket then push it to the stack;_
  - _If the token is a closing bracket, then we take the operators from the stack and save them in the resulting sequence until we meet an opening bracket. If an opening bracket is not found, then we signal about the error in the expression._
- Take the saved operators from the stack and move them to the final sequence. _If there is an opening bracket among the operators, then we signal about the error in the expression_.

The full implementation can be found on [GitHub](https://github.com/itdranik/coding-interview/) (the coding-interview project). This will finish the review of the shunting-yard algorithm, and without wasting time, we're moving on to the tokenization algorithm described in the [next post](/en/math-expressions-tokenization/).
