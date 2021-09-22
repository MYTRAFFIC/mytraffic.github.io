---
title: "Join algorithms"
author: awendlinger
categories: [ tech ]
layout: post
image: /assets/images/joins.jpg
---

Chances are, if you have used SQL, you have used the JOIN keyword. But do you know how a JOIN is actually done? In this article, we will review different strategies used to perform the JOIN operation: the algorithms behind, their performance and memory trade-offs.


*This article is part of our learning series, where we try to develop an understanding of how tools we frequently use work. This is part of the learning materials for our junior engineers.*

## Motivation

{% include question.html content="Why would I learn how to do what my database already does for me?" %}

SQL is a  [leaky abstraction](https://www.joelonsoftware.com/2002/11/11/the-law-of-leaky-abstractions/). Most of the time you don't need to know how it works. But as soon as performance is involved, you need to know how the queries are actually executed. If you have ever spent some time banging your head against the wall trying to make sense of the output of an `EXPLAIN ANALYZE` query in Postgres or of the Spark Web UI (who both explicitly relay the join algorithm that is used to the user), you'll know what I mean.

## Requirements

To get as much as possible from this article, you need:

- A basic understanding of Python.
- Familiarity with SQL.
- Notions of [computational complexity](https://en.wikipedia.org/wiki/Computational_complexity) are a plus, but not mandatory (just skip the parts mentioning complexity).

## Scope

SQL is used in *a lot* of places: relational databases, distributed computing (Spark, Presto), ... Then we have multiple kinds of joins (INNER, LEFT, CROSS, ...): explaining all of this in depth won't really be possible in a article. Instead, we are going to answer this question: How would one implement the JOIN in the following query?

```sql
SELECT
  employee.id,
  employee.name,
  company.name as company_name
FROM employee
INNER JOIN company ON company.id = employee.company_id
```

We assume the following tables exist:

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

Note that we chose a simple case, an employee will always have exactly one company:

- `company.id` is a primary key: no two companies will have the same id.
- `employee.company_id` is a foreign key: a matching company will exist for every employee.

We are going to use Python. Our goal will be to implement the following function:

```python
def perform_join(
    employees: List[Employee], companies: List[Company]
) -> List[Result]:
    ...

@dataclass
class Company:
    id: int
    name: str

@dataclass
class Employee:
    id: int
    company_id: int
    name: str

@dataclass
class Result:
    id: int
    name: str
    company_name: str
```

## The algorithms

Let's dive in! We are going to start by explaining and implementing the various algorithms, and analyzing their time complexity.


### The naïve approach: nested loop

The immediate solution is to brute force it: for each employee, iterate over all companies to find the one with the proper id.

```python
def perform_nested_loop_join(
    employees: List[Employee], companies: List[Company]
) -> List[Result]:
    results = []

    for employee in employees:
        for company in companies:
            if employee.company_id == company.id:
                results.append(make_result(employee, company))
                break

    return results

def make_result(employee: Employee, company: Company) -> Result:
    return Result(id=employee.id, name=employee.name, company_name=company.name)
```

Let `N` the number of employees, `M` the number of companies. The time complexity is `O(N * M)` with no additinal memory required. If there are a lot of companies and/or employees, this is going to be really slow. But it still has some legitimate use cases:

- Due to its simplicity, it is very flexible. If you JOIN condition is too complicated to be used with other algorithms, this one won't care: just change the condition in the `if` clause.

- Again, it's really simple: if the performance hit does not matter in your use case, got for it. (To be fair, this will not often be the case: quadratic complexity is going to be a big cost even on medium-sized datasets).


### The "I studied CS" approach: hash join

To improve performance, we can use a [hash table](https://yourbasic.org/algorithms/hash-tables-explained/) (in Python, a dictionnary) that maps a company's id to the company. This way we can, for each employee, efficiently lookup the matching company.

```python
def perform_hash_join(
    employees: List[Employee], companies: List[Company]
) -> List[Result]:
    result = []

    # Create a hash table that maps a company id to the company
    company_by_id = {c.id: c for c in companies}

    # Loop over employees
    for employee in employees:
        company = company_by_id[employee.company_id]
        result.append(make_result(employee, company))

    return result
```

For this algorithm the time complexity is `O(N + M)`:

- `O(M)` to compute the hash table.
- `O(N)` to perform a lookup for each employee (because hash table lookup is `O(1)` on average)

The additional memory complexity is `O(M)`.



### The balanced approach: sort-merge join

As the name implies, this algorithm is divided in two steps. First we sort both input datasets. Then, we merge them by looping over both simultaneously.

```python
def perform_sort_merge_join(
    employees: List[Employee], companies: List[Company]
) -> List[Result]:
    # First: sort the inputs by join key
    employees = sorted(employees, key=lambda e: e.company_id)
    companies = sorted(companies, key=lambda c: c.id)

    # Then loop over both simultaneously
    result = []
    company_idx = 0

    for employee in employees:
        while companies[company_idx].id < employee.company_id:
            company_idx += 1
        result.append(make_result(employee, companies[company_idx]))

    return result

```


This one can be hard to grok, and is a bit hard to explain on a written medium. It is very similar to the merge phase in the merge-sort sorting algorithm, about which you can easily find well-made [educational videos](https://youtu.be/alJswNJ4P3U?t=894).

Here, the time complexity is `O(N * log(N) + M * log(M))`:

- `O(M*logM + N*logN)` for sort phase

- `O(M + N)` for the merge phase.

The memory complexity depends on the sorting algorithm that is used.
Note that if the input datasets are already sorted, this has the same time complexity as a hash join, without requiring any additional memory.

![I heard you like sorting and merging, so I put a merge-sort sort before the merge phase of you sort-merge algorithm.](/assets/images/join-merge-sort-im-so-funny.jpeg)



## Which one do I choose ?

If you only look at asymptotic complexity, hash join looks like a clear winner. However, hash tables consume *a lot* of memory, which can be a no-go if your datasets are big, so it depends on your use case:

- If the inputs are already sorted, use a sort-merge join.
- If you already have a hash table on the columns used for the join condition for one of you datasets, use a hash join.
- If one of your datasets is small enough (small enough meaning "creating a hash table won't blow your memory budget"), use a hash join.
- ~~If you are feeling adventurous, use a nested loop join~~
- Else, use a sort-merge join.


{% include question.html content="Why in the world would I have a hash table on just the right columns lying around, or already sorted inputs?" %}

Well that's a good question! In the context of databases, this is actually often the case. Let's look back at our table definitions. `company.id` is a primary key: to enforce its unicity, an index is created. In Postgres (and I think most of the relational databases), the default index type is a BTree, in which values are sorted. `employee.company_id` is not necessarily indexed, but you could (and very likely should) create on index on this column. This way, you can perform a sort-merge join without sorting.

But BTrees are not the only index types: you can also create hash indexes. With them, you can easily meet the "If you already have a hash table on the columns used for the join condition" criteria.



## Closing words

Thank you for reading this article! I hope I helped demystify a component we too-often treat as a complete black box. This article was limited to a very simple case, but the algorithms generalize well to other situations: the code would be more complicated in order to deal with edge cases, but the algorithms stay the same. If you want to deep dive into the inner workings of relational database, I strongly recommend [Use the index Luke](https://use-the-index-luke.com/).
