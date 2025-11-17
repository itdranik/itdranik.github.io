---
title: Good Bad Code
author: yauheni.shybeka
categories:
  - stories
image: /wp-content/uploads/2021/01/programming-1857236_640.jpg
date: "2021-02-11T11:50:00+00:00"
language:
  - English
tags:
  - work

---
A wild cry rang out of the room: "I don't believe my eyes! For what?" A casual passerby could hear all the stages of grief: from denial to acceptance. No wonder there was a reason — the senior developer Vasily got an inheritance! But not the inheritance from a rich aunt that would make Vasily richer financially. This was the work of dozens of other developers. And judging by the screams that were heard, which it was decided not to quote here, the inheritance did not make also Vasily richer spiritually.

During his working career, Vasily saw it all: huge files of several thousand lines; wise reuse of code through copying; clever optimizations that didn't give a performance increase. And once, in a FORTRAN project, it seemed to him that he had even found dinosaur tracks. But this project was so unique in its kind that Vasily for the first time thought about retirement, to which he was only a little more than thirty years away.

## What Is Bad Code?

> When I was a kid, I used to clean out the manure in the cowshed in the village. I grew up, became a programmer, and nothing has changed.
>
> Taken from [bash.im](https://bash.im/quote/454030)

Well, the concept of bad code is very subjective on its own. Every developer can mentally divide the codebase into two parts: their own code and a disgusting pile of constructs which by a ridiculous coincidence is called code.

In many ways, the attitude to someone else's code depends on the experience, gained through the pain and suffering from developing and maintaining previous projects, as well as the habits developed as a result. Have you ever gone to buy bread at the supermarket where an unexpected reshuffle happened? Passing through different departments you become a happy owner of tea, coffee, or even a TV. But what a disappointment awaits at home when you realize that you have forgotten about the bread!

Some similar feelings come from wandering through someone else's code. Here should be a bakery department, but sports nutrition is sold. Just floor is not in the trend now, so it was decided to update the assortment, but they haven't got down to rename the department. At the same time here you can also find a refrigerator with beer because there was nowhere to cram it. And if you really need bread, then each package of wheat is issued for the purchase of one orange. Terrified, you leave the bike shop and set up a bread-baking factory in your shoe shop.

## How Does Bad Code Appear?

Figured it out! Bad code is any source file of your colleague! Following the amazing conclusion, this could be the end of it, and you can just advise the manager to kick the rest of the developers out of the team (before they get ahead of you). But would that solve the problem, even if the [bus factor](https://en.wikipedia.org/wiki/Bus_factor) were temporarily set aside? Unfortunately, no.

{{< figure src="/wp-content/uploads/2021/02/good-bad-code-colleague-make-changes-en.gif" alt="Your colleague making changes" caption="Your colleague making changes" >}}

Software development in the vast majority of cases is not a source of income in itself, but it is designed to solve problems and optimize business solutions. It is difficult to imagine how the main investor with a glass of good whiskey flips through the source code, reaching nirvana from perfection. Much more likely that you participate in another death march project from the wonderful book " [Death March](https://www.amazon.com/Death-March-2nd-Edward-Yourdon/dp/013143635X)" by E. Yourdon when the task that needs to be closed yesterday, and your manager says that this is the time for making a temporary solution.

## Spiral Degradation

There are more and more temporary solutions, they form the core of the project. A year later, it's hard to believe that this code has been written by you. The project is constantly breaking something, there is no time to make new changes.

{{< figure src="/wp-content/uploads/2021/02/good-bad-code-newbies-en.jpg" alt="Team lead hiding from newbies" caption="Team lead hiding from newbies" >}}

New developers are hired, but the project lacks any documentation, and the comments are more like an annual motivational `TODO` list. Without understanding the subtleties of the project and the depth of your architectural thought, which is already being made up of offensive legends, along with new features, developers add new bugs. You are increasingly forced to spend time reviewing someone else's code and act as interactive documentation.

Gradually, the project is explicitly or implicitly divided into areas of responsibility between developers. Due to the lack of a common approach ( [the spaghetti code](https://en.wikipedia.org/wiki/Spaghetti_code) does not count), each part is developed separately and independently. And everything moves in a spiral:

- Developers mentally begin to divide the codebase into the two parts described earlier;
- There are urgent tasks that need to be closed yesterday;
- Each developer begins to make temporary solutions to their part of the code…

## And What Should We Do?

> Always code as if the guy who ends up maintaining your code will be a violent psychopath who knows where you live.
>
> John Woods

The proposed approach of John Woods has one drawback: the developers would quickly run out, so we'll try to look for more.

Modern development methods involve frequent changes with the possibility of turning the project at any time by 180 degrees. At the same time, the original architecture will probably not be able to support such a reversal, but there is certainly no time to rewrite from scratch. And who can guarantee that it won't happen again in a day?

In the end, all this leads to a deterioration of the codebase — the appearance of the very bad code. At some point, the code becomes simply unsuitable for further maintenance and development, and making changes looks more like walking through a minefield.

That is why it is necessary to pause and allocate time for [refactoring](https://en.wikipedia.org/wiki/Code_refactoring), which will help make some parts of the code more reliable and convenient for changes. Since there won't be time for a complete rework, this code will most likely run counter to the original architecture. And no matter how happy you are with the result, it may still seem bad to an outside observer: it will not be obvious to him all the subtleties and reasons for the changes made. Hence, it turns out that the code is both good and bad. And we all write **good bad code**.
