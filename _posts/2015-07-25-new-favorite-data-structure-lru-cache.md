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

Let's now try to implement this awesome data structure in C#.

```C#
public class LRUCache<K, V>
{
    private readonly int _maxCapacity = 0;
    private readonly Dictionary<K, Node<V>> _LRUCache;
    private Node<V> head = null;
    private Node<V> tail = null; 

    public LRUCache(int argMaxCapacity)
    {
        _maxCapacity = argMaxCapacity;
        _LRUCache = new Dictionary<K, Node<V>>();
    }

    public void Insert(K key, V value)
    {
        if (_LRUCache.ContainsKey(key))
        {
            Node<V> foundItem = _LRUCache.FirstOrDefault(l => l.Key.Equals(key)).Value;
            MakeMostRecentlyUsed(foundItem);
        }

        if (_LRUCache.Count >= _maxCapacity) RemoveLeastRecentlyUsed();

        Node<V> insertedNode = new Node<V>(value);

        if (head == null)
        {
            head = insertedNode;
            tail = head;
        }
        else MakeMostRecentlyUsed(insertedNode);

        _LRUCache.Add(key, insertedNode);
    }

    public Node<V> GetItem(K key)
    {
        if (!_LRUCache.ContainsKey(key)) return null;
          
        Node<V> foundItem = _LRUCache.FirstOrDefault(l => l.Key.Equals(key)).Value;

        MakeMostRecentlyUsed(foundItem);

        return foundItem;
    }

    public int Size()
    {
        return _LRUCache.Count();
    }

    public string CacheFeed()
    {
        var headReference = head; 
        
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
        var key = _LRUCache.FirstOrDefault(l => l.Value.Equals(tail)).Key;

        _LRUCache.Remove(key);

        tail.Previous.Next = null;
        tail = tail.Previous;
    }

    private void MakeMostRecentlyUsed(Node<V> foundItem)
    {
        if (foundItem.Next == null && foundItem.Previous == null)
        {
            foundItem.Next = head;
            head.Previous = foundItem;
            if (head.Next == null) tail = head;
            head = foundItem;
        }
        else if (foundItem.Next == null && foundItem.Previous != null)
        {
            foundItem.Previous.Next = null;
            tail = foundItem.Previous;
            foundItem.Next = head;
            head.Previous = foundItem;
            head = foundItem;
        }
        else if (foundItem.Next != null && foundItem.Previous != null)
        {
            foundItem.Previous.Next = foundItem.Next;
            foundItem.Next.Previous = foundItem.Previous;
            foundItem.Next = head;
            head.Previous = foundItem;
            head = foundItem;
        }
    }
}
```

The above implementation is for educational purposes only because for now it is not thread-safe and does not accommodate for any scalability issues that might come up.

However, this post is going to be a living document so to speak - meaning that as I am working on this implementation I will also be updating this post.

If you would like to check out the full implementation and follow the process then [watch it on GitHub](https://github.com/AvetisG/LRUCache.NET).

