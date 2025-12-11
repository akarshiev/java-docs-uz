# LinkedList Ma'lumotlar Tuzilmasi

## Array (Massiv)

**Array** - bu kompyuter xotirasida bir guruh elementlarni saqlashning eng oddiy usuli. U ketma-ket xotira joyini ajratish va elementlarni ketma-ket yozishdan iborat bo'lib, ketma-ketlik oxiri maxsus NULL token bilan belgilanadi.

```
          A     B    C   D   NULL
10   11   12   13   14   15   16   17 
```

### Array Muammolari

#### 1. Ketma-ket Joylashish Talabi
Array elementlari doim ketma-ket joylashgan bo'lishi shart.

#### 2. O'lcham O'zgarganda Qiyinchilik
Array yangi element qo'shilganda va sig'imi oshganda yangi bo'sh joy izlaydi. Topgandan keyin barcha elementlar yangi xotira manziliga ko'chiriladi.

```
5    8    2    3    9    87
10   11   12   13   14   15   16   17 
```

**Muammo:** Yangi element qo'shish uchun bo'sh joy bo'lmasa, butun massiv ko'chirilishi kerak.

#### 3. Element O'chirishda Qiyinchilik
Arraydan birinchi element (5) o'chirilganda, uning o'rnini to'ldirish uchun keyingi barcha elementlar bitta pozitsiyaga siljishi kerak.

```
O'chirishdan oldin: [5, 8, 2, 3, 9]
O'chirishdan keyin: [8, 2, 3, 9]
```

**Muammo:** Agar 1000 ta element bo'lsa, 999 marta ko'chirish amalga oshirilishi kerak.

## Muammo Yechimi: LinkedList

Array'lardan farqli o'laroq, **Linked List** - bu linear data structure bo'lib, uning elementlari ketma-ket joylashmaydi. Elementlar pointer'lar yordamida bog'lanadi. Har bir node ma'lumot va keyingi node manzilini saqlaydi.

**Xususiyatlar:**
- Oxirgi node NULL saqlaydi
- Random access (tasodifiy kirish) mumkin emas

### LinkedList Turlari

#### 1. Singly Linked List (Bir yo'nalishli bog'langan ro'yxat)
```java
Node1 -> Node2 -> Node3 -> NULL
```
Faqat bitta yo'nalishda harakat qilish mumkin.

#### 2. Doubly Linked List (Ikki yo'nalishli bog'langan ro'yxat)
```java
NULL <- Node1 <-> Node2 <-> Node3 -> NULL
```
Ikkala yo'nalishda ham harakat qilish mumkin.

#### 3. Circular Linked List (Aylanma bog'langan ro'yxat)
```java
Node1 -> Node2 -> Node3 -> Node1 (back to start)
```
Oxirgi node birinchi node'ga ishora qiladi.

### LinkedList'ning Array'larga nisbatan Afzalliklari

#### 1. Dynamic Array (Dinamik massiv)
O'lcham avvaldan belgilanishi shart emas, kerak bo'lganda o'sadi.

#### 2. Oson Qo'shish/O'chirish
```java
// Qo'shish - O(1) complexity
newNode.next = current.next;
current.next = newNode;

// O'chirish - O(1) complexity
prev.next = current.next;
```

### LinkedList'ning Kamchiliklari

#### 1. Random Access Yo'q
Elementlarga ketma-ket kirish kerak. Binary search samarali emas.

#### 2. Qo'shimcha Xotira
Har bir element uchun pointer saqlash uchun qo'shimcha xotira kerak.

## Singly Linked List Amaliyoti

### SinglyLinkedList<E> Implementatsiyasi

