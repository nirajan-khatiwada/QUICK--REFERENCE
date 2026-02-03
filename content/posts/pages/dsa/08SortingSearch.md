---
title: "Searching & Sorting in Python: Linear, Binary & Bubble Sort"
slug: "searching-sorting-algorithms-python"
date: 2024-10-24
description: "Fundamental searching and sorting algorithms in Python. Linear Search, Binary Search, and Bubble Sort explained with code."
showToc: true
weight: 5
series: ["DSA"]
categories: ["DSA", "Python"]
tags: ["Algorithms", "Searching", "Sorting", "Python", "Binary Search"]
summary: "A beginner's guide to essential searching and sorting algorithms in Python, complete with time complexity analysis."
images: ["/images/sorting.png"]
---

Searching and sorting are fundamental concepts in computer science. Searching is the process of finding a specific element in a data structure, while sorting is the process of arranging elements in a specific order (ascending or descending).

This note covers **linear search**, **binary search**, and **bubble sort** with code explanations and step-by-step details.

---

## 1. Linear Search

### Concept

* Linear search traverses the list from start to end.
* Compares each element with the target key.
* Returns `True` if the key is found, `False` otherwise.

### Code

```python
def linear_search(lists, key):
    # Step 1: Traverse each element in the list
    for x in lists:
        # Step 2: Check if current element matches the key
        if x == key:
            return True
    # Step 3: If key is not found after traversal
    return False
```

### Explanation

1. Start from the first element.
2. Compare each element with the key.
3. If a match is found, return `True`.
4. If end of list is reached without a match, return `False`.

**Time Complexity:** O(n)
**Space Complexity:** O(1)

---

## 2. Binary Search

### Concept

* Binary search works on **sorted lists**.
* Divides the list into halves repeatedly to find the key.
* More efficient than linear search for large lists.

### Code

```python
def binary_search(lists, first, last, key):
    # Step 1: Base case - if start index is greater than end
    if first > last:
        return False

    # Step 2: Find middle element
    m = (first + last) // 2

    # Step 3: Check if middle element is the key
    if lists[m] == key:
        return True
    else:
        # Step 4: If middle element is less than key, search right half
        if lists[m] < key:
            return binary_search(lists, m+1, last, key)
        # Step 5: If middle element is greater than key, search left half
        elif lists[m] > key:
            return binary_search(lists, first, m-1, key)
```

### Explanation

1. Calculate the middle index `m`.
2. Compare `lists[m]` with `key`.
3. If equal, return `True`.
4. If `lists[m] < key`, search the right half.
5. If `lists[m] > key`, search the left half.
6. Repeat until the key is found or the sublist is empty.

**Time Complexity:** O(log n)
**Space Complexity:** O(log n) due to recursion

---

## 3. Bubble Sort

### Concept

* Bubble sort repeatedly swaps adjacent elements if they are in the wrong order.
* After each pass, the largest unsorted element moves to its correct position.
* Simple but not efficient for large lists.

### Code

```python
def bubble_sort(lists):
    # Step 1: Traverse the list multiple times
    for x in range(len(lists) - 1):
        # Step 2: Traverse the unsorted part of the list
        for y in range(0, len(lists) - x - 1):
            # Step 3: Swap if the current element is greater than next
            if lists[y] > lists[y+1]:
                temp = lists[y]
                lists[y] = lists[y+1]
                lists[y+1] = temp
```

### Explanation

1. Outer loop controls the number of passes.
2. Inner loop compares adjacent elements.
3. Swap elements if they are in the wrong order.
4. Repeat until the list is sorted.

**Time Complexity:** O(n^2)
**Space Complexity:** O(1)

---

## Selection Sort
### Concept
* In each iteration, find the minimum element from the unsorted part and swap it with the first unsorted element.

### Code
```python
def selection_sort(lists):
    # Step 1: Traverse through all list elements
    for i in range(len(lists)):
        # Step 2: Assume the minimum is the first element of unsorted part
        min_index = i
        # Step 3: Find the minimum element in remaining unsorted array
        for j in range(i+1, len(lists)):
            if lists[j] < lists[min_index]:
                min_index = j
        # Step 4: Swap the found minimum element with the first element
        temp = lists[i]
        lists[i] = lists[min_index]
        lists[min_index] = temp
```

## Insertion Sort
### Concept
* Initially take the first element as sorted and other elements as unsorted.
* Pick elements from unsorted part and insert them into the correct position in the sorted part.

### Code
```python
def insertion_sort(lists):
    # Step 1: Traverse from 1 to len(lists)
    for i in range(1, len(lists)):
        key = lists[i]
        j = i - 1
        # Step 2: Move elements of lists[0..i-1], that are greater than key,
        # to one position ahead of their current position
        while j >= 0 and key < lists[j]:
            lists[j + 1] = lists[j]
            j -= 1
        lists[j + 1] = key
```

## Merge Sort
### Concept
* Divide the list into halves recursively until each sublist contains a single element.
* Merge the sublists back together in sorted order.
* Efficient for large datasets.

```python
#using divide and conquer approach
def merge_sort(lists,low,high):
    if low < high:
        mid = (low + high) // 2
        merge_sort(lists, low, mid)
        merge_sort(lists, mid + 1, high)
        merge(lists, low, mid, high)
def merge(lists, low, mid, high):
    #using c based approach
    temp= [-1]*(high - low + 1)
    i = low
    j = mid + 1
    k = 0
    while i <= mid and j <= high:
        if lists[i] <= lists[j]:
            temp[k] = lists[i]
            i += 1
        else:
            temp[k] = lists[j]
            j += 1
        k += 1
    while i <= mid:
        temp[k] = lists[i]
        i += 1
        k += 1
    while j <= high:
        temp[k] = lists[j]
        j += 1
        k += 1
    for i in range(len(temp)):
        lists[low + i] = temp[i]

lists = [38, 27, 43, 3, 9, 82, 10]
merge_sort(lists, 0, len(lists) - 1)
```

## Quick Sort
### Concept
* Select a 'pivot' element from the list.
* Partition the other elements into two sub-arrays according to whether they are less than or greater than the pivot ie put pivot in its correct position.

```python
def quick_sort(lists, low, high):
    if low < high:
        pi = partition(lists, low, high)
        quick_sort(lists, low, pi - 1)
        quick_sort(lists, pi + 1, high)
def partition(lists, low, high):
    pivot = lists[high]
    i = low 
    j= high
    while i<j:
        while  lists[i] <= pivot:
            i += 1
        while lists[j] >= pivot:
            j -= 1
        if i < j:
            lists[i], lists[j] = lists[j], lists[i]
    lists[i]=lists[j]
    lists[j]= pivot
    return j
lists = [10, 7, 8, 9, 1, 5]
quick_sort(lists, 0, len(lists) - 1)
```




## Summary

| Algorithm     | Best Case | Worst Case | Average Case | Space Complexity |
| ------------- | --------- | ---------- | ------------ | ---------------- |
| Linear Search | O(1)      | O(n)       | O(n)         | O(1)             |
| Binary Search | O(1)      | O(log n)   | O(log n)     | O(log n)         |
| Bubble Sort   | O(n)      | O(n^2)     | O(n^2)       | O(1)             |
| Selection Sort| O(n^2)    | O(n^2)     | O(n^2)       | O(1)             |
| Insertion Sort| O(n)      | O(n^2)     | O(n^2)       | O(1)             |
| Merge Sort    | O(n log n)| O(n log n) | O(n log n)   | O(n)             |
| Quick Sort    | O(n log n)| O(n^2)     | O(n log n)   | O(log n)         |
---
