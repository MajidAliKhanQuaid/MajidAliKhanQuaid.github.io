---
layout: post
title: "Operator Overloading"
date: 2019-08-27 10:22:41 +0500
categories: c# operator overloading
---

# Operator Overloading

Say we have a class Point and we want to add two instances of it i.e. p1 + p2. By default, we would not get it to work because compiler applies '+' operator on values types. So, to make it work for custom type , we'll overload the '+' operator. The same applies for all arithmetic operators.

```cs
public class Point
{
    public int X { get; set; }
    public int Y { get; set; }
    public Point(int xPos, int yPos)
    {
        X = xPos;
        Y = yPos;
    }
}

public static Point operator +(Point p1, Point p2)
{
    return new Point(p1.X + p2.X, p1.Y + p2.Y);
}

// Now in main

Point p1 = new Point(10,20);
Point p2 = new Point(10,20);
Point p3 = p1 + p2;

```
