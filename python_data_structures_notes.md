# Python Data Structures Notes

A GitHub-ready study guide covering **RAM, arrays, stacks, linked lists, and queues**.
Each section includes a definition, core operations, Python examples, and time complexity.

---

## Table of Contents

1. [RAM and Memory Basics](#1-ram-and-memory-basics)
2. [Static Arrays](#2-static-arrays)
3. [Dynamic Arrays](#3-dynamic-arrays)
4. [Stacks](#4-stacks)
5. [Linked Lists](#5-linked-lists)
   - [Singly Linked Lists](#51-singly-linked-list)
   - [Doubly Linked Lists](#52-doubly-linked-list)
6. [Queues](#6-queues)
7. [Complexity Cheat Sheet](#7-complexity-cheat-sheet)

---

## 1. RAM and Memory Basics

**RAM (Random Access Memory)** is the computer's short-term working memory. A program stores the data it is currently using in memory.

### Key ideas

- **Memory address:** A location in memory where data is stored.
- **Byte:** A basic unit of memory; 1 byte = 8 bits.
- **Random access:** RAM allows the computer to access a known memory address directly.
- **Reference / pointer:** A value that identifies where an object is stored. In Python, variables generally refer to objects rather than containing the objects directly.

### Why this matters for data structures

- Arrays use indexed access, which is efficient because an element's location can be calculated from the array's starting address.
- Linked-list nodes can live at different memory locations. Each node stores a reference to the next node (and, in a doubly linked list, the previous node).
- Data structure design affects access time, insertion/deletion cost, and memory overhead.

---

## 2. Static Arrays

A **static array** has a fixed capacity: its allocated size cannot grow or shrink after creation.

In a low-level language such as C, array elements are typically stored in a contiguous block of memory. Python's built-in `list` is a dynamic array, not a fixed-capacity static array.

### Main properties

- Elements are stored in order.
- Elements can be accessed by index in **O(1)** time.
- Inserting or deleting in the middle generally requires shifting elements: **O(n)**.
- Capacity is fixed in a traditional static array.

### Python illustration

Python does not provide a standard fixed-capacity array with all the same semantics as a C array. This example simulates one:

```python
class StaticArray:
    def __init__(self, capacity):
        if capacity < 0:
            raise ValueError("Capacity cannot be negative")
        self._data = [None] * capacity
        self._size = 0

    def __len__(self):
        return self._size

    def get(self, index):
        self._check_index(index)
        return self._data[index]

    def set(self, index, value):
        self._check_index(index)
        self._data[index] = value

    def append(self, value):
        if self._size == len(self._data):
            raise OverflowError("Static array is full")
        self._data[self._size] = value
        self._size += 1

    def _check_index(self, index):
        if not 0 <= index < self._size:
            raise IndexError("Index out of range")


arr = StaticArray(3)
arr.append(10)
arr.append(20)
print(arr.get(1))  # 20
```

### Complexity

| Operation | Time |
|---|---:|
| Access by index | O(1) |
| Update by index | O(1) |
| Search (unsorted) | O(n) |
| Insert while preserving order | O(n) |
| Delete while preserving order | O(n) |

---

## 3. Dynamic Arrays

A **dynamic array** is an array-like structure that can automatically resize when it runs out of capacity.

Python's built-in `list` is a dynamic array.

### How resizing works

1. The dynamic array has an underlying storage capacity.
2. When it becomes full, it allocates a larger block of memory.
3. Existing elements are copied/referenced into the new storage.
4. The new element is added.

Resizing is expensive when it happens, but it does not happen on every append. Therefore, appending to a dynamic array is **amortized O(1)**.

### Python examples

```python
numbers = [10, 20, 30]

# Access
print(numbers[0])       # 10

# Update
numbers[1] = 25

# Append to the end
numbers.append(40)

# Insert at an index
numbers.insert(1, 15)

# Delete by value
numbers.remove(30)

# Delete by index
removed = numbers.pop(0)

# Search
print(25 in numbers)
```

### Complexity

| Operation | Time |
|---|---:|
| Access by index | O(1) |
| Update by index | O(1) |
| Append | Amortized O(1), worst-case O(n) |
| Pop from end | O(1) |
| Insert/delete at beginning or middle | O(n) |
| Search by value | O(n) |

**Static vs. dynamic array:** A static array has a fixed capacity. A dynamic array can resize, usually by allocating a larger block and copying its elements.

---

## 4. Stacks

A **stack** is a linear data structure that follows **LIFO (Last In, First Out)**. The last item added is the first item removed.

Think of a stack of plates: you add and remove plates from the top.

### Core operations

- `push`: Add an item to the top.
- `pop`: Remove and return the top item.
- `peek` / `top`: Read the top item without removing it.
- `is_empty`: Check whether the stack is empty.

### Python implementation

Use a Python list. Appending and popping at the end are efficient.

```python
class Stack:
    def __init__(self):
        self._items = []

    def push(self, value):
        self._items.append(value)

    def pop(self):
        if self.is_empty():
            raise IndexError("Pop from empty stack")
        return self._items.pop()

    def peek(self):
        if self.is_empty():
            raise IndexError("Peek at empty stack")
        return self._items[-1]

    def is_empty(self):
        return len(self._items) == 0

    def __len__(self):
        return len(self._items)


stack = Stack()
stack.push(10)
stack.push(20)
stack.push(30)

print(stack.peek())  # 30
print(stack.pop())   # 30
print(len(stack))    # 2
```

### Complexity

| Operation | Time |
|---|---:|
| Push | O(1) amortized |
| Pop | O(1) |
| Peek | O(1) |
| Check empty | O(1) |

### Common applications

- Undo / redo functionality
- Browser backtracking
- Function calls and recursion (call stack)
- Matching parentheses
- Depth-first search (DFS)

---

## 5. Linked Lists

A **linked list** is a linear data structure made of nodes. A node stores a value and one or more references to other nodes.

Unlike a typical array, linked-list nodes do not need to occupy adjacent memory locations.

### Key terms

- **Node:** One element in the linked list.
- **Head:** Reference to the first node.
- **Tail:** Reference to the last node (if maintained).
- **Next:** Reference to the next node.
- **Prev:** Reference to the previous node (in a doubly linked list).
- **None:** Marks the end of a non-circular list.

### Array vs. linked list

| Feature | Dynamic Array | Linked List |
|---|---|---|
| Access by index | O(1) | O(n) |
| Search | O(n) | O(n) |
| Insert at front | O(n) | O(1) with head |
| Delete at front | O(n) | O(1) with head |
| Insert after a known node | O(n) due to shifting | O(1) |
| Extra pointer memory | No per-element links | Yes |

A linked list does **not** make every insertion or deletion O(1): finding the position can still take O(n). The O(1) claim assumes the relevant node (and, when needed, its predecessor) is already known.

### 5.1 Singly Linked List

A **singly linked list** node stores a value and a reference to the next node.

```text
head
 |
 v
[10] -> [20] -> [30] -> None
```

#### Node definition

```python
class ListNode:
    def __init__(self, value):
        self.value = value
        self.next = None
```

#### Complete implementation

```python
class SinglyLinkedList:
    def __init__(self):
        self.head = None

    def prepend(self, value):
        """Insert a new node at the beginning: O(1)."""
        node = ListNode(value)
        node.next = self.head
        self.head = node

    def append(self, value):
        """Insert at the end: O(n) without a tail reference."""
        node = ListNode(value)

        if self.head is None:
            self.head = node
            return

        current = self.head
        while current.next is not None:
            current = current.next
        current.next = node

    def find(self, value):
        """Return the first matching node, or None: O(n)."""
        current = self.head
        while current is not None:
            if current.value == value:
                return current
            current = current.next
        return None

    def delete(self, value):
        """Delete the first node with this value: O(n)."""
        if self.head is None:
            return False

        if self.head.value == value:
            self.head = self.head.next
            return True

        previous = self.head
        current = self.head.next

        while current is not None:
            if current.value == value:
                previous.next = current.next
                return True
            previous = current
            current = current.next

        return False

    def to_list(self):
        """Return values in traversal order: O(n)."""
        result = []
        current = self.head
        while current is not None:
            result.append(current.value)
            current = current.next
        return result


linked = SinglyLinkedList()
linked.append(10)
linked.append(20)
linked.prepend(5)
print(linked.to_list())  # [5, 10, 20]
linked.delete(10)
print(linked.to_list())  # [5, 20]
```

#### Traversal pattern

```python
current = head
while current is not None:
    print(current.value)
    current = current.next
```

#### Reverse a singly linked list

This is a classic interview problem (LeetCode 206).

```python
def reverse_list(head):
    previous = None
    current = head

    while current is not None:
        next_node = current.next  # Save the next node
        current.next = previous   # Reverse the pointer
        previous = current       # Move previous forward
        current = next_node       # Move current forward

    return previous  # New head
```

**Important pointer order:** Save `current.next` before changing it. Otherwise, the rest of the list can become unreachable.

### 5.2 Doubly Linked List

A **doubly linked list** node stores a value and two references:

- `prev`: Previous node.
- `next`: Next node.

```text
None <- [10] <-> [20] <-> [30] -> None
        head                tail
```

It supports both forward and backward traversal, but requires extra memory for the additional pointer.

#### Node definition

```python
class DoublyListNode:
    def __init__(self, value):
        self.value = value
        self.prev = None
        self.next = None
```

#### Complete implementation

```python
class DoublyLinkedList:
    def __init__(self):
        self.head = None
        self.tail = None

    def prepend(self, value):
        """Insert at the head: O(1)."""
        node = DoublyListNode(value)

        if self.head is None:
            self.head = self.tail = node
            return

        node.next = self.head
        self.head.prev = node
        self.head = node

    def append(self, value):
        """Insert at the tail: O(1), because tail is maintained."""
        node = DoublyListNode(value)

        if self.tail is None:
            self.head = self.tail = node
            return

        node.prev = self.tail
        self.tail.next = node
        self.tail = node

    def find(self, value):
        """Return the first matching node, or None: O(n)."""
        current = self.head
        while current is not None:
            if current.value == value:
                return current
            current = current.next
        return None

    def insert_after(self, current, value):
        """Insert after a known node: O(1)."""
        if current is None:
            raise ValueError("current node cannot be None")

        node = DoublyListNode(value)
        node.prev = current
        node.next = current.next

        if current.next is not None:
            current.next.prev = node
        else:
            self.tail = node

        current.next = node

    def delete_node(self, node):
        """Delete a known node: O(1)."""
        if node is None:
            return

        if node.prev is not None:
            node.prev.next = node.next
        else:
            self.head = node.next

        if node.next is not None:
            node.next.prev = node.prev
        else:
            self.tail = node.prev

        # Disconnect the removed node
        node.prev = None
        node.next = None

    def forward(self):
        """Return values from head to tail: O(n)."""
        result = []
        current = self.head
        while current is not None:
            result.append(current.value)
            current = current.next
        return result

    def backward(self):
        """Return values from tail to head: O(n)."""
        result = []
        current = self.tail
        while current is not None:
            result.append(current.value)
            current = current.prev
        return result


dll = DoublyLinkedList()
dll.append(10)
dll.append(20)
dll.prepend(5)

print(dll.forward())   # [5, 10, 20]
print(dll.backward())  # [20, 10, 5]

node = dll.find(10)
dll.insert_after(node, 15)
print(dll.forward())   # [5, 10, 15, 20]

dll.delete_node(node)
print(dll.forward())   # [5, 15, 20]
```

#### Insertion after a known node

To insert `25` after node `20`:

```python
new_node.prev = current
new_node.next = current.next

if current.next is not None:
    current.next.prev = new_node

current.next = new_node
```

#### Deletion of a known node

To remove a node, connect its neighbors to each other:

```python
if node.prev is not None:
    node.prev.next = node.next

if node.next is not None:
    node.next.prev = node.prev
```

A complete implementation must also update `head` or `tail` when deleting an endpoint.

### Singly vs. Doubly Linked List

| Feature | Singly | Doubly |
|---|---|---|
| Pointers per node | `next` | `prev`, `next` |
| Forward traversal | Yes | Yes |
| Backward traversal | No | Yes |
| Extra memory | Lower | Higher |
| Delete a known node | Usually needs predecessor | O(1) |
| Insert before a known node | Needs predecessor | O(1) |

---

## 6. Queues

A **queue** is a linear data structure that follows **FIFO (First In, First Out)**. The first item added is the first item removed.

Think of a line of people waiting: the person who arrives first is served first.

### Core operations

- `enqueue`: Add an item to the rear.
- `dequeue`: Remove and return the front item.
- `peek` / `front`: Read the front item without removing it.
- `is_empty`: Check whether the queue is empty.

### Recommended Python implementation: `collections.deque`

Avoid using `list.pop(0)` for a queue because removing the first list element requires shifting the remaining elements (O(n)).

```python
from collections import deque

queue = deque()

# Enqueue
queue.append(10)
queue.append(20)
queue.append(30)

# Peek at the front
print(queue[0])       # 10

# Dequeue
print(queue.popleft())  # 10

# Check whether empty
print(len(queue) == 0)  # False
```

### Queue class

```python
from collections import deque


class Queue:
    def __init__(self):
        self._items = deque()

    def enqueue(self, value):
        self._items.append(value)

    def dequeue(self):
        if self.is_empty():
            raise IndexError("Dequeue from empty queue")
        return self._items.popleft()

    def peek(self):
        if self.is_empty():
            raise IndexError("Peek at empty queue")
        return self._items[0]

    def is_empty(self):
        return len(self._items) == 0

    def __len__(self):
        return len(self._items)


q = Queue()
q.enqueue("A")
q.enqueue("B")
q.enqueue("C")

print(q.peek())     # A
print(q.dequeue())  # A
print(len(q))       # 2
```

### Complexity

| Operation | Time |
|---|---:|
| Enqueue | O(1) |
| Dequeue | O(1) |
| Peek at front | O(1) |
| Check empty | O(1) |

### Common applications

- Task scheduling
- Breadth-first search (BFS)
- Print queues
- Request processing
- Producer-consumer systems

---

## 7. Complexity Cheat Sheet

| Data Structure | Access | Search | Insert | Delete |
|---|---:|---:|---:|---:|
| Static Array | O(1) | O(n) | O(n)* | O(n)* |
| Dynamic Array | O(1) | O(n) | O(1) amortized at end; O(n) elsewhere | O(1) at end; O(n) elsewhere |
| Stack | O(n)** | O(n)** | O(1) push | O(1) pop |
| Singly Linked List | O(n) | O(n) | O(1) at head; O(n) at tail without tail pointer | O(1) at head; O(n) to find value |
| Doubly Linked List | O(n) | O(n) | O(1) at head/tail or next to known node | O(1) for known node |
| Queue | O(n)** | O(n)** | O(1) enqueue | O(1) dequeue |

\* For array insertion/deletion while preserving element order; elements may need to shift.  
\** A stack or queue exposes only its ends efficiently; arbitrary access/search is not its intended use.

---

## Key Takeaways

1. **Arrays:** Fast indexed access; insertion/deletion in the middle can require shifting.
2. **Dynamic arrays:** Resize automatically; appending is amortized O(1).
3. **Stacks:** LIFO — Last In, First Out.
4. **Queues:** FIFO — First In, First Out.
5. **Singly linked lists:** Each node points forward to the next node.
6. **Doubly linked lists:** Each node points both backward and forward.
7. **Pointer updates matter:** Save references before changing links, and handle empty-list and endpoint cases.

## Suggested LeetCode Practice

| Topic | Problem | Number | Difficulty |
|---|---|---:|---|
| Array | Two Sum | 1 | Easy |
| Array | Remove Duplicates from Sorted Array | 26 | Easy |
| Stack | Valid Parentheses | 20 | Easy |
| Stack | Min Stack | 155 | Medium |
| Singly Linked List | Reverse Linked List | 206 | Easy |
| Singly Linked List | Linked List Cycle | 141 | Easy |
| Singly Linked List | Merge Two Sorted Lists | 21 | Easy |
| Doubly Linked List | Design Linked List | 707 | Medium |
| Doubly Linked List | LRU Cache | 146 | Medium |
| Queue | Implement Queue using Stacks | 232 | Easy |
| Queue | Number of Recent Calls | 933 | Easy |

---

*Study approach: understand the definition → draw the structure → trace pointer/index changes → implement operations → analyze time complexity → solve practice problems.*
