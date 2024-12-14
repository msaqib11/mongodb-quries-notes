# mongodb-quries-notes
# MongoDB Query Guide: Comprehensive Reference

## 1. Introduction to MongoDB Queries

### Basic Connection and Database Selection
```javascript
// Connect to MongoDB
use myDatabase

// Show current database
db

// List all databases
show dbs

// List collections in current database
show collections
```

## 2. CRUD Operations

### 2.1 Create Operations (Insert)

#### Insert Single Document
```javascript
// Insert a single user document
db.users.insertOne({ 
    name: "John Doe", 
    age: 30, 
    email: "john@example.com",
    status: "active",
    registeredDate: new Date()
})
```

#### Insert Multiple Documents
```javascript
// Insert multiple users in one operation
db.users.insertMany([
    { name: "Alice Smith", age: 28, city: "New York" },
    { name: "Bob Johnson", age: 35, city: "San Francisco" },
    { name: "Charlie Brown", age: 25, city: "Chicago" }
])
```

### 2.2 Read Operations (Find)

#### Basic Querying
```javascript
// Find all documents
db.users.find()

// Find with specific condition
db.users.find({ age: { $gt: 25 } })

// Find with multiple conditions
db.users.find({ 
    age: { $gt: 25, $lt: 35 },
    city: "New York"
})

// Projection (select specific fields)
db.users.find(
    { age: { $gt: 25 } }, 
    { name: 1, age: 1, _id: 0 }
)
```

#### Advanced Filtering
```javascript
// Complex query with multiple operators
db.products.find({
    $and: [
        { price: { $gte: 50, $lte: 200 } },
        { category: { $in: ["Electronics", "Computers"] } },
        { inStock: true }
    ]
})
```

### 2.3 Update Operations

#### Basic Updates
```javascript
// Update a single document
db.users.updateOne(
    { name: "John Doe" },
    { $set: { age: 31, status: "premium" } }
)

// Update multiple documents
db.users.updateMany(
    { city: "New York" },
    { $inc: { loyaltyPoints: 10 } }
)
```

#### Advanced Update Operators

##### $inc (Increment)
```javascript
// Increment numeric fields
db.products.updateOne(
    { _id: productId },
    { 
        $inc: { 
            quantity: -1,     // Decrease inventory
            totalSales: 1     // Increase sales count
        } 
    }
)
```

##### $push (Array Manipulation)
```javascript
// Add elements to an array
db.users.updateOne(
    { name: "John Doe" },
    { 
        $push: { 
            hobbies: "photography",
            // Add multiple elements
            skills: { $each: ["MongoDB", "React"] },
            // Limit array size
            recentActivities: { 
                $each: ["Logged in"],
                $slice: -5  // Keep only last 5 elements
            }
        } 
    }
)
```

##### $pull and $addToSet
```javascript
// Remove specific array elements
db.users.updateOne(
    { name: "John Doe" },
    { $pull: { hobbies: "old hobby" } }
)

// Add unique elements to array
db.users.updateOne(
    { name: "John Doe" },
    { $addToSet: { skills: "Python" } }
)
```

### 2.4 Delete Operations
```javascript
// Delete single document
db.users.deleteOne({ name: "John Doe" })

// Delete multiple documents
db.users.deleteMany({ status: "inactive" })
```

## 3. Advanced Query Techniques

### 3.1 Comparison Operators
```javascript
// Comparison query operators
db.users.find({ 
    age: { 
        $eq: 30,    // Equal to
        $ne: 25,    // Not equal to
        $gt: 25,    // Greater than
        $gte: 25,   // Greater than or equal
        $lt: 35,    // Less than
        $lte: 35    // Less than or equal
    }
})

// Element in a list
db.users.find({ 
    city: { $in: ["New York", "San Francisco"] } 
})
```

### 3.2 Logical Operators
```javascript
// Logical operators
db.products.find({
    $and: [
        { price: { $gt: 50 } },
        { category: "Electronics" }
    ]
})

db.products.find({
    $or: [
        { status: "sale" },
        { quantity: { $gt: 100 } }
    ]
})

// Negation
db.users.find({ 
    age: { $not: { $lt: 25 } } 
})
```

### 3.3 Text Search
```javascript
// Create text index
db.articles.createIndex({ content: "text" })

// Perform text search
db.articles.find({ 
    $text: { $search: "mongodb database" } 
})
```

### 3.4 Geospatial Queries
```javascript
// Create geospatial index
db.locations.createIndex({ location: "2dsphere" })

// Find locations near a point
db.locations.find({
    location: {
        $near: {
            $geometry: {
                type: "Point",
                coordinates: [-73.9667, 40.78]
            },
            $maxDistance: 1000  // meters
        }
    }
})
```

## 4. Aggregation Framework

### Basic Aggregation
```javascript
db.orders.aggregate([
    // Match stage
    { $match: { status: "completed" } },
    
    // Group stage
    { $group: {
        _id: "$customerId",
        totalSpent: { $sum: "$amount" },
        averageOrder: { $avg: "$amount" },
        orderCount: { $sum: 1 }
    }},
    
    // Sort stage
    { $sort: { totalSpent: -1 } },
    
    // Limit results
    { $limit: 10 }
])
```

## 5. Indexing Strategies

### Creating Indexes
```javascript
// Single field index
db.users.createIndex({ email: 1 })

// Compound index
db.products.createIndex({ 
    category: 1, 
    price: -1 
})

// Unique index
db.users.createIndex({ email: 1 }, { unique: true })

// Partial index
db.users.createIndex(
    { age: 1 },
    { partialFilterExpression: { age: { $gt: 18 } } }
)
```

## 6. Best Practices

### Performance Optimization
- Use indexes wisely
- Avoid complex queries on large collections
- Use projection to limit returned fields
- Leverage aggregation for complex data processing
- Monitor and analyze query performance

### Common Pitfalls
- Avoid frequent updates on large collections
- Be cautious with unbounded array growth
- Use appropriate data types
- Understand document-level atomicity
- Implement proper error handling

## 7. Useful Tips
- Always use `explain()` to analyze query performance
- Implement proper indexing strategy
- Use `$limit` and `$project` to reduce result set
- Leverage aggregation pipeline for complex data transformations
- Regularly review and optimize database schema

