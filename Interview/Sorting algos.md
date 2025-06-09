

### **Insertion Sort Overview**

Insertion Sort is a simple and intuitive sorting algorithm. It works by building a sorted portion of the array one element at a time. It iterates through the array, taking one element at a time and inserting it into its correct position in the already sorted portion of the array.

---

### **How Insertion Sort Works**:
1. Start with the second element (index 1) and assume the first element is already sorted.
2. Compare the current element with the elements in the sorted portion (to its left).
3. Shift all elements in the sorted portion that are greater than the current element to the right.
4. Insert the current element into its correct position in the sorted portion.
5. Repeat the process for all elements in the array.

---

### **Time Complexity**:
- **Best Case:** `O(n)` (when the array is already sorted).
- **Average Case:** `O(n^2)`.
- **Worst Case:** `O(n^2)` (when the array is sorted in reverse order).

---

### **Space Complexity**:
- `O(1)` (in-place sorting algorithm).

---

### **Insertion Sort Implementation in Java**

Here’s how you can implement Insertion Sort in Java:

```java
public class InsertionSort {

    public static void insertionSort(int[] arr) {
        int n = arr.length;

        // Start from the second element (index 1)
        for (int i = 1; i < n; i++) {
            int key = arr[i]; // Current element to be inserted
            int j = i - 1; // Start comparing with the previous element

            // Shift elements greater than the key to the right
            while (j >= 0 && arr[j] > key) {
                arr[j + 1] = arr[j]; // Shift element to the right
                j--;
            }

            // Insert the key in its correct position
            arr[j + 1] = key;
        }
    }

    public static void main(String[] args) {
        int[] arr = {12, 11, 13, 5, 6};

        System.out.println("Original array:");
        printArray(arr);

        // Perform insertion sort
        insertionSort(arr);

        System.out.println("Sorted array:");
        printArray(arr);
    }

    // Helper method to print the array
    public static void printArray(int[] arr) {
        for (int num : arr) {
            System.out.print(num + " ");
        }
        System.out.println();
    }
}
```

---

### **Explanation of the Code**:
1. **Outer Loop (`for` loop):**
   - Iterates through the array starting from the second element (`i = 1`).
   - The current element to be inserted is stored in the variable `key`.

2. **Inner Loop (`while` loop):**
   - Compares the `key` with elements in the sorted portion (to its left).
   - Shifts elements greater than the `key` to the right to make space for the `key`.

3. **Insertion:**
   - After finding the correct position, the `key` is inserted into its place.

4. **Helper Method (`printArray`):**
   - A utility method to print the contents of the array.

---

### **Example Walkthrough**:

Let’s sort the array `[12, 11, 13, 5, 6]` using Insertion Sort:

1. **Initial Array:** `[12, 11, 13, 5, 6]`
2. **After 1st Iteration (`i = 1`):**
   - `key = 11`
   - Compare `11` with `12` → Shift `12` to the right.
   - Insert `11` at index `0`.
   - Array: `[11, 12, 13, 5, 6]`
3. **After 2nd Iteration (`i = 2`):**
   - `key = 13`
   - `13` is already in the correct position.
   - Array: `[11, 12, 13, 5, 6]`
4. **After 3rd Iteration (`i = 3`):**
   - `key = 5`
   - Compare `5` with `13`, `12`, and `11` → Shift all to the right.
   - Insert `5` at index `0`.
   - Array: `[5, 11, 12, 13, 6]`
5. **After 4th Iteration (`i = 4`):**
   - `key = 6`
   - Compare `6` with `13`, `12`, and `11` → Shift `13`, `12`, and `11` to the right.
   - Insert `6` at index `1`.
   - Array: `[5, 6, 11, 12, 13]`

---

### **Output**:
```
Original array:
12 11 13 5 6 
Sorted array:
5 6 11 12 13 
```

---

### **Advantages of Insertion Sort**:
1. Simple to implement.
2. Efficient for small datasets or nearly sorted arrays.
3. In-place sorting (requires no additional memory).
4. Stable sorting algorithm (maintains the relative order of equal elements).

