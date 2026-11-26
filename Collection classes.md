# Lab 3 – Collections in C# (List and Dictionary)

## Aim

To understand and implement collections in C# using:

1. `List<T>` – Student Management System
2. `List<T>` – Shopping Cart System
3. `Dictionary<TKey, TValue>` – Student Grade Book

## 1. Introduction to Collections in C#

A collection is used to store multiple values or objects in a single variable.

For example, suppose we want to store the details of 5 students. We can create 5 different student objects, but managing them individually becomes difficult.

Collections make it easier to store, access, update, search, and delete multiple objects.

C# provides different types of collections. In this lab, we will learn:

- `List<T>`
- `Dictionary<TKey, TValue>`

## 2. What is List in C#?

A `List<T>` is a collection that stores multiple elements of the same data type.

It is similar to an array, but its size can increase or decrease dynamically.

### Syntax

```csharp
List<int> numbers = new List<int>();
```

Here:

- `List<int>` means a list that stores integer values.
- `numbers` is the name of the list.
- `new List<int>()` creates a new empty list.

### Example

```csharp
List<string> names = new List<string>();

names.Add("Amit");
names.Add("Neha");
names.Add("Rahul");

foreach (string name in names)
{
    Console.WriteLine(name);
}
```

### Output

```text
Amit
Neha
Rahul
```

### Important List Methods

| Method | Description |
|---|---|
| `Add()` | Adds an element to the list |
| `Remove()` | Removes a particular element |
| `Find()` | Finds an element that matches a condition |
| `Count` | Returns the number of elements |
| `Clear()` | Removes all elements |

## 3. What is Dictionary in C#?

A `Dictionary<TKey, TValue>` stores data in the form of key-value pairs.

Every value is stored with a unique key.

For example:

| Key (Student ID) | Value (Student Details) |
|---|---|
| 101 | Student 1 |
| 102 | Student 2 |
| 103 | Student 3 |

We can use the key to access the corresponding value quickly.

### Syntax

```csharp
Dictionary<int, string> students =
    new Dictionary<int, string>();
```

Here:

- `int` is the key data type.
- `string` is the value data type.
- `students` is the dictionary name.

### Example

```csharp
Dictionary<int, string> students =
    new Dictionary<int, string>();

students.Add(101, "Amit");
students.Add(102, "Neha");
students.Add(103, "Rahul");

Console.WriteLine(students[101]);
```

### Output

```text
Amit
```

### Important Dictionary Methods

| Method | Description |
|---|---|
| `Add()` | Adds a new key-value pair |
| `ContainsKey()` | Checks whether a key exists |
| `Remove()` | Removes an element using its key |
| `Count` | Returns the number of key-value pairs |
| `Clear()` | Removes all elements |

**Remember:** Dictionary keys must be unique. We cannot add two entries with the same key.

---

# Practical 1 – Student Management System Using List

## Aim

To create a Student Management System using `List<Student>` that allows us to:

1. Add a student
2. Display all students
3. Search for a student
4. Update student details
5. Delete a student

## Step 1: Create Student Class

Create a file named `Student.cs`.

### Student.cs

```csharp
using System;

namespace Lab3
{
    internal class Student
    {
        public int ID;
        public string Name;
        public int Age;
        public string Course;

        public void Display()
        {
            Console.WriteLine("ID     : " + ID);
            Console.WriteLine("Name   : " + Name);
            Console.WriteLine("Age    : " + Age);
            Console.WriteLine("Course : " + Course);
        }
    }
}
```

## Explanation of Student Class

### 1. Data Members

```csharp
public int ID;
public string Name;
public int Age;
public string Course;
```

These variables store the details of a student.

- `ID` stores the student ID.
- `Name` stores the student name.
- `Age` stores the student's age.
- `Course` stores the course name.

### 2. Display() Method

```csharp
public void Display()
{
    Console.WriteLine("ID     : " + ID);
    Console.WriteLine("Name   : " + Name);
    Console.WriteLine("Age    : " + Age);
    Console.WriteLine("Course : " + Course);
}
```

This method displays the details of a student.

For example, if the student has ID 101 and name Amit, it displays those values.

