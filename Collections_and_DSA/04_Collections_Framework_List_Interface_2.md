# 04 - List Interface

## List Interface nima?

**List Interface** - bu elementlarni tartiblangan (ordered) tarzda saqlash uchun mo'ljallangan interface. Foydalanuvchi har bir element qaerga joylashishini aniq nazorat qilishi mumkin.

**Asosiy xususiyatlari:**
- Elementlar indeks (tartib raqami) bo'yicha mavjud
- Takrorlangan elementlarga ruxsat beriladi
- Elementlarni qidirish mumkin

**Concrete implementation'lar:**
1. **ArrayList** - Dinamik array asosida
2. **LinkedList** - Linked list asosida
3. **Vector** - Thread-safe dynamic array (eski, deprecated)
4. **Stack** - LIFO (Last In First Out) strukturasi

**Eslatma:** Vector class'i JDK 1.5 dan beri deprecated hisoblanadi.

## ListInterfaceTest - List Interface Misoli

```java
package collectionsframework.list;

import java.util.Comparator;
import java.util.List;

public class ListInterfaceTest {
    public static void main(String[] args) {
        // List.of() - Java 9+ da immutable list yaratish
        List<Integer> integers = List.of(12, 45, 90, 12);
        
        // Asosiy metodlar:
        System.out.println("Element 0: " + integers.get(0));        // 12
        System.out.println("Contains 1: " + integers.contains(1));  // false
        System.out.println("First index of 12: " + integers.indexOf(12));     // 0
        System.out.println("Last index of 12: " + integers.lastIndexOf(12));  // 3
        System.out.println("Is empty: " + integers.isEmpty());      // false
        System.out.println("Size: " + integers.size());             // 4
        
        // Immutable list - sort qilish mumkin emas
        // integers.sort(Comparator.naturalOrder()); //  UnsupportedOperationException
    }
}
```

**Vazifasi:** List interface'ning asosiy metodlarini ko'rsatish.

**Muhim:** `List.of()` Java 9+ da immutable (o'zgartirib bo'lmaydigan) list yaratadi.

## ArrayList Class

**ArrayList** - xotirada kerak bo'lganda kengayadigan yoki qisqaydigan dinamik massiv.

**Xususiyatlari:**
- **Dinamik massiv** - Ichki ko'rinishda array ishlatadi (Java 1.2 dan beri)
- **Zero-based index** - Elementlar 0 dan boshlangan indeks bilan joylashadi
- **Avtomatik kengayish** - Sig'im oshsa, yangi katta massiv yaratiladi
- **Capacity va Size** - Capacity avtomatik qisqarmaydi, faqat size qisqaradi

## ArrayListTest - ArrayList Misollari