---

### **Disadvantages of Insertion Sort**:
1. Inefficient for large datasets (`O(n^2)` time complexity).
2. Not suitable for real-time applications with large input sizes.

---

### **When to Use Insertion Sort**:
- When the dataset is small.
- When the array is nearly sorted.
- When memory usage is a concern (in-place sorting).

---

This implementation is straightforward and demonstrates the core logic of Insertion Sort in Java.



### **Selection Sort: Explanation**

Selection Sort is a simple comparison-based sorting algorithm. It works by repeatedly selecting the smallest (or largest, depending on the order) element from the unsorted portion of the array and swapping it with the first unsorted element. This process continues until the entire array is sorted.

#### **Steps of Selection Sort**:
1. **Divide the array** into a sorted and an unsorted portion.
   - Initially, the sorted portion is empty, and the unsorted portion is the entire array.
2. **Find the smallest element** in the unsorted portion.
3. **Swap** this smallest element with the first element of the unsorted portion.
4. **Move the boundary** between the sorted and unsorted portions one element to the right.
5. Repeat steps 2–4 until the entire array is sorted.

#### **Time Complexity**:
- **Best Case**: \(O(n^2)\) (even if the array is already sorted)
- **Average Case**: \(O(n^2)\)
- **Worst Case**: \(O(n^2)\)

#### **Space Complexity**:
- \(O(1)\) (in-place sorting algorithm)

#### **Advantages**:
- Simple to implement.
- Performs well on small datasets.

#### **Disadvantages**:
- Inefficient for large datasets.
- Not stable (does not preserve the relative order of equal elements).

---

### **Selection Sort: Java Implementation**

```java
public class SelectionSort {

    public static void selectionSort(int[] arr) {
        int n = arr.length;

        // Traverse through the array
        for (int i = 0; i < n - 1; i++) {
            // Find the minimum element in the unsorted portion
            int minIndex = i;
            for (int j = i + 1; j < n; j++) {
                if (arr[j] < arr[minIndex]) {
                    minIndex = j; // Update the index of the minimum element
                }
            }

            // Swap the found minimum element with the first unsorted element
            int temp = arr[minIndex];
            arr[minIndex] = arr[i];
            arr[i] = temp;
        }
    }

    public static void main(String[] args) {
        int[] arr = {64, 25, 12, 22, 11};

        System.out.println("Original array:");
        printArray(arr);

        selectionSort(arr);

        System.out.println("Sorted array:");
        printArray(arr);
    }

    // Helper method to print the array
    public static void printArray(int[] arr) {
        for (int i : arr) {
            System.out.print(i + " ");
        }
        System.out.println();
    }
}
```

---

### **How It Works**:
1. **Initial Array**: `[64, 25, 12, 22, 11]`
2. **First Iteration**:
   - Find the smallest element (`11`) and swap it with the first element (`64`).
   - Array becomes: `[11, 25, 12, 22, 64]`
3. **Second Iteration**:
   - Find the smallest element in the unsorted portion (`12`) and swap it with the second element (`25`).
   - Array becomes: `[11, 12, 25, 22, 64]`
4. **Third Iteration**:
   - Find the smallest element in the unsorted portion (`22`) and swap it with the third element (`25`).
   - Array becomes: `[11, 12, 22, 25, 64]`
5. **Fourth Iteration**:
   - Find the smallest element in the unsorted portion (`25`) and swap it with the fourth element (`25`).
   - Array remains: `[11, 12, 22, 25, 64]`
6. **Final Sorted Array**: `[11, 12, 22, 25, 64]`

---

### **Output**:
```
Original array:
64 25 12 22 11 
Sorted array:
11 12 22 25 64 
```

This implementation demonstrates the simplicity and effectiveness of Selection Sort for small datasets. However, for larger datasets, more efficient algorithms like Merge Sort or Quick Sort are preferred.