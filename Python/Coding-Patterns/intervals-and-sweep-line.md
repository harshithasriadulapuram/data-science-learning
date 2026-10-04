
# Intervals and Sweep Line Algorithms in Python

## 1. What Is an Interval?

An interval represents a range between two endpoints.

```python
interval = [start, end]
```

For example:

```python
intervals = [[1, 3], [2, 6], [8, 10], [15, 18]]
```

Each interval represents a range from its start to its end.

Interval problems commonly involve:

- Merging overlapping intervals.
- Inserting a new interval.
- Finding overlapping intervals.
- Scheduling meetings.
- Finding the minimum number of rooms required.
- Finding the maximum number of simultaneous events.

## 2. Understanding Overlapping Intervals

Consider:

```text
[1, 3]
   [2, 6]
```

These intervals overlap because the second interval starts before the first interval ends.

After merging:

```text
[1, 6]
```

Now consider:

```text
[1, 3]  [4, 6]
```

These intervals do not overlap.

For closed intervals, `[1, 3]` and `[3, 6]` overlap at the endpoint `3`.

Whether touching endpoints count as overlap depends on the problem's interval convention.

## 3. Problem 1: Merge Overlapping Intervals

### Problem statement

Given a list of intervals, merge all overlapping intervals.

Example:

```python
intervals = [[1, 3], [2, 6], [8, 10], [15, 18]]
```

Output:

```python
[[1, 6], [8, 10], [15, 18]]
```

### Approach

1. Sort intervals by their starting points.
2. Add the first interval to the result.
3. Compare each subsequent interval with the last merged interval.
4. If they overlap, extend the last interval's endpoint.
5. Otherwise, add a new interval.

### Code

```python
def merge_intervals(intervals):
    if not intervals:
        return []

    intervals.sort(key=lambda interval: interval[0])
    merged = [intervals[0][:]]

    for start, end in intervals[1:]:
        last_end = merged[-1][1]

        if start <= last_end:
            merged[-1][1] = max(last_end, end)
        else:
            merged.append([start, end])

    return merged


print(merge_intervals([
    [1, 3], [2, 6], [8, 10], [15, 18]
]))
```

Output:

```text
[[1, 6], [8, 10], [15, 18]]
```

### Complexity

- Time: `O(N log N)` due to sorting.
- Auxiliary space: `O(N)` for the result, excluding sorting space.

## 4. Problem 2: Insert an Interval

### Problem statement

Given a sorted list of non-overlapping intervals, insert a new interval and merge any overlaps.

Example:

```python
intervals = [[1, 3], [6, 9]]
new_interval = [2, 5]
```

Output:

```python
[[1, 5], [6, 9]]
```

### Code

```python
def insert_interval(intervals, new_interval):
    result = []
    i = 0
    n = len(intervals)

    start, end = new_interval

    # Add intervals that finish before the new interval begins.
    while i < n and intervals[i][1] < start:
        result.append(intervals[i][:])
        i += 1

    # Merge overlapping intervals.
    while i < n and intervals[i][0] <= end:
        start = min(start, intervals[i][0])
        end = max(end, intervals[i][1])
        i += 1

    result.append([start, end])

    # Add remaining intervals.
    while i < n:
        result.append(intervals[i][:])
        i += 1

    return result


print(insert_interval(
    [[1, 3], [6, 9]],
    [2, 5]
))
```

Output:

```text
[[1, 5], [6, 9]]
```

### Complexity

- Time: `O(N)`.
- Auxiliary space: `O(N)` for the output.

The original intervals must already be sorted and non-overlapping for this implementation.

## 5. Problem 3: Meeting Rooms

### Problem statement

Given meeting intervals, determine whether a person can attend every meeting.

Example:

```python
meetings = [[0, 30], [5, 10], [15, 20]]
```

Output:

```text
False
```

The meeting from `0` to `30` overlaps with the other meetings.

### Code

```python
def can_attend_all_meetings(intervals):
    intervals = sorted(intervals, key=lambda x: x[0])

    for i in range(1, len(intervals)):
        if intervals[i][0] < intervals[i - 1][1]:
            return False

    return True


print(can_attend_all_meetings([
    [0, 30], [5, 10], [15, 20]
]))

print(can_attend_all_meetings([
    [0, 10], [10, 20]
]))
```

Output:

```text
False
True
```

This version treats a meeting ending at time `10` and another beginning at time `10` as non-overlapping, assuming the first meeting releases the room at its end time.

### Complexity

- Time: `O(N log N)`.
- Auxiliary space: depends on the sorting implementation and whether a copy is made.

## 6. Problem 4: Minimum Meeting Rooms

### Problem statement

Given meeting intervals, find the minimum number of rooms needed so that no overlapping meetings share a room.

Example:

```python
meetings = [[0, 30], [5, 10], [15, 20]]
```

Output:

```text
2
```

### Approach Using a Min-Heap

The heap stores the ending times of meetings currently assigned to rooms.

1. Sort meetings by start time.
2. If the earliest-ending meeting finishes before or when the next meeting starts, reuse that room.
3. Otherwise, allocate another room.
4. Track the maximum number of rooms needed.