## Step 2: Create Program.cs

### Program.cs

```csharp
using System;
using System.Collections.Generic;

namespace Lab3
{
    internal class Program
    {
        static List<Student> students = new List<Student>();

        static void Main(string[] args)
        {
            while (true)
            {
                Console.WriteLine("\n===== Student Management System =====");
                Console.WriteLine("1. Add Student");
                Console.WriteLine("2. Display Students");
                Console.WriteLine("3. Search Student");
                Console.WriteLine("4. Update Student");
                Console.WriteLine("5. Delete Student");
                Console.WriteLine("6. Exit");

                Console.Write("Enter your choice: ");
                int choice = Convert.ToInt32(Console.ReadLine());

                switch (choice)
                {
                    case 1:
                        AddStudent();
                        break;

                    case 2:
                        DisplayStudents();
                        break;

                    case 3:
                        SearchStudent();
                        break;

                    case 4:
                        UpdateStudent();
                        break;

                    case 5:
                        DeleteStudent();
                        break;

                    case 6:
                        Console.WriteLine("Program exited.");
                        return;

                    default:
                        Console.WriteLine("Invalid choice!");
                        break;
                }
            }
        }

        static void AddStudent()
        {
            Student s = new Student();

            Console.Write("Enter Student ID: ");
            s.ID = Convert.ToInt32(Console.ReadLine());

            Console.Write("Enter Student Name: ");
            s.Name = Console.ReadLine();

            Console.Write("Enter Age: ");
            s.Age = Convert.ToInt32(Console.ReadLine());

            Console.Write("Enter Course: ");
            s.Course = Console.ReadLine();

            students.Add(s);

            Console.WriteLine("Student added successfully!");
        }

        static void UpdateStudent()
        {
            Console.Write("Enter Student ID to update: ");
            int id = Convert.ToInt32(Console.ReadLine());

            Student found = students.Find(s => s.ID == id);

            if (found != null)
            {
                Console.Write("Enter New Name: ");
                found.Name = Console.ReadLine();

                Console.Write("Enter New Age: ");
                found.Age = Convert.ToInt32(Console.ReadLine());

                Console.Write("Enter New Course: ");
                found.Course = Console.ReadLine();

                Console.WriteLine("Student updated successfully!");
            }
            else
            {
                Console.WriteLine("Student not found.");
            }
        }

        static void DeleteStudent()
        {
            Console.Write("Enter Student ID to delete: ");
            int id = Convert.ToInt32(Console.ReadLine());

            Student found = students.Find(s => s.ID == id);

            if (found != null)
            {
                students.Remove(found);
                Console.WriteLine("Student deleted successfully!");
            }
            else
            {
                Console.WriteLine("Student not found.");
            }
        }

        static void SearchStudent()
        {
            Console.Write("Enter Student ID to search: ");
            int id = Convert.ToInt32(Console.ReadLine());

            Student found = students.Find(s => s.ID == id);

            if (found != null)
            {
                Console.WriteLine("\nStudent Found:");
                found.Display();
            }
            else
            {
                Console.WriteLine("Student not found.");
            }
        }

        static void DisplayStudents()
        {
            if (students.Count == 0)
            {
                Console.WriteLine("No student records found.");
                return;
            }

            Console.WriteLine("\n===== Student Records =====");

            foreach (Student s in students)
            {
                s.Display();
                Console.WriteLine("--------------------");
            }
        }
    }
}
```

## Explanation of Program.cs

### 1. Create a List

```csharp
static List<Student> students = new List<Student>();
```

This creates a list named `students` that can store multiple Student objects.

For example, it can store:

- Student 1
- Student 2
- Student 3

We do not need to create separate variables for every student.

### 2. while(true)

```csharp
while (true)
```

This creates an infinite loop.

It keeps displaying the menu until the user selects Exit.

### 3. switch Statement

```csharp
switch (choice)
```

The switch statement checks the user's choice and calls the appropriate method.

For example:

- Choice 1 calls `AddStudent()`.
- Choice 2 calls `DisplayStudents()`.
- Choice 3 calls `SearchStudent()`.
- Choice 4 calls `UpdateStudent()`.
- Choice 5 calls `DeleteStudent()`.

