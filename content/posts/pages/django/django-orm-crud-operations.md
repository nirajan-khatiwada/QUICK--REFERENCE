---
title: "Django ORM Mastery: CRUD, Filtering & Aggregation"
slug: "django-orm-crud-operations"
date: 2025-01-04
description: "Deep dive into Django ORM. Learn CRUD operations, filtering with Q objects, aggregation, F-expressions for efficient queries."
showToc: true
weight: 3
series: ["Django"]
categories: ["Django"]
tags: ["Django", "Python", "Database", "ORM", "Models", "Backend"]
summary: "Deep dive into Django Models covering field types, model options, database configuration, migrations, and model methods. Essential knowledge for database operations in Django."
images: ["/images/django.jpg"]
---


# 20. CRUD Operations in Django

## 20.1 Inserting Data using `save()` method
To insert data into the database, 

### Using save() method
we create an instance of the model and call the `save()` method on it.
```python
from myapp.models import Person
person = Person(name='Alice', age=25)
person.save()
```
Here we create a new object of the `Person` model with the name `Alice` and age `25` and save it to the database.


### Using create() method
We can also use the `create()` method to create and save an object in a single step.
```python
person = Person.objects.create(name='Alice', age=25)
```
This return the object that is created.

>Note:`save()` method is used to save the data in database and `create()` method is used to create and save the data in database.


## 20.2 Reading Data
### 20.2.1 Get all data
To get all the data from the database we use the `all()` method.
```python
from myapp.models import Person
persons = Person.objects.all()
```
This returns all the objects of the `Person` model.ie return all the rows of the table.



## 20.3 Updating Data
To update the data in the database, we first get the object we want to update, change its attributes, and then call the `save()` method on it.
```python
from myapp.models import Person
person = Person.objects.get(id=1)
person.age = 26
person.save()
```

## 20.4 Deleting Data
To delete data from the database, we first get the object we want to delete and then call the `delete()` method on it.
```python
from myapp.models import Person
person = Person.objects.get(id=1)
person.delete()
```

## 20.4 Deleting all data
To delete all the data from the database we use the `all()` method to get all the objects and then call the `delete()` method on it.
```python
from myapp.models import Person
persons = Person.objects.all()
persons.delete()
```

## 20.5 Filtering Data
We can use the following methods to filter the data in the database.
### 20.5.1 filter()
The `filter()` method is used to filter the data based on the given condition.
```python
from myapp.models import Person
persons = Person.objects.filter(age=25)
```
In the above example, we are filtering the data based on the age of the person.It returns all the objects that have the age equal to 25 in the form of a queryset(List)

### 20.5.2 exclude()
The `exclude()` method is used to exclude the data based on the given condition.
```python
from myapp.models import Person
persons = Person.objects.exclude(age=25)
```
In the above example, we are excluding the data based on the age of the person.It returns all the objects that have the age not equal to 25 in the form of a queryset(List)

### 20.5.3 get()
The `get()` method is used to get the single object based on the given condition.
```python
from myapp.models import Person
person = Person.objects.get(id=1)
```
In the above example, we are getting the object based on the id of the person.It returns the object that has the id equal to 1.

> Note:If there are more than one object or no object that satisfies the condition then `get()` will raise an error but `filter()` and `exclude()` will return an empty queryset.


## Operation that can be performed on queryset like `all()`,`filter()`,`exclude()`

### 20.6.order_by()
The `order_by()` method is used to order the data based on the given field.
```python
from myapp.models import Person
persons = Person.objects.all().order_by('field_name')
```

In the above example, we are ordering the data based on the field name in ascending order. If you want to order the data in descending order then you can use the `-` sign before the field name.
```python
from myapp.models import Person
persons = Person.objects.all().order_by('-field_name')
```

### 20.7.count()
The `count()` method is used to count the number of objects in the queryset.
```python
from myapp.models import Person
count = Person.objects.all().count()
```
### 20.8 reverse()
The `reverse()` method is used to reverse the order of the queryset.
```python
from myapp.models import Person
persons = Person.objects.all().reverse()
```

### 20.9.first()
The `first()` method is used to get the first object from the queryset.
```python
from myapp.models import Person
person = Person.objects.all().first()
```

### 20.10.last()
The `last()` method is used to get the last object from the queryset.
```python
from myapp.models import Person
person = Person.objects.all().last()
```

### 20.11.exists()
The `exists()` method is used to check if the queryset contains any objects.
```python
from myapp.models import Person
exists = Person.objects.all().exists()
```

## 21. And, Or, Not in filter
We can use `&` for `and`, `|` for `or`, and `~` for `not` in the filter method.