```python
import heapq


def min_meeting_rooms(intervals):
    if not intervals:
        return 0

    intervals = sorted(intervals, key=lambda x: x[0])
    end_times = []

    for start, end in intervals:
        if end_times and end_times[0] <= start:
            heapq.heappop(end_times)

        heapq.heappush(end_times, end)

    return len(end_times)


print(min_meeting_rooms([
    [0, 30], [5, 10], [15, 20]
]))
```

Output:

```text
2
```

### Complexity

- Time: `O(N log N)`.
- Auxiliary space: `O(N)` in the worst case.

## 7. What Is a Sweep Line Algorithm?

A sweep line algorithm processes events in sorted order, as though a vertical or horizontal line were moving across a timeline or geometric space.

Instead of examining every possible point, we process only the important event positions.

For interval problems, common events are:

- An interval starts.
- An interval ends.

We update an active count or active set as these events occur.

Sweep line techniques are useful for:

- Maximum simultaneous meetings.
- Counting overlapping intervals.
- Tracking active events over time.
- Finding intersections between geometric objects.

## 8. Problem 5: Maximum Number of Simultaneous Meetings

### Problem statement

Given intervals representing meetings, find the maximum number of meetings happening at the same time.

Example:

```python
meetings = [[1, 5], [2, 6], [4, 8], [9, 10]]
```

Output:

```text
3
```

At time `4`, the first three meetings overlap.

### Code: Sweep Line

```python
def max_simultaneous_meetings(intervals):
    events = []

    for start, end in intervals:
        events.append((start, 1))
        events.append((end, -1))

    # For half-open intervals [start, end), process end events
    # before start events at the same timestamp.
    events.sort()

    active = 0
    maximum = 0

    for time, change in events:
        active += change
        maximum = max(maximum, active)

    return maximum


print(max_simultaneous_meetings([
    [1, 5], [2, 6], [4, 8], [9, 10]
]))
```

Output:

```text
3
```

### Important endpoint detail

The implementation treats intervals as half-open: `[start, end)`. A meeting ending at time `5` is no longer active when another begins at time `5`.

For closed intervals, where both endpoints count as part of the interval, the event ordering must be adjusted according to the problem's requirements.

### Complexity

- Time: `O(N log N)` because events are sorted.
- Auxiliary space: `O(N)` for the events.

## 9. Problem 6: Remove Covered Intervals

### Problem statement

An interval is covered if another interval completely contains it.

Example:

```python
intervals = [[1, 4], [3, 6], [2, 8]]
```

Output:

```text
2
```

The interval `[1, 4]` is covered by `[2, 8]` only if its start is at least `2`, which it is not. Therefore, this example should be evaluated carefully: `[1, 4]` is not covered by `[2, 8]`, and `[3, 6]` is covered by `[2, 8]`. Two intervals remain.

### Code

```python
def remove_covered_intervals(intervals):
    intervals.sort(
        key=lambda x: (x[0], -x[1])
    )

    count = 0
    farthest_end = float("-inf")

    for start, end in intervals:
        if end > farthest_end:
            count += 1
            farthest_end = end

    return count


print(remove_covered_intervals([
    [1, 4], [3, 6], [2, 8]
]))
```

Output:

```text
3
```

All three intervals remain because neither `[1, 4]` nor `[3, 6]` is completely covered by another interval in this set.

The sorting rule places intervals with the same start in descending order of their endpoints. This helps identify covered intervals correctly.

### Complexity

- Time: `O(N log N)`.
- Auxiliary space: depends on the sorting implementation.

## 10. Choosing the Right Technique

| Problem type | Technique |
|---|---|
| Merge overlapping ranges | Sorting + merging |
| Insert one interval | Linear scan |
| Check meeting conflicts | Sorting |
| Minimum meeting rooms | Min-heap |
| Maximum simultaneous events | Sweep line |
| Count active intervals | Sweep line |
| Track overlapping geometric objects | Sweep line or specialized geometry algorithms |

## 11. Common Mistakes

1. Forgetting to sort intervals by start time.
2. Using `<` when the problem requires `<=`, or vice versa.
3. Confusing overlap with containment.
4. Updating the merged endpoint incorrectly.
5. Forgetting to handle empty input.
6. Using a heap when a simple sorted scan is enough.
7. Ignoring event ordering when multiple events have the same timestamp.
8. Assuming all interval problems use the same endpoint convention.

## 12. Interview Practice Questions

Solve these in order:

1. Merge Intervals.
2. Insert Interval.
3. Meeting Rooms.
4. Meeting Rooms II.
5. Non-overlapping Intervals.
6. Minimum Number of Arrows to Burst Balloons.
7. Remove Covered Intervals.
8. Employee Free Time.
9. My Calendar I.
10. Maximum Overlapping Intervals.

## 13. Final Interview Checklist

Before considering this topic mastered, make sure you can:

- Recognize overlapping and non-overlapping intervals.
- Merge intervals using sorting.
- Insert an interval into an existing sorted list.
- Calculate the minimum rooms needed with a min-heap.
- Explain the sweep line technique.
- Handle equal start and end times correctly.
- Analyze time and space complexity.

**Key takeaway:** Sort intervals when their order matters, use a heap when you must efficiently track the earliest ending event, and use a sweep line when processing changes over time.