### 4. AddStudent() Method

```csharp
Student s = new Student();
```

This creates a new Student object.

```csharp
s.ID = Convert.ToInt32(Console.ReadLine());
```

This accepts the student ID from the user and stores it in the object's ID variable.

```csharp
students.Add(s);
```

This adds the complete Student object to the list.

**Important:** `Add()` adds an object to the list, not just one of its individual fields.

### 5. SearchStudent() Method

```csharp
Student found = students.Find(s => s.ID == id);
```

This searches for a student whose ID matches the ID entered by the user.

Explanation:

- `students.Find()` searches inside the list.
- `s` represents each Student object being checked.
- `s.ID` accesses the student's ID.
- `== id` checks whether the ID matches.

If a matching student is found, the object is returned. Otherwise, `found` becomes `null`.

### 6. UpdateStudent() Method

```csharp
if (found != null)
{
    found.Name = Console.ReadLine();
}
```

If the student exists, we can directly update the object's fields.

For example, `found.Name` changes the name of the existing student.

### 7. DeleteStudent() Method

```csharp
students.Remove(found);
```

This removes the selected Student object from the list.

### 8. DisplayStudents() Method

```csharp
foreach (Student s in students)
{
    s.Display();
}
```

The foreach loop visits every Student object in the list and calls its Display method.

## Sample Output

```text
===== Student Management System =====
1. Add Student
2. Display Students
3. Search Student
4. Update Student
5. Delete Student
6. Exit

Enter your choice: 1

Enter Student ID: 101
Enter Student Name: Amit
Enter Age: 20
Enter Course: CSE

Student added successfully!
```

When the user selects Display Students:

```text
===== Student Records =====

ID     : 101
Name   : Amit
Age    : 20
Course : CSE
--------------------
```

When the user searches for ID 101:

```text
Student Found:

ID     : 101
Name   : Amit
Age    : 20
Course : CSE
```

---

# Practical 2 – Shopping Cart System Using List

## Aim

To create a Shopping Cart System using `List<CartItem>` that allows users to:

1. Add items
2. Remove items
3. View cart items
4. Calculate the total amount
5. Apply a discount

## Step 1: Create CartItem Class

Create a file named `CartItem.cs`.

### CartItem.cs

```csharp
using System;

namespace Lab3
{
    internal class CartItem
    {
        public int ID;
        public string Name;
        public double Price;
        public int Quantity;

        public double GetAmount()
        {
            return Price * Quantity;
        }

        public void Display()
        {
            Console.WriteLine(
                ID + "\t" + Name + "\t" + Price + "\t" +
                Quantity + "\t" + GetAmount()
            );
        }
    }
}
```

## Explanation of CartItem Class

### 1. Data Members

```csharp
public int ID;
public string Name;
public double Price;
public int Quantity;
```

These variables store the details of a shopping cart item.

For example:

- ID = 101
- Name = Laptop
- Price = 50000
- Quantity = 1

### 2. GetAmount() Method

```csharp
public double GetAmount()
{
    return Price * Quantity;
}
```

This method calculates the amount for an item.

Formula:

```text
Amount = Price × Quantity
```

Example:

```text
Price = 100
Quantity = 3

Amount = 100 × 3
       = 300
```

### 3. Display() Method

```csharp
public void Display()
{
    Console.WriteLine(
        ID + "\t" + Name + "\t" + Price + "\t" +
        Quantity + "\t" + GetAmount()
    );
}
```

This displays the item details in a row.

The `\t` character is used to provide tab spacing between columns.

## Step 2: Create Program.cs

### Program.cs

