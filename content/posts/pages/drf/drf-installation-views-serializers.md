---
title: "DRF Fundamentals: Installation, Views & Serializers"
slug: "drf-installation-views-serializers"
date: 2025-01-12
description: "Master Django REST Framework basics. Learn installation, APIView, Request/Response objects, and building your first Serializers."
showToc: true
weight: 1
series: ["Django REST Framework"]
categories: ["Django REST Framework"]
tags: ["Django", "Python", "DRF", "Backend", "API", "Serialization"]
summary: "A comprehensive guide to getting started with Django REST Framework, creating views, and handling data with serializers."
images: ["/images/drf.png"]
---

# 1. Installation

### Step 1: Install Django Rest Framework
```bash
pip install djangorestframework
```

### Step 2: Add 'rest_framework' to your INSTALLED_APPS setting.
```python
INSTALLED_APPS = [
    ...
    'rest_framework',
]
```


# 2.Views
We can Define class based views in django rest framework by inheriting from APIView class.

### step 1: Import APIView
```python
from rest_framework.views import APIView
from rest_framework.response import Response
```
### step 2: Define a class based view
```python
class HelloApiView(APIView):
    def get(self, request):
        #code
```
here we can define post, put, patch, delete methods also.

> Note: Only defined methods will be allowed to access the view.Like here we are only allowing get method to access the view.

# 3. URL Configuration
```python
from django.urls import path
from . import views
urlpatterns = [
    path('hello-view/', views.HelloApiView.as_view()),
]
```
> Note: APIView Base class always return a `Response` object.

# 4. Response
To return a response from a view we can use Response object from `rest_framework.response module`

syntax:
```python
from rest_framework.response import Response
Response(data, status=None, headers=None)
```

### Example
```python
from rest_framework.response import Response
from rest_framework.views import APIView
class HelloApiView(APIView):
    def get(self, request):
        return Response({'message': 'Hello From Rest Framework!'})
```




# 5.request
If you're doing REST-based web service stuff.you should ignore request.POST.

## request.data
`request.data` returns the parsed content of the request body. This is similar to the standard `request.POST` and `request.FILES` attributes except that:
- It includes all parsed content, including file and non-file inputs.
- It supports parsing the content of HTTP methods other than POST, meaning that you can access the content of PUT and PATCH requests.
- It supports REST framework's flexible request parsing, rather than just supporting form data. For example, you can handle incoming JSON data in the same way that you handle incoming form data.

## request.query_params
request.query_params is a more correctly named synonym for request.GET.For clarity inside your code, we recommend using request.query_params instead of the Django's standard request.GET. Doing so will help keep your codebase more correct and obvious - any HTTP method type may include query parameters, not just GET requests.


## Example
```python
class DemoApiView(APIView):
    def post(self, request):
        data = request.data
        return Response({'message': 'Hello From Rest Framework!', 'data': data})
    
    def get(self, request):
        query_params = request.query_params
        return Response({'message': 'Hello From Rest Framework!', 'query_params': query_params})
```



# Different HTTP Methods
- GET: Retrieve data from the server.
- POST: Send data to the server.
- DELETE: Delete data from the server.
- PUT: Update data on the server.
- PATCH: Update data partially on the server.



# Some important requests methods
- request.method: Returns the HTTP method used for the request (e.g., GET, POST
, PUT, DELETE).
- request.user: Returns the user associated with the request, if authentication is used.
- request.user.is_authenticated: Returns True if the user is authenticated, otherwise False.
- request.auth: Returns the authentication information for the request, if applicable.
> Note: All th concept of django are applied in DRF also.



# 6. Serializer
Serializer is used to convert complex data types like querysets and model instances to native Python datatypes that can then be easily rendered into JSON, XML or other content types. Serializers also provide deserialization, allowing parsed data to be converted back into complex types, after first validating the incoming data.
Serializer is used in DRF for:
- Parsing the incoming request data and validating it.
- Converting complex data types to native python datatypes.


# 7. Creating Serializer
Creating a serializer in DRF is similar to creating a form in Django.For now,We will create a simple serializer name comment.

### Create a serializer
```python
from rest_framework import serializers
class CommentSerializer(serializers.Serializer):
    name = serializers.CharField(max_length=100)
    email = serializers.EmailField()
    content = serializers.CharField()
```
> Note: The field name of serializer should be same as the field name of model.
> Note: Note all the field that are written in serializer should be present in the model but all the fields present in the model are not necessary to be present in the serializer but always remember the parameter like `required` and `read_only` can affects.

