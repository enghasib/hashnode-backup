---
title: "সিরিজ: কম্পিউটার আর্কিটেকচার – Process"
seoTitle: "The process"
seoDescription: "The process lifecycle includes transforming a static program into a dynamic entity, involving memory structures and execution states management"
datePublished: Mon Aug 04 2025 12:39:29 GMT+0000 (Coordinated Universal Time)
cuid: cmdx3kpy5001902lb8ztmhaqu
slug: process
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1754311038208/00afaaff-8e2e-4015-905b-57dc61c42880.jpeg
tags: os, process, processing, computer-architecture, computer-processes

---

## “Process: কম্পিউটার প্রোগ্রাম থেকে একটি জীবন্ত সত্তায় রূপান্তর”

আপনি যখন একটি সফটওয়্যার চালান—যেমন ব্রাউজার খুললেন, ভিডিও চালালেন বা কোড এডিটর চালু করলেন—তখন আসলে কি ঘটে?

এগুলোর প্রতিটিই হয় এক একটি **Process**। কিন্তু প্রশ্ন হচ্ছে—**Process** আসলে কী? কিভাবে একটি সাধারণ প্রোগ্রাম জীবন্ত হয়ে উঠে এবং কম্পিউটার রিসোর্স ব্যবহার করতে শুরু করে?

চলুন, আজ সেই জীবনচক্রটা বুঝে নেওয়া যাক।

---

### Process কী?

একটি Process হলো এক্সিকিউশনের সময় একটি প্রোগ্রামের **Running Instance**। সরলভাবে বললে:

> “প্রোগ্রাম হচ্ছে কোড (static), আর Process হচ্ছে সেই কোডের জীবন্ত চলমান রূপ (dynamic)।”

আপনি হয়তো শুনে থাকবেন, প্রগ্রামের এক্সিকিউশন ই হলো Process। যদিও এটা পুরাপুরি সত্য নয়। তাহলে প্রসেস কি ?

> একটি প্রগ্রাম রান করার জন্য যতগুলো যতগুলো কাজ করা হয় সবকিছু একত্রেই একটি প্রসেস গঠন করে।

একটি তৈরি করা প্রোগ্রাম ডিস্কে পড়ে থাকে; সেটি কোনো কাজ করতে পারে না। কিন্তু যখন সেটিকে Run করা হয়, তখন অপারেটিং সিস্টেম সেটিকে RAM-এ লোড করে, CPU-র রেজিস্টার সেটআপ করে এবং একটি স্বতন্ত্র Execution Context তৈরি করে—এই পুরো সেটআপটাই হল একটি **Process**।

---

### কিভাবে একটি Process তৈরি হয়?

একটি প্রোগ্রাম যখন চালু হয়, তখন নিচের ধাপগুলো ঘটে:

1. **Program Load**: অপারেটিং সিস্টেম ডিস্ক থেকে প্রোগ্রাম কোডকে RAM-এ লোড করে।
    
2. **Memory Allocation**: Stack, Heap, Data, Text সেগমেন্ট তৈরি হয়।
    
3. **Process ID (PID)** নির্ধারণ হয়।
    
4. **PCB তৈরি হয়**: Process Control Block—এই প্রসেসের সকল তথ্য ধারণ করে।
    
5. **Registers & Program Counter সেট হয়**
    
6. **CPU Execution শুরু করে**
    

Linux বা Unix এর মতো সিস্টেমে process তৈরির জন্য `fork()` এবং `exec()` নামে দুইটি সিস্টেম কল ব্যবহার করা হয়:

* `fork()` → মূল প্রোসেসের একটি কপি তৈরি করে
    
* `exec()` → নতুন প্রোগ্রামকে সেই প্রসেসে লোড করে দেয়
    

---

### Process Memory Layout (Structure)

প্রাথমিক ভবে একটি এক্সিকিউটেবল ফাইলের দুটি অংশ থাকে।

1. Text Segment: মেমরির এই সেকশনে executable প্রগ্রাম থাকে।
    
2. Data Segment: Initialized গ্লোবাল এবং static ভ্যারিয়েবল গুলো এখানে থাকে।
    

কিন্তু এই যখন প্রগ্রাম রান করার সময় আরও কিছু অতিরিক্ত storage এর প্রয়োজন হয়। যেমন একটি প্রগ্রাম রান করার পর রান টাইমে যে ডাটা গুলো create হয় সেগুলো রাখার জন্য, ইউজারের ইনপুট এবং Temporary variable গুলো রাখার জন্য ইত্যাদি। এই ডাটা গুলার জন্য Stack এবং Heap এর প্রয়োজন হয়।

সুতরাং প্রতিটি প্রসেসের মেমোরি চারটি ভাগে বিভক্ত থাকে:

```plaintext
Stack          ← অস্থায়ী ভ্যারিয়েবল, ফাংশনের কল
Heap           ← ডাইনামিক মেমোরি (malloc/new)  
Data Segment   ← গ্লোবাল ও স্ট্যাটিক ভ্যারিয়েবল
Text Segment   ← এক্সিকিউটেবল কোড
```

**1\. Text Segment**

* এখানে থাকে compiled machine code
    
* এটি read-only, যাতে ভুল করে কোড পরিবর্তন না হয়
    

**2\. Data Segment**

* Initialized গ্লোবাল এবং static ভ্যারিয়েবল এখানে থাকে
    

**3\. Heap**

* Run time-এ ডাইনামিকভাবে মেমোরি এলোকেশন হয় এখানে
    
* malloc, new ইত্যাদি এখানে কাজ করে
    

**4\. Stack**

* ফাংশনের লোকাল ভ্যারিয়েবল, রিটার্ন অ্যাড্রেস, ফ্রেম এখানে থাকে
    
* LIFO (Last In First Out) স্ট্রাকচারে কাজ করে
    

---

### Process-এর জীবনচক্র (States)

একটি Process সাধারণত নিচের স্টেটগুলোর মধ্য দিয়ে যায়:

* **New Process**: Process রয়েছে, তৈরি হচ্ছে
    
* **Ready**: এক্সিকিউট হওয়ার জন্য প্রস্তুত
    
* **Running**: CPU-তে চলছে
    
* **Waiting**: ইনপুট/আউটপুটের জন্য অপেক্ষায়
    
* **Terminated**: এক্সিকিউশন শেষ হয়ে গেছে
    

---

### একটি প্রোগ্রাম থেকে Process হবার গুরুত্ব

* Process মানেই OS তাকে আলাদা রিসোর্স দিয়েছে
    
* প্রতিটি Process এর নিজের আলাদা **Address Space** থাকে
    
* এক Process অন্যটির মেমোরি দেখতে পায় না (except shared memory)
    
* এটি নিশ্চিত করে security ও stability
    

---

🖊️ আল হাসিব  
📅 **চ্যালেঞ্জ**: ৩০ দিনে ৩০টি ব্লগ পোস্ট  
📂 **বিষয়**: Process, Memory Layout, Operating System, Execution Model