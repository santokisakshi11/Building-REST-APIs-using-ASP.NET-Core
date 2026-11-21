
# Lab 02: Class and Objects in C#

## 1. Objective

To understand classes and objects in C#.

## 2. Introduction

A class is a blueprint used to create objects.

An object is an instance of a class.

## 3. Practical Implementation

### Program 1: Faculty Details

**Problem Statement:**

Create a Faculty class to store faculty ID, name, age, weight and height.

**Source Code:**

```csharp
using System;

namespace Lab2
{
    internal class Faculty
    {
        int id;
        string name;
        int age;
        double weight;
        double height;

        public void GetFacultyDetails()
        {
            Console.WriteLine("Enter faculty id:");
            id = Convert.ToInt32(Console.ReadLine());

            Console.WriteLine("Enter faculty name:");
            name = Console.ReadLine();

            Console.WriteLine("Enter faculty age:");
            age = Convert.ToInt32(Console.ReadLine());

            Console.WriteLine("Enter faculty weight:");
            weight = Convert.ToDouble(Console.ReadLine());

            Console.WriteLine("Enter faculty height:");
            height = Convert.ToDouble(Console.ReadLine());
        }
    }
}
```

## 4. Conclusion

In this program, we learned how to create a class,
declare data members and accept user input.
