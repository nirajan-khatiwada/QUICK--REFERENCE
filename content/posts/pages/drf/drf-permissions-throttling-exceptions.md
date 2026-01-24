---
title: "DRF Security: Permissions, Throttling & Exceptions"
slug: "drf-permissions-throttling-exceptions"
date: 2025-01-15
description: "Secure and optimize your DRF API. Implement custom permissions, rate limiting (throttling), and handle exceptions gracefully."
showToc: true
weight: 4
series: ["Django REST Framework"]
categories: ["Django REST Framework"]
tags: ["Django", "DRF", "Security", "Permissions", "Throttling", "Error Handling"]
summary: "Guide to managing API access control with permissions and throttling, plus effective exception handling strategies."
images: ["/images/drf.png"]
---

# Permissions
Together with authentication and throttling, permissions determine whether a request should be granted or denied access.

Permission checks are always run at the very start of the view, before any other code is allowed to proceed. Permission checks will typically use the authentication information in the request.user and request.auth properties to determine if the incoming request should be permitted.

Permissions are used to grant or deny access for different classes of users to different parts of the API.

The simplest style of permission would be to allow access to any authenticated user, and deny access to any unauthenticated user. This corresponds to the IsAuthenticated class in REST framework.

A slightly less strict style of permission would be to allow full access to authenticated users, but allow read-only access to unauthenticated users. This corresponds to the IsAuthenticatedOrReadOnly class in REST framework.

# How permissions are determined
Permissions in REST framework are always defined as a list of permission classes.Before running the main body of the view each permission in the list is checked. If any permission check fails, an exceptions.PermissionDenied or exceptions.NotAuthenticated exception will be raised, and the main body of the view will not run.When the permission checks fail, either a "403 Forbidden" or a "401 Unauthorized" response will be returned.


# Setting the permission policy
The default permission policy may be set globally, using the DEFAULT_PERMISSION_CLASSES setting. For example.

```python
REST_FRAMEWORK = {
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAuthenticated',
    ]
}
```

If not specified, this setting defaults to allowing unrestricted access:

```python
'DEFAULT_PERMISSION_CLASSES': [
   'rest_framework.permissions.AllowAny',
]
```

You can also set the authentication policy on a per-view, or per-viewset basis, using the APIView class-based views.

```python
from rest_framework.permissions import IsAuthenticated
from rest_framework.response import Response
from rest_framework.views import APIView

class ExampleView(APIView):
    permission_classes = [IsAuthenticated]

    def get(self, request, format=None):
        content = {
            'status': 'request was permitted'
        }
        return Response(content)
```

when you set new permission classes via the class attribute or decorators you're telling the view to ignore the default list set in the settings.py file.


# List of permissions

- AllowAny
Allow any access whether authenticated or not.Useful for views that need to be publicly accessible (e.g., login or registration).This permission is not strictly required, since you can achieve the same result by using an empty list or tuple for the permissions setting.

- IsAuthenticated
The IsAuthenticated permission class will deny permission to any unauthenticated user, and allow permission otherwise.This permission is suitable if you want your API to only be accessible to registered users.

- IsAdminUser
The IsAdminUser permission class will deny permission to any user, unless user.is_staff is True in which case permission will be allowed.This permission is suitable if you want your API to only be accessible to a subset of trusted administrators

- IsAuthenticatedOrReadOnly
The IsAuthenticatedOrReadOnly will allow authenticated users to perform any request. Requests for unauthenticated users will only be permitted if the request method is one of the "safe" methods; GET, HEAD or OPTIONS.This permission is suitable if you want to your API to allow read permissions to anonymous users, and only allow write permissions to authenticated users.

Example Of Using Permissions

```python
from rest_framework import permissions
from rest_framework.response import Response
from rest_framework.views import APIView

class ExampleView(APIView):
    permission_classes = [permissions.IsAuthenticated]

    def get(self, request, format=None):
        content = {
            'status': 'request was permitted'
        }
        return Response(content)
```

# Custom Permissions
To implement a custom permission, override BasePermission and implement either, or both, of the following methods:
- has_permission(self, request, view)
Should return True if the request should be granted access, and False otherwise.
- has_object_permission(self, request, view, obj)
Should return True if the request should be granted access to the object obj, and False otherwise.

>  Note: The instance-level has_object_permission method will only be called if the view-level has_permission checks have already passed. Also note that in order for the instance-level checks to run, the view code should explicitly call .check_object_permissions(request, obj)


## Example Of HasPermission

for example, if you want to allow access to only admin users, you can create a custom permission class like this:
```python
from rest_framework.permissions import BasePermission
from rest_framework.response import Response
from rest_framework.views import APIView

class IsAdminUser(BasePermission):
    def has_permission(self, request, view):
        return request.user and request.user.is_staff

class ExampleView(APIView):
    permission_classes = [IsAdminUser]

    def get(self, request, format=None):
        content = {
            'status': 'request was permitted'
        }
        return Response(content)
```

