# Lab 6: DbContext, Entities and PK/FK Mapping

## 1. Aim

To create five entity classes (Role, User, Course, Assignment, Submission), create and configure Entity Framework Core DbContext, connect the application with SQL Server, and manually configure Primary Keys and Foreign Keys with appropriate delete behavior.

---

## 2. Practical Statement

1. Create the five entities (Role, User, Course, Assignment, Submission) with navigation properties.
2. Create CourseHubDbContext and register it with a connection string.
3. Configure Primary Keys and Foreign Keys with appropriate delete behavior manually.

---

# 3. Introduction to Entity Framework Core

## 3.1 What is Entity Framework Core?

Entity Framework Core (EF Core) is an Object Relational Mapper (ORM) used in .NET applications.

It allows developers to work with database tables using C# classes and objects instead of writing SQL queries for every operation.

### Example

Suppose we have a database table named `Roles`.

Without Entity Framework Core, we may write SQL:

```sql
SELECT * FROM Roles;
```

With Entity Framework Core, we can use C#:

```csharp
var roles = _context.Roles.ToList();
```

EF Core converts the C# operation into the appropriate database query.

### Advantages of EF Core

1. We can work with database tables using C# classes.
2. It reduces the need to write SQL queries manually.
3. It supports relationships between tables.
4. It supports Primary Keys and Foreign Keys.
5. It supports database migrations.
6. It works with databases such as SQL Server, SQLite, and PostgreSQL through providers.

## 3.2 What is an Entity?

An Entity is a C# class that represents a database table.

For example:

- `Role` represents the Roles table.
- `User` represents the Users table.
- `Course` represents the Courses table.
- `Assignment` represents the Assignments table.
- `Submission` represents the Submissions table.

Example:

```csharp
public class Role
{
    public int RoleId { get; set; }

    public string RoleName { get; set; }
}
```

This class represents the Role entity.

## 3.3 What is DbContext?

`DbContext` is a class provided by Entity Framework Core.

It acts as a bridge between our C# application and the database.

It is responsible for:

1. Connecting to the database.
2. Representing database tables using DbSet.
3. Tracking changes in entities.
4. Saving changes to the database.
5. Managing relationships between entities.

Example:

```csharp
public class AppDbContext : DbContext
{
    public DbSet<Role> Roles { get; set; }
}
```

Here, `AppDbContext` manages the Role entity.

## 3.4 What is DbSet?

`DbSet<TEntity>` represents a collection of entities, usually corresponding to a database table.

Example:

```csharp
public DbSet<Role> Roles { get; set; }

public DbSet<User> Users { get; set; }
```

- `Roles` represents the Roles table.
- `Users` represents the Users table.

We can use DbSet to retrieve, insert, update, and delete records.

## 3.5 What is Primary Key (PK)?

A Primary Key is a column that uniquely identifies each record in a table.

Example:

| RoleId (PK) | RoleName |
|---:|---|
| 1 | Admin |
| 2 | Faculty |
| 3 | Student |

Here, `RoleId` is the Primary Key.

Important points:

- A Primary Key must be unique.
- A Primary Key cannot contain NULL.
- Each table normally has one Primary Key, which may contain one or more columns.

## 3.6 What is Foreign Key (FK)?

A Foreign Key is a column that refers to a Primary Key in another table.

It establishes a relationship between two tables.

Example:

**Roles table**

| RoleId (PK) | RoleName |
|---:|---|
| 1 | Admin |
| 2 | Faculty |
| 3 | Student |

**Users table**

| UserId (PK) | FullName | RoleId (FK) |
|---:|---|---:|
| 1 | Sakshi | 1 |
| 2 | Lipsa | 2 |
| 3 | Daxa | 3 |

Here:

- `Users.UserId` is the Primary Key.
- `Users.RoleId` is the Foreign Key.
- `Roles.RoleId` is the Primary Key referenced by `Users.RoleId`.

This means every user belongs to a role.

## 3.7 What are Navigation Properties?

Navigation Properties allow us to navigate from one entity to another through their relationships.

For example:

```csharp
public class User
{
    public int RoleId { get; set; }

    public Role Role { get; set; }
}
```

Here:

- `RoleId` is the Foreign Key.
- `Role` is the Navigation Property.

Using the navigation property, we can access the role details of a user.

Example:

```csharp
user.Role.RoleName
```

This can access the role name associated with that user when the related entity is loaded.

## 3.8 What is Fluent API?

Fluent API is a way to configure entity properties and relationships using C# code.

We configure it inside the `OnModelCreating()` method of DbContext.

Example:

```csharp
modelBuilder.Entity<Role>()
    .HasKey(r => r.RoleId);
```

This tells EF Core that `RoleId` is the Primary Key of the Role entity.

We will use Fluent API to configure all Primary Keys and Foreign Keys manually.

---

# 4. Understanding the Course Hub Database

In this practical, we are working with the Course Hub database.

It contains five main tables:

1. Roles
2. Users
3. Courses
4. Assignments
5. Submissions

---

# 5. Understanding Database Relationships

Before writing code, understand how the five tables are connected.

## 5.1 Role and User Relationship

One Role can have many Users.

Example:

- Admin role can belong to multiple users.
- Faculty role can belong to multiple users.
- Student role can belong to multiple users.

Relationship:

**Role (1) → (Many) Users**

Foreign Key:

`Users.RoleId → Roles.RoleId`

## 5.2 User and Course Relationship

A User can be associated with multiple Courses in different roles.

The Course table contains:

- `FacultyId`
- `CreatedBy`
- `UpdatedBy`

All three columns reference `Users.UserId`.

These are three separate relationships.

Example:

- FacultyId tells us which faculty owns the course.
- CreatedBy tells us which user created the course.
- UpdatedBy tells us which user last updated the course.

## 5.3 Course and Assignment Relationship

One Course can have multiple Assignments.

Example:

A course named ASP.NET Core can contain:

- Assignment 1
- Assignment 2
- Assignment 3

Relationship:

**Course (1) → (Many) Assignments**

Foreign Key:

`Assignments.CourseId → Courses.CourseId`

## 5.4 User and Assignment Relationship

The Assignment table contains two User Foreign Keys:

- `CreatedBy`
- `UpdatedBy`

Both reference `Users.UserId`.

They represent the user who created or last updated the assignment.

## 5.5 Assignment and Submission Relationship

One Assignment can have multiple Submissions.

Example:

If 30 students submit an assignment, that assignment can have 30 submission records.

Relationship:

**Assignment (1) → (Many) Submissions**

Foreign Key:

`Submissions.AssignmentId → Assignments.AssignmentId`

## 5.6 User and Submission Relationship

The Submission table contains two User Foreign Keys:

- `StudentId`: The student who submitted the assignment.
- `GradedBy`: The faculty member who graded the submission.

Both reference `Users.UserId`.

---

# 6. Relationship Summary

| Parent Table | Child Table | Foreign Key | Relationship |
|---|---|---|---|
| Roles | Users | RoleId | One-to-Many |
| Users | Courses | FacultyId | One-to-Many |
| Users | Courses | CreatedBy | One-to-Many |
| Users | Courses | UpdatedBy | One-to-Many |
| Courses | Assignments | CourseId | One-to-Many |
| Users | Assignments | CreatedBy | One-to-Many |
| Users | Assignments | UpdatedBy | One-to-Many |
| Assignments | Submissions | AssignmentId | One-to-Many |
| Users | Submissions | StudentId | One-to-Many |
| Users | Submissions | GradedBy | One-to-Many |

**Important:** The User entity has multiple relationships with Course, Assignment, and Submission. We must configure these relationships separately in Fluent API.

---

# 7. Project Structure

Create the following folders and files in your Lab6 project:

```text
Lab6
│
├── Data
│   └── AppDbContext.cs
│
├── Entities
│   ├── Role.cs
│   ├── User.cs
│   ├── Course.cs
│   ├── Assignment.cs
│   └── Submission.cs
│
├── Controllers
│
├── Program.cs
├── appsettings.json
├── appsettings.Development.json
└── Lab6.csproj
```

### Explanation