# 8. Using Serializer

### Using Serializer to convert complex data types to native python datatypes.
```python
comment = Comment.objects.get(id=1)
serializer = CommentSerializer(comment)
serializer.data # {'name': 'John', 'email': 'john@gmail.com', 'content': 'Hello World!'}
```

Example:
```python
from .serializers import CommentSerializer
from .models import Comment
from rest_framework.response import Response
from rest_framework.views import APIView
class CommentApiView(APIView):
    def get(self, request):
        comments = Comment.objects.get(pk=1)
        serializer = CommentSerializer(comments)
        return Response(serializer.data)
```

# 9. Using Serializer to validate incoming data
### Example
```python
commentSer=CommentSerializer(data=request.data)
if commentSer.is_valid(): # Returns True if the data is valid
    print(commentSer.validated_data) # Returns the validated data
    #code
else:
    return Response(commentSer.errors)
```
TO be able to be validated the request.data should follow the same structure as the serializer and should have the same field name as the serializer.


# 10.Saving the data
To save the data in the database we can define it in APIView itself.

### Example
```python
from .serializers import CommentSerializer
from .models import Comment
from rest_framework.response import Response
from rest_framework.views import APIView

class CommentApiView(APIView):
    def post(self, request):
        commentSer=CommentSerializer(data=request.data)
        if commentSer.is_valid():
            data=Comment.objects.create(**commentSer.validated_data)
            return Response(CommentSerializer(data).data)
        else:
            return Response(commentSer.errors)
```

But it is not a good practice to define the save method in the APIView itself. We can define the save method in the serializer itself with the help of `create` or `update` method.

### Example
```python
from rest_framework import serializers
from .models import Comment
class CommentSerializer(serializers.Serializer):
    name = serializers.CharField(max_length=100)
    email = serializers.EmailField()
    content = serializers.CharField()
    
    def create(self, validated_data):
        return Comment.objects.create(**validated_data)
    def update(self,instance,validated_data):
        instance.name=validated_data.get('name',instance.name)
        instance.email=validated_data.get('email',instance.email)
        instance.content=validated_data.get('content',instance.content)
        instance.save()
        return instance
```

using the above serializer we can remove the save method from the APIView.

### Example
```python
from .serializers import CommentSerializer
from .models import Comment
from rest_framework.response import Response
from rest_framework.views import APIView

class CommentApiView(APIView):
    def post(self, request):
        commentSer=CommentSerializer(data=request.data)
        if commentSer.is_valid():
            data=commentSer.save()
            return Response(CommentSerializer(data).data)
        else:
            return Response(commentSer.errors)
```
 
```python 
# .save() will create a new instance.
serializer = CommentSerializer(data=data)

# .save() will update the existing `comment` instance.
serializer = CommentSerializer(comment, data=data)
```


## `save()` method
Internally, the `save()` method will call either `.create()` or `.update()` depending on whether an instance was passed when instantiating the serializer.

But if you doesnt create the `create` or `update` data in database then you can override the `save` method in the serializer.

For example: You take the form data and send the mail to the user and you dont want to save the data in the database then you can override the `save` method in the serializer.

### Example
```python
from rest_framework import serializers
from .models import Comment

class CommentSerializer(serializers.Serializer):
    name = serializers.CharField(max_length=100)
    email = serializers.EmailField()
    content = serializers.CharField()
    def save(self):
        name=self.validated_data.get('name')
        email=self.validated_data.get('email')
        content=self.validated_data.get('content')
        #code to send mail
```


# 11. Validation
When deserializing data, you always need to call is_valid() before attempting to access the validated data, or save an object instance. If any validation errors occur, the .errors property will contain a dictionary representing the resulting error messages. For example:
```python 
serializer = CommentSerializer(data={'email': 'foobar', 'content': 'baz'})
serializer.is_valid()
# False
serializer.errors
# {'email': ['Enter a valid e-mail address.'], 'created': ['This field is required.']}
```

## Defining field-level validation
You can specify custom validation for a field by adding .validate_<field_name> methods to your Serializer subclass. These are similar to the .clean_<field_name> methods on Django forms. For example:
```python
from rest_framework import serializers
class CommentSerializer(serializers.Serializer):
    name = serializers.CharField(max_length=100)
    email = serializers.EmailField()
    content = serializers.CharField()
    def validate_name(self, value):
        if value == 'admin':
            raise serializers.ValidationError("Name can't be admin")
        return value
```