```csharp
using System;
using System.Collections.Generic;

namespace Lab3
{
    internal class Program
    {
        static List<CartItem> cart = new List<CartItem>();

        static void Main(string[] args)
        {
            while (true)
            {
                Console.WriteLine("\n===== SHOPPING CART SYSTEM =====");
                Console.WriteLine("1. Add Item");
                Console.WriteLine("2. Remove Item");
                Console.WriteLine("3. View Cart");
                Console.WriteLine("4. Calculate Total");
                Console.WriteLine("5. Exit");

                Console.Write("Enter your choice: ");
                int choice = Convert.ToInt32(Console.ReadLine());

                switch (choice)
                {
                    case 1:
                        AddItem();
                        break;

                    case 2:
                        RemoveItem();
                        break;

                    case 3:
                        ViewCart();
                        break;

                    case 4:
                        CalculateTotal();
                        break;

                    case 5:
                        Console.WriteLine("Thank you!");
                        return;

                    default:
                        Console.WriteLine("Invalid choice!");
                        break;
                }
            }
        }

        static void AddItem()
        {
            CartItem item = new CartItem();

            Console.Write("Enter Item ID: ");
            item.ID = Convert.ToInt32(Console.ReadLine());

            Console.Write("Enter Item Name: ");
            item.Name = Console.ReadLine();

            Console.Write("Enter Price: ");
            item.Price = Convert.ToDouble(Console.ReadLine());

            Console.Write("Enter Quantity: ");
            item.Quantity = Convert.ToInt32(Console.ReadLine());

            cart.Add(item);

            Console.WriteLine("Item added to cart successfully!");
        }

        static void RemoveItem()
        {
            Console.Write("Enter Item ID to remove: ");
            int id = Convert.ToInt32(Console.ReadLine());

            CartItem item = cart.Find(x => x.ID == id);

            if (item != null)
            {
                cart.Remove(item);
                Console.WriteLine("Item removed successfully!");
            }
            else
            {
                Console.WriteLine("Item not found in cart.");
            }
        }

        static void ViewCart()
        {
            if (cart.Count == 0)
            {
                Console.WriteLine("Cart is empty.");
                return;
            }

            Console.WriteLine("\n========== SHOPPING CART ==========");
            Console.WriteLine("ID\tName\tPrice\tQty\tAmount");
            Console.WriteLine("-----------------------------------");

            foreach (CartItem item in cart)
            {
                item.Display();
            }
        }

        static void CalculateTotal()
        {
            if (cart.Count == 0)
            {
                Console.WriteLine("Cart is empty.");
                return;
            }

            double total = 0;

            foreach (CartItem item in cart)
            {
                total += item.GetAmount();
            }

            double discount = 0;

            if (total >= 5000)
            {
                discount = total * 0.10;
            }

            double finalAmount = total - discount;

            Console.WriteLine("\n========== BILL ==========");
            Console.WriteLine("Total Amount : " + total);
            Console.WriteLine("Discount     : " + discount);
            Console.WriteLine("Final Amount : " + finalAmount);
        }
    }
}
```

## Explanation of Program.cs

### 1. Create a List

```csharp
static List<CartItem> cart = new List<CartItem>();
```

This creates a list that stores CartItem objects.

Each object contains:

- Item ID
- Item Name
- Price
- Quantity

### 2. AddItem() Method

```csharp
CartItem item = new CartItem();
```

This creates a new CartItem object.

The program accepts the item's details and stores them in the object.

```csharp
cart.Add(item);
```

This adds the object to the shopping cart list.

### 3. RemoveItem() Method

```csharp
CartItem item = cart.Find(x => x.ID == id);
```

This searches for an item using its ID.

```csharp
cart.Remove(item);
```

This removes the matching item from the cart.

### 4. ViewCart() Method

```csharp
foreach (CartItem item in cart)
{
    item.Display();
}
```

This loop visits every item in the cart and displays its details.

### 5. CalculateTotal() Method

```csharp
double total = 0;
```

This variable stores the total shopping amount.

```csharp
foreach (CartItem item in cart)
{
    total += item.GetAmount();
}
```

This calculates the amount of every item and adds it to the total.

For example:

| Item | Price | Quantity | Amount |
|---|---:|---:|---:|
| Laptop | 40000 | 1 | 40000 |
| Mouse | 500 | 2 | 1000 |
| Keyboard | 1000 | 1 | 1000 |
| **Total** | | | **42000** |

### 6. Apply Discount

```csharp
if (total >= 5000)
{
    discount = total * 0.10;
}
```

If the total amount is greater than or equal to 5000, the program gives a 10% discount.

Formula:

```text
Discount = Total × 10 / 100

Final Amount = Total - Discount
```

Example:

```text
Total Amount = 10000

Discount = 10000 × 10 / 100
         = 1000

Final Amount = 10000 - 1000
             = 9000
```

## Sample Output

```text
===== SHOPPING CART SYSTEM =====
1. Add Item
2. Remove Item
3. View Cart
4. Calculate Total
5. Exit

Enter your choice: 1

Enter Item ID: 101
Enter Item Name: Laptop
Enter Price: 40000
Enter Quantity: 1

Item added to cart successfully!
```

When the user selects View Cart:

```text
========== SHOPPING CART ==========
ID      Name       Price    Qty    Amount
-----------------------------------
101     Laptop     40000    1      40000
102     Mouse      500      2      1000
```

When the user selects Calculate Total:

```text
========== BILL ==========

Total Amount : 41000
Discount     : 4100
Final Amount : 36900
```

---

# Practical 3 – Student Grade Book Using Dictionary

## Aim

To create a Student Grade Book using `Dictionary<int, Grade>` that allows users to:

1. Add a student
2. Update student details and marks
3. Display student reports
4. Search for a student
5. Calculate student percentage

This is the active example in the uploaded `Program.cs`.

## Step 1: Create Grade Class

Create a file named `Grade.cs`.

### Grade.cs

```csharp
using System;
using System.Collections.Generic;

namespace Lab3
{
    internal class Grade
    {
        public int ID;

        public string Name;

        public Dictionary<string, int> Marks =
            new Dictionary<string, int>();

        public void CalculatePercentage()
        {
            int total = 0;

            foreach (int mark in Marks.Values)
            {
                total += mark;
            }

            double percentage = (double)total / Marks.Count;

            Console.WriteLine("Percentage: " + percentage + "%");
        }

        public void Display()
        {
            Console.WriteLine("\nStudent ID   : " + ID);
            Console.WriteLine("Student Name : " + Name);

            Console.WriteLine("Subject-wise Marks:");

            foreach (var item in Marks)
            {
                Console.WriteLine(item.Key + " : " + item.Value);
            }

            CalculatePercentage();
        }
    }
}
```

## Explanation of Grade Class

### 1. Data Members

```csharp
public int ID;
public string Name;
```

These variables store the student ID and student name.

### 2. Dictionary for Marks

```csharp
public Dictionary<string, int> Marks =
    new Dictionary<string, int>();
```

This dictionary stores subject names and their marks.

Here:

- `string` represents the subject name.
- `int` represents the marks.

For example:

| Key (Subject) | Value (Marks) |
|---|---:|
| C# | 80 |
| Java | 90 |
| Python | 85 |

The subject name is the key, and marks are the value.

### 3. CalculatePercentage() Method

```csharp
int total = 0;
```

This variable stores the total marks.

```csharp
foreach (int mark in Marks.Values)
{
    total += mark;
}
```

`Marks.Values` gives all the marks stored in the dictionary.

The foreach loop adds all marks.

Example:

```text
C#      = 80
Java    = 90
Python  = 85

Total = 80 + 90 + 85
      = 255
```

```csharp
double percentage = (double)total / Marks.Count;
```

This calculates the average marks.

Here:

- `total` is the sum of all marks.
- `Marks.Count` gives the number of subjects.
- `(double)` converts the total into a double value so the division can produce a decimal result.

Example:

```text
Total Marks = 255
Number of Subjects = 3

Percentage = 255 / 3
           = 85%
```

**Important:** This calculation assumes every subject is out of 100 and all subjects have equal weight.

### 4. Display() Method

```csharp
foreach (var item in Marks)
{
    Console.WriteLine(item.Key + " : " + item.Value);
}
```

This displays all subject names and their marks.

Here:

- `item.Key` gives the subject name.
- `item.Value` gives the marks.

Finally:

```csharp
CalculatePercentage();
```

This calls the percentage calculation method.

## Step 2: Create Program.cs

### Program.cs

