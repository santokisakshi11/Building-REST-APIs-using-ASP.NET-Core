# Lab 4: Create Controllers and Action Methods and Implement CRUD APIs

## 1. Aim

To create Student and Subject model classes, create a Student Controller, and implement API action methods to:

1. Fetch all students.
2. Fetch a single student using its ID.
3. Fetch all subjects of a particular student.

We will store student records in a static in-memory `List<Student>`.

---

## 2. Practical Statement

1. Create a `Student` class with a list of `Subject` objects.
2. Create a `Subject` class.
3. Pre-load sample student records with their subjects in a static in-memory `List<Student>`.
4. Create `StudentController` with the following endpoints:
   - Fetch all students.
   - Fetch a single student by ID (return 404 if not found).
   - Fetch subjects of a particular student.

## 3. What is an API?

API stands for **Application Programming Interface**.

An API allows two applications to communicate with each other.

For example:

- A frontend application sends a request to the backend.
- The backend processes the request.
- The backend sends a response to the frontend.

In our practical:

- The client sends a request to the Student API.
- The Student Controller receives the request.
- The controller searches for student information.
- The API returns the requested student data.

### Example

Suppose we want to fetch all students.

The client sends:

`GET /api/Student`

The API returns a list of students in JSON format.

---

## 4. Important Concepts

### 4.1 What is a Controller?

A Controller is a class that handles incoming HTTP requests and sends responses.

In our practical, `StudentController` handles requests related to students.

Example:

```csharp
public class StudentController : ControllerBase
{
}
```

### 4.2 What is an Action Method?

An action method is a method inside a controller that handles a particular API request.

Example:

```csharp
[HttpGet]
public IActionResult GetAllStudent()
{
    return Ok(students);
}
```

This action method returns all students.

### 4.3 What is Model?

A Model represents the data or structure of an application.

We create two model classes:

1. Student
2. Subject

### 4.4 What is List?

A List is a collection that can store multiple objects.

Example:

```csharp
List<Student> students = new List<Student>();
```

This list can store multiple Student objects.

### 4.5 What is In-Memory Storage?

In-memory storage means storing data temporarily in the application's memory instead of saving it in a database.

In this practical, we use:

```csharp
static List<Student> students = new();
```

**Important:** Data stored in this list is not permanently saved. It can be lost when the application restarts.

### 4.6 What is IActionResult?

`IActionResult` is a return type used by controller action methods.

It allows us to return different HTTP responses.

Common responses:

| Method | Meaning | Status Code |
|---|---|---|
| `Ok()` | Request successful | 200 |
| `NotFound()` | Requested data not found | 404 |
| `BadRequest()` | Invalid request | 400 |
| `NoContent()` | Successful request with no response body | 204 |

### 4.7 What is HTTP GET?

GET is an HTTP method used to retrieve or read data from the server.

Example:

```csharp
[HttpGet]
public IActionResult GetAllStudent()
{
    return Ok(students);
}
```

It fetches student records without modifying them.

---

## 5. Project Structure

Create the following structure in your ASP.NET Core Web API project:

```text
Lab4
│
├── Controllers
│   └── StudentController.cs
│
├── Models
│   ├── Student.cs
│   └── Subject.cs
│
├── Program.cs
├── appsettings.json
└── Lab4.csproj
```

**Explanation:**

- `Controllers`: Contains controller classes and API action methods.
- `Models`: Contains the data classes.
- `StudentController.cs`: Handles student-related API requests.
- `Student.cs`: Defines student information.
- `Subject.cs`: Defines subject information.
- `Program.cs`: Configures and starts the Web API application.

---

# 6. Implementation

## Step 1: Create Student Model

Create a folder named `Models`.

Inside it, create a class named `Student.cs`.

### Student.cs

```csharp
namespace Lab4.Models
{
    public class Student
    {
        public int Id { get; set; }

        public string Name { get; set; }

        public List<Subject> Subjects { get; set; }
    }
}
```

### Explanation

**1. Id**

```csharp
public int Id { get; set; }
```

Stores the unique ID of a student.

**2. Name**

```csharp
public string Name { get; set; }
```

Stores the student's name.

**3. Subjects**

```csharp
public List<Subject> Subjects { get; set; }
```

Stores multiple Subject objects belonging to one student.

