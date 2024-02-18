### ODM & ORM

> [!tip] ODM & ORM
>
> - ORM: Object Relational Mapper(use with SQL databases)
> - ODM: Object Document Mapper (use with NoSQL databases)
> - Mongoose is an ODM for MongoDB

> [!warning] steps to connect to MongoDB (Mongoose)\
>
> 1.  intialize the connection to db server (connectionString,url, port, DB name) one time
> 2.  Model (Schema for each entity)
> 3.  Controller (Model CRUD )

> [!note] Mongoose
>
> - install mongoose
>
> ```bash
> npm install mongoose
> ```

> [!danger] start the db server
> `mongod`

> [!warning] use `127.0.0.1` instead of `localhost` in the connection string
>
> - connection string: `mongodb://127.0.0.1:27017/DBName`
>   > [!danger] this creates a new database if it does not exist
> - if the database exists, we have to write the name of the database with the same case

```js
//app.js
const mongoose = require("mongoose");

mongoose
  .connect("mongodb://mongodb://127.0.0.1:27017/pdDB") //returns a promise
  .then(() => {
    console.log("Connected to the database");
    //listen to the server only after connecting to the database has been successful
    server.listen(8080, () => {
      console.log("Server is running on port 8080");
    });
  })
  .catch((error) => {
    console.log("Error connecting to the database", error);
  });
```

> [!info] create Schema
>
> - inside the `model` folder, create a file for each entity

```js
//model/departmentSchema.js
const mongoose = require("mongoose");

// 1. create object from mongoose schema
const Schema = mongoose.Schema({
  _id: Number, ///can be ObjectId (in lab)
  // name: String,
  // we can add validation
  name: {
    type: String,
    unique: true, //this creates an index(to remove the index, we have to do it manually in the database)
  },
});

// 2. mappping the schema to a collection
module.exports = mongoose.model("department", Schema);
//is the same as (collectionName is the plural of the model name)
module.exports = mongoose.model("departments", Schema);
```

```js
//model/studentSchema.js
const mongoose = require("mongoose");

const Schema = mongoose.Schema({
  _id: Number,
  name: String,
  password: String,
  department: {
    type: Number,
    ref: "departments", //reference to the department collection name
  },
  //this automatically references _id of the department collection
});

module.exports = mongoose.model("students", Schema);
```

> [!danger] 1 to 1 relationship
>
> - how to write the schema for a 1 to 1 relationship

```js
//controller/studentController.js
const Student = require("../model/studentSchema");
//we can only require the model once as it is a singleton (mongoose)
// require("../model/studentSchema");

exports.getAllStudents = (req, res,next) => {
//   Student.find()//is the same as Student.find({})

  Student.find({})//returns a promise
    .then((students) => {
      res.status(200).json(students);
    })
    .catch((error) => {
      next(error);
    });
};
exports.insertStudent = (req, res,next) => {
  //create object from Student Schema
  const student = new Student({
    _id: 1,//doen't auto increment (Number)
    name: "Ahmed",
    password: "123",
    department: 1,
    });
    //get data from the request
    // const student = new Student(req.body);
    student
      .save()//returns a promise
      .then((student) => {
        res.status(201).json({
          message: "Student added successfully",
          student});
      })
      .catch((error) => {
        next(error);
      });
};

exports.getStudentById = (req, res,next) => {
  const id = req.params.id;
  Student.findById({ _id: id })//returns a promise
    .then((student) => {
        //we have to check if the student exists
        if (!student) 
        throw new Error("Id does not exist");//this will be caught by catch block
      res.status(200).json(student);
    })
    .catch((error) => {
      next(error);//this will be caught by the error middleware
    });
};
```
>[!danger] `ref` does not force us to add a department that exists in the department collection
> - it is use when returning the data from the database we can populate the department field with the department data

>[!bug] `_id` not `id`


###### departmentController.js

```js
//controller/departmentController.js
const Department = require("../model/departmentSchema");

exports.getAllDepartments = (req, res,next) => {
  Department.find({})//returns a promise
    .then((departments) => {
      res.status(200).json(departments);
    })
    .catch((error) => {
      next(error);
    });
};
exports.insertDepartment = (req, res,next) => {
  const department = new Department(req.body);
  department
    .save()//returns a promise
    .then((department) => {
      res.status(201).json({
        message: "Department added successfully",
        department,
      });
    })
    .catch((error) => {
      next(error);
    });
};

```

>[!tip] `populate` method
> - to get the department data when getting the student data

```js
//controller/studentController.js
exports.getAllStudents = (req, res,next) => {
  Student.find({})//returns a promise
  //.populate(path, select) path is the field to populate, select is the fields to return
  .populate({ path: "department"})// this will return the department data for each student instead of the department id
  .populate({ path: "department", select: { name: 1 } })
  // this will return the department name for each student(_id by default is returned)
    
  //this will return null if the department does not exist
    .then((students) => {
      res.status(200).json(students);
    })
    .catch((error) => {
      next(error);
    });
};
```
>[!bug] 1 to 1 relationship
> - create a schema for address instead of adding the address fields to the student schema
> - as address schema can be used in other entities


```js
//model/studentSchema.js
const mongoose = require("mongoose");

const addresSchema = mongoose.Schema({
  city: String,
  street: String,
  building: Number,
  //use options to not create an _id for the address
},{ _id: false });

const Schema = mongoose.Schema({
  _id: Number,
  name: String,
  password: String,
  department: {
    type: Number,
    ref: "departments", //reference to the department collection name
  },
  address:addresSchema
});

module.exports = mongoose.model("students", Schema);
```
>[!danger] embedding objects better to be added as subdocuments