```java
package collectionsframework.list;

import java.util.ArrayList;
import java.util.Comparator;
import java.util.UUID;

public class ArrayListTest {
    public static void main(String[] args) {
        // 1. ArrayList yaratish
        ArrayList<Integer> integers1 = new ArrayList<>();
        integers1.add(123);
        integers1.add(188);
        integers1.add(8);
        integers1.add(176);
        
        // 2. Boshqa listdan ArrayList yaratish (copy constructor)
        ArrayList<Integer> integers2 = new ArrayList<>(integers1);
        integers2.add(34);
        
        System.out.println("integers2: " + integers2); // [123, 188, 8, 176, 34]
        
        // 3. Index bo'yicha element qo'shish
        integers2.add(0, 45); // Boshiga qo'shish
        System.out.println("0-indexga 45 qo'shildi: " + integers2);
        
        // 4. Elementni o'zgartirish (replace)
        integers2.set(0, 90); // 0-indexdagi elementni 90 ga o'zgartiradi
        System.out.println("0-index 90 ga o'zgardi: " + integers2);
        
        // 5. Index bo'yicha element o'chirish
        integers2.remove(0); // 0-indexdagi element o'chirildi
        System.out.println("0-index o'chirildi: " + integers2);
        
        // 6. Qiymat bo'yicha element o'chirish
        integers2.remove(new Integer(34)); // 34 qiymatli element o'chirildi
        System.out.println("34 qiymati o'chirildi: " + integers2);
        
        // 7. Barcha elementlarni o'chirish (removeAll)
        integers2.removeAll(integers1);
        System.out.println("integers1 elementlari o'chirildi: " + integers2);
        
        // 8. Boshqa metodlar
        System.out.println("\n--- Boshqa Metodlar ---");
        
        // forEach - har bir element uchun amal bajarish
        integers1.forEach(o -> System.out.print(o + " "));
        System.out.println();
        
        // Method reference bilan
        integers1.forEach(System.out::print);
        System.out.println();
        
        // Sort qilish
        System.out.println("\n--- Sort qilish ---");
        ArrayList<Integer> numbers = new ArrayList<>(List.of(5, 2, 8, 1, 9));
        System.out.println("Avval: " + numbers);
        numbers.sort(Comparator.naturalOrder()); // O'sish tartibida
        System.out.println("Natural order: " + numbers);
        numbers.sort(Comparator.reverseOrder()); // Kamayish tartibida
        System.out.println("Reverse order: " + numbers);
        
        // 9. Custom class bilan ishlash
        ArrayList<User> users = new ArrayList<>();
        
        users.add(new User(30, "User 1"));
        users.add(new User(32, "User 2"));
        users.add(new User(16, "User 3"));
        users.add(new User(13, "User 4"));
        
        System.out.println("\n--- Users (Unsorted) ---");
        showUsers(users);
        
        // Comparable interface'i bo'yicha sort
        users.sort(Comparator.naturalOrder());
        System.out.println("\n--- Users (Sorted by age - descending) ---");
        showUsers(users);
        
        // Custom comparator bilan sort
        users.sort((u1, u2) -> u1.toString().compareTo(u2.toString()));
        System.out.println("\n--- Users (Sorted by toString) ---");
        showUsers(users);
        
        // 10. ArrayList method'lari
        System.out.println("\n--- ArrayList Method'lari ---");
        ArrayList<String> fruits = new ArrayList<>();
        
        // add() - element qo'shish
        fruits.add("Apple");
        fruits.add("Banana");
        fruits.add("Cherry");
        System.out.println("Fruits: " + fruits);
        
        // get() - element olish
        System.out.println("Element at index 1: " + fruits.get(1));
        
        // contains() - element borligini tekshirish
        System.out.println("Contains 'Apple': " + fruits.contains("Apple"));
        
        // indexOf() - element indeksini topish
        System.out.println("Index of 'Banana': " + fruits.indexOf("Banana"));
        
        // isEmpty() - bo'shlikni tekshirish
        System.out.println("Is empty: " + fruits.isEmpty());
        
        // size() - elementlar soni
        System.out.println("Size: " + fruits.size());
        
        // clear() - barcha elementlarni o'chirish
        fruits.clear();
        System.out.println("After clear - Size: " + fruits.size());
        
        // 11. Capacity bilan ishlash
        System.out.println("\n--- Capacity Management ---");
        ArrayList<Integer> withCapacity = new ArrayList<>(100); // Initial capacity = 100
        System.out.println("Initial capacity 100 bilan yaratildi");
        
        // trimToSize() - capacity ni size ga moslashtirish
        withCapacity.add(1);
        withCapacity.add(2);
        withCapacity.add(3);
        withCapacity.trimToSize(); // Capacity ni 3 ga qisqartiradi
        System.out.println("trimToSize() dan keyin");
    }
    
    private static void showUsers(ArrayList<User> users) {
        for (User user : users) {
            System.out.println(user);
        }
    }
}

// Comparable interface'ni implement qilgan class
class User implements Comparable<User> {
    private final String id;
    private final int age;
    private final String name;
    
    User(int age, String name) {
        this.id = UUID.randomUUID().toString();
        this.age = age;
        this.name = name;
    }
    
    // CompareTo - yosh bo'yicha kamayish tartibida (descending)
    @Override
    public int compareTo(User user) {
        // Teskari tartib: katta yosh -> kichik
        return Integer.compare(user.age, this.age);
        // Yoki: return -1 * Integer.compare(this.age, user.age);
    }
    
    @Override
    public String toString() {
        return "User{" +
                "id='" + id + '\'' +
                ", age=" + age +
                ", name='" + name + '\'' +
                '}';
    }
    
    // Getter metodlari
    public int getAge() { return age; }
    public String getName() { return name; }
}
```

