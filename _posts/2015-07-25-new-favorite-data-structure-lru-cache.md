---
layout: post
title: "New Favorite Data Structure: LRU Cache"
---

I found out about this data structure recently during my interview at Amazon/Kiva Systems. The reason why it appealed to me so much because it wasn't a single data structure but instead a combination of two.

Before we dive into coding it up and all I would like to first define what LRU cache stands for and what it is.

> LRU cache stands for least recently used cached.

It has a capacity which you set either by default or during initialization. One can insert elements into it and then look them up when needed. 

Here are the key parts:

1) Insertion and lookup both operate on `O(1)` in an LRU cache

2) When inserting an item or looking up an item you make that item as the recently used one

3) When you try inserting an item and it goes over capacity the least recently used one gets removed

Interesting right? So let's do a quick example to completely make sense of the functionality and then we'll go about implementing it.

Imagine if you have an LRU cache that has a capacity of `5`.

You go ahead and you insert `[1]`, then you insert `[2]`, then `[3]`, `[4]`.

The cache should now look as follows: `[4] [3] [2] [1]`. As you can see `[4]` is the most recently used one because it was inserted at the end and `1` is the least recently used one since it is was inserted in the beginning.

Now let's do something interested. Let's call a lookup on `1`. Boom! Now the LRU cache looks like this: `[1] [4] [3] [2]`. As you have noticed the `[1]` became the most recently used one now.

Let's go ahead and insert two more items `[5]` and `[6]`. You already know that when we insert `[6]` it will go over capacity and so one of the items would need to be removed, but which one? Let's see: `[6] [5] [1] [4] [3]`.

If we never did a lookup on our `[1]` then it would have been purged but since we did a lookup first and then started inserting the rest of our items we were able to keep it.

Let's now try to implement this awesome data structure in C#. We will be using a C# dictionaty data structure for `O(1)` lookups and a doubly linked list for `O(1)` insertion and deletion.

Let's start with writing up the Node structure that we will be using to create a doubly linked list.

```C#
public class Node<D, K>
{
    public D Data { get; private set; }
    public K Key { get; private set; }
    public Node<D, K> Previous { get; set; }
    public Node<D, K> Next { get; set; }

    public Node(D data, K key)
    {
        Data = data;
        Key = key;
    }
}
```

As you can see the Node takes in a data and also a value. The data is obviously going to be the data which the user will be caching, however the key is a specificity in our implementation. This can be done in many ways, but one of the ways to do it is to keep track of the key in the Node. But what are we trying to do? Great question.

We are basically saving the key of the node so that we can perform a reverse lookup in O(1) time instead of using `FirstOrDefault()` which would take `O(n)` time. The key allows use to remove the eatery from our dictionary after we get rid of it in the tail of our linked list.

And now let's write out the actual implementation of the LRU cache.

```C#
using System;
using System.Collections.Generic;
using System.Linq;

public class LRUCache<K, V>
{
    private readonly int _maxCapacity = 0;
    private readonly Dictionary<K, Node<V, K>> _LRUCache;
    private Node<V, K> _head = null;
    private Node<V, K> _tail = null;

    public LRUCache(int argMaxCapacity)
    {
        _maxCapacity = argMaxCapacity;
        _LRUCache = new Dictionary<K, Node<V, K>>();
    }

    public void Insert(K key, V value)
    {
        if (_LRUCache.ContainsKey(key))
        {
            MakeMostRecentlyUsed(_LRUCache[key]);
        }

        if (_LRUCache.Count >= _maxCapacity) RemoveLeastRecentlyUsed();

        Node<V, K> insertedNode = new Node<V, K>(value, key);

        if (_head == null)
        {
            _head = insertedNode;
            _tail = _head;
        }
        else MakeMostRecentlyUsed(insertedNode);

        _LRUCache.Add(key, insertedNode);
    }

    public Node<V, K> GetItem(K key)
    {
        if (!_LRUCache.ContainsKey(key)) return null;

        MakeMostRecentlyUsed(_LRUCache[key]);

        return _LRUCache[key];
    }

    public int Size()
    {
        return _LRUCache.Count();
    }

    public string CacheFeed()
    {
        var headReference = _head; 
        
        List<string> items = new List<string>();

        while (headReference != null)
        {
            items.Add(String.Format("[V: {0}]", headReference.Data));
            headReference = headReference.Next;
        }

        return String.Join(",", items);
    }

    private void RemoveLeastRecentlyUsed()
    {
        _LRUCache.Remove(_tail.Key);
        _tail.Previous.Next = null;
        _tail = _tail.Previous;
    }

    private void MakeMostRecentlyUsed(Node<V, K> foundItem)
    {
        if (foundItem.Next == null && foundItem.Previous == null)
        {
            foundItem.Next = _head;
            _head.Previous = foundItem;
            if (_head.Next == null) _tail = _head;
            _head = foundItem;
        }
        else if (foundItem.Next == null && foundItem.Previous != null)
        {
            foundItem.Previous.Next = null;
            _tail = foundItem.Previous;
            foundItem.Next = _head;
            _head.Previous = foundItem;
            _head = foundItem;
        }
        else if (foundItem.Next != null && foundItem.Previous != null)
        {
            foundItem.Previous.Next = foundItem.Next;
            foundItem.Next.Previous = foundItem.Previous;
            foundItem.Next = _head;
            _head.Previous = foundItem;
            _head = foundItem;
        }
    }
}
```

The above implementation is for educational purposes only because for now it is not thread-safe and does not accommodate for any scalability issues that might come up.

However, this post is going to be a living document so to speak - meaning that as I am working on this implementation I will also be updating this post.

If you would like to check out the full implementation and follow the process then [watch it on GitHub](https://github.com/AvetisG/LRUCache.NET).