Ip blacklisting is another example of custom permission
```python
from rest_framework.permissions import BasePermission
from rest_framework.response import Response
from rest_framework.views import APIView

class BlacklistPermission(BasePermission):
    def has_permission(self, request, view):
        ip = request.META['REMOTE_ADDR']
        blacklisted = Blacklist.objects.filter(ip=ip).exists()
        return not blacklisted

class ExampleView(APIView):
    permission_classes = [BlacklistPermission]

    def get(self, request, format=None):
        content = {
            'status': 'request was permitted'
        }
        return Response(content)
```



## Example of has_object_permission
The following example demonstrates how to implement a custom permission that only allows owners of an object to edit it. This permission checks if the user is the owner of the object.
```python
from rest_framework.permissions import BasePermission,IsAuthenticated
from rest_framework.response import Response
from rest_framework.views import APIView
from .models import ExampleModel
from .serializers import ExampleSerializer

class IsOwner(BasePermission):
    def has_object_permission(self, request, view, obj):
        return obj.owner == request.user

class ExampleView(APIView):
    permission_classes = [IsAuthenticated,IsOwner]

    def get(self, request, format=None):
        content = {
            'status': 'request was permitted'
        }
        return Response(content)
    def put(self, request, pk):
        example = ExampleModel.objects.get(pk=pk)
        self.check_object_permissions(request, example)
        serializer = ExampleSerializer(example, data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=400)     
```
Instead of using this custom permission we can use for this

```python
class ExampleView(APIView):
    def get(self, request, format=None):
        content = {
            'status': 'request was permitted'
        }
        return Response(content)
    def put(self, request, pk):
        example = ExampleModel.objects.get(pk=pk)
        if example.owner != request.user:
            return Response({'error': 'You do not have permission to edit this object'})
        serializer = ExampleSerializer(example, data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=400)   
```


## combining has_permission and has_object_permission
```python
from rest_framework.permissions import BasePermission,IsAuthenticated
from rest_framework.response import Response
from rest_framework.views import APIView
from .models import ExampleModel
from .serializers import ExampleSerializer

class IsOwner(BasePermission):
    def has_permission(self, request, view):
        return request.user and request.user.is_authenticated

    def has_object_permission(self, request, view, obj):
        return obj.owner == request.user

class ExampleView(APIView):
    permission_classes = [IsOwner]

    def get(self, request, format=None):
        content = {
            'status': 'request was permitted'
        }
        return Response(content)
    def put(self, request, pk):
        example = ExampleModel.objects.get(pk=pk)
        self.check_object_permissions(request, example)
        serializer = ExampleSerializer(example, data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=400)     
```

first, the has_permission method is called to check if the user is authenticated. If the user is authenticated, It will run the view and then the has_object_permission method is invoked to check if the user is the owner of the object if the user is the owner of the object then it will allow the user to edit the object otherwise it will return an error message.


# Throttling
Throttling is used to limit the number of requests that a user can make to an API within a certain timeframe. It is used to prevent abuse of the API.The order of execution is
- Authentication
- Permission
- Throttling
- View

## How throttling works
As with permissions and authentication, throttling in REST framework is always defined as a list of classes.Before running the main body of the view each throttle in the list is checked. If any throttle check fails an exceptions.Throttled exception will be raised, and the main body of the view will not run.


## Setting the throttling policy
The default throttling policy may be set globally, using the DEFAULT_THROTTLE_CLASSES setting. For example.
```python
REST_FRAMEWORK = {
    'DEFAULT_THROTTLE_CLASSES': [
        'rest_framework.throttling.AnonRateThrottle',
        'rest_framework.throttling.UserRateThrottle'
    ],
    'DEFAULT_THROTTLE_RATES': {
        'anon': '100/day',
        'user': '1000/day'
    }
}
```
The rate descriptions used in DEFAULT_THROTTLE_RATES may include `second`, `minute`, `hour` or `day` as the throttle period.

You can also set the throttling policy on a per-view or per-viewset basis, using the `APIView` class-based views.
```python
from rest_framework.response import Response
from rest_framework.throttling import UserRateThrottle
from rest_framework.views import APIView

class ExampleView(APIView):
    throttle_classes = [UserRateThrottle]

    def get(self, request, format=None):
        content = {
            'status': 'request was permitted'
        }
        return Response(content)
```

## How Clients are identified
The X-Forwarded-For HTTP header and REMOTE_ADDR WSGI variable are used to uniquely identify client IP addresses for throttling. If the X-Forwarded-For header is present then it will be used, otherwise the value of the REMOTE_ADDR variable from the WSGI environment will be used.


## API Reference

