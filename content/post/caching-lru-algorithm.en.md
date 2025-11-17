---
title: 'Popular Caching Algorithms: LRU Cache'
author: yauheni.shybeka
categories:
  - coding-interview
image: /wp-content/uploads/2020/03/caching-feature-image.png
date: "2020-03-22T19:15:11+00:00"
language:
  - English
tags:
  - c#
  - caching
  - data-structures

---
Hello dear reader!

A technical interview is a process of solving interesting algorithmic problems that most likely won't be useful to you or your interviewer in a professional career, except at the next technical interview. Nevertheless, such tasks are very popular interview tasks, because they allow an interviewer to evaluate candidate’s skills in finding constraints, to follow his or her thinking process, and also to measure the speed of writing **for**-loops in notepad. One type of popular tasks is the cache implementation task.

**Cache** is a software or hardware data storage with high-speed access. For example, a cache is often used to save calculation results in order to reduce the number of repeated calculations. A cache can be used to save the results of access to some storage with a lower access speed. In any case, since the cache only duplicates some data or results that can be obtained again, typically the cache has only temporary memory. Though not as temporary as that of the recruiter who promised to call back, but did not call.

Typically, the cache interface can be represented with the following set of operations:

1. Add an element by a key;
1. Get an element by a key;
1. Remove an element by a key;
1. Clear the cache.

At the same time, since we can infinitely add elements to the cache only in theory, the cache should also describe a strategy for discarding elements. For example, the strategy should determine the element which should be discarded when the maximum number of allowed elements is exceeded.

**LRU (least recently used)** is the strategy of discarding an element that has not been used the longest. It is the element that hasn't been accessed by a key the longest (methods of adding and receiving a value).

The complete implementation of LRU cache in C#, as usual, can be found on the [GitHub](https://github.com/itdranik/coding-interview/) of the coding-interview project.

Let's start with the implementation of the simplest case when we don't have a discarding strategy. In other words, add a wrapper around the standard implementation of the associative array, which usually comes with the standard library of [your favorite programming language](https://wiki.theory.org/index.php/YourLanguageSucks):

Now let's limit our cache capacity to some maximum allowed number of elements ( `capacity`). And let's create a common access counter ( `totalAccessSteps`), which will be increased each time we access an element. We need also to store access time for each saved element ( `accessStepsByKey`), which is necessary to determine the element that has not been used the longest. Looking ahead, we should note that this implementation is not optimal, it will be improved in the future.

Next, we're going to update the most obvious cache operations - removal of an element and cleaning of the cache:

Consider the operation of adding a new element to the cache. The following three cases may occur, depending on the situation:

1. If this element exists in the cache, then update its value and access time;
1. If the cache contains fewer elements than its capacity, then simply save this new element;
1. If the cache already contains the maximum allowed number of elements, then delete the element that has not been used the longest and save this new element.

Note that updating the value of an element, in this case, is equivalent to deleting the element and adding it again with another value since we still need to change its access time. So we get the following implementation of adding an element:

The operation of accessing an element by a key should also change the access time of the element, therefore, we will use the implemented method `Update`:

As noted above, this implementation is not optimal, which is of course immediately noticed by the interviewer who sarcastically reports that in the book it's written differently. The main problem is with the `Evict` method which looks for an element with the earliest access time using brute force. Well, why not do an optimization then?

As our operations over the cache are called sequentially, we can arrange our items in the list in the order they have been accessed from earlier to later (a key is written to the left and value to the right):

{{< figure src="/wp-content/uploads/2020/03/lru-cache-original.png" alt="LRU cache items" caption="LRU cache items" >}}

Let's see how the list is changed under the influence of various operations. **The operations of clearing the cache and removing a certain element are obvious**. Therefore, we consider the remaining operations (hereinafter, in red we will show the removal of some vertex, and in blue - the addition):

- **The operation of adding a new element when the cache contains fewer elements than the maximum allowed value**. In this case, a new element is added at the end of the list, since in any case, its time of addition will be later than the access time of any other element:

{{< figure src="/wp-content/uploads/2020/03/lru-cache-add-new-1.png" alt="Adding an item to LRU cache" caption="Adding an item to LRU cache" >}}

- **The operation of adding a new element when the cache contains the maximum number of elements**. In this case, the element that is the first in the list is removed, since the access time of it is less than the access time of any other element. After removing, we get a list having the number of elements less than the maximum allowed. That is exactly the same case described in the paragraph above.

{{< figure src="/wp-content/uploads/2020/03/lru-cache-add-new-exceeded-1.png" alt="Adding an item to filled LRU cache" caption="Adding an item to filled LRU cache" >}}

- **The operation of updating an existing element and the operation of obtaining a value by a key**. Both of these cases are equivalent to removing an element and adding it to the end of the list. A new value of the element can be anything (in the example below we have left the original value) since it does not affect the sorting of elements:

{{< figure src="/wp-content/uploads/2020/03/lru-cache-update-1.png" alt="Updating an item in LRU cache" caption="Updating an item in LRU cache" >}}

It remains to learn how to quickly find an element in the list by a key. To do this, we will create a link to an appropriate list node for each key.

Let's rewrite the methods for clearing and removing an element from the cache:

The methods for adding an element (note that the implementation of the main `Add` method remains the same):

And the method of obtaining an element (the implementation of the `TryGet` method almost has not changed):

Here we are, our cache is ready! Now you can safely go for an interview, and I need to say goodbye. See you soon!
