---
title: "Heap & Algorithms"
description: "This is a Sequel of a Binary Heap article it aims to show you some usages of a heap in various algorithms and coding challenges."
pubDate: 2021-09-28
category: "Algorithms"
tags: ["heap", "algorithms", "heapsort", "priority queue"]
---

This is a Sequel of a [Binary Heap](https://www.mhermovsisyan.com/post/heap-binary-heap) article it aims to show you some usages of a heap in various algorithms and coding challenges.

We will have a look at the following algorithms and coding challenges.

1. Heapsort
2. Kth Largest Element in an Array
3. Merge K Sorted Arrays

### Heapsort

Heapsort is one of the well-known sorting algorithms.
The implementation is quite simple, you put all elements into the min-heap and then pop them until the heap is empty.

Here is the implementation.

```swift
func heapSort(_ input: [Int]) -> [Int] {
    var heap = Heap<Int>()
    for num in input {
        heap.push(num)
    }
    var res = [Int]()
    while !heap.isEmpty {
        if let item = heap.pop() {
            res.append(item)
        }
    }
    return res
}
```

The space complexity is n as all the elements must be placed into the heap before popping them out.
The time complexity is O(n * log(n)) because we have n elements and each pop method takes logarithmic time.
So the speed is the same as in merge sort and quicksort.

In Swift, if you want you can add the extension to the Array type and then use the heapsort on every array which elements conform to the Comparable protocol.

```swift
extension Array where Element: Comparable {
    func heapSort() -> [Element] {
        // Implementation
    }
}

// usage
let sortedNums = [-4, 9, -3, 89, 0, 0, 2].heapSort()
let sortedWords = ["dune", "is", "an", "awesome", "movie"].heapSort()
```

### Kth Largest Element in an Array

We need to find the k-th largest element in an unsorted array.

Consider the following inputs: `array = [4, -9, 89, 0, -6, 89]`, `k = 2`
In this example, we need to find the second biggest element in the array. In this case, it is 89 and is equal to the biggest one. As you can see the uniqueness of elements doesn't matter. This is also a valid input: `array = [1, 1, 1, 1]`, `k = 2`.

The simplest way to solve this is to sort the array in descending order and return the item at index `[k - 1]`.

The time complexity, in that case, is based on the speed of sorting algorithms. For general sorting algorithms like heapsort, merge sort, and quicksort the time complexity is O(n log(n)) on average. As quicksort and merge sort can do sorting in place the space complexity will be O(1).

Now let’s consider solving this task with a heap. We will use a min-heap.
We will insert the elements one by one and when the size of the heap becomes bigger than k we will start popping items on each iteration. That way we will filter out the small items. In the end, we will have a min-heap with the top k elements and the required one will be in the root.

```swift
func kthLargestElement(_ input: [Int], _ k: Int) -> Int? {
    if k > input.count {
        return nil
    }
    var minHeap = Heap<Int>()
    for num in input {
        minHeap.push(num)
        if minHeap.count > k {
            minHeap.pop()
        }
    }
    return minHeap.peek
}
```

To grasp a better understanding of how the heap is changing during the loop please check the bottom diagrams.

![Heap algorithm diagram 1](/images/articles/heap-algorithms-1.png)
![Heap algorithm diagram 2](/images/articles/heap-algorithms-2.png)
As you can see in the final version the heap contains only the biggest k elements and the smallest of them is our target. The property of min-heap allows us to filter out all small elements and only keep the top k ones.

The time complexity of this algorithm is O(n * log(k)) because we are iterating through n items and the heap keeps at most k items.
The space complexity is O(k) as min-heap is keeping k items.

### Merge K Sorted Arrays

You are given an array of sorted arrays and you need to merge them.

Consider the following input: `array = [[1, 4, 5], [1, 3, 4], [2, 6]]`. For this example the output should be `[1, 1, 2, 3, 4, 4, 5, 6]`.

The simplest way to do it is to add all items to the array and then run a sort algorithm.
The time complexity is O(n * log(n)) and the space complexity is O(n).

Now let’s try to solve this with min-heap.
Please note that the algorithm with min-heap will allow you to solve the same question for harder scenarios. For example, what if the interviewer told you that instead of sorted arrays you have multiple sorted streams of infinite items and the references to them and you need to print out items in the order. In this case, you can’t add them to the array because you have a reference to the stream and not all the items and the second problem is that the RAM of your PC is finite and the stream is infinite.

I hope you want to know the solution to the described case.

I will create a `Helper` data structure that will store the value of the first item of the array the index of that array and the index that will track elements(to use it this way `input[i][j]`).

```swift
struct Helper: Comparable {
    var val: Int // value of the item in array at the index j
    var i: Int   // index of the array in the input
    var j: Int   // index of the element in array

    // min-heap will use the value to decide which item is the lowest one
    static func < (lhs: Helper, rhs: Helper) -> Bool {
        return lhs.val < rhs.val
    }
}
```

Here is the algorithm. I have added some comments to make the code more readable.

```swift
func mergeKSortedArrays(_ input: [[Int]]) -> [Int] {
    var heap = Heap<Helper>()
    for (index, arr) in input.enumerated() {
        if !arr.isEmpty {
            // Add first item of each array in the heap and keep index of the array and index of the element in array
            heap.push(Helper(val: arr[0], i: index, j: 0))
        }
    }
    var res = [Int]()

    // In the heap, we are keeping the minimum not processed value from each array and reference to that array.
    // On each iteration, we are fetching the root item and adding the value of that item to the result.
    // Then, we are checking if the fetched array is fully processed.
    // If it's not it means that we must add that array info back to the stack with the next value and element index.

    while !heap.isEmpty {
        guard let helper = heap.pop() else { return [] }
        // Adding value to the result
        res.append(helper.val)
        
        // Check is j in the last index or is there some items in array that I haven't used
        if helper.j + 1 < input[helper.i].count {
            // Add info back to the heap to process the rest of items in this array
            heap.push(Helper(val: input[helper.i][helper.j], i: helper.i, j: helper.j + 1))
        }
    }
    return res
}
```

The time complexity of this algorithm O(n * log(k)) where n is the number of all elements and k is the number of arrays.
The space complexity for the algorithm (not considering the return value) is O(k).

With this approach, you can solve the problem with infinite streams by keeping the reference to the streams and comparing their first values.

I have only touched the surface there are a great number of algorithms and coding challenges where the heap is extremely useful.

You can find more coding challenges on the heap in the [Leetcode](https://leetcode.com/tag/heap-priority-queue/).
