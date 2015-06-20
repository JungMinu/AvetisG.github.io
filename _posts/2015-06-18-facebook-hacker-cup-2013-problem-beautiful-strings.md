---
layout: post
title: "Facebook Hacker Cup 2013 Problem: Beautiful Strings"
---

So if you have been reading my blogs then you would know I am a CodeEval junkie (other challenge websites as well but CodeEval is one of the favorites).

There was a certain fun problem which stood out to me and the reason being was that it reminded me of a few lessons I learned in the beginning of my career, which I will be sharing in this post.

First of all let’s take a look at the problem and then solve it by breaking it down and coding it up. 

Finally, I will share the lessons I have learned from this simple yet fascinating problem.

This problem originally appeared in the Facebook Hacker Cup 2013 Hackathon and the name of it is Beautiful Strings.

Here is the original problem description.

> When John was a little kid he didn't have much to do. There was no internet, no Facebook, and no programs to hack on. So he did the only thing he could... he evaluated the beauty of strings in a quest to discover the most beautiful string in the world. 

> Given a string s, little Johnny defined the beauty of the string as the sum of the beauty of the letters in it. The beauty of each letter is an integer between 1 and 26, inclusive, and no two letters have the same beauty. Johnny doesn't care about whether letters are uppercase or lowercase, so that doesn't affect the beauty of a letter. (Uppercase 'F' is exactly as beautiful as lowercase 'f', for example.) 

> You're a student writing a report on the youth of this famous hacker. You found the string that Johnny considered most beautiful. What is the maximum possible beauty of this string?

Here are some of the original inputs:

> Your program should accept as its first argument a path to a filename. Each line in this file has a sentence.  

> 1) ABbCcc

> 2) Good luck in the Facebook Hacker Cup this year!

> 3) Ignore punctuation, please :)

> 4) Sometimes test cases are hard to make up.

> 5) So I just go consult Professor Dalves

And here are the outputs for them:

> Print out the maximum beauty for the string. E.g. 

> 1) 152

> 2) 754

> 3) 491

> 4) 729

> 5) 646

Now the first question that would naturally arise is **how in the world do we calculate a beautiful string?** 

That's a great question and pretty much the key to finding the solution.

The way I found the answer for it is by trial and error. Here is my thought process:

I thought that if it goes all the way from 1 to 26 then we already have boundaries and so something is high and something is low.

Since in our case we are talking about the alphabet I thought of A being the highest, B being in the middle and C being in the very end.

I then went ahead and did this calculation `A = 1, B = 2, C = 3` (the numbers being the amount of times they have occurred). Even though it says "no two letters have the same beauty" I just assumed that it meant two different letters instead of the same letter repeating. If it didn't work then I would simple invalidate my assumption and eliminate that as a possibility, so no harm no foul.

After that I did `(1*26) + (2*25) + (3*24) = 148` and this was not the output expected yet it was close.

Since I love trying different possibilities when I am lost I went ahead and did the opposite `(3*26) + (2*25) + (1*24) = 152` Voila!

I got the answer but still couldn't answer the question as to how to find the rest of the letters since there are so many unknowns.

The thought at that point that came to my mind was as follows: I realized that instead of assigning the highest beauty value to the top most letter in the alphabet I should assign the beauty value to the letter with the **highest frequency** in a descending order.

This is exactly what the first example demonstrated.

This was still an assumption but I was confident enough to code up a solution and see if my assumption would be validated at last.

There was one more decision I had to make and that was whether to include punctuation marks and spaces as well. Since I made so many assumptions I went ahead and made another one after reading the problem description.

Since in the problem description the word letter was mentioned over and over I took it as a hint that they wanted to exclude any punctuation marks along with spaces since those technically don't count as letters.

However, this assumption could have been wrong because most times if the problem doesn't explicitly exclude or include possibilities then you don't want to make up your own.

This was just one of those cases where I couldn't ask anybody around me and so the only way to go was to make an assumption and try to validate it or throw away based on results.

I took C# as a weapon of choice and started coding away.

I started from the main method and wrote it all up as how I imagined it to be. Some functions still nonexistent, but the skeleton complete.

