---
layout: post
title:  "Exam 70-783 Programming in C# ! Part - 2 (Reflection)"
date:   2019-08-27 10:22:41 +0500
categories: c# microsoft certification exam70-783 reflection part-2
---

# Reflection
Reflection namespace System.Reflection has a variety of different class and methods to faciliate runtime code generation. Runtime code generation is something that adds another level of dynamicity to the code i.e. you know name of the class then you can easily create its instance using Activator.CreateInstance method. Such is the power of reflection that you can even dynamically loop through the properties of class just like a normal array. Not only the data members, Reflection also enables the developer too seep through each element of the method i.e. members, properties, function etc.

## Anatomy of the Object using Reflection

```cs
Student student = Activator.CreateInstance<Student>();
            //
Console.WriteLine();
Console.WriteLine("*** Properties ***");
PropertyInfo[] propertyInfo = student.GetType().GetProperties();
foreach (var pi in propertyInfo)
{
	string getterAccessor = pi.GetGetMethod().IsPublic ? "public" : "not public";
	string setterAccessor = pi.GetSetMethod().IsPublic ? "public" : "not public"; 
	Console.WriteLine($"{pi.Name} has a '{getterAccessor}' getter and '{setterAccessor}' setter");
}
            //
Console.WriteLine();
Console.WriteLine("*** Methods ***");
MemberInfo[] memberInfo =  student.GetType().GetMembers();
foreach (var mi in memberInfo)
{
	Console.WriteLine(mi.Name);
}
            //
Console.WriteLine();
Console.WriteLine("*** Fields ***");
FieldInfo[] fieldInfo = student.GetType().GetFields();
foreach (var fi in fieldInfo)
{
	Console.WriteLine(fi.Name);
}
```

