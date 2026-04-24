## Wildcards in Java Generics
 
### What is a Wildcard?
      
You know `<T>` means "any type, but once you pick one, stick with it."
 
A wildcard `<?>` means **"I don't care what the type is."**

It's used when you want to write code that works with a generic type **without needing to know or fix what that type is.**

---

### Why do we need Wildcards?

Consider this:

```java
public static void printList(List<Object> list) {
    for (Object item : list) {
        System.out.println(item);
    }
}
```

Looks fine right? Now try calling it:

```java
List<String> names = List.of("Satwika", "Ravi");
printList(names);   // COMPILE ERROR
```

Even though `String` is a subtype of `Object`, **`List<String>` is NOT a subtype of `List<Object>`** in Java. This is where wildcards come in.

---

### There are 3 kinds of Wildcards

```
<?>              → Unknown wildcard      (read-only, any type)
<? extends T>    → Upper bounded         (T or its subtypes)
<? super T>      → Lower bounded         (T or its supertypes)
```

---

## 1. Unknown Wildcard `<?>`

### What it means
> *"I'll accept a list of **any** type — I just want to read from it."*

### Syntax
```java
public static void printList(List<?> list) {
    for (Object item : list) {
        System.out.println(item);
    }
}
```

### Usage
```java
List<String>  names   = List.of("Satwika", "Ravi");
List<Integer> numbers = List.of(1, 2, 3);
List<Double>  doubles = List.of(1.1, 2.2);

printList(names);    // Satwika Ravi
printList(numbers);  // 1 2 3
printList(doubles);  // 1.1 2.2
```

Works for all of them. Problem solved.

### Why can't you ADD to it?

```java
public static void addToList(List<?> list) {
    list.add("hello");   // COMPILE ERROR
    list.add(123);       // COMPILE ERROR
}
```

Because Java doesn't know what type the list holds. If it's a `List<Integer>` and you try adding a `String` — that's a disaster. So Java simply **blocks all adds** to be safe.

> `<?>` = **read only.** You can look inside, you cannot put anything in.

### Real use case — Utility methods
```java
// count how many non-null elements any list has
public static int countNonNull(List<?> list) {
    int count = 0;
    for (Object item : list) {
        if (item != null) count++;
    }
    return count;
}

countNonNull(List.of("a", "b", null));   // 2
countNonNull(List.of(1, 2, 3, null));    // 3
```

---

## 2. Upper Bounded Wildcard `<? extends T>`

### What it means
> *"I'll accept a list of **T or anything that extends T**."*

### Syntax
```java
public static double sumList(List<? extends Number> list) {
    double total = 0;
    for (Number n : list) {
        total += n.doubleValue();
    }
    return total;
}
```

### Usage
```java
List<Integer> ints    = List.of(1, 2, 3);
List<Double>  doubles = List.of(1.5, 2.5);
List<Float>   floats  = List.of(1.1f, 2.2f);

System.out.println(sumList(ints));     // 6.0
System.out.println(sumList(doubles));  // 4.0
System.out.println(sumList(floats));   // 3.3
```

All three work because `Integer`, `Double`, `Float` all **extend** `Number`.

### Why can't you ADD here either?

```java
public static void addNumber(List<? extends Number> list) {
    list.add(10);     // COMPILE ERROR
    list.add(3.14);   // COMPILE ERROR
}
```

Java knows the list holds *some subtype of Number* — but which one exactly? It could be `List<Integer>` or `List<Double>`. If you try adding a `Double` to a `List<Integer>` — broken. So again, **no adds allowed.**

> `<? extends T>` = safe for **reading**. You know everything coming out is at least a `T`.

### Real use case — Processing collections of related types

```java
// find the maximum value in any list of comparable items
public static <T extends Comparable<T>> T findMax(List<? extends T> list) {
    T max = list.get(0);
    for (T item : list) {
        if (item.compareTo(max) > 0) {
            max = item;
        }
    }
    return max;
}

System.out.println(findMax(List.of(3, 1, 4, 1, 5)));          // 5
System.out.println(findMax(List.of("banana", "apple", "mango"))); // mango
```

---

## 3. Lower Bounded Wildcard `<? super T>`

### What it means
> *"I'll accept a list of **T or anything that is a parent of T**."*

### Syntax
```java
public static void addNumbers(List<? super Integer> list) {
    list.add(1);
    list.add(2);
    list.add(3);
}
```

### Usage
```java
List<Integer> intList    = new ArrayList<>();
List<Number>  numList    = new ArrayList<>();
List<Object>  objectList = new ArrayList<>();

addNumbers(intList);     // works — Integer is Integer
addNumbers(numList);     // works — Number is parent of Integer
addNumbers(objectList);  // works — Object is parent of Integer
```

### Why CAN you add here?

Because you're saying *"this list holds Integer or something above Integer."* So adding an `Integer` is always safe — an `Integer` is definitely a valid `Integer`, `Number`, and `Object`.

But reading is the problem:

```java
public static void readFromList(List<? super Integer> list) {
    Integer val = list.get(0);  // COMPILE ERROR
    Object  obj = list.get(0);  // only this works
}
```

Java doesn't know if the list is `List<Integer>`, `List<Number>`, or `List<Object>`. So it can only safely give you back an `Object`.

> `<? super T>` = safe for **writing**. You know everything going in is at least a `T`.

### Real use case — Copying elements between lists

```java
// copy all elements from source into destination
public static <T> void copy(List<? extends T> source, List<? super T> dest) {
    for (T item : source) {
        dest.add(item);
    }
}

List<Integer> source = List.of(1, 2, 3);
List<Number>  dest   = new ArrayList<>();

copy(source, dest);
System.out.println(dest);   // [1, 2, 3]
```

`source` produces integers → `<? extends T>`
`dest` consumes integers → `<? super T>`

---

## PECS Rule — The Key to Remembering All Three

> **P**roducer → **E**xtends
> **C**onsumer → **S**uper

| You want to... | Use | Why |
|---|---|---|
| **Read** from a collection | `<? extends T>` | it produces values of type T |
| **Write** into a collection | `<? super T>` | it consumes values of type T |
| **Both** read and write | `<T>` | need exact type |
| **Just iterate**, don't care about type | `<?>` | fully flexible, read-only |

---

## Side by Side Comparison

```java
// Unknown — works for any list, read only
public static void print(List<?> list)

// Upper bounded — works for Number and subtypes, read only
public static void sum(List<? extends Number> list)

// Lower bounded — works for Integer and supertypes, write safe
public static void fill(List<? super Integer> list)
```

---

## Real Backend Use Case — Spring Boot Style

```java
// a service method that processes any list of DTOs
public static void processItems(List<? extends BaseDTO> items) {
    for (BaseDTO item : items) {
        System.out.println(item.getId());   // safe — all are BaseDTO
    }
}
```

```java
// works for UserDTO, OrderDTO, ProductDTO — all extending BaseDTO
processItems(userList);
processItems(orderList);
processItems(productList);
```

One method handles all your DTOs. No duplication.

---

## Quick Summary

```
<?>              → any type, read only, use for utility/iteration methods
<? extends T>    → T or subtypes, read only, use when consuming/reading data
<? super T>      → T or supertypes, write safe, use when producing/adding data
PECS             → Producer Extends, Consumer Super
```

---

Want me to generate the `.md` file for this so you can push it to `03_Core_APIs/Generics/`?
