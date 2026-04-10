 **clean GitHub README-style notes** 🔥


---

````md
# 🚀 Mongoose Cheat Sheet (Interview Ready)

A complete guide to essential Mongoose methods for backend interviews.

---

# 🧠 1. MUST-KNOW METHODS (🔥 Interview Critical)

## 📌 CREATE

```js
const user = new User({ name: "Somo", age: 21 });
await user.save();
````

```js
await User.create({ name: "Somo", age: 21 });
```

### ✅ Key Points

* `.save()` → instance method
* `.create()` → model method
* `.save()` triggers validation & middleware

---

## 📌 READ

### 1. Find Multiple

```js
await User.find({ age: { $gt: 18 } });
```

### 2. Find One

```js
await User.findOne({ email: "test@gmail.com" });
```

### 3. Find By ID

```js
await User.findById(id);
```

### ✅ Differences

* `find()` → returns array
* `findOne()` → returns single document
* `findById()` → shortcut for `_id`

---

## 📌 UPDATE

### 1. Update One

```js
await User.updateOne({ email }, { name: "New Name" });
```

### 2. Find One And Update 🔥

```js
await User.findOneAndUpdate(
  { email },
  { name: "Updated" },
  { new: true }
);
```

### 3. Update By ID

```js
await User.findByIdAndUpdate(id, { name: "Updated" }, { new: true });
```

### ✅ Important

* `{ new: true }` → returns updated document
* default → returns old document

---

## 📌 DELETE

```js
await User.deleteOne({ email });
```

```js
await User.deleteMany({ age: { $lt: 18 } });
```

```js
await User.findByIdAndDelete(id);
```

---

## 📌 UPSERT 🔥

```js
await User.updateOne(
  { email: "test@gmail.com" },
  { name: "Somo" },
  { upsert: true }
);
```

### ✅ Meaning

* If exists → update
* If not → create

---

# ⚡ 2. IMPORTANT METHODS (INTERMEDIATE)

## 📌 Projection

```js
await User.find().select("name email");
```

---

## 📌 Sorting

```js
await User.find().sort({ age: -1 });
```

* `1` → ascending
* `-1` → descending

---

## 📌 Pagination

```js
await User.find().limit(10).skip(20);
```

---

## 📌 Count

```js
await User.countDocuments({ age: { $gt: 18 } });
```

---

## 📌 Exists

```js
await User.exists({ email });
```

---

## 📌 Lean 🔥

```js
await User.find().lean();
```

### ✅ Benefit

* Returns plain JS object
* Faster performance

---

# 🧩 3. RELATIONS

## 📌 Populate

```js
await Post.find().populate("user");
```

### ✅ Use Case

* Works like JOIN in SQL
* Fetch referenced documents

---

# ⚙️ 4. INSTANCE METHODS

```js
const user = await User.findById(id);
user.name = "New";
await user.save();
```

---

# 🧪 5. VALIDATION & MIDDLEWARE

## 📌 Pre-save Hook

```js
userSchema.pre("save", function(next) {
  console.log("Before saving");
  next();
});
```

### ✅ Use Cases

* Password hashing
* Logging
* Data transformation

---

# 🚀 6. ADVANCED (HIGH VALUE)

## 📌 Aggregation

```js
await User.aggregate([
  { $match: { age: { $gt: 18 } } },
  { $group: { _id: "$age", count: { $sum: 1 } } }
]);
```

---

## 📌 Transactions

```js
const session = await mongoose.startSession();
session.startTransaction();

try {
  await User.create([{ name: "Somo" }], { session });
  await session.commitTransaction();
} catch (err) {
  await session.abortTransaction();
}
```

---

## 📌 Indexing

```js
userSchema.index({ email: 1 }, { unique: true });
```

---

# 🧾 FINAL INTERVIEW CHECKLIST ✅

## 🔥 Core

* create / save
* find / findOne / findById
* updateOne / findOneAndUpdate
* deleteOne / findByIdAndDelete
* upsert
* select / sort / limit / skip
* populate

## ⚡ Strong Candidate

* lean()
* countDocuments()
* exists()
* middleware
* validation

## 🚀 Standout Candidate

* aggregation
* transactions
* indexing

---

# 🧠 PRO TIP

👉 Prefer `findOneAndUpdate()` over `updateOne()` because:

* returns updated document
* supports options
* more flexible

---

# ⭐ Bonus Tip

Always use:

```js
{ new: true, runValidators: true }
```

---

```

---