- `Data`: Contains the DbContext class.
- `Entities`: Contains the five entity classes.
- `Controllers`: Contains API controllers.
- `Program.cs`: Registers services and configures the application.
- `appsettings.json`: Stores the database connection string.

---

# 8. Implementation

## Step 1: Create Role Entity

Create a folder named `Models`.

Inside it, create a class named `Role.cs`.

### Role.cs

```csharp
namespace Lab6.Entities
{
    public class Role
    {
        public int RoleId { get; set; }

        public string RoleName { get; set; } = string.Empty;

        // Navigation Property
        public ICollection<User> Users { get; set; } = new List<User>();
    }
}
```

### Explanation

**1. RoleId**

```csharp
public int RoleId { get; set; }
```

Stores the unique ID of the role.

It will be configured as the Primary Key.

**2. RoleName**

```csharp
public string RoleName { get; set; } = string.Empty;
```

Stores the role name, such as Admin, Faculty, or Student.

**3. Users**

```csharp
public ICollection<User> Users { get; set; } = new List<User>();
```

This is a Collection Navigation Property.

It represents multiple users belonging to one role.

For example, one Faculty role can have many users.

---

## Step 2: Create User Entity

Create a class named `User.cs` inside the Models folder.

### User.cs

```csharp
namespace Lab6.Entities
{
    public class User
    {
        public int UserId { get; set; }

        public int RoleId { get; set; }

        public string FullName { get; set; } = string.Empty;

        public string Email { get; set; } = string.Empty;

        public string PasswordHash { get; set; } = string.Empty;

        public string? ProfilePicturePath { get; set; }

        public bool IsActive { get; set; }

        public bool IsDeleted { get; set; }

        public DateTime CreatedAt { get; set; }

        public DateTime? UpdatedAt { get; set; }

        // Navigation Properties

        public Role Role { get; set; } = null!;

        public ICollection<Course> FacultyCourses { get; set; } = new List<Course>();

        public ICollection<Course> CreatedCourses { get; set; } = new List<Course>();

        public ICollection<Course> UpdatedCourses { get; set; } = new List<Course>();

        public ICollection<Assignment> CreatedAssignments { get; set; } = new List<Assignment>();

        public ICollection<Assignment> UpdatedAssignments { get; set; } = new List<Assignment>();

        public ICollection<Submission> StudentSubmissions { get; set; } = new List<Submission>();

        public ICollection<Submission> GradedSubmissions { get; set; } = new List<Submission>();
    }
}
```

### Explanation

**1. UserId**

Stores the unique ID of the user.

It will be configured as the Primary Key.

**2. RoleId**

```csharp
public int RoleId { get; set; }
```

This is a Foreign Key referencing `RoleId` in the Roles table.

### Navigation Properties

**Role**

```csharp
public Role Role { get; set; } = null!;
```

Accesses the role associated with the user.

**Important:** We use separate collection properties because the User table is connected to other tables through multiple Foreign Keys.

---

## Step 3: Create Course Entity

Create a class named `Course.cs`.

### Course.cs

```csharp
namespace Lab6.Entities
{
    public class Course
    {
        public int CourseId { get; set; }

        public string CourseCode { get; set; } = string.Empty;

        public string CourseTitle { get; set; } = string.Empty;

        public int FacultyId { get; set; }

        public int Credits { get; set; }

        public bool IsDeleted { get; set; }

        public DateTime CreatedAt { get; set; }

        public DateTime? UpdatedAt { get; set; }

        public int? CreatedBy { get; set; }

        public int? UpdatedBy { get; set; }

        // Navigation Properties

        public User Faculty { get; set; } = null!;

        public User? CreatedByUser { get; set; }

        public User? UpdatedByUser { get; set; }

        public ICollection<Assignment> Assignments { get; set; } = new List<Assignment>();
    }
}
```

### Explanation

**1. CourseId**

Stores the unique course ID.

It is the Primary Key.

**2. FacultyId**

```csharp
public int FacultyId { get; set; }
```

This is a Foreign Key referencing `Users.UserId`.

It identifies the faculty member responsible for the course.

