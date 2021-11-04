---
title: "TODO"
author: awendlinger
categories: [ tech ]
layout: post
description: "TODO"
image: TODO
---

JOIN is a fundamental operator in SQL: it puts the "relational" in relational databases, and is the main means of combining multiple data sources in any SQL engine. We previously learned about the [various join algorithms](TODO); in this article, we are going to focus on how JOIN is performed in distributed SQL engines (for example Spark, Presto, Athena, ...).

TODO:
- put link and tag ?
- refactor ?

*This article is part of our learning series, where we try to develop an understanding of how tools we frequently use work. This is part of the learning materials for our junior engineers.*


## Motivation

As soon as performance starts to matter (if you are using distributed computed, this should be the case), one can't simply ignore how distributed SQL engines implement a JOIN operator. Indeed, implementation details are going to dictacte how data is moved between machines, and data movement is a likely bottleneck of distributed computations. If you have ever wondered what Spark experts mean when they say they want to "avoid a shuffle", you have come to the right place.

## Scope

We are going to use the same **TODO: fil rouge** as in the [previous article](TODO): how is the following query performed ?

```sql
SELECT
  employee.id,
  employee.name,
  company.name as company_name
FROM employee
INNER JOIN company ON company.id = employee.company_id
```

Given the following tables:


```sql
CREATE TABLE company (
  id integer PRIMARY KEY,
  name text
);

CREATE TABLE employee (
  id integer PRIMARY KEY,
  company_id integer NOT NULL REFERENCES company(id),
  name text
);
```

But this time, we are going to place ourselves in a distributed setting: rather that doing the computation on a single machine, we are going to perform it on 3 machines (the actual number does not really matter, but 10000 machines would be hard
