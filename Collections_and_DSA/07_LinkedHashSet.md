# Comparator, Comparable va TreeSet

## Comparable Interface

**Comparable** interface'i uni implement qilgan har bir class ob'ektlari uchun umumiy tartibni belgilaydi. Bu tartib class'ning **natural ordering** (tabiiy tartibi) deb ataladi va class'ning `compareTo()` method'i uning natural comparison method'i hisoblanadi (JDK 1.2'dan boshlab).

### compareTo() Method'i

`compareTo()` method'i ikki ob'ektni solishtirish uchun ishlatiladi va 3 xil qiymat qaytaradi:
- **Manfiy son** - agar joriy ob'ekt parametrdan kichik bo'lsa
- **Nol** - agar ikkala ob'ekt teng bo'lsa
- **Musbat son** - agar joriy ob'ekt parametrdan katta bo'lsa

## Comparator Interface

**Comparator** - bu taqqoslash funksiyasi bo'lib, ob'ektlar to'plami uchun umumiy tartibni belgilaydi. Comparator'lar sort method'lariga (`Collections.sort()` yoki `Arrays.sort()`) o'tkazilishi mumkin, bu esa tartiblash ustida aniq nazoratni ta'minlaydi. Comparator'lar shuningdek, ba'zi data structure'lar (masalan, sorted set yoki sorted map) tartibini boshqarish yoki natural ordering'ga ega bo'lmagan ob'ektlar to'plami uchun tartib ta'minlash uchun ishlatilishi mumkin (JDK 1.2'dan boshlab).

## TreeSet Class'i

**TreeSet** class'i TreeMap asosida qurilgan NavigableSet implementatsiyasidir. Elementlar ularning natural ordering'i yoki set yaratilish vaqtida berilgan Comparator yordamida tartiblanadi (qaysi constructor ishlatilishiga qarab).

### TreeSet'ning Asosiy Xususiyatlari

1. **Noyob elementlar** - HashSet kabi faqat noyob elementlarni saqlaydi
2. **Null element yo'q** - Null element qo'shishga ruxsat bermaydi
3. **Synchronized emas** - `Collections.synchronizedSortedSet(new TreeSet(...))` yordamida synchronized qilish mumkin
4. **O'sish tartibida** - Elementlar har qanday tartibda qo'shilganda ham avtomatik ravishda o'sish tartibida joylashtiriladi
5. **TreeMap ichki qatlami** - Java TreeSet ichki qatlamda TreeMap'dan foydalanadi

### TreeSet Class'i Ierarxiyasi

```
                   Collection
                       |
                      Set
                          \
Cloneable-------|       SortedSet
                |           |             
Serializable ---|      NavigableSet
                |     /
AbstractSet     |    /
           \    |   /
            TreeSet
```

## TreeSet Amaliyotda

### JavaTreeSetTest - TreeSet Misollari