## JavaVectorTest - Vector Class'i

```java
package collectionsframework.list;

import java.util.Vector;

public class JavaVectorTest {
    public static void main(String[] args) {
        // Vector - Thread-safe ArrayList (eski versiya)
        Vector<String> languages = new Vector<>();
        languages.add("Java");
        languages.add("Python");
        languages.add("JavaScript");
        
        System.out.println("Languages: " + languages);
        System.out.println("Size: " + languages.size());
        System.out.println("Capacity: " + languages.capacity()); // Vector'da capacity metod bor
        
        // Vector'ning o'ziga xos metodlari
        languages.addElement("C++"); // add() ga o'xshash
        languages.insertElementAt("Ruby", 1); // Index bo'yicha qo'shish
        
        System.out.println("After additions: " + languages);
        System.out.println("First element: " + languages.firstElement());
        System.out.println("Last element: " + languages.lastElement());
        
        // Element o'chirish
        languages.removeElement("Python");
        System.out.println("After removing Python: " + languages);
        
        // Vector vs ArrayList
        System.out.println("\n--- Vector vs ArrayList ---");
        
        //  Vector - Thread-safe (synchronized)
        //  Vector - Performance pastroq
        //  ArrayList - Performance yaxshi
        //  ArrayList - Thread-safe emas
        
        // Zamonaviy yondashuv: ArrayList + Collections.synchronizedList()
        // List<String> syncList = Collections.synchronizedList(new ArrayList<>());
    }
}
```

## ArrayList Performance Tahlili

```java
package collectionsframework.list;

import java.util.ArrayList;
import java.util.LinkedList;

public class PerformanceTest {
    public static void main(String[] args) {
        int size = 100000;
        
        // 1. ArrayList - Tez random access
        ArrayList<Integer> arrayList = new ArrayList<>();
        
        long start = System.currentTimeMillis();
        for (int i = 0; i < size; i++) {
            arrayList.add(i); // Oxiriga qo'shish - O(1)
        }
        long arrayListAddTime = System.currentTimeMillis() - start;
        
        start = System.currentTimeMillis();
        for (int i = 0; i < size; i++) {
            arrayList.get(i); // Random access - O(1)
        }
        long arrayListGetTime = System.currentTimeMillis() - start;
        
        // 2. LinkedList - Tez insert/delete
        LinkedList<Integer> linkedList = new LinkedList<>();
        
        start = System.currentTimeMillis();
        for (int i = 0; i < size; i++) {
            linkedList.add(i); // Oxiriga qo'shish - O(1)
        }
        long linkedListAddTime = System.currentTimeMillis() - start;
        
        start = System.currentTimeMillis();
        for (int i = 0; i < size; i++) {
            linkedList.get(i); // Random access - O(n) - YOMON!
        }
        long linkedListGetTime = System.currentTimeMillis() - start;
        
        System.out.println("Performance Natijalari:");
        System.out.println("ArrayList add (" + size + " element): " + arrayListAddTime + "ms");
        System.out.println("ArrayList get (" + size + " element): " + arrayListGetTime + "ms");
        System.out.println("LinkedList add (" + size + " element): " + linkedListAddTime + "ms");
        System.out.println("LinkedList get (" + size + " element): " + linkedListGetTime + "ms");
        
        // Qoida: Random access kerak bo'lsa -> ArrayList
        //        Ko'p insert/delete kerak bo'lsa -> LinkedList
    }
}
```

## ArrayList va LinkedList Taqqoslash

| Xususiyat | ArrayList | LinkedList |
|-----------|-----------|------------|
| **Asosiy tuzilma** | Dynamic array | Doubly linked list |
| **Random access** | O(1) - Juda tez | O(n) - Sekin |
| **Element qo'shish** | Oxiriga: O(1), O'rtaga: O(n) | Har qaerda: O(1) |
| **Element o'chirish** | Oxiridan: O(1), O'rtadan: O(n) | Har qaerda: O(1) |
| **Memory usage** | Kamroq (faqat data) | Ko'proq (node + pointer) |
| **Use case** | Ko'p o'qish, kam o'zgartirish | Ko'p o'zgartirish |

