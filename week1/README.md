# Week 1 — Python Internals: Object Model, Memory, Refcounting, GC

**Cycle 1 · Week 1** · Status: 🟡 in progress

**Weekly target (definition of done):** a memory-behaviour notebook + article showing object layout,
interning, copy vs reference, and the measured memory footprint of `list` / `tuple` / `array` / NumPy
for the same data.

**In this folder:** [`ref_count.ipynb`](ref_count.ipynb) — every idea below, runnable, with outputs.

---

## 1. Everything is an object

There are no primitives in Python. An `int`, a `list`, a function, a class, even a module — each one is
a block on the heap carrying a **type**, an **id** (its address), a **refcount**, and its value.

```
  ┌────────────────────┐
  │ ob_refcnt :  2     │  ← how many arrows point here
  │ ob_type   :  list  │  ← what it is
  │ value     :  [1,2,3]
  └────────────────────┘
```

`id(x)` gives the address, `type(x)` the type. That header is the whole object model — everything else
in this week is just watching the first field change.

## 2. A variable is a reference, not a box

Assignment never copies. `b = a` writes a second name onto the *same* object and bumps the refcount.
So `b is a` is `True`, and mutating through one name is visible through the other.

```
   a = [1,2,3]          a ──►  [1,2,3]                count = 1

   b = a                a ──►  [1,2,3]  ◄── b         count = 2
```

Two ways to read the count: `sys.getrefcount(x)` (always **+1**, the temporary argument it receives),
or `ctypes.c_long.from_address(id(x))`, which reads the header directly and gives the true number.

## 3. Orphaned objects

Drop the last arrow and the count reaches `0`. CPython destroys the object **on that line** — no
collector, no delay. That is why `__del__` fires immediately when you reassign or `del` the last name.

```
   a ──► Ghost(1)     ──  a = None  ──►     Ghost(0)   ✗ destroyed instantly
```

This is also the cheap-but-not-free part: every assignment, argument pass and scope exit is an
increment or decrement, which is exactly why CPython pays a per-operation cost that JVM-style
tracing collectors do not.

## 4. Circular references — where refcounting breaks

Two objects pointing at each other keep each other alive. Delete both outside names and the counts
still sit at `1` — unreachable from your code, but never freed. A leak.

```
   a ──►  A ──────────►  B  ◄── b
          ▲              │
          └──────────────┘

   del a, b   →      A ──────────►  B          counts still 1 each
                     ▲              │          unreachable, never freed
                     └──────────────┘
```

## 5. The GC exists only for this case

The `gc` module periodically walks the heap looking for groups of objects that reference only each
other and nothing reachable from the outside, and frees the whole group. Two systems, two jobs:

```
   refcounting  →  frees anything that hits 0        immediate, most of the work
   gc           →  frees unreachable cycles          periodic, or gc.collect() by hand
```

In the notebook the collector is switched off with `gc.disable()` so the leak is visible, then
`gc.collect()` cleans it — you can see both objects die at once.

---

## Sources

- [Real Python — Object References](https://realpython.com/videos/object-references/) (video)
- [Python Memory Management / refcounting](https://youtu.be/IjE113_l66s) (video)

## Still to come this week

- Interning (small ints, strings) and why `a is b` sometimes surprises you
- Copy vs reference: shallow vs deep copy
- Measured memory footprint: `list` vs `tuple` vs `array` vs NumPy for the same data
- The write-up / article
