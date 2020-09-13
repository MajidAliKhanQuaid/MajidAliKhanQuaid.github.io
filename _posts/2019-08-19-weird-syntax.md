---
layout: post
title: "C# Weird Syntaxes !"
date: 2019-08-19 10:22:41 +0500
categories: cs
---

While working on nopCommerce, i found this syntax on my place and took me by surprise due to its resemblance with Python.

```cs
public (string name, string gender, int age) GetPerson()
{
    return ("ANY STRING", "Male", 25);
}
```

Now how would that get called, exactly the same way !!

```cs
(string name, string gender, int age) = GetPerson();
```

Local function is a concept you must have come across in javascript development, where you've a function contained in another function. Microsoft, has it copied into C# as well. Now, with the following syntax you should be able to use functional locally too.

```cs
void PrintResults(){

    // creating local functions within functions

    void PrintCsScore(){
        Console.WriteLine("Got 75% Marks in CS");
    }

    void PrintMathScore(){
        Console.WriteLine("Got 90% Marks in Mathematics");
    }
    //
    PrintCsScore();
    PrintMathScore();
}
```
