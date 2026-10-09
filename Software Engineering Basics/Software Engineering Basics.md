
# Object Oriented Programming
## What is OOP and why does it exist?

- _What it is:_ an object bundles **data + the behaviour that acts on that data** together in one place. Your `User` isn't just name/email/password sitting there — it also carries the methods that work on them (`set_email`, `get_name`). Data and behaviour, together.
- _Why it exists:_ to manage **complexity as code grows**. In a tiny script you don't need it. But at thousands of lines, keeping each thing's data and rules bundled in its own class is what stops the codebase becoming spaghetti. That's the real reason — not "it's easier," but _specifically_ "it keeps growing code organised."
## Encapsulation

So the real point of encapsulation isn't "hide the data for secrecy" — it's: **by forcing all changes through a setter, you get one guarded doorway where you can validate, so the object can never hold invalid data.**

You built `set_email` to `raise ValueError` on a bad email. So think concretely: if `email` were just public, someone could write `user.email = "aka"` or `user.email = 12345` or `user.email = None` — and **nothing would stop them.** The object would hold garbage, and it'd blow up somewhere far away, later.

![[Pasted image 20261002105425.png]]

Encapsulation is ==the process of bundling data and the methods that operate on that data into a single unit, like a class, while restricting direct access to the inner details==. [[1](https://www.sumologic.com/glossary/encapsulation), [2](https://www.geeksforgeeks.org/java/encapsulation-in-java/)]

How It Works

- **Bundling:** Variables (data) and functions (methods) live together inside a single container or class.

- **Data Hiding:** Internal variables are marked as private so outside code cannot change them directly.

- **Controlled Access:** Public methods—known as getters and setters—are provided to safely read or update the hidden data. [[1](https://www.youtube.com/shorts/eCTgZQRbgXk), [2](https://www.instagram.com/reel/DQ9pqoSCLG5/?hl=en), [3](https://www.coursera.org/articles/encapsulation), [4](https://www.geeksforgeeks.org/java/encapsulation-in-java/)]

Why It Matters

- **Security:** Prevents outside code from setting invalid or harmful values.

- **Simplicity:** Hides complex inner workings and only shows what is needed to interact with the object.

- **Maintenance:** Lets developers change internal code safely without breaking other parts of the program. [[1](https://en.wikipedia.org/wiki/Encapsulation_\(computer_programming\)), [2](https://www.coursera.org/articles/encapsulation), [3](https://www.instagram.com/reel/DQ9pqoSCLG5/?hl=en)]
## Polymorphism

"Same method, different behavior per type." That's polymorphism in one line.
## Inheritance

Your answer ("when children are too different") is one case. But the sharper rule is about the **relationship**, and it has a name: the **"is-a" test.** Inheritance is only right when the child genuinely _is a kind of_ the parent:

- A `Note` **is a** kind of `Item`. ✅
- A `Task` **is a** kind of `Item`. ✅

That's why your tree works.

The mistake people make is reaching for inheritance just to **reuse code**, when there's no real "is-a." Classic example: say `User` and `Note` both happen to need a `created_at` date. Tempting to make one inherit from the other to share that code, right? But a `User` **is not a** `Note`, and a `Note` **is not a** `User`. They're unrelated things that just share a field. Forcing inheritance there creates a fake, confusing hierarchy. The honest answer is: they _both_ might be `Item`s (is-a, fine), or you share that code a different way (composition — a topic for later).

So the rule to remember: **inherit only when "child is-a parent" is actually true.** If you're reaching for inheritance just because it saves typing, but the "is-a" sentence sounds wrong out loud, that's the anti-pattern — stop.
## Abstraction

Abstraction = **showing _what_ something does, hiding _how_ it does it.**

You've already used it today without naming it. Look at this call:

python

```python
user.set_email("nimesh@gmail.com")
```

When you call `set_email`, do you think about the regex, the validation check, the raise? No. You just think "set the email." All the messy _how_ is hidden inside the method. You only deal with the simple _what_. That hiding of the messy details behind a simple name — that's abstraction.

Same with your `summary()`:

python

```python
for item in items:
    print(item.summary())
```

The loop just says "give me a summary." It doesn't know or care _how_ a Note builds its string versus how a Task builds its string. The _how_ is hidden; you only use the _what_. Abstraction.

**The parent-promise version** (what we did with `Item`): `Item` declares "every item **must have** a `summary()`" — that's the _what_ — but it refuses to say _how_, leaving each child to decide. The parent defines the requirement, hides the implementation. That's abstraction at the design level.

**Don't mix it up with encapsulation** — this is the trap:

- **Encapsulation hides _data_** — your private `__email`, locked behind getters/setters.
- **Abstraction hides _complexity_** — the messy _how_ of a method, behind a simple name.

One sentence to keep: **encapsulation hides the data; abstraction hides the work.** You press the button (`set_email`) and it just works — you don't see the wiring behind it.


# How Code Actually Runs

## References (the big idea)
- A variable doesn't hold the object — it holds the object's **address**.
- `b = a` copies the **address**, not the object. Both names point to the same object.

## Mutation vs Reassignment
- **Mutation** = change the object's insides → affects everyone pointing to it.
  - `b.append(4)`, `b[0] = 9`, `b.sort()`
- **Reassignment** = point a name at a NEW object → only moves that one name.
  - `b = [9, 9]`
- `a = [1,2,3]; b = a`
  - `b.append(4)` → a and b both `[1,2,3,4]`  (mutation)
  - `b = [9,9]`   → a stays `[1,2,3]`, b is `[9,9]`  (reassignment)

## Stack vs Heap
- **Heap** → where the actual **objects** live (lists, User, Note...).
- **Stack** → where **names** and function calls live. A name holds the heap address.
- So "reference" = stack name holds heap address. That's why shared names see the same change.

## Frames (function calls)
- Each function call gets a **frame** on the stack holding its local variables.
- Call a function → frame created. Function returns → frame **destroyed instantly**, its locals vanish.
- That's why a variable inside a function can't be used outside it.
- A traceback IS the call stack printed out.

## Garbage Collector (different from frames)
- Frame removal = **stack**, about **names**, instant on return.
- Garbage collector = **heap**, about **objects**, frees objects nothing points to anymore (later).
- Often a frame dying is what makes an object unreachable → then GC cleans it.

## Everything is an object
- In Python, everything is an object (ints, strings, lists, functions, even None).
- Unlike Java, there are no primitives.

## Mutable vs Immutable
- **Immutable** (can't change): int, str, tuple → operations make a new object.
- **Mutable** (can change in place): list, dict, set, your classes.
- This is WHY append on a shared list surprised us, but math on a number never does.

## ⚠️ Mutable Default Argument (real bug)
- A default value is created **once**, when the function is defined — NOT per call.
- `def add(item, box=[])` → every call shares the SAME list → it grows across calls.
- Danger in StudyHub: `def __init__(self, notes=[])` → all users share one notes list.
- **Fix:** use None, create inside:
    def __init__(self, notes=None):
        if notes is None:
            notes = []
        self.__notes = notes

## One-line takeaways
- Mutation changes the object; reassignment changes which object a name points to.
- Name = stack, Object = heap, Reference = the address linking them.
- Never use a mutable default (`[]`, `{}`) in a function signature.

## Primitive & Non Primitive in Programming

| Feature             | Primitive Data Types                                                      | Non-Primitive Data Types                                                  |
| ------------------- | ------------------------------------------------------------------------- | ------------------------------------------------------------------------- |
| **What they store** | The actual single value.                                                  | A memory address (reference) pointing to the data.                        |
| **Memory Location** | Usually stored on the **Stack** (fast access).                            | The reference is on the stack, but the actual data lives on the **Heap**. |
| **Mutability**      | **Immutable**; the core value cannot be altered once created.             | **Mutable**; their internal properties or elements can be modified.       |
| **Size**            | Fixed and predefined by the language.                                     | Dynamic; size can change at runtime.                                      |
| **Nullability**     | Cannot hold a `null` value (they use default values like `0` or `false`). | Can be `null` to indicate the absence of an object.                       |
| **Methods**         | Do not have built-in methods or properties.                               | Can contain methods and custom behaviors.                                 |
| **Examples**        | `int`, `float`, `char`, `boolean`, `double`.                              | `Array`, `Class`, `Interface`, `String`, `Linked List`.                   |

# Python Data Structures

## List
mylist = ["apple", "banana", "cherry"]


## Hash Maps
==*Hash maps are really important keep for a another day!*==

I stuck here

# How does the python compile works
