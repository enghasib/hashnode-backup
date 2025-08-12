---
title: "সিরিজ: কম্পিউটার এক্সিকিউশন মডেল (Thread) – পর্ব 2"
seoTitle: "thread scheduling"
seoDescription: "Explore the intricacies of thread scheduling: learn how operating systems manage threads, maximize CPU usage, and ensure system responsiveness"
datePublished: Wed Aug 06 2025 17:53:31 GMT+0000 (Coordinated Universal Time)
cuid: cme09o9pn000o02kv0zg2cj8d
slug: threadscheduling
tags: threads, thread-scheduling

---

### “Thread Scheduling: কোন থ্রেড চলবে, আর কে বসে থাকবে?”

আপনি যদি একটি মাল্টিথ্রেড অ্যাপ চালান, যেখানে একসাথে অনেকগুলো থ্রেড তৈরি হচ্ছে, তাহলে CPU কীভাবে ঠিক করে কোন থ্রেডটা আগে চলবে,এবং কোনটা পরে? সবগুলো তো আর একসাথে চলতে পারবে না, বিশেষ করে যখন কোর সংখ্যা সীমিত।

এই প্রশ্নের উত্তরই দেয়: **Thread Scheduling** চলুন আজকে জেনে নেওয়া যাক Thread Scheduling সম্পর্কে যাবতীয় প্রশ্নের উত্তর।

---

### Thread Scheduling আসলে কী?

**Thread Scheduling** হচ্ছে এমন একটি পদ্ধতি যেখানে **Operating System ঠিক করে**—কোন থ্রেডকে কখন, কতক্ষণ, এবং কোন CPU কোরে রান করা হবে।

যেহেতু অনেক থ্রেড একসাথে রান করতে চায়, আর CPU কোর সংখ্যা সীমিত, তাই এই Thread গুলোকে লাইন ধরিয়ে, সময় ভাগ করে একে একে চালানো হয়। এই পুরো কাজটি করে **Scheduler** নামক একটি বিশেষ অংশ, যা OS-এর ভেতরে কাজ করে।

---

### বাস্তব উদাহরণ দিয়ে ভাবুন

করুন একটি সরকারি অফিস যেখানে ১০ জন আবেদনকারী এসেছেন সার্ভিস নিতে, কিন্তু সেখানে মাত্র ২টি কাউন্টার খোলা আছে। এখন কে কখন সার্ভিস পাবে, সেটা নির্ধারণ করবে অফিসের **টোকেন মেশিন বা লাইন ম্যানেজার**— এখানে নির্ধারণ করার কাজটি যে করে সেটাই হলো Scheduler।

এখানে প্রতিটি আবেদনকারী = একেকটি Thread  
প্রতিটি কাউন্টার একেকটা = CPU কোর  
এবং লাইন ম্যানেজার = Scheduler

---

### কেন Thread Scheduling করা প্রয়োজন?

* **CPU ব্যবহারের সর্বোচ্চ মান নিশ্চিত করতে**
    
* **সব থ্রেডকে ফেয়ার সুযোগ দেওয়ার জন্য**
    
* **Resposiveness বজায় রাখতে (UI বা রিয়েল টাইম অ্যাপের জন্য)**
    
* **Blocking বা starvation থেকে বাঁচাতে**
    

---

### Scheduler কী দেখে সিদ্ধান্ত নেয়?

Scheduler নিচের বিষয়গুলো দেখে Thread গুলোকে রান করে:

* **Priority**: কোন থ্রেড কতটা গুরুত্বপূর্ণ
    
* **State**: থ্রেড Ready আছে, না কি Waiting-এ
    
* **Time Slice (Quantum)**: একটি থ্রেড কতক্ষণ CPU পাবে
    
* **Affinity**: কোনো থ্রেডের জন্য নির্দিষ্ট কোনো CPU দরকার কি না
    
* **I/O Status**: থ্রেড I/O ওয়েট করছে কি না
    

---

### Scheduling Techniques এর মূল ধারণাগুলো হলোঃ

১. **Preemptive Scheduling**

* এক থ্রেড নির্দিষ্ট সময় (Time Slice) শেষ হলে CPU ছাড়তে বাধ্য হয়
    
* এতে Responsive অ্যাপ সহজে তৈরি হয়
    
* উদাহরণ: Desktop OS (Windows, Linux)
    

২. **Cooperative Scheduling**

* থ্রেড নিজেই সিদ্ধান্ত নেয় কখন CPU ছাড়বে
    
* সহজ কিন্তু রিস্কি—একটা থ্রেড দেরি করলে সব আটকে যাবে
    
* উদাহরণ: পুরনো Mac OS, embedded system
    

---

### Thread Scheduling এর কাজের ধাপগুলো হলোঃ

1. **Thread Ready State-এ আসবে**
    
2. **Scheduler Queue-তে রাখা হবে**
    
3. **Scheduler Thread বাছাই করবে (based on priority, fairness)**
    
4. **Context Switch হবে (আগের থ্রেডের state সেভ, নতুন থ্রেডের state লোড)**
    
5. **এবং নতুন Thread CPU-তে চালু হবে**
    

---

### CPU-bound vs I/O-bound Thread

* **CPU-bound Thread**: সবসময় computation করে। বেশি সময় CPU চাই।
    
* **I/O-bound Thread**: বেশি সময় অপেক্ষা করে I/O (ডিস্ক, নেটওয়ার্ক) এর জন্য। কম সময় CPU লাগে।
    

Scheduler চেষ্টা করে এই দুই ধরনের থ্রেডের মাঝে **ব্যালেন্স** রাখতে, যাতে CPU কখনো অলস না থাকে।

---

### Modern Scheduler কীভাবে কাজ করে?

আধুনিক OS গুলোতে (Linux, Windows) রয়েছে অনেক উন্নত Scheduler যেমন:

* **CFS (Completely Fair Scheduler)** – Linux-এ ব্যবহার হয়
    
* **Multilevel Feedback Queue (MLFQ)**
    
* **O(1) Scheduler** – আগের Linux version-এ
    
* **Priority-based Round Robin** – অনেক RTOS-এ
    

এসব Scheduler গুলো Fairness, Efficiency, Responsiveness এর ব্যালান্স করার চেষ্টা করে।

---

### Multi-core প্রসঙ্গে: Thread কে কোন কোরে রান হবে ?

একাধিক কোর থাকলে Scheduler আরও একটি কাজ করে:  
**Thread কে কোন কোরে চালানো হবে সেটি নির্ধারণ করা।**

যে থ্রেড আগেও কোনো নির্দিষ্ট কোরে চালানো হয়েছিল, Scheduler চেষ্টা করে তাকে আবার ওই কোরে চালাতে—একে বলে **CPU Affinity**। এতে Cache Hit বাড়ে, performance ভালো হয়।