For example, one student can have:

- DBMS
- Java
- C#
- Computer Networks

This is an example of one Student object containing multiple Subject objects.

---

## Step 2: Create Subject Model

Inside the `Models` folder, create a class named `Subject.cs`.

### Subject.cs

```csharp
namespace Lab4.Models
{
    public class Subject
    {
        public int SubjectId { get; set; }

        public string SubjectName { get; set; }

        public int Marks { get; set; }
    }
}
```

### Explanation

**1. SubjectId**

Stores the unique ID of a subject.

**2. SubjectName**

Stores the name of the subject.

**3. Marks**

Stores the marks obtained by the student in that subject.

Example:

| SubjectId | SubjectName | Marks |
|---|---|---:|
| 1 | DBMS | 98 |
| 2 | Java | 95 |

---

## Step 3: Create Student Controller

Create a folder named `Controllers`.

Inside it, create a class named `StudentController.cs`.

### StudentController.cs

```csharp
using Lab4.Models;
using Microsoft.AspNetCore.Mvc;

namespace Lab4.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class StudentController : ControllerBase
    {
        static List<Student> students = new()
        {
            new Student()
            {
                Id = 1,
                Name = "Sakshi",
                Subjects = new List<Subject>
                {
                    new Subject { SubjectName = "DBMS-I", Marks = 98 },
                    new Subject { SubjectName = "DBMS-II", Marks = 98 }
                }
            },

            new Student()
            {
                Id = 2,
                Name = "Lipsa",
                Subjects = new List<Subject>
                {
                    new Subject { SubjectName = "Java", Marks = 98 },
                    new Subject { SubjectName = "JavaScript", Marks = 98 }
                }
            },

            new Student()
            {
                Id = 3,
                Name = "Daxa",
                Subjects = new List<Subject>
                {
                    new Subject { SubjectName = "C", Marks = 98 },
                    new Subject { SubjectName = "C#", Marks = 98 }
                }
            }
        };

        // Get all students
        [HttpGet]
        public IActionResult GetAllStudent()
        {
            return Ok(students);
        }

        // Get student by ID
        [HttpGet("{id}")]
        public IActionResult GetStudentById(int id)
        {
            var student = students.FirstOrDefault(s => s.Id == id);

            if (student == null)
            {
                return NotFound("Student not found");
            }

            return Ok(student);
        }

        // Get subjects of a student
        [HttpGet("{id}/subjects")]
        public IActionResult GetStudentSubjects(int id)
        {
            var student = students.FirstOrDefault(s => s.Id == id);

            if (student == null)
            {
                return NotFound("Student not found");
            }

            return Ok(student.Subjects);
        }
    }
}
```

### Explanation of Controller Code

#### 1. Using Statements

```csharp
using Lab4.Models;
using Microsoft.AspNetCore.Mvc;
```

- `Lab4.Models`: Allows us to use Student and Subject classes.
- `Microsoft.AspNetCore.Mvc`: Provides controller and API-related classes.

#### 2. Route Attribute

```csharp
[Route("api/[controller]")]
```

Defines the URL of the controller.

Here, the controller name is `StudentController`.

ASP.NET Core removes the word `Controller` from the class name.

Therefore, the base URL becomes:

`/api/Student`

#### 3. ApiController Attribute

```csharp
[ApiController]
```

Identifies the class as an API controller and enables API-specific behaviours such as automatic model validation responses.

#### 4. ControllerBase

```csharp
public class StudentController : ControllerBase
```

`ControllerBase` provides methods such as:

- `Ok()`
- `NotFound()`
- `BadRequest()`

These methods help us return HTTP responses.

#### 5. Static List

```csharp
static List<Student> students = new()
```

This creates a static list of Student objects.

**Why static?**

The list belongs to the controller class rather than an individual controller object. It can be accessed by the action methods without creating a new list for each method.

#### 6. Creating Student Objects

```csharp
new Student()
{
    Id = 1,
    Name = "Sakshi",
    Subjects = new List<Subject>
    {
        new Subject { SubjectName = "DBMS-I", Marks = 98 },
        new Subject { SubjectName = "DBMS-II", Marks = 98 }
    }
}
```

Here:

- We create one Student object.
- We assign its ID.
- We assign its name.
- We create a list of subjects for that student.
- We add two Subject objects to the list.