```java
package collectionsframework.linkedlist;

import java.util.Iterator;
import java.util.NoSuchElementException;
import java.util.Objects;
import java.util.StringJoiner;

/**
 * SinglyLinkedList - bir yo'nalishli bog'langan ro'yxat implementatsiyasi
 * @param <E> ro'yxat elementlarining turi
 */
public class SinglyLinkedList<E> implements Iterable<E> {

    private Node<E> head;  // Ro'yxat boshi
    private int size;      // Elementlar soni

    /**
     * Elementni ro'yxat oxiriga qo'shish
     * @param element qo'shiladigan element
     * @return true - har doim true qaytaradi
     */
    public boolean add(E element) {
        var newNode = new Node<>(element);
        if (this.head == null) {
            this.head = newNode;
        } else {
            var current = this.head;
            while (current.next != null)
                current = current.next;
            current.next = newNode;
        }
        this.size++;
        return true;
    }

    /**
     * Index bo'yicha elementni olish
     * @param index element indeksi
     * @return element
     * @throws IndexOutOfBoundsException agar index noto'g'ri bo'lsa
     */
    public E get(int index) {
        Objects.checkIndex(index, size);
        if (index == 0)
            return head.element;

        var current = head;
        for (int i = 1; i <= index; i++)
            current = current.next;

        return current.element;
    }

    /**
     * Elementni ro'yxat boshiga qo'shish
     * @param element qo'shiladigan element
     * @return true
     */
    public boolean addAtBeginning(E element) {
        var newNode = new Node<>(element);
        if (head != null)
            newNode.next = head;
        head = newNode;
        size++;
        return true;
    }

    /**
     * Ro'yxat boshidan elementni o'chirish
     * @return o'chirilgan element
     * @throws NoSuchElementException agar ro'yxat bo'sh bo'lsa
     */
    public E removeFromBeginning() {
        if (head == null)
            throw new NoSuchElementException("LinkedList is empty");
        E element = head.element;
        head = head.next;
        size--;
        return element;
    }

    /**
     * Index bo'yicha elementni o'chirish
     * @param index o'chiriladigan element indeksi
     * @return o'chirilgan element
     * @throws IndexOutOfBoundsException agar index noto'g'ri bo'lsa
     */
    public E remove(int index) {
        Objects.checkIndex(index, size);
        if (index == 0) return removeFromBeginning();
        
        var current = head;
        for (int i = 1; i < index; i++)
            current = current.next;
        
        var element = current.next.element;
        current.next = current.next.next;
        return element;
    }

    /**
     * Ob'ekt bo'yicha elementni o'chirish
     * @param o o'chiriladigan ob'ekt
     * @return true - agar element topilib o'chirilsa, aks holda false
     */
    public boolean remove(Object o) {
        if (head == null) return false;
        Node<E> prev = null;
        Node<E> current = head;

        while (current != null) {
            if (Objects.equals(o, current.element)) {
                if (prev == null)
                    head = current.next;
                else
                    prev.next = current.next;
                size--;
                return true;
            }
            prev = current;
            current = current.next;
        }
        return false;
    }

    /**
     * Ro'yxatdagi elementlar sonini qaytarish
     * @return elementlar soni
     */
    public int size() {
        return size;
    }

    /**
     * Node ichki klassi - ro'yxatdagi har bir element
     * @param <E> element turi
     */
    private static class Node<E> {
        E element;      // Elementning o'zi
        Node<E> next;   // Keyingi elementga pointer

        public Node(E element) {
            this.element = element;
        }
    }

    /**
     * Ro'yxatni string ko'rinishiga o'tkazish
     * @return string formatdagi ro'yxat
     */
    @Override
    public String toString() {
        var sj = new StringJoiner(", ", "[", "]");
        var current = this.head;
        while (current != null) {
            sj.add(String.valueOf(current.element));
            current = current.next;
        }
        return sj.toString();
    }

    /**
     * Iterator yaratish - for-each loop uchun
     * @return iterator
     */
    @Override
    public Iterator<E> iterator() {
        return new Iterator<>() {
            private Node<E> current = head;

            @Override
            public boolean hasNext() {
                return current != null;
            }

            @Override
            public E next() {
                E element = current.element;
                current = current.next;
                return element;
            }
        };
    }

    /**
     * Asosiy method - sinov uchun
     */
    public static void main(String[] args) {
        var sll = new SinglyLinkedList<String>();
        sll.add("Java");
        sll.add("Scala");
        sll.add("Python");
        
        System.out.println("Ro'yxat: " + sll);
        System.out.println("Elementlar soni: " + sll.size());
        System.out.println("2-indexdagi element: " + sll.get(2));
        
        sll.addAtBeginning("C++");
        System.out.println("Boshiga qo'shgandan keyin: " + sll);
        
        sll.remove("Scala");
        System.out.println("'Scala' o'chirilgandan keyin: " + sll);
    }
}
```

## Doubly Linked List Amaliyoti

### DoublyLinkedList<E> Implementatsiyasi

