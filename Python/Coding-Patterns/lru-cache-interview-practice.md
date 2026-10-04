# LRU Cache Interview Practice in Python

## 1. What Is an LRU Cache?

LRU stands for **Least Recently Used**.

An LRU cache has a fixed capacity. When it becomes full, it removes the item that has not been accessed for the longest time.

Two operations are usually required:

- `get(key)`: Return the value if the key exists; otherwise return -1.
- `put(key, value)`: Insert or update a value. Evict the least recently used item when necessary.

Both operations should run in **O(1) average time**.

## 2. Example

Capacity = 2

```text
put(1, 10)  -> Cache: {1: 10}
put(2, 20)  -> Cache: {1: 10, 2: 20}
get(1)      -> 10; key 1 becomes most recently used
put(3, 30)  -> Evict key 2
get(2)      -> -1
get(3)      -> 30
```

Key 2 is removed because it was the least recently used item.

## 3. Implementation Using OrderedDict

Python's `collections.OrderedDict` supports efficient movement and removal of entries.

```python
from collections import OrderedDict


class LRUCache:
    def __init__(self, capacity):
        if capacity <= 0:
            raise ValueError("Capacity must be positive")

        self.capacity = capacity
        self.cache = OrderedDict()

    def get(self, key):
        if key not in self.cache:
            return -1

        self.cache.move_to_end(key)
        return self.cache[key]

    def put(self, key, value):
        if key in self.cache:
            self.cache.move_to_end(key)

        self.cache[key] = value

        if len(self.cache) > self.capacity:
            self.cache.popitem(last=False)


cache = LRUCache(2)

cache.put(1, 10)
cache.put(2, 20)

print(cache.get(1))  # 10

cache.put(3, 30)     # Evicts key 2

print(cache.get(2))  # -1
print(cache.get(3))  # 30
```

## 4. How It Works

- The beginning of the OrderedDict represents the least recently used entry.
- The end represents the most recently used entry.
- A successful get moves its key to the end.
- An update also moves its key to the end.
- When capacity is exceeded, popitem(last=False) removes the oldest entry.

## 5. Complexity Analysis

| Operation | Time complexity |
|---|---|
| Get | O(1) average |
| Put | O(1) average |
| Eviction | O(1) average |
| Space | O(capacity) |

## 6. Interview Follow-Up: Build It From Scratch

If an interviewer prohibits OrderedDict, use:

1. A hash map mapping each key to a node.
2. A doubly linked list to maintain usage order.
3. A dummy head and tail to simplify insertion and removal.

The hash map finds a node in O(1) average time. The doubly linked list moves or removes a known node in O(1) time.

### Node Structure

```python
class Node:
    def __init__(self, key=0, value=0):
        self.key = key
        self.value = value
        self.prev = None
        self.next = None
```

A complete from-scratch implementation must implement four helper operations:

- Remove a node from the list.
- Insert a node immediately after the head.
- Move an existing node to the most recently used position.
- Remove the least recently used node before inserting a new entry.

## 7. Common Mistakes

- Forgetting to update usage order after a successful get.
- Forgetting to update usage order when an existing key is overwritten.
- Evicting the most recently used item instead of the least recently used item.
- Failing to handle a missing key.
- Ignoring invalid or zero capacity.

## 8. Practice Questions

1. Implement an LRU cache using OrderedDict.
2. Implement an LRU cache using a hash map and doubly linked list.
3. Explain why a normal list alone is inefficient for frequent lookups.
4. What happens when an existing key is updated?
5. How would you test repeated access to the same key?
6. What is the difference between LRU and LFU (Least Frequently Used)?

## 9. Self-Test

Without looking at the code, explain:

- Why do we need both a hash map and a linked list?
- Why must get() update recency?
- Which item is evicted when the cache is full?
- Why are both get() and put() O(1) on average?