## Defining object-level validation
You can also add .validate() method to a Serializer subclass. This method is used to validate the entire object, rather than a specific field. For example:
```python
from rest_framework import serializers
class CommentSerializer(serializers.Serializer):
    name = serializers.CharField(max_length=100)
    email = serializers.EmailField()
    content = serializers.CharField()
    def validate(self, data):
        if data['name'] == 'admin':
            raise serializers.ValidationError("Name can't be admin")
        return data
```

## validators
You can also add validators to the fields in the serializer.

### Example
```python
from rest_framework import serializers

def name_validator(value):
    if value == 'admin':
        raise serializers.ValidationError("Name can't be admin")
    else:
        return value

class CommentSerializer(serializers.Serializer):
    name = serializers.CharField(max_length=100, validators=[name_validator])
    email = serializers.EmailField()
    content = serializers.CharField()
```
### Some Useful Built-in Validators
- `UniqueValidator`: This validator checks that a field is unique across the entire queryset.
- `UniqueTogetherValidator`: This validator checks that a set of fields is unique together across the
entire queryset.

Example:
```python
from rest_framework import serializers
from rest_framework.validators import UniqueValidator
from .models import Comment
class CommentSerializer(serializers.ModelSerializer):
    email = serializers.EmailField(
        validators=[UniqueValidator(queryset=Comment.objects.all())]
    )
    class Meta:
        model = Comment
        fields = ['name', 'email', 'content']
```

```python
from rest_framework import serializers
from rest_framework.validators import UniqueTogetherValidator
from .models import Comment
class CommentSerializer(serializers.ModelSerializer):
    class Meta:
        model = Comment
        fields = ['name', 'email', 'content']
        validators = [
            UniqueTogetherValidator(
                queryset=Comment.objects.all(),
                fields=['name', 'email']
            )
        ]
```


# 12. Partial update
To validate data using a serializer, we typically need to provide all the required fields. However, if we want to update only specific fields, we can use the partial argument in the serializer. This allows us to validate only the fields that are included in the request.

### Example 
```python
from .serializers import CommentSerializer
from .models import Comment
from rest_framework.response import Response

class CommentApiView(APIView):
    def patch(self, request):
        comment = Comment.objects.get(pk=1)
        commentSer = CommentSerializer(comment, data=request.data, partial=True)
        if commentSer.is_valid():
            commentSer.save()
            return Response(commentSer.data)
        else:
            return Response(commentSer.errors)
```


# 13.Nested Serializer
We can make nested serializer in DRF by defining the serializer inside the serializer.

### Example
```python
from rest_framework import serializers

class UserSerializer(serializers.Serializer):
    name = serializers.CharField(max_length=100)
    email = serializers.EmailField()

class CommentSerializer(serializers.Serializer):
    user = UserSerializer()
    content = serializers.CharField(max_length=100)
```

## Sending data to nested serializer
```python
data = {
    'user': {
        'name': 'John',
        'email': '
    },
    'content': 'Hello World!'
}
```

## Saving data to nested serializer
```python
def create(self, validated_data):
    user_data = validated_data.pop('user')
    user = User.objects.create(**user_data)
    comment = Comment.objects.create(user=user, **validated_data)
    return comment
def update(self, instance, validated_data):
    user_data = validated_data.pop('user')
    user = instance.user
    user.name = user_data.get('name', user.name)
    user.email = user_data.get('email', user.email)
    user.save()
    instance.content = validated_data.get('content', instance.content)
    instance.save()
    return instance
```

# 14. Dealing with multiple objects
To serialize a queryset or list of objects instead of a single object instance, you should pass the many=True flag when instantiating the serializer. You can then pass a queryset or list of objects to be serialized.

### Example
```python
comments = Comment.objects.all()
serializer = CommentSerializer(comments, many=True)

# [
#     {'id': 0, 'title': 'The electric kool-aid acid test', 'author': 'Tom Wolfe'},
#     {'id': 1, 'title': 'If this is a man', 'author': 'Primo Levi'},
#     {'id': 2, 'title': 'The wind-up bird chronicle', 'author': 'Haruki Murakami'}
# ]
```

# Including extra context
You can include additional context when instantiating the serializer, by passing additional keyword arguments. This context is not used directly by the serializer, but is passed through to any custom field or method that accepts additional arguments.

### Example
```python
serializer = CommentSerializer(comments, many=True, context={'request': request})
```
using it in the serializer
```python
class CommentSerializer(serializers.Serializer):
    def hello(self):
        request = self.context.get('request')  
```