```java
package collectionsframework.linkedlist;

import java.util.Iterator;
import java.util.Objects;
import java.util.StringJoiner;

/**
 * DoublyLinkedList - ikki yo'nalishli bog'langan ro'yxat implementatsiyasi
 * @param <E> ro'yxat elementlarining turi
 */
public class DoublyLinkedList<E> implements Iterable<E> {

    private Node<E> head;  // Ro'yxat boshi
    private Node<E> tail;  // Ro'yxat oxiri
    private int size;      // Elementlar soni

    /**
     * Elementni ro'yxat oxiriga qo'shish
     * @param element qo'shiladigan element
     * @return true
     */
    public boolean add(E element) {
        var l = tail;
        var newNode = new Node<>(element);
        tail = newNode;
        
        if (l == null) {
            head = newNode;
        } else {
            l.next = newNode;
            newNode.prev = l;
        }
        size++;
        return true;
    }

    /**
     * Index bo'yicha elementni olish - optimallashtirilgan
     * @param index element indeksi
     * @return element
     * @throws IndexOutOfBoundsException agar index noto'g'ri bo'lsa
     */
    public E get(int index) {
        Objects.checkIndex(index, size);
        if (index == 0)
            return head.element;

        Node<E> current = null;
        // Index ro'yxatning qaysi yarmida ekanligiga qarab optimallashtirish
        if (index < size / 2) {
            // Boshidan boshlab qidirish
            current = head;
            for (int i = 0; i < index; i++) {
                current = current.next;
            }
        } else {
            // Oxiridan boshlab qidirish
            current = tail;
            for (int i = size - 1; i > index; i--) {
                current = current.prev;
            }
        }
        return current.element;
    }

    /**
     * Iterator yaratish - for-each loop uchun
     * @return iterator
     */
    @Override
    public Iterator<E> iterator() {
        return new Iterator<E>() {
            private Node<E> current = head;

            @Override
            public boolean hasNext() {
                return current != null;
            }

            @Override
            public E next() {
                E element = current.element;
                current = current.next;
                return element;
            }
        };
    }

    /**
     * Ro'yxatni string ko'rinishiga o'tkazish
     * @return string formatdagi ro'yxat
     */
    @Override
    public String toString() {
        var sj = new StringJoiner(", ", "[", "]");
        this.forEach(lang -> sj.add(String.valueOf(lang)));
        return sj.toString();
    }

    /**
     * Node ichki klassi - ikki pointer'li
     * @param <E> element turi
     */
    private static class Node<E> {
        E element;      // Elementning o'zi
        Node<E> prev;   // Oldingi elementga pointer
        Node<E> next;   // Keyingi elementga pointer

        public Node(E element) {
            this.element = element;
        }

        public Node(E element, Node<E> prev, Node<E> next) {
            this.element = element;
            this.prev = prev;
            this.next = next;
        }
    }

    /**
     * Asosiy method - sinov uchun
     */
    public static void main(String[] args) {
        var dll = new DoublyLinkedList<String>();
        dll.add("Java");
        dll.add("C++");
        dll.add("Python");
        dll.add("Go");
        dll.add("Scala");
        dll.add("Kotlin");
        
        System.out.println("Ro'yxat: " + dll);
        System.out.println("4-indexdagi element: " + dll.get(4));
        System.out.println("Elementlar soni: " + dll.size);
    }
}
```

## Java LinkedList Class

