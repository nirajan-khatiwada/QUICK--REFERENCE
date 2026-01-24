---
title: "DRF Serializer Fields: Validation & Types"
slug: "drf-serializer-fields-validation"
date: 2025-01-13
description: "Deep dive into DRF Serializer fields. Explore CharFields, BooleanFields, DateFields, and custom validation logic."
showToc: true
weight: 2
series: ["Django REST Framework"]
categories: ["Django REST Framework"]
tags: ["Django", "Python", "DRF", "Serializers", "Validation", "Data Types"]
summary: "Detailed reference for DRF serializer fields, including usage examples and input validation techniques."
images: ["/images/drf.png"]
---


# 22. Serializer Field
 ### Boolean field
 A boolean representation.
```python
    from rest_framework import serializers
    class BooleanSerializer(serializers.Serializer):
        boolean_field = serializers.BooleanField()
```

### Char Field
A text representation. Optionally validates the text to be shorter than max_length and longer than min_length
List of parameters it take:
- `max_length` - Validates that the input contains no more than this number of characters.
- `min_length` - Validates that the input contains no fewer than this number of characters.
- `allow_blank` - If set to True then the empty string should be considered a valid value. If set to False then the empty string is considered invalid and will raise a validation error. Defaults to False.
- `trim_whitespace` - If set to True then leading and trailing whitespace is trimmed. Defaults to True.

```python
    from rest_framework import serializers
    class CharSerializer(serializers.Serializer):
        char_field = serializers.CharField(max_length=100, min_length=10)
```

### Email Field
A text representation, validates the text to be a valid e-mail address.

```python
    from rest_framework import serializers
    class EmailSerializer(serializers.Serializer):
        email_field = serializers.EmailField(max_length=100, min_length=10)
```

### Slug Field
A text representation, validates the text to be a valid slug.

```python
    from rest_framework import serializers
    class SlugSerializer(serializers.Serializer):
        slug_field = serializers.SlugField(max_length=100, min_length=10)
```

### URL Field
A text representation, validates the text to be a valid URL.

```python
    from rest_framework import serializers
    class URLSerializer(serializers.Serializer):
        url_field = serializers.URLField(max_length=100, min_length=10)
```

### UUID Field
A text representation, validates the text to be a valid UUID.

```python
    from rest_framework import serializers
    class UUIDSerializer(serializers.Serializer):
        uuid_field = serializers.UUIDField(max_length=100, min_length=10)
```

### Ip Address Field
A text representation, validates the text to be a valid IP address.

```python
    from rest_framework import serializers
    class IpAddressSerializer(serializers.Serializer):
        ip_address_field = serializers.IPAddressField(max_length=100, min_length=10)
```

### Integer Field
An integer representation. Optionally validates the integer to be greater than max_value and lesser than min_value.

```python
    from rest_framework import serializers
    class IntegerSerializer(serializers.Serializer):
        integer_field = serializers.IntegerField(max_value=100, min_value=10)
```

### Float Field
A float representation. Optionally validates the float to be greater than max_value and lesser than min_value.

```python
    from rest_framework import serializers
    class FloatSerializer(serializers.Serializer):
        float_field = serializers.FloatField(max_value=100, min_value=10)
```

### Decimal Field
A decimal representation. Optionally validates the decimal to be greater than max_value and lesser than min_value.

```python
    from rest_framework import serializers
    class DecimalSerializer(serializers.Serializer):
        decimal_field = serializers.DecimalField(max_value=100, min_value=10)
```

### Date Field
A date representation.
```python
    from rest_framework import serializers
    class DateSerializer(serializers.Serializer):
        date_field = serializers.DateField()
```

### DateTime Field
A datetime representation.
```python
    from rest_framework import serializers
    class DateTimeSerializer(serializers.Serializer):
        datetime_field = serializers.DateTimeField()
```

### Time Field
A time representation.
```python
    from rest_framework import serializers
    class TimeSerializer(serializers.Serializer):
        time_field = serializers.TimeField()
```

### Choice Field
A choice representation. Optionally validates the choice to be in the choices list.

```python
    from rest_framework import serializers
    class ChoiceSerializer(serializers.Serializer):
        choice_field = serializers.ChoiceField(choices==(
            ('choice1', 'Choice 1'),
            ('choice2', 'Choice 2'),
            ('choice3', 'Choice 3'),
        ))
```

Example:
```json
{
    "choice_field": "choice1"
}

// This is correct, but
{
    "choice_field": "choice4"
}

// THis is incorrect because choice4 is not in the choices list.
```

### Multiple Choice Field
A multiple choice representation. Optionally validates the choice to be in the choices list.

```python
    from rest_framework import serializers
    class MultipleChoiceSerializer(serializers.Serializer):
        multiple_choice_field = serializers.MultipleChoiceField(choices==(
            ('choice1', 'Choice 1'),
            ('choice2', 'Choice 2'),
            ('choice3', 'Choice 3'),
        ))
```

Example:
```json
{
    "multiple_choice_field": ["choice1", "choice2"]
}

// This is correct, but
{
    "multiple_choice_field": ["choice1", "choice4"]
}
// THis is incorrect because choice4 is not in the choices list.
```


### File Field
A file representation. Optionally validates the file to be of a certain type.

```python
    from rest_framework import serializers
    class FileSerializer(serializers.Serializer):
        file_field = serializers.FileField()
```

### Image Field
An image representation. Optionally validates the image to be of a certain type.

```python
    from rest_framework import serializers
    class ImageSerializer(serializers.Serializer):
        image_field = serializers.ImageField()
```

### List Field
A list representation. Optionally validates the list to be of a certain type.

```python
    from rest_framework import serializers
    class ListSerializer(serializers.Serializer):
        list_field = serializers.ListField(child=serializers.CharField())
```
Example:
```json
{
    "list_field": ["item1", "item2"]
}

// This is correct, but
{
    "list_field": ["item1", 1]
}
// THis is incorrect because 1 is not a string.
```

### Serializer Method Field
A serializer method representation. Optionally validates the method to be of a certain type.It is read-only field

```python
    from rest_framework import serializers
    class SerializerMethodSerializer(serializers.Serializer):
        serializer_method_field = serializers.SerializerMethodField()
        def get_serializer_method_field(self, obj):
            return obj.method_field
```