# 15. ModelSerializer
The ModelSerializer class is the same as a regular Serializer class, except that:
- It will automatically generate a set of fields for you, based on the model.
- It will automatically generate validators for the serializer, such as unique_together validators.
- It includes simple default implementations of `.create()` and `.update()`.

### Declaring a simple ModelSerializer
```python
from rest_framework import serializers
from .models import Comment
class CommentSerializer(serializers.ModelSerializer):
    class Meta:
        model = Comment
        fields = '__all__' 
```

### Specifying which fields to include
```python
class CommentSerializer(serializers.ModelSerializer):
    class Meta:
        model = Comment
        fields = ['name', 'email', 'content','user']

'''
{
    'name': 'John',
    'email': 'john@gmail.com',
    'content': 'Hello World!',
    'user': 1
}
'''
```
> Note: Any relationships such as foreign keys on the model will be mapped to `PrimaryKeyRelatedField` by default.

### Specifying which fields to exclude
```python
class CommentSerializer(serializers.ModelSerializer):
    class Meta:
        model = Comment
        exclude = ['created']
```

> Note: The fields attribute is used to specify which fields should be included in the serialized output. Also, In post request validation,The fields attribute is used to specify which fields should be required.

> Note: To make `required=False` in the serializer we can use `blank=True` in the model.

For example: I we want to show the created field in the response but we dont want to make it required in the post request then we can use `blank=True` in the model.


# 16. Specifying the nested serializer
The default ModelSerializer uses primary keys for relationships, but you can also easily generate nested representations using the depth option:

### Example
```python
class CommentSerializer(serializers.ModelSerializer):
    class Meta:
        model = Comment
        fields = ['name', 'email', 'content', 'user']
        depth = 1


'''
Before:
{
    'name': 'John',
    'email': 'johj@gmail.com",
    'user': 1
    'content': 'Hello World!',
}

After:
{
    'name': 'John',
    'email': 'johj@gmail.com",
    'user': {
        'name': 'John',
        'email': 'johj@gmail.com",
    },
    'content': 'Hello World!',
}
```
> Note: Depth are specially used in get request to show the nested serializer.For post request we have to use the nested serializer.
>Note: But by default model serializer can handel primary key related field but if we want to handle nested serializer then we can override the create method in the serializer.

# 17. Overriding the create method
By default model serializer can handel primary key related field but if we want to handle nested serializer then we can override the create method in the serializer.

### Example
```python
class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ['name', 'email']
class CommentSerializer(serializers.ModelSerializer):
    user = UserSerializer()
    class Meta:
        model = Comment
        fields = ['name', 'email', 'content', 'user']
        depth = 1
    def create(self, validated_data):
        user_data = validated_data.pop('user')
        user = User.objects.create(**user_data)
        comment = Comment.objects.create(user=user, **validated_data)
        return comment
    def update(self, instance, validated_data):
        user.name = user_data.get('name', user.name)
        user.email = user_data.get('email', user.email)
        user.save()
        instance.content = validated_data.get('content', instance.content)
        instance.save()
        return instance
```


# 18. Defining Field Explicitly
By default, all the fields of the model are included in the serializer but if we want to define the field explicitly then we can define it in the serializer.

### Example
```python
class CommentSerializer(serializers.ModelSerializer):
    name = serializers.CharField(max_length=100)
    email = serializers.EmailField()
    content = serializers.CharField()
    class Meta:
        model = Comment
        fields = ['name', 'email', 'content']
```



# 19. Specifying read_only fields
By default, all the fields of the model are included in the serializer but if we want to make some fields read-only then we can define it in the serializer.

### Example
```python
class CommentSerializer(serializers.ModelSerializer):
    class Meta:
        model = Comment
        fields = ['name', 'email', 'content']
        read_only_fields = ['name']
```
> Note: If we have editable=False,By default, the field is read-only. But if we want to make the field editable then we can use read_only_fields.

> Note: The field that are read-only are not required in the post request but it is displayed in the response.


# 20.Additional Keyword arguments
We can add additional arguments in the serializer as:
```python
class Serializer(serializers.ModelSerializer):
    name = serializers.CharField(max_length=100)
    email = serializers.EmailField(required=False)
    class Meta:
        model = Comment
        fields = ['name', 'email', 'content']
```
But here we need to define the field name in the serializer that are already present in the model serializer but if we want to add additional arguments in the serializer without redefining then we can use `extra_kwargs` in the Meta class.

### Example
```python
class CommentSerializer(serializers.ModelSerializer):
    class Meta:
        model = Comment
        fields = ['name', 'email', 'content']
        extra_kwargs = {
            'email': {'required': False}
        }
```





