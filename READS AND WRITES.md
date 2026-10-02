<h3 style="text-align: center;">READS AND WRITES</h3>
Think of a database as a big filing cabinet, and your application as a person who needs to use that cabinet

### 📖 READ = “Give me information”

A read happens when your application asks the database to retrieve existing information.

Imagine a school office with a filing cabinet.  
You walk in and say:  
“Please give me Bob’s student record.”

The clerk finds Bob’s file and hands you the information.  
That’s a **READ**.

```
Application
    │
    │ "Give me Bob's record"
    ▼
Database
    │
    │ finds existing data
    ▼
"Bob, Computer Security, Year 3"
```

**Examples of READ:**

- Opening your Facebook profile → READ
- Checking your bank balance → READ
- Viewing a product on an online shop → READ
- Searching for a student’s marks → READ
- Loading your emails → READ

---

### ✍️ WRITE = “Change the information”

A write happens when your application adds, changes, or removes information in the database.

Using our school office:  
You tell the clerk:  
“Add my new phone number to my student record.”

The clerk opens your file and changes the information.  
That’s a **WRITE**.

```
Application
    │
    │ "Change Bob's phone number"
    ▼
Database
    │
    │ modifies stored data
    ▼
Updated record
```

**Writes include:**

- **INSERT** → add new information
- **UPDATE** → change existing information
- **DELETE** → remove information

---

## 🛒 E-commerce example

Imagine you’re using an online shopping website.

You search for:  
“Laptop”

The website retrieves laptops from its database.  
That’s a **READ**.

```
You → Website → Database
                  ↓
             Find laptops
                  ↓
             Laptop results
                  ↓
                You
```

Then you buy one.  
The system needs to record your order.  
That’s a **WRITE**.

```
You → Website → Database
                  ↓
            Create order
                  ↓
       "Bob bought Laptop X"
```

Now suppose you open **My Orders**.  
The website retrieves your previous order.  
That’s another **READ**.

```
Database
    │
    │ READ
    ▼
Your orders
```

---

## 🏦 Bank analogy

This is an even better example.

Suppose your account contains:  
**Balance = KSh 10,000**

### Checking your balance

You ask:  
“How much money do I have?”

The bank system looks at the stored value.  
That’s a **READ**.

```
Database
   │
   │ READ
   ▼
KSh 10,000
```

Nothing changes.

### Depositing KSh 5,000

Your balance becomes:  
**KSh 15,000**

The system changes the stored information.  
That’s a **WRITE**.

```
Old:
KSh 10,000

       + KSh 5,000
            ↓

New:
KSh 15,000
```

### Checking the new balance

You ask:  
“How much do I have now?”

The system retrieves:  
**KSh 15,000**

That’s a **READ**.

So one simple transaction can involve both writes and reads.

---

## 🧠 The easiest way to remember

Think:

- **READ** = Look at the notebook.
- **WRITE** = Change the notebook.

|Operation|What happens?|Example|
|---|---|---|
|READ|Look at existing data|“What’s my balance?”|
|WRITE|Add/change/remove data|“Deposit KSh 5,000”|

Or even simpler:

- **READ**: “Tell me what is there.”
- **WRITE**: “Put something there or change what is there.”

---

## In a real application

Suppose you have:

- **User → Login**  
    The application may **READ** the database to find the user’s account.

Then:

- **User → Change password**  
    The application **WRITES** the new password information.

So when you’re learning databases, APIs, caching, distributed systems, Kafka, etc., you’ll repeatedly encounter these two fundamental operations:

- **READ** = retrieve data
- **WRITE** = modify data