```java
package collectionsframework.set;

import java.util.Comparator;
import java.util.TreeSet;

/**
 * TreeSet class'idan foydalanish misollari
 */
public class JavaTreeSetTest {
    
    public static void main(String[] args) {
        // 1. TreeSet yaratish (natural ordering bilan)
        var treeSet = new TreeSet<Integer>();
        treeSet.add(12);
        treeSet.add(2);
        treeSet.add(1);
        treeSet.add(90);
        treeSet.add(4);
        treeSet.add(6);
        treeSet.add(-12);
        
        System.out.println("TreeSet (auto-sorted): " + treeSet);
        
        // 2. NavigableSet method'lari
        System.out.println("\nNavigableSet Methods:");
        System.out.println("floor(5) - eng katta element ≤ 5: " + treeSet.floor(5));
        System.out.println("ceiling(6) - eng kichik element ≥ 6: " + treeSet.ceiling(6));
        System.out.println("lower(4) - eng katta element < 4: " + treeSet.lower(4));
        System.out.println("higher(6) - eng kichik element > 6: " + treeSet.higher(6));
        
        // 3. SubSet method'lari
        System.out.println("\nSubSet Methods:");
        System.out.println("subSet(6, 12) - 6 ≤ element < 12: " + treeSet.subSet(6, 12));
        System.out.println("subSet(6, true, 12, true) - 6 ≤ element ≤ 12: " + 
                          treeSet.subSet(6, true, 12, true));
        System.out.println("tailSet(6) - element ≥ 6: " + treeSet.tailSet(6));
        System.out.println("tailSet(6, false) - element > 6: " + treeSet.tailSet(6, false));
        System.out.println("headSet(6) - element < 6: " + treeSet.headSet(6));
        
        // 4. Comparator bilan TreeSet yaratish
        var treeSet2 = new TreeSet<Employee>(Comparator.comparing(Employee::getFullName));
        treeSet2.add(new Employee("Akbar Akbarov"));
        treeSet2.add(new Employee("Zafar Zafarov"));
        treeSet2.add(new Employee("Bahrom Bahromov"));
        
        System.out.println("\nEmployees sorted by name: " + treeSet2);
        
        // 5. Descending tartib
        System.out.println("\nDescending order:");
        System.out.println("Descending Set: " + treeSet.descendingSet());
        
        // 6. PollFirst va PollLast
        System.out.println("\nFirst and Last elements:");
        System.out.println("First element: " + treeSet.first());
        System.out.println("Last element: " + treeSet.last());
        
        Integer removedFirst = treeSet.pollFirst();  // Birinchi elementni olib tashlaydi
        Integer removedLast = treeSet.pollLast();    // Oxirgi elementni olib tashlaydi
        System.out.println("Removed first: " + removedFirst);
        System.out.println("Removed last: " + removedLast);
        System.out.println("TreeSet after poll: " + treeSet);
    }
}

/**
 * Employee class - TreeSet uchun misol
 */
class Employee {
    private String fullName;
    
    public Employee(String fullName) {
        this.fullName = fullName;
    }
    
    public String getFullName() {
        return fullName;
    }
    
    @Override
    public String toString() {
        return fullName;
    }
}
```

## Comparable Amaliyotda

### Student Class'i (Comparable bilan)

```java
package collectionsframework.comparable;

/**
 * Student class - Comparable interface'ini implement qilish misoli
 */
public class Student implements Comparable<Student> {
    public String firstName;
    public String lastName;
    public int age;
    
    public Student(String firstName, String lastName, int age) {
        this.firstName = firstName;
        this.lastName = lastName;
        this.age = age;
    }
    
    /**
     * Natural ordering: Avval lastName, keyin firstName, keyin age bo'yicha
     */
    @Override
    public int compareTo(Student student) {
        // 1. Avval lastName bo'yicha solishtirish
        int lastNameCompare = this.lastName.compareTo(student.lastName);
        if (lastNameCompare != 0) {
            return lastNameCompare;
        }
        
        // 2. Agar lastName bir xil bo'lsa, firstName bo'yicha
        int firstNameCompare = this.firstName.compareTo(student.firstName);
        if (firstNameCompare != 0) {
            return firstNameCompare;
        }
        
        // 3. Agar firstName ham bir xil bo'lsa, age bo'yicha
        return Integer.compare(this.age, student.age);
    }
    
    @Override
    public String toString() {
        return String.format("%s %s (%d yosh)", lastName, firstName, age);
    }
}
```

### MockData Class'i

```java
package collectionsframework.comparable;

import com.github.javafaker.Faker;
import java.util.ArrayList;
import java.util.Random;

/**
 * Test ma'lumotlari yaratish uchun utility class
 */
public class MockData {
    
    /**
     * Tasodifiy student'lar ro'yxatini yaratish
     */
    public static ArrayList<Student> generateStudents() {
        var students = new ArrayList<Student>();
        var faker = new Faker();
        var random = new Random();
        
        // 20 ta tasodifiy student
        for (int i = 0; i < 20; i++) {
            String firstName = faker.name().firstName();
            String lastName = faker.name().lastName();
            int age = random.nextInt(18, 22);  // 18-21 yosh oralig'ida
            students.add(new Student(firstName, lastName, age));
        }
        
        // Qo'shimcha student'lar (test uchun)
        students.add(new Student("Akbar", "Akbarov", 20));
        students.add(new Student("Asliddin", "Akbarov", 19));
        students.add(new Student("Asliddin", "Akbarov", 18));
        
        return students;
    }
}
```