### AnonRateThrottle
The AnonRateThrottle will only ever throttle unauthenticated users. The IP address of the incoming request is used to generate a unique key to throttle against.The allowed number of requests is determined by the 'anon' key in the DEFAULT_THROTTLE_RATES setting.AnonRateThrottle is suitable if you want to restrict the rate of requests from unknown sources.
### UserRateThrottle
The UserRateThrottle will throttle users to a given rate of requests across the API. The user id is used to generate a unique key to throttle against. Unauthenticated requests will fall back to using the IP address of the incoming request to generate a unique key to throttle against.The allowed number of requests is determined by the 'user' key in the DEFAULT_THROTTLE_RATES setting.UserRateThrottle.


### ScopedRateThrottle
The ScopedRateThrottle class can be used to restrict access to specific parts of the API. This throttle will only be applied if the view that is being accessed includes a .throttle_scope property. The unique throttle key will then be formed by concatenating the "scope" of the request with the unique user id or IP address.

The allowed request rate is determined by the DEFAULT_THROTTLE_RATES setting using a key from the request "scope".

For example, given the following views...
```python
class ContactListView(APIView):
    throttle_classes = [ScopedRateThrottle]
    throttle_scope = 'contacts'
    ...

class ContactDetailView(APIView):
    throttle_classes = [ScopedRateThrottle]
    throttle_scope = 'contacts'
    ...

class UploadView(APIView):
    throttle_classes = [ScopedRateThrottle]
    throttle_scope = 'uploads'
    ...
```
...and the following settings.
```python
REST_FRAMEWORK = {
    'DEFAULT_THROTTLE_CLASSES': [
        'rest_framework.throttling.ScopedRateThrottle',
    ],
    'DEFAULT_THROTTLE_RATES': {
        'contacts': '1000/day',
        'uploads': '20/day'
    }
}
```
User requests to either ContactListView or ContactDetailView would be restricted to a total of 1000 requests per-day. User requests to UploadView would be restricted to 20 requests per day.




# Status 
To send status code in response rest_framework provides a class called status which is used to send status code in response.

```python
from rest_framework import status
from rest_framework.response import Response
from rest_framework.views import APIView
class ExampleView(APIView):
    def get(self, request, format=None):
        content = {
            'status': 'request was permitted'
        }
        return Response(content, status=status.HTTP_200_OK)
```

For other status code you can visit [here](https://www.django-rest-framework.org/api-guide/status-codes/)

# Returning Url
To return url in response rest_framework provides `reverse` which is used to return url in response.

```python
from rest_framework.reverse import reverse
from rest_framework.response import Response
from rest_framework.views import APIView
class ExampleView(APIView):
    def get(self, request, format=None):
        content = {
            'status': 'request was permitted',
            'url': reverse('example-view', request=request,args=[1,2,3,4])
        }
        return Response(content)
```



# Exceptions in DRF
Django REST framework provides a set of built-in exceptions that can be used to handle errors in your API views. These exceptions are subclasses of the `APIException` class and can be raised to indicate different types of errors.
Some commonly used exceptions in DRF include:
- `NotFound`: Raised when a requested resource is not found (HTTP 404).
- `PermissionDenied`: Raised when a user does not have permission to access a resource (HTTP 403).
- `ValidationError`: Raised when data validation fails (HTTP 400).
- `AuthenticationFailed`: Raised when authentication fails (HTTP 401).
- `Throttled`: Raised when a request is throttled due to exceeding rate limits (HTTP 429).
- `MethodNotAllowed`: Raised when an HTTP method is not allowed for a particular endpoint (HTTP 405).
- `AuthenticationFailed`: Raised when authentication credentials are invalid or missing (HTTP 401).
- `NotAuthenticated`: Raised when authentication is required but not provided (HTTP 401).
- `ParseError`: Raised when there is an error parsing the request data (HTTP 400).
- `UnsupportedMediaType`: Raised when the request's media type is not supported (HTTP 415).
- `APIException`: The base class for all DRF exceptions. You can also create custom exceptions by subclassing this class.

These exceptions can be raised in your views or serializers to indicate specific error conditions. When an exception is raised, DRF will automatically generate an appropriate HTTP response with the corresponding status code and error message.

Example of using exceptions in DRF:
```python
from rest_framework.exceptions import NotFound, PermissionDenied
from rest_framework.response import Response
from rest_framework.views import APIView
class ExampleView(APIView):
    def get(self, request, pk, format=None):
        try:
            example = ExampleModel.objects.get(pk=pk)
        except ExampleModel.DoesNotExist:
            raise NotFound("Example not found")

        if not request.user.has_permission('view_example', example):
            raise PermissionDenied("You do not have permission to view this example")

        content = {
            'status': 'request was permitted'
        }
        return Response(content)
```


## Creating Custom Exceptions
You can create custom exceptions in DRF by subclassing the `APIException` class. Here's an example of how to create a custom exception:
```python
from rest_framework.exceptions import APIException
class CustomException(APIException):
    status_code = 418  # HTTP status code for "I'm a teapot"
    default_detail = 'This is a custom exception message.'
    default_code = 'custom_exception'
```
You can then raise this custom exception in your views or serializers just like any other DRF exception