# 🚀 Software Engineering: Under The Hood

ეს არის ჩემი პირადი გზამკვლევი პროგრამული ინჟინერიის რთული საკითხების "გლეხურად" და გასაგებად ასახსნელად. აქ თავმოყრილია ყველაფერი, რაც პროგრამირების "კაპოტის ქვეშ" ხდება.

## 📂 Table of Contents
1. [🏛️ The Evolution of Memory: Why two worlds?](#1-the-evolution-of-memory-why-two-worlds)
2. [⚡ Compiler Internals: Structs, Static & JIT](#2-compiler-internals-structs-static--jit)
3. [🕵️ Memory Deceptions: Stack leaks into Heap](#3-memory-deceptions-stack-leaks-into-heap)
4. [♻️ Resource Management: GC, ArrayPool & Streams](#4-resource-management-gc-arraypool--streams)
5. [🌐 Infrastructure & Scaling: Pods & Load Balancers](#5-infrastructure--scaling-pods--load-balancers)

---

## 1. The Evolution of Memory: Why two worlds? <a name="1-the-evolution-of-memory-why-two-worlds"></a>

### 📜 The Chaos Era: Only Heap
თავდაპირველად არსებობდა მხოლოდ ერთი დიდი მეხსიერება — **Heap**. როცა პროგრამას რამე სჭირდებოდა, ის ითხოვდა ადგილს ამ დიდ „საწყობში“.
* **The Problem:** წარმოიდგინე საწყობი, სადაც ნივთებს უწესრიგოდ ყრიან. როცა რაღაცას ვასრულებთ, ხელით უნდა გვახსოვდეს ნივთის გადაგდება. თუ დაგვავიწყდა — ხდება **Memory Leak**. თუ ბევრჯერ ვითხოვთ პატარა ადგილებს, საწყობი „დაცხრილული“ ხდება (**Fragmentation**) და დიდი ნივთისთვის ადგილი აღარ რჩება.

### 🏗️ The Order Era: Introducing the Stack
ინჟინრები მიხვდნენ: ფუნქციების უმეტესობას მონაცემები მხოლოდ მუშაობის პროცესში სჭირდება. ასე გაჩნდა **Stack**.
* **Why Stack?** სისწრაფისთვის და ავტომატური წესრიგისთვის. ის მუშაობს LIFO (Last-In, First-Out) პრინციპით. დაამთავრა ფუნქციამ მუშაობა? **CPU** უბრალოდ წევს **Stack Pointer**-ს ქვევით და ყველაფერი წამში „დავიწყებულია“.



---

## 2. Compiler Internals: Structs, Static & JIT <a name="2-compiler-internals-structs-static--jit"></a>

### 💎 Value Types (Struct) vs Reference Types (Class)
* **Struct:** კომპილერი მას აღიქვამს, როგორც ერთიან ბლოკს. ის პირდაპირ **Stack**-ზე იდება.
* **Class:** კომპილერი მას ყოფს ორად: **Pointer** (მისამართი) **Stack**-ზე და რეალური მონაცემები **Heap**-ზე.

### ⚡ JIT (Just-In-Time) Compiler & Inlining
**JIT** არის შენი კოდის პირადი ოპტიმიზატორი. ის ხედავს ხშირად გამოძახებად მეთოდებს და მათ პირდაპირ **Native Code**-ში თარგმნის.
* **Inlining:** თუ მეთოდი პატარაა, **JIT** მას საერთოდ აქრობს და მის შიგთავსს პირდაპირ გამომძახებელ კოდში სვამს, რომ **Stack**-ზე ზედმეტი **Stack Frame**-ის აშენება არ მოუწიოს.

---

## 3. Memory Deceptions: When Stack leaks into Heap <a name="3-memory-deceptions-stack-leaks-into-heap"></a>

ზოგჯერ გვგონია, რომ მონაცემი **Stack**-ზეა, მაგრამ ის სინამდვილეში **Heap**-ზე „იპარება“:
* **Boxing:** როცა **Value Type** (მაგ. int) გადაგვყავს **Object**-ში. მონაცემი სტეკიდან გადადის ჰიპზე, რაც პერფორმანსისთვის „მტკივნეულია“.
* **Closures & Lambdas:** თუ ფუნქციის შიგნით იყენებ ლოკალურ ცვლადს ლამბდაში, ის ცვლადი **Heap**-ზე გადაბარგდება, რადგან ლამბდამ შეიძლება ფუნქციის დასრულების შემდეგაც იცოცხლოს.
* **Async/Await:** `await`-ზე მეთოდის მდგომარეობა (**State Machine**) დროებით ინახება **Heap**-ზე, რომ **Thread**-ი გათავისუფლდეს და სხვა საქმე აკეთოს.



---

## 4. Resource Management: GC, ArrayPool & Streams <a name="4-resource-management-gc-arraypool--streams"></a>

### 🧹 Garbage Collector (GC)
ეს არის „საწყობის დამლაგებელი“. ის პერიოდულად ამოწმებს — რომელიმე **Pointer** (სტეკიდან) კიდევ უყურებს ამ ობიექტს ჰიპზე? თუ არა — ნაგავში!

### ♻️ ArrayPool & Streams (Efficiency)
* **ArrayPool:** ნუ ქმნი ახალ **Byte Array**-ს ყოველ წამს. გამოიყენე **ArrayPool** — ეს არის „ჭურჭლის სარეცხი მანქანა“. გამოიყენე თეფში, გარეცხე და დააბრუნე.
* **Streams:** ნუ ტვირთავ მთელ ფაილს **RAM**-ში. გამოიყენე ბუფერირებული წაკითხვა (ყლუპ-ყლუპად), რომ მეხსიერება არ გაივსოს.

---

## 5. Infrastructure & Scaling: Pods & Load Balancers 🌐 <a name="5-infrastructure--scaling-pods--load-balancers"></a>

როცა შენი კოდი **Memory Management**-ს არასწორად აკეთებს, **Pod** იწყებს „გასივებას“.
* **Vertical Scaling:** პოდს ვუმატებთ **RAM**-ს და **CPU**-ს (ვზრდით ერთ სამზარეულოს).
* **Horizontal Scaling:** ვამატებთ მეტ პოდს (ვხსნით მეტ სამზარეულოს).
* **Load Balancer:** ის არის „ადმინისტრატორი“, რომელიც შემოსულ ტრაფიკს იმ პოდში უშვებს, რომელიც ყველაზე ნაკლებადაა დატვირთული.

---

### 💡 Summary for Engineers
ამ ყველაფრის ცოდნა ნიშნავს, რომ შენ არ წერ მხოლოდ კოდს — შენ მართავ კომპიუტერის რესურსებს. ყოველი `new` ნიშნავს **Heap**-ზე გასვლას, ყოველი `static` ნიშნავს მუდმივ ადგილს მეხსიერებაში, ხოლო ყოველი `async` ნიშნავს ჭკვიანურ **Thread**-მენეჯმენტს.
