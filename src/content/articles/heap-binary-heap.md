---
title: "Heap (Binary Heap)"
description: "To put it simply Heap is a data structure that allows retrieving an element with higher priority(min/max) in a logarithmic time."
pubDate: 2021-09-28
category: "Data Structures"
tags: ["heap", "binary heap", "data structures", "trees"]
---

To put it simply Heap is a data structure that allows retrieving an element with higher priority(min/max) in a logarithmic time. Frankly speaking, retrieving is a constant time operation but it takes log(n) time to make the structure heap again. We will go deep into the properties of the Heap later.

Unfortunately, Swift’s Standart Library doesn’t provide out of box implementation for a Heap data structure, in contrast, Java, C++, Python, and many other popular programming languages provide it as part of their libraries.

Let’s fix this injustice.

First of all, let’s define the contract for our Heap. The data structure should have at least the following methods:

- `peek()` return’s(does not remove) the top(min or max) element: Time complexity O(1)
- `pop()` removes and return’s the top(min or max) element: Time complexity O(log(n))
- `push(:element)` inserts a new element in the Heap: Time complexity O(log(n))

Let’s consider the following example you are asked to build a system that will execute the tasks based on their priority and that system receives new tasks constantly.
You might consider adding the tasks to the array and then looping through it to get the one that has the highest priority and execute it. That will work for small sets, but it’s inefficient, not elegant, and most importantly, not cool. Nobody brags about running for-loop bazillion times and definitely, you will not mention it in your CV.

As you might guess the Heap data structure is an excellent match for our system. If we use Heap we can just push all the tasks into the Heap and then pop them when the system is idle.

### Min-heap

I will describe a min-heap, but it is the same as max-heap the difference is only in the condition check.
Min-heap is a binary tree where the parent is less than or equal to its children’s elements and that is true for all the subtrees.
Here is an example of a min-heap.

![Min-heap example](/images/articles/heap-binary-heap-1.png)
As you can see the smallest element is in the root and all the parent elements are bigger than the children ones.
A small reminder on the tree naming.
In the trees, the elements are often called nodes the topmost element is the root node and the bottom ones are called leaves.
One more thing that is very important to remember is that Heap is an almost complete binary tree. It means that all the levels of a binary tree are fully filled except the last one. The last level can be filled partially, all the elements in the last level are filled from left to right.
These binary trees are not min-heaps as they are not complete or they violate the min property of a binary heap tree.

![Invalid heap 1](/images/articles/heap-binary-heap-2.png)
![Invalid heap 2](/images/articles/heap-binary-heap-3.png)
![Invalid heap 3](/images/articles/heap-binary-heap-4.png)

## Min-heap API (peek(), pop(), push(:item))

#### Peek

From the structure of the Heap, it is obvious that the `peek()` method is quite trivial to implement, we are just returning the value of the root node. But, what about `pop()` and `push(:element)` methods.

#### Push

