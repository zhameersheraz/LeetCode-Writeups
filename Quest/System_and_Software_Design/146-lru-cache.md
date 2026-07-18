# 146. Q1 LRU Cache

Writeup by: zham

- **Difficulty:** Medium
- **Topic:** Design, Hash Table, Linked List
- **Platform:** LeetCode (Quest - Cache System Design)
- **Language:** Java

---

## Description

> Design a data structure that follows the constraints of a **Least Recently Used (LRU) cache**.
>
> Implement the `LRUCache` class:
>
> - `LRUCache(int capacity)` Initialize the LRU cache with positive size `capacity`.
> - `int get(int key)` Return the value of the `key` if the key exists, otherwise return `-1`.
> - `void put(int key, int value)` Update the value of the `key` if the `key` exists. Otherwise, add the key-value pair to the cache. If the number of keys exceeds the `capacity` from this operation, **evict** the least recently used key.
>
> The functions `get` and `put` must each run in `O(1)` average time complexity.
>
> **Example 1:**
>
> ```
> Input
> ["LRUCache", "put", "put", "get", "put", "get", "put", "get", "get", "get"]
> [[2], [1, 1], [2, 2], [1], [3, 3], [2], [4, 4], [1], [3], [4]]
> Output
> [null, null, null, 1, null, -1, null, -1, 3, 4]
>
> Explanation
> LRUCache lRUCache = new LRUCache(2);
> lRUCache.put(1, 1); // cache is {1=1}
> lRUCache.put(2, 2); // cache is {1=1, 2=2}
> lRUCache.get(1);    // return 1
> lRUCache.put(3, 3); // LRU key was 2, evicts key 2, cache is {1=1, 3=3}
> lRUCache.get(2);    // returns -1 (not found)
> lRUCache.put(4, 4); // LRU key was 1, evicts key 1, cache is {4=4, 3=3}
> lRUCache.get(1);    // return -1 (not found)
> lRUCache.get(3);    // return 3
> lRUCache.get(4);    // return 4
> ```
>
> **Constraints:**
>
> - `1 <= capacity <= 3000`
> - `0 <= key <= 10^4`
> - `0 <= value <= 10^5`
> - At most `2 * 10^5` calls will be made to `get` and `put`.

---

## How I thought about it

The "classic" answer here is to write a custom doubly-linked list plus a hash map. Every `get` and `put` does the dance: find the node in O(1), move it to the front in O(1), and on overflow, drop the tail in O(1). That works, but the code is over 100 lines of pointer juggling.

The thing that clicked for me: Java's `LinkedHashMap` already does exactly that, if I tell it to use access order instead of insertion order. Pass `true` as the third constructor argument, and every `get` or `put` re-orders the entry to the back. So the front of the map becomes the least-recently-used entry.

For the eviction, `LinkedHashMap` has a hook called `removeEldestEntry` that gets called automatically on every `put`. I just override it to return `true` when the map is over capacity. The map evicts the oldest entry for me.

That collapses the whole problem into a few lines. The runtime is O(1) average per operation, same as the manual implementation, because `LinkedHashMap` is doing the same work under the hood.

---

## Solution

```java
import java.util.LinkedHashMap;
import java.util.Map;

class LRUCache {
    private final int capacity;
    private final LinkedHashMap<Integer, Integer> cache;

    public LRUCache(int capacity) {
        this.capacity = capacity;
        this.cache = new LinkedHashMap<Integer, Integer>(capacity, 0.75f, true) {
            @Override
            protected boolean removeEldestEntry(Map.Entry<Integer, Integer> eldest) {
                return size() > capacity;
            }
        };
    }

    public int get(int key) {
        return cache.getOrDefault(key, -1);
    }

    public void put(int key, int value) {
        cache.put(key, value);
    }
}
```

---

## Walking through an example

Capacity is 2. Operations:

| step        | action             | cache (oldest to newest) |
|-------------|--------------------|--------------------------|
| put(1, 1)   | add 1, evict none  | {1=1}                    |
| put(2, 2)   | add 2, evict none  | {1=1, 2=2}               |
| get(1)      | 1 was LRU, now MRU | {2=2, 1=1}               |
| put(3, 3)   | 2 is LRU, evict 2  | {1=1, 3=3}               |
| get(2)      | miss               | {1=1, 3=3}, returns -1   |
| put(4, 4)   | 1 is LRU, evict 1  | {3=3, 4=4}               |
| get(1)      | miss               | {3=3, 4=4}, returns -1   |
| get(3)      | 3 was LRU, now MRU | {4=4, 3=3}, returns 3   |
| get(4)      | 4 was LRU, now MRU | {3=3, 4=4}, returns 4   |

The two `get` operations count as "use", so they bump the entry to the most-recently-used end. That is the key LRU idea: reading is also a form of using.

---

## Cost

- **Time:** O(1) average for `get` and `put`. The hash map lookup is O(1) and the linked list re-ordering is O(1) since each entry has direct pointers to its neighbors.
- **Space:** O(capacity), the size of the cache.

---

## What I took away from this one

The first time I saw this problem, the canonical answer was a hundred-plus lines of doubly-linked list and hash map code. That is what interviewers used to expect, and it is what I would have written before. The trick that is not obvious until you have seen it is that the standard library already has this data structure, just configured a certain way.

`LinkedHashMap` with `accessOrder = true` is the LRU. Plus `removeEldestEntry` is the eviction hook. Once you see those two pieces, the whole class is a wrapper. In an interview I would still mention that the underlying implementation is a doubly-linked list, because the interviewer wants to know I understand what is happening, not just that I can call a method. But the actual code I would write is the short version.

The "read counts as a use" idea is the part that surprised me most. `get(1)` after `put(1, 1)` and `put(2, 2)` should still let `1` stay in the cache when the next `put` comes, because we just touched it. The LRU is about "least recently used", not "least recently inserted". Reordering on `get` is what makes the data structure work.