### StudentSort Class'i

```java
package collectionsframework.comparable;

import java.util.Arrays;
import java.util.Collections;
import java.util.Comparator;
import java.util.List;

/**
 * Student'lar sort qilishning turli usullari
 */
public class StudentSort {
    
    public static void main(String[] args) {
        // Test ma'lumotlarni yaratish
        List<Student> students = MockData.generateStudents();
        
        System.out.println("Tartiblanmagan student'lar:");
        System.out.println("=".repeat(50));
        students.forEach(System.out::println);
        
        System.out.println("\n" + "=".repeat(50));
        System.out.println("Tartiblangan student'lar (Comparable - natural ordering):");
        System.out.println("=".repeat(50));
        
        // 1. Comparable yordamida tartiblash (natural ordering)
        Collections.sort(students);
        students.forEach(System.out::println);
        
        // 2. Comparator yordamida turli tartiblash usullari
        System.out.println("\n" + "=".repeat(50));
        System.out.println("Tartiblangan student'lar (Comparator - yosh bo'yicha):");
        System.out.println("=".repeat(50));
        
        // Yosh bo'yicha tartiblash
        Collections.sort(students, Comparator.comparingInt(s -> s.age));
        students.forEach(System.out::println);
        
        System.out.println("\n" + "=".repeat(50));
        System.out.println("Tartiblangan student'lar (Comparator - firstName bo'yicha):");
        System.out.println("=".repeat(50));
        
        // firstName bo'yicha tartiblash
        Collections.sort(students, Comparator.comparing(s -> s.firstName));
        students.forEach(System.out::println);
        
        // 3. Ko'p qavatli tartiblash
        System.out.println("\n" + "=".repeat(50));
        System.out.println("Tartiblangan student'lar (Comparator - lastName, keyin firstName):");
        System.out.println("=".repeat(50));
        
        Comparator<Student> multiLevelComparator = Comparator
            .comparing((Student s) -> s.lastName)
            .thenComparing(s -> s.firstName)
            .thenComparingInt(s -> s.age);
        
        Collections.sort(students, multiLevelComparator);
        students.forEach(System.out::println);
        
        // 4. Teskari tartiblash
        System.out.println("\n" + "=".repeat(50));
        System.out.println("Tartiblangan student'lar (Teskari tartib - yosh bo'yicha):");
        System.out.println("=".repeat(50));
        
        Comparator<Student> reverseAgeComparator = Comparator
            .comparingInt((Student s) -> s.age)
            .reversed();
        
        Collections.sort(students, reverseAgeComparator);
        students.forEach(System.out::println);
    }
}
```

## Comparator va Comparable Farqlari

### Comparable vs Comparator

| Xususiyat | Comparable | Comparator |
|-----------|------------|------------|
| **Package** | `java.lang` | `java.util` |
| **Method** | `compareTo()` | `compare()` |
| **Tartiblash** | Natural ordering (tabiiy tartib) | Custom ordering (maxsus tartib) |
| **Class o'zgartirish** | Class'ni o'zgartirish kerak | Class'ni o'zgartirish shart emas |
| **Bitta tartib** | Bir class uchun bitta natural tartib | Bir nechta turli tartiblash usullari |
| **Collections.sort()** | `Collections.sort(list)` | `Collections.sort(list, comparator)` |
| **TreeSet/TreeMap** | Constructor'siz | Constructor bilan berilishi mumkin |

### Comparable Misoli (Class ichida)