## ArrayList bilan Ishlash Bo'yicha Maslahatlar

### 1. Initial Capacity belgilash
```java
// Agar elementlar soni ma'lum bo'lsa
List<String> list = new ArrayList<>(1000); // Capacity = 1000
```

### 2. Immutable list yaratish
```java
// Java 9+ - List.of()
List<String> immutable1 = List.of("A", "B", "C");

// Java 8+ - Collections.unmodifiableList()
List<String> immutable2 = Collections.unmodifiableList(new ArrayList<>());
```

### 3. ArrayList'ni massivga o'tkazish
```java
ArrayList<String> list = new ArrayList<>();
list.add("A");
list.add("B");

// To array - 1-usul
String[] array1 = list.toArray(new String[0]);

// To array - 2-usul (size ni oldindan bilinsa)
String[] array2 = list.toArray(new String[list.size()]);
```

### 4. Sublist yaratish (view)
```java
ArrayList<Integer> numbers = new ArrayList<>();
for (int i = 0; i < 10; i++) numbers.add(i);

// Sublist - original list'ning view'ini yaratadi
List<Integer> sublist = numbers.subList(3, 7); // [3, 4, 5, 6]
sublist.clear(); // Original list'dan ham o'chadi
```

## ArrayList Method'lari

| Method | Tavsif | Time Complexity |
|--------|---------|-----------------|
| `add(E e)` | Oxiriga element qo'shish | O(1) |
| `add(int index, E e)` | Index bo'yicha qo'shish | O(n) |
| `get(int index)` | Element olish | O(1) |
| `set(int index, E e)` | Elementni o'zgartirish | O(1) |
| `remove(int index)` | Index bo'yicha o'chirish | O(n) |
| `remove(Object o)` | Qiymat bo'yicha o'chirish | O(n) |
| `contains(Object o)` | Mavjudligini tekshirish | O(n) |
| `indexOf(Object o)` | Birinchi indeksni topish | O(n) |
| `lastIndexOf(Object o)` | Oxirgi indeksni topish | O(n) |
| `size()` | Elementlar soni | O(1) |
| `isEmpty()` | Bo'shligini tekshirish | O(1) |
| `clear()` | Barchasini o'chirish | O(n) |
| `toArray()` | Massivga o'tkazish | O(n) |
| `sort(Comparator)` | Tartiblash | O(n log n) |

## Tekshiruv Savollari

1. **List interface'ning asosiy xususiyatlari nima?**
2. **ArrayList qanday ishlaydi?**
3. **ArrayList va LinkedList farqlari nima?**
4. **Nega Vector deprecated hisoblanadi?**
5. **ArrayList'da get() va remove() metodlari qanday ishlaydi?**
6. **Initial capacity nima uchun muhim?**
7. **Immutable list qanday yaratiladi?**
8. **ArrayList performance ta'sir qiluvchi omillar nima?**

---

**Keyingi mavzu:** [05 - LinkedList Data Structure](./05_LinkedList_Data_Structure.md)  
**[Mundarijaga qaytish](../README.md)**

---

**Muhim Atamalar:**
- **List Interface** - Ro'yxat interfeysi (tartiblangan kolleksiya)
- **ArrayList** - Massiv asosidagi ro'yxat
- **LinkedList** - Bog'langan ro'yxat
- **Vector** - Thread-safe massiv (eski)
- **Capacity** - Sig'im (massiv o'lchami)
- **Size** - Hajm (elementlar soni)
- **Immutable** - O'zgarmas (o'zgartirib bo'lmaydigan)
- **Random Access** - Tasodifiy kirish (har qanday elementga tez kirish)

> **Muhim:** ArrayList Java'da eng ko'p ishlatiladigan collection class'idir. Uni to'liq o'rganish va qachon ishlatish kerakligini bilish muhim.