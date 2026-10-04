
# Sweep Line Algorithm — Interview Practice

## 1. What Is the Sweep Line Algorithm?

The Sweep Line Algorithm solves problems involving overlapping intervals, events, or geometric objects by processing their start and end points in sorted order.

Imagine a vertical line moving from left to right across a set of intervals. As it passes each event, we update the active set of intervals.

Common applications include:

- Finding the maximum number of overlapping intervals.
- Meeting room scheduling.
- Counting concurrent users or processes.
- Detecting overlapping events.
- Calculating covered length.
- Computational geometry problems.

## 2. Core Idea

For each interval, create two events:

- Start event: an interval becomes active.
- End event: an interval stops being active.

Sort the events and process them in order while maintaining the number of active intervals.

The exact order of start and end events at the same coordinate depends on whether the intervals are closed or half-open.

For half-open intervals `[start, end)`, an interval ending at time `t` does not overlap an interval starting at `t`.

---

## 3. Maximum Number of Overlapping Intervals

Given intervals, find the maximum number that overlap at the same time.

### Example

```python
intervals = [(1, 5), (2, 6), (4, 8), (9, 10)]

# Maximum overlap: 3
```

### Solution

```python
def maximum_overlap(intervals):
    events = []

    for start, end in intervals:
        if start > end:
            raise ValueError("Start must not exceed end")

        if start == end:
            continue

        events.append((start, 1))
        events.append((end, -1))

    # For half-open intervals [start, end),
    # process end events before start events
    # at the same coordinate.
    events.sort()

    active = 0
    maximum = 0

    for _, change in events:
        active += change
        maximum = max(maximum, active)

    return maximum


print(maximum_overlap([(1, 5), (2, 6), (4, 8), (9, 10)]))
# 3
```

### Complexity

- Time: O(n log n), due to sorting.
- Auxiliary space: O(n), for the event list.

---

## 4. Minimum Meeting Rooms

Given meeting intervals, find the minimum number of rooms needed so that no overlapping meetings share a room.

For half-open intervals `[start, end)`, a meeting ending at the same time another begins does not require an additional room.

### Solution

```python
def min_meeting_rooms(intervals):
    if not intervals:
        return 0

    starts = sorted(start for start, end in intervals)
    ends = sorted(end for start, end in intervals)

    if any(start > end for start, end in intervals):
        raise ValueError("Start must not exceed end")

    rooms = 0
    max_rooms = 0
    end_index = 0

    for start in starts:
        while end_index < len(ends) and ends[end_index] <= start:
            rooms -= 1
            end_index += 1

        rooms += 1
        max_rooms = max(max_rooms, rooms)

    return max_rooms


print(min_meeting_rooms([(0, 30), (5, 10), (15, 20)]))
# 2

print(min_meeting_rooms([(1, 5), (5, 8)]))
# 1
```

### Complexity

- Time: O(n log n).
- Auxiliary space: O(n).

This solution assumes valid intervals and uses half-open interval semantics.

---

## 5. Count Active Users Over Time

Suppose each user session is represented by a start and end time. Find the maximum number of simultaneously active sessions.

```python
def max_active_sessions(sessions):
    events = []

    for start, end in sessions:
        if start > end:
            raise ValueError("Start must not exceed end")

        if start == end:
            continue

        events.append((start, 1))
        events.append((end, -1))

    events.sort()

    active = 0
    maximum = 0

    for _, change in events:
        active += change
        maximum = max(maximum, active)

    return maximum


sessions = [(1, 4), (2, 5), (3, 6), (7, 9)]
print(max_active_sessions(sessions))
# 3
```

The same sweep-line logic works for sessions, reservations, and other time intervals when the interval boundary rules are consistent.

---

## 6. Find the Length Covered by Intervals

Given intervals on a number line, calculate the total length covered by at least one interval.

For example:

`[(1, 4), (3, 6), (8, 10)]`

The covered regions are `[1, 6]` and `[8, 10]`, so the total length is `5 + 2 = 7`.

```python
def covered_length(intervals):
    if not intervals:
        return 0

    if any(start > end for start, end in intervals):
        raise ValueError("Start must not exceed end")

    intervals = sorted(intervals)

    current_start, current_end = intervals[0]
    total = 0

    for start, end in intervals[1:]:
        if start <= current_end:
            current_end = max(current_end, end)
        else:
            total += current_end - current_start
            current_start, current_end = start, end

    total += current_end - current_start
    return total


print(covered_length([(1, 4), (3, 6), (8, 10)]))
# 7
```

This particular problem can be solved by sorting and merging intervals, which is closely related to sweep-line processing.

**Time complexity:** O(n log n).  
**Auxiliary space:** O(n) for the sorted copy.

---

## 7. Sweep Line for Geometric Events

The same principle extends to geometry.

For example, when processing axis-aligned rectangles, a sweep line can move across x-coordinates while tracking active y-intervals.

Applications include:

- Rectangle overlap detection.
- Union area of rectangles.
- Line segment intersection.
- Closest pair of points.
- Skyline problems.

More advanced geometric algorithms may require balanced trees, heaps, coordinate compression, or segment trees.

---

## 8. Important Boundary Rules

Before writing a sweep-line solution, determine what happens when an interval starts exactly when another ends.

### Half-open intervals: `[start, end)`

- The start is included.
- The end is excluded.
- `[1, 3)` and `[3, 5)` do not overlap.

### Closed intervals: `[start, end]`

- Both endpoints are included.
- `[1, 3]` and `[3, 5]` overlap at point `3`.

This distinction affects event ordering and can change the answer.

---

## 9. Common Mistakes

1. Sorting events without defining tie-breaking rules.
2. Mixing closed intervals with half-open interval assumptions.
3. Counting zero-length intervals as active when the problem treats them as empty.
4. Forgetting to handle empty input.
5. Failing to validate interval boundaries when required.
6. Using a sweep line when a simpler interval-merging solution is sufficient.
7. Assuming every geometric sweep-line problem can be solved with only a counter.

---

## 10. Practice Problems

### Beginner

- Maximum number of overlapping intervals.
- Minimum Meeting Rooms.
- Employee Free Time.
- Merge Intervals.

### Intermediate

- Meeting Rooms II.
- My Calendar I.
- Car Pooling.
- Number of Flowers in Full Bloom.

### Advanced

- The Skyline Problem.
- Rectangle Area II.
- Maximum Sum of Rectangle No Larger Than K.
- Line Segment Intersection.

---

## 11. Interview Checklist

Before coding, ask:

1. What are the events?
2. In what order should events be processed?
3. What happens when events share the same coordinate?
4. What information must be maintained between events?
5. Can sorting alone solve the problem, or do I need a heap or tree?
6. What are the time and space complexities?

### Final Takeaway

The sweep-line technique turns a problem involving many overlapping objects into an ordered sequence of events. Sorting costs O(n log n), and the remaining processing is often linear, provided each event can be handled efficiently.