**3. Assignments**

```csharp
public ICollection<Assignment> Assignments { get; set; } = new List<Assignment>();
```

Stores multiple assignments belonging to one course.

---

## Step 4: Create Assignment Entity

Create a class named `Assignment.cs`.

### Assignment.cs

```csharp
namespace Lab6.Entities
{
    public class Assignment
    {
        public int AssignmentId { get; set; }

        public int CourseId { get; set; }

        public string Title { get; set; } = string.Empty;

        public string? Description { get; set; }

        public DateTime DueDate { get; set; }

        public int MaxMarks { get; set; }

        public bool IsDeleted { get; set; }

        public DateTime CreatedAt { get; set; }

        public DateTime? UpdatedAt { get; set; }

        public int CreatedBy { get; set; }

        public int? UpdatedBy { get; set; }

        // Navigation Properties

        public Course Course { get; set; } = null!;

        public User CreatedByUser { get; set; } = null!;

        public User? UpdatedByUser { get; set; }

        public ICollection<Submission> Submissions { get; set; } = new List<Submission>();
    }
}
```

### Explanation

**1. AssignmentId**

Stores the unique assignment ID.

It is the Primary Key.

**2. CourseId**

This is a Foreign Key referencing `Courses.CourseId`.

It identifies the course to which the assignment belongs.

---

## Step 5: Create Submission Entity

Create a class named `Submission.cs`.

### Submission.cs

```csharp
namespace Lab6.Entities
{
    public class Submission
    {
        public int SubmissionId { get; set; }

        public int AssignmentId { get; set; }

        public int StudentId { get; set; }

        public string FilePath { get; set; } = string.Empty;

        public int? MarksObtained { get; set; }

        public string? Remarks { get; set; }

        public string Status { get; set; } = string.Empty;

        public DateTime SubmittedAt { get; set; }

        public DateTime? GradedAt { get; set; }

        public int? GradedBy { get; set; }

        public bool IsDeleted { get; set; }

        public DateTime CreatedAt { get; set; }

        public DateTime? UpdatedAt { get; set; }

        // Navigation Properties

        public Assignment Assignment { get; set; } = null!;

        public User Student { get; set; } = null!;

        public User? GradedByUser { get; set; }
    }
}
```

### Explanation

**1. SubmissionId**

Stores the unique submission ID.

It is the Primary Key.

**2. AssignmentId**

Foreign Key referencing `Assignments.AssignmentId`.

It identifies the assignment for which the student submitted work.

**3. StudentId**

Foreign Key referencing `Users.UserId`.

It identifies the student who submitted the assignment.

**4. Navigation Properties**

- `Assignment`: Accesses assignment details.
- `Student`: Accesses the student who submitted the work.
- `GradedByUser`: Accesses the faculty member who graded the work.

---

# 9. Create DbContext

Now we have created all five entity classes.

Next, we need to create a DbContext class.

Create a folder named `Data`.

Inside it, create a class named `AppDbContext.cs`.

### AppDbContext.cs

