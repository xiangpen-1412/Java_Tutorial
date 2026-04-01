# PriorityQueue in Java

## Introduction to PriorityQueue

In Java, a **PriorityQueue** is a type of queue that allows you to store elements according to their priority. Unlike a regular queue (FIFO), a **PriorityQueue** can reorder elements based on their priority, ensuring that the element with the highest priority is always at the front.

### Key Characteristics:

- **Heap-Based**: Java’s `PriorityQueue` is implemented using a **binary heap**.
- **Automatic Sorting**: The elements in the queue are ordered according to their natural ordering or a comparator provided at the time of creation.
- **Partial Sorting**: The queue doesn’t guarantee that all elements are fully sorted. It only ensures that the head of the queue has the element with the highest priority (or smallest, depending on heap type).
- **Allows Duplicates**: Unlike `TreeSet`, `PriorityQueue` allows duplicate elements.

---

## Operations on PriorityQueue

The `PriorityQueue` provides several key operations that allow interaction with the data structure. Below are the common operations:

### 1. **offer(E e)**:

- Adds the specified element to the queue while maintaining the heap property.
- **Time complexity**: O(log n)

### 2. **poll()**:

- Retrieves and removes the head of the queue (the element with the highest priority).
- After polling, the queue reorganizes itself to maintain its heap structure.
- **Time complexity**: O(log n)

### 3. **peek()**:

- Retrieves but does not remove the head of the queue.
- Returns the element with the highest priority without modifying the queue.
- **Time complexity**: O(1)

### 4. **remove(E e)**:

- Removes a specific element from the queue.
- **Time complexity**: O(n), since the queue needs to scan through all the elements to find the specific element to remove.

---

## Default PriorityQueue (Min Heap)

By default, the `PriorityQueue` in Java is a **min heap** where the smallest element is given the highest priority.

### Example (Min Heap):

```java
import java.util.PriorityQueue;

public class MinHeapExample {  
    public static void main(String[] args) {  
        // Create a PriorityQueue (min heap)  
        PriorityQueue<Integer> pq = new PriorityQueue<>();  

        // Add elements  
        pq.offer(5);  
        pq.offer(1);  
        pq.offer(3);  
        pq.offer(8);  
        pq.offer(2);  

        // Print the queue  
        System.out.println("Priority Queue: " + pq);  

        // Poll the highest priority element (smallest element)  
        System.out.println("Poll: " + pq.poll()); // 1  

        // Peek the highest priority element  
        System.out.println("Peek: " + pq.peek()); // 2  

        // Print the remaining queue  
        System.out.println("Remaining Queue: " + pq);  
    }  
}

```

### Output:

```
Priority Queue: [1, 2, 3, 8, 5]  
Poll: 1  
Peek: 2  
Remaining Queue: [2, 5, 3, 8]
```

Explanation:

- Initially, the queue is organized to have 1 at the head, which is the smallest element.
- When `poll()` is called, it removes the smallest element (1).
- After `poll()`, the next smallest element (2) becomes the new head.
- The priority queue automatically maintains the heap structure.

---

## Max Heap with PriorityQueue

To create a **max heap** (where the largest element has the highest priority), you can use a custom comparator.

### Example (Max Heap):

```java
import java.util.PriorityQueue;  
  
public class MaxHeapExample {  
    public static void main(String[] args) {  
        // Create a PriorityQueue with max heap (using custom comparator)  
        PriorityQueue<Integer> pq = new PriorityQueue<>((a, b) -> b - a);  
  
        // Add elements  
        pq.offer(5);  
        pq.offer(1);  
        pq.offer(3);  
        pq.offer(8);  
        pq.offer(2);  
  
        // Print the queue  
        System.out.println("Priority Queue (Max Heap): " + pq);  
  
        // Poll the highest priority element (largest element)  
        System.out.println("Poll: " + pq.poll()); // 8  
  
        // Peek the highest priority element  
        System.out.println("Peek: " + pq.peek()); // 5  
  
        // Print the remaining queue  
        System.out.println("Remaining Queue: " + pq);  
    }  
}

```