# 21. Serializer Fields
Each serializer field class constructor takes at least these arguments. Some Field classes take additional, field-specific arguments, but the following should always be accepted:

- `read_only`: Boolean. Read-only fields are included in the API output, but should not be included in the input during create or update operations. Any 'read_only' fields that are incorrectly included in the serializer input will be ignored.

Set this to True to ensure that the field is used when we serialize a representation, but is not used when we create or update an instance during deserialization.

Defaults to `False`


- `write_only`: Boolean. Write-only fields are not included in the API output, but are accepted on input. When deserializing data, write-only fields are ignored. 

Set this to True to ensure that the field may be used when updating or creating an instance, but is not included when serializing the representation.

Defaults to `False`

- `required`:Boolean. Normally an error will be raised if a field is not supplied during deserialization. Set to false if this field is not required to be present during deserialization.

Defaults to True. If you're using Model Serializer default value will be False if you have specified blank=True or default or null=True at your field in your Model.

- `default`: The default value to use when a field is not provided in the input data.
The default is not applied during partial update operations. In the partial update case only fields that are provided in the incoming data will have a validated value returned.

When serializing the instance, default will be used if the object attribute or dictionary key is not present in the instance.

Note that setting a default value implies that the field is not required. Including both the default and required keyword arguments is invalid and will raise an error.

- `allow_null`: Boolean. Normally an error will be raised if None is passed to a field. Set to `True` if None should be considered a valid value.


- `validators`: A list of validator functions which should be applied to the incoming field input when validating the field.

- `source`: A string representing the attribute or dictionary key that should be used to populate the field. This is useful if you want to use a different name for the field in the serializer than the name of the attribute on the model.

Consider the following model:
```python
class Author(models.Model):
    full_name = models.CharField(max_length=100)

class Comment(models.Model):
    author = models.ForeignKey(Author, on_delete=models.CASCADE)
    content = models.TextField()
```

To represent the author's full name in the CommentSerializer, we can use the source argument:
```python
class CommentSerializer(serializers.ModelSerializer):
    author_name = serializers.CharField(source='author.full_name',
    read_only=True
    )
    class Meta:
        model = Comment
        fields = ['author_name', 'content']
```

In this example, the author_name field in the serializer will be populated with the full_name attribute of the related Author model.

```python
from .models import Comment
from .serializers import CommentSerializer
from rest_framework.response import Response
from rest_framework.views import APIView
class CommentApiView(APIView):
    def get(self, request):
        comments = Comment.objects.select_related('author').all()
        serializer = CommentSerializer(comments, many=True)
        return Response(serializer.data)
```

> Note: Use Source for get method to show the related field in the response not for post method.
> Always know consequences of n+1 query while using source for related fields.



> Note: serialization is the process of converting complex data types to native Python datatypes that can then be easily rendered into JSON, XML, or other content types.
> Deserialization is the reverse process of converting native Python datatypes to complex data types.


# 22. Adding custom fields in ModelSerializer
If we want to add custom fields in the serializer then we can add it in the serializer.

### Example
```python
from rest_framework import serializers
class CommentSerializer(serializers.ModelSerializer):
    custom_field = serializers.CharField(write_only=True)
    class Meta:
        model = Comment
        fields = ['name', 'email', 'content']
```
If we want to add the custom field in the request because its is not present in the model then we can use `write_only` in the custom field.But if we add this we should also add the `create` and `update` method explicitly in the serializer.

# 22. Adding custom fields during Response
If we want to add custom fields in the response then we can add it in the serializer.

### Example
```python
from rest_framework import serializers
class CommentSerializer(serializers.ModelSerializer):
    custom_field = serializers.SerializerMethodField()
    class Meta:
        model = Comment
        fields = ['name', 'email', 'content']
    def get_custom_field(self, obj):
        return 'Hello World!'
```
> Note: SerializerMethodField is used to add custom fields in the response.


# 23. Handling Complex and Internal Logic
During the user registration process, we need to hash the password before saving it to the database. But model serializer only saves the data to the database but if we want to hash the password before saving it to the database then we should override the `create` method in the serializer.
### Example
```python
from rest_framework import serializers
from .models import User
class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ['name', 'email', 'password']
        extra_kwargs = {'password': {'write_only': True}}
    def create(self, validated_data):
        password=validated_data.pop('password')
        user = User.objects.create_user(**validated_data)
        user.set_password(password)
        user.save()
        return user
```






