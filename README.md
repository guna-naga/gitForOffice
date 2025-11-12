MongoDB Cheat-Sheet for MERN
Developers
1. Basics & Setup
Topic Command/Info Notes
Start Mongo
shell
mongo (for community shell) Connects to local instance.
Switch/Create
DB
use <dbName> If database doesn’t exist, switching
creates it on first write.
Show current
DB
db Prints the name of current database.
List databases show dbs Shows all DBs with size > 0.
Drop database db.dropDatabase() Deletes current database and all
collections inside.
Show
collections
show collections Lists collections in current DB.
Create
collection
db.createCollection("<nam
e>", options)
Useful when you want explicit
creation with options.
Drop collection db.<collectionName>.drop(
)
Deletes the collection and all
documents.
2. Documents & Collections
Operation Syntax Notes
Insert one
document
db.users.insertOne({ name:
"Alice", age: 30 })
Creates a document in
users collection.
Insert many
documents
db.users.insertMany([{ name:"Bob"
}, { name:"Carol" }])
Bulk insert.
Find all docs db.users.find() Returns a cursor. In shell
you may need .pretty()
to format.
Find with filter db.users.find({ age: { $gt: 20 }
})
Filters docs where age > 20.
Find one
document
db.users.findOne({ name: "Alice"
})
Returns first matching
document (object).
Update one db.users.updateOne({ name:"Alice"
}, { $set: { age: 31 } })
Updates first match.
Update many db.users.updateMany({ age: { $lt:
18 } }, { $set: { minor:true } })
Bulk update.
Replace
document
db.users.replaceOne({ _id: someId
}, { name:"Alice", age:25 })
Replaces entire document
except _id.
Delete one db.users.deleteOne({ name:"Bob"
})
Deletes one.
Delete many db.users.deleteMany({ age: {
$gt:50 } })
Deletes all matching docs.
Count
documents
db.users.countDocuments({}) Returns count.
3. Mongoose & Node.js Usage
Concept Example Code Notes
Connect to
MongoDB
js mongoose.connect(process.env.MONGO_URI,
{ useNewUrlParser:true,
useUnifiedTopology:true })
Use environment
variable for URI.
Define
schema
js const userSchema = new mongoose.Schema({
name:String, age:Number, createdAt:{
type:Date, default:Date.now } });
Schema defines
structure.
Create
model
const User = mongoose.model("User",
userSchema);
User
corresponds to
users
collection.
Create
document
const doc = new User({ name:"Alice", age:30
}); await doc.save();
Use save()
method.
Find docs const users = await User.find({ age:{
$gte:20 } });
Returns array of
docs.
Find one by
id
const u = await User.findById(id); Shortcut for {
_id:id }.
Update doc await User.updateOne({ _id:id }, { $set:{
age:31 } });
Delete doc await User.deleteOne({ _id:id });
Populate
refs
Post.find().populate("author"); Joins referenced
document.
4. Indexes & Performance
● Create an index: db.users.createIndex({ age:1 })
● Compound index: db.orders.createIndex({ userId:1, createdAt:-1 })
● Unique index: db.users.createIndex({ email:1 }, { unique:true })
● View indexes: db.users.getIndexes()
● Explain plan: db.users.find({ age:30 }).explain("executionStats")
○ Helps analyze how query is executed.
5. Aggregation Framework
Stage Syntax Common Use
$match { $match: { age: { $gt: 25 } } } Filter docs.
$group { $group: { _id:"$status", total:{ $sum:1 }
} }
Group by field and
aggregate.
$proje
ct
{ $project: { name:1, year:{
$year:"$createdAt" } } }
Shape / compute
new fields.
$sort { $sort: { total:-1 } } Order results.
$limit { $limit: 10 } Restrict number of
docs.
$looku
p
{ $lookup: { from:"users",
localField:"userId", foreignField:"_id",
as:"userInfo" } }
Join collections.
Example full pipeline:
db.orders.aggregate([
{ $match:{ status:"completed" } },
{ $group:{ _id:"$userId", count:{ $sum:1 } } },
{ $sort:{ count:-1 } },
{ $limit:5 }
]);
6. Schema Design & Modeling for MERN
● Embed vs Reference: Use embedding when data is small and query brings entire
dataset; use reference when data is large or shared across collections.
● Use timestamps in schema: new mongoose.Schema({ … }, {
timestamps:true }) adds createdAt and updatedAt.
● Use lean queries: await User.find().lean() when you don’t need Mongoose
document methods — improves performance.
● Use validation: required:true, minlength, maxlength, enum in schema for safety.
● Avoid large arrays in documents; if arrays grow unbounded, prefer separate collections.
● Use TTL indexes for auto-deleting docs: db.sessions.createIndex({
"createdAt":1 }, { expireAfterSeconds:3600 }).
7. Security & Authentication for MERN
● Sanitize user input to prevent injection.
● Use bcrypt or argon2 to hash passwords before storing.
● Never store sensitive info in plain text in DB.
● Use express-session with MongoDB session store or JWT with cookie.
● For cookies: httpOnly:true, secure:true in production, sameSite:'none' if
cross-site.
● Use appropriate MongoDB user roles – either through Atlas or if self-hosted – restrict
privileges (avoid using root for app).
● Use parameterized queries and avoid exposing internal structure.
8. Backup, Replication & Atlas
● For production: Use mongodump and mongorestore for backups.
● Use replication sets for high availability.
● Use sharding for horizontal scale (if dataset is huge).
● In MongoDB Atlas: Create cluster, configure IP White-list, add database user, get
connection string. (mongodb.com docs)
● Monitor performance via Atlas Metrics and use Profiler if needed.
9. Common Interview / Coding Task Patterns
Task Quick Approach
Fetch recent N
records
Model.find().sort({ createdAt:-1 }).limit(N)
Pagination Model.find(filter).skip(pageSize*(page-1)).limit(pag
eSize)
Soft delete Add isDeleted:Boolean, deletedAt:Date. Filter
isDeleted:false in queries.
Bulk update
statuses
Model.updateMany({ status:"pending" }, { $set:{
status:"done", updatedAt:new Date() } })
Relationship
population
Use ref: in schema and .populate() when querying.
Aggregation for
analytics
Use .aggregate() pipeline to compute counts, sums, averages.
10. Environment Variables & URI
Example .env variables:
MONGO_URI=mongodb+srv://<username>:<password>@cluster0.mongodb.net/myD
B?retryWrites=true&w=majority
SESSION_SECRET=yourSecret
PORT=3000
Connection code:
mongoose.connect(process.env.MONGO_URI, {
useNewUrlParser:true,
useUnifiedTopology:true
}).then(() => console.log("MongoDB connected"))
.catch(err => console.error("MongoDB connection error:", err));
11. Quick Reference Table — Commands & Comparisons
Action Mongo Shell Mongoose (Node)
Find docs db.users.find({ age:30 }) User.find({ age:30 })
Insert doc db.users.insertOne({
name:"Alice" })
new User({ name:"Alice"
}).save()
Update
docs
db.users.updateOne({ _id:id }, {
$set:{ age:31 } })
User.updateOne({ _id:id
}, { age:31 })
Delete docs db.users.deleteMany({
isActive:false })
User.deleteMany({
isActive:false })
Aggregation db.orders.aggregate([ … ]) Order.aggregate([ … ])
12. Best Practices Checklist
● Use proper indexes on frequently queried fields.
● Monitor query performance (use .explain()).
● Keep your schema versioned / document migrations as app evolves.
● Sanitize and validate data before writing to DB.
● Use environment variables, never hard-code credentials.
● Use sessions or JWTs securely in MERN apps.
● Use connection pooling (Mongoose does by default).
● Keep production and development environments separated (different DBs).
● Document your schema with comments / README for collaboration.