```csharp
using Lab6.Models;
using Microsoft.EntityFrameworkCore;

namespace Lab6.Data
{
    public class AppDbContext : DbContext
    {
        public AppDbContext(DbContextOptions<AppDbContext> options)
            : base(options)
        {
        }

        // DbSets

        public DbSet<Role> Roles { get; set; }

        public DbSet<User> Users { get; set; }

        public DbSet<Course> Courses { get; set; }

        public DbSet<Assignment> Assignments { get; set; }

        public DbSet<Submission> Submissions { get; set; }

        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            base.OnModelCreating(modelBuilder);

            // Primary Keys

            modelBuilder.Entity<Role>()
                .HasKey(r => r.RoleId);

            modelBuilder.Entity<User>()
                .HasKey(u => u.UserId);

            modelBuilder.Entity<Course>()
                .HasKey(c => c.CourseId);

            modelBuilder.Entity<Assignment>()
                .HasKey(a => a.AssignmentId);

            modelBuilder.Entity<Submission>()
                .HasKey(s => s.SubmissionId);


            // Role - User Relationship

            modelBuilder.Entity<User>()
                .HasOne(u => u.Role)
                .WithMany(r => r.Users)
                .HasForeignKey(u => u.RoleId)
                .OnDelete(DeleteBehavior.Restrict);


            // User - Course Relationship (FacultyId)

            modelBuilder.Entity<Course>()
                .HasOne(c => c.Faculty)
                .WithMany(u => u.FacultyCourses)
                .HasForeignKey(c => c.FacultyId)
                .OnDelete(DeleteBehavior.Restrict);


            // User - Course Relationship (CreatedBy)

            modelBuilder.Entity<Course>()
                .HasOne(c => c.CreatedByUser)
                .WithMany(u => u.CreatedCourses)
                .HasForeignKey(c => c.CreatedBy)
                .OnDelete(DeleteBehavior.Restrict);


            // User - Course Relationship (UpdatedBy)

            modelBuilder.Entity<Course>()
                .HasOne(c => c.UpdatedByUser)
                .WithMany(u => u.UpdatedCourses)
                .HasForeignKey(c => c.UpdatedBy)
                .OnDelete(DeleteBehavior.Restrict);


            // Course - Assignment Relationship

            modelBuilder.Entity<Assignment>()
                .HasOne(a => a.Course)
                .WithMany(c => c.Assignments)
                .HasForeignKey(a => a.CourseId)
                .OnDelete(DeleteBehavior.Cascade);


            // User - Assignment Relationship (CreatedBy)

            modelBuilder.Entity<Assignment>()
                .HasOne(a => a.CreatedByUser)
                .WithMany(u => u.CreatedAssignments)
                .HasForeignKey(a => a.CreatedBy)
                .OnDelete(DeleteBehavior.Restrict);


            // User - Assignment Relationship (UpdatedBy)

            modelBuilder.Entity<Assignment>()
                .HasOne(a => a.UpdatedByUser)
                .WithMany(u => u.UpdatedAssignments)
                .HasForeignKey(a => a.UpdatedBy)
                .OnDelete(DeleteBehavior.Restrict);


            // Assignment - Submission Relationship

            modelBuilder.Entity<Submission>()
                .HasOne(s => s.Assignment)
                .WithMany(a => a.Submissions)
                .HasForeignKey(s => s.AssignmentId)
                .OnDelete(DeleteBehavior.Cascade);


            // User - Submission Relationship (StudentId)

            modelBuilder.Entity<Submission>()
                .HasOne(s => s.Student)
                .WithMany(u => u.StudentSubmissions)
                .HasForeignKey(s => s.StudentId)
                .OnDelete(DeleteBehavior.Restrict);


            // User - Submission Relationship (GradedBy)

            modelBuilder.Entity<Submission>()
                .HasOne(s => s.GradedByUser)
                .WithMany(u => u.GradedSubmissions)
                .HasForeignKey(s => s.GradedBy)
                .OnDelete(DeleteBehavior.Restrict);
        }
    }
}
```

---

# 10. Understanding DbContext Code

## 10.1 Inherit DbContext

```csharp
public class AppDbContext : DbContext
```

Here, `AppDbContext` inherits from the EF Core `DbContext` class.

This gives our class database-related functionality.

## 10.2 Constructor

```csharp
public AppDbContext(DbContextOptions<AppDbContext> options)
    : base(options)
{
}
```

### Explanation

- `DbContextOptions` contains the configuration information for DbContext.
- It includes information such as which database provider to use.
- `base(options)` passes these settings to the parent DbContext class.

## 10.3 DbSet Properties

```csharp
public DbSet<Role> Roles { get; set; }

public DbSet<User> Users { get; set; }

public DbSet<Course> Courses { get; set; }

public DbSet<Assignment> Assignments { get; set; }

public DbSet<Submission> Submissions { get; set; }
```

These properties represent our five entity collections.

| DbSet | Represents |
|---|---|
| Roles | Roles table |
| Users | Users table |
| Courses | Courses table |
| Assignments | Assignments table |
| Submissions | Submissions table |

