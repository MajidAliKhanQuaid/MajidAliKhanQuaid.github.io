---
layout: post
title:  "Extension Methods"
date:   2019-08-27 10:22:41 +0500
categories: c# operator extension method
---


# Extension Methods
If you use LINQ, you must have seen the `.Where` method. It would only appear in intelliSence when you import the `System.Linq` namespace. How does it come ? So that' where the extension methods comes in which facilitates the developers to expands the functionalities of their classes with out having to make a change with the prototyps. 

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

public class Swapper
{
	public static new SwapXY(this Point p){
		if(p != null){
			int hold = p.X;
			p.X = p.Y;
			p.Y = hold;
		}
	}
}

```

Now, when you use a `.` operator after a Point class object; you must see a SwapXY if you are in the Swapper namespace
