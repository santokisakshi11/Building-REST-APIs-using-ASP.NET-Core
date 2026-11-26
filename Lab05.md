# Lab 5: Working with HTTP Status Codes

## 1. Aim

To implement appropriate HTTP status codes in a Student Management API using ASP.NET Core Web API.

We will use the following status codes:

1. `200 OK` – For successful GET requests.
2. `201 Created` – For successfully creating a student.
3. `204 No Content` – For successfully updating or deleting a student.
4. `400 Bad Request` – For invalid input.
5. `404 Not Found` – When a student does not exist.
6. `409 Conflict` – When a duplicate student record is added.

---

## 2. Practical Statement

Return the appropriate HTTP status code for every endpoint of the Student Management API:

| HTTP Status Code | Meaning | Used For |
|---|---|---|
| 200 OK | Request successful | GET |
| 201 Created | Resource created successfully | POST |
| 204 No Content | Request successful, no response body | PUT and DELETE |
| 400 Bad Request | Invalid input | Invalid student details |
| 404 Not Found | Resource not found | Non-existing student |
| 409 Conflict | Conflict with existing data | Duplicate student ID |

---

## 3. What is an HTTP Status Code?

An HTTP Status Code is a three-digit number returned by the server to tell the client whether a request was successful or unsuccessful.

Whenever a client sends a request to an API, the server processes it and returns a response.

The response contains:

- Status code
- Response data (if applicable)
- Other response information

### Example

Suppose we want to get all students.

Request:

`GET /api/Student`

If the request is successful, the server returns:

`200 OK`

This means that the request was processed successfully.

### Why do we use HTTP Status Codes?

1. To understand whether an API request was successful.
2. To identify errors.
3. To inform the client about the result.
4. To make API responses meaningful.
5. To help frontend applications handle responses correctly.

---

# 4. Important HTTP Methods

Before implementing status codes, understand the four main HTTP methods.

| HTTP Method | Operation | Meaning |
|---|---|---|
| GET | Read | Fetch student information |
| POST | Create | Add a new student |
| PUT | Update | Modify student information |
| DELETE | Delete | Remove a student |

### Example

Suppose our student records are:

| ID | Name | Age |
|---:|---|---:|
| 1 | Sakshi | 20 |
| 2 | Lipsa | 21 |
| 3 | Daxa | 22 |

We can perform the following operations:

- GET: Display all students.
- POST: Add a new student.
- PUT: Update a student's details.
- DELETE: Remove a student.

---

# 5. Understanding HTTP Status Codes

## 5.1 200 OK

`200 OK` means that the request was successful.

It is commonly used for GET requests.

Example:

```csharp
return Ok(Students);
```

This returns all students with status code 200.

## 5.2 201 Created

`201 Created` means that a new resource has been successfully created.

It is commonly used for POST requests.

Example:

```csharp
return CreatedAtAction(nameof(GetById), new { id = student.id }, student);
```

This returns status code 201 and provides a location for retrieving the newly created student.

## 5.3 204 No Content

`204 No Content` means that the request was successful, but the server does not return any response body.

It is commonly used for PUT and DELETE requests.

Example:

```csharp
return NoContent();
```

## 5.4 400 Bad Request

`400 Bad Request` means that the client has sent invalid data.

Example:

```csharp
if (student == null || student.age <= 0)
{
    return BadRequest("Invalid student details.");
}
```

If the age is 0 or negative, the API returns 400.

## 5.5 404 Not Found

`404 Not Found` means that the requested resource does not exist.

Example:

```csharp
if (student == null)
{
    return NotFound("Student not found.");
}
```

If a student with ID 10 does not exist, the API returns 404.

## 5.6 409 Conflict

`409 Conflict` means that the request conflicts with existing data.

Example:

```csharp
if (Students.Any(x => x.id == student.id))
{
    return Conflict("Student ID already exists.");
}
```

If we try to add a student with an ID that already exists, the API returns 409.

---

# 6. Project Structure

Create the following structure:

```text
Lab5
│
├── Controllers
│   └── StudentController.cs
│
├── Models
│   └── Student.cs
│
├── Program.cs
├── appsettings.json
└── Lab5.csproj
```

### Explanation

- `Controllers`: Stores API controller classes.
- `StudentController.cs`: Contains student API action methods.
- `Models`: Stores model classes.
- `Student.cs`: Defines student information.
- `Program.cs`: Configures and starts the Web API application.

---

# 7. Implementation

## Step 1: Create Student Model

Create a folder named `Models`.

Inside it, create a class named `Student.cs`.

### Student.cs

```csharp
namespace Lab5.Models
{
    public class Student
    {
        public int id { get; set; }

        public string name { get; set; }

        public int age { get; set; }
    }
}
```

