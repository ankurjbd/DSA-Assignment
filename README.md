# Data Structures & Algorithms Assignment

**Name:** ANKUR
**Roll No:** 2510201090
**Course:** BCA (Bachelor of Computer Applications)  
**I'D:** BC2025277
---

## Question 1: Stack Implementation Using Array

### Description
A Stack is a linear data structure that follows the **LIFO (Last In, First Out)** principle. This implementation uses a fixed-size array to manage stack elements without relying on built-in libraries.

### Code Implementation (`stack.c`)

```c
#include <stdio.h>
#include <stdlib.h>

#define MAX_SIZE 5

typedef struct {
    int items[MAX_SIZE];
    int top;
} Stack;

// Function to initialize the stack
void initStack(Stack *s) {
    s->top = -1;
}

// Check if the stack is full
int isFull(Stack *s) {
    return s->top == MAX_SIZE - 1;
}

// Check if the stack is empty
int isEmpty(Stack *s) {
    return s->top == -1;
}

// Push operation to insert an element onto the stack
void PUSH(Stack *s, int x) {
    if (isFull(s)) {
        printf("Error: Stack Overflow! Cannot push %d onto a full stack.\n", x);
        return;
    }
    s->items[++(s->top)] = x;
    printf("Pushed %d onto the stack.\n", x);
}

// Pop operation to remove the top element from the stack
int POP(Stack *s) {
    if (isEmpty(s)) {
        printf("Error: Stack Underflow! Cannot pop from an empty stack.\n");
        return -1;
    }
    int poppedValue = s->items[(s->top)--];
    printf("Popped %d from the stack.\n", poppedValue);
    return poppedValue;
}

// Peek operation to view the top element without removing it
int PEEK(Stack *s) {
    if (isEmpty(s)) {
        printf("Error: Stack is empty! No element to peek.\n");
        return -1;
    }
    return s->items[s->top];
}

// Display all elements in the stack from top to bottom
void DISPLAY(Stack *s) {
    if (isEmpty(s)) {
        printf("Stack is empty.\n");
        return;
    }
    printf("Stack elements (Top to Bottom): ");
    for (int i = s->top; i >= 0; i--) {
        printf("%d ", s->items[i]);
    }
    printf("\n");
}

int main() {
    Stack s;
    initStack(&s);

    PUSH(&s, 10);
    PUSH(&s, 20);
    PUSH(&s, 30);
    DISPLAY(&s);

    printf("Top element (PEEK): %d\n", PEEK(&s));

    POP(&s);
    DISPLAY(&s);

    // Demonstrating Stack Overflow
    PUSH(&s, 40);
    PUSH(&s, 50);
    PUSH(&s, 60); 
    PUSH(&s, 70); // Triggers Stack Overflow

    // Demonstrating Stack Underflow
    POP(&s);
    POP(&s);
    POP(&s);
    POP(&s);
    POP(&s); // Triggers Stack Underflow

    return 0;
}
```
### Analysis & Fixed Size Discussion
* **Time Complexity:** `PUSH`: $O(1)$ | `POP`: $O(1)$ | `PEEK`: $O(1)$ | `DISPLAY`: $O(N)$
* **Space Complexity:** $O(N)$ total space allocation.
* **Overflow Behavior:** When the fixed array capacity is reached (`top == MAX_SIZE - 1`), subsequent `PUSH` attempts trigger a **Stack Overflow**. The explicit `isFull()` check prevents memory write errors and retains existing data integrity.

---

## 2. Circular Queue Implementation (Array-Based)

### Features
* Operations: `ENQUEUE`, `DEQUEUE`, `FRONT`, `DISPLAY`
* Uses modulo arithmetic `(index + 1) % MAX_SIZE` to reuse dequeued slots.

### Code (`circular_queue.c`)
```c
#include <stdio.h>
#include <stdlib.h>

#define MAX_SIZE 5

typedef struct {
    int items[MAX_SIZE];
    int front;
    int rear;
} CircularQueue;

// Initialize the circular queue
void initQueue(CircularQueue *q) {
    q->front = -1;
    q->rear = -1;
}

// Check if the circular queue is full
int isFull(CircularQueue *q) {
    return (q->rear + 1) % MAX_SIZE == q->front;
}

// Check if the circular queue is empty
int isEmpty(CircularQueue *q) {
    return q->front == -1;
}

// Insert an element into the circular queue
void ENQUEUE(CircularQueue *q, int x) {
    if (isFull(q)) {
        printf("Error: Queue Overflow! Cannot enqueue %d.\n", x);
        return;
    }
    if (isEmpty(q)) {
        q->front = 0;
        q->rear = 0;
    } else {
        q->rear = (q->rear + 1) % MAX_SIZE;
    }
    q->items[q->rear] = x;
    printf("Enqueued %d into the queue.\n", x);
}

// Remove an element from the circular queue
int DEQUEUE(CircularQueue *q) {
    if (isEmpty(q)) {
        printf("Error: Queue Underflow! Cannot dequeue from an empty queue.\n");
        return -1;
    }
    int value = q->items[q->front];
    if (q->front == q->rear) {
        // Queue becomes empty after removing the last element
        q->front = -1;
        q->rear = -1;
    } else {
        q->front = (q->front + 1) % MAX_SIZE;
    }
    printf("Dequeued %d from the queue.\n", value);
    return value;
}

// Get the front element of the circular queue
int FRONT(CircularQueue *q) {
    if (isEmpty(q)) {
        printf("Error: Queue is empty!\n");
        return -1;
    }
    return q->items[q->front];
}

// Display all elements in the circular queue
void DISPLAY(CircularQueue *q) {
    if (isEmpty(q)) {
        printf("Queue is empty.\n");
        return;
    }
    printf("Queue elements: ");
    int i = q->front;
    while (1) {
        printf("%d ", q->items[i]);
        if (i == q->rear) break;
        i = (i + 1) % MAX_SIZE;
    }
    printf("\n");
}

int main() {
    CircularQueue q;
    initQueue(&q);

    ENQUEUE(&q, 10);
    ENQUEUE(&q, 20);
    ENQUEUE(&q, 30);
    ENQUEUE(&q, 40);
    DISPLAY(&q);

    DEQUEUE(&q);
    DEQUEUE(&q);
    DISPLAY(&q);

    // Enqueue new items to demonstrate circular reuse of freed slots
    ENQUEUE(&q, 50);
    ENQUEUE(&q, 60);
    ENQUEUE(&q, 70); // Wraps around to fill front slots
    DISPLAY(&q);

    // Triggers full condition check
    ENQUEUE(&q, 80);

    return 0;
}
```
### Key Differences & Complexities

1. **Full vs. Empty Conditions:**
   * **Empty:** `front == -1`
   * **Full:** `(rear + 1) % MAX_SIZE == front`

2. **Linear Queue Problem (False Overflow):**
   * In a linear queue, when `REAR` reaches the final index (`MAX_SIZE - 1`), no new elements can be added even if slots at the beginning are freed by `DEQUEUE` operations.

3. **Circular Queue Solution:**
   * Circular queue wraps `REAR` around to index `0` using modulo arithmetic, allowing $100\%$ memory utilization of freed spaces.

4. **Complexities:**
   * **Time:** `ENQUEUE`: $O(1)$ | `DEQUEUE`: $O(1)$ | `FRONT`: $O(1)$ | `DISPLAY`: $O(N)$
   * **Space:** $O(N)$