This is called using a list of objects inside another object.

---

# 7. Understanding the Three API Action Methods

## API 1: Get All Students

```csharp
[HttpGet]
public IActionResult GetAllStudent()
{
    return Ok(students);
}
```

### Explanation

- `[HttpGet]` indicates that this method handles GET requests.
- `GetAllStudent()` is the action method.
- `Ok(students)` returns the complete student list.
- HTTP status code 200 means the request was successful.

**API URL:**

`GET https://localhost:<port>/api/Student`

### Expected Output

```json
[
  {
    "id": 1,
    "name": "Sakshi",
    "subjects": [
      {
        "subjectId": 0,
        "subjectName": "DBMS-I",
        "marks": 98
      },
      {
        "subjectId": 0,
        "subjectName": "DBMS-II",
        "marks": 98
      }
    ]
  },
  {
    "id": 2,
    "name": "Lipsa",
    "subjects": [
      {
        "subjectId": 0,
        "subjectName": "Java",
        "marks": 98
      },
      {
        "subjectId": 0,
        "subjectName": "JavaScript",
        "marks": 98
      }
    ]
  },
  {
    "id": 3,
    "name": "Daxa",
    "subjects": [
      {
        "subjectId": 0,
        "subjectName": "C",
        "marks": 98
      },
      {
        "subjectId": 0,
        "subjectName": "C#",
        "marks": 98
      }
    ]
  }
]
```

**Note:** `SubjectId` appears as 0 because the provided code does not assign values to it. The integer property gets its default value, which is 0.

## API 2: Get Student By ID

```csharp
[HttpGet("{id}")]
public IActionResult GetStudentById(int id)
{
    var student = students.FirstOrDefault(s => s.Id == id);

    if (student == null)
    {
        return NotFound("Student not found");
    }

    return Ok(student);
}
```

### Explanation

**1. Route Parameter**

```csharp
[HttpGet("{id}")]
```

The `{id}` represents a value received through the URL.

Example:

`/api/Student/2`

Here, the ID is 2.

**2. FirstOrDefault()**

```csharp
var student = students.FirstOrDefault(s => s.Id == id);
```

`FirstOrDefault()` searches the list and returns the first matching student.

- If a matching student is found, it returns that student.
- If no matching student is found, it returns null.

**3. If Condition**

```csharp
if (student == null)
{
    return NotFound("Student not found");
}
```

If the student does not exist, the API returns a 404 Not Found response.

**4. Return Student**

```csharp
return Ok(student);
```

If the student exists, the API returns the student information with status code 200.

### API URL

`GET https://localhost:<port>/api/Student/2`

### Expected Output

```json
{
  "id": 2,
  "name": "Lipsa",
  "subjects": [
    {
      "subjectId": 0,
      "subjectName": "Java",
      "marks": 98
    },
    {
      "subjectId": 0,
      "subjectName": "JavaScript",
      "marks": 98
    }
  ]
}
```

### If Student Does Not Exist

Request:

`GET https://localhost:<port>/api/Student/10`

Response:

```text
Student not found
```

Status code:

`404 Not Found`

## API 3: Get Subjects of a Student

```csharp
[HttpGet("{id}/subjects")]
public IActionResult GetStudentSubjects(int id)
{
    var student = students.FirstOrDefault(s => s.Id == id);

    if (student == null)
    {
        return NotFound("Student not found");
    }

    return Ok(student.Subjects);
}
```

### Explanation

**1. Route**

```csharp
[HttpGet("{id}/subjects")]
```

This endpoint accepts a student ID and returns the subjects of that student.

**2. Find Student**

```csharp
var student = students.FirstOrDefault(s => s.Id == id);
```

Searches for the student using the given ID.

**3. Check Student**

```csharp
if (student == null)
{
    return NotFound("Student not found");
}
```

If the student is not available, it returns a 404 response.

**4. Return Subjects**

```csharp
return Ok(student.Subjects);
```

Returns only the subject list of the selected student.

### API URL

`GET https://localhost:<port>/api/Student/1/subjects`

### Expected Output

```json
[
  {
    "subjectId": 0,
    "subjectName": "DBMS-I",
    "marks": 98
  },
  {
    "subjectId": 0,
    "subjectName": "DBMS-II",
    "marks": 98
  }
]
```

