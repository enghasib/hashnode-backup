---
title: "You don't know Closure"
seoTitle: "Understanding Closure Concepts"
datePublished: Wed Jul 02 2025 16:53:32 GMT+0000 (Coordinated Universal Time)
cuid: cmcm74bbt000g02jma2qc3yjy
slug: go-closure
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1751474632543/1559ab13-f765-48f6-8f7c-b10aac37bc80.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1751475158900/05358498-6cc3-45f0-93f4-9496691d02d9.png
tags: closure, go, closures, closure-with-example, go-closure

---

## 🔐 What is a **Closure** in Go?

A **closure** is a **function value** that **remembers the variables from the scope** in which it was created.

> ✅ In simple terms:  
> A closure "closes over" its environment and keeps those variable values alive even after the outer function exits.

### 🔧 Basic Syntax

```go
func outer() func() {
    x := 10
    return func() {
        fmt.Println(x)
    }
}
```

Here:

* The inner function `func() { fmt.Println(x) }` is a **closure**.
    
* It captures the variable `x` from its **outer** scope.
    

## ⚠️ Common Confusion: Closure vs Normal Function

* A normal function doesn’t retain local variables after returning.
    
* A **closure** retains state from the outer function.
    

## A visual diagram of closures:

![](https://sdmntprnorthcentralus.oaiusercontent.com/files/00000000-8c34-622f-888a-a39fde3c871f/raw?se=2025-07-02T17%3A29%3A27Z&sp=r&sv=2024-08-04&sr=b&scid=bd5615fb-82cf-506b-b25e-bedd95706d49&skoid=7399a3a4-0259-4d43-bcd6-a56ceeb4c28b&sktid=a48cca56-e6da-484e-a814-9c849652bcb3&skt=2025-07-02T01%3A35%3A03Z&ske=2025-07-03T01%3A35%3A03Z&sks=b&skv=2024-08-04&sig=Cxr6%2Bizqsd1KGfE3Mm6UPzBVA0cvkTlMS1BFcqJb8Kk%3D align="left")

## 💼 Real-Life Use Cases

| Use Case | How Closures Help |
| --- | --- |
| Generators | Return a function that tracks state |
| Event handlers | Wrap logic that depends on context |
| Custom sort logic | Pass behavior into functions |
| Encapsulation | Hide variables from outer world |

**Go closures capture variables** from outer scopes. Let's **explain exactly what happens** step by step, including what is captured and how Go handles it.

```go
const a int = 10    // constant (compile-time known)
var p int = 20      // global variable (heap)

func outer() func() {
	var money int = 100 // local variable (captured)
	var age int = 30    // local variable (not used in closure)

	show := func() {
		money = money + a + p // uses money (local), a (const), p (global)
		fmt.Println(money)
	}
	return show
}
```

## 🧪 What Gets Captured and How?

| Variable | Type | Captured by Closure? | How it's handled in Go |
| --- | --- | --- | --- |
| `a` | Constant | ✅ Yes | Inlined at compile time |
| `p` | Global | ✅ Yes | Captured as reference |
| `money` | Local | ✅ Yes | Stored in closure's heap |
| `age` | Local | ❌ No (not used) | Optimized away |

## 🔬 Explanation of Behavior

1. `a` (constant):
    
    * Constants in Go are **inlined at compile time**.
        
    * So `a` is treated as a literal `10`.
        
2. `p` (global variable):
    
    * `p` is declared outside the function.
        
    * It is **captured by reference**, so any changes to `p` Outside will affect closure behavior (if accessed).
        
3. `money` (local variable):
    
    * Local to `outer()`.
        
    * Since it's **used in the closure**, Go **allocates it on the heap**, not the stack.
        
    * This allows the inner function to access it even after `outer()` it has returned.
        
4. `age` (unused variable):
    
    * It's not used in the closure, so Go will **optimize it away** (won’t be captured or retained).
        

## 🔁 Here is a Problem:

> “If `show()` is defined **inside** `outer()`, and `outer()` has **already returned**, then how can I still call `show()` later, when `outer()`’s **stack frame** is gone?”

This is **a valid question** — in most languages, local variables are destroyed when the function returns.

## 🧠 But here's the magic: **Closures change that!**

### 🔓 **Go’s Solution: Escape Analysis + Heap Allocation**

Go uses a mechanism called **escape analysis** during compilation.

> If a **local variable is accessed by a closure**, Go **moves** that variable from the stack to the **heap** instead of destroying it with the function’s stack frame.

## 📦 What Happens Internally

Let’s look at this code again:

```go
func outer() func() {
	var money int = 100
	show := func() {
		money += 10
		fmt.Println(money)
	}
	return show
}
```

### 🔬 Internally:

1. `money` is **declared inside** `outer()` (normally stack).
    
2. `show` **references** `money`, and `show` is returned.
    
3. Go **detects** that `money` is used outside `outer()` — So it moves `money` to the **heap**.
    
4. `show()` retains a **pointer** to `money` it on the heap.
    
5. When you call `show()`, it **still has access** `money` via that pointer.
    

## 💡 Analogy

Imagine `outer()` writes a **note** (a variable like `money`) and leaves it on a **public notice board** (the heap). Even after `outer()` the leaves (stack is cleared), anyone with a reference (like `show`) can still **read and update** that note.

💬 **Closures extend the lifetime of captured variables** beyond their original stack frame — by **allocating them on the heap**.

##### You can even see this decision by running:

```bash
go build -gcflags="-m" yourfile.go
```

## 📦 Is Heap Allocation Always the Default for Closures?

Not **always**, but for most practical closures — **yes**. 👉 If the variable:

* is used by an inner function
    
* And the inner function **outlives** the outer one  
    → It **must be moved to the heap**
    

# 🧠 Let's clarify a doubt

> What happened If `outer()` returns a primitive value (like `int`), it behaves like a **normal function** or **formed a closure** ?

### 🧪 Answer 1: No Closure Needed for primitive return.

```go
func outer() int {
	x := 5
	return x // 👈 value is copied, stack-safe
}
```

* `x` is stored on the **stack**.
    
* Returned as a value → copied.
    
* No closure.
    
* ❌ Nothing escapes → no heap allocation.
    

### 🧪 Answer 2: Closure Needed if the Function returns using the outer variable.

```go
func outer() func() {
	x := 5

	return func() {
		fmt.Println(x) // 👈 closure: captures `x`
	}
}
```

* Now the **returned function uses** `x`.
    
* `x` must **survive after outer() exits**.
    
* So, Go **moves** `x` to the heap.
    
* The inner function holds a **pointer to x**.
    

✅ This is the **default behavior when closures are formed**.

🧠 Let's summarize the Closure Behavior, Go closures are **not magic** — they’re **syntactic sugar** over structs + function pointers. Any captured variable that **escapes the function** goes to the **heap**. Escape analysis + garbage collector handles it efficiently. Thank you for reading this article.