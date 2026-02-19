# Software-Engineering-Under-The-Hood
Technical notes and deep dives into .NET internals, infrastructure scaling, and concurrency. My personal guide to mastering what's happening under the hood.



🚀 Software Engineering: Under The Hood
1. The Evolution of Memory: Why two worlds?
📜 The Chaos Era: Only Heap
თავდაპირველად არსებობდა მხოლოდ ერთი დიდი მეხსიერება — Heap. როცა პროგრამას რამე სჭირდებოდა, ის ითხოვდა ადგილს ამ დიდ „საწყობში“.

The Problem: წარმოიდგინე დიდი საწყობი, სადაც ნივთებს უწესრიგოდ ყრიან. როცა რაღაცას ვასრულებთ, ხელით უნდა გვახსოვდეს ნივთის გადაგდება. თუ დაგვავიწყდა — ხდება Memory Leak. თუ ბევრჯერ ვითხოვთ პატარა ადგილებს, საწყობი „დაცხრილული“ ხდება (Fragmentation) და დიდი ნივთისთვის ადგილი აღარ რჩება.

🏗️ The Order Era: Introducing the Stack
ინჟინრები მიხვდნენ: ფუნქციების უმეტესობას მონაცემები მხოლოდ მუშაობის პროცესში სჭირდება. ასე გაჩნდა Stack.

Why Stack? სისწრაფისთვის და ავტომატური წესრიგისთვის. ის მუშაობს LIFO (Last-In, First-Out) პრინციპით. დაამთავრა ფუნქციამ მუშაობა? CPU უბრალოდ წევს Stack Pointer-ს ქვევით და ყველაფერი წამში „დავიწყებულია“.

2. Compiler Internals: Structs, Static & JIT
💎 Value Types (Struct) vs Reference Types (Class)
Struct: კომპილერი მას აღიქვამს, როგორც ერთიან ბლოკს. ის პირდაპირ Stack-ზე იდება.

Class: კომპილერი მას ყოფს ორად: Pointer (მისამართი) Stack-ზე და რეალური მონაცემები Heap-ზე.

⚡ JIT (Just-In-Time) Compiler & Inlining
JIT არის შენი კოდის პირადი ოპტიმიზატორი. ის ხედავს ხშირად გამოძახებად მეთოდებს და მათ პირდაპირ Native Code-ში თარგმნის.

Inlining: თუ მეთოდი პატარაა, JIT მას საერთოდ აქრობს და მის შიგთავსს პირდაპირ გამომძახებელ კოდში სვამს, რომ Stack-ზე ზედმეტი Stack Frame-ის აშენება არ მოუწიოს.

3. Memory Deceptions: When Stack leaks into Heap
ზოგჯერ გვგონია, რომ მონაცემი Stack-ზეა, მაგრამ ის სინამდვილეში Heap-ზე „იპარება“:

Boxing: როცა Value Type გადაგვყავს Object-ში. მონაცემი სტეკიდან გადადის ჰიპზე, რაც პერფორმანსისთვის „მტკივნეულია“.

Closures & Lambdas: თუ ფუნქციის შიგნით იყენებ ლოკალურ ცვლადს ლამბდაში, ის ცვლადი Heap-ზე გადაბარგდება, რადგან ლამბდამ შეიძლება ფუნქციის დასრულების შემდეგაც იცოცხლოს.

Async/Await: await-ზე მეთოდის მდგომარეობა (State Machine) დროებით ინახება Heap-ზე, რომ Thread-ი გათავისუფლდეს.

4. Resource Management: GC, ArrayPool & Streams
🧹 Garbage Collector (GC)
ეს არის „საწყობის დამლაგებელი“. ის პერიოდულად ამოწმებს — რომელიმე Pointer (სტეკიდან) კიდევ უყურებს ამ ობიექტს ჰიპზე? თუ არა — ნაგავში!

♻️ ArrayPool & Streams (Efficiency)
ArrayPool: ნუ ქმნი ახალ Byte Array-ს ყოველ წამს. გამოიყენე ArrayPool — ეს არის „ჭურჭლის სარეცხი მანქანა“. გამოიყენე თეფში, გარეცხე და დააბრუნე.

Streams: ნუ ტვირთავ მთელ ფაილს RAM-ში. გამოიყენე ბუფერირებული წაკითხვა, რომ მეხსიერება არ გაივსოს.

5. Infrastructure & Scaling: Pods & Load Balancers 🌐
როცა შენი კოდი Memory Management-ს არასწორად აკეთებს, Pod იწყებს „გასივებას“.

Vertical Scaling: პოდს ვუმატებთ RAM-ს და CPU-ს.

Horizontal Scaling: ვამატებთ მეტ პოდს (Instances).

Load Balancer: ის არის „ადმინისტრატორი“, რომელიც შემოსულ ტრაფიკს იმ პოდში უშვებს, რომელიც ყველაზე ნაკლებადაა დატვირთული.

💡 Summary for Engineers
ამ ყველაფრის ცოდნა ნიშნავს, რომ შენ არ წერ მხოლოდ კოდს — შენ მართავ კომპიუტერის რესურსებს. ყოველი new ნიშნავს Heap-ზე გასვლას, ყოველი static ნიშნავს მუდმივ ადგილს მეხსიერებაში, ხოლო ყოველი async ნიშნავს ჭკვიანურ Thread-მენეჯმენტს.