```java
class Product implements Comparable<Product> {
    private String name;
    private double price;
    
    public Product(String name, double price) {
        this.name = name;
        this.price = price;
    }
    
    @Override
    public int compareTo(Product other) {
        // Avval price bo'yicha, keyin name bo'yicha
        int priceCompare = Double.compare(this.price, other.price);
        if (priceCompare != 0) {
            return priceCompare;
        }
        return this.name.compareTo(other.name);
    }
    
    // Getters va toString...
}
```

### Comparator Misoli (Alohida class)

```java
// 1. Alohida Comparator class
class PriceComparator implements Comparator<Product> {
    @Override
    public int compare(Product p1, Product p2) {
        return Double.compare(p1.getPrice(), p2.getPrice());
    }
}

// 2. NameComparator
class NameComparator implements Comparator<Product> {
    @Override
    public int compare(Product p1, Product p2) {
        return p1.getName().compareTo(p2.getName());
    }
}

// 3. Anonymous inner class bilan
Comparator<Product> priceComparator = new Comparator<Product>() {
    @Override
    public int compare(Product p1, Product p2) {
        return Double.compare(p1.getPrice(), p2.getPrice());
    }
};

// 4. Lambda expression bilan (Java 8+)
Comparator<Product> lambdaComparator = (p1, p2) -> 
    Double.compare(p1.getPrice(), p2.getPrice());

// 5. Method reference bilan (Java 8+)
Comparator<Product> methodRefComparator = 
    Comparator.comparing(Product::getPrice);
```

## TreeSet Performance va Xususiyatlari

```java
package collectionsframework.set;

import java.util.HashSet;
import java.util.TreeSet;
import java.util.Set;

/**
 * TreeSet vs HashSet performance taqqoslash
 */
public class TreeSetPerformance {
    
    public static void main(String[] args) {
        final int elementCount = 100000;
        
        // HashSet performance
        Set<Integer> hashSet = new HashSet<>();
        long startTime = System.currentTimeMillis();
        
        for (int i = 0; i < elementCount; i++) {
            hashSet.add((int) (Math.random() * elementCount));
        }
        
        long endTime = System.currentTimeMillis();
        System.out.println("HashSet add time: " + (endTime - startTime) + "ms");
        System.out.println("HashSet size: " + hashSet.size());
        
        // TreeSet performance
        Set<Integer> treeSet = new TreeSet<>();
        startTime = System.currentTimeMillis();
        
        for (int i = 0; i < elementCount; i++) {
            treeSet.add((int) (Math.random() * elementCount));
        }
        
        endTime = System.currentTimeMillis();
        System.out.println("TreeSet add time: " + (endTime - startTime) + "ms");
        System.out.println("TreeSet size: " + treeSet.size());
        
        // Contains operation performance
        startTime = System.currentTimeMillis();
        hashSet.contains(50000);
        endTime = System.currentTimeMillis();
        System.out.println("\nHashSet contains time: " + (endTime - startTime) + "ms");
        
        startTime = System.currentTimeMillis();
        treeSet.contains(50000);
        endTime = System.currentTimeMillis();
        System.out.println("TreeSet contains time: " + (endTime - startTime) + "ms");
        
        // First/last operations
        startTime = System.currentTimeMillis();
        ((TreeSet<Integer>) treeSet).first();
        ((TreeSet<Integer>) treeSet).last();
        endTime = System.currentTimeMillis();
        System.out.println("TreeSet first/last time: " + (endTime - startTime) + "ms");
    }
}
```

## TreeSet Method'lari

### TreeSet'ning NavigableSet Method'lari

