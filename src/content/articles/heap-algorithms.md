---
title: "Heap & Algorithms"
description: "Exploring essential algorithms that use heaps, including heapsort, priority queue operations, and graph traversal optimizations."
pubDate: 2024-01-20
category: "Algorithms"
tags: ["heap", "algorithms", "heapsort", "priority queue"]
---

# Heap & Algorithms

Heaps are fundamental data structures that power many important algorithms. Let's explore how heaps enable efficient solutions to common computational problems.

## Heapsort

Heapsort is a comparison-based sorting algorithm that uses a binary heap. It has O(n log n) time complexity and sorts in-place.

```swift
func heapsort<T: Comparable>(_ array: inout [T]) {
    // Build max heap
    let n = array.count
    for i in stride(from: n / 2 - 1, through: 0, by: -1) {
        heapify(&array, n, i)
    }
    
    // Extract elements one by one
    for i in stride(from: n - 1, through: 1, by: -1) {
        array.swapAt(0, i)
        heapify(&array, i, 0)
    }
}

func heapify<T: Comparable>(_ array: inout [T], _ n: Int, _ i: Int) {
    var largest = i
    let left = 2 * i + 1
    let right = 2 * i + 2
    
    if left < n && array[left] > array[largest] {
        largest = left
    }
    if right < n && array[right] > array[largest] {
        largest = right
    }
    
    if largest != i {
        array.swapAt(i, largest)
        heapify(&array, n, largest)
    }
}
```

## Priority Queue Applications

### Dijkstra's Shortest Path

Using a min-heap as a priority queue enables efficient extraction of the minimum distance vertex.

```swift
func dijkstra(graph: [[Edge]], source: Int) -> [Int] {
    var distances = Array(repeating: Int.max, count: graph.count)
    distances[source] = 0
    
    var heap = MinHeap<(vertex: Int, distance: Int)>()
    heap.insert((source, 0))
    
    while let current = heap.extractMin() {
        if current.distance > distances[current.vertex] {
            continue
        }
        
        for edge in graph[current.vertex] {
            let newDist = distances[current.vertex] + edge.weight
            if newDist < distances[edge.to] {
                distances[edge.to] = newDist
                heap.insert((edge.to, newDist))
            }
        }
    }
    
    return distances
}
```

## Finding K Largest Elements

A min-heap of size K efficiently finds the K largest elements in a stream.

```swift
func findKLargest(_ nums: [Int], _ k: Int) -> [Int] {
    var minHeap = MinHeap<Int>()
    
    for num in nums {
        minHeap.insert(num)
        if minHeap.count > k {
            minHeap.extractMin()
        }
    }
    
    return minHeap.elements
}
```

## Complexity Summary

| Algorithm | Time | Space |
|-----------|------|-------|
| Heapsort | O(n log n) | O(1) |
| Dijkstra | O((V + E) log V) | O(V) |
| K Largest | O(n log k) | O(k) |
