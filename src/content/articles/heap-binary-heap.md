---
title: "Heap (Binary Heap)"
description: "Understanding binary heaps - a complete binary tree data structure that satisfies the heap property, essential for priority queues and heapsort."
pubDate: 2024-01-15
category: "Data Structures"
tags: ["heap", "binary heap", "data structures", "trees"]
---

# Heap (Binary Heap)

A **Binary Heap** is a complete binary tree that satisfies the **heap property**. It's one of the most efficient data structures for implementing priority queues.

## What is a Heap?

A heap is a specialized tree-based data structure that satisfies the heap property:

- **Max-Heap**: Every parent node is greater than or equal to its children
- **Min-Heap**: Every parent node is less than or equal to its children

## Properties

1. **Complete Binary Tree**: All levels are filled except possibly the last, which is filled from left to right
2. **Heap Property**: Parent-child relationship is maintained throughout the tree
3. **Array Representation**: Can be efficiently stored in an array

## Array Representation

For a node at index `i`:
- **Parent**: `(i - 1) / 2`
- **Left Child**: `2 * i + 1`
- **Right Child**: `2 * i + 2`

```swift
struct MaxHeap<T: Comparable> {
    private var elements: [T] = []
    
    var isEmpty: Bool { elements.isEmpty }
    var count: Int { elements.count }
    var peek: T? { elements.first }
    
    mutating func insert(_ value: T) {
        elements.append(value)
        siftUp(from: elements.count - 1)
    }
    
    mutating func extractMax() -> T? {
        guard !isEmpty else { return nil }
        elements.swapAt(0, elements.count - 1)
        let max = elements.removeLast()
        if !isEmpty { siftDown(from: 0) }
        return max
    }
    
    private mutating func siftUp(from index: Int) {
        var child = index
        var parent = (child - 1) / 2
        
        while child > 0 && elements[child] > elements[parent] {
            elements.swapAt(child, parent)
            child = parent
            parent = (child - 1) / 2
        }
    }
    
    private mutating func siftDown(from index: Int) {
        var parent = index
        
        while true {
            let left = 2 * parent + 1
            let right = 2 * parent + 2
            var candidate = parent
            
            if left < count && elements[left] > elements[candidate] {
                candidate = left
            }
            if right < count && elements[right] > elements[candidate] {
                candidate = right
            }
            if candidate == parent { return }
            
            elements.swapAt(parent, candidate)
            parent = candidate
        }
    }
}
```

## Time Complexity

| Operation | Time Complexity |
|-----------|----------------|
| Insert    | O(log n)       |
| Extract   | O(log n)       |
| Peek      | O(1)           |
| Build     | O(n)           |

## Use Cases

- **Priority Queues**: Efficiently manage elements by priority
- **Heapsort**: In-place sorting algorithm with O(n log n) complexity
- **Graph Algorithms**: Dijkstra's and Prim's algorithms use heaps
- **Median Finding**: Two heaps can track running median efficiently