## 10.4 OnModelCreating()

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
```

This method is used to configure entity properties and relationships.

We use it to configure:

1. Primary Keys.
2. Foreign Keys.
3. Delete behavior.

**Why do we use Fluent API?**

Because the practical specifically asks us to configure Primary Keys and Foreign Keys manually.

## 10.5 HasKey()

Example:

```csharp
modelBuilder.Entity<Role>()
    .HasKey(r => r.RoleId);
```

`HasKey()` defines the Primary Key of an entity.

Here, `RoleId` is the Primary Key of Role.

Similarly, we configure Primary Keys for all five entities.

## 10.6 HasOne()

Example:

```csharp
.HasOne(u => u.Role)
```

`HasOne()` indicates that an entity has one related entity.

Here, one User belongs to one Role.

## 10.7 WithMany()

```csharp
.WithMany(r => r.Users)
```

`WithMany()` indicates that the related entity can have multiple records.

Here, one Role can have many Users.

## 10.8 HasForeignKey()

```csharp
.HasForeignKey(u => u.RoleId)
```

This specifies which property acts as the Foreign Key.

Here, `RoleId` in the User entity references the Role entity.

## 10.9 OnDelete()

```csharp
.OnDelete(DeleteBehavior.Restrict);
```

This configures what happens when a referenced parent record is deleted.

We will understand the different delete behaviors in the next section.

---

# 11. Understanding Delete Behavior

Delete behavior tells EF Core what should happen to related records when a parent record is deleted.

Common delete behaviors include:

| Delete Behavior | Meaning |
|---|---|
| Cascade | Related child records are also deleted. |
| Restrict | Prevents deleting a parent while related records exist. |
| SetNull | Sets the related Foreign Key to NULL when the parent is deleted, if allowed. |
| NoAction | Does not automatically delete related records; the database enforces its constraints. |

### 11.1 Cascade Delete

Example:

```csharp
.OnDelete(DeleteBehavior.Cascade);
```

Suppose one Course has three Assignments.

If the Course is deleted, its related Assignments are also deleted.

In our practical:

- Course → Assignments: Cascade.
- Assignment → Submissions: Cascade.

### 11.2 Restrict Delete

Example:

```csharp
.OnDelete(DeleteBehavior.Restrict);
```

Suppose one Role has multiple Users.

If users are still associated with that Role, deleting the Role is prevented.

In our practical, Restrict is used for User-related relationships to avoid accidentally deleting users, courses, assignments, or submissions through their other relationships.

### 11.3 SetNull

Example:

```csharp
.OnDelete(DeleteBehavior.SetNull);
```

If the referenced parent is deleted, the Foreign Key in the child record is set to NULL.

The Foreign Key property must be nullable.

For example:

```csharp
public int? UpdatedBy { get; set; }
```

**Note:** This practical uses Restrict and Cascade. SetNull is explained as another available delete behavior.

## 11.4 Delete Behavior Used in Our Practical

| Relationship | Delete Behavior | Reason |
|---|---|---|
| Role → Users | Restrict | A role cannot be deleted while users reference it. |
| User → Courses (FacultyId) | Restrict | Prevents deleting a faculty user who owns courses. |
| User → Courses (CreatedBy) | Restrict | Prevents deleting a user who created courses. |
| User → Courses (UpdatedBy) | Restrict | Prevents deleting a user referenced as updater. |
| Course → Assignments | Cascade | Deleting a course also deletes its assignments. |
| User → Assignments (CreatedBy) | Restrict | Protects the creator reference. |
| User → Assignments (UpdatedBy) | Restrict | Protects the updater reference. |
| Assignment → Submissions | Cascade | Deleting an assignment also deletes its submissions. |
| User → Submissions (StudentId) | Restrict | Protects student submission records. |
| User → Submissions (GradedBy) | Restrict | Protects grading references. |

**Important:** These delete behaviors are the explicit mapping choices used in this guide. They are not all specified in the database schema document itself. The database schema defines the relationships; the Fluent API configures the chosen delete behavior.

---

# 12. Configure Database Connection

Now we need to connect our ASP.NET Core Web API application with SQL Server.

## 12.1 Install Required Packages

Your current Lab6 project already includes these EF Core packages in the `.csproj` file:

- Microsoft.EntityFrameworkCore
- Microsoft.EntityFrameworkCore.SqlServer
- Microsoft.EntityFrameworkCore.Design
- Microsoft.EntityFrameworkCore.Tools

They are currently using version `10.0.10`.

The SQL Server package allows EF Core to communicate with SQL Server.

## 12.2 Add Connection String in appsettings.json

Open `appsettings.json`.

Your current connection string is:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*",
  "ConnectionStrings": {
    "Default": "Server=SAKSHISANTOKI\\SQLEXPRESS;Database=TALab;Trusted_Connection=True;TrustServerCertificate=True;"
  }
}
```

