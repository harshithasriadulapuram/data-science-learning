# LFU Cache Interview Practice in Python

## 1. What Is an LFU Cache?

LFU stands for **Least Frequently Used**.

When the cache reaches capacity, it evicts the item with the lowest access frequency.

If multiple items have the same frequency, a common LFU design evicts the least recently used item among them.

### LRU vs LFU

| Feature | LRU | LFU |
|---|---|---|
| Eviction rule | Least recently used | Least frequently used |
| Tracks | Recency | Frequency and recency |
| Main data structures | Hash map + doubly linked list | Hash maps + frequency lists |
| Typical target complexity | O(1) average | O(1) average |

## 2. Example

Capacity = 2

```text
put(1, 10)
put(2, 20)
get(1)       -> 10; key 1 has frequency 2
put(3, 30)   -> Evicts key 2, whose frequency is 1
get(2)       -> -1
get(3)       -> 30
```

Key 2 is evicted because its frequency is lower than key 1's frequency.

## 3. Design Approach

To implement an LFU cache with O(1) average time for get and put, maintain:

1. **key_to_value:** Maps each key to its value.
2. **key_to_freq:** Maps each key to its access frequency.
3. **freq_to_keys:** Maps a frequency to an ordered collection of keys, ordered by recency.
4. **min_freq:** Stores the minimum frequency currently in the cache.

When keys share a frequency, the oldest key in that frequency's ordered collection is evicted first.

## 4. Python Implementation

```python
from collections import defaultdict, OrderedDict


class LFUCache:
    def __init__(self, capacity):
        self.capacity = capacity
        self.key_to_value = {}
        self.key_to_freq = {}
        self.freq_to_keys = defaultdict(OrderedDict)
        self.min_freq = 0

    def _touch(self, key):
        freq = self.key_to_freq[key]

        # Remove the key from its current frequency group.
        del self.freq_to_keys[freq][key]

        # If this group becomes empty, update min_freq if needed.
        if not self.freq_to_keys[freq]:
            del self.freq_to_keys[freq]

            if self.min_freq == freq:
                self.min_freq += 1

        # Increase the frequency and mark the key most recently used.
        new_freq = freq + 1
        self.key_to_freq[key] = new_freq
        self.freq_to_keys[new_freq][key] = None

    def get(self, key):
        if key not in self.key_to_value:
            return -1

        self._touch(key)
        return self.key_to_value[key]

    def put(self, key, value):
        if self.capacity <= 0:
            return

        if key in self.key_to_value:
            self.key_to_value[key] = value
            self._touch(key)
            return

        # Evict the least frequently used, least recently used key.
        if len(self.key_to_value) >= self.capacity:
            keys = self.freq_to_keys[self.min_freq]
            evicted_key, _ = keys.popitem(last=False)

            del self.key_to_value[evicted_key]
            del self.key_to_freq[evicted_key]

            if not keys:
                del self.freq_to_keys[self.min_freq]

        # New keys begin with frequency 1.
        self.key_to_value[key] = value
        self.key_to_freq[key] = 1
        self.freq_to_keys[1][key] = None
        self.min_freq = 1


cache = LFUCache(2)

cache.put(1, 10)
cache.put(2, 20)

print(cache.get(1))  # 10

cache.put(3, 30)     # Evicts key 2

print(cache.get(2))  # -1
print(cache.get(3))  # 30
print(cache.get(1))  # 10
```

## 5. How the Implementation Works

### On get(key)

- Return -1 if the key does not exist.
- Otherwise, increase its frequency.
- Move it to the most recently used position in the new frequency group.
- Return its value.

### On put(key, value)

- Do nothing if capacity is zero.
- If the key exists, update its value and frequency.
- If the cache is full, evict the oldest key from the minimum-frequency group.
- Insert the new key with frequency 1.
- Reset min_freq to 1 because the new key has frequency 1.

## 6. Complexity Analysis

| Operation | Complexity |
|---|---|
| get | O(1) average |
| put | O(1) average |
| Space | O(capacity) |

These bounds rely on average O(1) dictionary operations and OrderedDict operations.

## 7. Important Edge Cases

- Capacity is zero.
- Getting a key that does not exist.
- Updating an existing key.
- Several keys share the same frequency.
- Evicting the least recently used key among tied frequencies.
- Repeatedly accessing the same key.

## 8. Interview Practice

1. Explain why LFU needs more bookkeeping than LRU.
2. What does min_freq represent?
3. Why does inserting a new key reset min_freq to 1?
4. How is an eviction tie resolved?
5. Why is an ordinary heap not always the simplest solution for O(1) LFU operations?
6. Compare LFU and LRU using a concrete access sequence.

## 9. Self-Test

Without looking at the implementation, explain the role of each data structure and trace the cache after these operations:

```text
put(1, 10)
put(2, 20)
get(1)
put(3, 30)
get(2)
get(3)
```
