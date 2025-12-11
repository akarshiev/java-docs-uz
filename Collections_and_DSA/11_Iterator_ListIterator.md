# Iterator vs ListIterator

## Iterator

**Java Iterator** - bu Java ob'ekt komponentlarining to'plamini birma-bir iteratsiya qilish uchun qo'llaniladigan interface (JDK 1.2'dan boshlab).

Iterator collection elementlarini ketma-ket o'tish uchun standart interfeysni taqdim etadi. U collection elementlarini o'qish, olib tashlash, lekin qo'shish yoki o'zgartirish imkoniyatini bermaydi.

### Iterator Method'lari

1. **hasNext()** - Keyingi element mavjudligini tekshiradi
2. **next()** - Keyingi elementni qaytaradi
3. **remove()** - Oxirgi next() chaqiruvida qaytarilgan elementni o'chiradi
4. **forEachRemaining()** (Java 8+) - Qolgan barcha elementlar uchun harakatni bajaradi

## ListIterator

**ListIterator** - Iterator kabi, ListIterator ham Java Iterator'idir, u List implementatsiya qilingan ob'ektlardan elementlarni birma-bir iteratsiya qilish uchun ishlatiladi (JDK 1.2'dan boshlab).

- Iterator interfeysini kengaytiradi
- Faqat List implementatsiya qilingan class'lar uchun foydalidir
- Iterator'dan farqli o'laroq, u to'rtta operatsiyani qo'llab-quvvatlaydi: CRUD (Create, Read, Update, Delete)
- Iterator'dan farqli o'laroq, u ham Oldinga yo'nalish, ham Orqaga yo'nalish iteratsiyalarini qo'llab-quvvatlaydi

### ListIterator Method'lari

1. **Forward direction:** hasNext(), next(), nextIndex()
2. **Backward direction:** hasPrevious(), previous(), previousIndex()
3. **Modification:** add(), set(), remove()

## Fail-Fast Iterator'lar

Java'dagi Iterator'lar Collection ob'ektlari ustida iteratsiya qilish uchun ishlatiladi. **Fail-Fast iterator'lar** agar collection'da strukturaviy o'zgarish bo'lsa, darhol ConcurrentModificationException chiqaradi. Strukturaviy o'zgarish degani, thread collection ustida iteratsiya qilayotganda collection'dan element qo'shish yoki olib tashlashdir. ArrayList, HashMap class'laridagi Iterator'lar fail-fast Iterator'larga misoldir.

## Fail-Safe Iterator'lar

**Fail-Safe iterator'lar** agar collection iteratsiya qilinayotganda strukturaviy o'zgartirilsa, hech qanday exception chiqarmaydi. Buning sababi, ular original collection emas, balki collection'ning clone'ida ishlaydi va shuning uchun ular fail-safe iterator'lar deb ataladi. CopyOnWriteArrayList, ConcurrentHashMap class'laridagi Iterator'lar fail-safe Iterator'larga misoldir.

# Iterator va ListIterator Amaliyotda

## Custom Counter Iterator

### Counter Class'i (Iterable implementatsiyasi)

```java
package collectionsframework.iterators;

import java.util.Iterator;

/**
 * Counter - Iterable interface'ini implement qilgan counter class'i
 * Berilgan oraliqdagi sonlarni qadam bo'yicha iteratsiya qilish imkonini beradi
 */
public class Counter implements Iterable<Integer> {
    private int min;
    private int max;
    private int step;
    
    /**
     * Constructor - 0 dan max gacha, qadam 1
     * @param max maksimal qiymat
     */
    public Counter(int max) {
        this(0, max, 1);
    }
    
    /**
     * Constructor - 0 dan max gacha, berilgan qadam
     * @param max maksimal qiymat
     * @param step qadam
     */
    public Counter(int max, int step) {
        this(0, max, step);
    }
    
    /**
     * To'liq constructor
     * @param min minimal qiymat
     * @param max maksimal qiymat
     * @param step qadam
     */
    public Counter(int min, int max, int step) {
        if (min >= max) {
            throw new IllegalArgumentException("Min must be less than max");
        }
        if (step <= 0) {
            throw new IllegalArgumentException("Step must be positive");
        }
        
        this.min = min;
        this.max = max;
        this.step = step;
    }
    
    /**
     * Iterator qaytarish - for-each loop uchun
     * @return CounterIterator
     */
    @Override
    public Iterator<Integer> iterator() {
        return new CounterIterator(min, max, step);
    }
    
    /**
     * Counter ma'lumotlarini string ko'rinishida qaytarish
     * @return counter ma'lumotlari
     */
    @Override
    public String toString() {
        return String.format("Counter[min=%d, max=%d, step=%d]", min, max, step);
    }
}
```

### CounterIterator Class'i

```java
package collectionsframework.iterators;

import java.util.Iterator;

/**
 * CounterIterator - Counter uchun iterator implementatsiyasi
 */
public class CounterIterator implements Iterator<Integer> {
    private int min;
    private int max;
    private int step;
    private int current;
    
    /**
     * Constructor - 0 dan max gacha, qadam 1
     * @param max maksimal qiymat
     */
    public CounterIterator(int max) {
        this(0, max, 1);
    }
    
    /**
     * Constructor - 0 dan max gacha, berilgan qadam
     * @param max maksimal qiymat
     * @param step qadam
     */
    public CounterIterator(int max, int step) {
        this(0, max, step);
    }
    
    /**
     * To'liq constructor
     * @param min minimal qiymat
     * @param max maksimal qiymat
     * @param step qadam
     */
    public CounterIterator(int min, int max, int step) {
        this.min = min;
        this.max = max;
        this.step = step;
        this.current = min;
    }
    
    /**
     * Keyingi element mavjudligini tekshirish
     * @return true - agar keyingi element mavjud bo'lsa
     */
    @Override
    public boolean hasNext() {
        return current < max;
    }
    
    /**
     * Keyingi elementni qaytarish
     * @return keyingi son
     * @throws java.util.NoSuchElementException agar element qolmasa
     */
    @Override
    public Integer next() {
        if (!hasNext()) {
            throw new java.util.NoSuchElementException("No more elements");
        }
        
        int value = current;
        current = current + step;
        return value;
    }
    
    /**
     * remove() method'ini override qilish - bu iterator uchun qo'llanilmaydi
     * @throws UnsupportedOperationException har doim
     */
    @Override
    public void remove() {
        throw new UnsupportedOperationException("Remove operation is not supported by CounterIterator");
    }
}
```

### CounterTest

```java
package collectionsframework.iterators;

/**
 * Counter test qilish
 */
public class CounterTest {
    
    public static void main(String[] args) {
        System.out.println("========== CUSTOM COUNTER ITERATOR ==========");
        
        // 1. Oddiy counter (0 dan 10 gacha, qadam 1)
        System.out.println("1. Simple counter (0 to 10, step 1):");
        Counter counter1 = new Counter(10);
        for (Integer num : counter1) {
            System.out.print(num + " ");
        }
        System.out.println("\n");
        
        // 2. Qadam bilan counter
        System.out.println("2. Counter with step 3 (0 to 20):");
        Counter counter2 = new Counter(20, 3);
        for (Integer num : counter2) {
            System.out.print(num + " ");
        }
        System.out.println("\n");
        
        // 3. Min va max bilan counter
        System.out.println("3. Counter from 5 to 50 with step 5:");
        Counter counter3 = new Counter(5, 50, 5);
        System.out.println(counter3);
        
        for (Integer num : counter3) {
            System.out.print(num + " ");
        }
        System.out.println("\n");
        
        // 4. Iterator to'g'ridan-to'g'ri ishlatish
        System.out.println("4. Using iterator directly:");
        Counter counter4 = new Counter(15, 2);
        var iterator = counter4.iterator();
        
        while (iterator.hasNext()) {
            System.out.print(iterator.next() + " ");
        }
        System.out.println("\n");
        
        // 5. forEachRemaining() method'i (Java 8+)
        System.out.println("5. Using forEachRemaining():");
        Counter counter5 = new Counter(8);
        counter5.iterator().forEachRemaining(num -> 
            System.out.print(num + " ")
        );
        System.out.println("\n");
        
        // 6. Iterator xususiyatlari
        System.out.println("6. Iterator characteristics:");
        System.out.println("- Can traverse elements only in forward direction");
        System.out.println("- Can only remove elements (not add or modify)");
        System.out.println("- Must be implemented for custom collections");
        System.out.println("- Used by enhanced for-loop (for-each)");
    }
}
```

## Java Iterator Class Test

```java
package collectionsframework.iterators;

import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;

/**
 * Java standart Iterator sinfidan foydalanish
 */
public class IteratorClassTest {
    
    public static void main(String[] args) {
        System.out.println("========== JAVA STANDARD ITERATOR ==========");
        
        // 1. List yaratish
        List<String> languages = new ArrayList<>();
        languages.add("Java");
        languages.add("Scala");
        languages.add("Kotlin");
        languages.add("Go");
        languages.add("Python");
        languages.add("Groovy");
        
        System.out.println("Original list: " + languages);
        
        // 2. Iterator olish
        Iterator<String> iterator = languages.iterator();
        
        // 3. while loop bilan iteratsiya
        System.out.println("\nIterating with while loop:");
        while (iterator.hasNext()) {
            String language = iterator.next();
            System.out.println("Language: " + language);
            
            // Ma'lum shartga ko'ra elementni o'chirish
            if (language.equals("Go") || language.equals("Python")) {
                iterator.remove();  // Iterator orqali xavfsiz o'chirish
                System.out.println("  Removed: " + language);
            }
        }
        
        System.out.println("\nList after removal: " + languages);
        
        // 4. Yangi iterator yaratish (eski iterator ishlatilmaydi)
        System.out.println("\nCreating new iterator for forEachRemaining:");
        Iterator<String> newIterator = languages.iterator();
        
        // 5. forEachRemaining() method'i (Java 8+)
        System.out.println("Remaining languages:");
        newIterator.forEachRemaining(System.out::println);
        
        // 6. For-each loop bilan iteratsiya (iterator'ni yashirincha ishlatadi)
        System.out.println("\nIterating with enhanced for-loop:");
        for (String language : languages) {
            System.out.println("Language: " + language);
        }
        
        // 7. Iterator'ning cheklovlari
        System.out.println("\n7. Iterator limitations:");
        System.out.println("- Can only move forward");
        System.out.println("- Cannot add elements");
        System.out.println("- Cannot modify elements (except remove)");
        System.out.println("- Cannot get current index");
        
        // 8. ConcurrentModificationException namoyishi
        System.out.println("\n8. Demonstrating ConcurrentModificationException:");
        List<String> demoList = new ArrayList<>(List.of("A", "B", "C", "D"));
        Iterator<String> demoIterator = demoList.iterator();
        
        try {
            while (demoIterator.hasNext()) {
                String element = demoIterator.next();
                System.out.println("Processing: " + element);
                
                if (element.equals("B")) {
                    // Iterator orqali emas, to'g'ridan-to'g'ri list'dan o'chirish
                    demoList.remove(element);  // ConcurrentModificationException!
                }
            }
        } catch (Exception e) {
            System.out.println("Exception caught: " + e.getClass().getSimpleName());
            System.out.println("Message: " + e.getMessage());
        }
        
        // 9. To'g'ri usul - faqat iterator orqali o'chirish
        System.out.println("\n9. Correct way - remove only through iterator:");
        demoList = new ArrayList<>(List.of("A", "B", "C", "D"));
        demoIterator = demoList.iterator();
        
        while (demoIterator.hasNext()) {
            String element = demoIterator.next();
            if (element.equals("B")) {
                demoIterator.remove();  // To'g'ri usul
                System.out.println("Safely removed: " + element);
            }
        }
        System.out.println("List after safe removal: " + demoList);
    }
}
```

## Java ListIterator Class Test

```java
package collectionsframework.iterators;

import java.util.ArrayList;
import java.util.List;
import java.util.ListIterator;

/**
 * ListIterator - Iterator'ning kengaytmasi, faqat List'lar uchun
 */
public class ListIteratorClassTest {
    
    public static void main(String[] args) {
        System.out.println("========== JAVA LISTITERATOR ==========");
        
        // 1. List yaratish
        List<String> languages = new ArrayList<>();
        languages.add("Java");
        languages.add("Scala");
        languages.add("Kotlin");
        languages.add("Go");
        languages.add("Python");
        languages.add("Groovy");
        
        System.out.println("Original list: " + languages);
        
        // 2. ListIterator olish (boshidan)
        System.out.println("\n1. Forward iteration from beginning:");
        ListIterator<String> listIterator = languages.listIterator();
        
        while (listIterator.hasNext()) {
            int index = listIterator.nextIndex();
            String language = listIterator.next();
            System.out.printf("Index %d: %s%n", index, language);
        }
        
        // 3. Orqaga iteratsiya
        System.out.println("\n2. Backward iteration from end:");
        while (listIterator.hasPrevious()) {
            int index = listIterator.previousIndex();
            String language = listIterator.previous();
            System.out.printf("Index %d: %s%n", index, language);
        }
        
        // 4. O'rtadan boshlash
        System.out.println("\n3. Starting from specific position (index 2):");
        listIterator = languages.listIterator(2);  // 2-indexdan boshlab
        
        System.out.println("Next element: " + listIterator.next());
        System.out.println("Previous element: " + listIterator.previous());
        
        // 5. Element qo'shish
        System.out.println("\n4. Adding elements with ListIterator:");
        listIterator = languages.listIterator();
        
        while (listIterator.hasNext()) {
            String language = listIterator.next();
            if (language.equals("Kotlin")) {
                listIterator.add("TypeScript");  // "Kotlin" dan oldin qo'shiladi
                System.out.println("Added TypeScript before Kotlin");
            }
        }
        System.out.println("List after adding: " + languages);
        
        // 6. Element o'zgartirish (set)
        System.out.println("\n5. Modifying elements with set():");
        listIterator = languages.listIterator();
        
        while (listIterator.hasNext()) {
            String language = listIterator.next();
            if (language.equals("Go")) {
                listIterator.set("GoLang");  // "Go" ni "GoLang" ga o'zgartiradi
                System.out.println("Changed Go to GoLang");
            }
        }
        System.out.println("List after modification: " + languages);
        
        // 7. Element o'chirish
        System.out.println("\n6. Removing elements:");
        listIterator = languages.listIterator();
        
        while (listIterator.hasNext()) {
            String language = listIterator.next();
            if (language.equals("Python")) {
                listIterator.remove();  // "Python" ni o'chiradi
                System.out.println("Removed Python");
                break;  // Remove'dan keyin next() yoki previous() chaqirish kerak
            }
        }
        System.out.println("List after removal: " + languages);
        
        // 8. CRUD operatsiyalari
        System.out.println("\n7. ListIterator CRUD operations:");
        System.out.println("C (Create) - add() method");
        System.out.println("R (Read) - next(), previous(), nextIndex(), previousIndex()");
        System.out.println("U (Update) - set() method");
        System.out.println("D (Delete) - remove() method");
        
        // 9. ListIterator vs Iterator farqlari
        System.out.println("\n8. ListIterator vs Iterator differences:");
        System.out.println("| Feature           | Iterator | ListIterator |");
        System.out.println("|-------------------|----------|--------------|");
        System.out.println("| Forward traverse  |    ✓     |      ✓       |");
        System.out.println("| Backward traverse |    ✗     |      ✓       |");
        System.out.println("| Add elements      |    ✗     |      ✓       |");
        System.out.println("| Modify elements   |    ✗     |      ✓       |");
        System.out.println("| Get index         |    ✗     |      ✓       |");
        System.out.println("| Works with        | All Coll |  Only List   |");
        
        // 10. Amaliy misol - palindrome tekshirish
        System.out.println("\n9. Practical example - checking palindrome:");
        List<Character> word = new ArrayList<>();
        word.add('r');
        word.add('a');
        word.add('d');
        word.add('a');
        word.add('r');
        
        System.out.println("Word: " + word);
        System.out.println("Is palindrome? " + isPalindrome(word));
    }
    
    /**
     * ListIterator yordamida palindrome tekshirish
     * @param list tekshiriladigan list
     * @return true - agar palindrome bo'lsa
     */
    private static boolean isPalindrome(List<Character> list) {
        ListIterator<Character> forward = list.listIterator();
        ListIterator<Character> backward = list.listIterator(list.size());
        
        while (forward.hasNext() && backward.hasPrevious()) {
            if (forward.nextIndex() >= backward.previousIndex()) {
                break;  // O'rtaga yetib keldik
            }
            
            char front = forward.next();
            char back = backward.previous();
            
            if (front != back) {
                return false;
            }
        }
        return true;
    }
}
```

## Fail-Fast Iterator Test

```java
package collectionsframework.iterators;

import java.util.ArrayList;
import java.util.ConcurrentModificationException;
import java.util.Iterator;
import java.util.List;

/**
 * Fail-Fast iterator - strukturaviy o'zgarishda ConcurrentModificationException
 */
public class FailFastIterator {
    
    public static void main(String[] args) {
        System.out.println("========== FAIL-FAST ITERATOR ==========");
        
        // 1. List yaratish
        List<String> languages = new ArrayList<>();
        languages.add("Java");
        languages.add("Scala");
        languages.add("Kotlin");
        languages.add("Go");
        languages.add("Python");
        languages.add("Groovy");
        
        System.out.println("Original list: " + languages);
        
        // 2. Fail-fast iterator yaratish
        Iterator<String> iterator = languages.iterator();
        
        // 3. Concurrent modification - iterator orqali emas
        System.out.println("\nTrying to modify list while iterating:");
        try {
            while (iterator.hasNext()) {
                String language = iterator.next();
                System.out.println("Processing: " + language);
                
                if (language.equals("Go")) {
                    // Noto'g'ri: iterator orqali emas, to'g'ridan-to'g'ri list'dan o'chirish
                    languages.remove(language);  // ConcurrentModificationException!
                    System.out.println("  Trying to remove directly from list...");
                }
            }
        } catch (ConcurrentModificationException e) {
            System.out.println("Exception caught: " + e.getClass().getSimpleName());
            System.out.println("Message: " + e.getMessage());
        }
        
        // 4. To'g'ri usul - iterator orqali o'chirish
        System.out.println("\nCorrect way - remove through iterator:");
        iterator = languages.iterator();  // Yangi iterator
        
        while (iterator.hasNext()) {
            String language = iterator.next();
            if (language.equals("Python")) {
                iterator.remove();  // To'g'ri usul
                System.out.println("Safely removed: " + language);
            }
        }
        System.out.println("List after safe removal: " + languages);
        
        // 5. For-each loop ham fail-fast
        System.out.println("\nEnhanced for-loop is also fail-fast:");
        languages = new ArrayList<>(List.of("A", "B", "C", "D"));
        
        try {
            for (String element : languages) {
                System.out.println("Processing: " + element);
                if (element.equals("B")) {
                    languages.remove(element);  // ConcurrentModificationException!
                }
            }
        } catch (ConcurrentModificationException e) {
            System.out.println("Enhanced for-loop also threw: " + e.getClass().getSimpleName());
        }
        
        // 6. Fail-fast collection'lar ro'yxati
        System.out.println("\n6. Common fail-fast collections in Java:");
        System.out.println("- ArrayList");
        System.out.println("- HashMap");
        System.out.println("- HashSet");
        System.out.println("- Vector");
        System.out.println("- LinkedList (except ListIterator add/set)");
        
        // 7. Nima uchun fail-fast?
        System.out.println("\n7. Why fail-fast?");
        System.out.println("- Prevents unpredictable behavior");
        System.out.println("- Makes bugs easier to detect");
        System.out.println("- Thread-safety issues in single thread");
        System.out.println("- Modification count (modCount) tracks changes");
    }
}
```

## Fail-Safe Iterator Test

```java
package collectionsframework.iterators;

import java.util.Iterator;
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.CopyOnWriteArrayList;

/**
 * Fail-Safe iterator - strukturaviy o'zgarishda exception chiqarmaydi
 */
public class FailSafeIterator {
    
    public static void main(String[] args) {
        System.out.println("========== FAIL-SAFE ITERATOR ==========");
        
        // 1. CopyOnWriteArrayList yaratish (fail-safe)
        System.out.println("1. CopyOnWriteArrayList (fail-safe list):");
        CopyOnWriteArrayList<String> languages = new CopyOnWriteArrayList<>();
        languages.add("Java");
        languages.add("Scala");
        languages.add("Kotlin");
        languages.add("Go");
        languages.add("Python");
        languages.add("Groovy");
        
        System.out.println("Original list: " + languages);
        
        // 2. Fail-safe iterator yaratish
        Iterator<String> iterator = languages.iterator();
        
        // 3. Iterator davomida list'ni o'zgartirish
        System.out.println("\nModifying list while iterating (fail-safe):");
        while (iterator.hasNext()) {
            String language = iterator.next();
            System.out.println("Processing: " + language);
            
            if (language.equals("Go")) {
                // Iterator orqali emas, to'g'ridan-to'g'ri list'dan o'chirish
                languages.remove(language);  // No ConcurrentModificationException!
                System.out.println("  Removed 'Go' from list");
            }
            
            if (language.equals("Python")) {
                // Yangi element qo'shish
                languages.add("TypeScript");  // No ConcurrentModificationException!
                System.out.println("  Added 'TypeScript' to list");
            }
        }
        
        System.out.println("\nList after modifications: " + languages);
        System.out.println("Notice: Iterator didn't see the new 'TypeScript' element");
        
        // 4. Nima uchun fail-safe?
        System.out.println("\n2. Why fail-safe works:");
        System.out.println("- Iterator works on a snapshot/copy of the collection");
        System.out.println("- Original collection can be modified independently");
        System.out.println("- New iterator will see all modifications");
        
        // 5. Yangi iterator yaratish
        System.out.println("\n3. Creating new iterator after modifications:");
        Iterator<String> newIterator = languages.iterator();
        System.out.println("Elements seen by new iterator:");
        newIterator.forEachRemaining(System.out::println);
        
        // 6. ConcurrentHashMap (fail-safe map)
        System.out.println("\n4. ConcurrentHashMap (fail-safe map):");
        ConcurrentHashMap<Integer, String> concurrentMap = new ConcurrentHashMap<>();
        concurrentMap.put(1, "Java");
        concurrentMap.put(2, "Python");
        concurrentMap.put(3, "C++");
        
        Iterator<Integer> mapIterator = concurrentMap.keySet().iterator();
        
        System.out.println("Original map: " + concurrentMap);
        
        while (mapIterator.hasNext()) {
            Integer key = mapIterator.next();
            System.out.println("Processing key: " + key);
            
            if (key == 2) {
                concurrentMap.put(4, "JavaScript");  // No exception
                System.out.println("  Added new key-value pair");
            }
        }
        
        System.out.println("Map after modifications: " + concurrentMap);
        
        // 7. Fail-safe collection'lar ro'yxati
        System.out.println("\n5. Common fail-safe collections in Java:");
        System.out.println("- CopyOnWriteArrayList");
        System.out.println("- CopyOnWriteArraySet");
        System.out.println("- ConcurrentHashMap");
        System.out.println("- ConcurrentSkipListMap");
        System.out.println("- ConcurrentSkipListSet");
        
        // 8. Fail-fast vs Fail-safe taqqoslash
        System.out.println("\n6. Fail-fast vs Fail-safe comparison:");
        System.out.println("| Aspect              | Fail-Fast                | Fail-Safe                 |");
        System.out.println("|---------------------|--------------------------|---------------------------|");
        System.out.println("| Exception on mod    | Yes (ConcurrentModEx)    | No                        |");
        System.out.println("| Works on            | Original collection      | Copy/snapshot             |");
        System.out.println("| Performance         | Better (no copy)         | Worse (copy overhead)     |");
        System.out.println("| Memory usage        | Less                     | More (copy in memory)     |");
        System.out.println("| Thread safety       | No                       | Yes                       |");
        System.out.println("| Use case            | Single thread            | Concurrent access         |");
        
        // 9. Performance ta'siri
        System.out.println("\n7. Performance considerations:");
        System.out.println("- CopyOnWriteArrayList: Good for rarely modified, frequently read lists");
        System.out.println("- Not suitable for frequently modified lists (copy overhead)");
        System.out.println("- Use when thread safety is required without external synchronization");
    }
}
```

## Amaliy Maslahatlar

1. **Qachon Iterator ishlatish kerak?**
    - Collection elementlarini o'qish va o'chirish uchun
    - For-each loop ishlatilmaydigan holatlarda
    - Concurrent modification'ni boshqarish kerak bo'lganda

2. **Qachon ListIterator ishlatish kerak?**
    - Faqat List'lar bilan ishlaganda
    - Ikkala yo'nalishda harakat qilish kerak bo'lganda
    - Elementlarni qo'shish yoki o'zgartirish kerak bo'lganda
    - Elementning index'ini bilish kerak bo'lganda

3. **Fail-fast vs Fail-safe tanlash:**
    - **Fail-fast**: Single thread, tez performance kerak bo'lsa
    - **Fail-safe**: Multi-thread, thread safety kerak bo'lsa
    - **Fail-safe**: Collection tez-tez o'zgartiriladigan bo'lsa

4. **Iterator patterns:**
    - **Remove through iterator**: Xavfsiz o'chirish usuli
    - **Avoid concurrent modification**: Iterator davomida collection'ni o'zgartirmang
    - **Use new iterator**: Modification'dan keyin yangi iterator oling

5. **Performance considerations:**
    - Iterator'lar for-each loop'ga nisbatan biroz sekinroq
    - ListIterator Iterator'ga nisbatan biroz sekinroq
    - Fail-safe iterator'lar sekinroq (copy overhead)

---

**Keyingi mavzu:** [12_Collections_Algorithms_Optional_SOLID.md](./12_Collections_Algorithms_Optional_SOLID.md)

**Oldingi mavzu:** [10_Views_Copies.md](./10_Views_Copies.md)

**Asosiy sahifaga qaytish:** [README.md](../README.md)

---

**Muhim Atamalar:**
- **Iterator** - Collection elementlarini iteratsiya qilish interfeysi
- **ListIterator** - Iterator'ning List'lar uchun kengaytmasi
- **Fail-Fast** - O'zgarishda exception chiqaradigan iterator
- **Fail-Safe** - O'zgarishda exception chiqarmaydigan iterator
- **ConcurrentModificationException** - Fail-fast iterator'lar chiqaradigan exception
- **modCount** - Modification count, o'zgarishlarni kuzatish
- **Snapshot** - Fail-safe iterator'lar ishlaydigan nusxa

> **Bolalar, o'rganishda davom etamiz!** 🚀