Performing `and` operation in filter
```python
from myapp.models import Person
persons = Person.objects.filter(age=25, name='Alice')
```
or 
```python
from myapp.models import Person
from django.db.models import Q
persons = Person.objects.filter(Q(age=25) & Q(name='Alice'))
```
Performing `or` operation in filter
```python
from myapp.models import Person
persons = Person.objects.filter(Q(age=25) | Q(name='Alice'))
```
Performing `not` operation in filter
```python
from myapp.models import Person
persons = Person.objects.filter(~Q(age=25))
```

or

```python
from myapp.models import Person
persons = Person.objects.exclude(age=25)
```

### 21.1 Combining `and`, `or`, `not` operation in filter,get,exclude
```python
from myapp.models import Person
from django.db.models import Q
persons = Person.objects.filter(Q(age=25) & Q(name='Alice') | ~Q(age=25))
```

## 22. Comparision operators in filter,get,exclude
We can use the following comparision operators in the filter,get,exclude method.

## 22.1 Comparision operators for integer

1.greater than `gt`
```python
from myapp.models import Person
persons = Person.objects.filter(age__gt=25)
```
2.greater than or equal to `gte`
```python
from myapp.models import Person
persons = Person.objects.filter(age__gte=25)
```

3.less than `lt`
```python
from myapp.models import Person
persons = Person.objects.filter(age__lt=25)
```

4.less than or equal to `lte`
```python
from myapp.models import Person
persons = Person.objects.filter(age__lte=25)
```

5.equal
```python
from myapp.models import Person
persons = Person.objects.filter(age=25)
```


## 22.2 Comparision operators for string

1.exact
```python
from myapp.models import Person
persons = Person.objects.filter(name__exact='Alice')
```

2.iexact
```python
from myapp.models import Person
persons = Person.objects.filter(name__iexact='alice')
```

3.contains
```python
from myapp.models import Person
persons = Person.objects.filter(name__contains='li')
```

4.icontains
```python
from myapp.models import Person
persons = Person.objects.filter(name__icontains='li')
```

5.startswith
```python
from myapp.models import Person
persons = Person.objects.filter(name__startswith='A')
```

6.istartswith
```python
from myapp.models import Person
persons = Person.objects.filter(name__istartswith='a')
```

7.endswith
```python
from myapp.models import Person
persons = Person.objects.filter(name__endswith='e')
```

8.iendswith
```python
from myapp.models import Person
persons = Person.objects.filter(name__iendswith='e')
```

9.in
```python
from myapp.models import Person
persons = Person.objects.filter(name__in=['Alice','Bob'])
```

> Note:i in `iexact`,`icontains`,`istartswith`,`iendswith` stands for case-insensitive.

## 23. Limiting Queryset
We can limit the number of objects returned by the queryset using the `[:n]` syntax.
```python
from myapp.models import Person
persons = Person.objects.all()[:5]
```

similirly to get query set from 5 to 10 we can use
```python
from myapp.models import Person
persons = Person.objects.all()[5:10]
```


## 24. Aggregation
Its used to perform some operation on the queryset like counting the number of objects, finding the average, sum, min, max, etc.

### 24.1 Count
The `count()` method is used to count the number of objects in the queryset.
```python
from myapp.models import Person
count = Person.objects.all().count()
```

### 24.2 Sum
The `sum()` method is used to find the sum of the field in the queryset.
```python
from myapp.models import Person
sum = Person.objects.all().aggregate(Sum('field_name'))
```

### 24.3 Avg
The `avg()` method is used to find the average of the field in the queryset.
```python
from myapp.models import Person
avg = Person.objects.all().aggregate(Avg('field_name'))
```

### 24.4 Min
The `min()` method is used to find the minimum value of the field in the queryset.
```python
from myapp.models import Person
min = Person.objects.all().aggregate(Min('field_name'))
```

### 24.5 Max
The `max()` method is used to find the maximum value of the field in the queryset.
```python
from myapp.models import Person
max = Person.objects.all().aggregate(Max('field_name'))
```



### 24.6 Count
The `count()` method is used to count the number of objects in the queryset.
```python
from myapp.models import Person
count = Person.objects.all().aggregate(Count('field_name'))
```

### 24.7 Combining Aggregation
We can combine the aggregation methods to perform multiple operations on the queryset.
```python
from myapp.models import Person
from django.db.models import Count, Sum, Avg, Min, Max
result = Person.objects.all().aggregate(
    Count('field_name'),
    Sum('field_name'),
    Avg('field_name'),
    Min('field_name'),
    Max('field_name')
)
```

