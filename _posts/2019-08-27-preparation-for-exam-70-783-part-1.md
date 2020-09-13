---
layout: post
title: "Exam 70-783 Programming in C# ! Part # 1 (Serialization)"
date: 2019-08-27 10:22:41 +0500
categories: c# microsoft certification exam70-783 serialization part-1
---

Binary serialization

## Student

```c#

    [Serializable]
    public class Student
    {
        public int Id { get; set; } = 0;
        public string Name { get; set; } = "N/A";
        public double Fee { get; set; } = 0.0;
        public List<Favourite> Favourites { get; set; } = new List<Favourite>();

        public void Print()
        {
            Console.WriteLine();
            Console.WriteLine($"----------- New Record ---------- ");
            Console.WriteLine($"Student # {Id}");
            Console.WriteLine($"Student # {Name}");
            Console.WriteLine($"Student # {Fee}");
            if(Favourites != null)
            {
                for(int i = 0; i <  Favourites.Count; i++)
                {
                    var favourite = Favourites[i];
                    favourite.Print(Name);
                }
            }
        }

    }
```

## Favourite

```c#
    [Serializable]
    public class Favourite
    {
        public int Id { get; set; } = 0;
        public string Name { get; set; } = "N/A";
        public void Print(string _studentName)
        {
            Console.WriteLine($"\t {_studentName} likes {Name}");
        }
    }
```

# Serializers

## Binary Serializers

```c#

    public static class StudentBinarySerializer
    {
        public static bool SerializeObject(this List<Student> serializable, string path)
        {
            try
            {
                IFormatter formatter = new BinaryFormatter();
                Stream stream = new FileStream(path, FileMode.Create, FileAccess.Write, FileShare.None);
                formatter.Serialize(stream, serializable);
                stream.Flush();
                stream.Close();
                //
                return true;
            }
            catch (Exception)
            {
                return false;
            }
        }


        public static List<Student> DeserializeObject(string fileName)
        {
            try
            {
                string path = Path.Combine(AppDomain.CurrentDomain.BaseDirectory, fileName);
                IFormatter formatter = new BinaryFormatter();
                Stream stream = new FileStream(path, FileMode.Open, FileAccess.Read, FileShare.None);
                var students = formatter.Deserialize(stream) as List<Student>;
                stream.Flush();
                stream.Close();
                //
                return students;
            }
            catch (Exception)
            {
                return null;
            }
        }

    }
```

Usage

```c#
// If files does not exist, then first serialize Student records
if (File.Exists(FILE_NAME) == false)
{
    var students = GetDummyRecords();
    if (students.SerializeObject(FILE_NAME) == false)
    {
        Console.WriteLine("*** Data Serialized failed, Please try again");
        Console.ReadKey();
        return;
    }
}
```