```java
package collectionsframework.set;

import java.util.TreeSet;
import java.util.SortedSet;

/**
 * TreeSet navigatsiya method'lari
 */
public class TreeSetNavigation {
    
    public static void main(String[] args) {
        TreeSet<Integer> numbers = new TreeSet<>();
        
        // Elementlar qo'shish
        numbers.add(10);
        numbers.add(20);
        numbers.add(30);
        numbers.add(40);
        numbers.add(50);
        numbers.add(60);
        numbers.add(70);
        
        System.out.println("Original TreeSet: " + numbers);
        
        // 1. Ceiling, floor, higher, lower
        System.out.println("\nCeiling, Floor, Higher, Lower:");
        System.out.println("ceiling(25) - smallest ≥ 25: " + numbers.ceiling(25));
        System.out.println("floor(25) - largest ≤ 25: " + numbers.floor(25));
        System.out.println("higher(30) - smallest > 30: " + numbers.higher(30));
        System.out.println("lower(30) - largest < 30: " + numbers.lower(30));
        
        // 2. HeadSet, TailSet, SubSet
        System.out.println("\nHeadSet, TailSet, SubSet:");
        SortedSet<Integer> headSet = numbers.headSet(40);  // < 40
        System.out.println("headSet(40): " + headSet);
        
        SortedSet<Integer> tailSet = numbers.tailSet(40);  // ≥ 40
        System.out.println("tailSet(40): " + tailSet);
        
        SortedSet<Integer> subSet = numbers.subSet(20, 60);  // 20 ≤ element < 60
        System.out.println("subSet(20, 60): " + subSet);
        
        // 3. NavigableSet variantlari
        System.out.println("\nNavigableSet variants:");
        System.out.println("headSet(40, true): " + numbers.headSet(40, true));  // ≤ 40
        System.out.println("tailSet(40, false): " + numbers.tailSet(40, false));  // > 40
        System.out.println("subSet(20, false, 60, true): " + 
                          numbers.subSet(20, false, 60, true));  // 20 < element ≤ 60
        
        // 4. Descending operations
        System.out.println("\nDescending operations:");
        System.out.println("descendingSet(): " + numbers.descendingSet());
        System.out.println("descendingIterator(): ");
        numbers.descendingIterator().forEachRemaining(n -> System.out.print(n + " "));
        
        // 5. Poll operations
        System.out.println("\n\nPoll operations:");
        System.out.println("Original: " + numbers);
        System.out.println("pollFirst(): " + numbers.pollFirst());
        System.out.println("After pollFirst: " + numbers);
        System.out.println("pollLast(): " + numbers.pollLast());
        System.out.println("After pollLast: " + numbers);
    }
}
```

## Amaliy Maslahatlar

1. **Qachon TreeSet ishlatish kerak?**
    - Elementlarni tartiblangan holatda saqlash kerak bo'lsa
    - Range query (oralik so'rovlari) kerak bo'lsa
    - Natural ordering yoki custom ordering kerak bo'lsa

2. **Qachon Comparable vs Comparator?**
    - **Comparable** - class'ning default/natural tartibi bo'lsa
    - **Comparator** - bir nechta turli tartiblash usullari kerak bo'lsa
    - **Comparator** - class'ni o'zgartirish mumkin bo'lmasa

3. **Performance diqqatga olinishi kerak:**
    - **TreeSet**: O(log n) add/remove/contains operations
    - **HashSet**: O(1) average case, O(n) worst case
    - Agar tartiblash kerak bo'lmasa, HashSet tezroq

4. **Null qiymat bilan ishlash:**
    - TreeSet null qiymat qabul qilmaydi
    - NullPointerException beradi

5. **Thread safety:**
    - TreeSet synchronized emas
    - `Collections.synchronizedSortedSet()` yordamida thread-safe qilish mumkin

---

**Keyingi mavzu:** [08_Queue_Data_Structure.md](./08_Queue_Data_Structure.md)

**Oldingi mavzu:** [06_Set_Interface.md](./06_Set_Interface.md)

**Asosiy sahifaga qaytish:** [README.md](../README.md)

---

**Muhim Atamalar:**
- **Comparable** - Tabiiy tartib interface'i
- **Comparator** - Maxsus tartib interface'i
- **TreeSet** - Tartiblangan Set implementatsiyasi
- **Natural Ordering** - Class'ning default tartibi
- **compareTo()** - Comparable interface'ining asosiy method'i
- **compare()** - Comparator interface'ining asosiy method'i
- **NavigableSet** - Navigatsiya qilish imkoniyatiga ega Set
- **SortedSet** - Tartiblangan Set

> **Bolalar, o'rganishda davom etamiz!** 🚀