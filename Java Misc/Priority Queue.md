In Java, `PriorityQueue` is a class that implements the `Queue` interface. It is a **heap-based data structure** that provides efficient access to the smallest (or largest) element in the collection. By default, `PriorityQueue` is a **min-heap**, meaning the smallest element is always at the front of the queue. However, you can customize it to behave as a **max-heap** by providing a custom comparator.

---

### Key Features of `PriorityQueue`:
1. **Ordering:**
   - Elements are ordered according to their natural ordering (if they implement `Comparable`) or by a custom `Comparator`.
   - By default, it is a **min-heap**, so the smallest element is at the front.

2. **Underlying Data Structure:**
   - It is implemented as a **priority heap** (a binary heap).

3. **Thread Safety:**
   - `PriorityQueue` is **not thread-safe**. For thread-safe operations, use `PriorityBlockingQueue`.

4. **Nulls:**
   - `PriorityQueue` does not allow `null` elements.

5. **Time Complexity:**
   - **Insertion (`offer()` or `add()`):** `O(log n)`
   - **Removal (`poll()` or `remove()`):** `O(log n)`
   - **Peek (`peek()`):** `O(1)`

---

### How `PriorityQueue` Works:

#### 1. **Default Behavior (Min-Heap):**
By default, `PriorityQueue` orders elements in **ascending order**. The smallest element is always at the front of the queue.

```java
import java.util.PriorityQueue;

public class PriorityQueueExample {
    public static void main(String[] args) {
        // Create a min-heap (default behavior)
        PriorityQueue<Integer> minHeap = new PriorityQueue<>();

        // Add elements
        minHeap.offer(5);
        minHeap.offer(2);
        minHeap.offer(8);
        minHeap.offer(1);

        // Poll elements (smallest first)
        while (!minHeap.isEmpty()) {
            System.out.print(minHeap.poll() + " "); // Output: 1 2 5 8
        }
    }
}
```

---

#### 2. **Max-Heap Behavior:**
To create a **max-heap**, you can pass a custom comparator (`Collections.reverseOrder()`) to the `PriorityQueue` constructor.

```java
import java.util.PriorityQueue;
import java.util.Collections;

public class PriorityQueueExample {
    public static void main(String[] args) {
        // Create a max-heap using a custom comparator
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());

        // Add elements
        maxHeap.offer(5);
        maxHeap.offer(2);
        maxHeap.offer(8);
        maxHeap.offer(1);

        // Poll elements (largest first)
        while (!maxHeap.isEmpty()) {
            System.out.print(maxHeap.poll() + " "); // Output: 8 5 2 1
        }
    }
}
```

---

#### 3. **Custom Comparator:**
You can define your own comparator to order elements in a specific way. For example, ordering strings by length:

```java
import java.util.PriorityQueue;
import java.util.Comparator;

public class PriorityQueueExample {
    public static void main(String[] args) {
        // Create a PriorityQueue with a custom comparator (order by string length)
        PriorityQueue<String> pq = new PriorityQueue<>(Comparator.comparingInt(String::length));

        // Add elements
        pq.offer("apple");
        pq.offer("banana");
        pq.offer("kiwi");
        pq.offer("mango");

        // Poll elements (shortest first)
        while (!pq.isEmpty()) {
            System.out.print(pq.poll() + " "); // Output: kiwi apple mango banana
        }
    }
}
```

---

### Common Methods of `PriorityQueue`:

| Method            | Description                                                                 |
|-------------------|-----------------------------------------------------------------------------|
| `offer(E e)`      | Inserts the specified element into the queue. Returns `true` if successful. |
| `poll()`          | Retrieves and removes the head of the queue. Returns `null` if empty.       |
| `peek()`          | Retrieves (but does not remove) the head of the queue. Returns `null` if empty. |
| `size()`          | Returns the number of elements in the queue.                                |
| `isEmpty()`       | Returns `true` if the queue is empty.                                       |
| `clear()`         | Removes all elements from the queue.                                        |
| `contains(Object o)` | Returns `true` if the queue contains the specified element.               |

---

### Example: Using `PriorityQueue` for Sorting

You can use `PriorityQueue` to sort elements in ascending or descending order:

```java
import java.util.PriorityQueue;

public class PriorityQueueSort {
    public static void main(String[] args) {
        int[] numbers = {5, 2, 8, 1, 7};

        // Sort in ascending order (min-heap)
        PriorityQueue<Integer> minHeap = new PriorityQueue<>();
        for (int num : numbers) {
            minHeap.offer(num);
        }

        System.out.print("Ascending order: ");
        while (!minHeap.isEmpty()) {
            System.out.print(minHeap.poll() + " "); // Output: 1 2 5 7 8
        }

        // Sort in descending order (max-heap)
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>((a, b) -> b - a);
        for (int num : numbers) {
            maxHeap.offer(num);
        }

        System.out.print("\nDescending order: ");
        while (!maxHeap.isEmpty()) {
            System.out.print(maxHeap.poll() + " "); // Output: 8 7 5 2 1
        }
    }
}
```

---

### When to Use `PriorityQueue`:
- When you need efficient access to the smallest or largest element.
- For problems involving sorting, scheduling, or finding the top `k` elements.
- In algorithms like Dijkstra's shortest path, where you need to process elements in a specific order.

---

### Summary:
- `PriorityQueue` in Java is a heap-based data structure.
- By default, it is a **min-heap**, but you can customize it to be a **max-heap** or use a custom comparator.
- It provides efficient insertion, removal, and peek operations (`O(log n)` and `O(1)` respectively).
- It is widely used in algorithms and problems that require prioritized processing.