### Explanation

| Part | Meaning |
|---|---|
| Server | SQL Server instance name |
| Database | Database name |
| Trusted_Connection=True | Uses Windows Authentication |
| TrustServerCertificate=True | Trusts the SQL Server certificate for this connection |

**Important:** Keep your existing database name and SQL Server instance. Do not replace them with another database unless you intentionally want to connect to a different database.

## 12.3 Register DbContext in Program.cs

Open `Program.cs`.

Your existing project uses `AppDbContext`, so register it as follows.

### Program.cs

```csharp
using Lab6.Data;
using Microsoft.EntityFrameworkCore;
using Scalar.AspNetCore;

namespace Lab6
{
    public class Program
    {
        public static void Main(string[] args)
        {
            var builder = WebApplication.CreateBuilder(args);

            // Add services to the container.
            builder.Services.AddControllers();

            // Register DbContext
            builder.Services.AddDbContext<AppDbContext>(options =>
                options.UseSqlServer(
                    builder.Configuration.GetConnectionString("Default")));

            // Add OpenAPI
            builder.Services.AddOpenApi();

            var app = builder.Build();

            // Configure the HTTP request pipeline.
            if (app.Environment.IsDevelopment())
            {
                app.MapOpenApi();
                app.MapScalarApiReference();
            }

            app.UseHttpsRedirection();

            app.UseAuthorization();

            app.MapControllers();

            app.Run();
        }
    }
}
```

### Explanation

**1. Import DbContext Namespace**

```csharp
using Microsoft.EntityFrameworkCore;
```

Allows us to use EF Core methods such as `AddDbContext()` and `UseSqlServer()`.

**2. Register DbContext**

```csharp
builder.Services.AddDbContext<AppDbContext>(options =>
    options.UseSqlServer(
        builder.Configuration.GetConnectionString("Default")));
```

This registers our DbContext with the application's Dependency Injection container.

**3. UseSqlServer()**

```csharp
options.UseSqlServer(...)
```

Tells EF Core that we are using SQL Server as our database provider.

**4. GetConnectionString()**

```csharp
builder.Configuration.GetConnectionString("Default")
```

Reads the connection string named `Default` from `appsettings.json`.

**5. Dependency Injection**

Dependency Injection means that the application provides the required object automatically instead of us creating it manually.

Later, we can inject `AppDbContext` into a controller:

```csharp
private readonly AppDbContext _context;

public RoleController(AppDbContext context)
{
    _context = context;
}
```

We do not need to create `new AppDbContext()` manually.

---

# 12. How All Five Entities Are Connected

The following simplified diagram shows the main relationships:

```text
Role
  |
  | 1 to Many
  |
 User
  |
  | 1 to Many (FacultyId)
  |
 Course
  |
  | 1 to Many
  |
 Assignment
  |
  | 1 to Many
  |
 Submission
```
---

# 13. Common Mistakes

