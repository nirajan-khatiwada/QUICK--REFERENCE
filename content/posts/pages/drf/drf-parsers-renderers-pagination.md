---
title: "DRF Advanced: Parsers, Renderers & Pagination"
slug: "drf-parsers-renderers-pagination"
date: 2025-03-28
description: "Customize how your API handles data formats. Master Parsers for input, Renderers for output, and Pagination for large datasets."
showToc: true
weight: 5
series: ["Django REST Framework"]
categories: ["Django REST Framework"]
tags: ["Django", "DRF", "Pagination", "JSON", "XML", "API Performance"]
summary: "Learn to handle different content types with Parsers and Renderers, and implement efficient pagination for your API endpoints."
images: ["/images/drf.png"]
---

## Parsers in Django
In Django REST Framework (DRF), parsers are responsible for parsing request data based on the Content-Type header of incoming requests. When a client (such as a web or mobile app) sends a request to a DRF API, it often includes data in different formats (JSON, form data, files, etc.). Parsers determine how that data is read and processed into Python data structures.

### 1. Built-in Parsers
By default , DRF will use the following parsers if you don't specify any:
- `JSONParser`: Parses JSON-encoded data (application/json)
- `FormParser`: Parses form-encoded data (application/x-www-form-urlencoded). This is typically used for HTML forms.
- `MultiPartParser`: Parses multipart form data (used for file uploads). 
- `FileUploadParser`: Parses file uploads in a more efficient way, especially for large files.
- `MultiPartParser`: Parses multipart form data (used for file uploads). 

### 2. How DRF determines which parser to use
DRF select the parser based on the `Content-Type` header of the incoming request. The order of preference is as follows:
- request.data trigger the DRF to:
    1. Check the `Content-Type` header of the request.
    2. Match it against the parsers defined in the `DEFAULT_PARSER_CLASSES` setting or the `parser_classes` attribute of the view.
    3. Convert the request body into a Python data structure using the selected parser.
    4. If no parsers match the `Content-Type`, DRF will raise a `ParseError`.


### 3.Setting Up Parsers
If we want to customize the parser or we dont want to use the default parsers, we can do so by setting the `DEFAULT_PARSER_CLASSES` in the settings.py file or by using the `parser_classes` attribute in a specific view.

***Global Setting***
You can configure parsers at the project level using DEFAULT_PARSER_CLASSES in settings.py:
```python
REST_FRAMEWORK = {
    'DEFAULT_PARSER_CLASSES': [
        'rest_framework.parsers.JSONParser',
    ]
}
```
This will set the default parser for all views in your project.

***View Level Setting***
You can also set parsers for individual views by using the `parser_classes` attribute in your view class.Not that when we define the `parser_classes` attribute in a view, it will override the global setting for that specific view.
```python
from rest_framework.parsers import JSONParser, FormParser, MultiPartParser
from rest_framework.response import Response
from rest_framework.views import APIView

class ExampleView(APIView):
    """
    A view that accepts JSON data.
    """
    parser_classes = [JSONParser,FormParser,MultiPartParser]

    def post(self, request, format=None):
        return Response({'received data': request.data})
```


### 4.Individual Parser Classes In Detail
- **JSONParser**: 
    - Parses JSON request content. request.data will be populated with a dictionary of data.
    - .media_type: application/json

- **FormParser**:
    - Parses form-encoded data. This is typically used for HTML forms. request.data will be populated with a dictionary of data.
    - .media_type: application/x-www-form-urlencoded
    You will typically want to use both FormParser and MultiPartParser together in order to fully support HTML form data.

- **MultiPartParser**:
    - Parses multipart HTML form content, which supports file uploads. request.data and request.FILES will be populated with a QueryDict and MultiValueDict respectively.
    - You will typically want to use both FormParser and MultiPartParser together in order to fully support HTML form data.
    .media_type: multipart/form-data