```C#
static void Main(string[] args)
{
	if (args[0] != String.Empty)
	{
		IEnumerable<string> linesOfFile = ReadFile(args[0]);

		foreach (var lineOfFile in linesOfFile)
		{
			Console.WriteLine(GetStringBeauty((lineOfFile.ToLower()).ToCharArray()));
		}
	}
}
```

Then I took care of some simple needs such as being able to get information from a file:

```C#
static IEnumerable<string> ReadFile(string filePath)
{
	string[] fileLines = { };

	try
	{
		fileLines = File.ReadAllLines(filePath);
	}
	catch (Exception e)
	{
		throw e.InnerException;
	}

	return fileLines.ToList();
}
```

Finally the most interesting part for me was to get the beauty of a string given an array of characters representing the sentence or single line from the given file.

```C#
private static string GetStringBeauty(char[] characterCollection)
{
	int beautifulStringValue = 0;
	int beautyValue = 26;

	Dictionary<char, int> characterOccurance = new Dictionary<char, int>();

	foreach (var character in characterCollection)
	{
		if (character != ' ' && Char.IsLetter(character))
		{
			if (!characterOccurance.ContainsKey(character))
			{
				characterOccurance.Add(character, characterCollection.Count(a => a == character));
			}
		}
	}

	IOrderedEnumerable<KeyValuePair<char, int>> orderedCharacterOccurance = characterOccurance.OrderByDescending(a => a.Value);

	foreach (var orderedCharacter in orderedCharacterOccurance)
	{
		beautifulStringValue = beautifulStringValue + (beautyValue * orderedCharacter.Value);

		beautyValue = beautyValue - 1;
	}

	return beautifulStringValue.ToString();
}
```

The entire thing looks like this:

```C#
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;

namespace CodeEvalSolutions.BeautifulStrings
{
	class BeautifulStringsSolution
	{
		static void Main(string[] args)
		{
			if (args[0] != String.Empty)
			{
				IEnumerable<string> linesOfFile = ReadFile(args[0]);

				foreach (var lineOfFile in linesOfFile)
				{
					Console.WriteLine(GetStringBeauty((lineOfFile.ToLower()).ToCharArray()));
				}
			}
		}

		private static string GetStringBeauty(char[] characterCollection)
		{
			int beautifulStringValue = 0;
            int beautyValue = 26;

			Dictionary<char, int> characterOccurance = new Dictionary<char, int>();

			foreach (var character in characterCollection)
			{
				if (character != ' ' && Char.IsLetter(character))
				{
					if (!characterOccurance.ContainsKey(character))
					{
						characterOccurance.Add(character, characterCollection.Count(a => a == character));
					}
				}
			}

			IOrderedEnumerable<KeyValuePair<char, int>> orderedCharacterOccurance = characterOccurance.OrderByDescending(a => a.Value);

			foreach (var orderedCharacter in orderedCharacterOccurance)
			{
				beautifulStringValue = beautifulStringValue + (beautyValue * orderedCharacter.Value);

				beautyValue = beautyValue - 1;
			}

			return beautifulStringValue.ToString();
		}

		static IEnumerable<string> ReadFile(string filePath)
		{
			string[] fileLines = { };

			try
			{
				fileLines = File.ReadAllLines(filePath);
			}
			catch (Exception e)
			{
				throw e.InnerException;
			}

			return fileLines.ToList();
		}
	}
}
```

You can also check it out at my GitHub repository: [Beautiful Strings Solution](https://github.com/AvetisG/CodeEval/blob/master/01%20-%20Easy/C%23/BeautifulStrings/BeautifulStringsSolution.cs).

Finally, here are the lessons I have learned along the way:

> No matter how simple a problem is - **always give 100% of yourself to it** instead of assuming that you are too good to be engaged for it.

> Requirements are not always going to be clear, but it is your responsibility to do the best you can, to clarify them for yourself.

> Making assumptions is OK as long as you verify them at the end and do your best to understand why that assumption worked so that next time faced with a similar problem you don't have to make assumptions.

That's it ladies and gentlemen. I hope it helped you in some way and reminded you of a few lessons that you have learned in the past. 

I would definitely recommend you solving at least one problem a day if you are serious about developing your craft.

Solving problem a day keeps complacency away. But, seriously, it's fun so why not? :)