| Mistake | Solution |
|---|---|
| Forgetting to inherit DbContext | Use `: DbContext` in the DbContext class. |
| Forgetting the constructor | Add the DbContextOptions constructor. |
| Missing `using Microsoft.EntityFrameworkCore;` | Add the EF Core namespace. |
| Missing `using Lab6.Entities;` | Import the Entities namespace in DbContext. |
| Forgetting DbSet properties | Add all five DbSets. |
| Forgetting Primary Key configuration | Use `HasKey()`. |
| Forgetting Foreign Key configuration | Use `HasForeignKey()`. |
| Not configuring multiple User relationships separately | Configure each FK and navigation property independently. |
| Using the wrong connection string name | Make sure `GetConnectionString("Default")` matches the `Default` entry in appsettings.json. |
| Registering the wrong DbContext name | Match the generic type in `AddDbContext<T>()` with the actual class. |
| Forgetting to register DbContext | Add `AddDbContext()` in Program.cs. |
| Making a required FK nullable | Follow the nullability defined in the database schema. |
| Using Cascade for every relationship | Choose delete behavior carefully to avoid accidental deletion and multiple cascade paths. |

---

# 14. Viva Questions and Answers

**Q1. What is Entity Framework Core?**

Entity Framework Core is an ORM that allows us to work with databases using C# classes and objects.

**Q2. What is an ORM?**

ORM stands for Object Relational Mapper. It maps C# objects to database tables.

**Q3. What is an Entity?**

An Entity is a C# class that represents a database table.

**Q4. What is DbContext?**

DbContext is the main class used by EF Core to communicate with the database.

**Q5. What is DbSet?**

DbSet represents a collection of entities, usually corresponding to a database table.

**Q6. What is a Primary Key?**

A Primary Key uniquely identifies each record in a table.

**Q7. What is a Foreign Key?**

A Foreign Key is a column that references the Primary Key of another table.

**Q8. What is a Navigation Property?**

A Navigation Property allows us to access related entities through an object.

**Q9. What is Fluent API?**

Fluent API is used to configure entity properties and relationships using C# code.

**Q10. What is OnModelCreating()?**

It is a method used to configure the database model, including Primary Keys, Foreign Keys, relationships, and constraints.

**Q11. What is HasKey()?**

It is used to configure the Primary Key of an entity.

**Q12. What is HasOne()?**

It indicates that an entity has one related entity.

**Q13. What is WithMany()?**

It indicates that the related entity can have multiple records.

**Q14. What is HasForeignKey()?**

It specifies which property acts as the Foreign Key.

**Q15. What is Cascade Delete?**

Cascade Delete automatically deletes related child records when the parent record is deleted.

**Q16. What is Restrict Delete?**

Restrict prevents deleting a parent record while related records still reference it.

**Q17. What is SetNull?**

SetNull sets the related Foreign Key to NULL when the parent record is deleted, provided the FK is nullable.

**Q18. What is Dependency Injection?**

Dependency Injection is a technique in which required objects are provided automatically by the application.

**Q19. Why do we use AddDbContext()?**

It registers the DbContext in the Dependency Injection container.

**Q20. What is UseSqlServer()?**

It configures EF Core to use SQL Server as the database provider.

**Q21. Why do we use GetConnectionString()?**

It reads a named connection string from the application's configuration.

**Q22. Why do we use navigation properties?**

They allow us to access related records through entity objects.

**Q23. Can one User have multiple relationships with the Course table?**

Yes. In this database, a User can be referenced through FacultyId, CreatedBy, and UpdatedBy.

**Q24. What is the difference between a Foreign Key and a Navigation Property?**

A Foreign Key is a property that stores the referenced record's ID. A Navigation Property provides access to the related entity object.

**Q25. Does Lab 6 create the database migration?**

No. Lab 6 focuses on entities, DbContext, and PK/FK mapping. Creating and applying the first migration is part of Lab 7.

# 15. Conclusion

In this practical, we created five entity classes:

1. Role
2. User
3. Course
4. Assignment
5. Submission

We created an EF Core DbContext class and added DbSet properties for all five entities.

We also configured:

- Primary Keys using `HasKey()`.
- Foreign Keys using `HasForeignKey()`.
- Relationships using `HasOne()` and `WithMany()`.
- Delete behavior using `OnDelete()`.
- SQL Server connection using `UseSqlServer()`.
- DbContext registration using `AddDbContext()`.

**Result:** Successfully created the five Course Hub entities, configured DbContext, registered the SQL Server connection, and manually mapped Primary Keys, Foreign Keys, navigation properties, and delete behavior.

---