- **FileUploadParser**:
    - Parses raw file upload content. The request.data property will be a dictionary with a single key 'file' containing the uploaded file.
    - If the view used with FileUploadParser is called with a filename URL keyword argument, then that argument will be used as the filename.
    - If it is called without a filename URL keyword argument, then the client must set the filename in the Content-Disposition HTTP header. For example Content-Disposition: attachment; filename=upload.jpg.
    .media_type: */*
    Notes:
    - The FileUploadParser is for usage with native clients that can upload the file as a raw data request. For web-based uploads, or for native clients with multipart upload support, you should use the MultiPartParser instead.
    - Since this parser's media_type matches any content type, FileUploadParser should generally be the only parser set on an API view.
    - FileUploadParser respects Django's standard FILE_UPLOAD_HANDLERS setting, and the request.upload_handlers attribute. See the Django documentation for more details.

### 5.Third-Party Parsers
- YAMLParser: Parses YAML-encoded data. You can use the `drf-yaml` package to add this parser.
- XMLParser: Parses XML-encoded data. You can use the `djangorestframework-xml` package to add this parser.
- MsgpackParser: Parses MessagePack-encoded data. You can use the `djangorestframework-msgpack` package to add this parser.


## Renderers in Django
Renderers in Django REST Framework (DRF) are responsible for converting the response data into the appropriate format based on the client's request. When a client makes a request to a DRF API, it may specify the desired response format using the `Accept` header or by including a file extension in the URL. Renderers determine how the response data is serialized and formatted before being sent back to the client.

### 1. How DRF determines which renderer to use
When a request is made to a DRF API, the framework will check the `Accept` header of the request to determine the desired response format. The order of preference is as follows:
    1. Check the `Accept` header of the request.
    2. Match it against the renderers defined in the `DEFAULT_RENDERER_CLASSES` setting or the `renderer_classes` attribute of the view.
    3. Convert the response data into the appropriate format using the selected renderer.
    4. If no renderers match the `Accept` header, DRF will raise a `NotAcceptable` exception.
    5. if no accept header is provided, DRF will use the first renderer in the list of available renderers.

### 2. Default Renderers
By default, DRF will use the following renderers if you don't specify any:
- `JSONRenderer`: Renders data as JSON (application/json).
- `BrowsableAPIRenderer`: Renders a browsable HTML representation of the API. This is useful for testing and debugging.

### 3. Most Used Renderers
- **JSONRenderer**: Renders data as JSON. This is the default renderer in DRF and is used for most API responses.
    - .media_type: application/json
    - .format: json
    - .charset: UTF-8

- **XMLRenderer**: Renders data as XML. This is useful for APIs that need to support XML clients.
    - .media_type: application/xml
    - .format: xml
    - .charset: UTF-8
    - `djangorestframework-xml` package is required.

- **YAMLRenderer**: Renders data as YAML. This is useful for APIs that need to support YAML clients.
    - .media_type: application/x-yaml
    - .format: yaml
    - .charset: UTF-8
    - `drf-yaml` package is required.


### 4. Setting Up Renderers
You can configure renderers at the project level using `DEFAULT_RENDERER_CLASSES` in settings.py and also at the view level using the `renderer_classes` attribute.

***Default Setting***
```python
REST_FRAMEWORK = {
    'DEFAULT_RENDERER_CLASSES': [
        'rest_framework.renderers.JSONRenderer',
        'rest_framework.renderers.BrowsableAPIRenderer',
    ]
}
```
This will set the default renderers for all views in your project.

***View Level Setting***
You can also set renderers for individual views by using the `renderer_classes` attribute in your view class. Note that when you define the `renderer_classes` attribute in a view, it will override the global setting for that specific view.
```python
from rest_framework.renderers import JSONRenderer
from rest_framework_yaml.renderers import YAMLRenderer
from rest_framework.response import Response
from rest_framework.views import APIView

class ExampleView(APIView):
    """
    A view that accepts JSON data and returns YAML.
    """
    renderer_classes = [JSONRenderer, YAMLRenderer]

    def get(self, request, format=None):
        data = {'message': 'Hello, World!'}
        return Response(data)
```


## Paginator in Django
To implement pagination in Django REST Framework (DRF), you can use the built-in pagination classes provided by DRF. Pagination is useful when you have a large dataset and want to limit the number of items returned in a single response. DRF provides several pagination classes that you can use or customize according to your needs.Types of Pagination:

> Note : Pagination is only performed automatically if you're using the generic views or viewset.Pagination can be turned off by setting the pagination class to None.



There are two main types of pagination in DRF:

###  ***PageNumberPagination***: 
This is the default pagination style in DRF. It uses page numbers to navigate through the dataset. You can specify the number of items per page using the `page_size` attribute.

We can set this pagination :
-***Global Setting***
```python
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 10,  # Number of items per page
}
```
This will set the default pagination for all views in your project.

-***View Level Setting***
```python
from rest_framework.pagination import PageNumberPagination
from rest_framework.response import Response
from rest_framework.views import APIView
from .models import YourModel
from .serializers import YourModelSerializer
from rest_framework import generics

class YourModelPagination(PageNumberPagination):
    page_size = 10  # Number of items per page

class YourModelListView(generics.ListAPIView):
    queryset = YourModel.objects.all()
    serializer_class = YourModelSerializer
    pagination_class = YourModelPagination
```
This will set the pagination for the `YourModelListView` view only.ie overrides the global setting if defined.

```bash
GET /api/yourmodel/?page=2
```



###  ***LimitOffsetPagination***:
This pagination style mirrors the syntax used when looking up multiple database records. The client includes both a "limit" and an "offset" query parameter. The limit indicates the maximum number of items to return, and is equivalent to the page_size in other styles. The offset indicates the starting position of the query in relation to the complete set of unpaginated items.

We can set this pagination using 

-***Global Setting***
```python
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.LimitOffsetPagination',
    'PAGE_SIZE': 10,  # defaule Number of items per page if limit is not provided
}
```
This will set the default pagination for all views in your project.


-***View Level Setting***
```python
from rest_framework.pagination import LimitOffsetPagination
from rest_framework.response import Response
from rest_framework.views import APIView
from .models import YourModel
from .serializers import YourModelSerializer
from rest_framework import generics

class YourModelPagination(LimitOffsetPagination):
    default_limit = 10  # Default number of items per page if limit is not provided

class YourModelListView(generics.ListAPIView):
    queryset = YourModel.objects.all()
    serializer_class = YourModelSerializer
    pagination_class = YourModelPagination
```
This will set the pagination for the `YourModelListView` view only.ie overrides the global setting if defined.

```bash
GET /api/yourmodel/?limit=10&offset=20
```
This will return 10 items starting from the 21st item in the dataset.