Java'da **LinkedList** classi linear data structure bo'lib, ichki qatlamda doubly linked list yordamida elementlarni saqlaydi (JDK 1.2'dan beri).

### JavaLinkedListTest - Amaliy Misol

```java
package collectionsframework.linkedlist;

import java.util.Arrays;
import java.util.LinkedList;

/**
 * Java standart LinkedList sinfidan foydalanish misollari
 */
public class JavaLinkedListTest {
    public static void main(String[] args) {
        // LinkedList yaratish
        var programmingLanguages = new LinkedList<String>();
        programmingLanguages.add("Python");
        programmingLanguages.add("Java");

        // Ikkinchi LinkedList yaratish
        var languages = new LinkedList<String>();
        languages.add(0, "Scala");        // Boshiga qo'shish
        languages.add("Kotlin");          // Oxiriga qo'shish
        languages.add("Groovy");
        languages.addAll(2, programmingLanguages); // Index'ga boshqa list qo'shish
        
        System.out.println("Birlashtirilgan ro'yxat: " + languages);

        // Maxsus method'lar
        languages.addFirst("Go");        // Boshiga qo'shish
        languages.addLast("C");          // Oxiriga qo'shish
        languages.add("Kotlin");         // Duplikat qo'shish
        
        System.out.println("Maxsus method'lardan keyin: " + languages);

        // Element o'chirish
        languages.removeLastOccurrence("Kotlin"); // Oxirgi topilganini o'chirish
        System.out.println("O'chirilgandan keyin: " + languages);

        // Array'ga o'tkazish
        String[] objects = languages.toArray(String[]::new);
        System.out.println("Array ko'rinishi: " + Arrays.toString(objects));

        // Boshqa foydali method'lar
        System.out.println("Birinchi element: " + languages.getFirst());
        System.out.println("Oxirgi element: " + languages.getLast());
        System.out.println("Elementlar soni: " + languages.size());
        
        // Element mavjudligini tekshirish
        System.out.println("Java bor mi? " + languages.contains("Java"));
    }
}
```

## LinkedList vs ArrayList Tezlik Tahlili

### AnalyzeLinkedListAndArrayList - Performance Test

```java
package collectionsframework.linkedlist;

import java.util.ArrayList;
import java.util.LinkedList;

/**
 * LinkedList va ArrayList tezligini taqqoslash
 */
public class AnalyzeLinkedListAndArrayList {
    public static void main(String[] args) {
        var arrayList = new ArrayList<Integer>();
        var linkedList = new LinkedList<Integer>();

        final var maxElementCount = 100_000;

        // ArrayList'ga qo'shish (boshiga)
        var start = System.currentTimeMillis();
        addToArrayList(arrayList, maxElementCount);
        var end = System.currentTimeMillis();
        System.out.println("ArrayList qo'shish (boshiga) - " + (end - start) + "ms");

        // LinkedList'ga qo'shish (boshiga)
        start = System.currentTimeMillis();
        addToLinkedList(linkedList, maxElementCount);
        end = System.currentTimeMillis();
        System.out.println("LinkedList qo'shish (boshiga) - " + (end - start) + "ms");

        // ArrayList'dan o'chirish (boshidan)
        start = System.currentTimeMillis();
        removeFromBeginningArrayList(arrayList);
        end = System.currentTimeMillis();
        System.out.println("ArrayList o'chirish (boshidan) - " + (end - start) + "ms");

        // LinkedList'dan o'chirish (boshidan)
        start = System.currentTimeMillis();
        removeFromBeginningLinkedList(linkedList);
        end = System.currentTimeMillis();
        System.out.println("LinkedList o'chirish (boshidan) - " + (end - start) + "ms");
    }

    /**
     * ArrayList'ga boshiga element qo'shish
     * Time Complexity: O(n) - har safar elementlarni siljitish kerak
     */
    private static void addToArrayList(ArrayList<Integer> arrayList, int maxElementCount) {
        for (int i = 0; i < maxElementCount; i++) {
            arrayList.add(0, i);
        }
    }

    /**
     * LinkedList'ga boshiga element qo'shish
     * Time Complexity: O(1) - faqat pointer'ni o'zgartirish
     */
    private static void addToLinkedList(LinkedList<Integer> linkedList, int maxElementCount) {
        for (int i = 0; i < maxElementCount; i++) {
            linkedList.addFirst(i);
        }
    }

    /**
     * ArrayList'dan boshidan o'chirish
     * Time Complexity: O(n) - elementlarni siljitish kerak
     */
    private static void removeFromBeginningArrayList(ArrayList<Integer> arrayList) {
        for (int i = 0; i < 10000; i++) {
            arrayList.remove(0);
        }
    }

    /**
     * LinkedList'dan boshidan o'chirish
     * Time Complexity: O(1) - faqat pointer'larni o'zgartirish
     */
    private static void removeFromBeginningLinkedList(LinkedList<Integer> linkedList) {
        for (int i = 0; i < 10000; i++) {
            linkedList.removeFirst();
        }
    }
}
```

### Natijalar Tahlili

**Qo'shish (boshiga):**
- **ArrayList:** O(n) - Har safar barcha elementlarni siljitish kerak
- **LinkedList:** O(1) - Faqat pointer'larni o'zgartirish

**O'chirish (boshidan):**
- **ArrayList:** O(n) - Elementlarni siljitish kerak
- **LinkedList:** O(1) - Faqat pointer'larni o'zgartirish

**Random Access:**
- **ArrayList:** O(1) - Index bo'yicha to'g'ridan-to'g'ri kirish
- **LinkedList:** O(n) - Ketma-ket qidirish kerak

## Vector Class

**Vector** classi object'larning o'suvchi array'ini implement qiladi. Array kabi, u integer index yordamida kirish mumkin bo'lgan component'larni o'z ichiga oladi. Biroq, Vector o'lchami Vector yaratilgandan keyin qo'shiladigan va o'chiriladigan elementlarga moslashish uchun o'sishi yoki qisqarishi mumkin (JDK 1.0'dan beri).

### List Interface Hierarchy

```
List (Interface)
├── ArrayList
├── LinkedList
├── Vector
└── Stack
```

### Vector va ArrayList Farqlari

| Xususiyat | Vector | ArrayList |
|-----------|--------|-----------|
| **Sinxronizatsiya** | Thread-safe (synchronized) | Thread-unsafe |
| **Performance** | Sekinroq | Tezroq |
| **O'sish strategiyasi** | 2 marta o'sadi | 50% o'sadi |
| **Iterator** | Fail-safe | Fail-fast |
| **Tarix** | JDK 1.0 | JDK 1.2 |

### Vector Misoli

```java
import java.util.Vector;

public class VectorExample {
    public static void main(String[] args) {
        // Vector yaratish
        Vector<String> vector = new Vector<>();
        
        // Element qo'shish
        vector.add("Java");
        vector.add("Python");
        vector.add("C++");
        
        // Capacity va size
        System.out.println("Capacity: " + vector.capacity());
        System.out.println("Size: " + vector.size());
        
        // Element olish
        System.out.println("Element at index 1: " + vector.get(1));
        
        // Sinxronizatsiya - thread-safe
        synchronized(vector) {
            for (String lang : vector) {
                System.out.println(lang);
            }
        }
        
        // Legacy method'lar
        vector.addElement("JavaScript");  // add() bilan bir xil
        vector.insertElementAt("Go", 1);  // add(index, element) bilan bir xil
        
        System.out.println("Updated vector: " + vector);
    }
}
```

## Qachon Qaysisini Ishlatish Kerak?

### ArrayList Ishlatish:
- Ko'p o'qish amallari kerak bo'lsa
- Random access kerak bo'lsa
- Thread safety kerak bo'lmasa
- Ro'yxat o'lchami ko'p o'zgarmasa

### LinkedList Ishlatish:
- Ko'p qo'shish/o'chirish amallari kerak bo'lsa
- Boshiga/Oxiriga tez-tez element qo'shish kerak bo'lsa
- Queue yoki Deque amallari kerak bo'lsa

### Vector Ishlatish:
- Legacy code bilan ishlashda
- Thread safety zarur bo'lsa (lekin zamonaviy Collections.synchronizedList() yoki CopyOnWriteArrayList afzal)

## Amaliy Maslahatlar

1. **Default sifatida ArrayList ishlating** - Ko'p hollarda eng yaxshi performance
2. **LinkedList faqat boshiga/oxiriga tez-tez qo'shish/o'chirish kerak bo'lsa**
3. **Vector'dan qoching** - Legacy, Collections.synchronizedList() afzal
4. **Initial capacity belgilang** - Agar o'lcham ma'lum bo'lsa
5. **For-each yoki Iterator ishlating** - LinkedList uchun optimal

## Tekshiruv Savollari

1. **Array va LinkedList farqlari nima?**
2. **Singly va Doubly LinkedList farqi?**
3. **LinkedList qachon ArrayList'dan yaxshi?**
4. **Vector nima va nima uchun eskirgan hisoblanadi?**
5. **Random access complexity LinkedList va ArrayList uchun qanday?**

---

**Keyingi mavzu:** [06_Set_Interface.md](./06_Set_Interface.md)

**Oldingi mavzu:** [04_Collections_Framework_List_Interface_2.md](./04_Collections_Framework_List_Interface_2.md)

**Asosiy sahifaga qaytish:** [README.md](../README.md)

---

**Muhim Atamalar:**
- **Array** - Massiv, ketma-ket joylashgan elementlar
- **LinkedList** - Bog'langan ro'yxat, pointer'lar bilan bog'langan
- **Node** - Tugun, element va pointer(lar)ni saqlovchi birlik
- **Singly Linked** - Bir yo'nalishli bog'langan
- **Doubly Linked** - Ikki yo'nalishli bog'langan
- **Random Access** - Tasodifiy kirish, index orqali to'g'ridan-to'g'ri kirish
- **Sequential Access** - Ketma-ket kirish, boshidan boshlab qidirish
- **Thread-safe** - Thread'lar uchun xavfsiz, sinxronizatsiyalangan

> **Eslatma:** Data structure tanlash - bu trade-off. Har bir structure o'zining kuchli va zaif tomonlariga ega. Vazifaga qarab eng mosini tanlash muhim.

> **Bolalar, o'rganishda davom etamiz!** 🚀