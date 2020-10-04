---
title: Roman Numerals Kata in Ruby, Take II
date: 2012-01-28 11:34:00.000000000 -06:00
categories: ruby kata
---
Last week I posted a <a href="/geek-ballet-roman-numerals-kata-in-ruby/">screencast</a> of my take on the Roman Numerals kata in Ruby. It was <a href="http://coreyhaines.com" target="_blank">Corey Haines</a> who encouraged me to do that, and he was gracious enough to take the time to watch it and offer some suggestions. We both thought it might be interesting if I did another iteration of it and discussed the changes. His two main concerns were that (1) it would have been nice to see the pattern extracted sooner and (2) there must be a way to introduce the zero case without blowing up all the tests.

Here is the first video for reference:

<iframe src="//player.vimeo.com/video/35429698" width="500" height="300" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>

<em>It would have been nice to see the pattern extracted sooner.</em> In the first version, I let quite a few similar cases accumulate before making the inconsequential adjustments necessary to cultivate the uniformity that opened the door to bringing them together in the loop body (about 6 minutes in). The kata process of solving the same problem over and over has led me to a question I have not faced before: how much of the knowledge acquired in previous runs is it fair to apply in the current one? When do we rehearse the discovery process, and when do we simply benefit from the discovery? I've got a feeling I could spend the rest of my life pondering this question.

<em>There must be a way to introduce the zero case without without blowing up all the tests.</em> This happens around 6:42. I told Corey that this one was a matter of honesty. I knew that introducing the recursion without the zero case would blow everything up because I had seen it happen before. But I thought coding for it without first seeing the test failure felt like cheating, like skipping the first step in the red-green-refactor dance. After giving it more thought, I see where I was off. Only new tests should be red. The red that I allowed to happen was not a new test but a bad refactoring. Yes, test coverage is our safety net while we refactor, but still we should never intentionally fall off the trapeze.

It was short-sighted to introduce the recursion without the trivial exit case in the first place. We all know that such a case is a necessary part of recursion, and it is not cheating to introduce it in a refactoring without waiting for everything to blow up first. I introduced the recursion much earlier this time (1:45). Granted, I knew unfairly that was the direction I would go eventually, but still I coded only what was necessary to get to green. This change in approach addressed both problems; it allowed the pattern to be extracted earlier, and I never broke all my tests. As an added bonus, this also enabled me to shave three minutes off the time.

Here is the new version, set to the fourth movement of Mendelssohn's Piano Trio No. 2 in C minor:

<iframe src="//player.vimeo.com/video/35798908" width="500" height="375" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>