```csharp
using System;
using System.Collections.Generic;

namespace Lab3
{
    internal class Program
    {
        static Dictionary<int, Grade> students =
            new Dictionary<int, Grade>();

        static void Main(string[] args)
        {
            while (true)
            {
                Console.WriteLine("\n===== STUDENT GRADE BOOK =====");
                Console.WriteLine("1. Add / Update Student");
                Console.WriteLine("2. Display Reports");
                Console.WriteLine("3. Search Student");
                Console.WriteLine("4. Exit");

                Console.Write("Enter your choice: ");
                int choice = Convert.ToInt32(Console.ReadLine());

                switch (choice)
                {
                    case 1:
                        AddOrUpdateStudent();
                        break;

                    case 2:
                        DisplayReport();
                        break;

                    case 3:
                        SearchStudent();
                        break;

                    case 4:
                        Console.WriteLine("Program exited.");
                        return;

                    default:
                        Console.WriteLine("Invalid choice!");
                        break;
                }
            }
        }

        static void AddOrUpdateStudent()
        {
            Console.Write("Enter Student ID: ");
            int id = Convert.ToInt32(Console.ReadLine());

            Grade student;

            if (students.ContainsKey(id))
            {
                student = students[id];

                Console.WriteLine(
                    "Student already exists. Updating details..."
                );
            }
            else
            {
                student = new Grade();
                student.ID = id;

                Console.Write("Enter Student Name: ");
                student.Name = Console.ReadLine();

                students.Add(id, student);

                Console.WriteLine("Student added successfully!");
            }

            Console.Write("Enter number of subjects: ");
            int n = Convert.ToInt32(Console.ReadLine());

            for (int i = 0; i < n; i++)
            {
                Console.Write("Enter Subject Name: ");
                string subject = Console.ReadLine();

                Console.Write("Enter Marks: ");
                int marks = Convert.ToInt32(Console.ReadLine());

                if (student.Marks.ContainsKey(subject))
                {
                    student.Marks[subject] = marks;
                }
                else
                {
                    student.Marks.Add(subject, marks);
                }
            }

            Console.WriteLine("Student details updated successfully!");
        }

        static void DisplayReport()
        {
            if (students.Count == 0)
            {
                Console.WriteLine("No student records found.");
                return;
            }

            Console.WriteLine("\n========== STUDENT GRADE BOOK ==========");

            foreach (var item in students)
            {
                item.Value.Display();
                Console.WriteLine("----------------------------------------");
            }
        }

        static void SearchStudent()
        {
            Console.Write("Enter Student ID: ");
            int id = Convert.ToInt32(Console.ReadLine());

            if (students.ContainsKey(id))
            {
                students[id].Display();
            }
            else
            {
                Console.WriteLine("Student not found.");
            }
        }
    }
}
```

## Explanation of Program.cs

### 1. Create Dictionary

```csharp
static Dictionary<int, Grade> students =
    new Dictionary<int, Grade>();
```

This creates a dictionary named `students`.

Here:

- `int` is the key type, which stores the Student ID.
- `Grade` is the value type, which stores the complete Grade object.

For example:

| Key (Student ID) | Value |
|---|---|
| 101 | Grade object of student 101 |
| 102 | Grade object of student 102 |
| 103 | Grade object of student 103 |

Each student ID is used to access the student's complete details.

### 2. AddOrUpdateStudent() Method

This method adds a new student or updates the marks of an existing student.

```csharp
Grade student;
```

This declares a variable that can store a Grade object.

### 3. ContainsKey()

```csharp
if (students.ContainsKey(id))
```

`ContainsKey()` checks whether the entered student ID already exists in the dictionary.

For example:

```text
Student ID = 101
```

If ID 101 exists, the condition becomes true.

If it does not exist, the condition becomes false.

### 4. Access Existing Student

```csharp
student = students[id];
```

This gets the Grade object stored against the entered student ID.

For example:

```csharp
student = students[101];
```

This accesses the Grade object whose key is 101.

### 5. Create a New Student

```csharp
student = new Grade();
student.ID = id;
```

This creates a new Grade object and stores the entered student ID.

```csharp
students.Add(id, student);
```

This adds the student ID and Grade object as a key-value pair in the dictionary.

### 6. Accept Subject Marks

```csharp
for (int i = 0; i < n; i++)
```

