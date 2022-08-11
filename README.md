# ASP.NET 6 REST API Tutorial | MongoDB Database
![image](https://user-images.githubusercontent.com/87810853/184068578-ab827756-fb33-4129-893b-76491984b8f8.png)

> Building a RESTful API service using Visual Studio 2022, ASP.NET 6 and MongoDB database.
## Create new ASP.NET Core Web API project
I named the project `EmployeeManagement`. While creating the new ASP.NET Core Web API project, select the following options on `Additional information` page.
+ .NET 6.0 as the Target Framework
+ Configure for HTTPS - Check the checkbox
+ Use controllers - Check the checkbox
+ Enable OpenAPI support - Check the checkbox

![image](https://user-images.githubusercontent.com/87810853/184073668-2f98fc81-d74b-4058-ba89-eab501c983fa.png)
We discussed What is `Swagger and OpenAPI specification` in detail in Parts 29 and 30 of Azure tutorial. If you are new to these concepts please check out these 2 videos from the following course page.
## Install MongoDB.Driver nuget package
![image](https://user-images.githubusercontent.com/87810853/184073844-27024d93-bb4f-4d82-aa9c-3b03e1dbb122.png)
## Map MongoDB to C# objects
Add `Models` folder and place the following `Employee.cs` class file in it. When student data is retrieved from Mongo, the JSON data is mapped to this `Student` class in .NET and vice-versa.
```C#
[BsonIgnoreExtraElements]
    public class Employee
    {
        [BsonId]
        [BsonRepresentation(BsonType.ObjectId)]
        public string Id { get; set; } = String.Empty;
        [BsonElement("Name")]
        public string Name { get; set; } = String.Empty;
        [BsonElement("Address")]
        public string Address { get; set; } = String.Empty;
        [BsonElement("Phone")]
        public string Phone { get; set; } = String.Empty;
        [BsonElement("Email")]
        public string Email { get; set; } = String.Empty;
    }
```
+ `[BsonId]` attribute specifies that this is the Id field or property. In this example, the property Id maps to _id field in Mongo document.
+ `[BsonRepresentation]` attribute automatically converts Mongo data type to a .Net data type and vice-versa. In this example, Mongo data type `ObjectId` is automatically converted to string datatype and vice-versa.
+ `Name` property is decorated with the `[BsonElement]` attribute. So this means, `Name` property corresponds to `name` field in Mongo document. So, `[BsonElement]` attribute specifies the field in the Mongo document the decorated property corresponds to.
+ The property names (`Name`, `Address`, `Phone`, `Email`) have the same name as the fields in the Mongo document (`Name`, `Address`, `Phone`, `Email`). However the casing is different. In C# the properties start with an uppercase letter whereas in Mongo the field starts with lowercase. There are several approaches to handle this case sensitive mapping. One of the easiest and cleanest approaches is to use [BsonElement] attribute.
+ What to do if the JSON document in MongoDB contains more fields than the properties in the corresponding C# class? Well, we can use `[BsonIgnoreExtraElements]` attribute and instruct the serializer to ignore the extra elements.
All the following attributes are present in `MongoDB.Bson.Serialization.Attributes`
+ BsonId
+ BsonElement
+ BsonRepresentation
+ BsonIgnoreExtraElements
## MongoDB connection string in ASP.NET Core
Store MongoDB connection information in `appsettings.json` file.
```C#
{
  "EmployeeDatabaseSetting": {
    "EmployeesCollectionName": "Employees",
    "ConnectionString": "mongodb+srv://<username>:<password>@cluster0.jscfz7b.mongodb.net/test",
    "DatabaseName": "EmployeeDB"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*"
}
```
## Read MongoDB connection information

![image](https://user-images.githubusercontent.com/87810853/184075019-302a7825-cf25-4380-9e7b-aace54deab95.png)

Add the following  `IStudentStoreDatabaseSettings.cs` files in the Models folder. This interface and the class provide strongly typed access to MongoDB connection information.

### IStudentStoreDatabaseSettings.cs
```C#
 public class EmployeeDatabasesetting : IEmployeeDatabaseSettings
    {
        public string EmployeesCollectionName { get; set; } = String.Empty;
        public string ConnectionString { get; set; } = String.Empty;
        public string DatabaseName { get; set; } = String.Empty;
    }
    public interface IEmployeeDatabaseSettings
    {
        public string EmployeesCollectionName { get; set; }
        public string ConnectionString { get; set; }
        public string DatabaseName { get; set; }
    }
```
