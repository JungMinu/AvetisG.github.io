---
layout: post
title: "Time And Space Efficiency vs Readability"
---

I was very hesitant to write about this topic because it is very much subjective and should be decided on a case by case basis. However, I decided to get over my fear of being criticized because I felt that I really needed to share my experience.

This, in no way, shape or form, is the single source "truth" and is only my humble opinion based on my own experiences and knowledge that I have acquired overtime. If your opinion is completely different please don't hesitate to state it in the comments section because that is the only way we can all learn and grow together. Now that I am done with my disclaimer let's get into it :)

Let me start it this way - if you are a professional software developer then some percentage of the code you write will not stay in the same state forever.

## Change Is Always There

Everything changes starting from small-scale personal projects (open source or commercial) ending with large-scale, complex, enterprise systems, because requirements change, teams change, technologies change.

And of course we should follow the [open/close principle](https://en.wikipedia.org/wiki/Open/closed_principle) as much as we can to battle change, however there are going to be things that we are going to need to change. This is why I explicitly mentioned that **some percentage** of the code will change depending on your specific situation - it is virtually impossible to know how much of the code will change on a case by case basis. 

The bottom line is, however, that continuous maintenance and improvement of your code is necessary for it not to degrade overtime and become a [big ball of mud](https://en.wikipedia.org/wiki/Big_ball_of_mud).

Some people may disagree with this and if that is the case I would love to hear your counter-arguments in the comment section.

Since, we have established that change is in constant motion and that we consistently need to battle it through continuous improvement and maintenance, I can transition to my second point.

## Have To Read The Code

If we know that our code might change at some point we need to take measures to make it more readable for others as well as for ourselves. 

If it is a solo project you are working on then still you know you might come back to it for more work, if it is a large enterprise application then you want others to be able to understand your code. 

Either way you see that readability is important.

Code that is not readable ignites a lot of negative emotions, procrastination and most importantly [heisenbugs](https://en.wikipedia.org/wiki/Heisenbug) when trying to change it.

> So making code less readable is more or less like taking away from its quality.

I think the only time you wouldn't worry about readability is when you are solving problems on websites such as CodeEval, Hackerrank and such, since your only goal is to learn from the challenges and pass them. 

However, I would argue that while you are learning how to solve problems why not also learn how to write clean and readable code? Even if it takes a bit more space. This brings me to my next point...

## How Much To Optimize

This is a tricky question because it totally depends on a specific situation with its own particular requirements. Here is the mantra that I try to follow based on my experience:

> Don't innovate for optimization unless you have to

Let me expand on what I mean by this. Usually, when solutions are not over optimized, they are pretty easy to understand (good readability), but the moment one tries to do fancy tricks code becomes more and more complex to digest. 

This is why I try to stick to this mantra and optimize it only to the limit I have to, not more. The limit is usually the quality of user experience (in your case it might be something else and you need to find out what that is in order to know how much to optimize your code).

Here is an example if you know that for a certain problem you have to look through a list of n items in order to solve it then stick to that vision and go with that. You will have O(n) complexity. If this solution provides users with a great user experience then you have done your job in the optimization department.

Now if your users are still complaining and yelling at you for the software working slow then here is when you have an excuse to start innovating a bit. Before innovating however first look through your current code and see if there is something else you are missing that is slowing it down. Innovating for optimization should be your last resort because it introduces complexity and complexity is not very readable.

So let's put everything together.

Readability is important because change is a constant factor and code entropy is always waiting for us. If we want to keep our code readable we have to introduce less complexity. 

When we are trying to find innovative ways to optimize our code we are inevitably introducing complexity. Unless we **have to** innovate for optimization let's only optimize to the limit that we have to. 

This limit is an arbitrary mark and is different for everyone. Most times it is the users - either for a public API or software/web app/moblie app or others. If optimizing to the limit is not enough then we can start innovating for optimization after we make sure that there is nothing else that we can do.

In this case when introducing **complexity out of necessity**, please make sure that you document everything well and follow the open/close principle.