---

# 8. API Endpoint Summary

| No. | HTTP Method | Endpoint | Purpose |
|---|---|---|---|
| 1 | GET | `/api/Student` | Get all students |
| 2 | GET | `/api/Student/{id}` | Get student by ID |
| 3 | GET | `/api/Student/{id}/subjects` | Get subjects of a student |

### Status Codes

| Status Code | Meaning |
|---|---|
| 200 | OK – Request successful |
| 404 | Not Found – Student does not exist |

---

# 9. Important Difference: GET and CRUD

CRUD stands for:

| Operation | Meaning | HTTP Method |
|---|---|---|
| Create | Add new data | POST |
| Read | Fetch data | GET |
| Update | Modify existing data | PUT |
| Delete | Remove data | DELETE |

**In this practical, we have implemented only Read operations using GET.**

POST, PUT, and DELETE are not implemented in the provided code. Therefore, do not say that all four CRUD operations are completed.

---

# 10. Important Points to Remember

1. A Controller handles incoming API requests.
2. An Action Method handles a particular request.
3. `Student` and `Subject` are model classes.
4. `List<Student>` stores multiple student objects.
5. `List<Subject>` stores multiple subjects for one student.
6. `static` makes the list a class-level member.
7. `[HttpGet]` is used to retrieve data.
8. `[Route("api/[controller]")]` defines the controller's base route.
9. `FirstOrDefault()` searches for the first matching record.
10. `Ok()` returns HTTP status code 200.
11. `NotFound()` returns HTTP status code 404.
12. `MapControllers()` maps controller endpoints.
13. In-memory data is temporary and is not stored permanently in a database.

---

# 11. Viva Questions and Answers

**Q1. What is an API?**

An API allows two applications to communicate with each other.

**Q2. What is a Controller?**

A Controller handles incoming HTTP requests and returns responses.

**Q3. What is an Action Method?**

An Action Method is a method inside a controller that handles a particular API request.

**Q4. What is a Model?**

A Model represents the structure of application data.

**Q5. What is `ControllerBase`?**

It is a base class used to create API controllers in ASP.NET Core.

**Q6. What is `[ApiController]`?**

It identifies a class as an API controller and enables API-specific behaviours.

**Q7. What is `[HttpGet]`?**

It specifies that an action method handles HTTP GET requests.

**Q8. What is `IActionResult`?**

It is a return type that allows an action method to return different HTTP responses.

**Q9. What is `Ok()`?**

It returns a successful HTTP response with status code 200.

**Q10. What is `NotFound()`?**

It returns HTTP status code 404 when the requested resource is not found.

**Q11. What is `FirstOrDefault()`?**

It returns the first matching element from a collection or the default value if no matching element exists.

**Q12. What is in-memory storage?**

It means storing data temporarily in the application's memory.

**Q13. Why do we use `static List<Student>`?**

It creates a class-level list that can be accessed by the controller's action methods without creating a separate list for every controller object.

**Q14. What is the purpose of `MapControllers()`?**

It maps controller routes so incoming requests can reach the appropriate action methods.

**Q15. What is the difference between GET and POST?**

GET is used to retrieve data, whereas POST is generally used to create new data.

**Q16. What is the difference between PUT and DELETE?**

PUT is used to update existing data, whereas DELETE is used to remove data.

**Q17. Does this practical use a database?**

No. It uses a static in-memory list to store sample student records.

**Q18. What happens if we request a student ID that does not exist?**

The API returns a 404 Not Found response.

**Q19. Can one student have multiple subjects?**

Yes. We use `List<Subject>` inside the Student class.

**Q20. What does `[Route("api/[controller]")]` mean?**

It creates a route using the controller name. For `StudentController`, the route becomes `/api/Student`.

---

# 12. Conclusion

In this practical, we created Student and Subject model classes and stored student records in a static in-memory list.

We created a Student Controller and implemented three GET API action methods:

1. Get all students.
2. Get a student by ID.
3. Get subjects of a particular student.

We also learned about Controllers, Action Methods, Models, HTTP GET, IActionResult, Ok(), NotFound(), and API routing in ASP.NET Core Web API.

**Result:** Successfully created and tested three GET APIs for retrieving student and subject information.

---
