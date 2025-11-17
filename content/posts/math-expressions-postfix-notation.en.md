---
_edit_last: "2"
_last_editor_used_jetpack: block-editor
_thumbnail_id: "1731"
_wp_old_date: "2020-02-29"
_wpas_done_all: "1"
author: yauheni.shybeka
topics:
  - coding-interview
cover:
  alt: math-expressions-feature-image
  image: /wp-content/uploads/2020/02/math-expressions-feature-image-1.jpg
date: "2020-02-15T08:00:52+00:00"
guid: https://itdranik.com/?p=98
language:
  - English
parent_post_id: null
post_id: "98"
post_translations:
  - pll_5e4809c57cc17
tags:
  - c#
  - mathematical-expressions
  - parser
title: Parsing Math Expressions in C#. Postfix Notation
url: /en/math-expressions-postfix-notation-en/

---
Well, hello, dear reader!

Who else but we, software engineers, that burned out by the life and burned down by the light of the screen, knows how contradictory, difficult and unjust this world of technical interviews is! Whether your solutions launch spaceships or your service keeps millions of requests, an experienced interviewer will always bring you to light. Aren't you able to immediately recall the difference between the merge sort and insertion sort, don’t you know all the SOLID principles by heart? Oh, my friend, unlike you, the interviewer has prepared more seriously and in the morning has repeated these important topics. Your incompetence is obvious. See you! Bye!

For sure, next time these questions will not take you by surprise, you are amazed yourself if a person cannot immediately implement the heapsort or does not remember by heart all the rules for balancing red-black trees. You are invited to the interview again and isn't it just an amazing coincidence that here is the same interviewer? It is the moment of truth, now you know everything about sorting, you're ready to start, while suddenly you see the problem statement: "Implement a parser for math expressions".

Wholly unexpectable? Sure. So, I suggest right here and now to deal with the theme. Let's begin!

**Problem statement**: implement a parser for mathematical expressions.

Of course, it's quite inconvenient and difficult to work with the formulation, so it's desirable to follow the rule: discuss constraints first, then proceed with an implementation. If you want to put the interviewer in an ecstatic state then it’s worth proposing to register the constraints as a set of tests.

We're introducing the following set of constraints:

- An expression cannot contain variables or named constants; it consists only of numbers, arithmetic signs, and brackets;
- The following arithmetic signs are allowed: `+, -, *, /`;
- Only round brackets are allowed;
- If the expression contains a syntax or computational error, then it is necessary to return an error message.

The difficulty of parsing math expressions lies in the fact that mathematical operations have different priorities, which can be also changed by using brackets. Does it sound complicated? In fact, it’s enough to convert the expression from the infix notation to the postfix notation using the shunting-yard algorithm, do the calculation and that's it! It would seem that we can end the post at this, but most likely the evil interviewer will want to delve into the details, or even worse, will require implementation. Therefore, we continue.

**Infix notation** is a form of writing mathematical expressions where operators (in our case, arithmetic signs) are written between the operands (in our case, numbers) to which they are applied. The order of calculations is determined by the priority of the operators, but can be adjusted by brackets. Thus, the infix notation is the standard form of expression writing that we were taught back in school, for example: `1 + (2 - 3) * 4`.

**Postfix notation** is a form of writing mathematical expressions where operators are written after the operands to which they are applied. For this notation, there is no priority for operations, and therefore there are no brackets. For example, the expression above corresponds to one of the following expressions in the postfix notation: `2 3 - 4 * 1 +` or `4 2 3 - * 1 +`.

The full implementation in C# can be found on [GitHub](https://github.com/itdranik/coding-interview/) (the coding-interview project), but for now, let's analyze the algorithm for evaluating an expression written in the postfix notation. Let's create a class that receives a sequential set of tokens (operators and operands) as the input, and as the output, it returns a result in the form of a calculated operand.

If a token is an operand then we add it to the operands stack.

If the token is an operator, then we get the necessary number of operands from the stack, perform the calculation and push the result of the calculation back to the operands stack. If the stack does not have the necessary number of elements for applying the operator, then we return an error.

It remains only to add the returning of a result, i.e. the only left element in the stack. If after the processing of all the tokens there is more than one element in the stack, this says about the presence of a mismatch between operators and operands. If there are no elements in the stack, then this means that an empty sequence has been fed to the input.

At this, I need to say goodbye while in the [next post](/en/math-expressions-shunting-yard-algorithm-en/) we will analyze the implementation of the shunting-yard algorithm.