Let’s consider adding 4 into the min-heap. In Heap (doesn't matter min or max) we always add the element from the tail, it is always added as the left-most element of the last level.

![Push operation](/images/articles/heap-binary-heap-push.png)

You are probably saying, hold on, 52 is bigger than 4, it means that it’s not a min-heap anymore. You are absolutely right. In the Heap data structure, the insertion consists of two steps, first, we add the element and then we restore the heap property of the tree. To restore the heap structure we use a very elegant algorithm called heapify. Actually, there are two heapify algorithms in the Heap one is heapifyUp the second is heapifyDown. HeapifyUp is used during insertion and heapifyDown for popping the element.

In the min-heap, the heapifyUp is checking if the parent node is smaller than the current node and if it is, it swaps the items and continues the process until it either becomes a root or finds a parent node that is smaller than the current.

For this particular case, it compares 4 with 52 and swaps them, after it compares 4 with 3 because 3 is smaller it stops the process.

Here is the progression:

![Progression 1](/images/articles/heap-binary-heap-progression-1.png)
![Progression 2](/images/articles/heap-binary-heap-progression-2.png)
![Progression 3](/images/articles/heap-binary-heap-progression-3.png)
Now it’s a valid min-heap again 🤩

#### Pop

This is the hardest one it consists of three steps.

1. Swap the root element with the last one in our case 3 with 52.
2. Remove and return the swapped last element which is 3
3. HeapifyDown the new root which is 52

The first two steps are fairly simple so I will describe the HeapifyDown.
In the min-heap, the heapifyDown method is comparing the node with its children, and if the smallest child is smaller than the node it swaps them. It continues the process until the node becomes a leaf or until there is no smaller child.

Now you have got a good grasp regarding the Heap API.

## Min-heap implementation

The most fascinating thing about binary heap is that we are storing the tree in an array. Heaps are maintaining implicit binary tree structure in an array, the indexes of the array are used as pointers from parent to children and from children to parent. Here are simple math equations that will help to find a parent and children of a current node.

- Parent: `(index - 1)/2`
- Left child: `(2 * index) + 1`
- Right child: `(2 * index) + 2`

Storing a tree in the array is very efficient as we don’t use extra space for pointers and we have constant time access to the last element which is essential for `pop()` and `push(:element)` methods.

In a classical binary tree, it's not a trivial task to find where to insert a new element. You can do it with level order traversal which is a linear time algorithm or you can try to keep a list of all leaf nodes and then after every operation, you will need to update it which also will be a linear time algorithm. There might be a more efficient solution but I’m not aware of it.
Fortunately, with array access to the last element is a constant time operation it is `arr[n - 1]`.

I will create a Heap as a struct because all data structures in Swift are value types. You can go with the class as well in some cases it makes more sense.

```swift
public struct Heap<Element: Comparable> {
    private var items = [Element]()
}
```

First of all, I will add helper methods that will handle the parent-child relationship in the array. These methods are quite simple so I will not go into the details.

```swift
private extension Heap {
    mutating func swapElementsAtIndexes(_ indexOne: Int, _ indexTwo: Int) {
        let temp = items[indexOne]
        items[indexOne] = items[indexTwo]
        items[indexTwo] = temp
    }
    
    func hasLeftChild(_ index: Int) -> Bool {
        leftChildIndex(index) < items.count
    }
    
    func hasRightChild(_ index: Int) -> Bool {
        rightChildIndex(index) < items.count
    }
    
    func hasParent(_ index: Int) -> Bool {
        parentIndex(index) >= Int.zero
    }

    func leftChildIndex(_ index: Int) -> Int {
        2 * index + 1
    }
    
    func rightChildIndex(_ index: Int) -> Int {
        2 * index + 2
    }
    
    func parentIndex(_ index: Int) -> Int {
        (index - 1)/2
    }
    
    func leftChild(_ index: Int) -> Element {
        items[leftChildIndex(index)]
    }
    
    func rightChild(_ index: Int) -> Element {
        items[rightChildIndex(index)]
    }
    
    func parent(_ index: Int) -> Element {
        items[parentIndex(index)]
    }
}
```

### Now we can start implementing the main 3 methods.

#### Peek

```swift
public var peek: Element? {
    items.first
}
```

#### Push(:element)

```swift
public mutating func push(_ item: Element) {
    items.append(item)
    heapifyUp()
}
```

As I have already described above in the first step it adds an element to the end of the array and on the second step, it starts `heapifyUp()`.

```swift
mutating func heapifyUp() {
    var index = items.count - 1
    while hasParent(index) && parent(index) > items[index] {
        swapElementsAtIndexes(parentIndex(index), index)
        index = parentIndex(index)
    }
}
```

HeapifyUp starts from the last inserted element and checks for a valid parent to swap with. The parent is valid if its value is bigger than the current node's value.

#### Pop()

```swift
public mutating func pop() -> Element? {
    if items.isEmpty {
        return nil
    }
    // if items contain only one element just remove and return the last one
    if items.count == 1 {
        return items.removeLast()
    }
    let item = items.first
    let last = items.removeLast()
    items[.zero] = last
    heapifyDown()
    return item
}
```

HeapifyDown is starting from the root index and checks is there a valid child to swap the items. The valid element is the smallest child that is smaller than the node.

#### Time and space complexity

As you can see the space complexity is quite easy to measure it is the length of the array which is equivalent to the number of elements, so the space complexity is linear O(n).
The time complexity of a peek method is constant as it always returns the root element.
The time complexity of pop and push methods is logarithmic that is due to the nature of an almost complete binary tree. For this tree, the height is equal to log(n), which means that both heapifyUp and heapifyDown will swap elements maximum log(n) times.

#### More on heaps

You can check the complete code in [Github](https://github.com/Movsisyan/heap-swift/blob/main/Sources/Heap/Heap.swift).
I have also added it as a Swift package so you can add it as a dependency and play with it. Here is the URL [https://github.com/Movsisyan/heap-swift](https://github.com/Movsisyan/heap-swift)

The MIT has a great [[video](https://www.youtube.com/watch?v=HqPJF2L5h9U)](https://www.youtube.com/watch?v=B7hVxCmfPtM) on the topic also the video from Abdur Bari is a very nice one.
If you want to read more you can check [The Algorithm Design Manual](https://www.amazon.com/Algorithm-Design-Manual-Steven-Skiena/dp/1849967202) and [Introduction to Algorithms, 3rd Edition](https://www.amazon.com/Introduction-Algorithms-3rd-MIT-Press/dp/0262033844)