### Explanation

**1. id**

```csharp
public int id { get; set; }
```

Stores the unique ID of the student.

**2. name**

```csharp
public string name { get; set; }
```

Stores the name of the student.

**3. age**

```csharp
public int age { get; set; }
```

Stores the age of the student.

### Example

```csharp
Student s = new Student
{
    id = 1,
    name = "Sakshi",
    age = 20
};
```

This creates a student object with ID 1, name Sakshi, and age 20.

---

## Step 2: Create Student Controller

Create a folder named `Controllers`.

Inside it, create a class named `StudentController.cs`.

### StudentController.cs – Complete Code

The following version implements all the status-code requirements from the practical statement.

```csharp
using Lab5.Models;
using Microsoft.AspNetCore.Mvc;

namespace Lab5.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class StudentController : ControllerBase
    {
        private static readonly List<Student> Students = new List<Student>
        {
            new Student { id = 1, name = "Sakshi", age = 20 },
            new Student { id = 2, name = "Lipsa", age = 21 },
            new Student { id = 3, name = "Daxa", age = 22 }
        };

        // GET: Get all students
        [HttpGet]
        public IActionResult GetAll()
        {
            return Ok(Students);
        }

        // GET: Get student by ID
        [HttpGet("{id}")]
        public IActionResult GetById(int id)
        {
            var student = Students.FirstOrDefault(x => x.id == id);

            if (student == null)
            {
                return NotFound("Student not found.");
            }

            return Ok(student);
        }

        // POST: Add new student
        [HttpPost]
        public IActionResult Add(Student student)
        {
            if (student == null || student.id <= 0 ||
                string.IsNullOrWhiteSpace(student.name) ||
                student.age <= 0)
            {
                return BadRequest("Invalid student details.");
            }

            if (Students.Any(x => x.id == student.id))
            {
                return Conflict("Student ID already exists.");
            }

            Students.Add(student);

            return CreatedAtAction(
                nameof(GetById),
                new { id = student.id },
                student
            );
        }

        // PUT: Update student
        [HttpPut("{id}")]
        public IActionResult Update(int id, Student updatedStudent)
        {
            if (updatedStudent == null ||
                updatedStudent.id != id ||
                string.IsNullOrWhiteSpace(updatedStudent.name) ||
                updatedStudent.age <= 0)
            {
                return BadRequest("Invalid student details.");
            }

            var student = Students.FirstOrDefault(x => x.id == id);

            if (student == null)
            {
                return NotFound("Student not found.");
            }

            student.name = updatedStudent.name;
            student.age = updatedStudent.age;

            return NoContent();
        }

        // DELETE: Delete student
        [HttpDelete("{id}")]
        public IActionResult Delete(int id)
        {
            var student = Students.FirstOrDefault(x => x.id == id);

            if (student == null)
            {
                return NotFound("Student not found.");
            }

            Students.Remove(student);

            return NoContent();
        }
    }
}
```

---

# 8. Explanation of Controller Code

## 8.1 Using Statements

```csharp
using Lab5.Models;
using Microsoft.AspNetCore.Mvc;
```

- `Lab5.Models` allows us to use the Student class.
- `Microsoft.AspNetCore.Mvc` provides controller-related classes and methods.

## 8.2 Route Attribute

```csharp
[Route("api/[controller]")]
```

Defines the base URL of the controller.

Our controller name is `StudentController`.

Therefore, the base URL becomes:

`/api/Student`

## 8.3 ApiController Attribute

```csharp
[ApiController]
```

Identifies the class as an API controller and enables API-specific behaviours, including automatic model validation responses.

## 8.4 ControllerBase

```csharp
public class StudentController : ControllerBase
```

`ControllerBase` provides methods such as:

- `Ok()`
- `CreatedAtAction()`
- `NoContent()`
- `BadRequest()`
- `NotFound()`
- `Conflict()`

These methods help us return HTTP responses.

## 8.5 Static List

```csharp
private static readonly List<Student> Students = new List<Student>
{
    new Student { id = 1, name = "Sakshi", age = 20 },
    new Student { id = 2, name = "Lipsa", age = 21 },
    new Student { id = 3, name = "Daxa", age = 22 }
};
```

This creates a list containing three student records.

- `private`: The list can be accessed inside the controller class.
- `static`: The list belongs to the class.
- `readonly`: The list variable cannot be assigned a different list after initialization. Its contents can still be changed.

**Note:** This is temporary in-memory storage. Data is not permanently saved in a database.

---

# 9. Explanation of All API Endpoints

## API 1: Get All Students

```csharp
[HttpGet]
public IActionResult GetAll()
{
    return Ok(Students);
}
```