### Output:
```
Priority Queue (Max Heap): [8, 5, 3, 1, 2]  
Poll: 8  
Peek: 5  
Remaining Queue: [5, 2, 3, 1]
```

Explanation:

- The largest element (8) is polled first.
- The `PriorityQueue` ensures that after the largest element is polled, the next largest element (5) becomes the head.

---

## Custom Objects in PriorityQueue

You can also use `PriorityQueue` to store custom objects by providing a comparator. For example, suppose you have a list of students and want to prioritize them based on their scores.

### Example:

```java
import java.util.PriorityQueue;  
  
class Student {  
    String name;  
    int score;  
  
    Student(String name, int score) {  
        this.name = name;  
        this.score = score;  
    }  
  
    @Override  
    public String toString() {  
        return name + " (" + score + ")";  
    }  
}  
  
public class CustomObjectExample {  
    public static void main(String[] args) {  
        // Create a PriorityQueue with custom objects (sorting by score)  
        PriorityQueue<Student> pq = new PriorityQueue<>((a, b) -> b.score - a.score);  
  
        // Add students  
        pq.offer(new Student("Alice", 85));  
        pq.offer(new Student("Bob", 92));  
        pq.offer(new Student("Charlie", 78));  
        pq.offer(new Student("David", 95));  
  
        // Poll the highest priority student (with the highest score)  
        System.out.println("Highest priority: " + pq.poll());  
  
        // Print remaining queue  
        System.out.println("Remaining students: " + pq);  
    }  
}
```
### Output:

```
Highest priority: David (95)  
Remaining students: [Bob (92), Alice (85), Charlie (78)]
```

Explanation:

- The `PriorityQueue` is sorted based on the students' scores, and the student with the highest score is polled first.

---

## Use Cases for PriorityQueue

### 1. **Finding the Top K Elements**

A `PriorityQueue` is useful for efficiently finding the top K largest or smallest elements in a dataset. This is particularly valuable when the dataset is large, and sorting the entire collection would be inefficient.

### Example: Find the Top 3 Largest Numbers in an Array

```java
import java.util.PriorityQueue;  
  
public class TopKExample {  
    public static void main(String[] args) {  
        int[] numbers = {7, 1, 10, 3, 20, 15, 8};  
        int k = 3;  
  
        // Create a PriorityQueue (min heap)  
        PriorityQueue<Integer> pq = new PriorityQueue<>();  
  
        for (int num : numbers) {  
            pq.offer(num);  
            if (pq.size() > k) {  
                pq.poll(); // Remove the smallest element  
            }  
        }  
  
        // The remaining elements in the queue are the top K largest elements  
        System.out.println("Top " + k + " largest elements: " + pq);  
    }  
}
```

### Output:

Top 3 largest elements: [10, 15, 20]

Explanation:

- The queue maintains the top 3 elements by constantly removing the smallest element once the queue exceeds size `k`.
- The remaining elements in the queue are the top `k` largest elements.

### 2. **Job Scheduling and Task Prioritization**

A `PriorityQueue` is ideal for job scheduling systems where tasks with higher priority need to be executed first. This is commonly used in operating systems or in applications where you need to manage tasks based on their urgency.

---

## PriorityQueue Performance

- **Time Complexity**:
    - **offer()**: O(log n)
    - **poll()**: O(log n)
    - **peek()**: O(1)
- **Space Complexity**: O(n), where `n` is the number of elements in the queue.

---

## Conclusion

The `PriorityQueue` in Java is an efficient and versatile data structure used for maintaining elements in a specific order of priority. It is particularly useful in scenarios like task scheduling, top K problems, and where you need to ensure that the highest priority element is always accessible at the front of the queue. By understanding how it operates, you can effectively use it for a wide range of applications where priority-based element management is necessary.