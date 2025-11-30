#7 - MONGODB

Show dbs
db 
use SupermarketDB;
show collections;

db.createCollection("product");
db.createCollection("customer");
db.createCollection("Saletable");

db.product.insertMany([{PID: 101, Productname:"Intel"},{PID: 102, Productname:"AMD"},{PID: 103, Productname:"Nvidia"}]);
db.customer.insertMany([{CID: 101, Customername:"Pooja"},{CID: 102, Customername:"Arjun"},{CID: 103, Customername:"Tanu"}]);

show collections;
var p = db.product.find().toArray();
var c = db.customer.find().toArray();
console.table(p);
console.table(c);

db.product.updateOne({PID: 101}, {$set: {Productname:'Nvidia'}});
db.customer.remove({CID:103});
#or db.customer.deleteOne({CID:103});

var p = db.product.find().toArray();
var c = db.customer.find().toArray();
console.table(p);
console.table(c);

show collections;
db.Salestable.drop();

show collections;

cls
db.dropDatabase();