### Explanation

- `[HttpGet]` handles GET requests.
- `GetAll()` is the action method.
- `Ok(Students)` returns all student records.
- The response status code is 200.

### API URL

`GET https://localhost:<port>/api/Student`

### Expected Output

```json
[
  {
    "id": 1,
    "name": "Sakshi",
    "age": 20
  },
  {
    "id": 2,
    "name": "Lipsa",
    "age": 21
  },
  {
    "id": 3,
    "name": "Daxa",
    "age": 22
  }
]
```

<text>Expected status code: **200 OK**</text>

## API 2: Get Student By ID

```csharp
[HttpGet("{id}")]
public IActionResult GetById(int id)
{
    var student = Students.FirstOrDefault(x => x.id == id);

    if (student == null)
    {
        return NotFound("Student not found.");
    }

    return Ok(student);
}
```

### Explanation

**1. Route Parameter**

```csharp
[HttpGet("{id}")]
```

The ID is received through the URL.

Example:

`/api/Student/2`

**2. FirstOrDefault()**

```csharp
var student = Students.FirstOrDefault(x => x.id == id);
```

Searches for the student whose ID matches the given ID.

**3. Not Found Condition**

```csharp
if (student == null)
{
    return NotFound("Student not found.");
}
```

If the student does not exist, the API returns 404.

**4. Return Student**

```csharp
return Ok(student);
```

If the student exists, the API returns the student with status code 200.

### API URL

`GET https://localhost:<port>/api/Student/2`

### Expected Output

```json
{
  "id": 2,
  "name": "Lipsa",
  "age": 21
}
```

Expected status code: **200 OK**

### When Student Does Not Exist

Request:

`GET https://localhost:<port>/api/Student/10`

Response:

```text
Student not found.
```

Expected status code: **404 Not Found**

## API 3: Add New Student

```csharp
[HttpPost]
public IActionResult Add(Student student)
{
    if (student == null || student.id <= 0 ||
        string.IsNullOrWhiteSpace(student.name) ||
        student.age <= 0)
    {
        return BadRequest("Invalid student details.");
    }

    if (Students.Any(x => x.id == student.id))
    {
        return Conflict("Student ID already exists.");
    }

    Students.Add(student);

    return CreatedAtAction(
        nameof(GetById),
        new { id = student.id },
        student
    );
}
```

### Explanation

**1. HTTP POST**

```csharp
[HttpPost]
```

POST is used to add a new student.

**2. Validate Student Details**

```csharp
if (student == null || student.id <= 0 ||
    string.IsNullOrWhiteSpace(student.name) ||
    student.age <= 0)
{
    return BadRequest("Invalid student details.");
}
```

Checks whether the input is valid.

The API returns 400 if:

- Student object is null.
- Student ID is 0 or negative.
- Student name is empty or contains only spaces.
- Student age is 0 or negative.

**3. Check Duplicate ID**

```csharp
if (Students.Any(x => x.id == student.id))
{
    return Conflict("Student ID already exists.");
}
```

`Any()` checks whether at least one student has the given ID.

If the ID already exists, the API returns 409 Conflict.

**4. Add Student**

```csharp
Students.Add(student);
```

Adds the new student to the list.

**5. Return CreatedAtAction**

```csharp
return CreatedAtAction(
    nameof(GetById),
    new { id = student.id },
    student
);
```

Returns status code 201 Created.

It also provides the location of the newly created student's GET endpoint.

### API URL

`POST https://localhost:<port>/api/Student`

### Request Body

Select JSON as the body format in your API testing tool.

```json
{
  "id": 4,
  "name": "Rahul",
  "age": 23
}
```

### Expected Response

```json
{
  "id": 4,
  "name": "Rahul",
  "age": 23
}
```

Expected status code: **201 Created**

### Duplicate Student Example

Try adding this student again:

```json
{
  "id": 4,
  "name": "Rahul",
  "age": 23
}
```

Because ID 4 already exists, the API returns:

```text
Student ID already exists.
```

Expected status code: **409 Conflict**

### Invalid Input Example

```json
{
  "id": 5,
  "name": "",
  "age": -2
}
```

Expected status code: **400 Bad Request**

## API 4: Update Student

```csharp
[HttpPut("{id}")]
public IActionResult Update(int id, Student updatedStudent)
{
    if (updatedStudent == null ||
        updatedStudent.id != id ||
        string.IsNullOrWhiteSpace(updatedStudent.name) ||
        updatedStudent.age <= 0)
    {
        return BadRequest("Invalid student details.");
    }

    var student = Students.FirstOrDefault(x => x.id == id);

    if (student == null)
    {
        return NotFound("Student not found.");
    }

    student.name = updatedStudent.name;
    student.age = updatedStudent.age;

    return NoContent();
}
```