This loop runs according to the number of subjects entered by the user.

For example, if the user enters 3, the loop runs three times.

```csharp
string subject = Console.ReadLine();
int marks = Convert.ToInt32(Console.ReadLine());
```

These statements accept the subject name and marks.

### 7. Check Whether Subject Already Exists

```csharp
if (student.Marks.ContainsKey(subject))
{
    student.Marks[subject] = marks;
}
else
{
    student.Marks.Add(subject, marks);
}
```

This checks whether the subject already exists in the student's Marks dictionary.

If the subject exists, its marks are updated.

If the subject does not exist, a new subject and marks entry is added.

Example:

Initially:

```text
C# = 70
```

If the user enters C# again with marks 90, the old marks are replaced.

Updated result:

```text
C# = 90
```

### 8. DisplayReport() Method

```csharp
if (students.Count == 0)
{
    Console.WriteLine("No student records found.");
    return;
}
```

This checks whether the dictionary is empty.

If there are no students, it displays a message and exits the method.

```csharp
foreach (var item in students)
{
    item.Value.Display();
}
```

This loop visits every key-value pair in the dictionary.

- `item.Key` represents the Student ID.
- `item.Value` represents the Grade object.

We use `item.Value.Display()` because the Grade object contains the Display method.

### 9. SearchStudent() Method

```csharp
if (students.ContainsKey(id))
{
    students[id].Display();
}
```

This checks whether the student ID exists.

If it exists, the program accesses the Grade object and displays the student's details.

Otherwise, it displays:

```text
Student not found.
```

## Sample Output

```text
===== STUDENT GRADE BOOK =====
1. Add / Update Student
2. Display Reports
3. Search Student
4. Exit

Enter your choice: 1

Enter Student ID: 101
Enter Student Name: Amit

Student added successfully!

Enter number of subjects: 3

Enter Subject Name: C#
Enter Marks: 80

Enter Subject Name: Java
Enter Marks: 90

Enter Subject Name: Python
Enter Marks: 85

Student details updated successfully!
```

When the user selects Display Reports:

```text
========== STUDENT GRADE BOOK ==========

Student ID   : 101
Student Name : Amit
Subject-wise Marks:
C# : 80
Java : 90
Python : 85

Percentage: 85%

----------------------------------------
```

When the user searches for Student ID 101:

```text
Enter Student ID: 101

Student ID   : 101
Student Name : Amit
Subject-wise Marks:
C# : 80
Java : 90
Python : 85

Percentage: 85%
```

When the user searches for an ID that does not exist:

```text
Enter Student ID: 105

Student not found.
```

---

# 4. Difference Between List and Dictionary

| List | Dictionary |
|---|---|
| Stores elements in a collection. | Stores key-value pairs. |
| Access elements using an index. | Access values using a key. |
| Duplicate values are allowed. | Keys must be unique. |
| Example: `List<Student>` | Example: `Dictionary<int, Grade>` |
| Uses `Add()` to add an element. | Uses `Add(key, value)` to add an entry. |
| Uses `Find()` to search by a condition. | Uses `ContainsKey()` to check a key. |

### Example of List

```csharp
List<string> names = new List<string>();

names.Add("Amit");
names.Add("Neha");

Console.WriteLine(names[0]);
```

Output:

```text
Amit
```

### Example of Dictionary

```csharp
Dictionary<int, string> students =
    new Dictionary<int, string>();

students.Add(101, "Amit");
students.Add(102, "Neha");

Console.WriteLine(students[101]);
```

Output:

```text
Amit
```

---

# 5. Important Concepts to Remember

| Concept | Meaning |
|---|---|
| Class | Blueprint for creating objects. |
| Object | Instance of a class. |
| List | Collection that stores multiple elements. |
| Dictionary | Collection that stores key-value pairs. |
| `Add()` | Adds an element or key-value pair. |
| `Remove()` | Removes an element. |
| `Find()` | Searches for an element using a condition. |
| `ContainsKey()` | Checks whether a key exists. |
| `Count` | Returns the number of elements. |
| `foreach` | Iterates through collection elements. |
| `null` | Represents the absence of an object reference. |

# 6. Common Mistakes