### 24.8 Specifying the field
We can specify the name of the output field in the aggregation method.
```python
from myapp.models import Person
from django.db.models import Count, Sum, Avg, Min, Max
result = Person.objects.all().aggregate(
    field_name_count=Count('field_name'),
    field_name_sum=Sum('field_name'),
    field_name_avg=Avg('field_name'),
    field_name_min=Min('field_name'),
    field_name_max=Max('field_name'),
    difference=Max('field_name') - Min('field_name')
)
```
> By default the name of the output field is `field_name__count`, `field_name__sum`, `field_name__avg`, `field_name__min`, `field_name__max`.
> But we can specify the name of the output field by using 
```python
result = Person.objects.all().aggregate(
    name1=Count('field_name'),
    name2=Max('field_name')
)
```
>This will return the output field as `name1` and `name2` instead of `field_name__count` and `field_name__max`.


### 24.9 Using Filter in Aggregation
We can use the `filter()` method in the aggregation method to filter the data before performing the aggregation.
```python
from myapp.models import Person
from django.db.models import Count, Sum, Avg, Min, Max
result = Person.objects.filter(age=25).aggregate(
    Count('field_name'),
    Sum('field_name'),
    Avg('field_name'),
    Min('field_name'),
    Max('field_name')
)
```

## 25. F Expressions in Django

When we want to compare one field with another field in the same model, we can use **F expressions**. F expressions allow us to refer to model field values directly in queries, enabling comparisons and calculations without needing to fetch the data into Python memory.

### Basic Usage

```python
from django.db.models import F
from myapp.models import Blog
from datetime import timedelta

# Fetch blogs whose time is greater than the time of last edited time plus 5 minutes
blogs = Blog.objects.filter(created_time__gt=F('last_edited_time') + timedelta(minutes=5))
```

### Comparing Fields

To display all blogs where the last edited time is the same as the created time:

```python
from django.db.models import F
from myapp.models import Blog

# Fetch blogs where last edited time is the same as created time
blogs = Blog.objects.filter(last_edited_time=F('created_time'))
```

### Performance Benefits

F expressions are particularly useful because they:
- **Perform operations at the database level** rather than in Python
- **Reduce memory usage** by avoiding data transfer between database and Python
- **Improve performance** for large datasets

### Note on Relationships

While using operations like:

```python
Book.objects.filter(author__name='John Doe')
```

Consider the relationship between Author and Book. If there's a one-to-many relationship between author and book, Django will use **JOIN** clause to check the condition.

## 26. Update Method in Django ORM

To update a record in Django ORM, you can use the `update()` method on a queryset. This method allows you to change one or more fields of the records that match the queryset criteria.

### Basic Update Example

```python
from myapp.models import MyModel

# Update all records where the condition is met
MyModel.objects.filter(condition=True).update(field_name='new_value')
```

### Important Note

> **Note**: The `update()` method has access to only **one table at a time**. It cannot be used to update fields across related models in a single query.

### Using F Expressions with Update

You can also use F expressions with the update method:

```python
from django.db.models import F

# Increment a field value
MyModel.objects.filter(active=True).update(count=F('count') + 1)
```

## 27. Projection with values()

Projection allows you to select only specific fields from the database instead of fetching all fields.

### Get All Fields

```python
# Project all columns
Blog.objects.filter(name__startswith="Beatles").values()
# Output: <QuerySet [{'id': 1, 'name': 'Beatles Blog', 'tagline': 'All the latest Beatles news.'}]>

# Project all fields for all objects
Blog.objects.values()
# Output: <QuerySet [{'id': 1, 'name': 'Beatles Blog', 'tagline': 'All the latest Beatles news.'}]>
```

### Get Specific Fields

```python
# Project only id and name
Blog.objects.values("id", "name")
# Output: <QuerySet [{'id': 1, 'name': 'Beatles Blog'}]>
```

## 28. Distinct

Returns a new QuerySet that uses `SELECT DISTINCT` in its SQL query. This eliminates duplicate rows from the query results.

```python
# Get distinct author IDs
Book.objects.values('author_id').distinct()
```

## 29. Set Operations

### Union

The UNION operator selects only distinct values by default. To allow duplicate values, use the `all=True` argument.

```python
qs1 = Author.objects.values("name")
qs2 = Entry.objects.values("headline")
combined = qs1.union(qs2)

# With duplicates allowed
combined_with_duplicates = qs1.union(qs2, all=True)
```

### Intersection

```python
# Get common elements between querysets
result = qs1.intersection(qs2, qs3)
```

### Difference 

```python
# Get elements in qs1 but not in qs2 or qs3
result = qs1.difference(qs2, qs3)
```