### Explanation

**1. HTTP PUT**

```csharp
[HttpPut("{id}")]
```

PUT is used to update an existing student.

**2. Validate Input**

```csharp
if (updatedStudent == null ||
    updatedStudent.id != id ||
    string.IsNullOrWhiteSpace(updatedStudent.name) ||
    updatedStudent.age <= 0)
{
    return BadRequest("Invalid student details.");
}
```

Checks whether the request contains valid student details.

The ID in the URL must match the ID in the request body.

If the details are invalid, it returns 400.

**3. Find Student**

```csharp
var student = Students.FirstOrDefault(x => x.id == id);
```

Searches for the student using the given ID.

**4. Check Student**

```csharp
if (student == null)
{
    return NotFound("Student not found.");
}
```

If the student does not exist, it returns 404.

**5. Update Details**

```csharp
student.name = updatedStudent.name;
student.age = updatedStudent.age;
```

Updates the student's name and age.

**6. Return NoContent**

```csharp
return NoContent();
```

Returns status code 204.

It means that the update was successful, but no response body is returned.

### API URL

`PUT https://localhost:<port>/api/Student/1`

### Request Body

```json
{
  "id": 1,
  "name": "Sakshi Updated",
  "age": 21
}
```

Expected status code: **204 No Content**

### Invalid Update Example

If the URL contains ID 1 but the request body contains ID 2, the API returns:

**400 Bad Request**

### Student Does Not Exist

Request:

`PUT https://localhost:<port>/api/Student/10`

If the request details are valid but student 10 does not exist, the API returns:

**404 Not Found**

## API 5: Delete Student

```csharp
[HttpDelete("{id}")]
public IActionResult Delete(int id)
{
    var student = Students.FirstOrDefault(x => x.id == id);

    if (student == null)
    {
        return NotFound("Student not found.");
    }

    Students.Remove(student);

    return NoContent();
}
```

### Explanation

**1. HTTP DELETE**

```csharp
[HttpDelete("{id}")]
```

DELETE is used to remove a student.

**2. Find Student**

```csharp
var student = Students.FirstOrDefault(x => x.id == id);
```

Searches for the student using the given ID.

**3. Check Student**

```csharp
if (student == null)
{
    return NotFound("Student not found.");
}
```

If the student does not exist, it returns 404.

**4. Remove Student**

```csharp
Students.Remove(student);
```

Removes the student from the list.

**5. Return NoContent**

```csharp
return NoContent();
```

Returns status code 204.

### API URL

`DELETE https://localhost:<port>/api/Student/3`

Expected status code: **204 No Content**

### If Student Does Not Exist

Request:

`DELETE https://localhost:<port>/api/Student/10`

Expected status code: **404 Not Found**

---

# 10. API Endpoint Summary

| No. | HTTP Method | Endpoint | Success Status |
|---|---|---|---|
| 1 | GET | `/api/Student` | 200 OK |
| 2 | GET | `/api/Student/{id}` | 200 OK |
| 3 | POST | `/api/Student` | 201 Created |
| 4 | PUT | `/api/Student/{id}` | 204 No Content |
| 5 | DELETE | `/api/Student/{id}` | 204 No Content |

## Error Status Code Summary

| Status Code | Condition |
|---|---|
| 400 Bad Request | Invalid student details |
| 404 Not Found | Student ID does not exist |
| 409 Conflict | Student ID already exists |

---

# 11. Important Difference Between Status Codes

| Status Code | Simple Meaning | Example |
|---|---|---|
| 200 | Everything is successful and data is returned | Fetch students |
| 201 | New record created | Add student |
| 204 | Operation successful, no response body | Update or delete student |
| 400 | Input is invalid | Negative age |
| 404 | Record does not exist | Get student ID 10 |
| 409 | Record conflicts with existing data | Duplicate ID |

### Easy Way to Remember

- 200 = Successful request.
- 201 = Created.
- 204 = Successful, no content.
- 400 = Wrong input.
- 404 = Not found.
- 409 = Conflict or duplicate.

---

# 16. Conclusion

In this practical, we created a Student Management API using ASP.NET Core Web API.

We implemented the five main CRUD endpoints:

1. GET – Fetch all students.
2. GET – Fetch a student by ID.
3. POST – Add a new student.
4. PUT – Update student information.
5. DELETE – Remove a student.

We also learned how to return appropriate HTTP status codes such as 200, 201, 204, 400, 404, and 409.

**Result:** Successfully implemented Student Management API endpoints with appropriate HTTP status codes for successful requests, invalid input, missing students, and duplicate records.

---