### Mistake 1: Forgetting the Namespace

Incorrect:

```csharp
List<int> numbers = new List<int>();
```

If the required namespace is not included, the compiler may not recognize `List<T>`.

Correct:

```csharp
using System.Collections.Generic;
```

### Mistake 2: Adding the Same Dictionary Key Twice

Incorrect:

```csharp
students.Add(101, "Amit");
students.Add(101, "Neha");
```

This causes an error because dictionary keys must be unique.

### Mistake 3: Confusing List and Dictionary Add()

List:

```csharp
students.Add(student);
```

Dictionary:

```csharp
students.Add(id, student);
```

A List adds an element, while a Dictionary adds a key-value pair.

### Mistake 4: Accessing a Missing Dictionary Key

```csharp
Console.WriteLine(students[105]);
```

If key 105 does not exist, accessing it this way causes a `KeyNotFoundException`.

First check:

```csharp
if (students.ContainsKey(105))
{
    Console.WriteLine(students[105]);
}
```

### Mistake 5: Dividing by Zero in Percentage Calculation

If the Marks dictionary is empty, `Marks.Count` is zero.

Before calculating the percentage, check whether any subject marks are available.

For example:

```csharp
if (Marks.Count == 0)
{
    Console.WriteLine("No marks available.");
    return;
}
```

Place this check before dividing by `Marks.Count` in `CalculatePercentage()`.

### Mistake 6: Forgetting to Add the Object to the List

Creating an object does not automatically add it to a list.

```csharp
Student s = new Student();
```

To store it in the list, write:

```csharp
students.Add(s);
```

# 7. Important Viva Questions with Answers

**Q1. What is a collection in C#?**

A collection is used to store and manage multiple elements or objects.

**Q2. What is List in C#?**

List is a collection that stores multiple elements of the same data type. Its size can change dynamically.

**Q3. What is Dictionary in C#?**

Dictionary is a collection that stores data in key-value pairs.

**Q4. What is the difference between an array and a List?**

An array has a fixed size, whereas a List can grow or shrink dynamically.

**Q5. Can a List store objects?**

Yes. For example:

```csharp
List<Student> students = new List<Student>();
```

**Q6. What is the use of Add()?**

The Add method inserts an element into a List or a key-value pair into a Dictionary.

**Q7. What is ContainsKey()?**

It checks whether a particular key exists in a Dictionary.

**Q8. Can a Dictionary contain duplicate keys?**

No. Every key in a Dictionary must be unique.

**Q9. What is the use of Find()?**

Find searches a List for an element that matches a given condition.

**Q10. What is foreach?**

The foreach loop is used to visit each element of a collection.

**Q11. What is the use of Count?**

Count returns the total number of elements in a collection.

**Q12. What is the use of Remove()?**

Remove deletes an element from a collection.

**Q13. What is a key-value pair?**

A key-value pair contains a unique key associated with a value.

**Q14. Why is Dictionary used in the Grade Book program?**

Dictionary allows us to access a student's Grade object using the student's unique ID.

**Q15. What is the use of Marks.Values?**

It returns all the values stored in the Marks dictionary, which are the students' marks.

**Q16. What is the use of item.Key and item.Value?**

`item.Key` accesses the key, and `item.Value` accesses the corresponding value in a Dictionary.

**Q17. What happens if we search for a student who does not exist?**

The program displays "Student not found."

**Q18. Why do we use Convert.ToInt32()?**

It converts the string entered by the user into an integer.

**Q19. What does `return` do in the Exit case?**

It terminates the Main method and ends the program.

**Q20. Can we store class objects inside a Dictionary?**

Yes. For example:

```csharp
Dictionary<int, Grade> students =
    new Dictionary<int, Grade>();
```

# 8. Conclusion

In this lab, we learned how to use collections in C#.

- `List<T>` is used to store multiple elements or objects.
- `Dictionary<TKey, TValue>` is used to store data in key-value pairs.
- We implemented a Student Management System using List.
- We implemented a Shopping Cart System using List.
- We implemented a Student Grade Book using Dictionary.

Collections make it easier to store, search, update, delete, and manage multiple records in C# applications.
