---
_edit_last: "2"
_last_editor_used_jetpack: block-editor
_thumbnail_id: "1731"
_wp_old_date: "2020-03-01"
_wpas_done_all: "1"
author: yauheni.shybeka
topics:
  - coding-interview-en
cover:
  alt: math-expressions-feature-image
  image: /wp-content/uploads/2020/02/math-expressions-feature-image-1.jpg
date: "2020-02-29T08:00:00+00:00"
guid: https://itdranik.com/?p=588
language:
  - English
parent_post_id: null
post_id: "588"
post_translations:
  - pll_5e4809c57cc17
tags:
  - c#
  - mathematical-expressions
  - parser
title: Parsing Math Expressions in C#. Tokenization
url: /en/math-expressions-tokenization-en/

---
The science is constantly evolving, the technology is rapidly breaking into our lives, new tools and opportunities are appearing, the only one thing that is permanent is the gloomy expression of the face of your interviewer. And since we've figured out how to read the emotions of the interviewer, then we still have problems with reading mathematical expressions. Therefore, in this post, we will analyze the simplest topic in the context of this task - splitting a source string into separate tokens.

As has been mentioned in the previous posts about the [postfix notation](/en/math-expressions-postfix-notation-en/) and [shunting-yard algorithm](/en/math-expressions-shunting-yard-algorithm-en/). We have the following types of tokens:

1. Arithmetic signs: `+, -, *, /`;
1. Round brackets `(, )`;
1. Real numbers; for simplicity let's use only integer numbers and the numbers with a dot delimiter;
1. Space is the only delimiter for tokens.

Let's create a body for the main method in C#. We're going to read an expression character by character and process them in-place. For our case, it's enough while other lexical analyzers can require more complex approaches. [In the very book](https://en.wikipedia.org/wiki/Compilers:_Principles,_Techniques,_and_Tools), this topic is described quite well.

First, we're implementing the simplest case: an input line contains only one number, there are neither spaces no operators:

- While there are characters read a character and add it to special storage;
- If the storage is not empty then create a number based on the characters and add the number to the resulting sequence.

Let's complicate the task a bit by allowing the use of several numbers separated by spaces. Then the conversion into tokens can be represented as follows:

- While there are characters take a character:

  - If the character is a separator (a space in our case), then convert the previously saved characters in the storage to a number and add the number to the resulting sequence;

  - Otherwise, add the character to the numbers storage.
- If the storage contains characters then convert the characters to a number and add the number to the resulting sequence.

The last thing we need to add is a processor for operators. As in our problem statement, every operator can be represented by only a single character then when we meet an operator character during the processing we can convert it and put it to the resulting sequence at once. Then the final algorithm looks like this:

- While there are characters take a character:
  - If the character is a separator (a space in our case), then convert the previously saved characters in the numbers storage to a number and add the number to the resulting sequence;
  - If the character is an operator then convert the previously saved characters in the numbers storage to a number and add the number to the resulting sequence, after which add the operator token to the resulting sequence;
  - Otherwise, add the character to the numbers storage.
- If the storage contains characters then convert the characters to a number and add the number to the resulting sequence.

As always, the full implementation can be found on the [GitHub](https://github.com/itdranik/coding-interview-en/) of the coding-interview-en project, while with the post we are finishing the topic of parsing mathematical expressions. I hope that everything has been informative and interesting. See you!
