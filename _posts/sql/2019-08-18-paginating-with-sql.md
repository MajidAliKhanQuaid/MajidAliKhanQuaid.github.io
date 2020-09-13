---
layout: post
title:  "Paginating with SQL!"
date:   2019-08-18 10:22:41 +0500
categories: sql
---

Pagniation, is logical splitting of a large data into small (managable) piece of data chunks. The splitting is based on parameters i.e. OFFSET, PAGESIZE. Where, the OFFSET represents the number of records to be skipped while the PAGESIZE attributes to the number of rows of the data to be extracted.

```sql
SET NOCOUNT ON

-- @PAGE_INDEX must come from SP
-- @PAGE_INDEX must be positive integer

DECLARE @PAGE_SIZE INT = 20
DECLARE @SKIP_ROWS INT = @PAGE_INDEX * @PAGE_SIZE
SELECT [CustomerID] ,[CompanyName] ,[ContactName] ,[ContactTitle]
FROM [Northwind].[dbo].[Customers] 
ORDER BY [CustomerID] 
OFFSET @SKIP_ROWS ROWS 
FETCH NEXT @PAGE_SIZE ROWS ONLY
```

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
