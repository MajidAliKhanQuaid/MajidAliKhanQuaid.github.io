---
layout: post
title: "Concat column values in SQL !"
date: 2019-08-18 10:22:41 +0500
categories: sql
---

Stuff is an equivalent method to string replacement in SQL. By specifying the indices we can replace text at our desired location.

```sql
SELECT STUFF(
(
	SELECT ',' + [ContactName] FROM [Northwind].[dbo].[Customers] ORDER BY [CustomerID] FOR XML PATH('')
),
1, 1, ''
) AS Employees
```

To understand the working of this expression, lets split understand it line by line. Initially, we have to look into the main query which represents a simple SELECT statement except the last part which uses keywords XML and PATH. Here, results of the select statement is convereted to XML and the tag is represented

```sql
SELECT ',' + [ContactName] FROM [Northwind].[dbo].[Customers] ORDER BY [CustomerID] FOR XML PATH('Name')
```

The above script produces output as

```text
<Name>,abc</Name><Name>,efg</Name>.....<Name>,xyz</Name>
```

But if we don't specify any arguments to PATH i.e.

```sql
SELECT ',' + [ContactName] FROM [Northwind].[dbo].[Customers] ORDER BY [CustomerID] FOR XML PATH('')
```

We get an ouput like i.e.

```text
,abc,efg.....,xyz
```

So now, we have a problem ! The above query results in a proceeding comma at the beginning so lets get rid of it using SQL's STUFF

```sql
-- So we use STUFF to replace the contents of string a defined position
SELECT STUFF(',firstname,secondname,thirdname', 1, 1, '')
```
