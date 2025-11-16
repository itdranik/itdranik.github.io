---
_edit_last: "2"
_last_editor_used_jetpack: block-editor
_thumbnail_id: "1733"
_wpas_done_all: "1"
author: yauheni.shybeka
topics:
  - coding-interview-en
cover:
  alt: caching-feature-image
  image: /wp-content/uploads/2020/03/caching-feature-image.png
date: "2020-03-28T19:00:32+00:00"
guid: https://itdranik.com/?p=1157
language:
  - English
parent_post_id: null
post_id: "1157"
post_translations:
  - pll_5e4809c57cc17
tags:
  - c#
  - caching
  - data-structures
title: 'Popular Caching Algorithms: LFU Cache'
url: /en/caching-lfu-algorithm-en/

---
Hello, dear reader!

Today's post I would like to start with a standard question at the interview: "What achievement are you most proud of at work?" This question may surprise or even offend because it is not enough today to just perform your duties well, you should be proud of it. But don't let the interviewer take you by surprise. If the room is enough spacious, then it is even better to get out of the chair, your one-man show should light a fire in the interviewer's eyes. Have you recently wrapped some method to `try / catch`? Do not be shy - you have already warned the company against an exceptional situation! Did you fix another bug a month ago? It was not just a fix, it was saving the company from potential bankruptcy! And here the main thing is not to overdo, because the interviewer most likely also visits interviews quite often, and more than once has already "saved the company from bankruptcy". And it’s better, of course, to prepare for this question in advance.

But this is a standard question. And we will return to a non-standard one. In the [previous post](/en/caching-lru-algorithm-en/), we have already got acquainted with caching and the LRU algorithm of element eviction. I propose to consider the following example:

In the console, we will see `"Element 'x' was not found"`. Thus, the `x` element will be forced out of the cache, to which there were 100 times more accesses, but the LRU strategy won't take this into account. Although this example is quite far-fetched, it illustrates well the limitations of the LRU strategy. In this post, we will consider a new strategy that takes into account the frequency of calls to elements. Let's go!

**LFU (least frequently used)** is the strategy of discarding an element that has been least used. This element is the element that has been accessed by a key (methods of adding and receiving values) the least number of times. If there are several such elements, then the element that has not been accessed the longest is discarded.

The complete implementation of LFU cache, as usual, can be found on the [GitHub](https://github.com/itdranik/coding-interview/) coding-interview-en project.

And we're returning back to the implementation. Let's start with the simplest option. We will implement a wrapper around an associative array without an eviction strategy:

Now let’s limit our cache capacity to some maximum allowed number of elements ( `capacity`). And let’s create a common access counter ( `totalAccessSteps`), which will be increased each time we access an element. We need also to store access time for each saved element ( `accessStepsByKey`) and the number of accesses per element ( `frequenciesByKey`). Looking ahead, we should note that this implementation is not optimal, it will be improved in the future.

Let's update the most obvious cache operations – removal of an element and cleaning of the cache

Consider, please, the operation of adding a new element to the cache. The following three cases may occur, depending on the situation:

1. If this element exists in the cache, then update its value, access frequency and time;
1. If the cache contains fewer elements than its capacity, then simply save this new element;
1. If the cache already contains the maximum allowed number of elements, then delete the element that has been least used. If there are several such elements then remove the element that has not been used the longest. If there are several such elements, then remove the element that was accessed the earliest. Then add this new element.

The operation of accessing an element by a key should also change the access time and frequency of the element, therefore, we will use the implemented method `Update`:

As noted above, this implementation is not optimal. The main problem is with the `Evict` method which looks for an element that should be evicted using brute force. Well, let's optimize it!

We will divide our elements into groups according to the frequency of use. In each group, we will arrange the elements in the order they are added from earlier to later. We will arrange frequency groups vertically, and we will arrange elements in each group horizontally (squares indicate frequency groups, and circles indicate elements, where the key is written to the left while the value is written to the right):

Let's see how the structure is changed under the influence of various operations. **The clear cache operation is obvious**. Therefore, we will consider the remaining operations (hereinafter, in red we will show the removal of a vertex or group, and in blue - addition):

{{< figure src="/wp-content/uploads/2020/03/lfu-cache-original.png" alt="Elements in LFU cache" caption="Elements in LFU cache" >}}

- **The operation of removing an element from the cache**. In this case, we remove the element from the frequency group. If the element is the last in the group, then we delete this frequency group too:

{{< figure src="/wp-content/uploads/2020/03/lfu-cache-removal.png" alt="Removing an element from LFU cache" caption="Removing an element from LFU cache" >}}

{{< figure src="/wp-content/uploads/2020/03/lfu-cache-removal-last.png" alt="Removing an element and its frequency group from LFU cache" caption="Removing an element and its frequency group from LFU cache" >}}

- **The operation of adding a new element if the cache contains fewer elements than the maximum allowed value.** In this case, a new element is added at the end of the list of the group with the access frequency equals to one:

{{< figure src="/wp-content/uploads/2020/03/lfu-cache-add-new.png" alt="Adding an element to LFU cache" caption="Adding an element to LFU cache" >}}

- **The operation of adding a new element if the cache contains the maximum allowed number of elements.** In this case, the element that is first in the list of the group with the lowest access frequency is removed, since the access time to it is less than the access time to any other element. After removal, we get a structure in which the number of elements is less than the maximum allowable. That is exactly the same case described in the paragraph above:

{{< figure src="/wp-content/uploads/2020/03/lfu-cache-add-new-exceeded.png" alt="Adding an element with eviction to LFU cache" caption="Adding an element with eviction to LFU cache" >}}

- **The operation of updating an existing element.** In this case, the new value of an element can be any, since it does not affect the sorting of elements. Note, please, that if the item is the last in the group, then this group is removed. If necessary, a new frequency group is created:

{{< figure src="/wp-content/uploads/2020/03/lfu-cache-update.png" alt="Updating an element in LFU cache" caption="Updating an element in LFU cache" >}}

{{< figure src="/wp-content/uploads/2020/03/lfu-cache-update-group.png" alt="Updating an element and its frequency group in LFU cache" caption="Updating an element and its frequency group in LFU cache" >}}

- **The operation of getting an element by a key.** This operation is equivalent to updating an existing element while keeping its original value.

It remains to learn how to quickly find an element in the list by a key. To do this, we will create a link to an appropriate list node for each key. To quickly search for a frequency group, we will also store for each frequency a link to the corresponding frequency group.

Let's rewrite the methods for clearing and removing an element from the cache:

The methods for adding an element (note that the implementation of the main `Add` method remains the same):

And the method of obtaining an element (the implementation of the `TryGet` method almost has not changed):

This completes the implementation of LFU caching. I need to say goodbye. And I'd like to ask you, dear reader, to familiarize yourself also with the other reviews of [coding-interview-en problems](/en/topics/coding-interview-en-en/). See you soon!
