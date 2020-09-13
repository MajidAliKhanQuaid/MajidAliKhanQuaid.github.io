---
layout: post
title: "Sql Server Teardown !"
date: 2019-10-20 10:22:41 +0500
categories: sql server
---

Get list of database object via following queries

```sql
-- Getting a list of `Stored Procedures`
SELECT * FROM [master].INFORMATION_SCHEMA.ROUTINES WHERE [ROUTINE_TYPE] = 'PROCEDURE'

-- Getting a list of `Tables`
SELECT * FROM [master].INFORMATION_SCHEMA.TABLES WHERE [TABLE_TYPE] <> 'BASE TABLE'

-- Getting a list of `Views`
SELECT * FROM [master].INFORMATION_SCHEMA.TABLES WHERE [TABLE_TYPE] = 'VIEW'
```

Sql Server 2016 and above comes up with this great feature of parsing the json which gives users the ability to get queryable insights from json payloads in their logs table. Say, if you've a table named `Logs` and it has the following json in its `Payload` column like this

```json
{
  "error": "Object reference not set to an instance of an object",
  "dated": "07-03-2020"
}
```

Then you can easily group or filter your logs on the basis of fields contained in your json payload i.e.

```sql
SELECT JSON_VALUE([Payload], '$.error'), JSON_VALUE([Payload], '$.dated')
FROM [Logs]
```
