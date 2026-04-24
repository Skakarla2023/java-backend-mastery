# Generics in Java

## What are Generics?

Generics allow you to define **classes, interfaces, and methods** with a **type parameter** —
a placeholder for a data type that gets specified at the time of use.

They provide:
- **Type safety** at compile time
- **No explicit casting** needed
- **Code reusability** — write once, work for any type

> Think of it as a **labeled box** — instead of a box that holds anything (and surprises you),
> you label it upfront: *"this box holds only Strings."*

---

## Without Generics vs With Generics

### Without Generics — Unsafe

```java
List list = new ArrayList();
list.add("Hello");
list.add(123);                      // Java allows this — no restriction

String s = (String) list.get(1);    // ClassCastException at runtime!
```

### With Generics — Safe

```java
List<String> list = new ArrayList<>();
list.add("Hello");
list.add(123);                      // compile-time ERROR — caught immediately

String s = list.get(0);             // no casting needed
```

---

## Type Inference

Type inference means **Java figures out the type on its own** by looking at your code context.
You don't need to spell it out.

### Method Call Inference

```java
public static <T> void print(T val) {
    System.out.println(val);
}

print("hello");     // Java sees String  → T = String
print(42);          // Java sees Integer → T = Integer
print(3.14);        // Java sees Double  → T = Double
```

You never wrote `T = String` anywhere. Java looked at the argument and inferred it.

### Diamond Operator `<>`

```java
// Without inference — you repeat yourself
List<String> list = new ArrayList<String>();

// With inference — Java reads the left side
List<String> list = new ArrayList<>();   // T inferred as String
```

The empty `<>` is called the **diamond operator** — it tells Java: *"you figure it out."*

### Return Type Inference

```java
public static <T> T getFirst(T[] arr) {
    return arr[0];
}

String s = getFirst(new String[]{"a", "b"});    // T = String, returns String
Integer n = getFirst(new Integer[]{10, 20});    // T = Integer, returns Integer
```

Java looks at both — what you passed in AND where the result is going — and connects the dots.

### Where Inference Happens

| Situation          | Example                        | What Java looks at          |
|--------------------|--------------------------------|-----------------------------|
| Method argument    | `print("hello")`               | The argument type           |
| Diamond operator   | `new ArrayList<>()`            | The left side of `=`        |
| Return type        | `String s = getFirst(arr)`     | The variable receiving result |

---

## Generic Classes

A generic class is a **blueprint with a type placeholder** — written once, type gets locked in
when you create an object from it.

### Syntax

```java
public class ClassName<T> {
    // use T anywhere inside as a type
}
```

### Example 1 — Basic Generic Class

```java
public class Box<T> {
    private T value;

    public void set(T value) {
        this.value = value;
    }

    public T get() {
        return value;
    }
}
```

```java
Box<String> stringBox = new Box<>();
stringBox.set("Hello");
System.out.println(stringBox.get());    // Hello

Box<Integer> intBox = new Box<>();
intBox.set(100);
System.out.println(intBox.get());       // 100
```

### Example 2 — Multiple Type Parameters

```java
public class Pair<K, V> {
    private K key;
    private V value;

    public Pair(K key, V value) {
        this.key = key;
        this.value = value;
    }

    public K getKey()   { return key; }
    public V getValue() { return value; }
}
```

```java
Pair<String, Integer> age  = new Pair<>("Satwika", 21);
System.out.println(age.getKey());       // Satwika
System.out.println(age.getValue());     // 21
```

> This is exactly how `Map.Entry<K, V>` works internally in Java.

### Example 3 — Bounded Generic Class

```java
public class NumberBox<T extends Number> {
    private T value;

    public NumberBox(T value) {
        this.value = value;
    }

    public double doubleValue() {
        return value.doubleValue();     // safe — T is guaranteed to be a Number
    }
}
```

```java
NumberBox<Integer> intBox = new NumberBox<>(42);
System.out.println(intBox.doubleValue());   // 42.0

NumberBox<String> strBox = new NumberBox<>("hi");   // compile error
```

### Rules of Generic Classes

| Rule                          | Example                          |
|-------------------------------|----------------------------------|
| T is decided at object creation | `new Box<String>()`            |
| T applies to the whole object  | every method sees the same T    |
| Cannot use primitive types     | `Box<int>` ❌ → `Box<Integer>` ✅ |
| Multiple parameters allowed    | `<K, V>`, `<A, B, C>`           |
| Can be bounded                 | `<T extends Number>`            |

---

## Generic Methods

A generic method is a **regular method with a type placeholder `<T>`**, so the same method
works for any data type without rewriting it.

### Syntax

```java
public static <T> void methodName(T param) {
    // T is declared before return type
    // T is used as parameter type
}
```

```
public static <T> void print(T val)
               ↑           ↑
         declaring T     using T
     ("T exists")     (as parameter type)
```

### Example 1 — Basic Generic Method

```java
public static <T> void print(T val) {
    System.out.println(val);
}

print(42);          // 42
print("Satwika");   // Satwika
print(3.14);        // 3.14
```

### Example 2 — Generic Method with Return Type

```java
public static <T> T getFirst(T[] arr) {
    return arr[0];
}

String first = getFirst(new String[]{"a", "b", "c"});   // "a"
Integer num  = getFirst(new Integer[]{10, 20, 30});      // 10
```

Return type is also `T` — output type matches input type. No casting needed.

### Example 3 — printArray

```java
public static <T> void printArray(T[] arr) {
    for (T item : arr) {
        System.out.print(item + " ");
    }
}

printArray(new Integer[]{1, 2, 3});         // 1 2 3
printArray(new String[]{"a", "b", "c"});    // a b c
```

### Generic Class vs Generic Method

| | Generic Class | Generic Method |
|---|---|---|
| T is defined | at class level | at method level |
| T is fixed | for the whole object | only for that one call |
| Type specified | at object creation | inferred from argument |
| Example | `Box<String> b = new Box<>()` | `print("hello")` |

---

## Common Type Parameter Conventions

| Symbol | Meaning                        |
|--------|-------------------------------|
| `T`    | Type (general purpose)        |
| `E`    | Element (used in collections) |
| `K`    | Key (used in maps)            |
| `V`    | Value (used in maps)          |
| `N`    | Number                        |

---

## One Line Summary

> Generics = **write once, work for any type, with full compile-time safety.**
